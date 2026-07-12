---
title: "Claude Code 모델 계층 오케스트레이션 + PreToolUse 게이트"
domain: "ai-agent"
sensitivity: "public"
tags: ["Claude Code", "orchestration", "subagent", "PreToolUse hook", "model tier", "Opus", "Sonnet", "Haiku", "soft nudge", "additionalContext", "escalation", "symlink toggle", "위임"]
created: "2026-07-11"
updated: "2026-07-13"
sources:
  - "raw-sources/claude-code-opus-orchestration-setup.md"
  - "wiki/summaries/claude-code-opus-orchestration-setup.md"
confidence: "high"
related:
  - "wiki/analyses/multi-agent-orchestration-taxonomy.md"
  - "wiki/analyses/ai-coding-agent-cost-and-context-patterns.md"
  - "wiki/patterns/claude-code-token-optimization.md"
  - "wiki/concepts/claude-code-skills-plugins.md"
  - "wiki/summaries/claude-code-opus-orchestration-setup.md"
---

# Claude Code 모델 계층 오케스트레이션 + PreToolUse 게이트

메인 세션 + 모델 계층 서브에이전트(Opus/Sonnet/Haiku) 구조를 **PreToolUse 훅으로 유도**하고
**심링크로 on/off 토글**하는 실전 셋업. 직접 구축·검증했다. `multi-agent-orchestration-taxonomy`의
"Orchestrator-Worker(A)"를 Claude Code에 구체화한 것으로, **두 가지 지향**이 가능하다.

## 두 지향 — 방향이 반대다 (하나의 토글로 전환)

| 지향(모드) | 메인 | 주 위임 대상 | 게이트 역할 | 언제 |
|---|---|---|---|---|
| **hard**(하향 제한) | Opus(비쌈) | implementer(Sonnet)·runner(Haiku) | 메인의 직접 편집을 **차단**(아래로 흘림) | 품질 우선, 강모델이 직접 손대는 범위를 통제하고 싶을 때 |
| **soft**(상향 에스컬레이션, 권장·비용지향) | Sonnet(쌈) | deep-reasoner(Opus)·runner(Haiku) | 어려울 때 위로 **올리라 권고**(넛지) | 비용 우선. 메인이 큰 컨텍스트를 값싼 단가로 싣고 대부분 직접 실행 |
| **off** | (강제 없음) | — | 통과 | 비활성 |

`opus-orchestration {off|hard|soft}` 한 토글로 전환한다. 상태파일 값(off/hard/soft)을 훅·env.sh·active.md
심링크가 각각 읽어 분기하므로, 두 지향이 **공존**하고 즉시 갈아탈 수 있다.

**비용 핵심**: 메인 세션은 매 턴 시스템프롬프트+CLAUDE.md+전체 이력+tool 결과를 *메인 모델 단가*로 싣는다.
그래서 Opus-메인은 품질 셋업이지 절약 셋업이 아니다. 절약이 목표면 **Sonnet-메인 + 어려운 것만 Opus**가
정석([[ai-coding-agent-cost-and-context-patterns]] 패턴1: 강모델은 계획·감사만, 실행은 값싼 모델).
어느 쪽이든 서브에이전트가 **별도 컨텍스트**를 가져 tool 출력(파일덤프·로그)이 메인 컨텍스트를 오염시키지
않는 것이 진짜 이득(context rot 완화). 단, 선형·소규모 작업엔 위임 왕복 오버헤드가 더 크다 →
[[multi-agent-orchestration-taxonomy]].

## 구성 요소

### 1. 서브에이전트로 모델 계층 고정
`~/.claude/agents/*.md` frontmatter `model: opus|sonnet|haiku`로 계층 고정. 실체 파일은 설정 폴더
(`~/.claude/opus-orchestration/agents/`)에 두고 `~/.claude/agents/`엔 **상대 심링크**만. 세 에이전트가
**상시 공존**한다: `implementer`(Sonnet, hard 실행자) · `deep-reasoner`(Opus, soft 상향 추론) ·
`runner`(Haiku, 공통 잡무). 모드별 지침(active.md)이 어느 것을 주로 쓸지 정하고, 미사용 에이전트는 그냥 inert.

