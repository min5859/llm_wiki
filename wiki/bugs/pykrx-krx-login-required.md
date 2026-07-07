---
title: "pykrx — KRX 로그인 의무화로 비로그인 조회가 JSONDecodeError·빈 DataFrame 으로 실패"
domain: "trading"
sensitivity: "public"
tags: ["bug", "trading", "pykrx", "krx", "eod", "login", "jsondecodeerror"]
created: "2026-07-07"
updated: "2026-07-07"
sources:
  - "session-logs/20260706-220340-ac9d-아래와-같은-알고리즘을-셋업하고-싶은데-어떻게-하면-될까---최근-일주일간-지수대비-덜떨어.md"
confidence: "high"
related:
  - "wiki/projects/ht-dde.md"
---

# pykrx — KRX 로그인 의무화 (비로그인 조회 차단)

2025년부터 KRX 정보데이터시스템(data.krx.co.kr)이 **비로그인 조회를 차단**했다. 계정 없이 pykrx 를 호출하면 명시적 인증 에러가 아니라 **`JSONDecodeError: Expecting value: line 1 column 1 (char 0)`** 또는 **빈 DataFrame** (시가/고가/저가/종가 컬럼 자체가 없음) 으로 실패해 원인을 알아채기 어렵다.

## 증상

```
JSONDecodeError: Expecting value: line 1 column 1 (char 0)
# 또는 빈 DataFrame — KeyError: '시가' 등 후속 에러로 발현
```

## 해결

1. data.krx.co.kr 무료 회원 가입.
2. pykrx 1.2.8+ 는 `KRX_ID` / `KRX_PW` 환경변수로 **자동 로그인** (세션 1시간 자동 갱신) 을 지원.
3. 자격증명은 git 미추적 파일에 보관 — ht_dde 는 `config/krx.yaml` (+ 커밋용 `.example` 템플릿) 구조 사용.

## 일반 교훈

- 무료 공개 데이터 소스의 **접근 정책 변경은 라이브러리 에러 메시지에 드러나지 않는다** — 파싱 에러·빈 응답이 나오면 인증/차단 정책 변경부터 의심할 것 (네이버 금융 Referer 의무화 [[naver-finance-news-referer-required]] 와 같은 계열).

## 변경 이력

- 2026-07-07: 최초 생성 — ht_dde RS EOD 스캐너의 pykrx 도입 중 발견 (출처: session-logs/20260706-220340-ac9d-*)
