# Wiki Schema — 운영 규칙

## Frontmatter 필수 필드

모든 wiki 문서는 아래 frontmatter로 시작해야 합니다.

```yaml
---
title: "문서 제목"
domain: "ai-agent | trading"                   # 지식 도메인 (둘 밖이면 수집하지 않음)
sensitivity: "public | internal | confidential" # 공개 수준
tags: ["태그1", "태그2"]                        # 검색용 키워드
created: "YYYY-MM-DD"
updated: "YYYY-MM-DD"
sources:                                        # 참조 원본 파일
  - "raw-sources/파일명.pdf"                    # 개인 자료
  - "local-only: ~/work-raw/파일명.pdf"         # 회사 자료 (로컬 전용)
confidence: "high | medium | low"              # 정보 신뢰도
related:                                        # 연관 wiki 파일 (상대경로)
  - "wiki/관련문서.md"
---
```

### domain 기준

| 값 | 기준 |
|---|---|
| `ai-agent` | AI 에이전트 활용 — Claude Code, MCP, 토큰 절약, 자동화 파이프라인 |
| `trading` | 트레이딩·투자 시스템 — 증권 API, 백테스트, 스코어링, 운영 버그 |

두 값 밖의 주제는 wiki에 수집하지 않는다 (CLAUDE.md 수집 기준 참조).

### sensitivity 기준

| 값 | 기준 | git commit |
|---|---|---|
| `public` | 외부 공개 가능 | 항상 OK |
| `internal` | 사내 수준, 민감하지 않음 | 판단 후 commit |
| `confidential` | 기밀, 개인정보 포함 | commit 금지 |

### sources 작성 규칙

- 개인 자료: `raw-sources/파일명` (repo 내 경로)
- 회사 자료: `local-only: ~/work-raw/파일명` (`local-only:` 접두사 필수)

### confidence 기준

| 값 | 기준 |
|---|---|
| `high` | 복수의 신뢰할 수 있는 출처로 검증됨 |
| `medium` | 단일 출처이거나 직접 확인 필요 |
| `low` | 추론·추측 포함, 반드시 검증 필요 |

## 문서 본문 구조

```markdown
---
(frontmatter)
---

# 제목

한 문단 요약 — 이 문서가 무엇인지 한눈에 파악 가능하게.

## 핵심 내용

본문. 사실 중심으로 작성. 의견은 > 인용 블록으로 구분.

## 세부 사항

필요 시 하위 섹션 추가.

## 관련 맥락

다른 개념·문서와의 연결고리.

## 변경 이력

- YYYY-MM-DD: 최초 생성 (출처: raw/xxx.md)
- YYYY-MM-DD: ○○ 내용 추가 (출처: raw/yyy.pdf)
```

## 파일 명명 규칙

- 영문 소문자 + 하이픈: `llm-wiki-pattern.md`
- 한국어 주제라도 파일명은 영문: `knowledge-management.md`
- 너무 길면 핵심 단어만: `rag-vs-wiki.md` (O), `retrieval-augmented-generation-versus-llm-wiki-pattern.md` (X)

## 폴더 구조 규칙

```
llm_wiki2/
├── wiki/                  # 모든 지식 통합 (git sync)
│   ├── concepts/          # 개념 정의
│   ├── analyses/          # 방법론·기술 분석 (뉴스성 리뷰 금지)
│   ├── patterns/          # 실무 패턴, 활용법
│   ├── projects/          # 프로젝트 기록
│   ├── decisions/         # 기술 결정
│   └── bugs/              # 버그·트러블슈팅
└── raw-sources/           # 개인 원본 자료 (git 선택적 포함)
```

- `raw-sources/`: 날짜 접두어 사용 가능 (`2026-04-14-paper.pdf`)
- `wiki/`: 날짜 없이 주제 중심 파일명
- `~/work-raw/`: repo 밖 로컬 경로, `.gitignore`에 명시

## 업데이트 정책

1. **기존 문서 우선**: 새 자료가 들어오면 새 문서보다 기존 문서 업데이트를 먼저 고려
2. **분기 기준**: 하나의 문서가 500줄을 넘으면 주제별로 분리
3. **병합 기준**: 200줄 미만 문서가 3개 이상 유사 주제면 병합 고려
4. **삭제 금지**: 오래된 정보는 삭제 대신 `~~취소선~~` 처리 후 이유 기록
