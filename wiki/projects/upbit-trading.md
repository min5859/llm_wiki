---
title: "upbit_trading — 암호화폐 무한매수법 자동매매"
domain: "trading"
sensitivity: public
tags: ["project", "trading", "crypto", "upbit", "infinite-buy", "dca", "trailing-stop"]
created: "2026-05-10"
updated: "2026-06-07"
sources:
  - "session-logs/20260510-194933-8c5e-최근-몇일간-로그를-분석해서-수익율을-더-높이기-위한-전략을-추천해-주세요.md"
  - "session-logs/20260515-231744-34b6-지금-python3.12-가-문서-폴더의-파일에-접근하려고-합니다.-라는-팝업창이-계속-뜨.md"
  - "session-logs/20260606-210943-6534-아래-개선사항을-검토-•-PAUSED가-6시간-이상-지속되면-텔레그램-알림을-보내도록-하면.md"
confidence: high
related:
  - "wiki/analyses/dca-trailing-stop-tuning.md"
  - "wiki/analyses/backtest-timeframe-sensitivity.md"
  - "wiki/bugs/round-winrate-exit-type-undercount.md"
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
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
| ⑤ | 추세 필터 모두 OFF | `b694198`, `661a80f` 두 커밋으로 `use_trend_filter`/`use_btc_trend_filter` 비활성. 하락장 무방비 DCA. **(2026-06-06 재검증)** 30분봉 백테스트에서는 OFF가 오히려 수익률 우세 → OFF 유지가 합리적. MDD 방어가 필요할 때만 ON 고려 ([[backtest-timeframe-sensitivity]]) |

## 중기 백로그 (F·G)

| ID | 변경 | 비고 |
|----|------|------|
| **F** | `use_dynamic_coin_selection: True` (`trading_config.py:129`) | 이미 구현, OFF 만 돼 있음. 메이저 1~2 + dip 후보. 블랙리스트 (SOL/ADA/DOGE/XRP) 유지 |
| **G** | `btc_24h_drop_threshold: -3.0 → -5.0` + `use_btc_trend_filter: True` | 너무 자주 차단됐던 추세 필터 완화 재도입. 패닉 매도 차단 효과만 유지 |

## 2026-06-06 개선 검토 — PAUSED 알림 / volume 게이트 / 추세필터 재검증 후 OFF 유지

사용자 제안 4건(PAUSED 장기화 알림, 하락추세+데드크로스 DCA 제동, 하락추세+거래량 약세 DCA 제동, 매수액 절반)을 코드와 대조 검토. 핵심 발견: **추세 기반 DCA 방어(`_get_downtrend_level`)는 이미 구현돼 있으나 `infinite_buy.use_trend_filter: False`로 꺼져 있었다** (직전 커밋이 회전율 개선을 위해 의도적으로 OFF). 제안 ②는 신규 구현이 아니라 config 토글 문제였다.

| 제안 | 처리 | 내용 |
|------|------|------|
| ① PAUSED 6h 알림 | **신규 구현** | `trader.pause()`/`resume()` 메서드로 캡슐화 + `_paused_since` 추적. 사이클 스킵이 `paused_alert_hours`(기본 6h) 초과 시 텔레그램 경고 **1회** 발송 (의도치 않은 정지 조기 포착). 다른 제안과 독립 |
| ② 하락추세+SMA20하회+데드크로스 제동 | **config 토글** | 이미 있던 `_get_downtrend_level` (L1 약한하락=쿨다운×2, L2 강한하락=×4+신규진입 차단) 을 켜는 문제. 신규 코드 불필요 |
| ③ 하락추세+거래량 약세 DCA 제동 | **신규 구현** | `volume_ratio < low_volume_threshold(0.5)` 이고 하락추세면 쿨다운에 `low_volume_cooldown_multiplier(1.5)` 추가 배율 (예: 약한하락 24h → 36h). `test_low_volume_dca_brake` 추가. **추세필터 종속** — `use_trend_filter: False` 면 함께 무력화 |
| ④ 매수액 절반 | **제외** | `record_buy` 가 `buy_count` 를 무조건 +1 해 절반 매수해도 40분할 1회 소진 → "1회 건너뛰기"(쿨다운 연장)와 중복 |

→ 1차 적용 (commit `c1162af`)에서 ①②③ 반영하며 `use_trend_filter: True` 로 켰다.

### 백테스트 재검증 → 30분봉(운영 봉)에서는 OFF가 유리 → 최종 OFF 복원

켜기 결정 후 추세필터 ON/OFF를 동일 OHLCV 주입으로 공정 비교 (`compare_trend_filter.py` 신규, commit `9bf8d9d`). **봉 간격에 따라 결론이 뒤집혔다**:

- 4시간봉: 하락장 ON 압승(+7~10%p), 상승장 OFF 우세
- **30분봉(실제 운영 봉): 두 구간 모두 OFF가 수익률 우세** (30분봉 SMA/데드크로스 노이즈 큼 → 매수 늦추면 단기 반등 놓침)

