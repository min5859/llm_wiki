<!-- kakao-db: ai-summary v1 -->
# AI 요약

## 인사이트·노하우

- **Antigravity IDE 안정성 이슈**: P30 이 2026-05-09 부터 RAM 32GB 환경에서 AG 하나만 띄워도 버벅임/멈춤 발생 보고. P7 은 "쓸 때마다 그래서 안 쓴다", P9 은 "너무 무겁고 불안정". 운용 패턴은 "AG 주력 → 막히면 Cursor 로 넘어가서 전체 수정"으로 두 IDE 병용. 결론적인 평가는 P9·P2 의 "돌고 돌아 VS Code", "MS 인프라 못 이긴다" — 즉 무거운 AI IDE 의 안정성보다 VS Code 기반이 결국 낫다는 정서.
- **TRL 1.4.0 의 `chunked_nll` 손실 함수**: SFT(Supervised Fine-Tuning) 시 VRAM 사용량을 최대 50% 절감. 기존 `nll` 대비 속도 저하 없이 메모리 효율만 개선되었다고 보고 — 개인 GPU 로 더 큰 모델·더 긴 시퀀스를 미세조정할 때 1순위로 고려할 옵션.
- **MCP Python SDK 1.27.1 변경점**: Pydantic 오류 수정, OAuth 클라이언트 메타데이터 처리 개선, httpx 버전 제한, SSE 오류 처리 리팩토링. MCP 기반 도구 만드는 입장에서 안정성 호환성 패치 위주이므로 업그레이드 권장.
- **유튜브 라이브 마이크 인식 실패 → Zoom 우회**: P9 이 유튜브 라이브 진행 중 마이크 인식이 안 되자 즉시 Zoom 회의실로 전환해 청취자 유도. 실시간 방송 장애 시 백업 채널을 미리 준비하는 운용 패턴.

## 추천·자료