### 2. 메인 모델 지정
`ANTHROPIC_MODEL`(env)로 메인 모델을 정한다(예: `claude-sonnet-5`). 단 세션 내 `/model`·`--model`이
있으면 그쪽이 우선. 서브 계층은 frontmatter가, alias 흔들림 방지는 `ANTHROPIC_DEFAULT_{OPUS,HAIKU}_MODEL`
pin이 담당. env.sh는 상태 on일 때만 export.

### 3. PreToolUse 게이트 — 하드블록 vs 소프트 넛지
훅을 `Write|Edit|NotebookEdit|MultiEdit|Bash`에 걸어 **메인 에이전트만** 대상으로 한다. 훅이 상태파일의
모드값(off/hard/soft)을 읽어 분기한다. 공통: fail-open(`exit(0)`) 최우선 · off면 통과 ·
`agent_id`/`agent_type` 있으면(서브에이전트) 통과. **서브에이전트 툴콜도 PreToolUse 훅을 그대로 타므로**
이 제외 로직이 필수다 — 두 필드는 서브에이전트 컨텍스트에서만 채워진다(payload 스키마 확인됨).

- **하드블록**: 한 턴 distinct 코드 파일 2개 초과 시 `exit(2)` + stderr → 툴콜 거부. Bash 인플레이스
  (`sed -i`/`perl -i`/코드파일 `>`·`tee`)도 차단. 강제력 강하나 정당한 다중파일 수정도 막아 역효과.
- **소프트 넛지**(권장): **차단하지 않고** 조언만 주입. 임계(기본 3, distinct 코드 파일) 도달 시 한 턴에
  한 번 "어려우면 deep-reasoner(Opus)에 위임하라" 힌트. Bash 인플레이스도 넛지만.

> **핵심 재사용 사실 — Claude Code 훅에서 "허용 + 모델에 조언 주입"(non-blocking nudge)**:
> `exit(0)` + stdout에 아래 JSON. `additionalContext`가 **system-reminder로 모델 컨텍스트에 주입**되고
> 사용자 UI엔 안 보인다. `permissionDecisionReason`은 deny/ask 때만 표시되고 모델엔 안 감. stdout 평문은
> 모델에 전달 안 됨. `exit(2)`는 항상 차단이라 넛지 불가.
> ```json
> {"hookSpecificOutput":{"hookEventName":"PreToolUse","permissionDecision":"allow","additionalContext":"…조언…"}}
> ```
> (사람 확인이 필요하면 `permissionDecision:"ask"` — 흐름 중단, 모델엔 주입 안 됨.)

비코드(md/json/yaml/txt·확장자 없음)는 대상 제외. 카운트는 distinct 파일(재편집 1회), 상태는
`state/<session_id>.json`. 임계값은 env `OPUS_ORCH_NUDGE_THRESHOLD`.

### 4. 심링크 토글 (config 비파괴, off/hard/soft)
`~/.claude/CLAUDE.md` 끝에 `@~/.claude/opus-orchestration/active.md` import. `active.md`는 심링크:
off→`empty.md` · hard→`mode-hard.md` · soft→`mode-soft.md`. `~/.local/bin/opus-orchestration {off|hard|soft|status}`가
상태파일과 심링크 대상만 바꾼다(기존 설정 비파괴, `on`은 soft 별칭). settings.json 훅은 기존 배열에 **추가 병합**
(rtk 등 보존, 멱등). ⚠️ 토글 스크립트는 bash 3.2(macOS 기본) 호환 필수 — `${x^^}` 등 bash4 문법 금지, `tr`로 대체.

## 함정 (재현하며 겪은 것)

