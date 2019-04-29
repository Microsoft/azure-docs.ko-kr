---
title: Node에서 Azure Relay 하이브리드 연결 HTTP 요청 시작 | Microsoft Docs
description: Node에서 Azure Relay 하이브리드 연결 HTTP 요청에 대한 Node.js 콘솔 애플리케이션을 작성합니다.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: e54a096bd27efddaa9eafb8619e787178550a6e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553956"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Node에서 Relay 하이브리드 연결 HTTP 요청 시작

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

이 빠른 시작에서는 HTTP 프로토콜을 사용하여 메시지를 보내고 받는 Node.js 받는 사람 및 보낸 사람 애플리케이션을 만듭니다. 애플리케이션은 Azure Relay 하이브리드 연결 기능을 사용합니다. Azure Relay에 대한 일반적인 내용은 [Azure Relay](relay-what-is-it.md)를 참조하세요. 

이 빠른 시작에서 수행하는 단계는 다음과 같습니다.

1. Azure Portal을 사용하여 Relay 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 해당 네임스페이스에 하이브리드 연결을 만듭니다.
3. 메시지를 수신하는 서버(수신기) 콘솔 애플리케이션을 작성합니다.
4. 메시지를 보내는 클라이언트(발신자) 콘솔 애플리케이션을 작성합니다.
5. 애플리케이션을 실행합니다.

## <a name="prerequisites"></a>필수 조건
- [Node.js](https://nodejs.org/en/).
- Azure 구독. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

## <a name="create-a-namespace-using-the-azure-portal"></a>Azure Portal을 사용하여 네임스페이스 만들기
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Azure Portal을 사용하여 하이브리드 연결 만들기
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>서버 애플리케이션(수신기) 만들기
릴레이에서 메시지를 수신 대기하고 받으려면 Node.js 콘솔 애플리케이션을 작성합니다.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>클라이언트 애플리케이션(보낸 사람) 만들기

Relay에 메시지를 보내려면 HTTP 클라이언트를 사용하거나 Node.js 콘솔 애플리케이션을 작성합니다.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>애플리케이션 실행

1. Node.js 명령 프롬프트 형식 `node listener.js`의 서버 애플리케이션을 실행합니다.
2. Node.js 명령 프롬프트 형식 `node sender.js`의 클라이언트 애플리케이션을 실행하고 일부 텍스트를 입력합니다.
3. 서버 애플리케이션 콘솔이 클라이언트 애플리케이션에 입력된 텍스트를 출력하는지 확인합니다.

축하합니다. Node.js를 사용하여 종단 간 하이브리드 연결 애플리케이션을 만들었습니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 메시지를 보내고 받는 데 HTTP를 사용한 Node.js 클라이언트 및 서버 애플리케이션을 만들었습니다. Azure Relay의 하이브리드 연결 기능은 또한 WebSockets를 사용하여 메시지를 보내고 받을 수 있도록 지원합니다. Azure Relay 하이브리드 연결에 WebSockets를 사용하는 방법에 대한 자세한 내용은 [WebSockets 빠른 시작](relay-hybrid-connections-node-get-started.md)을 참조하세요.

이 빠른 시작에서는 클라이언트 및 서버 애플리케이션을 만드는 데 Node.js를 사용했습니다. .NET Framework를 사용하여 클라이언트 및 서버 애플리케이션을 작성하는 방법은 [.NET WebSockets 빠른 시작](relay-hybrid-connections-dotnet-get-started.md) 또는 [.NET HTTP 빠른 시작](relay-hybrid-connections-http-requests-dotnet-get-started.md)을 참조하세요.
