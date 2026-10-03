---
title: "Hermes 개인 머신 셋업 및 운영 이력 — 정본"
domain: "ai-agent"
sensitivity: internal
tags: ["project", "hermes", "ai-agent", "self-hosted", "telegram", "macos", "launchd", "operations"]
created: 2026-05-09
updated: 2026-10-03
sources:
  - "local-only: /Users/wooki/.hermes/config.yaml"
  - "local-only: /Users/wooki/.hermes/profiles/*/config.yaml"
  - "local-only: /Users/wooki/.hermes/cron/jobs.json"
  - "local-only: /Users/wooki/.hermes/profiles/news/cron/jobs.json"
  - "local-only: /Users/wooki/.hermes/profiles/maccoder/cron/jobs.json"
  - "local-only: /Users/wooki/.hermes/channel_directory.json"
  - "/Users/wooki/project/toy/hermes/CLAUDE.md"
  - "/Users/wooki/project/toy/hermes/README.md"
  - "/Users/wooki/project/toy/hermes/tasks/todo.md"
  - "/Users/wooki/project/toy/hermes/hermes-agent-update-2026-07-04.md"
  - "session-logs/20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
  - "session-logs/20260509-001610-307a-hermes-agent-를-설치했는데-메인-agent-말고-별도로-코딩-전용-agent를.md"
  - "session-logs/20260523-000054-480c-hermes-가-응답이-없습니다.md"
  - "session-logs/20260603-143737-7275-hermes-에-연결된-AI-provider-인-codex-cli-인증이-만료된-것-같은데.md"
  - "session-logs/20260621-181256-3227-지금-hermes-agent-에-AI-provider-연결이-끊긴것-같은데-다시-연결-시켜.md"
  - "session-logs/20260704-132738-e509-지금-세션에서-작업했던-hermes-webui-설치가-pc-를-껏다켜니-접속이-안되네-다시.md"
  - "session-logs/20260719-211542-2222-hermes-에-aI-provider로-모델이-뭘로-설정되어-있지.md"
confidence: high
related:
  - "wiki/bugs/hermes-webui-runtime-import-offline.md"
  - "wiki/concepts/hermes-agent.md"
  - "wiki/projects/hermes-dashboard.md"
  - "wiki/analyses/multi-profile-cli-agent-isolation.md"
  - "wiki/analyses/oauth-refresh-token-rotation-multi-client.md"
  - "wiki/analyses/self-hosted-agent-webui-integration.md"
  - "wiki/patterns/launchd-secret-management.md"
  - "wiki/patterns/single-dispatcher-per-queue.md"
---

# Hermes 개인 머신 셋업 및 운영 이력

이 문서는 macOS 개인 머신에서 운영 중인 [[hermes-agent|Hermes Agent]]의
**현재 셋업 상태와 변경 이력의 단일 기준 문서**다. 과거 운영 메모는
`/Users/wooki/project/toy/hermes`에 남아 있지만, 서로 다른 시점의 값이 섞여
있으므로 현재 상태를 파악할 때는 이 문서를 우선한다.

시크릿 값은 기록하지 않는다. `.env`, `auth.json`, API 키와 Telegram bot token은
런타임에서만 확인한다.

## 1. 현재 상태 요약

기준일: **2026-10-03 KST**. 버전·모델·추론 레벨·예약 작업은 당일 CLI와 파일로 재확인했다. WebUI는 당일 offline 장애 복구 후 메인 페이지·프로필 API HTTP 200을 확인했다. 개별 프로필 API 포트·인증 공유 구성은 2026-08-23 기록이며 이번에는 재검증하지 않았다.

