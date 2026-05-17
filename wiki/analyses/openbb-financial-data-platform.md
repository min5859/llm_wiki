---
title: OpenBB — Open Data Platform for finance (Python·REST·MCP·Excel)
domain: personal
sensitivity: public
tags: [analysis, oss, finance, python, mcp, openbb, quantitative-finance, data-platform]
created: 2026-05-17
updated: 2026-05-17
source_session: 20260517-071129-4e43-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.---오픈소스-큐레이션---파이.md
sources:
  - "session-logs/20260517-071129-4e43-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.---오픈소스-큐레이션---파이.md"
confidence: medium
related:
  - "wiki/analyses/news-driven-market-signal-framework.md"
  - "wiki/analyses/llm-news-prediction-pitfalls.md"
---

# OpenBB — Open Data Platform for finance

## 개요

- **레포**: https://github.com/OpenBB-finance/OpenBB
- **공식 사이트**: https://openbb.co/
- **GitHub stars**: ~67,638 (2026-05 기준)
- **라이선스**: AGPLv3
- **언어**: Python
- **Topics**: ai, crypto, derivatives, economics, equity, finance, fixed-income, machine-learning, openbb, options, python, quantitative-finance, stocks

「한 번 연결, 어디서나 소비」 모델의 금융 데이터 ODP (Open Data Platform). Python 패키지·REST API·MCP 서버·Excel·OpenBB Workspace 까지 동일 데이터 모델을 동시 노출.

## 핵심 컴포넌트

- **`openbb`** — Python 패키지 (`pip install openbb`, `openbb[all]` 로 풀 의존성)
- **`openbb-api`** — FastAPI/Uvicorn 기반 로컬 백엔드 (기본 `127.0.0.1:6900`)
- **OpenBB Workspace** — 자체 GUI/대시보드, 외부 백엔드를 붙여 사용
- **MCP 서버** — Model Context Protocol 노출 → Claude/Cursor 같은 AI 에이전트가 직접 금융 데이터 도구로 사용
- **Excel 연계** — 스프레드시트 사용자가 동일 데이터에 접근

## 데이터 도메인 (Topics 기준)

equity / crypto / derivatives / options / fixed-income / economics — 주식·암호화폐·파생상품·옵션·채권·거시 경제까지 망라.

## 사용 시나리오

- 퀀트 / 애널리스트 / 트레이더의 데이터 백엔드 표준화
- AI 에이전트 (LLM) 의 금융 데이터 도구 (MCP 경유)
- 인-하우스 데이터 인프라 구축 (`openbb-api` 로 백엔드 배포 + 팀 내 도구 연결)
- 스프레드시트 사용자의 데이터 접근 통합

## 기술 스택·아키텍처

- **Python 3.x** 패키지 + FastAPI 백엔드
- **provider abstraction** — 다양한 금융 데이터 vendor 를 모듈식으로 추가
- **MCP integration** — `gieok` 같은 wiki 와 같은 MCP 표준 위에서 동작

## 평가

- **성숙도**: 67K+ stars, AGPLv3 거버넌스, 회사 (OpenBB) 운영, 활발한 다중 인터페이스
- **트렌드**: 금융 데이터의 「오픈 ODP」 카테고리 사실상 단독. Bloomberg Terminal·Refinitiv 의 OSS 대안 포지셔닝
- **AI-friendly**: MCP 서버 노출이 LLM 에이전트 시대에 결정적 어드밴티지

## 한계·주의점

- **AGPLv3 라이선스 + 금융 거래 disclaimer** — 상용 거래·자문 서비스 통합 시 컴플라이언스 (자본시장법, 금융사 약관) 와 라이선스 양쪽 검토 필수
- **무료 vs 유료 데이터** — 일부 vendor 는 API 키·구독 필요. 「pip install 하면 다 된다」 가 아님
- 데이터 정확성·지연 시간은 vendor 의존
- 한국 시장 (KIS, Dart 등) 의 directly 통합은 1순위 vendor 가 아니므로 별도 어댑터 필요할 수 있음

## 관련

- [[news-driven-market-signal-framework]] — 시장 신호 추출 프레임워크 (데이터 입력으로 OpenBB 적합)
- [[llm-news-prediction-pitfalls]] — LLM × 금융 데이터 결합 시 함정
- [[ht-trading]] — KIS 기반 알고리즘 트레이딩 (한국 시장은 OpenBB 보조 필요 케이스)

## 변경 이력

- 2026-05-17: oss-radar 큐레이션 브리핑 로그 기반 신규 작성 (cron 분석 미생성 상태에서 candidate body 직접 요약)
