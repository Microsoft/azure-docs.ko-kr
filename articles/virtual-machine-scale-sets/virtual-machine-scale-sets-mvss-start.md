---
title: 가상 머신 확장 집합 템플릿에 대해 알아보기 | Microsoft Docs
description: 가상 머신 확장 집합에 대한 실행 가능한 최소 확장 집합 템플릿을 만드는 방법 알아보기
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: d4a3dd6ae390fd48a8085cca33063a6bb74bd96c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805570"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>가상 머신 확장 집합 템플릿에 대해 알아보기
[Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)은 관련된 리소스 그룹을 배포하는 유용한 방법입니다. 이 자습서 시리즈에서는 실행 가능한 최소 확장 집합 템플릿을 만드는 방법과 이러한 템플릿을 다양한 시나리오에 맞게 수정하는 방법을 보여 줍니다. 모든 예제는 [GitHub 리포지토리](https://github.com/gatneil/mvss)에서 가져온 것입니다. 

이 템플릿은 간단하게 제작되었습니다. 확장 집합 템플릿의 전체 예제를 보려면 [Azure 빠른 시작 템플릿 GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)를 참조하고 문자열 `vmss`가 포함된 폴더를 검색하세요.

템플릿 생성에 익숙한 경우 "다음 단계" 섹션으로 건너뛰어 템플릿을 수정하는 방법을 볼 수 있습니다.

## <a name="review-the-template"></a>템플릿 검토

GitHub를 사용하여 실행 가능한 최소 확장 집합 템플릿 [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)을 검토합니다.

이 자습서에서는 diff(`git diff master minimum-viable-scale-set`)를 검토하여 실행 가능한 최소 확장 집합 템플릿을 하나씩 만듭니다.

## <a name="define-schema-and-contentversion"></a>$schema 및 contentVersion 정의
먼저 템플릿에서 `$schema` 및 `contentVersion`을 정의합니다. `$schema` 요소는 템플릿 언어의 버전을 정의하고 Visual Studio 구문 강조 표시 및 유사한 유효성 검사 기능에 사용됩니다. `contentVersion` 요소는 Azure에 사용되지 않습니다. 대신 템플릿 버전을 추적하는 데 도움이 됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>매개 변수 정의
다음으로 두 개의 매개 변수인 `adminUsername` 및 `adminPassword`를 정의합니다. 매개 변수는 배포 시 사용자가 지정하는 값입니다. `adminUsername` 매개 변수는 단순히 `string` 형식이지만 `adminPassword`가 비밀이기 때문에 `securestring` 형식을 부여합니다. 이러한 매개 변수는 나중에 확장 집합 구성에 전달됩니다.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>변수 정의
Resource Manager 템플릿을 사용하여 나중에 템플릿에 사용할 변수를 정의할 수도 있습니다. 이 예제에서는 변수를 사용하지 않기 때문에 JSON 개체가 비어 있습니다.

```json
  "variables": {},
```

## <a name="define-resources"></a>리소스 정의
다음은 템플릿의 리소스 섹션입니다. 여기에 실제 배포할 항목을 정의합니다. `parameters` 및 `variables`(JSON 개체)과 달리 `resources`는 JSON 개체의 JSON 목록입니다.

```json
   "resources": [
```

모든 리소스에는 `type`, `name`, `apiVersion` 및 `location` 속성이 필요합니다. 이 예제의 첫 번째 리소스는 형식이 [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks)이고, 이름은 `myVnet`이며, apiVersion은 `2016-03-30`입니다. (리소스 형식에 대한 최신 API 버전을 찾으려면 [Azure Resource Manager 템플릿 참조](/azure/templates/)를 확인하세요.)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>위치 지정
가상 네트워크의 위치를 지정하려면 [Resource Manager 템플릿 함수](../azure-resource-manager/resource-group-template-functions.md)를 사용합니다. 이 함수는 `"[<template-function>]"`처럼 따옴표와 대괄호로 묶어야 합니다. 이 경우에는 `resourceGroup` 함수를 사용합니다. 인수는 취하지 않고 이 배포가 배포될 리소스 그룹에 대한 메타데이터를 포함하는 JSON 개체를 반환합니다. 리소스 그룹은 배포 시에 사용자가 설정합니다. 그런 후 이 값을 `.location`으로 이 JSON 개체에 인덱싱하여 JSON 개체에서의 위치를 가져옵니다.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>가상 네트워크 속성 지정
각 Resource Manager 리소스에는 해당 리소스 관련 구성에 대한 자체 `properties` 섹션이 있습니다. 이 경우에는 가상 네트워크에 개인 IP 주소 범위 `10.0.0.0/16`을 사용하는 하나의 서브넷만 있도록 지정합니다. 확장 집합은 항상 하나의 서브넷에 포함되며 여러 서브넷에 걸쳐 있을 수 없습니다.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>dependsOn 목록 추가
필수 `type`, `name`, `apiVersion`, `location` 속성 외에도 각 리소스는 선택적인 `dependsOn` 목록 문자열을 가질 수 있습니다. 이 목록은 이 리소스를 배포하기 전에 이 배포의 다른 리소스가 완료해야 하는 것을 지정합니다.

이 경우 목록에 요소가 하나만 있으며 해당 요소는 이전 예제의 가상 네트워크입니다. VM을 만들려면 먼저 확장 집합에 네트워크가 있어야 하므로 이러한 종속성을 지정합니다. 이러한 방식으로 확장 집합은 네트워크 속성에 이전에 지정된 IP 주소 범위의 개인 IP 주소를 이러한 VM에 제공할 수 있습니다. dependsOn 목록에 있는 각 문자열의 형식은 `<type>/<name>`입니다. 가상 네트워크 리소스 정의에 이전에 사용된 것과 동일한 `type` 및 `name`을 사용합니다.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>확장 집합 속성 지정
확장 집합에는 VM을 사용자 지정하기 위한 속성이 많이 있습니다. 이러한 속성의 전체 목록은 [템플릿 참조](/azure/templates/microsoft.compute/virtualmachinescalesets)에서 확인하세요. 이 자습서에서는 일반적으로 사용되는 몇 가지 속성만 설정됩니다.
### <a name="supply-vm-size-and-capacity"></a>VM 크기 및 용량 제공
확장 집합은 만들 VM의 크기("sku 이름") 및 이러한 크기로 만들려는 VM의 수("sku 용량")를 알아야 합니다. 사용 가능한 VM 크기를 확인하려면 [VM 크기 설명서](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes)를 참조하세요.

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>업데이트 유형 선택
또한 확장 집합은 확장 집합의 업데이트 처리 방법을 알아야 합니다. 현재 `Manual` 및 `Automatic`의 두 가지 옵션이 있습니다. 두 옵션 사이의 차이점에 대한 자세한 내용은 [확장 집합을 업그레이드하는 방법](./virtual-machine-scale-sets-upgrade-scale-set.md)에 대한 설명서를 참조하세요.

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>VM 운영 체제 선택
확장 집합은 VM에 적용할 운영 체제도 알아야 합니다. 여기서는 완벽하게 패치된 Ubuntu 16.04 LTS 이미지를 사용하여 VM을 만듭니다.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>computerNamePrefix 지정
확장 집합은 다수의 VM을 배포합니다. 각각의 VM 이름을 지정하는 대신 `computerNamePrefix`를 지정합니다. 확장 집합은 각 VM의 접두사에 인덱스를 추가하기 때문에 VM 이름은 `<computerNamePrefix>_<auto-generated-index>` 형식입니다.

이 코드 조각에서는 이전 매개 변수를 사용하여 확장 집합의 모든 VM에 대한 관리자 사용자 이름 및 암호를 설정합니다. 이 프로세스에서는 `parameters` 템플릿 함수를 사용합니다. 이 함수는 참조할 매개 변수를 지정하는 문자열을 취하여 해당 매개 변수의 값을 출력합니다.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>VM 네트워크 구성 지정
마지막으로 크기 집합의 VM에 대한 네트워크 구성을 지정합니다. 이 경우 이전에 만든 서브넷의 ID만 지정하면 됩니다. 이렇게 하면 확장 집합이 이 서브넷의 네트워크 인터페이스를 넣습니다.

`resourceId` 템플릿 함수를 사용하여 서브넷을 포함하는 가상 네트워크의 ID를 얻을 수 있습니다. 이 함수는 리소스의 형식 및 이름을 취한 후 해당 리소스의 정규화된 식별자를 반환합니다. 이 ID는 `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>` 형식입니다.

그러나 가상 네트워크의 식별자로는 충분하지 않습니다. 확장 집합 VM이 위치할 구체적인 서브넷을 제공합니다. 이 작업을 수행하려면 `/subnets/mySubnet`을 가상 네트워크의 ID에 연결합니다. 결과는 서브넷의 정규화된 ID입니다. 이 연결은 일련의 문자열을 취한 후 해당 연결을 반환하는 `concat` 함수를 사용하여 수행합니다.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
