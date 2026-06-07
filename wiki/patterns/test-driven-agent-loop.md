---
title: "테스트 주도 에이전트 루프 — 강건한 테스트 스위트로 코딩 에이전트 자율 위임"
domain: "both"
sensitivity: "public"
tags: ["AI coding agent", "Codex CLI", "test-driven", "agent loop", "마이그레이션", "포팅", "검증 게이트"]
created: "2026-06-08"
updated: "2026-06-08"
sources:
  - "session-logs/20260608-033356-dbf4-AI-Coding-Agents-Newsletter.md"
confidence: "high"
related:
  - "wiki/analyses/openai-codex-cli-overview.md"
  - "wiki/analyses/research-write-agent-separation.md"
  - "wiki/patterns/llm-json-parse-retry-with-dump.md"
---

# 테스트 주도 에이전트 루프 — 강건한 테스트 스위트로 코딩 에이전트 자율 위임

코딩 에이전트(Codex CLI, Claude Code 등)에 대규모 작업을 자율적으로 맡기는 성공 패턴은 "작업을 강건한 테스트 스위트로 환원한 뒤 에이전트 루프를 풀어놓는 것"이다. 통과/실패가 기계적으로 판정되는 테스트가 있으면 에이전트는 실패 → 수정 → 재실행을 사람 개입 없이 반복할 수 있다.

## 핵심 내용

Simon Willison 의 JustHTML 포팅 사례(Python HTML 파서 → JavaScript)가 이 패턴의 실증이다.

- Codex CLI + GPT-5.2 로 약 8개 프롬프트·4시간 동안 43커밋, 9,000줄 JS 를 생성
- 결과물은 `html5lib-tests` 9,200개를 통과하는 무의존성 라이브러리
- 시작 프롬프트는 원본 코드베이스를 읽고 **새 라이브러리의 사용자 API 를 설계**하게 하는 것

> "If you can reduce a problem to a robust test suite you can set a coding agent loop loose on it."

> "Start by reading ~/dev/justhtml and designing the user-facing API for the new library"

(출처: https://simonwillison.net/2025/Dec/15/porting-justhtml/ — dossier evidence, verified)

## 패턴의 구성 요소

1. **검증 게이트의 객관성**: 표준 적합성 테스트(html5lib-tests 같은)나 골든 출력 비교처럼, LLM 판단이 아니라 결정론적 통과/실패가 있어야 루프가 수렴한다.
2. **API 우선 설계**: 1:1 줄단위 번역이 아니라 먼저 목표 API 를 설계시켜야 에이전트가 구조를 잡고 점진적으로 채운다.
3. **루프 자율성**: 사람은 프롬프트 몇 개와 방향만 주고, 테스트 실패 수정은 에이전트가 반복한다. (이 사례는 8 프롬프트로 9,000줄)

## 적용 후보

- **언어 간 포팅/마이그레이션**: 원본의 동작이 테스트로 고정되어 있을 때 (파서, 직렬화기, 프로토콜 구현 등)
- **레거시 재작성**: 회귀 테스트 스위트가 먼저 갖춰진 모듈
- **사양 적합성 구현**: 표준 conformance suite 가 존재하는 영역(HTML/CSS/JSON/암호화 KAT 등)

테스트 스위트가 빈약하면 이 패턴은 역효과 — 에이전트가 "통과처럼 보이는" 코드를 만들고 실제 결함이 남는다. 그래서 **선행 투자 순서는 테스트 정비 → 에이전트 위임**이다.

## 관련 맥락

- 본 프로젝트 전역 규칙의 "test-based success criteria"(버그 → 실패 테스트 먼저, 통과시키기)와 같은 철학을 에이전트 자동화로 확장한 것.
- 에이전트가 산발적으로 무응답/잘못된 출력을 낼 때의 완화는 [[llm-json-parse-retry-with-dump]] 의 retry-with-dump 와 함께 간다.
- 도구 측면은 [[openai-codex-cli-overview]] 참조.

## 변경 이력

- 2026-06-08: 최초 생성 — dev-blog AI 코딩 에이전트 뉴스레터 dossier(JustHTML 포팅 사례) 인제스트. 출처: session-logs/20260608-033356-dbf4-*
