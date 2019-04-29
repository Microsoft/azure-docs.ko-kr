---
title: 가용성 및 일관성 - Azure Event Hubs | Microsoft Docs
description: 파티션을 사용하여 Azure Event Hubs에서 가용성 및 일관성의 최대치를 제공하는 방법입니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e5cad797b633d43bcc9ead657a60fca8aa6679bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822388"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Event Hubs의 가용성 및 일관성

## <a name="overview"></a>개요
Azure Event Hubs는 [파티셔닝 모델](event-hubs-features.md#partitions)을 사용하여 단일 이벤트 허브 내 가용성과 병렬 처리를 개선합니다. 예를 들어 이벤트 허브에 4개의 파티션이 있고 해당 파티션 중 하나가 부하 분산 작업 중 한 서버에서 다른 서버로 이동된 경우 여전히 나머지 3개의 파티션을 보내고 받을 수 있습니다. 또한 더 많은 파티션을 사용하면 더 많은 동시 읽기 권한자가 데이터를 처리할 수 있으므로 집계 처리량이 개선됩니다. 분산된 시스템에서 분할 및 순서 지정이 가진 의미를 이해하는 것은 솔루션 디자인의 중요한 측면입니다.

순서 지정과 가용성 간의 균형을 잘 이해하려면 Brewer의 정리라고도 하는 [CAP 정리](https://en.wikipedia.org/wiki/CAP_theorem)를 참조하세요. 이 정리에서는 일관성, 가용성, 파티션 허용 오차를 선택할 때의 차이점을 다룹니다. 네트워크에서 분할한 시스템의 경우 항상 일관성과 가용성을 절충해야 합니다.

Brewer의 정리는 일관성 및 가용성을 다음과 같이 정의합니다.
* 파티션 허용 오차: 파티션 오류가 발생하는 경우 데이터 처리를 계속하는 데이터 처리 시스템의 기능입니다.
* 가용성: 실패하지 않은 노드가 오류 또는 시간 제한 없이 적절한 시간 내에 적절한 응답을 반환합니다.
* 일관성: 읽기는 지정된 클라이언트에 대해 가장 최근의 쓰기를 반환하도록 보장됩니다.

## <a name="partition-tolerance"></a>파티션 허용 오차
Event Hubs는 분할된 데이터 모델을 기반으로 빌드됩니다. 설치하는 동안 이벤트 허브의 파티션 수를 구성할 수 있지만 나중에 이 값을 변경할 수 없습니다. Event Hubs에서 파티션을 사용해야 하므로 애플리케이션의 가용성 및 일관성에 대한 결정을 내려야 합니다.

## <a name="availability"></a>가용성
Event Hubs를 시작하는 가장 간단한 방법은 기본 동작을 사용하는 것입니다. 새 **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** 개체를 만들고 **[보내기](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** 메서드를 사용하는 경우 사용자의 이벤트는 이벤트 허브의 파티션 사이에 자동으로 분산됩니다. 이 동작을 사용하면 가동 시간을 최대화할 수 있습니다.

최대 가동 시간을 필요로 하는 사용 사례의 경우 이 모델을 사용하는 것이 좋습니다.

## <a name="consistency"></a>일관성
일부 시나리오에서는 이벤트의 순서가 중요합니다. 예를 들어, 백 엔드 시스템에서 삭제 명령 전에 업데이트 명령을 처리하려고 할 수 있습니다. 이 경우에는 이벤트에 대한 파티션 키를 설정하거나 `PartitionSender` 개체를 사용하여 이벤트만 특정 파티션에 보낼 수 있습니다. 이렇게 하면 이러한 이벤트를 파티션에서 읽을 때 순서대로 읽게 됩니다.

이 구성에서는 전송하는 특정 파티션을 사용할 수 없는 경우 오류 응답이 수신된다는 점을 염두에 두어야 합니다. 이에 비해 단일 파티션에 대한 선호도가 없는 경우 Event Hubs 서비스는 사용 가능한 다음 파티션에 이벤트를 보냅니다.

가동 시간을 극대화하는 동시에 순서도 지정할 수 있는 한 가지 솔루션은 이벤트 처리 애플리케이션의 일부로 이벤트를 집계하는 것입니다. 이 작업을 수행하는 가장 쉬운 방법은 이벤트에 사용자 지정 시퀀스 번호 속성 스탬프를 지정하는 것입니다. 다음은 예를 보여 주는 코드입니다.

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

이 예에서는 이벤트 허브에서 사용 가능한 파티션 중 하나에 이벤트를 보내고 애플리케이션에서 해당 시퀀스 번호를 설정합니다. 이 솔루션에서는 상태를 처리 애플리케이션에서 유지해야 하지만 사용할 가능성이 높은 엔드포인트를 보낸 사람에게 제공합니다.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 서비스 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
