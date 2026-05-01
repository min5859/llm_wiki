---
title: Operation Log
updated: 2026-05-02T08:00:00+09:00
---

# Operation Log

## 2026-05-02T08:00 — wiki-ingest (session-logs, ingested: false 2건)

- Source: session-logs/20260501-213505-aecb-*.md (cwd: japa / Vercel P2024 시세 새로고침 사고)
  - Project: japa-asset-dashboard
  - **Created**: wiki/bugs/prisma-connection-pool-vercel-supabase.md — `connection_limit=1` 환경에서 8개 `$transaction` 병렬화로 P2024 풀 고갈 + 같은 인스턴스 후속 요청 연쇄 실패. fix: 불변 시계열은 `createMany({ skipDuplicates: true })` + 오늘 한 행만 upsert, 무거운 쓰기는 click-path 에서 일별 cron 으로 분리. 일반화 신호 (Vercel + connection_limit=1 + 트랜잭션 병렬화 + 불변 데이터) 정리.
  - **Created**: wiki/patterns/vercel-cron-best-practices.md — Vercel Cron 5결정 종합. (1) Hobby 슬롯 제약 (≤2, 일1회) → 단일 `/api/cron/daily` + KST 날짜 분기 (월1일 / 1월1일) 패턴. (2) CRON_SECRET — 공개 cron URL 보호. Vercel 이 자동으로 `Authorization: Bearer` 헤더 첨부, 라우트는 헤더 비교만. (3) Next.js middleware 가 cron 호출을 `/login` 으로 redirect 하지 않게 matcher 에서 `api/cron` 제외. (4) 멱등성 — `createMany skipDuplicates` / `updateMany` / 비멱등 작업엔 24h guard. (5) Crons UI / Logs / 수동 Run / Notifications 운영.
  - **Updated**: wiki/projects/japa-asset-dashboard.md — sources 추가, "시세 새로고침 P2024 사고와 cron 통합" / "세테크 계좌 납입한도 평가액 분리 (`Account.contributionYTD` 컬럼)" / "WTI 원유 인덱스" / "CSV 내보내기 (UTF-8 BOM)" / "Prisma migrate deploy 빌드 통합" / "Yahoo quote 와 한국 종목 시간외 거래" 섹션 신설.
  - **Updated**: wiki/analyses/nextjs-vercel-supabase-deployment.md — sources 추가, `connection_limit=1` 의 인접 함정 (무거운 트랜잭션 병렬화) 섹션 신설, prisma migrate deploy 빌드 통합 운영 점검 사항 보강 (DIRECT_URL 환경 체크 / 빌드 실패가 안전망 / expand-and-contract 주의).
- Source: session-logs/20260501-233118-b6e0-*.md (cwd: finance-analysis-nextjs / 기능 제안 → DB 데이터 정리 검토)
  - Project: finance-analysis-nextjs
  - **Updated**: wiki/projects/finance-analysis-nextjs.md — sources 추가, "운영 측 약점 (analyses 누적 / DELETE 경로 부재 / financial_statements 미사용 / provider 하드코딩)" 섹션 신설, 백로그 4개 추가. PDF 내보내기 (`pdf-generator.ts` + `html2canvas-pro` + `jspdf`) 가 이미 구현되어 있음을 확인 — Claude 가 코드 검증 전에 PDF 기능을 신규 후보로 제안했다가 사용자 지적으로 정정한 사건이 있어 이번 갱신에서는 "기존 구현 확인됨" 을 명시.
- Updated: wiki/index.md (patterns/ 에 vercel-cron-best-practices 추가, bugs/ 에 prisma-connection-pool-vercel-supabase 추가)
- Marked ingested: true — 2개 session-log 파일

## 2026-05-01T13:00 — wiki-ingest (session-logs, ingested: false 26건)

- Skipped (내용 없음 — 단순 ping):
  - 20260430-141817-baa6, 20260430-144026-977a, 20260501-080100-ae82, 20260501-090051-be5b (모두 `Reply with only: OK`)
- Skipped (논문 분석 미완료, assistant_turns: 0):
  - 20260501-080108-f48b (arXiv 2604.24819 ProDa: Programming with Data) — 본문 길이가 컨텍스트 한계를 넘어 응답 생성되지 않음
  - 20260501-080209-9b29 (arXiv 2604.26752 GLM-5V-Turbo) — 동일 사유로 응답 없음
