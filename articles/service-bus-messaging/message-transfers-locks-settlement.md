---
title: 메시지 전송, 잠금 및 정착지 Azure Service Bus
description: 이 문서에서는 메시지 전송, 잠금 및 결산일 작업 Azure Service Bus에 대 한 개요를 제공 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: fd71edd12e478bcd5f14815c105c14482cf7e2bd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020034"
---
# <a name="message-transfers-locks-and-settlement"></a>메시지 전송, 잠금 및 확인

Service Bus와 같은 메시지 브로커의 핵심적인 기능은 메시지를 큐 또는 토픽에 수신한 후 나중에 검색할 수 있도록 보관하는 것입니다. *송신*은 메시지 브로커에 대한 메시지 전송에 일반적으로 사용되는 용어입니다. *수신*은 검색 클라이언트로의 메시지 전송에 일반적으로 사용되는 용어입니다.

클라이언트는 메시지를 송신할 때 일반적으로 메시지가 브로커로 제대로 전송되었는지와 브로커에 수신되었는지 또는 오류가 발생했는지 여부를 알기 원합니다. 이러한 긍정 또는 부정 응답은 클라이언트 및 브로커가 메시지의 전송 상태를 확인하도록 하므로 *확인*이라고 합니다.

마찬가지로 브로커가 클라이언트에 메시지를 전송할 때 브로커 및 클라이언트는 메시지가 성공적으로 처리되었으므로 제거할 수 있는지 여부 또는 메시지 배달 또는 처리가 실패하여 메시지를 다시 전달해야 하는지 여부를 확인하려고 합니다.

## <a name="settling-send-operations"></a>송신 작업 확인

Service Bus에 대한 송신 작업은 지원되는 Service Bus API 클라이언트 중 하나를 사용하여 항상 명시적으로 확인됩니다. 즉, API 작업은 Service Bus의 수락 결과가 도착하기를 기다린 후 송신 작업을 완료합니다.

Service Bus에서 메시지를 거부하면 거부에는 오류 표시와 내부에 "추적 ID"가 들어 있는 텍스트가 포함됩니다. 또한 거부에는 성공을 예측하면서 작업을 다시 시도할 수 있는지 여부에 대한 정보도 포함됩니다. 클라이언트에서 이 정보는 예외로 전환되고 송신 작업의 호출자에게 제기됩니다. 메시지가 수락된 경우 작업은 자동으로 완료됩니다.

.NET Standard 클라이언트 및 Java 클라이언트에 대한 독점 프로토콜이고 [.NET Framework 클라이언트에 대한 옵션](service-bus-amqp-dotnet.md)인 AMQP 프로토콜을 사용하는 경우 메시지 전송 및 확인은 파이프라인되고 완전히 비동기식으로 유지되므로, 비동기 프로그래밍 모델 API 변형을 사용하는 것이 좋습니다.

SBMP 프로토콜 또는 HTTP 1.1을 사용하는 경우처럼 보낸 사람은 각 메시지가 승인될 때까지 기다릴 필요 없이, 여러 메시지를 빠르게 연속해서 전송할 수 있습니다. 이러한 비동기 송신 작업은 해당 메시지가 분할된 엔터티에서 수락 및 저장될 때 또는 다른 엔터티에 대한 송신 작업이 겹칠 때 완료됩니다. 완료는 원래 송신 순서와 다르게도 발생할 수 있습니다.

송신 작업의 결과를 처리하는 전략은 애플리케이션 성능에 즉각적으로 지대한 영향을 미칠 수 있습니다. 이 섹션의 예제는 C#으로 작성되며 Java Futures에도 동일하게 적용됩니다.

여기서는 일반 루프로 표시된 것처럼 애플리케이션이 갑자기 대량의 메시지를 생성하며 다음 메시지를 송신하기 전에 각 송신 작업이 완료될 때까지 대기해야 할 경우, 동기 또는 비동기 API 둘 다 비슷하게 10개 메시지 송신은 확인을 위해 10번의 순차적 전체 왕복이 수행된 후에만 완료됩니다.

온-프레미스 사이트에서 Service Bus까지의 TCP 왕복 대기 시간 거리가 70ms라고 가정하고 Service Bus에 각 메시지를 수락하고 저장하는 데 10ms만 허용할 경우 다음 루프는 페이로드 전송 시간 또는 잠재적인 경로 정체 효과를 고려하지 않고 최대 8초가 걸립니다.

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

