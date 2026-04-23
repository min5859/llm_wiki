---
title: "OpenClaw Telegram 그룹 봇 설정 — Privacy Mode, requireMention, 다중 에이전트"
domain: "personal"
sensitivity: "public"
tags: ["openclaw", "telegram", "bot", "group", "privacy-mode", "routing", "multi-agent"]
created: "2026-04-23"
updated: "2026-04-23"
sources:
  - "session-logs/20260423-194609-6b61-코딩전용-openclaw-agent-를-추가했는데-텔레그램으로-메세지를-보내면-응답이-없습.md"
confidence: "high"
related:
  - "wiki/projects/openclaw.md"
---

# OpenClaw Telegram 그룹 봇 설정

Telegram 그룹에서 OpenClaw 봇이 mention 없이 응답하려면, **OpenClaw 설정**과 **Telegram Bot API Privacy Mode** 두 레이어를 모두 설정해야 한다. 어느 한쪽만 설정하면 작동하지 않는다.

## 필수 설정 두 가지

### 1. Telegram Bot API — Privacy Mode 비활성화 (BotFather)

Telegram Bot API에는 그룹 메시지를 봇에게 전달할지 여부를 결정하는 Privacy Mode가 있다. 기본값은 **활성화(Enabled)** 로, `/` 명령어와 봇 mention만 전달된다.

Privacy Mode가 켜진 상태에서는 OpenClaw의 `requireMention: false` 설정이 있어도 그룹 일반 메시지가 아예 봇에게 전달되지 않는다.

```
@BotFather → /setprivacy → 봇 선택 → Disable
```

> **기존 그룹 주의**: Privacy Mode 변경 전에 봇이 이미 그룹에 들어와 있으면 변경이 즉시 적용되지 않는다. 봇을 그룹에서 **추방한 뒤 다시 초대**해야 새 설정이 적용된다.

### 2. OpenClaw 설정 — `requireMention: false`

`openclaw.json`의 `channels.telegram.groups` 섹션에서 그룹 레벨로 설정해야 한다.

```json
"channels": {
  "telegram": {
    "groups": {
      "-100xxxxxxxxxx": {
        "requireMention": false,
        "topics": {
          "2": { "requireMention": false }
        }
      }
    }
  }
}
```

> **주의**: `topics.N.requireMention: false`만 설정하면 포럼 토픽 외부의 일반 채팅에는 적용되지 않는다. 그룹 레벨(상위 객체)에도 별도로 설정해야 한다.

설정 변경 후 반드시 게이트웨이 재시작:

```bash
openclaw gateway restart
```

## 설정 완료 확인

```bash
openclaw channels status --probe
```

정상 상태:
```
- Telegram default: enabled, configured, running, connected,
  mode:polling, bot:@yourbot, groups:unmentioned, works
```

`audit failed`나 `privacy mode will block` 경고가 없어야 한다.

## 다중 에이전트: 포럼 토픽 vs 별도 봇

하나의 Telegram 그룹에서 에이전트를 역할별로 분리하는 방법 두 가지:

### 방법 A: 포럼 토픽으로 분리 (같은 봇)

```
그룹 일반 채팅  →  main 에이전트 (맥비)
그룹 "코딩" 토픽  →  coder 에이전트
```

- 같은 봇(@yourbot)을 사용하고 Telegram 그룹의 **Forum Topics** 기능으로 토픽별 라우팅
- 설정: `bindings`에 `"peer": "group:-1003977252069:topic:2"` 형식
- 단점: Privacy Mode 해제, 봇 재초대, topic ID 매핑이 모두 필요

### 방법 B: 별도 봇으로 분리 (권장)

```
그룹 일반 채팅  →  main 에이전트 (@yourbot)
별도 그룹/DM    →  coder 에이전트 (@yourcoder_bot)
```

- BotFather에서 신규 봇 생성 후 `openclaw.json`에 토큰 추가
- 단순하고 topic ID 불일치 문제 없음

### 포럼 토픽 설정 시 주의사항

- Telegram 그룹에서 **"Topics" 기능**을 활성화해야 한다 (그룹 설정 → Topics 활성화)
- `General` 주제가 topic ID = 1, 첫 번째 추가 주제가 topic ID = 2
- `openclaw.json`의 `bindings`에 설정한 topic ID와 실제 Telegram topic ID가 일치해야 한다

## OpenClaw 바인딩 설정 주의사항

`bindings` 배열 항목에 `"type": "acp"` 필드를 추가하면 안 된다:

```json
// ❌ 잘못된 형식 — Routing rules: 0이 됨
{
  "type": "acp",
  "agentId": "coder",
  "match": { ... }
}

// ✅ 올바른 형식
{
  "agentId": "coder",
  "match": {
    "channel": "telegram",
    "accountId": "default",
    "peer": "group:-1003977252069:topic:2"
  }
}
```

`runtime.type: "acp"`는 에이전트 정의 안에 두는 설정이며, `bindings`와 별개다.

## 트러블슈팅 체크리스트

그룹에서 봇이 응답하지 않을 때:

1. `openclaw agents list --bindings` → 해당 에이전트 `Routing rules: 1` 이상 확인
2. `bindings`에 `"type": "acp"` 항목 없는지 확인
3. `requireMention: false`가 그룹 레벨에 설정됐는지 확인
4. BotFather에서 Privacy Mode Disable 여부 확인
5. Privacy Mode 변경 후 봇 추방 → 재초대 실시 여부 확인
6. `openclaw channels status --probe`로 경고 메시지 확인

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260423-194609-6b61에서 추출)