| 항목 | 현재 값 |
|---|---|
| 설치 위치 | `~/.hermes/hermes-agent` |
| Hermes 버전 | `v0.21.5+6217.gd526f14 (2026.9.24)` |
| 체크아웃 HEAD | `d526f14714` |
| upstream 표시 | `d526f147`, CLI에서 Up to date 표시 |
| Python | `3.14.7` (Hermes CLI 출력) |
| Provider | `openai-codex` |
| 기본 모델 설정 | base + 8개 프로필 모두 `gpt-6.1-sol` |
| reasoning effort 설정 | base + 8개 프로필 모두 `medium` |
| 프로필 수 | base `default` + 하위 프로필 8개 = 총 9개 |
| 자동 시작 | 모든 gateway와 Hermes WebUI가 launchd에 등록됨 |
| Hermes WebUI | `127.0.0.1:8787` |
| 비밀값 로그 마스킹 | 모든 프로필 `security.redact_secrets: true` |

> 2026-08-23에는 전 프로필 `gpt-5.6-sol` / `high`였으나 2026-10-03에 위 설정으로 변경했다. 설정값과 실행 중 세션은 구분한다. 당일 `news`·`maccoder`는 Gateway 재시작과 `gpt-6.1-sol` cron 실행을 확인했지만, 나머지 하위 프로필의 기존 세션 모델까지 실검증한 것은 아니다. `maccoder` 자체 Telegram 봇은 없는 구성이다.

## 2. 현재 프로필·채널·포트 배치

프로필은 설정, 세션, 메모리, gateway 상태를 각각 가진다. 다만 LLM OAuth는
`auth.json` symlink로 base와 공유한다.

| 프로필 | 주 역할 | 외부 연결 | 현재 상태 |
|---|---|---|---|
| `default` | 일반 personal assistant | Telegram | connected |
| `coder` | specialist Telegram 진입점 + kanban worker | Telegram | connected |
| `maccoder` | 코딩/WebUI API backend | API `8642` | HTTP 200 |
| `news` | 뉴스 자동화 API | API `8643` | HTTP 200 |
| `trading` | 트레이딩 자동화 API | API `8644` | HTTP 200 |
| `architect` | 역할 프로필, kanban dispatcher | 없음 | gateway running |
| `designer` | 역할 프로필, kanban dispatcher | 없음 | gateway running |
| `reporter` | 역할 프로필, kanban dispatcher | 없음 | gateway running |
| `reviewer` | 역할 프로필, kanban dispatcher | 없음 | gateway running |

현재 연결 흐름은 다음과 같다.

```text
Telegram 일반 봇 ───────────────> default gateway
Telegram specialist 봇 ─────────> coder gateway
Hermes WebUI :8787 ─────────────> maccoder API :8642
뉴스/트레이딩 자동화 ────────────> news :8643 / trading :8644
내부 역할·칸반 작업 ─────────────> architect/designer/reporter/reviewer
```

중요한 불변식:

- 하나의 Telegram bot token은 동시에 gateway 하나만 polling해야 한다.
- API gateway는 프로필별 고유 포트를 가져야 한다.
- `API_SERVER_ENABLED=true`뿐 아니라 **`API_SERVER_KEY`가 존재하는 것만으로도**
  API 서버가 활성화된다.
- 플랫폼이 필요 없는 역할 프로필에는 Telegram token과 API server key를 두지 않는다.

## 3. 런타임 파일과 자동 시작 구조

### 2026-10-03 프로필 역할·최근 활동

아래는 각 프로필의 `SOUL.md`, `state.db`, cron 실행 기록 기준이다. 대화 응답과 실제 작업을 구분한다.

| 프로필 | 만든 목적 | 마지막 확인된 활동 |
|---|---|---|
| `default` | 일반 비서·Hermes 운영·프로젝트 지원 | 10/3 업데이트·설정 관리, AI 알림·Wiki 자동 커밋 실행 |
| `maccoder` | 코딩·디버깅·리팩토링, 필요 시 Claude Code 위임 | 10/3 14:11 Wiki lint 정상 완료 |
| `news` | AI·금융·개발·국내 뉴스 브리핑 | 10/3 14:21 브리핑 정상 완료(당시 local 저장) |
| `trading` | `ht_trading`·`n_stock_info` 로그·성과·코드 분석 | 7/5 동시 에이전트 운영 리스크 분석; 일일 보고 cron의 소유자는 default |
| `architect` | 칸반 상위 설계, `ARCHITECTURE.md` | 실제 작업 7/5 AI 환경 발표자료 설계, 마지막 대화 7/19 연결 테스트 |
| `designer` | 칸반 상세 설계, `DESIGN.md` | 7/5 AI 환경 HTML 발표자료 제작 |
| `coder` | 칸반 코드·테스트 구현 | 실제 작업 7/5 Hermes 실행 근거 수집, 마지막 대화 8/23 호출 응답 |
| `reviewer` | 설계·구현·테스트 검수 | 7/5 HTML 발표자료 검수 |
| `reporter` | 산출물 종합·최종 보고 | 7/5 AI 환경 발표자료 종합 |

