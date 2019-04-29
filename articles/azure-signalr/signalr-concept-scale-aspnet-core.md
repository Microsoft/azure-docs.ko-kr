---
title: Azure SignalR을 사용하여 ASP.NET Core SignalR 크기 조정
description: Azure SignalR Service를 사용하여 ASP.NET Core SignalR 애플리케이션의 크기를 조정하는 방법에 대한 개요입니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 8a4012d204b6dafa1233e4ce3d878590120be47d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640228"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Azure SignalR Service를 사용하여 ASP.NET Core SignalR 애플리케이션 크기 조정

## <a name="developing-signalr-apps"></a>SignalR 앱 개발

현재 웹 애플리케이션에서 사용할 수 있는 SignalR의 [두 가지 버전](https://docs.microsoft.com/aspnet/core/signalr/version-differences)은 ASP.NET용 SignalR과 최신 버전인 ASP.NET Core SignalR입니다. Azure SignalR Service는 ASP.NET Core SignalR에 기반을 둔 Azure 관리 서비스입니다.

ASP.NET Core SignalR은 이전 버전을 다시 생성한 것입니다. 결과적으로 ASP.NET Core SignalR은 이전 버전의 SignalR과 호환되지 않습니다. API와 동작이 서로 다릅니다. ASP.NET Core SignalR SDK는 .NET Standard를 대상으로 하므로 .NET Framework에서도 사용할 수 있습니다. 그러나 이전 API 대신 새 API를 사용해야 합니다. SignalR을 사용하지만 ASP.NET Core SignalR 또는 Azure SignalR Service로 이동하려면 API의 차이를 처리하기 위해 코드를 변경해야 합니다.

Azure SignalR Service를 사용하면 ASP.NET Core SignalR의 서버 쪽 구성 요소가 Azure에서 호스팅됩니다. 그러나 이 기술은 ASP.NET Core를 기반으로 하여 구현되므로 [Azure App Service](../app-service/overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache), [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index)를 통해 호스팅하는 동안 여러 플랫폼(Windows, Linux 및 MacOS)에서 실제 웹 애플리케이션을 실행할 수 있습니다. 사용자 자신의 프로세스에 자체 호스팅을 사용할 수도 있습니다.

애플리케이션이 웹 클라이언트를 실시간 콘텐츠 업데이트로 업데이트하는 최신 기능을 지원하고, 여러 플랫폼(Azure, Windows, Linux 및 macOS)에서 실행하고, 다른 환경에서 호스트하기 위한 것이면 Azure SignalR Service를 활용하는 것이 가장 좋습니다.

## <a name="why-not-deploy-signalr-myself"></a>SignalR을 직접 배포하지 않는 이유는?

ASP.NET Core SignalR을 백 엔드 구성 요소로 지원하는 자신만의 Azure 웹 애플리케이션을 전체 웹 애플리케이션에 배포하는 방법은 여전히 유효합니다.

Azure SignalR Service를 사용하는 주요 이유 중 하나는 단순성입니다. Azure SignalR Service를 사용하면 성능, 확장성, 가용성과 같은 문제를 처리할 필요가 없습니다. 이러한 문제는 99.9% 서비스 수준 계약으로 처리됩니다.

또한 WebSocket은 일반적으로 실시간 콘텐츠 업데이트를 지원하는 데 선호되는 기술입니다. 그러나 많은 수의 WebSocket 영구 연결을 부하 분산하는 것은 크기 조정 시 해결해야 하는 복잡한 문제가 됩니다. 일반적인 해결 방법으로 DNS 부하 분산, 하드웨어 부하 분산 장치 및 소프트웨어 부하 분산을 활용합니다. Azure SignalR Service는 이 문제를 처리합니다.

또 다른 이유로, 실제로 웹 애플리케이션을 호스팅할 필요가 없습니다. 웹 애플리케이션의 논리에서는 [서버를 사용하지 않는 컴퓨팅](https://azure.microsoft.com/overview/serverless-computing/)을 활용할 수 있습니다. 예를 들어 코드는 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 트리거를 통해 호스팅되고 요청 시에만 실행될 것입니다. 코드가 요청 시에만 실행되고 클라이언트와의 긴 연결을 유지하지 않기 때문에 이 시나리오는 까다로울 수 있습니다. Azure SignalR Service는 이미 사용자를 위해 연결을 관리하므로 이러한 상황을 처리할 수 있습니다. 자세한 내용은 [Azure Functions와 함께 SignalR Service를 사용하는 방법에 대한 개요](signalr-concept-azure-functions.md)를 참조하세요.

## <a name="how-does-it-scale"></a>크기 조정은 어떻게 합니까?

SignalR은 일반적으로 SQL Server, Azure Service Bus 또는 Azure Cache for Redis를 사용하여 크기를 조정합니다. Azure SignalR Service는 크기 조정 방식을 처리합니다. 성능과 비용은 이러한 다른 서비스를 복잡하게 처리할 필요 없이 이러한 방식과 비슷합니다. 서비스에 대한 단위 수를 업데이트하면 됩니다. 각 장치마다 최대 1000개의 클라이언트 연결을 지원합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure SignalR을 사용하여 대화방 만들기](signalr-quickstart-dotnet-core.md)