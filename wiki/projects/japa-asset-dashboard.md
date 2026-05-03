---
title: "japa — 개인 자산 통합 대시보드"
domain: personal
sensitivity: internal
tags: ["nextjs", "prisma", "supabase", "vercel", "yahoo-finance", "personal-asset", "single-user-auth"]
created: 2026-04-30
updated: 2026-05-03
sources:
  - "session-logs/20260430-135011-e8eb-*.md"
  - "session-logs/20260430-161410-0fcc-*.md"
  - "session-logs/20260501-213505-aecb-*.md"
  - "session-logs/20260502-095014-6859-*.md"
  - "session-logs/20260503-100914-b80f-*.md"
confidence: high
related:
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
  - "wiki/bugs/yahoo-finance-concurrent-silent-fail.md"
  - "wiki/bugs/node-modules-symlink-copy-prisma.md"
  - "wiki/bugs/prisma-connection-pool-vercel-supabase.md"
  - "wiki/bugs/gemini-2-0-flash-free-tier-blocked.md"
  - "wiki/patterns/vercel-cron-best-practices.md"
  - "wiki/patterns/zod-schema-per-entity.md"
---

# japa — 개인 자산 통합 대시보드

흩어진 자산을 단일 페이지로 통합 관리하기 위한 1인 전용 Next.js 웹앱. Vercel hobby + Supabase Postgres + Prisma + Yahoo Finance + Gemini AI 조합으로 구성되어 있다. 프로젝트명은 `asset-dashboard` (package.json), 디렉터리명은 `japa`.

## 핵심 내용

### 기술 스택

| 계층 | 선택 |
|---|---|
| 프레임워크 | Next.js 16 (App Router, Turbopack) |
| 언어/UI | TypeScript, Tailwind CSS, shadcn/ui, Recharts |
| 데이터 | Supabase Postgres + Prisma 6 |
| AI | Gemini API (`@google/generative-ai`) |
| 시세 | `yahoo-finance2` (무료 API) |
| 호스팅 | Vercel Hobby (private GitHub repo + Vercel App webhook) |
| 인증 | 환경변수 비밀번호 + HMAC-SHA256 세션 쿠키 (Web Crypto, Edge runtime 호환) |

### 주요 페이지

- `/` — Dashboard (전체 요약)
- `/accounts`, `/accounts/[id]`, `/accounts/[id]/edit`, `/accounts/new` — 계좌별 보유
- `/holdings`, `/holdings/[id]/edit`, `/holdings/new` — 보유 종목 관리
- `/market` — 시장 인덱스
- `/tax` — 종합소득세/해외주식 양도세 추적
- `/ai` — Gemini 기반 종합 자산 분석
- `/login` — 로그인 화면

### 단일 사용자 인증 설계 (2026-04-30 추가)

다중 사용자 시스템이 아닌 **본인 1인 전용**이므로 OAuth/DB 사용자 테이블을 도입하지 않고 다음 최소 구성으로 보안을 갖췄다.

| 컴포넌트 | 구현 |
|---|---|
| 비밀번호 저장 | `ADMIN_PASSWORD` 환경변수 (Vercel Settings + 로컬 `.env`) |
| 세션 토큰 | HMAC-SHA256 서명, Web Crypto API 사용 (Node 의존성 없음 → Edge runtime/middleware 동작) |
| 토큰 서명키 | `AUTH_SECRET` 환경변수 (32자 이상 랜덤, `openssl rand -hex 32`) |
| 쿠키 정책 | HttpOnly + Secure(prod) + SameSite=Lax, 7일 만료 |
| 보호 범위 | `middleware.ts` 가 `/login` 외 모든 경로를 검사 |
| 비밀번호 비교 | timing-safe equal |

비유: `ADMIN_PASSWORD` = 현관문 비밀번호, `AUTH_SECRET` = 출입증 위조방지 도장. 두 환경변수는 로컬과 Vercel 사이에 같지 않아도 무방 (각 환경에서 발급된 쿠키만 그 환경에서 유효). `AUTH_SECRET`을 변경하면 모든 세션이 무효화된다.

