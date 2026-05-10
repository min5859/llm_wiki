---
title: "DCA·트레일링 스톱 튜닝 — 운영 로그 기반 진단·개선 패턴"
domain: personal
sensitivity: public
tags: ["analysis", "trading", "dca", "trailing-stop", "tuning", "infinite-buy"]
created: "2026-05-10"
updated: "2026-05-10"
source_session: "20260510-194933-8c5e-최근-몇일간-로그를-분석해서-수익율을-더-높이기-위한-전략을-추천해-주세요.md"
confidence: high
related:
  - "wiki/projects/upbit-trading.md"
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/scoring-system-ic-validation.md"
---

# DCA·트레일링 스톱 튜닝 — 운영 로그 기반 진단·개선 패턴

DCA (Dollar-Cost Averaging) + Trailing Stop 조합 자동매매에서, **운영 로그를 분석해 수익률을 끌어올리는 5가지 튜닝 레버**와 그 진단 패턴. Upbit 무한매수법, 한국 주식 트레일링 매도 모두 동일 사상이 적용된다.

## 운영 로그 진단의 6 단계

| 단계 | 측정 지표 | 데이터 소스 |
|------|-----------|-------------|
| 1 | 완료 라운드의 평균 수익률 / 보유일 / 매수 횟수 분포 | DB `infinite_buy_rounds` / 실현 손익 테이블 |
| 2 | 청산 사유별 분포 (트레일링 / 단계익절 / 손절 / 시간탈출) | 로그 grep `시그널: SELL` |
| 3 | 진입 평단 vs 청산가 분포 (수익 구간 히스토그램) | 청산 row 의 `profit_percent` |
| 4 | 미청산 라운드의 자본 묶임 시간 / 평단 회복 거리 | DB `status='active'` row |
| 5 | 매수 차단 사유 분포 (cooldown / throttle / 점수미달) | 로그 grep `매수 skip` / `결정: HOLD` |
| 6 | 종목 분산도 (몇 종목에 자본이 집중되는가) | DB `positions` group by ticker |

## 5가지 튜닝 레버

### 1. Trailing Stop 거리

| 너무 타이트 | 너무 느슨 |
|-------------|-----------|
| 6%대 이상 라운드 비율 ↓ | 고점 -10% 까지 보유 |
| 평균 수익률 +3~4% 에서 끊김 | 손실 라운드 폭 ↑ |
| **진단 신호**: 청산 분포가 +3~5% 에 응집 | **진단 신호**: 청산 시점이 고점 후 며칠 지연 |

→ 일반 권장 범위: **2.5~3.0%** (1.5~2.0% 는 너무 타이트). 단, "최소 보장 수익률" (예: 3.0%) 을 함께 두어 손실 청산 방지.

### 2. 매수 쿨다운 (DCA 회전율)

| 너무 길게 (12h+) | 너무 짧게 (1h-) |
|------------------|----------------|
| 라운드 평균 보유 ↑ (자본 회전 ↓) | 평단 개선 효과 ↓ (변동성 노이즈) |
| ETH 24일째 28/40 같은 자본 묶임 | API rate limit 위험 |

→ 일반 권장: **6~12시간**. 시장 변동성·라운드 회전 목표에 맞춰 조정.

### 3. 최대 보유 일수 (`max_round_days`)

평단 회복만 노리고 자본이 무기한 묶이는 패턴 차단. **계단식 손절** 과 짝지어 사용.

```python
# 계단식 손절 (max_round_days=45 정합)
if elapsed_days <= 15: threshold = -10.0
elif elapsed_days <= 30: threshold = -7.0
elif elapsed_days <= 45: threshold = -5.0
else: threshold = None  # 강제 정리
```

> 임계 단축 시 *일관성* 중요. `max_round_days: 90 → 45` 만 바꾸고 계단식 임계를 `30/60/90` 그대로 두면 절반은 무시된다.

### 4. 부분 익절 (Partial Profit-Take)

trailing 만으로 잡지 못하는 4~6% 구간의 *확정익* 을 획득.

