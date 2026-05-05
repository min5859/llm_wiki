---
title: "Vercel 60초 timeout 우회 — 인증 사용자에게 API key 노출 후 브라우저에서 직접 호출"
domain: both
sensitivity: public
tags: ["vercel", "hobby", "timeout", "anthropic", "openai", "client-side", "security-debt", "api-key", "pdf", "vision"]
created: 2026-05-05
updated: 2026-05-05
sources:
  - "session-logs/20260505-101659-115c-*.md"
confidence: medium
related:
  - "wiki/projects/finance-analysis-nextjs.md"
  - "wiki/patterns/vercel-cron-best-practices.md"
  - "wiki/analyses/vercel-friendly-database-options.md"
  - "wiki/bugs/prisma-connection-pool-vercel-supabase.md"
---

# Vercel 60초 timeout 우회 — 브라우저 직접 호출 패턴

Vercel Hobby 의 lambda 함수 한도 60초 안에서 끝나지 않는 무거운 AI/Vision 호출 (예: 큰 PDF 의 vision 분석) 을 일시적으로 풀어주는 escape hatch. **인증된 사용자에게만 API key 를 내려 주고 브라우저가 외부 LLM API 를 직접 호출**한다. 60초 제약이 사라지지만 DevTools 로 키 추출이 가능한 보안 부채를 끼고 살아야 한다 — 본질적인 해결책이 아니라 임시 우회.

## 핵심 내용

### 언제 쓰는가

다음 조건이 동시에 모일 때 일시 도입을 고려:

1. Vercel Hobby (60초) 또는 Pro (≤ 300초) 의 lambda 한도 안에서 끝나지 않는 단일 호출이 있다
2. 호출 대상은 **신뢰 가능한 외부 API** (Anthropic, OpenAI 등) 이고 응답 시간이 실시간 사용자 경험에 직결된다
3. 사용자는 **이미 인증된 상태** 이고 인증된 사용자만 호출하면 된다
4. **즉시 출시가 필요**하고 Pro 업그레이드 / Cloud Run / 외부 워커 도입은 며칠~몇 주가 걸린다

큰 PDF 를 multimodal 모델에 직접 던지는 케이스 (vision-based 재무제표 추출 등) 에서 가장 자주 나온다. 텍스트 추출 후 모델 호출은 60초 안에 끝나지만 vision 호출은 1~5 분이 걸리기 때문.

### 동작 원리

```
[Browser]                      [Server (Vercel)]              [Anthropic]
   |                                 |                             |
   | 1. 인증 확인 후 GET /api/anthropic-config                       |
   |-------------------------------->|                             |
   |                                 | auth() 통과 시                |
   |          { apiKey, model, ... } |                             |
   |<--------------------------------|                             |
   | 2. SDK 로 직접 호출 (timeout 없음)                              |
   |--------------------------------------------------------------->|
   |                  vision 응답                                    |
   |<---------------------------------------------------------------|
```

핵심: **lambda 가 LLM 호출의 wall-clock 을 짊어지지 않는다**. 서버는 단지 키를 발급하는 1초 이내 작업만 한다.

### 최소 구현 (Next.js + Anthropic 예)

**서버 — 키 발급 endpoint**

```ts
// src/app/api/anthropic-config/route.ts
// ⚠️ TEMPORARY: Vercel Hobby 60초 timeout 회피용. Pro 업그레이드 / 외부 워커 도입 시 회수.
import { auth } from "@/auth";

export async function GET() {
  const session = await auth();
  if (!session) return Response.json({ error: "unauthorized" }, { status: 401 });

  return Response.json({
    apiKey: process.env.ANTHROPIC_API_KEY,
    model: "claude-3-5-sonnet-latest",
    system: "...", // 시스템 프롬프트도 서버에서 통제
  });
}
```

**브라우저 — SDK 직접 호출**

