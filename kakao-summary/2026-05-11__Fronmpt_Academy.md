<!-- kakao-db: ai-summary v1 -->
# AI 요약

## 인사이트·노하우

### Manus Android 96% 할인 결제 사태 (2026-05-11)
- **발생**: 안드로이드 Google Play 의 Manus 앱에서 연간 Pro 결제가 ₩32,000 (96% 할인 자동 적용) 으로 표시되는 가격 오류 발생
- **공유된 결제 절차**:
  1. 안드로이드 Google Play 에서 Manus 앱 설치
  2. 새 계정 생성 후 결제 화면 진입
  3. 연간 Pro 결제 → ₩32,000
  4. 결제 후 "업그레이드" 버튼 → 20,000 크레딧 플랜 연간 결제 → 추가 ₩32,000
  5. 총 ₩64,000 으로 2년간 월 20,000 크레딧 이용 가능 (정가 대비 약 60만원 상당)
- **macOS / iPhone 우회**: 블루스택(맥용 안드로이드 에뮬레이터) 설치 후 가입하면 결제 가능했음. 단, 외국 계정으로는 안 됨
- **결제 후 진행 양상**:
  - 사용자 계정에 따라 성공/실패가 엇갈리고 순차적으로 막힘 (복불복)
  - 일부 계정 정지 사례 발생
  - 결국 Manus 측이 **사용자 동의 없이 일방적으로 취소 처리**. 다만 결제 2건 중 1건만 환불되고 나머지 약정은 유지되는 등 처리 일관성 없음
  - 공식 사과·공지 없이 대응 → 안티 양성 우려 의견 다수
- **교훈**: 명백한 가격 오류로 보일 때는 결제해도 사후 일방 취소·계정 정지 리스크가 있음. 환불·약정 처리도 비대칭적일 수 있음

### Manus 활용 포지셔닝 논의
- Manus 는 메일 생성, 웹사이트 생성, 텔레그램 연동 등 다목적 에이전트성 기능 제공
- 다만 토큰(크레딧) 단가 부담이 커서 Manus·Genspark 등 여러 도구를 병행하기 부담스러움 → 사용자별로 메인 1개를 정하는 것이 합리적
- 23만 크레딧 요금제는 구독 기간 3개월(매월 결제) 이므로 단기 대량 사용에 적합

### Claude Code 의 HTML 생성 활용
- P9 의 대학교 특강 준비 경험: Anthropic 의 Thariq 가 "HTML 이 새로운 마크다운" 이라 주장한 맥락과 일치하게, **Claude 가 클로드 디자인급 HTML 디자인을 뽑아준다**는 것을 실제 작업에서 확인. LLM 산출물 포맷팅 전략으로 마크다운보다 HTML 을 고려할 가치가 있음

## 추천·자료

