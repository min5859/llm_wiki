---
title: "Hermes Agent — Nous Research 의 self-hosted personal AI agent"
domain: personal
sensitivity: public
tags: ["concept", "ai-agent", "self-hosted", "hermes", "nous-research", "personal-assistant"]
created: 2026-05-02
updated: 2026-05-02
sources:
  - "session-logs/20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
confidence: medium
related:
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
  - "wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md"
  - "wiki/analyses/personal-ai-agent-messaging-channels.md"
---

# Hermes Agent

**Nous Research** 가 개발한 오픈소스 self-hosted AI 에이전트. 챗봇이나 코딩 코파일럿이 아니라, **사용자의 서버 (VPS / 개인 머신) 에서 항상 살아있으면서, 사용 시간이 누적될수록 점점 사용자에 fit 되어가는 영속적 personal agent** 라는 컨셉이다.

- Repo: `github.com/NousResearch/hermes-agent`
- License: **MIT**
- 공식 사이트: hermes-agent.org

## 위치 짓기

「Claude Code 같은 코딩 어시스턴트」, 「ChatGPT 같은 클라우드 챗봇」 과는 결이 다르다. 더 가까운 비유는 **개인용 Jarvis** — 단일 호출의 우수성보다, **상시 가동 + 누적 학습 + 멀티 채널** 이라는 운영 형태에 의의가 있다.

| 차원 | Hermes | Claude Code | Cursor / Copilot |
|---|---|---|---|
| 주 용도 | 일상의 personal assistant | 코딩 작업 | 코딩 작업 |
| 호스팅 | self-hosted (사용자 서버) | 로컬 CLI | 로컬 IDE |
| 메모리 | persistent (세션 넘어 누적) | 세션 단위 | 세션 단위 |
| 채널 | CLI + 메신저 다수 | CLI 한정 | IDE 한정 |
| 자가 학습 | skill 자가생성 | (사용자가 작성) | (없음) |

## 주요 기능

| 항목 | 내용 |
|---|---|
| **Persistent Memory** | 세션을 넘어 선호·프로젝트·환경을 기억. 오래 쓸수록 사용자에 fit |
| **Skill 자가생성** | 어려운 문제를 풀면 스스로 재사용 가능한 skill 문서를 작성. agentskills.io 표준 호환 |
| **Multi-Platform Gateway** | Telegram / Discord / Slack / WhatsApp / Signal / CLI 를 단일 gateway 로 통합 |
| **Sub-agent 병렬 실행** | 자식 에이전트 spawn 가능 |
| **Browser Control + Vision** | 브라우저 제어 및 이미지 분석 |
| **Scheduling** | cron 기반 자동화 |
| **로컬 우선** | 텔레메트리 없이 모든 데이터 로컬 보관, container hardening |

## LLM 백엔드

200+ 모델 지원. Provider lock-in 없음:

- Nous Portal (자체 호스팅)
- OpenRouter (다중 vendor 중계)
- OpenAI / Anthropic 등 직 API
- 로컬 vLLM, OpenAI 호환 커스텀 엔드포인트
- Codex CLI OAuth (`~/.codex/auth.json` 자동 import)
- Anthropic OAuth (단, 큰 함정 있음 → [[anthropic-oauth-third-party-billing-trap]])

## 시스템 요구사항

- **OS**: Linux / macOS / WSL2 (Android 는 Termux). **네이티브 Windows 미지원**
- **Python**: 3.11+
- **패키지 매니저**: uv (설치 스크립트가 자동 설치)
- **Git** 필요

## 설치 경로

```bash
# 원라인 (시스템 전역)
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash

# 또는 수동 (소스 학습/커스터마이징 목적)
git clone https://github.com/NousResearch/hermes-agent
cd hermes-agent
./setup-hermes.sh
```

설치 전 결정 사항:

1. **사용할 LLM provider** — Nous Portal / OpenRouter / Anthropic / OpenAI / 로컬. interactive config 단계에서 묻는다. API 키 미리 준비
2. **Gateway 연동 여부** — Telegram / Slack 등 메신저. CLI 만 써도 동작. 나중에 추가 가능

## 메신저 게이트웨이의 의의

Hermes 의 핵심 use case 는 **「VPS 에 hermes 띄워두고 외출 중에도 시킨다」** — 이게 Telegram bot 과 가장 잘 맞아서, 커뮤니티에서 채널 점유율이 압도적이다. 자세한 비교는 [[personal-ai-agent-messaging-channels]].

## 관련 페이지

- [[anthropic-oauth-third-party-billing-trap]] — Hermes 같은 third-party CLI 가 Anthropic OAuth 를 쓸 때의 빌링 함정
- [[llm-provider-aggregator-vs-local-vs-hub]] — OpenRouter / Ollama / HuggingFace 의 차이 (Hermes provider 선택 가이드)
- [[personal-ai-agent-messaging-channels]] — Telegram 이 이런 부류의 agent 에서 사실상의 표준이 된 이유

## 변경 이력

- 2026-05-02: 최초 생성. Hermes Agent 의 컨셉 / 기능 / 시스템 요구사항 / 설치 / LLM 백엔드 / 메신저 게이트웨이 의의 정리 (출처: session-logs/20260502-092045-628d-*)
