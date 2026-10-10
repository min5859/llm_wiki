---
title: Lint Report
date: 2026-10-11
---

# Wiki Lint Report (2026-10-11)

## 요약

- 검사 범위: `wiki/`의 **121개 파일**입니다. 지식 문서 117개, `index.md`·`log.md`·기존 리포트, 빈 임시 파일 1개를 확인했습니다.
- 검출·검토 항목: **총 132건**입니다. 확정된 결함뿐 아니라 모순 의심·전용 페이지 후보·갱신 의심도 포함합니다.
- 파일 생성·수정, 네트워크 조회, Git 작업은 수행하지 않았습니다.

| 카테고리 | 건수 | 집계 기준 |
|---|---:|---|
| 모순 | 5 | 사실·표현 충돌 단위 |
| 고립 페이지 | 2 | 메타 문서 2개, 지식 문서 0개 |
| 전용 페이지 후보 | 7 | 개념·주제 단위 |
| 오래된 기술 의심 | 12 | 갱신이 필요한 주장 묶음 |
| 링크 부족 | 75 | 깨진 대상 58 + 연결 후보 12쌍 + 인덱스 누락 4 + 모호한 링크 1 |
| 프런트매터 불비 | 30 | 필드 누락 19 + 자료형 1 + 갱신일 7 + 메타 문서 2 + 출처 형식의 공통 불일치 1 |
| R1 불가시 문자 | 1 | 제공된 pre-scan의 파일 단위, 2개 행 |

아래 경로는 별도 표시가 없으면 `wiki/` 기준입니다. 링크 집계에는 `related`, Markdown 링크, 위키링크를 포함하되 자기 링크·문법 예시·기존 lint 리포트의 문제 목록은 제외했습니다. 명시적으로 정정된 과거 기록은 별도의 모순으로 중복 집계하지 않았습니다.

## 모순

1. **스크리너 실행 주기 불일치**
   - `projects/n-stock-info.md:32`: “장중 매시간 실행”.
   - 같은 문서 125행 및 `projects/ht-trading.md:825`: 매 `:20`·`:50`, **30분 간격** 실행.
   - 프로젝트 소개와 구체적인 운영 기록의 주기가 다릅니다.

2. **25점 컷의 적용 환경 불일치**
   - `analyses/scoring-system-ic-validation.md`의 cutoff 표: **“25 (현 라이브)”**.
   - `projects/ht-trading.md`의 이중 점수 스케일 표: **백테스트 40점 만점·컷 25**, 라이브 100점 만점·컷 62.
   - `analyses/ht-trading-live-data-improvement-analysis.md`도 두 경로를 명확히 구분합니다. IC 문서의 “현 라이브” 표기는 적용 환경을 오해하게 합니다.

3. **지정가 비율 재채택 직전 값 불일치 — 확인 필요**
   - `projects/ht-trading.md:465`: 2026-06-22에 `0.995 → 1.0`.
   - 같은 문서 887행: 7월 개선에서 **`0.995 → 1.005` 재채택**.
   - `analyses/backtest-fill-model-adverse-selection.md`는 이전 이력을 `1.0 → 1.005 → 0.995 → 1.0`으로 정리합니다.
   - 중간 환원 기록이 누락됐거나 7월 이력의 직전 값이 잘못됐을 가능성이 있습니다.

4. **Karpathy 가이드 저장소 소유자 불일치 — 확인 필요**
   - `analyses/karpathy-claude-md-skills.md`: 제목은 `multica-ai/andrej-karpathy-skills`, 설치 설명과 원본 링크는 `forrestchang/andrej-karpathy-skills`.
   - `projects/oss-radar.md`의 5월 20일 기록은 `multica-ai`를 사용합니다.
   - 이전·포크 관계가 설명되지 않아 같은 프로젝트를 가리키는지 불명확합니다.

5. **gieok 실행 횟수의 관계 불명확**
   - `projects/gieok.md:60`: 기본 하루 3회, 07:00·13:00·19:00.
   - 같은 문서 100행: auto-ingest headless 호출은 **하루 1회 상한**.
   - `analyses/macos-launchagent-catchup-behavior.md`도 하루 세 번 예약을 사례로 듭니다.
   - 예약은 세 번이고 실제 LLM 호출만 하루 한 번인지, 설정이 변경됐는지 설명이 필요합니다.

## 고립 페이지

