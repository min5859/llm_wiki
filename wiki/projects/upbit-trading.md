---
title: "upbit_trading — 암호화폐 무한매수법 자동매매"
domain: personal
sensitivity: public
tags: ["project", "trading", "crypto", "upbit", "infinite-buy", "dca", "trailing-stop"]
created: "2026-05-10"
updated: "2026-05-10"
sources:
  - "session-logs/20260510-194933-8c5e-최근-몇일간-로그를-분석해서-수익율을-더-높이기-위한-전략을-추천해-주세요.md"
  - "session-logs/20260515-231744-34b6-지금-python3.12-가-문서-폴더의-파일에-접근하려고-합니다.-라는-팝업창이-계속-뜨.md"
confidence: high
updated: 2026-05-16
related:
  - "wiki/analyses/dca-trailing-stop-tuning.md"
  - "wiki/analyses/macos-tcc-documents-popup-diagnosis.md"
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/partial-sell-rule-idempotency.md"
---

# upbit_trading — 암호화폐 무한매수법 자동매매

Upbit API 기반 암호화폐 DCA 자동매매. Python + APScheduler + launchd 로 가동. `--strategy infinite_buy` 모드로 라이브 운영 중이며 `rule_based` 는 paper/백테스트 기본값.

## 시스템 구조

```
strategies/
├── base_strategy.py
├── infinite_buy_strategy.py   # 무한매수법 (40분할 DCA + Trailing Stop)
└── rule_based_strategy.py     # 룰베이스 (paper/백테스트 기본)
core/
├── trader.py                  # 전략 객체 생성·실행 루프
└── monitors/
    └── trailing_monitor.py    # 5분 간격 trailing stop 모니터
services/
├── upbit_service.py           # Upbit REST API 클라이언트
├── database_service.py        # SQLite (trades/positions/signals/infinite_buy_rounds)
└── telegram_service.py        # 텔레그램 알림·명령
analyzers/
├── rule_engine.py             # 룰 평가
└── adversarial_analyzer.py    # AI 매도 신호 (DeepSeek)
config/
└── trading_config.py          # infinite_buy 서브 dict 가 라이브 파라미터
```

라이브 가동: macOS launchd (`~/Library/LaunchAgents/com.wooki.upbit-trading.plist`, `--strategy infinite_buy` 명시). 30분 주기 거래 사이클 + 5분 trailing 모니터 + 06:00 일일 리포트.

## 무한매수법 (Infinite Buy DCA) 구조

### 라운드 단위 운영

- **라운드** = 첫 매수 → 목표 익절·트레일링 청산까지의 한 사이클
- **40분할** = 라운드당 최대 40회 추가 매수 (DCA, 1회분 95,400 원)
- **재시작 복원** = DB `infinite_buy_rounds` 테이블에서 활성 라운드와 trailing 고점 (`peak_price`) 을 init 시 자동 복원

### DCA 트리거 분기

| 분기 | 조건 | 동작 |
|------|------|------|
| `infinite_buy_first` | 활성 라운드 없음 | 라운드 1 시작 첫 매수 |
| `infinite_buy_dca` | 현재가 ≤ 평단 | 평단 이하 추가 매수 (n/40) |
| `infinite_buy_wait` | 현재가 > 평단 | HOLD |
| `infinite_buy_cooldown` | 직전 매수 후 쿨다운 미만 | HOLD |
| `infinite_buy_trailing_stop` | 고점 -trailing% 도달 | 라운드 청산 |
| `infinite_buy_partial_profit` | +4%/+6% 단계 도달 | 부분 매도 (30%) |
| `infinite_buy_target` | 목표 익절 % 도달 | 라운드 청산 |

### 운영 현황 (2026-05-09 기준 70일)

- 완료 라운드: 10건, **평균 +5.20%**
- 모두 trailing_stop 에서 청산 (고점 -2.0%)
- 미청산 2건: KRW-BTC R3 (2/40, 5/7~), KRW-ETH R1 (28/40, 4/15~, 24일째 자본 약 267만 묶임)

## 설정 튜닝 (2026-05-10 적용, commit `e3b8...`)

70일 운영 분석 → 5개 키 변경. 모두 즉시 적용 + launchd 재기동.

