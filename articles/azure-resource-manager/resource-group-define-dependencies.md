---
title: Azure 리소스에 대한 배포 순서 설정 | Microsoft Docs
description: 리소스가 올바른 순서대로 배포되도록 배포 중 다른 리소스에 종속된 것으로 리소스를 설정하는 방법에 대해 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: tomfitz
ms.openlocfilehash: 91325b7884eae4c6f4c85c142b1e81cf2121c039
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103808"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿에서 리소스를 배포하는 순서 정의
주어진 리소스에 대해 해당 리소스를 배포하기 전에 존재해야 하는 다른 리소스가 있을 수 있습니다. 예를 들어 SQL 데이터베이스를 배포하려면 SQL Server가 있어야 합니다. 하나의 리소스를 다른 리소스에 종속된 것으로 표시하여 이 관계를 정의합니다. 종속성은 **dependsOn** 요소를 사용하거나 **reference** 함수를 사용하여 정의합니다. 

Resource Manager는 리소스 간의 종속성을 평가한 후 종속된 순서에 따라 리소스를 배포합니다. 리소스가 서로 종속되어 있지 않은 경우 Resource Manager는 이를 병렬로 배포합니다. 동일한 템플릿에 배포되는 리소스에 대한 종속성만 정의하면 됩니다. 

자습서의 경우 [자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)를 참조하세요.

## <a name="dependson"></a>dependsOn
템플릿 내에서 dependsOn 요소를 사용하면 하나의 리소스를 하나 이상의 리소스에 종속된 것으로 정의할 수 있습니다. 값은 리소스 이름의 쉼표로 구분된 목록일 수 있습니다. 

다음 예제에서는 부하 분산 장치, 가상 네트워크 및 여러 저장소 계정을 만드는 루프에 종속된 가상 머신 확장 집합을 보여 줍니다. 이러한 다른 리소스는 다음 예제에 표시되어 있지 않지만 템플릿 내 다른 곳에 존재해야 합니다.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

앞의 예에서 종속성은 **storageLoop**라는 복사 루프를 통해 생성되는 리소스에 포함됩니다. 예제는 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

종속성을 정의할 때 모호성을 피하기 위해 리소스 공급자 네임스페이스 및 리소스 형식을 포함할 수 있습니다. 예를 들어 다른 리소스와 동일한 이름을 가질 수 있는 부하 분산 장치 및 가상 네트워크를 명확히 하려면 다음 형식을 사용합니다.

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

dependsOn을 사용하여 리소스 간의 관계를 매핑하도록 선택할 수 있지만 왜 그렇게 하는지에 대한 이유를 이해하는 것이 중요합니다. 예를 들어, 리소스가 상호 연결되는 방식을 문서화하려면, dependsOn은 올바른 접근 방법이 아닙니다. 배포 후 dependsOn 요소에 어떤 리소스가 정의되었는지 쿼리할 수 없습니다. dependsOn을 사용하면 Resource Manager는 종속성이 있는 두 리소스를 병렬로 배포하지 않으므로 배포 시간에 잠재적으로 영향을 줍니다. 

## <a name="child-resources"></a>자식 리소스
resources 속성을 사용하면 정의되는 리소스에 관련된 자식 리소스를 지정할 수 있습니다. 자식 리소스는 5개 수준 깊이까지만 정의할 있습니다. 자식 리소스 및 부모 리소스 간에 암시적 배포 종속성을 만들어지지 않습니다는 두는 것이 반드시 합니다. 부모 리소스 다음에 자식 리소스를 배포해야 하는 경우 dependsOn 속성을 사용하여 해당 종속성을 확실하게 명시해야 합니다. 

각 부모 리소스는 특정 리소스 종류만 자식 리소스로 허용합니다. 허용되는 리소스 종류는 부모 리소스의 [템플릿 스키마](https://github.com/Azure/azure-resource-manager-schemas)에서 지정됩니다. 자식 리소스 종류의 이름에는 부모 리소스 종류의 이름이 포함됩니다. 예를 들어 **Microsoft.Web/sites/config**와 **Microsoft.Web/sites/extensions**는 둘 다 **Microsoft.Web/sites**의 자식 리소스입니다.

다음 예제에서는 SQL Server 및 SQL 데이터베이스를 보여 줍니다. 데이터베이스가 서버의 자식인 경우에도 SQL 데이터베이스와 SQL Server 간에 명시적 종속성이 정의되어 있습니다.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>reference 및 list 함수
[reference 함수](resource-group-template-functions-resource.md#reference) 를 사용하면 식을 다른 JSON 이름 및 값 쌍 또는 런타임 리소스에서 해당 값을 파생시키는 식을 작성할 수 있습니다. [list* 함수](resource-group-template-functions-resource.md#list)는 목록 작업에서 리소스에 대한 값을 반환합니다.  참조 및 목록 식은 참조된 리소스가 동일한 템플릿에 배포되고 해당 이름(리소스 ID 아님)에 따라 참조되는 경우 한 리소스가 다른 리소스에 종속되도록 암시적으로 선언합니다. 리소스 ID를 reference 또는 list 함수로 전달하는 경우 암시적 참조가 생성되지 않습니다.

reference 함수의 일반 형식은 다음과 같습니다.

```json
reference('resourceName').propertyPath
```

listKeys 함수의 일반 형식은 다음과 같습니다.

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

다음 예제에서 CDN 엔드포인트는 CDN 프로필에 명시적으로 종속되고 웹앱에 암시적으로 종속됩니다.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

이 요소 또는 dependsOn 요소를 사용하여 종속성을 지정할 수 있지만 같은 종속 리소스에 대해 두 가지를 모두 사용할 필요는 없습니다. 가능하면 불필요한 종속성을 추가하지 않도록 암시적 참조를 사용합니다.

자세한 내용은 [reference 함수](resource-group-template-functions-resource.md#reference)를 참조하세요.

## <a name="circular-dependencies"></a>순환적 종속성

Resource Manager는 템플릿의 유효성을 검사하는 동안 순환적 종속성을 식별합니다. 순환적 종속성이 존재한다는 오류가 표시되면 템플릿을 평가하여 불필요한 종속성이 있는지, 제거할 수 있는지를 확인합니다. 종속성을 제거하는 것으로 해결되지 않는다면 일부 배포 작업을 순환적 종속성이 있는 리소스 다음에 배포된 자식 리소스로 이동하여 순환적 종속성을 피할 수 있습니다. 예를 들어 두 개의 가상 머신을 배포하지만 각 컴퓨터에 서로를 참조하는 속성을 설정해야 한다고 가정합니다. 이런 경우 다음과 같은 순서로 배포할 수 있습니다.

1. vm1
2. vm2
3. vm1의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm2에서 얻은 값을 vm1에 설정합니다.
4. vm2의 확장은 vm1 및 vm2에 종속됩니다. 이 확장은 vm1에서 얻은 값을 vm2에 설정합니다.

배포 순서를 평가하고 종속성 오류를 해결하는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 자습서를 살펴보려면 [자습서: 종속 리소스가 있는 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)를 참조하세요.
* 종속성을 설정할 때의 권장 사항은 [Azure Resource Manager 템플릿 모범 사례](template-best-practices.md)를 참조하세요.
* 배포 중 종속성 문제 해결에 대해 알아보려면 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
* Azure 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요. 
* 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.

