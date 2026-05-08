---
title: "알고리즘 트레이딩의 부분 매도 규칙 멱등성 패턴"
domain: both
sensitivity: public
tags: ["analysis", "trading", "algorithm", "idempotency", "state-machine", "sell-rule"]
created: 2026-05-08
updated: 2026-05-08
source_session: 20260507-224943-690c-익절-조건이-됐는데도-익절을-하지-않는-것-같습니다.-오늘자-매매로그를-확인해서-익절조건인.md
related:
  - "wiki/bugs/dict-get-default-no-bootstrap.md"
  - "wiki/projects/ht-trading.md"
---

## 개요

매도 조건이 여러 cycle 지속될 때, **부분 매도 규칙은 발동 추적 플래그가 없으면 누적 발동** 하여 의도치 않게 잔량을 소진한다. 전량 매도는 청산 후 자연 보호되지만 부분 매도는 직접 보호가 필요하다. ht_trading 의 Rule 4 (데드크로스 50% 익절) 가 7 cycle 만에 잔량 1주까지 강제 매도된 사례에서 도출.

## 전량 매도 vs 부분 매도

| 매도 종류 | 다음 cycle 자연 보호 | 보호 필요 |
|---|---|---|
| 전량 매도 (손절·시간손절) | ✅ `pos.qty == 0` 으로 자연 보호 | 불필요 |
| 부분 매도 (단계 익절·데드크로스 50%) | ❌ 잔량 > 0 + 조건 지속 시 다시 발동 | **필요** |

## 안티패턴

```python
# scoring_strategy.py:842 (수정 전)
if (sma5 < sma20) and (profit_pct >= 0.03):
    qty = pos.qty // 2
    return Sell(symbol, qty, reason="데드크로스 익절 50%")
```

조건 지속 → 매 cycle 발동 → 7 cycle 후 잔량 ≤ 1주.

## 3-step 안전 패턴

### 1. once-flag dict (인스턴스 변수)

```python
class ScoringStrategy:
    def __init__(self):
        self._dead_cross_done: dict[str, bool] = {}
        self._profit_take_done: dict[str, int] = {}   # 카운터형 (다단계)
```

부울 / 카운터 / set 중 도메인에 맞춰 선택. 단발 → bool, 다단계 → int 카운터, 발동 시점 기록 필요 → datetime.

### 2. 조건에 가드 추가

```python
if (sma5 < sma20) and (profit_pct >= 0.03) \
   and not self._dead_cross_done.get(symbol, False):
    self._dead_cross_done[symbol] = True
    return Sell(symbol, pos.qty // 2, reason="데드크로스 익절 50% (1회)")
```

### 3. state 영속화 + 청산 시 정리

```python
def get_state(self):
    return {
        ...,
        "dead_cross_done": dict(self._dead_cross_done),
    }

def set_state(self, state):
    ...
    self._dead_cross_done = state.get("dead_cross_done", {})

def on_position_closed(self, symbol):
    self._dead_cross_done.pop(symbol, None)   # 재진입 시 다시 1회 가능
```

- 영속화: 라이브 재시작 / hot-reload / crash 후에도 발동 이력 보존
- 청산 시 정리: 같은 종목에 재진입하면 새 사이클로 다시 1회 발동 가능 (현실적 의도)

## 카운터형 모범 사례 (단계적 익절)

ht_trading 의 Rule 2.5 단계적 익절은 `_profit_take_done[symbol]` 카운터로 정상 동작 — 이 패턴을 부분 매도 규칙 모두에 적용:

```python
done_count = self._profit_take_done.get(symbol, 0)
if done_count >= len(self.profit_take_levels):
    return None   # 모든 단계 발동 완료
target = self.profit_take_levels[done_count]
if profit_pct >= target.activation:
    self._profit_take_done[symbol] = done_count + 1
    return Sell(symbol, int(pos.qty * target.fraction), reason=f"단계 익절 {done_count+1}/{len(...)}")
```

장점: 단계가 늘어도 (1→2→3 단계) 코드 변경 불필요, 카운터 비교만으로 자동 진행.

## 회귀 테스트 패턴

```python
def test_dead_cross_fires_once():
    strat = ScoringStrategy(...)
    # cycle 1: 조건 충족 → 발동
    sell = strat.evaluate_sell(symbol, ctx_with_dead_cross_and_3pct_profit)
    assert sell is not None and sell.qty == pos.qty // 2

    # cycle 2: 같은 조건 → 차단 (잔량 유지)
    sell2 = strat.evaluate_sell(symbol, ctx_with_dead_cross_and_3pct_profit)
    assert sell2 is None

def test_dead_cross_state_persists():
    state = strat.get_state()
    new_strat = ScoringStrategy(...)
    new_strat.set_state(state)
    sell = new_strat.evaluate_sell(symbol, ctx_with_dead_cross_and_3pct_profit)
    assert sell is None   # 새 인스턴스에서도 차단

def test_dead_cross_resets_on_close():
    strat.on_position_closed(symbol)
    sell = strat.evaluate_sell(symbol, ctx_with_dead_cross_and_3pct_profit)
    assert sell is not None   # 재진입 후 다시 1회 가능
```

## Silent state-bug 탐지 (보너스)

이번 사례 (`_peak_prices` 영구 미초기화 — [[dict-get-default-no-bootstrap]]) 와 함께 도출된 진단법:

| 검증 | 명령 |
|---|---|
| state 파일이 비정상적으로 비었는지 | `cat config/.strategy_state.json \| jq '.peak_prices, .dead_cross_done'` |
| 발동되어야 할 이벤트가 0건인지 | `grep -E "트레일링\|데드크로스" logs/*.log* \| wc -l` |
| 단위 테스트가 수동 주입에 의존하는지 | `grep -rE "_peak_prices\[.*\] = " tests/` |

음성 증거 (이벤트 *부재*) 검증은 트레이딩 같이 침묵 결함이 누적 손실로 직결되는 도메인에서 필수.

## 결론

- 부분 매도 규칙은 반드시 once-flag (또는 카운터) + state 영속화 + 청산 시 정리의 3-step 패턴으로 보호
- 카운터형 (`_profit_take_done`) 은 다단계로 확장하기 좋고, 단순 단발은 `dict[str, bool]` 로 충분
- 코드 리뷰 체크리스트에 「**부분 매도 = 멱등성 보장**」을 추가
- state 파일과 운영 로그를 교차 검증하는 외부 lint / monitoring 가 silent failure 방어선

## 관련 페이지

- [[dict-get-default-no-bootstrap]] — 같은 세션의 자매 안티패턴 (peak_prices 영구 미초기화)
- [[ht-trading]] — 본 사례의 프로젝트, 매도 규칙 구조와 commit 957cf8a 적용 결과