> 주의: 현재 코드에는 brute force rate limiting이 없으므로 `ADMIN_PASSWORD`는 **최소 12자 이상**, 영문 대소문자+숫자+특수문자 조합을 강제해야 한다. 6자리 숫자는 100만 가지 → 자동화 공격에 취약.

### Prisma + Supabase 연결 (2026-04-30 확립)

런타임 쿼리와 마이그레이션은 서로 다른 connection 모드를 사용해야 한다. 자세한 트레이드오프와 풀러 비교는 [[nextjs-vercel-supabase-deployment]] 참고.

```env
# 런타임 — Transaction pooler (포트 6543), pgbouncer + connection_limit=1
DATABASE_URL="postgresql://USER:PASS@aws-1-ap-south-1.pooler.supabase.com:6543/postgres?pgbouncer=true&connection_limit=1"

# 마이그레이션 — Session pooler (포트 5432, 쿼리 파라미터 없음). prepared statements 필요해서 Transaction pooler에선 동작 안 함.
DIRECT_URL="postgresql://USER:PASS@aws-1-ap-south-1.pooler.supabase.com:5432/postgres"
```

`prisma/schema.prisma`:

```prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL")
}
```

Free tier에서는 Direct connection (`db.xxx.supabase.co:5432`) 이 IPv6 전용이라 한국 ISP에서 timeout 발생 → pooler 호스트만 사용해야 한다.

### `force-dynamic` 결정

`app/layout.tsx` 루트에 `export const dynamic = 'force-dynamic'`을 적용. 이유: 모든 페이지가 사용자별 동적 데이터(보유 종목·계좌)라 빌드 시점 정적 prerender의 의미가 없는데, 그대로 두면 Vercel 빌드 워커가 동시에 PrismaClient를 띄워 Supabase pool을 초과해 빌드가 실패한다.

### 시세 새로고침: 동시 호출 제한 + 재시도 (2026-04-30 수정)

`lib/market.ts`의 `refreshSymbols`/`refreshAllPrices` 가 30개 심볼을 한 번의 `Promise.allSettled`로 동시 호출하면, Yahoo Finance가 일부 응답에 `regularMarketPrice`를 누락한 채로 돌려주는 케이스가 간헐 재현된다. 자세한 분석은 [[yahoo-finance-concurrent-silent-fail]].

수정 내용:

| 항목 | 이전 | 이후 |
|---|---|---|
| 동시성 | 30개 동시 (`Promise.allSettled`) | worker pool 6개 |
| 재시도 | 없음 (silently fail) | 250ms 후 1회 재시도 |
| 실패 가시성 | 사용자에게 표시 안 됨 | UI ⚠️ 아이콘 + 사유 노출 (`Quote not found`, `no regularMarketPrice` 등) |

추가로 `RefreshPricesButton`을 `app/layout.tsx` 헤더로 이동시켜 모든 페이지에서 한 번의 클릭으로 새로고침이 가능해졌다.

### 보유 종목 수익률 % 표시

`lib/portfolio.ts:enrichHolding`에 `unrealizedGainPercent` 필드 추가. cost basis가 0인 경우 `null`. Dashboard / Accounts 탭 / 계좌 상세에서 동일한 색상 규칙 (상승=적색, 하락=청색) 으로 일관 표시.

### 시세 새로고침 P2024 사고와 cron 통합 (2026-05-01 수정)

배포 후 시세 새로고침 버튼이 Vercel 에서 P2024 (`Timed out fetching a new connection from the connection pool`) 를 일으켜 사이트 전체가 500을 뱉는 사고. 원인은 `connection_limit=1` 환경에서 `refreshMarketHistory` 가 8개 지수 × 365건의 upsert 를 8개 `$transaction` 으로 병렬 실행 → 단일 connection 을 두고 경합 → 풀 망가지면서 같은 인스턴스의 후속 `findMany` 까지 연쇄 실패. 자세한 트러블슈팅은 [[prisma-connection-pool-vercel-supabase]].

