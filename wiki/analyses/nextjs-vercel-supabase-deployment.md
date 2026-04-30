---
title: "Next.js + Vercel + Supabase 배포 — 핵심 결정 7가지"
domain: both
sensitivity: public
tags: ["nextjs", "vercel", "supabase", "prisma", "deployment", "pgbouncer", "force-dynamic", "serverless"]
created: 2026-04-30
updated: 2026-04-30
sources:
  - "session-logs/20260430-135011-e8eb-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/analyses/prisma-decimal-nextjs-serialization.md"
  - "wiki/analyses/prisma-generate-missing-error.md"
  - "wiki/bugs/node-modules-symlink-copy-prisma.md"
---

# Next.js + Vercel + Supabase 배포 — 핵심 결정 7가지

Next.js (App Router) + Prisma + Supabase Postgres 조합을 Vercel Hobby 에 배포할 때 거의 매번 똑같이 막히는 7가지 결정 지점 정리. 각 항목은 "안 알면 빌드 또는 런타임이 깨진다" 수준의 함정.

## 1. Vercel ↔ GitHub 연동은 Actions 가 아닌 Vercel 자체 빌드

```
git push → GitHub webhook → Vercel 빌드 서버에서 git clone → npm install → next build → 배포
```

GitHub Actions 무료 분 (월 2,000분) 을 소비하지 않는다. GitHub App + Webhook 조합이라 다음과 같이 확인할 수 있다:

- GitHub Settings → Integrations → GitHub Apps → "Vercel" 항목
- Settings → Webhooks → Vercel webhook URL
- PR 화면 → "Vercel" 봇 댓글에 프리뷰 URL

**커스텀 테스트/E2E/조건부 배포가 필요한 경우에만** GitHub Actions + Vercel CLI 조합을 직접 작성한다. 일반 케이스에선 기본 연동이 정답.

## 2. 업로드 크기는 사실상 신경 쓸 필요 없음

| 한도 | Hobby | Pro |
|---|---|---|
| 소스 파일 업로드 크기 | **100MB** | 1GB |
| 소스 파일 개수 | 15,000 | 15,000 |
| 빌드 시간 | 45분 | 45분 |
| 단일 파일 (GitHub) | 100MB hard | 100MB hard |

`node_modules`, `.next`, `.git`, `.env` 는 모두 자동 제외. Vercel 서버가 직접 `npm install` + `next build`. Hobby 한도에 걸릴 일은 거의 없다 (`japa` 의 경우 1.5GB 디스크 → 실제 업로드 596KB).

`.vercelignore` 는 보통 불필요. `.gitignore` 가 그대로 적용되며, 다음 정도가 표준:

```
.next
node_modules
.env
.env.local
*.log
```

## 3. Hobby 플랜의 organization 제약

| 저장소 위치 | Hobby |
|---|---|
| `github.com/<개인>/<repo>` (private 포함) | 가능 |
| `github.com/<org>/<repo>` | **Pro 이상 필요** |

private 자체는 Hobby 에서 제약 없음. Vercel App 권한이 public/private 구분 없이 설정됨.

## 4. `force-dynamic` — 빌드 시점 prerender 회피

Next.js 16 (App Router) 의 기본 동작은 server component 가 빌드 시점에 prerender (SSG) 시도. 모든 페이지가 사용자별 동적 데이터인 앱 (개인 대시보드 등) 에서는 의미가 없을 뿐 아니라 다음 사고가 발생한다:

```
Generating static pages using 1 worker (4/10)
prisma:error ... MaxClientsInSessionMode: max clients reached
```

빌드 워커가 동시에 PrismaClient 를 띄워 Supabase pool 한도를 초과 → 빌드 실패.

**해결**: 루트 `app/layout.tsx` 에 단 한 줄.

```ts
export const dynamic = 'force-dynamic';
```

