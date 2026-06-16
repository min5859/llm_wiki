---
title: "반복 알림 dedup/throttle — 로그는 매번, 텔레그램은 (키)당 하루 1회"
domain: "both"
sensitivity: "public"
tags: ["notification", "telegram", "dedup", "throttle", "alert-fatigue", "audit-log", "polling", "ht-trading"]
created: "2026-06-17"
updated: "2026-06-17"
sources:
  - "session-logs/20260616-225148-21a4-아래-사항은-오늘-로그에서-나온-사항들인데-개선할만한-포인트가-있을까--•-BUY-0106.md"
  - "session-logs/20260616-210439-d6f6-오늘-알고리즘-바꾼지-2일-지났는데-2일-동안-매매에서-오류나-개선점이-없었는지-검토해줘.md"
confidence: high
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/bugs/kis-cash-d2-settlement-buy-rejection.md"
---

# 반복 알림 dedup/throttle 패턴

폴링 루프(매 N분 사이클)가 **상태가 안 변하는 동안 같은 알림을 매 사이클 재전송**해 채널을 도배하는 문제의 표준 처방. `ht_trading` 라이브 엔진에서 보유가 한도(`max_positions=11`)에 차자 매 ~2분 사이클마다 새로 선정된 후보가 전부 거부되고, 거부 1건마다 무조건 텔레그램을 보내 **하루 239건**(같은 종목이 종일 반복)이 발송된 사례에서 도출.

## 안티패턴

```python
# 매 사이클 호출 → 상태 동일해도 매번 전송
if rejected:
    self._notify(f"[리스크 거부] 매수 거부: {symbol} ({reason})")
```

폴링 해상도가 곧 알림 빈도가 된다. 거부/대기/에러처럼 **"해소될 때까지 매 사이클 참인 조건"** 은 전부 이 함정에 빠진다.

## 패턴

`_notify` 에 선택적 `dedup_key` 를 추가하고, **로그(`logger.info`)는 항상 먼저 실행**한 뒤 dedup 이 외부 전송(텔레그램)만 가로막는다.

```python
def _notify(self, text, dedup_key=None):
    logger.info(text)                      # 감사 추적: 매 사이클 그대로 기록
    if dedup_key is not None:
        today = now_kst().strftime("%Y-%m-%d")
        if today != self._notify_dedup_date:   # 날짜 바뀌면 리셋
            self._notify_dedup_date = today
            self._notified_keys.clear()
        if dedup_key in self._notified_keys:
            return                         # 텔레그램만 스킵 (로그는 이미 남음)
        self._notified_keys.add(dedup_key)
    self.notifier.send(text)
```

호출부는 `dedup_key=(symbol, reject_reason)` 를 넘긴다. 사유가 바뀌면(예: 한도 초과 → 현금 부족) **키가 달라져 다시 1회 알린다.**

## 설계 포인트

- **로그와 외부 전송을 분리한다.** 로그(live.log)는 매 사이클 남겨 사후 분석/감사를 보존하고, 비용/피로가 있는 채널(텔레그램·이메일·슬랙)만 억제한다. "조용히 시키되 증거는 남긴다."
- **키 설계가 핵심.** `(엔티티, 사유)` 가 보통 옳다. 키를 너무 넓게(엔티티만) 잡으면 사유 전환을 놓치고, 너무 좁게(타임스탬프 포함) 잡으면 dedup 이 무의미해진다.
- **리셋 주기를 명시한다.** 보통 하루 1회(날짜 변경 시 set clear). 여전히 막혀 있으면 다음 날 1회 재알림 → "아직도 문제"라는 신호를 잃지 않는다.
- **dedup 은 판단/실행 경로를 건드리면 안 된다.** 위 코드에서 슬롯이 비면 다음 사이클에 매수 신호가 새로 생성·체결되고, 체결 알림은 `dedup_key` 없는 별도 `_notify` 로 정상 발송된다. 즉 **알림만 줄지 기회는 안 놓친다.**
- **보완: EOD 요약.** 매 사이클 알림을 끄면 "오늘 무슨 일이 있었나"가 안 보일 수 있다. 억제한 이벤트(예: 한도로 보류된 후보)를 하루 단위로 모아 **장 마감 후 1건 요약**으로 가시화하면 dedup 과 잘 맞는다(`ht_trading` 은 둘을 함께 적용).

## 적용/미적용 선택지 (사례)

ht_trading 에서 3안 중 1·3안 적용:
1. **(적용) 거부 알림 dedup** — `(종목, 사유)`당 하루 1회. 변경 최소·저위험. → 239건 폭주 해소.
2. (미적용) 한도 도달 시 매수 시도 자체 스킵 — 거부 루프를 없애지만 변경 범위가 큼.
3. **(적용) EOD 요약** — "보유 한도로 매수 보류: N종목" 1건을 마감 스냅샷에 추가.

## 변경 이력

- 2026-06-17: 최초 작성. 2026-06-16 ht_trading 세션(거부 알림 239건 폭주 dedup + EOD 요약, commit `621be66`/`84e15f6`)에서 추출 (출처: session-logs/20260616-225148-21a4-*, 20260616-210439-d6f6-*)
