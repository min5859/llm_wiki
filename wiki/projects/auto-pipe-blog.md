---
title: "auto-pipe-blog — 컨셉→velog 글 자동화 파이프라인"
domain: personal
sensitivity: public
tags: ["project", "blog-automation", "claude-cli", "agent-cli", "velog", "mermaid", "bash"]
created: 2026-05-16
updated: 2026-05-17
sources:
  - "session-logs/20260516-095909-4308-간단한-컨셉만-설명하면-자료조사부터-블로그에-포스팅할-수-있는-글로-만들어주는-프로그램을.md"
  - "session-logs/20260516-194006-487e-velog 게시 (auto-pipe-blog factcheck rewrite + velog publish 첫 가동)"
  - "session-logs/20260516-201219-fc52-기술-블로그-작가-사실-확인 rewrite (PostgreSQL VACUUM 1차)"
  - "session-logs/20260516-203504-694d-기술-블로그-작가-사실-확인 rewrite (Playwright flaky 2차)"
  - "session-logs/20260516-225709-62eb-Notion-publisher 자동 등록 (Playwright 글)"
confidence: high
related:
  - "wiki/projects/dev-blog.md"
  - "wiki/analyses/multi-llm-provider-adapter-pattern.md"
---

# auto-pipe-blog — 컨셉→velog 글 자동화 파이프라인

한 줄 컨셉만 주면 자료조사 → 목차 → 본문 → 다이어그램 → 최종 post.md 까지 만들어 velog 에 복붙할 수 있는 글로 변환하는 Bash 오케스트레이션 파이프라인. AI 호출은 `claude -p` (메인) 과 `agent -p` (보조 비교) 의 stdin 인터페이스만 사용한다.

자매 프로젝트 [[dev-blog]] 가 「매일 정해진 토픽들을 자동 수집→재작성→정적 사이트」 발행 흐름인 데 반해, 본 프로젝트는 「사용자가 컨셉 1개를 주면 1개의 velog 글을 만들어 주는」 단발 글 생성 도구이다.

## 위치 / 진입점

- 디렉터리: `auto-pipe-blog`
- 진입 스크립트: `./scripts/run.sh "<컨셉 문자열>"`
- AI 백엔드 전환: `CALL_LLM_BACKEND=agent ./scripts/run.sh "<컨셉>"` 으로 `claude -p` 대신 `agent -p` 호출

## 파이프라인 흐름

```
concept (CLI 인자)
   └─ 00-slug   → slug.txt          (디렉터리명용 한 줄 슬러그)
   └─ 01-research → research.md     (참고 자료·핵심 사실 정리)
   └─ 02-outline  → outline.md      (글의 구조·섹션 목차)
   └─ 03-draft    → draft.md        (본문 1차 작성)
   └─ 05-assemble → post.md         (velog frontmatter + mermaid 포함 최종본)
output/<slug>/{concept.txt, research.md, outline.md, draft.md, post.md, _rendered/}
```

각 단계는 prompt 파일 (`prompts/<step>.md`) 을 `lib.sh::render()` 가 변수 치환해 `output/<slug>/_rendered/<step>.rendered.md` 로 풀어 둔 뒤, `claude -p` 의 stdin 으로 흘려 보내고 표준출력을 산출물 파일로 받는다. **step 마다 skip-if-exists** (산출물 파일이 이미 있으면 LLM 재호출 없이 패스), `FORCE=1` 환경변수로 강제 재생성.

step 번호 04 (visualize, mermaid → PNG 변환) 는 Phase 2 로 분리되어 현 시점엔 비어 있다. post.md 안에 mermaid 코드블록만 박혀 있고 velog 가 직접 렌더하지 못하므로 다음 Phase 에서 `mmdc` 호출 + 본문 치환으로 PNG inline 화 예정.

## 주요 설계 판단

1. **bash + `claude -p` stdin** — Node/Python 런타임 없이 zsh 만으로 오케스트레이션. 멀티라인·특수문자 안전성은 `python3 -c` 한 줄로 `Template.substitute` 호출하는 `render()` 헬퍼에 위임 (sed/heredoc 의 quoting 함정 회피).
2. **LLM 백엔드 단일 진입점** — `CALL_LLM_BACKEND` 환경변수로 `claude -p` 와 `agent -p` 를 같은 인터페이스로 전환. 어댑터 경계가 lib.sh 한 곳에 응집해 비교 실험이 한 줄로 가능. ([[multi-llm-provider-adapter-pattern]] 의 bash 판)
3. **velog 자동 발행은 1차 범위 외** — post.md 까지만 생성하고 사용자가 복붙하는 흐름. velog Editor API 의존성 회피.
4. **다이어그램은 mermaid → mmdc → PNG** — velog 가 mermaid 직접 렌더 안 하므로, 본문에 mermaid 코드블록을 박은 채로 1차 만들고 Phase 2 에서 `mmdc` (Mermaid CLI) 로 PNG 변환 + 본문 자동 치환.
5. **단계별 산출물을 모두 디스크에 보존** — `output/<slug>/{research,outline,draft,post}.md` 가 각각 파일로 남아 재실행 시 비싼 LLM 호출 없이 후속 단계만 돌릴 수 있고, 사용자 검수에서 「draft 까지는 좋은데 assemble 만 다시」 같은 워크플로가 가능.
6. **샘플 산출물의 git 추적** — `samples/<slug>/` 에 첫 E2E 산출물 5개 파일 (concept.txt 포함) 을 그대로 보관. 향후 프롬프트 튜닝의 비교 기준점.

