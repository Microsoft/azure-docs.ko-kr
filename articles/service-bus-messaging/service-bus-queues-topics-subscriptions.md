---
title: Azure Service Bus 메시지 큐, 토픽 및 구독 개요 | Microsoft Docs
description: Service Bus 메시징 엔터티의 개요
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: aschhab
ms.openlocfilehash: 7cacabf4f171189810e943043b5513e20113d962
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125817"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus 큐, 토픽 및 구독

Microsoft Azure Service Bus는 신뢰할 수 있는 메시지 큐 및 지속형 게시/구독 메시징을 포함하여 클라우드 기반, 메시지 지향 미들웨어 기술 집합을 지원합니다. 이러한 조정된 메시징 기능은 Service Bus 메시징 워크로드를 사용하여 분리된 메시징 기능 게시-구독, 임시 분리 및 부하 분산 시나리오로 여겨질 수 있습니다. 분리된 통신에는 많은 장점이 있습니다. 예를 들어 필요하면 클라이언트와 서버가 연결되고 비동기 방식으로 작업을 수행할 수 있습니다.

Service Bus에서 메시지 기능의 핵심이 되는 메시지 엔터티는 큐, 토픽 및 구독, 규칙/동작입니다.

## <a name="queues"></a>큐

큐는 하나 이상의 경쟁 소비자에게 *FIFO(선입선출)* 메시지 배달을 제공합니다. 즉, 일반적으로 수신기는 큐에 추가된 순서대로 메시지를 수신하여 처리하며, 하나의 메시지 소비자만 각 메시지를 수신하여 처리합니다. 큐를 사용하는 주요 이점은 애플리케이션 구성 요소를 "임시로 분리"할 수 있다는 점입니다. 즉, 메시지가 큐에서 영구적으로 저장되기 때문에 생산자(발신자) 및 소비자(수신자)가 동시에 메시지를 보내고 받을 필요가 없습니다. 또한 생산자는 계속해서 메시지를 처리하고 보내기 위해 소비자의 회신을 기다릴 필요가 없습니다.

관련된 이점은 “부하 평준화”로 생산자와 소비자가 서로 다른 속도로 메시지를 주고받을 수 있습니다. 많은 애플리케이션에서 시스템 부하는 시간에 따라 다르지만 각 작업 단위에 필요한 처리 시간은 일반적으로 일정합니다. 큐를 사용한 메시지 생산자와 소비자 조정은 최대 부하 대신 평균 부하를 다룰 수 있으려면 소비 애플리케이션만 프로비전해야 함을 의미합니다. 수신 부하가 변경됨에 따라 큐의 깊이가 증가하고 축소됩니다. 따라서 애플리케이션 부하를 처리하는 데 필요한 인프라의 크기와 관련하여 비용을 직접 절약할 수 있습니다. 부하가 증가하면 큐에서 읽을 작업자 프로세스가 더 추가될 수 있습니다. 각 메시지는 하나의 작업자 프로세스를 통해서만 처리됩니다. 또한 이 가져오기 기반 부하 분산에서는 작업자 컴퓨터가 최대 속도로 메시지를 가져올 때 처리 능력이 다른 경우에도 작업자 컴퓨터의 최적 사용률을 허용합니다. 이 패턴을 종종 “경쟁 소비자” 패턴이라고 부릅니다.

메시지 생산자와 소비자 간을 중개하는 큐를 사용하면 구성 요소 간에 내재하는 느슨한 연결을 제공합니다. 생산자와 소비자가 서로를 인식하지 않기 때문에 생산자에 영향을 주지 않고 소비자를 업그레이드할 수 있습니다.

### <a name="create-queues"></a>큐 만들기

