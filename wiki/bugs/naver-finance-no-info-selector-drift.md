---
title: "네이버 금융 table.no_info 셀렉터 드리프트 — 거래량·시가·등락률 silent 0 (가짜 fixture가 가린 버그)"
domain: "both"
sensitivity: "public"
tags: ["scraping", "beautifulsoup", "naver-finance", "selector-drift", "silent-fail", "test-fixture", "n-stock-info"]
created: "2026-06-17"
updated: "2026-06-17"
sources:
  - "session-logs/20260616-210439-d6f6-오늘-알고리즘-바꾼지-2일-지났는데-2일-동안-매매에서-오류나-개선점이-없었는지-검토해줘.md"
confidence: high
related:
  - "wiki/projects/n-stock-info.md"
  - "wiki/bugs/yahoo-finance-concurrent-silent-fail.md"
  - "wiki/analyses/scoring-version-comparison-methodology.md"
---

# 네이버 금융 table.no_info 셀렉터 드리프트

`n_stock_info` 가 네이버 금융 종목 페이지(`item/main.naver`)에서 **거래량·시가·고가·저가·등락률**을 긁어오던 셀렉터가 페이지 HTML 구조 변경으로 깨져, **2026-03 이후 전 종목·전 날짜에서 해당 값들이 줄곧 0/null** 로 수집되고 있었다. 예외도 에러도 안 나는 silent fail 이라 6일치 cron 로그·테스트가 모두 정상으로 보였다.

## 증상

- DB(`daily_candidates.details_json`)의 `technical.volume = 0`, `open_price = null` 이 **전 종목·전 날짜 100%**. 옆 칸 `avg_volume_20d`(20일 평균)는 정상.
- 텔레그램 브리프의 등락률이 항상 `+0.0%`.
- 그 결과 기술 점수의 **거래량(10점)·캔들(5점)이 항상 0** → 기술 40점이 사실상 최대 25점으로만 작동.

## 근본 원인

네이버 `item/main` 페이지의 주요시세 표(`table.no_info`)는 라벨을 `<th>` 가 아니라 `<span class="sptxt">` 에, 값을 바로 뒤 `<em><span class="blind">16,694,918</span></em>` 에 둔다. 코드는 `table.select("th")` 로 라벨을 찾고 있어 **항상 빈 결과 → 0/null**.

등락률은 별도 버그였다. `.no_exday em span.blind` 를 `select_one` 으로 잡는데, 첫 `em` 은 전일대비 **금액**(6,000)이고 `%` 를 가진 `em` 은 그다음이다. 첫 em 에 `%` 가 없어 그대로 `0.0` 반환, 두 번째 em(`+1.78%`)에 도달하지 못했다.

> price·PER·ROE 는 멀쩡했다 — 다른 셀렉터를 쓰기 때문. 같은 페이지라도 영역별로 구조가 달라 **일부만 깨지면 더 안 보인다.**

## 왜 6일치 로그·테스트가 못 잡았나 (핵심 교훈)

테스트 fixture(`STOCK_MAIN_HTML`)가 **실제 네이버와 다른 가짜 구조**(`<tr><th>거래량</th><td>...</td>`, 단일 `<em>`)로 작성돼 있었다. 게다가 테스트는 `price`·PER 만 검증하고 **volume/open/change_pct 는 단언하지 않았다.** → 가짜 구조 + 미검증 필드 = 테스트는 green, 실제 파싱은 broken.

**가짜로 만든 fixture 는 "내 코드가 내 가짜 HTML 을 잘 파싱한다"만 증명할 뿐, 실제 원격 구조 변경을 절대 못 잡는다.** 스크래핑 fixture 는 반드시 실제로 캡처한 마크업을 박제하고, 추출한 필드 전부(여기선 volume/open/high/low/change/trade_value)를 단언해야 한다.

## 수정 (commit `e13765f`)

- `_no_info_values` 헬퍼 신설: `span.sptxt` 라벨 → 다음 `em` 의 `span.blind` 값 매핑. `_extract_volume_trade`·`_extract_ohlc` 를 그 위에 재작성(169줄 삭제·90줄 추가로 더 짧아짐).
- `_extract_change_pct`: `%` 를 **포함한** em 을 선택하고 방향은 `상승`/`하락` 텍스트로 판정.
- 가짜 fixture 를 **실제 `no_info`/`no_exday`/`no_today` 마크업**으로 교체하고 추출 필드 전부 단언(옛 코드에선 실패하는 테스트).
- 검증: 166 테스트 통과, ruff 클린, 라이브 수집 확인(삼성전자 등락률 1.78%·거래량 16.6M, 화신 +29.96%).

## 더 중요한 함의 — 망가진 데이터로 내린 설계 결정

`DECISION.md` 의 가중치 비교 백테스트(40/40/20 IC +0.248 → 0/80/20 IC +0.327, [[scoring-version-comparison-methodology]])는 **이 망가진 기술 점수로 계산된 값**이다. "기술을 빼니 예측력이 좋아졌다"는 결론에 *실은 기술이 절반(거래량+캔들 15점) 고장 나 있어서* 안 좋게 나온 측면이 섞여 있다. 지금 추천에는 영향이 없지만(0/80/20 라이브에서 기술 가중 0), **일주일 뒤 가중치 재검증은 버그 수정 후 다시 측정해야** 기술 알파 판단이 공정해진다.

## 일반 교훈

- **스크래핑 셀렉터 드리프트는 예외 없이 silent 하게 0/null 을 만든다.** 핵심 수치에는 "이 값이 0/null 이면 이상"이라는 sanity 경보(또는 비율 기반 알림)를 둔다.
- **가짜 fixture 는 회귀를 못 잡는다.** 실제 응답을 박제하고, 옛 코드에서 실패하는지 먼저 확인한 뒤 통과시킨다(red→green).
- **망가진 입력으로 산출한 지표(IC·승률 등)로 내린 결정은 입력 수정 후 재검증한다.**

## 변경 이력

- 2026-06-17: 최초 작성. 2026-06-16 세션(알고리즘 2일 검토 → 거래량·시가 수집 버그 수정)에서 추출 (출처: session-logs/20260616-210439-d6f6-*)