칸반 기본 파이프라인은 `architect → designer → coder → reviewer → reporter`다. 로마숫자 변환기 PoC와 PC AI 환경 소개 자료에 사용했다. `coder`와 `maccoder`는 별도 프로필이다.

### 예약 작업·Telegram 토픽

| 작업 / 소유 프로필 | 일정(KST) | 전달 / 모델 정책 |
|---|---|---|
| Trading daily analysis report / `default` (`018e8af17160`) | 매일 21:00 | origin = 맥비 작업실 topic 25; `ht_trading`만 보고; model/provider pin 없음 |
| daily-news-brief / `news` (`1d128439bd39`) | 매일 19:00 | `telegram:-1003551298957:609` = 맥비 작업실 뉴스 토픽; model/provider pin 없음 |
| weekly-wiki-lint / `maccoder` (`da2387b35196`) | 일요일 20:00 | local 저장; 공유 `hermes-wiki` 점검; model/provider pin 없음 |

뉴스는 별도 Telegram 봇을 새로 만들지 않고 기존 맥비 작업실의 뉴스 토픽으로 전달하도록 설정했다. 10/3 토픽 609의 사용자 메시지 수신·맥비 응답과 저장된 deliver 값은 확인했다. **이 토픽으로 변경한 뒤 실제 cron 발송은 아직 미검증**이며, local 저장으로 성공한 14:21 실행과 구분한다. 트레이딩 프롬프트에서 Upbit 경로·분석·출력 항목을 모두 제거했으며, 변경 후 보고서 실행도 아직 미검증이다.

모델 해제는 CLI `hermes [--profile NAME] cron edit JOB_ID --unpin`을 사용한다. pin 없는 작업은 `cron.model` 설정을 우선하고, 없으면 해당 프로필의 기본 모델을 따른다. 당일 확인 범위에서 별도 cron 모델은 설정되어 있지 않았다.

```text
~/.hermes/
├── config.yaml                 # default 설정
├── .env                        # default 시크릿; 커밋 금지
├── auth.json / auth.lock       # OpenAI Codex OAuth
├── SOUL.md                     # default 페르소나
├── state.db / kanban.db        # 상태·공유 작업 보드
├── hermes-agent/               # 코어 git checkout + uv 관리 venv
├── profiles/
│   └── <name>/
│       ├── config.yaml         # 프로필별 독립 설정
│       ├── .env                # 프로필별 플랫폼 시크릿
│       ├── auth.json           # base auth.json symlink
│       ├── auth.lock           # maccoder만 base lock symlink, 나머지는 별도 파일
│       ├── SOUL.md
│       ├── state.db
│       └── logs/
└── logs/

~/Library/LaunchAgents/
├── ai.hermes.gateway.plist
├── ai.hermes.gateway-<profile>.plist
└── com.hermestalk.webui.plist
```

launchd에 등록된 서비스:

- `ai.hermes.gateway`
- `ai.hermes.gateway-{architect,coder,designer,maccoder,news,reporter,reviewer,trading}`
- `com.hermestalk.webui`

### 인증 공유 상태

8개 하위 프로필의 `auth.json`은 모두 `~/.hermes/auth.json`을 가리킨다.
`auth.lock`까지 base와 공유하는 프로필은 현재 `maccoder`뿐이고, 나머지는 프로필별
일반 파일이다. 같은 auth 파일을 여러 프로세스가 갱신하므로 lock도 함께 공유하는
것이 더 안전한지 후속 점검이 필요하다. 일반 원리는
[[multi-profile-cli-agent-isolation]] 참고.

