---
title: "OpenClaw ACP Runtime 내부 구조 — plugins.allow / 좀비 task / sessions.json / wrapper 환경 상속"
domain: "ai-agent"
sensitivity: "public"
tags: ["openclaw", "acp", "acpx", "claude-code", "runtime", "security", "plugin"]
created: "2026-05-07"
updated: "2026-05-07"
sources:
  - "session-logs/20260507-011504-7932-지즘-openclaw-agent-중에-coding-agent-인-맥코더가-텔레그램-채팅-창.md"
confidence: "high"
related:
  - "wiki/projects/openclaw.md"
  - "wiki/bugs/openclaw-coder-silent-3-layer.md"
  - "wiki/decisions/openclaw-coder-default-model-codex.md"
  - "wiki/analyses/mcp-config-secret-exposure-via-ps.md"
  - "wiki/concepts/openclaw-agent-architecture.md"
---

# OpenClaw ACP Runtime 내부 구조

OpenClaw 가 Claude / Codex 등 외부 코딩 에이전트를 호출할 때 사용하는 ACP (Agent Control Protocol) runtime 의 내부 동작 정리. 2026-05-07 발생한 코더 응답 무 사건 (3계층 진단) 으로 드러난 4가지 함정 — `plugins.allow` 정책, 좀비 task 의 chicken-and-egg, `sessions.json` 의 stale ACP binding, ACP wrapper 의 user 환경 상속 — 을 한 곳에 모아둔다.

## 1. acpx plugin 과 `plugins.allow` 의 함정

OpenClaw 2026.5.3 이후 보안 정책으로 **`plugins.allow` 가 비어있으면 non-bundled plugin (전역 `~/.openclaw/extensions/` 에 설치된 것) 의 runtime register 가 차단**된다.

| 신호 | 의미 |
|------|------|
| 로그에 `[plugins] plugins.allow is empty; discovered non-bundled plugins may auto-load: acpx ...` | acpx 가 load 는 됐지만 register 는 차단됨 |
| `openclaw plugins inspect acpx` → `Status: loaded` | inspect 는 단순 load 검증이라 통과 |
| `openclaw plugins doctor` → `No plugin issues detected` | doctor 도 이 케이스를 잡지 못함 |
| `openclaw tasks cancel <id>` → `ACP runtime backend is not configured` | 실제 ACP 호출 시점에야 표면화 |

**해결**:

```bash
openclaw config set plugins.allow '["acpx","telegram","copilot-proxy"]'
openclaw daemon restart
# 로그에 다음 두 줄이 떠야 정상:
#   "embedded acpx runtime backend registered lazily"
#   "http server listening (2 plugins: acpx, telegram; 1.5s)"
```

함정:

- `plugins.allow` 에 들어가는 키는 **plugin short id** (예: `acpx`, `telegram`) 이다. `@openclaw/telegram` 같은 npm 풀네임은 "plugin not found" 가 된다.
- `set` 명령 출력의 워닝은 **stale snapshot** 일 수 있다. 실제 적용 여부는 `~/.openclaw/openclaw.json` 의 `plugins.allow` 직접 확인 + `plugins doctor` 로 검증.
- bundled plugin (`stock:` prefix) 은 원칙적으로 allowlist 면제지만, `plugins.entries` 에 명시 등록된 경우 allowlist 검사에 걸려 `daemon restart` 시 같이 죽을 수 있다 — telegram 봇이 통째로 죽는 위험.

## 2. 좀비 ACP task chicken-and-egg

ACP child session (Claude / Codex 하네스 프로세스) 이 죽었는데 task DB 의 `status` 가 `running` 으로 남으면, 다음 cancel 시도가 모두 실패하는 상태에 빠진다.

```
tasks cancel  → 살아있는 child handle 에 위임 → child 없음 → "ACP runtime backend is not configured"
flow cancel   → child task active 라며 거부
maintenance --apply → stale_running prune 미지원 (2026.5.3 시점)
```

해결: **sqlite 직접 편집** (sqlite3 의 status 컬럼만 `running` → `cancelled` 로 update). 위험 차단:

```bash
TS=$(date +%Y%m%d-%H%M%S)
cp ~/.openclaw/tasks/runs.sqlite          ~/.openclaw/tasks/runs.sqlite.bak.$TS
cp ~/.openclaw/flows/registry.sqlite      ~/.openclaw/flows/registry.sqlite.bak.$TS

# 좀비 task 만 정확히 지정 + status='running' 가드
sqlite3 ~/.openclaw/tasks/runs.sqlite \
  "UPDATE task_runs SET status='cancelled' WHERE task_id='<TASK_ID>' AND status='running';"
sqlite3 ~/.openclaw/flows/registry.sqlite \
  "UPDATE flow_runs SET status='cancelled' WHERE flow_id='<FLOW_ID>' AND status='running';"

openclaw daemon restart
openclaw tasks list --runtime acp   # 0건이거나 cancelled
openclaw tasks audit                 # findings 0
```

