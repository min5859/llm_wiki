---
title: "멀티 프로필 CLI agent 격리 — OAuth 공유 / HOME 격리 우회 / shell init 함정"
domain: "ai-agent"
sensitivity: public
tags: ["analysis", "cli-agent", "oauth", "isolation", "wrapper", "macos", "keychain", "symlink"]
created: 2026-05-09
updated: 2026-05-09
source_session: 20260509-001610-307a-hermes-agent-를-설치했는데-메인-agent-말고-별도로-코딩-전용-agent를.md
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/projects/hermes.md"
  - "wiki/patterns/launchd-secret-management.md"
  - "wiki/patterns/cron-nvm-node-path-trap.md"
---

## 개요

CLI 기반 personal agent (hermes / openclaw / claude / codex 등) 가 **profile / workspace / sandbox** 형태로 자기 영역을 격리할 때 발생하는 3대 함정과 그 우회 패턴. hermes 의 maccoder 프로필 셋업 (2026-05-09) 에서 도출했지만, profile-scoped HOME 또는 자식 프로세스용 환경 격리를 가진 모든 CLI agent 에서 재현된다.

## 1. OAuth credential 공유 — symlink vs copy

같은 머신에서 **두 프로필이 같은 LLM provider OAuth 를 쓰려면** 토큰 파일을 어떻게 공유하느냐가 핵심.

| 방식 | 동작 | 위험 |
|---|---|---|
| 새 OAuth 따기 | 프로필별 독립 토큰 | provider 가 멀티 세션 무효화하면 한쪽 끊김. **refresh-token 회전 충돌** (양쪽이 동시에 refresh 돌리면 한쪽 토큰이 invalidate) |
| 단순 복사 (cp) | 양쪽이 별 사본 | refresh-token 회전 후 한쪽이 stale 됨 → 두 사본이 점점 어긋남 |
| **symlink** ✅ | 디스크에 한 파일만 존재 | 회전이 일어나도 양쪽이 항상 같은 토큰 |

추가로 lock 파일 (`auth.lock` 등) 도 같이 symlink 하면 **동시 refresh 가 직렬화** 되어 race condition 0.

### 적용 예 (hermes maccoder)

```bash
# default 의 auth.json 을 maccoder 가 공유
ln -s ~/.hermes/auth.json   ~/.hermes/profiles/maccoder/auth.json
ln -s ~/.hermes/auth.lock   ~/.hermes/profiles/maccoder/auth.lock
```

이 패턴은 codex / opencode / 같은 OAuth-스토어를 가진 모든 CLI 에 그대로 통한다.

## 2. HOME 격리 + Keychain 인증의 충돌

여러 CLI agent (hermes 가 그 예) 는 자식 프로세스를 spawn 할 때 **`HOME` 을 profile dir 로 격리**한다. 이는 다른 도구를 호출했을 때 그 도구의 인증 lookup 을 깨뜨리는 흔한 함정이다.

### 재현 케이스: hermes 프로필 → claude CLI 위임

- `claude` 는 OAuth 토큰을 `~/.claude/.credentials.json` + **macOS Keychain** 에 저장
- hermes 가 `claude -p "..."` 를 spawn 할 때 `HOME=~/.hermes/profiles/maccoder/home/` 로 격리
- → `claude` 가 `~/.claude/` 를 못 봄 + Keychain lookup 도 격리된 HOME 기준으로 해서 실패
- 증상: `claude auth status` 가 "Not logged in"

### 우회 — wrapper 가 HOME 만 복원

```bash
# ~/.hermes/profiles/maccoder/home/bin/claude
#!/bin/sh
HOME=/Users/wooki exec /Users/wooki/.local/bin/claude "$@"
```

이 wrapper 를 PATH 앞단에 배치하면 자식이 정상 인증된다. **다른 도구의 격리는 그대로 유지**되는 게 장점.

### 검토했지만 효과 없는 우회

