---
title: "ht_trading 절대 손절 elif dead code — 벤치마크 항상 존재 시 -10% 컷 미발동"
domain: "personal"
sensitivity: "public"
tags: ["bug", "ht-trading", "scoring-strategy", "stop-loss", "dead-code", "control-flow"]
created: "2026-05-19"
updated: "2026-05-19"
sources:
  - "session-logs/20260518-233131-6a41-오늘-현대해상의-매매가-계속-거부되었는데-원인을-분석해-주세요.md"
confidence: "high"
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/dca-trailing-stop-tuning.md"
---

# ht_trading 절대 손절 elif dead code

`ScoringStrategy._check_stop_loss` 의 상대 손절 / 절대 손절 분기가 `if … elif` 로 묶여 있어, 벤치마크 (KOSPI 069500) 데이터가 항상 붙어 있는 라이브 환경에서는 절대 손절이 **한 번도 실행되지 않는** 구조. 종목이 -30% 빠져도 KOSPI 가 같이 빠지면 손절 0.

## 핵심 사실

- 위치: `src/ht_trading/strategy/builtin/scoring_strategy.py:817~833`
- 분기 구조:

```python
if bench_bars and entry_bench and entry_bench > 0:
    # 상대 손절만 검사 (지수 대비 -20% AND 종목 -7%)
    if (relative_return <= -self.relative_stop_loss_pct
            and profit_pct <= -self.relative_stop_min_loss_pct):
        return SELL(reason=f"상대손절 …")
elif profit_pct <= -self.absolute_stop_loss_pct:
    # 벤치마크 없을 때만 발동 — 사실상 dead code
    return SELL(reason=f"절대손절 …")
```

- 라이브에서 `bench_bars`(`ctx.bars.get("069500")`) 와 `entry_bench`(`self._entry_benchmark_price[symbol]`) 가 항상 채워지므로 `elif` 분기는 도달 불가
- `absolute_stop_loss_pct: 0.10` 설정값은 실 효과 없음

## 발견 경위

5/18 라이브에서 화신 -19% (5/12 매수), GS -11% (5/15 매수) 가 손절되지 않는 사용자 의문에서 출발.

매도 룰 4종 점검 결과:

| 룰 | 조건 | 화신 (5/18) | GS (5/18) |
|----|------|-------------|-----------|
| 상대 손절 | 지수 대비 -20% AND 종목 -7% | 종목 ✅ / 지수 차이 ❌ | 종목 ❌ |
| **절대 손절 (-10%)** | **벤치마크 없을 때만** (`elif` 구조) | **dead code** | **dead code** |
| 보유기간 손절 | 10거래일 후 수익 ≤ 0% | 7일 → 미도달 | 4일 → 미도달 |
| 트레일링 스톱 | +4% 활성화 후 하락 | 한 번도 +4% 못 찍음 → 미활성 | 동일 |

상대 손절은 V3 튜닝 때 -15% → -20% 로 완화돼 발동 폭이 더 좁아졌고 ([[ht-trading]] A/C/D 튜닝, commit `d0571c5`), 벤치마크 KOSPI 가 같이 하락하는 구간에서는 *차이* 가 -20% 에 미치지 못해 끝까지 미발동.

과거 발동된 SELL 들 (효성 / 삼성E&A / 코스맥스비티아이) 은 **지수가 +7~10% 상승하던 구간에 종목만 -10% 대로 빠져서 *차이* -20%** 가 만들어진 케이스. 지수 동반 하락기에는 상대 손절만으로는 컷이 안 됨.

## 권장 수정

1. **즉시 패치**: `elif` → `if` 로 변경해 상대 손절과 절대 손절을 **병행 검사**. 종목 -10% 면 무조건 컷 (벤치마크 유무 무관)
2. **보조**: `relative_stop_loss_pct` 0.20 → 0.15 환원 또는 `absolute_stop_loss_pct` 0.10 → 0.08 강화 검토

```python
# 권장
if bench_bars and entry_bench and entry_bench > 0:
    if (relative_return <= -self.relative_stop_loss_pct
            and profit_pct <= -self.relative_stop_min_loss_pct):
        return SELL(reason="상대손절 …")
if profit_pct <= -self.absolute_stop_loss_pct:  # elif → if
    return SELL(reason="절대손절 …")
```

## 일반 교훈

- **벤치마크 의존 손절 룰은 fallback 이 아니라 "추가 가드" 로 설계해야 한다.** 벤치마크 데이터가 있을 때만 발동하는 fallback 구조는 *벤치마크가 항상 존재하는 운영 환경에서는 다른 가드를 죽인다.* 손절·익절처럼 **포지션의 절대 손실 한계**를 지키는 룰은 어떤 컨텍스트 데이터 유무와 무관하게 항상 검사돼야 한다.
- **`if … elif` 의 상호 배타 의도**가 실제 운영 데이터 분포와 어긋날 때 dead code 가 된다. 코드 리뷰 시 두 분기가 **상호 배타가 본질**인지 (예: enum tag), 아니면 **fallback 으로 묶인 것**인지 (이 경우) 구분해야 한다. fallback 이라면 분리하거나 병렬 검사로 풀어쓰는 게 안전.
- **상대 손절 임계 완화의 함정** — relative_stop 을 -15% → -20% 로 완화하면 벤치마크 동반 하락기엔 *어떤 손실도 컷 못 함*. 절대 손절을 dead code 로 둔 채 상대 손절만 완화하면 손절 자체가 사실상 꺼진 상태로 운영된다.

## 관련 맥락

- [[ht-trading]] — V3 튜닝의 D 항목 (`relative_stop_loss_pct: 0.15 → 0.20`) 과 결합되어 효과가 증폭
- [[dca-trailing-stop-tuning]] — 트레일링 스톱 튜닝 일반 논의
- [[partial-sell-rule-idempotency]] — 매도 룰 동작 idempotency 관련 패턴

## 변경 이력

- 2026-05-19: 최초 작성 — 화신/GS 손절 미발동 분석 중 발견 (출처: session-logs/20260518-233131-6a41-*)
