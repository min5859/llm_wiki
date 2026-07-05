---
title: "비멱등 주문 POST 자동 재시도 — 이중 체결 위험과 취소 오판"
domain: "trading"
sensitivity: "public"
tags: ["bug", "trading", "kis", "retry", "idempotency", "double-fill", "circuit-breaker"]
created: "2026-07-05"
updated: "2026-07-05"
sources:
  - "session-logs/20260704-130158-2dad-현재-프로젝트에서-개선할-부분이-있을지-검토해줘.md"
confidence: high
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/risk-control-exemption-and-failed-attempt-accounting.md"
  - "wiki/analyses/partial-sell-rule-idempotency.md"
---

# 비멱등 주문 POST 자동 재시도 — 이중 체결 위험과 취소 오판

HTTP 클라이언트의 공용 재시도 래퍼가 **주문 제출 POST 까지 재전송**하면, 서버가 주문을 접수했지만 응답만 유실된 경우 같은 주문이 실제로 2번 체결된다. ht_trading 라이브 주문 경로 검토에서 발견된 실거래 자금 직접 손실 위험 (2026-07-04, commit e40db25 / b357c14).

## 1. 이중 체결 — 멱등키 없는 주문 POST 의 재시도

- **위험 시나리오**: `_request_with_retry` 가 타임아웃·5xx 시 POST 를 최대 2회 재전송. KIS 주문 body 에는 멱등키(client order id)가 없다 → 거래소는 주문을 접수했는데 응답만 유실된 경우, 재시도가 **동일 주문을 신규 접수**시킨다. 로컬은 마지막 1건만 기록하므로 잔고·포지션이 조용히 어긋난다.
- **수정**: `KISClient.post` 에 `retry: bool` 파라미터 추가. **주문 제출 POST 2곳** (국내 order-cash, 해외 order) 만 `retry=False` (1회 시도). 취소(order-rvsecncl)·GET·해시키 발급은 기존 재시도 유지 — 취소는 멱등에 가까워 이중 체결 위험이 없다.
- **후속 견고화 방향**: Timeout/HTTPError 시 무조건 포기 대신, **당일주문 조회로 실제 접수 여부를 reconcile 한 뒤** 미접수 확인 시에만 재주문.

> 일반 원칙: **비멱등 쓰기 요청은 자동 재시도에서 제외**하거나, 멱등키를 붙이거나, "조회로 확정 후 재시도"로 감싼다. 결제·주문·전송 API 전반에 적용.

## 2. 취소 오판 — 응답 유실 후 재시도의 "이미 취소" 에러

- **위험 시나리오**: 미체결 지정가 취소 요청이 거래소에 접수됐으나 응답만 유실 → 재시도가 "이미 취소된 주문" 류 `rt_cd` 에러를 수신 → **성공한 취소를 실패로 기록**. 더 나쁘게는 이 가짜 실패가 서킷브레이커 카운터(`_on_api_error`, 5회 연속 시 매매 중지)를 오염시켜 **오판이 다른 안전장치까지 잘못 발동**시킨다.
- **수정**: 취소 예외 시 미체결 목록을 재조회해 **대상 주문이 더 이상 OPEN/PARTIAL 이 아니면 취소 성공으로 판정** (체결로 사라졌더라도 "취소할 것이 없음"은 참).

> 일반 원칙: **멱등 연산의 에러는 실패로 단정하기 전에 대상 상태를 재조회해 확정**한다. 오판 결과가 서킷브레이커·알림·카운터 같은 다른 안전장치로 전파되는 결합 결함에 특히 주의.

## 관련 맥락

- 실패 시도의 카운터 회계·유령 체결 등 인접 원칙 묶음은 [[risk-control-exemption-and-failed-attempt-accounting]].
- SELL 경로의 멱등성·dedup 부재는 [[partial-sell-rule-idempotency]].

## 변경 이력

- 2026-07-05: 최초 생성 — ht_trading 라이브 주문 경로 전면 검토에서 발견·수정 (출처: session-logs/20260704-130158-2dad-*)
