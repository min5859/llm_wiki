---
title: "DB 없이 시작하는 웹 앱 — 데이터·이미지 저장 4가지 방법"
domain: both
sensitivity: public
tags: ["analysis", "frontend", "localstorage", "indexeddb", "static-files", "blob-storage", "mvp", "vercel"]
created: 2026-05-03
updated: 2026-05-03
source_session: 20260502-235145-8714-*.md
confidence: high
related:
  - "wiki/analyses/vercel-friendly-database-options.md"
  - "wiki/projects/wardrobe.md"
---

## 개요

개인 프로젝트 / MVP 에서 "웹에서 데이터를 보고 싶다" 는 요구는 자주 "DB 가 필요하다" 와 혼동된다. 실제로는 **데이터 저장 위치** 와 **이미지 저장 위치** 는 별개 문제이며, 둘 다 DB 없이 충분히 가능하다. 도메인 모델이 굳기 전에 스키마 마이그레이션 비용을 치르지 않기 위한 단계적 도입 가이드.

핵심 분리:
- "사진을 띄운다" = `<img src="...">` 에 유효한 URL 만 있으면 됨
- "옷·가계부·메모 등을 어디에 저장하느냐" = 이게 DB / LocalStorage / IndexedDB 결정

## 비교 내용

### 데이터 저장 — 4가지 옵션

| 방식 | 용량 | API | 적합 상황 | 트레이드오프 |
|---|---|---|---|---|
| **시드 JSON 모듈** (코드에 직접 import) | 디스크 한도 | `import data from './seeds.json'` | 데이터가 정적 (개발자 시드만) | 사용자 추가 불가, 배포에 포함됨 |
| **LocalStorage** | **5–10MB** | sync key-value (string) | 사용자 추가/삭제, 단순 메타데이터 | base64 이미지로 금세 한계, 동기 API |
| **IndexedDB** | 거의 무제한 (수십 GB) | async, indexed, Blob 지원 | 사용자 업로드 + 이미지 + 검색 | 코드 약간 복잡, Promise 래핑 필요 |
| **Cookies** | **4KB** | HTTP, 서버 전송 | 인증·세션 토큰 전용 | 데이터 저장에 부적합 (모든 요청에 첨부) |

### 이미지 저장 — 4가지 옵션

| 방식 | 적합 상황 | 트레이드오프 |
|---|---|---|
| **`/public/` 정적 파일** | 개발자 시드 사진 | 사용자가 직접 추가 못 함, 배포에 포함됨 |
| **base64 데이터 URI + LocalStorage** | 사용자가 폰으로 찍어 올린 사진 | LocalStorage 5–10MB 제한 → 의류 20–30벌이 한계 |
| **IndexedDB Blob** | 사용자 업로드 + 용량 여유 | 코드 약간 복잡, Promise 래핑 필요 |
| **외부 호스팅** (Vercel Blob, Cloudinary, S3) | 여러 기기 동기화·공유 | 외부 서비스 의존, 보통 DB 도 함께 도입 |

### 단계적 도입 패턴 (권장)

```
Step 1 (MVP):       시드 JSON + LocalStorage
Step 2 (사용자 업로드): + IndexedDB (Blob)
Step 3 (여러 기기 동기화): + Vercel Blob/Cloudinary + Postgres
```

| 단계 | 도입 신호 | 주요 변경 |
|---|---|---|
| **Step 1**: 시드 JSON + LocalStorage | "혼자 쓸 거고 도메인 모델도 미정" | `src/lib/<domain>.ts` 에 시드 + LocalStorage 어댑터 |
| **Step 2**: IndexedDB Blob 추가 | 사용자가 사진을 직접 올리고 싶다 (LocalStorage 5–10MB 한계 도달) | IndexedDB 어댑터 추가, 사진은 Blob 으로 저장 |
| **Step 3**: 외부 Blob + Postgres | "다른 기기에서도 보고 싶다" / "친구에게 공유하고 싶다" | Vercel Blob (이미지) + Neon/Supabase (메타데이터) — [[vercel-friendly-database-options]] 참고 |

각 단계는 **이전 단계의 데이터를 그대로 마이그레이션** 할 수 있도록 설계 (예: LocalStorage → IndexedDB 는 같은 JSON 구조 유지, IndexedDB → Postgres 는 같은 도메인 타입 유지).

### DB 없이의 한계 — 받아들일 수 있는가

