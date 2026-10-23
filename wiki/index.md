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

- [[agentic-cli-text-generation-lockdown]] — 에이전트형 CLI(claude -p/codex/cursor)를 순수 텍스트 생성기로 쓸 때의 잠금 패턴 — 도구 차단·cwd 격리·모델 별칭 고정·교정 재시도
- [[claude-code-model-tier-orchestration-gate]] — Claude Code 모델 계층 오케스트레이션 + PreToolUse 게이트 (off/hard/soft 모드 토글 · 하드블록 vs 소프트넛지(additionalContext 단독 — allow 동반 시 권한 우회) · Bash 감지는 정규식 아닌 shlex 토큰화 · hard 는 CC v2.1.196+ 필수 · Opus메인 vs Sonnet메인 · 심링크 토글 · 실측: 위임 강제는 hard 게이트뿐, Haiku는 지침 기반 · claude_env 멱등 install 패키징)
- [[claude-code-token-optimization]] — Claude Code 토큰 최적화 — 소비 절반 줄이기
- [[hermes-single-model-delegation]] — Hermes 단일 모델 delegate_task — context 부패 지연 + 약한 모델(소형 Qwen) 신뢰도
- [[homebrew-python-upgrade-breaks-cron-venv]] — Homebrew python 메이저 업그레이드가 cron/launchd venv 파이프라인을 조용히 깨뜨리는 패턴 — PATH prepend 로 venv 무력화 + venv 부재 전역 의존
- [[launchd-plist-symlink-from-project]] — launchd plist 프로젝트 폴더 마스터 + ~/Library/LaunchAgents symlink 패턴 (Homebrew 스타일)
- [[launchd-secret-management]] — macOS launchd 환경 시크릿 분리 패턴 — ~/.zshrc 는 안 읽힌다
- [[llm-json-parse-retry-with-dump]] — LLM JSON 파싱 실패 시 raw 응답 덤프 + 재시도 패턴
- [[macos-tcc-full-disk-access]] — macOS TCC: 터미널이 다른 앱 sandbox 에 접근할 때 토스트 팝업 처리
- [[oracle-cloud-free-tier-setup]] — Oracle Cloud Free Tier 무료 VM 가입·셋업 가이드 — ARM A1 재고 품귀 실전 기록과 PAYG 전환 해법
- [[oracle-cloud-operations-cheatsheet]] — Notion에서 보충한 OCI 운영 명령어·한국 가입 입력 팁·CLI API 키 설정·Windows SSH 접속 (과거 실행 기록)
- [[parallel-review-adversarial-fix-workflow]] — 병렬 리뷰 → 교차검증 → TDD 수정 + 적대적 검증 워크플로
- [[prompt-schema-pipeline-coupling]] — LLM 프롬프트 출력 스키마와 다운스트림 validator 간 결합 관리
- [[shell-set-eu-topic-isolation]] — shell 의 set -eu 와 multi-topic 파이프라인의 격리 패턴
- [[single-dispatcher-per-queue]] — 공유 작업 큐의 dispatcher 는 하나만 — claim churn 함정

### analyses

- [[ai-publishing-migration-safety]] — AI 게시 이관·발행 이력·단일 게시자·정기 실행 검증·오류 유형별 제한 재처리·서비스 계정/소스 소유권 통일·정기 복구/검색 예산 실패 분리·예산 내 종료 보완과 제한 검증