```yaml
partial_profit_enabled: True
partial_profit_levels: [(4.0, 0.3), (6.0, 0.3)]
# +4% 도달 시 30% 매도, +6% 도달 시 추가 30% 매도, 나머지 40% 는 trailing 추적
```

→ 일찍 끊긴 3~4% 라운드도 일부 확정익 확보. 강한 상승 추세에서 잔여 분으로 추가 수익도 가능.

> **부분 매도는 멱등성 가드 필수**. 같은 임계가 지속 충족되는 동안 cycle 마다 누적 발동하면 의도 반복 → 잔량 0 까지 강제 청산. [[partial-sell-rule-idempotency]] 참조.

### 5. 약세 시 자동 트레일링 축소 (`tighten_on_weakness`)

데드크로스 + SMA20 하회 등 약세 시그널 시 trailing distance 를 절반으로 축소 → 추세 꺾일 때 수익 보존.

```yaml
tighten_on_weakness_enabled: True
# SMA20 < SMA60 (중기 하락) 시 trailing 거리 ÷ 2
```

조건부 적용이라 paper 모드 1~2일 검증 권장.

## 즉시 적용 vs Paper 검증 분류

| 분류 | 항목 | 이유 |
|------|------|------|
| **즉시** (효과 안전) | trailing 거리, cooldown, max_round_days + 계단식 정합 | 분기 변경 없음, 회귀 테스트로 검증 가능 |
| **Paper 권장** (분기 변경) | partial_profit_enabled, tighten_on_weakness_enabled | 새 분기 동작, paper 1~2일 관측으로 누적 손익·발동 빈도 확인 |
| **중기** (구조 변경) | 종목 풀 확장, 추세 필터 재도입 | 백테스트 + walk-forward 권장 |

## 자본 분산 함정

### 종목 2개로 자본 정지 패턴

`use_dynamic_coin_selection: False` + 메이저 2종 (BTC/ETH) 만 운영 → 한 코인이 보합이면 50% 자본이 평단 회복 대기로 묶임. 신규 라운드 진입 불가.

→ 메이저 1~2개 + dip 후보 동적 선별 (블랙리스트 SOL/ADA/DOGE/XRP 같은 변동성 ↑ 종목 유지) 로 자본 회전 분산.

## 추세 필터의 양면성

- **OFF (기본)**: 하락장 무방비 DCA → 평단 -10% 이상 묶임
- **타이트 ON** (`btc_24h_drop_threshold: -3.0`): 너무 자주 차단되어 매수 기회 ↓
- **완화 ON** (`btc_24h_drop_threshold: -5.0`): 시장 패닉 매도 시에만 차단, 평상시 진입 유지

→ ON/OFF 만으로 결정하지 말고 임계값 완화로 spectrum 조정.

## "라이브 봇 재시작" 안전 체크리스트

config 변경은 봇 init 시점에만 로드된다. 적용을 위한 launchctl 재기동 후 다음을 로그에서 확인:

```
InfiniteBuyStrategy 초기화: 분할=40, 목표수익=5.0%, 라운드예산=3,816,000,
  매수쿨다운=6시간, 추세필터=OFF,
  TrailingStop=ON (거리=2.5%, 최소보장=3.0%),
  MaxRoundDays=45, Tightening=ON,
  PartialProfit=ON [(4.0, 0.3), (6.0, 0.3)]
```

→ 한 줄에 모든 변경 키 (cooldown, trailing, max_round_days, tighten, partial) 가 반영됐는지 *그 자리에서* 확인 가능. 재시작 직후 이 한 줄을 grep 해서 매번 검증.

## 관련 패턴

- [[scoring-system-ic-validation]] — 종목 *진입* 룰의 검증 (이 페이지는 진입 후 *청산* 룰 튜닝)
- [[partial-sell-rule-idempotency]] — 부분 매도 누적 발동 방지의 일반 원칙
- [[utc-iso-date-kst-rollover]] — KST 날짜 새벽 시점 cron 실행과 동일 사상 (시점성 데이터 처리)

## 변경 이력

- 2026-05-10: 최초 작성 (session-logs/20260510-194933-8c5e-*.md). Upbit 무한매수법 5 레버 튜닝 사례를 일반화. ht_trading 의 trailing/단계익절 튜닝과 같은 사상
