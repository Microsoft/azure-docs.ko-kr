---
title: Azure Service Bus를 사용하여 성능을 향상하는 모범 사례 | Microsoft Docs
description: broker 저장 메시지를 교환할 때 Azure Service Bus를 사용하여 성능을 최적화하는 방법에 대해 설명합니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: f5ce8a237bc2ba7fe15acfcd6afa0edcda7ef713
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589656"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Service Bus 메시징을 사용한 성능 향상의 모범 사례

이 문서에서는 조정된 메시지를 교환할 때 Azure Service Bus를 사용하여 성능을 최적화하는 방법에 대해 설명합니다. 이 문서의 첫 번째 부분에서는 성능 향상을 위해 제공되는 다양한 메커니즘에 대해 설명합니다. 두 번째 부분은 특정 시나리오에서 최고의 성능을 제공하는 방식으로 Service Bus를 사용하는 방법에 대해 안내합니다.

이 문서 전반적으로 “클라이언트”라는 용어는 Service Bus에 액세스하는 모든 엔터티를 가리킵니다. 클라이언트는 발신기 또는 수신기의 역할을 수행할 수 있습니다. "발신기"라는 용어는 메시지를 Service Bus 큐 또는 토픽 구독에 보내는 Service Bus 큐 또는 토픽 클라이언트에 사용됩니다. "수신기"라는 용어는 Service Bus 큐 또는 구독에서 메시지를 수신하는 Service Bus 큐 또는 구독 클라이언트를 가리킵니다.

다음 섹션에서는 Service Bus가 성능 향상을 위해 사용하는 몇 가지 개념에 대해 소개합니다.

## <a name="protocols"></a>프로토콜

Service Bus를 사용하면 클라이언트에서 세 가지 프로토콜 중 하나를 통해 메시지를 보내고 받을 수 있습니다.

1. AMQP(고급 메시지 큐 프로토콜)
2. SBMP(Service Bus 메시징 프로토콜)
3. HTTP

AMQP와 SBMP는 메시징 팩터리가 존재하는 한 Service Bus에 대한 연결을 유지하기 때문에 둘 다 보다 효율적입니다. 또한 일괄 처리와 프리페치도 구현합니다. 명시적으로 언급하지 않는 한 이 문서의 모든 내용에서는 AMQP 또는 SBMP를 사용하는 것으로 가정합니다.

## <a name="reusing-factories-and-clients"></a>팩터리 및 클라이언트 다시 사용

[QueueClient][QueueClient] 또는 [MessageSender][MessageSender]와 같은 Service Bus 클라이언트 개체는 내부 연결 관리도 제공하는 [MessagingFactory][MessagingFactory] 개체를 통해 만듭니다. 메시지를 보낸 후, 메시징 팩터리 또는 큐, 토픽, 구독 클라이언트를 닫은 다음, 다음 메시지를 보낼 때 이러한 메시징 팩터리 또는 큐, 토픽, 구독 클라이언트를 다시 만들지 않는 것이 좋습니다. 메시징 팩터리를 닫을 경우 Service Bus 서비스에 대한 연결이 삭제되고 팩터리를 다시 만들면 새 연결이 구축됩니다. 여러 작업에 대해 동일한 팩터리와 클라이언트 개체를 다시 사용하면 많은 비용이 드는 연결 작업을 하지 않아도 됩니다. 동시 비동기 작업에 대해 다중 스레드에서 이러한 클라이언트 개체를 안전하게 사용할 수 있습니다. 

## <a name="concurrent-operations"></a>동시 작업

작업(보내기, 받기, 삭제 등) 수행은 다소 시간이 걸립니다. 이 시간에는 요청 및 응답 지연 시간과 Service Bus 서비스의 작업 처리가 포함됩니다. 시간당 작업 수를 늘리려면 작업이 동시에 실행되어야 합니다. 

클라이언트가 비동기 작업을 수행하여 동시 작업을 예약합니다. 이전 요청이 완료되기 전에 다음 요청이 시작됩니다. 다음 코드 조각은 비동기 전송 작업의 예제입니다.
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  다음 코드는 비동기 수신 작업의 예제입니다. 전체 프로그램은 [여기](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues)를 참조하세요.
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>수신 모드