- [[ai-coding-agent-cost-and-context-patterns]] — AI 코딩 에이전트의 비용·컨텍스트 설계 패턴 — 강/약 모델 분리와 context rot 완화
- [[anthropic-oauth-third-party-billing-trap]] — Anthropic OAuth 의 third-party 빌링 함정 — Claude Max 구독을 외부 CLI 가 재사용한다는 오해
- [[build-vs-fork-personal-tool]] — 0부터 빌드 vs 오픈소스 포크 — 차별점이 사라질 때의 의사결정
- [[claude-code-source-leak-internals]] — Claude Code 소스 누출이 드러낸 에이전트 내부 설계 — anti-distillation · undercover · 회귀 측정
- [[everything-claude-code]] — everything-claude-code (ECC) — Claude Code 하네스 성능 시스템
- [[hermes-paperclip-adapter]] — hermes-paperclip-adapter — Paperclip 에 Hermes Agent 를 붙이는 런타임 어댑터
- [[karpathy-claude-md-skills]] — multica-ai/andrej-karpathy-skills — CLAUDE.md 4원칙 가이드
- [[llm-content-quality-guards]] — LLM 콘텐츠 품질 가드·제목 근거/필터/시각 검증 — AI는 기사 ID 선택, 코드는 원제목에서 근거 추출
- [[llm-newsletter-rewrite-metadata-grounding]] — LLM 뉴스레터 rewrite 의 메타데이터 그라운딩 베스트 프랙티스
- [[llm-provider-aggregator-vs-local-vs-hub]] — OpenRouter vs Ollama vs Hugging Face — LLM 백엔드 3가지 분류
- [[macos-launchagent-catchup-behavior]] — macOS LaunchAgent — 미실행 작업 캐치업 동작
- [[mcp-config-secret-exposure-via-ps]] — MCP 토큰이 `--mcp-config` 인자로 평문 노출되는 패턴 (`ps -ef` 가시성)
- [[multi-agent-orchestration-taxonomy]] — 멀티에이전트 오케스트레이션 3분류 — 코딩 파이프라인에서의 실무 결론
- [[multi-agent-shared-wiki-concurrency]] — 다중 에이전트 공유 지식베이스 — 동시 쓰기 위험과 완화
- [[multi-llm-provider-adapter-pattern]] — 멀티 LLM provider 어댑터 패턴 — 공식 SDK 직접 사용
- [[multi-profile-cli-agent-isolation]] — 멀티 프로필 CLI agent 격리 — OAuth 공유 / HOME 격리 우회 / shell init 함정
- [[oauth-refresh-token-rotation-multi-client]] — 회전형 OAuth refresh token 을 여러 클라이언트가 공유할 때의 쟁탈 함정
- [[openai-codex-cli-overview]] — OpenAI Codex CLI — 터미널 경량 코딩 에이전트와 로컬 모델 연결
- [[personal-llm-wiki-curation]] — 개인 LLM wiki 큐레이션 — write-only 창고를 피하는 수집·승격 설계
- [[openclaw-acp-runtime-internals]] — OpenClaw ACP Runtime 내부 구조 — plugins.allow / 좀비 task / sessions.json / wrapper 환경 상속
- [[openclaw-telegram-group-setup]] — OpenClaw Telegram 그룹 봇 설정 — Privacy Mode, requireMention, 다중 에이전트
- [[research-write-agent-separation]] — Research / Write 에이전트 분리 — LLM 콘텐츠 파이프라인의 도구 기반 조사 격상
- [[self-hosted-agent-webui-integration]] — 셀프호스팅 에이전트에 커스텀 UI 붙이기 — 내부 직결 vs OpenAI 호환 HTTP API
- [[weak-model-agent-reliability-compounding]] — 약한 모델의 에이전트 전략 — 신뢰도 복리 붕괴와 결정적 워크플로우

### decisions

- [[openclaw-coder-default-model-codex]] — OpenClaw 코더 default 모델: Anthropic 대신 Codex (GPT-5.5)

### bugs

- [[dossier-evidence-kind-enum-reject]] — evidence.kind enum 거부가 topic 전체 drop 으로 증폭 — opensource-curation research 이틀 연속 조용한 결손
- [[gieok-session-log-url-credential-masking-false-positive]] — gieok session-log 의 credential 마스킹이 lore.kernel.org 스타일 URL 을 파괴하는 오탐 — ingest 트리아지 오판 위험
- [[highlights-action-validator-schema-drift]] — publish validator 가 옛 스키마(action) 만 강제해 신 스키마(if/do/verify) 게시 전부 실패
- [[ndjson-stdout-parser-greedy-regex]] — AI CLI 어댑터의 stdout 파서가 NDJSON·envelope·잡음 텍스트에 깨지는 함정
- [[newsletter-research-anti-bot-blocking]] — 뉴스레터 research 파이프라인의 anti-bot 차단 — 1급 실패 모드로 설계되지 않은 소스 접근 장애
- [[openclaw-coder-silent-3-layer]] — OpenClaw 코더 응답 무 — 3계층 디버깅 (plugins.allow / 좀비 task / OAuth 403)
- [[openclaw-provider-id-legacy-rename]] — OpenClaw provider ID 리네임 브레이킹 체인지 — openai-codex → openai (전 모델 Unknown model 실패)
- [[stale-process-attributeerror-inprocess-coupling]] — 속성은 디스크에 있는데 AttributeError — in-process 강결합 데몬의 stale 프로세스 (재시작으로 해결)

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

### summaries

- [[claude-code-opus-orchestration-setup]] — raw-source 요약 — Claude Code용 Opus 오케스트레이션 설정 작업 명세서 (deep-reasoner/implementer 명명 불일치 각주)

## trading

### patterns

- [[backtest-clock-injection]] — 백테스트 벽시계 오염 — 전략 시각 소스 주입(clock injection) 패턴
- [[csv-roundtrip-backup-restore]] — CSV round-trip 백업·복원 — 외래키 ID 와 N:M 보존
- [[mirror-config-drift-guard-test]] — 미러 설정 드리프트 가드 테스트 — 원본을 읽어 자동 대조
- [[post-bugfix-reverification-data-cutoff]] — 데이터 품질 버그 수정 후 재검증 — 평가 도구도 수정일 이후로 필터해야
- [[startup-dependency-crash-loop]] — 기동 시 외부 의존성 강체크 + 자동 재시작 = 크래시 루프
- [[trading-performance-cash-flow-reconciliation]] — 입출금·체결 대조·공식/추정 손익·최근 N건/당일 전체/진입 코호트 구분

### analyses

- [[trading-observation-vs-order-time-gates]] — 가격 관측·고점 갱신·주문 시간 분리, 순간 고가와 실제 폴링·조건부 재생의 경계

