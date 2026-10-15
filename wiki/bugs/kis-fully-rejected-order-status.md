---
title: "KIS 전량 거부 주문을 OPEN으로 분류한 오류"
domain: "trading"
sensitivity: "public"
tags: ["kis", "order-status", "reconciliation"]
created: "2026-10-16"
updated: "2026-10-16"
sources:
  - "session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md"
confidence: "medium"
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/trading-api-failure-handling.md"
  - "wiki/bugs/order-post-retry-double-fill.md"
---

# KIS 전량 거부 주문을 OPEN으로 분류한 오류

브로커의 전량 거부를 내부 미체결 상태로 취급하면 체결 대기·대사·마감 보고가 함께 틀어진다. 10/15 ht_trading 세션은 전량 거부를 `REJECTED`로 분류하고 후속 경로까지 수정했다고 보고했다.

## 핵심 내용

- 증상: KIS 원본상 66주 전량 거부 주문이 내부 조회에서는 `OPEN`으로 표시됐다.
- 수정: 전량 거부 상태를 `REJECTED`로 처리하고 시장가 주문의 체결 대기, 보고, 대사 경로에 함께 반영했다. 세션에는 잘못된 분류를 만든 구체 조건식까지 제시되지 않아 추정하지 않는다.
- 검증 보고: 실제 KIS 재조회에서 해당 주문이 `REJECTED`, 마감 보고가 체결 3건·미체결 0건·거부 1건으로 나왔다. 백업 후 감사 상태 1행을 정정했고 기존 체결 126건과 전략 상태를 보존했다.
- 국내 서비스 반영·재시작 및 커밋 `a06f4ca`, 최종 테스트 714개·서브테스트 13개 통과가 보고됐다. 이는 세션의 결과이며 이번 수집에서 코드·서비스를 재검증하지 않았다.

## 관련 맥락

주문 제출 응답만 확인하지 말고 조회 → 내부 상태 → 체결 대기 종료 → 대사 → 보고의 일관성을 확인한다. 비멱등 주문 재시도 문제는 [[order-post-retry-double-fill]], 계좌 조회 실패와 오류 보고 기준은 [[trading-api-failure-handling]], 적용 상태는 [[ht-trading]]을 참조한다.

## 변경 이력

- 2026-10-16: 최초 생성, 10/15 세션의 최종 수정·판단을 수집 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)
