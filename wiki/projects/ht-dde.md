---
title: "ht_dde — DDE 스타일 실시간 매수후보 스캐너 + 종이거래 비교 대시보드"
domain: "trading"
sensitivity: "public"
tags: ["project", "trading", "kis", "scoring", "paper-trading", "scanner", "flask", "launchd"]
created: "2026-06-13"
updated: "2026-07-09"
sources:
  - "session-logs/20260709-230951-58a3-현재까지-동작-결과-검토해줘-표면적으로만-보지-말고-가능성과-인사이트까지-감안해서-검토해줘.md"
  - "session-logs/20260704-215721-8c43-현재-프로젝트는-최적의-주식-자동매매-알고리즘을-찾는-것이-목표임-이-목표에-도달하게-위해.md"
  - "session-logs/20260704-093146-5338-현재-프로젝트-분석.md"
  - "session-logs/20260701-231304-df33-지금까지-돌린-페이퍼트레이딩-알고리즘-평가-쉽게-설명.md"
  - "session-logs/20260630-080924-22d3-지금까지-돌린거-알고리즘들-평가해줘.md"
  - "session-logs/20260613-164818-fc2f-신규-프로젝트를-시작하려고-하는데-지금-내가-이미-한국-투자-증권으로-API-사용해서-거래.md"
  - "session-logs/20260620-101936-aba2-여기-페이퍼-트레이딩-대쉬보드에-몇가지-더-추가하고-싶음.md"
  - "session-logs/20260706-220340-ac9d-아래와-같은-알고리즘을-셋업하고-싶은데-어떻게-하면-될까---최근-일주일간-지수대비-덜떨어.md"
confidence: "high"
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/projects/n-stock-info.md"
  - "wiki/analyses/scoring-version-comparison-methodology.md"
  - "wiki/analyses/backtest-timeframe-sensitivity.md"
  - "wiki/analyses/signal-overfit-date-dispersion-check.md"
  - "wiki/analyses/dca-trailing-stop-tuning.md"
  - "wiki/bugs/equity-curve-max-vs-latest-aggregation.md"
  - "wiki/decisions/shared-broker-appkey-token-cache.md"
  - "wiki/analyses/launchd-daemon-vs-cron-periodic.md"
  - "wiki/analyses/stock-screening-score-design.md"
  - "wiki/bugs/kis-holiday-detection-bsop-date.md"
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
  - "wiki/patterns/test-driven-agent-loop.md"
  - "wiki/analyses/optimal-strategy-search-preconditions.md"
  - "wiki/patterns/mirror-config-drift-guard-test.md"
  - "wiki/bugs/absolute-stop-loss-elif-dead-code.md"
  - "wiki/bugs/pykrx-krx-login-required.md"
  - "wiki/bugs/naver-finance-news-referer-required.md"
---

# ht_dde — DDE 스타일 실시간 매수후보 스캐너

증권사 HTS 의 DDE+엑셀 수식으로 만들던 "장중 실시간 보조지표 점수판"을, 한국투자증권(KIS) REST API 기반 파이썬 스캐너로 옮긴 개인 토이 프로젝트. 거래대금/거래량 상위 종목을 자동 수집 → 종목별 거래량증가율·체결강도·VWAP 이격·호가불균형·시가대비·고점눌림을 계산 → YAML 점수식으로 채점·랭킹. 이후 **실거래 없이 종이거래(paper trading)로 3개 전략을 동시에 돌려 수익률을 비교**하는 웹 대시보드까지 확장. 실거래 봇 [[ht-trading]] 과 같은 KIS 계정을 쓰지만 주문 API 는 일절 호출하지 않는다.

## 출발점 — DDE+엑셀 점수판의 API 이식

원 아이디어는 "HTS 의 DDE 로 실시간 시세를 엑셀에 끌어와 거래량·체결강도·호가잔량 등을 수식으로 조합해 점수화하고, 그 조건이 실제로 수익 확률이 높은지 반복 검증한다"는 흔한 단타 워크플로우. 핵심은 *좋아 보이는 수식*이 아니라 **검증**(과거 재현·시간대별·수수료/슬리피지 반영·충분한 표본)이라는 점. ht_dde 는 이 워크플로우의 점수식·반복튜닝·로그누적을 코드로 구조화했다.

## 아키텍처

