---
title: "Oracle Cloud Free Tier — 무료 VM 가입·설치 가이드"
domain: "ai-agent"
sensitivity: public
tags: ["pattern", "oracle-cloud", "oci", "free-tier", "always-free", "iaas", "ubuntu", "arm", "ampere", "ssh", "payg", "out-of-capacity", "oci-cli"]
created: 2026-06-21
updated: "2026-07-12"
sources:
  - "대화 세션 20260621 (Oracle Cloud Free Tier 조사)"
  - "대화 세션 20260705~10 (실제 가입·A1 인스턴스 확보·셋업)"
confidence: high
---

# Oracle Cloud Free Tier — 무료 VM 가입·설치 가이드

Oracle Cloud Infrastructure(OCI) 가 제공하는 무료 계정으로 **클라우드에 떠 있는 리눅스 가상 서버(VM)** 를 평생 무료로 받는 절차. "베어메탈 PC" 나 "macOS PC" 가 아니라 **가상화된 IaaS 인스턴스** 임에 주의. 개인 토이 프로젝트·웹서버·홈 서버 대체 용도로 적합.

## 무료 계정의 구조 — 두 묶음

| 구분 | 내용 | 기간 |
|------|------|------|
| **Always Free** | 카드 등록만 하면 영구 무료로 쓰는 자원 | 무기한 |
| **Free Trial 크레딧** | 가입 시 $300 상당 크레딧 (유료 자원 체험용) | 약 30일 |

30일이 지나거나 크레딧을 다 써도 **Always Free 자원은 계속 유지**된다. 베어메탈 등 고급 자원은 크레딧으로만 잠깐 쓸 수 있고 영구 무료는 아니다.

## Always Free 주요 자원 (2026-01 기준)

- **ARM Ampere A1 컴퓨트**: 총 **4 OCPU + 24GB RAM** (1대 몰빵 또는 여러 대 분할 가능) — 핵심 자원
- **AMD x86 마이크로 VM**: `VM.Standard.E2.1.Micro` (1/8 OCPU + 1GB RAM), 최대 2대
- **블록 스토리지(디스크)**: 총 **200GB** — RAM 24GB 와는 별개. 부팅 디스크 + 추가 디스크를 이 안에서 분배
- **오브젝트 스토리지**: 약 20GB
- **Autonomous Database**: 2개 (각 20GB)
- **로드밸런서 1개, VCN, 모니터링, 아웃바운드 전송 10TB/월**

> 실용적 핵심: **ARM 4코어 / RAM 24GB / 디스크 200GB 리눅스 서버 1대**. RAM 24GB 는 개인 서버치고 넉넉한 편.

## 오해 바로잡기

- ✅ **IaaS 맞음** — VM 을 받아 OS(Ubuntu 등) 를 직접 설치·관리
- ❌ **베어메탈 아님** — Always Free 는 가상화된 VM. 베어메탈은 유료/크레딧 전용
- ❌ **macOS PC 아님** — OCI 는 macOS 인스턴스를 제공하지 않음 (맥이 필요하면 AWS EC2 Mac, MacStadium 등 별도 유료 서비스)
- 모니터 달린 데스크탑이 아니라 **SSH 로 접속하는 원격 리눅스 서버**. GUI 가 필요하면 VNC/RDP 직접 설치

## 가입 절차

1. `oracle.com/cloud/free` 접속 → "Start for free"
2. 이메일·국가 입력, 휴대폰 SMS 인증
3. **신용/체크카드 등록 (필수)** — 본인 확인 + 자동 과금 방지용. $0~1 가승인 후 취소됨
4. **홈 리전(Region) 선택** — 이후 **변경 불가**. 한국이면 서울/춘천 리전 권장
5. 가입 완료 → 콘솔(Console) 진입

> ⚠️ **함정**: 인기 리전은 ARM 인스턴스 재고가 자주 동나 `Out of capacity` 에러가 흔하다. 다른 리전 선택 또는 재시도 반복으로 우회.

## VM 생성 절차

1. 콘솔 → **Compute → Instances → Create Instance**
2. **Image**: Canonical Ubuntu (예: 24.04) 선택
3. **Shape**: `VM.Standard.A1.Flex` 선택 → OCPU/메모리 슬라이더 (무료 한도: 4 OCPU / 24GB)
4. **Networking**: 새 VCN 자동 생성 + 퍼블릭 IP 할당 체크
5. **SSH 키**: 로컬에서 키 생성 후 공개키 업로드
   ```bash
   ssh-keygen -t ed25519 -f ~/.ssh/oci_key
   # ~/.ssh/oci_key.pub 내용을 콘솔에 붙여넣기
   ```
6. Create → 인스턴스의 **Public IP** 확인

## 접속 및 초기 설치

```bash
# 1. SSH 접속 (Ubuntu 기본 사용자명: ubuntu)
ssh -i ~/.ssh/oci_key ubuntu@<PUBLIC_IP>

# 2. 패키지 업데이트
sudo apt update && sudo apt upgrade -y

# 3. 방화벽 — OCI 는 2단계 방화벽 (둘 다 열어야 외부 접근됨)
#  (a) 콘솔의 Security List / NSG 에 인바운드 규칙 추가 (예: 80, 443)
#  (b) 인스턴스 내부 iptables 도 열기
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
sudo netfilter-persistent save
```

