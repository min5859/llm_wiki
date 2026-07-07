---
title: "ht_trading — 프로젝트 설계 상세"
domain: "trading"
sensitivity: "public"
tags: ["project", "trading", "scoring", "algorithm", "config"]
created: "2026-04-23"
updated: "2026-07-08"
sources:
  - "session-logs/20260704-130158-2dad-현재-프로젝트에서-개선할-부분이-있을지-검토해줘.md"
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
  - "session-logs/20260518-233131-6a41-오늘-현대해상의-매매가-계속-거부되었는데-원인을-분석해-주세요.md"
  - "session-logs/20260526-232119-548d-현재-수익이-없는-종목-유지-기간이-얼마인가요.md"
  - "session-logs/20260527-213708-7369-오늘-SK-스퀘어-매수-거부가-발행-했는데-원인이-뭔지-로그를-살펴봐-주세요.md"
  - "session-logs/20260530-110224-e6bb-ht_trading은-min_score=48에서-하루-종일-후보-0개였습니다.-현재-시장.md"
  - "session-logs/20260530-203624-b2b6-얼마전-매수매도-시작-시간을-9시30분에서-매도는-9시10분,-매수는-10시-로-변경했는데.md"
  - "session-logs/20260531-211232-de76-지금-프로그램에서-몇가지종목은-지정해서-무한-매수법으로-운용하려고-합니다.-현재-구조를-파.md"
  - "session-logs/20260601-074556-75cf-스코링-전략에서-매수를-3분할로-하고-있는데-3분할-인터벌이-얼마로-되어-있나요.md"
  - "session-logs/20260602-221627-322d-아래-개선제안-사항을-검토해서-반영하는게-합리적인지-판단해-주세요.md"
  - "session-logs/20260603-135643-1e3b-오늘은-공휴일이라-휴장인데-동작을-하고있습니다.-공휴일에는-동작하지-않도록-해-줘.md"
  - "session-logs/20260604-232009-b35a-•-신세계는-15-10-매도-후-15-20-재매수로-10분-만에-재진입했습니다.-트레일링스.md"
  - "session-logs/20260616-210439-d6f6-오늘-알고리즘-바꾼지-2일-지났는데-2일-동안-매매에서-오류나-개선점이-없었는지-검토해줘.md"
  - "session-logs/20260616-225148-21a4-아래-사항은-오늘-로그에서-나온-사항들인데-개선할만한-포인트가-있을까--•-BUY-0106.md"
  - "session-logs/20260622-225750-4b1b-오늘-보해양조를-매수후에-10-%-가-넘은-상태까지-갔다가-다시--2-%-가-되었는데-트레.md"
  - "session-logs/20260706-214100-fe2f-무한매수-알고리즘에-종목을-하나더-추가하고-싶은데-KOSPI-0195S0-TIGER-SK하.md"
  - "session-logs/20260707-223019-2c24-오늘-남성이라는-종목이-추천되서-매수가-되었는데-실제-스코어가-62-점이-넘었는지-확인해줘.md"
confidence: "high"
related:
  - "wiki/bugs/kis-derivative-etf-order-reject-apbk1497.md"
  - "wiki/bugs/kis-holiday-detection-bsop-date.md"
  - "wiki/bugs/kis-cash-d2-settlement-buy-rejection.md"
  - "wiki/bugs/order-post-retry-double-fill.md"
  - "wiki/patterns/backtest-clock-injection.md"
  - "wiki/analyses/backtest-fill-model-adverse-selection.md"
  - "wiki/patterns/startup-dependency-crash-loop.md"
  - "wiki/patterns/parallel-review-adversarial-fix-workflow.md"
  - "wiki/bugs/dict-get-default-no-bootstrap.md"
  - "wiki/analyses/kis-balance-api-fields.md"
  - "wiki/analyses/partial-sell-rule-idempotency.md"
  - "wiki/analyses/scoring-system-ic-validation.md"
  - "wiki/analyses/scoring-version-comparison-methodology.md"
  - "wiki/analyses/dca-trailing-stop-tuning.md"
  - "wiki/analyses/polling-interval-vs-bar-interval.md"
  - "wiki/bugs/absolute-stop-loss-elif-dead-code.md"
  - "wiki/bugs/reentry-after-full-liquidation-no-cooldown.md"
  - "wiki/projects/n-stock-info.md"
  - "wiki/patterns/notification-dedup-throttle.md"
  - "wiki/analyses/averaging-down-vs-momentum-add-on.md"
  - "wiki/analyses/risk-control-exemption-and-failed-attempt-accounting.md"
---

# ht_trading — 알고리즘 트레이딩 프로젝트

Python 기반 개인 알고리즘 트레이딩 시스템. `ScoringStrategy`를 중심으로 매수 시그널을 점수화해 종목을 선별한다.

## 스코어링 시스템 구조

### 이중 점수 스케일

| 컨텍스트 | 스케일 | 파라미터명 | 현재 값 |
|---------|--------|-----------|---------|
| 백테스트 | 40점 만점 | `buy_min_score` | 25 |
| 라이브 screener | 100점 만점 | `buy_min_score_full` | **62** (2026-05-30 V3 리버트 후 V2 기준값 복원; V3 기간: 48) |

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

### 드로다운 초과 시 반등 폴백 조건 (2026-05-30, commit `7e752ed`)

기존 단순 -5% 차단에서, 드로다운이 임계를 넘어도 **저점 대비 반등 + 기술점수 회복** 시 추가매수를 허용하는 조건으로 변경.

**배경**: 012030 등이 -20% 손실 상태에서 시장이 회복해도 영구 차단되어 추가매수 기회를 모두 놓치는 문제.

**로직 흐름** (`scoring_strategy.py:_can_add_split`):

```
드로다운 > max_split_drawdown_pct (5%)?
  ├─ lowest_price 미추적 → 기존: "드로다운 과다" 차단
  └─ lowest_price 있음:
       ├─ 저점 대비 반등 < split_drawdown_rebound_pct (3%) → "반등 부족" 차단
       └─ 반등 ≥ 3%:
            ├─ 기술점수 < split_recovery_score (20/40) → "기술점수 미회복" 차단
            └─ 기술점수 ≥ 20/40 → 허용
```

**추가 상태**: `_lowest_price_since_entry[symbol]` — `on_bar`에서 실시간가/종가로 매 사이클 저점 갱신. 포지션 청산 시 초기화. `get_state`/`set_state`에 포함(재시작 후 유지).

**설정값** (`scoring.yaml`):
```yaml
split_drawdown_rebound_pct: 0.03  # 저점 대비 반등 최소값
split_recovery_score: 20.0        # 기술점수 회복 최소값 (40점 만점)
```

테스트 8개 추가 (T12a~e, T13~15). 182 통과 / 5 기존 실패 유지.

### 버그 수정 (2026-04-23, commit c5dc818)

**증상**: 1차 매수 후 하락 중인데도 2차 분할 매수가 진행되지 않음. 로그에 "split throttle 드로다운 부족: 0.00% < 1.50%" 반복.

**원인**: `_last_split_price`에 `last_bar.close`(일봉 캐시 종가)를 저장했고, 드로다운 비교도 `bar.close`(같은 일봉 캐시)로 수행 → 일봉 캐시는 장중 변하지 않아 드로다운 항상 0%.

