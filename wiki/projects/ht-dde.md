---
title: "ht_dde — DDE 스타일 실시간 매수후보 스캐너 + 종이거래 비교 대시보드"
domain: "personal"
sensitivity: "public"
tags: ["project", "trading", "kis", "scoring", "paper-trading", "scanner", "flask", "launchd"]
created: "2026-06-13"
updated: "2026-06-13"
sources:
  - "session-logs/20260613-164818-fc2f-신규-프로젝트를-시작하려고-하는데-지금-내가-이미-한국-투자-증권으로-API-사용해서-거래.md"
confidence: "high"
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/decisions/shared-broker-appkey-token-cache.md"
  - "wiki/analyses/launchd-daemon-vs-cron-periodic.md"
  - "wiki/analyses/stock-screening-score-design.md"
  - "wiki/bugs/kis-holiday-detection-bsop-date.md"
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
  - "wiki/patterns/test-driven-agent-loop.md"
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

## 관련 맥락

- 실거래 봇 [[ht-trading]] 의 자매 프로젝트 — 인증/토큰/도메인 모듈과 휴장 판정 패턴을 재사용하되 주문은 안 함.
- 점수 설계는 [[stock-screening-score-design]], 폴링 주기 vs 봉 간격은 [[polling-interval-vs-bar-interval]], 백테스트 봉 민감도는 [[backtest-timeframe-sensitivity]] 와 연결.

## 변경 이력

- 2026-06-13: 최초 생성 (출처: session-log 20260613-164818-fc2f). 스캐너 init → 종이거래+웹 대시보드 → launchd plist+휴장/버퍼까지 세션 1회 작업 기록.
