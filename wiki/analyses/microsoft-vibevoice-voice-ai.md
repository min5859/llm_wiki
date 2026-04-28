---
title: "Microsoft VibeVoice — 오픈소스 프론티어 음성 AI 모델 패밀리"
domain: "personal"
sensitivity: "public"
tags: ["ai", "voice", "tts", "asr", "microsoft", "open-source", "llm"]
created: "2026-04-29"
updated: "2026-04-29"
sources:
  - "session-logs/20260428-231551-12d1-현재-프로젝트를-실행시키려면-어떻게-해야-하나요.md"
confidence: "high"
related:
  - "wiki/projects/oss-radar.md"
---

# Microsoft VibeVoice — 오픈소스 프론티어 음성 AI 모델 패밀리

**레포**: microsoft/VibeVoice | 44,060 stars (2026-04-28 기준) | MIT 라이선스

## 한줄 요약

Microsoft가 공개한 오픈소스 프론티어 음성 AI 모델 패밀리로, 장시간 음성 인식(ASR)과 텍스트 음성 변환(TTS)을 단일 패스로 처리하는 것이 특징.

## 핵심 기술

연속 음성 토크나이저(Acoustic + Semantic) + **7.5 Hz 울트라 로우 프레임 레이트**. 오디오 충실도를 유지하면서 장시간 시퀀스 처리 효율을 높임. next-token diffusion 프레임워크: LLM(텍스트 이해) + diffusion head(고음질 음성 생성).

## 모델 구성

### VibeVoice-ASR (7B) — 장시간 음성 인식

- **최대 60분 오디오를 단일 패스**로 처리 (64K 토큰 윈도우)
- **Rich Transcription**: Who(화자 분리) + When(타임스탬프) + What(내용) 동시 출력
- 50개 이상 언어 지원
- 사용자 정의 Hotword 기능 (도메인 특화 용어 인식 향상)
- vLLM 추론 지원으로 고속 처리
- 파인튜닝 코드 공개 (finetuning-asr/)
- 2026-03-06: Hugging Face Transformers 라이브러리 공식 통합

### VibeVoice-TTS (1.5B) — 장시간 다화자 TTS

- **최대 90분** 음성 단일 패스 생성
- **최대 4명 화자** 자연스러운 턴테이킹
- 영어·중국어·크로스링구얼 지원
- ICLR 2026 Oral 채택
- **⚠️ 주의**: 악용 사례(딥페이크) 발생으로 코드가 저장소에서 제거됨

### VibeVoice-Realtime (0.5B) — 실시간 스트리밍 TTS

- **~300ms 첫 발화 지연** (실시간 반응)
- 스트리밍 텍스트 입력 지원
- 최대 10분 분량 장문 생성
- 9개 언어 + 11종 영어 스타일 음성 (실험적)

## 사용 시나리오

- 회의 녹음 → 발언자별·시간대별 자동 회의록 생성
- 팟캐스트·강의 영상의 다중 화자 자막 자동 생성
- 실시간 TTS 음성 어시스턴트·내레이션 서비스 구축
- 도메인 특화 음성 인식(의료, 법률 용어) 파인튜닝

## 한계 및 주의사항

- VibeVoice-TTS: 악용 우려로 저장소에서 코드 제거됨 (ASR/Realtime은 정상 제공)
- 딥페이크·음성 합성 악용 리스크 존재 → 상업적 실사용 전 추가 검증 권장
- 기반 모델: Qwen2.5 1.5B → 편향·오류 상속 가능성

## 수집 배경

oss-radar 파이프라인(2026-04-28 첫 실행)이 GitHub Trending에서 발굴한 주간 OSS 레포 중 하나. AI 모델 품질로는 가장 높은 평가를 받은 레포 (스코어 0.1515).

## 변경 이력

- 2026-04-29: 최초 생성 — oss-radar 파이프라인 분석 결과에서 추출
