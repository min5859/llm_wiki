---
title: "Hermes Agent — Nous Research 의 self-hosted personal AI agent"
domain: personal
sensitivity: public
tags: ["concept", "ai-agent", "self-hosted", "hermes", "nous-research", "personal-assistant"]
created: 2026-05-02
updated: 2026-06-20
sources:
  - "session-logs/20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
  - "session-logs/20260509-001610-307a-hermes-agent-를-설치했는데-메인-agent-말고-별도로-코딩-전용-agent를.md"
  - "session-logs/20260617-220010-47ab-내가-해커톤-주제를-구체화-시키고-있는데-좀-도와줘.md"
  - "session-logs/20260618-063919-3962-지금-프로젝트-시작-지침서-인데-완성도를-평가해줘.md"
  - "session-logs/20260620-080358-9eaa-지금-프로젝트에-아래-요구사항을-추가할-수있는지-검토.md"
confidence: high
related:
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
  - "wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md"
  - "wiki/analyses/personal-ai-agent-messaging-channels.md"
  - "wiki/analyses/multi-profile-cli-agent-isolation.md"
  - "wiki/analyses/self-hosted-agent-webui-integration.md"
  - "wiki/projects/hermes.md"
  - "wiki/projects/hermes-dashboard.md"
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

## 내장 API 서버 — 커스텀 프론트엔드의 정식 통로 (2026-06-17 검증)

Hermes 와 통신하는 방식은 셋으로 갈리는데, 헷갈리면 안 된다:

| 표면 | 용도 | 커스텀 웹앱이 쓸 것? |
|---|---|---|
| **Gateway** | Telegram/Slack 등 22개 소비자 메신저 연동 | ✗ (메신저용) |
| **SSH** | Hermes 의 *실행 백엔드*(명령 실행 위치) 옵션 | ✗ (통신 방식 아님) |
| **API 서버** | OpenAI 호환 HTTP/SSE — 커스텀 프론트가 붙는 통로 | ✅ |

- **활성화**: `.env` 에 `API_SERVER_ENABLED=true` + `API_SERVER_KEY`. 기본 포트 `:8642`.
- **OpenAI 호환**: `POST /v1/chat/completions` (SSE 스트리밍). OpenAI 클라이언트면 무엇이든 붙는다.
- **`model` 필드는 장식용**: 요청별 프로필 선택이 **안 된다.** 실제 프로필은 게이트웨이 시작 시 `config.yaml` 로 고정 → **프로필(=특화 에이전트) 1개 = 게이트웨이 프로세스 1개 = 포트 1개.** N개 특화 에이전트를 동시에 real 로 띄우려면 N개 게이트웨이를 N개 포트에 띄워야 한다.
- **Runs API = 위임/lifecycle 이벤트의 출처**: `POST /v1/runs` → `run_id` → `GET /v1/runs/{run_id}/events`(SSE) 에서 토큰 + **sub-agent 위임 lifecycle 이벤트**가 나온다. `chat/completions` 스트림엔 `hermes.tool.progress` 정도만 온다. 멀티 에이전트 위임을 UI 로 시각화하려면 이쪽을 봐야 한다.
- **스킬 API 있음**: `GET /v1/skills` (+ `/v1/toolsets`, `/v1/capabilities`). 단 OpenAI 호환 API 서버(`:8642`)에는 **Kanban HTTP 엔드포인트가 없다**. (Kanban 은 아래 dashboard 서버에 별도로 노출됨 — 2026-06-20 정정)
- **세션 영속 + export**: 프로필별 `state.db`(SQLite, `~/.hermes/profiles/<name>/`) 에 전체 메시지 히스토리(역할·내용·tool call·타임스탬프·토큰수·소스 태그) 저장. 세션 ID 가 `YYYYMMDD_HHMMSS_<hex>` 포맷이라 **날짜가 ID 에 박혀 주 단위 슬라이싱이 된다**(`sessions list` 에 `--since` 없음 → ID prefix 로 필터). 전체 export 는 `hermes -p <profile> sessions export out.jsonl` (stdout 아님, JSONL 파일 출력).

> **멀티 에이전트 오케스트레이션은 Hermes 네이티브 기능**이다. 위임받는 sub-agent 는 **리더 프로필 한 프로세스 내부**의 것이지, 별도로 띄운 다른 게이트웨이가 아니다. 따라서 위임 이벤트의 sub-agent 이름이 외부 레지스트리의 에이전트 id 와 다를 수 있다. 커스텀 UI 는 위임 로직을 새로 짤 게 아니라 Runs API 이벤트를 받아 *시각화* 만 하면 된다.