**지식 문서의 완전 고립은 검출되지 않았습니다.**

다른 문서에서 들어오는 명시적 링크가 없는 메타 문서는 다음 2개입니다.

- `index.md`
- `lint-report.md`

인덱스는 진입점이고 리포트는 생성 산출물이므로, 이를 지식 손실과 같은 심각도로 보기는 어렵습니다.

다음 3개는 완전 고립은 아니지만 **인덱스·로그 외 지식 문서에서 들어오는 링크가 없습니다**. 별도 문제로 중복 집계하지 않았습니다.

- `analyses/ht-trading-live-data-improvement-analysis.md`
- `projects/agent-weekly.md`
- `projects/finance-analysis-nextjs.md`

빈 임시 파일 `wiki/.lint-report.4uBYT2`는 내용이 없어 페이지 집계에서 제외했습니다.

## 전용 페이지 후보

빈도는 인덱스·로그·기존 리포트를 제외한 지식 문서 본문의 언급 문서 수입니다. 단순 언급도 포함하므로 생성 우선순위를 판단하는 보조 지표입니다.

| 후보 | 언급 문서 수 | 근거와 필요한 범위 |
|---|---:|---|
| **Headless LLM 실행 결과와 로그 누락 구분** | 5 | `projects/dev-blog.md`, `projects/oss-radar.md`, `patterns/llm-json-parse-retry-with-dump.md` 등에 분산돼 있습니다. `assistant_turns: 0`과 실제 산출·게시 실패를 구분하는 진단 기준을 모을 가치가 있습니다. |
| **Cursor CLI 어댑터** | 7 | `analyses/multi-llm-provider-adapter-pattern.md`, `bugs/ndjson-stdout-parser-greedy-regex.md` 등에 호출·출력 계약이 분산돼 있으며 `cursor-agent-cli-overview` 참조 대상은 없습니다. |
| **Research Wiki 게시 파이프라인** | 5 | `projects/oss-radar.md`, `projects/dev-blog.md`, `patterns/homebrew-python-upgrade-breaks-cron-venv.md` 등에 운영·이관 기록이 있습니다. 논문 뉴스가 아닌 자동화 운영 범위의 프로젝트 문서 후보입니다. |
| **CLAUDE.md 작성·유지 원칙** | 13 | `analyses/karpathy-claude-md-skills.md`, `analyses/everything-claude-code.md`, `patterns/claude-code-token-optimization.md` 등에 분산돼 있고 `claude-md-guide`가 없습니다. |
| **에이전트 Hook 생명주기와 적용 경계** | 7 | Claude 오케스트레이션, gieok, Codex 수집에서 반복됩니다. 기존 Codex 수집 문서와 구분되는 공통 개념 문서가 후보입니다. |
| **생존 편향과 평가 표본 선택** | 6 | `analyses/stock-screening-score-design.md`, `bugs/absolute-stop-loss-elif-dead-code.md`, 운영 성과 분석 등에 서로 다른 표본 누락 사례가 있습니다. |
| **EPS 절대값과 Earnings Yield 비교** | 3 | `projects/n-stock-info.md`, `projects/ht-trading.md`, `analyses/scoring-version-comparison-methodology.md`에서 반복 설명하지만 `eps-vs-earnings-yield`는 없습니다. |

## 오래된 기술 의심

1. **`afternoon_eod` 가설의 후속 실패 미반영**
   - `analyses/holding-period-signal-mismatch.md`: 10거래일 중 7일 초과수익을 근거로 보유기간 미스매치 가설이 성립한다고 설명합니다.
   - `analyses/scoring-system-ic-validation.md`의 7월 9일 기록: 해당 전략이 out-of-sample에서 약 −2%로 무너졌다고 설명합니다.
   - 원 분석에 후속 검증 결과와 현재 가설 상태가 연결돼 있지 않습니다.

2. **`vol_surge` 검증 강도의 하향 미반영**
   - `analyses/scoring-system-ic-validation.md`: 거래량 400% 이상 승률 64%, 500% 이상 82%를 강한 확증으로 제시합니다.
   - `analyses/signal-overfit-date-dispersion-check.md`의 7월 12일 재검토: 독립 이벤트가 약 10건 수준이며 근거가 약해졌다고 설명합니다.
   - 임계값·집계 단위·horizon 차이까지 함께 연결해야 기존 확증 표현의 과도한 재사용을 막을 수 있습니다.

