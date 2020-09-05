---
title: Azure Site Recovery를 사용한 Azure VM 재해 복구에 대한 지원 매트릭스
description: Azure Site Recovery를 사용한 보조 지역으로 Azure VM 재해 복구에 대한 지원을 요약합니다.
ms.topic: article
ms.date: 07/14/2020
ms.author: raynew
ms.openlocfilehash: 5fc01efc386a950cdc53e7928009c67a355962e4
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182267"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure 지역 간 Azure VM 재해 복구에 대한 지원 매트릭스

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 한 Azure 지역에서 다른 지역으로의 Azure VM 재해 복구를 위한 지원 및 필수 구성 요소를 요약합니다.


## <a name="deployment-method-support"></a>배포 방법 지원 여부

**배포** |  **지원**
--- | ---
**Azure Portal** | 지원됩니다.
**PowerShell** | 지원됩니다. [자세히 알아보기](azure-to-azure-powershell.md)
**REST API** | 지원됩니다.
**CLI** | 현재 지원되지 않음


## <a name="resource-support"></a>리소스 지원

**리소스 작업** | **세부 정보**
--- | ---
**리소스 그룹 간 자격 증명 모음 이동** | 지원되지 않음
**리소스 그룹 간에 계산/스토리지/네트워크 리소스 이동** | 지원되지 않습니다.<br/><br/> VM 복제 후 VM 또는 스토리지/네트워크와 같은 관련 구성 요소를 이동하는 경우 VM에 대한 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
**재해 복구를 위해 한 구독에서 다른 구독으로 Azure VM 복제** | 동일한 Azure Active Directory 테넌트 내에서 지원됩니다.
**지원되는 지역별 클러스터 내의 여러 지역 간에 VM 마이그레이션(구독 내/구독 간)** | 동일한 Azure Active Directory 테넌트 내에서 지원됩니다.
**동일한 지역 내에서 VM 마이그레이션** | 지원되지 않습니다.

## <a name="region-support"></a>지역 지원

동일한 지리적 클러스터 내의 두 지역 간에 VM을 복제 및 복구할 수 있습니다. 지리적 클러스터는 데이터 대기 시간 및 주권에 유의해서 정의해야 합니다.


**지리적 클러스터** | **Azure 지역**
-- | --
America | 캐나다 동부, 캐나다 중부, 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중부, 미국 중북부
유럽 | 영국 서부, 영국 남부, 북부 유럽, 유럽 서부, 남아프리카 공화국 서 부, 남아프리카 북부, 노르웨이 동부, 노르웨이 서 부, 프랑스 중부
아시아 | 인도 남부, 인도 중부, 인도 서부, 동남 아시아, 동아시아, 일본 동부, 일본 서부, 한국 중부, 한국 남부
오스트레일리아    | 오스트레일리아 동부, 오스트레일리아 남동부, 오스트레일리아 중부, 오스트레일리아 중부 2
Azure Government    | US Gov 버지니아, US Gov 아이오와, US Gov 애리조나, US Gov 텍사스, US DoD 동부, US DoD 중부
독일    | 독일 중부, 독일 북동부
중국 | 중국 동부, 중국 북부, 중국 북부 2, 중국 동부 2
국내 재해 복구를 위해 예약된 제한된 지역 |독일 중서부용으로 예약된 독일 북부, 스위스 북부용으로 예약된 스위스 서부, 프랑스 중부용으로 예약된 프랑스 남부, 아랍에미리트 북부 고객용으로 제한된 아랍에미리트 중부

>[!NOTE]
>
> - **브라질 남부**의 경우 다음 지역으로 복제 및 장애 조치(failover)할 수 있습니다. 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중북부.
> - 브라질 남부는 VM이 Site Recovery를 사용하여 복제할 수 있는 원본 지역으로만 사용할 수 있습니다. 대상 지역으로 사용할 수는 없습니다. 이는 지리적 거리로 인한 대기 시간 문제 때문입니다. 브라질 남부를 원본 지역으로 대상 지역에 장애 조치(failover)하는 경우 대상 지역에서 브라질 남부로 장애 복구(failback)가 지원됩니다.
> - 적절한 액세스 권한이 있는 지역 내에서 작업을 수행할 수 있습니다.
> - 자격 증명 모음을 만들 지역이 표시되지 않는 경우 구독에 해당 지역에서 리소스를 만들 수 있는 권한이 있는지 확인하세요.
> - 복제를 설정할 때 지리적 클러스터 내에 지역이 표시되지 않는 경우 구독에 해당 지역에서 VM을 만들 수 있는 권한이 있는지 확인하세요.



