---
title: "GitHub Pages 사용자 페이지 vs 프로젝트 페이지의 BASE_PATH 자동 대응 패턴"
domain: both
sensitivity: public
tags: ["analysis", "github-pages", "static-site", "base-path", "deployment", "github-actions", "rss"]
created: 2026-05-08
updated: 2026-05-08
source_session: 20260507-225855-4c7c-현재-프로그램을-분석해-주세요.md
related:
  - "wiki/projects/dev-blog.md"
  - "wiki/analyses/nextjs-vercel-supabase-deployment.md"
---

## 개요

GitHub Pages 는 repo 이름에 따라 배포 URL 구조가 달라진다 — `<id>.github.io` 는 root 도메인, 그 외는 서브경로. 정적 사이트의 모든 내부 링크 + RSS 절대 URL + 클라이언트 사이드 fetch 가 두 경우 모두 정상 동작하려면 **빌드 시 `BASE_PATH` 추상화** 가 필요하다. 자체 구현 정적 빌더에서도 *처음부터* 도입하는 게 후속 배포 옵션 (Cloudflare Pages, 사내 서버 서브경로 등) 의 마찰을 줄임.

## 두 가지 배포 형태

| repo 이름 | 배포 URL | base path |
|---|---|---|
| `<id>.github.io` (사용자 페이지) | `https://<id>.github.io/` | `""` (root) |
| 그 외 (프로젝트 페이지) | `https://<id>.github.io/<repo>/` | `/<repo>` |

## 패턴: 빌드 시 prefix 주입

```js
// scripts/build-site.mjs
const BASE_PATH = process.env.BASE_PATH || '';   // "" 또는 "/dev-blog"
const SITE_URL  = process.env.SITE_URL  || '';   // "https://<id>.github.io/dev-blog"

const url = (p) => `${BASE_PATH}${p}`;            // 내부 상대 경로
const absoluteUrl = (p) => `${SITE_URL}${BASE_PATH}${p}`;  // RSS 절대 경로
```

빌드 시 모든 `href="/..."`, `src="/..."`, `fetch('/...')`, RSS `<link>`/`<guid>` 를 위 헬퍼로 통일.

- `BASE_PATH=""` → 로컬 미리보기 · root 도메인 배포에서 그대로 동작 (`/archive.html`)
- `BASE_PATH=/dev-blog` → 프로젝트 페이지 배포에서 정상 (`/dev-blog/archive.html`)

## GitHub Actions 자동 주입

`.github/workflows/pages.yml` 에서 repo 이름을 보고 두 환경변수를 자동 결정하면, 같은 코드베이스가 두 배포 형태에 모두 대응:

```yaml
- name: Resolve base path
  run: |
    REPO="${GITHUB_REPOSITORY##*/}"
    OWNER="${GITHUB_REPOSITORY%%/*}"
    if [ "$REPO" = "${OWNER}.github.io" ]; then
      echo "BASE_PATH=" >> "$GITHUB_ENV"
      echo "SITE_URL=https://${OWNER}.github.io" >> "$GITHUB_ENV"
    else
      echo "BASE_PATH=/$REPO" >> "$GITHUB_ENV"
      echo "SITE_URL=https://${OWNER}.github.io/$REPO" >> "$GITHUB_ENV"
    fi
- run: npm run build
```

## 검증 명령

```bash
# 로컬 (root 배포 가정)
npm run build
grep -m1 'href=' public/index.html         # → /archive.html

# 프로젝트 페이지 배포 가정
BASE_PATH=/dev-blog SITE_URL=https://example.github.io/dev-blog npm run build
grep -m1 'href=' public/index.html         # → /dev-blog/archive.html
grep '<link>' public/feed.xml              # → https://example.github.io/dev-blog
grep '<guid>' public/feed.xml              # → https://example.github.io/dev-blog/posts/...
```

## 일반화 (다른 도구의 base path 옵션)

| 도구 | base 옵션 |
|---|---|
| Astro | `astro.config.mjs` 의 `base` |
| Vite | `vite.config.js` 의 `base` |
| Next.js | `next.config.js` 의 `basePath` |
| Hugo | `config.toml` 의 `baseURL` |
| Jekyll | `_config.yml` 의 `baseurl` |
| Docusaurus | `docusaurus.config.js` 의 `baseUrl` |

자체 구현 빌더에서도 동일한 1-변수 추상화 (`BASE_PATH`) 를 두면 충분. 도입 비용 낮고, 한번 빠뜨리면 배포 후 404 폭주의 원인.

## 함정

- **fetch URL 누락**: 검색 페이지의 `fetch('/search-index.json')` 같은 *클라이언트 사이드 fetch* 도 prefix 주입 필요. HTML 출력만 보고 끝내면 검색이 404.
- **RSS 절대 URL 이중 prefix**: `absoluteUrl()` 이 `BASE_PATH` 도 포함하는지 확인. `SITE_URL` 자체에 base path 가 들어 있으면 중복.
- **하드코딩된 슬래시**: `<a href="/posts/${id}.html">` 같이 템플릿 리터럴 안의 `/` 도 모두 헬퍼로 교체.
- **Sitemap / robots.txt** 도 절대 URL 사용 → `absoluteUrl()` 적용.
- **이미지·CSS·JS 경로**: `<img src="/img/...">`, `<link href="/style.css">`, `<script src="/main.js">` 모두 동일 처리.

## 결론

- 자체 구현 정적 빌더라도 `BASE_PATH` 1 변수 + `url()`/`absoluteUrl()` 헬퍼 2개로 충분
- GitHub Actions 단계에서 repo 이름만 보면 두 배포 형태 자동 대응 가능
- fetch / RSS 절대 URL / 이미지 등 *모든* `/` 시작 경로를 헬퍼로 통일해야 누락 없음
- 처음부터 도입하면 후속 배포 옵션 (Cloudflare Pages 의 `pages.dev` 서브도메인, 사내 서버 서브경로) 도 마찰 없이 흡수
