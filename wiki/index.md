---
title: Wiki Index
updated: 2026-05-15T00:00:00+09:00
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

## 프로젝트 (projects/)

- [[gieok]] — gieok 설치 상세, CC Hook 이벤트, LaunchAgent 스케줄, 기능별 LLM 필요 여부, 알려진 버그
- [[ht-trading]] — 알고리즘 트레이딩 시스템: ScoringStrategy 이중 스케일(40/100점), 버그 수정 4건(datetime.now/RSI/TrailingSell/split throttle), 백로그 B1-B4. V3 점수 알고리즘 (IC 검증 기반 재설계: 양봉/거래량 가중 ↑ MA ↓ EPS 절대값 → earnings_yield) + A/C/D 튜닝 (체결률 / 트레일링 둔감화 / 상대손절 완화)
- [[openclaw]] — AI 에이전트 자동화 도구: 다중 에이전트(main/english/coder) 구성, Telegram 그룹 Privacy Mode 설정, 라우팅 버그 트러블슈팅
- [[oss-radar]] — 주간 GitHub OSS 발굴 파이프라인: discover→fetch→analyze→publish 6단계, star_velocity 스코어링, env -u CLAUDECODE 중첩세션 방지, GitHub topic OR 미지원 우회, config/.env 시크릿 분리
- [[ai-shorts-production-with-claude-code]] — Claude Code로 AI 쇼츠 영상 대량 제작 흐름, Claude/사람 역할 분리
- [[japa-asset-dashboard]] — 1인 전용 자산 통합 대시보드: Next.js 16 + Prisma + Supabase + Yahoo Finance + 멀티 LLM, Supabase Auth 매직 링크 + RHF/Zod 폼 + BUY/SELL Transaction 추적 (가중평균 + realizedGain 동결)
- [[finance-analysis-nextjs]] — 한국 기업 재무분석 대시보드: PDF/JSON/DART API 입력, AI 멀티 프로바이더 구조화, 12 슬라이드 + AI/수동 밸류에이션, M&A 활용 계획. 비판적 검토 7개 갭 + Phase 1~7 일괄 구현 (F-Score / CSV / 토큰 비용 가드 / risk flag / 공유 링크 / 시나리오 패널 / vitest 32 테스트)
- [[wardrobe]] — 옷장 매칭 웹 앱 (Next.js 15 + Tailwind v4 + LocalStorage MVP): 사이드바 레이아웃, 시드 JSON, DB 없이 Step 1 단계로 시작
- [[kakao-mem]] — Mac KakaoTalk 메모리 CLI (Python + `kakaocli`): 어댑터 격리 / message_id sha256 dedup / launchd 자동화. 8개 잠재 이슈와 직접 통신 옵션 분석
- [[kakao-db]] — Mac KakaoTalk 로컬 sqlcipher DB + LOCO 어댑터 (Rust): 5 결정 (Rust 단일 / OSS LOCO / 단발 CLI + cron / Keychain / App Store 26.3.0), M0 완료, M1 inspect 휴리스틱 진입
- [[kernel-digest]] — 리눅스 커널 일일 다이제스트 (계획 단계, M0 완료): 4축 콘텐츠 / 8 데이터 소스 / Collectors→AI Stage→Publisher 파이프라인 / 종량제 API 금지 + 구독제 LLM (`claude -p`/`openclaw`) 만 사용 / 토픽-플러그인 확장형
- [[dev-blog]] — AI 보조 한국어 엔지니어링 일일 뉴스레터: Node 20+ 표준 API 만 사용 의존성 0개, claude-CLI 어댑터 + template fallback, cron-on-laptop + GitHub Actions 빌드, BASE_PATH 자동 대응. Multi-topic 가동 (Linux + Android + Lens 8 + OSS Trending + OSS Curation = 10토픽). 5/14 1토픽 quality-guard 정상 차단 → stdout 교정·publish 재실행 4단계 복구 (격리 패턴 첫 운영 성공)
- [[hermes]] — Nous Research personal AI agent macOS 셋업: default + 코딩 전용 `maccoder` 두 프로필, OAuth symlink 공유, claude CLI HOME 격리 우회 wrapper, Telegram 별도 봇
- [[upbit-trading]] — Upbit 암호화폐 무한매수법 자동매매 (Python + launchd, 40분할 DCA + Trailing Stop): 70일 운영 평균 +5.20% (10라운드), 5개 키 튜닝 적용 (trailing 2.5% / cooldown 6h / max_round_days 45 + 계단식 15/30/45 / partial_profit ON / tighten_on_weakness ON)
- [[disk-monitor]] — 일일 디스크 사용량 모니터링 (Python 단일 파일 + launchd 09:00). 데이터는 `~/Library/Application Support/disk-monitor/` (코드/데이터 분리), plist 마스터는 프로젝트 폴더 (Homebrew 스타일 symlink), 자동 정리 금지·사용자 컨펌 워크플로우. 첫 운영 3.23G 회수

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
- [[launchd-plist-symlink-from-project]] — launchd plist 마스터를 프로젝트 폴더에 두고 `~/Library/LaunchAgents/` 는 symlink (Homebrew services 패턴). 프로젝트 열었을 때 plist 가 보임·잊지 않음. install 서브커맨드 구현, `.gitignore` 필수 (절대 경로 박힘), rename 함정, `launchctl list` 출력 의미

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