## Phase 1 검증 (E2E smoke test, 2026-05-16)

테스트 컨셉: "PostgreSQL 의 MVCC 가 만들어내는 dead tuple 과 VACUUM 의 동작 원리"

| 단계 | 소요 시간 | 산출물 행 수 |
|------|----------|-------------|
| 00-slug | 6s | `postgres-mvcc-dead-tuples-vacuum` 한 줄 |
| 01-research | 63s | 58 |
| 02-outline | 41s | 63 |
| 03-draft | 74s | 76 |
| 05-assemble | 56s | 78 |
| **합계** | **~4분** | post.md 78줄 (3.3K 한글) |

post.md 에 velog frontmatter 정상, mermaid 코드블록 2개 inline. 샘플은 `samples/postgres-mvcc-dead-tuples-vacuum/` 로 보관.

## Phase 계획

- **Phase 1** ✅ — concept → post.md 5단계 파이프라인 (mermaid 코드블록은 텍스트로만, PNG 미생성)
- **Phase 2** — mermaid → mmdc → PNG inline (`mmdc` 설치 방식: npm global vs `npx -y @mermaid-js/mermaid-cli` 미결)
- **Phase 3** ✅ — velog API 자동 발행 (2026-05-16 가동) + 사실 확인 (factcheck) rewrite 단계 추가
- **Phase 3.5** ✅ — Notion publisher (2026-05-16) — `mcp__notion__API-*` 도구로 post.md → Notion 페이지 자동 등록. parent 가 page 인지 database 인지 자동 판별, frontmatter description 은 ℹ️ callout 으로, 본문은 100개 블록씩 patch-block-children 으로 분할 추가, 로컬 이미지 (`./images/...`) 는 "수동 첨부 필요" 안내 paragraph 로 치환
- **Phase 4** — frontmatter tags 자동 생성 (현재는 사용자 확인 전제)

`tasks/todo.md` 상단의 미결정 항목: ① mmdc 설치 방식 ② `claude -p` 웹 도구 권한 부여 방식 ③ mermaid 외 일러스트가 정말 필요한지 샘플로 검증 ④ tags 자동 vs 사용자 확인.

## 관련 파일 / 디렉터리

- `CLAUDE.md` — 프로젝트 의도·파이프라인·LLM 호출 규약
- `tasks/todo.md` — 미결정 항목 + Phase 별 진행 상황 체크리스트
- `tasks/lessons.md` — 작업 중 깨달은 교훈 누적
- `prompts/{00-slug,01-research,02-outline,03-draft,05-assemble}.md` — 단계별 프롬프트
- `scripts/lib.sh` — `render()`, `call_llm()` 헬퍼 (LLM 백엔드 분기 위치)
- `scripts/run.sh` — 5 단계 순차 실행 진입점
- `samples/<slug>/` — Phase 1 E2E 산출물 첫 보존본

## 변경 이력

- 2026-05-16: Phase 0 (scaffold) + Phase 1 (concept→post.md 5단계 파이프라인) 완료. `claude -p` stdin 호출 + skip-if-exists 산출물 흐름 + `CALL_LLM_BACKEND` 어댑터 분기. MVCC/VACUUM 컨셉으로 E2E ~4분 / 78줄 post.md / mermaid 2 블록 (commit `a9cbb0e` scaffold + `770b27f` phase1). 출처: session-logs/20260516-095909-4308-*
- 2026-05-16: **factcheck rewrite** 단계 추가 — draft 본문에 대해 자료조사 자료로 사실 확인 담당이 지적한 이슈를 반영한 새 본문 생성. PostgreSQL MVCC/VACUUM (1차) 및 Playwright flaky 테스트 (2차) 두 글에 적용. velog 게시까지 가동. 출처: session-logs/20260516-{194006,201219,203504}-*
- 2026-05-16: **Notion publisher** 추가 (Phase 3.5) — `mcp__notion__API-*` 도구로 post.md → Notion 페이지 자동 등록. parent ID 가 page 인지 database 인지 자동 판별, frontmatter description 은 ℹ️ callout 으로, 본문은 100 블록씩 patch-block-children 분할 추가, 로컬 이미지는 "수동 첨부 필요" 안내 paragraph 로 치환. Playwright 글 자동 등록 검증 (출처: session-logs/20260516-225709-62eb-*)
