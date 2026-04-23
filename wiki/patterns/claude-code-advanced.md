---
title: "Claude Code 고급 기능 — MCP·Hooks·SubAgents·Agent Teams"
domain: "personal"
sensitivity: "public"
tags: ["Claude Code", "MCP", "Hooks", "SubAgents", "Agent Teams", "병렬 에이전트", "자동화"]
created: "2026-04-15"
updated: "2026-04-16"
sources:
  - "raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf"
  - "raw/음성 260413_AA-1-5_original.txt"
  - "raw/음성 260414_AA-2-2_original.txt"
  - "raw/음성 260415_AA-3-1_original.txt"
  - "raw/음성 260415_AA-3-3_original.txt"
  - "raw/Tips/Agent-Team-tmux가이드.pdf"
  - "raw/Tips/Claude Code Agent Teams 완전 도입 가이드 — 멀티에이전트로 개발 생성.pdf"
confidence: "high"
related:
  - "wiki/ai-agent-basics.md"
  - "wiki/claude-code-setup.md"
  - "wiki/claude-code-skills-plugins.md"
  - "wiki/claude-code-agent-teams-tmux.md"
---

# Claude Code 고급 기능 — MCP·Hooks·SubAgents·Agent Teams

Claude Code의 고급 자동화 기능인 MCP 통합, Hooks 이벤트 시스템, 서브에이전트 생성, Agent Teams 패턴을 정리한다.

## 핵심 내용

### MCP (Model Context Protocol) 통합

Claude Code는 MCP를 통해 외부 시스템과 연결된다.

```bash
# MCP 서버 추가
claude mcp add postgres "npx @anthropic-ai/mcp-server-postgres $DB_URL"
claude mcp add github "npx @anthropic-ai/mcp-server-github"
```

주요 MCP 서버 예시:
- **Database**: PostgreSQL, MySQL, SQLite
- **Version Control**: GitHub, GitLab
- **Communication**: Slack, Jira, Notion
- **Browser**: Playwright (웹 자동화)
- **File System**: 원격 파일 접근
- **Codebase Indexing**: Serena, spec-workflow MCP 등
- **Diagram / Docs**: Draw.io, Obsidian 등
- **Runtime Debugging**: Chrome DevTools, Docker 등

MCP 서버는 `~/.claude/settings.json` 또는 프로젝트별 `.claude/settings.json`에서 관리.

실무상 MCP는 "연결 고리"에 가깝다. DB, GitHub, Figma, 브라우저, 장비 로그처럼 기존 시스템이 가진 기능을 Claude Code가 호출할 수 있게 노출한다. 다만 MCP 서버가 정상이어도 LLM이 도구를 인식하지 못하면 호출이 실패할 수 있으므로, 서버 자체 테스트와 LLM의 도구 인식 테스트를 분리해서 봐야 한다.

MCP가 항상 최선은 아니다. Claude Code처럼 터미널 기반 자동화를 많이 쓰는 환경에서는 반복 작업을 MCP보다 CLI로 만들어 배포하는 편이 더 단순한 경우가 있다. GUI 기반 도구나 외부 서비스 연결은 MCP, 사내 표준 자동화나 배포 가능한 실행 모듈은 CLI로 나누는 방식이 실용적이다.

### Hooks 이벤트 시스템

훅은 특정 이벤트에 쉘 스크립트를 자동 실행하는 메커니즘이다.

#### 주요 훅 이벤트

| 이벤트 | 트리거 시점 |
|---|---|
| `PreToolUse` | 도구 호출 직전 |
| `PostToolUse` | 도구 호출 성공 후 |
| `PostToolUseFailure` | 도구 호출 실패 후 |
| `PermissionRequest` | 권한 승인 요청 발생 시 |
| `SessionStart` | 세션 시작 시 |
| `PreCompact` / `PostCompact` | 컨텍스트 압축 전후 |
| `Stop` | Claude 응답 종료 시 |
| `SessionEnd` | 세션 종료 시 |

#### 훅 활용 예시

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | xargs npx prettier --write"
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "if": "Bash(rm *)",
            "command": ".claude/hooks/block-destructive-command.sh"
          }
        ]
      }
    ]
  }
}
```

**강제력**: 훅은 CLAUDE.md 규칙보다 강력하다. CLAUDE.md를 LLM이 무시하더라도 훅은 반드시 실행된다.

## 세부 사항

### SubAgents (서브에이전트)

Claude Code는 작업을 병렬로 처리하기 위해 서브에이전트를 생성할 수 있다.

```
오케스트레이터 Claude
├── SubAgent A: 프론트엔드 수정
├── SubAgent B: 백엔드 API 작성
└── SubAgent C: 테스트 코드 생성
```

- 각 서브에이전트는 독립적인 컨텍스트를 가짐
- AGENTS.md로 서브에이전트 전용 지시서 별도 관리 가능
- 병렬 처리로 대형 작업 시간 단축

### Agent Teams

여러 전문화된 에이전트가 역할을 분담하는 패턴.

예시: PR 리뷰 팀
- code-reviewer: 코드 품질
- security-analyst: 보안 취약점
- test-coverage-checker: 테스트 커버리지
- performance-reviewer: 성능 이슈

각 에이전트가 병렬로 검토 후 결과를 종합.

tmux 기반 Agent Teams는 pane을 나누어 여러 Claude Code 세션을 동시에 관찰하고 지시하는 방식이다. 2026년 4월 업데이트 이후 Windows PowerShell 환경에서는 tmux 운용이 막히는 사례가 있어 WSL2 또는 Git Bash 전환이 필요하다. 자세한 설정은 [[claude-code-agent-teams-tmux.md]]와 [[claude-code-windows-wsl-tmux.md]]에 분리한다.

### AGENTS.md

서브에이전트 전용 지시서. CLAUDE.md와 별개로 관리.

```markdown
# AGENTS.md

## 서브에이전트 공통 규칙
- 작업 완료 후 반드시 결과 요약 출력
- 외부 API 호출 시 rate limit 준수
- 오류 발생 시 즉시 오케스트레이터에 보고

## 코드 리뷰 에이전트
- SOLID 원칙 준수 여부 확인
- ...
```

### CI/CD 파이프라인 통합

```yaml
# GitHub Actions 예시
- name: Claude Code Review
  run: |
    claude --dangerously-skip-permissions \
      "PR #${{ github.event.number }} 코드를 검토하고 이슈를 코멘트로 남겨라"
```

- PR 자동 리뷰, 테스트 자동 실행, 배포 자동화
- 사람 없이 CI/CD 파이프라인 내에서 자율 실행

## 관련 맥락

- MCP는 Anthropic이 제안한 오픈 표준 → 다른 LLM 에이전트도 채택 중
- Agent Teams 패턴은 단일 에이전트의 컨텍스트 한계를 극복하는 방법
- AGENTS.md로 팀원 각자의 역할 정의 가능 (마치 팀원 온보딩 문서처럼)

## 변경 이력

- 2026-04-16: 녹취 및 Tips 기반 MCP 운영 팁, Hook 공식 이벤트명, Agent Teams/tmux 연결 보강
- 2026-04-15: 최초 생성 (출처: raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf)
