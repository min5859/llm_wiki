---
title: "KIS 파생·레버리지 ETF 주문 거부 [APBK1497] — 코드가 아니라 계좌 권한"
domain: "trading"
sensitivity: "public"
tags: ["bug", "trading", "kis", "etf", "leverage", "derivative", "order-reject", "apbk1497", "account"]
created: "2026-07-08"
updated: "2026-07-08"
sources:
  - "session-logs/20260707-223019-2c24-오늘-남성이라는-종목이-추천되서-매수가-되었는데-실제-스코어가-62-점이-넘었는지-확인해줘.md"
confidence: "high"
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/dca-intraday-buy-timing.md"
  - "wiki/bugs/kis-cash-d2-settlement-buy-rejection.md"
  - "wiki/bugs/relative-stop-benchmark-stale-price.md"
---

# KIS 파생·레버리지 ETF 주문 거부 [APBK1497]

레버리지 ETF(예: `0195S0` TIGER SK하이닉스단일종목레버리지)를 무한매수 대상에 추가했더니 **매수 신호는 정상 생성되는데 주문만 24회 전부 거부**됐다. 원인은 코드/설정 버그가 아니라 **KIS 계좌에 파생형 ETF 거래 권한이 없어서**였다. 파생/레버리지 ETP 를 신규 편입할 때 반복될 함정.

## 증상

- 30분 매수 사이클마다 `0195S0` 1주 매수를 시도했으나 **24회 시도 전부 동일 실패**.
- 거부 메시지:

```
[APBK1497] 파생ETF 미 신청(선택확인서 미 징구) 계좌는 파생ETF 거래가 불가합니다.
```

- 같은 날 **일반 주식(예: 남성 004270)은 정상 체결** — 이 게이트는 파생형 ETP 에만 걸린다.
- 매수 신호 생성·전략·설정(`config/trading.yaml` 전략 인스턴스, `max_positions` 증설 등)은 모두 정상이었다. 즉 애플리케이션 레벨에서는 "왜 안 사지는지" 단서가 없고, KIS 주문 응답 메시지에만 원인이 드러난다.

## 원인

파생형/레버리지 ETP(레버리지·인버스 ETF, ETN 등)는 계좌에 **선행 절차**가 완료돼야 주문이 통과한다:

1. **'파생상품 ETF/ETN 거래 선택확인서' 징구**
2. **레버리지 ETP 사전교육 이수**

이 절차는 한국투자증권 HTS/앱/영업점에서 사람이 신청해야 하며, 신청 전까지는 API 주문이 계속 `[APBK1497]` 로 거부된다. **코드로는 해결 불가.**

## 해결

- 증권사(HTS/앱/영업점)에서 파생 ETF 선택확인서 + 레버리지 ETP 사전교육을 완료한 뒤 재시도.
- 자동매매에 파생/레버리지 종목을 편입하기 **전에** 계좌 권한부터 확인할 것.

## 일반 교훈

1. **주문 거부의 원인이 항상 코드/설정에 있는 것은 아니다** — 신호·전략이 정상인데 주문만 100% 거부되면 **브로커 응답 메시지(에러 코드)를 먼저 본다**. KIS 는 거부 사유를 메시지 코드로 명시한다(`APBK1497` 파생ETF 미신청, `APBK0919` 장운영일자 상이 → [[kis-holiday-detection-bsop-date]], `주문가능금액 초과` → [[kis-cash-d2-settlement-buy-rejection]]).
2. **계좌 권한은 종목 유형별로 다르다** — 일반 주식이 되니 계좌 자체는 정상이라고 단정하면 안 된다. 파생 ETF/ETN·해외·공매도 등은 별도 사전신청 게이트가 있다.
3. **신규 종목 편입 체크리스트에 "계좌 상품 권한"을 추가** — [[ht-trading]] 의 종목 추가 절차(전략 인스턴스·`max_positions`·영숫자 코드·warmup)에 더해, 파생/레버리지면 계좌 권한 확인이 선결.

## 변경 이력

- 2026-07-08: 최초 생성 — 0195S0 레버리지 ETF 무한매수 편입 후 24회 매수 실패의 원인이 계좌 파생ETF 미신청([APBK1497])으로 확정된 사례 (출처: session-logs/20260707-223019-2c24-*)
