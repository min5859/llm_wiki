---
title: Operation Log
updated: 2026-05-07T13:05:00+09:00
---

# Operation Log

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
