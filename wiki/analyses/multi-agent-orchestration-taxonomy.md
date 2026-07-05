---
title: "멀티에이전트 오케스트레이션 3분류 — 코딩 파이프라인에서의 실무 결론"
domain: "ai-agent"
sensitivity: "public"
tags: ["analysis", "ai-agent", "orchestration", "multi-agent", "delegate", "kanban", "langgraph", "crewai"]
created: "2026-07-05"
updated: "2026-07-05"
sources:
  - "session-logs/20260704-132738-e509-지금-세션에서-작업했던-hermes-webui-설치가-pc-를-껏다켜니-접속이-안되네-다시.md"
confidence: high
related:
  - "wiki/analyses/weak-model-agent-reliability-compounding.md"
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/hermes-paperclip-adapter.md"
  - "wiki/patterns/single-dispatcher-per-queue.md"
---

# 멀티에이전트 오케스트레이션 3분류 — 코딩 파이프라인에서의 실무 결론

여러 AI 에이전트를 조율하는 방식의 분류와, "코딩 작업에 멀티에이전트가 이득인가"에 대한 실무 결론. Hermes 멀티에이전트 실험(delegate_task vs 칸반 파이프라인 vs Paperclip) 과정에서 리서치로 정리 (2026-07-04).

## 3가지 조율 방식

| 방식 | 구조 | 패턴명 | 예 |
|------|------|--------|-----|
| A. 내부 위임 | 한 에이전트가 일회용 서브에이전트를 spawn 해 처리·취합 | Orchestrator-Worker (프로덕션 최다) | Hermes `delegate_task`, Claude Code 서브에이전트 |
| B. 작업 큐 | 이름 붙은 역할 에이전트들이 공유 보드에서 태스크를 claim·핸드오프 | Sequential Pipeline | Hermes 네이티브 칸반 (architect→coder→…→reporter) |
| C. 조직 모델 | 에이전트를 "직원"으로 조직도·거버넌스로 조율 | Hierarchical | Paperclip ([[hermes-paperclip-adapter]]) |

프레임워크 지형: **LangGraph** (프로덕션 1위, 그래프 + 체크포인트), **CrewAI** (역할 crew, 프로토타입 강·관측성 약), **AutoGen** (연구용 GroupChat).

## 실무 결론 — 멀티에이전트가 이기는 조건

- **이긴다**: 병렬화·분해 가능한 작업 — 예: Anthropic 리서치 시스템의 병렬 검색 (+90% 성능). 각 워커가 독립 컨텍스트에서 다른 부분을 파고들 때.
- **이득이 marginal 하거나 진다**: **선형 코딩 파이프라인** — 조율 비용(지연·토큰 비용·유지보수)이 커서, 강한 단일 에이전트가 임계(SWE-bench 45%↑) 를 넘으면 에이전트를 추가할수록 오히려 성능이 떨어진다. 순차 조립라인(architect→coder→reviewer)은 "멋있지만 소수·특수 용도".
- **예외**: 약한 모델 환경에서는 역할 분할 + 검증 게이트가 오히려 정답 — [[weak-model-agent-reliability-compounding]].

## 운영 함정

방식 B(작업 큐)는 dispatcher 구성이 급소다 — 공유 보드를 여러 dispatcher 가 동시에 돌리면 claim churn 으로 태스크가 진행되지 않는다 → [[single-dispatcher-per-queue]].

## 변경 이력

- 2026-07-05: 최초 생성 — Hermes 멀티에이전트 실험 중 오케스트레이션 리서치에서 정리 (출처: session-logs/20260704-132738-e509-*)
