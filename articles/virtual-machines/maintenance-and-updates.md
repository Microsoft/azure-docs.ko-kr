---
title: 유지 관리 및 업데이트
description: Azure에서 실행 되는 가상 머신에 대 한 유지 관리 및 업데이트 개요입니다.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121855"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Azure에서 가상 머신 유지 관리

Azure는 가상 컴퓨터에 대 한 호스트 인프라의 안정성, 성능 및 보안을 개선 하기 위해 플랫폼을 주기적으로 업데이트 합니다. 이러한 업데이트의 용도는 호스팅 환경의 소프트웨어 구성 요소 패치에서 네트워킹 구성 요소 업그레이드 또는 하드웨어 서비스 해제에 이르기까지 다양합니다. 

업데이트는 호스트 된 Vm에 거의 영향을 주지 않습니다. 업데이트에 영향을 주는 경우 Azure는 업데이트에 대 한 최소한의 방법을 선택 합니다.

- 업데이트를 다시 부팅 하지 않아도 되는 경우 호스트가 업데이트 되거나 VM이 이미 업데이트 된 호스트로 실시간 마이그레이션되는 동안 VM이 일시 중지 됩니다. 
- 유지 관리를 다시 시작 해야 하는 경우 계획 된 유지 관리에 대 한 알림이 표시 됩니다. 또한 Azure는 사용자에 게 적합 한 시간에 유지 관리를 직접 시작할 수 있는 시간 창을 제공 합니다. 유지 관리가 긴급 한 경우를 제외 하 고는 일반적으로 자동 유지 관리 기간이 30 일입니다. Azure는 계획 된 플랫폼 유지 관리를 위해 Vm을 다시 부팅 해야 하는 사례 수를 줄이도록 기술에 투자 하 고 있습니다. 계획 된 유지 관리에 대 한 지침은 Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) 또는 [포털](maintenance-notifications-portal.md)을 사용 하 여 계획 된 유지 관리 알림 처리를 참조 하세요.

이 페이지에서는 Azure에서 두 가지 유형의 유지 관리를 모두 수행하는 방법을 설명합니다. 계획 되지 않은 이벤트 (중단)에 대 한 자세한 내용은 [Windows 용 vm의 가용성 관리](./windows/manage-availability.md) 또는 [Linux](./linux/manage-availability.md)에 대 한 해당 문서를 참조 하세요.

VM 내에서 Windows 또는 [Linux](./linux/scheduled-events.md)용 [Scheduled Events를 사용 하](./windows/scheduled-events.md) 여 예정 된 유지 관리에 대 한 알림을 받을 수 있습니다.



## <a name="maintenance-that-doesnt-require-a-reboot"></a>다시 부팅 하지 않아도 되는 유지 관리

대부분의 플랫폼 업데이트는 고객 Vm에 영향을 주지 않습니다. 영향을 받지 않는 업데이트를 사용할 수 없는 경우 Azure는 고객 Vm에 가장 덜 영향을 주는 업데이트 메커니즘을 선택 합니다. 

0이 아닌 영향을 미치는 대부분의 유지 관리는 VM을 10 초 이내에 일시 중지 합니다. 경우에 따라 Azure는 메모리 보존 유지 관리 메커니즘을 사용 합니다. 이러한 메커니즘은 최대 30 초 동안 VM을 일시 중지 하 고 RAM에 메모리를 유지 합니다. 그런 다음 VM이 다시 시작 되 고 시계가 자동으로 동기화 됩니다. 

메모리 보존 유지 관리는 Azure Vm의 90% 이상에서 작동 합니다. G, M, N 및 H 시리즈에서는 작동 하지 않습니다. Azure는 실시간 마이그레이션 기술을 점점 더 많이 사용 하며 일시 중지 기간을 줄이기 위해 메모리 보존 유지 관리 메커니즘을 개선 합니다.  

재부팅 하지 않아도 되는 이러한 유지 관리 작업은 한 번에 하나의 장애 도메인에 적용 됩니다. 경고 상태 신호를 수신 하면 중지 됩니다. 

