---
title: Azure Service Bus-Event Grid 통합 개요 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus 메시지를 Azure Event Grid와 통합 하는 방법에 대해 설명 합니다.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f0aaa82db61b5f40e42d6dad641bc09d5add9d0f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078336"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Azure Service Bus-Event Grid 통합 개요

Azure Service Bus가 Azure Event Grid와의 새로운 통합을 시작했습니다. 이 기능의 주요 시나리오는 메시지 볼륨이 적은 Service Bus 큐 또는 구독은 항상 메시지에 대한 수신기 폴링이 필요 없다는 것입니다. 

이제 Service Bus는 수신기가 없을 때 큐 또는 구독에 메시지가 있으면 Event Grid로 이벤트를 내보낼 수 있습니다. Service Bus 네임스페이스에 대한 Event Grid 구독을 만들고, 수신기를 시작하여 이러한 이벤트를 수신 대기하고 대응할 수 있습니다. 이 기능을 통해 사후 프로그래밍 모델에 Service Bus를 사용할 수 있습니다.

기능을 사용하려면 다음 항목이 필요합니다.

* Service Bus 큐가 하나 이상 있는 Service Bus 프리미엄 네임스페이스 또는 구독이 하나 이상 있는 Service Bus 토픽.
* Service Bus 네임스페이스에 대한 참가자 액세스.
* 또한 Service Bus 네임스페이스에 대한 Event Grid 구독이 필요합니다. 이 구독은 Event Grid에서 선택할 메시지가 있다는 알림을 수신합니다. 일반적으로 구독자는 Azure App Service의 Logic Apps 기능, Azure Functions 또는 웹앱에 연결한 웹 후크일 수 있습니다. 그런 다음, 구독자는 메시지를 처리합니다. 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>참가자 액세스 권한이 있는지 확인
Service Bus 네임 스페이스로 이동한 다음, **액세스 제어 (IAM)** 를 선택 하 고 **역할 할당** 탭을 선택 합니다. 네임 스페이스에 대 한 참가자 액세스 권한이 있는지 확인 합니다. 

### <a name="events-and-event-schemas"></a>이벤트 및 이벤트 스키마

현재 Service Bus는 두 가지 시나리오에 대한 이벤트를 보냅니다.

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* DeadletterMessagesAvailable

또한 Service Bus는 표준 Event Grid 보안 및 [인증 메커니즘](../event-grid/security-authentication.md)을 사용합니다.

자세한 내용은 [Azure Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 참조하세요.

#### <a name="active-messages-available-event"></a>활성 메시지 이벤트

큐 또는 구독에 활성 메시지가 있고 수신 대기 중인 수신기가 없는 경우 이 이벤트가 생성됩니다.

이 이벤트의 스키마는 다음과 같습니다.

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-letter-messages-available-event"></a>배달 못한 편지로 처리된 메시지 이벤트

배달 못한 편지 큐마다 메시지는 있고 활성 수신기는 없는 이벤트가 하나 이상 발생합니다.

이 이벤트의 스키마는 다음과 같습니다.

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>이벤트를 보내는 빈도 및 이벤트 수는 얼마나 되나요?

네임스페이스에 큐 및 토픽 또는 구독이 여러 개 있는 경우 큐당 이벤트 하나 이상, 구독당 이벤트 하나 이상이 발생합니다. Service Bus 엔터티에 메시지가 없고 새 메시지가 도착하는 경우 해당 이벤트를 즉시 내보냅니다. 또는 Service Bus가 활성화된 수신기를 검색하지 않는 한 해당 이벤트를 매 2분마다 내보냅니다. 메시지 찾아보기가 이벤트를 중단하지 않습니다.

기본적으로 Service Bus는 네임스페이스의 모든 엔터티에 대한 이벤트를 내보냅니다. 특정 엔터티에 대한 이벤트만 발생시키려면 다음 섹션을 참조하세요.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>이벤트를 얻는 장소를 제한하려면 필터 사용

네임스페이스 내부의 특정 큐 또는 특정 구독에 대한 이벤트만 발생시키려는 경우 Event Grid에서 제공하는 *시작 문자* 또는 *끝 문자* 필터를 사용하면 됩니다. 일부 인터페이스에서 필터는 *Pre* 및 *Suffix* 필터 라고 합니다. 전부는 아니지만 여러 큐 및 구독에 대한 이벤트를 발생시키려는 경우 여러 개의 Event Grid 구독을 만들고 각각에 필터를 제공하면 됩니다.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Service Bus 네임스페이스에 대한 Event Grid 구독 만들기

세 가지 방법으로 Service Bus 네임스페이스에 대한 Event Grid 구독을 만들 수 있습니다.

* Azure Portal에서
* [Azure CLI](#azure-cli-instructions) 에서
* [PowerShell](#powershell-instructions) 에서

## <a name="azure-portal-instructions"></a>Azure Portal 지침

새 Event Grid 구독을 만들려면 다음을 수행합니다.
1. Azure Portal에서 네임스페이스로 이동합니다.
2. 왼쪽 창에서 **Event Grid**를 선택합니다. 
3. **이벤트 구독**을 선택 합니다.  

   다음 이미지는 Event Grid 구독이 있는 네임스페이스를 표시합니다.

   ![Event Grid 구독](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   다음 이미지는 특정 필터링 없이 함수 또는 웹 후크를 구독하는 방법을 보여줍니다.

   ![21][]

## <a name="azure-cli-instructions"></a>Azure CLI 지침

먼저, Azure CLI 버전 2.0 이상이 설치되어 있는지 확인 합니다. [설치 프로그램을 다운로드합니다](/cli/azure/install-azure-cli?view=azure-cli-latest). **Windows + X**를 선택한 다음, 관리자 권한으로 새 PowerShell 콘솔을 엽니다. 또는 Azure Portal 내에서 명령 셸을 사용할 수 있습니다.

다음 코드를 실행합니다.

 ```azurecli-interactive
az login

az account set -s "<Azure subscription name>"

namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv

az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" --endpoint "<your_function_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

BASH를 사용 하는 경우 

## <a name="powershell-instructions"></a>PowerShell 지침

Azure PowerShell이 설치되어 있는지 확인합니다. [설치 프로그램을 다운로드합니다](/powershell/azure/install-Az-ps). 그런 다음, **Windows + X**를 선택한 다음, 관리자 권한으로 새 PowerShell 콘솔을 엽니다. 또는 Azure Portal 내에서 명령 셸을 사용할 수 있습니다.

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

여기서 다른 설치 옵션을 살펴볼 수도 있고 해당 이벤트가 흐르고 있는지 테스트할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* Service Bus 및 Event Grid [예제](service-bus-to-event-grid-integration-example.md)를 가져옵니다.
* [Event Grid](../event-grid/index.yml)에 대해 자세히 알아봅니다.
* [Azure Functions](../azure-functions/index.yml)에 대해 자세히 알아보세요.
* [Logic Apps](../logic-apps/index.yml)에 대해 자세히 알아봅니다.
* [Service Bus](/azure/service-bus/)에 대한 자세히 알아봅니다.

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[mb]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[일]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