3. **Upbit 봇의 현재 운영 상태**
   - `projects/upbit-trading.md` 서두: “라이브 운영 중”.
   - 같은 문서 후반: 매매 중지, `launchctl unload`, LaunchAgent symlink 제거.
   - 서두에서 중지 상태가 드러나지 않습니다.

4. **gieok의 LLM 실행 주체**
   - `concepts/gieok.md`, `projects/gieok.md`의 기본 설명은 Claude Hook과 `claude -p` 중심입니다.
   - 10월 10일 프로젝트 기록 및 `patterns/codex-session-capture-and-curation.md`: Claude 수집을 유지하면서 Codex 수집을 추가하고 예약 ingest·lint를 `GIEOK_LLM=codex`로 전환했습니다.
   - 제품 기본값과 해당 사용자 환경의 현재 설정을 구분하는 연결 설명이 부족합니다.

5. **Dev Blog 운영 개요가 이전 구조에 머묾**
   - `projects/dev-blog.md`의 파이프라인·운영 흐름: research 없는 단계표, `template`/`claude` 어댑터, 수동 승인, Mac 07:00 launchd.
   - 후속 기록: research/write 분리, Cursor 기본값, OCI systemd·`wiki-publisher` 운영, Mac 예약 중단.
   - 현재 운영을 찾는 독자가 문서 앞부분만 보면 다른 구조를 이해하게 됩니다.

6. **OSS Radar의 주기·실행 환경**
   - `projects/oss-radar.md` 서두는 주간 발굴, 자동화 절은 매일 09:00 Mac LaunchAgent로 설명합니다.
   - 같은 문서의 후속 기록은 05:00 실행과 OCI 공용 게시 계정 전환을 설명합니다.
   - 현재 운영 요약과 과거 설치 절차의 구분이 필요합니다.

7. **n_stock_info의 수집 원천**
   - `projects/n-stock-info.md`는 네이버 금융 수집 파이프라인으로 소개합니다.
   - `analyses/scoring-version-comparison-methodology.md`의 9~10월 사례: Naver HTML 수집 중단 후 KIS 중심으로 전환했고 기존 점수식을 운영 DB에 복구했다고 설명합니다.
   - 핵심 프로젝트 문서에 이 변경이 반영되지 않았습니다.

8. **뉴스레터 상위 재시도 부재 주장**
   - `bugs/newsletter-research-anti-bot-blocking.md:71`: 상위 파이프라인에 “재시도도, 알림도 없다”.
   - `projects/dev-blog.md`의 8월 2일 기록과 `patterns/llm-json-parse-retry-with-dump.md`: 토픽 단위 `retry_once` 도입 및 8월 3일 발동 확인.
   - 자동 대체 소스·알림의 존재까지 확인된 것은 아니지만, 재시도 부재 주장은 후속 구현으로 갱신돼야 합니다.

9. **ht_trading의 복사용 screener 설정**
   - `projects/ht-trading.md:96` 부근 코드블록: `min_score: 60`.
   - 바로 위 설정 동기화 설명과 후속 기록: 62 유지.
   - 현재 설정 예제로 복사하면 문서의 다른 설명과 달라집니다.

10. **ht_trading 매수 검증 순서의 포지션 한도**
    - `projects/ht-trading.md:499`: `len(positions) >= 5`.
    - 같은 문서에는 10·11을 거쳐 12로 증설한 기록이 있습니다.
    - 날짜가 붙은 과거 한도표와 별개로, 검증 순서의 고정 숫자 5가 낡았습니다.

11. **분할 매수 드로다운의 기준 가격**
    - `projects/ht-trading.md`의 G4-1 설명: 직전 분할 체결가 기준.
    - 10월 10일 보강한 4월 23일 최종 결과: 평단 기준 최대 드로다운으로 수정했다고 설명합니다.
    - 초기 설명과 최종 구현 기록의 적용 관계가 정리되지 않았습니다.

12. **기존 라이브 분석 스크립트의 손익 해석**
    - `projects/ht-trading.md`는 `scripts/analyze_live_period.py`를 기간별 손익·승률 비교 도구로 안내합니다.
    - `analyses/ht-trading-live-data-improvement-analysis.md`: 해당 스크립트가 매도 시그널 당시 미실현손익을 “실현 손익”으로 합산하고 새 실제 체결 로그 일부를 누락한다고 설명합니다.
    - 사용 안내에 알려진 측정 한계와 실제 Fill 장부 기준을 연결해야 합니다.

