---
title: "SLAB_NO_MERGE — cross-cache heap exploitation 방어를 위한 slab 캐시 격리"
domain: "personal"
sensitivity: "public"
tags: ["Linux kernel", "kernel security", "slab", "SLUB", "heap exploitation", "메모리 보안", "hardening"]
created: "2026-06-08"
updated: "2026-06-08"
sources:
  - "session-logs/20260608-034007-c12f-Linux-Kernel-Lens-Newsletter.md"
confidence: "medium"
related:
  - "wiki/projects/kernel-digest.md"
---

# SLAB_NO_MERGE — cross-cache heap exploitation 방어를 위한 slab 캐시 격리

리눅스 커널의 SLUB 할당자는 크기·플래그가 유사한 slab 캐시를 자동으로 병합(merge)해 메모리·캐시 효율을 높인다. 그러나 보안에 민감한 캐시가 일반 캐시와 병합되면, 공격자가 다른 객체 타입으로 같은 슬랩을 재점유하는 **cross-cache heap exploitation** 의 표면이 된다. `SLAB_NO_MERGE` 플래그는 특정 캐시를 병합 대상에서 제외해 이 공격 클래스를 구조적으로 차단한다.

## 핵심 내용

2026-06-06 linux-hardening 메일링리스트의 패치 시리즈는 `slab.rst` 문서에 **언제·어떻게 `SLAB_NO_MERGE` 를 써서 보안 민감 캐시를 cross-cache heap exploitation 으로부터 보호하는지**를 명문화하려 한다. 이는 `mm/slab_common.c` 의 병합 로직을 읽어야만 알 수 있던 암묵지를 공식 문서로 끌어올리는 흐름이다.

> "Add documentation to slab.rst explaining when and how to use SLAB_NO_MERGE to protect security-critical slab caches from cross-cache heap exploitation."

(출처: lore.kernel.org linux-hardening, 2026-06-06 패치 시리즈. dossier 의 evidence quote `verified: true`. 단 lore 가 Anubis 봇 차단으로 본문/diff·리뷰어 피드백 직접 확인은 제한됨 → confidence medium)

## 관련 메커니즘

- **캐시 병합(merge)**: SLUB 기본 동작. `slab_nomerge` 부팅 파라미터로 전역 비활성화 가능.
- **`SLAB_NO_MERGE`**: 캐시 단위로 병합을 막는 플래그. `cred_jar`(자격증명) 같은 민감 캐시에 적용해 권한상승형 cross-cache 공격을 어렵게 한다.
- **인접 hardening 옵션**: `CONFIG_RANDOM_KMALLOC_CACHES`(kmalloc 캐시 무작위화), `SLAB_TYPESAFE_BY_RCU`(타입 안전 재사용) 등과 함께 heap 익스플로잇 난이도를 높이는 다층 방어를 구성.

## 왜 durable 한가

특정 커밋·릴리스 뉴스가 아니라, 커널 메모리 보안의 **반복적으로 유효한 설계 패턴**이다. cross-cache heap exploitation 은 독립적인 CVE 클래스이며, slab 캐시 격리는 그에 대한 표준 완화책으로 계속 참조된다.

## 관련 맥락

- 일일 Linux 커널 보안 다이제스트(kernel-digest 프로젝트)에서 반복 등장한 항목으로, 단발 패치가 아니라 개념으로 분리 기록.
- 운영 관점: stable 트리를 따라가는 운영자는 보안 민감 캐시의 병합 여부를 점검하고, 필요 시 `slab_nomerge` 전역 옵션의 비용(메모리·캐시 효율 저하)과 보안 이득을 저울질해야 한다.

## 변경 이력

- 2026-06-08: 최초 생성 — Linux Kernel Lens(linux-kernel-security 렌즈) 뉴스레터 dossier 에서 SLAB_NO_MERGE 항목 인제스트. 출처: session-logs/20260608-034007-c12f-*
