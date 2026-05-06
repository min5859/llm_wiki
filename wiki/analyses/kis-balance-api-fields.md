---
title: "KIS 잔고 API 응답의 현금 필드 의미 (예수금 vs 매수가능)"
domain: "personal"
sensitivity: "public"
tags: ["kis", "trading", "api", "settlement", "d+2", "cash"]
created: "2026-05-07"
updated: "2026-05-07"
sources:
  - "session-logs/20260506-230405-a179-오늘-매수가능-금액이-있었는데도-매수가-리스크에-걸려-매수-되지-않았습니다.-제가-확인해.md"
confidence: "high"
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/bugs/kis-cash-d2-settlement-buy-rejection.md"
---

# KIS 잔고 API 응답의 현금 필드 의미

한국투자증권(KIS) Open API 의 잔고 조회 응답 (`/uapi/domestic-stock/v1/trading/inquire-balance`, TR `TTTC8434R`) 의 summary 블록에는 **현금 비슷한 값이 여러 개** 들어 있다. "예수금 = 매수가능"으로 단순 매핑하면 D+2 정산 사이에 매수가 차단되는 버그가 발생한다. 각 필드가 무엇을 의미하는지 정리.

## 핵심 비교

| 필드 | 의미 | 시점 | 매도 미정산 포함? |
|------|------|------|-------------------|
| `dnca_tot_amt` | **예수금총액** (D+0 잔고) | 즉시 (출금가능에 가까움) | ❌ 미반영 |
| `nxdy_excc_amt` | 익일정산금액 (D+1 결제분 반영) | T+1 | 일부 |
| `prvs_rcdl_excc_amt` | **가수도정산금액** (D+2 결제 완료 가정) | T+2 | ✅ 매도 미정산 포함 |
| `tot_evlu_amt` | 총평가금액 (= 평가 + 예수금) | 즉시 | — (현금이 아님) |
| `evlu_pfls_smtl_amt` | 평가손익합계금액 | — | — |

## "매수가능 현금"으로 사용해야 하는 값

매수 한도 검증에 사용해야 할 값은 **`prvs_rcdl_excc_amt`** (가수도정산금액) 이다. 이유:

- 한국 주식은 **D+2 결제** 시스템 — 오늘 매도해도 현금은 2영업일 후 입금
- 그러나 한국 시장에서는 **매도 직후 그 금액으로 다른 종목을 매수할 수 있다** (미수 거래로 처리되지 않음)
- 즉 매수 한도는 "오늘 매도분 + 기존 예수금"이며, 이는 KIS 응답에서 `prvs_rcdl_excc_amt` 에 노출됨
- `dnca_tot_amt` 는 "지금 출금하면 빼낼 수 있는 돈"에 가까워 매도 직후엔 항상 작은 값

KIS 가 별도로 제공하는 **매수가능조회 API** (`/uapi/domestic-stock/v1/trading/inquire-psbl-order`) 도 같은 로직을 거쳐 이 값을 돌려준다.

## 안전한 매핑 패턴

라이브 환경에서는 `prvs_rcdl_excc_amt` 가 정상 값이지만, **모의투자 환경에서는 0 으로 내려오는 케이스**가 있다. 안전하게는 두 값 중 **큰 값**을 사용:

```python
deposit = int(summary.get("dnca_tot_amt", "0"))
settled_d2 = int(summary.get("prvs_rcdl_excc_amt", "0"))
cash = max(deposit, settled_d2)   # 매수가능 현금
return {
    "cash": cash,        # 매수 한도 검증용
    "deposit": deposit,  # 출금가능 (별도 노출)
    ...
}
```

## 자주 빠지는 함정

- "예수금"이라는 단어가 한국어로 두 가지를 가리킴 — KIS API 가 사용하는 "예수금"(`dnca_tot_amt`) 은 D+0 출금가능에 가깝고, 일반인이 말하는 "예수금"은 매수가능에 가까움. **번역에 의존하면 안 됨**.
- 평가금 (`tot_evlu_amt`) 은 이미 보유 주식 평가까지 합쳐진 총자산이라 매수 한도가 아니다. 포지션 sizing 의 분모로는 적절하지만 RiskManager 의 "현금 충분성" 검증에는 부적절.
- KIS 모의투자는 응답이 라이브와 미묘하게 다름. `prvs_rcdl_excc_amt` 가 0 으로 오는 경우가 보고됨 → fallback 필수.

## 변경 이력

- 2026-05-07: 최초 작성. ht_trading 매수 차단 버그 진단 과정에서 도출 (세션 로그 20260506-230405-a179)
