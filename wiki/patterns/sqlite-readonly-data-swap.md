---
title: "REST 프록시 대신 SQLite readonly 직접 읽기로 데이터 소스 스왑"
domain: both
sensitivity: public
tags: ["sqlite", "better-sqlite3", "fts5", "readonly", "data-source", "nodejs", "createRequire", "decoupling"]
created: "2026-06-21"
updated: "2026-06-21"
sources:
  - "session-logs/20260621-154117-e509-▎-..-HANDOFF.md-와-..-CLAUDE.md-읽고-Phase-2-UI부터-이어가.md"
confidence: medium
related:
  - "wiki/projects/hermes-dashboard.md"
  - "wiki/patterns/blocked-dependency-productive-workflow.md"
---

# REST 프록시 대신 SQLite readonly 직접 읽기로 데이터 소스 스왑

대상 데이터가 로컬 SQLite 파일에 이미 있을 때, 그 데이터를 노출하는 REST 서버가 미기동/불안정하면 **서버 의존을 버리고 SQLite 를 readonly 로 직접 읽는다.** UI 계약(타입)은 그대로 두고 데이터 소스만 갈아끼우면 외부 서비스 의존 없이 동작한다.

## 핵심

- **readonly 직접 읽기**: `better-sqlite3` 등으로 `{ readonly: true }` 열기. 원본을 건드리지 않아 안전하고, REST 서버 기동을 기다릴 필요가 없다.
- **이미 있는 의존성 재사용**: 네이티브 SQLite 애드온이 이미 트리에 있으면 추가 의존 0. Node ESM 에서 네이티브 애드온은 `createRequire(import.meta.url)` 로 로드(기존 store 로더 패턴과 일치).
- **전문검색은 FTS5 단일 쿼리로**: 메시지/노트 검색을 SSE 스트림을 긁어 클라이언트에서 필터링하는 방식(느리고 freezing 위험) 대신, `state.db` 등에 이미 있는 **FTS5 인덱스를 readonly 단일 쿼리**로 활용한다.
- **UI 계약 유지, 소스만 스왑**: UI 가 기대하는 타입(예: `HermesTask`)으로 매핑해서 돌려준다. 소비자는 데이터 출처가 REST 였는지 SQLite 였는지 모른다.

## 함정

- **시간 단위 불일치**: SQLite 의 `created_at` 이 초 단위면 JS `Date`/ms 기대값과 안 맞는다 → ×1000 변환 필요.
- **스키마 결합**: 직접 읽기는 그 앱의 DB 스키마에 결합된다. 스키마가 바뀌면 깨지므로, 공식 API 가 안정화되면 그쪽으로 회귀할지 판단한다(단일 사용자 도구에선 보통 직접 읽기가 ROI 우위).

## 관련 맥락

[[hermes-dashboard]] 에서 네이티브 칸반/메시지 데이터를 가져올 때, 미기동인 dashboard REST 프록시 대신 프로필의 `kanban.db`/`state.db` 를 readonly 직접 읽기로 전환한 사례. 라이브 의존이 막혔을 때의 일반 작업 전략은 [[blocked-dependency-productive-workflow]].

## 변경 이력

- 2026-06-21: 최초 작성 (출처: session-logs/20260621-154117-e509)
