---
title: 이벤트 프로세서 호스트를 사용하여 이벤트 수신 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 검사점, 임대 및 병렬 읽기 이벤트의 관리를 간소화하는 Azure Event Hubs의 이벤트 프로세서 호스트에 대해 설명합니다.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 414179d62970315a7575be0411bf1cb152349fdc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162296"
---
# <a name="event-processor-host"></a>이벤트 프로세서 호스트
> [!NOTE]
> 이 문서는 Azure Event Hubs SDK의 이전 버전에 적용 됩니다. 최신 버전의 SDK로 코드를 마이그레이션하는 방법에 대 한 자세한 내용은 이러한 마이그레이션 가이드를 참조 하세요. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MIGRATIONGUIDE.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java 스크립트](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> 또한 [응용 프로그램의 여러 인스턴스에 걸쳐 파티션 부하 분산](event-processor-balance-partition-load.md)을 참조 하세요.

Azure Event Hubs는 저렴한 비용으로 수백만 개의 이벤트를 스트리밍하는 데 사용할 수 있는 강력한 원격 분석 수집 서비스입니다. 이 아티클에서는 검사점, 임대 및 병렬 이벤트 판독기의 관리를 간소화하는 지능형 소비자 에이전트인 EPH(*이벤트 프로세서 호스트*)를 사용하여 수집된 이벤트를 사용하는 방법을 설명합니다.  

Event Hubs의 크기를 조정하는 핵심은 분할된 소비자라는 개념입니다. [경쟁하는 소비자](https://msdn.microsoft.com/library/dn568101.aspx) 패턴과 달리 분할된 소비자 패턴을 사용하면 경합 병목 상태를 제거하고 종단 간 병렬 처리를 용이하게 하여 대규모 크기 조정이 가능해집니다.

## <a name="home-security-scenario"></a>홈 보안 시나리오

예제 시나리오로 100,000호의 집을 모니터링하는 보안 회사를 가정합니다. 1분마다 각 집에 설치된 다양한 센서(예: 동작 탐지기, 도어/창 열기 센서, 유리 파손 감지기 등)의 데이터를 가져옵니다. 회사는 거주자가 거의 실시간으로 해당 집의 활동을 모니터링하는 웹 사이트를 제공합니다.

각 센서는 데이터를 이벤트 허브로 푸시합니다. 이벤트 허브는 16개의 파티션으로 구성됩니다. 사용한 후에 이러한 이벤트를 읽고, 통합(필터, 집계 등)하고, 스토리지 Blob에 집계를 덤프할 수 있는 메커니즘이 필요합니다. 그런 다음, 사용자 중심 웹 페이지에 프로젝션합니다.

## <a name="write-the-consumer-application"></a>소비자 애플리케이션 작성

분산된 환경에서 소비자를 디자인할 때 시나리오는 다음 요구 사항을 처리해야 합니다.

1. **크기 조정:** 몇몇 Event Hubs 파티션에서 읽기 소유권을 가진 각 소비자를 사용하여 여러 소비자를 만듭니다.
2. **부하 분산:** 소비자를 동적으로 증가시키거나 감소시킵니다. 예를 들어, 새 센서 형식(예: 일산화 탐지기)이 각 집에 추가되면 이벤트 수가 증가합니다. 이 경우에 연산자(사람)는 소비자 인스턴스 수를 증가시킵니다. 그런 다음, 소비자 풀은 소유한 파티션의 수를 다시 조정하여 새로 추가된 소비자와 관련된 부하를 공유할 수 있습니다.
3. **오류에서 원활한 다시 시작:** 소비자(**소비자 A**)가 실패하면(예: 소비자를 호스트하는 가상 머신이 갑자기 충돌함) 다른 소비자는 **소비자 A**가 소유한 파티션을 선택한 다음, 계속할 수 있어야 합니다. 또한 *검사점* 또는 *오프셋*이라는 연속 지점은 **소비자 A**가 실패한 정확한 지점 또는 약간 앞에 위치해야 합니다.
4. **이벤트 사용:** 이전 세 개의 지점이 소비자의 관리를 처리하는 동안 이벤트를 사용하여 유용한 작업을 수행하는 코드가 있어야 합니다(예: 집계하고 Blob Storage에 업로드).

이에 대해 고유한 솔루션을 구축하는 대신에 Event Hubs는 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 인터페이스와 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 클래스를 통해 이 기능을 제공합니다.

## <a name="ieventprocessor-interface"></a>IEventProcessor 인터페이스

먼저 사용하는 애플리케이션은 [OpenAsync, CloseAsync, ProcessErrorAsync 및 ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)라는 네 가지 메서드가 있는 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods) 인터페이스를 구현합니다. 이 인터페이스에는 Event Hubs가 전송하는 이벤트를 사용하는 실제 코드가 포함됩니다. 다음 코드에서는 구현 예제를 보여줍니다.

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

다음으로, [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 인스턴스를 인스턴스화합니다. 오버로드에 따라 생성자에서 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 인스턴스를 만들 때 다음 매개 변수가 사용됩니다.

- **hostName:** 각 소비자 인스턴스의 이름입니다. **EventProcessorHost** 의 각 인스턴스에는 소비자 그룹 내에서이 변수에 대 한 고유 값이 있어야 하므로이 값을 하드 코딩 하지 마십시오.
- **eventHubPath:** 이벤트 허브의 이름입니다.
- **consumerGroupName:** Event Hubs는 **$Default**를 기본 소비자 그룹의 이름으로 사용하지만 처리의 특정 측면에 대한 소비자 그룹을 만드는 것이 좋습니다.
- **eventHubConnectionString:** Azure Portal에서 검색할 수 있는 이벤트 허브에 대한 연결 문자열입니다. 이 연결 문자열에는 이벤트 허브에 대한 **수신** 권한이 있어야 합니다.
- **storageConnectionString:** 내부 리소스 관리에 사용되는 스토리지 계정입니다.

마지막으로, 소비자는 Event Hubs 서비스를 사용하여 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 인스턴스를 등록합니다. EventProcessorHost 인스턴스에 이벤트 프로세서 클래스를 등록하면 이벤트 처리가 시작됩니다. 등록하면 Event Hubs 서비스에 지시하여 소비자 앱에서 해당 파티션 중 일부의 이벤트를 사용하도록 예상하고 사용할 이벤트를 푸시할 때마다 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 구현 코드를 호출합니다. 


### <a name="example"></a>예제

예를 들어 사용하는 이벤트에 대한 5대의 전용 VM(가상 머신)이 있고 각 VM에 간단한 콘솔 애플리케이션이 있다고 가정합니다. 여기에서 실제 사용 작업을 수행합니다. 그런 다음, 각 콘솔 애플리케이션은 하나의 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 인스턴스를 만들고 Event Hubs 서비스에 등록합니다.

이 예제 시나리오에서는 16개의 파티션을 5개의 **EventProcessorHost** 인스턴스에 할당했다고 가정하겠습니다. 일부 **EventProcessorHost** 인스턴스는 다른 인스턴스보다 더 많은 파티션을 소유할 수 있습니다. **EventProcessorHost** 인스턴스를 소유한 각 파티션의 경우 `SimpleEventProcessor` 클래스의 인스턴스를 만듭니다. 따라서 각 파티션에 하나씩 할당되어 전체 `SimpleEventProcessor`의 16개 인스턴스가 있습니다.

다음 목록에서는 이 예제를 요약합니다.

- 16개의 Event Hubs 파티션
- 5대의 VM, 각 VM에 1개의 소비자 앱(예: Consumer.exe).
- 동록된 5개의 EPH 인스턴스, Consumer.exe별로 각 VM에 1개.
- 5개의 EPH 인스턴스에서 생성된 16개의 `SimpleEventProcessor` 개체.
- 1개의 EPH 인스턴스가 4개의 파티션을 소유할 수 있으므로 1개의 Consumer.exe 앱에는 4개의 `SimpleEventProcessor` 개체가 포함됩니다.

## <a name="partition-ownership-tracking"></a>파티션 소유권 추적

EPH 인스턴스(또는 소비자)에 대한 파티션의 소유권은 추적을 위해 제공되는 Azure Storage 계정을 통해 추적됩니다. 다음과 같이 간단한 테이블로 추적을 시각화할 수 있습니다. 제공된 스토리지 계정에서 Blob을 검사하여 실제 구현을 확인할 수 있습니다.

| **소비자 그룹 이름** | **파티션 ID** | **호스트 이름(소유자)** | **임대(또는 소유권) 획득 시간** | **파티션(검사점)의 오프셋** |
| --- | --- | --- | --- | --- |
| $Default | 0 | 소비자\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | 소비자\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | 소비자\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | 소비자\_VM3 | 2018-04-15T01:22:56 | 976 |

여기에서 각 호스트는 특정 기간(임대 기간) 동안 파티션의 소유권을 획득합니다. 호스트가 실패할 경우(VM 종료) 임대가 만료됩니다. 다른 호스트가 파티션의 소유권을 가져오려고 시도하고 호스트 중 하나가 성공합니다. 이 프로세스는 새 소유자에서 파티션에 대한 임대를 다시 설정합니다. 이렇게 하면 한 번에 하나의 판독기만이 소비자 그룹 내의 지정된 파티션에서 읽을 수 있습니다.

## <a name="receive-messages"></a>메시지 받기

[ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)에 대한 호출은 이벤트의 컬렉션을 제공합니다. 사용자의 책임 하에 이러한 이벤트를 처리합니다. 프로세서 호스트에서 모든 메시지를 한 번 이상 처리되도록 하려는 경우 고유한 다시 시도 유지 코드를 작성해야 합니다. 하지만 포이즌 메시지에 대해 주의해야 합니다.

비교적 빠르게 작업을 수행하는 것이 좋습니다. 즉, 가능한 작은 처리로 수행합니다. 대신 소비자 그룹을 사용합니다. 저장소에 써야 하 고 일부 라우팅을 수행 해야 하는 경우 두 개의 소비자 그룹을 사용 하 고 별도로 실행 되는 두 개의 [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 구현을 사용 하는 것이 좋습니다.

처리하는 동안 특정 시점에 읽고 완료한 내용을 추적하는 것이 좋습니다. 스트림의 시작 부분으로 돌아가지 않도록 읽기를 다시 시작해야 하는 경우 추적하는 것이 중요합니다. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)는 *검사점*을 사용하여 이 추적을 간소화합니다. 검사점은 지정된 소비자 그룹 내에서 지정된 파티션의 위치 또는 오프셋입니다. 여기서 메시지를 처리하는 작업을 충족합니다. **EventProcessorHost**에서 검사점을 표시하는 작업은 [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) 개체에서 [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) 메서드를 호출하여 수행됩니다. 이 작업은 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 메서드 내에서 수행되지만 [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)에서 수행될 수도 있습니다.

## <a name="checkpointing"></a>검사점 설정

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) 메서드에는 두 개의 오버로드가 포함됩니다. 첫 번째는 매개 변수 없이 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)에서 반환한 컬렉션 내의 가장 높은 이벤트 오프셋에 대한 검사점입니다. 이 오프셋은 "상위 워터" 마크이며 호출할 때 모든 최신 이벤트를 처리한다고 가정합니다. 이러한 방식으로 이 메서드를 사용하는 경우 다른 이벤트 처리 코드가 반환된 후에 호출해야 합니다. 두 번째 오버로드를 통해 검사점에 대한 [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) 인스턴스를 지정할 수 있습니다. 이 메서드를 사용하면 검사점에 대한 워터 마크의 다양한 형식을 사용할 수 있습니다. 이 워터 마크를 사용하여 "하위 워터" 마크를 구현할 수 있습니다. 확신하는 가장 낮은 시퀀스의 이벤트가 처리되었습니다. 이 오버로드는 오프셋 관리에서 유연하게 사용할 수 있도록 제공됩니다.

