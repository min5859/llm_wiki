---
title: "ht_trading 전량 청산 직후 재매수 — flat 재진입 쿨다운 부재"
domain: "personal"
sensitivity: "public"
tags: ["bug", "ht-trading", "scoring-strategy", "trailing-stop", "cooldown", "guard-gap"]
created: "2026-06-05"
updated: "2026-06-05"
sources:
  - "session-logs/20260604-232009-b35a-•-신세계는-15-10-매도-후-15-20-재매수로-10분-만에-재진입했습니다.-트레일링스.md"
confidence: "high"
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/analyses/dca-trailing-stop-tuning.md"
  - "wiki/bugs/absolute-stop-loss-elif-dead-code.md"
---

# ht_trading 전량 청산 직후 재매수 — flat 재진입 쿨다운 부재

`ScoringStrategy` 가 트레일링스톱·손절로 한 종목을 **전량 청산하면, 같은 종목을 곧바로 다시 매수**하는 구조적 공백. 신세계(004170) 가 15:10 트레일링스톱 매도 → 15:20 재매수로 10분 만에 재진입한 사례에서 발견. 매도 직후 재진입을 막는 쿨다운이 전혀 없어, flat 상태가 되면 다음 사이클 `_try_buy()` 가 즉시 다시 사들였다.

## 핵심 사실

- 트레일링스톱 전량매도는 `_check_sell_rules` **Rule 2** (`scoring_strategy.py:885~898`) 에서 `qty=pos.qty` 로 발생
- 전량 청산 → flat → 다음 사이클 `on_bar` 의 no-position 분기 (`:350`) 에서 `_try_buy()` 가 즉시 재호출 → 같은 종목 재매수
- 매도 **시각**을 기록하는 상태가 없었음 (`_entry_dates` 는 진입일만 보관)
- 기존 분할 가드 `min_split_interval_minutes` (18시간) 는 **분할 추가매수** 간격이고 **첫 매수 (`splits_done == 0`) 는 면제**되므로, flat 재진입 경로에는 가드가 전혀 걸리지 않았다

## 원인 — 가드가 한 경로만 덮고 있었다

매수 경로는 두 갈래인데 throttle 이 한쪽만 보호하고 있었다:

| 경로 | 트리거 | 기존 가드 |
|------|--------|-----------|
| 분할 추가매수 (`splits_done >= 1`) | 보유 중 추가 진입 | `min_split_interval_minutes` 18h ✅ |
| **flat 재진입 (`splits_done == 0`)** | 전량 청산 후 첫 매수 | **없음** ❌ |

분할 throttle 이 첫 매수를 일부러 면제하는 설계라, 전량 청산으로 `splits_done` 이 0 으로 리셋되면 곧장 같은 종목을 되사는 경로가 무방비로 열려 있었다.

## 수정 (commit `70634aa`)

`scoring_strategy.py` 에 종목별 마지막 전량매도 시각 기록 + flat 재진입 쿨다운 검사 추가:

- `reentry_cooldown_minutes` (기본 60분) 파라미터 + `_last_sell_time` 상태 신설
- 매도 5개 지점을 모두 `_record_full_sell()` 헬퍼 경유 — `sell_qty >= pos.qty` 인 **전량 청산만** 시각 기록. 단계익절·데드크로스 같은 **부분 매도는 기록하지 않음** (엣지케이스로 부분 매도 지점도 헬퍼는 통과시키되 전량 여부로 판정)
- `_try_buy` 의 **flat 재진입 시에만** 쿨다운 검사 — 보유 중 분할 추가매수는 영향 없음
- `get_state`/`set_state` 영속화 (ISO 직렬화, 하위 호환). `reentry_cooldown_minutes: 0` 이면 비활성화
- `config/strategies/scoring.yaml` 에 `reentry_cooldown_minutes: 60` 등록

신규 회귀 테스트 6 cases (`tests/strategy/test_reentry_cooldown.py`) + 기존 전략 테스트 114건 통과, 회귀 없음. 적용에는 라이브 launchd 프로세스 재시작 필요 (`launchctl kickstart -k gui/$(id -u)/com.wooki.ht-trading`).

> 부수 발견: `pytest` 가 이 테스트 파일을 `No tests collected` 로 잡지 못해 `python -m unittest` 로 실행. (collection 규칙 불일치 — 별도 조사 대상이나 이번 세션에서는 unittest 로 검증만 진행)

## 일반 교훈

- **throttle/가드가 "어떤 경로를 덮고 어떤 경로를 비워두는지" 를 명시적으로 확인하라.** 분할 추가매수 간격 가드는 의도적으로 첫 매수를 면제했고, 그 면제가 전량 청산 후 재진입이라는 *다른 진입 경로* 에서 그대로 구멍이 됐다. 가드는 "보호하는 조건" 만큼 "면제하는 조건" 이 어디서 또 쓰이는지가 중요하다.
- **상태 전이의 부작용으로 가드가 리셋되는 경로를 의심하라.** 전량 청산이 `splits_done` 을 0 으로 되돌리면서 분할 throttle 까지 함께 풀려버렸다 — 청산이라는 한 동작이 두 개의 가드 의미를 동시에 무력화.
- **"언제 무엇을 기록하는가" 를 행위 단위로 분리하라.** 진입일(`_entry_dates`)만 있고 매도 시각이 없어 쿨다운을 걸 수가 없었다. 부분 매도와 전량 청산은 의미가 다르므로 (`sell_qty >= pos.qty`) 전량 청산만 시각을 남기도록 분기했다.

## 관련 맥락

- [[ht-trading]] — 매도 규칙(트레일링스톱/단계익절/손절)과 분할 매수 인터벌 설계 전반
- [[dca-trailing-stop-tuning]] — 트레일링스톱 튜닝 일반 논의 (활성화·distance 조정 이력)
- [[absolute-stop-loss-elif-dead-code]] — 같은 전략의 매도 룰에서 발견된 또 다른 구조적 공백 (가드가 도달 불가)

## 변경 이력

- 2026-06-05: 최초 작성 — 신세계 10분 재진입 사례에서 발견된 flat 재진입 쿨다운 부재 (출처: session-logs/20260604-232009-b35a-*)
