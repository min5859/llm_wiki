---
title: "dev-blog — AI 보조 한국어 엔지니어링 뉴스레터 시스템"
domain: personal
sensitivity: public
tags: ["project", "static-site", "newsletter", "claude-cli", "kernel", "lkml", "github-pages", "cron", "node20"]
created: 2026-05-08
updated: 2026-05-10
sources:
  - "session-logs/20260507-225855-4c7c-현재-프로그램을-분석해-주세요.md"
  - "session-logs/20260510-070019-a130-#-Linux-Daily-Newsletter-Rewrite-당신은-리눅스-커널-개발자를-돕.md"
  - "session-logs/20260510-070200-f6f1-#-Android-Kernel-Daily-Briefing-당신은-Android-커널-플랫폼.md"
  - "session-logs/20260510-070412-fd9a-#-Open-Source-Trending-Daily-Briefing-당신은-오픈소스-트렌드.md"
confidence: high
related:
  - "wiki/bugs/utc-iso-date-kst-rollover.md"
  - "wiki/patterns/cron-nvm-node-path-trap.md"
  - "wiki/analyses/github-pages-base-path-pattern.md"
  - "wiki/patterns/launchd-secret-management.md"
  - "wiki/projects/kernel-digest.md"
---

# dev-blog — AI 보조 한국어 엔지니어링 뉴스레터

리눅스 커널을 1차 주제로 한 한국어 엔지니어링 일일 뉴스레터 / 정적 블로그. 다중 토픽 (Android, AI, 보안, 툴체인, 배포판) 으로 확장 전제. 의존성 0개 (Node 20+ 의 `fetch` / `fs/promises` / `http` 표준 API 만 사용) 로 빌더·RSS 생성·Atom 파서까지 자체 구현.

자매 프로젝트인 [[kernel-digest]] (계획 단계, M0 완료) 가 좀 더 본격적인 데이터 파이프라인 + AI Stage + Publisher 설계인 반면, 본 프로젝트는 그 전 단계의 가벼운 정적 사이트 + cron 형태로 먼저 가동된 사례.

## 위치 / 진입점

- 디렉터리: `/Users/wooki/project/git/wk/dev-blog`
- npm scripts:
  - `daily:linux` — 수집 → 드래프트 → 재작성 (publish 미포함, 검토용)
  - `daily:linux:publish` — 위 + publish 까지 자동 (cron 용, `PUBLISH_DAILY=1`)
  - `serve` — `public/` 정적 미리보기 (포트 4321)

## 데이터 흐름

```
data/raw/<topic>/        ← 원본 스냅샷 (gitignore)
data/normalized/<topic>/ ← 통일된 source-record 스키마
data/generated/<topic>/  ← candidates / draft / rewritten / prompt
content/topics/<topic>/  ← 정식 게시용 (검토 후 수동 승격, git 추적)
public/                  ← 빌드 산출물 (HTML, RSS, search-index)
```

## 5-단계 파이프라인

| 단계 | 스크립트 | 역할 |
|------|---------|------|
| 수집 | `scripts/collect-linux.mjs` | kernel.org `releases.json` + lore.kernel.org LKML Atom 피드 → `data/raw/` 저장, `data/normalized/source-records-latest.json` 생성 |
| 드래프트 | `scripts/draft-linux.mjs` | 정규화 레코드 점수화 (서브시스템 매칭, 소스 종류, 패치/PR/회귀 신호) → 후보 선별 → 메타데이터 기반 한국어 초안 |
| 재작성 | `scripts/ai-rewrite-linux.mjs` | 어댑터 경계 (`template`/`claude`). `claude` 어댑터는 `claude -p` 를 spawn 하여 stdin 으로 프롬프트 전달, JSON 파싱 |
| 게시 | `scripts/publish-linux.mjs` | 재작성 결과를 `content/topics/linux/posts/` 로 승격 (수동 승인 필요) |
| 빌드 | `scripts/build-site.mjs` | `content/` → `public/` 정적 HTML, RSS, 검색 인덱스, 태그 페이지 |
| 자동화 | `scripts/run-daily-linux.mjs` | 위 단계를 순차 실행, `logs/daily/` 에 로그 + `linux-latest-status.json` 에 단계별 시작/종료/exit code/stdout/stderr |

## 설계 강점 (재사용 가능 패턴)

1. **AI 어댑터 경계** — `runAiAdapter` 가 `template`/`claude` 만 노출, 나머지 파이프라인은 어댑터 형태에 무관. `template` fallback (`DAILY_REWRITE_ADAPTER=template`) 으로 claude CLI 장애 시에도 그날치 발행 살림.
2. **수동 승급 흐름** — 재작성 ↔ publish 분리로 자동 생성물의 무검토 게시 방지 (정공법, 1인용에서도 가치 있음).
3. **Multi-topic 전제** — `content/topics/<id>/`, `data/raw/<topic>/`, `sources.json` 구조가 처음부터 다중 토픽 가정. linux 외 추가 토픽 도입 시 디렉터리 + sources.json + draft 스크립트만 추가.
4. **장애 가시성** — 단계별 status JSON 으로 어디서 깨졌는지 한 눈에 확인 가능.
5. **의존성 0개** — Node 표준 API 만으로 정적 사이트 빌더 + RSS + Atom 파서 자체 구현. supply chain 공격 면적 최소.

