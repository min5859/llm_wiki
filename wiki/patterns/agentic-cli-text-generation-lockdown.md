---
title: "에이전트형 CLI를 순수 텍스트 생성기로 쓸 때의 잠금(lockdown) 패턴"
domain: "ai-agent"
sensitivity: public
tags: ["pattern", "llm", "claude-cli", "codex", "cursor-agent", "pipeline", "reliability", "model-pinning", "sandbox"]
created: 2026-07-22
updated: 2026-07-22
sources:
  - "dev-blog commit e28df77 (fix: isolate rewrite adapters to stop agentic non-JSON replies)"
  - "dev-blog logs/ai-rewrite-failures/ 128건 분석 (2026-07-22 세션)"
confidence: high
related:
  - "wiki/patterns/llm-json-parse-retry-with-dump.md"
  - "wiki/patterns/prompt-schema-pipeline-coupling.md"
  - "wiki/projects/dev-blog.md"
  - "wiki/analyses/research-write-agent-separation.md"
---

# 에이전트형 CLI를 순수 텍스트 생성기로 쓸 때의 잠금(lockdown) 패턴

자동화 파이프라인이 `claude -p` / `codex exec` / `cursor-agent` 같은 **에이전트형 CLI 를 "프롬프트 → stdout JSON" 순수 텍스트 생성기로** 쓸 때, 그 stdout 계약은 명시적으로 잠그지 않으면 깨진다. 이 CLI 들은 기본값이 에이전트다: cwd 의 파일을 읽고 쓸 수 있고, 프로젝트 컨텍스트(CLAUDE.md·설정·기존 산출물)를 자동 로드하며, **자동 업데이트와 모델 별칭 재해석으로 기저 동작이 코드 변경 없이 표류**한다.

## 실패 양태 (dev-blog 실측, 2026-07)

- 실패 128건 중 89건이 `AI response did not contain JSON`. 89건의 raw 응답을 전수 분류하니 **100% 자연어 보고** ("뉴스레터를 작성했습니다" / "이미 파일이 유효하니 수정 불필요") — 잘림·코드펜스·빈 응답 0건.
- 모델이 저장소 cwd 에서 기존 `data/generated/*.json` 을 발견하고, JSON 출력 대신 **에이전트처럼 행동한 뒤 대화형 요약을 stdout 에 남긴 것**. 프롬프트의 "JSON만 출력" 지시(맨 앞 명시)로도 못 막았다.
- 급증 구간(7/8~7/17, 평시의 2.6배)에 저장소 커밋은 자동 briefing 뿐 — **원인이 저장소 밖** (CLI 자동 업데이트 / `sonnet` 별칭의 실모델 변경) 임을 시사. 실패 덤프에 모델명이 없어 소급 확정은 불가(→ 아래 5번 교훈).
- 동일 프롬프트 단순 재시도의 회복률은 54% (attempt1 실패 61건 중 33건만 attempt2 성공) — **행동적 실패는 확률적 실패와 달리 같은 프롬프트 재시도로 잘 안 잡힌다**.

## 잠금 체크리스트

| # | 잠금 | dev-blog 구현 |
|---|------|--------------|
| 1 | **도구 전면 차단** | claude CLI (≥2.1.217): `--tools ""` — 빌트인 도구 전부 비활성. 인자 배열로 직접 구성해야 함 (`split(/\s+/)` 는 빈 문자열 인자를 소실) |
| 2 | **cwd 격리** | spawn 시 `cwd: os.tmpdir()` — 프로젝트 CLAUDE.md·설정·기존 산출물이 컨텍스트에 노출되지 않음. 프롬프트가 자기완결적(입력 JSON 인라인)이어야 성립 |
| 3 | **읽기 전용 샌드박스** | codex: `--sandbox read-only`. cursor-agent: `--mode=ask` (읽기 전용). 도구가 필요한 어댑터(프롬프트를 파일로 넘기는 cursor)는 차단 대신 read-only 로 |
| 4 | **모델 별칭 고정** | `'sonnet'` → `'claude-sonnet-5'` 고정 ID. 별칭은 배포 시점에 따라 다른 실모델로 표류 — 무인 파이프라인에서 별칭 사용 금지 |
| 5 | **덤프에 adapter/model 기록** | 실패 덤프 헤더에 `# adapter:` / `# model:` — 다음 표류 때 소급 진단 가능하게 |
| 6 | **자동 업데이트 차단** | spawn env 에 `DISABLE_AUTOUPDATER=1` — cron 실행 중 CLI 동작 변경 방지 |
| 7 | **교정 재시도** | attempt≥2 에서 프롬프트에 "[재시도] 직전 응답이 유효한 JSON이 아니었습니다. 도구 사용·설명 없이 JSON 객체 하나만" 덧붙임 — 동일 프롬프트 재전송보다 행동적 실패에 유효 |

적용 결과: 실지 실행 1회에서 attempt1 즉시 성공, 실패 덤프 무증가 (회귀 테스트 117/117).

## 안티패턴

- **프롬프트 지시만으로 stdout 계약 방어** — "JSON만 출력, 코드펜스 금지"는 확률적 혼입은 줄여도 기저 하네스의 에이전트화는 못 막는다. 계약은 프롬프트가 아니라 **실행 환경(도구·cwd·샌드박스)에서** 강제할 것
- **행동적 실패를 동일 프롬프트 재시도로 대응** — 확률적 실패용 재시도(1~2회)는 유지하되, 재시도 프롬프트에 교정 지시를 덧붙여야 회복률이 오름
- **무인 파이프라인에서 모델 별칭(`sonnet`/`opus`) 사용** — 별칭 재해석은 조용한 대규모 회귀의 진원. 사람이 지켜보는 대화 세션과 달리 무인 경로는 고정 ID + 명시적 업그레이드
- **연구(research)·작문(write) 경로에 같은 잠금 일괄 적용** — 도구 조사가 목적인 research 경로는 cwd·도구가 필요하다. 잠금은 "순수 작문" 경로에만; 경로별 어댑터 함수를 분리해 두면 선택 적용이 한 줄

## 관련

- [[llm-json-parse-retry-with-dump]] — 이 패턴의 *대응* 계층 (실패 덤프 + 재시도). 본 패턴은 *예방* 계층
- [[prompt-schema-pipeline-coupling]] — stdout 계약 기반 파이프라인이 기저 CLI 의 에이전트화에 깨지는 신종 변형 (dev-blog 7/5 관측이 최초 신호)
- [[research-write-agent-separation]] — research/write 경로 분리가 잠금의 선택 적용을 가능하게 한 구조

## 변경 이력

- 2026-07-22: 최초 작성. dev-blog 7월 rewrite 실패 급증(89건, 100% 자연어 응답)의 근본 원인 분석 + commit e28df77 수정에서 도출. 7/5 세션 로그의 "write 에이전트형 표류" 관측(1회차)이 이번 확정·해결(2회차)로 승격 기준 충족
