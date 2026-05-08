---
title: "Supabase Auth 매직 링크 + 단일 사용자 이메일 Allowlist 패턴"
domain: both
sensitivity: public
tags: ["analysis", "supabase", "auth", "magic-link", "nextjs", "single-user", "allowlist", "owasp"]
created: 2026-05-08
updated: 2026-05-08
source_session: 20260507-230645-c555-tasks-plan-2026-05-03-japa-s-features.md-에-있는-내용중.md
related:
  - "wiki/projects/japa-asset-dashboard.md"
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
---

## 개요

1인용 Next.js 앱에서 자체 비밀번호 + 세션 쿠키 인증을 Supabase Auth 매직 링크 + `OWNER_EMAIL` allowlist 로 교체하는 패턴. 매직 링크 발송 라우트와 callback 라우트 양쪽에서 allowlist 재검증 (defense-in-depth) + 비-allowlist 이메일에도 generic 200 응답 (email enumeration 방지) 을 통해 OWASP A01 (Broken Access Control) / A07 (Identification and Authentication Failures) 를 차단. Vercel Cron 같은 cookie 없는 호출 경로는 middleware matcher 에서 분리 + `CRON_SECRET` Bearer 로 별도 인증.

## 비교: 자체 HMAC 비밀번호 vs Supabase 매직 링크

| 항목 | 자체 HMAC | Supabase 매직 링크 |
|---|---|---|
| 비밀번호 저장 | env 평문 (`ADMIN_PASSWORD`) | 없음 (이메일 자체가 인증) |
| 토큰 발행 | HMAC-SHA256 자체 구현 | Supabase OTP |
| 비밀번호 분실 | env 직접 변경 | 즉시 새 매직 링크 |
| Brute force | rate limiting 직접 구현 필요 | Supabase 가 처리 |
| 이메일 발송 | 별도 SMTP/SES 통합 | Supabase 기본 (또는 SMTP 연동) |
| 다중 디바이스 | 같은 비번 공유 | 디바이스마다 매직 링크 |
| 외부 의존성 | 없음 | Supabase 가용성 |
| 도입 비용 | 1 hour | 2~3 hours |

매직 링크가 견고하지만, **익숙한 비밀번호 UX 가 무너지는** 단점이 있음 (이메일 받을 때까지 대기). 1인 사용자가 Supabase 락아웃 시 환경변수 우회 인증 채널이 사라지므로 응급 복구 절차를 미리 마련해 둘 것.

## 4-Gate 인증 흐름

```
[1] 사용자가 /login 에 이메일 입력
        ↓
[2] /api/auth/send-magic-link  ← PRIMARY GATE (server-side allowlist)
    - 비-allowlist 이메일도 generic 200 응답 (enumeration 방지)
    - allowlist 통과 시에만 supabase.auth.signInWithOtp() 호출
        ↓
[3] Supabase 가 OTP 가 박힌 매직 링크 이메일 발송
        ↓
[4] /auth/callback?code=...     ← SECONDARY GATE (allowlist 재검증)
    - supabase.auth.exchangeCodeForSession(code)
    - getUser() 후 allowlist 재검증 (직접 OTP API replay 방어)
    - 통과 시 cookie 발급 후 / 로 redirect
```

각 Gate 의 의미:

- **[2] PRIMARY GATE** — 외부에서 이 라우트를 호출하면서 임의 이메일을 시도해도 OTP 발송 자체가 안 됨
- **[4] SECONDARY GATE** — 누군가 Supabase 직접 API 로 OTP 를 받았어도 allowlist 가 아닌 이메일로 로그인 안 됨 (defense-in-depth)
- **middleware** — 모든 protected 경로에서 `getUser()` 결과가 allowlist 인지 매 요청 검증

## 핵심 파일 (japa 케이스 기준)

