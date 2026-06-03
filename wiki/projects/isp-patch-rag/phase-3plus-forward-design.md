---
title: "[설계·미래] ISP 패치 RAG — Phase 3+ (side-effect 탐지·패치 가이드)"
domain: "work"
sensitivity: "internal"
tags: ["design", "roadmap", "regression", "side-effect", "blast-radius", "future"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "design-discussion: 2026-06-03 Claude Code 세션"
confidence: "low"
related:
  - "wiki/projects/isp-patch-rag/common-architecture.md"
  - "wiki/projects/isp-patch-knowledge-rag-system.md"
---

# Phase 3+ 미래 설계 (버전 공통, 방향성)

Phase 3·4는 기능 A(검색) 운영 후 착수하는 확장이며, 버전 A/B 메커니즘 차이가 작아 **공통 forward-design**으로 둔다. confidence: low — gold set 검증 전 방향성.

> 컴포넌트 매핑: 후보 추출 로직·추가 데이터는 **컴포넌트 1(수집)**, 결과 표시·질의는 **컴포넌트 2(분석 web app)**.

## Phase 3 — side-effect / 회귀 후보 탐지 (기능 B)

목표: "이 이슈가 최근 머지 패치의 부작용 아닌가?"를 후보로 제시.

### 알고리즘 (휴리스틱으로 좁히고 → AI가 인과 설명)

```
입력: 새 이슈 (증상 + 가능하면 재현 로그)
1) suspected_area 도출(extract_symptom + 로그 함수 스택)
2) blast-radius 후보: 같은 path/symbol을 건드린 patch 중
   merged_at ∈ [증상관측 - W, now] (시간창 W)
   ── symbol_touch / file_change 조인으로 결정적 추출
3) 후보 점수 = α·심볼겹침 + β·시간근접 + γ·증상-요약 임베딩 유사도
4) 상위 후보 diff+ticket → AI: "이 변경이 증상을 유발하는 메커니즘" 서술 (advisory)
```

- **결정적 후보 추출이 핵심**, AI는 설명만. false positive 억제를 위해 advisory + 점수 노출.

### 추가 데이터 요구

- 재현 로그 → 함수/심볼 매핑 파서(가능 범위).
- `git blame`/`git log -L`로 라인 수준 최근 변경 추적.

## Phase 4 — 패치 작성 가이드 (기능 C)

### C-1. 회귀 원복 경고

- 새 패치가 **과거 이슈로 의도적으로 변경된 라인**을 원복하는지 감지.
- 구현: 변경 라인의 `git blame` → 해당 커밋의 change_id → 그 패치의 ticket이 "버그 수정"이면 경고("EXYNOSISP-XXXX에서 의도적 변경").

### C-2. 패치 무력화 / 충돌 경고

- A 패치 이후 B 패치가 A와 **같은 심볼·로직**을 덮어써 A 효과를 무효화·중복하는지 감지.
- 구현: symbol_touch 시간순 + diff 의미 비교(AI) → 충돌 후보 제시.

### C-3. 재현 TC 제안

- 이슈 증상·IP 블록에 매칭되는 과거 재현 TC를 검색해 제안.
- 전제: 티켓/패치에 TC 참조가 기록돼 있어야 함(데이터 요구사항).

## 평가 (필수)

- **gold set**: 과거 "패치 X의 회귀로 판명된 이슈" 사례 수집 → Phase 3 precision/recall 측정.
- 지표: 후보 top-k 안에 실제 원인 패치 포함률(recall@k), 오탐률.

## 변경 이력

- 2026-06-03: 최초 생성 (Phase 3+ 미래 설계).
