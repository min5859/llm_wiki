---
title: "ht_trading — 프로젝트 설계 상세"
domain: "personal"
sensitivity: "public"
tags: ["project", "trading", "scoring", "algorithm", "config"]
created: "2026-04-23"
updated: "2026-05-14"
sources:
  - "session-logs/20260422-230939-22f1-스코어링-점수를-65-점에서-60-점으로-조정했는지-확인해-주세요.md"
  - "session-logs/20260423-120308-f269-오늘-거래중에서-삼성전자-매수-시그널이-발생한뒤-3분할-매수중-1회만-매수하고-나머지-매수.md"
  - "session-logs/20260423-193125-b999-graphify.md"
  - "session-logs/20260426-111623-cfe2-4월-24일-매매시-몇가지-종목들이-리그크-거부로-인해서-매수거부가-발생했습니다.-왜-그런.md"
  - "session-logs/20260428-235122-215d-오늘-매매-로그를-분석해서-개선사항을-도출해-주세요.md"
  - "session-logs/20260506-230405-a179-오늘-매수가능-금액이-있었는데도-매수가-리스크에-걸려-매수-되지-않았습니다.-제가-확인해.md"
  - "session-logs/20260507-224943-690c-익절-조건이-됐는데도-익절을-하지-않는-것-같습니다.-오늘자-매매로그를-확인해서-익절조건인.md"
  - "session-logs/20260510-195349-94ba-최근-몇일간-로글-분석해서-수익을-더-올릴수-있는-방안을-제안해-주세요.md"
  - "session-logs/20260511-230648-4621-종목-스코어링-산정-알고리즘을-수정한-후에-오늘-점수가-너무-낮게-나와서-종목-선정이-안.md"
  - "session-logs/20260514-175837-5657-수익율을-더-개선할-방안을-찾으려고-합니다.md"
confidence: "high"
related:
  - "wiki/bugs/kis-cash-d2-settlement-buy-rejection.md"
  - "wiki/bugs/dict-get-default-no-bootstrap.md"
  - "wiki/analyses/kis-balance-api-fields.md"
  - "wiki/analyses/partial-sell-rule-idempotency.md"
  - "wiki/analyses/scoring-system-ic-validation.md"
  - "wiki/analyses/scoring-version-comparison-methodology.md"
  - "wiki/analyses/dca-trailing-stop-tuning.md"
  - "wiki/analyses/polling-interval-vs-bar-interval.md"
---

# ht_trading — 알고리즘 트레이딩 프로젝트

Python 기반 개인 알고리즘 트레이딩 시스템. `ScoringStrategy`를 중심으로 매수 시그널을 점수화해 종목을 선별한다.

## 스코어링 시스템 구조

### 이중 점수 스케일

| 컨텍스트 | 스케일 | 파라미터명 | 현재 값 |
|---------|--------|-----------|---------|
| 백테스트 | 40점 만점 | `buy_min_score` | 25 |
| 라이브 screener | 100점 만점 | `buy_min_score_full` | **48** (2026-05-11 V3 분포 캘리브레이션; 직전 62) |

- 백테스트 전용 스케일(40점)과 라이브 screener 스케일(100점)이 별도로 운영된다.
- 라이브 기준 62점 = "어느 정도 검증된 시그널만 통과" (이전 60에서 상향)

### 설정 파일 동기화 규칙

라이브 screener 임계값은 **두 파일에 동시에 설정**되어 있어 반드시 함께 수정해야 한다.

```
config/strategies/scoring.yaml   → buy_min_score_full: 62
config/trading.yaml              → strategies.screener.min_score: 62
```

> 주의: 한 파일만 수정하면 설정 불일치가 발생한다. 변경 시 두 파일을 항상 같이 수정할 것.

### 기타 screener 파라미터 (`config/trading.yaml`)

```yaml
screener:
  min_score: 60          # 100점 만점 기준 매수 커트라인
  max_per_source: 10     # 거래량/상승률/거래대금 각 소스에서 최대 종목 수
  cache_ttl: 3600        # 캐시 TTL (1시간)
```

## 핵심 클래스

- `ht_trading.strategy.builtin.scoring_strategy.ScoringStrategy` — 점수 기반 매수 전략

## 코드베이스 구조 (graphify 분석, 2026-04-23)

82개 파일, ~57,088 단어. 주요 god node(연결 수 기준):

