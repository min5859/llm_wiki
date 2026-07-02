---
title: "OpenClaw 코더 응답 무 — 3계층 디버깅 (plugins.allow / 좀비 task / OAuth 403)"
domain: "ai-agent"
sensitivity: "public"
tags: ["bug", "openclaw", "acp", "acpx", "anthropic", "oauth", "debugging"]
created: "2026-05-07"
updated: "2026-05-07"
sources:
  - "session-logs/20260507-011504-7932-지즘-openclaw-agent-중에-coding-agent-인-맥코더가-텔레그램-채팅-창.md"
confidence: "high"
related:
  - "wiki/projects/openclaw.md"
  - "wiki/analyses/openclaw-acp-runtime-internals.md"
  - "wiki/decisions/openclaw-coder-default-model-codex.md"
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
---

# OpenClaw 코더 응답 무 — 3계층 디버깅

OpenClaw 의 코딩 에이전트 "맥코더" 가 텔레그램 코더 토픽에서 응답이 끊긴 사건. 같이 발현된 **3개의 독립 원인**을 한 층씩 벗겨 해결한 사례. 한 가지가 진짜 원인이고 나머지는 동시에 발현된 별개 문제로, 디버깅 중 가설이 두 번 교체됐다.

## 증상

- 텔레그램 코더 토픽에서 어떤 메시지를 보내도 응답이 안 옴
- 메인 토픽 / 영어 봇 등 다른 토픽은 정상
- `tasks list --runtime acp` 에 12일 17시간 묵은 stuck task 1건 발견 → 처음엔 이게 원인으로 보임

## 진짜 원인은 3개가 겹쳐 있었다

| # | 층 | 원인 | 결과 |
|---|---|------|------|
| 1 | 인프라 | OpenClaw 2026.5.3+ 보안 정책으로 `plugins.allow` 미설정 시 acpx runtime register 차단 | `plugins inspect acpx` 는 `loaded` 인데 실제 호출 시 `ACP runtime backend is not configured` |
| 2 | 데이터 | 12일 묵은 좀비 ACP task 가 `running` 상태 + cancel 도 stuck (chicken-and-egg) | 코더 토픽 세션 점유 → 새 메시지 진입 차단 가능성 |
| 3 | **인증** | **Anthropic OAuth 가 organization 차원에서 차단** (`HTTP 403 OAuth authentication is currently not allowed for this organization`) | 코더 default 모델이 `anthropic/claude-opus-4-6` + fallback 0개 → 403 시 빠질 데가 없어 silent 침묵 |

3번이 진짜 원인이었고, 1·2번은 해결해도 응답이 돌아오지 않았다. 1·2번은 별개로 해결할 가치가 있는 부수 이슈.

## 진단 흐름 (가설 교체 2번)

### 가설 A: 좀비 task 가 세션을 점유해서 막고 있다

`tasks audit` 결과 `b81a67c5-...` task 가 12일 17시간 stuck, owner 가 코더 토픽. 첫 가설은 자연스러움. 하지만:

- `tasks cancel` → "ACP runtime backend is not configured" 거부
- `tasks flow cancel` → "child tasks are still active" 거부
- `tasks maintenance --apply` → stale_running prune 미지원으로 0건 처리

→ 이상함. 다른 layer 가 있다.

### 가설 B: acpx plugin 이 사실 register 안 됨

로그 정밀 grep — `[plugins] plugins.allow is empty; discovered non-bundled plugins may auto-load: acpx ...` 워닝 발견. `plugins.allow` 설정 + restart 후 `embedded acpx runtime backend registered lazily` 가 정상적으로 떴다. → 좀비 task 도 sqlite 직접 편집으로 정리 (자세한 절차는 [[openclaw-acp-runtime-internals]] 참고).

ACP runtime + task 모두 정상이 됐다. 그런데도 **여전히 응답이 안 옴**.

### 가설 C: 모델 호출 자체가 실패하고 있다

gateway 로그에서 lane error 추출:

```
lane task error: lane=session:agent:coder:telegram:group:-1003977252069:topic:2
error="FailoverError: HTTP 403 permission_error: OAuth authentication is currently not allowed for this organization."
```

