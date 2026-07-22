---
domain: "ai-agent"
sensitivity: "public"
title: gieok — 프로젝트 설계 상세
tags: [project, gieok, claude-code, automation, launchagent]
created: 2026-04-22
updated: 2026-07-23
sources:
  - "session-logs/20260422-002046-60a1-*.md"
  - "session-logs/20260702-235052-ea52-근데-내가-처음-질문했던거는-llm-wiki-를-잘-셋업하는-방법을-물어봤는데-이걸-어떻게.md"
  - "session-logs/20260712-000307-1627-llm_wiki-와-llm_wiki2-비교해줘.md"
  - "session-logs/20260723-041432-7948-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
confidence: "high"
related:
  - "wiki/concepts/gieok.md"
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/analyses/personal-llm-wiki-curation.md"
  - "wiki/bugs/gieok-session-log-url-credential-masking-false-positive.md"
---

## 프로젝트 개요

gieok은 Claude Code 세션 기록을 Obsidian Wiki에 자동 축적하는 개발자 도구.
npm 패키지로 배포되며, Node.js 18+ 표준 모듈만 사용해 외부 의존성이 없다.

- **설치**: `npx gieok --vault <vault-path>` (또는 로컬 소스에서 `bash install.sh`)
- **요구사항**: Claude Code Max 플랜, Obsidian, jq

## 주요 컴포넌트

```
bin/gieok.mjs                  — npm 엔트리포인트 (install.sh 래퍼, 39줄)
hooks/
  session-logger.mjs           — 세션 I/O 캡처 + 비밀 정보 마스킹
  wiki-context-injector.mjs    — SessionStart 시 wiki/index.md 주입
mcp/
  server.mjs + tools/          — BM25+시맨틱 검색, PDF/URL ingest MCP 서버
scripts/
  install-hooks.sh             — CC settings.json에 Hook 등록
  setup-vault.sh               — Obsidian Vault 초기화
  auto-ingest.sh               — LLM 기반 로그→Wiki 변환
skills/
  wiki-ingest, wiki-ingest-all — CC 슬래시 커맨드
tests/                         — 쉘+ESM 혼합 테스트 (~30개 파일)
```

## CC Hook 이벤트 (4개)

| 이벤트 | 발생 시점 |
|--------|----------|
| `UserPromptSubmit` | 사용자가 메시지를 보낼 때 |
| `Stop` | Claude가 응답을 완료할 때 |
| `PostToolUse` | Claude가 도구(파일 읽기, 실행 등)를 쓸 때 |
| `SessionEnd` | 세션이 종료될 때 |

## LaunchAgent 자동 실행 스케줄

기본값: 하루 3회 (07:00, 13:00, 19:00)
설계 의도: "하루 세 번이면 충분히 자주, 너무 자주는 아님"

**PC를 껐다 켜도 정상 동작**: macOS launchd는 실행 예정 시간이 지나 있으면
PC 부팅 후 즉시 실행한다(catchup). PC를 항상 켜둘 필요 없음.

스케줄 변경 방법:
```bash
vi ~/Library/LaunchAgents/com.gieok.ingest.plist
# StartCalendarInterval 블록 편집 후:
launchctl unload ~/Library/LaunchAgents/com.gieok.ingest.plist
launchctl load  ~/Library/LaunchAgents/com.gieok.ingest.plist
```

## 보안 설계

- 세션 로그는 **로컬 전용** (Vault의 `.gitignore`에서 `session-logs/` 제외)
- API 키/토큰 **정규식 마스킹** (Anthropic, OpenAI, AWS, GitHub 등 주요 패턴)
- Wiki/templates만 Git으로 관리 → GitHub 노출 최소화
- 기존 `CLAUDE.md`를 보존하고, gieok 스키마는 `CLAUDE.brain.md`로 분리 생성

## 기능별 LLM 필요 여부

| 기능 | LLM 필요 | 비고 |
|------|---------|------|
| L0 세션 로그 저장 | ❌ | Node.js 스크립트만 |
| L3 Git 동기화 | ❌ | 단순 git pull/push |
| wiki 목차 주입 | ❌ | index.md 파일 읽기만 |
| L1 wiki 페이지 생성 | ✅ | `claude -p` 호출 |
| L2 wiki 무결성 검사 | ✅ | `claude -p` 호출 |
| PDF/URL ingest | ✅ | `claude -p` 호출 |

