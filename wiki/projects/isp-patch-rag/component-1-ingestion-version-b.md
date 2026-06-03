---
title: "[설계·컴포넌트1·버전B] 수집 — Hermes + SW/MCP"
domain: "work"
sensitivity: "internal"
tags: ["design", "component-1", "ingestion", "version-b", "hermes-agent", "mcp", "cron"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "design-discussion: 2026-06-03 Claude Code 세션"
  - "web: https://github.com/nousresearch/hermes-agent (2026-06-03 조회)"
confidence: "medium"
related:
  - "wiki/projects/isp-patch-rag/common-architecture.md"
  - "wiki/projects/isp-patch-rag/component-2-analysis-webapp.md"
---

# 컴포넌트 1 (버전 B) — 수집: Hermes + SW/MCP 하이브리드

DB를 쌓는 **백그라운드 컴포넌트**(구현·의존성 상 먼저)를 Hermes로 구동한다. **Hermes로 전부 되지 않으므로** 결정적 영역은 SW(MCP 도구)로 구현해 Hermes에 노출한다. 분석 컴포넌트(컴포넌트 2)는 버전 A와 **동일**(`claude -p`)하므로 본 문서는 **수집만** 다룬다.

## 역할 분담

| 영역 | 담당 | 이유 |
|---|---|---|
| Gerrit/Jira 조회, cron 스케줄, 워크플로 | Hermes (MCP + native git/REST) | 유연·자동화 |
| diff/티켓 요약·증상추출 (공통 §6) | Hermes LLM(사내 엔드포인트) | NL 해석 |
| 구조 추출(파일/함수/register) | **SW (MCP 도구)** | 결정성·정밀도 |
| 임베딩·DB upsert·idempotency | **SW (MCP 도구)** | 일관성 필수 |

> 분석 web app(컴포넌트 2)은 수집 방식과 무관하게 그대로 사용.

## 구성요소

```
patch-kb-mcp/        # SW: MCP 서버 (결정적 도구)
  - extract_symbols(diff) -> symbol_touch[]      (공통 §5)
  - ingest_patch(change_id, diff, meta, ticket, ai_summary) -> upsert  (idempotent)
  - embed_and_store(change_id, chunks) -> ok
hermes/
  - skill: ingest_flow (Gerrit/Jira 조회 → 요약/증상추출 → patch-kb-mcp 호출)
  - cron: 백필/증분 수집 트리거
  - model: 사내 LLM 엔드포인트 (외부 통합 차단)
```

## 처리 흐름

```
Hermes cron/이벤트
 → (MCP/git) Gerrit change·diff, (MCP) Jira description, Change-Id/Jira key 링킹
 → Hermes LLM: summarize_patch/extract_symptom (공통 §6 JSON, structured output 강제)
 → patch-kb-mcp.extract_symbols(diff)     # SW 결정적
 → patch-kb-mcp.ingest_patch(...)          # SW idempotent upsert
 → patch-kb-mcp.embed_and_store(...)       # SW 임베딩
```

## 단계별 (phase)

### P0 — PoC (백필)

- patch-kb-mcp 도구 + Hermes ingest 스킬 구현, 백필 1회.
- 링킹률 측정 + **결정성 검증**(동일 입력 재실행 → 동일 DB 레코드: SW 도구가 보장, Hermes는 해석만).
- 외부 egress 0 검증, Hermes 버전 고정.

### P2 — 운영 (증분)

- Hermes cron 증분 수집(마지막 처리 시점 이후만), idempotency로 재시도 안전.
- 관측: Hermes 실행 로그 + MCP 호출 로그(마스킹). 업그레이드는 스테이징 회귀 후 적용.

## 구현 Task 체크리스트

- [ ] patch-kb-mcp 서버(공통 §3 스키마 기반 도구).
- [ ] extract_symbols(tree-sitter/ctags, 공통 §5).
- [ ] ingest_patch idempotent upsert + 링킹 상태 기록.
- [ ] Hermes self-host(Docker), 사내 LLM 엔드포인트, **외부 차단 검증**.
- [ ] Hermes ingest 스킬 + structured output 강제(공통 §6).
- [ ] 백필/증분 cron, 링킹률·결정성·egress 측정.

## 보안 / 주의

- Hermes 기본 통합(Nous Portal·OpenRouter)은 외부 → **전면 차단**, 사내 LLM만.
- 비결정성은 **해석 단계에 한정**, 데이터 정합성은 SW 도구가 책임.
- 신생 OSS → 버전 고정 + 사내 포크 권장.

## 변경 이력

- 2026-06-03: 최초 생성 (2-컴포넌트 재정리 — 수집 버전 B).
- 2026-06-03: 컴포넌트 번호 재정렬(수집=1, 분석=2), 구현 순서 반영.
