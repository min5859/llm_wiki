# Wiki Index

수집 도메인: **ai-agent** (AI 에이전트 활용) · **trading** (트레이딩·투자 시스템)

## ai-agent

### concepts

- [[ai-usage-philosophy]] — AI 사용법 철학 — 언어화 능력·컨텍스트 설계·리버럴 아츠
- [[claude-code-skills-plugins]] — Claude Code 스킬 및 공식 플러그인 완전 가이드
- [[gieok]] — gieok — Claude Code 세컨드 브레인
- [[hermes-agent]] — Hermes Agent — Nous Research 의 self-hosted personal AI agent
- [[openclaw-agent-architecture]] — OpenClaw 에이전트 아키텍처 (현재 구조 + 확장 방향)

### patterns

- [[claude-code-token-optimization]] — Claude Code 토큰 최적화 — 소비 절반 줄이기
- [[launchd-plist-symlink-from-project]] — launchd plist 프로젝트 폴더 마스터 + ~/Library/LaunchAgents symlink 패턴 (Homebrew 스타일)
- [[launchd-secret-management]] — macOS launchd 환경 시크릿 분리 패턴 — ~/.zshrc 는 안 읽힌다
- [[llm-json-parse-retry-with-dump]] — LLM JSON 파싱 실패 시 raw 응답 덤프 + 재시도 패턴
- [[macos-tcc-full-disk-access]] — macOS TCC: 터미널이 다른 앱 sandbox 에 접근할 때 토스트 팝업 처리
- [[prompt-schema-pipeline-coupling]] — LLM 프롬프트 출력 스키마와 다운스트림 validator 간 결합 관리
- [[shell-set-eu-topic-isolation]] — shell 의 set -eu 와 multi-topic 파이프라인의 격리 패턴

### analyses

- [[ai-coding-agent-cost-and-context-patterns]] — AI 코딩 에이전트의 비용·컨텍스트 설계 패턴 — 강/약 모델 분리와 context rot 완화
- [[anthropic-oauth-third-party-billing-trap]] — Anthropic OAuth 의 third-party 빌링 함정 — Claude Max 구독을 외부 CLI 가 재사용한다는 오해
- [[build-vs-fork-personal-tool]] — 0부터 빌드 vs 오픈소스 포크 — 차별점이 사라질 때의 의사결정
- [[claude-code-source-leak-internals]] — Claude Code 소스 누출이 드러낸 에이전트 내부 설계 — anti-distillation · undercover · 회귀 측정
- [[everything-claude-code]] — everything-claude-code (ECC) — Claude Code 하네스 성능 시스템
- [[karpathy-claude-md-skills]] — multica-ai/andrej-karpathy-skills — CLAUDE.md 4원칙 가이드
- [[llm-content-quality-guards]] — LLM 기반 자동 콘텐츠 발행의 5 가지 품질 가드
- [[llm-newsletter-rewrite-metadata-grounding]] — LLM 뉴스레터 rewrite 의 메타데이터 그라운딩 베스트 프랙티스
- [[llm-provider-aggregator-vs-local-vs-hub]] — OpenRouter vs Ollama vs Hugging Face — LLM 백엔드 3가지 분류
- [[macos-launchagent-catchup-behavior]] — macOS LaunchAgent — 미실행 작업 캐치업 동작
- [[mcp-config-secret-exposure-via-ps]] — MCP 토큰이 `--mcp-config` 인자로 평문 노출되는 패턴 (`ps -ef` 가시성)
- [[multi-llm-provider-adapter-pattern]] — 멀티 LLM provider 어댑터 패턴 — 공식 SDK 직접 사용
- [[multi-profile-cli-agent-isolation]] — 멀티 프로필 CLI agent 격리 — OAuth 공유 / HOME 격리 우회 / shell init 함정
- [[oauth-refresh-token-rotation-multi-client]] — 회전형 OAuth refresh token 을 여러 클라이언트가 공유할 때의 쟁탈 함정
- [[openai-codex-cli-overview]] — OpenAI Codex CLI — 터미널 경량 코딩 에이전트와 로컬 모델 연결
- [[openclaw-acp-runtime-internals]] — OpenClaw ACP Runtime 내부 구조 — plugins.allow / 좀비 task / sessions.json / wrapper 환경 상속
- [[openclaw-telegram-group-setup]] — OpenClaw Telegram 그룹 봇 설정 — Privacy Mode, requireMention, 다중 에이전트
- [[research-write-agent-separation]] — Research / Write 에이전트 분리 — LLM 콘텐츠 파이프라인의 도구 기반 조사 격상
- [[self-hosted-agent-webui-integration]] — 셀프호스팅 에이전트에 커스텀 UI 붙이기 — 내부 직결 vs OpenAI 호환 HTTP API

### decisions

- [[openclaw-coder-default-model-codex]] — OpenClaw 코더 default 모델: Anthropic 대신 Codex (GPT-5.5)

### bugs

- [[highlights-action-validator-schema-drift]] — publish validator 가 옛 스키마(action) 만 강제해 신 스키마(if/do/verify) 게시 전부 실패
- [[ndjson-stdout-parser-greedy-regex]] — AI CLI 어댑터의 stdout 파서가 NDJSON·envelope·잡음 텍스트에 깨지는 함정
- [[openclaw-coder-silent-3-layer]] — OpenClaw 코더 응답 무 — 3계층 디버깅 (plugins.allow / 좀비 task / OAuth 403)

