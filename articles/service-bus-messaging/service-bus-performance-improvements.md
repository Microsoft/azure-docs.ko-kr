---
title: Azure Service Bus를 사용 하 여 성능 향상을 위한 모범 사례
description: broker 저장 메시지를 교환할 때 Azure Service Bus를 사용하여 성능을 최적화하는 방법에 대해 설명합니다.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2bd5a1598448722f46a91b889b0778e80ad4e140
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89012061"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Service Bus 메시징을 사용한 성능 향상의 모범 사례

이 문서에서는 조정된 메시지를 교환할 때 Azure Service Bus를 사용하여 성능을 최적화하는 방법에 대해 설명합니다. 이 문서의 첫 번째 부분에서는 성능 향상을 위해 제공되는 다양한 메커니즘에 대해 설명합니다. 두 번째 부분은 특정 시나리오에서 최고의 성능을 제공하는 방식으로 Service Bus를 사용하는 방법에 대해 안내합니다.

이 문서 전반적으로 “클라이언트”라는 용어는 Service Bus에 액세스하는 모든 엔터티를 가리킵니다. 클라이언트는 발신기 또는 수신기의 역할을 수행할 수 있습니다. "발신기"라는 용어는 메시지를 Service Bus 큐 또는 토픽 구독에 보내는 Service Bus 큐 또는 토픽 클라이언트에 사용됩니다. "수신기"라는 용어는 Service Bus 큐 또는 구독에서 메시지를 수신하는 Service Bus 큐 또는 구독 클라이언트를 가리킵니다.

다음 섹션에서는 Service Bus가 성능 향상을 위해 사용하는 몇 가지 개념에 대해 소개합니다.

## <a name="protocols"></a>프로토콜

Service Bus를 사용하면 클라이언트에서 세 가지 프로토콜 중 하나를 통해 메시지를 보내고 받을 수 있습니다.

1. AMQP(고급 메시지 큐 프로토콜)
2. SBMP(Service Bus 메시징 프로토콜)
3. HTTP(Hypertext Transfer Protocol)

AMQP는 Service Bus에 대 한 연결을 유지 하기 때문에 가장 효율적입니다. 또한 일괄 처리와 프리페치도 구현합니다. 명시적으로 언급하지 않는 한 이 문서의 모든 내용에서는 AMQP 또는 SBMP를 사용하는 것으로 가정합니다.

> [!IMPORTANT]
> SBMP는 .NET Framework에만 사용할 수 있습니다. AMQP는 .NET Standard에 대 한 기본값입니다.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>적절 한 Service Bus .NET SDK 선택

지원 되는 두 가지 Azure Service Bus .NET Sdk가 있습니다. Api는 매우 비슷하며 선택할 수 있는 것과 혼동 될 수 있습니다. 결정을 내리는 데 도움이 필요 하면 다음 표를 참조 하세요. ServiceBus SDK는 더 현대적이 고 성능이 뛰어나고 플랫폼 간 호환이 가능 하므로 권장 됩니다. 또한 Websocket을 통한 AMQP를 지원 하 고 오픈 소스 프로젝트의 Azure .NET SDK 컬렉션의 일부입니다.

| NuGet 패키지 | 기본 네임 스페이스 | 최소 플랫폼 | 프로토콜 |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">ServiceBus <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>유니버설 Windows 플랫폼 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">Windowsazure.servicebus. ServiceBus <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

