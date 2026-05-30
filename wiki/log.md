---
title: Operation Log
updated: 2026-05-31T00:00:00+09:00
---

# Operation Log

## 2026-05-31T00:00 — wiki-ingest (session-logs, ingested: false 1건)

처리 session-log 1건. 신규 페이지 0건, 기존 페이지 갱신 2건.

- **1건 분포**:
  - 20260530-203624-b2b6 ht_trading — 시각 가드 09:30 원복 + trailing stop activation 4%→3%, distance 전 구간 2%p 축소

- **갱신 페이지 2건**:
  - `wiki/projects/ht-trading.md` — 시각 가드 섹션 신설, trailing_tiers 파라미터 튜닝 이력 표 추가, 설계 변경 이력 2행 추가, sources 추가
  - `wiki/index.md` — ht-trading 요약 갱신 (시각 가드, trailing_tiers 현재 설정 반영)

- raw-sources/ 신규 .md 없음, `.cache/extracted/` 없음, fetched/ 없음, type: mcp-note 0건
- Marked ingested: true — 1개 session-log 파일

## 2026-05-30T18:00 — wiki-ingest (session-logs, ingested: false 3건)

처리 session-log 3건. 신규 페이지 1건, 기존 페이지 갱신 5건.

- **3건 분포**:
  - 10:38 disk_monitor — 이번 주 디스크 모니터링 분석. 5/29 -4.77G 급감 원인(wide/src-tauri 빌드 아티팩트), 개발 도구 경로 8개 추가 (~26G 사각지대 해소). `wiki/projects/disk-monitor.md` 갱신
  - 11:02 ht_trading — KIS API 서킷브레이커 구현, 추가매수 재개 조건 변경, n_stock_info V3 리버트+선택적 재적용, screener min_score 62 복원, 거래대금 순위 추가. `wiki/projects/ht-trading.md` 대규모 갱신, `wiki/analyses/scoring-version-comparison-methodology.md` 갱신
  - 11:28 dev-blog — `lib/run-daily-pipeline.mjs` 공통화(~700줄→~150줄), `lib/collect-utils.mjs` 추출. `wiki/projects/dev-blog.md` 갱신

- **신규 페이지 1건** (Phase I — 범용 지식):
  - `wiki/analyses/api-circuit-breaker-trading-pattern.md` — API 서킷브레이커 패턴 (ht_trading KIS 구현에서 일반화)

- **갱신 페이지 5건**:
  - `wiki/projects/disk-monitor.md` — 5/30 운영 회고 + 경로 확장 추가
  - `wiki/projects/ht-trading.md` — min_score 복원, 서킷브레이커, 반등 조건, V3 리버트, 거래대금 순위
  - `wiki/projects/dev-blog.md` — run-daily-pipeline.mjs 리팩토링 섹션 추가
  - `wiki/analyses/scoring-version-comparison-methodology.md` — V3 리버트 결정 섹션 추가
  - `wiki/index.md` — 프로젝트 요약 3건 갱신, 분석 페이지 2건 추가

- raw-sources/ 신규 .md 없음, `.cache/extracted/` 없음, fetched/ 없음, type: mcp-note 0건
- Marked ingested: true — 3개 session-log 파일 전체

## 2026-05-24T10:00 — wiki-ingest (session-logs, ingested: false 1건)

처리 0건 (의미 있는 신규 산출 없음). 신규 페이지 0건, 기존 페이지 갱신 0건. raw-sources/ 신규 .md 없음 (서브디렉터리 articles/ books/ ideas/ papers/ transcripts/ 비어 있음), `.cache/extracted/` 디렉터리 없음, `raw-sources/<subdir>/fetched/` 없음, `type: mcp-note` 인 session-log 0건.

- **1건 분포**:
  - 22:59 kakao-db 1건 — "Command Line Tools for Xcode 26.5" 가 무엇인지, 업데이트해야 하는지에 대한 단순 Q&A. 설계 판단·버그 수정·패턴·기술 선택에 해당하지 않으므로 wiki 페이지 생성 스킵
- raw-sources/ 신규 .md 없음 확인, `.cache/extracted/` 없음, fetched/ 없음, type: mcp-note 0건
- Marked ingested: true — 1개 session-log 파일

## 2026-05-23T19:00 — wiki-ingest (session-logs, ingested: false 2건)

처리 0건 (의미 있는 신규 산출 없음). 신규 페이지 0건, 기존 페이지 갱신 0건. raw-sources/ 신규 .md 없음 (서브디렉터리 articles/ books/ ideas/ papers/ transcripts/ 비어 있음), `.cache/extracted/` 디렉터리 없음, `raw-sources/<subdir>/fetched/` 없음, `type: mcp-note` 인 session-log 0건.

- **2건 분포**:
  - 18:50 research-wiki 1건 — Claude/Codex/Cursor Agent CLI의 non-interactive 모드 비교 탐색 (`--help` 출력 확인). 계획 파일 작성으로 종료, 구현 없음. 기존 [[cursor-agent-cli-overview]] / [[multi-llm-provider-adapter-pattern]] 에 CLI 비교 지식 이미 수록. 탐색적 시행착오로 스킵
  - 18:57 research-wiki 1건 (`assistant_turns: 0`) — "Reply with just OK" 테스트 프롬프트. 산출물 없음
- raw-sources/ 신규 .md 없음 확인, `.cache/extracted/` 없음, fetched/ 없음, type: mcp-note 0건
- Marked ingested: true — 2개 session-log 파일 전체

## 2026-05-23T08:00 — wiki-ingest (session-logs, ingested: false 23건)

처리 0건 신규 페이지 (의미 있는 산출물 모두 이전 사이클에서 흡수 완료), 운영 관찰 4건 기록. 신규 페이지 0건, 기존 페이지 갱신 1건 (`projects/oss-radar`). raw-sources/ 신규 .md 없음 (서브디렉터리 articles/ books/ ideas/ papers/ transcripts/ 비어 있음, Tips/ 8 PDF 모두 기존 wiki/patterns/ 페이지에 이미 매핑), `.cache/extracted/` 디렉터리 없음 (PDF chunk 추출 미실행), `raw-sources/<subdir>/fetched/` 없음, `type: mcp-note` 인 session-log 0건.

- **23건 분포**:
  - 22:30–23:16 hermes/openclaw "응답 없음" 3건 (`23:07:45` openclaw / `23:08:19` hermes / `23:15:50` dev-blog 정지 요청) — 모두 `assistant_turns: 0` (사용자가 입력만 던지고 종료). 의미 있는 산출물 없음. 단 같은 이슈를 다음 0:00:54 hermes 480c 세션에서 자세히 분석함
  - 23:42 disk-monitor 분석 1건 (`assistant_turns: 10`) — **이미 [[disk-monitor]] 의 2026-05-22 운영 회고 + [[disk-monitor-blind-spot-coverage]] 패턴으로 흡수 완료** (이전 사이클에서 처리). 사각지대 17G 추적·`du` timeout fallback·npm/uv 캐시 회수 9.4G 모두 페이지에 정리되어 있음
  - 00:00 hermes 480c 1건 (`assistant_turns: 1`) — **이미 [[hermes]] 의 2026-05-23 운영 회고 + [[long-lived-network-client-stuck-reconnect-loop]] 패턴으로 흡수 완료**. Telegram reconnect loop 진단 (망 정상이지만 gateway 프로세스 재연결 루프 누적, `hermes gateway restart` 1차 조치)
  - 08:00–08:01 research-wiki 3건 (5/22) + 3건 (5/23) — alive 핑 + 논문 분석 (5/22: arXiv 2605.11609 / 2605.19833, 5/23: arXiv 2605.22355 / 2605.22109) 모두 `assistant_turns: 0` (silent fail). 산출물 없음
  - 09:00–09:03 oss-radar 6건 (5/22) + 6건 (5/23) — alive 핑 + OSS 레포 분석 (5/22: Snailclimb/JavaGuide, trimstray/the-book-of-secret-knowledge, ChromeDevTools/chrome-devtools-mcp, multica-ai/multica, anthropics/claude-plugins-official / 5/23: yt-dlp/yt-dlp, Kong/kong, patchy631/ai-engineering-hub, wshobson/agents, facefusion/facefusion) 모두 `assistant_turns: 0` (silent fail). 산출물 없음

- **이번 사이클의 신규 운영 관찰** — 5/22 와 5/23 두 사이클 연속 oss-radar 09:00 + research-wiki 08:00 잡 **모두 100% silent fail** 재발. 이는 5/17 이후 처음 (5/18~5/21 은 부분 실패 또는 부분 정상). 즉 시스템 단 (claude CLI 모델 백엔드 / 네트워크 / OAuth) 원인 의심이 다시 켜짐. 이를 oss-radar 의 2026-05-22 / 2026-05-23 changelog 한 단락씩 흡수
- **Updated**: `wiki/projects/oss-radar.md` — sources 에 5/22–5/23 21건 (08시대 6건 + 09시대 12건 + 헬스체크 3건) 추가, 변경 이력에 「2026-05-22 (09:00 cron + companion research-wiki)」 와 「2026-05-23 (09:00 cron + companion research-wiki)」 4 항목 추가 (2일 연속 100% silent fail = 5/17 이후 처음), updated 타임스탬프 갱신
- raw-sources/ 신규 .md 없음 확인: 모든 서브디렉터리 (articles/ books/ ideas/ papers/ transcripts/) .md 0건. Tips/ 의 PDF 8개도 기존 매핑 유지. `.cache/extracted/` 디렉터리 없음, fetched/ 디렉터리 없음, type: mcp-note 인 session-log 0건
- Marked ingested: true — 23개 session-log 파일 전체 (의미 있는 신규 산출 0 — disk-monitor 와 hermes 의 본격 분석 2건은 이전 사이클에서 이미 페이지에 흡수, 나머지 21건은 silent fail / 단발 종료)

## 2026-05-20T11:00 — wiki-ingest (session-logs, ingested: false 23건)

처리 0건 (의미 있는 신규 산출 없음 — 23건 중 대부분이 이전 일자 ingest 사이클에서 이미 changelog 형태로 흡수됨), 운영 관찰 기록 1건. 신규 페이지 0건, 기존 페이지 갱신 2건 (`projects/oss-radar`, `index`). raw-sources/ 신규 .md 없음 (서브디렉터리 articles/ books/ ideas/ papers/ transcripts/ 비어 있음, Tips/ 8 PDF 모두 기존 wiki/patterns/ 페이지에 이미 매핑), `.cache/extracted/` 디렉터리 없음 (PDF chunk 추출 미실행).

- **23건 분포**:
  - 07:00–07:40 dev-blog 13건 (Linux Daily 1 + OSS Trending 1 + OSS Curation 2 + Linux specialist list lens 9) — `assistant_turns` 0~1 분포 혼합. 이미 `wiki/projects/dev-blog.md` 의 2026-05-20 changelog 항목으로 흡수 완료 (이전 사이클에서 처리)
  - 08:00–08:01 research-wiki 3건 (alive ping + 논문 분석 2: arXiv 2605.18747 "Code as Agent Harness", arXiv 2605.18401 "SkillsVote") — 모두 `assistant_turns: 0` (silent fail). 산출물 없으므로 신규 분석 페이지 없음
  - 08:14 oss-radar AI provider 다중화 계획 1건 — 이미 [[cursor-agent-cli-overview]] 신규 페이지 + `wiki/projects/oss-radar.md` 의 5/20 changelog 로 흡수 완료 (이전 사이클)
  - 09:00–09:03 oss-radar 6건 (alive + OSS 분석 5: ECC / karpathy-skills / CLI-Anything / erpnext / 12-factor-agents) — `karpathy-skills` 1건만 `assistant_turns: 1` 로 [[karpathy-claude-md-skills]] 신규 페이지로 흡수 완료, 나머지 4건은 `assistant_turns: 0`. 이미 oss-radar 5/20 changelog 에 흡수 완료 (이전 사이클)

- **이번 사이클의 신규 운영 관찰** — 08:00 research-wiki 2건의 silent fail 을 `oss-radar` 의 2026-05-20 changelog 에 companion observation 으로 추가. **부분 silent fail 패턴의 진단 신호 보강**: 같은 호스트의 dev-blog 07:00 사이클은 13건 중 6건 정상 (혼합), 08:00 research-wiki 는 2건 모두 silent, 09:00 oss-radar 는 5건 중 1건만 정상 — 시스템 단 (5/17 의 광범위 silent fail 같은) 원인이 아니라 prompt 길이 / rate-limit / 모델 단발 미응답이 사이클별로 산발하는 *부분 실패* 패턴

- **index.md 누락 보정** — 최근 신규 페이지 3건 ([[cursor-agent-cli-overview]] / [[karpathy-claude-md-skills]] / [[llm-json-parse-retry-with-dump]]) 이 디스크에는 있으나 `wiki/index.md` 분석/패턴 섹션에 누락 → 한 줄씩 추가

- **Updated**: `wiki/projects/oss-radar.md` — sources 에 5/20 08시대 3건 추가, 변경 이력에 「2026-05-20 (companion: 08:00 research-wiki silent fail)」 한 줄 기록 (부분 실패 패턴 진단 신호 보강), updated 타임스탬프 갱신
- **Updated**: `wiki/index.md` — analyses 섹션에 [[cursor-agent-cli-overview]] / [[karpathy-claude-md-skills]] 한 줄씩 추가, patterns 섹션에 [[llm-json-parse-retry-with-dump]] 한 줄 추가, updated 갱신

- raw-sources/ 신규 .md 없음 확인: articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 .md 0건. `Tips/` 의 PDF 8개는 5/17 사이클에서 이미 매핑 완료. `.cache/extracted/` 디렉터리 없음 (PDF chunk 추출 미실행), raw-sources/<subdir>/fetched/ 없음, type: mcp-note 인 session-log 0건
- Marked ingested: true — 23개 session-log 파일 전체 (의미 있는 신규 산출 0 — 대부분 이전 사이클 흡수, 추가 운영 관찰 1건만 oss-radar changelog 로 흡수)

## 2026-05-17T10:00 — wiki-ingest (session-logs, ingested: false 16건)

처리 0건 (의미 있는 산출 없음), 운영 관찰 기록 2건. 신규 페이지 0건, 기존 페이지 갱신 2건 (`projects/dev-blog`, `projects/oss-radar`). raw-sources/ 신규 .md 없음 (모든 PDF/PPTX 가 기존 wiki 페이지에 이미 매핑됨, articles/ books/ ideas/ papers/ transcripts/ 서브디렉터리에 .md 없음), `.cache/extracted/` 디렉터리 없음 (PDF 추출 미실행).

- **16건 전체가 `assistant_turns: 0` silent fail 패턴** — 동일 호스트 (wookiui-Macmini) 의 3개 시간대 cron 잡이 일제히 모델 호출 무응답으로 끝남:
  - 07:00–07:30 dev-blog 7건 = 오픈소스 큐레이션 브리핑 1 + Linux specialist list lens rewrite 6 (각각 다른 lens 토픽)
  - 08:00–08:02 research-wiki 3건 = `Reply-with-only--OK` 헬스체크 1 + AI 논문 분석 2 (Qwen-Image-2.0 / AnyFlow)
  - 09:00–09:03 oss-radar 6건 = `Reply-with-only--OK` 헬스체크 1 + OSS 레포 분석 5 (continuedev/cli, pinokiocomputer/program-pinokio, microsoft/awesome-copilot, OliveTin/OliveTin, photoprism/photoprism)
- 5/16 에도 일부 (5건) 가 동일 silent fail 했었음 → **2일 연속 광범위 발생**. 5/16 의 단발성 silent fail 과 달리 5/17 은 헬스체크 OK 까지 응답 0이라 시스템 단 (claude CLI 모델 백엔드 / 네트워크 / OAuth) 원인 의심
- **운영 관찰만 기록, 코드 변경·신규 분석 페이지 없음** — 추측이 아닌 사실로 wiki 에 넣을 정보가 부족 (원인 진단 불가)
- **Updated**: wiki/projects/dev-blog.md — sources 에 5/17 07시대 7건 추가, 변경 이력에 「07:00 cron 잡 7건 전체 silent fail + 동일 호스트의 08:00 research-wiki 2건 + 09:00 oss-radar 5건도 동일 패턴 → 시스템 단 원인 의심」 한 단락 기록. 광범위 silent fail 의 진단 신호 (「동일 시간대 모든 잡이 `assistant_turns: 0`」) 를 단발 silent fail 과 구분해야 함을 명시
- **Updated**: wiki/projects/oss-radar.md — sources 에 5/17 09시대 6건 추가, 변경 이력에 09:00 OSS 분석 5건 + 헬스체크 OK 1건 모두 silent fail 한 줄 기록. 토픽·레포 단 결함이 아님을 명시
- raw-sources/ 신규 .md 없음 확인: articles/ 8개 PDF (Agent-Team-tmux / Claude Code 4월 tmux 불가 / Agent Teams 완전 도입 / Claude Code loop 완전 정복 / 기업보안 AWS-Bedrock / AI 쇼츠 / 토큰 절약 8가지 / wsl-vscode-tmux) 는 모두 기존 wiki/patterns/ 페이지에 이미 매핑 완료. Tips/ 도 동일. books/ ideas/ papers/ transcripts/ 비어 있음. `.cache/extracted/` 디렉터리 없음 (PDF chunk 추출 미실행 — 새로운 PDF 추출이 트리거되지 않음)
- Marked ingested: true — 16개 session-log 파일 전체 (스킵 14건 = silent fail 의 본질이 system-level 이라 토픽별 의미 추출 불가, 운영 관찰 2건만 위 프로젝트 페이지 갱신으로 흡수)

## 2026-05-17T07:30 — wiki-ingest (session-logs, ingested: false 20건)

처리 4건 (의미 있는 산출), 스킵 16건 (블로그 자동 워크플로우 step별 LLM 1회 호출). 신규 페이지 2건 (`projects/auto-pipe-ppt`, `analyses/python-pptx-design-token-pipeline`), 기존 페이지 갱신 2건 (`projects/auto-pipe-blog`, `projects/finance-analysis-nextjs`).

- Source: session-logs/20260516-212048-3947-*.md (cwd: `/Users/wooki/project/git/wk/auto-pipe-ppt`, "JSON/YAML/MD 콘텐츠 디스크립션과 수치를 업로드하면 자동으로 PPTX 를 생성. DESIGN.md 로 디자인 일관성, 샘플 PPT 퀄리티 수준" → 신규 프로젝트 git init → CLAUDE.md → tasks/todo.md → M0/M1/M2/M3 4 마일스톤. 결과물 commit 3건 (`f1cd8ab` M0+M1+M2 scaffold / `bb29394` 한글 폰트 ea/cs fix / `ed8d51c` M3 재무 컴포넌트 6종). 41건 테스트 그린, Apple/Minimalissimo 양쪽 정상 출력)
  - **Created**: wiki/projects/auto-pipe-ppt.md — 신규 프로젝트 페이지. python-pptx + 절대좌표 도형 + OOXML 직접 작성 전략 (Claude Web/Desktop PPT 와 동일 방식), 디자인 토큰 이중 어댑터 (YAML frontmatter / CSS :root), role resolver (컴포넌트 ↔ 디자인별 토큰 이름 격리), 한글 폰트 ea/cs typeface fix, qlmanage 첫 슬라이드 시각 검증의 한계, KPI 값 wrap 함정. M0~M3 진행, M4 차트 / M5 재무 어댑터 미구현
  - **Created**: wiki/analyses/python-pptx-design-token-pipeline.md — 일반 패턴 분리. PPTX 라이브러리 5종 비교 (python-pptx 1순위 + 콤보/레이더는 OOXML 직접 작성), 이중 입력 어댑터의 단일 트리 정규화, role resolver 의 코드 예시, 한글 ea/cs fix 의 OOXML 코드 (Apple SD Gothic Neo / Pretendard 권장), EMU 좌표 환산 (1px @96dpi = 9525 EMU 등), 시각 검증 (qlmanage 첫 페이지만 / CI 에 LibreOffice 헤드리스 필수)

- Source: session-logs/20260516-{194006,194148,194156,194315,194401,194512,201219,203135,203141,203250,203333,203504,203556,224156}-*.md (cwd: auto-pipe-blog, 14건 모두 자동 파이프라인의 step별 prompt 호출 = slug / research / outline / draft / factcheck rewrite / velog publish 의 2 글 분량). 산출물 자체는 각 단계의 단순 LLM 호출이라 별도 wiki 페이지 없음. **다만 factcheck rewrite 단계와 velog publish 가동은 auto-pipe-blog 의 신규 기능**이므로 변경 이력에 반영
- Source: session-logs/20260516-225709-62eb-*.md (cwd: auto-pipe-blog, "markdown 문서를 Notion 페이지로 변환·등록하는 비대화 모드 담당. parent ID 가 page 인지 database 인지 자동 판별 (retrieve-a-page → retrieve-a-database fallback), frontmatter 의 title/description/tags 파싱, 본문은 100 블록씩 patch-block-children 분할 추가, 로컬 이미지는 '수동 첨부 필요' 안내 paragraph 로 치환" → Playwright flaky 글이 Notion 페이지로 자동 등록)
  - **Updated**: wiki/projects/auto-pipe-blog.md — Phase 3 (velog 자동 발행) 완료 표시 + Phase 3.5 (Notion publisher 자동 등록 워크플로우) 추가. 변경 이력에 5/16 factcheck rewrite + Notion publisher 2건 기록. sources 에 새 세션 4건 추가

- Source: session-logs/20260516-223645-9081-*.md (cwd: finance-analysis-nextjs, "사이드 바에서 로딩된 json 인풋 파일을 다운로드 받을수 있는 기능을 추가해 주세요." → `src/components/layout/Sidebar.tsx` 의 "데이터 로드됨" 옆에 ⬇️ 버튼 추가, `companyData` → `{회사파일명}.json` Blob 다운로드, ESLint 통과, 단일 커밋 후 rebase push)
  - **Updated**: wiki/projects/finance-analysis-nextjs.md — 변경 이력에 2026-05-16 사이드바 JSON 다운로드 버튼 추가 1줄 기록 (단일 변경, 새 분석 페이지는 불필요)

- 스킵 16건:
  - 단순 검색·검증 LLM 호출 3건 (`223957-b4d4` Playwright 버전 web search / `224718-1cc2` Notion API page search 테스트 / `070013-fe91` Linux Daily Newsletter Rewrite 자동 호출) — 모두 `assistant_turns: 0~1` 의 단발 호출, 산출물 없음
  - auto-pipe-blog 파이프라인 step별 자동 LLM 호출 13건 (slug / research / outline / draft / factcheck / velog publish 의 2글 분량) — 단계별 단순 LLM 호출 결과. *새 설계 판단·기술 선택 없음*. factcheck rewrite + Notion publisher 라는 **신기능 추가** 자체는 위 변경 이력에 정리

- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음 (PDF/PPTX/TXT 만 존재). `.cache/extracted/` 디렉터리도 없음 (PDF 추출 미실행)

## 2026-05-16T14:30 — wiki-ingest (session-logs, ingested: false 17건)

처리 3건 (의미 있는 산출), 스킵 14건 (헬스체크 / 자동 잡 빈 응답). 신규 페이지 2건 (projects/auto-pipe-blog, analyses/su-01-olympiad-reasoning), 기존 페이지 갱신 1건 (projects/dev-blog).