**수정 내용**:
| 수정 위치 | 변경 전 | 변경 후 |
|----------|---------|---------|
| `_can_add_split` | `bar.close` 기준 비교 | `pos.current_price`(브로커 실시간 평가가) 기준 비교 |
| `_record_split_event` (라이브 경로) | `last_bar.close` 저장 | `pb.limit_price`(지정가) 저장 |

## 버그 수정 이력

### 버그 수정 (2026-06-03, commit `2b17aba`): 공휴일 휴장 판정 실패 (bsop_date)

**증상**: 공휴일(휴장)인데 라이브 엔진이 사이클마다 매수 주문을 시도 → KIS 가 `APBK0919 장운영일자가 주문일과 상이합니다` 로 전량 거부. 로그에 "공휴일 감지" 가 한 번도 안 찍힘.

**원인**: `market_hours.py` 의 휴장 판정이 삼성전자 현재가의 `bsop_date`(영업일자)와 오늘 날짜 비교 방식. 공휴일에도 이 API 가 당일 날짜를 반환해 `bsop_date == today` → "개장" 으로 오판.

**수정**: KIS 국내휴장일조회 `CTCA0903R` 의 `opnd_yn`(개장일 여부)로 판정. `domestic.py` 에 `is_open_day()` 추가, `market_hours.py:is_market_open_live()` 가 그것을 사용. 실 API 호출로 오늘(20260603) `opnd_yn=N`(휴장) 검증, 테스트 218건 통과.

**부가**: 6시간 37분째 떠 있던 라이브 프로세스가 옛 코드를 메모리에 들고 돌아 지속됨 → `launchctl kickstart -k` 재시작으로 새 판정 적용 확인. **라이브 데몬은 코드 수정 후 재시작 전까지 옛 로직으로 동작.**

→ 자세한 분석: [[kis-holiday-detection-bsop-date]]

---

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

### 버그 수정 (2026-06-22): 트레일링 스톱 발동했으나 매도 미실행 (5개 연쇄 버그)

**증상**: 보해양조가 +10% 도달 후 -2%까지 되돌아왔는데 트레일링 스톱이 매도하지 않음. 트레일링 로직 자체는 정상 발동(13:47부터 반복 SELL 시그널)했으나 매도가 브로커에 도달하지 못함.

근본 원인은 5개 독립 버그의 연쇄였다:

| # | 원인 | 수정 |
|---|------|------|
| A | 일일 주문 한도 `break`가 BUY/SELL 무차별 적용 → 한도 소진 후 SELL도 차단 | 한도는 BUY에만, SELL 면제 (`dc2a215`) |
| B | `submit_order` 실패해도 카운터 증가 → 1주 매수 거부 85회가 한도 소진(15+85=100) | 성공 분기 안에서만 증가 (`f2948ed`) |
| C | KIS 즉시거부 경로에 dedup 없어 동일 BUY 무한 반복 | 거부 시 `on_order_submit_failed` 백오프 (`29670b5`), 날짜기준 통일 (`feaa434`) |
| D | 지정가 만료 시 내부 pending만 삭제, KIS 주문 미취소 → 유령 체결(나중에 OPEN 체결) | 취소 큐잉 + 주기적 멱등 reconciliation (`9f77e91`, `8230c9d`) |
| 증폭 | 폴링 2분 단일 사이클(매수·매도 공유)이 매수 거부도 2분마다 반복 | 매수/매도 사이클 분리 (`d9e5a44`) |

추가로 SK스퀘어 1주 거부의 근본 원인은 `get_cash()`가 예수금만 반환하고 **미체결 매수가 묶은 금액을 빼지 않아** 시스템은 현금 있다고 보고 제출하나 KIS가 `주문가능금액 초과`로 거부한 것. 가용현금 ≠ 예수금.

> 이 사건의 범용 교훈(리스크 감축 주문 면제 / 실패 시도 회계 / 포기≠취소 / 주기적 멱등 reconciliation / 가용현금 계산)은 [[risk-control-exemption-and-failed-attempt-accounting]] 에 정리. 모든 수정은 test-first(실패 확인→통과), 논리단위 커밋 분리, 267건 통과.

## KIS API 서킷브레이커 (2026-05-30, commit `32a1451`)

잔고 조회 연속 실패 시 주문을 중지하는 안전 장치. **KIS HTTP 500 경고가 반복될 때 오래된 캐시 기반으로 잘못된 거래가 발생하는 것을 방지**.

### 구현 (`kis_broker.py` + `live_engine.py`)

| 컴포넌트 | 변경 내용 |
|---------|---------|
| `KISBroker._api_consecutive_errors` | 연속 오류 카운터 |
| `KISBroker.api_halted` | 주문 중지 플래그 |
| `_on_api_success()` | 성공 시 카운터/플래그 리셋, 회복 로그 |
| `_on_api_error()` | 카운터 증가 → 5회째에 `api_halted=True` + ERROR 로그 |
| `get_cash() / get_equity()` | 성공/실패마다 위 헬퍼 호출; 캐시 반환 시 경과시간 + 30분 초과면 "캐시 만료" 경고 |
| `submit_order()` | `api_halted` 시 API 호출 없이 즉시 `REJECTED` |
| `LiveEngine._check_api_halt_and_notify()` | halt/회복 전환 시 텔레그램 1회 알림 (중복 방지 플래그) |

### 설계 결정

- `get_cash`와 `get_equity`는 각각 `get_balance()`를 별도 호출하므로 한 사이클에서 둘 다 실패하면 카운터가 2씩 증가 → 임계치 5로 **~2-3 사이클 연속 실패 시 중지** 발동
- 캐시 TTL 30분 초과 시 경고만 (주문 중지 아님) — 1일봉 전략에서 30분 이상 캐시 사용은 주의 필요
- 회복 시 텔레그램으로 재개 메시지 발송

테스트 17개 신규 추가 (`tests/broker/test_kis_broker_circuit_breaker.py`).

## 시각 가드 (매수/매도 시작 시각 제한)

시초 변동성 회피를 위한 가드. 개장 직후 지정 시각 이전의 매수/매도 신호를 차단한다. 라이브 전용이며, 백테스트에서는 모든 값을 0으로 두면 비활성.

**라이브 로그 시뮬레이션 근거** (2026-05-14 도입 시 검증): 09:30~09:59 매수 차단 시 1/3분할 진입가 **+1.58% 개선**, 전체 평균 **+1.03% 개선**. 매수 빈도 약 14% 감소.

### 현재 설정 (`config/strategies/scoring.yaml`)

```yaml
buy_min_hour: 9                        # KST 매수 최소 시각 (시, 0=비활성)
buy_min_minute: 30                     # KST 매수 최소 시각 (분) — 09:30 이전 매수 차단
sell_min_hour: 9                       # KST 매도 최소 시각 (시, 0=비활성)
sell_min_minute: 30                    # KST 매도 최소 시각 (분) — 09:30 이전 매도 차단
```

### 구현 (`scoring_strategy.py`)

- 파라미터 로드: `line:109~114` — `ctx.params.get("buy_min_hour", 0)` 등으로 YAML 주입
- 매수 가드 적용: `line:574`
- 매도 가드 적용: `line:831`

### 설정값 이력

| 날짜 | 매수 | 매도 | 비고 |
|------|------|------|------|
| 2026-05-14 도입 | 09:30 | 09:30 | 시초 변동성 회피, +1.58% 진입가 개선 효과 확인 |
| 이후 일시 변경 | 10:00 | 09:10 | 시도 후 복원 |
| 2026-05-30 원복 | **09:30** | **09:30** | commit `tune: 매수/매도 시각 가드 10:00/09:10 → 09:30 원복` |

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

