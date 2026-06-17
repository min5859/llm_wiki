---
title: "hermes-dashboard — Hermes 다중 에이전트 메신저 대시보드 (해커톤)"
domain: personal
sensitivity: public
tags: ["project", "hackathon", "hermes", "ai-agent", "nextjs", "mockup", "claude-code", "mvp"]
created: 2026-06-18
updated: 2026-06-18
sources:
  - "session-logs/20260617-220010-47ab-내가-해커톤-주제를-구체화-시키고-있는데-좀-도와줘.md"
  - "session-logs/20260618-063919-3962-지금-프로젝트-시작-지침서-인데-완성도를-평가해줘.md"
confidence: high
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/patterns/mock-first-demo-safety-net.md"
  - "wiki/analyses/self-hosted-agent-webui-integration.md"
  - "wiki/projects/hermes.md"
---

# hermes-dashboard — Hermes 다중 에이전트 메신저 대시보드

사내 서버의 [[hermes-agent|Hermes]] 를 SSH 터미널이 아니라 **웹에서 메신저처럼** 쓰고, 도메인별 특화 에이전트 여러 개를 한 화면에서 관제하려는 1일 해커톤 프로젝트. 2026-06-17 밤 ~ 06-18 새벽에 걸쳐 **구현 전 기획·지침 문서(CLAUDE.md / TODO.md / 기획안 / mockup.html / SPEC.md)** 를 Claude Code 와 만들고, 완성도를 평가·정정한 기록. 코드 구현은 해커톤 당일에 진행 예정.

프로젝트명만 기재 (cwd 전체 경로는 schema 규칙상 생략). git remote: `min5859/idea.git`, 브랜치 `hackathon/hermes-crew` 계열.

## 문제와 차별점

- **문제**: 사내 서버 Hermes 를 윈도우 PC → SSH → `hermes` 커맨드 → 채팅으로만 써야 해 불편. 메신저앱(Telegram 등)은 사내망에서 못 씀.
- **이미 있는 것**: 웹/모바일 채팅은 `hermes-webui`(단일 에이전트 1:1), 멀티+Kanban 은 `hermes-workspace`(Studio) 가 이미 제공.
- **빈 곳(해커톤 핵심)**: "여러 도메인 특화 Hermes 에이전트를 카톡 채팅방 목록처럼 관리 + 그룹방에서 리더가 위임·취합" 하는 **메신저 UX**. 차별점은 *멀티 에이전트 자체*가 아니라 **"Kanban 이 아닌 메신저 UX"** — workspace 가 이미 멀티를 하므로 이 방어선이 중요.

## 설계 진화 — HermesTalk(v1) → Hermes Crew(v2)

| | v1 (HermesTalk) | v2 (Hermes Crew) |
|---|---|---|
| 평가 점수 | 90/100 | 88/100 (구조·정직성 ↑) |
| 스택 | Next.js + TS + Tailwind + zustand | 바닐라 JS + FastAPI (경량화) |
| 킬러 기능 | 그룹방 오케스트레이션 | **주간보고 자동작성** |
| 오케스트레이션 | Must (데모 핵심) | **Phase 5 스트레치로 강등** |
| 문서 | 기획안/CLAUDE/TODO + config/types | + SPEC.md(인터페이스 계약)/START.md(복붙 프롬프트)/PROPOSAL.md |

v2 의 **가장 잘한 결정은 리스크 재배치**다. "오케스트레이션은 Hermes 네이티브인데 API 경유 동작이 미검증"이라는 약점을 그대로 반영해, 검증 안 된 기능을 스트레치로 내리고 **검증된 API 표면(chat completions + 세션)으로 달성 가능한 주간보고를 킬러로 교체**했다. 불확실한 걸 자신 있게 그리지 않는 판단.

## Hermes 연동의 검증된 제약 (소스로 확인)

문서 초안의 "real 연동" 가정 3개가 **Hermes 소스(`gateway/platforms/api_server.py`)·공식문서 확인 결과 틀렸고**, 정정이 설계를 바꿨다. 상세 사실은 [[hermes-agent]] 의 "내장 API 서버" 절. 요지:

