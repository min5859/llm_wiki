---
title: "Claude Code 의 scheduled-tasks — 동작 모델·생성 절차·운영 함정"
domain: both
sensitivity: public
tags: ["claude-code", "scheduled-tasks", "skills", "websearch", "automation", "skill-creator"]
created: 2026-05-05
updated: 2026-05-05
sources:
  - "session-logs/20260505-103341-adb1-*.md"
  - "session-logs/20260505-104124-ad05-*.md"
confidence: medium
related:
  - "wiki/patterns/claude-code-loop-automation.md"
  - "wiki/patterns/claude-code-advanced.md"
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/patterns/vercel-cron-best-practices.md"
---

# Claude Code 의 scheduled-tasks — 동작 모델·생성 절차·운영 함정

Claude Code 데스크톱 앱이 주기 (예: 10분) 마다 자동으로 실행해 주는 작업. `~/.claude/scheduled-tasks/<task-name>/SKILL.md` 에 정의된 prompt 를 정해진 cadence 로 새 세션처럼 실행시키며, 결과 파일을 디스크에 떨어뜨리는 식의 무인 자동화에 적합하다. 비슷해 보이는 `/loop` 와는 동작 주체와 수명이 다르다.

## 핵심 내용

### `/loop` 와의 차이 — 무엇이 누구를 돌리는가

| 항목 | `/loop` ([[claude-code-loop-automation]]) | scheduled-tasks |
|---|---|---|
| 동작 주체 | 현재 세션 | Claude Code 앱 자체의 스케줄러 |
| 등록 위치 | 세션 안 명령 | `~/.claude/scheduled-tasks/<name>/SKILL.md` 파일 |
| 수명 | 세션 종료 시 같이 종료 | 세션 닫혀도 유지, **앱 종료 시에만** 정지 |
| 다음 실행 시점 | 세션이 자가 결정 (또는 cron 인자) | 등록 시 정한 interval 고정 |
| 실행 컨텍스트 | 사용자가 함께 있다고 가정 | "사용자 부재" 가정 — 자동 실행 prompt 가 자율 동작을 명시 |
| 사이드바 표시 | "Tasks" / 진행 중 세션 | "Scheduled" 탭 (Claude Code 버전에 따라 미노출) |

같은 일을 매일 자동으로 돌리고 싶으면 scheduled-tasks, 같은 세션 안에서 N 회 반복하고 싶으면 `/loop`. 둘은 대체재가 아니다.

### 자동 실행 시 prompt 가 받는 컨텍스트

스케줄러가 호출하면 모델은 다음과 같은 system prompt 형태로 깨워진다:

```
<scheduled-task name="news-digest-10min" file="/Users/.../scheduled-tasks/news-digest-10min/SKILL.md">
This is an automated run of a scheduled task. The user is not present to answer
questions. For implementation details, execute autonomously without asking
clarifying questions — make reasonable choices and note them in your output.
"write" actions (e.g. MCP tools that send, post, create, update, or delete),
only take them if the task file asks for that specific action. When in doubt,
producing a report of what you found is the correct output.

(여기서부터 SKILL.md 의 본문)
</scheduled-task>
```

핵심:

- **사용자 부재** 가 명시 → 질문 던지지 말고 합리적 선택을 하라는 지시.
- **write 액션 (send/post/create/update/delete) 은 task file 이 명시적으로 요구할 때만**.
- 의심스러우면 "발견한 것을 리포트로 출력" 하라는 안전 기본값.

따라서 SKILL.md 에는 모호한 표현 대신 **저장 경로·파일명 패턴·중복 시 처리·실패 시 출력 형식** 같은 결정 규칙을 정확히 박아 둬야 한다.

### 만드는 절차 (skill-creator + 직접 작성)

1. `/skill-creator` 등으로 빈 스킬 디렉터리를 만들거나 직접 만든다.
2. `~/.claude/scheduled-tasks/<task-name>/SKILL.md` 에 자동 실행 prompt 를 작성.
3. cadence (예: 10분) 는 Claude Code 앱이 등록 시점에 부여 — 사이드바 "Scheduled" 또는 등록 dialog 에서 설정.
4. 첫 자동 실행 전에 **수동 "Run now" 한 번**을 권장 — `WebSearch`/`Bash`/`Write` 같이 권한 prompt 가 있는 도구를 미리 승인해 둬야 자동 실행이 권한 다이얼로그에서 멈추지 않는다.

### SKILL.md 의 좋은 형식 — 결정 가능한 prompt

```
WebSearch 로 최신 주요 뉴스를 수집하고 한국어 요약 markdown 파일로 저장하라.

## 실행 절차

### 1. 뉴스 검색
다음 두 쿼리를 동시에 WebSearch 로 실행한다:
- "breaking news today"
- "오늘 주요 뉴스"

각각 3~4개씩 총 5~7개 기사를 선별한다. 중복·광고·오피니언 기사는 제외한다.

### 2. 저장 경로
- 디렉토리: /Users/.../news/
- 파일명: YYYY-MM-DD_HH-MM.md
- 폴더 없으면 먼저 생성한다
- 같은 파일명이 이미 존재하면 _2, _3 접미사를 붙인다

### 3. 파일 형식 (정확히 준수)
# YYYY-MM-DD HH:MM 뉴스
- **제목(한국어, 30자 이내)** 핵심 요약 한 문장(50자 이내). `출처도메인`

### 4. 완료 후
저장된 파일 경로와 뉴스 개수를 한 줄로 출력한다:
저장 완료: news/2026-05-05_14-30.md (6개 뉴스)

## 제약 사항
- 파일 하나 목표 크기: 1KB 미만
- 출처는 도메인만 (프로토콜·경로 제외)
```