## <a name="cache-storage"></a>캐시 스토리지

이 표에서는 복제하는 동안 Site Recovery에서 사용하는 캐시 스토리지 계정에 대한 지원을 요약해서 보여 줍니다.

**설정** | **지원** | **세부 정보**
--- | --- | ---
범용 V2 스토리지 계정(핫 및 쿨 계층) | 지원됨 | V2의 트랜잭션 비용은 V1 스토리지 계정보다 훨씬 높기 때문에 GPv2는 사용하지 않는 것이 좋습니다.
Premium Storage | 지원되지 않음 | 표준 스토리지 계정은 캐시 스토리지에 사용되며 비용을 최적화하는 데 도움이 됩니다.
가상 네트워크의 Azure Storage 방화벽  | 지원됨 | 방화벽 지원 캐시 스토리지 계정 또는 대상 스토리지 계정을 사용하는 경우 ['신뢰할 수 있는 Microsoft 서비스 허용'](../storage/common/storage-network-security.md#exceptions)을 선택해야 합니다.<br></br>또한 원본 Vnet의 하나 이상의 서브넷에 대한 액세스를 허용해야 합니다.


## <a name="replicated-machine-operating-systems"></a>복제된 컴퓨터 운영 체제

Site Recovery는 이 섹션에 나열된 운영 체제를 실행하는 Azure VM의 복제를 지원합니다. 이미 복제 중인 머신이 이후 다른 주 커널로 업그레이드(또는 다운그레이드)된 경우에는 복제를 해제하고 업그레이드한 후 복제를 다시 설정해야 합니다.

### <a name="windows"></a>Windows


**운영 체제** | **세부 정보**
--- | ---
Windows Server 2019 | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2016  | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2012 R2 | 지원됩니다.
Windows Server 2012 | 지원됩니다.
Windows Server 2008 R2 SP1/SP2 | 지원됩니다.<br/><br/> Azure VM용 Mobility Service 확장의 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 버전부터 Windows Server 2008 R2 SP1/SP2를 실행하는 머신에서 Windows [SSU(서비스 스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치해야 합니다.  SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.
Windows 10(x64) | 지원됩니다.
Windows 8.1(x64) | 지원됩니다.
Windows 8(x64) | 지원됩니다.
Windows 7(x64) SP1 이상 | Azure VM용 Mobility Service 확장의 [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 버전부터 Windows 7 SP1을 실행하는 머신에서 Windows [SSU(서비스 스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419)를 설치해야 합니다.  SHA-1은 9월 2019에서 지원되지 않으며, SHA-2 코드 서명을 사용하도록 설정하지 않으면 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.



#### <a name="linux"></a>Linux

**운영 체제** | **세부 정보**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2,[7.3, 7.4](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [, 7.5](https://support.microsoft.com/help/4564347/), [7.6, 7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), [7.8, 7.9](https://support.microsoft.com/help/4578241/), 8.0, 8.1, [8.2](https://support.microsoft.com/help/4570609/) 8.2
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3 [, 7.4,](https://support.microsoft.com/help/4564347/) [7.5, 7.6](https://support.microsoft.com/help/4578241/), [7.7, 7.8, 7.9](https://support.microsoft.com/en-us/help/4570609) , 8.0, 8.1, 8.2
Ubuntu 14.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 암호 기반 인증 및 로그인을 사용하고, cloud-init 패키지를 사용하여 클라우드 VM을 구성하는 Ubuntu 서버의 암호 기반 로그인은 장애 조치(failover) 시 사용되지 않도록 설정할 수 있습니다(cloudinit 구성에 따라 다름). Azure Portal에서 장애 조치(failover)된 VM의 지원 > 문제 해결 > 설정 메뉴에서 암호를 재설정하여 암호 기반 로그인을 다시 사용하도록 설정할 수 있습니다.
Ubuntu 18.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines) </br> Ubuntu 18.4.03 (커널 v 5.4)는 [9.36](https://support.microsoft.com/help/4578241/) 에서 지원 됩니다.|
Ubuntu 20.04 LTS 서버 | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | 모든 7에 대 한 지원을 포함 합니다. [지원 되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines) 의 *x* 버전
Debian 8 | 모든 8에 대 한 지원을 포함 합니다. [지원 되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines) 의 *x* 버전
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(지원 되는 커널 버전)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines) </br> (* * SP5는 Azure에서 Azure로의 azure DR 시나리오에 대 한 9.33의 최신 패치를 통해 제공 됩니다.)
SUSE Linux Enterprise Server 15 | 15 및 15 SP1 [(지원되는 커널 버전)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 복제 컴퓨터를 SP3에서 SP4로 업그레이드하는 것은 지원되지 않습니다. 복제된 컴퓨터를 업그레이드한 경우 복제를 사용하지 않도록 설정하고 업그레이드 후에 다시 사용하도록 설정해야 합니다.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, [7.3, 7.4](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) [, 7.5](https://support.microsoft.com/help/4573888/), 7.6, [7.7, 7.8](https://support.microsoft.com/help/4573888/) , 8.0  <br/> Red Hat 호환 커널 또는 UEK(Unbreakable Enterprise Kernel Release) 3, 4 및 5를 실행<br/><br/>8.1<br/>모든 UEK 커널 및 RedHat 커널 <= 3.10.0-1062. *에서의 실행은 [9.36](https://support.microsoft.com/help/4578241/) [9.35](https://support.microsoft.com/help/4573888/) 에서 지원 됩니다.

* * 참고: 릴리스 30 일 이내에 최신 Linux 커널을 지원 하기 위해 Azure Site Recovery 최신 모바일 에이전트 버전을 기반으로 하는 핫 픽스 패치를 롤업 합니다. 이 픽스는 두 주 버전 릴리스 사이에서 롤아웃 됩니다. 최신 버전의 모바일 에이전트 (핫 픽스 패치 포함)로 업데이트 하려면 [이 문서](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)에 설명 된 단계를 따르세요. 이 패치는 현재 Azure에서 Azure로 DR 시나리오에 사용 되는 모바일 에이전트에 대해 출시 되었습니다.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Ubuntu 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | 9.32, [9.33](https://support.microsoft.com/help/4564347/),[9.34](https://support.microsoft.com/help/4570609), [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/)| 3.13.0-24-generic에서 3.13.0-170-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-148-generic<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
|||
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generic to 4.4.0-187-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-제네릭-4.15.0-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-4.15.0-1092-azure |
16.04 LTS | [9.34](https://support.microsoft.com/help/4570609), [9.35](https://support.microsoft.com/help/4573888/) | 4.4.0-21-generic to 4.4.0-184-generic,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-4.15.0-106-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-4.15.0-1089-azure</br> 4.15.0, 4.4.0 & 4.15.0-1091-azure-9.35 핫 픽스 패치 * * |
16.04 LTS | [9.33](https://support.microsoft.com/help/4564347/) | 4.4.0-21-4.4.0-178-제네릭<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-4.15.0-99-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-4.15.0-1082-azure </br> 4.15.0-generic & 4.4.0-179-9.33 핫 픽스 패치 * *|
16.04 LTS | 9.32 | 4.4.0-21-generic에서 4.4.0-171-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-74-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1066-azure|
|||
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generic to 4.15.0-112-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-5.0.0-58-generic </br> 5.3.0-19-5.3.0-65-generic </br> 5.4.0-37-5.4.0-42-제네릭</br> 4.15.0-1009-4.15.0-1092-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0로 </br> 5.4.0-1020-azure에서 5.4.0로 </br> 5.0.0-60-generic & 5.3.0-9.36-핫 픽스 패치 * *|
18.04 LTS | [9.34](https://support.microsoft.com/help/4570609), [9.35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-4.15.0-108-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-제네릭-5.0.0-제네릭 </br> 5.3.0-19-generic ~ 5.3.0-61-generic </br> 4.15.0-1009-4.15.0-1089-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-5.0.0-1036-azure </br> 5.3.0-1007-azure에서 5.3.0로 </br> 4.15.0, 5.0.0-generic, 5.3.0-62-generic, 4.15.0-1091-azure & 5.3.0-1032-azure-9.35 핫 픽스 패치 * *|
18.04 LTS | [9.33](https://support.microsoft.com/help/4564347/) | 4.15.0-20-generic to 4.15.0-99-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic to 5.0.0-47-generic </br> 5.3.0-19-generic to 5.3.0-51-generic </br> 4.15.0-1009-4.15.0-1082-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-5.0.0-1036-azure </br> 5.3.0-1007-5.3.0-1020-azure </br> 4.15.0, 5.0.0, 5.3.0, & 5.3.0-53-제네릭-9.33 핫 픽스 패치 * *|
18.04 LTS | 9.32| 4.15.0-20-generic에서 4.15.0-74-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-37-generic </br> 5.3.0-19-generic에서 5.3.0-24-generic </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1028-azure </br> 5.3.0-1007-azure에서 5.3.0-1009-azure|
|||
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-5.4.0-42 </br> -generic 5.4.0-5.4.0-azure

* * 참고: 릴리스 30 일 이내에 최신 Linux 커널을 지원 하기 위해 Azure Site Recovery 최신 모바일 에이전트 버전을 기반으로 하는 핫 픽스 패치를 롤업 합니다. 이 픽스는 두 주 버전 릴리스 사이에서 롤아웃 됩니다. 최신 버전의 모바일 에이전트 (핫 픽스 패치 포함)로 업데이트 하려면 [이 문서](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)에 설명 된 단계를 따르세요. 이 패치는 현재 Azure에서 Azure로 DR 시나리오에 사용 되는 모바일 에이전트에 대해 출시 되었습니다.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Debian 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
Debian 7 | 9.32, [9.33](https://support.microsoft.com/help/4564347/), [9.34](https://support.microsoft.com/help/4570609), [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/) | 3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.35](https://support.microsoft.com/help/4573888/, ), [9.36](https://support.microsoft.com/help/4578241/) | 3.16.0-amd64 to 3.16.0-11-amd64, 4.9.0 64,-amd64 to 4.9.0 64,. bpo |
Debian 8 | 9.32, [9.33](https://support.microsoft.com/help/4564347/), [9.34](https://support.microsoft.com/help/4570609) | 3.16.0-4-amd64에서 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.11-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 가상 머신에 대해 지원되는 SUSE Linux Enterprise Server 12 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.36](https://support.microsoft.com/help/4578241/) | 모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure-4.12.14-6.43 </br> 4.12.14-16.7-4.12.14-16.22-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.34](https://support.microsoft.com/help/4570609), [9.35](https://support.microsoft.com/help/4573888/) | 모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure-4.12.14-6.43 </br> 4.12.14-16.7-4.12.14-16.19-azure  |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3,SP4) | 9.32, [9.33](https://support.microsoft.com/help/4564347/) [9.35](https://support.microsoft.com/help/4573888/) | 모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | 9.33 핫 픽스 패치 | 모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.34-azure </br> 4.12.14-16.7-4.12.14-16.13-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 가상 머신에 대해 지원되는 SUSE Linux Enterprise Server 15 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 및 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/)  | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-4.12.14-5.47-azure </br></br> 4.12.14-4.12.14-8.38-azure
SUSE Linux Enterprise Server 15 및 15 SP1 | [9.34](https://support.microsoft.com/help/4570609), [9.35](https://support.microsoft.com/help/4573888/)  | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-4.12.14-5.47-azure </br></br> 4.12.14-4.12.14-8.33-azure 
|SUSE Linux Enterprise Server 15 및 15 SP1 | [9.33](https://support.microsoft.com/help/4564347/) | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-4.12.14-5.47-azure </br></br> 4.12.14-4.12.14-8.30-azure |
SUSE Linux Enterprise Server 15 및 15 SP1 | 9.32 | 기본적으로 모든 [Stock SUSE 15 및 15 커널](https://www.suse.com/support/kb/doc/?id=000019587)이 지원됩니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-8.22-azure |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>복제된 컴퓨터 - Linux 파일 시스템/게스트 스토리지

* 파일 시스템: ext3, ext4, XFS, BTRFS
* 볼륨 관리자: LVM2

> [!NOTE]
> 다중 경로 소프트웨어는 지원 되지 않습니다. 


## <a name="replicated-machines---compute-settings"></a>복제된 머신 - 컴퓨팅 설정

**설정** | **지원** | **세부 정보**
--- | --- | ---
크기 | CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM | [Azure Virtual Machine 크기](../virtual-machines/sizes.md)를 확인합니다.
가용성 집합 | 지원됨 | 기본 옵션을 사용하여 Azure VM에 대해 복제를 사용하도록 설정하면 원본 지역 설정에 따라 가용성 집합이 자동으로 생성됩니다. 이러한 설정을 수정할 수 있습니다.
가용성 영역 | 지원됨 |
HUB(하이브리드 사용 혜택) | 지원됨 | 원본 VM에 활성 HUB 라이선스가 있는 경우 테스트 장애 조치(failover) 또는 장애 조치(failover)된 VM에서도 HUB 라이선스를 사용합니다.
가상 머신 크기 집합 | 지원되지 않음 |
Azure 갤러리 이미지 - Microsoft 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Azure 갤러리 이미지 - 타사 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
사용자 지정 이미지 - 타사 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Site Recovery를 사용하여 마이그레이션된 VM | 지원됨 | VMware VM 또는 물리적 컴퓨터가 Site Recovery를 사용하여 Azure에 마이그레이션되면 컴퓨터에서 실행되는 이전 버전의 모바일 서비스를 제거하고 컴퓨터를 다시 시작한 후 다른 Azure 지역에 복제해야 합니다.
RBAC 정책 | 지원되지 않음 | Vm의 RBAC (역할 기반 액세스 제어) 정책은 대상 지역의 장애 조치 (failover) VM에 복제 되지 않습니다.
확장 | 지원되지 않음 | 확장은 대상 지역의 장애 조치(failover) VM에 복제되지 않습니다. 장애 조치(failover) 후 수동으로 설치해야 합니다.
근접 배치 그룹 | 지원됨 | 근접 배치 그룹 내에 있는 가상 머신은 Site Recovery를 사용 하 여 보호할 수 있습니다.


## <a name="replicated-machines---disk-actions"></a>복제된 컴퓨터 - 디스크 작업

**동작** | **세부 정보**
-- | ---
복제된 VM에서 디스크 크기 조정 | 장애 조치(failover) 전에 원본 VM에서 지원됩니다. 복제를 해제/다시 설정할 필요가 없습니다.<br/><br/> 장애 조치(failover) 후 원본 VM을 변경하면 변경 내용이 캡처되지 않습니다.<br/><br/> 장애 조치(failover) 후 Azure VM에서 디스크 크기를 변경하는 경우 Site Recovery에서 변경 내용이 캡처되지 않으며 원래 VM 크기로 장애 복구(failback)가 발생합니다.
복제된 VM에 디스크 추가 | 지원됨
보호 된 디스크에 대 한 오프 라인 변경 | 디스크 연결을 끊고 오프 라인으로 수정 하려면 전체 다시 동기화를 수행 해야 합니다.

## <a name="replicated-machines---storage"></a>복제된 컴퓨터 - 스토리지

이 표에서는 Azure VM OS 디스크, 데이터 디스크 및 임시 디스크에 대한 지원을 요약해서 표시합니다.

- 성능 문제를 방지하려면 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 및 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM의 VM 디스크 제한 및 목표를 확인하는 것이 중요합니다.
- 기본 설정을 사용하여 배포하는 경우 Site Recovery는 원본 설정에 따라 디스크 및 스토리지 계정을 자동으로 만듭니다.
- 사용자 지정하는 경우 지침을 따릅니다.

**구성 요소** | **지원** | **세부 정보**
--- | --- | ---
OS 디스크 최대 크기 | 2048GB | VM 디스크에 대해 [자세히 알아봅니다](../virtual-machines/managed-disks-overview.md).
임시 디스크 | 지원되지 않음 | 임시 디스크는 항상 복제에서 제외됩니다.<br/><br/> 임시 디스크에는 영구 데이터를 저장하지 마세요. [자세히 알아보기](../virtual-machines/managed-disks-overview.md).
데이터 디스크 최대 크기 | 관리 디스크의 경우 8,192GB<br></br>비관리 디스크의 경우 4,095GB|
데이터 디스크 최소 크기 | 비관리 디스크는 제한이 없습니다. 관리 디스크의 경우 2GB |
데이터 디스크 최대 수 | 특정 Azure VM 크기에 대한 지원에 따라 최대 64개 | VM 크기에 대해 [자세히 알아봅니다](../virtual-machines/sizes.md).
데이터 디스크 변경 비율 | Premium storage의 경우 디스크당 최대 20Mbps입니다. Standard Storage는 디스크당 최대 2MBps입니다. | 디스크의 평균 데이터 변경률이 계속해서 최대값보다 큰 경우 복제가 처리되지 않습니다.<br/><br/>  그러나 최대값을 산발적으로 초과하는 경우 복제가 처리될 수 있지만 복구 지점이 약간 지연될 수 있습니다.
데이터 디스크 - Standard Storage 계정 | 지원됨 |
데이터 디스크 - Premium Storage 계정 | 지원됨 | VM의 디스크가 Premium Storage 계정과 표준 스토리지 계정에 분산된 경우 대상 지역의 스토리지를 동일하게 구성하기 위해 각 디스크에 대해 서로 다른 대상 스토리지 계정을 선택할 수 있습니다.
Managed Disk - Standard | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. |
Managed Disk - Premium | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. |
표준 SSD | 지원됨 |
중복 | LRS 및 GRS가 지원됩니다.<br/><br/> ZRS는 지원되지 않습니다.
콜드 및 핫 스토리지 | 지원되지 않음 | VM 디스크는 콜드 및 핫 스토리지에서 지원되지 않습니다.
스토리지 공간 | 지원됨 |
미사용 암호화(SSE) | 지원됨 | SSE은 스토리지 계정의 기본 설정입니다.
미사용 암호화(CMK) | 지원됨 | 관리 디스크에는 소프트웨어 및 HSM 키가 모두 지원됩니다.
휴지 상태의 이중 암호화 | 지원됨 | [Windows](../virtual-machines/windows/disk-encryption.md) 및 [Linux](../virtual-machines/linux/disk-encryption.md) 에 대해 지원 되는 지역에 대 한 자세한 정보
Windows OS용 ADE(Azure Disk Encryption) | 관리 디스크를 사용하는 VM을 지원합니다. | 비관리 디스크를 사용하는 VM은 지원되지 않습니다. <br/><br/> HSM 보호 키는 지원되지 않습니다. <br/><br/> 단일 디스크에서 개별 볼륨 암호화는 지원되지 않습니다. |
Linux OS용 ADE(Azure Disk Encryption) | 관리 디스크를 사용하는 VM을 지원합니다. | 비관리 디스크를 사용하는 VM은 지원되지 않습니다. <br/><br/> HSM 보호 키는 지원되지 않습니다. <br/><br/> 단일 디스크에서 개별 볼륨 암호화는 지원되지 않습니다. <br><br> 복제를 사용 하는 것과 관련 된 알려진 문제입니다. [자세한 정보](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS 키 회전 | 지원되지 않음 | 저장소 계정에 대 한 SAS 키를 회전 하는 경우 고객은 복제를 사용 하지 않도록 설정 했다가 다시 사용 하도록 설정 해야 합니다. |
핫 추가    | 지원됨 | 복제된 Azure VM에 추가하는 데이터 디스크에 대한 복제 설정은 관리 디스크를 사용하는 VM을 지원합니다. <br/><br/> Azure VM에 한 번에 하나의 디스크만 핫으로 추가할 수 있습니다. 여러 디스크의 병렬 추가는 지원되지 않습니다. |
디스크 핫 제거    | 지원되지 않음 | VM에서 데이터 디스크를 제거하는 경우 해당 VM에 대해 복제를 해제했다가 다시 설정해야 합니다.
디스크 제외 | 지원됨. [PowerShell](azure-to-azure-exclude-disks.md)을 사용하여 구성해야 합니다. |    임시 디스크는 기본적으로 제외됩니다.
직접 스토리지 공간  | 크래시 일관성이 있는 복구 지점을 지원합니다. 애플리케이션 일관성이 있는 복구 지점은 지원되지 않습니다. |
스케일 아웃 파일 서버  | 크래시 일관성이 있는 복구 지점을 지원합니다. 애플리케이션 일관성이 있는 복구 지점은 지원되지 않습니다. |
DRBD | DRBD 설치 프로그램의 일부인 디스크는 지원되지 않습니다. |
LRS | 지원됨 |
GRS | 지원됨 |
RA-GRS | 지원됨 |
ZRS | 지원되지 않음 |
콜드 및 핫 스토리지 | 지원되지 않음 | 가상 머신 디스크는 콜드 및 핫 스토리지에서 지원되지 않습니다.
가상 네트워크의 Azure Storage 방화벽  | 지원됨 | 스토리지 계정에 대한 가상 네트워크 액세스를 제한하는 경우 [신뢰할 수 있는 Microsoft 서비스 허용](../storage/common/storage-network-security.md#exceptions)을 사용하도록 설정합니다.
범용 V2 스토리지 계정(핫 및 쿨 계층 모두) | 지원됨 | 범용 V1 Storage 계정에 비해 상당한 트랜잭션 비용 증가
2세대(UEFI 부팅) | 지원됨
NVMe 디스크 | 지원되지 않음
Azure 공유 디스크 | 지원되지 않음
보안 전송 옵션 | 지원됨

>[!IMPORTANT]
> 성능 문제를 방지하려면 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 또는 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM에 대한 VM 디스크 확장성 및 성능 목표를 따라야 합니다. 기본 설정을 사용하는 경우 Site Recovery가 원본 구성에 따라 필요한 디스크 및 스토리지 계정을 만듭니다. 사용자 고유의 설정을 사용자 지정하고 선택하는 경우 소스 VM의 디스크 확장성 및 성능 목표를 따릅니다.

## <a name="limits-and-data-change-rates"></a>제한 및 데이터 변경률

다음 표에는 Site Recovery 제한이 요약되어 있습니다.

- 이러한 제한은 테스트를 기반으로 하지만 모든 가능한 애플리케이션 I/O 조합을 다룰 수는 없습니다.
- 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다.
- 디스크 데이터 변동별 및 가상 머신 데이터 변동별로 두 가지 제한을 고려해야 합니다.
- 가상 머신 데이터 변동 별 현재 제한은 크기에 관계 없이 54 m b/초입니다.

**스토리지 대상** | **평균 원본 디스크 I/O** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20MB/s | 디스크당 1,684GB

## <a name="replicated-machines---networking"></a>복제된 컴퓨터 - 네트워킹
**설정** | **지원** | **세부 정보**
--- | --- | ---
NIC | 특정 Azure VM 크기에 대해 지원되는 최대 수 | 장애 조치(failover) 동안 VM이 만들어지면 NIC가 만들어집니다.<br/><br/> 장애 조치(failover) VM의 NIC 수는 복제를 사용하도록 설정한 당시에 원본 VM의 NIC 수에 따라 다릅니다. 복제를 사용하도록 설정한 후에 NIC를 추가하거나 제거하는 경우 장애 조치(failover) 후 복제된 VM의 NIC 수에는 영향을 주지 않습니다. <br/><br/> 장애 조치 (failover) 후의 Nic 순서는 원래 순서와 동일 하지 않을 수 있습니다. <br/><br/> 조직의 명명 규칙에 따라 대상 지역에서 Nic의 이름을 바꿀 수 있습니다. NIC 이름 바꾸기는 PowerShell을 사용 하 여 지원 됩니다.
인터넷 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결합니다.
내부 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결합니다.
공용 IP 주소 | 지원됨 | 기존 공용 IP 주소를 NIC에 연결합니다. 또는 공용 IP 주소를 만들고 복구 계획에서 Azure Automation 스크립트를 사용하여 NIC에 연결합니다.
NIC의 NSG | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 NIC에 연결합니다.
서브넷의 NSG | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 서브넷에 연결합니다.
예약된(고정) IP 주소 | 지원됨 | 원본 VM의 NIC에 고정 IP 주소가 있고 대상 서브넷에서 동일한 IP 주소를 사용할 수 있는 경우 해당 IP가 장애 조치(Failover)된 VM에 할당됩니다.<br/><br/> 대상 서브넷에서 동일한 IP 주소를 사용할 수 없는 경우 서브넷의 사용 가능한 IP 주소 중 하나가 이 VM용으로 예약됩니다.<br/><br/> **복제된 항목** > **설정** > **계산 및 네트워크** > **네트워크 인터페이스**에서 고정 IP 주소 및 서브넷을 지정할 수도 있습니다.
동적 IP 주소 | 지원됨 | 원본의 NIC에 동적 IP 주소가 있는 경우 장애 조치(failover)된 VM의 NIC도 기본적으로 동적으로 설정됩니다.<br/><br/> 필요한 경우 이 주소를 고정 IP 주소로 수정할 수 있습니다.
여러 IP 주소 | 지원되지 않음 | 여러 IP 주소가 할당된 NIC가 있는 VM을 장애 조치(failover)하는 경우 원본 지역의 NIC의 기본 IP 주소만 유지됩니다. 여러 IP 주소를 할당하려면 [복구 계획](recovery-plan-overview.md)에 VM을 추가하고 스크립트를 연결하여 계획에 추가 IP 주소를 할당하거나, 장애 조치(failover) 후 수동으로 또는 스크립트를 사용해 변경할 수 있습니다.
Traffic Manager     | 지원됨 | 트래픽이 평소에는 원본 지역의 엔드포인트로 라우팅되고 장애 조치(Failover) 시에는 대상 지역의 엔드포인트로 라우팅되도록 Traffic Manager를 미리 구성할 수 있습니다.
Azure DNS | 지원됨 |
사용자 지정 DNS    | 지원됨 |
인증되지 않은 프록시 | 지원됨 | [자세히 알아보기](./azure-to-azure-about-networking.md)
인증된 프록시 | 지원되지 않음 | VM에서 아웃바운드 연결에 인증된 프록시를 사용하는 경우 Azure Site Recovery를 사용하여 VM을 복제할 수 없습니다.
온-프레미스에 대한 VPN 사이트 간 연결<br/><br/>(ExpressRoute 유무에 관계 없음)| 지원됨 | Site Recovery 트래픽이 온-프레미스로 라우팅되지 않도록 UDR 및 NSG가 구성되어 있는지 확인합니다. [자세히 알아보기](./azure-to-azure-about-networking.md)
VNet 간 연결    | 지원됨 | [자세히 알아보기](./azure-to-azure-about-networking.md)
Virtual Network 서비스 엔드포인트 | 지원됨 | 스토리지 계정에 대한 가상 네트워크 액세스를 제한하는 경우 신뢰할 수 있는 Microsoft 서비스가 스토리지 계정에 액세스할 수 있는지 확인합니다.
가속된 네트워킹 | 지원됨 | 원본 VM에서 가속 네트워킹을 사용하도록 설정해야 합니다. [자세히 알아보기](azure-vm-disaster-recovery-with-accelerated-networking.md).
Palo Alto 네트워크 어플라이언스 | 지원되지 않음 | 타사 어플라이언스를 사용하는 경우 공급자가 가상 머신 내부에서 적용하는 제한 사항이 있는 경우가 흔합니다. Azure Site Recovery가 에이전트, 확장 및 아웃바운드 연결을 사용할 수 있어야 합니다. 하지만 어플라이언스는 가상 머신 내에서 아웃바운드 작업을 구성하는 것을 허용하지 않습니다.
IPv6  | 지원되지 않음 | IPv4 및 IPv6을 모두 포함하는 혼합 구성도 지원되지 않습니다. Site Recovery 작업을 수행하기 전에 IPv6 범위의 서브넷을 확보하세요.
Site Recovery 서비스에 대 한 개인 링크 액세스 | 지원됨 | [자세히 알아보기](azure-to-azure-how-to-enable-replication-private-endpoints.md)



## <a name="next-steps"></a>다음 단계

- Azure VM 복제를 위한 [네트워킹 지침](./azure-to-azure-about-networking.md)에 대해 알아봅니다.
- [Azure VM을 복제](./azure-to-azure-quickstart.md)하여 재해 복구를 배포합니다.
