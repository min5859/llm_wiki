---
title: "LLM JSON 파싱 실패 시 raw 응답 덤프 + 재시도 패턴"
domain: "ai-agent"
sensitivity: public
tags: ["pattern", "llm", "json", "retry", "reliability", "ai-adapter", "claude-cli", "diagnostics"]
created: 2026-05-18
updated: 2026-07-22
sources:
  - "session-logs/20260518-232056-c7c2-오늘은-실패한-포스팅이-많은데-원인이-뭔지-분석해-주세요.md"
  - "dev-blog commit e28df77 (2026-07-22 세션)"
confidence: high
related:
  - "wiki/projects/dev-blog.md"
  - "wiki/patterns/agentic-cli-text-generation-lockdown.md"
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
  - "wiki/bugs/ndjson-stdout-parser-greedy-regex.md"
  - "wiki/analyses/llm-content-quality-guards.md"
---

# LLM JSON 파싱 실패 시 raw 응답 덤프 + 재시도 패턴

자동화 파이프라인에서 LLM 어댑터 (`claude -p`, `cursor-agent`, OpenAI 등) 의 출력을 JSON 으로 파싱할 때, 일부 입력에 대해 모델이 *간헐적으로* JSON 외 텍스트 (서두 설명, 빈 응답, 잘림, fenced markdown) 를 섞어 반환해 파싱이 깨지는 패턴이 있다. **확률적 실패** 라 코드 버그 수정으로는 못 잡고, 헛 즉시 throw 하면 그날치 발행이 무더기로 사라진다.

본 패턴: **(1) 파싱 실패 시 raw 응답을 디스크에 덤프** + **(2) 한정 횟수 (1~2회) 재시도** 의 2단 헬퍼로 어댑터 호출을 감싼다. 재시도가 모두 실패하면 원래 에러를 던지되, 덤프된 raw 응답이 남아 사후 진단 가능.

## 동기 — 자동화 파이프라인의 흔한 사고

[[dev-blog]] 의 실 사례: 어느 날 10개 토픽 중 4개 (android, linux-arch-platform, linux-distro-stable, linux-gpu-ai) 가 동일하게 `Error: AI response did not contain JSON` 으로 죽었다. 동일 `ai-rewrite-lore-lens.mjs` 스크립트를 쓰는 6개 lens 중 3개는 *성공*, 3개는 *실패*. 즉:

- **코드 버그 아님** — 같은 코드 경로에서 절반은 성공·절반은 실패
- **확률적 실패** — `claude -p` 가 일부 입력에 대해 JSON 외 텍스트를 섞어 반환
- **시스템 단 원인 진단 불가** — 어떤 텍스트가 왔는지 흔적이 없으니 재현 불가

진단 흔적이 없는 게 핵심 문제. 실패 응답이 그대로 사라지면 *프롬프트가 잘못된 건지*, *입력 데이터가 특이한 건지*, *모델 백엔드의 일시 결함인지* 구분할 수 없다.

## 헬퍼 시그니처

```javascript
// scripts/lib/ai-rewrite-adapter.mjs
import { writeFile, mkdir } from 'node:fs/promises';
import path from 'node:path';

export async function runAiAdapterAndParse(prompt, {
  defaultAdapter,           // 'claude' | 'cursor' | 'template' | null
  logLabel,                 // 'opensource' / 'linux-gpu-ai' / 'weekly-linux' 등 — 덤프 파일명에 들어감
  maxAttempts = 2,          // 최대 시도 횟수 (1차 + 재시도 1회)
  failureDir,               // 덤프 디렉터리 — 기본 'logs/ai-rewrite-failures/'
  runner = runAiAdapterPrompt, // 테스트용 DI
} = {}) {
  const dir = failureDir
    ?? process.env.AI_REWRITE_FAILURE_DIR
    ?? path.join(process.cwd(), 'logs', 'ai-rewrite-failures');

  let lastError;
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    const text = await runner(prompt, { defaultAdapter });
    if (text === null) return null;     // template 모드 — 정상 fallback
    try {
      return parseNewsletterJsonFromAiOutput(text);
    } catch (err) {
      lastError = err;
      await dumpRawResponse({ dir, label: logLabel, attempt, text, error: err });
      console.warn(
        `[ai-rewrite][${logLabel}] attempt ${attempt}/${maxAttempts} parse failed: ${err.message}; raw saved to ${dir}`
      );
      // 마지막 시도가 아니면 재시도
    }
  }
  throw lastError;
}

async function dumpRawResponse({ dir, label, attempt, text, error }) {
  await mkdir(dir, { recursive: true });
  const ts = new Date().toISOString().replace(/[:.]/g, '-');
  const file = path.join(dir, `${ts}-${label}-attempt${attempt}.txt`);
  const header = [
    `# AI rewrite parse failure`,
    `# label: ${label}`,
    `# attempt: ${attempt}`,
    `# timestamp: ${new Date().toISOString()}`,
    `# error: ${error.message}`,
    '---',
    '',
  ].join('\n');
  await writeFile(file, header + text);
}
```

핵심 설계 선택:

- **`logLabel` 은 호출자가 지정** — newsletter 의 `topic` / `lens id` / `'weekly-linux'` 등. 덤프 파일명에 들어가 어느 호출에서 깨졌는지 즉시 식별
- **`null` 반환은 정상 (template 폴백)** — 어댑터가 비활성이면 파싱하지 말고 그대로 통과 (`AI_ADAPTER=template`)
- **재시도가 *모두* 실패하면 원래 에러를 던짐** — 호출부의 기존 catch 로직 (실패 시 `templateRewrite(draft)` fallback 등) 을 그대로 살림
- **덤프 디렉터리는 환경변수 override** — `AI_REWRITE_FAILURE_DIR` 로 CI 등에서 위치 바꿈
- **runner DI** — 테스트에서 가짜 어댑터 주입 가능
- **`.gitignore` 의 `logs/**/*` 에 자동 포함** — 덤프 파일이 추적되지 않음

## 호출부 일괄 치환

기존 패턴:

```javascript
// 옛 코드
const aiText = await runAiAdapterPrompt(prompt);
const rewritten = aiText
  ? parseNewsletterJsonFromAiOutput(aiText)   // ← 실패 시 즉시 throw, 흔적 없음
  : templateRewrite(draft);
