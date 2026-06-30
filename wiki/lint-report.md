---
title: Lint Report
date: 2026-07-01
---

# Wiki Lint Report (2026-07-01)

## 요약

- **검출한 문제의 총수: 약 110건** (자동 측정 + 5개 주제 클러스터 내용 감사 종합)
- 카테고리별 내역:

| 카테고리 | 건수 |
|---|---|
| 모순 | 11 |
| 고립 페이지 | 10 |
| 전용 페이지 후보 | 16 |
| 오래된 기술 의심 | 12 |
| 링크 부족 (끊긴 링크 + 상호 링크 누락) | 약 45 (끊긴 링크 18문서 + 위키링크 `.md` 15곳 + 누락 상호링크 ~25쌍) |
| 프런트매터 불비 | 34 (필드 누락 26 + `domain:research` 비허용 6 + 중복 키 1 + `related:[]` 10은 별도) |
| R1 불가시 문자 | 0 |

> 본 감사는 `wiki/` 내 192개 `.md` 문서 전수를 대상으로 했습니다(`.bkit/` 런타임 파일 제외). 내용 기반 항목(모순·전용 페이지·오래된 주장·상호 링크)은 5개 주제 클러스터로 분할해 정밀 감사했습니다. **수정은 일절 수행하지 않았습니다.**

---

## 모순

### M-FIN-1 · ht-trading.md 내부 `max_positions` 3중 표기 (5 / 10 / 11)
- `wiki/projects/ht-trading.md` line 478: `max_positions | 10종목 ... 직전 5종목에서 상향`
- 같은 파일 line 484 (매수 검증 순서 조건 1): `len(positions) >= 5` — 검증 로직은 여전히 5로 하드코딩 서술
- 같은 파일 line 753, 767: `max_positions: 11` (InfiniteBuying 활성화 후)
- → 매수 검증 규칙 서술(5)이 설정값(10→11)과 모순. 변경 시 검증 블록이 갱신되지 않음.

### M-FIN-2 · ht-trading.md `min_score` 60 vs 62
- line 62, 72: `buy_min_score_full: 62` ("이전 60에서 상향")
- line 81-85 (기타 screener 파라미터 YAML): `min_score: 60`
- → 같은 파일이 컷오프를 62와 60으로 동시 서술. 페이지 자신의 "두 파일 항상 62 동기화" 규칙(line 68-76)에 위배.

### M-FIN-3 · n_stock_info 가중치 `40+40+20` vs 실효 `50/30/20`
- `n-stock-info.md`(명목 40/40/20과 실효 50/30/20 혼재), `ht-dde.md`(baseline 50/30/20), `ht-trading.md` V3 표가 동일 스코어러를 서로 다른 프레이밍으로 기술 → 오독 위험.

### M-ML-1 · Flow Matching ↔ MeanFlow 관계 프레이밍 불일치
- `meanflow-text-to-image.md` L26: MeanFlow와 Flow Matching을 병렬적 별개 프레임워크(순간속도 vs 평균속도)로 기술
- `anyflow-video-diffusion-on-policy-distillation.md` L42-44: Flow Matching을 flow map의 `t=r` 특수 케이스로 기술(포섭 관계)
- → 두 페이지가 관계를 다르게 서술하나 상호 정정·참조 없음.

### M-ML-2 · "스텝 증가 시 무포화" 주장 범위 과대
- `meanflow-text-to-image.md` L96은 무포화 특성을 MeanFlow 일반에 귀속, `anyflow` L29는 flow-map backward-simulation 특유 트릭으로 귀속 → MeanFlow 페이지 주장이 출처 근거보다 광범위.

### M-CC-1 · OpenClaw `main` 에이전트 모델: gpt-5.4 vs gpt-5.5
- `projects/openclaw.md:31,95`: main = `gpt-5.4`, coder = `gpt-5.5`
- `bugs/openclaw-coder-silent-3-layer.md:65`: main = `openai-codex/gpt-5.5`
- → 버그 페이지와 프로젝트 페이지가 main 모델을 다르게 기술.

### M-CC-2 · `/loop` 하드 리밋 표기 불균형
- `concepts/claude-code-basic-usage.md:114-116`, `concepts/claude-code-skills-plugins.md:37-41`: "최대 7일 / 50태스크"를 사실로 명시
- `patterns/claude-code-loop-automation.md:86`: 해당 수치 없이 "세션 종료 시 종료"만 기술 → 동일 기능에 대한 권위 불균형.