수정 방향:

1. **`marketIndexHistory` 는 불변 시계열** → `createMany({ skipDuplicates: true })` + 마지막 한 행만 `upsert` 로 교체. 365 라운드트립 → 2 라운드트립.
2. **클릭 액션 슬림화** — `refreshPrices()` 에서 `refreshMarketHistory` 제거. 클릭은 `priceCache` (보유 종목) + `marketIndex` (현재값) 만 갱신.
3. **일별 Vercel Cron 신설** — `/api/cron/daily` 가 매일 KST 07:00 (UTC 22:00) 에 history 갱신 + 시세 갱신. 패턴은 [[vercel-cron-best-practices]] 로 분리.

cron 라우트는 단일 슬롯 (Hobby 제약) 안에서 KST 날짜 분기로 fan-out:

| 시점 (KST) | 작업 | 멱등성 |
|---|---|---|
| 매일 07:00 | `refreshMarketHistory` + `refreshAllPrices` + `refreshMarketIndices` | createMany skipDuplicates / upsert |
| 매월 1일 07:00 | `PortfolioSnapshot` 1건 추가 | 24h 내 기존 스냅샷 있으면 skip |
| 매년 1월 1일 07:00 | 모든 세테크 계좌 `contributionYTD = 0` | updateMany 자체가 멱등 |

cron 도입에 따른 인접 변경:

- `CRON_SECRET` 환경변수 + 라우트 내부 `Authorization: Bearer` 검증.
- `middleware.ts` 의 matcher 에 `api/cron` 제외 추가. 안 그러면 미들웨어가 cron 호출을 `/login` 으로 redirect 해 라우트가 호출조차 안 됨.
- Hobby 의 cron 슬롯 제약 (≤2, 일 1회) → 단일 `/api/cron/daily` 가 위 3개 작업 모두 처리.

### 세테크 계좌 납입한도 — 평가액과 분리 (2026-05-01 추가)

이전 코드는 `account.totalValueBase` (현금 + 보유자산 평가액) 를 "올해 납입액" 으로 처리해 시세가 오르면 잔여 한도가 음수로 가는 등 부정확. `Account` 에 `contributionYTD Decimal @default(0)` 컬럼을 추가하고, 세금 계산은 `used = max(0, contributionYTD)` 를 사용하도록 분리. 매년 1월 1일 cron 으로 전체 0 리셋.

도메인 패턴 메모: **평가액 (mark-to-market) 과 누적 입금액은 의미가 다르다** — 평가액은 시세에 따라 출렁이지만 납입한도는 실제 현금 입출금 누적치. 세금/한도 같은 회계성 지표는 평가액 대신 별도 컬럼으로 들고 가야 한다.

### 시장 인덱스 — WTI 원유 추가

`INDICES_CONFIG` 에 Yahoo 심볼 `CL=F` (USD) 추가. 기존 8개 지수 (KOSPI, S&P500, NASDAQ 등) 옆에 일관된 카드로 표시.

### CSV 내보내기

`GET /api/export/[type]` 단일 라우트가 `accounts` / `holdings` / `snapshots` 를 UTF-8 BOM 포함 CSV 로 스트림. **BOM 없으면 Excel 에서 한글이 깨진다**. 파일명은 `japa-{type}-YYYY-MM-DD.csv`. 인증 미들웨어가 `/api/export/*` 도 보호하므로 익명 다운로드는 차단됨.

### Prisma migrate deploy 빌드 통합

```json
"build": "prisma generate && prisma migrate deploy && next build"
```

