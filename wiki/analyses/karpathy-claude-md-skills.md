---
title: "multica-ai/andrej-karpathy-skills — CLAUDE.md 4원칙 가이드"
domain: "personal"
sensitivity: "public"
tags: ["CLAUDE.md", "Claude Code", "LLM coding", "prompt engineering", "open-source", "guideline"]
created: "2026-05-20"
updated: "2026-06-09"
sources:
  - "session-logs/20260520-090135-b97d-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md"
  - "session-logs/20260609-033242-06f7-#-AI-Coding-Agents-Newsletter-—-Write-from-Dossier.md"
confidence: "medium"
related:
  - "wiki/patterns/claude-md-guide.md"
  - "wiki/projects/oss-radar.md"
  - "wiki/analyses/claude-code-source-leak-internals.md"
---

# multica-ai/andrej-karpathy-skills — CLAUDE.md 4원칙 가이드

Andrej Karpathy 가 X(Twitter) 에서 지적한 LLM 코딩 도구의 구체적 함정을 4가지 원칙으로 정리해 단일 `CLAUDE.md` 파일로 배포하는 오픈소스 가이드라인. 2026-05-20 oss-radar cron 의 GitHub 트렌딩 분석에서 발굴됨. 별 수가 매우 큰 (≈137K) "단일 파일" 프로젝트로 보고된 점이 특이.

## 한줄 요약

`curl` 한 줄 또는 Claude Code 플러그인 두 줄로 설치 가능한 단일 `CLAUDE.md` 파일이며, Karpathy 의 관찰에서 도출한 4가지 LLM 코딩 원칙을 강제하는 가이드라인 모음.

## 4원칙

| # | 원칙 | 의도 |
|---|------|------|
| 1 | **Think Before Coding** | LLM 이 가정을 명시하고, 모호함이 있을 때 침묵 대신 명확화 질문을 던지도록 강제 |
| 2 | **Simplicity First** | 요청 범위를 벗어난 추상화·기능 추가·오버엔지니어링 방지 |
| 3 | **Surgical Changes** | 요청과 무관한 코드·주석·포매팅 수정 금지 (최소 변경 원칙) |
| 4 | **Goal-Driven Execution** | "수정해" 대신 "이 테스트를 통과시켜" 처럼 검증 가능한 성공 기준으로 변환 |

> Karpathy 인용 (README 발췌):
> "LLMs are exceptionally good at looping until they meet specific goals... Don't tell it what to do, give it success criteria and watch it go."

## 설치 방식

두 가지 경로:

- **A. Claude Code Plugin** — `forrestchang/andrej-karpathy-skills` 마켓플레이스 등록 형태. 모든 프로젝트에 공통 적용.
- **B. CLAUDE.md 직접 추가** — 프로젝트별 적용. 새 프로젝트는 `curl -o CLAUDE.md ...`, 기존 프로젝트는 `>> CLAUDE.md` 로 append.

Cursor IDE 도 `.cursor/rules/karpathy-guidelines.mdc` 가 동봉되어 동일 가이드라인이 동작.

## 작동 신호 (저자 정의)

이 가이드라인이 잘 동작하면 관찰되는 변화:

- diff 안에 불필요한 변경이 줄어든다 (요청한 것만 변경됨)
- 오버엔지니어링으로 인한 재작성이 줄어든다 (처음부터 단순함)
- 구현 전에 명확화 질문이 먼저 나온다 (실수 후가 아님)
- PR 이 작고 깔끔해진다 (드라이브-바이 리팩터링 없음)

## 트레이드오프

가이드라인은 **신중함 (caution) > 속도 (speed)** 쪽으로 편향된다. 단순한 typo 수정 같은 trivial 작업에는 오히려 응답 속도를 늦출 수 있다. 저자는 명시적으로 "판단해서 적용" 을 권장 — 모든 변경에 풀 리거(rigor) 가 필요한 것은 아니다.

## 우리 wiki 와의 연결

- 사용자가 이미 [[claude-md-guide]] (CLAUDE.md 완전 가이드) 페이지를 보유 — 4계층 강제력·작성 패턴·실물 공개 프로젝트 규칙·스타터 템플릿이 정리되어 있음. 본 Karpathy 가이드라인은 그 중 "원칙 4종" 형태의 응축된 외부 사례로 자리잡음.
- 사용자의 글로벌 `~/.claude/CLAUDE.md` (Global Rules) 도 ① Ambiguity → 명확화 질문, ② Simplicity → 200줄을 50줄로, ③ Goal-Driven Execution → 테스트 기반 성공 기준 으로 Karpathy 4원칙과 사실상 동형. 이 페이지는 "외부 사례의 응축 표현" 으로 참고 가치.

## 실용성 평가

- **설치 장벽 거의 없음**: `curl` 1줄 또는 플러그인 명령 2줄.
- **기존 `CLAUDE.md` 와 병합 친화적**: 프로젝트 고유 규칙과 충돌 없이 추가 가능 (저자가 명시).
- **검증 한계**: 137K 별이라는 수치는 매우 큰 편이라 통상의 단일 파일 OSS 와 다소 이격. README 발췌 기반의 1차 정보이므로 별 수·트렌드 평가는 [원본 레포](https://github.com/forrestchang/andrej-karpathy-skills) 에서 재확인 권장 (oss-radar 의 hallucination 가드 룰과 동일 자세).
- **`confidence: medium`**: README 발췌만으로 평가했고, 실제 가이드라인의 효과는 모델 버전·작업 복잡도에 따라 달라짐.

## 관련: skills 의 크로스툴 표준 수렴

본 페이지는 `CLAUDE.md` 형태의 **가이드라인 배포**를 다루지만, 별개 축으로 **Agent Skills(폴더+Markdown) 규격의 도구 간 수렴**이 진행 중이다. OpenAI 가 ChatGPT·Codex CLI 에 동일 형식의 filesystem skill 을 채택(`--enable skills`, `~/.codex/skills`)하면서, 파일시스템을 읽는 어떤 LLM 도구든 이식 가능한 사실상 표준으로 굳어질 가능성이 보고됐다. 상세는 [[claude-code-source-leak-internals]] 의 "skills 의 크로스툴 표준 수렴" 절 참조. (출처: dev-blog 2026-06-09 AI 코딩 에이전트 dossier)

## 변경 이력

- 2026-06-09: "skills 의 크로스툴 표준 수렴" 절 추가 + [[claude-code-source-leak-internals]] 교차 링크. 출처: session-logs/20260609-033242-06f7-*
- 2026-05-20: 최초 생성 — oss-radar cron 의 GitHub 트렌딩 분석 결과 (`assistant_turns: 1` 으로 5건 중 유일하게 완성된 분석) 에서 분리. 출처: session-logs/20260520-090135-b97d-*
