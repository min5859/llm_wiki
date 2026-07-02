# v1 → v2 이관 기록 (완료)

2026-07-02, v1(`~/project/git/wk/llm_wiki`) 194개 중 **73건 이관** (ai-agent 44 · trading 29).
전체 목록은 `wiki/index.md` 참조. v1은 수정하지 않고 보존.

## 선별 기준 (근거 기반)

도메인 일치 + 아래 중 하나 이상:

- **문서 간 참조 5회 이상** (다른 문서가 인용하는 허브 지식)
- **변경 이력 2회 이상** (반복 갱신된 살아있는 지식)
- **도메인 고유 지식** (KIS D+2 결제, 휴장일 감지 등 — 재조회 시 검색으로 찾기 어려움)

주요 근거 상위: research-write-agent-separation(참조 42), llm-content-quality-guards(40),
dev-blog(변경 33·참조 34), ht-trading(변경 23·참조 23).

## 의도적으로 제외한 것

| 부류 | 예 | 이유 |
|------|-----|------|
| 뉴스성 도구 리뷰 | cursor/zed/continue/daytona/aris 등 | 내용이 빨리 낡음, 재검색이 더 정확 |
| 일반 튜토리얼 | claude-code-overview/basic-usage/setup 등 | 공식 문서로 대체 가능 |
| 논문 스크랩 | diffusion/VLM/신모델 ~25건 | 재조회 이력 없음 |
| 일회성 잡학 | 디스크 정리, 카톡 위치, 단축키 등 | 검색엔진이 더 빠름 |
| **도메인 밖 (신호 높지만 제외)** | disk-monitor(참조 9·변경 5), isp-patch-rag(회사 업무), Next.js/Supabase 풀스택 패턴군 | 2-도메인 원칙 유지. 필요해지면 v1에서 개별 참조 |

## 되돌리기

제외 문서가 필요해지면 v1에서 개별 복사 후 frontmatter `domain` 갱신 + index 등록.
단, "두 번째로 필요해진 시점"에만 — 승격 규칙과 동일.
