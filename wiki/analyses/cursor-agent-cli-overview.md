---
title: "cursor-agent CLI — claude -p 호환의 print-mode 에이전트"
domain: "personal"
sensitivity: "public"
tags: ["cursor-agent", "Claude Code", "CLI", "print-mode", "AI adapter", "multi-provider"]
created: "2026-05-20"
updated: "2026-05-20"
sources:
  - "session-logs/20260520-081411-b945-현재-AI-provider-가-claude--p-로-되어-있는데-이것을-추가로-agent.md"
confidence: "medium"
related:
  - "wiki/projects/oss-radar.md"
  - "wiki/projects/dev-blog.md"
  - "wiki/patterns/claude-md-guide.md"
---

# cursor-agent CLI — claude -p 호환의 print-mode 에이전트

Cursor 가 제공하는 비대화형 (non-interactive) 에이전트 CLI. `claude -p` 와 거의 동일한 print-mode 인터페이스를 가지므로, 기존 `claude -p` 기반 자동화 (dev-blog 의 rewrite, oss-radar 의 analyze.sh 등) 를 어댑터 한 곳만 분기하여 옵셔널하게 전환 가능. 본 페이지는 2026-05-20 oss-radar 의 `claude -p` → `cursor-agent` 다중 provider 화 검토 시점에 정리한 1차 자료.

## 설치 / 실행 파일

- 실행 파일: `~/.local/bin/cursor-agent` (alias: `~/.local/bin/cursor`)
- 검증: `command -v cursor-agent` 로 PATH 확인

## 호출 형태

```bash
cursor-agent [options] [command] [prompt...]
```

기본은 대화형 (interactive). 자동화에서는 `-p/--print` 로 비대화형 + 모든 도구 접근 (write, shell 포함) 모드 사용.

## 자동화에 쓰이는 주요 옵션

| 옵션 | 의미 | `claude -p` 와의 비교 |
|------|------|---------------------|
| `-p, --print` | 응답을 stdout 으로 출력 (스크립트용). 도구 접근 권한 포함 | 동일 (`claude -p`) |
| `--output-format <text\|json\|stream-json>` | 출력 포맷 선택 (`--print` 와 함께 사용) | 동일한 키워드와 의미 |
| `--stream-partial-output` | 부분 출력을 텍스트 델타로 스트리밍 (`--print` + `stream-json` 한정) | claude 의 `--print-stream` 과 대응 |
| `--mode <plan\|ask>` | plan = read-only/분석, ask = Q&A | claude 의 plan-mode 와 유사 |
| `--plan` | `--mode=plan` 단축형 | — |
| `--resume [chatId]` | 세션 재개 | claude 의 `--resume` 와 대응 |
| `--continue` | 직전 세션 이어가기 | claude 의 `--continue` 와 대응 |
| `--model <name>` | 모델 선택 (예: `gpt-5`, `sonnet-4`, `sonnet-4-thinking`) | claude 는 자체 모델만, cursor 는 멀티-모델 가능 |
| `--list-models` | 사용 가능한 모델 나열 | 부분 대응 |
| `-f, --force` | 명시적으로 거부되지 않은 한 명령 허용 | claude 의 `--dangerously-skip-permissions` 와 대조 (cursor 는 「allowlist 기본 + force 옵트인」 모델) |
| `--yolo` | 모든 권한 자동 허용 (위험 옵션) | claude 의 `--dangerously-skip-permissions` 와 동치 |
| `--api-key <key>` / `CURSOR_API_KEY` env | API 키 인증 | claude 는 `ANTHROPIC_API_KEY` 또는 OAuth |
| `-H, --header "Name: Value"` | 커스텀 헤더 (반복 가능) | claude 에는 직접 대응 없음 |

## 어댑터 레이어 설계 시사점

dev-blog 의 [[dev-blog#기본-ai-어댑터-cursor--claude-일괄-전환-2026-05-16|resolveAiAdapter 응집]] 사례와 동일하게, **한 함수에 default 를 첫 인자로 둔 어댑터 진입점** 만 있으면 다음 두 어댑터를 한 곳에서 분기 가능:

| 추상화된 인터페이스 | claude 매핑 | cursor 매핑 |
|------------------|-----------|-----------|
| `print(prompt, model, format)` | `claude -p "$prompt" --model "$model" --output-format "$format"` | `cursor-agent -p "$prompt" --model "$model" --output-format "$format"` |
| `auth-check()` | `claude -p "Reply with only: OK"` | `cursor-agent -p "Reply with only: OK"` |
| `env unset` | `env -u CLAUDECODE claude ...` (중첩 세션 방지) | `cursor-agent` 는 별도 unset 불필요 (현재 관찰 기준, 변경될 수 있음) |

oss-radar 의 `src/analyze.sh` 는 `env -u CLAUDECODE claude -p "$FULL_PROMPT" --model "$MODEL" --output-format text > "$OUTPUT_FILE"` 형태. cursor 추가 시 `AI_AGENT=claude|cursor` 환경변수로 분기, `MODEL` 도 provider 별로 매핑 (claude → sonnet/opus, cursor → gpt-5/sonnet-4 등).

## 트레이드오프

- **장점**: claude 의 단발 무응답 (`assistant_turns: 0`) 빈도가 dev-blog / oss-radar 에서 운영 관찰됨 → 백업 provider 가 있으면 그날치 산출물 살림. 또한 cursor 는 GPT 계열 (`gpt-5`) 도 호출 가능해 비교 평가가 쉬움.
- **단점**: cursor-agent 도 자체 산발적 실패가 존재 (dev-blog 의 2026-05-16 「매일 한두 토픽씩 산발 실패」 관찰이 cursor 어댑터 자체). provider 다중화는 *완화* 일 뿐 근본 해결이 아니므로, 어댑터 fallback 체인 + retry-with-dump ([[llm-json-parse-retry-with-dump]]) 와 같이 가야 한다.
- **인증 모델 차이**: claude 는 Anthropic Pro/Max OAuth 또는 `ANTHROPIC_API_KEY`, cursor 는 `CURSOR_API_KEY`. 자동화 환경에서는 둘 다 launchd 의 `~/Library/LaunchAgents/*.plist` 평문이 아니라 `config/.env` 분리 ([[launchd-secret-management]]) 가 필요.

## 적용 후보 영역

- `oss-radar/src/analyze.sh` — 단일 `claude -p` 호출 → 어댑터 분기 (2026-05-20 plan 단계)
- `dev-blog/scripts/lib/ai-rewrite-adapter.mjs` — 이미 `claude / cursor / template` 3-way 어댑터 도입 완료 (dev-blog 의 2026-05-16 일괄 전환 사례)
- `research-wiki` 의 논문 분석 cron — 동일 패턴 적용 가능, 미구현

## 변경 이력

- 2026-05-20: 최초 생성 — oss-radar 의 `claude -p` 외 옵셔널 provider 추가 검토 세션 (`~/.claude/plans/ai-provider-zazzy-elephant.md` 작성) 에서 분리. cursor-agent `--help` 출력 (truncated) 의 옵션 표만 신뢰. 출처: session-logs/20260520-081411-b945-*