### 파라미터 튜닝 이력

| 날짜 | activation | tiers (activation→distance) | 이유 |
|------|-----------|---------------------------|------|
| 2026-04-28 도입 | 3% | {3%,3%} / {10%,6%} / {20%,10%} | 추세 추적형 trailing stop 도입 |
| 2026-05-10 (C 튜닝) | 5% | {5%,4%} / {12%,6%} / {22%,10%} | 산일전기/DN오토모티브 조기 청산 방지 |
| 2026-05-14 (1단계) | **4%** | {4%,4%} / {12%,6%} / {22%,10%} | 활성화만 낮춤 (+이득 6건 확인), distance 조정은 관찰 후 |
| 2026-05-30 | **3%** | **{3%,2%} / {12%,4%} / {22%,8%}** | 수익 보존율 강화 — distance 전 구간 2%p 축소 |

**현재 설정 (2026-05-30 기준)**:

```yaml
trailing_activation_pct: 0.03
trailing_tiers:
  - {activation: 0.03, distance: 0.02}   # 수익 3~11%: 2% 거리
  - {activation: 0.12, distance: 0.04}   # 수익 12~21%: 4% 거리
  - {activation: 0.22, distance: 0.08}   # 수익 22%+: 8% 거리
```

> tier1 activation=3%, distance=2% → 수익 3% 도달 후 고점 대비 -2% 하락 시 청산 → 최소 보존 수익 약 +1%

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
| 2026-05-30 | 매수/매도 시각 가드 10:00/09:10 → 09:30 원복 | 일시 변경 후 복원 |
| 2026-05-30 | trailing stop activation 4%→3%, distance 전 구간 2%p 축소 ({4%,4%}→{3%,2%}, {12%,6%}→{12%,4%}, {22%,10%}→{22%,8%}) | 수익 보존율 강화 |
| 2026-06-11 | `reentry_cooldown_minutes` 60 → 1080 (commit `c4cc530`) | 분할 throttle(다음날=1080)과 비대칭 가드 해소 — 같은날 휩쏘 재진입 차단 |
| 2026-06-11 | 폴링 주기 10분 → 2분 단축 (commit `1bb80f1`) | 트레일링스톱이 실시간 평가가 기반이라 폴링 해상도 = 청산 해상도 |
| 2026-06-11 | MARKET 매도 EOD 체결 검증 추가 (commit `1cb9be2`) | submit 시점 즉시 FILLED 가정의 공백 보완 — 미체결 주문 경고 |
| 2026-06-22 | 일일 주문 한도 `break`를 BUY에만 적용 (commit `dc2a215`) | 한도 소진이 트레일링/손절 SELL까지 차단 → 안전장치가 손실 방치 |
| 2026-06-22 | `_increment_order_count`를 제출 성공 분기로 이동 (commit `f2948ed`) | 1주 매수 거부 85회가 카운터 소진 → 한도 조기 고갈 |
| 2026-06-22 | 브로커 거부 시 `on_order_submit_failed` 재주문 백오프 (commit `29670b5`, 날짜기준 `feaa434`) | KIS 즉시거부 경로 dedup 부재로 동일 BUY 무한 반복 |
| 2026-06-22 | 매수/매도 사이클 분리 — `allow_buy` 플래그, 매도 2분 / 매수 30분 (commit `d9e5a44`) | 청산용 2분 폴링이 매수 거부 폭증 유발 (부작용 분리) |
| 2026-06-22 | 지정가 만료 시 취소 큐잉 + 주기적 멱등 reconciliation (commit `9f77e91`, `8230c9d`) | 내부 pending만 삭제하고 KIS 미취소 → 유령 체결 |
| 2026-06-22 | 매수 지정가 `limit_price_ratio` 0.995 → 1.0 (commit `ac74e07`) | 현재가-0.5%는 체결 불리, 1.0은 가격제한폭 내라 거부 없고 체결률↑ |

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
| `max_positions` | 10종목 (동시 보유 최대) — 2026-05-18 기준. 직전 5종목에서 상향 |
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

### 리스크 거부 텔레그램 알림 세부 사유 (2026-05-27, commit 기록)

`RiskManager`에 `last_reject_reason: str | None` 속성을 추가하고, 6가지 거부 조건별로 수치를 포함한 상세 메시지를 설정. `live_engine.py` 의 텔레그램 알림에 `reject_detail` 필드로 포함.

**이전 메시지:**
```
[리스크 거부] 매수 거부: SK스퀘어 (보유분할·기술스코어 27/40 2/3분할) — 리스크 조건 미충족
```

**개선 후 메시지 (예시 — 누적 포지션 한도 초과 시):**
```
[리스크 거부] 매수 거부: SK스퀘어 (보유분할·기술스코어 27/40 2/3분할)
사유: 종목별 누적 포지션 한도 초과 (기존 1,303,000 + 신규 1,317,000 = 2,620,000원 = 32.8% > 한도 30%)
```

커버되는 6가지 거부 사유: 일일 손실 한도 초과, 최대 보유 종목 수 초과, 단일 주문 금액 한도 초과, 단일 주문 비율 한도 초과, 누적 포지션 한도 초과, 현금 부족.

**구현 방식**: `validate_signal()` 각 `return None` 직전에 `self.last_reject_reason = "..."` 설정. `_validate_buy()` 진입 시 `self.last_reject_reason = None` 으로 초기화. 호출부(backtest, tests) 는 변경 없음.

**SK스퀘어 거부 분석 사례 (2026-05-27)**: 2차 분할 매수 시도에서 기존 보유분 ~130만원 + 신규 주문 ~131만원 = 263만원(32.8%) > 한도 30%. 설계대로 동작한 정상 거부.

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

### n_stock_info V3 리버트 + 선택적 재적용 (2026-05-30, commit `afea220` + `선택적`)

**배경**: V3 적용(2026-05-10) 후 min_score=48에서도 하루 종일 후보 0건 발생. 모멘텀 추종 전략과 V3 알고리즘 충돌이 원인.

**V3 모멘텀 충돌 포인트**:

| V3 변경 | 모멘텀 전략과의 충돌 |
|---------|---------------------|
| 신고가 역전: 돌파(100%+) 8점 → 직전(95~99%) 12점 | 모멘텀 추종은 돌파 종목을 선호 |
| 거래량 역전: 5배+ 감점 ("펌프 의심") | 거래량 폭발 종목이 불이익 |
| MA 가중치 15→8점 페이드 | 트렌드 추종에서 MA 정배열은 핵심 신호 |

**조치**: `git revert 0088d85` 로 V2 완전 복원.

**선택적 재적용** (V2 기반에서 두 가지만 재적용, commit 별도):

1. **EPS 절대값 → earnings_yield** (EPS/Price × 100%)
   - 고가주 보너스 버그 수정: EPS=20,000 삼성전자 만점 / EPS=300 소형주 2점 → 종목 크기 무관 수익성 비교
   - 총점(10점) 유지, `max(0, EPS/Price × 100%)` 기준 5단계 점수표

2. **캔들 세분화** (5점 상한 유지)
   - 강양봉(+2% 이상) → 5점 / 일반 양봉 → 3점
   - IC +0.039 근거 유지, 가중치는 V2 그대로

