# 운영 로그

## 2026-07-08 (ingest)

- **session-logs 유래** — 미처리 49건 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음(디렉터리 비어 있음).
  - 신규 1건: trading `bugs/kis-derivative-etf-order-reject-apbk1497` — 무한매수에 추가한 레버리지 ETF `0195S0` 이 매수 신호는 정상인데 30분 사이클마다 **24회 전부 거부**. 원인은 코드/설정이 아니라 **계좌 파생ETF 미신청** (`[APBK1497]` 선택확인서 미징구 + 레버리지 ETP 사전교육 미이수). 일반 주식은 무관. 교훈: 신호 정상인데 주문만 100% 거부 → 브로커 에러코드부터 보고, 계좌 권한은 종목 유형별로 다르며 파생/레버리지 편입은 계좌 상품 권한이 선결 (출처 2c24).
  - 갱신 5건 (전부 출처 2c24 · ea52):
    - `projects/ht-trading` — ① 0195S0 24회 거부 [APBK1497] 절(신규 버그 링크). ② **매수 시점 스코어 감사 로그** 절: 추천 소스 n_stock_info 의 일자 멱등 재작성(:20/:50 cron DELETE→INSERT)이 매수 시점 스냅샷을 덮어써 사후 DB 추적 불가 → `BuyCandidate.report_date` + 소수점 점수·현재가·기준일 전용 감사 로그 도입(`test_scoring_buy_audit_log.py`, 393 테스트). 2단계 컷(추천 55 vs 매수 62)·`%.0f` 반올림 은폐 기록.
    - `projects/n-stock-info` — §5 에 "하류 관측성 비용" 절: 일자 멱등 저장이 point-in-time 이력을 파괴해 다운스트림 매수 종목이 사후 소실됨. 자체 추천 컷 55 vs ht_trading 매수 컷 62.
    - `analyses/stock-screening-score-design` — §5 에 두 번째 실증(소비 측 감사 로그로 저장 정책 불변한 채 재현성 확보).
    - `projects/gieok` — **토큰 비용 모델** 절: "LLM 미호출 ≠ 토큰 0". wiki 목차 주입은 `claude -p` 는 안 부르지만 매 세션·매 턴 입력에 index 전문이 실림 → 비용 = index 크기 × 세션 수. v1→v2 index 85% 축소(55KB→9KB) 실측.
    - `patterns/claude-code-token-optimization` — 관련 맥락에 "SessionStart 주입 컨텍스트 = 반복 입력 비용" 원칙 한 줄.
  - **인덱스 드리프트 보정**: 07-07 저녁 cron(commit 20260707-2227)이 fe2f·ac9d 를 이미 인제스트하며 5개 페이지를 생성했으나 index.md 미등록 상태였음 → index 에 `dca-intraday-buy-timing`·`kis-minute-chart-trs`·`naver-finance-news-referer-required`·`pykrx-krx-login-required`·`relative-stop-benchmark-stale-price` 추가.
  - **이미 인제스트되어 플래그만 갱신 (신규 없음)**: fe2f(무한매수 0195S0 추가 — ht-trading §종목추가·dca-intraday-buy-timing·kis-minute-chart-trs·relative-stop 에 완전 반영), ac9d(RS EOD 스캐너 — ht-dde §RS·pykrx·naver-referer·optimal-strategy §5 에 완전 반영). 07-07 cron 이 내용은 넣었으나 `ingested` 플래그를 못 넘긴 것을 이번에 정리.
  - 스킵 45건: dev-blog cron 뉴스레터/리서치 dossier (Linux Daily·Android Kernel·Opensource Trending/Curation·AI Coding Agents·Linux Kernel Lens) 전량 — 뉴스성, 파이프라인 메타 지식은 기존 문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards·dev-blog)에 이미 흡수. AI Coding Agents dossier 표본 확인 결과 신 릴리스·체인지로그 조사 산출물로 재조회 가치 없음.
  - 참고: 2c24 세션에서 사용자가 실수로 붙여넣은 `DART_API_KEY` 값은 비밀 정보이므로 wiki 어디에도 기록하지 않음.

## 2026-07-06 (ingest)

