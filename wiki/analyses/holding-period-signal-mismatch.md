---
title: "보유기간-신호 미스매치 — 같은 진입신호가 홀딩 호라이즌에 따라 손실↔초과수익으로 뒤집힌다"
domain: "trading"
sensitivity: "public"
tags: ["analysis", "trading", "signal-validation", "holding-period", "exit-rules", "paper-trading"]
created: "2026-07-04"
updated: "2026-07-04"
sources:
  - "session-logs/20260704-093146-5338-현재-프로젝트-분석.md"
confidence: "medium"
related:
  - "wiki/projects/ht-dde.md"
  - "wiki/analyses/signal-overfit-date-dispersion-check.md"
  - "wiki/analyses/backtest-timeframe-sensitivity.md"
  - "wiki/analyses/stock-screening-score-design.md"
---

# 보유기간-신호 미스매치 — 같은 진입신호가 홀딩 호라이즌에 따라 손실↔초과수익으로 뒤집힌다

전략이 잃을 때 흔히 진입 신호를 의심하지만, **신호는 맞는데 보유기간이 틀린 경우**가 있다. [[ht-dde]] 스냅샷 약 12만 건(다중 시점 forward 라벨 `fwd_5m/30m/eod`) 분석의 실측: "오후(12시+) + 현재가>VWAP + 현재가>시가 + 체결강도 100~130" 조건은 **30분 스캘핑 기준으로는 손실**이지만, 같은 진입을 **당일 종가까지 보유하면 10거래일 중 7일 시장 초과수익**(EOD +0.30% vs 시장 -0.37%)이었다. 기존 스캐너의 실패 원인이 신호가 아니라 '보유기간 미스매치'였다는 가설이 성립한다.

## 교훈

1. **홀딩 호라이즌은 신호와 독립된 파라미터다.** 신호 검증 시 단일 보유기간의 수익률만 라벨링하면 이 미스매치를 볼 수 없다 — 스냅샷에 여러 시점(fwd 5m/30m/EOD)의 forward 라벨을 함께 남겨야 신호와 보유기간을 분리해 평가할 수 있다. (봉 간격 쪽의 같은 현상은 [[backtest-timeframe-sensitivity]].)
2. **검증하려는 엣지를 청산 규칙이 잘라내지 않게 하라.** EOD 드리프트 가설을 검증하는 전략에 익절/트레일링을 켜면 검증 대상 엣지를 스스로 절단한다. 실전화한 `afternoon_eod` 전략은 익절·트레일링을 의도적으로 배제하고, 신호와 무관한 재난 손절(-8%, 단일종목 폭락으로 인한 결과 왜곡 방지)만 남겼다.
3. **모멘텀 지표는 밴드로 컷하라.** 체결강도는 높을수록 좋은 게 아니었다 — 150+ 는 과열로 승률 최저, 100~130 밴드가 엣지 구간. 단조증가 가정(≥ 임계) 대신 `between` 이 필요할 수 있다.
4. **가산점의 필수조건화** — 점수식 프레임(rules + min_score)을 유지하면서 AND 필터를 표현하려면, 각 규칙 1점 × N개 + `min_score: N` 으로 선언하면 된다. 엔진 수정 없이 조건 전부 필수인 전략을 점수식에 얹는 기법.

> 파생 필터에 조건을 더 얹어 성과가 급등하면(예: +거래량증가율 300 → +0.6%) 과최적화를 의심 — 성과가 이틀에 몰려 있었음. 판별법은 [[signal-overfit-date-dispersion-check]].

## 관련 맥락

- 발굴(2026-07-01 스냅샷 분석) → 전략 구현·구동(07-02 `afternoon_eod` 추가) → 페이퍼 검증 중. 운영 경과는 [[ht-dde]].

## 변경 이력

- 2026-07-04: 최초 생성 (출처: session-log 20260704-093146-5338. 07-01 발굴 세션에서 재등장해 승격).