- Skipped (oss-radar 자동 발행물 — 본 LLM Wiki 의 wiki/analyses/ 에는 추가하지 않음):
  - 20260430-141825-b1dc, 20260430-141857-8b24, 20260430-141931-b081, 20260430-142011-af92, 20260430-142048-7399 (CJackHwang/ds2api), 20260430-144033-2641 (badlogic/pi-mono), 20260430-144111-c80d, 20260430-144143-2c7c, 20260430-144218-615f, 20260430-144247-00cb (everything-claude-code — 이미 wiki/analyses 에 페이지 존재), 20260501-090057-57ff, 20260501-090135-2511, 20260501-090209-cd7c (microsoft/generative-ai-for-beginners), 20260501-090243-4133, 20260501-090320-90d0 (langchain-ai/langchain) — 모두 oss-radar 의 README 분석 자동 워크플로우 산출물로, oss-radar 의 GitHub Wiki 에 발행되는 정형 리포트
- Source: session-logs/20260430-134759-328e-*.md (cwd: oss-radar / GITHUB_TOKEN 401)
  - Project: oss-radar
  - 이미 처리됨: wiki/projects/oss-radar.md, wiki/patterns/launchd-secret-management.md (sources 에 본 세션 포함)
  - 추가 변경 없음 (PAT 401 진단 + plist → config/.env 분리 + fine-grained PAT 권장 + launchd 가 ~/.zshrc 안 읽음 모두 이미 정리됨)
- Source: session-logs/20260430-135011-e8eb-*.md (cwd: japa / disk size + Vercel 배포)
  - Project: japa-asset-dashboard
  - 이미 처리됨: wiki/projects/japa-asset-dashboard.md, wiki/analyses/nextjs-vercel-supabase-deployment.md (sources 에 본 세션 포함)
  - 추가 변경 없음 (Vercel-GitHub App + Webhook + Hobby private repo + Pooler 모드 + force-dynamic + Import .env 모두 이미 정리됨)
- Source: session-logs/20260430-161410-0fcc-*.md (cwd: japa / 4가지 개선 + 인증 + Yahoo silent fail)
  - Project: japa-asset-dashboard
  - 이미 처리됨: wiki/projects/japa-asset-dashboard.md, wiki/bugs/yahoo-finance-concurrent-silent-fail.md (sources 에 본 세션 포함)
  - 추가 변경 없음 (HMAC-SHA256 단일 사용자 인증 + 시세 새로고침 worker pool 6 + 1회 retry + 수익률 % 표시 모두 이미 정리됨)
- Source: session-logs/20260430-171050-9ee3-*.md (cwd: openclaw / acp list 확인)
  - Project: openclaw
  - Skipped: 결론 없는 1턴 짜리 세션 (`apc` 가 `acp` 의 오타임을 확인하고 종료, 새로운 설계 판단·버그·패턴 없음)
- Source: session-logs/20260430-174408-1a2e-*.md (cwd: openclaw workspace / finance-analysis-nextjs 분석)
  - Project: finance-analysis-nextjs
  - 이미 처리됨: wiki/projects/finance-analysis-nextjs.md, wiki/analyses/ai-valuation-trustworthiness.md (sources 에 본 세션 포함)
  - **Created**: wiki/analyses/pdf-text-extraction-vs-ocr.md — ai-valuation-trustworthiness 와 finance-analysis-nextjs 가 모두 link 하고 있었으나 페이지가 누락 상태였음. 디지털 PDF vs 스캔 PDF vs 하이브리드 PDF 의 구분, 빈 텍스트 침묵 실패 차단, OCR 엔진 비교 (Tesseract / Google Vision / Upstage / Azure / Textract), 트렁케이션 시 재무제표 페이지 선별, 연결/별도 재무제표 구분 metadata 패턴 정리.
- Updated: wiki/index.md (analyses/ 섹션에 pdf-text-extraction-vs-ocr 추가), wiki/log.md (본 항목)
- Marked ingested: true — 26개 session-log 파일 전체

## 2026-04-22 — wiki-ingest (session-log)

- Project: gieok
- Source: session-logs/20260422-002046-60a1-왜-7-00-인가요--매일-오전-7-00-에-동작하는-건가요.md
- Created:
  - wiki/concepts/gieok.md — gieok 개념 (4계층 아키텍처, Hook 등록 방식, gieok vs Obsidian)
  - wiki/projects/gieok.md — gieok 프로젝트 상세 (컴포넌트, LaunchAgent 스케줄, LLM 필요 여부, 버그 이력)
  - wiki/analyses/macos-launchagent-catchup-behavior.md — macOS LaunchAgent 캐치업 동작 분석
