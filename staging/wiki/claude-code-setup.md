---
title: "Claude Code 초기 설정 및 하네스 엔지니어링"
domain: "staging"
tags: ["Claude Code", "설정", "하네스 엔지니어링", "CLAUDE.md", "Hooks", "MCP", "Permissions"]
created: "2026-04-15"
updated: "2026-04-15"
sources:
  - "raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf"
  - "raw/CLAUDE.md 완전 가이드-김재우-202604.pdf"
confidence: "high"
related:
  - "wiki/claude-code-overview.md"
  - "wiki/claude-md-guide.md"
  - "wiki/claude-code-basic-usage.md"
---

# Claude Code 초기 설정 및 하네스 엔지니어링

Claude Code를 처음 설치한 후 해야 할 7가지 필수 설정과, 에이전트가 안전하고 일관되게 작동하도록 구조를 설계하는 하네스 엔지니어링 개념을 정리한다.

## 핵심 내용

### 초기 7가지 설정

1. **CLAUDE.md 작성** — 프로젝트 루트에 지시서 파일 생성
2. **Permission 설정** — 허용/거부 도구 및 명령어 범위 정의
3. **MCP 서버 연결** — 필요한 외부 도구 연결 (DB, API 등)
4. **Hooks 설정** — 이벤트 기반 자동화 스크립트 구성
5. **Rules 파일 작성** — 세부 행동 규칙 정의
6. **Skills 정의** — 자주 사용하는 작업 패턴 템플릿화
7. **Model 선택** — 작업 유형에 맞는 모델 설정 (`/model`)

### 하네스 엔지니어링의 6요소

에이전트가 원하는 방향으로 안전하게 작동하도록 하는 구조 설계:

| 요소 | 설명 | 강제력 |
|---|---|---|
| 플로우 내장 | 스킬/플러그인 내부 로직 | 최강 |
| Hooks | 이벤트 트리거 자동 실행 | 강 |
| 함수/스크립트 | 코드로 구현된 규칙 | 중 |
| CLAUDE.md | 자연어 지시서 | 약 |
| Rules | 도구별 허용/거부 목록 | 중 |
| Permissions | 파일·명령 접근 범위 | 중 |

> 중요: CLAUDE.md는 자연어이므로 LLM이 해석에 따라 무시할 수 있다. 중요한 제약은 Hooks나 Permissions로 강제해야 한다.

## 세부 사항

### CLAUDE.md 위치와 범위

| 파일 위치 | 적용 범위 |
|---|---|
| `~/.claude/CLAUDE.md` | 전체 프로젝트 (전역) |
| `{project}/CLAUDE.md` | 해당 프로젝트 |
| `{subdir}/CLAUDE.md` | 해당 서브디렉토리 |

여러 CLAUDE.md가 있으면 모두 합산하여 컨텍스트에 포함된다. 전역 > 프로젝트 > 서브디렉토리 순으로 우선순위가 낮아진다.

### Hooks 설정

훅은 특정 이벤트 발생 시 자동으로 실행되는 쉘 스크립트다.

주요 훅 이벤트:
- `PreToolCall` — 도구 호출 전 실행
- `PostToolCall` — 도구 호출 후 실행
- `PreCommit` — git commit 전 실행
- `SessionStart` — 세션 시작 시 실행

예시 활용:
- 파일 저장 시 자동 포맷팅
- 보안 취약점 자동 스캔
- 특정 명령어 실행 차단

### Permissions 설정

```json
{
  "allow": ["Bash(npm test)", "Bash(git status)"],
  "deny": ["Bash(rm -rf *)", "Bash(sudo *)"]
}
```

`--dangerously-skip-permissions` 플래그: 승인 대화 84% 절감. `/sandbox`와 함께 사용해 OS 격리 상태에서 자동 실행.

### MCP 서버 연결

```bash
claude mcp add <server-name> <command>
```

예: Postgres DB 연결, GitHub API, Slack, Jira 등 외부 시스템과 통합 가능.

## 관련 맥락

- 하네스 엔지니어링은 "에이전트를 어떻게 길들이는가"의 문제
- 팀 단위 사용 시 CLAUDE.md를 git에 커밋하면 팀 전체가 동일한 규칙 공유
- AGENTS.md: 서브에이전트 전용 지시서 (CLAUDE.md와 별개로 관리 가능)

## 변경 이력

- 2026-04-15: 최초 생성 (출처: raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf, raw/CLAUDE.md 완전 가이드-김재우-202604.pdf)
