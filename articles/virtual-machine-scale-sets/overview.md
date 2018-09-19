---
title: Azure 가상 머신 확장 집합 개요 | Microsoft Docs
description: Azure 가상 머신 확장 집합 및 응용 프로그램의 크기를 자동으로 조정하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/27/2018
ms.author: negat
ms.openlocfilehash: 8ded9b20bd70d18b8a68df0c9775f3a56f8b185b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
ms.locfileid: "30247378"
---
# <a name="what-are-virtual-machine-scale-sets"></a>가상 머신 확장 집합이란?
Azure 가상 머신 확장 집합을 사용하면 부하 분산된 동일한 VM 그룹을 만들고 관리할 수 있습니다. VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다. 확장 집합은 응용 프로그램에 고가용성을 제공하고 많은 수의 VM을 중앙에서 관리, 구성 및 업데이트할 수 있게 합니다. 가상 머신 확장 집합을 사용하면 컴퓨팅, 빅 데이터 및 컨테이너 작업과 같은 영역에 대한 대규모 서비스를 구축할 수 있습니다.


## <a name="why-use-virtual-machine-scale-sets"></a>가상 머신 확장 집합을 사용하는 이유는?
중복성과 향상된 성능을 제공하기 위해 응용 프로그램은 일반적으로 여러 인스턴스 간에 분산됩니다. 고객은 응용 프로그램 인스턴스 중 하나에 요청을 배포하는 부하 분산 장치를 통해 응용 프로그램에 액세스할 수 있습니다. 유지 관리를 수행하거나 응용 프로그램 인스턴스를 업데이트해야 하는 경우, 고객은 사용 가능한 다른 응용 프로그램 인스턴스에 배포되어야 합니다. 추가 고객 요구에 맞추려면 응용 프로그램을 실행하는 응용 프로그램 인스턴스의 수를 늘려야 할 수도 있습니다.

Azure 가상 머신 확장 집합은 많은 VM에서 실행되는 응용 프로그램, [리소스의 자동 크기 조정](virtual-machine-scale-sets-autoscale-overview.md) 및 트래픽 부하 분산에 대한 관리 기능을 제공합니다. 확장 집합에서 제공하는 주요 이점은 다음과 같습니다.

- **손쉬운 여러 VM 만들기 및 관리**
    - 응용 프로그램을 실행하는 VM을 많이 사용하는 경우 환경 전체에서 일관된 구성을 유지해야 합니다. 신뢰할 수 있는 응용 프로그램 성능을 위해 VM 크기, 디스크 구성 및 응용 프로그램 설치가 모든 VM에서 일치해야 합니다.
    - 확장 집합을 사용하면 동일한 기본 OS 이미지 및 구성에 모든 VM 인스턴스가 만들어집니다. 이 방법을 사용하면 추가 구성 작업 또는 네트워크 관리 없이 수백 개의 VM을 쉽게 관리할 수 있습니다.
    - 확장 집합은 기본 4계층 트래픽 분산에는 [Azure 부하 분산 장치](../load-balancer/load-balancer-overview.md)를 사용하고, 고급 7계층 트래픽 분산 및 SSL 종료에는 [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)를 사용하도록 지원합니다.

- **고가용성 및 응용 프로그램 복원력 제공**
    - 확장 집합은 응용 프로그램의 여러 인스턴스를 실행하는 데 사용됩니다. 이러한 VM 인스턴스 중 하나에 문제가 있는 경우, 고객은 중단을 최소화하면서 다른 VM 인스턴스 중 하나를 통해 응용 프로그램에 계속 액세스합니다.
    - 추가 가용성을 위해 [가용성 영역](../availability-zones/az-overview.md)을 사용하여 단일 데이터 센터 또는 여러 데이터 센터 내에서 VM 인스턴스를 확장 집합에 자동으로 배포할 수 있습니다.

- **리소스 수요 변화에 따라 자동으로 응용 프로그램 크기 조정**
    - 응용 프로그램에 대한 고객의 요구는 하루 또는 일주일 내내 변할 수 있습니다. 고객 요구를 맞추기 위해 확장 집합은 응용 프로그램 수요가 증가함에 따라 VM 인스턴스 수를 자동으로 늘린 다음, 수요가 감소함에 따라 VM 인스턴스 수를 줄일 수 있습니다.
    - 또한 자동 크기 조정은 수요가 낮을 때 응용 프로그램을 실행하는 불필요한 VM 인스턴스의 수를 최소화하는 한편, 수요가 증가하고 추가 VM 인스턴스가 자동으로 추가될 때 고객이 허용 가능한 수준의 성능을 계속 확보할 수 있습니다. 이 기능은 필요에 따라 비용을 절감하고 Azure 리소스를 효율적으로 만드는 데 도움이 됩니다.

- **대규모 작업**
    - 확장 집합은 최대 1,000개의 VM 인스턴스를 지원합니다. 사용자 고유의 사용자 지정 VM 이미지를 만들고 업로드하는 경우 300개 VM 인스턴스로 제한됩니다.
    - 프로덕션 작업에서 최상의 성능을 얻으려면 [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) 및 [Premium Storage](../virtual-machines/windows/premium-storage.md)를 사용합니다.


## <a name="differences-between-virtual-machines-and-scale-sets"></a>가상 머신과 확장 집합 간의 차이점
확장 집합은 가상 머신에서 작성됩니다. 확장 집합을 사용하면 응용 프로그램을 실행하고 크기를 조정할 수 있는 관리 및 자동화 계층이 제공됩니다. 개별 VM을 수동으로 만들고 관리하거나 기존 도구를 통합하여 비슷한 수준의 자동화를 구축할 수 있습니다. 다음 표에서는 여러 VM 인스턴스를 수동으로 관리하는 것과 비교한 확장 집합의 이점을 간략히 설명합니다.

| 시나리오                           | 수동적인 VM 그룹                                                                    | 가상 머신 크기 집합 |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| 추가 VM 인스턴스 추가        | 수동으로 생성하고, 구성하고, 규정 준수를 확인합니다.                             | 자동으로 중앙 구성에서 생성합니다. |
| 트래픽 분산 및 배포 | 수동으로 Azure 부하 분산 장치 또는 Application Gateway를 만들고 구성합니다.      | 자동으로 Azure 부하 분산 장치 또는 Application Gateway를 만들고 통합할 수 있습니다. |
| 고가용성 및 중복성   | 수동으로 가용성 집합을 만들거나 가용성 영역 전체에 VM을 배포하고 추적합니다. | 자동으로 가용성 영역 또는 가용성 집합에서 VM 인스턴스를 배포합니다. |
| VM 크기 조정                     | 수동 모니터링 및 Azure Automation                                                 | 자동으로 호스트 메트릭, 인 게스트(in-guest) 메트릭, Application Insights 또는 일정에 따라 크기를 조정합니다. |

확장 집합은 추가 비용 없이 사용할 수 있습니다. VM 인스턴스, 부하 분산 장치 또는 Managed Disk 저장소와 같은 기본 컴퓨팅 리소스에 대해서만 비용을 지불합니다. 자동 크기 조정 및 중복성과 같은 관리 및 자동화 기능은 VM 사용에 대한 추가 요금이 발생하지 않습니다.


## <a name="next-steps"></a>다음 단계
시작하려면 Azure Portal에서 첫 번째 가상 머신 확장 집합을 만듭니다.

> [!div class="nextstepaction"]
> [Azure Portal에서 확장 집합 만들기](quick-create-portal.md)