- **universe** — `volume-rank`(FHPST01710000) 로 거래대금·거래량 상위 + 거래량증가율(`vol_inrt`)을 한 번에 수집 (동적 유니버스)
- **indicators** — 종목별 지표를 **순수 계산 함수**로 분리 (단위테스트 용이). 시세 조회 결과를 받아 파생지표(거래량증가율·시가대비·고점눌림·VWAP 이격·호가불균형) 계산
- **scoring** — 룰/등급을 전부 `config/scoring.yaml` 에서 읽어 평가 (코드 수정 없이 임계값·가중치 튜닝)
- **scanner / cli** — 1회 또는 `--loop N` 초 간격 반복 스캔, `--top N` 콘솔 출력, 매 스냅샷을 `data/scan_log.csv` 에 누적 (나중 "점수 N → t분 후 수익률" 검증 재료)
- **paper/** (2차) — `portfolio`(가상계좌, 수수료/증권거래세/슬리피지 반영) · `exits`(청산 규칙) · `strategy`(전략 로드) · `store`(SQLite 매매일지·평가곡선·포지션) · `engine`(폴링+진입/청산 오케스트레이션)
- **web/** (2차) — Flask + Chart.js 대시보드. 평가곡선·카드 비교·포지션·매매일지 + **전략별 점수식·진입/청산 파라미터를 그대로 렌더링**해 개선 포인트를 눈으로 비교
- **app.py** — 엔진(백그라운드 스레드) + Flask 를 한 프로세스에서 동시 기동 (`--no-web`/`--web-only` 로 분리 가능)

## 핵심 설계 판단

1. **점수식 YAML 외부화** — 글의 "반복 관찰로 보정"하는 워크플로우를 그대로 살리려 룰·임계값·배점을 전부 `scoring.yaml`/`strategies/*.yaml` 로 빼서 코드 수정 없이 튜닝. 점수 설계의 구조적 함정(게이트 vs 가산점·섹터 상대화·생존편향)은 [[stock-screening-score-design]] 참고.
2. **지표 계산 = 순수함수 + test-first** — 단위테스트를 먼저 작성하고 실 API 연결 전에 통과시킴 ([[test-driven-agent-loop]]). 최종 27개 통과.
3. **타임프레임 독립(현재 스냅샷 기반)** — 단타/종가 어느 쪽이든 실험 가능하도록 지표 엔진을 타임프레임에 묶지 않음.
4. **토큰 캐시 공유** — 실거래 [[ht-trading]] 과 같은 KIS appkey 라, ht_dde 는 **새 토큰을 발급하지 않고 ht_trading 의 토큰 캐시 파일을 공유**해 분당 1회 발급 제한·초당 호출 제한 충돌을 회피 → [[shared-broker-appkey-token-cache]].
5. **실거래 없는 종이거래 모니터링** — 시세는 읽기만, 주문 API 호출 0. 가상계좌로 매매일지·실현/평가손익·평가곡선을 SQLite 에 실시간 누적.
6. **3개 전략 동시 비교** — 공격형/균형형/보수형이 같은 후보 풀에서 각자 점수화·진입·청산. 지표는 **종목당 1회만 조회해 셋이 공유**(API 부하 1배 유지).
7. **상시 데몬 → launchd** — 웹서버+폴링 루프가 계속 떠 있어야 하므로 cron 이 아닌 launchd KeepAlive 로 운영 → [[launchd-daemon-vs-cron-periodic]]. plist 는 프로젝트 폴더 마스터 + LaunchAgents symlink ([[launchd-plist-symlink-from-project]]), 포트는 AirPlay 충돌 회피로 5050.

## 구현 중 발견·해결

- **체결강도(cttr)는 `inquire-price` 에 없다** — `inquire-price`(FHKST01010100) 응답의 `cttr` 가 None. 체결강도는 별도 엔드포인트 `inquire-ccnl`(FHKST01010300) 의 `output[0]['tday_rltv']` 에 있어 별도 조회를 추가. (VWAP=`wghn_avrg_stck_prc`, 시가/고가/누적거래대금 등은 `inquire-price` 에 존재. 호가 총잔량은 `inquire-asking-price-exp-ccn` FHKST01010200.)
- **공휴일/임시휴장 판정** — 초기엔 주말·시간창만 거르고 공휴일을 못 걸렀음. KIS 개장일 조회(`chk-holiday`, CTCA0903R)로 하루 1회 판정하도록 보완 (ht_trading 의 [[kis-holiday-detection-bsop-date]] 패턴 재사용). 장 시작 5분 전~마감 5분 후 버퍼(`warmup_min`/`cooldown_min`), 15:20 부터 단타 전량청산. 2026-06-13(토)→휴장, 06-12(금)·06-15(월)→개장 실측 확인.
- **localhost:5000 안 보임** — 원인은 단순히 "앱이 안 떠 있었음". 추가로 macOS 는 **포트 5000 을 AirPlay Receiver 가 점유**하는 경우가 많아 5050 으로 변경. 백그라운드(`&`)로 띄우고 터미널을 닫으면 같이 죽는 점도 주의(→ 상시는 launchd).

## 검증·운영 상태

- 단위테스트 27개 통과. 실거래 데이터로 end-to-end 스캔·점수·CSV 로그·종이거래·웹 API(`/`, `/api/overview|equity|trades|positions`) 모두 정상.
- 장중 시각 시뮬레이션에서 공격형 4종목·균형형 1종목·보수형 0종목으로 의도대로 분기.
- launchd 등록 완료(symlink + `launchctl load`, PID 구동, `휴장` 판정 시 idle).
- 커밋: `56b57b7`(스캐너 init) → `628558d`(종이거래+웹 대시보드) → `5435c47`(launchd plist + 휴장/버퍼).

## 미해결 — API rate limit 우려

3전략 동시 비교 자체는 종목당 1회 조회 공유라 부하가 늘지 않지만, **ht_trading 과 appkey 를 공유**하는 게 위험. 1회 폴링당 호출 ≈ 거래량순위 1 + 종목당 3 × top_n 60 ≈ 181건. ht_dde throttle 은 초당 ~16건(11초 몰아치고 19초 쉼). KIS 실전 계좌는 appkey당 **초당 ~20건** 제한이라 ht_dde 단독은 안전하나, ht_trading 호출과 겹치면 합산 20 초과로 둘 다 `EGW00201 초당 거래건수 초과` 가 날 수 있다. 완화안: ① throttle 0.06→0.1초(초당 10건) ② 폴링 30→60초 ③ **사전 필터** — 거래량순위가 주는 값으로 1차 컷 후 통과 종목만 상세 3건 조회(60→15~25종목, 호출 60~70% 감소). 적용 범위 결정 대기 중. (rate-limit 충돌 회피의 일반 논의는 [[shared-broker-appkey-token-cache]].)

## 제안된 방향 — 스코어링 가중치 A/B 종이거래 (2026-06-20 검토)

[[n-stock-info]] 의 종목 선정 가중치(현재 실효 50/30/20)를 **약간씩 변형한 변종을 ht_dde 종이거래로 동시 구동해 알고리즘을 최적화**하려는 방향이 제기됨. n_stock_info 는 라이브에서 0/80/20 실험이 4일 만에 -19.6%(falling knife) 났던 전력이 있어, 실거래 위험 없이 가중치 변형을 검증할 무위험 테스트베드로 ht_dde 의 "3전략 동시 비교" 구조가 적합하다. 이 세션은 검토 단계로 구현은 미착수. 가중치 변천 상세는 [[n-stock-info]], 비교 방법론은 [[scoring-version-comparison-methodology]].

## 리웨이트 A/B 구현 & 첫 평가 (2026-06-30)

6-20 에 검토만 했던 "가중치 변형 A/B 종이거래"가 실제로 구현·구동됐다. `reweight/` 모듈(`weights`·`sim`·`live`·`engine`·`store`·`source`) + 별도 `data/reweight.db` + 전용 대시보드(`reweight.html`)·launchd 잡(`reweight_daily.sh`)로 분리. 변형 5종을 정의하고 **일봉 시뮬(SIM)**과 **장중 실시간 페이퍼(LIVE)** 두 경로로 동시에 돌린다.

- variants(min_score 62 공통): `baseline` 50/30/20 · `tech_heavy` 60/20/20 · `balanced` 40/40/20 · `tech_mid` 55/25/20 · `research_up` 40/30/30 (기술/기본/리서치 가중).

**평가 결과 — 전 전략 손실.** 시드 1천만원 기준 최신 자본(`MAX(equity)` 아닌 최신 ts 기준 → [[equity-curve-max-vs-latest-aggregation]]):

- **스캐너 페이퍼**(06-15~06-29, 5전략): 전부 마이너스. conservative -1.26%(최선) · scalp_breakout -1.58% · balanced -1.77% · breakout -4.69% · **aggressive -19.95%(최악)**. 공격형은 매도 560건 승률 30%로 거래는 많지만 회전당 평균 -3,971원으로 잃었다.
- **리웨이트 LIVE**(장중, 06-22~06-29): 전부 마이너스. **baseline 50/30/20 -3.38%(최선)** · balanced -3.93% · research_up -4.10% · tech_mid -7.75% · **tech_heavy 60/20/20 -10.07%(최악)**. 손실은 거의 전부 `절대손절 -10%`대 청산에 집중.
- **리웨이트 SIM**(일봉, 06-17~06-25): **balanced 40/40/20 만 +403K(승률 44.4%)**, 나머지(tech_heavy·tech_mid·baseline·research_up)는 -47K~-231K.

**판단/교훈:**
1. **일봉 SIM 과 장중 LIVE 가 전략 순위를 뒤집는다** — SIM 유일 흑자였던 balanced 가 LIVE 에선 중위, SIM 하위였던 baseline 이 LIVE 1위. 운영이 장중 폴링이므로 **LIVE 결과가 기준**이고 SIM 순위로 고르면 안 됨 → 일반화는 [[backtest-timeframe-sensitivity]].
2. **기술가중 과다(tech_heavy 60/20/20)가 LIVE 최악** — 기본/리서치를 줄일수록 하락장 추격매수 위험↑. [[n-stock-info]] 의 0/80/20 falling-knife 전력과 같은 방향. 이번 구간에선 **현행 baseline 50/30/20 이 가장 덜 잃어** 가중치 변경 근거가 약함.
3. **표본·구간 한계** — 평가 구간(2주 안팎)이 하락 우세장이라 전 전략 마이너스. "어느 변형이 덜 잃나"는 보이지만 알파 유무 판정엔 표본 부족. A/B 우열은 [[scoring-version-comparison-methodology]] 식 정량 비교(IC·구간 robustness)로 재검증 필요.
4. **집계 함정 실측** — 평가 중 `MAX(equity)` 로 자본을 뽑아 aggressive 가 +2.3% 처럼 보였으나 최신 ts 기준 -19.95% → [[equity-curve-max-vs-latest-aggregation]] 로 분리 기록.

## 2차 평가 & 신호 발굴 (2026-07-01)

06-30의 "전 전략 손실" 진단을 이어받아, ① 어떤 알고리즘이 튜닝 여지가 큰지 ② 쌓인 스냅샷에서 새 알고리즘 아이디어가 나오는지를 파고든 세션. 약 13거래일(6/15~7/1) 결과 기준.

**판단 1 — 자주 매매할수록 더 크게 잃는다 (거래비용 드래그).** 스캐너 공격형은 687매매·수수료 113만원(자본의 11%)으로 -25.8% 손실의 절반 가까이가 순수 거래비용. 반대로 거의 안 건드린 보수형이 가장 덜 잃음. 회전율 자체가 손실원 → 공격형은 튜닝해도 회생 불가 구조.

**판단 2 — 리웨이트 baseline(50/30/20)이 최선이자 튜닝 여지 최대.** 성적(-0.4%, 승률 58% 전체 최고)뿐 아니라 **구조** 때문:
- **단일 튜닝 레버가 명확**: 평균이익 6.0만 vs 평균손실 9.2만 → payoff 0.65(손실>이익). 손실의 거의 전부가 **−10% 절대손절 8건(−74만원)** 에 집중. 손절폭을 −10%→−5~6%로 좁히면 평균손실이 반토막→payoff>1, 승률 58%면 **산술적으로 흑자 전환**. 감이 아니라 데이터가 가리키는 단일 개선점. (일반 튜닝 레버는 [[dca-trailing-stop-tuning]]; "payoff<1 + 높은 승률 → 손절 조이면 흑자"는 재사용 가능한 진단.)
- 저빈도 스윙(19매매)이라 수수료 함정 없음.
- **실거래 [[ht-trading]]과 로직 동일** → 여기서 튜닝한 결과가 바로 실전 이전. 스캐너 스캘핑은 실거래 미사용이라 튜닝 이득이 페이퍼에 갇힘.
- 튜닝 순서 추천: 손절폭(−10%→−5~6%) 먼저, 다음 트레일링 진입 시점(현재 +3%).

**판단 3 — 새 알고리즘 아이디어: 스냅샷 라벨에서 EOD 복합필터 발굴.** `snapshots`(약 12만 행, `fwd_5m/30m/eod` 라벨)에서 지표 예측력을 분석 → **단일 지표는 예측력 사실상 0**, 조합에서만 엣지. 오후(12시+)+VWAP위+시가위+체결강도 100~130 필터가 당일종가(EOD) 기준 시장(-0.37%)을 이기고 **10거래일 중 7일 초과**로 재현. 단, 여기에 거래량증가율 300+를 더한 필터는 성과가 급등(+0.6%)하지만 **이틀에만 몰려 과최적화**. 신호와 과최적화를 가르는 방법(날짜 분산 + 시장 상대 대조 + overfit funnel)은 [[signal-overfit-date-dispersion-check]]로 일반화.

> 06-30 대비 이번 회차의 새 결론: (a) 손실이 −10% 절대손절에 집중된다는 payoff 분해 → 손절폭이 유일·최우선 레버, (b) 스냅샷 라벨이 신호 발굴 데이터셋이며 EOD 복합필터가 실재 신호 후보.

## afternoon_eod 구현 & 심층 코드 분석 (2026-07-04)

**07-01 발굴한 EOD 복합필터가 6번째 전략 `afternoon_eod` 로 실전화됐다** (07-02 커밋). 오후 12:00~15:20 에만 진입(전략 yaml의 `trade_window` 선언을 엔진 `_can_enter`/`_force_close` 가 해석 — 엔진 무수정 확장점), VWAP위+시가위+체결강도 100~130 세 규칙을 각 1점 × `min_score: 3` 으로 **가산점의 필수조건화**(AND 필터). 익절/트레일링은 검증 대상인 fwd_eod 엣지를 잘라내지 않도록 의도적으로 배제하고 -8% 재난 손절만 둠. 체결강도 150+ 는 과열(승률 최저)이라 상단 130 컷. 일반화는 [[holding-period-signal-mismatch]].

리웨이트 쪽도 진행: 변형 `research_up` 40/30/30 추가, 장중 폴링 5분→2분(실거래 ht_trading 정합), 차트 제자리 갱신(update none)으로 깜빡임 제거.

**병렬 서브시스템 분석(워크플로 8 에이전트)에서 확인·발견된 것들** (분석 결과 기반, 수정은 미착수):

- `trade_window` 경계 겹침 — `_force_close` 는 `>= end`(15:20 포함) 강제청산인데 `_can_enter` 는 `<= end` 로 같은 분에 진입 허용 → 15:20 정각에 청산 직후 재진입 가능성.
- 스캔 루프의 예외 내성 부족 — `scan_once` 가 KISAPIError만 캐치해 재시도 소진 후의 HTTPError/ConnectionError/JSONDecodeError 는 프로세스 전체를 죽인다. rate limit 이 `rt_cd≠0`(EGW00201)로 오면 백오프 없이 종목 스킵 → 연쇄 누락 ([[shared-broker-appkey-token-cache]] 에 일반화).
- 토큰 캐시 쓰기 비원자적(write_text 직접 덮어쓰기, 락 없음) → 경합 시 불필요 발급 리스크 (상동).
- universe 의 `rank_by` 재정렬은 volume-rank API 가 이미 자른 집합 안에서만 작동 — 실질 선정 기준을 바꾸지 못하고, API 반환이 top_n 미만이면 유니버스가 조용히 축소.
- 콜 예산 실측: 종목당 3콜 × 60 + 유니버스 1콜 ≈ 181콜, min_interval 0.1초 기준 스캔 1회 ≥ 약 18초. `cli` 의 sleep 이 스캔 소요를 차감하지 않아 실제 주기 = loop + 스캔시간.
- 설계 확인(의도대로): 지표 전략 간 공유(코드당 2콜) + 호가는 결정에 영향 주는 종목만 지연조회(`_needs_orderbook`, scoring 의 None=불통과 전제 위에 성립), `max_hold_minutes` 는 거래시간 분 기준, SQLite WAL 로 엔진 쓰기·웹 읽기 동시성, 재시작 복구는 DB 단일 진실원.

## 목표 지향 재정렬 & 실거래 미러링 (2026-07-04 저녁)

"최적 알고리즘 찾기"라는 목표 자체를 5개 주제로 재검토한 세션. 상위 방법론(목적함수·레짐·counterfactual 하네스·비용 우선 탐색)은 [[optimal-strategy-search-preconditions]] 로 분리.

- **가중치 실험 종결 → 규칙 실험으로 교체**: 5개 가중치 변형의 5일 중앙값이 전부 -8.6% 동일 — 컷 근처 후보가 2~3개뿐이라 어떤 가중치로도 순위가 안 뒤집히는 재가중 no-op 확인 ([[scoring-version-comparison-methodology]] "재가중 무효 조건"). 변형 4개 은퇴(이력 보존·포지션 동결) 후 슬롯을 규칙 실험으로 재활용: `no_surge15`/`no_surge20`(급등 제외)·`ma_ext30`(이격 상한), 대조군 baseline 유지, 변형별 판정 기준 사전 등록.
- **급등 꼭지 편향 실측**: 추천 당일 +15% 급등 픽 8개의 5일 중앙값 -20.6% vs 일반 픽 -3.6%. 기술점수 구조상 급등 순간 만점(보해양조 40/40 = 상한가 당일)이라 가중치로 못 고치고 하드 게이트 필요 — 추세 게이트(>MA20)는 급등주를 오히려 잘 통과시키는 상방 사각 ([[stock-screening-score-design]] §4).
- **실거래 손절 체계 미러링**: ht_trading 의 Rule1a -20% 절대 백스톱 + Rule1b 상대손절 -10% 3층 체계를 페이퍼에 미러링 ([[absolute-stop-loss-elif-dead-code]]). 페이퍼의 기존 -10% 무조건 손절과의 괴리가 하락장 손익곡선 분기의 핵심 원인이었음 (실거래는 -19% 종목 보유 지속, 페이퍼였다면 컷). 매수 리듬 괴리(실거래 30분/3분할 vs 페이퍼 2분 일괄)도 확인. 미러 드리프트는 `tests/test_mirror_drift.py` 가 원본 `scoring.yaml` 을 직접 읽어 자동 감시, 의도적 단순화는 allowlist ([[mirror-config-drift-guard-test]]).
- **point-in-time 이력 부재 발견**: n_stock_info 의 당일 후보 멱등 재작성이 "산 순간의 명단"을 지워 체결 정합 불가 — 멱등 저장 vs 감사 재현성의 긴장 ([[stock-screening-score-design]] §5).
- 운영 함정 재확인: 서비스 재기동 누락으로 7/2 기동 프로세스(PID 52124)가 7/4 커밋 미반영 상태로 가동 중이던 것 발견·재기동 (ht-trading 계열의 반복 함정).

## 주간 상대강도(RS) EOD 스캐너 신설 (2026-07-06)

"최근 일주일간 지수 대비 덜 빠지거나 오른 종목 + 이유가 분명한 종목" 을 찾는 **EOD(종가) 스캐너를 신규 모듈 `src/ht_dde/rs/` 로 추가**. 기존 장중 스캐너(KIS REST)와 달리 **데이터 소스를 pykrx(KRX EOD) 로 의도적으로 분리** — 날짜당 전 종목 1콜 + 지수 1콜로 끝나 KIS rate limit 과 무관하고, **과거 일봉은 불변**이므로 `data/krx_cache/` 영구 캐시로 재실행이 빠르다 (DART 종목코드 매핑도 동일 캐시). KRX 로그인 의무화 함정은 [[pykrx-krx-login-required]].

### 3개 알고리즘 (임계값은 전부 `config/rs.yaml`)

1. **주간 상대강도 (weekly_rs)** — `RS = 최근 5거래일 종목 수익률 − 소속 지수(KOSPI/KOSDAQ) 수익률` 내림차순. 필터: 평균 거래대금 10억 미만·우선주·스팩 제외.
2. **하락일 역행 (contrarian)** — 지수 −0.5% 이상 하락일에 거래량 20일 평균 2.0배 이상 + 등락률 ≥ 0 인 종목을 "역행" 카운트, 반복 횟수순 (동률은 거래량 배율). 실측에서 하락일 2회 역행 + 거래량 72배 종목 등 포착.
3. **실적 공시 후 눌림 (earnings_dip)** — DART 거래소 공시에서 최근 14일 내 "영업(잠정)실적" 종목 수집 → 공시 직전 거래일 종가 대비 수익률이 지수보다 낮은 종목. 실측: 파라다이스 (−6.31%p, 뉴스 "주가는 신저가, 실적은 사상 최대")·셀트리온 (−5.67%p).

### 설계 판단

- **"이유가 분명한가" 는 자동 판정하지 않는다** — 후보별 DART 공시 + 네이버 뉴스 헤드라인을 첨부해 사람이 판단. 둘 다 없으면 "이유 불분명, 수급만일 가능성 주의" 경고. 실적 YoY 판정도 1단계는 사람, 본문 파싱·컨센서스 서프라이즈는 난이도순 유보. 네이버 뉴스 크롤링의 Referer 필수 함정은 [[naver-finance-news-referer-required]].
- **순수 계산과 I/O 분리** — `rs/scanner.py` 는 네트워크 없음 (합성 DataFrame 으로 테스트), `rs/data.py` 가 pykrx 래핑 + 거래일 캘린더 + 캐시.
- **EOD 페이퍼 트레이딩** (`rs/paper.py`) — 3개 알고리즘을 독립 전략으로 당일 종가 매수 (종목당 자본 10%, 최대 10종목, 전략당 가상자본 1천만원), 종가 기준 트레일링 스탑 (+3% 발동 → 고점 −4% 청산, 발동 전 고정손절 −5%), 비용 반영 (수수료 0.015%·거래세 0.15%·슬리피지 0.1%), **날짜 멱등** (같은 날 재실행 스킵), `data/rs_paper.db`. `/rs` 웹 페이지 + launchd 자동 스캔 연결.
- **판정 기준 사전 등록** — "20거래일 후 전략별 KOSPI 대비 초과수익으로 유지/폐기" 를 첫날에 명시 ([[optimal-strategy-search-preconditions]] §5 pre-registration).
- **append-only CSV 스키마 진화 대응** — earnings 알고리즘 추가로 누적 CSV 가 13→15 컬럼이 되며 `pd.read_csv` `ParserError` 발생. 기존 파일은 일회성 마이그레이션 (구 스키마 행에 빈 값 삽입), 이후 헤더 불일치 시 `.bak` 회전 후 새 파일 시작하도록 `cli._append_csv` 보강.

## 4주 동작 검토 & Infinity 버그 & vol_surge 슬롯 (2026-07-09)

세 서브시스템(스캐너·RS·리웨이트)의 누적 산출물을 표면이 아니라 검증 데이터로 검토.

### 결론 — 엔지니어링은 건강, 매매는 전부 손실 (그리고 그 이유가 데이터로 확정됨)

112개 테스트 통과·정직한 시뮬레이션(갭 관통 체결·사전 판정기준·선택편향 제거 스냅샷)은 신뢰할 만하다. 그러나 실제 성과는 전 서브시스템 손실: 스캐너 aggressive **−28.3%**(807매매)~conservative −1.4%(19거래일), RS weekly_rs −13.2%(4거래일·승률 5~7%), 리웨이트 8변형 전부 손실(±0.05%p ≈ 0). 표면의 "시장이 나빴다" 가 아니라 **검증 데이터가 "핵심 신호의 예측력이 음(−)"임을 4주 연속 일관 증명**한 것이 최대 발견.

- **스코어가 역(逆)신호**: 점수↑ → +30분 수익률·승률 단조 감소(점수5 = −0.27%/승률35%), 06-18/06-25/07-02/07-09 4주 재현. 점수0·점수5는 같은 유니버스·시각에서 뽑혀 시장 베타가 상쇄되므로 이 음수 차이는 시장 방향 무관 = **모멘텀 추격 신호가 단기 평균회귀**. 상세·방법론은 [[scoring-system-ic-validation]] 의 "라이브 out-of-sample 2차 확증" 절.
- **유일한 엣지가 합산에 희석**: 거래량증가율만 양(+)(400%↑ +1.01%/승률64% n=58). 6지표 1점 합산이 이 하나를 노이즈 5개로 평균 내 죽인다. → **`vol_surge` 단일 규칙 슬롯 신설**(config/strategies/vol_surge.yaml, paper.yaml 로스터 등록). 전역 진입가드 `max_day_change_pct: 7.0` 이 겹쳐 실제로는 "거래량 400%↑ AND 당일등락 <7%" 를 테스트. 판정 기준(20거래일 KOSPI 대비)을 todo.md 에 사전 등록.
- **가중치 미세조정 은퇴 확정**: 리웨이트 8변형 5일 초과수익 전부 ±0.05%p → 50/30/20 유지, 규칙 실험으로 축 이동(07-05 판단의 2차 확증).
- **RS 는 판정 전**: 4거래일·급락장(07-08 코스피 −5.35%) 시작이라 −13% 로 폐기하면 성급. 다만 **매일 새로 뽑아 종가 매수**하는 능동 회전이라 매일 새 "꼭지"를 사는 구조 + 종목이 전부 저가 소형주 → 되돌림. 유동성/최소주가 필터 + 지수 필터 필요. 20거래일 판정까지 유지.

### `/rs` 빈 화면 버그 — 응답 JSON 의 Infinity

"어제까진 되던" `/rs` 추천 3표가 전부 공백. 원인은 오늘 스캔에 직전 20일 평균거래량 0 종목(금호에이치티)이 들어와 `거래량/0 = inf` → `max_vol_ratio:Infinity` 가 `/api/rs/latest` 응답에 실림 → 브라우저 `response.json()`(JSON.parse)이 거부 → `render()` 미실행. Flask `jsonify` 는 `allow_nan` 기본 True 라 curl·Python 확인 시엔 정상으로 보여 오진하기 쉬움. 2겹 방어(scanner: avg=0 → NaN, server: 응답 직전 inf→"") + 회귀 테스트 + launchd kickstart 재기동. 상세·일반 교훈은 신규 [[flask-jsonify-infinity-breaks-browser-json]].

### 실거래 매핑

검토 결론을 실거래 두 프로그램에 매핑: **선정 = [[n-stock-info]], 매매 실행 = [[ht-trading]]**. "고점수·고체결강도 추격" 로직이 실거래에 있으면 역엣지 의심, 가중치 튜닝은 중단 권고. "지금 실거래에 바로 넣을 돈 버는 신호는 없고, 4주치가 확정한 건 대부분 '하지 말 것'" 이 정직한 결론.

## 관련 맥락

- 실거래 봇 [[ht-trading]] 의 자매 프로젝트 — 인증/토큰/도메인 모듈과 휴장 판정 패턴을 재사용하되 주문은 안 함.
- 종목 스코어링 본체 [[n-stock-info]] — ht_dde 가 검증하려는 가중치/점수식의 출처.
- 점수 설계는 [[stock-screening-score-design]], 폴링 주기 vs 봉 간격은 [[polling-interval-vs-bar-interval]], 백테스트 봉 민감도는 [[backtest-timeframe-sensitivity]] 와 연결.

## 변경 이력

- 2026-07-09: "4주 동작 검토 & Infinity 버그 & vol_surge 슬롯" 절 추가 — 세 서브시스템 전부 손실이나 원인이 "스코어 역예측(4주 재현)"임을 검증 데이터로 확정, 거래량증가율 유일 양(+)이라 `vol_surge` 단일규칙 슬롯 격리 신설(20거래일 사전등록), 가중치 미세조정 은퇴·RS 소형주 되돌림 구조 진단. `/rs` 빈 화면 = 응답 JSON 의 `Infinity`(vol/0) → 브라우저 JSON.parse 실패, 2겹 방어로 수정. 신규 [[flask-jsonify-infinity-breaks-browser-json]] 분리, [[scoring-system-ic-validation]] 라이브 2차 확증 보강 (출처: session-logs/20260709-230951-58a3-*)
- 2026-07-02: "2차 평가 & 신호 발굴 (2026-07-01)" 절 추가. ①과매매=거래비용 드래그(공격형 수수료 113만=자본 11%) ②리웨이트 baseline이 최선+튜닝여지 최대(payoff 0.65·손실이 −10% 절대손절 8건에 집중 → 손절폭 −10%→−5~6%가 유일·최우선 레버, 승률 58%면 산술적 흑자전환) ③스냅샷 라벨에서 EOD 복합필터(오후+VWAP위+시가위+체결100~130) 발굴, 단일지표는 예측력 0·조합만 엣지. 신규 [[signal-overfit-date-dispersion-check]](날짜분산+시장대조로 과최적화 판별) 분리, [[dca-trailing-stop-tuning]] 교차링크 (출처: session-logs/20260701-231304-df33-*)
- 2026-07-04: "afternoon_eod 구현 & 심층 코드 분석" 절 추가 — 07-01 발굴 EOD 필터의 실전화(trade_window 확장점·가산점 필수조건화·검증 엣지 보호 청산 설계), 워크플로 분석의 발견(trade_window 경계 겹침, 스캔 루프 예외 내성, 토큰 캐시 비원자 쓰기, universe rank_by 실질 무효, 콜 예산 181콜/스캔 ≥18초). 신규 [[holding-period-signal-mismatch]] 분리 (출처: session-logs/20260704-093146-5338-*)
- 2026-07-05: "목표 지향 재정렬 & 실거래 미러링" 절 추가 — 재가중 no-op 확인 후 가중치 실험 은퇴·규칙 실험(no_surge/ma_ext) 교체, 급등 꼭지 편향 실측(-20.6% vs -3.6%), 실거래 3층 손절 미러링 + 드리프트 가드 테스트, point-in-time 이력 부재. 신규 [[optimal-strategy-search-preconditions]]·[[mirror-config-drift-guard-test]] 분리, [[scoring-version-comparison-methodology]]·[[stock-screening-score-design]] 보강 (출처: session-logs/20260704-215721-8c43-*)
- 2026-06-13: 최초 생성 (출처: session-log 20260613-164818-fc2f). 스캐너 init → 종이거래+웹 대시보드 → launchd plist+휴장/버퍼까지 세션 1회 작업 기록.
- 2026-06-20: "제안된 방향 — 스코어링 가중치 A/B 종이거래" 절 추가. [[n-stock-info]] 가중치 변형을 무위험 검증하는 테스트베드로 ht_dde 활용 검토(미착수). n-stock-info 와 상호 링크 (출처: session-logs/20260620-101936-aba2-*)
- 2026-07-07: "주간 상대강도(RS) EOD 스캐너 신설" 절 추가 — 신규 모듈 `rs/` (3 알고리즘: weekly_rs·contrarian·earnings_dip), pykrx EOD 채택 + 불변 일봉 영구 캐시, 이유 점검은 공시·뉴스 첨부 후 사람 판단, EOD 페이퍼 트레이딩 (날짜 멱등·판정 기준 사전 등록), append-only CSV 스키마 진화 대응 (마이그레이션 + `.bak` 회전). 신규 [[pykrx-krx-login-required]]·[[naver-finance-news-referer-required]] 분리, [[optimal-strategy-search-preconditions]] §5 (pre-registration) 보강 (출처: session-logs/20260706-220340-ac9d-*)
- 2026-06-30: "리웨이트 A/B 구현 & 첫 평가" 절 추가. 6-20 검토안이 `reweight/` 모듈 + reweight.db + 전용 대시보드/launchd 로 구현돼 변형 5종을 SIM(일봉)·LIVE(장중) 동시 구동. 첫 평가는 전 전략 손실(스캐너 aggressive -19.95% 최악, 리웨이트 LIVE baseline 50/30/20 -3.38% 최선·tech_heavy -10.07% 최악, SIM balanced 만 +403K). 교훈: 일봉 SIM≠장중 LIVE 순위 역전·기술가중 과다=하락장 최악·표본부족. [[backtest-timeframe-sensitivity]]·[[equity-curve-max-vs-latest-aggregation]] 신규/보강 (출처: session-logs/20260630-080924-22d3-*)
