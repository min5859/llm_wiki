---
title: "OpenClaw — AI 에이전트 자동화 도구"
domain: "ai-agent"
sensitivity: "public"
tags: ["project", "openclaw", "ai-agent", "telegram", "automation", "npm"]
created: "2026-04-23"
updated: "2026-06-21"
sources:
  - "session-logs/20260423-113736-72aa-openclaw-를-업데이트-하려고-합니다.-가이드를-알려주세요.md"
  - "session-logs/20260621-181739-bf68-지금-openclaw-agent-의-AI-provider-연결이-끊긴것-같은데-현재-상태를.md"
  - "session-logs/20260603-140159-fbbf-지금-openclaw가-응답이-없는-것-같습니다.-왜-응답이-없는지-확인해-주세요.md"
  - "session-logs/20260423-194609-6b61-코딩전용-openclaw-agent-를-추가했는데-텔레그램으로-메세지를-보내면-응답이-없습.md"
  - "session-logs/20260426-120703-304f-현재-프로젝트는-openclaw-라는-Agent-를-사용해서-자산관리-웹앱을-구현해보고-있.md"
  - "session-logs/20260426-121630-14c3-https---bongman.tistory.com-1341-위-웹페이지-내용을-요약해-주세.md"
  - "session-logs/20260426-141208-ad61-맥비-지금-코드가-어디까지-구현되었는지-확인해-주세요.md"
  - "session-logs/20260426-184740-49b5-지금-실행되는-next.js-에서-5-issues-라고-뜹니다.-##-Error-Type.md"
  - "session-logs/20260507-011504-7932-지즘-openclaw-agent-중에-coding-agent-인-맥코더가-텔레그램-채팅-창.md"
confidence: "high"
related:
  - "wiki/projects/gieok.md"
  - "wiki/analyses/openclaw-telegram-group-setup.md"
  - "wiki/analyses/openclaw-acp-runtime-internals.md"
  - "wiki/bugs/openclaw-coder-silent-3-layer.md"
  - "wiki/decisions/openclaw-coder-default-model-codex.md"
  - "wiki/analyses/oauth-refresh-token-rotation-multi-client.md"
  - "wiki/projects/hermes.md"
---

# OpenClaw — AI 에이전트 자동화 도구

Telegram 등 채널과 연동하는 AI 에이전트 자동화 도구. npm으로 전역 설치. 현재 사용 중인 AI 프로바이더: `openai-codex/gpt-5.4`.

## 설치 및 업데이트

```bash
# 업데이트
npm install -g openclaw@latest

# 버전 확인
openclaw --version

# 서비스 재시작 (핵심 명령)
openclaw daemon restart
```

> **주의**: `openclaw start`가 아닌 `openclaw daemon restart`가 LaunchAgent를 올바르게 재시작하는 명령이다.

## 설정 파일 구조 (`~/.openclaw/`)

| 파일/디렉터리 | 역할 | git 관리 |
|-------------|------|----------|
| `openclaw.json` | 메인 설정 (auth, 모델, 채널 등) | ✅ 로컬 private repo |
| `agents/*/models.json` | 에이전트 모델 설정 | ✅ |
| `cron/jobs.json` | 크론 작업 설정 | ✅ |
| `devices/paired.json` | 페어링된 기기 | ✅ |
| `identity/device.json` | 기기 정보 | ✅ |
| `workspace/` | 메인 에이전트 페르소나 (SOUL.md 등) | 별도 git (OpenClaw 자체 관리) |
| `workspace-english/` | 영어 튜터 에이전트 | 별도 git (OpenClaw 자체 관리) |
| `memory/*.sqlite` | 벡터 메모리 DB | ❌ (바이너리, 자주 변경) |
| `logs/`, `cron/runs/`, `media/` | 런타임 아티팩트 | ❌ |

### git 관리 설정

`~/.openclaw`를 로컬 private git으로 관리 중:

```bash
# 설정 변경 후 커밋
cd ~/.openclaw && git add -A && git commit -m "설명"
```

