---
title: "hermes-dashboard — Hermes 다중 에이전트 메신저 대시보드 (해커톤)"
domain: personal
sensitivity: public
tags: ["project", "hackathon", "hermes", "ai-agent", "nextjs", "mockup", "claude-code", "mvp"]
created: 2026-06-18
updated: 2026-06-21
sources:
  - "session-logs/20260617-220010-47ab-내가-해커톤-주제를-구체화-시키고-있는데-좀-도와줘.md"
  - "session-logs/20260618-063919-3962-지금-프로젝트-시작-지침서-인데-완성도를-평가해줘.md"
  - "session-logs/20260620-080358-9eaa-지금-프로젝트에-아래-요구사항을-추가할-수있는지-검토.md"
  - "session-logs/20260621-154117-e509-▎-..-HANDOFF.md-와-..-CLAUDE.md-읽고-Phase-2-UI부터-이어가.md"
confidence: high
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/patterns/mock-first-demo-safety-net.md"
  - "wiki/analyses/self-hosted-agent-webui-integration.md"
  - "wiki/analyses/build-vs-fork-personal-tool.md"
  - "wiki/patterns/esm-live-binding-global-state.md"
  - "wiki/patterns/blocked-dependency-productive-workflow.md"
  - "wiki/patterns/sqlite-readonly-data-swap.md"
  - "wiki/patterns/upstream-fork-minimal-invasion.md"
  - "wiki/projects/hermes.md"
---

# hermes-dashboard — Hermes 다중 에이전트 메신저 대시보드

사내 서버의 [[hermes-agent|Hermes]] 를 SSH 터미널이 아니라 **웹에서 메신저처럼** 쓰고, 도메인별 특화 에이전트 여러 개를 한 화면에서 관제하려는 1일 해커톤 프로젝트. 2026-06-17 밤 ~ 06-18 새벽에 걸쳐 **구현 전 기획·지침 문서(CLAUDE.md / TODO.md / 기획안 / mockup.html / SPEC.md)** 를 Claude Code 와 만들고, 완성도를 평가·정정한 기록. 코드 구현은 해커톤 당일에 진행 예정.

프로젝트명만 기재 (cwd 전체 경로는 schema 규칙상 생략). git remote: `min5859/idea.git`, 브랜치 `hackathon/hermes-crew` 계열.

> **2026-06-20 방향 전환**: 해커톤이 끝나고 **개인 실사용 도구**로 성격이 바뀌면서, 아래 v1/v2 의 "0부터 빌드" 계획을 폐기하고 **`JPeetz/Hermes-Studio`(MIT, TanStack Start + React) 포크** 로 전환했다. 자세한 판단 근거와 변경점은 바로 아래 절 참조. 이하 v1/v2 기록은 폐기된 설계로 보존한다.

## 2026-06-20 방향 전환 — 0부터 빌드 → Hermes Studio 포크

해커톤 종료 후 "발표용 차별점(메신저 UX)"이라는 전제가 사라지자 판단 기준이 **① 내가 원하는 동작 ② 유지보수 부담 최소화** 두 개로 바뀌었고, 이 기준에서는 처음부터 빌드보다 포크가 압도적으로 유리했다. Studio 가 만들려던 기능의 **~90%**(게이트웨이 capability 자동 감지, `/v1/runs` 위임 이벤트 SSE, tool 카드, 세션 관리, 칸반, cron, approvals)를 이미 구현했기 때문. 일반화한 의사결정 틀은 [[build-vs-fork-personal-tool]].

### 환경 검증 (Phase 0) — 가정과 달랐던 점

- 이 버전은 OpenAI 호환 API 서버(`:8642`)가 **꺼져 있고**(`API_SERVER_ENABLED` 미설정), 웹 API 를 `hermes dashboard`(기본 `:9119`)로 제공. 텔레그램은 outbound polling 이라 포트 없이 동작.
- dashboard 의 대부분 경로가 무인증 `curl` 에 200 을 주지만 본문은 **SPA `index.html`**(JSON 아님). 인증은 페이지 임베드 세션 토큰 필요, 임의 Bearer/Cookie 는 `Invalid HTTP request received`.
- **네이티브 Kanban 은 존재**(`hermes kanban` CLI + `/api/plugins/kanban/`, `~/.hermes/kanban.db` 전 프로필 공유) — v2 의 "Kanban API 없음 → 로컬 store 확정" 가정 정정. 위임도 `delegate_task` 로 네이티브 지원. 상세는 [[hermes-agent]].

