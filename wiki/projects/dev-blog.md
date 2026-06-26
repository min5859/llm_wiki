---
title: "dev-blog — AI 보조 한국어 엔지니어링 뉴스레터 시스템"
domain: personal
sensitivity: public
tags: ["project", "static-site", "newsletter", "claude-cli", "kernel", "lkml", "github-pages", "cron", "node20"]
created: 2026-05-08
updated: 2026-06-27
sources:
  - "session-logs/20260507-225855-4c7c-현재-프로그램을-분석해-주세요.md"
  - "session-logs/20260627-030014-eb14-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260626-030010-1f9e-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260625-030015-cfe3-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260624-030010-c83b-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260622-030008-1da5-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260611-031305-4894-#-Opensource-Trending-Research-Dossier-당신은-오픈소스-트렌.md"
  - "session-logs/20260510-070019-a130-#-Linux-Daily-Newsletter-Rewrite-당신은-리눅스-커널-개발자를-돕.md"
  - "session-logs/20260510-070200-f6f1-#-Android-Kernel-Daily-Briefing-당신은-Android-커널-플랫폼.md"
  - "session-logs/20260510-070412-fd9a-#-Open-Source-Trending-Daily-Briefing-당신은-오픈소스-트렌드.md"
  - "session-logs/20260511-230001-14d5-오늘-dev-blog-주제들이-5월11일-자로-업데이트-되지않았습니다.md"
  - "session-logs/20260512-093236-ee09-주제중에-오늘-5-12일-업데이트가-된것도-있고-안된것도-있습니다.-안된건-왜-안된건지-분.md"
  - "session-logs/20260513-074737-a32f-오늘날짜-포스팅이-안-보입니다.-오늘-동작-했는지-확인해-주세요.md"
  - "session-logs/20260514-080604-8120-자동-파이프라인-상태-2026-05-14-1개-토픽-실패---9개-성공.-linux-gpu.md"
  - "session-logs/20260516-002256-34e8-cursor-로-동작-시켜놓으니-10개-토픽중-하나씩-fail-이-발생-하는-듯합니다.-c.md"
  - "session-logs/20260516-120035-a415-dev-blog-프로젝트의-AI-provider-가-cursor-인가요--claude-인가.md"
  - "session-logs/20260517-071129-4e43-*.md"
  - "session-logs/20260517-071310-baf5-*.md"
  - "session-logs/20260517-071628-9f89-*.md"
  - "session-logs/20260517-071853-f4a9-*.md"
  - "session-logs/20260517-072103-9a09-*.md"
  - "session-logs/20260517-072334-54e2-*.md"
  - "session-logs/20260517-072621-5d31-*.md"
  - "session-logs/20260517-204826-4fc6-주간-다이제스트는-언제-수행되나요.md"
  - "session-logs/20260518-070009-ddac-#-Linux-Daily-Newsletter-Rewrite-당신은-리눅스-커널-개발자를-돕.md"
  - "session-logs/20260518-070202-63b9-#-Android-Kernel-Daily-Briefing-당신은-Android-커널-플랫폼.md"
  - "session-logs/20260518-070433-9bd5-#-Open-Source-Trending-Daily-Briefing-당신은-오픈소스-트렌드.md"
  - "session-logs/20260518-071049-b424-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.---오픈소스-큐레이션---파이.md"
  - "session-logs/20260518-071258-b7e7-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260518-071614-9260-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260518-071926-be69-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260518-072313-6c93-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260518-072522-3ef3-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260518-072906-691d-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260518-073052-9857-#-Linux-Kernel-Weekly-Digest-당신은-리눅스-커널-개발-주간-다이제스.md"
  - "session-logs/20260518-232056-c7c2-오늘은-실패한-포스팅이-많은데-원인이-뭔지-분석해-주세요.md"
  - "session-logs/20260519-070009-952a-#-Linux-Daily-Newsletter-Rewrite-당신은-리눅스-커널-개발자를-돕.md"
  - "session-logs/20260519-070235-f999-#-Android-Kernel-Daily-Briefing-당신은-Android-커널-플랫폼.md"
  - "session-logs/20260519-070820-89b9-#-Android-Kernel-Daily-Briefing-당신은-Android-커널-플랫폼.md"
  - "session-logs/20260519-071225-e4ab-#-Open-Source-Trending-Daily-Briefing-당신은-오픈소스-트렌드.md"
  - "session-logs/20260519-071530-0239-#-Open-Source-Trending-Daily-Briefing-당신은-오픈소스-트렌드.md"
  - "session-logs/20260519-072035-eb67-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.md"
  - "session-logs/20260519-072717-a881-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.md"
  - "session-logs/20260519-072829-a108-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-073334-ec00-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-073731-bb7c-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-074322-43c9-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-074723-c5d9-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-075328-9d97-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-075640-765e-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-080125-a0bd-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-080447-ed0d-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260519-080717-91ce-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-070014-f353-#-Linux-Daily-Newsletter-Rewrite-당신은-리눅스-커널-개발자를-돕.md"
  - "session-logs/20260520-070245-58b4-#-Open-Source-Trending-Daily-Briefing-당신은-오픈소스-트렌드.md"
  - "session-logs/20260520-070934-b568-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.---오픈소스-큐레이션---파이.md"
  - "session-logs/20260520-071312-d75c-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.---오픈소스-큐레이션---파이.md"
  - "session-logs/20260520-071446-0197-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-071656-1bad-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-072033-f8d7-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-072415-bbe1-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-072807-c3cf-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-073030-8829-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-073528-5706-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-073659-991c-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260520-074033-3915-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md"
  - "session-logs/20260525-031658-92f2-#-Linux-Kernel-Weekly-Digest-당신은-리눅스-커널-개발-주간-다이제스.md"
  - "session-logs/20260530-112824-bd1b-현재-프로젝트를-분석해-주세요.md"
  - "session-logs/20260606-151702-d243-지금-프로그램을-개선하고-싶은데-개선할만한-포인트를-찾아줘-관점은-블로그-내용의-질적인-향.md"
  - "session-logs/20260606-184227-1875-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