## 이미지 생성 기능

```bash
openclaw capability image generate --model <provider/model> --prompt "..." --output ~/out.png
```

### 지원 프로바이더

| 프로바이더 | 설정 필요 | 비고 |
|-----------|----------|------|
| openai | OpenAI API Key | gpt-image-2 기본; Codex OAuth와 별도 키 |
| google | Google API Key | Gemini image |
| fal | fal API Key | Flux 기반 |
| minimax | MiniMax API Key | |
| comfy | ComfyUI 로컬 설정 | |

> **중요**: 텍스트(gpt-5.4)는 OpenAI Codex OAuth로 인증하지만, 이미지 생성(`gpt-image-1/2`)은 **별도의 `OPENAI_API_KEY`** 가 필요하다. 두 인증 체계가 분리되어 있다.

## 다중 에이전트 구성

현재 운용 중인 에이전트 3개:

| 에이전트 ID | 이름 | 모델 | 라우팅 |
|------------|------|------|--------|
| `main` | 맥비 🐝 | openai-codex/gpt-5.4 | Telegram default (DM + 그룹 일반 채팅) |
| `english` | English Tutor 📚 | openai-codex/gpt-5.4-mini | Telegram english (별도 봇) |
| `coder` | 코더 💻 | openai-codex/gpt-5.5 (2026-05-07 변경) | Telegram default, 그룹 "코딩" 토픽 |

> 코더의 default 모델은 2026-05-07 까지 `anthropic/claude-opus-4-6` 였으나 Anthropic OAuth organization-level 거부로 인해 `openai-codex/gpt-5.5` 로 변경. Anthropic Claude 는 사용자가 명시적으로 `/acp spawn --bind here` 를 호출한 ACP 세션 내에서만 사용. 자세히 → [[openclaw-coder-default-model-codex]] / [[openclaw-coder-silent-3-layer]]

### 라우팅 규칙 (`bindings`)

`openclaw.json`의 `bindings` 배열에서 에이전트별 라우팅을 설정한다:

```json
// 에이전트 바인딩 (올바른 형식)
{
  "agentId": "coder",
  "match": {
    "channel": "telegram",
    "accountId": "default",
    "peer": "group:-1003977252069:topic:2"
  }
}
```

> **중요**: `"type": "acp"` 필드는 `bindings` 항목이 아닌 **agent 정의의 `runtime`** 에 두어야 한다. `bindings`에 `"type": "acp"`가 있으면 OpenClaw가 알 수 없는 타입으로 간주해 `Routing rules: 0`이 되어 해당 에이전트로 메시지가 전달되지 않는다.

### 포럼 토픽 vs 별도 봇

같은 그룹 내에서 에이전트를 분리하는 방법 두 가지:

| 방법 | 설정 난이도 | 주의사항 |
|------|-----------|----------|
| **포럼 토픽** | 복잡 (Privacy Mode 해제, 봇 재초대, topic ID 일치) | `coder` 방식 |
| **별도 봇** | 간단 (BotFather에서 봇 생성 후 토큰 등록) | `english` 방식 |

실용적으로는 **별도 봇** 방식이 훨씬 단순하다.

## Telegram 그룹 설정

그룹에서 mention 없이 응답하려면 두 가지를 모두 설정해야 한다:

### 1. OpenClaw 설정: `requireMention: false`

그룹 레벨에 설정 필요 (`channels.telegram.groups.<GROUP_ID>`):

```json
"groups": {
  "-1003977252069": {
    "requireMention": false,
    "topics": {
      "2": { "requireMention": false }
    }
  }
}
```

> **주의**: topic 레벨에만 `requireMention: false`를 설정하면 일반 채팅(non-topic)에는 적용되지 않는다.

### 2. BotFather: Privacy Mode 비활성화

OpenClaw 설정만으로는 부족하다. **Telegram Bot API 레벨에서** Privacy Mode가 켜져 있으면 그룹 메시지가 봇에게 아예 전달되지 않는다. `/start @botname`처럼 `/`로 시작하는 명령만 예외적으로 전달된다.