```ts
// src/lib/anthropic-browser.ts
import Anthropic from "@anthropic-ai/sdk";

export async function fetchAnthropicConfig() {
  const res = await fetch("/api/anthropic-config");
  if (!res.ok) throw new Error("auth required");
  return res.json();
}

export async function extractFinanceFromPdfDirect(file: File) {
  const { apiKey, model, system } = await fetchAnthropicConfig();
  const client = new Anthropic({ apiKey, dangerouslyAllowBrowser: true });

  const base64 = await fileToBase64(file);
  return client.messages.create({
    model,
    system,
    max_tokens: 8000,
    tools: [/* tool_use 강제로 structured JSON */],
    messages: [{
      role: "user",
      content: [
        { type: "document", source: { type: "base64", media_type: "application/pdf", data: base64 } },
        { type: "text", text: "재무데이터를 JSON 으로 추출하세요." },
      ],
    }],
  });
}
```

**미들웨어 보강 — `/api/*` 미인증은 JSON 401**

```ts
// src/proxy.ts (또는 middleware.ts)
// 인증 실패 시 HTML redirect 가 아니라 JSON 401 — 클라이언트 fetch 가 res.ok 분기로 처리 가능
if (!session && req.nextUrl.pathname.startsWith("/api/")) {
  return NextResponse.json({ error: "unauthorized" }, { status: 401 });
}
```

### 분기 — 어느 호출만 client-direct 로 보낼지

전부를 client-direct 로 옮길 필요는 없다. **timeout 위험이 있는 호출만** 분기:

```ts
async function processPdfFile(file: File, opts: { provider: string; forceOcr: boolean }) {
  // Anthropic vision 또는 OCR 강제 시 → 브라우저 직접 호출
  if (opts.provider === "anthropic" || opts.forceOcr) {
    return extractFinanceFromPdfDirect(file);
  }

  // 그 외 (텍스트 추출 + GPT/Gemini) → 기존 server multipart 경로 유지
  const fd = new FormData();
  fd.append("file", file);
  return fetch("/api/extract", { method: "POST", body: fd });
}
```

진행 메시지도 갈아끼운다 — 60초 제약이 사라졌으니 "1~5분 소요됩니다" 같은 안내가 가능.

## 보안 부채와 회수 조건

이 패턴은 **임시 escape hatch**다. 다음을 의도적으로 받아들인다:

| 부채 | 상세 |
|---|---|
| API key 클라이언트 노출 | DevTools Network 탭에서 키 추출 가능 |
| Rate limit 통제 약화 | 사용자가 자기 키로 임의 횟수 호출 가능 (인증된 사용자만이지만) |
| 시스템 프롬프트도 노출 | `system` 필드를 키와 함께 내려줬다면 같이 추출됨 |
| 사용량 추적 어려움 | 서버 로그에 LLM 호출이 안 잡힘 — 비용 추적은 Anthropic 대시보드에서만 |

회수 (제거) 조건은 **명시적으로 코드와 docs/tasks/todo.md 에 박아 둘 것**:

```
회수 조건 — 다음 중 하나가 완료되는 즉시 이 escape hatch 를 제거한다:
- [ ] Vercel Pro 업그레이드 (300초 timeout)
- [ ] Cloud Run / Fargate 등 long-running 워커 도입
- [ ] 외부 큐 (Inngest / Trigger.dev / SQS) 로 작업 분리

회수 시 작업:
- [ ] /api/anthropic-config endpoint 삭제
- [ ] src/lib/anthropic-browser.ts 삭제
- [ ] processPdfFile 분기 → server-only 경로로 일원화
- [ ] env 의 ANTHROPIC_API_KEY 키 회전 (브라우저로 노출됐던 값은 폐기)
```

회수 절차에 **API key 회전** 이 빠지면 의미가 없다. 한 번이라도 클라이언트에 내려갔던 키는 유출 전제로 다뤄야 한다.

## 정공법 — escape hatch 의 대안들