push 한 방으로 마이그레이션 → 코드 배포가 동시에. 빌드 단계에서 `prisma migrate deploy` 가 실패하면 빌드 자체가 멈춰서 컬럼 없이 새 코드가 배포되는 일은 없음. 단 Vercel Project Env 에 `DIRECT_URL` 이 빌드 시점에 노출돼야 동작 (Production / Preview / Development 모두 체크). 기존 배포는 그대로 돌고, 빌드 성공 시에만 트래픽이 새 배포로 전환되므로 다운타임 없음. 일반 패턴은 [[nextjs-vercel-supabase-deployment]] §6.

### 사이드바 네비게이션 + 모바일 드로어 (2026-05-02 추가)

`toy/japa` 의 사이드바 UI 가 보기 좋다는 사용자 피드백을 받아, 기존 상단 pill nav 를 좌측 sticky sidebar (데스크톱) + 햄버거 슬라이드오버 (모바일) 로 교체. shadcn `Sheet` 컴포넌트가 없어 자체 슬라이드오버 구현 (`components/layout/app-shell.tsx`).

| 결정 | 선택 | 이유 |
|---|---|---|
| `RefreshPricesButton` / `LogoutButton` 위치 | 본문 상단 toolbar | 페이지별 작업 버튼과의 일관성 |
| 모바일 대응 | 햄버거 토글 | `w-56` 고정 사이드바는 좁은 화면에서 본문이 너무 좁아짐 |
| `/login` chrome | 기존 isAuthPage 분기로 사이드바 비표시 | 중복 chrome 방지 |
| 자동 닫힘 | 라우트 변경 시 모바일 드로어 자동 닫힘, ESC 키 닫힘, body scroll lock | UX 표준 |

`AppShell` 은 client component 로 사이드바 상태를 소유하고, 서버 컴포넌트인 `app/layout.tsx` 가 그 안에 children 을 넘겨 wrap. middleware 는 `x-pathname` 헤더로 현재 경로를 서버 컴포넌트에 노출 (`/login` 분기용).

### 종목 자동 판별 (KOSPI/KOSDAQ 6자리 코드, 2026-05-02 추가)

`toy/japa` 의 `detectSymbol` 패턴을 가져와 `HoldingForm` 의 ticker 입력란 옆에 「자동」 버튼을 추가. 6자리 숫자 코드를 입력하면 `.KS` (KOSPI) → 실패 시 `.KQ` (KOSDAQ) 순서로 Yahoo Finance 에 probe, 성공 시 name / symbol / currency 자동 채움. 영문/숫자 혼합 ticker 는 그대로 query.

구현:
- `lib/market.ts` — `lookupSymbol()` 직렬 .KS / .KQ probe
- `app/actions/symbols.ts` — server action wrapper (`{ ok, name, symbol, currency }` 또는 `{ ok: false, error }` 봉투)
- `components/forms/holding-form.tsx` — 입력 필드를 controlled 로 전환 (서버 액션 결과로 setState), 사용자가 이미 채운 name 은 덮어쓰지 않음

도메인 패턴 메모: **사용자 입력 보호 우선** — 자동 채움은 빈 필드만, 사용자가 입력한 값은 보존. 예측이 잘못됐을 때 사용자 작업이 날아가지 않게 함.

### 수동 시세 갱신 60초 쿨다운 (2026-05-02 추가)

`refreshPrices()` server action 진입부에 `priceCache.fetchedAt` 검사 추가. 60초 이내면 즉시 `{ skippedCooldown: true, cooldownRemainingSeconds }` 반환, UI 는 「X초 후 재시도」 표시. cron 경로 (`refreshAllPrices` 직접 호출) 는 영향 없음.

```ts
const newest = await prisma.priceCache.findFirst({ orderBy: { fetchedAt: "desc" } });
if (newest && Date.now() - newest.fetchedAt.getTime() < 60_000) {
  return { skippedCooldown: true, cooldownRemainingSeconds, ... };
}
```

도메인 패턴 메모: **쿨다운은 click-path 에만 적용**, 자동화 (cron / 백그라운드 작업) 에는 적용하지 말 것. 그렇지 않으면 cron 의 정상적 빈도까지 막힘. 60초는 연타·실수 클릭 차단 + 실제 사용 지장 없음의 균형점.

