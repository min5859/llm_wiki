<!-- kakao-db: ai-summary v1 -->
# AI 요약

## 인사이트·노하우

- **DeepSeek / Kimi IDE 연동 방법**
  - Kimi: 직접 구독 후 CLI로 사용 가능
  - DeepSeek: API Router(예: OpenRouter) 경유 필요
  - Cursor·Antigravity에서 모델을 직접 지원하지 않는 경우 → **Zed 에디터** 권장 ("가볍다, 강추")
  - Zed는 터미널 느낌이 강하고 별도 설정 필요하나 공개 포스팅 미존재(독학 필요)

- **DeepSeek API 비용 감각** (P26 실사용 경험)
  - 100달러 충전 후 1개월 사용 → 약 8달러 소비 (매우 저렴)
  - "죽어있는 웹앱에 DeepSeek API를 붙이면 활용도 높음" — VPS + 가비아 도메인 조합 추천

- **DeepSeek 찍먹 전략** (P1 추천)
  - Command Code 1달러 구독 → DeepSeek 약 10달러치 이용 가능
  - 또는 OpenCode Go 첫 달 5달러 요금제로 저렴하게 시작
  - 코딩 경험이 있다면 OpenRouter에 직접 충전

- **워크플로 분기 권장안** (P1)
  - 가벼운 작업: Command Code → OpenCode Go
  - 딥한 작업: Claude 또는 GPT
  - ("딥한 작업아니면 commandcode → opencode go 이렇게 짱")

- **Cursor Composer 2.5** — 커뮤니티 평가 좋음, "가성비 최고" 평 확산 중 (다만 트렌드 변화 빠름)

