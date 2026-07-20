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

- [[claude-code-model-tier-orchestration-gate]] — Claude Code 모델 계층 오케스트레이션 + PreToolUse 게이트 (off/hard/soft 모드 토글 · 하드블록 vs 소프트넛지(additionalContext 단독 — allow 동반 시 권한 우회) · Bash 감지는 정규식 아닌 shlex 토큰화 · hard 는 CC v2.1.196+ 필수 · Opus메인 vs Sonnet메인 · 심링크 토글 · 실측: 위임 강제는 hard 게이트뿐, Haiku는 지침 기반 · claude_env 멱등 install 패키징)
- [[claude-code-token-optimization]] — Claude Code 토큰 최적화 — 소비 절반 줄이기
- [[hermes-single-model-delegation]] — Hermes 단일 모델 delegate_task — context 부패 지연 + 약한 모델(소형 Qwen) 신뢰도
- [[launchd-plist-symlink-from-project]] — launchd plist 프로젝트 폴더 마스터 + ~/Library/LaunchAgents symlink 패턴 (Homebrew 스타일)
- [[launchd-secret-management]] — macOS launchd 환경 시크릿 분리 패턴 — ~/.zshrc 는 안 읽힌다
- [[llm-json-parse-retry-with-dump]] — LLM JSON 파싱 실패 시 raw 응답 덤프 + 재시도 패턴
- [[macos-tcc-full-disk-access]] — macOS TCC: 터미널이 다른 앱 sandbox 에 접근할 때 토스트 팝업 처리
- [[oracle-cloud-free-tier-setup]] — Oracle Cloud Free Tier 무료 VM 가입·셋업 가이드 — ARM A1 재고 품귀 실전 기록과 PAYG 전환 해법
- [[parallel-review-adversarial-fix-workflow]] — 병렬 리뷰 → 교차검증 → TDD 수정 + 적대적 검증 워크플로
- [[prompt-schema-pipeline-coupling]] — LLM 프롬프트 출력 스키마와 다운스트림 validator 간 결합 관리
- [[shell-set-eu-topic-isolation]] — shell 의 set -eu 와 multi-topic 파이프라인의 격리 패턴
- [[single-dispatcher-per-queue]] — 공유 작업 큐의 dispatcher 는 하나만 — claim churn 함정

### analyses

- [[ai-coding-agent-cost-and-context-patterns]] — AI 코딩 에이전트의 비용·컨텍스트 설계 패턴 — 강/약 모델 분리와 context rot 완화
- [[anthropic-oauth-third-party-billing-trap]] — Anthropic OAuth 의 third-party 빌링 함정 — Claude Max 구독을 외부 CLI 가 재사용한다는 오해
- [[build-vs-fork-personal-tool]] — 0부터 빌드 vs 오픈소스 포크 — 차별점이 사라질 때의 의사결정
- [[claude-code-source-leak-internals]] — Claude Code 소스 누출이 드러낸 에이전트 내부 설계 — anti-distillation · undercover · 회귀 측정
- [[everything-claude-code]] — everything-claude-code (ECC) — Claude Code 하네스 성능 시스템
- [[hermes-paperclip-adapter]] — hermes-paperclip-adapter — Paperclip 에 Hermes Agent 를 붙이는 런타임 어댑터
- [[karpathy-claude-md-skills]] — multica-ai/andrej-karpathy-skills — CLAUDE.md 4원칙 가이드
- [[llm-content-quality-guards]] — LLM 기반 자동 콘텐츠 발행의 5 가지 품질 가드
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

- [[highlights-action-validator-schema-drift]] — publish validator 가 옛 스키마(action) 만 강제해 신 스키마(if/do/verify) 게시 전부 실패
- [[ndjson-stdout-parser-greedy-regex]] — AI CLI 어댑터의 stdout 파서가 NDJSON·envelope·잡음 텍스트에 깨지는 함정
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

### analyses

