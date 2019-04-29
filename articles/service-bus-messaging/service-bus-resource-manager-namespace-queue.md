---
title: Azure Resource Manager 템플릿을 사용하여 Azure Service Bus 네임스페이스 및 큐 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Service Bus 네임스페이스 및 큐 만들기
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 2952c446e2d66505ebaec42eb82e37be02035483
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111495"
---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Service Bus 네임스페이스 및 큐 만들기

이 문서에서는 해당 네임스페이스 내에 Service Bus 네임스페이스 및 큐를 만드는 Azure Resource Manager 템플릿을 사용하는 방법을 보여 줍니다. 이 문서는 어떤 리소스를 배포할지 지정하는 방법 및 배포를 실행할 때 지정되는 매개 변수를 정의하는 방법을 설명합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

템플릿을 만들기에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성][Authoring Azure Resource Manager templates]을 참조하세요.

전체 템플릿은 GitHub에서 [Service Bus 네임스페이스 및 큐 템플릿][Service Bus namespace and queue template]을 참조하세요.

> [!NOTE]
> 다음 Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다.
> 
> * [큐 및 권한 부여 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-auth-rule.md)
> * [토픽 및 구독이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
> * [Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace.md)
> * [토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 최신 템플릿을 확인하려면 [Azure 퀵 스타트 템플릿][Azure Quickstart Templates] 갤러리를 방문하여 **Service Bus**를 검색합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>배포할 항목

이 템플릿으로 큐가 있는 Service Bus 네임스페이스를 배포합니다.

[Service Bus 큐](service-bus-queues-topics-subscriptions.md#queues)는 하나 이상의 경쟁 소비자에게 FIFO(선입선출) 메시지 배달을 제공합니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>매개 변수

Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함된 `Parameters` 라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마세요. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿은 다음 매개 변수를 정의합니다.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
만들 Service Bus 네임스페이스 이름입니다.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Service Bus 네임스페이스에서 만든 큐의 이름입니다.

```json
"serviceBusQueueName": {
"type": "string"
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
큐가 있는 **메시징**형식의 표준 Service Bus 네임스페이스를 만듭니다.

```json
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

JSON 구문 및 속성의 경우 [네임스페이스](/azure/templates/microsoft.servicebus/namespaces) 및 [큐](/azure/templates/microsoft.servicebus/namespaces/queues)를 참조하세요.

## <a name="commands-to-run-deployment"></a>배포 실행 명령
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>다음 단계
이제 Azure Resource Manager를 사용하여 리소스를 만들고 배포했으므로 다음 문서를 참조하여 이러한 리소스를 관리하는 방법에 대해 알아봅니다.

* [PowerShell을 사용하여 Service Bus 관리](service-bus-manage-with-ps.md)
* [Service Bus 탐색기로 Service Bus 리소스 관리](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
