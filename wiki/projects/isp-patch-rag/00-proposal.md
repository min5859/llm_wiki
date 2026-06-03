---
title: "[기획안] ISP Driver 패치 지식 RAG 시스템 — 이슈 초도 분석 자동화"
domain: "work"
sensitivity: "internal"
tags: ["proposal", "presentation", "rag", "gerrit", "jira", "isp-driver", "hermes-agent", "claude-cli"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "design-discussion: 2026-06-03 Claude Code 세션"
confidence: "medium"
related:
  - "wiki/projects/isp-patch-knowledge-rag-system.md"
  - "wiki/projects/isp-patch-rag/common-architecture.md"
---

# ISP Driver 패치 지식 RAG 시스템 — 기획안 (발표용)

> 발표 슬라이드 변환용. 각 `---` 구분선이 1슬라이드 단위. Marp/Slidev로 변환 가능.
> 상세 설계는 `common-architecture.md` 및 `version-a-* / version-b-*` 설계문서 참조.

---

## 1. 한 줄 요약

**머지되는 모든 패치(diff)와 Jira 리뷰 티켓을 사내 지식 DB로 축적하고, 새 이슈가 나오면 AI가 "유사 과거 이슈·회귀 후보·재현 방법"을 초도 분석으로 제시한다.**

---

## 2. 문제 (현황)

- 하루 ~10건 패치 머지(연 ~2,500건). 이슈 해결·기능 구현 지식이 **개인과 Jira 티켓에 흩어져 휘발**.
- 비슷한 이슈가 반복돼도 **과거 해결 이력을 빠르게 못 찾음** → fault localization에 시간 과다.
- 인력 이동 시 **tribal knowledge 손실**. 최근 머지 패치의 side effect를 사람 기억에 의존해 추적.

---

## 3. 해결 아이디어

```
Gerrit merge ──┐
               ├─→ 지식 DB(patch+ticket+구조메타+벡터) ──→ AI 초도 분석
Jira ticket  ──┘                                            ├─ 유사 이슈 검색
                                                            ├─ 회귀/side-effect 후보
                                                            └─ 재현 TC 제안
```

- 머지 시점마다 자동 수집 → 검색 가능한 institutional memory.
- 새 이슈 입력 시 advisory(보조) 초도 분석 제공. **사람을 대체하지 않고 옆에서 돕는다.**

---

## 4. 기대 효과 (효용성)

- **확실(높음)**: 링크된 patch+ticket 검색 DB만으로도 가치. 코퍼스가 쌓일수록 **복리로** 가치 증가. 수집 비용 거의 0.
- **조건부(중간)**: 회귀/side-effect 탐지는 맞으면 가장 비싼 문제를 잡지만 검증(gold set) 전엔 advisory 힌트 수준.
- **핵심 변수**: Jira description 품질이 ROI를 좌우.

---

## 5. 아키텍처 개요 — 2-컴포넌트

```
[컴포넌트 1] 수집 (DB 빌더) ─────write──┐
   (백그라운드/배치, 버전별 분기)         │
                                         ├── Postgres + pgvector (공유 DB)
[컴포넌트 2] 이슈 분석 Web App ──read────┘
   (버전 공통, claude -p, 인터랙티브)
```

- **저장**: Postgres + pgvector (구조 메타 + 임베딩 단일 저장).
- **구조 추출(결정적·SW)**: tree-sitter(C) + ctags로 파일/함수/register 매핑.
- **AI(비결정적)**: 요약·증상추출·답변합성. AI 호출은 추상화되어 엔드포인트 교체 가능.
- **검색**: 메타 선필터 → 벡터 검색 → rerank (하이브리드).
- 공유 자원은 DB 하나뿐(분석=read, 수집=write) → 두 컴포넌트 독립 운영·확장.

---

## 6. 두 가지 구현 방식 — 차이는 "수집"뿐

분석 web app(컴포넌트 2)은 **버전 공통**이며 AI는 `claude -p`로 통일. **버전 차이는 수집 컴포넌트(컴포넌트 1)에만** 존재.

| 구분 | 버전 A — 수집 SW | 버전 B — 수집 Hermes |
|---|---|---|
| 수집 골격 | 자체 Python cron/이벤트 서비스 | Hermes Agent 오케스트레이션 |
| 수집 AI | `claude -p` | Hermes provider-agnostic(사내 LLM) |
| 결정적 처리 | SW 구현 | **SW 구현(MCP 도구로 노출)** |
| 분석 컴포넌트 | 공통(claude -p) | 공통(claude -p) — A와 동일 |
| 장점 / 비고 | 제어·재현성↑, 유지보수 부담 | 구축 빠름·유연, 신생 OSS 안정성 |

> 버전 B에서 Hermes로 전부 되지 않는 부분(결정적 DB upsert·구조파싱·임베딩·랭킹)은 SW로 구현해 MCP 도구로 Hermes에 제공.

---

## 7. 로드맵

- **Phase 0 — PoC**: 과거 N개월 백필, 링킹 성공률 측정, 수집방식 비교.
- **Phase 1 — MVP**: 하이브리드 검색 + CLI "유사 이슈"(기능 A).
- **Phase 2 — 운영화**: 실시간 머지 ingest, Gerrit/Jira 봇 코멘트.
- **Phase 3 — 확장**: blast-radius 기반 side-effect 후보 탐지(기능 B).
- **Phase 4 — 가이드**: 회귀 원복·패치 무력화 경고, 재현 TC 제안(기능 C).

---

## 8. 보안 전제 (선결 — 가장 중요)

- ISP driver 코드·티켓은 **confidential**. 어떤 경로로도 **무단 외부 송신 금지**.
- **버전 A `claude -p` 주의**: 기본값은 Anthropic API(외부)로 송신됨. → **사내 승인 엔드포인트**(사내 Claude 게이트웨이 / 격리 VPC Bedrock / zero-retention 엔터프라이즈)로만 라우팅하는 것이 **도입 선결조건**.
- **버전 B Hermes 주의**: 기본 통합(Nous Portal·OpenRouter)은 외부. → 외부 엔드포인트·커넥터 차단, 사내 LLM 엔드포인트로만 구성.
- 실데이터는 사내 인프라에만 존재. 본 기획·설계 문서에는 실제 코드·수치 미포함.

---

## 9. 리스크 & 성공 조건

- **성공 조건**: ① Jira description 품질/일관성 ② 링킹 성공률 ③ gold set 기반 정밀도 검증 ④ advisory 포지셔닝.
- **리스크**: AI 비결정성 → 스키마 강제+검증, false positive → 신뢰 붕괴 방지(advisory), 코퍼스 노후화 → recency/HW필터+consolidation.

---

## 10. 요청 사항 (Ask)

- 사내 LLM 엔드포인트 / `claude -p` 승인 채널 확정.
- Gerrit 이벤트 구독 + Jira API 접근 권한.
- 백필 대상 기간·규모 합의, PoC용 GPU/스토리지.
- gold set 구축 협조(과거 "회귀로 판명된" 사례).

## 변경 이력

- 2026-06-03: 최초 생성 (발표용 기획안).
