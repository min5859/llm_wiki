---
title: "회전형 OAuth refresh token 을 여러 클라이언트가 공유할 때의 쟁탈 함정"
domain: both
sensitivity: public
tags: ["oauth", "refresh-token", "rotation", "codex", "openclaw", "hermes", "auth", "diagnosis", "ai-agent"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "session-logs/20260603-140159-fbbf-지금-openclaw가-응답이-없는-것-같습니다.-왜-응답이-없는지-확인해-주세요.md"
  - "session-logs/20260603-143737-7275-hermes-에-연결된-AI-provider-인-codex-cli-인증이-만료된-것-같은데.md"
related:
  - "wiki/projects/openclaw.md"
  - "wiki/projects/hermes.md"
  - "wiki/analyses/multi-profile-cli-agent-isolation.md"
  - "wiki/bugs/openclaw-coder-silent-3-layer.md"
  - "wiki/analyses/anthropic-oauth-third-party-billing-trap.md"
---

# 회전형 OAuth refresh token 을 여러 클라이언트가 공유할 때의 쟁탈 함정

ChatGPT/Codex OAuth 처럼 **refresh token 이 회전형(rotating, 일회용)** 인 인증을, 같은 계정으로 **여러 클라이언트(OpenClaw·Hermes·codex CLI·VS Code 등)가 각자 복사해서** 들고 있으면, 한쪽이 토큰을 갱신할 때마다 다른 쪽의 토큰 체인이 무효화되는 **핑퐁(쟁탈)** 이 발생한다. 단순 재인증으로는 해결되지 않고, 한쪽을 고치면 다른 쪽이 깨지는 도돌이표가 된다. 2026-06-03 OpenClaw·Hermes 가 둘 다 `min5859@gmail.com` Codex OAuth 를 공유하면서 실시간으로 재현된 사례에서 일반화.

## 핵심 메커니즘 — refresh token rotation

OAuth refresh token 갱신에는 두 방식이 있다.

| 방식 | 동작 | 다중 클라이언트 |
|---|---|---|
| **비회전 (static)** | 갱신해도 refresh token 그대로 | 여러 곳에서 복사해 써도 충돌 없음 |
| **회전 (rotating)** | 갱신 시 **새 refresh token 발급 + 기존 것 즉시 무효화** | 한쪽이 갱신하면 다른 쪽 토큰이 죽음 |

ChatGPT/Codex OAuth 는 **회전형**이다. 클라이언트 A 와 B 가 같은 refresh token 사본을 들고 있을 때:

```
A 가 access token 만료 → refresh 요청 → 새 refresh_token_2 수령, refresh_token_1 무효화
B 는 여전히 refresh_token_1 보유 → 다음 refresh 시도 → 401 "token has been invalidated"
→ B 의 자격증명 풀이 비어 응답 불가
(B 가 먼저 재인증하면 이번엔 A 가 같은 이유로 죽음 = 핑퐁)
```

## 진단의 함정 — `status` 의 "ok expires" 를 믿지 마라

가장 헷갈리는 지점: 클라이언트의 상태 명령은 토큰을 **정상**으로 표시한다.

```
$ openclaw models status
- openai-codex:default  ok expires in 41h    ← 로컬 만료시각일 뿐
```

이 `expires` 는 **로컬에 저장된 access token 의 만료시각**일 뿐, **서버측에서 refresh token 이 이미 무효화됐는지는 모른다.** 서버측 무효화는 오직 **런타임 raw log 의 401** 로만 드러난다.

```
errorPreview="Your authentication token has been invalidated. Please try signing in again."  (401)
"Codex refresh token was already consumed by another client (e.g. Codex CLI or VS Code extension)."
```

→ **진단 키는 status 출력이 아니라 raw log 의 `token has been invalidated` / `consumed by another client` / `lane task error`.** ([[openclaw-coder-silent-3-layer]] 의 교훈 연장 — status 가 아니라 lane error log 가 결정적)

### fallback 이 같은 provider 뿐이면 무의미

OpenClaw 의 fallback 체인이 `gpt-5.5 → gpt-5.3-codex → gpt-5.4` 였는데 **셋 다 openai-codex provider**. provider 하나가 죽으면 fallback 전체가 같이 죽어 **모든 lane(main·coder·temp) 동시 응답 무**가 된다. fallback 은 **다른 provider** 를 최소 하나 둬야 의미가 있다.

## 토큰 공유 방식 4가지 비교

같은 OAuth 계정을 여러 클라이언트가 쓰는 방법과 회전 충돌 내성:

| 방식 | 설명 | 회전 충돌 | 비고 |
|---|---|---|---|
| **① 복사 (import)** | 설치 시 `~/.codex/auth.json` 를 자기 store 로 복사 후 독립 운용 | ❌ **위험** — 각자 refresh 돌다 회전으로 한쪽 무효화 | 이번 Hermes 가 빠진 함정 |
| **② 한 파일 직접 참조 (pointer)** | 토큰 사본 없이 `~/.codex/auth.json` 를 그대로 읽음 | ✅ 충돌 없음 (항상 같은 파일) | OpenClaw ↔ codex CLI 구조 |
| **③ symlink 공유** | `auth.json` + `auth.lock` 을 symlink. lock 도 링크돼 동시 refresh 직렬화 | ✅ 충돌 없음 | Hermes default ↔ maccoder ([[multi-profile-cli-agent-isolation]]) |
| **④ 독립 device-flow 등록** | 클라이언트마다 자체 OAuth device flow 로 **독립 토큰 체인** 발급 | ✅ 근본 해결 | provider 가 멀티 세션 허용 시 |

②③ 은 "한 토큰을 공유" 하는 접근이고, ④ 는 "각자 독립 토큰" 접근이다. 클라이언트가 토큰 store 포맷이 달라 파일 공유가 안 되면(OpenClaw 의 profiles store ↔ Hermes 의 credential pool) **④ 가 유일한 깔끔한 해법**이다.

## 해결 (2026-06-03 사례)

각 클라이언트를 **독립 device-flow OAuth 로 따로 인증** → 각자 독립 refresh token 체인 보유 → 쟁탈 소멸.

```bash
# OpenClaw
openclaw models auth login --provider openai-codex

# Hermes (구 `hermes login` 은 제거됨 → auth add)
hermes auth add openai-codex --type oauth
hermes auth remove openai-codex 1   # 회전돼 죽은 옛 자격증명(exhausted) 제거
```

> 죽은 자격증명은 refresh token 이 회전돼 무효이므로 `reset`(exhaustion 해제)으로는 안 살아난다. 새로 `auth add` 하는 게 정답.

## 교훈

- **회전형 OAuth + 토큰 사본 복사 = 시한폭탄.** 같은 계정을 여러 곳에서 쓰면 복사(①) 말고 공유(②③) 또는 독립 등록(④).
- **`status` 의 "ok expires" 는 로컬 만료시각.** 서버측 무효화는 raw log 의 401 로만 보인다 — 진단은 항상 런타임 로그부터.
- **fallback 은 다른 provider 로.** 같은 provider 모델만 늘어놓으면 provider 장애 시 전멸.
- **"한쪽 고치니 다른 쪽이 깨진다" 는 공유 자원 쟁탈의 신호.** 재인증을 반복하지 말고 공유 구조 자체를 끊어야 한다.
- 사용자의 실관측("openclaw 는 응답 오는데 hermes 는 안 와")이 로그 스냅샷보다 정확할 수 있다 — 모순 시 즉시 가설 정정.

## 변경 이력

- 2026-06-03: 최초 작성. OpenClaw·Hermes Codex OAuth refresh token 쟁탈 진단 2세션 기반 (출처: session-logs/20260603-140159-fbbf, 20260603-143737-7275)