- [[backtest-fill-model-adverse-selection]] — 백테스트 체결 모델과 역선택·종가/익일 전방 비교·다음 호가 체결의 미체결 편향·호가 비교와 전략 성과 분리·마감 전 후보/최종 추천의 정보 시점
- [[backtest-timeframe-sensitivity]] — 백테스트 봉 간격 민감도 — 지표 신호 효과는 timeframe 따라 뒤집힌다 + 공정 비교 방법론
- [[dca-intraday-buy-timing]] — 정액 DCA 의 일중 매수 타이밍 — 레버리지 ETF 분봉 실측 (시가 직후·14:30 회피)
- [[dca-trailing-stop-tuning]] — DCA·트레일링 스톱 튜닝 — 운영 로그 기반 진단·개선 패턴
- [[holding-period-signal-mismatch]] — 보유기간-신호 미스매치 — 같은 진입신호가 홀딩 호라이즌에 따라 손실↔초과수익으로 뒤집힌다
- [[holding-transaction-cost-basis-design]] — 보유 종목 매수/매도 거래 추적 설계 — 평균단가·실현손익 동결·삭제 역연산
- [[financial-data-freshness-and-cache]] — 재무 기준기간·확정값 대조, KIS 우선·제한적 장애 대체와 일일 원자료 캐시
- [[ht-trading-live-data-improvement-analysis]] — ht_trading 운영 로그·분봉 기반 누적 개선 분석 — BUY 안전가드·관찰 대장, 조건부 장중 재생·8거래일 독립 추천 비교·고점 관측 민감도
- [[kis-balance-api-fields]] — KIS 잔고 API 응답의 현금 필드 의미 (예수금 vs 매수가능)
- [[paper-to-live-strategy-isolation]] — 페이퍼→실거래 규칙·자금/보유 격리·추천/집행 대사·응답 시각 호가 검증·동일 주문 부분체결 감시 유지·복제 안전검증/성과 장부 분리·계좌 보류의 후속 미전송 진단
- [[multi-market-trading-account-isolation]] — 국내/미국 실행 격리·미국 V1 변형/2021 공개규칙의 동일시작 대조·40거래일 정책 경계·매수일과 손익 분모
- [[kis-minute-chart-trs]] — KIS 분봉 조회 TR 비교 — 당일 전용(FHKST03010200) vs 과거일(FHKST03010230)
- [[llm-news-prediction-pitfalls]] — LLM 의 뉴스 기반 시장 예측 — 6가지 함정과 한계
- [[news-driven-market-signal-framework]] — 뉴스 기반 시장 시그널 추출 프레임워크 (7-축 + 3-시나리오)
- [[optimal-strategy-search-preconditions]] — 전략 탐색의 4가지 선결 조건 — "최적을 찾았다"를 판정하기 전에
- [[partial-sell-rule-idempotency]] — 알고리즘 트레이딩의 부분 매도 규칙 멱등성 패턴
- [[polling-interval-vs-bar-interval]] — 폴링 주기와 봉 단위·샘플링 전 이벤트 판단·고점/신호 보존·단일 실매도 경로 연결·1분 장애 보완·장중 확인 대기
- [[risk-control-exemption-and-failed-attempt-accounting]] — 안전장치가 손실을 키울 때 — 리스크 감축 주문 면제와 실패 시도 회계
- [[scoring-system-ic-validation]] — 스코어 시스템의 IC (Information Coefficient) 검증 방법론
- [[scoring-version-comparison-methodology]] — 점수/후보/추천 필터 효과 분리·최종 추천 네 조합·200억원 필터 제거 재생·소표본 판단·영향 검증 후 지문 갱신·확인된 개별 결측 제외/통신 실패 보류
- [[signal-overfit-date-dispersion-check]] — 신호 vs 과최적화 판별 — 날짜 분산 + 시장 상대 대조
- [[trading-api-failure-handling]] — API 실패·계좌 신선도·접수 미확인의 증거 기반 복구·공통 호출 제어와 미확정 경계
- [[stock-screening-score-design]] — 종목 스크리닝 점수 설계 — 게이트 vs 가산점 / 섹터 상대화 / 백테스트 생존 편향
- [[surge-chasing-exclusion-filter]] — 급등 종목 추격 배제 필터 — 하락장 레짐의 유일한 초과수익 규칙 (confidence medium — 단일 레짐 표본)

### decisions

- [[shared-broker-appkey-token-cache]] — 공유 broker appkey의 토큰 캐시·공통 호출 제한 장부·실주문 우선 처리 — 10/22 후속: 세션 보고 18회 한도·내부 합산 8회/배경 4회 예산 구분

### bugs

- [[kis-fully-rejected-order-status]] — 전량 거부의 OPEN 오분류 수정·체결 대기/대사/보고 일관성

