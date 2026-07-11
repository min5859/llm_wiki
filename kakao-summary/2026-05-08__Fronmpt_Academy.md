<!-- kakao-db: ai-summary v1 -->
# AI 요약

## 인사이트·노하우

- **엉클잡스(응제)의 사업 여정**: 2년 전엔 도메인 설정도 모르던 상태였으나, "판매하려고 만들다 보니" 이커머스를 주전공처럼 익히게 됨. 게장 판매 경험까지 있고, 홍게는 금어기 거의 없어 유리하다는 노하우 보유.
- **수익성 ≠ 기능 화려함**: "수익성 있는 제품은 화려한 기능이 중요한 게 아닙니다. 노션으로 구독제 서비스 만들어서 수익화 했습니다" (엉클잡스).
- **YouTube 채널 폐간 이유**: "수익성이 낮아서 그럴거에요. 사람들은 콘텐츠 소비를 스토리에 치중되서 하기 때매" — 정보보다 스토리 중심 큐레이션이 필요.
- **셀프 마케팅 철학**: "겸손할 거면 그냥 시작을 말라. 어차피 똑똑한 애들 천지니" / "1인 브랜드면 진짜 겸손은 사치" / "돈 자랑만 아니면 뭐든 자랑해야" — 한국식 겸손 vs 서양식 셀프 어필 비교 토론.
- **AI 시대 일하는 방식 전환**: Claude Code 헤드 Boris Cherny가 올해 직접 코드 안 쓰고 휴대폰에서 여러 세션·에이전트를 관리하며 매일 수십 개 PR을 합친다고 발언. 핵심은 "코드 작성"이 아니라 "무엇을 AI에 맡기고, 어떻게 검증하고, 어떤 기준으로 결과를 승인하느냐".
- **Claude Code Auto-dream 확인 방법**: 세션에서 `/memory` 입력 → "Auto-dream: on" 토글 보이면 백그라운드에서 메모리 파일 통합 작동 중. (Claude Managed Agents의 정식 Dreaming 기능은 별도로 developer access program 필요)
- **클로드 vs 챗GPT 분업**: 코딩·장문 분석은 클로드, 데이터 처리·이미지 생성·웹 검색은 챗GPT. 클로드로 설계/기획 → 챗GPT로 시각화/검증 병행 전략 권장.
- **N64 애드디티브 블렌딩 한계**: RDP가 애드디티브 블렌딩을 지원하지만 결과를 클램핑하지 않아 의도된 효과를 못 냄 (PS1과의 그래픽 차이 배경).
- **ASCII에서 대문자 Z 다음 바로 소문자 a가 안 오는 이유**: 2의 거듭제곱 비트 연산 효율을 위해 특수문자를 사이에 끼워 넣은 설계 결정.
- **LiteLLM 보안 강화**: 모든 도커 이미지가 cosign으로 서명됨. `cosign verify`로 무결성 검증 가능.
- **2026-05-08 클로드 장애 대응**: 같은 날 오류율 상승 사고 발생. 프로덕션에는 재시도 + 캐시 기반 폴백 로직 필수.
- **goose 책 후기 (엉클잡스)**: "본인이 만드신 cli인 moai에 후반부가 집중되어 있군여. 다른 거랑 비교해 주셨음 더 좋았을 텐데 아쉽" — 책 선택 시 참고.

## 추천·자료

