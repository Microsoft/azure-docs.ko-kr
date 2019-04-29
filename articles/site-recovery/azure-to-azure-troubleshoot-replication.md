---
title: Azure Site Recovery의 진행 중인 Azure 간 복제 문제 해결 | Microsoft Docs
description: 재해 복구를 위해 Azure 가상 머신을 복제할 때 오류 및 문제 해결
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103749"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Azure 간 VM 복제에서 진행 중인 문제 해결

이 문서에서는 지역 간에 Azure 가상 머신을 복제 및 복구할 때 Azure Site Recovery에서 발생하는 일반적인 문제를 설명합니다. 또한 이러한 문제를 해결하는 방법을 설명합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)를 참조하세요.

Azure Site Recovery는 원본 지역에서 재해 복구 지역으로 데이터를 일관되게 복제하고 5분마다 크래시 일관성 복구 지점을 만듭니다. Site Recovery에서 60분 동안 복구 지점을 만들 수 없는 경우 다음 정보를 알려줍니다.

오류 메시지: "지난 60분 동안 VM에 사용할 수 있는 크래시 일관성 복구 지점이 없습니다."</br>
오류 ID: 153007 </br>

다음 섹션에서는 원인 및 해결 방법을 설명합니다.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>원본 가상 머신의 높은 데이터 변경률
Azure Site Recovery는 원본 가상 머신의 데이터 변경률이 지원되는 제한보다 높은 경우 이벤트를 발생시킵니다. 문제가 높은 변동으로 인한 것인지 확인하려면 **복제된 항목** > **VM** > **이벤트 - 지난 72시간**으로 이동합니다.
"지원되는 제한을 초과하는 데이터 변경률"이라는 이벤트가 표시됩니다.

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

이벤트를 선택하면 정확한 디스크 정보가 표시됩니다.

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 제한
다음 테이블에는 Azure Site Recovery 제한이 제공됩니다. 이러한 한도는 테스트를 기반으로 하지만 모든 가능한 애플리케이션 I/O 조합을 다룰 수는 없습니다. 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다. 

고려해야 할 두 가지 제한은 디스크당 데이터 변동 및 가상 머신당 데이터 변동입니다. 예를 들어, 다음 표에서 프리미엄 P20 디스크를 살펴보겠습니다. Site Recovery는 VM당 총 변동 폭이 25MB/s로 제한되어 있기 때문에 VM당 최대 5개의 디스크를 사용하여 디스크당 5MB/s의 변동을 처리할 수 있습니다.

**복제 저장소 대상** | **원본 디스크의 평균 I/O 크기** |**원본 디스크의 평균 데이터 변동** | **원본 데이터 디스크의 일별 총 데이터 변동**
---|---|---|---
Standard Storage | 8KB | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB  | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |  디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |10MB/초 | 디스크당 842GB

### <a name="solution"></a>해결 방법
Azure Site Recovery에는 디스크 유형에 따라 데이터 변경률 제한이 있습니다. 이 문제가 되풀이되는지 또는 일시적인지 알려면 영향을 받는 가상 머신의 데이터 변동률을 찾으십시오. 원본 가상 머신으로 이동하여 **모니터링** 아래의 메트릭을 찾은 다음, 이 스크린샷과 같이 메트릭을 추가합니다.

![데이터 변경률을 찾기 위한 3단계 프로세스](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. **메트릭 추가**를 선택하고 **OS 디스크 쓰기 바이트/초** 및 **데이터 디스크 쓰기 바이트/초**를 추가합니다.
2. 스크린샷에 표시된 것처럼 스파이크를 모니터링합니다.
3. OS 디스크 및 결합된 모든 데이터 디스크에서 발생하는 총 쓰기 작업을 보여줍니다. 이러한 메트릭은 디스크별 수준에서 정보를 제공하지는 않지만 데이터 변동의 총 패턴을 나타냅니다.

간헐적인 데이터 버스트에서 스파이크가 발생하고 데이터 변경률이 일정 시간 동안 10MB/s(프리미엄) 및 2MB/s(표준)를 초과했다가 낮아지는 경우에는 복제가 처리됩니다. 그러나 변동이 대부분의 시간 동안 지원되는 제한을 초과하는 경우 가능하면 다음 옵션 중 하나를 고려합니다.

* **높은 데이터 변경률을 일으키는 디스크 제외**: [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)을 사용하여 디스크를 제외할 수 있습니다.
* **재해 복구 스토리지 디스크 계층의 변경**: 이 옵션은 디스크 데이터 변동이 10MB/s보다 작은 경우에만 가능합니다. P10 디스크가 있는 VM에 8MB/s보다 크지만 10MB/s보다 작은 데이터 변동이 있다고 가정합니다. 고객이 보호 중에 대상 스토리지에 대해 P30 디스크를 사용할 수 있는 경우 문제를 해결할 수 있습니다.

## <a name="Network-connectivity-problem"></a>네트워크 연결 문제

### <a name="network-latency-to-a-cache-storage-account"></a>캐시 스토리지 계정의 네트워크 대기 시간
Site Recovery는 복제된 데이터를 캐시 스토리지 계정으로 보냅니다. 가상 머신에서 캐시 스토리지 계정으로 데이터를 업로드하는 데 3초 동안 4MB보다 느리게 업로드하면 네트워크 대기 시간이 표시될 수 있습니다. 

대기 시간과 관련된 문제를 확인하려면 [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)를 사용하여 가상 머신에서 캐시 스토리지 계정으로 데이터를 업로드합니다. 대기 시간이 높은 경우 VM에서 아웃바운드 네트워크 트래픽을 제어하는 데 NVA(네트워크 가상 어플라이언스)를 사용 중인지 확인합니다. 모든 복제 트래픽이 NVA를 통과하는 경우 어플라이언스가 제한될 수 있습니다. 

복제 트래픽이 NVA로 이동하지 않도록 "Storage"에 대한 가상 네트워크에서 네트워크 서비스 엔드포인트를 만드는 것이 좋습니다. 자세한 내용은 [네트워크 가상 어플라이언스 구성](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)을 참조하세요.

### <a name="network-connectivity"></a>네트워크 연결
Site Recovery 복제가 작동하려면 VM에서 특정 URL 또는 IP 범위에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다. 모든 URL이 연결되었는지 확인하려면 [Site Recovery URL에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)을 참조하세요. 