---
title: Azure Resource Manager 템플릿 구조 및 구문 | Microsoft Docs
description: 선언적 JSON 구문을 사용하여 Azure Resource Manager 템플릿의 구조 및 속성을 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: tomfitz
ms.openlocfilehash: 94ed3c876ece827e4decd2b5b14332f5e854ab83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728041"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 구조 및 구문 이해

이 문서에서는 Azure Resource Manager 템플릿의 구조에 대해 설명합니다. 여기서는 템플릿의 다른 섹션 및 해당 섹션에서 사용할 수 있는 속성을 보여 줍니다. 템플릿은 배포에 대한 값을 생성하는 데 사용할 수 있는 식과 JSON으로 구성됩니다.

이 문서에서는 Resource Manager 템플릿 사용 하 여에 대해 알고 있는 사용자를 위한 것입니다. 구조 및 템플릿의 구문에 대 한 자세한 정보를 제공합니다. 템플릿 만들기 소개를 참조 하세요 [첫 번째 Azure Resource Manager 템플릿 만들기](resource-manager-create-first-template.md)합니다.

## <a name="template-format"></a>템플릿 형식

가장 간단한 구조의 템플릿에 포함되는 요소는 다음과 같습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| $schema |예 |템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다.<br><br> 리소스 그룹 배포의 경우 `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`을 사용합니다.<br><br>구독 배포의 경우 `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`을 사용합니다. |
| contentVersion |예 |템플릿의 버전입니다(예: 1.0.0.0). 이 요소에 값을 제공할 수 있습니다. 이 값을 사용하여 템플릿에서 중요한 변경 내용을 문서화할 수 있습니다. 템플릿을 사용하여 리소스를 배포할 때 이 값을 사용하면 정확한 템플릿이 사용되도록 할 수 있습니다. |
| apiProfile |아닙니다. | 리소스 종류에 대 한 API 버전 컬렉션으로 사용 되는 API 버전입니다. 템플릿에서 각 리소스에 대 한 API 버전을 지정할 필요가 없도록 하려면이 값을 사용 합니다. API 프로필 버전을 지정 하 고 리소스 형식에 대 한 API 버전을 지정 하지 않는 Resource Manager 프로필에 정의 된 해당 리소스 종류에 대 한 API 버전을 사용 합니다.<br><br>API 프로필 속성이 Azure Stack 및 전역 Azure와 같은 다른 환경에 템플릿을 배포할 때 특히 유용 합니다. 서식 파일에 두 환경 모두에서 지원 되는 버전을 자동으로 사용 하도록 하려면 API 프로필 버전을 사용 합니다. 현재 API 프로필 버전 및 API 버전 프로필에 정의 된 리소스의 목록을 참조 하세요 [API 프로필](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)합니다.<br><br>자세한 내용은 [API 프로필을 사용 하 여 버전 추적](templates-cloud-consistency.md#track-versions-using-api-profiles)합니다. |
| [매개 변수](#parameters) |아닙니다. |배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값입니다. |
| [variables](#variables) |아닙니다. |템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값입니다. |
| [functions](#functions) |아닙니다. |템플릿 내에서 사용할 수 있는 사용자 정의 함수입니다. |
| [resources](#resources) |예 |리소스 그룹 또는 구독에 배포되거나 업데이트되는 리소스 종류입니다. |
| [outputs](#outputs) |아닙니다. |배포 후 반환되는 값입니다. |

각 요소에는 사용자가 설정할 수 있는 속성이 있습니다. 이 기사에서는 템플릿의 섹션에 대해 자세히 설명합니다.

## <a name="syntax"></a>구문

템플릿의 기본 구문은 JSON이지만, 그러나 식 템플릿 내에서 사용할 수 있는 JSON 값 확장을 사용할 수 있습니다.  시작 하 고 대괄호를 사용 하 여 종료 하는 식: `[` 고 `]`, 각각. 식의 값은 템플릿을 배포할 때 평가됩니다. 식은 문자열, 정수, 부울, 배열 또는 개체를 반환할 수 있습니다. 다음 예제에서는 매개 변수의 기본 값에는 식을 보여 줍니다.

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

식에서 구문 `resourceGroup()` Resource Manager 템플릿 내에서 사용 하기 위해 제공 하는 함수 중 하나를 호출 합니다. JavaScript에서와 마찬가지로 함수 호출은 `functionName(arg1,arg2,arg3)`과 같이 형식이 지정됩니다. 구문을 `.location` 해당 함수에서 반환 되는 개체에서 하나의 속성을 검색 합니다.

템플릿 함수 및 해당 매개 변수는 대/소문자를 구분하지 않습니다. 예를 들어 Resource Manager에서 **variables('var1')** 와 **VARIABLES('VAR1')** 는 동일합니다. 계산될 때 함수는 대/소문자를 명시적으로 수정하지 않는 한(toUpper 또는 toLower 등) 대/소문자를 보존합니다. 특정 리소스 유형에는 함수가 계산되는 방식에 관계없이 대/소문자 요구 사항이 있을 수 있습니다.

리터럴 문자열을 대괄호 `[`로 시작하되, 식으로 해석되지 않게 하려면 문자 `[[`로 시작하도록 추가 대괄호를 추가합니다.

문자열 값을 매개 변수로 함수에 전달 하려면 작은따옴표를 사용 합니다.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

템플릿에서 JSON 개체를 추가 하는 등 식에서 큰따옴표를 이스케이프 하려면 백슬래시를 사용 합니다.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

템플릿 식은 24,576 자를 초과할 수 없습니다.

템플릿 함수의 전체 목록을 보려면 [Azure 리소스 관리자 템플릿 함수](resource-group-template-functions.md)를 참조하세요. 

## <a name="parameters"></a>매개 변수

템플릿의 매개 변수 섹션에서는 리소스를 배포할 때 입력할 수 있는 값을 지정합니다. 이러한 매개 변수 값을 사용하여 개발, 테스트 및 프로덕션 등의 특정 환경에 맞게 조정되는 값을 제공함으로써 배포를 사용자 지정할 수 있습니다. 템플릿에서 매개 변수를 제공할 필요는 없지만 매개 변수가 없으면 템플릿이 항상 이름, 위치 및 속성이 같은 동일한 리소스를 배포합니다.

템플릿에서 매개 변수는 256개로 제한됩니다. 이 문서에 표시된 것처럼 여러 속성이 포함된 개체를 사용하여 매개 변수 수를 줄일 수 있습니다.

### <a name="available-properties"></a>사용 가능한 속성

매개 변수에 대해 사용 가능한 속성 다음과 같습니다.

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>" 
    }
  }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| parameterName |예 |매개 변수의 이름입니다. 유효한 JavaScript 식별자여야 합니다. |
| 형식 |예 |매개 변수 값의 유형입니다. 허용되는 유형 및 값은 **string**, **securestring**, **int**, **bool**, **object**, **secureObject** 및 **array**입니다. |
| defaultValue |아닙니다. |매개 변수 값을 제공하지 않는 경우 매개 변수의 기본값입니다. |
| allowedValues |아닙니다. |올바른 값을 제공하도록 매개 변수에 대해 허용되는 값의 배열입니다. |
| minValue |아닙니다. |Int 형식 매개 변수의 최소값이며, 이 값이 포함됩니다. |
| maxValue |아닙니다. |Int 형식 매개 변수의 최대값이며, 이 값이 포함됩니다. |
| minLength |아닙니다. |string, securestring 및 array 형식 매개 변수의 최소 길이이며, 이 값이 포함됩니다. |
| maxLength |아닙니다. |string, securestring 및 array 형식 매개 변수의 최대 길이이며, 이 값이 포함됩니다. |
| description |아닙니다. |포털에서 사용자에게 표시되는 매개 변수의 설명입니다. 자세한 내용은 [템플릿의 주석](#comments)을 참조하세요. |

### <a name="define-and-use-a-parameter"></a>매개 변수 정의 및 사용

다음 예제에서는 간단한 매개 변수 정의를 보여 줍니다. 매개 변수 이름을 정의하고 문자열 값을 사용하도록 지정합니다. 매개 변수는 용도에 적합한 값만 허용합니다. 매개 변수는 배포 중에 값을 제공하지 않으면 기본값을 지정합니다. 마지막으로 매개 변수에는 용도에 대한 설명이 포함됩니다.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

템플릿에서 다음 구문을 사용하여 매개 변수 값을 참조합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>매개 변수가 있는 템플릿 함수

매개 변수에 기본값을 지정하는 경우 대부분의 템플릿 함수를 사용할 수 있습니다. 다른 매개 변수 값을 사용하여 기본값을 빌드할 수 있습니다. 다음 템플릿은 기본값인 함수의 사용을 보여 줍니다.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

매개 변수 섹션에서는 `reference` 함수를 사용할 수 없습니다. 매개 변수는 배포 전에 평가되므로 `reference` 함수에서 리소스의 런타임 상태를 가져올 수 없습니다. 

### <a name="objects-as-parameters"></a>개체를 매개 변수로 사용

관련 값을 개체로 전달하면 더 쉽게 구성할 수 있습니다. 이렇게 하면 템플릿의 매개 변수 수도 줄어듭니다.

템플릿에서 매개 변수를 정의하고 배포 중에 단일 값 대신 JSON 개체를 지정하세요. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

그런 다음 점 연산자를 사용하여 매개 변수의 하위 속성을 참조하세요.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>매개 변수 예제 템플릿

이러한 예제 템플릿은 매개 변수 사용에 대한 일부 시나리오를 보여 줍니다. 다른 시나리오에서 처리되는 방식을 테스트하려면 매개 변수를 배포하세요.

|Template  |설명  |
|---------|---------|
|[parameters with functions for default values](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json)(기본값에 대한 함수가 있는 매개 변수) | 매개 변수의 기본값을 정의할 때 템플릿 함수를 사용하는 방법을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |
|[parameter object](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json)(매개 변수 개체) | 매개 변수에 대한 개체 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |

## <a name="variables"></a>variables

변수 섹션에서 템플릿을 통해 사용할 수 있는 값을 생성합니다. 변수를 정의할 필요는 없지만 종종 변수를 통해 복잡한 식을 줄이면 템플릿이 단순화됩니다.

### <a name="available-definitions"></a>사용 가능한 정의

다음 예에서는 변수를 정의 하기 위한 사용 가능한 옵션을 보여 줍니다.

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

에 대 한 내용은 `copy` 변수에 대 한 여러 값을 만들려면 참조 [변수 반복](resource-group-create-multiple.md#variable-iteration)합니다.

### <a name="define-and-use-a-variable"></a>변수를 정의하고 사용합니다.

다음 예제는 변수 정의를 보여 줍니다. 저장소 계정 이름의 문자열 값을 만듭니다. 매개 변수 값을 가져오는 몇 가지 템플릿 함수를 사용 하 고 고유 문자열에 연결 합니다.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

리소스를 정의할 때 변수를 사용합니다.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>구성 변수

복합 JSON 형식을 사용하여 환경에 대한 관련 값을 정의할 수 있습니다.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

매개 변수에서 사용할 구성 값을 나타내는 값을 만듭니다.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

다음과 같이 현재 설정을 검색합니다.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>변수 예제 템플릿

이러한 예제 템플릿은 변수 사용에 대한 일부 시나리오를 보여 줍니다. 변수가 다른 시나리오에서 처리되는 방식을 테스트하려면 변수를 배포하세요. 

|Template  |설명  |
|---------|---------|
| [변수 정의](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 다양한 변수 형식을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 변수 값을 구성하고 해당 값을 반환합니다. |
| [구성 변수](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 구성 값을 정의하는 변수의 사용을 보여 줍니다. 템플릿은 리소스를 배포하지 않으며, 변수 값을 구성하고 해당 값을 반환합니다. |
| [네트워크 보안 규칙](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) 및 [매개 변수 파일](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 보안 규칙을 네트워크 보안 그룹에 할당하기 위한 올바른 형식으로 배열을 구성합니다. |


## <a name="functions"></a>Functions

템플릿 내에서 함수를 직접 만들 수 있습니다. 이러한 함수는 템플릿에서 사용할 수 있습니다. 일반적으로 템플릿 전체에서 반복하지 않으려는 복잡한 식을 정의합니다. 템플릿에서 지원되는 식 및 [함수](resource-group-template-functions.md)에서 사용자 정의 함수를 만듭니다.

사용자 함수를 정의할 때는 다음과 같은 몇 가지 제한 사항이 있습니다.

* 함수는 변수에 액세스할 수 없습니다.
* 함수는 함수에 정의된 매개 변수만 사용할 수 있습니다. 사용 하는 경우는 [매개 변수 함수](resource-group-template-functions-deployment.md#parameters) 해당 함수에 대 한 매개 변수를 제한 하는 사용자 정의 함수 내에서.
* 함수는 다른 사용자 정의 함수를 호출할 수 없습니다.
* 함수는 [참조 함수](resource-group-template-functions-resource.md#reference)를 사용할 수 없습니다.
* 함수의 매개 변수는 기본값을 가질 수 없습니다.

함수는 템플릿 함수와 이름 충돌을 피하기 위해 네임스페이스 값이 필요합니다. 다음 예제에서는 저장소 계정 이름을 반환하는 함수를 보여줍니다.

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

다음을 사용하여 함수를 호출합니다.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>리소스
리소스 섹션에서 배포되거나 업데이트되는 리소스를 정의합니다.

### <a name="available-properties"></a>사용 가능한 속성

다음과 같은 구조를 사용하여 리소스를 정의합니다.

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| condition | 아닙니다. | 리소스가 이 배포 중 프로비전되는지 여부를 나타내는 부울 값입니다. `true`인 경우 리소스는 배포하는 동안 만들어집니다. `false`인 경우 리소스는 이 배포에 대해 건너뛰어집니다. 참조 [조건을](#condition)합니다. |
| apiVersion |예 |리소스를 만들 때 사용하는 REST API의 버전입니다. 사용 가능한 값을 확인 하려면 참조 [템플릿 참조](/azure/templates/)합니다. |
| 형식 |예 |리소스 유형입니다. 이 값은 리소스 공급자의 네임스페이스와 리소스 형식을 조합한 값입니다(예: **Microsoft.Storage/storageAccounts**). 사용 가능한 값을 확인 하려면 참조 [템플릿 참조](/azure/templates/)합니다. 자식 리소스에 대 한 형식의 형식을 부모 리소스 내에 중첩 된 부모 리소스 외부 정의 했거나이 있는지 여부에 따라 달라 집니다. 참조 [자식 리소스](#child-resources)합니다. |
| 이름 |예 |리소스의 이름입니다. 이 이름은 RFC3986에 정의된 URI 구성 요소 제한을 따라야 합니다. 또한 리소스 이름을 외부에 노출하는 Azure 서비스는 다른 ID를 스푸핑하려는 시도가 아님을 확인하기 위해 이름의 유효성을 검사합니다. 자식 리소스에 대 한 이름 형식은 부모 리소스 내에 중첩 된 부모 리소스 외부 정의 했거나이 있는지 여부에 따라 달라 집니다. 참조 [자식 리소스](#child-resources)합니다. |
| location |다름 |제공된 리소스의 지역적 위치를 지원합니다. 사용 가능한 위치 중 하나를 선택할 수 있지만 대개는 사용자에게 가까운 하나를 선택하는 것이 좋습니다. 일반적으로 동일한 지역에서 서로 상호 작용하도록 리소스를 배치하는 것도 좋습니다. 대부분의 리소스 종류에는 위치가 필요하지만 일부 종류(예: 역할 할당)에는 위치가 필요하지 않습니다. |
| tags |아닙니다. |리소스와 연결된 태그입니다. 태그를 적용하여 구독에서 리소스를 논리적으로 구성합니다. |
| 설명 |아닙니다. |템플릿에서 리소스를 문서화하는 내용에 대한 참고입니다. 자세한 내용은 [템플릿의 주석](resource-group-authoring-templates.md#comments)을 참조하세요. |
| 복사 |아닙니다. |인스턴스가 둘 이상 필요한 경우 만드는 리소스의 수입니다. 기본 모드는 병렬입니다. 모든 리소스를 동시에 배포하지 않으려면 직렬 모드를 지정합니다. 자세한 내용은 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요. |
| dependsOn |아닙니다. |이 리소스를 배포하기 전에 배포해야 하는 리소스입니다. Resource Manager는 리소스 간의 종속성을 평가한 후 올바른 순서에 따라 리소스를 배포합니다. 리소스는 서로 종속되지 않을 경우, 병렬로 배포됩니다. 이 값은 리소스 이름 또는 리소스 고유 식별자의 쉼표로 구분된 목록입니다. 이 템플릿에 배포된 리소스만 나열합니다. 이 템플릿에 정의되지 않은 리소스는 이미 존재해야 합니다. 불필요한 종속성은 배포 속도를 느리게 만들고 순환 종속성을 만들기 때문에 추가하지 않습니다. 종속성 설정에 대한 지침은 [Azure Resource Manager 템플릿에서 종속성 정의](resource-group-define-dependencies.md)를 참조하세요. |
| properties |아닙니다. |리소스별 구성 설정입니다. 속성의 값은 리소스를 만들기 위해 REST API 작업(PUT 메서드)에 대한 요청 본문에 제공하는 값과 동일합니다. 복사 배열을 지정하여 속성의 여러 인스턴스를 만들 수도 있습니다. 사용 가능한 값을 확인 하려면 참조 [템플릿 참조](/azure/templates/)합니다. |
| sku | 아닙니다. | 일부 리소스에서는 SKU를 정의하는 값을 허용합니다. 예를 들어 저장소 계정에 대한 중복 유형을 지정할 수 있습니다. |
| kind | 아닙니다. | 일부 리소스에서는 배포하는 리소스 종류를 정의하는 값을 허용합니다. 예를 들어 만들 Cosmos DB 종류를 지정할 수 있습니다. |
| 계획 | 아닙니다. | 일부 리소스에서는 배포할 계획을 정의하는 값을 허용합니다. 예를 들어 가상 머신에 대한 마켓플레이스 이미지를 지정할 수 있습니다. | 
| 리소스 |아닙니다. |정의 중인 리소스에 종속되는 하위 리소스입니다. 부모 리소스의 스키마에서 허용되는 리소스 유형만 제공합니다. 부모 리소스에 대한 종속성은 암시되지 않습니다. 해당 종속성을 명시적으로 정의해야 합니다. 참조 [자식 리소스](#child-resources)합니다. |

### <a name="condition"></a>조건

사용 하 여 리소스를 만들지 여부를 배포 하는 동안 결정 해야 할, 경우를 `condition` 요소입니다. 이 요소 값은 true 또는 false로 확인됩니다. 값이 true이면 리소스가 만들어집니다. 값이 false이면 리소스가 만들어지지 않습니다. 값은 전체 리소스에만 적용할 수 있습니다.

일반적으로 새 리소스를 만들거나 기존 리소스를 사용하려는 경우 이 값을 사용합니다. 예를 들어 새 저장소 계정 배포 여부 또는 기존 저장소 계정 사용 여부를 지정하려면 다음을 사용합니다.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

`condition` 요소를 사용하는 전체 예제 템플릿은 [신규 또는 기존 가상 네트워크, 저장소 및 공용 IP를 사용하는 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)을 참조하세요.

사용 하는 경우는 [참조](resource-group-template-functions-resource.md#reference) 하거나 [목록](resource-group-template-functions-resource.md#list) 리소스 배포 되지 않는 경우에 리소스를 조건부로 배포 되는 함수를 사용 하 여 함수 평가 됩니다. 함수가 존재 하지 않는 리소스를 참조 하면 오류가 발생 합니다. 사용 된 [경우](resource-group-template-functions-logical.md#if) 함수가만 조건에 대 한 계산 리소스를 배포할 때 되도록 함수입니다. 참조를 [하는 경우 함수](resource-group-template-functions-logical.md#if) 경우 사용 하는 샘플 템플릿 및 조건에 따라 배포 된 리소스를 사용 하 여 참조 합니다.

### <a name="resource-names"></a>리소스 이름

일반적으로 Resource Manager에서는 세 가지 유형의 리소스 이름으로 작업합니다.

* 고유해야 하는 리소스 이름
* 고유해야 할 필요는 없지만, 리소스를 식별하는 데 도움이 될 수 있는 이름을 제공하기 위해 선택하는 리소스 이름입니다.
* 일반적일 수 있는 리소스 이름

제공 된 **고유한 리소스 이름을** 데이터 액세스 끝점이 있는 모든 리소스 유형에 대 한 합니다. 고유 이름이 필요한 일반적인 리소스 유형은 다음과 같습니다.

* Azure Storage<sup>1</sup> 
* Azure App Service의 Web Apps 기능
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> Storage 계정 이름은 24자 이내의 소문자여야 하며 하이픈은 포함할 수 없습니다.

이름을 설정할 때 고유한 이름을 수동으로 만들거나 [uniqueString()](resource-group-template-functions-string.md#uniquestring) 함수를 사용하여 이름을 생성할 수 있습니다. 또한 **uniqueString** 결과에 접두사 또는 접미사를 추가할 수도 있습니다. 고유한 이름을 수정하면 이름으로 리소스 유형을 보다 쉽게 식별할 수 있습니다. 예를 들어 다음 변수를 사용하여 저장소 계정에 대한 고유 이름을 생성할 수 있습니다.

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

일부 리소스 유형의 경우 제공 하려는 **식별에 대 한 이름**, 하지만 이름을 고유할 필요는 없습니다. 이러한 리소스 유형의 경우 사용 하 여 또는 특징에 설명 하는 이름을 제공 합니다.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

리소스 형식의 다른 리소스를 통해 대부분 액세스 했는지를 사용할 수는 **일반 이름을** 템플릿에 하드 코드 된입니다. 예를 들어 SQL Server에서 방화벽 규칙에 대해 표준 일반 이름을 설정할 수 있습니다.

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>리소스 위치

템플릿을 배포할 때는 각 리소스의 위치를 지정해야 합니다. 다양한 리소스 형식이 다양한 위치에서 지원됩니다. 리소스 형식에 대해 지원되는 위치를 가져오려면 [Azure 리소스 공급 기업 및 형식](resource-manager-supported-services.md)을 참조하세요.

매개 변수를 사용하여 리소스에 대한 위치를 지정하고, 기본값을 `resourceGroup().location`으로 설정합니다.

다음 예제에서는 매개 변수로 지정된 위치에 배포되는 스토리지 계정을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>자식 리소스

일부 리소스 형식 내에서 자식 리소스의 배열도 정의할 수 있습니다. 자식 리소스는 다른 리소스의 컨텍스트 내에만 존재하는 리소스입니다. 예를 들어, SQL 데이터베이스는 SQL Server 없이 존재할 수 없으므로 데이터베이스가 서버의 자식입니다. 서버에 대한 정의 내에서 데이터베이스를 정의할 수 있습니다.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  ...
  "resources": [
    {
      "apiVersion": "2017-10-01-preview",
      "type": "databases",
      "name": "exampledatabase",
      ...
    }
  ]
}
```

그러나 서버 내에서 데이터베이스를 정의할 필요는 없습니다. 최상위 수준에 자식 리소스를 정의할 수 있습니다. 부모 리소스가 동일한 템플릿에서 배포되지 않는 경우 또는 `copy`를 사용하여 둘 이상의 자식 리소스를 만들려는 경우, 이 방법을 사용할 수 있습니다. 이 방법을 사용하는 경우 전체 리소스 유형을 입력하고 자식 리소스 이름에 부모 리소스 이름을 포함해야 합니다.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  "resources": [ 
  ],
  ...
},
{
  "apiVersion": "2017-10-01-preview",
  "type": "Microsoft.Sql/servers/databases",
  "name": "exampleserver/exampledatabase",
  ...
}
```

형식 및 이름에 대해 제공한 값을 자식 리소스가 부모 리소스 내에서 또는 부모 리소스 외부에서 정의 되었는지 여부에 따라 다릅니다.

부모 리소스에서 중첩 된 경우 사용 합니다.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

부모 리소스 외부에서 정의 하는 경우 사용 합니다.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

중첩 형식으로 설정 됩니다 `databases` 전체 리소스 형식은 여전히 이지만 `Microsoft.Sql/servers/databases`합니다. `Microsoft.Sql/servers/`는 부모 리소스 종류에서 유추되므로 입력하지 않습니다. 자식 리소스 이름은 `exampledatabase`로 설정되지만 전체 이름에는 부모 이름이 포함됩니다. `exampleserver`는 부모 리소스에서 유추되므로 입력하지 않습니다.

리소스에 대한 정규화된 참조를 생성할 때 형식과 이름의 세그먼트를 결합하는 순서는 단순히 두 세그먼트의 연결이 아닙니다. 대신, 네임스페이스 뒤에 구체성이 낮은 순으로 *형식/이름* 쌍의 시퀀스를 사용합니다.

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

예를 들면 다음과 같습니다.

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`는 올바릅니다. `Microsoft.Compute/virtualMachines/extensions/myVM/myExt`는 올바르지 않습니다.

## <a name="outputs"></a>outputs

Outputs 섹션에서, 배포에서 반환되는 값을 지정합니다. 일반적으로 배포 된 리소스에서 값을 반환 합니다.

### <a name="available-properties"></a>사용 가능한 속성

다음 예제에서는 출력 정의의 구조를 보여줍니다.

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| 요소 이름 | 필수 | 설명 |
|:--- |:--- |:--- |
| outputName |예 |출력 값의 이름입니다. 유효한 JavaScript 식별자여야 합니다. |
| condition |아닙니다. | 이 출력 값의 반환 여부를 나타내는 부울 값입니다. `true`이면 해당 값이 배포의 출력에 포함됩니다. `false`이면 이 배포에 대한 출력 값을 건너뜁니다. 지정하지 않으면 기본값은 `true`입니다. |
| 형식 |예 |출력 값의 유형입니다. 출력 값은 템플릿 입력 매개 변수와 동일한 유형을 지원합니다. 지정 하는 경우 **securestring** 출력 형식에 대 한 값 배포 기록에서 표시 되지 않으면 및 다른 서식 파일에서 검색할 수 없습니다. 둘 이상의 템플릿에서 비밀 값을 사용 하려면 Key Vault에 비밀을 저장 하 고 매개 변수 파일에서 비밀을 참조할 합니다. 자세한 내용은 [배포 동안 보안 매개 변수 값을 전달 하기 위해 사용 하 여 Azure Key Vault](resource-manager-keyvault-parameter.md)합니다. |
| 값 |예 |출력 값으로 계산되어 반환되는 템플릿 언어 식입니다. |

### <a name="define-and-use-output-values"></a>출력 값 정의 및 사용

다음 예제에서는 공용 IP 주소의 리소스 ID를 반환하는 방법을 보여줍니다.

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

다음 예제에서는 새 항목이 배포되었는지 여부에 따라 공용 IP 주소의 리소스 ID를 조건부로 반환하는 방법을 보여 줍니다.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

조건부 출력의 간단한 예제를 보려면 [조건부 출력 템플릿](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)합니다.

배포 후에 스크립트를 사용하여 값을 검색할 수 있습니다. PowerShell의 경우 다음을 사용합니다.

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Azure CLI의 경우 

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

[참조](resource-group-template-functions-resource.md#reference) 함수를 사용하여 연결된 템플릿에서 출력 값을 검색할 수 있습니다. 연결된 템플릿에서 출력 값을 가져오려면 `"[reference('deploymentName').outputs.propertyName.value]"` 같은 구문으로 속성 값을 검색합니다.

연결된 템플릿에서 출력 속성을 가져올 때 속성 이름에 대시를 포함할 수 없습니다.

다음 예제에서는 연결 된 템플릿에서 값을 검색 하 여 부하 분산 장치의 IP 주소를 설정 하는 방법을 보여 줍니다.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

`reference` 함수는 [중첩된 템플릿](resource-group-linked-templates.md#link-or-nest-a-template)의 출력 섹션에 사용할 수 없습니다. 중첩된 템플릿에서 배포된 리소스의 값을 반환하려면 중첩된 템플릿을 연결된 템플릿으로 변환합니다.

### <a name="output-example-templates"></a>출력 예제 템플릿

|Template  |설명  |
|---------|---------|
|[변수 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 복잡한 변수를 만들고 해당 값을 출력합니다. 여기서는 리소스를 배포하지 않습니다. |
|[공용 IP 주소](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 공용 IP 주소를 만들고 리소스 ID를 출력합니다. |
|[부하 분산 장치](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 이전 템플릿에 연결됩니다. 부하 분산 장치를 만들 때 출력에 리소스 ID를 사용합니다. |


<a id="comments" />

## <a name="comments-and-metadata"></a>주석 및 메타데이터

템플릿에 주석 및 메타데이터를 추가하는 몇 가지 옵션이 있습니다.

`metadata` 개체는 템플릿의 거의 모든 위치에 추가할 수 있습니다. Azure Resource Manager는 해당 개체를 무시하지만 JSON 편집기가 해당 속성이 유효하지 않음을 경고할 수 있습니다. 개체 내에 필요한 속성을 정의합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

**매개 변수**에 대해 `description` 속성과 함께 `metadata` 개체를 추가합니다.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

포털을 통해 템플릿을 배포하는 경우 설명에 제공하는 텍스트가 자동으로 해당 매개 변수에 대한 팁으로 사용됩니다.

![매개 변수 팁 표시](./media/resource-group-authoring-templates/show-parameter-tip.png)

**리소스**에 대해 `comments` 요소 또는 메타데이터 개체를 추가합니다. 다음 예제에서는 주석 요소와 메타데이터 개체를 모두 보여 줍니다.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

**outputs**의 경우 출력 값에 메타데이터 개체를 추가합니다.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

사용자 정의 함수에 메타데이터 개체를 추가할 수 없습니다.

인라인 주석의 경우 `//`를 사용할 수 있지만 이 구문은 일부 도구에서 작동하지 않습니다. Azure CLI를 사용하여 인라인 주석 포함 템플릿을 배포할 수 없습니다. 또한 포털 템플릿 편집기를 사용하여 인라인 주석 포함 템플릿에 대해 작업할 수 없습니다. 이 주석 스타일을 추가하는 경우 사용하는 도구가 반드시 인라인 JSON 주석을 지원해야 합니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

VS Code에서 언어 모드를 주석 포함 JSON으로 설정할 수 있습니다. 그러면 인라인 주석이 더 이상 유효하지 않음으로 표시되지 않습니다. 모드를 변경하려면:

1. 언어 모드 선택(Ctrl+K M)을 엽니다.

1. **주석 포함 JSON**을 선택합니다.

   ![언어 모드 선택](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>다음 단계
* 다양한 유형의 솔루션에 대한 전체 템플릿을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.
* 템플릿 내에서 사용할 수 있는 함수에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
* 배포 중 여러 템플릿을 결합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 템플릿을 만드는 방법에 대한 권장 사항은 [Azure Resource Manager 템플릿 모범 사례](template-best-practices.md)를 참조하세요.
* 모든 Azure 환경 및 Azure Stack에서 사용할 수 있는 Resource Manager 템플릿을 만드는 방법에 대한 권장 사항은 [클라우드 일관성을 위한 Azure Resource Manager 템플릿 개발](templates-cloud-consistency.md)을 참조하세요.
