---
title: Azure Functions 및 Azure SignalR Service를 사용 하 여 실시간 앱 빌드
description: Azure SignalR Service를 사용 하 여 서버 리스 응용 프로그램에서 간략히 설명 합니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: c06203cfd7537dd4c9bc35c75a3f21d12bf64d26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401890"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Azure Functions 및 Azure SignalR Service를 사용 하 여 실시간 앱 빌드

Azure SignalR Service와 Azure Functions는 둘 다 완전히 관리되는 서비스라서 인프라를 관리하는 대신 애플리케이션 빌드에 집중이 가능한 확장성이 높은 서비스이기 때문에, [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 환경에서 두 가지 서비스를 함께 사용하여 실시간 통신을 제공하는 것이 일반적입니다.

## <a name="integrate-real-time-communications-with-azure-services"></a>Azure 서비스를 통해 실시간 통신 통합

Azure Functions를 사용하면 JavaScript, C # 및 Java를 비롯한 [여러 가지 언어](../azure-functions/supported-languages.md)로 코드를 작성하여 클라우드에서 이벤트가 발생할 때마다 트리거 할 수 있습니다. 이러한 이벤트의 예는 다음과 같습니다.

* HTTP 및 웹후크 요청
* 주기적 타이머
* 다음과 같은 Azure 서비스의 이벤트:
    - Event Grid
    - Event Hubs
    - Service Bus
    - Cosmos DB 변경 피드
    - Storage - Blob 및 큐
    - Salesforce 및 SQL Server와 같은 Logic Apps 커넥터

Azure Functions를 사용하여 이러한 이벤트를 Azure SignalR Service와 통합하면, 이벤트가 발생할 때마다 수천 명의 고객에게 알릴 수 있습니다.

Azure Functions 및 SignalR Service로 구현할 수 있는 실시간 서버리스 메시징의 일반적인 시나리오는 다음과 같습니다.

* 실시간 대시보드 또는 지도에 IoT 디바이스 원격 분석 데이터 시각화
* Cosmos DB에서 문서가 업데이트되면 애플리케이션의 데이터 업데이트
* Salesforce에서 새 주문이 생성되면 앱 내 알림 보내기

## <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions의 SignalR Service 바인딩

Azure Functions용 SignalR Service 바인딩을 통해 Azure Function 앱이 SignalR Service에 연결된 클라이언트에 메시지를 게시할 수 있습니다. 클라이언트는 .NET, JavaScript 및 Java에서 사용할 수 있는 SignalR 클라이언트 SDK를 사용하여 서비스에 연결할 수 있으며 더 많은 언어에서 곧 제공 될 예정입니다.

### <a name="an-example-scenario"></a>예제 시나리오

SignalR Service 바인딩을 사용하는 방법의 예에는 Azure Functions를 사용하여 Azure Cosmos DB 및 SignalR Service와 통합하여 Cosmos DB 변경 피드에 새로운 이벤트가 나타날 때 실시간 메시지를 전송하는 것입니다.

![Cosmos DB, Azure Functions, SignalR Service](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. Cosmos DB 컬렉션에서 변경 발생
2. 변경 이벤트가 Cosmos DB 변경 피드로 전파
3. Cosmos DB 트리거를 사용하여 변경 이벤트에 의해 Azure Functions 트리거
4. SignalR Service 출력 바인딩이 SignalR Service에 메시지 게시
5. SignalR Service에서 연결된 모든 클라이언트에 메시지 게시

### <a name="authentication-and-users"></a>인증 및 사용자

SignalR Service를 사용하면 모든 클라이언트 또는 클라이언트의 하위 집합(예: 단일 사용자에 속하는 하위 집합)에만 메시지를 브로드캐스트할 수 있습니다. Azure Functions에 대한 SignalR Service 바인딩은 App Service 인증과 결합하여 Azure Active Directory, Facebook 및 Twitter와 같은 공급자를 통해 사용자를 인증할 수 있습니다. 그런 다음 이렇게 인증된 사용자에게 메시지를 직접 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Functions를 SignalR Service와 함께 사용하여 다양한 실시간 메시징 시나리오를 사용하는 방법에 대한 개요를 살펴봤습니다.

Azure Functions 및 SignalR Service를 함께 사용 하는 방법에 대 한 전체 내용은 다음 가이드를 참조 하세요.

* [Azure Functions 개발 및 SignalR Service를 사용 하 여 구성](signalr-concept-serverless-development-config.md)

자세히 알아보려면 다음 빠른 시작 중 하나를 수행하세요.

* [Azure SignalR Service Serverless 빠른 시작 - C#](signalr-quickstart-azure-functions-csharp.md)
* [Azure SignalR Service Serverless 빠른 시작 - JavaScript](signalr-quickstart-azure-functions-javascript.md)