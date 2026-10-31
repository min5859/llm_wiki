---
title: Lint Report
date: 2026-11-01
---

# Wiki Lint Report (2026-11-01)

## 요약

- 검사 대상: `wiki/` 전체 **128개 파일** — 지식 문서 125개와 `index.md`, `log.md`, 기존 리포트입니다.
- 검출·검토 항목: **총 130건**입니다. 확정 결함과 확인이 필요한 후보를 포함합니다.
- 파일 생성·수정, 네트워크 조회, Git 작업, 다른 에이전트 실행은 수행하지 않았습니다.

| 카테고리 | 건수 | 집계 기준 |
|---|---:|---|
| 모순 | 4 | 사실·표현 충돌 단위 |
| 고립 페이지 | 2 | 지식 문서 0개, 메타 문서 2개 |
| 전용 페이지 후보 | 8 | 개념·프로젝트 단위 |
| 오래된 기술 의심 | 11 | 후속 정보가 반영되지 않은 주장 묶음 |
| 링크 부족 | 75 | 깨진 대상 58개, 연결 후보 12쌍, 인덱스 누락 4개, 모호한 대상 1개 |
| 프런트매터 불비 | 29 | 필드 누락 18건, 갱신일 7건, 중복 키 1건, 메타 문서 2건, 출처 규약 불일치 1건 |
| R1 불가시 문자 | 1 | 제공된 pre-scan의 파일 단위 |

아래 경로는 별도 표시가 없으면 `wiki/` 기준입니다. 링크 검사에는 `related`, Markdown 링크, 위키링크를 포함했습니다. 자기 링크·문법 예시·기존 리포트의 문제 목록은 연결 근거에서 제외했습니다.

날짜와 정정 관계가 명시된 과거 기록은 그 자체로 모순으로 집계하지 않았습니다. 외부 사실의 현행 여부는 재검증하지 않았습니다.

## 모순

1. **스크리너 25점 컷의 적용 환경 불일치**
   - `analyses/scoring-system-ic-validation.md:67`은 cutoff 25를 **“현 라이브”**로 표시합니다.
   - `projects/ht-trading.md`의 이중 점수 스케일 표는 백테스트를 **40점 만점·컷 25**, 라이브를 **100점 만점·컷 62**로 구분합니다.
   - IC 문서의 적용 환경 표시가 프로젝트 설명과 다릅니다.

2. **지정가 비율 재채택 직전 값 불일치 — 확인 필요**
   - `projects/ht-trading.md:478`은 6월 22일 `limit_price_ratio`를 **0.995 → 1.0**으로 변경했다고 기록합니다.
   - 같은 문서 1146행은 7월 개선을 **0.995 → 1.005 재채택**으로 설명합니다.
   - `analyses/backtest-fill-model-adverse-selection.md:55`는 앞선 이력을 `1.0 → 1.005 → 0.995 → 1.0`으로 정리합니다.
   - 중간 환원 기록이 빠졌거나 7월 기록의 직전 값이 잘못됐을 가능성이 있습니다.

3. **Karpathy 가이드 저장소 소유자 불일치 — 확인 필요**
   - `analyses/karpathy-claude-md-skills.md:2,18`의 제목은 `multica-ai/andrej-karpathy-skills`입니다.
   - 같은 문서 42행의 설치 설명과 69행의 원본 링크는 `forrestchang/andrej-karpathy-skills`를 가리킵니다.
   - `projects/oss-radar.md`의 5월 20일 기록도 `multica-ai`를 사용합니다.
   - 이전·포크 관계가 설명되지 않아 동일 프로젝트인지 불명확합니다.

4. **Dev Blog 토픽 수와 열거 내용 불일치**
   - `projects/dev-blog.md`의 운영 흐름도에는 **“daily 10 토픽”**이라고 적혀 있습니다.
   - 바로 뒤 열거는 `linux / android / opensource / lens 8 / opensource-curation`으로, 합계 **12개**입니다.
   - 같은 흐름도 안에서 개수와 구성 목록이 일치하지 않습니다.

## 고립 페이지

**지식 문서의 완전 고립은 검출되지 않았습니다.**

다른 페이지에서 들어오는 명시적 링크가 없는 메타 문서는 다음 2개입니다.

- `index.md`
- `lint-report.md`

인덱스는 진입점이고 리포트는 점검 산출물이므로, 일반 지식 문서의 고립과는 영향이 다릅니다.

추가로 다음 문서는 인덱스·로그를 제외한 지식 문서에서 들어오는 링크가 없습니다. 별도 문제로 중복 집계하지 않았습니다.

