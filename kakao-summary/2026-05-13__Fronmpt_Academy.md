<!-- kakao-db: ai-summary v1 -->
# AI 요약

입력 텍스트만 기준으로 정리했습니다.

---

## 인사이트·노하우

- **Open Design로 “클로드 디자인”을 Codex·커서·로컬에서 쓰기**: Threads 포스트와 GitHub 릴리스 기준으로, OS에 맞는 프로그램을 받아 실행하면 로컬에 깔린 **Claude Code·Codex 등**을 골라 바로 디자인 작업에 연결할 수 있다는 흐름이 공유됨. 다만 대화에는 **실패·원인 분석 같은 트러블슈팅 로그는 없음**.
- **빠르게 적용하려면**: 어떤 GitHub 문서든 **「퀵스타트(Quickstart)」만 먼저** 보라는 실무 팁이 반복 강조됨 ("퀵스타트!!").
- **`/goal` 스타일 프롬프트**: 최종 결과 한 줄(`goal`) 아래에 **컨텍스트·성공 기준(측정 가능)·운영 규칙(계획 선행·자율 실행·플레이스홀더/TODO 금지 등)·품질 기준·최종 산출물 체크리스트**를 고정 포맷으로 두는 패턴. "계획을 먼저 출력하라. 그 다음 끝까지 또는 진짜 막힐 때까지 체크인 없이 실행하라" 같은 **자율 코딩 에이전트** 운용 의도.
- **구독·도구 선택(대화 속 의견만)**: 완전 초보면 **Antigravity 무료로 시작**하거나, **월 20~30달러대로 시작해 부족하면 올리기**, **초보 UX는 Codex 앱이 편하다**는 등 **개인차** 전제의 조언. (검증된 벤치마크 아님)
- **OpenAI 쪽 내부 추측(출처 불명)**: "소라에 쓰이는 GPU가 크다/관계자 말로 소라 접고 이미지·Codex 쪽에 자원" 같은 **제3자 전언·추정** 수준. **미검증**.
- **역사·실존 인물 이미지 생성**: 특정 인물(예: 대화 속 **임화 시인**)은 **사진같은 결과가 잘 안 나오고**, 다른 인물(예: **윤봉길 의사**)은 상대적으로 낫다는 **개별 경험담**(원인 분석 없음).

---

## 추천·자료

