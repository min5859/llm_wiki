---
title: "네이버 금융 종목 뉴스 크롤링 — Referer 헤더 없으면 빈 스텁 페이지"
domain: "trading"
sensitivity: "public"
tags: ["bug", "trading", "naver-finance", "crawling", "referer", "news", "euc-kr"]
created: "2026-07-07"
updated: "2026-07-07"
sources:
  - "session-logs/20260706-220340-ac9d-아래와-같은-알고리즘을-셋업하고-싶은데-어떻게-하면-될까---최근-일주일간-지수대비-덜떨어.md"
confidence: "high"
related:
  - "wiki/bugs/naver-finance-no-info-selector-drift.md"
  - "wiki/projects/ht-dde.md"
---

# 네이버 금융 종목 뉴스 — Referer 헤더 필수

`finance.naver.com/item/news_news.naver` (종목 뉴스 목록) 은 **Referer 헤더가 없으면 에러 대신 빈 스텁 페이지 (약 5.9KB) 를 반환**한다. HTTP 상태는 정상이라 셀렉터가 아무것도 못 찾는 형태로만 발현되어 원인을 놓치기 쉽다.

## 해결

```
Referer: https://finance.naver.com/item/news.naver?code=<종목코드>
```

- Referer 추가 시 정상 응답 (약 15.6KB).
- 인코딩은 **euc-kr**.
- 파싱 앵커: `<td class="title">` 안의 `<a class="tit">`.

## 일반 교훈

- 네이버 금융 크롤링의 실패는 **에러가 아니라 "조용한 빈 결과"** 로 발현되는 패턴이 반복된다 — 셀렉터 드리프트로 silent 0 이 나오던 [[naver-finance-no-info-selector-drift]] 와 같은 계열. 응답 크기(바이트) 를 로깅해 두면 스텁/정상 구분이 빠르다.

## 변경 이력

- 2026-07-07: 최초 생성 — ht_dde RS 스캐너의 "이유 점검" 뉴스 첨부 구현 중 발견 (출처: session-logs/20260706-220340-ac9d-*)
