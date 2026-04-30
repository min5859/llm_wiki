---
title: "japa — 개인 자산 통합 대시보드"
domain: personal
sensitivity: internal
tags: ["nextjs", "prisma", "supabase", "vercel", "yahoo-finance", "personal-asset", "single-user-auth"]
created: 2026-04-30
updated: 2026-04-30
sources:
  - "session-logs/20260430-135011-e8eb-*.md"
  - "session-logs/20260430-161410-0fcc-*.md"
confidence: high
related:
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
  - "wiki/bugs/yahoo-finance-concurrent-silent-fail.md"
  - "wiki/bugs/node-modules-symlink-copy-prisma.md"
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

## 변경 이력

- 2026-04-30: 최초 생성. 디스크 분석·Vercel 배포·Supabase pooler 모드·force-dynamic·단일 사용자 인증·Yahoo Finance 동시 호출 silent fail 수정·수익률 % 표시 작업 기록 (출처: session-logs/20260430-135011-e8eb-*, 161410-0fcc-*)
