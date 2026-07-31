---
title: Lint Report
date: 2026-08-01
---

# Wiki Lint Report (2026-08-01)

## 요약

- 검사 대상: `wiki/` 전체 113개 .md (본문 문서 111 + 메타 2: index.md·log.md). 총 15,214줄.
- 검출 문제 총수: **약 151건**
- 카테고리별 내역:
  - 모순: 7건 (확실 4 · 의심 3) + 명시적 정정 관계 6건(문제 아님, 참고)
  - 고립 페이지: 6건 (실질 고립 3 + index 미등록 3)
  - 전용 페이지 후보: 13건 (확실 8 · 의심 5)
  - 오래된 기술 의심: 15건 (확실 8 · 의심 7)
  - 링크 부족: 상호 링크 부족 17쌍 + 깨진 링크 고유 대상 59건 + 경로 오류 1건
  - 프런트매터 불비: 29건 (sources 8 · confidence 8 · updated 미갱신 5 · 변경 이력 섹션 누락 8)
  - 구조 이슈: 2건 (`summaries/` 카테고리 스키마 미반영, `gieok.md` 파일명 충돌)
- 양호 판정: `sensitivity: confidential` git 커밋 위반 0건, frontmatter enum 값 위반 0건, 빈 tags 0건, R1 불가시 문자 검출 0건.
- 검사 방법: 셸 기반 기계 검사(frontmatter·링크 그래프·git 추적) + 도메인별 전수 정독 감사(ai-agent 65 / trading 47) + 핵심 모순 6건 원문 인용 스팟체크.

## 모순

### 확실

1. **Karpathy 스킬 repo 소유자 표기 불일치** — `wiki/analyses/karpathy-claude-md-skills.md` 내부에서 제목·서두는 `multica-ai/andrej-karpathy-skills`(L2, L18), 설치 절·검증 한계 절은 `forrestchang/andrej-karpathy-skills`(L42, L69). `wiki/projects/oss-radar.md`(05-20 이력)는 `multica-ai`로 기술. 어느 쪽이 맞는지 확정 기록 없음.
2. **Hermes Studio 위임 이벤트 — "동작 코드가 옳았다" vs "코드 어휘도 실물과 달랐다"** — `wiki/projects/hermes-dashboard.md` L127(06-21 절)은 "동작 코드(`tool.started/completed`)가 옳음"이라 기술하나, `wiki/concepts/hermes-agent.md`(07-04 라이브 캡처)는 "문서와 코드 어휘가 둘 다 실물과 달랐다"로 실측. hermes-dashboard 자신의 07-05 이력은 정정했지만 본문 06-21 절 문구는 취소선 없이 잔존.
3. **`limit_price_ratio` 1.005 재채택 직전 값 — 0.995 vs 1.0** — `wiki/projects/ht-trading.md` L459(06-22 이력: "0.995 → 1.0") 이후인데 L867(07-05 이력)은 "0.995→1.005 재채택"으로 기술. `wiki/analyses/backtest-fill-model-adverse-selection.md` L50은 반전 이력을 "1.0→1.005→0.995→1.0"으로 정리해 직전 값을 1.0으로 봄. 동일 이벤트의 직전 값이 문서 내·문서 간 불일치.
4. **n_stock_info cron 주기 — "장중 매시간" vs "매 :20/:50 (30분 간격)"** — `wiki/projects/n-stock-info.md` L32 서두는 "장중 매시간", 같은 문서 L125와 `wiki/projects/ht-trading.md`(매수 시점 감사 로그 절)는 ":20/:50 30분 간격". `wiki/analyses/stock-screening-score-design.md` §3도 "시간당 cron"으로 옛 주기 기술.

### 의심

