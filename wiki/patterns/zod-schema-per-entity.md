---
title: "Zod 스키마를 entity별 모듈로 분리하는 패턴"
domain: both
sensitivity: public
tags: ["zod", "typescript", "nextjs", "form-validation", "ssot", "react-hook-form", "prisma"]
created: 2026-05-03
updated: 2026-05-03
sources:
  - "session-logs/20260503-100914-b80f-*.md"
confidence: high
related:
  - "wiki/projects/japa-asset-dashboard.md"
---

# Zod 스키마를 entity별 모듈로 분리하는 패턴

서버 검증과 클라이언트 폼 검증이 동일한 Zod 스키마를 import 할 수 있게, **entity 단위로 `lib/<entity>/schema.ts` 1개 파일에 enum 배열, label map, Zod 폼 스키마, 추론 타입을 응집** 시키는 패턴. server action 안에 인라인으로 박혀 있던 검증 코드를 끌어내 SSOT (Single Source of Truth) 로 만든다.

## 안티 패턴 — 분산형

```
prisma/schema.prisma           # AccountType enum (8종) — DB 마스터
lib/labels.ts                  # ACCOUNT_TYPES 폼 select 옵션 배열 (label 포함)
app/actions/accounts.ts        # const AccountSchema = z.object({...})
                               # z.enum(["CHECKING", "SAVINGS", ...]) ← 인라인 enum 재정의
components/forms/account-form.tsx  # HTML required + onChange 빈 검증
```

문제점:
- **enum 중복** — Prisma 와 server action 두 곳에서 같은 string array. 한쪽 바꾸면 다른 쪽이 어긋남
- **Zod 스키마가 server action 안에 갇힘** — 폼 컴포넌트가 import 불가 → 진짜 검증은 서버 왕복 후에야 발생
- **에러 메시지 불일치 위험** — 폼 placeholder/required 메시지와 서버 Zod 메시지가 다를 수 있음

## 통합형 — `lib/<entity>/schema.ts`

```ts
// lib/accounts/schema.ts
import { z } from "zod";
import { AccountType, Currency } from "@prisma/client";  // Prisma SSOT 참조

type Option<T extends string> = { value: T; label: string };

export const ACCOUNT_TYPES: Option<AccountType>[] = [
  { value: "CHECKING", label: "입출금" },
  { value: "BROKERAGE", label: "증권" },
  // ...
];

export const ACCOUNT_TYPE_LABELS: Record<string, string> = Object.fromEntries(
  ACCOUNT_TYPES.map((o) => [o.value, o.label])
);

export const accountFormSchema = z.object({
  name: z.string().trim().min(1, "이름은 필수입니다").max(100),
  type: z.nativeEnum(AccountType),
  currency: z.nativeEnum(Currency),
  cashBalance: z.string().optional(),
  // ...
});

export type AccountFormInput = z.infer<typeof accountFormSchema>;
```

소비처:

```ts
// app/actions/accounts.ts (server)
import { accountFormSchema } from "@/lib/accounts/schema";
const parsed = accountFormSchema.safeParse(parseFormData(formData));

// components/forms/account-form.tsx (client)
import { ACCOUNT_TYPES, accountFormSchema, type AccountFormInput }
  from "@/lib/accounts/schema";
// + react-hook-form + zodResolver(accountFormSchema) 로 즉시 검증 가능

// app/accounts/page.tsx (server component)
import { ACCOUNT_TYPE_LABELS } from "@/lib/accounts/schema";
{ACCOUNT_TYPE_LABELS[account.type] ?? account.type}
```

## 효과

