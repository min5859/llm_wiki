---
title: "Claude 토큰 절약 8가지 방법"
domain: "staging"
tags: ["Claude", "Claude Code", "토큰 절약", "컨텍스트", ".claudeignore", "sandbox", "Jupyter"]
created: "2026-04-16"
updated: "2026-04-16"
sources:
  - "raw/Tips/Claude 토큰 절약 8가지 방법.pdf"
confidence: "medium"
related:
  - "wiki/claude-code-token-optimization.md"
  - "wiki/claude-md-guide.md"
  - "wiki/claude-code-setup.md"
---

# Claude 토큰 절약 8가지 방법

Claude Code 비용과 품질은 컨텍스트 관리에 크게 좌우된다. Tips 자료는 `.claude/` 정리, 압축형 대화 규칙, 세션 길이, sandbox, `.claudeignore` 같은 실무 절약 방법을 묶어 설명한다.

## 핵심 내용

### 8가지 방법

| 번호 | 방법 | 목적 |
|---:|---|---|
| 1 | `.claude/` 정리정돈 | 불필요한 설정·프롬프트 로딩 감소 |
| 2 | 압축형 대화 규칙 도입 | 장황한 응답과 반복 설명 축소 |
| 3 | jupytext로 Jupyter 조작 경량화 | 노트북 JSON 전체 로딩 방지 |
| 4 | 세션 길이 최적화 | context rot와 auto compact 빈도 감소 |
| 5 | 로컬 LLM으로 지식을 RAG화 | 반복 참조 지식의 외부화 |
| 6 | 에이전트 역할에 맞는 모델 선택 | 작업 난이도별 비용 조절 |
| 7 | sandbox 설정 도입 | 승인 대기와 반복 권한 흐름 감소 |
| 8 | `.claudeignore` 설정 | 읽지 않아도 되는 파일 제외 |

## 세부 사항

### `.claude/` 정리

`.claude/` 안에 실험용 prompt, 오래된 settings, 쓰지 않는 hook이 쌓이면 세션 시작과 도구 선택이 복잡해진다. 실제 쓰는 파일만 남기고 목적별로 분리한다.

권장 구조:

```
.claude/
├── prompts/
├── hooks/
├── rules/
└── settings.json
```

### 압축형 대화 규칙

CLAUDE.md에 응답 스타일을 짧게 고정한다.

예:

```markdown
응답은 결론, 변경 파일, 검증 결과 중심으로 짧게 작성한다.
긴 설명은 사용자가 요청할 때만 제공한다.
```

### Jupyter는 jupytext로 경량화

`.ipynb`는 JSON과 출력이 커서 토큰을 많이 쓴다. `jupytext`로 `.py` 또는 `.md` paired format을 쓰면 diff와 읽기 비용이 줄어든다.

### 세션 길이

너무 긴 세션은 context rot, auto compact, 캐시 손상 가능성을 높인다. 하나의 주제 또는 하나의 레이어가 끝나면 세션을 정리한다.

### `.claudeignore`

읽지 않아도 되는 대용량 파일을 제외한다.

예:

```gitignore
node_modules/
dist/
build/
.next/
coverage/
*.log
*.mp4
*.png
```

## 관련 맥락

- Claude Code 전용 토큰 구조는 [[claude-code-token-optimization.md]].
- 설정·rules·sandbox는 [[claude-code-setup.md]]와 함께 관리한다.

## 변경 이력

- 2026-04-16: Tips PDF 기반 최초 생성
