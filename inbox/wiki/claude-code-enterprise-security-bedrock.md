---
title: "Claude Code 기업 보안 도입과 AWS Bedrock"
domain: "staging"
tags: ["Claude Code", "기업 보안", "AWS Bedrock", "Vertex AI", "Azure AI", "권한", "데이터 유출"]
created: "2026-04-16"
updated: "2026-04-16"
sources:
  - "raw/Tips/Claude Code-기업보안도입가이드-AWS-Bedrock-2026.pdf"
  - "raw/음성 260415_AA-3-2_original.txt"
confidence: "medium"
related:
  - "wiki/claude-code-setup.md"
  - "wiki/claude-code-skills-plugins.md"
  - "wiki/claude-code-overview.md"
---

# Claude Code 기업 보안 도입과 AWS Bedrock

기업에서 Claude Code를 도입할 때 핵심은 모델 선택보다 데이터 경계, 권한, 플러그인 검증, 감사 가능성이다. AWS Bedrock, Vertex AI, Azure AI 같은 관리형 경로는 보안·계정·네트워크 통제를 위해 검토할 수 있다.

## 핵심 내용

### 기업 도입 시 먼저 정할 것

| 영역 | 결정할 내용 |
|---|---|
| 데이터 | 어떤 코드·문서·로그를 모델에 보낼 수 있는가 |
| 계정 | 개인 계정, 팀 계정, SSO, cloud provider 계정 중 무엇을 쓰는가 |
| 네트워크 | 외부 웹, MCP, 플러그인, HTTP hook 허용 범위 |
| 권한 | 파일 읽기/쓰기, shell 명령, 배포 명령 허용 범위 |
| 감사 | 누가 어떤 세션에서 어떤 변경을 했는지 추적 방법 |
| 비용 | 사용자별/팀별 사용량과 상한 관리 |

### Bedrock 사용 맥락

AWS Bedrock을 쓰면 Anthropic 모델을 AWS 계정과 리전, IAM, 네트워크 정책 안에서 호출하는 구성을 검토할 수 있다.

Tips 자료에 등장하는 핵심 환경 변수:

```bash
CLAUDE_CODE_USE_BEDROCK=1
AWS_REGION=<region>
AWS_PROFILE=<profile>
```

실제 적용 전에는 회사의 AWS 계정 정책, 허용 리전, 로그 보관 정책, 모델 사용 승인 절차를 확인해야 한다.

### 외부 스킬·플러그인 위험

녹취에서는 검증되지 않은 스킬 사용을 특히 경고한다. 스킬은 단순 프롬프트가 아니라 shell 명령, 파일 조작, 네트워크 전송을 포함할 수 있다.

위험 예시:

- 사내 소스나 문서를 외부 endpoint로 전송
- 민감 파일을 읽어 로그에 남김
- 과도한 파일 삭제/수정 명령 실행
- 검증되지 않은 MCP 서버가 백그라운드에서 통신

회사 환경에서는 공식·검증 플러그인 위주로 시작하고, 팀 전용 플러그인은 보안 리뷰 후 배포한다.

## 세부 사항

### 권한 설계

권장:

- 기본은 최소 권한
- 위험 명령은 deny
- 반복 허용 명령은 명시적 allow
- 프로젝트별 `.claude/settings.json`으로 팀 표준 공유
- 개인 실험은 `.claude/settings.local.json`에 분리

예시:

```json
{
  "permissions": {
    "allow": ["Bash(npm test)", "Bash(git status)"],
    "deny": ["Bash(rm -rf *)", "Bash(sudo *)"]
  }
}
```

### 관리형 도입 단계

1. 개인 실험: 비민감 샘플 프로젝트로 사용 패턴 파악
2. 팀 파일럿: 허용 도구, 금지 명령, 리포트 형식 정의
3. 보안 검토: 플러그인, MCP, hook, 로그, 데이터 경계 점검
4. 표준 템플릿화: CLAUDE.md, settings, rules, prompt 템플릿 배포
5. 운영 모니터링: 비용, 세션 품질, 보안 이벤트 추적

### 내부 LLM 또는 대체 모델 사용 시 주의

Claude Code의 품질은 단순히 "어떤 LLM이 붙었는가"만으로 결정되지 않는다. 도구 호출, 작업 난이도 판단, 컨텍스트 구성, 모델 라우팅 같은 내부 운용 로직도 영향을 준다. 내부 LLM이나 다른 provider를 붙일 때는 다음을 별도로 검증한다.

- 자연어 요청에서 도구를 올바르게 선택하는가
- MCP 함수 정의를 이해하는가
- 코드 수정 후 테스트/검증 루프를 유지하는가
- 긴 컨텍스트에서 누락이나 환각이 증가하지 않는가

## 관련 맥락

- 스킬 보안은 [[claude-code-skills-plugins.md]].
- 권한과 settings 우선순위는 [[claude-code-setup.md]].

## 변경 이력

- 2026-04-16: Tips PDF 및 녹취 기반 최초 생성
