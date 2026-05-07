---
title: RAGFlow — Open-Source RAG + Agent Context Engine
domain: "personal"
sensitivity: "public"
tags: [analysis, oss, rag, llm, agent, infiniflow]
created: 2026-05-07
updated: 2026-05-07
source_session: 20260507-090136-e343-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md
sources:
  - "session-logs/20260507-090136-e343-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md"
confidence: "medium"
related: []
---

# RAGFlow — Open-Source RAG + Agent Context Engine

## 개요

- **레포**: https://github.com/infiniflow/ragflow
- **Stars**: 79,828
- **언어**: Python
- **라이선스**: Apache-2.0
- **메인테이너**: infiniflow
- **최신 버전**: v0.25.1
- **Topics**: agentic-ai, agentic-retrieval, agentic-search, ai, ai-agents, context-engine, context-management, llm-apps, rag, retrieval-augmented-generation

RAGFlow 는 RAG (Retrieval-Augmented Generation) 와 Agent 능력을 결합해 LLM 을 위한 "컨텍스트 레이어" 를 제공하는 오픈소스 엔진. 비정형 데이터를 production-ready AI 시스템으로 변환하는 것이 목표.

## 핵심 기능

- **Deep document understanding** — 복잡한 포맷의 비정형 데이터에서 지식 추출
- **Template-based chunking** — 설명 가능한 인텔리전트 청킹, 다양한 템플릿 옵션
- **Grounded citations** — 청킹 시각화로 인간 개입 가능, 참조·인용 추적으로 환각 감소
- **Heterogeneous data sources** — Word/슬라이드/엑셀/이미지/스캔본/구조화 데이터/웹 페이지 지원
- **Automated RAG workflow** — 다중 recall + fused re-ranking, configurable LLM/embedding 모델
- **Agentic workflow + MCP** (2025-08-01 추가)
- **Orchestrable ingestion pipeline** (2025-10-15)
- **MinerU & Docling** 문서 파싱 (2025-10-23)
- **외부 데이터 동기화** — Confluence, S3, Notion, Discord, Google Drive (2025-11-12)
- **Memory** for AI agent (2025-12-26)
- **OpenClaw skill** — RAGFlow dataset 접근용 공식 skill (2026-03-24)

## 아키텍처

- **Doc engine**: 기본 Elasticsearch, [Infinity](https://github.com/infiniflow/infinity/) 로 전환 가능
- **의존 서비스**: MinIO, Elasticsearch, Redis, MySQL — Docker Compose 로 기동
- **Sandbox**: 코드 실행기 사용 시 gVisor 필요
- **개발 환경**: Python 3.12, `uv` 패키지 매니저, `pre-commit`
- **프런트엔드**: web/ 디렉터리, npm 기반
- **시스템 요구사항**: CPU ≥ 4 cores, RAM ≥ 16 GB, Disk ≥ 50 GB, Docker ≥ 24.0.0

## 평가

- **성숙도**: 79K+ stars, v0.25.1, 다국어 README (영/중/일/한/프/포/아랍/터키/인니), 클라우드 서비스 (cloud.ragflow.io) 운영 — 매우 성숙
- **사용 시나리오**: 기업 규모 RAG 시스템, 컨텍스트 엔지니어링 플랫폼, agentic 워크플로 빌드
- **트렌드 맥락**: "context engineering" 키워드와 agentic RAG 결합 — 2025~2026 RAG 시장 표준화 흐름의 선두

## 한계

- ARM64 Docker 이미지 미공식 제공 (별도 빌드 가이드 필요)
- ARM64 Linux 에서 Infinity 엔진 전환 미지원
- v0.22.0 부터 slim 에디션만 출시 (임베딩 모델 미포함, 외부 LLM/embedding 의존도 증가)

## 변경 이력

- 2026-05-07: oss-radar 자동 분석 로그 기반 신규 작성