### M-CC-3 · Hermes Kanban API "없음" vs "있음" 공존
- `concepts/hermes-agent.md`: 2026-06-18 "Kanban API 없음" + 2026-06-20 "네이티브 Kanban 존재" 양립
- `projects/hermes-dashboard.md:87`(v2 보존 섹션): "Kanban API 없음 → 로컬 store 전용 확정"이 평문으로 잔존, 같은 파일 :42,:50의 06-20 정정과 충돌 → 스캔 시 구 주장이 현행처럼 읽힘.

### M-CC-4 · gieok 개념 vs 프로젝트: 스케줄링 모델 상이
- `concepts/gieok.md:33-38,71`: 세션 트리거(`SessionStart` 주입) 기반, 명시적 cron 없음
- `projects/gieok.md:45-46`: "하루 3회(07:00/13:00/19:00) LaunchAgent" 고정 스케줄
- → 동일 시스템의 ingest 트리거 모델 2종이 정합되지 않음. Hook 목록도 불일치(개념 페이지의 `SessionStart`가 프로젝트 4-hook 표에 없음).

### M-WEB-1 · Supabase `:5432`(DIRECT_URL) 정의 충돌
- `nextjs-vercel-supabase-deployment.md` §5: `:5432`를 "Session pooler, pgbouncer 경유"로 정의
- `pgbouncer-direct-url-hybrid-routing.md`: 같은 `:5432`를 "PgBouncer 우회 직결"로 정의, 성능 주장(23.5s→11.6s)이 우회 전제에 의존
- → 동일 연결 문자열이 "pgbouncer 경유"이면서 "우회"일 수 없음. 핵심 결함.

### M-WEB-2 · Supabase free tier 연결 한도 단일 출처
- `pgbouncer-direct-url-hybrid-routing.md`: "free tier 60개"
- `vercel-friendly-database-options.md`: "0.5GB/50K MAU"만, 연결 수 없음 → 60 수치가 한 페이지에만 존재, 교차 미검증.

### M-PRJ-1 · isp-patch-rag AI 실행 경로 프레이밍 불일치
- `isp-patch-knowledge-rag-system.md`(원본): "외부 LLM API(Claude/OpenAI 등) 전송 불가 — 사내 LLM 전용"
- `00-proposal.md`/`common-architecture.md`/`component-2-analysis-webapp.md`(리팩터): 분석 컴포넌트를 `claude -p`(사내 승인 엔드포인트 경유)로 표준화
- → 동일 시스템의 AI 실행 경로를 외부 금지 vs `claude -p` 통일로 다르게 기술, 미정합.

> 참고(경미·자체 정정됨): dev-blog 토픽 수 10/11/12 혼재, upbit-trading 추세필터 진단⑤ 인플레이스 정정, reentry cooldown 60→1080 진화 등은 페이지 내에서 화해되어 모순으로 카운트하지 않음.

---

## 고립 페이지

다른 어느 페이지·index.md에서도 링크되지 않는 문서 10개 (모두 index.md에도 누락):

1. `wiki/analyses/anyflow-video-diffusion-on-policy-distillation.md`
2. `wiki/analyses/awesome-copilot-vscode-customizations.md`
3. `wiki/analyses/daytona-ai-code-runtime.md`
4. `wiki/analyses/openbb-financial-data-platform.md`
5. `wiki/analyses/photoprism-ai-photos-app.md`
6. `wiki/analyses/qwen-image-2-unified-generation-editing.md`
7. `wiki/analyses/shellcheck-shell-script-linter.md`
8. `wiki/analyses/upscayl-ai-image-upscaler.md`
9. `wiki/patterns/oracle-cloud-free-tier-setup.md`
10. `wiki/projects/isp-patch-rag/proposal-slides.md`

> 추가: `oss-radar`가 산출한 OSS 분석 6종(ragflow 제외 photoprism/upscayl/shellcheck 등)은 `related: []`로 비어 있어 사실상 준-고립 상태. isp-patch-rag 클러스터 전체(8파일)가 index.md 프로젝트 섹션에서 누락(아래 링크 부족 참조).

---

## 전용 페이지 후보

빈출(3개 이상 파일)이나 전용 개념 페이지가 없는 항목:

**AI/Claude Code 도메인**
1. **MCP(Model Context Protocol)** — 정의가 ai-agent-basics / claude-code-advanced / claude-code-setup / claude-code-overview 등에 중복. 등록 메커니즘 페이지(`claude-mcp-server-scope-and-add-json`)는 있으나 "MCP란" 개념 페이지 부재.
2. **Sub-agents / 병렬 에이전트** — 5개 파일에서 재설명(컨텍스트 격리·병렬 spawn·FleetView).
3. **tmux** — 3개 패턴 페이지에 분산, 전용 페이지 없음.
4. **Slash commands** — basic-usage / skills-plugins / tui-navigation에 산재, 레퍼런스 목록 부재.
5. **`claude -p` print/headless 모드** — gieok / hermes / multi-llm-adapter / cursor / kakao-db / oss-radar / dev-blog에서 매번 재기술(`--output-format json`, `env -u CLAUDECODE`).
6. **하네스 4계층 강제력(플로우>Hook>스크립트>CLAUDE.md)** — ai-agent-basics / claude-code-setup / claude-md-guide에 verbatim 3중 복제.

**ML/논문 도메인**
7. **Flow Matching / MeanFlow / Flow Map** — 5개 파일에서 3가지로 인라인 정의(M-ML-1 참조).
8. **GRPO(Group Relative Policy Optimization)** — npo / agent-world-synthesis / mint-lora / su-01에서 매번 재설명.
9. **RLVR(검증가능 보상 RL)** — npo / su-01 / agent-world-synthesis.
10. **VLM(Vision-Language Model)** — onevl / invitrovision / llatisa / llada2.
11. **MMDiT** — qwen-image-2 / tstars / cointeract.
12. **LoRA** — mint-lora / invitrovision에서 각각 재설명, primer 부재.

**인프라/도메인**
13. **Supabase pooler 모드(Transaction 6543 / Session 5432 / pgbouncer)** — 4+ 페이지에서 공식 재유도(최우선 후보, M-WEB-1과 직결).
14. **비대화 셸의 PATH/env 미로드** — launchd-secret / cron-nvm-node-path-trap / launchd-plist-symlink / launchd-daemon-vs-cron 4파일.
15. **RAG(검색증강생성)** — ragflow + isp-patch-rag 8파일 + qualcomm-camera-kernel-isp. hybrid-search(prefilter→vector→rerank) 재기술, `concepts/rag.md` 부재.
16. **ISP/카메라 커널 도메인 프리미티브(CSID/IFE/VFE 등)** — qualcomm-camera-kernel-isp / soc-otf / isp-patch-rag 클러스터 공통 전제, 개념 페이지 없음.

---

## 오래된 기술 의심

새 정보로 덮어쓰였을 가능성이 있으나 구 주장이 명시 정정되지 않은 항목:

1. **n_stock_info IC 결론 오염** — `naver-finance-no-info-selector-drift.md`(L48-50)가 `0/80/20 IC +0.327` 백테스트는 **깨진** 거래량/캔들 데이터(pre-`e13765f`)로 계산됐다고 밝힘. 그러나 `scoring-version-comparison-methodology.md`·`scoring-system-ic-validation.md`는 이 caveat 없이 IC 표를 유효한 것처럼 제시.
2. **ht-trading `min_score:60` 예시 블록** — 자체 62 컷오프에 의해 폐기됨(M-FIN-2).
3. **ht-trading 매수검증 `len>=5`** — 5→10→11 상향에 의해 폐기(M-FIN-1).
4. **openclaw-agent-architecture.md (updated 2026-04-17)** — 2026-05-07 coder→gpt-5.5 모델 변경·OAuth 403 사건 이전 상태. 다이어그램이 Anthropic을 live main 경로로 여전히 암시. `decisions/openclaw-coder-default-model-codex.md`에 의해 갱신됨에도 미수정.
5. **claude-code-overview.md (updated 2026-04-16)** — 가장 오래된 CC 개념 페이지. 요금/설치/"4세대" 목록이 pre-v2.1로 동결, 후속 분석들은 v2.1.x 동작 전제.
6. **hermes-dashboard v2 "Kanban API 없음"** — 같은 파일 06-20 정정으로 폐기됐으나 취소선 없이 잔존(M-CC-3).
7. **gemini-2-0-flash-free-tier-blocked.md** — `gemini-2.5-flash` "신규 default 권장" + RPM/RPD 표가 2026-07 시점 노후 가능. "수치 변동" 주석은 있으나 권장 default가 현행처럼 제시.
8. **vercel-friendly-database-options.md 무료 티어 표** — "2026-05 기준" 명시, 현재 ~2개월 경과, confidence medium.
9. **anthropic-oauth-third-party-billing-trap.md** — 2026-05 "Sonnet 4.6/Opus 4.7 429, Haiku만 안정"이 2026-06 "Pro/Max 전면 차단"에 의해 부분 폐기, 05 표에 폐기 표시 없음.
10. **dev-blog 기본 어댑터 cursor→claude 전환(05-16)** — 같은 파일 05-25 로그가 `rewriteAdapter:"cursor"` 여전히 운영 중 기록(L340 vs L353), 미정합.
11. **research-write-agent-separation.md "Newsletter Write silent fail"** — 06-10~06-28 동일 `assistant_turns:0` 결함이 다수 변경이력에 반복 기록되나 미해결. bugs 페이지로 승격 또는 해결 표시 필요(만성 미해결 주장).
12. **버전 스냅샷 노후 위험** — `prisma clientVersion 6.19.3`, `claude-code/2.1.128`, frontier 모델명(GPT-5.2/Gemini-3.1/Sonnet-4.6 등 invitrovision·opengame·agent-world-synthesis) — "verified-as-of" 가드 없이 silently 노후.