```
BotFather → /setprivacy → 봇 선택 → Disable
```

> **기존 그룹 적용**: Privacy Mode 변경 전에 봇이 이미 들어가 있는 그룹은 변경이 즉시 적용되지 않는다. 봇을 **그룹에서 추방 후 재초대**해야 한다.

설정 완료 후 확인:
```bash
openclaw channels status --probe
```

경고 없이 `groups:unmentioned, works`가 표시되면 정상.

## 상태 확인

```bash
openclaw status           # 전체 상태 조회
openclaw gateway status   # LaunchAgent + 게이트웨이 상세
openclaw channels status --probe  # 채널 상태 + 그룹 접근 테스트
openclaw agents list --bindings   # 에이전트 라우팅 규칙 확인
```

## 버전 이력

| 날짜 | 버전 | 비고 |
|------|------|------|
| 2026-04-23 | 2026.4.21 | npm install -g openclaw@latest |
| 이전 | 2026.3.28 | — |

## LaunchAgent

Gateway 서비스는 macOS LaunchAgent로 실행된다:
- 로그인 시 자동 시작
- 포트: 18789 (localhost loopback)

## 알려진 버그 / 트러블슈팅

### bindings에 `"type": "acp"` 포함 → Routing rules 0

**증상**: `openclaw agents list --bindings`에서 특정 에이전트의 `Routing rules: 0`. 해당 에이전트로 메시지가 라우팅되지 않음.

**원인**: `bindings` 배열의 항목에 `"type": "acp"` 필드가 들어간 경우. `runtime.type: "acp"`는 에이전트 정의에 넣어야 하며, `bindings`에 두면 OpenClaw가 해당 바인딩을 무시한다.

**수정**: `bindings` 항목에서 `"type": "acp"` 제거. `openclaw config validate`로 검증 후 `openclaw gateway restart`.

---

### 그룹 채팅 응답 없음

체크리스트:
1. `openclaw agents list --bindings` → 해당 에이전트의 `Routing rules: 1` 이상인지 확인
2. `openclaw.json`에 그룹 레벨 `requireMention: false` 설정 여부 확인
3. BotFather에서 Privacy Mode Disable 여부 확인
4. 봇이 그룹에 입장한 시점이 Privacy Mode 변경 이전이면 추방 후 재초대 필요
5. `openclaw channels status --probe`로 audit 경고 확인

## ACP coder 에이전트 — 자율 실행 권한 설정

### permissionMode / nonInteractivePermissions 스키마 제약

acpx 플러그인 스키마에서 확인된 허용값:

| 필드 | 허용값 |
|------|--------|
| `permissionMode` | `"approve-all"`, `"approve-reads"`, `"deny-all"` |
| `nonInteractivePermissions` | `"deny"`, `"fail"` |

**중요**: `permissionMode: "auto"`, `nonInteractivePermissions: "allow"` 옵션은 acpx 플러그인 스키마에 존재하지 않는다. 이 설정으로는 완전한 자율 실행이 불가능하다.

설정 변경 명령어:
```bash
# CLI로 변경 (스키마 내 값만 가능)
openclaw config set plugins.entries.acpx.config.permissionMode approve-reads
openclaw config get plugins.entries.acpx.config.permissionMode

# 현재 실제 운용 값 (기본)
# permissionMode: "approve-all"
# nonInteractivePermissions: "deny"
```

> **주의**: openclaw 게이트웨이 실행 중에 `~/.openclaw/openclaw.json`을 직접 편집하면 프로세스가 파일을 감시하다가 덮어쓸 수 있다. 편집 시 `openclaw gateway stop` → 편집 → `openclaw gateway start` 순서로 진행할 것.

### Claude Code settings.json을 통한 권한 우회

acpx 플러그인 외에, coder 에이전트 디렉터리의 `.claude/settings.json`을 통해 Claude Code 레벨에서 직접 권한을 부여하는 방법도 존재한다 (별도 검토 필요).

