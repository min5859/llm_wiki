---
title: "Personal AI agent 의 메신저 채널 비교 — Telegram 이 사실상 표준이 된 이유"
domain: personal
sensitivity: public
tags: ["analysis", "ai-agent", "telegram", "discord", "slack", "messaging", "bot", "personal-assistant"]
created: 2026-05-02
updated: 2026-05-02
source_session: "20260502-092045-628d-hermes-라는-opensource-agent-를-설치하려고하는데-조사좀-해-주세요.md"
confidence: medium
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/openclaw-telegram-group-setup.md"
---

# Personal AI agent 의 메신저 채널 비교

## 개요

Hermes / OpenClaw 같은 self-hosted personal AI agent 를 셋업할 때 마주치는 결정점: **「어느 메신저로 봇을 노출할 것인가?」**. 커뮤니티 관찰상 **Telegram 이 압도적인 사실상의 표준** 이며, 다른 채널은 특정 사용 패턴에서만 우위를 갖는다. 이 페이지는 그 트레이드오프를 정리한다.

(같은 부류의 도구 — Hermes, OpenClaw, n8n bot, langchain-bot 보일러플레이트 — 모두 첫 번째 예시로 Telegram 을 든다)

## 채널별 위치 짓기

| 순위 | 채널 | 비중 (체감) | 주 사용층 |
|---|---|---|---|
| 🥇 | **Telegram** | 압도적 1위 | 개인 사용자 거의 전부 |
| 🥈 | Discord | 중간 | 팀 / 커뮤니티 공유 |
| 🥉 | Slack | 중간 | 회사 / 업무 환경 |
| 그 외 | WhatsApp / Signal / iMessage / Matrix | 소수 | 지역·취향별 |

## 왜 Telegram 이 압도적인가

1. **봇 생성이 가장 쉬움** — `@BotFather` 한 명에게 메시지 보내면 30초 안에 토큰 발급. 다른 플랫폼처럼 워크스페이스, OAuth 앱 등록, 도메인 인증 같은 거 일체 없음
2. **개인 채팅에 바로 사용 가능** — Slack / Discord 처럼 「워크스페이스 / 서버」 개념 없이 1:1 DM 으로 그냥 쓸 수 있음. **「내 개인 비서」 컨셉에 가장 맞음**
3. **음성 메시지** — Telegram 음성 메모를 보내면 agent 가 자동 transcribe → 처리. 운전 중·산책 중 사용 친화적
4. **그룹 채팅 지원** — 가족 / 팀 단톡방에 봇으로 넣으면 공유 비서 (단 그룹 모드는 Privacy Mode 등 별도 설정 필요 → [[openclaw-telegram-group-setup]])
5. **API 가 안정적이고 무료**, 봇 관련 정책도 관대
6. **모바일 우선** — 이런 부류 agent 의 핵심 use case 가 「VPS 에 떠있는 agent 한테 외출 중에도 시킨다」 인데, 이게 Telegram 과 가장 잘 맞음

## 시나리오별 추천

| 사용 패턴 | 추천 채널 |
|---|---|
| 혼자 쓰는 개인 비서 (외출 / 이동 중에도) | **Telegram** ✅ |
| 가족 / 스터디 그룹과 공유 | **Telegram 그룹** + Privacy Mode 해제 |
| 팀 협업·업무 자동화 | **Slack** (회사가 이미 Slack 쓰면) |
| 게이밍 / 오픈소스 커뮤니티 봇 | **Discord** |
| iPhone 사용자, 메신저 안 바꾸고 싶음 | **iMessage (BlueBubbles)** — 단 Mac 필요 |
| 프라이버시 최우선 | **Signal** |
| 일단 책상에서만 | CLI 만 (gateway 안 켜기) |

## 한국 사용자 입장의 함정

- **카톡 / 라인은 봇 API 가 자유롭지 않음** — Hermes / OpenClaw / 대다수 OSS agent 의 공식 지원 채널이 아니다
- 한국에서 카톡이 익숙하지만, 이 환경에서 가장 매끄러운 선택지는 결국 **Telegram**. 봇 토큰 받는 데 한국 번호 인증 같은 장벽도 없음
- 카톡 봇이 필요하면 별도 비공식 게이트웨이 (사용자 개인의 카톡 계정 → IMAP / 웹 자동화 등) 가 필요해 안정성이 떨어짐

## 셋업 흐름 (Telegram 기준)

대부분의 self-hosted agent 가 동일한 흐름이다:

1. 텔레그램에서 `@BotFather` → `/newbot` → 봇 토큰 발급
2. `@userinfobot` 한테 메시지 → 본인 numeric user ID 받기 (개인 비서 용 — 다른 사람이 봇한테 말 걸 수 없게 화이트리스트)
3. agent 의 gateway setup 명령으로 토큰 / ID 입력
4. gateway 데몬 기동
5. (그룹 사용 시) BotFather 의 `/setprivacy` → Disable, 봇을 그룹에서 추방 → 재초대

그룹 사용 시 Privacy Mode 와 agent 측 `requireMention` 설정의 상호작용이 함정이 된다 — [[openclaw-telegram-group-setup]] 의 케이스가 그대로 일반화된다.

## 채널 1개로 충분한가

- **CLI + Telegram 1개로 90% 커버됨**. 처음에는 이 조합으로 시작
- Telegram → 외부 (모바일 / 외출 중) 호출
- CLI → 책상 앞에서 디버깅 / 무거운 작업
- Discord / Slack 은 **공유 시나리오가 명확해질 때** 추가. 처음부터 다 켜면 게이트웨이 운영 부하만 늘어남

## 결론

- 셋업 0순위는 **Telegram**. 개인 비서로 쓸 거라면 거의 100% 정답
- 그룹 사용 시 Privacy Mode 와 agent 측 `requireMention` 의 2계층 설정이 필수 — 어느 한쪽만 끄면 동작 안 함
- 한국 카톡은 OSS agent 표준 경로가 아님 → Telegram 으로 우회

## 관련 페이지

- [[hermes-agent]] — 본 비교가 도출된 사례
- [[openclaw-telegram-group-setup]] — Telegram 그룹 봇의 Privacy Mode / requireMention / topic 라우팅 트러블슈팅

## 변경 이력

- 2026-05-02: 최초 생성. Hermes Agent 조사 중 사용자가 "보통 hermes 사용자들은 어떤 채팅앱을 많이 사용하나요?" 라고 물어 정리한 비교 (출처: session-logs/20260502-092045-628d-*)
