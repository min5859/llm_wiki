---
title: "뉴스레터 research 파이프라인의 anti-bot 차단 — 1급 실패 모드로 설계되지 않은 소스 접근 장애"
domain: "ai-agent"
sensitivity: "internal"
tags: ["dev-blog", "anubis", "cloudflare", "anti-bot", "research-dossier", "pipeline-failure", "newsletter"]
created: "2026-07-24"
updated: "2026-07-31"
sources:
  - "session-logs/20260731-030015-6e61-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260731-040747-0836-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260731-042126-87b6-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260731-045010-fdc4-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260729-030012-1ece-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260729-044041-3f8a-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260729-045245-d39c-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260729-035106-b662-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260729-040000-feda-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260729-041304-d7dd-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260729-042651-425d-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260728-040459-14e9-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260727-213100-094c-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260727-041837-175a-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260725-034709-524e-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260725-035718-34ba-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260725-042607-586c-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260724-030015-a3a0-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260724-040043-5ae1-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260724-033656-7388-#-AI-Coding-Agents-Research-Dossier-당신은-AI-코딩-에이전트.md"
  - "session-logs/20260703-030007-8175-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
  - "session-logs/20260726-040855-2b13-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260726-042144-44bc-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260726-042949-7f1b-#-Linux-Kernel-Lens-Research-Dossier-당신은-특정-커널-서브시.md"
  - "session-logs/20260726-030018-b165-#-Linux-Daily-Research-Dossier-당신은-리눅스-커널-개발-뉴스레터의.md"
confidence: "high"
related:
  - "wiki/analyses/research-write-agent-separation.md"
  - "wiki/projects/dev-blog.md"
---

# 뉴스레터 research 파이프라인의 anti-bot 차단 — 1급 실패 모드로 설계되지 않은 소스 접근 장애

dev-blog 뉴스레터 파이프라인의 research(dossier) 단계가 외부 소스의 anti-bot 방어에 막히는 사건이 2026-07-03 을 시작으로 2026-07-24 이후에는 매일 관측되고 있다 (07-31 현재 아홉 차례 — 산발 사건이 아니라 **상시 운영 조건**이다). 개별 URL 차단은 confidence 강등·openQuestions 격리로 흡수되지만, **소스가 전면 차단된 렌즈는 dossier 자체가 산출되지 않아 해당 회차 newsletter 가 조용히 결손**된다. 파이프라인 레벨의 자동 폴백·결손 감지 장치는 없다 — 단, 2026-07-25 에 차단으로 실패한 렌즈가 10분 뒤 재발사되어 성공한 사례가 관측됐고, 2026-07-26 에는 실측된 우회 채널이 다채널로 확장돼 그날 research·write 전건이 산출 성공했다 (아래 07-25·07-26 절 참조).

## 증상

2026-07-24 03:00~04:33 cron 사이클에서 세 가지 독립된 차단 사건이 관측됨:

1. **Linux Daily dossier** (`030015`) — `kernel.org`/`lore.kernel.org` 전체가 Anubis 봇 챌린지로 WebFetch 실패. 에이전트가 스스로 "All the kernel.org and lore.kernel.org URLs are blocked by Anubis (bot protection) — WebFetch can't read them directly. Let me try WebSearch to corroborate" 로 우회 시도. 일부 항목은 confidence 강등·openQuestions 증가로 산출은 완료.
2. **Linux Kernel Lens dossier — `lore-stable-new` 렌즈** (`040043`) — `lore.kernel.org/stable/*` 접근이 전부 `403 Forbidden`. 응답 본문에 `"Making sure you're not a bot!"` (Anubis 챌린지 페이지) 확인. `curl/8.4.0`·`Mozilla/5.0`·`git/2.43.0`·`Wget/1.21`·`Googlebot/2.1` 등 여러 User-Agent 로 재시도했으나 전부 403 — **UA 스푸핑이 무효화됨**. 세션은 `assistant_turns: 0` 으로 종료 — dossier JSON 을 끝내 산출하지 못함.
3. **AI Coding Agents dossier** (`033656`) — `x.com` 트윗 URL이 `HTTP 402 Payment Required` 로 차단. 서브에이전트가 "The x.com tweet (candidate 2) was unfetchable (HTTP 402), so I corroborated it via BleepingComputer and other real coverage" 로 대응, openQuestions 에 "primary source unfetchable" 을 명시.