## asset-dashboard 프로젝트 git 분리

openclaw workspace-coder(`~/.openclaw/workspace-coder`) 아래에 `asset-dashboard/` 서브디렉터리가 존재했으나, 독립 git repo로 분리되었다.

```
~/.openclaw/workspace-coder/
├── .gitignore          ← asset-dashboard/ 와 .openclaw/workspace-state.json 제외
├── IDENTITY.md
├── SOUL.md
├── USER.md
└── asset-dashboard/    ← 독립 git repo (자체 .gitignore, 초기 커밋 완료)
```

### workspace-state.json untrack

`.openclaw/workspace-state.json`은 런타임 상태 파일로 `git rm --cached`로 트래킹에서 제거하고 `.gitignore`에 추가되었다.

### asset-dashboard 현재 진행 상태 (2026-04-26 기준)

| Phase | 내용 | 상태 |
|-------|------|------|
| 1 | Next.js + TypeScript + Tailwind + shadcn/ui 설정, Prisma 스키마, 포트폴리오 계산 로직 | ✅ 완료 |
| 2 | Account/Holding CRUD 페이지 + Server Actions | ✅ 완료 |
| 3 | Yahoo Finance 시세·환율 연동, PriceCache, RefreshPricesButton | ✅ 완료 |
| 4 | 세금 관리 (금융소득종합과세, 해외주식 양도소득세) | ❌ 미시작 |
| 5 | Gemini AI 재무 분석·추천 | ❌ 미시작 |
| 6 | Recharts 차트, 월별 스냅샷, Vercel 배포 | ❌ 미시작 |

#### Prisma 스키마 핵심

- `Account`, `Holding`, **`PriceCache`** 모델
- `AccountType` / `AssetClass` / `Currency` enum
- Supabase PostgreSQL (pooled + direct URL 분리)

#### Phase 3 구현 상세 (Yahoo Finance 연동)

| 파일 | 역할 |
|------|------|
| `prisma/schema.prisma` | `PriceCache` 모델 추가 (symbol, price, fxRate, cachedAt) |
| `lib/market.ts` | yahoo-finance2 로 시세/환율 조회, DB 캐시 upsert |
| `app/actions/prices.ts` | `refreshPrices()` Server Action + `revalidatePath` |
| `lib/portfolio.ts` | `PriceContext` 타입 추가 — `enrichHolding`/`enrichAccount`/`summarizePortfolio` 라이브 가격 지원 |
| `lib/data.ts` | 포트폴리오·계좌 조회 시 캐시 가격 자동 주입 |
| `components/refresh-prices-button.tsx` | 시세 새로고침 버튼 (로딩 스피너 + 갱신 수 표시) |

**동작 방식**: 대시보드 로드 → DB 캐시에서 가격 읽어 평가액 계산 → 버튼 클릭 → Yahoo Finance API → DB 캐시 갱신 → 전체 페이지 revalidate. `symbol` 없거나 캐시 없는 보유자산은 `manualPrice`/`manualFxRate` 폴백.

**yahoo-finance2 ESM 주의사항**: `require('yahoo-finance2').quote`는 `undefined`. ESM-only 모듈로, `import('yahoo-finance2').then(m => new m.default())` 또는 TypeScript에서 `import YahooFinance from "yahoo-finance2"` + `new YahooFinance()` 인스턴스화 필요. 오버로드 타입 매칭 실패 시 `as any` 캐스팅으로 우회.

#### Prisma Decimal → Client Component 직렬화 버그

**증상**: Next.js 에서 `Only plain objects can be passed to Client Components from Server Components. Decimal objects are not supported.` 에러.

**원인**: `enrichHolding`/`enrichAccount`에서 `...holding` spread 시 Prisma `Decimal` 객체가 그대로 포함된다.

**수정**: spread 전에 모든 Decimal 필드를 `toNumber()` 유틸로 명시적 변환.

