---
title: "코드 변경 이력 RAG 지식베이스 개선 — AI 실행용 작업 지시서"
domain: "ai-agent"
sensitivity: "internal"
tags: ["pattern", "ai-agent", "rag", "work-spec", "hybrid-search", "co-change", "graphify", "golden-set", "hermes"]
created: "2026-07-12"
updated: "2026-07-13"
sources:
  - "wiki/analyses/code-change-rag-kb-design.md"
  - "wiki/analyses/code-change-rag-kb-research.md"
confidence: medium
related:
  - "wiki/analyses/code-change-rag-kb-design.md"
  - "wiki/analyses/code-change-rag-kb-research.md"
  - "wiki/concepts/hermes-agent.md"
---

# 코드 변경 이력 RAG 지식베이스 개선 — AI 실행용 작업 지시서

사내 QA 에이전트용 지식베이스 개선 작업을 AI 에이전트에게 위임하기 위한 자기완결 지시서.
설계 근거는 `analyses/code-change-rag-kb-design.md` 에 있으며, **이 문서는 단독 반출을 전제로 필요한 맥락을 모두 본문에 포함**한다. Task 단위로 독립 세션에서 실행 가능하다.

## 실행 프로토콜 — 인터뷰 우선 (필수)

이 문서를 받은 AI 에이전트는 **어떤 Task 도 바로 시작하지 않는다.**

1. 문서 전체를 읽고 "환경 가정" 표, "사람 결정 필요 목록", 각 Task 의 **인터뷰 항목**을 수집한다
2. 도구로 직접 확인 가능한 항목(예: DB 확장 조회)은 스스로 확인하고, 나머지는 **한 번에 묶어서** 인터뷰로 질문한다 — 하나씩 나눠 묻지 않는다
3. 답변을 기록 문서(`answers.md` 등)로 남긴 뒤에야 Task 에 착수한다
4. 진행 중 새로운 불확실성이 나오면: **추측으로 진행 금지** → 해당 Task 중단 → 질문을 모아 추가 인터뷰 → 답변 기록 후 재개

## 시스템 컨텍스트 (현재 구조)

- Jira 패치 설명 + commit diff 를 원천으로, ingest 시 LLM 요약을 병행 생성해 임베딩 vector DB (Postgres) 에 저장
- vector 검색 + rerank API 제공
- 전체 데이터에서 LLM wiki 페이지를 일괄 rebuild (데이터 증가에 따라 품질 저하 중)
- QA 전용 agent profile (Hermes) 이 이 API 를 사용, 대화 중 topic wiki 도 작성
- 시스템 목적: 디버깅 QA / 패치 사이드 이펙트 예측 / 구현 주의사항 / 회귀 원인 패치 추측

핵심 진단: 사이드 이펙트 예측·회귀 추적은 의미 유사도가 아니라 **관계형(파일·심볼)·시간형(머지 일자) 질의** 문제이므로, 구조화 메타데이터와 도구 분리 없이는 풀리지 않는다.

## 비목표 · 가드레일 (모든 Task 공통)

- **전체 코드베이스 임베딩 금지** — 현재 코드는 도구(grep/read)로 접근한다 (Task 8)
- 기존 검색 API 의 기존 소비자 호환성 유지 — 엔드포인트 추가는 OK, 기존 시그니처 파괴 금지
- wiki 페이지 **자동 삭제 금지** — stale 마킹까지만 (비가역 조작 방지)
- 골든셋(Task 0) 측정 없이 검색 파라미터 튜닝을 프로덕션에 반영하지 않는다
- 불확실하면 추측으로 진행하지 말고 "실행 프로토콜"의 추가 인터뷰로 해소한다

## 환경 가정 (⚠️ 실행 전 확인)

| 가정 | 확인 방법 |
|---|---|
| Postgres 에 pgvector 확장 사용 중 | `\dx` |
| 원천 git 저장소 이력 전체 접근 가능 | `git log --oneline \| wc -l` |
| ingest 파이프라인 코드 수정 권한 있음 | 담당자 확인 |
| 검색 API 질의 로그 수집 가능 (Task 6 전제) | API 서버 로깅 설정 확인 |

## 사람 결정 필요 목록 (AI가 임의 결정 금지)

1. hybrid 융합 방식·가중치 (기본 제안: RRF, k=60) — Task 2
2. 에이전트 런타임의 저장소 read-only 접근 허용 여부 — Task 8
3. 대화 유래 지식 리뷰 프로세스의 책임자 지정 — Task 7
4. graphify 실행 대상 모듈 선정 (전체 리포는 규모 초과) — Task 5
5. 메타데이터 백필 대상 기간 (전체 vs 최근 N년) — Task 1

