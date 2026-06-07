---
title: "Claude Code 네이티브 세션 로그 (~/.claude/projects/*.jsonl) 포맷 — 주간보고·기억 에이전트의 원천 데이터"
domain: both
sensitivity: public
tags: ["claude-code", "session-log", "jsonl", "agent", "data-source", "weekly-report"]
created: 2026-06-07
updated: 2026-06-07
sources:
  - "session-logs/20260607-095133-951f-*.md"
confidence: medium
related:
  - "wiki/concepts/gieok.md"
  - "wiki/projects/agent-weekly.md"
  - "wiki/analyses/claude-code-tui-navigation-and-instance-isolation.md"
---

# Claude Code 네이티브 세션 로그 포맷

Claude Code 는 모든 세션을 자체적으로 `~/.claude/projects/` 아래에 JSONL 로 보존한다.
gieok 의 Hook 기반 `session-logs/*.md` 와 별개로, **CLI 본체가 직접 남기는 1차 원본**이다.
"대화 내용을 모두 기록해 주간보고로 작성하는 에이전트"처럼 과거 활동을 재구성하는 도구는 이 파일을 원천 데이터로 삼을 수 있다.

## 경로·파일명 규칙

```
~/.claude/projects/<encoded-cwd>/<session-uuid>.jsonl
```

- `<encoded-cwd>`: 작업 디렉터리 절대경로의 슬래시(`/`)를 하이픈(`-`)으로 치환한 것
  (예: `/Users/wooki/project/git/wk/agent-weekly` → `-Users-wooki-project-git-wk-agent-weekly`)
- 파일명 = 세션 UUID + `.jsonl` (1 세션 = 1 파일)
- 한 프로젝트 디렉터리당 다수의 세션 jsonl 이 누적된다 (수십~수백 개)
- 크기는 세션 길이에 비례 (관측 샘플: 7줄 32K ~ 497줄 1.1M)

## 라인 구조 — type 필드로 분기

각 줄은 독립된 JSON 객체이며 `type` 필드를 가진다. 한 세션의 type 분포 관측 예:

| type | 의미 |
|------|------|
| `user` | 사용자 입력 메시지 |
| `assistant` | 모델 응답 |
| `attachment` | 첨부/컨텍스트 주입 |
| `ai-title` | 세션 자동 제목 |
| `last-prompt` | 마지막 프롬프트 추적 |
| `mode` / `permission-mode` | 모드·권한 모드 전환 |
| `file-history-snapshot` | 파일 이력 스냅샷 |

일부 줄은 키가 `['type', 'leafUuid', 'sessionId']` 뿐인 최소 레코드(요약/포인터)다.
따라서 파싱 시 **type 별로 키 존재를 가정하지 말고 분기**해야 한다.

### user 레코드의 키

```
parentUuid, isSidechain, promptId, type, message, uuid, timestamp,
permissionMode, promptSource, userType, entrypoint, cwd, sessionId,
version, gitBranch
```

- `message.content` 는 **문자열(str) 또는 배열(array)** 둘 다 가능 → 양쪽 처리 필요
- `parentUuid` 로 메시지 트리(분기·sidechain)를 복원할 수 있다
- `cwd` / `gitBranch` / `version` 이 각 레코드에 박혀 있어 사후에 프로젝트·브랜치 귀속이 가능

## 타임존 함정

`timestamp` 는 **UTC ISO8601 (`...Z`)** 로 기록된다.
예: KST 09:51:33 세션이 `2026-06-07T00:51:33.926Z` 로 남는다.
일자별 집계(주간보고 등)를 KST 기준으로 할 때는 +9h 변환 후 날짜 경계를 잡아야 한다
(자정 근처 KST↔UTC 롤오버 주의 — [[utc-iso-date-kst-rollover]] 와 동일 부류의 함정).

## 활용 시사점

- 주간보고/활동 요약 에이전트는 별도 로깅 Hook 없이도 이 jsonl 만으로 과거를 재구성할 수 있다
  (단 gieok 의 `.md` 는 마스킹·블록리스트가 적용된 정제본, jsonl 은 원본 그대로라 시크릿 노출 위험이 더 큼)
- mtime 정렬(`stat -f '%m %N'`)로 최근 세션을 빠르게 추릴 수 있다
- type 분포가 세션마다 다르므로 `user`/`assistant` 만 추출해 대화 흐름을 만들고, 나머지는 메타로 취급하는 것이 실용적

## 변경 이력

- 2026-06-07: agent-weekly 아이디어 구체화 세션에서 jsonl 포맷 조사 결과 신규 작성
