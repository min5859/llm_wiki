---
title: "everything-claude-code (ECC) — Claude Code 하네스 성능 시스템"
domain: both
sensitivity: public
tags: ["claude-code", "agent-harness", "plugin", "skills", "agentshield", "oss"]
created: 2026-04-30
updated: 2026-04-30
sources:
  - "session-logs/20260430-144247-00cb-*.md"
confidence: medium
related:
  - "wiki/concepts/claude-code-overview.md"
  - "wiki/concepts/claude-code-skills-plugins.md"
  - "wiki/patterns/claude-code-setup.md"
  - "wiki/patterns/claude-md-guide.md"
---

# everything-claude-code (ECC)

`affaan-m/everything-claude-code` — Claude Code · Codex · Cursor 등 AI 에이전트 하네스의 성능을 극대화하는 종합 최적화 시스템. Anthropic 해커톤 우승작이며 2026-04-30 기준 약 170,387 stars.

## 핵심 내용

### 한 줄 요약

단순 설정 모음이 아니라 "에이전트 하네스 자체를 성능 시스템으로 다룬" 메타 플러그인. v2.0.0-rc.1 (2026-04) 부터는 Rust 컨트롤 플레인 (`ecc2/`) 까지 in-tree 로 진행 중.

### 주요 구성 요소

- **48개 전문 에이전트**: planner, architect, code-reviewer, security-reviewer 등 언어별·역할별 세분화된 서브에이전트 위임 구조.
- **182개 Skills**: TDD 워크플로우, 보안 검토, 지속적 학습 (instinct 기반), 멀티 플랫폼 패턴 등 도메인별 워크플로우 정의.
- **AgentShield 보안 스캐너**: 1,282개 테스트, 102개 정적 분석 룰. CLAUDE.md, MCP 설정, hooks, 에이전트 정의를 취약점 탐지. CI 파이프라인에서 `npx ecc-agentshield scan` 으로 자동화 가능.
- **지속적 학습 v2 (Continuous Learning)**: 세션에서 패턴을 자동 추출하여 instinct로 저장하고 신뢰도 점수 기반으로 skill 로 진화.
- **크로스 하네스 지원**: Claude Code, Codex, Cursor, OpenCode, Gemini 에 걸쳐 동일 설정으로 동작 (12개 언어 생태계 커버).
- **68개 legacy command shims**, 다중 IDE 통합 (Antigravity 포함).

### 기술 스택

| 계층 | 선택 |
|---|---|
| 메인 언어 | JavaScript / TypeScript |
| 보조 | Shell, Python, Go, Java, Perl, Rust |
| 런타임 | Node.js (크로스 플랫폼 hooks/scripts), Tkinter (Dashboard GUI), SQLite (상태 저장소) |
| 패키징 | npm (`ecc-universal`, `ecc-agentshield`), Claude Code Plugin 시스템, GitHub App |
| 아키텍처 | Plugin manifest 기반 selective install, hooks.json 트리거 자동화, 언어별 rules 디렉터리 분리, ECC 2.0 alpha 의 Rust 컨트롤 플레인 (`ecc2/`) 프로토타입 |

### 설치

플러그인 마켓플레이스를 통한 가장 단순한 경로:

```bash
# Claude Code 내부에서
/plugin marketplace add affaan-m/everything-claude-code
```

또는 `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "ecc": {
      "source": {
        "source": "github",
        "repo": "affaan-m/everything-claude-code"
      }
    }
  },
  "enabledPlugins": {
    "everything-claude-code@everything-claude-code": true
  }
}
```

> Claude Code 의 플러그인 시스템은 `rules/` 의 자동 배포를 지원하지 않으므로, rules 는 git clone 후 수동으로 `~/.claude/rules/` 에 복사해야 한다. 공식 문서가 이를 명시.

### v2.0.0-rc.1 핵심 변경 (2026-04)

- **Dashboard GUI** — Tkinter 기반 데스크탑 앱 (`ecc_dashboard.py` 또는 `npm run dashboard`). 다크/라이트 토글, 폰트 커스터마이즈.
- **공개 surface 동기화** — 메타데이터·카탈로그·플러그인 매니페스트가 실 OSS 표면 (48 agents / 182 skills / 68 legacy shims) 과 일치.
- **Operator/outbound 워크플로우 확장** — `brand-voice`, `social-graph-ranker`, `connections-optimizer`, `customer-billing-ops`, `google-workspace-ops` 등.
- **Media tooling** — `manim-video`, `remotion-video-creation`, social publishing.
- **ECC 2.0 alpha** — Rust 컨트롤 플레인 (`ecc2/`) 이 in-tree 로 빌드 가능. `dashboard / start / sessions / status / stop / resume / daemon` 명령 노출.
- **Ecosystem hardening** — AgentShield 강화, ECC Tools 비용 컨트롤, 빌링 포털.

### 실용성 평가

즉시 활용 가능하나 **설치 복잡도** 주의. 자주 마주치는 함정:

| 함정 | 결과 | 대응 |
|---|---|---|
| Plugin 경로 + 수동 설치 혼용 | 동일 명령 중복 동작 | 한쪽만 사용 (공식 경고) |
| `rules/` 자동 배포 기대 | 적용 안 됨 | 수동 복사 |
| Claude Code CLI 버전 | 일부 기능 미동작 | **v2.1.0 이상** 필수 |
| `multi-*` 명령 사용 | 미동작 | 별도 `ccg-workflow` 런타임 설치 필요 |

처음 도입 시 권장 최소 구성: **plugin 경로 단독 사용 + `rules/common` + 사용 언어 팩 하나만 복사**.

## 관련 맥락

- [[claude-code-skills-plugins]] 의 공식 플러그인/스킬 모델 위에서, ECC는 "한 사람이 운영하는 거대 marketplace" 형태로 본인 워크플로우를 패키징한 형태.
- [[claude-md-guide]] 의 4계층 강제력 / 7가지 작성 테크닉 패턴이 ECC 의 rules 디렉터리 설계에도 반영되어 있다고 추정 (ECC 의 `rules/common`, `rules/typescript` 등 언어별 폴더 분리 = CLAUDE.md 의 "Project rules" 계층).
- AgentShield 의 102개 정적 분석 룰은 [[claude-code-enterprise-security-bedrock]] 에서 다룬 기업 도입 시 데이터·계정·네트워크·권한·감사·비용 6대 결정 사항 중 "감사" 자동화 지점으로 활용 가능.

## 변경 이력

- 2026-04-30: 최초 생성. oss-radar 자동 분석 결과 기반 (출처: session-logs/20260430-144247-00cb-*)
