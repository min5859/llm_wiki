---
title: "OpenClaw — AI 에이전트 자동화 도구"
domain: "personal"
sensitivity: "public"
tags: ["project", "openclaw", "ai-agent", "telegram", "automation", "npm"]
created: "2026-04-23"
updated: "2026-04-23"
sources:
  - "session-logs/20260423-113736-72aa-openclaw-를-업데이트-하려고-합니다.-가이드를-알려주세요.md"
  - "session-logs/20260423-194609-6b61-코딩전용-openclaw-agent-를-추가했는데-텔레그램으로-메세지를-보내면-응답이-없습.md"
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

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260423-113736-72aa에서 추출)
- 2026-04-23: 다중 에이전트 구성, Telegram 그룹 설정, 버그 트러블슈팅 추가 (세션 로그 20260423-194609-6b61)
