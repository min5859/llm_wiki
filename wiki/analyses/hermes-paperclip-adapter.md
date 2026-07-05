---
title: "hermes-paperclip-adapter — Paperclip 에 Hermes Agent 를 붙이는 런타임 어댑터"
domain: "ai-agent"
sensitivity: public
tags: ["analysis", "ai-agent", "hermes", "paperclip", "adapter", "orchestration", "workflow"]
created: 2026-07-04
updated: 2026-07-05
sources:
  - "https://github.com/NousResearch/hermes-paperclip-adapter/tree/937ea71a34f5efcaa3834b11fdd08cfc1c99cb2c"
  - "https://www.npmjs.com/package/hermes-paperclip-adapter/v/0.3.0"
  - "session-logs/20260704-132738-e509-지금-세션에서-작업했던-hermes-webui-설치가-pc-를-껏다켜니-접속이-안되네-다시.md"
confidence: high
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/self-hosted-agent-webui-integration.md"
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
  - "wiki/projects/hermes.md"
---

# hermes-paperclip-adapter

`hermes-paperclip-adapter` 는 논문 스크래퍼나 문서 수집기가 아니다. **Paperclip 의 이슈/heartbeat 시스템에서 Hermes Agent 를 자동 실행하기 위한 TypeScript 런타임 어댑터**다. 감각적으로는 "AI 개발자(Hermes)를 Jira/GitHub Issues 같은 작업판(Paperclip)에 붙이는 연결 장치"에 가깝다.

## 정체

구성 요소를 역할로 나누면 이해가 쉽다.

| 구성 | 역할 |
|---|---|
| Hermes Agent | 실제로 작업하는 self-hosted AI agent. 터미널, 파일, 웹, 브라우저, MCP, 스킬 등을 사용 |
| Paperclip | 이슈/작업/heartbeat 를 관리하는 오케스트레이션 시스템 |
| hermes-paperclip-adapter | Paperclip 작업을 Hermes CLI 호출로 바꾸고, Hermes 결과를 Paperclip 결과로 되돌리는 통역기 |

흐름은 다음과 같다.

```text
Paperclip 이슈/heartbeat
  → adapter 가 task/comment/context 로 프롬프트 생성
  → hermes chat -q "<prompt>" 실행
  → Hermes 가 파일 수정·터미널 실행·웹 작업 등 수행
  → adapter 가 stdout/stderr/session/cost/usage 를 파싱
  → Paperclip 에 결과, 로그, sessionParams 반환
```

## 아닌 것

- 논문 PDF 를 긁어오는 scraper 가 아니다.
- 웹페이지를 자동 수집해 요약하는 단독 앱이 아니다.
- Hermes 자체를 더 똑똑하게 만드는 모델/플러그인이 아니다.
- Paperclip 없이 단독 설치해서 쓰는 CLI 도구가 아니다.

이름의 `Paperclip` 은 문서 클립핑 기능이 아니라 **작업 오케스트레이션 플랫폼 이름**이다.

## 실제 구현 포인트

핵심 파일은 `src/server/execute.ts` 다. 이 파일이 Paperclip 실행 컨텍스트를 받아 다음을 수행한다.

- task/comment 정보로 기본 프롬프트 렌더링
- `hermes chat -q ... -Q` 실행
- 모델과 provider 결정: adapter config → `~/.hermes/config.yaml` → 모델 prefix 추론 → `auto`
- `--resume` 으로 이전 Hermes session 이어가기
- `--source tool` 로 Hermes interactive history 와 구분
- stdout/stderr 에서 `session_id`, usage, cost, summary 추출
- Paperclip 이 다음 heartbeat 에 쓸 `sessionParams.sessionId` 저장

스킬 통합(`src/server/skills.ts`)은 Hermes 에 스킬을 새로 설치한다기보다, Paperclip 관리 스킬과 `~/.hermes/skills` 를 스캔해 UI 에 하나의 snapshot 으로 보여주는 쪽에 가깝다.

## 내 PC 에 적용하려면

이 어댑터만 설치해서는 체감 기능이 없다. 필요한 구성은 아래처럼 묶인다.

```text
local machine
├── Paperclip server
├── Hermes Agent
├── hermes-paperclip-adapter
└── LLM API key / Hermes provider config
```

대략적인 적용 순서:

1. Hermes Agent 설치 및 provider/API key 설정
2. Paperclip 서버 설치 또는 실행
3. Paperclip adapter registry 에 `hermes_local` 로 이 패키지 등록 — **단, 패키지판에는 불필요 (아래 2026-07-05 정정)**
4. Paperclip 에 Hermes agent 생성
5. 이슈를 배정하거나 heartbeat 로 깨워 작업 처리

Paperclip 을 운영하지 않을 거라면, 이 어댑터보다 Hermes CLI/API 를 직접 쓰는 편이 단순하다.

### 정정 — 패키지판 Paperclip 은 hermes 어댑터를 내장한다 (2026-07-05 실측)

