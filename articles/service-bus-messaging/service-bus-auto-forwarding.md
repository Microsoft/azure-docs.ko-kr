---
title: Azure Service Bus 메시징 엔터티 자동 전달 | Microsoft Docs
description: Service Bus 큐 또는 구독을 다른 큐 또는 토픽에 연결하는 방법
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 86fa7f62230c0ae0530b67ff2384942c876083d4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098595"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>자동 전달을 사용한 Service Bus 엔터티 연결

Service Bus *자동 전달* 기능을 통해 동일한 네임스페이스의 일부인 다른 큐 또는 토픽에 큐 또는 구독을 연결할 수 있습니다. 자동 전달을 사용하도록 설정하면 Service Bus가 자동으로 첫 번째 큐 또는 구독(원본)에 있는 메시지를 제거하고 두 번째 큐 또는 토픽(대상)에 넣습니다. 대상 엔터티에 직접 메시지를 보내는 것은 여전히 가능합니다.

## <a name="using-autoforwarding"></a>자동 전달 사용

자동 전달은 다음 예제와 같이 [QueueDescription][QueueDescription] 또는 [SubscriptionDescription][SubscriptionDescription] 개체를 원본으로 하는 [QueueDescription.ForwardTo][QueueDescription.ForwardTo] 또는 [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] 속성을 설정하여 사용할 수 있습니다.

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

원본 엔터티가 생성될 시점에 대상 엔터티가 존재해야 합니다. 대상 엔터티가 없는 경우 Service Bus에 원본 엔터티를 만드는 요청을 보내면 예외가 반환됩니다.

자동 전달을 사용하여 개별 토픽을 확장할 수도 있습니다. Service Bus는 [지정된 토픽에 대한 구독 수](service-bus-quotas.md)를 2,000으로 제한합니다. 두 번째 수준의 토픽을 만들면 구독의 수를 늘릴 수 있습니다. Service Bus의 구독 수 제한에 묶여 있는 경우가 아니라도, 두 번째 수준의 토픽을 추가하면 토픽의 전체적인 처리량을 늘릴 수 있습니다.

![자동 전달 시나리오][0]

자동 전달을 사용하여 메시지 보낸 사람과 받는 사람을 분리할 수도 있습니다. 주문 처리, 재고 관리 및 고객 관계 관리라는 세 모듈로 구성된 ERP 시스템을 예로 들어 보겠습니다. 이러한 각 모듈은 해당 토픽의 큐에 저장되는 메시지를 생성합니다. Alice와 Bob은 고객에 관련된 모든 메시지에 관심이 있는 영업 담당자입니다. Alice와 Bob이 이러한 메시지를 받으려면 각 ERP 토픽에 개인 큐와 구독을 만들어 모든 메시지가 자신의 큐로 자동으로 전달되도록 해야 합니다.

![자동 전달 시나리오][1]

Alice가 휴가를 가면 ERP 토픽이 아닌 그녀의 개인 큐가 채워집니다. 이러한 경우 영업 담당자가 메시지를 확인하지 못하기 때문에 어떤 ERP 토픽도 할당량에 도달하지 못합니다.

> [!NOTE]
> 자동 전달에는 설치 되 면 AutoDeleteOnIdle 대상에 대 한 값 데이터 형식의 최대값을 자동으로 설정 됩니다.
> 이렇게 메시지를 전달할 대상 항상 인지 확인 합니다.

## <a name="autoforwarding-considerations"></a>자동 전달 관련 고려 사항

대상 엔터티에 많은 메시지가 누적되어 할당량을 초과하거나 대상 엔터티가 사용하지 않도록 설정되는 경우, 원본 엔터티는 대상에 공간이 생길 때까지(또는 엔터티가 다시 사용하도록 설정됨) 메시지를 [배달 못 한 편지 큐](service-bus-dead-letter-queues.md)에 추가합니다. 이러한 메시지는 배달 못한 큐에 계속 남아 있으므로, 배달 못한 큐에서 직접 수령하여 처리해야 합니다.

여러 구독이 있는 복합 토픽을 받기 위해 개별 토픽을 연결하는 경우, 첫 번째 수준의 토픽에 있는 구독의 수를 조정하고 두 번째 수준의 토픽에 많은 구독이 있도록 하는 것이 좋습니다. 예를 들어 첫 번째 수준의 토픽에 20개의 구독이 있고, 각 구독이 두 번째 수준의 토픽에 있는 200개 구독에 연결될 경우, 첫 번째 수준의 토픽에 200개 구독이 있고 각 구독이 두 번째 토픽에 있는 20개의 구독에 연결되는 것보다 처리량이 높습니다.

Service Bus는 전달된 각 메시지당 하나의 작업을 요청합니다. 예를 들어 각각 다른 큐나 토픽으로 메시지를 자동 전달하도록 구성된 구독이 20개인 토픽에 메시지를 보내는 경우 모든 첫 번째 수준 구독이 메시지 복사본을 수신한다면 21개 작업에 해당하는 대금이 청구됩니다.

다른 큐나 토픽에 연결될 구독을 만들려면 구독을 만든 사람에게 원본과 대상 엔터티에 대한 **관리** 권한이 있어야 합니다. 원본 토픽에 메시지를 보낼 때는 원본 토픽에 대한 **보내기** 권한만 있으면 됩니다.

## <a name="next-steps"></a>다음 단계

자동 전달에 대한 자세한 내용은 다음 참조 항목을 참조하세요.

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Service Bus 성능 향상에 대한 자세한 내용은 다음을 참조하세요. 

* [Service Bus 메시징을 사용한 성능 향상의 모범 사례](service-bus-performance-improvements.md)
* [분할된 메시징 엔터티][Partitioned messaging entities]

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
