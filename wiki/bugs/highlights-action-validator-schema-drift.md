---
title: "publish validator 가 옛 스키마(action) 만 강제해 신 스키마(if/do/verify) 게시 전부 실패"
domain: "ai-agent"
sensitivity: public
tags: ["bug", "schema-drift", "validator", "pipeline", "dev-blog", "json-schema", "newsletter"]
created: 2026-05-13
updated: 2026-05-13
sources:
  - "session-logs/20260513-074737-a32f-오늘날짜-포스팅이-안-보입니다.-오늘-동작-했는지-확인해-주세요.md"
confidence: high
related:
  - "wiki/projects/dev-blog.md"
  - "wiki/patterns/prompt-schema-pipeline-coupling.md"
  - "wiki/analyses/llm-content-quality-guards.md"
---

# publish validator 의 highlights[].action 스키마 표류

LLM rewrite 파이프라인의 출력 스키마를 프롬프트에서 변경했는데 publisher / weekly / 일부 rewrite validator 갱신이 같은 PR 안에서 빠져, 다음 cron 사이클에 모든 토픽이 publish 단계 첫 줄에서 throw 한 사례.

## 증상

매일 07:00 KST launchd 잡은 정상 실행 (`logs/daily/2026-05-13-*.log` 가 모두 `startedAt` 있음). collect / draft / rewrite 단계는 모두 `code: 0`. 그러나 publish 단계가 10개 토픽 (linux, android, opensource, opensource-curation, linux-kernel-security, linux-toolchain, linux-distro-stable, linux-perf-rt, linux-arch-platform, linux-gpu-ai) 전부에서 동일 메시지로 실패:

```
Error: highlights[0].action required
    at validatePost (file:///.../scripts/publish-linux.mjs:59:72)
    at main      (file:///.../scripts/publish-linux.mjs:68:3)
```

콘텐츠 git diff 가 비어 있으므로 `daily-deploy.sh` 의 git push 단계는 `no content/ changes — nothing to push` 로 정상 통과 — silent skip 형태로 사이트에서 오늘자 글이 통째로 사라짐 (사용자가 사이트를 직접 보고서야 인지).

## 근본 원인

직전 커밋 `223ac17 feat(prompts): headline·title 사건 삽입·action 3분해·CJK 가드 지시 추가` 가 프롬프트 출력 스키마를 다음과 같이 변경:

```diff
- "highlights": [{ "title": "...", "priority": "상", "verifyLink": "...", "action": "..." }]
+ "highlights": [{ "title": "...", "priority": "상", "verifyLink": "...",
+                  "if": "...", "do": "...", "verify": "..." }]
```

rewrite 단계의 출력은 의도대로 신 스키마로 나왔지만 (`rewritten-2026-05-13-linux-daily-briefing.json` 에 `if`/`do`/`verify` 가 있고 `action` 은 없음), 다음 단계의 validator 들은 옛 스키마를 강제하고 있었음:

```js
// scripts/publish-linux.mjs:59 (등 다섯 군데)
for (const key of ['title', 'priority', 'verifyLink', 'action']) {
  if (typeof highlight[key] !== 'string' || !highlight[key])
    throw new Error(`highlights[${index}].${key} required`);
}
```

대조적으로 `build-site.mjs:249` 와 `ai-rewrite-linux.mjs:99-103` 의 일부 메서드는 이미 두 스키마를 모두 받도록 진화해 있었음 — 즉 *프로젝트 전체로는 마이그레이션이 진행 중이었으나 한 번에 동기화되지 못한 케이스*.

또한 `opensource` / `opensource-curation` 은 rewrite 단계의 validator (`ai-rewrite-opensource.mjs:74`, `ai-rewrite-opensource-curation.mjs:97`) 자체에도 같은 강제가 남아 있어 rewrite 산출물조차 생성되지 않았음 — publish 보다 더 앞 단계에서 막힘.

## 영향 범위

| 컴포넌트 | 변경 전 상태 | 변경 필요 |
|---------|------------|----------|
| 프롬프트 7종 (linux/android/lens/opensource/opensource-curation) | `action` → `if`/`do`/`verify` 로 이미 갱신됨 (commit 223ac17) | — |
| `build-site.mjs` | `hasAction` / `hasIfDoVerify` 양쪽 분기 이미 존재 | — |
| `ai-rewrite-linux.mjs`, `ai-rewrite-android.mjs`, `ai-rewrite-lore-lens.mjs` | `const hasFlat = typeof highlight.action === 'string'` 패턴으로 양쪽 받음 | — |
| `publish-linux.mjs`, `publish-android.mjs`, `publish-lore-lens.mjs` | `action` 만 강제 | **수정** |
| `publish-opensource.mjs`, `publish-opensource-curation.mjs` | `action` 만 강제 | **수정** |
| `ai-rewrite-opensource.mjs`, `ai-rewrite-opensource-curation.mjs` | rewrite validator 가 `action` 만 강제 | **수정** |
| `weekly-linux.mjs` (validateWeekly + ensureHighlight) | `action` 만 강제 / 폴백 시 구조화 필드 잃음 | **수정** |

