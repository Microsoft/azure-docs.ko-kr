---
title: Azure Resource Manager를 사용 하 여 Redis 용 Azure Cache 배포
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Cache for Redis 리소스를 배포 하는 방법을 알아봅니다. 일반적인 시나리오에 대한 템플릿이 제공 됩니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412399"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>템플릿을 사용하여 Azure Cache for Redis 만들기

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 항목에서는 Azure Cache for Redis를 배포하는 Azure Resource Manager 템플릿을 만드는 방법에 대해 알아봅니다. 기존 스토리지 계정과 함께 캐시를 사용하여 진단 데이터를 유지할 수 있습니다. 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법도 알아봅니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

현재 진단 설정은 구독에 대한 동일한 지역의 모든 캐시에서 공유됩니다. 지역의 캐시 하나를 업데이트하면 해당 지역의 다른 모든 캐시에 영향을 줍니다.

템플릿을 만드는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../azure-resource-manager/templates/template-syntax.md)를 참조하십시오. 캐시 리소스 종류의 JSON 구문 및 속성에 대해 알아보려면 [Microsoft.Cache 리소스 종류](/azure/templates/microsoft.cache/allversions)를 참조하세요.

전체 템플릿은 [Azure Cache for Redis 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)을 참조하세요.

> [!NOTE]
> 새 [프리미엄 계층](cache-premium-tier-intro.md) 에 대한 Resource Manager 템플릿을 사용할 수 있습니다. 
> 
> * [클러스터링이 있는 프리미엄 Azure Cache for Redis 만들기](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [데이터 지속성이 있는 프리미엄 Azure Cache for Redis 만들기](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Virtual Network에 배포 된 프리미엄 Redis Cache 만들기](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> 최신 템플릿을 확인하려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/) 및 `Azure Cache for Redis`에 대한 검색을 참조하세요.
> 
> 

## <a name="what-you-will-deploy"></a>배포할 내용
이 템플릿에서는 진단 데이터에 기존 스토리지 계정을 사용하는 Azure Cache for Redis를 배포합니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>매개 변수
Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함 된 매개 변수라는 섹션을 포함합니다.
배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Azure Cache for Redis의 위치입니다. 최상의 성능을 위해 캐시와 함께 사용될 앱과 동일한 위치를 사용합니다.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
진단에 사용할 기존 스토리지 계정의 이름입니다. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
비 SSL 포트를 통한 액세스를 허용할지 여부를 나타내는 부울 값입니다.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
진단을 사용하도록 설정할지 여부를 나타내는 값입니다. ON 또는 OFF를 사용합니다.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>배포할 리소스
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis를 만듭니다.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2017-05-01-preview",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>배포 실행 명령
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