실제 이 API 표면 위에 커스텀 대시보드를 설계한 사례는 [[hermes-dashboard]] (project), 셀프호스팅 에이전트 UI 가 백엔드에 붙는 두 방식 비교는 [[self-hosted-agent-webui-integration]].

### HTTP 서버는 둘 — API 서버(`:8642`)와 dashboard(`:9119`) (2026-06-20 정정)

API 서버(`:8642`)와 별개로 `hermes dashboard` 가 띄우는 **웹 dashboard 서버(기본 `:9119`)** 가 존재한다. 빌드/버전에 따라 둘 중 하나만 켜져 있을 수 있어 환경 검증이 필요하다 (실측 환경: `API_SERVER_ENABLED` 미설정으로 8642 꺼짐, dashboard 9119 가동). dashboard 검증 시 함정:

- `/v1/models`, `/api/plugins/kanban/tasks` 등 대부분 경로가 무인증 `curl` 에 **200 을 주지만 본문은 SPA 의 `index.html`**(JSON API 아님). 200 OK 만 보고 "API 살아있다"고 오판하기 쉽다.
- 인증은 페이지에 임베드된 세션 토큰을 써야 하고, 임의 `Bearer`/`Cookie` 를 붙이면 `Invalid HTTP request received` 가 떠 별도 핸드셰이크가 필요.

**네이티브 Kanban 은 존재한다** — `hermes kanban list` CLI 로 즉시 동작 확인되며, dashboard 의 `/api/plugins/kanban/` HTTP 경로로도 노출된다. 저장소는 `~/.hermes/kanban.db`(SQLite) 로 **전 프로필 공유**. (앞선 2026-06-18 의 "Kanban API 없음" 은 8642 API 서버에 한정된 사실이었고, dashboard 서버 + CLI 에는 있다.)

### 멀티에이전트 구성 — 2가지 방식 (2026-06-20)

| | 방식 A: `delegate_task` | 방식 B: 공유 Kanban |
|---|---|---|
| 구조 | 게이트웨이 1개 안에서 익명 일회용 subagent 를 즉석 spawn → 병렬 처리 후 결과 취합 (orchestrator-worker) | 이름 붙은 영속 프로필 봇들(각자 게이트웨이 1개)이 `~/.hermes/kanban.db` 로 협업 |
| 제어 | `config.yaml` 의 `max_concurrent_children`(기본 3) / `max_spawn_depth`(기본 1) / `orchestrator_enabled` | 디스패처가 담당 프로필을 atomic claim → 게이트웨이 자동 기동 → 처리 |
| 자식 컨텍스트 | 부모 대화를 모름. `goal`/`context` 만 받아 요약만 반환(토큰 절약) | 프로필별 메모리 독립, 보드로만 인계 |

실사용 권장 조합: **영속 특화봇 몇 개(B) + 그룹방 내 병렬은 `delegate_task`(A) + 봇 간 인계는 공유 Kanban**. 위임 lifecycle 시각화는 위 Runs API 절 참조.

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
- 2026-06-18: **내장 API 서버** 섹션 신설 — 통신 3표면(Gateway/SSH/API 서버) 구분, OpenAI 호환 `:8642`, `model` 필드 장식용·프로필=게이트웨이=포트, Runs API 가 위임 lifecycle 이벤트 출처, `GET /v1/skills` 있음·Kanban API 없음, 세션 `state.db`·ID `YYYYMMDD_HHMMSS`·`sessions export`, 오케스트레이션은 리더 프로필 내부 sub-agent. hermes-dashboard 해커톤 설계에서 소스·공식문서로 검증 (출처: session-logs/20260617-220010-47ab-*, 20260618-063919-3962-*)
- 2026-06-20: **HTTP 서버 둘(8642 API vs 9119 dashboard)** 구분 + **"Kanban API 없음" 정정** — 네이티브 Kanban 은 `hermes kanban` CLI + dashboard `/api/plugins/kanban/` 로 존재(`~/.hermes/kanban.db` 전 프로필 공유), 8642 한정 사실이었음. dashboard 검증 함정(SPA fallback 200·세션 토큰 인증) 추가. **멀티에이전트 2방식**(`delegate_task` orchestrator-worker vs 공유 Kanban) 표 신설. hermes-dashboard 포크 검토 세션에서 코드로 확인 (출처: session-logs/20260620-080358-9eaa-*)
