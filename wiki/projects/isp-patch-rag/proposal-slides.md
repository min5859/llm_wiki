---
marp: true
theme: default
paginate: true
size: 16:9
footer: "ISP Patch RAG · Internal / Advisory"
title: "[슬라이드] ISP Driver 패치 지식 RAG 시스템"
domain: "work"
sensitivity: "internal"
tags: ["proposal", "presentation", "marp", "slides", "rag"]
created: "2026-06-03"
updated: "2026-06-03"
sources:
  - "design-discussion: 2026-06-03 Claude Code 세션"
confidence: "medium"
related:
  - "wiki/projects/isp-patch-rag/00-proposal.md"
---

<style>
:root{
  --accent:#6366f1; --accent2:#22d3ee;
  --good:#34d399; --mid:#fbbf24; --warn:#f87171;
  --ink:#e5e7eb; --muted:#94a3b8;
  --card:rgba(255,255,255,.06); --border:rgba(255,255,255,.14);
}
section{
  font-family:'Pretendard','Apple SD Gothic Neo','Noto Sans KR',system-ui,sans-serif;
  background:
    radial-gradient(1100px 520px at 82% -12%, rgba(99,102,241,.28), transparent),
    radial-gradient(900px 500px at -10% 110%, rgba(34,211,238,.18), transparent),
    linear-gradient(135deg,#0b1220 0%, #111827 58%, #0b1220 100%);
  color:var(--ink); font-size:25px; line-height:1.5;
  padding:60px 72px; letter-spacing:.2px;
}
section::after{ color:var(--muted); font-size:15px; }
footer{ color:var(--muted); font-size:14px; }
h1{
  font-size:44px; font-weight:800; margin:0 0 .45em;
  background:linear-gradient(90deg,#a5b4fc,#67e8f9);
  -webkit-background-clip:text; background-clip:text; color:transparent;
  border-left:8px solid var(--accent); padding-left:20px;
}
h2{ font-size:30px; color:#c7d2fe; margin:.2em 0; }
h3{ color:#cbd5e1; font-weight:600; }
strong{ color:#ffffff; }
a{ color:#7dd3fc; }
li{ margin:.3em 0; }
small{ color:var(--muted); font-size:.8em; }
table{
  border-collapse:separate; border-spacing:0; width:100%;
  font-size:20px; border-radius:14px; overflow:hidden;
  box-shadow:0 10px 34px rgba(0,0,0,.4); margin-top:10px;
}
th{ background:linear-gradient(90deg,var(--accent),#4338ca); color:#fff; padding:12px 14px; text-align:left; }
td{ background:var(--card); padding:10px 14px; border-top:1px solid var(--border); }
code{ background:rgba(148,163,184,.2); padding:2px 7px; border-radius:6px; font-size:.85em; color:#a5f3fc; }
pre{ background:#0a0f1c; border:1px solid var(--border); border-radius:12px; }

section.lead{ display:flex; flex-direction:column; align-items:center; justify-content:center; text-align:center; }
section.lead h1{ font-size:58px; border:0; padding:0; }
section.lead h3{ color:#a5b4fc; font-weight:600; margin:.15em 0; }

.flow{ display:flex; align-items:center; gap:16px; margin-top:26px; }
.col{ display:flex; flex-direction:column; gap:12px; }
.node{
  background:var(--card); border:1px solid var(--border); border-radius:14px;
  padding:13px 18px; font-weight:600; text-align:center;
  box-shadow:0 6px 20px rgba(0,0,0,.3);
}
.node.big{ background:linear-gradient(135deg,#312e81,#0e7490); border-color:#67e8f9; padding:22px 22px; }
.node.out{ border-left:4px solid var(--accent2); text-align:left; }
.arrow{ font-size:32px; color:var(--accent2); font-weight:800; }

.arch{ display:flex; align-items:center; justify-content:center; gap:20px; margin-top:30px; }
.comp{ background:var(--card); border:1px solid var(--border); border-radius:18px; padding:22px 24px; text-align:center; min-width:290px; box-shadow:0 10px 30px rgba(0,0,0,.4); }
.comp.c1{ border-top:5px solid var(--accent); }
.comp.c2{ border-top:5px solid var(--accent2); }
.lab{ font-size:.78em; color:var(--accent2); font-weight:700; }
.db{ background:linear-gradient(135deg,#155e75,#1e3a8a); border:1px solid #67e8f9; border-radius:22px; padding:26px 22px; min-width:170px; text-align:center; font-weight:700; box-shadow:0 0 42px rgba(34,211,238,.35); }

.cards{ display:grid; grid-template-columns:1fr 1fr; gap:16px; margin-top:14px; }
.card{ background:var(--card); border:1px solid var(--border); border-radius:14px; padding:15px 18px; }
.card h3{ margin:.1em 0 .3em; }
.badge{ display:inline-block; padding:3px 11px; border-radius:999px; font-size:.66em; font-weight:800; vertical-align:middle; }
.badge.good{ background:#064e3b; color:var(--good); }
.badge.mid{ background:#451a03; color:var(--mid); }
.badge.warn{ background:#450a0a; color:var(--warn); }
.warnbox{ border-left:4px solid var(--warn); background:rgba(248,113,113,.12); padding:9px 15px; border-radius:8px; margin:9px 0; }

.steps{ display:flex; gap:12px; margin-top:18px; }
.step{ flex:1; background:var(--card); border:1px solid var(--border); border-top:4px solid var(--accent); border-radius:12px; padding:14px; }
.step b{ color:#a5b4fc; }
</style>

<!-- _class: lead -->

# ISP Driver 패치 지식 RAG 시스템

### 머지되는 모든 패치 + Jira 티켓을 지식으로 축적하고
### 새 이슈를 AI가 **초도 분석**

<br>

###### 2026-06-03 · 사내 발표안 (Advisory)

---

# 1. 한 줄 요약

머지되는 **모든 패치(diff)와 Jira 리뷰 티켓**을 사내 지식 DB로 축적하고,

새 이슈가 나오면 AI가 **유사 과거 이슈 · 회귀 후보 · 재현 방법**을 초도 분석으로 제시한다.

---

# 2. 문제 (현황)

- 하루 **~10건** 패치 머지 (연 ~2,500건) — 지식이 개인·티켓에 흩어져 **휘발**
- 비슷한 이슈가 반복돼도 **과거 해결 이력을 빠르게 못 찾음**
- 인력 이동 시 **tribal knowledge 손실**
- 최근 머지 패치의 side effect를 **사람 기억에 의존**

---

# 3. 해결 아이디어

<div class="flow">
  <div class="col">
    <div class="node">Gerrit merge</div>
    <div class="node">Jira ticket</div>
  </div>
  <div class="arrow">→</div>
  <div class="node big">지식 DB<br><small>patch · ticket · 구조메타 · 벡터</small></div>
  <div class="arrow">→</div>
  <div class="col">
    <div class="node out">유사 이슈 검색</div>
    <div class="node out">회귀 / side-effect 후보</div>
    <div class="node out">재현 TC 제안</div>
  </div>
</div>

<br>

- 머지 시점마다 **자동 수집** → 검색 가능한 institutional memory
- 새 이슈 입력 시 **advisory(보조) 초도 분석** — 사람을 대체하지 않고 옆에서 돕는다

---

# 4. 기대 효과

<div class="cards">
  <div class="card">
    <h3>검색 DB <span class="badge good">효용 높음</span></h3>
    링크된 patch+ticket 검색만으로도 가치. 코퍼스가 쌓일수록 <strong>복리로</strong> 증가, 수집 비용 ≈ 0
  </div>
  <div class="card">
    <h3>회귀 탐지 <span class="badge mid">조건부</span></h3>
    맞으면 가장 비싼 문제를 잡지만, gold set 검증 전엔 <strong>advisory 힌트</strong> 수준
  </div>
</div>

<div class="warnbox"><strong>핵심 변수</strong> — Jira description 품질이 ROI를 좌우</div>

---

# 5. 아키텍처 — 2 컴포넌트

<div class="arch">
  <div class="comp c1">컴포넌트 1<br><strong>수집 (DB 빌더)</strong><br><small>백그라운드 · 버전별 분기</small><br><span class="lab">write ▸</span></div>
  <div class="db">Postgres<br>+ pgvector<br><small>공유 DB</small></div>
  <div class="comp c2">컴포넌트 2<br><strong>이슈 분석 Web App</strong><br><small>버전 공통 · claude -p · 인터랙티브</small><br><span class="lab">◂ read</span></div>
</div>

<br>

- 공유 자원은 **DB 하나** → 두 컴포넌트 독립 운영·확장
- 구조 추출(파일/함수/register)은 **결정적 SW**, 요약·답변만 **AI**

---

# 6. 두 가지 구현 방식 — 차이는 "수집"뿐

| 구분 | 버전 A · 수집 SW | 버전 B · 수집 Hermes |
|---|---|---|
| 수집 골격 | 자체 Python cron | Hermes 오케스트레이션 |
| 수집 AI | `claude -p` | 사내 LLM (Hermes) |
| 결정적 처리 | SW | **SW (MCP 도구)** |
| 분석 컴포넌트 | 공통 (claude -p) | 공통 (claude -p) |

###### 분석 web app은 **버전 공통** — 한 번 만들면 양쪽이 그대로 사용

---

# 7. 로드맵

<div class="steps">
  <div class="step"><b>Phase 0</b><br>PoC<br><small>백필·링킹률·방식비교</small></div>
  <div class="step"><b>Phase 1</b><br>MVP<br><small>분석 web + 검색 (A)</small></div>
  <div class="step"><b>Phase 2</b><br>운영화<br><small>실시간 ingest·봇</small></div>
  <div class="step"><b>Phase 3</b><br>확장<br><small>side-effect 후보 (B)</small></div>
  <div class="step"><b>Phase 4</b><br>가이드<br><small>회귀·무력화·재현 (C)</small></div>
</div>

<br>

- **기능 A(검색)** 먼저 — 단독으로도 가치, 빠른 효용
- 기능 B·C는 **gold set 검증** 후 단계적 확장

---

# 8. 보안 전제 <span class="badge warn">선결 · 최우선</span>

- ISP driver 코드·티켓은 **confidential** → 무단 외부 송신 금지

<div class="warnbox">⚠️ <strong><code>claude -p</code> 기본값은 외부(Anthropic) 송신</strong> → <strong>사내 승인 엔드포인트</strong>로만 라우팅 (도입 선결조건)</div>

<div class="warnbox">⚠️ <strong>Hermes 기본 통합(Nous Portal·OpenRouter)도 외부</strong> → 차단, 사내 LLM만 구성</div>

- 실데이터는 사내 인프라에만 존재 · 발표/설계 문서엔 실제 코드·수치 미포함

---

# 9. 리스크 & 성공 조건

<div class="cards">
  <div class="card">
    <h3>✅ 성공 조건</h3>
    <ul>
      <li>Jira description 품질·일관성</li>
      <li>링킹 성공률</li>
      <li>gold set 정밀도 검증</li>
      <li><strong>advisory 포지셔닝</strong></li>
    </ul>
  </div>
  <div class="card">
    <h3>⚠️ 리스크 → 대응</h3>
    <ul>
      <li>AI 비결정성 → 스키마 강제·검증</li>
      <li>false positive → advisory</li>
      <li>코퍼스 노후화 → recency 필터 + consolidation</li>
    </ul>
  </div>
</div>

---

# 10. 요청 사항 (Ask)

- 사내 LLM 엔드포인트 / `claude -p` **승인 채널** 확정
- **Gerrit 이벤트 구독** + Jira API 접근 권한
- 백필 대상 기간·규모 합의, PoC용 **GPU/스토리지**
- **gold set 구축 협조** — 과거 "회귀로 판명된" 사례

---

<!-- _class: lead -->

# 감사합니다

### 질문 / 논의

<br>

###### 상세 설계 · `wiki/projects/isp-patch-rag/`
