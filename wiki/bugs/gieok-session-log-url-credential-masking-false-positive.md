---
title: "gieok session-log 의 credential 마스킹이 lore.kernel.org 스타일 URL 을 파괴하는 오탐"
domain: "ai-agent"
sensitivity: "internal"
tags: ["gieok", "session-log", "regex", "masking", "false-positive", "ingest"]
created: "2026-07-23"
updated: "2026-07-23"
sources:
  - "session-logs/20260723-041432-7948-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "Dossier 세션 로그 12건 (2026-07-23 dev-blog cron)"
confidence: "medium"
related:
  - "wiki/projects/gieok.md"
---

# gieok session-log 의 credential 마스킹이 lore.kernel.org 스타일 URL 을 파괴하는 오탐

gieok 이 저장한 session-log 에서 `lore.kernel.org` 스타일 URL — 경로에 `<msgid>@<domain>` 형태(메일 msgid 관례)가 포함된 URL — 이 `https://***:***@linux.dev/` 형태로 파괴되어 기록되는 오탐이 관찰됐다. host 와 경로 전체가 사라지고 이메일 도메인 부분만 남는다.

## 증상

```
원본: https://lore.kernel.org/bpf/20260722151909.69142-2-leon.hwang@linux.dev/
기록: https://***:***@linux.dev/
```

`session-logs/20260723-041432-7948-*` 로그 하나에서 이 패턴으로 마스킹된 URL이 12건, 반면 원형 그대로 살아남은 lore URL 이 2건 공존한다 — **동일 로그 파일 안에서도 비결정적**이다.

## 메커니즘 (추정)

credential 마스킹 로직이 `https://<user>:<pass>@<host>` (userinfo) 형태를 가리기 위해 `@` 앞부분을 통째로 매칭하는 정규식을 쓰는 것으로 보인다. lore.kernel.org 의 URL 경로는 `<날짜>.<번호>-<번호>-<이름>@<도메인>` 형태로 메일 Message-ID 를 경로에 그대로 노출하는데, 이 안의 `@` 를 정규식이 userinfo 구분자로 오인해 `https://` 부터 `@` 직전까지(슬래시 포함 전체 경로)를 `***:***@` 로 치환하고, 그 뒤의 이메일 도메인만 host 로 살아남는다.

**비결정성**: 같은 로그 파일 안에서 일부 lore URL 은 원형 그대로 생존한다 (같은 20260723-041432-7948 로그에서 masked 12건 vs 원형 2건 공존). 마스킹 적용 여부가 URL 이 등장하는 컨텍스트(예: 코드블록 vs 평문, 인용 방식)에 의존하는 것으로 보이나 정확한 조건은 미확인.

## 실질 피해

1. **세션 로그에서 소스 URL 감사·복원 불가** — dossier 가 실제로 어떤 lore 스레드를 인용했는지 로그만으로 되짚을 수 없음
2. **wiki ingest 트리아지의 오판 위험** — `***:***@` 패턴을 보고 「dossier url 필드 데이터 무결성 손상」으로 잘못 진단할 수 있다. 2026-07-23 ingest 사이클에서 실제로 서브에이전트가 이렇게 오판했으며, 07-22 사이클의 보류 건(cc55, url 필드 텍스트 혼입)과 혼동될 위험이 있었다.

> ingest 트리아지 시 `***:***@` 패턴을 마주치면 **dossier 자체의 결함이 아니라 gieok 로거의 마스킹 오탐일 가능성을 먼저 의심**할 것. 이번 확인 결과 07-22 cc55 건과는 별개의 현상으로 판명됐다.

## 상태

관찰만 기록. gieok 마스킹 코드(정규식) 자체의 수정은 아직 미착수.

## 관련 맥락

- [[gieok]] — session-logger 의 마스킹 설계 (Anthropic/OpenAI/AWS/GitHub 키 패턴 등)

## 변경 이력

- 2026-07-23: 최초 생성. 2026-07-23 dev-blog cron dossier 세션 로그 12건에서 관찰, ingest 트리아지 오판 사례 포함 (출처: session-logs/20260723-041432-7948-* 외 11건)
