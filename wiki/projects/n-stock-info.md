---
title: "n_stock_info — KIS 중심 종목 스크리닝·스코어링·리포트"
domain: "trading"
sensitivity: "public"
tags: ["project", "trading", "scoring", "screening", "python", "telegram", "backtest"]
created: "2026-06-03"
updated: "2026-10-18"
status: active
tech_stack: ["python", "requests", "beautifulsoup", "sqlite", "ruff", "pytest"]
sources:
  - "session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md"
  - "session-logs/20260602-223325-f11f-지금-프로젝트를-분석해-주세요.md"
  - "session-logs/20260602-230501-585d-리포트에도-업종-PER-표시해줘.md"
  - "session-logs/20260616-210439-d6f6-오늘-알고리즘-바꾼지-2일-지났는데-2일-동안-매매에서-오류나-개선점이-없었는지-검토해줘.md"
  - "session-logs/20260620-101936-aba2-여기-페이퍼-트레이딩-대쉬보드에-몇가지-더-추가하고-싶음.md"
  - "session-logs/20260701-232403-58cb-지금까지-쌓인-데이터를-바탕으로-알고리즘-개선을-할-만한게-있는지-분석해줘.md"
  - "session-logs/20260707-223019-2c24-오늘-남성이라는-종목이-추천되서-매수가-되었는데-실제-스코어가-62-점이-넘었는지-확인해줘.md"
  - "session-logs/20260721-231701-8f9d-오늘-동작-로그좀-분석해줘-매수조건에-들어온-종목이-있는-것-같은데-매수를-하지-않은-것.md"
confidence: high
related:
  - "wiki/decisions/shared-broker-appkey-token-cache.md"
  - "wiki/projects/ht-trading.md"
  - "wiki/projects/ht-dde.md"
  - "wiki/analyses/scoring-system-ic-validation.md"
  - "wiki/analyses/scoring-version-comparison-methodology.md"
  - "wiki/analyses/eps-vs-earnings-yield.md"
  - "wiki/analyses/stock-screening-score-design.md"
  - "wiki/bugs/naver-finance-no-info-selector-drift.md"
  - "wiki/patterns/post-bugfix-reverification-data-cutoff.md"
---

# n_stock_info — 종목 스크리닝·스코어링·리포트 시스템

KIS 중심 데이터와 네이버 보조정보로 종목을 100점 만점으로 스코어링하고, 추천을 텔레그램 Daily Brief로 전송하는 Python 파이프라인. `ht_trading` 의 스코어 함수 (`analyzers/{technical,fundamental}.py`) 가 이 패키지에서 나오지만, n_stock_info 자체는 **별도 git 저장소**이며 수집→분석→리포트→저장의 독립 파이프라인을 가진다. 2026년 9~10월 기록 기준 cron으로 장중 30분마다 실행된다. 아래 기존 아키텍처·설계 절은 6~7월 이력이며, 최종 전환 상태는 「KIS 전환과 운영 점수식 복구」를 참조한다.

## 아키텍처

단방향 파이프라인. src layout, 본문 ~2,740 LOC / 테스트 ~1,347 LOC.

```
collectors(수집·HTTP 부수효과) → analyzers(계산·순수 함수) → reporters(전송) → db(저장)
main.py 가 9단계 오케스트레이션
```

- `collectors/` — market / stock / screening / research. HTTP 부수효과 격리.
- `analyzers/` — technical / fundamental / scorer (+ backtest). 순수 함수.
- `reporters/` — formatter / telegram. 출력.
- `db.py` — SQLite (`daily_reports`, `daily_candidates`).
- `models.py` — `RawStockData → 분석 → ScoredStock` 데이터 흐름.

책임 분리가 명확하고, analyzers 가 순수 함수라 백테스트·테스트가 결정적으로 가능한 것이 이 구조의 핵심 강점이다.

## 스코어링 시스템 (100점)

40(기술) + 40(기본) + 20(리서치). `min_score` 이상만 통과, 점수순 정렬 후 상위 N 종목만 리포트.

