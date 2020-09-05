---
title: Azure Event Hubs-클라이언트 Sdk | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs의 클라이언트 Sdk에 대 한 정보를 제공 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930879"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs-클라이언트 Sdk
이 문서에서는 Azure Event Hubs에서 지 원하는 Sdk에 대해 다음과 같은 정보를 제공 합니다. 

- 응용 프로그램에서 사용할 수 있는 패키지의 위치 
- 소스 코드, 샘플, 추가 정보, 변경 로그, 보고 된 문제를 찾고 새 문제를 발생 시킬 수 있는 GitHub 위치 
- 빠른 시작 자습서 링크 

## <a name="client-sdks"></a>클라이언트 SDK
다음 표에서는 현재 사용 가능한 모든 Azure Event Hubs 런타임 클라이언트에 대해 설명 합니다. 이러한 라이브러리 중 일부에는 제한된 관리 기능이 포함되어 있지만 관리 작업에만 사용되는 특정 라이브러리도 있습니다. 이러한 라이브러리의 핵심 초점은 이벤트 허브에서 **메시지를 보내고 받는** 것입니다.

| 언어 | 패키지 | 참조 | 
| -------- | ------- | --------------- | 
| . NET Standard (**최신 버전** 및에서는 .net Core와 .NET Framework 모두 지원) | [EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [EventHubs.](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Standard (**레거시** 및는 .net Core와 .NET Framework 모두 지원) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . .NET Framework (**이전**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[자습서](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[자습서](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(레거시)** | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[자습서](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[자습서](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[자습서](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [azure/event-허브](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[자습서](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs 저장소-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[자습서](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-이벤트-허브-이동](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-event-hubs-go)</li><li>[자습서](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-이벤트-허브-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-event-hubs-c)</li><li>[자습서](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>관리 SDK
다음 표에서는 현재 사용 가능한 관리용 라이브러리를 나열합니다. 이러한 라이브러리에는 런타임 작업이 포함 되어 있지 않으며 **Event Hubs 엔터티를 관리**하는 용도로만 사용할 수 있습니다.

| 언어 | 패키지 | 참조 | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](./event-hubs-about.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)