### 시크릿 경계

- `.env`, `auth.json`, OAuth JSON, API 키는 git에 넣지 않는다.
- `com.hermestalk.webui.plist`는 현재 API 키를 환경변수로 보유한다. 값은 문서에
  적지 않으며, 장기적으로 전용 권한 `600` env 파일로 분리하는 편이 안전하다.
- 2026-08-22 이전 오류 로그는 secret redaction이 꺼진 상태에서 생성되었을 수 있다.
  압축본은 `chmod 600`으로 제한했다.

## 4. maccoder 특수 구성

`maccoder`는 2026-05-09에 default에서 분리한 코딩 전용 프로필이다.

핵심 결정:

1. `hermes profile create maccoder --clone`으로 시작했다. 설정·환경·SOUL은
   복제하지만 메모리·세션·cron·kanban은 새로 시작한다.
2. OpenAI Codex OAuth는 `auth.json`과 `auth.lock` symlink로 base와 공유한다.
3. Hermes의 profile-local HOME 때문에 Claude CLI의 macOS Keychain 인증이
   깨져, `home/bin/claude` wrapper가 Claude 호출 때만 실제 HOME을 복원한다.
4. Hermes terminal tool은 zsh 설정이 아니라 `.bash_profile` 계열을 읽으므로
   wrapper PATH는 `home/.bash_profile`에 둔다.
5. 처음에는 maccoder 전용 Telegram 봇을 사용했으나, 이후 역할 프로필 복제 과정에서
   동일 토큰이 여러 프로필로 퍼졌다. 2026-08-22 충돌 해결 후 현재는 Telegram을
   `coder`가 소유하고 `maccoder`는 WebUI API `8642`만 담당한다.

```text
~/.hermes/profiles/maccoder/
├── config.yaml
├── .env                         # API 8642 설정, Telegram token 없음
├── auth.json -> ~/.hermes/auth.json
├── auth.lock -> ~/.hermes/auth.lock
├── SOUL.md
└── home/
    ├── .bash_profile
    └── bin/claude               # 실제 HOME 복원 wrapper
```

Hermes와 Claude CLI 사이에는 ACP client 연결이 없다. 현재 위임은 Hermes terminal
tool이 `claude -p`를 subprocess로 실행하는 구조다. Hermes의 `hermes acp`는
에디터가 Hermes를 호출하는 ACP server 방향이다.

## 5. 일상 운영 Runbook

### 전체 상태

```bash
hermes version
hermes profile list
hermes gateway list
launchctl list | rg 'ai\.hermes\.gateway|com\.hermestalk\.webui'
```

### 프로필별 gateway

```bash
hermes --profile <name> gateway status
hermes --profile <name> gateway restart
tail -f ~/.hermes/profiles/<name>/logs/gateway.error.log
```

### API health와 포트 소유자

```bash
curl -fsS http://127.0.0.1:8642/health
curl -fsS http://127.0.0.1:8643/health
curl -fsS http://127.0.0.1:8644/health
lsof -nP -iTCP:8642-8644 -sTCP:LISTEN
```

정상 소유자는 `8642=maccoder`, `8643=news`, `8644=trading`이다.

### 설정 변경 절차

1. 대상 프로필의 `config.yaml`과 `.env`를 권한 보존 백업한다.
2. base 설정은 프로필로 자동 전파되지 않으므로 필요한 프로필을 모두 수정한다.
3. 프로필별 gateway를 재시작한다.
4. `hermes config check`, `gateway_state.json`, 포트 listener, 새 error log를 확인한다.
5. 최소 수십 초 동안 로그 크기가 계속 증가하지 않는지 관찰한다.

`gateway_state.json`은 플랫폼을 비활성화해도 이전 상태 항목이 남을 수 있다.
오래된 상태가 진단을 방해하면 gateway를 멈추고 파일을 백업 이름으로 이동한 뒤
재시작해 fresh state를 만든다.

### Codex OAuth가 끊겼을 때