### projects

- [[agent-weekly]] — agent-weekly — Claude 대화 기록 기반 주간보고 자동작성 에이전트
- [[dev-blog]] — dev-blog — AI 보조 한국어 엔지니어링 뉴스레터 시스템
- [[gieok]] — gieok — 프로젝트 설계 상세
- [[hermes-dashboard]] — hermes-dashboard — Hermes 다중 에이전트 메신저 대시보드 (해커톤)
- [[hermes]] — hermes — Nous Research personal AI agent 셋업 (개인 머신)
- [[kakao-db]] — kakao-db — Mac KakaoTalk 로컬 DB + LOCO 어댑터 (Rust)
- [[kakao-mem]] — kakao-mem — Mac KakaoTalk 메모리 CLI
- [[openclaw]] — OpenClaw — AI 에이전트 자동화 도구
- [[oss-radar]] — oss-radar — 주간 GitHub OSS 발굴 파이프라인

## trading

### patterns

- [[csv-roundtrip-backup-restore]] — CSV round-trip 백업·복원 — 외래키 ID 와 N:M 보존
- [[post-bugfix-reverification-data-cutoff]] — 데이터 품질 버그 수정 후 재검증 — 평가 도구도 수정일 이후로 필터해야

### analyses

- [[backtest-timeframe-sensitivity]] — 백테스트 봉 간격 민감도 — 지표 신호 효과는 timeframe 따라 뒤집힌다 + 공정 비교 방법론
- [[dca-trailing-stop-tuning]] — DCA·트레일링 스톱 튜닝 — 운영 로그 기반 진단·개선 패턴
- [[holding-transaction-cost-basis-design]] — 보유 종목 매수/매도 거래 추적 설계 — 평균단가·실현손익 동결·삭제 역연산
- [[kis-balance-api-fields]] — KIS 잔고 API 응답의 현금 필드 의미 (예수금 vs 매수가능)
- [[llm-news-prediction-pitfalls]] — LLM 의 뉴스 기반 시장 예측 — 6가지 함정과 한계
- [[news-driven-market-signal-framework]] — 뉴스 기반 시장 시그널 추출 프레임워크 (7-축 + 3-시나리오)
- [[partial-sell-rule-idempotency]] — 알고리즘 트레이딩의 부분 매도 규칙 멱등성 패턴
- [[polling-interval-vs-bar-interval]] — 폴링 주기 vs 봉 단위 — 알고리즘 트레이딩의 시간 정합성
- [[risk-control-exemption-and-failed-attempt-accounting]] — 안전장치가 손실을 키울 때 — 리스크 감축 주문 면제와 실패 시도 회계
- [[scoring-system-ic-validation]] — 스코어 시스템의 IC (Information Coefficient) 검증 방법론
- [[scoring-version-comparison-methodology]] — 스코어 알고리즘 두 버전 비교 — 컷오프 캘리브레이션 vs 진짜 알파 변화
- [[signal-overfit-date-dispersion-check]] — 신호 vs 과최적화 판별 — 날짜 분산 + 시장 상대 대조
- [[stock-screening-score-design]] — 종목 스크리닝 점수 설계 — 게이트 vs 가산점 / 섹터 상대화 / 백테스트 생존 편향

### decisions

- [[shared-broker-appkey-token-cache]] — 같은 broker appkey 를 두 프로세스가 쓸 때 — 토큰 캐시 공유 + rate limit 분담

### bugs

- [[equity-curve-max-vs-latest-aggregation]] — 평가곡선 집계 함정 — MAX(equity) 는 최신 잔고가 아니라 역대 고점
- [[kis-cash-d2-settlement-buy-rejection]] — KIS 매수가능 현금에 D+2 미정산분 누락 → RiskManager 매수 차단
- [[kis-holiday-detection-bsop-date]] — KIS 공휴일 휴장 판정 실패 — bsop_date 비교 대신 휴장일조회 API
- [[naver-finance-no-info-selector-drift]] — 네이버 금융 table.no_info 셀렉터 드리프트 — 거래량·시가·등락률 silent 0 (가짜 fixture가 가린 버그)
- [[reentry-after-full-liquidation-no-cooldown]] — ht_trading 전량 청산 직후 재매수 — flat 재진입 쿨다운 부재
- [[utc-iso-date-kst-rollover]] — new Date().toISOString() 가 KST 새벽~오전에 어제 날짜로 떨어지는 버그
- [[yahoo-finance-concurrent-silent-fail]] — Yahoo Finance 동시 호출 시 일부 응답에 regularMarketPrice 누락 (silent fail)

### projects

- [[finance-analysis-nextjs]] — finance-analysis-nextjs — 한국 기업 재무분석 대시보드
- [[ht-dde]] — ht_dde — DDE 스타일 실시간 매수후보 스캐너 + 종이거래 비교 대시보드
- [[ht-trading]] — ht_trading — 프로젝트 설계 상세
- [[japa-asset-dashboard]] — japa — 개인 자산 통합 대시보드
- [[n-stock-info]] — n_stock_info — 네이버 금융 기반 종목 스크리닝·스코어링·텔레그램 리포트
- [[upbit-trading]] — upbit_trading — 암호화폐 무한매수법 자동매매

## 변경 이력

- 2026-07-02: v2 초기 생성, v1에서 73건 이관 (ai-agent 44 · trading 29)
