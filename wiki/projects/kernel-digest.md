---
title: "kernel-digest — 리눅스 커널 일일 다이제스트 (계획 단계)"
domain: "personal"
sensitivity: "public"
tags: ["project", "kernel-digest", "linux", "newsletter", "claude-code", "openclaw", "ai-agent"]
created: "2026-05-07"
updated: "2026-05-07"
sources:
  - "session-logs/20260507-004906-1f45-⚙️-Source-channel-delivery-is-private-by-default-f.md"
  - "session-logs/20260507-015508-eb0c-⚙️-Source-channel-delivery-is-private-by-default-f.md"
confidence: "medium"
related:
  - "wiki/projects/openclaw.md"
---

# kernel-digest — 리눅스 커널 일일 다이제스트 (계획 단계)

리눅스 커널 개발 동향 (메인라인 패치 / 향후 로드맵 / 메이저 버전 발전사 / 커뮤니티) 을 매일 자동 수집·요약해 웹에서 팀이 함께 보는 뉴스레터 서비스. 향후 안드로이드·AI 등 다른 토픽으로 확장 가능한 **토픽-플러그인 구조**.

현재 상태: **M0 (요구사항 정의)** — `kernel-digest/AGENTS.md` 작성 완료, M1 진입 전 미결정 7건 대기 중.

## 위치

- 작업 디렉터리: `~/.openclaw/workspace/kernel-digest/`
- AGENTS.md: `~/.openclaw/workspace/kernel-digest/AGENTS.md` (13개 섹션, 약 230줄)

## 핵심 요구사항 (R1~R7)

| ID | 요구사항 |
|----|----------|
| R1 | 매일 정기 실행 (기본 09:00 KST, 부분 실패 허용) |
| R2 | 4축 콘텐츠: 메인라인 패치 동향 / 향후 로드맵·토론 / 버전 히스토리 인사이트 / 커뮤니티 |
| R3 | AI 요약 — 알기 쉽고 인사이트 있는 한국어 요약 + 원문 링크 |
| R4 | 웹 블로그 형태로 팀과 공유 가능 |
| R5 | **종량제 API 금지** — 구독제 LLM (`claude -p`, `openclaw`) 만 사용 (월 비용 0원 목표) |
| R6 | 토픽 플러그인 — 향후 안드로이드 / AI 등으로 확장 |
| R7 | 라이선스 / 저작권 존중 (출처 링크 + 발췌 비율 제한) |

## 데이터 소스 (8종 1차 후보)

- `git.kernel.org` (메인라인 / -next / -rc 트리)
- LKML / lore.kernel.org (메일링 리스트)
- LWN.net (정리된 분석 — 유료 구독 보유 여부 미결정)
- KernelNewbies (버전 발전사 / 친절한 정리)
- Phoronix (벤치 / 변경점 코멘트)
- Patchwork (제안 패치 트래킹)
- kernel.org 공지
- 컨퍼런스 (LSF/MM, Plumbers 등)

## 시스템 아키텍처 (4단계 파이프라인)

```
Collectors → Raw Store → AI Stage → Publisher → Web
```

| 단계 | 역할 |
|------|------|
| Collectors | 8종 소스에서 구조화된 raw 데이터 수집 (cron / 워커) |
| Raw Store | 원본 보관 (재처리 가능) + 메타데이터 |
| AI Stage | 구독제 LLM 으로 한국어 요약 + 인사이트 생성 |
| Publisher | 정적 사이트 빌드 |
| Web | GitHub Pages / Netlify / 사내 호스팅 |

## AI 통합 규칙 (정책)

- **`ANTHROPIC_API_KEY` 사용 금지** — 종량제 우회 차단
- 허용: `claude -p` (Pro 구독) / `openclaw` (기존 구독 인프라 경유)
- LLM 라우팅 정책 (`claude -p` 단일 vs `openclaw` 혼용) 은 미결정

## 마일스톤

| 마일스톤 | 내용 | 상태 |
|---------|------|------|
| M0 | 요구사항 정의 (AGENTS.md) | ✅ 완료 |
| M1 | 저장소 스캐폴딩 (`kdigest --help` 동작까지) | 결정 7건 대기로 보류 |
| M2 | Collectors (1차 데이터 소스 2~3개) |  |
| M3 | AI Stage (요약 / 인사이트) |  |
| M4 | Publisher / Web |  |
| M5 | 토픽 플러그인 추상화 (안드로이드 등) |  |
| M6 | 운영 안정화 (모니터링 / 롤백 / 부분 실패 허용) |  |

## M1 진입 전 미결정 7건

| # | 항목 | 기본 제안 |
|---|---|---|
| 1 | 정적 사이트 도구 | MkDocs Material / Astro / 11ty |
| 2 | 호스팅 | GitHub Pages / Netlify / 사내 |
| 3 | 저장소 구조 | 모노레포 vs 분리 |
| 4 | 일일 발행 시각 | 09:00 KST |
| 5 | LWN 유료 구독 보유 여부 | — |
| 6 | 공유 URL 공개 범위 | 공개 / 사내 IP / 패스워드 |
| 7 | LLM 라우팅 정책 | `claude -p` 단일 vs `openclaw` 혼용 |

기본값 제안 묶음: `MkDocs Material + GitHub Pages + 모노레포 + 09:00 KST + claude -p 단일`. 사용자가 "기본값으로 진행" 지시 시 M1 즉시 시작.

## 에이전트 작업 규칙 (10개)

AGENTS.md 본문에 정의 — plan-first, 단순함 우선, 종량제 금지 등. 추측 코드 작성 금지 (M0 결정 후에 M1 진입).

## 변경 이력

- 2026-05-07: 최초 작성. M0 요구사항 정의 단계 (세션 로그 20260507-004906-1f45 와 동일 요구사항으로 두 번째 세션 20260507-015508-eb0c 가 진행돼 기존 AGENTS.md 가 충실히 반영돼 있음을 재확인)