**미적용** (V2 유지): MA 페이드(15→8점), 신고가 역전, 거래량 역전, ATR 신규.

**screener 커트라인 복원** (commit `5/30`): `48 → 62` (V2 점수 분포 기준값 복원). 캔들 변경으로 일반 양봉 종목 최대 2점 낮아지는 효과만 있으므로 62 복원이 적절.

**editable 설치 (`pip install -e .`)** 이므로 n_stock_info 재설치 불필요 — 파일 변경 즉시 반영.

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

### Backlog (B/F/G, `tasks/backlog.md`)

| ID | 항목 | 비고 |
|----|------|------|
| **B** | Split throttle 완화 (`min_split_interval_minutes: 60→30`, `max_split_drawdown_pct: 0.05→0.07`) | 5/4 산일전기 5.5% 드로다운에서 12회 연속 차단 |
| **F** | 보유 종목 보유분할 cut 25→22 (별도 파라미터화) | 매수 시그널 268건 중 90% 가 보유 7종목 집중 → 분할 진행/신규 슬롯 확보 |
| **G** | 골든시그널 OR 채널 (`강양봉 ≥2% AND 거래량 2~4배` 동시 시 점수 무관 후보) | IC 검증 단일 룰 승률 67.2%, 평균 +3.38%. V3 라이브 결과 양호 시 후속 적용 |

> **E — 부분 적용 완료 (2026-05-26, commit `513c7a7`)**: `stale_holding_days: 10 → 15` 만 반영. 백로그에 있던 `profit_pct < -0.02` 추가 조건은 미적용.

### 적용 후 launchd 재시작 검증

```
2026-05-10 23:02:39  ScoringStrategy 초기화: buy_min_score=25, relative_stop=20.0%,
  trailing활성=5.0% tiers=[5%→4% 12%→6% 22%→10%], profit_take=2단계
```

이전 버전 (`relative_stop=15.0%, trailing활성=3.0%, profit_take=1단계`) 과 한 줄 비교로 5개 변경 (B,A,C,E,D) 반영 확인 가능. 다음 개장 (5/11) 부터 효과.

## 거래대금 순위 신호 모니터 (2026-05-30, commit `feat: 거래대금 순위 기능 추가`)

기존 거래량 순위(주 단위)에 더해 **거래대금(금액 기준) TOP 10** 을 텔레그램으로 전송하는 기능 추가.

| 항목 | 기존 | 추가 |
|------|------|------|
| `scripts/run_signal_monitor.py` CLI | `signal`, `volume`, `all` | + `value` 모드 |
| `DomesticAPI.get_trade_value_rank()` | — | 거래량 TR 응답의 `acml_tr_pbmn` 필드로 재정렬 |
| `OverseasAPI.get_trade_value_rank()` | — | `price × volume` 계산 기준 |
| 텔레그램 포맷 | 📈 거래량 TOP 10 | + 💰 거래대금 TOP 10 (국내: 억 단위, 미국: $B) |

`all` 모드에 자동 포함. crontab 수정 불필요 — 기존 `run_signal_monitor.py all` 실행이 거래대금도 전송.

```
국내: FHPST01710000 TR 응답의 acml_tr_pbmn (누적 거래대금) 사용
미국: OverseasAPI.get_volume_rank() 응답 price × volume 계산
```

## 무한 매수법 (InfiniteBuying) 전략 활성화 (2026-05-31)

161580.KQ 종목을 하루 1주씩 평균단가 이하일 때 분할 매수하고, 수익 3% 달성 후 tiered trailing stop으로 자동 매도하는 전략을 `scoring_kospi`와 독립적으로 동시 운용.

commit: `34b1a88 feat: 무한매수법 전략 활성화`

### 설계 결정: Signal.bypass_position_check 플래그 (Option C 선택)

무한 매수법은 포지션이 계속 누적되므로 RiskManager의 30% 누적 포지션 한도에 의해 조기 차단됨. 3가지 아키텍처 옵션 중 Option C를 채택:

| 옵션 | 접근 | 결정 |
|------|------|------|
| **A: 전역 한도 상향** | `max_position_pct: 0.30 → 0.70` | ❌ scoring_kospi까지 70%까지 투자 가능해져 의도치 않은 리스크 확대 |
| **B: 전략별 RiskManager** | `risk_overrides` + LiveEngine 분기 | ❌ LiveEngine 구조 변경이 크고 복잡도 높음 |
| **C: Signal 플래그** | `Signal.bypass_position_check: bool = False` | ✅ backward compatible, 최소 범위 변경 |

**구현**: `Signal.bypass_position_check = True` 로 설정된 신호는 `RiskManager._validate_buy()` 에서 누적 포지션 비율 체크(30% 한도)만 건너뜀. 현금 확인·단일 주문금액 체크는 그대로 유지. `scoring_kospi` 신호는 `bypass_position_check = False` (default) 이므로 영향 없음.

### InfiniteBuying Tiered Trailing Stop

수익 3% 달성 시 고점 추적 시작. `ScoringStrategy`의 `trailing_tiers`보다 훨씬 타이트한 거리:

| 수익 구간 | 고점 대비 하락 시 매도 |
|---------|-----------------|
| 3% ~ 7% | **1%** |
| 7% ~ 15% | **2%** |
| 15% 이상 | **3%** |

```yaml
# config/strategies/infinite_buying.yaml
trailing_activation_pct: 0.03
trailing_tiers:
  - {from: 0.03, to: 0.07, distance: 0.01}
  - {from: 0.07, to: 0.15, distance: 0.02}
  - {from: 0.15, to: 999, distance: 0.03}
```

> ScoringStrategy의 trailing_tiers({3%,2%}/{12%,4%}/{22%,8%})보다 타이트한 이유: 매도 후 사이클 리셋 → 재진입 가능 구조이므로 조기 익절이 유리.

매도 후 `buy_count=0`, `_peak_prices[symbol]` 제거 → 다음 하락 시 재매수 사이클 자동 시작.

### ScoringStrategy.exclude_codes 기능 추가

`on_bar` 진입 시 지정 종목을 스킵해 scoring과 InfiniteBuying의 동일 종목 중복 진입 방지.

```yaml
# config/strategies/scoring.yaml
exclude_codes:
  - "161580"   # 무한매수법 운용 종목 격리
```

### _limit_buy_signals 우선순위 변경

기존: 사이클당 최대 3개 BUY 신호 (점수순)
변경: **이미 포지션이 있는 종목의 BUY(= 추가매수)는 3개 제한 제외**, 신규 BUY만 점수순 3개 제한

```python
add_buys = [(s, sig) for s, sig in buys if sig.symbol in self._cached_positions]
new_buys = [(s, sig) for s, sig in buys if sig.symbol not in self._cached_positions]
new_buys.sort(key=_buy_score, reverse=True)
return sells + add_buys + new_buys[:self._MAX_BUY_SIGNALS]
```

### max_positions 11로 조정

`config/trading.yaml`: `max_positions: 11` (scoring_kospi 10종목 + 무한매수법 1종목)

### 변경 파일 요약 (9파일)