최소 .NET Standard 플랫폼 지원에 대 한 자세한 내용은 [.net 구현 지원](/dotnet/standard/net-standard#net-implementation-support)을 참조 하세요.

## <a name="reusing-factories-and-clients"></a>팩터리 및 클라이언트 다시 사용

# <a name="microsoftazureservicebus-sdk"></a>[ServiceBus SDK](#tab/net-standard-sdk)

또는의 구현과 같은 Service Bus 클라이언트 개체는 [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender] 종속성 주입을 위해 단일 항목 (또는 한 번 인스턴스화하고 공유 됨)로 등록 되어야 합니다. 메시지를 보낸 후, 메시징 팩터리 또는 큐, 토픽, 구독 클라이언트를 닫은 다음, 다음 메시지를 보낼 때 이러한 메시징 팩터리 또는 큐, 토픽, 구독 클라이언트를 다시 만들지 않는 것이 좋습니다. 메시징 팩터리를 닫을 경우 Service Bus 서비스에 대한 연결이 삭제되고 팩터리를 다시 만들면 새 연결이 구축됩니다. 여러 작업에 대해 동일한 팩터리와 클라이언트 개체를 다시 사용하면 많은 비용이 드는 연결 작업을 하지 않아도 됩니다. 동시 비동기 작업에 대해 다중 스레드에서 이러한 클라이언트 개체를 안전하게 사용할 수 있습니다.

# <a name="windowsazureservicebus-sdk"></a>[ServiceBus SDK Windowsazure.servicebus](#tab/net-framework-sdk)

또는와 같은 Service Bus 클라이언트 개체 `QueueClient` 는 `MessageSender` 연결에 대 한 내부 관리도 제공 하는 [messagingfactory][MessagingFactory] 개체를 통해 생성 됩니다. 메시지를 보낸 후, 메시징 팩터리 또는 큐, 토픽, 구독 클라이언트를 닫은 다음, 다음 메시지를 보낼 때 이러한 메시징 팩터리 또는 큐, 토픽, 구독 클라이언트를 다시 만들지 않는 것이 좋습니다. 메시징 팩터리를 닫을 경우 Service Bus 서비스에 대한 연결이 삭제되고 팩터리를 다시 만들면 새 연결이 구축됩니다. 여러 작업에 대해 동일한 팩터리와 클라이언트 개체를 다시 사용하면 많은 비용이 드는 연결 작업을 하지 않아도 됩니다. 동시 비동기 작업에 대해 다중 스레드에서 이러한 클라이언트 개체를 안전하게 사용할 수 있습니다.

---

## <a name="concurrent-operations"></a>동시 작업

작업(보내기, 받기, 삭제 등) 수행은 다소 시간이 걸립니다. 이 시간에는 요청 및 응답의 대기 시간 외에도 Service Bus 서비스에서 작업을 처리 하는 작업이 포함 됩니다. 시간당 작업 수를 늘리려면 작업이 동시에 실행되어야 합니다.

클라이언트가 비동기 작업을 수행하여 동시 작업을 예약합니다. 이전 요청이 완료되기 전에 다음 요청이 시작됩니다. 다음 코드 조각은 비동기 전송 작업의 예제입니다.

# <a name="microsoftazureservicebus-sdk"></a>[ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[ServiceBus SDK Windowsazure.servicebus](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

다음 코드는 비동기 수신 작업의 예제입니다.

# <a name="microsoftazureservicebus-sdk"></a>[ServiceBus SDK](#tab/net-standard-sdk)

전체 <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">소스 코드 예제 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>는 GitHub 리포지토리를 참조 하세요.

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

`MessageReceiver`개체는 연결 문자열, 큐 이름 및 피킹 (peeking) 수신 모드를 사용 하 여 인스턴스화됩니다. 그런 다음 `receiver` 인스턴스를 사용 하 여 메시지 처리기를 등록 합니다.

# <a name="windowsazureservicebus-sdk"></a>[ServiceBus SDK Windowsazure.servicebus](#tab/net-framework-sdk)

전체 <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">소스 코드 예제 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>는 GitHub 리포지토리를 참조 하세요.

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

는 `MessagingFactory` `factory` 연결 문자열에서 개체를 만듭니다. 인스턴스를 사용 하 여 `factory` `MessageReceiver` 가 인스턴스화됩니다. 그런 다음 `receiver` 인스턴스를 사용 하 여 메시지 처리기를 등록 합니다.

---

## <a name="receive-mode"></a>수신 모드

큐 또는 구독 클라이언트를 만들 때 *보기-잠금* 또는 *수신 및 삭제* 중에서 수신 모드를 지정할 수 있습니다. 기본 수신 모드는 `PeekLock`입니다. 기본 모드에서 작동할 때 클라이언트는 Service Bus에서 메시지를 수신 하는 요청을 보냅니다. 클라이언트가 메시지를 받으면 메시지를 완료하기 위한 요청을 보냅니다.

수신 모드를 `ReceiveAndDelete`로 설정하면 이 두 단계가 단일 요청으로 결합되므로 그러면 전체 작업 수가 줄어들고 전체 메시지 처리량을 향상시킬 수 있습니다. 이와 같이 성능이 향상되지만 메시지 손실의 위험이 있습니다.

Service Bus는 수신 및 삭제 작업에 대한 트랜잭션을 지원하지 않습니다. 또한 클라이언트가 메시지를 연기하거나 [배달 못 한](service-bus-dead-letter-queues.md) 시나리오에서는 보기-잠금 의미 체계가 필요합니다.

## <a name="client-side-batching"></a>클라이언트 쪽 일괄 처리

클라이언트 쪽 일괄 처리에서는 큐 또는 토픽 클라이언트가 일정 시간 동안 메시지 전송을 연기할 수 있습니다. 클라이언트가 이 기간 동안 추가 메시지를 보내면 메시지를 단일 배치로 전송합니다. 또한 클라이언트 쪽 일괄 처리에서는 또한 큐 또는 구독 클라이언트가 여러 **완료** 요청을 단일 요청으로 일괄 처리합니다. 일괄 처리는 비동기 **Send** 및 **Complete** 작업에만 사용할 수 있습니다. 동기 작업은 Service Bus 서비스에 즉시 보내집니다. 일괄 처리는 보기 또는 수신 작업에 대해 발생하지 않으며 클라이언트 전반에서도 발생하지 않습니다.

# <a name="microsoftazureservicebus-sdk"></a>[ServiceBus SDK](#tab/net-standard-sdk)

.NET Standard SDK에 대 한 일괄 처리 기능은 아직 조작할 속성을 노출 하지 않습니다.

# <a name="windowsazureservicebus-sdk"></a>[ServiceBus SDK Windowsazure.servicebus](#tab/net-framework-sdk)

기본적으로 클라이언트는 20ms의 배치 간격을 사용합니다. 메시지 팩터리를 만들기 전에 [BatchFlushInterval][BatchFlushInterval] 속성을 설정하여 배치 간격을 변경할 수 있습니다. 이 설정은이 이 팩터리에서 만든 모든 클라이언트에 영향을 줍니다.

일괄 처리를 사용하지 않도록 설정하려면 [BatchFlushInterval][BatchFlushInterval] 속성을 **TimeSpan.Zero**로 설정합니다. 예를 들어:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

일괄 처리는 청구 가능 메시징 작업의 수에 영향을 주지 않으며 [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)을 사용하여 Service Bus 클라이언트 프로토콜에 대해서만 사용할 수 있습니다. HTTP 프로토콜은 일괄 처리를 지원하지 않습니다.

> [!NOTE]
> 를 설정 하면 `BatchFlushInterval` 일괄 처리가 응용 프로그램의 관점에서 암시적으로 수행 됩니다. 예: 응용 프로그램에서 `SendAsync` 및를 `CompleteAsync` 호출 하 고 특정 일괄 처리를 수행 하지 않습니다.
>
> 다음 메서드 호출을 활용 하 여 명시적 클라이언트 쪽 일괄 처리를 구현할 수 있습니다.
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> 여기서 메시지의 결합 된 크기는 가격 책정 계층에서 지 원하는 최대 크기 보다 작아야 합니다.

---

## <a name="batching-store-access"></a>저장소 액세스 일괄 처리

큐, 토픽 또는 구독의 처리량을 높이기 위해 Service Bus는 내부 저장소에 쓸 때 여러 메시지를 일괄 처리합니다. 큐 또는 토픽에 설정된 경우 저장소에 메시지를 쓰는 작업이 일괄 처리됩니다. 큐 또는 구독에 설정된 경우 저장소에서 메시지를 삭제하는 작업이 일괄 처리됩니다. 엔터티에 대해 일괄 처리된 저장소 액세스를 사용할 경우 Service Bus는 해당 엔터티와 관련된 저장소 쓰기 작업을 최대 20ms까지 지연합니다.

> [!NOTE]
> 20ms 일괄 처리 간격이 끝날 때 Service Bus 오류가 있더라도 메시지가 손실될 위험이 없습니다.

이 간격 동안 발생하는 추가 저장소 작업은 배치에 추가됩니다. 일괄 처리 방식 저장소 액세스는 **Send** 및 **Complete** 작업에만 적용되며 수신 작업에는 적용되지 않습니다. 일괄 처리된 저장소 액세스는 엔터티의 속성입니다. 일괄 처리는 일괄 처리된 저장소 액세스가 가능한 모든 엔터티에서 발생합니다.

새 큐, 토픽 또는 구독을 만들면 기본적으로 일괄 처리된 저장소 액세스가 사용하도록 설정됩니다.

# <a name="microsoftazureservicebus-sdk"></a>[ServiceBus SDK](#tab/net-standard-sdk)

일괄 처리 된 저장소 액세스를 사용 하지 않도록 설정 하려면 인스턴스가 필요 `ManagementClient` 합니다. 속성을로 설정 하는 큐 설명에서 큐를 만듭니다 `EnableBatchedOperations` `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

자세한 내용은
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[ServiceBus SDK Windowsazure.servicebus](#tab/net-framework-sdk)

일괄 처리 된 저장소 액세스를 사용 하지 않도록 설정 하려면 인스턴스가 필요 `NamespaceManager` 합니다. 속성을로 설정 하는 큐 설명에서 큐를 만듭니다 `EnableBatchedOperations` `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

자세한 내용은
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

일괄 처리된 저장소 액세스는 청구 가능한 메시징 작업의 수에 영향을 주지 않으며 큐, 토픽 또는 구독의 속성입니다. 또한 수신 모드와 독립적이며 클라이언트와 Service Bus 서비스 간 사용되는 프로토콜과도 무관합니다.

## <a name="prefetching"></a>프리페치

[프리페치](service-bus-prefetch.md) 를 통해 큐 또는 구독 클라이언트는 수신 작업을 수행할 때 서비스에서 추가 메시지를 로드할 수 있습니다. 클라이언트는 이러한 메시지를 로컬 캐시에 저장합니다. 캐시 크기는 또는 속성에 의해 결정 됩니다 `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` . 프리페치를 사용할 수 있는 각 클라이언트는 각각의 캐시를 유지합니다. 캐시는 클라이언트 사이에서 공유되지 않습니다. 클라이언트가 수신 작업을 시작할 때 캐시가 비어 있으면 서비스에서 메시지의 배치를 전송합니다. 배치의 크기는 캐시의 크기와 256KB 중 더 작은 값입니다. 클라이언트가 수신 작업을 시작할 때 캐시에 메시지가 포함되어 있으면 캐시에서 해당 메시지를 가져옵니다.

메시지가 프리페치되는 경우 서비스는 프리페치된 메시지를 잠급니다. 잠금을 사용하면 프리페치된 메시지를 다른 수신기가 받을 수 없습니다. 잠금이 만료되기 전에 수신기가 메시지를 완료할 수 없는 경우 다른 수신기가 메시지를 사용할 수 있습니다. 프리페치된 메시지의 복사본은 캐시에 남아 있습니다. 만료 및 캐시된 복사본을 사용하는 수신기가 해당 메시지를 완료하려고 하면 예외를 수신합니다. 기본적으로 메시지 잠금은 60초 후에 만료됩니다. 이 값은 5분으로 연장할 수 있습니다. 만료된 메시지를 사용하지 못하도록 하려면 캐시 크기가 언제나 잠금 시간 초과 간격 이내에 클라이언트가 사용할 수 있는 메시지 수보다 작아야 합니다.

기본 잠금 만료 시간으로 60초를 사용하는 경우 적절한 `PrefetchCount` 값은 모든 팩터리 수신자의 최대 처리 속도x20입니다. 예를 들어 팩터리에서 수신기 3개를 만들 경우 각 수신기는 초당 최대 10개의 메시지를 처리할 수 있습니다. 프리페치 수가 20 X 3 X 10 = 600을 초과해서는 안됩니다. 기본적으로 `PrefetchCount` 는 0으로 설정 됩니다. 즉, 서비스에서 추가 메시지가 인출 되지 않습니다.

메시지를 프리페치할 경우 전체 메시지 작업 수 또는 왕복 수가 감소하므로 큐 또는 구독에 대한 전체 처리량이 증가합니다. 그러나 첫 번째 메시지를 페치하는 작업은 증가된 메시지 크기로 인해 더 오래 걸립니다. 프리페치된 메시지는 클라이언트에서 이미 다운로드한 메시지이므로 더 빠르게 수신할 수 있습니다.

메시지의 TTL(Time to Live) 속성은 서버가 클라이언트에 메시지를 보낼 때 서버에 의해 확인됩니다. 클라이언트는 메시지를 받을 때 메시지의 TTL 속성을 확인 하지 않습니다. 대신 클라이언트가 메시지를 캐시 하는 동안 메시지의 TTL이 전달 된 경우에도 메시지를 수신할 수 있습니다.

프리페치는 청구 가능 메시징 작업의 수에 영향을 주지 않으며 Service Bus 클라이언트 프로토콜에 대해서만 사용할 수 있습니다. HTTP 프로토콜은 프리페치를 지원하지 않습니다. 프리페치는 동기 및 비동기 수신 작업에 사용할 수 있습니다.

# <a name="microsoftazureservicebus-sdk"></a>[ServiceBus SDK](#tab/net-standard-sdk)

자세한 내용은 다음 속성을 참조 하세요 `PrefetchCount` .

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[ServiceBus SDK Windowsazure.servicebus](#tab/net-framework-sdk)

자세한 내용은 다음 속성을 참조 하세요 `PrefetchCount` .

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>프리페치 및 ReceiveBatch

> [!NOTE]
> Windowsazure.servicebus SDK는 배치 함수를 노출 하지 않으므로이 섹션은 ServiceBus SDK에만 적용 됩니다.

여러 메시지를 함께 프리페치 하는 개념은 일괄 처리 ()에서 메시지를 처리 하는 것과 비슷한 의미를 가지 지만 이러한 기능을 `ReceiveBatch` 함께 사용할 때는 몇 가지 사소한 차이점이 있습니다.

프리페치는 클라이언트 (및)의 구성 (또는 모드) `QueueClient` 이 `SubscriptionClient` 고 `ReceiveBatch` 작업은 요청-응답 의미 체계가 있습니다.

이러한 기능을 함께 사용 하는 동안 다음과 같은 경우를 고려 합니다.

* 프리페치는 수신 해야 하는 메시지 수보다 크거나 같아야 합니다 `ReceiveBatch` .
* 프리페치는 초당 처리 된 메시지 수의 최대 n/3 배까지 가능 합니다. 여기서 n은 기본 잠금 기간입니다.

Greedy 접근 방식 (예: 프리페치 수를 매우 높음)으로 설정 하면 메시지가 특정 받는 사람에 게 잠겨 있기 때문에 몇 가지 문제가 있습니다. 위에서 언급 한 임계값과 알려지고 실험적으로가 일치 하는 항목을 식별 하기 위해 프리페치 값을 사용해 보는 것이 좋습니다.

## <a name="multiple-queues"></a>여러 큐

단일 큐 또는 토픽에서 예상 되는 부하를 처리할 수 없는 경우 여러 메시징 엔터티를 사용 해야 합니다. 여러 엔터티를 사용할 때는 모든 엔터티에 동일한 클라이언트를 사용하는 대신 각 엔터티의 전용 클라이언트를 만듭니다.

## <a name="development-and-testing-features"></a>개발 및 테스트 기능

> [!NOTE]
> 이 섹션은 ServiceBus SDK에만 적용 됩니다. ServiceBus SDK는이 기능을 노출 하지 Windowsazure.servicebus.

Service Bus에는 **프로덕션 구성에서 사용해 서는 안**되는, 개발 전용으로 사용 되는 기능이 하나 [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] 있습니다.

항목에 새 규칙이 나 필터를 추가 하는 경우를 사용 [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] 하 여 새 필터 식이 예상 대로 작동 하는지 확인할 수 있습니다.

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

### <a name="multiple-high-throughput-queues"></a>처리량이 높은 복수 큐

목표: 여러 큐의 전체 처리량을 극대화합니다. 개별 큐의 처리량은 보통 또는 높음입니다.

여러 큐에서 최대 처리량을 얻으려면 여기에서 설명하는 설정을 사용하여 단일 큐의 처리량을 최대화합니다. 또한 다른 팩터리를 사용하여 다양한 큐에서 보내거나 받는 클라이언트를 만듭니다.

### <a name="low-latency-queue"></a>대기 시간이 짧은 큐

목표: 큐 또는 토픽의 엔드투엔드 대기 시간을 최소화합니다. 발신기와 수신기의 수가 작습니다. 큐의 처리량은 적음 또는 보통입니다.

* 클라이언트 쪽 일괄 처리를 사용하지 않도록 설정합니다. 클라이언트에서 즉시 메시지를 보냅니다.
* 일괄 처리된 저장소 액세스를 사용하지 않도록 설정합니다. 서비스가 저장소에 즉시 메시지를 씁니다.
* 단일 클라이언트를 사용하는 경우 프리페치 수를 수신기의 처리 속도의 20배로 설정합니다. 동시에 여러 메시지가 큐에 도착할 경우 Service Bus 클라이언트 프로토콜은 이러한 메시지를 동시에 전송합니다. 클라이언트가 다음 메시지를 수신하면 해당 메시지는 이미 로컬 캐시에 있는 것입니다. 캐시는 작아야 합니다.
* 여러 클라이언트를 사용하는 경우 프리페치 수를 0으로 설정합니다. 이렇게 하면 첫 번째 클라이언트가 아직 첫 번째 메시지를 처리하는 동안 두 번째 클라이언트가 두 번째 메시지를 수신할 수 있습니다.

### <a name="queue-with-a-large-number-of-senders"></a>발신기 수가 많은 큐

목표: 발신기 수가 많은 큐 또는 토픽의 처리량 극대화 각 발신기는 보통 속도의 메시지를 전송합니다. 수신기의 수가 작습니다.

Service Bus는 메시징 엔터티에 대해 최대 1000의 동시 연결을 사용 하도록 설정 합니다. 이 제한은 네임스페이스 수준에서 적용되며, 큐/토픽/구독은 네임스페이스당 동시 연결 수로 제한됩니다. 큐의 경우 이 숫자는 발신기와 수신기 사이에 공유됩니다. 발신기에 1,000개 연결이 모두 필요할 경우 큐를 토픽 및 단일 구독으로 대체합니다. 토픽은 발신기로부터 최대 1,000개의 동시 연결을 지원하는 반면, 구독은 수신기로부터 추가 1,000개의 동시 연결을 지원합니다. 1,000개보다 많은 동시 발신기가 필요할 경우 발신기는 HTTP를 통해 Service Bus 프로토콜에 메시지를 전송해야 합니다.

처리량을 최대화하려면 다음 단계를 수행합니다.

* 각 발신기가 다른 프로세스에 상주하는 경우 프로세스당 하나의 팩터리만 사용합니다.
* 클라이언트 쪽 일괄 처리를 활용하려면 비동기 작업을 사용합니다.
* Service Bus 클라이언트 프로토콜 전송 수를 줄이려면 기본 일괄 처리 간격인 20ms를 사용합니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이 액세스로 인해 메시지가 큐 또는 토픽에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 모든 팩터리 수신기의 최대 처리 속도의 20배로 설정합니다. 이렇게 하면 Service Bus 클라이언트 프로토콜 전송 횟수가 줄어듭니다.

### <a name="queue-with-a-large-number-of-receivers"></a>수신기 수가 많은 큐

목표: 수신기 수가 많은 큐 또는 구독의 수신 속도를 극대화합니다. 각 수신자는 보통 속도로 메시지를 받습니다. 발신기의 수가 작습니다.

Service Bus는 엔터티에 대해 최대 1,000개의 동시 연결을 지원합니다. 큐에 1,000개보다 많은 수신기가 필요할 경우 큐를 토픽 및 여러 구독으로 대체합니다. 각 구독은 최대 1,000개의 동시 연결을 지원할 수 있습니다. 또는 수신자가 HTTP 프로토콜을 통해 큐에 액세스할 수 있습니다.

처리량을 최대화하려면 다음을 수행합니다.

* 각 수신기가 다른 프로세스에 상주하는 경우 프로세스당 하나의 팩터리만 사용합니다.
* 수신기는 동기 또는 비동기 작업을 사용할 수 있습니다. 개별 수신기의 수신 속도가 보통일 경우 완료 요청의 클라이언트 쪽 일괄 처리는 수신기 처리량에 영향을 주지 않습니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이렇게 하면 엔터티의 전체 부하가 줄어듭니다. 또한 메시지가 큐 또는 토픽에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 작은 값으로 설정합니다(예: PrefetchCount = 10). 이렇게 하면 발신기에 많은 수의 메시지가 캐시된 동안 수신기가 유휴 상태가 되지 않습니다.

### <a name="topic-with-a-small-number-of-subscriptions"></a>구독 수가 적은 토픽

목표: 구독 수가 적은 토픽의 처리량을 최대화합니다. 여러 구독에서 메시지를 수신합니다. 즉, 모든 구독의 수신 속도를 결합한 속도가 전송 속도보다 큼을 의미합니다. 발신기의 수가 작습니다. 구독당 수신기의 수가 작습니다.

처리량을 최대화하려면 다음을 수행합니다.

* 토픽에 대한 전반적 전송 속도를 높이려면 여러 메시지 팩터리를 만들어 발신기를 만듭니다. 각 발신기에 대해 비동기 작업이나 여러 스레드를 사용합니다.
* 구독에서 전반적 수신 속도를 높이려면 여러 메시지 팩터리를 만들어 수신기를 만듭니다. 각 수신기에 대해 비동기 작업이나 여러 스레드를 사용합니다.
* 클라이언트 쪽 일괄 처리를 활용하려면 비동기 작업을 사용합니다.
* Service Bus 클라이언트 프로토콜 전송 수를 줄이려면 기본 일괄 처리 간격인 20ms를 사용합니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이 액세스로 인해 메시지가 토픽에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 모든 팩터리 수신기의 최대 처리 속도의 20배로 설정합니다. 이렇게 하면 Service Bus 클라이언트 프로토콜 전송 횟수가 줄어듭니다.

### <a name="topic-with-a-large-number-of-subscriptions"></a>구독 수가 많은 토픽

목표: 구독 수가 많은 토픽의 처리량을 최대화합니다. 여러 구독에서 메시지를 수신합니다. 즉, 모든 구독의 수신 속도를 결합한 속도가 전송 속도보다 훨씬 큼을 의미합니다. 발신기의 수가 작습니다. 구독당 수신기의 수가 작습니다.

모든 메시지가 모든 구독으로 라우팅될 경우 구독 수가 많은 토픽은 일반적으로 전반적 처리량이 감소합니다. 낮은 처리량은 각 메시지가 여러 번 수신되며 토픽에 포함된 모든 메시지와 해당 구독이 동일한 저장소에 저장되기 때문입니다. 구독당 발신기 수와 수신기 수가 작은 경우를 가정합니다. Service Bus는 토픽당 최대 2,000개의 구독을 지원합니다.

처리량을 최대화하려면 다음 단계를 시도해 보십시오.

* 클라이언트 쪽 일괄 처리를 활용하려면 비동기 작업을 사용합니다.
* Service Bus 클라이언트 프로토콜 전송 수를 줄이려면 기본 일괄 처리 간격인 20ms를 사용합니다.
* 일괄 처리된 저장소 액세스는 사용하도록 설정한 상태로 둡니다. 이 액세스로 인해 메시지가 토픽에 기록되는 전반적 속도가 향상됩니다.
* 프리페치 수를 초당 예상 수신 속도의 20배로 설정합니다. 이렇게 하면 Service Bus 클라이언트 프로토콜 전송 횟수가 줄어듭니다.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md