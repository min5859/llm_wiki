---
title: Lint Report
date: 2026-06-01
---

# Wiki Lint Report (2026-06-01)

## 요약

- 검사 파일: **145** (`wiki/**/*.md`, lint-report.md 자체 제외)
- 전회 보고서 (2026-05-01) 이후 신규 추가: **50 파일**
- 이번 보고서에서 해소 확인: **1건** (`pdf-text-extraction-vs-ocr.md` 끊긴 링크 — 파일 생성으로 해소)
- 카테고리별 검출 건수

| 카테고리 | 건수 |
|---|---|
| 모순 (사실 불일치 위험) | 2 페어 |
| 고립 페이지 (inbound 링크 0) | 15 파일 |
| 전용 페이지 후보 (빈출 무전용) | 6 개념 |
| 오래된 기술 의심 | 2 파일 |
| 링크 부족 | 6 묶음 |
| 프런트매터 불비 | 25 파일 |
| R1 Unicode 불가시 문자 | 0 (검출 없음) |

---

## 모순

명확한 사실 충돌은 없지만, 아래 페어는 **부분 중복·이중 기술**로 향후 한쪽만 갱신될 경우 불일치가 발생할 위험이 있습니다.

| 페어 | 중복 영역 | 비고 |
|---|---|---|
| `wiki/patterns/claude-code-token-optimization.md` ↔ `wiki/patterns/claude-token-saving-tips.md` | "토큰 절약 7가지" vs "8가지 방법", `.claudeignore` / 세션 분리 / `/compact`·`/clear` | 같은 항목을 서로 다른 번호로 다시 열거. 비용표·효과치 갱신 시 한쪽만 수정될 위험. `related`는 설정되어 있음. |
| `wiki/concepts/gieok.md` ↔ `wiki/projects/gieok.md` | 4계층 아키텍처 표, Hook 이벤트, LaunchAgent 일정 | 동일 시스템에 대한 "개념 vs 프로젝트 상세". 4계층 표가 양쪽에 산재하여 갱신 충돌 위험. |

---

## 고립 페이지

동료(peer) 위키 페이지 어디에서도 참조되지 않는 파일 목록입니다. (`index.md` / `log.md`만의 inbound는 의미가 약하므로 고립으로 분류)

| 파일 | 비고 |
|---|---|
| `wiki/analyses/algorithmic-collusion-reinforcement-learning.md` | 담합·RL — 완전 고립 |
| `wiki/analyses/cointeract-hoi-video-synthesis.md` | HOI 비디오 합성 — 완전 고립 |
| `wiki/analyses/everything-claude-code.md` | Claude Code OSS 전체 분석 — `concepts/claude-code-skills-plugins.md` 등에서 링크 권장 |
| `wiki/analyses/invitrovision-embryo-vlm.md` | outbound related는 있으나 역방향 inbound 없음 |
| `wiki/analyses/llada2-uni-unified-multimodal-diffusion.md` | 멀티모달 확산 — 완전 고립 |
| `wiki/analyses/llatisa-time-series-reasoning.md` | TSR VLM — 완전 고립 |
| `wiki/analyses/microsoft-vibevoice-voice-ai.md` | `oss-radar`에 related만 있고 본문 inbound 없음 |
| `wiki/analyses/near-future-policy-optimization-npo.md` | RLVR/NPO — 완전 고립 |
| `wiki/analyses/onemancompany-heterogeneous-agents-organization.md` | 3개 outbound 있으나 inbound 0 |
| `wiki/analyses/opengame-agentic-game-development.md` | `agentspex` outbound만, inbound 0 |
| `wiki/analyses/soc-otf-sensor-to-ap.md` | work·internal 분류, 다른 wiki와 단절 |
| `wiki/analyses/tstars-tryon-virtual-try-on.md` | `meanflow-text-to-image` outbound만 |
| `wiki/concepts/openclaw-agent-architecture.md` | 7개 related outbound, inbound 0 — `projects/openclaw.md`에서 링크 권장 |
| `wiki/projects/ai-shorts-production-with-claude-code.md` | 2개 related outbound만, peer inbound 0 |
| `wiki/projects/ht-trading.md` | 완전 단절 — `openclaw-agent-architecture.md` tags에 `ht_trading` 있으나 본문 링크 없음 |

---

## 전용 페이지 후보

3개 이상 파일에서 반복 언급되지만 전용 개요 페이지가 없는 개념입니다.

