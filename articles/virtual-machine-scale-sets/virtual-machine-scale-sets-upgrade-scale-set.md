---
title: Azure 가상 머신 확장 집합 수정 | Microsoft Docs
description: REST API, Azure PowerShell 및 Azure CLI로 Azure 가상 머신 확장 집합을 수정 및 업데이트하는 방법 알아보기
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: manayar
ms.openlocfilehash: 71899a9d6782c4700c287458c85ec83bd1516a4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803144"
---
# <a name="modify-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 수정

애플리케이션의 수명 주기 전반에 걸쳐 가상 머신 확장 집합을 수정하거나 업데이트해야 할 수도 있습니다. 이러한 업데이트에는 확장 집합의 구성을 업데이트하거나 애플리케이션 구성을 변경하는 방법이 포함될 수 있습니다. 이 문서에서는 REST API, Azure PowerShell 또는 Azure CLI를 사용하여 기존 확장 집합을 수정하는 방법에 대해 설명합니다.

## <a name="fundamental-concepts"></a>기본 개념

### <a name="the-scale-set-model"></a>확장 집합 모델
확장 집합에는 확장 집합의 *desired* 상태를 전체적으로 캡처하는 “확장 집합 모델”이 있습니다. 확장 집합의 모델을 쿼리하려는 경우 다음을 사용할 수 있습니다. 