### `toy/japa` 와의 비교 — 차용 / 미차용 결정 (2026-05-02)

`/Users/wooki/project/toy/japa` (별도의 Supabase 직접 사용 버전) 와 비교한 결과, **차용 가치가 있는 4가지** 와 **차용하지 않을 1가지** 를 결정. (rate limit 방어 측면에서는 본 프로젝트가 더 견고함이 확인됨 — toy 의 직렬+100ms 보다 본 프로젝트의 worker pool 6 + 250ms 1회 재시도가 정교함)

| 차용 | 우선도 | 상태 |
|---|---|---|
| ⭐⭐⭐ 배당 내역 기록 (Dividend 모델) | 1 | 백로그 |
| ⭐⭐ 계좌 그룹 (N:M, account_groups + members) | 2 | 백로그 |
| ⭐⭐ 종목 자동 판별 (`detectSymbol`) | 3 | ✅ 적용 (2026-05-02) |
| ⭐ 사이드바 UI | 4 | ✅ 적용 (2026-05-02) |
| ⭐ 수동 시세 갱신 쿨다운 (60초) | 5 | ✅ 적용 (2026-05-02). 단 toy 의 5분 쿨다운은 설계 문서에만 있고 코드엔 없었음 — 본 프로젝트가 신규 도입 |

미차용:

- **Stocks 마스터 테이블 분리** — 정규화는 깔끔하지만 현재 Holding 구조로 충분히 동작. 마이그레이션 비용 > 이득. "상장폐지 종목 추적" 이 필요해지면 `Holding.status` 컬럼 추가 정도로 충분
- **ISA 200/400만원 비과세 한도** — `annualContributionLimit` / `contributionYTD` 로 납입한도는 이미 추적 중. 비과세 한도는 별도 개념이지만 우선순위 낮음
- **toy 의 직렬+sleep API 호출 패턴** — 동시성 6 + 재시도가 더 정교. 베끼지 않음

### 배당 내역 기록 — Dividend 모델 (백로그, 2026-05-02 정리)

현재 한계: `Holding.dividendYield(%)` 로 「예상 배당」 만 계산 → 실제 받은 배당과 괴리, 세금 신고 자료 활용 불가. Tax 페이지의 "예상 배당소득" 이 추정치 기반.

`toy/japa` 모델:

```
Dividend {
  accountId, holdingId, dividend_date, ex_dividend_date,
  amount_per_share, quantity, total_amount,
  tax_amount (자동계산 + 수동 override 가능),
  net_amount, currency, is_tax_overridden, memo
}
```

도입 시 얻는 가치:
- 실제 배당 ↔ 예상 배당 비교
- 연도별 / 계좌별 / 종목별 배당 합산 (Tax 페이지에 실값 반영)
- 원천징수 자동 계산 + 사용자 오버라이드 (정산금액 등 예외 처리)
- 금융소득종합과세 추적이 「예상치」 가 아닌 「실값」 기반으로 전환

도메인 패턴 메모: **「예측치」 와 「실측치」 는 같은 컬럼에 섞지 말 것** — 평가액과 누적 입금액이 분리되어야 했던 것과 같은 패턴. 회계성 / 신고용 지표는 실측치 기반이어야 신뢰성이 생긴다.

### 계좌 그룹 (N:M, 백로그, 2026-05-02 정리)

현재 한계: `Account.type` enum + `isTaxAdvantaged` 플래그만 → 「해외주식 전용 계좌들만 묶어서 보기」 같은 사용자 정의 관점이 불가능.

`toy/japa` 모델: `account_groups` + `account_group_members` 매핑 테이블로 한 계좌가 여러 그룹 소속 가능. 사용자가 자유롭게 「절세계좌」, 「해외주식 계좌」, 「장기 보유」 등으로 분류·필터링.

### 야후 quote 와 한국 종목 시간외 거래

