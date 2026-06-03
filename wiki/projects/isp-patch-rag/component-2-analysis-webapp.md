---
title: "[설계·컴포넌트2] 이슈 분석 Web App (버전 공통, claude -p)"
domain: "work"
sensitivity: "internal"
tags: ["design", "component-2", "analysis", "webapp", "claude-cli", "retrieval", "common"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "design-discussion: 2026-06-03 Claude Code 세션"
confidence: "medium"
related:
  - "wiki/projects/isp-patch-rag/common-architecture.md"
  - "wiki/projects/isp-patch-rag/component-1-ingestion-version-a.md"
  - "wiki/projects/isp-patch-rag/component-1-ingestion-version-b.md"
---

# 컴포넌트 2 — 이슈 분석 Web App (버전 공통)

수집(컴포넌트 1)으로 쌓인 DB 위에서 동작하는 **분석 컴포넌트**. 새 이슈를 입력받아 **유사 과거 이슈·회귀 후보·재현 가이드**를 advisory로 제시하는 인터랙티브 web app. **버전 A·B가 이 컴포넌트를 동일하게 공유**한다(차이는 수집 컴포넌트뿐). AI 실행은 두 버전 모두 **`claude -p`로 통일**. 공통 계약은 `common-architecture.md` 참조.

## 성격

- **read-only**(DB read), 인터랙티브, 저지연, 사람이 질의→응답.
- 수집 컴포넌트와의 유일한 공유 자원은 **Postgres**(수집=write, 분석=read).
- 출력은 항상 **advisory**(판정 아님), 인용 강제.

## 구성요소

```
web frontend         # 질의 입력 + 결과/인용 표시 (간단 SPA 또는 서버 렌더)
api backend (FastAPI)# 검색 오케스트레이션 엔드포인트
search module        # 공통 §8 하이브리드 검색 (prefilter → vector → rerank)
ai_invoker           # claude -p 래퍼 (extract_symptom, synthesize_answer) — 공통 §6
query_log            # 질의/응답/피드백 기록 (gold set 시드)
```

## 처리 흐름 (공통 §8)

```
사용자 질의(web)
 → ai_invoker.extract_symptom (claude -p) → keywords/suspected_area/hw_gen
 → search.prefilter (SQL) → search.vector_topk (pgvector) → search.rerank
 → ai_invoker.synthesize_answer (claude -p) → advisory 답변 + 인용 change_id
 → 화면 표시(인용 patch/ticket 링크) + query_log 기록
```

## 단계별 (phase)

- **P1 (MVP)**: web UI + 하이브리드 검색 + advisory 답변(인용 강제, 미검색 시 정직 응답).
- **P2 (운영)**: 신규 Jira 티켓 생성 트리거 → 자동 초도 분석 코멘트(봇). "AI advisory" 라벨 명시.
- **P3+**: side-effect 후보·재현 가이드 표시 (`phase-3plus-forward-design.md`).

## 보안

- `claude -p`는 **사내 승인 엔드포인트로만**(외부 egress 차단). 도입 선결조건.
- 인증: 사내 SSO. DB는 **read 권한만** 부여(쓰기 차단).

## 구현 Task 체크리스트

- [ ] FastAPI 백엔드 + 검색 엔드포인트.
- [ ] search module: 공통 §8 (prefilter SQL → pgvector top-k → rerank 가중합, 가중치 설정값 노출).
- [ ] ai_invoker(claude -p): extract_symptom, synthesize_answer + JSON 스키마 검증.
- [ ] synthesize_answer 프롬프트: 인용(change_id) 강제, 추측-사실 구분, advisory 톤.
- [ ] web frontend(질의/결과/링크), 사내 SSO.
- [ ] query_log 적재.
- [ ] (P2) 신규 티켓 → 봇 코멘트.

## 완료 기준 (DoD)

- 샘플 이슈 20건에서 top-k 안에 사람이 "관련 있다" 판단하는 패치 포함률 ≥ 목표(예: 70%).
- 모든 답변에 인용 change_id 포함, 외부 egress 0.
- 버전 A/B 어느 수집본 위에서도 **동일 동작**(분석은 수집 방식에 비의존).

## 변경 이력

- 2026-06-03: 최초 생성 (2-컴포넌트 재정리 — 분석 공통화, claude -p 통일).
- 2026-06-03: 컴포넌트 번호 재정렬(수집=1, 분석=2).