검사점을 수행할 때 파티션 별 정보(특히, 오프셋)를 포함한 JSON 파일은 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)에 대한 생성자에서 제공된 스토리지 계정에 작성됩니다. 이 파일이 지속적으로 업데이트됩니다. 컨텍스트에서 검사점을 사용해야 합니다. 모든 메시지를 검사점으로 지정하지 않는 것이 좋습니다. 아마도 검사점에 사용된 스토리지 계정은 이 부하를 처리하지 않지만 무엇보다도 모든 단일 이벤트를 검사점으로 지정하면 큐에 대기한 메시지 패턴을 나타냅니다. 이런 이유로 Service Bus 큐가 이벤트 허브보다 더 나은 옵션일 수 있습니다. Event Hubs의 기본 개념은 대규모로 "최소한 한 번" 제공받는다는 것입니다. 다운스트림 시스템 멱등원을 만들어서 쉽게 오류로부터 복구하거나 여러 번 수신되는 동일한 이벤트에서 해당 결과를 다시 시작할 수 있습니다.

## <a name="thread-safety-and-processor-instances"></a>스레드 안전성 및 프로세서 인스턴스

기본적으로 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)는 스레드로부터 안전하고 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 인스턴스와 관련되어 동기 방식으로 작동합니다. 파티션에 대한 이벤트가 발생할 때 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)는 해당 파티션에 대한 **IEventProcessor** 인스턴스에서 호출되고 파티션에 대한 **ProcessEventsAsync**에 대한 추가 호출을 차단합니다. 메시지 펌프가 계속 다른 스레드의 백그라운드에서 실행되어 **ProcessEventsAsync**에 대한 후속 메시지 및 호출은 기본적으로 큐에 대기합니다. 스레드 보안은 스레드로부터 안전한 컬렉션에 대한 필요성을 제거하고 성능을 크게 향상시킵니다.