`yahoo-finance2` 의 `quote()` 는 `regularMarketPrice` (정규장 종가) 만 안정적으로 채워 주고, `postMarketPrice` 필드가 있긴 하지만 **한국 종목은 거의 빈값**. 시간외 단일가 / 애프터마켓은 사실상 반영되지 않음. 또한 한국 휴장일 (예: 근로자의 날) 에는 야후 자체가 "어제 종가" 를 최신값으로 내려주므로, 사용자에게 "시세가 갱신 안 됐다" 로 보일 수 있다 — 실제로는 거래일이 없어서 그게 최신.

### `git/wk/japa-s` 와의 비교 — 차용 / 미차용 결정 (2026-05-03)

`/Users/wooki/project/git/wk/japa-s` (Supabase SSR + Zod 스키마 + 멀티 LLM 중심의 별도 재구축본) 와 비교한 결과 상위 3개 항목을 우선순위로 정리. (앞선 `toy/japa` 비교는 또 다른 프로젝트로, japa-s 는 보다 새로운 별도 재구현이다)

| 우선도 | 기능 | 도입 가치 | 상태 |
|---|---|---|---|
| 🔴 1 | Supabase Auth + 이메일 Allowlist (`lib/supabase/middleware.ts`, `lib/auth/allowlist.ts`) | 1인용 매직 링크 인증 → 자체 SESSION_COOKIE 보다 견고 | 백로그 (별도 세션 권장 — 인증 락아웃 위험) |
| 🔴 2 | AI 키 AES-256-GCM 암호화 (`lib/ai/crypto.ts`, `AiCredential` 모델 + `/settings/ai` UI) | 멀티 LLM 확장 시 사용자 키 안전 저장, 환경변수 의존도 감소 | 백로그 (멀티 LLM 도입 의사결정 선행) |
| 🔴 3 | **Zod 스키마 entity별 분리** (`lib/<entity>/schema.ts`) | 서버·클라이언트 검증 일관성, enum SSOT 강제 | ✅ 적용 (2026-05-03) |
| 🟡 중 | 보유종목 이동평균 재계산 (`lib/holdings/recompute.ts`) FIFO 양도차익 | Decimal 단위 테스트 필요 | 백로그 (Phase 3~4) |
| 🟡 중 | Yahoo Finance rate limit + fallback 심볼 변환 (`.KS/.KQ/.T`) | 본 프로젝트는 worker pool 6 + 250ms 재시도로 이미 견고 | 미도입 (현 구조 유지) |
| 🟡 중 | 설계 문서 구조 (`docs/01-plan/`, `docs/02-design/`, `.bkit/`) | tasks/ 와 보완적 | 백로그 |

미차용:
- **PostgreSQL 트리거 기반 SQL 마이그레이션** — Prisma 유지 시 우선순위 낮음

발견한 추가 정비 사항:
- 현재 `middleware.ts` 가 `api/cron` 을 matcher 에서 제외 → 외부 노출 상태. Supabase Auth 전환과 별개로 `Authorization: Bearer $CRON_SECRET` 헤더 검증 추가 권장 (라우트 핸들러 내부에는 이미 존재. matcher 제외와 분리 정리 필요)

### Zod 스키마 entity별 분리 적용 (2026-05-03)

`japa-s` 의 패턴을 따라 `lib/<entity>/schema.ts` 에 enum 배열, label map, Zod 폼 스키마, 추론 타입을 한 파일로 모음. 일반 패턴은 [[zod-schema-per-entity]] 로 분리.