이러한 종류의 업데이트는 일부 응용 프로그램에 영향을 줄 수 있습니다. VM이 다른 호스트로 실시간 마이그레이션되는 경우 일부 중요 한 워크 로드는 VM 일시 중지까지 몇 분 안에 약간의 성능 저하가 발생할 수 있습니다. VM 유지 관리를 준비 하 고 Azure 유지 관리 중의 영향을 줄이려면 이러한 응용 프로그램에 대해 Windows 또는 [Linux](./linux/scheduled-events.md) [에 대 한 Scheduled Events](./windows/scheduled-events.md) 사용해 보세요. 

다시 부팅 하지 않아도 되는 유지 관리를 관리 하는 데 도움이 될 수 있는 공개 미리 보기의 기능 (유지 관리 제어)도 있습니다. [Azure 전용 호스트](./linux/dedicated-hosts.md) 또는 [격리 된 VM](../security/fundamentals/isolation-choices.md)중 하나를 사용 해야 합니다. 유지 관리 제어를 통해 플랫폼 업데이트를 건너뛰고 35 일의 롤링 기간 내에 업데이트를 적용할 수 있습니다. 자세한 내용은 [유지 관리 제어를 사용 하 여 업데이트 제어 및 Azure CLI](maintenance-control-cli.md)를 참조 하세요.


### <a name="live-migration"></a>실시간 마이그레이션

실시간 마이그레이션은 다시 부팅이 필요 하지 않으며 VM에 대 한 메모리를 유지 하는 작업입니다. 일시 중지 또는 중지를 발생 시킵니다. 일반적으로 5 초 미만으로 지속 됩니다. G, M, N, H 시리즈를 제외 하 고 모든 IaaS (infrastructure as a service) Vm은 실시간 마이그레이션에 적합 합니다. 적격 Vm은 Azure 제 수에 배포 된 IaaS Vm의 90% 이상을 나타냅니다. 

Azure 플랫폼은 다음과 같은 시나리오에서 실시간 마이그레이션을 시작 합니다.
- 계획된 유지 보수
- 하드웨어 오류
- 할당 최적화

일부 계획 된 유지 관리 시나리오에서는 실시간 마이그레이션을 사용 하며, Scheduled Events를 사용 하 여 실시간 마이그레이션 작업을 시작할 때 미리 확인할 수 있습니다.

Azure Machine Learning 알고리즘이 발생 하는 하드웨어 오류를 예측 하거나 VM 할당을 최적화 하려는 경우 실시간 마이그레이션을 사용 하 여 Vm을 이동할 수도 있습니다. 저하 된 하드웨어의 인스턴스를 검색 하는 예측 모델링에 대 한 자세한 내용은 [예측 기계 학습 및 실시간 마이그레이션을 사용 하 여 AZURE VM 복원 력 향상](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)을 참조 하세요. 실시간 마이그레이션 알림은 모니터 및 Service Health 로그의 Azure Portal 및 이러한 서비스를 사용 하는 경우 Scheduled Events에 표시 됩니다.

## <a name="maintenance-that-requires-a-reboot"></a>다시 부팅 해야 하는 유지 관리

드물지만 계획 된 유지 관리를 위해 Vm을 다시 부팅 해야 하는 경우에는 미리 알림이 표시 됩니다. 계획 된 유지 관리에는 셀프 서비스 단계와 예약 된 유지 관리 단계의 두 단계가 있습니다.

일반적으로 4 주 동안 지속 되는 *셀프 서비스 단계*에서는 vm에 대 한 유지 관리를 시작 합니다. 셀프 서비스의 일부로 각 VM을 쿼리하여 마지막 유지 관리 요청의 상태와 결과를 확인할 수 있습니다.

셀프 서비스 유지 관리를 시작하면 VM이 이미 업데이트된 노드에 다시 배포됩니다. VM이 다시 부팅 되기 때문에 임시 디스크가 손실 되 고 가상 네트워크 인터페이스와 연결 된 동적 IP 주소가 업데이트 됩니다.

셀프 서비스 유지 관리를 수행 하는 동안 오류가 발생 하면 작업이 중지 되 고, VM이 업데이트 되지 않으며, 셀프 서비스 유지 관리를 다시 시도할 수 있는 옵션이 제공 됩니다. 