DB 없이 가는 결정은 다음을 받아들이는 것:

1. **브라우저 단위로 데이터 격리** — 폰에서 추가한 데이터가 PC 에서 안 보임
2. **브라우저 캐시 청소 시 데이터 사라짐** — 백업 자산이 아님
3. **공유 불가** — 다른 사람과 같은 데이터 못 봄

이 셋이 받아들일 수 없는 시점이 곧 DB 도입 시점.

### LocalStorage 의 함정 — base64 이미지

LocalStorage 는 5–10MB string 한도. JPEG 200KB 사진을 base64 인코딩하면 약 270KB → **약 20–30벌** 이 한계. 데모 / 시드용 데이터는 OK 지만, 사용자 업로드를 받기 시작하면 즉시 막힌다.

또한 LocalStorage 는 **동기 API** — 큰 데이터를 직렬화/역직렬화할 때 메인 스레드 블록. IndexedDB 는 async 라 같은 데이터를 다뤄도 UI 가 안 멎는다.

### `<img>` 태그가 받는 src 종류

| src 형식 | 어디서 옴 | 예 |
|---|---|---|
| 상대 경로 | `/public/` 정적 파일 | `<img src="/clothes/jacket.jpg">` |
| 절대 URL | 외부 호스팅 | `<img src="https://blob.vercel-storage.com/...">` |
| `data:` URI | base64 (LocalStorage 등) | `<img src="data:image/jpeg;base64,/9j/4AA...">` |
| `blob:` URL | IndexedDB Blob (`URL.createObjectURL`) | `<img src="blob:https://app.com/uuid">` |

이미지를 어디에 두느냐는 src 의 종류만 바뀔 뿐, JSX/HTML 코드는 거의 같다. 즉 저장소 변경의 영향 범위가 작다.

### `URL.createObjectURL` 의 메모리 누수

IndexedDB Blob 을 `URL.createObjectURL(blob)` 로 변환하면, **명시적으로 `URL.revokeObjectURL(url)` 을 호출하기 전까지 메모리에 남는다**. React 의 `useEffect` cleanup 에서 revoke 하지 않으면 SPA 가 의류 카드를 100개 스크롤한 뒤 100MB+ 가 누적된다.

```ts
useEffect(() => {
  const url = URL.createObjectURL(blob);
  setSrc(url);
  return () => URL.revokeObjectURL(url);
}, [blob]);
```

## 결론

### 권장 판단 기준

| 질문 | 답 |
|---|---|
| "데이터를 웹에서 보고 싶어요" | **DB 불필요** — LocalStorage / IndexedDB / 시드 JSON 으로 충분 |
| "사진을 카드에 띄우고 싶어요" | **DB 불필요** — `/public/` 정적 / base64 / Blob URL 로 충분 |
| "여러 기기에서 같은 데이터를 보고 싶어요" | **DB 필요 시점** — Vercel Blob + Neon Postgres 도입 |
| "친구와 공유하고 싶어요" | **DB + Auth 필요 시점** — Supabase 가 묶음으로 빠름 |
| "브라우저 캐시 청소에도 데이터를 지키고 싶어요" | **DB 필요 시점** |

### 안티 패턴

- ❌ MVP 단계에서 "언젠가 DB 가 필요하니까" 라며 처음부터 Postgres + Prisma 도입 → 도메인 모델이 굳기 전에 스키마 마이그레이션 비용을 치르게 됨
- ❌ 단순 데이터를 Cookie 에 저장 (4KB 한도, 모든 요청에 첨부됨)
- ❌ 사용자 업로드 사진을 LocalStorage 에 base64 로 저장 (20–30장에서 막힘)
- ❌ IndexedDB Blob 을 `URL.createObjectURL` 로 변환만 하고 `URL.revokeObjectURL` 하지 않음 (메모리 누수)

## 관련 페이지

- [[vercel-friendly-database-options]] — Step 3 진입 시 DB 후보 비교
- [[nextjs-vercel-supabase-deployment]] — Step 3 진입 후 Vercel + Supabase 의 7가지 함정
- [[wardrobe]] — 본 패턴을 적용한 옷장 매칭 앱 (Step 1 단계)

## 변경 이력

- 2026-05-03: 최초 생성 (출처: session-logs/20260502-235145-8714-*) — wardrobe 프로젝트 시작 시의 "DB 없이 사진을 보고 싶다" 질문에서 도출