→ **수익률 우선이므로 `use_trend_filter: False` 로 복원** (직전 커밋 방향이 30분봉 기준 옳았음). PAUSED 알림(①)과 승률 버그 수정은 이 결정과 무관하게 확정. 단 MDD는 모든 봉에서 ON이 낮음(방어적 설정). 검증 방법론·표 전체는 [[backtest-timeframe-sensitivity]] 참조.

> 검증 도중 리포트 **승률 집계 버그**(종료 유형 분류 누락 → 승률 0%)도 발견·수정 (commit `b947351`). [[round-winrate-exit-type-undercount]] 참조.

### launchd 봇 당분간 중지

최종 결정: OFF 유지 + **당분간 매매 정지**. `launchctl unload` + `~/Library/LaunchAgents/com.wooki.upbit-trading.plist` **symlink 제거**(원본 `config/*.plist` 보존)로 재부팅 자동 시작까지 차단. 같은 머신의 `com.wooki.ht-trading` 은 건드리지 않음. 영구 비활성화 절차는 [[launchd-plist-symlink-from-project]] 참조.

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

## 운영 잡음 — 갑작스러운 ~/Documents TCC 팝업 (2026-05-15)

5/10 튜닝 커밋 직후 launchd 로 재기동된 라이브 봇 (PID 97336, `--strategy infinite_buy`) 이 5/15 자로 처음 "python3.12 가 문서 폴더의 파일에 접근하려고 합니다" 토스트 팝업을 띄우기 시작. 동일 봇이 70일 + 5일을 정상 가동하다 갑자기 발생한 케이스.

추적 결과:
- 용의 프로세스의 인터프리터 경로가 `/opt/homebrew/.../Python.framework/.../Resources/Python.app/Contents/MacOS/Python` — macOS TCC 가 이 번들 인터프리터를 "python3.12" 로 인식
- 다른 후보 (ht_trading 의 python3.13, hermes 의 python3.11) 는 버전이 달라 제외
- upbit_trading 코드 자체에는 `Documents` / `expanduser` / `Path.home()` 참조 없음 — 의존성 라이브러리 (matplotlib·HF·OpenAI·telegram-bot 류 캐시) 가 HOME traverse 중 새 게이트에 걸린 것으로 추정
- 결정적 시그널: 시스템 `/Library/Application Support/com.apple.TCC/TCC.db` 가 5/15 10:00:01 정시에 자동 변경 — Apple 백그라운드 정책 푸시 흔적 (XProtect / 보안 정책 자동 동기화)
- `fs_usage` / `tccd` 로그 추적에서는 정확한 호출 라이브러리 미특정 (TCC 거부된 syscall 이 fs_usage 에 안 잡힘)

봇 동작 자체는 영향 없음 (의존성이 EACCES 를 흡수). 실용적 대응은 시스템 설정 → 개인정보 보호 → 파일 및 폴더 → `python3.12` 의 "문서 폴더" 토글을 거부로 두는 것 — 봇이 ~/Documents 를 정상 동작에 필요로 하지 않으므로 부작용 없음.

> 진단 절차 (5단계) + 갑작스러운 발생 원인 ①② 의 일반 사상은 [[macos-tcc-documents-popup-diagnosis]] 참조.

## 변경 이력

- 2026-05-10: 최초 작성 (session-logs/20260510-194933-8c5e). 70일 운영 분석 + 튜닝 A~E 즉시 적용 + 백로그 F/G + DB 스키마 + 진단 패턴 5건 정리. 일반 사상은 [[dca-trailing-stop-tuning]] 으로 분리
- 2026-05-16: 5/15 자 ~/Documents TCC 팝업 잡음 관찰. 시스템 TCC.db 의 10:00:01 자동 변경 + Python.app 번들 인터프리터 인식 결합으로 추정. 봇 동작에는 영향 없음 (의존성 EACCES 흡수). 진단 절차는 [[macos-tcc-documents-popup-diagnosis]] 로 분리 (출처: session-logs/20260515-231744-34b6-*)
- 2026-06-07: 개선 검토 4건 → ①PAUSED 6h 텔레그램 알림 신규(`pause()`/`resume()`+`_paused_since`), ③하락추세+거래량약세 DCA 쿨다운 추가배율 신규(`low_volume_*`), ②추세필터는 config 토글 문제(이미 구현됨)임을 확인, ④매수액 절반은 분할 소진 중복으로 제외 (commit `c1162af`). `use_trend_filter: True` 로 켰다가 ON/OFF 백테스트 공정 비교 → **30분봉(운영 봉)에서는 OFF가 수익률 우세**로 결론, `False` 복원. 검증 중 라운드 승률 집계 버그 수정(`b947351`). 최종 OFF + launchd 봇 당분간 중지(symlink 제거). 일반 지식은 [[backtest-timeframe-sensitivity]] / [[round-winrate-exit-type-undercount]] / [[launchd-plist-symlink-from-project]] 로 분리 (출처: session-logs/20260606-210943-6534-*)