- [[backtest-fill-model-adverse-selection]] — 백테스트 체결 모델과 역선택 — 즉시체결 가정이 은폐하는 것
- [[backtest-timeframe-sensitivity]] — 백테스트 봉 간격 민감도 — 지표 신호 효과는 timeframe 따라 뒤집힌다 + 공정 비교 방법론
- [[dca-intraday-buy-timing]] — 정액 DCA 의 일중 매수 타이밍 — 레버리지 ETF 분봉 실측 (시가 직후·14:30 회피)
- [[dca-trailing-stop-tuning]] — DCA·트레일링 스톱 튜닝 — 운영 로그 기반 진단·개선 패턴
- [[holding-period-signal-mismatch]] — 보유기간-신호 미스매치 — 같은 진입신호가 홀딩 호라이즌에 따라 손실↔초과수익으로 뒤집힌다
- [[holding-transaction-cost-basis-design]] — 보유 종목 매수/매도 거래 추적 설계 — 평균단가·실현손익 동결·삭제 역연산
- [[kis-balance-api-fields]] — KIS 잔고 API 응답의 현금 필드 의미 (예수금 vs 매수가능)
- [[kis-minute-chart-trs]] — KIS 분봉 조회 TR 비교 — 당일 전용(FHKST03010200) vs 과거일(FHKST03010230)
- [[llm-news-prediction-pitfalls]] — LLM 의 뉴스 기반 시장 예측 — 6가지 함정과 한계
- [[news-driven-market-signal-framework]] — 뉴스 기반 시장 시그널 추출 프레임워크 (7-축 + 3-시나리오)
- [[optimal-strategy-search-preconditions]] — 전략 탐색의 4가지 선결 조건 — "최적을 찾았다"를 판정하기 전에
- [[partial-sell-rule-idempotency]] — 알고리즘 트레이딩의 부분 매도 규칙 멱등성 패턴
- [[polling-interval-vs-bar-interval]] — 폴링 주기 vs 봉 단위 — 알고리즘 트레이딩의 시간 정합성
- [[risk-control-exemption-and-failed-attempt-accounting]] — 안전장치가 손실을 키울 때 — 리스크 감축 주문 면제와 실패 시도 회계
- [[scoring-system-ic-validation]] — 스코어 시스템의 IC (Information Coefficient) 검증 방법론
- [[scoring-version-comparison-methodology]] — 스코어 알고리즘 두 버전 비교 — 컷오프 캘리브레이션 vs 진짜 알파 변화
- [[signal-overfit-date-dispersion-check]] — 신호 vs 과최적화 판별 — 날짜 분산 + 시장 상대 대조
- [[stock-screening-score-design]] — 종목 스크리닝 점수 설계 — 게이트 vs 가산점 / 섹터 상대화 / 백테스트 생존 편향
- [[surge-chasing-exclusion-filter]] — 급등 종목 추격 배제 필터 — 하락장 레짐의 유일한 초과수익 규칙 (confidence medium — 단일 레짐 표본)

### decisions

- [[shared-broker-appkey-token-cache]] — 같은 broker appkey 를 두 프로세스가 쓸 때 — 토큰 캐시 공유 + rate limit 분담

### bugs

- [[absolute-stop-loss-elif-dead-code]] — 벤치마크 존재 시 절대손절 elif dead code — 손절 자체가 꺼진다
- [[equity-curve-max-vs-latest-aggregation]] — 평가곡선 집계 함정 — MAX(equity) 는 최신 잔고가 아니라 역대 고점
- [[flask-jsonify-infinity-breaks-browser-json]] — Flask jsonify 의 Infinity/NaN 이 브라우저 JSON.parse 를 깨는 침묵 함정
- [[kis-cash-d2-settlement-buy-rejection]] — KIS 매수가능 현금에 D+2 미정산분 누락 → RiskManager 매수 차단
- [[kis-derivative-etf-order-reject-apbk1497]] — KIS 파생·레버리지 ETF 주문 거부 [APBK1497·APBK1681] — 계좌 권한(선택확인서)·기본예탁금의 다층 게이트
- [[kis-holiday-detection-bsop-date]] — KIS 공휴일 휴장 판정 실패 — bsop_date 비교 대신 휴장일조회 API
- [[naver-finance-news-referer-required]] — 네이버 금융 종목 뉴스 크롤링 — Referer 헤더 없으면 빈 스텁 페이지
- [[naver-finance-no-info-selector-drift]] — 네이버 금융 table.no_info 셀렉터 드리프트 — 거래량·시가·등락률 silent 0 (가짜 fixture가 가린 버그)
- [[order-post-retry-double-fill]] — 비멱등 주문 POST 자동 재시도 — 이중 체결 위험과 취소 오판
- [[pykrx-krx-login-required]] — pykrx — KRX 로그인 의무화로 비로그인 조회가 JSONDecodeError·빈 DataFrame
- [[reentry-after-full-liquidation-no-cooldown]] — ht_trading 전량 청산 직후 재매수 — flat 재진입 쿨다운 부재
- [[relative-stop-benchmark-stale-price]] — 상대손절의 벤치마크 stale price — 종목은 실시간가, 지수는 일봉 캐시 종가
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