원칙:

- `sqlite3 changes()` 는 별도 connection 이라 0 으로 보일 수 있음. **AFTER select 로만 검증**.
- gateway 가 in-memory 상태를 캐싱하므로 **반드시 daemon restart** 후 검증.
- task 가 12일 이상 묵었으면 `cancelRequestedAt` 이 찍혀 있어도 cancel 자체가 stuck 가능성. 사용자 명시 승인 후에만 진행.

## 3. `sessions.json` 의 stale ACP binding

`/acp spawn --bind here` 로 만든 ACP binding (`agent:<id>:acp:binding:<channel>:<account>:<hash>`) 는 wrapper child process 가 종료되어도 **`~/.openclaw/agents/<id>/sessions/sessions.json` 에 영구 남는다**.

증상:
- ACP wrapper / claude binary 자식들 모두 종료됨 (`ps -ef` 에 없음)
- `tasks list --runtime acp` → 0건
- 하지만 새 메시지 보내도 응답이 (텔레그램에) 안 옴
- 코더 trajectory 가 갱신되지만 응답이 어딘가로 사라짐 (또는 [[mcp-config-secret-exposure-via-ps]] 에서 다루는 카카오톡 미스라우팅 케이스 발생)

해결: stale binding 키만 골라 제거 + restart.

```bash
SF=~/.openclaw/agents/coder/sessions/sessions.json
cp "$SF" "$SF.bak.$(date +%Y%m%d-%H%M%S)"
python3 -c "
import json
SF='$SF'
with open(SF) as f: d=json.load(f)
removed=[k for k in list(d) if 'acp:binding' in k]
for k in removed: del d[k]
with open(SF,'w') as f: json.dump(d,f,indent=2)
print('Removed:', removed)
"
openclaw daemon restart
```

## 4. ACP wrapper 의 user 환경 상속 (보안 함정)

acpx wrapper (`~/.openclaw/acpx/claude-agent-acp-wrapper.mjs`) 가 claude binary 를 spawn 할 때 다음 옵션을 박는다:

```
--setting-sources=user,project,local
--allow-dangerously-skip-permissions
--permission-mode auto
```

결과:

- 사용자 user-level Claude Code 환경 (`~/.claude/settings.json` + `~/.claude/plugins/` + Claude.ai connector) 을 **모두 상속**
- 텔레그램에서 들어온 임의 입력이 사용자 자격증명으로 user-level MCP 전부에 접근 가능 (Gmail / Calendar / Drive / KakaotalkChat 등 Claude.ai connector)
- `--allow-dangerously-skip-permissions` 라 권한 prompt 없이 자동 실행
- 모델이 "응답 도구"를 잘못 골라 텔레그램 stdout 대신 카카오톡 MCP 등으로 응답 미스라우팅

또한 `--mcp-config` 옵션에 NOTION_API_KEY 등의 시크릿이 **인라인 JSON 평문**으로 전달돼 `ps -ef` 에 노출 — [[mcp-config-secret-exposure-via-ps]] 참고.

권장 격리:

- ACP 자제 정책 — 일반 메시지는 embedded 모델 (Codex 등) 으로 처리하고 `/acp spawn` 은 신뢰된 사용자가 명시적으로 호출할 때만
- 격리된 `~/.claude` (별도 HOME) 또는 `--setting-sources=project,local` 로 user 제외 (acpx upstream 개선 필요)
- 시크릿은 `--mcp-config-file <path>` 형태 (CLI 옵션 추가 시) 또는 환경변수로 주입

## 4계층 디버깅 체크리스트

코더 응답이 침묵할 때 점검 순서:

1. **plugins.allow** — `openclaw config get plugins.allow` 가 `["acpx", ...]` 포함하고 로그에 `embedded acpx runtime backend registered lazily` 가 떴는지
2. **좀비 task** — `openclaw tasks list --runtime acp` 0건, `audit` findings 0
3. **인증** — gateway 로그에 `lane task error: ... HTTP 403 OAuth authentication is currently not allowed` 가 있는지 (Anthropic OAuth 거부 등). 모델별 fallback 0개라면 silently 침묵 → [[openclaw-coder-silent-3-layer]]
4. **stale ACP binding** — `~/.openclaw/agents/<id>/sessions/sessions.json` 에 `acp:binding` 키가 남아있는지
5. **모델-prompt 미스매치** — trajectory 의 마지막 assistant 응답이 빈 텍스트 (`text=""`, `output: 4 tokens, stopReason: stop`) 라면 모델이 호명만 보고 응답 불필요로 판정. system prompt 가 다른 모델 (예: Claude Opus) 용이고 현재 GPT-5.5 가 호명에 약하게 반응할 가능성

## 변경 이력

- 2026-05-07: 최초 작성 (세션 로그 20260507-011504-7932 의 OpenClaw 코더 응답 무 3계층 디버깅에서 추출)
