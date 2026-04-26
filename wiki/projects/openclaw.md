---
title: "OpenClaw — AI 에이전트 자동화 도구"
domain: "personal"
sensitivity: "public"
tags: ["project", "openclaw", "ai-agent", "telegram", "automation", "npm"]
created: "2026-04-23"
updated: "2026-04-26"
sources:
  - "session-logs/20260423-113736-72aa-openclaw-를-업데이트-하려고-합니다.-가이드를-알려주세요.md"
  - "session-logs/20260423-194609-6b61-코딩전용-openclaw-agent-를-추가했는데-텔레그램으로-메세지를-보내면-응답이-없습.md"
  - "session-logs/20260426-120703-304f-현재-프로젝트는-openclaw-라는-Agent-를-사용해서-자산관리-웹앱을-구현해보고-있.md"
  - "session-logs/20260426-121630-14c3-https---bongman.tistory.com-1341-위-웹페이지-내용을-요약해-주세.md"
confidence: "high"
related:
  - "wiki/projects/gieok.md"
  - "wiki/analyses/openclaw-telegram-group-setup.md"
---

# OpenClaw — AI 에이전트 자동화 도구

Telegram 등 채널과 연동하는 AI 에이전트 자동화 도구. npm으로 전역 설치. 현재 사용 중인 AI 프로바이더: `openai-codex/gpt-5.4`.

## 설치 및 업데이트

```bash
# 업데이트
npm install -g openclaw@latest

# 버전 확인
openclaw --version

# 서비스 재시작 (핵심 명령)
openclaw daemon restart
```

> **주의**: `openclaw start`가 아닌 `openclaw daemon restart`가 LaunchAgent를 올바르게 재시작하는 명령이다.

## 설정 파일 구조 (`~/.openclaw/`)

| 파일/디렉터리 | 역할 | git 관리 |
|-------------|------|----------|
| `openclaw.json` | 메인 설정 (auth, 모델, 채널 등) | ✅ 로컬 private repo |
| `agents/*/models.json` | 에이전트 모델 설정 | ✅ |
| `cron/jobs.json` | 크론 작업 설정 | ✅ |
| `devices/paired.json` | 페어링된 기기 | ✅ |
| `identity/device.json` | 기기 정보 | ✅ |
| `workspace/` | 메인 에이전트 페르소나 (SOUL.md 등) | 별도 git (OpenClaw 자체 관리) |
| `workspace-english/` | 영어 튜터 에이전트 | 별도 git (OpenClaw 자체 관리) |
| `memory/*.sqlite` | 벡터 메모리 DB | ❌ (바이너리, 자주 변경) |
| `logs/`, `cron/runs/`, `media/` | 런타임 아티팩트 | ❌ |

### git 관리 설정

`~/.openclaw`를 로컬 private git으로 관리 중:

```bash
# 설정 변경 후 커밋
cd ~/.openclaw && git add -A && git commit -m "설명"
```

## 이미지 생성 기능

```bash
openclaw capability image generate --model <provider/model> --prompt "..." --output ~/out.png
```

### 지원 프로바이더

| 프로바이더 | 설정 필요 | 비고 |
|-----------|----------|------|
| openai | OpenAI API Key | gpt-image-2 기본; Codex OAuth와 별도 키 |
| google | Google API Key | Gemini image |
| fal | fal API Key | Flux 기반 |
| minimax | MiniMax API Key | |
| comfy | ComfyUI 로컬 설정 | |

> **중요**: 텍스트(gpt-5.4)는 OpenAI Codex OAuth로 인증하지만, 이미지 생성(`gpt-image-1/2`)은 **별도의 `OPENAI_API_KEY`** 가 필요하다. 두 인증 체계가 분리되어 있다.

## 다중 에이전트 구성

현재 운용 중인 에이전트 3개:

| 에이전트 ID | 이름 | 모델 | 라우팅 |
|------------|------|------|--------|
| `main` | 맥비 🐝 | openai-codex/gpt-5.4 | Telegram default (DM + 그룹 일반 채팅) |
| `english` | English Tutor 📚 | openai-codex/gpt-5.4-mini | Telegram english (별도 봇) |
| `coder` | 코더 💻 | anthropic/claude-opus-4-6 | Telegram default, 그룹 "코딩" 토픽 |

