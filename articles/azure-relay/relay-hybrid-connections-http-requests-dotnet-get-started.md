---
title: Azure Relay 하이브리드 연결-.NET의 HTTP 요청
description: .NET에서 Azure Relay 하이브리드 연결 HTTP 요청에 대한 C# 콘솔 애플리케이션을 작성합니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 7a11abb984da3601a4d6aa921224e01f94d0871c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922585"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>.NET에서 Relay 하이브리드 연결 HTTP 요청 시작
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

이 빠른 시작에서는 HTTP 프로토콜을 사용하여 메시지를 보내고 받는 .NET 받는 사람 및 보낸 사람 애플리케이션을 만듭니다. 애플리케이션은 Azure Relay 하이브리드 연결 기능을 사용합니다. Azure Relay에 대한 일반적인 내용은 [Azure Relay](relay-what-is-it.md)를 참조하세요. 

이 빠른 시작에서 수행하는 단계는 다음과 같습니다.

1. Azure Portal을 사용하여 Relay 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 해당 네임스페이스에 하이브리드 연결을 만듭니다.
3. 메시지를 수신하는 서버(수신기) 콘솔 애플리케이션을 작성합니다.
4. 메시지를 보내는 클라이언트(발신자) 콘솔 애플리케이션을 작성합니다.
5. 애플리케이션을 실행합니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Visual Studio 2015 이상](https://www.visualstudio.com) - 이 자습서의 예제에서는 Visual Studio 2017을 사용합니다.
* Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

## <a name="create-a-namespace"></a>네임스페이스 만들기
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>하이브리드 연결 만들기
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>서버 애플리케이션(수신기) 만들기
Visual Studio에서 메시지를 릴레이로부터 수신 대기하고 받을 C# 콘솔 애플리케이션을 작성합니다.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>클라이언트 애플리케이션(보낸 사람) 만들기
Visual Studio에서 메시지를 릴레이로 보낼 C# 콘솔 애플리케이션을 작성합니다.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>애플리케이션 실행
1. 서버 애플리케이션을 실행합니다. 콘솔 창에서 다음과 같은 텍스트가 표시됩니다.

    ```
    Online
    Server listening
    ```
1. 클라이언트 애플리케이션을 실행합니다. 클라이언트 창에서 `hello!`가 표시됩니다. 클라이언트는 HTTP 요청을 서버에 보냈고, 서버는 `hello!`로 응답했습니다. 
3. 이제 콘솔 창을 닫으려면 콘솔 창에서 **ENTER** 키를 누릅니다. 

축 하 합니다. 전체 하이브리드 연결 응용 프로그램을 만들었습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 메시지를 보내고 받는 데 HTTP를 사용한 .NET 클라이언트 및 서버 애플리케이션을 만들었습니다. Azure Relay의 하이브리드 연결 기능은 또한 WebSockets를 사용하여 메시지를 보내고 받을 수 있도록 지원합니다. Azure Relay 하이브리드 연결에 WebSockets를 사용하는 방법에 대한 자세한 내용은 [WebSockets 빠른 시작](relay-hybrid-connections-dotnet-get-started.md)을 참조하세요.

이 빠른 시작에서는 클라이언트 및 서버 애플리케이션을 만드는 데 .NET Framework를 사용했습니다. Node.js를 사용하여 클라이언트 및 서버 애플리케이션을 작성하는 방법은 [Node.js WebSockets 빠른 시작](relay-hybrid-connections-node-get-started.md) 또는 [Node.js HTTP 빠른 시작](relay-hybrid-connections-http-requests-dotnet-get-started.md)을 참조하세요.
