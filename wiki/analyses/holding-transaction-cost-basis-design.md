---
title: "보유 종목 매수/매도 거래 추적 설계 — 평균단가·실현손익 동결·삭제 역연산"
domain: "trading"
sensitivity: public
tags: ["analysis", "accounting", "portfolio", "transaction", "cost-basis", "realized-gain", "korean-tax"]
created: 2026-05-09
updated: 2026-05-09
source_session: 20260509-080729-fd9f-기존-종목을-매수-또는-매도-하는-것을-관리하는-부분이-없는-것-같습니다.md
sources:
  - "session-logs/20260509-080729-fd9f-*.md"
confidence: medium
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/patterns/zod-schema-per-entity.md"
---

# 보유 종목 매수/매도 거래 추적 설계 — 평균단가·실현손익 동결·삭제 역연산

개인용 자산 대시보드, 위탁계좌 시뮬레이터, 가계부의 투자 모듈 등 「BUY/SELL 거래 → 보유 종목 평균단가·수량·계좌 현금에 반영」 워크플로를 구현할 때의 설계 결정 묶음. 한국 양도세 표준 + 1인 운영 환경의 단순성 가정.

## 개요

「Holding (현재 보유)」 만 관리하면 사용자가 매수·매도 시 평균단가를 손으로 재계산해야 하고, 매도 이력·실현 손익이 어디에도 안 남아 양도세 신고가 불가능해진다. 별도 `Transaction` 테이블로 거래 이력을 보존하고 ‖ 보유·계좌 현금을 자동 갱신 ‖ 매도 시점의 실현 손익은 거래 row 에 박아 보존하는 게 정공법.

## 4가지 핵심 결정

### 1. 평균단가 산식 — 한국 양도세 표준 (수수료 취득원가 포함)

```
buy:  newAvg = (oldAvg·oldQty + price·qty + fee) / (oldQty + qty)
sell: avgCost 그대로 유지, qty 만 감소
```

수수료를 **취득원가에 포함** 하는 것이 한국 양도세 신고 표준. 매도 시 수수료는 별도로 차감 (`realizedGain = (price - avgCost)·qty - fee`). 매도해도 평균단가는 흔들지 않는다 — 남은 수량의 평단은 매수 시점에 결정된 값을 그대로 유지.

### 2. 실현 손익 — 매도 거래 row 에 「박아둠」

```sql
Transaction.realizedGain Decimal?  -- SELL 행만 채움. BUY 는 NULL.
```

집계 시 `SUM(Transaction.realizedGain) WHERE type = SELL` 한 줄. **현재 평단가에서 역산하지 않는다** — 이후 추가 매수로 평단가가 바뀌어도 과거 매도의 실현 손익이 흔들리지 않게 하기 위함.

이 패턴의 일반화: **시점성 있는 파생값은 row 에 박아 immutable 화**. 회계·세금·감사처럼 **그 시점의 사실** 이 의미 있는 도메인에선 불가결. 같은 패턴을 [[partial-sell-rule-idempotency]] 의 `realizedGain` 누적 카운터에서도 사용.

대비 (안티패턴): "현재 평단가 - 매도가 × 수량" 으로 매번 재계산 → 추가 매수 1건이 과거 매도 손익을 모두 흔들어 양도세 신고 자료의 신뢰성 0.

### 3. 삭제는 역연산

거래 row 삭제 시 그 거래의 효과를 정확히 역으로 적용:

| BUY 삭제 | SELL 삭제 |
|---|---|
| `qty -= 거래수량`, 평균단가 재계산 (`(curAvg·curQty - price·qty - fee) / (curQty - qty)`) | `qty += 거래수량`, 평균단가 그대로 유지 (매도 시점에도 안 바꿨으므로) |
| `cashBalance += 거래총액 + fee` | `cashBalance -= 거래총액 - fee` |

수정 (UPDATE) 은 1차 미지원 — 「삭제 후 재입력」 으로 우회. 수정의 역연산은 BUY/SELL 두 효과를 동시에 풀어야 해서 코너케이스가 폭증.

**`prisma.$transaction([...])` 으로 묶기**: 수량·평단·현금 갱신은 모두 한 트랜잭션 안. 중간에 실패하면 셋 다 롤백 → 정합성 유지. 삭제도 동일.

### 4. 현금 자동 갱신은 통화 일치 시에만

```ts
const cashAdjusted = form.cashAdjusted && account.currency === transaction.currency;
if (cashAdjusted) {
  // BUY: cashBalance -= total
  // SELL: cashBalance += total
}
```

해외주식 매수처럼 **계좌 통화 (KRW) 와 거래 통화 (USD) 가 다른 경우** 환전 시점·환율을 누가 정의하느냐의 모호함이 발생. 자동 갱신을 끄고 사용자가 별도 환전 거래를 입력하는 정공법으로 회피.

`fxRate` 필드는 거래 row 에 두되, 자동 갱신에는 사용하지 않음. 통화별 합계 표시 / 양도세 환산용으로만 사용.

