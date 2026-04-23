# LLM Wiki — Claude Code 지침

이 프로젝트는 Karpathy의 LLM Wiki 패턴을 따르는 개인 지식 축적 시스템입니다.
SW 개발 엔지니어의 업무·개인 지식을 단일 wiki에 통합 관리합니다.

## 디렉토리 구조

```
llm_wiki/
├── wiki/                  # 모든 지식 통합 (git sync 대상)
│   ├── concepts/          # 개념 정의
│   ├── analyses/          # 논문·기술 분석
│   ├── patterns/          # 실무 패턴, AI 활용법
│   ├── projects/          # 프로젝트 기록
│   ├── decisions/         # 기술 결정
│   ├── summaries/         # 요약·참고
│   ├── bugs/              # 버그·트러블슈팅
│   └── people/            # 인물·팀
├── raw-sources/           # 개인 원본 자료 (git 포함)
├── graphify-out/          # 지식 그래프 출력물
├── schema.md              # Wiki 운영 규칙
└── GUIDE.md               # 작성 가이드

[로컬 전용 — git 밖]
~/work-raw/                # 회사 원본 파일 (절대 git 포함 금지)
```

## Claude의 역할

### 새 자료가 들어올 때

1. 개인 자료는 `raw-sources/`, 회사 자료는 `~/work-raw/` 에 있음을 확인한다.
2. 관련된 `wiki/` 문서들을 검색한다.
3. 기존 문서가 있으면 업데이트, 없으면 새 문서를 생성한다.
4. frontmatter의 `domain`, `sensitivity`, `sources`, `updated` 를 정확히 기재한다.
5. 영향받는 모든 wiki 페이지의 `updated` 날짜와 `sources`를 갱신한다.

### 질문에 답할 때

- 먼저 `wiki/` 를 검색해 기존 지식을 확인한다.
- wiki에 없는 정보라면 raw를 참조하거나 새 wiki 작성을 제안한다.
- 답변 후 wiki 업데이트가 필요하면 제안한다.

### 절대 하지 말아야 할 것

- `raw-sources/`, `~/work-raw/` 파일 수정 금지
- 사용자 확인 없이 wiki 삭제 금지
- 출처 없는 정보를 wiki에 기정사실로 기록 금지
- `sensitivity: confidential` 문서를 git commit 금지
- 회사 원본 파일 내용을 그대로 wiki에 복사 금지 (반드시 sanitize)

## Wiki 파일 형식

모든 wiki 파일은 `schema.md`에 정의된 frontmatter를 포함해야 한다.
파일명: `kebab-case.md` (영문 소문자, 하이픈 구분)

### frontmatter 필수 필드

- `domain`: `work | personal | both`
- `sensitivity`: `public | internal | confidential`
- `sources`: 개인 자료는 `raw-sources/파일명`, 회사 자료는 `local-only: ~/work-raw/파일명`

### 파일 배치 기준

| 내용 | 폴더 |
|------|------|
| 개념 정의, 기술 설명 | `wiki/concepts/` |
| 논문 요약, 기술 분석 | `wiki/analyses/` |
| 실무 패턴, AI 활용법 | `wiki/patterns/` |
| 프로젝트 기록 | `wiki/projects/` |
| 기술 결정 및 이유 | `wiki/decisions/` |
| 요약·참고 자료 | `wiki/summaries/` |

## 업데이트 원칙

- 새 자료 1개 → 관련 wiki 페이지 전체 검토 후 필요한 것만 수정
- 추가/수정된 내용은 `## 변경 이력` 섹션에 한 줄 기록
- confidence가 `low`인 항목은 반드시 출처를 명시하거나 삭제 제안
- 회사 자료 기반 wiki 작성 시: 구체적 수치·코드·고유명사 제거 후 개념·패턴만 기록