| 개념 | 등장 규모 | 현재 상태 |
|---|---|---|
| **MCP (Model Context Protocol)** | 13 파일 / 48 회 | 전용 페이지 없음. `claude-code-advanced.md` 일부 섹션에 산재 |
| **Hooks (Claude Code Hook)** | 12 파일 / 32 회 | 전용 페이지 없음. `claude-code-advanced.md` / gieok 페이지에 산재 |
| **Prisma (개요·공통 함정)** | 10 파일 / 93 회 | 사고·버그 개별 페이지만 3건, 통합 "Prisma 개요" 없음 |
| **Plan 모드 (`/plan`)** | 5 파일 / 14 회 | `claude-code-basic-usage.md` 한 섹션으로만 존재 |
| **Sub-agents** | 4 파일 / 10 회 | `claude-code-advanced.md` 한 섹션으로만 존재 |
| **Context Rot** | 4 파일 / 6 회 | `claude-code-token-optimization.md` 한 섹션. 개념 자체 분리 여지 있음 |

---

## 오래된 기술 의심

명확한 덮어쓰기는 없으나, 이하 파일은 **갱신 시점이 오래됐고 이후 출현한 파일에 최신 정보가 있어** 정합성 점검이 필요합니다.

| 파일 | updated | 의심 사유 |
|---|---|---|
| `wiki/concepts/claude-code-overview.md` | 2026-04-16 | Max 플랜 가격·버전 위치 단정 기술. 2026-04-30 작성된 `analyses/everything-claude-code.md` 에 플러그인 마켓플레이스·스킬 시스템 등 신규 사실 기술. 모순은 아니나 overview 쪽 신선도 낮음. |
| `wiki/concepts/claude-code-basic-usage.md` | 2026-04-16 | `/plan`·`/compact`·`/clear` 위주 설명. 이후 추가된 `/loop`, `/schedule` 등은 cross-link만 존재하고 본문에 미반영. |

---

## 링크 부족

서로 명확히 관련된 파일인데 상호 링크가 없거나 단방향인 경우입니다.

1. **Prisma 클러스터**: `analyses/prisma-decimal-nextjs-serialization.md` ↔ `analyses/prisma-generate-missing-error.md` ↔ `bugs/node-modules-symlink-copy-prisma.md` ↔ `projects/japa-asset-dashboard.md` — 4개 파일을 묶는 일관된 cross-link 필요. 현재 부분적.

2. **세계 모델 클러스터**: `analyses/agentic-world-modeling-taxonomy.md` ↔ `analyses/agent-world-environment-synthesis.md` — 같은 사조의 두 서베이인데 직접 link 없음. `onemancompany-heterogeneous-agents-organization.md`만이 양쪽 outbound를 가짐.

3. **OpenClaw 클러스터**: `concepts/openclaw-agent-architecture.md` ← (역방향 없음) ← `projects/openclaw.md`. 프로젝트 페이지에서 아키텍처 개념 페이지 명시 링크 권장.

4. **gieok 클러스터**: `concepts/gieok.md`의 `[[gieok-project]]` 및 `analyses/macos-launchagent-catchup-behavior.md`의 동일 wikilink가 끊긴 상태. 실제 파일은 `projects/gieok.md`. wikilink 명세 통일 필요(`[[projects/gieok]]`).

5. **AI 쇼츠 ↔ loop**: `projects/ai-shorts-production-with-claude-code.md` → `patterns/claude-code-loop-automation.md` (단방향). 루프 패턴 페이지에서 쇼츠를 사용 사례로 역방향 링크 권장.

6. **openclaw-acp ↔ ht-trading**: `analyses/openclaw-acp-runtime-internals.md` ↔ `projects/ht-trading.md` — tags에서만 연결 시사, 본문 cross-link 없음.

### 끊긴 wikilink (미해소)

| 출처 | 끊긴 대상 | 비고 |
|---|---|---|
| `wiki/concepts/gieok.md` (L76) | `[[gieok-project]]` | 실제 파일: `wiki/projects/gieok.md` |
| `wiki/analyses/macos-launchagent-catchup-behavior.md` (L43) | `[[gieok-project]]` | 동일 원인 |

> **해소 확인 (이번 보고서)**: `wiki/projects/finance-analysis-nextjs.md` / `wiki/analyses/ai-valuation-trustworthiness.md` → `pdf-text-extraction-vs-ocr.md` 끊긴 링크는 해당 파일 생성으로 해소됨.

### 동일 basename 충돌 (미해소)

`gieok.md`가 `wiki/concepts/`와 `wiki/projects/` 양쪽에 존재. Obsidian wikilink `[[gieok]]` 해석 불명. 링크 시 `[[projects/gieok]]` 등 명시화 필요.

---

## 프런트매터 불비

`schema.md` 필수 필드: `title`, `domain`, `sensitivity`, `tags`, `created`, `updated`, `sources`, `confidence` (+ optional `related`)