### 라우팅 규칙 (`bindings`)

`openclaw.json`의 `bindings` 배열에서 에이전트별 라우팅을 설정한다:

```json
// 에이전트 바인딩 (올바른 형식)
{
  "agentId": "coder",
  "match": {
    "channel": "telegram",
    "accountId": "default",
    "peer": "group:-1003977252069:topic:2"
  }
}
```

> **중요**: `"type": "acp"` 필드는 `bindings` 항목이 아닌 **agent 정의의 `runtime`** 에 두어야 한다. `bindings`에 `"type": "acp"`가 있으면 OpenClaw가 알 수 없는 타입으로 간주해 `Routing rules: 0`이 되어 해당 에이전트로 메시지가 전달되지 않는다.

### 포럼 토픽 vs 별도 봇

같은 그룹 내에서 에이전트를 분리하는 방법 두 가지:

| 방법 | 설정 난이도 | 주의사항 |
|------|-----------|----------|
| **포럼 토픽** | 복잡 (Privacy Mode 해제, 봇 재초대, topic ID 일치) | `coder` 방식 |
| **별도 봇** | 간단 (BotFather에서 봇 생성 후 토큰 등록) | `english` 방식 |

실용적으로는 **별도 봇** 방식이 훨씬 단순하다.

## Telegram 그룹 설정

그룹에서 mention 없이 응답하려면 두 가지를 모두 설정해야 한다:

### 1. OpenClaw 설정: `requireMention: false`

그룹 레벨에 설정 필요 (`channels.telegram.groups.<GROUP_ID>`):

```json
"groups": {
  "-1003977252069": {
    "requireMention": false,
    "topics": {
      "2": { "requireMention": false }
    }
  }
}
```

> **주의**: topic 레벨에만 `requireMention: false`를 설정하면 일반 채팅(non-topic)에는 적용되지 않는다.

### 2. BotFather: Privacy Mode 비활성화

OpenClaw 설정만으로는 부족하다. **Telegram Bot API 레벨에서** Privacy Mode가 켜져 있으면 그룹 메시지가 봇에게 아예 전달되지 않는다. `/start @botname`처럼 `/`로 시작하는 명령만 예외적으로 전달된다.

```
BotFather → /setprivacy → 봇 선택 → Disable
```

> **기존 그룹 적용**: Privacy Mode 변경 전에 봇이 이미 들어가 있는 그룹은 변경이 즉시 적용되지 않는다. 봇을 **그룹에서 추방 후 재초대**해야 한다.

설정 완료 후 확인:
```bash
openclaw channels status --probe
```

경고 없이 `groups:unmentioned, works`가 표시되면 정상.

## 상태 확인

```bash
openclaw status           # 전체 상태 조회
openclaw gateway status   # LaunchAgent + 게이트웨이 상세
openclaw channels status --probe  # 채널 상태 + 그룹 접근 테스트
openclaw agents list --bindings   # 에이전트 라우팅 규칙 확인
```

## 버전 이력

| 날짜 | 버전 | 비고 |
|------|------|------|
| 2026-04-23 | 2026.4.21 | npm install -g openclaw@latest |
| 이전 | 2026.3.28 | — |

## LaunchAgent

Gateway 서비스는 macOS LaunchAgent로 실행된다:
- 로그인 시 자동 시작
- 포트: 18789 (localhost loopback)

## 알려진 버그 / 트러블슈팅

### bindings에 `"type": "acp"` 포함 → Routing rules 0

**증상**: `openclaw agents list --bindings`에서 특정 에이전트의 `Routing rules: 0`. 해당 에이전트로 메시지가 라우팅되지 않음.

**원인**: `bindings` 배열의 항목에 `"type": "acp"` 필드가 들어간 경우. `runtime.type: "acp"`는 에이전트 정의에 넣어야 하며, `bindings`에 두면 OpenClaw가 해당 바인딩을 무시한다.

**수정**: `bindings` 항목에서 `"type": "acp"` 제거. `openclaw config validate`로 검증 후 `openclaw gateway restart`.

