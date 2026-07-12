---
title: "코드 변경 이력 RAG 지식베이스 설계 — QA 에이전트용"
domain: "ai-agent"
sensitivity: "internal"
tags: ["analysis", "ai-agent", "rag", "vector-search", "hybrid-search", "co-change", "knowledge-base", "hermes", "pgvector", "rerank", "graphify", "knowledge-graph", "graphrag"]
created: "2026-07-12"
updated: "2026-07-12"
sources:
  - "2026-07-12 Claude Code 세션 대화 — 사내 Hermes QA 지식베이스 아키텍처 평가"
confidence: medium
related:
  - "wiki/patterns/code-change-rag-kb-spec.md"
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/multi-agent-shared-wiki-concurrency.md"
  - "wiki/analyses/personal-llm-wiki-curation.md"
  - "wiki/concepts/gieok.md"
  - "wiki/projects/gieok.md"
  - "wiki/projects/ht-trading.md"
---

# 코드 변경 이력 RAG 지식베이스 설계 — QA 에이전트용

Jira 패치 설명 + commit diff 를 임베딩한 RAG 지식베이스 위에 QA 전용 에이전트(Hermes)를 붙인 사내 시스템의 설계 평가에서 도출한 권고. 핵심 결론: **목적 중 "사이드 이펙트 예측"과 "최근 이슈 ↔ 최근 머지 인과 추측"은 벡터 유사도 문제가 아니라 관계형·시간형 질의 문제**이므로, 임베딩 검색만으로는 구조적으로 풀리지 않는다.

## 평가 대상 구조

- Jira 패치 설명 + commit diff → LLM 요약을 ingest 시 병행 생성 → 임베딩 vector DB (Postgres)
- rebuild 과정으로 전체 데이터에서 LLM wiki 페이지 일괄 생성 (데이터가 커지며 품질 저하 관찰됨)
- vector 검색 + rerank API 제공
- QA 전용 agent profile, 대화 중 중요 지식을 topic wiki 로 축적하는 역할 부여
- 목적: 디버깅 QA / 패치 사이드 이펙트 예측 / 구현 주의사항 / 회귀 원인 패치 추측

잘 잡은 부분: ingest 시 LLM 요약 병행 저장 (raw diff 는 임베딩 품질이 낮으므로 의미적 앵커 역할), vector + rerank 2단계 검색.

## 설계 권고

### 1. 관계·시간 질의는 구조화 메타데이터로 — 도구 분리

"이 패치가 건드린 파일을 최근에 누가 또 건드렸나"는 의미 유사도가 아니라 메타데이터 필터 + 조인 문제다. rerank 는 recency 를 보정하지 못한다.

- 커밋별로 파일 경로·심볼(함수/클래스명)·머지 일자를 구조화 컬럼으로 저장
- 에이전트에게 검색 엔드포인트 하나가 아니라 **도구를 분리해서 제공**:
  - `vector_search(query)` — 의미 검색
  - `keyword_search(query)` — lexical 검색 (아래 2번)
  - `recent_commits(path, since)` — 경로·기간 필터
  - `co_changed_files(path)` — 동반 변경 이력 (아래 3번)
  - `get_commit(id)` — diff 원문 조회
- 에이전트가 다단계로 조합하게 한다: 이슈 증상 파악 → 해당 영역 최근 머지 목록 → diff 정독 → 추론

### 2. Hybrid search — 디버깅 QA 에선 ROI 최고

에러 메시지, 함수명, 설정 키는 exact-match 토큰이라 임베딩 검색이 약한 영역. pgvector 옆에 Postgres full-text (또는 BM25) 를 붙여 hybrid 로 전환. 디버깅 질문 대부분이 식별자·에러 문자열 기반이므로 단일 개선 중 효과가 가장 클 것으로 판단.

### 3. Co-change 신호 — 사이드 이펙트 예측의 최강 신호

git 이력에서 "함께 바뀌는 파일 쌍" 통계는 계산이 싸고 회귀 예측력이 검증된 고전 신호. "파일 A 를 고친 커밋의 40% 가 파일 B 도 고쳤다" → A 만 고친 패치는 B 확인 필요. 의존성 그래프(콜 그래프) 없이도 사이드 이펙트 예측 목적의 대부분을 커버한다.

### 4. 변경 이력 KB 는 "지금 코드"를 모른다

diff·Jira 중심 KB 는 "무엇이 바뀌었나"만 알고 "지금 코드가 어떤가"를 모른다. 디버깅 질문은 현재 상태가 필요한 경우가 많으므로, 에이전트에 **현재 리포지토리 grep/read 도구를 RAG 와 병행 제공**하는 조합이 강하다. 전체 코드를 임베딩하려 하지 말 것 (agentic search 가 더 정확하고 항상 최신).

### 5. Wiki rebuild — 공급 주도 일괄 재생성 → 수요 주도 증분 갱신

전체 데이터에서 주기적으로 위키를 재생성하면 데이터가 클수록 품질이 떨어진다 (v1 llm_wiki 가 write-only 창고가 된 것과 동일 병리 — [[personal-llm-wiki-curation]]).

