---
title: "멀티 LLM provider 어댑터 패턴 — 공식 SDK 직접 사용"
domain: both
sensitivity: public
tags: ["llm", "multi-provider", "adapter-pattern", "openai", "anthropic", "gemini", "abstraction"]
created: 2026-05-03
updated: 2026-05-03
sources:
  - "session-logs/20260503-100914-b80f-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md"
---

# 멀티 LLM provider 어댑터 패턴 — 공식 SDK 직접 사용

OpenAI / Anthropic / Gemini 등 여러 LLM provider 를 한 앱에서 선택 가능하게 만들 때, **추상화 라이브러리 (LangChain, Vercel AI SDK 등) 를 도입하지 않고 공식 SDK 를 직접 어댑터 뒤에 두는** 경량 패턴. 의존성 1개 더 받는 비용으로 라이브러리 추상화의 학습/수명 위험을 회피한다.

## 핵심 내용

### 디렉터리 구조

```
lib/ai/
├── types.ts            # AiAnalysisResult, AiProvider 타입 정의
├── context.ts          # 도메인 → 프롬프트 빌더 (provider 무관)
├── index.ts            # provider 선택 + 라우팅 (단일 진입점)
└── providers/
    ├── gemini.ts       # @google/generative-ai 직접 호출
    ├── openai.ts       # openai SDK 직접 호출
    └── anthropic.ts    # @anthropic-ai/sdk 직접 호출
```

각 provider 어댑터는 동일한 시그니처를 구현:

```ts
// lib/ai/types.ts
export type AiProvider = "gemini" | "openai" | "anthropic";

export interface AiAdapter {
  analyze(prompt: string, model?: string): Promise<AiAnalysisResult>;
}
```

`index.ts` 는 `provider` 인자를 받아 해당 어댑터로 분기:

```ts
export async function analyzePortfolio(
  provider: AiProvider,
  ...args: AnalyzeArgs
): Promise<AiAnalysisResult> {
  const prompt = buildPortfolioContext(...args);
  switch (provider) {
    case "gemini": return geminiAdapter.analyze(prompt);
    case "openai": return openaiAdapter.analyze(prompt);
    case "anthropic": return anthropicAdapter.analyze(prompt);
  }
}
```

### 환경변수 / 모델 선택 정책

| 항목 | 패턴 |
|---|---|
| API 키 | `GEMINI_API_KEY`, `OPENAI_API_KEY`, `ANTHROPIC_API_KEY` — **있는 것만** UI 에 활성화 노출 |
| 기본 모델 | 각 provider 어댑터 안에 default (예: `gemini-2.5-flash`, `gpt-4o-mini`, `claude-haiku-4-5`) |
| Override | `GEMINI_MODEL`, `OPENAI_MODEL`, `ANTHROPIC_MODEL` env 로 코드 변경 없이 교체 |
| DB 기록 | 분석 결과를 영속화하는 경우 `provider` 컬럼 함께 저장 (어떤 모델로 분석한지 추적) |

### 추상화 라이브러리를 쓰지 않는 이유

| 항목 | 라이브러리 (예: LangChain.js, Vercel AI SDK) | 어댑터 직접 |
|---|---|---|
| 의존성 수 | 라이브러리 + provider SDK 들 (혹은 라이브러리만) | provider SDK 만 |
| 학습 곡선 | 라이브러리 추상화 (Chain, Agent, RunnableSequence...) | 각 SDK 의 `chat.completions.create` / `messages.create` / `generateContent` |
| 새 모델 출시 대응 | 라이브러리 업데이트 대기 (며칠~몇 주) | 즉시 (모델 ID 만 바꿈) |
| 라이브러리 종료/리브랜드 위험 | 마이그레이션 비용 큼 | 영향 없음 |
| 디버깅 | 추상화 레이어 통과 | 직접 SDK 호출, stack trace 짧음 |
| 고급 기능 (function calling, structured output) | 라이브러리가 wrapping 안 했으면 막힘 | SDK 의 모든 기능 즉시 사용 |
| **잃는 것** | — | provider 간 자동 fallback / load balancing / retry 로직은 직접 짜야 함 |

### 어댑터 구현 시 통일 필요 항목

provider 마다 SDK 인터페이스가 다르므로 어댑터 안에서 **호출 시그니처 + 응답 형식** 을 일관되게 맞춘다:

| 항목 | OpenAI | Anthropic | Gemini |
|---|---|---|---|
| 메서드 | `client.chat.completions.create` | `client.messages.create` | `model.generateContent` |
| 시스템 프롬프트 | `messages: [{role: "system", ...}]` | 별도 `system` 인자 | `systemInstruction` 인자 |
| 응답 본문 | `choices[0].message.content` | `content[0].text` | `response.text()` |
| Streaming | `stream: true` + AsyncIterator | `client.messages.stream(...)` | `model.generateContentStream` |
| Token usage | `usage.completion_tokens` | `usage.output_tokens` | `usageMetadata.candidatesTokenCount` |

어댑터의 일은 이 차이를 흡수해 **공통 타입** (`AiAnalysisResult`) 으로 변환하는 것뿐. JSON 파싱 / 스키마 검증 / 에러 정규화는 도메인이 아니라 어댑터 책임.

### 키 저장 — 환경변수 vs DB 암호화

| 단계 | 키 저장 |
|---|---|
| Phase 1 (1인 또는 신뢰된 팀) | 환경변수만 (`.env` + Vercel Settings) |
| Phase 2 (멀티 사용자, 사용자별 키) | DB 에 AES-256-GCM 암호화 저장 (예: `AiCredential` 모델) + `AI_KEY_ENCRYPTION_SECRET` (64자 hex) env |

DB 저장으로 넘어갈 때:
- **암호화 키 분실 시 복구 불가** → 1Password 등 별도 백업 필수
- 키 회전 정책 결정 필요 (회전 시 모든 레코드 재암호화)
- UI 에서 키 입력 → server action → 암호화 후 DB 저장 → 사용 시 복호화 후 어댑터 주입

### 요약 — 언제 이 패턴을 쓰나

- 프로젝트가 LLM 제어 흐름이 복잡하지 않다 (단순 prompt → 응답)
- 여러 provider 중 1개만 활성화되는 경우가 대부분 (공급자 자동 fallback 불필요)
- 모델 출시 속도가 빨라 라이브러리 업데이트를 기다릴 수 없다
- 의존성 수와 빌드 사이즈를 작게 유지하고 싶다

복잡한 에이전트 워크플로우 (도구 호출 체인, RAG, 멀티턴 메모리) 가 필요해지면 그때 LangChain 등으로 마이그레이션하면 된다 — 어댑터 인터페이스가 명확하면 마이그레이션 범위도 작다.

## 관련 맥락

- 본 패턴은 [[japa-asset-dashboard]] 의 `/ai` 페이지 멀티 LLM 도입 사례에서 추출
- LLM provider 의 분류 (aggregator / local / hub) 는 [[llm-provider-aggregator-vs-local-vs-hub]]
- 어댑터 뒤에 OpenRouter 같은 **aggregator** 를 붙이면 단일 어댑터로 수십 개 모델 라우팅도 가능 (직접 SDK 와 trade-off)

## 변경 이력

- 2026-05-03: 최초 생성. japa 프로젝트의 멀티 LLM 도입 사례에서 일반화 (출처: session-logs/20260503-100914-b80f-*).