- **session-logs 유래** — 미처리 29건 처리. raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음(디렉터리 비어 있음).
  - 신규 1건: ai-agent `bugs/stale-process-attributeerror-inprocess-coupling` — hermes-webui(:8787) `'ToolEntry' object has no attribute 'dynamic_schema_overrides'` 사례. 디스크 코드에는 속성이 있는데 런타임만 AttributeError → **코드 버그가 아니라 stale 장수 프로세스**. 진단은 `ps lstart/etime`(기동시각) ↔ `stat %Sm`(파일 mtime) ↔ `git log -S`(속성 도입시점) 3종 대조로 "프로세스가 파일 갱신보다 먼저 떴음" 확정, 해결은 재시작. launchd 관리 데몬은 `kill`=자동 respawn 이라 `ctl.sh start` 불필요(포트 충돌). in-process import 강결합([[self-hosted-agent-webui-integration]] 방식 A "버전 동기화 필수")의 실제 발현이므로 그 분석에 역링크 추가 (출처 3336).
  - 갱신 1건: `analyses/self-hosted-agent-webui-integration` (방식 A 단점에 "파일 갱신해도 프로세스 미재시작 시 런타임 AttributeError" 절 + related 추가).
  - 스킵 28건: dev-blog cron 뉴스레터/리서치 dossier 26건 (뉴스성 — 파이프라인 메타 지식은 기존 문서에 이미 흡수), `d1fe 디스크 사용 상태 확인` (일회성 상태 조회), `02ad 새 OCI 계정 무료티어` (오라클 클라우드 가입·VM 생성·SSH·재시도 스크립트 — 두 도메인 밖 인프라 셋업). 승격 규칙상 첫 등장이나 stale-process 버그는 도메인 핵심 운영 지식이라 즉시 기록.

## 2026-07-05 (ingest)

- **session-logs 유래** — 미처리 28건 처리 (인터랙티브 대형 세션 3건 + dev-blog cron 25건). raw-sources/·.cache/extracted/·fetched/·mcp-note 는 대상 없음.
  - 신규 12건:
    - trading (ht_trading 7/4 전면 개선 세션 2dad): `bugs/absolute-stop-loss-elif-dead-code` (red-link 해소 — Rule1a -20% 무조건 백스톱, 백스톱은 스윕 최적값이 아니라 tail 보험), `bugs/order-post-retry-double-fill` (비멱등 주문 POST 재시도 이중 체결 + 취소 오판 상태 재조회 확정), `patterns/backtest-clock-injection` (벽시계 오염 → 거래 43→249건·수익 +2.4%→+20% 재측정), `analyses/backtest-fill-model-adverse-selection` (즉시체결 가정이 역선택 은폐, limit ratio V자 스윕 + 장중 리플레이 검증), `patterns/startup-dependency-crash-loop` (주말 배포 크래시 루프 — startup/steady-state 오류 비대칭)
    - trading (ht_dde 목표 검토 세션 8c43): `analyses/optimal-strategy-search-preconditions` (목적함수·레짐·counterfactual·비용 우선의 4 선결 조건), `patterns/mirror-config-drift-guard-test` (원본 yaml 직접 읽어 자동 대조 + allowlist)
    - ai-agent (hermes 멀티에이전트 실험 세션 e509): `analyses/weak-model-agent-reliability-compounding` (0.9⁵≈59% 신뢰도 복리 붕괴 → 결정적 워크플로우 + 검증 게이트), `analyses/multi-agent-orchestration-taxonomy` (delegate/칸반/조직 3분류 + 선형 코딩 파이프라인은 조율 비용이 이득 잠식), `analyses/multi-agent-shared-wiki-concurrency` (다중 동시 쓰기는 실험적 확장 — 단일 큐레이터·git·lint cron), `patterns/single-dispatcher-per-queue` (칸반 claim churn — 보드당 dispatcher 하나)
    - ai-agent (2dad+5338 두 번째 등장으로 승격): `patterns/parallel-review-adversarial-fix-workflow` (병렬 리뷰 → 교차검증 → TDD 수정 → 적대적 검증 + exit-code masking 함정)
  - 갱신 13건: `projects/ht-trading` (7/4 커밋 20여 개 종합 — limit_price_ratio 1.005 재채택 근거 포함), `projects/ht-dde` (목표 재정렬·실거래 미러링 절), `analyses/risk-control-exemption-and-failed-attempt-accounting` (손실 한도 SELL 차단 + 배치 스냅샷 투영), `analyses/scoring-version-comparison-methodology` (재가중 무효 조건 + 실험 슬롯 운영), `analyses/stock-screening-score-design` (급등 꼭지 편향 §4 + 멱등 vs point-in-time §5), `concepts/hermes-agent` (위임 이벤트 실측·API_SERVER_KEY·FTS·dispatcher·클론 정체성), `analyses/hermes-paperclip-adapter` (패키지판 builtin 어댑터 정정), `analyses/multi-profile-cli-agent-isolation` (§5 정체성은 SOUL 에), `projects/hermes` (update=origin/main 함정), `patterns/launchd-secret-management` (ssh-agent 부재 절), `analyses/oauth-refresh-token-rotation-multi-client` (run.failed 발현 경로), `projects/hermes-dashboard` (7/4 세션 이력), `projects/dev-blog` + `analyses/research-write-agent-separation` (7/5 사이클 — write "에이전트형 표류"로 stdout 계약 파괴, 3주 silent fail 서사의 상태 변화)
  - 스킵: 20260705 cron 뉴스 콘텐츠 전량 (뉴스성 — 기존 결정 동일), AI Coding dossier 의 스테가노그래피 마킹 등 단발 제품 뉴스, eslint↔tsc 충돌·vite dev 미들웨어 shadowing (도메인 밖 프론트 툴체인), ruff 도입·DRY 리팩터링·좀비 vite 프로세스 등 일회성. 서브에이전트 4대 병렬 트리아지로 기존 문서와 중복 대조 완료.

