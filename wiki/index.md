---
title: Wiki Index
updated: 2026-06-10T12:00:00+09:00
---

# Wiki Index

## 개념 (concepts/)

- [[gieok]] — Claude Code 세컨드 브레인: 세션 기억 상실 문제를 해결하는 자동 지식 축적 시스템
- [[openclaw-agent-architecture]] — 맥 기반 OpenClaw 에이전트 2계층(Main+Coding) 구조와 확장 방향
- [[claude-code-overview]] — 터미널 기반 4세대 AI 코딩 에이전트의 기본 개념, 요금체계, 도입 관점
- [[claude-code-skills-plugins]] — 5가지 번들스킬, 공식 플러그인 마켓플레이스, 개발 라이프사이클 플러그인
- [[claude-code-basic-usage]] — Plan 모드, /context, /compact vs /clear, 중요도별 커맨드 분류
- [[ai-agent-basics]] — LLM+프롬프트+도구 3계층 구조, 자율도 5레벨, 하네스 엔지니어링의 6요소
- [[ai-usage-philosophy]] — 언어화 능력·컨텍스트 설계·리버럴 아츠의 AI 활용 3가지 핵심 원칙
- [[hermes-agent]] — Nous Research 의 self-hosted personal AI agent: persistent memory + skill 자가생성 + Telegram/Discord/Slack 다중 게이트웨이 + 200+ LLM provider
- [[slab-no-merge-cross-cache-hardening]] — 리눅스 커널 SLUB 캐시 병합과 SLAB_NO_MERGE: 보안 민감 캐시를 병합에서 제외해 cross-cache heap exploitation 차단. slab_nomerge / RANDOM_KMALLOC_CACHES / TYPESAFE_BY_RCU 다층 방어

## 프로젝트 (projects/)

- [[agent-weekly]] — Claude 대화 기록 기반 주간보고 자동작성 에이전트(아이디어 구체화 단계). gieok 의 "Hook→로그→스케줄 LLM→Wiki→Git" 파이프라인을 골격으로 차용, 주간 집계 LLM 호출로 치환. 원천 데이터로 [[claude-code-session-jsonl-format]] 활용 가능
- [[gieok]] — gieok 설치 상세, CC Hook 이벤트, LaunchAgent 스케줄, 기능별 LLM 필요 여부, 알려진 버그
- [[ht-trading]] — 알고리즘 트레이딩 시스템: ScoringStrategy 이중 스케일(40/100점). KIS API 서킷브레이커 (연속 5회 오류 시 주문 중지). 추가매수 재개 조건 (저점 반등 +3% AND 기술점수 회복). n_stock_info V3 리버트 → EPS/캔들만 선택적 재적용 (모멘텀 충돌 회피). screener min_score 62 복원. 거래대금 TOP 10 텔레그램 추가. 시각 가드 09:30 (매수/매도 공통). trailing stop activation 3%, tiers {3%,2%}/{12%,4%}/{22%,8%}. **무한매수법(InfiniteBuying) 활성화**: Signal.bypass_position_check 플래그, exclude_codes 종목 격리, Tiered Trailing Stop (3→1%/7→2%/15→3%), max_positions 11.
- [[n-stock-info]] — 네이버 금융 기반 종목 스크리닝·스코어링·텔레그램 리포트 (Python, collectors→analyzers→reporters→db). 100점 스코어링(40기술+40기본+20리서치). 2026-06-02 세션: EPS→EY 라벨 동기화, 안정성 게이트 분리(적자·고부채 점수 무관 제외), 섹터 상대 PER(네이버 동일업종 PER 외부 벤치마크), 백테스트 하네스(Rank IC+분위 스프레드, 생존 편향 노출), 전체 scored 저장(idempotent DELETE-INSERT), 데이터 기반 config 튜닝(병목은 상한이 아니라 min_score 컷오프 → 65→55)
- [[openclaw]] — AI 에이전트 자동화 도구: 다중 에이전트(main/english/coder) 구성, Telegram 그룹 Privacy Mode 설정, 라우팅 버그 트러블슈팅. 6/3 전 토픽 응답 무 = Hermes 와 공유하는 Codex OAuth refresh token 쟁탈(핑퐁) → 클라이언트별 독립 device-flow 등록으로 해결
- [[oss-radar]] — 주간 GitHub OSS 발굴 파이프라인: discover→fetch→analyze→publish 6단계, star_velocity 스코어링, env -u CLAUDECODE 중첩세션 방지, GitHub topic OR 미지원 우회, config/.env 시크릿 분리
- [[ai-shorts-production-with-claude-code]] — Claude Code로 AI 쇼츠 영상 대량 제작 흐름, Claude/사람 역할 분리
- [[japa-asset-dashboard]] — 1인 전용 자산 통합 대시보드: Next.js 16 + Prisma + Supabase + Yahoo Finance + 멀티 LLM, Supabase Auth 매직 링크 + RHF/Zod 폼 + BUY/SELL Transaction 추적 (가중평균 + realizedGain 동결)
- [[finance-analysis-nextjs]] — 한국 기업 재무분석 대시보드: PDF/JSON/DART API 입력, AI 멀티 프로바이더 구조화, 12 슬라이드 + AI/수동 밸류에이션, M&A 활용 계획. 비판적 검토 7개 갭 + Phase 1~7 일괄 구현 (F-Score / CSV / 토큰 비용 가드 / risk flag / 공유 링크 / 시나리오 패널 / vitest 32 테스트)
- [[wardrobe]] — 옷장 매칭 웹 앱 (Next.js 15 + Tailwind v4 + LocalStorage MVP): 사이드바 레이아웃, 시드 JSON, DB 없이 Step 1 단계로 시작
- [[kakao-mem]] — Mac KakaoTalk 메모리 CLI (Python + `kakaocli`): 어댑터 격리 / message_id sha256 dedup / launchd 자동화. 8개 잠재 이슈와 직접 통신 옵션 분석
- [[kakao-db]] — Mac KakaoTalk 로컬 sqlcipher DB + LOCO 어댑터 (Rust): 5 결정 (Rust 단일 / OSS LOCO / 단발 CLI + cron / Keychain / App Store 26.3.0), M0 완료, M1 inspect 휴리스틱 진입. AI provider 다중화 (KAKAO_AI_PROVIDER: claude/cursor/codex, 기본 codex). M6-new: dashboard.html self-contained 확인, Cloudflare Pages + Zero Trust Access 권장
- [[kernel-digest]] — 리눅스 커널 일일 다이제스트 (계획 단계, M0 완료): 4축 콘텐츠 / 8 데이터 소스 / Collectors→AI Stage→Publisher 파이프라인 / 종량제 API 금지 + 구독제 LLM (`claude -p`/`openclaw`) 만 사용 / 토픽-플러그인 확장형
- [[dev-blog]] — AI 보조 한국어 엔지니어링 일일 뉴스레터: Node 20+ 표준 API 만 사용 의존성 0개, claude-CLI 어댑터 + template fallback, cron-on-laptop + GitHub Actions 빌드. Multi-topic 가동 (11토픽). `lib/run-daily-pipeline.mjs` 로 6개 run-daily 스크립트 공통화 (~700줄→~150줄). `lib/collect-utils.mjs` readJson 추출. 기본 어댑터 cursor→claude 일괄 전환. research(dossier)→write(newsletter) 2단계 분리가 6/10 cron 운영에 통합 (03:00 사이클), Anubis 봇 차단이 lore.kernel.org 전반으로 확대.
- [[auto-pipe-blog]] — 컨셉 1개 → velog 글 자동화 파이프라인 (bash + `claude -p` stdin): 00-slug → 01-research → 02-outline → 03-draft → 05-assemble 5단계, skip-if-exists, `CALL_LLM_BACKEND=agent` 로 백엔드 전환. Phase 1 E2E ~4분 / 78줄 post.md / mermaid 2 블록. Phase 3.5 Notion publisher 추가 (parent page/database 자동 판별, 100블록 분할, 로컬 이미지 안내 paragraph 치환) + factcheck rewrite 단계 추가
- [[auto-pipe-ppt]] — JSON/YAML → 디자인 토큰 기반 멀티슬라이드 PPTX 자동 생성 (Python + `python-pptx` + 절대좌표 도형 + 일부 OOXML 직접 작성). 1차 타겟 재무제표 10장. M0/M1/M2/M3 완료 (41건 테스트 그린): 토큰 이중 어댑터 (YAML / CSS :root), role resolver, 한글 폰트 ea/cs fix, 재무 컴포넌트 6종 (KPI/Insight/Verdict/ScoreCard/Conclusion/Table). M4 차트 5종 / M5 재무 어댑터 미구현
- [[hermes]] — Nous Research personal AI agent macOS 셋업: default + 코딩 전용 `maccoder` 두 프로필, OAuth symlink 공유, claude CLI HOME 격리 우회 wrapper, Telegram 별도 봇. 6/3 codex 토큰 만료(복사 import → 회전 충돌) → `hermes auth add openai-codex --type oauth` 자체 device-flow 재인증
- [[upbit-trading]] — Upbit 암호화폐 무한매수법 자동매매 (Python + launchd, 40분할 DCA + Trailing Stop): 70일 운영 평균 +5.20% (10라운드), 5개 키 튜닝 (trailing 2.5% / cooldown 6h / max_round_days 45 + 계단식 / partial_profit ON / tighten ON). 2026-06-06: PAUSED 6h 텔레그램 알림 + 저거래량 DCA 제동 신규, 추세필터 ON/OFF 백테스트 재검증 결과 30분봉(운영 봉)에서 OFF 우세 → OFF 유지, launchd 봇 당분간 중지
- [[disk-monitor]] — 일일 디스크 사용량 모니터링 (Python 단일 파일 + launchd 09:00). 데이터는 `~/Library/Application Support/disk-monitor/` (코드/데이터 분리), plist 마스터는 프로젝트 폴더 (Homebrew 스타일 symlink), 자동 정리 금지·사용자 컨펌 워크플로우. 5/30: 개발 도구 경로 8개 추가 (~/project, ~/.hermes 등 ~26G 사각지대 해소), 모니터링 경로 31개로 확장. 6/3: -16G 급감 = macOS Tahoe 업데이트 준비물(추적 밖). 코드 보완 — 측정 실패 가시화(`errors`/`roots`), `report` 에 Tracked vs Unaccounted 갭 라인, `/Library/Updates` 추적

