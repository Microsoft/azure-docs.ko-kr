---
title: 분할된 Azure Service Bus 큐 및 토픽 만들기 | Microsoft Docs
description: 여러 메시지 broker를 사용하여 Service Bus 큐 및 항목을 분할하는 방법을 설명합니다.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2019
ms.author: aschhab
ms.openlocfilehash: 699581c7ccd3f36da0cd0c1def623607b7c0a13b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589669"
---
# <a name="partitioned-queues-and-topics"></a>분할 큐 및 항목

Azure Service Bus에서는 여러 메시지 broker가 메시지를 처리하고 여러 메시징 저장소가 메시지를 저장하도록 합니다. 일반적인 큐 또는 항목은 단일 메시지 broker에서 처리되며 하나의 메시징 저장소에 저장됩니다. Service Bus *파티션*을 사용하면 큐 및 항목 또는 *메시징 엔터티*가 여러 메시지 broker 및 메시징 저장소에 분할될 수 있습니다. 분할은 분할된 엔터티의 전체 처리량이 단일 메시지 broker 또는 메시징 저장소의 성능으로 제한되지 않는다는 의미입니다. 또한 메시징 스토어가 일시적으로 중단된 경우에도 분할된 큐 또는 항목을 계속 렌더링할 수 없습니다. 분할된 큐 및 항목은 트랜잭션 및 세션에 대한 지원 같은 모든 고급 Service Bus 기능을 포함할 수 있습니다.

> [!NOTE]
> 분할은 기본 또는 표준 SKU의 모든 큐와 항목에서 엔터티 생성에 지원됩니다. 프리미엄 메시지 SKU에 지원되지 않지만 프리미엄 네임스페이스에서 기존에 분할된 엔터티는 정상적으로 계속 작동합니다.
 
기존 큐 또는 토픽에 대해 분할 옵션을 변경할 수 없으며 엔터티를 만들 때만 이 옵션을 설정할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

각 분할 된 큐 또는 토픽에는 여러 파티션 구성 됩니다. 파티션마다 다른 메시징 저장소에 저장 되 고 다른 메시지 broker에서 처리 합니다. 분할 된 큐 또는 토픽으로 메시지를 보내면 Service Bus는 메시지 파티션 중 하나에 할당 합니다. 선택은 발신자가 지정할 수 있는 Service Bus 또는 파티션 키에서 임의로 수행됩니다.

클라이언트에서 메시지를 받는 분할 된 큐 또는 Service Bus 쿼리에서 분할된 된 항목에 대 한 구독에서 메시지에 대 한 모든 파티션을 하려는 경우에 수신자에 게 메시징 저장소 중 하나에서 획득 되는 첫 번째 메시지를 반환 합니다. Service Bus는 추가 수신 요청을 받을 때 다른 메시지를 캐시하고 반환합니다. 수신하는 클라이언트는 분할을 인식하지 못합니다. 분할된 큐 또는 항목의 클라이언트 연결 동작(예, 읽기, 완료, 연기, 효력 상실, 프리페치)은 일반적인 엔터티 동작과 동일합니다.

분할된 큐 또는 항목에 메시지를 보내거나 메시지를 받을 때 추가 비용이 없습니다.

## <a name="enable-partitioning"></a>분할 사용

Azure Service Bus로 분할된 큐 및 항목을 사용하려면 Azure SDK 버전 2.2 이상을 사용하거나 HTTP 요청에서 `api-version=2013-10` 이상을 지정합니다.

### <a name="standard"></a>표준

표준 메시징 계층에서 Service Bus 큐 및 토픽은 1, 2, 3, 4 또는 5GB 크기로 만들 수 있습니다(기본값은 1GB). 분할을 사용 Service Bus는 지정 된 동일한 크기의 각 엔터티의 16 복사본 (16 개 파티션)을 만듭니다. 따라서 크기가 5GB인 큐를 만들 경우 16개의 파티션에서 최대 큐 크기는 (5 \* 16) = 80GB가 됩니다. [Azure Portal][Azure portal]의 해당 엔터티에 대한 **개요** 블레이드에서 해당 항목을 보면 분할된 큐 또는 토픽의 최대 크기를 확인할 수 있습니다.

### <a name="premium"></a>프리미엄

분할 엔터티는 프리미엄 계층 네임 스페이스에서 지원 되지 않습니다. 그러나 Service Bus 큐 및 항목은 1, 2, 3, 4, 5, 10, 20, 40 또는 80GB 크기로 만들 수 있습니다(기본값은 1GB). [Azure Portal][Azure portal]의 해당 엔터티에 대한 **개요** 블레이드에서 해당 항목을 보면 큐 또는 토픽의 크기를 확인할 수 있습니다.

