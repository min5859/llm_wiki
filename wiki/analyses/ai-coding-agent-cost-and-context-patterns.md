---
title: "AI 코딩 에이전트의 비용·컨텍스트 설계 패턴 — 강/약 모델 분리와 context rot 완화"
domain: both
sensitivity: public
tags: ["ai-agent", "coding-agent", "cost-optimization", "context-management", "context-rot", "claude-code", "codex", "skills", "pattern"]
created: 2026-06-15
updated: 2026-06-15
sources:
  - "session-logs/20260615-031501-653a-#-Opensource-Trending-Newsletter-—-Write-from-Doss.md"
  - "session-logs/20260615-033024-a9a3-#-Opensource-Curation-Research-Dossier-당신은-오픈소스-큐레.md"
confidence: medium
related:
  - "wiki/analyses/research-write-agent-separation.md"
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
  - "wiki/analyses/onemancompany-heterogeneous-agents-organization.md"
  - "wiki/analyses/everything-claude-code.md"
---

# AI 코딩 에이전트의 비용·컨텍스트 설계 패턴

dev-blog 의 "Opensource Trending / Curation" 뉴스레터 dossier 에 반복 등장한, 특정 프로젝트에 매이지 않는 **재사용 가능한 에이전트 설계 패턴**을 추출한 문서. 개별 트렌딩 프로젝트(스타 수·출시일)는 시간성이 강해 생략하고, 여러 프로젝트가 공통으로 구현하는 **메타패턴**만 남긴다. 수치는 대부분 프로젝트 자체 발표 벤치마크이므로 `confidence: medium`.

## 패턴 1 — 강/약 모델 분리 (분석은 비싼 모델, 실행은 싼 모델)

여러 에이전트 스킬이 같은 구조를 독립적으로 구현한다: **가장 강력한 모델로 "계획·감사"만 하고, 실제 코드 작성·실행은 저렴한 모델에 위임**한다.

- `shadcn/improve`: 최강 모델로 코드베이스를 9개 카테고리(정확성·보안·성능·테스트 커버리지·기술부채·의존성·DX·문서·방향성)로 감사하되 **소스는 직접 고치지 않고 `plans/` 에만 계획을 쓴다**. 실행은 저렴한 모델 몫.
- `9arm-skills` 의 `qwen-agent`: 저렴한 Qwen 서브에이전트에 작업을 위임하는 스킬.
- `ponytail`: AI 가 "게으른 시니어 개발자" 원칙(기존 해법·stdlib·플랫폼 기능 우선)을 따르게 유도해 **작성 코드량 자체를 최소화**. Claude Code·Codex·Gemini CLI 등 다수 에이전트용 플러그인 제공.

> 핵심: 비싼 추론은 "무엇을·왜" 판단(계획·감사)에 쓰고, 값싼 실행은 "어떻게" 채우기에 쓴다. "계획을 코드가 아니라 `plans/` 파일에만 쓴다"는 제약이 강모델 출력을 검증 가능한 산출물로 고정한다.

자가 발표 수치(ponytail): 코드량 80–94% 감소, 3–6배 빠름, 47–77% 저렴. 독립 검증 필요.

## 패턴 2 — fresh-context 서브에이전트로 context rot 완화

`open-gsd/gsd-core` 는 무거운 작업마다 **매번 깨끗한 200k 컨텍스트의 서브에이전트**에서 돌려 누적 오염(context rot)을 줄이는 경량 메타프롬프팅 프레임워크다.

- 루프: **Discuss → Plan → Execute → Verify → Ship** 5단계.
- 상태는 대화 컨텍스트가 아니라 `STATE.md` · `CONTEXT.md` 아티팩트에 외부화 → 서브에이전트는 매번 신선한 컨텍스트로 시작하되 필요한 상태만 파일로 받는다.
- Claude Code·Codex·Gemini CLI 등에 두루 적용 가능.

> 핵심: 긴 세션의 품질 저하는 "컨텍스트를 비우고, 상태를 파일로 넘긴다"로 구조적으로 푼다. 이는 research/write 분리에서 dossier 가 계약 역할을 하는 것과 같은 발상(상태를 명시적 아티팩트로 외부화)이다.

## 패턴 3 — 요구사항 주도 워크플로 단계화

`KunAgent/Kun` 은 **요구사항 명확화 → 문서 → 설계 → 실행계획 → 에이전트 코딩·검수**로 단계를 고정한 워크스페이스. Code/Write 모드, MCP·Skills, IM 연동 지원. 단 **PolyForm Noncommercial 1.0.0(비상업 전용)** 라이선스라 상업 도입 시 제약 확인 필수 — 트렌딩 에이전트 도구는 라이선스가 제각각(미명시·비상업 전용 다수)이라 도입 전 점검이 패턴화된 체크포인트다.

## 패턴 4 — 재사용 가능한 디버그·리뷰 스킬 정의

`9arm-skills` 의 스킬 정의는 그 자체로 범용 휴리스틱:

- `debug-mantra`: **재현 → 실패 경로 추적 → 가설 반증**.
- `scrutinize`: 외부 시점(outside-view) 리뷰로 자기 출력 비판.

벤더 스킬 포맷이 **폴더 + Markdown** 형태로 수렴(Codex 가 Anthropic 스킬 형식 채택)하면서, 이런 스킬은 특정 에이전트에 종속되지 않고 이식 가능해지는 추세다.

## 관련 맥락

- research/write 분리([research-write-agent-separation](research-write-agent-separation.md))의 dossier 계약과 패턴 2 의 STATE/CONTEXT 외부화는 "상태를 명시적 아티팩트로 분리"라는 공통 원리.
- 강/약 모델 분리는 [multi-llm-provider-adapter-pattern](multi-llm-provider-adapter-pattern.md)·[onemancompany-heterogeneous-agents-organization](onemancompany-heterogeneous-agents-organization.md) 의 이기종 모델 오케스트레이션과 같은 맥락.

## 변경 이력

- 2026-06-15: 최초 생성. dev-blog Opensource Trending/Curation dossier(2026-06-15 실행)에서 추출한 재사용 가능 패턴 정리. 개별 트렌딩 프로젝트의 시간성 정보는 의도적으로 제외.
