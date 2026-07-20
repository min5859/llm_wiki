---
title: "OpenClaw provider ID 리네임 브레이킹 체인지 — openai-codex → openai (전 모델 Unknown model 실패)"
domain: "ai-agent"
sensitivity: "public"
tags: ["bug", "openclaw", "provider-id", "breaking-change", "telegram", "update", "nvm", "launchd"]
created: "2026-07-19"
updated: "2026-07-19"
sources:
  - "session-logs/20260719-213640-b1d7-현재-openclaw-모델이-뭐지.md"
confidence: "high"
related:
  - "wiki/projects/openclaw.md"
  - "wiki/bugs/openclaw-coder-silent-3-layer.md"
  - "wiki/decisions/openclaw-coder-default-model-codex.md"
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
---

# OpenClaw provider ID 리네임 브레이킹 체인지 — openai-codex → openai

OpenClaw `2026.7.1-2` 업데이트가 provider ID `openai-codex`를 legacy 로 강등하고 `openai`로 리네임했다. 설정·인증 프로파일이 옛 ID를 계속 참조해 **기본 모델·fallback 모델이 전부 "Unknown model" 로 실패**, 텔레그램 봇이 "Something went wrong" 만 반복했다. `openclaw-coder-silent-3-layer` (2026-05-07) 와 증상은 비슷하지만 원인은 완전히 다른 사건.

## 증상

- 텔레그램 전 토픽에서 응답 없음, 봇이 "Something went wrong" 만 반환
- `openclaw gateway`/Node/네트워크는 전부 정상 — 로그에서만 확인 가능:
  ```
  FailoverError: Unknown model: openai-codex/gpt-5.5. "openai-codex" is a legacy provider ID. Run `openclaw doctor --fix` to ...
  ```
- `openclaw models status` 는 auth 를 0개로 표시 (새 sqlite 인증 저장소가 legacy ID 프로파일을 못 읽어서)

## 3계층 원인 (겹쳐서 발현)

| # | 층 | 원인 | 해결 |
|---|---|------|------|
| A | Node 엔진 게이트 | `openclaw update` 요구 버전이 `>=24.15.0`으로 상향, 로컬 nvm node 는 `24.14.0` (0.0.1 부족) → 업데이트 자체가 doctor 게이트에 막혀 실행 불가 | `nvm install 24 --reinstall-packages-from=24.14.0` 으로 `24.18.0` 설치 |
| B | 게이트웨이 LaunchAgent plist 고정 | Node 를 올려도 게이트웨이 서비스 plist 가 옛 `v24.14.0` 경로를 하드코딩 중이라 서비스는 여전히 구 Node 로 기동 | `openclaw daemon install` (또는 `update` 재실행) 로 plist 를 현재 Node 경로로 재생성 |
| C | **provider ID 리네임 (진짜 원인)** | 패키지는 `2026.7.1-2` 로 이미 설치 완료됐으나, `openai-codex/*` provider ID 가 legacy 로 바뀜. 설정(`openclaw.json`)·인증 프로파일(`auth-profiles.json`)엔 옛 ID가 그대로 박혀 있어 기본+fallback 모델이 전부 "Unknown model" | `openclaw doctor --fix` — config·auth 의 `openai-codex/*` → `openai/*` 일괄 마이그레이션 (auth 는 legacy JSON 을 새 sqlite 저장소로 이관) |

세 층 모두 같은 업데이트 시도에서 순서대로 드러났다: A 를 풀어야 B 가 보이고, B 를 풀어야 실제 게이트웨이가 새 버전으로 뜨고, 그제서야 C(진짜 원인)의 에러 메시지가 로그에 나타난다.

## 핵심 교훈

1. **fallback 이 있어도 같은 legacy prefix 면 전멸한다.** `primary: openai-codex/gpt-5.5`, `fallback: openai-codex/gpt-5.4` 둘 다 같은 접두어라 provider ID 리네임 한 방에 fallback 체인 전체가 죽는다. `openclaw-coder-silent-3-layer`(2026-05-07)의 "fallback 이 같은 provider 뿐이면 무의미" 교훈과 동일 축.
2. **에러 메시지가 해법을 직접 지정하는 패턴** — `FailoverError` 문구 자체에 `Run "openclaw doctor --fix"` 가 포함돼 있었다. 벤더가 브레이킹 체인지에 자동 마이그레이션 경로를 마련해둔 경우, 로그 원문을 끝까지 읽는 것만으로 해법이 나온다.
3. **nvm 관리 Node + LaunchAgent 게이트웨이 조합은 매 업그레이드마다 깨질 수 있는 구조** — Node 버전을 올릴 때마다 게이트웨이 plist 재생성이 필요하다는 걸 놓치기 쉽다. 근본 해법은 시스템 Node(24.15+ 또는 22 LTS)로 게이트웨이를 고정하고 nvm 의존을 끊는 것 (이월 과제로 기록됨).
4. `openclaw doctor --fix` 실행 전 `openclaw.json.bak-pre-doctorfix` / `auth-profiles.json.bak-pre-doctorfix` 백업이 자동 생성된다.

## 복구 후 상태 (2026-07-19)

- `agents.defaults` 기본 모델: `openai/gpt-5.6-sol` (fallback: `openai/gpt-5.5`, 기존 `gpt-5.4` 제거)
- `agents.defaults.thinkingDefault: xhigh`
- Node: `v24.18.0` (nvm), 게이트웨이 plist 재생성 완료

## 관련 맥락

- 증상(텔레그램 무응답)은 [[openclaw-coder-silent-3-layer]] (2026-05-07, plugins.allow/좀비 task/Anthropic 403) 와 유사하지만 원인은 무관 — OpenClaw 에서 "텔레그램 무응답"은 매번 다른 근본원인으로 재발하는 증상 패턴임에 유의.
- provider 정책 이력은 [[openclaw-coder-default-model-codex]] (Anthropic → Codex 전환) 참고.

## 변경 이력

- 2026-07-19: 최초 생성 (출처: session-logs/20260719-213640-b1d7-*)
