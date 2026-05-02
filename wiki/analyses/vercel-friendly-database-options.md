---
title: "Vercel 배포 친화적 데이터베이스 옵션 비교"
domain: both
sensitivity: public
tags: ["analysis", "vercel", "database", "neon", "supabase", "turso", "vercel-kv", "serverless", "postgres", "sqlite"]
created: 2026-05-03
updated: 2026-05-03
source_session: 20260502-235145-8714-*.md
confidence: medium
related:
  - "wiki/analyses/web-app-storage-without-db.md"
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
  - "wiki/bugs/prisma-connection-pool-vercel-supabase.md"
---

## 개요

Vercel 의 serverless 환경에 1인 프로젝트 / 사이드 프로젝트의 DB 를 도입할 때 가장 자주 마주치는 4가지 옵션 (Neon / Vercel KV / Supabase / Turso) 의 비교. 각 옵션의 데이터 모델·요금 구조·serverless 적합도·인접 함정을 한 페이지에 정리.

본 페이지는 "처음부터 DB 가 필요한 시점" 의 선택 가이드. DB 자체가 필요한지의 판단은 [[web-app-storage-without-db]] 참고.

## 비교 내용

### 4가지 옵션 한눈 비교

| 옵션 | 데이터 모델 | 적합도 | 핵심 특징 | 인접 함정 |
|---|---|---|---|---|
| **Neon (Vercel Postgres)** | Postgres (관계형) | ⭐ 1순위 (관계 데이터) | 풀 SQL, Vercel 마켓플레이스에서 무설정 연결, 무료 티어 충분, **branching** (브랜치별 DB 복사) 지원 | serverless connection pool 관리 (PgBouncer) |
| **Vercel KV (Upstash Redis)** | key-value (Redis) | 단순 캐시·카운터에만 | REST API, edge runtime 호환, 마이크로초 단위 latency | 관계형 쿼리 불가, 옷장처럼 필터·검색이 필요한 데이터엔 부적합 |
| **Supabase** | Postgres + Auth + Storage + Realtime | 인증·스토리지·실시간까지 묶을 때 | 풀 백엔드 번들, OAuth/매직링크 무설정, RLS (Row Level Security) | Vercel 외부 서비스, [[prisma-connection-pool-vercel-supabase]] 의 함정 (Transaction pooler 6543 + connection_limit=1 필수) |
| **Turso (libSQL/SQLite)** | SQLite (관계형) | 가벼운 1인용·edge 분산 | edge 에서 동작, 글로벌 복제, 무료 티어 관대, 비용 매우 저렴 | SQLite 의 데이터 타입 약점 (정수·실수·텍스트만), 동시 쓰기 약함 |

### 데이터 모델별 권장

| 데이터 형태 | 권장 옵션 |
|---|---|
| 관계형 (사용자 ↔ 게시물 ↔ 댓글 등) | **Neon** 또는 **Supabase** |
| 키-값 / 캐시 / 카운터 / rate limit | **Vercel KV** |
| 가벼운 1인용 + 글로벌 latency 우선 | **Turso** |
| 인증·파일 업로드·실시간이 필요 | **Supabase** (한 번에 묶음) |
| 시계열 / 무거운 분석 | (이 4개는 모두 부적합 — TimescaleDB 또는 ClickHouse Cloud) |

### Neon vs Supabase — 1:1 비교

| 항목 | Neon | Supabase |
|---|---|---|
| 데이터베이스 | Postgres | Postgres |
| Vercel 마켓플레이스 통합 | ✅ Vercel Postgres 로 일급 | ⚪ 외부 connector |
| 인증 | ❌ 별도 솔루션 (NextAuth/Clerk 등) 필요 | ✅ 내장 (Email/OAuth/매직링크/SAML) |
| 스토리지 (이미지 등) | ❌ 별도 (Vercel Blob 등) | ✅ 내장 (S3 호환) |
| 실시간 | ❌ | ✅ Postgres LISTEN/NOTIFY 기반 |
| Branching (브랜치별 DB) | ✅ git 브랜치당 별도 DB 분기 | ❌ |
| RLS (Row Level Security) | ⚪ Postgres 표준이지만 클라이언트 직결은 없음 | ✅ 클라이언트가 anon key 로 직결, RLS 가 보안 경계 |
| 무료 티어 | 0.5GB / 무료 컴퓨트 시간 | 0.5GB DB + 1GB 스토리지 + 50K MAU |
| Vercel + Prisma 함정 | 동일 (Transaction pooler 모드 필요) | [[prisma-connection-pool-vercel-supabase]] 참고 |

### 연결 방식 (코드 관점)

#### Neon

Vercel 대시보드 → Storage → Neon 추가 → 환경변수 자동 주입.

```ts
import { neon } from '@neondatabase/serverless';
const sql = neon(process.env.DATABASE_URL!);
const rows = await sql`SELECT * FROM clothes WHERE warmth >= ${4}`;
```

또는 Drizzle ORM / Prisma 와 함께.

#### Supabase

```ts
import { createClient } from '@supabase/supabase-js';
const supabase = createClient(URL, ANON_KEY);
const { data } = await supabase.from('clothes').select('*').gte('warmth', 4);
```