## <a name="shut-down-gracefully"></a>정상적으로 종료

마지막으로, [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync)는 모든 파티션 판독기를 완전히 종료할 수 있고 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)의 인스턴스를 종료할 때 항상 호출되어야 합니다. 이렇게 하는 데 실패하면 임대 만료 및 Epoch 충돌로 인해 **EventProcessorHost**의 다른 인스턴스를 시작할 때 지연이 발생할 수 있습니다. Epoch 관리는 문서의 [epoch](#epoch) 섹션에 자세히 설명 되어 있습니다. 

## <a name="lease-management"></a>임대 관리
EventProcessorHost 인스턴스에 이벤트 프로세서 클래스를 등록하면 이벤트 처리가 시작됩니다. 호스트 인스턴스는 이벤트 허브의 일부 파티션에서 임대를 획득하므로 모든 호스트 인스턴스에 걸쳐 균일한 파티션 분포로 수렴하는 방식으로 다른 호스트 인스턴스에서 일부를 가져올 수 있습니다. 각 임대 파티션의 경우 호스트 인스턴스는 제공된 이벤트 프로세서 클래스 인스턴스를 만든 후, 해당 파티션에서 이벤트를 수신하고, 이벤트 프로세서 인스턴스에 전달합니다. 더 많은 리스가 추가되고 더 많은 임대를 가져오면 EventProcessorHost는 결국 모든 소비자 간에 부하를 분산합니다.

앞에서 설명한 대로 추적 테이블은 [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync)의 자동 크기 조정 특성을 크게 간소화합니다. **EventProcessorHost**의 인스턴스가 시작되면 가능한 많은 임대를 획득하고 읽기 이벤트를 시작합니다. 임대가 만료되려고 하면 **EventProcessorHost**는 예약을 배치하여 이를 갱신하려고 합니다. 임대가 갱신 가능한 경우 프로세서는 읽기를 계속하지만 불가능한 경우 판독기가 닫히고 [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)가 호출됩니다. **CloseAsync**는 해당 파티션에 대한 최종 정리를 수행하기에 적합합니다.

**EventProcessorHost**에는 [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) 속성이 포함됩니다. 이 속성을 사용하면 임대 관리를 제어할 수 있습니다. [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 구현을 등록하기 전에 이러한 옵션을 설정합니다.

## <a name="control-event-processor-host-options"></a>이벤트 프로세서 호스트 제어 옵션

또한 [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_)의 한 오버로드는 [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) 개체를 매개 변수로 사용합니다. 이 매개 변수를 사용하여 [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) 자체의 동작을 제어합니다. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions)는 네 가지 속성 및 하나의 이벤트를 정의합니다.

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)의 호출에서 수신하려는 컬렉션의 최대 크기입니다. 이 크기는 최소가 아닌 최대 크기만 해당됩니다. 수신될 메시지가 거의 없으면 **ProcessEventsAsync**는 사용할 수 있는 만큼 많이 사용하여 실행합니다.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): 클라이언트가 수신해야 하는 메시지 수의 상한을 결정하기 위해 기본 AMQP 채널에서 사용하는 값입니다. 이 값은 [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize) 이상이어야 합니다.
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout):이 매개 변수가 **true**인 경우 파티션의 이벤트를 수신 하는 기본 호출 시간이 초과 되 면 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 가 호출 됩니다. 이 메서드는 파티션에서 사용 하지 않는 기간 동안 시간 기반 작업을 수행 하는 데 유용 합니다.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): 함수 포인터 또는 람다 식을 설정할 수 있으며 판독기가 파티션을 읽기 시작할 때 초기 오프셋을 제공하도록 호출됩니다. 오프셋을 사용하는 JSON 파일이 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 생성자에 제공된 스토리지 계정에 저장되지 않으면 판독기는 이 오프셋을 지정하지 않고 가장 오래된 이벤트에서 시작됩니다. 이 메서드는 판독기 시작 동작을 변경하려는 경우에 유용합니다. 이 메서드를 호출할 때 개체 매개 변수에는 판독기를 시작하기 위한 파티션 ID가 포함됩니다.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)에서 발생하는 내부 예외에 대한 알림을 받을 수 있습니다. 작업이 예상한 대로 작동하지 않는 경우 이 이벤트를 찾기 시작하는 것이 좋습니다.