## 실행 순서

```
Task 0 (골든셋) ──┬─→ Task 2 (hybrid, 검증에 0 필요)
Task 1 (메타데이터) ──→ Task 3 (도구 분리) ──→ Task 6 (wiki 수요 주도, 질의 로그 필요)
Task 4 (co-change) ─── 독립, Task 3 의 도구로 노출
Task 5 (graphify) ──── 독립
Task 7 (provenance) ── 독립
Task 8 (코드 접근) ─── 결정 2번 대기 (연구 근거로 우선순위 상향 — 조기 결정 요청 권장)
Task 9 (출처 인용) ─── 독립, 즉시 가능 (프롬프트 변경만)
Task 10 (피드백 루프) ─ 독립, Task 0 골든셋 보충 경로
```

권장: Task 0 최우선(다른 모든 작업의 측정 기준) → 9(즉시, 저비용) → 1·4·5 병렬 → 2 → 3 → 8 → 6·7 → 10.

---

## Task 0: 골든셋 구축 (AI 추출 + 사람 검수)

- **목표**: 이후 모든 검색 개선의 효과를 측정할 정답 데이터셋 확보. 이것 없이는 튜닝이 주관적 판단이 된다.
- **인터뷰 항목 (착수 전 확인)**: 이슈 트래커 API 접근 가능 여부, 커밋 메시지에 Jira 키를 붙이는 관행 여부, revert 커밋 관행 존재 여부, 사내 QA 문의 로그 접근 가능 여부, 대상 기간, 후보 승인자.
- **작업 — AI 추출 방법 (정답 신뢰도 순)**:
  1. **revert 역추적**: `git log --grep -i revert` — revert 커밋은 원인 커밋을 명시하므로 정답이 자동 확정된다. 질문은 revert 사유·연결 이슈의 증상에서, 정답은 revert 대상 커밋
  2. **SZZ (blame 역추적)**: 이슈와 연결된 fix 커밋이 고친 라인을 `git blame` 으로 역추적 → 그 라인을 도입한 커밋 = 원인 커밋. 질문은 이슈의 증상 서술에서, 정답은 도입 커밋
  3. **이슈 코멘트에 원인 패치가 명시된 사례**: 트래커 검색("caused by", "원인 커밋" 등)으로 수집
  4. **디버깅 QA 유형**: 실제 문의 로그가 있으면 (질문 원문, 당시 답변에 인용된 커밋·문서) 쌍으로 수집
- **Leakage 금지**: 질문 텍스트를 정답 커밋의 diff·요약에서 생성하지 말 것 — 질문·정답의 어휘가 겹쳐 검색 난이도가 비현실적으로 낮아지고 측정이 과대평가된다. 질문은 반드시 **이슈·사용자 쪽 표현**(이슈 제목, 증상 보고 원문)에서 가져온다.
- **사람 검수 게이트 (생략 불가)**: AI 는 후보 50건+건별 근거를 뽑아 제시까지만 한다. 사람이 승인한 것만 골든셋에 편입. 골든셋이 오염되면 이후 모든 측정이 무의미하다.
- **유형 배분**: 회귀 원인 추적 10+, 디버깅 QA 10+, 사이드 이펙트("패치 Y 영향 범위") 5+.
- **산출물**: 후보 추출 스크립트, `golden.jsonl` — `{question, expected_commit_ids[], type, evidence_link, extraction_method}`; 기준선 측정 스크립트 (hit@5, MRR).
- **완료 조건**: 승인된 30건 이상, 각 건에 정답 근거 기록, 현재 시스템 기준선 수치 기록됨.
- **검증**: 스크립트 재실행 시 동일 수치 재현.

## Task 1: 커밋 메타데이터 구조화

