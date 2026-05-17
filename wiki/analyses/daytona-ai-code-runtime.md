---
title: Daytona — AI 생성 코드를 위한 보안 격리 실행 인프라
domain: personal
sensitivity: public
tags: [analysis, oss, ai, sandbox, devbox, infrastructure, nestjs, go]
created: 2026-05-17
updated: 2026-05-17
source_session: 20260517-071129-4e43-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.---오픈소스-큐레이션---파이.md
sources:
  - "session-logs/20260517-071129-4e43-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.---오픈소스-큐레이션---파이.md"
confidence: medium
related:
  - "wiki/analyses/everything-claude-code.md"
---

# Daytona — AI 생성 코드를 위한 보안 격리 실행 인프라

## 개요

- **레포**: https://github.com/daytonaio/daytona
- **공식 사이트**: https://www.daytona.io/
- **운영**: Daytona Platforms, Inc.
- **포지셔닝**: "Run AI Code. Secure and Elastic Infrastructure for Running Your AI-Generated Code."

LLM 에이전트가 만들어낸 코드를 안전·탄력적으로 실행하기 위한 sandbox 인프라. SDK (`@daytonaio/sdk`) + REST API + Go CLI 의 3중 인터페이스로 sandbox 라이프사이클·SSH·VNC·Git 작업을 제공.

## 핵심 컴포넌트

### 3-plane 아키텍처

- **Interface plane** — Dashboard, Web Terminal, SSH, VNC, Git 등 사람·자동화 양쪽 진입점
- **Control plane** — `apps/api` (NestJS 기반) 가 sandbox 라이프사이클·인증·과금·감사를 담당
- **Compute plane** — `apps/cl*` (이 부분은 README 발췌만으로는 미완성, 컨테이너 런타임 추정)

### 거버넌스·운영 매트릭스

문서 기능 표에 다음 축이 매트릭스로 묶여 있음:

- **거버넌스**: Organizations, API Keys, Limits, Billing, Audit logs
- **시스템 훅**: Webhooks
- **네트워크**: 네트워크 제한 정책
- **인터페이스**: Dashboard, Web Terminal, SSH, VNC, Git 작업

→ 단순 컨테이너 런처가 아니라 「조직·과금·감사」 까지 묶인 운영 플랫폼.

## 사용 시나리오

- LLM 에이전트가 생성한 코드를 격리된 sandbox 에서 실행 (코드 실행 도구가 호스트를 침해하지 못하도록)
- AI 코딩 어시스턴트·CI 봇의 sandbox 백엔드
- 멀티 테넌트 환경 (한 인프라에서 여러 사용자·조직 코드 실행)
- 일회용 devbox (PR 미리보기, 데모 환경)

## 기술 스택

- **API**: NestJS (TypeScript)
- **CLI**: Go
- **SDK**: TypeScript (`@daytonaio/sdk`)
- **컨테이너**: Compute plane 의 정확한 런타임은 README 발췌만으로는 불명 (Docker/Firecracker/gVisor 등 일반 후보)

## 평가

- **포지셔닝**: 「AI 코드 실행」 이라는 명확한 카테고리. E2B (https://e2b.dev/), Modal, Replit Agent Cloud 등과 유사·경쟁
- **거버넌스 우위**: Audit log·Billing·Organizations 가 처음부터 묶인 게 단순 sandbox SaaS 대비 차별점
- **트렌드**: AI 에이전트가 직접 코드를 실행하는 흐름 (Claude code 의 Bash tool, OpenAI code interpreter 등) 의 인프라 면

## 한계·주의점

- README 만으로는 라이선스가 명확하지 않음 — 도입 전 LICENSE 직접 확인 필요
- self-hosted 와 SaaS 양쪽 모델 가능성, 운영 부담 차이 큼
- 「AI 가 만든 코드」 라는 광고 카피의 영역 외, 일반 sandbox 로도 사용 가능

## 관련

- [[everything-claude-code]] — Claude Code 의 도구 실행 영역과 인접
- 경쟁 카테고리: E2B (open-source sandbox), Modal (compute fabric), Replit Agent Cloud

## 변경 이력

- 2026-05-17: oss-radar 큐레이션 브리핑 로그 기반 신규 작성 (cron 분석 미생성 상태에서 candidate body 직접 요약)
