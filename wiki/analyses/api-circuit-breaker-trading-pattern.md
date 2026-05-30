---
title: "외부 API 서킷브레이커 패턴 — 연속 오류 시 주문 중지"
domain: personal
sensitivity: public
tags: ["analysis", "trading", "circuit-breaker", "api", "reliability", "kis"]
created: 2026-05-30
updated: 2026-05-30
sources:
  - "session-logs/20260530-110224-e6bb-ht_trading은-min_score=48에서-하루-종일-후보-0개였습니다.-현재-시장.md"
confidence: high
related:
  - "wiki/projects/ht-trading.md"
---

# 외부 API 서킷브레이커 패턴 — 연속 오류 시 주문 중지

실시간 거래 시스템에서 외부 API(증권사, 거래소 등)가 연속으로 실패할 때 **오래된 캐시 기반으로 잘못된 주문이 나가는 것을 방지**하는 안전 장치 패턴.

## 문제 설정

```
get_cash() / get_equity() → KIS API 500 오류 반복
→ 캐시 값(_last_known_cash) 사용 계속
→ 캐시가 30분 이상 오래되어도 주문은 정상 진행
→ 실제 잔고와 괴리된 금액으로 매수 주문 발생
```

## 서킷브레이커 구현 (Python 예시)

```python
# KISBroker 클래스 (ht_trading 구현)
_api_consecutive_errors: int = 0
api_halted: bool = False
_last_known_cash_ts: float = 0.0
_CACHE_TTL_WARN_SECONDS = 1800   # 30분 초과 시 경고
_ERROR_HALT_THRESHOLD = 5        # 연속 5회 실패 시 주문 중지

def _on_api_success(self) -> None:
    self._api_consecutive_errors = 0
    if self.api_halted:
        self.api_halted = False
        logger.info("API 회복 — 주문 재개")

def _on_api_error(self, err: Exception) -> None:
    self._api_consecutive_errors += 1
    if self._api_consecutive_errors >= self._ERROR_HALT_THRESHOLD:
        self.api_halted = True
        logger.error("API 연속 오류 %d회 — 주문 중지", self._api_consecutive_errors)

def get_cash(self) -> float:
    try:
        cash = self._call_api_for_cash()
        self._on_api_success()
        self._last_known_cash = cash
        self._last_known_cash_ts = time.time()
        return cash
    except Exception as e:
        self._on_api_error(e)
        elapsed = time.time() - self._last_known_cash_ts
        if elapsed > self._CACHE_TTL_WARN_SECONDS:
            logger.warning("캐시 만료 (%.0f분) — 오래된 잔고 사용 중", elapsed / 60)
        return self._last_known_cash

def submit_order(self, order: Order) -> str:
    if self.api_halted:
        logger.warning("주문 차단 (API halt): %s", order.symbol)
        order.status = OrderStatus.REJECTED
        return order.order_id
    # ... 정상 주문 처리
```

## 주요 설계 포인트

### 1. 카운터 vs 단순 플래그

단순 `try/except → halt` 대신 **연속 N회** 임계치를 사용하는 이유:
- 일시적 네트워크 오류 한 번에 주문이 멈추면 정상 거래 방해
- N회 연속 = 일시 오류가 아닌 지속 장애로 판단

### 2. 호출 단위 카운터 주의

`get_cash`와 `get_equity`를 별도 API 호출로 구현 시 한 사이클에서 **둘 다 실패하면 카운터 2씩 증가**. 임계치를 N으로 설정하면 실제로는 N/2 사이클 연속 실패 시 중지. 의도에 맞게 임계치 조정 필요.

### 3. TTL 경고 vs 즉시 차단

캐시 만료는 **경고만** (주문은 허용). 이유: 일봉 전략에서 30분 캐시는 수용 가능한 경우가 있음. `api_halted`(주문 차단)와 캐시 만료 경고는 별도 신호로 분리.

### 4. 회복 알림의 단 1회 발송

```python
# LiveEngine (ht_trading)
_api_halt_notified: bool = False

def _check_api_halt_and_notify(self) -> None:
    if self.broker.api_halted and not self._api_halt_notified:
        self._notify("⚠️ API 장애 — 주문 중지")
        self._api_halt_notified = True
    elif not self.broker.api_halted and self._api_halt_notified:
        self._notify("✅ API 회복 — 주문 재개")
        self._api_halt_notified = False
```

알림 중복 방지 플래그 (`_api_halt_notified`)로 halt/회복 전환 시에만 텔레그램 발송.

## 적용 체크리스트

- [ ] 연속 오류 카운터 + 임계치 설정 (`_ERROR_HALT_THRESHOLD`)
- [ ] 성공 시 카운터 리셋 + 회복 로그
- [ ] `submit_order` 선두에 `api_halted` 체크 → REJECTED 반환
- [ ] 캐시 반환 시 경과 시간 로그 + TTL 초과 경고
- [ ] halt/회복 전환 시 텔레그램 알림 1회 (중복 방지 플래그)
- [ ] 단위 테스트: halt 후 주문 차단, 회복 후 주문 통과, 캐시 TTL 경고

## ht_trading 구현 현황

`KISBroker` + `LiveEngine` 에 구현 완료 (2026-05-30, commit `32a1451`). 임계치 5회, TTL 경고 30분, 테스트 17개.

## 변경 이력

- 2026-05-30: 최초 작성 (ht_trading KIS 서킷브레이커 구현에서 일반화)
