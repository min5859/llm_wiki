---
title: "Prisma Decimal → Next.js Client Component 직렬화 오류"
domain: "personal"
sensitivity: "public"
tags: ["nextjs", "prisma", "serialization", "bug-pattern", "typescript", "server-components"]
created: "2026-04-26"
updated: "2026-04-26"
sources:
  - "session-logs/20260426-184740-49b5-지금-실행되는-next.js-에서-5-issues-라고-뜹니다.-##-Error-Type.md"
confidence: "high"
related:
  - "wiki/projects/openclaw.md"
---

# Prisma Decimal → Next.js Client Component 직렬화 오류

## 증상

Next.js App Router (Server Components → Client Components) 환경에서 다음 에러 발생:

```
Only plain objects can be passed to Client Components from Server Components.
Decimal objects are not supported.
  {id: ..., quantity: Decimal, averageCost: Decimal, ...}
                               ^^^^^^^
```

## 원인

Prisma는 숫자 정밀도 보장을 위해 `Decimal` 타입(`Prisma.Decimal`)을 사용한다. 이 객체는 React의 직렬화 경계(Server → Client)를 통과하지 못한다. 흔히 발생하는 패턴:

```typescript
// ❌ ...holding spread 시 Decimal 필드가 그대로 포함됨
export function enrichHolding(holding: Holding & { ... }) {
  const marketValueBase = toNumber(holding.manualPrice) * toNumber(holding.quantity);
  return {
    ...holding,          // quantity: Decimal, averageCost: Decimal ← 위험
    marketValueBase,
  };
}
```

`enrichHolding`의 반환값이 Client Component에 props로 전달되면 런타임 에러.

## 수정 패턴

spread 전에 Decimal 필드를 **명시적으로 `number`로 변환**해 override:

```typescript
// ✅ Decimal 필드를 number로 덮어쓴 뒤 spread
export function enrichHolding(holding: Holding & { ... }) {
  const qty = toNumber(holding.quantity);
  const avgCost = toNumber(holding.averageCost);
  const price = toNumber(holding.manualPrice);
  const fxRate = toNumber(holding.manualFxRate);

  const costBasisBase = qty * avgCost * fxRate;
  const marketValueBase = qty * price * fxRate;
  const unrealizedGainBase = marketValueBase - costBasisBase;

  return {
    ...holding,
    // Decimal → number override (직렬화 경계 통과 보장)
    quantity: qty,
    averageCost: avgCost,
    manualPrice: price,
    manualFxRate: fxRate,
    dividendYield: toNumber(holding.dividendYield),
    costBasisBase,
    marketValueBase,
    unrealizedGainBase,
  };
}
```

### toNumber 유틸리티

```typescript
export function toNumber(value: unknown): number {
  if (typeof value === "number") return Number.isFinite(value) ? value : 0;
  if (typeof value === "string") {
    const parsed = Number(value.replaceAll(",", ""));
    return Number.isFinite(parsed) ? parsed : 0;
  }
  // Prisma.Decimal의 .toNumber() 메서드 지원
  if (value && typeof value === "object" && "toNumber" in value) {
    return (value as { toNumber: () => number }).toNumber();
  }
  return 0;
}
```

## 주의사항

- `edit` 페이지처럼 Server Component에서 raw Prisma 데이터를 그대로 Client Form에 넘기는 경우도 동일 문제 발생. Form에 전달할 props도 반드시 직렬화 가능 타입으로 변환 필요.
- `createdAt`, `updatedAt`(`Date` 타입)도 엄밀히는 직렬화 이슈를 일으킬 수 있으나 Next.js가 자동으로 ISO 문자열로 변환하는 경우가 많다. `Date` 오류가 발생하면 `.toISOString()` 처리.
- TypeScript 레벨에서 반환 타입에 `Prisma.Decimal`이 포함되어 있어도 컴파일 오류는 나지 않는다 (`tsc --noEmit` 통과). 런타임에만 드러나는 버그.

## 언제 발생하는가

| 조건 | 발생 여부 |
|------|----------|
| Prisma 모델에 `Decimal` 타입 필드 + Client Component에 props 전달 | ✅ 발생 |
| Server Component에서만 렌더링 (Client로 안 넘김) | ❌ 안전 |
| `toNumber()` 등으로 명시 변환 후 전달 | ❌ 안전 |

## 변경 이력

- 2026-04-26: 최초 작성 (asset-dashboard Phase 3 작업 중 발견)
