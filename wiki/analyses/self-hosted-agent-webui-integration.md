---
title: "셀프호스팅 에이전트에 커스텀 UI 붙이기 — 내부 직결 vs OpenAI 호환 HTTP API"
domain: "ai-agent"
sensitivity: public
tags: ["analysis", "ai-agent", "self-hosted", "architecture", "coupling", "openai-compatible", "hermes", "webui"]
created: 2026-06-18
updated: 2026-06-18
sources:
  - "session-logs/20260617-220010-47ab-내가-해커톤-주제를-구체화-시키고-있는데-좀-도와줘.md"
confidence: high
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/projects/hermes-dashboard.md"
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
---

# 셀프호스팅 에이전트에 커스텀 UI 붙이기 — 두 가지 연동 방식

self-hosted AI 에이전트(Hermes 류) 위에 커스텀 웹 UI/대시보드를 얹을 때, 프론트엔드가 에이전트 런타임에 붙는 방식은 크게 **둘로 갈린다.** 둘은 결합도·언어 자유·운영 비용이 정반대다. [[hermes-agent]] 생태계의 두 OSS — `hermes-webui`(nesquena) vs `hermes-workspace`(Studio) — 가 정확히 이 대비를 이룬다. 같은 에이전트에 붙는 두 프로젝트인데 연동 방식이 정반대라, 선택 기준을 일반화하기 좋은 사례다.

## 방식 A — 내부 직결 (tight coupling)

UI 앱이 에이전트의 **내부 모듈을 직접 import** 하고 같은 데이터 디렉터리를 마운트한다. (예: `hermes-webui` 가 `api/config.py`·`api/streaming.py` 를 import, 같은 `~/.hermes/` 공유)

- **장점**: 내부 상태/스트림에 직접 접근 — 별도 API 표면이 없어도 깊은 기능을 그대로 쓴다.
- **단점**:
  - **같은 호스트 + 같은 언어**에 묶인다 (에이전트가 Python 이면 UI 도 Python).
  - **버전 동기화 필수** — 에이전트 내부 변경에 UI 가 깨지므로 같은 릴리스로 함께 업그레이드해야 한다.
  - 원격 분리 배포가 어렵다.

## 방식 B — OpenAI 호환 HTTP API (loose coupling)

UI 앱이 에이전트의 **HTTP/SSE API 서버**(예: `:8642`, OpenAI 호환)에 네트워크로 붙는다. (예: `hermes-workspace` 의 `hermes webapi`(FastAPI), Open WebUI 연동)

- **장점**:
  - **언어 자유** — Next.js/바닐라 JS 등 무엇으로든 붙는다 (에이전트가 Python 이어도 무관).
  - **버전 결합 없음** — 깔끔한 네트워크 경계라 내부 변경에 잘 안 깨진다.
  - **원격 가능** — 사내 서버의 에이전트에 별도 호스트의 웹앱이 HTTP 로 접속.
- **단점**: API 표면이 노출하는 것만 쓸 수 있다 (예: 스킬은 `GET /v1/skills` 로 되지만 Kanban write-back 엔드포인트는 없을 수 있음 → 그 기능은 로컬 전용으로 우회).

## 선택 기준

| 따져볼 것 | 내부 직결(A) | HTTP API(B) |
|---|---|---|
| UI 를 다른 언어/프레임워크로 짜고 싶다 | ✗ | ✅ |
| 에이전트와 UI 를 다른 호스트에 배포 | ✗ | ✅ |
| 에이전트 버전 업그레이드에 안 깨지고 싶다 | ✗ (동기화) | ✅ |
| API 가 노출 안 한 깊은 내부 기능이 필요 | ✅ | ✗ (우회 필요) |
| 새로 처음부터 짜는 경우 | 포크·해독 부담 | **권장** |

> **일반 결론**: 처음부터 새 UI 를 짠다면 **HTTP API(B) 가 거의 항상 낫다.** API 서버가 무거운 일(런타임·sub-agent·이벤트)을 다 해주므로 UI 는 "얇은 프록시 + 화면"만 짜면 되고, 언어·배포·버전이 모두 자유롭다. 시간 압박 속에 내부 직결형 OSS 를 포크·해독하는 건 오히려 리스크다. 단, 같은 API 진영의 다른 OSS(workspace 등)는 **코드 포크가 아니라 API 호출 패턴 참고용**으로 활용할 수 있다.

## 관련 맥락

- [[hermes-agent]] — 내장 API 서버(:8642 OpenAI 호환)의 표면·제약(model 필드 장식용, Runs API, 스킬/Kanban API 유무)
- [[hermes-dashboard]] — HTTP API(B) 방식을 택해 스크래치 신규 구현한 해커톤 프로젝트
- [[multi-llm-provider-adapter-pattern]] — 어느 방식이든 어댑터 경계로 추상화하면 백엔드 교체가 쉬워진다

## 변경 이력

- 2026-06-18: 최초 생성 — hermes-webui(내부 직결) vs hermes-workspace(OpenAI 호환 HTTP API) 비교에서 일반화. 신규 UI 는 HTTP API 권장 (출처: session-logs/20260617-220010-47ab-*)