- **목표**: 관계·시간 질의를 SQL 로 풀 수 있게 커밋별 구조화 컬럼 추가.
- **작업**: 스키마 확장 — `commit_id, jira_key, merged_at, author, files[] (경로), symbols[] (변경된 함수·클래스명), module`. 파일 경로는 diff 헤더에서 결정적으로 추출, 심볼은 hunk 헤더 또는 tree-sitter. ingest 파이프라인에 통합 + 기존 커밋 백필 (기간은 사람 결정 5번).
- **임베딩 개선 (검증됨)**: 청크·요약을 임베딩할 때 **파일 상대 경로를 텍스트에 포함**한다 — 이것만으로 MRR +16.9% 개선이 검증됨 (arxiv 2605.17965). ingest 쪽 저비용 변경이므로 이 Task 에 포함해 함께 백필.
- **메타데이터 enrichment (검증됨, Uber)**: ingest 시 LLM 요약 외에 **FAQ·키워드**도 생성해 청크 메타데이터로 저장하고 lexical 검색(Task 2)에 공급 — Uber Genie 가 이 방식 포함 개편으로 acceptable 답변 상대 +27% 달성 (단독 기여 ablation 은 없으므로 골든셋으로 효과 확인).
- **산출물**: 마이그레이션 스크립트, 백필 스크립트, 갱신된 ingest 코드.
- **완료 조건**: "경로 P 를 건드린 최근 30일 커밋" 이 SQL 한 방으로 응답.
- **검증**: 샘플 커밋 20건 수동 대조 — 파일 목록 정확도 100%, 심볼 정확도 95%+.

## Task 2: Hybrid search (lexical + vector)

- **목표**: 에러 메시지·함수명·설정 키 같은 exact-match 토큰 질의 개선. 디버깅 QA 유형에서 ROI 최고.
- **작업**: Postgres full-text (또는 BM25 확장) 인덱스 추가 → vector 결과와 융합 (기본 RRF, 방식·가중치는 사람 결정 1번) → 융합 결과에 기존 rerank 적용.
- **토크나이저 주의**: lexical 인덱스는 기본 텍스트 분석기가 아니라 **code-aware 토크나이저**로 — 구두점 보존, stemming 금지, stop-word 제거 금지 (GitHub 코드 검색 교훈, 미검증이나 방향 일치).
- **반박된 가정 주의**: "diff 원문 rerank 가 커밋 메시지 rerank 보다 우수"라는 주장은 리서치 검증에서 반박됨(0-3) — diff 원문 rerank 단계 추가는 골든셋 측정으로 효과 확인 후에만 도입.
- **산출물**: lexical 인덱스, 융합 로직, 갱신된 검색 API.
- **완료 조건**: 골든셋 "디버깅 QA" 유형에서 hit@5 가 기준선 대비 개선. 다른 유형 회귀 없음.
- **검증**: Task 0 스크립트로 전·후 비교표 산출.

## Task 3: 검색 도구 분리

- **목표**: 에이전트가 단일 검색이 아니라 목적별 도구를 다단계로 조합하게 한다.
- **작업**: API 를 도구 단위로 분리 노출 —
  - `vector_search(query, top_k, filters?)` / `keyword_search(query, top_k, filters?)`
  - `recent_commits(path_prefix, since, until?, limit)` — Task 1 컬럼 기반
  - `get_commit(commit_id)` → diff 원문 + 요약 + Jira 링크
  - `similar_commits(query, top_k)` → 유사 커밋 메시지 검색 후 **그 커밋들이 수정한 파일 목록** 반환 — 회귀 추적의 검증된 1차 후보 신호 (BM25 커밋 메시지 → rerank 캐스케이드가 BM25 단독 대비 MRR 약 2배, arxiv 2502.07067)
  - `co_changed_files(path, min_support)` — Task 4 완료 후 활성화
  - agent profile 프롬프트에 다단계 절차 지침 추가: 증상 파악 → 해당 영역 최근 머지 목록 → diff 정독 → 추론.
- **쿼리 변환 (검증됨)**: 버그·이슈 질의는 에이전트가 **구조적 쿼리**(식별자·트레이스백·모듈명)와 **행동적 쿼리**(증상, 기대 vs 실제 동작) 두 개로 분해해 각각 검색 후 결과를 병합한다 — 두 관점이 서로 다른 파일을 찾으며 병합 시 Top-10 recall 94.3% (arxiv 2605.17965). 단 이득은 retrieval 단독 기준이며 전체 agentic 파이프라인 안에서는 축소됨(+3.4% MRR) — 저비용이므로 도입하되 기대치는 보수적으로.
- **설계 원칙**: 에이전트의 LLM 검사·재랭킹은 검색으로 압축된 후보 집합 안에서만 수행 (bounded rerank). 개방형 리포 전체 탐색보다 정확도·비용 모두 우위가 검증됨 (Top-1 86.7%, 비용 1/18).
- **완료 조건**: 회귀 추적 유형 골든셋 개선 + 에이전트가 실제로 2개 이상 도구를 조합한 트레이스 확인.
- **검증**: 대표 시나리오 3건 수동 트레이스 검토 (도구 호출 순서가 절차 지침대로인지).