- [[absolute-stop-loss-elif-dead-code]] — 벤치마크 존재 시 절대손절 elif dead code — 손절 자체가 꺼진다
- [[equity-curve-max-vs-latest-aggregation]] — 평가곡선 집계 함정 — MAX(equity) 는 최신 잔고가 아니라 역대 고점
- [[flask-jsonify-infinity-breaks-browser-json]] — Flask jsonify 의 Infinity/NaN 이 브라우저 JSON.parse 를 깨는 침묵 함정
- [[kis-cash-d2-settlement-buy-rejection]] — KIS 매수가능 현금에 D+2 미정산분 누락 → RiskManager 매수 차단
- [[kis-derivative-etf-order-reject-apbk1497]] — KIS 파생·레버리지 ETF 주문 거부 [APBK1497·APBK1681] — 계좌 권한(선택확인서)·기본예탁금의 다층 게이트
- [[kis-holiday-detection-bsop-date]] — KIS 공휴일 휴장 판정 실패 — bsop_date 비교 대신 휴장일조회 API
- [[naver-finance-news-referer-required]] — 네이버 금융 종목 뉴스 크롤링 — Referer 헤더 없으면 빈 스텁 페이지
- [[naver-finance-no-info-selector-drift]] — 네이버 금융 table.no_info 셀렉터 드리프트 — 거래량·시가·등락률 silent 0 (가짜 fixture가 가린 버그)
- [[order-post-retry-double-fill]] — 비멱등 주문 POST 자동 재시도 — 이중 체결 위험과 취소 오판
- [[pykrx-index-name-lookup-failure]] — 지수 이름 부수 조회의 JSON 실패·빈 목록·2차 출력 오류, name_display=False 수정·반영 완료, 정기 배치·실거래 편입은 별도 검증
- [[pykrx-krx-login-required]] — pykrx — KRX 로그인 의무화·CD010 비밀번호 변경 요구가 파싱 오류로 발현
- [[reentry-after-full-liquidation-no-cooldown]] — ht_trading 전량 청산 직후 재매수 — flat 재진입 쿨다운 부재
- [[relative-stop-benchmark-stale-price]] — 상대손절의 벤치마크 stale price — 종목은 실시간가, 지수는 일봉 캐시 종가
- [[sqlite-cross-thread-connection-threading-local]] — SQLite 커넥션 스레드 간 공유 → ProgrammingError 로 데몬 스레드 청산 침묵 마비 (threading.local 지연 생성으로 해결)
- [[utc-iso-date-kst-rollover]] — new Date().toISOString() 가 KST 새벽~오전에 어제 날짜로 떨어지는 버그
- [[yahoo-finance-concurrent-silent-fail]] — Yahoo Finance 동시 호출 시 일부 응답에 regularMarketPrice 누락 (silent fail)

### projects

- [[finance-analysis-nextjs]] — finance-analysis-nextjs — 한국 기업 재무분석 대시보드
- [[ht-dde]] — ht_dde — 스캐너·페이퍼 비교·실시간 신호/체결 대사·추천 네 계정 지문 복구·분할매수 감사 오탐 수정
- [[ht-trading]] — ht_trading — 전략 자금/보유 격리·09:35 매수·실시간 실매도 연결 반영/장중 확인 대기·추천 필터 유지 분석
- [[japa-asset-dashboard]] — japa — 개인 자산 통합 대시보드
- [[n-stock-info]] — n_stock_info — KIS 중심 수집·기존 점수식 운영 복구·독립 네이버 비교·불변 최종 추천·재무 보완/장애 대체·일일 캐시
- [[upbit-trading]] — upbit_trading — 암호화폐 무한매수법 자동매매

## Codex 미수집 기록 보강 (2026-10-10)

- [[codex-session-capture-and-curation]] — Claude 훅 유지, Codex 전역 수집·정리, 재개 세션 원본 합치기·실패 배치 재시도·완료 플래그와 본문 변경 구분
- [[trailing-activation-current-profit-gate]] — 활성화 이후 현재 수익률로 트레일링 검사를 끄는 공통 오류
- [[trading-performance-cash-flow-reconciliation]] — 체결·입출금·성과 대조 및 오염된 페이퍼 검증 경계

## 변경 이력

- 2026-10-23: 지정 두 세션 후속을 기존 8문서에 통합 — 제목 근거 추출·오류 제한 재처리·소유권 통일, 실시간 단일 매도 경로·호가/부분체결 수정, 체결 모형 정정·추천 필터 재생·비교 지문 복구. 신규 문서 없음. 출처·스킵 사유·전체 변경 파일은 [[log]] 참조.

- 2026-10-22: 지정 1세션 추가 대화를 기존 6문서에 통합 — [[polling-interval-vs-bar-interval]]의 저장 샘플링 전 이벤트 판단·실주문 없는 검증 감시, [[paper-to-live-strategy-isolation]]의 추천/매수 대사, [[shared-broker-appkey-token-cache]]·[[trading-api-failure-handling]]의 한도/내부 예산·밴 증거 경계와 [[ht-trading]]·[[ht-dde]] 후속. 신규 문서 없음 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-21: 지정 두 세션 후속을 기존 3문서에 통합 — [[ai-publishing-migration-safety]]의 배포/AI 부분 실패·원인 로그 누락, [[trading-api-failure-handling]]과 [[order-post-retry-double-fill]]의 접수 미확인·신규매수 차단·미복구 경계. 신규 문서 없음.

- 2026-10-21: 지정 1세션 후속을 기존 5문서에 통합 — 장후/익일 진입 비교 등록·안전한 보류 로그·공식/추정 손익 분리·첫 역행 실거래와 두 번째 추천 비교 대사. 신규 문서 없음 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-20: 지정 1세션 후속 대조 — [[ai-publishing-migration-safety]]·[[llm-content-quality-guards]]에 첫 정기 실행 성공 보고와 누적/신규 및 검증 범위 경계 보완. 신규 문서 없음 (출처: session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md)