| 파일 | 변경 내용 |
|------|-----------|
| `core/models.py` | `Signal`에 `bypass_position_check: bool = False` 추가 |
| `risk/manager.py` | bypass 플래그 True 시 누적 포지션 한도 체크 스킵 |
| `engine/live_engine.py` | `_limit_buy_signals` 추가매수 우선 통과 로직 |
| `strategy/base.py` | `buy()` 메서드에 `bypass_position_check` 파라미터 추가 |
| `infinite_buying.py` | 1주 고정 매수 + tiered trailing stop 전체 재구현 |
| `scoring_strategy.py` | `exclude_codes` 지원 추가 |
| `infinite_buying.yaml` | trailing_tiers, num_splits: 40 |
| `scoring.yaml` | `exclude_codes: ["161580"]` 추가 |
| `trading.yaml` | `infinite_buying_kosdaq` 활성화, `max_positions: 11` |

신규 테스트 19개: `test_infinite_buying_trailing.py` 12개 + `test_scoring_exclude_codes.py` 7개. 전부 통과.

### 종목 추가/변경 방법

**같은 파라미터**로 운용: `config/trading.yaml` (symbols) + `config/strategies/scoring.yaml` (exclude_codes) 두 곳만 수정.

**종목별 파라미터 분리** 필요 시: 전략 인스턴스를 별도 등록 + 별도 `.yaml` 파일 사용.

```yaml
# 별도 파라미터 예시
- name: "infinite_buying_035720"
  class: "ht_trading.strategy.builtin.infinite_buying.InfiniteBuying"
  params_file: "strategies/infinite_buying_035720.yaml"
  symbols:
    - market: "KOSDAQ"
      codes: ["035720"]
```

> **주의**: KOSPI 종목은 `market: "KOSPI"`, KOSDAQ 종목은 `market: "KOSDAQ"` 으로 지정.

### 신규 종목 추가: 0195S0 TIGER SK하이닉스단일종목레버리지 (2026-07-06)

무한매수 "매일 1주 매수" 대상에 0195S0 (2배 레버리지 ETF, 2026-05-27 상장) 을 추가. `config/trading.yaml` 에 전략 인스턴스(`infinite_buying_kospi`) 등록, 파라미터는 공용 `infinite_buying.yaml` 공유 (splits=40, trailing_activation=3.0%, tiers 3구간, 시각 가드 09:30). **`max_positions: 11 → 12` 동반 증설** — 한도가 차 있으면 신규 종목의 "첫 매수" 가 거부되므로 (무한매수 종목도 한도를 함께 소진) 종목 추가 시 반드시 같이 늘린다.

**종목 추가 전 확인 체크리스트** (이 세션에서 정리):

1. 전략 인스턴스 로딩·상태 저장 방식 — `get_state`/`set_state`, `config/.strategy_state.json`
2. `max_positions` 여유 (위 규칙)
3. **영숫자 코드 함정** — KRX 신규 ETF/ETN 은 "0195S0" 같은 영문 혼용 코드를 받으므로, 종목코드를 숫자로 가정하는 코드 (`int()` 변환, `isdigit` 등) 가 없는지 탐색
4. warmup 요구치 vs 상장 이력 — warmup 60봉 요구인데 상장 28일이면 28봉만 로드됨. 걸러지지 않고 동작하는지 로그로 확인 (실측: 동작함)

**매수 시간대 사전 검증**: 코드 수정 전에 ETF 1분봉 29거래일 + 기초자산 000660 6개월 분봉으로 매수 시각별 유불리를 실측 → 현행 09:30 시각 가드 + 30분 매수 사이클 유지 결론 (파라미터 변경 불필요). 분석은 [[dca-intraday-buy-timing]], 과거일 분봉 조회 TR 은 [[kis-minute-chart-trs]] 로 분리.

### 0195S0 이 24회 전부 매수 거부됨 — 계좌 파생ETF 미신청 [APBK1497] (2026-07-07)

다음날 실전에서 `0195S0` 매수 신호는 정상 생성됐으나 30분 사이클마다 **24회 전부 거부**됐다. 원인은 코드/설정이 아니라 **계좌에 파생ETF 거래 권한(선택확인서 징구 + 레버리지 ETP 사전교육)이 없어서**였다. KIS 응답: `[APBK1497] 파생ETF 미 신청(선택확인서 미 징구) 계좌는 파생ETF 거래가 불가합니다.` 일반 주식(남성 004270 등)은 무관하게 정상 체결. **신규 종목 편입 체크리스트에 "파생/레버리지면 계좌 상품 권한 확인"을 선결로 추가.** 상세·일반 교훈은 [[kis-derivative-etf-order-reject-apbk1497]].

## 매수 시점 스코어 감사 로그 (2026-07-07, 관측성 개선)

"추천·매수된 종목의 실제 점수가 컷(62)을 넘었는지" 를 사후에 검증하려 했으나 **DB로는 추적이 불가능**했다. 원인·해법을 아래에 기록(매매 로직 불변, 관측성만 개선).

- **매수 시점 스냅샷이 사후 소실**: 종목 추천 소스인 [[n-stock-info]] 는 평일 매 `:20`/`:50` (30분 간격) cron 으로 그날 `report_date` 행을 **DELETE→재삽입**(일자 멱등)한다. ht_trading 이 09:30 에 읽은 09:20 스냅샷(남성 추천)이 이후 실행들로 통째로 덮어써져, **사후 조회 시 그 종목 레코드가 사라지고 그날 전 종목 `is_recommended=0`** 이 된다. → 매수 당시 점수/추천 여부는 DB 가 아니라 **라이브 매수 로그(`ht_live.log`)에만 잔존**. 멱등 덮어쓰기 vs point-in-time 이력의 일반 긴장은 [[stock-screening-score-design]] §5.
- **2단계 컷 구조**: 추천 컷(n_stock_info 자체 `min_score=55`)과 매수 컷(ht_trading screener `min_score:62` = `buy_min_score_full:62`, 100점 만점)이 **별개**다. 경계 종목은 55 는 여유 통과하고 62 는 아슬하게 통과할 수 있어(남성 사례), 텔레그램 추천 목록에 장중 등락으로 들락날락한다(발송 자체는 정상).
- **표시 반올림이 경계 점수 은폐**: 매수 로그의 점수 표시가 `%.0f` 반올림(`scoring_strategy.py:783`)이라 경계값(62.0~62.5)의 실제 소수점이 가려졌다.
- **개선**: `BuyCandidate` 에 `report_date`(스냅샷 기준일) 필드 추가, 매수 지점에 **점수 분해(소수점 포함)·현재가·기준일을 전용 감사 로그**로 기록. 신규 테스트 `test_scoring_buy_audit_log.py`, 전체 **393 테스트 통과** + ruff 클린. 이제 "매수됐는데 지금 DB엔 왜 없나" 류는 감사 로그가 유일한 진실원.

## 휴장 대기 중 생존 로그 (2026-05-31, commit bcf5d74)

주말/공휴일에 프로세스 정상 대기인지 크래시인지 구분 불가 문제를 해결. 장외 대기 루프에 1시간마다 heartbeat 로그 추가.

```python
# live_engine.py 장외 대기 루프 (5줄 변경)
wait_end = time.monotonic() + wait_seconds
last_log = time.monotonic()
while self._running and time.monotonic() < wait_end:
    time.sleep(1)
    if time.monotonic() - last_log >= 3600:
        remaining = (wait_end - time.monotonic()) / 60
        logger.info("휴장 대기 중 (프로세스 정상). 개장까지 %.0f분.", remaining)
        last_log = time.monotonic()
```