| 노드 | 엣지 수 | 역할 |
|------|--------|------|
| `Bar` | 176 | 봉 데이터 — 모든 전략·지표의 입력 |
| `Market` | 174 | 9개 커뮤니티를 잇는 시스템 허브 |
| `Position` | 128 | 포지션 상태 |
| `StrategyContext` | 122 | 전략 실행 컨텍스트 |
| `Strategy` | 93 | 전략 ABC (BacktestEngine과 ScoringStrategy의 연결 지점) |
| `LiveEngine` | 75 | KIS API + RiskManager + CycleSnapshot 연결 |
| `RiskManager` | 71 | 매수 신호 검증 게이트 |

주요 커뮤니티: Strategy Indicators(141), Backtest Engine(137), KIS API Auth(117), Data Feed(94), Live Engine Core(88), Risk Manager(25), State Store(24), Stock Screener(고립, degree 8).

## 분할 매수 Throttle (G4-1)

2차·3차 분할 매수를 제어하는 가드. 조건 두 가지를 **AND**로 충족해야 추가 매수 허용:
1. **시간 조건** — 직전 분할 이후 충분한 시간 경과
2. **드로다운 조건** — 직전 분할 체결가 대비 현재가 하락 ≥ `min_split_drawdown_pct` (기본 1.5%)

관련 파라미터:

```yaml
# config/strategies/scoring.yaml
enable_split_throttle: true
min_split_drawdown_pct: 0.015  # 1.5%
```

### 버그 수정 (2026-04-23, commit c5dc818)

**증상**: 1차 매수 후 하락 중인데도 2차 분할 매수가 진행되지 않음. 로그에 "split throttle 드로다운 부족: 0.00% < 1.50%" 반복.

**원인**: `_last_split_price`에 `last_bar.close`(일봉 캐시 종가)를 저장했고, 드로다운 비교도 `bar.close`(같은 일봉 캐시)로 수행 → 일봉 캐시는 장중 변하지 않아 드로다운 항상 0%.

**수정 내용**:
| 수정 위치 | 변경 전 | 변경 후 |
|----------|---------|---------|
| `_can_add_split` | `bar.close` 기준 비교 | `pos.current_price`(브로커 실시간 평가가) 기준 비교 |
| `_record_split_event` (라이브 경로) | `last_bar.close` 저장 | `pb.limit_price`(지정가) 저장 |

## 버그 수정 이력

### 버그 수정 (2026-04-23, commit 803a158): Rule 6 datetime.now() 백테스트 오작동

**증상**: 백테스트 시 모든 포지션에서 Rule 6(시간 기반 손절)이 즉시 발동.

**원인**: `scoring_strategy.py:849`에서 `datetime.now()`를 사용해 현재 실행 시각과 비교. 2024년 봉 데이터를 돌리면 보유 기간이 수백 일로 계산되어 손실 포지션을 즉시 손절.

**수정**: `bar.dt` 기준으로 변경.

```python
# 전: days_held = (dt.now() - dt.strptime(entry_date, "%Y-%m-%d")).days
# 후: days_held = (bar.dt - dt.strptime(entry_date, "%Y-%m-%d")).days
```

> **패턴**: 백테스트에서는 절대로 `datetime.now()`를 쓰지 말 것. 항상 `bar.dt`(봉 시각)를 현재 시각으로 사용해야 한다.

---

### 버그 수정 (2026-04-23, commit 803a158): RSI 평탄 구간 100 반환

**증상**: 가격 변동이 없는 봉 구간에서 RSI=100으로 계산되어 Rule 5(과매수 익절)가 오발동.

**원인**: `indicators.py:54`에서 `avg_loss == 0`인 경우 항상 100.0 반환. 실제로 `avg_gain`, `avg_loss` 모두 0이면(가격 변화 없음) RSI는 50(중립)이어야 한다.

**수정**:
```python
# 전: if avg_loss == 0: return 100.0
# 후: if avg_loss == 0: return 100.0 if avg_gain > 0 else 50.0
```

---

### 버그 수정 (2026-04-23, commit f376ba8): TrailingSell 트레일링 스톱 기준 불일치

**증상**: 라이브에서 트레일링 스톱이 사실상 동작하지 않음 (하락률 항상 0%).

**원인**: `trailing_sell.py:154`에서 고점 추적과 현재가 비교에 `bar.close`(일봉 캐시 종가)를 사용. `bar.close`는 하루 종일 고정값이라 고점 = 현재가 → 하락률 0%.

`ScoringStrategy`는 이미 `c5dc818`에서 `pos.current_price`(KIS 실시간 평가가)로 수정됐는데 `TrailingSell`은 누락.