동일한 Anubis 차단은 2026-07-03 (`session-logs/20260703-030007-8175-*`) 에도 발생한 바 있어, 이번이 두 번째 등장이다.

## 차단 유형 3종

| 유형 | 소스 | 관측 응답 | 대응 |
|---|---|---|---|
| Anubis PoW/챌린지 | kernel.org, lore.kernel.org | 챌린지 페이지 반환 (WebFetch 실패) | WebSearch 2차 corroborate |
| 403 + Anubis 챌린지 (v1.25.0, UA 스푸핑 무효) | lore.kernel.org/stable/* | `403 Forbidden` + "Making sure you're not a bot!" (UA 무관하게 전부) | 없음 — 렌즈 결손 |
| HTTP 402 | x.com | `402 Payment Required` | BleepingComputer 등 2차 소스 corroborate |

## 실패 전파 경로 — 렌즈 결손은 조용히 일어난다

`lore-stable-new` 렌즈 사건이 핵심 신호다: 개별 URL 차단 대응(WebSearch corroborate)이 통하지 않을 만큼 소스가 **전면** 차단되면, research 에이전트는 dossier JSON 자체를 산출하지 못하고 `assistant_turns: 0` 으로 세션이 끝난다. 이 결손은:

- write 단계로 전파되지 않는다 (해당 렌즈의 newsletter 가 애초에 생성되지 않음)
- 상위 파이프라인(cron/daily-deploy)이 감지하지 않는다 — 재시도도, 알림도 없다
- Kernel Lens 는 이날 렌즈 6개 중 dossier 6회는 발사됐으나 newsletter 는 5회만 생성됨 (1개 렌즈 결손, 040043 실패분)

## 관찰된 대응 계층

1. **WebSearch / 2차 소스 corroborate + confidence 강등** — 1차 소스가 막혀도 후속 검색으로 사실을 재구성하고 신뢰도를 낮춰 반영 (Linux Daily, AI Coding Agents 사례)
2. **openQuestions 에 미확인 명시** — "primary source unfetchable" 등으로 grounding 계약상 추측·단정을 금지
3. **전부 실패 시 해당 회차 결손** — 위 두 계층이 통하지 않으면(전면 차단) dossier 자체가 안 나오고, 상위 파이프라인은 이를 감지·재시도하지 않는다

## UA 스푸핑은 효과 없었다 (운영 기록)

`lore-stable-new` 렌즈 사건에서 `curl -A "curl/8.4.0"`, `-A "Mozilla/5.0"`, `-A "git/2.43.0"`, `-A "Wget/1.21"`, `-A "Googlebot/2.1"` 등 5종 이상의 User-Agent 로 재시도했으나 전부 `403 Forbidden` 이었다. [[research-write-agent-separation]] 이 기록한 2026-06-18 의 "`curl -A "git/2.39.0"` 비-브라우저 UA 우회 성공" 이 이 소스·이 날짜에는 재현되지 않음 — 봇 차단 방어가 강화되거나 대상 경로(`/stable/*`)가 다른 정책을 적용하는 것으로 보인다 (원인 미확정).

> 교훈: 웹 리서치형 에이전트 파이프라인은 소스 차단을 **1급 실패 모드**로 설계에 반영해야 한다. 렌즈별 결손 감지, 대체 소스(미러·2차 아카이브), 재시도 정책이 없으면 파이프라인은 "정상 완료"처럼 보이는 로그를 남긴 채 조용히 결손된다. UA 우회 같은 임기응변은 방어 강화 시 무효화될 수 있는 비신뢰 폴백이다.

> 2026-07-27 정밀화 — 무효는 브라우저형 UA 에 한함, `git/2.43` 은 통과 (아래 5번째 관측 참조).
> 2026-07-28 재정밀화 — 차단은 브라우저형/도구형의 고정 이분법이 아니라 UA 문자열별 블록리스트에 가깝고 시점에 따라 변동한다(`Wget/1.21` 이 07-24엔 403, 07-28엔 200). 아래 6번째 관측 참조.

## 2026-07-25 후속 관측 — 우회 성공 2건 + 차단 렌즈 재발사 성공

같은 cron 사이클(03:00~04:32)에서 세 번째 관측. 차단은 지속됐지만 이번에는 **통한 대응**이 두 가지 기록됐다:

1. **mbox.gz raw 엔드포인트 우회 성공** (`042607`, dri-devel 렌즈) — `lore.kernel.org/dri-devel/` 이 `403 Forbidden` 을 반환했으나 mbox.gz 대체 엔드포인트로 우회 다운로드에 성공해 dossier 를 정상 산출. 7/24 에 UA 스푸핑 5종이 전멸했던 것과 달리, raw mbox 엔드포인트는 이날 열려 있었다 — [[research-write-agent-separation]] 폴백 사다리의 "raw 엔드포인트" 계층이 실효한 사례 (단 7/7 에는 raw mbox 까지 차단된 날도 있어 신뢰 가능한 상수는 아님).
2. **차단 렌즈의 10분 후 재발사 성공** (`034709` → `035718`, linux-kernel-security 렌즈) — 03:47 dossier 가 Anubis 챌린지("Making sure you're not a bot!")에 막혀 실패한 뒤, 03:57 동일 렌즈 dossier 세션이 다시 발사되어 정상 산출. 7/24 의 "자동 재시도 없음" 관측과 배치되는 움직임 — 파이프라인에 재시도 장치가 추가된 것인지 스케줄상 재발사인지는 세션 로그만으로 미확정 (dev-blog 저장소 쪽 확인 필요).

그 외: Linux Daily dossier(`030015`)는 kernel.org·lore.kernel.org Anubis 차단에 `cdn.kernel.org` 폴백을 시도했고, 다른 렌즈(`041951`)는 "lore 스레드 원문이 Anubis 봇 차단으로 독립 재확인 불가"를 openQuestions 로 격리 — 기존 대응 계층(2차 corroborate·openQuestions 격리)은 계속 작동.

## 2026-07-26 — 4번째 관측: 차단 지속 + 우회 채널 실측 확장

07-26 새벽 cron 로그 7건에서 `lore.kernel.org` 403/Anubis 차단이 다시 관측됨 — 07-24·07-25 에 이은 3일 연속 관측으로 상시 운영 조건이라는 판단을 강화한다. 이번엔 우회에 실제로 성공한 채널이 다양하게 실측됐다:

- **patchwork.kernel.org API** — 로그 6건에서 사용
- **infradead.org 메일 아카이브** — 4건
- **yhbt.net 미러** — 2건
- **NNTP 직접 접근** — 1건 (`042949` GPU/DRM 렌즈)
- **git ls-remote / shallow clone** 병행

결과: Research 5회·Write 4회 전부 dossier/뉴스레터 산출 성공 — 다채널 폴백이 이날은 전면 결손을 막았다. lore.kernel.org 단일 소스에 의존하지 않고 미러·API·메일아카이브·NNTP 를 다중 경로로 병행하는 것이 anti-bot 차단 상시화 국면에서 실효 있는 완화책임을 실측으로 뒷받침한다.

## 2026-07-27 — 5번째 관측: 차단은 2층 구조, git 클라이언트 UA 는 양층을 모두 통과

linux-perf-rt 렌즈 dossier 세션(04:19, 출처 `session-logs/20260727-041837-175a-*`)에서 `lore.kernel.org` `/bpf/<msgid>/raw` 접근을 User-Agent 3종으로 실측:

```
$ curl -sS -m 25 -A "curl/8.7.1" -o /tmp/lore1.txt -w "HTTP:%{http_code} SIZE:%{size_download}\n" \
    "https://lore.kernel.org/bpf/20260726070122.2407344-1-zirajs7@gmail.com/raw"
HTTP:403 SIZE:146
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

```
== UA: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0 Safari/537.36
HTTP:200 SIZE:4473
<!doctype html><html lang="en"><head><title>Making sure you&#39;re not a bot!</title><link rel="stylesheet" href="/.within.website/x/xess/xess.min.css?cachebuster=1.25.0">...
== UA: git/2.43
HTTP:200 SIZE:7058
From mboxrd@z Thu Jan  1 00:00:00 1970
Received: from mail-pl1-f181.google.com (mail-pl1-f181.google.com [209.85.214.181])
	(using TLSv1.2 with cipher ECDHE-RSA-AES128-GCM-SHA256 (128/128 bits))
	(No ...
```

- `curl/8.7.1`(기본 UA) → `HTTP 403`, 146바이트 순수 nginx 에러 페이지. Anubis 챌린지가 아니라 **nginx 레벨 UA 필터**.
- 브라우저 UA(`Mozilla/5.0 … Chrome/126`) → `HTTP 200` 이지만 본문은 Anubis 챌린지 HTML("Making sure you're not a bot!", cachebuster=1.25.0, 4473바이트).
- `git/2.43` UA → `HTTP 200` + **정상 raw mbox 수신**(`From mboxrd@z …`, 7058바이트).

함의:

1. 차단이 **2층 구조**임이 확인됨 — nginx 레벨에서 curl 류 UA 를 bare 403 으로 거부하고, 그 아래(통과 시)에는 Anubis 가 브라우저형 UA 에 챌린지를 제시.
2. 07-24 관측의 "UA 5종 스푸핑 전부 무효"는 **브라우저형 UA 에 한한 이야기**였다 — 비브라우저 도구 UA(`git/2.43`)는 양층을 모두 통과하는 유효한 우회 채널로 실측됨(Anubis 가 기본적으로 "Mozilla" 포함 UA 만 챌린지 대상으로 삼는 동작과 부합하는 것으로 **추정** — 원인 확정은 아님).
3. 우회 채널 목록에 **"git UA 로 lore raw(mbox) 직접 수신"** 추가.

## 2026-07-28 — 6번째 관측: 차단 지속 + UA 스윕 정밀화 + 폴백 채널의 신선도 한계

07-27 저녁 Kernel Lens dossier 세션(21:31, 출처 `session-logs/20260727-213100-094c-*`)에서 WebFetch 는 여전히 `lore.kernel.org` 전 경로에서 Anubis 로 막혔다:

```
I could not directly read any of the three lore.kernel.org pages: the entire host is behind
Anubis anti-bot protection, which returns "Access Denied" (error 9e4edb5b6b850c41) to WebFetch
on every URL variant tried (canonical `/` and raw `/raw`).
```

같은 세션에서 lore 후보를 검증하던 병렬 서브에이전트 두 그룹의 결과가 갈렸다: patchwork.kernel.org 로 메시지 ID·Fixes 태그까지 교차확인된 그룹은 `confidence: high`, 반면 발신 1일 이내(07-26자) 메시지라 patchwork·lkml.iu.edu·marc·spinics 그 어디에도 아직 색인되지 않은 그룹은 `medium/low` + `openQuestions` 로 강등됐다. 즉 [[research-write-agent-separation]] 이 기록한 폴백 사다리(raw 엔드포인트 → 미러 → commitMessage+WebSearch 교차검증)는 **lore 메시지가 색인될 시간(수일)이 지나야 유효해지는 신선도 한계**를 갖는다 — 발신 직후 시간창에서는 미러 폴백 자체가 무력하고, git/Wget 등 도구형 UA 로 lore 를 직접 수신하는 경로만이 실측 가능하다.

07-28 새벽 dossier 세션(04:07, 출처 `session-logs/20260728-040459-14e9-*`)이 `lore.kernel.org/linux-kbuild/.../raw`(90,051바이트 mbox) 대상으로 UA 5종을 스윕했다:

```
UA[git/2.45.0]          -> HTTP 200 size=90051  From mboxrd@z Thu Jan  1 00:00:00 1970...
UA[Wget/1.21]           -> HTTP 200 size=90051  (git 과 동일 본문)
UA[public-inbox-mirror] -> HTTP 200 size=90051  (git 과 동일 본문)
UA[python-requests/2.31]-> HTTP 403 size=146   <html><head><title>403 Forbidden</title>...
UA[] (curl 기본)        -> HTTP 403 size=146   <html><head><title>403 Forbidden</title>...
```

함의:

1. `git` UA 채널은 07-27 관측(`git/2.43`)에 이어 `git/2.45.0` 에서도 유효 — 버전 무관하게 통과.
2. **`Wget/1.21` 이 07-24 관측에서는 403 이었으나 이번엔 200 으로 통과** — 같은 UA 문자열의 판정이 시점(또는 대상 경로)에 따라 바뀐다. 07-27 의 "무효는 브라우저형 UA 에 한함" 정밀화를 한 단계 더 정밀화해야 한다: nginx 레벨 필터는 브라우저형/도구형의 고정 이분법이 아니라 **알려진 스크레이퍼 UA(curl 기본·python-requests)를 겨냥한 블록리스트에 가깝고, 통과 여부가 UA 문자열별·시점별로 변동**한다. 도구형이라도 `python-requests` 는 여전히 차단되므로 "도구형 UA 는 통과"라는 단순화도 정확하지 않다.
3. `public-inbox-mirror` 라는 자기서술적 UA 문자열도 통과 — 필터가 특정 알려진 문자열을 화이트리스트/블랙리스트하는 방식에 가까움을 시사(원인 확정은 아님).
4. 릴리스류 검증(kernel.org `releases.json` + CDN ChangeLog)은 이번에도 lore 없이 가능함이 재확인됐다(094c 의 릴리스 후보 4건 전부 이 경로로 검증, 커밋 수 정확히 일치).

## 2026-07-29 — 7번째 관측: 브라우저 UA 의 500 변형 + NNTP 프로토콜 확정 + 다채널 폴백 지속

07-29 새벽 cron Kernel Lens research 6렌즈(03:51~05:00)와 Linux Daily dossier(1ece) 전체에서 `lore.kernel.org` 차단이 다시 관측됨 — 07-24 이래 6일 연속 관측으로 상시 운영 조건 판단을 재확인한다.

1. **UA 스윕 재확인** (`d39c`, GPU/DRM 렌즈) — 07-28 스윕과 동일한 목록으로 재실측: `curl/8.7.1`·`python-requests/2.32` 기본 UA → `403`, `git/2.43.0`·`Wget/1.21` → `200`. 판정이 07-28 과 정확히 일치.
   ```
   for ua in "curl/8.7.1" "python-requests/2.32" "git/2.43.0" "" "Wget/1.21"; do ...
   UA='Wget/1.21' -> 200
   ```
2. **브라우저 UA 의 신규 변형 — `500 Server Error`** (`3f8a`, arch/전력관리 렌즈) — `curl` 기본 UA → `403 Forbidden`(nginx, 146바이트)까지는 기존과 동일하나, 이번엔 `Mozilla/5.0 (Macintosh…) Chrome/126.0` 브라우저 UA 가 07-27·07-28 이 관측한 "`200` + Anubis 챌린지 HTML" 이 아니라 **`500`** 을 반환했다(타이틀 `Oh noes!`, `cachebuster=1.25.0` — 같은 Anubis 프런트지만 응답 코드 자체가 달라진 첫 관측). 이후 프로젝트 자체 수집기 UA(`dev-blog-collector/0.1 (+…)`)로 재시도해 `200` 을 얻었고, `lists.infradead.org` 메일링리스트 아카이브 경로(같은 세션에서 28회 참조)를 병행해 나머지 후보를 확보했다. 수신한 raw mbox 하나는 `Content-Transfer-Encoding: base64` 라 본문이 그대로 읽히지 않아, python `base64.b64decode()` 로 직접 디코딩해서 본문을 복원했다.
3. **yhbt.net 미러 폴백 지속** (`b662`, 커널 보안 렌즈) — `yhbt.net/lore/all/<message-id>/raw` 및 `t.mbox.gz` + `gunzip` 조합으로 6회 성공 조회 — 07-26 4번째 관측 이래의 상시 폴백 채널로 재확인.
4. **patchwork.kernel.org API 병행** (`425d`, perf-rt 렌즈) — 21회 조회로 리뷰 상태(Reviewed-by / changes-requested / pw-bot 태그)를 교차검증하며 lore 원문 없이 dossier(6 entries)를 완성.
5. **NNTP 직접 접근 — 프로토콜 상세 최초 확인** (`1ece`, Linux Daily) — python `nntplib` 스크립트로 `nntp.lore.kernel.org` 에 접속해 `GREET: 201 nntp.lore.kernel.org ready - post via email` 를 받고, `ARTICLE <Message-ID>` 요청에 `220 … article retrieved - head and body follow` 로 헤더+본문을 통째로 수신(`HEAD`/`BODY` 개별 커맨드도 각각 `221`/`222` 로 성공). 07-26 4번째 관측이 "NNTP 직접 접근 1건"으로만 기록했던 채널이 이번에 처음으로 실제 명령·응답 코드 수준까지 재실측·확정됐다.
6. **릴리스 검증 경로 지속** — `kernel.org/releases.json` + `cdn.kernel.org` ChangeLog 조합이 이번에도(1ece, d7dd 양쪽) lore 없이 릴리스 정보를 200 으로 검증.

결과: 6렌즈 research 전부와 Linux Daily 가 실제로 발사돼 위 채널들로 조사를 진행했고, anti-bot 차단으로 인한 완전 결손(dossier 자체가 아예 없음)은 관측되지 않았다 — 다만 `b662`(보안)·`d7dd`(stable)·`3f8a`(arch/전력관리)·`d39c`(GPU) 4개 세션은 로그 자체에 최종 dossier JSON 이 남지 않았다(Assistant 텍스트 턴도 `Write` 파일 콜도 기록 없이 bash 탐색만 존재 — `feda`(툴체인)만 `/tmp/lore/dossier.json` 에 명시적 `Write` + 파이썬 스키마 검증(`valid True`, entries=6)이 로그에 남았고, `425d`(perf-rt)는 Assistant 턴에 dossier JSON 전문을 직접 출력했다). 이 로그 상 최종 산출 소실 현상과 그 여파는 [[dev-blog]] 07-29 운영 노트로 분리.

## 2026-07-31 — 9번째 관측: mutt 메일 클라이언트 UA 통과 + 브라우저 UA 응답의 챌린지 형태 복귀

(8번째 관측(07-30)은 07-28 판정과 완전 일치해 본문 무변경 — [[dev-blog]] 07-30 운영 관찰 참조. `public-inbox-fetch` UA 통과 포함.)

07-31 새벽 cron 에서 차단 지속 — Linux Daily(`6e61`)와 Kernel Lens 렌즈 3건(`0836`·`87b6` bare 403, `fdc4` Anubis 챌린지)에서 재관측, 07-24 이래 8일 연속. Linux Daily dossier(03:01)가 `lore.kernel.org/lkml/<msgid>/raw` 대상으로 UA 4종을 스윕했다:

```
UA=[Mozilla/5.0 (… Chrome/140.0 …)] code=200 size=4473   ← Anubis 챌린지 HTML
UA=[git/2.53.0]                     code=200 size=7275   ← 정상 raw mbox
UA=[mutt/2.2]                       code=200 size=7275   ← 정상 raw mbox (신규)
UA=[] (curl 기본)                    code=403 size=146    ← nginx bare 403
```

함의:

1. **`mutt/2.2` 가 통과 UA 목록에 신규 추가** — git·Wget·public-inbox-mirror·public-inbox-fetch 에 이어 메일 클라이언트 UA 도 통과. "알려진 스크레이퍼 UA 블록리스트 + 나머지 통과" 판정(07-28)과 부합.
2. git UA 는 `2.43`→`2.45.0`→`2.53.0` 세 버전째 통과 — 버전 무관 지속 재확인.
3. **브라우저 Mozilla UA 응답이 `200` + Anubis 챌린지 HTML(4473바이트) 형태로 복귀** — 07-29 에 관측된 `500 "Oh noes!"` 변형은 일시적이었다. UA 별 통과 판정뿐 아니라 챌린지 응답의 코드·형태 자체도 시점별로 오간다.
4. `git.kernel.org` cgit 태그 페이지는 기본 curl UA 로도 `200` — UA 필터는 lore.kernel.org 에 국한됨을 같은 사이클에서 실측. GPU/DRM 렌즈(`fdc4`)는 Anubis PoW 를 풀지 않고 "우회하지 않고 대체 공개 아카이브로 확인" 방침으로 raw 엔드포인트·대체 아카이브를 병행해 조사를 진행했다.

## 관련 맥락

- [[research-write-agent-separation]] — 봇 차단 폴백 사다리(raw 엔드포인트 → 미러 → commitMessage+WebSearch 교차검증 → confidence 강등+openQuestions 격리)의 누적 기록. 이번 사건은 그 사다리가 끝까지 실패했을 때(전면 차단) 무슨 일이 일어나는지를 보여준다.
- [[dev-blog]] — 이 파이프라인이 구현된 프로젝트, 2026-07-24 운영 노트

## 변경 이력

- 2026-07-24: 최초 작성 (2026-07-03·07-24 dev-blog cron 로그에서 승격)
- 2026-07-25: 3번째 관측 반영 — mbox.gz raw 엔드포인트 우회 성공, 차단 렌즈 10분 후 재발사 성공(재시도 장치 여부 미확정), cdn.kernel.org 폴백 시도. "자동 재시도 없음" 단정 완화
- 2026-07-26: 4번째 관측 추가 — lore.kernel.org 403/Anubis 차단 지속(새벽 cron 로그 7건). 실측 성공 우회 채널 확장 기록: patchwork.kernel.org API·infradead.org 메일 아카이브·yhbt.net 미러·NNTP 직접 접근·git ls-remote/shallow clone 병행. Research 5회·Write 4회 전부 산출 성공 — 다채널 폴백의 실효 실증 (출처: session-logs/20260726-040855-2b13-*, -042144-44bc-*, -042949-7f1b-*, -030018-b165-*)
- 2026-07-27: 5번째 관측 — 2층 차단 구조 확인(nginx 레벨 UA 필터 bare 403 → 통과 시 Anubis 챌린지) + `git/2.43` UA 가 양층을 모두 통과해 raw mbox 정상 수신을 실측. "UA 스푸핑 무효" 단정을 브라우저형 UA 로 한정, 우회 채널에 "git UA 로 lore raw 직접 수신" 추가 (출처: session-logs/20260727-041837-175a-*)
- 2026-07-28: 6번째 관측 — 차단 지속(094c, Anubis Access Denied 전 경로) + UA 5종 스윕 실측(14e9, git/2.45.0·Wget/1.21·public-inbox-mirror 통과, python-requests·curl 기본 403). `Wget/1.21` 이 07-24엔 403·07-28엔 200 으로 판정이 시점에 따라 바뀜을 확인해 "브라우저형 UA 만 차단" 정밀화를 "알려진 스크레이퍼 UA 블록리스트 + 시점별 변동"으로 재정밀화. 신규: 발신 1일 이내 lore 메시지는 미러 폴백 전부 미인덱스라 도구형 UA 직접 수신이 유일한 실측 경로임을 확인(신선도 한계) (출처: session-logs/20260727-213100-094c-*, 20260728-040459-14e9-*)
- 2026-07-29: 7번째 관측 — UA 스윕 재확인(d39c, 07-28 판정과 일치) + 브라우저 Mozilla UA 의 신규 응답 변형(3f8a, 기존 `200`+Anubis 챌린지 대신 `500 Server Error` "Oh noes!" 최초 관측, 자체 수집기 UA + infradead.org 메일 아카이브로 우회, base64 CTE mbox 는 python 디코딩) + yhbt.net 미러(b662)·patchwork.kernel.org API(425d) 병행 지속 + NNTP 직접 접근의 프로토콜 상세 최초 확정(1ece, GREET 201·ARTICLE 220·HEAD 221·BODY 222). 6렌즈 research 전부 발사, anti-bot 로 인한 완전 결손은 없었으나 4개 세션(b662·d7dd·3f8a·d39c)은 로그에 최종 dossier JSON 이 남지 않음(feda·425d 만 명시적 완료 확인) — 상세는 [[dev-blog]] 07-29 운영 노트로 분리 (출처: session-logs/20260729-030012-1ece-*, -035106-b662-*, -040000-feda-*, -041304-d7dd-*, -042651-425d-*, -044041-3f8a-*, -045245-d39c-*)
- 2026-07-31: 9번째 관측 — 차단 지속(6e61·0836·87b6 bare 403, fdc4 Anubis 챌린지, 07-24 이래 8일 연속). UA 스윕 실측(6e61): `mutt/2.2` 메일 클라이언트 UA 가 통과 목록에 신규 추가, `git/2.53.0` 세 버전째 통과, 브라우저 Mozilla UA 는 07-29 의 `500` 변형에서 `200`+Anubis 챌린지 HTML 형태로 복귀. `git.kernel.org` cgit 는 기본 curl UA 로도 200 — 필터는 lore 국한. 8번째 관측(07-30)은 판정 동일로 본문 무변경이었음을 명시 (출처: session-logs/20260731-030015-6e61-*, -040747-0836-*, -042126-87b6-*, -045010-fdc4-*)