- **[Terminal Playground](https://terminal-playground.netlify.app/)** — Windows & Mac 터미널 학습용 인터랙티브 플레이그라운드. 초보 실습용으로 평가됨
- **[Manus 용 K-Skill](https://www.threads.com/@bunniesossdev/post/DYLv3A6ET_W)** — Manus 에서 사용할 수 있는 한국형 스킬 셋 소개 (Threads 게시물)
- **[Seedance Prompt 아카이브](https://www.seedance2prompt.com/ko/prompts)** — Seedance 영상 생성 프롬프트 한국어 아카이브
- **[wmux](https://github.com/amirlehmam/wmux)** — `cmux` 의 Windows 포팅판. 터미널 멀티플렉서를 Windows 에서 쓰고 싶을 때
- **[Tiro CLI](https://api-docs.tiro.ooo/cli/cli-overview)** — Tiro 의 신규 CLI 도구 (출시 공지)
- **[Academic Research Skills for Claude Code](https://github.com/Imbad0202/academic-research-skills)** — Claude Code 용 학술 연구 스킬 모음. 연구 흐름 자동화 후보
- **[Clipdrop](https://clipdrop.co)** — 원래 AR 기반 이미지 추출에서 출발해 카피라이팅·이미지 편집 SaaS 로 확장. **월 1,200원** 의 저렴한 구독 옵션이 매력 (P3 가 수년간 사용 중)
- **[강수진 박사: Perplexity API Cookbook](https://www.linkedin.com/posts/sujin-prompt-engineer_perplexity-api-cookbook-perplexity-activity-7457102473703366656-qz10)** — 프롬프트 쿡북 현황 공유
- **[Long Black: Prompt Cookbook 노트](https://longblack.co/note/1975?ticket=NT2620bcbb39c7acd4cd47758313dacc493afc)** — 쿡북 관련 분석/소개 글
- **[디자인픽 레퍼런스 시트](https://docs.google.com/spreadsheets/d/1i1i4x2h0jkRr-IHlV7uNM_KeiGZ0wY2UquRF6hiI13w/edit?gid=735671903)** — 디자인 레퍼런스를 모아둔 구글 스프레드시트
- **[TikTok Shop Basic 101 웨비나](https://www.tiktokacademy.com/student/page/3352552-south-korea-tiktok-shop-basic-101)** — 한국 틱톡 샵 기초 무료 웨비나 신청 페이지
- **영끌맨 Threads** — Manus/Genspark 사용 안 하는 사용자에게 추천된 비교·정리 콘텐츠 (P9 추천)
- **[클립드롭 소개 영상 1](https://youtu.be/ogKO58Ay2Z8)**, **[클립드롭 소개 영상 2](https://youtu.be/D6gYMzbFHzM)** — Clipdrop 초기 AR 서비스 시절 영상

## 의미있는 링크

- **[Manus 결제 대응 정리 (Uncle Jobs Threads)](https://www.threads.com/@unclejobs.ai/post/DYMEm8xCQWU)** — Manus 사태가 "이벤트가 아니라 오류" 였다는 점을 일목요연하게 정리. 방 안에서 "잘 정리됐다" 는 반응
- **[Anthropic Thariq 의 HTML-as-Markdown 맥락 글](https://www.threads.com/@unclejobs.ai/post/DYGSYMmkwt9)** — Claude 의 HTML 산출물 품질 논의로 이어진 글
- **[영끌맨의 Manus/Genspark 활용 콘텐츠](https://www.threads.com/@ai_younggle_man/post/DYL6P2Qk-eA)** — 어떤 도구를 메인으로 쓸지 고민할 때 참고
- **[Lucas Flatwhite 트윗](https://x.com/lucas_flatwhite/status/2053633907262996932)** — 공유 후 추가 토론 (구체 내용 불명)
- **[Dilum Sanjaya 트윗](https://x.com/DilumSanjaya/status/2053155739389378849)** — "우와 멋지다" 는 반응 유발 (구체 내용 불명)
- **[카카오 KOMI 학생 GO 프로모션](https://b.kakao.com/views/komi/promotions/student-go?t_src=kakaotalk&t_ch=digitalcard&t_obj=event-benefit)** — 학생 인증 기반 ChatGPT Go 관련 프로모션으로 추정. "학생이고 싶다" 는 아쉬움 반응
- **[중앙일보: Academic Research Skills 관련 기사](https://www.joongang.co.kr/article/25427388)** — 학술 리서치 스킬 GitHub 와 함께 공유됨

## 반응이 컸던 화제

- **Manus 96% 할인 사태**: 하루 종일 대화의 중심. 결제 인증→ 우회 팁→ 계정 정지 사례→ 일방 취소 사태→ 공식 대응 부재 비판으로 흐름이 이어지며 거의 모든 참가자가 참여. 노이즈 마케팅 의혹과 "안티 양성 프로젝트" 라는 비판까지 등장
- **메타의 Manus 인수 루머**: 중국 정부가 막았다는 풍문, 그럼에도 표시상 메타와 엮여 있는 점, 얀 르쿤 이탈·"A. 왕" 영입 이후 메타가 실속을 못 보고 있다는 평가가 곁들여짐
- **OpenAI / Anthropic 의 영리성 논의** (23시대): "OpenAI = CloseAI", "진정한 돈미새는 Anthropic" 등 가벼운 농담이지만 여러 명이 호응. 빅모델 업체의 폐쇄성·수익화에 대한 누적 불만의 정서적 표출
- **"다시 쓰는 능력주의" 책 제목 제안** (P14): 능력주의를 다룬 책 집필 계획을 공유하며 제목 피드백 요청 → 호응 있었음

## 결정·약속·일정

- 명시적 외부 일정(웨비나 등)은 링크만 공유됨. 채팅 참가자 간 약속·마감일은 발견되지 않음

## 자동화·시스템 적용 후보

- **Academic Research Skills for Claude Code** (`Imbad0202/academic-research-skills`): Claude Code 의 스킬 체계로 학술 리서치 워크플로를 자동화하는 사례. 자신의 리서치 파이프라인에 도입 후보
- **Tiro CLI**: 어떤 도메인의 CLI 인지 한 줄로 확정되지 않았으나, CLI 워크플로 자동화 가능성 점검 대상
- **wmux**: cmux 의 윈도우용 포트. 멀티 OS 환경의 터미널 작업 표준화에 활용 검토 가능
- **K-Skill for Manus**: 한국형 작업 패턴을 Manus 에 주입하는 스킬 셋 → 자체 에이전트 구성 시 한국어 워크플로 템플릿 참고용

## 질문·미해결

- **ChatGPT Go 에서 Codex 가 되는가?** (P37) → P38 이 "GO 는 Codex 없을 것" 이라 답했으나 공식 확인은 아님. 사용 가능 모델·기능 범위 추가 확인 필요
- **Manus 환불 정책의 일관성**: 결제 2건 중 1건만 환불되거나, 일부는 50% 환불·약정 유지 등 처리 기준 불명확. 공식 가이드 미발표
- **메타의 Manus 인수 최종 확정 여부**: 중국 정부 제동 이후 재딜 가능성 거론되었으나 확정 정보 없음
- **클립드롭의 사업 영역 변화 경위**: AR → 카피라이팅 → 이미지 편집으로 확장된 정확한 변천사는 단편적으로만 공유됨

---

## 요약 통계

- 메시지 수: 123
- 기간: 2026-05-11 07:45 ~ 2026-05-11 23:38

### URL (21개)

- https://terminal-playground.netlify.app/
- https://www.linkedin.com/posts/sujin-prompt-engineer_perplexity-api-cookbook-perplexity-activity-7457102473703366656-qz10
- https://longblack.co/note/1975?ticket=NT2620bcbb39c7acd4cd47758313dacc493afc
- https://www.tiktokacademy.com/student/page/3352552-south-korea-tiktok-shop-basic-101
- https://docs.google.com/spreadsheets/d/1i1i4x2h0jkRr-IHlV7uNM_KeiGZ0wY2UquRF6hiI13w/edit?gid=735671903#gid=735671903
- https://www.threads.com/@bunniesossdev/post/DYLv3A6ET_W
- https://www.seedance2prompt.com/ko/prompts
- https://github.com/amirlehmam/wmux
- https://x.com/lucas_flatwhite/status/2053633907262996932
- https://x.com/DilumSanjaya/status/2053155739389378849
- ... +11 more

### 해시태그

- #gid: 1

### 멘션

- @unclejobs: 2
- @ai_younggle_man: 1
- @bunniesossdev: 1
- @playbook: 1
- @드론: 1

---

# Fronmpt Academy — 2026-05-11

- 메시지 수: 123

**07:45** [P3]
Manus for Android 대란이네요 ㅋㅋㅋㅋ (아이폰은 안된다는 소문이 있네요)

**07:59** [P19]
맥북 아이폰 사용자는 블루스택 까셔서 하세요. 블루스택=맥에서 안드로이드 구동

**08:01** [P17]
공식 앱에서 이벤트 한건가요?

**08:03** [P3]
안드로이드 공식 앱 다운로드 받으셔서 확인해 보시기 바랍니다.

**08:15** [P36]
저거 구독하면 피씨나 다른 디바이스에서도 되지 않나요?

**08:16** [P17]
한번 써볼려는데 왜 계정이 막혀있을까요...ㄷㄷ

**08:17** [P19]
외국에선 안됩니당

**08:58** [P3]
결제 방법 (커뮤니티 공유 기준)

안드로이드 Google Play에서 Manus 앱 설치
새 계정 생성 후 결제 화면 진입
연간 Pro 결제 → ₩32,000 (96% 할인 자동 적용)
결제 후 "업그레이드" 버튼 → 20,000 크레딧 플랜 연간 결제 → 추가 ₩32,000
총 ₩64,000으로 2년간 월 20,000 크레딧 이용 가능

**08:58** [P7]
성공했습니다

**09:15** [P17]
사진 2장
업그레이드 할때 이런식으로도 되는것 같던데 해보신 분 있나요?
연구독이 아니라서 2개월만 되는 것 같은데, 크레딧이 말이 안되네요

**09:18** [P11]
저는 연구독은 32000원으로 했는데, 크레딧은 안되네요.

**09:22** [P19]
ㄷㄷㄷ 저거 되는거 보니까 의도하지 않은것으로 보이네요. 취소메일 올지도

**09:23** [P9]
메타 인수 불발된게 이렇게 흘러가나요 

**09:31** [P3]
ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

**09:34** [P13]
ㅋㅋㅋㅋㅋㅋㅋ

**10:39** [P23]
아 무조건
아묻따 결재해야겠죠?

**10:40** [P3]
사용자 게정에 따라 성공하시는 분도 있고, 안되는 분돞 있습니다. 순차적으로 막히고 있다고 합니다.

**10:55** [P28]
계정 정지 당한분들이 속속 나오고 있습니다 주의하세요

**10:56** [P11]
계정정지면...환불은 해주고 시키는 걸까요.ㄷㄷㄷ

**10:58** [P3]
Windows & Mac Terminal Playground

이런 것도 있네요~~

<https://terminal-playground.netlify.app/>

**11:00** [P14]
서울에서도 곧
열릴듯

**11:01** [P11]
초보실습용으로 너무 좋아보여요

**11:27** [P3]
Prompt Cookbook 현황 (강수진 박사)

<https://www.linkedin.com/posts/sujin-prompt-engineer_perplexity-api-cookbook-perplexity-activity-7457102473703366656-qz10>
<https://longblack.co/note/1975?ticket=NT2620bcbb39c7acd4cd47758313dacc493afc>

**11:31** [P9]
되신분들 부럽습니다..
마누스로 html 생성하고 ppt 생성하고 할거 천지삐까리일텐데 

**11:31** [P3]
아이폰이나 맥에서 블루스택 설치 후 가입하는 방법도 되기는 합니다. ㅎㅎㅎㅎ 

**11:32** [P9]
오 지금되나여? ㅋㅋ

**11:33** [P3]
순차적으로 막히고 있다고 하는데 복불복인 것 같습니다.
다른 방에 보니 되시는 분들도 있고, 안되는 분들오 있네요.

**11:33** [P9]
2년치 결제하신분도 계시던데 ;;

**11:33** [P3]
전 2년치 결제 ㅋㅋㅋㅋ

**11:34** [P17]
230,000  크레딧 요금제 쓰시는 분들이 있긴 하더라구요

**11:34** [P3]
그건 구독기간이 3개월입니다.

**11:34** [P17]
매달 결제긴 한데
네 근데 크레딧 양보면 단기간에 대량으로 쓰시는 분들에겐 좋은 것 같아요

**11:35** [P3]
Manus를 메인으로 자주 사용하신다면 그게 답일 수도 있습니다.

**11:38** [P9]
헐 ㅋㅋㅋ 2년치 

**11:38** [P12]
2만크레딧으로 2년 했는데 그냥 사용하면 되겠죠?
6만4천원들었습니다 ㅋㅋ

**11:39** [P9]
ㅋㅋ 60만원어치 아닌가요 요고 

**11:39** [P12]
@드론@playbook 님 정보 감사해요!

**11:40** [P9]
진짜 두분은 칭송받으셔야 합니다 

**11:40** [P14]
Openclaw는 안하려나요

**11:40** [P11]
저도 아까 삽질했는데, Pro 1년 선택해서 32,000원 결제 후
다시 업그레이드 들어가서 크레딧 20,000 선택하니 금액할인이 전혀 안먹게 보이더라구요(연납)

근데 그냥 다음단계 진행하니 플레이스토어 결제 창에는 32,000원으로 나와서 냅다 승인했습니다.

**11:41** [P9]
마누스 메타 잉ㄴ수 불발된거 아니엇나요 ㅋㅋ
어느샌가 메타가 붙어잇네요

**11:41** [P11]
메타 호소인

**11:41** [P14]
인수된걸로 아는데

**11:41** [P3]
아직 최종은 인수 확정이 안되기는 했어요. 다시 확정될 수도 있기에 남아 있는 것이라고 하네요.

**11:42** [P9]
최근에 중국 정부에서 막았다고 들었는데 다시 딜하나보네여
메타 지금 뒤쳐지고 잇어서 사활을 걸고있을듯 ㅋㅋㅋ

**11:42** [P14]
A. 왕 영입 이후에
별 소식이 없네요

**11:43** [P9]
얀 르쿤 나가고 뭔가 마니 하는데 실속을 못 보나봐여

**11:43** [P14]
그 둘이 조금 미묘했죠 
말은 사이 나쁘지않았다 그러는데

**11:46** [P3]
TikTok Shop Basic 101 웨비나 신청

<https://www.tiktokacademy.com/student/page/3352552-south-korea-tiktok-shop-basic-101>

**11:55** [P3]
디자인 레퍼런스 (디자인픽)

<https://docs.google.com/spreadsheets/d/1i1i4x2h0jkRr-IHlV7uNM_KeiGZ0wY2UquRF6hiI13w/edit?gid=735671903#gid=735671903>

**12:00** [P23]
마누스 활용가치 높을까요?

**12:01** [P28]
문서 잘만들더라고요

**12:05** [P36]
일단 결제했네요 카드가 안되는군요

**13:05** [P3]
K-Skill for Manus

<https://www.threads.com/@bunniesossdev/post/DYLv3A6ET_W>

**13:26** [P14]
오
K-SKILL

**13:40** [P3]
Seedance Prompt 아카이브

<https://www.seedance2prompt.com/ko/prompts>

**13:56** [P3]
cmux 윈도우용이 나왔네요

<https://github.com/amirlehmam/wmux>

**15:02** [P3]
<https://x.com/lucas_flatwhite/status/2053633907262996932>

**15:03** [P29]
이 방의 참가자분들은 마누스를 어떤 용도로 사용하시는지 궁금합니다.

제 경우 젠스파크를 사용하다보니 마누스와 겹치는 부분들이 있어 마누스는 사용하지 않고 있습니다만..

**15:06** [P3]
<https://x.com/DilumSanjaya/status/2053155739389378849>

**15:10** [P7]
우와
멋지네요

**15:11** [P14]
감사해요 항상 ㅠ

**15:11** [P9]
<https://www.threads.com/@ai_younggle_man/post/DYL6P2Qk-eA?xmt=AQG0aS-dhzzqREaxVD3LXVE7pAQXyryPJGj8po-amQqLS22wp4_ATgPv7S_H0EJvuWGdwW0V&slof=1>
저도 마누스나 젠스파크는 사용하질 않아서 영끌맨님 콘텐츠를 참고하시면 좋을 것 같습니다 ㅎ

**15:12** [P14]
아 잡스님 아티클인줄

**15:14** [P19]
마누스 기능이 참 맘에드네요 메일도 만들어줘 웹사이트도 만들어줘 텔레그램 연동도해줘

**15:14** [P7]
오늘 마누스 강의하면 조회수 잘나오겠네요

**15:27** [P28]
토큰이 비싸여 글케 세가지나 하기 힘듬 ㅋㅋㅋ

**15:41** [P9]
<https://www.threads.com/@unclejobs.ai/post/DYMEm8xCQWU?xmt=AQG016dkd_YY_VgBFpUh3g6kAbQx6-MmqHydtLr-JWxvRXsD7SGq-pIKCjxpuQ_DX4Nplu_F&slof=1>
마쩐쿠 탑승함니도 

**15:43** [P14]
이벤트가 아니었군요
오류
일목요연하게 정리해주셨네요

**15:55** [P9]
<https://b.kakao.com/views/komi/promotions/student-go?t_src=kakaotalk&t_ch=digitalcard&t_obj=event-benefit>
대란데이인가요 오늘

**15:56** [P14]
아 머여
학생증?!!
에이..

**15:56** [P25]
학생이고싶다 ...

**15:57** [P14]
그러게요 

**15:57** [P37]
지피티 고도 코덱스 되나요??

**15:57** [P14]
명함 인증하고 바로 시작!

**15:57** [P37]
지금 프로 하나 + 플러스 하나로 영끌해서 쓰는 중이라 5분만 쓸 수 있어도 소중한데..

**15:57** [P14]
이건 없나..

**15:58** [P7]
go는 뭐죠?

**16:02** [P38]
GO는 코덱스 없을걸용?

**16:02** [P14]
능력주의에 관한 책을 쓸 계획인데

**16:02** [P38]
플러스보다 싼 모델입니당

**16:02** [P14]
다시 쓰는 능력주의
책 제목 어떤가요 

**16:11** [P9]
괜찮은 것 같습니다 ㅎ

**16:41** [P3]
<https://youtu.be/KtYs5W2yzjg>

**16:53** [P14]
감사합니다

**17:24** [P9]
이번에 대학교 특강 준비하는데
anthropic의 thariq가 Html이  새로운 마크다운이라고 
주장한게 괜한게 아니네여

**17:25** [P14]
와

**17:25** [P9]
클로드 디자인급으로 Html 디자인을 뽑아주는군여

**17:25** [P14]
멋지십니다
혹시 서울대인기요

**17:25** [P9]
<https://www.threads.com/@unclejobs.ai/post/DYGSYMmkwt9?xmt=AQG0vRQLQsVrN3QG8hnKEFmDjm17SZlAbPpNKNwIZ7FziA>
아녀 ㅋㅋㅋ 

**17:32** [P14]
하하 넵
근데 언젠가 서울대 오실거같습니다

**17:35** [P9]
서울대 계시다는 말씀이신가여? ㅋㅋ

**17:36** [P14]
아주 예전에 졸업했습니다 ㅋ

**18:11** [P36]
마누스 결제 된거 취소됐네요

**18:15** [P14]
잡스님이 훨씬 더 대단하십니다 !!!

**18:29** [P9]
마누스 취소 사례 속출하네요 지금

**18:32** [P13]
마누스 사태를 처음 접하고 ㅋㅋ 마누스가 미소스 인줄알고 "벌써 일반유저들에게 풀렸나…" 하고 착각한 1인입니다 ㅋㅋ

**18:33** [P9]
다들 낚이셨군요 

**18:37** [P19]
마누스 노이즈 마케팅인가
수수료만 내면 되는 노이즈마케팅..

**18:38** [P7]
취소엔딩 ㅠ
전 크레딧만 회수하고 아직 결제 취소도 안됐네요

**18:40** [P12]
아 취소네요 ㅠ
취소는 왜 하나만 됐을까요?ㅠㅠ

**18:45** [P9]
오히려 여론 안 좋아질 것 같은데 흠

**18:50** [P11]
100% 환불도 아니고 크레딧 추가만 환불해버리는 건지, 또는 다 해제시키고 돈만 50% 환불하는 거면 정말이지 마누스 안티양성 프로젝트.

**19:36** [P9]
클립드롭은 뭔가요 

**19:37** [P3]
<https://clipdrop.co>
전 몇 년 전부터 월 1200원 구독중입니다. ㅋㅋㅋㅋ

**19:39** [P9]
얘네 카피라이팅 전문 saas 였느네 어느샌가 이미지로 확장햇나여 ㅋㅋ

**19:40** [P3]
최초 서비스는 AR 관련 서비스로 출발하기는 했습니다.
<https://youtu.be/ogKO58Ay2Z8>
<https://youtu.be/D6gYMzbFHzM>

**19:51** [P3]
누구든 실수는 할 수 있지만 서용자에게 아무런 동의 없이 일방적으로 취소 처리한 Manus가 이번 사건으로 퇴출되면 좋겠네요~~

**19:53** [P15]
카카오때는 환불까진 아니었는데 말이죠..

**19:58** [P9]
마누스 근데 본인들 잘못이라고 공지하던가 했어야 한다고 봅니다

**19:58** [P13]
맞아요

**20:04** [P11]
동의합니다. 결제는 2건이 일어났는데, 
그걸 일부만 공지없이 1건만 임의로 환불처리하고 나머지는 약정이 유지되는게 문제네요. 

**20:16** [P13]
공식적인 입장을 내놓아야지 이런식의 대응은..

**20:18** [P3]
Tiro CLI 출시

<https://api-docs.tiro.ooo/cli/cli-overview>

**22:14** [P3]
Academic Research Skills for Claude Code

<https://github.com/Imbad0202/academic-research-skills>
<https://www.joongang.co.kr/article/25427388>

**22:36** [P14]
우와!

**23:09** [P39]
그런데 오픈AI는 이름만 오픈이고 가장 클로즈AI이고 가장 돈을 좋아하는 회사 같아요 ㅎ

**23:09** [P14]
 하하
CloseAI

**23:17** [P14]
앤트로픽도 돈 엄청 밝히는듯

**23:24** [P9]
진정한 돈미새는 앤트로픽 입니다 ㅋㅋㅋ

**23:28** [P13]
ㅋㅋㅋㅋㅋㅋㅋㅋ

**23:34** [P11]
해프닝으로 끝난 마누스 사태

**23:38** [P14]
사람들을 지대로 우롱하는군요