## 링크 부족

### 깨진 참조 — 고유 대상 58개

동일 대상을 `related`와 본문에서 반복 참조한 경우 한 건으로 집계했습니다. 표에는 대표 참조 페이지 하나씩을 표시합니다.

| 대표 참조 페이지 | 해결되지 않는 대상 |
|---|---|
| `concepts/ai-usage-philosophy.md` | `ai-agent-basics` |
| `projects/finance-analysis-nextjs.md` | `ai-token-usage-cost-guard`, `ai-valuation-trustworthiness`, `financial-health-composite-scores`, `pdf-text-extraction-vs-ocr`, `vercel-timeout-browser-direct-api` |
| `decisions/shared-broker-appkey-token-cache.md` | `api-circuit-breaker-trading-pattern` |
| `projects/ht-trading.md` | `averaging-down-vs-momentum-add-on`, `notification-dedup-throttle` |
| `projects/hermes-dashboard.md` | `blocked-dependency-productive-workflow`, `esm-live-binding-global-state`, `mock-first-demo-safety-net`, `sqlite-readonly-data-swap`, `upstream-fork-minimal-invasion` |
| `concepts/claude-code-skills-plugins.md` | `claude-code-advanced`, `claude-code-basic-usage` |
| `concepts/openclaw-agent-architecture.md` | `claude-code-agent-teams-tmux`, `claude-code-loop-automation`, `claude-code-windows-wsl-tmux`, `mac-keyboard-shortcuts-for-windows-users` |
| `projects/dev-blog.md` | `claude-code-auto-mode-safety-guardrails`, `github-pages-base-path-pattern`, `kernel-digest` |
| `analyses/everything-claude-code.md` | `claude-code-enterprise-security-bedrock`, `claude-code-setup`, `claude-md-guide` |
| `analyses/anthropic-oauth-third-party-billing-trap.md` | `claude-code-overview` |
| `analyses/news-driven-market-signal-framework.md` | `claude-code-scheduled-tasks` |
| `projects/agent-weekly.md` | `claude-code-session-jsonl-format` |
| `patterns/claude-code-token-optimization.md` | `claude-token-saving-tips` |
| `analyses/multi-profile-cli-agent-isolation.md` | `cron-nvm-node-path-trap` |
| `analyses/openai-codex-cli-overview.md` | `cursor-agent-cli-overview`, `test-driven-agent-loop` |
| `analyses/partial-sell-rule-idempotency.md` | `dict-get-default-no-bootstrap` |
| `patterns/launchd-plist-symlink-from-project.md` | `disk-monitor` |
| `analyses/stock-screening-score-design.md` | `eps-vs-earnings-yield` |
| `projects/japa-asset-dashboard.md` | `gemini-2-0-flash-free-tier-blocked`, `nextjs-vercel-supabase-deployment`, `nextjs16-use-server-non-async-export`, `node-modules-symlink-copy-prisma`, `react-hook-form-zod-server-action`, `supabase-magic-link-single-user-allowlist` |
| `analyses/macos-launchagent-catchup-behavior.md` | `gieok-project` |
| `projects/oss-radar.md` | `github-search-api-topic-or-limitation` |
| `projects/kakao-db.md` | `kakao-messaging-automation-options` |
| `patterns/macos-tcc-full-disk-access.md` | `kakaotalk-mac-data-locations` |
| `projects/ht-dde.md` | `launchd-daemon-vs-cron-periodic` |
| `analyses/research-write-agent-separation.md` | `llm-json-parse-retry-with-dump` — `related` 경로 오류 |
| `projects/upbit-trading.md` | `macos-tcc-documents-popup-diagnosis` |
| `analyses/ai-coding-agent-cost-and-context-patterns.md` | `onemancompany-heterogeneous-agents-organization` |
| `concepts/hermes-agent.md` | `personal-ai-agent-messaging-channels` |
| `patterns/csv-roundtrip-backup-restore.md` | `pgbouncer-direct-url-hybrid-routing`, `supabase-region-migration` |
| `analyses/holding-transaction-cost-basis-design.md` | `prisma-connection-pool-vercel-supabase`, `zod-schema-per-entity` |
| `projects/openclaw.md` | `prisma-decimal-nextjs-serialization` |
| `bugs/equity-curve-max-vs-latest-aggregation.md` | `round-winrate-exit-type-undercount` |
| `bugs/utc-iso-date-kst-rollover.md` | `vercel-cron-best-practices` |