| 축 | 배점 | 의미 | 세부 |
|---|---|---|---|
| 기술 | 40 | "지금 오르는 주도주인가" (모멘텀) | 이평선 정배열 15 / 거래량 배율 10 / 120일 신고가 10 / 캔들 5 |
| 기본 | 40 | "기업 가치가 받쳐주는가" (밸류) | PER 10 / ROE 10 / EY 10 / 안정성 10 (PBR4+부채3+배당3) |
| 리서치 | 20 | "시장이 아직 안 쳐다보는가" (군중 회피) | 조회 심리 10 (**역발상**: 조회 적을수록 고점) / 애널리스트 커버리지 10 (4~10건 스윗스팟) |

설계 사상은 **모멘텀(기술) + 밸류(기본) + low-attention premium(리서치)** 조합 — 학술적으로 검증된 팩터들이다. 일반론은 [[stock-screening-score-design]] / [[scoring-system-ic-validation]] 참조.

### 가중치 변천 (40/40/20 → 50/30/20) 과 추세 게이트 (2026-06)

축별 세부 배점은 40/40/20 만점이지만 **유효 가중치는 라이브 운용 중 튜닝**됐다 (`scorer.py`: `_TECH_WEIGHT=50/40=1.25`, `_FUND_WEIGHT=30/40=0.75`, `_RESEARCH_WEIGHT=1.0` → 실효 **50/30/20**). 변천 과정 자체가 한 번의 실패 사례를 포함한다:

1. **40/40/20** (기존)
2. **0/80/20 (기술 제거) 실험** — 06-15 라이브 투입. **4일 만에 고점 대비 -19.6%**. 펀더 위주 채점이 기술점수 5~9/40 의 **하락추세 저평가주(falling knife)** 를 골라낸 게 원인. 싸 보이지만 계속 떨어지는 종목을 매수하는 전형적 함정.
3. **40/40/20 원복** → **기술 비중 상향 50/30/20** 으로 안착. 모멘텀(주도주) 신호와 정합.
4. **추세 게이트 추가** — 가중치만으론 falling knife 를 못 막아, `_passes_trend_gate`(현재가 < 20일선 종목 **veto**)로 하락추세 종목을 컷오프 단계에서 별도 제외.

> ⚠️ 주의: 이 가중치 튜닝의 백테스트 IC 근거 일부는 신뢰 불가다. 기술 IC 음수 신호는 **거래량/캔들 수집 버그(`e13765f` 이전)** 의 오염 데이터에서 나온 것 → 버그 수정 후 깨끗한 데이터로 **재검증 필요**(아래 변경 이력 e13765f 항목 참조). 가중치 비교 방법론 일반론은 [[scoring-version-comparison-methodology]] / [[averaging-down-vs-momentum-add-on]].

## 설계 판단 (2026-06-02 세션)

### 1. EPS 절대값 → Earnings Yield (EY)

기본 점수에서 EPS 절대값으로 채점하면 **주가가 비쌀수록 유리해지는 버그**가 있다 (같은 EPS 5,700원도 7만원 주식은 EY 8%, 50만원 주식은 1.1%인데 같은 점수). EY(EPS/주가)로 전환해 종목 간 비교가 가능해졌다. `fundamental.py` 주석의 "고가주 보너스 버그 수정"이 이 흔적. 일반론은 [[eps-vs-earnings-yield]].

이 세션에서는 `affd03b` 커밋이 코드 라벨을 `EPS`→`EY`로 바꿨지만 `test_formatter.py` 의 기대 문자열은 `EPS6`으로 남아 테스트 1건이 깨져 있었다. 동작 로직이 아니라 표시 문자열 검증이므로 테스트를 `EY6`으로 동기화 (commit `f611f5e`). **교훈: 라벨/표시 변경 시 같은 커밋에 테스트도 동기화** (CLAUDE.md 규칙 위반 사례).

### 2. 안정성을 가산점 → 통과 게이트로 분리

세 축을 단순 합산하면, **적자(PER 음수)·고부채 기업이라도 기술 점수가 높으면 컷오프를 통과**한다. 재무 안정성은 가산점이 아니라 필수 통과 조건이어야 한다.