- Updated: wiki/index.md, wiki/log.md
- Skipped: 탐색적 QA 대화, 설치 과정 시행착오 (결론만 추출)

## 2026-04-23 — wiki-ingest (session-logs, ingested: false 1건)

- Source: session-logs/20260422-230939-22f1-스코어링-점수를-65-점에서-60-점으로-조정했는지-확인해-주세요.md
  - Project: ht_trading
  - Created: wiki/projects/ht-trading.md
    — ScoringStrategy 이중 스케일 구조(백테스트 40pt / 라이브 100pt), config 2파일 동기화 규칙, 임계값 65→60 조정 이력
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 1개 session-log 파일

## 2026-04-23T13:00 — wiki-ingest (session-logs, ingested: false 6건)

- Source: session-logs/20260423-080035-4bb7-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260423-080041-976a-*.md (arXiv: 2604.19748)
  - Created: wiki/analyses/tstars-tryon-virtual-try-on.md
    — Taobao 상업용 가상 피팅 시스템: MMDiT 5B, 8카테고리, 단일 3.92s/멀티 6.74s, RL(DiffusionNFT)
- Source: session-logs/20260423-080131-e2e6-*.md (arXiv: 2604.18486)
  - Created: wiki/analyses/onevl-latent-reasoning.md
    — Xiaomi 자율주행 VLA: 잠재 CoT로 명시적 CoT 정확도 + 답변-only 지연시간 동시 달성
- Source: session-logs/20260423-120308-f269-*.md (ht_trading)
  - Updated: wiki/projects/ht-trading.md
    — split throttle G4-1 드로다운 버그 수정 기록 추가 (bar.close → pos.current_price, commit c5dc818)
- Source: session-logs/20260423-113736-72aa-*.md (openclaw)
  - Created: wiki/projects/openclaw.md
    — OpenClaw 업데이트(2026.3.28→2026.4.21), git 관리 구조, 이미지 생성 별도 API 키 필요
- Source: session-logs/20260423-125016-fe2e-*.md (gieok)
  - Skipped: 짧은 탐색적 세션 (gieok 세션 컨텍스트 주입 확인), 새로운 설계 판단 없음
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 6개 session-log 파일 전체

## 2026-04-23T13:30 — wiki-ingest (session-logs, ingested: false 1건)

- Source: session-logs/20260423-130102-15da-지금-wiki에-어떤-페이지가-있나요.md
  - Project: gieok
  - Skipped: 단순 wiki 페이지 조회 세션 (vault 경로 확인 외 설계 판단·버그·패턴 없음)
- Updated: wiki/index.md (기존에 누락된 concepts 6건, patterns 9건, projects 1건, analyses 1건 일괄 추가)
- Marked ingested: true — 1개 session-log 파일

## 2026-04-23T21:15 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260423-192900-e7ec-Reply-with-exactly--coder-session-alive.md
  - Project: openclaw
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 ping 프롬프트)

- Source: session-logs/20260423-193125-b999-graphify.md
  - Project: ht_trading
  - Updated: wiki/projects/ht-trading.md
    - graphify 아키텍처 분석 추가 (god nodes: Bar 176, Market 174, 87 communities)
    - 버그 수정 3건 추가:
      - Rule 6 백테스트 `datetime.now()` → `bar.dt` (commit 803a158)
      - RSI 평탄 구간 100 → 50 반환 (commit 803a158)
      - TrailingSell `bar.close` → `pos.current_price` (commit f376ba8)
    - 개선 백로그 B1-B4 추가

- Source: session-logs/20260423-194609-6b61-코딩전용-openclaw-agent-를-추가했는데-텔레그램으로-메세지를-보내면-응답이-없습.md
  - Project: openclaw
  - Updated: wiki/projects/openclaw.md
    - 다중 에이전트 구성 (main/english/coder) 섹션 추가
    - Telegram 그룹 설정 (requireMention, Privacy Mode) 섹션 추가
    - 라우팅 버그 트러블슈팅 섹션 추가
  - Created: wiki/analyses/openclaw-telegram-group-setup.md
    - Telegram Bot Privacy Mode 비활성화 필요성
    - requireMention 그룹/토픽 레벨 차이
    - 포럼 토픽 vs 별도 봇 트레이드오프
    - OpenClaw bindings에 `"type": "acp"` 포함 시 라우팅 0 버그

- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체 (skip 1건, 처리 2건)

## 2026-04-24T09:00 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260424-080017-211f-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" health-check 프롬프트)
- Source: session-logs/20260424-080022-8403-*.md (arXiv: 2604.20796)
  - Created: wiki/analyses/llada2-uni-unified-multimodal-diffusion.md
    — LLaDA2.0-Uni: SigLIP-VQ(의미론적 이산 토크나이저) + 16B MoE dLLM + 확산 디코더. 이해·생성·편집을 단일 마스크 예측 목표로 통합. SPRINT 추론 1.6× 가속
- Source: session-logs/20260424-080120-625e-*.md (arXiv: 2604.15825)
  - Created: wiki/analyses/algorithmic-collusion-reinforcement-learning.md
    — Soft Actor-Critic 기반 가격 게임에서 담합 수렴: Q-learning 대비 100배 빠름 (~5만 스텝 ≈ 5년), 반독점 규제 시사점
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체 (skip 1건, 생성 2건)

## 2026-04-25T09:00 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260425-080017-8e34-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" health-check)
- Source: session-logs/20260425-080021-dbd0-*.md (arXiv: 2604.13346)
  - Created: wiki/analyses/agentspex-agent-specification-language.md
    — AgentSPEX: YAML 선언형 에이전트 워크플로우 명세 언어. LangGraph 대비 모델 버전 변경 시 -0.2% 유지(vs Live-SWE-agent -6.8%). 형식 검증(formal verification) 가능.
- Source: session-logs/20260425-080113-edb6-*.md (arXiv: 2604.21061)
  - Created: wiki/analyses/invitrovision-embryo-vlm.md
    — PaliGemma-2 LoRA 적응: 1,000개 IVF 배아 이미지로 ChatGPT 5.2 능가(총점 0.67 vs 0.52). 임상 맞춤 복합 평가 메트릭(ECC/MD/PD 가중치) 제안.
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체 (skip 1건, 생성 2건)

## 2026-04-26T13:00 — wiki-ingest (session-logs, ingested: false 6건)

- Source: session-logs/20260426-080019-1991-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" health-check)
- Source: session-logs/20260426-080023-cb0a-*.md (arXiv: 2604.19636)
  - Created: wiki/analyses/cointeract-hoi-video-synthesis.md
    — CoInteract: Human-Aware MoE + 비대칭 Co-Attention 2단계 학습으로 손-물체 교합 제거, 추론 시 HOI 브랜치 제거(zero overhead). VLM-QA 0.72, HQ 0.724로 기존 방법 능가
- Source: session-logs/20260426-080121-2ff3-*.md (arXiv: 2604.17295)
  - Created: wiki/analyses/llatisa-time-series-reasoning.md
    — LLaTiSA: 4단계 인지 계층(L1-L4) TSR 분류, 83K HITSR 데이터셋, 시각화+수치 테이블 융합 VLM, 3단계 커리큘럼 파인튜닝
- Source: session-logs/20260426-111623-cfe2-*.md (ht_trading)
  - Updated: wiki/projects/ht-trading.md
    - 리스크 매니저 정책(max_positions:5, max_position_pct:30%, _validate_buy 5조건) 추가
    - 2026-04-24 리스크 거부 사례 분석: HD현대일렉트릭·수산인더스트리 매수 거부 원인이 5/5 포지션 포화
    - 구조적 문제: rotation 로직 부재 → 고점수 신호도 진입 불가
- Source: session-logs/20260426-120703-304f-*.md (openclaw / asset-dashboard)
  - Updated: wiki/projects/openclaw.md
    - asset-dashboard git 분리 구조 (workspace-coder/.gitignore, 독립 repo 초기화)
    - workspace-state.json untrack 처리
    - asset-dashboard Phase 1 완료·Phase 2 미시작 현황
- Source: session-logs/20260426-121630-14c3-*.md (openclaw / ACP 설정)
  - Updated: wiki/projects/openclaw.md
    - acpx 플러그인 permissionMode 스키마 제약 (approve-all/approve-reads/deny-all만 허용, "auto" 없음)
    - nonInteractivePermissions 스키마: deny/fail만 허용 ("allow" 없음) → 자율 실행 불가
    - openclaw config set 명령으로 설정 변경 방법
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 6개 session-log 파일 (skip 1건, 생성 2건, 수정 2건)

