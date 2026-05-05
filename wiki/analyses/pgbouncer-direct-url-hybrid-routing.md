---
title: "PgBouncer + DIRECT_URL 하이브리드 — page 라우트와 cron lambda 의 이중 trafficking"
domain: both
sensitivity: public
tags: ["pgbouncer", "supabase", "prisma", "vercel", "serverless", "cron", "connection-pool", "direct-url"]
created: 2026-05-05
updated: 2026-05-05
sources:
  - "session-logs/20260505-084952-fe4f-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/bugs/prisma-connection-pool-vercel-supabase.md"
  - "wiki/patterns/vercel-cron-best-practices.md"
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
---

# PgBouncer + DIRECT_URL 하이브리드 라우팅

Vercel + Supabase + Prisma 환경에서 **page 라우트는 PgBouncer 풀을, cron / 배치 lambda 는 DIRECT_URL 을** 각각 별도 PrismaClient 로 사용하는 패턴. PgBouncer transaction mode 가 page 라우트에는 정답이지만 write-heavy lambda 에는 함정인 이유, 그리고 두 traffic 을 분리해서 양쪽 장점을 다 가져가는 방법.

## 동기 — PgBouncer transaction mode 가 cron 에 안 맞는 이유

Supabase 의 권장 구성은 다음 두 가지를 동시에 만족시킨다:

1. **page 라우트** — 짧은 read 위주, 동시 사용자 다수. PgBouncer transaction mode + `connection_limit=1` 이 표준 정답.
2. **cron / batch** — 긴 write 위주, 동시 호출 1개. lambda 안에서 query 수십~수백 개 쏟아짐.

문제는 같은 DATABASE_URL 을 두 traffic 에 쓰는 순간 한쪽이 망가진다는 점.

### Page 라우트에서 PgBouncer 가 정답인 이유

- Vercel serverless 함수는 invocation 마다 새 인스턴스 가능 → connection 1개씩만 잡아도 cold start 누적이 적음.
- PgBouncer 가 client connection 을 backend connection 으로 multiplex → Postgres backend 한도 (Supabase free tier 60개) 를 초과하지 않음.
- prepared statement 충돌 회피를 위해 `connection_limit=1` + `pgbouncer=true` 가 권장. Prisma 가 자동으로 prepared statement 를 끄고 단순 query 로 보냄.

### Cron lambda 에서 PgBouncer 가 함정인 이유

세 가지 누적 효과가 동시에 발생한다.

1. **connection_limit=1 의 동시 호출 충돌** — `Promise.all` 로 prisma 호출 N개 띄우면 모두 1개 connection 을 두고 경쟁 → P2024 (`Timed out fetching a new connection from the connection pool`). 자세한 건 [[prisma-connection-pool-vercel-supabase]].
2. **query 당 acquire 비용 누적** — 직렬화로 P2024 를 막아도, PgBouncer transaction mode 는 query 마다 backend connection 을 잡았다 놓는 사이클이 있다. 이 acquire round-trip 자체가 누적 → 9개 직렬 prisma upsert 가 23초씩 걸리는 기현상이 가능.
3. **prepared statement 비활성화의 latency 페널티** — `pgbouncer=true` 가 prepared statement 를 끄므로 매 query 가 단순 textual query 로 처리. statement caching 이 빠져 lambda 의 누적 latency 가 더 늘어남.

직렬화도 `createMany skipDuplicates` 도 (1) (2) 모두를 동시에 풀지는 못한다. 직렬화는 (1) 만, payload 축소는 (2) 의 절대값만 줄일 뿐.

## Fix — cron lambda 만 DIRECT_URL 로 PgBouncer 우회

Supabase 가 발급하는 두 가지 connection string 을 둘 다 사용한다.

```env
# 페이지 라우트 — Transaction pooler (포트 6543, pgbouncer + connection_limit=1)
DATABASE_URL="postgresql://USER:PASS@aws-1-ap-south-1.pooler.supabase.com:6543/postgres?pgbouncer=true&connection_limit=1"

# cron / 마이그레이션 — 5432 포트 직결 또는 Session pooler. PgBouncer 우회.
DIRECT_URL="postgresql://USER:PASS@aws-1-ap-south-1.pooler.supabase.com:5432/postgres"
```

```prisma
// prisma/schema.prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL")  // migrate / introspect 용
}
```

`directUrl` 은 Prisma 의 마이그레이션·introspection 용 옵션이지만, **PrismaClient 인스턴스에 별도 datasource 로 주입하면 런타임에서도 그대로 쓸 수 있다**.

```ts
// lib/prisma.ts
import { PrismaClient } from "@prisma/client";

declare global { var prismaGlobal: PrismaClient | undefined; }

// 페이지 라우트용 — DATABASE_URL (PgBouncer)
export const prisma = globalThis.prismaGlobal ?? new PrismaClient();
if (process.env.NODE_ENV !== "production") globalThis.prismaGlobal = prisma;

// cron / 배치용 — DIRECT_URL (PgBouncer 우회)
export const prismaDirect = new PrismaClient({
  datasources: { db: { url: process.env.DIRECT_URL! } },
});
```

```ts
// app/api/cron/daily/route.ts
import { prismaDirect } from "@/lib/prisma";
import { refreshAllPrices, refreshMarketIndices, refreshMarketHistory } from "@/lib/market";

export async function GET(req: Request) {
  // CRON_SECRET 검증 ...
  const [portfolio, indicesUpdated] = await Promise.all([
    refreshAllPrices(prismaDirect),
    refreshMarketIndices(prismaDirect),
    refreshMarketHistory(prismaDirect),
  ]);
  return Response.json({ ok: true, ran: [...] });
}
```

