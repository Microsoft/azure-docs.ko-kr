---
title: 변수의 여러 인스턴스 정의
description: 변수를 만들 때 Azure Resource Manager 템플릿에서 복사 작업을 사용 하 여 여러 번 반복 합니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 9e252a0b9721ffec99535c5d30e609e12e9e67eb
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210815"
---
# <a name="variable-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 변수 반복

이 문서에서는 Azure Resource Manager 템플릿에서 변수 값을 두 개 이상 만드는 방법을 보여 줍니다. **복사** 요소를 템플릿의 변수 섹션에 추가 하 여 배포 하는 동안 변수에 대 한 항목 수를 동적으로 설정할 수 있습니다. 템플릿 구문을 반복 하지 않아도 됩니다.

[리소스의](copy-properties.md)리소스 및 속성과 함께 [copy를 사용할](copy-resources.md) 수도 있습니다.

## <a name="variable-iteration"></a>변수 반복

Copy 요소의 일반적인 형식은 다음과 같습니다.

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**Name** 속성은 루프를 식별 하는 값입니다. **Count** 속성은 변수에 대해 원하는 반복 횟수를 지정 합니다.

**입력** 속성은 반복할 속성을 지정 합니다. **Input** 속성의 값에서 생성 된 요소의 배열을 만듭니다. 단일 속성 (예: 문자열) 또는 여러 속성이 있는 개체 일 수 있습니다.

다음 예제에서는 문자열 값의 배열을 만드는 방법을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

위의 템플릿은 다음 값을 포함 하는 배열을 반환 합니다.

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

다음 예제에서는 이름, diskSizeGB 및 diskIndex의 세 가지 속성을 사용 하 여 개체 배열을 만드는 방법을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

앞의 예제에서는 다음 값을 포함 하는 배열을 반환 합니다.

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> 변수 반복은 오프셋 인수를 지원 합니다. 오프셋은 copyIndex (' diskNames ', 1)와 같이 반복의 이름 뒤에와 야 합니다. 오프셋 값을 제공 하지 않으면 첫 번째 인스턴스의 경우 기본값은 0입니다.
>

변수 내에서 copy 요소를 사용할 수도 있습니다. 다음 예제에서는 배열을 해당 값 중 하나로 포함 하는 개체를 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

앞의 예제에서는 다음 값을 가진 개체를 반환 합니다.

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

다음 예에서는 변수를 사용 하 여 복사를 사용 하는 다양 한 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>복사 제한

개수는 800를 초과할 수 없습니다.

개수는 음수일 수 없습니다. Azure PowerShell 2.6 Azure CLI 이상 버전을 사용 하 여 템플릿을 배포 하거나, 2.0.74 이상 또는 REST API **2019-05-10** 이상 버전을 사용 하는 경우 count를 0으로 설정할 수 있습니다. 이전 버전의 PowerShell, CLI 및 REST API는 count에 대해 0을 지원 하지 않습니다.

## <a name="example-templates"></a>예제 템플릿

다음 예에서는 변수에 대 한 값을 두 개 이상 만드는 일반적인 시나리오를 보여 줍니다.

|템플릿  |Description  |
|---------|---------|
|[변수 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |변수를 반복하는 다양한 방법을 보여 줍니다. |
|[다중 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |네트워크 보안 그룹에 여러 보안 규칙을 배포합니다. 매개 변수에서 보안 규칙을 구성합니다. 매개 변수는 [여러 NSG 매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)을 참조합니다. |

## <a name="next-steps"></a>다음 단계

* 자습서를 살펴보려면 [자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](template-tutorial-create-multiple-instances.md)를 참조하세요.
* Copy 요소를 다른 용도로 사용 하는 경우 [Azure Resource Manager 템플릿에서 리소스 반복](copy-resources.md) 및 [Azure Resource Manager 템플릿의 속성 반복](copy-properties.md)을 참조 하세요.
* 템플릿 섹션에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](template-syntax.md)을 참조하세요.
* 템플릿 배포 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 애플리케이션 배포](deploy-powershell.md)를 참조하세요.

