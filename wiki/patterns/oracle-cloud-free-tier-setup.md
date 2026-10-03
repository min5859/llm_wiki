---
title: "Oracle Cloud Free Tier — 무료 VM 가입·설치 가이드"
domain: "ai-agent"
sensitivity: public
tags: ["pattern", "oracle-cloud", "oci", "free-tier", "always-free", "iaas", "ubuntu", "arm", "ampere", "ssh", "payg", "out-of-capacity", "oci-cli", "boot-volume", "resize"]
created: 2026-06-21
updated: "2026-10-03"
sources:
  - "대화 세션 20260621 (Oracle Cloud Free Tier 조사)"
  - "대화 세션 20260705~10 (실제 가입·A1 인스턴스 확보·셋업)"
  - "2026-10-03 OCI API 및 SSH 읽기 전용 점검"
  - "https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm (2026-10-03 확인)"
  - "https://www.oracle.com/cloud/price-list/ (2026-10-03 확인)"
  - "https://docs.oracle.com/en-us/iaas/Content/Block/Tasks/update-online-resize-block-boot-volume.htm (2026-10-03 확인)"
  - "https://docs.oracle.com/en-us/iaas/Content/Block/Tasks/rescanningdisk.htm (2026-10-03 확인)"
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

아래는 과거 조사 당시의 한도 기록이다. 새 인스턴스 생성에는 뒤의
「무료 서버 수와 계정별 기준」을 적용한다. 특히 A1 4 OCPU/24GB를 모든 계정의
현재 무료 한도로 간주하지 않는다.

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
3. **Shape**: `VM.Standard.A1.Flex` 선택 → 계정에 적용되는 무료 사용량과 서비스 한도를 확인한 뒤 OCPU/메모리 선택
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

아래 경험과 당시 설명은 2026-07 기록으로 보존한다. 현재 CPU/RAM 무료 적용과
청구 여부는 최신 공식 조건과 계정의 사용량·청구 화면으로 확인한다.

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

## 무료 서버 수와 계정별 기준 (2026-10-03 확인)

무료 서버가 계정당 한 대로 제한되는 것은 아니다. A1은 CPU·RAM 무료 사용량을
여러 VM에 분배하며, AMD E2.1.Micro는 최대 두 대라는 별도 기준이 있다.
스토리지는 홈 리전의 부팅·블록 볼륨 합산 200GB를 공유한다.
[Always Free 공식 안내](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm)

| 계정 구분 | 현재 공식 문서의 A1 무료 사용량 |
|---|---|
| Always Free 전용 | 월 1,500 OCPU-hours / 9,000 GB-hours, 상시 2 OCPU/12GB 상당, A1 한두 VM 분할 안내 |
| 유료 tenancy | 월 3,000 OCPU-hours / 18,000 GB-hours를 VM 등에 합산 적용 |

