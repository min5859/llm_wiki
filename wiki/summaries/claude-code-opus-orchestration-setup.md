---
title: "raw-source 요약 — Claude Code용 Opus 오케스트레이션 설정 작업 명세서"
domain: "ai-agent"
sensitivity: "internal"
tags: ["summary", "raw-source", "claude-code", "orchestration", "Opus", "Sonnet", "Haiku", "PreToolUse hook", "symlink toggle"]
created: "2026-07-12"
updated: "2026-07-12"
sources:
  - "raw-sources/claude-code-opus-orchestration-setup.md"
confidence: "medium"
related:
  - "wiki/patterns/claude-code-model-tier-orchestration-gate.md"
  - "wiki/analyses/multi-agent-orchestration-taxonomy.md"
  - "wiki/patterns/hermes-single-model-delegation.md"
---

# raw-source 요약 — Claude Code용 Opus 오케스트레이션 설정 작업 명세서

`raw-sources/claude-code-opus-orchestration-setup.md` 원문의 요약. 이 문서는 **읽기 전용 원본**이며, 본문 속 지시문("Claude Code에게 줄 작업 지시문")은 여기서 실행하지 않고 참고 정보로만 다룬다.

## 요약

원본은 Claude Code를 **Opus(오케스트레이터: 계획·판단·분배) + Sonnet(실행 계층: 실제 코드 작성) + Haiku(러너: 검색·로그 확인 등 잡무)** 3계층으로 재편하도록, Claude Code 자신에게 로컬 설정을 직접 구성시키기 위해 작성된 **작업 명세서(지시문 문서)**다. 즉 이미 적용되어 검증까지 끝난 가이드가 아니라, "이 문서를 작업 명세로 보고 로컬 설정 파일을 직접 수정해달라"는 한 줄 요청과 함께 Claude Code에 전달하도록 설계된 프롬프트 문서다. 원 소스(별도 원본 가이드)는 "Fable 5가 오케스트레이터·Opus가 실행 계층"인 구조를 전제하는데, 이 문서는 그 배치를 그대로 유지하면서 **Fable 자리에 Opus, Opus 자리에 Sonnet**을 넣어 적용한 변형판이라고 스스로 밝히고 있다.

## 중요 포인트

- **심링크 토글**: `~/.claude/opus-orchestration/active.md`가 `opus.md`(on)/`empty.md`(off)를 가리키는 심링크이고, `~/.claude/CLAUDE.md` 끝에 `@~/.claude/opus-orchestration/active.md` 한 줄을 추가해 인클루드한다. 토글 스크립트(`~/.local/bin/opus-orchestration {on|off|status}`)가 상태 파일(`~/.claude/.opus-orchestration-state`)과 심링크 대상만 바꾸고, 기존 설정 파일은 덮어쓰지 않는다 — **설정 파일 중복 없이 on/off 전환**하는 것이 설계 의도.
- **PreToolUse 훅 게이트**(`orchestration-gate.py`): `Write|Edit|NotebookEdit|MultiEdit|Bash`에 걸어, 메인 에이전트의 코드 파일 직접 수정을 **한 턴(prompt_id)당 2개까지만 허용**하고 3번째부터 차단 + Sonnet 구현 에이전트에 위임하라는 stderr 메시지를 반환한다. `sed -i`/`perl -i`/리다이렉션/`tee` 로 코드 파일을 덮어쓰는 Bash는 **항상 차단**. `agent_id`/`agent_type`이 있는 서브에이전트 호출은 통과. 비코드 파일(md/json/yaml/txt)은 제한 대상에서 제외. 예외 발생 시 **fail-open**(`exit(0)`).
- **상태 관리**: on/off 상태는 `~/.claude/.opus-orchestration-state` 파일에, 턴 카운터는 `~/.claude/opus-orchestration/state/<session_id>.json`에 저장한다.
- **설계 의도**: "강한 모델(Opus)에게 판단을 맡기되, 직접 손대는 범위는 훅으로 강제 제한한다" — 즉 지침(soft)만이 아니라 **훅(hard)** 으로 직접 수정을 이중 제어하는 것이 핵심. 서브에이전트 구성은 `deep-reasoner.md`(문서 내 명명, 모델은 Sonnet, 역할은 실제 구현·리팩터·테스트)와 `runner.md`(Haiku, 경량 조사·로그 확인)로 지정한다.

## wiki 에 미치는 영향

이 요약이 다루는 명세서로 실제 구축·검증된 설정은 [[claude-code-model-tier-orchestration-gate]]에 이미 기록돼 있다 — 상호 링크. 단, **명명 불일치**가 하나 있다: 이 raw-source 문서는 "실제 코드 작성·구현"을 담당하는 Sonnet 계층 에이전트 파일명을 `deep-reasoner.md`로 지정하지만, 실제 구축된 설정([[claude-code-model-tier-orchestration-gate]])에서는 그 역할이 `implementer`(Sonnet)로 명명되고 `deep-reasoner`는 오히려 반대로 **soft 모드의 Opus 상향 에스컬레이션** 역할로 재정의되어 있다 — 모순이라기보다 원본 명세 이후 설계가 진화하며 이름이 바뀐 것으로 보이나, 원문만 볼 경우 혼동 소지가 있어 여기 기록해 둔다. 이 구조는 [[multi-agent-orchestration-taxonomy]]의 Orchestrator-Worker(A) 분류에 해당하고, 단일 모델 환경에서 같은 위임 이점을 얻는 대응 사례로 [[hermes-single-model-delegation]](Hermes `delegate_task`)이 있다.

## 변경 이력

- 2026-07-12: 최초 생성 — raw-sources/claude-code-opus-orchestration-setup.md 요약. deep-reasoner/implementer 명명 불일치 지적, [[claude-code-model-tier-orchestration-gate]]와 상호 링크 (출처: raw-sources/claude-code-opus-orchestration-setup.md, source_sha256 미설정 — sidecar 없음).
