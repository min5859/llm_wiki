# Wiki Schema — 운영 규칙

## Frontmatter 필수 필드

모든 wiki 문서는 아래 frontmatter로 시작해야 합니다.

```yaml
---
title: "문서 제목"
domain: "office | research | personal | ..."   # 최상위 도메인
tags: ["태그1", "태그2"]                        # 검색용 키워드
created: "YYYY-MM-DD"
updated: "YYYY-MM-DD"
sources:                                        # raw/ 아래 참조한 파일들
  - "raw/파일명.pdf"
  - "raw/링크.md"
confidence: "high | medium | low"              # 정보 신뢰도
related:                                        # 연관 wiki 파일 (상대경로)
  - "wiki/관련문서.md"
---
```

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
{domain}/
├── raw/          # 원본 자료
│   ├── *.pdf
│   ├── *.md      # 웹 페이지 스크랩, 메모
│   └── *.txt
└── wiki/         # 지식 문서
    └── *.md
```

- `raw/`는 날짜 접두어 사용 가능: `2026-04-14-karpathy-llm-wiki.md`
- `wiki/`는 날짜 접두어 없이 주제 중심으로

## 업데이트 정책

1. **기존 문서 우선**: 새 자료가 들어오면 새 문서보다 기존 문서 업데이트를 먼저 고려
2. **분기 기준**: 하나의 문서가 500줄을 넘으면 주제별로 분리
3. **병합 기준**: 200줄 미만 문서가 3개 이상 유사 주제면 병합 고려
4. **삭제 금지**: 오래된 정보는 삭제 대신 `~~취소선~~` 처리 후 이유 기록