`rank_and_filter` 에 하드 게이트 추가 (commit `b4e2d98`):
- 적자: `PER < 0` 또는 `EPS ≤ 0` 제외
- 고부채: `debt_ratio > max_debt_ratio` (기본 200%, config) 제외
- `None`(데이터 없음)은 **통과** → 신규상장·누락 보호
- watchlist 종목은 게이트 **우회** (명시적 사용자 의도 보존)

### 3. 섹터 상대 PER (네이버 동일업종 PER)

PER 8·ROE 20%를 전 업종에 일률 적용하면 은행(PER 5 정상)·바이오(PER 50 정상)를 같은 잣대로 봐 섹터 쏠림이 생긴다. 후보군이 거래대금 상위 30~50개로 압축돼 섹터당 1~2종목뿐이라 **후보군 내부 상대화는 표본 부족·선택 편향**으로 무의미. 대신 **네이버 종목 페이지의 "동일업종 PER"(업종 전체 평균)을 외부 벤치마크로 사용** (commit `7d6ef13`):
- collector 가 종목 페이지에서 업종명 + 동일업종 PER 수집 (`table[summary="동일업종 PER 정보"]`)
- `_score_per` 를 `종목 PER / 동일업종 PER` 비율 기준으로 (≤0.6→10점 … >1.6→1점)
- 동일업종 PER 없으면 기존 절대 기준 **fallback**
- ROE/PBR 은 네이버가 업종 평균을 안 줘서 절대 기준 유지

검증: 삼성전자 PER 8.0 vs 업종 24.13 → ratio 0.33 → 10점 (반도체 대비 저평가). KB금융 PER 9.0 vs 업종 8.41 → ratio 1.07 → 4점 (은행 평균보다 비쌈). 절대 기준이었다면 KB금융이 7점을 받았을 것. 일반론은 [[stock-screening-score-design]].

리포트에도 `PER 12.3 (업종 24.1)` 형태로 병기해 점수 근거를 노출 (commit `3b074a8`).

### 4. 백테스트 하네스 (점수 예측력 검증)

스코어 임계값들이 백테스트로 검증된 게 아니라 휴리스틱이다 ("V3 IC 검증 재설계" 커밋이 들어왔다 리버트된 이력). DB 에 쌓인 히스토리로 **추가 스크래핑 없이 결정적으로** 점수의 예측력을 측정 (commit `5a98169`):
- `db.get_all_candidate_records`: 가격이 기록된 모든 후보 행 읽기
- `analyzers/backtest.py`: 각 레코드에 대해 `horizon` 일 이상 경과한 같은 종목의 첫 미래 레코드 가격을 forward price 로 → **Rank IC**(날짜별 점수-수익률 Spearman) + **분위 스프레드**(고점수 vs 저점수 평균수익률) 를 카테고리별 계산
- `main --backtest [--horizons 7,30]`

**실제 DB(67일/562행) 결과가 두 가지를 드러냄:**
1. **강한 생존 편향** — 7일 평균 +20.7%, 승률 97~100%는 비현실적. "이후 후보로 재등장한" 종목만 측정돼, 떨어져서 탈락한 종목은 미래 가격이 없어 통째로 빠짐 (리포트에 경고 명시).
2. **점수의 음(-)의 예측력** — 종합 Rank IC -0.26(7일)/-0.38(30일), 기술 IC -0.66. 후보 풀 안에서는 현재 점수가 미래 수익을 잘 예측 못 하고 오히려 역상관. 단 날짜별 표본(1~6일)이 적어 신뢰도는 약함.

### 5. 전체 scored 종목 저장 (생존 편향 완화)

백테스트가 "재등장한 고점수 후보"만 측정해 탈락(=하락)한 종목이 표본에서 빠지는 편향을 줄이려, **필터 탈락 포함 전체 scored 종목을 저장** (commit `8ffa4e7`):
- `daily_candidates` 에 `is_recommended` 컬럼 추가 (추천=1, 측정용=0)
- 주간/월간 top 쿼리는 `is_recommended=1` 만 → 기존 리포트 의미 유지
- **날짜 단위 멱등 저장 (DELETE→INSERT)** — 시간당 cron 이 중복 누적하지 않고 일 1스냅샷
- `init_db` 자동 마이그레이션 (`ALTER ADD COLUMN DEFAULT 1`) — 기존 562행을 추천으로 백필, 데이터 손실 0

