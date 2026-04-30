---
title: Lint Report
date: 2026-05-01
---

# Wiki Lint Report (2026-05-01)

## 요약

- 총 검사 파일: **53** (`wiki/**/*.md`)
- 카테고리별 검출 건수
  - 모순 (사실 불일치): 0
  - 고립 페이지 (true orphan): 0 (`index.md`, `log.md` 자체만 inbound 0)
  - 약(弱)고립 (`index.md`/`log.md`만이 inbound): **15**
  - 끊긴 링크 (참조 대상이 존재하지 않음): **6** (false-positive 제외)
  - 동일 basename 충돌: **1쌍** (`gieok.md`)
  - 전용 페이지 후보 (빈출 개념): **6**
  - 오래된 기술 의심: 0 (명확한 모순 없음, 갱신 여지 있는 페이지 2 건만 메모)
  - 상호 링크 부족 (개선 권고): **8 묶음**
  - 프런트매터 불비: **9 페이지** (+ index/log 2 건은 정보성)
  - R1 Unicode 불가시 문자: **0** (검출 없음)

---

## 모순

명확한 사실 충돌은 검출되지 않았습니다. 단, 다음 페어는 **부분 중복**이 있어 향후 충돌 가능성이 있어 모니터링 권고:

| 페어 | 중복 영역 | 비고 |
|---|---|---|
| `wiki/patterns/claude-code-token-optimization.md` ↔ `wiki/patterns/claude-token-saving-tips.md` | "토큰 절약 7가지" vs "8가지 방법", `.claudeignore`/세션 분리/`/compact`·`/clear` | 양쪽 모두 `related`로 서로를 가리키나, 같은 항목을 다른 번호로 다시 열거. 향후 사실(예: `/compact` 효과치, 비용표) 갱신 시 한쪽만 수정될 위험. |
| `wiki/analyses/everything-claude-code.md` ↔ `wiki/concepts/claude-code-overview.md` | "Claude Code의 위치/요금/플러그인" 일부 | 각각 OSS 1건 분석 vs 일반 개요로 스코프는 다름. 모순은 아님. |
| `wiki/concepts/gieok.md` ↔ `wiki/projects/gieok.md` | 아키텍처 / 4계층 / Hook 이벤트 / LaunchAgent 일정 | 동일 시스템에 대한 "개념 vs 프로젝트 상세". 4계층 표는 두 곳에 산재. 한쪽이 갱신되어도 다른 쪽이 남을 수 있음. |

---

## 고립 페이지

`index.md`/`log.md`만이 가리키고 있고, **동료(peer) 페이지 어디에서도 참조되지 않는** 페이지 = "약(弱)고립". 색인은 자동으로 모든 페이지를 나열하므로 색인만의 inbound는 의미가 약합니다.

| 페이지 | 분류 |
|---|---|
| `wiki/analyses/algorithmic-collusion-reinforcement-learning.md` | 완전 고립 토픽 (담합·RL) |
| `wiki/analyses/cointeract-hoi-video-synthesis.md` | 완전 고립 토픽 (HOI 비디오) |
| `wiki/analyses/everything-claude-code.md` | Claude Code OSS — `concepts/claude-code-skills-plugins.md` 등에서 링크 권장 |
| `wiki/analyses/invitrovision-embryo-vlm.md` | `onevl-latent-reasoning`을 related로 가리키지만 역방향 없음 (단방향) |
| `wiki/analyses/llada2-uni-unified-multimodal-diffusion.md` | 완전 고립 토픽 (멀티모달 확산) |
| `wiki/analyses/llatisa-time-series-reasoning.md` | 완전 고립 토픽 (TSR VLM) |
| `wiki/analyses/microsoft-vibevoice-voice-ai.md` | `oss-radar`만 related — 본문 측 링크 없음 |
| `wiki/analyses/near-future-policy-optimization-npo.md` | 완전 고립 토픽 (RLVR) |
| `wiki/analyses/onemancompany-heterogeneous-agents-organization.md` | 본인이 3개 related를 outbound로 가리키나 inbound 0 |
| `wiki/analyses/opengame-agentic-game-development.md` | `agentspex` 가리키나 역방향 inbound 0 |
| `wiki/analyses/soc-otf-sensor-to-ap.md` | work·internal 분류, 다른 wiki 페이지와 단절됨 |
| `wiki/analyses/tstars-tryon-virtual-try-on.md` | `meanflow-text-to-image` outbound만 |
| `wiki/concepts/openclaw-agent-architecture.md` | 7개 related를 outbound로 가지나 inbound는 index.md만 — `projects/openclaw.md`에서 링크 권장 |
| `wiki/projects/ai-shorts-production-with-claude-code.md` | 2개 related outbound만, peer inbound 0 |
| `wiki/projects/ht-trading.md` | 완전히 단절. `openclaw-agent-architecture.md`의 tags에 `ht_trading` 있음 — 본문에서 링크 권장 |

