---
title: Wiki Index
updated: 2026-04-29T00:00:00+09:00
---

# Wiki Index

## 개념 (concepts/)

- [[gieok]] — Claude Code 세컨드 브레인: 세션 기억 상실 문제를 해결하는 자동 지식 축적 시스템
- [[openclaw-agent-architecture]] — 맥 기반 OpenClaw 에이전트 2계층(Main+Coding) 구조와 확장 방향
- [[claude-code-overview]] — 터미널 기반 4세대 AI 코딩 에이전트의 기본 개념, 요금체계, 도입 관점
- [[claude-code-skills-plugins]] — 5가지 번들스킬, 공식 플러그인 마켓플레이스, 개발 라이프사이클 플러그인
- [[claude-code-basic-usage]] — Plan 모드, /context, /compact vs /clear, 중요도별 커맨드 분류
- [[ai-agent-basics]] — LLM+프롬프트+도구 3계층 구조, 자율도 5레벨, 하네스 엔지니어링의 6요소
- [[ai-usage-philosophy]] — 언어화 능력·컨텍스트 설계·리버럴 아츠의 AI 활용 3가지 핵심 원칙

## 프로젝트 (projects/)

- [[gieok]] — gieok 설치 상세, CC Hook 이벤트, LaunchAgent 스케줄, 기능별 LLM 필요 여부, 알려진 버그
- [[ht-trading]] — 알고리즘 트레이딩 시스템: ScoringStrategy 이중 스케일(40/100점), 버그 수정 4건(datetime.now/RSI/TrailingSell/split throttle), 백로그 B1-B4
- [[openclaw]] — AI 에이전트 자동화 도구: 다중 에이전트(main/english/coder) 구성, Telegram 그룹 Privacy Mode 설정, 라우팅 버그 트러블슈팅
- [[oss-radar]] — 주간 GitHub OSS 발굴 파이프라인: discover→fetch→analyze→publish 6단계, star_velocity 스코어링, env -u CLAUDECODE 중첩세션 방지
- [[ai-shorts-production-with-claude-code]] — Claude Code로 AI 쇼츠 영상 대량 제작 흐름, Claude/사람 역할 분리

## 설계 판단 (decisions/)

(아직 페이지가 없습니다)

## 패턴 (patterns/)

- [[claude-code-setup]] — Claude Code 초기 설정 7가지, 하네스 6요소, CLAUDE.md 위치, Rules/Hooks, MCP 검증
- [[claude-code-advanced]] — MCP 통합, Hooks 이벤트 시스템, SubAgents 생성, Agent Teams 패턴
- [[claude-code-agent-teams-tmux]] — Agent Teams 개념, tmux 사용 이유, 작업 분해 기준, Git Worktree 활용
- [[claude-code-loop-automation]] — /loop 사용 사례, 기본 구조, 재현성, 출력 리포트, 운영 주의점
- [[claude-md-guide]] — 4계층 강제력, Memory 4타입, 7가지 작성 테크닉, 3가지 스타터 템플릿
- [[claude-code-token-optimization]] — 4계층 토큰 구조, Context Rot, /compact 효과, 7가지 절약 전략
- [[claude-token-saving-tips]] — .claude/ 정리, 압축형 대화, jupytext, 세션 길이, .claudeignore 등 8가지 기법
- [[claude-code-enterprise-security-bedrock]] — 기업 도입 시 데이터·계정·네트워크·권한·감사·비용 6가지 결정사항
- [[claude-code-windows-wsl-tmux]] — WSL2 필요성, 4월 tmux 이슈, 프로젝트 배치, 트러블슈팅 가이드

## 버그와 해결책 (bugs/)

(아직 페이지가 없습니다)

## 요약 (summaries/)

- [[mac-keyboard-shortcuts-for-windows-users]] — Command(⌘) 중심 구조, 복사/붙여넣기, 스크린샷, 창관리, 앱 전환 단축키

## 분석 (analyses/)

