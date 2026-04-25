---
title: "AgentSPEX — YAML 기반 LLM 에이전트 워크플로우 명세 언어"
domain: "both"
sensitivity: "public"
tags: ["llm-agent", "workflow", "yaml", "swe-bench", "langraph", "declarative", "formal-verification"]
created: "2026-04-25"
updated: "2026-04-25"
sources:
  - "session-log: 20260425-080021-dbd0 (arXiv 2604.13346)"
confidence: "high"
related:
  - "wiki/concepts/ai-agent-basics.md"
  - "wiki/patterns/claude-code-agent-teams-tmux.md"
---

# AgentSPEX — YAML 기반 LLM 에이전트 워크플로우 명세 언어

UIUC 등 공동 연구팀이 제안한 에이전트 워크플로우 명세·실행 언어. LangGraph/DSPy/CrewAI처럼 Python에 제어 흐름을 묻어두는 방식의 한계를 극복하기 위해 YAML로 워크플로우를 선언하고, 별도의 에이전트 하네스가 실행을 담당한다. (arXiv: 2604.13346, 2026)

## 핵심 내용

### 기존 프레임워크의 문제

- **반응형 프롬프팅(reactive prompting)**: 단일 지시문으로 모든 추론·도구 사용을 유도 → 제어 흐름과 중간 상태가 암시적
- **Python 결합 프레임워크** (LangGraph, DSPy, CrewAI): 워크플로우 로직이 Python 코드에 강결합 → 유지보수·수정 어려움, 모델 버전 변경 시 취약

### AgentSPEX 설계 원칙

- **선언적 YAML 명세**: 타입된 스텝, 분기/루프, 병렬 실행, 재사용 서브모듈, 명시적 상태 관리
- **에이전트 하네스 분리**: 도구 접근, 샌드박스 가상 환경, 체크포인팅, 검증, 로깅
- **시각적 편집기**: 그래프 뷰와 워크플로우 뷰를 동기화

### 모델 버전 견고성 (SWE-Bench Verified)

Claude-Opus-4.5 → Claude-Opus-4.6 전환 시 성능 변화:

| 프레임워크 | Opus 4.5 | Opus 4.6 | 변화 |
|---|---|---|---|
| AgentSPEX | 77.2% | 77.0% | **-0.2%** |
| mini-SWE-agent | 76.8% | 75.6% | -1.2% |
| Live-SWE-agent | 78.0% | 71.2% | **-6.8%** |

> Python에 제어 흐름을 묻은 프레임워크는 모델 동작의 미세한 변화가 코드 전반에 파급됨. 선언적 YAML 명세는 프롬프트와 실행 코드를 분리하여 이 취약점을 차단.

### 형식 검증 (Formal Verification)

- 선언적 구조 덕분에 제어 흐름, 변수 의존성, 스텝 경계가 명시적 → 정적·런타임 검증 가능
- 예: `extract_single_citation_module` 워크플로우를 YAML에서 속성 추론 후 궤적 검증

### 평가 벤치마크 (7개)

| 도메인 | 벤치마크 |
|---|---|
| 소프트웨어 엔지니어링 | SWE-Bench Verified |
| 수학 | AIME 2025 |
| 화학 | ChemBench, SciBench, MMLU-Pro Stemez |
| 생성적 글쓰기 | WritingBench |
| 논문 이해 | ELAIPBench |

### 사용자 연구

- 설문 항목: 가독성, 신규 워크플로우 작성 용이성, 프롬프트 파악 용이성, 복잡한 멀티스텝 워크플로우 선호도
- 결과: AgentSPEX가 LangGraph 대비 해석 가능성·접근성에서 우위

## 관련 맥락

- 에이전트 프레임워크 비교 관점에서 [[ai-agent-basics]] 참고
- gieok 처럼 Claude Code Hooks 기반 하네스를 직접 구축할 때 이 선언적 분리 원칙이 동일하게 적용됨

## 변경 이력

- 2026-04-25: 최초 생성 (출처: session-log 20260425-080021-dbd0, arXiv 2604.13346)
