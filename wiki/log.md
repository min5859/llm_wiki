---
title: Operation Log
updated:
---

# Operation Log

## 2026-04-22 — wiki-ingest (session-log)

- Project: gieok
- Source: session-logs/20260422-002046-60a1-왜-7-00-인가요--매일-오전-7-00-에-동작하는-건가요.md
- Created:
  - wiki/concepts/gieok.md — gieok 개념 (4계층 아키텍처, Hook 등록 방식, gieok vs Obsidian)
  - wiki/projects/gieok.md — gieok 프로젝트 상세 (컴포넌트, LaunchAgent 스케줄, LLM 필요 여부, 버그 이력)
  - wiki/analyses/macos-launchagent-catchup-behavior.md — macOS LaunchAgent 캐치업 동작 분석
- Updated: wiki/index.md, wiki/log.md
- Skipped: 탐색적 QA 대화, 설치 과정 시행착오 (결론만 추출)

## 2026-04-23 — wiki-ingest (session-logs, ingested: false 1건)

- Source: session-logs/20260422-230939-22f1-스코어링-점수를-65-점에서-60-점으로-조정했는지-확인해-주세요.md
  - Project: ht_trading
  - Created: wiki/projects/ht-trading.md
    — ScoringStrategy 이중 스케일 구조(백테스트 40pt / 라이브 100pt), config 2파일 동기화 규칙, 임계값 65→60 조정 이력
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 1개 session-log 파일

## 2026-04-22T08:30 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260422-080031-e9df-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260422-080037-7719-*.md (arXiv: 2604.18168)
  - Created: wiki/analyses/meanflow-text-to-image.md
    — MeanFlow 텍스트 조건부 확장 (EMF), discriminability·disentanglement 분석
- Source: session-logs/20260422-080140-da77-*.md (arXiv: 2604.16044)
  - Created: wiki/analyses/diffusion-snr-t-bias.md
    — DPM의 SNR-t 편향 규명 및 웨이블릿 도메인 training-free 보정법
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체
