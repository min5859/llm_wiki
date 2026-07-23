---
title: "뉴스레터 research 파이프라인의 anti-bot 차단 — 1급 실패 모드로 설계되지 않은 소스 접근 장애"
domain: "ai-agent"
sensitivity: "internal"
tags: ["dev-blog", "anubis", "cloudflare", "anti-bot", "research-dossier", "pipeline-failure", "newsletter"]
created: "2026-07-24"
updated: "2026-07-24"
sources:
  - "session-logs/20260724-030015-a3a0-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260724-040043-5ae1-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260724-033656-7388-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md"
  - "session-logs/20260703-030007-8175-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
confidence: "high"
related:
  - "wiki/analyses/research-write-agent-separation.md"
  - "wiki/projects/dev-blog.md"
---

# 뉴스레터 research 파이프라인의 anti-bot 차단 — 1급 실패 모드로 설계되지 않은 소스 접근 장애

dev-blog 뉴스레터 파이프라인의 research(dossier) 단계가 외부 소스의 anti-bot 방어에 막히는 사건이 2026-07-03·2026-07-24 두 차례 관측됐다. 개별 URL 차단은 confidence 강등·openQuestions 격리로 흡수되지만, **소스가 전면 차단된 렌즈는 dossier 자체가 산출되지 않아 해당 회차 newsletter 가 조용히 결손**된다. 파이프라인 레벨의 자동 재시도·폴백·결손 감지 장치는 없다.

## 증상

2026-07-24 03:00~04:33 cron 사이클에서 세 가지 독립된 차단 사건이 관측됨:

1. **Linux Daily dossier** (`030015`) — `kernel.org`/`lore.kernel.org` 전체가 Anubis 봇 챌린지로 WebFetch 실패. 에이전트가 스스로 "All the kernel.org and lore.kernel.org URLs are blocked by Anubis (bot protection) — WebFetch can't read them directly. Let me try WebSearch to corroborate" 로 우회 시도. 일부 항목은 confidence 강등·openQuestions 증가로 산출은 완료.
2. **Linux Kernel Lens dossier — `lore-stable-new` 렌즈** (`040043`) — `lore.kernel.org/stable/*` 접근이 전부 `403 Forbidden`. 응답 본문에 `"Making sure you're not a bot!"` (Anubis 챌린지 페이지) 확인. `curl/8.4.0`·`Mozilla/5.0`·`git/2.43.0`·`Wget/1.21`·`Googlebot/2.1` 등 여러 User-Agent 로 재시도했으나 전부 403 — **UA 스푸핑이 무효화됨**. 세션은 `assistant_turns: 0` 으로 종료 — dossier JSON 을 끝내 산출하지 못함.
3. **AI Coding Agents dossier** (`033656`) — `x.com` 트윗 URL이 `HTTP 402 Payment Required` 로 차단. 서브에이전트가 "The x.com tweet (candidate 2) was unfetchable (HTTP 402), so I corroborated it via BleepingComputer and other real coverage" 로 대응, openQuestions 에 "primary source unfetchable" 을 명시.

동일한 Anubis 차단은 2026-07-03 (`session-logs/20260703-030007-8175-*`) 에도 발생한 바 있어, 이번이 두 번째 등장이다.

## 차단 유형 3종

| 유형 | 소스 | 관측 응답 | 대응 |
|---|---|---|---|
| Anubis PoW/챌린지 | kernel.org, lore.kernel.org | 챌린지 페이지 반환 (WebFetch 실패) | WebSearch 2차 corroborate |
| 403 + Anubis 챌린지 (v1.25.0, UA 스푸핑 무효) | lore.kernel.org/stable/* | `403 Forbidden` + "Making sure you're not a bot!" (UA 무관하게 전부) | 없음 — 렌즈 결손 |
| HTTP 402 | x.com | `402 Payment Required` | BleepingComputer 등 2차 소스 corroborate |

## 실패 전파 경로 — 렌즈 결손은 조용히 일어난다

`lore-stable-new` 렌즈 사건이 핵심 신호다: 개별 URL 차단 대응(WebSearch corroborate)이 통하지 않을 만큼 소스가 **전면** 차단되면, research 에이전트는 dossier JSON 자체를 산출하지 못하고 `assistant_turns: 0` 으로 세션이 끝난다. 이 결손은:

- write 단계로 전파되지 않는다 (해당 렌즈의 newsletter 가 애초에 생성되지 않음)
- 상위 파이프라인(cron/daily-deploy)이 감지하지 않는다 — 재시도도, 알림도 없다
- Kernel Lens 는 이날 렌즈 6개 중 dossier 6회는 발사됐으나 newsletter 는 5회만 생성됨 (1개 렌즈 결손, 040043 실패분)

## 관찰된 대응 계층

1. **WebSearch / 2차 소스 corroborate + confidence 강등** — 1차 소스가 막혀도 후속 검색으로 사실을 재구성하고 신뢰도를 낮춰 반영 (Linux Daily, AI Coding Agents 사례)
2. **openQuestions 에 미확인 명시** — "primary source unfetchable" 등으로 grounding 계약상 추측·단정을 금지
3. **전부 실패 시 해당 회차 결손** — 위 두 계층이 통하지 않으면(전면 차단) dossier 자체가 안 나오고, 상위 파이프라인은 이를 감지·재시도하지 않는다

## UA 스푸핑은 효과 없었다 (운영 기록)

`lore-stable-new` 렌즈 사건에서 `curl -A "curl/8.4.0"`, `-A "Mozilla/5.0"`, `-A "git/2.43.0"`, `-A "Wget/1.21"`, `-A "Googlebot/2.1"` 등 5종 이상의 User-Agent 로 재시도했으나 전부 `403 Forbidden` 이었다. [[research-write-agent-separation]] 이 기록한 2026-06-18 의 "`curl -A "git/2.39.0"` 비-브라우저 UA 우회 성공" 이 이 소스·이 날짜에는 재현되지 않음 — 봇 차단 방어가 강화되거나 대상 경로(`/stable/*`)가 다른 정책을 적용하는 것으로 보인다 (원인 미확정).

> 교훈: 웹 리서치형 에이전트 파이프라인은 소스 차단을 **1급 실패 모드**로 설계에 반영해야 한다. 렌즈별 결손 감지, 대체 소스(미러·2차 아카이브), 재시도 정책이 없으면 파이프라인은 "정상 완료"처럼 보이는 로그를 남긴 채 조용히 결손된다. UA 우회 같은 임기응변은 방어 강화 시 무효화될 수 있는 비신뢰 폴백이다.

## 관련 맥락

- [[research-write-agent-separation]] — 봇 차단 폴백 사다리(raw 엔드포인트 → 미러 → commitMessage+WebSearch 교차검증 → confidence 강등+openQuestions 격리)의 누적 기록. 이번 사건은 그 사다리가 끝까지 실패했을 때(전면 차단) 무슨 일이 일어나는지를 보여준다.
- [[dev-blog]] — 이 파이프라인이 구현된 프로젝트, 2026-07-24 운영 노트

## 변경 이력

- 2026-07-24: 최초 작성 (2026-07-03·07-24 dev-blog cron 로그에서 승격)