| 파일 | 역할 |
|---|---|
| `lib/supabase/server.ts` | 서버 컴포넌트용 SSR client 생성 (`createServerClient`) |
| `lib/supabase/client.ts` | 클라이언트용 (`createBrowserClient`) |
| `lib/supabase/middleware.ts` | middleware 의 `updateSession` 헬퍼 (cookie refresh) |
| `lib/auth/allowlist.ts` | `getOwnerEmail()`, `isAllowedEmail(email)` |
| `app/api/auth/send-magic-link/route.ts` | PRIMARY GATE — allowlist 통과 시 `signInWithOtp` |
| `app/auth/callback/route.ts` | SECONDARY GATE — `exchangeCodeForSession` + allowlist 재검증 |
| `app/api/auth/logout/route.ts` | `supabase.auth.signOut()` |
| `middleware.ts` | matcher 에서 `/api/cron` 제외 후 `updateSession` 위임 |

## allowlist 헬퍼

```ts
// lib/auth/allowlist.ts
export function getOwnerEmail(): string {
  const v = process.env.OWNER_EMAIL;
  if (!v) throw new Error("OWNER_EMAIL not set");
  return v.toLowerCase().trim();
}

export function isAllowedEmail(email: string | null | undefined): boolean {
  if (!email) return false;
  return email.toLowerCase().trim() === getOwnerEmail();
}
```

다중 admin 으로 확장하려면 `OWNER_EMAILS=a@x.com,b@y.com` + 배열 비교로 변경.

## PRIMARY GATE — 매직 링크 발송

```ts
// app/api/auth/send-magic-link/route.ts
export async function POST(req: NextRequest) {
  const { email } = await req.json();

  // generic 200 응답 시간을 일정하게 유지 (timing attack 방어)
  if (!isAllowedEmail(email)) {
    await new Promise((r) => setTimeout(r, 200));
    return NextResponse.json({ ok: true });   // generic 응답
  }

  const supabase = createServerClient(...);
  const { error } = await supabase.auth.signInWithOtp({
    email,
    options: { emailRedirectTo: `${process.env.NEXT_PUBLIC_APP_URL}/auth/callback` },
  });
  if (error) return NextResponse.json({ ok: true });   // 동일하게 generic
  return NextResponse.json({ ok: true });
}
```

핵심:

- **항상 200 + 동일한 페이로드** — 비-allowlist 이메일을 시도해도 응답으로 구별 불가 (enumeration 방지)
- **응답 시간 일정화** — 매직 링크 발송 시 발생하는 지연을 비-allowlist 경로에서도 모방 (timing attack 방어, 단순화 시 200ms sleep)

## SECONDARY GATE — callback 재검증

```ts
// app/auth/callback/route.ts
export async function GET(req: NextRequest) {
  const code = new URL(req.url).searchParams.get("code");
  if (!code) return NextResponse.redirect(new URL("/login?error=missing_code", req.url));

  const supabase = createServerClient(...);
  const { data, error } = await supabase.auth.exchangeCodeForSession(code);
  if (error || !data.user) {
    return NextResponse.redirect(new URL("/login?error=exchange_failed", req.url));
  }

  // Defense-in-depth: allowlist 재검증
  if (!isAllowedEmail(data.user.email)) {
    await supabase.auth.signOut();
    return NextResponse.redirect(new URL("/login?error=not_allowed", req.url));
  }

  return NextResponse.redirect(new URL("/", req.url));
}
```

## middleware

```ts
// middleware.ts
export async function middleware(req: NextRequest) {
  return await updateSession(req);   // Supabase cookie refresh + getUser() 검증
}

export const config = {
  matcher: [
    // /api/cron 은 cookie 없이 CRON_SECRET Bearer 로 인증 → matcher 제외
    "/((?!_next/static|_next/image|favicon.ico|api/cron|login|auth/callback|api/auth).*)",
  ],
};
```

`updateSession` 안에서 `getUser()` 호출 후 allowlist 검증 → 비-allowlist 면 `/login` redirect.

