---
title: 가용성 옵션
description: Azure에서 가상 머신을 실행 하기 위한 가용성 기능에 대해 알아봅니다.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 3826019a19aa744cb03737ed7f0c985f8063a1aa
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815609"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Azure에서 가상 머신의 가용성 옵션

이 문서에서는 Azure Vm (가상 머신)의 가용성 기능에 대 한 개요를 제공 합니다.

## <a name="high-availability"></a>고가용성

워크 로드는 일반적으로 다양 한 가상 컴퓨터에 분산 되어 높은 처리량, 성능을 확보 하 고 업데이트 또는 기타 이벤트로 인해 VM이 영향을 받는 경우 중복성을 만듭니다. 

고가용성을 위해 Azure에서 제공 하는 몇 가지 옵션이 있습니다. 먼저 기본 구문에 대해 살펴보겠습니다. 

### <a name="availability-zones"></a>가용성 영역

[가용성 영역](../availability-zones/az-overview.md)은 VM에 있는 애플리케이션 및 데이터의 가용성을 유지해야 하는 컨트롤 수준을 확장합니다. 가용성 영역은 Azure 지역 내에서 물리적으로 별도 영역입니다. 지원되는 Azure 지역당 3개의 가용성 영역이 있습니다. 

각 가용성 영역에는 고유한 소스, 네트워크 및 냉각 장치가 있습니다. 영역에 복제된 VM을 사용하는 솔루션을 설계하여 데이터 센터 손실로부터 앱과 데이터를 보호할 수 있습니다. 하나의 영역이 손상되면 다른 영역에서 복제된 앱 및 데이터를 즉시 사용할 수 있습니다. 

![가용성 영역](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

가용성 영역에서 [Windows](./windows/create-powershell-availability-zone.md) 또는 [Linux](./linux/create-cli-availability-zone.md) VM을 배포하는 방법에 대해 자세히 알아봅니다.


### <a name="fault-domains"></a>장애 도메인

장애 도메인은 온-프레미스 데이터 센터 내의 랙과 비슷하게 공통 전원 및 네트워크 스위치를 공유하는 기본 하드웨어의 논리적 그룹입니다. 

### <a name="update-domains"></a>업데이트 도메인

업데이트 도메인은 동시에 유지 관리를 진행하거나 다시 부팅될 수 있는 기본 하드웨어의 논리적 그룹입니다. 

이 방법을 통해 Azure 플랫폼이 정기적으로 유지 관리를 거치는 동안 애플리케이션에 있는 하나 이상의 인스턴스가 항상 실행됩니다. 다시 부팅 되는 업데이트 도메인의 순서는 유지 관리 중에 순차적으로 진행 되지 않을 수 있지만 한 번에 하나의 업데이트 도메인만 다시 부팅 됩니다.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines 크기 집합 

Azure 가상 머신 확장 집합을 사용하면 부하 분산된 VM의 그룹을 만들고 관리할 수 있습니다. VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다. 크기 집합은 응용 프로그램에 고가용성을 제공 하 고 다양 한 Vm을 중앙에서 관리, 구성 및 업데이트할 수 있도록 합니다. 항상 사용 가능한 응용 프로그램을 제공 하 고 [99.95% AZURE SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 충족 하기 위해 확장 집합 내에서 두 개 이상의 vm을 만드는 것이 좋습니다. 크기 집합 자체에 대 한 비용은 없으며 만든 각 VM 인스턴스에만 요금을 지불 합니다. 단일 VM이 [Azure 프리미엄 SSD](./disks-types.md#premium-ssd)를 사용하는 경우, 계획되지 않은 유지 관리 이벤트에 대해 Azure SLA가 적용됩니다. 규모 집합의 가상 머신은 여러 업데이트 도메인 및 장애 도메인에 배포 하 여 데이터 센터 중단, 계획 되거나 계획 되지 않은 유지 관리 이벤트로 인해 중단에 대 한 가용성 및 복원 력을 최대화할 수 있습니다. 확장 집합의 가상 머신은 단일 가용성 영역 또는 지역적으로 배포할 수도 있습니다. 가용성 영역 배포 옵션은 오케스트레이션 모드에 따라 다를 수 있습니다.

**장애 도메인 및 업데이트 도메인**

가상 머신 확장 집합은 장애 도메인 및 업데이트 도메인을 정렬 하 여 고가용성을 위한 설계를 간소화 합니다. 확장 집합에 대 한 장애 도메인 수를 정의 하기만 하면 됩니다. 확장 집합에 사용할 수 있는 장애 도메인 수는 지역에 따라 다를 수 있습니다. [Azure에서 가상 머신의 가용성 관리](./windows/manage-availability.md)를 참조 하세요.


## <a name="availability-sets"></a>가용성 집합
가용성 세트은 중복성과 가용성을 제공하기 위해 Azure에서 애플리케이션이 빌드되는 방식을 이해할 수 있도록 하는 데이터 센터에 있는 VM의 논리적 그룹입니다. 고가용성 애플리케이션을 제공하고 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 충족할 수 있도록 가용성 집합 내에 둘 이상의 VM을 만드는 것이 좋습니다. 가용성 집합 자체에 대한 비용은 없으므로 만드는 각 VM 인스턴스에 대해서만 요금을 지불합니다. 단일 VM이 [Azure 프리미엄 SSD](./disks-types.md#premium-ssd)를 사용하는 경우, 계획되지 않은 유지 관리 이벤트에 대해 Azure SLA가 적용됩니다.

가용성 집합에서 Vm은 이러한 장애 도메인에 자동으로 분산 됩니다. 이 방법은 잠재적인 물리적 하드웨어 오류, 네트워크 중단 또는 전원 중단의 영향을 제한합니다.

[Azure Managed Disks](./faq-for-disks.md)를 사용하는 VM의 경우, 관리 가용성 집합을 사용할 때 VM은 관리 디스크 장애 도메인에 맞춰집니다. 이러한 정렬은 VM에 연결된 모든 관리 디스크가 동일한 관리 디스크 장애 도메인 내에 있도록 합니다. 

관리 디스크의 VM만 관리 가용성 집합에서 만들어질 수 있습니다. 관리 디스크 장애 도메인의 수는 지역에 따라 다릅니다. 즉, 지역당 2개 또는 3개의 관리 디스크 장애 도메인이 있을 수 있습니다. 이러한 [Linux VM](./linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) 또는 [Windows VM](./windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)용 관리 디스크 장애 도메인에 대한 자세한 내용을 참조할 수 있습니다.

![관리 가용성 집합](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


가용성 집합 내의 Vm도 업데이트 도메인 간에 자동으로 분산 됩니다. 

![가용성 집합](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>다음 단계
이제 이러한 가용성 및 중복 기능을 사용하여 Azure 환경을 빌드하기 시작할 수 있습니다. 모범 사례 정보는 [Azure 가용성 모범 사례](/azure/architecture/checklist/resiliency-per-service)를 참조하세요.