## 2026-07-04 (ingest)

- **GitHub 프로젝트 분석 유래** — `NousResearch/hermes-paperclip-adapter` 를 ai-agent 분석 문서로 승격.
  - 신규 1건: `analyses/hermes-paperclip-adapter` (Paperclip 이슈/heartbeat 를 Hermes CLI 실행으로 연결하는 런타임 어댑터, 논문 scraper 아님, 적용 조건은 Paperclip+Hermes 동시 운영, 장점은 AI agent 를 작업 큐 worker 로 붙이는 것, 주요 리스크는 비대화형 실행을 위한 `--yolo`).

- **session-logs 유래** — 미처리 26건 처리 (v2 첫 정식 ingest 사이클). raw-sources/·.cache/extracted/·mcp-note 는 대상 없음.
  - 신규 2건: `analyses/personal-llm-wiki-curation` (v1 write-only 창고 실패 → 도메인 한정·2회 등장 승격·재조회율 기준·지식 3분류, 출처 ea52), `analyses/holding-period-signal-mismatch` (같은 진입신호가 보유기간에 따라 손실↔초과수익 역전 + 검증 엣지를 청산 규칙이 잘라내지 않는 설계, 출처 5338)
  - 갱신 5건: `projects/gieok` (vault v1→v2 전환 연결 지점 3곳 + hook 세션 시작 고정 함정 + 보존 정책 07:40), `projects/ht-dde` (afternoon_eod 실전화 + 워크플로 심층 분석 발견), `decisions/shared-broker-appkey-token-cache` (구현 검증 + 비원자적 캐시 쓰기 리스크), `analyses/research-write-agent-separation` (evidence-stuffing / openQuestions 자동 주입 / 마스킹 span 파괴 3건), `projects/dev-blog` (7/3·7/4 사이클 운영 관찰 — write 로그 소실 확대·후보 공급 정체·MIME 미디코딩)
  - 스킵: 뉴스레터 cron 로그 24건의 뉴스 콘텐츠 전량 (뉴스성 — 수집 기준 밖), 파이프라인 메타 지식만 위 문서들로 흡수. 서브에이전트 트리아지로 기존 3문서(research-write-agent-separation·llm-newsletter-rewrite-metadata-grounding·llm-content-quality-guards)와 중복 대조 완료.

## 2026-07-02

- llm_wiki v2 초기 셋업. v1의 주제 산개 교훈을 반영해 도메인을 ai-agent·trading 2개로 한정.
- 수집 기준·승격 규칙·읽기 경로를 CLAUDE.md에 명문화.
- v1에서 73건 이관 완료 (ai-agent 44 · trading 29). 선별 기준: 참조 5+ 또는 변경 이력 2+ 또는 도메인 고유 지식. 상세는 MIGRATION.md.
- frontmatter `domain` 을 `ai-agent | trading` 으로 전건 갱신, index.md 전건 등록.
