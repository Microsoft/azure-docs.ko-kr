---
title: Azure Site Recovery의 새로운 기능
description: Azure Site Recovery 서비스의 새 기능 및 최신 업데이트에 대 한 요약 정보를 제공 합니다.
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 837344d10e21d20488760f6d5aa4749c039f9b07
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816952"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery의 새로운 기능

[Azure Site Recovery](site-recovery-overview.md) 서비스는 지속적으로 업데이트되고 개선됩니다. 서비스를 최신 상태로 유지할 수 있도록 이 문서에서는 최신 릴리스, 새 기능 및 새 콘텐츠에 대한 정보를 제공합니다. 이 페이지는 정기적으로 업데이트됩니다.

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 채널에서 Site Recovery 업데이트 알림을 수행 하 고 구독할 수 있습니다.

## <a name="supported-updates"></a>지원 되는 업데이트

Site Recovery 구성 요소의 경우 N-4 버전을 지원 합니다. 여기서 N은 최신 릴리스 버전입니다. 각각은 다음 표에 요약되어 있습니다.

**Update** |  **통합 설치** | **구성 서버 ova** | **모바일 서비스 에이전트** | **Site Recovery Provider** | **Recovery Services 에이전트**
--- | --- | --- | --- | --- | ---
[롤업 49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0
[롤업 48](https://support.microsoft.com/help/4573888/) | 9.35.5659.1 | 5.1.6200.0 | 9.35.5659.1 | 5.1.6200.0 | 2.0.9186.0
[롤업 47](https://support.microsoft.com/help/4570609/) | 9.34.5634.1 | 5.1.6100.0 | 9.34.5634.1 | 5.1.6100.0 | HyperV 컴퓨터-2.0.9183.0 <br> VMware 컴퓨터-2.0.9177.0
[롤업 46](https://support.microsoft.com/help/4564347/) | 9.33.5598.1 | 5.1.5900.0 | 9.33.5598.1 | 5.1.5900.0 | 2.0.9175.0
[롤업 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0

업데이트 설치 및 지원에 [대해 자세히 알아보세요](service-updates-how-to.md) .


## <a name="updates-august-2020"></a>업데이트 (8 월 2020)

### <a name="update-rollup-49"></a>업데이트 롤업 49

[업데이트 롤업 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 설명 된 대로 Site Recovery 에이전트 및 공급자를 업데이트 합니다.
**문제 해결/향상** | 롤업에 자세히 설명 된 다양 한 수정 사항 및 개선 사항입니다.

## <a name="updates-july-2020"></a>업데이트 (7 월 2020)

### <a name="update-rollup-48"></a>업데이트 롤업 48

[업데이트 롤업 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 설명 된 대로 Site Recovery 에이전트 및 공급자를 업데이트 합니다.
**문제 해결/향상** | 롤업에 자세히 설명 된 다양 한 수정 사항 및 개선 사항입니다.

> [!NOTE]
> 업데이트 롤업 48에는 ADE를 사용 하 여 암호화 된 Linux 컴퓨터에 대해 복제를 사용 하도록 설정 하는 알려진 문제가 있습니다. [자세히 알아보기](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137).

### <a name="update-rollup-47"></a>업데이트 롤업 47

[업데이트 롤업 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 설명 된 대로 Site Recovery 에이전트 및 공급자를 업데이트 합니다.
**문제 해결/향상** | 롤업에 자세히 설명 된 다양 한 수정 사항 및 개선 사항입니다.

## <a name="updates-june-2020"></a>업데이트 (6 월 2020)

### <a name="update-rollup-46"></a>업데이트 롤업 46

[업데이트 롤업 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 설명 된 대로 Site Recovery 에이전트 및 공급자를 업데이트 합니다.
**문제 해결/향상** | 롤업에 자세히 설명 된 다양 한 수정 사항 및 개선 사항입니다.

## <a name="updates-march-2020"></a>업데이트 (3 월 2020)

### <a name="update-rollup-45"></a>업데이트 롤업 45

[업데이트 롤업 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | 롤업에 설명 된 대로 Site Recovery 에이전트 및 공급자를 업데이트 합니다.
**문제 해결/향상** | 롤업에 자세히 설명 된 다양 한 수정 사항 및 개선 사항입니다.

## <a name="updates-january-2020"></a>업데이트 (1 월 2020)

### <a name="update-rollup-44"></a>업데이트 롤업 44

[업데이트 롤업 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 공급자 및 에이전트에 대 한 업데이트가 없습니다.
**문제 해결/향상** | 롤업에 자세히 설명 된 다양 한 수정 사항 및 개선 사항입니다.

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware 재해 복구

Azure virtual machines는 이제 고객이 관리 하는 키를 사용 하 여 미사용 암호화를 사용 하도록 설정 하는 Vm을 지원 합니다. [자세히 알아보기](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>업데이트 롤업 43

[업데이트 롤업 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)


## <a name="updates-november-2019"></a>업데이트 (11 월 2019)

### <a name="update-rollup-42"></a>업데이트 롤업 42

[업데이트 롤업 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)


### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

Azure VM 재해 복구에 대 한 새로운 기능이 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**UEFI** | Site Recovery은 이제 UEFI 기반 부팅 아키텍처를 사용 하는 Azure Vm에 대 한 재해 복구를 지원 합니다.
**Linux** | Site Recovery는 이제 Azure Disk Encryption (ADE)를 사용 하 여 Linux를 실행 하는 Azure Vm을 지원 합니다.
**2 세대** | 이제 모든 2 세대 Azure Vm이 재해 복구를 지원 합니다.
**지역** | 이제 노르웨이 지역에서 Azure Vm에 대 한 재해 복구를 사용 하도록 설정할 수 있습니다.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware에서 Azure로 재해 복구

VMware에서 Azure로의 재해 복구에 대 한 새로운 기능이 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**UEFI** | 이제 Site Recovery에서는 UEFI 기반 부팅 아키텍처를 사용 하 여 VMware Vm에 대 한 재해 복구를 지원 합니다.<br/><br/> 지원 되는 운영 체제에는 Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8이 포함 됩니다.

## <a name="update-to-servicing-stack-updatesha-2"></a>서비스 스택 업데이트/s h a-2로 업데이트

Azure Vm을 보조 지역 또는 온-프레미스 VMware Vm 또는 물리적 서버에서 Azure로 재해 복구 하는 경우 다음에 유의 하세요.

- 모바일 서비스 확장 (Azure Vm의 경우) 및 모바일 서비스 에이전트 (VMware/물리적 컴퓨터의 경우)의 버전 9.30.5407.1에서 일부 컴퓨터 운영 체제는 서비스 스택 업데이트 및 s h a-2를 실행 해야 합니다. 세부 정보는 아래 표에 나와 있습니다.
- 연결 된 KB에 따라 업데이트 및 s h a-2를 설치 합니다. SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다.
- [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.

**운영 체제** | **Azure VM** | **VMware v m/물리적 컴퓨터**
--- | --- | ---
**Windows 2008 R2 SP1** | [서비스 스택 업데이트](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [서비스 스택 업데이트](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [서비스 스택 업데이트](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [서비스 스택 업데이트](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [서비스 스택 업데이트](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [서비스 스택 업데이트](https://support.microsoft.com/help/4490628)<br/> [S h a-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>업데이트 (10 월 2019)

### <a name="update-rollup-41"></a>업데이트 롤업 41

[업데이트 롤업 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)



### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

Azure VM 재해 복구에 대 한 새로운 기능이 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**테스트 장애 조치 (failover) 설정** | 테스트 장애 조치 (failover)를 설정할 때 이제 IP 주소, NSG, 내부 부하 분산 및 각 컴퓨터 NIC의 공용 IP 주소를 포함 하 여 테스트 장애 조치 (failover) VM 및 네트워크에 대 한 설정을 구성할 수 있습니다. 이러한 설정은 선택 사항이 며 현재 동작을 변경 하지 않습니다. 이러한 설정을 구성 하지 않으면 테스트 장애 조치 (failover) 시 Azure VNet을 선택할 수 있습니다. [자세히 알아보기](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**복구 계획** | 이제 복구 계획이 장애 조치 (failover) 안정성을 보장 하기 위해 100 Vm으로 제한 됩니다.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware에서 Azure로 재해 복구

VMware에서 Azure로의 재해 복구에 대 한 새로운 기능이 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**복구 계획** | 이제 복구 계획이 장애 조치 (failover) 안정성을 보장 하기 위해 100 Vm으로 제한 됩니다.


## <a name="updates-september-2019"></a>업데이트 (9 월 2019)

### <a name="update-rollup-40"></a>업데이트 롤업 40

[업데이트 롤업 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)




### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

Azure VM 재해 복구에 대 한 새로운 기능이 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**장애 복구 후 정리** | 보조 Azure로 장애 조치 (failover) 한 후 주 지역으로 장애 복구 (failback) 한 후 보조 지역에서 자동으로 컴퓨터를 정리 Site Recovery 합니다. Vm 및 Nic를 수동으로 삭제할 필요가 없습니다.
**테스트 장애 조치 (failover)에서 IP 주소 유지** | 이제 재해 복구 훈련 중에 원본 VM의 IP 주소를 유지 하 고 테스트 장애 조치 (failover)에 대 한 고정 IP 주소를 선택할 수 있습니다.

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
새 프로세스 서버 경고 | 새 프로세스 서버 경고가 추가 되었습니다. [자세히 알아보기](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Hyper-v 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
스토리지 계정 | Site Recovery은 이제 Hyper-v에서 Azure로 재해 복구를 위해 방화벽을 사용 하도록 설정 된 저장소 계정 사용을 지원 합니다.  방화벽 사용 저장소 계정을 대상 계정 또는 캐시 저장소로 선택할 수 있습니다. 방화벽이 사용 되는 계정을 사용 하는 경우 신뢰할 수 있는 Microsoft 서비스를 허용 하는 옵션을 사용 하도록 설정 해야 합니다.<br/><br/> 이는 System Center VMM이 있거나 없는 Hyper-v Vm에 대해 지원 됩니다.


## <a name="updates-august-2019"></a>업데이트 (8 월 2019)

### <a name="update-rollup-39"></a>업데이트 롤업 39

[업데이트 롤업 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)


### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

Azure VM 재해 복구에 대 한 새로운 기능이 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Azure AD가 없는 암호화** | 이제 Azure AD 앱이 없는 암호화가 Windows를 실행 하는 관리 디스크에 대 한 Azure VM 복제에 지원 됩니다.
**장애 조치 (failover) 용 네트워크 리소스** | 이제 다른 지역으로 장애 조치 (failover) 할 때 네트워크 리소스 설정 (NSGs, 부하 분산, 공용 IP 주소)을 VM에 연결할 수 있습니다.

## <a name="updates-july-2019"></a>업데이트 (7 월 2019)

### <a name="update-rollup-38"></a>업데이트 롤업 38

[업데이트 롤업 38](https://support.microsoft.com/help/4513507/) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)


### <a name="general"></a>일반

Site Recovery은 이제 캐시 저장소 또는 대상 저장소에 범용 v2 저장소 계정을 사용할 수 있도록 지원 합니다. 이전에는 v1만 지원 되었습니다.

### <a name="vmware-to-azure-disaster-recovery"></a>VMware에서 Azure로 재해 복구

이제 관리 디스크를 사용 하 여 Azure VM에 복제할 때 디스크를 최대 8TB까지 복제할 수 있습니다.


## <a name="updates-june-2019"></a>업데이트 (6 월 2019)

### <a name="update-rollup-37"></a>업데이트 롤업 37

[업데이트 롤업 37](https://support.microsoft.com/help/4508614/) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**GPT 파티션** | 업데이트 롤업 37 이후로 (모바일 서비스 버전 9.25.5241.1) UEFI에서 최대 5 개의 GPT 파티션이 지원 됩니다. 이 업데이트 전에 4 개가 지원 됩니다.



## <a name="updates-may-2019"></a>업데이트 (2019 년 5 월)

### <a name="update-rollup-36"></a>업데이트 롤업 36

[업데이트 롤업 36](https://support.microsoft.com/help/4503156) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트와 공급자에 대 한 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**추가 된 디스크 복제** | 이미 재해 복구를 사용 하도록 설정 된 Azure VM에 추가 된 데이터 디스크에 대해 복제를 사용 하도록 설정 합니다. [자세히 알아보기](azure-to-azure-enable-replication-added-disk.md).
**자동 업데이트** | 재해 복구를 위해 사용 하도록 설정 된 Azure Vm에서 실행 되는 모바일 서비스 확장에 대 한 자동 업데이트를 구성 하는 경우, 이제 Site Recovery에서 만든 기본 계정을 사용 하는 대신 사용할 기존 automation 계정을 선택할 수 있습니다. [자세히 알아보기](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**프로세스 서버 모니터링** | 온-프레미스 VMware Vm 및 물리적 서버의 재해 복구를 위해 향상 된 서버 상태 보고 및 경고와 함께 프로세스 서버 문제를 모니터링 하 고 문제를 해결 합니다. [자세히 알아보기](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>업데이트 (3 월 2019)

### <a name="update-rollup-35"></a>업데이트 롤업 35

[업데이트 롤업 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트와 공급자에 대 한 업데이트 (롤업에 설명 된 대로)
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**관리되는 디스크** | 온-프레미스 VMware Vm 및 물리적 서버 복제는 이제 Azure의 관리 디스크에 직접 복제 됩니다. 온-프레미스 데이터는 Azure의 캐시 저장소 계정으로 전송 되 고, 복구 지점은 대상 위치의 관리 디스크에 생성 됩니다. 이렇게 하면 여러 대상 저장소 계정을 관리할 필요가 없습니다.
**구성 서버** | Site Recovery는 이제 여러 Nic가 있는 구성 서버를 지원 합니다. 자격 증명 모음에 구성 서버를 등록 하기 전에 구성 서버 VM에 어댑터를 추가 합니다. 나중에를 추가 하는 경우 자격 증명 모음에서 서버를 다시 등록 해야 합니다.


## <a name="updates-february-2019"></a>업데이트 (2 월 2019)

### <a name="update-rollup-34"></a>업데이트 롤업 34

[업데이트 롤업 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세히 설명 됨)에 대 한 업데이트입니다.
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명).


### <a name="update-rollup-33"></a>업데이트 롤업 33

[업데이트 롤업 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세히 설명 됨)에 대 한 업데이트입니다.
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명).


### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**네트워크 매핑** | 이제 Azure VM 재해 복구의 경우 복제를 사용 하도록 설정할 때 사용 가능한 모든 대상 네트워크를 사용할 수 있습니다.
**표준 SSD** | 이제 [표준 SSD 디스크](../virtual-machines/disks-types.md#standard-ssd)를 사용 하 여 Azure vm에 대 한 재해 복구를 설정할 수 있습니다.
**스토리지 공간 다이렉트** | 고가용성을 위해 [스토리지 공간 다이렉트](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 를 사용 하 여 Azure VM 앱에서 실행 되는 앱에 대 한 재해 복구를 설정할 수 있습니다.  Site Recovery와 함께 S2D (스토리지 공간 다이렉트)를 사용 하면 Azure VM 워크 로드의 포괄적인 보호 기능을 제공 합니다. S2D를 사용 하면 Azure에서 게스트 클러스터를 호스트할 수 있습니다. 이 기능은 VM이 SAP ASCS 계층, SQL Server 또는 스케일 아웃 파일 서버와 같은 중요 한 응용 프로그램을 호스트 하는 경우에 특히 유용 합니다.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux BRTFS 파일 시스템** | Site Recovery는 이제 BRTFS 파일 시스템을 사용 하 여 VMware Vm을 복제 하도록 지원 합니다. 다음과 같은 경우 복제가 지원 되지 않습니다.<br/><br/>-복제를 사용 하도록 설정한 후 BTRFS 파일 시스템 하위 볼륨이 변경 됩니다.<br/><br/>-파일 시스템이 여러 디스크에 분산 되어 있습니다.<br/><br/>-BTRFS 파일 시스템은 RAID를 지원 합니다.
**Windows Server 2019** | Windows Server 2019를 실행 하는 컴퓨터에 대 한 지원이 추가 되었습니다.


## <a name="updates-january-2019"></a>업데이트 (1 월 2019)


### <a name="accelerated-networking-azure-vms"></a>가속화 네트워킹 (Azure Vm)

가속화 된 네트워킹을 사용 하면 VM에 대 한 단일 루트 i/o 가상화 (SR-IOV)를 사용 하 여 네트워킹 성능을 향상 시킬 수 있습니다. Azure VM에 복제를 사용하도록 설정하면 Site Recovery는 가속화된 네트워킹의 사용 여부를 감지합니다. 사용되는 경우 장애 조치(failover) 후 Site Recovery는 자동으로 [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 및 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)의 대상 복제본 Azure VM에서 가속화된 네트워킹을 구성합니다.

[자세히 알아보기](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>업데이트 롤업 32

[업데이트 롤업 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세히 설명 됨)에 대 한 업데이트입니다.
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명).

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | RedHat 워크스테이션 6/7 및 Ubuntu, Debian 및 SUSE에 대 한 새 커널 버전에 대 한 지원이 추가 되었습니다.
**스토리지 공간 다이렉트** | Site Recovery는 S2D (스토리지 공간 다이렉트)를 사용 하 여 Azure Vm을 지원 합니다.

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware v m/물리적 서버 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | Redhat Enterprise Linux 7.6, RedHat 워크스테이션 6/7, Oracle Linux 6.10 및 Oracle Linux 7.6, Ubuntu, Debian 및 SUSE에 대 한 새 커널 버전에 대 한 지원이 추가 되었습니다.


### <a name="update-rollup-31"></a>업데이트 롤업 31

[업데이트 롤업 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세히 설명 됨)에 대 한 업데이트입니다.
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware v m/물리적 서버 복제

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** |  Red Hat 호환 커널 및 UEK (Unbreakable Enterprise Kernel) 릴리스 5에 대 한 Oracle Linux 6.8, Oracle Linux 6.9 및 Oracle Linux 7.0에 대 한 지원이 추가 되었습니다.
**LVM** | LVM 및 LVM2 볼륨에 대 한 지원이 추가 되었습니다.<br/><br/> 디스크 파티션 및 LVM 볼륨의/boot 디렉터리가 이제 지원 됩니다.
**디렉터리로** | 별도의 파티션으로 설정 된 이러한 디렉터리나 동일한 시스템 디스크에 없는 파일 시스템에 대 한 지원이 추가 되었습니다.<br/><br/> /(root),/boot,/usr,/usr/local,/var,/etc
**Windows Server 2008** | 동적 디스크에 대 한 지원이 추가 되었습니다.
**장애 조치(Failover)** | Storvsc 및 vsbus가 부팅 드라이버가 아닌 VMware Vm에 대 한 장애 조치 (failover) 시간이 개선 되었습니다.
**UEFI 지원** | Azure Vm은 부팅 유형 UEFI를 지원 하지 않습니다. 이제 Site Recovery를 사용 하 여 UEFI를 사용 하 여 온-프레미스 물리적 서버를 Azure로 마이그레이션할 수 있습니다. 마이그레이션 전에 부팅 유형을 BIOS로 변환 하 여 서버를 Site Recovery 마이그레이션합니다. Site Recovery 이전에 Vm에 대해서만이 변환을 지원 했습니다. Windows Server 2012 이상을 실행 하는 물리적 서버에 대 한 지원이 제공 됩니다.

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | Red Hat 호환 커널 및 UEK (Unbreakable Enterprise Kernel) 릴리스 5에 대 한 Oracle Linux 6.8, Oracle Linux 6.9 및 Oracle Linux 7.0에 대 한 지원이 추가 되었습니다.
**Linux BRTFS 파일 시스템** | Azure Vm에 대해 지원 됩니다.
**가용성 영역의 Azure Vm** | 가용성 영역에 배포 된 Azure Vm에 대해 다른 지역으로 복제를 사용 하도록 설정할 수 있습니다. 이제 Azure VM에 대한 복제를 설정하고, 단일 VM 인스턴스, 가용성 집합의 VM 또는 가용성 영역의 VM으로의 장애 조치(failover) 대상을 설정할 수 있습니다. 이 설정은 복제에 영향을 주지 않습니다. 공지를 [읽어보세요](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/).
**방화벽 사용 저장소 (포털/PowerShell)** | [방화벽 사용 저장소 계정](../storage/common/storage-network-security.md)에 대 한 지원이 추가 되었습니다.<br/><br/> 재해 복구를 위해 방화벽 사용 저장소 계정에서 관리 되지 않는 디스크를 사용 하 여 Azure Vm을 다른 Azure 지역에 복제할 수 있습니다.<br/><br/> 방화벽 사용 저장소 계정을 관리 되지 않는 디스크에 대 한 대상 저장소 계정으로 사용할 수 있습니다.<br/><br/> 포털에서 지원 되며 PowerShell을 사용 합니다.

## <a name="updates-december-2018"></a>업데이트 (12 월 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>모바일 서비스 (Azure Vm)에 대 한 자동 업데이트

Site Recovery는 모바일 서비스 확장의 자동 업데이트 옵션을 추가했습니다. 모바일 서비스 확장은 Site Recovery에서 복제한 각 Azure VM에 설치됩니다. 복제를 사용하도록 설정할 때 Site Recovery에서 확장 업데이트를 관리하도록 허용할 것인지 여부를 선택합니다.

업데이트 후 VM을 다시 시작할 필요가 없으며 복제에 영향을 주지 않습니다. [자세히 알아보기](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 재해 복구의 새 가격 책정 계산기

Azure Vm의 재해 복구는 VM 라이선스 비용, 네트워크 및 저장소 비용을 발생 시킵니다. Azure는 이러한 비용을 계산할 수 있는 [가격 책정 계산기](https://aka.ms/a2a-cost-estimator)를 제공합니다. 이제 Site Recovery는 표준 HDD 디스크 12개와 프리미엄 SSD 디스크 6개가 포함된 VM을 사용하는 3계층 앱을 기반으로 샘플 배포의 가격을 정하는 [가격 견적 예제](https://aka.ms/a2a-cost-estimator)를 제공합니다.

- 이 샘플에서는 표준에 대해 10gb의 데이터 변경 및 premium의 경우 20gb로 가정 합니다.
- 특정 배포에 맞게 변수를 예상 비용으로 변경할 수 있습니다.
- VM 수, 관리 디스크의 수 및 유형, VM에서 예상되는 총 데이터 변경률을 지정할 수 있습니다.
- 또한 압축 비율을 적용하여 대역폭 비용을 예측할 수 있습니다.

공지를 [읽어보세요](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).


## <a name="updates-october-2018"></a>업데이트 (10 월 2018)

### <a name="update-rollup-30"></a>업데이트 롤업 30

[업데이트 롤업 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세히 설명 됨)에 대 한 업데이트입니다.
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명).

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구
이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**지역 지원** | 오스트레일리아 중부 1 및 오스트레일리아 중부 2에 대 한 Site Recovery 지원이 추가 되었습니다.
**디스크 암호화 지원** | Azure AD 앱으로 Azure Disk Encryption (ADE)로 암호화 된 Azure Vm의 재해 복구를 위한 지원이 추가 되었습니다. [자세히 알아보기](azure-to-azure-how-to-enable-replication-ade-vms.md).
**디스크 제외** | 초기화 되지 않은 디스크는 이제 Azure VM을 복제 하는 동안 자동으로 제외 됩니다.
**방화벽 사용 저장소 (PowerShell)** | [방화벽 사용 저장소 계정](../storage/common/storage-network-security.md)에 대 한 지원이 추가 되었습니다.<br/><br/> 재해 복구를 위해 방화벽 사용 저장소 계정에서 관리 되지 않는 디스크를 사용 하 여 Azure Vm을 다른 Azure 지역에 복제할 수 있습니다.<br/><br/> 방화벽 사용 저장소 계정을 관리 되지 않는 디스크에 대 한 대상 저장소 계정으로 사용할 수 있습니다.<br/><br/> PowerShell을 사용 하는 경우에만 지원 됩니다.


### <a name="update-rollup-29"></a>업데이트 롤업 29

[업데이트 롤업 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) 는 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세히 설명 됨)에 대 한 업데이트입니다.
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명).


## <a name="updates-august-2018"></a>업데이트 (8 월 2018)

### <a name="update-rollup-28"></a>업데이트 롤업 28

[업데이트 롤업 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세히 설명 됨)에 대 한 업데이트입니다.
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명).

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구
이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | RedHat Enterprise Linux 6.10에 대해 지원 됨 CentOS 6.10.<br/><br/>
**클라우드 지원** | 독일 클라우드의 Azure Vm에 대해 지원 되는 재해 복구.
**구독 간 재해 복구** | 동일한 Azure Active Directory 테 넌 트 내에서 다른 구독의 Azure Vm을 다른 지역으로 복제 하는 기능을 지원 합니다. [자세히 알아보기](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware v m/물리적 서버 재해 복구
이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | RedHat Enterprise Linux 6.10, CentOS 6.10에 대 한 지원이 추가 되었습니다.<br/><br/> 기존 BIOS 호환성 모드에서 GPT (GUID 파티션 테이블) 파티션 스타일을 사용 하는 Linux 기반 Vm이 이제 지원 됩니다. 자세한 내용은 [AZURE VM FAQ](../virtual-machines/faq-for-disks.md) 를 참조 하세요.
**마이그레이션 후 Vm에 대 한 재해 복구** | 복제를 사용 하도록 설정 하기 전에 VM에서 모바일 서비스를 제거할 필요 없이 온-프레미스 VMware VM을 Azure로 마이그레이션하는 보조 지역에 대 한 재해 복구를 사용 하도록 지원 합니다.
**Windows Server 2008** | Windows Server 2008 R2/2008 64 비트 및 32 비트를 실행 하는 컴퓨터를 마이그레이션하기 위한 지원<br/><br/> 마이그레이션만 (복제 및 장애 조치). 장애 복구는 지원 되지 않습니다.

## <a name="updates-july-2018"></a>업데이트 (7 월 2018)

### <a name="update-rollup-27-july-2018"></a>업데이트 롤업 27 (7 월 2018)

[업데이트 롤업 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) 은 다음과 같은 업데이트를 제공 합니다.

**Update** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세히 설명 됨)에 대 한 업데이트입니다.
**문제 해결/향상** | 다양 한 수정 및 개선 사항 (롤업에 자세히 설명).

### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | Red Hat Enterprise Linux 7.5에 대 한 지원이 추가 되었습니다.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware v m/물리적 서버 재해 복구

이번 달에 추가 된 기능은 표에 요약 되어 있습니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12에 대 한 지원이 추가 되었습니다.



## <a name="next-steps"></a>다음 단계

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 페이지에서 업데이트를 항상 최신 상태로 유지하세요.