**수정**: `bar.close` → `pos.current_price` (고점 갱신 + 하락률 계산 모두).

```python
# 전: peak = self._peak_prices.get(symbol, bar.close)
# 후: ref = pos.current_price if pos.current_price > 0 else bar.close
#     peak = self._peak_prices.get(symbol, ref)
```

---

### 버그 수정 (2026-05-07, commit 60ba3a6): 트레일링 스톱 `_peak_prices` 영구 미초기화

**증상**: 1개월간 라이브에서 `Rule 2` (트레일링 스톱) 가 단 한 번도 발동하지 않음. `config/.strategy_state.json` 의 `peak_prices` 가 보유 9종목임에도 빈 dict (`{}`) 로 잔존. 005930 (4/29 +22.51% → 5/4 +9.74%), 004000 (4/29 +15.71% → 5/4 +2.47%) 등 명백한 트리거 사례 모두 누락. 정상 동작이라면 5/4 에 약 +8.7% 확정 매도되었을 잔량 (004000 9주 ≈ 35,000원) 이 미실현 손실로 회수 불가.

**원인**: `dict.get(key, default)` 는 default 를 *반환* 만 하고 dict 를 *갱신하지 않음*. 첫 cycle 에서 `peak == ref_price` 가 되어 `if ref_price > peak` 가 영원히 False → `_peak_prices` 에 항목이 절대 생성되지 않음. 영향 범위: `scoring_strategy.py:802` (Rule 2), `trailing_sell.py:158` (Rule 2), `ma_crossover.py:172` 모두 동일 패턴.

**수정**: `get` → `setdefault` 한 줄 교체.

```python
# 전: peak = self._peak_prices.get(symbol, ref_price)   # dict 미갱신
# 후: peak = self._peak_prices.setdefault(symbol, ref_price)
```

**회귀 테스트**: `tests/strategy/test_peak_prices_bootstrap.py` 4 cases (Scoring/TrailingSell × 부트스트랩/트리거).

**운영 주의**: 패치 후에도 기존 보유 종목의 historical peak 는 복원 불가 (KIS 가 일중 고가만 제공). 다음 cycle 가격이 새 peak 로 부트스트랩됨.

→ 자세한 분석: [[dict-get-default-no-bootstrap]]

---

### 버그 수정 (2026-05-07, commit 957cf8a): `on_order_submitted` 의 `PendingBuy.limit_price` 유실

**증상**: 명시적 에러 없이 G4-2 no-chase 정책이 무력화. `_check_pending_buy` 타임아웃 분기에서 `if pb.limit_price > 0` 가 False 가 되어 `_last_failed_limit` 에 기록되지 않음 → 재주문이 더 높은 현재가를 추격할 수 있음.

**원인**: `scoring_strategy.py:443` `on_order_submitted` 에서 `PendingBuy` dataclass 를 새 인스턴스로 재생성할 때 `limit_price` 인수를 빠뜨려 default `0.0` 로 덮어씌워짐.

**수정**: `PendingBuy(..., limit_price=pb.limit_price)` 명시. 권장: 향후 부분 갱신은 `dataclasses.replace(pb, order_id=order_id)` 로 전환 (잊은 필드는 기존 값 유지). **dataclass 부분 갱신 안티패턴** — 새 인스턴스 인수 직접 나열은 차후 필드 추가 시 깨지기 쉽다.

---

### 버그 수정 (2026-05-07, commit 957cf8a): Rule 4 데드크로스 익절이 매 cycle 반복 발동

**증상**: SMA5<SMA20 + 수익≥3% 가 지속되면 매 cycle 50% 씩 누적 매도 → 7 cycle 만에 잔량 1주까지 강제 소진. docstring "50% 매도" 의 단발 의도와 불일치. 수수료/슬리피지 누적 손실.

**원인**: 부분 매도 규칙에 발동 추적 dict 가 없음. 전량 매도 (Rule 1 손절, Rule 6 시간손절) 는 청산 후 `pos.qty == 0` 으로 자연 보호되지만, 부분 매도는 직접 보호 필요. (`_profit_take_done` 카운터를 쓰는 Rule 2.5 단계적 익절은 정상 동작 — 모범 사례).

**수정** (`scoring_strategy.py`):
- `_dead_cross_done: dict[str, bool]` 인스턴스 변수 신설 (line 129)
- Rule 4 조건에 `not self._dead_cross_done.get(symbol, False)` 가드 추가 (line 854)
- 발동 시 `self._dead_cross_done[symbol] = True` 설정 (line 857)
- `get_state` / `set_state` 양쪽에 영속화 추가 (hot-reload·crash 대비)
- 포지션 청산 시 `self._dead_cross_done.pop(symbol, None)` 정리 (line 301)