[Azure Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md) 또는 [Resource Manager 템플릿](service-bus-resource-manager-namespace-queue.md)을 사용하여 큐를 만듭니다. 그런 다음, [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 개체를 사용하여 메시지를 보내고 받습니다.

큐를 만든 다음, 큐에서 메시지를 보내고 받는 방법을 빠르게 알아보려면 각 방법에 대한 [빠른 시작](service-bus-quickstart-portal.md)을 참조하세요. 큐를 사용하는 방법에 대한 자세한 자습서는 [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)을 참조하세요.

작업 샘플을 보려면 GitHub의 [BasicSendReceiveUsingQueueClient 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient)을 참조하세요.

### <a name="receive-modes"></a>수신 모드

Service Bus에서 메시지를 받는 두 가지 다른 모드 (*ReceiveAndDelete* 또는 *PeekLock*)를 지정할 수 있습니다. [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) 모드에서 수신은 1단계 작업입니다. 즉, Service Bus가 요청을 받으면 메시지를 이용되는 것으로 표시하고 애플리케이션에 반환합니다. **ReceiveAndDelete** 모드는 가장 간단한 모델이며, 오류가 발생할 경우 애플리케이션에서 메시지 처리를 허용할 수 없는 시나리오에 가장 적합합니다. 이 시나리오를 이해하려면 소비자가 수신 요청을 실행한 다음, 처리하기 전에 충돌하는 시나리오를 고려합니다. Service Bus가 메시지를 이용되는 것으로 표시했기 때문에 애플리케이션이 다시 시작되고 메시지 이용을 다시 시작할 때 크래시 전에 이용된 메시지는 누락됩니다.

[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) 모드에서는 수신 작업이 2단계이므로 메시지 누락이 허용되지 않는 애플리케이션을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션에서 메시지 처리가 완료되면(또는 추가 처리를 위해 안정적으로 저장되면), 수신된 메시지에서 [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. Service Bus에서 **CompleteAsync** 호출이 확인되면 메시지를 사용 중인 것으로 표시합니다.

수신 애플리케이션에서 어떤 이유로 메시지를 처리할 수 없는 경우 받은 메시지에서 [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 메서드 대신 [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 메서드를 호출할 수 있습니다. 이 메서드를 사용하면 Service Bus에서 메시지의 잠금을 해제하고 동일한 소비자 또는 다른 경쟁 소비자에서 메시지를 다시 받을 수 있습니다. 두 번째로, 잠금과 연결된 시간 제한이 있으며, 잠금 시간 제한이 만료되기 전에 애플리케이션에서 메시지를 처리하지 못하는 경우(예: 애플리케이션이 충돌하는 경우) Service Bus에서 메시지의 잠금을 해제하여 다시 받을 수 있게 합니다(기본적으로 [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 작업 수행).

메시지를 처리한 후 애플리케이션이 충돌하지만 **CompleteAsync** 요청이 실행되기 전에 메시지가 다시 시작되면 애플리케이션에 다시 전달됩니다. 이 프로세스는 종종 *한 번 이상* 처리라고 합니다. 즉, 각 메시지가 한 번 이상 처리됩니다. 그러나 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 시나리오가 중복 처리를 허용하지 않는 경우 메시지의 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 속성에 따라 얻을 수 있는 중복을 검색하려면 애플리케이션에 추가 논리가 필요하며 이는 전달 시도를 걸쳐 일관성을 유지합니다. 이 기능은 *정확히 한번* 처리라고 합니다.

## <a name="topics-and-subscriptions"></a>토픽 및 구독

각 메시지가 단일 소비자에 의해 처리되는 큐와 반대로, *토픽*과 *구독*은 *게시/구독* 패턴을 사용하여 일 대 다 형태의 통신을 제공합니다. 크기를 많은 수의 받는 사람으로 조정하는 데 유용하며, 게시된 각 메시지는 토픽에 등록된 각 구독에서 사용할 수 있습니다. 메시지를 토픽에 보내고 구독 단위로 설정할 수 있는 필터 규칙에 따라 하나 이상의 연결된 구독에 전달합니다. 구독은 추가 필터를 사용하여 수신하려는 메시지를 제한할 수 있습니다. 메시지는 큐로 전송된 것과 동일한 방식으로 토픽에 전송되지만 메시지는 토픽에서 직접 수신되지 않습니다. 대신 구독에서 수신합니다. 토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 메시지는 큐에서 수신하는 방식과 동일하게 구독에서 수신됩니다.

비교를 통해 큐의 메시지 보내기 기능은 토픽에 직접 매핑하고 해당 메시지 받기 기능은 구독에 매핑합니다. 다른 기능 중에서 이 기능은 구독에서 큐와 관련하여 이 섹션의 앞부분에서 설명한 동일한 패턴(경쟁 소비자, 임시 분리, 부하 평준화 및 부하 분산)을 지원한다는 것을 의미합니다.

### <a name="create-topics-and-subscriptions"></a>토픽 및 구독 만들기

토픽을 만드는 것은 이전 섹션에서 설명한 대로 큐를 만드는 것과 비슷합니다. 그런 다음, [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) 클래스를 사용하여 메시지를 보냅니다. 메시지를 받으려면 토픽에 대한 구독을 하나 이상 만듭니다. 큐와 마찬가지로 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 개체 대신 [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) 개체를 사용하여 구독에서 메시지를 수신합니다. 토픽의 이름, 구독의 이름 및 (선택 사항)수신 모드를 매개 변수로 전달하는 구독 클라이언트를 만듭니다.

전체 작업 예제를 보려면 GitHub의 [BasicSendReceiveUsingTopicSubscriptionClient 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient)을 참조하세요.

### <a name="rules-and-actions"></a>규칙 및 동작

대부분의 시나리오에서 특정 특성을 가진 메시지를 다른 방법으로 처리해야 합니다. 이 처리를 사용하려면 desired 속성이 있는 메시지를 찾은 다음, 해당 속성에 대한 특정 수정 작업을 수행하도록 구독을 구성하면 됩니다. Service Bus 구독이 토픽으로 전송된 모든 메시지를 확인하는 동안 가상 구독 큐로 이러한 메시지의 하위 집합을 복사할 수 있습니다. 이 필터링은 구독 필터를 사용하여 수행됩니다. 이와 같은 수정을 *필터 동작*이라고 합니다. 구독을 만들 경우 메시지의 속성, 즉 시스템 속성(예: **Label**) 및 사용자 지정 애플리케이션 속성(예:**StoreName**) 모두에서 작동하는 필터 식을 제공할 수 있습니다. 이 경우에 SQL 필터 식은 선택 사항입니다. SQL 필터 식 없이 구독에 정의된 필터 작업을 구독에 대한 모든 메시지에서 수행합니다.

전체 작업 예제를 보려면 GitHub의 [TopicSubscriptionWithRuleOperationsSample 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample)을 참조하세요.

가능한 필터 값에 대한 자세한 내용은 [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) 및 [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) 클래스에 대한 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징을 사용하는 방법에 대한 자세한 내용 및 예제는 다음에 나오는 고급 항목을 참조하세요.

* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [빠른 시작: Azure Portal 및 .NET을 사용하여 메시지 보내기 및 받기](service-bus-quickstart-portal.md)
* [자습서: Azure Portal 및 토픽/구독을 사용하여 재고 업데이트](service-bus-tutorial-topics-subscriptions-portal.md)