- **자기 자신을 게이트하는 함정**(하드블록 한정): 상태 on에서 `echo '...sed -i...' | python hook`로 검증하면
  실제 게이트가 내 검증 명령 문자열을 보고 차단한다. → 위험 문자열은 **자식 스크립트 파일**로 묶어 실행
  (외부 툴콜은 `bash verify.sh` 한 줄). 소프트 넛지로 바꾸면 차단이 없어 이 함정 자체가 사라진다.
- **훅은 세션 시작 시 로드** — settings.json 등록해도 현재 세션엔 즉시 반영 안 됨(다음 CC 실행부터).
- **env.sh는 셸 시작 시 1회 source** — 토글 후 새 셸부터 반영. `ANTHROPIC_MODEL`은 `/model`·`--model`이 우선.
- **셸 rc 대상**: 로그인 zsh는 `.bashrc`를 안 읽는다 → 실효 파일(`.zshrc`)에 넣어야 동작.
- **turn 경계**: payload `prompt_id`는 CC v2.1.196+부터 제공(확인됨). 그 이전 버전은 transcript 마지막
  user turn uuid → session 순 fallback으로 degrade — 매 편집마다 transcript 스캔이라 긴 세션에선
  tail 제한/캐시가 낫다(개선 여지).

## 실측 — 위임은 "알아서" 일어나지 않는다

Opus 메인을 켜 두면 Sonnet/Haiku가 거의 돌지 않는다는 실사용 관찰이 있었고, 그게 정상 동작이다.

- **강한 메인은 위임 인센티브가 없다.** CC는 사용자 비용을 아끼도록이 아니라 과업을 끝내도록 튜닝돼 있어,
  CLAUDE.md의 "위임해" 권고만으론 자주 무시된다. "네이티브가 알아서 tiering"은 사실상 일어나지 않는다.
- **Sonnet(implementer) 위임을 신뢰할 수 있게 만드는 유일한 장치는 hard 게이트의 편집 차단.**
  편집이 막히니 메인이 어쩔 수 없이 위임한다. 게이트 없으면 idle.
- **Haiku(runner) 잡무 위임은 어떤 모드에서도 강제 불가** — 게이트는 편집만 막을 수 있고, 조사(read/grep)를
  막으면 작업 자체가 마비된다. 대신 지침은 실제로 작동한다: 모드 지침의 "저비용 잡무는 runner에 위임" 한 줄이
  Opus를 움직여 Haiku를 돌린 것이 실측으로 확인됨(지침 기반이라 보장은 아님).
- **soft 모드 문구 함정**: "위임 왕복 비용이 크므로 굳이 위임하지 않는다"(구현 위임 자제)를 모델이 일반화해
  **조사 위임까지 억제**한다. → 구현 자제와 조사 권장을 분리 명시: "여러 파일 훑기·대량 로그처럼 출력이 큰
  조사만 runner에 위임"(판단 기준: 이 조사 출력이 길어질까? Yes→runner). runner description도 대량·고출력
  조사 트리거 중심으로 보강. 과유도하면 단발 grep까지 위임돼 오히려 느려진다.

## 배포 — 멱등 install 패키징 (claude_env)

셋업 전체를 다른 PC로 옮기는 방식. **CC 플러그인은 agents·훅·커맨드만 번들 가능**하고 사용자
`~/.zshrc`·`~/.claude/CLAUDE.md`는 못 건드린다 → env 모델 고정·import 토글이 플러그인 밖이라
**git repo + 멱등 install.sh**(자기완결 패키지 폴더)가 전체를 커버하는 정답.

- **재설치가 상태를 리셋하면 안 된다**: install이 상태를 무조건 off로 초기화하면 재설치 때 활성 모드가
  조용히 꺼진다 → 기존 모드 유지, 신규 설치만 off.
- **레거시 상태값은 자동 매핑하지 않는다**: 구판 `on`(=하드블록)을 신판이 soft로 매핑하면 의미가 조용히
  뒤바뀐다. 값만으로 의도를 알 수 없으면 off + "hard/soft 명시 선택" 안내가 맞다.
