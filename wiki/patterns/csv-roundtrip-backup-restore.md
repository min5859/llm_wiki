---
title: "CSV round-trip 백업·복원 — 외래키 ID 와 N:M 보존"
domain: both
sensitivity: public
tags: ["pattern", "csv", "backup", "restore", "prisma", "database-migration", "round-trip"]
created: 2026-05-12
updated: 2026-05-12
source_session: "20260512-000725-28e8-DB-에-있는-내용을-모두-CSV-로-export-한-뒤-계좌-내용을-초기화-하고-다시-추.md"
sources:
  - "session-logs/20260512-000725-28e8-DB-에-있는-내용을-모두-CSV-로-export-한-뒤-계좌-내용을-초기화-하고-다시-추.md"
confidence: high
related:
  - "wiki/patterns/supabase-region-migration.md"
  - "wiki/projects/japa-asset-dashboard.md"
---

# CSV round-trip 백업·복원 — 외래키 ID 와 N:M 보존

DB 의 전체 데이터를 사람이 읽을 수 있는 CSV 로 export 한 뒤, 동일 CSV 로 다시 import 했을 때 **원래 상태가 그대로 복원** 되는 백업·복원 워크플로의 설계 패턴. 단순 csv export 가 import-안 되는 가장 흔한 원인은 **외래키 ID 누락** + **N:M 관계 직렬화 부재**.

## 사용 사례

- DB 리전 / 호스팅 이전 (e.g. Supabase 뭄바이 → 서울, RDS → Aurora) → [[supabase-region-migration]]
- 정기 cold backup (S3 등) 으로 시점별 스냅샷 보관
- 개발용 데이터 dump (회사용 → 로컬, 익명화 후 공유)
- 대규모 운영 변경 전 안전망 (DB 스키마 migration / 잘못된 cron 적용 / mass update 사고 대비)

## 흔한 함정 — 외래키 ID 누락

UI 표시 편의로 `holdings.csv` 에 `accountName` 컬럼을 두지만 `accountId` 컬럼이 없는 형태가 자주 보인다. 이 상태에서는:

1. accounts.csv 를 먼저 import → 새 UUID/cuid 가 생성됨 (원본과 다름)
2. holdings.csv 의 `accountName` 으로 join 시도 → 동명이인 계좌가 있으면 깨짐, 정렬 불안정
3. N:M 매핑 (계좌 그룹 등) 은 더 심함 — `;-구분 accountNames` 만 있으면 ambiguity 폭발

→ **export 시점에 외래키 ID 를 CSV 에 박아 두는 것이 round-trip 의 1차 조건**.

## 5 단계 round-trip 설계

### 1. Export — 외래키 ID + 사람이 읽는 컬럼 모두 유지

```typescript
const EXPORTS: Record<string, CsvExport> = {
  accounts: {
    headers: ["id", "name", "type", "currency", "cashBalance", "createdAt", "updatedAt"],
    build: async () => (await prisma.account.findMany()).map(a => [a.id, a.name, ...])
  },

  // N:M 은 ;-구분 ID 목록으로 직렬화 — 단일 CSV 로 round-trip
  groups: {
    headers: ["id", "name", "description", "displayOrder", "accountIds", "createdAt", "updatedAt"],
    build: async () => {
      const rows = await prisma.accountGroup.findMany({
        include: { accounts: { select: { id: true } } }
      });
      return rows.map(g => [
        g.id, g.name, g.description ?? "", g.displayOrder,
        g.accounts.map(a => a.id).join(";"),   // ← N:M 직렬화
        ...
      ]);
    }
  },

  holdings: {
    headers: ["id", "accountId", "accountName", "name", "symbol", ...],
    //                ^^^^^^^^^ 외래키 (필수)  ^^^^^^^^^^^ 사람이 읽기 (옵션)
    ...
  },

  transactions: {
    headers: ["id", "accountId", "holdingId", "type", "tradeDate", ...],
    //                ^^^^^^^^^  ^^^^^^^^^ 다중 외래키 모두 박음
  },

  dividends: { headers: ["id", "accountId", "holdingId", ...], ... }
};
```