- 2026-10-20: 기존 5문서 보강 — [[trading-observation-vs-order-time-gates]]의 시초 세 방식 비교·현행 유지, [[paper-to-live-strategy-isolation]]의 고정 금액 가드 수정·엔진 복구, [[llm-content-quality-guards]]의 요약 근거/필터/시각 검증, [[ht-trading]]·[[ht-dde]]의 운영 및 첫 가상매수 대사 후속. 지정 3세션 검토·신규 문서 없음.

- 2026-10-19: [[trading-observation-vs-order-time-gates]] 신규 등록·ht-trading/기존 트레일링 버그 연결, 게시/품질 분석의 10/19 배포·누적 구현 후속 반영. 지정 세션 2건 검토 (출처: session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md; session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-19: 기존 분석 2건 보강 — [[ai-publishing-migration-safety]]의 실행 환경·재실행 대기·이전 결과 표시와 [[llm-content-quality-guards]]의 분류 보류·표본·실제 호출/배포 경계. 신규 페이지 없음 (출처: session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md)

- 2026-10-18: [[financial-data-freshness-and-cache]] 신규 등록, [[n-stock-info]] 재무 보완·장애 대체·일일 캐시와 [[ht-trading-live-data-improvement-analysis]] 8거래일 재생 보강. 첫 정상 수집 연결은 확인 전 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-18: 기존 지식 6건 갱신 — 최종 추천 네 조합 구현·불변 스냅샷·전체 롤백, 미국 공개규칙/기존 방식의 동일시작 대조와 지표 의미 반영. 첫 신규 입력·체결·마감은 확인 전 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-17: 기존 5페이지 보강 — 역행 실운영 활성화와 첫 체결 미확인, 페이퍼 규칙 보존·배치 재시도, 최종 추천 네 조합 목표와 현재 두 후보 장부의 차이 반영. 신규 페이지 없음 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-17: 페이퍼→실거래 전략 격리 분석 신규 등록, ht-trading·ht-dde·누적 개선 분석에 최종 재생 검증·버전 보존·비활성 집행 보강 반영 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-16: 지정 세션 후속으로 기존 지식 6건 갱신 — [[scoring-version-comparison-methodology]]의 완료 시각·현금 재배분 검증, [[backtest-clock-injection]] 시간 가드 수정, [[backtest-fill-model-adverse-selection]] 관측/체결 경계, [[ht-trading-live-data-improvement-analysis]] 전체 재생 결과, [[ht-dde]] 후보 비교 등록·[[ht-trading]] 비활성 집행 장부. 신규 페이지 없음 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-16: 주문 상태 버그·API 실패 판단 분석 신규 등록, 기존 체결 모델·개선 분석·ht-trading에 애프터 기반과 미가동 경계 반영 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-15: 기존 분석 2건·ht-trading에 최근 장중 재생 결과, 단일 종목 제외 민감도와 후보 발굴/채점/매매 조건의 비교 범위를 보강. 신규 페이지 없음 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-15: 기존 미국 시장 분리 분석·ht-dde에 장부 기반 마감 평가 보고와 그래프의 비교/검증 경계 보강. 신규 페이지 없음 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-15: 기존 분석 2건·프로젝트 2건에 RS 정상 20일 충족, 종가/익일 진입 비교 설계와 비활성 주문 준비 범위, 미국 첫 마감 반영의 검증 경계 보강. 신규 페이지 없음 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-14: 지수 이름 조회 버그·ht-dde·인증 버그의 후속 수정 완료 반영, 신규 페이지 없이 기존 기록 갱신 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-14: 지정 세션 후속 결과로 지수 이름 조회 버그 신규 등록, 기존 분석 3건·프로젝트 2건·인증 버그 1건 보강. V1 재현 가동과 RS 편입 보류·미적용 수정안을 구분 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-13: 지정 세션 후속 결론으로 기존 분석 2건·프로젝트 1건 보강: 수집 소스 부분 실패와 정기 실행 증거, 무한매수 버전·LOC 검증 및 미가동 상태 (출처: session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md; session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-13: 지정 세션 2건에서 게시 이관 분석 신규 등록 및 미국 실제 관측·동일 계좌 위험 관리로 기존 분석/프로젝트 4개 보강 (출처: session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md; session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-12: 지정 세션 후속 미국 기반·오프라인 페이퍼 구현을 기존 분석·프로젝트 4개에 보강. 합성 검증과 실제 관측·실거래의 경계를 표시 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-12: 지정 Codex 세션 재검토로 미국 계좌 분리 분석 등록, n_stock_info·KRX 인증 설명 갱신 (출처: session-logs/codex-01a0e7f0-a72a-73f3-9c57-632c987a3a4e.md)

- 2026-10-11: 지정 Codex 세션 최종 내용은 기존 [[codex-session-capture-and-curation]]·[[wiki/projects/gieok|gieok 프로젝트]]에 기수록으로 확인. 패턴 목차에 실패 배치 재시도를 명시, 신규 analyses 없음.

- 2026-10-11: [[wiki/projects/gieok|gieok 프로젝트]]의 최종 운영 설정·Sol medium 실실행·기존 기록 처리 상태와 [[codex-session-capture-and-curation]]의 리미트 중단 복구를 보강. 신규 페이지 없이 기존 문서에 통합.

- 2026-10-11: 지정 세션 1건 재검토. [[codex-session-capture-and-curation]]의 감사 체크섬 기준 보강, 신규 페이지 없음.

- 2026-10-10: 지정된 2026-08-04~08-08 Research Dossier 세션 104건 검토·스킵 처리. 조사 요청과 후보만 남아 새 지식 페이지 없음. 기존 조사 계약·로깅 한계 문서와 대조했으며 파일별 근거와 변경 파일 전체 목록은 [[log]]의 해당 일자 기록 참조.

- 2026-08-17: [[ht-trading-live-data-improvement-analysis]] 갱신 — 국내 BUY 안전가드 구현·배포 결과와 스크리너 점수(30건), 시장 국면 필터(나쁜 장의 실제 BUY), InfiniteBuying(실제 라운드 종료) 추적 조건을 인덱스에 반영.
- 2026-08-01: 신규 0건. 갱신 1건 — [[newsletter-research-anti-bot-blocking]] (08-01 10번째 관측 — patchwork.kernel.org API 가 Mozilla UA 호출에 Anubis 챌린지 v1.18.0(lore 의 1.25.0 과 별도 배포)을 처음 반환해 폴백 채널 자체로 차단 확산 + Mozilla 포함 복합 UA `… curl-lore-fetch` 도 챌린지 대상 + git/2.43.0 UA·cdn ChangeLog·releases.json 통과 재확인, 차단 9일 연속). session-logs 유래 21건 처리(20260801 dev-blog cron 03:00~05:04 사이클), 뉴스레터 기사 콘텐츠는 전량 뉴스성 스킵(MIME 미디코딩·마스킹 span 파괴는 기수록 재발, `Assisted-by: Claude` 커밋 표기는 도메인 밖 뉴스).
- 2026-07-31: 신규 0건. 갱신 1건 — [[newsletter-research-anti-bot-blocking]] (07-31 9번째 관측 — `mutt/2.2` 메일 클라이언트 UA 통과 신규 실측 + `git/2.53.0` 세 버전째 통과 + 브라우저 Mozilla UA 응답이 07-29 `500` 변형에서 `200`+Anubis 챌린지 형태로 복귀 + git.kernel.org cgit 는 기본 curl 로 200 이라 필터는 lore 국한). session-logs 유래 23건 처리(20260731 dev-blog cron 03:00~05:00 사이클), 뉴스레터 기사 콘텐츠는 전량 뉴스성 스킵.
- 2026-07-29: 신규 0건. 갱신 3건 — [[newsletter-research-anti-bot-blocking]] (07-29 7번째 관측 — 브라우저 Mozilla UA 의 신규 `500 Server Error` 변형 + NNTP GREET/ARTICLE 프로토콜 상세 최초 확정 + yhbt.net·patchwork.kernel.org 병행 지속, 차단 6일 연속), [[llm-json-parse-retry-with-dump]] (2026-07-22 도입한 교정 재시도 프롬프트가 dev-blog cron 실운영에서 발사된 것을 세션 로그로 첫 실측 — Opensource Trending write 1차 실패 → `[재시도]` 지시문 부착 재발사, 최종 성공 여부는 로그 미기록), [[dev-blog]] (07-29 운영 노트 — 교정 재시도 실사용 실측 + Kernel Lens 6렌즈 중 4개 로그에 최종 dossier 미기록[대응 write 는 3건 정상 발사] + anti-bot 7번째 관측 + credential 마스킹 파괴 지속). session-logs 유래 21건 처리(20260729 dev-blog cron 03:00~05:00 사이클), 뉴스레터 기사 콘텐츠는 전량 뉴스성 스킵.
- 2026-07-26: 신규 1건 등록 — ai-agent: [[dossier-evidence-kind-enum-reject]] (bugs — dev-blog `opensource-curation` research 가 07-24·07-25 아침 cron 에서 반복 실패한 원인. LLM 이 evidence.kind 에 enum 밖 값 `"repo"` 를 창발 → `validateEvidence()` throw → topic 전체 drop, 이틀 연속 발생 후 발견. commit 823213a 로 enum 확장 수정). 갱신 4건: [[llm-content-quality-guards]] (「가드 5 의 오탐 클래스」 절 — CJK 가드가 중국어 소스 원문 인용을 차단한 false positive, 혼입과 인용의 구분 불가 + 결정론적 차단 + 해소는 소스 배제), [[newsletter-research-anti-bot-blocking]] (07-26 4번째 관측 — lore.kernel.org 차단 지속 + patchwork API·infradead·yhbt.net·NNTP·git 우회 채널 실측, Research 5·Write 4 전건 산출 성공), [[prompt-schema-pipeline-coupling]] (재발 변형 — 사람이 아니라 LLM 이 스스로 enum 밖 값을 창발한 케이스), [[dev-blog]] (07-25 evidence.kind enum 거부 + CJK 인용 오탐 2단 연쇄 장애 + 07-26 정상 복귀 운영 노트). session-logs 유래 27건 처리(07-25 저녁 + 07-26 새벽 cron), 뉴스레터 기사 콘텐츠는 전량 뉴스성 스킵.
- 2026-07-24: 신규 1건 등록 — ai-agent: [[newsletter-research-anti-bot-blocking]] (bugs — dev-blog cron research dossier 의 anti-bot 차단 3종(Anubis·403+봇검사·HTTP 402) + Kernel Lens `lore-stable-new` 렌즈 전면 차단으로 dossier 미산출·newsletter 결손 시 파이프라인 재시도·감지 없음. 2026-07-03 Anubis 관측의 두 번째 등장으로 승격, 출처 040043 외). 갱신 2건: [[research-write-agent-separation]] (Research 단계의 병렬 서브에이전트 클러스터링 신규 절 — AI Coding Agents dossier 033656 에서 후보 12건을 4개 주제 클러스터로 나눠 병렬 Task 위임, "분리의 가치는 도구에 있다"는 원리의 research 단계 내부 재귀 적용 + anti-bot 차단 단락·bugs 문서 링크), [[dev-blog]] (7/24 운영 노트 — Kernel Lens dossier 6회/newsletter 5회 비대칭, anti-bot 차단 이슈 링크). session-logs 미처리 21건 중 20260724 dev-blog cron 뉴스레터/dossier 21건 중 신규 2건 반영분 제외 전량 뉴스성 스킵(7/22 방침과 동일, AI Coding Agents dossier 내 뉴스 항목도 스킵).
- 2026-07-22: 신규 1건 등록 — trading: [[sqlite-cross-thread-connection-threading-local]] (bugs, bb66 — ht_dde rt 데몬 스레드가 메인 스레드 생성 SQLite 커넥션을 재사용해 ProgrammingError 388회, 장중 청산 7/17~7/21 침묵 마비 → A/B 오염 리셋. threading.local 지연 생성 @property 로 수정). 갱신 4건: [[ht-dde]] (07-21 상태 점검 — 감시 주기 A/B(tier/rt) 설계·오염 리셋·launchd 정식 등록, bb66), [[launchd-plist-symlink-from-project]] (함정 5 「config/ 직접 bootstrap 은 재부팅 소멸」+ 함정 6 「StandardOutPath 0바이트 ≠ 미실행」, bb66), [[polling-interval-vs-bar-interval]] (매도 감시 2분→10~30초 검토 — 잔고 캐시 TTL 하한·매도 전용 경량 루프 전제·1분봉 백테스트 재검증 전 미적용, 8f9d), [[n-stock-info]] (§8 유동성 게이트가 만드는 "추천 0종목" — 80.25점 종목도 시총·거래대금 컷 탈락, 8f9d). session-logs 미처리 23건 중 20260722 dev-blog cron 뉴스레터/dossier 20건은 뉴스성 스킵, ea52 는 07-04 기수집 확인(플래그 이미 true).
- 2026-07-17: 갱신 1건 — ai-agent: [[research-write-agent-separation]] (봇 차단 함정에 "PoW 솔버가 저-difficulty 일엔 완전 우회 성공" 절 추가 — 07-15 의 "솔버=비신뢰 폴백" 을 부분 정정: `linux-arch-platform` 렌즈에서 동일 SHA256 nonce 솔버가 `diff=4` 를 1~69ms 에 풀어 lore raw mbox 3건 완전 취득, 솔버 성공은 그날 difficulty 에 좌우되는 비결정적 폴백, 출처 1119). session-logs 미처리 18건(20260717 dev-blog cron 뉴스레터/dossier) 중 나머지 17건은 뉴스성 + write 단계 `assistant_turns:0` no-op(기수록)으로 스킵. raw-sources/·.cache/extracted/·fetched/·mcp-note 대상 없음.
- 2026-07-13: 갱신 1건 — ai-agent: [[claude-code-model-tier-orchestration-gate]] (실측 "네이티브 위임은 알아서 안 일어남" 절 + PreToolUse payload 스키마 확정(`prompt_id` v2.1.196+ · agent_id/agent_type 서브에이전트 한정) + claude_env 멱등 install 패키징 절, 출처 ccc7). session-logs 미처리 23건 중 20260713 dev-blog cron 뉴스레터/dossier 22건은 뉴스성으로 스킵(표본 3건 확인 — WebFetch silent fail·프롬프트 스키마 vs 출력 편차는 기존 문서 계열로 신규성 없음).
- 2026-07-12: 신규 2건 등록 — trading: [[surge-chasing-exclusion-filter]] (analyses — ht_dde 26거래일 전수 감사에서 급등 추격 배제 필터가 유일하게 시장 대비 초과수익, confidence medium 단일 하락장 레짐 표본, 출처 9413) / ai-agent: [[claude-code-opus-orchestration-setup]] (신설 `summaries` 카테고리 — raw-sources/claude-code-opus-orchestration-setup.md 요약, [[claude-code-model-tier-orchestration-gate]]와 상호링크). 갱신 4건: [[personal-llm-wiki-curation]]("v1→v2 재편 사례" — 선별이관 37.6%·vault 개명 교훈, 출처 1627), [[gieok]]("Vault 재개명" 절, 출처 1627), [[signal-overfit-date-dispersion-check]]("vol_surge 승률 착시" 사례, 출처 9413), [[ht-dde]]("2026-07-12 전략 전수 감사" — 방어규칙 3종 확정·vol_surge300_eod/combo_guard 신규, 출처 9413), [[claude-code-model-tier-orchestration-gate]](원본 명세서·요약 상호링크 + 명명 불일치 각주). 이관 121건 미대상은 v1 개별 복사 원칙 유지.
- 2026-07-12: 신규 1건 등록 — ai-agent: [[oracle-cloud-free-tier-setup]] (patterns — v1(llm_wiki)에서 이관, domain personal → ai-agent 재분류(인프라·자동화 호스팅 용도). v1 06-06 회차에서 "일회성"으로 스킵됐던 항목을 재검토해 등록. related 의 `ssh-cli-toolkit-essentials.md` 는 v2 미존재로 제거).
- 2026-07-11: 신규 1건 등록 — ai-agent: [[hermes-single-model-delegation]] (patterns — 단일 소형 모델 Hermes에서 delegate_task 동일모델 위임으로 context 부패 지연 + 약한 모델 신뢰도, `~/.hermes/config.yaml` delegation 블록 실측 기반, 회사 PC Qwen 대상).
- 2026-07-11: 신규 1건 등록 — ai-agent: [[claude-code-model-tier-orchestration-gate]] (patterns — Opus 오케스트레이터+Sonnet 실행자+Haiku 러너 3계층을 서브에이전트 frontmatter·PreToolUse 게이트(메인 직접수정 턴당 2파일 제한·Bash 직접쓰기 차단·서브에이전트 예외·fail-open)·심링크 토글로 구성. 직접 구축·검증 22/22. 출처: raw-sources/claude-code-opus-orchestration-setup.md)
- 2026-07-09: 신규 1건 등록 — trading: [[flask-jsonify-infinity-breaks-browser-json]] (bugs, 58a3 — ht_dde `/rs` 빈 화면이 응답 JSON 의 Infinity(vol/0)로 브라우저 JSON.parse 만 깨진 사례, Flask jsonify allow_nan 관측 비대칭 + 2겹 방어). 갱신 2건: [[scoring-system-ic-validation]] (라이브 out-of-sample 2차 확증 — 스코어 역예측 4주 재현·거래량급증만 양·가중치 미세조정 노이즈), [[ht-dde]] (4주 동작검토·Infinity 버그·vol_surge 단일규칙 슬롯). session-logs 미처리 24건 중 20260710 dev-blog cron 뉴스레터/dossier 23건은 뉴스성으로 스킵(Anubis 소스차단 회복 패턴은 [[research-write-agent-separation]] 07-07 항목에 이미 흡수 — 신규성 없음).
- 2026-07-08: 신규 1건 등록 — trading: [[kis-derivative-etf-order-reject-apbk1497]] (bugs, 2c24 — 레버리지 ETF 24회 매수 거부가 코드가 아니라 계좌 파생ETF 미신청). 인덱스 드리프트 보정 — 07-07 cron 이 생성했으나 미등록이던 5건 추가: trading/analyses [[dca-intraday-buy-timing]]·[[kis-minute-chart-trs]] (fe2f), trading/bugs [[naver-finance-news-referer-required]]·[[pykrx-krx-login-required]] (ac9d)·[[relative-stop-benchmark-stale-price]] (fe2f). 갱신: [[ht-trading]]·[[n-stock-info]]·[[stock-screening-score-design]] (매수 시점 스코어 감사 로그·2단계 컷·멱등 vs point-in-time 2번째 실증, 2c24), [[gieok]]·[[claude-code-token-optimization]] (index 주입 반복 토큰 비용 모델·85% 축소, ea52). fe2f·ac9d 는 07-07 cron 이 이미 완전 인제스트해 플래그만 갱신. session-logs 미처리 49건 중 dev-blog cron 뉴스레터/dossier 45건은 뉴스성으로 스킵.
- 2026-07-06: 신규 1건 등록 — ai-agent: [[stale-process-attributeerror-inprocess-coupling]] (bugs). session-logs 미처리 29건 중 뉴스레터/dossier cron 26건·일회성(디스크 상태·OCI 무료티어 셋업) 3건은 도메인 밖/뉴스성으로 스킵.
- 2026-07-05: 신규 12건 등록 — trading: [[absolute-stop-loss-elif-dead-code]]·[[order-post-retry-double-fill]] (bugs), [[backtest-clock-injection]]·[[mirror-config-drift-guard-test]]·[[startup-dependency-crash-loop]] (patterns), [[backtest-fill-model-adverse-selection]]·[[optimal-strategy-search-preconditions]] (analyses) / ai-agent: [[multi-agent-orchestration-taxonomy]]·[[multi-agent-shared-wiki-concurrency]]·[[weak-model-agent-reliability-compounding]] (analyses), [[parallel-review-adversarial-fix-workflow]]·[[single-dispatcher-per-queue]] (patterns)
- 2026-07-04: 신규 1건 등록 — [[hermes-paperclip-adapter]] (ai-agent/analyses)
- 2026-07-04: 신규 2건 등록 — [[personal-llm-wiki-curation]] (ai-agent/analyses), [[holding-period-signal-mismatch]] (trading/analyses)
- 2026-07-02: v2 초기 생성, v1에서 73건 이관 (ai-agent 44 · trading 29)
