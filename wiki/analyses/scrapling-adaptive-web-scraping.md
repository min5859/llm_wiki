---
title: Scrapling — Adaptive Web Scraping Framework
domain: "personal"
sensitivity: "public"
tags: [analysis, oss, web-scraping, python, parsing]
created: 2026-05-07
updated: 2026-05-07
source_session: 20260507-090326-b169-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md
sources:
  - "session-logs/20260507-090326-b169-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md"
confidence: "low"
related: []
---

# Scrapling — Adaptive Web Scraping Framework

## 개요

- **레포**: https://github.com/D4Vinci/Scrapling
- **저자**: Karim Shoair (D4Vinci)
- **라이선스**: BSD-3-Clause
- **언어**: Python
- **자기 소개**: "An adaptive Web Scraping framework that handles everything from a single request to a full-scale crawl"

단발 요청부터 풀스케일 크롤까지 처리하는 적응형 (adaptive) 웹 스크래핑 프레임워크. CSS / XPath / BeautifulSoup 스타일 셀렉터를 단일 API 로 노출.

> 주의: 세션 로그가 부분적으로 redacted 되어 stars 수치, 전체 features 표는 추출 불가. 본 페이지는 README 단편에서 확인 가능한 부분만 기록.

## 핵심 기능

- **다중 셀렉터 스타일** — CSS, XPath, BeautifulSoup-style `find_all` 동시 지원
- **DynamicFetcher** — 한 번의 호출로 브라우저 띄우기 → fetch → 종료 (one-off 요청)
- **Selector 단독 사용** — `from scrapling.parser import Selector` 로 페치 없이 HTML 파싱만 수행 가능
- **Async 세션 관리** — `FetcherSession`, `AsyncStealthySession`, `AsyncDynamicSession` 제공
- **HTTP/3 지원** — `FetcherSession(http3=True)`
- **Element 관계 탐색** — `next_sibling`, `parent`, `find_similar()`, `below_elements()` 등 유사·관계형 헬퍼
- **체이닝 셀렉터** — `page.css('.quote').css('.text::text').getall()`
- **텍스트 기반 검색** — `page.find_by_text('quote', tag='div')`

## 아키텍처

- **Translator 모듈**: Parsel (BSD) 코드 어댑테이션
- **Session 추상화**: 동기/비동기 양립 컨텍스트 매니저
- **Stealthy / Dynamic 세션**: 별도 fetcher 모듈로 봇 탐지 회피·헤드리스 브라우저 자동화 분리

## 평가

- **사용 시나리오**: BeautifulSoup·Scrapy·Playwright 사이를 오가는 사용자에게 단일 API 제공. one-off 스크래핑부터 stealthy 크롤까지.
- **차별점**: 셀렉터 패러다임 통합 + 적응형 (사이트 구조 변화에도 견고) 마케팅
- **성숙도**: BSD-3-Clause, 인용용 BibTeX 제공 — 학술 인용 사용성 고려한 설계

## 한계

- 세션 로그 일부 누락 — 정확한 star 수, 버전, 의존성 매트릭스, 성능 벤치마크는 본 페이지에서 검증 불가
- "adaptive" 라는 슬로건의 구체 메커니즘 (셀렉터 자동 적응 등) 은 원 README 미게재 부분
- 봇 탐지 우회 (`AsyncStealthySession`) 는 대상 사이트 ToS·법적 리스크 동반

## 변경 이력

- 2026-05-07: oss-radar 자동 분석 로그 기반 신규 작성 (session log redaction 으로 부분 정보)
