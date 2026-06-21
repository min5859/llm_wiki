---
title: "라이브 의존이 막혔을 때 — 모델 무관 작업 최대화 + 파서 선구현 + 동작 코드 우선"
domain: both
sensitivity: public
tags: ["workflow", "ai-agent", "verification", "parser-first", "testing", "blocker", "spec-vs-code"]
created: "2026-06-21"
updated: "2026-06-21"
sources:
  - "session-logs/20260621-154117-e509-▎-..-HANDOFF.md-와-..-CLAUDE.md-읽고-Phase-2-UI부터-이어가.md"
confidence: medium
related:
  - "wiki/patterns/test-driven-agent-loop.md"
  - "wiki/patterns/mock-first-demo-safety-net.md"
  - "wiki/projects/hermes-dashboard.md"
---

# 라이브 의존이 막혔을 때 — 모델 무관 작업 최대화 + 파서 선구현 + 동작 코드 우선

외부 의존(LLM 백엔드 재인증, 미기동 서버 등)이 막혀 "mock 금지·real 검증" 원칙상 블라인드 빌드도 못 하는 상황에서, idle 하지 않고 **진척을 최대화하면서 위험을 격리**하는 작업 패턴. 세 원칙이 함께 작동한다.

## ① 블로커는 먼저 명확히 보고, 그다음 모델 무관 작업으로 최대화

블로커(예: 사용자만 할 수 있는 OAuth 재인증)를 발견하면 **먼저 명확히 보고**하되 멈추지 않는다. 라이브 의존이 *없는* 작업을 끝까지 완결한다:

- plan / design 문서
- 순수 함수 + 단위 테스트
- readonly 데이터 검증(DB 직접 읽기 등)
- 컴파일/타입 검증 (`tsc --noEmit`, 번들러 transform)

라이브가 필요한 부분만 **isolated 모듈로 격리**해 재인증 후로 미룬다. 위험도 높은 코어(파서 등)는 테스트로 고정해, 나중에 라이브 결과로 그 모듈만 안전하게 교체할 수 있게 한다.

## ② 파서 선구현(parser-first) — 라이브 없이 스펙 오류를 잡는다

라이브 캡처를 기다리지 않고 **파서 + 단위 테스트를 먼저** 작성한다. 작성 과정 자체가 검증 도구가 된다: 테스트를 짜려고 입력 형태를 추적하다 보면 핸드오프 문서/스펙의 오류가 드러난다.

> 실제 사례: 위임 이벤트 형태를 "OpenAI Responses 스타일(`response.output_item.added`)"이라 적은 핸드오프 문서가, 같은 저장소의 *이미 동작하는 코드* 가 쓰는 어휘(`{event:"tool.started/completed"}`)와 달랐다. 파서를 짜면서 라이브 없이 문서가 틀렸음을 확인하고 정정.

## ③ 권위 출처는 "스펙 문서"가 아니라 "이미 동작하는 코드"

추측은 보통 두 번 틀린다 — 스펙 문서에서 한 번, 라이브 캡처에서 또 한 번. 따라서:

- **스펙 문서 < 동작 코드.** 형태가 의심되면 그 데이터를 *실제로 처리하는 기존 코드* 를 권위 출처로 삼는다.
- 그래도 라이브로 최종 확인한다(②의 사례에서도 실제 필드는 또 달랐다). 단, ②에서 테스트로 고정해 둔 덕에 파서 필드만 바꾸면 돼 교정이 안전했다.

## 핵심 요약

블로커 → **보고 후 idle 금지**. 모델 무관 작업으로 진척을 밀고, 위험 코어는 **테스트로 고정**해 격리. 형태 검증은 **동작 코드 > 스펙 문서 > 추측** 순으로 권위를 두되 최종은 라이브로 닫는다.

## 관련 맥락

[[hermes-dashboard]] 의 Phase 2~6 구현에서, 모델 백엔드 재인증 블로커 동안 파서/순수함수/readonly DB 검증을 선완결한 사례. 테스트로 코어를 고정하는 일반 루프는 [[test-driven-agent-loop]], 외부 의존 폴백 전략은 [[mock-first-demo-safety-net]].

## 변경 이력

- 2026-06-21: 최초 작성 (출처: session-logs/20260621-154117-e509)