- Source: session-logs/20260516-095909-4308-*.md (cwd: `/Users/wooki/project/git/wk/auto-pipe-blog`, "간단한 컨셉만 설명하면 자료조사부터 블로그에 포스팅할 수 있는 글로 만들어주는 프로그램을 만들 계획, CLAUDE.md 를 만들고 계획서 작성부터 먼저, 블로그는 velog 에 올릴 수 있는 형태, 다이어그램이나 그림까지 포함, AI 사용은 `claude -p` 또는 `agent -p`" → Phase 0 scaffold (commit `a9cbb0e`) + Phase 1 (commit `770b27f`, 5단계 파이프라인 + 7 prompts + 7 scripts + 첫 샘플) E2E 4분 검증)
  - **Created**: wiki/projects/auto-pipe-blog.md — 신규 프로젝트 페이지. 파이프라인 (concept → 00-slug → 01-research → 02-outline → 03-draft → 05-assemble → post.md) + 6 가지 주요 설계 판단 (bash + `claude -p` stdin / `CALL_LLM_BACKEND` 어댑터 단일 진입점 / velog 자동 발행 1차 범위 외 / mermaid → mmdc → PNG / 단계별 산출물 디스크 보존 / 샘플 git 추적). Phase 1 E2E 검증 표 (MVCC/VACUUM 컨셉, 5 단계 시간 + 행 수). [[dev-blog]] / [[multi-llm-provider-adapter-pattern]] 와 상호 링크

- Source: session-logs/20260516-120035-a415-*.md (cwd: dev-blog, "dev-blog 프로젝트의 AI provider 가 cursor 인가요? claude 인가요?" + "일단 ai provider 를 `claude -p` 로 변경" + "계속 1개씩 fail 이 발생하는 원인 뭔가요?" → 어댑터 일괄 변경 (cursor → claude) 은 0002 세션 ingest 와 중복이라 sources 추가만, 새 정보인 fail 원인 분석을 변경 이력에 추가. 5/16 android = gitiles `429 Too Many Requests` collect 실패, 5/15 linux-toolchain = `highlights[0].priority required` schema validation 실패. 매번 다른 토픽이 다른 단계에서 깨지는 구조 = 9 토픽 × 외부 호출 의존 → 일시 실패 확률의 곱)
  - **Updated**: wiki/projects/dev-blog.md — sources 에 새 세션 추가, 변경 이력 "2026-05-16 (2nd batch)" 항목 신설 (fail 의 토픽별 다른 단계 / 다른 원인, 9 토픽 × 외부 호출 의존 구조, 근본 완화 후보 ① collect 429/네트워크 지수 backoff ② rewrite schema validation retry / default 채움). 미구현 (제안만 기록)

- Source: session-logs/20260516-080028-7c79-*.md (cwd: `/Users/wooki/project/toy/research-wiki`, 자동 잡: "당신은 AI 연구 논문 분석 전문가입니다. 아래 논문을 읽고 한국어로 분석 — Achieving Gold-Medal-Level Olympiad Reasoning via Simple and Unified Scaling, arXiv 2605.13301" → Assistant 가 정상 응답, 1500자 분량 한국어 분석 산출. SU-01 모델의 4 단계 레시피 + 8 벤치마크 결과표 + 4 한계점 + 4 실무 적용 + 3 관련 논문)
  - **Created**: wiki/analyses/su-01-olympiad-reasoning.md — SU-01 논문 분석 (arXiv 2605.13301). 4 단계 레시피 (역퍼플렉시티 SFT / Coarse RL GSPO / Refined RL DeepSeekMath-V2 보상 + 자기수정 20% + 경험재생 25% / TTS 30사이클 × 10회 100K 토큰), 8 벤치마크 결과표 (IMO-ProofBench 70.2% / IMO 2025 35점 동점 1위 / USAMO 2026 35점 340명 중 최고), 4 한계점, 4 실무 적용, 3 관련 논문 (DeepSeek-R1 / ExGRPO / Winning Gold at IMO 2025). 일반 교훈 (표준 컴포넌트 재배열만으로 SOTA / 실패-수정 vs 성공-재생 비율 디자인의 가치)

- 스킵 14건:
  - 헬스체크 4건: `Reply-with-only--OK` (080022, 090048) / `say-only-the-word-PONG` (120817) — 자동 잡 ping
  - 자동 OSS 분석 5건 (090054 anthropics/skills, 090118 oven-sh/bun, 090151 mindsdb/minds-platform, 090219 langchain-ai/langgraph, 090244 mlflow/mlflow) — 모두 `assistant_turns: 0`, prompt 만 들어가고 응답 없음 (silent fail). raw-sources/papers/ 에 PDF 없음, 분석 산출도 없으므로 wiki 추가 항목 없음
  - 자동 논문 분석 1건 (080121 δ-mem arXiv 2605.12357) — `assistant_turns: 0`, 응답 없음. 분석 산출 없음
  - dev-blog Phase 0~2 후속 발행 4건 (120836 slug 생성 / 120842 자료조사 / 120945 글 구조 / 121027 본문 / 121142 velog frontmatter) — 모두 auto-pipe-blog 의 단계별 자동 prompt. 산출물은 각 단계의 단순 LLM 호출 결과 (slug 한 줄, research / outline / draft / post 본문). 의미 있는 *설계 판단*·*버그 수정*·*패턴*·*기술 선택* 없음. 모두 도구의 1회 호출

- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음 (PDF/PPTX/TXT 만 존재). `.cache/extracted/` 디렉터리도 비어 있음 (PDF 추출 미실행)

## 2026-05-16T13:30 — wiki-ingest (session-logs, ingested: false 5건)

처리 4건 + 스킵 1건. 신규 페이지 2건 (analyses), 기존 페이지 갱신 2건 (projects/upbit-trading, projects/dev-blog).

- Source: session-logs/20260515-231744-34b6-*.md (cwd: question, "python3.12 가 문서 폴더의 파일에 접근하려고 합니다 — 어디서 접근하려고 하는지 체크" → 5단계 진단 (`pgrep -lf python` 으로 후보 → `lsof -p` 로 Python.app 번들 인터프리터 PID 97336 확정, ht_trading 의 python3.13 / hermes 의 python3.11 은 버전 다름으로 제외 → cwd `/Users/wooki/project/toy/upbit_trading` 의 launchd `com.wooki.upbit-trading` 자식 → 코드에 Documents 참조 없음 → `fs_usage` 추적 시 아무것도 안 잡힘 (TCC 거부된 syscall) → 시스템 `/Library/Application Support/com.apple.TCC/TCC.db` 가 5/15 10:00:01 정시에 자동 변경된 흔적 = Apple 백그라운드 정책 푸시). "왜 갑자기" 의 가장 유력한 원인 = ① OS 본체 변경 없이 시스템 TCC.db 만 갱신 + ② 5/10 튜닝 커밋의 새 코드 경로가 며칠 만에 처음 호출. fs_usage / tccd 로그 추적은 정확한 라이브러리 미특정으로 끝나, 진단 절차와 일반 패턴만 영속화)
  - **Created**: wiki/analyses/macos-tcc-documents-popup-diagnosis.md — 5단계 진단 절차 (PID Python.app 번들 탐지 / 부모·가동시점 / 코드 참조 / fs_usage / 시스템 TCC.db mtime). "왜 갑자기" 의 2 원인 (Apple 백그라운드 정책 푸시 / 며칠 만에 처음 호출된 코드 경로). 대응 옵션 표 4건 (설정 거부 / 인터프리터 교체 / 라이브러리 캐시 경로 우회 / 무시), 함정 5건 (venv 도 framework 상속 / fork 자식 별 PID / cron `*/30` 단명 / TCC 거부에도 봇 동작 / fs_usage 가 TCC 거부 호출 미감지). iTerm 셸 시나리오 [[macos-tcc-full-disk-access]] 와의 격리 명시
  - **Updated**: wiki/projects/upbit-trading.md — "운영 잡음 — 갑작스러운 ~/Documents TCC 팝업 (2026-05-15)" 섹션 신설 (추적 결과 5건 + 봇 동작 무영향 + 실용적 대응), sources / updated / related / 변경 이력 갱신

- Source: session-logs/20260515-235344-9737-*.md + 20260516-000703-e374-*.md (cwd: question 2건. ① "claude agents 의 기능이 뭔가요" + "ctrl+B 를 눌러도 아무런 화면 변경이 없습니다" — 서브에이전트 정의 (컨텍스트 격리·병렬·전문 역할·도구 제한) + 빌트인 4종 + AGENTS.md + 화면 전환 키 (`Esc` / `Ctrl+B` FleetView). ② "다른 창에 claude code 창을 여러개 띄워놨는데 왜 못찾나요" + "claude --bg 로 띄운것만 claude agent 에서 모니터링 가능한가요" — Claude Code 인스턴스는 각각 독립 프로세스라 IPC 경로 없음. 한 인스턴스 안의 자식만 보임. 다중 창 통합 5 경로 (공유 파일/git / memory 디렉터리 / `$CLAUDE_JOB_DIR` / MCP 외부 저장소 / tmux))
  - **Created**: wiki/analyses/claude-code-tui-navigation-and-instance-isolation.md — 화면 전환 키 표 (`Esc` / `Ctrl+B` / `/config`) + 다중 인스턴스 격리 모델 (보이는 것 vs 안 보이는 것 표) + 다중 창 묶기 5 경로 표 + 트러블슈팅 4 시나리오 + 함정 4건 (`--bg` 의 부모-자식 오해 / 서브에이전트와 인스턴스 혼동 / `/config` 가 인스턴스가 아님 / memory 디렉터리는 자동 reload 안 됨). [[claude-code-basic-usage]] / [[claude-code-advanced]] / [[claude-code-agent-teams-tmux]] 와 상호 링크

- Source: session-logs/20260516-002256-34e8-*.md (cwd: dev-blog, "cursor 로 동작 시켜놓으니 10개 토픽중 하나씩 fail 이 발생 하는 듯합니다. claude -p 를 사용하도록 변경해 주세요" → 격리 worktree (`.claude/worktrees/claude-adapter-default`) 에서 14개 파일 +21/-19. `resolveAiAdapter('cursor')` → `'claude'` (4개 ai-rewrite 스크립트 + weekly + lens), `LENS_DEFAULT_ADAPTER` 상수 변경, `normalizeDailyRewriteAdapter` 빈 입력 기본값 `cursor` → `claude`, `rewriteScriptMap` fallback `rewrite:<topic>:cursor` → `rewrite:<topic>:claude` (5개 run-daily), `AI_ADAPTER` 분기 default, docs/SCHEDULING.md 문구. `cursor-agent` 별칭 / `AI_ADAPTER` per-invocation override / `DAILY_REWRITE_ADAPTER` 환경변수 모두 유지. 회귀 테스트 66/66 통과)
  - **Updated**: wiki/projects/dev-blog.md — "기본 AI 어댑터 cursor → claude 일괄 전환 (2026-05-16)" 섹션 신설 (14 파일 변경 표 + 환경변수 보존 사항 3건 + 일반 교훈 3건: `resolveAiAdapter` 첫 인자를 default 로 설계해 둔 응집이 일괄 변경 비용을 5분 작업으로 / 모듈 상수 default 패턴의 가치 / 빈 입력 회귀 테스트가 default 변경의 첫 가드). sources / updated / 변경 이력 갱신

- 스킵 1건:
  - session-logs/20260516-000529-7870-*.md (cwd: question, "이건 무슨 작업인가요?" — assistant_turns 0, 빈 세션. 의미 있는 산출 없음)

- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음 (PDF 만 존재)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- raw-sources/<subdir>/fetched/ 없음, .cache/extracted/ 디렉터리 자체 없음
- Updated: wiki/analyses/macos-tcc-documents-popup-diagnosis.md (신규), wiki/analyses/claude-code-tui-navigation-and-instance-isolation.md (신규), wiki/projects/upbit-trading.md, wiki/projects/dev-blog.md, wiki/index.md (analyses 2건 추가 + updated), wiki/log.md
- Marked ingested: true — 5개 session-log 파일

## 2026-05-15T10:00 — wiki-ingest (session-logs, ingested: false 9건)

처리 2건 + 스킵 7건. 신규 페이지 2건 (analyses), 기존 페이지 갱신 0건.

- Source: session-logs/20260515-080036-c38d-*.md (cwd: research-wiki, 자동 cron 의 AI 논문 분석. 논문 "MinT — 백만 LoRA 어댑터 학습·서빙 관리형 인프라" 한국어 분석 리포트 1편 산출. 어댑터 리비전 중심 재설계 / Scale Up·Down·Out 세 축 / 4B 어댑터 핸드오프 18.3× · 다중 정책 GRPO 1.77× · 단일 엔진 100K 정책 · 카탈로그 10⁶)
  - **Created**: wiki/analyses/mint-lora-serving-infrastructure.md — 어댑터 리비전 중심 훈련–서빙 재설계 + Megatron 분산 (R3 router replay) + 시간 분할 다정책 + 3계층 캐시. 결과 표 (10지표), 한계 4가지 (DSA 미완 / cold 직렬화 / rank-1 제한 / weak-locality p95 63s), 실무 적용 (다중 테넌트 SaaS / 지속 학습 / 비용 99% 절감), [[multi-llm-provider-adapter-pattern]] 과 비교축 명시

- Source: session-logs/20260515-090332-3138-*.md (cwd: oss-radar, 자동 cron 의 OSS 분석. `flipped-aurora/gin-vue-admin` 24,673 stars 의 한국어 분석 리포트. Go+Vue 풀스택 어드민 + AI 코드 생성기 + Casbin 3단계 권한 + 내장 MCP)
  - **Created**: wiki/analyses/gin-vue-admin-mcp-fullstack.md — 기술 스택 표 (Gin / GORM / Casbin / Vue 3 / Pinia / Viper / Swagger / MCP), 4가지 사용 시나리오, 주목 이유 (MCP 가 어드민에 박히는 신호 / Vibe Coding 부합), 라이선스·플러그인 마켓 주의점, [[oss-radar]] / [[claude-code-skills-plugins]] / [[claude-mcp-server-scope-and-add-json]] 상호 링크

- 스킵 7건 — 모두 자동 cron 작업의 빈 응답 또는 단순 핑:
  - session-logs/20260515-080023-260c-*.md (cwd: research-wiki, 단순 OK ping)
  - session-logs/20260515-080144-d720-*.md (cwd: research-wiki, AI 논문 자동 분석 요청 — assistant_turns: 0, 응답 없음)
  - session-logs/20260515-090051-b7bc-*.md (cwd: oss-radar, 단순 OK ping)
  - session-logs/20260515-090057-7fda-*.md (cwd: oss-radar, scrcpy 분석 요청 — assistant_turns: 0)
  - session-logs/20260515-090131-b4ab-*.md (cwd: oss-radar, gstack 분석 요청 — assistant_turns: 0)
  - session-logs/20260515-090213-abcf-*.md (cwd: oss-radar, openclaude 분석 요청 — assistant_turns: 0)
  - session-logs/20260515-090249-73ba-*.md (cwd: oss-radar, GitHub 레포 분석 요청 — assistant_turns: 0)

- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음 (PDF 만 존재, .cache/extracted/ 없음)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- raw-sources/<subdir>/fetched/ 없음
- Updated: wiki/analyses/mint-lora-serving-infrastructure.md (신규), wiki/analyses/gin-vue-admin-mcp-fullstack.md (신규), wiki/index.md (analyses 섹션 2건 추가 + updated 타임스탬프), wiki/log.md
- Marked ingested: true — 9개 session-log 파일

## 2026-05-14T18:00 — wiki-ingest (session-logs, ingested: false 1건)

처리 1건. 신규 페이지 1건 (analyses), 기존 페이지 갱신 1건 (projects/ht-trading).

- Source: session-logs/20260514-175837-5657-*.md (cwd: ht_trading, "수익율을 더 개선할 방안을 찾으려고 합니다. 지금 한국장 열린 사간에 동작 주기가 몇분 단위 인가요?" → launchd plist + `scripts/run_live.py:30` 확인 결과 `periodic --interval 10 --market domestic` 으로 10분 폴링, `config/trading.yaml: bar_interval: "1d"` 로 일봉 입력. 한국장 6.5h 동안 ~47회 폴링이 동일 일봉을 47번 반복 평가하는 구조. 사용자 후속 "5분으로 하는게 어떨까요?" → 일봉을 유지하면 5분 폴링도 같은 시그널 94회 평가일 뿐 알파 0 증가 + API 호출만 2배 라는 결론. 폴링 주기 단축이 의미를 가지려면 `bar_interval` 도 함께 분봉으로 내려야 함. 일봉 유지 시 진짜 레버는 진입/청산 타이밍 (시초가 슬리피지 회피, 종가 근처 14:30~15:20 분할 매수). 사용자 진행 방향 미확정 — 일반 사상만 분리)
  - **Created**: wiki/analyses/polling-interval-vs-bar-interval.md — 라이브 트레이딩 폴링 주기 (cycle interval) 와 봉 단위 (bar_interval) 의 정합성. 핵심 정의 (폴링 ≠ 시그널 갱신 주기, 시그널은 봉 단위에 묶임) + 안티패턴 (일봉 + 빠른 폴링은 같은 봉 반복 평가) + 폴링/봉 한 쌍 매트릭스 (4 시나리오) + 일봉 유지 시 진짜 레버 (시초가 회피 / 종가 분할 / VWAP/TWAP / 장중 1회 폴링) + 진단 체크리스트 5단계. 일반 패턴이므로 ht_trading 외 모든 자동매매 시스템에 적용
  - **Updated**: wiki/projects/ht-trading.md — sources/related/updated 갱신, 변경 이력에 2026-05-14 항목 추가 (운영 주기 정합성 검토: 10분 폴링 + 일봉의 함정, 폴링 단축은 알파 0 증가, 폴링/봉 한 쌍 동시 조정 필요)
- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음. .cache/extracted/ 도 없음
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/projects/ht-trading.md, wiki/analyses/polling-interval-vs-bar-interval.md (신규), wiki/index.md (analyses 섹션 + updated 타임스탬프), wiki/log.md
- Marked ingested: true — 1개 session-log 파일

## 2026-05-14T09:10 — wiki-ingest (session-logs, ingested: false 10건)

처리 1건 + 스킵 9건. 신규 페이지 0건, 기존 페이지 갱신 2건 (projects/dev-blog, analyses/llm-content-quality-guards).

- Source: session-logs/20260514-080604-8120-*.md (cwd: dev-blog, "자동 파이프라인 상태 2026-05-14 1개 토픽 실패 / 9개 성공. linux-gpu-ai - rewrite 단계에서 실패" → status JSON 으로 단계별 검증 (steps: ['collect:True', 'draft:True', 'rewrite:False']) → rewrite stdout 에 한자 "明文" 두 글자 혼입 발견 → `quality-guard.mjs` 의 `auditPostQuality` 가 정상 차단했음을 확인 → 4단계 수동 복구 (stdout 두 글자 한글 교정 → 일회용 스크립트로 rewritten JSON 재빌드 → `NEWSLETTER_DATE=2026-05-14 node scripts/publish-lore-lens.mjs linux-gpu-ai` → `markPublishOk` 가 status JSON 을 `ok=true` + `manualRepublishedAt` 마커로 in-place 갱신) → `npm run build` (10 topics, 63 posts) → commit `1f9db82`. **5/11 도입한 `if !` 토픽 격리의 첫 운영 성공 사례**)
  - **Updated**: wiki/projects/dev-blog.md — "rewrite 에 한자 두 글자 혼입 → quality-guard 정상 차단 (2026-05-14)" 섹션 신설 (status JSON 단계별 가시성의 진단 가치 + 4단계 복구 절차 + 4가지 일반 교훈: 격리 작동 / status 가시성 / quality-guard 정상 차단을 사고로 오대응 금지 / CJK 비한국어 혼입은 만성 함정). sources / updated / 변경 이력 갱신
  - **Updated**: wiki/analyses/llm-content-quality-guards.md — 4가지 → 5가지 가드로 확장. 5번째 가드 "비-한글 CJK 차단" 추가 (발생 메커니즘 4가지 + 가드 5-1 post-generation 검출 [`HANGUL` / `NON_HANGUL_CJK` 정규식] + 가드 5-2 프롬프트 강조 + 가드 5-3 단계별 status 가시성과 짝 + 일반화: 다국어 콘텐츠의 모든 강제 언어). 적용 위치 표 5행으로 확장. 제목 / 도입부 / 변경 이력 갱신