`npx paperclipai@latest onboard` 로 설치한 **패키지판 Paperclip 은 `hermes_local`·`hermes_gateway` 어댑터를 builtin 으로 이미 내장**한다 (그 외 codex_local·claude_local·acpx_local·cursor·gemini_local 등 총 14종). 즉 소스 `registry.ts` 수정이 필요한 이 npm 패키지는 **패키지판 배포에서는 불필요**하고, 소스 빌드에서 어댑터를 직접 등록할 때만 의미가 있다. 패키지판 Paperclip 은 embedded-postgres + loopback `:3100` 으로 뜨고, 데이터는 `~/.paperclip/instances/default/` 에 저장된다.

## 장점

이 어댑터의 장점은 "채팅형 AI" 를 "작업 큐를 처리하는 AI 직원" 형태로 바꾸는 데 있다.

- 사용자가 매번 채팅으로 지시하지 않고, Paperclip 이슈 단위로 작업을 맡길 수 있다.
- Hermes 세션이 heartbeat 사이에 이어져 장기 작업 컨텍스트를 유지한다.
- 작업 로그, 결과, 비용, 토큰 사용량이 Paperclip 쪽에 남는다.
- 여러 agent 를 역할별로 Paperclip 에 등록해 운영할 수 있다.
- Hermes 의 terminal/file/web/browser/MCP/skills 능력을 Paperclip workflow 안에서 활용한다.

적합한 사용처:

- 이슈 기반 코드 수정 자동화
- 반복 운영 작업을 AI agent 에게 할당
- 여러 agent 의 작업 상태를 중앙 작업판에서 보고 싶은 경우
- self-hosted Hermes 를 팀/개인 작업관리 흐름에 붙이고 싶은 경우

## 리스크와 주의점

가장 큰 운영 리스크는 `--yolo` 다. 어댑터는 비대화형 subprocess 실행에서 승인 프롬프트가 막히지 않도록 Hermes 에 `--yolo` 를 붙인다. 따라서 Hermes 가 실행되는 환경은 반드시 제한되어야 한다.

- 별도 작업 디렉터리, 제한 계정, Docker/VM 등 sandbox 가 필요하다.
- 개인 홈 전체 권한으로 돌리면 파일 수정/명령 실행 범위가 넓어진다.
- Paperclip/Hermes/API key 를 한 머신에 묶으므로 secret 관리가 중요하다.

코드 품질 관점의 주의점:

- 저장소에 자동 테스트가 없다. 확인 가능한 검증은 TypeScript build/typecheck 중심이다.
- README 의 일부 표현은 실제 구현보다 넓다. 예를 들어 "ASCII banner/table 을 GFM 으로 변환"하는 rich post-processing 은 코드에서 명확히 보이지 않는다.
- GitHub tag 는 `v0.1.1` 까지만 보이지만 npm 은 `0.3.0` 이 배포되어 있어 릴리스 태깅이 깔끔하지 않다.
- Hermes config YAML 파싱은 정식 YAML parser 가 아니라 단순 regex/indent 기반이다.

## 판단 기준

| 원하는 것 | 이 어댑터 적합도 |
|---|---|
| Paperclip 에 이슈를 쌓고 AI agent 가 자동 처리 | 높음 |
| Hermes 를 장기 세션 기반 worker 로 운영 | 높음 |
| 여러 AI 직원을 작업판에서 관리 | 높음 |
| 논문/PDF 수집·요약 | 낮음 |
| 로컬 코드 한 번 수정 | 낮음. Hermes/Codex/Claude CLI 직접 사용이 간단 |
| 개인 챗봇 또는 웹 UI | 낮음. Hermes API/WebUI 쪽이 더 적합 |

## 검증 메모

2026-07-04 기준 main commit `937ea71` 을 로컬 클론해 확인했다.

- `package.json`: `hermes-paperclip-adapter@0.3.0`, Node `>=20`
- 런타임 의존성: `@paperclipai/adapter-utils`, `picocolors`
- `npm install`: 성공
- `npm run typecheck`: 성공
- `npm run build`: 성공
- `npm audit --omit=dev`: 취약점 0개

## 관련 맥락

- [[hermes-agent]] — Hermes 본체의 self-hosted agent 개념, API 서버, 멀티 프로필
- [[self-hosted-agent-webui-integration]] — Hermes 류 agent 에 UI 를 붙일 때 내부 직결 vs HTTP API 방식
- [[multi-llm-provider-adapter-pattern]] — 외부 런타임/CLI 를 어댑터 경계 뒤에 두는 일반 패턴

## 변경 이력

- 2026-07-04: 최초 생성 — NousResearch/hermes-paperclip-adapter 코드 구조, 적용 조건, 장점, `--yolo` 운영 리스크, Paperclip 없이 단독 사용 가치 낮음을 정리.
- 2026-07-05: 패키지판 Paperclip(`npx paperclipai onboard`)은 hermes_local/hermes_gateway 어댑터 builtin 내장(14종) → 이 npm 패키지는 소스 빌드 전용이라는 실측 정정 추가. embedded-postgres + `:3100`, 데이터 경로 명시 (출처: session-logs/20260704-132738-e509-*)
