---
title: "Gemini 2.0-flash 가 free tier 에서 차단되어 429 + limit:0 (2026-05)"
domain: both
sensitivity: public
tags: ["gemini", "google-ai", "free-tier", "429", "quota", "model-deprecation", "env-override"]
created: 2026-05-03
updated: 2026-05-03
sources:
  - "session-logs/20260503-100914-b80f-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
---

# Gemini 2.0-flash 가 free tier 에서 차단되어 429 + limit:0

2026년 5월 시점 `gemini-2.0-flash` 모델이 free tier 에서 사실상 차단된 상태. `generateContent` 호출 시 429 quota error + 응답 메시지에 `"limit: 0"` 동반. Google 이 2.5 출시 후 2.0 free tier 를 제한한 것으로 보이며, **모델 ID 한 줄 교체로 해결**.

## 증상

```
[GoogleGenerativeAI Error]: Error fetching from https://...
"@type":"type.googleapis.com/google.rpc.Help"
"@type":"type.googleapis.com/google.rpc.RetryInfo"
"retryDelay":"54s"
... limit: 0 ...
```

핵심 단서는 메시지 안의 **`limit: 0`** — 단순 quota 초과가 아니라 "이 모델에 할당된 free tier 한도 자체가 0" 이라는 의미. retryDelay 가 표시되어 있어도 분당 한도 회복으로 풀리는 종류가 아님.

## 원인

- Google AI 가 2.5 시리즈 (gemini-2.5-flash / pro / flash-lite) 를 출시한 뒤 free tier 의 **2.0 시리즈 분당/일 한도를 0 으로 줄였음** (정확한 시점은 공식 발표 전이지만 2026 봄 시점에 이 동작이 광범위하게 관측됨)
- 사용자/프로젝트가 잘못된 게 아니라 **모델 자체가 무료 사용에서 빠짐**
- 유료 결제가 켜진 GCP project 라면 같은 코드가 동작할 수 있음 (free 한정 차단)

## 수정

```ts
// lib/ai.ts (수정 전)
const model = genAI.getGenerativeModel({ model: "gemini-2.0-flash" });

// lib/ai.ts (수정 후)
// 2.0-flash 는 free tier 에서 제한됨 (2026 기준). GEMINI_MODEL 로 override 가능.
const model = genAI.getGenerativeModel({
  model: process.env.GEMINI_MODEL ?? "gemini-2.5-flash",
});
```

| 항목 | 변경 |
|---|---|
| 기본 모델 | `gemini-2.0-flash` → `gemini-2.5-flash` |
| Override | `GEMINI_MODEL` 환경변수로 코드 변경 없이 교체 가능 |
| 백업 옵션 | 같은 429 가 반복되면 `GEMINI_MODEL=gemini-2.5-flash-lite` (더 관대한 한도) |

## 모델별 free tier 한도 (2026 봄 기준 대략)

| 모델 | RPM (분당) | RPD (일) | 비고 |
|---|---|---|---|
| `gemini-2.5-flash` | ~10 | ~250 | **신규 default 권장** |
| `gemini-2.5-flash-lite` | 더 관대 | 더 관대 | quota 빠듯할 때 fallback |
| `gemini-2.5-pro` | 작음 | 작음 | 품질 요구 시. free 한도 작음 |
| `gemini-2.0-flash` | **0** | **0** | free 차단 |

수치는 시점에 따라 변동. 실제 한도는 Google AI Studio dashboard 에서 확인.

## 재발 방지 — 모델 ID 를 env override 가능하게

하드코딩된 모델 ID 는 **provider 가 모델 라이프사이클을 바꿀 때마다 재배포** 가 필요해 운영 부담이 크다. 일관된 패턴:

```ts
const model = client.getModel({
  model: process.env.<VENDOR>_MODEL ?? "<sensible-default>",
});
```

- 새 모델 출시 → env 만 바꿔 즉시 교체
- 한 모델이 차단되어도 hotfix 배포 없이 회복
- 어댑터 패턴과 함께 쓰면 더 강력 ([[multi-llm-provider-adapter-pattern]] 참고)

## 디버깅 시 빠른 분기

429 가 떴을 때:

| 메시지 안 단서 | 해석 | 조치 |
|---|---|---|
| `limit: 0` | 모델 자체가 free 차단 | 다른 모델 ID 로 교체 |
| `limit: N` (N>0) + `retryDelay` 짧음 | 분당 한도 도달 | 재시도 / 호출 빈도 조절 |
| `limit: N` + 일 한도 메시지 | 일 한도 소진 | 다음날 / 결제 활성화 / 다른 provider 로 fallback |
| `API key not valid` | 키 문제 | aistudio.google.com 에서 신규 키 발급 |

## 관련 맥락

- 이 사건이 [[japa-asset-dashboard]] 에서 멀티 LLM 어댑터 도입을 가속한 직접 원인 — 단일 provider 의존 위험을 즉시 체감
- 어댑터로 분기해 두면 `gemini` provider 가 차단돼도 `openai` / `anthropic` 으로 즉시 전환 가능 ([[multi-llm-provider-adapter-pattern]])

## 변경 이력

- 2026-05-03: 최초 생성. japa 프로젝트의 `/ai` 분석 페이지에서 발생한 사건 기록 (출처: session-logs/20260503-100914-b80f-*).