셀프 서비스 단계가 종료 되 면 *예약 된 유지 관리 단계가* 시작 됩니다. 이 단계에서 유지 관리 단계를 계속 쿼리할 수 있지만 직접 유지 관리를 시작할 수는 없습니다.

다시 부팅이 필요한 유지 관리 관리에 대 한 자세한 내용은 Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) 또는 [포털](maintenance-notifications-portal.md)을 사용 하 여 **계획 된 유지 관리 알림 처리** 를 참조 하세요. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>예약 된 유지 관리 중의 가용성 고려 사항 

예약 된 유지 관리 단계까지 대기 하려는 경우 Vm의 최고 가용성을 유지 하기 위해 고려해 야 할 몇 가지 사항이 있습니다. 

#### <a name="paired-regions"></a>쌍을 이루는 지역

각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이룹니다. 따라서 지역 쌍을 만듭니다. 예약 된 유지 관리 단계에서 Azure는 지역 쌍의 단일 지역에 있는 Vm만 업데이트 합니다. 예를 들어 미국 중부 미국의 VM을 업데이트 하는 동안 Azure는 미국 중 북부에 있는 VM을 동시에 업데이트 하지 않습니다. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다. 지역 쌍의 작동 방식을 이해하면 지역에 VM을 더 잘 배포할 수 있습니다. 자세한 내용은 [Azure 지역 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.

#### <a name="availability-sets-and-scale-sets"></a>가용성 집합 및 확장 집합

Azure Vm에 워크 로드를 배포할 때 응용 프로그램에 고가용성을 제공 하기 위해 *가용성 집합* 내에서 vm을 만들 수 있습니다. 가용성 집합을 사용 하면 다시 부팅 해야 하는 가동 중단 또는 유지 관리 이벤트 중에 하나 이상의 VM을 사용할 수 있도록 보장할 수 있습니다.

가용성 집합 내에서 개별 VM은 최대 20개의 업데이트 도메인에 걸쳐 분산됩니다. 예약 된 유지 관리 중에는 지정 된 시간에 하나의 업데이트 도메인만 업데이트 됩니다. 업데이트 도메인은 반드시 순차적으로 업데이트 되지 않습니다. 

가상 머신 *확장 집합* 은 동일한 vm 집합을 단일 리소스로 배포 하 고 관리 하는 데 사용할 수 있는 Azure compute 리소스입니다. 확장 집합은 가용성 집합의 Vm과 같은 Ud 간에 자동으로 배포 됩니다. 가용성 집합과 마찬가지로, 크기 집합을 사용 하는 경우 예약 된 유지 관리 중에 지정 된 시간에 한 UD만 업데이트 됩니다.

고가용성을 위해 Vm을 설정 하는 방법에 대 한 자세한 내용은 [Windows 용 vm의 가용성 관리](./windows/manage-availability.md) 또는 [Linux](./linux/manage-availability.md)에 대 한 해당 문서를 참조 하세요.

#### <a name="availability-zones"></a>가용성 영역

가용성 영역은 Azure 지역 내에서 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 

가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. Azure 지역의 세 영역에서 Vm을 3 개 이상 만들면 Vm이 세 개의 장애 도메인과 3 개의 업데이트 도메인에 효과적으로 배포 됩니다. Azure 플랫폼은 업데이트 도메인에 분산된 VM을 인식하여 다른 영역에 있는 VM이 동시에 업데이트되지 않게 합니다.

각 인프라 업데이트는 단일 지역 내에서 영역별로 영역을 롤업 합니다. 그러나 영역 1에서 배포가 진행 되 고 영역 2에서 다른 배포를 동시에 수행할 수 있습니다. 배포는 모두 serialize 되지 않습니다. 하지만 단일 배포는 위험을 줄이기 위해 한 번에 하나의 영역만을 롤업 합니다.

## <a name="next-steps"></a>다음 단계 

[Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) 또는 [포털](maintenance-notifications-portal.md) 을 사용 하 여 계획 된 유지 관리를 관리할 수 있습니다. 