| 변경 | 파일 |
|---|---|
| 신규 | `lib/accounts/schema.ts`, `lib/holdings/schema.ts`, `lib/dividends/schema.ts`, `lib/groups/schema.ts` (각 entity 의 `<ENTITY>_TYPES`, `<ENTITY>_TYPE_LABELS`, `<entity>FormSchema`, `<Entity>FormInput` 통합) |
| 리팩터 | 4 server action (`app/actions/{accounts,holdings,dividends,groups}.ts`) — 인라인 `z.object({...})` 와 `z.enum(["KRW", ...])` 중복 제거, 새 schema import |
| 출처 통일 | `components/forms/{account,holding,dividend}-form.tsx`, `app/{page,accounts/page,accounts/[id]/page,holdings/page,tax/page}.tsx`, `components/charts/allocation-pie.tsx`, `lib/ai.ts` |
| 정리 | `lib/labels.ts` — entity별 항목 제거, `CURRENCIES` 와 `QUOTE_TYPE_LABELS` 만 잔류 (3개 entity 가 공유) |

**효과**: `Currency` enum 3중 중복 (`accounts/holdings/dividends.ts` 모두 같은 string array) → Prisma `Currency` 단일 SSOT (`z.nativeEnum(Currency)`). `AccountType`/`AssetClass` 도 동일 정리. 순감 80줄 (109 삭제 / 29 추가).

**`Record<AssetClass, string>` 노출 시 호출부 깨짐**: 더 엄격해진 라벨 맵에서 `?? holding.assetClass` 같은 fallback 패턴이 `string` 인덱싱을 전제로 해 컴파일 실패. 옵션 배열의 `Option<AssetClass>[]` 가 이미 SSOT 강제 책임을 지고 있으므로 라벨 맵은 `Record<string, string>` 으로 노출해 호출부 호환을 유지하는 절충이 적절.

**SDK**: zod 4.3.6 기준 `z.nativeEnum(...)` 사용. zod 4 도 동작하며 추후 `z.enum(Object.values(...))` 류로 마이그레이션 가능.

### Gemini 2.0-flash → 2.5-flash + GEMINI_MODEL env override (2026-05-03 hotfix)

배포된 `/ai` 페이지에서 「AI 분석 시작」 클릭 시 429 quota error + 메시지에 `limit: 0` 동반 → `gemini-2.0-flash` 가 2026 시점 free tier 에서 사실상 차단된 상태 (Google 이 2.5 출시 후 2.0 free tier 를 제한). `lib/ai.ts:94` 한 줄 교체 + env override 추가:

```ts
const model = genAI.getGenerativeModel({
  model: process.env.GEMINI_MODEL ?? "gemini-2.5-flash",
});
```

일반 패턴은 [[gemini-2-0-flash-free-tier-blocked]] 로 분리.

### AI 분석 결과 DB 저장 + 멀티 LLM provider 도입 (2026-05-03)

기존 `/ai` 흐름은 `analyzePortfolio()` 결과를 `useState` 에만 보관 → 새로고침 시 사라지고, 매 분석마다 free tier quota 소비. 두 가지 작업을 한 번에 진행:

1. **`AiAnalysis` 모델 추가** — `prisma migrate dev --name add_ai_analysis` (5개 한국어 텍스트 필드 + `createdAt @@index` + `netWorthAtTime Decimal?`). 1건 ~2-5KB → 1000건 쌓여도 ~5MB (Supabase 무료 500MB 의 1%).
2. **멀티 LLM provider 어댑터 도입** — 단일 `lib/ai.ts` 를 `lib/ai/{types,context,index}.ts` + `lib/ai/providers/{gemini,openai,anthropic}.ts` 로 분해. `prisma migrate dev --name add_ai_provider` 로 `provider` 컬럼 추가. 일반 패턴은 [[multi-llm-provider-adapter-pattern]] 로 분리.

