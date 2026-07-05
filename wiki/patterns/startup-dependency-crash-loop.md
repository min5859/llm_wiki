---
title: "기동 시 외부 의존성 강체크 + 자동 재시작 = 크래시 루프"
domain: "trading"
sensitivity: "public"
tags: ["pattern", "trading", "launchd", "keepalive", "crash-loop", "startup", "resilience"]
created: "2026-07-05"
updated: "2026-07-05"
sources:
  - "session-logs/20260704-130158-2dad-현재-프로젝트에서-개선할-부분이-있을지-검토해줘.md"
confidence: high
related:
  - "wiki/projects/ht-trading.md"
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
---

# 기동 시 외부 의존성 강체크 + 자동 재시작 = 크래시 루프

자동 재시작 supervisor(launchd KeepAlive / systemd Restart) 아래에서 도는 프로세스가 **기동 시퀀스에서 외부 의존성을 강하게 체크(실패 시 exit)** 하면, 의존성이 내려간 시간대에 크래시 루프 + 알림 폭풍이 된다. ht_trading 라이브 배포 중 실사고로 발견 (2026-07-04, commit 60a32ec).

## 증상 → 근본 원인

- **증상**: 주말 밤 배포 직후 프로세스가 60초마다 재기동을 반복하며 텔레그램 알림 폭주.
- **근본 원인 체인**: 기동 시퀀스(`_load_strategies → get_positions`)가 KIS 잔고를 조회 → 주말/점검 시간엔 KIS 서버가 접속 자체를 거부(ConnectionError) → 크래시 가드가 exit 1 → launchd KeepAlive 가 60초 후 재기동 → 무한 반복.
- **은폐 요인**: 기존 장기 프로세스는 (장중에) 한 번만 기동해 이 경로를 밟지 않았다. **장기 가동 프로세스는 재시작 경로를 거의 안 밟으므로 기동부 결함이 오래 은폐된다** — 배포·재부팅·의존성 점검 시간대에만 드러남.

## 해법 — startup 오류의 분류 처리

기동 시퀀스를 재시도 루프로 감싸되, 오류를 두 부류로 나눈다:

| 부류 | 예 | 처리 |
|------|----|------|
| 연결성 오류 | OSError 계열, requests 예외 (서버 다운·점검·네트워크) | **5분 대기 후 재시도** (프로세스 유지) |
| 프로그램 결함 | 설정 오류, 코드 버그 | 크래시 가드로 전파 (exit → 사람이 인지) |

대기 중에도 1초마다 `_running` 플래그를 확인해 **SIGTERM 응답성 유지** (5분 sleep 통짜 금지).

## 일반 원칙

**startup 오류 처리와 steady-state 오류 처리는 비대칭으로 설계한다.** 가동 중 API 오류는 재시도·스킵으로 흡수하면서 기동 시에만 강체크로 죽는 구성은, supervisor 와 결합해 "의존성 다운 = 크래시 루프"를 만든다. 기동 시에도 연결성 오류는 대기-재시도가 기본값이어야 한다.

## 변경 이력

- 2026-07-05: 최초 생성 — ht_trading 주말 배포 크래시 루프 실사고에서 일반화 (출처: session-logs/20260704-130158-2dad-*)