- [[macos-launchagent-catchup-behavior]] — macOS LaunchAgent의 미실행 작업 캐치업 동작 (cron과의 차이)
- [[meanflow-text-to-image]] — MeanFlow 프레임워크를 텍스트 조건부 T2I로 확장 (EMF, arXiv 2604.18168): discriminability·disentanglement의 중요성
- [[diffusion-snr-t-bias]] — 확산 모델 추론 시 발생하는 SNR-t 편향 규명과 웨이블릿 도메인 training-free 보정법 (arXiv 2604.16044)
- [[tstars-tryon-virtual-try-on]] — Taobao 상업용 가상 피팅: MMDiT 5B, 8카테고리, 단일 3.92s (arXiv 2604.19748)
- [[onevl-latent-reasoning]] — 자율주행 VLA의 잠재 CoT: 명시적 CoT 정확도 + 답변-only 지연시간 달성 (arXiv 2604.18486)
- [[soc-otf-sensor-to-ap]] — SoC On-The-Fly: Sensor→AP 직접 스트리밍 연결의 HW/SW 설계 주의사항
- [[openclaw-telegram-group-setup]] — Telegram 그룹 봇: Privacy Mode 비활성화, requireMention 설정, 포럼 토픽 vs 별도 봇 트레이드오프
- [[llada2-uni-unified-multimodal-diffusion]] — SigLIP-VQ + MoE dLLM + 확산 디코더로 이해·생성·편집을 단일 마스크 예측으로 통합 (arXiv 2604.20796)
- [[algorithmic-collusion-reinforcement-learning]] — Soft Actor-Critic 가격 게임: Q-learning 대비 100배 빠른 담합 수렴, 반독점 시사점 (arXiv 2604.15825)
- [[agentspex-agent-specification-language]] — YAML 선언형 에이전트 워크플로우 명세 언어: 모델 버전 업그레이드 내성 (+0.2% vs -6.8%), 형식 검증 가능 (arXiv 2604.13346)
- [[invitrovision-embryo-vlm]] — IVF 배아 캡셔닝용 PaliGemma-2 LoRA 적응: 1,000개로 ChatGPT 5.2 능가 (총점 0.67 vs 0.52) (arXiv 2604.21061)
- [[cointeract-hoi-video-synthesis]] — Human-Aware MoE + 비대칭 Co-Attention으로 손-물체 교합 제거, 추론 시 HOI 브랜치 zero-overhead 제거 (arXiv 2604.19636)
- [[llatisa-time-series-reasoning]] — 4단계 인지 계층 TSR 분류 + 시각화·수치 테이블 융합 VLM, 독점 모델 능가 (arXiv 2604.17295)
- [[prisma-decimal-nextjs-serialization]] — Prisma Decimal 타입이 Next.js Server→Client 직렬화 경계에서 오류를 일으키는 원인과 toNumber() 변환 수정 패턴
- [[agent-world-environment-synthesis]] — RUC+ByteDance의 자기 진화형 에이전트 훈련 아레나: 환경 다양성 스케일링 + 공진화로 14B 모델이 685B 능가 (arXiv 2604.18292)
- [[opengame-agentic-game-development]] — CUHK의 end-to-end 웹 게임 생성 에이전트: Template Skill+Debug Skill+GameCoder-27B, BH/VU/IA 평가 체계 (arXiv 2604.18394)
- [[agentic-world-modeling-taxonomy]] — L1 예측기·L2 시뮬레이터·L3 진화자 × 4가지 지배 법칙 체제: 400편+ 문헌 망라 세계 모델 역량 분류체계 (arXiv 2604.22748)
- [[near-future-policy-optimization-npo]] — RLVR 혼합 정책: 같은 훈련 런의 근미래 체크포인트로 S=Q/V 최대화, Qwen3-VL-8B +5.27pt (arXiv 2604.20733)
- [[microsoft-vibevoice-voice-ai]] — Microsoft 오픈소스 음성 AI: ASR 60분 단일패스+Rich Transcription, TTS 90분·4화자, Realtime 300ms 지연 (44k stars)
- [[prisma-generate-missing-error]] — Prisma 클라이언트 미생성으로 인한 findMany 에러: dev 스크립트에 prisma generate 추가하는 재발 방지 패턴