```

치환 후:

```javascript
// 새 코드
const aiResult = await runAiAdapterAndParse(prompt, { logLabel: 'opensource' });
const rewritten = aiResult ?? templateRewrite(draft);
```

같은 어댑터를 쓰는 모든 호출부 (newsletter 1글당 1회 호출되는 rewrite 스크립트 N 곳) 를 한 번에 갈아끼운다. lore-lens 처럼 토픽별로 도는 스크립트는 `logLabel: topic` 으로 두면 어느 lens 가 깨졌는지 덤프 파일명만으로 식별.

## 회귀 테스트

```javascript
import { test } from 'node:test';
import assert from 'node:assert';
import { tmpdir } from 'node:os';
import { mkdtemp, readdir, readFile } from 'node:fs/promises';
import { runAiAdapterAndParse } from './ai-rewrite-adapter.mjs';

test('retries once on parse failure and dumps raw', async () => {
  const dir = await mkdtemp(path.join(tmpdir(), 'rewrite-failure-'));
  let calls = 0;
  const runner = async () => {
    calls += 1;
    if (calls === 1) return 'oops not json';   // 1차 실패
    return '{"id":"x","summary":"ok","highlights":[]}';
  };
  const result = await runAiAdapterAndParse('prompt', {
    logLabel: 'retry-test', failureDir: dir, runner,
  });
  assert.equal(calls, 2);
  assert.equal(result.summary, 'ok');
  const dumped = await readdir(dir);
  assert.equal(dumped.length, 1, 'should dump 1 raw response');
});

test('throws after exhausting retries and dumps every attempt', async () => {
  const dir = await mkdtemp(path.join(tmpdir(), 'rewrite-failure-'));
  const runner = async () => 'always bad';
  await assert.rejects(
    runAiAdapterAndParse('prompt', { logLabel: 'always-fail', failureDir: dir, runner }),
    /JSON/,
  );
  const dumped = await readdir(dir);
  assert.equal(dumped.length, 2, 'should dump 2 attempts');
});

