---
title: Azure Resource Manager 확장 집합 템플릿을 변환하여 관리되는 디스크 사용 | Microsoft Docs
description: 확장 집합 템플릿을 변환하여 관리되는 디스크 확장 집합 템플릿을 사용합니다.
keywords: 가상 머신 크기 집합
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: b2d1738b85799079b3af7ab39c5cb1799a38d382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731740"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>확장 집합 템플릿을 변환하여 관리되는 디스크 확장 집합 템플릿 사용

확장 집합을 만드는 데 관리되는 디스크를 사용하지 않고 Resource Manager 템플릿을 사용하는 고객은 관리되는 디스크를 사용하도록 수정하려고 할 수 있습니다. 이 문서에서는 샘플 Resource Manager 템플릿용 커뮤니티 중심 리포지토리 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates)에서 끌어오기 요청을 예로 사용하여 관리 디스크를 수행하는 방법을 보여 줍니다. 전체 끌어오기 요청은 [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998)에서 찾을 수 있으며 차이점 관련 부분은 설명과 함께 아래에 있습니다.

## <a name="making-the-os-disks-managed"></a>관리되는 OS 디스크 만들기

아래 diff에서 저장소 계정 및 디스크 속성에 관련된 몇 가지 변수가 제거됩니다. Storage 계정 유형은 더 이상 필요하지 않지만(Standard_LRS가 기본값) 원하는 경우 여전히 지정할 수 있었습니다. Standard_LRS 및 Premium_LRS는 관리되는 디스크로만 지원됩니다. 새 저장소 계정 접미사, 고유 문자열 배열 및 sa 수는 저장소 계정 이름을 생성하는 기존 템플릿에 사용되었습니다. 관리되는 디스크는 고객을 대신하여 저장소 계정을 자동으로 만들기 때문에 이러한 변수는 새 템플릿에 더 이상 필요하지 않습니다. 마찬가지로 관리 디스크는 기본 저장소 Blob 컨테이너 및 디스크의 이름을 자동으로 지정하므로 vhd 컨테이너 이름 및 OS 디스크 이름은 더 이상 필요하지 않습니다.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


아래 diff에서 계산 API 버전이 확장 집합이 지원되는 관리 디스크에 필요한 가장 오래된 버전인 2016-04-30-미리 보기로 업데이트됩니다. 원하는 경우 기존 구문을 사용하여 새 API 버전에서 관리되지 않는 디스크를 여전히 사용할 수 있었습니다. 계산 API 버전만을 업데이트하고 다른 내용을 변경하지 않는 경우 템플릿은 이전처럼 계속해서 작동해야 합니다.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

아래 diff에서 저장소 계정 리소스가 리소스 배열에서 완전히 제거됩니다. 관리 디스크는 자동으로 생성하기 때문에 리소스는 더 이상 필요하지 않습니다.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

아래 diff에서 확장 집합에서 저장소 계정을 만들고 있던 루프까지 참조하는 절에 따라 제거하고 있는 것을 볼 수 있습니다. 기존 템플릿에서 확장 집합이 만들기를 시작하기 전에 저장소 계정이 만들어졌던 것을 보장했지만 이 절은 관리되는 디스크에서 더 이상 필요하지 않습니다. 또한 vhd 컨테이너 속성 및 OS 디스크 이름 속성은 관리 디스크에 의해 내부에서 자동으로 처리되므로 해당 속성을 제거합니다. 프리미엄 OS 디스크를 원하면 “osDisk” 구성에 `"managedDisk": { "storageAccountType": "Premium_LRS" }`를 추가할 수 있었습니다. VM sku의 대문자 또는 소문자 ‘s’를 사용하는 VM만 프리미엄 디스크를 사용할 수 있습니다.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

확장 집합 구성에는 관리되거나 관리되지 않는 디스크를 사용할 것인지에 대한 명시적 속성이 없습니다. 확장 집합은 저장소 프로필에 있는 속성에 따라 사용할 것을 파악합니다. 따라서 올바른 속성이 확장 집합의 저장소 프로필에 있도록 템플릿을 수정하는 경우 중요합니다.


## <a name="data-disks"></a>데이터 디스크

위의 변경 내용으로 확장 집합은 OS 디스크에 관리되는 디스크를 사용하지만 데이터 디스크의 경우는 어떻습니까? 데이터 디스크를 추가하려면 "osDisk"와 같은 수준인 "storageProfile" 아래에 "dataDisks" 속성을 추가합니다. 속성의 값은 다음 예제와 같이 개체의 JSON 목록이며 각각에는 "lun"(VM에서 데이터 디스크마다 고유해야 함), "createOption"("empty"는 현재 지원되는 유일한 옵션임) 및 "diskSizeGB"(기가바이트 단위의 디스크 크기, 0보다 크고 1024보다 작아야 함) 속성이 있습니다.

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

이 배열에 `n`개의 디스크를 지정하는 경우 확장 집합의 각 VM은 `n`개의 데이터 디스크를 가져옵니다. 그러나 이러한 데이터 디스크는 원시 디바이스입니다. 포맷되지 않습니다. 사용하기 전에 디스크를 연결, 파티션 및 포맷하는 것은 고객에게 달려 있습니다. 필요에 따라 각 데이터 디스크 개체에 `"managedDisk": { "storageAccountType": "Premium_LRS" }`를 지정하여 프리미엄 데이터 디스크가 되어야 하도록 지정할 수도 있습니다. VM sku의 대문자 또는 소문자 ‘s’를 사용하는 VM만 프리미엄 디스크를 사용할 수 있습니다.

확장 집합으로 데이터 디스크 사용에 대한 자세한 내용은 [이 문서](./virtual-machine-scale-sets-attached-disks.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
확장 집합을 사용하는 예제 리소스 관리자 템플릿은 [Azure 빠른 시작 템플릿 GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 “vmss”를 검색하세요.

일반적인 정보는 [확장 집합에 대한 주 방문 페이지](https://azure.microsoft.com/services/virtual-machine-scale-sets/)를 확인하세요.

