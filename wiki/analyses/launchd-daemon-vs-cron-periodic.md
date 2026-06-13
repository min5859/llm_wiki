---
title: "상시 데몬은 launchd KeepAlive, 단발 주기 작업만 cron — macOS 실행 방식 선택 기준"
domain: "both"
sensitivity: "public"
tags: ["analysis", "launchd", "cron", "macos", "daemon", "keepalive", "scheduling", "decision-criteria"]
created: "2026-06-13"
updated: "2026-06-13"
sources:
  - "session-logs/20260613-164818-fc2f-신규-프로젝트를-시작하려고-하는데-지금-내가-이미-한국-투자-증권으로-API-사용해서-거래.md"
confidence: "high"
related:
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
  - "wiki/analyses/macos-launchagent-catchup-behavior.md"
  - "wiki/patterns/launchd-secret-management.md"
  - "wiki/patterns/cron-nvm-node-path-trap.md"
---

# 상시 데몬 vs 단발 주기 작업 — launchd / cron 선택 기준

macOS 에서 "주기적으로 뭔가 돌리고 싶다"가 곧 cron 은 아니다. **프로세스가 계속 떠 있어야 하는가(데몬), 아니면 정해진 시각에 잠깐 떴다 끝나는가(단발 배치)** 가 갈림길이다. 둘을 혼동하면 cron 에 데몬을 물려 중복 실행·좀비 프로세스가 생기거나, 반대로 단발 배치를 데몬으로 띄워 자원을 낭비한다.

## 판별 질문

> "이 프로그램은 *스스로 루프를 돌며 계속 살아 있어야* 하는가, 아니면 *한 번 실행되고 종료* 하는가?"

- **계속 살아 있어야 함** (웹서버, 폴링 루프, 워커, 메시지 구독, 내부에 자체 스케줄러 보유) → **launchd 상주 + KeepAlive**
- **한 번 실행되고 종료** (백업 스크립트, 리포트 생성, 일일 수집 잡) → **cron** 또는 launchd `StartCalendarInterval`

## 상시 데몬 → launchd KeepAlive 가 맞는 이유

자체 폴링 루프나 웹서버를 가진 프로세스는 "주기"가 프로그램 *내부*에 있다. 바깥 스케줄러(cron)가 또 주기를 잡으면:

- 이전 실행이 안 끝났는데 다음 주기가 또 띄움 → **중복 인스턴스**. 막으려면 lockfile/pidfile 기반 중복 실행 방지 래퍼 + 종료 스크립트가 별도로 필요해 더 번거롭다.
- 죽었을 때 다음 cron 주기까지 **공백**이 생긴다.

launchd 는 이 경우에 정확히 맞는다:

```xml
<key>RunAtLoad</key>      <true/>   <!-- 로그인/부팅 시 자동 시작 -->
<key>KeepAlive</key>
  <dict><key>SuccessfulExit</key><false/></dict>  <!-- 크래시(비정상 종료) 시에만 재시작 -->
<key>ThrottleInterval</key> <integer>60</integer>  <!-- 재시작 폭주 방지 -->
```

- `KeepAlive.SuccessfulExit=false`: 정상 종료(코드 0)는 그대로 두고, **크래시 때만** 자동 재시작.
- 장중/장외, 영업일/휴장 같은 **"언제 일하는가"는 프로그램이 스스로 판단**하게 둔다 (예: `is_market_open()` 으로 장외엔 폴링을 쉬어 API 낭비 방지). launchd 는 "살아 있게"만 책임지고, "지금 일할 때인가"는 앱 책임 — 관심사 분리.

## 단발 배치 → cron vs launchd 캘린더

단발 작업은 cron 으로도 충분하지만, 개인 Mac 처럼 **항상 켜져 있지 않은 환경**에서는 launchd 의 `StartCalendarInterval` 이 낫다. cron 은 예정 시각에 PC 가 꺼져 있으면 그 실행을 **그냥 스킵**하지만, launchd 는 켜질 때 놓친 작업을 **즉시 캐치업**한다 → [[macos-launchagent-catchup-behavior]]. 서버처럼 24/7 켜진 환경이면 cron 도 무방.

## 정리 표

| 상황 | 권장 | 이유 |
|---|---|---|
| 웹서버 / 폴링 루프 / 워커 (상주) | launchd + KeepAlive | 중복 실행·공백 없이 "살아 있게" 보장, 크래시 자동 복구 |
| 자체 내부 스케줄러 보유 데몬 | launchd + KeepAlive | 외부 주기와 이중 스케줄 충돌 제거 |
| 정해진 시각 단발 배치 (개인 Mac) | launchd StartCalendarInterval | 꺼져 있어 놓친 실행을 캐치업 |
| 정해진 시각 단발 배치 (상시 서버) | cron 또는 launchd | 둘 다 무방 |

## 부수 함정

- **포트 충돌**: macOS 는 포트 5000 을 AirPlay Receiver(Control Center)가 점유하는 경우가 많다. 로컬 웹 데몬은 5050 등으로 피하는 게 안전.
- **터미널 종속**: `&` 백그라운드로 띄우고 터미널을 닫으면 프로세스가 같이 죽는다. 상시 가동은 반드시 launchd(또는 `nohup`/`disown`)로.
- macOS 는 cron 을 사실상 레거시로 취급(Apple 도 launchd 권장). PATH·환경변수도 cron 에선 자주 비어 사고가 난다 → [[cron-nvm-node-path-trap]]. 환경 시크릿 주입은 [[launchd-secret-management]].

## 변경 이력

- 2026-06-13: 최초 생성 (출처: session-log 20260613-164818-fc2f, ht_dde Flask+폴링 데몬을 cron 대신 launchd KeepAlive 로 운영 결정에서 일반화).