```bash
hermes auth list
hermes auth add openai-codex --type oauth
```

`exhausted` 자격증명은 refresh token 자체가 무효일 수 있어 `auth reset`보다 새
device-flow 인증이 정석이다. 자세한 원인은
[[oauth-refresh-token-rotation-multi-client]] 참고.

### WebUI 업데이트 후 offline 복구·예방

- 원인·해결·재발 복구 명령은 [[hermes-webui-runtime-import-offline]] 참고.
- 10/3 기존 Python 3.11 서비스에서 최신 Hermes runtime으로 전환되며 `api` 모듈 경로를 잃어 서버가 반복 종료됐다. 현재 runtime과 WebUI 경로를 명시한 1차 복구 후 HTTP 200 확인.
- 버전 경로를 고정하지 않는 영구 런처 `~/.hermes/scripts/webui_runtime_launcher.py`를 만들고 import·서로 다른 runtime argv·별도 포트 2회 기동 테스트를 통과했다.
- plist는 `/usr/bin/python3` + 영구 런처로 수정하고 인증값 등 나머지 필드 보존을 확인했다. **외부 Mac 터미널 reload 후 production 활성화 검증 완료:** loaded service에 영구 런처 반영, running(PID 9157), 메인 페이지·프로필 API HTTP 200, 프로필 9개. 최초 bootstrap error 5는 제거 완료 후 단독 재등록으로 복구했다. 실제 채팅·다음 업데이트 버튼 테스트는 별도다.

### 코어 업데이트 절차

`hermes update`는 안정 릴리스가 아니라 `origin/main`을 따라간다. 2026-08-23의
10,193 commits behind 상태는 과거 기록이며, 2026-10-03 업데이트 후 CLI는 Up to date를 표시했다. 업데이트 전 백업·사용자 변경 보존·재시작 범위를 확인한다.

통제된 업데이트 순서:

1. 현재 HEAD에 rollback tag를 만든다.
2. zip 백업이 필요하면 `hermes update --backup --yes`를 명시한다.
3. 릴리스 고정이 필요하면 원하는 tag를 직접 checkout한다.
4. `uv pip install --python venv/bin/python -e .`로 editable install을 갱신한다.
5. 모든 프로필 gateway를 재시작하고 API·Telegram을 실검증한다.

2026-08-23에는 코어 저장소의 `package-lock.json`에 사용자 변경이 있었다. 10/3 문서 갱신에서는 해당 파일 상태를 재검증하지 않았으므로 후속 업데이트 전에 다시 확인한다.

## 6. 2026-08-22 gateway 충돌·로그 폭증 해결

### 발견 배경

디스크 사용량 조사 중 `~/.hermes`가 계속 증가하는 것을 확인했다. 당시 전체 크기는
약 `7,234.4 MiB`였고, 프로필 로그 디렉터리만 약 `958 MiB`였다.

주요 증상:

- 6개 프로필의 `gateway.error.log`가 각각 약 100~120 MiB까지 증가
- API `8642 already in use`가 프로필별 4천~1만 4천 회 반복
- Telegram bot token lock 충돌 반복
- launchd `KeepAlive` 아래에서 gateway 재시도·재시작이 계속됨
- `security.redact_secrets: false`라 로그에 키·토큰이 남을 가능성 존재

### 근본 원인

역할 프로필을 clone하면서 `.env`의 플랫폼 자격증명까지 복제됐다.

- `architect`, `coder`, `designer`, `maccoder`, `reporter`, `reviewer`가 같은
  specialist Telegram token을 공유했다.
- 같은 6개 프로필에 동일 `API_SERVER_KEY`가 남아 있었다.
- 첫 수정에서 `API_SERVER_ENABLED`만 제거했지만 충돌이 계속됐다.
- Hermes 소스를 확인한 결과 `API_SERVER_KEY`만 있어도 API 서버를 켜는 조건이었다.
- 그 결과 시작 순서에서 먼저 뜬 프로필이 8642를 차지하고 나머지가 계속 실패했다.

### 수정 내용