**회귀 테스트**: `tests/strategy/test_scoring_misc_bugs.py` 4 cases (limit_price 보존 1 + dead_cross 일회 발동 3).

→ 일반 패턴: [[partial-sell-rule-idempotency]]

---

### 버그 수정 (2026-05-06, commit c6109f4): KIS 매수가능 현금에 D+2 미정산분 누락

**증상**: 매도 직후 매수 사이클에서 RiskManager 가 "현금 부족" 으로 매수 차단. KIS HTS 의 매수가능금액은 충분.

**원인**: `src/ht_trading/api/domestic.py:get_balance()` 의 `cash` 키에 `dnca_tot_amt` (예수금총액 = D+0 출금가능) 만 매핑. 한국은 D+2 결제 사이클이지만 매도 직후 그 금액으로 매수가 가능 — `prvs_rcdl_excc_amt` (가수도정산금액, D+2) 가 매도 미정산 포함 매수가능 현금이다.

**수정**:
```python
deposit = int(summary.get("dnca_tot_amt", "0"))
settled_d2 = int(summary.get("prvs_rcdl_excc_amt", "0"))
cash = max(deposit, settled_d2)   # 모의투자에서 D+2=0 fallback
return {"cash": cash, "deposit": deposit, ...}
```

신규 회귀 테스트 3건 (`tests/api/test_domestic_balance.py`): D+2 사용 / D+2=0 fallback / 동일값. 다운스트림 (`KISBroker.get_cash` → `_cached_cash` → `ctx.cash` → `RiskManager._validate_buy`) 은 동일한 `cash` 키라 변경 불필요.

→ 자세한 분석: [[kis-cash-d2-settlement-buy-rejection]] / [[kis-balance-api-fields]]

---

### 버그 수정 (2026-04-23, commit c5dc818): split throttle 드로다운 0% 버그

**증상**: 1차 매수 후 하락 중인데도 2차 분할 매수가 진행되지 않음. "split throttle 드로다운 부족: 0.00% < 1.50%"가 계속 출력.

**원인**: `_can_add_split`에서 `bar.close`(일봉 캐시, 장중 고정값)를 사용. 드로다운이 항상 0%.

**수정**: `pos.current_price`(브로커 실시간 평가가) 기준으로 변경.

## 동적 트레일링 스톱 (trailing_tiers)

### 배경

기존 Rule 2 (고정 trailing)의 문제: 수익 3%+에서 활성화되지만 distance가 고정 3%라 15% 상승한 주식도 3% 조정만 발생하면 매도됨. 강한 상승 추세에서 조기 매도가 잦음.

### 설계

수익률이 높아질수록 trailing distance를 확대해 큰 추세를 끝까지 추적:

```yaml
# scoring.yaml
trailing_tiers:
  - {activation: 0.03, distance: 0.03}   # 수익 3~9%: 3% 거리 (소폭 이익 보호)
  - {activation: 0.10, distance: 0.06}   # 수익 10~19%: 6% 거리 (조정 허용)
  - {activation: 0.20, distance: 0.10}   # 수익 20%+: 10% 거리 (큰 추세 추적)
```

수익률 구간별 동작:
```
수익 2~9%  → trailing distance 3%
수익 10~19% → trailing distance 6%
수익 20%+  → trailing distance 10%
```

### 변경 사항 (2026-04-28 commit: feat: 동적 트레일링 스톱 추가)

- `scoring_strategy.py`: `_get_trailing_distance(profit_pct)` 메서드 추가 (38 insertions, 12 deletions)
- `config/strategies/scoring.yaml`: `trailing_tiers` 추가, `profit_take_levels: []`로 10% 고정 익절 비활성화

> 10% 고정 익절(`profit_take_levels`)을 비활성화하고 `trailing_tiers`가 대체. 상승 추세에서 1/3씩 끊어 파는 대신 트레일링으로 전량 추적.

## 설계 변경 이력

