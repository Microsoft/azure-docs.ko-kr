---
title: Azure Event Hubs .NET Standard API 개요 | Microsoft Docs
description: .NET Standard API 개요
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821899"
---
# <a name="event-hubs-net-standard-api-overview"></a>Event Hubs .NET Standard API 개요

이 문서는 핵심 Azure Event Hubs[.NET Standard 클라이언트 API](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 일부를 요약해서 설명합니다. 현재 다음과 같은 Event Hubs에 대한 두 개의 .NET Standard 클라이언트 라이브러리가 있습니다.

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): 모든 기본 런타임 작업을 제공합니다.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): 처리된 이벤트를 추적할 수 있는 추가 기능을 추가하며, 이벤트 허브에서 읽을 수 있는 가장 쉬운 방법입니다.

## <a name="event-hubs-client"></a>Event Hubs 클라이언트

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)는 이벤트를 전송하고, 수신기를 만들고, 런타임 정보를 얻는 데 사용하는 기본 개체입니다. 이 클라이언트는 특정 이벤트 허브에 연결되며 Event Hubs 엔드포인트에 대한 새 연결을 만듭니다.

### <a name="create-an-event-hubs-client"></a>Event Hubs 클라이언트 만들기

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) 개체는 연결 문자열에서 만들어집니다. 새 클라이언트를 인스턴스화하는 가장 간단한 방법은 다음 예제에 나와 있습니다.

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

프로그래밍 방식으로 연결 문자열을 편집하려면 [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) 클래스를 사용하고 연결 문자열을 [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)에 대한 매개 변수로 전달할 수 있습니다.

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>이벤트 보내기

이벤트 허브에 이벤트를 전송하려면 [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) 클래스를 사용합니다. 본문은 `byte` 배열 또는 `byte` 배열 세그먼트여야 합니다.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>이벤트 수신

Event Hubs에서 이벤트를 수신하는 권장 방법은 이벤트 허브 오프셋을 자동으로 추적하는 기능과 파티션 정보를 제공하는 [Event Processor Host](#event-processor-host-apis)를 사용하는 것입니다. 그러나 핵심 Event Hubs 라이브러리의 유연성을 활용하여 이벤트를 수신할 수 있는 상황도 있습니다.

#### <a name="create-a-receiver"></a>수신기 만들기

수신기는 특정 파티션에 연결되어 있으므로 이벤트 허브에서 모든 이벤트를 수신하려면 여러 인스턴스를 만들어야 합니다. 파티션 ID를 하드 코딩하는 것보다 프로그래밍 방식으로 파티션 정보를 가져오는 것이 좋습니다. 이렇게 하기 위해 [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) 메서드를 사용할 수 있습니다.

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

이벤트는 이벤트 허브에서 절대 제거되지 않으므로(만료되기만 함) 적절한 시작 지점을 지정해야 합니다. 다음 예제에서는 가능한 조합을 보여 줍니다.

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>이벤트 소비

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>이벤트 프로세서 호스트 API

이러한 API는 사용 가능한 작업자에 걸쳐 파티션을 분산하여 사용할 수 없게 되는 작업자 프로세스에 복구 성능을 제공합니다.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

다음은 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 인터페이스의 구현 샘플입니다.

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>다음 단계

Event Hubs 시나리오에 대한 자세한 내용은 다음 링크를 방문하십시오.

* [Azure Event Hubs 정의](event-hubs-what-is-event-hubs.md)
* [사용할 수 있는 Event Hubs API](event-hubs-api-overview.md)

.NET API 참조는 다음과 같습니다.

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
