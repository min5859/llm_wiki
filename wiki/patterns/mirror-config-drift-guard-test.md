---
title: "미러 설정 드리프트 가드 테스트 — 원본을 읽어 자동 대조"
domain: "trading"
sensitivity: "public"
tags: ["pattern", "testing", "config", "drift", "mirror", "paper-trading", "allowlist"]
created: "2026-07-05"
updated: "2026-07-05"
sources:
  - "session-logs/20260704-215721-8c43-현재-프로젝트는-최적의-주식-자동매매-알고리즘을-찾는-것이-목표임-이-목표에-도달하게-위해.md"
confidence: high
related:
  - "wiki/projects/ht-dde.md"
  - "wiki/projects/ht-trading.md"
  - "wiki/bugs/absolute-stop-loss-elif-dead-code.md"
---

# 미러 설정 드리프트 가드 테스트 — 원본을 읽어 자동 대조

두 코드베이스가 미러링 관계일 때(예: ht_dde 페이퍼가 ht_trading 실거래의 청산 파라미터를 복제), 원본 설정이 바뀌면 미러가 **조용히 낡아간다**. "⚠️ 원본 변경 시 수동 동기화 필요" 같은 주석 경고는 지켜지지 않는다 — 이를 **테스트로 강제화**하는 패턴 (2026-07-04, ht_dde `tests/test_mirror_drift.py`).

## 패턴

1. 테스트가 **원본 설정 파일을 읽기 전용으로 직접 읽는다** (ht_dde 테스트가 ht_trading 의 `scoring.yaml` 을 read). 미러 값과 자동 대조, 불일치 시 테스트 실패 → 드리프트가 CI/로컬 테스트에서 즉시 드러난다.
2. **의도적 단순화는 명시적 allowlist 로 등록**해 오탐을 방지한다. 예: 페이퍼는 일봉 종가 기준이라 장중 저점·분할 타이밍을 미반영 — 이런 알려진 차이는 allowlist 에 사유와 함께 기록하고 대조에서 제외.

## 왜 주석이 아니라 테스트인가

- 미러 괴리는 증상이 느리고 간접적이다 — ht_trading 이 손절 체계를 바꿨는데(Rule1a 백스톱, [[absolute-stop-loss-elif-dead-code]]) 페이퍼가 옛 규칙으로 돌면, 페이퍼 실험 결과 전체가 "이제는 존재하지 않는 전략"의 평가가 된다. 결과가 틀렸다는 신호 자체가 없다.
- 문서·주석은 코드와 드리프트한다 — 같은 프로젝트에서 "백로그 문서는 코드와 드리프트한다"는 교훈의 테스트 기반 해법.

## 적용 범위

shadow/paper 환경, 미러 서비스, 설정을 복제하는 모든 다중 시스템. 원본 저장소가 로컬에 존재해 직접 읽을 수 있는 경우가 최적이고, 아니면 원본 스냅샷을 fixture 로 주기 갱신하는 차선책.

## 변경 이력

- 2026-07-05: 최초 생성 — ht_dde 페이퍼 ↔ ht_trading 실거래 미러링의 드리프트 가드에서 일반화 (출처: session-logs/20260704-215721-8c43-*)