유료 tenancy의 기준은 [Oracle 가격표](https://www.oracle.com/cloud/price-list/)에서
확인했다. 각 VM에 한도가 새로 생기는 방식이 아니다. 예를 들어 이 기준이 적용되면
2 OCPU/12GB 두 VM은 4 OCPU/24GB 한 VM과 총 사용량이 같지만, 4 OCPU/24GB VM을
한 대 더 상시 사용하면 무료 사용량을 넘을 수 있다.

실제 생성에는 계정 한도, 리전 재고, 부팅 디스크 여유도 필요하다. 기존 서버의 할당량은
무료 청구의 증거가 아니므로 생성 전 Limits, Quotas and Usage와 청구 화면을 확인한다.

## 기존 OCI 서버 무료 디스크 증설 (2026-10-03 조사)

현재 API/SSH 점검 결과:

- 홈 리전: `ap-tokyo-1` (도쿄)
- 서버: `freevm-arm`, A1 4 OCPU/24GB 한 대
- 부팅 볼륨: `freevm-arm (Boot Volume)`, API `size-in-gbs=47`
- 다른 블록 볼륨·하위 compartment: 없음
- 성능: Balanced, 10 VPU/GB
- Ubuntu 루트: `/dev/sda1`, ext4, 약 45GiB 중 17GiB 사용·28GiB 여유
- `growpart`, `resize2fs` 설치됨

기존 부팅 볼륨을 온라인 확장하면 새 볼륨이나 마운트 경로 없이 루트 용량을 늘릴 수
있다. 100/150GB는 다른 VM의 부팅 디스크 여유를 남기고, 200GB는 이 서버에 전체
무료 스토리지를 할당한다. 같은 볼륨은 나중에 축소할 수 없지만, 계속 사용할 서버라면
굳이 줄일 필요는 없다. 실제 변경 직전에 전체 볼륨 합계를 재확인한다.
[온라인 확장](https://docs.oracle.com/en-us/iaas/Content/Block/Tasks/update-online-resize-block-boot-volume.htm),
[크기 변경 제약](https://docs.oracle.com/en-us/iaas/Content/Block/Tasks/resizingavolume.htm)

### 증설 순서

1. 목표 용량과 임시 볼륨 백업 사용 여부를 결정한다. 백업은 변경 실패 시 복구용이며,
   증설한 볼륨을 줄이는 수단은 아니다.
2. 03:00/04:00/05:00 KST 게시 파이프라인이 끝난 뒤 진행한다.
3. OCI 콘솔의 Block Storage → Boot Volumes → 해당 볼륨 → Edit에서 크기를
   늘리고 기존 성능 설정은 유지한다.
4. 볼륨 확장이 완료되면 서버에서 장치를 다시 확인하고 rescan한다.

```bash
findmnt /
lsblk -b -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
sudo dd iflag=direct if=/dev/sda of=/dev/null count=1
echo 1 | sudo tee /sys/class/block/sda/device/rescan
lsblk -b -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
```

이 명령은 조회 당시의 `/dev/sda` 기준이다. 실행 때 장치가 다르면 그대로 사용하지
않는다. 전체 디스크 크기가 늘어난 것을 확인한 뒤에만 다음 단계로 진행한다.
[Oracle rescan 절차](https://docs.oracle.com/en-us/iaas/Content/Block/Tasks/rescanningdisk.htm)

5. 루트 파티션 1과 ext4를 확장한다. EFI의 `sda15`, `/boot`의 `sda16`은 유지한다.

```bash
sudo growpart -N /dev/sda 1   # 변경 예정 내용만 확인
sudo growpart /dev/sda 1
sudo resize2fs /dev/sda1
df -h /
systemctl list-timers dev-blog.timer research-wiki.timer oss-radar.timer
```

`growpart`는 파티션을, `resize2fs`는 파일시스템을 늘린다. 중간 오류가 나면 멈추고
인식 상태를 확인한다. 기존 파티션을 포맷하거나 강제로 재생성하지 않는다.
[Ubuntu growpart](https://manpages.ubuntu.com/manpages/noble/man1/growpart.1.html),
[Ubuntu resize2fs](https://manpages.ubuntu.com/manpages/noble/man8/resize2fs.8.html)

### 조사 및 실행 상태

- 2026-10-03: 볼륨·서버 조회와 방법 조사 완료. **실제 증설은 미실행**.
- OCI 운영 계획 원본:
  `/home/ubuntu/wiki/40 Projects/OCI 무료 스토리지 증설 계획.md`
- 실제 변경 시 전후 볼륨/파일시스템 크기, 무료 합계, 다음 게시 결과와 백업 삭제
  여부를 기록한다. 자격증명이나 private key는 문서에 넣지 않는다.

## 관련 맥락

- SSH 키 관리·접속 도구는 `wiki/patterns/ssh-cli-toolkit-essentials.md` 참고
- 클라우드 DB 가 필요하면 Supabase(`wiki/patterns/supabase-region-migration.md`) 등 BaaS 대안도 검토

## 변경 이력

- 2026-06-21: 최초 생성 (출처: Oracle Cloud Free Tier 조사 세션). 수치는 2026-01 기준 지식이며 가입 직전 공식 페이지 재확인 권장 (confidence: medium)
- 2026-07-10: 실제 도쿄 계정으로 A1.Flex(4 OCPU/24GB) 확보·셋업 완료. "실전 기록 — 도쿄 재고 품귀와 PAYG 해결" 섹션 추가, iptables 규칙 순서 함정 추가, confidence high 로 상향 (출처: 20260705~10 실행 세션)
- 2026-07-12: v1(llm_wiki)에서 이관. domain personal → ai-agent 재분류 (인프라·자동화 호스팅 용도)
- 2026-10-03: 현재 OCI API/SSH 점검과 무료 디스크 증설 절차 추가. 과거 4 OCPU/24GB 기록과 현재 계정별 기준을 구분하고, 복수 VM의 CPU/RAM·스토리지 공유 한도와 증설 미실행 상태 기록
