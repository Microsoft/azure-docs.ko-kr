---
title: Azure 가상 머신 확장 집합에 애플리케이션 상태 확장 사용 | Microsoft Docs
description: 애플리케이션 상태 확장을 사용하여 가상 머신 확장 집합에 배포된 애플리케이션의 상태를 모니터링하는 방법을 알아봅니다.
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
ms.openlocfilehash: d1cff1011e190e5fbb2874657cbdfbdc68bde0c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619827"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>가상 머신 확장 집합에 애플리케이션 상태 확장 사용
애플리케이션 상태 모니터링은 배포 관리 및 업그레이드에 대한 중요한 신호입니다. Azure 가상 머신 확장 집합은 배포를 업그레이드하기 위해 개별 인스턴스의 상태 모니터링을 사용하는 [자동 OS 이미지 업그레이드](virtual-machine-scale-sets-automatic-upgrade.md)를 포함한 [롤링 업그레이드](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)를 지원합니다.

이 문서에서는 애플리케이션 상태 확장을 사용하여 가상 머신 확장 집합에 배포된 애플리케이션의 상태를 모니터링하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다.
-   Azure 가상 머신 [확장](../virtual-machines/extensions/overview.md)
-   가상 머신 확장 집합 [수정](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-the-application-health-extension"></a>애플리케이션 상태 확장을 사용하는 경우
애플리케이션 상태 확장은 가상 머신 확장 집합 인스턴스 내에 배포되고, 확장 집합 인스턴스 내부에서 VM 상태를 보고합니다. 애플리케이션 엔드포인트를 검색하고 해당 인스턴스의 애플리케이션 상태를 업데이트하도록 확장을 구성할 수 있습니다. Azure에서 이 인스턴스 상태를 확인하여 해당 인스턴스가 업그레이드 작업에 적합한지 여부를 결정합니다.

확장은 VM 내에서 상태를 보고하므로 Application Health Probes(사용자 지정 Azure Load Balancer [프로브](../load-balancer/load-balancer-custom-probe-overview.md) 활용)와 같은 외부 프로브를 사용할 수 없는 경우에 사용할 수 있습니다.

## <a name="extension-schema"></a>확장 스키마

애플리케이션 상태 확장에 대한 스키마를 보여 주는 JSON은 다음과 같습니다. 확장을 사용하려면 적어도 연결된 포트 또는 요청 경로가 각각 있는 "tcp" 또는 "http" 요청이 필요합니다.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 | 데이터 형식
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | 문자열 |
| 형식 | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows`(Windows) | 문자열 |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>설정

| 이름 | 값/예제 | 데이터 형식
| ---- | ---- | ----
| protocol | `http` 또는 `tcp` | 문자열 |
| 포트 | 프로토콜이 `http`인 경우 선택 항목이고, 프로토콜이 `tcp`인 경우 필수 항목입니다. | int |
| requestPath | 프로토콜이 `http`인 경우 필수 항목이고, 프로토콜이 `tcp`인 경우 허용되지 않습니다. | 문자열 |

## <a name="deploy-the-application-health-extension"></a>애플리케이션 상태 확장 배포
아래 예제에서 자세히 설명한 대로 애플리케이션 상태 확장은 확장 집합에 여러 가지 방법으로 배포할 수 있습니다.

### <a name="rest-api"></a>REST API

다음 예제에서는 Windows 기반 확장 집합의 확장 집합 모델에서 myHealthExtension이라는 애플리케이션 상태 확장을 extensionProfile에 추가합니다.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
이미 배포된 확장을 편집하려면 `PATCH`를 사용합니다.

### <a name="azure-powershell"></a>Azure PowerShell

[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet을 사용하여 애플리케이션 상태 확장을 확장 집합 모델 정의에 추가합니다.

다음 예제에서는 Windows 기반 확장 집합의 확장 집합 모델에서 애플리케이션 상태 확장을 `extensionProfile`에 추가합니다. 이 예제에서는 새 Az PowerShell 모듈을 사용합니다.

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
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

[az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set)를 사용하여 애플리케이션 상태 확장을 확장 집합 모델 정의에 추가합니다.

다음 예제에서는 애플리케이션 상태 확장을 Windows 기반 확장 집합의 확장 집합 모델에 추가합니다.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>문제 해결
확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

또한 로그는 정기적으로 애플리케이션 상태를 캡처합니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
