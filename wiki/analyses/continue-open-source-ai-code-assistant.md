---
title: Continue — 오픈소스 AI 코드 어시스턴트 (VS Code / JetBrains / CLI)
domain: personal
sensitivity: public
tags: [analysis, oss, ai, coding-assistant, copilot-alternative, vscode, jetbrains, cli]
created: 2026-05-17
updated: 2026-05-17
source_session: 20260517-090150-c808-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md
sources:
  - "session-logs/20260517-090150-c808-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md"
confidence: medium
related:
  - "wiki/analyses/everything-claude-code.md"
  - "wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md"
---

# Continue — 오픈소스 AI 코드 어시스턴트

## 개요

- **레포**: https://github.com/continuedev/continue
- **CLI 패키지**: `@continuedev/cli` (npx 로 `cn` 실행)
- **VS Code 확장**: extensions/vscode/
- **JetBrains 플러그인**: extensions/intellij/
- **라이선스**: Apache-2.0 © 2023-2024 Continue Dev, Inc.

「자체 LLM 으로 동작하는 GitHub Copilot 대안」 — 다양한 LLM provider (Anthropic, OpenAI, Ollama, Gemini, OpenRouter 등) 를 backend 로 붙여 IDE·CLI 에서 코드 자동완성·채팅·에디트·에이전트 워크플로를 제공.

## 핵심 특징

- **provider-agnostic** — `config.yaml` 로 model/provider 자유 설정 (Ollama 로컬, Anthropic, OpenAI, OpenRouter 등)
- **3가지 UI 진입점** — VS Code 확장 / JetBrains 플러그인 / CLI (`cn`)
- **자동완성 + 채팅 + 에디트 + 에이전트** — 4가지 사용 모드, 모드별 다른 모델 지정 가능 (예: 자동완성은 빠른 로컬 모델, 에이전트는 Claude Opus)
- **컨텍스트 프로바이더** — 코드, docs, git diff, 파일, terminal, codebase 등 IDE/repo 컨텍스트를 자동 첨부
- **설정 hub** — hub.continue.dev 에서 커뮤니티 프리셋 공유

## 사용 시나리오

- Copilot 의 가격·정책에 의존하지 않고 자체 LLM 운용 (사내 LLM, Ollama 로컬 등)
- multi-model 워크플로 (자동완성용 빠른 로컬 + 에이전트용 Opus 같은 분리)
- 프라이버시 민감 코드베이스의 100% 로컬 코딩 어시스턴트 (Ollama 백엔드)
- 다양한 provider 비교 테스트 (Continue 한 곳에서 swap)

## 기술 스택

- **언어**: TypeScript 중심
- **UI**: VS Code Extension API, IntelliJ Platform SDK, ink/React 기반 TUI (CLI)
- **백엔드**: provider 추상화 레이어, BYOK (Bring Your Own Key) 모델
- **MCP 지원** — Model Context Protocol 서버 연결로 외부 도구 호출

## 평가

- **성숙도**: Apache-2.0, 회사 (Continue Dev, Inc.) 운영, IDE 마켓플레이스 등록, 활발한 GitHub Discussions
- **트렌드**: 「Copilot alternative」 카테고리에서 가장 IDE 친화. Aider (CLI 위주), Cursor (포크 IDE) 와 대비
- **CLI 위상 강화** — `npx @continuedev/cli` 로 진입 장벽 낮춤, claude code / openclaw 같은 CLI 에이전트 시장과의 경쟁

## 한계·주의점

- IDE 통합이 복잡 → 자동완성 지연/오류는 provider+IDE 양쪽 디버깅 필요
- VS Code 측 권한 모델 (workspace trust 등) 에 영향 받음
- 자체 LLM 운용은 GPU 인프라 비용을 사용자가 책임
- 4 사용 모드의 학습 곡선 — Copilot 대비 설정 항목 많음

## 관련

- 비교: [[everything-claude-code]] (Claude Code 자체 하네스 / 스킬 시스템)
- 비교: [[multi-llm-provider-adapter-pattern]] (provider abstraction 패턴)
- 비교: [[llm-provider-aggregator-vs-local-vs-hub]] (provider 카테고리 분류)

## 변경 이력

- 2026-05-17: oss-radar 자동 분석 로그 기반 신규 작성 (cron 분석 미생성 상태에서 README 직접 요약)