1. **프로필 = 게이트웨이 = 포트.** `chat/completions` 의 `model` 필드는 장식용 → 요청별 프로필 선택 불가. 8개 특화 에이전트를 다 real 로 띄우려면 8개 게이트웨이 = 비현실적 → **현실적 real 데모 = 1~2개만 real, 나머지 mock.**
2. **위임/스트리밍은 Chat Completions 가 아니라 Runs API** (`POST /v1/runs` → `GET /v1/runs/{id}/events` SSE). 어댑터를 Runs API 기준으로 설계.
3. **스킬 API 있음(`GET /v1/skills`) / Kanban API 없음** → 스킬 보드는 real, 칸반은 로컬 store 전용 확정.
4. **주간보고 입력원**: 프로필별 메모리는 독립이라 report 프로필이 남의 세션을 못 읽음 → **백엔드가 `hermes -p <p> sessions export`(JSONL, 세션 ID `YYYYMMDD` prefix 로 주 단위 필터)를 집계해 프롬프트에 주입**하는 방식으로 교차 집계를 백엔드 책임으로 해결.

또 스트리밍에 `EventSource`(GET 전용)는 틀림 — 채팅은 body 있는 POST 라 `fetch` + `ReadableStream` 으로 읽어야 한다.

## 확장성 — 어댑터 경계가 핵심 이음새

`adapter.ts` 의 `chat(agentId, messages): AsyncIterable<HermesEvent>` 가 UI 와 백엔드를 분리한다. 덕분에 mock→real, 나아가 "HermesTalk 가 직접 지휘하는 자체 오케스트레이터"까지 **같은 `HermesEvent`(token/delegation/done)만 내보내면 UI 를 안 고치고 어댑터만 교체**하면 된다. mock→real 전환도 `isLive()` 로 이미 이 패턴. (단 정규 경로는 자체 오케스트레이터가 아니라 Hermes 네이티브 위임에 맡기는 것)

나중에 손볼 곳: `HermesEvent.delegation` 이 `{agent,status}` 로 평면적 → 위임 트리/병렬/`runId`/`parentId` 표현 불가(다단계 오케스트레이션 시 첫 리팩터 지점). `AgentId` 하드코딩 union → 동적 등록 불가(DB 없는 의도된 단순화). `token` 에 출처 `agent?` 필드 없음 → 여러 봇 동시 발화 시 추가 필요.

## 핵심 패턴 — mock-first 안전망

1일 해커톤의 최대 안전망은 **`HERMES_MODE=mock` 기본값 + 모든 외부의존 함정에 "안 되면 mock 으로 완결" 폴백**. `config/agents.ts` 의 모든 에이전트가 기본 `live:false` 라 클론 직후 환경값 0개로 mock 데모가 돈다. 일반 패턴은 [[mock-first-demo-safety-net]].

해커톤 당일 함정 3개(문서에 폴백 내장): ① `create-next-app` 이 비지 않은 폴더에서 거부 → 임시 스캐폴드 후 병합, ② 해커톤 망에서 npm registry 접근 불가 가능 → mock UI 는 가능하나 셋업 막힘, ③ 사내 Hermes 게이트웨이(:8642) 도달성 → 안 되면 mock 데모로 완결.

## 폴더 구성 (구현 착수 상태)

```
hermes-dashboard/
├── 기획안.md / PROPOSAL.md   # 발표용
├── CLAUDE.md                 # 작업 지침 (자동 로드)
├── TODO.md                   # Phase 0~7 체크리스트
├── SPEC.md                   # 인터페이스 계약 (v2)
├── START.md / prompt.md      # 복붙용 첫 프롬프트
├── mockup.html               # UI·mock 데이터 정본 (의존성 0)
├── .env.example              # 환경변수 계약 (채울 빈칸)
├── config/{agents,skills}.ts # 에이전트·스킬 (live/baseUrl)
└── lib/types.ts              # 공통 타입
```

CLAUDE.md 가 "TODO 를 Phase 0 부터, mockup.html 이 UI 정본, mock 먼저"를 못박아, 폴더 열고 첫 프롬프트만 붙이면 착수되도록 설계.

## 변경 이력

- 2026-06-18: 최초 생성 — 2026-06-17 밤 기획(HermesTalk v1) + 06-18 새벽 완성도 평가/정정(Hermes Crew v2) 세션에서 정리. Hermes API 연동 검증 사실은 [[hermes-agent]], mock-first 안전망은 [[mock-first-demo-safety-net]], webui vs workspace 연동 비교는 [[self-hosted-agent-webui-integration]] 로 분리 (출처: session-logs/20260617-220010-47ab-*, 20260618-063919-3962-*)
