---
title: "Windows에서 WSL·VS Code·tmux·Claude Code 환경 구성"
domain: "staging"
tags: ["Claude Code", "Windows", "WSL2", "VS Code", "tmux", "Git Bash", "PowerShell"]
created: "2026-04-16"
updated: "2026-04-16"
sources:
  - "raw/Tips/wsl-vscode-tmux-claude-code-guide.pdf"
  - "raw/Tips/Claude Code 4월 업데이트 후 tmux 불가 문제.pdf"
confidence: "medium"
related:
  - "wiki/claude-code-agent-teams-tmux.md"
  - "wiki/claude-code-setup.md"
  - "wiki/claude-code-advanced.md"
---

# Windows에서 WSL·VS Code·tmux·Claude Code 환경 구성

Windows에서 Claude Code를 안정적으로 쓰려면 PowerShell 단독 환경보다 WSL2, VS Code Remote WSL, tmux 조합이 실용적이다. 특히 Agent Teams처럼 tmux 기반 병렬 세션을 운용하려면 셸 선택이 중요하다.

## 핵심 내용

### 왜 WSL을 사용하는가

Claude Code는 터미널 기반 개발 흐름과 잘 맞는다. Windows 기본 PowerShell만으로도 일부 작업은 가능하지만, Linux 도구 체인과 tmux를 자연스럽게 쓰려면 WSL2가 유리하다.

WSL2 장점:

- Linux 패키지와 shell script를 그대로 사용
- tmux, git, Docker, Node/Python 도구와 호환성 높음
- VS Code Remote WSL과 연동 가능
- 사내 Linux 서버 운영 환경과 명령 체계가 가까움

### 2026년 4월 tmux 이슈

Tips 자료는 Claude Code 4월 업데이트 이후 PowerShell 기반 tmux 운용이 어려워진 문제를 다룬다. 해결 방향은 WSL2 또는 Git Bash를 기본 셸로 전환하는 것이다.

| 환경 | 권장도 | 비고 |
|---|---:|---|
| WSL2 | 높음 | Linux 도구 체인과 tmux 운용에 적합 |
| Git Bash | 중간 | 가볍지만 복잡한 개발 환경에는 한계 |
| PowerShell | 낮음 | tmux 기반 Agent Teams 운용에 제약 가능 |

### 기본 셋업 흐름

1. WSL2 설치 및 배포판 확인
2. 기본 도구 설치: git, curl, Node.js, Python, tmux 등
3. VS Code Remote WSL 설치
4. 프로젝트를 WSL 파일 시스템 아래에 배치
5. Claude Code 설치 및 로그인
6. tmux 설정 적용
7. 필요한 경우 Docker Desktop의 WSL integration 활성화

## 세부 사항

### 프로젝트 배치

권장:

```
~/projects/my-repo
```

피해야 할 위치:

```
/mnt/c/Users/.../my-repo
```

Windows 파일 시스템을 WSL에서 마운트해 쓰면 파일 IO가 느려질 수 있다. 개발 프로젝트는 WSL 내부 파일 시스템에 둔다.

### VS Code Remote WSL

VS Code는 Windows 앱을 쓰되, 실제 터미널과 확장 실행 환경은 WSL로 붙인다.

확인할 것:

- 좌하단 Remote WSL 표시
- 터미널이 WSL shell인지 확인
- 확장 기능이 WSL 쪽에 설치됐는지 확인
- Claude Code가 WSL 터미널에서 실행되는지 확인

### tmux 기본 조작

Agent Teams 운용 전 최소한 익힐 것:

- 새 세션 생성
- pane 좌우/상하 분할
- pane 이동
- 세션 detach/attach
- scroll/copy mode

### 트러블슈팅

| 증상 | 점검 |
|---|---|
| tmux가 중첩됨 | 이미 tmux 안인지 확인 후 새 세션 대신 새 window/pane 사용 |
| 클립보드 이미지 붙여넣기 실패 | WSL/VS Code/터미널의 clipboard 경로 확인 |
| WSL이 느림 | 프로젝트가 `/mnt/c` 아래 있는지 확인 |
| Docker가 동작하지 않음 | Docker Desktop WSL integration 확인 |
| tmux 설정 오류 | `.tmux.conf` 문법과 plugin 경로 확인 |
| VS Code 확장 미동작 | 확장이 Windows 쪽이 아니라 WSL 쪽에 설치됐는지 확인 |

## 관련 맥락

- tmux를 이용한 Agent Teams 운용은 [[claude-code-agent-teams-tmux.md]].
- 팀 표준 환경으로 배포하려면 [[claude-code-enterprise-security-bedrock.md]]의 보안/권한 원칙을 함께 고려한다.

## 변경 이력

- 2026-04-16: Tips PDF 기반 최초 생성