큐 또는 구독 클라이언트를 만들 때 ‘보기-잠금’ 또는 ‘수신 및 삭제’ 중에서 수신 모드를 지정할 수 있습니다. 기본 수신 모드는 [PeekLock][PeekLock]입니다. 이 모드에서 작동하는 경우 클라이언트가 Service Bus에서 메시지를 수신하기 위한 요청을 보냅니다. 클라이언트가 메시지를 받으면 메시지를 완료하기 위한 요청을 보냅니다.

수신 모드를 [ReceiveAndDelete][ReceiveAndDelete]로 설정하는 경우 두 단계가 단일 요청으로 결합됩니다. 그러면 전체 작업 수가 줄어들고 전체 메시지 처리량을 향상시킬 수 있습니다. 이와 같이 성능이 향상되지만 메시지 손실의 위험이 있습니다.

Service Bus는 수신 및 삭제 작업에 대한 트랜잭션을 지원하지 않습니다. 또한 클라이언트가 메시지를 연기하거나 [배달 못 한](service-bus-dead-letter-queues.md) 시나리오에서는 보기-잠금 의미 체계가 필요합니다.

## <a name="client-side-batching"></a>클라이언트 쪽 일괄 처리

클라이언트 쪽 일괄 처리에서는 큐 또는 토픽 클라이언트가 일정 시간 동안 메시지 전송을 연기할 수 있습니다. 클라이언트가 이 기간 동안 추가 메시지를 보내면 메시지를 단일 배치로 전송합니다. 또한 클라이언트 쪽 일괄 처리에서는 또한 큐 또는 구독 클라이언트가 여러 **완료** 요청을 단일 요청으로 일괄 처리합니다. 일괄 처리는 비동기 **전송** 및 **완료** 작업에만 사용할 수 있습니다. 동기 작업은 Service Bus 서비스에 즉시 보내집니다. 일괄 처리는 보기 또는 수신 작업에 대해 발생하지 않으며 클라이언트 전반에서도 발생하지 않습니다.

기본적으로 클라이언트는 20ms의 배치 간격을 사용합니다. 메시지 팩터리를 만들기 전에 [BatchFlushInterval][BatchFlushInterval] 속성을 설정하여 배치 간격을 변경할 수 있습니다. 이 설정은이 이 팩터리에서 만든 모든 클라이언트에 영향을 줍니다.

일괄 처리를 사용하지 않도록 설정하려면 [BatchFlushInterval][BatchFlushInterval] 속성을 **TimeSpan.Zero**로 설정합니다. 예를 들면 다음과 같습니다.

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

일괄 처리는 청구 가능 메시징 작업의 수에 영향을 주지 않으며 [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)을 사용하여 Service Bus 클라이언트 프로토콜에 대해서만 사용할 수 있습니다. HTTP 프로토콜은 일괄 처리를 지원하지 않습니다.

> [!NOTE]
> BatchFlushInterval 설정 일괄 처리 응용 프로그램의 관점에서 암시적 인지 확인 합니다. 즉, 응용 프로그램은 sendasync () 및 CompleteAsync()를 호출 하 고 특정 일괄 처리 호출을 수행 하지 않습니다.
>
> 명시적 클라이언트 쪽 일괄 처리를 활용 하 여 구현할 수 있습니다를 메서드 호출-아래 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> 여기 메시지의 결합 된 크기의 가격 책정 계층에서 지 원하는 최대 크기 미만 이어야 합니다.

## <a name="batching-store-access"></a>저장소 액세스 일괄 처리

큐, 토픽 또는 구독의 처리량을 높이기 위해 Service Bus는 내부 저장소에 쓸 때 여러 메시지를 일괄 처리합니다. 큐 또는 토픽에 설정된 경우 저장소에 메시지를 쓰는 작업이 일괄 처리됩니다. 큐 또는 구독에 설정된 경우 저장소에서 메시지를 삭제하는 작업이 일괄 처리됩니다. 엔터티에 대해 일괄 처리된 저장소 액세스를 사용할 경우 Service Bus는 해당 엔터티와 관련된 저장소 쓰기 작업을 최대 20ms까지 지연합니다. 

> [!NOTE]
> 20ms 일괄 처리 간격이 끝날 때 Service Bus 오류가 있더라도 메시지가 손실될 위험이 없습니다. 

