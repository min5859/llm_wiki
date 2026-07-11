# Claude Code용 Opus 오케스트레이션 설정 문서

이 문서는 Claude Code가 직접 로컬 설정을 구성하도록 지시하기 위한 작업 문서다. 원본 가이드의 역할 배치를 바꿔서, **지휘자는 Opus**, **실행자는 Sonnet**, **잡무는 Haiku**가 맡도록 설정한다.[page:1]

## 목표 구조

설정 목표는 다음 3계층이다.[page:1]

- Opus 4.8: 오케스트레이터, 계획·판단·분배 담당.[page:1]
- Sonnet: 실제 코드 작성과 일반 실행 담당.[page:1]
- Haiku 4.5: 검색, 로그 확인, 반복성 잡무 담당.[page:1]

원본 문서는 Fable 5를 지휘자로 두고 Opus를 실행 계층으로 쓰지만, 여기서는 그 구조를 그대로 유지하되 **Fable 자리에 Opus**, **Opus 자리에 Sonnet**을 넣어서 적용한다.[page:1]

## Claude Code에게 줄 작업 지시문

아래 지시문을 Claude Code에 그대로 전달해서 로컬 환경을 수정하게 한다.

---

당신은 현재 사용자의 Claude Code 로컬 설정을 직접 구성해야 한다.
목표는 Claude Code에서 **Opus를 오케스트레이터로 사용하고, Sonnet을 실행 계층으로 사용하며, Haiku를 경량 러너로 유지하는 오케스트레이션 구조**를 만드는 것이다.[page:1]

다음 요구사항을 정확히 반영하라.[page:1]

### 1. 디렉터리 구조 생성

아래 디렉터리를 기준으로 필요한 파일을 생성하라.[page:1]

```text
~/.claude/opus-orchestration/
├── opus.md
├── empty.md
├── active.md
├── env.sh
├── hooks/
│   └── orchestration-gate.py
├── agents/
│   ├── deep-reasoner.md
│   └── runner.md
└── state/
```

의도는 설정 본체를 한 폴더에 모으고, Claude가 실제로 읽는 파일은 최소 연결만 유지하는 것이다.[page:1]

### 2. CLAUDE.md 연결

`~/.claude/CLAUDE.md` 파일 끝에 아래 한 줄이 존재하도록 하라.[page:1]

```md
@~/.claude/opus-orchestration/active.md
```

이 경로는 on/off 토글 시 심링크 대상만 바꾸기 위해 사용한다.[page:1]

### 3. active.md 토글 구조

다음 규칙으로 파일을 구성하라.[page:1]

- `opus.md`: 오케스트레이션이 켜졌을 때 로드할 실제 지침 파일.[page:1]
- `empty.md`: 오케스트레이션 off 상태에서 비워진 지침 파일.[page:1]
- `active.md`: `opus.md` 또는 `empty.md`를 가리키는 심링크.[page:1]
- `~/.claude/.opus-orchestration-state`: 현재 on/off 상태를 기록하는 파일.[page:1]

### 4. 토글 스크립트 생성

`~/.local/bin/opus-orchestration` 스크립트를 만들고 실행 가능하게 설정하라.[page:1]

이 스크립트는 다음 동작을 제공해야 한다.[page:1]

- `opus-orchestration on`: 상태 파일에 `on` 기록, `active.md -> opus.md` 심링크 설정.[page:1]
- `opus-orchestration off`: 상태 파일에 `off` 기록, `active.md -> empty.md` 심링크 설정.[page:1]
- `opus-orchestration status`: 현재 상태와 심링크 대상을 출력.[page:1]

기존 설정 파일을 직접 덮어쓰기보다, 상태 파일과 심링크만 바꾸는 방식으로 구현하라.[page:1]

### 5. 오케스트레이터 지침 파일 작성

`~/.claude/opus-orchestration/opus.md`를 작성하라.

이 파일에는 다음 원칙이 반드시 들어가야 한다.[page:1]

- 메인 모델은 직접 대량의 코드 수정을 하지 말고 계획, 작업 분해, 검토, 위임 중심으로 행동한다.[page:1]
- 실제 코드 작성과 수정은 가능한 한 Sonnet 계층에 위임한다.[page:1]
- 단순 조사, grep, 로그 확인, 파일 탐색 같은 저비용 작업은 Haiku runner에 위임한다.[page:1]
- 메인 모델이 직접 수정해야 할 때는 최소 범위만 수정하고, 여러 파일 수정은 우선 위임한다.[page:1]
- 메인 모델은 항상 “계획 → 위임 → 검토 → 필요 시 재위임” 순서로 행동한다.[page:1]

지침 문구는 Claude Code가 이해하기 쉬운 한국어로 작성하되, 명령형으로 간결하게 쓰라.

### 6. 서브에이전트 파일 구성

다음 두 파일을 생성하라.[page:1]

#### `~/.claude/opus-orchestration/agents/deep-reasoner.md`

요구사항:[page:1]

- 모델은 **Sonnet**으로 설정한다.[page:1]
- 역할은 실제 코드 작성, 리팩터링, 테스트 수정, 구현 작업 담당이다.[page:1]
- 광범위한 아키텍처 판단보다는 명확한 구현 실행에 집중하게 한다.[page:1]
- 출력은 변경 내용, 이유, 검증 결과 중심으로 정리하게 한다.[page:1]

#### `~/.claude/opus-orchestration/agents/runner.md`

요구사항:[page:1]

