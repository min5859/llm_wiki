---
title: "평가곡선 집계 함정 — MAX(equity) 는 최신 잔고가 아니라 역대 고점"
domain: "trading"
sensitivity: public
tags: ["bug", "trading", "sql", "aggregation", "equity-curve", "paper-trading", "ht-dde", "reporting"]
created: "2026-06-30"
updated: "2026-06-30"
sources:
  - "session-logs/20260630-080924-22d3-지금까지-돌린거-알고리즘들-평가해줘.md"
confidence: high
related:
  - "wiki/projects/ht-dde.md"
  - "wiki/bugs/round-winrate-exit-type-undercount.md"
  - "wiki/analyses/scoring-version-comparison-methodology.md"
---

# 평가곡선 집계 함정 — MAX(equity) 는 최신 잔고가 아니라 역대 고점

평가곡선(equity curve)·누적 잔고 같은 **시계열 테이블에서 전략별 "현재 자본"을 뽑을 때 `MAX(equity)` 로 집계하면 역대 고점(peak)이 나온다.** 운영 중 한 번이라도 플러스를 찍었다면 MAX 는 그 고점을 반환하므로, 실제로는 큰 손실 중인 전략이 **수익 중인 것처럼 보이는 착시**가 생긴다. 현재 잔고는 반드시 **시각이 가장 최신인 행**으로 뽑아야 한다.

## 사례 (ht_dde 종이거래 평가, 2026-06-30)

같은 `equity` 테이블을 두 방식으로 집계했더니 정반대 결론이 나왔다.

```sql
-- ❌ 잘못: 역대 고점(peak)을 "최신 자본"으로 오인
SELECT strategy, ROUND(MAX(equity),0) AS latest_equity
FROM equity GROUP BY strategy;
--   aggressive 10,230,329  (← +2.3% 수익 중인 듯 보임)

-- ✅ 정정: ts 가 가장 최신인 행의 equity
SELECT e.strategy, ROUND(e.equity,0) latest_equity,
       ROUND(100.0*(e.equity-10000000)/10000000,2) ret_pct
FROM equity e
JOIN (SELECT strategy, MAX(ts) mts FROM equity GROUP BY strategy) m
  ON e.strategy=m.strategy AND e.ts=m.mts
ORDER BY ret_pct DESC;
--   aggressive 8,004,591  ret_pct -19.95%  (← 실제로는 큰 손실)
```

`aggressive` 전략은 MAX 집계로는 +2.3% 처럼 보였지만 최신 잔고 기준으로는 **-19.95%** 였다. 고점과 현재값의 차이가 20%p 를 넘는 전형적인 오독 사례.

## 원인

- 평가곡선은 단조증가가 아니다. 운영 중 잔고가 오르내리므로 `MAX` 는 **그 구간의 최고점**을 집어 든다.
- 손익이 마이너스인 전략일수록 "한때 찍었던 고점"과 "지금 잔고"의 괴리가 커서 오독이 더 위험하다.
- `GROUP BY strategy` 로 묶을 때 `MAX(equity)` 와 `MAX(ts)` 를 같은 행으로 착각하기 쉽다 (비집계 컬럼 혼용 함정과 동형).

## 올바른 패턴

- **최신 행 조인**: `JOIN (SELECT key, MAX(ts) FROM t GROUP BY key)` 로 그룹별 마지막 시각을 구해 원본과 조인.
- 또는 윈도우 함수: `ROW_NUMBER() OVER (PARTITION BY strategy ORDER BY ts DESC)=1`.
- 보고 시 **수익률(%)을 함께** 표기하면 (`(eq-seed)/seed`) 부호가 바로 보여 고점 착시를 즉시 잡는다.
- "역대 고점(MFE 성)"과 "현재 잔고"는 **다른 지표**다. 둘 다 보고 싶으면 `MAX(equity)` 와 최신-잔고를 **별도 컬럼**으로 둔다.

## 일반 교훈

- **집계 함수는 의미를 바꾼다**: 누적/평가 시계열에 `MAX`·`SUM`·`AVG` 를 그냥 걸면 "최신 상태"가 아니라 "전 구간 통계"가 나온다. 현재 상태를 원하면 항상 시각 기준 마지막 행.
- 같은 함정의 사촌: [[round-winrate-exit-type-undercount]] (집계 분류 누락), 평가 보고의 `%` vs `%p` 혼동([[backtest-timeframe-sensitivity]]).
- A/B 전략 비교([[scoring-version-comparison-methodology]])처럼 숫자로 우열을 판정하는 작업에서는, 집계 쿼리 자체가 결론을 뒤집을 수 있으므로 **부호·기간·기준선을 함께 검산**한다.

## 변경 이력

- 2026-06-30: 최초 작성 — ht_dde 종이거래 평가 중 `MAX(equity)` 가 고점을 반환해 -19.95% 손실 전략이 수익처럼 보인 오독을 일반화 (출처: session-logs/20260630-080924-22d3-*)