> ⚠️ **2단계 방화벽 함정**: 포트를 열었는데 접속이 안 되면 십중팔구 콘솔 Security List 또는 인스턴스 내부 `iptables` 중 한쪽만 열린 것. **양쪽 모두** 확인.

> ⚠️ **iptables 규칙 순서 함정**: OCI Ubuntu 는 INPUT 체인 끝에 `REJECT`(icmp-host-prohibited) 규칙이 있다. 80/443 ACCEPT 규칙을 이 **REJECT 보다 위**에 넣어야 한다. `iptables -I INPUT 6` 처럼 위치를 고정하면 체인이 짧을 때 REJECT 뒤에 삽입돼 무력화된다. `REJPOS=$(iptables -L INPUT --line-numbers | awk '/REJECT/{print $1;exit}')` 로 REJECT 위치를 구해 그 앞에 삽입할 것.

## 실전 기록 — 도쿄 재고 품귀와 PAYG 해결 (2026-07)

실제로 계정을 만들어 A1 인스턴스를 확보하며 얻은 교훈. **이 섹션이 이 문서에서 가장 중요하다.**

### 핵심 결론

> **인기 리전(도쿄·서울 등)의 무료 ARM(A1.Flex)은 Always Free 재고 풀이 만성 품귀다. Pay As You Go(PAYG) 전환이 사실상 유일한 현실적 해법이다.**

### 겪은 흐름

1. 도쿄(`ap-tokyo-1`, **AD 1개뿐**)로 가입. 콘솔에서 A1.Flex 생성 시도 → `Out of capacity` 반복.
2. OCI CLI 로 "성공할 때까지 자동 재시도" 스크립트를 백그라운드로 구동.
   - **4 OCPU/24GB**: 480회(약 12시간) 시도 전부 실패.
   - **1 OCPU/6GB 로 낮춤**: 그래도 480회 실패. 즉 코어 수를 낮춰도 안 잡힘.
   - 장기 재시도(5000회)로 전환해도 며칠간 못 잡음.
3. **PAYG(종량제) 업그레이드** 후 재시도 → **841번째 시도에서 즉시 확보**. 유료 재고 풀에 접근하면서 해결됨.

### PAYG 전환이 정답인 이유

- 무료 계정은 **"Always Free 전용" 좁은 재고 풀**만 쓴다 (인기 리전은 상시 고갈).
- PAYG 로 올리면 **훨씬 큰 유료 재고 풀**에 접근 → A1 이 잘 잡힌다.
- **Always Free 한도(ARM 4 OCPU/24GB, 디스크 200GB)는 PAYG 후에도 그대로 무료.** 한도 안이면 **청구 0원**, 결제 수단만 활성화될 뿐.
- 덤으로 **유휴(idle) 회수도 사라진다** (무료 계정은 CPU·네트워크·메모리 사용률이 7일간 95백분위 20% 미만이면 인스턴스 회수 대상. 도쿄처럼 재고 없는 리전은 한 번 회수되면 다시 못 켬).

### PAYG 업그레이드 함정

- **Upgrade 버튼 비활성** = "Add a payment method to upgrade" → **결제 수단(신용카드) 미등록**이 원인. Billing & Cost Management → Payment Method 에서 카드 등록해야 버튼 활성화.
- 가입 때 카드 확인(가승인 ~$1, 자동 취소)과 **청구용 결제 수단 등록은 별개**.
- 개인은 Tax registration number(사업자등록번호) **공란**으로 진행.
- 업그레이드는 처리에 **수십 분~수 시간** 걸림 (Plan type 이 Pay As You Go 로 바뀌면 완료).

### 자동 재시도 스크립트 요점 (OCI CLI)

- 인증: API 키 방식(`~/.oci/config` + PEM). 세션 토큰과 달리 만료 없어 장시간 재시도에 적합.
- 루프에서 `oci compute instance launch` 반복. **재시도 판정을 화이트리스트(치명 오류만 중단)로** 짤 것 — capacity 외에도 `429 TooManyRequests`, 네트워크 `timed out`, `5xx` 가 수시로 섞여 나온다. 이것들을 "예상 못한 에러"로 처리하면 스크립트가 조기 중단됨.
- 인증/파라미터/실제 서비스 한도(`NotAuthenticated`, `InvalidParameter`, `reached your service limit`)만 중단.
- 간격 90~120초 권장. 너무 촘촘하면 429 유발.
- 스크립트·설정 위치: `~/.oci/freevm/` (repo 밖 로컬 전용).

## 관련 맥락

- SSH 키 관리·접속 도구는 `wiki/patterns/ssh-cli-toolkit-essentials.md` 참고
- 클라우드 DB 가 필요하면 Supabase(`wiki/patterns/supabase-region-migration.md`) 등 BaaS 대안도 검토

## 변경 이력

- 2026-06-21: 최초 생성 (출처: Oracle Cloud Free Tier 조사 세션). 수치는 2026-01 기준 지식이며 가입 직전 공식 페이지 재확인 권장 (confidence: medium)
- 2026-07-10: 실제 도쿄 계정으로 A1.Flex(4 OCPU/24GB) 확보·셋업 완료. "실전 기록 — 도쿄 재고 품귀와 PAYG 해결" 섹션 추가, iptables 규칙 순서 함정 추가, confidence high 로 상향 (출처: 20260705~10 실행 세션)
- 2026-07-12: v1(llm_wiki)에서 이관. domain personal → ai-agent 재분류 (인프라·자동화 호스팅 용도)
