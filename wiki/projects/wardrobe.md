---
title: "wardrobe — 옷장 매칭 웹 앱"
domain: personal
sensitivity: internal
tags: ["nextjs", "tailwind", "vercel", "localstorage", "mvp", "wardrobe", "outfit-recommendation"]
created: 2026-05-03
updated: 2026-05-03
sources:
  - "session-logs/20260502-235145-8714-*.md"
confidence: high
related:
  - "wiki/analyses/web-app-storage-without-db.md"
  - "wiki/analyses/vercel-friendly-database-options.md"
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
---

# wardrobe — 옷장 매칭 웹 앱

옷장 속 의류를 이벤트·날씨·외출 장소에 맞춰 매칭해 주는 1인 전용 웹 앱. Next.js 15 (App Router) + Tailwind CSS v4 + 시드 JSON + LocalStorage 의 MVP 구성으로 시작. 프로젝트 디렉터리는 `~/project/git/wk/wardrobe`.

## 핵심 내용

### 기술 스택 (Vercel 표준 조합)

| 레이어 | 선택 | 이유 |
|---|---|---|
| 프레임워크 | Next.js 15 (App Router) | Vercel 무설정 배포 |
| 언어/UI | React 19 + TypeScript strict | App Router 표준 |
| 스타일 | Tailwind CSS v4 (PostCSS plugin) | `@tailwindcss/postcss` + `@import "tailwindcss"` 진입점 |
| 데이터 (MVP) | LocalStorage + 시드 JSON | 백엔드 도입 전까지 클라이언트 단독 |
| 디스플레이 폰트 | Playfair Display (serif) | 헤더·숫자에 사용 |
| 본문 폰트 | Geist Sans | 본문 |
| 배포 | Vercel (무설정) | GitHub App + Webhook |

### 도메인 모델

`src/lib/wardrobe.ts`:

- `ClothingItem` — 의류 1벌 (warmth 1–5, formality 1–5, category, color, pattern)
- `Outfit` — 코디 1세트 (여러 ClothingItem 의 조합)
- `MatchingContext` — 매칭 입력 (온도, 날씨, 상황, 장소)

### 주요 라우트

| 경로 | 화면 |
|---|---|
| `/` | 대시보드 (Welcome Back, 최근 추가, TOTAL/MOST USED/SAVED OUTFITS 통계) |
| `/wardrobe` | 내 옷장 (검색 + 카테고리 필터 + 그리드 + 새 아이템 추가) |
| `/recommend` | 오늘의 추천 (온도/날씨/상황/장소 4필드 폼 → 매칭) |
| `/outfits` | 저장된 코디 (북마크) |

### 주요 컴포넌트

- `src/components/Sidebar.tsx` — 클라이언트 컴포넌트, `usePathname` 으로 active 상태 (`aria-current="page"`) 표시
- `src/components/ItemCard.tsx` — 의류 카드 (W placeholder + 카테고리/색상/패턴 배지)
- `src/components/WardrobeView.tsx` — 옷장 그리드 + 검색·필터
- `src/components/RecommendForm.tsx` — 추천 입력 폼

### 디자인 토큰

- 액센트: forest green (`#1F4D3A`)
- 배경: 베이지
- 디스플레이: serif (Playfair Display) — `Welcome Back.`, 숫자 22, 카테고리명
- 본문: sans-serif (Geist Sans)
- 카드: 카테고리 / 색상 / 패턴 배지로 정보 표시 (실제 사진은 W placeholder)

## 세부 사항

### MVP 단계 — DB 없이 시작한 이유

[[web-app-storage-without-db]] 의 단계적 도입 패턴을 따름.

1. **현 단계 (LocalStorage + 시드 JSON)**: 도메인 로직·매칭 알고리즘·UI 검증
2. **Step 2 (사용자 업로드 추가)**: IndexedDB 로 옮김 (LocalStorage 5–10MB 제한, base64 의류 사진은 20–30벌이 한계)
3. **Step 3 (여러 기기 동기화 필요)**: Vercel Blob (이미지) + Neon/Supabase Postgres (메타데이터) 도입

도메인 모델이 굳기 전에 스키마 마이그레이션 비용을 치르지 않도록 일부러 미룸. "사진 표시" 와 "DB 필요" 는 별개 문제 — 사진은 `<img src>` 에 유효한 URL 만 있으면 되고, `/public/` 정적 파일이나 IndexedDB Blob 으로 충분.

### 매칭 알고리즘 — 단순 점수합산

ML 이나 규칙엔진을 의도적으로 배제. `MatchingContext` (온도/날씨/상황/장소) 를 받아 보온성·포멀도 점수합산으로 후보 의류를 정렬. 평면 순수함수 → 테스트 용이.

### Next.js 수동 스캐폴딩 (CLAUDE.md 충돌 회피)

`create-next-app` 은 디렉터리에 `CLAUDE.md` 같은 비표준 파일이 있으면 거부:

```
The directory wardrobe contains files that could conflict:
  CLAUDE.md
```

CLAUDE.md 를 보존하기 위해 수동으로 11개 파일 스캐폴딩:

- 필수 (5): `package.json`, `tsconfig.json`, `next.config.ts`, `src/app/layout.tsx`, `src/app/page.tsx`
- Tailwind v4 (2): `postcss.config.mjs` (`@tailwindcss/postcss` 플러그인), `src/app/globals.css` (`@import "tailwindcss"`)
- Git (1): `.gitignore`
- 문서 (2): `CLAUDE.md`, `README.md`
- 선택 (1): `eslint.config.mjs` (빌드/배포에 필수 아님)

또한 `tsconfig.json` 의 `paths` 에 `@/*` 별칭 (`"@/*": ["./src/*"]`) 추가 — `create-next-app` 은 자동으로 넣지만 수동 스캐폴딩은 누락하기 쉽다.

### 시드 데이터 보존 원칙

CLAUDE.md 에 "샘플 22개" 를 기준점으로 명시하다가 실제 시드는 10개로 축소 → 사용자 지적으로 22개 언급을 모두 제거하고 "샘플 의류 데이터" 로 일반화. 정확히 모르는 숫자는 CLAUDE.md 에 박지 않는다.

### 빌드 결과

```
Route (app)              Size   First Load JS
┌ ○ /                   161 B  106 kB
├ ○ /_not-found         994 B  103 kB
├ ○ /outfits            123 B  102 kB
├ ○ /recommend         1.52 kB 104 kB
└ ○ /wardrobe          1.57 kB 104 kB
+ First Load JS shared by all  102 kB
```

전 라우트 정적 생성 (`○ Static`) — Vercel hobby 에서 즉시 배포 가능.

### 커밋 이력

- `c1fb5de` chore: initial Next.js 15 + Tailwind v4 scaffold for Vercel deploy (11 파일, +338)
- `94fab63` feat: dashboard UI with sidebar layout and seed clothing data (11 파일, +6529/-40)

## 관련 맥락

- [[web-app-storage-without-db]] — DB 없이 데이터·이미지를 저장하는 4가지 옵션
- [[vercel-friendly-database-options]] — 향후 DB 도입 시 후보 (Neon/Vercel KV/Supabase/Turso)
- [[nextjs-vercel-supabase-deployment]] — Vercel 배포 7가지 핵심 결정 (Step 3 진입 시 참고)

## 변경 이력

- 2026-05-03: 최초 생성 (출처: session-logs/20260502-235145-8714-*) — Next.js 15 + Tailwind v4 스캐폴드, 4개 라우트, 시드 10개, 사이드바 레이아웃 + 디자인 (forest green + Playfair Display)
