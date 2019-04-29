---
title: Azure 가상 머신 확장 집합에 확장 시퀀스 사용 | Microsoft Docs
description: 가상 머신 확장 집합에 여러 개의 확장을 배포할 때 확장 프로비전 시퀀스를 지정하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: 2e5dfda16c4828b3113fc50d4cffc79fe6ff19e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620175"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>가상 머신 확장 집합의 확장 프로비전 시퀀스 지정
Azure 가상 머신 확장 집합은 배포 후 구성 및 관리, 모니터링, 보안 등의 기능을 제공합니다. 프로덕션 배포는 일반적으로 VM 인스턴스에 대해 구성된 여러 개의 확장 조합을 사용하여 원하는 결과를 얻습니다.

가상 머신에서 여러 개의 확장을 사용하는 경우 동일한 OS 리소스가 필요한 확장이 동시에 이러한 리소스 획득을 시도하지 않도록 하는 것이 중요합니다. 또한 일부 확장은 환경 설정, 비밀 등의 필수 구성을 제공하기 위해 다른 확장에 의존합니다. 올바른 순서 및 시퀀스가 없으면 종속 확장 배포가 실패할 수 있습니다.

이 문서에서는 가상 머신 확장 집합의 VM 인스턴스에 대해 구성할 확장의 시퀀스 지정 방법을 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다.
-   Azure 가상 머신 [확장](../virtual-machines/extensions/overview.md)
-   가상 머신 확장 집합 [수정](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-extension-sequencing"></a>확장 시퀀스를 사용해야 하는 경우
확장 집합에 확장 시퀀스가 꼭 필요한 것은 아니며, 지정하지 않을 경우 확장 집합 인스턴스에서 임의 순서로 확장이 프로비전될 수 있습니다.

예를 들어 확장 집합의 모델에 두 개의 확장(ExtensionA 및 ExtensionB)이 지정된 경우 다음 프로비전 시퀀스 중 하나가 발생할 수 있습니다.
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

애플리케이션에서 확장 A가 항상 확장 B 이전에 프로비전되어야 하는 경우 이 문서에 설명된 대로 확장 시퀀스를 사용해야 합니다. 확장 시퀀스를 사용하면 이제 하나의 시퀀스만 발생합니다.
-   ExtensionA - > ExtensionB

정의된 프로비전 시퀀스에 지정되지 않은 확장은 정의된 시퀀스 이전, 이후 또는 도중을 포함하여 언제든지 프로비전될 수 있습니다. 확장 시퀀스는 단지 특정 확장이 다른 특정 확장 이후에 프로비전되도록 지정합니다. 모델에 정의된 다른 확장의 프로비전에는 영향을 주지 않습니다.

예를 들어 확장 집합 모델에 세 개의 확장(확장 A, 확장 B, 확장 C)이 지정되어 있고 확장 C가 확장 A 이후에 프로비전되도록 설정된 경우 다음 프로비전 시퀀스 중 하나가 발생할 수 있습니다.
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

정의된 확장 시퀀스가 실행되는 동안 다른 확장이 프로비전되지 않도록 해야 하는 경우 확장 집합 모델에 포함된 모든 확장의 시퀀스를 지정하는 것이 좋습니다. 위 예제에서 하나의 시퀀스만 발생할 수 있게 하려면 확장 B가 확장 C 이후에 프로비전되도록 설정할 수 있습니다.
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>확장 시퀀스 사용 방법
확장 프로비전의 시퀀스를 지정하려면 확장 이름 배열을 허용하는 “provisionAfterExtensions” 속성을 포함하도록 확장 집합 모델의 확장 정의를 업데이트해야 합니다. 속성 배열 값에 언급된 확장은 확장 집합 모델에 완전히 정의되어 있어야 합니다.

### <a name="template-deployment"></a>템플릿 배포
다음 예제에서는 확장이 순서대로 프로비전되도록 확장 집합에 세 개의 확장(ExtensionA, ExtensionB, ExtensionC)이 있는 템플릿을 정의합니다.
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

“provisionAfterExtensions” 속성이 확장 이름 배열을 허용하므로 ExtensionC가 ExtensionA 및 ExtensionB 이후에 프로비전되지만 ExtensionA와 ExtensionB의 순서는 지정할 필요가 없도록 위 예제를 수정할 수 있습니다. 다음 템플릿을 사용하여 이 시나리오를 수행할 수 있습니다.

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
다음 예제에서는 확장 집합 모델에 ExtensionC라는 새 확장을 추가합니다. ExtensionC는 확장 집합 모델에 이미 정의되어 있는 ExtensionA와 ExtensionB에 종속됩니다.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

ExtensionC가 확장 집합 모델에서 먼저 정의되었고 지금 해당 종속성을 추가하려는 경우 `PATCH`를 실행하여 이미 배포된 확장의 속성을 편집할 수 있습니다.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
기존 확장 집합 인스턴스에 대한 변경 내용은 다음 [업그레이드](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 시 적용됩니다.

### <a name="azure-powershell"></a>Azure PowerShell
[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet을 사용하여 확장 집합 모델 정의에 애플리케이션 상태 확장을 추가합니다. 확장 시퀀스를 지정하려면 Az PowerShell 1.2.0 이상을 사용해야 합니다.

다음 예제에서는 Windows 기반 확장 집합의 확장 집합 모델에 있는 `extensionProfile`에 [애플리케이션 상태 확장](virtual-machine-scale-sets-health-extension.md)을 추가합니다. 확장 집합에 이미 정의된 [사용자 지정 스크립트 확장](../virtual-machines/extensions/custom-script-windows.md)을 프로비전한 후 애플리케이션 상태 확장이 프로비전됩니다.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set)를 사용하여 애플리케이션 상태 확장을 확장 집합 모델 정의에 추가합니다. 확장 시퀀스를 지정하려면 Azure CLI 2.0.55 이상을 사용해야 합니다.

다음 예제에서는 Windows 기반 확장 집합의 확장 집합 모델에 [애플리케이션 상태 확장](virtual-machine-scale-sets-health-extension.md)을 추가합니다. 확장 집합에 이미 정의된 [사용자 지정 스크립트 확장](../virtual-machines/extensions/custom-script-windows.md)을 프로비전한 후 애플리케이션 상태 확장이 프로비전됩니다.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>문제 해결

### <a name="not-able-to-add-extension-with-dependencies"></a>종속성이 있는 확장을 추가할 수 없나요?
1. provisionAfterExtensions에 지정된 확장이 확장 집합 모델에 정의되어 있는지 확인합니다.
2. 순환 종속성이 발생하지 않는지 확인합니다. 예를 들어 다음 시퀀스는 허용되지 않습니다. ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. 종속성이 있는 확장의 확장 “properties” 아래에 “settings” 속성이 있는지 확인합니다. 예를 들어 ExtentionB가 ExtensionA 이후에 프로비전되어야 하는 경우 ExtensionA의 ExtensionA “properties” 아래에 “settings” 필드가 있어야 합니다. 확장에 필수 설정이 없는 경우 비어 있는 “settings” 속성을 지정할 수 있습니다.

### <a name="not-able-to-remove-extensions"></a>확장을 제거할 수 없나요?
제거할 확장이 다른 확장의 provisionAfterExtensions 아래에 나열되지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