## 2026-04-27T09:00 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260427-080020-5805-Reply-with-only--OK.md
  - Skipped: 내용 없음 (단순 "Reply with only: OK" 프롬프트, assistant_turns: 1, 유의미한 내용 없음)

- Source: session-logs/20260427-080024-03ac-*.md (arXiv: 2604.18292)
  - Paper: Agent-World: Scaling Real-World Environment Synthesis for Evolving General Agent Intelligence
  - Institution: Renmin University of China + ByteDance Seed
  - Created: wiki/analyses/agent-world-environment-synthesis.md
    - 자기 진화형 에이전트 훈련 아레나: Agentic Environment-Task Discovery + Continuous Self-Evolving Agent Training
    - GRPO 기반 다중 환경 RL + 자동 진단→타겟 확장→RL 루프
    - Agent-World-14B: BFCL-V4 55.8% (DeepSeek-V3.2-685B 54.1% 초과, 50배 작은 모델)
    - 환경 스케일링: 0→1978개 환경, 평균 성능 18.4%→38.5% (+20.1 pt)

- Source: session-logs/20260427-080119-fdca-*.md (arXiv: 2604.18394)
  - Paper: OpenGame: Open Agentic Coding for Games
  - Institution: CUHK MMLab
  - Created: wiki/analyses/opengame-agentic-game-development.md
    - 첫 오픈소스 end-to-end 웹 게임 생성 에이전틱 프레임워크
    - Game Skill: Template Skill(5개 물리 특성별 템플릿 패밀리) + Debug Skill(living protocol)
    - GameCoder-27B: Qwen3.5-27B 기반 CPT+SFT+RL 3단계 훈련
    - OpenGame-Bench: BH/VU/IA 동적 평가 (헤드리스 브라우저 + VLM 판정)
    - Hook-Driven Implementation이 가장 중요한 컴포넌트 (-10.1 BH, -11.6 IA without it)

- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 (skip 1건, 생성 2건)

## 2026-04-26T20:30 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260426-141208-ad61-맥비-지금-코드가-어디까지-구현되었는지-확인해-주세요.md
  - Project: openclaw / asset-dashboard
  - Updated: wiki/projects/openclaw.md
    - Phase 2 완료 기록 (Account/Holding CRUD + Server Actions)
    - Phase 3 완료 기록 (Yahoo Finance 시세·환율 연동)
    - Phase 3 구현 파일 상세: PriceCache 모델, lib/market.ts, app/actions/prices.ts, lib/portfolio.ts PriceContext, RefreshPricesButton
    - yahoo-finance2 ESM-only 모듈 주의사항 (require 불가, new YahooFinance() 인스턴스화 필요, as any 우회)

- Source: session-logs/20260426-155125-ac49-logout.md
  - Project: openclaw
  - Skipped: "logout" 2회 입력만 있는 빈 세션 (assistant_turns: 0)

- Source: session-logs/20260426-184740-49b5-지금-실행되는-next.js-에서-5-issues-라고-뜹니다.-##-Error-Type.md
  - Project: openclaw / asset-dashboard
  - Created: wiki/analyses/prisma-decimal-nextjs-serialization.md
    - Prisma Decimal 타입 → Next.js Server→Client 직렬화 경계 오류 원인 분석
    - spread `...holding` 시 Decimal 포함 → 런타임 에러 (tsc 통과라 발견 어려움)
    - 수정 패턴: toNumber()로 Decimal 필드를 명시 변환 후 override
  - Updated: wiki/projects/openclaw.md
    - Prisma Decimal 직렬화 버그 수정 패턴 + [[prisma-decimal-nextjs-serialization]] 링크 추가

- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 (skip 1건, 수정 1건, 생성 1건 + 수정 1건)

## 2026-04-28T09:00 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260428-080030-b01f-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" health-check 프롬프트)
- Source: session-logs/20260428-080034-ffa6-*.md (arXiv: 2604.22748)
  - Paper: Agentic World Modeling: Foundations, Capabilities, Laws, and Beyond
  - Institution: HKUST·NUS·Oxford·NTU·CUHK 등 다기관
  - Created: wiki/analyses/agentic-world-modeling-taxonomy.md
    — L1(Predictor)/L2(Simulator)/L3(Evolver) × 4가지 지배 법칙 체제(물리/디지털/사회/과학)
    — 400편+ 문헌 합성. L1→L2 경계는 단일 스텝 품질이 아닌 롤아웃 충실도로 결정
    — 대표 시스템: EfficientZero(2시간 Atari 초인간), TD-MPC2(317M·104과제)
