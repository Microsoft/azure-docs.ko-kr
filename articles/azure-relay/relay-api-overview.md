---
title: Azure Relay API 개요 | Microsoft Docs
description: 이 문서에서는 사용 가능한 Azure Relay Api (.NET Standard, .NET Framework, Node.js 등)의 개요를 제공 합니다.
ms.topic: article
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 98bbb1ecc7e870ff9b7687284e7087d44375d275
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935724"
---
# <a name="available-relay-apis"></a>사용 가능한 Relay API

## <a name="runtime-apis"></a>런타임 API

다음 표에는 현재 사용 가능한 모든 Relay 런타임 클라이언트가 나와 있습니다.

[추가 정보](#additional-information) 섹션에는 각 런타임 라이브러리의 상태에 대한 자세한 정보가 포함되어 있습니다.

| 언어/플랫폼 | 사용 가능한 기능 | 클라이언트 패키지 | 리포지토리 |
| --- | --- | --- | --- |
| .NET Standard | 하이브리드 연결 | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF 릴레이 | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| 노드 | 하이브리드 연결 | [Websocket `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websocket `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP 요청: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>추가 정보

#### <a name="net"></a>.NET

.NET 에코시스템에는 여러 개의 런타임이 있으므로 Relay용 .NET 라이브러리도 여러 개 있습니다. .NET Framework 라이브러리는 .NET Framework 환경에서만 실행될 수 있지만 .NET Standard 라이브러리는 .NET Core 또는 .NET Framework를 사용하여 실행할 수 있습니다. .NET framework에 대 한 자세한 내용은 [프레임 워크 버전](/dotnet/articles/standard/frameworks)을 참조 하세요.

.NET Framework 라이브러리는 WCF 프로그래밍 모델만 지원하고 WCF `net.tcp` 전송을 기반으로 하는 소유 이진 프로토콜을 사용합니다. 이 프로토콜 및 라이브러리는 기존 애플리케이션과의 하위 호환성을 위해 유지 관리됩니다.

.NET Standard 라이브러리는 HTTP 및 Websocket을 빌드하는 하이브리드 연결 릴레이에 대한 개방형 프로토콜 정의를 기반으로 합니다. 라이브러리는 Websocket을 통한 스트림 추상화와 HTTP 요청에 응답 하기 위한 간단한 요청-응답 API 제스처를 지원 합니다. [웹 API](https://github.com/Azure/azure-relay-dotnet) 샘플에서는 웹 서비스를 위해 하이브리드 연결을 ASP.NET Core와 통합하는 방법을 보여 줍니다.

#### <a name="nodejs"></a>Node.js

위 테이블에 나열된 하이브리드 연결 모듈은 기존 Node.js 모듈을 로컬 네트워킹 스택 대신 Azure Relay 서비스에서 수신 대기하는 대체 구현으로 바꾸거나 수정합니다.

`hyco-https`이 모듈은 핵심 Node.js 모듈을 부분적으로 재정의 하 고 부분적으로 재정의 하 여 `http` `https` 이러한 핵심 모듈을 사용 하는 여러 기존 Node.js 모듈 및 응용 프로그램과 호환 되는 HTTPS 수신기 구현을 제공 합니다.

`hyco-ws` 및 `hyco-websocket` 모듈은 Node.js에 인기 있는 `ws` 및 `websocket` 모듈을 수정하여 대체 수신기 구현을 제공합니다. 그러면 모듈 및 어느 한 모듈에 의존하는 애플리케이션을 하이브리드 연결 릴레이 뒤에서 실행할 수 있습니다.

이러한 모듈에 대한 자세한 내용은 [azure-relay-node](https://github.com/Azure/azure-relay-node) GitHub 리포지토리에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Relay에 대한 자세한 내용은 다음 링크를 방문하세요.
* [Azure Relay란?](relay-what-is-it.md)
* [릴레이 FAQ](relay-faq.md)
