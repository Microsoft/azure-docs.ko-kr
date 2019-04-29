---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 3c008e77116a9b42a2ea137069529c5e241adddd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456876"
---
[가상 머신 확장 집합](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)과 [Azure Monitor의 자동 조정 기능](../articles/azure-monitor/platform/autoscale-overview.md)을 사용하면 [VM(가상 머신)](../articles/virtual-machines/windows/overview.md)을 쉽게 [자동으로 크기 조정](../articles/azure-monitor/platform/autoscale-best-practices.md)할 수 있습니다. VM은 자동으로 크기 조정되도록 설정된 확장 집합의 멤버여야 합니다. 이 문서에서는 자동 및 수동 방법을 사용하여 VM을 수직 및 수평으로 확장하는 방법을 더 잘 이해할 수 있는 정보를 제공합니다.

## <a name="horizontal-or-vertical-scaling"></a>수평 또는 수직적 크기 조정

Azure Monitor의 자동 크기 조정 기능은 수평으로만 확장하며, 이는 VM 수를 늘리거나("규모 확장") 줄입니다("규모 감축"). 수평적 크기 조정은 부하를 처리하기 위해 잠재적으로 수천 개의 VM을 실행할 수 있으므로 클라우드 상황에서 더 유연합니다. 자동 또는 수동으로 확장 집합의 용량(또는 인스턴스 수)을 변경하여 수평으로 크기 조정합니다. 

수직적 크기 조정은 VM 수를 동일하게 유지하지만 VM을 강하게 만들거나("강화") 약하게 만듭니다("규모 축소"). 성능은 메모리, CPU 속도 또는 디스크 공간과 같은 특성으로 측정됩니다. 수직적 크기 조정은 더 큰 하드웨어의 가용성에 따라 달라지며, 상한값에 빠르게 도달하여 지역에 따라 다를 수 있습니다. 또한 수직적 규모 조정은 일반적으로 VM 중지 및 재시작이 필요합니다. 확장 집합의 VM 구성에 새 크기를 설정하여 수직으로 크기 조정합니다.

[Azure Automation](../articles/automation/automation-intro.md)에서 Runbook을 사용하면 [확장 집합](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md)으로 VM을 쉽게 강화하거나 규모 축소할 수 있습니다.

## <a name="create-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 만들기

확장 집합을 사용하면 동일한 VM을 집합으로 쉽게 배포하고 관리할 수 있습니다. [Azure Portal](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md) 또는 [Azure CLI](../articles/virtual-machines/linux/tutorial-create-vmss.md)를 사용하여 Linux 또는 Windows 확장 집합을 만들 수 있습니다. 또한 [Python](https://azure.microsoft.com/develop/python/) 또는 [Node.js](/nodejs/azure)와 같은 SDK를 사용하거나 [REST API](/rest/api/compute/virtualmachinescalesets)를 직접 사용하여 확장 집합을 만들고 관리할 수도 있습니다. VM의 자동 크기 조정은 확장 집합에 메트릭 및 규칙을 적용하여 수행됩니다.

## <a name="configure-autoscale-for-a-scale-set"></a>확장 집합에 대한 자동 크기 조정 구성

자동 크기 조정에서는 애플리케이션의 부하를 처리하는 데 적절한 VM 수를 제공합니다. 이렇게 하면 VM을 추가하여 늘어난 부하를 처리하고, 유휴 상태로 있는 VM을 제거하여 비용을 절약할 수 있습니다. 규칙 집합에 따라 실행할 VM의 최소 및 최대 수를 지정합니다. 최소값을 설정하면 애플리케이션이 항상 부하가 적은 상태에서 실행될 수 있습니다. 최대값을 설정하면 시간당 가능한 총 비용이 제한됩니다.

[Azure PowerShell](../articles/azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings) 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings)를 사용하여 확장 집합을 만들 때 자동 크기 조정을 사용하도록 설정할 수 있습니다. 확장 집합을 만든 후에도 이와 같이 설정할 수 있습니다. [Azure Resource Manager 템플릿](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)을 사용하여 확장 집합을 만들고, 확장을 설치하고, 자동 크기 조정을 구성할 수 있습니다. Azure Portal에서 Azure Monitor를 통해 자동 크기 조정을 사용하도록 설정하거나, 확장 집합 설정에서 자동 크기 조정을 사용하도록 설정합니다.

