---
title: "OpenAI Codex CLI — 터미널 경량 코딩 에이전트와 로컬 모델 연결"
domain: "ai-agent"
sensitivity: "public"
tags: ["Codex CLI", "OpenAI", "AI coding agent", "CLI", "로컬 모델", "Gemma", "Ollama", "Claude Code 대안"]
created: "2026-06-08"
updated: "2026-06-08"
sources:
  - "session-logs/20260608-033356-dbf4-AI-Coding-Agents-Newsletter.md"
confidence: "medium"
related:
  - "wiki/analyses/cursor-agent-cli-overview.md"
  - "wiki/analyses/everything-claude-code.md"
  - "wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md"
  - "wiki/patterns/test-driven-agent-loop.md"
---

# OpenAI Codex CLI — 터미널 경량 코딩 에이전트와 로컬 모델 연결

OpenAI 가 터미널에서 로컬 실행되는 경량 코딩 에이전트 Codex CLI 를 공개했다. Claude Code 의 직접적인 터미널 에이전트 경쟁 제품이며, ChatGPT 플랜 또는 API 키로 사용하고 custom provider 설정으로 로컬·오픈 모델까지 붙일 수 있다는 점이 평가 축이다.

## 핵심 내용

- 정체: "Lightweight coding agent that runs in your terminal" (출처: https://github.com/openai/codex)
- 구현: 주로 Rust(약 96%), Mac·Linux·Windows 지원, VS Code·Cursor·Windsurf 확장 제공
- 인증: ChatGPT 플랜 또는 API 키
- 비교축: Rust 기반 로컬 실행 + ChatGPT 플랜 통합 → Claude Code 대비 비용·이식성 비교의 핵심

## 로컬·오픈 모델 연결 (custom provider)

Daniel Vaughan 의 실험: Codex CLI 의 custom model provider(`config.toml`, `wire_api="responses"`)로 로컬 Gemma 4 를 연결.

- M4 Mac → llama.cpp 26B MoE, Dell GB10 → Ollama 31B Dense
- 클라우드 GPT-5.4 가 65초에 끝낸 작업을 로컬은 수 분 소요
- **교훈: 로컬 실행에서는 생성 속도보다 first-pass 신뢰도가 더 중요**

> "first-pass reliability mattered more than raw generation speed"

- Apple Silicon 함정: 약 500토큰 초과 프롬프트에서 Flash Attention freeze 로 Ollama 가 행(hang)

> "a Flash Attention freeze hangs Ollama on any prompt longer than about 500 tokens"

(출처: https://blog.danielvaughan.com/i-ran-gemma-4-as-a-local-model-in-codex-cli-7fda754dc0d4 — dossier confidence medium, quote 검증 실패 표시 있음. 단정 금지, 재현 시 직접 확인 필요)

## 평가자 관점 시사점

- Codex CLI 는 사실상 로컬·오픈 모델 클라이언트로도 쓸 수 있다 → 데이터 프라이버시·비용 절감 노선에서 후보.
- 단, 로컬 모델의 first-pass 신뢰도와 도구 호출(tool-call) 스트리밍 안정성이 한계. Apple Silicon 의 Flash Attention freeze 는 실사용 차단 요인이 될 수 있다.
- 멀티 provider 어댑터([[cursor-agent-cli-overview]], [[multi-llm-provider-adapter-pattern]]) 관점에서 claude·cursor 에 이어 Codex CLI 를 또 하나의 print-mode 어댑터로 검토 가능.

## 관련 맥락

- 강건한 테스트가 있으면 Codex CLI 루프를 대규모 포팅에 자율 위임 가능 → [[test-driven-agent-loop]] (JustHTML 포팅 사례).
- 로컬 vs 클라우드 vs 허브 선택 기준은 [[llm-provider-aggregator-vs-local-vs-hub]].

## 변경 이력

- 2026-06-08: 최초 생성 — dev-blog AI 코딩 에이전트 뉴스레터 dossier 인제스트(Codex CLI 소개 + 로컬 Gemma 4 실험). 출처: session-logs/20260608-033356-dbf4-*
