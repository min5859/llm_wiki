---
title: "장수 네트워크 클라이언트의 재연결 루프 갇힘 — 네트워크 OK 인데 응답 없음"
domain: both
sensitivity: public
tags: ["pattern", "diagnosis", "network", "telegram", "hermes", "launchd", "reconnect-loop"]
created: "2026-05-23"
updated: "2026-05-23"
sources:
  - "session-logs/20260523-000054-480c-hermes-가-응답이-없습니다.md"
confidence: medium
related:
  - "wiki/projects/hermes.md"
  - "wiki/concepts/hermes-agent.md"
  - "wiki/patterns/launchd-plist-symlink-from-project.md"
---

# 장수 네트워크 클라이언트의 재연결 루프 갇힘

launchd / systemd 로 24/7 돌리는 메시지 게이트웨이 (Telegram bot, Slack RTM, IRC, MQTT, WebSocket 등) 가 "응답이 없다" 라는 증상으로 떨어지는 흔한 실패 모드. **네트워크 자체는 지금 멀쩡한데 프로세스는 과거 끊김 시점부터 재연결 루프에 갇혀 있다.**

## 증상

- 봇/게이트웨이 명령에 반응이 없음
- `<service> gateway status` 는 "loaded" / "PID active" — 프로세스는 살아 있음
- 로그에는 `Connect attempt N/8 failed` `connect timed out` `Fallback IP X.X.X.X failed` 가 반복
- `ping <host>` / `curl <host>` 는 정상 (RTT 200ms 대, HTTP 200/302)
- `df`, 디스크, CPU, 메모리 모두 정상

즉, **외부에서 본 망 상태는 OK 인데 프로세스 내부의 reconnect state machine 만 멈춰 있다.**

## 원인 가설 (이번 케이스에서 좁힌 범위)

- **TCP 소켓의 stuck-syn / FIN_WAIT 잔여물** — 끊긴 시점에 OS 레벨 소켓이 좀비 상태로 남아 client 가 새 connect 를 못 하거나 잘못된 ip 캐시에 매달림
- **httpx / aiohttp / requests 의 connection pool 손상** — 라이브러리 내부 풀이 dead connection 을 살아 있다고 표시
- **DNS 캐시 stale** — 프로세스가 시작 시 resolve 한 IP 를 계속 사용. 대상이 IP 를 옮긴 후 새 IP 를 모름
- **백오프 max 도달** — 재시도 간격이 점점 길어져서 사실상 멈춤 (이번엔 retry 1s → 2s → 4s → 8s → 15s 로 누적)

이 증상을 봤을 때 라이브러리 별로 정확한 root cause 를 매번 잡기보다, **재시작이 가장 저렴한 진단 + 치료** 인 경우가 많다.

## 진단 체크리스트

봇이 죽었다고 보고받으면:

1. **프로세스 살아 있는지** — `launchctl list | grep <label>` / `ps -p <PID>`
2. **로그 tail** — 어떤 패턴이 반복되는지. `Connect attempt N/M`, `timed out`, `Fallback IP` 가 보이면 reconnect loop 의심
3. **외부 망 확인** — `ping <hostname>`, `curl -sS -o /dev/null -w "HTTP %{http_code} %{time_total}s\n" https://<host>/`, fallback IP 도 직접 ping
4. **다른 망 작업 확인** — `curl https://google.com/` 로 일반 인터넷 살아 있는지
5. **재시작** — `<service> gateway restart` 또는 `launchctl kickstart -k <label>`

망이 정상인데 프로세스만 못 따라잡고 있으면 (2 ↔ 3 의 모순), 재시작이 1차 조치.

## 재시작 후 검증 패턴

```bash
<service> gateway restart
sleep 8  # 프로세스가 connect 시도할 시간 확보
<service> gateway status         # PID 새로 잡혔는지
tail -n 20 <logs>/gateway.log    # "Connected" 같은 라인 찾기
tail -n 15 <logs>/gateway.error.log  # 새 에러 안 나오는지
```

`status` 만 보면 항상 "loaded" — `LastExitStatus` / PID 변경 / 로그 라인이 진짜 신호.

## 재시작이 안 풀어 줄 때

같은 패턴이 재시작 직후에도 반복되면 그건 진짜 망 / API 키 / rate-limit 문제:

- API 키 만료 / revoked
- 대상 host 가 region block / GFW 등으로 막힘 (`curl --resolve` 로 다른 IP 시도)
- 서비스 측 incident — official status page 확인
- 머신 시계 어긋남 → TLS handshake 실패 (`date`, NTP 확인)
- 라이브러리 버그 — `pip list | grep <lib>` 후 changelog 확인

## 자동화 가능성

재시작이 99% 의 케이스를 해결한다면 launchd / systemd 의 **watchdog 패턴** 으로 자동화 가능:

- 로그 라인 패턴 매칭 (`Connect attempt 8/8 failed`) → 외부 watcher 가 `launchctl kickstart -k <label>`
- 단, **자동 재시작은 본질 원인을 가린다** — 가시화 (Slack / Telegram self-notification 으로 "restart 했어요") 가 동반되어야 silent failure 가 되지 않는다.

이번 hermes 케이스는 아직 자동화 미적용 — 수동 진단 + 재시작.

## 사례 (2026-05-23, hermes)

```
PID 1655 (etime 24:09) 가 Telegram reconnect loop:
  Connect attempt 5/8 failed → 15s
  ERROR gateway.run: telegram connect timed out after 30s
  ERROR gateway.run: Gateway failed to connect any configured messaging platform
```

병행 확인:

- `ping api.telegram.org` → 0% loss, 228ms
- `curl https://api.telegram.org/` → HTTP 302, 0.9s
- `ping 149.154.167.220` (fallback IP) → 0% loss, 247ms
- `curl google.com` → HTTP 200

→ 망 OK, 프로세스만 갇힘. `hermes gateway restart` 로 1차 조치.

(주의: 이번 케이스에선 재시작 직후에도 같은 reconnect loop 가 한 번 더 잡혔음 — `LastExitStatus = 15`. 라이브러리/OS 레벨 stuck 가능성. 후속 조사 필요.)

## 변경 이력

- 2026-05-23: 최초 생성. hermes Telegram gateway 의 reconnect loop 진단 경험 기반 (출처: session-logs/20260523-000054-480c)