| 날짜 | 변경 내용 | 이유 |
|------|---------|------|
| 2026-04-22 | `buy_min_score_full` 65 → 60 | 매수 기회 확대 (임계값 완화) |
| 2026-04-23 | split throttle 드로다운 버그 수정 (c5dc818) | 일봉 캐시 종가 대신 실시간 평가가 사용 |
| 2026-04-23 | Rule 6 백테스트 datetime.now() 버그 수정 (803a158) | bar.dt 기준으로 변경 |
| 2026-04-23 | RSI 평탄 구간 100→50 버그 수정 (803a158) | avg_gain=0, avg_loss=0 시 중립값 반환 |
| 2026-04-23 | TrailingSell 트레일링 스톱 기준 불일치 수정 (f376ba8) | bar.close → pos.current_price |
| 2026-04-28 | trailing_tiers 동적 트레일링 스톱 추가, profit_take_levels 비활성화 | 상승 추세 조기 매도 방지 |
| 2026-05-07 | `_peak_prices` setdefault 부트스트랩 수정 (60ba3a6) | `dict.get` 가 dict 갱신 안 해 1개월간 트레일링 영구 비활성화 |
| 2026-05-07 | `on_order_submitted` `limit_price` 유실 수정 + Rule 4 once-flag 추가 (957cf8a) | dataclass 재생성 시 인수 누락, 부분 매도 규칙 멱등성 |

## 투자 파라미터

```yaml
buy_invest_pct: 0.20    # 1종목 최대 투자 비중 (20%)
buy_split_count: 3      # 분할 매수 횟수 (3분할)
lookback_period: 60     # 최소 봉 수 / MA·신고가 기간
```

## 개선 백로그 (B 시리즈)

graphify 코드베이스 분석(2026-04-23)에서 발굴. `docs/BACKLOG.md`에도 기록됨.

| ID | 파일 | 라인 | 내용 | 영향도 |
|----|------|------|------|--------|
| **B1** | `scoring_strategy.py` | `_calc_qty` | 분할 수량이 현금 잔액 기준으로 계산되어 2차·3차로 갈수록 수량이 줄어드는 구조적 문제. 의도(총 투자금의 1/N씩)와 구현이 다름 | 중 |
| **B2** | `risk/manager.py` | 31 | `max_order_value = 5_000_000` 하드코딩. equity ≥ 2,500만원이면 매수를 막기 시작 | 중 |
| **B3** | `scoring_strategy.py` | 799~831 | Rule 4(데드크로스, ≥3% 익절 50%) vs Rule 2.5(단계적 익절, ≥10% 1/3 매도) 우선순위 충돌. Rule 2.5가 먼저 적용되어 Rule 4는 3~10% 구간에서만 동작 | 검토 필요 |
| **B4** | `scoring_strategy.py` | 711~720 | `_calc_buy_score` 거래량 점수 계단식 불연속 (vol_r=4.9 vs 5.0이 2점 차이). 과적합 위험 | 낮음 |

## 리스크 매니저 정책 (`risk/manager.py` + `config/trading.yaml`)

### 설정값

| 항목 | 설정값 |
|------|--------|
| `max_positions` | 5종목 (동시 보유 최대) |
| `max_position_pct` | 30% (종목당 최대 자산 비율) |
| `daily_loss_limit_pct` | 3% (일일 최대 손실 비율) |
| `max_order_value` | 200만원 (단일 주문 최대 금액) |

### 매수 검증 순서 (`_validate_buy`, 조건 5가지)

1. 최대 포지션 수 — `len(positions) >= 5`이고 신규 종목이면 → 거부
2. 단일 주문 금액 — 예상금액 > 200만원 → 거부
3. 단일 주문 비율 — 예상금액 > 총자산의 30% → 거부
4. 누적 포지션 비율 — 기존 보유분 + 신규 주문 > 총자산의 30% → 거부
5. 현금 확인 — 주문금액 > 보유현금 → 거부

> **매도 검증**: 매도는 수량 부족 시 보유 수량으로 조정만 할 뿐 한도 초과로 차단하지 않는다.

### 2026-04-24 리스크 거부 사례 분석

**원인**: `max_positions: 5`가 장 내내 가득 차서 HD현대일렉트릭(61점), 수산인더스트리(70점) 매수 거부.

**타임라인**:
- 09:30 장 시작, 4종목 보유
- 09:33 롯데정밀화학+OCI홀딩스 매수 주문(미체결 포함 포지션 5개)
- 10:33~ HD현대일렉트릭 매 10분마다 `최대 포지션 수 초과: 5/5`로 거부
- 14:33~ 수산인더스트리도 동일 이유로 거부

**장 내내 보유된 5개 포지션**: 대한제분, 롯데정밀화학, 삼성전자, 벽산, OCI홀딩스

**구조적 문제**: 포지션 교체(rotation) 로직이 없다. 새 시그널 스코어가 기존 최저점 포지션보다 높아도 진입 불가.