- `~/.claude` / `~/.claude.json` 를 profile home 안에 symlink → **부족**. Keychain 경로는 HOME 기준이라 lookup 이 격리 HOME 으로 잡힘
- `--allow-unrestricted-keychain-access` 같은 옵션 → CI 전용 escape hatch, 실 사용자 환경에선 권장 X
- HOME 격리 자체를 끄기 (`HERMES_HOME_MODE` 등) → profile 격리 의의 상실

## 3. shell init file 의 zsh / bash 비대칭

CLI agent 가 자식 쉘을 띄울 때 source 하는 init 파일이 **macOS 기본 쉘 (zsh)** 과 다른 경우가 많다.

| Agent | Source 하는 파일 | 함정 |
|---|---|---|
| hermes (terminal tool) | `auto_source_bashrc=True` → `.bashrc` / `.bash_profile` 만 | macOS 기본 `.zshrc` 는 무시됨 |
| cron (BSD/Linux) | 아무것도 안 source | NVM node 경로 등 모두 PATH 누락 ([[cron-nvm-node-path-trap]]) |
| launchd | 환경변수 plist 직접 또는 명시 source | 비슷 ([[launchd-secret-management]]) |
| GitHub Actions | login shell 아님 | `~/.zshrc` 무시 |

### 적용 패턴

profile-local PATH 주입은 **항상 `.bash_profile` (또는 그 agent 가 명시한 init file) 에** 작성한다. `.zshrc` 에 쓰면 hermes / cron 자식 프로세스가 못 본다.

```bash
# ~/.hermes/profiles/maccoder/home/.bash_profile
export PATH="$HOME/bin:$PATH"
```

## 4. 운영 함정 — `--clone` 의 환경변수 지나침

`profile create --clone` (또는 동등한 복사 명령) 은 보통 `.env` 까지 통째로 복사한다. **gateway / API key 등 프로필 고유 자원은 첫 부팅 시 반드시 reconfigure** 해야 두 프로필이 충돌 없이 운영된다.

- Telegram bot token 두 프로필이 공유 → 둘 다 깨지거나 한쪽만 메시지 받음
- API key 가 같은 vendor 의 같은 quota 를 두 프로필이 경쟁

`--clone` 직후의 첫 setup 위저드에서 `Reconfigure? y` 가 정상 답.

## 결론 — 체크리스트

새 CLI agent 의 프로필을 분리할 때 다음 4가지를 하나하나 확인:

1. **OAuth 토큰 파일** — 공유할 거면 symlink (lock 도 같이). 별도 OAuth 낼 거면 refresh-token 충돌 정책을 provider 문서로 확인.
2. **외부 CLI 도구 위임** — 그 도구가 Keychain / `~/.config/` / `~/.claude/` 처럼 HOME 기준 lookup 을 하는지 strings + 코드 grep 으로 검증. 깨지면 wrapper 로 HOME 복원.
3. **shell init 파일** — agent 가 source 하는 파일이 어떤 건지 docs / config 또는 코드 (`auto_source_bashrc` / `shell_init_files` 등) 로 확인. 그 파일에 PATH 주입.
4. **`.env` 등 환경변수 파일** — `--clone` 직후 첫 setup 에서 반드시 reconfigure. 아니면 두 프로필이 같은 토큰 / 같은 채널을 공유.

## 관련 페이지

- [[hermes-agent]] — 본 분석의 발견 환경. multi-profile / claude-code skill / `hermes acp` server 모드
- [[hermes]] (project) — 실제 maccoder 프로필 셋업 결과
- [[launchd-secret-management]] — 비대화 셸 환경의 환경변수 / secret 함정 패밀리
- [[cron-nvm-node-path-trap]] — 같은 셸 init 함정의 cron 변형
- [[anthropic-oauth-third-party-billing-trap]] — third-party CLI 의 Anthropic OAuth 함정 (provider 정책의 다른 면)