### 2. Export — UTF-8 BOM 포함

Excel 한글 깨짐 방지. `﻿` 를 CSV 본문 앞에 prepend.

```typescript
return new Response("﻿" + csv, {
  headers: { "Content-Type": "text/csv; charset=utf-8" }
});
```

### 3. Reset — 명시적 확인 필수

destructive 작업이므로 클라이언트에서 "RESET" 텍스트 입력 + 브라우저 confirm 2 중 확인:

```typescript
"use server";
export async function resetAccountData(_state, formData: FormData) {
  if (formData.get("confirmation") !== "RESET") {
    return { error: "확인 텍스트가 일치하지 않습니다", message: null };
  }
  await prisma.$transaction([
    prisma.dividend.deleteMany(),
    prisma.transaction.deleteMany(),
    prisma.holding.deleteMany(),
    // accountGroup-account 매핑은 cascade
    prisma.accountGroup.deleteMany(),
    prisma.account.deleteMany(),
  ]);
  // 시장 시세 캐시·AI 분석·채팅·스냅샷은 보존 (대상 외)
  return { error: null, message: "초기화 완료" };
}
```

선택 기준:
- "RESET" 텍스트 입력 (서버) + 브라우저 confirm (클라이언트) 의 2중 가드
- delete 순서는 외래키 의존성 역순 (자식 → 부모)
- 운영 데이터 외 (캐시/AI 분석/스냅샷) 는 명시적으로 대상 외로 분리

### 4. Import — 의존성 순서 + N:M 별도 처리

`createMany` 는 N:M relation connect 를 지원하지 않으므로 N:M 만 분리:

```typescript
export async function importAccountData(_state, formData: FormData) {
  const files = ['accounts', 'groups', 'holdings', 'transactions', 'dividends'];
  const parsed: Record<string, any[]> = {};
  for (const f of files) parsed[f] = parseCsv(await formData.get(f).text());

  await prisma.$transaction([
    // 1) 부모 (accounts) 부터, CSV 의 id 그대로 유지
    prisma.account.createMany({
      data: parsed.accounts.map(row => ({
        id: row.id, name: row.name, type: row.type, ...
      })),
    }),

    // 2) groups (현재는 N:M connect 없이 메타만)
    prisma.accountGroup.createMany({
      data: parsed.groups.map(row => ({
        id: row.id, name: row.name, displayOrder: parseInt(row.displayOrder), ...
      })),
    }),

    // 3) 자식들 (외래키 row.accountId 그대로 사용)
    prisma.holding.createMany({ data: parsed.holdings.map(row => ({
      id: row.id, accountId: row.accountId, ...
    })) }),
    prisma.transaction.createMany({ data: ... }),
    prisma.dividend.createMany({ data: ... }),
  ]);

  // 4) N:M connect — createMany 가 지원 안 하므로 group 별 update 별도 처리
  for (const g of parsed.groups) {
    const accountIds = g.accountIds.split(";").filter(Boolean);
    if (accountIds.length) {
      await prisma.accountGroup.update({
        where: { id: g.id },
        data: { accounts: { connect: accountIds.map(id => ({ id })) } },
      });
    }
  }

  return { error: null, message: "Import 완료" };
}
```

### 5. `@updatedAt` 강제 갱신 인지

Prisma 의 `@updatedAt` 컬럼은 모든 write 마다 자동 갱신되므로 CSV 의 `updatedAt` 값은 import 시점에 덮어쓰임. round-trip 에서 보존되지 않는 유일한 컬럼.

대응:
- `createdAt` 만 CSV 에서 복원 (Prisma 가 강제 갱신 안 함)
- 사용자에게 명시 — "import 시 updatedAt 은 import 시각으로 재설정됩니다"
- 정말 보존이 필요하면 `$executeRaw` 로 raw insert (Prisma 우회) → 대부분의 경우 불필요

## RFC 4180 호환 CSV 파서

운영 데이터에는 메모 컬럼에 콤마·줄바꿈·따옴표가 섞이는 게 일반. 직접 `split(",")` 으로 짜면 깨지므로 RFC 4180 미니 파서 작성:

```typescript
function parseCsv(text: string): string[][] {
  if (text.charCodeAt(0) === 0xfeff) text = text.slice(1); // BOM
  const rows: string[][] = [];
  let row: string[] = [], cell = "", inQuote = false;
  for (let i = 0; i < text.length; i++) {
    const c = text[i];
    if (inQuote) {
      if (c === '"' && text[i+1] === '"') { cell += '"'; i++; }
      else if (c === '"') inQuote = false;
      else cell += c;
    } else {
      if (c === '"') inQuote = true;
      else if (c === ',') { row.push(cell); cell = ""; }
      else if (c === '\n' || (c === '\r' && text[i+1] === '\n')) {
        row.push(cell); rows.push(row); row = []; cell = "";
        if (c === '\r') i++;
      } else cell += c;
    }
  }
  if (cell || row.length) { row.push(cell); rows.push(row); }
  return rows;
}
```

처리해야 할 케이스:
- 따옴표 escape (`""` → `"`)
- CRLF / LF 혼재
- 콤마 / 개행 포함 필드
- BOM 제거

## 단일 페이지 UI 의 흐름 강제

운영 데이터를 다루는 화면이므로 실수 방지가 1차 목표:

```
/settings/data
├── 1. CSV 내보내기 — 5개 파일 다운로드 버튼
├── 2. 초기화        — "RESET" 입력 + confirm
└── 3. CSV 가져오기  — 5-파일 업로드 + 실행
```

흐름이 한 페이지에 있으면 사용자가 "다운로드 안 받고 reset" 같은 실수를 덜 한다. 페이지 분리하면 reset 페이지에 직접 들어가 작업하는 경로가 생김.

## PgBouncer transaction-mode 호환

Prisma 의 interactive `$transaction(callback)` 은 PgBouncer transaction-mode 와 호환 안 됨. **array form** 으로 변경:

```typescript
// fragile (interactive, PgBouncer transaction-mode 와 충돌)
await prisma.$transaction(async (tx) => {
  await tx.account.deleteMany();
  ...
});

// good (array form)
await prisma.$transaction([
  prisma.dividend.deleteMany(),
  prisma.transaction.deleteMany(),
  ...
]);
```

→ [[pgbouncer-direct-url-hybrid-routing]]

## 일반 원칙

1. **외래키 ID 컬럼이 round-trip 의 첫 번째 조건** — 사람이 읽기용 이름 컬럼은 보너스
2. **N:M 은 단일 CSV 안에 `;-구분 ID 목록` 으로 직렬화** — 별도 매핑 테이블 CSV 도 가능하지만 파일 수 증가
3. **delete 순서는 외래키 역순, create 순서는 외래키 순방향** — `$transaction` 안에서 명시적 순서
4. **destructive 작업은 2중 가드** — 텍스트 확인 + 브라우저 confirm
5. **운영 데이터 외는 명시적으로 대상 외** — 캐시/AI 분석/스냅샷은 reset 대상에서 분리. "본질적 사용자 데이터" 만 reset

## 안티 패턴

- **id 컬럼 없이 name 으로 join** — 동명이인 / 순서 변경 / 동기화 깨짐
- **N:M 을 별도 매핑 CSV 로 export 만 하고 import 미구현** — 매핑이 빈 채로 import 끝남
- **`$transaction(callback)` + PgBouncer transaction-mode** — connection 잡고 release 안 함, 사일런트 실패
- **`createMany skipDuplicates: true` 로 id 충돌 회피** — 새로 들어가는 데이터가 없어도 에러 없음. 빈 결과 모르고 넘어감. 정답은 "기존 행이 있으면 rollback" + 사전에 reset 강제

## 관련 페이지

- [[supabase-region-migration]] — 이 패턴을 활용한 리전 마이그레이션 워크플로
- [[japa-asset-dashboard]] — 적용 사례
- [[pgbouncer-direct-url-hybrid-routing]] — Prisma $transaction array form 의 배경

## 변경 이력

- 2026-05-12: 최초 작성 (session-logs/20260512-000725-28e8-*.md). japa CSV 백업/복원 워크플로 도입 작업을 일반화