**개선 방향**:
1. `max_positions` 상향 조정 (단순하지만 리스크도 증가)
2. 새 시그널 스코어가 기존 최저점 포지션 대비 높으면 교체하는 rotation 로직 도입

> 총자산 참고값 (2026-04-24): 6,350,019원 (평가 2,405,275 + 예수금 3,944,744)

## 점수 알고리즘 V3 (2026-05-10, IC 검증 기반 재설계)

`n_stock_info` 패키지의 점수 함수 (`src/n_stock_info/analyzers/{technical,fundamental}.py`) 를 IC 검증 결과 기반으로 전면 재설계. 별도 git 저장소이므로 별도 커밋 (commit `0088d85`).

### 검증 (commit `b507f2a`, `scripts/validate_score_ic.py` +588 lines)

KIS 일봉 25종목 × 2년 (2362 표본) 으로 forward 5/10/20일 return 과의 Pearson/Spearman IC 측정. 결과는 [[scoring-system-ic-validation]] 의 표본 케이스로 기록. 핵심 발견:

- **양봉 (candle, ≥2% body) IC +0.0387** (최강 알파)
- **거래량 비율 (sweet 2~4배) IC +0.0287** (두 번째)
- **MA 정렬 IC -0.0031** (사실상 0)
- **단기 모멘텀 5일 IC -0.0383** (음수, mean reversion 압력)
- **RSI 안정구간 45~65 IC -0.0201** (음수)
- 단일 룰 OR `강양봉 AND 거래량 sweet`: 승률 67.2%, 평균 +3.38% (n=64)

### V3 가중치 재배치 (총점 100점 만점 그대로)

| 영역 | 변경 전 | V3 |
|------|---------|-----|
| **기술 40 → 50점** | candle 5 / vol 10 / high 10 / ma 15 | candle **10** / vol **14** / high 12 / ma **8** / **ATR 6** (신규) |
| **펀더 40 → 30점** | PER 10 / ROE 10 / **EPS 절대값** 10 / 안정성 10 | PER 8 / ROE 8 / **earnings_yield** 10 / 안정성 4 |
| 리서치 20점 | (변경 없음) | 리포트 10 / 조회수 10 |

핵심 변경 의의:

- **EPS 절대값 폐기 → earnings yield**: EPS=20,000 정체 대형주가 만점, EPS=300 고성장 소형주는 4점 → 사실상 "고가/대형주 보너스" 결함. `_score_earnings_yield(EPS/Price)` 로 교체
- **양봉·거래량 가중 강화** + **MA 페이드** + **ATR 정상구간 신규 6점** (광기/정체 회피)
- **펀더 40 → 30점**: 5/8 로그에서 모든 후보가 펀더 11~25 사이에 응집 = 변별력 0. 단기 4~10일 보유에 분기 데이터 비중 과다

회귀: `n_stock_info` 134/134 통과. `min_score: 62` 는 점수 분포 변동으로 V3 적용 후 1~2주 라이브 모니터링 후 재캘리브레이션 검토.

### V3 적용 후 컷오프 캘리브레이션 (2026-05-11, commit `50c929c`)

V3 적용 첫 날 (5/10) 부터 컷오프 62 통과 종목이 0건이 되어 라이브 매수 중단. 점수 분포 변화:

| 날짜 | n | 평균 | 최대 | ≥62 통과 |
|------|---|------|------|---------|
| 5/04~5/08 (V2) | ~100~133/일 | 42~48 | 65~70 | 6~21건/일 |
| **5/10 (V3 첫날)** | 40 | **29.75** | 52 | **0** |
| **5/11 (V3)** | 104 | **28.51** | **51** | **0** |

평균 −17점, 최대 −15~18점. 매수 정지의 직접 원인.

**V2 ↔ V3 비교 (30 매칭 종목)**:
- Spearman ρ = **+0.835** (총점), Pearson r = +0.897
- Top-10 픽 교집합: **8/10 (80%)**
- 큰 순위 이동: SK하이닉스 21위→**5위** (대형주 earnings_yield 보정), 대한해운 22위→9위, 가온전선 10위→20위 (MA 가중치 감점)

해석: V3 는 **평행이동에 가깝지만 일부 reordering 존재**. 컷오프 단순 하향만 해도 매수 후보의 80% 는 같지만, SK하이닉스 / 대한해운 같이 V3 효과로 신규 진입한 종목들이 V3 의 진짜 효과. 비교 방법론 일반화는 [[scoring-version-comparison-methodology]] 로 분리.

