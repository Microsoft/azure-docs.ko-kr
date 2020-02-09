---
title: Azure Functions 앱 개발 & 구성-Azure SignalR
description: Azure Functions 및 Azure SignalR 서비스를 사용 하 여 서버 리스 실시간 응용 프로그램을 개발 하 고 구성 하는 방법에 대한 세부 정보
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: f86a63315798d982f7e78fd1ff293061daf50132
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786778"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure SignalR Service를 사용 하 여 개발 및 구성 Azure Functions

Azure Functions 응용 프로그램은 [Azure SignalR 서비스 바인딩을](../azure-functions/functions-bindings-signalr-service.md) 활용 하 여 실시간 기능을 추가할 수 있습니다. 클라이언트 응용 프로그램은 여러 언어로 제공 되는 클라이언트 Sdk를 사용 하 여 Azure SignalR Service에 연결 하 고 실시간 메시지를 수신 합니다.

이 문서에서는 SignalR Service와 통합 된 Azure 함수 앱을 개발 하 고 구성 하는 개념을 설명 합니다.

## <a name="signalr-service-configuration"></a>SignalR 서비스 구성

Azure SignalR Service는 여러 가지 모드로 구성할 수 있습니다. Azure Functions와 함께 사용 하는 경우 서비스를 *서버* 리스 모드로 구성 해야 합니다.

Azure Portal에서 SignalR Service 리소스의 *설정* 페이지를 찾습니다. *서비스 모드* 를 *서버*리스로 설정 합니다.