2026-08-22 23:51~23:54 KST에 다음처럼 역할을 분리했다.

- `coder`: Telegram token 유지, API 관련 key 제거
- `maccoder`: Telegram token 제거, API enabled/key 유지, `API_SERVER_PORT=8642` 명시
- `architect`, `designer`, `reporter`, `reviewer`: Telegram token과 API key 제거
- `news`: 기존 API `8643` 유지
- `trading`: 기존 API `8644` 유지
- base 포함 9개 config: `security.redact_secrets: true`

변경 전에 다음 백업을 만들었다.

```text
~/.hermes/config.yaml.bak.gateway-conflict-20260822-235144
~/.hermes/profiles/<name>/.env.bak.gateway-conflict-20260822-235144
~/.hermes/profiles/<name>/config.yaml.bak.gateway-conflict-20260822-235144
~/.hermes/profiles/<changed>/gateway_state.json.before-fix-20260822-235426
```

백업 `.env`에는 시크릿이 있으므로 외부 공유·커밋 금지다. 전체 백업을 한꺼번에
복원하면 충돌 설정도 되살아나므로, 롤백 시 필요한 키만 비교·선별한다.

### 기존 오류 로그 처리

gateway를 멈춘 상태에서 기존 `gateway.error.log`를 날짜가 붙은 파일로 이동하고
gzip 압축했다.

```text
gateway.error.log.before-fix-20260822-235225.gz
gateway.error.log.after-first-fix-20260822-235426.gz
```

압축본은 총 `4.2 MiB`, 권한은 `600`이다. 원본 반복 로그를 압축해
`~/.hermes`가 `7,234.4 MiB`에서 `6,538.4 MiB`로 줄어 약 `696 MiB`를 회수했다.

### 검증

- 9개 gateway 모두 launchd supervised 상태
- `default`와 `coder` Telegram connected
- `maccoder/news/trading` API health 모두 HTTP 200
- listener 소유자 `8642=maccoder`, `8643=news`, `8644=trading`
- 중복 API·Telegram 오류 0건
- secret redaction disabled 경고 0건
- 20초 관찰 중 새 오류 로그 증가 0 byte
- 9개 프로필 `hermes config check` 모두 통과

## 7. 설치·운영 변경 이력

### 2026-05-02~03 — 최초 설치

- Nous Research Hermes Agent를 macOS에 원라인 installer로 설치했다.
- OpenClaw의 memory·user profile·SOUL 일부를 마이그레이션했다.
- OpenAI Codex OAuth를 provider로 사용하고 Telegram gateway를 구성했다.
- base launchd 서비스 `ai.hermes.gateway`를 등록했다.

### 2026-05-09 — maccoder 프로필 추가

- default와 분리된 코딩 전용 프로필을 clone으로 생성했다.
- OpenAI OAuth 파일·lock을 symlink로 공유했다.
- Claude Max 인증을 쓰기 위한 HOME 복원 wrapper와 `.bash_profile` PATH를 추가했다.
- 당시에는 별도 Telegram 봇을 설정해 default와 병행했다.

### 2026-05-23 — Telegram reconnect loop

- 네트워크는 정상이지만 장기 gateway 프로세스가 reconnect backoff loop에 갇혔다.
- `hermes gateway restart`로 복구했다.
- 이후 “네트워크 정상 + 프로세스 stuck”을 분리 진단하는 운영 패턴으로 남겼다.

### 2026-06-03·06-21 — Codex OAuth 만료·재인증

- `openai-codex` 자격증명이 `exhausted`가 되어 모델 호출이 중단됐다.
- `hermes auth add openai-codex --type oauth`로 독립 device-flow 재인증했다.
- 단순 reset으로 살리지 못하는 refresh-token 회전 문제를 확인했다.

### 2026-07-04 — WebUI 복구와 Hermes 코어 업데이트

- 재부팅 후 gateway·WebUI 자동 시작 누락을 복구했다.
- launchd에서 SSH agent를 사용할 수 없어 git remote 접근이 실패한 것을 확인하고,
  public remote를 HTTPS로 전환했다.