모든 하위 페이지가 빌드 시 prerender 를 건너뛰고 요청 시점에 렌더링된다. 사용자별 동적 데이터를 다루는 SaaS / 개인 대시보드의 사실상 default.

## 5. Supabase Pooler 모드 비교 — Direct vs Transaction vs Session

이 표가 핵심. 거의 모든 "왜 로컬은 되는데 Vercel 에서 터지지" 의 원인.

| 항목 | Direct | **Transaction** | Session |
|---|---|---|---|
| 포트 | 5432 | **6543** | 5432 (pooler 도메인) |
| 호스트 | `db.xxx.supabase.co` | `pooler.supabase.com` | `pooler.supabase.com` |
| pgbouncer 경유 | ❌ | ✅ Transaction mode | ✅ Session mode |
| **IPv4 (Free tier)** | ❌ **IPv6 only** | ✅ | ✅ |
| 동시 연결 처리 | 한정 | **수천** (트랜잭션 단위 반납) | 한정 (pool_size) |
| Connection 점유 | 클라이언트 종료까지 | 매 트랜잭션 후 즉시 반납 | 클라이언트 종료까지 |
| Prepared statements | 지원 | **비지원** (Prisma 는 `?pgbouncer=true` 필수) | 지원 |
| 적합한 환경 | 항상 켜져 있는 서버 | **Serverless (Vercel/Lambda)** | 일반 서버, 마이그레이션 |

### 한국 Free tier 사용자의 함정

Direct connection 이 IPv6 전용이라 한국 ISP (KT, SKB 등) 에서 timeout. 그래서 자연스럽게 Session pooler 로 정착하는 경우가 많은데, **Vercel serverless 환경에서는 Session pooler 가 거의 확실히 터진다**:

| 환경 | 동시 Prisma 클라이언트 |
|---|---|
| 로컬 dev (싱글 프로세스) | 1~2개 |
| Vercel serverless | 수십~수백 개 (요청마다 함수 인스턴스 별도 깨어남) |

Session pooler 는 클라이언트가 연결을 점유하므로 100개 클라이언트 = 100~200개 연결 시도 → `MaxClientsInSessionMode`. Transaction pooler 는 트랜잭션 단위로 반납하므로 같은 pool 로 수천 클라이언트를 처리.

### 정답 조합

```env
# 런타임 — Transaction pooler (포트 6543), pgbouncer + connection_limit
DATABASE_URL="postgresql://USER:PASS@aws-N-REGION.pooler.supabase.com:6543/postgres?pgbouncer=true&connection_limit=1"

# 마이그레이션 — Session pooler (포트 5432, 쿼리 파라미터 없음)
DIRECT_URL="postgresql://USER:PASS@aws-N-REGION.pooler.supabase.com:5432/postgres"
```

`prisma/schema.prisma`:

```prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL")
}
```

핵심 4요소:
1. 호스트 `pooler.supabase.com`
2. 포트 **6543** (런타임)
3. `?pgbouncer=true` — Prisma 에게 prepared statements 끄라고 알림
4. `&connection_limit=1` — **각 함수 인스턴스가 단 1개의 연결만 사용** (serverless 필수). 없으면 기본 `num_cpus * 2 + 1` 개 연결을 만들려 한다.

`prisma migrate` 는 prepared statements 를 쓰므로 자동으로 `directUrl` (Session pooler) 사용 → 정상 동작.

## 6. 환경변수 — `.env` Import 와 재배포 타이밍

Vercel 의 Configure Project 화면에 **".env" 형식 통째 붙여넣기 (Import .env)** 가 있다. 한 줄씩 입력하지 말 것.

| 마크 | Vercel 처리 |
|---|---|
| `# 주석` | 무시 |
| 빈 줄 | 무시 |
| `KEY="값"` | 따옴표 제거 후 저장 |
| `KEY=값` | 그대로 저장 |
| 환경 (Prod/Preview/Dev) 선택 | Import 단계에선 자동으로 3개 모두 적용 |

