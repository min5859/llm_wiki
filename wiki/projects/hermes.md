---
title: "hermes — Nous Research personal AI agent 셋업 (개인 머신)"
domain: personal
sensitivity: public
tags: ["project", "hermes", "ai-agent", "self-hosted", "telegram", "macos"]
created: 2026-05-09
updated: 2026-05-09
sources:
  - "session-logs/20260509-001610-307a-hermes-agent-를-설치했는데-메인-agent-말고-별도로-코딩-전용-agent를.md"
  - "session-logs/20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
confidence: high
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/multi-profile-cli-agent-isolation.md"
  - "wiki/analyses/personal-ai-agent-messaging-channels.md"
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
---

# hermes — 개인 머신 셋업 프로젝트

프로젝트명: hermes (cwd: `toy/hermes`).
[[hermes-agent]] (Nous Research) 를 macOS 미니에 셋업하고 운영하는 개인 프로젝트.
2026-05-03 에 base 설치, 2026-05-09 에 코딩 전용 추가 프로필 `maccoder` 도입.

## 구성

| 프로필 | 용도 | 모델 | Telegram 봇 |
|---|---|---|---|
| `default` | 일반 personal assistant | gpt-5.5 (OpenAI Codex OAuth) | 일반 봇 |
| `maccoder` | 코딩 위임 전용 | gpt-5.5 (default 와 OAuth 공유) | 별도 봇 (BotFather 신규) |

CLI: `hermes` (default), `maccoder` (= `hermes -p maccoder`).
launchd: `~/Library/LaunchAgents/ai.hermes.gateway.plist`, `ai.hermes.gateway-maccoder.plist` (각각 별 plist 로 동시 가동).

## maccoder 셋업 핵심 결정 5가지

(2026-05-09, 일반 패턴은 [[multi-profile-cli-agent-isolation]] 으로 분리)

1. **`hermes profile create maccoder --clone`** — default 의 `config.yaml` / `.env` / `SOUL.md` / skills 만 복사. 메모리 / 세션 / cron / kanban 은 fresh.
2. **OAuth 공유**: `auth.json` + `auth.lock` 을 default 로 **symlink** (복사 X). refresh-token 회전 시 양쪽이 항상 같은 토큰을 보고, lock 도 같이 링크되어 동시 refresh 가 직렬화됨.
3. **Claude CLI 통합**: `claude` 는 OAuth 토큰을 macOS Keychain 에 저장하므로 hermes 의 HOME 격리 (subprocess HOME = `~/.hermes/profiles/maccoder/home/`) 와 충돌. 해결책은 profile-local wrapper `home/bin/claude` 가 `HOME=/Users/wooki` 로 복원해서 `claude -p ...` 호출. 사용자의 Claude Max 인증 그대로 활용.
4. **shell init 함정**: hermes terminal tool 은 `auto_source_bashrc=True` 로 `.bashrc` / `.bash_profile` 만 source 한다 (macOS 기본 zsh 와 무관). 따라서 PATH 주입은 `.zshrc` 가 아닌 `home/.bash_profile` 에 작성.
5. **Telegram gateway 별도 봇**: BotFather 에서 새 봇 생성 후 `maccoder gateway setup` 위저드 → bot token / Allowed user IDs (`5910428983`) / Home channel. `--clone` 이 default 의 `.env` 까지 복사하므로 **첫 setup 에서 토큰을 반드시 reconfigure** 해야 두 봇이 충돌 없이 운영됨.

## 파일 레이아웃 (maccoder)

```
~/.hermes/profiles/maccoder/
├── config.yaml             # default 에서 클론 후 분리
├── .env                    # 별도 Telegram bot token
├── auth.json → ~/.hermes/auth.json   # symlink (Codex OAuth 공유)
├── auth.lock → ~/.hermes/auth.lock   # symlink
├── SOUL.md                 # "맥 코더" 페르소나 (한국어, 시니어 톤)
├── home/
│   ├── .bash_profile       # PATH 에 home/bin 우선 주입
│   ├── bin/claude          # HOME 복원 wrapper
│   ├── .claude → ~/.claude            # symlink (안 쓰임 / Keychain 경로 별개)
│   └── .claude.json → ~/.claude.json  # symlink
├── memories/, sessions/, logs/, ...
~/Library/LaunchAgents/
└── ai.hermes.gateway-maccoder.plist  # default 와 별개 plist
```

## ACP 방향성 (2026-05-09 확인)

| 방향 | 지원 |
|---|---|
| ACP **server** (에디터/외부 클라이언트가 hermes 를 부른다) | ✅ `hermes acp` (stdio) — VS Code / Zed / JetBrains 통합 |
| ACP **client** (hermes 가 다른 ACP 서버를 부른다) | ❌ — `hermes acp --help` 에 client 모드 없음 |
| `claude` CLI 의 ACP | ❌ — 바이너리 strings 에 `acp` / `agent-client-protocol` 키워드 0건 |

결론: hermes ↔ claude 를 ACP 로 직결할 경로는 **둘 중 어느 쪽에도 없음**. 현재 maccoder 는 hermes terminal tool 이 `claude -p "..."` 를 단순 subprocess 로 spawn 하는 구조 (`--output-format json` 으로 session_id / cost / turns 회수 가능). 토큰 스트리밍이나 실시간 tool call 가시화가 필요해지면 그때 ACP server 모드 검토.

## End-to-end 검증 (2026-05-09)

- `maccoder -z "안녕"` → "맥 코더" 페르소나 한국어 응답
- `maccoder -z "claude -p ... python --version"` → wrapper 경유 OAuth 복원 → claude CLI Bash → `Python 3.12.12` 회수
- launchd: `ai.hermes.gateway-maccoder` PID active, `[Telegram] Connected to Telegram (polling mode)`
- error log 비어 있음

## 운영 명령어

```bash
# 상태·로그
maccoder gateway status
tail -f ~/.hermes/profiles/maccoder/logs/gateway.log

# 정지·재시작
maccoder gateway stop
maccoder gateway restart

# 자동 시작 해제 (제거 X, 비활성화)
maccoder gateway uninstall
```

## 알려진 함정 (이번 셋업 회복 메모)

- `--clone` 후 `auth.json` 재사용 시도 시: 단순 복사면 refresh-token 회전 충돌. 반드시 symlink.
- `claude auth status` 가 "Not logged in" 으로 떨어지면: HOME 격리 미해결 → wrapper 부재 또는 `.bash_profile` 미배치를 의심.
- `gateway setup` 에서 첫 reconfigure 묻는 질문에 `n` 누르면 default 봇 토큰 재사용 → 두 봇 충돌. 반드시 `y`.
- `client_secret_*.json` (Google OAuth) 등 secret 파일은 `.gitignore` 에 패턴 추가 (이번 세션에서 추가됨, commit `62aec7d`).

## 변경 이력

- 2026-05-09: 최초 생성. base hermes (default) 위에 코딩 전용 `maccoder` 프로필 추가, claude CLI 통합 / Telegram 별도 봇 / launchd 분리 plist. README + tasks/todo.md Review 섹션 commit `9feb783`, `62aec7d` (출처: session-logs/20260509-001610-307a-*)