코더의 default 모델은 `anthropic/claude-opus-4-6`. `models status --agent coder` 에서 **fallback 0개** 확인. main agent 는 같은 에러를 띄워도 모델이 `openai-codex/gpt-5.5` 라 무관 → main 은 응답, 코더만 침묵. 진짜 원인 확정.

Anthropic 토큰 형식이 `sk-ant-o...` 로 시작 (OAuth access token 형식, 일반 API key 는 `sk-ant-api03-...`). third-party CLI 의 Anthropic OAuth 가 organization 정책으로 차단된 케이스 — [[anthropic-oauth-third-party-billing-trap]] 참고.

## 해결

코더 default 모델을 `openai-codex/gpt-5.5` 로 변경. `runtime.acp.agent: claude` 는 유지 (정책: Anthropic 은 ACP 경유 사용자가 명시 spawn 시에만, 일반 메시지는 codex 로) — [[openclaw-coder-default-model-codex]].

```bash
# config CLI 의 models set 은 글로벌 only. agent config 직접 편집:
openclaw config set 'agents.list[2].model' '"openai-codex/gpt-5.5"'
openclaw daemon restart
```

## 후속 이슈 (Part 2): ACP wrapper 카카오톡 미스라우팅

위 해결 후 사용자가 `/acp spawn --bind here` 를 한 번 실행하니 다시 응답 무. 하지만 이번엔 신기한 현상 — **응답이 카카오톡 "나와의 채팅" 으로 갔다**.

원인:

- ACP wrapper 가 claude binary 를 `--setting-sources=user,project,local --allow-dangerously-skip-permissions` 로 spawn
- claude 가 user-level Claude.ai connector 전부 (Gmail / Calendar / Drive / KakaotalkChat MemoChat) 를 상속
- 모델이 응답 도구로 텔레그램 stdout (`message.send`) 대신 `mcp__claude_ai_PlayMCP__KakaotalkChat-MemoChat` 선택 → 카카오톡으로 송신

→ 자세한 메커니즘과 회복 절차 (wrapper kill + sessions.json stale binding 정리) 는 [[openclaw-acp-runtime-internals]] §3·§4

## 호명-only silent stop (모델-prompt 미스매치)

위 회복 후에도 사용자가 "맥코더" 호명만 보내면 모델이 빈 텍스트로 즉시 stop:

```
user: "맥코더"  →  assistant: text=""  (output: 4 tokens, stopReason: stop)
```

cacheRead 정상 (15360) — 컨텍스트는 전달되는데 GPT-5.5 가 "별다른 응답 필요 없음" 으로 판정. 코더 system prompt 가 Claude Opus 용으로 작성되어 호명만으로는 응답 강제력이 약함.

→ 회피: 호명만 보내지 말고 실제 명령 / 질문을 함께 보냄. 근본 해결은 system prompt 를 모델 변경에 맞춰 재조정.

## 교훈

- **3개 층이 겹쳐 있을 수 있다** — "좀비 task 가 원인" 처럼 그럴듯한 1차 가설을 잡았어도 해결 후에 응답이 안 오면 즉시 가설 폐기
- `plugins inspect` / `plugins doctor` 만으로 acpx register 검증 불충분 — **로그의 `embedded acpx runtime backend registered lazily` 로 확정**
- **Fallback 0개 + organization 정책 변동** = silent 침묵 함정. fallback 을 최소 1개라도 두면 같은 함정 재발 시 자동 우회됨
- ACP 디버깅은 **lane error log** 가 결정적 — 모델 호출 실패는 trajectory 가 아니라 gateway log 에 찍힘
- ACP wrapper 의 user 환경 상속은 보안적으로 위험 — [[mcp-config-secret-exposure-via-ps]] / [[openclaw-acp-runtime-internals]] §4 참고

## 관련 changelog

- `~/project/toy/openclaw/changelog/2026-05-07_코더-응답무-3계층-진단-plugins.allow-좀비task-OAuth403.md` (Part 1 + Part 2)

## 변경 이력

- 2026-05-07: 최초 작성 (세션 로그 20260507-011504-7932)