## <a name="epoch"></a>Epoch

Receive epoch의 작동 방식은 다음과 같습니다.

### <a name="with-epoch"></a>Epoch 사용
Epoch는 파티션/임대 소유권을 적용 하기 위해 서비스에서 사용 하는 고유 식별자 (epoch 값)입니다. [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) 메서드를 사용 하 여 Epoch 기반 수신기를 만듭니다. 이 메서드는 Epoch 기반 수신기를 만듭니다. 지정 된 소비자 그룹의 특정 이벤트 허브 파티션에 대해 수신기가 생성 됩니다.

Epoch 기능을 사용 하면 다음 규칙을 사용 하 여 소비자 그룹에 수신기가 하나만 있는지 확인할 수 있습니다.

- 소비자 그룹에 기존 수신기가 없는 경우 사용자는 epoch 값을 사용 하 여 수신기를 만들 수 있습니다.
- Epoch 값은 e1이 고 e1은 e1 값 e2 (e1 < = e2)를 사용 하 여 새 수신자를 만든 경우 e1을 가진 받는 사람에 게 자동으로 연결이 끊어집니다.
- Epoch 값이 e1 인 수신기가 있고, e1 > e2 인 epoch 값 e2를 사용 하 여 새 수신자를 만든 후에 e2를 만들지 못했습니다.

