# Graph Report - ./wiki  (2026-04-23)

## Corpus Check
- 27 files · ~15,296 words
- Verdict: corpus is large enough that graph structure adds value.

## Summary
- 142 nodes · 179 edges · 11 communities detected
- Extraction: 90% EXTRACTED · 10% INFERRED · 0% AMBIGUOUS · INFERRED: 18 edges (avg confidence: 0.5)
- Token cost: 12,500 input · 2,800 output

## Community Hubs (Navigation)
- [[_COMMUNITY_AI 에이전트 기초·하네스 엔지니어링|AI 에이전트 기초·하네스 엔지니어링]]
- [[_COMMUNITY_Claude Code 기본 명령·UI|Claude Code 기본 명령·UI]]
- [[_COMMUNITY_기업 보안·AWS Bedrock|기업 보안·AWS Bedrock]]
- [[_COMMUNITY_OpenClaw·자동화 파이프라인|OpenClaw·자동화 파이프라인]]
- [[_COMMUNITY_이미지 생성·확산 모델 분석|이미지 생성·확산 모델 분석]]
- [[_COMMUNITY_Agent Teams·tmux 협업|Agent Teams·tmux 협업]]
- [[_COMMUNITY_자율주행·VLA 논문|자율주행·VLA 논문]]
- [[_COMMUNITY_SoC OTF 하드웨어|SoC OTF 하드웨어]]
- [[_COMMUNITY_AI 쇼츠 영상 제작|AI 쇼츠 영상 제작]]
- [[_COMMUNITY_AI 철학·활용 수준|AI 철학·활용 수준]]
- [[_COMMUNITY_macOS 키보드 단축키|macOS 키보드 단축키]]

## God Nodes (most connected - your core abstractions)
1. `OpenClaw 에이전트 아키텍처` - 11 edges
2. `MeanFlow Text-to-Image Extension (EMF)` - 10 edges
3. `Claude Code 개요` - 10 edges
4. `Claude Code 고급 기능 — MCP·Hooks·SubAgents·Agent Teams` - 9 edges
5. `Claude Code 스킬 및 공식 플러그인 가이드` - 9 edges
6. `AI 에이전트 기초` - 9 edges
7. `Wiki Index` - 8 edges
8. `CLAUDE.md 완전 가이드 — 작성법·패턴·템플릿` - 8 edges
9. `Claude Code 초기 설정 및 하네스 엔지니어링` - 8 edges
10. `Claude Code 기본 사용법` - 8 edges

## Surprising Connections (you probably didn't know these)
- `Wiki Index` --references--> `Claude Code Agent Teams와 tmux 운용`  [INFERRED]
  wiki/index.md → wiki/patterns/claude-code-agent-teams-tmux.md
- `CLAUDE.md 4계층 강제력` --conceptually_related_to--> `Hooks Event System`  [INFERRED]
  wiki/patterns/claude-md-guide.md → wiki/patterns/claude-code-advanced.md
- `AGENTS.md 서브에이전트 지시서 (claude-md-guide)` --conceptually_related_to--> `AGENTS.md 서브에이전트 지시서`  [INFERRED]
  wiki/patterns/claude-md-guide.md → wiki/patterns/claude-code-advanced.md
- `AI 활용 수준 분류 (초급/중급/고급)` --conceptually_related_to--> `하네스 엔지니어링 (Harness Engineering)`  [INFERRED]
  wiki/concepts/ai-usage-philosophy.md → wiki/concepts/ai-agent-basics.md
- `Operation Log` --references--> `MeanFlow Text-to-Image Extension (EMF)`  [EXTRACTED]
  wiki/log.md → wiki/analyses/meanflow-text-to-image.md

## Communities

### Community 0 - "AI 에이전트 기초·하네스 엔지니어링"
Cohesion: 0.09
Nodes (28): AI 에이전트 3계층 구조 (두뇌/지시서/손발), A2A (Agent-to-Agent) 프로토콜, AI 에이전트 자율도 레벨 1-5, 하네스 엔지니어링 (Harness Engineering), MCP (Model Context Protocol) — AI 에이전트, ReAct 패턴 (Think→Act→Observe), AI 에이전트 기초, Claude Code 4세대 AI 코딩 에이전트 (+20 more)

### Community 1 - "Claude Code 기본 명령·UI"
Cohesion: 0.11
Nodes (19): /clear 대화 초기화, /compact 대화 압축, /loop 자동화 파이프라인, Plan 모드 (/plan), /remote-control 스마트폰 원격 조작, /sandbox 격리 환경 실행, Claude Code 기본 사용법, Claude Code Plan 모드 (+11 more)

### Community 2 - "기업 보안·AWS Bedrock"
Cohesion: 0.14
Nodes (17): AWS Bedrock for Claude Code, Claude Code 기업 보안 도입과 AWS Bedrock, Managed Enterprise Adoption Stages, Enterprise Permission Design, CLAUDE.md 위치와 범위 계층, 하네스 엔지니어링 6요소, Claude Code 초기 설정 및 하네스 엔지니어링, Permissions 설정 (allow/deny) (+9 more)

### Community 3 - "OpenClaw·자동화 파이프라인"
Cohesion: 0.24
Nodes (15): macOS LaunchAgent Catchup Behavior, Claude Code (CC), gieok Hook→LLM→Wiki→Git Pipeline, macOS LaunchAgent (launchd scheduling), Obsidian Wiki Vault, gieok Concept — Claude Code Second Brain, split throttle 드로다운 버그 수정 (c5dc818), 설정 파일 동기화 규칙 (scoring.yaml + trading.yaml) (+7 more)

