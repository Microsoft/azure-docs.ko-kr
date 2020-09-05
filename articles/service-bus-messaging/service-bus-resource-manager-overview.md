---
title: 템플릿을 사용 하 여 Azure Service Bus 리소스 만들기
description: Azure Resource Manager 템플릿을 사용하여 자동으로 Service Bus 리소스 만들기
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df8a7fde9114f03521f0e57e072f81a867efcf39
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89075259"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 Service Bus 리소스 만들기

이 문서에서는 Azure Resource Manager 템플릿, PowerShell 및 Service Bus 리소스 공급자를 사용하여 Service Bus 리소스를 만들어 배포하는 방법을 설명합니다.

Azure Resource Manager 템플릿을 통해 솔루션에 사용할 리소스를 정의하고, 여러 환경의 값을 입력하는 데 사용할 수 있는 변수 및 매개 변수를 지정합니다. 템플릿은 JSON에 작성되고 배포에 대한 값을 생성하는 데 사용할 수 있는 식으로 구성됩니다. Azure Resource Manager 템플릿 작성에 대한 자세한 내용과 템플릿 형식에 대한 논의는 [Azure Resource Manager 템플릿의 구조 및 구문](../azure-resource-manager/templates/template-syntax.md)을 참조하세요.

> [!NOTE]
> 이 문서의 예제에서는 Azure Resource Manager를 사용하여 Service Bus 네임스페이스와 메시징 엔터티(큐)를 만드는 방법을 보여 줍니다. 다른 템플릿 예제는 [Azure 퀵 스타트 템플릿 갤러리][Azure Quickstart Templates gallery]를 방문하여 **Service Bus**를 검색하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Service Bus Resource Manager 템플릿

이러한 Service Bus Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다. 각각에 대한 자세한 내용은 GitHub의 템플릿에 대한 링크가 포함된 다음 링크를 클릭하세요.

* [Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace.md)
* [큐가 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-queue.md)
* [토픽 및 구독이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
* [큐 및 권한 부여 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-auth-rule.md)
* [토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>PowerShell을 사용하여 배포

다음 절차에서는 표준 계층 Service Bus 네임스페이스와, 네임스페이스 안의 큐를 만드는 Azure Resource Manager 템플릿을 PowerShell을 사용하여 배포하는 방법을 설명합니다. 이 예제는 [큐가 있는 Service Bus 네임스페이스 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) 템플릿을 기초로 합니다. 대략적인 워크플로 다음과 같습니다.

1. PowerShell을 설치합니다.
2. 템플릿 및 매개 변수 파일(옵션)을 만듭니다.
3. PowerShell에서 Azure 계정에 로그인합니다.
4. 새 리소스 그룹이 아직 없으면 만듭니다.
5. 배포를 테스트합니다.
6. 원하는 경우 배포 모드를 설정합니다.
7. 템플릿을 배포합니다.

Azure Resource Manager 배포 템플릿에 대한 모든 내용은 [Azure Resource Manager 템플릿으로 리소스 배포][Deploy resources with Azure Resource Manager templates]를 참조하세요.

### <a name="install-powershell"></a>PowerShell 설치

[Azure PowerShell 시작하기](/powershell/azure/get-started-azureps)의 지침을 따라서 Azure PowerShell을 설치합니다.

### <a name="create-a-template"></a>템플릿 만들기

리포지토리를 복제하거나 GitHub에서 [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) 템플릿을 복사합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>매개 변수 파일 만들기(옵션)

옵션인 매개 변수 파일을 사용하려면 [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) 파일을 복사합니다. `serviceBusNamespaceName` 값을 이 배포에 만들려는 Service Bus 네임스페이스의 이름으로 바꾸고, `serviceBusQueueName` 값을 만들려는 큐의 이름으로 바꿉니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

자세한 내용은 [매개 변수](../azure-resource-manager/templates/parameter-files.md) 문서를 참조하세요.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Azure에 로그인하고 Azure 구독 설정

PowerShell 프롬프트에서 다음 명령을 실행합니다.

```powershell
Connect-AzAccount
```

Azure 계정에 로그온하라는 메시지가 표시됩니다. 로그온한 후 다음 명령을 실행하여 사용 가능한 구독을 확인합니다.

```powershell
Get-AzSubscription
```

이 명령은 사용 가능한 Azure 구독 목록을 반환합니다. 다음 명령을 실행하여 현재 세션에 대한 구독을 선택합니다. `<YourSubscriptionId>`는 사용할 Azure 구독의 GUID로 바꿉니다.

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>리소스 그룹 설정

기존 리소스 그룹이 없는 경우 **New-AzResourceGroup** 명령을 사용하여 새 리소스 그룹을 만듭니다. 사용할 리소스 그룹의 이름과 위치를 입력합니다. 예를 들면 다음과 같습니다.

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

성공하면 새 리소스 그룹에 대한 요약이 표시됩니다.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>배포 테스트

`Test-AzResourceGroupDeployment` cmdlet을 실행하여 배포의 유효성을 검사합니다. 배포를 테스트할 때는 배포를 실행할 때처럼 정확하게 매개 변수를 제공합니다.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>배포 만들기

새 배포를 만들려면 `New-AzResourceGroupDeployment` cmdlet을 실행하고 메시지가 표시되면 필요한 매개 변수를 입력합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름 및 템플릿 파일의 경로 또는 URL이 포함됩니다. **Mode** 매개 변수가 지정되지 않은 경우 기본값 **Incremental**이 사용됩니다. 자세한 내용은 [증분 및 전체 배포](../azure-resource-manager/templates/deployment-modes.md)를 참조하세요.

다음 명령은 PowerShell 창에서 세 매개 변수의 입력을 요청합니다.

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

그 대신에 매개 변수 파일을 지정하려면 다음 명령을 사용합니다.

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

배포 cmdlet을 실행하면 인라인 매개 변수를 사용할 수도 있습니다. 이 명령은 다음과 같습니다.

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

[전체](../azure-resource-manager/templates/deployment-modes.md) 배포를 실행하려면 **Mode** 매개 변수를 **Complete**로 설정합니다.

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>배포 확인
리소스가 성공적으로 배포되면 배포의 요약이 PowerShell 창에 표시됩니다.

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>다음 단계
이제 Azure Resource Manager 템플릿 배포를 위한 기본 워크플로와 명령이 표시됩니다. 자세한 내용은 다음 링크를 방문하세요.

* [Azure Resource Manager 개요][Azure Resource Manager overview]
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포][Deploy resources with Azure Resource Manager templates]
* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/templates/template-syntax.md)
* [Microsoft.ServiceBus 리소스 종류](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
