---
title: "같은 broker appkey 를 두 프로세스가 쓸 때 — 토큰 캐시 공유 + rate limit 분담"
domain: "trading"
sensitivity: "public"
tags: ["decision", "kis", "trading", "auth", "token-cache", "rate-limit", "appkey", "multi-process"]
created: "2026-06-13"
updated: "2026-07-04"
sources:
  - "session-logs/20260613-164818-fc2f-신규-프로젝트를-시작하려고-하는데-지금-내가-이미-한국-투자-증권으로-API-사용해서-거래.md"
  - "session-logs/20260704-093146-5338-현재-프로젝트-분석.md"
confidence: "high"
related:
  - "wiki/projects/ht-dde.md"
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/oauth-refresh-token-rotation-multi-client.md"
  - "wiki/analyses/api-circuit-breaker-trading-pattern.md"
---

# 같은 broker appkey 를 두 프로세스가 공유할 때의 토큰·rate limit 결정

한국투자증권(KIS) 같은 증권사 API 는 보통 **appkey/appsecret → access token** 방식이고, 두 가지 제약이 동시에 걸린다: ① **토큰 발급 자체에 분당 1회 제한**, ② **appkey당 초당 호출 건수 제한**(KIS 실전 ~20건/초). 같은 계정으로 실거래 봇([[ht-trading]])과 모니터링 스캐너([[ht-dde]])를 **동시에** 돌릴 때, 두 프로세스가 각자 토큰을 발급하고 각자 마음껏 호출하면 두 제약을 모두 건드린다.

이 문서는 그때의 두 가지 설계 결정을 정리한다. (OAuth 회전형 refresh token 의 쟁탈은 메커니즘이 다르다 → [[oauth-refresh-token-rotation-multi-client]].)

## 결정 1 — 새 토큰을 발급하지 말고 기존 캐시를 공유

**문제**: 두 프로세스가 각자 토큰을 발급하면, KIS 의 "분당 1회 발급 제한"에 걸려 한쪽 또는 양쪽이 발급 에러를 받는다. (회전형 OAuth 처럼 서로의 토큰을 *무효화*하진 않더라도, 발급 시도 자체가 throttle 된다.)

**결정**: 후순위 프로세스(ht_dde)는 **토큰을 새로 발급하지 않고, 먼저 떠 있는 프로세스(ht_trading)의 토큰 캐시 파일을 그대로 읽어 재사용**한다.

- ht_trading 의 `config/.token_cache.json`(access token + `expires_at` + `base_url`)을 공유.
- 시작 시 `expires_at - now > 0` 이면 발급 없이 그대로 사용. KIS access token 은 만료가 길어(수십 시간) 대부분 재사용 가능.
- 효과: 발급 호출이 한 곳(실거래 봇)으로 단일화 → "분당 1회" 충돌 원천 제거. 인증 모듈도 ht_trading 것을 재사용해 코드 중복 0.

> 핵심: 단일 자격증명에 발급 제한이 있으면 **발급의 단일 소유자(owner)를 두고 나머지는 캐시 소비자(consumer)로 만든다.** 소비자는 절대 발급하지 않는다.

## 결정 2 — 호출량은 두 프로세스 합산으로 본다 (사전 필터로 분담)

**문제**: rate limit 은 토큰이 아니라 **appkey 단위로 합산**된다. ht_dde 단독은 초당 ~16건으로 20 미만이라 안전하지만, ht_dde 가 한 폴링에서 11초간 몰아치는 동안 마침 ht_trading 이 호출하면 **합산이 20/초를 넘어 둘 다 `EGW00201 초당 거래건수 초과`** 가 난다. 모니터링이 실거래에 영향을 주는 건 용납 불가.

**완화 (조합):**

1. **호출량 자체를 줄이는 사전 필터(가장 효과적)** — 종목마다 상세 3건(현재가·체결강도·호가)을 다 조회하지 말고, 유니버스 조회가 *이미 주는* 값(거래대금·등락률·거래량증가율)으로 1차 컷 → 통과한 종목만 상세 조회. 60종목 → 15~25종목으로 호출량 60~70% 감소.
2. **throttle 하향** — 후순위 프로세스의 `min_interval` 을 0.06→0.1초(초당 ~10건)로 낮춰 owner 프로세스에 초당 여유를 남긴다.
3. **폴링 주기 완화** — 30→60초. 모니터링 목적엔 해상도 차이 미미, 부하 절반.

1번이 본질적 해법(필요 없는 호출을 안 함), 2·3 은 안전마진. 일일 누적 한도는 시세 조회엔 사실상 없고 **초당 제한이 핵심**이라, 분담의 단위도 "초당 합산"으로 잡는다.

## 구현 검증과 남은 리스크 (2026-07-04 코드 분석)

ht_dde 심층 분석에서 결정 1의 구현이 의도대로임을 확인:

- consumer(ht_dde)는 `token` property **접근 시마다 캐시 파일을 재로드**해 다른 프로세스(owner)의 토큰 갱신을 즉시 흡수한다. 캐시 스키마(`base_url`/`appkey`/`token`/`expires_at`)와 만료 안전마진 1시간 선반영 방식이 owner 쪽 KISAuth와 완전 호환.
- 캐시 로드 시 `base_url`/`appkey` 일치를 검사해 다른 서버·앱키의 토큰을 오용하지 않는다.

**남은 리스크 — 캐시 파일 쓰기가 비원자적.** owner의 토큰 저장이 `write_text` 직접 덮어쓰기(파일 락 없음)라, owner가 쓰는 도중 consumer가 읽으면 JSONDecodeError → 캐시 무시 → **consumer가 불필요한 신규 발급을 시도**해 "분당 1회 발급 제한"과 충돌할 수 있다. 완화는 저비용: 임시파일에 쓴 뒤 `os.replace` 로 원자적 교체. (미적용 상태 — 주석으로 경합 가능성만 인지.)

또 하나: rate limit 초과가 HTTP 오류가 아니라 **비즈니스 코드(`rt_cd≠0`, `EGW00201`)로 오면** 재시도 백오프를 타지 않고 종목 스킵으로 처리돼, 한도 초과 상태에서 연쇄 누락이 날 수 있다. 429든 EGW00201이든 "한도 초과"는 백오프 대상으로 분류해야 한다.

## 일반화

- 단일 자격증명 + 발급 제한 → **발급 owner 1개 + 캐시 consumer N개**.
- rate limit 이 자격증명 단위로 합산되면, 프로세스별 throttle 합이 한도를 넘지 않게 **예산을 나눠 배정**하고, 무엇보다 **불필요한 호출을 사전 필터로 제거**한다.
- 우선순위(실거래 > 모니터링)를 정해, 충돌 시 양보하는 쪽(throttle 낮추는 쪽)을 명시한다.

## 변경 이력

- 2026-06-13: 최초 생성 (출처: session-log 20260613-164818-fc2f, ht_dde 가 ht_trading 과 KIS appkey 공유 시의 토큰·rate limit 결정).
- 2026-07-04: "구현 검증과 남은 리스크" 절 추가 — token property 접근 시마다 캐시 재로드로 owner 갱신 흡수(스키마 완전 호환 확인), 비원자적 write_text 경합 → 불필요 발급 리스크(완화: temp file + os.replace), rt_cd 기반 한도 초과(EGW00201)가 백오프 없이 스킵되는 문제 (출처: session-log 20260704-093146-5338 코드 분석).
