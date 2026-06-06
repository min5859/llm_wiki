---
title: "Qualcomm 카메라 커널(cam_isp/CAMSS) — 구조·소스 입수·Exynos ISP 비교"
domain: work
sensitivity: public
tags: ["qualcomm", "camera", "isp", "kernel-driver", "camss", "ife", "vfe", "csid", "exynos", "pablo", "android", "gpl", "codelinaro"]
created: 2026-06-06
updated: 2026-06-06
sources:
  - "session-logs/20260606-170440-643f-finder-에서-경로-확인하는-방법.md"
confidence: medium
related:
  - "wiki/projects/isp-patch-rag/isp-patch-knowledge-rag-system.md"
  - "wiki/analyses/soc-otf-sensor-to-ap.md"
---

# Qualcomm 카메라 커널(cam_isp/CAMSS) — 구조·소스 입수·Exynos ISP 비교

Android 폰(예: Galaxy S26 Snapdragon)의 카메라 ISP 드라이버를 분석할 때의 기준점 정리. 어디까지가 공개(GPL)이고 어디부터 독점인지, 소스를 어떻게 합법적으로 받는지, Qualcomm `cam_isp` 의 디렉터리·데이터 흐름 골격, 그리고 Exynos ISP 와의 차이를 다룬다. 실제 CodeLinaro `camera-kernel` 레포(64M)를 클론해 구조를 확인한 결과 기반.

## 공개(GPL) vs 독점(proprietary) 경계

"오픈소스라 공개되어 있을 것"이라는 가정은 **커널 부분에 한해서만** 맞다. ISP 알고리즘/HAL 핵심은 닫혀 있다.

| 계층 | 경로(예) | 공개 여부 |
|---|---|---|
| **커널 드라이버** | `techpack/camera`, `drivers/media/platform/.../camera`, `kernel_platform/qcom/opensource/camera-kernel/` | GPL → **반드시 공개·다운로드 가능** ✅ |
| **유저스페이스 HAL / CamX-CHI** | `CamX`, `chi-cdk`, ISP firmware tuning | Qualcomm **독점**, 공식 비공개 ❌ |

## 소스 입수 두 경로

**경로 1 — opensource.samsung.com (실제 기기에 들어간 그대로)**
- GPL 의무로 삼성이 기기별 커널 소스 tar 를 공개. 삼성 커스텀 패치 포함 → 정확성 ↑.
- 모델번호로 검색(S26 Snapdragon 추정: `SM-S941`/`SM-S946`/`SM-S948` 계열, 정확한 번호는 기기 설정 > 휴대폰 정보 확인).
- ⚠️ 브라우저 수동 다운로드(약관 동의)라 `wget`/`curl` 로 바로 못 받는다.

**경로 2 — git.codelinaro.org (CLO, git clone 가능)**
- Qualcomm 원본 카메라 커널 트리. 삼성 커스텀은 빠지지만 git 이라 버전 비교·blame 이 되어 **구조 분석엔 더 깔끔**하다.
- `git clone https://git.codelinaro.org/clo/la/platform/vendor/opensource/camera-kernel.git`
- 칩셋 태그/브랜치로 체크아웃(예: SM8750 "Sun" → `LA.UM.12.x` 계열). 태그 2600+/브랜치 280+ 로 칩셋별 비교 가능.

요약: 경로 1 = "내 기기에 실제로 올라간 코드", 경로 2 = "Qualcomm 레퍼런스 원본".

## `cam_isp` / CAMSS 디렉터리 골격

`camera-kernel/drivers/` 주요 구성:

- **`cam_isp`** — ISP 파이프라인 (IFE/VFE, CSID 등) ★ 분석 대상
- `cam_sensor_module` — 센서/액추에이터/플래시
- `cam_cdm` — Camera DMA, `cam_cpas` — 버스/전력, `cam_smmu` — IOMMU
- `cam_icp` — ICP(이미지 처리 코프로세서), `cam_req_mgr` — 요청 관리자
- `cam_core` / `cam_sync` / `cam_utils` — 공통 프레임워크

`cam_isp` 내부(`isp_hw_mgr/isp_hw/`): `ife_csid_hw`, `vfe_hw/{vfe_bus, vfe_top, vfe17x}` 등 IP 블록별 계층.

데이터 흐름 골격 (센서 → CSID → IFE → DDR):
- **리소스 획득** — `cam_ife_hw_mgr_acquire_res_ife_csid_pxl`/`_rdi`, `_ife_out_pixel`/`_rdi` 가 PIX(이미지) 경로와 RDI(raw) 경로를 나눠 잡는다(`CAM_IFE_PIX_PATH_RES_RDI_*`, `CAM_ISP_IFE_OUT_RES_RDI_*`).
- **컨텍스트 상태기계** — `cam_isp_context.c` 의 `cam_ctx_ops` 가 SOF/EPOCH/BUBBLE 상태별 `apply_req` 핸들러(`__cam_isp_ctx_apply_req_in_sof`/`_epoch`/`_bubble`)로 요청을 적용.
- **HW 업데이트** — `cam_ife_mgr_prepare_hw_update` → `cam_ife_mgr_config_hw` 로 패킷 파싱(`cam_isp_packet_parser`) 후 레지스터 설정.

## Exynos ISP 와의 차이

- Exynos ISP = 삼성 자체(SLSI) **"Pablo" 드라이버(구 FIMC-IS)**, `drivers/media/platform/exynos/camera/` 에 위치하며 삼성 커널 소스에서 별도 다운로드 필요.
- 칩이 다르면 ISP 가 다르다: **Snapdragon 모델 = Qualcomm cam_isp**, **Exynos 모델 = 삼성 Pablo**. 지역/모델번호(SM-S94x 등)로 칩이 갈리므로 "퀄컴 ISP 코드"가 의미 있으려면 Snapdragon 모델이어야 한다.

> ⚠️ cam_isp ↔ Pablo 의 상세 데이터 흐름 대조는 세션에서 착수만 하고 끝맺지 못함. 위 Qualcomm 골격은 클론한 레포에서 확인한 사실이고, Exynos 측은 아키텍처 일반 지식 수준 — 정밀 대조는 삼성 tar 확보 후 보강 필요(그래서 confidence: medium).

## 관련 맥락

- [[isp-patch-knowledge-rag-system]] — ISP 드라이버 패치(Gerrit×Jira) 지식 RAG. 이 커널 구조 이해가 패치 분석의 배경 지식.
- [[soc-otf-sensor-to-ap]] — Sensor→AP 직접 스트리밍(OTF) 구조. CSID→IFE 입력단의 상위 SoC 관점.

## 변경 이력

- 2026-06-06: 최초 생성 — Galaxy(cowork) 세션에서 CodeLinaro camera-kernel 클론·구조 확인 결과 정리 (출처: session-logs/20260606-170440-643f-*). cam_isp↔Pablo 정밀 대조는 미완(추후 보강)
