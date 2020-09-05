---
title: Azure에 함수 앱 리소스 배포 자동화
description: 함수 앱을 배포하는 Azure Resource Manager 템플릿을 빌드하는 방법을 알아봅니다.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 56a68fca42bcab7642a5ebad953b59269a4d88a1
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180646"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions의 함수 앱에 대한 리소스 배포 자동화

Azure Resource Manager 템플릿을 사용하여 함수 앱을 배포할 수 있습니다. 이 문서에서는 이 작업을 수행하는 데 필요한 리소스와 매개 변수를 간략히 설명합니다. 함수 앱의 [트리거 및 바인딩](functions-triggers-bindings.md)에 따라 추가 리소스를 배포해야 할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성하기](../azure-resource-manager/templates/template-syntax.md)를 참조하십시오.

샘플 템플릿은 다음을 참조하세요.
- [소비 계획의 함수 앱]
- [Azure App Service 계획의 함수 앱]

## <a name="required-resources"></a>필요한 리소스

Azure Functions 배포는 일반적으로 다음 리소스로 구성 됩니다.

| 리소스                                                                           | 요구 사항 | 구문 및 속성 참조                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| 함수 앱                                                                     | 필수    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| [Azure Storage](../storage/index.yml) 계정                                   | 필수    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) 구성 요소 | 선택 사항    | [Microsoft 인 사이트/구성 요소](/azure/templates/microsoft.insights/components)         |
| [호스팅 계획](./functions-scale.md)                                             | 선택 사항<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> 호스팅 계획은 [프리미엄 계획이](./functions-premium-plan.md) 나 [App Service 계획](../app-service/overview-hosting-plans.md)에서 함수 앱을 실행 하도록 선택 하는 경우에만 필요 합니다.

> [!TIP]
> 필수는 아니지만 앱에 대 한 Application Insights를 구성 하는 것이 좋습니다.

<a name="storage"></a>
### <a name="storage-account"></a>스토리지 계정

