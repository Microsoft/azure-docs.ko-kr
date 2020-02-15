---
title: 속성의 여러 인스턴스 정의
description: Azure Resource Manager 템플릿에서 복사 작업을 사용 하 여 리소스에 대 한 속성을 만들 때 여러 번 반복 합니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b1e31f981f361b4cfbe7e7930f2c70bfce8b8656
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210867"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 속성 반복

이 문서에서는 Azure Resource Manager 템플릿에서 속성의 인스턴스를 둘 이상 만드는 방법을 보여 줍니다. 템플릿에서 리소스의 속성 섹션에 **copy** 요소를 추가 하 여 배포 중에 속성의 항목 수를 동적으로 설정할 수 있습니다. 템플릿 구문을 반복 하지 않아도 됩니다.

[리소스](copy-resources.md) 및 [변수와](copy-variables.md)함께 copy를 사용할 수도 있습니다.

## <a name="property-iteration"></a>속성 반복

Copy 요소의 일반적인 형식은 다음과 같습니다.

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

**이름**에 만들려는 리소스 속성의 이름을 입력 합니다. **Count** 속성은 속성에 대해 원하는 반복 횟수를 지정 합니다.

**입력** 속성은 반복할 속성을 지정 합니다. **Input** 속성의 값에서 생성 된 요소의 배열을 만듭니다.

다음 예제는 가상 머신에서 `copy`를 dataDisks 속성에 적용하는 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

속성 반복 내에서 `copyIndex`를 사용하는 경우 반복의 이름을 제공해야 합니다.

> [!NOTE]
> 또한 속성 반복은 offset 인수를 지원 합니다. 오프셋은 copyIndex (' dataDisks ', 1)와 같이 반복의 이름 뒤에와 야 합니다.
>

Resource Manager는 배포 중 `copy` 배열을 확장합니다. 배열 이름은 속성의 이름이 됩니다. 입력 값은 개체 속성이 됩니다. 배포된 템플릿은 다음과 같습니다.

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

copy 요소는 배열이므로 리소스에 대해 1 초과 속성을 지정할 수 있습니다.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

리소스 및 속성 반복을 함께 사용할 수 있습니다. 이름별로 속성 반복을 참조하세요.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>복사 제한

개수는 800를 초과할 수 없습니다.

개수는 음수일 수 없습니다. Azure PowerShell 2.6 Azure CLI 이상 버전을 사용 하 여 템플릿을 배포 하거나, 2.0.74 이상 또는 REST API **2019-05-10** 이상 버전을 사용 하는 경우 count를 0으로 설정할 수 있습니다. 이전 버전의 PowerShell, CLI 및 REST API는 count에 대해 0을 지원 하지 않습니다.

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 속성에 대 한 값을 두 개 이상 만드는 일반적인 시나리오를 보여 줍니다.

|템플릿  |Description  |
|---------|---------|
|[가변적인 수의 데이터 디스크를 사용한 VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |가상 머신을 사용하여 여러 데이터 디스크를 배포합니다. |

## <a name="next-steps"></a>다음 단계

* 자습서를 살펴보려면 [자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](template-tutorial-create-multiple-instances.md)를 참조하세요.
* Copy 요소의 다른 용도는 [Azure Resource Manager 템플릿에서 리소스 반복](copy-resources.md) 및 [Azure Resource Manager 템플릿의 변수 반복](copy-variables.md)을 참조 하세요.
* 템플릿 섹션에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](template-syntax.md)을 참조하세요.
* 템플릿 배포 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 애플리케이션 배포](deploy-powershell.md)를 참조하세요.