효과는 점진적 — 기존 562행은 여전히 추천만이라 신규 데이터가 쌓여야 편향이 줄어든다.

> **하류 관측성 비용 (2026-07-07 발견)**: 이 일자 멱등 저장(`:20`/`:50` cron 이 그날 `report_date` 행을 DELETE→INSERT)은 생존 편향을 없애지만 **당일 스냅샷의 point-in-time 이력을 파괴**한다. 다운스트림 [[ht-trading]] 이 09:30 에 읽은 09:20 추천(예: 남성)이 이후 실행들로 덮어써져, 사후 조회 시 그 레코드가 사라지고 그날 전 종목 `is_recommended=0` 이 됐다. → "매수된 종목의 매수 시점 점수·추천 여부"는 이 DB 로 추적 불가하며 ht_trading 라이브 로그가 유일한 진실원이다. ht_trading 은 이 때문에 매수 시점 감사 로그를 별도 도입([[ht-trading]] 「매수 시점 스코어 감사 로그」). 일반 긴장(멱등 재작성 vs 감사 재현성)은 [[stock-screening-score-design]] §5. 별개로, n_stock_info 자체 추천 컷은 `min_score=55` 이고 ht_trading 매수 컷은 62 라 경계 종목은 한쪽만 통과할 수 있다.

### 6. 데이터 기반 config 튜닝 — 진짜 병목은 컷오프

리포트 종목이 적어 `max_report_count`(10) 상한을 늘리려 했으나, DB 분석 결과 **67일 중 10개 상한에 걸린 날이 0일** (일 평균 추천 0.5개, 추천 종목 점수가 65점 컷 바로 위 65~75에 몰림). 즉 진짜 병목은 상한이 아니라 `min_score`(65) 컷오프. `min_score` 65→55 인하가 추천을 늘리는 실질 레버이고, `max_report_count` 10→30 은 안전장치, screening 30→50 은 풀 확대 (commit `9c915f4`). **교훈: "상한을 늘릴까"가 아니라 실제 분포를 보고 병목을 찾는다.**

### 7. 재검증 도구의 오염 데이터 하한 필터 (2026-07-01)

06-16 버그 수정(`e13765f`) 후 "깨끗한 데이터 ~2주 누적 뒤 가중치 재검증"(D-8/F-1)의 예정 시점이 도래. 그런데 재검증에 쓸 두 도구(`get_all_candidate_records`, `scripts/compare_weights.py`)가 **날짜 하한이 없어 06-16 이전 오염 행(~849개, volume=0으로 technical_score 왜곡)을 그대로 다시 포함**했다. 지금 `--backtest`/`compare_weights.py`를 돌리면 2주 대기의 목적이 무력화되는 함정.

- 분석자는 운영 머신에만 있는 DB(gitignore, 로컬 부재)에 접근 못 해 수치는 못 돌렸지만, **코드만 읽고 "재검증 도구에 날짜 필터가 없다"는 결함을 확정**하고 도구를 먼저 고침 (계측기 선수정 → 오염된 근거로 판단 굳는 것 방지).
- 수정 (commit `57153e5`): `get_all_candidate_records(db_path, since_date=None)` → `WHERE report_date >= ?`; `main.py --backtest`에 `--since ISO_DATE` 플래그(전달+로깅); `compare_weights.py`는 **기본값 `SINCE=2026-06-16`(버그 수정일)** 로 재검증이 오염 없이 기본 동작; `test_db`에 하한 필터 검증 추가. 173 테스트 통과, ruff 통과.
- 일반 원칙(버그 수정 + 데이터 축적 대기 + **평가 도구의 수정일 이후 필터** 셋이 다 있어야 재검증 성립, 컷오프는 도구에 디폴트로 박기)은 [[post-bugfix-reverification-data-cutoff]] 로 분리.

### 8. 유동성 게이트가 만드는 "추천 0종목" — 고점수 마이크로캡 배제 (2026-07-21)