또는 Postgres 직접 연결 (Prisma) — 단 [[nextjs-vercel-supabase-deployment]] 의 Transaction pooler 6543 + `?pgbouncer=true&connection_limit=1` 필수.

#### Turso

```ts
import { createClient } from '@libsql/client';
const turso = createClient({ url: TURSO_URL, authToken: TURSO_TOKEN });
const { rows } = await turso.execute('SELECT * FROM clothes WHERE warmth >= ?', [4]);
```

#### Vercel KV (Upstash Redis)

```ts
import { kv } from '@vercel/kv';
await kv.set('user:42:lastSeen', Date.now());
const value = await kv.get('user:42:lastSeen');
```

### 서버리스 환경의 공통 함정 — Connection Pool

Vercel serverless 함수는 요청마다 새 인스턴스가 깨어나므로, 동시 클라이언트 수가 로컬 dev 의 1–2개에서 **수십~수백 개** 로 폭증한다.

| 환경 | Postgres 측 핵심 |
|---|---|
| Neon | 자체 Pool 관리, `@neondatabase/serverless` 가 HTTP 기반 → connection 개념 없음 (가장 편함) |
| Supabase | PgBouncer Transaction mode 모드 (포트 6543) 필수, Prisma 는 `?pgbouncer=true&connection_limit=1` |
| Turso | HTTP 기반 → connection 개념 없음 |
| Vercel KV | HTTP 기반 → connection 개념 없음 |

전통적인 Postgres 라이브러리 (`pg`, Prisma) 를 Direct Connection 으로 쓰면 거의 확실히 `MaxClientsExceeded` 가 터진다. **Neon serverless driver (HTTP) 또는 Supabase Transaction pooler** 가 정답.

### 비용 (2026-05 무료 티어, 변동 가능)

| 옵션 | 무료 티어 | 다음 단계 |
|---|---|---|
| Neon | 0.5GB / 191시간 컴퓨트/월 | Launch $19/월 |
| Vercel KV (Upstash) | 30K commands/일 | $0.2/100K commands |
| Supabase | 0.5GB DB + 1GB 스토리지 + 50K MAU | Pro $25/월 (8GB) |
| Turso | 9GB / 1B reads / 25M writes / 월 | Scaler $29/월 |

1인 프로젝트는 **모든 옵션이 무료 티어 안에서 운영 가능** — 비용보다는 적합도로 선택.

### 도입 단계의 안티 패턴

- ❌ MVP 단계에서 "언젠가 필요할테니" 라며 처음부터 도입 → [[web-app-storage-without-db]] 의 LocalStorage 단계를 건너뛰는 것은 도메인 모델이 굳기 전에 스키마 마이그레이션 비용을 치르는 것
- ❌ 관계형 데이터를 Vercel KV 에 욱여넣음 → 옷장처럼 "warmth ≥ 4 AND category = 'jacket'" 같은 복합 쿼리가 못 됨
- ❌ Vercel + Supabase 에 Direct Connection 으로 Prisma → 첫 트래픽 스파이크에 풀 고갈
- ❌ Neon Branching 을 활용하지 않고 production DB 에서 곧장 마이그레이션 시도 → 대신 git 브랜치별 분기로 PR 검증

## 결론

### 권장 의사결정 트리

```
인증·파일·실시간을 한 번에?
  YES → Supabase
  NO  → 데이터 모델은?
        관계형 → Neon (Vercel 일급 통합 + Branching)
        키-값  → Vercel KV
        가벼운 1인용 + edge 분산 → Turso
```

### 1순위 추천 (관계형 데이터, 1인 프로젝트)

**Neon (Vercel Postgres)**. 이유:

- Vercel 마켓플레이스에서 무설정 연결 → 환경변수 자동 주입
- HTTP 기반 serverless driver → connection pool 함정 없음
- Branching 으로 PR 검증 → production DB 를 직접 만지지 않아도 됨
- Postgres 표준 SQL → 미래 마이그레이션 비용 낮음

### 1순위 추천 (인증·스토리지·실시간 묶음)

**Supabase**. 단, [[prisma-connection-pool-vercel-supabase]] 와 [[nextjs-vercel-supabase-deployment]] 의 7가지 함정을 미리 읽어둘 것.

## 관련 페이지

- [[web-app-storage-without-db]] — DB 도입 *전* 단계의 4가지 저장 옵션
- [[nextjs-vercel-supabase-deployment]] — Supabase 선택 시 Vercel 배포 7가지 핵심 결정
- [[prisma-connection-pool-vercel-supabase]] — Vercel + Supabase + Prisma 의 connection pool 사고 사례
- [[wardrobe]] — 본 페이지를 향후 Step 3 진입 시 참고할 옷장 매칭 앱

## 변경 이력

- 2026-05-03: 최초 생성 (출처: session-logs/20260502-235145-8714-*) — wardrobe 프로젝트의 "DB 가 필요한가" 질문에서 도출. confidence: medium (요금 구조는 2026-05 기준이며 빠르게 변동 가능).