### <a name="create-a-partitioned-entity"></a>분할된 엔터티 만들기

분할된 큐 또는 항목을 만들 수 있는 여러 방법이 있습니다. 애플리케이션에서 큐 또는 항목을 만들 때 [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] 또는 [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] 속성을 각각 **true**로 설정하여 큐 또는 항목에 분할을 설정할 수 있습니다. 이러한 속성은 큐 또는 항목을 만든 시간에 설정해야 하며, 이전 [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 라이브러리에서만 사용할 수 있습니다. 앞서 설명한 것처럼 기존 큐 또는 토픽에서 이러한 속성을 변경하는 것은 불가능합니다. 예를 들면 다음과 같습니다.

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

또는 [Azure Portal][Azure portal]에서 분할된 큐나 토픽을 만들 수 있습니다. 포털에서 큐 또는 토픽을 만들 때 큐 또는 토픽 **만들기** 대화 상자에 있는 **분할 사용** 옵션이 기본적으로 선택되어 있습니다. 이 옵션은 표준 계층 엔터티에서만 사용하지 않도록 설정할 수 있습니다. 프리미엄 계층에서 분할은 지원되지 않고, 확인란은 적용되지 않습니다. 

## <a name="use-of-partition-keys"></a>파티션 키의 사용

메시지가 분할된 큐 또는 항목에 큐를 넣으면 Service Bus는 파티션 키의 존재를 확인합니다. 를 찾으면 해당 키를 기준으로 파티션을 선택 합니다. 파티션 키를 찾지 못하면 내부 알고리즘을 기준으로 파티션을 선택 합니다.

### <a name="using-a-partition-key"></a>파티션 키 사용

세션 또는 트랜잭션과 같은 일부 시나리오에서는 특정 파티션에 저장할 메시지가 필요 합니다. 이러한 모든 시나리오에서는 파티션 키를 사용해야 합니다. 동일한 파티션 키를 사용 하는 모든 메시지는 동일한 파티션에 할당 됩니다. 파티션을 일시적으로 사용할 수 없는 경우 Service Bus는 오류를 반환 합니다.

시나리오에 따라 다양한 메시지 속성이 파티션 키로 사용됩니다.

**SessionId**: 메시지에 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 속성 집합이 있으면 Service Bus는 **SessionID**를 파티션 키로 사용합니다. 이러한 방식으로 동일한 세션에 속한 모든 메시지가 동일한 메시지 broker에서 처리됩니다. 세션을 사용하면 Service Bus가 세션 상태의 일관성 뿐만 아니라 메시지 순서를 보장할 수 있습니다.

**PartitionKey**: 메시지에 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 속성이 있지만 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 속성 집합이 없으면 Service Bus는 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 속성 값을 파티션 키로 사용합니다. 메시지에 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 및 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 속성 집합이 모두 있으면 속성이 모두 동일해야 합니다. [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 속성이 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 속성이 아닌 다른 값으로 설정되면 Service Bus는 잘못된 작업 예외를 반환합니다. 발신자가 비 세션 인식 트랜잭션 메시지를 보내는 경우 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 속성을 사용해야 합니다. 파티션 키는 트랜잭션 내에서 전송되는 모든 메시지가 동일한 메시징 broker에서 처리되도록 합니다.

**MessageId**: 큐 또는 항목에 [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) 속성이 **true**로 설정되고 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 또는 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 속성이 설정되지 않은 경우 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 속성 값이 파티션 키로 제공됩니다. (보내는 애플리케이션이 메시지 ID를 할당하지 않으면 Microsoft .NET 및 AMQP 라이브러리가 자동으로 메시지 ID를 할당합니다.) 이 경우에 동일한 메시지의 모든 복사본이 동일한 메시지 broker에서 처리됩니다. 이 ID를 사용하면 Service Bus가 중복 메시지를 감지하고 제거할 수 있습니다. [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) 속성이 **true**로 설정되지 않으면 Service Bus는 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 속성을 파티션 키로 고려하지 않습니다.

### <a name="not-using-a-partition-key"></a>파티션 키 사용하지 않음

파티션 키가 없으면 Service Bus는 분할 된 큐 또는 토픽의 모든 파티션에 라운드 로빈 방식으로 메시지를 분산합니다. 선택한 파티션에 사용할 수 없는 경우 Service Bus는 메시지를 서로 다른 파티션에 할당 합니다. 이러한 방식으로 메시징 저장소를 일시적으로 사용할 수 없음에도 불구하고 보내기 작업은 성공합니다. 그러나 파티션 키가 제공하는 순서가 보장되지는 않습니다.

가용성(파티션 키 없음) 및 일관성(파티션 키 사용) 간 상쇄 관계에 대한 보다 자세한 설명을 보려면 [이 문서](../event-hubs/event-hubs-availability-and-consistency.md)를 참조하세요. 이 정보는 분할된 Service Bus 엔터티에 동일하게 적용됩니다.

Service bus에 충분 한 시간을 큐에 넣기 메시지를 다른 파티션에 [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) 15 초 보다 커야 메시지를 전송 하는 클라이언트에서 지정 된 값입니다. [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) 속성을 기본값인 60초로 설정하는 것이 좋습니다.

파티션 키는 "고정" 특정 파티션에 메시지입니다. 이 파티션이 포함 된 메시징 저장소를 사용할 수 없는 경우 Service Bus는 오류를 반환 합니다. 파티션 키가 없으면 Service Bus는 서로 다른 파티션을 선택할 수 있습니다 하 고 작업 성공 합니다. 그러므로 필요한 경우가 아니면 파티션 키를 제공하지 않는 것이 좋습니다.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>고급 항목: 분할된 엔터티로 트랜잭션 사용

트랜잭션의 일부로 전송되는 메시지는 파티션 키를 지정해야 합니다. 키는 다음 속성 중 하나일 수 있습니다. [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 또는 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 동일한 트랜잭션의 일부로 전송되는 모든 메시지는 동일한 파티션 키를 지정해야 합니다. 트랜잭션 내에서 파티션 키 없이 메시지를 보내려면 Service Bus는 잘못된 작업 예외를 반환합니다. 다른 파티션 키를 가진 동일한 트랜잭션 내에서 여러 메시지를 보내려면 Service Bus는 잘못된 작업 예외를 반환합니다. 예를 들면 다음과 같습니다.

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

파티션 키로 사용 되는 속성을 설정 하는 경우 Service Bus는 특정 파티션에 대 한 메시지를 고정 합니다. 이 동작은 트랜잭션이 사용되는지 여부와 상관 없이 발생합니다. 필요하지 않은 경우 파티션 키를 지정하지 않는 것이 좋습니다.

## <a name="using-sessions-with-partitioned-entities"></a>분할된 엔터티로 세션 사용

세션 인식 토픽 또는 큐에 트랜잭션 메시지를 보내려면 메시지에 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 속성 집합이 있어야 합니다. [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 속성도 지정될 경우 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 속성과 동일해야 합니다. 다른 경우 Service Bus가 잘못된 작업 예외를 반환합니다.

일반(분할되지 않은) 큐 또는 항목과 달리 단일 트랜잭션을 사용하여 다른 세션에 여러 메시지를 보낼 수 없습니다. 시도할 경우 Service Bus가 잘못된 작업 예외를 반환합니다. 예를 들면 다음과 같습니다.

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>분할된 엔터티로 자동 메시지 전달

Service Bus는 분할된 엔터티 간에 자동 메시지 전달을 지원합니다. 자동 메시지 전달을 사용하려면 원본 큐 또는 구독에 [QueueDescription.ForwardTo][QueueDescription.ForwardTo] 속성을 설정합니다. 메시지가 파티션 키를 지정하는 경우([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 또는 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)) 해당 파티션 키를 대상 엔터티에 사용합니다.

## <a name="considerations-and-guidelines"></a>고려 사항 및 지침
* **높은 일관성 기능**: 세션, 중복 검색 또는 분할 키의 명시적 제어와 같은 기능을 사용 하는 엔터티의 경우 메시징 작업은 특정 파티션으로 라우팅됩니다 항상. 트래픽이 발생할 파티션 중 하나 또는 기본 저장소 올바른 상태가 아닙니다. 이러한 작업이 실패 하 고 가용성은 줄어듭니다. 결과적으로 분할되지 않은 엔터티보다 일관성은 훨씬 높아집니다. 모든 트래픽이 아닌 트래픽 일부에서만 문제가 나타나기 때문입니다. 자세한 내용은 이 [가용성 및 일관성 논의](../event-hubs/event-hubs-availability-and-consistency.md)를 참조하세요.
* **관리**: 엔터티의 모든 파티션에 대해 Create, Update 및 Delete 등의 작업을 수행 되어야 합니다. 파티션의 정상이 아니면 이러한 작업에 실패에서 될 수 있습니다. 가져오기 작업에 대 한 정보 메시지 수와 같은 집계 해야 모든 파티션에서 합니다. 모든 파티션 정상이 아니면 엔터티 가용성 상태가 제한으로 보고 됩니다.
* **저용량 메시지 시나리오**: 이런 시나리오에서, 특히 HTTP 프로토콜을 사용하는 경우 모든 메시지를 가져오기 위해 여러 수신 작업을 수행해야 할 수 있습니다. 수신 요청에 대 한 프런트 엔드 모든 파티션에 대해 수신을 수행 하 고 받은 모든 응답을 캐시. 동일한 연결에 대한 후속 수신 요청은 이러한 캐싱 덕분에 수신 대기 시간이 감소합니다. 그러나 연결이 여러 개 있거나 HTTP를 사용하는 경우 각 요청에 대해 새 연결이 설정됩니다. 따라서 동일한 노드에 요청이 들어온다고 보장할 수 없습니다. 모든 기존 메시지가 잠기고 다른 프런트 엔드에 캐시될 경우 수신 작업은 **null**을 반환합니다. 결과적으로 메시지가 만료되고 다시 받을 수 있습니다. HTTP 연결 유지를 사용하는 것이 좋습니다.
* **메시지 찾아보기/엿보기**: 이전 [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 라이브러리에서만 사용할 수 있습니다. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch)가 [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) 속성에 지정된 메시지 수를 항상 반환하지는 않습니다. 이 동작에는 일반적으로 다음 두 가지 이유가 있습니다. 하나는 메시지 컬렉션의 집계 크기가 최대 크기인 256KB를 초과하기 때문입니다. 또 다른 이유는 큐 또는 토픽이 [EnablePartitioning 속성](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning)을 **true**로 설정하면 파티션에 요청된 메시지 수를 완료하기 위한 충분한 메시지에 없을 수 있기 때문입니다. 일반적으로 애플리케이션이 특정 개수의 메시지를 받으려는 경우 해당 메시지 수에 도달할 때까지 또는 엿볼 수 있는 추가 메시지가 없게 될 때까지 [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch)를 반복적으로 호출해야 합니다. 코드 샘플을 비롯한 자세한 내용은 [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) 또는 [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API 설명서를 참조하세요.

## <a name="latest-added-features"></a>최근에 추가된 기능

* 이제 분할된 엔터티에 대해 규칙 추가 또는 제거가 지원됩니다. 분할되지 않은 엔터티와 달리, 트랜잭션에서 이러한 작업은 지원되지 않습니다. 
* AMQP는 분할된 엔터티와 메시지를 주고받기 위해 지원됩니다.
* 이제 AMQP가 다음 작업에 지원됩니다. [Batch 전송](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Batch 수신](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [시퀀스 번호로 수신](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [엿보기](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [잠금 갱신](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [메시지 예약](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [예약된 메시지 취소](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [규칙 추가](/dotnet/api/microsoft.azure.servicebus.ruledescription), [규칙 제거](/dotnet/api/microsoft.azure.servicebus.ruledescription), [세션 잠금 갱신](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [세션 상태 설정](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [세션 상태 가져오기](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate) 및 [세션 열거](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions)

## <a name="partitioned-entities-limitations"></a>분할된 엔터티 제한 사항

현재 Service Bus에서는 분할된 큐 및 항목에 다음과 같은 제한 사항이 적용됩니다.

* 프리미엄 메시징 계층에서 분할된 큐 및 항목이 지원되지 않습니다. 세션은 SessionId를 사용하는 방식을 통해 프리미어 계층에서 지원됩니다. 
* 분할된 큐 및 항목은 단일 트랜잭션에서 다른 세션에 속한 보내는 메시지를 지원하지 않습니다.
* Service Bus는 현재 네임스페이스당 최대 100개의 분할된 큐 또는 항목을 허용합니다. 각 분할된 큐 또는 항목은 네임스페이스 당 10,000개의 엔터티를 할당량으로 계산합니다(프리미엄 계층에는 적용되지 않음).

## <a name="next-steps"></a>다음 단계

[AMQP 1.0 프로토콜 가이드](service-bus-amqp-protocol-guide.md)에서 AMQP 1.0 메시징 사양의 핵심 개념을 읽어보세요.

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