- **증분 갱신**: 신규 커밋을 retrieval 로 기존 topic 페이지에 라우팅해 해당 페이지만 갱신. 전체 재생성 금지
- **수요 주도 큐레이션**: 검색 API 질의 로그가 최고의 큐레이션 신호. 실제로 물어보는 주제만 페이지화
- 위키 페이지에 파생 원본 커밋 링크를 남겨, 해당 영역이 다시 바뀌면 페이지를 무효화·재생성 가능하게

### 6. 대화 유래 지식의 자기 오염 루프 차단

에이전트가 대화에서 위키를 직접 쓰면, 검증 안 된 대화 내용이 위키가 되고 다시 검색돼 사실처럼 인용되는 오염 경로가 생긴다.

- frontmatter 에 **provenance 구분** 필수: `origin: commit | conversation`
- 대화 유래 지식은 rerank 가중치를 낮추거나 사람 리뷰 후 승격
- 다중 사용자 동시 대화 환경이면 중복 topic 난립 → 쓰기는 단일 큐레이터로 모으고 나머지는 제안만 ([[multi-agent-shared-wiki-concurrency]] 의 (b) 형태)

### 7. 골든셋 평가 없이는 튜닝이 장님

"이슈 X 의 원인 패치는 Y 였다" 같은 정답 있는 실제 사례 20~30개로 retrieval hit rate 를 측정하는 골든셋을 먼저 구축. 위 개선들의 효과 판단 기준이 된다.

### 8. 코드 그래프 (graphify) 를 정보원으로 추가

원천 코드 git 에 graphify 를 셋업해 코드 그래프를 에이전트 정보원으로 추가하는 방안. 권고 ①(관계 질의)·④(현재 코드 접근) 의 갭을 정확히 메우므로 방향은 타당.

- **코드 엣지는 AST 기반 정적 분석** (import·class/function 정의) 이라 결정적·비환각 — 사이드 이펙트 판단의 근거로 사용 가능. 단 LLM 추출 엣지는 `EXTRACTED / INFERRED / AMBIGUOUS` 태그가 붙으므로, **INFERRED·AMBIGUOUS 엣지는 힌트로만** 쓰고 가중치를 낮출 것
- **에이전트 소비 방식**: `graph.json` (node-link 스키마) 을 도구로 노출 — `neighbors(path)`, `community_of(path)` 등. HTML 은 사람용, JSON 원문을 컨텍스트에 통째로 넣지 말 것
- **커뮤니티 요약을 RAG 문서로**: 클러스터별 요약은 "이 시스템에서 X 는 어디서 처리되나" 같은 전역 질문에 chunk 단위 vector 검색이 못 하는 답을 준다 (GraphRAG 패턴)
- **신선도**: `--update` 증분 갱신 지원 → 머지 파이프라인(CI)에 연동해 그래프가 HEAD 를 추적하게
- **규모**: 200+ 파일에서 경고 임계 → 사내 리포는 모듈·서브폴더 단위로 분할 실행
- **co-change 와 상호 보완**: 정적 그래프는 구조적 결합, co-change 는 경험적 결합(설정 파일·빌드 스크립트 등 정적 분석 사각지대 포함). **양쪽 모두에 잡히는 파일 쌍 = 최고 위험 신호**
- 사내 적용 전 실측 사례: [[ht-trading]] 에서 82개 파일 (~57K 단어) 코드베이스에 적용, God Nodes 와 9개 커뮤니티 도출 확인

## 적용 우선순위 제안

> 의견: ROI 순으로 ② hybrid search → ① 메타데이터 + 도구 분리 → ③ co-change → ⑦ 골든셋 → ⑤ wiki 수요 주도 전환 → ⑥ provenance. ④(현재 코드 접근)는 에이전트 런타임이 리포지토리에 접근 가능해지는 시점에, ⑧(graphify 코드 그래프)은 ③ co-change 확보 후 상호 보완용으로.

## 관련 맥락

- [[hermes-agent]] — 평가 대상 에이전트 플랫폼
- [[multi-agent-shared-wiki-concurrency]] — 공유 위키 동시 쓰기 위험, 단일 큐레이터 모델
- [[personal-llm-wiki-curation]] — 전부 기록하면 write-only 창고가 되는 병리와 큐레이션 기준
- [[gieok]] — 대화 → 위키 자동 축적 패턴의 개인용 구현 (같은 문제의식)
- [[ht-trading]] — graphify 코드베이스 분석 실측 사례 (82 파일, God Nodes·커뮤니티 도출)

## 변경 이력

- 2026-07-12: 최초 생성 — 사내 Hermes QA 지식베이스 아키텍처 평가 대화에서 설계 권고 정리
- 2026-07-12: ⑧ graphify 코드 그래프 정보원 추가 검토 반영 (출처: 동일 세션 후속 대화, graphify SKILL.md 확인)
- 2026-07-12: AI 실행용 작업 지시서 파생 — patterns/code-change-rag-kb-spec.md (related 추가)
