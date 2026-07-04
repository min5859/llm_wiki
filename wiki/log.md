# 운영 로그

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
