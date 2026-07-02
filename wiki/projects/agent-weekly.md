---
title: agent-weekly — Claude 대화 기록 기반 주간보고 자동작성 에이전트
domain: "ai-agent"
sensitivity: public
tags: [project, agent, weekly-report, claude-code, automation, gieok]
created: 2026-06-07
updated: 2026-06-07
sources:
  - "session-logs/20260607-095133-951f-*.md"
related:
  - "wiki/concepts/gieok.md"
  - "wiki/analyses/claude-code-session-jsonl-format.md"
---

# agent-weekly

## 개요

Claude 와 대화한 내용을 모두 기록해, 한 주간 진행한 내용을 **주간보고로 자동 작성**해 주는 에이전트 프로그램.
아이디어 구체화 단계의 신규 프로젝트로, [[gieok]] 을 참고 구현으로 삼는다.

## 컨셉

- 한 주간의 Claude Code 활동(대화·작업)을 수집 → 요약 → 주간보고 형식으로 산출
- 원천 데이터 후보: gieok 의 정제된 `session-logs/*.md`, 또는 CC 본체의 [[claude-code-session-jsonl-format|네이티브 jsonl]]

## gieok 에서 차용 가능한 구조 (조사 결과)

gieok 의 "Hook → 로그 → 스케줄 LLM → Wiki → Git" 파이프라인이 그대로 주간보고 흐름의 골격이 된다.

- **수집(L0)**: CC Hook 이벤트(`UserPromptSubmit`/`Stop`/`PostToolUse`/`SessionEnd`) → `session-logs/` Markdown 기록
  (`hooks/session-logger.mjs`, 외부 의존성 0·Node 18+ 내장 모듈만·에러여도 항상 exit 0 페일세이프)
- **마스킹**: `scripts/lib/masking.mjs` 로 시크릿/명령 블록리스트 처리 — 원본 jsonl 직접 사용보다 안전
- **스케줄 인제스트(L1)**: `scripts/auto-ingest.sh` 가 cron/LaunchAgent 로 미처리 로그를 `claude -p` 에 투입
  - 30분 소프트 타임아웃(`GIEOK_INGEST_MAX_SECONDS`)으로 LLM 호출 장시간 점유 방지
  - `.gieok-mcp.lock` 으로 cron × MCP 배타 (atomic `set -C` 생성 + mtime stale 감지, TTL 30분)
- 주간보고는 인제스트 대신 **주 1회 집계 LLM 호출**로 치환하면 됨 (스케줄·락·타임아웃 구조는 재사용)

## 현황

- 2026-06-07 세션: 아이디어 구체화 + gieok 구조/네이티브 jsonl 포맷 조사까지. 구현·아키텍처 확정은 미완.

## 변경 이력

- 2026-06-07: 프로젝트 신규 기록 (아이디어 구체화 세션)
