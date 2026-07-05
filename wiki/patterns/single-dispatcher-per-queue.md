---
title: "공유 작업 큐의 dispatcher 는 하나만 — claim churn 함정"
domain: "ai-agent"
sensitivity: "public"
tags: ["pattern", "ai-agent", "kanban", "task-queue", "dispatcher", "claim", "churn", "hermes"]
created: "2026-07-05"
updated: "2026-07-05"
sources:
  - "session-logs/20260704-132738-e509-지금-세션에서-작업했던-hermes-webui-설치가-pc-를-껏다켜니-접속이-안되네-다시.md"
confidence: high
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/multi-agent-orchestration-taxonomy.md"
---

# 공유 작업 큐의 dispatcher 는 하나만 — claim churn 함정

공유 작업 큐(칸반 보드)를 **여러 dispatcher 가 동시에 폴링**하면, 같은 태스크를 서로 claim·회수·재spawn 하는 churn 으로 태스크가 영원히 진행되지 않는다. Hermes 네이티브 칸반의 5역할 파이프라인 실행에서 실측 (2026-07-04).

## 증상 → 근본 원인

- **증상**: designer 태스크가 run 1~4 까지 잡혔다-회수됐다를 반복(churn)하며 진행하지 못함.
- **근본 원인**: 공유 보드 하나(`~/.hermes/kanban.db`, 전 프로필 공유)를 — `dispatch_in_gateway: true`(60초 주기)인 **모든 실행 중 게이트웨이**(default·maccoder·news·trading = 최대 4개) + **수동 드라이버 스크립트**까지 — 동시에 dispatch. claim 에는 만료 TTL 이 있어 워커가 느리면 다른 dispatcher 가 회수·재spawn → 무한 반복.
- dispatcher 는 게이트웨이 내부의 인격 없는 코드 루프다(모델 호출 아님) — "에이전트가 서로 뺏는다"가 아니라 **스케줄러가 중복 기동**된 것.

## 해법 — 보드당 dispatcher 딱 하나

규칙은 "수동 vs 게이트웨이 중 택1"이 아니라 **"보드당 dispatcher 하나"**다.

- 항상 켜져 있는 프로필(default) 하나만 `dispatch_in_gateway: true` 유지, 나머지 게이트웨이는 전부 `false` 로 끄고 재시작.
- 멈춘 워커는 kill 후 단독 재dispatch 로 복구.
- 워커는 on-demand spawn 이므로 역할별 게이트웨이를 상시 기동할 필요 없음.

검증된 파이프라인 레시피: `hermes kanban create --assignee <role> --parent <id> --workspace dir:<path>` + `link parent child` + `dispatch --dry-run / --max N`.

## 일반화

분산 작업 큐 일반의 함정 — 컨슈머(워커)는 여럿이어도 되지만, **claim/lease 를 발급하는 스케줄러가 다중이면** lease TTL 과 결합해 churn 이 된다. 스케줄러 다중화가 필요하면 리더 선출이나 파티셔닝이 선행돼야 한다.

## 변경 이력

- 2026-07-05: 최초 생성 — Hermes 칸반 역할 파이프라인의 designer 태스크 churn 실측에서 일반화 (출처: session-logs/20260704-132738-e509-*)
