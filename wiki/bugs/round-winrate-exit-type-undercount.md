---
title: "라운드 승률 집계 0% — 종료 유형 분류가 일부 케이스만 커버"
domain: personal
sensitivity: public
tags: ["bug", "upbit-trading", "backtest", "statistics", "aggregation", "enum-coverage"]
created: "2026-06-07"
updated: "2026-06-07"
sources:
  - "session-logs/20260606-210943-6534-아래-개선사항을-검토-•-PAUSED가-6시간-이상-지속되면-텔레그램-알림을-보내도록-하면.md"
confidence: high
related:
  - "wiki/projects/upbit-trading.md"
  - "wiki/analyses/backtest-timeframe-sensitivity.md"
  - "wiki/analyses/dca-trailing-stop-tuning.md"
---

# 라운드 승률 집계 0% — 종료 유형 분류 누락

upbit_trading 백테스트 리포트의 `_calculate_round_summary`가 라운드 **종료 유형을 `infinite_buy_target`/`stop_loss`로만 분류**해 승률·목표달성을 집계했다. 실제 청산은 대부분 `trailing_stop`/`time_exit`/`partial`이라 집계에서 통째로 누락 → "라운드 승률 0%, 목표 달성 0회"로 표시되는 버그. 수익률 비교(시뮬레이터 실제 자산 기준)에는 영향이 없지만 **리포트만 보면 전략이 한 번도 못 이긴 것처럼 오해**하게 만든다.

## 핵심 사실

- 위치: `backtest/backtester.py` 의 `_calculate_round_summary` + `_print_report` 출력부
- 증상: 평균 수익률 `+6.45%`인데 "목표 달성 0회 / 손절 0회 / 승률 0.0%" — 평균은 양수인데 승률 0%라는 모순이 단서
- 원인: 종료 사유를 두 enum(`target`/`stop_loss`)으로만 매칭. trailing_stop·time_exit·partial 종료가 어느 분류에도 안 잡혀 분모/분자에서 빠짐

## 수정

승률을 종료 유형이 아니라 **실현 손익 부호(`profit > 0`)** 기준으로 계산하고, 종료 유형은 `exit_breakdown`으로 별도 집계:

```python
# before: target/stop_loss 종료만 세어 승률 산정 → trailing/time/partial 누락
# after: profit > 0 기준 승률 + 종료 유형별 분해
wins = sum(1 for r in rounds if r.profit_percent > 0)
win_rate = wins / len(rounds) if rounds else 0.0
exit_breakdown = Counter(r.exit_reason for r in rounds)  # trailing_stop/time_exit/partial/target/stop_loss
```

수정 후 상승장 구간 재검증에서 승률이 0% → 100% 정상 표시됨을 확인 (commit `b947351`).

## 일반 교훈

- **통계 집계의 분류 기준이 enum 일부만 커버하면 0%/누락으로 왜곡된다.** 카테고리로 집계할 때는 "모든 케이스가 어느 한 버킷에 반드시 들어가는가"를 점검해야 한다. 종료 유형이 늘어나면(trailing/time/partial 추가) 집계 코드도 같이 갱신돼야 하는데, 이 동기화 누락이 전형적 함정.
- **승률처럼 "성공/실패" 본질이 있는 지표는 분류 태그가 아니라 결과 값(손익 부호)으로 직접 판정**하는 게 견고하다. 종료 유형은 별도 분해(breakdown)로 빼면 분류가 늘어도 승률 계산은 깨지지 않는다.
- **모순 신호로 버그를 잡는다**: "평균 수익률 양수인데 승률 0%"처럼 내부적으로 앞뒤가 안 맞는 리포트 수치는 집계 버그의 강한 단서다.

## 관련 맥락

- [[upbit-trading]] — 이 버그가 발견된 프로젝트. 추세필터 ON/OFF 백테스트 비교 중 발견
- [[backtest-timeframe-sensitivity]] — 같은 세션의 백테스트 비교 방법론 (수익률 비교 자체는 이 버그와 무관)

## 변경 이력

- 2026-06-07: 최초 작성 — 추세필터 비교 백테스트 중 "승률 0%, 목표달성 0회" 모순에서 발견·수정 (commit `b947351`, 출처: session-logs/20260606-210943-6534-*)
