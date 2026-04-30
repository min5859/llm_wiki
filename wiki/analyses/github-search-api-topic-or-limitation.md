---
title: "GitHub Repository Search API의 topic 한정자는 OR 연산자를 받지 않는다"
domain: both
sensitivity: public
tags: ["github", "github-search-api", "topic", "or-operator", "query-limitation", "workaround"]
created: 2026-04-30
updated: 2026-04-30
sources:
  - "session-logs/20260430-134759-328e-*.md"
confidence: high
related:
  - "wiki/projects/oss-radar.md"
---

# GitHub Repository Search API의 `topic:` 한정자는 OR 연산자를 받지 않는다

GitHub Repository Search API 에서 토픽을 OR 로 묶으려는 의도가 사실상 동작하지 않는 케이스 정리. 한 번의 OR 쿼리 대신 **카테고리별로 N번 쿼리 후 `full_name` 으로 dedupe** 가 표준 우회.

## 핵심 사실

GitHub Repository Search API의 **기본 연산자는 AND**. 그리고 `topic:` 한정자는 **`OR` 연산자를 받아들이지 않는다** (응답에 `total_count` 가 없는 invalid query 형태로 거부).

## 흔한 함정 코드

```python
# 의도: ai 또는 dev-tools 또는 productivity 토픽 중 하나라도 매칭되는 repo
topic_q = " ".join(f"topic:{c}" for c in categories)
q = f"stars:>={min_stars} pushed:>={since} ({topic_q})"
```

생성되는 쿼리:

```
stars:>=100 pushed:>=2026-04-23 (topic:ai topic:developer-tools topic:productivity)
```

GitHub 의 해석: "**ai 그리고 developer-tools 그리고 productivity** 토픽을 **모두** 가진 repo". 세 토픽을 동시에 단 repo는 거의 없으므로 매번 0건.

```python
# 의도를 OR 로 명시
topic_q = " OR ".join(f"topic:{c}" for c in categories)
```

생성되는 쿼리:

```
stars:>=100 pushed:>=2026-04-23 (topic:ai OR topic:developer-tools OR topic:productivity)
```

마찬가지로 결과 없음. 격리 테스트로 확인:

| 쿼리 | 응답 |
|---|---|
| `topic:ai` (단독) | total: 117,038 ✅ |
| `topic:ai OR topic:developer-tools` (다른 한정자 없음) | total: **None** (invalid) |
| `stars:>=100 pushed:>=YYYY topic:ai` (단일 + AND) | total: 1,434 ✅ |
| `stars:>=100 pushed:>=YYYY (topic:ai OR topic:developer-tools)` | total: 0 |
| `topic:ai OR topic:productivity` | total: **None** |

→ `topic:` 한정자는 OR 연산자를 받지 않는다는 결론이 일관되게 나온다.

## 표준 우회 — 카테고리별 N번 쿼리 + 병합

```python
def fetch_github_search(lookback_days, min_stars, categories):
    """GitHub Repository Search API는 topic: 한정자에 OR 연산자를 지원하지 않으므로,
    카테고리별로 별도 쿼리를 실행한 뒤 full_name 기준으로 병합한다.
    """
    since = (datetime.now(UTC) - timedelta(days=lookback_days)).strftime("%Y-%m-%d")
    queries = [f"stars:>={min_stars} pushed:>={since} topic:{c}" for c in categories]

    merged: dict[str, dict] = {}
    for q in queries:
        for repo in _search_one(q):
            merged.setdefault(repo["full_name"], repo)
    return list(merged.values())
```

`oss-radar` 의 실측 효과: Search 후보 0 → **422** (ai 187, dev-tools 161, productivity 150). `full_name` 으로 dedupe 되어 다중 토픽 매칭 repo 도 한 번만 들어간다.

## 비용 측면

쿼리 수가 카테고리 수만큼 N배가 된다. 인증된 토큰은 5,000 req/h 이므로 카테고리 3~5개 정도면 영향 없음. 쿼리당 1회 호출 + 페이지네이션 (3 페이지 max) 기준으로 보수적으로 잡아도 카테고리 5개 × 페이지 3 = 15 req/실행 — 매일 09:00 한 번 도는 정도라면 무시 가능.

## 다른 한정자에서의 OR

참고: `topic:` 외 다른 한정자 (`stars:`, `language:`, `user:`) 의 OR 동작은 별도. 이 페이지는 `topic:` 만 검증. 일반화하지 말 것.

## 관련 맥락

- [[oss-radar]] 프로젝트의 `discover.py` 가 이 패턴을 사용. 발견 당시 `topic_q = " ".join(...)` 로 쿼리를 생성했으나 GitHub Search 결과가 항상 0이라 사실상 GitHub Trending 만으로 후보가 채워지고 있었다.
- 이 한계는 GitHub 공식 문서에 명시되어 있지 않다. 격리 테스트로만 확인 가능.

## 변경 이력

- 2026-04-30: 최초 생성. oss-radar 디버깅 중 격리 테스트로 발견 (출처: session-logs/20260430-134759-328e-*)
