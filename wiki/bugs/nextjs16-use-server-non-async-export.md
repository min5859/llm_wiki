---
title: "Next.js 16 — \"use server\" 파일은 async function 만 export 가능"
domain: both
sensitivity: public
tags: ["bug", "nextjs", "nextjs-16", "use-server", "server-action", "turbopack"]
created: 2026-05-12
updated: 2026-05-12
source_session: "20260512-000725-28e8-DB-에-있는-내용을-모두-CSV-로-export-한-뒤-계좌-내용을-초기화-하고-다시-추.md"
sources:
  - "session-logs/20260512-000725-28e8-DB-에-있는-내용을-모두-CSV-로-export-한-뒤-계좌-내용을-초기화-하고-다시-추.md"
confidence: high
related:
  - "wiki/patterns/react-hook-form-zod-server-action.md"
  - "wiki/projects/japa-asset-dashboard.md"
---

# Next.js 16 — `"use server"` 파일은 async function 만 export 가능

Next.js 16 Turbopack 부터 server actions 파일 (`"use server"` 디렉티브가 있는 파일) 의 export 규칙이 엄격해져, **async function 외의 export 가 있으면 런타임 에러로 페이지 전체가 깨진다**. 객체/상수/타입 alias 모두 거부.

## 증상

```
Runtime Error
A "use server" file can only export async functions, found object.
Read more: https://nextjs.org/docs/messages/invalid-use-server-value

Next.js version: 16.2.4 (Turbopack)
```

해당 server action 을 import 하는 페이지에 접속하면 즉시 발생. `npm run typecheck` 는 통과 (TypeScript 입장에서는 export 는 export). 런타임 에러로만 잡힘.

## 원인

`"use server"` 파일은 **client 가 import 해도 안전한 RPC 경계** 로 취급된다. async function 만 허용함으로써:

- client bundle 에 동기 값 (DB 핸들, secret 등) 이 실수로 새는 것 방지
- `await fn(args)` 형태의 RPC 호출 패턴 강제
- 직렬화 가능성 자동 검증 (값 export 하면 직렬화 보장 못 함)

Next.js 15 까지는 비-async export 가 warning 수준이었으나, 16 부터는 런타임 에러로 격상.

## 흔한 위반 패턴

```typescript
// app/actions/data.ts
"use server";

import { prisma } from "@/lib/prisma";

export type DataActionState = { error: string | null; message: string | null };
//                          ^^^ TYPE export — 빌드 시 erase 되므로 OK

const INITIAL_STATE: DataActionState = { error: null, message: null };
export { INITIAL_STATE as INITIAL_DATA_STATE };
//      ^^^^^^^^^^^^^ OBJECT export — Next.js 16 에서 RUNTIME ERROR

export async function resetAccountData(state, formData) { ... }
//                                                       ^^^ async function — OK

export async function importAccountData(state, formData) { ... }
```

타입 export 는 빌드 시점에 erase 되므로 런타임에 영향 없지만, 상수·객체·함수 (non-async) 는 모두 거부.

## 수정

```typescript
"use server";

export type DataActionState = { error: string | null; message: string | null };
// ^^^ 타입만 유지 (런타임 erase)

// 객체/상수는 다른 파일로 이동 또는 client 가 자체 정의
// e.g. components/data-management.tsx
//   const INITIAL: DataActionState = { error: null, message: null };

export async function resetAccountData(state, formData) { ... }
export async function importAccountData(state, formData) { ... }
```

### 어디로 옮길 것인가

| 원래 export | 어디로 |
|------------|--------|
| `INITIAL_STATE` 같은 상수 | 호출하는 client component 내부에 const 로 정의 (대부분 1 곳에서만 씀) |
| 헬퍼 함수 (`parseFormData` 같은 동기 util) | 별도 `lib/<entity>/utils.ts` (server-only or shared) |
| Zod 스키마 | 이미 `"use server"` 밖에 둠 (`lib/<entity>/schema.ts`) — [[zod-schema-per-entity]] |
| TypeScript 타입 | 그대로 유지 (런타임 erase) |

## 회귀 방지

- `useFormState` / `useActionState` 의 `INITIAL_STATE` 는 **client component 안에 직접 선언**. server action 파일에서 export 할 필요가 거의 없음
- ESLint 룰: `@typescript-eslint/no-restricted-exports` 또는 커스텀 룰로 `"use server"` 파일의 non-async-function export 차단 (Next.js 공식 룰이 아직 없음)
- typecheck 만 믿으면 runtime 에서 잡힘. dev 서버에서 페이지 한 번 열어보는 것이 회귀 검증

## 호환성 마이그레이션 시점

Next.js 15 → 16 업그레이드 시 점검 항목:
1. `grep -rn '"use server"' app/ lib/` 로 모든 server action 파일 식별
2. 각 파일에서 `^export ` 중 `async function` 가 아닌 것 검토
3. type-only export 는 OK, 그 외는 이전

## 동기 vs 비동기 함수

```typescript
"use server";

// ❌ 동기 함수 — 거부
export function buildQuery(...) { ... }

// ✅ async 로 변경 — 단순 wrapper 라도 OK
export async function buildQuery(...) { ... }
```

동기 로직이라도 `"use server"` 안에 두려면 `async` 키워드 강제. 의미적으로는 즉시 resolve 되는 Promise 지만 RPC 경계 통과를 위해 필요.

## 관련 페이지

- [[japa-asset-dashboard]] — 본 버그가 발생한 프로젝트
- [[react-hook-form-zod-server-action]] — server action 호출 client 패턴

## 변경 이력

- 2026-05-12: 최초 작성 (session-logs/20260512-000725-28e8-*.md). japa 의 `/settings/data` 페이지 도입 작업 중 발견된 Next.js 16 Turbopack 의 엄격해진 export 규칙
