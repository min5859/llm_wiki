---
title: "Vercel Cron 베스트 프랙티스 — Hobby 슬롯 제약·CRON_SECRET·middleware·멱등성"
domain: both
sensitivity: public
tags: ["vercel", "cron", "serverless", "idempotency", "middleware", "nextjs", "hobby-plan"]
created: 2026-05-02
updated: 2026-05-05
sources:
  - "session-logs/20260501-213505-aecb-*.md"
  - "session-logs/20260505-084952-fe4f-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/bugs/prisma-connection-pool-vercel-supabase.md"
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
  - "wiki/analyses/pgbouncer-direct-url-hybrid-routing.md"
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
---

# Vercel Cron 베스트 프랙티스

Vercel Cron 을 처음 도입할 때 매번 다시 막히는 5가지 결정 지점 (Hobby 슬롯 제약 / CRON_SECRET 인증 / Next.js middleware 충돌 / 단일 라우트 fan-out / 멱등성 설계) 정리.

## 1. Hobby 플랜의 cron 슬롯 제약 → "단일 라우트 + 날짜 분기" 패턴

Vercel Hobby 는 **cron job 등록 개수가 매우 제한적** (최근 기준 2개까지, 모두 일 1회 최대 빈도). "월 1회 스냅샷", "1월 1일 리셋", "매일 시세 갱신" 같이 빈도가 다른 작업을 슬롯 별로 등록하려고 하면 슬롯이 부족해서 막힌다.

해결 패턴: **하나의 일별 cron 라우트가 날짜를 보고 분기**.

```ts
// app/api/cron/daily/route.ts
export async function GET(req: Request) {
  // 1. CRON_SECRET 검증 (아래 §2)
  const auth = req.headers.get("authorization");
  if (auth !== `Bearer ${process.env.CRON_SECRET}`) {
    return new Response("unauthorized", { status: 401 });
  }

  const ran: string[] = [];

  // 항상 실행 — 매일 시장 인덱스 history 갱신
  await refreshMarketHistory();
  ran.push("marketHistory");

  // KST 1일이면 — 월별 스냅샷
  const kst = new Date(Date.now() + 9 * 3600 * 1000);
  if (kst.getUTCDate() === 1) {
    await createSnapshot();
    ran.push("monthlySnapshot");
  }

  // KST 1월 1일이면 — 모든 세테크 계좌 contributionYTD = 0
  if (kst.getUTCMonth() === 0 && kst.getUTCDate() === 1) {
    const { count } = await prisma.account.updateMany({
      where: { type: { in: TAX_ADVANTAGED_TYPES } },
      data: { contributionYTD: 0 },
    });
    ran.push(`contributionYTDReset(${count})`);
  }

  return Response.json({ ok: true, ran });
}
```

```json
// vercel.json
{ "crons": [{ "path": "/api/cron/daily", "schedule": "0 22 * * *" }] }
```

응답에 `ran: [...]` 을 돌려주면 Logs 탭에서 "오늘 어떤 작업이 돌았는지" 한눈에 보인다.

### KST 계산 노트

cron 자체는 UTC 기준이라 `0 22 * * *` 가 KST 07:00 에 해당. 라우트 내부에서 "오늘 KST 가 1일인가?" 판단할 때는 `new Date(Date.now() + 9*3600*1000)` 후 UTC field 를 읽어야 한다 (`getUTCDate`, `getUTCMonth`). 로컬 timezone API (`getDate`) 는 Vercel runtime 에서 UTC 라 의도와 다른 결과.

## 2. CRON_SECRET — 공개 cron URL 을 외부 호출로부터 보호

cron 라우트는 **인터넷에 공개된 URL** 이다. 누구나 `https://example.com/api/cron/daily` 를 GET 으로 두드릴 수 있고, 인증 없으면 다음 사고:

1. 외부에서 임의 트리거 → Yahoo Finance API rate limit 소진 / DB 쓰기 폭주
2. 의도적 DoS — connection pool 고갈 같은 사고를 반복적으로 유발 가능 ([[prisma-connection-pool-vercel-supabase]] 참고)
3. 비용/리소스 낭비

해결: `CRON_SECRET` 환경변수 + `Authorization: Bearer <값>` 검증.

- **Vercel Cron** 은 등록된 cron 호출 시 이 값을 자동으로 헤더에 붙인다 — 코드 변경 없음.
- **라우트** 가 헤더 값과 환경변수가 일치하는지만 검사하면 된다.

값은 의미 있는 단어 금지, 강한 난수만:

