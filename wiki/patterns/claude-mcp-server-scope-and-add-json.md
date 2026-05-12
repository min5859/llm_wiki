---
title: "Claude Code MCP 서버 등록 — `-e` 파서 함정과 `add-json`, scope 차이"
domain: both
sensitivity: public
tags: ["claude-code", "mcp", "cli", "scope", "shell-parsing", "notion"]
created: 2026-05-13
updated: 2026-05-13
sources:
  - "session-logs/20260512-214455-2046-*.md"
confidence: high
related:
  - "wiki/analyses/mcp-config-secret-exposure-via-ps.md"
  - "wiki/bugs/grep-env-var-leak-to-chatlog.md"
---

# Claude Code MCP 서버 등록 — `-e` 파서 함정과 `add-json`, scope 차이

`claude mcp add` 로 stdio MCP 서버 (예: Notion `@notionhq/notion-mcp-server`) 를 등록할 때 자주 부딪히는 두 가지 함정 — **(1) `-e KEY=VALUE` 옵션의 파서 모호함**, **(2) 기본 scope 인 `local` 의 디렉터리 격리**. `add-json` + `-s user` 조합이 가장 깔끔한 해결.

## 함정 1 — `-e KEY=VALUE` 의 셸 토큰 분해 모호함

`-e` 옵션은 zsh/bash 의 토큰화에 영향을 받아 다음과 같은 에러가 자주 발생한다.

```
$ claude mcp add -e NOTION_TOKEN=$NOTION_API_KEY notion -- npx -y @notionhq/notion-mcp-server
Invalid environment variable format: notion, environment variables should be added as: -e KEY1=value1 -e KEY2=value2
```

- 원인: `$NOTION_API_KEY` 가 비어 있거나, 셸이 `KEY=VALUE` 토큰을 `notion` 등 후속 위치 인자와 섞어 해석
- 우회 A: 따옴표로 감싸기 — `-e "NOTION_TOKEN=$NOTION_API_KEY"`
- 우회 B (권장): **`add-json` 으로 전체 구조를 JSON 으로 넘김**

### 권장 — `add-json`

```bash
claude mcp add-json -s user notion "$(cat <<EOF
{
  "command": "npx",
  "args": ["-y", "@notionhq/notion-mcp-server"],
  "env": {"NOTION_TOKEN": "$NOTION_API_KEY"}
}
EOF
)"
```

JSON 구조로 넘기면 인자 파싱 모호함이 사라진다. `command` / `args` / `env` 가 명확하게 분리됨.

### 토큰 분해 진단

`-e` 옵션 에러가 났을 때 셸이 명령을 어떻게 잘랐는지 한 번에 보는 트릭:

```bash
print -l -- claude mcp add -e NOTION_TOKEN=$NOTION_API_KEY notion -- npx -y @notionhq/notion-mcp-server
```

각 토큰이 한 줄씩 출력되므로 `NOTION_TOKEN=...` 이 한 줄로 묶였는지, 빈 값으로 두 토큰으로 쪼개졌는지 즉시 확인 가능.

## 함정 2 — 기본 scope `local` 의 디렉터리 격리

`claude mcp add` 의 `-s` 기본값은 **`local`** 이고, 이는 **명령 실행한 디렉터리 단위로 격리**된다.

```
$ pwd
/Users/wooki

$ claude mcp add-json notion '...'      # -s 생략 → local scope, /Users/wooki 에만 묶임

$ cd ~/project/foo
$ claude mcp list                       # 여기서는 notion 안 보임
```

`~/.claude.json` 의 저장 위치가 scope 별로 다르다.

```
~/.claude.json
├── mcpServers              ← user 스코프
└── projects
    └── /Users/wooki        ← local 스코프 (명령 실행한 디렉터리 키)
        └── mcpServers
            └── notion
```

어디서 Claude Code 를 띄워도 같은 MCP 서버를 보려면 **`-s user`** 로 등록해야 한다.

### scope 변경 — local → user

```bash
claude mcp remove notion                                              # 기존 local 제거
claude mcp add-json -s user notion '{"command":"npx", ... }'          # user 로 재등록
claude mcp list                                                       # ✓ Connected 확인
```

### scope 진단 — `~/.claude.json` 직접 확인

```bash
python3 -c "
import json
d = json.load(open('/Users/wooki/.claude.json'))
print('top-level mcpServers (user):', list(d.get('mcpServers', {}).keys()))
for path, p in d.get('projects', {}).items():
    if isinstance(p, dict) and p.get('mcpServers'):
        print(f'project [{path}] (local):', list(p['mcpServers'].keys()))
"
```

`top-level mcpServers (user): ['notion']` 이 정상 결과.

## 함정 3 — 새 MCP 서버는 *세션 시작 시* 만 로드됨

등록 후 `✓ Connected` 가 떠도, **현재 채팅 세션은 등록 전에 시작되어** 새 MCP 도구를 못 본다.

> 새 MCP 서버는 Claude Code 세션이 시작될 때 한 번 로드됩니다.

→ **Claude Code 종료 → 다시 시작** 이 필요.

## 함정 4 — 외부 MCP + API key 등록은 자동 승인 거부

Claude Code 의 자동 모드 분류기가 다음과 같은 명령을 종종 차단한다.

> 외부 MCP 패키지를 API key 와 함께 등록하는 것은 사용자가 직접 승인해야 하는 동작이라, 제가 대신 실행할 수 없습니다.

이런 경우 어시스턴트가 명령을 사용자 셸에 그대로 출력하고 사용자가 직접 실행해야 한다.

## 보안 — Notion 페이지 연결과 API key 위생

- Integration Token (API key) 만으로는 부족 — 페이지 우측 상단 `...` → `연결` → 만든 Integration 선택해야 API 가 페이지를 본다
- API key 를 진단 명령으로 노출하지 말 것 — `grep -c "NOTION_API_KEY" ~/.zshrc` 같은 명령이 실제 키 값을 채팅 로그에 흘릴 수 있다. 길이만 확인하려면 `echo "len=${#NOTION_API_KEY}"` 만 사용한다. 자세한 사고 패턴은 [[grep-env-var-leak-to-chatlog]] 참고
- 등록된 MCP 서버의 `env` 는 `--mcp-config` 인라인 JSON 으로 child 프로세스에 전달되며 `ps -ef` 에 노출된다. 자세한 패턴은 [[mcp-config-secret-exposure-via-ps]] 참고

## 변경 이력

- 2026-05-13: 최초 작성 (출처: session-logs/20260512-214455-2046 — Notion MCP 등록 중 `-e` 파서 에러 + local scope 함정 + 세션 재시작 요구 + 자동 승인 거부 4가지 함정 한 묶음으로 발생)
