---
title: "dev-blog — AI 보조 한국어 엔지니어링 뉴스레터 시스템"
domain: personal
sensitivity: public
tags: ["project", "static-site", "newsletter", "claude-cli", "kernel", "lkml", "github-pages", "cron", "node20"]
created: 2026-05-08
updated: 2026-05-14
sources:
  - "session-logs/20260507-225855-4c7c-현재-프로그램을-분석해-주세요.md"
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
confidence: high
updated: 2026-05-17
related:
  - "wiki/bugs/utc-iso-date-kst-rollover.md"
  - "wiki/bugs/ndjson-stdout-parser-greedy-regex.md"
  - "wiki/bugs/highlights-action-validator-schema-drift.md"
  - "wiki/patterns/cron-nvm-node-path-trap.md"
  - "wiki/patterns/shell-set-eu-topic-isolation.md"
  - "wiki/patterns/prompt-schema-pipeline-coupling.md"
  - "wiki/analyses/github-pages-base-path-pattern.md"
  - "wiki/analyses/llm-content-quality-guards.md"
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
