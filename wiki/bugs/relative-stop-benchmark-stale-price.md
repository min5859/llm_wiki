---
title: "상대손절의 벤치마크 stale price — 종목은 실시간가, 지수는 일봉 캐시 종가"
domain: "trading"
sensitivity: "public"
tags: ["bug", "trading", "relative-stop", "benchmark", "stale-price", "daily-cache", "realtime"]
created: "2026-07-07"
updated: "2026-07-07"
sources:
  - "session-logs/20260706-214100-fe2f-무한매수-알고리즘에-종목을-하나더-추가하고-싶은데-KOSPI-0195S0-TIGER-SK하.md"
confidence: "medium"
related:
  - "wiki/bugs/absolute-stop-loss-elif-dead-code.md"
  - "wiki/projects/ht-trading.md"
---

# 상대손절의 벤치마크 stale price — 비교 대상 두 값은 동일 시점·동일 소스여야 한다

상대(벤치마크 대비) 손절 규칙에서 **종목가는 실시간가, 벤치마크 지수는 일봉 캐시 종가**를 쓰면, 시장 전체가 동반 급락하는 날 지수 하락이 반영되지 않아 상대손절이 오발동한다. ht_trading 에서 실제 발생한 사례 (fe2f 세션의 todo/커밋 컨텍스트로 재부상한 기록).

## 사례 (2026-06-23, 삼성전자)

- 코스피200 추종 069500 이 장중 **-10%** 인데 시스템은 **-2%** 로 인식 — `bench_bars[-1].close` (일봉 캐시 종가) 가 하루 종일 148,355 로 고정.
- 종목가는 `pos.current_price` (실시간) 라 급락이 즉시 반영 → 종목-지수 차이가 -10.1% 로 계산되어 상대손절 오발동. 실제로는 시장과 같이 빠진 것뿐인데 "지수 대비 크게 뒤처짐" 으로 오판.

## 원인과 수정

- **원인**: 비교식의 두 입력이 소스 비대칭 — 종목 = 실시간가, 벤치마크 = 장중 미갱신 일봉 캐시.
- **수정**: `Broker.get_current_price` + `StrategyContext.benchmark_price` 를 매 사이클 주입해 종목가·벤치마크가를 **동일한 실시간 소스로 통일** (0 이면 기존 캐시로 폴백).

## 일반 교훈

1. **상대 비교 규칙(A vs B)의 두 입력은 반드시 동일 시점·동일 소스** — 한쪽만 신선하면 차이값 전체가 허구가 된다.
2. ht_trading 에서 "일봉 캐시 vs 실시간가 비대칭"은 반복 함정이다 — split throttle 드로다운 0% (c5dc818), TrailingSell 하락률 0% (f376ba8) 에 이어 세 번째 발현. 새 규칙이 가격을 비교한다면 두 피연산자의 소스부터 점검할 것.
3. 벤치마크 의존 규칙의 dead code 화 ([[absolute-stop-loss-elif-dead-code]]) 와 짝을 이루는 함정 — 벤치마크 데이터는 "있냐/없냐" 뿐 아니라 "신선하냐" 도 검증해야 한다.

## 변경 이력

- 2026-07-07: 최초 생성 — fe2f 세션 컨텍스트(todo.md·과거 커밋)에서 재부상한 6/23 사례를 승격 (출처: session-logs/20260706-214100-fe2f-*)