- 처음에는 안정 tag `v2026.5.7`로 올린 뒤, 사용자 결정으로 `origin/main`
  `047b48df`까지 업데이트해 `v0.18.0`이 됐다.
- 업데이트 전 zip 백업 `pre-update-2026-07-04-180432.zip`을 생성했다.
- `maccoder/news/trading` API 8642/8643/8644와 WebUI 8787을 검증했다.

### 2026-07-19 — 9개 프로필 모델 일괄 변경

- base + 8개 프로필을 `gpt-5.6-sol`로 변경했다.
- 당시 effort를 `xhigh`로 올렸으나 이후 현재 값은 전 프로필 `high`다.
- base config 변경이 프로필로 전파되지 않아 파일별 수정·gateway별 재시작이
  필요하다는 사실을 재확인했다.

### 2026-08-22 — 디스크 조사와 gateway 충돌 해결

- `disk_monitor` 장기 로그에서 `.hermes` 증가를 발견했다.
- 프로필별 API·Telegram 자격증명 복제로 인한 오류 폭증을 확인했다.
- 플랫폼 소유권을 분리하고 secret redaction을 켰다.
- 오류 로그를 압축해 약 696 MiB를 회수했다.
- 같은 조사에서 `dev-blog`의 Cursor Agent 자동 실행 기록도 발견해 별도 7일 GC를
  추가했다(`dev-blog` commit `1557afe`). 이 항목은 Hermes 자체가 아니라 연관된
  디스크 조사 이력이다.

### 2026-08-23 — 문서 통합

- `toy/hermes`의 README, 설치 todo, 7월 업데이트 로그와 기존 llm-wiki Hermes
  문서를 이 페이지로 통합했다.
- 현재 런타임을 다시 실측해 과거의 maccoder Telegram·xhigh 설명을 정정했다.

### 2026-10-03 — 모델·추론 통일, cron 복구·보고 범위 변경

- 전체 9개 프로필 기본 모델을 `gpt-6.1-sol`, provider를 `openai-codex`, reasoning effort를 `medium`으로 맞추고 각 config를 확인했다.
- 뉴스와 Wiki lint는 생성 당시 `gpt-5.5`와 이후 `gpt-5.6-sol` 사이의 모델 변경을 구버전 보호장치가 감지하여 inference 전에 차단하고 있었다. 최신 코드에서는 legacy model/provider snapshot을 모델 고정으로 취급하지 않는다.
- 두 작업의 pin을 해제하고 `news`·`maccoder` Gateway를 재시작했다. Wiki lint는 14:11, 뉴스는 14:21 실행 기록이 `ok`, `last_error` 및 `last_delivery_error`가 null인 것을 확인했다. 두 실행은 당시 local 저장 대상이었다.
- Wiki lint 실행 보고에 따르면 공유 Wiki의 내용 충돌 페이지를 `contested: true`로 표시하고 커밋 `850c19b`를 생성했다. 이는 작업 산출물 보고 기준이다.
- 트레이딩 일일 보고서는 `gpt-5.6-sol` 고정을 해제하고 default 모델을 따르게 했다. 이후 Upbit 관련 지시를 제거하고 `ht_trading`만 보고하도록 프롬프트를 수정했다. prompt 외 설정 변경 없음도 확인했다.
- 뉴스 전달은 local에서 맥비 작업실 뉴스 토픽 609로 변경했다. topic 수신과 deliver 설정은 검증했고, 새 대상의 실제 발송은 다음 19시 실행에서 확인할 예정이다.

## 8. 남은 위험과 후속 점검

아래 기존 위험 1–7은 2026-08-23 조사 기록이며, 2026-10-03에 크기·파일 상태·해결 여부를 재검증한 것은 아니다.

- **당일 후속:** 뉴스 토픽 609 첫 발송 확인, 수정된 HT 전용 21시 보고서 확인, 나머지 하위 프로필 기존 세션의 새 모델 반영 여부 확인.

