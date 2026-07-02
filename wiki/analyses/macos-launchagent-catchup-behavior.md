---
domain: "ai-agent"
sensitivity: "public"
title: macOS LaunchAgent — 미실행 작업 캐치업 동작
tags: [analysis, macos, launchagent, launchd, scheduling]
created: 2026-04-22
updated: 2026-04-22
source_session: 20260422-002046-60a1-왜-7-00-인가요--매일-오전-7-00-에-동작하는-건가요.md
---

## 개요

macOS의 launchd(LaunchAgent)는 예정된 실행 시간에 PC가 꺼져 있던 경우,
**PC 부팅 직후 해당 작업을 즉시 실행(캐치업)**한다.

cron과 다르게, PC를 항상 켜두지 않아도 하루에 한 번이라도 켜면 밀린 작업이 자동으로 처리된다.

## 동작 예시

```
07:00  — PC 꺼져 있음 → 실행 스킵
10:00  — PC 켬 → launchd가 "07:00 놓쳤다" 인식하고 즉시 실행 ✅
13:00  — PC 켜져 있으면 정시 실행 ✅
19:00  — PC 꺼져 있으면 다음 켤 때 캐치업 ✅
```

## cron과의 차이

| | cron | LaunchAgent |
|---|---|---|
| 놓친 실행 | 그냥 스킵 | **즉시 캐치업** |
| PC 꺼져 있을 때 | 스킵됨 | PC 켜질 때 실행 |
| 설정 파일 | crontab | plist |
| 사용 대상 | 서버 등 상시 실행 환경 | 개인 Mac (간헐적 온/오프) |

## 실제 활용

개인 Mac에서 하루 N회 정기 배치 작업을 설정할 때는 LaunchAgent가 적합하다.
PC를 항상 켜두지 않아도 "오늘 중에 한 번은 반드시 실행된다"는 보장을 얻을 수 있다.

예: gieok의 auto-ingest.sh (07:00, 13:00, 19:00)는 이 동작을 활용해
하루에 여러 번 켜고 끄는 일반 사용 패턴에서도 Wiki 업데이트가 누락되지 않도록 설계되어 있다.

## 관련 페이지
- [[gieok-project]]
