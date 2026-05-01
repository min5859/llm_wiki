---
title: "Vercel Cron 베스트 프랙티스 — Hobby 슬롯 제약·CRON_SECRET·middleware·멱등성"
domain: both
sensitivity: public
tags: ["vercel", "cron", "serverless", "idempotency", "middleware", "nextjs", "hobby-plan"]
created: 2026-05-02
updated: 2026-05-02
sources:
  - "session-logs/20260501-213505-aecb-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/bugs/prisma-connection-pool-vercel-supabase.md"
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
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

## 관련 맥락

- 이 패턴이 풀어 주는 본진 사고는 [[prisma-connection-pool-vercel-supabase]] (무거운 트랜잭션 click-path 폭주). cron 으로 옮기는 게 정공법.
- Cron 자체의 catchup 동작 비교는 [[macos-launchagent-catchup-behavior]] (launchd) 와 비교해 볼 만하다 — Vercel Cron 은 캐치업 없음, 누락은 다음 슬롯까지 그대로 누락.
- 환경변수 / 빌드 / Pooler 같은 Vercel + Supabase 인프라 결정은 [[nextjs-vercel-supabase-deployment]].

## 변경 이력

- 2026-05-02: 최초 생성. japa 자산 대시보드의 cron 도입 과정에서 마주한 5가지 결정 (Hobby 슬롯 제약 / CRON_SECRET / middleware 충돌 / 단일 라우트 fan-out / 멱등성) 정리 (출처: session-logs/20260501-213505-aecb-*)