test('returns null when adapter returns null (template mode)', async () => {
  const runner = async () => null;
  const result = await runAiAdapterAndParse('prompt', { logLabel: 't', runner });
  assert.equal(result, null);
});
```

## 프롬프트 강화 (병행)

재시도 + 덤프는 *대응* 이지 *예방* 이 아니다. 같은 자리에 *예방* 도 함께:

- 시스템 지시 맨 앞과 맨 뒤 양쪽에 「JSON 객체만 출력, 코드펜스·서두 금지」 박기
- 출력 스키마 예시를 프롬프트에 같이 두기 (`expected output` 블록)
- LLM 이 자주 섞는 패턴 (서두 「물론입니다, 다음은 JSON 입니다…」) 을 음성 예시로 명시 금지

## 운영 가시성

덤프 파일이 늘면 「확률적 실패의 빈도」 가 시계열로 측정됨. `logs/ai-rewrite-failures/*-${date}*` 카운트를 daily status 카드에 띄우면 모델 백엔드의 회귀 (어느 날부터 갑자기 실패율 급증) 를 빠르게 잡을 수 있다.

```bash
# 오늘 실패 건수
ls logs/ai-rewrite-failures/$(date -u +%Y-%m-%d)*.txt 2>/dev/null | wc -l
```

## 재시도의 한계 — 확률적 실패 vs 행동적 실패 (2026-07-22 보강)

본 패턴의 재시도는 **확률적 실패** (같은 프롬프트에 간헐적으로 JSON 외 텍스트 혼입) 전제다. 2026-07 dev-blog 에서 다른 종류가 관측됨: `claude -p` 기저 하네스/모델이 에이전트화되어 **일관되게** 자연어 보고를 반환하는 **행동적 실패** (89건, 전수 자연어 응답·잘림 0건). 이 경우:

- **동일 프롬프트 재전송의 회복률이 낮다** — attempt1 실패 61건 중 attempt2 회복 33건 (54%). 재시도가 같은 행동을 다시 유발하기 때문
- **대응 2가지를 패턴에 추가**: (1) attempt≥2 의 프롬프트 끝에 교정 지시("[재시도] 직전 응답이 유효한 JSON이 아니었습니다. 도구 사용·설명 없이 JSON 객체 하나만 출력") 를 덧붙임 — 원본 prompt 변수는 오염시키지 않고 attempt 별 지역 변수로. (2) 덤프 헤더에 `# adapter:` / `# model:` 기록 — 행동적 실패는 CLI 버전·모델 별칭 표류가 원인일 수 있어 소급 진단에 필수 (7월 사고 때 이 메타가 없어 원인 모델을 확정 못 했다)
- **근본 대책은 재시도 계층 밖** — 도구 차단·cwd 격리·모델 고정. [[agentic-cli-text-generation-lockdown]] 참조 (본 패턴 = 대응, 잠금 패턴 = 예방)

## 안티패턴

- **try / catch 로 감싸 silent 통과** — 다운스트림 publisher 가 비어 있는 산출물 게시 또는 빌더가 깨짐. 모델 응답이 잘못된 거지 그날 게시본을 빼면 안 됨. **template fallback 또는 throw 둘 중 하나만**
- **재시도 N 회 무한 지수 backoff** — 어댑터 호출 1건이 분 단위로 길어지고 사용자 구독 (Anthropic Pro / Max) 의 rate-limit 도 갉아먹음. 1~2회 즉시 재시도 + 최종 실패는 깔끔히 던지는 게 합리적
- **raw 응답 stdout 으로 echo** — 자동화 cron 로그에 N KB 의 텍스트가 매번 박혀 `logs/daily/<topic>.log` 가 비대해짐. 별도 디렉터리 + 파일로 분리
- **`logs/ai-rewrite-failures/` 를 git 추적** — 덤프 텍스트는 LLM 출력이라 잠재적 민감 정보 (사용자 입력의 echo 등) 포함 가능. `.gitignore` 의 `logs/**/*` 가 자동 차단해야 함

## 관련 패턴

- [[ndjson-stdout-parser-greedy-regex]] — cursor 어댑터의 NDJSON / envelope 케이스에 깨지는 탐욕 정규식 파서 → 4 경로 폴백. 본 패턴의 1차 시도 (`parseNewsletterJsonFromAiOutput`) 견고화의 이전 단계
- [[llm-content-quality-guards]] — 파싱 성공 후 산출물의 *결함* (CJK 비한글 / hallucination / 저신호 부풀리기) 검사 — 본 패턴은 파싱 *실패* 만 다룸
- [[multi-llm-provider-adapter-pattern]] — provider 어댑터 경계 설계. `runAiAdapterAndParse` 같은 헬퍼는 어댑터 경계 한 곳에 응집해 호출부가 일괄 치환

## 변경 이력

- 2026-05-18: 최초 작성. dev-blog 5/18 cron 의 10개 토픽 중 4개 동일 JSON 파싱 실패 사고에서 도출. `runAiAdapterAndParse` 헬퍼 (raw 덤프 + 1회 재시도) + 6개 newsletter rewrite 호출부 일괄 치환 + 회귀 테스트 3건 (재시도 성공 / 끝까지 실패 / template null 경로). 일반 패턴으로 분리 (출처: session-logs/20260518-232056-c7c2-*)
- 2026-07-22: 「확률적 vs 행동적 실패」 절 추가. dev-blog 7월 실패 급증(89건, 100% 자연어 응답) 분석에서 동일 프롬프트 재시도의 한계(회복률 54%) 확인 → 교정 재시도 + 덤프 헤더 adapter/model 기록을 패턴에 추가, 예방 계층은 [[agentic-cli-text-generation-lockdown]] 로 분리 (출처: dev-blog commit e28df77)
