---
title: Azure SignalR Service 내부 기능
description: Azure SignalR Service 내부 기능에 대한 개요입니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: cbcdfccfdca1dbed3b766b3f50295b1d355b3478
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401767"
---
# <a name="azure-signalr-service-internals"></a>Azure SignalR Service 내부 기능

Azure SignalR Service는 ASP.NET Core SignalR 프레임워크를 기반으로 구축되었습니다. 또한 ASP.NET SignalR을 미리 보기 기능으로 지원합니다.

> ASP.NET SignalR을 지원하기 위해 Azure SignalR Service는 ASP.NET Core 프레임워크를 기반으로 ASP.NET SignalR의 데이터 프로토콜을 다시 구현합니다.

코드 몇 줄을 변경하여 로컬 ASP.NET Core SignalR 애플리케이션을 SignalR Service에서 작동하도록 쉽게 마이그레이션할 수 있습니다.

아래 다이어그램에서는 애플리케이션 서버를 통해 SignalR Service를 사용하는 경우 일반적인 아키텍처를 설명합니다.

자체 호스팅 ASP.NET Core SignalR 애플리케이션과의 차이점도 설명되어 있습니다.

![아키텍처](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>서버 연결

자체 호스팅 ASP.NET Core SignalR 애플리케이션 서버는 클라이언트를 직접 수신 대기하고 연결합니다.

SignalR Service를 사용하면 애플리케이션 서버가 영구적인 클라이언트 연결을 더 이상 허용하지 않고, 대신:

1. Azure SignalR Service SDK에서 각 허브에 대해 `negotiate` 엔드포인트가 노출됩니다.
1. 이 엔드포인트는 클라이언트의 협상 요청에 응답하고 클라이언트를 SignalR Service에 리디렉션합니다.
1. 결국 클라이언트가 SignalR Service에 연결됩니다.

자세한 내용은 [클라이언트 연결](#client-connections)을 참조하세요.

애플리케이션 서버가 시작되면, 
- ASP.NET Core SignalR의 경우, Azure SignalR Service SDK에서 SignalR Service에 허브당 WebSocket 연결을 5개 엽니다. 
- ASP.NET SignalR의 경우, Azure SignalR Service SDK가 SignalR Service에 허브당 WebSocket 연결을 5개 열고, 애플리케이션 WebSocket 연결당 하나를 엽니다.

WebSocket 연결 5개는 [구성](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount)에서 변경할 수 있는 기본값입니다.

클라이언트와 주고 받는 메시지는 이러한 연결로 멀티플렉싱됩니다.

이 연결은 SignalR Service에 항상 연결된 상태로 유지됩니다. 네트워크 문제로 인해 서버 연결이 끊어지면,
- 이 서버 연결을 통해 제공되는 모든 클라이언트의 연결이 끊어지고(자세한 내용은 [클라이언트와 서버 간의 데이터 전송](#data-transmit-between-client-and-server) 참조);
- 서버가 자동으로 다시 연결되기 시작합니다.

## <a name="client-connections"></a>클라이언트 연결

SignalR Service를 사용하는 경우 클라이언트는 애플리케이션 서버 대신 SignalR Service에 연결됩니다.
클라이언트와 SignalR Service 사이에 영구 연결을 설정하는 단계는 두 가지입니다.

1. 클라이언트가 애플리케이션 서버에 협상 요청을 보냅니다. Azure SignalR Service SDK를 사용하여 애플리케이션 서버가 SignalR Service의 URL 및 액세스 토큰으로 리디렉션 응답을 반환합니다.

- ASP.NET Core SignalR의 경우 일반적인 리디렉션 응답은 다음과 같습니다.
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- ASP.NET SignalR의 경우 일반적인 리디렉션 응답은 다음과 같습니다.
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. 리디렉션 응답을 받으면, 클라이언트는 새 URL과 액세스 토큰을 사용하여 정상적인 프로세스를 시작하고 SignalR Service에 연결합니다.

ASP.NET Core SignalR의 [전송 프로토콜](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)에 대해 자세히 알아보세요.

## <a name="data-transmit-between-client-and-server"></a>클라이언트와 서버 간의 데이터 전송

클라이언트가 SignalR Service에 연결되면 서비스 런타임은 이 클라이언트에 제공할 서버 연결을 찾습니다.
- 이 단계는 한 번만 발생하고 클라이언트와 서버 연결 간의 일대일 매핑입니다.
- 매핑은 클라이언트나 서버 연결이 끊어질 때까지 SignalR Service에 유지됩니다.

이 시점에서 애플리케이션 서버는 새 클라이언트의 정보가 포함된 이벤트를 수신합니다. 애플리케이션 서버에 클라이언트에 대한 논리적 연결이 생성됩니다. SignalR Service를 통해 클라이언트에서 애플리케이션 서버로 데이터 채널이 설정됩니다.

SignalR Service는 클라이언트에서 페어링 애플리케이션 서버로 데이터를 전송합니다. 그리고 애플리케이션 서버의 데이터는 매핑된 클라이언트로 전송됩니다.

이와 같이, Azure SignalR Service는 본질적으로 애플리케이션 서버와 클라이언트 간의 논리적 전송 계층입니다. 모든 영구 연결은 SignalR Service에 오프로드됩니다.
애플리케이션 서버는 클라이언트 연결은 걱정할 필요 없이 허브 클래스의 비즈니스 논리만 처리하면 됩니다.