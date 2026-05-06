---
title: "KIS 매수가능 현금에 D+2 미정산분 누락 → RiskManager 매수 차단"
domain: "personal"
sensitivity: "public"
tags: ["bug", "kis", "trading", "ht-trading", "settlement", "d+2"]
created: "2026-05-07"
updated: "2026-05-07"
sources:
  - "session-logs/20260506-230405-a179-오늘-매수가능-금액이-있었는데도-매수가-리스크에-걸려-매수-되지-않았습니다.-제가-확인해.md"
confidence: "high"
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/kis-balance-api-fields.md"
---

# KIS 매수가능 현금에 D+2 미정산분 누락 → 매수 차단

ht_trading 의 KIS 잔고 조회에서 `cash` 키에 `dnca_tot_amt` (예수금총액 = D+0 출금가능) 만 매핑하던 탓에, **당일 매도분 (D+2 정산 대기) 이 매수 한도에 반영되지 않아** RiskManager 의 현금 검증에 걸려 매수가 차단되던 버그.

## 증상

- 화면 / KIS HTS 에서 "매수가능 금액" 은 충분
- ht_trading 라이브 엔진 로그: "매수 거부 — 현금 부족"
- 발생 시점: 매도가 있었던 당일 직후의 매수 사이클

## 진단 흐름

1. `risk/manager.py:_validate_buy` 의 5번째 조건 ("주문금액 > 보유현금") 에 걸린 것 확인
2. `KISBroker.get_cash` → `DomesticAPI.get_balance` → `summary["dnca_tot_amt"]` 매핑 추적
3. 한국 주식 D+2 결제 사이클 + 매도 직후 매수 가능 정책 인식
4. KIS 응답 필드 비교 — `prvs_rcdl_excc_amt` 가 매도 미정산 포함 매수가능 현금임을 확인 → [[kis-balance-api-fields]]

## 수정

`src/ht_trading/api/domestic.py:get_balance()` 의 `cash` 매핑을 변경:

```python
deposit = int(summary.get("dnca_tot_amt", "0"))
settled_d2 = int(summary.get("prvs_rcdl_excc_amt", "0"))
cash = max(deposit, settled_d2)   # D+2 정산까지 포함한 매수가능 현금
return {
    "holdings": holdings,
    "total_eval": int(summary.get("tot_evlu_amt", "0")),
    "cash": cash,
    "deposit": deposit,   # 출금가능 예수금은 별도 키로 노출
    "total_pnl": int(summary.get("evlu_pfls_smtl_amt", "0")),
}
```

`max()` 사용 이유: KIS 모의투자에서 `prvs_rcdl_excc_amt = 0` 으로 내려오는 케이스 fallback.

## 회귀 테스트

`tests/api/test_domestic_balance.py` 신규 추가 — 3 케이스:

1. **D+2 사용** — 매도 미정산 (`prvs_rcdl_excc_amt > dnca_tot_amt`) 시 D+2 값 채택
2. **D+2 = 0 fallback** — 모의투자 케이스, `dnca_tot_amt` 채택
3. **매도 없음 동일값** — 두 필드 같으면 그 값 채택

모두 통과. 기존 도메스틱 테스트도 회귀 없음.

## 다운스트림 영향

전 체인 (`KISBroker.get_cash` → `_cached_cash` → `ctx.cash` → `RiskManager._validate_buy`) 이 동일한 `cash` 키를 사용하므로 다운스트림 변경 불필요.

## 교훈

- KIS 응답의 `dnca_tot_amt` 와 `prvs_rcdl_excc_amt` 는 같은 "예수금"이 아니다. 사용 목적 (출금 vs 매수) 별로 다른 필드를 골라야 한다 — [[kis-balance-api-fields]]
- 매수 한도 검증은 결제 사이클을 고려하지 않으면 매도-매수 회전이 막힌다. 결제 시스템이 다른 시장에 같은 패턴이 재현될 가능성 (해외주식, 선물 등)
- 단위 테스트는 모킹된 KIS 응답 형태를 명시적으로 검증해야 한다 — 응답 필드 변경에 빠르게 반응

## 관련 commit

- `c6109f4` (ht_trading) — fix: 매수가능 현금에 당일 매도 미정산 금액 포함 (`prvs_rcdl_excc_amt` 사용)

## 변경 이력

- 2026-05-07: 최초 작성 (세션 로그 20260506-230405-a179)