특히 다음 두 대상은 신규 페이지 생성보다 기존 문서로의 연결 검토가 우선입니다.

- `wiki/analyses/llm-json-parse-retry-with-dump.md`: 실제 문서는 `wiki/patterns/llm-json-parse-retry-with-dump.md`입니다.
- `gieok-project`: 프로젝트 문서는 `wiki/projects/gieok.md`로 존재합니다.

나머지 부재 대상도 모두 생성 후보라는 뜻은 아닙니다. 수집 도메인 밖의 링크는 기존 문맥과 이관 정책 검토가 필요합니다.

### 직접 연결이 부족한 문서 — 12쌍

다음 쌍은 `related`와 본문을 합쳐도 서로를 직접 링크하지 않습니다.

| 문서 쌍 | 연결 이유 |
|---|---|
| `concepts/openclaw-agent-architecture.md` ↔ `projects/openclaw.md` | 같은 시스템의 개념과 실제 운영 기록 |
| `analyses/openai-codex-cli-overview.md` ↔ `analyses/oauth-refresh-token-rotation-multi-client.md` | 인증 개요와 다중 클라이언트 회전 충돌 |
| `analyses/claude-code-source-leak-internals.md` ↔ `concepts/claude-code-skills-plugins.md` | 공통으로 다루는 스킬 규격·이식성 |
| `bugs/gieok-session-log-url-credential-masking-false-positive.md` ↔ `analyses/research-write-agent-separation.md` | 후보 데이터 손상과 로그 마스킹 오탐의 구분 |
| `analyses/stock-screening-score-design.md` ↔ `analyses/surge-chasing-exclusion-filter.md` | 급등 추격 편향과 후속 배제 필터 |
| `analyses/kis-balance-api-fields.md` ↔ `analyses/risk-control-exemption-and-failed-attempt-accounting.md` | 현금 필드와 미체결 예약금 차감 |
| `analyses/backtest-timeframe-sensitivity.md` ↔ `analyses/polling-interval-vs-bar-interval.md` | 검증 봉과 운영 감시 주기의 정합성 |
| `analyses/partial-sell-rule-idempotency.md` ↔ `bugs/reentry-after-full-liquidation-no-cooldown.md` | 부분·전량 매도 후 상태 관리 |
| `analyses/ht-trading-live-data-improvement-analysis.md` ↔ `patterns/trading-performance-cash-flow-reconciliation.md` | 실제 체결·입출금·성과 장부의 후속 정리 |
| `analyses/dca-trailing-stop-tuning.md` ↔ `bugs/trailing-activation-current-profit-gate.md` | 트레일링 튜닝과 활성 후 검사 누락 |
| `bugs/stale-process-attributeerror-inprocess-coupling.md` ↔ `bugs/hermes-webui-runtime-import-offline.md` | 같은 WebUI의 stale 프로세스와 런타임 import 장애 감별 |
| `patterns/agentic-cli-text-generation-lockdown.md` ↔ `bugs/ndjson-stdout-parser-greedy-regex.md` | 출력 행동 제약과 stdout 파싱 방어 |

### 인덱스 미등록 — 4개

다른 페이지의 링크는 있지만 `index.md`에 등록되지 않았습니다.

- `analyses/code-change-rag-kb-design.md`
- `analyses/code-change-rag-kb-research.md`
- `patterns/code-change-rag-kb-spec.md`
- `bugs/hermes-webui-runtime-import-offline.md`

### 위키링크 대상 모호성 — 1건

`concepts/gieok.md`와 `projects/gieok.md`가 동일한 파일명을 사용합니다.

`index.md`를 포함한 9개 문서에서 `[[gieok]]`을 사용하므로 개념·프로젝트 중 어느 문서를 의도했는지 링크만으로 확정하기 어렵습니다. 두 문서의 인덱스 연결은 “미등록”이 아니라 이 모호성 문제로 집계했습니다.

## 프런트매터 불비

118개 문서의 존재하는 프런트매터는 YAML로 파싱됐으며 **구문 오류는 없었습니다**. 작성된 `domain`·`sensitivity`·`confidence` 값의 허용값 위반과 빈 `tags`도 검출되지 않았습니다.