확인 방법: 로그에 `"휴장 대기 중 (프로세스 정상)"` 가 1시간마다 찍히면 정상. 없으면 프로세스 중단 의심.

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
- 2026-05-18: 현대해상(001450) 매매 거부 사례 — 5/18 09:31~15:20 동안 매 10분마다 BUY 시그널 (52점, 5주, 1/3분할) 이 생성됐지만 모두 `[리스크 거부] 매수 거부: 현대해상 — 리스크 조건 미충족` 으로 차단. 직전 라인의 `RiskManager: 최대 포지션 수 초과: 10/10` 가 결정 사유. **사이에 `max_positions: 5 → 10` 상향 조정이 있었음** (현재 `config/trading.yaml: max_positions: 10`). 그럼에도 장 내내 10종목 (대원강업/대한제분/삼진제약/삼성전자/화신/금호석유화학/GS/한화생명/비에이치/영원무역/동일고무벨트) 가득 → 신규 후보가 들어갈 슬롯 0. 예수금 3,240,679 원은 충분했지만 슬롯 부족이 결정적. **4/24 의 max_positions=5 거부 사례** ([[ht-trading]] 「2026-04-24 리스크 거부 사례 분석」 절) 와 *동일한 구조적 문제* — `max_positions` 만 상향해도 가득 차면 같은 패턴이 재발. 진짜 해결은 「**포지션 교체 (rotation) 로직**」 — 새 시그널 스코어가 기존 보유 최저점보다 높으면 교체. 미구현 (사용자 결정 대기, 백로그 후보). **운영 관찰만, 코드 변경 없음** (출처: session-logs/20260518-233131-6a41-*)
- 2026-05-26: 백로그 **E** 부분 적용 — `stale_holding_days: 10 → 15` (commit `513c7a7`). Rule6 보유 기간 손절 임계를 10거래일→15거래일로 완화. 5/8 롯데정밀화학 -0.3% 본전 청산 회피 목적. `profit_pct < -0.02` 추가 조건은 이번 커밋에 미포함. launchd plist 재시작으로 즉시 반영 확인 (출처: session-logs/20260526-232119-548d-*)
- 2026-05-27: 리스크 거부 텔레그램 알림 세부 사유 추가. `RiskManager.last_reject_reason` 속성 추가, 6가지 거부 조건별 수치 포함 메시지 설정, `live_engine.py` 알림에 포함. 12 테스트 통과. SK스퀘어 2차 분할 매수 시도가 누적 포지션 30% 한도 초과로 정상 거부된 사례 확인 (출처: session-logs/20260527-213708-7369-*)
- 2026-05-30: KIS API 서킷브레이커 구현 — 연속 오류 5회 시 주문 중지 + 텔레그램 알림 (commit `32a1451`). 추가매수 재개 조건 개선 — 드로다운 초과 시 저점 반등 +3% AND 기술점수 20/40 이상으로 허용 (commit `7e752ed`, 테스트 8개 추가). n_stock_info V3 리버트 (commit `afea220`) + 선택적 재적용 (EPS→earnings_yield, 캔들 세분화) — 모멘텀 전략과 충돌하는 신고가 역전/거래량 역전/MA 페이드는 V2 유지. screener min_score 48→62 복원 (V2 분포 기준). 거래대금 TOP 10 텔레그램 알림 추가 (출처: session-logs/20260530-110224-e6bb-*)
- 2026-05-30 (2nd, 20:36~21:46): 시각 가드 매수/매도 09:30 원복 (commit `tune: 매수/매도 시각 가드 10:00/09:10 → 09:30 원복`). trailing stop activation 4%→3%, distance 전 구간 2%p 축소 — tier1 {4%,4%}→{3%,2%}, tier2 {12%,6%}→{12%,4%}, tier3 {22%,10%}→{22%,8%} (commit `tune: trailing stop 활성화 4%→3%, distance 전 구간 2%p 축소`). 시각 가드 섹션 신설, trailing_tiers 파라미터 튜닝 이력 표 추가 (출처: session-logs/20260530-203624-b2b6-*)
- 2026-05-31: 무한매수법(InfiniteBuying) 전략 활성화 (commit `34b1a88`). Signal.bypass_position_check 플래그 도입(Option C — 최소 범위 변경), ScoringStrategy.exclude_codes 추가, _limit_buy_signals 추가매수 우선 통과, Tiered Trailing Stop (3→1%/7→2%/15→3%). max_positions: 11 (scoring 10 + 무한매수 1). 휴장 대기 중 1시간마다 생존 로그 추가 (commit `bcf5d74`). 신규 테스트 19개 통과 (출처: session-logs/20260531-211232-de76-*)
- 2026-06-01: 분할 매수 인터벌 설계 — 복잡한 날짜 분기 로직 대신 **기존 시각 가드와의 조합으로 단순화**. 2·3분할 간격을 "다음날 09:30"으로 하려고 처음엔 `_can_add_split` 에 라이브/백테스트 날짜 분기 로직을 새로 짰으나 (테스트 다수 수정 필요), 사용자가 "가격(시간 임계)을 충분히 키우면 장이 종료돼 자동으로 다음날로 넘어가지 않나"라고 지적. 장 마지막 체결(15:30)~다음날 첫 매수(09:30) 간격이 18시간이므로 `min_split_interval_minutes: 1080` (18시간) 으로 두면 기존 `_try_buy` 의 09:30 시각 가드와 맞물려 어느 시각 체결이든 다음날 09:30 에 매수된다. 복잡한 분기 구현을 전부 `git restore` 로 리셋하고 단순 분(分) 기반 로직 복원 + yaml 값 하나만 변경 (commit, 2 files 3 insertions). 부수 발견: T9 테스트는 HEAD 에서도 `elapsed < 0` 으로 드로다운 체크에 미도달하던 잠재 버그라 `bar.dt` 를 1080분 이후로 수정. **교훈: 새 분기 로직을 짜기 전에 기존 가드와의 조합을 먼저 본다 — 가장 단순한 구현이 정답일 때가 많다** (출처: session-logs/20260601-074556-75cf-*)
- 2026-06-02: KIS `get_balance()` 중복 호출 제거 (perf, commit `1b301c3`). 사용자 제안은 "장중 10분마다 잔고 조회를 주문 직전·직후로 줄이자"였으나, 분석 결과 **더 근본적인 중복 호출**이 선행 문제: `_refresh_cache()` 1회가 `get_cash()`/`get_equity()`/`get_positions()` 를 호출하는데 셋 모두 독립적으로 `domestic.get_balance()` 를 부른다 (3배). 사이클당 강제 초기화 2회 + 주문 전후 스냅샷까지 최대 8회/10분 사이클. → `_get_domestic_balance()` 2초 TTL dedup 캐시 래퍼로 연속 호출 시 API 1회만. 주문 전후 `_safe_balance_snapshot()` 은 신선한 값이 필요해 캐시 우회 유지, 실패 시 캐시 미저장으로 서킷브레이커 카운터 영향 없음. **교훈: 호출 빈도를 줄이기 전에 1회당 실제 API 호출 수(중복 통합)부터 본다**. 부수로 기존 실패 TC 4건 수정 (commit `cf93d47`) — ① `test_multiple_symbols_parallel` 의 `assert_called_once()` 가 라운드완료+체결완료 2이벤트 정상 발생을 1회로 잘못 가정 → `assert_called()`, ② `_daily_cache` 기능이 나중에 추가되며 `kis_historical_cache` 3건이 `_try_last_good()` fallback 경로 대신 `_daily_cache` 히트로 조용히 성공 → 해당 호출 전 `_daily_cache.clear()`. **교훈: 새 캐시/경로를 추가하면 기존 테스트가 검증하려던 경로를 우회해 통과/실패가 뒤집힐 수 있다**. 코드 변경 반영에는 launchd 서비스 재시작 필요 (`launchctl kickstart -k gui/$(id -u)/com.wooki.ht-trading`) — 휴장 대기 중이라 매매 중단 없이 안전 (출처: session-logs/20260602-221627-322d-*)
- 2026-05-19: 같은 세션의 후속 질의 — 「화신 -19%, GS -11% 인데 왜 손절 안 되나」. `scoring_strategy.py:817~833` 의 매도 룰 4종 (상대 손절 / 절대 손절 / 보유기간 / 트레일링) 점검 결과 **절대 손절이 `if … elif` 구조 때문에 dead code** 라는 사실 발견. 벤치마크 (KOSPI 069500) 데이터가 라이브에서 항상 붙어 있어 `elif profit_pct <= -self.absolute_stop_loss_pct:` 분기는 도달 불가. `absolute_stop_loss_pct: 0.10` 설정값은 실효 없음. 게다가 V3 의 D 튜닝으로 `relative_stop_loss_pct: 0.15 → 0.20` 완화 (5/10 commit `d0571c5`) 이 결합돼, **벤치마크 동반 하락기엔 어떤 손실도 컷 못 함**. 화신 (-19%, 5/12 매수, 7일) 과 GS (-11%, 5/15 매수, 4일) 가 정확히 그 케이스. 권장 수정은 `elif` → `if` 로 절대 손절을 병행 검사 + `relative_stop_loss_pct` 0.15 환원 또는 `absolute_stop_loss_pct` 0.08 강화. 일반 교훈은 [[absolute-stop-loss-elif-dead-code]] 로 분리 (벤치마크 의존 손절은 fallback 이 아니라 "추가 가드" 로 설계 / 상대 손절 완화가 절대 손절 dead code 와 결합되면 손절 자체가 꺼진 상태). **사용자 확인까지 코드 변경은 미진행** (출처: session-logs/20260518-233131-6a41-*)
- 2026-06-04: 전량 청산 직후 재매수 방지 — flat 재진입 쿨다운 추가 (commit `70634aa`). 신세계(004170) 가 15:10 트레일링스톱 전량매도 → 15:20 재매수로 10분 만에 재진입한 사례. 원인은 분할 추가매수 throttle (`min_split_interval_minutes` 18h) 이 첫 매수(`splits_done==0`)를 면제하므로 전량 청산 → flat → 다음 사이클 `_try_buy` 재진입 경로에 가드가 전혀 없었던 것. 매도 시각을 기록하는 상태(`_entry_dates` 는 진입일만)도 부재. 수정: `reentry_cooldown_minutes`(기본 60) 파라미터 + `_last_sell_time` 상태, 매도 5개 지점 모두 `_record_full_sell()` 경유로 `sell_qty >= pos.qty` 전량 청산만 시각 기록 (부분 익절·데드크로스 제외), `_try_buy` flat 재진입 시에만 쿨다운 검사, `get_state`/`set_state` 영속화 (ISO, 하위 호환), `scoring.yaml` 에 `reentry_cooldown_minutes: 60` 등록. `0` 으로 비활성화. 신규 테스트 6 cases + 전략 테스트 114건 통과 후 launchd 재시작 반영. 일반 교훈은 [[reentry-after-full-liquidation-no-cooldown]] 로 분리 (throttle 이 덮는/면제하는 경로를 명시 확인, 상태 전이 부작용으로 가드가 리셋되는 경로 의심) (출처: session-logs/20260604-232009-b35a-*)
- 2026-06-11: 외부 개선 제안 7건 타당성 검토 후 3건 적용 (session-logs/20260611-231312-ba46-*). **(1) flat 재진입 쿨다운 60 → 1080분** (commit `c4cc530`) — 분할 throttle(`min_split_interval_minutes`)은 같은날 재매수를 다음날(1080분)까지 막는데 전량청산 후 재진입은 60분만 막던 **비대칭 가드**. 전량 트레일링 청산 → 60분 후 반등 재매수 → 재손절의 같은날 휩쏘가 여전히 가능했음. 두 가드가 같은 위험(재매수)을 덮으므로 1080분(다음날)으로 정렬. (2) **폴링 10분 → 2분 단축** (commit `1bb80f1`) — 초기엔 "일봉이라 폴링 단축 무의미"로 판단했으나 사용자 지적으로 정정: 트레일링스톱은 `bar.close` 가 아니라 `pos.current_price`(매 사이클 강제 갱신)로 트리거되므로 폴링 해상도가 곧 청산 해상도. 시그널 알파는 안 늘지만 청산 타이밍 개선. 일반 사상은 [[polling-interval-vs-bar-interval]] 예외 절로 분리. (3) **MARKET 매도 EOD 체결 검증** (commit `1cb9be2`) — MARKET 매도를 submit 시점에 즉시 `FILLED` 로 가정하던 공백 발견. `get_daily_orders` 를 `sll_buy_dvsn`/`ccld_dvsn` 필터로 일반화(기본값 불변 → 재시작 정합성 영향 없음, 테스트로 고정)하고 SELL 측 파싱 + EOD 체결 요약(체결/미체결 카운트, 미체결 주문별 경고를 로그+텔레그램)을 추가. 신규 테스트 7건, 총 231건 통과. **검토에서 2건은 전제가 틀려 반려** — ② ATR 동적 트레일링(이미 수익 구간별 tier 적용 중), ⑥ 종목별 상세 로깅(`_format_position_lines` 가 이미 종목별 수량/평단/현재가/수익률 로깅). `tasks/backlog.md`(5/10자) 가 현 `scoring.yaml` 과 괴리(문서 split 60 vs 실제 1080 등) — **교훈: 제안의 전제를 현 코드/설정에 대조한 뒤 타당성을 판단하라, 백로그 문서는 코드와 드리프트한다**. launchd 재시작으로 반영
- 2026-06-16 (2nd, 22:51~23:17): **지정가 비율 환원** — `limit_price_ratio: 1.005 → 0.995` (commit `config: revert limit_price_ratio to below-current (1.005->0.995)`). 화신(010690) 상한가 초과 주문 거부 반복이 원인. 5/10 튜닝 A 에서 체결률 개선 목적으로 현재가 +0.5%(marketable limit)로 바꿨으나, **상한가 도달 종목은 `현재가 × 1.005` 가 상한가를 넘어 KIS 가 거부** (no-chase 캡은 직전 실패가 자체가 상한가 초과라 막지 못함). 원 설계(strategy-guards design.md = `bar.close × 0.99`)대로 현재가 -0.5% 로 환원해 구조적으로 상한가를 안 넘게 함. KIS 잔고조회 500 은 재시도·예수금 캐시 폴백으로 graceful 처리 중 → 우리 버그 아님, 로그 노이즈만(5xx 소진 시 traceback→WARNING 권고). 보류된 후속안: `_compute_limit_price` 에 `min(base, 상한가 stck_mxpr)` 캡 + 상한가 근접 추격 금지 게이트 (출처: session-logs/20260616-225148-21a4-*)
- 2026-06-16: 거부 알림 폭주 dedup + 분할 폴백 기술 게이트 제거 (3 commits). **(1) 거부 알림 dedup** (commit `621be66`) — 보유가 한도(11)에 차자 매 ~2분 사이클마다 새 후보가 전부 거부되고 거부 1건당 무조건 텔레그램 발송 → 하루 **239건** 폭주(같은 종목 종일 반복). `_notify` 에 `dedup_key=(종목,사유)` 추가, **로그는 매 사이클 그대로 남기고 텔레그램만 당일 1회**, 날짜 변경 시 리셋. 사유 바뀌면 재알림. 일반 패턴은 [[notification-dedup-throttle]]. **(2) EOD 요약** (commit `84e15f6`) — dedup 으로 가려진 "한도로 보류된 후보 N종목"을 마감 스냅샷에 1건 추가. **(3) 분할 폴백 기술 게이트 제거** (commit `dd3d1b7`) — 보유 종목이 당일 스크리너 후보에 없을 때 남은 분할을 막던 40점 기술 컷(≥25)을 제거. 진입은 0/80/20(기술 폐기)인데 추가매수만 순수 기술로 막는 **철학 불일치** 탓에 펀더로 산 SK스퀘어(기술 22/40)가 무음 정체하던 것. 이제 `_can_add_split`(18시간 + 평단 -5% 드로다운 가드)·수량>0 만 통과 조건. 독립 40점 모드(백테스트)·드로다운 회복 판정의 `buy_min_score` 는 그대로. 일반 사상은 [[averaging-down-vs-momentum-add-on]]. engine 53 + strategy 120, 총 244 테스트 통과. **라이브 데몬(PID 55874)은 옛 코드 보유 → SIGTERM 후 launchd KeepAlive 재기동(PID 197, com.wooki.ht-trading)으로 반영 — 휴장 대기 중이라 매매 중단 없음** (출처: session-logs/20260616-210439-d6f6-*)
- 2026-06-03: 공휴일 휴장 판정 실패 버그 수정 (commit `2b17aba`). `bsop_date` 비교 방식이 공휴일에도 당일 날짜를 반환해 오판 → KIS 국내휴장일조회 `CTCA0903R` 의 `opnd_yn` 으로 교체 (`domestic.py:is_open_day()` 추가, `market_hours.py` 수정). 실 API 검증 + 테스트 218건 통과. 6시간째 떠 있던 라이브 프로세스가 옛 코드 보유 → launchd 재시작으로 적용. 일반 분석은 [[kis-holiday-detection-bsop-date]] (출처: session-logs/20260603-135643-1e3b-*)
- 2026-07-05 (7/4 전면 개선 세션, 커밋 20여 개 → 라이브 배포): 3 병렬 리뷰 에이전트 검토 → '높음' 교차검증 → TDD 병렬 수정 + 적대적 검증 워크플로([[parallel-review-adversarial-fix-workflow]])로 이틀간 진행. **핵심 수정**: ① 주문 제출 POST 재시도 제외 — 이중 체결 위험 + 취소 오판 시 미체결 재조회 확정 (commit e40db25/b357c14, [[order-post-retry-double-fill]]) ② 백테스트 벽시계 오염 3곳 → clock injection (commit c0ebd22/0aac79a/1d11174, [[backtest-clock-injection]]) — 재측정 거래 43→249건·+2.4%→+20%·MDD 3배 ③ 슬리피지 dead code + LIMIT 즉시체결 → 대기 큐 체결 모델 (commit ce9724a/bbf8fff/890d860/314082b, [[backtest-fill-model-adverse-selection]]) — **`limit_price_ratio` 0.995→1.005 재채택** (과거 반전 이력들은 즉시체결 구모델 하의 결정이라 근거 무효, 신모델에선 +0.5% 프리미엄이 역선택 해소. 5월에 1.005 를 포기했던 상한가 초과 거부 스팸은 6/22 거부 백오프 `on_order_submit_failed`+`max_reorder_retries_per_day` 로 종목당 하루 2~3회로 유한해져 재채택 가능) ④ 절대손절 elif dead code → Rule1a -20% 무조건 백스톱 (commit 07e473b, [[absolute-stop-loss-elif-dead-code]] — 5/19 발견 건의 실제 해결) ⑤ 일일 손실 한도가 SELL 까지 차단 → BUY 분기 내부로 (commit 2c68554) + 사이클 내 다중 매수 캐시 투영 (commit eee7098, 둘 다 [[risk-control-exemption-and-failed-attempt-accounting]]) ⑥ 주말 배포 크래시 루프 → 기동 시퀀스 연결성 오류 재시도 (commit 60a32ec, [[startup-dependency-crash-loop]]). **부수**: 일일 실현손익 영속화(record-before-reset 순서 — 지연 리셋 누산기는 기록 전에 날짜 롤오버 먼저), `_pending_fills` deque 상한(소비자 없는 버퍼 누수), WebSocket 미완성 뼈대 제거(이력은 git 에), live_engine God class 분리(동명 thin delegate 로 기존 테스트 안전망 유지), ruff 도입, 실행 주기 문서 통일(plist 2분/매수 30분). 운영 함정: 테스트 종료코드가 파이프라인에 가려 실패 상태 커밋 1회(05c6db3→6e7fffb 자가 수정, lessons 기록) (출처: session-logs/20260704-130158-2dad-*)
- 2026-07-07: 무한매수 신규 종목 0195S0 (TIGER SK하이닉스단일종목레버리지) 추가 + `max_positions` 11→12 (한도 소진 시 신규 종목 첫 매수 거부 — 종목 추가 시 동반 증설 규칙). 종목 추가 전 체크리스트 4항목 정리 (상태 저장 / max_positions / 영숫자 코드 int 가정 탐색 / warmup vs 상장 이력). 코드 수정 전 분봉 실측으로 매수 시간대 검증 → 현행 09:30 가드 + 30분 사이클 유지 (변경 불필요 결론). 분석은 [[dca-intraday-buy-timing]], 과거일 분봉 TR `FHKST03010230` 은 [[kis-minute-chart-trs]], 세션 컨텍스트로 재부상한 6/23 상대손절 벤치마크 stale price 버그는 [[relative-stop-benchmark-stale-price]] 로 분리 (출처: session-logs/20260706-214100-fe2f-*)
- 2026-07-08: "0195S0 24회 매수 거부 [APBK1497]" + "매수 시점 스코어 감사 로그" 절 추가. ① 0195S0 이 실전에서 24회 전부 거부된 원인은 코드가 아니라 계좌 파생ETF 미신청(선택확인서·레버리지 사전교육) → 신규 [[kis-derivative-etf-order-reject-apbk1497]] 분리, 종목 편입 체크리스트에 계좌 상품 권한 선결 추가. ② "매수 종목 실제 점수 62 컷 확인"이 [[n-stock-info]] 의 일자 멱등 재작성(:20/:50 cron DELETE→INSERT)으로 사후 DB 추적 불가 → `BuyCandidate.report_date` + 소수점 점수·현재가·기준일 전용 감사 로그 도입(`test_scoring_buy_audit_log.py`, 393 테스트). 2단계 컷(추천 55 vs 매수 62)·`%.0f` 반올림 은폐(`scoring_strategy.py:783`) 기록, 라이브 로그가 유일한 진실원. 멱등 vs point-in-time 일반론은 [[stock-screening-score-design]] §5 (출처: session-logs/20260707-223019-2c24-*)
