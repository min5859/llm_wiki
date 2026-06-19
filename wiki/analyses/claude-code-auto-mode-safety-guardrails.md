---
title: "Claude Code auto 모드 안전 가드레일 — 파괴적 명령 차단 (v2.1.183 기준)"
domain: both
sensitivity: public
tags: ["claude-code", "auto-mode", "safety", "git", "terraform", "destructive-ops", "guardrail", "release-notes"]
created: 2026-06-20
updated: 2026-06-20
sources:
  - "session-logs/20260620-032649-a796-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md"
  - "https://github.com/anthropics/claude-code/releases/tag/v2.1.183"
confidence: medium
related:
  - "wiki/concepts/claude-code-overview.md"
  - "wiki/patterns/claude-code-advanced.md"
  - "wiki/analyses/claude-code-source-leak-internals.md"
---

# Claude Code auto 모드 안전 가드레일 (v2.1.183 기준)

Claude Code 의 **auto 모드**(사용자 확인 없이 도구를 실행하는 모드)에서, 사용자가 명시적으로 요청하지 않은 **파괴적·비가역 명령을 에이전트가 차단**하도록 강화된 동작. dev-blog 의 "AI Coding Agents" dossier(2026-06-20)가 수집한 v2.1.183 릴리스 노트에서 추출. auto 모드를 일상적으로 쓰는 운영자(본인)에게 직접 영향이 있는 도구 동작 변화라 기록.

> 출처는 dossier 가 조사한 릴리스 노트 텍스트(단일 출처) — 실제 적용 동작은 자기 환경에서 한 번 확인 권장(confidence: medium). 원본: `github.com/anthropics/claude-code/releases/tag/v2.1.183`.

## 차단되는 파괴적 작업 (명시 요청 시에만 예외 허용)

- **파괴적 git** — `git reset --hard`, `git checkout -- .`, `git clean -fd`, `git stash drop` 은 사용자가 *로컬 변경을 버리라고 직접 요청하지 않은 경우* 차단.
- **세션 외 커밋 amend** — `git commit --amend` 는 *이번 세션에서 에이전트가 만든 커밋이 아니면* 차단(남의/과거 커밋 변조 방지).
- **명명 안 된 인프라 destroy** — `terraform destroy` / `pulumi destroy` / `cdk destroy` 는 *특정 스택을 지정해 요청하지 않으면* 차단.

핵심 원리: **"되돌릴 수 없는 작업은 명시적 의도가 있을 때만"**. auto 모드의 편의(확인 생략)와 안전(파괴 차단)을 분리해, 일상 작업은 자동화하되 비가역 작업만 게이트로 남긴다.

## 함께 들어온 관련 수정 (운영 영향 있는 것만)

- deprecated/자동 교체된 모델 사용 시 **stderr 경고** — `-p`(print mode) 와 **agent frontmatter 에 박아둔 모델**까지 커버. 오래된 모델 ID 를 쓰던 스크립트·서브에이전트 정의가 조용히 다른 모델로 바뀌는 것을 감지 가능.
- subagent spawn 시 `thinking.disabled` 로 인한 **`Extra inputs are not permitted` 400 에러** 수정.
- **subagent 에서 WebSearch 가 빈 결과 반환**하던 버그 수정 — research/write 파이프라인의 WebSearch 교차검증(cf. [[research-write-agent-separation]])에 직접 영향.
- 모델이 **thinking 블록만 반환하고 출력 없이 턴 종료**되던 silent 완료 → 1회 재프롬프트로 수정.
- headless/SDK 모드에서 **인증 필요 MCP 서버의 auth-stub 도구가 모델에 노출**되던 문제 수정.

## 관련 맥락

- [[claude-code-overview]] — CC 기본 개념·자율도
- [[claude-code-advanced]] — 고급 사용 패턴
- [[research-write-agent-separation]] — subagent WebSearch 빈결과 수정이 이 파이프라인에 영향

## 변경 이력

- 2026-06-20: 최초 생성 — dev-blog "AI Coding Agents" dossier(2026-06-20)가 수집한 CC v2.1.183 릴리스 노트에서 auto 모드 파괴적 명령 차단 + 운영 영향 수정 추출. dossier 의 HN 가십 클러스터(소스 leak·OpenClaw 거부 논란 등)는 미검증 루머라 제외, 검증 가능한 릴리스 노트 사실만 수록 (출처: session-logs/20260620-032649-a796-*)
