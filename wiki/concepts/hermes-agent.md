---
title: "Hermes Agent — Nous Research 의 self-hosted personal AI agent"
domain: personal
sensitivity: public
tags: ["concept", "ai-agent", "self-hosted", "hermes", "nous-research", "personal-assistant"]
created: 2026-05-02
updated: 2026-05-09
sources:
  - "session-logs/20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
  - "session-logs/20260509-001610-307a-hermes-agent-를-설치했는데-메인-agent-말고-별도로-코딩-전용-agent를.md"
confidence: high
related:
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
  - "wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md"
  - "wiki/analyses/personal-ai-agent-messaging-channels.md"
  - "wiki/analyses/multi-profile-cli-agent-isolation.md"
  - "wiki/projects/hermes.md"
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

## 멀티 프로필 (`hermes profile`)

하나의 hermes 인스톨에 여러 개의 격리된 인스턴스를 운용할 수 있다.

```bash
hermes profile list
hermes profile create <name> [--clone | --clone-all]
hermes profile alias <name>     # /Users/<user>/.local/bin/<name> wrapper 생성
hermes profile use <name>       # sticky default
```

- 각 프로필은 `~/.hermes/profiles/<name>/` 에 자체 `config.yaml` / `.env` / `SOUL.md` / `memories/` / `sessions/` / `cron/` / `kanban.db` 보유
- launchd / systemd 도 프로필별로 별 plist (`ai.hermes.gateway-<name>.plist`) 가 깔려 동시 가동 가능
- `--clone` 은 config / .env / SOUL / skills 만 복사, **메모리·세션은 fresh**
- OAuth 토큰 (`auth.json`) 은 클론 대상 외 — 공유하려면 [[multi-profile-cli-agent-isolation]] 의 symlink 패턴
- 자식 프로세스 spawn 시 **`HOME` 이 profile dir 로 격리** 됨 (subprocess 가 외부 CLI 의 Keychain/OAuth 를 못 보는 함정)

실 셋업 사례는 [[hermes]] (project) 의 `maccoder` 코딩 전용 프로필 참조.

## 빌트인 코딩 위임 — `claude-code` skill

`claude-code` (autonomous-ai-agents 카테고리) 가 builtin 으로 enabled. hermes terminal tool 이 `claude -p "..."` 를 단순 subprocess 로 spawn 해서 결과를 회수하는 구조. ACP 가 끼어 있는 게 아니다.

- print mode: `claude -p '...' --max-turns N --allowedTools Bash` — JSON 출력 (`--output-format json`) 으로 session_id / cost / turns 회수
- tmux 모드: 인터랙티브 세션 위임 (skill SKILL.md 에 패턴)
- 동일 패턴으로 `codex` / `opencode` / `hermes-agent` (자기 자신) skill 도 builtin

사용자의 Claude Max OAuth 를 그대로 활용하려면 HOME 격리 우회 wrapper 가 필요하다 ([[multi-profile-cli-agent-isolation]] 참조).

## ACP 지원 — 방향 비대칭

`hermes acp` 는 **stdio ACP server 모드** 만 제공 (VS Code / Zed / JetBrains 가 hermes 를 부르는 방향). hermes 가 다른 ACP server 를 부르는 client 모드는 없다. claude CLI 자체도 ACP 미채택 (바이너리에 키워드 0건). 따라서 hermes ↔ claude 직결 ACP 경로는 현재 둘 중 어느 쪽에도 없고, subprocess (claude-code skill) 가 현실적인 선택지.

## 관련 페이지

- [[anthropic-oauth-third-party-billing-trap]] — Hermes 같은 third-party CLI 가 Anthropic OAuth 를 쓸 때의 빌링 함정
- [[llm-provider-aggregator-vs-local-vs-hub]] — OpenRouter / Ollama / HuggingFace 의 차이 (Hermes provider 선택 가이드)
- [[personal-ai-agent-messaging-channels]] — Telegram 이 이런 부류의 agent 에서 사실상의 표준이 된 이유
- [[multi-profile-cli-agent-isolation]] — hermes 멀티 프로필 셋업에서 일반화한 OAuth 공유 / HOME 격리 우회 / shell init 함정
- [[hermes]] (project) — 실제 default + maccoder 운영 기록

## 변경 이력

- 2026-05-02: 최초 생성. Hermes Agent 의 컨셉 / 기능 / 시스템 요구사항 / 설치 / LLM 백엔드 / 메신저 게이트웨이 의의 정리 (출처: session-logs/20260502-092045-628d-*)
- 2026-05-09: 멀티 프로필 (`hermes profile`) / `claude-code` skill / ACP 방향 비대칭 (server-only) 추가. multi-profile-cli-agent-isolation / hermes (project) 와 cross-link. confidence: medium → high (출처: session-logs/20260509-001610-307a-*)