- [browser-use/video-use](https://github.com/browser-use/video-use) — Claude Code 를 AI 영상 편집 도구로 확장하는 프로젝트.
- [karpathy.bearblog.dev — Animals vs Ghosts](https://karpathy.bearblog.dev/animals-vs-ghosts/) — Andrej Karpathy 의 블로그 글. 본문 내용은 채팅에 요약되지 않음(불명).
- [CADara](https://cadara.app/) — 브라우저에서만 동작하는 오픈소스 CAD. 설치 없이 3D 모델링·디자인 가능.
- [InvokeAI 6.13.0.rc2](https://github.com/invoke-ai/InvokeAI/releases/tag/v6.13.0.rc2) — 원격 호스팅 외부 제공자 모델 지원, Qwen Image 2.5 / Qwen Image Edit 2.5 추가, turbo 모드 LoRA 포함.
- [CyberSecQwen-4B](https://huggingface.co/blog/lablab-ai-amd-developer-hackathon/cybersecqwen-4b) — 사이버 보안 특화 소형(4B) 모델. 로컬 실행 가능.
- [openclaw/clickclack](https://github.com/openclaw/clickclack) · [regent-vcs/re_gent](https://github.com/regent-vcs/re_gent) — "Git for AI Coding Agents". AI 코딩 에이전트용 버전 관리.
- [PeepShow](https://www.peepshow.dev/) — 용도 불명. P3 가 단순 공유.
- [Oh My Hermes](https://github.com/Salomondiei08/oh-my-hermes) — `oh-my-zsh` 시리즈 패러디성 작명. 구체 용도 불명.
- [BotStreet 소개 글](https://dev.to/miles_aero_e74216b79edc3c/wan-zheng-jie-shao-bo-jie-cong-bot-jing-ji-xi-tong-dao-5-fen-zhong-jie-ru-ai-agent-3h0j) — AI 에이전트 거래 플랫폼. REST API 로 5분 안에 에이전트 등록, A2A(에이전트-투-에이전트) 마케팅 모델로 광고 예산 없이 품질·응답속도로 경쟁. 독립 개발자에게 직접 수익화 채널.

## 의미있는 링크

- [Claude FM — `/radio` 명령으로 Claude Code 에서 라디오 듣기](https://www.threads.com/@unclejobs.ai/post/DYGPGodicxb) — uncle jobs 의 업데이트 공지. P9 가 "정말 멋진 업데이트", P2 가 "주말은 클로드 라디오로 시작하시길" 으로 호응.
- [Lattice Log — Codex Vibe Coding Live](https://lattice-log.vercel.app/blog/2026-05-09-codex-vibe-coding-live) — P2 가 "코덱스 쓰시는 분 참고" 로 명시 추천한 블로그 글.
- [Gowers 블로그 — ChatGPT 5.5 Pro 와 수학 연구 경험](https://gowers.wordpress.com/2026/05/08/a-recent-experience-with-chatgpt-5-5-pro) — Timothy Gowers(필즈상 수상자) 가 ChatGPT 5.5 Pro 로 PhD 수준 수학 연구를 단시간에 수행한 경험. 채팅 큐레이션에서 "수학계에 큰 충격" 으로 소개.
- [Luke Curley — WebRTC 의 문제](https://simonwillison.net/2026/May/9/luke-curley) — WebRTC 가 저대역 환경에서 데이터를 조각내 보내는 설계 때문에 정확성이 중요한 통신에 부적합하다는 지적.
- [Lattice Live — 오전판](https://lattice-log.vercel.app/news/2026-05-09-morning) · [오후판](https://lattice-log.vercel.app/news/2026-05-09-afternoon) — 일일 AI 큐레이션 아카이브.

## 결정·약속·일정

- **2026-05-09 저녁 — uncle jobs 유튜브 라이브 → Zoom 전환**: 마이크 문제로 [Zoom 회의실](https://us06web.zoom.us/j/89041968660?pwd=khopRiSDziTSkbwvJVcPqndeKQtzBU.1) 로 이동해 진행. 21시경 종료.
- **녹화본 처리 방침**: 유료 제공 없음. 오픈방 한정 공유도 하지 않음. P9 가 "글쓰기 팁·프롬프트를 녹여 다시 촬영해 유튜브 콘텐츠로 정식 업로드" 하기로 약속. 시점은 미정.

## 반응이 컸던 화제

- **Antigravity IDE 의 안정성**: P30 의 버벅임 호소를 시작으로 P7·P9·P31·P18·P2 등 다수가 참여. "쓸 때마다 그래서 안 씀" vs "아직 잘 쓰는 중" 으로 의견이 갈렸지만, 대체로 무겁고 불안정하다는 합의 — VS Code 의 견고함을 재확인하는 흐름으로 정리됨.
- **uncle jobs 라이브 시청 동선**: 유튜브 마이크 실패 → Zoom 이동 과정에서 다수가 링크 재공유·"가즈아" 등으로 참여. 종료 후에는 "녹화본 보고 싶다" 는 요청과 P9 의 유튜브 재촬영 약속으로 마무리.

## 자동화·시스템 적용 후보

- **Lattice Live 큐레이션 포맷**: P2 가 오전/오후 두 차례 같은 포맷으로 투입.
  - 섹션 = `▸ AI / Models / Papers` + `▸ Dev / Tools / Community`
  - 각 항목 = 제목 → 본문 요약 → `왜 지금:` → `써먹기:` → `출처: <slug> · <URL>`
  - 끝에 "전체 보기 + 실시간 오픈카톡" 링크.
  - 일일 AI/도구 뉴스 자동 큐레이션 시 그대로 차용 가능한 템플릿.
- **Claude Code 슬래시 커맨드로 외부 콘텐츠 소비**: `/radio` 식으로 CLI 안에서 미디어를 트는 패턴. 본 프로젝트의 `kakao-db` 같은 CLI 도구에서도 보조 명령(예: 통계 뷰, 친구 목록 라디오 식 낭독 등) 으로 응용 여지.

## 질문·미해결

- **Antigravity 의 버벅임 원인**: 사용자 환경 문제인지, 최근 빌드 회귀인지 미확인. P30 본인도 "내 컴퓨터 문제인가" 로 시작했고 해결 보고는 없음.
- **uncle jobs 라이브 재촬영본의 공개 시점·프롬프트 동봉 여부**: P14 의 "프롬프트는 추후 제공되나요?" 에 P9 가 "물론" 이라 답했지만 일정·형식 미정.
- **PeepShow / Oh My Hermes 의 구체 용도**: 링크만 공유되고 설명 없이 흘러감.

---

## 요약 통계

- 메시지 수: 44
- 기간: 2026-05-09 00:36 ~ 2026-05-09 22:26

### URL (25개)

- https://github.com/browser-use/video-use
- https://karpathy.bearblog.dev/animals-vs-ghosts/
- https://youtu.be/X9k53P-2SOg?si=uFHXTL-fCzCGpzn4
- https://longblack.co/note/1974?ticket=NT26191f6f279dee5d295701f313b021fab7a4
- https://www.threads.com/@unclejobs.ai/post/DYGPGodicxb
- https://github.com/huggingface/trl/releases/tag/v1.4.0
- https://github.com/modelcontextprotocol/python-sdk/releases/tag/v1.27.1
- https://huggingface.co/blog/lablab-ai-amd-developer-hackathon/cybersecqwen-4b
- https://github.com/invoke-ai/InvokeAI/releases/tag/v6.13.0.rc2
- https://cadara.app/
- ... +15 more

### 멘션

- @all: 1
- @unclejobs: 1
- @엉클잡스: 1

---

# Fronmpt Academy — 2026-05-09

- 메시지 수: 44

**00:36** [P3]
Claude Code : AI 영상 편집 도구로 확장

<https://github.com/browser-use/video-use>
안드레 카파시

<https://karpathy.bearblog.dev/animals-vs-ghosts/>

**00:48** [P3]
<https://youtu.be/X9k53P-2SOg?si=uFHXTL-fCzCGpzn4>

**08:11** [P3]
<https://longblack.co/note/1974?ticket=NT26191f6f279dee5d295701f313b021fab7a4>

**09:21** [P9]
정말 .. 멋진 업데이트 입니다
클로드코드에 /radio로 
클로드 fm을 들을 수 있습니다
<https://www.threads.com/@unclejobs.ai/post/DYGPGodicxb>

**09:21** [P2]
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ
좋은 주말입니다

**09:21** [P9]
이런거나 업뎃하고 아주 좋네여 좋아

**09:21** [P2]
주말은 클로드 라디오로 시작하시길 바랍니다

**10:05** [P2]
📡 Lattice Live 

▸ AI / Models / Papers

✱ TRL 1.4.0, SFT VRAM 50% 절감
trl 라이브러리가 1.4.0으로 업데이트되며 SFT(Supervised Fine-Tuning) 시 VRAM 사용량을 최대 50%까지 줄이는 'chunked_nll' 손실 함수 옵션을 추가했습니다. 이는 대규모 언어 모델 미세조정 시 메모리 병목 현상을 완화하여, 더 긴 시퀀스 길이나 더 큰 모델 학습을 가능하게 합니다. 기존 'nll' 방식 대비 속도 저하 없이 메모리 효율성을 극대화한 점이 주목할 만합니다.
왜 지금: LLM 성능 향상과 함께 VRAM 요구량 증가는 개발자들에게 큰 부담이었으나, 이 업데이트로 모델 학습 및 실험의 접근성이 높아졌습니다.
써먹기: 개인 GPU 환경에서 더 큰 LLM을 미세조정하거나, 긴 문맥을 처리하는 모델을 사이드 프로젝트에 도입할 때 유용합니다.
출처: trl_rel · <https://github.com/huggingface/trl/releases/tag/v1.4.0>

✱ 모델 컨텍스트 프로토콜 Python SDK 1.27.1 버전
모델 컨텍스트 프로토콜의 Python SDK가 1.27.1 버전으로 업데이트되었습니다. 이 버전에서는 Pydantic 관련 오류 수정, OAuth 클라이언트 메타데이터 처리 개선, httpx 버전 제한, SSE 오류 처리 리팩토링 등이 포함되어 있습니다. 이러한 변경 사항은 SDK의 안정성과 호환성을 향상시키는 데 도움이 됩니다. 개발자들은 이 새로운 버전을 사용하여 자신의 프로젝트에서 모델 컨텍스트 프로토콜을 더 안정적으로 사용할 수 있습니다.
왜 지금: 최신 버전의 SDK를 사용하여 프로젝트의 안정성과 호환성을 유지하기 위해
써먹기: vibe-coder는 사이드 프로젝트에서 모델 컨텍스트 프로토콜을 사용하여 데이터 처리와 모델 관리를 더 효율적으로 할 수 있습니다.
출처: mcp_python_rel · <https://github.com/modelcontextprotocol/python-sdk/releases/tag/v1.27.1>

✱ 사이버 보안 모델 CyberSecQwen-4B
CyberSecQwen-4B는 사이버 보안을 위한 작은 규모의 전문 모델입니다. 기존의 대형 모델은 비용이 많이 들고, 외부 데이터 센터에 의존하며, 실제 보안 위협에 대한 처리가 부족합니다. CyberSecQwen-4B는 이러한 문제를 해결하기 위해 설계되었으며, 로컬에서 실행할 수 있고, 특정 작업에 최적화되어 있습니다. 이 모델은 사이버 보안 분야에서 자동화와 효율성을 높이는 데 도움이 될 수 있습니다.
왜 지금: 최근 사이버 보안 위협이 증가하고 있기 때문에, 효과적인 대응을 위해 새로운 접근 방식이 필요합니다.
써먹기: vibe-coder는 사이드 프로젝트에서 CyberSecQwen-4B 모델을 사용하여 보안 관련 작업을 자동화하고, 효율성을 높일 수 있습니다.
출처: hf_blog · <https://huggingface.co/blog/lablab-ai-amd-developer-hackathon/cybersecqwen-4b>

✱ 인보크AI 6.13.0 출시
인보크AI 6.13.0 버전이 출시되었습니다. 이 버전에서는 원격으로 호스팅되는 외부 제공자의 모델을 지원하며, 새로운 로컬 모델과 버그 수정이 포함되어 있습니다. Qwen Image 2.5와 Qwen Image Edit 2.5 모델이 추가되었으며, turbo 모드를 지원하는 LoRA 모델도 포함되어 있습니다. 이러한 모델은 이미지 생성과 편집을 위한 새로운 기능을 제공합니다.
왜 지금: 최신 모델과 기능을 사용하여 이미지 생성과 편집 작업을 개선할 수 있습니다.
써먹기: 사이드 프로젝트에서 인보크AI 6.13.0를 사용하여 이미지 생성과 편집 작업을 자동화할 수 있습니다.
출처: invoke_ai_rel · <https://github.com/invoke-ai/InvokeAI/releases/tag/v6.13.0.rc2>

▸ Dev / Tools / Community

✱ 브라우저 기반 오픈소스 CAD
CADara는 브라우저에서만 작동하는 오픈소스 CAD 소프트웨어입니다. 사용자는 브라우저에서 3D 모델링과 디자인을 할 수 있습니다. CADara는 사용자들이 쉽게 접근할 수 있는 CAD 도구를 제공하여 더 많은 사람들이 3D 모델링과 디자인을 할 수 있도록 지원합니다. CADara의 오픈소스 특성으로 인해 개발자들이 함께 참여하여 기능을 추가하고 개선할 수 있습니다. 브라우저 기반으로 작동하기 때문에 사용자가 별도의 소프트웨어를 설치할 필요가 없습니다.
왜 지금: 브라우저 기반 CAD로 더 많은 사람들이 3D 모델링과 디자인을 할 수 있도록 지원
써먹기: vibe-coder는 CADara를 사용하여 사이드 프로젝트에서 3D 모델링과 디자인을 할 수 있습니다.
출처: lobsters · <https://cadara.app/>

전체 보기: <https://lattice-log.vercel.app/news/2026-05-09-morning>

오픈카톡 실시간 AI 큐레이션: <https://open.kakao.com/o/peJMmUsi>

**10:33** [P3]
OpenClaw에서 배포하는~~

<https://github.com/openclaw/clickclack>
Git for AI Coding Agents

<https://github.com/regent-vcs/re_gent>

**11:04** [P19]
오늘 유튜브 강의 준비가 한창이시군요. 메일로 커밋된게 날라옵니다 ㅎㅎㅎ

**11:10** [P9]
세컨드 클코 업뎃햇는데 메일로 받으시나요 ?! ㅋㅋㅋ

**11:12** [P3]
PeepShow

<https://www.peepshow.dev/>

**11:17** [P19]
네 ㅋㅋ

**11:19** [P3]
<https://www.instagram.com/reel/DYDRdHBS_9o/>

**11:33** [P2]
<https://lattice-log.vercel.app/blog/2026-05-09-codex-vibe-coding-live>
코덱스 쓰시는 분 참고.

**11:57** [P30]
저.  혹시 안티그래비티 사용하시는 분들..  저는 어제 부터 자꾸 버벅거리고.  멈추는 현상이 있는데.  그런증상 없으신가요?  제컴퓨터가 문제인가 싶어서요 ㅜ.ㅜ  ram 32gb에 ag하나만 사용하는데도  갑자기 어제부터 이상한것 같습니다... 

**12:07** [P7]
전 안티 쓸 때 마다 그래서 안쓰고 있습니다

**13:15** [P9]
안티그라비티 너무 무겁긴해요 
불안정하고 

**13:17** [P31]
그냥 터미널에서 사용하는 것과 다른가요?

**13:20** [P30]
그동안은  이런 경우가 없었는데 어제부터 그럽네요.. 커서하고 안티그래비티하고 두가지가 비슷한 환경이라.. 주로 안티로 작업하고. 필요에 따라 커서로 전체적으로 수정하고 하는데.  잘하는 건지 모르겠습니다.   안티에서 막히면 커서로 넘어간다고 협박도 하고요. 

**13:24** [P9]
돌고돌아 vs code 같습니다 

**13:28** [P2]
비에스코드 
못이김
마소 인프라 무시못합니다

**13:32** [P18]
저도 아직 잘쓰는 중이에요 그래비키

**14:07** [P2]
📡 Lattice Live · 오후


▸ AI / Models / Papers

✱ WebRTC의 문제점
WebRTC는 낮은 네트워크 상황에서 데이터를 조각내어 전송하는 방식으로 설계되어 있다. 이는 실시간 통신에 적합하지만, 데이터의 정확성이 중요한 경우에는 문제가 될 수 있다. Luke Curley는 WebRTC의 이 문제점을 지적하며, 더 나은 대안을 찾는 것이 필요하다고 주장한다. WebRTC의 한계를 이해하는 것은 데이터 통신의 안정성과 정확성을 높이는 데 중요하다. WebRTC의 문제점은 데이터의 손실과 지연을 초래할 수 있기 때문에, 개발자들은 이에 대한 대안을 찾는 것이 필요하다.
왜 지금: 현재 데이터 통신의 안정성과 정확성이 중요해지는 상황에서 WebRTC의 문제점을 이해하는 것이 필요하다.
써먹기: vibe-coder는 사이드 프로젝트에서 데이터 통신의 안정성과 정확성을 높이기 위해 WebRTC의 대안을 찾아 적용할 수 있다.
출처: simonw · <https://simonwillison.net/2026/May/9/luke-curley>

✱ 클라우드 코드 대형 프로젝트 시작 전략
클라우드 코드를 사용하여 대형 프로젝트를 시작할 때, 효과적으로 아이디어를 조직화하고 구현하는 방법을 고려해야 합니다. 큰 프로젝트는 작은 프로젝트와 달리, 복잡성과 규모가 크기 때문에 초기에 계획과 설계가 중요합니다. 이때 클라우드 코드의 강점을 활용하여 프로젝트의 요구사항을 명확하게 정의하고, 이를 기반으로 구체적인 설계와 구현 계획을 수립할 수 있습니다.
왜 지금: 대형 프로젝트의 성공을 결정짓는 초기 설계와 계획이 중요하기 때문에
써먹기: 클라우드 코드의 기능을 활용하여 프로젝트의 요구사항을 분석하고, 이를 기반으로 효율적인 설계와 구현 계획을 수립할 수 있습니다.
출처: r_claudeai · <https://www.reddit.com/r/ClaudeAI/comments/1t7ta88/how_do_you_usually_get_around_when_starting_big>

✱ AI 에이전트 경제 시스템, 봇스트리트
봇스트리트는 AI 에이전트 중심의 경제 시스템이자 서비스 거래 플랫폼입니다. 개발자는 자신의 에이전트를 연결하여 서비스를 등록하고, 작업을 수주하며 수익을 창출할 수 있습니다. 광고 예산 대신 서비스 품질과 응답 속도로 경쟁하는 A2A 마케팅 모델은 독립 개발자에게 특히 유리합니다. REST API를 통해 5분 만에 에이전트를 연동, 실제 수익 활동을 시작할 수 있습니다.
왜 지금: AI 에이전트의 상업화 기회가 확대되며, 개발자에게 직접적인 수익 모델을 제공하기 때문입니다.
써먹기: 사이드 프로젝트로 개발한 AI 에이전트를 BotStreet에 등록하여 실제 수요를 발굴하고 수익화할 수 있습니다.
출처: devto_ai · <https://dev.to/miles_aero_e74216b79edc3c/wan-zheng-jie-shao-bo-jie-cong-bot-jing-ji-xi-tong-dao-5-fen-zhong-jie-ru-ai-agent-3h0j>

▸ Dev / Tools / Community

✱ ChatGPT 5.5 Pro, 수학 연구에 혁신
ChatGPT 5.5 Pro는 최근 수학 연구에 큰 영향을 미치고 있다. 이 모델은 PhD 수준의 연구를 단시간 내에 수행할 수 있으며, 이는 수학계에 큰 충격을 주고 있다. 기존의 언어 모델은 기존 연구 결과를 바탕으로 문제를 해결하였지만, ChatGPT 5.5 Pro는 새로운 접근 방법을 제시하고 있다. 이는 수학 연구의 새로운 가능성을 열어주고 있다.
왜 지금: 수학 연구의 새로운 가능성을 열어준다
써먹기: vibe-coder는 ChatGPT 5.5 Pro를 사용하여 수학 연구에 대한 사이드 프로젝트를 진행할 수 있다
출처: hn_top · <https://gowers.wordpress.com/2026/05/08/a-recent-experience-with-chatgpt-5-5-pro>

전체 보기: <https://lattice-log.vercel.app/news/2026-05-09-afternoon>

실시간 오픈카톡: <https://open.kakao.com/o/peJMmUsi>


**14:47** [P3]
Oh My Hermes

<https://github.com/Salomondiei08/oh-my-hermes>

**14:48** [P26]
오 시리즈가..ㅋㅋ

**14:50** [P9]
<https://www.youtube.com/watch?app=desktop&v=lqAedZhgMo0>
지금 라이브 중임니다 ㅋㅋ

**14:56** [P26]
피자 맛있겠네요..

**20:03** [P32]
라이브 하고 계신가요
어디서보나용

**20:04** [P21]
소리나오나요?

**20:05** [P9]
아오 음성 안되서 
줌으로 전달 드릴게요
<https://us06web.zoom.us/j/89041968660?pwd=khopRiSDziTSkbwvJVcPqndeKQtzBU.1>
유튜브 라이브에서 마이크 인식이 안되네요
@all 
라이브 궁금하신분들은 여기서 뵈요 

<https://us06web.zoom.us/j/89041968660?pwd=khopRiSDziTSkbwvJVcPqndeKQtzBU.1>

**20:13** [P14]
가즈아

**20:22** [P33]
<https://us06web.zoom.us/j/89041968660?pwd=khopRiSDziTSkbwvJVcPqndeKQtzBU.1>

**20:59** [P25]
뿌엥 녹화본 유료제공은 없을꺼요 ㅠ

**21:00** [P13]
아 지금 녹화중입니다
아 ㅋㅋ 잠시만요 ㅋㅋ
@엉클잡스  님께서 라이브 종료 후  말씀해줄것같아요 ㅋ

**21:34** [P34]
이 오픈방회원들에게라도 녹화 파일 볼 수 있게 해주시면 좋을 것 같아요 ㅠㅠ. 

**21:44** [P9]
아는 분들이 너무 많이 들어와서 .. 
유튜브 콘텐츠로 올려보겠습니다! 
글쓰기 팁이라던가 프롬프트도 녹여내서 다시 촬영 할 예정입니다! 

**21:45** [P34]
감사합니다 !!

**21:45** [P14]
수고하셨습니다

**21:48** [P9]
오늘은 반쯤은 소통하러 켠거라 ㅎㅎ

**21:52** [P14]
프롬프트는 추후 제공되나요?

**22:02** [P25]
감사해요 오늘 어버이날 시부모님과 식사하느라 못들어갔거든요 ㅠㅠ

**22:07** [P9]
물론이져 
ㅋㅋ 유튜브로 보답드리겟나이다

**22:26** [P25]
감사합니다 ㅠㅠ