[[ht-trading]] 라이브 로그 분석("매수조건에 들어온 종목이 있는데 매수를 안 했다")의 원인 추적. `rank_and_filter`(scorer.py)의 4단계 필터를 **전부** 통과해야 `is_recommended=1` 이 된다:

① `min_score` 컷 → ② stability gate (적자·고부채 제외, §2) → ③ trend gate (하락추세 veto, §가중치 변천) → ④ **liquidity gate — `min_market_cap: 900`(억)·`min_trade_value: 200`(억), config screening 섹션**

- 실측(07-21): 자이에스앤디는 total_score **80.25** 로 점수·안정성·추세를 모두 통과하고도 liquidity gate 미달로 `is_recommended=0`. 오리엔트바이오(73.25)는 손실기업(PER·EPS 음수)이라 stability gate 탈락. 그날 18회 실행 전부 "Filtered to 0 candidates".
- ht_trading 스크리너의 "기준일=N/A" 표시는 DB 누락이 아니라 **오늘자 `is_recommended=1` 만 매수 대상으로 읽는 설계**의 결과 (stale 매수 방지, screener.py — 어느 게이트에서든 탈락하면 그날 추천 레코드가 없다).
- 판단: 고점수 마이크로캡 배제는 유동성 보호로 의도된 설계일 수 있으나, 900억/200억 임계가 **추천 공급 자체를 0으로 만드는 날이 반복**되면 §6과 같은 방식으로 실제 분포를 보고 임계를 재검토해야 한다 — 병목이 점수 컷이 아니라 유동성 컷일 수 있다.

## 현재 상태

- 테스트 173 passed(07-01), ruff 통과.
- 이번 세션 커밋: `f611f5e`(라벨 동기화) → `b4e2d98`(안정성 게이트) → `7d6ef13`(섹터 상대 PER) → `3b074a8`(리포트 업종 PER) → `5a98169`(백테스트 하네스) → `8ffa4e7`(전체 scored 저장) → `7ccf9fc`/`2111d04`(CLAUDE.md 문서) → `9c915f4`(config 튜닝).
- 미구현/관찰 대상: 신규 데이터 누적 후 `--backtest` 재측정 (편향 줄어든 IC 확인), 55점 컷이 품질을 떨어뜨리는지 검증.

## 배운 것

- **단순 합산은 치명적 결함을 가린다** → 안정성은 게이트로 분리. ([[stock-screening-score-design]])
- **절대 임계값은 섹터 편향** → 외부 벤치마크(동일업종 PER)로 상대화. 후보군 내부 상대화는 표본·선택 편향으로 함정.
- **백테스트 생존 편향** — 승자(고점수 재등장)만 DB 에 남으면 수익률이 부풀고 IC 가 왜곡. 전체 universe 저장이 선결.
- **숫자를 바꾸기 전에 분포를 본다** — 상한이 아니라 컷오프가 병목이었던 사례.
- EPS vs EY 같은 도메인 지식은 [[eps-vs-earnings-yield]] 로 분리.

## KIS 전환과 운영 점수식 복구 (2026-09~10)

### 수집 장애와 데이터 역할

9월 11일부터 구 네이버 URL이 Npay 페이지로 이동했다. HTTP 200이어도 구형 `table.type_2`·지수 선택자가 없어 후보가 0건이 됐고, 마지막 정상 데이터는 9월 10일이었다. cron 실행 성공과 데이터 수집 성공은 다르다. 필수 응답 형식 불일치·전 후보 수집 실패를 정상적인 추천 0건과 구별해야 한다.

9월 28일 KIS 공식 API로 후보 순위·지수·시세·일봉·재무·투자의견·추정실적 수집을 전환했다. Npay 리포트 제목·원문 링크·조회 수는 보조 수집으로 격리했다. KIS 인증·토큰 캐시는 ht_trading과 공유한다([[shared-broker-appkey-token-cache]]). 단일 소스 실패를 다른 소스 값이나 0점으로 조용히 대체하지 않고 실패 사유를 남긴다.

### 점수식 변경과 최종 복구

