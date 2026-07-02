---
title: "데이터 품질 버그 수정 후 재검증 — 평가 도구도 수정일 이후로 필터해야"
domain: "trading"
sensitivity: public
tags: ["pattern", "data-quality", "backtest", "verification", "trading", "pipeline", "silent-contamination"]
created: "2026-07-02"
updated: "2026-07-02"
sources:
  - "session-logs/20260701-232403-58cb-지금까지-쌓인-데이터를-바탕으로-알고리즘-개선을-할-만한게-있는지-분석해줘.md"
confidence: high
related:
  - "wiki/projects/n-stock-info.md"
  - "wiki/bugs/naver-finance-no-info-selector-drift.md"
  - "wiki/analyses/scoring-system-ic-validation.md"
  - "wiki/analyses/scoring-version-comparison-methodology.md"
---

# 데이터 품질 버그 수정 후 재검증 — 평가 도구도 수정일 이후로 필터해야

수집·계산 계층의 데이터 품질 버그를 고치고 **"깨끗한 데이터가 1~2주 쌓이면 재검증한다"**는 계획은, **평가 도구가 수정일 이후 데이터만 쓰도록 필터하지 않으면 무력화된다.** 오래된 오염 행이 조용히 다시 섞여 들어가 2주를 기다린 이유가 사라진다.

## 사례 — n_stock_info 스코어 재검증

- 배경: `e13765f`(2026-06-16)에서 네이버 HTML 파싱 버그를 수정 → 그 전까지 `volume=0`으로 **technical_score가 오염**돼 있었다 ([[naver-finance-no-info-selector-drift]]). 결정 로그(D-8/F-1)는 "수정 후 깨끗한 라이브 데이터를 ~2주 쌓고 50/30/20 가중치를 재검증"으로 계획.
- 함정: 재검증에 쓸 두 도구(`db.get_all_candidate_records`, `scripts/compare_weights.py`)가 **날짜 하한이 없어 전체 행을 조회**했다. 지금 `--backtest`나 `compare_weights.py`를 돌리면 F-1이 경고한 **~849개 오염 행(06-16 이전)이 그대로 섞인다.** 2주 대기의 목적이 통째로 무력화.
- 진짜 개선은 파라미터 튜닝이 아니라 **검증 파이프라인이 깨끗한 데이터만 쓰게 만드는 것**:
  - `get_all_candidate_records(db_path, since_date=None)` — `WHERE report_date >= ?` 추가
  - `main.py --backtest`에 `--since ISO_DATE` 플래그(전달 + 로깅)
  - `compare_weights.py`는 **기본값 `SINCE=2026-06-16`(버그 수정일)** — 재검증 전용 스크립트이므로 운영자가 잊어도 오염이 안 섞이도록 디폴트로 컷

## 일반 원칙

1. **"버그 수정 + 데이터 축적 대기"는 절반짜리다.** 나머지 절반은 "평가 도구가 수정일 이후로 필터"다. 셋이 다 있어야 재검증이 성립.
2. **컷오프 날짜는 재검증 도구 안에 디폴트로 박아라.** 운영자가 매번 `--since`를 기억해서 붙이는 구조는 언젠가 빠진다. 오염 데이터를 섞는 실수가 "기본 동작"이 되면 안 된다.
3. **오염 데이터는 지우지 말고 경계로 격리한다.** 과거 행을 삭제하는 대신 날짜 하한으로 걸러야, 오염 전/후 비교나 다른 분석에 원본을 남길 수 있다.
4. **데이터에 접근 못 해도 계측기는 고칠 수 있다.** 이 세션에서 분석자는 운영 머신에만 있는 DB(gitignore)에 접근 못 했지만, **코드만 읽고 "재검증 도구에 날짜 필터가 없다"는 결함을 확정**하고 도구를 먼저 고쳤다. 수치 분석은 운영자에게 넘기되, 측정 도구의 결함은 데이터 없이도 선제 수정 가능 — 오염된 근거로 판단이 굳는 것을 막는 게 우선순위.

## 관련 맥락

- [[naver-finance-no-info-selector-drift]] — 이 재검증을 촉발한 원 데이터 수집 버그(셀렉터 드리프트로 전 종목 volume=0 silent 오염).
- [[scoring-version-comparison-methodology]] — 오염 제거 후 실제 가중치 비교(Rank IC·분위 스프레드) 방법론.
- [[scoring-system-ic-validation]] — 재검증에서 측정할 컴포넌트별 IC·cutoff 캘리브레이션.

## 변경 이력

- 2026-07-02: 최초 작성 — n_stock_info 07-01 재검증 도구 수정 세션에서 일반화. 날짜 하한 필터 디폴트 + "계측기는 데이터 없이도 선고칠 수 있다" (출처: session-logs/20260701-232403-58cb-*)
