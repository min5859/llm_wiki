---
title: "pykrx 지수 이름 부수 조회가 가격 수집을 중단시키는 오류"
domain: "trading"
sensitivity: "internal"
tags: ["bug", "pykrx", "krx", "eod", "optional-metadata", "secondary-error"]
created: "2026-10-14"
updated: "2026-10-14"
sources:
  - "session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md"
confidence: "medium"
related:
  - "wiki/bugs/pykrx-krx-login-required.md"
  - "wiki/projects/ht-dde.md"
---

# pykrx 지수 이름 부수 조회가 가격 수집을 중단시키는 오류

2026-10-12 ht_dde RS 수집 실패를 10월 14일 분석한 결과, 필요한 가격 조회 뒤 불필요한 지수 이름 조회가 전체 작업을 중단시키는 경로가 오프라인 재현됐다. 도메인 핵심 수집 장애이므로 첫 발견에도 수집한다. 수정은 아직 적용하지 않았다.

## 핵심 내용

1. 지수 일봉 조회 뒤 pykrx가 지수 이름 목록을 추가 요청한다.
2. 이름 조회의 JSON 해석 실패를 빈 목록으로 처리한다.
3. 빈 목록에서 `지수명`을 찾다가 오류가 발생해 당일 배치가 중단된다.
4. 오류 출력 코드의 별도 TypeError는 원인을 가리는 2차 오류다.

가격 데이터에 지수명이 필요 없다면 `name_display=False`로 부수 조회를 생략하는 것이 최소 수정안이다. 세션에는 해당 경로를 생략해도 동일 가격 데이터가 유지됨을 네트워크 없이 재현했다고 기록됐다. 이번 수집에서 라이브러리 코드나 테스트를 직접 재실행하지 않았다.

## 판단과 한계

10월 10일의 CD010 인증 장애와 복구는 [[pykrx-krx-login-required]]에 별도로 기록돼 있다. 동일한 JSON·빈 목록·KeyError 증상만으로 같은 인증 원인이라고 단정하지 않는다. 당시 KRX가 왜 비정상 응답을 보냈는지는 로그 부족으로 미확정이다.

일반 패턴은 필수 가격과 선택적 표시 메타데이터의 실패 경계를 분리하고, 로그의 마지막 예외보다 최초 실패 요청을 확인하는 것이다. 적용 후 정상 수집·재발 여부 확인은 남은 작업이며, RS 전략의 실거래 편입 검증도 아직 완료되지 않았다([[ht-dde]]).

## 변경 이력

- 2026-10-14: 최초 생성 — 부수 이름 조회 실패·2차 출력 오류·미적용 최소 수정안 기록 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)
