---
title: "Supabase 프로젝트 리전 마이그레이션 — 9 단계 절차"
domain: both
sensitivity: public
tags: ["pattern", "supabase", "migration", "postgres", "prisma", "vercel", "database"]
created: 2026-05-12
updated: 2026-05-12
source_session: "20260512-000725-28e8-DB-에-있는-내용을-모두-CSV-로-export-한-뒤-계좌-내용을-초기화-하고-다시-추.md"
sources:
  - "session-logs/20260512-000725-28e8-DB-에-있는-내용을-모두-CSV-로-export-한-뒤-계좌-내용을-초기화-하고-다시-추.md"
confidence: high
related:
  - "wiki/patterns/csv-roundtrip-backup-restore.md"
  - "wiki/analyses/pgbouncer-direct-url-hybrid-routing.md"
  - "wiki/analyses/supabase-magic-link-single-user-allowlist.md"
  - "wiki/projects/japa-asset-dashboard.md"
---

# Supabase 프로젝트 리전 마이그레이션 — 9 단계 절차

기존 Supabase 프로젝트 (예: 뭄바이) 의 데이터를 다른 리전 (예: 서울) 의 새 프로젝트로 옮기는 표준 절차. Supabase 자체에는 "리전 변경" 기능이 없어 **새 프로젝트 신규 생성 → 데이터 이전** 이 유일 경로. 1인 / 단일 사용자 앱 (Magic Link 인증) 전제로 정리하지만, 핵심 단계는 멀티 사용자 앱에도 응용 가능.

## 결정 흐름

```
[기존 DB] ──CSV export──> [로컬 백업] ──reset+import──> [새 DB]
   │                                                       │
   └─ .env 의 DATABASE_URL 만                                │
      뭄바이 ─────────────────────────────────────────────> 서울
              ↑                  ↑                      ↑
        백업 보존              Auth 재설정          Vercel env 교체
        (롤백 라인)             (auth.users 분리)    (운영 전환)
```

핵심 원칙:
- **롤백 라인 유지** — 기존 DB 는 절대 삭제하지 않음 (1~2주 안전망)
- **`.env` 만 교체로 즉시 복귀 가능** — 코드 변경 없이 `cp .env.backup .env` 한 줄
- **운영 (Vercel) 은 마지막에 전환** — 로컬에서 모든 검증 끝낸 후 마지막에 prod env 교체

## STEP 1 — 현재 데이터 CSV 백업

[[csv-roundtrip-backup-restore]] 패턴으로 5종 (accounts/groups/holdings/transactions/dividends) CSV export. 외래키 ID + N:M 직렬화가 들어 있어야 round-trip 보장.

```
~/Downloads/japa-backup-2026-05-11/
├── japa-accounts-2026-05-11.csv
├── japa-groups-2026-05-11.csv
├── japa-holdings-2026-05-11.csv
├── japa-transactions-2026-05-11.csv
└── japa-dividends-2026-05-11.csv
```

방어선이지 필수는 아님 — 기존 DB 가 살아 있어 언제든 다시 export 가능. 다만 **"CSV import 가 새 DB 에서 정상 동작하는지 처음 검증" 하는 작업이므로 백업이 있으면 시행착오 비용 낮음**.

## STEP 2 — 새 프로젝트 연결 정보 수집

새 리전의 Supabase 대시보드에서 4개 값을 메모:

| 키 | 위치 | 형태 |
|----|------|------|
| `DATABASE_URL` (pooled) | Project Settings → Database → Connection pooling → Transaction mode (port 6543) | `postgresql://USER:PASS@aws-X-ap-northeast-2.pooler.supabase.com:6543/postgres?pgbouncer=true&connection_limit=1` |
| `DIRECT_URL` (direct) | Project Settings → Database → Connection string → Direct (port 5432) | `postgresql://USER:PASS@aws-X-ap-northeast-2.pooler.supabase.com:5432/postgres` |
| `NEXT_PUBLIC_SUPABASE_URL` | Project Settings → API → Project URL | `https://<project-ref>.supabase.co` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Project Settings → API → anon public key | `eyJ...` JWT |

