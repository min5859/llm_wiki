---
title: "Claude Code TUI 화면 전환·인스턴스 격리 — Ctrl+B FleetView·Esc·다중 인스턴스의 한계"
domain: both
sensitivity: public
tags: ["analysis", "claude-code", "tui", "subagent", "fleetview", "ipc", "ux"]
created: "2026-05-16"
updated: "2026-05-16"
source_session: "20260515-235344-9737-claude-agents-의-기능이-뭔가요.md"
sources:
  - "session-logs/20260515-235344-9737-claude-agents-의-기능이-뭔가요.md"
  - "session-logs/20260516-000703-e374-다른-창에-claude-code-창을-여러개-띄워놨는데-왜-못찾나요--여기서-만든-clau.md"
confidence: high
related:
  - "wiki/concepts/claude-code-basic-usage.md"
  - "wiki/patterns/claude-code-advanced.md"
  - "wiki/patterns/claude-code-agent-teams-tmux.md"
---

# Claude Code TUI 화면 전환·인스턴스 격리

Claude Code 의 TUI 화면 전환 키 (`Esc` / `Ctrl+B`) 와 다중 인스턴스 운용의 격리 모델을 정리한다. 화면이 바뀌었는데 "어떻게 되돌리지?" 가 막히는 경우와, 다른 창에서 띄운 Claude Code 가 현재 세션에서 안 보이는 경우의 정답.

## 화면 전환 키

| 키 | 동작 |
|----|------|
| `Esc` | 현재 서브 화면 (서브에이전트·작업 상세·설정창 등) 에서 이전 화면으로 복귀 |
| `Ctrl+B` | **FleetView** (백그라운드 작업 목록) 토글. 현재 CLI 인스턴스 내부의 백그라운드 잡·서브에이전트 일람 |
| `/config` | 설정창. 슬래시 커맨드일 뿐 "별도 실행 중인 Claude" 인스턴스가 아님 |

FleetView 동작 순서:

1. `Ctrl+B` → 현재 CLI 가 소유한 백그라운드 작업 리스트가 뜸
2. 화살표로 작업 선택 → `Enter` 로 그 작업 화면 진입
3. `Esc` 로 빠져나와 메인 대화로 복귀

`Ctrl+B` 를 눌렀는데 아무 변화가 없으면 그 CLI 인스턴스에 띄운 백그라운드 작업이 0개라는 뜻 (서브에이전트 / `--bg` / TaskCreate 로 띄운 잡 0개).

## 다중 인스턴스의 격리 모델

**한 줄 요약**: Claude Code CLI 인스턴스는 각각 독립 프로세스라 **서로의 세션·대화·상태를 공유하지 않는다**. 다른 터미널 창에서 띄운 Claude Code 는 이 세션에서 안 보인다.

| 보이는 것 | 보이지 않는 것 |
|-----------|---------------|
| 현재 CLI 인스턴스 안에서 띄운 백그라운드 잡 / 서브에이전트 / worktree | 다른 터미널 창의 별도 Claude Code CLI 프로세스 |
| `TaskList` 로 노출되는 자식 작업들 | `claude --bg` 로 다른 셸에서 띄운 별도 CLI 인스턴스 |
| `$CLAUDE_JOB_DIR` 를 공유하는 형제 잡 | 같은 사용자가 다른 디렉터리에서 띄운 CC |

**즉**: 현재 CLI 가 자식으로 띄운 것만 본다. 형제·외부 CLI 프로세스는 IPC 경로가 없다.

## 다중 창을 묶어서 운용하려면

CLI 인스턴스 간 통신·상태 공유가 필요하면 외부 경로가 필요하다:

| 경로 | 용도 | 한계 |
|------|------|------|
| **공유 파일 / git** | 같은 디렉터리에서 작업하고 파일을 통해 간접 공유 | 실시간 알림 불가, polling 필요 |
| **메모리 디렉터리** (`~/.claude/projects/.../memory/`) | 동일 프로젝트 컨텍스트의 memory 파일 공유 | 같은 프로젝트 디렉터리일 때만 |
| **`$CLAUDE_JOB_DIR`** | 한 인스턴스 안에서 띄운 잡들의 공통 작업 디렉터리 | 한 부모 CLI 안의 형제만, 다른 창 CC 는 불가 |
| **MCP 외부 저장소** (Notion / DB 등) | 두 창이 동일 MCP 서버로 같은 외부 상태 read/write | 외부 의존, 응답 지연 |
| **tmux + Agent Teams** | 한 사용자의 여러 CC 세션을 tmux pane 으로 묶어 한 화면에 관찰 | IPC 가 아닌 *시각적* 통합 (→ [[claude-code-agent-teams-tmux]]) |

