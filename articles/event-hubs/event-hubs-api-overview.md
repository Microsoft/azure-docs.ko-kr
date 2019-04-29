---
title: Azure Event Hubs API 개요 | Microsoft Docs
description: 사용할 수 있는 Azure Event Hubs API의 개요
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 80566b0246179064d2a479b8c9bf3c79a2a93aac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822627"
---
# <a name="available-event-hubs-apis"></a>사용할 수 있는 Event Hubs API

이 문서에서는 Event Hubs 리소스 관리를 위해 사용할 수 있는 API 클라이언트 세트를 설명합니다.

## <a name="runtime-apis"></a>런타임 API

다음 섹션은 현재 사용 가능한 모든 Azure Event Hubs 런타임 클라이언트를 설명합니다. 이러한 라이브러리 중 일부에는 제한된 관리 기능이 포함되어 있지만 관리 작업에만 사용되는 [특정 라이브러리](#management-apis)도 있습니다. 이러한 라이브러리의 핵심적인 부분은 이벤트 허브에서 메시지를 주고 받는 것입니다.

각 런타임 라이브러리의 현재 상태에 대한 자세한 내용은 [추가 정보](#additional-information)를 참조하세요.

| 언어/플랫폼 | 클라이언트 패키지 | EventProcessorHost 패키지 | 리포지토리 |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/A |
| 자바 | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| 노드 | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N/A | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>추가 정보

#### <a name="net"></a>.NET

.NET 에코 시스템에는 여러 개의 런타임이 있으므로 Event Hub용 .NET 라이브러리도 여러 개 있습니다. .NET Framework 라이브러리는 .NET Framework 환경에서만 실행될 수 있지만 .NET Standard 라이브러리는 .NET Core 또는 .NET Framework를 사용하여 실행할 수 있습니다. .NET Framework 버전에 대한 자세한 내용은 [프레임워크 버전](https://docs.microsoft.com/dotnet/articles/standard/frameworks)을 참조하세요.

#### <a name="node"></a>노드

[Node.js library 라이브러리](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)는 현재 미리 보기로 제공되며 Microsoft 직원 및 외부 기여자가 추가적인 프로젝트로 관리합니다. 소스 코드를 포함하는 모든 참가 프로그램은 언제든지 환영이며 검토해드립니다.

## <a name="management-apis"></a>관리 API

다음 표에서는 현재 사용 가능한 관리용 라이브러리를 나열합니다. 이러한 라이브러리 중 런타임 작업을 포함하는 것은 없으며 Event Hubs 엔터티를 관리하는 데만 사용됩니다.

| 언어/플랫폼 | 관리 패키지 | 리포지토리 |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)