주의:
- `aws-N` prefix 는 풀러 인스턴스마다 다름 (aws-0, aws-1, ...). Supabase 가 발급한 값 그대로 사용
- Supabase 가 준 템플릿에 **`&connection_limit=1` 가 빠져 있으면 추가**. Vercel 서버리스에서 PgBouncer 풀 고갈 방지에 중요
- `DIRECT_URL` 에는 `&connection_limit=1` **안 붙임** (직접 연결이라 불필요)

## STEP 3 — `.env` 교체 (백업 먼저)

```bash
cp .env .env.mumbai.backup   # 롤백 라인
```

`.env` 의 다음 4 줄 교체:

```env
DATABASE_URL="postgresql://...@aws-1-ap-northeast-2.pooler.supabase.com:6543/postgres?pgbouncer=true&connection_limit=1"
DIRECT_URL="postgresql://...@aws-1-ap-northeast-2.pooler.supabase.com:5432/postgres"
NEXT_PUBLIC_SUPABASE_URL="https://<new-ref>.supabase.co"
NEXT_PUBLIC_SUPABASE_ANON_KEY="<new-anon-key>"
```

`OWNER_EMAIL`, `NEXT_PUBLIC_APP_URL`, `GEMINI_API_KEY` 등은 그대로.

## 비밀번호 함정 — Supabase 의 4 가지 자격증명

`P1000: Authentication failed` 가 가장 흔한 발 헛디딤 포인트. 다음 4 가지가 모두 별개:

| 위치 | 용도 | 우리 `.env` |
|------|------|-------------|
| Settings → Database → **Database password** | Postgres 직접 접속 비밀번호 | `DATABASE_URL` / `DIRECT_URL` 의 `:PASS@` |
| Settings → API → `anon` `public` key | 브라우저 노출용 Auth 키 | `NEXT_PUBLIC_SUPABASE_ANON_KEY` |
| Settings → API → `service_role` secret key | 서버 전용 관리자 키 | (대부분 미사용) |
| Settings → API → JWT Secret | JWT 서명 키 | (대부분 미사용) |

**Database password 는 프로젝트 생성 시 한 번만 표시**되고 이후 다시 볼 수 없음 → 기억 안 나면 reset 필요.

### Reset 시 권장

1. Settings → Database → Database password → "Reset database password"
2. **"Generate a password" 사용하지 말고 직접 입력** — 특수문자 들어가면 URL percent-encoding 필요 (URL 안에서 `@` `#` `$` `%` `&` `?` `/` `:` `+` 모두 인코딩). 영숫자만 16~24자로 직접 입력하면 인코딩 회피
3. 즉시 비밀번호 매니저 (1Password, 키체인) 에 저장
4. `.env` 의 두 줄 (DATABASE_URL, DIRECT_URL) 의 `:PASS@` 자리에 동일 값 붙여넣기
5. 둘 다 같은 비밀번호여야 함 — md5 한 줄로 검증 가능:
   ```bash
   db_pw_hash=$(grep DATABASE_URL .env | sed 's/.*postgres\.[a-z]*://;s/@aws.*//' | md5)
   dir_pw_hash=$(grep DIRECT_URL .env | sed 's/.*postgres\.[a-z]*://;s/@aws.*//' | md5)
   [ "$db_pw_hash" = "$dir_pw_hash" ] && echo OK || echo MISMATCH
   ```

### URL 인코딩 표 (특수문자 사용 시)

| 원본 | 인코딩 |
|------|--------|
| `@` | `%40` |
| `#` | `%23` |
| `$` | `%24` |
| `%` | `%25` |
| `&` | `%26` |
| `?` | `%3F` |
| `/` | `%2F` |
| `:` | `%3A` |
| `+` | `%2B` |
| ` ` (공백) | `%20` |

## STEP 4 — 새 DB 에 Prisma 스키마 적용

```bash
npx prisma migrate deploy 2>&1 | tail -50
```

기대 결과 — 모든 마이그레이션 적용 + 빈 스키마 생성. 에러 케이스:

### P1000: Authentication failed

→ STEP 3 의 비밀번호 표 다시 확인. 자가 점검:
1. `.env` 에디터 저장됨 (Cmd+S)
2. 두 줄 모두 갱신 (DATABASE_URL, DIRECT_URL)
3. 앞뒤 공백·줄바꿈 없음
4. 특수문자 없음 (또는 percent-encoded)
5. 두 비밀번호 hash 일치

### P3005: database schema is not empty

