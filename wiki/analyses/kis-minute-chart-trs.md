---
title: "KIS 분봉 조회 TR 비교 — 당일 전용 vs 과거일 조회 (FHKST03010200 / FHKST03010230)"
domain: "trading"
sensitivity: "public"
tags: ["analysis", "trading", "kis", "api", "tr", "minute-bar", "chart"]
created: "2026-07-07"
updated: "2026-07-07"
sources:
  - "session-logs/20260706-214100-fe2f-무한매수-알고리즘에-종목을-하나더-추가하고-싶은데-KOSPI-0195S0-TIGER-SK하.md"
confidence: "high"
related:
  - "wiki/analyses/kis-balance-api-fields.md"
  - "wiki/analyses/dca-intraday-buy-timing.md"
  - "wiki/projects/ht-trading.md"
---

# KIS 분봉 조회 TR 비교 — 당일 전용 vs 과거일 조회

KIS OpenAPI 의 국내주식 분봉 조회 TR 두 가지의 결정적 차이. **`FHKST03010200` 은 당일 1분봉만 주고, 과거 특정일의 분봉은 `FHKST03010230` 으로만 조회할 수 있다.** 과거 시간대 분석(일중 매수 타이밍 검증 등)을 하려면 후자가 필수인데, 문서에서 눈에 잘 띄지 않아 미구현으로 남기 쉽다.

## 두 TR 비교

| | FHKST03010200 | FHKST03010230 |
|---|---|---|
| 엔드포인트 | `/uapi/domestic-stock/v1/quotations/inquire-time-itemchartprice` | `/uapi/domestic-stock/v1/quotations/inquire-time-dailychartprice` |
| 조회 범위 | **당일** 1분봉만 | **과거 특정일** 분봉 |
| 반환량 | — | 호출당 output2 약 120건 |
| ht_trading 구현 상태 | 구현됨 | 미구현이었으나 실동작 확인 (2026-07-06) |

## FHKST03010230 파라미터 (실측 동작 확인값)

```
FID_COND_MRKT_DIV_CODE = J        # 주식/ETF
FID_INPUT_ISCD         = <종목코드>
FID_INPUT_DATE_1       = <조회일 YYYYMMDD>
FID_INPUT_HOUR_1       = 153000   # 이 시각 이전 분봉을 역순 반환
FID_PW_DATA_INCU_YN    = N
FID_FAKE_TICK_INCU_YN  = ""
```

- 하루치 전체(약 381분)를 얻으려면 `FID_INPUT_HOUR_1` 을 내려가며 여러 번 호출해 이어붙인다 (호출당 ~120건).

## 관련 맥락

- 이 TR 로 수집한 과거 분봉이 [[dca-intraday-buy-timing]] 의 데이터 기반. 후속 과제: `domestic.py` 에 230 TR 정식 구현 (재검증용).
- KIS API 필드 의미 계열 레퍼런스: [[kis-balance-api-fields]].

## 변경 이력

- 2026-07-07: 최초 생성 — 0195S0 매수 시간대 검증에서 과거일 분봉 필요로 발견·실동작 확인 (출처: session-logs/20260706-214100-fe2f-*)