- Source: session-logs/20260428-080124-aa02-*.md (arXiv: 2604.20733)
  - Paper: Near-Future Policy Optimization
  - Institution: IIE, CAS / JD.COM
  - Created: wiki/analyses/near-future-policy-optimization-npo.md
    — RLVR 혼합 정책: S=Q/V (신호 품질/분산 비용) 트레이드오프 정식화
    — NPO: 같은 훈련 런의 근미래 체크포인트를 가이드로 활용
    — AutoNPO: 엔트로피 붕괴 탐지 → Δ* 자동 선택 → 롤백 재학습
    — Qwen3-VL-8B: 57.88 → 62.84 (NPO) → 63.15 (AutoNPO)
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 (skip 1건, 생성 2건)

## 2026-04-28T17:00 — wiki-ingest (session-logs, ingested: false 2건)

- Source: session-logs/20260428-152446-9b5b-project-toy-oss-radar--프로젝트를-시작하려고합니다.-현재상태를-분석해주세.md
  - Project: oss-radar (toy)
  - Skipped: 상태 분석 탐색 세션 (현황 파악만, 설계 판단 없음) — 다음 세션과 합산 처리
- Source: session-logs/20260428-153031-2553-project-toy-oss-radar--프로젝트의-phase-1부터-진행해-주세요.md
  - Project: oss-radar (toy)
  - Created: wiki/projects/oss-radar.md
    — Phase 1~6 전체 구현 완료 기록
    — 스코어링 공식 (star_velocity×0.5 + star_total_norm×0.3 + fork_norm×0.2)
    — `env -u CLAUDECODE claude -p` 중첩 세션 방지 패턴
    — GitHub API rate limit (60→5000 req/h with GITHUB_TOKEN)
    — README 40k자 truncate, bkit footer 제거, 한국어 검증 max 2회 재시도
    — macOS launchd 매주 월요일 09:00 KST 자동화
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 2개 session-log 파일

## 2026-04-29T00:00 — wiki-ingest (session-logs, ingested: false 9건)

- Source: session-logs/20260428-234006-d910-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 OK 프롬프트)
- Source: session-logs/20260428-234011-cd92-*.md (oss-radar analyze 세션: public-apis)
  - Skipped: assistant_turns: 0 (analyze.sh가 `claude -p`로 호출한 비대화형 세션, 실제 분석은 data/analysis/에 저장)
- Source: session-logs/20260428-234044-dc39-*.md (oss-radar analyze 세션: free-programming-books)
  - Skipped: 동일 사유 (assistant_turns: 0)
- Source: session-logs/20260428-234111-ba4a-*.md (oss-radar analyze 세션: system-design-primer)
  - Skipped: 동일 사유 (assistant_turns: 0)
- Source: session-logs/20260428-234144-9d4e-*.md (oss-radar analyze 세션: VibeVoice)
  - Skipped: 동일 사유 (assistant_turns: 0) — VibeVoice 분석 내용은 231551-12d1 세션에서 추출
- Source: session-logs/20260428-234218-45ec-*.md (oss-radar analyze 세션: mattpocock/skills)
  - Skipped: 동일 사유 (assistant_turns: 0)
- Source: session-logs/20260428-231410-9f52-*.md (asset-dashboard Prisma 에러)
  - Project: asset-dashboard (Next.js + Prisma)
  - Created: wiki/analyses/prisma-generate-missing-error.md
    - `.prisma/client/` 미생성으로 findMany 에러 원인 분석
    - 해결: `npx prisma generate`
    - 재발 방지: `dev` 스크립트에 `prisma generate &&` 추가 패턴
