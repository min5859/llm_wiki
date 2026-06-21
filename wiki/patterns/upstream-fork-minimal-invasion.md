---
title: "업스트림 포크 최소 침습 운영 — 머지 여지를 남기는 변경 전략"
domain: both
sensitivity: public
tags: ["fork", "upstream", "open-source", "merge", "git", "maintenance", "refactoring"]
created: "2026-06-21"
updated: "2026-06-21"
sources:
  - "session-logs/20260621-154117-e509-▎-..-HANDOFF.md-와-..-CLAUDE.md-읽고-Phase-2-UI부터-이어가.md"
confidence: medium
related:
  - "wiki/analyses/build-vs-fork-personal-tool.md"
  - "wiki/projects/hermes-dashboard.md"
---

# 업스트림 포크 최소 침습 운영 — 머지 여지를 남기는 변경 전략

OSS 를 포크해 기능을 얹을 때, 나중에 업스트림 변경을 당겨오기 쉽도록 **원본 파일 수정을 최소화하고 우리 변경을 격리**하는 운영 패턴. (포크할지 빌드할지의 *의사결정* 은 [[build-vs-fork-personal-tool]], 이 문서는 포크하기로 한 *이후의 운영*.)

## 원칙

- **신규 파일로 추가, 원본은 거의 건드리지 않기.** 기능을 새 파일로 구현하고, 원본에는 import 1줄 + 주입 2줄 수준의 최소 hook 만 남긴다(예: 사이드바에 섹션 하나 끼우기 = import 1 + 삽입 2). 원본 diff 가 작을수록 업스트림 머지 충돌이 적다.
- **우리 변경은 별도 커밋으로 분리.** 업스트림 동기화 커밋과 우리 기능 커밋을 섞지 않는다. 머지/리베이스 때 우리 것만 골라내기 쉽다.
- **머지 절차를 문서화.** `UPSTREAM-MERGE.md` 같은 문서에 "어떤 remote, 어떻게 당기고, 우리 변경과 어디서 충돌하는지"를 적어 둔다.
- **잔재는 삭제 대신 보관.** 포크 전 from-scratch 잔재나 폐기 코드는 즉시 삭제하지 말고 `legacy/` 로 `git mv`(이력 보존) + 보관 사유 README. 임의 삭제는 되돌리기 어렵고 맥락을 잃는다.

## 왜

포크의 최대 비용은 **업스트림과 멀어지는 것**(divergence)이다. 원본 파일을 많이 고칠수록 매 업스트림 릴리스마다 머지 지옥이 된다. 변경을 신규 파일·별도 커밋으로 격리하면 "업스트림 추적"과 "우리 기능"을 동시에 유지할 수 있다.

## 관련 맥락

[[hermes-dashboard]] 가 Hermes Studio(MIT)를 포크해 메신저 UX 레이어를 얹을 때 적용. Studio 원본은 최소 hook 만, 메신저 기능은 신규 파일, 잔재는 `legacy/` 이동.

## 변경 이력

- 2026-06-21: 최초 작성 (출처: session-logs/20260621-154117-e509)