새 프로젝트인데 이전 사용 흔적이 남아 있는 경우 (이전에 다른 앱이 연결됐거나, 기본 schema 생성 흔적). **destructive reset 으로 한 번에 해결**:

```bash
npx prisma migrate reset --force --skip-seed
```

- 새 DB 라 손실될 사용자 데이터 없음
- `public` schema 의 모든 테이블 drop → 마이그레이션 처음부터 재적용
- `--force` 는 interactive 확인 skip (CI / 자동화 안전)
- `--skip-seed` 는 seed 스크립트 안 돌림 (수동 import 가 별도)

Claude Code 같은 분류기가 이 명령을 차단하면 **사용자가 직접 터미널에서 실행** 후 STEP 5 진행.

### 프로젝트 paused 상태 의심

Supabase 무료 플랜은 7일 이상 미사용 시 자동 pause. "받아 놓았던" 프로젝트라면 가능성 큼. 대시보드 상단 "Project paused" / "Restore project" 배지 확인 → 클릭 후 1~2분 활성화 대기.

또한 **SQL Editor 접속 테스트**: 대시보드 → SQL Editor → `SELECT 1;` 실행. 에러면 프로젝트 자체 문제, 성공이면 비밀번호 문제.

## STEP 5 — Auth URL 설정

이 단계가 빠지면 매직 링크 callback 거부되어 로그인 불가:

1. Authentication → URL Configuration
2. **Site URL**: `http://localhost:3000` (로컬 검증 단계, 운영 도메인은 STEP 9 에서)
3. **Redirect URLs** (whitelist):
   - `http://localhost:3000/auth/callback`
   - `https://<your-domain>/auth/callback` (운영 도메인도 미리 등록 가능)
4. Save
5. (옵션) Authentication → Providers → Email 활성 확인 (기본 ON)

Auth users 마이그레이션은 **불필요** — 새 프로젝트의 `auth.users` 는 비어있고, 다음 매직 링크 요청 시 자동 생성됨. `OWNER_EMAIL` 같은 allowlist 기반 단일 사용자라면 매직 링크 한 번 다시 받으면 끝.

## STEP 6 — dev 서버 재시작 + 로그인 + CSV import

`.env` 가 바뀌었으니 dev 서버 재시작 (Next.js 가 새 값 읽도록):

```bash
# 기존 dev 서버 종료 후
npm run dev
```

로그인:
1. `http://localhost:3000` → `/login` 으로 redirect
2. `OWNER_EMAIL` 입력 → 매직 링크 발송
3. 메일 클릭 → 인증 → 대시보드. **빈 상태가 정상** (새 DB)

CSV import:
1. `/settings/data` → "3. CSV 가져오기"
2. 5 파일 모두 선택 (accounts / groups / holdings / transactions / dividends)
3. 실행 → 메시지에 import 행 수 표시
4. 원본 CSV 행 수와 일치하는지 확인 (헤더 제외)

## STEP 7 — 검증

각 페이지에서 데이터 일치 + 응답 속도 체감:

- [ ] Dashboard — 순자산·총자산
- [ ] Accounts — 계좌 수, 이름, 현금 잔액
- [ ] Groups — N:M 매핑 (그룹별 소속 계좌 수)
- [ ] Holdings — 종목 수, 평단가, 수량
- [ ] Dividends — 배당 내역
- [ ] 계좌 1개 상세 — 보유 종목 + 최근 거래

응답 속도는 리전 변경의 직접 효과 — 뭄바이 → 서울이면 page latency 명확히 빨라짐.

## STEP 8 — 코드 수정 commit

migration 진행 중에 발견된 코드 수정 (e.g. "use server" 파일 export 정리) 을 별도 커밋으로 정리. `.env*.backup` 은 절대 add 하지 말 것 (`.gitignore` 의 `.env*` 패턴이 `.backup` 까지 포함하는지 확인).

## STEP 9 — Vercel 운영 환경 전환

마지막 단계. 로컬에서 모든 검증이 끝났을 때 실행.

### 9.1 Vercel 환경 변수 교체

Vercel → Project → Settings → Environment Variables → 4개 키 (`DATABASE_URL`, `DIRECT_URL`, `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`) 값 교체. **Production / Preview / Development 모두 체크**. Save 만 하고 **아직 Redeploy 클릭 X**.

### 9.2 Supabase Auth URL 에 운영 도메인 추가