## Task 4: Co-change 통계

- **목표**: "함께 바뀌는 파일 쌍" 통계 확보 — 사이드 이펙트 예측의 최강 신호이며 계산이 싸다.
- **작업**: `git log --name-only` 에서 파일 쌍 동반 변경 빈도 산출, support(동반 횟수)·confidence(조건부 비율) 를 테이블로 저장, 주기 갱신 job (주 1회).
- **산출물**: 산출 스크립트, `co_change` 테이블, cron job.
- **완료 조건**: `co_changed_files(path)` 가 confidence 순 상위 쌍 반환, 자동 갱신 동작.
- **검증**: 개발자가 아는 결합 쌍 5건을 사전 수집 → 상위 결과에 포함되는지 확인.

## Task 5: graphify 코드 그래프

- **목표**: 코드 구조 그래프(AST 기반 import·정의 관계)와 모듈 커뮤니티 요약을 에이전트 정보원으로 추가.
- **작업**: 대상 모듈(사람 결정 4번)에 graphify 실행 → `graph.json` 을 `neighbors(path)`, `community_of(path)` 도구로 노출 → 커뮤니티 요약 텍스트를 RAG 인덱스에 문서로 추가 → `--update` 증분 갱신을 머지 파이프라인(CI)에 연동.
- **제약**: `INFERRED`/`AMBIGUOUS` 태그 엣지는 힌트로만 사용 (rerank 가중치 하향), `EXTRACTED`(AST) 엣지만 사이드 이펙트 근거로 사용. graph.json 원문을 에이전트 컨텍스트에 통째로 넣지 말 것. co-change(Task 4)와 양쪽 모두에 잡히는 파일 쌍은 최고 위험으로 표시.
- **완료 조건**: 도구 응답 동작 + CI 에서 증분 갱신 확인.
- **검증**: "X 는 이 시스템 어디서 처리되나" 유형 전역 질문 5건에서 답변이 커뮤니티 요약을 근거로 인용하는지.

## Task 6: Wiki rebuild → 수요 주도 증분 갱신

- **목표**: 전체 일괄 재생성(데이터 증가에 따라 품질 저하)을 폐기하고, 실제 질의되는 주제만 증분 유지.
- **작업**: 검색 API 질의 로그 수집 → 주기적으로 빈출 토픽 추출 → 해당 토픽 페이지만 생성·갱신. 신규 커밋은 retrieval 로 기존 페이지에 라우팅해 그 페이지만 갱신. 각 페이지에 파생 원본 commit_id 기록 → 해당 파일 재변경 시 stale 마킹 (삭제 금지).
- **주기 자가 검증 잡 (검증됨, Meta)**: 수 주 간격 자동 잡으로 위키 페이지의 참조 경로 검증·커버리지 갭 탐지·품질 critic 재실행. 페이지 형식은 Meta 의 "compass, not encyclopedia" 원칙 참고 — 망라 문서 대신 25~35줄 내비게이션용 (Quick Commands / Key Files / Non-Obvious Patterns / See Also).
- **완료 조건**: 일괄 rebuild 파이프라인 중단, 증분 경로로 대체, stale 마킹 동작.
- **검증**: 생성·갱신된 페이지 목록이 질의 로그 상위 토픽과 일치하는지 월 1회 대조.

## Task 7: Provenance 구분

- **목표**: 대화 유래 지식이 검증 없이 사실로 재인용되는 자기 오염 루프 차단.
- **작업**: 지식 항목에 `origin: commit | conversation` 필드 추가, 검색 결과에 origin 노출, conversation 유래는 rerank 가중치 하향 + 리뷰 큐 (책임자는 사람 결정 3번). agent profile 에 "conversation 유래 단독 근거로 단정 답변 금지" 지침 추가.
- **완료 조건**: 모든 신규 항목에 origin 기록, 기존 항목 백필, 가중치 차등 적용.
- **검증**: conversation 유래 문서만 히트하는 질의에서 답변이 출처 한계를 명시하는지 3건 확인.

## Task 8: 현재 코드 접근 도구 (우선순위 상향 — 정확성 요건)

