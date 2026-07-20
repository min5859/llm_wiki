---
title: "hermes — Nous Research personal AI agent 셋업 (개인 머신)"
domain: "ai-agent"
sensitivity: public
tags: ["project", "hermes", "ai-agent", "self-hosted", "telegram", "macos"]
created: 2026-05-09
updated: 2026-07-19
sources:
  - "session-logs/20260704-132738-e509-지금-세션에서-작업했던-hermes-webui-설치가-pc-를-껏다켜니-접속이-안되네-다시.md"
  - "session-logs/20260509-001610-307a-hermes-agent-를-설치했는데-메인-agent-말고-별도로-코딩-전용-agent를.md"
  - "session-logs/20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
  - "session-logs/20260523-000054-480c-hermes-가-응답이-없습니다.md"
  - "session-logs/20260603-143737-7275-hermes-에-연결된-AI-provider-인-codex-cli-인증이-만료된-것-같은데.md"
  - "session-logs/20260621-181256-3227-지금-hermes-agent-에-AI-provider-연결이-끊긴것-같은데-다시-연결-시켜.md"
  - "session-logs/20260719-211542-2222-hermes-에-aI-provider로-모델이-뭘로-설정되어-있지.md"
confidence: high
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/multi-profile-cli-agent-isolation.md"
  - "wiki/analyses/personal-ai-agent-messaging-channels.md"
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
  - "wiki/patterns/long-lived-network-client-stuck-reconnect-loop.md"
  - "wiki/analyses/oauth-refresh-token-rotation-multi-client.md"
  - "wiki/projects/openclaw.md"
---

# hermes — 개인 머신 셋업 프로젝트

프로젝트명: hermes (cwd: `toy/hermes`).
[[hermes-agent]] (Nous Research) 를 macOS 미니에 셋업하고 운영하는 개인 프로젝트.
2026-05-03 에 base 설치, 2026-05-09 에 코딩 전용 추가 프로필 `maccoder` 도입.

## 구성

| 프로필 | 용도 | 모델 (2026-07-19 기준) | Telegram 봇 |
|---|---|---|---|
| `default` (base `~/.hermes/config.yaml`) | 일반 personal assistant | gpt-5.6-sol, effort xhigh (OpenAI Codex OAuth) | 일반 봇 |
| `maccoder` | 코딩 위임 전용 | gpt-5.6-sol, effort xhigh (default 와 OAuth 공유) | 별도 봇 (BotFather 신규) |
| `architect`/`coder`/`designer`/`news`/`reporter`/`reviewer`/`trading` | 용도별 위임 프로필 (2026-07-19 시점 7개 추가 확인, 최초 도입 시점 미상) | gpt-5.6-sol, effort xhigh | 프로필별 개별 gateway |

`~/.hermes/profiles/` 아래 총 **8개 프로필**(위 표의 `maccoder` 포함 8개, `default` 는 base config 라 별도)이 존재하며, 각자 자체 `config.yaml`+gateway 를 가진다.

CLI: `hermes` (default), `maccoder` (= `hermes -p maccoder`), 그 외는 `hermes --profile <name>`.
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

## 운영 회고 (2026-05-23)

`hermes gateway` 가 응답 없음 → 진단 결과 **Telegram reconnect loop** 갇힘. 망은 정상 (`ping api.telegram.org` 228ms, `curl` HTTP 302), `gateway` 프로세스만 과거 끊김 시점부터 retry loop 누적 (1→2→4→8→15s 백오프). `hermes gateway restart` 가 1차 조치. 단, 재시작 직후에도 같은 패턴이 한 번 더 잡혔으므로 라이브러리/OS 레벨 stuck 가능성 — 후속 조사 필요.

일반 진단 패턴은 [[long-lived-network-client-stuck-reconnect-loop]].

## 운영 회고 (2026-06-03) — Codex OAuth 만료·쟁탈

Hermes 가 응답 없음 → `hermes auth list` 의 openai-codex 자격증명이 **exhausted**. 토큰 만료(access 2026-06-01) + `last_refresh` 가 05-22 에 멈춤.