```typescript
// ❌ Decimal이 그대로 전달됨
return { ...holding, marketValueBase, ... }

// ✅ Decimal 필드를 number로 변환 후 override
return {
  ...holding,
  quantity: toNumber(holding.quantity),
  averageCost: toNumber(holding.averageCost),
  manualPrice: toNumber(holding.manualPrice),
  manualFxRate: toNumber(holding.manualFxRate),
  marketValueBase,
  ...
}
```

→ 범용 분석 페이지: [[prisma-decimal-nextjs-serialization]]

## 2026-05-07 코더 응답 무 사건 (3계층 디버깅)

코더 (`agent.id = coder`) 가 텔레그램 코더 토픽에서 응답이 끊긴 사건. 3개의 독립 원인이 동시에 발현. 자세한 디버깅 흐름 / sqlite 좀비 task 정리 절차 / 카카오톡 미스라우팅 후속 이슈는 별도 페이지로 분리:

- [[openclaw-coder-silent-3-layer]] — 3계층 진단 사례
- [[openclaw-acp-runtime-internals]] — `plugins.allow` / 좀비 task / `sessions.json` stale binding / wrapper 환경 상속
- [[openclaw-coder-default-model-codex]] — Anthropic → Codex 모델 정책 변경
- [[mcp-config-secret-exposure-via-ps]] — `--mcp-config` 평문 인자 노출 보안 함정

핵심 변경:

| 항목 | 이전 | 이후 |
|------|------|------|
| `agents.list[<코더>].model` | `anthropic/claude-opus-4-6` | `openai-codex/gpt-5.5` |
| `runtime.acp.agent` | `claude` | (유지) |
| `plugins.allow` | (미설정) | `["acpx", "telegram", "copilot-proxy"]` |

운영 정책: 일반 메시지는 embedded 모델 (Codex) 로 즉시 응답. Anthropic Claude 는 `/acp spawn --bind here` 명시 호출 시에만 사용. ACP wrapper 가 user-level Claude.ai connector / MCP 를 모두 상속하므로 보안적으로 신뢰된 사용자만 ACP 호출 권장.

## 2026-06-03 전 토픽 응답 무 사건 (Codex OAuth refresh token 쟁탈)

OpenClaw 가 모든 텔레그램 토픽에서 응답이 끊긴 사건. 5/7 의 3계층(plugins.allow/좀비 task/anthropic 403) 과 **다른 새 원인** — openai-codex provider 인증 실패.

- **증상**: Telegram `in:1m ago, out:6h ago` (메시지는 받지만 응답 못 냄). raw log 에 `FailoverError: No available auth profile for openai-codex (all in cooldown or unavailable)` 48회, main·temp 등 모든 lane.
- **함정**: `openclaw models status` 는 codex 토큰 2개를 `ok, expires in 41h/33h` 로 표시. 하지만 이건 **로컬 만료시각**일 뿐 — 실제로는 서버측에서 토큰 무효화(`token has been invalidated`, 401). fallback 체인이 `gpt-5.5→gpt-5.3-codex→gpt-5.4` 로 **셋 다 openai-codex** 라 provider 하나 죽으면 전멸.
- **진짜 원인**: 같은 Codex OAuth 계정(`min5859@gmail.com`)을 OpenClaw·Hermes(·codex CLI)가 공유하는데, Codex refresh token 은 **회전형(일회용)**. 11일째 돌던 Hermes 가 토큰을 회전시켜 OpenClaw 토큰을 무효화 → 14:17 OpenClaw 재로그인 → 14:29 Hermes 가 다시 회전 → 재발하는 **핑퐁**.
- **해결**: 클라이언트별 **독립 device-flow OAuth 등록** (`openclaw models auth login --provider openai-codex` + Hermes 도 따로). 각자 독립 refresh token 체인 → 쟁탈 소멸.
- **부차**: anthropic OAuth 403 은 `lane=cron-nested`(08:00 뉴스 스크랩 cron, claude-sonnet)에서만이고 텔레그램 응답무와 무관. 5/7 에 "정리 권장" 한 만료 `sk-ant-o...` 토큰이 그대로 남아 매일 cron 실패 중 — 이월 권장.

