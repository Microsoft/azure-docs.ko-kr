---
title: 빠른 시작 - Azure SignalR Service REST API
description: 다음 샘플에서 Azure SignalR Service와 함께 REST API를 사용하는 방법을 알아봅니다. REST API 사양에 대한 세부 정보를 찾습니다.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 13e11bb6064888ac36a2d2801fee3db1e9e9fcba
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050508"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>빠른 시작: 콘솔 앱에서 실시간 메시지 브로드캐스트

Azure SignalR Service는 브로드캐스트와 같은 서버-클라이언트 통신 시나리오를 지원하는 [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)를 제공합니다. REST API를 호출할 수 있는 모든 프로그래밍 언어를 선택할 수 있습니다. 연결된 모든 클라이언트, 이름별 특정 클라이언트 또는 클라이언트 그룹에 메시지를 게시할 수 있습니다.

이 빠른 시작에서는 명령줄 앱에서 C#으로 작성된 연결된 클라이언트 앱에 메시지를 보내는 방법을 알아보겠습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작은 macOS, Windows 또는 Linux에서 실행할 수 있습니다.

* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* 선택한 텍스트 편집기 또는 코드 편집기

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 <https://portal.azure.com/>에서 Azure Portal에 로그인합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

서비스가 배포되는 동안 코드 준비로 전환하겠습니다. [GitHub의 샘플 앱](https://github.com/aspnet/AzureSignalR-samples.git)을 복제하고, SignalR Service 연결 문자열을 설정하고, 애플리케이션을 로컬로 실행합니다.

1. Git 터미널 창을 엽니다. 샘플 프로젝트를 복제할 폴더로 변경합니다.

1. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

이 샘플은 Azure SignalR Service 사용을 보여 주는 콘솔 앱입니다. 다음 두 가지 모드를 제공합니다.

- 서버 모드: 단순 명령을 사용하여 Azure SignalR Service REST API를 호출합니다.
- 클라이언트 모드: Azure SignalR Service에 연결하고 서버로부터 메시지를 받습니다.

또한 액세스 토큰을 생성하여 Azure SignalR Service에 인증하는 방법을 확인할 수 있습니다.

### <a name="build-the-executable-file"></a>실행 파일 빌드

macOS osx.10.13-x64를 예제로 사용합니다. 다른 플랫폼에서 빌드하는 방법에 대한 [참조](https://docs.microsoft.com/dotnet/core/rid-catalog)를 확인할 수 있습니다.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>클라이언트 시작

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>서버 시작

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

## <a name="run-the-sample-without-publishing"></a>게시하지 않고 샘플 실행

아래 명령을 실행하여 서버 또는 클라이언트를 시작할 수도 있습니다.

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>user-secrets를 사용하여 연결 문자열 지정

샘플의 루트 디렉터리에서 `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"`을 실행할 수 있습니다. 그 뒤에는 `-c "<ConnectionString>"` 옵션이 더 이상 필요하지 않습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

## <a name="usage"></a>사용

서버가 시작된 후에 명령을 사용하여 메시지를 보냅니다.

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

각기 다른 클라이언트 이름으로 여러 클라이언트를 시작할 수 있습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> 타사 서비스와 통합

Azure SignalR 서비스를 통해 타사 서비스를 시스템과 통합할 수 있습니다.

### <a name="definition-of-technical-specifications"></a>기술 사양 정의

다음 표에서는 현재 지원되는 모든 REST API 버전을 보여 줍니다. 각 특정 버전에 대한 정의 파일도 찾을 수 있습니다.

버전 | API 상태 | 문 | 특정
--- | --- | --- | ---
`1.0-preview` | 사용 가능 | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | 사용 가능 | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

각 특정 버전에 사용 가능한 API 목록은 다음 목록에서 확인할 수 있습니다.

API | 1.0-미리 보기 | 1.0
--- | --- | ---
[모두에게 브로드캐스트](#broadcast) | **&#x2713;** | **&#x2713;**
[그룹에 브로드캐스트](#broadcast-group) | **&#x2713;** | **&#x2713;**
일부 그룹에 브로드캐스트 | **&#x2713;** (사용되지 않음) | `N / A`
[사용자에게 보내기](#send-user) | **&#x2713;** | **&#x2713;**
일부 사용자에게 보내기 | **&#x2713;** (사용되지 않음) | `N / A`
[그룹에 사용자 추가](#add-user-to-group) | `N / A` | **&#x2713;**
[그룹에서 사용자 제거](#remove-user-from-group) | `N / A` | **&#x2713;**
[사용자 존재 확인](#check-user-existence) | `N / A` | **&#x2713;**
[모든 그룹에서 사용자 제거](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[연결로 보내기](#send-connection) | `N / A` | **&#x2713;**
[그룹에 연결 추가](#add-connection-to-group) | `N / A` | **&#x2713;**
[그룹에서 연결 제거](#remove-connection-from-group) | `N / A` | **&#x2713;**
[클라이언트 연결 닫기](#close-connection) | `N / A` | **&#x2713;**
[Service Health](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>모두에게 브로드캐스트

버전 | API HTTP 메서드 | 요청 URL | 요청 본문
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | 위와 동일

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>그룹에 브로드캐스트

버전 | API HTTP 메서드 | 요청 URL | 요청 본문
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | 위와 동일

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>사용자에게 보내기

버전 | API HTTP 메서드 | 요청 URL | 요청 본문
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | 위와 동일

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>그룹에 사용자 추가

버전 | API HTTP 메서드 | 요청 URL
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>그룹에서 사용자 제거

버전 | API HTTP 메서드 | 요청 URL
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>그룹에 사용자가 있는지 확인

API 버전 | API HTTP 메서드 | 요청 URL
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

응답 상태 코드 | Description
---|---
`200` | 사용자가 있음
`404` | 사용자가 없음

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>모든 그룹에서 사용자 제거

API 버전 | API HTTP 메서드 | 요청 URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>연결로 메시지 보내기

API 버전 | API HTTP 메서드 | 요청 URL | 요청 본문
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>그룹에 연결 추가

API 버전 | API HTTP 메서드 | 요청 URL
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>그룹에서 연결 제거

API 버전 | API HTTP 메서드 | 요청 URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>클라이언트 연결 닫기

API 버전 | API HTTP 메서드 | 요청 URL
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>서비스 상태

API 버전 | API HTTP 메서드 | 요청 URL
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

응답 상태 코드 | Description
---|---
`200` | 서비스 양호
`5xx` | 서비스 오류

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 REST API를 사용하여 SignalR Service에서 클라이언트로 실시간 메시지를 브로드캐스트하는 방법을 알아보았습니다. 다음으로, REST API를 기반으로 빌드되는 SignalR Service 바인딩을 사용하여 Azure Functions를 개발 및 배포하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure SignalR Service 바인딩을 사용하여 Azure Functions 개발](signalr-quickstart-azure-functions-csharp.md)

[문제가 있나요? 알려주세요.](https://aka.ms/asrs/qsapi)