- **검증은 가짜 HOME으로 install/uninstall 실제 실행**(신규·재설치·구버전 업그레이드·완전 빈 HOME 시나리오,
  실제 `~/.claude` 무접촉). 병합은 기존 훅(rtk 등)·rc 내용 보존 + 백업 + grep 중복 방지.
- 남에게 줄 땐 개인 설정이 섞인 repo 전체가 아니라 **자기완결 패키지 폴더 하나만**.

## 관련 맥락

- 이 구조가 이기는 조건: [[multi-agent-orchestration-taxonomy]] — 병렬·분해엔 이득, 선형 코딩엔 marginal.
- 강/약 모델 분리·fresh-context 서브에이전트: [[ai-coding-agent-cost-and-context-patterns]].
- 모델 계층을 비용 관점으로: [[claude-code-token-optimization]]. 서브에이전트·훅 일반: [[claude-code-skills-plugins]].
- 원본 작업 명세서는 `raw-sources/claude-code-opus-orchestration-setup.md`, 그 요약은 [[claude-code-opus-orchestration-setup]] 참고. 명세서는 Sonnet 실행 계층 에이전트를 `deep-reasoner.md`로 명명하지만, 실제 구축된 이 구조에서는 그 역할이 `implementer`로 불리고 `deep-reasoner`는 반대로 soft 모드의 Opus 상향 에스컬레이션 역할로 쓰인다 — **명명 불일치**(모순이라기보다 명세 이후 설계 진화)이며 기존 내용을 정정할 근거는 아니라 취소선 없이 각주로만 남긴다.

## 변경 이력

- 2026-07-13: 실측 절 추가 — 네이티브 위임은 자발적으로 안 일어남(강모델 비위임 인센티브 · Sonnet 위임 강제는 hard 게이트뿐 · Haiku 조사 위임은 지침 기반, soft의 "굳이 위임 안 함" 문구가 조사 위임까지 억제 → 구현/조사 분리 + runner description 트리거 보강). PreToolUse payload 스키마 확정(`prompt_id` v2.1.196+ · `agent_id`/`agent_type` 서브에이전트 한정 · 서브 툴콜도 훅 탐). claude_env 멱등 install 패키징 절(모드 유지 · 레거시 비자동매핑 · 가짜 HOME 검증 · 플러그인 한계) (출처: session-log ccc7).
- 2026-07-12: 관련 맥락에 원본 명세서(raw-sources/claude-code-opus-orchestration-setup.md) 및 요약([[claude-code-opus-orchestration-setup]]) 링크 추가. 명세서의 `deep-reasoner`(Sonnet 실행) 명명과 실제 구축본의 `implementer`/`deep-reasoner`(Opus 에스컬레이션) 명명 불일치를 각주로 기록 (모순 아님, 기존 내용 변경 없음).
- 2026-07-11: 모드 전환(off/hard/soft) 도입 — 두 지향을 하나의 `opus-orchestration {off|hard|soft}` 토글로
  통합. 에이전트 3종(implementer/deep-reasoner/runner) 상시 공존, 훅·env.sh가 상태값을 읽어 hard=차단/soft=넛지,
  hard=Opus메인/soft=Sonnet메인으로 분기. 토글 스크립트 bash3.2 호환 이슈(`${x^^}` bad substitution) `tr`로 수정. 검증 26/26.
- 2026-07-11: 구성 반전 + 게이트 소프트화 반영 — (1) Sonnet-메인 + Opus deep-reasoner(상향 에스컬레이션,
  소스 직접수정 대신 계획·판정 산출) + Haiku runner로 재구성(비용지향 기본), (2) 하드블록→소프트 넛지
  (`permissionDecision:"allow"`+`additionalContext` 비차단 조언, 임계 env화). 두 지향·비용 분석 정리. 검증 23/23.
- 2026-07-11: 최초 생성 — Opus 오케스트레이터 + Sonnet 실행자 + Haiku 러너 하드블록 구조를 직접 구축·검증(22/22) (출처: raw-sources/claude-code-opus-orchestration-setup.md)