`claude -p`: Claude Code CLI를 비대화형으로 실행하는 방식. 설치된 Claude Code Max 플랜을
그대로 활용하므로 별도 API 비용이 발생하지 않는다.

### 토큰 비용 모델 — "LLM 미호출" ≠ "토큰 0"

위 표의 "❌ LLM 필요"는 **`claude -p` 를 호출하지 않는다**는 뜻일 뿐, 토큰을 안 쓴다는 뜻이 아니다. gieok 의 실제 토큰 소비는 딱 두 곳:

1. **wiki-context-injector (SessionStart hook)** — `wiki/index.md` **전문을 매 세션 시스템 프롬프트에 주입**하고, 그 이후 *모든 턴*의 입력 컨텍스트에 계속 실린다. 로컬 파일 읽기라 `claude -p` 는 안 부르지만, **비용의 본질 = index 크기 × 세션 수(× 턴 수)** 다. "로그 저장" 자체는 공짜지만 "목차 주입"은 반복 입력 비용이다.
2. **매일 auto-ingest** — `claude -p` headless 1회(하루 1회 상한, 최대 수십 턴). 구독 로그인 계정이라 API 과금이 아니라 사용량 한도에서 차감.

나머지 hook(session-logger 4종, git pull/commit)은 로컬 node/git 스크립트라 토큰을 전혀 안 쓴다.

> 실측: v1 index 55KB(≈1.4만 토큰) → v2 9KB(≈2천 토큰), **약 85% 감소**(`wc -c` 54801 → 8985 bytes). 도메인을 ai-agent·trading 2개로 좁힌 것이 index 크기까지 줄인 효과다. **index 는 문서당 제목 한 줄만 유지**하고, 크기 상한(예: 200줄 초과 경고)을 lint 단계에 두는 것이 반복 입력 비용을 억제하는 레버.

## 삭제 (uninstall)

```bash
bash ~/.local/share/gieok/repo/install.sh --uninstall
```

자동 삭제: LaunchAgent, skills 심볼릭 링크
**수동 삭제 필요**: `~/.claude/settings.json`의 Hook 항목, `~/.local/share/gieok/`
Vault 내용은 gieok 삭제 후에도 **그대로 보존**됨.

## Vault 전환 절차 (v1 → v2 실측, 2026-07-02)

vault 경로는 전부 파라미터로 주입되므로 gieok 본체는 건드리지 않고 **연결 지점 3곳**만 바꾸면 된다:

1. **`~/.claude/settings.json` hooks** — session-logger·wiki-context-injector·auto commit/push·git pull 명령에 vault 경로가 7곳 하드코딩 → 치환 후 JSON 유효성 확인
2. **LaunchAgent plist 2개** (`com.gieok.ingest`, `com.gieok.lint`) — vault 인자 수정 후 `launchctl unload/load` 재로드
3. **새 vault 사전 준비** — auto-commit hook은 `.gitignore` 에 `session-logs/` 가 있어야만 커밋하는 안전장치가 있으므로, 새 vault에 `.gitignore`·`session-logs/`·`templates/` 를 먼저 만들어야 파이프라인이 돈다

**함정: hook 설정은 세션 시작 시점에 고정된다.** 전환 전에 열려 있던 세션은 계속 구 vault에 로그를 쓴다. 해당 세션 종료 후 로그를 새 vault의 `session-logs/` 로 `mv` 하면 다음 ingest가 처리한다. ingest의 스킵 판단은 vault의 CLAUDE.md를 읽으므로, 새 vault의 수집 기준이 그대로 승격 게이트로 작동한다.

## Vault 재개명 (llm_wiki2 → llm_wiki, 2026-07-12)

vault 디렉터리명을 `llm_wiki2` → `llm_wiki` 로 개명. gieok LaunchAgent 3개(`com.gieok.ingest`·`com.gieok.lint`·`com.gieok.log-retention`)와 Claude Code `settings.json` 훅 7곳의 경로 참조를 일괄 갱신했다. `kakao-summary` 출력 경로는 원래부터 `llm_wiki` 경로에 쓰고 있었기 때문에 개명 후 별도 조치 없이 자동 일치.