---

### 그룹 채팅 응답 없음

체크리스트:
1. `openclaw agents list --bindings` → 해당 에이전트의 `Routing rules: 1` 이상인지 확인
2. `openclaw.json`에 그룹 레벨 `requireMention: false` 설정 여부 확인
3. BotFather에서 Privacy Mode Disable 여부 확인
4. 봇이 그룹에 입장한 시점이 Privacy Mode 변경 이전이면 추방 후 재초대 필요
5. `openclaw channels status --probe`로 audit 경고 확인

## ACP coder 에이전트 — 자율 실행 권한 설정

### permissionMode / nonInteractivePermissions 스키마 제약

acpx 플러그인 스키마에서 확인된 허용값:

| 필드 | 허용값 |
|------|--------|
| `permissionMode` | `"approve-all"`, `"approve-reads"`, `"deny-all"` |
| `nonInteractivePermissions` | `"deny"`, `"fail"` |

**중요**: `permissionMode: "auto"`, `nonInteractivePermissions: "allow"` 옵션은 acpx 플러그인 스키마에 존재하지 않는다. 이 설정으로는 완전한 자율 실행이 불가능하다.

설정 변경 명령어:
```bash
# CLI로 변경 (스키마 내 값만 가능)
openclaw config set plugins.entries.acpx.config.permissionMode approve-reads
openclaw config get plugins.entries.acpx.config.permissionMode

# 현재 실제 운용 값 (기본)
# permissionMode: "approve-all"
# nonInteractivePermissions: "deny"
```

> **주의**: openclaw 게이트웨이 실행 중에 `~/.openclaw/openclaw.json`을 직접 편집하면 프로세스가 파일을 감시하다가 덮어쓸 수 있다. 편집 시 `openclaw gateway stop` → 편집 → `openclaw gateway start` 순서로 진행할 것.

### Claude Code settings.json을 통한 권한 우회

acpx 플러그인 외에, coder 에이전트 디렉터리의 `.claude/settings.json`을 통해 Claude Code 레벨에서 직접 권한을 부여하는 방법도 존재한다 (별도 검토 필요).

## asset-dashboard 프로젝트 git 분리

openclaw workspace-coder(`~/.openclaw/workspace-coder`) 아래에 `asset-dashboard/` 서브디렉터리가 존재했으나, 독립 git repo로 분리되었다.

```
~/.openclaw/workspace-coder/
├── .gitignore          ← asset-dashboard/ 와 .openclaw/workspace-state.json 제외
├── IDENTITY.md
├── SOUL.md
├── USER.md
└── asset-dashboard/    ← 독립 git repo (자체 .gitignore, 초기 커밋 완료)
```

### workspace-state.json untrack

`.openclaw/workspace-state.json`은 런타임 상태 파일로 `git rm --cached`로 트래킹에서 제거하고 `.gitignore`에 추가되었다.

### asset-dashboard 현재 진행 상태 (2026-04-26 기준)

| Phase | 내용 | 상태 |
|-------|------|------|
| 1 | Next.js + TypeScript + Tailwind + shadcn/ui 설정, Prisma 스키마, 포트폴리오 계산 로직 | ✅ 완료 |
| 2 | Account/Holding CRUD 페이지 + Server Actions | ❌ 미시작 |
| 3 | Yahoo Finance 연동 | ❌ 미시작 |
| 4 | 세금 관리 | ❌ 미시작 |
| 5 | Gemini AI 분석 | ❌ 미시작 |
| 6 | 차트 시각화 + 배포 | ❌ 미시작 |

Prisma 스키마 핵심: `Account`, `Holding` 모델, `AccountType` / `AssetClass` / `Currency` enum, Supabase PostgreSQL 연결.

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260423-113736-72aa에서 추출)
- 2026-04-23: 다중 에이전트 구성, Telegram 그룹 설정, 버그 트러블슈팅 추가 (세션 로그 20260423-194609-6b61)
- 2026-04-26: ACP permissionMode 스키마 제약, asset-dashboard git 분리 구조 추가 (세션 로그 20260426-120703-304f, 20260426-121630-14c3)