`/remote-control` 도 인스턴스 간 통신이 아니라 *같은* 인스턴스를 다른 디바이스에서 조작하는 기능이다 (스마트폰 ↔ 로컬 동일 세션).

## 서브에이전트의 격리 모델 (참고)

`Ctrl+B` 로 보이는 서브에이전트는 **현재 CLI 안의 자식**이고, 다음 격리 특성을 가진다:

- **컨텍스트 격리**: 부모 대화창과 별도 컨텍스트 윈도우. 대량 탐색·검색·분석 결과로 부모 컨텍스트 오염을 피한다
- **병렬 실행**: 여러 서브에이전트를 동시에 띄울 수 있다 (예: 코드 검색 + 테스트 분석 동시)
- **전문화된 역할**: 빌트인 (`Explore` / `Plan` / `general-purpose` / `claude-code-guide`) + 커스텀 (`~/.claude/agents/*.md` 또는 `.claude/agents/*.md`)
- **도구 권한 제한**: 에이전트별로 사용 가능한 도구를 제한 가능

→ 자세한 운용 패턴은 [[claude-code-advanced]] 의 SubAgents 섹션 참조.

## 트러블슈팅 흐름

**증상 ①**: "다른 창에서 만든 Claude Code 가 여기서 안 보임"
→ 정상. 별도 프로세스라 IPC 없음. 모니터링하려면 그 CLI 가 자식으로 띄운 잡이어야 함 (`--bg` 도 별도 인스턴스를 띄우는 것이지 부모-자식 관계가 아님).

**증상 ②**: "Ctrl+B 를 눌러도 화면 변화 없음"
→ 현재 인스턴스에 띄운 백그라운드 작업이 0개. `Agent` / `TaskCreate` / 서브에이전트 호출 후 다시 시도.

**증상 ③**: "/config 가 떠 있는데 이것도 Claude 인스턴스인가?"
→ 아님. 슬래시 커맨드의 설정창일 뿐. `Esc` 로 메인 대화 복귀.

**증상 ④**: "서브에이전트 화면에 들어왔는데 메인 대화로 못 돌아감"
→ `Esc` 로 한 단계씩 빠져나옴. 안 되면 `Ctrl+B` 토글 후 메인을 선택.

## 함정

1. **`--bg` 의 의미 오해**: `claude --bg` 는 *현재 세션의 백그라운드 잡* 이 아니라 *분리된 별도 CLI 인스턴스* 를 띄운다. 이건 부모의 FleetView 에서 안 보인다. "백그라운드" 단어가 부모 ↔ 자식 관계를 의미하지 않음.
2. **서브에이전트와 인스턴스를 혼동**: 서브에이전트 = 한 인스턴스 안의 자식 (보임). 다른 창의 CC = 별도 인스턴스 (안 보임). 같은 "Claude Code 인스턴스" 라는 단어로 묶이지만 격리 단위가 다름.
3. **`/config` 가 인스턴스인 줄 안다**: 슬래시 커맨드의 설정창은 인스턴스가 아니다. 떠 있는 모든 "Claude Code 화면" 이 별개 프로세스인 게 아님.
4. **메모리 디렉터리도 자동 동기화 아님**: `~/.claude/projects/.../memory/` 의 파일을 두 CLI 가 공유하지만, 한쪽이 쓴 변경을 다른 쪽이 자동으로 reload 하지는 않는다. 다음 세션 시작 / 다음 도구 호출 시점에 읽힌다.

## 관련 맥락

- 기본 슬래시 커맨드와 컨텍스트 관리는 [[claude-code-basic-usage]]
- 서브에이전트 / Hooks / Agent Teams 의 정의는 [[claude-code-advanced]]
- tmux 로 여러 CC 세션을 시각적으로 묶는 패턴은 [[claude-code-agent-teams-tmux]]

## 변경 이력

- 2026-05-16: 최초 생성 — Claude Code TUI 화면 전환 (`Esc` / `Ctrl+B` / FleetView) 과 다중 인스턴스 격리 (IPC 없음, 부모-자식 만 보임) 의 일반 사상 정리. 사용자가 화면 전환에 막힌 케이스 + "다른 창 CC 가 왜 안 보이지" 케이스 2건을 통합 (출처: session-logs/20260515-235344-9737, 20260516-000703-e374)