![자동 크기 조정 사용](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>메트릭

Azure Monitor의 자동 크기 조정 기능을 사용하면 [메트릭](../articles/azure-monitor/platform/autoscale-common-metrics.md)에 따라 실행 중인 VM 수를 크기 조정할 수 있습니다. 기본적으로 VM은 디스크, 네트워크 및 CPU 사용량에 대한 기본 호스트 수준 메트릭을 제공합니다. 진단 확장을 사용하여 진단 데이터 컬렉션을 구성하면 디스크, CPU 및 메모리에 대한 추가 게스트 OS 성능 카운터를 사용할 수 있게 됩니다.

![메트릭 조건](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

애플리케이션에서 호스트를 통해 사용할 수 없는 메트릭에 기반하여 크기 조정해야 하는 경우 확장 세트의 VM에는 [Linux 진단 확장](../articles/virtual-machines/linux/diagnostic-extension.md) 또는 [Windows 진단 확장](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)이 설치되어 있어야 합니다. Azure Portal을 사용하여 확장 집합을 만드는 경우 Azure PowerShell 또는 Azure CLI를 사용하여 필요한 진단 구성으로 확장을 설치해야 합니다.
 
### <a name="rules"></a>규칙

[규칙](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)은 수행할 작업과 메트릭을 결합합니다. 규칙 조건이 충족되면 하나 이상의 자동 크기 조정 동작이 트리거됩니다. 예를 들어 평균 CPU 사용량이 85%를 초과하면 VM 수를 하나씩 늘리도록 정의된 규칙이 있을 수 있습니다.

![자동 크기 조정 작업](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>공지

[트리거를 설정](../articles/azure-monitor/platform/autoscale-webhook-email.md)하여 특정 웹 URL을 호출하거나 만든 자동 크기 조정 규칙에 따라 전자 메일을 보낼 수 있습니다. Webhook를 사용하면 사후 처리 또는 사용자 지정 알림을 위해 Azure 경고 알림을 다른 시스템으로 라우팅할 수 있습니다.

## <a name="manually-scale-vms-in-a-scale-set"></a>확장 집합에서 VM 크기를 수동으로 조정

### <a name="horizontal"></a>수평적 크기 조정

확장 집합의 용량을 변경하여 VM을 추가하거나 제거할 수 있습니다. Azure Portal에서 크기 조정 화면의 재정의 조건 표시줄을 왼쪽이나 오른쪽으로 밀어 확장 집합의 VM 수를 줄이거나 늘릴 수 있습니다(**인스턴스 수**로 표시됨).

Azure PowerShell을 사용하면 [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss)를 사용하여 확장 집합 개체를 가져와야 합니다. 그런 다음, **sku.capacity** 속성을 원하는 VM 수로 설정하고 [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss)를 통해 확장 집합을 업데이트합니다. Azure CLI를 사용하면 [az vmss scale](/cli/azure/vmss?view=azure-cli-latest#az-vmss-scale) 명령에 대한 **--new-capacity** 매개 변수를 통해 용량을 변경합니다.

### <a name="vertical"></a>Vertical

Azure Portal에서 확장 집합에 대한 크기 조정 화면에서 VM의 크기를 수동으로 변경할 수 있습니다. Azure PowerShell을 Get-AzVmss와 함께 사용하여 이미지 참조 SKU 속성을 설정한 다음, [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) 및 [Update-AzVmssInstance](https://docs.microsoft.com/powershell/module/az.compute/update-azvmssinstance)를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [확장 집합에 대한 설계 고려 사항](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md)에서 확장 집합에 대해 자세히 알아봅니다.