| 항목 | Before | After |
|---|---|---|
| Currency enum 중복 | 3개 server action 에 동일 string array | Prisma `Currency` SSOT (`z.nativeEnum`) |
| AccountType enum 중복 | Prisma + server action 2곳 | Prisma 1곳 |
| Form 옵션·라벨 출처 | `lib/labels.ts` 단일 (entity 무관) | `lib/<entity>/schema.ts` (응집도 ↑) |
| Form ↔ Server 검증 일관 | 폼이 검증 import 불가 | 동일 schema import 가능 |
| 새 enum 값 추가 | 4~5곳 동시 수정 | Prisma 1곳 + label 추가 (잘못된 string literal 은 컴파일 타임에 잡힘) |

## 도입 단계 — 점진적 확산

한 번에 전부 옮기지 말고 entity 단위로:

1. **시범 entity 1개** (보통 가장 단순한 것) — `lib/<entity>/schema.ts` 작성, server action / 폼 / 페이지 import 출처를 새 모듈로 통일, `lib/labels.ts` 에서 해당 entity 항목 제거
2. **나머지 entity 확산** — 한 번 패턴이 검증되면 동일 작업 반복 (보통 entity 당 30분~1시간)
3. **공유 항목은 공통 위치 유지** — 여러 entity 가 공유하는 enum (`CURRENCIES` 등) 은 `lib/labels.ts` 에 그대로
4. **(옵션) 폼 즉시 검증** — react-hook-form + `@hookform/resolvers/zod` 로 폼 제출 → 서버 왕복 → 에러 사이클을 클라이언트 즉시 검증으로 단축

## 자주 부딪히는 함정

### `Record<EnumType, string>` 노출 시 호출부 깨짐

라벨 맵을 엄격한 `Record<AssetClass, string>` 으로 export 하면, 호출부의 `?? holding.assetClass` fallback 패턴이 `string` 인덱싱을 전제로 해 컴파일 실패:

```
error TS7053: Element implicitly has an 'any' type because expression of type 'string'
can't be used to index type 'Record<AssetClass, string>'.
```

해결: 옵션 배열의 `Option<AssetClass>[]` 가 이미 SSOT 강제 책임을 지고 있으므로, **라벨 맵은 `Record<string, string>` 으로 노출** 해 호출부 호환을 유지하는 절충이 적절. 안전성은 옵션 배열 타입이 책임진다.

### Prisma enum 과 Zod enum 의 SSOT 정리

`z.nativeEnum(Prisma.AccountType)` 으로 Prisma enum 을 Zod 검증에 직접 주입. zod 4 에서도 동작 (4.x 에서 deprecation warning 가능 — 추후 `z.enum(Object.values(...))` 로 마이그레이션).

### `formData.get()` 과 안 맞는 필드는 별도 정규화

`FormData` 는 모든 값을 `string | File` 로 내보내므로, `.transform(Number)` / `.transform(s => s === "true")` 등으로 boolean / number 변환을 schema 안에 흡수. server action 시작부에 `parseFormData(formData)` 헬퍼로 `Object.fromEntries(formData)` 한 뒤 `safeParse` 하면 깔끔.

### 폼 select 옵션 배열은 `as const`

```ts
export const ACCOUNT_TYPES = ["general", "isa", "pension"] as const;
export type AccountType = (typeof ACCOUNT_TYPES)[number];
```

Prisma enum 을 안 쓰는 경우의 대안. `as const` 가 있으면 array → literal union 변환이 동작 + `z.enum(ACCOUNT_TYPES)` 도 같은 배열 그대로 사용 가능.

## 관련 맥락

- 본 패턴은 [[japa-asset-dashboard]] 의 4개 entity (Account/Holding/Dividend/Group) 일괄 적용 사례에서 추출
- Prisma 의 Decimal 직렬화 함정은 [[prisma-decimal-nextjs-serialization]]
- Next.js + Vercel 일반 배포 패턴은 [[nextjs-vercel-supabase-deployment]]

## 변경 이력

- 2026-05-03: 최초 생성. japa 프로젝트의 4개 entity 일괄 분리 사례에서 일반화 (출처: session-logs/20260503-100914-b80f-*).