- 스킵 9건 — 모두 자동 cron 작업 (assistant_turns: 0):
  - session-logs/20260514-080029-4c11-*.md (cwd: research-wiki, 단순 OK ping)
  - session-logs/20260514-080036-bf79-*.md (cwd: research-wiki, 논문 `MemPrivacy: Privacy-Preserving Personalized Memory Management for Edge-Cloud Agents` arXiv 2605.09530 자동 분석 요청, 응답 없음)
  - session-logs/20260514-080124-883c-*.md (cwd: research-wiki, 논문 `SenseNova-U1: Unifying Multimodal Understanding and Generation with NEO-unify Architecture` arXiv 2605.12500 자동 분석 요청, 응답 없음)
  - session-logs/20260514-090057-b6b1-*.md (cwd: oss-radar, 단순 OK ping)
  - session-logs/20260514-090107-7a41-*.md (cwd: oss-radar, GitHub `files-community/Files` (43479★ Windows file manager, C#) 자동 분석, 응답 없음)
  - session-logs/20260514-090156-5144-*.md (cwd: oss-radar, GitHub `usebruno/bruno` (43754★ offline API client) 자동 분석, 응답 없음)
  - session-logs/20260514-090224-74b0-*.md (cwd: oss-radar, GitHub `appsmithorg/appsmith` 자동 분석, 응답 없음)
  - session-logs/20260514-090252-022b-*.md (cwd: oss-radar, GitHub `CorentinTh/it-tools` (38522★ Vue 개발자 도구) 자동 분석, 응답 없음)
  - session-logs/20260514-090327-8d6a-*.md (cwd: oss-radar, GitHub `ShareX/ShareX` (37498★ C# screen capture) 자동 분석, 응답 없음)
  - 자동 cron 결과물은 research-wiki / oss-radar 프로젝트 측에 별도 저장. llm_wiki 측에는 응답이 없어 추출할 지식 없음 → ingested: true 만 표시
- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음. .cache/extracted/ 도 비어 있음 (PDF chunk 추출 대기 상태)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/projects/dev-blog.md, wiki/analyses/llm-content-quality-guards.md, wiki/index.md (dev-blog 라인 + llm-content-quality-guards 신규 인덱스 + updated 타임스탬프), wiki/log.md
- Marked ingested: true — 10개 session-log 파일 전체 (처리 1건 + 스킵 9건; 신규 페이지 0건 + 기존 페이지 갱신 2건)

## 2026-05-13T10:30 — wiki-ingest (session-logs, ingested: false 10건)

처리 1건 + 스킵 9건. 신규 페이지 2건 (bugs 1 + patterns 1), 기존 페이지 갱신 1건 (projects/dev-blog).

- Source: session-logs/20260513-074737-a32f-*.md (cwd: dev-blog, "오늘날짜 포스팅이 안 보입니다. 오늘 동작 했는지 확인해 주세요" → launchd 잡 정상 실행됐으나 10개 토픽 모두 publish 단계 `Error: highlights[0].action required` 로 실패. 직전 commit `223ac17` 의 프롬프트 변경 (`action` → `if`/`do`/`verify` 3분해) 이 publisher 5종 + weekly + opensource rewrite validator 갱신과 비동기로 진행됨이 원인. 사용자 결정 "1번 진행 + 오늘것은 수동" → validator 를 build-site 와 동형으로 완화 (8 files +64/-14, 회귀 49/49 통과) + 8토픽 publish 직접 호출 + 2토픽 (opensource/opensource-curation) rewrite 부터 재실행 → 10토픽 5/13 분 복구. main push 는 자동 분류기에 막혀 사용자 직접 실행 안내. commit 2건 분리 (코드 vs 콘텐츠))
  - **Created**: wiki/bugs/highlights-action-validator-schema-drift.md — publish validator 의 highlights[].action 스키마 표류 사례. 증상 (10토픽 동일 throw + git push 가 `nothing to push` 로 silent skip) + 근본 원인 (commit 223ac17 의 프롬프트만 갱신, validator 미갱신) + 영향 범위 매트릭스 (8 files 수정 필요) + 수정 (build-site 와 동형 validator: `action` 또는 `if`+`do`+`verify` 둘 중 하나 허용) + 복구 절차 (publish 직접 호출 8 + run-daily 재실행 2) + 재발 방지 3가지 (validator 공용 모듈 / 스키마 변경 PR 체크리스트 / 사전 dry-run schema 검증)
  - **Created**: wiki/patterns/prompt-schema-pipeline-coupling.md — LLM 프롬프트 출력 스키마 ↔ 다운스트림 validator 결합 관리 패턴. 결합점 인벤토리 8가지 (프롬프트 본문/예시 JSON/rewrite/publish/builder/주간집계/회귀테스트/문서) + 흔한 안티패턴 3가지 (validator 복붙 / 단계별 검증 비대칭 / cron silent) + 권장 작업 순서 5단계 ("fixture 먼저 → 프롬프트 → dry-run → validator 완화 → deprecation") + "옛 OR 신" 둘 다 받는 validator 코드 + 가시성 신호 (today's run 0 commits → 명시적 비정상 종료). API 버전 헤더 관행과의 유추
  - **Updated**: wiki/projects/dev-blog.md — "highlights[].action 스키마 표류 (2026-05-13)" 섹션 신설 (증상 / 영향 / 수정 / 복구 절차 / git push 보안 분류기 차단 관찰) + sources/related/updated/변경 이력 갱신

- 스킵 9건 — 모두 자동 cron 작업 (assistant_turns: 0):
  - session-logs/20260513-080024-b5c7-Reply-with-only--OK.md (cwd: research-wiki, 단순 OK ping)
  - session-logs/20260513-080030-fed7-*.md (cwd: research-wiki, 논문 `Flow-OPD: On-Policy Distillation for Flow Matching Models` arXiv 2605.08063 자동 분석 요청, 응답 없음)
  - session-logs/20260513-080123-c524-*.md (cwd: research-wiki, 논문 `MACE-Dance: Motion-Appearance Cascaded Experts for Music-Driven Dance Video Generation` arXiv 2512.18181 자동 분석 요청, 응답 없음)
  - session-logs/20260513-090053-bd53-Reply-with-only--OK.md (cwd: oss-radar, 단순 OK ping)
  - session-logs/20260513-090059-1ebd-*.md (cwd: oss-radar, GitHub `rtk-ai/rtk` (46714★ Rust CLI proxy) 자동 분석, 응답 없음)
  - session-logs/20260513-090133-130b-*.md (cwd: oss-radar, GitHub `agno-agi/agno` (40085★ Python agent platform) 자동 분석, 응답 없음)
  - session-logs/20260513-090215-8bd9-*.md (cwd: oss-radar, GitHub `hpcaitech/ColossalAI` 자동 분석, 응답 없음)
  - session-logs/20260513-090256-da8f-*.md (cwd: oss-radar, GitHub `danielmiessler/Fabric` 자동 분석, 응답 없음)
  - session-logs/20260513-090329-59af-*.md (cwd: oss-radar, GitHub `sickn33/antigravity-awesome-skills` 자동 분석, 응답 없음)
  - 자동 cron 결과물은 research-wiki / oss-radar 프로젝트 측에 별도 저장. llm_wiki 측에는 응답이 없어 추출할 지식 없음 → ingested: true 만 표시
- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음. Tips/ 의 PDF 들은 `.cache/extracted/` 추출 대기 (이 ingest 사이클 처리 대상 외). .cache/ 디렉터리 자체가 비어 있음
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/projects/dev-blog.md, wiki/index.md (신규 2 페이지 1줄씩 + updated 타임스탬프), wiki/log.md
- Marked ingested: true — 10개 session-log 파일 전체 (처리 1건 + 스킵 9건; 신규 페이지 2건 + 기존 페이지 갱신 1건)

## 2026-05-13T08:00 — wiki-ingest (session-logs, ingested: false 2건)

처리 2건 + 스킵 0건. 신규 페이지 6건 (analyses 2 + patterns 2 + bugs 1 + projects 0), 기존 페이지 갱신 1건 (projects/finance-analysis-nextjs).

- Source: session-logs/20260512-214455-2046-*.md (cwd: personal-vim-md-editor, "지금 프로젝트는 vim 인터페이스의 터미널형태의 코딩 에디터 ... CLAUDE.md 를 만들어 주세요" → SSH 환경에 Tauri 가 안 맞다는 사실 발견 → glow + neovim 워크플로 + CLI 도구 추천 + Notion MCP 등록. 32 prompts + 31 turns + 32 bash + 4 file edits, commit `5a18fdc`)
  - **Created**: wiki/analyses/terminal-markdown-viewer-tools.md — 터미널·CLI 마크다운 뷰어 6종 비교표 (glow / mdcat / frogmouth / bat / neovim + render-markdown.nvim / markdown-preview.nvim). Mermaid SVG 의 터미널 본질적 한계 + 4단계 우회 (코드블록 → ASCII → 외부 뷰어 → 인라인 이미지). neovim 마크다운 렌더 3단계 + Tauri/Electron 데스크탑 앱이 SSH 환경에 부적합한 이유 (webview, DOM 의존, 빌드 산출물 GB 단위)
  - **Created**: wiki/patterns/ssh-cli-toolkit-essentials.md — SSH 개발 환경 CLI 도구 우선순위. Top 3 (tmux/ripgrep/fzf) + 보조 4종 (bat/fd/lazygit/delta) + mac/linux 설치 명령 + 표준 워크플로 (`tmux new -s work` → `rg --type md` → `glow -p $(fzf)` → `Ctrl+B d`). 왜 이 3개가 우선인가의 정성적 설명
  - **Created**: wiki/patterns/claude-mcp-server-scope-and-add-json.md — Claude Code MCP 등록의 4 함정. `-e KEY=VALUE` 의 셸 토큰 분해 모호함 → `add-json` 우회 / 기본 `local` scope 의 디렉터리 격리 → `-s user` 필수 / 새 MCP 는 세션 시작 시만 로드 → 재시작 / 외부 MCP + API key 자동 승인 거부. `~/.claude.json` 의 `mcpServers` (user) vs `projects.<path>.mcpServers` (local) 분기 + `print -l --` 토큰 분해 진단
  - **Created**: wiki/bugs/grep-env-var-leak-to-chatlog.md — `grep -c "NOTION_API_KEY" ~/.zshrc` 가 라인 전체 출력으로 API key 가 LLM 채팅 로그에 그대로 노출된 실 사고. 즉시 조치 (revoke/rotate → `.zshrc` 갱신 → 새 셸) + 안전한 환경변수 검증법 (`echo "len=${#VAR}"` / `[ -n "$VAR" ]` / `grep -q`) + AI 어시스턴트 측 위생 (마스킹 출력, 사전 안전한 명령 설계). 관련: [[mcp-config-secret-exposure-via-ps]]

- Source: session-logs/20260512-231800-c191-*.md (cwd: finance-analysis-nextjs, "본 프로젝트를 비판적으로 검토해서 더 추가하면 좋을 기능을 알려 주세요" → 7개 갭 식별 → 사용자 "순차적으로 진행, 페이즈별 커밋" → Phase 1~7 일괄 구현. 3 prompts + 3 turns + 70+ file edits + commit 7건: `a9568b9` `926567e` `44ff302` `9eefe34` `fbefdfa` `65c191c` `98a8101`)
  - **Created**: wiki/analyses/financial-health-composite-scores.md — 재무 건전성 합성 스코어 3종 비교 (Altman Z / Piotroski F / Beneish M) + 5 카테고리 룰 기반 risk flag (profitability/liquidity/leverage/efficiency/cash). 한국 시장 적용 시 함정 — Z 의 운전자본·이익잉여금 절대값 누락 → 단순화된 룰 기반 distress signal 대체 / F 의 insufficient fallback / Beneish 의 false positive. LLM 호출 0 의 가치 (속도/비용/신뢰/튜닝)
  - **Created**: wiki/patterns/ai-token-usage-cost-guard.md — AI 토큰 사용량 + per-user 일일 비용 한도 가드 패턴 4단계. `usage_events` 테이블 + `lib/pricing.ts` (provider/model pricing, prefix 매칭으로 datestamped Anthropic 대응, provider default safety net) + `ai-client.ts` 가 `UsageInfo` 함께 반환 + entrypoint 가드 (사전 한도 체크 + 사후 recordUsage). client-direct 우회 경로용 `/api/usage` POST reporting endpoint + `/api/anthropic-config` 의 사전 가드. UTC 자정 cutoff, $5 기본 한도
  - **Updated**: wiki/projects/finance-analysis-nextjs.md — 2026-05-12 비판적 검토 7개 갭 + Phase 1~7 일괄 구현 섹션 신설. commit 7건 표 (F-Score / CSV / 토큰 비용 가드 / risk flag / 공유 링크 / 시나리오 패널 / vitest 32 테스트) + 우선순위 결정 (비용/리스크 대비 효과) + 빌드 함정 (`PerformanceData` 인덱스 시그니처 누락) + 일반 패턴 분리 ([[financial-health-composite-scores]], [[ai-token-usage-cost-guard]]). sources/related/updated 갱신, 변경 이력 신설 항목

- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음 (Tips/ 및 루트의 PDF/pptx/txt 들은 처리 대상 외). .cache/extracted/ 디렉터리 없음 (PDF 자동 추출 대상 없음)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/projects/finance-analysis-nextjs.md (Phase 1~7 + sources/related/변경이력), wiki/index.md (finance-analysis-nextjs 1줄 업데이트 + 신규 5 페이지 1줄씩 추가 + updated 타임스탬프), wiki/log.md
- Marked ingested: true — 2개 session-log 파일 전체 (처리 2건 + 스킵 0건; 신규 페이지 5건 + 기존 페이지 갱신 1건)

## 2026-05-12T15:00 — wiki-ingest (session-logs, ingested: false 14건)

처리 1건 + 스킵 13건. 신규 페이지 0건, 기존 페이지 갱신 1건 (wiki/projects/dev-blog.md).

대부분의 핵심 내용은 직전 자동 인제스트 (`2889307 auto: wiki update 20260512-0800`) 가 이미 wiki/projects/{dev-blog, ht-trading, japa-asset-dashboard}.md 에 반영 완료 (5/11 dev-blog 콘텐츠 품질 가드 4종 + ht_trading V3 컷오프 캘리브레이션 + japa Supabase 리전 마이그레이션 + CSV round-trip). 본 인제스트는 5/12 8시 이후의 새 세션 (dev-blog 자동화 누락 발견) 1건만 추가 반영.

- Source: session-logs/20260512-093236-ee09-*.md (cwd: dev-blog, "오늘 5/12일 업데이트가 안된 토픽이 있다 — 분석해서 업데이트 되도록 해 주세요" — 8 prompts + 8 turns + commits 2건)
  - **Updated**: wiki/projects/dev-blog.md — `daily-deploy.sh` 의 자동화 토픽 누락 발견·수정 섹션 신설. launchd 진입점이 linux/android/opensource 3개만 호출하고 있어 lens 8종 + opensource-curation 은 매일 silent 누락 (5/11 의 12개 토픽 게시는 사실 수동 실행 결과였다). 9개 토픽 5/12 분 수동 재생성 (run-all-kernel-lenses + run-daily-opensource-curation, 첫 실패 lens 는 per-topic 루프 + 1회 재시도 셸 스니펫으로 회피) + daily-deploy.sh 에 per-topic 루프 + `if !` 격리로 lens 8종 / opensource-curation 추가. `run-all-kernel-lenses` 자체도 stop-on-first-fail 함정이 있어 per-topic 우회 필요. 일반 교훈: multi-topic 파이프라인에서 「토픽 진입점 / 자동화 호출 목록 / 그룹 호출자」 3계층 모두 토픽 추가 시 동기화 필요. sources 에 ee09 추가, 변경 이력에 "2nd batch" 항목 신설. 관련: [[shell-set-eu-topic-isolation]]

- Source: session-logs/20260511-230001-14d5-*.md (이미 직전 자동 인제스트 commit `2889307` 가 wiki/projects/dev-blog.md 의 콘텐츠 품질 가드 4종 섹션 + commit 2a4b2ec / 2cc5ff5 변경 이력으로 반영 완료. ingested:false 만 잔존)
- Source: session-logs/20260511-230648-4621-*.md (이미 직전 자동 인제스트가 wiki/projects/ht-trading.md 의 V3 컷오프 캘리브레이션 섹션 + commit 50c929c 변경 이력 + V2/V3 30 매칭 종목 Spearman ρ=+0.835 / Top-10 80% 교집합 분석 + [[scoring-version-comparison-methodology]] 분리로 반영 완료. ingested:false 만 잔존)
- Source: session-logs/20260512-000725-28e8-*.md (이미 직전 자동 인제스트가 wiki/projects/japa-asset-dashboard.md 의 2026-05-12 항목 — CSV round-trip + Supabase 뭄바이→서울 리전 마이그레이션 9단계 + [[supabase-region-migration]] / [[csv-roundtrip-backup-restore]] / [[nextjs16-use-server-non-async-export]] 분리로 반영 완료. ingested:false 만 잔존)

- Skipped (단발 QA, assistant_turns: 0 — 사용자 질문만 있고 응답 없이 종료): 20260512-011921-bc6c "mac 에서 .dmsg 로 앱 설치 후 .dmsg 삭제 가능?" (cwd: question, 1 prompt + 0 turn — 답변되지 않은 1회성 질문, 일반 지식이지만 응답이 없어 wiki 화 대상 외)
- Skipped (cron heartbeat — `Reply with only: OK`, assistant_turns: 0): 20260512-080024-b516 (research-wiki), 20260512-090051-62dc (oss-radar). 2건
- Skipped (research-wiki cron 의 논문 분석 입력 — assistant_turns: 0, 본 cron 의 응답은 research-wiki 파이프라인이 직접 받아 처리하므로 별도 인제스트 불요): 20260512-080032-4eeb (arXiv 2605.06716, *From Storage to Experience: A Survey on the Evolution of LLM Agent Memory Mechanisms*), 20260512-080135-6c12 (arXiv 2605.06169, *Mean Mode Screaming: Mean-Variance Split Residuals for 1000-Layer Diffusion Transformers*). 2건
- Skipped (oss-radar cron 의 OSS 분석 입력 — assistant_turns: 0): 20260512-090057-be65, 20260512-090130-eadb, 20260512-090205-0665, 20260512-090238-a42d, 20260512-090313-b25c. 5건

- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음 (Tips/ 의 PDF 들은 기존 wiki 페이지가 모두 존재). .cache/extracted/ 디렉터리 없음 (PDF 자동 추출 대상 없음)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/projects/dev-blog.md (daily-deploy.sh 자동화 누락 섹션 + sources + 변경 이력), wiki/log.md, wiki/index.md (updated 타임스탬프만 — 신규 페이지 없어 목차 변경 없음)
- Marked ingested: true — 14개 session-log 파일 전체 (처리 1건 + 스킵 13건; 신규 페이지 0건 + 기존 페이지 갱신 1건)

## 2026-05-11T09:30 — wiki-ingest (session-logs, ingested: false 9건)

처리 0건 + 스킵 9건. 모든 신규 로그가 cron 자동 입력 (research-wiki / oss-radar 의 paper / OSS 일일 분석 + heartbeat). 신규 wiki 페이지 0건, 기존 페이지 갱신 0건.

- Skipped (cron heartbeat — `Reply with only: OK`, assistant_turns: 0): 20260511-080024-fcd1 (research-wiki), 20260511-090048-1738 (oss-radar). 2건
- Skipped (research-wiki cron 의 논문 분석 입력 — assistant_turns: 0, 논문 본문이 입력 프롬프트에 직접 포함된 형태. 본 cron 의 응답은 research-wiki 파이프라인이 직접 받아 처리하므로 별도 인제스트 불요): 20260511-080031-55d7 (arXiv 2605.05242, *Beyond Semantic Similarity: Direct Corpus Interaction*), 20260511-080145-d3ea (arXiv 2605.06130, *Skill1: Unified Evolution of Skill-Augmented Agents via RL*). 2건
- Skipped (oss-radar cron 의 OSS 분석 입력 — assistant_turns: 0, 본 cron 의 응답은 oss-radar 파이프라인이 직접 받아 처리하므로 별도 인제스트 불요): 20260511-090056-b201 (JuliusBrussee/caveman, 57k stars, Claude Code skill ~75% 토큰 절약), 20260511-090135-0493 (google-research/google-research), 20260511-090217-df09 (Fission-AI/OpenSpec), 20260511-090250-9ecb (asgeirtj/system_prompts_leaks), 20260511-090330-f6ff (streamlit/streamlit). 5건
- raw-sources/ 의 신규 .md 없음 — Tips/ articles/ books/ ideas/ papers/ transcripts/ 의 모든 서브디렉터리에 PDF / .pptx / .txt 만 존재 (chunk MD 처리 대상 외). .cache/extracted/ 디렉터리 없음 (PDF chunk 처리 대상 외)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/index.md (updated 타임스탬프만 — 신규/갱신 페이지 없음), wiki/log.md
- Marked ingested: true — 9개 session-log 파일 전체 (skip 9건, 처리 0건)

## 2026-05-11T00:00 — wiki-ingest (session-logs, ingested: false 7건)

처리 2건 + 스킵 5건. 신규 페이지 3건 (analyses 2 + projects 1), 기존 페이지 갱신 1건 (projects/ht-trading).

- Source: session-logs/20260510-194933-8c5e-*.md (cwd: upbit_trading, "수익율을 더 높이기 위한 전략을 추천해 주세요" — 5 prompts + 5 turns + 45 bash + 3 file edits + commit `e3b8...` + launchd 재시작)
  - **Created**: wiki/projects/upbit-trading.md — Upbit 암호화폐 무한매수법 자동매매 (Python + APScheduler + launchd, 40분할 DCA + Trailing Stop). 70일 운영 평균 +5.20% (10라운드, 모두 trailing 청산). 5개 키 즉시 적용 (A trailing 2.0→2.5% / B cooldown 12→6h / C max_round_days 90→45 + 계단식 30/60/90→15/30/45 정합 / D partial_profit ON levels [(4.0,0.3),(6.0,0.3)] / E tighten_on_weakness ON). 진단된 수익 저해 패턴 5건 (trailing 너무 타이트 / 부분익절 미사용 / 자본 묶임 / 종목 2개 분산 부족 / 추세 필터 모두 OFF). 백로그 F (use_dynamic_coin_selection True), G (btc_24h_drop_threshold -3→-5 + use_btc_trend_filter True). DB 스키마 (`infinite_buy_rounds.peak_price`/`consumed_partial_levels` 가 재시작 후 trailing·partial 상태 복원의 핵심)
  - **Created**: wiki/analyses/dca-trailing-stop-tuning.md — DCA·Trailing Stop 자동매매 튜닝의 일반화된 5 레버 (trailing 거리 / 매수 쿨다운 / max_round_days + 계단식 / partial profit / tighten on weakness) + 운영 로그 진단 6단계 (라운드 평균/청산 사유/수익 분포/자본 묶임/매수 차단 사유/종목 분산도) + 즉시 vs Paper 검증 vs 중기 분류 + 자본 분산 함정 + 추세 필터 양면성 (OFF/타이트 ON/완화 ON spectrum) + 라이브 봇 재시작 안전 체크리스트 (`InfiniteBuyStrategy 초기화: ...` 한 줄 grep 검증)

- Source: session-logs/20260510-195349-94ba-*.md (cwd: ht_trading, "수익을 더 올릴수 있는 방안을 제안해 주세요" — 15 prompts + 14 turns + 88 bash + 16 file edits + commits `9d69502` `b507f2a` `0088d85` `d0571c5` + launchd 재시작 2회)
  - **Updated**: wiki/projects/ht-trading.md — V3 점수 알고리즘 (IC 검증 기반 재설계) 섹션 신설. `n_stock_info` 별도 저장소 commit `0088d85` (technical 40→50 / fundamental 40→30 / EPS 절대값 → earnings_yield / ATR 신규). IC 검증 결과 핵심 (양봉/거래량 sweet 최강 알파 +0.039/+0.029, MA 정렬 IC -0.003 거의 0, 단기 모멘텀 IC -0.038 음수 mean reversion, RSI 안정구간 IC -0.020 음수, 단일 룰 OR `강양봉 AND 거래량 sweet` 승률 67.2% 평균 +3.38%). A/C/D 튜닝 (commit `d0571c5`) — 체결률 (limit_price_ratio 1.0→1.005 → 36% → 70%+ 기대), 트레일링·단계익절 둔감화 (5%→4% + 18% tier 추가), 상대손절 완화 (15%→20%). B/E/F/G 는 `tasks/backlog.md`. 라이브 분석 스크립트 `scripts/analyze_live_period.py` (commit `9d69502`, +407, --from/--to/--recent/--compare-with). 검증 스크립트 `scripts/validate_score_ic.py` (commit `b507f2a`, +588). 적용 후 ScoringStrategy 초기화 한 줄 비교 검증
  - **Created**: wiki/analyses/scoring-system-ic-validation.md — 룰베이스 트레이딩의 종목 선별 점수 검증 방법론. IC (Pearson/Spearman) 정의, 표본 설계 5가지 결정, 한국 시장 검증 결과 (n=2362), cutoff 시뮬레이션, AND vs OR 게이팅, regime 안정성, 검증 → 재설계 표준 절차 6단계, 펀더는 forward 로깅 인프라 권장 (분기 데이터 과거 시점 재현 어려움), 안티 패턴 4가지 (검증 없는 가중치 직관 조정 / 합산 점수만 측정 / single-asset 검증 / forward horizon 단일 측정)

- Skipped (kakao-db 의 카톡 채팅 요약 prompt 테스트 5건, assistant_turns: 0, 모두 prompt-only — kakao-db 가 LLM 으로 라우팅하는 채팅 요약 기능을 테스트한 흔적. 새 wiki 페이지 작성할 만한 일반 지식 없음, kakao-db 프로젝트 문서에 *summary 기능 테스트 흔적* 정도 mention 만 가능): 20260510-233843-1e8c (한 줄 요약 프롬프트), 20260510-234019-1af3 (오픈테스트방 4 메시지 요약), 20260510-234036-b5f2 (오픈테스트방 1 메시지), 20260510-234521-e7d0 (다른방 1 메시지), 20260510-234533-571c (인덱스 출력)

- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음 (Tips/ 의 PDF 들은 기존 wiki 페이지가 모두 존재). .cache/extracted/ 디렉터리 없음 (PDF 자동 추출 대상 없음)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/index.md (upbit-trading 프로젝트 1줄 + ht-trading V3 추가 1줄 + analyses 2건 추가 2줄, updated 타임스탬프), wiki/log.md, wiki/projects/ht-trading.md (V3 + A/C/D 섹션 + 변경 이력)
- Marked ingested: true — 7개 session-log 파일 전체 (처리 2건 + 스킵 5건; 신규 페이지 3건 + 기존 페이지 갱신 1건)

## 2026-05-10T09:30 — wiki-ingest (session-logs, ingested: false 12건)

처리 0건 + 스킵 12건. 모든 신규 로그가 dev-blog / research-wiki / oss-radar 의 cron 자동 입력 또는 heartbeat. 신규 wiki 페이지 0건, 기존 페이지 갱신 1건 (projects/dev-blog).

- Skipped (dev-blog cron 자동 입력 — 시스템 프롬프트만, dev-blog 파이프라인이 직접 받아 처리하므로 별도 인제스트 불요): 20260510-070019-a130 (Linux Daily Newsletter Rewrite, assistant_turns: 0), 20260510-070412-fd9a (Open Source Trending Daily Briefing, assistant_turns: 0). 어제 [[dev-blog]] log entry (2026-05-09) 와 같은 multi-topic 가동 패턴.
- Skipped (dev-blog Android Kernel Daily Briefing — 1턴 자동 출력, 일일 발행물이라 시간 지나면 outdated): 20260510-070200-f6f1 (assistant_turns: 1, file_edits: 1, 결과물은 `dev-blog/content/topics/android/posts/2026-05-10-android-daily-briefing.json`)
- Skipped (cron heartbeat — `Reply with only: OK`, assistant_turns: 0): 20260510-080022-053f (research-wiki), 20260510-090048-4ae8 (oss-radar)
- Skipped (research-wiki cron 의 논문 분석 입력 — assistant_turns: 0): 20260510-080029-6a68 (arXiv 2605.05185, fatal-aware GRPO), 20260510-080128-8e5f (arXiv 2604.28196). 2건
- Skipped (oss-radar cron 의 OSS 분석 입력 — assistant_turns: 0): 20260510-090055-a233, 20260510-090130-632e, 20260510-090206-4410, 20260510-090244-772a, 20260510-090324-7062. 5건
- Updated: wiki/projects/dev-blog.md — 변경 이력에 multi-topic 정상 가동 확인 + 토픽별 시스템 프롬프트의 큐레이션 정책 차이 (Linux 의 fromMaintainer/maintainerComments 메타 / Android 의 ACK prefix 풀어쓰기 + GKI/ABI 가중 / OSS Trending 의 HN frontpage 1순위) + 공통 휴리스틱 (3-tier priority 강제, implications/nextActions 보충 섹션 금지, 데이터 부족 시 솔직한 fallback). sources 에 070019/070200/070412 3건 추가, updated 2026-05-10. 새 wiki/analyses/ 페이지는 만들지 않음 (어제 동일 자료 보고도 동일한 판단)
- raw-sources/ 의 신규 .md 없음 — articles/ books/ ideas/ papers/ transcripts/ 모든 서브디렉터리 비어 있음. .cache/extracted/ 디렉터리 없음 (PDF 자동 추출 대상 없음)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/index.md (dev-blog 한 줄 요약에 Multi-topic 가동 확인 추가, updated 타임스탬프), wiki/log.md, wiki/projects/dev-blog.md (sources / 변경 이력 / updated)
- Marked ingested: true — 12개 session-log 파일 전체 (skip 12건, 처리 0건; 기존 페이지 갱신 1건)

## 2026-05-09T15:30 — wiki-ingest (session-logs, ingested: false 12건)

처리 1건 + 스킵 11건. 신규 페이지 1건 (analyses 1), 기존 페이지 갱신 1건 (projects/japa-asset-dashboard).

- Source: session-logs/20260509-080729-fd9f-*.md (cwd: japa, "기존 종목을 매수 또는 매도하는 부분이 없는 것 같습니다" — 4 turns + 9 file edits + commit 0aa2187 push)
  - **Created**: wiki/analyses/holding-transaction-cost-basis-design.md — 보유 종목 매수/매도 거래 추적의 일반화된 설계 4결정. (1) 평단가 산식: 한국 양도세 표준 가중평균 `(oldAvg·oldQty + price·qty + fee) / newQty` (수수료 취득원가 포함), 매도 시 평단 유지·수량 차감 (2) 실현 손익은 SELL row 의 `realizedGain` 컬럼에 박아 동결 (현재 평단가에서 역산하면 추가 매수가 과거 매도 손익을 흔들어 양도세 신고 자료 신뢰성 0). 시점성 파생값의 일반 패턴 (3) 거래 삭제는 효과 역연산 (BUY: 평단 재계산 + cash 환급 / SELL: qty 복원 + 평단 유지 + cash 차감). 수정은 1차 미지원 (삭제 후 재입력) (4) cashBalance 자동 갱신은 계좌·거래 통화 일치 시에만 (환전 시점 모호함 회피, fxRate 는 표시용 only). MVP/풀구현/입력만 3범위 비교, 회계성 데이터 일반 원칙 4가지 (예측치/실측치 분리, 시점성 파생값 동결, $transaction 묶음, 삭제 역연산 가능성)
  - **Updated**: wiki/projects/japa-asset-dashboard.md — BUY/SELL Transaction 추적 → ✅ MVP 적용 (commit 0aa2187, +938/-16) 섹션 신설. enum 1차 BUY/SELL, Transaction 모델 + migration 20260508232227, `lib/transactions/schema.ts` Zod, `app/actions/transactions.ts` createTransaction/deleteTransaction (`prisma.$transaction` 묶음), `components/forms/transaction-form.tsx`, 신설 `/holdings/[id]` detail + `/holdings/[id]/trade/new?type=BUY|SELL`, 자산명 → detail 링크 + 계좌 detail 매수/매도 단축 아이콘. 후속 백로그 (Tax 페이지 실현/미실현 분리 / CSV export / DEPOSIT/WITHDRAW enum 확장) 명시. 변경 이력 + sources/related 갱신, holding-transaction-cost-basis-design 와 cross-link
- Skipped (dev-blog cron 자동 입력 — Linux Daily Newsletter 가 아닌 Android Kernel Daily Briefing / Open Source Trending Daily Briefing 으로 토픽 확장. JSON 출력 templated, dev-blog 파이프라인이 직접 받아 처리하므로 별도 인제스트 불요): 20260509-074318-377b (Android Kernel Daily Briefing), 20260509-075110-05ad (Open Source Trending Daily Briefing). 기존 Linux Kernel 만이 아닌 토픽 다양화 진행 중 ([[dev-blog]] 의 Multi-topic 전제 부합)
- Skipped (cron heartbeat — `Reply with only: OK`, assistant_turns: 0): 20260509-080025-d8bf (research-wiki), 20260509-090053-a9f7 (oss-radar)
- Skipped (research-wiki cron 의 논문 분석 입력 — assistant_turns: 0, 논문 본문이 입력 프롬프트에 직접 포함된 형태): 20260509-080032-ef14, 20260509-080122-a28e (2건)
- Skipped (oss-radar cron 의 OSS 분석 입력 — assistant_turns: 0): 20260509-090100-a5e6, 20260509-090141-9120, 20260509-090222-8603, 20260509-090254-77e1, 20260509-090329-6c4b (5건)
- raw-sources/ 의 신규 .md 없음 — Tips/ articles/ books/ ideas/ papers/ transcripts/ 의 모든 서브디렉터리에 PDF / .pptx / .txt 만 존재 (chunk MD 처리 대상 외). .cache/extracted/ 디렉터리 없음 (PDF 자동 추출 대상 없음)
- mcp-note 없음 — `type: mcp-note` 인 session-log 0건
- Updated: wiki/index.md (analyses 에 holding-transaction-cost-basis-design 1건 추가, japa-asset-dashboard 요약 한 줄 갱신, updated 타임스탬프), wiki/log.md, wiki/projects/japa-asset-dashboard.md (sources / related / 본문 1섹션 / 변경 이력 갱신)
- Marked ingested: true — 12개 session-log 파일 전체 (skip 11건, 처리 1건 → 신규 페이지 1건 + 기존 페이지 갱신 1건)

## 2026-05-09T13:00 — wiki-ingest (session-logs, ingested: false 21건)

처리 1건 + 스킵 20건. 신규 페이지 2건 (projects 1 / analyses 1), 기존 페이지 갱신 1건 (concepts/hermes-agent).

- Source: session-logs/20260509-001610-307a-*.md (cwd: toy/hermes, hermes 코딩 전용 프로필 maccoder 추가 + 13 turns + 75 bash + 7 file edits)
  - **Created**: wiki/projects/hermes.md — toy/hermes 프로젝트 기록. default + 코딩 전용 `maccoder` 두 프로필 운영, 셋업 핵심 결정 5가지 (`profile create --clone` / OAuth symlink 공유 / Claude CLI HOME 복원 wrapper / `.bash_profile` PATH 주입 / Telegram 별도 봇 reconfigure), 파일 레이아웃, ACP 방향성 (server-only / claude 미채택), end-to-end 검증, 운영 명령어, 알려진 함정. commit `9feb783` (README + tasks/todo.md Review) / `62aec7d` (`.gitignore` 에 `client_secret_*.json` 추가)
  - **Created**: wiki/analyses/multi-profile-cli-agent-isolation.md — CLI agent 멀티 프로필 격리 4대 함정 일반화. (1) OAuth 토큰 공유는 symlink (단순 복사·새 OAuth 모두 refresh-token 회전 충돌, lock 도 같이 symlink 하면 동시 refresh 직렬화) (2) Keychain 인증은 HOME 격리로 깨짐 → wrapper 가 HOME 만 복원 (`~/.claude` symlink 만으로는 부족) (3) hermes 등은 `.bashrc`/`.bash_profile` 만 source (`auto_source_bashrc`) → macOS zsh init 무시, cron / launchd / GitHub Actions 와 같은 비대화 셸 함정 패밀리 (4) `profile create --clone` 후 `.env` 의 Telegram bot token / API key 는 첫 setup 에서 reconfigure 필수
  - **Updated**: wiki/concepts/hermes-agent.md — 멀티 프로필 (`hermes profile` 명령 / 격리 디렉터리 / launchd 분리 plist / `--clone` 메모리 fresh) 섹션, 빌트인 `claude-code` skill 의 동작 (subprocess + ACP 미사용), ACP 지원 방향 비대칭 (server-only) 추가. confidence medium → high. multi-profile-cli-agent-isolation / hermes (project) 와 cross-link
- Skipped (dev-blog cron 자동 입력 — Linux Daily Newsletter Rewrite, JSON 출력 templated, dev-blog 파이프라인이 직접 받아 처리하므로 별도 인제스트 불요): 20260509-001334-015d, 20260509-001713-6cc4, 20260509-002321-504c, 20260509-003728-3549, 20260509-004240-96c7, 20260509-004756-55b3, 20260509-005319-3b67, 20260509-005725-84f6, 20260509-010205-6bcc, 20260509-010807-0814, 20260509-012359-8040, 20260509-014628-7071, 20260509-070015-71e8 (13건)
- Skipped (dev-blog cron 자동 입력 — Linux Kernel Weekly Digest, JSON 출력 templated, 7일치 일일 다이제스트 압축): 20260509-013212-cc6f, 20260509-013708-3abd, 20260509-014127-d241 (3건)
- Skipped (이전 maccoder 셋업 세션 직후의 sub-process 검증 호출 — 0~1 turn, 신규 지식 없음): 20260509-001352-0485 (`say hi`), 20260509-001423-38b6 (`say hi`), 20260509-004309-976f (`python3 -c "print(2+2)"` Bash 검증), 20260509-004343-06ab (`python --version` Bash 검증). 이 4건은 모두 maccoder 본 세션 (307a-*) 에서 hermes terminal tool 동작·HOME 격리 우회 검증으로 spawn 된 것이며 결론은 본 세션에 흡수됨
- raw-sources/ 의 신규 .md 없음 — Tips/ articles/ books/ ideas/ papers/ transcripts/ 의 모든 서브디렉터리에 PDF / .pptx / .txt 만 존재 (chunk MD 처리 대상 외). .cache/extracted/ 디렉터리 없음 (PDF 자동 추출 대상 없음)
- Updated: wiki/index.md (projects 에 hermes 1건, analyses 에 multi-profile-cli-agent-isolation 1건 추가, updated 타임스탬프), wiki/log.md, wiki/concepts/hermes-agent.md (sources / related / updated / 본문 3섹션 추가)
- Marked ingested: true — 21개 session-log 파일 전체 (skip 20건, 처리 1건 → 신규 페이지 2건 + 기존 페이지 갱신 1건)

## 2026-05-08T13:00 — wiki-ingest (session-logs, ingested: false 19건)

처리 4건 + 스킵 15건. 신규 페이지 11건 (bugs 1 / patterns 2 / analyses 6 / projects 1 + dict-get 사전 작성 1 갱신 없음). projects 갱신 2건 (ht-trading, japa-asset-dashboard).

- Source: session-logs/20260507-224943-690c-*.md (cwd: ht_trading, 익절 미발동 버그 분석 + 7 turns)
  - **Already exists**: wiki/bugs/dict-get-default-no-bootstrap.md — `dict.get(key, default)` 가 dict 미갱신으로 1개월간 트레일링 스톱 영구 비활성. setdefault 1줄 교체. 회귀 테스트 4 cases. (이미 5/8 사전 작성되어 있음, sources 일치 확인)
  - **Created**: wiki/analyses/partial-sell-rule-idempotency.md — 부분 매도 규칙 멱등성 패턴. 전량 매도 (자연 보호) vs 부분 매도 (직접 보호 필요) 구분, 3-step (once-flag dict + state 영속화 + 청산 시 정리). Rule 4 데드크로스 7 cycle 누적 발동 사례. 카운터형 모범 사례 (`_profit_take_done`). silent state-bug 탐지 (state 파일 / 로그 grep 교차 검증)
  - **Updated**: wiki/projects/ht-trading.md — 매도 규칙 버그 수정 3건 추가 (commit 60ba3a6 setdefault, commit 957cf8a limit_price + dead_cross once-flag), 회귀 테스트 8 cases, 변경 이력 신설. partial-sell-rule-idempotency 와 cross-link
- Source: session-logs/20260507-225855-4c7c-*.md (cwd: dev-blog, 신규 프로젝트 분석 + cron PATH/UTC 버그 발견)
  - **Created**: wiki/projects/dev-blog.md — AI 보조 한국어 엔지니어링 일일 뉴스레터. 5-단계 파이프라인 (collect → draft → rewrite → publish → build), AI 어댑터 경계 (`template`/`claude` fallback), Multi-topic 전제, cron-on-laptop + GitHub Actions 빌드 분리. [[kernel-digest]] 와 자매 프로젝트
  - **Created**: wiki/bugs/utc-iso-date-kst-rollover.md — `new Date().toISOString().slice(0, 10)` 가 KST 새벽~오전 (UTC 15:00~23:59 전날) 에 어제 날짜 반환. 매일 07:00 cron 이 어제 게시본 silent overwrite. `Intl.DateTimeFormat('en-CA', { timeZone: 'Asia/Seoul' })` 로 수정. 모든 Vercel/Actions/Lambda/Workers 의 한국 운영 cron 에 재현
  - **Created**: wiki/patterns/cron-nvm-node-path-trap.md — cron 이 `~/.zshrc` 안 읽어 NVM node 못 찾는 함정. crontab 상단 `PATH=` 명시 + 비교표 (cron / launchd / systemd / GitHub Actions). [[launchd-secret-management]] 와 같은 비대화 셸 함정 패밀리
  - **Created**: wiki/analyses/github-pages-base-path-pattern.md — `<id>.github.io` (root) vs 프로젝트 페이지 (`/<repo>`) 의 BASE_PATH 자동 대응. 빌드 시 prefix 주입 헬퍼 + GitHub Actions 자동 결정. Astro / Vite / Next.js / Hugo / Jekyll / Docusaurus 모두 동일 옵션
- Source: session-logs/20260507-230645-c555-*.md (cwd: japa, tasks/plan-2026-05-03 #1·#2·#3 처리 + 25 turns)
  - **Created**: wiki/patterns/react-hook-form-zod-server-action.md — RHF + zodResolver + server action 동일 스키마 패턴. resolvers v5 의 input/output 분리 → `useForm<z.input, unknown, z.output>` 3-제너릭 (TS2719 회피), FormData 재구성으로 server action 시그니처 보존, defense-in-depth (서버 검증 유지). [[zod-schema-per-entity]] 후속
  - **Created**: wiki/analyses/supabase-magic-link-single-user-allowlist.md — 자체 HMAC 비밀번호 → Supabase Auth 매직 링크 + `OWNER_EMAIL` allowlist. 4-Gate (UI / send-magic-link PRIMARY / Supabase OTP / callback SECONDARY) + email enumeration 방지 (generic 200) + 응답 시간 일정화 + cron 라우트 분리. Vercel env 자동 재배포 안 됨 / Site URL vs Redirect URLs 차이
  - **Updated**: wiki/projects/japa-asset-dashboard.md — #1 (commit 96a22e1, RHF 도입 +365/-186), #2 (commit e1bdb4a, Supabase Auth 교체), #3 (AI 키 암호화 보류 결정) 적용 완료. 변경 이력 신설. cross-link 2건
- Source: session-logs/20260507-235145-988a-*.md (cwd: infinite_loop/news, 3일치 뉴스 → 시장 전망 + 4 turns)
  - **Created**: wiki/analyses/news-driven-market-signal-framework.md — 다일자 뉴스 → 시그널 추출 프레임워크. 일자별 병렬 서브에이전트 분해 + 7-축 템플릿 (매크로/지정학/시장흐름/섹터/기업이벤트/수급심리/톤) + 3-시나리오 (확률 가중) + 섹터 비중 + 체크포인트. 뉴스 카테고리 → 영향 매핑 휴리스틱
  - **Created**: wiki/analyses/llm-news-prediction-pitfalls.md — LLM 시장 예측 6가지 함정 (검증 결여 / 확률 직관 / selection bias / stale 속도 / 자문 회색지대 / 단일 인과 추론). 결과는 영속화 부적절, 방법론·함정·매핑 휴리스틱만 영속화
- Skipped (Linux Daily Newsletter Rewrite cron 자동 입력 — `assistant_turns: 0`, dev-blog 의 `claude -p` 호출): 20260507-234605-c291, 20260507-234905-e1e1, 20260508-000741-f257, 20260508-005038-1664, 20260508-081629-022f, 20260508-081840-4a2e (6건). 본 cron 의 응답은 dev-blog 파이프라인이 직접 받아 처리하므로 별도 인제스트 불요. 이전 동일 패턴: research-wiki / oss-radar cron 의 분석 입력
- Skipped (cron heartbeat — `Reply with only: OK`, assistant_turns: 0): 20260508-080019-8442 (research-wiki), 20260508-090052-7b1a (oss-radar)
- Skipped (research-wiki cron 의 논문 분석 입력 — assistant_turns: 0): 20260508-080027-da2a, 20260508-080128-b612 (2건)
- Skipped (oss-radar cron 의 OSS 분석 입력 — assistant_turns: 0): 20260508-090059-6b91, 20260508-090132-49e4, 20260508-090208-19a7, 20260508-090241-6480, 20260508-090319-b7ec (5건)
- raw-sources/ 의 신규 .md 없음 — Tips/ articles/ books/ ideas/ papers/ transcripts/ 의 모든 서브디렉터리에 PDF / .pptx / .txt 만 존재. .cache/extracted/ 디렉터리 없음 (PDF chunk 처리 대상 외)
- Updated: wiki/index.md (projects/dev-blog 1건, patterns/cron-nvm-node-path-trap + react-hook-form-zod-server-action 2건, bugs/dict-get-default-no-bootstrap + utc-iso-date-kst-rollover 2건, analyses/partial-sell-rule-idempotency + github-pages-base-path-pattern + supabase-magic-link-single-user-allowlist + news-driven-market-signal-framework + llm-news-prediction-pitfalls 5건 추가, updated 타임스탬프), wiki/log.md
- Marked ingested: true — 19개 session-log 파일 전체 (skip 15건, 처리 4건 → 신규 페이지 10건 + 사전 작성 페이지 1건 cross-reference + 기존 페이지 갱신 2건)

## 2026-05-07T13:05 — wiki-ingest (session-logs, ingested: false 7건)

- 모두 cron 자동 호출 (research-wiki / oss-radar) 의 분석 입력 프롬프트 — `assistant_turns: 0`. 5건은 같은 날 별도 세션에서 이미 wiki/analyses/ 페이지로 처리되어 있었으나 source session 의 `ingested: false` 가 잔존. wiki/index.md 의 analyses 섹션에 미반영이던 5건을 신규 등재
- Source: session-logs/20260507-080029-d1f3-*.md (research-wiki cron, 논문 ESamp / arXiv 2604.24927)
  - **Already exists**: wiki/analyses/esamp-latent-distilling-exploration.md — Latent Distiller (RND 영감) + KL-regularized closed-form 재가중 + 비동기 파이프라인 < 5% 오버헤드. AIME25 Pass@64 등 Pass@k 효율 향상
- Source: session-logs/20260507-080128-c5a0-*.md (research-wiki cron, 논문 ARIS / arXiv 2605.03042)
  - **Already exists**: wiki/analyses/aris-autonomous-research-harness.md — cross-family executor/reviewer 강제 + 65+ Markdown 스킬 + 5 워크플로우 + 3단계 assurance. github.com/wanshuiyin/Auto-claude-code-research-in-sleep
- Source: session-logs/20260507-090100-b830-*.md (oss-radar cron, rasbt/LLMs-from-scratch)
  - Skipped — README 가 정규식 마스킹으로 인해 citation BibTeX 외 거의 전부 redacted (`https://***:***@...` 형태). 응답 미수집 + 추출 가능한 컨텐츠 부족 → wiki 페이지 미작성
- Source: session-logs/20260507-090136-e343-*.md (oss-radar cron, infiniflow/ragflow)
  - **Already exists**: wiki/analyses/ragflow-rag-engine.md — 79.8k stars, Apache-2.0, Python. Deep document understanding · template-based chunking · grounded citations · agentic + MCP. Elasticsearch ↔ Infinity 전환 가능
- Source: session-logs/20260507-090208-7524-*.md (oss-radar cron, koala73/worldmonitor)
  - **Already exists**: wiki/analyses/worldmonitor-global-intelligence-dashboard.md — 53.6k stars, AGPL-3.0, TypeScript/Tauri. AI 뉴스 어그리게이션 + 지정학 모니터링 + 인프라 추적 실시간 대시보드
- Source: session-logs/20260507-090243-d19f-*.md (oss-radar cron, ComposioHQ/awesome-claude-skills)
  - Skipped — README 의 contributor URL 이 거의 전부 마스킹 (`https://***:***@...` 형태) 으로 awesome list 의 큐레이트된 항목들이 추출 불가. 응답 미수집 + 의미 있는 컨텐츠 부재 → wiki 페이지 미작성
- Source: session-logs/20260507-090326-b169-*.md (oss-radar cron, D4Vinci/Scrapling)
  - **Already exists**: wiki/analyses/scrapling-adaptive-web-scraping.md (confidence: low) — BSD-3-Clause, Python. 다중 셀렉터 + DynamicFetcher + Async/Stealthy 세션 + HTTP/3 + element 관계 헬퍼
- raw-sources/ 의 신규 .md 없음 — Tips/ 서브디렉터리는 PDF 만 존재 (chunk MD 처리 대상 외). articles/ books/ ideas/ papers/ transcripts/ 모두 비어 있음. .cache/extracted/ 디렉터리 없음
- Updated: wiki/index.md (analyses 섹션에 esamp / aris / ragflow / scrapling / worldmonitor 5건 추가, updated 타임스탬프), wiki/log.md
- Marked ingested: true — 7개 session-log 파일 전체 (skip 2건: 마스킹된 redacted README, 처리 5건: 모두 사전 작성된 페이지로 cross-reference)

## 2026-05-07T13:00 — wiki-ingest (session-logs, ingested: false 4건)

- Source: session-logs/20260506-230405-a179-*.md (cwd: ht_trading, KIS 매수 차단 버그 수정 + 매수 커트라인 60→62 튜닝)
  - **Created**: wiki/bugs/kis-cash-d2-settlement-buy-rejection.md — `domestic.py:get_balance()` 의 `cash` 매핑이 `dnca_tot_amt` (D+0 출금가능) 만 사용 → 매도 직후 매수 사이클에서 RiskManager 가 "현금 부족" 차단. 한국 D+2 결제 시스템에서 `prvs_rcdl_excc_amt` (D+2 가수도정산금액) 가 매도 미정산 포함 매수가능 현금. `cash = max(deposit, settled_d2)` 로 매핑 + 모의투자 D+2=0 fallback. 신규 회귀 테스트 3건 (D+2 사용 / D+2=0 fallback / 동일값). 다운스트림 (`KISBroker.get_cash` → `_cached_cash` → `ctx.cash` → `RiskManager._validate_buy`) 동일 키라 변경 불필요. commit c6109f4
  - **Created**: wiki/analyses/kis-balance-api-fields.md — KIS Open API 잔고 응답 (`/uapi/domestic-stock/v1/trading/inquire-balance`, TR `TTTC8434R`) 의 5개 현금/잔고 필드 의미 비교표: `dnca_tot_amt` (D+0 예수금총액 = 출금가능) / `nxdy_excc_amt` (D+1 익일정산) / `prvs_rcdl_excc_amt` (D+2 가수도정산 = 매도 미정산 포함 매수가능) / `tot_evlu_amt` (총평가) / `evlu_pfls_smtl_amt` (평가손익). 한국 D+2 결제 사이클 + 매도 직후 매수 가능 정책. 별도 매수가능조회 API (`inquire-psbl-order`) 와 동일 로직. 안전한 매핑 패턴 (max + fallback). "예수금" 한국어 의미 충돌 함정
  - **Updated**: wiki/projects/ht-trading.md — sources / related 추가, 버그 수정 5번째 (D+2 정산 누락) 신설, 스코어링 임계값 60 → 62 (commit faa0518) 반영, 변경 이력 항목 추가
- Source: session-logs/20260507-004906-1f45-*.md + 20260507-015508-eb0c-*.md (cwd: openclaw/workspace, kernel-digest 신규 프로젝트 AGENTS.md 작성. 두 번째 세션은 첫 세션의 산출물 재확인이라 내용 동일)
  - **Created**: wiki/projects/kernel-digest.md — 리눅스 커널 일일 다이제스트 서비스. 4축 콘텐츠 (메인라인 패치 / 로드맵 / 버전 史 / 커뮤니티), 8 데이터 소스 (`git.kernel.org` / lore-LKML / LWN / KernelNewbies / Phoronix / Patchwork / kernel.org 공지 / 컨퍼런스), 4단계 파이프라인 (Collectors → Raw Store → AI Stage → Publisher → Web), 마일스톤 M0~M6 (현재 M0 완료). 정책: **종량제 API 금지** (`ANTHROPIC_API_KEY` 사용 금지) — 구독제 LLM (`claude -p` / `openclaw`) 만 허용 (월 비용 0원 목표). M1 진입 전 미결정 7건 (정적 사이트 도구 / 호스팅 / 저장소 구조 / 발행 시각 / LWN 구독 / 공유 범위 / LLM 라우팅). 토픽-플러그인 확장 (안드로이드 / AI 등)
- Source: session-logs/20260507-011504-7932-*.md (cwd: openclaw, 코더 응답 무 3계층 디버깅 + 카카오톡 미스라우팅 후속)
  - **Created**: wiki/bugs/openclaw-coder-silent-3-layer.md — 3개 독립 원인이 동시 발현 (1) `plugins.allow` 미설정으로 acpx runtime register 차단 (2) 12일 묵은 좀비 ACP task chicken-and-egg (3) **진짜 원인** Anthropic OAuth organization 차단 (HTTP 403) + 코더 fallback 0개로 silent 침묵. 가설 교체 2번 (좀비 task → plugins.allow → 인증). 회복: 모델 anthropic/claude-opus-4-6 → openai-codex/gpt-5.5. 후속 (Part 2): `/acp spawn` 후 응답이 카카오톡 "나와의 채팅" 으로 미스라우팅 (ACP wrapper 가 user-level Claude.ai connector 상속). 호명-only silent stop (모델-prompt 미스매치). 5가지 교훈
  - **Created**: wiki/analyses/openclaw-acp-runtime-internals.md — OpenClaw 2026.5.3 ACP runtime 의 4가지 함정: (1) `plugins.allow` 보안 정책 — non-bundled plugin 의 register 차단, `inspect=loaded` 와 실제 register 의 격차, plugin short id 매칭 / set 출력의 stale snapshot / bundled plugin 의 entries 등록 시 같이 죽는 위험 (2) 좀비 ACP task 의 chicken-and-egg (cancel/flow cancel/maintenance 모두 거부) → sqlite 직접 편집 절차 + status='running' 가드 + sqlite3 changes() 0 함정 + daemon restart 필수 (3) `sessions.json` 의 stale ACP binding (wrapper 종료 후에도 잔존, 새 메시지 처리 차단) → python 으로 'acp:binding' 키 제거 + restart (4) ACP wrapper 의 `--setting-sources=user,project,local --allow-dangerously-skip-permissions` 가 user-level Claude.ai connector 전부 상속 → 보안 위험 + 응답 미스라우팅. 4계층 디버깅 체크리스트
  - **Created**: wiki/analyses/mcp-config-secret-exposure-via-ps.md — Claude Code (그리고 ACP wrapper) 가 child claude binary 를 spawn 할 때 MCP 설정을 `--mcp-config` 옵션에 **인라인 JSON 평문**으로 전달 → `ps -ef` 의 command 컬럼에 NOTION_API_KEY 등 시크릿 지속 노출. macOS/Linux 기본 정책상 **다른 사용자도 보임**. 토큰 교체로 해결 안 됨 (재발 메커니즘). 노출 통로별 위험도 (다른 로컬 사용자 / 모니터링 에이전트 / 디버깅 캡처 외부 공유). 노출 방지 4옵션 (A upstream `--mcp-config-file` 추가 / B MCP 자체 제거 / C ACP 비활성 / D 환경 격리). single-user 환경의 실효 위험 평가
  - **Created**: wiki/decisions/openclaw-coder-default-model-codex.md — 코더 default 모델 결정: anthropic/claude-opus-4-6 → openai-codex/gpt-5.5. 이유: Anthropic OAuth organization 차단 + fallback 0개 silent 침묵 회피, main agent 와 인증 단일화, Anthropic 은 ACP 경유 사용을 권장하는 정책 시그널. `runtime.acp.agent: claude` 유지 (ACP 경로 보존). `openclaw models set --agent` 는 글로벌 only 라 사용 불가 — `agents.list[<idx>].model` 직접 편집 + daemon restart. 트레이드오프 (Opus 응답 톤 상실 / prompt 재조정 필요) + 후속 작업 (system prompt 재조정, ACP 격리, 만료 토큰 정리)
  - **Updated**: wiki/projects/openclaw.md — sources / related 추가, 코더 모델 표 갱신 (anthropic → codex + 정책 주석), "2026-05-07 코더 응답 무 사건 (3계층 디버깅)" 신설 (별도 페이지로 분리), 변경 이력 항목 추가
- raw-sources/ 의 신규 .md 없음 — Tips/ 서브디렉터리는 PDF 만 존재 (chunk MD 처리 대상 외). 그 외 articles/ books/ ideas/ papers/ transcripts/ 모두 비어 있음
- Updated: wiki/index.md (analyses/ 4건 / projects/ 1건 / bugs/ 2건 / decisions/ 1건 신규 추가, decisions 섹션 처음으로 활성화, updated 타임스탬프), wiki/log.md
- Marked ingested: true — 4개 session-log 파일 전체 (skip 0건, 처리 4건 → 신규 페이지 7건 + 기존 페이지 갱신 2건)

## 2026-05-06T10:00 — wiki-ingest (session-logs, ingested: false 9건)

- Skipped (cron heartbeat — `Reply with only: OK`, assistant_turns: 0):
  - 20260506-080028-ce80 (cwd: research-wiki)
  - 20260506-090050-9c0d (cwd: oss-radar)
- Skipped (research-wiki cron 의 논문 분석 입력 — 응답 미수집, assistant_turns: 0):
  - 20260506-080035-0d45 (MolmoAct2 — robotics action model)
  - 20260506-080147-f700 (Evoskill — automated skill discovery for multi-agent systems, arXiv 2603.02766)
- Skipped (oss-radar cron 의 OSS 분석 입력 5건 — 응답 미수집, assistant_turns: 0):
  - 20260506-090057-1b0d (forrestchang/andrej-karpathy-skills — Karpathy guidelines as Claude Code plugin/CLAUDE.md)
  - 20260506-090131-55a5 (bytedance/deer-flow — LangChain 기반 deep research / agent flow)
  - 20260506-090209-90d6 (daytonaio/daytona — secure dev sandbox SDK)
  - 20260506-090245-55e5 (jeecgboot/JeecgBoot — Apache-2.0 enterprise low-code 플랫폼)
  - 20260506-090335-a0d7 (OpenBB-finance/OpenBB — open-source 금융 데이터 플랫폼)
  - 같은 시간대에 oss-radar/research-wiki cron 양쪽이 동시에 응답 미수집 → 호스트의 일시적 cron 환경 이슈 가능성 (이전 동일 패턴: 2026-05-04T13:30, 2026-05-03T12:30, 2026-04-30T18:30)
- raw-sources/ 의 신규 .md 없음 (해당 서브디렉터리에는 PDF 만 존재), .cache/extracted/ 디렉터리 없음 — PDF 유래 chunk 처리 대상 외
- Updated: wiki/index.md (updated 타임스탬프), wiki/log.md
- Marked ingested: true — 9개 session-log 파일 전체 (skip 9건, 신규 페이지 0건)

## 2026-05-06T01:30 — wiki-ingest (session-logs, ingested: false 2건)

- Source: session-logs/20260505-232155-9172-*.md (cwd: kakao-mem, 기존 코드 분석 + 카카오 직접 통신 옵션 평가)
  - Project: kakao-mem (신규 wiki/projects/ 페이지)
  - **Created**: wiki/projects/kakao-mem.md — Python + `kakaocli` 어댑터 read-only CLI 구조 정리. 잘 된 점 5개 (어댑터 격리 / `derive_message_id` sha256(24) dedup / `_try_json_command` 후보 폴백 / 13 tests 0.003s 결정적 커버리지 / 프라이버시 가드레일). 잠재 이슈 7개 우선순위 정리 (`load_messages` JSONDecodeError raise, `Message(**item)` 미지키 폭주, 룸 ID sanitize 매핑 부재, `_parse_text_messages` `:` 분할 위험, `is_question` `"나요"` false positive, `_try_json_command` 마지막 에러만 보존, `config/local.toml` git 추적 vs README 모순). "카톡 앱 상시 가동" 의미 분해 (DB 읽기 시점 != 동기화 시점). 자매 프로젝트 [[kakao-db]] 와 옵션 분석으로 링크
  - **Created**: wiki/analyses/kakao-messaging-automation-options.md — 카카오톡 자동화 3 옵션 일반 비교. (1) 공식 Kakao Developers API: 단톡 읽기 불가. (2) 비공식 LOCO 클라이언트 (`node-kakao` / `PyKakao` / `agent-messenger`): 실시간 가능하지만 약관 위반 · 계정 정지 (페이/뱅크/T 연동 위험) · 키 유출 시 타인 로그인 가능 · 프로토콜 변동에 따른 유지보수 부업화 · 정통망법/개보법 회색지대. 별도 카카오 계정 운용이 업계 관행. (3) 하이브리드 (Mac 로컬 DB 읽기 + "나에게 보내기" 공식 송신): 약관 위반 0, PC 상시 가동 필요. 7행 비교표 + 시나리오별 추천 4가지
- Source: session-logs/20260505-235703-d859-*.md (cwd: kakao-db, gpters 글 기반 신규 Rust 프로젝트 시작)
  - Project: kakao-db (신규 wiki/projects/ 페이지)
  - **Created**: wiki/projects/kakao-db.md — Mac KakaoTalk 로컬 sqlcipher DB + LOCO 어댑터 Rust 프로젝트. 초기 5 결정 표 (Rust 단일 / OSS LOCO wrap / 단발 CLI + cron / macOS Keychain / App Store 26.3.0). 마일스톤 M0–M5 정의. M0 산출물 (Cargo.toml 의존성 `clap`/`anyhow`/`tracing`/`rusqlite-bundled-sqlcipher`/`chrono`/`walkdir`/`tempfile`, `cli/db/inspect` 모듈, 6/6 테스트 통과, `~/.zshrc` cargo env 1줄). M1 결정 표 B1-B4 (rusqlite + bundled-sqlcipher / 읽기 전용 사본 모드 / 키는 환경변수·Keychain 직접 주입 / 첫 서브커맨드 `inspect`). 운영 모드 = "보안·시스템 위험만 묻고 추천 옵션 자율 진행" (사용자 명시 0:23). 알려진 함정 (TCC 토스트 / `/fewer-permission-prompts` skill / DB 락 회피)
  - **Created**: wiki/analyses/kakaotalk-mac-data-locations.md — App Store v26.3.0 (`com.kakao.KakaoTalkMac`, TeamID `L75WVXX68A`) 의 메시지 sqlcipher DB 위치 일반 패턴. 본체는 `~/Library/Containers/com.kakao.KakaoTalkMac/Data/Library/Application Support/com.kakao.KakaoTalkMac/<80자 hex>` + `-shm` + `-wal` 3종 세트, 약 90MB. 80자 hex 무확장자 파일에 평문 SQLite 매직 부재 → 일반 `*.db`/`*.sqlite*` 글롭으로 안 잡힘. 식별 단서 = WAL 동반 + 큰 파일 크기 + `-wal` 자체에는 평문 매직 존재. `inspect` 휴리스틱 분류 `[magic]`/`[wal]`/`[ext]` 정의. 키 도출은 미해결 spike (App Store sandbox Keychain 의존 가능성)
  - **Created**: wiki/patterns/macos-tcc-full-disk-access.md — macOS Sonoma+ TCC 토스트 ("iTerm 이 다른 앱의 데이터에 접근하려고 합니다") 일반 처리 패턴. Claude Code 권한 팝업 vs macOS TCC 토스트 비교표 (출처/형태/단위/처리/영구적용). 한 번에 끄는 방법 6단계 (시스템 설정 → 개인정보 보호와 보안 → 전체 디스크 접근 → +iTerm.app → ON → **iTerm 완전 종료 후 재시작**). 권한 부여 없이 우회 (사용자 직접 cp / 매번 허용). 별개로 Claude Code 권한 팝업 줄이는 3가지 (Always allow / `/fewer-permission-prompts` skill / `/permissions`). 함정 4가지 (재시작 누락 / 자식 프로세스 별도 권한 / 권한 거부 후 부분 성공 silent / 두 종류 팝업 동시 등장)
- Updated: wiki/index.md (projects/ 에 kakao-mem, kakao-db 추가 / patterns/ 에 macos-tcc-full-disk-access 추가 / analyses/ 에 kakao-messaging-automation-options, kakaotalk-mac-data-locations 추가 / updated 타임스탬프), wiki/log.md
- Marked ingested: true — 2개 session-log 파일 전체 (skip 0건, 처리 2건 → 신규 페이지 5건)

## 2026-05-05T11:00 — wiki-ingest (session-logs, ingested: false 14건)

- Skipped (cron heartbeat — `Reply with only: OK`, assistant_turns: 0):
  - 20260505-080021-35e0 (cwd: research-wiki)
  - 20260505-090054-20e7 (cwd: oss-radar)
- Skipped (research-wiki cron 의 논문 분석 입력 — 응답 미수집, assistant_turns: 0):
  - 20260505-080027-b1fd (대규모 ML 인프라 / Poplar SDK / TensorFlow IPU 관련 논문 입력)
  - 20260505-080120-54c6 (VBench 기반 video temporal stability 평가 관련 논문 입력)
- Skipped (oss-radar cron 의 OSS 분석 입력 5건 — 응답 미수집, assistant_turns: 0):
  - 20260505-090101-7fe2, 20260505-090145-ef9f, 20260505-090220-941a, 20260505-090255-bb23, 20260505-090334-872f
- Skipped (탐색적 시행착오 — 결론 없이 종료):
  - 20260505-102319-121e (cwd: infinite_loop, "토큰 소비를 위한 1분 단위 작업 만들어 달라" — assistant 가 구체 작업 질의 후 종료. 신규 지식 없음)
- Skipped (vercel cron 디버깅 세션 — 결론은 이전 사이클에서 이미 흡수 완료):
  - 20260505-084952-fe4f (cwd: japa) — 6 commit 디버깅 끝에 `directUrl` 분리 + `prismaDirect` 도입 + `instrumentation.register` fire-and-forget. 결론과 일반 패턴은 이미 [[prisma-connection-pool-vercel-supabase]] / [[vercel-cron-best-practices]] / [[pgbouncer-direct-url-hybrid-routing]] / [[japa-asset-dashboard]] 의 이전 사이클 (2026-05-05 일자) 에 흡수됨. 신규 추출 없음
- Source: session-logs/20260505-101659-115c-*.md (cwd: finance-analysis-nextjs-backup, vercel 60s timeout 우회 commit 검토)
  - Project: finance-analysis-nextjs
  - **Created**: wiki/patterns/vercel-timeout-browser-direct-api.md — Vercel Hobby 60s lambda timeout escape hatch 일반 패턴. 인증 사용자에게 `ANTHROPIC_API_KEY` 를 `/api/anthropic-config` 로 내려주고 브라우저에서 `dangerouslyAllowBrowser: true` 로 SDK 직접 호출. 동작 원리 / 최소 구현 / 분기 전략 / 보안 부채 (DevTools 키 추출 / rate limit 통제 약화 / 시스템 프롬프트 노출 / 사용량 추적 어려움) / 회수 조건 + 키 회전 의무 / 정공법 대안 6종 비교표 / 함정 정리. confidence: medium
  - **Updated**: wiki/projects/finance-analysis-nextjs.md — sources / related 추가, "Vercel 60초 timeout 우회 — 임시 client-direct Anthropic 패턴 (2026-05-04 hotfix, 회수 대상)" 섹션 신설 (commit `215b9ff` 의 변경 파일 5개와 보안 부채 명시), 변경 이력 항목 추가
- Source: session-logs/20260505-103341-adb1-*.md + 20260505-104124-ad05-*.md (cwd: infinite_loop, news-digest 스킬 + scheduled-task 자동 실행)
  - **Created**: wiki/analyses/claude-code-scheduled-tasks.md — Claude Code 데스크톱의 scheduled-tasks 동작 모델. `~/.claude/scheduled-tasks/<name>/SKILL.md` 위치, `/loop` 와의 차이표 (동작 주체 / 등록 위치 / 수명 / 다음 실행 시점 / 실행 컨텍스트), 자동 실행 시 prompt 가 받는 `<scheduled-task>` 컨텍스트 ("사용자 부재" + "write 액션은 task file 명시 시에만"), SKILL.md 의 좋은 형식 (저장 경로/파일명 패턴 / 출력 형식 / 크기 제약 / 완료 신호), 운영 함정 (앱 종료 시 정지 / "Scheduled" 사이드바 미노출 버전 존재 / 권한 다이얼로그가 자동 실행 멈춤 → "Run now" 사전 승인 / 결과 파일 mtime 으로 동작 검증 / 디렉터리 제거로 정지 / 중복 호출 멱등성), 다른 자동화 수단 (LaunchAgent / Vercel Cron / GitHub Actions) 과의 trade-off 비교
- Updated: wiki/index.md (patterns/ 에 vercel-timeout-browser-direct-api 추가, analyses/ 에 claude-code-scheduled-tasks 추가, updated 타임스탬프), wiki/log.md
- Marked ingested: true — 14개 session-log 파일 전체 (skip 12건, 처리 3건 → 신규 페이지 2건 + 기존 페이지 갱신 1건)

## 2026-05-04T13:30 — wiki-ingest (session-logs, ingested: false 11건)

- Skipped (내용 없음 — 단순 ping, assistant_turns: 0):
  - 20260504-080029-0ab9 (cwd: research-wiki, `Reply with only: OK`)
  - 20260504-090051-a943 (cwd: oss-radar, `Reply with only: OK`)
- Skipped (research-wiki cron 의 논문 분석 프롬프트 / 응답 미수집, assistant_turns: 0):
  - 20260504-080042-eee3 (arXiv 2604.24300 ReVSI: Rebuilding Visual Spatial Intelligence Evaluation for Accurate Assessment of VLM 3D Reasoning) — 본문이 들어왔으나 응답이 세션 로그에 기록되지 않음
  - 20260504-080144-bada (arXiv 2604.22554 Video Analysis and Generation via a Semantic Progress Function, Tel Aviv U + SFU) — 동일 사유
- Skipped (oss-radar cron 의 OSS 분석 프롬프트 5개, 모두 assistant_turns: 0):
  - 20260504-090058-a1ca (puppeteer/puppeteer)
  - 20260504-090131-5a27 (spec-kit-extensions / spec-kit 확장 카탈로그)
  - 20260504-090209-64c6 (대상 미상 — 입력 본문 짧음)
  - 20260504-090251-5c5f (modelcontextprotocol/servers 또는 awesome-mcp 카탈로그류)
  - 20260504-090331-3151 (대상 미상)
  - 같은 시간대에 oss-radar/research-wiki cron 양쪽이 동시에 응답 미수집 → 호스트의 일시적 cron 환경 이슈 가능성. 이전 cron 실패 사례 (2026-04-30T18:30, 2026-05-03T12:30) 와 동일 패턴
- Skipped (탐색적 시행착오 — 결론 없이 세션 종료):
  - 20260504-121101-2aec (cwd: kakao-mem, "vercel DB 와 동일하게 로컬 연결" 질문) — 14개 bash 탐색 후 assistant_turns: 0 으로 종료. 탐색만으로 kakao-mem 은 Python CLI 이며 DB 의존 없음 (Postgres/Neon/Supabase/SQLite 모두 미발견) 이 드러났으나 LLM 결론 미생성. 새로운 설계 판단·버그·패턴 없음
  - 20260504-121656-61ee (cwd: finance-analysis-nextjs, 동일 질문) — Session Summary 미기록 상태로 중단. 탐색만으로 schema.prisma 가 `DATABASE_URL` + `DIRECT_DATABASE_URL` (이미 [[nextjs-vercel-supabase-deployment]] 에 정리됨) 임이 재확인됨. 신규 지식 없음
- Updated: wiki/index.md (updated 타임스탬프), wiki/log.md
- Marked ingested: true — 11개 session-log 파일 전체 (skip 11건, 신규 페이지 0건)

## 2026-05-03T12:30 — wiki-ingest (session-logs, ingested: false 10건)

- Skipped (내용 없음 — 단순 ping, assistant_turns: 0):
  - 20260503-080020-bda1 (cwd: research-wiki, `Reply with only: OK`)
  - 20260503-090052-f7b6 (cwd: oss-radar, `Reply with only: OK`)
- Skipped (논문 분석 입력만 / 응답 미수집, assistant_turns: 0):
  - 20260503-080027-8bf0 (arXiv 2604.24763 Tuna-2: Pixel Embeddings Beat Vision Encoders) — 본문이 들어왔으나 응답이 세션 로그에 기록되지 않음
  - 20260503-080129-3506 (arXiv 2604.26067 RADIO-ViPE: Open-Vocabulary Semantic SLAM in Dynamic Environments) — 동일 사유
- Skipped (oss-radar 자동 발행물 입력 — 본 LLM Wiki 의 wiki/analyses/ 에는 추가하지 않음, 모두 assistant_turns: 0):
  - 20260503-090102-a146 (jwasham/coding-interview-university)
  - 20260503-090139-8e4e (Gar-b-age/CodeFormer)
  - 20260503-090229-eb25 (kamranahmedse/developer-roadmap)
  - 20260503-090304-0867 (hacksider/Deep-Live-Cam)
  - 20260503-090339-5fb3 (firecrawl/firecrawl)
- Source: session-logs/20260503-100914-b80f-*.md (cwd: japa / japa-s 비교 + Zod 스키마 통합 + Gemini 모델 hotfix + 멀티 LLM provider 도입)
  - Project: japa-asset-dashboard
  - **Updated**: wiki/projects/japa-asset-dashboard.md — sources 추가, "`git/wk/japa-s` 와의 비교 — 차용 / 미차용 결정" / "Zod 스키마 entity별 분리 적용" / "Gemini 2.0-flash → 2.5-flash + GEMINI_MODEL env override" / "AI 분석 결과 DB 저장 + 멀티 LLM provider 도입" / "Zod-schema 정리 후 폼 즉시 검증 (백로그)" 섹션 신설. 4개 entity 의 server action 인라인 Zod 를 `lib/<entity>/schema.ts` 로 분리해 enum 3중 중복 제거 (순감 80줄, Currency/AccountType/AssetClass 모두 Prisma SSOT). `AiAnalysis` Prisma 모델 + `provider` 컬럼으로 분석 결과 영속화. 단일 Gemini 코드 → `lib/ai/providers/{gemini,openai,anthropic}.ts` 어댑터 (공식 SDK 직접 사용, 멀티 LLM 추상화 라이브러리 미도입)
  - **Created**: wiki/analyses/multi-llm-provider-adapter-pattern.md — LangChain / Vercel AI SDK 같은 추상화 레이어 없이 OpenAI / Anthropic / Gemini 공식 SDK 를 어댑터 뒤에 두는 경량 패턴. 디렉터리 구조 (`lib/ai/{types,context,index}.ts` + `providers/<vendor>.ts`), 환경변수 / 모델 선택 정책 (`<VENDOR>_MODEL` env override), 라이브러리를 안 쓰는 이유 비교표, provider 별 SDK 인터페이스 차이 흡수 항목 (메서드 / system 프롬프트 / 응답 본문 / streaming / token usage), 키 저장 단계 (env-only → DB AES-256-GCM 암호화), 본 패턴이 적합한 시나리오 정리
  - **Created**: wiki/patterns/zod-schema-per-entity.md — `lib/<entity>/schema.ts` 에 enum 배열 / label map / Zod 폼 스키마 / 추론 타입을 응집시키는 SSOT 패턴. 안티 패턴 (분산형 — Prisma + labels.ts + server action 인라인 3중 분산) 과 통합형 비교, 도입 단계 (시범 entity 1개 → 확산 → 폼 즉시 검증), 자주 부딪히는 함정 (`Record<EnumType, string>` 노출 시 호출부 깨짐 → `Record<string, string>` 로 절충 / `z.nativeEnum` SSOT / FormData 정규화 / `as const` literal union)
  - **Created**: wiki/bugs/gemini-2-0-flash-free-tier-blocked.md — 2026 봄 시점 `gemini-2.0-flash` 가 free tier 에서 사실상 차단 (429 + `limit: 0`). 증상 단서로 `limit: 0` 의 의미 (모델 자체 한도 0, retry 로 회복 안 됨), `gemini-2.5-flash` 로 한 줄 교체 + `GEMINI_MODEL` env override 추가. 모델별 free tier 한도 표 (2.5-flash / 2.5-flash-lite / 2.5-pro / 2.0-flash 차단), 429 디버깅 분기 (`limit: 0` vs `limit: N` vs API key invalid)
- Updated: wiki/index.md (patterns/ 에 zod-schema-per-entity 추가, bugs/ 에 gemini-2-0-flash-free-tier-blocked 추가, analyses/ 에 multi-llm-provider-adapter-pattern 추가)
- Marked ingested: true — 10개 session-log 파일 전체 (skip 9건, 처리 1건)

## 2026-05-03T00:45 — wiki-ingest (session-logs, ingested: false 1건)

- Source: session-logs/20260502-235145-8714-*.md (cwd: wardrobe / 신규 옷장 매칭 앱 시작)
  - Project: wardrobe (~/project/git/wk/wardrobe — 신규 빈 디렉터리에서 시작)
  - **Created**: wiki/projects/wardrobe.md — Next.js 15 + Tailwind v4 + LocalStorage MVP 구성, 4개 라우트 (대시보드/옷장/추천/저장), `ClothingItem`/`Outfit`/`MatchingContext` 도메인 모델, 사이드바 (`usePathname` active 표시) + ItemCard + WardrobeView + RecommendForm 컴포넌트, forest green + Playfair Display 디자인 토큰. CLAUDE.md 충돌 회피용 수동 스캐폴딩 11파일의 분류 (필수 5 / Tailwind 2 / Git 1 / 문서 2 / 선택 1), 시드 22개 → 10개 변경 시 CLAUDE.md 의 22개 언급 모두 정리 (사용자 지적 반영). 커밋 2건 (`c1fb5de` scaffold, `94fab63` dashboard UI)
  - **Created**: wiki/analyses/web-app-storage-without-db.md — "DB 없이 사진을 보고 싶다" 의 분리. 데이터 저장 4옵션 (시드 JSON / LocalStorage / IndexedDB / Cookie) × 이미지 저장 4옵션 (public 정적 / base64+LocalStorage / IndexedDB Blob / 외부 호스팅), 단계적 도입 패턴 (Step 1 LocalStorage → Step 2 IndexedDB → Step 3 외부 Blob+Postgres). LocalStorage 의 base64 이미지 한계 (20–30장), `URL.createObjectURL` 메모리 누수, `<img src>` 4가지 형식. MVP 단계의 DB 조기 도입 안티 패턴
  - **Created**: wiki/analyses/vercel-friendly-database-options.md — Step 3 진입 시 후보 4개 비교. Neon (관계형 1순위, Vercel 마켓플레이스 일급, Branching, HTTP serverless driver 로 connection pool 함정 회피) / Vercel KV (Upstash Redis, 캐시·카운터 전용) / Supabase (Postgres+Auth+Storage+Realtime 묶음) / Turso (libSQL, edge 분산). serverless 환경의 connection pool 함정과 옵션별 회피 메커니즘 정리, 도입 의사결정 트리, 무료 티어 비교 (2026-05 기준, confidence: medium)
- Updated: wiki/index.md (projects/ 에 wardrobe 추가, analyses/ 에 web-app-storage-without-db / vercel-friendly-database-options 2개 추가)
- Marked ingested: true — 1개 session-log 파일

## 2026-05-02T11:00 — wiki-ingest (session-logs, ingested: false 11건)

- Skipped (내용 없음 — 단순 ping):
  - 20260502-080035-bff8, 20260502-090049-1969 (모두 `Reply with only: OK` health-check, assistant_turns: 0)
- Skipped (논문 분석 입력만 / 응답 미수집, assistant_turns: 0):
  - 20260502-080041-a662 (arXiv 2604.27351 EywaAgent — Heterogeneous Scientific Foundation Model Collaboration) — 본문이 들어왔으나 응답이 세션 로그에 기록되지 않음
  - 20260502-080150-8887 (arXiv 2604.28185 Visual Generation in the New Era — Atomic Mapping → Agentic World Modeling) — 동일 사유. 인접 주제는 [[agentic-world-modeling-taxonomy]] 가 이미 존재
- Skipped (oss-radar 자동 발행물 입력 — 본 LLM Wiki 의 wiki/analyses/ 에는 추가하지 않음):
  - 20260502-090055-8c94 (open-webui/open-webui), 20260502-090134-6028 (NousResearch/hermes-agent), 20260502-090214-be27 (excalidraw/excalidraw), 20260502-090243-d6a2 (Comfy-Org/ComfyUI), 20260502-090321-7ab6 (google-gemini/gemini-cli) — 모두 oss-radar 의 README 분석 자동 워크플로우 산출물. assistant_turns: 0 으로 응답도 미수집
  - (메모) hermes-agent README 의 입력은 사용자가 별도 세션 (092045) 에서 수동 조사를 진행했고, 그 결과를 별도로 ingest 함
- Source: session-logs/20260502-092045-628d-*.md (cwd: hermes / Hermes Agent 조사)
  - Project: hermes (toy/hermes — 빈 디렉터리, 설치 전 단계의 사전 조사)
  - **Created**: wiki/concepts/hermes-agent.md — Nous Research 의 self-hosted personal AI agent 의 컨셉 / 기능 / 시스템 요구사항 / 설치 / LLM 백엔드 / 메신저 게이트웨이 의의 정리. Claude Code / Cursor 와의 위치 짓기 (persistent memory + 멀티 채널 + skill 자가생성 의 차별점)
  - **Created**: wiki/analyses/anthropic-oauth-third-party-billing-trap.md — Hermes 조사 중에 드러난 함정. Anthropic OAuth 가 third-party 클라이언트 요청을 별도 `extra_usage` pool 로 라우팅 → Pro 불가, Max + credits 필수, Sonnet/Opus 는 429, Haiku 만 안정. 권장 패턴 (API key 직발급 / OpenRouter 우회). 같은 부류 도구를 만났을 때의 점검 항목 (issue tracker 검색 키워드 등)
  - **Created**: wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md — 사용자의 "OpenRouter 가 Ollama 같은 건가요?" 질문에서 도출. OpenRouter (클라우드 중계 / aggregator) vs Ollama (로컬 inference 런타임) vs Hugging Face (모델 허브 + Inference) 의 3가지 분류. 같은 차원의 선택지가 아님을 강조. 시나리오별 권장 / third-party agent 의 provider 옵션 매핑
  - **Created**: wiki/analyses/personal-ai-agent-messaging-channels.md — Telegram 이 self-hosted personal AI agent 의 사실상 표준이 된 6가지 이유 (BotFather 30초 토큰 / 1:1 DM / 음성 / 그룹 / 안정 무료 API / 모바일 우선). 한국 카톡은 봇 API 제약으로 OSS 표준 경로가 아님. [[openclaw-telegram-group-setup]] 과 상호 링크
- Source: session-logs/20260502-095014-6859-*.md (cwd: japa / toy/japa 비교 + 사이드바 + 종목 자동 판별 + 60초 쿨다운)
  - Project: japa-asset-dashboard
  - **Updated**: wiki/projects/japa-asset-dashboard.md — sources 추가, "사이드바 네비게이션 + 모바일 드로어" / "종목 자동 판별 (KOSPI/KOSDAQ 6자리 코드)" / "수동 시세 갱신 60초 쿨다운" / "toy/japa 와의 비교 — 차용 / 미차용 결정" / "배당 내역 기록 — Dividend 모델 (백로그)" / "계좌 그룹 (N:M, 백로그)" 섹션 신설. 도메인 패턴 메모 3건 (자동 채움은 빈 필드만 / 쿨다운은 click-path 만 / 예측치와 실측치 분리)
  - 사용자 지적으로 정정한 사건이 있어서 기록: 처음 답변에서 "toy 의 5분 쿨다운" 을 차용 가치로 ⭐ 매겼으나, 실제 코드 확인 결과 toy 도 코드엔 쿨다운이 없고 설계 문서에만 있음. **본 프로젝트는 신규로 60초 쿨다운 도입**. rate limit 방어 측면에서 toy (직렬+100ms) 보다 본 프로젝트 (worker pool 6 + 250ms 1회 재시도) 가 견고함이 확인됨
- Updated: wiki/index.md (concepts/ 에 hermes-agent 추가, analyses/ 에 anthropic-oauth-third-party-billing-trap / llm-provider-aggregator-vs-local-vs-hub / personal-ai-agent-messaging-channels 3개 추가)
- Marked ingested: true — 11개 session-log 파일 전체 (skip 9건, 처리 2건)

## 2026-05-02T08:00 — wiki-ingest (session-logs, ingested: false 2건)

- Source: session-logs/20260501-213505-aecb-*.md (cwd: japa / Vercel P2024 시세 새로고침 사고)
  - Project: japa-asset-dashboard
  - **Created**: wiki/bugs/prisma-connection-pool-vercel-supabase.md — `connection_limit=1` 환경에서 8개 `$transaction` 병렬화로 P2024 풀 고갈 + 같은 인스턴스 후속 요청 연쇄 실패. fix: 불변 시계열은 `createMany({ skipDuplicates: true })` + 오늘 한 행만 upsert, 무거운 쓰기는 click-path 에서 일별 cron 으로 분리. 일반화 신호 (Vercel + connection_limit=1 + 트랜잭션 병렬화 + 불변 데이터) 정리.
  - **Created**: wiki/patterns/vercel-cron-best-practices.md — Vercel Cron 5결정 종합. (1) Hobby 슬롯 제약 (≤2, 일1회) → 단일 `/api/cron/daily` + KST 날짜 분기 (월1일 / 1월1일) 패턴. (2) CRON_SECRET — 공개 cron URL 보호. Vercel 이 자동으로 `Authorization: Bearer` 헤더 첨부, 라우트는 헤더 비교만. (3) Next.js middleware 가 cron 호출을 `/login` 으로 redirect 하지 않게 matcher 에서 `api/cron` 제외. (4) 멱등성 — `createMany skipDuplicates` / `updateMany` / 비멱등 작업엔 24h guard. (5) Crons UI / Logs / 수동 Run / Notifications 운영.
  - **Updated**: wiki/projects/japa-asset-dashboard.md — sources 추가, "시세 새로고침 P2024 사고와 cron 통합" / "세테크 계좌 납입한도 평가액 분리 (`Account.contributionYTD` 컬럼)" / "WTI 원유 인덱스" / "CSV 내보내기 (UTF-8 BOM)" / "Prisma migrate deploy 빌드 통합" / "Yahoo quote 와 한국 종목 시간외 거래" 섹션 신설.
  - **Updated**: wiki/analyses/nextjs-vercel-supabase-deployment.md — sources 추가, `connection_limit=1` 의 인접 함정 (무거운 트랜잭션 병렬화) 섹션 신설, prisma migrate deploy 빌드 통합 운영 점검 사항 보강 (DIRECT_URL 환경 체크 / 빌드 실패가 안전망 / expand-and-contract 주의).
- Source: session-logs/20260501-233118-b6e0-*.md (cwd: finance-analysis-nextjs / 기능 제안 → DB 데이터 정리 검토)
  - Project: finance-analysis-nextjs
  - **Updated**: wiki/projects/finance-analysis-nextjs.md — sources 추가, "운영 측 약점 (analyses 누적 / DELETE 경로 부재 / financial_statements 미사용 / provider 하드코딩)" 섹션 신설, 백로그 4개 추가. PDF 내보내기 (`pdf-generator.ts` + `html2canvas-pro` + `jspdf`) 가 이미 구현되어 있음을 확인 — Claude 가 코드 검증 전에 PDF 기능을 신규 후보로 제안했다가 사용자 지적으로 정정한 사건이 있어 이번 갱신에서는 "기존 구현 확인됨" 을 명시.
- Updated: wiki/index.md (patterns/ 에 vercel-cron-best-practices 추가, bugs/ 에 prisma-connection-pool-vercel-supabase 추가)
- Marked ingested: true — 2개 session-log 파일

## 2026-05-01T13:00 — wiki-ingest (session-logs, ingested: false 26건)

- Skipped (내용 없음 — 단순 ping):
  - 20260430-141817-baa6, 20260430-144026-977a, 20260501-080100-ae82, 20260501-090051-be5b (모두 `Reply with only: OK`)
- Skipped (논문 분석 미완료, assistant_turns: 0):
  - 20260501-080108-f48b (arXiv 2604.24819 ProDa: Programming with Data) — 본문 길이가 컨텍스트 한계를 넘어 응답 생성되지 않음
  - 20260501-080209-9b29 (arXiv 2604.26752 GLM-5V-Turbo) — 동일 사유로 응답 없음
- Skipped (oss-radar 자동 발행물 — 본 LLM Wiki 의 wiki/analyses/ 에는 추가하지 않음):
  - 20260430-141825-b1dc, 20260430-141857-8b24, 20260430-141931-b081, 20260430-142011-af92, 20260430-142048-7399 (CJackHwang/ds2api), 20260430-144033-2641 (badlogic/pi-mono), 20260430-144111-c80d, 20260430-144143-2c7c, 20260430-144218-615f, 20260430-144247-00cb (everything-claude-code — 이미 wiki/analyses 에 페이지 존재), 20260501-090057-57ff, 20260501-090135-2511, 20260501-090209-cd7c (microsoft/generative-ai-for-beginners), 20260501-090243-4133, 20260501-090320-90d0 (langchain-ai/langchain) — 모두 oss-radar 의 README 분석 자동 워크플로우 산출물로, oss-radar 의 GitHub Wiki 에 발행되는 정형 리포트
- Source: session-logs/20260430-134759-328e-*.md (cwd: oss-radar / GITHUB_TOKEN 401)
  - Project: oss-radar
  - 이미 처리됨: wiki/projects/oss-radar.md, wiki/patterns/launchd-secret-management.md (sources 에 본 세션 포함)
  - 추가 변경 없음 (PAT 401 진단 + plist → config/.env 분리 + fine-grained PAT 권장 + launchd 가 ~/.zshrc 안 읽음 모두 이미 정리됨)
- Source: session-logs/20260430-135011-e8eb-*.md (cwd: japa / disk size + Vercel 배포)
  - Project: japa-asset-dashboard
  - 이미 처리됨: wiki/projects/japa-asset-dashboard.md, wiki/analyses/nextjs-vercel-supabase-deployment.md (sources 에 본 세션 포함)
  - 추가 변경 없음 (Vercel-GitHub App + Webhook + Hobby private repo + Pooler 모드 + force-dynamic + Import .env 모두 이미 정리됨)
- Source: session-logs/20260430-161410-0fcc-*.md (cwd: japa / 4가지 개선 + 인증 + Yahoo silent fail)
  - Project: japa-asset-dashboard
  - 이미 처리됨: wiki/projects/japa-asset-dashboard.md, wiki/bugs/yahoo-finance-concurrent-silent-fail.md (sources 에 본 세션 포함)
  - 추가 변경 없음 (HMAC-SHA256 단일 사용자 인증 + 시세 새로고침 worker pool 6 + 1회 retry + 수익률 % 표시 모두 이미 정리됨)
- Source: session-logs/20260430-171050-9ee3-*.md (cwd: openclaw / acp list 확인)
  - Project: openclaw
  - Skipped: 결론 없는 1턴 짜리 세션 (`apc` 가 `acp` 의 오타임을 확인하고 종료, 새로운 설계 판단·버그·패턴 없음)
- Source: session-logs/20260430-174408-1a2e-*.md (cwd: openclaw workspace / finance-analysis-nextjs 분석)
  - Project: finance-analysis-nextjs
  - 이미 처리됨: wiki/projects/finance-analysis-nextjs.md, wiki/analyses/ai-valuation-trustworthiness.md (sources 에 본 세션 포함)
  - **Created**: wiki/analyses/pdf-text-extraction-vs-ocr.md — ai-valuation-trustworthiness 와 finance-analysis-nextjs 가 모두 link 하고 있었으나 페이지가 누락 상태였음. 디지털 PDF vs 스캔 PDF vs 하이브리드 PDF 의 구분, 빈 텍스트 침묵 실패 차단, OCR 엔진 비교 (Tesseract / Google Vision / Upstage / Azure / Textract), 트렁케이션 시 재무제표 페이지 선별, 연결/별도 재무제표 구분 metadata 패턴 정리.
- Updated: wiki/index.md (analyses/ 섹션에 pdf-text-extraction-vs-ocr 추가), wiki/log.md (본 항목)
- Marked ingested: true — 26개 session-log 파일 전체

## 2026-04-22 — wiki-ingest (session-log)

- Project: gieok
- Source: session-logs/20260422-002046-60a1-왜-7-00-인가요--매일-오전-7-00-에-동작하는-건가요.md
- Created:
  - wiki/concepts/gieok.md — gieok 개념 (4계층 아키텍처, Hook 등록 방식, gieok vs Obsidian)
  - wiki/projects/gieok.md — gieok 프로젝트 상세 (컴포넌트, LaunchAgent 스케줄, LLM 필요 여부, 버그 이력)
  - wiki/analyses/macos-launchagent-catchup-behavior.md — macOS LaunchAgent 캐치업 동작 분석
- Updated: wiki/index.md, wiki/log.md
- Skipped: 탐색적 QA 대화, 설치 과정 시행착오 (결론만 추출)

## 2026-04-23 — wiki-ingest (session-logs, ingested: false 1건)

- Source: session-logs/20260422-230939-22f1-스코어링-점수를-65-점에서-60-점으로-조정했는지-확인해-주세요.md
  - Project: ht_trading
  - Created: wiki/projects/ht-trading.md
    — ScoringStrategy 이중 스케일 구조(백테스트 40pt / 라이브 100pt), config 2파일 동기화 규칙, 임계값 65→60 조정 이력
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 1개 session-log 파일

## 2026-04-23T13:00 — wiki-ingest (session-logs, ingested: false 6건)

- Source: session-logs/20260423-080035-4bb7-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260423-080041-976a-*.md (arXiv: 2604.19748)
  - Created: wiki/analyses/tstars-tryon-virtual-try-on.md
    — Taobao 상업용 가상 피팅 시스템: MMDiT 5B, 8카테고리, 단일 3.92s/멀티 6.74s, RL(DiffusionNFT)
- Source: session-logs/20260423-080131-e2e6-*.md (arXiv: 2604.18486)
  - Created: wiki/analyses/onevl-latent-reasoning.md
    — Xiaomi 자율주행 VLA: 잠재 CoT로 명시적 CoT 정확도 + 답변-only 지연시간 동시 달성
- Source: session-logs/20260423-120308-f269-*.md (ht_trading)
  - Updated: wiki/projects/ht-trading.md
    — split throttle G4-1 드로다운 버그 수정 기록 추가 (bar.close → pos.current_price, commit c5dc818)
- Source: session-logs/20260423-113736-72aa-*.md (openclaw)
  - Created: wiki/projects/openclaw.md
    — OpenClaw 업데이트(2026.3.28→2026.4.21), git 관리 구조, 이미지 생성 별도 API 키 필요
- Source: session-logs/20260423-125016-fe2e-*.md (gieok)
  - Skipped: 짧은 탐색적 세션 (gieok 세션 컨텍스트 주입 확인), 새로운 설계 판단 없음
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 6개 session-log 파일 전체

## 2026-04-23T13:30 — wiki-ingest (session-logs, ingested: false 1건)

- Source: session-logs/20260423-130102-15da-지금-wiki에-어떤-페이지가-있나요.md
  - Project: gieok
  - Skipped: 단순 wiki 페이지 조회 세션 (vault 경로 확인 외 설계 판단·버그·패턴 없음)
- Updated: wiki/index.md (기존에 누락된 concepts 6건, patterns 9건, projects 1건, analyses 1건 일괄 추가)
- Marked ingested: true — 1개 session-log 파일

## 2026-04-23T21:15 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260423-192900-e7ec-Reply-with-exactly--coder-session-alive.md
  - Project: openclaw
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 ping 프롬프트)

- Source: session-logs/20260423-193125-b999-graphify.md
  - Project: ht_trading
  - Updated: wiki/projects/ht-trading.md
    - graphify 아키텍처 분석 추가 (god nodes: Bar 176, Market 174, 87 communities)
    - 버그 수정 3건 추가:
      - Rule 6 백테스트 `datetime.now()` → `bar.dt` (commit 803a158)
      - RSI 평탄 구간 100 → 50 반환 (commit 803a158)
      - TrailingSell `bar.close` → `pos.current_price` (commit f376ba8)
    - 개선 백로그 B1-B4 추가

- Source: session-logs/20260423-194609-6b61-코딩전용-openclaw-agent-를-추가했는데-텔레그램으로-메세지를-보내면-응답이-없습.md
  - Project: openclaw
  - Updated: wiki/projects/openclaw.md
    - 다중 에이전트 구성 (main/english/coder) 섹션 추가
    - Telegram 그룹 설정 (requireMention, Privacy Mode) 섹션 추가
    - 라우팅 버그 트러블슈팅 섹션 추가
  - Created: wiki/analyses/openclaw-telegram-group-setup.md
    - Telegram Bot Privacy Mode 비활성화 필요성
    - requireMention 그룹/토픽 레벨 차이
    - 포럼 토픽 vs 별도 봇 트레이드오프
    - OpenClaw bindings에 `"type": "acp"` 포함 시 라우팅 0 버그

- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체 (skip 1건, 처리 2건)

## 2026-04-24T09:00 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260424-080017-211f-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" health-check 프롬프트)
- Source: session-logs/20260424-080022-8403-*.md (arXiv: 2604.20796)
  - Created: wiki/analyses/llada2-uni-unified-multimodal-diffusion.md
    — LLaDA2.0-Uni: SigLIP-VQ(의미론적 이산 토크나이저) + 16B MoE dLLM + 확산 디코더. 이해·생성·편집을 단일 마스크 예측 목표로 통합. SPRINT 추론 1.6× 가속
- Source: session-logs/20260424-080120-625e-*.md (arXiv: 2604.15825)
  - Created: wiki/analyses/algorithmic-collusion-reinforcement-learning.md
    — Soft Actor-Critic 기반 가격 게임에서 담합 수렴: Q-learning 대비 100배 빠름 (~5만 스텝 ≈ 5년), 반독점 규제 시사점
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체 (skip 1건, 생성 2건)

## 2026-04-25T09:00 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260425-080017-8e34-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" health-check)
- Source: session-logs/20260425-080021-dbd0-*.md (arXiv: 2604.13346)
  - Created: wiki/analyses/agentspex-agent-specification-language.md
    — AgentSPEX: YAML 선언형 에이전트 워크플로우 명세 언어. LangGraph 대비 모델 버전 변경 시 -0.2% 유지(vs Live-SWE-agent -6.8%). 형식 검증(formal verification) 가능.
- Source: session-logs/20260425-080113-edb6-*.md (arXiv: 2604.21061)
  - Created: wiki/analyses/invitrovision-embryo-vlm.md
    — PaliGemma-2 LoRA 적응: 1,000개 IVF 배아 이미지로 ChatGPT 5.2 능가(총점 0.67 vs 0.52). 임상 맞춤 복합 평가 메트릭(ECC/MD/PD 가중치) 제안.
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체 (skip 1건, 생성 2건)

## 2026-04-26T13:00 — wiki-ingest (session-logs, ingested: false 6건)

- Source: session-logs/20260426-080019-1991-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" health-check)
- Source: session-logs/20260426-080023-cb0a-*.md (arXiv: 2604.19636)
  - Created: wiki/analyses/cointeract-hoi-video-synthesis.md
    — CoInteract: Human-Aware MoE + 비대칭 Co-Attention 2단계 학습으로 손-물체 교합 제거, 추론 시 HOI 브랜치 제거(zero overhead). VLM-QA 0.72, HQ 0.724로 기존 방법 능가
- Source: session-logs/20260426-080121-2ff3-*.md (arXiv: 2604.17295)
  - Created: wiki/analyses/llatisa-time-series-reasoning.md
    — LLaTiSA: 4단계 인지 계층(L1-L4) TSR 분류, 83K HITSR 데이터셋, 시각화+수치 테이블 융합 VLM, 3단계 커리큘럼 파인튜닝
- Source: session-logs/20260426-111623-cfe2-*.md (ht_trading)
  - Updated: wiki/projects/ht-trading.md
    - 리스크 매니저 정책(max_positions:5, max_position_pct:30%, _validate_buy 5조건) 추가
    - 2026-04-24 리스크 거부 사례 분석: HD현대일렉트릭·수산인더스트리 매수 거부 원인이 5/5 포지션 포화
    - 구조적 문제: rotation 로직 부재 → 고점수 신호도 진입 불가
- Source: session-logs/20260426-120703-304f-*.md (openclaw / asset-dashboard)
  - Updated: wiki/projects/openclaw.md
    - asset-dashboard git 분리 구조 (workspace-coder/.gitignore, 독립 repo 초기화)
    - workspace-state.json untrack 처리
    - asset-dashboard Phase 1 완료·Phase 2 미시작 현황
- Source: session-logs/20260426-121630-14c3-*.md (openclaw / ACP 설정)
  - Updated: wiki/projects/openclaw.md
    - acpx 플러그인 permissionMode 스키마 제약 (approve-all/approve-reads/deny-all만 허용, "auto" 없음)
    - nonInteractivePermissions 스키마: deny/fail만 허용 ("allow" 없음) → 자율 실행 불가
    - openclaw config set 명령으로 설정 변경 방법
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 6개 session-log 파일 (skip 1건, 생성 2건, 수정 2건)

## 2026-04-27T09:00 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260427-080020-5805-Reply-with-only--OK.md
  - Skipped: 내용 없음 (단순 "Reply with only: OK" 프롬프트, assistant_turns: 1, 유의미한 내용 없음)

- Source: session-logs/20260427-080024-03ac-*.md (arXiv: 2604.18292)
  - Paper: Agent-World: Scaling Real-World Environment Synthesis for Evolving General Agent Intelligence
  - Institution: Renmin University of China + ByteDance Seed
  - Created: wiki/analyses/agent-world-environment-synthesis.md
    - 자기 진화형 에이전트 훈련 아레나: Agentic Environment-Task Discovery + Continuous Self-Evolving Agent Training
    - GRPO 기반 다중 환경 RL + 자동 진단→타겟 확장→RL 루프
    - Agent-World-14B: BFCL-V4 55.8% (DeepSeek-V3.2-685B 54.1% 초과, 50배 작은 모델)
    - 환경 스케일링: 0→1978개 환경, 평균 성능 18.4%→38.5% (+20.1 pt)

- Source: session-logs/20260427-080119-fdca-*.md (arXiv: 2604.18394)
  - Paper: OpenGame: Open Agentic Coding for Games
  - Institution: CUHK MMLab
  - Created: wiki/analyses/opengame-agentic-game-development.md
    - 첫 오픈소스 end-to-end 웹 게임 생성 에이전틱 프레임워크
    - Game Skill: Template Skill(5개 물리 특성별 템플릿 패밀리) + Debug Skill(living protocol)
    - GameCoder-27B: Qwen3.5-27B 기반 CPT+SFT+RL 3단계 훈련
    - OpenGame-Bench: BH/VU/IA 동적 평가 (헤드리스 브라우저 + VLM 판정)
    - Hook-Driven Implementation이 가장 중요한 컴포넌트 (-10.1 BH, -11.6 IA without it)

- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 (skip 1건, 생성 2건)

## 2026-04-26T20:30 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260426-141208-ad61-맥비-지금-코드가-어디까지-구현되었는지-확인해-주세요.md
  - Project: openclaw / asset-dashboard
  - Updated: wiki/projects/openclaw.md
    - Phase 2 완료 기록 (Account/Holding CRUD + Server Actions)
    - Phase 3 완료 기록 (Yahoo Finance 시세·환율 연동)
    - Phase 3 구현 파일 상세: PriceCache 모델, lib/market.ts, app/actions/prices.ts, lib/portfolio.ts PriceContext, RefreshPricesButton
    - yahoo-finance2 ESM-only 모듈 주의사항 (require 불가, new YahooFinance() 인스턴스화 필요, as any 우회)

- Source: session-logs/20260426-155125-ac49-logout.md
  - Project: openclaw
  - Skipped: "logout" 2회 입력만 있는 빈 세션 (assistant_turns: 0)

- Source: session-logs/20260426-184740-49b5-지금-실행되는-next.js-에서-5-issues-라고-뜹니다.-##-Error-Type.md
  - Project: openclaw / asset-dashboard
  - Created: wiki/analyses/prisma-decimal-nextjs-serialization.md
    - Prisma Decimal 타입 → Next.js Server→Client 직렬화 경계 오류 원인 분석
    - spread `...holding` 시 Decimal 포함 → 런타임 에러 (tsc 통과라 발견 어려움)
    - 수정 패턴: toNumber()로 Decimal 필드를 명시 변환 후 override
  - Updated: wiki/projects/openclaw.md
    - Prisma Decimal 직렬화 버그 수정 패턴 + [[prisma-decimal-nextjs-serialization]] 링크 추가

- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 (skip 1건, 수정 1건, 생성 1건 + 수정 1건)

## 2026-04-28T09:00 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260428-080030-b01f-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" health-check 프롬프트)
- Source: session-logs/20260428-080034-ffa6-*.md (arXiv: 2604.22748)
  - Paper: Agentic World Modeling: Foundations, Capabilities, Laws, and Beyond
  - Institution: HKUST·NUS·Oxford·NTU·CUHK 등 다기관
  - Created: wiki/analyses/agentic-world-modeling-taxonomy.md
    — L1(Predictor)/L2(Simulator)/L3(Evolver) × 4가지 지배 법칙 체제(물리/디지털/사회/과학)
    — 400편+ 문헌 합성. L1→L2 경계는 단일 스텝 품질이 아닌 롤아웃 충실도로 결정
    — 대표 시스템: EfficientZero(2시간 Atari 초인간), TD-MPC2(317M·104과제)
- Source: session-logs/20260428-080124-aa02-*.md (arXiv: 2604.20733)
  - Paper: Near-Future Policy Optimization
  - Institution: IIE, CAS / JD.COM
  - Created: wiki/analyses/near-future-policy-optimization-npo.md
    — RLVR 혼합 정책: S=Q/V (신호 품질/분산 비용) 트레이드오프 정식화
    — NPO: 같은 훈련 런의 근미래 체크포인트를 가이드로 활용
    — AutoNPO: 엔트로피 붕괴 탐지 → Δ* 자동 선택 → 롤백 재학습
    — Qwen3-VL-8B: 57.88 → 62.84 (NPO) → 63.15 (AutoNPO)
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 (skip 1건, 생성 2건)

## 2026-04-28T17:00 — wiki-ingest (session-logs, ingested: false 2건)

- Source: session-logs/20260428-152446-9b5b-project-toy-oss-radar--프로젝트를-시작하려고합니다.-현재상태를-분석해주세.md
  - Project: oss-radar (toy)
  - Skipped: 상태 분석 탐색 세션 (현황 파악만, 설계 판단 없음) — 다음 세션과 합산 처리
- Source: session-logs/20260428-153031-2553-project-toy-oss-radar--프로젝트의-phase-1부터-진행해-주세요.md
  - Project: oss-radar (toy)
  - Created: wiki/projects/oss-radar.md
    — Phase 1~6 전체 구현 완료 기록
    — 스코어링 공식 (star_velocity×0.5 + star_total_norm×0.3 + fork_norm×0.2)
    — `env -u CLAUDECODE claude -p` 중첩 세션 방지 패턴
    — GitHub API rate limit (60→5000 req/h with GITHUB_TOKEN)
    — README 40k자 truncate, bkit footer 제거, 한국어 검증 max 2회 재시도
    — macOS launchd 매주 월요일 09:00 KST 자동화
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 2개 session-log 파일

## 2026-04-29T00:00 — wiki-ingest (session-logs, ingested: false 9건)

- Source: session-logs/20260428-234006-d910-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 OK 프롬프트)
- Source: session-logs/20260428-234011-cd92-*.md (oss-radar analyze 세션: public-apis)
  - Skipped: assistant_turns: 0 (analyze.sh가 `claude -p`로 호출한 비대화형 세션, 실제 분석은 data/analysis/에 저장)
- Source: session-logs/20260428-234044-dc39-*.md (oss-radar analyze 세션: free-programming-books)
  - Skipped: 동일 사유 (assistant_turns: 0)
- Source: session-logs/20260428-234111-ba4a-*.md (oss-radar analyze 세션: system-design-primer)
  - Skipped: 동일 사유 (assistant_turns: 0)
- Source: session-logs/20260428-234144-9d4e-*.md (oss-radar analyze 세션: VibeVoice)
  - Skipped: 동일 사유 (assistant_turns: 0) — VibeVoice 분석 내용은 231551-12d1 세션에서 추출
- Source: session-logs/20260428-234218-45ec-*.md (oss-radar analyze 세션: mattpocock/skills)
  - Skipped: 동일 사유 (assistant_turns: 0)
- Source: session-logs/20260428-231410-9f52-*.md (asset-dashboard Prisma 에러)
  - Project: asset-dashboard (Next.js + Prisma)
  - Created: wiki/analyses/prisma-generate-missing-error.md
    - `.prisma/client/` 미생성으로 findMany 에러 원인 분석
    - 해결: `npx prisma generate`
    - 재발 방지: `dev` 스크립트에 `prisma generate &&` 추가 패턴
- Source: session-logs/20260428-231551-12d1-*.md (oss-radar 실행 및 자동화)
  - Project: oss-radar
  - Updated: wiki/projects/oss-radar.md
    - analyze.sh venv python3 우선 사용 버그 수정 기록 (시스템 python3 → .venv/bin/python3)
    - 스케줄 변경: 매주 월요일 → 매일 09:00
    - GITHUB_TOKEN git 비포함 패턴 (LaunchAgents 설치 파일에만)
    - 중복 방지 한계 3가지 추가 (최신성·Trending 의존도·lookback_days 미적용)
  - Created: wiki/analyses/microsoft-vibevoice-voice-ai.md
    - oss-radar 파이프라인 첫 실행에서 발굴한 Microsoft VibeVoice 분석
    - ASR-7B 60분 단일패스, TTS-1.5B 90분, Realtime-0.5B 300ms
    - Rich Transcription (Who/When/What), 파인튜닝 코드 공개
- Source: session-logs/20260428-235122-215d-*.md (ht_trading 매매 로그 분석)
  - Project: ht_trading
  - Updated: wiki/projects/ht-trading.md
    - 동적 트레일링 스톱 (trailing_tiers) 기능 추가 기록
    - 수익 3~9%: 3%, 10~19%: 6%, 20%+: 10% distance 구간별 확대
    - profit_take_levels 비활성화 (trailing_tiers로 대체)
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 9개 session-log 파일 (skip 6건, 처리 3건, 생성 2건, 수정 1건)

## 2026-04-22T08:30 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260422-080031-e9df-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260422-080037-7719-*.md (arXiv: 2604.18168)
  - Created: wiki/analyses/meanflow-text-to-image.md
    — MeanFlow 텍스트 조건부 확장 (EMF), discriminability·disentanglement 분석
- Source: session-logs/20260422-080140-da77-*.md (arXiv: 2604.16044)
  - Created: wiki/analyses/diffusion-snr-t-bias.md
    — DPM의 SNR-t 편향 규명 및 웨이블릿 도메인 training-free 보정법
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체

## 2026-04-30T09:00 — wiki-ingest (session-logs, ingested: false 5건)

- Source: session-logs/20260429-080017-0092-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260429-080021-a14d-*.md (arXiv: 2604.24355, "An Aircraft Upset Recovery System with Reinforcement Learning")
  - Skipped: assistant_turns: 0 (LLM 분석 응답이 누락된 세션, 결론 부재로 wiki 생성 보류)
- Source: session-logs/20260429-080059-18fa-*.md (arXiv: 2604.22446, "From Skills to Talent: Organising Heterogeneous Agents as a Real-World Company")
  - Created: wiki/analyses/onemancompany-heterogeneous-agents-organization.md
    — Talent–Container 분리(6 인터페이스) + E²R 트리(AND-FSM 7 불변조건) + 자기 진화(1on1·SOP·HR) → PRDBench 84.67% (Claude-4.5 +15.48pt), 태스크당 $6.91
- Source: session-logs/20260430-080020-6dbb-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260430-080453-b15a-hi.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "hi")
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 5개 session-log 파일 전체 (skip 4건, 처리 1건, 생성 1건)

## 2026-04-30T18:30 — wiki-ingest (session-logs, ingested: false 17건)

- Source: session-logs/20260430-141817-baa6-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0)
- Source: session-logs/20260430-141825-b1dc-*.md, 141857-8b24-*.md, 141931-b081-*.md, 142011-af92-*.md, 142048-7399-*.md (oss-radar cron 의 OSS 분석 프롬프트 5개)
  - Skipped: 모두 assistant_turns: 0 (cron 첫 시도, 분석 응답이 생성되지 않은 세션. 같은 시간대에 oss-radar 프로그램 디버깅이 진행되어 cron 실행이 실패한 결과로 보임)
- Source: session-logs/20260430-144026-977a-Reply-with-only--OK.md
  - Skipped: 내용 없음
- Source: session-logs/20260430-144033-2641-*.md, 144111-c80d-*.md, 144143-2c7c-*.md, 144218-615f-*.md (oss-radar cron 의 OSS 분석 프롬프트 4개)
  - Skipped: 모두 assistant_turns: 0
- Source: session-logs/20260430-144247-00cb-*.md (oss-radar cron, affaan-m/everything-claude-code 분석)
  - Project: oss-radar
  - Created: wiki/analyses/everything-claude-code.md
    — Claude Code 하네스 성능 시스템 (170k stars): 48 agents, 182 skills, AgentShield 1,282 tests + 102 정적 분석 룰, Continuous Learning v2, 크로스 하네스, ECC 2.0 alpha (Rust 컨트롤 플레인), v2.0.0-rc.1 변경, 설치 함정 4가지
- Source: session-logs/20260430-134759-328e-*.md (oss-radar cron 동작 안 됨 디버깅)
  - Project: oss-radar
  - Updated: wiki/projects/oss-radar.md
    — GITHUB_TOKEN 401 만료 → fine-grained PAT 재발급 + plist 평문 → config/.env 분리. GitHub Search API의 topic: OR 미지원 발견 → 카테고리별 N번 쿼리로 우회 (Search 후보 0 → 422)
  - Created: wiki/patterns/launchd-secret-management.md
    — launchd 가 ~/.zshrc 를 안 읽는 메커니즘, 안티 패턴 2종 (plist 평문, zshrc export), config/.env + run.sh source + chmod 600 표준 패턴, fine-grained PAT 권장
  - Created: wiki/analyses/github-search-api-topic-or-limitation.md
    — GitHub Repository Search API의 topic 한정자가 OR 연산자를 거부하는 격리 테스트, 카테고리별 쿼리 + full_name dedupe 우회 코드
- Source: session-logs/20260430-135011-e8eb-*.md (japa Vercel 첫 배포)
  - Project: japa-asset-dashboard
  - Created: wiki/projects/japa-asset-dashboard.md
    — 1인 자산 통합 대시보드 (Next.js 16 + Prisma + Supabase + Yahoo Finance + Gemini), 단일 사용자 인증 (HMAC-SHA256 Web Crypto + Edge runtime middleware), Prisma + Supabase 풀러 모드 분리 (DATABASE_URL Transaction / DIRECT_URL Session), force-dynamic 결정
  - Created: wiki/analyses/nextjs-vercel-supabase-deployment.md
    — Next.js + Vercel + Supabase 통합 배포 7가지 결정 지점 (GitHub App 연동, 업로드 한도, organization 제약, force-dynamic, Pooler 모드 비교, 환경변수 import + 재배포 타이밍, .next/.gitignore). 8항목 종합 점검 리스트
  - Created: wiki/bugs/node-modules-symlink-copy-prisma.md
    — node_modules 폴더 카피 시 .bin/prisma 심볼릭 링크 풀려서 wasm ENOENT, rm -rf node_modules && npm install 로 재생성. cp -a / rsync -aH 예방
- Source: session-logs/20260430-161410-0fcc-*.md (japa 추가 기능: 수익률 % / 시세 새로고침 글로벌 / 누락 종목 / 로그인)
  - Project: japa-asset-dashboard
  - Updated: wiki/projects/japa-asset-dashboard.md (단일 사용자 인증·시세 새로고침 worker pool 6 + retry·수익률 % 표시)
  - Created: wiki/bugs/yahoo-finance-concurrent-silent-fail.md
    — yahoo-finance2 동시 30개 호출 시 일부 응답에 regularMarketPrice 누락 silent fail, worker pool 6 + 250ms 1회 재시도 + UI ⚠️ 가시성 패턴, 진단 명령
- Source: session-logs/20260430-171050-9ee3-*.md (openclaw apc list 확인)
  - Skipped: openclaw 명령어 디스커버리 (apc 는 acp 의 오타, sessions 명령으로 추정), 새로운 설계 판단·버그·패턴 없음
- Source: session-logs/20260430-174408-1a2e-*.md (finance-analysis-nextjs 파악)
  - Project: finance-analysis-nextjs
  - Created: wiki/projects/finance-analysis-nextjs.md
    — 한국 기업 재무분석 대시보드 (Next.js 16 + Prisma + 멀티 AI 프로바이더), PDF/JSON/DART API 입력, 12 슬라이드 + AI/수동 밸류에이션, M&A 활용 계획. 약점 4가지 (밸류에이션 신뢰도, JSON 통째 저장 + FinancialStatement 미사용, PDF 인식율, sessionStorage 탭 분리)
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 17개 session-log 파일 전체 (skip 12건, 처리 5건; 생성: projects 2건, analyses 3건, patterns 1건, bugs 2건; 업데이트: projects 1건)

## 2026-05-15T00:00 — wiki-ingest (session-logs, ingested: false 2건)

- Source: session-logs/20260514-215345-f0e2-제가사용하는-PC-의-SDD-disk-size가-256GB-로-작은-사이즈-입니다.-현재.md
  - Project: disk-monitor (신규)
  - Created: wiki/projects/disk-monitor.md
    — 일일 디스크 사용량 모니터링 (Python 단일 파일 + launchd 09:00). 핵심 설계 판단 4가지 (코드/데이터 분리, plist symlink 패턴, 자동 정리 금지·사용자 컨펌, 하루 한 스냅샷 덮어쓰기). 첫 baseline 스캔 + 캐시 정리 3.23G 회수
  - Created: wiki/analyses/macos-disk-cleanup-cache-classification.md
    — macOS 캐시 3 카테고리 분류 (자동 재생성 / 순수 회수 / 다음 사용 시 재다운로드) + Claude Desktop 의 9.8G footprint 분해 (vm_bundles 8.4G = Cowork Linux VM, ShipIt = Sparkle 업데이트 잔여물 등). depth 1 만 보고 결론 금지의 함정, 첫 운영 케이스 회수 표
- Source: session-logs/20260514-220947-2eee-todo.md-읽고-이어서.md
  - Project: disk-monitor (이어지는 세션)
  - Updated: wiki/projects/disk-monitor.md (rename 후속, plist 위치 이전, CLAUDE.md 신설 반영)
  - Created: wiki/patterns/launchd-plist-symlink-from-project.md
    — launchd plist 마스터를 프로젝트 폴더에 두고 `~/Library/LaunchAgents/` 는 symlink (Homebrew services 패턴). install 서브커맨드 구현 코드, `.gitignore` 필수 (절대 경로), rename 시 dangling symlink 함정, `launchctl list -  0` 출력 의미, 시크릿 분리 ([[launchd-secret-management]]) 와의 분담
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 2개 session-log 파일 전체 (생성: projects 1건, analyses 1건, patterns 1건)

## 2026-05-18T22:00 — wiki-ingest (session-logs, ingested: false 7건)

- Source: session-logs/20260517-204826-4fc6-주간-다이제스트는-언제-수행되나요.md
  - Project: dev-blog (운영 위치 확인)
  - Updated: wiki/projects/dev-blog.md
    — 「운영 흐름 (cron + GitHub Pages)」 섹션 보강. launchd 진입점 `~/Library/LaunchAgents/com.user.dev-blog.daily.plist` 가 단 하나이며 (`StartCalendarInterval: Hour=7, Minute=0`), 주간 다이제스트 (`weekly-linux.mjs` 의 `<YYYY>-W<NN>-linux-weekly.json`) 는 별도 weekly cron 없이 daily 흐름 안에서 같이 호출되는 구조 명시. 변경 이력에도 5/17 운영 위치 확인 항목 추가. 코드 변경 없음
- Source: session-logs/20260518-070009-ddac-#-Linux-Daily-Newsletter-Rewrite-당신은-리눅스-커널-개발자를-돕.md
  - Project: dev-blog (Linux Daily Newsletter Rewrite 프롬프트 진화)
  - Note: 이 세션은 dev-blog 의 daily 파이프라인에서 외부 LLM (claude -p) 으로 자동 발사된 프롬프트 본문이라 `assistant_turns: 0` 이지만, 프롬프트 본문에 5/10 대비 진화한 메타 그라운딩 룰이 풍부히 포함되어 일반 분석으로 추출
  - Created: wiki/analyses/llm-newsletter-rewrite-metadata-grounding.md
    — LLM 뉴스레터 rewrite 메타데이터 그라운딩 베스트 프랙티스 10 룰. candidateBodies 종류별 처리 (LKML commit message vs kernel.org 백포트 목록), history.previousVersion (v2→v3) vs previouslySeenAt (X일부터 추적) 차별, fromMaintainer (Linus/Greg KH/Andrew Morton) 권한 단서 자연스러운 삽입, maintainerComments excerpt 톤 3분류 (반대·보류/환영·머지/모호), action 「조건절+검증단서」 강제 + 부정 예시, 본문 4섹션 (릴리스·회귀보안·핵심변경·기타)·priority 상1~2/중2/하0~1 분포·summary 2문장 제약·implications/nextActions 금지, 입력 키 (id/topic/date/sources/draftMetadata) 보존·candidateBodies 출력 금지, 「독자 가정」 첫 줄 명시. 토픽 일반화 표 (Android 커널 / OSS Trending / 보안 어드바이저리 / 머신러닝 논문) 포함
  - Updated: wiki/projects/dev-blog.md
    — frontmatter sources 에 5/17 / 5/18 두 로그 추가 + related 에 신규 분석 페이지 링크. 변경 이력에 5/18 항목 추가. updated 2026-05-18
- Source: session-logs/20260518-070202-63b9-#-Android-Kernel-Daily-Briefing-당신은-Android-커널-플랫폼.md
  - Project: dev-blog (Android Daily Briefing 프롬프트)
  - Note: 같은 launchd 사이클의 Android 토픽 prompt 본문, `assistant_turns: 0`
  - Updated: wiki/analyses/llm-newsletter-rewrite-metadata-grounding.md (Android 커널 ACK prefix 풀어쓰기 룰 + ACK 브랜치 한 줄 추적을 토픽 일반화 표에 보강)
  - Updated: wiki/projects/dev-blog.md (frontmatter sources + 변경 이력 5/18 Android/OSS 항목)
- Source: session-logs/20260518-070433-9bd5-#-Open-Source-Trending-Daily-Briefing-당신은-오픈소스-트렌드.md
  - Project: dev-blog (OSS Trending Daily Briefing 프롬프트)
  - Note: 같은 launchd 사이클의 OSS 토픽 prompt 본문, `assistant_turns: 0`
  - Updated: wiki/analyses/llm-newsletter-rewrite-metadata-grounding.md (룰 5 변형 추가 — action 3분해 `if`/`do`/`verify` 글자수 가이드라인 30/50/60자 / 룰 7a 신설 — title 형식 `{date} {핵심사건} — {토픽}` + 80자 headline 필드 / 토픽 일반화 표의 OSS 행 보강 (신규 60일 vs 활발 5k+ 7일내 push 분리))
  - Updated: wiki/projects/dev-blog.md (frontmatter sources)
- Source: session-logs/20260518-071049-b424-#-오픈소스-큐레이션-브리핑-당신은-오픈소스-큐레이터입니다.---오픈소스-큐레이션---파이.md
  - Project: dev-blog (OSS Curation 프롬프트, `topic: opensource-curation`)
  - Note: 같은 launchd 사이클의 OSS Curation prompt 본문, `assistant_turns: 0`
  - Updated: wiki/analyses/llm-newsletter-rewrite-metadata-grounding.md (토픽 일반화 표에 OSS Curation 행 추가 — `analysisExcerpt` + `curationScore` + `hasAnalysis` 메타가 핵심 그라운딩 신호)
  - Updated: wiki/projects/dev-blog.md (frontmatter sources)
- Source: session-logs/20260518-071258-b7e7-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md
- Source: session-logs/20260518-071614-9260-#-Linux-specialist-list-lens-—-Newsletter-rewrite.md
  - Project: dev-blog (Linux specialist list lens 프롬프트, 보안/도구체인/RT/GPU 등 렌즈 — 같은 sample template 의 lens별 발사)
  - Note: 같은 launchd 사이클의 Linux Lens prompt 본문, `assistant_turns: 0`. 두 파일 모두 동일 prompt body (lens 토픽만 다름). linux-newsletter-ko.md 의 룰을 인용하면서 렌즈-specific 변형만 추가하는 구조
  - Updated: wiki/analyses/llm-newsletter-rewrite-metadata-grounding.md (토픽 일반화 표에 Linux Lens 행 추가, `draftMetadata.signalLevel` 의 「저신호일 summary prefix」 룰은 [[llm-content-quality-guards]] 가드 4 의 토픽-specific instantiation 임을 명시)
  - Updated: wiki/projects/dev-blog.md (frontmatter sources)
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 7개 session-log 파일 전체 (생성: analyses 1건, 업데이트: projects 1건; 동일 분석 페이지에 토픽 5종 그라운딩 룰 흡수)

## 2026-05-18T23:30 — wiki-ingest (session-logs, ingested: false 14건, 자동 cron 후속 배치)

- 대상 14건 모두 동일 호스트 (wookiui-Macmini) 의 5/18 07:19~09:03 자동 cron 결과물. 사람 결정·디버깅·구현 없이 prompt 발사 + 결과만 기록된 로그. 분류:
  - dev-blog 사이클 (07:19~07:30 KST, 5건): Linux specialist list lens prompt 4건 (be69 / 6c93 / 3ef3 / 691d, lens 토픽만 다른 동일 sample template) + Linux Kernel Weekly Digest prompt 1건 (9857)
  - alive 핑 (08:00 / 09:00 KST, 2건): research-wiki 와 oss-radar 의 "Reply with only: OK" 헬스체크
  - research-wiki 사이클 (08:00 KST, 2건): AI 논문 분석 prompt (a73e Causal Forcing++, d399 다른 논문) — 매우 일반적인 「논문 분석」 템플릿 (한줄 요약 / 배경 / 방법론 / 결과 / 한계 / 적용성 / 관련 논문 + 1000~1500자), 신규 룰 없음
  - oss-radar 사이클 (09:00~09:03 KST, 5건): OSS 레포 분석 prompt (153d microsoft/ai-agents-for-beginners 외 4건) — 「한줄 요약 / 주요 기능 / 사용 시나리오 / 기술 스택 / 주목 이유 / 실용성 평가 + 800~1200자」 템플릿 그대로, 신규 룰 없음
- Source: session-logs/20260518-073052-9857-#-Linux-Kernel-Weekly-Digest-*.md (Linux Kernel Weekly Digest prompt)
  - Project: dev-blog (`weekly-linux.mjs` 진입점, daily 흐름 안에서 매일 호출)
  - Note: 일일 prompt 와 별개의 「주간 압축」 룰 4개를 정의 (`assistant_turns: 1`, file_edits: 1 — weekly JSON 생성). 일일 룰의 시간축 확장 변형
  - Updated: wiki/analyses/llm-newsletter-rewrite-metadata-grounding.md
    — 「주간 다이제스트 변형 — 일일의 "압축" 모드」 섹션 신설. 주간 룰 W1 (같은 흐름 묶기 — "월: v2 → 수: 피드백 → 금: v3 보류" 식 진행 흐름 한 줄, 일일 룰 2 의 주 단위 확장), W2 (이번 주 3~5개 흐름만, 일일 룰 9 의 주간판, 28개 일일 후보의 평탄화 금지), W3 (국부 드라이버 제외, 일일 룰 8 그대로), W4 (dailies 배열 재포함 금지, 일일 룰 10 의 주간판). 「주간 = 일일의 압축, 합집합 아님」 일반 사상 정리
- Source: session-logs/20260518-071926-be69-*.md / 072313-6c93-*.md / 072522-3ef3-*.md / 072906-691d-*.md (Linux specialist list lens prompt 4건)
  - Project: dev-blog (lens 토픽 4종, 직전 22:00 ingest 의 lens 2건과 동일 prompt body)
  - Note: 신규 룰 없음 (lens 토픽만 다른 sample template). 본 분석 페이지에는 sources 누적 가치 없어 생략, dev-blog 변경 이력으로만 추적
  - Updated: wiki/projects/dev-blog.md (frontmatter sources 5건 추가 [lens 4건 + weekly 1건] + 변경 이력 「Lens 4 + Weekly Digest」 항목 + 「cron 정상 회복」 항목)
- Source: session-logs/20260518-090051-c52b-*.md (alive 핑) + 20260518-090057-153d-*.md / 090130-8d3b-*.md / 090158-f59e-*.md / 090228-3568-*.md / 090304-cd1c-*.md (OSS 레포 분석 prompt 5건)
  - Project: oss-radar
  - Note: 5/17 의 광범위 silent fail (시스템 단 원인 의심) 후속, 5/18 cron 정상 회복 — prompt 발사 정상, `assistant_turns` 일부 0~1 분포. prompt 본문은 기존 OSS 분석 템플릿 그대로 (신규 룰 없음)
  - Updated: wiki/projects/oss-radar.md (frontmatter sources 6건 추가 + 변경 이력 「2026-05-18 cron 정상 회복」 항목)
- Source: session-logs/20260518-080019-b120-*.md (alive 핑) + 20260518-080025-a73e-*.md / 080126-d399-*.md (AI 논문 분석 prompt 2건)
  - Project: research-wiki (wiki/projects/ 페이지 없음 — 5/17 dev-blog 변경 이력에서 "08:00 research-wiki 의 논문 분석" 형태로 간접 추적). 신규 페이지 생성하지 않음 (논문 분석은 research-wiki 별도 vault 로 누적되는 결과물이며 본 wiki 가 그 누적치를 다시 수집할 가치는 낮음)
  - Note: 5/17 silent fail 후속, 5/18 정상 회복. 신규 룰 없음
- Skipped: 자동 cron 결과물 자체를 본 wiki 에 페이지로 누적하지 않음 (research-wiki / oss-radar / dev-blog 라는 별도 시스템에 산출물이 누적되며, 본 wiki 는 *프롬프트 룰의 진화* 와 *운영 관찰* 만 추출). AI 논문 분석 / OSS 레포 분석 prompt 템플릿 자체는 일반적이라 신규 룰 흡수 없음
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 14개 session-log 파일 전체 (생성: 0건, 업데이트: analyses 1건 [주간 변형 섹션 신설] + projects 2건 [dev-blog 변경 이력 2항 + sources 5건, oss-radar 변경 이력 1항 + sources 6건])

## 2026-05-19T20:00 — wiki-ingest (session-logs, ingested: false 27건)

- 대상 27건 분류:
  - **ht_trading 디버깅 (1건, 핵심)**: 5/18 23:31 시작된 「현대해상 매매 거부 원인 분석」 세션. 첫 질의는 max_positions 10/10 한도 — 이미 5/18 ingest 에서 기록됨. 같은 세션 5/19 09:29~09:34 의 후속 질의 「화신 -19%, GS -11% 인데 왜 손절을 안 하나」에서 **scoring_strategy.py:817~833 의 절대 손절 `elif` dead code** 발견 (벤치마크 항상 존재 → `elif profit_pct <= -absolute_stop_loss_pct:` 도달 불가, absolute_stop_loss_pct=0.10 설정값 무효). 상대 손절 V3 D 튜닝 (-15% → -20%) 과 결합되어 *벤치마크 동반 하락기 손절 전부 꺼짐*
  - **단발 메모 1건**: 5/18 23:38 「자동화하면 좋은 것들」 — 14자 user prompt 만 있고 assistant 응답 없음. 후속 작업 없는 메모, ingest 가치 없음
  - **dev-blog 07:00 cron 사이클 (17건)**: Linux Daily 1 + Android 2 + OSS Trending 2 + OSS Curation 2 + Linux specialist list lens 10. 일부 (보안 렌즈 등) 는 실제 게시 JSON 생성, 나머지는 prompt 발사만 기록 (`assistant_turns: 0`). 5/18 의 [[llm-newsletter-rewrite-metadata-grounding]] 룰 (W1~W4 주간 변형 / 5 변형 — action 3분해 / 7a — title·headline) 그대로 적용, **신규 룰 없음**. 5/17 의 광범위 silent fail 후 5/18·19 모두 정상 — 시스템 단 결함 해소 유지
  - **research-wiki 08:00 cron (3건)**: alive 핑 + 2건 AI 논문 분석 prompt (`assistant_turns: 0`, 산출물 없음). prompt 본문은 기존 「한줄 요약 / 배경 / 방법론 / 결과 / 한계 / 적용성 / 관련 논문 + 1000~1500자」 템플릿 그대로
  - **oss-radar 09:00 cron (6건)**: alive 핑 + 5건 OSS 레포 분석 prompt. prompt 본문은 기존 템플릿 그대로, 신규 룰 없음
- Source: session-logs/20260518-233131-6a41-오늘-현대해상의-매매가-계속-거부되었는데-원인을-분석해-주세요.md (5/18~5/19 양일에 걸친 ht_trading 디버깅, 503 라인)
  - Project: ht_trading
  - Note: max_positions 한도 거부 (5/18 ingest 완료) + 절대 손절 elif dead code 발견 (5/19 ingest, **신규**). 권장 수정 `elif → if` 는 사용자 확인 대기로 미진행
  - Created: wiki/bugs/absolute-stop-loss-elif-dead-code.md
    — 위치 (scoring_strategy.py:817~833) / 분기 구조 / 발견 경위 (화신 -19%, GS -11% 미발동) / 매도 룰 4종 대조표 / 권장 패치 / 일반 교훈 (벤치마크 의존 손절은 fallback 이 아니라 "추가 가드", 상대 손절 완화 + 절대 손절 dead code = 손절 꺼짐, if/elif 의 상호 배타 의도 vs fallback 구분)
  - Updated: wiki/projects/ht-trading.md (frontmatter `updated: 2026-05-19`, related 에 bug 페이지 추가, 변경 이력 「2026-05-19」 항목 — elif dead code 발견과 V3 D 튜닝 결합 효과)
  - Updated: wiki/index.md (bugs 섹션에 [[absolute-stop-loss-elif-dead-code]] 항목 추가, frontmatter updated)
- Source: session-logs/20260519-070009-952a-*.md 외 dev-blog cron 17건
  - Project: dev-blog
  - Note: 5/19 launchd 사이클의 정상 발사. Android 2 + OSS Trending 2 + OSS Curation 2 + Linux specialist list lens 10 + Weekly daily (이미 ingest 됨). 보안 렌즈 (07:28) 는 FPIN u8 카운터 wrap DoS 보안 패치를 핵심 사건으로 잡아 4 highlights · 80자 headline 가드 통과까지 4회 재편집해 완성한 사례 — 신규 룰 흡수 없음, 5/18 의 분석 페이지 룰 그대로 통과
  - Updated: wiki/projects/dev-blog.md (frontmatter sources 17건 추가 + `updated: 2026-05-19` + 변경 이력 「2026-05-19 07:00 cron 정상 사이클, 17건」 항목)
- Source: session-logs/20260519-080023-b933-Reply-with-only--OK.md + 080028-2446-*.md + 080116-a9a0-*.md (research-wiki 08:00 cron 3건)
  - Project: research-wiki (별도 vault, 본 wiki 에 project 페이지 없음)
  - Note: alive 핑 + 논문 분석 prompt 2건 모두 `assistant_turns: 0`. 본 wiki 에 페이지 생성하지 않음 (논문 분석 결과물은 research-wiki vault 에 누적). 본 ingest 에서는 wiki/log.md 의 기록으로만 추적
  - Skipped: project 페이지 생성 보류 (5/18 ingest 와 동일 방침)
- Source: session-logs/20260519-090053-4874-Reply-with-only--OK.md 외 oss-radar 6건
  - Project: oss-radar
  - Note: alive 핑 + 5건 OSS 레포 분석 prompt. 신규 룰 없음
  - Updated: wiki/projects/oss-radar.md (frontmatter sources 6건 추가 + `updated: 2026-05-19` + 변경 이력 「2026-05-19 09:00 cron 정상 사이클 (2일 연속)」 항목)
- Source: session-logs/20260518-233810-d326-자동화하면-좋은-것들.md
  - Project: (없음, cwd=/Users/wooki/question)
  - Note: 14자 user prompt 만 있고 assistant 응답 없음. 후속 작업 없는 단발 메모. ingest 대상 외이지만 ingested: true 로만 마킹
  - Skipped: 페이지 생성·갱신 없음
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 27개 session-log 파일 전체 (생성: bugs 1건 [absolute-stop-loss-elif-dead-code], 업데이트: projects 3건 [ht-trading 변경 이력 + bugs related, dev-blog sources 17건 + 변경 이력, oss-radar sources 6건 + 변경 이력] + index 1건)

## 2026-05-21T10:00 — wiki-ingest (session-logs, ingested: false 9건)

- 대상 9건 분류 (전부 동일 호스트 wookiui-Macmini 의 5/21 자동 cron 사이클 결과물, 사람 결정·디버깅·구현 없음):
  - **research-wiki 08:00 cron (3건, 전부 silent)**: alive 핑 1건 (`assistant_turns: 0`) + AI 논문 분석 prompt 2건 (arXiv 2605.13527 "MMSkills — Multimodal Skills for General Visual Agents" Shanghai Jiao Tong/Xiaohongshu / arXiv 2605.18739 "LongLive-2.0 — NVFP4 Parallel Infrastructure for Long Video Generation" NVIDIA, 둘 다 `assistant_turns: 0`). 3건 전부 미응답 종료, 산출물 0
  - **oss-radar 09:00 cron (6건, 1/5 정상)**: alive 핑 (`assistant_turns: 1`) + 5건 OSS 레포 분석 prompt (freeCodeCamp/devdocs, explosion/spaCy, RSSNext/Folo, refinedev/refine, lapce/lapce). **Folo 1건만 `assistant_turns: 1` 로 응답 완성**, 나머지 4건은 단발 미응답. 5/20 의 「1/5 정상 + 4/5 단발 미응답」 부분 실패 분포가 2일 연속 재현
- Source: session-logs/20260521-080025-9af9-Reply-with-only--OK.md (research-wiki alive 핑) + 080031-f864-*.md (MMSkills 논문 분석 prompt) + 080132-6917-*.md (LongLive-2.0 논문 분석 prompt)
  - Project: research-wiki (별도 vault, 본 wiki 에 project 페이지 없음 — 5/18·19·20 ingest 와 동일 방침으로 페이지 생성 보류)
  - Note: 3건 전부 `assistant_turns: 0` 으로 prompt 발사·미응답 종료. prompt 본문은 기존 「한줄 요약 / 배경 / 방법론 / 결과 / 한계 / 적용성 / 관련 논문 + 1000~1500자」 템플릿 그대로 (신규 룰 없음). 산출물 0
  - Updated: wiki/projects/oss-radar.md (companion 항목 — 같은 호스트의 oss-radar 사이클이 1/5 정상이라 호스트·시스템 단 광범위 결함은 아니고, prompt 입력이 더 긴 research-wiki 쪽이 단발 미응답에 더 취약하다는 진단 신호로 묶어 기록)
  - Skipped: 신규 페이지 생성 없음 (논문 분석 결과 0건, 추출 가능한 산출물 부재)
- Source: session-logs/20260521-090055-7fda-Reply-with-only--OK.md (oss-radar alive 핑) + 090100-f8ec-*.md (devdocs) + 090133-b561-*.md (spaCy) + 090207-5ad9-*.md (Folo) + 090246-e375-*.md (refine) + 090328-c564-*.md (lapce)
  - Project: oss-radar
  - Note: alive 핑 + 5건 OSS 레포 분석 prompt. RSSNext/Folo 1건만 `assistant_turns: 1` 로 응답 완료, 나머지 4건 (devdocs / spaCy / refine / lapce) 은 단발 미응답. prompt 본문은 기존 OSS 분석 템플릿 그대로 (신규 룰 없음)
  - Updated: wiki/projects/oss-radar.md (frontmatter sources 9건 추가 [oss-radar 6건 + research-wiki 3건 companion] + `updated: 2026-05-21T10:00:00+09:00` + 변경 이력 「2026-05-21 09:00 cron」 항목 + 「2026-05-21 companion: 08:00 research-wiki 전체 silent fail」 항목)
- Skipped: 자동 cron 결과물 자체를 본 wiki 에 페이지로 누적하지 않음 (5/18·19·20 ingest 와 동일 방침). 신규 룰·코드 변경·버그·디버깅 신호 모두 없음
- Updated: wiki/index.md (updated 갱신), wiki/log.md
- Marked ingested: true — 9개 session-log 파일 전체 (생성: 0건, 업데이트: projects 1건 [oss-radar sources 9건 + 변경 이력 2항] + index 1건)

## 2026-05-27 — wiki-ingest (session-logs, ingested: false 5건)

- 대상 5건 분류:
  - **20260525-221758-68ef** (hi, dev-blog): 단발 hi 메시지, 산출물 없음 → 스킵
  - **20260526-231733-1640** (hi, finance-analysis-nextjs): 단발 hi 메시지, 산출물 없음 → 스킵
  - **20260526-231909-27e3** (login/hi, ht_trading): 단발 login·hi, 산출물 없음 → 스킵
  - **20260525-031658-92f2** (Linux Kernel Weekly Digest, dev-blog): 05-25 03:16 KST 자동 주간 다이제스트 실행. 5/19~5/23 일일 브리핑 → 주간 JSON 생성. `rewriteAdapter: cursor`, `generator: scripts/draft-linux.mjs`. Linux 7.1-rc5 / 7.0.10 / 5.10.257 트래킹 정상. 코드 변경 없음
  - **20260526-232119-548d** (현재 수익 없는 종목 유지 기간, ht_trading): 백로그 E 항목 부분 적용 — `stale_holding_days: 10 → 15` (commit `513c7a7`). Rule6 보유 기간 손절 임계 확장. `profit_pct < -0.02` 조건은 미포함. launchd plist 재시작 확인
- Source: session-logs/20260525-031658-92f2-*
  - Project: dev-blog
  - Updated: wiki/projects/dev-blog.md (sources 1건 추가 + updated 2026-05-25 + 변경 이력 2026-05-25 운영 항목)
- Source: session-logs/20260526-232119-548d-*
  - Project: ht_trading
  - Updated: wiki/projects/ht-trading.md (sources 1건 추가 + updated 2026-05-26 + 백로그 E 부분 적용 완료 표시 + 변경 이력 2026-05-26 항목)
- Skipped: 단발 hi/login 3건 (산출물·설계 판단·버그 없음)
- Updated: wiki/index.md (updated 갱신), wiki/log.md
- Marked ingested: true — 5개 session-log 파일 전체 (생성: 0건, 업데이트: projects 2건 [ht-trading, dev-blog] + index 1건)

## 2026-05-28 — wiki-ingest (session-logs, ingested: false 3건)

- 대상 3건 분류:
  - **20260527-213708-7369** (SK스퀘어 매수 거부, ht_trading): 리스크 거부 텔레그램 알림 세부 사유 추가 구현
  - **20260527-225019-71d3** (AI provider 선택, kakao-db): AI provider 다중화 구현 (claude/cursor/codex)
  - **20260527-231107-7699** (AI coding agent 블로그 조사, dev-blog): 탐색/조사 단계만, 최종 결론·구현 없음 → 스킵
- Source: session-logs/20260527-213708-7369-*
  - Project: ht_trading
  - Updated: wiki/projects/ht-trading.md (sources 1건 추가 + updated 2026-05-27 + 리스크 거부 텔레그램 알림 세부 사유 섹션 신설 + 설계 변경 이력 2026-05-27 항목)
- Source: session-logs/20260527-225019-71d3-*
  - Project: kakao-db
  - Updated: wiki/projects/kakao-db.md (sources 1건 추가 + updated 2026-05-27 + AI provider 다중화 섹션 신설 + 변경 이력 2026-05-27 항목)
  - Updated: wiki/analyses/multi-llm-provider-adapter-pattern.md (Shell 스크립트 기반 AI CLI 어댑터 패턴 섹션 추가 + 변경 이력 2026-05-27 항목)
- Skipped: 탐색/조사 단계 1건 (dev-blog, 최종 결론·구현 없음)
- Updated: wiki/index.md (updated 갱신 + ht-trading / kakao-db 요약 업데이트), wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체 (생성: 0건, 업데이트: projects 2건 [ht-trading, kakao-db] + analyses 1건 [multi-llm-provider-adapter-pattern] + index 1건)