8 files +64/-14.

## 수정

validator 패턴을 `build-site.mjs` 와 동형으로 — `action` 단일 필드 또는 `if`+`do`+`verify` 세 필드 중 하나만 있으면 통과:

```js
function validatePost(post) {
  // ...
  for (const [index, h] of post.highlights.entries()) {
    if (!h || typeof h !== 'object') throw new Error(`highlights[${index}] must be an object`);
    for (const key of ['title', 'priority', 'verifyLink']) {
      if (typeof h[key] !== 'string' || !h[key])
        throw new Error(`highlights[${index}].${key} required`);
    }
    const hasAction = typeof h.action === 'string' && h.action;
    const hasIfDoVerify = ['if','do','verify'].every(k => typeof h[k] === 'string' && h[k]);
    if (!hasAction && !hasIfDoVerify)
      throw new Error(`highlights[${index}] requires either .action or .if/.do/.verify`);
    if (!PRIORITY_VALUES.has(h.priority))
      throw new Error(`highlights[${index}].priority must be 상/중/하`);
  }
}
```

weekly-linux 의 `ensureHighlight` 는 구조화 필드를 우선 보존하면서 디폴트가 필요한 경우에만 `action: '본문에서 확인하세요.'` 합성. 회귀 테스트 49/49 통과.

## 5/13 분 콘텐츠 복구 절차

코드 수정 후 그날 분 publish 만 재실행하면 됨 — 토큰을 아끼려면 `daily-deploy.sh` 를 처음부터 다시 돌리지 말고 단계만 호출:

```bash
# 1. publish 단계에서 막혔던 8개 토픽 — rewritten-2026-05-13-*.json 이미 디스크에 있음
NEWSLETTER_DATE=2026-05-13 node scripts/publish-linux.mjs
NEWSLETTER_DATE=2026-05-13 node scripts/publish-android.mjs
for L in linux-kernel-security linux-toolchain linux-distro-stable linux-perf-rt \
         linux-arch-platform linux-gpu-ai; do
  NEWSLETTER_DATE=2026-05-13 node scripts/publish-lore-lens.mjs "$L"
done

# 2. rewrite 단계에서 막혔던 2개 토픽 — 처음부터 다시
PUBLISH_DAILY=1 NEWSLETTER_DATE=2026-05-13 node scripts/run-daily-opensource.mjs
PUBLISH_DAILY=1 NEWSLETTER_DATE=2026-05-13 node scripts/run-daily-opensource-curation.mjs
```

10개 토픽 모두 publish 성공 후 두 커밋으로 분리 (코드 수정 vs 콘텐츠) — `daily-deploy.sh` 의 평소 흐름과 동일하게 의도가 다른 변경을 섞지 않음.

## 재발 방지

1. **Validator 패턴을 공용 모듈로** — 5개 publisher 의 `validatePost` 함수와 weekly 의 `validateWeekly` 가 highlight 검증 로직을 동일 패턴으로 복붙하고 있음. `scripts/lib/highlight-validator.mjs` 로 추출하면 다음 스키마 진화 시 한 곳만 고치면 됨.
2. **스키마 변경 PR 의 체크리스트** — 프롬프트 출력 스키마를 바꾸는 PR 은 다음 모두를 확인:
   - rewrite validator (`ai-rewrite-*.mjs`)
   - publish validator (`publish-*.mjs`)
   - 사이트 빌더 (`build-site.mjs`)
   - 주간 집계 (`weekly-*.mjs`)
   - 회귀 테스트
3. **샘플 게시본의 사전 검증** — `daily-deploy.sh` 가 publish 전에 schema 검증을 dry-run 으로 한 번 더 돌려서, 실패 시 다음 토픽으로 넘어가지 말고 전체 잡을 중단 + 알림 (현재는 한 토픽 실패가 다음을 막지 않지만, 10개 전부 실패는 cron 사일런트 — 가시성 부재).

→ 일반 패턴: [[prompt-schema-pipeline-coupling]]
