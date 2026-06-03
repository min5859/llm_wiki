---
title: "ISP Driver 패치 지식 RAG 시스템 (Gerrit×Jira 기반 이슈 초도 분석)"
domain: "work"
sensitivity: "internal"
tags: ["rag", "gerrit", "jira", "isp-driver", "exynos", "regression", "agentic-ingestion", "on-prem-llm", "hermes-agent", "nous-research", "mcp"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "design-discussion: 2026-06-03 Claude Code 세션 (구두 논의, raw 파일 없음)"
  - "web: https://github.com/nousresearch/hermes-agent (2026-06-03 조회)"
  - "web: https://hermes-agent.nousresearch.com/ (2026-06-03 조회)"
  - "web: https://blogs.nvidia.com/blog/rtx-ai-garage-hermes-agent-dgx-spark/ (2026-06-03 조회)"
confidence: "medium"
related:
  - "wiki/projects/isp-patch-rag/00-proposal.md"
  - "wiki/projects/isp-patch-rag/common-architecture.md"
  - "wiki/projects/isp-patch-rag/component-1-ingestion-version-a.md"
  - "wiki/projects/isp-patch-rag/component-1-ingestion-version-b.md"
  - "wiki/projects/isp-patch-rag/component-2-analysis-webapp.md"
  - "wiki/projects/isp-patch-rag/phase-3plus-forward-design.md"
---

# ISP Driver 패치 지식 RAG 시스템

Exynos AP의 ISP HW Android device driver 개발 과정에서 생성되는 **Gerrit 머지 패치 + Jira 리뷰 티켓**을 머지 시점마다 지식 DB로 축적하고, 새 이슈 발생 시 AI가 "유사 과거 이슈 검색 → 회귀/side-effect 후보 분석 → 재현 가이드"를 1차로 제공하는 사내 on-prem 시스템 설계 문서.

> 본 문서는 2026-06-03 설계 논의 기준의 방향성 정리이며, 구현 전 타당성 검토 단계입니다. 수치·구체 구현은 미확정(confidence: medium).

## 배경 / 문제

- 업무 특성상 **이슈 해결·기능 구현 패치**가 주 산출물. 하루 약 10건 머지(연 ~2,500건, 수년 누적 시 수만 건 규모).
- 패치는 git 형상 관리 + **remote Gerrit** 머지. 패치마다 **Patch Review용 Jira 티켓** 생성, 티켓 description은 commit message보다 상세한 패치 설명 포함.
- 현재 이 지식이 개인·티켓에 흩어져 있어, 비슷한 이슈가 반복돼도 과거 해결 이력을 빠르게 찾기 어려움.

## 목표

머지 이벤트마다 `patch diff + Jira description + 메타데이터`를 지식 DB로 적재하고, 새 이슈 발생 시 AI가 초도 분석을 수행:

1. **(1차) 유사 이슈 검색** — "이전에 비슷한 이슈가 있었나? 당시 어떻게 고쳤나?"
2. **(확장) side-effect / 회귀 후보 탐지** — "최근 머지된 패치의 부작용 아닌가?"
3. **(확장) 패치 가이드** — 회귀 원복 경고, 패치 무력화 경고, 재현 TC 제안 등

## 전제 — 보안 (1순위 제약)

- ISP driver 코드와 Jira description은 **사내 confidential 자산**. 외부 LLM API(Claude/OpenAI 등)로 전송 불가.
- **on-prem 사내 GPU + 로컬/사내 LLM 전제** (사용 가능 확인됨, 2026-06-03).
- 본 wiki 문서에는 실제 코드·register·고유 수치를 기록하지 않음(아키텍처/패턴만). 실데이터는 사내 DB에만 존재.

## 데이터 파이프라인 (수집 → 링킹 → 구조화)

### 수집 트리거 (머지 시점)

- Gerrit `change-merged` 이벤트 구독 (`stream-events` SSH 또는 webhooks 플러그인).
- Gerrit REST API로 diff·파일목록·commit metadata 취득.
- Jira REST API로 commit footer의 티켓 키 조회 → description·증상·라벨 취득.

### 링킹 (데이터 품질의 핵심)

- 조인 키: commit message의 **Change-Id** + **Jira issue key**.
- 두 키가 일관되게 포함되는지가 데이터 품질을 좌우. **초기에 링킹 성공률 측정 필수** (미포함 과거 패치는 누락됨).

### 구조화 (단순 텍스트 임베딩만으로 부족한 이유)

C driver diff는 자연어 대비 임베딩 변별력이 약함. diff에서 **구조적 메타데이터**를 추출해 함께 저장해야 확장 기능(side-effect 탐지)이 동작:

- 변경된 **파일 / 함수** (tree-sitter C 파서 + universal-ctags로 hunk→함수 매핑)
- 건드린 **ISP IP 블록 / register / `#define` 심볼**
- 증상 키워드 (Jira description에서 추출: hang, underrun, frame drop, timeout 등)

## 수집 방식 — Agentic Ingestion (Hermes Agent 활용)

ingest 파이프라인을 고정 코드 스크립트가 아닌 **에이전트(Hermes Agent)** 가 Gerrit/Jira를 조회·정규화·요약·적재하도록 위임하는 방식을 시도한다.

### Hermes Agent란 (검증됨, 2026-06-03 web)

- **Nous Research가 2026-02 공개한 오픈소스 자율 AI 에이전트**. 출시 ~3개월 만에 GitHub 14만+ 스타, OpenRouter 사용량 글로벌 1위(일 2,000억+ 토큰)로 "2026 최고 성장 오픈소스 에이전트 프레임워크"로 불림.
- 구현: **Python(~84%) + TypeScript**, Node.js 의존. 자가호스팅 — Docker Compose 제공, 6개 백엔드(local/Docker/SSH/Singularity/Modal/Daytona), $5 VPS~GPU 클러스터.
- **Provider-agnostic LLM**: `hermes model`로 모델 교체, **커스텀 엔드포인트 지정 가능** → 사내 로컬 LLM 연결 경로 존재.
- **연동**: MCP(Model Context Protocol) 서버 연결, native git 명령 지원, REST(Nous Portal), 메시징(Telegram/Slack/Discord 등).
- **메모리/스킬**: FTS5 세션 검색 + LLM 요약, MEMORY.md/USER.md 기반 영속 메모리, 복잡 작업 후 **자동 skill 생성·자가개선**(agentskills.io 호환, `~/.hermes/skills/`).
- **스케줄·확장**: built-in cron, 병렬 subagent spawn, "Python 스크립트가 RPC로 도구 호출", 배치 trajectory 생성.

### 우리 설계에의 적용

- **Gerrit/Jira 연결**: 전용 **MCP 서버**로 노출하는 것이 정석(또는 native git + REST 호출 스킬). 머지 트리거는 cron 폴링 또는 webhook→큐.
- **수집 자동화**: built-in cron으로 정기 ingest, 백필은 병렬 subagent + RPC 스크립트 경로 활용.
- **메타추출/요약**: diff/티켓 해석·증상 키워드 추출·요약을 에이전트 스킬로 위임 → 포맷 변화에 유연, 코드 유지보수 부담 감소.

### 리스크 / 검증 포인트

- **보안(최우선)**: Hermes의 기본 통합(Nous Portal·OpenRouter)은 **외부 송신**. confidential 환경에서는 반드시 외부 엔드포인트를 차단하고 **사내 로컬 LLM 엔드포인트로만** 구성. 외부 도구·메시징 커넥터도 비활성/검증 필요.
- **적재 결정성·재현성**: 에이전트 비결정성은 DB 일관성에 치명적 → structured output 스키마 강제 + 검증 단계 + idempotency(Change-Id 기준 upsert).
- **처리량/비용**: 하루 10건은 부담 적음. 백필(수만 건)은 병렬 subagent 처리 전략 별도 검토.
- **버전 안정성**: 빠르게 진화하는 신생 OSS — 사내 도입 시 버전 고정·자체 포크 운영 고려.

### 권장 절충

수집 트리거·저장 스키마·idempotency는 **코드로 골격**을 잡고, **diff/티켓 해석·메타추출·요약만 Hermes 스킬에 위임**하는 하이브리드. 순수 agentic vs 코드 ingestion을 Phase 0에서 비교 PoC 후 결정.

## 기능 설계

### 기능 A — 유사 이슈 검색 (1차 MVP)

하이브리드 검색:

- **벡터 검색**(증상·description 의미 유사) + **키워드/메타 필터**(같은 IP 블록·파일·register).
- 새 이슈의 증상 텍스트 임베딩 → top-k 유사 과거 패치 + 링크 Jira 반환 → LLM이 "유사 근거 + 당시 해결 방법" 요약.
- **MVP 범위는 여기까지.** 단독으로도 실무 체감 가치 큼.

### 기능 B — side-effect / 회귀 후보 탐지 (확장, 고난도)

순수 임베딩 불충분. **구조 신호 + 시간 상관** 조합:

1. 새 이슈의 **의심 영역**(증상→관련 파일/함수, 또는 재현 로그의 함수) 파악.
2. 최근 N일/N건 머지 중 **같은 파일·함수·register를 건드린 패치**를 blast-radius로 필터 (`git blame`, `git log -L`).
3. 시간 창(증상 직전 머지) + 영역 겹침 → **회귀 후보 점수** 산출.
4. 후보 diff·티켓을 LLM에 투입 → "이 변경이 증상을 유발할 수 있는 메커니즘"을 인과적으로 서술.

> 핵심: "AI가 알아서"가 아니라 **휴리스틱으로 후보를 좁히고 → LLM이 인과 설명**해야 정밀도 확보.

### 기능 C — 패치 작성 가이드 (장기 비전)

- **회귀 원복 경고**: 과거 이슈로 수정된 라인을 다시 원복하는 패치인지 감지 → "EXYNOSISP-XXXX에서 이 부분을 의도적으로 변경함" 경고.
- **패치 무력화 / 충돌 경고**: A 패치 이후 B 패치가 A의 효과를 무효화·중복·충돌시키는지 감지.
- **재현 TC 제안**: 이슈 증상·영역에 매칭되는 과거 재현 TC를 제안해 "이 TC로 재현 가능성 검토" 안내.
- (장기) 신규 이슈에 대한 초안 패치 방향·관련 코드 포인터 제시.

## 기술 스택 (on-prem 전제)

| 영역 | 1순위 추천 | 비고 |
|---|---|---|
| 수집 트리거 | Gerrit stream-events / webhooks, Jira REST | 머지 이벤트 구동 |
| 해석·메타추출 | Hermes Agent(MCP + 사내 LLM 엔드포인트) + structured output | agentic ingestion PoC, 외부 송신 차단 |
| 코드 구조 추출 | tree-sitter(C), universal-ctags | hunk→함수, register 심볼 |
| 저장소 | **Postgres + pgvector** | 구조 메타 + 벡터 단일 저장. 수만 건 규모면 충분 |
| 임베딩 | bge-m3(한/영/코드) 또는 code 특화 모델 | **로컬 GPU 구동** |
| LLM | 사내 LLM 우선 / Qwen2.5-Coder·DeepSeek-Coder 로컬 | **외부 API 금지** |
| 서빙 | FastAPI + 경량 큐(Redis/RQ) | 단순 시작 |
| 인터페이스 | Gerrit 코멘트 봇 / Jira 코멘트 / 쿼리 CLI | 기존 워크플로에 녹임 |

- 전용 벡터 DB(Qdrant/Milvus)는 규모·ANN 성능 필요 시 도입. 초기 over-engineering 지양.

## 로드맵

1. **Phase 0 — 타당성 PoC**: 과거 N개월 patch+Jira 백필, 링킹 성공률 측정, agentic ingestion vs 코드 ingestion 비교.
2. **Phase 1 — MVP(기능 A)**: 하이브리드 검색 + CLI 질의 "유사 이슈".
3. **Phase 2 — 운영화**: 머지 이벤트 실시간 ingest, Gerrit/Jira 봇 코멘트.
4. **Phase 3 — 확장(기능 B)**: blast-radius 기반 side-effect 후보 탐지.
5. **Phase 4 — 가이드(기능 C)**: 회귀 원복·패치 무력화 경고, 재현 TC 제안.

## 효용성 평가

기능별 효용 편차가 크며, 성패의 대부분은 **Jira description 품질**이라는 단일 변수에 좌우된다.

- **효용 확실 (높음)**: 기능 A는 화려한 AI 없이 "링크된 patch+ticket 검색 DB"만으로도 가치가 큼. driver 팀은 fault localization 비용이 크고 인력 교체 시 tribal knowledge 손실이 큼 → AI 정확도와 무관하게 institutional memory로 남음. 수집 비용이 거의 없고(하루 10건), 코퍼스가 쌓일수록 가치가 **복리로** 증가하는 자산형 시스템.
- **효용 조건부 (중간, 검증 필요)**: 기능 B는 맞으면 가장 비싼 문제를 잡지만 false positive 누적 시 신뢰가 급격히 붕괴 → gold set 평가 전엔 "advisory 후보 힌트" 수준. ISP 이슈 상당수가 **HW 타이밍·silicon revision 의존**이라 코드 diff+텍스트만으로 인과가 안 잡히는 본질적 한계 존재.
- **핵심 결정 변수**: Jira description이 일관·충실하면 검색 품질↑, 한 줄 티켓이 많으면 garbage-in. ROI 1순위 요인.
- **포지셔닝 권고**: "판정 게이트"가 아닌 **"엔지니어 옆 advisory 초도 분석"**으로 두면 정확도 허들이 낮아져 실현 효용이 커짐.

> 요약: 시작 가치 높음(기능 A + 지식 자산화), 고도화는 검증 전제, 기대치는 advisory로 설정.

## 확장성 — 수년치 코퍼스가 쌓여도 동작하는가

결론: **용량(capacity)은 비이슈, 진짜 위험은 정밀도 희석과 지식 노후화**이며 둘 다 설계로 해결 가능.

- **용량은 문제 없음**: 하루 10건 → 연 ~2,500건, 10년 누적 ~2.5만 patch. chunk·벡터로 풀어도 수십만 벡터 수준으로, pgvector(수백만 벡터 처리)에는 작은 규모. ANN 인덱스·저장 모두 여유.
- **LLM 컨텍스트도 무관**: 전체 코퍼스를 넣지 않고 항상 **top-k만** 투입하므로 코퍼스 크기가 커져도 컨텍스트 폭증 없음.
- **진짜 위험 1 — 정밀도 희석**: 코퍼스가 커지면 유사 후보·near-duplicate가 늘어 검색 정밀도가 떨어짐. → **메타데이터 선필터링**(IP 블록·파일·register·branch·시간창)으로 후보를 좁힌 뒤 벡터 검색 + reranking 적용. 용량이 아니라 **이것이 실질 병목**.
- **진짜 위험 2 — 지식 노후화**: 구세대 Exynos·deprecated HW 패치가 현재 칩 검색을 오염. → **recency 가중 + HW 세대/branch 메타 필터**, 그리고 LLM-wiki 패턴의 핵심인 **주기적 consolidation**(반복 패턴을 IP·증상 단위 curated 요약 페이지로 정리) 적용. raw diff 더미가 아니라 **정제된 지식**을 검색하게 만들면 raw 볼륨과 무관하게 품질 유지.
- **운영 고려 — 재인덱싱**: 수년 뒤 임베딩 모델 교체 시 전체 re-embedding 필요 → 모델 버전·인덱스 재생성 절차를 처음부터 설계.

> 핵심: "DB가 커져서 느려지나?"가 아니라 "커질수록 관련 없는 결과가 섞이나?"가 본질. 답은 **선필터링 + recency/HW 필터 + 주기적 consolidation**.

## 리스크 / 평가

- **평가 데이터(gold set) 필수**: "이 버그는 패치 X의 회귀였다"가 확인된 과거 사례를 모아 precision/recall 측정. 없으면 "그럴듯하지만 틀린" 결과를 거를 수 없음 (특히 기능 B/C).
- **링킹 성공률**: Change-Id/Jira key 누락 패치 비율.
- **agentic ingestion 결정성**: 스키마 강제 + 검증으로 DB 일관성 확보.
- **오너십**: 유지보수 주체·운영 비용.
- **confidential 격리**: 실데이터는 사내 인프라에만, 외부 반출 차단.

## 핵심 미해결 / 확인 필요

- Hermes Agent를 confidential 환경에서 **외부 송신 완전 차단**하고 사내 LLM 엔드포인트로만 구성 가능한지 (보안 승인 포함).
- Gerrit/Jira용 MCP 서버 구축 또는 기존 커넥터 가용성.
- 사내 LLM 종류·성능·코드 이해 수준.
- Gerrit 이벤트 구독 권한 및 Jira API 접근 권한.
- 백필 대상 기간·규모.

## 변경 이력

- 2026-06-03: 최초 생성 (출처: 2026-06-03 설계 논의). 기능 A/B/C, 로드맵, on-prem 스택, agentic ingestion(Hermes) 방향 정리.
- 2026-06-03: Hermes Agent web 조사 반영 — Nous Research 오픈소스, Python+TS, MCP/native git 연동, provider-agnostic(사내 LLM 연결), cron·메모리·자동 skill, confidential 외부송신 차단 리스크 추가.
- 2026-06-03: 효용성 평가 + 확장성(대규모 코퍼스) 섹션 추가.
