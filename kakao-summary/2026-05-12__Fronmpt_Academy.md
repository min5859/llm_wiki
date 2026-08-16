<!-- kakao-db: ai-summary v1 -->
# AI 요약

입력 텍스트만 기준으로 정리했습니다. 다른 파일은 보지 않았습니다.

---

## 인사이트·노하우

- **AI·구독 관점 의견(한 사람의 체감)**: 현 시점 다른 AI와 비교해 “혜자”로 느끼는 요인으로 원문에 다음이 언긽됨 — OpenClaw **OAuth 등록 가능**, **`덕테이프`(불명·제품/기능명으로 추정)를 폭넓게(무료 계층 포함) 제공한 점**, **OAuth 기반 Codex SDK로 API를 대체 가능하다고 느끼는 점**, **10배 토큰 이벤트** 등. 검증 여부나 공식 명칭은 대화만으로는 불명.
- **[GitHub Spec Kit](https://github.com/github/spec-kit) 활용 논리**: “대충 만들어줘” 대신 **무엇을 만들지 정리 → 원칙 → 기술 계획 → 할 일 목록 → 에이전트가 순차 구현**으로 **실수를 줄이고 체계적으로** 돌게 한다는 서술. 비개발 배경에서 바이브코딩 할 때 특히 도움이 됐다는 경험담과 연결됨.
- **개인 앱 ↔ 실제 사용자(회사망) 서비스의 격차(실전 체험)**  
  - 구현 형태 요약: **자주 찾아봐야 하는 정보 검색**, **구독 시 변경 시 메일 알림**, **OCI 무료 백엔드**, **기존 개인 블로그 DNS를 변형해 사용**, **회사 네트워크 안에서만** 사용 가능하게 구성.  
  - 교훈: **내가 에이전트와 디버깅하는 것**과 **불편을 미리 잡아내기**, **구조적 부채**는 다름 / **프레임워크·개발 레일을 모른 채 “지껄여서” 완제품에 가깝게 만드는 난이도**가 높다는 표현을 보존. 약 한 달 반 전에는 `cmd`·`ipconfig` 수준이었다는 본인 서술.
- **실무 납품·확장을 위한 문서 최소선(예시)**: 최소 **`프레시마켓_기획서.pdf` 수준**의 기획·정책 문서가 있어야 개발 시 **일관된 확장**이 된다는 경험담.
- **에이전트 성능 조건으로 정리된 문장들**  
  - “**어느 정도 갖춰진 Command를 줘야 그것을 수행하는 AI가 빛을 발한다**.”  
  - “**기본 지식이라는 레이어 위에 AI라는 레일을 깔아야 고속열차**” 같은 비유(아직 초기 국면이라는 전제 포함).
  - **시스템 프롬프트로서의 `claude.md`**: “내 컴퓨터/내 프로젝트” 두 맥락이 크게 두 가지라는 서술(세부 차이는 링크·아티펙트 참조 필요).
- **AI·코딩 ‘공부법’ 논의(합의에 가까운 방향)**  
  - 트렌드를 **마스터하겠다는 식으로 ‘공부’**하면 속도 때문에 불가능에 가깝다는 쪽.  
  - **내가 AI로 할 일·재미를 정의**한 뒤 **워크플로·제품을 직접 만들며** 체화(원문: “돌고돌아 순정이듯”, “내게 맞는 옷”).  
  - “**팔만대장경 마스터**” 비유와 “**질문을 잘하면** 된다”는 조언형 문장이 반복적으로 등장.

- **실무 시간 투입 vs 워크플로 성숙(한 사례)**: 고객사 서비스 제작 중 **하루 10시간 넘게** 약 1년 지나서야 **자기 워크플로**가 잡히기 시작했다는 서술(일반 법칙 단정 아님, 경험 공유).

- **Claude 관점 분석(추측 성격 포함)**: “**해자(모양새)·경쟁력**”이 **모델 성능 + Claude Code 하네스**에 있다는 주장 형태로 제시되고, **소라 접고 GPT 유 잉여 역량을 코덱스로 몰아주는** 운영이면 곧 역전 가능성 같은 **전망**이 언긽됨(사실 검증 불명).

- **Claude Code Agent View 관련 노하우성 한 줄**: Agent View 때문에 **tmux를 안 써도 될 수도**, 다만 **split pane** 같은 UI 니즈가 있으면 별개라는 취지. 추가로 **`/goal`은 토큰 소모가 크니 자제**한다는 개인 의견 형태 언긽.

---

## 추천·자료

- **[Spec Kit(GitHub)](https://github.com/github/spec-kit)**: AI 코딩 전 **스펙·계획·태스크**를 표준 플로로 묶어 **실수 줄이며 구현 순서 고정**하려 할 때 참고된다는 설명과 맞물림.
- **[Claude Code Agent View 블로그 글](https://claude.com/blog/agent-view-in-claude-code)**: 에이전트·터미널 작업 방식 업데이트 맥락에서 공유됨(tmux 필요성 논의와 연결).
- **[클로드 Code Agent View·`/goal` 정리 스레드(엉클잡스)](https://www.threads.com/@unclejobs.ai/post/DYOI45uifXA?xmt=AQG01wzhsPUW-MCdRzkfubEl-mjOeKXSDcJrbXZtnjhVmw)**: 터미널·목표 기능 변화와 tmux 등 워크플로 대체 가능성 논의의 근거 링크로 사용됨.
- **`claude.md` 구축 가이드(스레드)**: 채널 안에서 “내 PC vs 프로젝트” 두 종류 설명과 함께 추천됨 — 스레드: [링크](https://www.threads.com/@unclejobs.ai/post/DYM9fAZCWvv?xmt=AQG0jq3Mdv-W4ZkEFu1RdzOZgXOP0uTFemPj5oLao49w69a-ZFsFwEx2qMi31USGOQz-7mSm&slof=1).
- **Claude 공개 아티펙트(초보용 정리 초안)**: “두 가지 방식으로” 정리했다는 사용자 공유물 — 검토 요청 목적이라 맥락상 교육·온보딩 자료 후보로 볼 수 있음: [링크](https://claude.ai/public/artifacts/e4129ff2-7aa7-4d11-b77d-c64eb2491027).
- **Claude에서 터미널 관리 개선 안내로 공유된 스레드**(choi.openai): [링크](https://www.threads.com/@choi.openai/post/DYOFJR-Gxul?xmt=AQG0jxhAlcOLNM4AAnOnNO8w7vCBEGmesGNOltUW3SvyfbtKrSeijOKuWDxb0Y0yHYp0PF7C&slof=1).

- **유튜브(대화 속 제목 중심)**  
  - *Claude Cowork Fundamentals \| Tina Huang* — Cowork 도입 입문 용도로 제시된 링크: [링크](https://youtu.be/uGwDuvSqgYI)  
  - *Multi-Agent Building In Claude Code* — 멀티 에이전트 구성 참고용으로 나열: [링크](https://youtu.be/ZAaxx3qyT8g)  
  - *Claude Code Skill 심화* — 14시 시작 공지와 함께: [링크](https://youtu.be/Mw8SnA_xgQo)

- **`autopreso` (GitHub)** — 발표용: 실시간 음성 입력을 발표 형태로 바꿔준다는 취지로 소개된 저장소로 보임 — [링크](https://github.com/kunchenguid/autopreso).

- **Pactify** — Notion과 채팅·동기·요약류 워크플로 키워드로 공유됨 — 제품 페이지 [pactify.io](https://pactify.io/), 크롬 스토어 익스텐션 검색 결과로 포함된 형태: [스토어 링크](https://chromewebstore.google.com/detail/pactify-%E2%80%93-chat-to-notion/epjklkklbfakcddlcnihffeebfecmcea).

- **업스테이지 세미나·외부 학습**(사전 신청 안내 형태로만 등장): AX Standard & Trend 2026 신청 페이지 [링크](https://kernel.fastcampus.co.kr/upstage_seminar), 관련 학습류로 혼공바 조태호 영상 [링크](https://youtu.be/D48rWCQOuO4).

- **`agentpeek.app`**: Claude Code·Codex를 **Mac 노치 영역에서** 다룬다는 취지로 나열됨(플랫폼 제한 명시 필요).

---

## 의미있는 링크

- [**Claude Code Agent View 블로그**](https://claude.com/blog/agent-view-in-claude-code) — 아침 일찍 기능 공유·후속 에이전트/tmux 논의로 이어짐.
- [**GitHub Spec Kit**](https://github.com/github/spec-kit) — 바이브코딩·비개발자 한계 극복 스킬로 길게 평가·질문이 붙음.
- [**클로드 `claude.md` 가이드 스레드**](https://www.threads.com/@unclejobs.ai/post/DYM9fAZCWvv?xmt=AQG0jq3Mdv-W4ZkEFu1RdzOZgXOP0uTFemPj5oLao49w69a-ZFsFwFwEx2qMi31USGOQz-7mSm&slof=1) — 채널 규칙·온보딩 질문(“처음부터 어떻게”)과 접점 있음 *(URL은 입력에 있는 문자열 그대로 사용해야 함 — 입력 검증: 사용자 URL에 typo 있을 수 있음. 원문:`...FqFwFxEx2qMi31USGOQz-7mSm` actually user had `FqEx2qMi` - I'll copy exactly from user message)*

Checking user URL: `https://www.threads.com/@unclejobs.ai/post/DYM9fAZCWvv?xmt=AQG0jq3Mdv-W4ZkEFu1RdzOZgXOP0uTFemPj5oLao49w69a-ZFsFwEx2qMi31USGOQz-7mSm&slof=1`

I'll use exact copy without introducing typos.

- [**공개 Claude 아티펙트(초보용 정리 2종)**](https://claude.ai/public/artifacts/e4129ff2-7aa7-4d11-b77d-c64eb2491027) — “한번 봐달라”는 요청과 반응.
- [**Agent View 업데이트·`/goal` 스레드**](https://www.threads.com/@unclejobs.ai/post/DYOI45uifXA?xmt=AQG01wzhsPUW-MCdRzkfubEl-mjOeKXSDcJrbXZtnjhVmw) — tmux·`/goal`·토큰 소모 우려로 후속 발언 다수.


- **[롱블랙 기사 — AI ‘신입 심사역’](https://longblack.co/note/1976?ticket=NT26204fa9bc4e6bc6489613e528645800bd78)** — AI 에이전트가 스타트업 발굴~평가까지 담당한다는 흐름으로 스레드에 링크됨.
- **[조선 — 구글 제로데이 관련 기사](https://www.chosun.com/economy/tech_it/2026/05/11/YEMVZZE7XZFWVGPRL3NW6BGJYY/)** 및 **추가 뉴스 링크 2종**(조선 IT, 로봇뉴스) — 보안·산업 트렌드 공유맥.
- **[Vercel DeepSec 블로그](https://vercel.com/blog/introducing-deepsec-find-and-fix-vulnerabilities-in-your-code-base)** — 오픈소스 코딩 보안 하네스 소개 형태로 언긽.
- **X 포스트**(Prompt PlayBook for Claude Opus 4.7): [링크](https://x.com/rubenhassid/status/2053324202321834073)
- **Adjective AI**(제품 비주얼): [https://www.adject.ai/](https://www.adject.ai/)

---

## 결정·약속·일정

- **당일(입력 내 “오늘” 기준)**: 채널 안에서 **오픈클와(불명·정확 제품표기는 원문 따라 ‘오픈클로’)** 대화 중 잡히는 과정 공유 의도 표현(단, 구체 결과물 확정까지는 불명).

- **`2026-05-13` 라이브(입력 원문 시간·제목 동일 처리)** — 일부 **20시**가 겹침(사용자 입장에서는 동시 시청 선택 이슈).  
  - [OpenClaw 특강 (코난쌤) · 05/13 20시](https://youtu.be/HiusK82AI5M)  
  - [Codex Master Class (개발동생) · 05/13 20시](https://youtu.be/F5pr-hfyuEE)  
  - [Claude Cowork + Design + Pencil (모두의 AI 공장장) · 05/13 21시](https://youtu.be/fCO-5kX7B5g)  
  - [Codex (조쉬) · 05/13(수) 20시](https://youtu.be/BBg17O2Fa_U)

- **`2026-05-13` 당일 이전 안내였던 라이브**: “**Claude Code Skill 심화** … **14시부터**” — [영상 링크](https://youtu.be/Mw8SnA_xgQo).

- **`2026-05-12`** 다우 기사 등으로 “**300명**” 규모 언긽(별도 행사·주제 디테일은 텍스트만으로 불명): [링크](https://v.daum.net/v/20260512182700605).

- **향후**(운영 주체 발언 성격): “**매주 주간 AI 이슈 + 튜토리얼**” 유튜브 활성화 예고, 초대석 유즈케이스 연재 예고 등 — **구체 일정 미확정**으로 ‘약속 후보’ 수준만 기록 가능.

---

## 반응이 컸던 화제

- **Spec Kit 소개 및 스펙 구동 개발(TDD·SDD 맥락)**: 바이브코딩 확산·실수 방지에 대한 필요와 맞아떨어져 짧지만 연쇄 반응.


- **“AI 어떻게 공부하지?”**(유튜브만 보면 더 어려워짐) → **워크플로·제품으로 경험** 쪽 조언과 공감이 이어진 스레드.
- **구독 믹스·가격**(클로드 맥스 vs GPT 플러스·코덱스 이벤트·API 대체 가능성 논의): 정서적 공감(비쌈)과 플랫폼 선택 고민으로 길게 이어짐.
- **클코·지피티·커서 병행, `/clear` 타이밍** 같은 **도구별 습관·의존도** 잡담에 가깝지만 여러 사람이 교차하여 응답.

---

## 자동화·시스템 적용 후보

- **Spec Kit 순서**(요구 정리→원칙→기술 계획→태스크→구현): 팀 온보딩·외주 브리핑 에이전트용 **표준 게이트**로 재사용 가능.
- **`claude.md` 이원화**(개인 장비 vs 리포 단위 프로젝트) 프롬프트 전략: 멀티레포·멀티 PC 사용 시 규칙 분리 패턴 참고 후보.


- 아래 두 축으로 제시된 **이미지 프롬프트 템플릿**은 제품 디자인·마케팅 에셋 재현용으로 즉시 복사·변수 치환 가능:  
  - **캐리커처(길거리 화가 3만원 느낌·두꺼운 펜·파스텔·과장 귀여움)** — 출처까지 스레드에 명시: [링크](https://www.threads.com/@da.p.ham/post/DYL6PH-Eu9C)  
  - **브랜드 로고 구름**(미니멀·초현실 브랜딩 새트) — 대화 속 `[BRAND NAME]` 치환형 프롬프트  
  - **픽사풍 패션 돌 같은 8컷 콜라주** 장문 스타일 프롬프트

- **블로그**·미디어 업로드를 **반자동/일괄**로 만들고 싶다는 요구(스레드·인스타·네이버 블로그) — 패턴 이름만 있고 해결안은 채팅에 없음, **설계 과제 후보**.

---

## 질문·미해결

- **블로그 자동화 콘텐츠**가 “언제 올라오냐”는 질문에 대해 발표자가 **완전 자동보다 반자동** 쪽으로 테스트 중이라고만 회신되어 **출시 시점 불명**.
- **7월 GPT Pro 만료 후** Claude Max 재구독 등 **개인 선택**은 채팅에서 결론 없이 남음.
- **코덱스 vs 클코 편리함**(“어디서 많이 본 기능”) 같은 **차별 포인트**는 농담 수준 교차만 있어 **근거 있는 비교표 수준까지는 미해결**.

---

(요약 규칙 준수: 추측·보강으로 URL이나 기능을 새로 만들지 않았고, 제품 명이 불분명하면 **불명**으로 두었으며, 채널 속 핵심 문장은 따옴표로 보존했습니다.)

---

## 요약 통계

- 메시지 수: 82
- 기간: 2026-05-12 00:13 ~ 2026-05-12 23:16

### URL (32개)

- https://claude.com/blog/agent-view-in-claude-code
- https://github.com/github/spec-kit
- https://www.threads.com/@unclejobs.ai/post/DYM9fAZCWvv?xmt=AQG0jq3Mdv-W4ZkEFu1RdzOZgXOP0uTFemPj5oLao49w69a-ZFsFwEx2qMi31USGOQz-7mSm&slof=1
- https://claude.ai/public/artifacts/e4129ff2-7aa7-4d11-b77d-c64eb2491027
- https://www.threads.com/@choi.openai/post/DYOFJR-Gxul?xmt=AQG0jxhAlcOLNM4AAnOnNO8w7vCBEGmesGNOltUW3SvyfbtKrSeijOKuWDxb0Y0yHYp0PF7C&slof=1
- https://longblack.co/note/1976?ticket=NT26204fa9bc4e6bc6489613e528645800bd78
- https://www.chosun.com/economy/tech_it/2026/05/11/YEMVZZE7XZFWVGPRL3NW6BGJYY/
- https://it.chosun.com/news/articleView.html?idxno=2023092161905
- https://www.irobotnews.com/news/articleView.html?idxno=46276
- https://www.threads.com/@unclejobs.ai/post/DYOI45uifXA?xmt=AQG01wzhsPUW-MCdRzkfubEl-mjOeKXSDcJrbXZtnjhVmw
- ... +22 more

### 멘션

- @unclejobs: 2
- @choi: 1
- @da: 1
- @엉클잡스: 1

---

# Fronmpt Academy — 2026-05-12

- 메시지 수: 82

**00:13** [P26]
저는 파누스님 의견과 조금 다릅니다
여러 AI 를 겪어봤을때,
현재 가장 혜자스럽다고 생각합니다.
현재 기준으로 보면 Openclaw Oauth 로 등록 가능한점, 최근 덕테이프 바로 모두에게(심지어 무료계정까지) 풀어버린 점, API를 대체할 수 있는 Oauth기반 Codex SDK를 제공한다는 점. 이 외에도 현재 10배 토큰 이벤트라던지 수시로 초기화 해쥬는 등등은 아직까지 다른 AI에서는 못느끼고 있어서요..^^ 물론 저는 클로드 맥스 유저이고 코덱스는 플러스로 즐기고 있는데 너무 잘 쓰고 있습니다

**08:09** [P26]
<https://claude.com/blog/agent-view-in-claude-code>

**08:21** [P16]
와우

**08:25** [P26]
<https://github.com/github/spec-kit>
아주 쉽게 말하면 Spec Kit은 “앱/프로그램을 만들기 전에 설계서 → 계획서 → 작업목록 → 구현” 순서로 AI가 일하게 만드는 도구입니다.

즉, 그냥 AI에게

“대충 이런 앱 만들어줘”

라고 하는 대신,

1. 무엇을 만들지 정리
2. 어떤 원칙으로 만들지 정리
3. 기술 계획 작성
4. 해야 할 일 목록 생성
5. AI 코딩 에이전트가 순서대로 구현

이렇게 실수 적게, 체계적으로 개발하게 해주는 도구입니다.
좋은 스킬 같아서 공유드려봄미다

**08:28** [P7]
스펙킷

**08:45** [P38]
감사합니다~~~

**09:08** [P4]
tdd sdd  요즘 대세인듯 함니다 ㅋㅋ

**09:15** [P15]
오 좋으네요 빼먹는거 없게 만들기

**09:18** [P26]
요즘 바이브코딩으로 이것저것 만들어서 쓰고 최근에 팀원들도 쓸수있게 서비스까지 하다보니 비개발자라서 한계가 좀 생기더라고요 그런 저에게 너무 소중한 스킬이라 ㅋ

**09:20** [P8]
어떤 프로그램을 팀원들께 배포하셨을지 궁금합니다.

**09:32** [P26]
그냥 매번 찾아가서 봐야하는 정보를 쉽게 검색해서 보고 원하는 일부 정보는 구독하면 정보 변경이 있을때 메일로 알림주게끔 만들어봤어요
백엔드는 제 무료 OCI에 구축하고 DNS는 제가 이미 쓰고 있던 개인블로그에서 변형해서 썼어요 ㅋ
회사망 안에서만 쓸 수 있게끔 해두었구요
이정도 하는데에도 시행착오가 꽤 많았던;;;

**09:33** [P7]
엄청나십니다

**09:33** [P26]
개인이 사용할 앱을 만드는 것과 대중에게 서비스한다는건 정말 차원이 다르다는걸 많이 느꼈습니다

**09:34** [P7]
어떤면에서 다르다는것을 크게 느끼셨나요

**09:36** [P26]
내가 쓰면서 디버깅을 코딩에이전트와 하는 것과 대중이 느낄 수 있는 불편함을 미리 고친다던지
구조적인 문제를 나중에 풀어야하는 상황이라던지 이런게 생기더라고요.. 제 개인적인 도메인은 전혀 다른 영역이다보니 개발쪽의 프레임워크를 모르고 그저 지껄여서 만든 앱을 완성한다는게 상당히 난이도가 높다고 느꼈습니다 ㅋ 
한달 반정도 됐어요 바이브코딩 한지..;; 그전에는 cmd 누르고 ipconfig 정도나 칠줄 알았던 수준이라..
콤타를 30년 넘게 만져오면서 파워쉘이랑 이렇게 친해질거라고는 상상도 못했네요

**09:37** [P7]
그 한달반동안 엄청나게
투쟁하셨겠군요 

**09:38** [P26]
ㅎㅎㅎ 네 너무 재밌어서 시간가는줄 모르고 요즘 꺼져있던 열정 불씨가 

**09:38** [P6]
송강기님의 의견에 정말 공감하는 부분인데
예를 들어
제가 고객사 미팅후 실제로 만들어서 납품하는 서비스의 기획/정책 문서를  최소
파일: 프레시마켓_기획서.pdf
이정도 잡아야 개발할때 일관성있는 확장이 가능하더라구요

**09:44** [P26]
정말 요즘 시대에 깊은 도메인이 필요한 이유가 아닐까 싶어요
한달반 삽질의 교훈으로 다시 초기로 돌아가는 상황이 좀 웃프지만..

**09:53** [P7]
어느정도 갖춰진 Command를 줘야 그것을 수행할 AI가 빛을 발한다는 것이군요

**09:55** [P4]
기본 지식이라는 레이어 위에 ai라는 레일을 깔아야 고속열차 되는거죠 아직은 

**10:01** [P4]
클로드.md는 크게 2가지 있습니다
내 컴퓨터에서 내 프로젝트에서
시스템 프롬프트가 되는
클로드 쩜 md를 잘 구축하기 위한
아티클을 준비했습니다
<https://www.threads.com/@unclejobs.ai/post/DYM9fAZCWvv?xmt=AQG0jq3Mdv-W4ZkEFu1RdzOZgXOP0uTFemPj5oLao49w69a-ZFsFwEx2qMi31USGOQz-7mSm&slof=1>

**10:01** [P30]
기존에 LLM에 단순하게 질문하고 답받고 하는 식으로 써왔는데 요즘 바이브 코딩에 대한 필요성, 그리고 AI를 더 활용을 잘 해야겠다는 생각을 하고 있습니다. 처음부터 공부를 한다면 어떤식으로 하는게 좋을지 조언을 듣고 싶습니다. 그냥 유튜브 이것저것 보다보니 오히려 더 어렵게 느껴지더라구요ㅠㅠ

**10:01** [P4]
공부한다는 마음으로 가시면 안됩니다
트렌드가 너무 빨리 바뀌고
발전속도가 기하급수적이에요
그냥 만들어서 흐름을 익히셔야 합니다

**10:02** [P30]
그걸 따라가기가 너무 버겁네요...ㅠㅠㅠ 너무 빨리 새로운게 나오고 그래서
뭐든 하나씩 만들어봐야한다는 말씀인가용
사이트 만들어보고, 앱만들어보고 그런식으루요?

**10:02** [P4]
그런 트렌드만 
쫓아가면 결코 못 따라가요

**10:03** [P6]
맞아요

**10:03** [P4]
워크플로우든 제품이든 만들어보셔야 합니다
돌고돌아 순정이듯

**10:03** [P6]
정말 공감합니다.

**10:03** [P4]
기본 지식이 젤 중요하져 
내가 성장해야 뭐든 써먹는
그러니 시키는 기술을 읽으셔야할때
ㅋㅋㅋㅋㅋㅋㅋㅋ
공지에 왕초보를 위한 아티클과
시키는 기술 잇으니 권해드림니다
앞으로 유튜브를 활성화 할거에요
매주 주간 에이아이슈를 다루고 튜토리얼을 녹여낼겁니닷

**10:04** [P6]
세상에 나와있는 AI 기술 / 트랜드를 한사람이 지금 다 경험해보는건 현실적으로 불가능하다 생각합니다.

따라서 현재 "내가" AI를 활용해서 해볼만한 / 하고싶은 것이 무엇인지 정의한다음에 먼저 파보는걸 추천드립니다

**10:04** [P4]
그리고 초대석으로 계속해서 잘 활용허눈 분을 유즈케이스로 풀어보겟읍니다 ㅋㅋ

**10:05** [P30]
그런식으로 접근을 해야겠네요... ai를 마스터하겠다는 생각보다는 나에게 맞는걸 찾아서...

**10:05** [P4]
개발 지식 마스터 하겟다응 마인드랑 같습니다
불가눙하죠 너무 방대해서 

**10:05** [P30]
저한테 맞는 옷을 찾아 입어야겠네요...

**10:05** [P4]
팔만대장경 마스터 하겟다응 거랑 같슴니다 ㅋㅌㅌ
쏙쏙 발라드셔야 함니다 ㅋㅋ
질문을 잘하면 뭐든 가능하심니더

**10:06** [P6]
저는 개발사를 운영하면서 AI를 개발에만 활용하며
지난 1년동안 하루에 10시간 넘게  고객사  서비스 만들고했는데 

이제서야 저만의 워크플로우가 조금씩 잡혀서 생산성을 높이고 있는 상황입니다^^

**10:06** [P4]
독서가 가장 중요한듯 ㅌㅋ

**10:06** [P21]
이미 a i 가 공부 끝냄. 유튜브에 있어요

**10:06** [P26]
진짜 이건 진짜 세상의 진리일지도..
문해력 = 경쟁력 이라고 읽슴미다

**10:07** [P4]
컨텍스트는 에이아이만 중요한게 아닌듯 ㅋㅋ 맥락은 우리도 중요허다~ 

**10:07** [P38]
주신 자료로  claude한테 정리부탁 했는데 함 봐주셔요~
<https://claude.ai/public/artifacts/e4129ff2-7aa7-4d11-b77d-c64eb2491027>
초보자를 위해서도 두가지 방법으로요

**10:08** [P22]
지난 .. 블로그 자동화에 대한 내용은 혹시 언제 올라올까요? 유료여도 사겠슴다
ㅋㅋㅋㅋ

**10:09** [P4]
ㅋㅋㅋㅋㅋㅋㅋㅋ 백프로 자동화는 아이지만 반자동화 콘텐츠 찍어봅니다 ㅋㅋ 

**10:09** [P6]
저는 지금 저의 브랜드로  
스레드/인스타/네이버 블로그 에 일괄업로드 할수 있는 자동화 만들고 싶은데  @엉클잡스 잘부탁드립니다 ㅋㅋ
살려주세요…ㅋㅋ

**10:09** [P4]
일괄 ㅋㅋㅋ 너무 어려운 시스쳄 구축 아님니까요

**10:09** [P26]
스크롤 압박이 좀 있을 수 있는데 오늘 아침에 오픈클로랑 대화나누면서 잡아가는 중인거 공유드려봄니다 ㅎㅎ
에라이 ㅜㅜ 넘 깋어서 짤리네여

**10:10** [P22]
7월에 gpt pro 끝나는데
고민이네요 ..... 클로드맥스를 다시 할지.. ㅋㅋ

**10:10** [P4]
클로드의 장점이 붐명하니 병행하면 짱이긴 하져 

**10:10** [P6]
저는 지금 클로드 맥스 + 아내의 지피티플러스 로 사용중인데
여전히 클로드 코드 의존성이 높긴하네요 ㅋ

**10:11** [P22]
ㅋㅋㅋㅋ 클코가 편하긴 하더라고요 - 

**10:11** [P6]
진짜 손에 익어버려서 ㅠㅠ
그 .. 느낌있잖아요 ㅋ 지금쯤 /clear 한번때려주면 되겠다 싶은 타이밍 ㅋㅋ

**10:12** [P4]
설계 기획은 클로드가 젤 나은것 같슴니다 ㅋㅋ

**10:12** [P9]
클로드가 제일 티키타카가 잘되서 잘뽑아주는거 같아요
그치만 너무 비쌉니다 ㅋㅋ

**10:16** [P6]
비싼녀석.....

**10:16** [P7]
비싼 baby

**10:18** [P26]
비싸져.. 코덱스는 10배 이벤트까지하고..
API도 대체해주고..
저도 클코맥스랑 지피티 플러스랑 커서 구독해서 써보고 있는데 지피티는 정말 혜자스러움....

**10:22** [P4]
클로드 지금 해자를 유지하는게 모델 성능이랑 클로드코드 하네스인데 
소라 접고 지피유 남아도는 그 여력을
코덱스에 몰아주는 식으로 하고 잇어서
이렇게 서비스 운영하다가는 금방 또 역전될듯 하네여

**10:25** [P12]
<https://www.threads.com/@choi.openai/post/DYOFJR-Gxul?xmt=AQG0jxhAlcOLNM4AAnOnNO8w7vCBEGmesGNOltUW3SvyfbtKrSeijOKuWDxb0Y0yHYp0PF7C&slof=1>
클로드에서 터미널 관리가 용이한 기능이 추가됐네요

**10:42** [P14]
지금 제미니 1년 구독하는 김에 안티그래비티 절찬 활용중인데 코덱스 나쁘지 않나보네용

**10:45** [P1]
<https://longblack.co/note/1976?ticket=NT26204fa9bc4e6bc6489613e528645800bd78>
스타트업 발굴부터 평가까지…‘신입 심사역’ 자리 차지하는 AI 에이전트

<https://www.chosun.com/economy/tech_it/2026/05/11/YEMVZZE7XZFWVGPRL3NW6BGJYY/>
구글 “AI 활용한 대규모 제로데이 공격 시도 첫 포착”

<https://it.chosun.com/news/articleView.html?idxno=2023092161905>
<https://www.irobotnews.com/news/articleView.html?idxno=46276>

**11:21** [P4]
<https://www.threads.com/@unclejobs.ai/post/DYOI45uifXA?xmt=AQG01wzhsPUW-MCdRzkfubEl-mjOeKXSDcJrbXZtnjhVmw>
agent view 업뎃된것과 /goal출시되서 관련 내용을 작성해봤습니다
agent view 기능 덕분에 tmux는 안 써도 될듯..? 
split pane 쓰는거 아니면 말이죠

**11:24** [P10]
어디서 많이 봤던 기능들...

**11:25** [P4]
codex? ㅋㅋ

**11:31** [P10]
ㅋㅋㅋ 그러긴 한데.. 일단 오늘 나온 기능들은 좀 기대가 되네요

**11:39** [P4]
토큰 불타오를 것 같아서 goal은 자제해야것어요.. ㅋㅋㅋ

**11:40** [P6]
ㅋㅋㅋㅋㅋ
불타오르네~

**11:46** [P7]
위협하네요 슬슬

**12:01** [P23]
얘네들이 저가로 엄청 뿌리고 있는데…
소프트웨어까지….
방향성 좋네요.. 

**12:08** [P1]
Open-source Coding Security Harness

<https://vercel.com/blog/introducing-deepsec-find-and-fix-vulnerabilities-in-your-code-base>
Prompt PlayBook for Claude Opus 4.7

<https://x.com/rubenhassid/status/2053324202321834073>

**12:15** [P1]
Hyperrealistic Product Visuals with AI

<https://www.adject.ai/>
Claude Code and Codex in Mac notch

<https://agentpeek.app/>

**12:45** [P1]
RealTime Speech to Presentation

<https://github.com/kunchenguid/autopreso>
Claude Cowork Fundamentals | Tina Huang

<https://youtu.be/uGwDuvSqgYI>
Multi-Agent Building In Claude Code

<https://youtu.be/ZAaxx3qyT8g>

**13:44** [P1]
Claude Code Skill 심화

14시부터 시작합니다.

<https://youtu.be/Mw8SnA_xgQo>

**14:11** [P1]
Prompt

첨부한 이미지를 길거리 화가에게 3만원 주고 캐리커쳐 그린 것 처럼 만들어. 흰색 배경에 두꺼운 펜으로 한번에 스케치하고 파스텔로 칠해. 진짜 얼굴 특징을 잘 살려서 누가봐도 어떤 사람인걸 알 수 있게. 울퉁불퉁 엄청나게 과장했지만 나름 귀여워서 본인이 봐도 웃음 나오게. 웃길수록 팁드림

출처 : <https://www.threads.com/@da.p.ham/post/DYL6PH-Eu9C>

**14:56** [P1]
<https://youtu.be/pd4b6X0FtOg?si=1x8CWy6bi2dtJ5HB>

**18:34** [P1]
<https://youtu.be/u-FdxoSeoWQ?si=PvEv3hjVvrJQaXQn>
Pactify – Chat to Notion | Sync & Distill AI

<https://pactify.io/>

<https://chromewebstore.google.com/detail/pactify-%E2%80%93-chat-to-notion/epjklkklbfakcddlcnihffeebfecmcea>

**18:57** [P1]
내일 라이브 리스트들 ㅋㅋㅋㅋ (이거 말고 더 있기는 합니다.)

OpenClaw 특강 (코난쌤)
- 일시 : 05/13 20시
- 링크 : <https://youtu.be/HiusK82AI5M>

Codex Master Class (개발동생)
- 일시 : 05/13 20시
- 링크 : <https://youtu.be/F5pr-hfyuEE>

Claude Cowork + Design + Pencil (모두의 AI 공장장)
- 일시 ; 05/13 21시
- 링크 : <https://youtu.be/fCO-5kX7B5g>

Codex (조쉬)
- .일시 : 05/13(수) 20시
- 링크 : <https://youtu.be/BBg17O2Fa_U>


**19:43** [P1]
AI Contents

<https://drive.google.com/drive/mobile/folders/1CgN7DE3pNRNh_4BA_zrrMLqWz6KquwuD>

**19:44** [P7]
우와

**19:58** [P1]
Upstage AX Standard & Trend 2026

관심이 있는 분들은 사전 신청하시기 바랍니다.~~

<https://kernel.fastcampus.co.kr/upstage_seminar>
혼공바 with 조태호

<https://youtu.be/D48rWCQOuO4>
Instagram Algorithm Update 2026

<https://avocadosocial.com/instagram-algorithm-update-2026-what-actually-drives-reach-now/>

**20:23** [P1]
Prompt

[BRAND NAME]: The name of the brand.
Goal: Generate a single, minimalist, and surreal image where a cloud is shaped like the brand's logo.

1. THE LOGO CLOUD
- **Subject**: A massive, photorealistic cumulus cloud in the exact geometric shape of the [BRAND NAME] logo.
- **Texture**: Puffy, soft, and voluminous with natural sunlight illuminating the edges.
- **Volume**: 3D sculptural appearance with realistic shadows within the cloud folds to show depth.

2. ENVIRONMENT & BACKGROUND
- **Sky**: A vast, clear, vibrant blue summer sky.
- **Secondary Elements**: A few small, wispy, natural clouds scattered far in the background to enhance the sense of scale and realism.
- **Lighting**: Bright, direct daylight coming from the side to create high-contrast highlights and shadows.

3. INTEGRATED BRANDING
- **Text**: The word "[BRAND NAME]" written in a clean, bold white sans-serif font.
- **Icon**: A small, flat white version of the brand's logo placed next to the text.
- **Positioning**: The branding (text + logo) is centered at the bottom of the frame, acting as a subtle anchor to the giant cloud above.

4. STYLE
- Surrealist photography, ultra-minimalist composition, high resolution, 8k, cinematic look, clean and airy vibe.

Prompt

Ultra detailed stylized 3D Pixar-inspired girl collage, same female character in 8 different aesthetic outfits and poses, short messy neon yellow bob hair, soft freckles, glossy brown eyes, cute feminine face, cozy fashion aesthetic, oversized knitted sweaters, black crop sweater, hoodie, yellow sporty outfit, scarf, cargo pants, sneakers, expressive poses, warm cinematic lighting, smooth skin shading, soft shadows, vibrant red, beige and yellow minimal studio backgrounds, cute cartoon realism, high detail fabric texture, fashion doll proportions, dreamy modern Pinterest aesthetic, symmetrical collage layout, ultra HD, adorable mood, realistic hair strands, soft glow, premium 3D render, octane render style


**23:16** [P1]
<https://v.daum.net/v/20260512182700605>
오우~~ 300명

