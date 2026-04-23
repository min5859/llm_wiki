---
title: "ht_trading — 프로젝트 설계 상세"
domain: "personal"
tags: ["project", "trading", "scoring", "algorithm", "config"]
created: "2026-04-23"
updated: "2026-04-23"
sources:
  - "session-logs/20260422-230939-22f1-스코어링-점수를-65-점에서-60-점으로-조정했는지-확인해-주세요.md"
  - "session-logs/20260423-120308-f269-오늘-거래중에서-삼성전자-매수-시그널이-발생한뒤-3분할-매수중-1회만-매수하고-나머지-매수.md"
confidence: "high"
related: []
---

# ht_trading — 알고리즘 트레이딩 프로젝트

Python 기반 개인 알고리즘 트레이딩 시스템. `ScoringStrategy`를 중심으로 매수 시그널을 점수화해 종목을 선별한다.

## 스코어링 시스템 구조

### 이중 점수 스케일

| 컨텍스트 | 스케일 | 파라미터명 | 현재 값 |
|---------|--------|-----------|---------|
| 백테스트 | 40점 만점 | `buy_min_score` | 25 |
| 라이브 screener | 100점 만점 | `buy_min_score_full` | 60 |

- 백테스트 전용 스케일(40점)과 라이브 screener 스케일(100점)이 별도로 운영된다.
- 라이브 기준 60점 = "어느 정도 검증된 시그널만 통과"

### 설정 파일 동기화 규칙

라이브 screener 임계값은 **두 파일에 동시에 설정**되어 있어 반드시 함께 수정해야 한다.

```
config/strategies/scoring.yaml   → buy_min_score_full: 60
config/trading.yaml              → strategies.screener.min_score: 60
```

> 주의: 한 파일만 수정하면 설정 불일치가 발생한다. 변경 시 두 파일을 항상 같이 수정할 것.

### 기타 screener 파라미터 (`config/trading.yaml`)

```yaml
screener:
  min_score: 60          # 100점 만점 기준 매수 커트라인
  max_per_source: 10     # 거래량/상승률/거래대금 각 소스에서 최대 종목 수
  cache_ttl: 3600        # 캐시 TTL (1시간)
```

## 핵심 클래스

- `ht_trading.strategy.builtin.scoring_strategy.ScoringStrategy` — 점수 기반 매수 전략

## 분할 매수 Throttle (G4-1)

2차·3차 분할 매수를 제어하는 가드. 조건 두 가지를 **AND**로 충족해야 추가 매수 허용:
1. **시간 조건** — 직전 분할 이후 충분한 시간 경과
2. **드로다운 조건** — 직전 분할 체결가 대비 현재가 하락 ≥ `min_split_drawdown_pct` (기본 1.5%)

관련 파라미터:

```yaml
# config/strategies/scoring.yaml
enable_split_throttle: true
min_split_drawdown_pct: 0.015  # 1.5%
```

### 버그 수정 (2026-04-23, commit c5dc818)

**증상**: 1차 매수 후 하락 중인데도 2차 분할 매수가 진행되지 않음. 로그에 "split throttle 드로다운 부족: 0.00% < 1.50%" 반복.

**원인**: `_last_split_price`에 `last_bar.close`(일봉 캐시 종가)를 저장했고, 드로다운 비교도 `bar.close`(같은 일봉 캐시)로 수행 → 일봉 캐시는 장중 변하지 않아 드로다운 항상 0%.

**수정 내용**:
| 수정 위치 | 변경 전 | 변경 후 |
|----------|---------|---------|
| `_can_add_split` | `bar.close` 기준 비교 | `pos.current_price`(브로커 실시간 평가가) 기준 비교 |
| `_record_split_event` (라이브 경로) | `last_bar.close` 저장 | `pb.limit_price`(지정가) 저장 |

## 설계 변경 이력

| 날짜 | 변경 내용 | 이유 |
|------|---------|------|
| 2026-04-22 | `buy_min_score_full` 65 → 60 | 매수 기회 확대 (임계값 완화) |
| 2026-04-23 | split throttle 드로다운 버그 수정 (c5dc818) | 일봉 캐시 종가 대신 실시간 평가가 사용 |

## 투자 파라미터

```yaml
buy_invest_pct: 0.20    # 1종목 최대 투자 비중 (20%)
buy_split_count: 3      # 분할 매수 횟수 (3분할)
lookback_period: 60     # 최소 봉 수 / MA·신고가 기간
```

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260422-230939-22f1 에서 추출)
- 2026-04-23: split throttle G4-1 섹션 추가, 드로다운 버그 수정 기록 (세션 로그 20260423-120308-f269)