## 보유 종목 → 거래 진입점

도메인 객체 (Holding) 의 detail 페이지가 모든 거래 진입점:

```
/holdings/[id]                  보유 카드 (수량/평단/누적 실현손익) + 거래 히스토리 + [매수][매도] 버튼
/holdings/[id]/trade/new?type=BUY|SELL   거래 폼
```

기존 `/holdings/new`, `/holdings/[id]/edit` 는 「최초 등록 / 직접 편집」 용으로 유지 — 평단가를 손으로 입력해서 historic 보유를 빠르게 등록하는 케이스를 위해.

## Prisma 스키마

```prisma
enum TransactionType { BUY SELL }

model Transaction {
  id            String          @id @default(cuid())
  accountId     String
  account       Account         @relation(fields: [accountId], references: [id])
  holdingId     String
  holding       Holding         @relation(fields: [holdingId], references: [id])
  type          TransactionType
  tradeDate     DateTime        @db.Date
  quantity      Decimal
  pricePerShare Decimal
  fee           Decimal         @default(0)
  currency      Currency
  fxRate        Decimal?
  realizedGain  Decimal?        // SELL 만 채움
  notes         String?
  createdAt     DateTime        @default(now())

  @@index([holdingId, tradeDate])
  @@index([accountId, tradeDate])
}
```

`@@index([holdingId, tradeDate])` 는 보유 detail 페이지의 「최근 거래순」 정렬용. `accountId, tradeDate` 는 양도세 페이지의 연도별 집계용 (`WHERE accountId AND tradeDate BETWEEN ...`).

## 점진 도입 — MVP / 풀구현 / 입력만

| 범위 | 포함 | 작업량 | 추천 |
|---|---|---|---|
| **A. MVP** | Transaction 모델 + BUY/SELL action (평단·수량·현금 자동 갱신, realizedGain 보존) + 보유 detail 페이지 | 1세션 | ✅ 1차 적용 |
| **B. 풀구현** | A + Tax 페이지 실현/미실현 분리 + DEPOSIT/WITHDRAW/FEE/REINVEST + CSV export | 다세션 | 데이터 누적 후 |
| **C. 입력만** | Transaction 모델 + 단순 입력/리스트, Holding 자동 갱신 없음 | 작음 | ❌ 의미 없음 |

C 가 안 되는 이유: Holding 자동 갱신이 없으면 사용자가 거래를 입력해도 평단가를 손으로 또 갱신해야 함 → 거래 추적의 본래 가치 (평단·실현손익 자동화) 가 0. 입력 폼만 늘려 UX 부담만 추가.

A → B 점진 도입의 핵심: **enum 1차 범위는 BUY/SELL 만**. DIVIDEND 는 별도 모델 (`Dividend`) 이 이미 있다면 그쪽이 SSOT, 거래 enum 에 다시 넣지 말 것. DEPOSIT/WITHDRAW 같은 현금 흐름은 `Account.cashBalance` 의 직접 입력으로 이미 표현 가능 — 거래 추적 모델에 끌어 넣을지는 양도세 신고 자료의 완결성 요구 시점에 결정.

## 회계성 데이터의 일반 원칙

1. **「예측치」 와 「실측치」 는 같은 컬럼에 섞지 말 것** — `Holding.dividendYield(%)` 의 「예상 배당」 과 `Dividend.amount` 의 「실제 배당」 처럼 분리. 평가액 (mark-to-market) 과 누적 입금액의 분리도 같은 패턴 (japa 의 `contributionYTD` 별도 컬럼).
2. **시점성 파생값은 row 에 박아둠** — 실현손익 / 환율 적용 결과 / 세금 원천징수액. 「현재 상태에서 역산」 하지 않음.
3. **mutate 하는 갱신은 `$transaction` 으로 묶음** — 수량 / 평단 / 현금 셋이 따로 노는 시간이 한 순간이라도 있으면 정합성 깨짐.
4. **삭제는 역연산이 가능해야** — row 가 곧 「내가 한 변경」 의 구체화. 같은 데이터로 정확히 풀 수 있어야 신뢰성.

## 관련 페이지

- [[japa-asset-dashboard]] — 본 패턴 적용 사례 (commit `0aa2187`, +938/-16, 2026-05-09)
- [[zod-schema-per-entity]] — `lib/transactions/schema.ts` 의 entity별 분리 패턴
- [[partial-sell-rule-idempotency]] — once-flag dict + state 영속화. 「발동 사실의 동결」 이라는 같은 패턴
- [[prisma-connection-pool-vercel-supabase]] — `prisma.$transaction` 사용 시의 connection pool 주의사항

## 변경 이력

- 2026-05-09: 초안 작성. japa 의 BUY/SELL transaction tracking MVP 구현 (commit `0aa2187`) 에서 추출. Korean tax 표준 가중평균 + 실현손익 동결 + 삭제 역연산 + 통화 일치 시 cashBalance 자동 갱신
