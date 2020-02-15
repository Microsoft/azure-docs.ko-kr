---
title: 템플릿 함수-배포
description: Azure Resource Manager 템플릿에서 배포 정보를 검색하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: b241aaf43ee3204c9960d0099ce3c61d4c1a80ee
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207284"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿용 배포 함수 

리소스 관리자는 현재 배포와 관련 된 값을 가져오기 위한 다음 함수를 제공 합니다.

* [deployment](#deployment)
* [개발](#environment)
* [parameters](#parameters)
* [variables](#variables)

리소스, 리소스 그룹 또는 구독에서 값을 가져오려면 [리소스 함수](template-functions-resource.md)를 참조하세요.

## <a name="deployment"></a>배포

`deployment()`

현재 배포 작업에 대한 정보를 반환합니다.

### <a name="return-value"></a>반환 값

이 함수는 배포하는 동안 전달되는 개체를 반환합니다. 반환된 개체의 속성은 배포 개체가 링크로 전달되는지 아니면 인라인 개체로 전달되는지에 따라 다릅니다. 배포 개체가 로컬 파일을 가리키기 위해 Azure PowerShell의 **-TemplateFile** 매개 변수를 사용할 때와 같이 인라인으로 전달되는 경우 개체는 다음 형식으로 반환됩니다.

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

개체가 원격 개체를 가리키기 위해 **-TemplateUri** 매개 변수를 사용할 때와 같이 링크로 전달되는 경우 개체는 다음 형식으로 반환됩니다. 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

리소스 그룹 대신 [Azure 구독에 배포](deploy-to-subscription.md)할 때 반환 개체에는 `location` 속성이 포함됩니다. 로컬 템플릿 또는 외부 템플릿을 배포할 때 위치 속성이 포함됩니다.

### <a name="remarks"></a>설명

deployment()를 사용하여 부모 템플릿의 URI를 기반으로 하는 다른 템플릿에 연결할 수 있습니다.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

포털의 배포 기록에서 템플릿을 다시 배포하는 경우 템플릿은 로컬 파일로 배포됩니다. `templateLink` 속성은 배포 함수에 반환되지 않습니다. 템플릿이 `templateLink`를 사용하여 다른 템플릿과의 링크를 설정하는 경우 포털을 사용하여 다시 배포하지 마세요. 대신 처음에 템플릿을 배포하는 데 사용한 명령을 사용하세요.

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json)에서는 배포 개체를 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

앞의 예제에서는 다음 개체를 반환합니다.

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

배포 기능을 사용하는 구독 수준 템플릿의 경우 [구독 배포 기능](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json)을 참조하세요. `az deployment create` 또는 `New-AzDeployment` 명령을 사용하여 배포됩니다.

## <a name="environment"></a>환경

`environment()`

배포에 사용 되는 Azure 환경에 대 한 정보를 반환 합니다.

### <a name="return-value"></a>반환 값

이 함수는 현재 Azure 환경에 대 한 속성을 반환 합니다. 다음 예제에서는 글로벌 Azure에 대 한 속성을 보여 줍니다. 소 버린 클라우드는 약간 다른 속성을 반환할 수 있습니다.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>예제

다음 예제 템플릿에서는 환경 개체를 반환 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

위의 예제는 글로벌 Azure에 배포 된 경우 다음 개체를 반환 합니다.

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>매개 변수

`parameters(parameterName)`

매개 변수 값을 반환합니다. 템플릿의 매개 변수 섹션에서 지정된 매개 변수 이름을 정의해야 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |yes |문자열 |반환할 매개 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정한 매개 변수의 값입니다.

### <a name="remarks"></a>설명

일반적으로 매개 변수를 사용하여 리소스 값을 설정합니다. 다음 예제에서는 웹 사이트의 이름을 배포 중에 전달된 매개 변수 값으로 설정합니다.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json)에서는 매개 변수 함수의 간소화된 사용을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| stringOutput | String | 옵션 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {“one”: “a”, “two”: “b”} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | 옵션 1 |

매개 변수 사용에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 매개 변수](template-parameters.md)를 참조 하세요.

## <a name="variables"></a>variables

`variables(variableName)`

변수의 값을 반환합니다. 템플릿의 변수 섹션에서 지정된 변수 이름을 정의해야 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| variableName |yes |String |반환할 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정한 변수의 값입니다.

### <a name="remarks"></a>설명

일반적으로 복잡한 값을 한 번만 구성하여 템플릿을 간소화하기 위해 변수를 사용합니다. 다음 예제에서는 스토리지 계정에 대한 고유한 이름을 생성합니다.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json)은 각기 다른 변수 값을 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | Type | 값 |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {“property1”: “value1”, “property2”: “value2”} |

변수를 사용 하는 방법에 대 한 자세한 내용은 [Azure Resource Manager 템플릿에서 변수](template-variables.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
* Azure Resource Manager 템플릿의 섹션에 대한 설명은 [Azure Resource Manager 템플릿 작성](template-syntax.md)을 참조하세요.
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](linked-templates.md)을 참조하세요.
* 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](copy-resources.md)를 참조하세요.
* 만든 템플릿을 배포하는 방법을 보려면 [Azure Resource Manager 템플릿을 사용하여 애플리케이션 배포](deploy-powershell.md)를 참조하세요.