### Community 4 - "이미지 생성·확산 모델 분석"
Cohesion: 0.19
Nodes (14): Diffusion SNR-t Bias Analysis, MeanFlow Text-to-Image Extension (EMF), arXiv:2505.13447 — MeanFlow (Geng et al., 2025), arXiv:2510.15857 — BLIP3o-NEXT (Chen et al., 2025), arXiv:2604.16044 — Elucidating SNR-t Bias in DPMs, arXiv:2604.18168 — EMF: Extending MeanFlow to Text-to-Image, BLIP3o-NEXT (Vision-Language Aligned Text Encoder), DCW — Wavelet Domain Difference Correction (+6 more)

### Community 5 - "Agent Teams·tmux 협업"
Cohesion: 0.19
Nodes (14): Git Worktree 병렬 개발, Agent Teams 역할 구성, tmux 기반 Agent Teams 운용, Claude Code Agent Teams와 tmux 운용, Agent Teams Pattern, AGENTS.md 서브에이전트 지시서, CI/CD Pipeline Integration with Claude Code, Hooks Event System (+6 more)

### Community 6 - "자율주행·VLA 논문"
Cohesion: 0.17
Nodes (13): MeanFlow text-to-image (참조 논문), OneVL 벤치마크 (NAVSIM, ROADWork, Impromptu, Alpamayo-R1), OneVL Latent Chain-of-Thought, OneVL — 자율주행 잠재 추론 모델, OneVL Prefill Inference (Auxiliary Decoder 생략), OneVL 3단계 훈련 파이프라인, VLA (Vision-Language-Action model) 자율주행, Tstars-VTON Benchmark (+5 more)

### Community 7 - "SoC OTF 하드웨어"
Cohesion: 0.32
Nodes (8): SoC On-The-Fly (OTF) Sensor→AP 직접 연결, OTF 3A 레이턴시 타이밍 모델, AXI-Stream OTF 인터페이스 버스, OTF 백프레셔(Backpressure) 처리, OTF 클럭 도메인 경계 (CDC), ISP (Image Signal Processor) OTF 핵심 IP, OTF 파이프라인 종료 순서 (상류→하류), OTF 파이프라인 초기화 순서 (하류→상류)

### Community 8 - "AI 쇼츠 영상 제작"
Cohesion: 0.33
Nodes (6): Claude Code로 AI 쇼츠 영상 대량 제작, 쇼츠 스크립트 생성 프롬프트 구조, AI 쇼츠 제작 전체 흐름, /loop 파일 기반 지시 구조, Claude Code /loop 자동화 패턴, /loop 좋은 사용 사례

### Community 9 - "AI 철학·활용 수준"
Cohesion: 0.4
Nodes (5): AI 사용법 철학, AI 활용 수준 분류 (초급/중급/고급), 컨텍스트 설계 (Context Design), 리버럴 아츠 (Liberal Arts) — AI 시대, 언어화 능력 (Verbalization)

### Community 10 - "macOS 키보드 단축키"
Cohesion: 0.67
Nodes (3): 맥 키보드 단축키 (윈도우 사용자용), Command(⌘) 키 중심 단축키 체계, 터미널 Ctrl+C 프로세스 중단 (맥 주의사항)

## Knowledge Gaps
- **65 isolated node(s):** `arXiv:2604.18168 — EMF: Extending MeanFlow to Text-to-Image`, `arXiv:2604.16044 — Elucidating SNR-t Bias in DPMs`, `Text Encoder Discriminability (for Few-Step Generation)`, `Text Encoder Disentanglement (Semantic Separation)`, `BLIP3o-NEXT (Vision-Language Aligned Text Encoder)` (+60 more)
  These have ≤1 connection - possible missing edges or undocumented components.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `Wiki Index` connect `OpenClaw·자동화 파이프라인` to `이미지 생성·확산 모델 분석`, `Agent Teams·tmux 협업`?**
  _High betweenness centrality (0.099) - this node is a cross-community bridge._
- **Why does `Claude Code Agent Teams와 tmux 운용` connect `Agent Teams·tmux 협업` to `OpenClaw·자동화 파이프라인`?**
  _High betweenness centrality (0.097) - this node is a cross-community bridge._
- **Why does `Claude Code 개요` connect `AI 에이전트 기초·하네스 엔지니어링` to `Claude Code 기본 명령·UI`?**
  _High betweenness centrality (0.076) - this node is a cross-community bridge._
- **What connects `arXiv:2604.18168 — EMF: Extending MeanFlow to Text-to-Image`, `arXiv:2604.16044 — Elucidating SNR-t Bias in DPMs`, `Text Encoder Discriminability (for Few-Step Generation)` to the rest of the system?**
  _65 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `AI 에이전트 기초·하네스 엔지니어링` be split into smaller, more focused modules?**
  _Cohesion score 0.09 - nodes in this community are weakly interconnected._
- **Should `Claude Code 기본 명령·UI` be split into smaller, more focused modules?**
  _Cohesion score 0.11 - nodes in this community are weakly interconnected._
- **Should `기업 보안·AWS Bedrock` be split into smaller, more focused modules?**
  _Cohesion score 0.14 - nodes in this community are weakly interconnected._