**임시 캘리브레이션** (`commit 50c929c`):
- `config/strategies/scoring.yaml`: `buy_min_score_full: 62 → 48`
- `config/trading.yaml`: `min_score: 62 → 48`

V3 분포 기준 ≥48 통과 종목 = 일평균 5~10건 (과거 ≥62 통과 빈도 6~21건/일 과 유사 모집단).

**중요**: forward return 으로 검증된 컷오프가 아닌 임시 캘리브레이션. **2~4주 라이브 데이터 누적 후 V3 점수 vs 실제 수익률 평가 필요**. 백테스트로 V2/V3 비교가 사실상 불가능한 이유 (40점/100점 이중 점수 스케일 + 펀더/리서치 과거 시점 재현 불가) 는 [[scoring-version-comparison-methodology]] 참조.

### `scripts/analyze_live_period.py` (commit `9d69502`, +407 lines)

기간 지정 라이브 로그 분석. V3 적용 효과 검증용.

```bash
# 기본
python scripts/analyze_live_period.py --from 2026-05-11 --to 2026-05-15

# 비교 (V3 전후)
python scripts/analyze_live_period.py --from 2026-05-11 --to 2026-05-15 \
  --compare-with 2026-05-04:2026-05-08

# 최근 N거래일
python scripts/analyze_live_period.py --recent 5
```

출력: 점수 분포 (히스토그램 + cutoff 통과율) / BUY·SELL 시그널 + 지정가 체결률 / 매도 사유별 (단계익절/트레일링/상대손절/보유기간) 손익 분류 / 매수 skip 사유 / 일별 보유 추이 / 두 기간 대조표.

V3 적용 효과 검증 핵심 지표: 점수 평균 차이 / cutoff 통과율 (현 10.8%) / 상대손절 합계 감소 (현 -131,830원) / 승률 변화.

## A/C/D 튜닝 (2026-05-10, commit `d0571c5`)

5/4-5/8 라이브 로그 분석에서 도출된 개선안 7건 (A~G) 중 즉시 적용 3건. 미적용 4건은 `tasks/backlog.md`.

| 항목 | 변경 (`config/strategies/scoring.yaml`) | 근거 |
|------|------------------------------------------|------|
| **A** 체결률 개선 | `limit_price_ratio: 1.0 → 1.005` (현재가 +0.5% 상한) | 지정가 체결률 36% (18체결 / 50건) → 70%+ 기대. 진입가 +0.5% 상승은 단계익절 +10% 대비 미미 |
| **C** 트레일링·단계익절 둔감화 | `trailing_activation_pct: 0.03 → 0.05`, 첫 tier `{0.03,0.03} → {0.05,0.04}`, 18% 1/2 익절 단계 추가 | 5/8 산일전기/DN오토모티브 +9% 고점 직후 -3.5% 빠져 +4% 청산 패턴 차단. *수익 절반 손실* 방지 |
| **D** 상대손절 완화 | `relative_stop_loss_pct: 0.15 → 0.20`, `relative_stop_min_loss_pct: 0.05 → 0.07` | 5/6 KG케미칼/문배철강/명신산업 일괄청산 -103,165원 사고 회피 |

### Backlog (B/E/F/G, `tasks/backlog.md`)

| ID | 항목 | 비고 |
|----|------|------|
| **B** | Split throttle 완화 (`min_split_interval_minutes: 60→30`, `max_split_drawdown_pct: 0.05→0.07`) | 5/4 산일전기 5.5% 드로다운에서 12회 연속 차단 |
| **E** | 보유기간손절 임계 상향 (`stale_holding_days: 10→15` + `profit_pct < -0.02` 조건) | 5/8 롯데정밀화학 -0.3% 본전 청산 회피 |
| **F** | 보유 종목 보유분할 cut 25→22 (별도 파라미터화) | 매수 시그널 268건 중 90% 가 보유 7종목 집중 → 분할 진행/신규 슬롯 확보 |
| **G** | 골든시그널 OR 채널 (`강양봉 ≥2% AND 거래량 2~4배` 동시 시 점수 무관 후보) | IC 검증 단일 룰 승률 67.2%, 평균 +3.38%. V3 라이브 결과 양호 시 후속 적용 |

### 적용 후 launchd 재시작 검증

```
2026-05-10 23:02:39  ScoringStrategy 초기화: buy_min_score=25, relative_stop=20.0%,
  trailing활성=5.0% tiers=[5%→4% 12%→6% 22%→10%], profit_take=2단계
```