## Supabase Dashboard 설정

| 설정 | 값 |
|---|---|
| Site URL | `https://<production>.vercel.app` (fallback) |
| Redirect URLs (화이트리스트) | `http://localhost:3000/auth/callback`, `https://<production>.vercel.app/auth/callback` |
| Email Templates → Magic Link | 한국어 텍스트로 변경 (선택) |

**Redirect URLs 가 핵심** — 코드가 `emailRedirectTo` 를 명시하면 Site URL 은 단순 fallback. Redirect URLs 화이트리스트에 등록되지 않은 URL 은 Supabase 가 거부.

## 환경변수

| 키 | 값 예시 |
|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | `https://<project>.supabase.co` |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | `eyJh...` (anon, 공개 가능) |
| `OWNER_EMAIL` | `me@example.com` |
| `NEXT_PUBLIC_APP_URL` | `http://localhost:3000` (로컬) / `https://<prod>.vercel.app` (운영) |

`NEXT_PUBLIC_APP_URL` 을 환경별로 다르게 두는 것이 핵심. 코드는 `${process.env.NEXT_PUBLIC_APP_URL}/auth/callback` 으로 통일.

## Cron 라우트 분리

Vercel Cron 은 cookie 없이 호출 → middleware 가 redirect 하면 cron 자체가 실패. 해결:

```ts
// middleware matcher 에서 /api/cron 제외 (위 config 참조)

// app/api/cron/daily/route.ts
export async function GET(req: NextRequest) {
  const auth = req.headers.get("authorization");
  if (auth !== `Bearer ${process.env.CRON_SECRET}`) {
    return new Response("Unauthorized", { status: 401 });
  }
  // ... cron 로직
}
```

middleware matcher 에서 분리 + 라우트 핸들러 내부 Bearer 검증 = 두 단계 보호.

## 함정

- **Vercel 환경변수 변경 후 자동 재배포 안 됨** — `Redeploy` 수동 트리거 필요. 변경 후 동작 안 하면 가장 먼저 의심.
- **Site URL vs Redirect URLs 혼동** — 코드가 `emailRedirectTo` 명시하면 Site URL 은 fallback 일 뿐. Redirect URLs 화이트리스트가 핵심.
- **로컬·운영 분리** — Redirect URLs 에 `localhost:3000/auth/callback` + `<prod>/auth/callback` 둘 다 등록.
- **OTP code 재사용 방어** — Supabase 가 처리하지만, 사용자에게 「만료된 링크입니다」 에러 페이지 제공 필요.
- **이메일 발송 실패** — `signInWithOtp` 가 200 으로 응답해도 실제 이메일 발송 실패 가능 (Supabase free tier 의 일일 한도 등). 사용자가 「이메일이 안 와요」 보고 시 Supabase Dashboard → Logs → Auth 확인.
- **환경 변수 락아웃** — `OWNER_EMAIL` 을 잘못 변경하면 자기 자신이 못 들어감. 변경 시 Supabase Dashboard 에서 직접 user 추가/수정 가능.

## 결론

- 1인용 앱이라도 매직 링크 + allowlist 가 자체 비밀번호 + 세션 쿠키 보다 견고
- defense-in-depth 가 핵심 — PRIMARY (발송) + SECONDARY (callback) + middleware 3단 검증
- generic 응답 + 응답 시간 일정화로 email enumeration 차단
- Cron / webhook 같은 cookie-less 경로는 middleware matcher 분리 + Bearer 토큰 별도 인증
- 락아웃 응급 복구 절차 (Supabase Dashboard 직접 user 관리) 를 미리 점검

## 관련 페이지

- [[nextjs-vercel-supabase-deployment]] — Supabase pooler / Vercel 통합 결정
- [[japa-asset-dashboard]] — 본 패턴 적용 사례 (commit e1bdb4a)
