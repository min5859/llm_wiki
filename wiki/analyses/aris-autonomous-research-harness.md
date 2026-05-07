---
title: "ARIS — 적대적 멀티에이전트 협업 기반 자율 연구 하네스"
domain: "personal"
sensitivity: "public"
tags: ["analysis", "agent", "multi-agent", "autonomous-research", "harness", "claude-code", "skill", "MCP", "cross-model"]
created: "2026-05-07"
updated: "2026-05-07"
source_session: "20260507-080128-c5a0-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
sources:
  - "session-logs/20260507-080128-c5a0-당신은-AI-연구-논문-분석-전문가입니다.-아래-논문을-읽고---반드시-한국어로---분석.md"
confidence: "medium"
related:
  - "wiki/analyses/everything-claude-code.md"
  - "wiki/analyses/onemancompany-heterogeneous-agents-organization.md"
---

# ARIS — 적대적 멀티에이전트 협업 기반 자율 연구 하네스

Ruofeng Yang 외 (Shanghai Jiao Tong University, Shanghai Innovation Institute). arXiv 2605.03042.
프로젝트: github.com/wanshuiyin/Auto-claude-code-research-in-sleep (v0.4, 2026-04 기준).

## 한줄 요약

장기 ML 연구 워크플로우에서 단일 에이전트의 "그럴듯한 무근거 성공(plausible unsupported success)" 을 막기 위해, 서로 다른 모델 family 의 executor/reviewer 를 기본값으로 강제하고 65+ 개 Markdown 스킬·5개 워크플로우·3단계 assurance 를 결합한 오픈소스 연구 하네스.

## 개요

배경: AI Scientist v1/v2, Agent Laboratory 등 기존 자율 연구 에이전트들은 (1) 동일 모델 family 가 생성과 검증을 모두 수행해 inductive bias 가 공유되고, (2) end-to-end 가 강결합돼 단계 교체/재개가 어렵고, (3) 실험 무결성에 대한 시스템 수준 검사가 부재하다는 한계를 가짐.

핵심 가정: "단일 에이전트가 수행하는 모든 장기 작업은 신뢰할 수 없다. 워크플로우를 sub-workflow 로 분할하고, cross-family 모델로 각 단계를 독립 검토해야 한다."

## 주요 기여

- **3계층 아키텍처**: execution / orchestration / assurance.
- **5개 end-to-end 워크플로우**: W1 idea discovery, W1.5 experiment bridge, W2 auto-review loop, W3 paper writing, W4 rebuttal.
- **Cross-family 적대적 협업**: Claude-family executor + GPT-family reviewer (Codex MCP / Oracle MCP) 를 기본값으로 채택, 그 반대도 지원. Gemini, MiniMax, GLM, Kimi, DeepSeek 도 MCP/llm-chat 브리지를 통해 연결.
- **3단계 Assurance Cascade**: integrity verification → result-to-claim mapping → claim auditing(클레임 ledger 와 raw evidence 교차검증). 추가로 5-pass 과학 편집 파이프라인, 수학 증명 검증, 렌더링된 PDF 의 visual inspection.
- **Persistent Research Wiki**: 프로젝트별 4개 entity type (논문, 아이디어, 실험 기록, tracked claim) 의 영속 메모리.
- **Self-improvement loop (프로토타입)**: 연구 trace 를 기록하고 reviewer 승인 후에만 하네스 개선안을 채택.

## 방법

### 5가지 설계 원칙

1. Heterogeneous models > single-model self-refinement (Madaan/Reflexion 류 한계 회피).
2. Modular skill files (`SKILL.md`) > monolithic agents.
3. Composability over fixed pipelines — checkpoint 기반 재개.
4. Portability — 동일 SKILL.md 가 Claude Code, Codex CLI, Cursor 에서 무수정 동작 (Trae 등 3개 추가 어댑트).
5. Persistent memory over ephemeral context (Karpathy 2026 의 LLM Wiki 패턴 차용).

### Cross-Model Critique-to-Action Loop

executor 가 artifact 생성 → reviewer 가 파일 경로만 받아 직접 읽고 rubric 기반 점수 + 구조화된 action items 반환 → executor 가 수정 → 수렴 점검. 종료 조건: 점수 ≥ 6/10 + 모든 critical 항목 해결, 또는 최대 4 라운드.

Reviewer access 두 축:
- **Access scope**: document-only / artifact-augmented / repository-level.
- **Context policy**: 동일/분리.

executor 의 요약 대신 reviewer 가 직접 읽도록 강제 — framing 상속을 차단.

### 컴포넌트 (v0.4, 2026-04)

- Skills: 65+ Markdown 파일.
- Model bridges: 6개 MCP (Codex, Oracle, Claude, Gemini, MiniMax, llm-chat).
- Tested executors: Claude Code, Codex CLI, Cursor (+ 3 어댑트).
- Effort presets: lite / balanced / max / beast.
- 의존성: 스킬은 zero-dep, CLI 는 단일 Rust 바이너리.

## 결과

본 보고서는 정량 벤치마크보다 시스템 보고에 가깝다. 주요 정성 결과:

- W2 auto-review: cross-model rubric 점수 임계치 + GPU 실험 트리거 결합으로 수렴.
- W3 paper writing: Plan&Generate → Draft&Assure (5-pass edit, proof check, claim audit) → Compile&Improve (GPT-5.4 xhigh 의 visual review 2 라운드).
- W4 rebuttal: 8-issue atomization, Claude 가 작성·GPT-5.4 가 stress test (provenance/commitment 검증) 후 PASTE_READY 산출.
- 3개 executor 플랫폼에서 deploy 검증, 추가 3개 어댑트, community usage report 포함.

## 한계

- 정량 비교(AI Scientist v1/v2, Agent Laboratory 등 대비 정확도/완성도 수치) 부족.
- "Plausible unsupported success" 를 막는 assurance 는 reviewer 모델의 능력에 강하게 의존 — 동일 family 만 사용 가능한 환경에서는 핵심 가정이 깨짐.
- Self-improvement loop 는 prototype 단계.
- Adversarial review 가 executor 의 최적화를 어렵게 만드는 trade-off (저자 인정).
- 인간 in-the-loop 가 여전히 품질 향상에 유의미하다고 명시.

## 실무 적용 가능성

- Claude Code / Codex CLI / Cursor 사용자가 자신의 연구·문서 워크플로우에 그대로 도입 가능 (SKILL.md 가 portable).
- 회사 환경의 MCP 기반 코드 리뷰·문서 검수 파이프라인 설계 시, "동일 family self-review 금지" 원칙은 즉시 적용 가능한 지침.
- Persistent research wiki 패턴은 Karpathy LLM Wiki / 본 프로젝트(gieok) 와 동일 계보 — 연구·업무 지식 축적의 표준 기법으로 자리 잡는 추세.
- Rebuttal 처럼 "근거-주장 정합성" 이 중요한 작업에 cross-family stress test 가 효과적인 패턴임을 시사.

## 관련 페이지

- [[everything-claude-code]] — Claude Code 하네스/스킬 시스템
- [[onemancompany-heterogeneous-agents-organization]] — 이종 에이전트 조직화 패턴
- [[agentspex-agent-specification-language]] — 에이전트 스펙 언어 비교

## 변경 이력

- 2026-05-07: 신규 작성 (자동 ingest from session-logs).