### <a name="no-epoch"></a>Epoch 없음
[CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) 메서드를 사용 하 여 Epoch 기반이 아닌 수신기를 만듭니다. 

사용자가 단일 소비자 그룹에 여러 개의 수신기를 만들려는 스트림 처리에는 몇 가지 시나리오가 있습니다. 이러한 시나리오를 지원 하기 위해 epoch 없이 수신기를 만들 수 있으며,이 경우 소비자 그룹에서 최대 5 개의 동시 수신기를 사용할 수 있습니다.

### <a name="mixed-mode"></a>혼합 모드(Mixed Mode)
Epoch를 사용 하 여 수신기를 만든 다음 동일한 소비자 그룹에서 epoch로 전환 하거나 그 반대로 전환 하는 응용 프로그램을 사용 하지 않는 것이 좋습니다. 그러나이 동작을 수행 하는 경우 서비스는 다음 규칙을 사용 하 여 처리 합니다.

- Epoch e1을 사용 하 여 이미 생성 된 수신기가 있고 적극적으로 이벤트를 받고 새 받는 사람이 epoch를 사용 하지 않고 생성 된 경우 새 받는 사람 만들기가 실패 합니다. Epoch 수신기는 항상 시스템에서 우선 적용 됩니다.
- 이미 epoch e1을 사용 하 여 만든 수신기가 있고 새 MessagingFactory에 epoch를 사용 하지 않고 새 수신자를 만든 경우 새 수신자 만들기가 성공 합니다. 여기서는 시스템에서 10 분 후에 "받는 사람 연결 끊김"을 감지 한다는 주의 사항이 있습니다.
- Epoch를 사용 하지 않고 만든 수신기가 하나 이상 있고 epoch e1을 사용 하 여 새 수신자를 만든 경우 모든 이전 수신기의 연결이 끊어집니다.


> [!NOTE]
> Epoch를 사용 하는 응용 프로그램 및 epoch를 사용 하지 않는 응용 프로그램에 대해 서로 다른 소비자 그룹을 사용 하 여 오류를 방지 하는 것이 좋습니다. 


## <a name="next-steps"></a>다음 단계

이제 이벤트 프로세서 호스트에 익숙해졌으므로 다음 아티클을 참조하여 Event Hubs에 대해 자세히 알아봅니다.

- Event Hubs 시작
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs 프로그래밍 가이드](event-hubs-programming-guide.md)
* [Event Hubs의 가용성 및 일관성](event-hubs-availability-and-consistency.md)
* [Event Hubs FAQ](event-hubs-faq.md)
* [GitHub에 대한 Event Hubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples)
