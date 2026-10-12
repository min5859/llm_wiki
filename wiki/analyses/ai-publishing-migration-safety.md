---
title: "AI 게시 파이프라인의 안전한 이관과 발행 이력 관리"
domain: "ai-agent"
sensitivity: "internal"
tags: ["analysis", "publishing", "migration", "idempotency", "systemd"]
created: "2026-10-13"
updated: "2026-10-13"
sources:
  - "session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md"
confidence: "medium"
related:
  - "wiki/projects/oss-radar.md"
  - "wiki/projects/dev-blog.md"
  - "wiki/patterns/launchd-secret-management.md"
---

# AI 게시 파이프라인의 안전한 이관과 발행 이력 관리

OSS Radar와 Research Wiki 이관에서 반복 확인된 원칙이다. 분석 성공·인증 성공·예약 실행 성공을 실제 게시 성공과 구별하고, 상태 이력과 게시자를 함께 전환한다. 도메인 핵심 운영 결정이며 두 파이프라인에서 재현돼 범용 분석으로 수집한다.

## 핵심 내용

- 후보 선정 때 history를 쓰면 분석·게시 실패 자료까지 처리된 것으로 제외된다. Wiki push 성공 이후 이력을 갱신한다. OSS Radar는 700→600개, Research Wiki는 390→362개로 실제 게시 결과에 맞춰 백업 후 복구했다. 이력은 clone으로 따라오지 않는 운영 상태이므로 체크섬으로 이관을 확인한다.
- 전체 dry-run은 수집·변환·분석·페이지 검증을 수행하되 원격 Wiki와 history를 변경하지 않는다. GitHub API 조회 인증과 Wiki 쓰기 인증은 별도 확인한다. push dry-run 성공은 실제 게시 성공의 증거로 대신 쓰지 않는다.
- 새 서버 예약은 비활성으로 준비한다. 마지막 기존 실행을 확인하고 최신 history를 동기화한 뒤 기존 예약 중지→새 예약 활성화로 게시자 하나만 유지한다. 첫 정기 실제 게시와 원격 commit·이력 증가를 확인한 뒤 다음 프로젝트를 통합한다.
- 서버 UTC와 게시 날짜 KST를 구분한다. 파일명뿐 아니라 생성 시각과 Home 링크까지 같은 날짜 기준을 사용한다.

## 서비스 계정과 장애 관측

외부 콘텐츠를 AI CLI로 처리하고 GitHub 쓰기 권한을 갖는 작업은 서비스 계정으로 운영했다. 성격이 같은 게시 작업은 공용 계정으로 통합하되 프로젝트 간 격리가 줄어드는 점을 감수한다. 계정 홈을 옮길 때 UID/GID 유지와 별개로 CLI의 절대경로 symlink·인증·systemd 실행 계정을 확인한다. symlink는 release→current→실행 링크의 의존 순서로 복구한다.

OSS Radar에서는 모델 설정이 비어 기본 모델을 사용하는 구버전 CLI가 새 기본 모델을 지원하지 못해 분석 전체가 실패했다. 같은 구버전 호출로 재현했으며 stderr 첫 200자만 저장하던 로그가 오류 본문을 숨겼다. 마지막 4,000자를 보존하도록 개선하고 실제 응답·전체 dry-run으로 복구를 검증했다. 단순 버전 업데이트보다 모델 지원과 오류 본문 확인이 핵심이다.

서비스 exit 0만으로 게시 상태를 판단하지 않는다. 토픽별 성공·후보 없음에 따른 정상 생략·실패, 실제 원격 게시를 구분한다. 디스크 감소도 게시물과 CLI 설치본·캐시 증가를 분리해 조사한다. 이전 CLI 버전의 자동 정리 정책은 세션에서 확인하지 못했으므로 자동 삭제를 보장하지 않는다.

## 관련 맥락

구체적인 OCI 이관·공용 계정 적용은 [[oss-radar]]와 [[dev-blog]], 환경파일과 서비스 인증 분리는 [[launchd-secret-management]]를 참조한다. 이 문서는 세션에 기록된 결과를 정리하며 현재 서버를 재검증하지 않았다.

## 변경 이력

- 2026-10-13: 두 Wiki의 이관·history 복구, 게시자 전환과 AI CLI 오류 관측 패턴 수집 (출처: session-logs/codex-01a0d80a-7b52-7360-abc0-00becbe1e705.md)