| 결정 | 선택 |
|---|---|
| Provider 추상화 | `lib/ai/providers/<vendor>.ts` 어댑터 |
| 환경변수 | `GEMINI_API_KEY`, `OPENAI_API_KEY`, `ANTHROPIC_API_KEY` (있는 것만 UI 노출) |
| 모델 선택 | 각 provider 별 default + `<VENDOR>_MODEL` env override |
| 키 저장 | **환경변수만** (DB 저장 + 암호화는 #1 백로그) |
| DB 컬럼 | `provider` 추가 (마이그레이션 별건) |
| SDK | 세 provider 모두 공식 SDK 직접 사용 — 멀티 LLM 추상화 라이브러리 도입 안 함 |
| UI | provider 드롭다운, server component (`page.tsx`) + client component (`ai-page-client.tsx`) 분리 |

**삭제 정책 (백로그)**: `prisma.aiAnalysis.deleteMany()` 으로 전체 비우기 가능, daily cron 에 `where: { createdAt: { lt: 90일 전 } }` 추가하면 자동 retention. 모델 자체 drop 도 `Account/Holding` 핵심 데이터와 분리되어 있어 영향 없음.

### Zod-schema 정리 후 폼 즉시 검증 (백로그)

server/client 가 동일 schema 를 import 할 수 있게 되었으므로 `react-hook-form + @hookform/resolvers/zod` 도입 시 폼 제출 → 서버 왕복 → 에러 사이클이 **클라이언트 즉시 검증** 으로 단축 가능. 이번 세션에서는 보류 (별건).

## 변경 이력

- 2026-04-30: 최초 생성. 디스크 분석·Vercel 배포·Supabase pooler 모드·force-dynamic·단일 사용자 인증·Yahoo Finance 동시 호출 silent fail 수정·수익률 % 표시 작업 기록 (출처: session-logs/20260430-135011-e8eb-*, 161410-0fcc-*)
- 2026-05-02: P2024 connection pool 사고와 cron 통합 작업 기록 — `marketIndexHistory.createMany skipDuplicates`, click-path 슬림화, `/api/cron/daily` 신설 (CRON_SECRET + middleware exempt + KST 날짜 분기), `Account.contributionYTD` 컬럼 신설 + 1월 1일 cron 리셋, WTI 원유 인덱스, CSV 내보내기, prisma migrate deploy 빌드 통합 (출처: session-logs/20260501-213505-aecb-*). 일반 패턴은 [[prisma-connection-pool-vercel-supabase]], [[vercel-cron-best-practices]] 로 분리.
- 2026-05-02 (2nd batch): `toy/japa` 와의 비교 분석 + 4개 차용 결정 (사이드바 / 종목 자동 판별 / 60초 쿨다운 / 배당 모델 / 계좌 그룹). 사이드바 + 종목 자동 판별 + 쿨다운 3건 적용 완료, 배당 모델 + 계좌 그룹은 백로그. rate limit 방어 측면에서 본 프로젝트가 toy 보다 견고함 확인 (직렬+100ms vs worker pool 6 + 250ms 1회 재시도). 도메인 패턴 메모: 「자동 채움은 빈 필드만 / 사용자 입력 보호 우선」, 「쿨다운은 click-path 만 / cron 은 영향 없음」, 「예측치와 실측치는 같은 컬럼에 섞지 말 것」 (출처: session-logs/20260502-095014-6859-*).
- 2026-05-03: `git/wk/japa-s` 와의 비교 + 상위 3개 항목 결정 (Supabase Auth / AI 키 암호화 / Zod 스키마 분리). #3 Zod 스키마 entity별 분리 (`lib/<entity>/schema.ts` 4개 신규 + 15개 파일 리팩터, 순감 80줄) 적용 완료. Gemini 2.0-flash free tier blocked → 2.5-flash 마이그레이션 + `GEMINI_MODEL` env override 추가. `AiAnalysis` Prisma 모델 추가로 분석 결과 DB 영속화. 단일 Gemini 코드를 `lib/ai/providers/{gemini,openai,anthropic}.ts` 어댑터 패턴으로 분해 + `provider` 컬럼 추가 (공식 SDK 직접 사용, 멀티 LLM 추상화 라이브러리 미도입). 일반 패턴은 [[zod-schema-per-entity]], [[multi-llm-provider-adapter-pattern]], [[gemini-2-0-flash-free-tier-blocked]] 로 분리 (출처: session-logs/20260503-100914-b80f-*).
