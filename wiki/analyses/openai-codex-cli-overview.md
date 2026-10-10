---
title: "OpenAI Codex CLI — 터미널 경량 코딩 에이전트와 로컬 모델 연결"
domain: "ai-agent"
sensitivity: "public"
tags: ["Codex CLI", "OpenAI", "AI coding agent", "CLI", "로컬 모델", "Gemma", "Ollama", "Claude Code 대안"]
created: "2026-06-08"
updated: "2026-10-10"
sources:
  - "session-logs/codex-019d91a6-e931-7bb3-acfd-12bc49260658.md"
  - "session-logs/codex-019d96b5-ecf1-74f2-860c-0d277a094067.md"
  - "session-logs/codex-019e3b82-158c-7c41-a94e-907f9fb5ff38.md"
  - "session-logs/codex-019fdf6f-a6bc-7570-9d19-0a712511078b.md"
  - "session-logs/codex-01a125b5-9d9a-7392-96fa-f91459bb4130.md"
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

## 실제 운영에서 확인한 구분 (2026-04~10)

`codex exec`는 비대화형 정리에 사용할 수 있다. 설치된 0.160.1에서 `--ephemeral`, `--output-last-message`, `--output-schema`, read-only/workspace-write 및 lifecycle hooks 지원을 확인했다. [[codex-session-capture-and-curation]]의 gieok 전환은 실제 질문·응답 캡처와 Claude 로그 104건 정리로 검증했다.

컨텍스트 창 사용량, 누적 토큰, 구독의 시간별 사용량 한도는 서로 다르다. 4월 상태라인 구현에서는 좁은 tmux 영역에 중요한 ctx/rate가 잘려 보였고 앞쪽 배치와 폭 조정으로 해결했다. 큰 컨텍스트 설정이 사용량 한도를 늘려주는 것은 아니다.

8월 dev-blog 사례에서 터미널과 LaunchAgent가 서로 다른 nvm global Codex 설치본을 사용했다. Node 버전별 global 패키지와 예약 PATH를 직접 확인해야 한다. 인증 만료, 오래된 CLI/모델 호환성, MCP 인증, 구독 사용량 제한을 같은 장애로 묶지 않는다.

## 변경 이력

- 2026-10-10: 미수집 Codex 세션의 최종 결과·정정·적용 경계를 검토해 보강. 출처는 frontmatter의 codex 세션 목록 참조.

- 2026-06-08: 최초 생성 — dev-blog AI 코딩 에이전트 뉴스레터 dossier 인제스트(Codex CLI 소개 + 로컬 Gemma 4 실험). 출처: session-logs/20260608-033356-dbf4-*