---

## 링크 부족

### A. 끊긴 링크 (체계적 결함 — 우선순위 높음)

**A-1. `related:` 경로에서 하위폴더 누락 (18개 문서):** 다수 frontmatter가 `wiki/<basename>.md`로 작성돼 실제 경로 `wiki/<subfolder>/<basename>.md`를 가리키지 못함. 예:
- `concepts/ai-agent-basics.md` → `wiki/claude-code-overview.md` (실제: `wiki/concepts/...`)
- `concepts/openclaw-agent-architecture.md` → `wiki/claude-code-setup.md` 외 6개
- `patterns/claude-code-*` 다수, `projects/ai-shorts-production-...`, `summaries/mac-keyboard-shortcuts-...`
- `projects/isp-patch-rag/{00-proposal,common-architecture,phase-3plus-forward-design}.md` → `wiki/projects/isp-patch-knowledge-rag-system.md` (실제: `wiki/projects/isp-patch-rag/...`, 세그먼트 누락)
- `analyses/research-write-agent-separation.md` → `wiki/analyses/llm-json-parse-retry-with-dump.md` (실제: `patterns/`)
- `analyses/shellcheck-shell-script-linter.md` → `wiki/analyses/ssh-cli-toolkit-essentials.md` (실제: `patterns/`)

**A-2. 위키링크에 `.md` 확장자 포함 (15곳):** `patterns/claude-code-advanced.md`의 `[[claude-code-agent-teams-tmux.md]]` 등. Obsidian 위키링크는 확장자를 붙이지 않으므로 해석 실패 가능.

**A-3. 존재하지 않는 타겟:**
- `[[gieok-project]]` — `macos-launchagent-catchup-behavior.md`, `concepts/gieok.md`에서 참조하나 해당 파일 없음.
- `[[ExGRPO]]` — `su-01-olympiad-reasoning.md`.
- `concepts/gieok.md`의 `[[wikilink]]`(예시 추정), `cursor-agent-cli-overview.md`의 섹션앵커 링크 등은 오탐 가능성 있어 사람 확인 권장.

> 오탐 제외: shellcheck/launchd-secret의 `[[$foo==0]]`,`[[-e *.mpg]]`,`[[-o interactive]]` 등은 bash `[[ ]]` 테스트 구문(코드)이며 위키링크 아님.

### B. 상호 링크 누락 (강연관·단방향 또는 무링크)

**금융:** kis-balance-api-fields ↔ shared-broker-appkey-token-cache · api-circuit-breaker ↔ risk-control-exemption · api-circuit-breaker ↔ kis-cash-d2/kis-balance · financial-health-composite ↔ eps-vs-earnings-yield · financial-health-composite ↔ stock-screening-score-design · round-winrate ↔ partial-sell-rule · naver-finance-selector-drift ↔ scoring-system-ic-validation · openbb ↔ finance-analysis-nextjs

**ML:** anyflow ↔ qwen-image-2/tstars · mint-lora ↔ npo · su-01 ↔ npo(ExGRPO) · esamp ↔ onevl · agent-world-synthesis ↔ agentic-world-modeling-taxonomy · agent-world-synthesis ↔ opengame · invitrovision ↔ llatisa · ai-valuation-trustworthiness ↔ ai-coding-agent-cost-and-context-patterns

**Claude Code:** token-optimization ↔ agent-teams-tmux(단방향) · multi-llm-adapter ↔ cursor/codex-cli(단방향) · scheduled-tasks ↔ gieok/agent-weekly · source-leak ↔ auto-mode-guardrails(단방향) · everything-claude-code ↔ aris/agentspex/onemancompany · enterprise-security-bedrock ↔ mcp-config-secret/auto-mode-guardrails · ai-usage-philosophy ↔ karpathy-claude-md-skills · claude-code-overview(허브여야 하나 sink)

