---
title: "AI 토큰 사용량 기록 + per-user 일일 비용 한도 가드 패턴"
domain: both
sensitivity: public
tags: ["ai", "llm", "cost", "rate-limit", "prisma", "nextjs", "monitoring"]
created: 2026-05-13
updated: 2026-05-13
sources:
  - "session-logs/20260512-231800-c191-*.md"
confidence: high
related:
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
  - "wiki/patterns/vercel-timeout-browser-direct-api.md"
---

# AI 토큰 사용량 기록 + per-user 일일 비용 한도 가드 패턴

LLM API 를 사용하는 사용자별 SaaS / 1인 도구에서, **인증된 사용자가 비용 한도 없이 무한 호출** 하는 사고를 막기 위한 표준 패턴. 이전에는 다음 날 아침 provider 청구 대시보드를 보고서야 사고를 알 수 있었지만, **DB 에 호출 단위로 토큰을 기록 + entrypoint 마다 한도 가드**로 즉시 차단한다.

## 4단계 구성

```
UsageEvent 테이블
  ↓
provider/model → pricing.ts (USD per 1M tokens)
  ↓
ai-client.ts 가 UsageInfo 도 함께 반환
  ↓
모든 AI entrypoint (/api/extract, /api/valuation, ...) 에 한도 체크 + 기록
```

### 1) DB — `UsageEvent` 모델

```prisma
model UsageEvent {
  id           String   @id @default(cuid())
  userEmail    String
  provider     String   // 'anthropic' | 'openai' | 'gemini' | 'deepseek'
  model        String   // 'claude-sonnet-4-20250514' 등
  inputTokens  Int
  outputTokens Int
  costUsd      Decimal  @db.Decimal(10, 6)
  endpoint     String   // '/api/extract' 등
  createdAt    DateTime @default(now())

  @@index([userEmail, createdAt])
  @@index([createdAt])
  @@map("usage_events")
}
```

- 인덱스 두 개: `(userEmail, createdAt)` 은 일일 합산 / `(createdAt)` 은 전체 집계
- `costUsd` 는 추정값 (provider 공개 list price). **abuse 차단 용도이지 정산 용도가 아님**

### 2) `lib/pricing.ts` — provider/model 별 USD per 1M tokens

```ts
type Pricing = { inputPer1M: number; outputPer1M: number };

const PRICING: Record<string, Pricing> = {
  // 정확한 모델 ID 매칭
  'claude-sonnet-4-20250514': { inputPer1M: 3, outputPer1M: 15 },
  'gpt-4o':                   { inputPer1M: 2.5, outputPer1M: 10 },
  // ...
};

const DEFAULTS_BY_PROVIDER: Record<string, Pricing> = {
  anthropic: { inputPer1M: 3, outputPer1M: 15 },
  openai:    { inputPer1M: 2.5, outputPer1M: 10 },
  gemini:    { inputPer1M: 0, outputPer1M: 0 },
  deepseek:  { inputPer1M: 0.27, outputPer1M: 1.10 },
};

export function calculateCost(provider: string, model: string, input: number, output: number): number {
  // 1. exact match
  // 2. prefix match (모델 ID 가 datestamped — claude-sonnet-4-... → claude-sonnet-4-20250514)
  // 3. provider default
}
```

- **prefix 매칭이 핵심** — Anthropic 모델 ID 는 datestamp 가 붙기 때문에 (`claude-sonnet-4-20250514`) 매핑 누락 시 prefix 로 정확하게 해결
- provider default 는 safety net — 새 모델이 추가돼도 0 으로 떨어지지 않음

### 3) `lib/ai-client.ts` — UsageInfo 함께 반환

```ts
export type UsageInfo = {
  provider: string;
  model: string;
  inputTokens: number;
  outputTokens: number;
};

export type ChatCompletionResult<T> = {
  data: T;
  usage: UsageInfo;
};

export async function chatCompletionJson<T>(...): Promise<ChatCompletionResult<T>> {
  // 각 provider SDK 응답에서 token 추출 + 통일된 UsageInfo 로 반환
}
```

호출자가 SDK 응답 shape 을 매번 다시 파싱하지 않아도 되도록 *통일된 UsageInfo* 를 옆구리에 함께 내려준다.

### 4) `lib/usage.ts` — `recordUsage` / `getDailyUsage`

