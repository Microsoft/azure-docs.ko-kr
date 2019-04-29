---
title: Azure Site Recovery의 Azure 간 복제 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery 서비스를 사용하여 Azure VM의 Azure 지역 간에 재해 복구를 설정할 때 사용되는 구성 요소와 아키텍처에 대해 간략히 설명합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 96873b5fdefc74893929f8150230118a162f195b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791176"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Azure 간 재해 복구 아키텍처


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure VM(가상 머신)에 대한 재해 복구를 배포할 때 사용되는 아키텍처, 구성 요소 및 프로세스에 대해 설명합니다. 재해 복구 설정을 사용하여 Azure VM은 다른 대상 지역으로 계속해서 복제합니다. 중단이 발생하는 경우 VM을 보조 지역으로 장애 조치하고, 해당 지역에서 액세스할 수 있습니다. 모두 정상적으로 다시 실행 중인 경우 다시 장애 복구하고 기본 위치에서 작업을 계속할 수 있습니다.



## <a name="architectural-components"></a>아키텍처 구성 요소

Azure VM에 대한 재해 복구에 관련된 구성 요소는 다음 표에 요약되어 있습니다.

**구성 요소** | **요구 사항**
--- | ---
**원본 지역의 VM** | [지원되는 원본 지역](azure-to-azure-support-matrix.md#region-support)에서 하나 이상의 Azure VM입니다.<br/><br/> VM은 [지원되는 운영 체제](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)에서 실행될 수 있습니다.
**원본 VM 스토리지** | Azure VM은 관리될 수 있습니다. 또는 스토리지 계정에 분산된 관리되지 않는 디스크를 가집니다.<br/><br/>지원되는 Azure 스토리지에 대해 [자세히 알아봅니다](azure-to-azure-support-matrix.md#replicated-machines---storage).
**원본 VM 네트워크** | VM은 원본 지역의 VNet(가상 네트워크)에서 하나 이상의 서브넷에 있을 수 있습니다. 네트워킹 요구 사항에 대해 [자세히 알아봅니다](azure-to-azure-support-matrix.md#replicated-machines---networking).
**캐시 스토리지 계정** | 원본 네트워크에서 캐시 스토리지 계정이 필요합니다. 복제 중 VM 변경 내용이 대상 스토리지로 전송되기 전에 캐시에 저장됩니다.<br/><br/> 캐시를 사용하여 VM에서 실행 중인 프로덕션 애플리케이션에 미치는 영향이 최소화됩니다.<br/><br/> 캐시 스토리지 요구 사항에 대해 [자세히 알아봅니다](azure-to-azure-support-matrix.md#cache-storage). 
**대상 리소스** | 대상 리소스는 복제하는 동안 및 장애 조치(failover)가 발생하는 경우에 사용됩니다. Site Recovery는 기본적으로 대상 리소스를 설정할 수 있습니다. 또는 사용자가 만들거나 사용자 지정할 수 있습니다.<br/><br/> 대상 지역에서 VM을 만들 수 있는지, 구독에 대상 지역에 필요한 VM 크기를 지원하기 위해 충분한 리소스가 있는지 확인합니다. 

![원본 및 대상 복제](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>대상 리소스

VM의 복제를 활성화하면 Site Recovery는 대상 리소스를 자동으로 만들기 위한 옵션을 제공합니다. 

**대상 리소스** | **기본 설정**
--- | ---
**대상 구독** | 원본 구독과 동일합니다.
**대상 리소스 그룹** | 장애 조치(failover) 후 VM이 속하게 되는 리소스 그룹입니다.<br/><br/> 원본 지역을 제외한 모든 Azure 지역일 수 있습니다.<br/><br/> Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 리소스 그룹을 만듭니다.<br/><br/>
**대상 VNet** | 장애 조치(failover) 후 복제된 VM이 있는 VNet(가상 네트워크)입니다. 네트워크 매핑은 원본 및 대상 가상 네트워크 간에 만들어집니다.<br/><br/> Site Recovery는 "asr" 접미사를 사용하여 새 VNet 및 서브넷을 만듭니다.
**대상 스토리지 계정** |  VM에서 관리 디스크를 사용하지 않는 경우 데이터가 복제되는 스토리지 계정입니다.<br/><br/> Site Recovery는 대상 지역에 새 스토리지 계정을 만들어서 원본 스토리지 계정을 미러링합니다.
**복제 관리 디스크** | VM에서 관리 디스크를 사용하는 경우 데이터가 복제되는 관리 디스크입니다.<br/><br/> Site Recovery는 원본을 미러링하는 스토리지 영역에서 관리 디스크의 복제본을 만듭니다.
**대상 가용성 집합** |  장애 조치(failover) 후 복제된 VM이 있는 가용성 집합입니다.<br/><br/> Site Recovery는 원본 위치의 가용성 집합에 있는 VM에 대해 "asr" 접미사를 사용하여 대상 지역에 가용성 집합을 만듭니다. 가용성 집합이 있는 경우 가용성 집합이 사용되며 새로 생성되지 않습니다.
**대상 가용성 영역** | 대상 지역에서 가용성 영역을 지원하는 경우 Site Recovery는 원본 지역에 사용된 동일한 영역 번호를 할당합니다.

### <a name="managing-target-resources"></a>대상 리소스 관리

다음과 같이 대상 리소스를 관리할 수 있습니다.

- 복제를 사용하도록 설정하면 대상 설정을 수정할 수 있습니다.
- 복제가 작동한 후 대상 설정을 수정할 수 있습니다. 예외는 가용성 유형입니다(단일 인스턴스, 집합 또는 영역). 이 설정을 변경하려면 복제를 비활성화하고, 설정을 수정한 다음, 다시 활성화해야 합니다.



## <a name="replication-policy"></a>복제 정책 

Azure VM 복제를 활성화하면 기본적으로 Site Recovery는 표에 요약된 기본 설정을 사용하여 새 복제 정책을 만듭니다.

**정책 설정** | **세부 정보** | **기본값**
--- | --- | ---
**복구 지점 보존** | Site Recovery에서 복구 지점을 유지하는 기간을 지정합니다. | 24시간
**앱 일치 스냅숏 빈도** | Site Recovery에서 앱 일치 스냅숏을 만드는 주기입니다. | 매 60분

### <a name="managing-replication-policies"></a>복제 정책 관리

다음과 같이 기본 복제 정책 설정을 관리 및 수정할 수 있습니다.
- 복제를 사용하도록 설정하면 설정을 수정할 수 있습니다.
- 언제든지 복제 정책을 만든 다음, 복제를 활성화할 때 적용할 수 있습니다.

### <a name="multi-vm-consistency"></a>다중 VM 일관성

VM을 함께 복제하고, 장애 조치(failover) 시 공유 크래시 일관성 및 앱 일치 복구 지점을 가지려는 경우 복제 그룹으로 함께 수집할 수 있습니다. 다중 VM 일관성은 워크로드 성능에 영향을 미치며, 모든 머신에서 일관성을 필요로 하는 워크로드를 실행하는 VM에만 사용해야 합니다. 



## <a name="snapshots-and-recovery-points"></a>스냅숏 및 복구 지점

복구 지점은 특정 시점에 생성되는 VM 디스크의 스냅숏에서 생성됩니다. VM을 장애 조치(failover)할 때 복구 지점을 사용하여 대상 위치에서 VM을 복원합니다.

장애 조치(failover)할 때 일반적으로 손상 또는 데이터 손실 없이 VM을 시작하고, VM 데이터가 운영 체제 및 VM에서 실행되는 앱에 대해 일관되는지 확인하려고 합니다. 이는 생성되는 스냅숏의 유형에 따라 달라집니다.

Site Recovery는 다음과 같이 스냅숏을 생성합니다.

1. Site Recovery는 빈도를 지정하는 경우 기본적으로 데이터의 크래시 일관성이 있는 스냅숏 및 앱 일치 스냅숏을 생성합니다.
2. 복구 지점은 스냅숏에서 생성되고, 복제 정책에서 보존 설정에 따라 저장됩니다.

### <a name="consistency"></a>일관성

다음 표에서는 여러 유형의 일관성에 대해 설명합니다.

### <a name="crash-consistent"></a>크래시 일관성

**설명** | **세부 정보** | **권장 사항**
--- | --- | ---
크래시 일관성 스냅숏은 스냅숏을 만들 때 디스크에 있는 데이터를 캡처합니다. 메모리의 데이터를 포함하지 않습니다.<br/><br/> VM이 충돌하거나 스냅숏이 생성된 순간 전원 코드를 서버에서 가져온 경우 존재하는 디스크의 데이터에 해당합니다.<br/><br/> 크래시 일관성은 운영 체제 또는 VM의 앱에 대한 데이터 일관성을 보장하지 않습니다. | Site Recovery는 기본적으로 5분마다 크래시 일관성 복구 지점을 만듭니다. 이 설정은 수정할 수 없습니다.<br/><br/>  | 오늘날 대부분의 앱은 크래시 일관성 지점에서도 제대로 복구할 수 있습니다.<br/><br/> 크래시 일관성 복구 지점은 일반적으로 운영 체제 및 DHCP 서버 및 인쇄 서버와 같은 앱의 복제를 위해 충분합니다.

### <a name="app-consistent"></a>앱 일치

**설명** | **세부 정보** | **권장 사항**
--- | --- | ---
앱 일치 복구 지점은 앱 일치 스냅숏에서 생성됩니다.<br/><br/> 앱 일치 스냅숏은 크래시 일관성 스냅숏의 모든 정보와 메모리의 모든 데이터 및 진행 중인 트랜잭션을 포함합니다. | 앱 일치 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용합니다.<br/><br/>   1) 스냅숏이 시작되면 VSS는 볼륨에 COW(기록 중 복사) 작업을 수행합니다.<br/><br/>   2) COW를 수행하기 전에 VSS는 머신의 모든 앱에 해당 메모리 상주 데이터를 디스크에 플러시해야 함을 알립니다.<br/><br/>   3) 그런 다음, VSS는 백업/재해 복구 앱(이 경우 Site Recovery)에서 스냅숏 데이터를 읽고 진행하도록 허용합니다. | 앱 일치 스냅숏은 지정하는 빈도에 따라 생성됩니다. 이 빈도는 항상 복구 지점 유지에 대한 설정보다 작아야 합니다. 예를 들어 24시간의 기본 설정을 사용하여 복구 지점을 보존하는 경우 빈도를 24시간 미만으로 설정해야 합니다.<br/><br/>크래시 일관성 스냅숏보다 더 복잡하며 완료하는 데 시간이 더 걸립니다.<br/><br/> 복제에 대해 활성화된 VM에서 실행되는 앱의 성능에 영향을 미칩니다. 

## <a name="replication-process"></a>복제 프로세스

Azure VM에 대해 복제를 활성화하면 다음 상황이 발생합니다.

1. Site Recovery 이동성 서비스 확장이 VM에 자동으로 설치됩니다.
2. 확장은 Site Recovery를 사용하여 VM을 등록합니다.
3. VM에 대해 연속 복제를 시작합니다.  데이터 쓰기가 원본 위치의 캐시 스토리지 계정에 즉시 전송됩니다.
4. Site Recovery는 캐시의 데이터를 처리하여 대상 스토리지 계정 또는 복제본 관리 디스크로 보냅니다.
5. 데이터가 처리된 후 크래시 일관성 복구 지점이 5분마다 생성됩니다. 앱 일치 복구 지점은 복제 정책에 지정된 설정에 따라 생성됩니다.

![복제를 사용하도록 설정하는 프로세스, 2단계](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**복제 프로세스**

## <a name="connectivity-requirements"></a>연결 요구 사항

 복제하는 Azure VM에는 아웃바운드 연결이 필요합니다. Site Recovery에는 VM에 대한 인바운드 연결이 필요하지 않습니다. 

### <a name="outbound-connectivity-urls"></a>아웃바운드 연결(URL)

VM에 대한 아웃바운드 액세스가 URL로 제어되는 경우 다음 URL을 허용합니다.

| **URL** | **세부 정보** |
| ------- | ----------- |
| \*.blob.core.windows.net | VM에서 원본 지역의 캐시 저장소 계정에 데이터를 쓸 수 있도록 합니다. |
| login.microsoftonline.com | Site Recovery 서비스 URL에 대한 권한 부여 및 인증을 제공합니다. |
| \*.hypervrecoverymanager.windowsazure.com | VM이 Site Recovery 서비스와 통신할 수 있도록 합니다. |
| \*.servicebus.windows.net | VM이 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 합니다. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 주소 범위에 대한 아웃바운드 연결

IP 주소를 사용하여 VM에 대한 아웃바운드 연결을 제어하려면 다음 주소를 허용합니다.

#### <a name="source-region-rules"></a>원본 지역 규칙

**규칙** |  **세부 정보** | **Service 태그**
--- | --- | --- 
HTTPS 아웃바운드 허용: 포트 443 | 원본 지역의 스토리지 계정에 해당하는 범위를 허용합니다. | 저장소입니다. \<지역 이름 >.
HTTPS 아웃바운드 허용: 포트 443 | Azure AD(Azure Active Directory)에 해당하는 범위를 허용합니다.<br/><br/> Azure AD 주소가 나중에 추가되는 경우 새 NSG(네트워크 보안 그룹) 규칙을 만들어야 합니다.  | AzureActiveDirectory
HTTPS 아웃바운드 허용: 포트 443 | 대상 위치에 해당하는 [Site Recovery 엔드포인트](https://aka.ms/site-recovery-public-ips)에 대한 액세스를 허용합니다. 

#### <a name="target-region-rules"></a>대상 지역 규칙

**규칙** |  **세부 정보** | **Service 태그**
--- | --- | --- 
HTTPS 아웃바운드 허용: 포트 443 | 대상 지역의 스토리지 계정에 해당하는 범위를 허용합니다. | 저장소입니다. \<지역 이름 >.
HTTPS 아웃바운드 허용: 포트 443 | Azure AD에 해당하는 범위를 허용합니다.<br/><br/> Azure AD 주소가 나중에 추가되는 경우 새 NSG 규칙을 만들어야 합니다.  | AzureActiveDirectory
HTTPS 아웃바운드 허용: 포트 443 | 원본 위치에 해당하는 [Site Recovery 엔드포인트](https://aka.ms/site-recovery-public-ips)에 대한 액세스를 허용합니다. 


#### <a name="control-access-with-nsg-rules"></a>NSG 규칙을 사용하여 액세스 제어

[NSG 규칙](https://docs.microsoft.com/azure/virtual-network/security-overview)을 사용하여 Azure 네트워크/서브넷 간 네트워크 트래픽을 필터링하여 VM 연결을 제어하는 경우 다음 요구 사항을 확인하세요.

- 원본 Azure 지역에 대한 NSG 규칙은 복제 트래픽에 대한 아웃바운드 액세스를 허용해야 합니다.
- 프로덕션 환경에 배치하기 전에 테스트 환경에서 규칙을 만드는 것이 좋습니다.
- 개별 IP 주소를 허용하는 대신 [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)를 사용합니다.
    - 서비스 태그는 보안 규칙을 만들 때 복잡성을 최소화하도록 함께 수집되는 IP 주소 접두사의 그룹을 나타냅니다.
    - Microsoft는 시간이 지남에 따라 서비스 태그를 자동으로 업데이트합니다. 
 
Site Recovery에 대한 [아웃바운드 연결](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) 및 [NSG를 사용하여 연결 제어](concepts-network-security-group-with-site-recovery.md)에 대해 자세히 알아봅니다.


### <a name="connectivity-for-multi-vm-consistency"></a>다중 VM 일관성에 대한 연결

다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다.
- 20004 포트를 통한 VM 간의 내부 통신을 차단하는 방화벽 어플라이언스가 없는지 확인합니다.
- Linux VM을 복제 그룹에 포함하고 싶다면 특정 Linux 버전의 지침에 따라 20004 포트의 아웃바운드 트래픽을 수동으로 열어야 합니다.




## <a name="failover-process"></a>장애 조치(failover) 프로세스

장애 조치(failover)를 시작하면 대상 리소스 그룹, 대상 가상 네트워크, 대상 서브넷 및 대상 가용성 집합에 VM이 생성됩니다. 장애 조치(failover) 중에는 모든 복구 지점을 사용할 수 있습니다.

![장애 조치(failover) 프로세스](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>다음 단계

Azure VM을 보조 지역에 [신속하게 복제](azure-to-azure-quickstart.md)합니다.