이 간격 동안 발생하는 추가 저장소 작업은 배치에 추가됩니다. 일괄 처리된 저장소 액세스는 **전송** 및 **완료** 작업에만 영향을 주고 수신 작업은 영향을 받지 않습니다. 일괄 처리된 저장소 액세스는 엔터티의 속성입니다. 일괄 처리는 일괄 처리된 저장소 액세스가 가능한 모든 엔터티에서 발생합니다.

새 큐, 토픽 또는 구독을 만들면 기본적으로 일괄 처리된 저장소 액세스가 사용하도록 설정됩니다. 일괄 처리된 저장소 액세스를 사용하지 않으려면 엔터티를 만들기 전에 [EnableBatchedOperations][EnableBatchedOperations] 속성을 **false**로 설정합니다. 예를 들면 다음과 같습니다.

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

일괄 처리된 저장소 액세스는 청구 가능한 메시징 작업의 수에 영향을 주지 않으며 큐, 토픽 또는 구독의 속성입니다. 또한 수신 모드와 독립적이며 클라이언트와 Service Bus 서비스 간 사용되는 프로토콜과도 무관합니다.

## <a name="prefetching"></a>프리페치

[프리페치](service-bus-prefetch.md)에서는 큐 또는 구독 클라이언트가 수신 작업을 수행할 때 서비스에서 추가 메시지를 로드할 수 있습니다. 클라이언트는 이러한 메시지를 로컬 캐시에 저장합니다. 캐시 크기는 [QueueClient.PrefetchCount][QueueClient.PrefetchCount] 또는 [SubscriptionClient.PrefetchCount][SubscriptionClient.PrefetchCount] 속성으로 결정됩니다. 프리페치를 사용할 수 있는 각 클라이언트는 각각의 캐시를 유지합니다. 캐시는 클라이언트 사이에서 공유되지 않습니다. 클라이언트가 수신 작업을 시작할 때 캐시가 비어 있으면 서비스에서 메시지의 배치를 전송합니다. 배치의 크기는 캐시의 크기와 256KB 중 더 작은 값입니다. 클라이언트가 수신 작업을 시작할 때 캐시에 메시지가 포함되어 있으면 캐시에서 해당 메시지를 가져옵니다.

메시지가 프리페치되는 경우 서비스는 프리페치된 메시지를 잠급니다. 잠금을 사용하면 프리페치된 메시지를 다른 수신기가 받을 수 없습니다. 잠금이 만료되기 전에 수신기가 메시지를 완료할 수 없는 경우 다른 수신기가 메시지를 사용할 수 있습니다. 프리페치된 메시지의 복사본은 캐시에 남아 있습니다. 만료 및 캐시된 복사본을 사용하는 수신기가 해당 메시지를 완료하려고 하면 예외를 수신합니다. 기본적으로 메시지 잠금은 60초 후에 만료됩니다. 이 값은 5분으로 연장할 수 있습니다. 만료된 메시지를 사용하지 못하도록 하려면 캐시 크기가 언제나 잠금 시간 초과 간격 이내에 클라이언트가 사용할 수 있는 메시지 수보다 작아야 합니다.

기본 잠금 만료 시간인 60초를 사용할 경우 [PrefetchCount][SubscriptionClient.PrefetchCount]에는 모든 팩터리 수신기의 최대 처리 속도보다 20배 높은 값이 적합합니다. 예를 들어 팩터리에서 수신기 3개를 만들 경우 각 수신기는 초당 최대 10개의 메시지를 처리할 수 있습니다. 프리페치 수가 20 X 3 X 10 = 600을 초과해서는 안됩니다. 기본적으로 [PrefetchCount][QueueClient.PrefetchCount]는 서비스에서 추가 메시지를 페치하지 않음을 의미하는 0으로 설정되어 있습니다.

메시지를 프리페치할 경우 전체 메시지 작업 수 또는 왕복 수가 감소하므로 큐 또는 구독에 대한 전체 처리량이 증가합니다. 그러나 첫 번째 메시지를 페치하는 작업은 증가된 메시지 크기로 인해 더 오래 걸립니다. 프리페치된 메시지는 클라이언트에서 이미 다운로드한 메시지이므로 더 빠르게 수신할 수 있습니다.

메시지의 TTL(Time to Live) 속성은 서버가 클라이언트에 메시지를 보낼 때 서버에 의해 확인됩니다. 클라이언트는 메시지를 수신할 때 메시지의 TTL 속성을 검사하지 않습니다. 대신, 클라이언트가 메시지를 캐시한 동안에는 메시지의 TTL이 경과된 경우에도 메시지를 수신할 수 있습니다.

