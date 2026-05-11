---
title: "AI CLI 어댑터의 stdout 파서가 NDJSON·envelope·잡음 텍스트에 깨지는 함정"
domain: personal
sensitivity: public
tags: ["bug", "parser", "ai-adapter", "cursor", "claude-cli", "json", "ndjson"]
created: 2026-05-12
updated: 2026-05-12
source_session: "20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
sources:
  - "session-logs/20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
confidence: high
related:
  - "wiki/projects/dev-blog.md"
  - "wiki/patterns/shell-set-eu-topic-isolation.md"
---

# AI CLI 어댑터의 stdout 파서가 NDJSON·envelope·잡음 텍스트에 깨지는 함정

`claude -p` / `cursor-agent` 같은 AI CLI 가 표준 출력을 통해 JSON 을 돌려준다고 가정하고 정규식 한 줄로 추출하는 파서가, 4가지 흔한 출력 모양 중 하나만 받아도 즉시 무너진다. 단일 파이프라인이 다중 토픽을 처리하는 환경에서는 이 깨짐 한 번이 `set -eu` 와 만나 모든 토픽의 일일 발행을 중단시킬 수 있다.

## 증상

dev-blog 의 cursor 어댑터 (`scripts/lib/ai-rewrite-adapter.mjs`) 가:

```
Unexpected non-whitespace character after JSON at position 3930
```

로 깨졌고, `daily-deploy.sh` 가 `set -eu` 로 첫 토픽 실패에 전체 스크립트를 중단했다. 결과적으로 5/11 자 12개 토픽 콘텐츠가 한 건도 게시되지 않음.

## 원인: AI CLI 출력 모양 4가지

같은 cursor / claude CLI 가 동일한 프롬프트에도 시점·세션 상태에 따라 4가지 다른 모양으로 출력한다.

| 모양 | 예시 | 기존 파서 결과 |
|------|------|----------------|
| 1. 단일 JSON | `{"title":"...","sections":[...]}` | OK |
| 2. NDJSON (연속 JSON) | `{"type":"start"}\n{"title":"...",...}\n{"type":"end"}` | **fail** (탐욕 정규식이 첫 `{` ~ 마지막 `}` 를 한 덩어리로 잡음) |
| 3. envelope.result 안 잡음 | `{"result":"some prose ... {\"title\":...}", "metadata":{...}}` | **fail** (envelope 의 result 가 다시 JSON 을 품은 prose) |
| 4. fenced JSON | ` ```json\n{...}\n``` ` | **fail** (코드펜스 토큰 미처리) |

## 안티 패턴: 탐욕 정규식 + 단일 모양 가정

```js
// fragile
const m = stdout.match(/\{[\s\S]*\}/);
return JSON.parse(m[0]);
```

- `[\s\S]*` 는 탐욕적이라 NDJSON 의 첫 `{` 부터 마지막 `}` 까지 한 덩어리로 잡아 `Unexpected non-whitespace character after JSON at position N` 발생
- envelope `result` 안에 또 다른 JSON 이 박혀 있어도 바깥 envelope 의 외피 JSON 만 잡고 끝

## 견고한 추출 전략 (4 경로 흡수)

```js
function extractNewsletterJson(stdout) {
  // 1) 단일 JSON 시도 (가장 흔한 정상 경로)
  const trimmed = stdout.trim();
  if (trimmed.startsWith('{') && trimmed.endsWith('}')) {
    try { return JSON.parse(trimmed); } catch {}
  }

  // 2) NDJSON: 줄별 JSON.parse 시도 → 본문 후보 선별
  const lines = trimmed.split('\n').filter(l => l.trim().startsWith('{'));
  const candidates = [];
  for (const line of lines) {
    try { candidates.push(JSON.parse(line)); } catch {}
  }
  for (const c of candidates) {
    if (c.title && Array.isArray(c.sections)) return c;
  }

  // 3) envelope.result 안에 박힌 JSON: result 문자열에 같은 추출 한 번 더
  for (const c of candidates) {
    if (typeof c.result === 'string') {
      const inner = extractNewsletterJson(c.result);
      if (inner) return inner;
    }
  }

  // 4) fenced JSON: ```json ... ``` 또는 ``` ... ```
  const fence = trimmed.match(/```(?:json)?\s*\n([\s\S]+?)\n```/);
  if (fence) {
    try { return JSON.parse(fence[1]); } catch {}
  }

  return null;
}
```

설계 원칙:

- **단일 정규식 대신 단계적 폴백** — 가장 흔한 모양부터 시도하고, 실패하면 다음 모양
- **NDJSON 은 줄별 parse** — `[\s\S]*` 같은 탐욕 매칭 금지
- **envelope.result 는 재귀** — result 가 string 이고 그 안에 JSON 이 박혀 있으면 동일 로직 한 단계 적용
- **회귀 테스트 필수** — 4 모양 각각의 대표 fixture 를 단위 테스트로 박아 둠 (NDJSON 깨짐 케이스 1건 + envelope 깨짐 케이스 1건 최소)

## 진단의 핵심

이번 사고의 핵심 단서는 **stdout 을 파일로 떨궈서 직접 검사** 한 것:

```bash
cursor-agent < prompt.txt > /tmp/raw-stdout.txt
head -c 200 /tmp/raw-stdout.txt
wc -l /tmp/raw-stdout.txt
```

`Unexpected non-whitespace character after JSON at position N` 에러 메시지만 보고 파서를 고치려 들면 "이건 JSON 이 깨졌나?" 로 시작해 원인 추적에 시간이 들지만, 실제 출력 한 번 보면 NDJSON 인지 envelope 인지 즉시 식별된다.

## 일반 패턴: 외부 LLM CLI 의 stdout 가정 금지

| 가정 | 현실 |
|------|------|
| "CLI 가 단일 JSON 으로만 돌려준다" | 세션 상태에 따라 NDJSON / envelope / fenced JSON 등 다양 |
| "이번에 통과한 fixture 가 내일도 같다" | CLI 업데이트, 토큰 capacity, 모델 응답 길이에 따라 다른 모양 |
| "stdout 정규식 1줄이면 충분" | 4 모양 모두 커버하려면 단계적 추출 + 재귀 분해 |

LLM CLI 를 어댑터로 감쌀 때는 **stdout 의 구조를 신뢰하지 말고** 다중 추출 경로를 갖춘 파서 + 깨졌을 때의 명확한 에러 메시지 + 회귀 fixture 를 함께 두는 것이 표준이다.

## 토픽 격리와의 결합

파서 견고화만으로는 부족하다. `daily-deploy.sh` 같은 multi-topic 파이프라인에서 `set -eu` 로 짜인 경우, 한 토픽이 (이번처럼) 깨지면 나머지 토픽도 발행 중단된다. 파서 견고화와 함께 `if !` 로 토픽별 격리도 같이 적용해야 한다 → [[shell-set-eu-topic-isolation]].

## 관련 페이지

- [[shell-set-eu-topic-isolation]] — `set -eu` 의 토픽 연쇄 실패 차단
- [[dev-blog]] — 이 패턴이 발견된 프로젝트

## 변경 이력

- 2026-05-12: 최초 작성 (session-logs/20260511-230001-14d5-*.md). dev-blog 5/11 일일 파이프라인 사고의 파서 견고화 작업을 일반화
