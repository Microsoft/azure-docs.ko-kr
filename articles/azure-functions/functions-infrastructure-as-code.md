---
title: Azure Functions의 함수 앱에 대한 리소스 배포 자동화 | Microsoft Docs
description: 함수 앱을 배포하는 Azure Resource Manager 템플릿을 빌드하는 방법을 알아봅니다.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, 함수, 서버 없는 아키텍처, 코드로서의 인프라, Azure Resource Manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 5d028768c062ef7df74d48f83ccc4e27a506f1ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737060"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions의 함수 앱에 대한 리소스 배포 자동화

Azure Resource Manager 템플릿을 사용하여 함수 앱을 배포할 수 있습니다. 이 문서에서는 이 작업을 수행하는 데 필요한 리소스와 매개 변수를 간략히 설명합니다. 함수 앱의 [트리거 및 바인딩](functions-triggers-bindings.md)에 따라 추가 리소스를 배포해야 할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성하기](../azure-resource-manager/resource-group-authoring-templates.md)를 참조하십시오.

샘플 템플릿은 다음을 참조하세요.
- [소비 계획의 함수 앱]
- [Azure App Service 계획의 함수 앱]

> [!NOTE]
> Azure Functions 호스팅에 대 한 프리미엄 요금제는 현재 미리 보기 중입니다. 자세한 내용은 [Azure Functions 프리미엄 요금제](functions-premium-plan.md)합니다.

## <a name="required-resources"></a>필요한 리소스

Azure Functions 배포는 일반적으로 이러한 리소스의 구성 됩니다.

| 리소스                                                                           | 요구 사항 | 구문 및 속성 참조                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| 함수 앱                                                                     | 필수    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Azure Storage](../storage/index.yml) 계정                                   | 필수    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) 구성 요소 | 옵션    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| [호스팅 계획](./functions-scale.md)                                             | 선택적<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>호스팅 계획은만에서 함수 앱을 실행 하도록 선택할 때 필요한를 [프리미엄 요금제](./functions-premium-plan.md) (미리 보기)에서 또는 [App Service 계획](../app-service/overview-hosting-plans.md)합니다.

> [!TIP]
> 필요 없이 앱에 대 한 Application Insights를 구성 하는 것이 좋습니다.

<a name="storage"></a>
### <a name="storage-account"></a>Storage 계정

