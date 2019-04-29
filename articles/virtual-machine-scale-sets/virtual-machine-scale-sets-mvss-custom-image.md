---
title: Azure 확장 집합 템플릿에서 사용자 지정 이미지 참조 | Microsoft Docs
description: 기존 Azure Virtual Machine Scale Set 템플릿에 사용자 지정 이미지를 추가하는 방법 알아보기
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
origin.date: 05/10/2017
ms.date: 11/30/2018
ms.author: v-junlch
ms.openlocfilehash: 2e3c8177a32082c251be74e597a18730ae1c9d37
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108380"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Azure 확장 집합 템플릿에 사용자 지정 이미지 추가

이 문서에서는 사용자 지정 이미지에서 배포할 [실행 가능한 최소 크기 집합 템플릿](./virtual-machine-scale-sets-mvss-start.md)을 수정하는 방법을 보여 줍니다.

## <a name="change-the-template-definition"></a>템플릿 정의 변경

실행 가능한 최소 확장 집합 템플릿은 [여기](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)에 있으며, 사용자 지정 이미지에서 확장 집합을 배포하기 위한 템플릿은 [여기](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json)에 있습니다. 이 템플릿(`git diff minimum-viable-scale-set custom-image`)을 하나씩 만드는 데 사용되는 diff에 대해 살펴보겠습니다.

### <a name="creating-a-managed-disk-image"></a>Managed Disk 이미지 만들기

사용자 지정 Managed Disk 이미지가 이미 있는 경우(`Microsoft.Compute/images` 형식 리소스) 이 섹션을 건너뛰어도 됩니다.

먼저 배포를 시작할 사용자 지정 이미지를 포함하는 Azure Storage의 일반화된 Blob의 URI에 해당하는 `sourceImageVhdUri` 매개 변수를 추가합니다.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

다음으로 `sourceImageVhdUri` URI에 있는 일반화된 Blob을 기준으로 하는 Managed Disk 이미지에 해당하는 `Microsoft.Compute/images` 형식의 리소스를 추가합니다. 이 이미지는 사용되는 확장 집합과 동일한 하위 지역에 있어야 합니다. 이미지의 속성에서 OS 운영 체제, blob의 위치(`sourceImageVhdUri` 매개 변수) 및 저장소 계정 유형을 지정합니다.

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

확장 집합 리소스에서 사용자 지정 이미지를 참조하는 추가 `dependsOn` 절을 추가하여 확장 집합이 해당 이미지에서의 배포를 시도하기 전에 이미지가 만들어지도록 합니다.

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Managed Disk 이미지를 사용하도록 확장 집합 속성 설정

확장 집합 `storageProfile`의 `imageReference`에서, 플랫폼 이미지의 게시자, 제품, SKU 및 버전을 지정하는 대신, `Microsoft.Compute/images` 리소스의 `id`를 지정합니다.

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

이 예제에서는 `resourceId` 함수를 사용하여 같은 템플릿에서 만들어진 이미지의 리소스 ID를 가져옵니다. Managed Disk 이미지를 미리 만든 경우 대신 해당 이미지의 ID를 제공해야 합니다. 이 ID는 `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>` 형식이어야 합니다.


## <a name="next-steps"></a>다음 단계

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

<!-- Update_Description: update metedata properties -->