- `projects/agent-weekly.md`
- `projects/finance-analysis-nextjs.md`

## 전용 페이지 후보

후보는 수집 도메인 안에서 반복되는 지식을 기준으로 선정했습니다. 기존 문서의 보강·통합으로 해결할 수 있는지도 함께 검토할 대상입니다.

| 후보 | 반복 근거와 정리할 범위 |
|---|---|
| **Headless 실행의 로그 누락과 실제 산출 실패 구분** | `projects/dev-blog.md`, `projects/oss-radar.md`, `analyses/research-write-agent-separation.md`, `patterns/llm-json-parse-retry-with-dump.md` 등에 분산돼 있습니다. `assistant_turns: 0`만으로 실패를 확정하지 않는 진단 기준이 필요합니다. |
| **Cursor CLI 어댑터** | `analyses/multi-llm-provider-adapter-pattern.md`, `analyses/openai-codex-cli-overview.md`, `bugs/ndjson-stdout-parser-greedy-regex.md` 등에서 반복됩니다. 호출·인증·출력 계약을 참조할 `cursor-agent-cli-overview`는 없습니다. |
| **Research Wiki 게시 파이프라인** | `projects/oss-radar.md`, `projects/dev-blog.md`, `analyses/ai-publishing-migration-safety.md`, `patterns/homebrew-python-upgrade-breaks-cron-venv.md` 등에 운영 기록이 분산돼 있습니다. 자동화 구조·이관·부분 실패 판단을 모을 후보입니다. |
| **CLAUDE.md 작성과 공통 지침 유지** | `analyses/karpathy-claude-md-skills.md`, `analyses/everything-claude-code.md`, `patterns/claude-code-token-optimization.md`, `analyses/ai-publishing-migration-safety.md`에서 반복됩니다. `claude-md-guide` 참조 대상은 없습니다. |
| **에이전트 Hook 생명주기와 적용 범위** | `projects/gieok.md`, `patterns/codex-session-capture-and-curation.md`, `patterns/claude-code-model-tier-orchestration-gate.md`에서 수집·권한·주입 경계가 반복됩니다. 기존 개별 구현과 공통 원리를 연결할 후보입니다. |
| **생존 편향과 평가 표본 선택** | `analyses/stock-screening-score-design.md`, `analyses/scoring-system-ic-validation.md`, `projects/n-stock-info.md`, `bugs/absolute-stop-loss-elif-dead-code.md`에 표본 누락 사례가 분산돼 있습니다. |
| **EPS 절대값과 Earnings Yield 비교** | `projects/n-stock-info.md`, `projects/ht-trading.md`, `analyses/scoring-version-comparison-methodology.md` 등에서 반복됩니다. `eps-vs-earnings-yield`는 없습니다. |
| **news-monitor 운영 구조** | `analyses/ai-publishing-migration-safety.md`, `analyses/llm-content-quality-guards.md`에 검색 예산·분류·브리핑·배포 기록이 누적돼 있습니다. 공통 분석과 구분되는 프로젝트 구조·현재 상태의 참조점이 없습니다. |

## 오래된 기술 의심

1. **보유기간 미스매치 가설의 후속 실패 미반영**
   - `analyses/holding-period-signal-mismatch.md:20,33`은 10거래일 중 7일 초과수익을 근거로 가설 성립·페이퍼 검증 중 상태를 설명합니다.
   - `analyses/scoring-system-ic-validation.md:149`는 `afternoon_eod`가 out-of-sample에서 약 −2%로 무너졌다고 기록합니다.
   - 원 분석에 후속 검증 결과와 가설 상태가 연결돼 있지 않습니다.

2. **`vol_surge` 확증 표현의 하향 조정 미반영**
   - `analyses/scoring-system-ic-validation.md:133,157,158`은 독립 재현과 거래량 급증 조건의 승률 64~82%를 강조합니다.
   - `analyses/signal-overfit-date-dispersion-check.md:81,83`은 독립 이벤트가 약 10건 수준이고 기존 근거가 약해졌다고 설명합니다.
   - 임계값·horizon·집계 단위가 다르므로 수치를 단순 대체하기보다, 후속 검증의 제한을 원 문서에 연결할 필요가 있습니다.

3. **Upbit 봇의 현재 운영 상태**
   - `projects/upbit-trading.md:25`는 “라이브 운영 중”이라고 소개합니다.
   - 같은 문서 140행은 매매 정지·LaunchAgent unload·symlink 제거를 최종 결정으로 기록합니다.
   - 서두에서 중지 상태를 확인하기 어렵습니다.

