---
title: Azure 템플릿을 사용 하 여 Service Bus 토픽 구독 및 규칙 만들기
description: Azure Resource Manager 템플릿을 사용하여 토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f3287dd67f89f678a9875ddce93e2d0d26b2209
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077627"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기

이 문서에서는 토픽, 구독 및 규칙(필터)이 있는 Service Bus 네임스페이스를 만드는 Azure Resource Manager 템플릿을 사용하는 방법을 보여 줍니다. 이 문서는 어떤 리소스를 배포할지 지정하는 방법 및 배포를 실행할 때 지정되는 매개 변수를 정의하는 방법을 설명합니다. 자체 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다.

템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성하기][Authoring Azure Resource Manager templates]를 참조하십시오.

Azure 리소스 명명 규칙의 사례 및 패턴에 대한 자세한 내용은 [Azure 리소스에 대한 권장되는 명명 규칙][Recommended naming conventions for Azure resources]을 참조하세요.

전체 템플릿은 [토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스][Service Bus namespace with topic, subscription, and rule] 템플릿을 참조하세요.

> [!NOTE]
> 다음 Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다.
> 
> * [큐 및 권한 부여 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-auth-rule.md)
> * [큐가 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace.md)
> * [토픽 및 구독이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
> 
> 최신 템플릿을 확인 하려면 [Azure 빠른 시작 템플릿][Azure Quickstart Templates] 갤러리를 방문 하 여 Service Bus를 검색 합니다.

## <a name="what-do-you-deploy"></a>배포할 항목

이 템플릿을 사용하여 토픽, 구독 및 규칙(필터)이 있는 Service Bus 네임스페이스를 배포합니다.

[Service Bus 토픽 및 구독](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)은 *게시/구독* 패턴으로 일 대 다 형태의 통신을 제공합니다. 토픽 및 구독을 사용하는 경우, 분산된 애플리케이션의 구성 요소는 서로 직접 통신하지 않으며 대신 중간 단계로 사용되는 토픽을 통해 메시지를 교환합니다. 토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 구독에서 필터를 사용하여 토픽에 전송된 메시지 중 특정 토픽 구독 내에 표시되어야 하는 메시지를 지정할 수 있습니다.

## <a name="what-are-rules-filters"></a>규칙(필터)란?

대부분의 시나리오에서 특정 특성을 가진 메시지를 다른 방법으로 처리해야 합니다. 사용자 지정 프로세스를 사용하도록 설정하려면 구독을 구성하여 특정 속성을 갖는 메시지를 찾은 다음 해당 속성에 수정 작업을 수행하도록 구독을 구성합니다. Service Bus 구독이 토픽으로 전송된 모든 메시지를 확인하지만 가상 구독 큐로 이러한 메시지의 하위 집합을 복사할 수 있습니다. 구독 필터를 사용하여 이 복사 작업을 수행합니다. 규칙(필터)에 대한 자세한 내용은 [규칙 및 작업](service-bus-queues-topics-subscriptions.md#rules-and-actions)을 참조하세요.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>매개 변수

Azure Resource Manager를 사용하여 템플릿 배포 시에 지정하려는 값의 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함된 `Parameters` 라는 섹션을 포함합니다. 배포하는 프로젝트나 배포 대상 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿은 다음 매개 변수를 정의합니다.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

만들 Service Bus 네임스페이스 이름입니다.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Service Bus 네임스페이스에서 만든 토픽의 이름입니다.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Service Bus 네임스페이스에서 만든 구독의 이름입니다.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>serviceBusRuleName

Service Bus 네임스페이스에서 만든 규칙(필터)의 이름입니다.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

템플릿의 Service Bus API 버전입니다.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>배포할 리소스

토픽, 구독 및 규칙이 있는 **메시징** 형식의 표준 Service Bus 네임스페이스를 만듭니다.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

JSON 구문 및 속성의 경우 [네임스페이스](/azure/templates/microsoft.servicebus/namespaces), [항목](/azure/templates/microsoft.servicebus/namespaces/topics), [구독](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions) 및 [규칙](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules)을 참조하세요.

## <a name="commands-to-run-deployment"></a>배포 실행 명령

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>다음 단계

다음 문서를 검토하여 이러한 리소스를 관리하는 방법을 알아보세요.

* [Azure Service Bus 관리](service-bus-management-libraries.md)
* [PowerShell을 사용하여 Service Bus 관리](service-bus-manage-with-ps.md)
* [Service Bus 탐색기로 Service Bus 리소스 관리](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