confidence: high
updated: 2026-06-06
related:
  - "wiki/bugs/utc-iso-date-kst-rollover.md"
  - "wiki/bugs/ndjson-stdout-parser-greedy-regex.md"
  - "wiki/bugs/highlights-action-validator-schema-drift.md"
  - "wiki/patterns/cron-nvm-node-path-trap.md"
  - "wiki/patterns/shell-set-eu-topic-isolation.md"
  - "wiki/patterns/prompt-schema-pipeline-coupling.md"
  - "wiki/patterns/llm-json-parse-retry-with-dump.md"
  - "wiki/analyses/github-pages-base-path-pattern.md"
  - "wiki/analyses/llm-content-quality-guards.md"
  - "wiki/analyses/llm-newsletter-rewrite-metadata-grounding.md"
  - "wiki/analyses/research-write-agent-separation.md"
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
[macOS launchd] 매일 07:00 KST  (~/Library/LaunchAgents/com.user.dev-blog.daily.plist)
   └─ scripts/daily-deploy.sh
        ├─ daily 10 토픽 (linux / android / opensource / lens 8 / opensource-curation)
        ├─ scripts/weekly-linux.mjs  (W## 폴더 게시. 별도 cron 없이 daily 흐름 안에서 실행)
        └─ git push  ──→  [GitHub Actions: pages.yml]
                              └─ scripts/build-site.mjs
                                   └─ public/  →  GitHub Pages
```

- **launchd 진입점은 단 하나**: `com.user.dev-blog.daily.plist` (`StartCalendarInterval: Hour=7, Minute=0`). 별도 weekly cron 없음 — 주간 다이제스트는 같은 daily 흐름 안에서 `weekly-linux.mjs` 가 호출
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

## research/write 에이전트 분리 (2026-06-06, commits da60cec~9f2ccad)

내용 *질적* 향상을 목표로 파이프라인을 `collect → draft → **research** → rewrite(write) → build` 로 확장. 기존 `rewrite` 한 단계가 "조사+판단+작문"을 한꺼번에 하던 것을 둘로 나누고, 두 에이전트를 **dossier**(`research-latest.json`) 계약으로 연결했다. 핵심은 단계 쪼개기가 아니라 **draft 의 결정론적 regex fetch 를 추론 기반 도구 조사(WebFetch/WebSearch/git log)로 격상**하는 것 — 그래야 collect 의 700자 excerpt 천장을 깬다.

- **Step 1** (`da60cec`) — dossier 스키마 + validator (14 테스트). 핵심 불변식: 본문에 들어갈 모든 claim 은 evidence URL 로 추적. `impactType` 은 `highlight-schema` enum 재사용.
- **Step 2** (`2e75ee3`) — `research-linux.mjs`. `AI_ADAPTER=claude` 면 read-only 도구로 능동 조사, `template`/`codex` 면 draft 결과만으로 schema-valid dossier 를 만들어 파이프라인이 끝까지 돈다(deterministic fallback). adapter export 시그니처 보존.
- **Step 3** (`8bebbd8`) — write 가 dossier 를 우선 입력으로 받고 grounding 근거도 dossier 로 넘김 → `quality-guard` 의 `findUngroundedUrls` 가 "dossier 밖 URL 금지"를 자동 강제(8 테스트). dossier 없으면 기존 draft 흐름으로 폴백.
- **Step 4** (`0543372`) — daily 파이프라인 배선 + PoC. 전체 ok, 테스트 103/103. write 가 dossier 만으로 작성해 URL 10/10 grounding.
- **보강** (`9f2ccad`) — claude 가 evidence.quote 를 200자 초과로 줄 때 `normalizeDossier` 로 절단, `RESEARCH_RAW_PATH` 로 직전 stdout 재호출 없이 재파싱(7분 조사 결과 복구).

**실측 품질 리프트**: claude 도구 조사 dossier 가 collect 에 없던 LWN 기사 5건·CVE 2건 등 13개 evidence 를 모음(template 기계 dossier 는 record URL 8건뿐). 기존 파이프라인엔 0건이던 2차 소스가 최종 글 출처에 들어감. 단, **fallback/codex 경로만으로는 입력 깊이가 그대로라 품질은 거의 불변** — 리프트는 `npm run research:linux:claude` 도구 경로에서만 발생(이 점이 "배관 완료 ≠ 품질 완료"의 핵심). 일반 패턴은 [[research-write-agent-separation]] 로 분리.

## 관련 파일 / 디렉터리 (참고)

- `scripts/build-site.mjs` — 정적 빌더 (BASE_PATH 처리)
- `scripts/serve-site.mjs` — 로컬 미리보기 (path traversal 방어 포함)
- `docs/DEPLOYMENT.md` — 운영 흐름과 cron→push→Pages 자동 갱신 셋업 방법
- `.github/workflows/pages.yml` — GitHub Actions Pages 배포 워크플로
- `logs/daily/` — cron 출력 로그 + 단계별 status JSON

## 콘텐츠 품질 가드 4종 (2026-05-12 추가, commit 2a4b2ec)

5/11 12개 토픽 게시본을 사용자 관점에서 정독한 결과 도출된 4가지 결함 패턴을 파이프라인 레벨에서 가드. 일반 사상은 [[llm-content-quality-guards]] 로 분리.

| # | 결함 | 가드 | 변경 위치 |
|---|------|------|----------|
| 1 | **토픽 간 release 트리오 중복** — mainline/stable/longterm 3종이 linux-daily / kernel-security / distro-stable 의 highlight 에 동시 등장 | pipeline.json 의 `highlightReleaseMonikers` 로 토픽별 opt-in. distro-stable 만 `stable/longterm` opt-in 유지. lens prompt 에 "draft 에 highlight 로 들어 있지 않은 release 추가 금지" 명시 | `scripts/draft-lore-lens.mjs`, `prompts/linux-lens-newsletter-ko.md`, `content/topics/linux-distro-stable/pipeline.json` |
| 2 | **상위 포스트의 action 이 일반적** — "changelog 로 확인하세요" 등 클릭 외 부가 정보 없음 | prompt 에 (1) 조건절 (2) 구체 검증 단서 모두 강제. 부정 예시 ("changelog 확인하세요") 명시 금지 | `prompts/linux-newsletter-ko.md`, `prompts/linux-lens-newsletter-ko.md` |
| 3 | **opensource 토픽 hallucination** — description 만 보고 설정 키·플래그 발명 | (3-1) README excerpt fetcher 신규 (HEAD/main/master fallback, timeout 8s, 동시성 3) 로 후보의 README 첫 ~700자를 `candidateBodies.readmeExcerpt` 로 그라운딩. (3-2) prompt 에 "candidateBodies 에 없는 키 발명 금지" 룰 + 강제 disclaimer. (3-3) publish 단계에서 disclaimer 누락 시 안전망으로 confidence.note 강제 주입 | `scripts/lib/github-readme.mjs` (신규), `scripts/draft-opensource.mjs`, `scripts/draft-opensource-curation.mjs`, `scripts/publish-opensource.mjs`, `scripts/publish-opensource-curation.mjs`, `prompts/opensource-*-newsletter-ko.md` |
| 4 | **저신호일 단일 항목 부풀리기** — 1건만 수집된 렌즈를 그럴듯하게 늘려 적음 | draft 단계에서 lens-specific 신호량 계산 → `signalLevel` (`high/medium/low`) + `lensSignalCount` 를 draftMetadata 에 노출. `low` 면 summary 첫 문장을 "오늘은 이 렌즈에서 신호가 적은 날입니다." 로 prefix. lens prompt 에 "signalLevel=low 면 단일 항목 부풀리기 금지" 명시 | `scripts/draft-lore-lens.mjs`, `prompts/linux-lens-newsletter-ko.md` |

회귀 테스트 49/49 통과. dry-run 으로 4 lens 모두 highlight 에서 release 가 빠지고 lens-specific 항목으로 채워졌고, linux-perf-rt 는 `signalLevel=low` 로 자동 prefix.

## daily-deploy.sh 의 자동화 토픽 누락 (2026-05-12 발견·수정, commit 의 후속)

5/11 의 NDJSON 사고 수정 시점에는 lens 8종 + opensource-curation 도 동일하게 12개 토픽 모두 수동 재실행해 채웠지만, **launchd 자동 실행 진입점인 `scripts/daily-deploy.sh` 는 `linux` / `android` / `opensource` 3개만 호출**하는 구조였다. 즉 매일 launchd 7시 cron 으로는 3개만 게시되고, 나머지 9개 (lens 8 + opensource-curation) 는 사용자가 수동으로 돌린 날에만 게시되어 왔다 (5/11 의 `854b74a content: 2026-05-11 일일 토픽 12종 게시` 가 그 흔적).

5/12 의 발견: `linux` / `android` / `opensource` 는 5/12 자로 업데이트 됐는데 9개가 누락. 원인 파악 후 두 단계로 진행:

1. **5/12 누락 콘텐츠 수동 채움** — `run-all-kernel-lenses` + `run-daily-opensource-curation` 으로 9개 토픽 5/12 분 생성·게시. `run-all-kernel-lenses` 가 첫 실패 (linux-ebpf — cursor 어댑터의 일시 출력 파싱 실패) 에서 멈추는 부작용을 발견 → per-topic 루프 + 1회 재시도 셸 스니펫으로 5개 남은 lens 를 마저 진행 (모두 성공)
2. **`daily-deploy.sh` 구조 개선** — 9개 토픽을 자동 실행 대상에 추가. 토픽별 `if !` 실패 격리 패턴 ([[shell-set-eu-topic-isolation]]) 유지하면서 `run-all-kernel-lenses` 대신 lens 8종을 per-topic 으로 호출 — 한 lens 실패가 다음 lens 를 막지 않도록.

**일반 교훈** — multi-topic 파이프라인에서 「수집 → 드래프트 → 재작성 → publish」 의 토픽별 진입점과 launchd / cron 의 호출 목록은 **별도 코드 경로**라 한쪽만 추가하면 silent 누락이 발생한다. 토픽 추가 시 ① 디렉터리 + sources.json + draft 스크립트 ② daily-deploy.sh 의 자동 실행 목록 둘 다 수정해야 한다 (체크리스트 형태로 docs 에 명시 권장).

**`run-all-kernel-lenses` 의 stop-on-first-fail 함정** — 토픽 격리가 daily-deploy.sh 한 단계 안에서 끝나지 않고, `run-all-kernel-lenses` 자체도 같은 함정을 갖고 있었음. lens 8 종을 자동화에 끼울 때 이 스크립트를 그대로 호출했다면 첫 실패한 lens 이후가 다시 누락됐을 것 — daily-deploy.sh 에 per-topic 루프로 풀어 쓴 이유.

## 알려진 함정 — NDJSON 출력 파서 깨짐 (2026-05-11)

cursor 어댑터 (`scripts/lib/ai-rewrite-adapter.mjs`) 가 cursor CLI 의 4가지 출력 모양 (단일 JSON / NDJSON / envelope.result 안 잡음 / fenced JSON) 중 NDJSON·envelope 케이스에서 깨졌고, `daily-deploy.sh` 의 `set -eu` 로 한 토픽 실패가 전체 중단을 일으켜 5/11 자 12개 토픽이 누락. fix 두 갈래:

1. **파서 견고화** (`scripts/lib/ai-rewrite-adapter.mjs`, commit 2cc5ff5): 단일 JSON · NDJSON · envelope.result · fenced JSON 4 경로를 단계적 폴백으로 모두 흡수. 회귀 테스트 2건 추가
2. **토픽 격리** (`scripts/daily-deploy.sh`): linux 단계를 `if !` 로 감싸 한 토픽 실패가 다른 토픽 push 를 막지 않도록

→ 일반 패턴: [[ndjson-stdout-parser-greedy-regex]], [[shell-set-eu-topic-isolation]]

## 알려진 함정 — highlights[].action 스키마 표류 (2026-05-13)

5/13 launchd 잡은 정상 실행됐지만 10개 토픽 모두 publish 단계에서 동일 에러:

```
Error: highlights[0].action required
```

직전 커밋 `223ac17 feat(prompts): … action 3분해 …` 가 프롬프트의 highlight 출력 스키마를 `action` 단일 필드 → `if` / `do` / `verify` 3필드로 분해했지만, **publisher 5종과 opensource rewrite 2종의 validator 가 여전히 `action` 만 강제**해 모든 게시가 첫 highlight 에서 즉시 throw. 결과적으로 `git push` 가 게시본 없이 도는 silent skip 으로 사이트에 오늘자 글이 통째로 사라짐.

`build-site.mjs` 와 `ai-rewrite-linux.mjs` 의 일부 메서드는 이미 두 스키마를 모두 받도록 진화해 있었지만, publisher / weekly validator / 일부 rewrite validator 가 옛 스키마에 고정돼 한 PR 안에서 일관되게 진화하지 못한 케이스 — 스키마 변경의 추적성 부재.

**수정** (commit 후속, 8 files +64/-14):
- `publish-linux.mjs` / `publish-android.mjs` / `publish-lore-lens.mjs` / `publish-opensource.mjs` / `publish-opensource-curation.mjs`: validator 에 "`action` 또는 (`if`+`do`+`verify` 셋 다)" 둘 중 하나 강제 (build-site 의 hasAction/hasIfDoVerify 와 동형)
- `ai-rewrite-opensource.mjs` / `ai-rewrite-opensource-curation.mjs`: 동일 패턴
- `weekly-linux.mjs`: `ensureHighlight` 가 구조화 필드 (`if`/`do`/`verify`) 를 보존하면서 폴백 시에만 action 합성

회귀 테스트 49/49 통과 후 8개 토픽은 기존 rewrite 산출물에 publish 만 직접 호출 (`NEWSLETTER_DATE=2026-05-13 node scripts/publish-*.mjs`), opensource / opensource-curation 2종은 rewrite validator 까지 막혀 있었으므로 `PUBLISH_DAILY=1 NEWSLETTER_DATE=2026-05-13 node scripts/run-daily-*.mjs` 로 rewrite 부터 재실행. 10개 토픽 5/13 분 모두 복구.

**git push 단계의 보안 분류기 차단** — `daily-deploy.sh` 가 평소 main 에 직접 push 하는 흐름이지만 자동 분류기가 인가를 인식하지 못해 차단. 사용자가 `! git push origin main` 으로 직접 실행하거나 `~/.claude/settings.json` 의 Bash permission rule 에 패턴 추가가 필요. 자동화 cron 의 push 가 평소엔 통과하는데 수동 세션에서만 막히는 비대칭은 별도 관찰사항.

→ 일반 패턴: [[prompt-schema-pipeline-coupling]] / 버그 페이지: [[highlights-action-validator-schema-drift]]

## 알려진 함정 — rewrite 에 한자 두 글자 혼입 → quality-guard 정상 차단 (2026-05-14)

5/14 launchd 잡은 10개 토픽 중 9개 성공, 1개 (`linux-gpu-ai`) 가 rewrite 단계에서 실패. status JSON 의 `steps: ['collect:True', 'draft:True', 'rewrite:False']` 로 어느 단계에서 깨졌는지 한 눈에 확인 가능 (단계별 status 메타의 가시성 가치 — 5/8 의 설계 강점 4번 항목이 실제로 진단을 빠르게 해줬다).

원인은 `scripts/ai-rewrite-lore-lens.mjs` 가 호출하는 LLM rewrite 결과의 stdout (`data/generated/linux-gpu-ai/rewrite-stdout-latest.txt`) 에 한자 두 글자 "明文" (한국어 "명문" 의 일부가 한자로 출력됨) 이 혼입. `scripts/lib/quality-guard.mjs` 의 `auditPostQuality` 가 한국어 강제 가드로서 비-한글 CJK 문자를 검출해 차단 (= 의도된 동작, 5/12 의 4 가드와는 별개의 5번째 가드).

**복구 절차** (4단계, ~3분):

1. **stdout 직접 교정** — `data/generated/linux-gpu-ai/rewrite-stdout-{latest,2026-05-14}.txt` 의 한자 두 글자만 한글로 교정 (`Edit` 도구). LLM 을 다시 호출하지 않고 작은 부분만 수동 수정해 비용·재현성 모두 절약
2. **rewritten JSON 재생성** — `/tmp/build-rewritten-linux-gpu-ai.mjs` 일회용 스크립트로 교정된 stdout 에서 rewritten JSON 을 재빌드. `ai-rewrite-lore-lens.mjs` 의 정상 흐름은 LLM 호출이 포함되므로 그 부분만 우회
3. **publish 만 재실행** — `NEWSLETTER_DATE=2026-05-14 node scripts/publish-lore-lens.mjs linux-gpu-ai`. `markPublishOk` (5/12 추가된 헬퍼) 가 status JSON 을 in-place 갱신해 `ok=true`, `manualRepublishedAt` 마커 + `outputs.post` 경로 채움. cron-only 흐름과 수동 복구 흐름이 status 갱신을 공유하도록 설계된 게 살아남
4. **빌드 + commit** — `npm run build` (10 topics, 63 posts) → `git add content/topics/linux-gpu-ai/posts/2026-05-14-linux-gpu-ai-daily.json logs/daily/linux-gpu-ai-latest-status.json` → commit `1f9db82`

**일반 교훈**:

- **부분 실패 격리가 작동** — 5/11 사고에서 도입한 토픽 `if !` 격리 ([[shell-set-eu-topic-isolation]]) 가 이번엔 실효성을 보였다. 1개 토픽이 실패해도 나머지 9개가 평시 게시. 격리 패턴이 빛을 발하는 첫 운영 사례
- **status JSON 의 단계별 가시성이 진단 시간을 줄임** — `steps: ['collect:True', 'draft:True', 'rewrite:False']` 한 줄로 LLM 출력 검사가 시작점임을 즉시 알 수 있음. log 텍스트 grep 없이 status JSON 만으로 분류 가능
- **quality-guard 의 정상 차단을 사고로 분류하지 말 것** — quality-guard 가 막은 것은 *결함 있는 콘텐츠의 게시* 를 막은 *의도된 동작*. 5/11 의 NDJSON 파서 깨짐·5/13 의 스키마 표류 와 달리 이번엔 코드 변경 없이 데이터만 교정하면 된다. 「가드 작동 = 코드 수정」 으로 오인해 가드를 완화하면 진짜 결함이 게시되는 회귀
- **CJK 비한국어 혼입은 한국어 강제 프롬프트의 만성 함정** — 한국어 출력 강제 프롬프트라도 LLM 은 가끔 한자/일본어 가나/중국어 간체를 섞는다. 정독으로 잡기는 너무 후행이고 사전 가드 (`auditPostQuality` 의 CJK 비한글 검출) 가 비용 대비 효과 큼. 일반 가드 패턴으로 분리 → [[llm-content-quality-guards]] 5번째 가드 항목

→ 일반 패턴: [[llm-content-quality-guards]] (5번째 가드 신설), [[shell-set-eu-topic-isolation]] (격리 패턴의 첫 운영 성공 사례)

## run-daily 파이프라인 공통화 리팩토링 (2026-05-30)

### collect-utils.mjs 공통 유틸 추출 (commit `refactor: readJson 공통화`)

4개 `collect-*.mjs` 스크립트에 중복 정의되어 있던 `readJson` 함수를 `scripts/lib/collect-utils.mjs` 로 추출. 4개 파일에서 인라인 정의 제거 후 import 로 교체.

`daily-deploy.sh` 의 커밋 메시지도 `"Linux briefing"` → `"briefing"` 으로 수정 (11개 토픽 모두 포함하는 메시지로).

### run-daily-pipeline.mjs 공통 로직 추출 (commit `refactor: run-daily-*.mjs 공통 로직 추출`)

6개 `run-daily-*.mjs` 스크립트에서 중복되던 `runStep` / `renderLog` / `main` / `catch` 보일러플레이트를 `scripts/lib/run-daily-pipeline.mjs` 의 `runPipeline()` 한 함수로 통합.

**결과**: 6개 스크립트 합산 ~700줄 → ~150줄 (각 파일 25~35줄의 설정 오브젝트만 남음).

**설계 포인트**:
- steps 포맷 통일: `[name, command, args, extraEnv?]`
- `lore-lens` 의 per-step extraEnv 지원 포함
- `linux` 의 `outputs.extraStatus` 지원 포함
- `opensource-curation` 의 upstream steps 패턴 유지

검증: `run-daily-linux.mjs` 실행 → 정상 완료 + `linux-latest-status.json` 구조 확인. `linux-kernel-security` lens, `opensource-curation` 도 동일 확인.

**일반 교훈**: 토픽 N 개가 공통 파이프라인 구조를 공유할 때, 단계 목록만 다른 설정 오브젝트로 분리하면 N 파일 × (공통 로직)을 1 파일로 줄일 수 있다 — 단 `lore-lens` 처럼 per-step 특수 처리가 있는 경우 이를 추상화 안에 수용해야 한다.

## 기본 AI 어댑터 cursor → claude 일괄 전환 (2026-05-16)

`cursor` 어댑터로 10개 토픽을 돌리던 중 매일 한두 토픽씩 산발적으로 실패하는 패턴이 관찰됨 (5/11 NDJSON 사고처럼 파서 깨짐이 아닌, cursor CLI 자체의 일시 실패). 사용자 결정으로 기본 어댑터를 `cursor` → `claude` 로 일괄 전환.

격리 worktree (`.claude/worktrees/claude-adapter-default`) 에서 진행. 14개 파일 +21/-19:

| 위치 | 변경 |
|------|------|
| `scripts/lib/ai-rewrite-adapter.mjs` | `normalizeDailyRewriteAdapter` 빈 입력 기본값 `cursor` → `claude`. `cursor-agent` 별칭은 그대로 `cursor` 로 매핑 유지 |
| `scripts/ai-rewrite-{linux,android,opensource,opensource-curation}.mjs` | `resolveAiAdapter('cursor')` → `resolveAiAdapter('claude')` |
| `scripts/weekly-linux.mjs` | 동일 |
| `scripts/ai-rewrite-lore-lens.mjs` | `LENS_DEFAULT_ADAPTER = 'cursor'` → `'claude'` (lens 8종 일괄) |
| `scripts/run-daily-{linux,android,opensource,opensource-curation,lore-lens}.mjs` | `rewriteScriptMap` fallback `rewrite:<topic>:cursor` → `rewrite:<topic>:claude` |
| `scripts/run-daily-lore-lens.mjs` | `AI_ADAPTER` 분기에서 default `'cursor'` → `'claude'` (template / claude / cursor 3분기 유지) |
| `scripts/ai-rewrite-adapter.test.mjs` | 빈 문자열 / undefined 입력이 `claude` 를 반환하도록 단언 갱신 + `cursor-agent` 별칭 회귀 유지 |
| `docs/SCHEDULING.md` | 기본값 문구 "uses the Cursor adapter by default" → "uses the Claude adapter by default", 환경변수 설명 갱신 |

회귀 테스트 66/66 통과 (`node --test scripts/ai-rewrite-adapter.test.mjs` 6 cases + `npm test` 전체 60).

**환경변수 분기는 그대로 유지**:
- `AI_ADAPTER=cursor|claude|template` per-invocation override 유지
- `DAILY_REWRITE_ADAPTER=cursor` 로 cron 에서 cursor 강제 가능
- `cursor-agent` 별칭도 그대로 cursor 로 정규화

**일반 교훈**:

- **`resolveAiAdapter` 의 첫 인자가 default 라는 점**이 이번 일괄 변경의 단일 진입점이 됐다. 어댑터 결정 로직을 한 함수에 응집해 둔 게 multi-script 환경에서의 정합 변경을 5분 작업으로 만들었다 (어댑터 분기를 각 스크립트가 if/else 로 가졌다면 14개 파일 모두 다른 분기 패턴이라 누락 위험 높음). LLM 어댑터처럼 N 곳에서 호출되는 결정 로직은 default 도 첫 인자에 위치시켜 두는 게 미래의 일괄 전환 비용을 낮춤.
- **lens 의 LENS_DEFAULT_ADAPTER 상수처럼 모듈 상수로 default 를 빼둔 패턴**도 같은 가치 — 한 줄 변경으로 lens 8종 일괄 영향.
- **테스트의 빈 입력 단언**이 default 변경의 첫 가드 — `normalizeDailyRewriteAdapter defaults empty to claude and maps cursor-agent to cursor` 한 테스트 케이스가 의도와 실 코드의 일치를 회귀 방지.


- 2026-05-10: Multi-topic 전제가 실제로 정상 가동 중임을 확인 — Linux 외에 Android Kernel Daily Briefing 과 Open Source Trending Daily Briefing 의 2개 토픽이 매일 07:00 KST cron 으로 추가 발행 중. 토픽별 시스템 프롬프트는 각각 다른 큐레이션 정책을 정의: Linux 는 LKML maintainer/`fromMaintainer`/`maintainerComments` 메타로 머지 신호 추출 + ACK prefix 풀어쓰기, Android 는 `ANDROID:`/`FROMGIT:`/`FROMLIST:`/`BACKPORT:`/`UPSTREAM:` prefix 를 한국어로 풀고 GKI/ABI 영향에 가중, OSS Trending 은 HN frontpage hit 을 1순위 신호로 두고 별 100k+ long-tail giants 는 별도 카테고리로 격리. 공통 휴리스틱: 3-tier priority 분포 강제 (상 1~2 / 중 2 / 하 0~1), `implications`/`nextActions` 같은 LLM 의 자동 보충 섹션 금지, 데이터 부족 시 솔직한 fallback 표현. (출처: session-logs/20260510-070019-a130-* Linux, 20260510-070200-f6f1-* Android, 20260510-070412-fd9a-* OSS Trending)
- 2026-05-12: 5/11 일일 파이프라인 사고와 콘텐츠 품질 회고 — (1) cursor 어댑터 NDJSON 파싱 깨짐 + `daily-deploy.sh` `set -eu` 연쇄 중단으로 12개 토픽 누락 사고 발생, 파서 4 경로 폴백 + 토픽 `if !` 격리로 수정 (commit 2cc5ff5). (2) 12개 게시본 정독에서 발견된 4 결함 (토픽 중복 / action 일반성 / opensource hallucination / 저신호일 부풀리기) 을 파이프라인 가드로 보강 (commit 2a4b2ec, 11 files +208/-8). README excerpt fetcher 신규 도입으로 OSS 토픽 hallucination 그라운딩, `signalLevel` 메타 노출. 일반 패턴은 [[ndjson-stdout-parser-greedy-regex]] / [[shell-set-eu-topic-isolation]] / [[llm-content-quality-guards]] 로 분리 (출처: session-logs/20260511-230001-14d5-*)
- 2026-05-12 (2nd batch): `daily-deploy.sh` 자동화 누락 발견·수정. launchd 진입점이 linux/android/opensource 3개만 호출하고 있어 lens 8종 + opensource-curation 은 매일 silent 누락 (5/11 의 12개 토픽 게시는 수동 실행 결과였다). 9개 토픽 5/12 분을 수동 재생성 후 daily-deploy.sh 에 per-topic 루프 + `if !` 격리로 추가. 추가로 `run-all-kernel-lenses` 자체도 첫 lens 실패에서 멈추는 함정 발견 (per-topic 우회로 회피). 「토픽 진입점 vs 자동화 호출 목록」 분리에서 비롯되는 silent 누락 패턴은 일반 교훈으로 기록 (출처: session-logs/20260512-093236-ee09-*)
- 2026-05-13: `highlights[].action` → `if`/`do`/`verify` 3분해 프롬프트 변경이 publisher / weekly / 일부 rewrite validator 갱신과 비동기로 진행돼 5/13 launchd 잡의 10개 토픽 publish 가 모두 실패. validator 5+2+1 군데를 `action` 또는 (`if`+`do`+`verify`) 둘 중 하나 허용으로 완화 (build-site 와 동형). 49/49 테스트 통과 후 8토픽은 publish 직접 호출, 2토픽 (opensource / opensource-curation) 은 rewrite 부터 재실행으로 복구. 일반 교훈은 [[prompt-schema-pipeline-coupling]], 버그 상세는 [[highlights-action-validator-schema-drift]] 로 분리 (출처: session-logs/20260513-074737-a32f-*)
- 2026-05-16: 기본 AI 어댑터 `cursor` → `claude` 일괄 전환. cursor 가동 시 매일 한두 토픽씩 산발 실패하는 패턴이 트리거. 격리 worktree 에서 14개 파일 +21/-19 (`resolveAiAdapter('cursor')` → `'claude'`, `LENS_DEFAULT_ADAPTER` 상수, `normalizeDailyRewriteAdapter` 기본값, `rewriteScriptMap` fallback, docs). 회귀 테스트 66/66 통과. `AI_ADAPTER` / `DAILY_REWRITE_ADAPTER` 환경변수 override 와 `cursor-agent` 별칭은 그대로 유지. **`resolveAiAdapter` 의 첫 인자를 default 로 설계해 둔 응집이 14곳 일괄 변경을 5분 작업으로 만든 사례** (출처: session-logs/20260516-002256-34e8-*)
- 2026-05-14: 10개 토픽 중 1개 (`linux-gpu-ai`) 만 rewrite 실패, 나머지 9개 평시 게시 — 5/11 도입한 토픽 `if !` 격리의 첫 운영 성공 사례. 원인은 LLM rewrite stdout 에 한자 "明文" 두 글자 혼입 → `quality-guard.mjs` 의 `auditPostQuality` 가 정상 차단. 복구 4단계 (stdout 한자 두 글자만 한글 교정 → 일회용 스크립트로 rewritten JSON 재빌드 → publish 만 재실행 → build + commit `1f9db82`). `markPublishOk` 가 status JSON 을 in-place 갱신해 `ok=true` + `manualRepublishedAt` 마커. **교훈** — quality-guard 의 정상 차단을 「가드 완화」로 오대응 하지 말 것 (회귀 위험), CJK 비한국어 혼입은 한국어 강제 프롬프트의 만성 함정으로 사전 가드가 정독 회고보다 비용 효과적. 일반 패턴은 [[llm-content-quality-guards]] 5번째 가드로 분리 (출처: session-logs/20260514-080604-8120-*)
- 2026-05-16 (2nd batch): 「매일 1개씩 fail」 구조의 후속 진단 — 5/16 의 android 토픽은 `collect` 단계에서 gitiles `429 Too Many Requests` (rate-limit), 5/15 의 linux-toolchain 은 `rewrite` 단계에서 `highlights[0].priority required` (cursor 어댑터가 5 개 highlight 중 첫 highlight 의 priority 필드 누락). 매번 *다른* 토픽이 *다른* 단계에서 깨지는 이유는 9 토픽 × 외부 호출 (gitiles / lore.kernel.org / AI agent) 의존이라 토픽당 일시 실패 확률이 작아도 9 토픽 곱으로 매일 1개가 우연히 걸리는 구조. 근본 완화 후보는 ① `scripts/collect-*.mjs` 에 429 / 네트워크 오류에 대한 지수 backoff 재시도 ② `scripts/ai-rewrite-*.mjs` 의 schema validation 실패 시 1회 retry 또는 priority 등 필수 필드 누락 시 기본값 채움 — 둘 중 (a) 가 우선순위 (publish 단계에서 토픽 통째 skip 의 빈도가 더 높음). 미구현 (제안만 기록). (출처: session-logs/20260516-120035-a415-*)
- 2026-05-17: 07:00 cron 잡 7건 전체 silent fail — 오픈소스 큐레이션 브리핑 1건 (`opensource-curation`) + Linux specialist list lens rewrite 6건 (각각 다른 렌즈 토픽) 이 모두 `assistant_turns: 0`. claude CLI 가 prompt 까지 받았으나 모델 호출이 무응답으로 끝나 산출물 0. 같은 날 08:00 research-wiki 의 논문 분석 2건 (Qwen-Image-2.0 / AnyFlow) 과 09:00 oss-radar 의 OSS 분석 5건도 일제히 동일 패턴. 동일 호스트 (wookiui-Macmini) 의 시간대별 cron 잡이 모두 무응답이라 토픽·렌즈·레포 단의 결함이 아니라 시스템 단 (claude CLI 모델 백엔드 / 네트워크 / OAuth 등) 원인으로 추정. 5/16 도 일부 (5건) 가 동일하게 silent fail 했었음 → 2일 연속 광범위 발생. **운영 관찰만 기록, 코드 변경 없음**. 산출물·post 게시 0. 광범위 silent fail 의 진단 신호: 「동일 시간대 모든 잡이 `assistant_turns: 0`」. 단발 silent fail (특정 토픽만) 과 구분되어야 함 (출처: session-logs/20260517-{071129,071310,071628,071853,072103,072334,072621}-*)
- 2026-05-17 (운영 위치 확인): 「주간 다이제스트는 언제 수행되나」 질의 — 별도 weekly cron 이 없고, `~/Library/LaunchAgents/com.user.dev-blog.daily.plist` 가 매일 07:00 (`StartCalendarInterval: Hour=7, Minute=0`) 로 trigger 되는 단일 진입점에서 `scripts/daily-deploy.sh` 가 `scripts/weekly-linux.mjs` 를 같이 호출. content/topics/linux/posts/`<YYYY>-W<NN>-linux-weekly.json` 은 daily 흐름 안에서 매일 생성되며, 주 단위 변동은 ISO 주 (Y-Wnn) 가 바뀌는 첫 날에 새 파일이 만들어지는 구조. **운영 사실 정리만, 코드 변경 없음** (출처: session-logs/20260517-204826-4fc6-*)
- 2026-05-18: Linux Daily Newsletter Rewrite 프롬프트의 진화된 룰을 일반 분석으로 분리 — `candidateBodies` 종류별 처리 (LKML commit message vs kernel.org 백포트 목록), `history.previousVersion` vs `previouslySeenAt` 차별 (v2→v3 갱신 vs X일부터 추적), `fromMaintainer` 메인테이너 직접 발신 단서 자연스러운 삽입, `maintainerComments` excerpt 의 톤 3분류 (반대/보류·환영/머지·모호) 본문 한 줄 반영. 5/10 의 첫 도입 대비 시리즈 진척·발신자 권한·응답 톤을 직접 끌어쓰는 그라운딩 룰로 발전. 일반 패턴은 [[llm-newsletter-rewrite-metadata-grounding]] 로 분리. **코드 변경 없음** (프롬프트 본문 inspection 기반, 출처: session-logs/20260518-070009-ddac-*)
- 2026-05-18 (Android / OSS): 같은 launchd 사이클에서 Android Kernel Daily Briefing 과 Open Source Trending Daily Briefing 프롬프트도 발사. Android 는 ACK prefix (`ANDROID:`/`FROMGIT:`/`FROMLIST:`/`BACKPORT:`/`UPSTREAM:`) 한국어 풀어쓰기 룰 + ACK 브랜치 진행 (`android-mainline`, `android16-6.12`) 한 줄 추적이 토픽-specific 그라운딩. OSS Trending 은 「2026-05 도입 신규 출력 규칙」 추가: title 형식 `{date} {핵심사건} — 오픈소스 트렌드` (12자 한국어 요약, 약어·식별자 금지), 80자 한 문장 `headline` 필드 (필수, summary 앞), action 단일 필드 → `if`(30자) / `do`(50자) / `verify`(60자) 3분해. hallucination 가드는 OSS 의 설정 키·환경변수·CLI 플래그·파일경로·함수명·npm/PyPI 패키지명·엔드포인트·버전 번호 절대 금지로 구체화, `confidence.note` 에 「GitHub 메타·HN 신호·짧은 README 발췌」 출처 disclaimer 강제 주입. 일반 패턴은 [[llm-newsletter-rewrite-metadata-grounding]] 의 토픽 일반화 표·룰 5 변형·룰 7a 로 흡수. **코드 변경 없음** (출처: session-logs/20260518-070202-63b9-*, session-logs/20260518-070433-9bd5-*)
- 2026-05-18 (Lens 4 + Weekly Digest): 같은 launchd 사이클의 후속 발사 — Linux specialist list lens prompt 4건 (07:19/07:23/07:25/07:29) 은 직전 22:00 ingest 의 2건 (07:12/07:16) 과 동일 prompt body (lens 토픽만 다른 sample template) 로 신규 룰 없이 sources 만 갱신. 한편 같은 daily 흐름 안에서 호출되는 Linux Kernel Weekly Digest prompt (07:30, `weekly-linux.mjs` 진입점) 본문은 일일과 별개의 「주간 압축」 룰을 정의 — 같은 흐름 묶기 (한 시리즈가 여러 날 등장 시 "월: v2 → 수: 피드백 → 금: v3 보류" 식 진행 흐름 한 줄), 3~5개 흐름만 (28개 일일 후보의 평탄화 금지), 국부 드라이버 제외 (일일 정책 그대로), dailies 배열 재포함 금지 (멱등 + 토큰 절약). 「주간 = 일일의 압축, 합집합 아님」 이 본질. 일반 패턴은 [[llm-newsletter-rewrite-metadata-grounding]] 의 「주간 다이제스트 변형」 섹션 (룰 W1~W4) 으로 흡수. **코드 변경 없음** (출처: session-logs/20260518-071926-be69-*, 072313-6c93-*, 072522-3ef3-*, 072906-691d-*, 073052-9857-*)
- 2026-05-18 (cron 정상 회복): 5/17 의 광범위 silent fail (07:00 dev-blog 7건 + 08:00 research-wiki 2건 + 09:00 oss-radar 5건 모두 `assistant_turns: 0`) 의 후속 — 5/18 같은 호스트의 동시간대 cron 잡은 모두 prompt body 가 정상적으로 발사·기록됐고 (`assistant_turns` 일부는 0~1 분포지만 prompt 자체는 도달), 광범위 silent fail 패턴은 사라짐. 시스템 단 원인 (claude CLI 모델 백엔드 / 네트워크 / OAuth) 이 1일 만에 자동 해소된 것으로 보임. **운영 관찰만, 코드 변경 없음**
- 2026-05-18 (rewrite 어댑터 견고화, commit `a42d470`): 5/18 launchd 잡 10개 토픽 중 4개 (android, linux-arch-platform, linux-distro-stable, linux-gpu-ai) 가 `parseNewsletterJsonFromAiOutput()` (`scripts/lib/ai-rewrite-adapter.mjs:142`) 에서 동일하게 `Error: AI response did not contain JSON` 으로 죽음. collect·draft 는 모두 `code: 0` 정상 종료, `claude -p` 어댑터 호출 단계에서만 파싱 실패. 동일 `ai-rewrite-lore-lens.mjs` 를 쓰는 6개 lens 중 3개는 성공·3개는 실패라 **코드 버그가 아니라 어댑터의 확률적 실패** (모델이 일부 입력에 대해 JSON 외 텍스트를 섞어 반환). 8 files +157/-38 수정 — (1) `runAiAdapterAndParse(prompt, { defaultAdapter, logLabel, maxAttempts=2, failureDir, runner })` 신설: 어댑터 호출 + 파싱을 묶고 파싱 실패 시 `logs/ai-rewrite-failures/<ts>-<label>-attemptN.txt` 에 raw 텍스트 덤프 (헤더에 label/attempt/timestamp/error 메타) + stderr 경고 + 1회 재시도, 최종 실패는 원래 에러 throw. `AI_REWRITE_FAILURE_DIR` 환경변수 override. (2) newsletter rewrite 호출부 6곳 (opensource / linux / android / opensource-curation / lore-lens / weekly-linux) 일괄 치환. (3) 회귀 테스트 3건 (재시도 성공 / 끝까지 실패 / template null 경로) 추가, 10/10 그린. `parseNewsletterJsonFromAiOutput` / `runAiAdapterPrompt` 는 그대로 export 유지 (하위호환). 일반 패턴은 [[llm-json-parse-retry-with-dump]] 로 분리 (출처: session-logs/20260518-232056-c7c2-*)
- 2026-05-19 (Linux Daily Newsletter Rewrite 프롬프트): 07:00 cron 의 정기 발사. session-log 본문은 system prompt 만 (assistant 응답 없음, cron 호출 자체로 prompt 도달만 기록). 신규 룰 없음 (5/18 의 [[llm-newsletter-rewrite-metadata-grounding]] 룰 동일). **운영 흔적, 코드 변경 없음** (출처: session-logs/20260519-070009-952a-*)
- 2026-05-20 (07:00 cron 정상 사이클, 13건): Linux Daily Newsletter Rewrite 1건 (07:00, `assistant_turns: 1` 로 「파일이 이미 작성·검수 완료된 상태」 로그 — daily-deploy 의 멱등성 확인), Open Source Trending Daily Briefing 1건 (07:02), 오픈소스 큐레이션 브리핑 2건 (07:09 / 07:13, 07:09 는 `assistant_turns: 1` 로 「브리핑 JSON 작성을 시작합니다」 응답만 기록 — 산출물은 stdout 파일로 직접 출력됨), Linux specialist list lens 9건 (07:14~07:40, 5/19 와 동일 sample template — 보안·도구체인·RT/eBPF·GPU 등 lens 토픽만 변경, `assistant_turns` 0~1 분포). 신규 룰 없음, 모두 5/18 의 [[llm-newsletter-rewrite-metadata-grounding]] 룰 적용. 운영 관찰 — 5/19·20 연속 정상 발사 사이클로 5/17 광범위 silent fail 의 시스템 단 원인은 완전 해소. **코드 변경 없음** (출처: session-logs/20260520-{070014, 070245, 070934, 071312, 071446, 071656, 072033, 072415, 072807, 073030, 073528, 073659, 074033}-*)
- 2026-05-19 (07:00 cron 정상 사이클, 17건): 5/19 launchd 사이클 후속 — Android Kernel Daily Briefing 2건 (07:02 / 07:08), Open Source Trending Daily Briefing 2건 (07:12 / 07:15), 오픈소스 큐레이션 브리핑 2건 (07:20 / 07:27), Linux specialist list lens prompt 11건 (07:28~08:07, lens 토픽만 다른 동일 sample template — 보안·도구체인·RT/eBPF·GPU 등). 일부는 `assistant_turns: 1` 로 실제 파일 생성됨 (07:28 `linux-kernel-security` 보안 렌즈는 FPIN u8 카운터 wrap → DoS 보안 패치를 한 단어 사건으로 잡아 4 highlights · 80자 headline 가드 통과까지 4회 재편집해 완성, 07:37 / 07:47 / 07:56 / 08:01 등도 1턴 응답). **신규 룰 없음**, 모두 5/18 의 [[llm-newsletter-rewrite-metadata-grounding]] 룰 (W1~W4 주간 변형 / 5 변형 — action 3분해 / 7a — title·headline) 그대로 적용. 운영 관찰: 5/17 의 광범위 silent fail (시스템 단 원인 의심) 후 5/18·19 모두 prompt 발사 정상 — 시스템 단 결함은 자동 해소된 상태 유지. **코드 변경 없음** (출처: session-logs/20260519-{070235-f999, 070820-89b9, 071225-e4ab, 071530-0239, 072035-eb67, 072717-a881, 072829-a108, 073334-ec00, 073731-bb7c, 074322-43c9, 074723-c5d9, 075328-9d97, 075640-765e, 080125-a0bd, 080447-ed0d, 080717-91ce}-*)
- 2026-05-25 (03:16 Weekly Digest 자동 생성): Linux Kernel Weekly Digest 주간 압축 실행 — 5/19~5/23 일일 브리핑 2건 (2026-05-21: mm/vmalloc·iommu/vt-d 시리즈, 2026-05-23: Linux 7.0.10 stable 대량 백포트) 을 입력으로 주간 다이제스트 JSON 생성. draftMetadata 의 `rewriteAdapter: "cursor"` (cursor 어댑터가 여전히 운영 중), `generator: "scripts/draft-linux.mjs"`, `promptTemplate: "prompts/linux-newsletter-ko.md"`. Linux 7.1-rc5 mainline / 7.0.10 stable / 5.10.257 longterm 3개 릴리스 트래킹 정상. **운영 관찰, 코드 변경 없음** (출처: session-logs/20260525-031658-92f2-*)
- 2026-05-30: `scripts/lib/collect-utils.mjs` 신규 — 4개 collect 스크립트의 `readJson` 중복 추출. `scripts/lib/run-daily-pipeline.mjs` 신규 — 6개 `run-daily-*.mjs` 의 `runStep`/`renderLog`/`main` 보일러플레이트를 `runPipeline()` 한 함수로 통합 (합산 ~700줄→~150줄). `daily-deploy.sh` 커밋 메시지 `"Linux briefing"` → `"briefing"` 수정. linux/lore-lens/opensource-curation 3토픽 실제 실행 검증 완료 (출처: session-logs/20260530-112824-bd1b-*)
- 2026-06-06: research/write 에이전트 분리 PoC 를 Step 1~4 + 보강까지 끝까지 구현(commits `da60cec`/`2e75ee3`/`8bebbd8`/`0543372`/`9f2ccad`, bkit PDCA 단계별 커밋). 파이프라인을 `collect→draft→research→rewrite→build` 로 확장하고 dossier(`research-latest.json`) 계약으로 두 에이전트 연결 — 모든 claim 에 evidence URL 강제 → `findUngroundedUrls` 가 dossier 밖 URL 자동 차단. `template`/`codex` 는 결정론적 fallback dossier, `claude` 는 도구 조사. 실측: claude 도구 조사가 LWN 5건·CVE 2건 등 13 evidence 수집(기존 2차 소스 0건 → 최종 글 출처 진입)으로 700자 천장 돌파. 함정: 7분 조사 결과가 quote 232자(>200자)로 저장 직전 막혀 `normalizeDossier`+`RESEARCH_RAW_PATH` 복구, `git.kernel.org` Anubis 봇 차단 → WebSearch 우회. "배관 완료 ≠ 품질 완료"(fallback 만으론 입력 깊이 불변). 일반 패턴은 [[research-write-agent-separation]] 로 분리 (출처: session-logs/20260606-151702-d243-*, 20260606-184227-1875-*)
- 2026-06-10 (research/write 분리의 cron 운영 통합 + write 단계 광범위 silent fail): 6/6 PoC 였던 research(dossier)→write(newsletter) 2단계 분리가 **실제 cron 사이클로 통합**돼 03:00~04:06 KST 에 토픽별로 발사됨 — Research Dossier 10건 + Newsletter Write 10건이 각각 별도 잡으로 짝지어 실행 (Linux Daily / Android Kernel / Opensource Trending / Opensource Curation / AI Coding Agents 각 1쌍 + Linux Kernel Lens 6쌍 [linux-toolchain·linux-arch-platform·linux-gpu-ai 등 렌즈 토픽별]). **운영 관찰 3가지**: (1) Newsletter Write 10건이 *전부* `assistant_turns: 0` — research dossier 는 1턴 동작했는데 write 단계만 무응답으로, 5/17 류 silent fail 이 write 단계에 국한돼 재발 (dossier 입력은 프롬프트에 정상 주입됨). (2) Anubis 봇 차단이 6/6 의 `git.kernel.org` 단발에서 **`lore.kernel.org` 전반으로 확대** — 거의 모든 lens dossier 가 1차 WebFetch 검증 실패, 게다가 발행일이 미래 날짜(2026-06)라 `Fixes:` 해시의 WebSearch 교차검증도 인덱스에 없어 이중 실패. 우회로 GitHub torvalds 미러(`linux-gpu-ai` v3d 시리즈)·Phoronix(7.1-rc7) 교차확인은 일부 성공, 나머지는 입력 후보의 `commitMessage`(cover letter 본문)를 1차 근거로 쓰되 전 항목 `confidence: medium` + `openQuestions` 로 미확인 명시. (3) 산출물은 사실상 `linux-arch-platform` dossier 1건(RISC-V/Exynos 5 entry)만 완성, 나머지는 미완성. 휘발성 커널 패치 + 미검증(medium) 이라 durable wiki 사실로는 **전량 스킵**, 운영 관찰만 기록. **코드 변경 없음** (출처: session-logs/20260610-03{0014,0301,0506,0748,0928,1257,2316,2418,2727,3051,3422,3824,4225,4501,4802,5133,5444,5716}-*, 040029/040319/040657-*)
- 2026-06-11 (03:00 cron 사이클 23건 — 6/10 패턴 이틀째 지속): research(dossier)→write(newsletter) 2단계 분리 cron 이 03:00~04:12 KST 에 발사 (Research Dossier 11건 + Newsletter Write 12건, 테마: Linux Daily / Android Kernel / Opensource Trending / Opensource Curation / AI Coding Agents 각 1쌍 + Linux Kernel Lens 6쌍). **운영 관찰 3가지**: (1) **Newsletter Write 12건이 또 전부 `assistant_turns: 0`** — 6/10 의 write 단계 silent fail 이 이틀 연속 지속(고착), dossier 입력은 정상 주입되나 write 모델 호출만 무응답. (2) Research Dossier 도 `opensource-curation`·`ai-coding-agents` 2건은 `assistant_turns: 0` 무응답, 나머지(Linux Daily/Android/Opensource Trending/Linux Kernel Lens 6렌즈)는 1턴 동작. (3) **신규 도구 신뢰성 관찰** — Opensource Trending dossier 에서 에이전트가 "WebFetch 가 GitHub 404 페이지에서 환각했을 가능성" 을 스스로 인지하고 신규 repo 교차검증을 시도. WebFetch 가 에러 페이지도 정상처럼 변환해 미실재 repo 를 환각할 위험은 6/10 의 `lore.kernel.org` Anubis 봇차단과는 다른 축의 도구 신뢰성 함정 — 일반 패턴은 [[research-write-agent-separation]] 구현 함정에 추가. 휘발성 커널 패치·미검증 dossier·무응답 write 산출물은 **전량 durable 스킵**(6/10 과 동일 기준). **코드 변경 없음** (출처: session-logs/20260611-03* 03:00 사이클 23건)
- 2026-06-18 (03:00 cron 사이클 20건 — write 단계 silent fail 일주일 이상 고착): research(dossier)→write(newsletter) 2단계 분리 cron 이 03:00~04:10 KST 에 발사 (Linux Daily/Android Kernel/Opensource Trending·Curation/AI Coding Agents 각 1쌍 + Linux Kernel Lens 6쌍). **운영 관찰**: (1) Newsletter Write 단계 대부분이 여전히 `assistant_turns: 0` — 6/10·6/11 의 write 단계 silent fail 이 **6/18 에도 지속·확대**(dossier=research 단계는 정상 1턴, write 단계만 0 인 비대칭이 일주일 이상 미해결). (2) Anubis 봇 차단을 **`curl -A "git/2.39.0"` 비-브라우저 UA 로 우회**해 `lore.kernel.org/<thread>/raw` mbox 취득 성공 — 일반 기법은 [[research-write-agent-separation]] 구현 함정에 추가. 휘발성 커널·OSS 콘텐츠는 durable 전량 스킵. **코드 변경 없음** (출처: session-logs/20260618-03* 03:00~04:10 사이클 20건)
- 2026-06-20 (03:00 cron 사이클 21건 — write silent fail 약 2주 고착 + 봇차단 폴백 사다리 완성형 관측): research(dossier)→write(newsletter) 2단계 cron 이 03:00~04:00 KST 에 발사 (Linux Daily/Android Kernel/Opensource Trending·Curation/AI Coding Agents 각 1쌍 + Linux Kernel Lens 6쌍 — 단, AI Coding Agents 는 dossier 만). **운영 관찰**: (1) Newsletter Write 10건이 **여전히 전부 `assistant_turns: 0`** — 6/10 이래 write 단계 silent fail 이 약 2주째 미해결(dossier 단계는 1턴 정상인 비대칭 지속). (2) **봇 차단이 `lore.kernel.org`+`git.kernel.org` 동시 차단**으로 악화된 날 — dossier 에이전트가 raw 엔드포인트→`mail-archive.com` 미러→후보 `commitMessage`+WebSearch 교차검증→미확인은 `confidence` 강등+`openQuestions` 격리(추측 금지)의 **단계적 폴백 사다리**로 흡수, 또한 `grep -rl <sourceId> content/topics/*/sources.json` 로 **topic-id 자가판별** 후 조사 시작 — 두 일반 기법은 [[research-write-agent-separation]] 구현 함정에 추가. AI Coding Agents dossier 가 수집한 CC v2.1.183 auto 모드 안전 가드는 본인 도구 운영 지식이라 [[claude-code-auto-mode-safety-guardrails]] 로 분리. 휘발성 커널·OSS 뉴스 콘텐츠(kCFI KUnit 논쟁 등)는 durable 전량 스킵. **코드 변경 없음** (출처: session-logs/20260620-03* 03:00~04:00 사이클 21건)
- 2026-06-22 (03:00 cron 사이클 24건 — write silent fail 약 3주째 + 간헐적 1턴 회복 관측): research(dossier)→write(newsletter) 2단계 cron 이 03:00~04:18 KST 에 발사 (Research Dossier 11건 + Newsletter Write 12건 + Weekly Digest 1건; 테마: Linux Daily/Android Kernel/Opensource Trending/Opensource Curation/AI Coding Agents 각 1쌍 + Linux Kernel Lens 6쌍 [linux-toolchain·linux-distro-stable·linux-perf-rt·linux-arch-platform·linux-gpu-ai 등]). **운영 관찰**: (1) **Newsletter Write 12건 중 10건이 여전히 `assistant_turns: 0`** — 6/10 이래 write 단계 silent fail 이 약 3주째 미해결. 단 이번엔 Opensource Trending(66e0)·Linux Kernel Lens(10ff) **2건이 1턴 산출**해 완전 무응답에서 *간헐적 부분 회복*으로 변화(원인 미상, 비결정적). (2) Research Dossier 도 비대칭 지속 — 9건은 1턴 정상, opensource-curation(0270)·Linux Kernel Lens(4ca9) 2건은 `assistant_turns: 0`. (3) Weekly Digest(041814) 는 1턴 동작했으나 grounding 메타(`verifyLink` 플레이스홀더 등)가 깨진 채 산출 — 주간 압축 단계의 메타 스키마 표류 신호. (4) Anubis 봇 차단(`lore.kernel.org`)은 이번에도 지속, dossier 들은 6/18·6/20 의 폴백 사다리(raw 엔드포인트·미러·commitMessage+WebSearch 교차검증·미확인 confidence 강등)로 흡수. 휘발성 커널/OSS 뉴스(UFS 에러핸들러 PM 경쟁 수정연쇄·Rust DRM ioctl lifetime·eBPF verifier scalar 정밀도·BT ISO/UDF 잠금 버그·dma_fence 콜백 동기화 등)는 **단일 패치를 범용 패턴으로 일반화 = 과잉추출**이라 6/14~6/21 결정과 동일하게 durable 전량 스킵(출처 lore 차단으로 미검증 confidence medium/low). 파이프라인 메타패턴은 이미 [[research-write-agent-separation]]/[[llm-content-quality-guards]] 등에 수록. **코드 변경 없음** (출처: session-logs/20260622-03* 03:00~04:18 사이클 24건)
- 2026-06-24 (03:00 cron 사이클 21건 — write silent fail 약 2주 반째 + 6/22 의 간헐 회복 비지속): research(dossier)→write(newsletter) 2단계 cron 이 03:00~04:13 KST 에 발사 (Research Dossier 11건 + Newsletter Write 10건; 테마: Linux Daily/Android Kernel/Opensource Trending/Opensource Curation 각 1쌍 + AI Coding Agents 1[dossier만] + Linux Kernel Lens 6쌍). **운영 관찰**: (1) **Newsletter Write 10건이 다시 전부 `assistant_turns: 0`** — 6/22 에 잠깐 보였던 2건 1턴 산출(간헐 부분 회복)이 **이번엔 비지속**, 6/10 이래 write 단계 silent fail 이 약 2주 반째 미해결인 비결정적 고착으로 복귀. (2) Research Dossier 는 9건 1턴 정상, AI Coding Agents(c3c9)·Android Kernel(dcd2) 2건이 `assistant_turns: 0` 무응답 — research/write 비대칭 지속. (3) Anubis 봇 차단(`lore.kernel.org`)은 이번에도 지속, Linux Daily dossier(c83b)는 `WebFetch 차단→WebSearch 교차검증`으로 af_alg allowlist sysctl·Linux 7.1.1 stable·arm64 TLBI errata(CVE-2025-10263) 3건을 high confidence 로 흡수(6/18·6/20 폴백 사다리 동일). 1건(d67e Kernel Lens)은 `bash_commands_logged: 2` 로 curl/grep 폴백 사용. (4) AI Coding Agents dossier 는 산출 0턴이나 입력 후보(Copilot CLI TUI GA·Claude Code v2.1.186 changelog·소스리크·"복잡 작업 불가" 이슈·OpenClaw 과금 차단·Cowork·Codex CLI)는 이미 [[claude-code-source-leak-internals]]/[[anthropic-oauth-third-party-billing-trap]]/[[ai-coding-agent-cost-and-context-patterns]] 에 기수록(재탕). 휘발성 커널/OSS 뉴스는 **단일 패치를 범용 패턴으로 일반화 = 과잉추출**이라 6/14~6/22 결정과 동일하게 durable 전량 스킵. **코드 변경 없음** (출처: session-logs/20260624-03* 03:00~04:13 사이클 21건)
- 2026-06-26 (03:00 cron 사이클 23건 — write silent fail 약 2주 반째 완전 무응답 지속): research(dossier)→write(newsletter) 2단계 cron 이 03:00~04:12 KST 에 발사 (Research Dossier 11건 + Newsletter Write 12건; 테마: Linux Daily/Android Kernel/Opensource Trending/Opensource Curation/AI Coding Agents 각 1쌍 + Linux Kernel Lens 6쌍, Opensource Trending 만 write 2건). **운영 관찰**: (1) **Newsletter Write 12건이 전부 `assistant_turns: 0`** — 6/24·6/25 와 동일하게 write 단계 완전 무응답, 6/10 이래 약 2주 반째 비결정적 고착 지속(6/22 의 간헐 1턴 회복은 계속 재현 안 됨). (2) Research Dossier 는 8건 1턴 정상, Android Kernel(0305)·Opensource Curation(0329)·Linux Kernel Lens(0405) 3건이 `assistant_turns: 0` 무응답 — research/write 비대칭 지속(6/25 와 동일 8/11 비율). (3) AI Coding Agents dossier(033326)는 이번엔 1턴 산출했으나 휘발성 외부 뉴스 — 이미 [[claude-code-source-leak-internals]]/[[anthropic-oauth-third-party-billing-trap]]/[[ai-coding-agent-cost-and-context-patterns]] 에 수록된 주제의 재탕. (4) Anubis 봇 차단(`lore.kernel.org`) 폴백 사다리(raw 엔드포인트·미러·commitMessage+WebSearch 교차검증·미확인 confidence 강등)는 6/18~6/25 와 동일하게 흡수, bash 폴백 로그에 에러·실패 흔적 없음. 휘발성 커널/OSS·CC 릴리스 속보는 **단일 패치/릴리스를 범용 패턴으로 일반화 = 과잉추출**이라 6/14~6/25 결정과 동일하게 durable 전량 스킵. 파이프라인 메타패턴은 이미 [[research-write-agent-separation]]/[[llm-newsletter-rewrite-metadata-grounding]]/[[llm-content-quality-guards]]/[[ai-coding-agent-cost-and-context-patterns]] 에 수록. **코드 변경 없음** (출처: session-logs/20260626-03* 03:00~04:12 사이클 23건)
- 2026-06-25 (03:00 cron 사이클 22건 — write silent fail 약 2주 반째 완전 무응답 지속): research(dossier)→write(newsletter) 2단계 cron 이 03:00~04:17 KST 에 발사 (Research Dossier 11건 + Newsletter Write 11건; 테마: Linux Daily/Android Kernel/Opensource Trending/Opensource Curation/AI Coding Agents 각 1쌍 + Linux Kernel Lens 6쌍). **운영 관찰**: (1) **Newsletter Write 11건이 전부 `assistant_turns: 0`** — 6/24 와 동일하게 write 단계가 완전 무응답, 6/10 이래 약 2주 반째 비결정적 고착 지속(6/22 의 간헐 1턴 회복은 재현 안 됨). (2) Research Dossier 는 8건 1턴 정상, opensource-curation(0431 아님—b9a6)·Linux Kernel Lens 2건(a8a3·79ff) 도합 3건이 `assistant_turns: 0` 무응답 — research/write 비대칭 지속. (3) Linux Kernel Lens dossier 4건(9f56·cc9f·2664·6f2c)은 `bash_commands_logged≥1` 로 로컬 `git log` 조회 폴백 사용(에러·실패 없음). (4) AI Coding Agents dossier(d87c)는 1턴 산출했으나 내용은 휘발성 외부 뉴스 — Claude Code v2.1.187(`sandbox.credentials` 자격증명 차단·조직 모델 제한·MCP 5분 행 타임아웃 수정)·Copilot Free/Student auto 모델 선택 전환 2건만 채택, 소스리크·third-party 과금 차단 등 나머지는 stale 로 droppedCandidates 처리(이미 [[claude-code-source-leak-internals]]/[[anthropic-oauth-third-party-billing-trap]] 에 기수록). 휘발성 커널/OSS·CC 릴리스 속보는 **단일 패치/릴리스를 범용 패턴으로 일반화 = 과잉추출**이라 6/14~6/24 결정과 동일하게 durable 전량 스킵. 파이프라인 메타패턴은 이미 [[research-write-agent-separation]]/[[llm-newsletter-rewrite-metadata-grounding]]/[[llm-content-quality-guards]]/[[ai-coding-agent-cost-and-context-patterns]] 에 수록. **코드 변경 없음** (출처: session-logs/20260625-03* 03:00~04:17 사이클 22건)
- 2026-06-27 (03:00 cron 사이클 23건 — write silent fail 약 2주 반째 완전 무응답 지속): research(dossier)→write(newsletter) 2단계 cron 이 03:00~04:16 KST 에 발사 (Research Dossier 11건 + Newsletter Write 12건; 테마: Linux Daily/Android Kernel/Opensource Trending/Opensource Curation/AI Coding Agents 각 1쌍 + Linux Kernel Lens 6쌍, Android Kernel 만 write 2건). **운영 관찰**: (1) **Newsletter Write 12건이 전부 `assistant_turns: 0`** — 6/24·6/25·6/26 와 동일하게 write 단계 완전 무응답, 6/10 이래 약 2주 반째 비결정적 고착 지속(6/22 의 간헐 1턴 회복은 계속 재현 안 됨). (2) Research Dossier 는 10건 1턴 정상, opensource-curation(d988) 1건만 `assistant_turns: 0` 무응답 — 6/25·6/26(8/11)보다 dossier 측 회복(10/11), research/write 비대칭 지속. (3) Linux Kernel Lens dossier 2건(040055·040640)은 `bash_commands_logged≥2` 로 로컬 `git log`·grep·curl 폴백 사용(에러·실패 없음). (4) AI Coding Agents dossier(033304)는 1턴 산출했으나 휘발성 외부 뉴스(MAI-Code-1-Flash GA·GitHub Desktop 3.6 worktree·Copilot code review 비용 20% 절감·Cowork·CC 소스리크·redact-thinking) — 이미 [[claude-code-source-leak-internals]]/[[anthropic-oauth-third-party-billing-trap]]/[[ai-coding-agent-cost-and-context-patterns]]/[[everything-claude-code]] 에 수록된 주제의 재탕. (5) Anubis 봇 차단(`lore.kernel.org` 및 raw mbox 동시 차단, 040640 에서 "Lore is fully Anubis-blocked" 확인)은 6/18~6/26 와 동일하게 폴백 사다리(입력 후보의 base64 `commitMessage` 디코드→WebSearch 교차검증→미확인 confidence 강등+openQuestions 격리)로 흡수, bash 폴백 로그에 에러·실패 흔적 없음. 휘발성 커널/OSS·CC 릴리스 속보는 **단일 패치/릴리스를 범용 패턴으로 일반화 = 과잉추출**이라 6/14~6/26 결정과 동일하게 durable 전량 스킵. 파이프라인 메타패턴은 이미 [[research-write-agent-separation]]/[[llm-newsletter-rewrite-metadata-grounding]]/[[llm-content-quality-guards]]/[[ai-coding-agent-cost-and-context-patterns]] 에 수록. **코드 변경 없음** (출처: session-logs/20260627-03* 03:00~04:16 사이클 23건)