## 요약 (summaries/)

- [[mac-keyboard-shortcuts-for-windows-users]] — Command(⌘) 중심 구조, 복사/붙여넣기, 스크린샷, 창관리, 앱 전환 단축키

## 분석 (analyses/)

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
- [[multi-profile-cli-agent-isolation]] — CLI agent 멀티 프로필 셋업의 4함정: OAuth 토큰 공유는 symlink (refresh-token 회전 충돌 회피) / Keychain 인증은 HOME 격리에 깨짐 → wrapper 로 HOME 복원 / hermes 등 agent 는 `.bashrc`·`.bash_profile` 만 source (zsh init 무시) / `--clone` 후 `.env` reconfigure 필수
- [[holding-transaction-cost-basis-design]] — 보유 종목 매수/매도 거래 추적 4결정: 한국 양도세 표준 가중평균 (수수료 취득원가 포함) / SELL row 의 `realizedGain` 컬럼 동결 / 거래 삭제는 효과 역연산 / 계좌·거래 통화 일치 시에만 cashBalance 자동 갱신. MVP/풀구현/입력만 점진 도입
- [[scoring-system-ic-validation]] — 트레이딩 스코어 시스템의 IC (Information Coefficient) 검증 방법론: Pearson/Spearman, 컴포넌트별 분해, cutoff 시뮬레이션, AND vs OR 게이팅, regime 안정성. 한국 시장 검증 결과 (단기 모멘텀/RSI 안정구간 음수 IC, 양봉·거래량 sweet 최강 알파)
- [[dca-trailing-stop-tuning]] — DCA·Trailing Stop 자동매매 튜닝 5 레버 (trailing 거리 / cooldown / max_round_days + 계단식 / partial profit / tighten on weakness) + 운영 로그 진단 6단계. 즉시 vs Paper 검증 vs 중기 분류, 부분 매도 멱등성 가드, 추세 필터 양면성
- [[terminal-markdown-viewer-tools]] — 터미널·CLI 마크다운 뷰어 비교 (glow / mdcat / frogmouth / bat / neovim + render-markdown.nvim / markdown-preview.nvim). Mermaid SVG 의 터미널 본질적 한계 (코드블록 → ASCII → 외부 뷰어 → 인라인 이미지 4단계 우회). SSH 환경에서 Tauri/Electron GUI 부적합
- [[financial-health-composite-scores]] — 재무 건전성 합성 스코어 3종 (Altman Z / Piotroski F / Beneish M) + 5 카테고리 (profitability/liquidity/leverage/efficiency/cash) 룰 기반 risk flag. LLM 호출 0, 한국 시장 적용 시 Z 의 절대값 데이터 누락 + Beneish 의 false positive + F 의 insufficient fallback 처리
- [[polling-interval-vs-bar-interval]] — 라이브 트레이딩 폴링 주기 (cycle interval) 와 봉 단위 (bar_interval) 의 정합성: 일봉 + 10분 폴링이면 47회 중복 평가, 폴링 단축은 알파 0 증가 + API 호출만 ↑. 폴링 단축이 의미를 가지려면 bar_interval 도 함께 분봉으로 내려야. 일봉 유지 시 진짜 레버는 진입/청산 타이밍 (시초가 회피, 종가 근처 분할)
- [[llm-content-quality-guards]] — LLM 자동 콘텐츠 발행의 5가지 품질 가드 (토픽 중복 / action 일반성 / hallucination / 저신호 부풀리기 / 비-한글 CJK 혼입). 프롬프트 룰 + draft 메타 + publish 안전망의 defense-in-depth. CJK 혼입은 한국어 강제 프롬프트의 만성 함정 — `auditPostQuality` post-rewrite 검출 + stdout 교정·publish 재실행 4단계 복구