프리페치는 청구 가능 메시징 작업의 수에 영향을 주지 않으며 Service Bus 클라이언트 프로토콜에 대해서만 사용할 수 있습니다. HTTP 프로토콜은 프리페치를 지원하지 않습니다. 프리페치는 동기 및 비동기 수신 작업에 사용할 수 있습니다.

## <a name="prefetching-and-receivebatch"></a>프리페치 및 ReceiveBatch

함께 여러 메시지를 프리페치 할 개념 비슷한 의미 체계 (ReceiveBatch) 일괄 처리에서 메시지 처리를 하는 동안 이러한 작업을 함께 활용 하는 경우 염두에서 유지 해야 하는 몇 가지 사소한 차이점이 있습니다.

프리페치 구성 (또는 모드) (QueueClient 및 SubscriptionClient) 클라이언트에서 이며 ReceiveBatch는 연산을 (요청-응답 의미 체계).

이러한 작업을 함께 사용 하는 동안 다음과 같은 경우를-것이 좋습니다.

* 프리페치 보다 크거나 ReceiveBatch에서 수신 하도록 예상 되는 메시지의 수와 동일 해야 합니다.
* 여기서 n은 기본 잠금 기간이 초당 처리 된 메시지 수를 곱한 n/3 최대 프리페치 수 있습니다.

일부의 문제는 탐욕 적 갖는 방식을 (즉, 매우 높은 프리페치 수 유지) 하므로 메시지를 특정 수신자에 게 잠겨 있음을 의미 합니다. 위에서 언급 한 임계값 사이의 값을 프리페치 아웃 하 고 알려지고 실험적으로 적합 한 항목을 식별 하는 것이 좋습니다.

## <a name="multiple-queues"></a>여러 큐

분할된 단일 큐 또는 토픽으로 예상 부하를 처리할 수 없을 경우 여러 메시징 엔터티를 사용해야 합니다. 여러 엔터티를 사용할 때는 모든 엔터티에 동일한 클라이언트를 사용하는 대신 각 엔터티의 전용 클라이언트를 만듭니다.

## <a name="development-and-testing-features"></a>개발 및 테스트 기능

Service Bus의 다음 기능은 특별히 개발용으로 사용되며 **프로덕션 구성에서 사용하면 안 됩니다**. [TopicDescription.EnableFilteringMessagesBeforePublishing][].

새 규칙 또는 필터가 항목에 추가되면 [TopicDescription.EnableFilteringMessagesBeforePublishing][]을 사용하여 새 필터 식이 예상대로 작동하는지 확인할 수 있습니다.

## <a name="scenarios"></a>시나리오

다음 섹션에서는 일반적인 메시징 시나리오에 대해 설명하고 기본 Service Bus 설정에 대해 간단히 소개합니다. 처리량 속도는 적음(초당 1개 메시지 미만), 보통(초당 1개 메시지 이상, 초당 100개 메시지 미만), 높음(초당 100개 메시지 이상)으로 분류됩니다. 클라이언트 수는 적음(5개 이하), 보통(5개 초과, 20개 이하), 많음(20개 초과)으로 분류됩니다.

### <a name="high-throughput-queue"></a>처리량이 높은 큐

목표: 단일 큐의 처리량을 최대화합니다. 발신기와 수신기의 수가 작습니다.

* 큐에 대한 전반적 전송 속도를 높이려면 여러 메시지 팩터리를 만들어 발신기를 만듭니다. 각 발신기에 대해 비동기 작업이나 여러 스레드를 사용합니다.
* 큐에서 전반적 수신 속도를 높이려면 여러 메시지 팩터리를 만들어 수신기를 만듭니다.
* 클라이언트 쪽 일괄 처리를 활용하려면 비동기 작업을 사용합니다.
* 일괄 처리 간격을 50ms로 설정하여 Service Bus 클라이언트 프로토콜 전송 횟수를 줄입니다. 여러 발신기를 사용할 경우 일괄 처리 간격을 100ms로 높입니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이 액세스로 인해 메시지가 큐에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 모든 팩터리 수신기의 최대 처리 속도의 20배로 설정합니다. 이렇게 하면 Service Bus 클라이언트 프로토콜 전송 횟수가 줄어듭니다.
* 성능 및 가용성 향상을 위해 분할된 큐를 사용합니다.