- REST API에서 [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get), 아래 참조:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell에서 [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI에서 [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- [resources.azure.com](https://resources.azure.com) 또는 언어별 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용할 수도 있습니다.

정확한 출력 표시는 명령에 제공하는 옵션에 따라 달라집니다. 다음 예제는 Azure CLI의 압축된 샘플 출력입니다.

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

이러한 속성은 확장 집합에 전체적으로 적용됩니다.


### <a name="the-scale-set-instance-view"></a>확장 집합 인스턴스 보기
또한 확장 집합에는 확장 집합의 현재 *런타임* 상태를 캡처하는 “확장 집합 인스턴스 보기”가 있습니다. 확장 집합의 인스턴스 보기를 쿼리하려면 다음을 사용할 수 있습니다.

- REST API에서 [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview), 아래 참조:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell에서 [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI에서 [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- [resources.azure.com](https://resources.azure.com) 또는 언어별 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용할 수도 있습니다.

정확한 출력 표시는 명령에 제공하는 옵션에 따라 달라집니다. 다음 예제는 Azure CLI의 압축된 샘플 출력입니다.

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

이러한 속성은 확장 집합에 적용된 확장의 상태를 포함하여 확장 집합에 있는 VM의 현재 런타임 상태 요약을 제공합니다.


### <a name="the-scale-set-vm-model-view"></a>확장 집합 VM 모델 보기
확장 집합에는 모델 보기가 있는 것처럼, 확장 집합의 각 VM 인스턴스에도 고유한 모델 보기가 있습니다. 확장 집합의 특정 VM 인스턴스에 대한 모델 보기를 쿼리하려면 다음을 사용할 수 있습니다.

- REST API에서 [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get), 아래 참조:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell에서 [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI에서 [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- [resources.azure.com](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용할 수도 있습니다.

정확한 출력 표시는 명령에 제공하는 옵션에 따라 달라집니다. 다음 예제는 Azure CLI의 압축된 샘플 출력입니다.

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

이러한 속성은 확장 집합의 전체 구성이 아니라 확장 집합 내의 VM 인스턴스 구성을 설명합니다. 예를 들어, 확장 집합 모델에는 `overprovision` 속성이 있지만 확장 집합 내의 VM 인스턴스에 대한 모델에는 포함되지 않습니다. 이러한 차이는 과도 프로비전이 확장 집합의 개별 VM 인스턴스가 아닌 확장 집합 전체의 속성이기 때문에 나타납니다(과도 프로비전에 대한 자세한 내용은 [확장 집합 디자인 고려 사항](virtual-machine-scale-sets-design-overview.md#overprovisioning) 참조).


### <a name="the-scale-set-vm-instance-view"></a>확장 집합 VM 인스턴스 보기
확장 집합에는 인스턴스 보기가 있는 것처럼, 확장 집합의 각 VM 인스턴스에도 고유한 인스턴스 보기가 있습니다. 확장 집합 내의 특정 VM 인스턴스에 대한 인스턴스 보기를 쿼리하려면 다음을 사용할 수 있습니다.

- REST API에서 [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview), 아래 참조:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell에서 [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI에서 [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- [resources.azure.com](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용할 수도 있습니다.

정확한 출력 표시는 명령에 제공하는 옵션에 따라 달라집니다. 다음 예제는 Azure CLI의 압축된 샘플 출력입니다.

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

이러한 속성은 확장 집합에 적용되는 모든 확장을 포함하여 확장 집합 내에 있는 VM 인스턴스의 현재 런타임 상태를 설명합니다.


## <a name="how-to-update-global-scale-set-properties"></a>전역 확장 집합 속성을 업데이트하는 방법
전역 확장 집합 속성을 업데이트하려면 확장 집합 모델의 속성을 업데이트해야 합니다. 이러한 업데이트는 다음을 통해 수행할 수 있습니다.

- REST API에서 [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate), 아래 참조:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- REST API의 속성으로 Resource Manager 템플릿을 배포하여 전역 확장 집합 속성을 업데이트할 수 있습니다.

- Azure PowerShell에서 [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI에서 [az vmss update](/cli/azure/vmss):
    - 속성을 수정하려면:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - 확장 집합의 목록 속성에 개체를 추가하려면: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - 확장 집합의 목록 속성에서 개체를 제거하려면: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - `az vmss create` 명령을 사용하여 이전에 확장 집합을 배포한 경우 `az vmss create` 명령을 다시 실행하여 확장 집합을 업데이트할 수 있습니다. 수정하려는 속성을 제외하고 `az vmss create` 명령의 모든 속성이 이전과 같은지 확인합니다.

- [resources.azure.com](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용할 수도 있습니다.

확장 집합 모델이 업데이트되면 새 구성이 확장 집합에서 만들어진 모든 새 VM에 적용됩니다. 그러나 확장 집합의 기존 VM에 대한 모델은 최신 전체 확장 집합을 사용해서 여전히 최신 상태로 유지해야 합니다. 각 VM에 대한 모델에는 VM이 최신 전체 확장 집합 모델로 최신 상태를 유지하는지 여부를 나타내는 `latestModelApplied`라는 부울 속성이 있습니다(`true`는 VM이 최신 모델로 최신 상태를 유지하고 있음을 의미함).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>최신 확장 집합 모델로 VM을 최신 상태로 유지하는 방법
확장 집합에는 VM이 최신 확장 집합 모델로 최신 상태를 유지하는 방법을 결정하는 "업그레이드 정책"이 있습니다. 업그레이드 정책에 대한 세 가지 모드는 다음과 같습니다.

- **자동** - 이 모드에서 확장 집합은 가져오는 VM의 순서를 보장하지 않습니다. 확장 집합은 모든 VM을 동시에 제거할 수 있습니다. 
- **롤링** - 이 모드에서 확장 집합은 일괄 처리 사이에 선택적 일시 중지 시간을 유지하면서 업데이트를 일괄적으로 롤아웃합니다.
- **수동** - 이 모드에서 확장 집합 모델을 업데이트하면 기존 VM에 아무런 변화도 발생하지 않습니다.
 
기존 VM을 업데이트하려면 각 기존 VM의 "수동 업그레이드"를 수행해야 합니다. 다음을 통해 이 수동 업그레이드를 수행할 수 있습니다.

- REST API에서 [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances), 아래 참조:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell에서 [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI에서 [az vmss update-instances](/cli/azure/vmss):

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- 언어별 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용할 수도 있습니다.

>[!NOTE]
> Service Fabric 클러스터는 *자동* 모드만 사용할 수 있지만 업데이트는 다르게 처리됩니다. 자세한 내용은 [Service Fabric 응용 프로그램 업그레이드](../service-fabric/service-fabric-application-upgrade.md)합니다.

업그레이드 정책을 따르지 않는 전역 확장 집합 속성에 대한 한 가지 유형의 수정 작업이 있습니다. 확장 집합 OS 프로필(예: 관리자 사용자 이름 및 암호)을 변경하면 API 버전 *2017-12-01* 이상에서만 변경됩니다. 이러한 변경은 확장 집합 모델이 변경된 후에 만들어지는 VM에만 적용됩니다. 기존 VM을 최신 상태로 유지하려면 각 VM을 “이미지로 다시 설치”해야 합니다. 다음을 통해 VM을 이미지로 다시 설치할 수 있습니다.

- REST API에서 [compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage), 아래 참조:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell에서 [Set-AzVmssVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI에서 [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- 언어별 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용할 수도 있습니다.


## <a name="properties-with-restrictions-on-modification"></a>수정이 제한되는 속성

### <a name="create-time-properties"></a>만들기 시간 속성
일부 속성은 확장 집합을 만들 때만 설정할 수 있습니다. 이러한 속성은 다음과 같습니다.

- 가용성 영역
- 이미지 참조 게시자
- 이미지 참조 제공
- 관리되는 OS 디스크 저장소 계정 형식

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>현재 값에 따라서만 변경할 수 있는 속성
현재 값을 따른다는 점을 제외하고, 일부 속성을 변경할 수 있습니다. 이러한 속성은 다음과 같습니다.

- **singlePlacementGroup** - singlePlacementGroup이 true이면 false로 수정할 수 있습니다. 그러나 singlePlacementGroup이 false인 경우, true로 수정하지 **못할 수 있습니다**.
- **서브넷** - 확장 집합의 서브넷은 원래 서브넷과 새 서브넷이 동일한 가상 네트워크에 있는 경우 수정할 수 있습니다.

### <a name="properties-that-require-deallocation-to-change"></a>변경하기 위해 할당을 취소해야 하는 속성
일부 속성은 확장 집합의 VM을 할당 취소해야만 특정 값으로 변경할 수 있습니다. 이러한 속성은 다음과 같습니다.

- **SKU 이름** - 새 VM SKU가 현재 확장 집합이 있는 하드웨어에서 지원되지 않을 경우, SKU 이름을 수정하기 전에 확장 집합에서 VM 할당을 취소해야 합니다. 자세한 내용은 [Azure VM의 크기를 조정하는 방법](../virtual-machines/windows/resize-vm.md)을 참조하세요.


## <a name="vm-specific-updates"></a>VM 관련 업데이트
특정 수정 내용은 전역 확장 집합 속성 대신 특정 VM에 적용될 수 있습니다. 현재 지원되는 유일한 VM 관련 업데이트는 확장 집합의 VM에서 데이터 디스크 연결/분리 작업입니다. 이 기능은 미리 보기 상태입니다. 자세한 내용은 [미리 보기 설명서](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)를 참조하세요.


## <a name="scenarios"></a>시나리오

### <a name="application-updates"></a>애플리케이션 업데이트
애플리케이션이 확장을 통해 확장 집합에 배포되는 경우, 확장 구성을 업데이트하면 업그레이드 정책에 따라 애플리케이션이 업데이트됩니다. 예를 들어, 새 버전의 스크립트를 사용자 지정 스크립트 확장에서 실행되도록 하려면 새 스크립트를 가리키도록 *fileUris* 속성을 업데이트할 수 있습니다. 경우에 따라, 확장 구성이 변경되지 않더라도 강제로 업데이트하려고 할 수 있습니다(예: 스크립트의 URI를 변경하지 않고 스크립트 업데이트). 이러한 경우 업데이트를 강제로 수행하도록 *forceUpdateTag*를 수정할 수 있습니다. Azure 플랫폼에서는 이 속성을 해석하지 않습니다. 값을 변경해도 확장이 실행되는 방식에 영향을 미치지 않습니다. 변경하면 확장이 강제로 다시 실행되기만 합니다. *forceUpdateTag*에 대한 자세한 내용은 [확장에 대한 REST API 설명서](/rest/api/compute/virtualmachineextensions/createorupdate)를 참조하세요. *forceUpdateTag*는 사용자 지정 스크립트 확장뿐만 아니라 모든 확장에서 사용할 수 있습니다.

애플리케이션을 사용자 지정 이미지를 통해 배포하는 것도 일반적입니다. 이 시나리오는 다음 섹션에서 설명됩니다.

### <a name="os-updates"></a>OS 업데이트
Azure 플랫폼 이미지를 사용하는 경우 *imageReference*를 수정하여 이미지를 업데이트할 수 있습니다(자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate) 참조).

>[!NOTE]
> 플랫폼 이미지를 사용하는 경우 이미지 참조 버전으로 "최신"을 지정하는 것이 일반적입니다. VM을 만들고 규모를 확장하고 이미지로 다시 설치하는 동안 VM은 사용 가능한 최신 버전으로 만들어집니다. 하지만 시간이 지나면서 새 이미지 버전이 릴리스되면 OS 이미지가 자동으로 업데이트된다는 의미는 **아닙니다**. 자동 OS 업그레이드를 제공하는 별도의 기능이 현재 미리 보기 상태에 있습니다. 자세한 내용은 [자동 OS 업그레이드 설명서](virtual-machine-scale-sets-automatic-upgrade.md)를 참조하세요.

사용자 지정 이미지를 사용하는 경우 *imageReference* ID를 업데이트하여 이미지를 업데이트할 수 있습니다(자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate) 참조).

## <a name="examples"></a>예

### <a name="update-the-os-image-for-your-scale-set"></a>확장 집합에 대한 OS 이미지 업데이트
이전 버전의 Ubuntu LTS 16.04를 실행하는 확장 집합이 있을 수도 있습니다. Ubuntu LTS 16.04의 최신 버전(예: 버전 *16.04.201801090*)으로 업데이트하려고 합니다. 이미지 참조 버전 속성은 목록에 포함되어 있지 않으므로 다음 중 한 가지 명령으로 이러한 속성을 직접 수정할 수 있습니다.

- Azure PowerShell에서 [Update-AzVmss](/powershell/module/az.compute/update-azvmss), 아래 참조:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI에서 [az vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

또는 확장 집합에서 사용하는 이미지를 변경할 수 있습니다. 예를 들어 확장 집합에서 사용하는 사용자 지정 이미지를 업데이트하거나 변경할 수 있습니다. 이미지 참조 ID 속성을 업데이트하여 확장 집합에서 사용하는 이미지를 변경할 수 있습니다. 이미지 참조 ID 속성은 목록에 포함되어 있지 않으므로 다음 중 한 가지 명령으로 이 속성을 직접 수정할 수 있습니다.

- Azure PowerShell에서 [Update-AzVmss](/powershell/module/az.compute/update-azvmss), 아래 참조:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI에서 [az vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>확장 집합에 대한 부하 분산 장치 업데이트
Azure Load Balancer가 있는 확장 집합이 있으며 Azure Load Balancer를 Azure Application Gateway로 바꾸려고 한다고 가정해보겠습니다. 확장 집합에 대한 부하 분산 장치 및 Application Gateway 속성은 목록에 포함되어 있으므로 속성을 직접 수정하는 대신 명령을 사용하여 목록 요소를 제거하거나 추가할 수 있습니다.

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> 이러한 명령은 확장 집합에 IP 구성 및 Load Balancer가 하나만 있다고 가정합니다. 여러 개 있는 경우, *0*이 아닌 목록 인덱스를 사용해야 할 수 있습니다.


## <a name="next-steps"></a>다음 단계
[Azure CLI](virtual-machine-scale-sets-manage-cli.md) 또는 [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md)을 사용하여 확장 집합에서 일반 관리 작업을 수행할 수도 있습니다.