**웹/인프라:** supabase-magic-link ↔ vercel-cron-best-practices · mcp-config-secret ↔ launchd-secret(단방향) · grep-env-var-leak ↔ launchd-secret · build-vs-fork ↔ sqlite-readonly-data-swap · utc-iso-date-kst-rollover ↔ pgbouncer/prisma cron · oracle-cloud ↔ launchd-daemon-vs-cron · personal-ai-agent-messaging-channels ↔ kakao-messaging-automation(단방향)

**프로젝트:** isp-patch-rag 클러스터 ↔ qualcomm-camera-kernel-isp/soc-otf(단방향) · auto-pipe-blog ↔ auto-pipe-ppt(비대칭) · kernel-digest ↔ dev-blog(단방향) · kernel-digest ↔ slab-no-merge(단방향) · oss-radar ↔ 산출 OSS 분석 6종(거의 무링크) · mock-first-demo-safety-net ↔ blocked-dependency-productive-workflow(비대칭)

### C. index.md 최신성

- **isp-patch-rag 프로젝트 전체(8파일)가 프로젝트 섹션에서 누락** — 최대 갭.
- analyses 섹션에 `photoprism-ai-photos-app`, `upscayl-ai-image-upscaler`, `shellcheck-shell-script-linter`, 그리고 고립 페이지 목록 전부 누락.

---

## 프런트매터 불비

### 필수 필드 누락 (26문서)
| 문서 | 누락 필드 |
|---|---|
| analyses/agentic-world-modeling-taxonomy.md | tags, created, confidence |
| analyses/algorithmic-collusion-reinforcement-learning.md | tags, created, confidence |
| analyses/llada2-uni-unified-multimodal-diffusion.md | tags, created, confidence |
| analyses/near-future-policy-optimization-npo.md | tags, created, confidence |
| analyses/macos-launchagent-catchup-behavior.md | domain, sensitivity, sources, confidence |
| analyses/su-01-olympiad-reasoning.md | domain, sensitivity, sources, confidence |
| concepts/gieok.md | domain, sensitivity, confidence |
| projects/gieok.md | domain, sensitivity, confidence |
| analyses/github-pages-base-path-pattern.md | sources, confidence |
| analyses/llm-news-prediction-pitfalls.md | sources, confidence |
| analyses/multi-profile-cli-agent-isolation.md | sources, confidence |
| analyses/news-driven-market-signal-framework.md | sources, confidence |
| analyses/partial-sell-rule-idempotency.md | sources, confidence |
| analyses/supabase-magic-link-single-user-allowlist.md | sources, confidence |
| analyses/dca-trailing-stop-tuning.md | sources |
| analyses/llm-provider-aggregator-vs-local-vs-hub.md | sources |
| analyses/personal-ai-agent-messaging-channels.md | sources |
| analyses/polling-interval-vs-bar-interval.md | sources |
| analyses/risk-control-exemption-and-failed-attempt-accounting.md | sources |
| analyses/scoring-system-ic-validation.md | sources |
| analyses/soc-otf-sensor-to-ap.md | sources |
| analyses/vercel-friendly-database-options.md | sources |
| analyses/web-app-storage-without-db.md | sources |
| analyses/oauth-refresh-token-rotation-multi-client.md | confidence |
| analyses/python-pptx-design-token-pipeline.md | confidence |
| projects/agent-weekly.md | confidence |

### 비허용 필드값
- **`domain: research`** (스키마 허용값 `work|personal|both` 위반, 6문서): `diffusion-snr-t-bias`, `opengame-agentic-game-development`, `onevl-latent-reasoning`, `agent-world-environment-synthesis`, `tstars-tryon-virtual-try-on`, `meanflow-text-to-image`.

### 중복 키
- `projects/dev-blog.md` — `updated:` 키 2개(L7 `2026-07-01`, L84 `2026-06-06`). YAML 중복 키이며 06-06 값은 노후.

### 빈 `related: []` (10문서)
주로 oss-radar 산출 OSS 분석 등. 스키마상 필수는 아니나 상호 링크 부족(위 B)과 직결되므로 보강 권장.

> 양호: 파일명 전부 kebab-case 준수, `sensitivity: confidential` 문서 0개(commit 금지 위반 없음), `updated` 필드는 lint-report 본인 외 전 문서 보유.

---

## R1: Unicode 불가시 문자 (prompt injection 감사)

검출 없음 — `wiki/` 내의 어떤 `.md`에서도 ZWSP / RTLO / SHY / BOM 등의 불가시 문자는 검출되지 않았습니다. (셸 측 pre-scan 결과와 일치)
