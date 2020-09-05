---
title: Azure Site Recovery를 사용하여 Azure VM을 다른 지역으로 이동
description: Azure Site Recovery를 사용하여 Azure 지역 간에 Azure VM을 이동합니다.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 0c7efc94bcde18e7b6ff43726602fa87641f3e76
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86130627"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>다른 Azure 지역으로 Azure VM 이동

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure VM을 다른 Azure 지역으로 이동하는 데 필요한 이유와 단계에 대한 개요를 제공합니다. 


## <a name="reasons-to-move-azure-vms"></a>Azure VM을 이동하는 이유

다음과 같은 이유로 VM을 이동할 수 있습니다.

- 한 지역에 이미 배포한 상태에서 애플리케이션 또는 서비스의 최종 사용자에게 더 가까운 새 지역 지원이 추가되었습니다. 이 시나리오에서는 VM을 새 지역에 그대로 옮겨서 대기 시간을 줄이는 것이 좋습니다. 구독을 통합하려는 경우 또는 이동을 요구하는 거버넌스 또는 조직 규칙이 있는 경우에도 동일한 방식을 사용합니다.
- VM이 단일 인스턴스 VM으로 배포되거나 가용성 집합의 일부로 배포되었습니다. 가용성 SLA를 높이려면 VM을 가용성 영역으로 옮기면 됩니다.

## <a name="steps-to-move-azure-vms"></a>Azure VM을 이동하는 단계

VM을 이동하려면 다음 단계를 수행해야 합니다.

1. 필수 조건을 확인합니다.
2. 원본 VM을 준비합니다.
3. 대상 지역을 준비합니다.
4. 대상 지역에 데이터를 복사합니다. Azure Site Recovery 복제 기술을 사용하여 원본 VM에서 대상 지역으로 데이터를 복사합니다.
5. 구성을 테스트합니다. 복제가 완료되면 비 프로덕션 네트워크에 테스트 장애 조치(failover)를 수행하여 구성을 테스트합니다.
6. 이동을 수행합니다.
7. 원본 지역의 리소스를 삭제합니다.

> [!NOTE]
> 이 단계에 대한 자세한 내용은 다음 섹션에 나와 있습니다.
> [!IMPORTANT]
> 현재 Azure Site Recovery는 한 지역에서 다른 지역으로 VM 이동은 지원하지만, 같은 Azure 지역 내 이동은 지원하지 않습니다.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>다중 계층 배포를 위한 일반적인 아키텍처

아래 섹션에서는 Azure의 다중 계층 애플리케이션의 가장 일반적인 배포 아키텍처에 대해 설명합니다. 이 예제는 공용 IP가 있는 3계층 애플리케이션입니다. 계층마다(웹, 애플리케이션 및 데이터베이스) VM 2대가 있으며 이들은 Azure Load Balancer를 통해 다른 계층에 연결되어 있습니다. 데이터베이스 계층에서는 고가용성을 위해 VM 간에 SQL Server Always On 복제가 사용됩니다.

* **다양한 계층에 배포되는 단일 인스턴스 VM**: 계층의 각 VM은 단일 인스턴스 VM으로 구성되고, 부하 분산 장치를 통해 다른 계층과 연결됩니다. 이 구성은 채택하기 가장 간단합니다.

     ![여러 계층에 단일 인스턴스 VM 배포](media/move-vm-overview/regular-deployment.png)

* **가용성 집합에 배포되는 각 계층의 VM**: 계층의 각 VM은 가용성 집합으로 구성됩니다. [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md)을 사용하면 Azure에 배포한 VM이 클러스터의 격리된 여러 하드웨어 노드에 분산되도록 할 수 있습니다. 이렇게 하면 Azure 내의 하드웨어 또는 소프트웨어에 장애가 발생해도 VM의 하위 세트만 영향을 받고 전반적인 솔루션은 사용 가능한 작동 상태를 유지할 수 있습니다.

     ![가용성 집합 전반에 VM 배포](media/move-vm-overview/avset.png)

* **가용성 영역 전반에 배포되는 각 계층의 VM**: 계층의 각 VM은 [가용성 영역](../availability-zones/az-overview.md)에서 구성됩니다. Azure 지역의 가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. 예를 들어 Azure 지역의 3개 영역에 VM을 3개 이상 만들면 장애 도메인 3개와 업데이트 도메인 3개에 VM이 효과적으로 분산됩니다. Azure 플랫폼은 업데이트 도메인에 분산된 VM을 인식하여 다른 영역에 있는 VM이 동시에 업데이트되지 않게 합니다.

     ![가용성 영역 배포](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>VM을 있는 그대로 대상 Azure 지역으로 이동

앞서 언급한 [아키텍처](#typical-architectures-for-a-multi-tier-deployment)를 기반으로, 있는 그대로 대상 지역에 이동한 후 배포의 모양은 다음과 같습니다.

* **다양한 계층에 배포되는 단일 인스턴스 VM**

     ![여러 계층에 단일 인스턴스 VM 배포](media/move-vm-overview/single-zone.png)

* **가용성 집합에 배포되는 각 계층의 VM**

     ![지역 간 가용성 집합](media/move-vm-overview/crossregionaset.png)

* **가용성 영역 전반에 배포되는 각 계층의 VM**

     ![가용성 영역 전반에 VM 배포](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>VM을 이동하여 가용성 향상

* **다양한 계층에 배포되는 단일 인스턴스 VM**

     ![여러 계층에 단일 인스턴스 VM 배포](media/move-vm-overview/single-zone.png)

* **가용성 집합에 배포되는 각 계층의 VM**: Azure Site Recovery를 사용하여 VM에 대한 복제를 사용하도록 설정하면 가용성 집합의 VM을 별도의 가용성 영역으로 구성할 수 있습니다. 이동 작업이 완료된 후 가용성 SLA는 99.99%가 됩니다.

     ![가용성 집합 및 가용성 영역 전반에 VM 배포](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> 
> * [다른 지역으로 Azure VM 이동](azure-to-azure-tutorial-migrate.md)
> 
> * [가용성 영역으로 Azure VM 이동](move-azure-vms-avset-azone.md)