- **전제**: 사람 결정 2번 (저장소 read-only 접근 허용) 이후 착수. 아래 근거를 첨부해 **조기 결정을 요청**할 것.
- **근거 (검증됨)**: stale 컨텍스트는 컨텍스트 없음보다 해롭다 — 낡은 스니펫만 검색되면 모델이 낡은 시그니처·상태에 확신을 갖고 답하며(17샘플 중 최대 15건), 현재 상태를 함께 검색하면 stale 참조율이 88.2%→23.5%로 급감. rerank 순서 튜닝은 효과 0 — "현재 상태가 검색되는가"가 지배 요인 (arxiv 2605.14478). 따라서 이 Task 는 부가 기능이 아니라 **답변 정확성 요건**이다.
- **작업**:
  - read-only clone 대상 `grep_repo(pattern, path?)`, `read_file(path, range?)` 도구 제공. 임베딩하지 않는다 (비목표 참조)
  - diff 청크 검색 결과에 "이 파일의 현재 버전과 다를 수 있음" 표시 + 해당 파일의 **현재 코드 co-retrieval 을 기본 동작**으로 (에이전트가 diff 를 인용하기 전 현재 상태 확인)
- **완료 조건**: 디버깅 QA 유형에서 에이전트가 KB 검색 + 현재 코드 확인을 조합한 트레이스 확인.
- **검증**: 최근 리팩터링된 영역 질문 3건 — 낡은 diff 가 아니라 현재 코드 기준으로 답하는지.

## Task 9: 답변 출처 인용 강제 (즉시 적용 가능)

- **목표**: 환각 완화 — Uber Genie 검증 사례: 검색된 모든 청크에 출처가 붙은 sub-context 를 강제하고 "제공된 sub-context 에서만 답하라"로 grounding. **단독으로는 불충분** — Genie 도 이 계약만으로는 helpfulness 48.9% 에 머물러 agentic 개편을 병행했다. 즉시 적용하되 이것만으로 품질 문제가 끝난다고 기대하지 말 것.
- **작업**: 검색 API 응답의 각 청크에 출처(commit_id·Jira 키·wiki 페이지 링크) 필드 포함, agent profile 에 "제공된 컨텍스트에서만 답하고 근거 출처를 항상 인용, 컨텍스트에 없으면 모른다고 답할 것" 지시 추가.
- **완료 조건**: 모든 답변에 근거 출처 링크 포함.
- **검증**: 샘플 답변 10건 전수 — 출처 존재 여부 + 인용된 문서가 실제로 답의 근거인지 대조.

## Task 10: 온라인 피드백 루프

- **목표**: 오프라인 골든셋만으로는 부족 — Uber Genie 가 응답마다 4단계 피드백 버튼(Resolved/Helpful/Not Helpful/Not Relevant)으로 평가 루프를 닫는 구조가 검증됨 (Sourcegraph 의 online-offline 평가 괴리 보고는 미검증). 실사용 피드백으로 품질 추적 + 골든셋 보충.
- **인터뷰 항목 (착수 전 확인)**: 챗 플랫폼에서 응답별 버튼 UI 가능 여부, 피드백 로그 적재 위치.
- **작업**: 응답마다 Resolved / Helpful / Not Helpful 버튼 → 로그 적재 → 주간 대시보드. Not Helpful 사례는 Task 0 골든셋 후보로 환류 (사람 검수 게이트 동일 적용).
- **완료 조건**: 피드백 수집·저장 동작 + 주간 리포트 1회 생성.
- **검증**: Not Helpful 사례 중 1건 이상이 골든셋 후보로 등록되는 흐름 확인.

## 변경 이력

- 2026-07-12: 최초 생성 — analyses/code-change-rag-kb-design.md 의 권고 8건을 AI 실행용 Task 지시서로 재구성
- 2026-07-12: 인터뷰 우선 실행 프로토콜 추가, Task 0 을 AI 추출(revert·SZZ 역추적)+사람 검수 방식으로 구체화
- 2026-07-13: 리서치 검증 결과 반영 (analyses/code-change-rag-kb-research.md) — 파일 경로 임베딩(Task 1), code-aware 토크나이저·diff rerank 반박 주의(Task 2), similar_commits 도구·이중 쿼리 변환·bounded rerank 원칙(Task 3), stale 근거로 Task 8 우선순위 상향 + 현재 코드 co-retrieval, Task 9(출처 인용 강제)·Task 10(온라인 피드백 루프) 신설
- 2026-07-13: 3차 검증 완료 반영 — Task 10 근거(Genie 피드백 버튼) 검증 확정으로 갱신
- 2026-07-14: 4차(최종) 검증 반영 — 메타데이터 enrichment(Task 1), 쿼리 변환 효과 축소 유의(Task 3), 주기 자가 검증 잡·compact 페이지 원칙(Task 6), 출처 인용 단독 불충분 유의(Task 9)