```bash
openssl rand -hex 32
# 또는
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

Production 에만 등록하면 되고 (Preview/Dev 에서는 cron 안 돔), 회전 (rotate) 은 환경변수만 갈아끼우면 끝 — 코드 변경 불필요. 미설정 시 라우트가 401 만 반환하므로 앱 자체 동작에는 영향 없지만 **cron 작업이 조용히 안 돌게** 된다 → 모니터링 필요.

## 3. Next.js middleware 가 cron 라우트를 잡아채지 않게

전체 경로를 보호하는 auth middleware (`middleware.ts`) 가 깔려 있으면 **CRON_SECRET 검증이 실행되기 전에** middleware 가 먼저 redirect 처리해버린다. 결과:

```
Vercel Cron → /api/cron/daily 
  → middleware: 쿠키 없음 → 307 Redirect /login
  → 라우트의 CRON_SECRET 체크는 호출조차 안 됨
```

증상: Crons 탭의 "Run" 수동 트리거에서 응답이 **307 redirect 또는 HTML** 이면 이 케이스다.

해결: middleware matcher 에서 `/api/cron/*` 를 명시적으로 제외.

```ts
// middleware.ts
export const config = {
  matcher: [
    // 다음을 제외한 모든 경로 보호:
    // - /login
    // - /api/cron/*  ← cron 라우트는 자체 CRON_SECRET 으로 가드
    // - 정적 자산
    "/((?!login|api/cron|_next/static|_next/image|favicon.ico).*)",
  ],
};
```

cron 라우트의 인증은 미들웨어가 아니라 **라우트 내부 CRON_SECRET 체크에 위임**하는 것이 정답.

## 4. 멱등성 (idempotency) — cron 작업 설계의 핵심

같은 작업을 몇 번 다시 실행해도 결과가 똑같은 성질. 수학에선 `f(f(x)) = f(x)`. 실무에선 **"재시도해도 부작용이 누적되지 않는가"** 의 기준.

cron 은 다음 이유로 **반드시 멱등**해야 한다:

- Vercel cron 이 네트워크 일시 장애로 같은 시각에 두 번 호출될 수 있음
- 사용자가 디버깅하려고 Crons 탭의 "Run" 으로 수동 재실행할 수 있음
- 배포 직후 cron 이 재실행될 수 있음

### 멱등인 예 vs 아닌 예

| 작업 | 1번 실행 | 또 실행 | 멱등? |
|---|---|---|---|
| `prisma.marketIndexHistory.createMany({ skipDuplicates: true })` | 365행 insert | 0행 insert (이미 있음) | ✅ |
| `prisma.account.updateMany({ data: { contributionYTD: 0 } })` | 0으로 세팅 | 또 0으로 세팅 | ✅ |
| `prisma.priceCache.upsert(...)` | 가격 갱신 | 같은 가격으로 갱신 | ✅ |
| `prisma.account.update({ data: { cashBalance: { increment: 1000000 } } })` | 100만원 입금 | **또 100만원 입금** | ❌ |
| `prisma.snapshot.create({ data: ... })` (조건 없이) | 행 추가 | **또 추가** | ❌ |
| 외부 이메일 발송 | 1통 | **2통** | ❌ |

### 비멱등 작업을 cron 에 넣어야 할 때 — guard 패턴

스냅샷 저장처럼 본질적으로 누적되는 작업은 **"최근 N 시간 안에 같은 작업이 있었는가" guard 를 cron 안에 직접 넣어** 멱등으로 만든다:

```ts
// 24h 내 기존 스냅샷이 있으면 skip → 같은 날 두 번 호출돼도 안전
const recent = await prisma.portfolioSnapshot.count({
  where: { takenAt: { gte: new Date(Date.now() - 24 * 3600 * 1000) } },
});
if (recent > 0) return; // no-op
await createSnapshot();
```

요약: **여러 번 돌려도 한 번 돌린 것과 결과가 같다 = 멱등**. 멱등하지 않은 cron 은 사고나 디버깅 한 번에 데이터 무결성이 깨진다.

## 5. 운영 — Vercel UI 에서 cron 상태 확인

| 위치 | 무엇 |
|---|---|
| Project → 좌측 사이드바 **Crons** (또는 Settings → Crons) | 등록된 cron 목록, 마지막 실행, 다음 예정, 성공/실패, 우측 **Run** 수동 트리거 |
| Project → **Logs** → Filter `path: /api/cron/daily` | 호출 내역 + 라우트가 반환한 `{ ok, ran }` JSON 그대로 확인 |
| Crons 탭 → **Notifications** | cron 실패 시 이메일 (Hobby 도 가능). 도입 직후 며칠은 켜 두는 걸 권장 — `CRON_SECRET` 누락 같은 운영 실수가 즉시 드러남 |

### Crons 메뉴가 안 보일 때

`vercel.json` 에 cron 정의가 한 번도 deploy 되지 않은 상태에서는 **사이드바에 Crons 항목 자체가 안 노출된다**. push 후 빌드가 끝나야 메뉴가 나타남. 메뉴가 안 보이면 다음 순서로 확인:

1. Deployments → 최신 배포가 `Ready` 인가?
2. Functions 탭에 `/api/cron/daily` 가 함수 목록에 떴는가? (없으면 빌드가 라우트를 못 잡음)
3. Source 탭에 `vercel.json` 이 포함됐는가? (없으면 push 가 다른 브랜치 / Vercel 이 다른 root 디렉터리를 봄)

### 수동 검증

```bash
curl -H "Authorization: Bearer <CRON_SECRET>" https://yourapp.vercel.app/api/cron/daily
# {"ok":true,"ran":["marketHistory"]}
```

이 응답을 손으로 한 번 받아 봐 두면, 이후 Crons 탭의 "Run" 이 같은 결과를 내는지로 정상 동작을 빠르게 진단할 수 있다.

## 6. 빈도 결정 — 무료 DB 부담은 보통 무시 가능

Supabase 무료 한도 (DB 500MB, egress 2GB) 대비 일반적인 cron 누적량은 매우 작다:

| 주기 | 1년 행 수 | 10년 누적 | 한도 대비 |
|---|---|---|---|
| 월 1회 | 12 | ~50KB | 0.01% |
| 주 1회 | 52 | ~210KB | 0.04% |
| 일 1회 | 365 | ~1.5MB | 0.3% |

DB 부담보다 **차트 가독성** 이 진짜 제약. 365점을 한 화면에 깔면 점이 겹쳐 추세가 잘 안 보인다. 일별로 자주 찍더라도 차트 측에서 "최근 90일은 일별, 그 이전은 월별 다운샘플" 같은 처리를 하는 편이 보기 좋음.

## 7. Lambda 60초 timeout vs P2024 — 두 단계 구분

cron 라우트 안에서 무거운 batch 를 돌릴 때 마주칠 수 있는 두 가지 실패 모드는 **아예 다른 원인**이라 해결책도 다르다. 헷갈리지 말 것:

| 실패 | 응답 | 원인 | 해결 방향 |
|---|---|---|---|
| **P2024** | 즉시 500 (10초 안) | 한 lambda 안의 동시 prisma 호출이 `connection_limit=1` 풀을 두고 경쟁 | 호출 병렬도 낮추기 / mutex 직렬화 / `createMany skipDuplicates` |
| **FUNCTION_INVOCATION_TIMEOUT** | 60초 후 504 | wall-clock 이 lambda budget 초과 (외부 I/O 누적, connection acquire 누적, instrumentation hang 등) | 외부 I/O 병렬화 / 페이로드 축소 / DIRECT_URL 우회 / instrumentation fire-and-forget |

**같이 일어날 때 순서**: 먼저 P2024 를 푸는 게 정공법. P2024 가 살아있으면 timeout 진단 자체가 어렵다 (connection 못 받아서 죽는 건지 일이 길어서 죽는 건지 분간 안 됨). P2024 → timeout 으로 증상이 바뀌면 progress 로 받아들이고 다음 layer 를 본다.

**timeout 진단의 함정**:
- `console.log` 가 stdout buffer 에 갇혀 timeout SIGKILL 시 lost. `console.error` (stderr) 는 즉시 flush 되므로 marker 출력은 stderr 권장.
- module top-level marker (`console.error("[cron] module loaded")`) + handler 첫 줄 marker 를 따로 출력해 "module 자체가 import 됐는지" vs "handler 가 진입했는지" 분리. 후자가 안 찍히면 instrumentation / 다른 module top-level await 가 hang 의심.
- `instrumentation.ts` 의 `register()` 안에 `await prisma.xxx.count()` 같은 호출이 있으면 PgBouncer 환경에서 cold connection acquire 가 길어질 때 lambda startup 에서 hang 함. fire-and-forget + try/catch 로 분리해 startup 을 절대 죽이지 말 것.

**PgBouncer transaction mode 의 cumulative cost**: `connection_limit=1` 환경에서는 query 당 connection acquire 비용이 누적된다. 직렬화로 P2024 는 막혀도 9개 직렬 upsert 가 23초씩 걸리는 기현상이 가능. cron lambda 만 `prismaDirect` (DIRECT_URL) 로 PgBouncer 우회하는 게 정공법. 자세한 건 [[pgbouncer-direct-url-hybrid-routing]].

**병렬 vs 직렬 결정 원칙**:
- 외부 I/O (HTTP fetch, yahoo quote 등) → **병렬** (`Promise.all`). wall-clock 을 가장 느린 1개로 압축.
- 자원 한정된 자원 (DB connection 1개, rate limit) → **직렬** (mutex / `for...of`). 안전하게 큐잉.

같은 작업의 충돌 지점만 직렬화하면 된다. 전체를 직렬화하면 외부 I/O 누적 비용으로 lambda budget 을 못 맞춘다 (실측: 59회 yahoo round-trip 직렬 ≈ 60초 초과).

## 8. Hobby 플랜의 cron 운영 한계

| 항목 | Hobby | 비고 |
|---|---|---|
| cron 슬롯 수 | ≤ 2 | 단일 라우트 + 날짜 분기로 우회 (§1) |
| cron 빈도 | 일 1회 최대 | 시간 단위 cron 불가 |
| **flexible window** | **1 시간** | `0 22 * * *` 등록해도 22:00~23:00 사이 어느 시점에 실행. 정확한 분 단위 보장 안 됨 |
| **Last Run / Status 컬럼** | **표시 안 됨** | Crons 페이지에 등록 정보만 보임. 실제 실행 검증은 Logs 또는 수동 curl 호출로 |
| Notifications | 사용 가능 | 실패 시 이메일 |
| `maxDuration` | 60s | route file 의 `export const maxDuration = 60` |

flexible window 때문에 사용자가 "오전 7시에 안 돌고 8시 5분에 돈다" 로 보일 수 있는데 정상 동작 범위. 분 단위 정확도가 필요하면 Pro 또는 외부 cron (cron-job.org / GitHub Actions) 트리거.

### Vercel logs CLI 의 시간 윈도우 한계

cron 실행 여부를 사후 검증할 때 `vercel logs` CLI 의존은 위험. 흔히 마주치는 한계:

- `--since 2h`, `-n 5000` 줘도 **server-side cap** 으로 가까운 ~50분 윈도우만 반환. 과거 1~2 시간 전 cron 실행 시점에 못 닿음.
- `--until <ISO>` 단독 → 400.
- 특정 ISO `--since` → traffic 많은 시간대면 400 또는 결과 비어 옴.
- `-q <text>` 텍스트 필터, `--source serverless` 가 잘 안 먹음.
- `--json` redirect 시 stdout 비기도 함 — `script` 로 PTY 모방해야 잡힘.

정공법: **Vercel 대시보드 Logs 탭에서 시간 범위 좁혀 검색** 또는 **endpoint 를 수동 curl 호출 → 응답 + 직후 로그 함께 캡처**. 후자는 호출 시각이 명확하므로 logs CLI 가 그 짧은 윈도우 안의 데이터를 줄 가능성이 높다.

## 관련 맥락

- 이 패턴이 풀어 주는 본진 사고는 [[prisma-connection-pool-vercel-supabase]] (무거운 트랜잭션 click-path 폭주). cron 으로 옮기는 게 정공법.
- Cron 자체의 catchup 동작 비교는 [[macos-launchagent-catchup-behavior]] (launchd) 와 비교해 볼 만하다 — Vercel Cron 은 캐치업 없음, 누락은 다음 슬롯까지 그대로 누락.
- 환경변수 / 빌드 / Pooler 같은 Vercel + Supabase 인프라 결정은 [[nextjs-vercel-supabase-deployment]].

## 변경 이력

- 2026-05-02: 최초 생성. japa 자산 대시보드의 cron 도입 과정에서 마주한 5가지 결정 (Hobby 슬롯 제약 / CRON_SECRET / middleware 충돌 / 단일 라우트 fan-out / 멱등성) 정리 (출처: session-logs/20260501-213505-aecb-*)
- 2026-05-05: §7 Lambda 60초 timeout vs P2024 의 두 단계 구분, console.error stderr marker / module-level marker / instrumentation fire-and-forget 진단 패턴, PgBouncer cumulative cost 와 DIRECT_URL 우회 전략, 병렬/직렬 결정 원칙 추가. §8 Hobby cron 운영 한계 (flexible 1-hour window / Last Run 컬럼 미노출 / `vercel logs` CLI 시간 윈도우 cap) 추가 (출처: session-logs/20260505-084952-fe4f-*)
