---
title: Upscayl — AI 이미지 업스케일 데스크톱 앱
domain: personal
sensitivity: public
tags: [analysis, oss, ai, image-upscaling, electron, desktop]
created: 2026-05-17
updated: 2026-05-17
source_session: 20260517-090054-75ca-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md
sources:
  - "session-logs/20260517-090054-75ca-당신은-오픈소스-프로젝트-분석-전문가입니다.-아래-GitHub-레포지토리를-읽고---반드시.md"
confidence: medium
related: []
---

# Upscayl — AI 이미지 업스케일 데스크톱 앱

## 개요

- **레포**: https://github.com/upscayl/upscayl
- **저작자**: Nayam Amarshe, TGS963
- **라이선스**: AGPL-3.0
- **플랫폼**: Linux, macOS, Windows (Electron)

저해상도 이미지를 AI 모델 (Real-ESRGAN 계열 + 커뮤니티 모델) 로 고해상도 업스케일 변환하는 무료·오픈소스 크로스 플랫폼 데스크톱 앱.

## 핵심 특징

- **오프라인 동작** — 클라우드 API 의존 없이 로컬 GPU/CPU 로 추론
- **다양한 모델** — Real-ESRGAN 기반 + 커뮤니티 모델 (예: Helaman 의 HFA2k anime/art 특화) 추가 가능
- **GUI 일체형** — drag&drop, 배치 처리, 출력 포맷·해상도 선택
- **NCNN-Vulkan 백엔드** — CUDA 없이도 동작 (AMD·Intel GPU 지원, ROCm 불필요)

## 사용 시나리오

- 오래된 사진·일러스트의 해상도 복원
- 만화·애니메이션 프레임의 고해상도 변환 (HFA2k 등 art-specific 모델)
- 인쇄·인쇄용 디자인 자원 확보
- 프라이버시 민감 자료 (개인 사진·기업 문서) 의 클라우드 외 업스케일

## 기술 스택·아키텍처

- **GUI**: Electron + TypeScript/React
- **추론 엔진**: NCNN-Vulkan (CPU·다양한 GPU 백엔드 지원)
- **모델 포맷**: ncnn `.param` + `.bin`
- **유지보수**: Windows·Linux 워크어라운드는 커뮤니티 PR (#390 등) 로 보강

## 평가

- **성숙도**: 안정적인 다중 OS 빌드, 활발한 커뮤니티 (Discord, GitHub Discussions), 다국어 인터페이스
- **수익 모델**: 무료 + Pro 유료 버전 (추가 모델·우선 지원)
- **AGPL-3.0** 라이선스이므로 SaaS·재배포 시 소스 공개 의무 주의

## 한계·주의점

- AGPL-3.0 라이선스 — 상용 SaaS 통합 시 소스 공개 의무 발생
- GPU 메모리 부족 시 큰 이미지에서 OOM
- 모델별 품질 편차 — 사진 vs 만화·일러스트에서 적합 모델 다름
- macOS Apple Silicon 에서 NCNN-Vulkan 의 성능은 CUDA·Metal 네이티브 대비 떨어질 수 있음

## 변경 이력

- 2026-05-17: oss-radar 자동 분석 로그 기반 신규 작성 (cron 분석 미생성 상태에서 README 직접 요약)
