---
title: Wiki Index
updated: 2026-05-07T13:05:00+09:00
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
- [[ht-trading]] — 알고리즘 트레이딩 시스템: ScoringStrategy 이중 스케일(40/100점), 버그 수정 4건(datetime.now/RSI/TrailingSell/split throttle), 백로그 B1-B4
- [[openclaw]] — AI 에이전트 자동화 도구: 다중 에이전트(main/english/coder) 구성, Telegram 그룹 Privacy Mode 설정, 라우팅 버그 트러블슈팅
- [[oss-radar]] — 주간 GitHub OSS 발굴 파이프라인: discover→fetch→analyze→publish 6단계, star_velocity 스코어링, env -u CLAUDECODE 중첩세션 방지, GitHub topic OR 미지원 우회, config/.env 시크릿 분리
- [[ai-shorts-production-with-claude-code]] — Claude Code로 AI 쇼츠 영상 대량 제작 흐름, Claude/사람 역할 분리
- [[japa-asset-dashboard]] — 1인 전용 자산 통합 대시보드: Next.js 16 + Prisma + Supabase + Yahoo Finance, force-dynamic + Transaction pooler + HMAC-SHA256 단일 사용자 인증
- [[finance-analysis-nextjs]] — 한국 기업 재무분석 대시보드: PDF/JSON/DART API 입력, AI 멀티 프로바이더 구조화, 12 슬라이드 + AI/수동 밸류에이션, M&A 활용 계획
- [[wardrobe]] — 옷장 매칭 웹 앱 (Next.js 15 + Tailwind v4 + LocalStorage MVP): 사이드바 레이아웃, 시드 JSON, DB 없이 Step 1 단계로 시작
- [[kakao-mem]] — Mac KakaoTalk 메모리 CLI (Python + `kakaocli`): 어댑터 격리 / message_id sha256 dedup / launchd 자동화. 8개 잠재 이슈와 직접 통신 옵션 분석
- [[kakao-db]] — Mac KakaoTalk 로컬 sqlcipher DB + LOCO 어댑터 (Rust): 5 결정 (Rust 단일 / OSS LOCO / 단발 CLI + cron / Keychain / App Store 26.3.0), M0 완료, M1 inspect 휴리스틱 진입
- [[kernel-digest]] — 리눅스 커널 일일 다이제스트 (계획 단계, M0 완료): 4축 콘텐츠 / 8 데이터 소스 / Collectors→AI Stage→Publisher 파이프라인 / 종량제 API 금지 + 구독제 LLM (`claude -p`/`openclaw`) 만 사용 / 토픽-플러그인 확장형

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

## 버그와 해결책 (bugs/)

- [[node-modules-symlink-copy-prisma]] — node_modules 폴더 카피 시 .bin/prisma 심볼릭 링크 풀려서 wasm ENOENT, rm -rf node_modules && npm install 로 재생성, cp -a / rsync -aH 예방
- [[yahoo-finance-concurrent-silent-fail]] — 30개 심볼 Promise.allSettled 동시 호출 시 일부 응답에 regularMarketPrice 누락 silent fail, worker pool 6 + 250ms 1회 재시도 + UI 가시성으로 수정
- [[prisma-connection-pool-vercel-supabase]] — Vercel + connection_limit=1 환경에서 8개 $transaction 병렬화로 P2024 풀 고갈, 같은 인스턴스 후속 요청까지 연쇄 실패. 불변 시계열은 createMany skipDuplicates 한 방으로, 무거운 쓰기는 click-path 에서 일별 cron 으로 분리
- [[gemini-2-0-flash-free-tier-blocked]] — 2026 봄 시점 `gemini-2.0-flash` 가 free tier 차단 (429 + `limit: 0`), `gemini-2.5-flash` 로 교체 + `<VENDOR>_MODEL` env override 패턴으로 재발 방지
- [[kis-cash-d2-settlement-buy-rejection]] — KIS 잔고 응답에서 `dnca_tot_amt` (D+0 출금가능) 만 매수가능 현금으로 사용해 D+2 정산 대기 매도분이 누락 → RiskManager 가 매수 차단. `prvs_rcdl_excc_amt` (D+2 가수도정산금액) 와 max() 처리 + 모의투자 fallback
- [[openclaw-coder-silent-3-layer]] — OpenClaw 코더 응답 무 3계층 디버깅: plugins.allow 미설정 (acpx runtime 차단) + 12일 묵은 좀비 ACP task (sqlite 직접 정리) + Anthropic OAuth 403 (진짜 원인). 모델 codex 로 변경 후 회복

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
