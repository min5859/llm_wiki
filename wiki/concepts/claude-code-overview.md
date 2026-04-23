---
title: "Claude Code 개요"
domain: "personal"
sensitivity: "public"
tags: ["Claude Code", "CLI", "AI 코딩 에이전트", "요금", "설치", "터미널"]
created: "2026-04-15"
updated: "2026-04-16"
sources:
  - "raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf"
  - "raw/음성 260413_AA-1-1_original.txt"
  - "raw/음성 260413_AA-1-6_original.txt"
  - "raw/음성 260414_AA-2-4_original.txt"
confidence: "high"
related:
  - "wiki/ai-agent-basics.md"
  - "wiki/claude-code-setup.md"
  - "wiki/claude-code-basic-usage.md"
  - "wiki/claude-code-enterprise-security-bedrock.md"
---

# Claude Code 개요

Claude Code는 Anthropic이 만든 터미널 기반 4세대 AI 코딩 에이전트다. 코드베이스 전체를 이해하고 파일을 읽고·쓰고·실행하며 자율적으로 개발 작업을 수행한다.

## 핵심 내용

### Claude Code의 위치

- **1세대**: 단순 코드 생성 (Copilot 초기)
- **2세대**: 컨텍스트 인식 (Copilot Chat)
- **3세대**: 인라인 편집 (Cursor, Windsurf)
- **4세대**: 자율 에이전트 (Claude Code, Devin)

Claude Code는 IDE에 종속되지 않는 터미널 CLI 도구다. VS Code, JetBrains 등의 IDE 익스텐션으로도 사용 가능하지만 핵심은 터미널이다.

### 요금 체계

| 플랜 | 월 요금 | 사용 한도 |
|---|---|---|
| Max 5× | $100 | 일반 대비 5배 |
| Max 20× | $200 | 일반 대비 20배 |

- API 키로 직접 사용 시 토큰 소비량에 따라 과금 (pay-as-you-go)
- Pro 플랜($20/월)에서도 Claude Code 사용 가능 (한도 있음)

### 설치 방법

```bash
npm install -g @anthropic-ai/claude-code
claude
```

Node.js 18+ 필요. 최초 실행 시 OAuth 인증 또는 API 키 설정.

### Claude Code가 할 수 있는 것

- 파일 읽기/쓰기/삭제
- 터미널 명령어 실행 (빌드, 테스트, git 등)
- 웹 검색 (MCP 또는 내장)
- 코드베이스 전체 탐색 및 수정
- 서브에이전트 생성 (병렬 작업)
- MCP 서버를 통한 외부 시스템 연결
- 음성 입력, IDE 연동, 원격 제어 등으로 터미널 작업 흐름 확장

## 세부 사항

### 동작 원리

1. 사용자가 자연어로 작업 요청
2. Claude가 코드베이스를 탐색하여 컨텍스트 수집
3. 계획(Plan) 수립
4. 도구 호출로 파일 수정, 명령 실행
5. 결과 확인 후 다음 단계 진행

### 토큰 소비 구조 (4계층)

| 계층 | 설명 | 토큰 비율 |
|---|---|---|
| System Prompt | Claude Code 내부 지시 | 고정 |
| CLAUDE.md | 프로젝트 지시서 | 매 요청마다 포함 |
| Context Window | 대화 이력 | 누적 증가 |
| Tool Results | 파일 읽기·실행 결과 | 작업마다 추가 |

### 주요 특징

- **Plan 모드**: 코드 변경 없이 계획만 수립 (`/plan`)
- **Permission 시스템**: 위험한 작업은 사용자 승인 요구
- **Hooks**: 특정 이벤트에 자동 스크립트 실행
- **MCP**: 외부 도구 연결 표준 프로토콜
- **Sub-agents**: 병렬 작업을 위한 에이전트 생성

### 도입 관점

강의에서는 Claude Code를 단순 개발 도구보다 "업무 실행 에이전트"로 설명했다. 개인이 쓰면 체감 생산성은 빠르게 올라가지만, 회사 전체 성과는 기존 프로세스가 바뀌지 않으면 제한적이다. 따라서 도입은 도구 설치보다 업무 절차, 승인, 보안, 산출물 검토 방식을 함께 바꾸는 문제다.

비개발 직군도 대상이다. 녹취에서는 일본 디자인 회사 사례처럼 코딩 경험이 없는 직원이 Claude Code로 업무 앱이나 자동화 도구를 배포하는 흐름을 언급했다. 엑셀 매크로, 이메일 응답, 보고 자동화 같은 사무 업무도 MCP나 CLI 연결을 통해 터미널 기반으로 처리할 수 있다.

## 관련 맥락

- VS Code와 달리 프로젝트 구조에 종속되지 않음 — 어떤 언어·프레임워크도 지원
- "에디터를 쓰는 사람"에서 "에이전트를 관리하는 사람"으로 개발자 역할 변화
- 삼성전자 반도체부문 2026년 4월 교육 수강 (강사: 김재우)

## 변경 이력

- 2026-04-16: 녹취 기반 도입 관점과 비개발 직군 활용 맥락 보강
- 2026-04-15: 최초 생성 (출처: raw/3-삼성전자-Claude-Code-입문교육-202604-2차-김재우.pdf)
