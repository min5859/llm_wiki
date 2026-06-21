---
title: "ESM live binding 으로 전역 의존성을 한 점에서 전환하기"
domain: both
sensitivity: public
tags: ["esm", "javascript", "typescript", "live-binding", "global-state", "refactoring", "single-user-tool"]
created: "2026-06-21"
updated: "2026-06-21"
sources:
  - "session-logs/20260621-154117-e509-▎-..-HANDOFF.md-와-..-CLAUDE.md-읽고-Phase-2-UI부터-이어가.md"
confidence: medium
related:
  - "wiki/projects/hermes-dashboard.md"
---

# ESM live binding 으로 전역 의존성을 한 점에서 전환하기

수십 개 모듈이 import 하는 단일 상수(예: 게이트웨이 baseUrl)를 런타임에 바꿔야 할 때, 모든 호출 경로에 값을 파라미터로 스레딩하는 대신 **ESM 의 live binding 특성**을 활용해 한 군데서 재할당하면 모든 importer 에 자동 반영된다. 단일 사용자 도구의 "활성 대상 전환"에 특히 최소 침습적이다.

## 메커니즘 — `export let` 은 라이브 참조다

ESM 의 named export 는 **값 복사가 아니라 라이브 바인딩**이다. `export let x` 를 import 한 쪽은 x 의 *현재 값* 을 항상 보므로, export 한 모듈 내부에서 setter 로 재할당하면 모든 importer 가 새 값을 본다.

```js
// gateway.ts
export let activeBaseUrl = DEFAULT_URL          // const 아님 — let
export function setActiveGateway(url) {
  activeBaseUrl = url                            // 모든 importer 에 즉시 반영
  reprobeCapabilities(url)                       // 부수효과(재probe)도 여기 한 곳에서
}

// 24개 파일: import { activeBaseUrl } from './gateway'  ← 수정 불필요
```

> 주의: importer 가 `import { activeBaseUrl }` 로 받아야 한다. `const { activeBaseUrl } = await import(...)` 로 구조분해하면 그 시점 값이 복사돼 라이브 바인딩이 끊긴다.

## 언제 쓰나 / 언제 피하나

- **적합**: 단일 사용자·단일 프로세스 도구의 전역 "활성 X" 전환(활성 게이트웨이/프로필/워크스페이스). 호출부 24개를 건드리지 않고 setter 1개 + `let` 1개로 끝난다.
- **부적합**: 멀티 사용자/동시 요청 환경. 전역 가변 상태라 요청 A 가 바꾼 값을 요청 B 가 보는 경쟁이 생긴다 → 이 경우는 per-request 컨텍스트로 baseUrl 을 명시적으로 흘려야 한다.

핵심 트레이드오프는 **"전역 전환의 최소 침습" vs "요청 단위 격리"**. 동시성이 없다고 확신할 때만 전자를 택한다.

## 관련 맥락

[[hermes-dashboard]] 의 에이전트별 게이트웨이 전환에서 채택. 채팅 백엔드 ~24개 파일이 단일 게이트웨이 상수를 import 하던 구조라, per-request 스레딩(24파일 수정) 대신 live binding setter 로 전환해 한 점에서 게이트웨이 + capability 재probe 를 처리했다.

## 변경 이력

- 2026-06-21: 최초 작성 (출처: session-logs/20260621-154117-e509)