5. **OpenClaw main 에이전트 모델 — gpt-5.4 vs gpt-5.5** — `wiki/projects/openclaw.md`(에이전트 표): `openai-codex/gpt-5.4` / `wiki/bugs/openclaw-coder-silent-3-layer.md`(05-07): "main agent 는 … `openai-codex/gpt-5.5`". 중간에 모델 변경이 있었을 수 있으나 변경 기록이 없어 표면상 충돌.
6. **gieok auto-ingest 실행 횟수 — "하루 3회" vs "하루 1회 상한"** — `wiki/analyses/macos-launchagent-catchup-behavior.md`·`wiki/projects/gieok.md`(스케줄 절): 07:00/13:00/19:00 하루 3회 / `wiki/projects/gieok.md`(토큰 비용 모델 절): "`claude -p` headless 1회(하루 1회 상한)". "잡 3회·LLM 1회" 해석이 가능하나 관계 설명이 어디에도 없음.
7. **40점 스케일 컷 25의 위상 — "현 라이브" vs "백테스트 전용"** — `wiki/analyses/scoring-system-ic-validation.md` cutoff 표는 "25 (현 라이브)", `wiki/projects/ht-trading.md`·`wiki/analyses/scoring-version-comparison-methodology.md`는 "백테스트 40점/컷 25, 라이브 100점/컷 62"로 기술.

### 정정 관계 (모순 아님 — 명시적 정정이 기록된 쌍, 참고)

