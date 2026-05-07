---
title: "Python dict.get(key, default) 가 dict 를 갱신하지 않아 부트스트랩이 영구 실패"
domain: both
sensitivity: public
tags: ["bug", "python", "trailing-stop", "ht-trading", "anti-pattern", "setdefault"]
created: 2026-05-08
updated: 2026-05-08
sources:
  - "session-logs/20260507-224943-690c-*.md"
confidence: high
related:
  - "wiki/projects/ht-trading.md"
---

# Python `dict.get(key, default)` 가 dict 를 갱신하지 않아 부트스트랩이 영구 실패

## 증상

ht_trading 의 `ScoringStrategy` / `TrailingSell` / `MACrossover` 세 전략 모두 트레일링 스톱이 **사실상 영구 비활성** 상태였다. `config/.strategy_state.json` 의 `peak_prices` 가 4월부터 9개 종목을 보유 중인데도 `{}` 로 비어 있었고, 전체 라이브 / 백테스트 로그에 `트레일링스톱` 매도 신호가 단 한 건도 없었다. 4/29 +22.51% 까지 갔다가 5/4 +9.74% 로 하락한 005930, 4/29 피크 +15.71% 에서 +2.47% 까지 폭락한 004000 같은 명백한 트리거 사례가 모두 누락.

## 잘못된 코드

```python
# scoring_strategy.py:798-803, trailing_sell.py:154-160, ma_crossover.py:168-174
ref_price = pos.current_price if pos.current_price > 0 else bar.close
peak = self._peak_prices.get(symbol, ref_price)   # ❌ default 만 반환, dict 미갱신
if ref_price > peak:                               # 항상 False (peak == ref_price)
    self._peak_prices[symbol] = ref_price
    peak = ref_price
```

## 원인

`dict.get(key, default)` 는 **default 를 반환만** 하고 dict 자체를 수정하지 않는다.

1. 첫 cycle: `peak = ref_price` (default fallback), `if ref_price > ref_price` → False → dict 에 키 미생성
2. 다음 cycle: 같은 default fallback 반복 → 영원히 부트스트랩되지 않음
3. 결과: `_peak_prices` 가 영원히 빈 dict, `drop_from_peak` 항상 0%, distance 비교가 영원히 False

## 수정

`get` → `setdefault` 한 줄 교체로 부트스트랩 동작:

```python
peak = self._peak_prices.setdefault(symbol, ref_price)
if ref_price > peak:
    self._peak_prices[symbol] = ref_price
    peak = ref_price
```

`setdefault(key, default)` 는 키가 없으면 **default 로 dict 에 항목을 생성하고** default 를 반환. 첫 호출에서 dict 에 기록되므로 다음 cycle 부터 정상 추적.

## 일반적 안티패턴

다음과 같은 "default 반환 + 비교 후 dict 갱신" 패턴은 항상 의심해야 한다:

```python
# ❌ 안티패턴: dict 부트스트랩이 절대 안 됨
val = some_dict.get(key, initial_value)
if condition_relative_to_val(val):
    some_dict[key] = new_val
```

```python
# ✅ 의도가 명확하면 setdefault
val = some_dict.setdefault(key, initial_value)
if condition_relative_to_val(val):
    some_dict[key] = new_val
```

```python
# ✅ 또는 명시적 분기
if key not in some_dict:
    some_dict[key] = initial_value
val = some_dict[key]
```

## 검증

회귀 테스트 4개 (`tests/strategy/test_peak_prices_bootstrap.py`):

- 첫 호출에 `_peak_prices` 자동 기록 (Scoring / TrailingSell)
- Cycle 1 피크 형성 → Cycle 2 distance 초과 하락 시 트레일링 매도 트리거 (Scoring / TrailingSell)

`config/.strategy_state.json` 의 `"peak_prices": {}` 검사 + 전체 로그의 `트레일링스톱` 매도 0건 확인이 결정적 증거였다.

## 운영 시 주의

- 패치 후 기존 보유 종목들의 `_peak_prices` 는 빈 상태 → 다음 cycle 의 시점 가격이 새 peak 로 부트스트랩.
- 004000 의 4/29 피크 71,700원 같은 historical peak 는 복원되지 않는다 (KIS API 가 일중 고가만 제공).
- 발생한 trailing 매도 누락 손실 (예: 004000 9주 × 약 35,000원) 은 회수 불가.

## 교훈

- Python 의 dict 부트스트랩 패턴은 `setdefault` 가 정공법이다. `get(key, default)` 는 **읽기 전용 lookup with fallback** 으로 한정해 사용한다.
- "테스트가 통과한다 ≠ 코드가 동작한다" 를 보여주는 사례. 기존 단위 테스트 (`tests/strategy/test_trailing_sell_live_price.py:81`) 는 `strategy._peak_prices[SYMBOL] = peak_price` 로 수동 주입했기 때문에 부트스트랩 누락을 잡지 못했다 — **자동 부트스트랩 자체를 검증하는 테스트가 필요**.
- 대규모 경로 (`peak_prices` 가 비어 있는 라이브 state) 는 단위 테스트만으론 안 잡힌다. 운영 state 파일의 비정상값 (`{}`) 자체를 모니터링 / lint 하는 게 효과적.

## 변경 이력

- 2026-05-08: 최초 작성 (세션 로그 20260507-224943-690c, ht_trading commit 60ba3a6)