- Source: session-logs/20260428-231551-12d1-*.md (oss-radar 실행 및 자동화)
  - Project: oss-radar
  - Updated: wiki/projects/oss-radar.md
    - analyze.sh venv python3 우선 사용 버그 수정 기록 (시스템 python3 → .venv/bin/python3)
    - 스케줄 변경: 매주 월요일 → 매일 09:00
    - GITHUB_TOKEN git 비포함 패턴 (LaunchAgents 설치 파일에만)
    - 중복 방지 한계 3가지 추가 (최신성·Trending 의존도·lookback_days 미적용)
  - Created: wiki/analyses/microsoft-vibevoice-voice-ai.md
    - oss-radar 파이프라인 첫 실행에서 발굴한 Microsoft VibeVoice 분석
    - ASR-7B 60분 단일패스, TTS-1.5B 90분, Realtime-0.5B 300ms
    - Rich Transcription (Who/When/What), 파인튜닝 코드 공개
- Source: session-logs/20260428-235122-215d-*.md (ht_trading 매매 로그 분석)
  - Project: ht_trading
  - Updated: wiki/projects/ht-trading.md
    - 동적 트레일링 스톱 (trailing_tiers) 기능 추가 기록
    - 수익 3~9%: 3%, 10~19%: 6%, 20%+: 10% distance 구간별 확대
    - profit_take_levels 비활성화 (trailing_tiers로 대체)
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 9개 session-log 파일 (skip 6건, 처리 3건, 생성 2건, 수정 1건)

## 2026-04-22T08:30 — wiki-ingest (session-logs, ingested: false 3건)

- Source: session-logs/20260422-080031-e9df-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260422-080037-7719-*.md (arXiv: 2604.18168)
  - Created: wiki/analyses/meanflow-text-to-image.md
    — MeanFlow 텍스트 조건부 확장 (EMF), discriminability·disentanglement 분석
- Source: session-logs/20260422-080140-da77-*.md (arXiv: 2604.16044)
  - Created: wiki/analyses/diffusion-snr-t-bias.md
    — DPM의 SNR-t 편향 규명 및 웨이블릿 도메인 training-free 보정법
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 3개 session-log 파일 전체

## 2026-04-30T09:00 — wiki-ingest (session-logs, ingested: false 5건)

- Source: session-logs/20260429-080017-0092-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260429-080021-a14d-*.md (arXiv: 2604.24355, "An Aircraft Upset Recovery System with Reinforcement Learning")
  - Skipped: assistant_turns: 0 (LLM 분석 응답이 누락된 세션, 결론 부재로 wiki 생성 보류)
- Source: session-logs/20260429-080059-18fa-*.md (arXiv: 2604.22446, "From Skills to Talent: Organising Heterogeneous Agents as a Real-World Company")
  - Created: wiki/analyses/onemancompany-heterogeneous-agents-organization.md
    — Talent–Container 분리(6 인터페이스) + E²R 트리(AND-FSM 7 불변조건) + 자기 진화(1on1·SOP·HR) → PRDBench 84.67% (Claude-4.5 +15.48pt), 태스크당 $6.91
- Source: session-logs/20260430-080020-6dbb-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "Reply with only: OK" 프롬프트)
- Source: session-logs/20260430-080453-b15a-hi.md
  - Skipped: 내용 없음 (assistant_turns: 0, 단순 "hi")
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 5개 session-log 파일 전체 (skip 4건, 처리 1건, 생성 1건)

## 2026-04-30T18:30 — wiki-ingest (session-logs, ingested: false 17건)

- Source: session-logs/20260430-141817-baa6-Reply-with-only--OK.md
  - Skipped: 내용 없음 (assistant_turns: 0)
- Source: session-logs/20260430-141825-b1dc-*.md, 141857-8b24-*.md, 141931-b081-*.md, 142011-af92-*.md, 142048-7399-*.md (oss-radar cron 의 OSS 분석 프롬프트 5개)
  - Skipped: 모두 assistant_turns: 0 (cron 첫 시도, 분석 응답이 생성되지 않은 세션. 같은 시간대에 oss-radar 프로그램 디버깅이 진행되어 cron 실행이 실패한 결과로 보임)
- Source: session-logs/20260430-144026-977a-Reply-with-only--OK.md
  - Skipped: 내용 없음
- Source: session-logs/20260430-144033-2641-*.md, 144111-c80d-*.md, 144143-2c7c-*.md, 144218-615f-*.md (oss-radar cron 의 OSS 분석 프롬프트 4개)
  - Skipped: 모두 assistant_turns: 0