4. **Dev Blog의 초기 운영 개요**
   - `projects/dev-blog.md:155` 이후 단계표는 research가 없는 흐름, `template`/`claude` 어댑터, 수동 승격을 설명합니다.
   - 177행의 운영도는 Mac 07:00 launchd입니다.
   - 후속 절은 research/write 분리, Cursor 기본값, OCI systemd·`wiki-publisher` 운영과 Mac 예약 중단을 기록합니다.
   - 초기 개요와 현재 운영의 구분이 부족합니다.

5. **OSS Radar의 주기와 실행 환경**
   - `projects/oss-radar.md:79,81`은 주간·매주 발굴로 소개하고 자동화 설정에는 매일 09:00 Mac 예약이 남아 있습니다.
   - 262행은 OCI 공용 게시 계정과 03:00·04:00·05:00 분리 실행을 기록합니다.
   - 현재 운영 요약과 과거 설치 절차의 구분이 필요합니다.

6. **뉴스레터 상위 재시도 부재 주장**
   - `bugs/newsletter-research-anti-bot-blocking.md:71`은 상위 파이프라인에 “재시도도, 알림도 없다”고 설명합니다.
   - `projects/dev-blog.md:450,451`과 `patterns/llm-json-parse-retry-with-dump.md:191`은 8월 2일 토픽 단위 `retry_once` 도입과 다음 날 발동을 기록합니다.
   - 알림·대체 소스의 구현까지 확인된 것은 아니지만, 재시도 부재 설명은 후속 상태와 다릅니다.

7. **ht_trading의 복사용 점수 설정**
   - `projects/ht-trading.md:106`의 설정 예시는 `min_score: 60`입니다.
   - 바로 위 설정 동기화 설명과 후속 운영 기록은 **62 유지**입니다.
   - 현재 설정 예제로 복사하면 다른 설명과 달라집니다.

8. **ht_trading 매수 검증 순서의 포지션 한도**
   - `projects/ht-trading.md:512`는 신규 종목 거부 조건을 `len(positions) >= 5`로 설명합니다.
   - 같은 문서에는 한도를 10·11·12로 늘린 기록이 있습니다.
   - 검증 순서의 고정 숫자 5가 과거 값으로 남아 있습니다.

9. **분할매수 드로다운 기준**
   - `projects/ht-trading.md:135`는 직전 분할 체결가 대비 하락으로 설명합니다.
   - 865행의 최종 결과는 평단 기준 최대 드로다운으로 수정됐다고 기록합니다.
   - 초기 설명에서 최종 구현과의 관계를 알기 어렵습니다.

10. **라이브 분석 스크립트의 손익 해석**
    - `projects/ht-trading.md:649` 이후는 `scripts/analyze_live_period.py`를 성과 비교 도구로 안내합니다.
    - `analyses/ht-trading-live-data-improvement-analysis.md:175`는 이 도구가 매도 시그널 당시 미실현손익을 “실현 손익”으로 합산하고 실제 체결 로그 일부를 누락한다고 설명합니다.
    - 사용 안내에 측정 한계와 실제 Fill 장부 기준이 연결돼 있지 않습니다.

11. **Hermes 대시보드의 구현 방식**
    - `analyses/self-hosted-agent-webui-integration.md:57`은 `hermes-dashboard`를 스크래치 신규 구현 프로젝트로 설명합니다.
    - `projects/hermes-dashboard.md:33`은 6월 20일 해당 계획을 폐기하고 Hermes Studio 포크로 전환했다고 명시합니다.
    - 분석 문서의 사례 설명에 후속 전환이 반영되지 않았습니다.

## 링크 부족

### 깨진 참조 — 고유 대상 58개

같은 대상을 본문과 `related`에서 반복 참조하면 한 건으로 집계했습니다. 아래에는 대표 참조 페이지를 기재했습니다.

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
| `analyses/research-write-agent-separation.md` | `wiki/analyses/llm-json-parse-retry-with-dump.md` |
| `projects/upbit-trading.md` | `macos-tcc-documents-popup-diagnosis` |
| `analyses/ai-coding-agent-cost-and-context-patterns.md` | `onemancompany-heterogeneous-agents-organization` |
| `concepts/hermes-agent.md` | `personal-ai-agent-messaging-channels` |
| `patterns/csv-roundtrip-backup-restore.md` | `pgbouncer-direct-url-hybrid-routing`, `supabase-region-migration` |
| `analyses/holding-transaction-cost-basis-design.md` | `prisma-connection-pool-vercel-supabase`, `zod-schema-per-entity` |
| `projects/openclaw.md` | `prisma-decimal-nextjs-serialization` |
| `bugs/equity-curve-max-vs-latest-aggregation.md` | `round-winrate-exit-type-undercount` |
| `bugs/utc-iso-date-kst-rollover.md` | `vercel-cron-best-practices` |