| 항목 | 한 줄 맥락 |
|------|------------|
| [ChatGPT Prompt Library (Notion)](https://co-trendr.notion.site/ChatGPT-Prompt-Library-150575ffadbb80e88534f047dc47552d?source=copy_link) | 300명 기념으로 공유된 **프롬프트 모음**. |
| [영상: Anthropic 제품 팀 속도](https://youtu.be/PplmzlgE0kg) | **앤트로픽 제품 조직 움직임** 관련 레퍼런스로 소개됨. |
| [실밸(Silvalee) 바이브코딩 무료 세미나 슬라이드](https://docs.google.com/presentation/d/1YIKqtrxF_ywFNzOl0DwvhtB6SBVYLfSS95FqXi9ZbNA/edit?slide=id.p1#slide=id.p1) | **바이브코딩** 소개 자료로 링크됨. |
| [Google Meet GoogleBook (Android)](https://blog.google/products-and-platforms/platforms/android/meet-googlebook/) | **Android** 플랫폼 신제품/발표류 링크. |
| [Microsoft New Future of Work Report 2025 (PDF)](https://www.microsoft.com/en-us/research/wp-content/uploads/2025/12/New-Future-Of-Work-Report-2025.pdf) | **미래 업무·AI** 계열 리서치 리포트. |
| [Atlantis — Beautiful Diagrams & Notes](https://fantastic-computing-machine.github.io/atlantis/) · [GitHub](https://github.com/Fantastic-Computing-Machine/atlantis) | **다이어그램·노트** 도구로 소개. |
| [Excalidraw Plus — API & MCP Beta 문서](https://plus.excalidraw.com/docs) | **MCP 연동 가능한 도면/화이트보드** 쪽 최신 기능 공개 소식. |
| [Udemy: Django Ninja (무료 쿠폰 링크)](https://www.udemy.com/course/django-ninja/?couponCode=2514B8446739AB0585A9) | **Django 웹** 학습 리소스. |
| [Reddit — Claude를 문서 작성 도구로](https://www.reddit.com/r/PromptEngineering/comments/1spmwkg/i_didnt_realise_claude_could_build_actual_word/) | **문서 작성** 용 Claude 활용 프롬프트/사례. |
| [Hugging Face — Sulphur 2](https://huggingface.co/SulphurAI/Sulphur-2-base) | 대화에서는 **비검열(uncensored) 영상 생성** 계열 모델로 언급 — **실사용·윤리·법적 검토는 각자 책임**. |
| GitHub [`anthropics/claude-for-legal`](https://github.com/anthropics/claude-for-legal) | **법률** 도메인용 Claude 관련 레포로 소개. |
| TechCrunch [AI legal services / Anthropic](https://techcrunch.com/2026/05/12/the-ai-legal-services-industry-is-heating-up-anthropic-is-getting-in-on-the-action/) | **AI 법률 서비스** 업계 동향. |
| [Obsidian Skills (공식)](https://github.com/kepano/obsidian-skills) | **Obsidian** 확장 스킬. |
| [Markdown by 김진중 — playloom.mark](https://playloom.app/mark) | **마크다운 기반 뷰어**(골빈해커님 회사 제품이라는 채팅 언급). |
| [longblack 노트](https://longblack.co/note/1977?ticket=NT262059db4e6ae5d704716941be105b9cd3fb) | 일정 시작 시점의 **외부 노트**(대화 속 설명 부족). |
| 각종 이슈/칼럼 링크 (한경 기사, bemyb 칼럼, Remember 커뮤니티, Pollo AI·KM저널, AWS Claude Platform 블로그 등) | **산업 동향·제품 소개·에세이**류로 흩어져 공유됨. |

---

## 의미있는 링크

- [Open Design — Codex/로컬에서 클로드 디자인 (Threads @unclejobs.ai)](https://www.threads.com/@unclejobs.ai/post/DX_Aj6nCTxb?xmt=AQG0PO_50lMZzjZ2YQAJjsTzJYtX55x7UQfgSjaWzhGVrA) — **반응이 크고** 설치 경로·퀵스타트 논의로 이어짐.
- [Open Design 릴리스 (GitHub)](https://github.com/nexu-io/open-design/releases) — **OS별 다운로드 → 로컬 코딩 에이전트 연결** 절차가 구체화된 지점.
- [goal 프롬프트 정리 본문 (Threads)](https://www.threads.com/@unclejobs.ai/post/DYQ_uzCiXar?xmt=AQG0vKu1DZLmFCwr8ZWX-98gw2JO2QOw1jtI0AcXMD6M1A) — `/goal` 템플릿 질의에 **직접 답으로 연결**됨.
- [정부·중소벤처 지원 정보 (bizinfo)](https://www.bizinfo.go.kr/sii/siia/selectSIIA200View.do?hashCode=01) — **모두의 창업** 비슷한 정보를 **어디서 찾나**는 질문에 실제로 달린 답.
- [중소벤처기업부 게시글 뷰](https://www.mss.go.kr/site/smba/ex/bbs/View.do?cbIdx=310&bcIdx=1064197&parentSeq=1064197) — 위와 같은 맥락의 **공지/사업** 레퍼런스.
- [Salesforce Agentforce World Tour Korea 사전등록](https://www.salesforce.com/kr/events/world-tour/korea/register/) — 일정·장소가 텍스트로 명시됨.
- 추가로 대화에 나온 URL: [RAG is Dead 영상](https://youtu.be/RvkbPPZbPgc?si=HziPA2bApp605UOx), [Computer Use in Codex](https://youtu.be/D_FCYsshMI4?si=iNi565I-7bsOaizu), [reCAPTCHA 논란 관련 글들](https://reclaimthenet.org/google-broke-recaptcha-for-de-googled-android-users) · [HN 토픽](https://news.hada.io/topic?id=29306), [GitHub is Sinking](https://dbushell.com/2026/04/29/github-is-sinking/), [GPT 2.0 원피스 짤 생성 프롬프트 (@iniprompt)](https://www.threads.com/@iniprompt/post/DYMkcU8iSfy?xmt=AQG0cfmlmwP5ZVTeiEloYmlMiDPSoxuZqqOjjb6TI4W8Bg), OpenAI 배포 회사 관련 [Facebook 공유 링크](https://www.facebook.com/share/1CSq6ZxPeB/), [Claude Platform on AWS](https://claude.com/blog/claude-platform-on-aws), 한경 기사·X·폴로 AI 영상 등 — **후속 정보 탐색의 출발점**으로만 적혀 있음.

---

## 결정·약속·일정

- **Salesforce Agentforce World Tour Korea**: **2026-06-10(수) 09:00–17:00**, **COEX 컨벤션 센터**, 오프라인(승인 시)·온라인, [등록](https://www.salesforce.com/kr/events/world-tour/korea/register/).
- **모두의 창업**: 대화일 **2026-05-13** 기준 **“금요일까지” 신청** 언급 → **해당 주 금요일(2026-05-16)** 로 해석 가능(원문이 “이번 주 금요일”인지는 **불명**). [P12]는 **금요일에 올릴 예정**이라고 함.
- **사업자 있으면 안 된다**는 조건 언급으로 [P28]는 포기 — **프로그램 세부 자격은 공식 공지 재확인 필요**.

---

## 반응이 컸던 화제

- **클로드 디자인을 Codex·로컬(Open Design)에서 쓴다**: “우와”“대바”“진작 알았는데 못 써본다” 등 **도구 접근성·생산성**에 대한 높은 관심.
- **`/goal` 프롬프트 패턴**: “궁극적으로 찾던 것”“초안 후 디자인 시스템 적용” 등 **워크플로 설계**에 바로 연결하는 반응.
- **바이브코딩 구독 조합(Codex vs Antigravity vs Claude $200 등)**: 예산 **월 $100–200**, 웹 목적 등 **현실 선택** 논의.
- **저작권·캐릭터 이미지 생성(원피스 등)**: “저작권 무엇..” 농담 섞인 반응 — **법적 결론은 대화에 없음**.

---

## 자동화·시스템 적용 후보

- **이미 채팅에 풀 텍스트로 있는 `/goal` 블록**을 IDE·Cursor·Codex용 **시스템 프롬프트/커맨드 템플릿**으로 저장.
- **Notion Prompt Library** + **Threads goal 정리**를 **“목표 고정 → 자율 실행 루프”** 표준 안으로 묶는 내부 가이드.
- **공지 분리**: 운영자가 “유용한 자료는 공지에 따로”라고 함 — **링크 폭주 방지용 운영 패턴**.
- **지원사업 모니터링**: `bizinfo`, `mss` 게시판을 **주기적 스크랩 후보**로 언급됨(자동화는 대화에 **구체 스크립트 없음**).

---

## 질문·미해결

- **모두의 창업**의 **정확한 일정·자격(사업자 제한 등)** 은 채팅만으로는 불완전 — **공식 공지 확인 필요**.
- **OpenAI 소라 vs 이미지·Codex 리소스 배분**: 내부자 소식처럼 오갔으나 **출처·검증 없음**.
- **저작권(원피스 짤 생성 등)**: 실질적 가이드라인·리스크 정리는 **미해결**.
- [longblack 노트](https://longblack.co/note/1977?ticket=NT262059db4e6ae5d704716941be105b9cd3fb) 내용이 본문에 없어 **무엇에 대한 링크인지 불명**.

---

## 요약 통계

- 메시지 수: 68
- 기간: 2026-05-13 07:40 ~ 2026-05-13 23:19

### URL (36개)

- https://longblack.co/note/1977?ticket=NT262059db4e6ae5d704716941be105b9cd3fb
- https://co-trendr.notion.site/ChatGPT-Prompt-Library-150575ffadbb80e88534f047dc47552d?source=copy_link
- https://youtu.be/PplmzlgE0kg
- https://techcrunch.com/2026/05/12/the-ai-legal-services-industry-is-heating-up-anthropic-is-getting-in-on-the-action/
- https://docs.google.com/presentation/d/1YIKqtrxF_ywFNzOl0DwvhtB6SBVYLfSS95FqXi9ZbNA/edit?slide=id.p1#slide=id.p1
- https://blog.google/products-and-platforms/platforms/android/meet-googlebook/
- https://www.microsoft.com/en-us/research/wp-content/uploads/2025/12/New-Future-Of-Work-Report-2025.pdf
- https://fantastic-computing-machine.github.io/atlantis/
- https://github.com/Fantastic-Computing-Machine/atlantis
- https://www.threads.com/@unclejobs.ai/post/DX_Aj6nCTxb?xmt=AQG0PO_50lMZzjZ2YQAJjsTzJYtX55x7UQfgSjaWzhGVrA
- ... +26 more

### 해시태그

- #slide: 1

### 멘션

- @unclejobs: 3
- @all: 1
- @iniprompt: 1
- @엉클잡스: 1

---

# Fronmpt Academy — 2026-05-13

- 메시지 수: 68

**07:40** [P1]
<https://longblack.co/note/1977?ticket=NT262059db4e6ae5d704716941be105b9cd3fb>

**08:47** [P2]
@all 안녕하세요, 엉클잡스입니다.

300분이 되셨습니다!! 풀방되는 그날까지 열심히 콘텐츠와 정보로 보답드리겠습니다.

300은 스파르타죠. 스파르타 식으로 AI를 다루시길 빌며, 이전에 만들어놨던 프롬프트 라이브러리를 나눠드립니다!


<https://co-trendr.notion.site/ChatGPT-Prompt-Library-150575ffadbb80e88534f047dc47552d?source=copy_link>

**08:50** [P15]
와 벌써 300 ...

**09:04** [P1]
앤트로픽의 제품 팀이 다른 어떤 팀보다 빠르게 움직이는 비결

<https://youtu.be/PplmzlgE0kg>
Anthropic AI Legal 시장 진출

<https://techcrunch.com/2026/05/12/the-ai-legal-services-industry-is-heating-up-anthropic-is-getting-in-on-the-action/>
실밸 바이브코딩 무료 세미나

<https://docs.google.com/presentation/d/1YIKqtrxF_ywFNzOl0DwvhtB6SBVYLfSS95FqXi9ZbNA/edit?slide=id.p1#slide=id.p1>
Google GoogleBook

<https://blog.google/products-and-platforms/platforms/android/meet-googlebook/>
Microsoft NewFuture of WorkReport 2025

<https://www.microsoft.com/en-us/research/wp-content/uploads/2025/12/New-Future-Of-Work-Report-2025.pdf>

**09:17** [P4]
축하드립니다 300인

**09:26** [P1]
Beautiful Diagrams & Notes

<https://fantastic-computing-machine.github.io/atlantis/>

<https://github.com/Fantastic-Computing-Machine/atlantis>

**10:15** [P12]
감사합니다 드론님
'일'의 정의가 바뀔거같네요

**10:24** [P14]
좋은 정보 감사합니다 !

**11:08** [P2]
이거 너무 좋은데 모르고 계신분들이 많네요
클로드 디자인을 codex나 로컬에서 돌릴 수 있습니다
<https://www.threads.com/@unclejobs.ai/post/DX_Aj6nCTxb?xmt=AQG0PO_50lMZzjZ2YQAJjsTzJYtX55x7UQfgSjaWzhGVrA>
오픈 디자인ㄴ

**11:14** [P15]
우와!!!!!!!
대박.....

**11:18** [P22]
진작 알고 설치도 해놨ㄴ는데 요새 너무 쏟아져나오는게 많아서 ㅜㅜ 저같은 쪼렙은 알아도 써보지도 못하는중...ㅜㅜ 엉클님 포스트 보고 얼른 도전해봐야겠어요!!!!


진짜 요새 시간이 너무 없네요.....본업하면서 하려니 ㅋ

**11:19** [P15]
엉클잡스님 포스트 제가 다 보고있다고 생각했는데 이건 놓쳤었네요 ㅋ 오늘 바로 테스트 해봐야겠어요!

**11:20** [P2]
코덱스 커서 등 다 됩니다 ㅋㅋ

**11:31** [P15]
아!!
<https://github.com/nexu-io/open-design/releases>
에서 운영체제에 맞게 프로그램을 다운로드 해서 실행하면 로컬에 이미 설치된 클로드 코드 혹은 코덱스 등등 다 바로 선택해서 작업이 가능하군요!

**11:36** [P17]
혹시 모두의 창업 지원하신 분들 있을까요?!
신청은 금요일까지더라구요

**11:40** [P15]
금요일 이군요!!!

**11:40** [P12]
저요
지원합니다
금요일에 올릴 예정입니다

**11:41** [P28]
사업자 있으면 안되서 전 포기요 ㅎㅎ

**11:41** [P15]
아 안되군요 ㅠㅠ

**11:44** [P12]
사람들의 창의력이 대단

**11:45** [P20]
이런 정보는 어디서 얻으면 좋을까요? 모두의 창업 사업자 없는 사람이 해보기 너무 좋아보이네요. 이틀남았다니 ㅠㅠ

**11:48** [P12]
아이디어만 있으면 되서 어렵지 않습니다
<https://www.bizinfo.go.kr/sii/siia/selectSIIA200View.do?hashCode=01>
이런 곳에서 얻어요
<https://www.mss.go.kr/site/smba/ex/bbs/View.do?cbIdx=310&bcIdx=1064197&parentSeq=1064197>

**12:32** [P1]
저도 Open Design 사용한지 2주 정도 된 것 같아요. 물건이죠 ㅋㅋㅋㅋ 
Google Omni

<https://x.com/vision_ia/status/2054203113226059873>

**12:36** [P2]
앞으로 유용한 자료는 공지에 따로 띄워보겠습니다 ㅎㅎ

**12:37** [P1]
클로드를 문서 작성 도구로 바꿔주는 프롬프트

<https://www.reddit.com/r/PromptEngineering/comments/1spmwkg/i_didnt_realise_claude_could_build_actual_word/>
Django 웹 개발 (무료)

<https://www.udemy.com/course/django-ninja/?couponCode=2514B8446739AB0585A9>

**12:43** [P1]
Excalidraw API & MCP Beta 공개

<https://plus.excalidraw.com/docs>

**13:00** [P12]
<https://www.hankyung.com/article/2026051322997>

**13:15** [P1]
Uncensored Video Generation Model | Sulphur 2

<https://huggingface.co/SulphurAI/Sulphur-2-base>


**13:20** [P1]
Claude for Legal

<https://github.com/anthropics/claude-for-legal>

**13:28** [P1]
Salesforce Agentforce World Tour Korea 사전 신청 (무료)

- 일시 : 06/10(수) 9시~17시

- 장소 : COEX 컨벤션 센터

- 참석 : 오프라인(승인에 한함) 또는 온라인

- 링크 : <https://www.salesforce.com/kr/events/world-tour/korea/register/>


**13:34** [P11]
어떤점이 좋으셨을까요???

**13:34** [P2]
<https://www.threads.com/@unclejobs.ai/post/DYQ_uzCiXar?xmt=AQG0vKu1DZLmFCwr8ZWX-98gw2JO2QOw1jtI0AcXMD6M1A>
/goal [최종 결과 - "완료"의 모습을 한 줄로]

── 컨텍스트 ──
· 프로젝트: [무엇을 만드는지]
· 스택: [언어, 프레임워크, 인프라]
· 현재 상태: [지금 존재하는 것]
· 작업 디렉토리: [경로 or 레포]
· 제약: [예산, 시간, 건드리면 안 되는 것]
· 청중: [누구를 위한 것]

── 성공 기준 (모두 참이어야 함) ──
1. [구체적이고 측정 가능한 결과]
2. [구체적이고 측정 가능한 결과]
3. [구체적이고 측정 가능한 결과]
4. 최종 결과물이 에러 없이 실행됨
5. 증거 제시 가능 (스크린샷, 테스트 출력, URL)

── 운영 규칙 (협상 불가) ──
1. 계획 먼저. 코드 작성 전에 번호 매긴 태스크 리스트 출력.
2. 자율 실행. 진짜 막힌 게 아니면 질문 금지.
3. 자가 검증. 매 단계 후 테스트 실행, 출력 확인, 작동 확인.
4. 직접 디버깅. 실패하면 진단하고 수정. 넘기지 마라.
5. 모든 도구 사용. MCP, 터미널, 웹, 코드 실행, 실제 데이터.
6. 플레이스홀더 금지. TODO 금지, 스텁 금지, 진짜 컴포넌트와 진짜 상태.
7. 진행 로그. 완료, 진행 중, 결정, 블로커 추적.
8. 목표 유지. 스펙에서 벗어난 발견은 메모만 하고 계속.
9. 막혔으면. 벽을 기록하고 병렬 가능한 모든 것은 계속.
10. 멈추기 전 성공 확인. 기준 다시 읽고 각 항목 충족 확인.

── 품질 기준 ──
· 코드: 깔끔, 타입 명시, 프로젝트 컨벤션 준수
· 디자인: 자금 잘 받은 스타트업이 만든 것처럼
· 출력: 시니어 코드 리뷰 통과 가능
· 문서: 새 패턴, 환경 변수, 결정 모두 기록

── 최종 결과물 ──
✅ 각 기준 충족 확인
📁 생성/수정된 모든 파일
🚀 실행/테스트/배포 방법
📊 증거 (스크린샷, 테스트 출력, URL)
📓 결정사항과 알아두어야 할 것
⚠️ 알려진 한계와 후속 작업

계획을 먼저 출력하라. 그 다음 끝까지 또는 진짜 막힐 때까지 체크인 없이 실행하라.
goal이 화제죠. 정리해보았습니다. 그리고 프롬프트도 전달 드립니다

**13:35** [P12]
감사합니다
Danke !

**14:20** [P15]
@엉클잡스 대박입니다. 진짜 제가 궁극적으로 찾던거였어요 ㅋㅋ
이걸로 초안 만들고 바로 디자인 시스템 적용하면될것같네요

**14:22** [P2]
ㅋㅋㅋㅋ 진짜 클로드 디자인 토큰 걱정 없어도 되고 짱입니다
이걸로 또 유튜브 콘테츠 잡아야겟네여
블로그 후속타 ㅋㅋ

**14:35** [P28]
자료링크가 너무 많아서 그런데 이 자료가 어떤 거였는지 알려주시면 감사하겠습니다

**14:36** [P2]
<https://www.threads.com/@unclejobs.ai/post/DX_Aj6nCTxb?xmt=AQG0PO_50lMZzjZ2YQAJjsTzJYtX55x7UQfgSjaWzhGVrA>
이거 보시면 됩니다

**14:36** [P15]
네네!
맞습니다. 
open-design 사용방법이 다양한데  어떤 깃허브 문서든 가장 빨리 적용해보고자한다면
퀵스타트!! 이부분만 먼저 보시면됩니다!
기억해주세요! 퀵스타뜨!!ㅋ

**14:39** [P1]
Open 시리즈 ㅋㅋㅋㅋ

**14:47** [P1]
RAG is Dead

<https://youtu.be/RvkbPPZbPgc?si=HziPA2bApp605UOx>
Computer Use in Codex

<https://youtu.be/D_FCYsshMI4?si=iNi565I-7bsOaizu>
Google Broke reCAPTCHA

<https://reclaimthenet.org/google-broke-recaptcha-for-de-googled-android-users>

<https://news.hada.io/topic?id=29306>
GitHub is Sinking

<https://dbushell.com/2026/04/29/github-is-sinking/>

**14:54** [P1]
Markdown by 김진중

<https://playloom.app/mark>

**14:59** [P2]
프롬프트 엔지니어링 책 내신 골빈해커님 회사네요 여기
마크다운 포맷 기반 뷰어 툴 임니다 

**15:04** [P1]
OpenAI, OpenAI Deployment Company 출범에 관한 설명 (황민호)

<https://www.facebook.com/share/1CSq6ZxPeB/>
특정 채팅방에 한두번 정보 주었더니 이런 결과가 ㅋㅋㅋㅋ

**15:09** [P33]
ㅋㅋㅋㅋ

**15:09** [P34]
항상 감사함미다… 드론님..

**15:14** [P2]
ㅋㅋ 돌고도는구만요 다들 

**15:14** [P15]
온라인바닥?도 생각보다 좁죠 ㅋ

**15:16** [P6]
다 보이시는 분들만 보이는거 아닌가요 ㅋㅋㅋ

**15:17** [P33]
진짜 이제 엑셀 팡션이 아니라 
AI 팡션
시대네요

**16:16** [P2]
<https://www.threads.com/@iniprompt/post/DYMkcU8iSfy?xmt=AQG0cfmlmwP5ZVTeiEloYmlMiDPSoxuZqqOjjb6TI4W8Bg>
지피티 2.0으로 원피스 짤 생성 프롬프트 전달 드립니다 
저작권 무엇.. 

**16:19** [P12]
놀랍군요

**16:23** [P2]
소라에 쓰이는 지피유가 엄청나다고 하더라구요 관계자들이
그래서 소라접고 지금은 지피티 이미지랑 코덱스 쪽에 자원 투입하는듯 합니다 

**17:09** [P26]
소라는 베이스 모델도 아예 달랐던듯요?
이미지 같은 경우는 모델로 추론 돌리고 넘겨주면 이미지 생성만 해줄 거 같은데

기존 컴퓨팅 활용이 안 되고 자체적으로 잡아먹는게 많았던 것 같아요

어디서 들은건지는 기억이 안 나지만..ㅎㅎ

**17:09** [P18]
소라가 영상 생성 비용이 많이 들긴 하더라구요....ㅎㅎ

**17:17** [P2]
자체적으로도 소라 잘 접었다고 평할거에요 ㅋㅋㅋㅋㅋ

**17:18** [P15]
제 아내가 소라인데 실제로도 신경이 많이쓰입니다 ㅋㅋ
죄송합니다 
ㅋㅋ

**17:36** [P1]
저작권? X나 줘버려 (요즈음 OpenAI 신경 안쓰는 듯) ㅋㅋㅋㅋ

**17:51** [P1]
Obsidian Skills 공식

<https://github.com/kepano/obsidian-skills>

**18:01** [P21]
이번달에 ai 구독하려는데 바이브코딩하려면 코덱스랑 클로드 안티그래비티 중 어떤 게 나을까요..? 웹사이트 구축이 주 목적이고 한달 100~200달러 생각중입니다..
코덱스면 100달러 짜리 생각중입니다 개인용이라..

**18:03** [P27]
아에 첨이시면 안티 무료로 시작해보심이

**18:04** [P35]
20~30달러짜리 쓰시다 모자라면 결제하시는것도 추천드립니다. 클로드, 코덱스 비교해보셔요. 사람마다 맞는게 달라서;

**18:04** [P18]
초보자가 하시기에는 코덱스 앱이 편하긴 할 것 같아요

**18:06** [P21]
현재 클로드 200달러짜리로 하고 있는데 요새 코덱스가 좋다는 이야기가 많아서 고민입니다
클로드는 공용이라 개인용으로 하나 더 쓰려고해요!

**18:28** [P1]
Pollo AI가 요즈음 자주 보이네요~~

<https://youtu.be/m8v9bGQ5bts?si=JOlRgLGYtJSaphNL>
<https://www.kmjournal.net/news/articleView.html?idxno=11241>

**18:32** [P12]
저는 요즘 독립운동가와 그 시대 시인들에게 필이 꽂혀서 상황을 가정하여 막 이미지 만들어보고 있습니다. 근데 사진처럼은 잘 안나오더라구요
윤봉길 의사하고 임화 시인 사진인데
윤봉길 의사는 얼추 잘나오는데 임화 시인이 잘 안나옴..

**18:41** [P1]
일잘러와 좋은 팀장은 동의어가 아니다

<https://bemyb.kr/contents/?bmode=view&idx=171229724>

**19:54** [P1]
<https://community.rememberapp.co.kr/post/198367>

**23:19** [P1]
Claude Platform on AWS

<https://claude.com/blog/claude-platform-on-aws>