## 설계 판단 (decisions/)

- [[openclaw-coder-default-model-codex]] — OpenClaw 코더 default 모델을 Anthropic Claude Opus → Codex GPT-5.5 로 변경. Anthropic OAuth organization 차단 + fallback 0개로 인한 silent 침묵 회피. Claude Opus 는 `/acp spawn --bind here` 명시 호출 시에만 사용

## 패턴 (patterns/)

- [[claude-code-setup]] — Claude Code 초기 설정 7가지, 하네스 6요소, CLAUDE.md 위치, Rules/Hooks, MCP 검증
- [[claude-code-advanced]] — MCP 통합, Hooks 이벤트 시스템, SubAgents 생성, Agent Teams 패턴
- [[claude-code-agent-teams-tmux]] — Agent Teams 개념, tmux 사용 이유, 작업 분해 기준, Git Worktree 활용
- [[claude-code-loop-automation]] — /loop 사용 사례, 기본 구조, 재현성, 출력 리포트, 운영 주의점
- [[claude-md-guide]] — 4계층 강제력, Memory 4타입, 7가지 작성 테크닉, 3가지 스타터 템플릿
- [[claude-code-token-optimization]] — 4계층 토큰 구조, Context Rot, /compact 효과, 7가지 절약 전략
- [[claude-token-saving-tips]] — .claude/ 정리, 압축형 대화, jupytext, 세션 길이, .claudeignore 등 8가지 기법
- [[claude-code-enterprise-security-bedrock]] — 기업 도입 시 데이터·계정·네트워크·권한·감사·비용 6가지 결정사항
- [[claude-code-windows-wsl-tmux]] — WSL2 필요성, 4월 tmux 이슈, 프로젝트 배치, 트러블슈팅 가이드
- [[launchd-secret-management]] — macOS launchd 환경 시크릿 분리: ~/.zshrc 안 읽힘, plist 평문 안티 패턴, config/.env + run.sh source + chmod 600 표준 패턴, fine-grained PAT 권장
- [[vercel-cron-best-practices]] — Vercel Cron 5결정: Hobby 슬롯 제약 → 단일 라우트 + KST 날짜 분기, CRON_SECRET, middleware exempt, 멱등성 (createMany skipDuplicates / updateMany / 24h guard), Crons UI / Logs 운영
- [[zod-schema-per-entity]] — `lib/<entity>/schema.ts` 에 enum / label / Zod schema / 추론 타입 응집: server-client 검증 일관성 + Prisma enum SSOT 강제 + 점진 도입 (entity 당 30분~1시간)
- [[vercel-timeout-browser-direct-api]] — Vercel Hobby 60s lambda timeout escape hatch: 인증 사용자에게 API key 내려주고 브라우저에서 Anthropic SDK 직접 호출. 보안 부채 (DevTools 키 추출) + 회수 조건 + 키 회전 의무
- [[macos-tcc-full-disk-access]] — macOS Sonoma+ TCC 토스트 ("iTerm 이 다른 앱 데이터에 접근") 처리: 시스템 설정 → 전체 디스크 접근 + iTerm 완전 재시작. Claude Code 권한 팝업과 별개로 구분
- [[cron-nvm-node-path-trap]] — cron 이 `~/.zshrc` 안 읽어 NVM node 못 찾는 함정 (`env: node: No such file or directory`). crontab 상단 `PATH=` 명시 + 비교표 (cron / launchd / systemd / GitHub Actions)
- [[react-hook-form-zod-server-action]] — Next.js Server Actions + react-hook-form + Zod 동일 스키마 패턴: `useForm<z.input, unknown, z.output>` 3-제너릭 (resolvers v5 의 input/output 분리), FormData 재구성으로 server action 시그니처 보존, defense-in-depth
- [[shell-set-eu-topic-isolation]] — shell `set -eu` 와 multi-topic 파이프라인의 격리: 한 토픽 실패가 후속 전체 중단을 일으키는 부작용을 `if !` 한 줄 wrap 으로 차단. 의존 사슬 / 독립 N건 의 작업 단위 결정이 핵심
- [[csv-roundtrip-backup-restore]] — CSV round-trip 백업/복원: 외래키 ID + N:M `;-구분 ID 목록` 직렬화로 export → reset → import 시 PK/FK/N:M 매핑 그대로 복원. RFC 4180 미니 파서, "RESET" 텍스트 + browser confirm 2중 가드, @updatedAt 함정
- [[supabase-region-migration]] — Supabase 프로젝트 리전 마이그레이션 9 단계 (CSV 백업 → .env 백업 → 새 프로젝트 연결정보 → .env 교체 → `prisma migrate reset --force` → Auth URL → 로컬 import 검증 → commit → Vercel env 교체). Database password / anon key / service_role 혼동 함정 + URL percent-encoding + P3005 + auth.users 마이그레이션 불필요
- [[ssh-cli-toolkit-essentials]] — SSH 개발 환경의 CLI 필수 도구: tmux + ripgrep + fzf 우선 (Top 3) + bat/fd/lazygit/delta. mac/linux 양쪽 동일 명령. AI 가 만드는 `.md` 더미 + 코드 작업의 표준 워크플로 (`tmux new` → `rg --type md` → `glow -p $(fzf)` → `Ctrl+B d`)
- [[claude-mcp-server-scope-and-add-json]] — Claude Code `claude mcp add` 의 4 함정: `-e KEY=VALUE` 의 셸 토큰 분해 모호함 → `add-json` 우회 / 기본 `local` scope 의 디렉터리 격리 → `-s user` 필수 / 새 MCP 는 세션 시작 시만 로드 → 재시작 / 외부 MCP + API key 등록은 자동 승인 거부. `~/.claude.json` 의 `mcpServers` vs `projects.<path>.mcpServers` 분기
- [[ai-token-usage-cost-guard]] — AI 토큰 사용량 기록 + per-user 일일 비용 한도 가드 패턴: `usage_events` 테이블 + provider/model pricing (prefix 매칭 + provider default) + `ai-client` 가 `UsageInfo` 함께 반환 + 모든 entrypoint 한도 가드 + client-direct 우회 경로용 `/api/usage` reporting endpoint. UTC 자정 cutoff
- [[prompt-schema-pipeline-coupling]] — LLM 프롬프트 출력 스키마와 다운스트림 validator / 빌더 / 집계 간 결합 관리 패턴. 8가지 결합점 인벤토리 + 안티패턴 (validator 복붙 / 단계별 검증 비대칭 / cron silent) + "옛 OR 신" 둘 다 받는 validator 마이그레이션 흐름 + 가시성 신호 (today's run 0 commits = error)
- [[launchd-plist-symlink-from-project]] — launchd plist 마스터를 프로젝트 폴더에 두고 `~/Library/LaunchAgents/` 는 symlink (Homebrew services 패턴). 프로젝트 열었을 때 plist 가 보임·잊지 않음. install 서브커맨드 구현, `.gitignore` 필수 (절대 경로 박힘), rename 함정, `launchctl list` 출력 의미. **영구 비활성화는 unload 만으로 부족 — RunAtLoad/KeepAlive plist 는 재부팅 시 재로드되므로 symlink 제거가 곧 자동 시작 차단** (원본 plist 보존 → 무손실 원복)

- [[test-driven-agent-loop]] — 강건한 테스트 스위트로 코딩 에이전트에 대규모 작업 자율 위임. JustHTML 포팅 실증(Codex CLI+GPT-5.2, 8프롬프트→9,000줄, html5lib-tests 9,200 통과). API 우선 설계 + 결정론적 검증 게이트가 핵심

## 버그와 해결책 (bugs/)

- [[node-modules-symlink-copy-prisma]] — node_modules 폴더 카피 시 .bin/prisma 심볼릭 링크 풀려서 wasm ENOENT, rm -rf node_modules && npm install 로 재생성, cp -a / rsync -aH 예방
- [[yahoo-finance-concurrent-silent-fail]] — 30개 심볼 Promise.allSettled 동시 호출 시 일부 응답에 regularMarketPrice 누락 silent fail, worker pool 6 + 250ms 1회 재시도 + UI 가시성으로 수정
- [[prisma-connection-pool-vercel-supabase]] — Vercel + connection_limit=1 환경에서 8개 $transaction 병렬화로 P2024 풀 고갈, 같은 인스턴스 후속 요청까지 연쇄 실패. 불변 시계열은 createMany skipDuplicates 한 방으로, 무거운 쓰기는 click-path 에서 일별 cron 으로 분리
- [[gemini-2-0-flash-free-tier-blocked]] — 2026 봄 시점 `gemini-2.0-flash` 가 free tier 차단 (429 + `limit: 0`), `gemini-2.5-flash` 로 교체 + `<VENDOR>_MODEL` env override 패턴으로 재발 방지
- [[kis-cash-d2-settlement-buy-rejection]] — KIS 잔고 응답에서 `dnca_tot_amt` (D+0 출금가능) 만 매수가능 현금으로 사용해 D+2 정산 대기 매도분이 누락 → RiskManager 가 매수 차단. `prvs_rcdl_excc_amt` (D+2 가수도정산금액) 와 max() 처리 + 모의투자 fallback
- [[openclaw-coder-silent-3-layer]] — OpenClaw 코더 응답 무 3계층 디버깅: plugins.allow 미설정 (acpx runtime 차단) + 12일 묵은 좀비 ACP task (sqlite 직접 정리) + Anthropic OAuth 403 (진짜 원인). 모델 codex 로 변경 후 회복
- [[dict-get-default-no-bootstrap]] — Python `dict.get(key, default)` 가 dict 미갱신 → 부트스트랩 영구 실패. ht_trading 트레일링 스톱 1개월간 영구 비활성. `setdefault` 한 줄 교체
- [[utc-iso-date-kst-rollover]] — `new Date().toISOString().slice(0, 10)` 가 KST 새벽~오전에 어제 날짜로 떨어짐. 매일 07:00 cron 이 어제 게시본 silent overwrite. `Intl.DateTimeFormat('en-CA', { timeZone: 'Asia/Seoul' })` 로 수정. 모든 한국 운영 cron 에 재현 가능
- [[ndjson-stdout-parser-greedy-regex]] — AI CLI (cursor/claude -p) 의 stdout 파서가 NDJSON / envelope.result / fenced JSON 케이스에 깨짐. 탐욕 정규식 (`/\{[\s\S]*\}/`) 대신 단계적 4 경로 폴백 + 회귀 fixture. dev-blog 5/11 12토픽 누락 사고의 직접 원인
- [[nextjs16-use-server-non-async-export]] — Next.js 16 Turbopack 부터 `"use server"` 파일이 async function 외 export (객체/상수) 거부 → runtime 에러로 페이지 깨짐. typecheck 미감지. type-only export 만 안전
- [[grep-env-var-leak-to-chatlog]] — `grep "NOTION_API_KEY" ~/.zshrc` 진단 한 줄로 시크릿이 LLM 채팅 로그에 그대로 노출된 실 사고. 안전한 검증법은 `${#VAR}` / `[ -n "$VAR" ]` / `grep -q` 만 사용. 키는 즉시 폐기·회전
- [[highlights-action-validator-schema-drift]] — dev-blog 의 LLM rewrite 출력 스키마 (`action` → `if`/`do`/`verify` 3분해) 변경이 publisher 5종 + weekly + 일부 rewrite validator 갱신과 비동기로 진행되어 5/13 launchd 잡의 10개 토픽 publish 가 모두 silent skip. validator 를 둘 중 하나 허용으로 완화 (build-site 와 동형), 49 테스트 통과 후 publish/rewrite 재실행으로 복구
- [[kis-holiday-detection-bsop-date]] — ht_trading 공휴일 휴장 판정이 삼성전자 현재가 `bsop_date`(영업일자) 비교 방식이라 공휴일에도 당일 날짜 반환 → 휴장 감지 실패, KIS `APBK0919` 주문 거부 반복. KIS 국내휴장일조회 `CTCA0903R` 의 `opnd_yn` 으로 교체. 부가: 6시간째 떠 있던 라이브 데몬이 옛 코드 보유 → 재시작 필수
- [[reentry-after-full-liquidation-no-cooldown]] — ht_trading `ScoringStrategy` 가 트레일링스톱·손절 전량 청산 직후 같은 종목을 즉시 재매수 (신세계 15:10 매도 → 15:20 재매수, 10분). 분할 추가매수 throttle (`min_split_interval_minutes` 18h) 이 첫 매수를 면제하므로 flat 재진입 경로엔 가드 전무. 전량 청산만 매도시각 기록 + `_try_buy` flat 재진입 시 `reentry_cooldown_minutes`(60) 검사로 수정 (commit 70634aa)
- [[round-winrate-exit-type-undercount]] — upbit_trading 백테스트 리포트가 라운드 종료 유형을 `target`/`stop_loss` 로만 분류해 trailing_stop/time_exit/partial 종료가 누락 → "승률 0%, 목표달성 0회" 왜곡 (평균 수익률은 양수인 모순이 단서). `profit>0` 기준 승률 + `exit_breakdown` 별도 집계로 수정 (commit `b947351`). 통계 집계 분류가 enum 일부만 커버할 때의 일반 함정
- [[absolute-stop-loss-elif-dead-code]] — ht_trading `scoring_strategy.py` 의 절대 손절이 `if … elif` 분기 때문에 dead code. 라이브에서 벤치마크 (KOSPI) 가 항상 붙어 있어 `elif profit_pct <= -absolute_stop_loss_pct` 분기 도달 불가 → `absolute_stop_loss_pct: 0.10` 무효. 상대 손절 -15% → -20% 완화 (D 튜닝) 와 결합되어 *벤치마크 동반 하락기엔 어떤 손실도 컷 못 함*. 화신 -19% / GS -11% 미발동의 직접 원인

## 요약 (summaries/)

- [[mac-keyboard-shortcuts-for-windows-users]] — Command(⌘) 중심 구조, 복사/붙여넣기, 스크린샷, 창관리, 앱 전환 단축키

## 분석 (analyses/)

- [[openai-codex-cli-overview]] — OpenAI 터미널 경량 코딩 에이전트(Rust ~96%, ChatGPT 플랜/API 키). custom provider 로 로컬 Gemma 4 연결 실험: 로컬은 속도보다 first-pass 신뢰도가 중요, Apple Silicon Flash Attention freeze(>500 토큰) 함정. Claude Code 대안 비교축
- [[claude-code-source-leak-internals]] — 2026-03 Claude Code npm 소스맵 누출이 드러낸 내부 설계: anti-distillation(가짜 tool 주입), undercover 모드(내부 코드네임 제거·force-OFF 없음), frustration regex, 미출시 KAIROS 모드. + 에이전트 품질 회귀의 정량 측정법(Read:Edit 비율·인터럽트율) + skills 의 크로스툴 표준 수렴(OpenAI Codex CLI 가 파일시스템 skill 채택). confidence medium
- [[claude-code-session-jsonl-format]] — Claude Code 네이티브 세션 로그 `~/.claude/projects/<encoded-cwd>/<session-uuid>.jsonl` 포맷: cwd 슬래시→하이픈 인코딩, 1세션=1파일, type 분기(user/assistant/attachment/ai-title/last-prompt/mode/permission-mode/file-history-snapshot), `message.content` str|array 양면, user 레코드 키, `timestamp` UTC(Z)→KST 변환 함정. 주간보고·기억 에이전트의 원천 데이터(gieok `.md` 정제본 vs jsonl 원본 시크릿 노출차)
- [[backtest-timeframe-sensitivity]] — 추세필터·지표 신호의 손익 효과는 백테스트 봉 간격에 따라 뒤집힌다 (4시간봉 ON 압승 ↔ 30분봉 OFF 우세; 고빈도 봉일수록 SMA/크로스 노이즈). 검증은 반드시 운영 봉/주기로. 공정 비교(동일 OHLCV 1회 fetch 후 주입), `%` vs `%p` 구분, 수익률-MDD 트레이드오프 방법론
- [[research-write-agent-separation]] — LLM 콘텐츠 파이프라인의 research/write 분리: 진짜 레버는 단계 쪼개기가 아니라 조사 단계에 도구(WebFetch/WebSearch/git log)를 줘 입력 깊이 천장을 깨는 것. dossier 계약(모든 claim=evidence URL)이 hallucination 가드를 구조화, template/codex 결정론적 fallback. 실측: LWN 5·CVE 2건 등 13 evidence 로 700자 천장 돌파. 함정: 200자 quote 절단·RESEARCH_RAW_PATH 복구·Anubis 봇 차단·"배관 완료≠품질 완료"
- [[qualcomm-camera-kernel-isp]] — Qualcomm 카메라 커널(cam_isp/CAMSS) 구조·소스 입수·Exynos 비교: 커널 드라이버는 GPL 공개(opensource.samsung.com tar / CodeLinaro git clone)지만 CamX-CHI HAL 은 독점. cam_isp 골격(IFE/VFE/CSID, csid_pxl/rdi 리소스, SOF/EPOCH/BUBBLE 상태기계). Exynos = 삼성 Pablo(구 FIMC-IS), 칩(Snapdragon vs Exynos)별로 ISP 가 갈림
- [[zed-editor]] — Rust 기반 고속 코드 에디터: macOS/Windows/Linux 설치, SSH 원격 개발, AI provider(Claude/GPT/Gemini/Ollama) 연결 내장
- [[macos-launchagent-catchup-behavior]] — macOS LaunchAgent의 미실행 작업 캐치업 동작 (cron과의 차이)
- [[everything-claude-code]] — affaan-m/everything-claude-code (170k stars): 48 agents + 182 skills + AgentShield 102 룰 + Continuous Learning v2 + ECC 2.0 alpha (Rust) — Claude Code 하네스 성능 시스템
- [[nextjs-vercel-supabase-deployment]] — Next.js + Vercel + Supabase 통합 배포 7가지 결정: GitHub App 연동, 업로드 한도, force-dynamic, Pooler 모드 (Direct vs Transaction vs Session), 환경변수 import, .next/.gitignore
- [[github-search-api-topic-or-limitation]] — GitHub Repository Search API의 topic: 한정자는 OR 연산자 미지원, 카테고리별 N번 쿼리 + full_name dedupe 우회 (oss-radar Search 후보 0 → 422)
- [[meanflow-text-to-image]] — MeanFlow 프레임워크를 텍스트 조건부 T2I로 확장 (EMF, arXiv 2604.18168): discriminability·disentanglement의 중요성
- [[diffusion-snr-t-bias]] — 확산 모델 추론 시 발생하는 SNR-t 편향 규명과 웨이블릿 도메인 training-free 보정법 (arXiv 2604.16044)
- [[tstars-tryon-virtual-try-on]] — Taobao 상업용 가상 피팅: MMDiT 5B, 8카테고리, 단일 3.92s (arXiv 2604.19748)
- [[onevl-latent-reasoning]] — 자율주행 VLA의 잠재 CoT: 명시적 CoT 정확도 + 답변-only 지연시간 달성 (arXiv 2604.18486)
- [[soc-otf-sensor-to-ap]] — SoC On-The-Fly: Sensor→AP 직접 스트리밍 연결의 HW/SW 설계 주의사항
- [[openclaw-telegram-group-setup]] — Telegram 그룹 봇: Privacy Mode 비활성화, requireMention 설정, 포럼 토픽 vs 별도 봇 트레이드오프
- [[llada2-uni-unified-multimodal-diffusion]] — SigLIP-VQ + MoE dLLM + 확산 디코더로 이해·생성·편집을 단일 마스크 예측으로 통합 (arXiv 2604.20796)
- [[algorithmic-collusion-reinforcement-learning]] — Soft Actor-Critic 가격 게임: Q-learning 대비 100배 빠른 담합 수렴, 반독점 시사점 (arXiv 2604.15825)
- [[agentspex-agent-specification-language]] — YAML 선언형 에이전트 워크플로우 명세 언어: 모델 버전 업그레이드 내성 (+0.2% vs -6.8%), 형식 검증 가능 (arXiv 2604.13346)
- [[invitrovision-embryo-vlm]] — IVF 배아 캡셔닝용 PaliGemma-2 LoRA 적응: 1,000개로 ChatGPT 5.2 능가 (총점 0.67 vs 0.52) (arXiv 2604.21061)
- [[cointeract-hoi-video-synthesis]] — Human-Aware MoE + 비대칭 Co-Attention으로 손-물체 교합 제거, 추론 시 HOI 브랜치 zero-overhead 제거 (arXiv 2604.19636)
- [[llatisa-time-series-reasoning]] — 4단계 인지 계층 TSR 분류 + 시각화·수치 테이블 융합 VLM, 독점 모델 능가 (arXiv 2604.17295)
- [[prisma-decimal-nextjs-serialization]] — Prisma Decimal 타입이 Next.js Server→Client 직렬화 경계에서 오류를 일으키는 원인과 toNumber() 변환 수정 패턴
- [[agent-world-environment-synthesis]] — RUC+ByteDance의 자기 진화형 에이전트 훈련 아레나: 환경 다양성 스케일링 + 공진화로 14B 모델이 685B 능가 (arXiv 2604.18292)
- [[opengame-agentic-game-development]] — CUHK의 end-to-end 웹 게임 생성 에이전트: Template Skill+Debug Skill+GameCoder-27B, BH/VU/IA 평가 체계 (arXiv 2604.18394)
- [[agentic-world-modeling-taxonomy]] — L1 예측기·L2 시뮬레이터·L3 진화자 × 4가지 지배 법칙 체제: 400편+ 문헌 망라 세계 모델 역량 분류체계 (arXiv 2604.22748)
- [[near-future-policy-optimization-npo]] — RLVR 혼합 정책: 같은 훈련 런의 근미래 체크포인트로 S=Q/V 최대화, Qwen3-VL-8B +5.27pt (arXiv 2604.20733)
- [[microsoft-vibevoice-voice-ai]] — Microsoft 오픈소스 음성 AI: ASR 60분 단일패스+Rich Transcription, TTS 90분·4화자, Realtime 300ms 지연 (44k stars)
- [[prisma-generate-missing-error]] — Prisma 클라이언트 미생성으로 인한 findMany 에러: dev 스크립트에 prisma generate 추가하는 재발 방지 패턴
- [[onemancompany-heterogeneous-agents-organization]] — Talent–Container 분리 + E²R 트리 + 자기 진화로 50개 PRDBench 태스크 84.67% 성공 (Claude-4.5 대비 +15.48pt) (arXiv 2604.22446)
- [[pdf-text-extraction-vs-ocr]] — 스캔 PDF 침묵 실패 차단 패턴: 빈 텍스트 감지·OCR 폴백·재무제표 페이지 선별·연결/별도 구분
- [[anthropic-oauth-third-party-billing-trap]] — third-party CLI 의 Claude OAuth 는 별도 `extra_usage` pool 로 라우팅 → Pro 불가, Max+credits 필수, Sonnet/Opus 429. API key 직발급 또는 OpenRouter 우회 권장
- [[llm-provider-aggregator-vs-local-vs-hub]] — OpenRouter (클라우드 중계) vs Ollama (로컬 런타임) vs Hugging Face (모델 허브) 의 3가지 분류 + 시나리오별 선택 가이드
- [[personal-ai-agent-messaging-channels]] — Telegram 이 self-hosted personal AI agent 의 사실상 표준이 된 6가지 이유 + 한국 카톡 함정 + 시나리오별 채널 선택
- [[web-app-storage-without-db]] — DB 없이 시작하는 웹 앱: 데이터 4옵션 (시드 JSON/LocalStorage/IndexedDB/Cookie) + 이미지 4옵션 (public/base64/Blob/외부) + 단계적 도입 패턴
- [[vercel-friendly-database-options]] — Vercel 배포 친화적 DB 4종 비교: Neon (관계형 1순위) / Vercel KV (캐시) / Supabase (인증·스토리지 묶음) / Turso (가벼운 1인용·edge)
- [[multi-llm-provider-adapter-pattern]] — 추상화 라이브러리 (LangChain 등) 없이 Gemini/OpenAI/Anthropic 공식 SDK 를 어댑터 뒤에 두는 경량 멀티 LLM 패턴: `lib/ai/providers/<vendor>.ts` + `<VENDOR>_MODEL` env override
- [[claude-code-scheduled-tasks]] — Claude Code 데스크톱의 scheduled-tasks: `~/.claude/scheduled-tasks/<name>/SKILL.md`, 앱 종료 시에만 정지, /loop 와의 차이, 권한 사전 승인 / "Run now" 의 의미
- [[kakao-messaging-automation-options]] — 카카오톡 자동화 3 옵션 비교: Kakao Developers API (정식, 단톡 읽기 불가) / LOCO (비공식, 약관 위반·계정 정지 위험) / 하이브리드 (로컬 DB 읽기 + 나에게 보내기 송신). 시나리오별 추천
- [[kakaotalk-mac-data-locations]] — App Store v26.3.0 (`com.kakao.KakaoTalkMac`) 의 메시지 sqlcipher DB 위치: `~/Library/Containers/.../Application Support/com.kakao.KakaoTalkMac/<80hex>` + `-wal` + `-shm`. `[wal]` / `[magic]` / `[ext]` 휴리스틱
- [[kis-balance-api-fields]] — KIS 잔고 API summary 의 현금 필드 5종 비교: `dnca_tot_amt` (D+0 출금가능) vs `prvs_rcdl_excc_amt` (D+2 가수도정산금액 = 매도 미정산 포함 매수가능) vs `nxdy_excc_amt` / `tot_evlu_amt` / `evlu_pfls_smtl_amt`. 모의투자 fallback 패턴
- [[openclaw-acp-runtime-internals]] — OpenClaw ACP runtime 의 4가지 함정: plugins.allow 미설정 시 acpx register 차단 / 좀비 task 의 chicken-and-egg + sqlite 직접 정리 절차 / sessions.json stale ACP binding / wrapper 의 user 환경 상속 보안 함정. 4계층 디버깅 체크리스트
- [[mcp-config-secret-exposure-via-ps]] — Claude Code 가 MCP 설정을 `--mcp-config` 인라인 JSON 평문으로 전달 → `ps -ef` 에 `NOTION_API_KEY` 등 시크릿 지속 노출. 토큰 교체로 해결 안 됨 (재발). 노출 방지 4옵션 (upstream `--mcp-config-file` / MCP 제거 / ACP 비활성 / 환경 격리)
- [[esamp-latent-distilling-exploration]] — ESamp (arXiv 2604.24927): LLM 내부 hidden representation 예측 오차를 RND 영감 신규성 신호로 활용해 의미 공간 다양성을 강제하는 디코딩. KL-regularized closed-form 재가중 + 비동기 LD 파이프라인 < 5% 오버헤드, AIME25 Pass@64 +대폭
- [[aris-autonomous-research-harness]] — ARIS (arXiv 2605.03042): cross-family executor/reviewer 강제 + 65+ Markdown 스킬 + 5 워크플로우 + 3단계 assurance 로 단일 모델 family inductive bias 와 plausible-unsupported-success 차단하는 오픈소스 자율 연구 하네스
- [[ragflow-rag-engine]] — RAGFlow (79.8k stars, Apache-2.0, Python): RAG + Agent 결합 컨텍스트 엔진. Deep document understanding · template chunking · grounded citations · agentic + MCP · MinerU/Docling 파싱 · Confluence/S3/Notion/Drive 동기화. Elasticsearch ↔ Infinity 전환, Docker Compose 기동
- [[scrapling-adaptive-web-scraping]] — Scrapling (D4Vinci, BSD-3-Clause, Python): CSS/XPath/BeautifulSoup 셀렉터 단일 API + DynamicFetcher one-off + Stealthy/Async 세션 + HTTP/3 + element 관계 헬퍼·체이닝·텍스트 검색
- [[worldmonitor-global-intelligence-dashboard]] — World Monitor (53.6k stars, AGPL-3.0, TypeScript/Tauri): AI 뉴스 어그리게이션 + 지정학 모니터링 + 인프라 추적 통합 실시간 글로벌 인텔리전스 대시보드, "오픈소스 Palantir 류"
- [[partial-sell-rule-idempotency]] — 알고리즘 트레이딩 부분 매도 규칙 멱등성: 전량 매도 (자연 보호) vs 부분 매도 (직접 보호 필요). 3-step 패턴 (once-flag dict + state 영속화 + 청산 시 정리). ht_trading Rule 4 데드크로스 누적 발동 사례
- [[github-pages-base-path-pattern]] — GitHub Pages 사용자 페이지 (`<id>.github.io`, root) vs 프로젝트 페이지 (`/<repo>` 서브경로) 의 BASE_PATH 자동 대응. 빌드 시 prefix 주입 헬퍼 (`url`/`absoluteUrl`) + Actions 자동 결정. RSS / fetch / 이미지 모두 처리
- [[supabase-magic-link-single-user-allowlist]] — 1인용 Next.js 앱의 자체 비밀번호 → Supabase Auth 매직 링크 + `OWNER_EMAIL` allowlist 교체. 4-Gate (UI / send-magic-link PRIMARY / Supabase OTP / callback SECONDARY) + email enumeration 방지 (generic 200 응답) + cron 라우트 분리
- [[news-driven-market-signal-framework]] — 다일자 뉴스 코퍼스 → 시장 시그널 추출의 7-축 (매크로/지정학/시장흐름/섹터/기업이벤트/수급심리/톤) + 3-시나리오 (확률 가중 base/neutral/risk) + 섹터 비중 + 체크포인트. 일자별 병렬 서브에이전트 분해
- [[llm-news-prediction-pitfalls]] — LLM 시장 예측 6가지 함정 (검증 결여 / 확률 직관 / selection bias / stale 속도 / 자문 회색지대 / 단일 인과 추론). 결과 영속화 부적절, 방법론·함정만 영속화
- [[oauth-refresh-token-rotation-multi-client]] — 회전형(rotating) OAuth refresh token 을 같은 계정으로 여러 클라이언트(OpenClaw·Hermes·codex CLI)가 각자 복사해 쓰면 한쪽 갱신이 다른 쪽을 무효화하는 핑퐁. 진단 함정 (`status` 의 "ok expires" 는 로컬 만료시각, 서버측 무효화는 raw log 401), fallback 이 같은 provider 뿐이면 무의미, 공유 방식 4비교 (복사 위험 / 파일 참조 / symlink / 독립 device-flow=정답)
- [[multi-profile-cli-agent-isolation]] — CLI agent 멀티 프로필 셋업의 4함정: OAuth 토큰 공유는 symlink (refresh-token 회전 충돌 회피) / Keychain 인증은 HOME 격리에 깨짐 → wrapper 로 HOME 복원 / hermes 등 agent 는 `.bashrc`·`.bash_profile` 만 source (zsh init 무시) / `--clone` 후 `.env` reconfigure 필수
- [[holding-transaction-cost-basis-design]] — 보유 종목 매수/매도 거래 추적 4결정: 한국 양도세 표준 가중평균 (수수료 취득원가 포함) / SELL row 의 `realizedGain` 컬럼 동결 / 거래 삭제는 효과 역연산 / 계좌·거래 통화 일치 시에만 cashBalance 자동 갱신. MVP/풀구현/입력만 점진 도입
- [[scoring-system-ic-validation]] — 트레이딩 스코어 시스템의 IC (Information Coefficient) 검증 방법론: Pearson/Spearman, 컴포넌트별 분해, cutoff 시뮬레이션, AND vs OR 게이팅, regime 안정성. 한국 시장 검증 결과 (단기 모멘텀/RSI 안정구간 음수 IC, 양봉·거래량 sweet 최강 알파)
- [[dca-trailing-stop-tuning]] — DCA·Trailing Stop 자동매매 튜닝 5 레버 (trailing 거리 / cooldown / max_round_days + 계단식 / partial profit / tighten on weakness) + 운영 로그 진단 6단계. 즉시 vs Paper 검증 vs 중기 분류, 부분 매도 멱등성 가드, 추세 필터 양면성 (효과는 봉 간격 따라 뒤집힘 → 운영 봉 검증 필수)
- [[terminal-markdown-viewer-tools]] — 터미널·CLI 마크다운 뷰어 비교 (glow / mdcat / frogmouth / bat / neovim + render-markdown.nvim / markdown-preview.nvim). Mermaid SVG 의 터미널 본질적 한계 (코드블록 → ASCII → 외부 뷰어 → 인라인 이미지 4단계 우회). SSH 환경에서 Tauri/Electron GUI 부적합
- [[financial-health-composite-scores]] — 재무 건전성 합성 스코어 3종 (Altman Z / Piotroski F / Beneish M) + 5 카테고리 (profitability/liquidity/leverage/efficiency/cash) 룰 기반 risk flag. LLM 호출 0, 한국 시장 적용 시 Z 의 절대값 데이터 누락 + Beneish 의 false positive + F 의 insufficient fallback 처리
- [[macos-disk-cleanup-cache-classification]] — macOS 캐시 3 카테고리 (자동 재생성 / 순수 회수 / 다음 사용 시 재다운로드) + Claude Desktop 9.8G footprint 분해 (vm_bundles 8.4G = Cowork Linux VM, Cache_Data 1.2G, claude-code 본체 212M). depth 1 만 보고 결론 금지, 사용자 컨펌 워크플로우, 첫 운영 케이스 3.23G 회수
- [[polling-interval-vs-bar-interval]] — 라이브 트레이딩 폴링 주기 (cycle interval) 와 봉 단위 (bar_interval) 의 정합성: 일봉 + 10분 폴링이면 47회 중복 평가, 폴링 단축은 알파 0 증가 + API 호출만 ↑. 폴링 단축이 의미를 가지려면 bar_interval 도 함께 분봉으로 내려야. 일봉 유지 시 진짜 레버는 진입/청산 타이밍 (시초가 회피, 종가 근처 분할)
- [[llm-content-quality-guards]] — LLM 자동 콘텐츠 발행의 5가지 품질 가드 (토픽 중복 / action 일반성 / hallucination / 저신호 부풀리기 / 비-한글 CJK 혼입). 프롬프트 룰 + draft 메타 + publish 안전망의 defense-in-depth. CJK 혼입은 한국어 강제 프롬프트의 만성 함정 — `auditPostQuality` post-rewrite 검출 + stdout 교정·publish 재실행 4단계 복구
- [[mint-lora-serving-infrastructure]] — MinT: 백만 LoRA 어댑터 학습·서빙 관리형 인프라. 어댑터 리비전 중심 재설계, Scale Up (Megatron + R3 router replay) / Scale Down (time-sliced 다정책) / Scale Out (3계층 캐시 10⁶ 정책). 4B 어댑터 핸드오프 18.3×, 다중 정책 GRPO 1.77× 가속
- [[gin-vue-admin-mcp-fullstack]] — flipped-aurora/gin-vue-admin (24,673 stars): Go(Gin) + Vue 3 풀스택 엔터프라이즈 어드민. AI 코드 생성기 + Casbin 3단계 권한 + 내장 MCP 서버. MCP 가 IDE 를 넘어 백오피스 운영 인터페이스로 침투하는 신호
- [[macos-tcc-documents-popup-diagnosis]] — 갑작스레 시작된 "python3.12 가 문서 폴더 접근" TCC 팝업 5단계 진단 절차 (PID Python.app 번들 탐지 / 부모·가동시점 / 코드 참조 / fs_usage / 시스템 TCC.db mtime). "왜 갑자기" 의 2 원인 (Apple 백그라운드 정책 푸시 / 며칠 만에 처음 호출된 코드 경로). iTerm 셸 → 다른 앱 sandbox 시나리오인 [[macos-tcc-full-disk-access]] 와 다른 격
- [[claude-code-tui-navigation-and-instance-isolation]] — CC TUI 화면 전환 (`Esc` / `Ctrl+B` FleetView) + 다중 인스턴스 격리 모델 (각 CLI 가 독립 프로세스라 IPC 없음). 한 인스턴스 안의 자식 (서브에이전트·--bg·worktree) 만 모니터링 가능. `--bg` 가 부모-자식이 아닌 별도 인스턴스라는 함정, `/config` 가 인스턴스가 아니라는 오해, 다중 창 묶기 5가지 외부 경로 (파일·memory·CLAUDE_JOB_DIR·MCP·tmux)
- [[su-01-olympiad-reasoning]] — 30B-A3B (활성 3B) 모델이 「역퍼플렉시티 SFT + Coarse RL + Refined RL (DeepSeekMath-V2 보상 + 자기수정 20% / 경험재생 25% 버퍼) + TTS 30사이클 × 10회」 의 *컴포넌트 재배열* 만으로 IMO 2025 / USAMO 2026 금메달 (35점 동점 1위) 달성 (arXiv 2605.13301)
- [[python-pptx-design-token-pipeline]] — JSON/YAML + 디자인 토큰 → 편집 가능 PPTX 자동 생성 범용 패턴: PPTX 라이브러리 5종 비교 (python-pptx 1순위 + OOXML 직접 작성으로 콤보·레이더 보완), 이중 입력 어댑터 (YAML / CSS :root), role resolver, 한글 ea/cs typeface fix (`Apple SD Gothic Neo` 미설정 시 macOS 명조체로 fallback), EMU 좌표 환산, qlmanage 첫 슬라이드 한계 (CI 에 LibreOffice 헤드리스 필수), KPI wrap 함정
- [[llm-newsletter-rewrite-metadata-grounding]] — LLM 뉴스레터 rewrite 의 메타데이터 그라운딩 베스트 프랙티스 10 룰: candidateBodies 종류별 처리 (commit message vs 백포트 목록), history.previousVersion vs previouslySeenAt 차별, fromMaintainer 권한 단서, maintainerComments 톤 3분류, action 조건절+검증단서 강제, 본문 4섹션·priority 분포·summary 2문장 제약, 입력 키 보존·candidateBodies 출력 금지. dev-blog Linux Daily Rewrite 프롬프트의 진화에서 일반화
- [[cursor-agent-cli-overview]] — Cursor 의 비대화형 `cursor-agent -p`: `claude -p` 와 옵션 호환 (print / output-format / model / continue / resume), 멀티 모델 (gpt-5, sonnet-4) + `CURSOR_API_KEY` 인증. `resolveAiAdapter` 한 함수 분기로 claude/cursor 옵셔널 전환. provider 다중화는 *완화* 일 뿐 — fallback 체인 + retry-with-dump 와 같이 가야
- [[karpathy-claude-md-skills]] — multica-ai/andrej-karpathy-skills (Karpathy CLAUDE.md 큐레이션): SKILL.md frontmatter 1줄 자동 로드, `code-organization` / `git-pre-commit` / `python-style` / `nanochat-design` 4 카테고리. CLAUDE.md 가 컨벤션 강제 인터페이스가 되어가는 흐름
- [[llm-json-parse-retry-with-dump]] — LLM JSON 출력 파싱 실패의 1회 재시도 + 원문 덤프 패턴: `runAiAdapterAndParse(prompt, { logLabel, maxAttempts=2, failureDir })` 단일 진입점. 어댑터·파싱 묶음 + 파싱 실패 시 `logs/ai-rewrite-failures/<ts>-<label>-attemptN.txt` 에 raw 텍스트 덤프 → 사후 분석 가능. dev-blog 의 6 rewrite 호출부 일괄 적용 (5/18 commit `a42d470`)
- [[api-circuit-breaker-trading-pattern]] — 외부 API 연속 오류 시 주문 중지 서킷브레이커 패턴: 연속 N회 임계치 카운터 + `api_halted` 플래그 + `submit_order` 선두 차단 + 캐시 TTL 경고 + 텔레그램 halt/회복 1회 알림. ht_trading KIS 구현에서 일반화 (2026-05-30)
- [[scoring-version-comparison-methodology]] — 알고리즘 교체 후 컷오프 캘리브레이션 vs 진짜 알파 변화 판정: Spearman ρ + Top-N 교집합 2지표, 백테스트의 한계. V3 리버트 결정 추가 (모멘텀 전략 vs IC 검증 집단 충돌, 선택적 재적용 EPS→earnings_yield + 캔들 세분화)
- [[eps-vs-earnings-yield]] — EPS 절대값 점수의 고가주 편향 (같은 EPS도 비싼 주식이 부당하게 유리) → EY(EPS/주가 비율)로 정규화해 종목 간 비교 가능. "스케일 다른 절대값을 점수 차원에 직접 매핑하지 마라"의 일반 원리 (거래량·시총·매출도 동일)
- [[stock-screening-score-design]] — 종목 스크리닝 점수 설계 3함정: ① 단순 합산이 결함을 가린다 → 안정성은 가산점 아닌 **게이트**(적자·고부채 점수 무관 제외, None 통과, watchlist 우회) ② 절대 임계값 섹터 편향 → **외부 벤치마크** 상대화 (후보군 내부 상대화는 표본·선택 편향 함정) ③ 백테스트 **생존 편향** — 승자(고점수 재등장)만 저장하면 IC 왜곡 → 전체 universe 저장이 선결