- **DeepSeek V4 Flash** — Nous Portal에서 무료 재개방됨. Hermes Agent 사용자 확인 권장
  - 링크: [Nous Portal 구독 관리](https://portal.nousresearch.com/manage-subscription)

---

## 추천·자료

### 도구

- [Antigravity IDE Multi-Account Switchboard](https://github.com/erennyuksell/ag-multi-account-switchboard) — Antigravity IDE에서 다중 계정을 전환할 수 있는 도구
- [oh-my-claw](https://github.com/jkf87/ohmyclaw) — OpenClaw 관련 설정/유틸리티 도구 (맥락상 Claude CLI 래퍼로 추정, 상세 불명)
- [AionUi](https://github.com/iOfficeAI/AionUi) — AI 에이전트와 함께 오픈소스 협업 작업을 위한 UI
- [Zed 에디터](https://zed.dev) — 가볍고 빠른 코드 에디터; DeepSeek·Kimi 등 비주류 모델 연동 시 대안으로 추천
- [Cue (usecue.xyz)](https://usecue.xyz/) — macOS용 초경량 인라인 AI 어시스턴트; 어떤 텍스트 필드에서도 바로 AI 사용 가능
- [Freu AI](https://www.freu.ai/) — Mac용 AI 에이전트 앱
- [Model Hub (Conscious Engines)](https://studio.consciousengines.com/model-hub) / [GitHub](https://github.com/conscious-engines/modelhub) — macOS 메뉴바 앱 형태의 모델 허브
- [Prototype Bench](https://github.com/prototypebench/prototypebench) — 프로토타입 벤치마킹 도구 (상세 불명)
- [FigMirror](https://github.com/VILA-Lab/FigMirror) — 논문 스타일로 데이터 플롯을 재현해주는 도구
- [Agent Gateway (Edgee)](https://www.edgee.ai/fallback-models) — AI 에이전트용 폴백 모델 게이트웨이
- [What Cable](https://www.whatcable.uk/) — USB-C 케이블 사양 확인 도구 (기술 맥락에서 공유)
- [Runway Agent](https://app.runwayml.com/) — Runway의 AI 에이전트 기능
- [Obsidian Excalidraw Plugin](https://github.com/zsviczian/obsidian-excalidraw-plugin/) — Obsidian 내 Excalidraw 통합; 플러그인 개발자가 미래 방향 발표
- [Hada 뉴스 - Excalidraw 관련](https://news.hada.io/topic?id=29653) — 위 플러그인 관련 GeekNews 토픽

### 모델

- [SANA-WM Bidirectional](https://huggingface.co/Efficient-Large-Model/SANA-WM_bidirectional) — HuggingFace 공개 이미지 생성 모델 (양방향 지원)
- [MS Lens Turbo](https://huggingface.co/microsoft/Lens-Turbo) — Microsoft의 HuggingFace 공개 모델 (상세 불명)

### 독서·영상

- [다시, 읽기로 1부 — 읽기 도파민](https://youtu.be/X9Ek3gRp-Fw) — 디지털 시대 독서 습관을 다룬 다큐 1부
- [다시, 읽기로 2부 — AI 시대, 읽기의 반격](https://youtu.be/uQYWztR5UKU) — AI 시대에 책 읽기의 의미를 다룬 다큐 2부
- [다시, 책으로 (yes24)](https://www.yes24.com/product/goods/73031896) — 순간접속 시대 독서의 의미를 다룬 도서
- [AI 에이전트로 스타트업 1인 운영하는 방법](https://youtu.be/IDqdVZwAwjw) — 1인 창업에 AI 에이전트를 활용하는 실전 영상
- [Excalidraw 플러그인 개발자: Obsidian 플러그인의 미래](https://youtu.be/wedHXARs6n4) — Obsidian 생태계 플러그인 방향성 논의

---

## 의미있는 링크

- [YC의 AI Native Company 정의 (LinkedIn)](https://www.linkedin.com/posts/gb-jeong_yc가-ai-native-company를-정의했습니다-회사의-운영체제를-share-7464315740825886720-pTLK/) — YC가 AI 네이티브 기업을 정의한 내용, 회사 운영체제 관점
- [DeepSeek 중국 가성비 전략 분석 (Threads)](https://www.threads.com/@unclejobs.ai/post/DYv1vIeCfq9?xmt=AQG0_di5vW_nByoOYlT9gnmi0ZBwUiPwlKXPm166WK-ncA) — DeepSeek V4 Pro 75% 영구할인 vs Gemini 3.5 Flash 가성비 비교 분석
- [중앙일보 기사](https://www.joongang.co.kr/article/25430744) — 공유된 시사 기사 (제목 불명)
- [디지털투데이 기사](https://www.digitaltoday.co.kr/news/articleView.html?idxno=666128) — 공유된 IT 기사 (제목 불명)
- [한겨레 경제 기사](https://www.hani.co.kr/arti/economy/economy_general/1260309.html) — 공유된 경제 기사 (제목 불명)

---

## 반응이 컸던 화제

- **DeepSeek API 비용 및 도입 방법** — P17의 질문을 시작으로 P1·P26·P7이 각자 실사용 경험과 전략을 공유하며 가장 길게 토론됨. "얼마 충전해서 쓰냐"는 실용적 질문이 촉매가 되어 구체적 워크플로까지 전개됨.

- **Cursor Composer 2.5 평가** — P1과 P7이 짧게지만 서로 호응하며 "가성비 최고" 평을 공유. 커뮤니티 전반의 관심사임을 시사.

---

## 자동화·시스템 적용 후보

- **"죽은 웹앱 + DeepSeek API" 패턴** (P26): 기존 방치된 웹앱에 DeepSeek API를 연결하고, VPS + 저렴한 도메인(가비아)으로 배포하면 비용 대비 재활용성이 높음. 개인 사이드 프로젝트 부활 전략으로 재현 가능.

- **비용 효율 AI 워크플로 스택**: 
  - 경량 작업: Command Code(1달러) → OpenCode Go(5달러/월)
  - 중량 작업: Claude / GPT
  - 모델 탐색: OpenRouter 충전 방식
  - 무료 실험: Nous Portal (DeepSeek V4 Flash 무료)

---

## 질문·미해결

- **Zed 에디터 설정 방법** — P17이 질문했으나 P1이 "아직 포스팅 없음"으로 답변. 공개 가이드 부재 상태, 후속 포스팅 예정.
- **DeepSeek/Kimi를 Cursor·Antigravity에서 직접 모델 등록하는 방법** — 현재는 공식 지원 없음으로 결론났으나, 향후 IDE 지원 여부 추적 필요.

---

## 요약 통계

- 메시지 수: 29
- 기간: 2026-05-25 10:10 ~ 2026-05-25 23:58

### URL (28개)

- https://www.linkedin.com/posts/gb-jeong_yc가-ai-native-company를-정의했습니다-회사의-운영체제를-share-7464315740825886720-pTLK/
- https://github.com/erennyuksell/ag-multi-account-switchboard
- https://github.com/jkf87/ohmyclaw
- https://github.com/iOfficeAI/AionUi
- https://www.threads.com/@unclejobs.ai/post/DYv1vIeCfq9?xmt=AQG0_di5vW_nByoOYlT9gnmi0ZBwUiPwlKXPm166WK-ncA
- https://www.joongang.co.kr/article/25430744
- https://youtu.be/X9Ek3gRp-Fw
- https://youtu.be/uQYWztR5UKU
- https://www.yes24.com/product/goods/73031896
- https://youtu.be/j4vy9u3fqgc
- ... +18 more

### 멘션

- @unclejobs: 1

---

# Fronmpt Academy — 2026-05-25

- 메시지 수: 29

**10:10** [P7]
AI Native Company | YC

<https://www.linkedin.com/posts/gb-jeong_yc가-ai-native-company를-정의했습니다-회사의-운영체제를-share-7464315740825886720-pTLK/>
Antigravity IDE Multi-Account Switchboard

<https://github.com/erennyuksell/ag-multi-account-switchboard>

**10:35** [P7]
oh-my-claw

<https://github.com/jkf87/ohmyclaw>

**11:13** [P7]
Open Source Cowork with AI Agent | AionUi

<https://github.com/iOfficeAI/AionUi>

**13:08** [P1]
DeepSeek v4 pro 가격ㅇ을 75% 할인했죠 
그것도 영구히. 

구글은 Gemini 3.5 Flash를 내놨으나
가성비에서 물매를 맞았습니다

미국의 프런티어 모델을 위협하는
중국의 가성비 전략을 가볍게 다뤄보았습니다
<https://www.threads.com/@unclejobs.ai/post/DYv1vIeCfq9?xmt=AQG0_di5vW_nByoOYlT9gnmi0ZBwUiPwlKXPm166WK-ncA>

**13:22** [P17]
딥식 또는 키미는  코드고나 오픈라우터 이용하는 것 밖에 방법이 없는거죠?  커서나 안티그래비티에서 모델을 등록해서 쓰거나 할수 있는 방법은 없을까요? 

**13:22** [P1]
kimi는 직접 구독해서 cli로 사용 가능합니다 
딥시기는 api router로 해야하구여 
커서나 안티그래비티에서 모델 지원해주면 그걸 사용 가능하시지만 
그렇지 않은 경우는 zed 추천드립니다

**13:24** [P17]
오. 어제  포스팅하신것 보고 zed설치는 했는데 한번 해보겠습니다. 

**13:24** [P1]
제드 가볍습니다 강추 ㅎ

**13:25** [P17]
근데 제드는 터미널 같은 느낌이 강하고 설정을 많이 해줘야 하나보네요.. 설정을 어떻게 하는건지 혹시 올려주신 포스팅 있으실까요? 

**13:27** [P1]
아녀 아직 업로드한 포스팅은 없습니다

**13:28** [P17]
그럼 포스팅하실때까지 독학해보겠습니다. 감사합니다. ~ 

**13:38** [P7]
<https://www.joongang.co.kr/article/25430744>

**14:07** [P7]
* 다큐 추천 및 도서 추천

다시, 읽기로 - 1부 읽기 도파민

<https://youtu.be/X9Ek3gRp-Fw>

다시, 읽기로 - 2부 AI 시대, 읽기의 반격

<https://youtu.be/uQYWztR5UKU>

다시, 책으로 | 순간접속의 시대에 책을 읽는다는 것

<https://www.yes24.com/product/goods/73031896>

**14:13** [P10]
잘보겠습니다

**16:13** [P7]
DeepSeek 사용하시는 분들은 보통 얼마 충전해서 사용하시나요? 75% 영구할인이라 한번 사용해 보려고 합니다.


**16:20** [P27]
오 저도 궁금한

**17:17** [P1]
command code 1달러 사용가능한데 딥식이 10달러치? 이용 가능할거에요 그거를 사용해서 찍먹해보시거나, OpenCode Go 첫달 요금제 5달러로 사용하시면서 저렴하게 이용해보시길 권해드립니다
코딩 경험이나 작업을 좀 해보셨다면 OpenRouter에다가 충전해서 사용하셔도 되구요

**17:42** [P26]
전 100달러 충전해놓고 이것저것 쓰는데 정말 안줄더라구요 ㅋㅋㅋ 
한달썼는데 8달러였나
제일 좋은건 죽어있는 웹앱에 딥시크api가져다 붙이면 정말 좋더라구요 
vps에 가비아에서 도메인하나 구독해서 앱만들고 거기에 딥시크붙이면 꽤 재밋어요

**17:45** [P7]
<https://youtu.be/j4vy9u3fqgc>

**18:49** [P1]
DeepSeek V4 Flash가 Nous Portal에서 다시 무료로 풀렸대요.
Hermes Agent 쓰시는 분들 참고로 한번 확인해보세요~

링크: <https://portal.nousresearch.com/manage-subscription>

**19:09** [P7]
<https://www.digitaltoday.co.kr/news/articleView.html?idxno=666128>
<https://www.hani.co.kr/arti/economy/economy_general/1260309.html>

**19:47** [P7]
AI 에이전트로 스타트업을 1인 운영하는 방법

<https://youtu.be/IDqdVZwAwjw>
command code 1달러 구독 후 사용해보고 있는데 나름 쓸만하네요~~ ㅎㅎ

**19:51** [P1]
딥한 작업아니면 commandcode -> opencode go 
이렇게 짱입니다 ㅋㅋ

**19:53** [P7]
deep work는 claude or gpt ㅎㅎㅎㅎ

**19:55** [P1]
요새 커서 컴포저 2.5 평이 좋네요 
가성비 최고라고 

**20:00** [P7]
맞아요. composer 2.5 엄청 평이 좋더라구요~~ => 이것도 한때 이기는 하지만요 ㅋㅋㅋㅋ
인라인 AI 어시스턴트 앱

내가 입력 중인 텍스트 필드 안에서 바로 쓰게 해주는 macOS용 초경량 AI 도구

<https://usecue.xyz/>

**20:18** [P7]
Excalidraw 플러그인 개발자: Obsidian 플러그인의 미래

<https://youtu.be/wedHXARs6n4>

<https://github.com/zsviczian/obsidian-excalidraw-plugin/>

<https://news.hada.io/topic?id=29653>

SANA-WM (Bidirectional)

<https://huggingface.co/Efficient-Large-Model/SANA-WM_bidirectional>
MS Lens Turbo

<https://huggingface.co/microsoft/Lens-Turbo>
Prototype Bench

<https://github.com/prototypebench/prototypebench>
FigMirror: Plot Your Data in Any Paper's Style

<https://github.com/VILA-Lab/FigMirror>

**23:39** [P7]
Runway Agent

<https://app.runwayml.com/>

**23:58** [P7]
Agent Gateway

<https://www.edgee.ai/fallback-models>
USB-C Cable

<https://www.whatcable.uk/>
AI agent for Mac

<https://www.freu.ai/>
Native macOS Menu Bar App

<https://studio.consciousengines.com/model-hub>

<https://github.com/conscious-engines/modelhub>

