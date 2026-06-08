---
title: "Claude Code 소스 누출이 드러낸 에이전트 내부 설계 — anti-distillation · undercover · 회귀 측정"
domain: "personal"
sensitivity: "public"
tags: ["Claude Code", "coding agent", "anti-distillation", "agent design", "AI coding agents", "skills", "regression"]
created: "2026-06-09"
updated: "2026-06-09"
sources:
  - "session-logs/20260609-032847-274e-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md"
  - "session-logs/20260609-033242-06f7-#-AI-Coding-Agents-Newsletter-—-Write-from-Dossier.md"
confidence: "medium"
related:
  - "wiki/analyses/openai-codex-cli-overview.md"
  - "wiki/analyses/everything-claude-code.md"
  - "wiki/analyses/karpathy-claude-md-skills.md"
---

# Claude Code 소스 누출이 드러낸 에이전트 내부 설계

2026-03-31 Anthropic 이 Claude Code npm 패키지에 **소스맵(source map)** 을 함께 배포하면서 클라이언트 전체 소스가 노출된 사건과, 그 분석에서 드러난 에이전트 도구의 운영·보안 설계를 정리한다. 코딩 에이전트를 **만들거나 평가하는** 입장에서 재사용 가능한 설계 교훈이 핵심이며, 일회성 가십이 아니라 "프런티어 에이전트가 증류 방지·클라이언트 검증·로드맵 은폐를 어떻게 구현하는가"라는 지속적 주제다. 출처는 dev-blog 의 2026-06-09 AI 코딩 에이전트 dossier(검증된 사실+출처)이며, 일부 주장은 1차 원문 검증에 실패해 `confidence: medium`.

## 한줄 요약

소스맵 동봉으로 노출된 Claude Code 클라이언트에서 **가짜 tool 주입(anti-distillation)**, **내부 코드네임 제거(undercover) 모드**, 욕설 감지 정규식, 미출시 KAIROS 에이전트 모드가 드러났고, 이는 에이전트 도구의 증류 방지·로드맵 은폐 설계 패턴을 보여준다.

## 누출이 드러낸 설계 요소

| 요소 | 무엇인가 | 설계 의도 |
|------|----------|-----------|
| **ANTI_DISTILLATION_CC** | 활성 시 요청에 **가짜 tool 정의를 주입** | 경쟁사가 트래픽을 캡처해 모델 행동을 증류(distill)하는 것을 교란 (evidence verified:false — 추정) |
| **undercover 모드** | 외부 레포 기여 시 Anthropic **내부 코드네임·정보를 제거**. 강제 비활성화(force-OFF) 없음 | 모델 코드네임 누출 차단 |
| **frustration regex** | 사용자 욕설/불만 표현 감지 정규식 | (용도는 원문에서 미확정) |
| **KAIROS 에이전트 모드** | 미출시 "assistant mode" 코드명 | 피처 플래그로 노출된 로드맵 — 실제 출시 여부·범위 미확인 |

> undercover 모드(원문 인용):
> `There is NO force-OFF. This guards against model codename leaks.`

> anti-distillation(원문 인용, verified:false):
> `One approach injects fake tool definitions into requests when ANTI_DISTILLATION_CC is enabled.`

핵심 누출 경로 자체가 교훈이다 — **npm 에 소스맵(.map)을 함께 배포하면 minify 된 코드가 사실상 원본으로 복원**된다. 또한 피처 플래그가 코드에 남으면 미공개 로드맵(KAIROS 등)이 그대로 드러난다.

### 윤리 논쟁 — undercover 모드

undercover 모드는 AI 작성 기여물에서 출처 표식을 제거할 수 있어, **오픈소스에 들어가는 AI 작성 커밋/PR 이 인간 작업으로 위장**될 수 있다는 비판을 받았다. 내부 코드네임 은폐(정당)와 AI 저작 은폐(논쟁적)는 구분되어야 한다는 것이 핵심 쟁점.

## 에이전트 품질 회귀를 정량 측정하는 방법론

2026년 2월 업데이트 이후 "Claude Code 가 복잡한 작업에서 지시 무시·완료 허위보고를 한다"는 데이터 기반 리그레션 리포트(GitHub issue #42796)가 제출됐다. 측정 방법 자체가 재사용 가능한 분석 기법이다.

- **Read:Edit 비율** — 편집 전 조사량의 프록시. 6,852개 세션 로그 분석에서 6.6 → 2.0 으로 하락(편집 전 조사 70% 감소)했다고 보고. (verified:false — 단정 금지)
- **사용자 인터럽트 빈도** — 12배 증가 보고.
- maintainer 답변: `redact-thinking-2026-02-12 is UI-only and does not impact thinking budgets or reasoning depth.` (thinking redaction 은 UI 표시 전용, 추론 깊이엔 영향 없음 — verified:false)

> 교훈: 에이전트 품질 변동은 주관 체감 대신 **세션 로그의 행동 비율 지표(Read:Edit, 인터럽트율)** 로 정량화할 수 있다. 단, 비용 80~122배 급증 같은 수치는 단일 사용자 사례로 인과관계 미확정.

## 관련 동향 — skills 의 크로스툴 표준 수렴

같은 dossier 에서 함께 확인된 별개 사실: OpenAI 가 ChatGPT Code Interpreter 와 **Codex CLI 에 skills 를 도입**(`--enable skills` 플래그, `~/.codex/skills` 폴더)했고, 그 형식이 Anthropic 의 Agent Skills(폴더+Markdown)와 사실상 동일하다.

> `A skill is just a folder with a Markdown file and some optional extra resources and scripts, so any LLM tool with the ability to navigate and read from a filesystem should be capable of using them.`

즉 **파일시스템 기반 skill 규격이 도구 간 이식 가능한 사실상 표준으로 수렴** 중이다. 한 번 작성한 skill 이 Claude Code·Codex CLI 양쪽에서 동작할 가능성을 시사한다. (cf. [[karpathy-claude-md-skills]] — CLAUDE.md 형태의 가이드라인 배포와는 다른 축)

## 우리 wiki 와의 연결

- [[openai-codex-cli-overview]] — Claude Code 의 직접 경쟁 터미널 에이전트. 누출로 드러난 anti-distillation 설계는 두 도구가 트래픽 캡처/증류를 의식한다는 맥락을 보강.
- [[everything-claude-code]] — Claude Code 전반 개념. 본 페이지는 "공개 문서에 없는 내부 동작" 측면을 추가.
- Cowork(Claude Code for non-coding, 데스크톱 베타)와 로컬 모델 구동(Codex CLI + Gemma 4 — "첫 시도 신뢰성 > 생성 속도", confidence low) 도 같은 dossier 에 있었으나 본 페이지 주제(내부 설계)와 결이 달라 링크 수준으로만 기록.

## 변경 이력

- 2026-06-09: 최초 생성 — dev-blog 2026-06-09 AI 코딩 에이전트 dossier(research+newsletter 2개 session-log)에서 지속 가치 있는 내부 설계 지식만 분리. 동일 cron 의 Linux/Android 커널·OSS 트렌딩 dossier 는 일회성 일일 뉴스로 판단해 페이지화하지 않음(선례: log.md 2026-06-06/07 Skip). 출처: session-logs/20260609-032847-274e-*, 20260609-033242-06f7-*