`lib/market.ts` 의 refresh 함수들은 default 인자로 풀드 `prisma` 를 받게 두고, cron route 에서만 `prismaDirect` 를 주입. 페이지 라우트는 영향 없음.

## 효과 측정 — 실제 사례

japa 자산 대시보드의 일별 cron 사례:

| 단계 | 호출 시간 | 상태 |
|---|---|---|
| `Promise.all` 직렬화만 적용 | 60초 timeout | `FUNCTION_INVOCATION_TIMEOUT` |
| + prisma mutex (`withPrismaLock`) | 60초 timeout | yahoo I/O 병렬 복원해도 동일 |
| + history fetch 1년 → 7일 | 60초 timeout | payload 축소만으로 부족 |
| + instrumentation fire-and-forget | 60초 timeout | startup hang 까지 풀어도 부족 |
| **+ `prismaDirect` (DIRECT_URL) 주입** | **24.5초 OK** | 41 holdings + 9 indices + history 정상 |

`refreshMarketIndices` 단독 timing: 23.5초 (PgBouncer) → 11.6초 (DIRECT). 약 50% 감소가 9개 직렬 upsert 에서 발생. Acquire 누적 비용이 무시 못 할 수준임을 보여 줌.

## 동시 사용 제약 — Supabase 무료 direct connection 한도

Supabase 무료 플랜의 **direct connection 한도는 ~60개**로 PgBouncer pool 보다 훨씬 작다. cron lambda 한 개가 동시에 잡는 connection 수가 적어야 안전:

- `prismaDirect` 인스턴스를 module scope 에 두고 재사용 (cold start 마다 1개)
- cron lambda 안에서도 prisma 호출은 mutex (`withPrismaLock`) 로 직렬화 — connection 하나만 활성화
- 페이지 traffic 은 절대 `prismaDirect` 를 안 쓰게 격리 (페이지 cold start 다수 동시 발생 → 60개 즉시 소진)

```ts
// lib/market.ts — prisma mutex
let writeQueue: Promise<unknown> = Promise.resolve();

export function withPrismaLock<T>(fn: () => Promise<T>): Promise<T> {
  const next = writeQueue.then(fn, fn);
  writeQueue = next.catch(() => undefined);
  return next;
}

// 사용
await withPrismaLock(() =>
  prismaDirect.priceCache.upsert({ where: ..., update: ..., create: ... })
);
```

mutex 가 lambda 안의 prisma 호출만 직렬화하므로 yahoo fetch 같은 외부 I/O 는 그대로 병렬. wall-clock 은 가장 느린 외부 호출 1개만큼만 소요.

## 3계층 분리 원칙

위 패턴은 일반화하면 다음 3계층 분리:

```
외부 I/O (yahoo, OpenAI, 다른 API)
  └─ 병렬 (Promise.all) — wall-clock 압축

DB write
  └─ mutex (withPrismaLock) — 자원 한정 안전화

DB connection
  └─ DIRECT_URL — PgBouncer overhead 제거
```

각 계층의 정답이 다르다는 점이 핵심. "전부 직렬화" 또는 "전부 병렬화" 의 단일 전략으로는 lambda budget 안에 못 들어간다.

## 언제 안 써도 되는가

- **페이지 traffic 만 있는 앱**: PgBouncer 가 정답. DIRECT_URL 은 마이그레이션용으로만 쓰고 런타임에서는 안 씀.
- **cron 도 가벼운 작업 (read-only count 1~2개 등)**: PgBouncer 그대로 충분. acquire 누적 비용이 1~2초면 무시 가능.
- **Pro 플랜 + maxDuration 300초**: budget 여유가 5배라 acquire 비용 누적이 budget 을 초과하지 않으면 그대로 가도 무방. 단 비용은 같이 늘어남.

DIRECT_URL 우회는 **lambda budget 압박이 실제로 보이기 시작한 시점에 도입하는 게 합리적**. 미리 도입하면 페이지 외 traffic 격리 / direct connection 한도 관리 같은 운영 부담만 추가됨.

## 진단 신호 정리

다음 패턴이 보이면 PgBouncer 우회를 검토:

- ✅ Vercel + Supabase + Prisma + `connection_limit=1`
- ✅ cron 또는 배치 lambda 의 prisma 호출 수가 두 자리수 이상
- ✅ P2024 는 직렬화 / mutex 로 막혔는데 여전히 60초 timeout
- ✅ timing log 상 직렬 prisma 호출 N개의 누적 시간이 yahoo round-trip 합보다 큼
- ✅ 페이지 traffic 은 정상 동작 중

마지막 항목이 "DATABASE_URL 자체를 바꾸지 마라" 의 신호. cron lambda 만 분리해 우회하는 게 정답.

## 변경 이력

- 2026-05-05: 최초 생성. japa 일별 cron 의 60초 timeout 사고 → `prismaDirect` (DIRECT_URL) 주입으로 24.5초 OK 까지 들어간 사례 정리. PgBouncer transaction mode 의 query 당 acquire 비용 누적, 외부 I/O / DB write / DB connection 의 3계층 분리 원칙, 페이지 traffic 격리 + 무료 direct connection 한도 관리 노하우 (출처: session-logs/20260505-084952-fe4f-*)