핵심 운영 규칙:

| 변경 종류 | 재배포 트리거 |
|---|---|
| 코드 변경 | `git push` 만으로 자동 |
| **환경변수 변경** | **수동 Redeploy 필요** (Deployments → ⋯ → Redeploy, Build cache OFF) |
| Prisma schema 변경 | 로컬 `prisma migrate dev` → push → Vercel 은 `prisma generate` 만 함 → **production schema 자동 반영 안 됨** |

production 자동 마이그레이션이 필요하면 build 스크립트를:

```json
"build": "prisma generate && prisma migrate deploy && next build"
```

다만 staging 없이 곧장 prod 에 reflect 되니 신중히. 작은 1인 프로젝트가 아니면 별도 CI 단계로 분리하는 편이 안전하다.

### 비밀번호 재설정이 가장 빠른 진단 종결자

DATABASE_URL 인증 실패 (`Authentication failed against database server`) 의 원인은 보통 다음 셋:

1. `[YOUR-PASSWORD]` placeholder 그대로 둠 (가장 흔함)
2. 비밀번호의 특수문자 (`@ # ? & / : + = [ ] ( ) 공백`) URL 인코딩 누락 → `@` 가 호스트 구분자로 오인됨
3. 단순 오타

**가장 빠른 해결**: Supabase → Settings → Database → **Reset database password → Generate password** (alphanumeric only) → 로컬 `.env` 와 Vercel 양쪽 갱신. 디버깅에 시간 쓰는 것보다 5분 안에 끝난다.

## 7. `.next` 와 `node_modules` 는 cache, 코드/데이터/설정이 아님

`.next/` (704MB 흔함) 는 단순 빌드 캐시. dev 서버를 끄고 지우면 안전, 다음 실행 시 자동 재생성. `.next/dev/cache` 에 누적된 dev 서버 캐시가 비대화의 주범 (450MB+ 흔함).

| 상황 | 영향 |
|---|---|
| dev 서버 종료 후 삭제 | 안전. 다음 실행 시 재생성 |
| dev 서버 실행 중 삭제 | HMR 깨짐, 서버 재시작 필요 |
| `next start` (prod) 중이면 | 다시 `next build` 필요 |

`node_modules` 도 `.gitignore` 에 들어 있어 GitHub 에 안 올라가고 Vercel 서버에서 새로 설치. 단, **폴더 카피 시 심볼릭 링크가 풀려서 `.bin/prisma` 가 wasm 을 못 찾는 ENOENT 에러**가 흔히 발생한다 — [[node-modules-symlink-copy-prisma]] 참고.

## 종합 점검 리스트

배포 직전 다음 8개를 한 번에 점검:

1. ✅ `.env` 가 `.gitignore` 에 들어 있는가? (`git log --all -S "ghp_"` 등으로 히스토리 노출 점검)
2. ✅ DATABASE_URL = Transaction pooler (`:6543`) + `?pgbouncer=true&connection_limit=1`?
3. ✅ DIRECT_URL = Session pooler (`:5432`, 쿼리 없음)?
4. ✅ root `app/layout.tsx` 에 `force-dynamic`?
5. ✅ build 스크립트에 `prisma generate` 포함?
6. ✅ Supabase production DB 에 마이그레이션 적용 (`prisma migrate deploy`) 했는가?
7. ✅ Vercel 환경변수에 4개 (DATABASE_URL / DIRECT_URL / 외부 API 키들) Import .env 로 일괄 등록?
8. ✅ Hobby 플랜이라면 repo 가 개인 계정 소유인가? (organization 이면 Pro 필요)

## 변경 이력

- 2026-04-30: 최초 생성. japa 자산 대시보드의 첫 Vercel 배포 시 발생한 빌드 실패·런타임 500·Yahoo throttling 외의 인프라 문제 7가지 정리 (출처: session-logs/20260430-135011-e8eb-*)