새 프로젝트의 Authentication → URL Configuration:
- Site URL: `https://<your-domain>` (매직 링크 메일의 기본 callback)
- Redirect URLs: 로컬용 + 운영용 둘 다 등록
- Save

### 9.3 push (자동 배포 트리거)

```bash
git push origin main
```

Vercel 이 자동 빌드 시작, 새 env 사용. **수동 Redeploy 불필요** (push 가 트리거). 환경 변수만 바꾸고 push 안 하면 자동 배포 안 됨 — 수동 Redeploy 필요.

### 9.4 운영 검증

배포 완료 후:
- 운영 URL 접속 → 매직 링크 재로그인 (새 Supabase 프로젝트라 세션 갱신 필요)
- Dashboard / Accounts / Holdings 일치 확인
- 응답 속도 체감

### 9.5 안전망 유지

- 뭄바이 프로젝트: 1~2주 대기 후 문제 없으면 Supabase 대시보드에서 pause 또는 delete. **즉시 삭제는 비추**
- `.env.mumbai.backup`: 로컬에 그대로 두거나 안전 보관 (`.gitignore` 처리 확인)

## "운영 환경에서 데이터를 쓰지 마세요" 제약

마이그레이션 진행 중 (STEP 3 ~ STEP 9 사이) 에는 운영 (뭄바이) 에서 **데이터 입력·수정 금지**. 안 그러면 STEP 9 에서 서울로 전환할 때 그 데이터가 사라짐 (뭄바이에 남고 서울에는 없음). 1인 전용 앱이라 본인이 안 쓰면 됨.

멀티 사용자 앱이라면:
- 점검 페이지 모드로 전환 (`/maintenance` redirect)
- 또는 read-only 모드로 전환 후 마이그레이션
- 또는 마지막에 한 번 더 incremental sync (뭄바이 → 서울)

## 일반 원칙

1. **새 프로젝트로 데이터 이전 = Supabase 리전 변경의 유일 방법** — `gcloud sql instances clone` 같은 in-place 마이그레이션 없음
2. **롤백 라인 유지가 마이그레이션 안전의 90%** — `.env.backup` + 기존 DB 보존
3. **로컬 검증 → 운영 전환 분리** — 한 번에 운영까지 갈아치우면 import 실패 시 운영 마비
4. **Auth 마이그레이션은 보통 불필요** — 단일 사용자 매직 링크는 새 프로젝트에서 다시 로그인하면 끝. 멀티 사용자 OAuth/Email/Password 면 `auth.users` export/import 별도 검토
5. **STEP 별 사용자 확인 점** — 자동화하지 말고 단계마다 결과 확인 후 다음 진행 (특히 STEP 4 P1000 / P3005, STEP 6 import 결과)

## 안티 패턴

- **`prisma db push` 로 스키마 만들기** — migration history 가 안 만들어져 이후 운영 마이그레이션에서 P3005 발생. `prisma migrate deploy` 사용
- **Auth users 와 application data 를 같은 단계에서** — Supabase 의 `auth.*` 는 자기 스키마 (RLS 등) 가 복잡. application 데이터 (`public.*`) 와 같은 transaction 에 넣지 말 것
- **운영 환경 env 만 먼저 교체** — 로컬에서 한 번도 import 검증 안 한 상태로 운영 전환 → import 실패 시 운영 마비
- **`.env.backup` 을 git add** — 비밀번호 누출. `.gitignore` 에 `.env*` 가 `.env*.backup` 까지 커버하는지 확인 (대부분의 패턴은 커버하지만, `.env.local` 만 ignore 한 경우 등 주의)

## 관련 페이지

- [[csv-roundtrip-backup-restore]] — STEP 1·6 의 CSV export/import 패턴
- [[pgbouncer-direct-url-hybrid-routing]] — STEP 2 의 DATABASE_URL / DIRECT_URL 의 트레이드오프
- [[supabase-magic-link-single-user-allowlist]] — STEP 5·9.2 의 Auth 설계
- [[japa-asset-dashboard]] — 적용 사례 (뭄바이 → 서울)
- [[nextjs-vercel-supabase-deployment]] — Vercel 환경 변수 / migrate deploy 빌드 통합

## 변경 이력

- 2026-05-12: 최초 작성 (session-logs/20260512-000725-28e8-*.md). japa 의 뭄바이 → 서울 마이그레이션 작업을 일반화