### 포크 후 실제 작업량 = 메신저 UX 레이어

Studio 에 없는 부분(presence·unread·@mention·스레드)만 포크 위에 얹는 게 실작업. Slack 벤치마크로 ROI 우선순위 선별: **@mention 라우팅**(그룹방에서 `@리뷰봇` 직접 지목)·**unread 뱃지/알림**(텔레그램 알림과 중복 조율)이 최상위, 스레드·전역검색은 중간, pin/슬래시커맨드는 낮음. 끼울 지점: `chat-sidebar`(presence/unread)·`chat-composer`(@mention)·`message-item`(위임 말풍선)·`/chat/$sessionKey`(group)·`search-modal`(검색).

### Studio 코드에서 확인한 사실 (검증된 가정 정정)

- Studio 의 **칸반은 Hermes 네이티브가 아니다** — `task-store.ts` 가 `.runtime/tasks.json` 에 직접 저장하고 `/api/plugins/kanban/` 호출이 없음. 봇이 보드에서 작업을 claim 하는 진짜 협업을 원하면 Studio 보드를 네이티브 Kanban API 로 갈아끼워야 함.
- **Conductor = `delegate_task` 경로**(`conductor-spawn.ts` 가 `/api/jobs` 로 orchestrator spawn, `/v1/runs` SSE 프록시·`👥 Delegate Task` 카드 구현). 단 office-view 관제 UI 라 메신저 그룹방엔 부적합 → 그룹방은 chat 화면을 확장하기로.
- **Crews = fan-out 디스패치**(`$crewId.dispatch.ts` 가 멤버마다 `/api/send-stream` 개별 호출) — 위계 위임이 아닌 병렬 발송.
- Hermes 게이트웨이엔 **전역 이벤트 스트림이 없어** Studio 가 자체 `chat-event-bus` 로 중계(`/api/chat-events`, `Last-Event-ID` 재전송). 백엔드가 global push 를 안 줄 때 서버측 in-memory 이벤트 버스 + SSE 재배포 + `Last-Event-ID` 복구 패턴.

### PC 별 차등 표시 = 런타임 게이트웨이 디스커버리

집/회사 PC 마다 다른 에이전트가 보이게 하려면 하드코딩 배열(`config/agents.ts`)을 버리고 **런타임 디스커버리**로 전환. Studio 의 `probeGateway()`(`gateway-capabilities.ts`)가 부팅 시 후보 게이트웨이의 `/health`·`/v1/chat/completions`·`/v1/models`·`/api/{sessions,skills,memory,config,jobs}` 를 병렬 probe 해 **살아있는 기능만 켜고 120초 캐시**. "켜둔 게이트웨이만 잡힌다"는 특성 덕에 PC 별 차등 표시가 자연히 달성됨. ("프로필=게이트웨이 프로세스=전용 포트=전용 봇 토큰" 제약은 서버측 구조라 프론트가 못 바꿈 → 자주 쓰는 2~3개만 띄우고 `<프로필> gateway install` 로 부팅 자동 기동.)

### 방향 전환 시 잔재 코드 처리

from-scratch 잔재(`config/agents.ts` 하드코딩 8봇·`config/skills.ts`·`lib/types.ts`)는 임의 삭제 대신 **사용자 확인 후 `git mv` 로 `legacy/` 이동(이력 보존) + 보관 사유 README**. mockup.html 은 전면 재작도 대신 "역할 재정의 + 틀린 사실 수정"으로 처리하고 실제 재작도는 포크를 띄워 화면을 본 뒤로 미룸(추측 기반 재작업 회피). 문서 정합화는 stale 키워드(mock/Next.js/"Kanban API 없음"/HERMES_MODE)를 grep 전수 스캔으로 검증.

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

## 2026-06-21 Phase 2~6 구현 — 검증된 실무 패턴 4가지

포크(Hermes Studio) 위에 메신저 UX 레이어를 얹는 구현을 진행하면서, 프로젝트에 갇히지 않는 일반 패턴 4가지가 나왔다. 모델 백엔드가 Codex OAuth `refresh_token_reused`(다른 클라이언트가 토큰 소비)로 죽어 라이브 검증이 막힌 상황에서 진행했고, 그 제약이 오히려 좋은 패턴을 끌어냈다.

