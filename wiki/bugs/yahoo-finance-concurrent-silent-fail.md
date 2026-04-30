---
title: "Yahoo Finance 동시 호출 시 일부 응답에 regularMarketPrice 누락 (silent fail)"
domain: both
sensitivity: public
tags: ["yahoo-finance", "yahoo-finance2", "throttling", "silent-fail", "promise-allsettled", "worker-pool", "retry"]
created: 2026-04-30
updated: 2026-04-30
sources:
  - "session-logs/20260430-161410-0fcc-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
---

# Yahoo Finance 동시 호출 시 silent fail

`yahoo-finance2` 같은 비공식 라이브러리로 30개 정도의 심볼을 한 번의 `Promise.allSettled` 로 동시 호출하면, **일부 응답에서 `regularMarketPrice` 가 누락된 채로 fulfilled 상태로 돌아오는 케이스가 간헐적으로 재현**된다. throw 가 안 일어나기 때문에 단순 try/catch 로는 안 잡히고, 캐시 갱신 로직이 silently fail 하면서 옛 가격이 그대로 남는다.

## 증상

다음 패턴이 모두 맞으면 이 케이스다:

1. **PriceCache (또는 보유 종목 표시 가격) 의 일부가 며칠 전 시점에서 멈춤** — 다른 종목은 잘 갱신되는데 특정 5~6개만 stale.
2. **단일 심볼 호출은 모두 정상** — `yf.quote('005930.KS')` 직접 실행하면 매번 정상 가격 반환.
3. **30개 심볼 일괄 동시 호출** 도 격리 환경에서 따로 돌리면 100% 성공 (재현이 매번 안 됨).
4. **응답에 `regularMarketPrice` 가 `undefined`** 인데 `regularMarketPreviousClose`, `currency` 등은 채워져 있는 부분 응답이 섞여 있다.

## 원인 가설

Yahoo 인프라가 동시 요청 폭주에 대응해 일부 응답에서 라이브 가격 필드를 누락시켜 보내는 것으로 추정. 매번 같은 심볼이 누락되지 않고 무작위적이라 throttle 또는 lazy hydration 문제로 추정. 야후 응답 자체에 에러 표시가 없어 라이브러리가 fulfilled 로 처리한다.

이걸 silent fail 시켜서 다음 새로고침까지 옛 가격 그대로 두는 코드는 흔하다:

```ts
// BAD: 누락된 응답을 silently 무시
const results = await Promise.allSettled(symbols.map(s => yf.quote(s)));
for (const r of results) {
  if (r.status === 'fulfilled' && r.value.regularMarketPrice != null) {
    await updateCache(r.value);
  }
  // 누락된 건 그냥 흘려보냄 → 사용자에게도 안 알림
}
```

## 수정: 동시성 제한 + 1회 재시도 + 가시성

```ts
async function refreshSymbols(symbols: string[]) {
  const concurrency = 6;
  const results: Array<{ symbol: string; ok: boolean; reason?: string }> = [];

  // worker pool: 동시 6개씩만 처리
  const queue = [...symbols];
  await Promise.all(
    Array.from({ length: concurrency }, async () => {
      while (queue.length) {
        const s = queue.shift()!;
        results.push(await fetchOnce(s));
      }
    }),
  );

  return results;
}

async function fetchOnce(symbol: string) {
  for (let attempt = 0; attempt < 2; attempt++) {
    try {
      const q = await yf.quote(symbol);
      if (q?.regularMarketPrice != null) {
        await updateCache(symbol, q);
        return { symbol, ok: true };
      }
      // 1회차에서 가격 누락 → 250ms 대기 후 재시도
      if (attempt === 0) await sleep(250);
    } catch (e) {
      if (attempt === 0) await sleep(250);
      else return { symbol, ok: false, reason: (e as Error).message };
    }
  }
  return { symbol, ok: false, reason: 'no regularMarketPrice after retry' };
}
```

UI 측에는 `failed` 목록을 별도로 노출 (⚠️ 아이콘 + 사유) 해서 silently fail 하지 않도록 한다.

## 동시성 6 의 근거

한국 종목 25 + 미국 ETF 5 + FX 1 정도의 실제 포트폴리오에서:

- 동시성 30 (전체 한 번에) → silent fail 재현
- 동시성 6 → 30개 종목 전체 새로고침까지 ~3초 (체감상 늘어나지 않음) + silent fail 미재현

야후 throttle 위험을 거의 없애면서 사용자가 기다리는 시간도 늘리지 않는 균형점. 종목 수가 100+ 으로 늘어나면 별도 측정 필요.

## 진단 명령

```bash
# 1) DB 캐시 vs Yahoo 직접 호출 비교 — 어느 종목이 stale 인지 확인
node -e "
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();
prisma.priceCache.findMany({ select: { symbol: true, price: true, fetchedAt: true }, orderBy: { fetchedAt: 'asc' } })
  .then(rows => { console.log(rows.map(r => \`\${r.symbol}  \${r.price}  @ \${r.fetchedAt.toISOString()}\`).join('\n')); prisma.\$disconnect(); });
"

# 2) Yahoo 직접 단일 호출 — 라이브 가격 가져오는지 확인
node -e "
const YF = require('yahoo-finance2').default;
const yf = new YF({ suppressNotices: ['yahooSurvey'] });
['005930.KS','009150.KS'].forEach(async s => {
  const q = await yf.quote(s);
  console.log(s, q.regularMarketPrice, q.currency);
});
"

# 3) 30개 동시 호출 재현 — fulfilled 인데 price 누락된 응답이 있는지
node -e "
const YF = require('yahoo-finance2').default;
const yf = new YF({ suppressNotices: ['yahooSurvey'] });
const all = [/* 30개 심볼 배열 */];
Promise.allSettled(all.map(s => yf.quote(s).then(q => ({s, p: q?.regularMarketPrice}))))
  .then(rs => rs.forEach((r, i) => {
    if (r.status === 'rejected' || r.value.p == null) console.log('MISS:', all[i], r.status);
  }));
"
```

`MISS: 005930.KS fulfilled` 같은 출력이 나오면 본 케이스 확정.

## 일반화 — 외부 API 동시 호출의 silent partial response

이 패턴은 야후 한정이 아니다. 비공식 / rate-limit 있는 외부 API 를 동시 호출할 때 자주 발생:

- **응답 스키마 검증 필수** — 단순 `try/catch` 로 잡히지 않는 부분 응답을 `?.field == null` 같은 체크로 명시 검증.
- **동시성 제한** — `Promise.allSettled` 로 모두 던지기보다 worker pool 또는 `p-limit` 등으로 제한.
- **단일 retry** 로 일시적 누락 흡수. 무한 retry 는 throttle 을 악화시킬 수 있다.
- **실패 가시성** — 로그뿐 아니라 UI 에 명시적으로 노출. silent fail 은 디버깅 시점을 며칠 뒤로 미룬다 (이번 케이스도 4일 stale 후 발견).

## 관련 맥락

- [[japa-asset-dashboard]] 의 시세 새로고침 실패 5종목 (삼성전자 / 우 / 전기, 현대차, KODEX 레버리지) 을 추적하면서 발견.

## 변경 이력

- 2026-04-30: 최초 생성. PriceCache 4일 stale 디버깅 → Yahoo 동시 호출 silent fail 식별 + worker pool 6개 + 1회 재시도로 수정 (출처: session-logs/20260430-161410-0fcc-*)
