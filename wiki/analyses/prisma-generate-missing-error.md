---
title: "Prisma 클라이언트 미생성으로 인한 findMany 에러"
domain: "personal"
sensitivity: "public"
tags: ["prisma", "nextjs", "bug-pattern", "typescript", "setup"]
created: "2026-04-29"
updated: "2026-04-29"
sources:
  - "session-logs/20260428-231410-9f52-현재-프로젝트-실행시-에러가-발생하고-있습니다.-##-Error-Type-Runtime-T.md"
confidence: "high"
related:
  - "wiki/analyses/prisma-decimal-nextjs-serialization.md"
---

# Prisma 클라이언트 미생성으로 인한 findMany 에러

## 증상

```
RuntimeTypeError: Cannot read properties of undefined (reading 'findMany')
    at measure ([native code]:null:null)
```

Next.js(Turbopack) 환경에서 `npm run dev` 실행 시 Prisma 모델 접근 시 발생.

두 번째 에러 `at measure ([native code])` 는 별도 에러가 아니라 첫 번째 에러가 Next.js 내부에서 전파되는 경로 — 혼동 주의.

## 원인

`npx prisma generate`가 실행된 적 없어서 `.prisma/client/` 디렉터리가 비어 있거나 존재하지 않음. `PrismaClient` 인스턴스에 `account`, `holding` 등의 모델 delegate가 없어 `prisma.account.findMany()` 호출 시 `undefined.findMany()`로 실패.

```bash
# 확인 방법
ls node_modules/.prisma/client/
# 비어있으면 문제
```

## 해결

```bash
npx prisma generate
# 또는
npm run prisma:generate
```

실행 후 `.prisma/client/` 에 `index.d.ts`, native query engine, `default.js` 등 생성됨.

## 재발 방지 패턴

`package.json`의 `build` 스크립트에는 보통 `prisma generate`가 포함되어 있지만 **`dev` 스크립트에는 없는 경우가 많다.** `node_modules` 재설치 후 dev 서버를 바로 실행하면 같은 문제가 반복된다.

```json
{
  "scripts": {
    "dev": "prisma generate && next dev",
    "build": "prisma generate && next build"
  }
}
```

`dev` 스크립트에도 `prisma generate &&`를 추가하면 매번 실행마다 자동으로 클라이언트가 갱신된다. 스키마가 자주 바뀌지 않는다면 `prepare` 스크립트에 넣는 방법도 있다:

```json
{
  "scripts": {
    "prepare": "prisma generate"
  }
}
```

## 발생 맥락

`node_modules`를 새로 설치(`npm install` / `pnpm install`)한 뒤 dev 서버를 실행할 때. 기존 `.prisma/client/`가 삭제되거나 초기 셋업 시.

## 관련

[[prisma-decimal-nextjs-serialization]] — 다른 Prisma+Next.js 이슈: Decimal 타입이 Server→Client 직렬화 경계에서 오류를 일으키는 패턴

## 변경 이력

- 2026-04-29: 최초 생성 — asset-dashboard 프로젝트에서 발생한 에러 해결 기록