- **에이전트별 게이트웨이 전환 = ESM live binding.** 채팅 백엔드 ~24개 파일이 단일 게이트웨이 상수를 import → per-request 스레딩(24파일 수정) 대신 `export let` + setter 로 한 점에서 전환 + capability 재probe. 단일 사용자 전제. → [[esm-live-binding-global-state]]
- **블로커 중 생산성 최대화.** 모델 재인증(사용자 조치)이 블로커이고 "mock 금지·real 검증" 원칙상 블라인드 빌드 불가 → 보고 후 idle 대신 파서/순수함수+단위테스트/readonly DB/컴파일 검증을 선완결. 위임 이벤트 파서는 **핸드오프 문서(`response.output_item.added`)가 틀리고 동작 코드(`tool.started/completed`)가 옳음**을 라이브 없이 발견, 테스트로 고정해 라이브 후 안전 교정. → [[blocked-dependency-productive-workflow]]
- **REST 프록시 대신 SQLite readonly 직접 읽기.** 미기동인 dashboard REST 대신 프로필 `kanban.db`/`state.db`(FTS5)를 `better-sqlite3` readonly 로 직접 읽어 데이터 소스 스왑(UI 계약 유지). `createRequire` 로 네이티브 애드온 로드, `created_at` 초→ms 변환 함정. → [[sqlite-readonly-data-swap]]
- **업스트림 포크 최소 침습.** Studio 원본은 import 1줄+주입 2줄 수준의 최소 hook 만, 메신저 기능은 신규 파일·별도 커밋, 머지 절차는 `UPSTREAM-MERGE.md`, 잔재는 `legacy/` 이동. → [[upstream-fork-minimal-invasion]]

> 모델 백엔드 장애 자체는 이 프로젝트 고유 버그가 아니라 [[oauth-refresh-token-rotation-multi-client]] 의 동일 패턴(Codex OAuth 토큰 회전 쟁탈)이다.

## 변경 이력

- 2026-06-18: 최초 생성 — 2026-06-17 밤 기획(HermesTalk v1) + 06-18 새벽 완성도 평가/정정(Hermes Crew v2) 세션에서 정리. Hermes API 연동 검증 사실은 [[hermes-agent]], mock-first 안전망은 [[mock-first-demo-safety-net]], webui vs workspace 연동 비교는 [[self-hosted-agent-webui-integration]] 로 분리 (출처: session-logs/20260617-220010-47ab-*, 20260618-063919-3962-*)
- 2026-06-20: **방향 전환 — 0부터 빌드 폐기, Hermes Studio(`JPeetz/Hermes-Studio`) 포크 채택**. 개인 실사용 도구로 성격 변화 → 차별점 전제 소멸 → 90% 겹치는 OSS 포크. Phase 0 환경 검증(dashboard `:9119`·SPA fallback 200·세션 토큰 인증·네이티브 Kanban 존재로 v2 가정 정정), 실작업=메신저 UX 레이어(@mention·unread, Slack ROI 선별), Studio 코드 검증(Conductor=delegate_task / Crews=fan-out / 자체 task-store / chat-event-bus), PC 별 차등 표시=`probeGateway` 런타임 디스커버리, 잔재 코드 `legacy/` 이동. 일반화 의사결정 틀은 [[build-vs-fork-personal-tool]], Hermes 측 사실은 [[hermes-agent]] 갱신 (출처: session-logs/20260620-080358-9eaa-*)
- 2026-06-21: Phase 2~6 구현 회고 + 일반 패턴 4건 분리. 모델 백엔드 OAuth 장애([[oauth-refresh-token-rotation-multi-client]])로 라이브가 막힌 채 진행. ESM live binding 게이트웨이 전환([[esm-live-binding-global-state]]), 블로커 중 파서 선구현·동작 코드 우선([[blocked-dependency-productive-workflow]]), SQLite readonly 데이터 스왑([[sqlite-readonly-data-swap]]), 업스트림 포크 최소 침습([[upstream-fork-minimal-invasion]]) (출처: session-logs/20260621-154117-e509-*)