> **교훈**: vault 경로는 vault 본체가 아니라 자동화 여러 곳(LaunchAgent plist, hook 설정)에 절대경로로 하드코딩되어 있다. 개명 작업은 코드 수정보다 **경로 참조 전수조사**가 먼저다 — grep 없이 진행하면 일부 automation이 구 경로를 계속 참조한 채 조용히 멈춘다.

## Session-log 보존 정책 (2026-07-03 추가)

처리 완료된 로그는 삭제되지 않고 무한 증가한다 (v1 실측: 1,031개 / 24MB, 전부 `ingested: true`인데도 보관). 해결: vault repo의 `scripts/session-log-retention.sh` + LaunchAgent `com.gieok.log-retention` 이 매일 **07:40**(ingest 07:00 이후)에 `ingested: true` 이면서 30일 경과한 로그만 삭제. 미처리(`ingested: false`) 로그는 절대 건드리지 않으며, `GIEOK_RETENTION_DRY_RUN=1` 로 dry-run 검증 가능. gieok 본체 무수정 — vault 쪽 스크립트로만 구현.

## 알려진 버그 및 수정 이력

### LaunchAgent 템플릿 이름 버그 (수정 완료)
- **증상**: LaunchAgent plist 파일 내 번들 ID가 `com.kioku`로 하드코딩되어 있었음
- **수정**: `com.kioku` → `com.gieok` 으로 템플릿 파일명 변경
- **발생 시점**: 초기 설치 과정에서 발견, 즉시 수정

### session-log credential 마스킹이 lore.kernel.org 스타일 URL 을 파괴 (관찰, 미수정)
- **증상**: session-logger 의 credential 마스킹이 `https://lore.kernel.org/bpf/20260722...-leon.hwang@linux.dev/` 같이 경로에 `<msgid>@<domain>` 을 포함한 URL 을 `https://***:***@linux.dev/` 로 파괴 — host·경로 전체가 사라지고 이메일 도메인만 남음. 같은 로그 안에서도 일부 URL은 원형 생존하는 비결정적 동작.
- **실질 피해**: 세션 로그에서 소스 URL 감사 불가 + wiki ingest 트리아지가 이를 「dossier 데이터 무결성 손상」으로 오판할 위험 (2026-07-23 사이클에서 실제 발생).
- **상태**: 관찰만, 마스킹 코드 수정 미착수. 상세는 [[gieok-session-log-url-credential-masking-false-positive]].

## 관련 페이지
- [[gieok]]
- [[macos-launchagent-catchup-behavior]]
- [[personal-llm-wiki-curation]]
- [[gieok-session-log-url-credential-masking-false-positive]]

## 변경 이력
- 2026-07-23: "알려진 버그 및 수정 이력"에 session-log credential 마스킹이 lore.kernel.org 스타일 URL 을 파괴하는 오탐 절 추가 — ingest 트리아지 오판 위험 포함, 상세는 [[gieok-session-log-url-credential-masking-false-positive]] (출처: session-logs/20260723-041432-7948-* 외 11건)
- 2026-07-12: "Vault 재개명 (llm_wiki2 → llm_wiki)" 절 추가 — LaunchAgent 3개 + settings.json 훅 7곳 경로 일괄 갱신, kakao-summary 는 자동 일치. 교훈: vault 경로는 자동화 여러 곳에 하드코딩되어 개명 전 경로 참조 전수조사 필요 (출처: session-logs/20260712-000307-1627-*)
- 2026-04-22: 최초 작성 (세션 로그 20260422-002046-60a1 에서 추출)
- 2026-07-08: "토큰 비용 모델 — 'LLM 미호출' ≠ '토큰 0'" 절 추가. wiki 목차 주입이 `claude -p` 는 안 부르지만 매 세션·매 턴 입력에 index 전문이 실림 → 비용 = index 크기 × 세션 수. v1→v2 index 85% 감소(55KB→9KB) 실측, index 는 제목 한 줄만 유지·크기 상한 lint. 토큰 최적화 일반론은 [[claude-code-token-optimization]] (출처: session-logs/20260702-235052-ea52-*)
- 2026-07-04: "Vault 전환 절차 (v1→v2)" · "Session-log 보존 정책" 절 추가 — 연결 지점 3곳(settings.json hooks 7곳·LaunchAgent 2개·새 vault 사전 준비), hook 설정은 세션 시작 시점 고정 함정, retention LaunchAgent 07:40 (출처: session-logs/20260702-235052-ea52-*)