> **진성(true) 고립 (inbound 0):** `wiki/index.md`, `wiki/log.md` (의도된 루트, 정상)

---

## 전용 페이지 후보

빈출 개념인데 전용 페이지가 없거나, 다른 페이지의 한 섹션으로만 존재합니다:

| 개념 | 등장 빈도 | 현재 상태 |
|---|---|---|
| **MCP (Model Context Protocol)** | 13 파일 / 48 회 | 전용 페이지 없음. `claude-code-advanced.md` 일부 섹션 |
| **Hooks (Claude Code Hook)** | 12 파일 / 32 회 | 전용 페이지 없음. `claude-code-advanced.md`/gieok 페이지에 산재 |
| **Plan 모드 (`/plan`)** | 5 파일 / 14 회 | `claude-code-basic-usage.md`의 한 섹션 |
| **Sub-agents / Sub-agent** | 4 파일 / 10 회 | `claude-code-advanced.md`의 한 섹션 |
| **Prisma (개념·운영 패턴)** | 10 파일 / 93 회 | 사고/버그 페이지 3건은 있으나 "Prisma 개요/공통 함정" 페이지 없음 |
| **Context Rot** | 4 파일 / 6 회 | `claude-code-token-optimization.md`의 한 섹션. 컨셉 자체는 분리할 만함 |

(빈도가 매우 높지만 자연어 일반어인 "agent", "claude-code"는 제외)

---

## 오래된 기술 의심

명확하게 새 정보로 덮어쓰기된 주장은 검출하지 못했습니다. 단, **갱신 시점이 분리**된 다음 페어는 향후 사실 정합성 점검 권고:

- `wiki/concepts/claude-code-overview.md` (`updated: 2026-04-16`) — Max 플랜 가격, "4세대" 위치 등을 단정. 2026-04-30 작성된 `analyses/everything-claude-code.md`(v2.0.0-rc.1, ECC 2.0 alpha 등)와 비교했을 때 overview 측이 신규 사실(플러그인 마켓플레이스, 스킬 시스템 등)을 누락. *모순은 아니지만 신선도 낮음.*
- `wiki/concepts/claude-code-basic-usage.md` (`updated: 2026-04-16`) — `/plan`·`/context`·`/compact`·`/clear` 위주 설명. 그 후 추가된 `claude-code-skills-plugins.md`(2026-04-30 추정), `claude-code-loop-automation.md` 등의 신규 명령(`/loop` 등)은 cross-link만 있고 본문에 반영 없음.

---

## 링크 부족

상호 링크 권고 (현재 단방향이거나 누락):

1. **Prisma 클러스터**: `analyses/prisma-decimal-nextjs-serialization.md` ↔ `analyses/prisma-generate-missing-error.md` ↔ `bugs/node-modules-symlink-copy-prisma.md` ↔ `projects/japa-asset-dashboard.md` — 4개 모두를 묶는 일관된 cross-link 필요. 현재는 부분적.
2. **세계 모델 클러스터**: `analyses/agentic-world-modeling-taxonomy.md` ↔ `analyses/agent-world-environment-synthesis.md` — 같은 사조의 두 서베이지만 직접 link 없음. `onemancompany-heterogeneous-agents-organization.md`만이 두 페이지 모두를 outbound로 가짐.
3. **OpenClaw 클러스터**: `concepts/openclaw-agent-architecture.md` ← (역방향 부재) ← `projects/openclaw.md`. 프로젝트 페이지에서 아키텍처 개념 페이지를 명시적으로 link 권장.
4. **gieok 클러스터**: `wiki/concepts/gieok.md` 의 `[[gieok-project]]`는 끊김 (아래 끊긴 링크 참조). `[[gieok|projects/gieok]]` 등으로 명시화 필요.
5. **AI 쇼츠 ↔ loop**: `projects/ai-shorts-production-with-claude-code.md` 는 `claude-code-loop-automation.md` 를 가리키지만 역방향(루프 사용 사례로 쇼츠 인용)은 없음.
6. **`meanflow` ↔ `near-future-policy-optimization-npo`**: 둘 다 RL/RLVR/policy 계열인데 cross-link 없음.
7. **`microsoft-vibevoice-voice-ai` ↔ `tstars-tryon-virtual-try-on`**: 둘 다 OSS·상업화 멀티모달 모델이지만 분리.
8. **`openclaw-agent-architecture.md` ↔ `projects/ht-trading.md` ↔ `projects/openclaw.md`**: tag(`ht_trading`, `upbit_trading`)에서만 연결 시사. 본문 cross-link 추가 권장.

