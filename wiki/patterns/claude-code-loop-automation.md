---
title: "Claude Code /loop 자동화 패턴"
domain: "personal"
sensitivity: "public"
tags: ["Claude Code", "/loop", "자동화", "리포트", "스케줄", "정보 수집"]
created: "2026-04-16"
updated: "2026-04-16"
sources:
  - "raw/Tips/Claude Code loop 완전 정복-매일 아침 자동으로 기술·금융 정보를 수집하는 방법.pdf"
confidence: "medium"
related:
  - "wiki/claude-code-basic-usage.md"
  - "wiki/claude-code-token-optimization.md"
---

# Claude Code /loop 자동화 패턴

`/loop`는 Claude Code 세션에서 같은 작업을 주기적으로 반복하도록 만드는 기능이다. 매일 아침 기술·금융 정보 수집, 로그 요약, 문서 갱신 후보 점검처럼 반복 리포트가 필요한 작업에 적합하다.

## 핵심 내용

### 언제 쓰는가

좋은 사용 사례:

- 매일 아침 뉴스·기술 동향 요약
- 특정 저장소의 이슈/PR 변화 확인
- 로그나 리포트 파일을 주기적으로 읽고 요약
- 정해진 형식의 markdown 리포트 생성

피해야 할 사용 사례:

- 영구 예약 작업
- 승인 없이 외부 시스템을 변경하는 작업
- 장시간 무한 루프가 필요한 작업
- 비용 상한이 불명확한 대규모 분석

### 기본 구조

```
CLAUDE.md 또는 prompt 파일
→ 수집 대상 정의
→ 실행 스크립트
→ /loop 반복
→ markdown 리포트 출력
```

자료에서는 `collect.sh` 같은 수집 스크립트와 `CLAUDE.md` 지시를 조합해 반복 리포트를 만드는 흐름을 다룬다.

## 세부 사항

### 재현성을 높이는 방법

`/loop`를 안정적으로 쓰려면 대화창 지시보다 파일 기반 지시가 낫다.

- 수집 대상 URL/API/파일 경로를 명시
- 출력 파일명과 디렉터리를 고정
- 요약 형식과 섹션 순서를 고정
- 실패 시 보고 형식을 정함
- 외부 명령과 네트워크 접근 권한을 명시

예시 구조:

```
.claude/
├── prompts/
│   └── daily-report.md
scripts/
└── collect.sh
reports/
└── daily/
```

### 출력 리포트 예시

권장 섹션:

- 오늘의 핵심 변화
- 중요 링크
- 업무에 영향이 있는 항목
- 추가 확인 필요
- 다음 실행 때 볼 것

### 운영 주의점

- `/loop`는 세션 기반이다. 세션 종료 시 반복도 종료될 수 있다.
- 반복 주기가 짧으면 토큰과 API 비용이 빠르게 증가한다.
- 외부 웹/금융 정보는 최신성 검증이 필요하다.
- 자동 수집 결과는 사람 검토 없이 의사결정 자료로 쓰지 않는다.
- 회사 환경에서는 네트워크 접근과 저장 위치를 제한한다.

## 관련 맥락

- `/loop`는 [[claude-code-basic-usage.md]]의 자동화 파이프라인과 연결된다.
- 반복 작업은 컨텍스트를 빠르게 키우므로 [[claude-code-token-optimization.md]]의 세션·출력 관리 원칙이 중요하다.

## 변경 이력

- 2026-04-16: Tips PDF 기반 최초 생성