### domain / sensitivity 누락 (4 파일)

| 파일 | 누락 필드 |
|---|---|
| `wiki/analyses/macos-launchagent-catchup-behavior.md` | domain, sensitivity |
| `wiki/analyses/su-01-olympiad-reasoning.md` | domain, sensitivity |
| `wiki/concepts/gieok.md` | domain, sensitivity |
| `wiki/projects/gieok.md` | domain, sensitivity |

### confidence 누락 (15 파일)

`wiki/analyses/agentic-world-modeling-taxonomy.md`, `wiki/analyses/algorithmic-collusion-reinforcement-learning.md`, `wiki/analyses/github-pages-base-path-pattern.md`, `wiki/analyses/llada2-uni-unified-multimodal-diffusion.md`, `wiki/analyses/llm-news-prediction-pitfalls.md`, `wiki/analyses/macos-launchagent-catchup-behavior.md`, `wiki/analyses/multi-profile-cli-agent-isolation.md`, `wiki/analyses/near-future-policy-optimization-npo.md`, `wiki/analyses/news-driven-market-signal-framework.md`, `wiki/analyses/partial-sell-rule-idempotency.md`, `wiki/analyses/python-pptx-design-token-pipeline.md`, `wiki/analyses/su-01-olympiad-reasoning.md`, `wiki/analyses/supabase-magic-link-single-user-allowlist.md`, `wiki/concepts/gieok.md`, `wiki/projects/gieok.md`

### sources 누락 (16 파일)

`wiki/analyses/anthropic-oauth-third-party-billing-trap.md`, `wiki/analyses/dca-trailing-stop-tuning.md`, `wiki/analyses/github-pages-base-path-pattern.md`, `wiki/analyses/llm-news-prediction-pitfalls.md`, `wiki/analyses/llm-provider-aggregator-vs-local-vs-hub.md`, `wiki/analyses/macos-launchagent-catchup-behavior.md`, `wiki/analyses/multi-profile-cli-agent-isolation.md`, `wiki/analyses/news-driven-market-signal-framework.md`, `wiki/analyses/partial-sell-rule-idempotency.md`, `wiki/analyses/personal-ai-agent-messaging-channels.md`, `wiki/analyses/polling-interval-vs-bar-interval.md`, `wiki/analyses/scoring-system-ic-validation.md`, `wiki/analyses/su-01-olympiad-reasoning.md`, `wiki/analyses/supabase-magic-link-single-user-allowlist.md`, `wiki/analyses/vercel-friendly-database-options.md`, `wiki/analyses/web-app-storage-without-db.md`

### sources 형식 오류 (2 파일)

| 파일 | 오류 |
|---|---|
| `wiki/concepts/gieok.md` | `sources: 1` — 정수 스칼라. 배열이어야 함 |
| `wiki/projects/gieok.md` | 동일 |

### 비표준 키 사용 (5 파일)

| 파일 | 비표준 키 | 비고 |
|---|---|---|
| `wiki/analyses/agentic-world-modeling-taxonomy.md` | `arxiv` | schema 정의에 없는 키 |
| `wiki/analyses/algorithmic-collusion-reinforcement-learning.md` | `arxiv` | 동일 |
| `wiki/analyses/llada2-uni-unified-multimodal-diffusion.md` | `arxiv` | 동일 |
| `wiki/analyses/near-future-policy-optimization-npo.md` | `arxiv` | 동일 |
| `wiki/analyses/su-01-olympiad-reasoning.md` | `source_session`, `arxiv_id` | 동일 |

### updated 형식 오류 (2 파일)

| 파일 | 실제 값 | 오류 |
|---|---|---|
| `wiki/analyses/soc-otf-sensor-to-ap.md` | `"2026-04-21 (SW control sequence 다이어그램 추가)"` | 날짜 뒤 텍스트 혼입. schema 요구형식 `YYYY-MM-DD` 위반 |
| `wiki/projects/oss-radar.md` | `"2026-05-23T08:00:00+09:00"` | ISO 8601 타임스탬프. schema 요구형식 `YYYY-MM-DD` 위반 |

> **(정보성)** `wiki/index.md`, `wiki/log.md`: 스키마 면제 여부 미확정. 현재 `title`·`updated`만 존재.

---

## R1: Unicode 불가시 문자 (prompt injection 감사)

검출 없음.

---

### R1 pre-scan findings (셸이 측정한 결과, LLM 측 재측정 불필요)

해당 없음 — `wiki/` 내의 어떤 .md 에서도 ZWSP / RTLO / SHY / BOM 등은 검출되지 않았습니다.