- **원인**: Hermes 는 설치 때 codex 토큰을 `~/.hermes/auth.json` 에 **복사(import)** 해 독립 운용했다. codex CLI/openclaw 가 같은 ChatGPT 계정(`min5859@gmail.com`)으로 refresh 하면서 **회전형 refresh token** 이 회전 → Hermes 가 쥔 옛 체인이 무효화돼 갱신 불가. (gateway.log: `Codex refresh token was already consumed by another client`)
- **공유 구조 대조**: openclaw 는 `~/.codex/auth.json` 를 **포인터로 참조**(사본 없음)해 충돌 없음. hermes default↔maccoder 는 `auth.json`/`auth.lock` 을 **symlink** 공유. 하지만 hermes↔codex CLI 는 **복사** 구조라 정확히 회전 충돌 함정에 빠졌다.
- **해결**: `hermes login` 은 제거됨 → **`hermes auth add openai-codex --type oauth`** 로 자체 device-flow 재인증. 독립 토큰 체인을 가져 이후 codex/openclaw 가 갱신해도 안 끊긴다. 죽은 자격증명은 회전돼 무효라 `auth reset` 안 되고 `auth remove` 로 정리.
- openclaw 와 동시에 같은 계정을 쓰던 구조라, 둘 다 독립 device-flow 로 인증하니 핑퐁 소멸. (openclaw 쪽 기록 [[openclaw]])

일반 메커니즘·진단 함정·공유 방식 4가지 비교는 [[oauth-refresh-token-rotation-multi-client]].

## 운영 회고 (2026-06-21) — Codex OAuth 재만료·재인증 (6/3 재발)

6/3 와 동일 증상이 재발. `hermes auth list` 의 openai-codex 자격증명 2개가 모두 `exhausted` → gpt-5.5 호출 불가. `hermes auth add openai-codex --type oauth` device 재인증으로 새 자격증명 #3 활성화, `hermes doctor` "logged in" 확인. 옛 exhausted 항목 정리는 무해해 보류(`auth remove` 로 직접 가능). 6/3→6/21 반복이라 todo.md 에 주기적 재발 패턴으로 명시. 같은 시점 openclaw 도 동반 재발([[openclaw]]) — 결국 OpenAI 결제 미납 만료가 주원인이었고 셋(hermes·openclaw·Codex.app)이 같은 계정 공유. "쟁탈 vs 단순 만료" 구별·경쟁 앱 선종료 등 일반 분석은 [[oauth-refresh-token-rotation-multi-client]].

## 운영 회고 (2026-07-04) — 재부팅 복구와 `hermes update` 의 정체

PC 재부팅 후 webui 접속 불가 — 게이트웨이·webui 가 자동 기동 미등록 상태였다. `hermes --profile <p> gateway install` + webui launchd 등록으로 부팅 자동 기동 구성.

**`hermes update` 는 릴리스가 아니라 `origin/main` 을 pull 한다.** WebUI 가 "release 업데이트 가능(v2026.5.7)" 이라 안내하지만 실제 update 명령은 릴리스 태그가 아닌 개발 최신(main)으로 점프 — 실측 시 main 보다 7,408 커밋 뒤처져 있어 실행했다면 코어가 bleeding-edge 로 통째 이동했을 것. **안정 릴리스 고정을 원하면**: `git fetch --tags && git checkout v2026.5.x` 후 `uv pip install --python venv/bin/python -e .` + 게이트웨이 재시작. 부가 실측: (a) update 의 zip 백업은 기본 OFF (`--backup` 또는 config `updates.pre_update_backup: true` 필요, 가벼운 git 스냅샷은 항상 생성), (b) venv 는 **uv 관리라 pip 이 없다** — editable 재설치는 `uv pip install ... -e .`. "in-app 업데이터의 'release' 표기와 CLI 의 main-pull 이 다른 의미"라는 일반 함정.

## 운영 회고 (2026-07-19) — 프로필 8개 일괄 모델/effort 변경