### <a name="multiple-high-throughput-queues"></a>처리량이 높은 복수 큐

목표: 여러 큐의 전체 처리량을 최대화합니다. 개별 큐의 처리량은 보통 또는 높음입니다.

여러 큐에서 최대 처리량을 얻으려면 여기에서 설명하는 설정을 사용하여 단일 큐의 처리량을 최대화합니다. 또한 다른 팩터리를 사용하여 다양한 큐에서 보내거나 받는 클라이언트를 만듭니다.

### <a name="low-latency-queue"></a>대기 시간이 짧은 큐

목표: 큐 또는 토픽의 엔드투엔드 대기 시간을 최소화합니다. 발신기와 수신기의 수가 작습니다. 큐의 처리량은 적음 또는 보통입니다.

* 클라이언트 쪽 일괄 처리를 사용하지 않도록 설정합니다. 클라이언트에서 즉시 메시지를 보냅니다.
* 일괄 처리된 저장소 액세스를 사용하지 않도록 설정합니다. 서비스가 저장소에 즉시 메시지를 씁니다.
* 단일 클라이언트를 사용하는 경우 프리페치 수를 수신기의 처리 속도의 20배로 설정합니다. 동시에 여러 메시지가 큐에 도착할 경우 Service Bus 클라이언트 프로토콜은 이러한 메시지를 동시에 전송합니다. 클라이언트가 다음 메시지를 수신하면 해당 메시지는 이미 로컬 캐시에 있는 것입니다. 캐시는 작아야 합니다.
* 여러 클라이언트를 사용하는 경우 프리페치 수를 0으로 설정합니다. 이렇게 하면 첫 번째 클라이언트가 아직 첫 번째 메시지를 처리하는 동안 두 번째 클라이언트가 두 번째 메시지를 수신할 수 있습니다.
* 성능 및 가용성 향상을 위해 분할된 큐를 사용합니다.

### <a name="queue-with-a-large-number-of-senders"></a>발신기 수가 많은 큐

목표: 발신기 수가 많은 큐 또는 토픽의 처리량을 최대화합니다. 각 발신기는 보통 속도의 메시지를 전송합니다. 수신기의 수가 작습니다.

Service Bus는 메시지 엔터티에 대해 최대 1,000개의 동시 연결을 지원합니다(AMQP를 사용할 경우 5,000개). 이 제한은 네임스페이스 수준에서 적용되며, 큐/토픽/구독은 네임스페이스당 동시 연결 수로 제한됩니다. 큐의 경우 이 숫자는 발신기와 수신기 사이에 공유됩니다. 발신기에 1,000개 연결이 모두 필요할 경우 큐를 토픽 및 단일 구독으로 대체합니다. 토픽은 발신기로부터 최대 1,000개의 동시 연결을 지원하는 반면, 구독은 수신기로부터 추가 1,000개의 동시 연결을 지원합니다. 1,000개보다 많은 동시 발신기가 필요할 경우 발신기는 HTTP를 통해 Service Bus 프로토콜에 메시지를 전송해야 합니다.

처리량을 최대화하려면 다음 단계를 수행합니다.

* 각 발신기가 다른 프로세스에 상주하는 경우 프로세스당 하나의 팩터리만 사용합니다.
* 클라이언트 쪽 일괄 처리를 활용하려면 비동기 작업을 사용합니다.
* Service Bus 클라이언트 프로토콜 전송 수를 줄이려면 기본 일괄 처리 간격인 20ms를 사용합니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이 액세스로 인해 메시지가 큐 또는 토픽에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 모든 팩터리 수신기의 최대 처리 속도의 20배로 설정합니다. 이렇게 하면 Service Bus 클라이언트 프로토콜 전송 횟수가 줄어듭니다.
* 성능 및 가용성 향상을 위해 분할된 큐를 사용합니다.

### <a name="queue-with-a-large-number-of-receivers"></a>수신기 수가 많은 큐

목표: 수신기 수가 많은 큐 또는 구독의 수신 속도를 최대화합니다. 각 수신자는 보통 속도로 메시지를 받습니다. 발신기의 수가 작습니다.