## 운영 흐름 (cron + GitHub Pages)

```
[macOS cron] 매일 07:00 KST
   └─ npm run daily:linux:publish
        ├─ collect → draft → rewrite (claude -p) → publish
        └─ git push  ──→  [GitHub Actions: pages.yml]
                              └─ scripts/build-site.mjs
                                   └─ public/  →  GitHub Pages
```

- **cron 은 사용자 머신에서**: `claude -p` 가 사용자 구독 (Anthropic Pro / Max) 을 필요로 하므로 GitHub Actions 안에서는 못 씀
- **빌드만 GitHub Actions 에서**: cron 끝에 `git push` → Actions 가 정적 빌드 + Pages 배포
- 분리 이유는 *cron-on-laptop, deploy-via-actions* 패턴 — Claude 구독을 외부 CI 로 옮길 수 없는 모든 자동화 시스템의 표준 형태

## 알려진 함정 (이번 세션에서 발견·수정)

### 1. cron 의 NVM node PATH 문제

cron 등록은 됐지만 `env: node: No such file or directory` 로 즉시 실패. NVM 의 node 는 `~/.nvm/versions/node/v24.14.0/bin/` 에 있고 cron 은 `~/.zshrc` 를 안 읽기 때문. crontab 상단에 명시적 `PATH=` 라인 추가로 해결.

→ 일반 패턴: [[cron-nvm-node-path-trap]]

### 2. UTC 기준 ISO 날짜 → KST 새벽~오전에 어제 날짜로 떨어짐

`new Date().toISOString().slice(0, 10)` 가 UTC 기준이라 KST 08:15 = UTC 23:15 → "2026-05-07" 반환. 매일 07:00 KST cron 이 *어제* 게시본을 silent overwrite 하는 데이터 손실 시나리오. `Intl.DateTimeFormat('en-CA', { timeZone: 'Asia/Seoul' })` 로 4개 스크립트 (`run-daily-linux.mjs`, `draft-linux.mjs`, `ai-rewrite-linux.mjs`, `publish-linux.mjs`) 동시 수정.

→ 일반 패턴: [[utc-iso-date-kst-rollover]]

### 3. GitHub Pages — 사용자 페이지 vs 프로젝트 페이지의 base path

`<id>.github.io` 는 root 도메인 (`""`), 그 외 repo 는 서브경로 (`/<repo>`). 모든 내부 링크 + RSS 절대 URL + 클라이언트 사이드 fetch 가 두 케이스 모두 동작해야 함. `BASE_PATH` 환경변수 + 빌드 시 prefix 주입 헬퍼로 해결.

→ 일반 패턴: [[github-pages-base-path-pattern]]

## 관련 파일 / 디렉터리 (참고)

- `scripts/build-site.mjs` — 정적 빌더 (BASE_PATH 처리)
- `scripts/serve-site.mjs` — 로컬 미리보기 (path traversal 방어 포함)
- `docs/DEPLOYMENT.md` — 운영 흐름과 cron→push→Pages 자동 갱신 셋업 방법
- `.github/workflows/pages.yml` — GitHub Actions Pages 배포 워크플로
- `logs/daily/` — cron 출력 로그 + 단계별 status JSON

## 변경 이력

- 2026-05-08: 최초 작성. 사용자 머신 cron 에서 가동 중인 것을 사용자가 분석 요청. cron PATH 문제 + UTC/KST 날짜 버그 + GitHub Pages BASE_PATH 패턴 3건을 일반 페이지로 분리 (출처: session-logs/20260507-225855-4c7c-*)
- 2026-05-10: Multi-topic 전제가 실제로 정상 가동 중임을 확인 — Linux 외에 Android Kernel Daily Briefing 과 Open Source Trending Daily Briefing 의 2개 토픽이 매일 07:00 KST cron 으로 추가 발행 중. 토픽별 시스템 프롬프트는 각각 다른 큐레이션 정책을 정의: Linux 는 LKML maintainer/`fromMaintainer`/`maintainerComments` 메타로 머지 신호 추출 + ACK prefix 풀어쓰기, Android 는 `ANDROID:`/`FROMGIT:`/`FROMLIST:`/`BACKPORT:`/`UPSTREAM:` prefix 를 한국어로 풀고 GKI/ABI 영향에 가중, OSS Trending 은 HN frontpage hit 을 1순위 신호로 두고 별 100k+ long-tail giants 는 별도 카테고리로 격리. 공통 휴리스틱: 3-tier priority 분포 강제 (상 1~2 / 중 2 / 하 0~1), `implications`/`nextActions` 같은 LLM 의 자동 보충 섹션 금지, 데이터 부족 시 솔직한 fallback 표현. (출처: session-logs/20260510-070019-a130-* Linux, 20260510-070200-f6f1-* Android, 20260510-070412-fd9a-* OSS Trending)