함수 앱에는 Azure Storage 계정이 필요합니다. Blob, 테이블, 큐 및 파일을 지원하는 일반 용도의 계정이 있어야 합니다. 자세한 내용은 [Azure Functions 저장소 계정 요구 사항](functions-create-function-app-portal.md#storage-account-requirements)을 참조하세요.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
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
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

함수 앱을 모니터링 하는 것에 대 한 application Insights는 사용 하는 것이 좋습니다. Application Insights 리소스 형식을 사용 하 여 정의 됩니다 **microsoft.insights/components** 종류 **웹**:

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
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

계측 키를 사용 하 여 함수 앱에 제공 해야 하는 또한는 `APPINSIGHTS_INSTRUMENTATIONKEY` 응용 프로그램 설정 합니다. 이 속성에 지정 된 된 `appSettings` 컬렉션에는 `siteConfig` 개체:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>호스팅 계획

호스팅 계획의 정의 다르며 다음 중 하나일 수 있습니다.
* [소비 계획](#consumption) (기본값)
* [프리미엄 요금제](#premium) (미리 보기)에서
* [App Service 계획](#app-service-plan)

### <a name="function-app"></a>함수 앱

함수 앱 리소스 종류의 리소스를 사용 하 여 정의한 **microsoft.web/sites** 및 종류 **functionapp**:

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
```

> [!IMPORTANT]
> 호스팅 계획을 명시적으로 정의 하는 경우 dependsOn 배열에 추가 항목이 필요 합니다. `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

함수 앱에는 이러한 응용 프로그램 설정을 포함 해야 합니다.

| 설정 이름                 | 설명                                                                               | 예제 값                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | 저장소 연결 문자열을 Functions 런타임에 내부 큐를 제공 하는 계정 | 참조 [저장소 계정](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions 런타임 버전                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 이 앱의 함수에 사용 되는 언어 스택                                   | `dotnet`하십시오 `node`, `java`, 또는 `python` |
| WEBSITE_NODE_DEFAULT_VERSION | 사용 하는 경우에 필요 합니다 `node` 언어 스택 사용할 버전을 지정 합니다.              | `10.14.1`                             |

이러한 속성을 지정 합니다 `appSettings` 컬렉션에는 `siteConfig` 속성:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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

## <a name="deploy-on-consumption-plan"></a>소비 계획에서 배포

소비 계획은 코드가 실행 중일 때 계산 용량을 자동으로 할당하고, 로드를 처리하는 데 필요한 만큼 확장한 다음, 코드가 실행되지 않을 때 축소합니다. 유휴 Vm에 대 한 요금을 지불 하지 않아도 및 용량을 미리 예약할 필요가 없습니다. 자세한 내용은 참조 하세요 [Azure Functions 크기 조정 및 호스팅](functions-scale.md#consumption-plan)합니다.

샘플 Azure Resource Manager 템플릿은 [소비 계획의 함수 앱]을 참조하세요.

### <a name="create-a-consumption-plan"></a>소비 계획 만들기

소비 계획 정의 될 필요가 없습니다. 하나는 자동으로 생성 또는 자체 함수 앱 리소스를 만들 때-지역 단위로 선택 합니다.

소비 계획은 특수 한 유형의 "서버 팜" 리소스입니다. Windows를 지정할 수 있습니다이 사용 하 여 합니다 `Dynamic` 에 대 한 값을 `computeMode` 및 `sku` 속성:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

> [!NOTE]
> 소비 계획은 Linux에 대 한 명시적으로 정의할 수 없습니다. 자동으로 생성 됩니다.

소비 계획을 정의 명시적으로 설정 해야 합니다는 `serverFarmId` 한다는 계획의 리소스 ID를 가리키는 하므로 앱의 속성입니다. 함수 앱에 있는지 확인 해야는 `dependsOn` 도 계획에 대 한 설정입니다.

### <a name="create-a-function-app"></a>함수 앱 만들기

#### <a name="windows"></a>Windows

Windows, 소비 계획 인해 사이트 구성의 두 가지 추가 설정: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 고 `WEBSITE_CONTENTSHARE`입니다. 이러한 속성은 함수 앱 코드와 구성이 저장되는 저장소 계정 및 파일 경로를 구성합니다.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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

Linux에서 함수 앱 있어야 해당 `kind` 로 설정 `functionapp,linux`, 있어야 하 고는 `reserved` 속성이로 설정 `true`:

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
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

## <a name="deploy-on-premium-plan"></a>프리미엄 요금제에 배포

프리미엄 요금제 소비 계획으로 이와 동일한 크기 조정을 제공 하지만 전용된 리소스 및 추가 기능을 포함 합니다. 자세한 내용은 참조 하세요 [Azure Functions 프리미엄 계획 (미리 보기)](./functions-premium-plan.md)합니다.

### <a name="create-a-premium-plan"></a>프리미엄 계획 만들기

프리미엄 요금제에는 "서버 팜" 리소스의 특수 형식입니다. 사용 하 여 지정할 수 있습니다 `EP1`, `EP2`, 또는 `EP3` 에 대 한는 `sku` 속성 값입니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>함수 앱 만들기

프리미엄 계획에서 함수 앱을 있어야 합니다 `serverFarmId` 속성이 이전에 만든 계획의 리소스 ID를 설정 합니다. 또한 프리미엄 요금제 하려면 사이트 구성의 두 가지 추가 설정: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 고 `WEBSITE_CONTENTSHARE`입니다. 이러한 속성은 함수 앱 코드와 구성이 저장되는 저장소 계정 및 파일 경로를 구성합니다.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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

App Service 계획을 "서버 팜" 리소스에 의해 정의 됩니다.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

앱에서 Linux를 실행 하려면 설정 해야 합니다 `kind` 에 `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>함수 앱 만들기 

App Service 계획에서 함수 앱을 있어야 합니다 `serverFarmId` 속성이 이전에 만든 계획의 리소스 ID를 설정 합니다.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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

Linux 앱도 포함 됩니다는 `linuxFxVersion` 아래의 속성 `siteConfig`합니다. 코드에 바로 배포 하는 경우이 값은 원하는 런타임 스택을 따라 결정 됩니다.

| 스택            | 예제 값                                         |
|------------------|-------------------------------------------------------|
| Python(미리 보기) | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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

있다면 [사용자 지정 컨테이너 이미지를 배포](./functions-create-function-linux-custom-image.md)를 사용 하 여 지정 해야 `linuxFxVersion` 이미지를 풀링할 수에서 같이를 허용 하는 구성을 포함 하 고 [Web App for Containers](/azure/app-service/containers)합니다. 또한 설정 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 에 `false`때문에 앱 콘텐츠 자체 컨테이너에서 제공 됩니다.

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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

함수 앱에는 앱 설정 및 소스 제어 옵션을 포함하여 배포에 사용할 수 있는 자식 리소스가 많이 있습니다. 또한 **sourcecontrols** 자식 리소스를 제거하고 대신에 다른 [배포 옵션](functions-continuous-deployment.md)을 사용하도록 선택할 수 있습니다.

> [!IMPORTANT]
> Azure Resource Manager를 사용하여 애플리케이션을 성공적으로 배포하려면 Azure에서 리소스가 배포되는 방식을 이해하는 것이 중요합니다. 다음 예제에서는 **siteConfig**를 사용하여 최상위 수준 구성을 적용합니다. Functions 런타임 및 배포 엔진에 정보를 전달하기 때문에 최상위 수준에서 이러한 구성을 설정하는 것이 중요합니다. **sourcecontrols/web** 자식 리소스를 적용하기 전에 최상위 수준 정보가 필요합니다. 자식 수준 **config/appSettings** 리소스에 이러한 설정을 구성할 수 있지만 **config/appSettings**가 적용되기 *전에* 함수 앱이 배포되어야 하는 경우도 있습니다. 예를 들어 [Logic Apps](../logic-apps/index.yml)에서 함수를 사용하는 경우 함수는 다른 리소스의 종속성입니다.

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
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
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
> 이 템플릿은 Functions 배포 엔진(Kudu)에서 배치할 수 있는 코드를 검색할 기본 디렉터리를 설정하는 [프로젝트](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 앱 설정 값을 사용합니다. 리포지토리에서 함수는 **src** 폴더의 하위 폴더에 있습니다. 따라서 앞의 예제에서 앱 설정 값은 `src`로 설정합니다. 함수가 리포지토리의 루트에 있거나 소스 제어에서 배포하지 않는 경우 이 앱 설정 값을 제거할 수 있습니다.

## <a name="deploy-your-template"></a>템플릿 배포

다음 방법 중 하나를 사용하여 템플릿을 배포할 수 있습니다.

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

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

## <a name="next-steps"></a>다음 단계

Azure Functions를 개발하고 구성하는 방법에 대해 자세히 알아봅니다.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure 함수 앱 설정을 구성하는 방법](functions-how-to-use-azure-function-app-settings.md)
* [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)

<!-- LINKS -->

[소비 계획의 함수 앱]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Azure App Service 계획의 함수 앱]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
