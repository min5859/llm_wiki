---
title: "Mock-first 데모 안전망 — 외부 의존이 막혀도 데모가 보장되는 MVP 설계"
domain: both
sensitivity: public
tags: ["pattern", "hackathon", "mvp", "mock", "adapter", "demo", "fallback", "claude-code"]
created: 2026-06-18
updated: 2026-06-18
sources:
  - "session-logs/20260617-220010-47ab-내가-해커톤-주제를-구체화-시키고-있는데-좀-도와줘.md"
  - "session-logs/20260618-063919-3962-지금-프로젝트-시작-지침서-인데-완성도를-평가해줘.md"
confidence: high
related:
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
  - "wiki/projects/hermes-dashboard.md"
---

# Mock-first 데모 안전망

시간이 극도로 제한된 빌드(하루짜리 해커톤, MVP, PoC)에서 **외부 시스템 연동이 당일에 막혀도 데모는 반드시 돌게** 만드는 설계 패턴. 핵심은 "mock 을 나중에 떼어내는 fallback" 이 아니라, **mock 을 기본값(default)으로 두고 real 을 opt-in 으로 켜는 것**이다. [[hermes-dashboard]] 해커톤 기획에서 일반화.

## 왜 fallback 이 아니라 default 여야 하는가

"일단 real 로 만들고 안 되면 mock 으로 떨어뜨린다"는 순서는 위험하다. real 경로가 막히는 시점이 보통 데모 직전이고, 그때 mock 으로 되돌릴 시간이 없다. 반대로 **mock 이 default 면 앱은 환경값 0개로 항상 켜지고**, real 은 검증된 만큼만 점진적으로 켜진다. "최악의 경우에도 데모가 보장"되는 하한선이 처음부터 깔린다.

> 하루짜리 빌드의 가장 큰 적은 버그가 아니라 **외부 미지수**(API 형식 모름, 사내망 도달성, 패키지 다운로드 가능 여부)다. mock-first 는 이 미지수들을 데모 성공 여부에서 분리한다.

## 세 가지 구성 요소

### 1. 모드 스위치 + 기본값 mock

`HERMES_MODE=mock` 같은 단일 환경변수를 두고 **기본값을 mock 으로**. 클론 직후 `.env` 를 안 채워도 데모가 돈다. real 은 명시적으로 켤 때만.

### 2. 어댑터 경계 (seam)

UI/소비자는 추상 이벤트만 소비하고, mock/real 은 같은 인터페이스를 구현하는 어댑터로 교체한다.

```
chat(agentId, messages): AsyncIterable<HermesEvent>   // token | delegation | done
```

UI 는 `HermesEvent` 만 알면 되므로, mock→real→(나아가 자체 구현)까지 **UI 를 한 줄도 안 고치고 어댑터만 추가/교체**한다. 엔티티별 `isLive()` 헬퍼로 "이 에이전트만 real, 나머지 mock" 같은 **부분 real** 도 가능 — N개 중 1~2개만 진짜로 붙이고 나머지는 mock 으로 채워 데모 그림을 완성한다. (다중 provider 를 같은 인터페이스로 묶는 일반론은 [[multi-llm-provider-adapter-pattern]])

### 3. 모든 외부의존 함정에 폴백 내장

각 미지수마다 "안 되면 이렇게 완결" 경로를 문서에 못박는다. 예:

| 미지수 | 폴백 |
|---|---|
| 스캐폴더가 비지 않은 폴더 거부 | 임시 폴더에 스캐폴드 후 병합 |
| 사내망에서 패키지 registry 접근 불가 | mock UI 구현은 가능(real 연동만 막힘) |
| 외부 API 서버 도달 불가 | mock 모드로 데모 완결 |
| 데이터 API 엔드포인트 부재 | 로컬 store 전용 + config 하드코딩 데이터 |

mock 데이터는 별도 만들지 말고 **UI 정본(mockup.html 등)과 같은 소스**로 두면 일관성이 유지된다.

## 짝꿍 원칙 — 외부 연동 가정은 소스/문서로 검증

mock-first 가 "막혀도 데모는 된다"를 보장하더라도, **real 경로 설계가 추측에 기반하면 당일에 무너진다.** [[hermes-dashboard]] 에서는 문서 초안의 "real 연동" 가정 3개(요청별 프로필 선택 가능, 위임이 chat completions 에 실림, Kanban write-back API 존재)가 **소스코드·공식문서 확인 결과 전부 틀렸다.** 추측한 채로 환경값만 채웠다면 "잘못된 방향으로 구현하다 막혔을" 것이다.

- 외부 시스템 연동을 문서화할 때 미확정 사실은 **"가정하지 말 것" 목록**으로 명시하고 착수 전 검증(Phase 0)으로 묶는다.
- "배관 완료(파이프라인이 끝까지 돈다) ≠ 기능 완료(real 이 실제로 붙는다)". mock 데모가 돈다고 real 연동이 검증된 것은 아니다.

## "되는 것처럼 보임" 과 "실제로 됨" 을 정직하게 구분

mock 데모는 본질적으로 **스크립트된 연출**(하드코딩된 시나리오 재생)이다. 데모 임팩트는 주지만, 그게 진짜 동작이라고 자신·청중을 속이지 말 것. 슬라이드/문서에 미검증 기능은 *(스트레치)* 표기를 달아, 당일 검증 실패 시 "시연 안 되는 걸 주장하는" 사태를 막는다.

## 관련 맥락

- [[multi-llm-provider-adapter-pattern]] — 여러 백엔드를 단일 인터페이스로 묶는 어댑터 일반론 (mock 도 그 한 구현)
- [[hermes-dashboard]] — 이 패턴이 적용된 해커톤 프로젝트 (HERMES_MODE=mock 기본값, isLive() 부분 real)

## 변경 이력

- 2026-06-18: 최초 생성 — hermes-dashboard 해커톤 기획에서 일반화. mock=default + 어댑터 seam + 함정별 폴백 + 가정 소스검증 + 정직한 mock/real 구분 (출처: session-logs/20260617-220010-47ab-*, 20260618-063919-3962-*)
