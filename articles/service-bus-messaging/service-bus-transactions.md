---
title: Azure Service Bus에서 트랜잭션 처리 개요
description: 이 문서에서는 Azure Service Bus의 트랜잭션 처리 및 전송 via 기능에 대 한 개요를 제공 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f51e570775fbce8a316d98b5198fa906173dc755
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999957"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Service Bus 트랜잭션 처리의 개요

이 문서에서는 Microsoft Azure Service Bus의 트랜잭션 기능을 설명합니다. 설명의 대부분은 [Service Bus 샘플을 사용 하는 Amqp 트랜잭션에](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)설명 되어 있습니다. 이 문서는 트래잭션 처리에 대한 개요와 Service Bus의 *send via* 기능으로 제한되며 원자적 트랜잭선 샘플의 범위는 훨씬 광범위하고 더 복잡합니다.

## <a name="transactions-in-service-bus"></a>Service Bus의 트랜잭션

*트랜잭션은* 둘 이상의 작업을 *실행 범위로*그룹화 합니다. 기본적으로 이러한 트랜잭션은 지정된 작업 그룹에 속한 모든 작업이 성공 또는 실패해야 합니다. 이러한 점에서 트랜잭션은 하나의 단위(또는 *원자성*이라고 함)로 작용합니다.

Service Bus는 트랜잭션 메시지 broker이며 메시지 저장소에 대한 모든 내부 작업의 트랜잭션 무결성을 보장합니다. 엔터티 간 메시지의 [원자성 전달](service-bus-auto-forwarding.md) 또는 [배달 못한 편지 큐](service-bus-dead-letter-queues.md)로 메시지 이동과 같이 Service Bus 내 메시지의 모든 전송은 트랜잭션입니다. 따라서 Service Bus에서 메시지를 수락할 경우 이미 저장되어 시퀀스 번호가 레이블로 지정되었습니다. 이후로 Service Bus 내 메시지 전송은 엔터티 전반에서 조정된 작업으로 손실(원본 성공 및 대상 실패) 또는 메시지의 중복(원본 실패 및 대상 성공)이 발생하지 않습니다.

Service Bus는 트랜잭션 범위 내에서 단일 메시징 엔터티(큐, 토픽, 구독)에 대한 그룹화 작업을 지원합니다. 예를 들어 트랜잭션 범위 내에서 하나의 큐에 여러 메시지를 보낼 수 있으며, 트랜잭션이 성공적으로 완료되면 메시지가 큐의 로그에만 커밋됩니다.

## <a name="operations-within-a-transaction-scope"></a>트랜잭션 범위 내 작업

트랜잭션 범위 내에서 수행할 수 있는 작업은 다음과 같습니다.

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: `Send` , `SendAsync` , `SendBatch` ,`SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: `Complete` , `CompleteAsync` , `Abandon` , `AbandonAsync` , `Deadletter` , `DeadletterAsync` , `Defer` `DeferAsync` `RenewLock` ,,, `RenewLockAsync` 

애플리케이션이 일부 수신 루프 내에서 또는 [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) 콜백과 함께 [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) 모드를 사용하여 메시지를 획득한다고 가정하므로 수신 작업은 포함되지 않으며, 메시지 처리에 대한 트랜잭션 범위만 열립니다.

메시지 처리(완료, 중단, 배달 못 한 편지, 지연)가 범위 내에서 그리고 전반적인 트랜잭션 결과에 다라 발생합니다.

## <a name="transfers-and-send-via"></a>전송 및 "send via"

큐에서 프로세서, 그런 다음 다른 큐로 데이터의 트랜잭션을 인도하기 위해 Service Bus는 *전송*을 지원합니다. 전송 작업에서 발신자는 먼저 *전송 큐*에 메시지를 보내며, 전송 큐는 autoforward 기능에서 사용 하는 것과 동일한 강력한 전송 구현을 사용 하 여 메시지를 의도 된 대상 큐로 즉시 이동 합니다. 이 메시지는 전송 큐의 소비자에게 표시되는 방식으로 전송 큐의 로그에 커밋되지 안습니다.

이 트랜잭션 기능 자체는 전송 큐가 발신자의 입력 메시지 원본인 경우에 분명해집니다. 즉, Service Bus는 하나의 통합된 원자성 작업으로 입력 메시지에 대한 완료(또는 지연, 또는 배달 못 한 편지) 작업을 수행하면서 메시지를 전송 큐를 “통해" 대상 큐로 메시지를 전송할 수 있습니다. 

### <a name="see-it-in-code"></a>실제 코드 엿보기

이러한 전송을 설정하기 위해 전송 큐를 통해 대상 큐를 목표로 하는 메시지 보낸 사람을 생성합니다. 또한 같은 큐에서 메시지를 풀링하는 수신기가 있을 수도 있습니다. 예를 들어:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

다음 예제와 같이 단순 트랜잭션은 이러한 요소를 사용 합니다. 전체 예제를 참조 하려면 [GitHub의 소스 코드](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)를 참조 하세요.

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="timeout"></a>제한 시간
2 분 후에 트랜잭션이 시간 초과 됩니다. 트랜잭션의 첫 번째 작업이 시작 되 면 트랜잭션 타이머가 시작 됩니다. 

## <a name="next-steps"></a>다음 단계

Service Bus 큐에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Service Bus 큐를 사용하는 방법](service-bus-dotnet-get-started-with-queues.md)
* [자동 전달을 사용한 Service Bus 엔터티 연결](service-bus-auto-forwarding.md)
* [Autoforward 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Service Bus 샘플과 함께 원자성 트랜잭션](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure 큐와 Service Bus 큐 비교](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