초기 `kis-naver-v2`는 유효 가중치 50/30/20을 유지했지만 리서치를 KIS 신호 18점·네이버 조회 수 2점으로 바꿨다. 과거 리포트 수는 변수명과 달리 최근 30일이 아니라 최신 최대 30개였으며, 기간 정의까지 달라졌다. 배당·동일업종 PER 누락과 재무 기준기간 차이도 분포 이동에 섞였다.

10월 3일 최종 복구에서는 KIS 시세·일봉·재무를 유지하며 기존 조회 수 10점·리포트 수 10점 리서치식을 운영 `stock_info.db`에 연결했다. 재무 보완에는 EPS의 최근 12개월 환산, ROE·부채비율의 확정 연간 기준 대조가 포함됐다. 팬오션은 가중 기술 38.75·재무 19.50·리서치 12.00으로 70.25점이 됐고, ht_trading 스크리너가 실제 매수 후보로 읽는 것까지 확인했다. 복구 전 61.75점과의 차이는 리서치 7점·재무 1.5점이었다.

운영 추천 컷 55와 실매수 컷 62는 별개이며, 최종 실매수 컷은 62를 유지했다. 점수 상승은 수익성 개선의 증명이 아니다. 과거 재무·조회 수를 현재 값으로 소급 재구성하지 않고 버전·시점을 나눠 검증한다([[scoring-version-comparison-methodology]]).

### 네이버 비교군의 의미 변화

9월 29일 비교는 후보 발굴만 KIS/Naver로 다르고 상세 입력·점수식은 KIS로 통일한 실험이었다. 따라서 그 결과를 네이버 자체 데이터 품질 비교로 읽으면 안 된다. 10월 3일에는 독립 네이버 시세·재무·장기 일봉 수집과 기존 점수식을 추가했다. `source_comparison.db`와 `score_audit.db`의 비교 관측은 운영 DB와 분리하며, 비교 기능 완료와 실제 매수 경로 복구 완료를 구별한다.

### 수집 주기와 알림 보안

30분마다 수집·DB 갱신을 유지하되 텔레그램은 당일 첫 실행, KIS/Naver 최종 추천 집합 변경, 17:50 마감 실행에만 보낸다. 운영 Daily Brief와 그림자 비교 요약을 구분하고, 전체 후보 목록은 대시보드에 둔다.

HTTP 성공 요청 로그는 `httpx/httpcore` WARNING 이상으로 제한하고 Telegram URL의 인증 부분을 오류 로그에서도 마스킹했다. 로그 마스킹은 기존 노출 토큰의 폐기·교체를 대신하지 않는다. 당시 토큰 교체는 보류됐고 기존 로그도 변경하지 않았다.

### 검증 경계

세션에 기록된 최종 오류 수정 검증은 n_stock_info 215개·ht_trading 568개·ht_dde 호환성 251개 테스트 통과다. 이번 wiki 수집에서 실제 서비스·외부 API를 재검증하지 않았다. 과거 「현재 상태」의 173개 테스트 수와 네이버 중심 아키텍처는 7월 시점 기록이다.

## 최종 추천 불변 스냅샷 생산자 연결 (10/17 후속)

최종 추천 네 조합의 페이퍼 비교를 위해 소스별 실제 추천·정책·수집 완료 시각·완전성을 불변 이력에 추가하고 기존 일별 저장과 원자적으로 기록했다고 보고했다(`b3c0ed1`). 수집 실패와 정상 추천 0건을 구분하며, 일별 덮어쓰기 데이터만으로 과거 추천을 재구성하지 않는다.

운영 추천·알림 정책은 유지하고 ht_dde 소비자가 새 이력에서 각 소스 최종 추천 중 자체 점수 62점 이상을 읽도록 연결했다. 생산자 연결·저장 실패 복원 테스트는 보고됐으나 10/19 첫 정상 수집은 확인 전이다. 비교 계약은 [[scoring-version-comparison-methodology]], 장부·대시보드는 [[ht-dde]]를 참조한다.

## 변경 이력

