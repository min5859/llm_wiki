---
title: World Monitor — Real-time Global Intelligence Dashboard
domain: "personal"
sensitivity: "public"
tags: [analysis, oss, dashboard, osint, geopolitics, tauri, ai]
created: 2026-05-07
updated: 2026-05-07
source_session: 20260507-090208-7524-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md
sources:
  - "session-logs/20260507-090208-7524-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md"
confidence: "medium"
related: []
---

# World Monitor — Real-time Global Intelligence Dashboard

## 개요

- **레포**: https://github.com/koala73/worldmonitor
- **Stars**: 53,651
- **언어**: TypeScript
- **라이선스**: AGPL-3.0 (비상업, 상업용 별도 라이선스)
- **저자**: Elie Habib (koala73)
- **웹 앱**: https://worldmonitor.app
- **Topics**: ai, dashboard, geopolitics, monitoring, news, opensource, osint, palantir, situation

AI 기반 뉴스 어그리게이션, 지정학 모니터링, 인프라 추적을 통합한 실시간 글로벌 인텔리전스 대시보드. "오픈소스 Palantir 류" 의 상황 인식 도구.

## 핵심 기능

- **500+ 큐레이션 뉴스 피드** — 15 카테고리, AI synthesized briefs
- **듀얼 맵 엔진** — 3D globe (globe.gl) + WebGL flat map (deck.gl), 45 데이터 레이어
- **Cross-stream 상관분석** — military / economic / disaster / escalation 신호 수렴
- **Country Intelligence Index** — 12 신호 카테고리에 걸친 composite risk scoring
- **Finance radar** — 92 stock exchanges, commodities, crypto, 7-signal market composite
- **Local AI** — Ollama 로 API 키 없이 전체 동작 가능
- **5 사이트 variants** — world / tech / finance / commodity / happy (단일 코드베이스)
- **Native desktop app** — Tauri 2 (macOS/Windows/Linux)
- **21 언어** — native-language feeds + RTL 지원

## 아키텍처

- **Frontend**: Vanilla TypeScript, Vite, globe.gl + Three.js, deck.gl + MapLibre GL
- **Desktop**: Tauri 2 (Rust) + Node.js sidecar
- **AI/ML**: Ollama / Groq / OpenRouter, Transformers.js (브라우저 사이드)
- **API Contracts**: Protocol Buffers (92 protos, 22 services), sebuf HTTP annotations
- **Deployment**: Vercel Edge Functions (60+), Railway relay, Tauri, PWA
- **Caching**: Redis (Upstash), 3-tier cache, CDN, service worker
- **Data sources**: 65+ 외부 데이터 소스 (지정학/금융/에너지/기후/항공/사이버/군/인프라/뉴스)

## 평가

- **성숙도**: 53K+ stars, 멀티 플랫폼 데스크톱 빌드 제공, 5개 도메인 variants 운영 — 완성도 높음
- **사용 시나리오**: OSINT 분석, 글로벌 동향 모니터링, 지정학 리스크 평가, 트레이더의 매크로 시그널 수집
- **트렌드 맥락**: 오픈소스 Palantir·OSINT 시각화 + Local AI (Ollama) + 데스크톱 (Tauri) 결합 — 프라이버시 보존형 인텔리전스 도구의 흐름

## 한계

- **AGPL-3.0** — 비상업적 자기호스팅·연구·교육 한정. 상업·SaaS·리브랜딩은 별도 상업 라이선스 필요 (장벽)
- "Palantir" 류 명칭이지만 본격 정부·기업 OSINT 플랫폼 대비 신호 신뢰성·소스 검증 메커니즘은 README 만으로 검증 불가
- Tauri 2 / Node.js sidecar 등 다층 런타임 — 빌드 복잡도 존재
- 보안 이슈 사전 공개 (IPC command exposure, renderer-to-sidecar trust boundary, fetch patch credential injection — 2026 패치) — 신뢰 경계 검토 필요

## 변경 이력

- 2026-05-07: oss-radar 자동 분석 로그 기반 신규 작성