1. **Hermes 코어 git garbage**: `~/.hermes/hermes-agent/.git`이 약 1.4 GiB이고,
   `git count-objects -vH` 기준 `tmp_pack_*` garbage가 약 593.6 MiB다. gateway
   오류와 별개이며 아직 정리하지 않았다.
2. **업데이트 백업**: `pre-update-2026-07-04-180432.zip`이 약 460.8 MiB로 고정돼
   있다. 복구 필요성이 없어질 때 사용자 확인 후 정리한다.
3. **maccoder toolchain**: profile-local Rust toolchain과 Cargo registry가 약
   1.48 GiB다. 설치 자산이며 지속 증가 원인은 아니었다.
4. **launchd stderr 회전**: Hermes 내부 logger는 회전하지만 plist의
   `StandardErrorPath`인 `gateway.error.log`는 launchd가 계속 append한다. 현재
   오류 원인은 제거했지만 크기 감시는 계속 필요하다.
5. **WebUI API 키 저장**: `com.hermestalk.webui.plist`의 평문 환경변수를 전용
   권한 파일로 분리하고 노출됐던 키를 교체하는 작업이 남아 있다.
6. **OAuth lock 비대칭**: `auth.json`은 전 프로필이 공유하지만 `auth.lock`은
   maccoder 외 프로필에서 별도 파일이다. 동시 refresh 안전성 검토가 필요하다.
7. **코어 업데이트 전 사용자 변경**: `~/.hermes/hermes-agent/package-lock.json`이
   수정 상태다. 업데이트 전에 보존 여부를 결정해야 한다.

## 9. 기존 문서와 정본 우선순위

다음 문서는 설치 당시 맥락과 상세 작업 로그를 보존하는 원본 자료다.

- `/Users/wooki/project/toy/hermes/README.md`: 초기 사용 가이드와 maccoder 구조
- `/Users/wooki/project/toy/hermes/tasks/todo.md`: 설치 단계와 날짜별 Review
- `/Users/wooki/project/toy/hermes/hermes-agent-update-2026-07-04.md`: 코어 업데이트·롤백 로그
- `/Users/wooki/project/toy/hermes/CLAUDE.md`: 문서 저장소 운영 지침
- [[hermes-agent]]: 제품 개념과 API·멀티에이전트 구조
- [[multi-profile-cli-agent-isolation]]: OAuth/HOME/shell/profile 복제 일반 패턴
- [[hermes-dashboard]]: Hermes API를 사용하는 웹 대시보드 프로젝트

과거 사실의 세부 명령이나 당시 버전은 원본 문서를 참고한다. **현재 운영 상태와
역할 배치는 이 문서를 우선**한다.

## 변경 이력

- 2026-10-03: WebUI 업데이트 후 offline의 runtime/import 경로 원인과 복구 기록을 별도 bug Runbook에 추가. 자동 탐색 런처·회귀/HTTP 테스트·plist 수정 및 사용자 reload 후 production 적용 검증 완료. error 5 후 재등록 절차도 기록.

- 2026-10-03: 실제 CLI·config·cron 기록을 바탕으로 버전·모델·추론 설정 갱신. 프로필 역할·활동, cron 장애 복구, HT 전용 보고 및 뉴스 토픽 전달 설정 추가. 실행 성공과 새 전달 대상 미검증 상태를 구분하고 과거 운영 수치를 과거 기록으로 표시.

- 2026-05-09: 최초 생성. default 위에 maccoder 프로필을 추가한 셋업을 기록.
- 2026-05-23: Telegram reconnect loop 복구 기록 추가.
- 2026-06-03: Codex OAuth refresh-token 회전·device 재인증 기록 추가.
- 2026-06-21: OAuth 장애 재발과 재인증 기록 추가.
- 2026-07-05: 재부팅 복구, WebUI launchd, Hermes 업데이트 함정 기록 추가.
- 2026-07-19: base + 8개 프로필 모델·effort 변경 기록 추가.
- 2026-08-23: `toy/hermes`의 기존 셋업 문서와 2026-08-22 gateway 충돌 해결을
  통합하고 현재 실측 상태 기준의 단일 운영 문서로 전면 재정리.
