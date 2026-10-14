---
title: "pykrx — KRX 로그인 의무화로 비로그인 조회가 JSONDecodeError·빈 DataFrame 으로 실패"
domain: "trading"
sensitivity: "public"
tags: ["bug", "trading", "pykrx", "krx", "eod", "login", "jsondecodeerror"]
created: "2026-07-07"
updated: "2026-10-14"
sources:
  - "session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md"
  - "session-logs/20260706-220340-ac9d-아래와-같은-알고리즘을-셋업하고-싶은데-어떻게-하면-될까---최근-일주일간-지수대비-덜떨어.md"
confidence: "high"
related:
  - "wiki/bugs/pykrx-index-name-lookup-failure.md"
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

## 인증정보가 있어도 계정 상태로 실패할 수 있다

2026년 10월 ht_dde RS 수집은 pykrx 1.2.8과 인증정보가 이미 있는데도 `KeyError: '지수명'`으로 실패했다. 로그인 원응답은 `CD010`으로 비밀번호 변경을 요구했다. 따라서 라이브러리 업그레이드나 선택자 수정만으로 해결되지 않았다.

사용자가 KRX 웹에서 비밀번호 변경을 완료하고 로컬 설정을 갱신한 뒤 정상 코드 `CD001`과 지수 26개 일봉 수집을 확인했다. 후속 안전장치는 오래된 추천 재사용 차단, 휴장일 관측 제외, 배치 중단 기간 매도대금의 한 번만 반영이었다. 상세 복구 경계는 [[ht-dde]]에 기록돼 있다.

파싱 오류는 인증 실패의 후속 증상일 수 있다. 자격증명 존재 여부뿐 아니라 로그인 결과와 계정 조치 요구를 확인하고, 비밀번호·쿠키 자체는 로그나 wiki에 남기지 않는다. 이 사례의 코드 의미와 복구 결과는 지정 세션에 기록된 사실이며, 현재 외부 정책을 재확인한 것은 아니다.

## 동일한 파싱 증상이 인증 실패를 뜻하지는 않는다

10월 10일 CD010→CD001 복구 뒤에도 10월 12일 RS 수집이 실패했다. 10월 14일 후속 분석은 부수적인 지수 이름 목록 조회 실패를 오프라인 재현했다. 당시 비정상 응답 이유는 미확정이며 비밀번호 오류로 단정할 근거가 없다. 이름 조회 생략의 후속 수정·반영 결과와 정기 배치 검증 경계는 [[pykrx-index-name-lookup-failure]]를 참조한다. 인증 결과와 실제 실패한 요청을 각각 대조해야 한다.

## 변경 이력

- 2026-10-14: 이름 조회 오류의 후속 수정 완료 링크로 미적용 설명 갱신 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-14: 인증 복구 이후 이름 조회 실패를 별개 원인으로 구분하고 후속 버그 상호 링크 추가 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-12: 로그인 의무화의 후속 계정 상태 오류 CD010 및 복구 결과 추가 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-07-07: 최초 생성 — ht_dde RS EOD 스캐너의 pykrx 도입 중 발견 (출처: session-logs/20260706-220340-ac9d-*)
