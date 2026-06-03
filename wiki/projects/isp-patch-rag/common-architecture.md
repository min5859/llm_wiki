---
title: "[설계·공통] ISP 패치 RAG — 아키텍처·데이터모델·검색·AI계약"
domain: "work"
sensitivity: "internal"
tags: ["design", "architecture", "data-model", "pgvector", "retrieval", "ai-contract", "common"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "design-discussion: 2026-06-03 Claude Code 세션"
confidence: "medium"
related:
  - "wiki/projects/isp-patch-knowledge-rag-system.md"
  - "wiki/projects/isp-patch-rag/00-proposal.md"
---

# 공통 설계 — 2-컴포넌트 구조와 공유 불변 계약

시스템은 **2개 컴포넌트**로 분리된다. 데이터 모델·DB 스키마·구조 추출·검색 알고리즘·AI 입출력 계약은 컴포넌트·버전과 무관하게 **동일**하며, 이 문서가 그 공통 계약을 정의한다.

> 코딩 에이전트(Claude Code/Codex CLI) 지시: 이 문서의 스키마·계약을 단일 출처(source of truth)로 삼아 구현하라.

## 0. 컴포넌트 구조

```
[컴포넌트 1] 수집 (DB 빌더) ─────write──┐
   (백그라운드/배치, 버전별 분기)         │
   ├─ 버전 A: SW cron job + claude -p (요약)
   └─ 버전 B: Hermes agent + SW/MCP (요약은 사내 LLM)
                                         ├── Postgres + pgvector (공유 DB)
[컴포넌트 2] 이슈 분석 Web App ──read────┘
   (버전 공통, claude -p, 인터랙티브)
```

- **구현 순서**: 컴포넌트 1(수집) 먼저 → 데이터가 쌓여야 컴포넌트 2(분석) 동작.
- **공유 자원은 Postgres 하나뿐**. 컴포넌트 1(수집)=write, 컴포넌트 2(분석)=read.
- **버전 차이는 컴포넌트 1(수집)에만** 존재. 컴포넌트 2(분석)은 A/B 동일, AI는 `claude -p`로 통일.
- 문서 매핑: 컴포넌트1 = `component-1-ingestion-version-{a,b}.md`, 컴포넌트2 = `component-2-analysis-webapp.md`.

## 1. 설계 원칙

- **결정적/비결정적 분리**: 파일·함수·register 추출, DB upsert, 임베딩, 랭킹은 **결정적 SW**. 요약·증상추출·답변합성만 **AI**.
- **idempotency**: 모든 적재는 `change_id` 기준 upsert. 재실행해도 동일 결과.
- **AI 추상화**: AI는 "task → JSON" 계약으로만 호출. 엔드포인트/실행기는 버전이 결정.
- **보안 불변식**: confidential 데이터는 사내 경계 밖으로 나가지 않는다. AI 호출은 승인된 사내 엔드포인트로만.

## 2. 데이터 모델 (엔티티)

- `patch` — 머지된 Gerrit change 1건 (diff, 메타, AI 요약)
- `ticket` — 연결된 Jira 티켓 (description, 라벨, 증상)
- `file_change` — patch가 건드린 파일 단위
- `symbol_touch` — 변경된 함수/register/#define 심볼 (구조 추출 결과)
- `chunk` — 임베딩 단위 (요약·증상·hunk 조각) + 벡터
- `query_log` — 사용자 이슈 질의·반환·피드백 (gold set·평가 재료)

## 3. DB 스키마 (Postgres + pgvector, DDL 스케치)

```sql
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE patch (
  change_id      TEXT PRIMARY KEY,          -- Gerrit Change-Id (조인·idempotency 키)
  gerrit_number  INTEGER,
  subject        TEXT,
  branch         TEXT,                       -- HW 세대/제품 구분 핵심
  hw_generation  TEXT,                        -- 정규화된 칩 세대 (branch에서 파생)
  author         TEXT,
  merged_at      TIMESTAMPTZ,
  commit_message TEXT,
  diff           TEXT,                        -- 원본 diff (사내 보관)
  ai_summary     JSONB,                       -- summarize_patch 결과(아래 계약)
  created_at     TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE ticket (
  jira_key     TEXT PRIMARY KEY,
  change_id    TEXT REFERENCES patch(change_id),
  summary      TEXT,
  description  TEXT,
  labels       TEXT[],
  symptom_kw   TEXT[],                         -- extract_symptom 결과
  created_at   TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE file_change (
  id         BIGSERIAL PRIMARY KEY,
  change_id  TEXT REFERENCES patch(change_id),
  path       TEXT,
  change_type TEXT                              -- A/M/D
);

CREATE TABLE symbol_touch (
  id         BIGSERIAL PRIMARY KEY,
  change_id  TEXT REFERENCES patch(change_id),
  path       TEXT,
  symbol     TEXT,                              -- 함수명/register/#define
  symbol_kind TEXT                              -- function|register|define|struct
);

CREATE TABLE chunk (
  id         BIGSERIAL PRIMARY KEY,
  change_id  TEXT REFERENCES patch(change_id),
  kind       TEXT,                              -- summary|symptom|hunk
  content    TEXT,
  embedding  vector(1024)                       -- bge-m3 차원에 맞춤
);
CREATE INDEX ON chunk USING hnsw (embedding vector_cosine_ops);
CREATE INDEX ON symbol_touch (symbol);
CREATE INDEX ON file_change (path);
CREATE INDEX ON patch (merged_at);
```

## 4. 수집·링킹 로직

1. Gerrit에서 change 메타·diff·파일목록 취득.
2. commit message footer에서 **Jira key**와 **Change-Id** 파싱.
3. Jira REST로 description·labels 취득.
4. `branch → hw_generation` 정규화 매핑 적용.
5. 구조 추출(§5) → `file_change`, `symbol_touch` 적재.
6. AI 계약(§6) 호출 → `ai_summary`, `symptom_kw` 채움.
7. chunk 생성 + 임베딩(§7) → `chunk` 적재.
8. 전부 `change_id` 트랜잭션 upsert (idempotent).

- **링킹 실패(키 누락)** 시 `patch`만 적재하고 `linking_status=unlinked` 플래그 → 성공률 측정.

## 5. 구조 메타 추출 (결정적 SW, 두 버전 공통)

- **tree-sitter (C 문법)**: diff hunk의 line range → 포함 함수 매핑.
- **universal-ctags**: 심볼 인덱스 보강.
- **register/#define**: 정규식 + 헤더 심볼 테이블로 변경된 매크로·레지스터명 추출.
- 출력: `symbol_touch` 레코드 목록. **AI 사용 안 함**(재현성·정밀도 위해).

## 6. AI 계약 (task → JSON, 버전 무관 동일 입출력)

각 task는 동일한 입력→JSON 스키마를 가진다. 버전 A는 `claude -p`, 버전 B는 Hermes가 실행하지만 **출력 레코드 형태는 같아야 한다**.

```jsonc
// task: summarize_patch  (입력: diff + commit_message + ticket.description)
{
  "summary": "패치 요약 (1-3문장)",
  "root_cause": "원인 추정",
  "fix_approach": "해결 방식",
  "symptom_keywords": ["hang","underrun"],
  "affected_ip_blocks": ["<ISP IP block>"]
}

// task: extract_symptom  (입력: 새 이슈 텍스트/로그)
{
  "symptoms": ["..."],
  "suspected_area": ["path 또는 함수 추정"],
  "keywords": ["..."]
}

// task: synthesize_answer  (입력: 질의 + 검색된 patch/ticket top-k)
{
  "answer": "초도 분석 서술 (advisory)",
  "cited_change_ids": ["I1234..."],
  "confidence": "high|medium|low"
}
```

- **검증**: 모든 AI 출력은 JSON 스키마 검증 통과 후에만 DB 반영. 실패 시 재시도 N회 → 그래도 실패면 `ai_summary=null`로 적재(데이터 손실 방지).

## 7. 임베딩

- 모델: **bge-m3**(한/영/코드 혼합), 로컬 GPU. 차원은 스키마와 일치.
- 임베딩 대상: `summary`, `symptom`, 주요 `hunk` 조각.
- **재인덱싱 절차**: 모델 교체 시 `chunk` 전체 재생성. `embedding_model_version` 컬럼/메타로 버전 추적(운영 시 추가).

## 8. 하이브리드 검색 (기능 A 핵심 알고리즘)

```
입력: 새 이슈 텍스트
1) extract_symptom(AI) → keywords, suspected_area, hw_generation 추정
2) 메타 선필터(SQL): hw_generation/branch 일치 + (suspected_area 파일/심볼 겹침 OR labels 매칭)
   → 후보 집합 C (수천 → 수십 건으로 축소)
3) 벡터 검색: C 내에서 질의 임베딩 cosine top-k
4) rerank: recency 가중(merged_at) + 심볼 겹침 점수 + 벡터 점수 가중합
5) synthesize_answer(AI): top-k 근거로 advisory 답변 + 인용
```

- **선필터가 정밀도의 핵심**. 코퍼스가 커져도 후보를 먼저 좁혀 품질 유지.

## 9. 인터페이스 (컴포넌트 2 — 분석 web app)

- P1: web app — 이슈 텍스트 입력 → advisory 답변 + 인용 patch/ticket 링크.
- P2: 신규 Jira 티켓 생성 시 자동 초도 분석 코멘트(봇, advisory 라벨).
- 상세는 `component-2-analysis-webapp.md`.

## 10. 보안 불변식 (필수 준수)

- AI 호출 엔드포인트는 **사내 승인 endpoint 화이트리스트**로만. 외부 egress 차단(네트워크 정책).
- diff·description 원문은 사내 DB·로그에만. 외부 서비스/메시징 커넥터 비활성.
- 시크릿(API 키 등)은 환경변수/볼트. 레포·로그에 평문 금지.

## 변경 이력

- 2026-06-03: 최초 생성 (공통 아키텍처·데이터모델·AI 계약).