다음 두 건은 기존 문서로의 연결을 검토할 수 있습니다.

- `wiki/analyses/llm-json-parse-retry-with-dump.md`: 실제 파일은 `wiki/patterns/llm-json-parse-retry-with-dump.md`입니다.
- `gieok-project`: 프로젝트 문서는 `wiki/projects/gieok.md`로 존재합니다.

부재 대상 전체를 신규 생성 후보로 판단한 것은 아닙니다. 도메인 밖 참조와 기존 문서로 대체 가능한 참조도 포함돼 있습니다.

### 직접 연결이 부족한 문서 — 12쌍

다음 쌍은 `related`와 본문을 합쳐도 서로를 직접 링크하지 않습니다.

| 문서 쌍 | 연결 근거 |
|---|---|
| `concepts/openclaw-agent-architecture.md` ↔ `projects/openclaw.md` | 같은 시스템의 개념과 운영 기록 |
| `analyses/openai-codex-cli-overview.md` ↔ `analyses/oauth-refresh-token-rotation-multi-client.md` | 인증 개요와 다중 클라이언트 회전 충돌 |
| `analyses/claude-code-source-leak-internals.md` ↔ `concepts/claude-code-skills-plugins.md` | 스킬 규격과 도구 간 이식성 |
| `bugs/gieok-session-log-url-credential-masking-false-positive.md` ↔ `analyses/research-write-agent-separation.md` | 조사 근거 손상과 로그 마스킹 오탐의 구분 |
| `analyses/stock-screening-score-design.md` ↔ `analyses/surge-chasing-exclusion-filter.md` | 급등 추격 편향과 배제 필터 |
| `analyses/kis-balance-api-fields.md` ↔ `analyses/risk-control-exemption-and-failed-attempt-accounting.md` | 현금 필드와 미체결 예약금 차감 |
| `analyses/backtest-timeframe-sensitivity.md` ↔ `analyses/polling-interval-vs-bar-interval.md` | 검증 봉과 운영 감시 주기의 정합성 |
| `analyses/partial-sell-rule-idempotency.md` ↔ `bugs/reentry-after-full-liquidation-no-cooldown.md` | 부분·전량 매도 후 상태 관리 |
| `analyses/dca-trailing-stop-tuning.md` ↔ `bugs/trailing-activation-current-profit-gate.md` | 트레일링 튜닝과 활성 후 검사 누락 |
| `bugs/stale-process-attributeerror-inprocess-coupling.md` ↔ `bugs/hermes-webui-runtime-import-offline.md` | 같은 WebUI의 프로세스·import 장애 감별 |
| `patterns/agentic-cli-text-generation-lockdown.md` ↔ `bugs/ndjson-stdout-parser-greedy-regex.md` | 출력 행동 제약과 stdout 파싱 방어 |
| `decisions/shared-broker-appkey-token-cache.md` ↔ `analyses/financial-data-freshness-and-cache.md` | 공유 API 호출 예산과 재무 캐시의 호출 감소 효과 |

### 인덱스 미등록 — 4개

다른 페이지에서 연결되지만 `index.md`에 등록되지 않은 문서입니다.

- `analyses/code-change-rag-kb-design.md`
- `analyses/code-change-rag-kb-research.md`
- `patterns/code-change-rag-kb-spec.md`
- `bugs/hermes-webui-runtime-import-offline.md`

### 위키링크 대상 모호성 — 1건

`concepts/gieok.md`와 `projects/gieok.md`가 같은 파일명을 사용합니다.

`index.md`를 포함한 9개 문서에 `[[gieok]]`이 있어, 개념과 프로젝트 중 어느 문서를 가리키는지 불명확합니다. 인덱스의 개념 문서 연결 문제도 여기에 포함하고 별도로 중복 집계하지 않았습니다.

## 프런트매터 불비

지식 문서 125개의 프런트매터를 검사했습니다. 아래 중복 키를 제외한 YAML 구문 오류, 작성된 열거형 값의 허용값 위반, 빈 `tags`는 검출되지 않았습니다. `tags`와 `updated`의 필드 자체 누락도 없었습니다.

