---
title: "백테스트 벽시계 오염 — 전략 시각 소스 주입(clock injection) 패턴"
domain: "trading"
sensitivity: "public"
tags: ["pattern", "trading", "backtest", "clock", "datetime-now", "simulation", "dependency-injection"]
created: "2026-07-05"
updated: "2026-07-05"
sources:
  - "session-logs/20260704-130158-2dad-현재-프로젝트에서-개선할-부분이-있을지-검토해줘.md"
confidence: high
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/backtest-fill-model-adverse-selection.md"
  - "wiki/analyses/backtest-timeframe-sensitivity.md"
---

# 백테스트 벽시계 오염 — 전략 시각 소스 주입(clock injection) 패턴

전략 코드가 상태 기록·시간 비교에 `datetime.now()`(벽시계)를 쓰면, 같은 코드가 백테스트에서 실행될 때 **시뮬레이션 시간이 아닌 실행 시각**으로 판정해 전략이 통째로 왜곡된다. ht_trading 에서 이 오염 하나가 백테스트를 "다른 전략"으로 만들고 있었다 (2026-07-04, commit c0ebd22).

## 증상 → 근본 원인

- **증상**: 백테스트에서 2·3차 분할 매수와 재진입이 전혀 일어나지 않음.
- **근본 원인**: `ScoringStrategy` 가 분할 체결 시각·전량매도 시각을 `datetime.now()` 로 기록하는데 이 경로가 백테스트에서도 실행된다. 백테스트는 수초 만에 끝나므로 elapsed ≈ 0 → `min_split_interval_minutes`(1080분)·재진입 쿨다운(1080분)에 **항상 걸려 영구 차단**. 결과적으로 백테스트가 실제 3분할 전략을 **1/3 사이즈·재진입 없는 전략**으로 시뮬레이션.
- **왜곡 크기 실측** (오염 제거 후 재측정): 거래 43건 → 249건, 수익 +2.4% → +20%, MDD 6.9% → 21% (3배). 백테스트 기반의 모든 과거 판단이 축소판 전략 기준이었던 셈.

## 해법 — 주입 가능한 시간 소스

```python
class Strategy:
    _clock = None                      # None = 라이브 (벽시계)
    def set_clock(self, clock): ...    # 백테스트가 주입
    def now(self, tz):
        return self._clock() if self._clock else datetime.now(tz)
```

- `BacktestEngine` 이 날짜 루프마다 `set_clock(lambda dt=dt: dt)` 로 **현재 봉 시각을 주입** (같은 반복 내 `on_fill` 까지 동일 시각 유지).
- 라이브는 `_clock=None` → `datetime.now(tz)` 와 동작 동일 (라이브 경로 무변경).

## 오염 지점은 하나가 아니다

같은 벽시계 오염이 별개 위치 2곳에서 추가 발견·수정됨: `infinite_buying` 의 하루 1회 매수 한도 (commit 0aac79a), intraday_replay (commit 1d11174). **한 곳에서 발견되면 `datetime.now` 전수 검색**이 정석.

## 주의 — 의도적 벽시계는 남는다

라이브 전용 시각 가드(`buy_min_hour` 등 장중 시간창)는 의도적으로 벽시계 유지 → 백테스트에서는 해당 가드를 0/무효로 설정해야 한다. "모든 now 를 주입 시각으로" 가 아니라 **상태 기록·경과 비교에 쓰이는 now 만** 주입 대상.

## 일반 원칙

시뮬레이션/리플레이 엔진에서 재사용되는 코드가 시간을 읽는다면, **시간 소스를 주입 가능하게 설계**한다. `datetime.now()` 는 라이브 폴백일 뿐 기본 API 가 아니다. 기존의 점(点) 교훈("백테스트에서 Rule 6 시간손절이 `datetime.now()` 로 즉시 발동", [[ht-trading]] 2026-04-23)을 구조적 해법으로 일반화한 것.

## 변경 이력

- 2026-07-05: 최초 생성 — ht_trading 백테스트 신뢰도 검토에서 분할·재진입 영구 차단 발견, clock injection 으로 수정 (출처: session-logs/20260704-130158-2dad-*)
