---
title: "Claude Code 고급 기능 — MCP·Hooks·SubAgents·Agent Teams"
domain: "staging"
tags: ["Claude Code", "MCP", "Hooks", "SubAgents", "Agent Teams", "병렬 에이전트", "자동화"]
created: "2026-04-15"
updated: "2026-04-15"
sources:
  - "raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf"
confidence: "high"
related:
  - "wiki/ai-agent-basics.md"
  - "wiki/claude-code-setup.md"
  - "wiki/claude-code-skills-plugins.md"
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

MCP 서버는 `~/.claude/settings.json` 또는 프로젝트별 `.claude/settings.json`에서 관리.

### Hooks 이벤트 시스템

훅은 특정 이벤트에 쉘 스크립트를 자동 실행하는 메커니즘이다.

#### 주요 훅 이벤트

| 이벤트 | 트리거 시점 |
|---|---|
| `PreToolCall` | 도구 호출 직전 |
| `PostToolCall` | 도구 호출 완료 후 |
| `PreCommit` | git commit 실행 전 |
| `PostCommit` | git commit 완료 후 |
| `SessionStart` | 세션 시작 시 |
| `SessionEnd` | 세션 종료 시 |

#### 훅 활용 예시

```json
{
  "hooks": {
    "PostToolCall": [
      {
        "matcher": "Write",
        "command": "eslint --fix {{file_path}}"
      }
    ],
    "PreCommit": [
      {
        "command": "npm test"
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

- 2026-04-15: 최초 생성 (출처: raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf)
