---
title: "Claude Code Agent Teams와 tmux 운용"
domain: "staging"
tags: ["Claude Code", "Agent Teams", "tmux", "멀티에이전트", "병렬 개발", "split panes"]
created: "2026-04-16"
updated: "2026-04-16"
sources:
  - "raw/Tips/Agent-Team-tmux가이드.pdf"
  - "raw/Tips/Claude Code Agent Teams 완전 도입 가이드 — 멀티에이전트로 개발 생성.pdf"
confidence: "medium"
related:
  - "wiki/claude-code-advanced.md"
  - "wiki/claude-code-basic-usage.md"
  - "wiki/claude-code-windows-wsl-tmux.md"
---

# Claude Code Agent Teams와 tmux 운용

Agent Teams는 여러 Claude Code 에이전트를 역할별로 나누어 동시에 실행하는 개발 방식이다. tmux는 이 병렬 세션을 한 터미널 안에서 관찰하고 제어하기 위한 운영 도구로 쓰인다.

## 핵심 내용

### Agent Teams란

단일 Claude Code 세션이 모든 일을 처리하는 대신, 여러 에이전트를 팀처럼 구성한다.

| 역할 | 예시 |
|---|---|
| 오케스트레이터 | 전체 목표, 작업 분해, 통합 판단 |
| 구현 에이전트 | 프론트엔드, 백엔드, DB, 테스트 등 단위 구현 |
| 리뷰 에이전트 | 코드 품질, 보안, 타입, 테스트 관점 검토 |
| 문서 에이전트 | README, 변경 이력, 운영 문서 정리 |

핵심은 병렬화 자체가 아니라 **작업 경계와 통합 책임을 명확히 나누는 것**이다.

### tmux를 쓰는 이유

tmux는 여러 터미널 pane/window를 한 세션에서 관리한다.

- 여러 Claude Code 세션을 동시에 띄우고 관찰
- split panes 모드로 팀원별 작업 상태 확인
- 세션을 유지한 채 터미널을 닫거나 다시 접속
- `send-keys`로 pane 간 지시 전달 가능

Agent Teams를 수동으로 운용할 때 tmux는 "상황판" 역할을 한다.

### 기본 운용 흐름

1. 작업을 레이어나 기능 단위로 분해한다.
2. 각 에이전트가 담당할 디렉터리와 파일 범위를 정한다.
3. tmux pane/window를 나누어 Claude Code 세션을 실행한다.
4. 오케스트레이터가 각 에이전트에게 명확한 목표와 금지사항을 전달한다.
5. 각 세션 결과를 요약 파일이나 PR 단위로 모은다.
6. 최종 통합 세션에서 충돌, 테스트, 문서를 정리한다.

## 세부 사항

### 작업 분해 기준

좋은 분해:

- `frontend/`, `backend/`, `db/`, `docs/`처럼 경계가 분명함
- 같은 파일을 여러 에이전트가 수정하지 않음
- 각 에이전트가 독립적으로 테스트하거나 검증 가능
- 최종 통합자가 확인할 산출물이 명확함

나쁜 분해:

- "전체 코드 품질 개선"처럼 범위가 넓음
- 같은 공통 타입이나 설정 파일을 여러 에이전트가 동시에 건드림
- 구현과 리뷰와 문서가 한 세션에 섞임

### tmux 설정 포인트

Tips 자료는 tmux 설치, 빠른 시작, `tmux.conf` 적용, Agent Teams 설정을 다룬다. 실제 설정값은 운영 환경마다 다르므로 원문 PDF의 명령을 확인한다.

점검할 항목:

- tmux 설치 확인
- pane 분할 단축키
- 마우스 모드
- copy mode
- status line
- Claude Code 세션별 working directory

### Git Worktree와 병렬 개발

동일 저장소를 여러 에이전트가 동시에 수정할 때는 Git worktree를 쓰면 충돌을 줄일 수 있다.

```
main repo
├── worktree-frontend
├── worktree-backend
└── worktree-docs
```

각 에이전트가 별도 worktree에서 작업하고, 통합자가 변경사항을 병합한다.

### 주의점

- 병렬 에이전트는 토큰을 많이 쓴다. 단순 검색이나 작은 수정에는 과하다.
- 담당 파일 범위가 겹치면 통합 비용이 병렬화 이득을 상쇄한다.
- 세션별 결과 요약을 남기지 않으면 최종 통합자가 맥락을 잃는다.
- 회사 환경에서는 외부 플러그인과 스크립트 실행 권한을 먼저 검토한다.

## 관련 맥락

- Windows 환경에서 tmux를 쓰려면 WSL2 또는 Git Bash 기반 구성이 필요할 수 있다. 자세한 내용은 [[claude-code-windows-wsl-tmux.md]].
- Agent Teams는 [[claude-code-advanced.md]]의 SubAgents 개념을 실제 운영 환경으로 확장한 패턴이다.
- 토큰 비용 관점에서는 [[claude-code-token-optimization.md]]의 세션 분리 원칙과 함께 봐야 한다.

## 변경 이력

- 2026-04-16: Tips PDF 기반 최초 생성