![SignalR Service 모드](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 개발

Azure Functions 및 Azure SignalR 서비스를 사용 하 여 빌드한 서버 리스 실시간 응용 프로그램은 일반적으로 다음 두 가지 Azure Functions 필요 합니다.

* 클라이언트에서 유효한 SignalR 서비스 액세스 토큰 및 서비스 엔드포인트 URL을 얻기 위해 호출 하는 "negotiate" 함수
* 메시지를 보내거나 그룹 멤버 자격을 관리 하는 하나 이상의 함수

### <a name="negotiate-function"></a>negotiate 함수

클라이언트 응용 프로그램에는 Azure SignalR Service에 연결 하는 데 유효한 액세스 토큰이 필요 합니다. 액세스 토큰은 지정 된 사용자 ID에 대해 익명 이거나 인증 될 수 있습니다. 서버를 사용 하지 않는 SignalR 서비스 응용 프로그램에는 토큰 및 기타 연결 정보 (예: SignalR Service 엔드포인트 URL)를 가져오기 위해 "negotiate" 라는 HTTP 엔드포인트이 필요 합니다.

HTTP로 트리거된 Azure 함수 및 *SignalRConnectionInfo* 입력 바인딩을 사용 하 여 연결 정보 개체를 생성 합니다. 함수는 `/negotiate`로 끝나는 HTTP 경로를 포함 해야 합니다.

Negotiate 함수를 만드는 방법에 대한 자세한 내용은 [ *SignalRConnectionInfo* 입력 바인딩 참조](../azure-functions/functions-bindings-signalr-service.md#input)를 참조 하세요.

인증 된 토큰을 만드는 방법에 대한 자세한 내용은 [App Service 인증 사용](#using-app-service-authentication)을 참조 하세요.

### <a name="sending-messages-and-managing-group-membership"></a>메시지 보내기 및 그룹 멤버 자격 관리

*SignalR* 출력 바인딩을 사용 하 여 Azure SignalR Service에 연결 된 클라이언트에 메시지를 보냅니다. 모든 클라이언트에 메시지를 브로드캐스트 하거나 특정 사용자 ID로 인증 되거나 특정 그룹에 추가 된 클라이언트의 하위 집합으로 메시지를 보낼 수 있습니다.

하나 이상의 그룹에 사용자를 추가할 수 있습니다. *SignalR* 출력 바인딩을 사용 하 여 그룹에 사용자를 추가 하거나 그룹에서 사용자를 제거할 수도 있습니다.

자세한 내용은 [ *SignalR* 출력 바인딩 참조](../azure-functions/functions-bindings-signalr-service.md#output)를 참조 하세요.

### <a name="signalr-hubs"></a>SignalR 허브

SignalR에는 "허브" 라는 개념이 있습니다. 각 클라이언트 연결 및 Azure Functions에서 보낸 각 메시지의 범위는 특정 허브로 지정 됩니다. 연결 및 메시지를 논리적 네임 스페이스로 분리 하는 방법으로 허브를 사용할 수 있습니다.

## <a name="client-development"></a>클라이언트 개발

SignalR client 응용 프로그램은 여러 언어 중 하나에서 SignalR client SDK를 활용 하 여 Azure SignalR Service에서 메시지에 쉽게 연결 하 고 메시지를 수신할 수 있습니다.

### <a name="configuring-a-client-connection"></a>클라이언트 연결 구성

SignalR Service에 연결 하려면 클라이언트는 다음 단계로 구성 된 성공적인 연결 협상을 완료 해야 합니다.

1. 올바른 연결 정보를 얻기 위해 위에서 설명한 *negotiate* HTTP 엔드포인트에 대한 요청을 수행 합니다.
1. *Negotiate* 엔드포인트에서 가져온 서비스 엔드포인트 URL 및 액세스 토큰을 사용 하 여 SignalR service에 연결 합니다.

SignalR 클라이언트 Sdk는 협상 핸드셰이크를 수행 하는 데 필요한 논리를 이미 포함 하 고 있습니다. `negotiate` 세그먼트를 제외 하 고 negotiate 엔드포인트의 URL을 SDK의 `HubConnectionBuilder`에 전달 합니다. JavaScript의 예제는 다음과 같습니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

규칙에 따라 SDK는 URL에 `/negotiate`을 자동으로 추가 하 고이를 사용 하 여 협상을 시작 합니다.

> [!NOTE]
> 브라우저에서 JavaScript/TypeScript SDK를 사용 하는 경우 함수 앱에서 [CORS (원본 간 리소스 공유)를 사용 하도록 설정](#enabling-cors) 해야 합니다.

SignalR client SDK를 사용 하는 방법에 대한 자세한 내용은 해당 언어에 대한 설명서를 참조 하세요.

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>클라이언트에서 서비스로 메시지 보내기

SignalR SDK를 사용 하면 클라이언트 응용 프로그램에서 SignalR hub의 백 엔드 논리를 호출할 수 있지만,이 기능은 Azure Functions와 함께 SignalR Service를 사용 하는 경우에는 아직 지원 되지 않습니다. HTTP 요청을 사용 하 여 Azure Functions를 호출 합니다.

## <a name="azure-functions-configuration"></a>Azure Functions 구성

Azure SignalR Service와 통합 하는 azure 함수 앱은 [지속적으로 배포](../azure-functions/functions-continuous-deployment.md)하 고, [zip을 배포](../azure-functions/deployment-zip-push.md)하 고, [패키지에서 실행](../azure-functions/run-functions-from-deployment-package.md)하는 등의 기법을 사용 하 여 일반적인 azure 함수 앱 처럼 배포할 수 있습니다.

그러나 SignalR 서비스 바인딩을 사용 하는 앱에 대한 몇 가지 특별 한 고려 사항이 있습니다. 클라이언트가 브라우저에서 실행 되는 경우 CORS를 사용 하도록 설정 해야 합니다. 응용 프로그램에 인증이 필요한 경우 negotiate 엔드포인트을 App Service 인증과 통합할 수 있습니다.

### <a name="enabling-cors"></a>CORS 사용

JavaScript/TypeScript 클라이언트는 협상 함수에 대해 HTTP 요청을 수행 하 여 연결 협상을 시작 합니다. 클라이언트 응용 프로그램이 Azure 함수 앱과 다른 도메인에서 호스트 되는 경우 함수 앱에서 CORS (크로스-원본 자원 공유)를 사용 하도록 설정 해야 합니다. 그렇지 않으면 브라우저에서 요청을 차단 합니다.

#### <a name="localhost"></a>호스트

로컬 컴퓨터에서 함수 앱을 실행 하는 경우 CORS에 `Host` 섹션을 추가 하 *여 CORS* 를 사용 하도록 설정할 수 있습니다. `Host` 섹션에서 다음 두 가지 속성을 추가 합니다.

* `CORS`-클라이언트 응용 프로그램의 원본인 기준 URL을 입력 합니다.
* `CORSCredentials`-"withCredentials" 요청을 허용 하도록 `true`로 설정 합니다.

예제:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>클라우드-Azure Functions CORS

Azure 함수 앱에서 CORS를 사용 하도록 설정 하려면 Azure Portal에서 함수 앱의 *플랫폼 기능* 탭 아래 cors 구성 화면으로 이동 합니다.

> [!NOTE]
> CORS 구성은 Azure Functions Linux 소비 계획에서 아직 사용할 수 없습니다. [Azure API Management](#cloud---azure-api-management) 를 사용 하 여 CORS를 사용 하도록 설정 합니다.

SignalR 클라이언트가 negotiate 함수를 호출 하려면 액세스 제어-자격 증명이 있는 CORS를 사용 하도록 설정 해야 합니다. 확인란을 선택 하 여 사용 하도록 설정 합니다.

허용 된 *원본* 섹션에서 웹 응용 프로그램의 원본 기반 URL을 사용 하 여 항목을 추가 합니다.

![CORS 구성](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>클라우드-Azure API Management

Azure API Management는 기존 백 엔드 서비스에 기능을 추가 하는 API 게이트웨이를 제공 합니다. 이를 사용 하 여 함수 앱에 CORS를 추가할 수 있습니다. 작업 단위 가격 책정 및 월간 무료 부여를 사용 하는 소비 계층을 제공 합니다.

[Azure 함수 앱을 가져오는](../api-management/import-function-app-as-api.md)방법에 대한 자세한 내용은 API Management 설명서를 참조 하세요. 가져온 후에는 인바운드 정책을 추가 하 여 액세스 제어-자격 증명 지원을 통해 CORS를 사용 하도록 설정할 수 있습니다.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

API Management URL을 사용 하도록 SignalR 클라이언트를 구성 합니다.

### <a name="using-app-service-authentication"></a>App Service 인증 사용

Azure Functions에는 Facebook, Twitter, Microsoft 계정, Google, Azure Active Directory 등 널리 사용 되는 공급자를 지 원하는 기본 제공 인증이 있습니다. 이 기능을 *SignalRConnectionInfo* 바인딩과 통합 하 여 사용자 ID에 인증 된 Azure SignalR Service에 대한 연결을 만들 수 있습니다. 응용 프로그램은 해당 사용자 ID를 대상으로 하는 *SignalR* 출력 바인딩을 사용 하 여 메시지를 보낼 수 있습니다.

Azure Portal의 함수 앱 *플랫폼 기능* 탭에서 *인증/권한 부여* 설정 창을 엽니다. 사용자가 선택한 id 공급자를 사용 하 여 인증을 구성 하려면 [App Service 인증](../app-service/overview-authentication-authorization.md) 에 대한 설명서를 따르세요.

구성 된 인증 된 HTTP 요청에는 인증 된 id의 사용자 이름 및 사용자 ID를 포함 하는 `x-ms-client-principal-name` 및 `x-ms-client-principal-id` 헤더가 포함 됩니다.

*SignalRConnectionInfo* binding 구성에서 이러한 헤더를 사용 하 여 인증 된 연결을 만들 수 있습니다. `x-ms-client-principal-id` 헤더를 사용 C# 하는 negotiate 함수 예제는 다음과 같습니다.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

그런 다음 SignalR 메시지의 `UserId` 속성을 설정 하 여 해당 사용자에 게 메시지를 보낼 수 있습니다.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

다른 언어에 대한 자세한 내용은 [Azure SignalR Service bindings](../azure-functions/functions-bindings-signalr-service.md) for Azure Functions 참조를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Functions를 사용 하 여 서버를 사용 하지 않는 SignalR Service 응용 프로그램을 개발 하 고 구성 하는 방법을 배웠습니다. [SignalR 서비스 개요 페이지](index.yml)의 빠른 시작 또는 자습서 중 하나를 사용 하 여 응용 프로그램을 직접 만들어 보세요.