"hermes 모델이 뭐로 설정돼있지" 질문에서 시작해, base + 프로필 8개(`architect`/`coder`/`designer`/`maccoder`/`news`/`reporter`/`reviewer`/`trading`) 전체를 `gpt-5.5`→`gpt-5.6-sol`, `agent.reasoning_effort: medium`→`xhigh` 로 일괄 변경.

- **base 설정은 프로필로 전파되지 않는다**: `~/.hermes/config.yaml`(base) 를 고쳐도 `~/.hermes/profiles/<name>/config.yaml` 8개는 별도 파일이라 반영 안 됨. 프로필마다 값을 각각 써넣어야 한다.
- **프로필마다 자체 gateway** — base 재시작은 프로필 gateway 에 영향 없음. `hermes --profile <name> gateway restart` 를 8번 반복해야 전체 반영.
- `delegation.reasoning_effort` 는 빈 값(`''`)으로 둬야 위임 시 `agent.reasoning_effort`(xhigh) 를 그대로 상속한다 — 굳이 채우지 말 것.
- **모델명 검증**: `openai-codex` provider 플러그인은 모델명을 화이트리스트 검증하지 않는 범용 프록시라, 카탈로그 캐시에 존재하는 모델명이면 설정 즉시 적용된다(사전 등록 불필요).
- zsh 함정: 배열 대신 unquoted 변수로 여러 프로필을 순회하면 word-split 이 안 돼 루프가 깨진다 — 명시적 배열(`profiles=(...)`)로 순회할 것.

## 변경 이력

- 2026-05-09: 최초 생성. base hermes (default) 위에 코딩 전용 `maccoder` 프로필 추가, claude CLI 통합 / Telegram 별도 봇 / launchd 분리 plist. README + tasks/todo.md Review 섹션 commit `9feb783`, `62aec7d` (출처: session-logs/20260509-001610-307a-*)
- 2026-05-23: gateway "응답 없음" 1건 — Telegram reconnect loop, 망 정상이었음. restart 1차 조치. 패턴 일반화는 [[long-lived-network-client-stuck-reconnect-loop]] (출처: session-logs/20260523-000054-480c)
- 2026-06-03: Codex OAuth 만료·쟁탈 회고 추가. hermes 가 codex 토큰을 복사(import)해 독립 운용 → 회전형 refresh token 회전으로 무효화. `hermes auth add openai-codex --type oauth` 로 자체 device-flow 재인증 (구 `hermes login` 제거됨). openclaw 와 동시 사용 시 독립 등록으로 핑퐁 해소. 일반 분석은 [[oauth-refresh-token-rotation-multi-client]] (출처: session-logs/20260603-143737-7275)
- 2026-06-21: Codex OAuth 재만료·재인증 회고 추가 (6/3 재발). `hermes auth add` device 재인증으로 복구, 주기적 재발 패턴으로 todo.md 명시. openclaw 동반 재발이며 주원인은 결제 미납 만료. 일반 분석 갱신은 [[oauth-refresh-token-rotation-multi-client]] (출처: session-logs/20260621-181256-3227)
- 2026-07-05: 재부팅 복구(gateway install + webui launchd 등록)와 `hermes update` = origin/main pull 실측 회고 추가. 안정 릴리스 고정 절차·uv venv(pip 없음)·백업 기본 OFF 명시. webui 업데이터의 "네트워크 오류" 위장 원인(launchd ssh-agent 부재)은 [[launchd-secret-management]] 로 분리 (출처: session-logs/20260704-132738-e509-*)
- 2026-07-19: 프로필이 2개(default+maccoder)에서 **8개**로 확장된 것을 확인, 구성 표 갱신. base+8프로필 전체 모델 `gpt-5.5`→`gpt-5.6-sol`, effort `medium`→`xhigh` 일괄 변경 회고 추가 — base config 는 프로필로 전파 안 됨·프로필마다 개별 gateway 재시작 필요·delegation.reasoning_effort 빈 값 유지가 핵심 (출처: session-logs/20260719-211542-2222-*)