- Source: session-logs/20260430-144247-00cb-*.md (oss-radar cron, affaan-m/everything-claude-code 분석)
  - Project: oss-radar
  - Created: wiki/analyses/everything-claude-code.md
    — Claude Code 하네스 성능 시스템 (170k stars): 48 agents, 182 skills, AgentShield 1,282 tests + 102 정적 분석 룰, Continuous Learning v2, 크로스 하네스, ECC 2.0 alpha (Rust 컨트롤 플레인), v2.0.0-rc.1 변경, 설치 함정 4가지
- Source: session-logs/20260430-134759-328e-*.md (oss-radar cron 동작 안 됨 디버깅)
  - Project: oss-radar
  - Updated: wiki/projects/oss-radar.md
    — GITHUB_TOKEN 401 만료 → fine-grained PAT 재발급 + plist 평문 → config/.env 분리. GitHub Search API의 topic: OR 미지원 발견 → 카테고리별 N번 쿼리로 우회 (Search 후보 0 → 422)
  - Created: wiki/patterns/launchd-secret-management.md
    — launchd 가 ~/.zshrc 를 안 읽는 메커니즘, 안티 패턴 2종 (plist 평문, zshrc export), config/.env + run.sh source + chmod 600 표준 패턴, fine-grained PAT 권장
  - Created: wiki/analyses/github-search-api-topic-or-limitation.md
    — GitHub Repository Search API의 topic 한정자가 OR 연산자를 거부하는 격리 테스트, 카테고리별 쿼리 + full_name dedupe 우회 코드
- Source: session-logs/20260430-135011-e8eb-*.md (japa Vercel 첫 배포)
  - Project: japa-asset-dashboard
  - Created: wiki/projects/japa-asset-dashboard.md
    — 1인 자산 통합 대시보드 (Next.js 16 + Prisma + Supabase + Yahoo Finance + Gemini), 단일 사용자 인증 (HMAC-SHA256 Web Crypto + Edge runtime middleware), Prisma + Supabase 풀러 모드 분리 (DATABASE_URL Transaction / DIRECT_URL Session), force-dynamic 결정
  - Created: wiki/analyses/nextjs-vercel-supabase-deployment.md
    — Next.js + Vercel + Supabase 통합 배포 7가지 결정 지점 (GitHub App 연동, 업로드 한도, organization 제약, force-dynamic, Pooler 모드 비교, 환경변수 import + 재배포 타이밍, .next/.gitignore). 8항목 종합 점검 리스트
  - Created: wiki/bugs/node-modules-symlink-copy-prisma.md
    — node_modules 폴더 카피 시 .bin/prisma 심볼릭 링크 풀려서 wasm ENOENT, rm -rf node_modules && npm install 로 재생성. cp -a / rsync -aH 예방
- Source: session-logs/20260430-161410-0fcc-*.md (japa 추가 기능: 수익률 % / 시세 새로고침 글로벌 / 누락 종목 / 로그인)
  - Project: japa-asset-dashboard
  - Updated: wiki/projects/japa-asset-dashboard.md (단일 사용자 인증·시세 새로고침 worker pool 6 + retry·수익률 % 표시)
  - Created: wiki/bugs/yahoo-finance-concurrent-silent-fail.md
    — yahoo-finance2 동시 30개 호출 시 일부 응답에 regularMarketPrice 누락 silent fail, worker pool 6 + 250ms 1회 재시도 + UI ⚠️ 가시성 패턴, 진단 명령
- Source: session-logs/20260430-171050-9ee3-*.md (openclaw apc list 확인)
  - Skipped: openclaw 명령어 디스커버리 (apc 는 acp 의 오타, sessions 명령으로 추정), 새로운 설계 판단·버그·패턴 없음
- Source: session-logs/20260430-174408-1a2e-*.md (finance-analysis-nextjs 파악)
  - Project: finance-analysis-nextjs
  - Created: wiki/projects/finance-analysis-nextjs.md
    — 한국 기업 재무분석 대시보드 (Next.js 16 + Prisma + 멀티 AI 프로바이더), PDF/JSON/DART API 입력, 12 슬라이드 + AI/수동 밸류에이션, M&A 활용 계획. 약점 4가지 (밸류에이션 신뢰도, JSON 통째 저장 + FinancialStatement 미사용, PDF 인식율, sessionStorage 탭 분리)
- Updated: wiki/index.md, wiki/log.md
- Marked ingested: true — 17개 session-log 파일 전체 (skip 12건, 처리 5건; 생성: projects 2건, analyses 3건, patterns 1건, bugs 2건; 업데이트: projects 1건)
