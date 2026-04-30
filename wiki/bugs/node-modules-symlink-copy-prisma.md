---
title: "node_modules 폴더 카피 시 심볼릭 링크 손실로 prisma wasm ENOENT 발생"
domain: both
sensitivity: public
tags: ["node_modules", "symlink", "prisma", "wasm", "enoent", "macos", "rsync", "cp"]
created: 2026-04-30
updated: 2026-04-30
sources:
  - "session-logs/20260430-135011-e8eb-*.md"
confidence: high
related:
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
  - "wiki/analyses/prisma-generate-missing-error.md"
---

# node_modules 카피 시 심볼릭 링크 손실 → prisma wasm ENOENT

다른 폴더에서 프로젝트를 통째로 카피해 온 직후 `npm run dev` 가 다음 에러로 죽는다.

```
node:fs:560
  return binding.open(
                 ^
Error: ENOENT: no such file or directory, open
  '/Users/wooki/project/git/wk/japa/node_modules/.bin/prisma_schema_build_bg.wasm'
```

## 원인

`node_modules/.bin/prisma` 가 **심볼릭 링크가 아니라 실제 파일로 카피**된 상태. 원래는 `../prisma/build/index.js` 를 가리키는 심볼릭 링크여야 한다 (`lrwxr-xr-x` 으로 표시되어야 정상).

폴더 카피 시 다음 도구들의 기본 동작이 심볼릭 링크를 풀어버린다:

| 도구 | 기본 동작 |
|---|---|
| `cp -r` | **심볼릭 링크 풀음** → 파일 본체 복사 |
| macOS Finder 드래그 | **심볼릭 링크 풀음** |
| `cp -a` | 심볼릭 링크 보존 |
| `rsync -aH` | 심볼릭 링크 + 하드링크 보존 |

심볼릭 링크가 풀린 결과 prisma 스크립트가 실행되면 `__dirname` (= `node_modules/.bin/`) 기준으로 `.wasm` 파일을 찾는데, `.bin/` 디렉터리에는 wasm 이 없어서 (실제 wasm 은 `node_modules/prisma/build/` 에 있음) ENOENT 발생.

## 진단

```bash
# 정상이면 lrwxr-xr-x 로 표시되고 → ../prisma/build/index.js 로 링크됨
ls -la node_modules/.bin/prisma

# file 명령어는 심볼릭 링크를 따라가서 실제 파일을 보여주므로
# 단독으로는 정상/비정상 구분이 어렵다
file node_modules/.bin/prisma
```

`ls -la` 결과의 첫 글자가 `l` 이면 정상. `-` (일반 파일) 이면 심볼릭 링크가 풀린 상태.

## 해결: 재설치

`.gitignore` 에 들어 있고 `package-lock.json` 이 있으니 안전하게 재현 가능.

```bash
rm -rf node_modules .next
npm install
```

`.next` 도 함께 지우는 게 깔끔하다 (빌드 캐시도 다른 절대경로 기준이라 카피 직후 무효).

## 예방: 카피 시 옵션 선택

### 방법 1 — `node_modules` 빼고 카피하고 재설치 (가장 안전)

```bash
rsync -av --exclude='node_modules' --exclude='.next' /원본/ /대상/
cd /대상 && npm install
```

추천 이유:
- `node_modules` 자체가 100MB 단위라 카피 시간이 길다.
- `.next` 도 600MB+ 인 경우가 흔함. 어차피 자동 재생성.
- 재설치가 OS · 노드 버전 차이를 흡수해 준다.

### 방법 2 — 심볼릭 링크 보존하며 카피

```bash
cp -a /원본/ /대상/        # -a 옵션이 심볼릭 링크 보존
# 또는
rsync -aH /원본/ /대상/    # -H 옵션으로 hardlink + symlink 보존
```

`node_modules` 까지 같이 옮기고 싶을 때만 사용. 일반적으론 방법 1 이 단순.

## 같은 메커니즘으로 깨질 수 있는 다른 도구

`node_modules/.bin/` 의 거의 모든 항목이 심볼릭 링크다. 카피로 풀린 경우 다음 패턴의 에러가 나올 수 있다:

- 도구가 `__dirname` 기준 상대 경로로 리소스를 찾을 때 ENOENT
- 일부 패키지는 `process.argv[1]` 기준 경로로 동작 → 같은 영향

증상이 다양하지만 해결책은 모두 `rm -rf node_modules && npm install` 로 동일.

## 관련 맥락

- `prisma generate` 자체가 빠졌을 때의 에러는 [[prisma-generate-missing-error]] 와 다른 종류 — 그쪽은 dev 스크립트에 `prisma generate` 가 없어서 발생, 이쪽은 generate 가 실행되기 전에 prisma 바이너리 자체가 wasm 을 못 찾아 죽는다.
- Vercel 배포 자체에는 영향 없다. `node_modules` 가 `.gitignore` 에 들어 있으니 GitHub 에 안 올라가고, Vercel 빌드 서버에서 `npm install` 로 새로 깔린다 — 카피 사고는 로컬 머신에서만 재현되는 문제.

## 변경 이력

- 2026-04-30: 최초 생성. japa 자산 대시보드를 다른 폴더에서 카피 후 `npm run dev` 실행 시 발생 (출처: session-logs/20260430-135011-e8eb-*)
