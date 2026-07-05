---
title: "다중 에이전트 공유 지식베이스 — 동시 쓰기 위험과 완화"
domain: "ai-agent"
sensitivity: "public"
tags: ["analysis", "ai-agent", "llm-wiki", "shared-knowledge-base", "concurrency", "curation", "git"]
created: "2026-07-05"
updated: "2026-07-05"
sources:
  - "session-logs/20260704-132738-e509-지금-세션에서-작업했던-hermes-webui-설치가-pc-를-껏다켜니-접속이-안되네-다시.md"
confidence: high
related:
  - "wiki/analyses/personal-llm-wiki-curation.md"
  - "wiki/analyses/karpathy-claude-md-skills.md"
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/weak-model-agent-reliability-compounding.md"
---

# 다중 에이전트 공유 지식베이스 — 동시 쓰기 위험과 완화

여러 에이전트가 같은 파일 기반 위키 폴더를 공유하는 구성의 위험과 안전한 형태. Hermes 멀티에이전트 실험에서 실측 (2026-07-04) — Hermes 는 글로벌 `~/.hermes/.env` 의 `WIKI_PATH` 한 줄로 전 프로필이 위키를 공유하며(파일 기반이라 자연 공유), news 에이전트가 쓴 개념 페이지를 maccoder 가 즉시 읽는 것까지 확인됨. `llm-wiki` 는 Hermes 번들 스킬(Karpathy 패턴 구현, MIT)로 제공된다.

## 위험 — 다중 동시 쓰기는 원본 패턴의 실험적 확장이다

Karpathy 원본 LLM wiki 패턴이 상정한 기본형은 **단일 에이전트 + 사람 큐레이션**이다 ([[personal-llm-wiki-curation]]). 여러 에이전트의 동시 쓰기는 두 가지 축에서 깨진다:

1. **물리적 충돌** — `index.md`/`log.md` 같은 공유 진입 파일에 락 없이 동시 쓰기 → 덮어쓰기·깨짐.
2. **논리적 오염** — 조율 없는 다중 쓰기로 중복·모순 페이지가 누적 → 지식베이스 품질이 서서히 저하. (단, 모델 자체가 오염되는 것이 아니라 **참조 시 답 품질만 흐려지는 가역적 데이터 문제**라는 구분이 중요 — git revert·큐레이션으로 복구 가능.)

## 더 안전한 형태 2가지

| 형태 | 구조 | 비고 |
|------|------|-----|
| (a) 섹션 소유권 분담 | news 는 뉴스 섹션만, coder 는 기술 섹션만 쓰기 | 충돌 면적 축소 |
| (b) **단일 큐레이터** | 쓰기 전담 1 에이전트, 나머지는 읽기 전용 | 실무에서 흔한 형태, 원본 패턴에 가장 가까움 |

## 안전망 (형태와 무관하게 권장)

- **위키 폴더 git 추적** — 어떤 에이전트가 무엇을 바꿨는지 추적 + revert 가능.
- **주기적 lint cron** — 모순·중복·깨진 링크 점검. 단 **페이지 삭제는 금지**하고 보고만 (자동 삭제는 비가역), 실행 전 git 스냅샷.

## 변경 이력

- 2026-07-05: 최초 생성 — Hermes 전 프로필 위키 공유 실측과 동시 쓰기 리스크 검토에서 정리 (출처: session-logs/20260704-132738-e509-*)
