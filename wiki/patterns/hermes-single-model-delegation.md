---
title: "Hermes 단일 모델 delegate_task — context 부패 지연 + 약한 모델 신뢰도"
domain: "ai-agent"
sensitivity: "public"
tags: ["Hermes", "delegate_task", "single-model", "context rot", "orchestrator-worker", "weak model", "Qwen", "context hygiene", "위임"]
created: "2026-07-11"
updated: "2026-07-11"
sources:
  - "local-only: ~/.hermes/config.yaml (delegation 블록, 2026-07-11 실측)"
  - "local-only: ~/.hermes/hermes-agent/tools/async_delegation.py"
confidence: "high"
related:
  - "wiki/concepts/hermes-agent.md"
  - "wiki/analyses/multi-agent-orchestration-taxonomy.md"
  - "wiki/analyses/ai-coding-agent-cost-and-context-patterns.md"
  - "wiki/analyses/weak-model-agent-reliability-compounding.md"
---

# Hermes 단일 모델 delegate_task — context 부패 지연 + 약한 모델 신뢰도

모델을 하나만 쓰는 Hermes(예: 회사 PC의 소형 오픈소스 모델 Qwen ~27B)에서도, `delegate_task`로
서브에이전트에 일을 넘기면 이득이 있다. **모델 성능 차이 때문이 아니라 context 위생 때문**이다.
Claude Code 모델 계층 오케스트레이션([[claude-code-model-tier-orchestration-gate]])이 tiering으로 푸는 걸,
단일 모델 Hermes는 **동일 모델 위임**으로 푼다.

## 왜 단일·동일 모델인데도 이득인가

- Hermes `delegate_task`는 orchestrator-worker다. 자식은 **부모 대화를 모르고** `goal`/`context`만 받아
  처리 후 **요약만 반환**한다([[hermes-agent]] 멀티에이전트 방식 A).
- 따라서 무거운 조사·다파일 읽기·반복 작업의 **tool 출력이 리더 대화에 누적되지 않는다** → 리더 컨텍스트가
  얇게 유지되어 **context 부패(context rot)가 늦게 온다**. 같은 모델이어도 성립한다.
  ([[ai-coding-agent-cost-and-context-patterns]] 패턴2: fresh-context 서브에이전트 + 상태 외부화.)
- 약한 모델(소형 Qwen)에는 추가로, 역할 분할 + 검증 게이트가 신뢰도를 올린다
  ([[weak-model-agent-reliability-compounding]]) — 단, 아래 "한계"의 전제가 있다.

## 실제 설정 (검증됨: `~/.hermes/config.yaml` `delegation:` 블록)

```yaml
delegation:
  model: ''                    # 비움 = 리더와 동일 모델. 단일 모델이면 자식도 그 모델(Qwen).
  provider: ''                 # 비움 = 리더 provider 상속
  inherit_mcp_toolsets: true
  max_iterations: 50           # 자식 1개의 최대 반복
  child_timeout_seconds: 600
  reasoning_effort: ''
  max_concurrent_children: 3   # 동시 자식 수
  max_spawn_depth: 1           # 위임 깊이(자식이 또 위임 못 함)
  orchestrator_enabled: true   # delegate_task 도구 활성
  subagent_auto_approve: false # 자식의 위험 작업 자동승인 여부
context:
  engine: compressor           # 리더 컨텍스트 압축 엔진(위임과 병행해 부패 완화)
```

- 기본값 자체가 **동일 모델·orchestrator 활성**이라, 기능은 이미 있다. 가이드의 본질은 *잘 쓰도록 튜닝·유도*.
- `delegate_task(background=true)`면 비동기 팬아웃(백그라운드 유닛으로 회수). 동시성은 `max_concurrent_children`로 제한.

## 소형 Qwen(약한 모델) 권장 튜닝·운영 규칙

1. **깊이는 1로 유지** (`max_spawn_depth: 1`). 약한 모델은 깊은 재귀 오케스트레이션을 감당 못 한다.
2. **동시성은 보수적으로**(`max_concurrent_children` 2~3). ⚠️ 로컬 소형 모델은 클라우드 API와 달리 동시 추론이
   VRAM·처리량 병목 → 올릴수록 오히려 느려질 수 있다. 로컬 서빙 처리량에 맞춰라.
3. **위임을 "구조적으로" 유도**(SOUL.md / 시스템 프롬프트에 규칙 명시):
   - "무거운 조사·다파일 읽기·긴 반복 작업은 `delegate_task`로 **좁게 정의**해 넘겨라."
   - "자식에겐 **명확한 goal + 필요한 최소 context만** 주고, **요약만** 받아라."
   - 약한 모델은 자유 오케스트레이션이 약하므로, *언제 위임할지*를 예시로 못박아 준다.
4. **검증 게이트**: 리더가 자식 결과를 그대로 신뢰하지 말고 확인/대조하게 한다(약한 모델 신뢰도 복리 붕괴 방지).
5. **`subagent_auto_approve: false` 유지** — 약한 자식이 위험 작업(삭제·쓰기)을 자동 승인하지 않게.
6. **상태 외부화**: 긴 작업의 상태는 파일/메모리로 저장(자식은 매번 fresh context). `context.engine: compressor` 병행.

## 한계 (과신 금지)

- **리더가 약하면 분해·위임 품질도 낮다.** context 위생 이득(부패 지연)은 확실하지만, 추론 품질 자체는 모델 한계를
  넘지 못한다. delegate_task는 "약한 모델을 강하게" 만들지 않는다 — "약한 모델이 긴 세션에서 덜 무너지게" 할 뿐.
- 로컬 동시성 병목(위 2번). 클라우드 tiering 직관을 그대로 적용하면 안 된다.
- 위임 lifecycle 이벤트는 `tool.started`/`tool.completed`(tool=`delegate_task`)로 오고 결과는 리더의
  `message.delta`/`run.completed.output`으로 온다 — 모니터링 시 [[hermes-agent]] 참조.

## 적용 절차 (회사 PC)

1. `~/.hermes/config.yaml`(또는 대상 프로필의 config) `model.provider`가 **로컬 Qwen 엔드포인트**
   (vLLM/OpenAI 호환)를 가리키는지 확인. `delegation.model: ''`(동일 모델)인지 확인.
2. `orchestrator_enabled: true`, `max_spawn_depth: 1`, `max_concurrent_children`는 로컬 처리량에 맞춰 2~3.
3. `SOUL.md`/프롬프트에 위 "구조적 위임" 규칙 추가.
4. 게이트웨이 재시작(`hermes -p <profile> gateway restart`).
5. 검증: 무거운 조사 작업을 시켜 `delegate_task` 발생 + 리더 세션 토큰이 급증하지 않는지 관찰
   (`hermes kanban`/로그/Runs API 이벤트).

## 변경 이력

- 2026-07-11: 최초 생성 — 단일 소형 모델(회사 PC Qwen) Hermes에서 delegate_task로 context 부패 지연 + 약한 모델
  신뢰도 보강 전략 정리. `~/.hermes/config.yaml` delegation 블록 실측 기반, [[claude-code-model-tier-orchestration-gate]]의
  tiering을 단일 모델 동일-모델 위임으로 대응시킴.
