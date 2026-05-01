---
title: "japa — 개인 자산 통합 대시보드"
domain: personal
sensitivity: internal
tags: ["nextjs", "prisma", "supabase", "vercel", "yahoo-finance", "personal-asset", "single-user-auth"]
created: 2026-04-30
updated: 2026-05-02
sources:
  - "session-logs/20260430-135011-e8eb-*.md"
  - "session-logs/20260430-161410-0fcc-*.md"
  - "session-logs/20260501-213505-aecb-*.md"
confidence: high
related:
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
  - "wiki/bugs/yahoo-finance-concurrent-silent-fail.md"
  - "wiki/bugs/node-modules-symlink-copy-prisma.md"
  - "wiki/bugs/prisma-connection-pool-vercel-supabase.md"
  - "wiki/patterns/vercel-cron-best-practices.md"
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

### 야후 quote 와 한국 종목 시간외 거래

`yahoo-finance2` 의 `quote()` 는 `regularMarketPrice` (정규장 종가) 만 안정적으로 채워 주고, `postMarketPrice` 필드가 있긴 하지만 **한국 종목은 거의 빈값**. 시간외 단일가 / 애프터마켓은 사실상 반영되지 않음. 또한 한국 휴장일 (예: 근로자의 날) 에는 야후 자체가 "어제 종가" 를 최신값으로 내려주므로, 사용자에게 "시세가 갱신 안 됐다" 로 보일 수 있다 — 실제로는 거래일이 없어서 그게 최신.

## 변경 이력

- 2026-04-30: 최초 생성. 디스크 분석·Vercel 배포·Supabase pooler 모드·force-dynamic·단일 사용자 인증·Yahoo Finance 동시 호출 silent fail 수정·수익률 % 표시 작업 기록 (출처: session-logs/20260430-135011-e8eb-*, 161410-0fcc-*)
- 2026-05-02: P2024 connection pool 사고와 cron 통합 작업 기록 — `marketIndexHistory.createMany skipDuplicates`, click-path 슬림화, `/api/cron/daily` 신설 (CRON_SECRET + middleware exempt + KST 날짜 분기), `Account.contributionYTD` 컬럼 신설 + 1월 1일 cron 리셋, WTI 원유 인덱스, CSV 내보내기, prisma migrate deploy 빌드 통합 (출처: session-logs/20260501-213505-aecb-*). 일반 패턴은 [[prisma-connection-pool-vercel-supabase]], [[vercel-cron-best-practices]] 로 분리.