좋은 점:

- **저장 경로/파일명 패턴**이 정확. 동시 실행으로 충돌 가능한 경우의 처리 (`_2`, `_3` 접미사) 도 명시.
- **출력 형식**을 예시로 박음 — 모델이 매 회 다른 형식을 만들지 않게 함.
- **크기 제약 (1KB 미만)** 으로 디스크 누적 폭주 방지.
- **완료 신호 한 줄** 로 사이드바 / 후속 자동화가 success 판정 가능.

### 운영 함정

- **앱이 켜져 있어야 한다** — Claude Code 데스크톱 앱이 종료되면 스케줄러도 함께 멈춘다. 새 세션을 만들거나 현재 세션을 닫아도 스케줄러는 살아 있지만, 앱 자체를 quit 하면 즉시 정지. macOS LaunchAgent 같이 시스템 레벨에서 catchup 하지 않는다 ([[macos-launchagent-catchup-behavior]] 와 비교).
- **사이드바 "Scheduled" 탭이 모든 버전에서 보이지 않는다** — 사용자에 따라 메뉴가 없을 수 있음. 그래도 동작은 한다 (`~/.claude/scheduled-tasks/<name>/SKILL.md` + 등록 정보로 관리). UI 가 없으면 결과 파일 (예: `news/YYYY-MM-DD_HH-MM.md`) 의 mtime 으로 정상 동작 검증.
- **권한 다이얼로그가 자동 실행을 멈춘다** — `WebSearch`, `Write`, `Bash` 같은 도구는 첫 사용 시 사용자 승인이 필요. 자동 실행 중에 권한 prompt 가 뜨면 사용자 부재 상태에서 작업이 그대로 정지. 등록 직후 **수동 "Run now"** 로 모든 도구를 한 번 통과시켜 권한을 미리 승인해 두는 것이 표준.
- **결과가 안 보이면 mtime 으로 검증** — 사이드바 없는 버전에서는 결과 파일 디렉터리를 `ls -la --time-style=long-iso` 같은 형태로 보고 새 파일이 매 cadence 마다 생기는지로 동작 확인.
- **"중지" 가 명시적이지 않다** — 사이드바 없는 버전에서는 disable 버튼이 안 보일 수 있음. 정지하려면 `~/.claude/scheduled-tasks/<task-name>/` 디렉터리를 직접 제거 (또는 SKILL.md 를 비워 noop 으로) 하는 것이 가장 확실.
- **중복 호출 (idempotency)** — Vercel Cron 과 마찬가지로 ([[vercel-cron-best-practices]] §4) 같은 cadence 안에 두 번 실행될 수 있다. write 액션이 누적되는 작업이라면 guard 패턴 필수.

### 비교 — 다른 자동화 수단과의 trade-off

| 수단 | 강점 | 약점 |
|---|---|---|
| Claude Code scheduled-tasks | LLM 추론 + 도구 사용 결합. 실시간 의사결정 가능 | 데스크톱 앱 살아 있어야 함. macOS / 단일 머신 의존 |
| `/loop` | 한 세션 안에서 짧은 주기 가능, 세션 안 컨텍스트 공유 | 세션 닫히면 멈춤 |
| macOS LaunchAgent ([[macos-launchagent-catchup-behavior]]) | 시스템 레벨, sleep 후 catchup | LLM 호출 없음. shell 작업 한정 |
| Vercel Cron ([[vercel-cron-best-practices]]) | 24/7 클라우드 실행 | LLM 사용 시 별도 API 비용. 60초 lambda 한도 |
| GitHub Actions cron | 무료 tier 풍부, repo 와 연동 | 분 단위 정확도 낮음, LLM 호출은 별도 키 |

핵심 trade-off: **scheduled-tasks 는 "데스크톱 사용자 + LLM 추론이 필요한" 좁은 자리**에 적합. 24/7 무인 환경에서는 부적절 — 클라우드 cron 으로 가야 한다.

## 관련 맥락

- 같은 마음으로 만든 자동화 패턴 [[claude-code-loop-automation]] 과 함께 보면 동작 주체의 차이가 더 분명.
- Cron 일반 (멱등성 / `/api/*` 외부 노출 / 권한 분리) 은 [[vercel-cron-best-practices]] 에서 논의된 원칙이 그대로 적용된다.
- 시스템 cron 의 catchup 의미는 [[macos-launchagent-catchup-behavior]] 와 비교 — scheduled-tasks 는 catchup 없음.

## 변경 이력

- 2026-05-05: 최초 생성. news-digest 스킬을 10분 주기로 등록·실행하는 세션에서 도출된 동작 모델 (앱 종속 / 자동 실행 prompt / 권한 사전 승인 / "Run now" 의 의미) 정리 (출처: session-logs/20260505-103341-adb1-*, session-logs/20260505-104124-ad05-*)
