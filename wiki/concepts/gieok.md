---
title: gieok — Claude Code 세컨드 브레인
tags: [concept, gieok, claude-code, knowledge-management, automation]
created: 2026-04-22
updated: 2026-04-22
sources: 1
---

## 개요

gieok은 Claude Code의 세션 간 기억 상실 문제를 해결하는 자동 지식 축적 시스템이다.
Claude Code는 세션이 끝나면 모든 대화를 잊는다. gieok은 CC Hook 이벤트를 가로채
대화를 자동으로 Obsidian Wiki에 축적하고, 다음 세션 시작 시 관련 지식을 Claude에게 다시 주입한다.

**한 줄 정의**: "Claude Code를 위한 세컨드 브레인 — Hook → LLM → Wiki → Git 파이프라인"

## 핵심 파이프라인

```
대화 발생
  ↓ (CC Hook 자동 캡처)
session-logs/ 저장
  ↓ (LLM 처리, auto-ingest.sh)
wiki/ 페이지 생성 ([[wikilink]] 포함)
  ↓ (다음 세션 시작 시)
wiki/index.md → Claude 시스템 프롬프트에 자동 주입
  ↓
Claude가 관련 페이지를 Read 도구로 읽고 작업 시작
```

## 아키텍처 (4계층)

| 레이어 | 역할 | LLM 필요 |
|--------|------|----------|
| **L0** 자동 캡처 | CC Hook으로 세션 I/O 기록 | ❌ |
| **L1** 구조화 | 로그 → Wiki 페이지 변환 | ✅ |
| **L2** 무결성 | 월간 Wiki 점검 + 비밀 정보 탐지 | ✅ |
| **L3** 동기화 | GitHub Private repo로 멀티 머신 동기화 | ❌ |

LLM이 필요한 기능(`claude -p`)은 별도 API 비용 없이 설치된 Claude Code Max 플랜을 재활용한다.

## gieok vs Obsidian

| | Obsidian | gieok |
|---|---|---|
| 성격 | **앱** (GUI, 그래프 뷰) | **백그라운드 자동화 도구** |
| 화면 | O | **없음** |
| 역할 | 노트를 사람이 보고 편집 | Claude 대화를 자동으로 노트로 변환 |

gieok은 콘텐츠를 자동 생성하는 파이프라인이고, Obsidian은 그 결과물을 보는 뷰어다.
이 둘은 독립적으로 존재하며, gieok 없이도 Obsidian을 쓸 수 있고 그 반대도 마찬가지다.

## 설치 아키텍처 (Tool vs Vault 분리)

```
~/.local/share/gieok/repo/   ← gieok 도구 (scripts, hooks, MCP 서버)
         ↓ 바라본다
<OBSIDIAN_VAULT>/            ← Vault (데이터, 독립적으로 존재)
```

설치는 Vault 자체를 변경하는 것이 아니라, gieok 도구가 Vault를 **바라보게 연결**하는 것이다.
Vault의 기존 내용은 보존되고 `wiki/`, `session-logs/`, `templates/` 디렉터리만 추가된다.

## Hook 등록 범위

`~/.claude/settings.json`(전역)에 등록되므로, **어느 프로젝트에서 나눈 대화든 전부 캡처**된다.
특정 프로젝트에서만 동작하도록 제한하는 기능은 기본 제공되지 않는다.

## wiki 목차 주입 방식

세션 시작 시(`SessionStart` Hook) `wiki/index.md`만 시스템 프롬프트에 주입된다.
세션 로그를 직접 불러오는 것이 아니라, 목차를 보고 Claude가 **스스로 판단**해서
필요한 페이지만 Read 도구로 읽는다.

## 관련 페이지
- [[gieok-project]]