---

## 프런트매터 불비

`schema.md` 필수 필드: `title, domain, sensitivity, tags, created, updated, sources, confidence` (+ optional `related`).

| 페이지 | 누락 / 비정상 |
|---|---|
| `wiki/analyses/agentic-world-modeling-taxonomy.md` | `tags`, `created`, `confidence` 없음. 비스키마 키 `arxiv` 사용. `sources`가 인라인 문자열(리스트 아님). |
| `wiki/analyses/algorithmic-collusion-reinforcement-learning.md` | 동일 (tags, created, confidence 없음 / arxiv 키 / sources 인라인) |
| `wiki/analyses/llada2-uni-unified-multimodal-diffusion.md` | 동일 |
| `wiki/analyses/near-future-policy-optimization-npo.md` | 동일 |
| `wiki/analyses/macos-launchagent-catchup-behavior.md` | `domain`, `sensitivity`, `sources`, `confidence` 없음. 비스키마 키 `source_session` 사용. |
| `wiki/analyses/soc-otf-sensor-to-ap.md` | `sources: []` 빈 배열 (work·internal 페이지인데 출처 미기재). `updated: "2026-04-21 (SW control sequence 다이어그램 추가)"`처럼 날짜 외 텍스트 혼입. |
| `wiki/concepts/gieok.md` | `domain`, `sensitivity`, `confidence` 없음. `sources: 1` (스칼라, 리스트여야 함). |
| `wiki/projects/gieok.md` | 동일 (`domain`, `sensitivity`, `confidence` 없음 / `sources: 1` 스칼라) |
| **(정보성)** `wiki/index.md` | `title`, `updated`만 있음. 인덱스 파일은 스키마 면제 여부 확정 필요 |
| **(정보성)** `wiki/log.md` | `title`, `updated`만 있음. 로그 파일은 스키마 면제 여부 확정 필요 |

### 끊긴 참조 (related / wikilink 대상이 존재하지 않음)

| 출처 | 끊긴 대상 | 비고 |
|---|---|---|
| `wiki/analyses/ai-valuation-trustworthiness.md` | `wiki/analyses/pdf-text-extraction-vs-ocr.md` | 미존재 페이지 (related + 본문 wikilink 양쪽) |
| `wiki/projects/finance-analysis-nextjs.md` | `wiki/analyses/pdf-text-extraction-vs-ocr.md` | 미존재 페이지 (related + 본문 wikilink 2회) |
| `wiki/concepts/gieok.md` | `[[gieok-project]]` | 실제 파일은 `wiki/projects/gieok.md`. wikilink 명세 불일치 |
| `wiki/analyses/macos-launchagent-catchup-behavior.md` | `[[gieok-project]]` | 동일 원인 |

> false-positive (코드/설명 텍스트 안의 `[[...]]`): `wiki/concepts/gieok.md`의 `[[wikilink]]`(예시 텍스트), `wiki/patterns/launchd-secret-management.md`의 `[[-o interactive]]`(쉘 옵션 인용) — 실제 링크 의도 아님. 자동 수정 대상에서 제외.

### 동일 basename 충돌

- `gieok.md` 가 `wiki/concepts/` 와 `wiki/projects/` 양쪽에 존재. Obsidian wikilink `[[gieok]]` 는 어느 쪽으로 해석되는지 불명. 링크 시 `[[gieok|concepts]]` / `[[projects/gieok]]` 같은 명시화 필요.

---

## R1: Unicode 불가시 문자 (prompt injection 감사)

검출 없음.

---

### R1 pre-scan findings (셸이 측정한 결과, LLM 측 재측정 불필요)

해당 없음 — `wiki/` 내의 어떤 .md 에서도 ZWSP / ZWNJ / ZWJ / RTLO / SHY / BOM / LRM / RLM / WJ 등은 검출되지 않았습니다. (NBSP 포함, 일반적인 인쇄·복붙 유래 불가시 문자 0건)
