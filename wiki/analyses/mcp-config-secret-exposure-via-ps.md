---
title: "MCP 토큰이 `--mcp-config` 인자로 평문 노출되는 패턴 (`ps -ef` 가시성)"
domain: "personal"
sensitivity: "public"
tags: ["security", "secrets", "mcp", "claude-code", "openclaw", "process-args"]
created: "2026-05-07"
updated: "2026-05-07"
sources:
  - "session-logs/20260507-011504-7932-지즘-openclaw-agent-중에-coding-agent-인-맥코더가-텔레그램-채팅-창.md"
confidence: "high"
related:
  - "wiki/analyses/openclaw-acp-runtime-internals.md"
  - "wiki/patterns/launchd-secret-management.md"
---

# MCP 토큰이 `--mcp-config` 인자로 평문 노출되는 패턴

Claude Code (그리고 그를 ACP 로 감싸는 OpenClaw 등) 는 MCP 서버 설정을 자식 프로세스에 전달할 때 `--mcp-config` 옵션에 **JSON 을 인라인 평문**으로 박는다. JSON 안의 `env` 필드 (예: `NOTION_API_KEY`) 가 **`ps -ef` 출력에 그대로 노출**된다. 단발 노출이 아니라 **세션이 살아있는 동안 지속 노출**되는 패턴.

## 노출 메커니즘

ACP wrapper / Claude Code 가 child claude binary 를 spawn 할 때:

```
... claude --output-format stream-json --verbose ...
    --mcp-config {"mcpServers":{"notion":{"type":"stdio","command":"npx",
                  "args":["-y","@notionhq/notion-mcp-server"],
                  "env":{"NOTION_API_KEY":"ntn_xxxxxxxx..."}}}}
    --setting-sources=user,project,local --permission-mode auto ...
```

`--mcp-config` 의 값으로 JSON 이 통째로 들어가 있고, 그 JSON 의 `env.NOTION_API_KEY` 에 토큰 평문이 들어간다.

`ps -ef` 의 `command` 컬럼은 Unix/macOS 기본 동작상 **같은 머신의 다른 사용자도 읽을 수 있다**. 따라서 다음 통로로 노출:

| 통로 | 위험도 |
|------|--------|
| 같은 머신의 다른 로컬 사용자 / 게스트 / SSH | 높음 (해당될 때) |
| `ps` 를 주기적 수집하는 모니터링 / EDR 에이전트 | 중간 (도입돼 있을 때) |
| 디버깅 중 사용자가 `ps -ef` 출력을 캡처 → 채팅 / 이슈 / 로그 첨부 | 중간 (실수 잦음) |
| single-user 맥미니 + 본인이 외부 공유 안 함 | 낮음 (실효 위험 거의 없음) |

## 토큰 교체로는 해결되지 않는 이유

- 토큰은 OpenClaw 의 경우 `~/.openclaw/openclaw.json` 의 `plugins.entries.acpx.config.mcpServers.<name>.env.<KEY>` 에 저장됨
- spawn 시점에 wrapper 가 그 값을 읽어 `--mcp-config` JSON 으로 다시 박음
- 토큰을 새로 발급해 config 에 갈아넣어도 다음 spawn 부터 **새 토큰이 같은 자리에서 또 노출**

즉 **노출 메커니즘 자체를 바꿔야** 한다.

## 노출 방지 옵션

| 옵션 | 효과 | 비용 |
|------|------|------|
| **A. upstream 개선** — wrapper / Claude Code 에 `--mcp-config-file <path>` 추가 요청 | 근본 해결 | 사용자가 직접 못 함 |
| **B. MCP 자체 제거** — `openclaw config unset plugins.entries.acpx.config.mcpServers.<name>` | ps 노출 차단 | 해당 MCP 사용 불가 |
| **C. ACP 비활성** — ACP 안 쓰면 wrapper 가 child 를 spawn 하지 않음 | ps 노출 차단 | ACP 외 (embedded) 모델로만 운영 |
| **D. 환경 격리** | 다른 로컬 사용자 차단 | single-user 환경에선 효과 미미 |

가장 현실적인 결론은 보통 **C + D + ACP 자제 정책** 조합. ACP 격리 작업에 들어갈 때 B 도 같이 묶어 정리.

## ps 출력을 외부에 보여줄 때의 위생

디버깅 중 `ps -ef | grep claude` 결과를 사용자 / 이슈 / 채팅에 그대로 붙이면 토큰이 같이 흘러간다. 채팅 로그는 Anthropic 세션 보관 + 사용자 본인이 transcripts 공유 시 노출 통로가 된다. AI 어시스턴트도 토큰을 발견했을 때 **마스킹 (e.g. `ntn_465...emebpF`) 후 출력**하는 위생 습관이 필요하다.

## 일반 원칙

- 비밀값은 **명령행 인자가 아니라 환경변수 / 임시파일 / stdin** 으로 전달하는 게 권장 패턴
- `--option <value>` 형태에 시크릿이 박히면, 그 프로세스가 살아있는 동안 누구나 `ps` 만으로 본다
- macOS / Linux 기본 정책상 **다른 사용자의 명령어 인자도 보임** — 윈도우는 더 엄격하지만 대신 노출 채널이 다양

## 변경 이력

- 2026-05-07: 최초 작성 (세션 로그 20260507-011504-7932 OpenClaw 코더 디버깅 중 NOTION_API_KEY ps 노출 발견)
