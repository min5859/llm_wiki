---
title: "Prisma P2024 — Vercel + Supabase pgbouncer 환경에서 Connection Pool 고갈"
domain: both
sensitivity: public
tags: ["prisma", "supabase", "vercel", "pgbouncer", "p2024", "connection-pool", "createMany", "transaction"]
created: 2026-05-02
updated: 2026-05-02
sources:
  - "session-logs/20260501-213505-aecb-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
  - "wiki/patterns/vercel-cron-best-practices.md"
---

# Prisma P2024 — Vercel + Supabase pgbouncer 환경에서 Connection Pool 고갈

Vercel serverless + Supabase Transaction pooler + `connection_limit=1` 권장 구성에서, 무거운 `$transaction` 을 한 번의 사용자 액션에서 병렬로 8개 띄우니 풀이 10초 안에 비지 못해 후속 요청까지 모두 500을 뱉는 사고. 같은 Vercel 함수 인스턴스가 받는 그 다음 GET 들도 풀이 망가진 상태로 같이 고꾸라진다.

## 증상

Vercel 로그에 동일 패턴이 연쇄:

```
Error [PrismaClientKnownRequestError]: Invalid `prisma.holding.findMany()` invocation:
Timed out fetching a new connection from the connection pool.
{ code: 'P2024', meta: { modelName: 'Holding', connection_limit: 1, timeout: 10 }, clientVersion: '6.19.3' }
```

처음에 터지는 건 무거운 쓰기 (`marketIndexHistory.upsert`), 그 후 같은 함수 인스턴스가 처리하는 평범한 `findMany` 들도 모두 P2024 로 500. 사용자 입장에서는 "버튼 한 번 눌렀는데 사이트 전체가 죽었다" 로 보인다.

## 원인 — 단일 커넥션을 길게 점유하는 트랜잭션이 병렬화됨

japa 의 시세 새로고침 버튼이 부르는 [refreshPrices()](app/actions/prices.ts) 가 세 작업을 `Promise.all` 로 동시 실행했다:

1. `refreshAllPrices()` — 종목별 `priceCache.upsert` (concurrency 6)
2. `refreshMarketIndices()` — 8개 지수 `marketIndex.upsert`
3. **`refreshMarketHistory()`** — 8개 지수 × **365일 ≈ 2,920건의 upsert를 8개의 `$transaction` 으로 병렬 실행** ← 이게 결정타

Supabase Transaction pooler + `connection_limit=1` 는 한 함수 인스턴스가 단 1개 connection 만 보유한다 ([[nextjs-vercel-supabase-deployment]] 참조). 8개의 `$transaction` 이 그 1개의 connection 을 두고 경쟁하면서 10초 타임아웃에 걸리고, 트랜잭션이 실패하면 **Prisma client 의 connection pool 이 망가진 상태로 남아** 같은 인스턴스가 그 후 받는 요청들도 같은 P2024 로 500 을 뱉는다.

로그 흐름이 정확히 그 모양:

```
21:31:28 POST /accounts/...  500   ← marketIndexHistory.upsert 타임아웃
21:31:49 GET  /accounts/...  500   ← 같은 인스턴스, holding.findMany 타임아웃
21:32:00 GET  /              500   ← 후속 페이지뷰도 같이 죽음
```

## 핵심 인사이트 — `$transaction` 의 connection 점유 시간

Prisma 의 `$transaction([...queries])` 은 **모든 쿼리가 끝날 때까지 한 connection 을 잡고 있는다**. 365개 upsert 를 한 트랜잭션으로 묶으면 365 라운드트립 동안 풀 점유. 그게 8 인덱스 동시면 8개 트랜잭션이 1개 connection 을 두고 경합. `connection_limit=1` 은 잘못이 아니라 **serverless 의 정답**이고 ([[nextjs-vercel-supabase-deployment]] §5), 잘못된 건 그 환경에서 무거운 트랜잭션을 병렬화한 쪽.

## Fix — 불변 데이터는 `createMany({ skipDuplicates: true })`

`marketIndexHistory` 는 `@@id([symbol, date])` 복합 PK 가 있고, **과거 OHLC 는 사후 변경되지 않는다**. 즉 365건 upsert 트랜잭션이 필요 없고 한 방의 bulk insert 로 끝난다:

```ts
// 변경 전 — 365건 upsert 를 단일 $transaction 으로
await prisma.$transaction(
  quotes.filter((r) => r.close != null).map((r) =>
    prisma.marketIndexHistory.upsert({
      where: { symbol_date: { symbol, date: new Date(r.date) } },
      update: { open, high, low, close },
      create: { symbol, date, open, high, low, close },
    })
  )
);

// 변경 후 — 한 번의 createMany + 마지막 한 행만 upsert
const data = quotes.filter((r) => r.close != null).map((r) => ({
  symbol, date: new Date(r.date),
  open: r.open ?? r.close, high: r.high ?? r.close,
  low:  r.low  ?? r.close, close: r.close,
}));
if (!data.length) return;

// 과거 OHLC 는 불변 → skipDuplicates 안전
await prisma.marketIndexHistory.createMany({ data, skipDuplicates: true });

// 단, 오늘 행은 장중에 종가/고가/저가가 갱신될 수 있으므로 마지막 한 건만 덮어쓰기
const today = data[data.length - 1];
await prisma.marketIndexHistory.upsert({
  where: { symbol_date: { symbol, date: today.date } },
  update: { open: today.open, high: today.high, low: today.low, close: today.close },
  create: today,
});
```

지수당 ~365 라운드트립 → **2 라운드트립** 으로 떨어뜨려 풀 점유 시간이 극적으로 짧아진다. `connection_limit=1` 환경에서도 안정적으로 끝남.

## 구조적 fix — 무거운 작업은 click-path 에서 cron 으로

위 변경만으로 즉시 사고는 풀리지만, **"버튼 클릭 한 번에 1년치 OHLC 를 다시 훑는 비효율"** 은 그대로 남는다. 정공법은 click-path 에서 분리하고 일별 Vercel Cron 으로 옮기는 것:

- **클릭 액션** — `priceCache` (보유 종목) + `marketIndex` (현재 지수값) 만 갱신. 가벼움.
- **일별 cron** — `marketIndexHistory` 1년치 갱신. 매일 1회면 충분 (과거는 불변).

자세한 cron 패턴은 [[vercel-cron-best-practices]].

## 일반화 — 이 함정에 빠지는 신호

다음 조합이 한 번에 모이면 P2024 사고가 나온다:

| 신호 | 위험 |
|---|---|
| Vercel serverless + `connection_limit=1` | 함수당 1 connection (정답) |
| `Promise.all` 로 `$transaction` 병렬화 | 1 connection 을 N개 트랜잭션이 경합 |
| 트랜잭션 안에 100+ 쿼리 | 점유 시간 = N 라운드트립 |
| 쓰는 데이터가 사실상 **불변** | `$transaction` 자체가 불필요 |

마지막 항목이 핵심이다. **불변 시계열 / 로그 / 과거 OHLC** 같은 데이터는 `createMany({ skipDuplicates: true })` 한 번이 정답이고, `upsert` 트랜잭션은 오버킬이다. "트랜잭션 = 정합성" 이 무조건 좋아 보여서 자동으로 끼워넣지 말 것.

## 인접 함정 — 같은 인스턴스 후속 요청 연쇄 실패

Prisma Client 의 connection pool 이 한번 망가지면, **같은 함수 인스턴스가 받는 그 후의 평범한 쿼리들도 같이 죽는다**. Vercel 함수는 짧은 시간 내 연속 요청을 같은 인스턴스가 처리하기 때문에, "버튼 안 누른 사용자도 사이트가 안 열린다" 는 사용자 경험으로 이어진다. 사용자가 보고한 첫 증상이 "버튼 안 눌렀는데 메인 페이지가 안 열림" 인 경우 P2024 만성화일 가능성이 높음.

## 변경 이력

- 2026-05-02: 최초 생성. japa 시세 새로고침 클릭 시 P2024 가 8개 `$transaction` 병렬화 + `connection_limit=1` 충돌로 발생 → `createMany skipDuplicates` + 일별 cron 분리로 해결 (출처: session-logs/20260501-213505-aecb-*)
