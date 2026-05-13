---
title: "LLM 프롬프트 출력 스키마와 다운스트림 validator 간 결합 관리"
domain: both
sensitivity: public
tags: ["pattern", "llm", "schema", "validator", "pipeline", "ci", "json"]
created: 2026-05-13
updated: 2026-05-13
sources:
  - "session-logs/20260513-074737-a32f-오늘날짜-포스팅이-안-보입니다.-오늘-동작-했는지-확인해-주세요.md"
confidence: medium
related:
  - "wiki/bugs/highlights-action-validator-schema-drift.md"
  - "wiki/projects/dev-blog.md"
  - "wiki/analyses/llm-content-quality-guards.md"
---

# 프롬프트 ↔ Validator 결합 관리

LLM 의 출력은 *프롬프트 텍스트* 가 곧 인터페이스 정의이고, 다운스트림 코드 (parser / validator / 빌더) 가 그 출력을 다시 입력으로 받는다. 프롬프트 한 줄 수정이 정적 타입 시스템 밖에서 silent 하게 스키마를 변경할 수 있다는 점에서 "선언된 적 없는 contract" 가 된다.

## 문제의 형태

```
[prompt]               → "highlights[*].action"
[ai-rewrite output]    → 단일 string "action"
[publish validator]    → expects "action"  ✓
[build-site renderer]  → expects "action"  ✓
```

…에서 프롬프트만 갱신:

```
[prompt]               → "highlights[*].if / do / verify"   ← 변경
[ai-rewrite output]    → 3개 필드
[publish validator]    → expects "action"   ✗ throw
[build-site renderer]  → expects "action"   ✗ 빈 칸 렌더
```

테스트가 모킹된 fixture 로 통과하고 LLM 응답을 실제로 통과시켜 보지 않으면 PR 머지 시점엔 안 보임. 다음 cron 사이클 (24시간 뒤) 에 production 에서 발견 — *변경과 발견 사이의 시간차* 가 LLM 파이프라인의 특징.

## 결합점의 인벤토리

LLM 출력 스키마를 바꾸는 PR 은 다음 모두를 동시에 갱신해야 한다:

1. **프롬프트 본문** — 출력 형식 명세 (key 이름, 타입, 글자 수 제한 등)
2. **프롬프트 안 예시 JSON** — 본문 명세와 예시가 불일치하면 LLM 이 어느 쪽을 따를지 비결정
3. **Rewrite/parse validator** — LLM 응답의 JSON 파싱 직후 형태 검증
4. **다음 단계 validator** — 위와 별개 (publish / promote / merge 단계의 재검증)
5. **저장소 빌더** — 정적 사이트 / API 응답 / 데이터 export 의 렌더
6. **주간/월간 집계** — 일일 산출물을 후처리하는 코드 (필드 이름 누락 시 silent 데이터 손실)
7. **회귀 테스트** — fixture / snapshot / unit test
8. **사용자 문서** — README, prompts 디렉터리 README, dashboard 설명

## 흔한 안티패턴

- **Validator 함수 복붙** — 5개 publisher 가 `validatePost` 의 highlight 검증 블록을 각자 갖고 있는 형태. 한 군데를 고치면 나머지 4개가 silently 옛 스키마에 묶여 있음. 해결: `scripts/lib/<thing>-validator.mjs` 같은 공용 모듈 추출.
- **"옛 필드만 강제" vs "옛+신 OR 신만"** — 빌더는 양쪽 받고 (관대), validator 는 옛 것만 강제 (엄격) 같이 *동일 데이터에 대한 검증 기준이 단계마다 다른* 상태. 해결: 단계 간 검증 정책을 명시적으로 표준화 (예: "모든 단계에서 hasOld OR hasNew").
- **Cron 사일런트 — 실패 가시성 부재** — `daily-deploy.sh` 가 토픽별 `if !` 격리로 한 토픽 실패가 다음을 막지 않는 패턴 (좋음) 인데, *모든* 토픽이 실패해도 git push 가 `nothing to push` 로 정상 종료해 cron 알림이 안 옴. 해결: "today's run produced 0 content commits" 를 명시적 에러 신호로.

## 권장 작업 순서 (스키마 변경 시)

1. 새 스키마의 fixture 1건을 만들고, 모든 validator 를 그 fixture 로 돌려 *모두 통과하는지* 확인
2. 위가 통과하면 프롬프트 본문 + 예시 JSON 갱신
3. dry-run 으로 LLM 응답 1건 받아서 fixture 와 같은 모양인지 확인
4. validator 들을 "옛 OR 신" 로 완화 (마이그레이션 윈도 동안 양쪽 받음)
5. 충분한 사이클이 지나면 옛 스키마 분기 제거 (deprecation)

이 순서는 LLM 출력이 *외부 API 와 같은 가중치* 로 다루어져야 한다는 인식에서 나온다. 프롬프트를 "그저 텍스트 한 줄" 로 보면 1번을 생략하게 됨.

## Validator 패턴: 양쪽 받기

```js
// 옛 스키마: highlight.action (string)
// 신 스키마: highlight.if / highlight.do / highlight.verify (각 string)
//
// 마이그레이션 윈도 동안 둘 중 하나만 있어도 통과.
function validateHighlightShape(h, ctx) {
  for (const key of ['title', 'priority', 'verifyLink']) {
    if (typeof h[key] !== 'string' || !h[key])
      throw new Error(`${ctx}.${key} required`);
  }
  const hasAction = typeof h.action === 'string' && h.action;
  const hasIfDoVerify = ['if','do','verify'].every(
    k => typeof h[k] === 'string' && h[k]
  );
  if (!hasAction && !hasIfDoVerify)
    throw new Error(`${ctx} requires either .action or .if/.do/.verify`);
}
```

build-site 와 publisher 5종, weekly 가 모두 같은 함수를 import 하도록.

## 가시성 신호 추가

cron 사일런트 방지:

- `daily-deploy.sh` 가 publish 후에 `git diff --stat content/` 가 0 줄이면 *명시적 비정상* 종료 코드로 빠진다 (cron 알림 트리거).
- 또는 `logs/daily/<date>-status.json` 의 단계별 exit code 를 종합해 "오늘 0개 토픽 게시" 케이스에 별도 알림.
- 단순한 빈 시도는 사이트의 "오늘 글" 카드가 비어 있는 형태로만 발현 — 사용자가 사이트를 안 보면 영원히 모름.

## 적용 범위

이 패턴은 dev-blog 같은 "LLM 이 데이터를 만들고, 코드가 그것을 후처리하는" 모든 파이프라인에 해당:

- AI 자동 뉴스레터 / 일일 요약
- LLM-as-judge 의 평가 출력
- 코드 생성기의 산출물 (구조화된 patch / 변경 명세)
- 챗봇 응답의 구조화 모드 (`response_format: json_schema`)

API 클라이언트의 "버전 헤더 + 양쪽 받기 → deprecation" 흐름과 본질적으로 같다 — *프롬프트 본문이 곧 API 버전* 이라고 생각하면 인터페이스 진화가 보임.

## 관련 페이지

- [[highlights-action-validator-schema-drift]] — dev-blog 에서 이 패턴이 무너졌던 실제 사례
- [[llm-content-quality-guards]] — 콘텐츠 측면의 일반 가드
- [[ndjson-stdout-parser-greedy-regex]] — LLM 출력 *형태* 의 비결정성에서 발생한 다른 사례