### `sources` 누락 — 8건

`source_session`은 존재하더라도 스키마의 필수 `sources`를 대체하지 않습니다.

- `analyses/dca-trailing-stop-tuning.md`
- `analyses/llm-news-prediction-pitfalls.md`
- `analyses/llm-provider-aggregator-vs-local-vs-hub.md`
- `analyses/macos-launchagent-catchup-behavior.md`
- `analyses/news-driven-market-signal-framework.md`
- `analyses/partial-sell-rule-idempotency.md`
- `analyses/polling-interval-vs-bar-interval.md`
- `analyses/scoring-system-ic-validation.md`

### `confidence` 누락 — 8건

- `analyses/llm-news-prediction-pitfalls.md`
- `analyses/macos-launchagent-catchup-behavior.md`
- `analyses/multi-profile-cli-agent-isolation.md`
- `analyses/news-driven-market-signal-framework.md`
- `analyses/oauth-refresh-token-rotation-multi-client.md`
- `analyses/partial-sell-rule-idempotency.md`
- `concepts/gieok.md`
- `projects/agent-weekly.md`

### `related` 누락 — 3건

`schema.md`의 필수 프런트매터 예시에 포함된 필드입니다.

- `analyses/macos-launchagent-catchup-behavior.md`
- `concepts/gieok.md`
- `patterns/oracle-cloud-free-tier-setup.md`

### `sources` 자료형 오류 — 1건

- `concepts/gieok.md`: `sources: 1`로, 출처 목록이 아니라 정수입니다. 원본 위치도 추적할 수 없습니다.

### `updated`가 최신 변경 이력보다 오래됨 — 7건

| 페이지 | `updated` | 최신 변경 이력 |
|---|---|---|
| `analyses/code-change-rag-kb-design.md` | 2026-07-12 | 2026-07-13 |
| `analyses/oauth-refresh-token-rotation-multi-client.md` | 2026-06-21 | 2026-07-05 |
| `concepts/claude-code-skills-plugins.md` | 2026-04-16 | 2026-06-08 |
| `decisions/openclaw-coder-default-model-codex.md` | 2026-05-07 | 2026-06-13 |
| `patterns/code-change-rag-kb-spec.md` | 2026-07-13 | 2026-07-14 |
| `patterns/llm-json-parse-retry-with-dump.md` | 2026-07-30 | 2026-08-03 |
| `projects/dev-blog.md` | 2026-07-29 | 2026-10-10 |

### 메타 문서의 프런트매터 부재 — 2건

- `index.md`
- `log.md`

스키마는 “모든 wiki 문서”에 필수 필드를 요구하지만 메타 문서 예외를 명시하지 않습니다. 각 누락 필드가 아니라 문서당 한 건으로 집계했습니다.

`lint-report.md`는 사용자가 지정한 `title`·`date` 전용 형식을 따르는 산출물이므로 일반 지식 문서의 필수 필드 검사에서 제외했습니다.

### 출처 형식과 스키마의 공통 불일치 — 1건

**104개 문서**의 `sources` 목록에 스키마가 정한 `raw-sources/…` 또는 `local-only: …` 이외의 표기가 있습니다. 공통 규약 불일치 한 건으로 집계했습니다.

- 널리 사용되는 `session-logs/…`가 스키마에 정의돼 있지 않습니다.
- `raw/…`, 외부 URL, `local-repo: …`, 절대경로, 위키 문서 경로도 혼재합니다.
- `analyses/code-change-rag-kb-design.md`와 `analyses/code-change-rag-kb-research.md`는 세션·워크플로 설명을 출처로 사용합니다.
- `concepts/openclaw-agent-architecture.md`는 `local environment observation`, `assistant conversation summary`처럼 특정 원본을 식별하기 어려운 설명을 사용합니다.

이는 출처가 모두 없다는 판정이 아닙니다. 실제 운영에서 허용하는 출처 종류와 스키마 사이의 불일치이며, 설명형 출처에는 원본 추적성 문제도 있습니다.

## R1: Unicode 불가시 문자 (prompt injection 감사)

제공된 셸 pre-scan 결과를 그대로 기재합니다.

- `wiki/patterns/csv-roundtrip-backup-restore.md` (lines 81,84)

재측정 및 자동 수정은 수행하지 않았습니다. 이 검출만으로 악성 지시 삽입이 확인된 것은 아닙니다.