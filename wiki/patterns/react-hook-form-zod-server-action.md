---
title: "Next.js Server Actions + react-hook-form + Zod 동일 스키마 패턴"
domain: both
sensitivity: public
tags: ["nextjs", "react-hook-form", "zod", "server-actions", "form", "validation", "typescript"]
created: 2026-05-08
updated: 2026-05-08
sources:
  - "session-logs/20260507-230645-c555-tasks-plan-2026-05-03-japa-s-features.md-에-있는-내용중.md"
confidence: high
related:
  - "wiki/patterns/zod-schema-per-entity.md"
  - "wiki/projects/japa-asset-dashboard.md"
---

# Next.js Server Actions + react-hook-form + Zod 동일 스키마 패턴

`lib/<entity>/schema.ts` 의 Zod 스키마를 클라이언트 (react-hook-form) 와 서버 (server action) 양쪽에서 재사용해 중복 검증 로직을 제거하면서, 클라이언트 즉시 검증으로 폼 UX 를 개선하는 패턴. [[zod-schema-per-entity]] 의 후속 단계로 Wiring 만 추가하는 형태.

## 동기

스키마 단일화 ([[zod-schema-per-entity]]) 만으로는 폼이 여전히 *제출 → 서버 라운드트립 → 에러 표시* 사이클. 클라이언트에서도 같은 스키마로 검증하면 blur/submit 즉시 에러 표시 가능. 단 서버 검증은 **반드시 유지** (defense-in-depth).

## 설치

```bash
npm i react-hook-form @hookform/resolvers zod
# japa 검증 버전: react-hook-form 7.75 + @hookform/resolvers 5.2 + zod 4.3
```

## 핵심 — input/output 타입 분리 (resolvers v5)

`@hookform/resolvers@5+` 부터 input/output 타입이 분리되었다. Zod 의 `coerce.number()`, `default(...)`, `transform(...)` 등이 있으면 `z.input<S>` ≠ `z.output<S>` 가 되어 다음 코드가 TS2719 (Two different types with this name exist, but they are unrelated) 로 깨진다:

```ts
// ❌ 컴파일 깨짐
const form = useForm({ resolver: zodResolver(schema) });
```

해결 — 3-제너릭 명시:

```ts
// ✅
type Input = z.input<typeof accountFormSchema>;
type Output = z.output<typeof accountFormSchema>;

const form = useForm<Input, unknown, Output>({
  resolver: zodResolver(accountFormSchema),
});
```

`Input` 은 폼이 다루는 raw 값 (text input 의 string 등), `Output` 은 검증·변환 후 server action 에 넘기는 정규화 값 (number, enum 등). 같은 스키마지만 두 타입은 다르다.

## 폼 ↔ Server Action 와이어링

```tsx
// components/forms/account-form.tsx
"use client";
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { useTransition } from "react";
import { accountFormSchema } from "@/lib/accounts/schema";
import { createAccount } from "@/app/actions/accounts";

export function AccountForm() {
  const [isPending, startTransition] = useTransition();
  const form = useForm<z.input<typeof accountFormSchema>, unknown, z.output<typeof accountFormSchema>>({
    resolver: zodResolver(accountFormSchema),
    defaultValues: { name: "", currency: "KRW", ... },
  });

  const onSubmit = form.handleSubmit((values) => {
    // values 는 이미 검증·변환된 Output 타입
    const fd = new FormData();
    Object.entries(values).forEach(([k, v]) => fd.append(k, String(v ?? "")));
    startTransition(async () => {
      const res = await createAccount(fd);   // 기존 server action 그대로 호출
      // 서버 에러 표시 / redirect 처리
    });
  });

  return (
    <form onSubmit={onSubmit}>
      <Input {...form.register("name")} />
      {form.formState.errors.name && <p>{form.formState.errors.name.message}</p>}
      ...
    </form>
  );
}
```

핵심:

- 검증 통과한 `values` 를 `FormData` 로 재구성해 **기존 server action 인터페이스를 그대로 사용** — server action 의 시그니처를 바꿀 필요 없음, 점진적 도입 가능
- server action 내부의 `accountFormSchema.parse(...)` 는 그대로 유지 (서버 검증 = defense-in-depth)
- shadcn/ui 의 `Input`/`Select`/`Textarea` 가 `forwardRef` 로 만들어졌으면 `{...register("field")}` spread 가 그대로 동작

## 자주 쓰는 헬퍼 패턴

### Cascade 자동 채움 (`setValue`)

```ts
// holding 폼: 6자리 코드 입력 → Yahoo lookup → symbol/currency/name 동시 채움
async function lookup() {
  const res = await detectSymbol(form.getValues("ticker"));
  if (res.ok) {
    form.setValue("symbol", res.symbol, { shouldValidate: true });
    form.setValue("currency", res.currency);
    if (!form.getValues("name")) form.setValue("name", res.name);   // 사용자 입력 보호
  }
}
```

### 체크박스 배열

```tsx
// group 폼의 accountIds — 기존 Set state + hidden inputs 패턴 폐기
{accounts.map((a) => (
  <label key={a.id}>
    <input type="checkbox" value={a.id} {...register("accountIds")} />
    {a.name}
  </label>
))}
```

`register("accountIds")` 한 줄로 충분. Zod 스키마는 `accountIds: z.array(z.string()).min(1)` 형태.

### Pending 상태

```ts
const [isPending, startTransition] = useTransition();
// 버튼 비활성화: <Button disabled={isPending || !form.formState.isValid}>
```

## 서버 측 — 동일 스키마 재사용

```ts
// app/actions/accounts.ts
"use server";
import { accountFormSchema } from "@/lib/accounts/schema";

export async function createAccount(fd: FormData) {
  const parsed = accountFormSchema.safeParse({
    name: fd.get("name"),
    currency: fd.get("currency"),
    ...
  });
  if (!parsed.success) return { error: parsed.error.format() };
  // parsed.data 는 z.output<S> 타입
  await prisma.account.create({ data: parsed.data });
}
```

## 도입 비용

| 항목 | 비용 |
|---|---|
| 의존성 추가 | 약 50KB gzipped (react-hook-form 12KB + resolvers 1KB + zod 12KB; zod 는 이미 도입됐으면 0) |
| 폼 1개 마이그레이션 시간 | 30분 ~ 1시간 (entity 당). 점진 적용 가능 |
| TypeScript 학습 비용 | input/output 분리 1회 이해하면 끝 |
| 실 서비스 RTT 절감 | 잘못 입력한 사용자가 50% 라면 서버 라운드트립이 절반 줄어듦 |

## 함정

- **resolvers v4 → v5 마이그레이션 시 TS2719** — 위 input/output 분리 미적용이 원인. 한 줄 변경.
- **shadcn/ui 가 아닌 자체 Input** 이 `forwardRef` 가 아니면 `register()` 가 ref 를 못 넘김. ref forwarding 추가 필요.
- **server action 의 FormData 키명 불일치**: 폼 필드명과 server action 의 `fd.get("...")` 키가 일치하는지 확인.
- **`defaultValues` 미설정 시 controlled/uncontrolled 경고**: react-hook-form 은 default value 가 있어야 controlled 로 동작. text 필드는 빈 문자열, number 는 `undefined` 또는 `0`.
- **server action 의 검증 결과를 폼에 다시 표시**: server-side 에러 (DB unique constraint 등) 는 `form.setError("name", { message: ... })` 로 입력 필드 옆에 표시.

## 결론

- [[zod-schema-per-entity]] 가 선결되어야 ROI 가 분명 — 스키마가 산재해 있으면 공통화부터
- input/output 3-제너릭은 보일러플레이트 1줄, 한번 익히면 끝
- 서버 검증은 절대 제거하지 말 것 (defense-in-depth)
- entity 당 30분 ~ 1시간이라 점진 도입 적합

## 관련 페이지

- [[zod-schema-per-entity]] — `lib/<entity>/schema.ts` 1-파일 응집 패턴 (선결)
- [[japa-asset-dashboard]] — 본 패턴 적용 사례 (commit 96a22e1)