- 모델은 **Haiku 4.5**로 설정한다.[page:1]
- 역할은 grep, read, bash 기반 조사, 테스트 실행, 로그 확인, 파일 탐색 같은 경량 작업이다.[page:1]
- 판단이 필요한 설계 변경은 하지 않게 한다.[page:1]
- 가능한 경우 결과를 짧게 요약만 하게 한다.[page:1]

그리고 Claude가 참조하는 실제 경로인 `~/.claude/agents/deep-reasoner.md`, `~/.claude/agents/runner.md`는 위 파일을 가리키는 심링크로 맞춰라.[page:1]

### 7. Sonnet 실행 계층 유지용 env.sh 작성

`~/.claude/opus-orchestration/env.sh`를 작성하라.

의도는 원본 가이드의 “Sonnet 호출을 Opus로 승격” 패턴을 뒤집어서, 이번 구성에서는 **실행 계층을 Sonnet으로 고정**하는 것이다.[page:1]

다음 규칙을 따른다.[page:1]

- 상태 파일 `~/.claude/.opus-orchestration-state`를 읽는다.[page:1]
- 상태가 `on`일 때만 관련 환경변수를 적용한다.[page:1]
- Claude Code에서 기본 실행 모델이 Sonnet 계열을 사용하도록 필요한 환경변수 또는 래퍼 함수를 구성한다.[page:1]
- 상태가 `off`면 어떤 모델 강제도 하지 않는다.[page:1]

`~/.bashrc` 끝에는 아래 한 줄이 존재하도록 하라.[page:1]

```bash
source ~/.claude/opus-orchestration/env.sh
```

단, 중복 삽입은 피하라.[page:1]

### 8. PreToolUse 훅 작성

`~/.claude/opus-orchestration/hooks/orchestration-gate.py`를 작성하라.[page:1]

이 훅은 원본 가이드의 게이트 아이디어를 유지하되, 이번 구조에 맞춰 다음 규칙으로 구현하라.[page:1]

- 오케스트레이션 상태가 `off`면 모든 호출을 통과시킨다.[page:1]
- 메인 에이전트가 코드 파일을 직접 수정하는 경우, 한 턴(prompt_id)당 최대 2개 파일까지만 허용한다.[page:1]
- 3번째 코드 파일 수정부터는 차단하고, Sonnet 구현 에이전트에 위임하라는 stderr 메시지를 반환한다.[page:1]
- Bash를 통한 직접 코드 수정(`sed -i`, `perl -i`, 리다이렉션 쓰기, `tee` 기반 덮어쓰기 등)은 메인 에이전트에서 항상 차단한다.[page:1]
- 서브에이전트 호출(`agent_id` 또는 `agent_type` 존재)은 통과시킨다.[page:1]
- 상태 저장은 `~/.claude/opus-orchestration/state/<session_id>.json`에 수행한다.[page:1]
- 비코드 파일(markdown, json, yaml, txt 등)은 제한 대상에서 제외한다.[page:1]
- 예외가 발생하면 fail-open으로 `exit(0)` 처리한다.[page:1]

### 9. settings.json에 훅 등록

`~/.claude/settings.json` 파일의 `hooks` 설정에 PreToolUse 훅을 추가하라.[page:1]

매처는 아래 도구들을 포함해야 한다.[page:1]

- `Write`
- `Edit`
- `NotebookEdit`
- `MultiEdit`
- `Bash`

기존 hooks 구성이 있으면 병합하고, JSON 구조를 깨뜨리지 마라.[page:1]

### 10. 검증 절차 수행

설정 완료 후 아래 항목을 검증하라.[page:1]

1. `opus-orchestration on` 실행 후 상태가 on인지 확인.[page:1]
2. `active.md`가 `opus.md`를 가리키는지 확인.[page:1]
3. 메인 에이전트가 코드 파일 3개를 연속 생성하려고 할 때 3번째에서 차단되는지 확인.[page:1]
4. deep-reasoner 서브에이전트가 같은 작업을 수행할 때는 3개 모두 생성 가능한지 확인.[page:1]
5. `opus-orchestration off` 후에는 훅이 사실상 비활성처럼 동작하는지 확인.[page:1]
6. `.bashrc`, `CLAUDE.md`, `settings.json`, `~/.claude/agents/*` 심링크가 모두 정상인지 확인.[page:1]

### 11. 최종 보고 형식

작업이 끝나면 아래 형식으로 결과를 보고하라.

- 생성/수정한 파일 목록
- 각 파일의 역할 한 줄 요약
- 검증 결과
- 수동으로 확인할 항목이 있으면 별도 표시

그리고 실제 파일 내용이 중요하므로, 핵심 파일들은 생성 후 내용을 다시 읽어 검증하라.

---

## 구현 메모

- 원본 가이드의 핵심은 “강한 모델에게 판단을 맡기되, 직접 손대는 범위를 훅으로 제한한다”는 점이다.[page:1]
- 이번 문서는 그 구조를 그대로 유지하면서, 지휘 모델을 Fable에서 Opus로 내리고, 실행 모델을 Opus에서 Sonnet으로 내린 변형판이다.[page:1]
- 따라서 비용·품질 균형은 원본보다 보수적이지만, Claude Code에서 더 일반적인 실행 흐름에 맞추기 쉬운 형태다.[page:1]

## Claude Code에 붙여넣을 때의 한 줄 요청

아래 한 줄로 시작하면 된다.

```text
다음 문서를 작업 명세로 보고, 내 로컬 Claude Code 설정 파일을 직접 수정해서 Opus 오케스트레이터 + Sonnet 실행자 + Haiku 러너 구조를 구성해줘. 파일 생성, 심링크, 훅 등록, 검증까지 모두 수행하고 마지막에 변경 사항을 보고해.
```