```ts
export async function recordUsage(args: {
  userEmail: string;
  usage: UsageInfo;
  endpoint: string;
}): Promise<void>;

export async function getDailyUsage(userEmail: string): Promise<{
  totalCostUsd: number;
  callCount: number;
}>;
```

`getDailyUsage` 의 **UTC 자정 cutoff** — `WHERE createdAt >= date_trunc('day', NOW())` 정도. 사용자 시간대 별 자정으로 하려면 분기가 늘어나서, 추정값 abuse 차단 용도엔 UTC 자정으로 충분.

## Entrypoint 가드 패턴

모든 AI 호출 경로의 *최상단* 에서:

```ts
export async function POST(request: NextRequest) {
  const session = await auth();
  if (!session?.user?.email) return new Response('Unauthorized', { status: 401 });

  // 한도 체크 — 호출 전에
  const daily = await getDailyUsage(session.user.email);
  const limit = Number(process.env.DAILY_USAGE_LIMIT_USD ?? 5);
  if (daily.totalCostUsd >= limit) {
    return Response.json(
      { error: `일일 사용 한도 ($${limit}) 도달. 자정 UTC 에 리셋됩니다.` },
      { status: 429 }
    );
  }

  // AI 호출
  const result = await chatCompletionJson(...);

  // 기록 — 호출 후
  await recordUsage({
    userEmail: session.user.email,
    usage: result.usage,
    endpoint: '/api/extract',
  });

  return Response.json(result.data);
}
```

`DAILY_USAGE_LIMIT_USD` 는 env 로 override (기본 $5). 429 응답 메시지를 사용자에게 그대로 보여 한도 도달 사실을 즉시 인지하게 함.

## 함정 — Client-direct 우회 경로

[[vercel-timeout-browser-direct-api]] 처럼 **Vercel timeout 우회**를 위해 인증된 사용자에게 API key 를 직접 내려 브라우저에서 SDK 를 직접 호출하는 경로가 있다면, 서버를 거치지 않으므로 위 가드가 자동으로 적용되지 않는다.

→ 두 가지 보조 entrypoint 가 필요:

1. **사전 가드** — `/api/anthropic-config` (key 발급) 자체에 동일한 `getDailyUsage` 체크. 한도 초과 시 key 발급 거부 (429)
2. **사후 reporting** — `/api/usage` (POST) 를 신설해 브라우저가 직접 호출 후 usage 를 신고. 이 endpoint 는 인증 필요 + body 검증

```ts
// /api/usage POST
export async function POST(req: NextRequest) {
  const session = await auth();
  if (!session?.user?.email) return new Response('Unauthorized', { status: 401 });

  const { provider, model, inputTokens, outputTokens, endpoint } = await req.json();
  await recordUsage({
    userEmail: session.user.email,
    usage: { provider, model, inputTokens, outputTokens },
    endpoint: endpoint ?? 'client-direct',
  });

  return Response.json({ ok: true });
}
```

브라우저 SDK 가 응답에서 `usage` 를 받자마자 즉시 fire-and-forget 으로 reporting. 신고는 사용자 신뢰에 의존하지만, 한도 *사전* 가드가 이미 걸려있어 최악은 한 호출 만큼의 over-burst.

## 운영 측면

- **provider 청구 vs DB 추정 차이** — 본 패턴의 cost 는 추정값이므로 월말에는 provider 청구가 진실. DB 의 합계는 *abuse 차단 기준* 이지 정산 기준이 아니다
- **0 으로 추정되는 모델 누락 감지** — `pricing.ts` 의 lookup 결과가 0 으로 떨어진 horizon 들을 매일 한 번 alert. 새 모델 출시 시 매핑이 자동 추가되지 않는다
- **per-endpoint 통계** — `endpoint` 컬럼이 있어 `SUM(costUsd) GROUP BY endpoint` 로 어떤 기능이 비용을 가장 많이 태우는지 즉시 확인 가능

## 변경 이력

- 2026-05-13: 최초 작성 (출처: session-logs/20260512-231800-c191 — finance-analysis-nextjs Phase 3 "토큰 비용 가드" 구현. `usage_events` 테이블 + pricing + ai-client UsageInfo + 3 entrypoint 가드 + client-direct reporting endpoint, commit `44ff302`)