함수 앱에는 Azure Storage 계정이 필요합니다. Blob, 테이블, 큐 및 파일을 지원하는 일반 용도의 계정이 있어야 합니다. 자세한 내용은 [Azure Functions 스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

또한 속성 `AzureWebJobsStorage`가 사이트 구성에서 앱 설정으로 지정되어야 합니다. 함수 앱이 Application Insights를 모니터링에 사용하지 않으면 `AzureWebJobsDashboard`도 앱 설정으로 지정되어야 합니다.

Azure Functions 런타임에서는 `AzureWebJobsStorage` 연결 문자열을 사용하여 내부 큐를 만듭니다.  Application Insights가 활성화되지 않은 경우 런타임은 `AzureWebJobsDashboard` 연결 문자열을 사용하여 Azure Table Storage에 로그온하고 포털의 **모니터** 탭에 전원을 공급합니다.

이러한 속성은 `siteConfig` 개체의 `appSettings` 컬렉션에서 지정됩니다.

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights 함수 앱 모니터링에 권장 됩니다. Application Insights 리소스 **는 다음과 같은**형식으로 지정 됩니다 **.**

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

또한 응용 프로그램 설정을 사용 하 여 함수 앱에 계측 키를 제공 해야 합니다 `APPINSIGHTS_INSTRUMENTATIONKEY` . 이 속성은 `appSettings` 개체의 컬렉션에서 지정 됩니다 `siteConfig` .

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>호스팅 계획

호스팅 계획의 정의는 다양 하며 다음 중 하나일 수 있습니다.
* [소비 계획](#consumption) (기본값)
* [프리미엄 계획](#premium)
* [App Service 계획](#app-service-plan)

### <a name="function-app"></a>함수 앱

함수 앱 리소스 **는 다음과 같은**형식의 리소스를 사용 하 여 정의 됩니다 **.**

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> 호스팅 계획을 명시적으로 정의 하는 경우 dependsOn 배열에 추가 항목이 필요 합니다. `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

함수 앱에는 다음과 같은 응용 프로그램 설정이 포함 되어야 합니다.

| 설정 이름                 | 설명                                                                               | 예제 값                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | 함수 런타임에서 내부 큐에 사용 하는 저장소 계정에 대 한 연결 문자열입니다. | [저장소 계정](#storage) 을 참조 하세요.       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions 런타임의 버전입니다.                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 이 앱의 함수에 사용할 언어 스택입니다.                                   | `dotnet`, `node`, `java`, `python` 또는 `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | 언어 스택을 사용 하는 경우에만 `node` 사용할 버전을 지정 합니다.              | `10.14.1`                             |

이러한 속성은 `appSettings` 속성의 컬렉션에서 지정 됩니다 `siteConfig` .

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>소비 계획에 배포

소비 계획은 코드가 실행 중일 때 계산 능력을 자동으로 할당 하 고, 로드를 처리 하는 데 필요한 만큼 확장 한 다음 코드가 실행 되지 않을 때 확장 됩니다. 유휴 Vm에 대 한 비용을 지불할 필요가 없으며, 용량을 미리 예약할 필요가 없습니다. 자세한 내용은 [Azure Functions 크기 조정 및 호스팅](functions-scale.md#consumption-plan)을 참조하세요.

샘플 Azure Resource Manager 템플릿은 [소비 계획의 함수 앱]을 참조하세요.

### <a name="create-a-consumption-plan"></a>소비 계획 만들기

소비 계획을 정의할 필요가 없습니다. 함수 앱 리소스 자체를 만들 때 지역 별로 자동으로 만들어지거나 선택 됩니다.

소비 계획은 특수 한 유형의 "서버 팜" 리소스입니다. Windows의 경우 `Dynamic` 및 속성에 대 한 값을 사용 하 여 지정할 수 있습니다 `computeMode` `sku` .

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Linux에 대해 소비 계획을 명시적으로 정의할 수 없습니다. 자동으로 생성 됩니다.

소비 계획을 명시적으로 정의 하는 경우 `serverFarmId` 계획의 리소스 ID를 가리키도록 앱에 대 한 속성을 설정 해야 합니다. 함수 앱에 `dependsOn` 계획에 대 한 설정도 있는지 확인 해야 합니다.

### <a name="create-a-function-app"></a>함수 앱 만들기

#### <a name="windows"></a>Windows

Windows에서 소비 계획에는 사이트 구성에 및의 두 가지 추가 설정이 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 필요 `WEBSITE_CONTENTSHARE` 합니다. 이러한 속성은 함수 앱 코드와 구성이 저장되는 스토리지 계정 및 파일 경로를 구성합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

Linux에서 함수 앱은를 `kind` 로 설정 하 고 속성을로 설정 해야 합니다 `functionapp,linux` `reserved` `true` .

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>프리미엄 계획에 배포

프리미엄 요금제는 소비 계획과 동일한 크기 조정을 제공 하지만 전용 리소스 및 추가 기능을 포함 합니다. 자세한 내용은 [Azure Functions Premium 요금제](./functions-premium-plan.md)를 참조 하세요.

### <a name="create-a-premium-plan"></a>프리미엄 플랜 만들기

프리미엄 요금제는 특수 한 유형의 "서버 팜" 리소스입니다. `EP1` `EP2` `EP3` `Name` `sku` [설명 개체](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)의 속성 값에, 또는 중 하나를 사용 하 여 지정할 수 있습니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>함수 앱 만들기

프리미엄 계획의 함수 앱은 속성을 앞에서 `serverFarmId` 만든 계획의 리소스 ID로 설정 해야 합니다. 또한 프리미엄 계획에는 사이트 구성에 및의 두 가지 추가 설정이 필요 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 합니다 `WEBSITE_CONTENTSHARE` . 이러한 속성은 함수 앱 코드와 구성이 저장되는 스토리지 계정 및 파일 경로를 구성합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>App Service 계획에 배포

App Service 계획에서 함수 앱은 웹앱과 유사하게 기본, 표준, 프리미엄 SKU의 전용 VM에서 실행됩니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요.

샘플 Azure Resource Manager 템플릿은 [Azure App Service 계획의 함수 앱]을 참조하세요.

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

App Service 계획은 "서버 팜" 리소스에 의해 정의 됩니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Linux에서 앱을 실행 하려면도로 설정 해야 합니다 `kind` `Linux` .

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>함수 앱 만들기

App Service 계획의 함수 앱에는 `serverFarmId` 앞에서 만든 계획의 리소스 ID로 설정 된 속성이 있어야 합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

또한 Linux 앱에는에 속성이 포함 되어야 합니다 `linuxFxVersion` `siteConfig` . 코드를 배포 하는 경우이 값은 원하는 런타임 스택에 의해 결정 됩니다.

| 스택            | 예제 값                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

[사용자 지정 컨테이너 이미지를 배포](./functions-create-function-linux-custom-image.md)하는 경우에는를 사용 하 여 지정 해야 `linuxFxVersion` 하며, [Web App for Containers](../app-service/containers/index.yml)처럼 이미지를 끌어올 수 있도록 하는 구성을 포함 해야 합니다. 또한 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false` 앱 콘텐츠는 컨테이너 자체에 제공 되므로를로 설정 합니다.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>배포 사용자 지정

함수 앱에는 앱 설정 및 소스 제어 옵션을 포함하여 배포에 사용할 수 있는 자식 리소스가 많이 있습니다. **Sourcecontrols** 자식 리소스를 제거 하 고 대신 다른 [배포 옵션](functions-continuous-deployment.md) 을 사용 하도록 선택할 수도 있습니다.

> [!IMPORTANT]
> Azure Resource Manager를 사용하여 애플리케이션을 성공적으로 배포하려면 Azure에서 리소스가 배포되는 방식을 이해하는 것이 중요합니다. 다음 예제에서는 **siteConfig**를 사용하여 최상위 수준 구성을 적용합니다. Functions 런타임 및 배포 엔진에 정보를 전달하기 때문에 최상위 수준에서 이러한 구성을 설정하는 것이 중요합니다. **sourcecontrols/web** 자식 리소스를 적용하기 전에 최상위 수준 정보가 필요합니다. 자식 수준 **구성/appsettings** 리소스에서 이러한 설정을 구성할 수 있지만, 경우에 따라 **config/appsettings** 를 적용 *하기 전에* 함수 앱을 배포 해야 합니다. 예를 들어 [Logic Apps](../logic-apps/index.yml)에서 함수를 사용하는 경우 함수는 다른 리소스의 종속성입니다.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 이 템플릿에서는 [프로젝트](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 앱 설정 값을 사용 하며,이 값은 Kudu (함수 배포 엔진)에서 배포 가능한 코드를 검색 하는 기본 디렉터리를 설정 합니다. 리포지토리에서 함수는 **src** 폴더의 하위 폴더에 있습니다. 따라서 앞의 예제에서 앱 설정 값은 `src`로 설정합니다. 함수가 리포지토리의 루트에 있거나 소스 제어에서 배포하지 않는 경우 이 앱 설정 값을 제거할 수 있습니다.

## <a name="deploy-your-template"></a>템플릿 배포

다음 방법 중 하나를 사용하여 템플릿을 배포할 수 있습니다.

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Azure 단추에 배포

```<url-encoded-path-to-azuredeploy-json>```을 GitHub에 있는 `azuredeploy.json` 파일의 원시 경로에 대한 [URL 인코딩](https://www.bing.com/search?q=url+encode) 버전으로 바꿉니다.

markdown을 사용하는 예는 다음과 같습니다.

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

HTML을 사용하는 예는 다음과 같습니다.

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>PowerShell을 사용하여 배포

다음 PowerShell 명령은 리소스 그룹을 만들고 필요한 리소스를 사용 하 여 함수 앱을 만드는 템플릿을 배포 합니다. 로컬로 실행 하려면 [Azure PowerShell](/powershell/azure/install-az-ps) 설치 되어 있어야 합니다. [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount)을 실행 하 여 로그인 합니다.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

이 배포를 테스트 하려면 소비 계획의 Windows에서 함수 앱을 만드는 것 [과 같은 템플릿을](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) 사용할 수 있습니다. `<function-app-name>`함수 앱에 대 한 고유한 이름으로 대체 합니다.

## <a name="next-steps"></a>다음 단계

Azure Functions를 개발하고 구성하는 방법에 대해 자세히 알아봅니다.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure 함수 앱 설정을 구성 하는 방법](functions-how-to-use-azure-function-app-settings.md)
* [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)

<!-- LINKS -->

[소비 계획의 함수 앱]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Azure App Service 계획의 함수 앱]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
