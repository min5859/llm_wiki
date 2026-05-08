---
title: "new Date().toISOString() 가 KST 새벽~오전에 어제 날짜로 떨어지는 버그"
domain: both
sensitivity: public
tags: ["bug", "timezone", "utc", "kst", "iso8601", "javascript", "cron", "date-rollover"]
created: 2026-05-08
updated: 2026-05-08
sources:
  - "session-logs/20260507-225855-4c7c-현재-프로그램을-분석해-주세요.md"
confidence: high
related:
  - "wiki/projects/dev-blog.md"
  - "wiki/patterns/vercel-cron-best-practices.md"
---

# `new Date().toISOString()` 가 KST 새벽~오전에 어제 날짜로 떨어지는 버그

JavaScript / Node 환경에서 *오늘 날짜* 를 계산할 때 `toISOString().slice(0, 10)` 이 자주 쓰이지만, 이는 UTC 기준이라 한국에서 운영 시 새벽~오전 시간대에 *어제 날짜* 가 반환된다. cron / 스케줄러로 도는 코드에서 게시본 / 로그 파일명 / DB key 가 어제 것으로 덮어씌워지는 silent overwrite 사고로 직결.

## 증상

KST 08:15 에 cron 이 돌면 로그/게시본 파일명이 `2026-05-07-linux.log` 로 찍힘 (사용자 인지: `2026-05-08`). 매일 07:00 KST cron 이 어제 (`2026-05-07`) 게시본을 silently overwrite 하는 데이터 손실 시나리오 (dev-blog 에서 발견).

## 원인

```js
// BAD: UTC 기준
const runDate = new Date().toISOString().slice(0, 10);
// 5/8 KST 08:15  =  5/7 UTC 23:15  →  "2026-05-07"
```

`toISOString()` 은 항상 UTC 로 직렬화되므로 **KST 00:00 ~ 08:59** (UTC 15:00 ~ 23:59 전날) 구간에서 어제 날짜가 반환된다. 매일 KST 07:00 cron 같은 한국 표준 운영 시간이 정확히 영향권.

## 수정

```js
// GOOD: en-CA 로케일은 ISO 형식 (YYYY-MM-DD) 을 그대로 반환
function todayKst() {
  return new Intl.DateTimeFormat('en-CA', { timeZone: 'Asia/Seoul' }).format(new Date());
}
const runDate = process.env.NEWSLETTER_DATE ?? todayKst();
```

- `en-CA` 로케일을 쓰는 이유: ISO 8601 의 `YYYY-MM-DD` 가 *기본 출력 형식* — 별도 파싱·재조립 필요 없음
- 환경변수 override (`NEWSLETTER_DATE`) 는 그대로 유지 — one-off backfill 용

## 체크리스트 (재발 방지)

- cron / 스케줄러로 도는 모든 코드에서 `new Date().toISOString().slice(0, 10)` 패턴 검색 → 타임존 명시한 헬퍼로 교체
- 영향 범위가 *파일명 · DB key · 게시 ID* 처럼 멱등성·덮어쓰기에 직결되면 우선순위 최상
- 단위 테스트는 `Asia/Seoul` 의 00:00 ~ 08:59 시뮬레이션 필요 (Date mock + Intl.DateTimeFormat 격리)

## 일반화

같은 패턴이 재현되는 환경:

| 환경 | 기본 타임존 | 위험도 |
|---|---|---|
| **Vercel cron** | UTC | 한국 사용자 운영 시 동일 |
| **GitHub Actions** | UTC | 동일 |
| **AWS Lambda** | UTC | 동일 |
| **Cloudflare Workers** | UTC | 동일 |
| **macOS / Linux 로컬 cron** | 시스템 TZ (보통 KST 로 설정됨) | 코드의 `toISOString()` 자체가 UTC 라 영향 |

→ 즉 *코드의 `toISOString()` 은 시스템 TZ 와 독립적으로 UTC*. 한국 사용자가 운영하는 거의 모든 자동화에서 KST 자정 ~ 오전 9시 사이 실행분이 영향. JST 사용자도 동일.

## 함정

- `new Date().toLocaleDateString('ko-KR')` 은 `YYYY. MM. DD.` 같은 비-ISO 형식 → 별도 파싱 필요. `en-CA` 가 더 단순.
- `new Date().toLocaleDateString('sv-SE')` 도 ISO 형식 반환 (스웨덴 표기) — 동등하게 사용 가능.
- 라이브러리 (date-fns / dayjs / luxon) 의 `format(new Date(), 'yyyy-MM-dd')` 는 시스템 TZ 기준 — 시스템이 UTC 인 클라우드에서는 같은 함정. **타임존을 명시적으로 지정** 해야 안전.
- `new Date('2026-05-08')` 는 *UTC 자정* 으로 파싱됨 (KST 09:00). 날짜 비교 시 다른 TZ 에서 하루 차이 날 수 있음.

## 변경 이력

- 2026-05-08: 최초 작성. dev-blog cron 분석 중 발견 (출처: session-logs/20260507-225855-4c7c-*)
