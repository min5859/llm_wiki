---
title: "OpenClaw 코더 default 모델: Anthropic 대신 Codex (GPT-5.5)"
domain: "personal"
sensitivity: "public"
tags: ["decision", "openclaw", "claude", "codex", "anthropic", "acp"]
created: "2026-05-07"
updated: "2026-05-07"
sources:
  - "session-logs/20260507-011504-7932-지즘-openclaw-agent-중에-coding-agent-인-맥코더가-텔레그램-채팅-창.md"
confidence: "high"
related:
  - "wiki/projects/openclaw.md"
  - "wiki/bugs/openclaw-coder-silent-3-layer.md"
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
  - "wiki/analyses/openclaw-acp-runtime-internals.md"
---

# OpenClaw 코더 default 모델: Anthropic 대신 Codex (GPT-5.5)

## 결정

- 코더 (`agent.id = coder`) 의 **default 모델**: `anthropic/claude-opus-4-6` → **`openai-codex/gpt-5.5`**
- `runtime.acp.agent`: `claude` 유지 (Anthropic Claude 는 ACP 경유로만 사용)
- 즉 일반 텔레그램 메시지는 embedded GPT-5.5 로 즉시 응답, Claude Opus 는 사용자가 명시적으로 `/acp spawn --bind here` 를 실행한 ACP 세션 내에서만 사용

## 배경

2026-05-07 코더 응답 무 사건 ([[openclaw-coder-silent-3-layer]]) 에서 진짜 원인이 **Anthropic OAuth 의 organization 차원 차단** (`HTTP 403 OAuth authentication is currently not allowed for this organization`) 임을 발견. third-party CLI 에서 Claude OAuth 를 사용하려는 시나리오에 대한 별도 정책이 있는 것으로 추정 ([[anthropic-oauth-third-party-billing-trap]]).

코더의 fallback 이 0개라 OAuth 거부가 곧 silent 침묵으로 이어짐. 단순한 fallback 추가만으로는 코더의 정체성 ("Claude Opus 4.6") 이 깨짐.

> **후속 (2026-06 보고)**: 이 organization-level 차단은 이후 더 강화돼, 구독의 third-party 하네스 라우팅 차단 + **커밋 메시지의 "OpenClaw" 문자열 스캔으로 거부/추가과금**까지 보고됐다. 프로젝트명이 OpenClaw 라 1st-party Claude Code 작업도 영향권 — 자세히 [[anthropic-oauth-third-party-billing-trap]] "정책 강화" 절.

## 결정 이유

- **신뢰성**: main agent 는 codex 라 같은 OAuth 거부 영향 없음. 코더도 같은 모델로 통일하면 인증 단일화로 운영 안정성 확보.
- **정책**: Anthropic 은 third-party CLI 인증 정책상 ACP 경유 사용을 권장하는 시그널. 직접 OAuth 가 아니라 Claude Code 하네스 (= ACP) 가 자기 인증으로 호출하는 경로가 정공법.
- **선택권**: `/acp spawn` 으로 사용자가 명시 호출할 때만 Claude Opus 를 쓰면, 평소엔 Codex 로 빠르게 응답하고 무거운 코딩 task 만 Opus 로 escalate 하는 자연스러운 분리.

## 변경 명령

`openclaw models set --agent <id>` 는 글로벌 only 라 사용 불가. `agent config 직접 편집`:

```bash
# 현재 위치 확인 (인덱스 변동 가능)
python3 -c "
import json
with open('/Users/wooki/.openclaw/openclaw.json') as f: d=json.load(f)
for i,e in enumerate(d['agents']['list']):
    if e.get('id')=='coder':
        print(f'agents.list[{i}].model = {e[\"model\"]}')
"

# 모델 변경
openclaw config set 'agents.list[2].model' '"openai-codex/gpt-5.5"'
openclaw daemon restart

# 검증
openclaw models status --agent coder | grep -E "Default|Fallbacks"
```

`runtime.acp` 블록은 그대로 유지 — `/acp spawn` 호출 시 backend=acpx, agent=claude 로 Claude Code 하네스를 띄우는 경로는 살려둔다.

## 트레이드오프

- **잃는 것**: 일반 메시지에 대한 Claude Opus 의 응답 톤 / 추론 깊이. 코더 system prompt 가 Opus 용으로 작성돼 있어 GPT-5.5 와의 미스매치 발생 가능 (호명-only silent stop 등). prompt 재조정 필요.
- **얻는 것**: OAuth 정책 변동에 silent 침묵하지 않음. 인증 단일화. `/acp spawn` 경로로 Claude 사용도 보존.

## 후속 작업

- 코더 system prompt (`~/.openclaw/agents/coder/agent/...`) 를 GPT-5.5 응답 강도에 맞춰 재조정 (호명만 와도 응답 강제)
- ACP 격리 작업 — wrapper 의 `--setting-sources` 에서 user 제외 또는 별도 HOME 사용 ([[openclaw-acp-runtime-internals]] §4)
- Anthropic 토큰 정리 — 만료된 OAuth 자격증명을 `~/.openclaw/agents/coder/agent/auth-profiles.json` 에서 제거하거나 신규 API key 등록

## 변경 이력

- 2026-06-13: "후속 (2026-06 보고)" 노트 추가 — 차단 정책이 커밋 메시지 "OpenClaw" 문자열 스캔 거부/과금으로 강화됐다는 보고 교차링크 ([[anthropic-oauth-third-party-billing-trap]]). 출처: session-logs/20260613-033132-ea91-*
- 2026-05-07: 최초 작성 (세션 로그 20260507-011504-7932)
