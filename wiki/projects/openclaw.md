---
title: "OpenClaw — AI 에이전트 자동화 도구"
domain: "personal"
sensitivity: "public"
tags: ["project", "openclaw", "ai-agent", "telegram", "automation", "npm"]
created: "2026-04-23"
updated: "2026-04-23"
sources:
  - "session-logs/20260423-113736-72aa-openclaw-를-업데이트-하려고-합니다.-가이드를-알려주세요.md"
confidence: "high"
related:
  - "wiki/projects/gieok.md"
---

# OpenClaw — AI 에이전트 자동화 도구

Telegram 등 채널과 연동하는 AI 에이전트 자동화 도구. npm으로 전역 설치. 현재 사용 중인 AI 프로바이더: `openai-codex/gpt-5.4`.

## 설치 및 업데이트

```bash
# 업데이트
npm install -g openclaw@latest

# 버전 확인
openclaw --version

# 서비스 재시작 (핵심 명령)
openclaw daemon restart
```

> **주의**: `openclaw start`가 아닌 `openclaw daemon restart`가 LaunchAgent를 올바르게 재시작하는 명령이다.

## 설정 파일 구조 (`~/.openclaw/`)

| 파일/디렉터리 | 역할 | git 관리 |
|-------------|------|----------|
| `openclaw.json` | 메인 설정 (auth, 모델, 채널 등) | ✅ 로컬 private repo |
| `agents/*/models.json` | 에이전트 모델 설정 | ✅ |
| `cron/jobs.json` | 크론 작업 설정 | ✅ |
| `devices/paired.json` | 페어링된 기기 | ✅ |
| `identity/device.json` | 기기 정보 | ✅ |
| `workspace/` | 메인 에이전트 페르소나 (SOUL.md 등) | 별도 git (OpenClaw 자체 관리) |
| `workspace-english/` | 영어 튜터 에이전트 | 별도 git (OpenClaw 자체 관리) |
| `memory/*.sqlite` | 벡터 메모리 DB | ❌ (바이너리, 자주 변경) |
| `logs/`, `cron/runs/`, `media/` | 런타임 아티팩트 | ❌ |

### git 관리 설정

`~/.openclaw`를 로컬 private git으로 관리 중:

```bash
# 설정 변경 후 커밋
cd ~/.openclaw && git add -A && git commit -m "설명"
```

## 이미지 생성 기능

```bash
openclaw capability image generate --model <provider/model> --prompt "..." --output ~/out.png
```

### 지원 프로바이더

| 프로바이더 | 설정 필요 | 비고 |
|-----------|----------|------|
| openai | OpenAI API Key | gpt-image-2 기본; Codex OAuth와 별도 키 |
| google | Google API Key | Gemini image |
| fal | fal API Key | Flux 기반 |
| minimax | MiniMax API Key | |
| comfy | ComfyUI 로컬 설정 | |

> **중요**: 텍스트(gpt-5.4)는 OpenAI Codex OAuth로 인증하지만, 이미지 생성(`gpt-image-1/2`)은 **별도의 `OPENAI_API_KEY`** 가 필요하다. 두 인증 체계가 분리되어 있다.

## 상태 확인

```bash
openclaw status           # 전체 상태 조회
openclaw daemon status    # LaunchAgent 상태
```

## 버전 이력

| 날짜 | 버전 | 비고 |
|------|------|------|
| 2026-04-23 | 2026.4.21 | npm install -g openclaw@latest |
| 이전 | 2026.3.28 | — |

## LaunchAgent

Gateway 서비스는 macOS LaunchAgent로 실행된다:
- 로그인 시 자동 시작
- 포트: 18789 (localhost loopback)

## 변경 이력

- 2026-04-23: 최초 작성 (세션 로그 20260423-113736-72aa에서 추출)