### 필수 필드 누락 — 18건

| 누락 필드 | 페이지 |
|---|---|
| `sources` — 7건 | `analyses/dca-trailing-stop-tuning.md` |
| | `analyses/llm-news-prediction-pitfalls.md` |
| | `analyses/llm-provider-aggregator-vs-local-vs-hub.md` |
| | `analyses/macos-launchagent-catchup-behavior.md` |
| | `analyses/news-driven-market-signal-framework.md` |
| | `analyses/partial-sell-rule-idempotency.md` |
| | `analyses/scoring-system-ic-validation.md` |
| `confidence` — 8건 | `analyses/llm-news-prediction-pitfalls.md` |
| | `analyses/macos-launchagent-catchup-behavior.md` |
| | `analyses/multi-profile-cli-agent-isolation.md` |
| | `analyses/news-driven-market-signal-framework.md` |
| | `analyses/oauth-refresh-token-rotation-multi-client.md` |
| | `analyses/partial-sell-rule-idempotency.md` |
| | `concepts/gieok.md` |
| | `projects/agent-weekly.md` |
| `related` — 3건 | `analyses/macos-launchagent-catchup-behavior.md` |
| | `concepts/gieok.md` |
| | `patterns/oracle-cloud-free-tier-setup.md` |

`source_session`은 필수 `sources`를 대체하지 않습니다.

### `updated`가 최신 변경 이력보다 오래됨 — 7건

| 페이지 | `updated` | 최신 변경 이력 |
|---|---|---|
| `analyses/code-change-rag-kb-design.md` | 2026-07-12 | 2026-07-13 |
| `analyses/oauth-refresh-token-rotation-multi-client.md` | 2026-06-21 | 2026-07-05 |
| `concepts/claude-code-skills-plugins.md` | 2026-04-16 | 2026-06-08 |
| `decisions/openclaw-coder-default-model-codex.md` | 2026-05-07 | 2026-06-13 |
| `patterns/code-change-rag-kb-spec.md` | 2026-07-13 | 2026-07-14 |
| `patterns/llm-json-parse-retry-with-dump.md` | 2026-07-30 | 2026-08-03 |
| `projects/dev-blog.md` | 2026-07-29（後勝ち解釈） | 2026-10-10 |

### 同一キーの重複 — 1件

- `projects/dev-blog.md:7`：`updated: "2026-10-10"`
- 同ファイル111行：`updated: 2026-07-29`

`updated` が二重定義されています。後の値を採用するパーサーでは古い日付になり、重複を拒否するパーサーでは読み込みに失敗する可能性があります。

### メタ文書のフロントマター不在 — 2件

- `index.md`
- `log.md`

スキーマは「すべての wiki 文書」に必須フィールドを求めていますが、メタ文書の例外を定義していません。各フィールドではなく文書ごとに1件として集計しました。

`lint-report.md` はユーザー指定の `title`・`date` 形式に従う生成物のため、通常の知識文書の必須フィールド検査から除外しました。

### 出典形式とスキーマの共通不一致 — 1件

**114文書**の `sources` に、スキーマが規定する `raw-sources/…` または `local-only: …` 以外の表記があります。

該当範囲は、知識文書125件から上記の `sources` 欠落7件と、形式に適合する次の4件を除いた全文書です。

- `bugs/hermes-webui-runtime-import-offline.md`
- `patterns/hermes-single-model-delegation.md`
- `patterns/oracle-cloud-operations-cheatsheet.md`
- `summaries/claude-code-opus-orchestration-setup.md`

主な不一致は次のとおりです。

- 広く使われる `session-logs/…` がスキーマに定義されていません。
- `raw/…`、外部URL、`local-repo: …`、Wiki文書のパスが混在しています。
- `analyses/code-change-rag-kb-design.md` と `analyses/code-change-rag-kb-research.md` はセッション・調査作業の説明を出典にしています。
- `concepts/openclaw-agent-architecture.md` には、特定の原本を識別しにくい説明形式の出典があります。

出典がすべて存在しないという判定ではなく、実際の運用とスキーマの不一致です。説明形式の出典には原本の追跡性という課題もあります。

## R1: Unicode 불가시 문자 (prompt injection 감사)

제공된 셸 pre-scan 결과를 그대로 기재합니다.

- `wiki/patterns/csv-roundtrip-backup-restore.md` (lines 81,84)

재측정 및 자동 수정은 수행하지 않았습니다. 이 검출만으로 악성 지시 삽입이 확인된 것은 아닙니다.