애플리케이션이 바로 연속해서 10개의 비동기 송신 작업을 시작하고 각각이 별도로 완료될 때까지 기다리는 경우 해당 10개 송신 작업에 대한 왕복 시간이 겹칩니다. 10개 메시지가 바로 연속해서 전송되고 심지어 TCP 프레임을 공유할 수도 있으며, 전반적인 전송 기간은 대체적으로 브로커에 메시지를 전송하는 데 걸리는 네트워크 관련 시간에 따라 좌우됩니다.

이전 루프와 동일하게 가정할 경우 다음 루프에 대해 겹치는 총 실행 시간은 1초 미만으로 유지될 수 있습니다.

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

모든 비동기 프로그래밍 모델이 보류 중인 작업을 보유하는 특정 형태의 메모리 기반의 숨겨진 작업 큐를 사용한다는 점에 유의해야 합니다. [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_)(C#) 또는 **Send**(Java)가 반환되면 송신 작업은 해당 작업 큐에 대기되지만 프로토콜 제스처는 해당 작업이 실행될 차례가 되어야만 시작됩니다. 대량의 메시지를 푸시하는 경향이 있고 안정성이 중요하게 고려되는 코드의 경우 송신된 모든 메시지가 실제로 전송될 때까지 메모리를 차지하므로 너무 많은 메시지가 한꺼번에 "처리 중" 상태가 되지 않도록 주의해야 합니다.

C#으로 작성된 다음 코드 조각에 표시되는 세마포는 필요할 때 이러한 애플리케이션 수준 제한을 사용하도록 설정하는 동기화 개체입니다. 이러한 세마포를 사용하면 최대 10개의 메시지를 한 번에 처리할 수 있습니다. 10개의 사용 가능한 세마포 잠금 중 하나는 송신 전에 적용되고 송신이 완료되면 해제됩니다. 11번째 루프 통과는 이전 송신 중 하나 이상이 완료될 때까지 대기한 후 잠금을 사용 가능하게 만듭니다.

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

애플리케이션은 작업 결과를 검색하지 않으면서 "fire-and-forget"(실행 후 망각) 방식으로 비동기 송신 작업을 시작하지 **않아야 합니다**. 이렇게 하면 메모리가 고갈될 때까지 내부 및 보이지 않는 작업 큐가 로드될 수 있으며 애플리케이션은 송신 오류를 검색하지 못할 수 있습니다.

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

하위 수준 AMQP 클라이언트를 사용할 경우에도 Service Bus는 "사전 확인된" 전송을 허용합니다. 사전 확인된 전송은 어떤 식으로든 결과가 클라이언트로 다시 보고되지 않고 메시지가 송신 시 확인된 것으로 간주되는 fire-and-forget(실행 후 망각) 작업입니다. 클라이언트에 대한 피드백이 부족하다는 것은 진단을 위해 사용할 수 있는 실행 가능 데이터가 없으며, 이 모드가 Azure 지원을 통해 도움을 제공하는 데 적합하지 않다는 것을 의미합니다.

## <a name="settling-receive-operations"></a>수신 작업 확인

수신 작업의 경우 Service Bus API 클라이언트에서는 두 가지 명시적 모드인 *수신 및 삭제* 및 *보기-잠금*을 사용할 수 있습니다.

### <a name="receiveanddelete"></a>ReceiveAndDelete

[수신 및 삭제](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드는 수신 클라이언트로 송신하는 모든 메시지를 송신 시 확인된 상태로 간주하도록 브로커에 지시합니다. 즉, 메시지는 브로커가 송신하는 즉시 사용된 것으로 간주됩니다. 메시지 전송이 실패할 경우 메시지는 손실됩니다.

이 모드의 장점은 수신기가 메시지에 대해 추가 작업을 수행할 필요가 없으며 확인 결과를 기다리느라 지연되지도 않는다는 것입니다. 개별 메시지에 포함된 데이터에 값이 부족하고 매우 짧은 기간 동안만 의미가 있는 경우 이 모드를 사용하는 것이 좋습니다.

### <a name="peeklock"></a>PeekLock

[보기-잠금](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드는 수신 클라이언트가 수신된 메시지를 명시적으로 확인하기 원한다는 것을 브로커에 알려줍니다. 메시지가 서비스에서 배타적 잠금 상태에 있는 동안 메시지를 수신기가 처리할 수 있지만 경쟁하는 다른 수신기는 해당 메시지를 볼 수 없습니다. 잠금 기간은 처음에 큐 또는 구독 수준에 정의되며, 잠금을 소유하는 클라이언트가 [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) 작업을 통해 확장할 수 있습니다.

메시지가 잠겨 있으면 동일한 큐 또는 구독에서 수신하는 다른 클라이언트가 잠금을 해제하고, 활성 잠금 상태가 아닌 사용 가능한 다음 메시지를 검색합니다. 메시지에 대한 잠금이 명시적으로 해제되거나 잠금이 만료될 때 메시지는 재배달을 위해 검색 순서 맨 위 또는 위쪽에 다시 팝업됩니다.

수신기가 메시지를 반복적으로 해제하거나 정의된 시간 동안 잠금이 경과되도록 허용하면([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)) 메시지는 큐 또는 구독에서 자동으로 제거되고 연결된 배달 못 한 메시지 큐에 추가됩니다.

수신 클라이언트는 API 수준에서 [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_)를 호출할 때 긍정 승인으로 수신된 메시지의 확인을 시작합니다. 이를 통해 브로커는 메시지가 성공적으로 처리되었음을 알 수 있으며, 메시지는 큐 또는 구독에서 제거됩니다. 브로커는 확인을 수행할 수 있는지 여부를 나타내는 응답으로 받는 사람의 확인 의도에 회신합니다.

수신 클라이언트가 메시지를 처리하지 못하지만 메시지를 다시 배달하려고 할 경우 [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon)을 호출하여 메시지를 해제하고 즉시 잠금을 해제할 것을 명시적으로 요청하거나, 아무 작업도 수행하지 않고 잠금 기한이 경과되도록 할 수 있습니다.

수신 클라이언트가 메시지를 처리하지 못하고 메시지를 다시 배달하고 작업을 다시 시도해도 도움이 되지 않는다는 사실을 확인하면 메시지를 거부할 수 있으며 이 경우 [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter)가 호출되면서 메시지는 배달 못 한 메시지 큐로 이동됩니다. 또한  배달 못 한 메시지 큐에서 메시지와 함께 검색할 수 있는 이유 코드를 포함하는 사용자 지정 속성을 설정할 수 있습니다.

확인의 특별한 경우는 바로 연기로, 별도 문서에서 논의됩니다.

**Complete** 또는 **Deadletter** 작업은 물론 **RenewLock** 작업은 네트워크 문제가 있는 경우, 보류 중인 잠금이 만료된 경우 또는 확인을 방해하는 기타 서비스 쪽 상태가 발생한 경우에 실패할 수 있습니다. 후자의 경우 중 하나에서 서비스는 API 클라이언트에서 예외로 인식되는 부정 승인을 전송합니다. 끊어진 네트워크 연결이 문제인 경우 Service Bus는 다른 연결의 기존 AMQP 링크 복구를 지원하지 않으므로 잠금이 삭제됩니다.

일반적으로 메시지 처리 맨 끝에서, 경우에 따라 몇 분 동안의 처리 작업 후에 **Complete**가 실패하면 수신 애플리케이션은 작업 상태를 보존하고 한 번 더 배달될 경우 동일한 메시지를 무시할지 여부 또는 작업 결과를 삭제하고 메시지가 다시 배달될 때 다시 시도할지 여부를 결정할 수 있습니다.

중복 메시지 배달을 식별하기 위한 일반적인 메커니즘은 보낸 사람이 원본 프로세스의 식별자에 맞게 고유한 값으로 설정할 수 있고 설정해야 하는 메시지 ID를 확인하는 것입니다. 작업 스케줄러는 지정된 작업자를 사용하여 메시지 ID를 작업자에게 할당하려는 작업의 식별자로 설정하고, 작업자는 해당 작업이 이미 수행된 경우 해당 작업 할당이 두 번째로 나오는 경우를 무시합니다.

> [!IMPORTANT]
> 메시지에서 PeekLock 획득 하는 잠금은 휘발성 이며 다음 조건에서 손실 될 수 있다는 점에 유의 해야 합니다.
>   * 서비스 업데이트
>   * OS 업데이트
>   * 잠금을 보유 하는 동안 엔터티 (큐, 토픽, 구독)의 속성을 변경 합니다.
>
> 잠금이 손실 되 면 Azure Service Bus에서 클라이언트 응용 프로그램 코드에 표시 되는 LockLostException을 생성 합니다. 이 경우 클라이언트의 기본 재시도 논리가 자동으로 시작 되 고 작업을 다시 시도 합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