Service Bus는 엔터티에 대해 최대 1,000개의 동시 연결을 지원합니다. 큐에 1,000개보다 많은 수신기가 필요할 경우 큐를 토픽 및 여러 구독으로 대체합니다. 각 구독은 최대 1,000개의 동시 연결을 지원할 수 있습니다. 또는 수신자가 HTTP 프로토콜을 통해 큐에 액세스할 수 있습니다.

처리량을 최대화하려면 다음을 수행합니다.

* 각 수신기가 다른 프로세스에 상주하는 경우 프로세스당 하나의 팩터리만 사용합니다.
* 수신기는 동기 또는 비동기 작업을 사용할 수 있습니다. 개별 수신기의 수신 속도가 보통일 경우 완료 요청의 클라이언트 쪽 일괄 처리는 수신기 처리량에 영향을 주지 않습니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이렇게 하면 엔터티의 전체 부하가 줄어듭니다. 또한 메시지가 큐 또는 토픽에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 작은 값으로 설정합니다(예: PrefetchCount = 10). 이렇게 하면 발신기에 많은 수의 메시지가 캐시된 동안 수신기가 유휴 상태가 되지 않습니다.
* 성능 및 가용성 향상을 위해 분할된 큐를 사용합니다.

### <a name="topic-with-a-small-number-of-subscriptions"></a>구독 수가 적은 토픽

목표: 구독 수가 적은 토픽의 처리량을 최대화합니다. 여러 구독에서 메시지를 수신합니다. 즉, 모든 구독의 수신 속도를 결합한 속도가 전송 속도보다 큼을 의미합니다. 발신기의 수가 작습니다. 구독당 수신기의 수가 작습니다.

처리량을 최대화하려면 다음을 수행합니다.

* 토픽에 대한 전반적 전송 속도를 높이려면 여러 메시지 팩터리를 만들어 발신기를 만듭니다. 각 발신기에 대해 비동기 작업이나 여러 스레드를 사용합니다.
* 구독에서 전반적 수신 속도를 높이려면 여러 메시지 팩터리를 만들어 수신기를 만듭니다. 각 수신기에 대해 비동기 작업이나 여러 스레드를 사용합니다.
* 클라이언트 쪽 일괄 처리를 활용하려면 비동기 작업을 사용합니다.
* Service Bus 클라이언트 프로토콜 전송 수를 줄이려면 기본 일괄 처리 간격인 20ms를 사용합니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이 액세스로 인해 메시지가 토픽에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 모든 팩터리 수신기의 최대 처리 속도의 20배로 설정합니다. 이렇게 하면 Service Bus 클라이언트 프로토콜 전송 횟수가 줄어듭니다.
* 성능 및 가용성 향상을 위해 분할된 토픽을 사용합니다.

### <a name="topic-with-a-large-number-of-subscriptions"></a>구독 수가 많은 토픽

목표: 구독 수가 많은 토픽의 처리량을 최대화합니다. 여러 구독에서 메시지를 수신합니다. 즉, 모든 구독의 수신 속도를 결합한 속도가 전송 속도보다 훨씬 큼을 의미합니다. 발신기의 수가 작습니다. 구독당 수신기의 수가 작습니다.

모든 메시지가 모든 구독으로 라우팅될 경우 구독 수가 많은 토픽은 일반적으로 전반적 처리량이 감소합니다. 낮은 처리량은 각 메시지가 여러 번 수신되며 토픽에 포함된 모든 메시지와 해당 구독이 동일한 저장소에 저장되기 때문입니다. 구독당 발신기 수와 수신기 수가 작은 경우를 가정합니다. Service Bus는 토픽당 최대 2,000개의 구독을 지원합니다.

처리량을 최대화하려면 다음 단계를 시도해 보십시오.

* 클라이언트 쪽 일괄 처리를 활용하려면 비동기 작업을 사용합니다.
* Service Bus 클라이언트 프로토콜 전송 수를 줄이려면 기본 일괄 처리 간격인 20ms를 사용합니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이 액세스로 인해 메시지가 토픽에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 초당 예상 수신 속도의 20배로 설정합니다. 이렇게 하면 Service Bus 클라이언트 프로토콜 전송 횟수가 줄어듭니다.
* 성능 및 가용성 향상을 위해 분할된 토픽을 사용합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 성능 최적화에 대한 자세한 내용은 [분할된 메시징 엔터티][Partitioned messaging entities]를 참조하세요.

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
