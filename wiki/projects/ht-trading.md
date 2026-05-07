---
title: "ht_trading — 프로젝트 설계 상세"
domain: "personal"
sensitivity: "public"
tags: ["project", "trading", "scoring", "algorithm", "config"]
created: "2026-04-23"
updated: "2026-05-08"
sources:
  - "session-logs/20260422-230939-22f1-스코어링-점수를-65-점에서-60-점으로-조정했는지-확인해-주세요.md"
  - "session-logs/20260423-120308-f269-오늘-거래중에서-삼성전자-매수-시그널이-발생한뒤-3분할-매수중-1회만-매수하고-나머지-매수.md"
  - "session-logs/20260423-193125-b999-graphify.md"
  - "session-logs/20260426-111623-cfe2-4월-24일-매매시-몇가지-종목들이-리그크-거부로-인해서-매수거부가-발생했습니다.-왜-그런.md"
  - "session-logs/20260428-235122-215d-오늘-매매-로그를-분석해서-개선사항을-도출해-주세요.md"
  - "session-logs/20260506-230405-a179-오늘-매수가능-금액이-있었는데도-매수가-리스크에-걸려-매수-되지-않았습니다.-제가-확인해.md"
  - "session-logs/20260507-224943-690c-익절-조건이-됐는데도-익절을-하지-않는-것-같습니다.-오늘자-매매로그를-확인해서-익절조건인.md"
confidence: "high"
related:
  - "wiki/bugs/kis-cash-d2-settlement-buy-rejection.md"
  - "wiki/bugs/dict-get-default-no-bootstrap.md"
  - "wiki/analyses/kis-balance-api-fields.md"
---

# ht_trading — 알고리즘 트레이딩 프로젝트

Python 기반 개인 알고리즘 트레이딩 시스템. `ScoringStrategy`를 중심으로 매수 시그널을 점수화해 종목을 선별한다.

## 스코어링 시스템 구조

### 이중 점수 스케일

| 컨텍스트 | 스케일 | 파라미터명 | 현재 값 |
|---------|--------|-----------|---------|
| 백테스트 | 40점 만점 | `buy_min_score` | 25 |
| 라이브 screener | 100점 만점 | `buy_min_score_full` | 62 (2026-05-06 60 → 62) |

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

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260422-230939-22f1 에서 추출)
- 2026-04-23: split throttle G4-1 섹션 추가, 드로다운 버그 수정 기록 (세션 로그 20260423-120308-f269)
- 2026-04-23: graphify 아키텍처 분석, 버그 수정 3건, 백로그 B1-B4 추가 (세션 로그 20260423-193125-b999)
- 2026-04-26: 리스크 매니저 정책 + 4/24 리스크 거부 사례 분석 추가 (세션 로그 20260426-111623-cfe2)
- 2026-05-07: KIS 매수가능 현금 D+2 정산 누락 버그 수정 추가 (commit c6109f4, 세션 로그 20260506-230405-a179). 매수 커트라인 60 → 62 (commit faa0518)