이전 버전 (`relative_stop=15.0%, trailing활성=3.0%, profit_take=1단계`) 과 한 줄 비교로 5개 변경 (B,A,C,E,D) 반영 확인 가능. 다음 개장 (5/11) 부터 효과.

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260422-230939-22f1 에서 추출)
- 2026-04-23: split throttle G4-1 섹션 추가, 드로다운 버그 수정 기록 (세션 로그 20260423-120308-f269)
- 2026-04-23: graphify 아키텍처 분석, 버그 수정 3건, 백로그 B1-B4 추가 (세션 로그 20260423-193125-b999)
- 2026-04-26: 리스크 매니저 정책 + 4/24 리스크 거부 사례 분석 추가 (세션 로그 20260426-111623-cfe2)
- 2026-05-07: KIS 매수가능 현금 D+2 정산 누락 버그 수정 추가 (commit c6109f4, 세션 로그 20260506-230405-a179). 매수 커트라인 60 → 62 (commit faa0518)
- 2026-05-08: ScoringStrategy 매도 규칙 3건 수정 추가 — (1) `_peak_prices` setdefault 부트스트랩 (commit 60ba3a6) — `dict.get` 가 dict 미갱신으로 1개월간 트레일링 영구 비활성, 005930·004000 등 명백한 트리거 누락. (2) `on_order_submitted` `PendingBuy.limit_price` 유실 → G4-2 no-chase 정책 무력화 (commit 957cf8a). (3) Rule 4 데드크로스 익절 once-flag 추가 — 부분 매도 규칙이 조건 지속 시 누적 발동하던 문제 (commit 957cf8a). 신규 회귀 테스트 2 파일 8 cases. 일반 패턴은 [[dict-get-default-no-bootstrap]] / [[partial-sell-rule-idempotency]] 로 분리 (출처: session-logs/20260507-224943-690c-*)
- 2026-05-10: 점수 알고리즘 V3 (IC 검증 기반 재설계) 섹션 신설. `n_stock_info` 별도 저장소 commit `0088d85` (technical 40→50 / fundamental 40→30 / EPS 절대값 → earnings_yield / ATR 신규). 검증 스크립트 `scripts/validate_score_ic.py` (commit `b507f2a`, +588) + 라이브 분석 스크립트 `scripts/analyze_live_period.py` (commit `9d69502`, +407). A/C/D 튜닝 적용 (commit `d0571c5`) — 체결률 (limit_price_ratio 1.0→1.005), 트레일링·단계익절 둔감화 (5%→4% + 18% tier 추가), 상대손절 완화 (15%→20%). B/E/F/G 는 `tasks/backlog.md`. 일반 사상은 [[scoring-system-ic-validation]] 으로 분리 (출처: session-logs/20260510-195349-94ba-*)
- 2026-05-12: V3 적용 후 컷오프 캘리브레이션 (commit `50c929c`). V3 첫 적용일부터 컷오프 62 통과 종목 0건 → 매수 중단. V2/V3 30 매칭 종목 비교에서 Spearman ρ=+0.835 / Top-10 교집합 80% 로 **평행이동에 가까운 분포 이동 + 일부 reordering** (SK하이닉스 21→5위, 대한해운 22→9위 등) 확인. 컷오프 62 → 48 로 V3 분포 기준 일평균 5~10건 통과 (과거 빈도 회복). 임시 캘리브레이션이며 forward return 검증은 2~4주 라이브 데이터 누적 후 진행 예정. 백테스트로 V2/V3 비교가 불가능한 이유 (이중 점수 스케일 + 펀더/리서치 과거 시점 재현 불가) 명시. 일반 비교 방법론은 [[scoring-version-comparison-methodology]] 로 분리 (출처: session-logs/20260511-230648-4621-*)
- 2026-05-14: 운영 주기 정합성 검토. launchd 가 `periodic --interval 10 --market domestic` 로 10분 단위 폴링, `config/trading.yaml: bar_interval: "1d"` 로 일봉 입력. 한국장 6.5h 동안 ~47회 폴링이 동일 일봉을 반복 평가 → 폴링 단축 (10→5분) 만으로는 알파 0 증가, API 호출만 2배. 폴링 주기 단축이 의미를 가지려면 `bar_interval` 도 분봉으로 함께 내려야 한다는 결론. 일봉 유지 시 진짜 레버는 진입/청산 타이밍 (시초가 회피, 종가 근처 분할). 일반 사상은 [[polling-interval-vs-bar-interval]] 으로 분리 (출처: session-logs/20260514-175837-5657-*)
