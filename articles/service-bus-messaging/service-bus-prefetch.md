---
title: Azure Service Bus 메시지 프리페치 | Microsoft Docs
description: Azure Service Bus 메시지를 프리페치하여 성능을 향상시킵니다.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: aschhab
ms.openlocfilehash: c63e6bf66e4832a1a5b0b5e6fc3dfbbf02d1e490
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125851"
---
# <a name="prefetch-azure-service-bus-messages"></a>Azure Service Bus 메시지 프리페치

*프리페치*를 공식적인 Service Bus 클라이언트에서 사용하도록 설정하면 수신기는 애플리케이션이 처음에 요청한 수준을 넘어 [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) 제한까지 더 많은 메시지를 자동으로 획득합니다.

따라서 단일 초기 [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) 또는 [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) 호출은 가능한 한 빠르게 반환되는 메시지를 즉시 사용하기 위해 획득합니다. 그런 후 클라이언트는 백그라운드에서 추가 메시지를 획득하여 프리페치 버퍼를 채웁니다.

## <a name="enable-prefetch"></a>프리페치 사용

.NET에서 **MessageReceiver**, **QueueClient** 또는 **SubscriptionClient**의 [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) 속성을 0보다 큰 수로 설정하여 프리페치 기능을 사용하도록 설정합니다. 이 값을 0으로 설정하면 프리페치가 해제됩니다.

이 설정을 [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) 또는 [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) 샘플 설정의 수신 쪽에 쉽게 추가하여 해당 컨텍스트에 미치는 영향을 확인할 수 있습니다.

메시지를 프리페치 버퍼에서 사용할 수 있지만 모든 후속 **Receive**/**ReceiveAsync** 호출은 버퍼에서 즉시 이행되고, 버퍼는 공간이 사용 가능해지면 백그라운드에서 보충됩니다. 배달할 수 있는 메시지가 없으면 수신 작업은 버퍼를 비우고 예상대로 대기하거나 차단됩니다.

또한 프리페치는 [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) 및 [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API와도 같은 방식으로 작동합니다.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>속도가 더 빠른데도 프리페치를 기본 옵션으로 사용하지 않는 이유는 무엇인가요?

프리페치는 애플리케이션을 요청할 때 및 그 전에 메시지를 로컬로 쉽게 검색할 수 있도록 하여 메시지 흐름 속도를 높입니다. 이러한 처리량 혜택은 애플리케이션 작성자가 명시적으로 수행해야 하는 절충의 결과입니다.

[ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 수신 모드를 사용할 경우 프리페치 버퍼로 획득되는 모든 메시지는 큐에서 더 이상 사용할 수 없으며 **Receive**/**ReceiveAsync** 또는 **OnMessage**/**OnMessageAsync** API를 통해 애플리케이션에 수신될 때까지 메모리 내 프리페치 버퍼에만 상주합니다. 메시지가 애플리케이션에 수신되기 전에 애플리케이션이 종료되면 해당 메시지는 복구할 수 없게 손실됩니다.

[PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) 수신 모드에서 프리페치 버퍼로 가져온 메시지는 잠금 상태에서 버퍼로 인출되며 시간 제한 클럭이 잠금 시간을 계산합니다. 프리페치 버퍼가 크고 처리하는 데 너무 오래 걸려서 메시지가 프리페치 버퍼에 상주하는 동안 또는 심지어 애플리케이션이 메시지를 처리하는 동안에도 메시지 잠금이 만료되는 경우 애플리케이션이 처리할 이벤트에 혼동이 발생할 수 있습니다.

애플리케이션은 만료되었거나 만료가 임박한 잠금이 적용된 메시지를 획득할 수 있습니다. 이 경우 애플리케이션은 메시지를 처리할 수 있지만 잠금 만료로 인해 완료할 수 없음을 알게 됩니다. 애플리케이션은 [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) 속성을 확인할 수 있습니다(브로커 및 로컬 컴퓨터 클럭 간 클럭 오차 적용). 메시지 잠금이 만료되면 애플리케이션은 메시지를 무시해야 합니다. 즉, 메시지에 대한 API 호출이나 메시지를 사용한 API 호출은 수행되지 않아야 합니다. 메시지가 만료되지 않았으나 만료가 임박한 경우 [message.RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)을 호출하여 잠금을 갱신하고 다른 기본 잠금 기간까지 연장할 수 있습니다.

잠금이 프리페치 버퍼에서 자동으로 만료되면 메시지는 중단된 것으로 처리되며 큐에서 다시 검색할 수 있습니다. 이 경우 메시지가 프리페치 버퍼로 인출되고 맨 끝에 배치됩니다. 일반적으로 메시지 만료 동안 프리페치 버퍼를 사용할 수 없는 경우 메시지는 반복적으로 프리페치되지만 유용한 상태(유효하게 잠금 처리) 상태로 전달되지 못하며 결과적으로 최대 배달 횟수가 초과되면 배달 못 한 메시지 큐로 이동됩니다.

메시지 처리에 대해 높은 수준의 안정성이 필요하며 처리에 많은 작업과 시간이 소요될 경우 을 해야 하는 경우 프리페치 기능을 신중하게 사용하거나 아예 사용하지 않는 것이 좋습니다.

높은 처리량이 필요하고 메시지 처리 비용이 일반적으로 저렴한 경우 프리페치 기능을 사용하면 처리량이 크게 개선됩니다.

큐 또는 구독에 구성된 최대 프리페치 수와 잠금 기간을 적절히 조정하여, 적어도 잠금 시간 제한이 프리페치 버퍼의 최대 크기+1개 메시지에 대해 예상되는 누적 메시지 처리 시간을 초과하도록 해야 합니다. 동시에 잠금 시간 제한은 메시지가 최대 [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive)를 초과할 수 있을 만큼 길게 설정하지 않아야 합니다. 이 기간이 초과되면 메시지가 실수로 삭제되므로 잠금이 만료될 때까지 기다렸다가 다시 배달해야 합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