### 도구·라이브러리
- **[Sangse.page](https://Sangse.page)** — 엉클잡스 본인의 상세페이지 자동 생성 서비스. 무료 2회. 가벼운 키워드만 입력해도 AI가 주요 특징·혜택·문제점·마케팅 에셋까지 작성.
- **[Remotion](https://www.remotion.dev/)** + **[Remotion Bits](https://remotion-bits.dev/docs/bits/remotion-bits/)** — 코드 기반 모션 그래픽. Remotion Bits는 더 인터랙티브한 효과 제공.
- **[HyperFrames](https://github.com/heygen-com/hyperframes)** — HTML/CSS/GSAP를 동영상으로 변환. LP·웹 페이지 표현에 강함.
- **[Editframe](https://editframe.com/)** — 소재·음성·자막·타임라인 편집 특화.
- **StemDeck** — Demucs 모델로 YouTube 오디오를 6트랙 분리, DAW 유사 인터페이스. [GitHub](https://github.com/thcp/stemdeck).
- **Codex Chrome 확장** — [Chrome Web Store](https://chromewebstore.google.com/detail/codex/hehggadaopoacecdllhhajmbjkdcmajg?hl=ko).
- **BlueKiwi** ([repo](https://github.com/dandacompany/bluekiwi)) — AI Agent Workflow Engine.
- **memento** ([repo](https://github.com/dandacompany/memento)) — AI 코딩 에이전트용 양방향 메모리 동기화.
- **Mojo v1.0.0b1** — Python 친화 문법으로 C·Rust 수준 성능을 노리는 AI/시스템 언어 첫 베타 ([릴리즈](https://mojolang.org/releases/v1.0.0b1)). MLIR 위에서 GPU 커널·ML 연산 최적화에 적합.

### 책
- **시키는 기술** (엉클잡스 저, 약 293p) — 앞 80p 무료. 후속 **위임의 기술**(에이전트 위임), **관리의 기술**(토큰·세션·컨텍스트 관리) 시리즈 예고. [구매 링크](https://www.latpeed.com/products/k9vwB).
- **클로드 코드로 시작하는 실전 에이전틱 코딩** (goose 저) — [YES24](https://www.yes24.com/product/goods/189211422). 단, 후반부가 저자 본인 CLI(moai)에 집중되어 있다는 평.
- **Mathematics for Computer Science (MIT 6.042)** — [PDF](https://courses.csail.mit.edu/6.042/spring18/mcs.pdf).
- **東京大学 Python Programming 교재** — [utokyo-ipp](https://utokyo-ipp.github.io/index.html).

### 콘텐츠 사이트
- **[Starter Story](https://www.youtube.com/watch?v=D4fkiQfzw_I)** — 1인 창업자 인터뷰 채널, AI FOMO에 휩쓸리지 않는 사업 방향성. 응제님은 구독자 1천 명 시절부터 시청.
- **[Long Black](https://longblack.co)** — 양질의 비즈니스/문화 큐레이션 노트.
- **중국어 추천 사이트**: [Buzzing](https://buzzing.cc), [SoPilot](https://sopilot.net/zh/hot-tweets), [NewsNow](https://newsnow.busiyi.world), [今日热榜](https://tophub.today), [V2EX](https://v2ex.com), [少数派](https://sspai.com), [小众软件](https://appinn.com) — 중화권 테크 트렌드 추적용.

### AI 리서치 도구 모음
- Perplexity.ai (빠른 리서치), Scite.ai (출처 검증), Elicit.org (논문 요약), Scholarcy.com (자동 요약), Consensus.app (근거 검색), ResearchRabbit.ai (관련 연구 발견), Litmaps.com (인용 시각화), SemanticScholar.org (심층 논문 검색).

## 의미있는 링크

- [엉클잡스 — AI 인프라와 에이전트 표준화 동시 부상](https://lattice-log.vercel.app/blog/20260508-ai-%EC%9D%B8%ED%94%84%EB%9D%BC%EC%99%80-%EC%97%90%EC%9D%B4%EC%A0%84%ED%8A%B8-%ED%91%9C%EC%A4%80%ED%99%94-%EB%8F%99%EC%8B%9C-%EB%B6%80%EC%83%81) — 채팅 시작 시점에 공유된 주요 분석글.
- [Boris Cherny의 에이전트 관리 시대 정리 (Threads)](https://www.threads.com/@unclejobs.ai/post/DYEPTstiRii?xmt=AQG0rKwMX0Km7wlF1qCTKppYX55seLHLk_NGQpXJYdB18g) — "코드 작성에서 에이전트 관리로" 핵심 논점, 방 내 큰 반응.
- [Claude Dreaming 기능 (X/claudedevs)](https://x.com/claudedevs/status/2052069321355182447) — 멀티 에이전트 오케스트레이션 + 자기학습 dreaming + webhook 추가 발표. P28의 상세 한국어 요약과 함께 후속 토론.
- [업스테이지 라이브 — 하네스 설계부터 Skill 활용까지](https://youtu.be/eSNawIIHRcQ).
- [Claude Skills PDF/Tweet](https://x.com/heynavtoor/status/2052351837123363273) — Claude Skills 정리.
- [최홍찬 아티클 (브런치)](https://brunch.co.kr/@hongchanchoi/11) — Drone 추천.
- [GPT Image 2 Prompt Gallery + Agentic Skill](https://github.com/wuyoscar/gpt_image_2_skill/blob/main/README.zh.md).
- [Claude for Outlook 사용법](https://support.claude.com/en/articles/14855664-use-claude-for-outlook).
- [Claude for Financial Services (Anthropic 공식)](https://github.com/anthropics/financial-services).
- [UI-TARS-desktop (ByteDance)](https://github.com/bytedance/UI-TARS-desktop) — 오픈소스 멀티모달 AI 에이전트 스택.
- [PersonaLive (실시간 인물 이미지 애니메이션)](https://github.com/GVCLab/PersonaLive).

## 결정·약속·일정

- **2026-05-09 (금) 20:00** — 엉클잡스 라이브 "클로드코드로 Notion MCP 연동해서 블로그 자동화 + 글쓰기 특강" 진행. [YouTube 라이브 링크](https://youtube.com/live/kNE9evX0CTo?feature=share). 다시보기는 아카데미 멤버 한정. 프롬프트 배포 가능성 언급.
- **2026-05-08 (목) 11:00** — 업스테이지 "하네스 설계부터 Skill 활용까지" 라이브.
- **차기 콘텐츠 후보**: 코덱스, 에르메스 에이전트.
- **"시키는 기술" 정식판** — 곧 출시 예정. 추가 감수는 시간 끌릴 것 같아 바로 출시 결정. 후속 책 "위임의 기술", "관리의 기술" 시리즈 집필 계획 확정.

## 반응이 컸던 화제

- **"겸손 = 백수 되는 길"** — 한국식 겸손 문화 vs 서양식 셀프 마케팅. P14는 김연아 영문 표기(Yuna Kim) 사례까지 들며 공감, 여러 명이 길게 동의.
- **엉클잡스의 "아티클 장인" 정체성** — P13이 "스레드에서 정독하는 글이 다 엉클잡스 컨텐츠였다", "댓글 → 소통 → 만나고 → 커피 → 밥"으로 이어진 실제 경험 공유. 셀프 마케팅 실효성을 보여주는 사례로 다수 호응.
- **AI 에이전트 관리로의 패러다임 전환** — Boris Cherny 발언을 기반으로 "코딩 능력 < AI에게 잘 시키는 능력" 논의 활발. P26 "잘 시켜야 한다가 맞다" 등 강한 동의.
- **"시키는 기술" 시리즈물 아이디어** — 위임의 기술/관리의 기술/컨텍스트 관리까지 즉석에서 시리즈 확장 결정, "사마천에 사기가 있다면 잡스님에겐 시기"라는 농담까지 나옴.
- **Claude Dreaming 발표** — P28이 상세 분석 공유, P14 등 즉시 시도 의향 표명.

## 자동화·시스템 적용 후보

- **Notion MCP + Claude Code 블로그 자동화** — 5/9 라이브에서 공개될 워크플로우. 아티클 품질을 자동화로 뽑아내는 패턴.
- **트리거 기반 워크플로우 설계** — "범용 도구 사용자 → 모델 디자이너"로 전환. 특정 이벤트 발생 시 자동 분석을 돌리는 시스템 (예: 고객 저널 키워드 + 과제 진행도 + 감성 트렌드 → 개인화 피드백 프롬프트 자동 생성).
- **Sangse.page 식 상세페이지 자동 생성 패턴** — 가벼운 입력 → AI가 특징/혜택/문제점/마케팅 에셋까지 작성하는 입력 인터페이스 설계.
- **백과사전 스타일 인포그래픽 프롬프트 (P3 공유)** — "encyclopedia-style educational infographic image" 프롬프트 템플릿. 메인 이미지 + 디테일 확대 + 모듈형 정보 패널 + 시각적 점수 시스템 등으로 재사용 가능. 카테고리는 주제에 맞춰 자동 적응(분류/물리적 특성/행동/관리법/위험 등).
- **LiteLLM 도커 이미지 cosign 검증** — CI에 `cosign verify` 단계 추가해 LLM 인프라 무결성 보장.
- **클로드 장애 대비 폴백** — 재시도 + 캐시 기반 폴백 로직을 LLM 호출부에 표준 패턴으로 도입.

## 질문·미해결

- **간장게장 테마로 Sangse.page 시도 시 HTTP 400 발생** — 업데이트 중 버그로 보임. 엉클잡스가 "보수공사 또 해랴겟네"로 인지, 후속 수정 결과는 대화에서 미확인.
- **다음 라이브 콘텐츠 주제**: 코덱스 / 에르메스 에이전트 후보 거론, 확정 미정.
- **"시키는 기술" 정식판 출시 정확한 일자** — "곧 출시"로만 언급, 구체적 날짜 미확정.
- **엉클잡스 YouTube 본격 재개 시점** — "이제 본격 활성화 갑닷" 선언만 있고 구체 일정 없음.

---

## 요약 통계

- 메시지 수: 195
- 기간: 2026-05-08 09:21 ~ 2026-05-08 22:53

### URL (98개)

- https://lattice-log.vercel.app/blog/20260508-ai-%EC%9D%B8%ED%94%84%EB%9D%BC%EC%99%80-%EC%97%90%EC%9D%B4%EC%A0%84%ED%8A%B8-%ED%91%9C%EC%A4%80%ED%99%94-%EB%8F%99%EC%8B%9C-%EB%B6%80%EC%83%81
- https://github.com/BerriAI/litellm/releases/tag/v1.83.14-stable.patch.3
- https://www.reddit.com/r/ClaudeAI/comments/1t6ndxa/eu_subscribers_claude_pros_usage_limits_may_not
- https://github.com/supabase/supabase/releases/tag/v1.26.05
- https://phoboslab.org/log/2026/05/n64-additive-blending
- https://tylerhillery.com/blog/why-dont-lowercase-chars-come-after-upper
- https://lattice-log.vercel.app/news/2026-05-08-morning
- https://mojolang.org/releases/v1.0.0b1
- https://longblack.co/note/1973?ticket=NT2619a7270dec3a9e9d9151ff99d47662c45a
- https://namu.wiki/w/Tech%20N9ne
- ... +88 more

### 멘션

- @unclejobs: 3
- @all: 2
- @hongchanchoi: 1

---

# Fronmpt Academy — 2026-05-08

- 메시지 수: 195

**09:21** [P14]
굿모닝입니다
오늘도 차분하게 하나씩 나아가겠습니다

**09:34** [P2]
<https://lattice-log.vercel.app/blog/20260508-ai-%EC%9D%B8%ED%94%84%EB%9D%BC%EC%99%80-%EC%97%90%EC%9D%B4%EC%A0%84%ED%8A%B8-%ED%91%9C%EC%A4%80%ED%99%94-%EB%8F%99%EC%8B%9C-%EB%B6%80%EC%83%81>

**09:56** [P13]
진짜 이런거 볼때마다 응제님 실행력에 감탄합니다.
진짜 제가 배워야할 자세인것같아요

**10:02** [P27]
응제님은 원래가 개발자이셨나용?

**10:06** [P2]
📡 Lattice Live

▸ AI / Models / Papers

✱ LiteLLM 도커 이미지 서명 확인
LiteLLM의 모든 도커 이미지는 cosign을 사용하여 서명됩니다. 각 릴리즈는 동일한 키로 서명되며, 사용자는 cosign verify 명령어를 통해 이미지의 서명을 확인할 수 있습니다. 이렇게 하면 사용자가 원본 서명 키를 사용하여 이미지를 검증할 수 있습니다. 이는 보안을 강화하고 도커 이미지가 변경되지 않았음을 보장하는 중요한 단계입니다.
왜 지금: 도커 이미지를 사용할 때 보안을 강화하고 신뢰성을 높이기 위해 이미지 서명을 확인하는 것이 중요합니다.
써먹기: vibe-coder는 사이드 프로젝트에서 도커 이미지를 사용할 때 cosign을 사용하여 이미지 서명을 확인하고, 이를 통해 보안과 신뢰성을 높일 수 있습니다.
출처: litellm_rel · <https://github.com/BerriAI/litellm/releases/tag/v1.83.14-stable.patch.3>

✱ EU 클로드 프로, 불명확한 사용량 고지 논란
EU 거주 클로드 프로 구독자가 명확한 사용량 고지 없이 추가 요금을 부과받았다고 주장합니다. EU 소비자 보호법에 따르면 계약 전 서비스의 구체적인 특성 고지가 의무인데, 클로드 측이 이를 충족하지 못했다는 지적입니다. 향후 유사한 분쟁 발생 시 중요한 판례가 될 수 있습니다.
왜 지금: LLM 서비스의 투명한 고지와 소비자 권리 보호에 대한 논의가 필요한 시점입니다. AI 서비스의 과금 정책을 면밀히 살펴봐야 할 이유를 보여줍니다. ', 'related': [], 
출처: r_claudeai · <https://www.reddit.com/r/ClaudeAI/comments/1t6ndxa/eu_subscribers_claude_pros_usage_limits_may_not>

▸ Dev / Tools / Community

✱ Supabase 개발자 업데이트
Supabase에서 지난 달에 일어난 모든 일들을 요약한 개발자 업데이트입니다. 커스텀 OAuth/OIDC 제공자, 새로운 테이블의 자동 노출 제거, ISO 27001 인증 등을 포함합니다. 이러한 업데이트들은 Supabase의 보안과 사용자 편의성을 향상시킵니다.
왜 지금: 지금 알아야 하는 이유는 Supabase의 최신 기능과 보안 강화로 인해 개발자들이 자신의 프로젝트에 적용할 수 있는 새로운 기회가 생겼기 때문입니다.
써먹기: vibe-coder는 사이드 프로젝트에서 Supabase의 새로운 기능을 활용하여 보안과 사용자 편의성을 높일 수 있습니다.
출처: supabase_rel · <https://github.com/supabase/supabase/releases/tag/v1.26.05>

✱ N64 애드디티브 블렌딩
오리지널 플레이스테이션과 닌텐도 64의 그래픽 차이를 설명하는 블로그 포스트입니다. 애드디티브 블렌딩은 그래픽 효과를 더 현실적으로 표현하는 기술입니다. 포스트에서는 N64의 Reality Display Processor가 애드디티브 블렌딩을 지원하지만, 결과를 클램핑하지 않아 원하는 결과를 얻지 못했다는 점을 설명합니다.
왜 지금: 현재 그래픽 기술이 발전함에 따라, 과거의 기술을 이해하는 것이 중요합니다.
써먹기: 애드디티브 블렌딩을 사용하여 게임이나 그래픽 프로젝트에서 더 현실적인 효과를 줄 수 있습니다.
출처: lobsters · <https://phoboslab.org/log/2026/05/n64-additive-blending>

✱ ASCII에서 대문자와 소문자 순서
ASCII 테이블에서 대문자 Z 다음에 바로 소문자 a가 나오지 않고 몇 가지 특수 문자가 있는 이유를 설명하는 글입니다. 이는 컴퓨터가 숫자만을 이해할 수 있기 때문에 문자를 숫자로 매핑하는 인코딩이 필요했으며, ASCII는 초기의 인코딩 방식 중 하나입니다. ASCII는 7비트를 사용하여 128개의 코드 포인트만을 표현할 수 있었기 때문에, 이후에 Unicode와 같은 더 큰 인코딩 방식이 필요하게 되었습니다. ASCII의 설계에서 대문자와 소문자 사이에 특수 문자를 삽입한 이유는 2의 거듭제곱을 이용한 비트 연산을 효율적으로 사용하기 위함입니다.
왜 지금: 컴퓨터 과학의 기본적인 원리와 문자 인코딩의 역사적인 배경을 이해하는 데 도움이 됩니다.
써먹기: 문자열 처리와 인코딩에 관한 사이드 프로젝트에서 ASCII와 Unicode의 차이점을 고려하여 효율적인 문자열 처리를 구현할 수 있습니다.
출처: lobsters · <https://tylerhillery.com/blog/why-dont-lowercase-chars-come-after-upper>

전체 보기: <https://lattice-log.vercel.app/news/2026-05-08-morning>

**10:08** [P9]
ㅋㅋㅋㅋㅋㅋ 지르고 보는 스타일이라 ㅎㅎ
아닙니다 
2년전까지 도메인 설정도 몰랏어요 
판매하려고 만들다보니 여기까지 왔네여 ㅋㅋ 아직 갈길이 멀지만 .. 

**10:09** [P2]
라이브 기대하고 있습니다.

**10:09** [P9]
만들어나가면서 하나씩 하시다 보면 
누구나 가능하심니다 

**10:10** [P2]
저건 뭔가요?

**10:10** [P9]
상세페이지 만들기 기능 붙이는 중임니다 
Https://Sangse.page 
요기에 붙이고 있습죠 ㅋㅋ

**10:11** [P2]
시도해보겠슴다

**10:11** [P14]
아 게장을 파시는것은 아니죠?

**10:12** [P9]
전에 팔앗습니다 ㅋㅋㅋㅋㅋ

**10:12** [P14]
엥
헐
!!!

**10:12** [P13]
전 조만간 홍게 팔겁니다 ㅋ

**10:12** [P9]
이커머스가 제 주전공이엇숩죠 ㅋㅋ

**10:12** [P14]
와 그러셨군요
게장 좋아하는데요 
하하

**10:13** [P9]
홍게는 금어기 거의 없으니 짱
ㅋㅋ 다시 브랜드 살릴때까지 흐흐

**10:13** [P13]
ㅋㅋㅋㅋ

**10:14** [P14]
Https://Sangse.page  이거는 자유롭게 이용하면 되는거죠?

**10:15** [P9]
넵 무료로 2회 가능하심니다 
ㅋㅋ

**10:15** [P2]
엇..
간장게장 테마로했는데 ㅠ

**10:15** [P9]
어허이 에러낫구만요 ㅋㅋㅋ
보수공사 또 해랴겟네

**10:15** [P2]
HTTP400
ㅋㅋㅋ

**10:15** [P7]
잘못된 요청

**10:15** [P9]
ㅋㅋㅋ 업뎃하면서 뻑나갓나보네요

**10:16** [P14]
이커머스 경험이 많이 도움되실거같습니다

**10:16** [P13]
공감합니다.
진짜 경험이 제일 큰 무기라 생각합니다.

**10:16** [P2]
[General] Mojo v1.0.0b1 — AI·시스템 프로그래밍 언어 첫 1.0 베타 공개

Modular가 개발한 프로그래밍 언어 Mojo의 첫 1.0 베타 버전(v1.0.0b1)이 공개됐다. Mojo는 Python 문법 친화성을 유지하면서 C·Rust 수준의 성능을 AI·시스템 프로그래밍에서 구현하는 것을 목표로 설계된 언어로, 특히 GPU 커널 작성과 ML 연산 최적화에 적합하도록 MLIR(Multi-Level Intermediate Representation — 컴파일러 중간 표현 표준) 인프라 위에 올라타는 구조다. v1.0.0b1은 안정 API를 향한 첫 번째 공식 베타 마일스톤이며, 언어 설계가 실험 단계를 넘어 프로덕션 채택 가능성을 본격 타진하는 시점임을 의미한다. 새로운 AI 추론 커널이나 커스텀 연산자를 파이썬 생태계와 연계해 작성하려는 팀이라면 1.0 안정화 로드맵을 지금 확인할 만하다.

(출처: lobsters_ai — <https://mojolang.org/releases/v1.0.0b1> — 2026-05-08 04:23 KST / 약 5시간 전)

**10:16** [P14]
맞습니다

**10:16** [P2]
AI 프로그래밍 언어 등장

**10:17** [P9]
이 세상에 필요없는 경험은 없다고 생각함니다 ㅋㅋ

**10:17** [P7]
동의합니다 ㅋㅋ

**10:18** [P2]
극공

**10:19** [P9]
@all 
안녕하심니까 여러분

일시 : 5월 9일 20시 
주제 : 클로드코드로 notion mcp 연동해서 블로그 자동화

아티클 장인으로서 개쩌는 품질의 콘텐츠 만드는 법 알려드릴 예정입니다!!!

**10:20** [P14]
다시보기 될까요? 

**10:20** [P2]
무조건 본다

**10:20** [P9]
이번 다시보기는 아카데미 분들께만 .. ㅋㅋ 

**10:20** [P2]
그떄 로드바이크 타는데 
타면서 들을게요
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

**10:20** [P17]
저기 위의 링크로 구매해서 보면 되나요?

**10:21** [P9]
아 이건 라이브로 할 예정입니다 ㅋㅋ

**10:21** [P14]
앗
넵

**10:23** [P2]
개쩌는 품질 아티클 만드는거 배워야함

**10:24** [P9]
ㅋㅋ 글쓰기 장인으로서 보여두리겠습니다 어떻게 하는지
이렇게 피알하면 된다죠 미쿡에서는? 
겸손은 백수되눈 길이라던데 ㅋㅋ

**10:24** [P2]
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ
더 적극적으로 해야함
뻔뻔히
뻔뻔하게
어제 그 댓글 좋았음
그리고 항상 비평하는 시각 ㄱㄱ

**10:24** [P9]
스레드 1타 
아티클 황제
엉클잡스 미만잡

이렇게요? ㅋㅋㅋ

**10:24** [P2]
그렇죠 ㅋㅋ
우리나라사람들이 contrarian되는거 어려워하잖아요

**10:25** [P14]
서양에서는 겸손을 미덕이라고 생각하지 않더라구요

**10:25** [P2]
겸손 = 너드, geek임
능력있으면 어필해야함 무조건

**10:25** [P13]
저는 응제님이 "아티클 장인"이라는 부분에 사실 진짜 공감하는 입장입니다. 왜냐하면.. 스레드에 AI 소식공유하시는 분들 많은데 항상 제가 정독하고 있는 글 보면 다 엉클잡스 컨텐츠였거든요 ㅋ

그러다 보니 거기에 댓글도 그냥 습관적으로 달게되고.. 소통하게되고… 만나고.. 커피마시고.. 밥먹고...ㅋㅋ

**10:25** [P14]
물론 메시같은 특이케이스가 있긴하지만 어쨌든

**10:25** [P2]
우리나라도 그렇게 되지 않을까 하는 생각입니다. 
마케팅의 중요성이 더 부각 되고 있고 사실

**10:26** [P17]
1인 브랜드면 진짜 겸손은 사치죠 ㅋㅋ

**10:26** [P2]
겸손할거면 그냥 시작을 말라.
오차피 똑똑한 애들 천지니

**10:26** [P17]
아무도 안알아줘요 ㅋㅋ

**10:26** [P9]
돈 자랑만 아니면 뭐든 자랑해야하는듯 하네요 

**10:27** [P27]
와 그러면 어떻게 이정도 수준까지 올라오셨나요,,,대단하시네요

**10:27** [P7]
어제 그 영상은 충격적이긴 했는데요…
현실 토큰 충전 자랑은 해보고 싶네요
ㅋㅋㅋ

**10:27** [P13]
개발자인 저도 진짜 놀란 부분이죠 ㅋ

**10:27** [P9]
넵 맞슺니다
저는 뭐든 잘합니다 
왜냐하면 잘될때까지 하거둔요

**10:27** [P14]
잘될때까지 하자

**10:27** [P9]
그래서 잘되면 놔버리는… ㅋㅋㅋㅋㅋ
이쪽은 그게 안되서 계속 하는중 ㅋㅋ

**10:27** [P14]
명언이네요

**10:27** [P13]
ㅋㅋㅋㅋㅋㅋ

**10:28** [P17]
현실 토큰  좀 많이 충전되면 좋겠네요

**10:28** [P14]
잠언

**10:28** [P2]
응제님이랑 협업하길 기대하면서.. 전 오늘도 달려봅니다

**10:28** [P3]
<https://longblack.co/note/1973?ticket=NT2619a7270dec3a9e9d9151ff99d47662c45a>

**10:28** [P9]
국내 1황이 되어보겟습니다

**10:28** [P2]
렛츠고

**10:28** [P9]
글로벌 1황까지 가즈아

**10:28** [P14]
오 머죠 이거
롱블랙
괜찮은 컨텐츠가 많네요 

**10:28** [P9]
롱블랙 좋아요 
테크나인도 개안앗능데

**10:29** [P22]
방장님 유튜브채널도있으세요?

**10:29** [P9]
폐간 ㅠ

**10:29** [P14]
왜 폐간됐죠 ㅠ

**10:29** [P9]
넵 쇼추만 몇개 올렷는데 이제 본격 활성화 갑닷 ㅋㅋ

**10:29** [P22]
채널명이 어떤거에요?

**10:29** [P9]
수익성이 낮아서 그럴거에요 

**10:29** [P14]
테크나인 검색하니 뭔 래퍼가
<https://namu.wiki/w/Tech%20N9ne>
하하

**10:29** [P9]
사람들은 콘텐츠 소비를 스토리에 치중되서 하기 때매 ㅋㅋ
엉클잡스입니덧

**10:30** [P22]
감사합니닷

**10:30** [P3]
하네스 설계부터 Skill 활용까지 | 업스테이지

오늘 오전 11시 시작

<https://youtu.be/eSNawIIHRcQ>

**10:30** [P2]
좋네요

**10:30** [P14]
그런데 Fronmpt 이거는 오타죠...?!

**10:31** [P9]
아녀 정확한 브랜드명이에요 ㅋㅋ
프롬프트 구독제 서비수할때 지은거

**10:31** [P14]
오!! 그렇군요

**10:31** [P9]
참고로 수익성 있는 제품은 화려한 기능이 중요한게 아닙니다
노션으로 구독제 서비스 만들어서
수익화 했습니다 

**10:31** [P14]
김연아 전 피겨선수 영문네이밍 
생각났습니다
Yeon-A가 되어야 하는데 Yuna Kim이잖아요 

**10:32** [P13]
구글도 ㅋㅋㅋ

**10:32** [P9]
<https://youtu.be/D4fkiQfzw_I?si=UA0gLeMP8v8rr19l>
추천드립니다 다들 
Ai fomo에 휩쓸리지 않고 나아가는 길

**10:32** [P14]
제가 생각한 그 채널이 맞았군요
Starter Story 
잘보겠습니다

**10:33** [P9]
업스테이지 다음 인수 햇던데
Ipo곧 추진하겟군여
조단위 밸류라고 봣는데

**10:34** [P2]
ㅇㅇ
맞아요 ㅋ

**10:34** [P14]
다음카카오 말씀하시는건가요

**10:34** [P2]
유니콘임 
한국에 ipo 하는게 아쉬울 뿐

**10:34** [P9]
설왕설래오가는중 

**10:35** [P2]
저거 starterstory
저도 보는데
저렇게 살아야함 ㅋ

**10:35** [P3]
Claude for Outlook

<https://support.claude.com/en/articles/14855664-use-claude-for-outlook>

**10:35** [P14]
그러네 
<https://v.daum.net/v/20260507081435710>

**10:35** [P2]
구독자 1천명일때부터 봤음

**10:35** [P14]
헉 엄청 오래전부터 보셨군요

**10:35** [P2]
회사에있엇죠
저렇게 살아야지 하면서
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

**10:37** [P3]
저는 극초기부터 보고 있기는 했네요~~ ㅎㅎ (지금은 아주 가끔 봅니다.)

시장이 다르다는 것을 알아야 합니다.
비이브 패밀리 ㅎㅎ

<https://www.youtube.com/watch?v=G4VliKcuxHo>

**10:42** [P14]
오 한번 보겠습니다
할일 관리 앱

**10:43** [P2]
so cute

**10:46** [P3]
AI Agent Workflow Engine | BlueKiwi

<https://github.com/dandacompany/bluekiwi>
Bi-directional memory sync for AI coding agents | memento

<https://github.com/dandacompany/memento>

**10:48** [P9]
이런 정보 폭탄을 주시눈 Drone님은 봇 아니시고, 이쪽에 조예가 깊으신 분입니다 

가장 중요한 건
저희 아카데미 멤버십니다 😎

**10:49** [P2]
아카데미 링크좀 주십쇼!

**10:49** [P14]
감사한 마음 뿐입니다

**10:50** [P22]
방장님 스레드는 자동인지 직접 다 작성하시는건지 궁금합니다  그리고 본업은 다른거세요?

**10:50** [P9]
ㅋㅋㅋㅋㅋㅋ 공지에 있습니다만 리뉴얼 하면 다시 드릴게요

**10:50** [P2]
꿋!

**10:51** [P3]
Google Agents CLI

<https://github.com/google/agents-cli>

**10:51** [P9]
반자동입니다 
큐레이션 방향성은 터치합니다
본업은 사업도 하고 컨설팅도 하고 그렇숩니다 ㅋㅋ

**10:51** [P3]
아티클 추천

<https://brunch.co.kr/@hongchanchoi/11>

**10:52** [P9]
본명으로 하니 헷갈려 하셔서 엉클잡스로 닉냄 변경햇슴니다요 ㅋㅋ

**10:52** [P2]
신청하면 진행이 어떻게 되는건가요?
같이 4 주동안 허슬하는건가요?

**10:53** [P3]
Open Generative AI

<https://github.com/Anil-matcha/Open-Generative-AI>

**10:53** [P9]
영끌임니다 
영혼까지 끌어모아서 ㅋㅋㅋㅋ 

**10:53** [P2]
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

**11:13** [P14]
Boot Camp네요

**11:16** [P9]
커뮤니티 멤버십입니다 ㅋㅋ
Vod 무한 재수강 가능한

**11:18** [P3]
GPT 5.5

<https://youtu.be/wuDzFRYI1S8?si=9Y8gIqfmZO-axa26>

**11:34** [P3]
Codex in Chrome
Portrait Image Animation for Live Streaming | Personal Live

<https://github.com/GVCLab/PersonaLive>

**11:41** [P9]
잘되네요 ㅋㅋ 모바일로 들어가셨나이까

**11:41** [P3]
Open-Source Multimodal AI Agent Stack

<https://github.com/bytedance/UI-TARS-desktop>

**12:07** [P9]
@all  

안녕하십니까, 여러분 엉클잡스 입니다.

연휴 후유증으로 다들 고생이 많으십니다.

요청하셔서 라이브 진행합니다.

다음엔 초대석으로 또 해보겠습니다. ㅋㅋ

일시 : 5월 9일 20시 
주제 : 클로드코드로 notion mcp 연동해서 블로그 자동화

아티클 장인으로서 개쩌는 품질의 콘텐츠 만드는 법 알려드림

p.s 프롬프트를 나눠드릴수도..?

<https://youtube.com/live/kNE9evX0CTo?feature=share>
글쓰기 특강이 반 이상 진행될 것 같네요 ㅋㅋㅋ

**12:13** [P9]
다음 콘텐츠 주제도 미리 받습니다 ㅋㅋㅋㅋㅋㅋ
대기 중인게 코덱스 , 에르메스 에이전트 네여 ㅋㅋ

**12:16** [P26]
클코+omo(딥식이+코덱스) 조합쓰는데..괜찮은 조합이 더있을까요

**12:18** [P3]
Claude Skills

<https://x.com/heynavtoor/status/2052351837123363273>

**12:18** [P9]
omo만으로도 이미 강려크하실걸요 ㅋㅋ

**12:18** [P3]
파일: Claude Skils.pdf

**12:29** [P3]
중국 추천 사이트

Buzzing : <https://buzzing.cc>

SoPilot : <https://sopilot.net/zh/hot-tweets>

NewsNow : <https://newsnow.busiyi.world>

今日热榜 : <https://tophub.today>

V2EX : <https://v2ex.com>

少数派 : <https://sspai.com>

小众软件 : <https://appinn.com>


**12:34** [P7]
중국 사이트는 생각을 못해봤네요

**12:34** [P26]
은혜롭긴합니다ㅋㅋ

**12:35** [P3]
StemDeck

Demucs 모델을 사용해 YouTube 오디오를 여섯 트랙으로 분리하며, DAW와 유사한 멀티트랙 인터페이스를 제공

<https://github.com/thcp/stemdeck>

**12:35** [P14]
와 중국

**12:36** [P2]
바로 공유드감
아뇨 웹으로했었는데 간장게장은 안되나요 ㅋㅋㅋ

**12:45** [P3]
Prompt

Based on { TOPIC }, create a high-quality vertical “encyclopedia-style educational infographic image.”

This image should NOT look like a regular poster or a simple illustration. Instead, it should feel like a structured knowledge guide that combines:

the feeling of a collectible reference handbook

a modern encyclopedia page

a lifestyle knowledge card

and a highly shareable social-media infographic

The overall style should resemble a premium natural-history guidebook mixed with modern editorial infographic design.

The image should include:

One beautiful and highly detailed main subject image

Several zoomed-in detail sections highlighting important features

Multiple modular information panels with rounded corners

Clear title hierarchy and highlighted key labels

Concise yet information-rich encyclopedia content

Visual scoring systems, quick summaries, or “Top 5” modules

The information sections should automatically adapt to the topic. Select and combine relevant categories such as:

Basic profile

Classification / taxonomy

Physical characteristics

Behavior / ecology / habits

Structure or formation mechanisms

Growth conditions or usage methods

Care, maintenance, or optimization tips

Risks, warnings, and important notes

Suitable users or application scenarios

Pros and cons comparison

Quick rating tags or summary cards

Visual requirements:

Clean light-colored background

Soft and elegant color palette

Gentle shadows

Small refined icons

Rounded information boxes

Organized editorial layout

High information density without feeling crowded

Comfortable reading experience

AI Researchers Tools

📌 <http://Perplexity.ai> – research faster
📌 <http://Scite.ai> – verify sources
📌 <http://Elicit.org> – summarize papers
📌 <http://ChatGPT.com> – explain theories
📌 <http://Scholarcy.com> – auto summaries
📌 <http://Consensus.app> – find evidence
📌 <http://ResearchRabbit.ai> – discover related work
📌 <http://Litmaps.com> – visualize citations
📌 <http://SemanticScholar.org> – deep paper search

**12:50** [P17]
어휴 알려주시는 정보들 담아먹기만 해도  바쁘네요 ㅋㅋ

**13:06** [P13]
코드로 영상 만드는 라이브러리인 remotion 보다 좀더 인터랙티브한 효과를 제공해주는 <https://remotion-bits.dev/docs/bits/remotion-bits/> 를 어제 발견했습니다 ㅋ

**13:25** [P3]
Codex / Claude Code | 스킬 > Plan Mode

<https://youtu.be/vet6pZmm2_w?si=aUlCotSs-StZ-t1g>

**13:39** [P9]
깨알 홍보
이렇게 내용 가볍게 입력해도 ai가 입력해줍니다 ㅋㅋ
이렇게 주요 특징과 혜택, 문제점도 말해주고 마케팅 에셋도 해준다구요우 ㅋㅋㅋㅋ
이제 유튜브 론칭하면 대대적으로 홍보갑니다욧

**13:56** [P3]
GTM Tool with Gemini

<https://luma.com/xh87ti8o?tk=Kwm75P>
추천 Repo

1. AutoHedge
<https://github.com/The-Swarm-Corporation/AutoHedge>

2. Vibe-Trading
<https://github.com/HKUDS/Vibe-Trading>

3. Fincept Terminal
<https://github.com/Fincept-Corporation/FinceptTerminal>

4. Claude Ads
<https://github.com/AgriciDaniel/claude-ads>

5. Toprank
<https://github.com/nowork-studio/toprank>

6. Open Higgsfield AI
<https://github.com/Anil-matcha/Open-Higgsfield>

7. Hyperframes
<https://github.com/heygen-com/hyperframes>

8. Camofox Browser
<https://github.com/jo-inc/camofox>

9. Agentic Inbox
<https://github.com/cloudflare/agentic-inbox>

10. Context Mode
<https://github.com/mksglu/context-mode>


**14:12** [P9]
1년만입니다
OpenAI가 Realtime API를 업데이트 했습니다
최근까지 대세는 일레븐랩스인데 과연 어떠한 시장 점유율을 보여줄지 기대 됩니다
<https://www.threads.com/@unclejobs.ai/post/DYEJt8gCYG7?xmt=AQG0-9CkBtd61qhGIOUnzEea5shEK_BuB8ecKdY4PGEU9g>

**14:27** [P14]
알려주시는 것들 잘 소화하면

**14:27** [P2]
📡 Lattice Live · 오후 2:00

▸ AI / Models / Papers

✱ LLM 에이전트 스킬 검색 벤치마크
최근에 발표된 SkillRet은 LLM 에이전트의 스킬 검색을 위한 대규모 벤치마크입니다. SkillRet에는 17,810개의 공개 에이전트 스킬이 포함되어 있으며, 구조화된 의미 태그와 2단계 분류 체계를 통해 6개의 주요 카테고리와 18개의 하위 카테고리로 구성되어 있습니다. 또한 63,259개의 훈련 샘플과 4,997개의 평가 쿼리가 제공되어 벤치마킹과 검색을 위한 훈련을 모두 지원합니다. 이 벤치마크는 LLM 에이전트의 스킬 검색 성능을 평가하고 개선하는 데 유용할 것입니다.
왜 지금: LLM 에이전트의 스킬 검색 성능을 평가하고 개선하려는 개발자들에게 필수적인 벤치마크입니다.
써먹기: vibe-coder는 SkillRet을 사용하여 자신의 LLM 에이전트의 스킬 검색 성능을 평가하고 개선할 수 있습니다.
출처: arxiv_csai · <https://arxiv.org/abs/2605.05726>

✱ 에이전트 차이
Gemma 4와 Opus 4.6을 비교한 벤치마크 결과, Gemma 4가 더 빠르고 비용이 낮은 것으로 나타났지만, 실제 프로젝트에서 Gemma 4는 실패하고 Opus 4.6이 성공했다. 이는 에이전트의 능력이 실제 프로젝트에서 어떻게 활용되는지에 따라 달라질 수 있다. Gemma 4는 빠른 속도와 낮은 비용을 제공하지만, 실제 프로젝트에서 필요한 능력이 부족할 수 있다. Opus 4.6은 더 높은 비용을 요구하지만, 실제 프로젝트에서 필요한 능력을 제공할 수 있다.
왜 지금: 현재 에이전트의 능력과 실제 프로젝트에서의 활용이 중요해지고 있다.
써먹기: vibe-coder는 사이드 프로젝트에서 에이전트의 능력과 실제 프로젝트에서의 활용을 고려하여 적절한 에이전트를 선택할 수 있다.
출처: devto_ai · <https://dev.to/carryologist/the-agentic-gap-claude-oneshots-gemma-fails-31b6>

✱ 스팀 추천기: 고도화된 취향 분석과 '왜' 추천하는지 설명
기존 스팀 게임 추천 시스템을 고도화하여, 단순 장르 매칭을 넘어 사용자 취향의 미묘한 특징까지 분석해줍니다. '액션' 같은 광범위한 태그 대신, '도시 분위기, 재즈 퓨전'처럼 게임별 고유한 요소를 파악합니다. 이는 사용자가 어떤 기준으로 추천받았는지 명확히 이해하게 하여, 더욱 만족도 높은 게임 탐색을 돕습니다. 추천 시스템의 '설명 가능성'을 높여 사용자 신뢰와 활용성을 극대화한 좋은 사례입니다.
왜 지금: 설명 가능한 AI(XAI) 트렌드 속에서, 사용자가 추천을 납득하게 만드는 '왜'가 중요해지는 시점입니다.
써먹기: LLM을 활용해 사용자 피드백이나 콘텐츠 설명을 세분화하여 '왜 추천하는지'를 명시하는 추천 시스템을 구축할 수 있습니다.
출처: r_machinelearning · <https://www.reddit.com/r/MachineLearning/comments/1t6x2zw/steam_similarity_recommender_p>

✱ 권한 제한 에이전트: 불완전 답변 위험 측정
기업 AI 에이전트는 점점 더 제한된 접근 권한 환경에서 작동합니다. 문제는 에이전트가 호출자의 권한 경계 밖에 중요한 증거가 있음에도 불구하고 완전해 보이는 답변을 생성할 수 있다는 점입니다. Partial Evidence Bench는 이러한 위험한 불완전 답변(unsafe completeness) 실패 모드를 측정하기 위한 결정론적 벤치마크입니다. 총 72개 태스크로 구성된 세 가지 시나리오(실사, 규정 준수 감사, 보안 사고 대응)를 통해 에이전트의 답변 정확성, 완전성 인지, 누락 보고 품질 등을 평가합니다. 이 벤치마크는 에이전트 시스템의 거버넌스 관련 핵심 실패를 인간 평가 없이 측정 가능하게 만듭니다.
왜 지금: 엔터프라이즈 환경에서 AI 에이전트 도입이 가속화되면서, 보안 및 규정 준수 관점에서 에이전트의 정보 처리 신뢰성은 필수적이기 때문입니다.
써먹기: 사이드 프로젝트에서 에이전트를 개발할 때, 의도적으로 특정 정보를 주지 않고 에이전트가 '나는 이 정보에 접근할 권한이 없다'는 식의 '누락 보고' 기능을 구현하여 불완전성을 명시하도록 훈련해보세요.
출처: arxiv_csai · <https://arxiv.org/abs/2605.05379>

✱ 클로드 vs 챗GPT 실전 비교
2026년 기준 클로드와 챗GPT는 각각의 강점이 뚜렷하다. 코딩과 장문 분석에선 클로드가 우위지만, 데이터 처리·이미지 생성·웹 검색은 챗GPT가 앞선다. 둘은 보완 관계이며, 실제 사용에선 목적에 따라 선택해야 한다. 사이드 프로젝트에서는 클로드로 설계를 짜고, 챗GPT로 시각화와 검증을 하는 병행 전략이 효과적이다.
왜 지금: AI 어시스턴트 선택이 개발 생산성의 핵심 변수로 자리잡았다.
써먹기: 클로드로 기획서 작성 후, 챗GPT로 표지 디자인과 데이터 검증까지 한 번에 처리하라.
출처: devto_ai · <https://dev.to/_6638a39c349d7e9c85ee20/claude-vs-chatgpt-2026-which-ai-assistant-actually-gets-the-job-done-p7>

▸ Dev / Tools / Community

✱ GEO 팩트체크: 시간·비용 낭비 전 필수 지식
제로클릭 시대, GEO에 대한 높은 관심 속 검증되지 않은 정보가 넘쳐납니다. 이 세미나는 GEO 적용 시 무엇을 성과로 볼지, 어떤 도구를 어떤 기준으로 선택할지 등 실질적인 의문을 해소합니다. 7년차 SEO 컨설턴트와 콘텐츠 전략가, AI 검색 엔지니어 등 전문가들이 직접 GEO를 적용하며 겪은 경험을 바탕으로 시장의 통념을 팩트체크하고 오해와 진실을 짚어줍니다. 시간과 비용을 낭비하기 전에 GEO의 본질을 파악할 기회입니다.
왜 지금: 제로클릭 시대의 핵심 전략으로 떠오른 GEO를 정확히 이해하고 검증되지 않은 조언에 시간과 비용을 낭비하지 않기 위해 지금 바로 알아야 합니다.
써먹기: 사이드 프로젝트의 서비스나 콘텐츠를 기획할 때 GEO의 핵심 원리를 이해하고 적용하여 불필요한 시행착오 없이 유기적 트래픽을 극대화할 수 있습니다.
출처: yozm_it · <https://yozm.wishket.com/magazine/detail/3743>

✱ PQ 적용한 WireGuard
양자내성(PQ) 키 교환을 WireGuard에 통합한 새로운 혼합 프로토콜이 제안됐다. 기존 WireGuard의 성능과 호환성을 유지하면서, NIST 표준 후보인 Kyber 기반의 양자내성 보안을 추가했다. 실험 결과, 오버헤드는 1% 내외로 거의 무시 가능하며, 실제 배포 가능성을 보여줬다. 이는 네트워크 보안 인프라의 양자 시대 대비에 중요한 한 걸음이다.
왜 지금: 양자 컴퓨팅 공격에 대비한 암호화 전환이 실제 프로토콜 수준에서 구체화되고 있다.
써먹기: 자체 보안 터널링이 필요한 사이드 프로젝트에 PQ 혼합 방식을 참고해 보안 레벨을 사전에 높일 수 있다.
출처: lobsters · <https://eprint.iacr.org/2025/1758.pdf>

전체 보기: <https://lattice-log.vercel.app/news/2026-05-08-afternoon>

**14:27** [P14]
Starter Story에 곧 소개될듯
하하하

**14:40** [P9]
코딩은 이제 “직접 쓰는 일”에서 “AI 에이전트를 관리하는 일”로 바뀌고 있습니다.

코드를 잘 치는 사람보다, AI에게 일을 잘 맡기는 사람이 더 빠른 결과물을 만들어내고 있습니다.

Claude Code를 만든 Boris Cherny는 올해 직접 코드를 쓰지 않고, 휴대폰에서 여러 세션과 에이전트를 관리하며 매일 수십 개의 PR을 합친다고 말했습니다. 과장처럼 들릴 수 있지만, 방향은 분명합니다. 소프트웨어 개발의 중심이 코드 작성에서 에이전트 관리로 옮겨가고 있습니다.

이 변화는 개발직군에 한정되지 않습니다.

기획자, 디자이너, 데이터 분석가, 마케터, 1인 창업자까지 전부 영향을 받습니다. 앞으로 중요한 건 “내가 코드를 얼마나 잘 쓰느냐”가 아니라, 무엇을 AI에게 맡기고, 어떻게 검증하고, 어떤 기준으로 결과를 승인하느냐가 될 가능성이 큽니다.
클로드코드 헤드, 보리스 체르니의 발언을 중점으로 내용을 정리해보았습니다
<https://www.threads.com/@unclejobs.ai/post/DYEPTstiRii?xmt=AQG0rKwMX0Km7wlF1qCTKppYX55seLHLk_NGQpXJYdB18g>
결국 AI한테 잘 시켜야 합니다. 그래서 <<시키는 기술>>을 읽으셔야 합니다 :)

**14:41** [P14]
시기

**14:41** [P9]
<https://www.latpeed.com/products/k9vwB>

**14:41** [P26]
'잘'시켜야된다가 맞는것같아요

**14:41** [P14]
사마천에 사기가 있다면 잡스님에게는 시기

**14:41** [P9]
다음은 위임의 기술로 갑니다 
마지막엔 관리의 기술로 ㅋㅋㅋㅋㅋㅋ

**14:42** [P14]
기술 3부작이군요
기술 시리즈

**14:42** [P26]
크흐..

**14:42** [P14]
집필중이신가요 지금?

**14:43** [P9]
위임은 에이전트에게 위임하는 걸로
관리는 토큰관리나 세션관리하는 걸로 ㅋㅋㅋㅋㅋ
관리의 기술은 방금 생각해냈습니다
아 컨텍스트 관리도 잇군여 

**14:44** [P6]
와 아이디어 좋네요 ㄷㄷ
시리즈로 가면 굿일듯 ㄷㄷ

**14:45** [P9]
ㅋㅋ 시키는 기술 정식판 곧 내보내고
바로 리서치 들어가서 시리즈물로다가 ㅎㅎ

**14:46** [P6]
이제 유료로 하시죠
시키는 기술도 진짜 0원인게
말도 안됨 ㅠㅠㅠㅠ

**14:47** [P9]
앞부분만 80p만 .. ㅋㅋㅋㅋ 300페이집니다 총

**14:48** [P6]
아아 그렇군요 ㅋㅋㅋ

**14:48** [P9]
아 아니구나 293페이지 정도? ㅋㅋ

**14:48** [P3]
클로드 코드로 시작하는 실전 에이전틱 코딩

<https://www.yes24.com/product/goods/189211422>

**14:48** [P6]
아아 아직 정식판 낸 게 아니군요 ㄷㄷ

**14:51** [P3]
GPT Image 2 Prompt Gallery + Agentic Skill + CLI

<https://github.com/wuyoscar/gpt_image_2_skill/blob/main/README.zh.md>

**14:52** [P6]
어우 오늘도 열일하시네 ㄷㄷㄷ
항상 감사드려요

**14:55** [P14]
잡스님 시키는 기술 나머지 페이지 정식판 곧 나오나요?

**15:10** [P9]
ㅋㅋ넵 감수좀 더 하려고 했는데 
시간만 끌거 같아서 바로 출시해야겟습니다
아 goose님 책이 본인이 만드신 cli인 moai에 후반부가 집중되어있군여 
다른거랑 비교해주셨음 더 좋았을텐데 아쉽

**15:13** [P14]
더 하셔도 됩니다

**15:58** [P3]
AI Security Guide

<https://pan.quark.cn/s/c7b6691bdf5d#/list/share>

**17:51** [P14]
이거 중국어도 공부해야겠네요
Xie Xie

**18:07** [P2]
📡 Lattice Live 

▸ AI / Models / Papers

✱ 의료 질문 답변 모델 MedQA
MedQA는 AMD ROCm을 사용하여 훈련된 의료 질문 답변 모델입니다. 이 모델은 LoRA fine-tuning을 통해 Qwen3-1.7B 모델을 기반으로 하며, AMD Instinct MI300X 하드웨어에서 CUDA 없이 훈련되었습니다. MedQA는 다중 선택형 의료 질문에 대한 답변과 함께 임상적 이유를 제공합니다. 이 프로젝트는 AMD 하드웨어에서 의료 AI 모델을 훈련시키는 가능성을 보여줍니다.
왜 지금: 의료 분야에서 정확한 모델이 필요하기 때문에 지금 알아야 합니다.
써먹기: vibe-coder는 MedQA 모델을 기반으로 자신의 의료 관련 프로젝트에서 사용할 수 있습니다.
출처: hf_blog · <https://huggingface.co/blog/lablab-ai-amd-developer-hackathon/medqa>

✱ BioMedArena, 바이오 LLM 에이전트 평가 표준화
바이오메드 아레나(BioMedArena)는 생의학 분야 딥 리서치 에이전트 개발 및 평가를 위한 오픈소스 툴킷입니다. 개별 논문마다 상이하던 에이전트 평가 환경을 표준화하여 '논문별 엔지니어링 비용'을 제거합니다. 벤치마크 로딩, 툴 노출, 선택, 실행, 컨텍스트 관리, 점수 산정 등 6가지 평가 레이어를 분리합니다. 이를 통해 147개 벤치마크와 75개 바이오 툴을 제공하며, 새로운 모델, 벤치마크, 툴 추가가 몇 줄의 어댑터 등록으로 간소화됩니다.
왜 지금: 전문 분야 LLM 에이전트의 개발과 비교 평가가 복잡해지는 현 시점에서, 공정하고 효율적인 에이전트 개발 및 성능 검증을 위한 표준 환경 구축은 필수적입니다.
써먹기: 바이오 외 다른 도메인 특화 에이전트 개발 시에도, 이 툴킷의 평가 레이어 분리 및 표준화 접근 방식을 응용하여 반복적인 엔지니어링 오버헤드를 줄일 수 있습니다.
출처: arxiv_csai · <https://arxiv.org/abs/2605.06177>

▸ Dev / Tools / Community

✱ HPKE-ng: 더 빠르고 작은 HPKE
hpke-ng는 Rust로 구현된 HPKE 라이브러리이며, 기존의 hpke-rs 라이브러리보다 더 빠르고 작은 구현체입니다. hpke-ng는 44개의 벤치마크 테스트에서 hpke-rs를 상회하는 성능을 보여주었습니다. 이는 hpke-ng의 더 효율적인 프레임워크와 메모리 관리 덕분입니다. hpke-ng는 Apache-2.0과 MIT 라이선스를 지원하며, cargo를 통해 쉽게 설치할 수 있습니다.
왜 지금: 현재 hpke-rs에서 발견된 보안 취약점을 해결하기 위해 새로운 라이브러리가 필요합니다.
써먹기: vibe-coder는 사이드 프로젝트에서 hpke-ng를 사용하여 보안을 강화하고 성능을 개선할 수 있습니다.
출처: lobsters · <https://symbolic.software/blog/2026-05-08-hpke-ng>

✱ 프레임워크 쓰는 이유
복잡한 웹 앱에서는 순수 HTML과 DOM 조작만으로는 유지보수와 접근성이 한계에 달한다. React 같은 프레임워크는 컴포넌트 기반 아키텍처로 UI 상태를 체계화하고, 실제 사용자 경험을 개선한다. 단순한 정적 페이지가 아니라 인터랙션과 상태 관리가 필요한 서비스라면 프레임워크 도입은 필수다.
왜 지금: 현대 웹은 앱 수준의 복잡도를 가지며, 개발자 경험과 접근성도 핵심 요구사항이 됐다.
써먹기: 사이드 프로젝트 초기부터 컴포넌트 설계를 고민하면, 나중에 기능 확장이 훨씬 수월해진다.
출처: lobsters 

전체 보기: <https://lattice-log.vercel.app/news/2026-05-08-evening>

**18:30** [P3]
GPT Image로 그린 이미지 ㅎㅎㅎㅎ

**18:31** [P5]
오우 예술이네요.

**18:35** [P14]
예쁘다

**18:35** [P2]
Dayum

**18:38** [P14]
다윰 데이엄 
무슨뜻인가요 ㅋ

**18:40** [P24]
영어 이름

**18:46** [P2]
ㅋㅋㅋㅋㅋㅋ 영어이름이라뇨

**18:47** [P14]
아하
오!
슬랭인데 어감이 오히려
더 좋네요

**18:54** [P3]
Mathematics for Computer Science

<https://courses.csail.mit.edu/6.042/spring18/mcs.pdf>

**19:04** [P17]
페이퍼 아트 느낌 나네요

**20:03** [P3]
Office CLI

<https://github.com/iOfficeAI/OfficeCLI>
Multi-Role AI Agent Runtime } Orca

<https://github.com/junkyard22/Orca>
Voice Input App | OpenLess

<https://github.com/appergb/openless>
✅Remotion
<https://www.remotion.dev/>
<https://github.com/remotion-dev/remotion>
코드 기반의 모션 그래픽, 자막, 템플릿화, 대량 제작에 강함
✅HyperFrames
<https://github.com/heygen-com/hyperframes>
LP나 웹 페이지, HTML/CSS/GSAP를 동영상으로 변환하는 표현에 강함
✅Editframe
<https://editframe.com/>
소재, 음성, 자막, 타임라인 편집에 강함
NotebookLM CLI & MCP Server

<https://github.com/jacob-bd/notebooklm-mcp-cli>

Claude for Financial Services

<https://github.com/anthropics/financial-services>
Music Download

<https://github.com/MrsEWE44/musicDownload>
Python Programming

<https://utokyo-ipp.github.io/index.html>
Warp Agents Skills

<https://github.com/warpdotdev/oz-skills>
Open-Source 비디오 검색 집계 도구 | OuonnkiTV

<https://github.com/Ouonnki/OuonnkiTV>

**21:17** [P28]
<https://x.com/claudedevs/status/2052069321355182447?s=46>
클로드 드리밍 오늘 해봐야겠네요 ㅎㅎ

**21:17** [P14]
드리밍이 머죠 ?!!

**21:18** [P28]
x(<https://short.oursophy.com/RF98L8>) 에 대한 사이트 요약입니다:

👉[In Claude Managed Agents, we’ve added multiagent orchestration, an outcomes loop for rubric-driven self-improvement, dreaming for self-learn]
📈 Claude 관리형 에이전트에 다중 에이전트 오케스트레이션, 루브릭 기반 자기개선 루프, 자기 학습을 위한 꿈꾸기, 그리고 웹훅이 도입되었습니다.
1) Anthropic 공식 — Claude Managed Agents의 “Dreaming”
2026년 5월 6일 Code with Claude 컨퍼런스에서 발표된 정식 기능입니다. 개별 에이전트 세션을 넘어 과거 세션과 메모리 저장소를 검토하고, 패턴을 추출해 메모리를 큐레이션하면서 시간이 지남에 따라 에이전트가 개선되도록 하는 스케줄드 프로세스 입니다. 현재 research preview 단계이며, developer access program을 통해서만 이용 가능 합니다.
→ 적용 방법: Claude Managed Agents 플랫폼 사용자만 해당. 일반 Claude.ai/Claude Code 사용자는 아직 직접 적용 불가. 신청은 Anthropic 측에 developer access 요청해야 합니다.
3) Claude Code 내장 — Auto-dream 토글 (조용히 출시됨)
Claude Code 세션에서 /memory를 실행해서 “Auto-dream: on” 표시가 보이면 이미 백그라운드에서 메모리 파일이 통합되고 있는 상태 입니다. 이게 가장 빠른 확인 방법이에요.
→ 확인 방법: Mac Mini에서 Claude Code 세션 진입 → /memory 입력 → 토글 존재 여부 확인

**21:35** [P9]
<https://www.threads.com/@unclejobs.ai/post/DYCHrARCdzZ?xmt=AQG0hKLyL4fldWsxD4YKRxtTn4qGPKD5Um6fkTXSz48_ow>

**21:36** [P14]
Dreaming
감사합니다

**22:04** [P3]
Agentic Multipane Markdown Editor | NeverWrite

<https://github.com/jsgrrchg/NeverWrite>
Codex 확장앱

<https://chromewebstore.google.com/detail/codex/hehggadaopoacecdllhhajmbjkdcmajg?hl=ko>

**22:14** [P2]
📡 Lattice Live

▸ AI / Models / Papers

✱ 클로드 모델 일시 오류
2026년 5월 8일, 클로드 모델 전반에서 오류율이 상승하는 장애가 발생했습니다. 현재는 점진적으로 정상화되고 있으나, 일부 요청에서 응답 실패 또는 지연이 보고되고 있습니다. 개발자 및 프로덕션 환경에서는 일시적 장애 대응을 위한 폴백 로직이 중요합니다.
왜 지금: 생산 시스템에 클로드를 사용 중이라면 장애 대응 전략이 시급합니다.
써먹기: 사이드 프로젝트에선 재시도 및 캐시 기반 폴백을 추가해 안정성을 높일 수 있습니다.
출처: r_claudeai · <https://www.reddit.com/r/ClaudeAI/comments/1t7403o/claude_status_update_elevated_errors_across>

✱ AI 에이전트용 Commerce MCP 서버
웹 스크래핑은 AI 에이전트가 쇼핑 기능을 구현하는 데 사용할 수 있지만, 여러 가지 문제가 있습니다. 예를 들어, CSS 클래스가 변경되면 스크래퍼가 깨질 수 있고, 반복적인 요청으로 인해 IP 차단이나 CAPTCHA가 발생할 수 있습니다. 또한, HTML을 파싱하여 사용 가능한 제품 데이터를 얻는 것은 신뢰할 수 없습니다. 이러한 문제를 해결하기 위해 MCP(MCP 서버)가 제안되었습니다. MCP 서버는 AI 에이전트가 외부 시스템에 구조화된 데이터를 제공하는 방법입니다. MCP 서버를 사용하면 AI 에이전트가 다양한 마켓플레이스에서 제품 정보를 가져와서 비교하고 구매할 수 있습니다.
왜 지금: 현재 AI 에이전트가 쇼핑 기능을 구현하는 데 있어 웹 스크래핑의 한계를 극복하기 위해 MCP 서버를 사용해야 합니다.
써먹기: vibe-coder는 사이드 프로젝트에서 MCP 서버를 사용하여 AI 에이전트가 다양한 마켓플레이스에서 제품 정보를 가져와서 비교하고 구매할 수 있는 기능을 구현할 수 있습니다.
출처: devto_ai · <https://dev.to/buywhere/why-your-ai-agent-needs-a-commerce-mcp-server-not-a-web-scraper-3igg>

✱ AI 도구를 넘어, 나만의 맞춤형 AI 엔진 설계
AI 활용의 핵심은 범용 도구 구매가 아닌, 서비스 내 고유 문제를 해결할 맞춤형 워크플로우 설계입니다. 일회성 AI 작업 대신, 특정 이벤트 발생 시 자동으로 분석을 실행하는 '트리거 기반 시스템'을 구축하는 것이 핵심입니다. 당신은 이제 도구 사용자가 아닌, 문제 정의 (트리거) → 실행 (액션) → 결과 도출 (출력)을 설계하는 '모델 디자이너'가 됩니다. 이를 통해 고객 저널 키워드, 과제 진행도, 감성 트렌드를 종합해 개인화된 피드백 프롬프트를 자동 생성하는 코칭 엔진처럼, 실제 비즈니스 가치를 창출할 수 있습니다.
왜 지금: 최근 에이전트 프레임워크와 로컬 LLM 환경이 성숙하면서, 고도화된 개인화 AI 시스템 구축이 훨씬 용이해졌기 때문입니다.
써먹기: 사이드 프로젝트에서 반복적인 수동 작업을 AI 에이전트와 트리거 기반 워크플로우로 자동화하여, 나만의 지능형 시스템을 구축하는 데 활용할 수 있습니다.
출처: devto_ai · <https://dev.to/ken_deng_ai/from-generic-tool-to-custom-ai-model-building-your-coaching-engine-258i>

▸ Dev / Tools / Community

✱ 고를 사용해라
블레인 스미스는 Go를 사용하여 개발을 단순화하고 효율성을 높일 수 있다고 주장한다. Go는 컴파일 시간이 짧고, 단일 바이너리 파일로 배포할 수 있으며, 의존성 관리가 용이하다는 장점이 있다. 또한 Go의 표준 라이브러리는 프레임워크의 역할을 할 수 있어 외부 라이브러리의 의존도를 줄일 수 있다.
왜 지금: 현재 개발 환경에서 효율성과 단순성을 추구하는 개발자들에게 Go는 좋은 선택이 될 수 있다.
써먹기: vibe-coder는 사이드 프로젝트에서 Go를 사용하여 백엔드 개발을 단순화하고, 개발 시간을 줄일 수 있다.
출처: lobsters 

✱ Wii IP6 웹서버
Wii IP6 웹서버는 Wii 콘솔에서 작동하는 웹서버입니다. 이 웹서버는 Wii의 IP6 주소를 통해 접근할 수 있으며, 사용자들은 <http://를> 추가하여 사이트에 접근해야 합니다. 이 프로젝트는 Wii의 잠재력을 보여주며, 사용자들이 콘솔을 더 유연하게 사용할 수 있는 방법을 제공합니다.
왜 지금: 현재 Wii의 가능성을 재평가하는 시점에 이러한 프로젝트가 관심을 끌고 있습니다.
써먹기: vibe-coder는 사이드 프로젝트에서 Wii와 같은 임베디드 시스템을 활용하여 독특한 프로젝트를 개발할 수 있습니다.
출처: lobsters · <http://wii.sjmulder.nl/>

전체 보기: <https://lattice-log.vercel.app/news/2026-05-08-night>

**22:35** [P2]
<https://www.youtube.com/watch?v=GJQ0rNvTfPw>
라이브중.

**22:53** [P29]
ㆍ I tear it.