일반 메커니즘·진단·공유 방식 비교는 [[oauth-refresh-token-rotation-multi-client]]. (Hermes 쪽 동일 사건은 [[hermes]])

## 2026-06-21 AI provider 연결 끊김 (재발 — 결국 결제 미납 만료)

6/3 와 같은 `refresh_token_reused` 증상이 재발. 처음엔 쟁탈로 의심했으나, 사용자 확인 결과 **OpenAI 결제 미납으로 며칠 인증이 끊겼던 것**이 주원인이었고(쟁탈 아님) 새벽의 일시적 호스트 네트워크 장애(`network connection error`, 02:41~08:55)가 겹쳐 헷갈렸다.

- **진단 키**: `openclaw models status` 는 토큰을 `ok` 로 표시했지만, `openclaw agent --agent main --message "PONG"` 실제 호출로 `All models failed: refresh_token_reused (401)` 를 잡아 end-to-end 확인. 표면 네트워크 에러와 근본 auth 에러를 이 호출로 구별.
- **해결**: 결제 재개 후 OpenClaw·Hermes 각각 독립 device-flow OAuth 재등록(`openclaw models auth login --provider openai-codex` → `gateway restart`, hermes 는 `auth add`). 쟁탈이면 경쟁 앱(Codex.app) 선종료 후 재인증해야 ~12분 내 재발 안 함.
- **검증**: `openclaw agent` → `PONG` 정상, `min5859` ok 10d.
- **이월**: 죽은 `default` 중복 프로필 정리 + anthropic `sk-ant` 토큰 재발급(매일 08시 뉴스 cron 이 403 으로 실패 중, 6/3 부터 이월).

쟁탈 vs 단순 만료 구별·경쟁 앱 선종료 순서·end-to-end 검증 등 일반 교훈은 [[oauth-refresh-token-rotation-multi-client]] 2026-06-21 절. (Hermes 동반 재발은 [[hermes]])

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260423-113736-72aa에서 추출)
- 2026-04-23: 다중 에이전트 구성, Telegram 그룹 설정, 버그 트러블슈팅 추가 (세션 로그 20260423-194609-6b61)
- 2026-04-26: ACP permissionMode 스키마 제약, asset-dashboard git 분리 구조 추가 (세션 로그 20260426-120703-304f, 20260426-121630-14c3)
- 2026-04-26: asset-dashboard Phase 2/3 완료 기록, Yahoo Finance 연동 상세, Prisma Decimal 직렬화 버그 수정 패턴 추가 (세션 로그 20260426-141208-ad61, 20260426-184740-49b5)
- 2026-05-07: 코더 응답 무 사건 (3계층 디버깅) 섹션 추가, 모델 정책 변경 (Anthropic → Codex). 자세한 분석은 별도 페이지로 분리 (세션 로그 20260507-011504-7932)
- 2026-06-03: 전 토픽 응답 무 사건 (Codex OAuth refresh token 쟁탈) 섹션 추가. OpenClaw·Hermes 가 동일 Codex OAuth 공유 + 회전형 토큰 → 핑퐁. 진단 함정(status "ok expires" 는 로컬 만료시각, 서버측 무효화는 raw log 401), fallback 이 같은 provider 뿐이면 무의미. 해결은 클라이언트별 독립 device-flow 등록. 일반 분석은 [[oauth-refresh-token-rotation-multi-client]] (세션 로그 20260603-140159-fbbf)
- 2026-06-21: AI provider 연결 끊김 재발 섹션 추가. 6/3 와 같은 증상이나 주원인은 OpenAI 결제 미납 만료(쟁탈 아님) + 일시 네트워크 장애 중첩. `openclaw agent` PONG 호출로 end-to-end 진단·검증, 결제 재개 후 독립 device-flow 재등록으로 복구. 일반 교훈(쟁탈 vs 단순 만료, 경쟁 앱 선종료)은 [[oauth-refresh-token-rotation-multi-client]] (세션 로그 20260621-181739-bf68)
