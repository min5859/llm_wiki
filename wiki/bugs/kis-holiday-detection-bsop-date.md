---
title: "KIS 공휴일 휴장 판정 실패 — bsop_date 비교 대신 휴장일조회 API"
domain: "trading"
sensitivity: public
tags: ["bug", "ht-trading", "kis", "trading", "holiday", "market-hours", "daemon"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "session-logs/20260603-135643-1e3b-오늘은-공휴일이라-휴장인데-동작을-하고있습니다.-공휴일에는-동작하지-않도록-해-줘.md"
confidence: high
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/kis-balance-api-fields.md"
  - "wiki/bugs/kis-cash-d2-settlement-buy-rejection.md"
---

# KIS 공휴일 휴장 판정 실패 — bsop_date 비교 대신 휴장일조회 API

ht_trading 라이브 엔진이 **공휴일(휴장)에도 매수를 반복 시도**해 KIS 가 `APBK0919 장운영일자가 주문일과 상이합니다` 로 전부 거부한 사건. 공휴일 판정 로직 자체가 신뢰 불가능한 신호를 쓰고 있었고, 거기에 장시간 실행 데몬이 옛 코드를 메모리에 들고 있어 더 오래 지속됐다.

## 증상

```
2026-06-03 13:40:16 [ERROR] api.client: API 에러: rt_cd=7 msg_cd=APBK0919 msg=장운영일자가 주문일과 상이합니다
2026-06-03 13:40:16 [ERROR] broker.kis_broker: 주문 실패: BUY 161580
오늘 주문: 26/100  (10분마다 반복 — 체결은 안 되지만 계속 시도)
```

- 공휴일인데 라이브 엔진이 사이클마다 매수 주문 시도 → KIS 가 전량 거부
- 로그에 "공휴일 감지" 가 **한 번도 안 찍힘** → 휴장 판정이 작동 안 함

## 원인 — `bsop_date` 는 공휴일에도 당일 날짜를 반환

기존 `market_hours.py` 의 휴장 판정은 **삼성전자 현재가 조회 응답의 `bsop_date`(영업일자)와 오늘 날짜를 비교** 하는 방식이었다.

```
가정: 공휴일이면 API 가 전일(영업일) 날짜를 반환 → bsop_date != today → 휴장
실제: 공휴일에도 이 API 가 당일 날짜를 그대로 반환 → bsop_date == today → "개장"으로 오판
```

→ 신호 자체가 휴장을 구별하지 못하는 신호였다. (현재가 API 의 `bsop_date` 는 "마지막 거래 기준일" 의미가 모호 — 휴장일 판정용으로 부적합)

## 해결 — KIS 국내휴장일조회 API (`CTCA0903R`)

정확한 개장일 정보를 주는 전용 API 로 교체. 실제 호출로 먼저 검증한 뒤 적용.

```
GET /uapi/domestic-stock/v1/quotations/chk-holiday   tr_id=CTCA0903R
params: BASS_DT=20260603, CTX_AREA_NK="", CTX_AREA_FK=""

output[0] 필드: bass_dt, wday_dvsn_cd, bzdy_yn, tr_day_yn, opnd_yn, sttl_day_yn
검증 결과 (2026-06-03): opnd_yn=N  ← 개장일 아님(휴장), bzdy_yn=N(영업일 아님)
```

판정 기준은 **`opnd_yn`(개장일 여부)**. `tr_day_yn`(거래일여부)은 휴장에도 Y 가 나올 수 있어 부적합 — `opnd_yn` 이 정답.

**수정 2곳**:
- `api/domestic.py`: `is_open_day(date)` 추가 — `CTCA0903R` 호출, `opnd_yn` 으로 개장 여부 반환
- `utils/market_hours.py`: `is_market_open_live()` 가 `bsop_date` 비교 대신 `is_open_day()` 사용

검증: 테스트 218건 통과, 통합 실행 시 `휴장일 감지: 20260603 개장일 아님(opnd_yn=N) → 휴장` 정상 출력.

## 부가 교훈 — 장시간 실행 데몬은 코드 수정 후 재시작 필수

근본 원인은 판정 로직이지만, **6시간 37분째 떠 있던 launchd 라이브 프로세스(PID 67660)가 옛 코드(`bsop_date` 방식)를 메모리에 들고 돌고 있어** 코드를 고쳐도 곧바로 반영되지 않았다.

```bash
launchctl kickstart -k gui/$(id -u)/com.wooki.ht-trading   # KeepAlive 로 자동 재기동
# 재시작 후: "휴장일 감지 → 휴장", "장외 시간. 다음 개장까지 1140분 대기" — 주문 시도 중단
```

> 라이브 데몬은 파일을 고쳐도 **재시작 전까지 옛 로직으로 동작**한다. 핫리로드가 없으면 수정·검증 후 반드시 재기동해 새 코드 적용을 확인할 것. (실거래 데몬 재시작은 자동 승인에서 막히는 합리적 차단 — 사용자 결정 필요)

## 패턴

- **외부 API 응답 필드를 휴장/상태 판정에 쓰기 전, 그 필드가 정말 그 의미를 구별하는지 실제 호출로 검증.** "공휴일이면 전일 날짜가 오겠지" 같은 추정은 깨진다.
- 전용 API 가 있으면(휴장일조회 `CTCA0903R`) 부수 신호(현재가의 `bsop_date`) 대신 그것을 쓴다.
- 검증 못 한 외부 TR ID/엔드포인트는 **실 호출로 먼저 확인** 후 코드에 반영.

## 변경 이력

- 2026-06-03: 최초 작성 (세션 로그 20260603-135643-1e3b, commit `2b17aba`)