- `claude-code-model-tier-orchestration-gate` ↔ `summaries/claude-code-opus-orchestration-setup` (deep-reasoner/implementer 명명 — 양쪽 각주 처리)
- `hermes-agent` ↔ `hermes-dashboard` (Kanban API 존재 여부 06-20 정정)
- `newsletter-research-anti-bot-blocking` ↔ `research-write-agent-separation` (UA 우회 유효성 시점별 정정)
- `openclaw.md` 에이전트 표 `openai-codex/` 접두어 (인트로에서 과거 시점 주석)
- `signal-overfit-date-dispersion-check`(07-12) → `scoring-system-ic-validation`(07-09) vol_surge 근거 하향 — **단, 원 문서 미갱신 (→ 오래된 기술 #10)**
- `scoring-system-ic-validation`(07-09) → `holding-period-signal-mismatch` afternoon_eod 번복 — **단, 원 문서 미갱신 (→ 오래된 기술 #9)**

## 고립 페이지

index.md 가 전 문서를 [[wikilink]] 로 링크하는 허브라서 **완전 고립(인바운드 0)은 없음**. 아래는 두 가지 준(準)고립.

### index.md·log.md 에서만 링크됨 (본문 문서 인바운드 0)

- `wiki/patterns/oracle-cloud-free-tier-setup.md`
- `wiki/projects/agent-weekly.md`
- `wiki/projects/finance-analysis-nextjs.md`

### index.md 에 미등록 (인덱스 드리프트 — 역방향 고립)

- `wiki/analyses/code-change-rag-kb-design.md`
- `wiki/analyses/code-change-rag-kb-research.md`
- `wiki/patterns/code-change-rag-kb-spec.md`

세 문서는 서로 간 링크는 있으나 index 경유로는 발견 불가. 07-08 이력에 "인덱스 드리프트 보정" 선례가 있는 유형.

## 전용 페이지 후보

### ai-agent

1. **[확실] cron headless LLM 무응답 (`assistant_turns: 0` silent fail)** — 진단 분류(광범위=시스템 단 vs 산발=prompt/rate-limit vs 로거 출력 소실)가 5개 문서에 산재: `projects/dev-blog.md`(이력 20여 건), `projects/oss-radar.md`, `bugs/newsletter-research-anti-bot-blocking.md`, `analyses/research-write-agent-separation.md`, `patterns/llm-json-parse-retry-with-dump.md`.
2. **[확실] Cursor CLI (`cursor-agent`) 어댑터** — 6개 문서에서 실질 논의(`patterns/multi-llm-provider-adapter-pattern.md`, `bugs/ndjson-stdout-parser-greedy-regex.md`, `projects/dev-blog.md`, `patterns/agentic-cli-text-generation-lockdown.md`, `projects/kakao-db.md`, `projects/oss-radar.md`). `[[cursor-agent-cli-overview]]` 참조가 있으나 파일 미존재(v1 미이관 추정).
3. **[확실] research-wiki 프로젝트** — 운영·수정 이력이 4개 문서에 있는 실프로젝트인데 `projects/research-wiki.md` 부재: `projects/oss-radar.md`, `patterns/homebrew-python-upgrade-breaks-cron-venv.md`(커밋 해시까지 인용), `projects/kakao-db.md`, `projects/dev-blog.md`.
4. **[확실] `claude -p` headless 운영 패턴** — `env -u CLAUDECODE` 중첩 방지·구독 차감·타임아웃 지식이 5개 문서에 산재: `patterns/multi-llm-provider-adapter-pattern.md`, `projects/kakao-db.md`, `projects/oss-radar.md`, `projects/gieok.md`, `concepts/hermes-agent.md`.
5. **[확실] CLAUDE.md 작성·운영 가이드** — v1 `claude-md-guide` 미이관. `analyses/everything-claude-code.md`·`analyses/karpathy-claude-md-skills.md`가 `[[claude-md-guide]]` 를 실질 참조(파일 없음), `patterns/claude-code-model-tier-orchestration-gate.md`·`patterns/claude-code-token-optimization.md`·`analyses/personal-llm-wiki-curation.md`도 내용 보유.
6. **[의심] Claude Code Hook 시스템** — payload 스키마·SessionStart 주입·로드 시점 고정 함정이 4개 문서에 분산: `patterns/claude-code-model-tier-orchestration-gate.md`, `concepts/gieok.md`+`projects/gieok.md`, `patterns/claude-code-token-optimization.md`, `projects/agent-weekly.md`.
7. **[의심] 에이전트 페르소나 파일(SOUL.md)·정체성 관리** — `analyses/multi-profile-cli-agent-isolation.md`, `concepts/hermes-agent.md`, `projects/hermes.md`, `patterns/hermes-single-model-delegation.md`.

### trading

8. **[확실] 백테스트·평가 데이터의 생존 편향(survivorship bias)** — 5개 문서에서 실질 논의: `analyses/stock-screening-score-design.md` §3, `bugs/absolute-stop-loss-elif-dead-code.md`, `analyses/scoring-system-ic-validation.md`, `projects/n-stock-info.md` §4, `analyses/signal-overfit-date-dispersion-check.md`.
9. **[확실] 침묵 실패 탐지 — 음성 증거(있어야 할 이벤트의 부재) 검증** — 6개 문서: `analyses/partial-sell-rule-idempotency.md`, `analyses/risk-control-exemption-and-failed-attempt-accounting.md` §7, `bugs/sqlite-cross-thread-connection-threading-local.md`, `bugs/naver-finance-no-info-selector-drift.md`, `bugs/yahoo-finance-concurrent-silent-fail.md`, `bugs/kis-holiday-detection-bsop-date.md`.
10. **[확실] 라이브 데몬 stale 코드 — 수정 후 재시작 전까지 옛 로직 (launchctl kickstart -k 체크리스트)** — 6개 trading 문서가 각자 서술: `bugs/kis-holiday-detection-bsop-date.md`, `projects/upbit-trading.md`, `analyses/dca-trailing-stop-tuning.md`, `projects/ht-trading.md`(3회 이상), `projects/ht-dde.md`, `bugs/flask-jsonify-infinity-breaks-browser-json.md`. ai-agent 쪽 `bugs/stale-process-attributeerror-inprocess-coupling.md`는 진단법만 커버.
11. **[확실] eps-vs-earnings-yield** — 4개 문서가 실질 논의·참조하나 파일 미존재: `projects/n-stock-info.md` §1, `projects/ht-trading.md`(V3 재설계 절), `analyses/scoring-version-comparison-methodology.md`(V3 리버트 표), `analyses/stock-screening-score-design.md`(frontmatter related).
12. **[의심] 체결강도 지표** — 결론이 5개 문서에 흩어져 상충 이력 추적 곤란(밴드 엣지 → 무의미 → 역방향): `projects/ht-dde.md`, `analyses/holding-period-signal-mismatch.md`, `analyses/signal-overfit-date-dispersion-check.md`, `analyses/surge-chasing-exclusion-filter.md`, `analyses/scoring-system-ic-validation.md`.
13. **[의심] forward 라벨링/forward test 인프라** — `analyses/scoring-system-ic-validation.md` §6, `analyses/scoring-version-comparison-methodology.md`, `analyses/holding-period-signal-mismatch.md`, `projects/n-stock-info.md` §4, `projects/ht-dde.md`. 기존 방법론 문서 2개가 부분 커버라 우선순위 낮음.

## 오래된 기술 의심

schema.md 정책(오래된 정보는 삭제 대신 ~~취소선~~ + 이유 기록) 위반이 의심되는 곳.

### 확실

1. **`wiki/analyses/anthropic-oauth-third-party-billing-trap.md` L58** — "Codex CLI OAuth 는 … 비교적 무난. `~/.codex/auth.json` 자동 import 됨"이 `analyses/oauth-refresh-token-rotation-multi-client.md`(06-03: "복사(import)는 ❌ 위험 — 회전 쟁탈. 이번 Hermes 가 빠진 함정")로 번복됐으나 취소선 없이 유지.
2. **`wiki/projects/dev-blog.md` 운영 흐름 절** — "매일 07:00 KST (`StartCalendarInterval: Hour=7`)"인데 06-10 이후 이력 20여 건은 전부 03:00~05:00 사이클로 기록.
3. **`wiki/projects/dev-blog.md` 파이프라인 표·강점 절** — research 단계 없는 5-단계 표, "`runAiAdapter` 가 template/claude 만 노출" — 06-06 research 단계 확장, cursor 어댑터가 기본값이던 시기(05-16 전환)와 불일치.
4. **`wiki/concepts/openclaw-agent-architecture.md`** (2026-04-17 이후 무갱신) — "코딩은 ACP/Claude Code 전용 세션으로 분리가 적합·1순위" 확장 방향이 `decisions/openclaw-coder-default-model-codex.md`(05-07)와 `analyses/openclaw-acp-runtime-internals.md` §4("ACP 자제 정책")로 실질 수정됐으나 원 문서 미갱신. related 도 전부 v1 잔재.
5. **`wiki/analyses/holding-period-signal-mismatch.md`** (최종 07-04) — afternoon_eod 가설("10거래일 중 7일 시장 초과수익 … 가설 성립")이 `analyses/scoring-system-ic-validation.md`(07-09: "out-of-sample −2% 로 무너진 것")로 번복됐으나 원문 무갱신.
6. **`wiki/analyses/scoring-system-ic-validation.md`** — vol_surge "400%↑ 승률 64% n=58 / 500%↑ 승률 82% n=11"이 `analyses/signal-overfit-date-dispersion-check.md`(07-12: "독립 이벤트 약 10건, 실제 승률 50~60%대로 하향")로 하향됐으나 원문 수치 유지.
7. **`wiki/projects/upbit-trading.md` L25 서두** — "라이브 운영 중"인데 같은 문서 06-07 절은 "당분간 매매 정지, `launchctl unload` + plist symlink 제거". 서두 미갱신.
8. **`wiki/projects/ht-trading.md` 리스크 매니저 정책 표** — "`max_positions` 10종목 (05-18 기준)"·검증 순서 "`len(positions) >= 5`"가 05-31(→11)·07-06(→12) 두 차례 상향을 미반영.

### 의심

9. **`wiki/projects/hermes.md`** — maccoder `--clone` "메모리/세션 fresh"가 `concepts/hermes-agent.md`(07-05: "MEMORY.md 를 물려받아 전부 자기를 맥비로 인식" 실측)와 어긋남 (클론 시점 차이로 조건부일 수 있음).
10. **`wiki/projects/hermes-dashboard.md`** — "Kanban API 없음 → 로컬 store 전용 확정" 항목이 06-20 정정 이후에도 취소선 없는 "확정" 단정으로 잔존 (상단 폐기 배너는 존재).
11. **`wiki/projects/dev-blog.md` 설계 강점 2** — "무검토 게시 방지"(수동 승인)가 `PUBLISH_DAILY=1` 완전 자동 cron 운영 현실과 불일치.
12. **`wiki/projects/ht-trading.md` screener 코드블록** — "`min_score: 60`"이 바로 위 표의 "62 (05-30 복원)"와 불일치 (옛 스냅샷 잔존 추정).
13. **`wiki/analyses/kis-balance-api-fields.md`** — "매수가능 현금" 정의에 `analyses/risk-control-exemption-and-failed-attempt-accounting.md` §5의 "미체결 매수 예약금 차감" 보완이 미반영 (번복이 아닌 중요 보완 누락).
14. **`wiki/projects/ht-dde.md` 미해결 절** — "throttle 0.06→0.1초 … 적용 범위 결정 대기 중"인데 07-04 절은 "min_interval 0.1초 기준"으로 이미 적용된 듯 기술.
15. **`wiki/projects/ht-dde.md` 07-01 권고** — "손절폭 -10%→-5~6%가 유일·최우선 레버"가 07-04 실거래 미러링(-20% 백스톱 + 상대 -10%, 완화 방향)과 반대인데 채택/폐기 미기록.

## 링크 부족

### 깨진 링크 (존재하지 않는 문서로의 링크) — 고유 대상 59건

v1→v2 이관(07-02, 73건 선별 이관) 시 미이관 문서를 가리키는 related·본문 링크가 대량 잔존. 참조하는 문서가 많은 순 주요 대상:

- **"별도 문서로 분리했다"고 이력에 기록했으나 파일이 없는 것 (6건 — 가장 시급)**: `esm-live-binding-global-state`, `blocked-dependency-productive-workflow`, `sqlite-readonly-data-swap`, `upstream-fork-minimal-invasion`, `mock-first-demo-safety-net` (이상 `projects/hermes-dashboard.md` 06-21 "일반 패턴 4건 분리" 관련), `claude-code-auto-mode-safety-guardrails` (`projects/dev-blog.md` 06-20 "분리"), `long-lived-network-client-stuck-reconnect-loop` (`projects/hermes.md`).
- **복수 문서가 참조하는 v1 미이관 문서**: `claude-code-overview`(4), `claude-md-guide`(3), `cron-nvm-node-path-trap`(2), `test-driven-agent-loop`(2), `kakaotalk-mac-data-locations`(3), `kakao-messaging-automation-options`(2), `supabase-region-migration`(3), `personal-ai-agent-messaging-channels`(2), `zod-schema-per-entity`(2), `prisma-connection-pool-vercel-supabase`(2), `vercel-cron-best-practices`(2), `eps-vs-earnings-yield`(2), `round-winrate-exit-type-undercount`(2), `dict-get-default-no-bootstrap`(2), `averaging-down-vs-momentum-add-on`(2), `cursor-agent-cli-overview`(1, 전용 페이지 후보 #2와 동일).
- **단일 문서 참조**: `onemancompany-heterogeneous-agents-organization`, `claude-code-setup`, `claude-code-enterprise-security-bedrock`, `gieok-project`(실제 파일명 `projects/gieok.md` — 링크명 오류), `claude-code-scheduled-tasks`, `api-circuit-breaker-trading-pattern`, `claude-code-basic-usage`, `claude-code-advanced`, `claude-token-saving-tips`, `ai-agent-basics`, `claude-code-agent-teams-tmux`, `claude-code-loop-automation`, `claude-code-windows-wsl-tmux`, `mac-keyboard-shortcuts-for-windows-users`, `pgbouncer-direct-url-hybrid-routing`, `disk-monitor`, `ssh-cli-toolkit-essentials`, `claude-code-session-jsonl-format`, `github-pages-base-path-pattern`, `kernel-digest`, `ai-valuation-trustworthiness`, `financial-health-composite-scores`, `pdf-text-extraction-vs-ocr`, `ai-token-usage-cost-guard`, `vercel-timeout-browser-direct-api`, `nextjs-vercel-supabase-deployment`, `supabase-magic-link-single-user-allowlist`, `gemini-2-0-flash-free-tier-blocked`, `nextjs16-use-server-non-async-export`, `node-modules-symlink-copy-prisma`, `react-hook-form-zod-server-action`, `prisma-decimal-nextjs-serialization`, `github-search-api-topic-or-limitation`, `macos-tcc-documents-popup-diagnosis`, `launchd-daemon-vs-cron-periodic`, `notification-dedup-throttle`.
- 참고: `japa-asset-dashboard.md`·`finance-analysis-nextjs.md`의 깨진 링크 상당수(Next.js/Supabase/Vercel 계열)는 수집 기준 두 도메인 밖이라 v2 생성 대상이 아닐 가능성이 높음 — 생성보다 링크 정리가 적절해 보임.
- **경로 오류 1건**: `wiki/analyses/research-write-agent-separation.md` L32 related가 `wiki/analyses/llm-json-parse-retry-with-dump.md` 를 가리키나 실제 위치는 `wiki/patterns/`.
- **이력-본문 불일치 1건**: index.md 07-12 이력은 oracle-cloud 문서에서 "`ssh-cli-toolkit-essentials.md` 는 v2 미존재로 제거"라 기록했으나, `wiki/patterns/oracle-cloud-free-tier-setup.md` L129-130 본문(관련 맥락 절)에는 해당 링크와 `supabase-region-migration.md` 가 잔존.
- 제외(오탐): `wiki/concepts/gieok.md` L26의 `[[wikilink]]` 는 문법 예시 표기.

### 상호 링크 부족 (밀접한데 양방향 링크 전무) — 17쌍

ai-agent:
1. [확실] `concepts/openclaw-agent-architecture.md` ↔ `projects/openclaw.md` — 같은 시스템의 개념/프로젝트 문서인데 상호 링크 0.
2. [확실] `analyses/openai-codex-cli-overview.md` ↔ `analyses/oauth-refresh-token-rotation-multi-client.md` — Codex 인증 개요 ↔ 그 OAuth 의 회전 쟁탈 함정.
3. [확실] `analyses/claude-code-source-leak-internals.md` ↔ `concepts/claude-code-skills-plugins.md` — 양쪽 모두 "skills 벤더 간 표준 수렴"을 별도 절로 이중 서술.
4. [확실] `bugs/gieok-session-log-url-credential-masking-false-positive.md` ↔ `analyses/research-write-agent-separation.md` — credential 마스킹 span 파괴의 양면.
5. [의심] `concepts/ai-usage-philosophy.md` ↔ `analyses/karpathy-claude-md-skills.md` — "Global Rules 와 4원칙 동형" 논의. 전자의 related 는 전부 부재 파일이라 살아있는 링크 0.
6. [의심] `patterns/agentic-cli-text-generation-lockdown.md` ↔ `bugs/ndjson-stdout-parser-greedy-regex.md` — AI CLI stdout 계약 붕괴의 두 축.
7. [의심] `patterns/parallel-review-adversarial-fix-workflow.md` ↔ `patterns/claude-code-model-tier-orchestration-gate.md` — 서브에이전트 병렬 워크플로 실전 패턴 쌍.
8. [의심] `analyses/openclaw-telegram-group-setup.md` ↔ `bugs/openclaw-coder-silent-3-layer.md` — 코더 토픽 라우팅 설정 ↔ 코더 토픽 무응답 사건.

trading:
9. [확실] `analyses/stock-screening-score-design.md` ↔ `analyses/surge-chasing-exclusion-filter.md` — §4 급등 꼭지 편향이 필터의 직접 전신·동일 수치 인용.
10. [확실] `analyses/kis-balance-api-fields.md` ↔ `analyses/risk-control-exemption-and-failed-attempt-accounting.md` — "매수가능 현금" 산정의 전신/후속 보완 관계.
11. [확실] `analyses/backtest-timeframe-sensitivity.md` ↔ `analyses/polling-interval-vs-bar-interval.md` — 시간 해상도 정합성 자매 주제 (둘 다 backtest-fill-model 에서 함께 인용됨).
12. [의심] `analyses/kis-minute-chart-trs.md` ↔ `analyses/polling-interval-vs-bar-interval.md` — 1분봉 재검증에 필요한 과거일 분봉 TR 이 바로 FHKST03010230.
13. [의심] `analyses/surge-chasing-exclusion-filter.md` ↔ `analyses/scoring-version-comparison-methodology.md` — A/B 실험 슬롯 방법론 위에서 실행된 결과.
14. [의심] `analyses/partial-sell-rule-idempotency.md` ↔ `bugs/reentry-after-full-liquidation-no-cooldown.md` — 같은 매도 경로의 상태 관리 공백 계열.
15. [의심] `bugs/kis-derivative-etf-order-reject-apbk1497.md` ↔ `analyses/risk-control-exemption-and-failed-attempt-accounting.md` — 주문 거부 백오프 실측 ↔ 실패 시도 회계 원리.
16. [의심] `bugs/flask-jsonify-infinity-breaks-browser-json.md` ↔ `bugs/sqlite-cross-thread-connection-threading-local.md` — 같은 ht_dde 의 "살아있는데 기능만 침묵 마비" 계열.
17. [의심] `bugs/reentry-after-full-liquidation-no-cooldown.md` ↔ `patterns/backtest-clock-injection.md` — 백테스트 영구 차단 대상이 재진입 쿨다운(1080분).

## 프런트매터 불비

schema.md 필수 필드(title, domain, sensitivity, tags, created, updated, sources, confidence) 기준.

### sources 누락 (8건)

- `wiki/analyses/dca-trailing-stop-tuning.md`
- `wiki/analyses/llm-news-prediction-pitfalls.md`
- `wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md`
- `wiki/analyses/macos-launchagent-catchup-behavior.md`
- `wiki/analyses/news-driven-market-signal-framework.md`
- `wiki/analyses/partial-sell-rule-idempotency.md`
- `wiki/analyses/polling-interval-vs-bar-interval.md`
- `wiki/analyses/scoring-system-ic-validation.md`

### confidence 누락 (8건)

- `wiki/analyses/llm-news-prediction-pitfalls.md`
- `wiki/analyses/macos-launchagent-catchup-behavior.md`
- `wiki/analyses/multi-profile-cli-agent-isolation.md`
- `wiki/analyses/news-driven-market-signal-framework.md`
- `wiki/analyses/oauth-refresh-token-rotation-multi-client.md`
- `wiki/analyses/partial-sell-rule-idempotency.md`
- `wiki/concepts/gieok.md`
- `wiki/projects/agent-weekly.md`

### updated 필드가 변경 이력 최신 날짜보다 오래됨 (5건)

- `wiki/analyses/code-change-rag-kb-design.md` (updated=2026-07-12, 이력 최신=2026-07-13)
- `wiki/analyses/oauth-refresh-token-rotation-multi-client.md` (updated=2026-06-21, 이력 최신=2026-07-05)
- `wiki/concepts/claude-code-skills-plugins.md` (updated=2026-04-16, 이력 최신=2026-06-08)
- `wiki/decisions/openclaw-coder-default-model-codex.md` (updated=2026-05-07, 이력 최신=2026-06-13)
- `wiki/patterns/code-change-rag-kb-spec.md` (updated=2026-07-13, 이력 최신=2026-07-14)

### `## 변경 이력` 섹션 누락 (8건)

- `wiki/analyses/llm-news-prediction-pitfalls.md`
- `wiki/analyses/macos-launchagent-catchup-behavior.md`
- `wiki/analyses/multi-profile-cli-agent-isolation.md`
- `wiki/analyses/news-driven-market-signal-framework.md`
- `wiki/analyses/partial-sell-rule-idempotency.md`
- `wiki/bugs/highlights-action-validator-schema-drift.md`
- `wiki/concepts/gieok.md`
- `wiki/concepts/openclaw-agent-architecture.md`

### 메타 파일 (참고)

- `wiki/index.md`, `wiki/log.md`: frontmatter 전무. 메타 파일이라 예외가 합리적이나 schema.md 에 예외 규정이 없음 — 규정 추가 여부 판단 필요.

### 구조 이슈 (2건)

- **`wiki/summaries/` 디렉토리가 schema.md·CLAUDE.md 폴더 구조에 없음** — index.md 07-12 이력에 "신설 summaries 카테고리"로 의도는 기록됐으나 schema.md 미갱신 (문서-스키마 드리프트).
- **`gieok.md` 파일명 충돌** — `wiki/concepts/gieok.md` 와 `wiki/projects/gieok.md` 가 동일 basename. index.md 가 둘 다 `[[gieok]]` 으로 표기해 wikilink 해석이 모호하고, 인바운드 링크 계측도 구분 불가.

## R1: Unicode 불가시 문자 (prompt injection 감사)

**검출 없음** — 셸 측 pre-scan 결과, wiki/ 내의 어떤 .md 에서도 ZWSP / RTLO / SHY / BOM 등 불가시 문자는 검출되지 않았습니다.