- 2026-10-18: 최종 추천 불변 스냅샷 생산자와 원자 저장·첫 정상 입력 대기 기록 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-12: KIS 수집 전환·운영 기존식 복구·독립 비교군·알림 정책의 최종 상태를 보강 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-07-22: §8 "유동성 게이트가 만드는 추천 0종목" 추가 — 4단계 필터(점수→안정성→추세→유동성) 중 liquidity gate(시총 900억·거래대금 200억)가 80.25점 종목(자이에스앤디)도 탈락시켜 18회 연속 "Filtered to 0 candidates", ht_trading "기준일=N/A"는 is_recommended 필터 설계의 결과. 임계 재검토는 분포 기반(§6 사상) (출처: session-logs/20260721-231701-8f9d-*)
- 2026-07-08: §5 에 "하류 관측성 비용" 절 추가 — 일자 멱등 저장(DELETE→INSERT)이 당일 point-in-time 이력을 파괴해, 다운스트림 [[ht-trading]] 이 매수한 종목(남성)의 매수 시점 추천 스냅샷이 사후 소실됨(그날 전 종목 is_recommended=0). ht_trading 이 매수 감사 로그를 별도 도입한 배경. 자체 추천 컷 55 vs ht_trading 매수 컷 62 의 2단계 컷 관계 명시 (출처: session-logs/20260707-223019-2c24-*)
- 2026-07-02: "재검증 도구의 오염 데이터 하한 필터" 절 추가 (2026-07-01 세션). 06-16 버그 수정 후 재검증 예정 시점 도래, 재검증 도구가 날짜 하한이 없어 오염 행 재포함 함정 → `since_date`/`--since`/`compare_weights.py` 기본 SINCE=2026-06-16 추가(commit `57153e5`, 173 테스트). DB 접근 없이 코드만으로 계측기 결함 확정·선수정. 신규 [[post-bugfix-reverification-data-cutoff]] 분리 (출처: session-logs/20260701-232403-58cb-*)
- 2026-06-20: **가중치 변천 + 추세 게이트** 절 추가 — 유효 가중치가 40/40/20 → (0/80/20 실험 4일 -19.6% falling knife 실패) → **50/30/20** 으로 안착, `_passes_trend_gate`(현재가<20일선 veto)로 하락추세 종목 컷오프. IC 근거 일부는 e13765f 이전 오염 데이터라 재검증 필요. 별 프로젝트 [[ht-dde]] 의 종이거래로 가중치 변형을 A/B 비교하려는 방향이 제안됨(이 세션은 검토 단계). (출처: session-logs/20260620-101936-aba2-*)
- 2026-06-17: 거래량·시가·등락률 수집 버그 수정 (commit `e13765f`). 네이버 `item/main` 페이지의 `table.no_info` 가 라벨을 `<th>` 가 아니라 `<span class="sptxt">` 에 두도록 구조가 바뀌어, 코드의 `table.select("th")` 가 항상 빈 결과 → **2026-03 이후 전 종목·전 날짜 거래량=0·시가=null·등락률 +0.0%** 가 silent 하게 수집됨. 등락률은 별도로 `.no_exday em span.blind` 의 `select_one` 이 첫 em(전일대비 금액)을 잡아 `%` em 에 도달 못 하던 버그. **가짜로 만든 테스트 fixture(`<th>거래량</th>` + 단일 em)가 회귀를 못 잡았고** volume/open/change 도 미검증이었다. `_no_info_values` 헬퍼 도입 + fixture 를 실제 마크업으로 교체 + 추출 필드 전부 단언. 166 테스트 통과. 영향: 0/80/20 라이브 추천엔 무영향(기술 가중 0)이나, DECISION 의 가중치 IC 표가 망가진 기술 데이터 산출이라 재검증 전 재측정 필요. 일반 교훈은 [[naver-finance-no-info-selector-drift]] (출처: session-logs/20260616-210439-d6f6-*)
- 2026-06-03: 최초 작성. 2026-06-02 세션 2건 (프로젝트 분석 + 업종 PER 표시) 에서 추출 — EPS→EY 라벨 동기화, 안정성 게이트, 섹터 상대 PER, 백테스트 하네스, 전체 scored 저장, 데이터 기반 config 튜닝. 일반 사상은 [[eps-vs-earnings-yield]] / [[stock-screening-score-design]] 로 분리 (출처: session-logs/20260602-223325-f11f-*, 20260602-230501-585d-*)