| 방법 | 60s 우회 | 비용 | 도입 난이도 | 비고 |
|---|---|---|---|---|
| **Vercel Pro 업그레이드** | ✅ 300초 | $20/mo + | 즉시 | 가장 단순. 300초로도 부족하면 다른 옵션 필요 |
| Vercel Functions Streaming | △ 응답 streaming 만 가능 | 동일 | 중 | LLM 호출 자체의 wall-clock 은 그대로 |
| 외부 워커 (Cloud Run / Fargate) | ✅ 무제한 | $5~/mo + | 높음 | 가장 깔끔. 큐 + 워커 + 결과 폴링/웹소켓 |
| 작업 큐 (Inngest / Trigger.dev) | ✅ | 무료 tier 있음 | 중 | Vercel 친화적, async 패턴이 매끄러움 |
| 외부 cron + 결과 캐시 | ✅ (사용자 즉시성 포기) | 거의 무료 | 낮음 | "오늘 분석된 회사만 보여주기" 식으로 UX 변경 |
| **Browser-direct (이 패턴)** | ✅ | 무료 | 가장 낮음 | 보안 부채 동반. 임시 |

도입 난이도 / 시간 / 보안 의 trade-off. **장기 운영 의도가 있으면 처음부터 외부 워커**가 정답이고, **출시 데드라인이 임박했고 회수 약속이 명확하면** browser-direct 가 실용적.

## 함정과 주의

- **`dangerouslyAllowBrowser: true`** 는 Anthropic SDK 가 의도적으로 막아 둔 안전장치를 푸는 옵션. 이름 그대로 dangerous — DOM 에서 호출되는 코드가 키를 만질 수 있음을 SDK 가 명시한다. 이 옵션을 쓴 코드 줄에는 "TEMPORARY" 마킹 + 회수 조건 한 줄 주석을 권장.
- **시스템 프롬프트를 키와 함께 내려주면 같이 노출**된다. 영업비밀 수준의 프롬프트면 분리하거나, 프롬프트 자체를 client-direct 호출에는 포함시키지 말 것.
- **Tool definitions / few-shot 예시도 노출**된다 — 같은 이유.
- **인증 통과 후에만 키 발급**이 강제되는지 미들웨어 / route handler 에서 한 번 더 검증. 인증 우회 가능한 path 가 하나라도 있으면 키가 무방비로 새 나감.
- **로그/모니터링이 없어진다** — 사용자 호출이 서버를 거치지 않으므로 서버 로그에 "누가 언제 호출했는지" 가 안 잡힘. Anthropic 의 usage logs 와 사용자 인증 로그를 시간으로 cross-check 해야 책임 추적이 가능.
- **CORS / preflight** — Anthropic SDK 는 자체 처리하지만 직접 fetch 로 다른 LLM API 를 두드릴 때는 origin 화이트리스트가 필요할 수도 있음.

## 인접 패턴 — 서버 측 60초 timeout 의 다른 갈래

같은 lambda 한도 문제를 다른 결로 푸는 사례:

- **Cron 라우트의 timeout** ([[vercel-cron-best-practices]] §7) — 외부 I/O 병렬 + DB write 직렬 + DIRECT_URL 우회 같이 lambda 안에서 풀 수 있는 경우. 사용자 즉시성이 없는 batch 라서 lambda 안에서 끝낼 수만 있으면 OK.
- **Prisma + PgBouncer 환경의 connection acquire 누적** ([[prisma-connection-pool-vercel-supabase]]) — 60초 timeout 의 또 다른 단골 원인. 같은 증상이라도 client-direct 로는 못 푼다 (DB 호출이 본질).
- **Edge runtime** — node runtime 보다 빠른 cold start, 다른 timeout. 단 Anthropic SDK / Prisma / pdf-parse 같은 라이브러리 호환성을 먼저 확인해야 한다.

## 변경 이력

- 2026-05-05: 최초 생성. finance-analysis-nextjs 의 큰 PDF vision 분석이 Vercel Hobby 60초 lambda timeout 에 걸리던 사고에서 도출. 인증 사용자에게 API key 를 내려주고 브라우저에서 Anthropic SDK 직접 호출로 우회. 보안 부채 (DevTools 키 추출 가능) 와 회수 조건을 명시한 escape hatch 패턴으로 정리 (출처: session-logs/20260505-101659-115c-*)
