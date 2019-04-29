---
title: Azure Relay API 개요 | Microsoft Docs
description: 사용 가능한 Azure Relay API 개요
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 05d7ac56d6c1c48125eb458d0eee852ba396b300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593350"
---
# <a name="available-relay-apis"></a>사용 가능한 Relay API

## <a name="runtime-apis"></a>런타임 API

다음 표에는 현재 사용 가능한 모든 Relay 런타임 클라이언트가 나와 있습니다.

[추가 정보](#additional-information) 섹션에는 각 런타임 라이브러리의 상태에 대한 자세한 정보가 포함되어 있습니다.

| 언어/플랫폼 | 사용 가능한 기능 | 클라이언트 패키지 | 리포지토리 |
| --- | --- | --- | --- |
| .NET Standard | 하이브리드 연결 | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF 릴레이 | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| 노드 | 하이브리드 연결 | [Websockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP 요청: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>추가 정보

#### <a name="net"></a>.NET

.NET 에코시스템에는 여러 개의 런타임이 있으므로 Relay용 .NET 라이브러리도 여러 개 있습니다. .NET Framework 라이브러리는 .NET Framework 환경에서만 실행될 수 있지만 .NET Standard 라이브러리는 .NET Core 또는 .NET Framework를 사용하여 실행할 수 있습니다. .NET Framework에 대한 자세한 내용은 [프레임워크 버전](/dotnet/articles/standard/frameworks)을 참조하세요.

.NET Framework 라이브러리는 WCF 프로그래밍 모델만 지원하고 WCF `net.tcp` 전송을 기반으로 하는 소유 이진 프로토콜을 사용합니다. 이 프로토콜 및 라이브러리는 기존 애플리케이션과의 하위 호환성을 위해 유지 관리됩니다.

.NET Standard 라이브러리는 HTTP 및 Websocket을 빌드하는 하이브리드 연결 릴레이에 대한 개방형 프로토콜 정의를 기반으로 합니다. 라이브러리는 HTTP 요청 응답을 위해 Websocket에서의 스트림 추상화 및 간단한 요청-응답 API 제스처를 지원합니다. [웹 API](https://github.com/Azure/azure-relay-dotnet) 샘플에서는 웹 서비스를 위해 하이브리드 연결을 ASP.NET Core와 통합하는 방법을 보여 줍니다.

#### <a name="nodejs"></a>Node.js

위 테이블에 나열된 하이브리드 연결 모듈은 기존 Node.js 모듈을 로컬 네트워킹 스택 대신 Azure Relay 서비스에서 수신 대기하는 대체 구현으로 바꾸거나 수정합니다.

`hyco-https` 모듈은 핵심 Node.js 모듈 `http` 및 `https`를 수정하고 부분적으로 재정의하여 HTTPS 수신기 구현을 제공합니다. 이는 이러한 핵심 모듈을 사용하는 많은 기존의 Node.js 모듈 및 애플리케이션과 호환됩니다.

`hyco-ws` 및 `hyco-websocket` 모듈은 Node.js에 인기 있는 `ws` 및 `websocket` 모듈을 수정하여 대체 수신기 구현을 제공합니다. 그러면 모듈 및 어느 한 모듈에 의존하는 애플리케이션을 하이브리드 연결 릴레이 뒤에서 실행할 수 있습니다.

이러한 모듈에 대한 자세한 내용은 [azure-relay-node](https://github.com/Azure/azure-relay-node) GitHub 리포지토리에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Relay에 대한 자세한 내용은 다음 링크를 방문하세요.
* [Azure 릴레이란?](relay-what-is-it.md)
* [릴레이 FAQ](relay-faq.md)
