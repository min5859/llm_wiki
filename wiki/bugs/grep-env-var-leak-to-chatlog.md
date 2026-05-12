---
title: "`grep \"KEY\" ~/.zshrc` 로 환경변수 시크릿이 채팅 로그에 유출"
domain: both
sensitivity: public
tags: ["security", "secrets", "shell", "grep", "env-var", "claude-code", "incident"]
created: 2026-05-13
updated: 2026-05-13
sources:
  - "session-logs/20260512-214455-2046-*.md"
confidence: high
related:
  - "wiki/analyses/mcp-config-secret-exposure-via-ps.md"
  - "wiki/patterns/claude-mcp-server-scope-and-add-json.md"
---

# `grep "KEY" ~/.zshrc` 로 환경변수 시크릿이 채팅 로그에 유출

`.zshrc` 의 `export NOTION_API_KEY=ntn_xxxxxx...` 라인이 셸에 잡혔는지 *길이만* 확인하려고 했는데, `grep -c "NOTION_API_KEY" ~/.zshrc` 가 라인 전체를 출력하면서 시크릿이 **AI 채팅 로그에 그대로 남는** 사고. 키는 그 시점부터 손상된 것으로 간주하고 즉시 폐기·회전해야 한다.

## 사고 재현

```bash
$ echo "현재 셸 NOTION_API_KEY=${NOTION_API_KEY=*** — 현재 셸에는 아직 안 보임}"; \
  echo "---"; \
  grep -c "NOTION_API_KEY" ~/.zshrc 2>/dev/null && echo ".zshrc 에 항목 있음"
현재 셸 NOTION_API_KEY=*** — 현재 셸에는 아직 안 보임
---
1 matches in 1F:

[file] /Users/wooki/.zshrc (1):
    21: export NOTION_API_KEY=ntn_46xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
.zshrc 에 항목 있음
```

`grep -c` 를 의도했지만 (셸 wrapper / `rg` alias 가) 매치 라인을 같이 출력해 **API key 전체가 그대로 노출**.

## 원인

- `grep -c` 의 의도는 매치 카운트만이지만 환경에 따라 wrapper 가 매치 라인까지 같이 보여줌 (또는 사용자가 `-c` 가 아닌 `-cn` 등을 잘못 쓰는 경우)
- AI 어시스턴트가 명령을 *대신 실행*했을 때, 출력은 채팅 transcript / Anthropic 세션에 그대로 적재된다
- 이 transcript 는 사용자가 공유하거나 backup 으로 남으면 **노출 통로**가 된다

## 즉시 조치 (incident response)

1. **노션 등 발급처에서 해당 토큰 revoke / rotate** — 채팅에 한 번 들어간 키는 손상된 것으로 간주
   - 노션: https://www.notion.so/my-integrations → Integration → Secrets → Rotate
2. **새 키로 `.zshrc` 갱신** 후 새 셸 띄우기 (`source ~/.zshrc` 또는 새 터미널)
3. **새 키 length 만 확인** — `echo "len=${#NOTION_API_KEY}"` (값 자체는 절대 echo 하지 말 것)

## 안전한 환경변수 검증법

```bash
# ✅ 길이만 — 값은 노출되지 않음
echo "len=${#NOTION_API_KEY}"

# ✅ 존재 여부만
[ -n "$NOTION_API_KEY" ] && echo "set" || echo "unset"

# ✅ 앞 몇 글자만 (필요시)
echo "${NOTION_API_KEY:0:4}..."

# ❌ 절대 하지 말 것
echo "$NOTION_API_KEY"
grep "NOTION_API_KEY" ~/.zshrc            # 라인 전체 노출
env | grep NOTION                         # 마찬가지
```

`.zshrc` 에 라인이 있는지 *존재 확인*만 하고 싶다면:

```bash
grep -q "^export NOTION_API_KEY" ~/.zshrc && echo "found" || echo "missing"
```

`-q` 는 quiet 모드라 매치 라인을 출력하지 않는다.

## AI 어시스턴트 측 위생

- 셸의 `grep` 동작이 라인 전체를 같이 출력해버릴 가능성을 미리 가정하고, 시크릿 환경변수의 길이/존재만 확인하는 명령을 *처음부터 안전한 형태*로 작성한다
- 채팅 로그에서 시크릿 추정값 (`ntn_*` `sk-*` 등의 패턴) 을 발견하면 **자동 마스킹** (e.g. `ntn_46xx...emxxxF`) 후 출력
- 사용자에게 즉시 사고를 알리고 폐기/회전 절차를 안내한다 — 사후 reactive 가 아니라 어시스턴트 측에서 능동적으로 경고

## 관련 패턴 — MCP `--mcp-config` 평문 노출

별도 통로지만 같은 부류의 위험: Claude Code / OpenClaw 가 child claude binary 를 spawn 할 때 MCP `env` 시크릿이 `--mcp-config` 인자에 인라인 JSON 평문으로 박혀 `ps -ef` 에 *세션이 살아있는 동안 지속* 노출된다. 자세한 메커니즘과 대응은 [[mcp-config-secret-exposure-via-ps]] 참고.

## 일반 원칙

- 시크릿 환경변수 검증은 **값 → 메타데이터로 환원** (길이 / 존재 / prefix) 한 형태로만 출력
- `grep` 은 기본적으로 매치 라인을 출력하는 도구 — 환경변수 노출 위험 변수에는 **`grep -q`** 또는 별도 검증 명령 사용
- AI 어시스턴트가 자동 실행하는 진단 명령일수록 *시크릿이 흘러나갈 통로가 없도록* 사전 설계할 것

## 변경 이력

- 2026-05-13: 최초 작성 (출처: session-logs/20260512-214455-2046 — Notion MCP 등록 진단 중 `grep -c "NOTION_API_KEY" ~/.zshrc` 한 줄로 API key 가 채팅 로그에 노출된 실 사고 기록. 어시스턴트가 즉시 키 회전 + MCP 재등록을 안내)