| 항목 | 변경 | 위치 | 근거 |
|------|------|------|------|
| **A** | trailing 2.0% → **2.5%** | `trading_config.py:71` | BTC R2 고점 119,995,000 → +5.36% 갱신 후 +3.83% 청산. 6%대 이상 못 잡음 |
| **B** | buy_cooldown_hours 12 → **6** | `trading_config.py:68` | DCA 회전율 ↑, 라운드 평균 보유 단축 |
| **C** | max_round_days 90 → **45** + 계단식 임계 15/30/45 | `trading_config.py:76`, `infinite_buy_strategy.py:146-153` | ETH R1 같은 24일째 자본 묶임 차단. 계단식 30/60/90 → 15/30/45 정합 |
| **D** | partial_profit ON, levels [(4.0, 0.3), (6.0, 0.3)] | `trading_config.py:85-86` | 일찍 끊긴 3~4% 라운드도 30% 확정익 확보 |
| **E** | tighten_on_weakness ON | `trading_config.py:80` | 데드크로스 + SMA20 하회 시 trailing 거리 ÷ 2 |

D·E 는 코드 주석에 "paper 모드 1~2일 관측 필수" 로 표시되어 있었으나, 사용자 결정으로 즉시 라이브 적용. 추후 관찰 권장 항목:

- `infinite_buy_partial_profit` 트리거 빈도 (ETH 1라운드 28/40 에서 4% 회복 시 첫 시험대)
- trailing 2.5% 로 5%대 청산 → 6%대로 늘어나는지
- 시간 탈출 트리거 시 손실 폭 (계단식 압축 영향)

> 일반 튜닝 사상은 [[dca-trailing-stop-tuning]] 참조.

## 진단된 수익 저해 패턴 (5건)

| # | 패턴 | 근거 |
|---|------|------|
| ① | trailing 너무 타이트 (2%) | 고점 +5.36% 갱신 후 +3.83% 청산 (BTC R2). 6%대 못 잡음 |
| ② | 부분 익절 미사용 | 라운드 길어질수록 평단 회복만 노리고 확정익 0 |
| ③ | 자본 묶임 | ETH 24일째 28/40, 평단 회복 대기로 신규 라운드 진입 불가 |
| ④ | 종목 2개 분산 부족 | `use_dynamic_coin_selection: False`, BTC/ETH 만. 한 코인 보합 시 50% 자본 정지 |
| ⑤ | 추세 필터 모두 OFF | `b694198`, `661a80f` 두 커밋으로 `use_trend_filter`/`use_btc_trend_filter` 비활성. 하락장 무방비 DCA |

## 중기 백로그 (F·G)

| ID | 변경 | 비고 |
|----|------|------|
| **F** | `use_dynamic_coin_selection: True` (`trading_config.py:129`) | 이미 구현, OFF 만 돼 있음. 메이저 1~2 + dip 후보. 블랙리스트 (SOL/ADA/DOGE/XRP) 유지 |
| **G** | `btc_24h_drop_threshold: -3.0 → -5.0` + `use_btc_trend_filter: True` | 너무 자주 차단됐던 추세 필터 완화 재도입. 패닉 매도 차단 효과만 유지 |

## DB 스키마

```sql
trades (id, timestamp, ticker, action, amount, price, volume, profit_percent, reason, trade_type)
positions (id, ticker, entry_time, entry_price, exit_time, exit_price, volume, profit_percent, profit_amount, exit_reason, status)
infinite_buy_rounds (id, ticker, round_number, total_splits, amount_per_split, buy_count, total_invested, started_at, last_buy_at, completed_at, profit_percent, status, peak_price, consumed_partial_levels)
signals (id, timestamp, ticker, decision, confidence, reason, blue_team_decision, ..., consensus_score, target_ratio, stop_loss, take_profit, executed)
market_snapshots, portfolio_snapshots
```

`infinite_buy_rounds` 의 `peak_price` 와 `consumed_partial_levels` 가 launchd 재시작 후에도 트레일링·부분익절의 상태를 복원하는 핵심 컬럼.

## 알려진 함정

- **config 변경은 Trader init 시에만 로드**. 변경 즉시 `launchctl kickstart -k gui/$(id -u)/com.wooki.upbit-trading` 으로 재기동 필요. 재시작 후 `InfiniteBuyStrategy 초기화: ...` 한 줄에서 모든 변경 키 (cooldown, trailing, max_round_days, Tightening, PartialProfit) 가 반영됐는지 확인
- **사전 실패 테스트 2건** (`test_infinite_buy.py` 의 `재시작 시 쿨다운 유지` / `InfiniteBuyStrategy 로직`) 은 변경 전부터 존재. 회귀 아님
- 라이브 봇 재시작 직후 30~60초간 reconciliation·token cache 로드 출력 후 첫 거래 사이클 진입

## 변경 이력

- 2026-05-10: 최초 작성 (session-logs/20260510-194933-8c5e). 70일 운영 분석 + 튜닝 A~E 즉시 적용 + 백로그 F/G + DB 스키마 + 진단 패턴 5건 정리. 일반 사상은 [[dca-trailing-stop-tuning]] 으로 분리
