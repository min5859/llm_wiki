---
title: "[설계·컴포넌트1·버전A] 수집 — SW cron + claude -p"
domain: "work"
sensitivity: "internal"
tags: ["design", "component-1", "ingestion", "version-a", "claude-cli", "cron", "backfill"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "design-discussion: 2026-06-03 Claude Code 세션"
confidence: "medium"
related:
  - "wiki/projects/isp-patch-rag/common-architecture.md"
  - "wiki/projects/isp-patch-rag/component-2-analysis-webapp.md"
---

# 컴포넌트 1 (버전 A) — 수집: SW cron job + `claude -p`

DB를 쌓는 **백그라운드 컴포넌트**이며 **구현·의존성 상 먼저** 만든다(데이터가 있어야 분석 가능). 자체 Python 서비스로 cron/이벤트 구동, 요약·증상추출 AI는 **`claude -p`**. 분석 컴포넌트(컴포넌트 2)는 버전 공통이므로 본 문서는 **수집만** 다룬다. 공통 계약은 `common-architecture.md` 참조.

## 성격

- **무인 배치/백그라운드**, 사람 개입 없음.
- **write 중심**(DB write), `change_id` 기준 **idempotent**.
- 공유 자원: Postgres(분석 컴포넌트가 read).

## 구성요소

```
event_listener.py    # Gerrit stream-events(SSH) 또는 webhook 수신
worker.py            # 큐 소비 → ingest 파이프라인
queue (Redis/RQ)     # 이벤트 버퍼·재시도
ingest_pipeline.py   # 조회 → 링킹 → 구조추출 → AI요약 → 임베딩 → upsert
ai_invoker.py        # claude -p 래퍼 (summarize_patch, 공통 §6) — 사내 승인 엔드포인트
structural_extract.py# tree-sitter/ctags (공통 §5, 결정적)
embed.py / db.py     # bge-m3 임베딩 / idempotent upsert
scheduler            # 폴링 fallback(이벤트 유실 보정), 재인덱싱 잡, metrics
```

## 단계별 (phase)

### P0 — PoC (백필)

- 과거 N개월 merged change 백필, **링킹 성공률**(Change-Id/Jira key 포함률) 측정.
- `claude -p` invoker가 **사내 승인 엔드포인트로만** 동작 검증(미확정 시 더미 데이터).
- DoD: N개월 백필 무오류 재실행(idempotent), 링킹률 산출, egress 0 확인.

### P2 — 운영 (실시간)

- Gerrit `change-merged` 이벤트 → 큐 → worker 자동 적재.
- 폴링 fallback으로 유실 보정, `change_id` 중복 방지.
- 재인덱싱 잡(임베딩 모델 버전 변경, 공통 §7).
- DoD: 머지 후 N분 내 자동 적재, 7일 무중단, 유실 0.

## 구현 Task 체크리스트

- [ ] Gerrit 수집(REST/SSH, 페이지네이션) + Change-Id/Jira key 파싱·링킹률 집계.
- [ ] Jira REST description/labels 취득(승인 범위).
- [ ] structural_extract(공통 §5) → symbol_touch.
- [ ] ai_invoker(claude -p) summarize_patch + JSON 검증·재시도.
- [ ] bge-m3 임베딩 → chunk.
- [ ] idempotent upsert(공통 §3).
- [ ] event_listener + queue + worker(P2), 폴링 fallback, 재인덱싱 잡.
- [ ] metrics(링킹률, 처리량, AI 검증 실패율, egress 검증).

## 보안

- `claude -p` 외부 송신 주의 → 사내 승인 엔드포인트만. claude 동시성·rate 제어.
- 시크릿은 볼트/환경변수, 로그 마스킹.

## 변경 이력

- 2026-06-03: 최초 생성 (2-컴포넌트 재정리 — 수집 버전 A).
- 2026-06-03: 컴포넌트 번호 재정렬(수집=1, 분석=2), 구현 순서 반영.
