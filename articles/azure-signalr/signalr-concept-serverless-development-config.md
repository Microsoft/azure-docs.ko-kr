---
title: 개발 및 Azure Functions SignalR Service 응용 프로그램 구성
description: 개발 및 Azure Functions 및 Azure SignalR Service를 사용 하 여 서버 리스 실시간 응용 프로그램을 구성 하는 방법에 대 한 세부 정보
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809013"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions 개발 및 Azure SignalR Service를 사용 하 여 구성

Azure 함수 응용 프로그램에서 활용할 수 있습니다 합니다 [Azure SignalR Service 바인딩](../azure-functions/functions-bindings-signalr-service.md) 실시간 기능을 추가 합니다. 클라이언트 응용 프로그램 여러 언어에서 사용할 수 있는 클라이언트 Sdk를 사용 하 여 Azure SignalR Service에 연결 하 고 실시간 메시지를 수신 합니다.

이 문서에서는 개발 및 SignalR Service와 통합 된 Azure 함수 앱 구성에 대 한 개념을 설명 합니다.

## <a name="signalr-service-configuration"></a>SignalR Service 구성

Azure SignalR Service는 다른 모드로 구성할 수 있습니다. Azure Functions를 사용 하면 서비스에서 구성 해야 *서버 없이* 모드입니다.

Azure portal에서 찾습니다 합니다 *설정을* SignalR Service 리소스의 페이지입니다. 설정 합니다 *서비스 모드* 하 *서버*합니다.

![SignalR Service 모드](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 개발

일반적으로 Azure Functions 및 Azure SignalR Service를 사용 하 여 빌드한 실시간 서버 리스 응용 프로그램에는 두 가지 Azure 기능이 필요 합니다.

* 클라이언트를 유효한 SignalR Service를 가져오기 위해 호출 하는 "negotiate" 함수 액세스 토큰 및 서비스 끝점 URL
* 메시지를 보내거나 그룹 멤버 자격을 관리 하는 하나 이상의 함수

### <a name="negotiate-function"></a>함수를 협상 합니다.

클라이언트 응용 프로그램을 Azure SignalR Service에 연결할 유효한 액세스 토큰이 필요 합니다. 익명 또는 지정 된 사용자 ID를 인증 된 액세스 토큰 일 수 있습니다. 서버 리스 SignalR Service 응용 프로그램 "negotiate" 토큰 및 SignalR Service 끝점 URL과 같은 기타 연결 정보를 가져오려면 HTTP 끝점 이름이 필요 합니다.

HTTP를 사용 하 여 Azure 함수 트리거 및 *SignalRConnectionInfo* 연결 정보 개체를 생성 하는 바인딩을 입력 합니다. 함수에 대 한 HTTP 경로 끝나는 있어야 합니다. `/negotiate`합니다.

협상 함수를 만드는 방법에 대 한 자세한 내용은 참조는 [ *SignalRConnectionInfo* 바인딩 참조 입력](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding)합니다.

인증된 토큰을 만드는 방법에 대 한 자세한 참조 [를 사용 하 여 App Service 인증](#using-app-service-authentication)합니다.

### <a name="sending-messages-and-managing-group-membership"></a>메시지를 보내고 그룹 멤버 자격 관리

사용 된 *SignalR* Azure SignalR Service에 연결 된 클라이언트에 메시지를 보낼 출력 바인딩. 모든 클라이언트에 메시지를 브로드캐스트할 수 있습니다 또는 특정 사용자 ID를 사용 하 여 인증 하거나 특정 그룹에 추가 된는 클라이언트의 하위 집합을 보낼 수 있습니다.

하나 이상의 그룹에 사용자를 추가할 수 있습니다. 사용할 수도 있습니다는 *SignalR* 출력 바인딩 추가 또는 사용자 그룹에서 제거 합니다.

자세한 내용은 참조는 [ *SignalR* 출력 바인딩 참조](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding)합니다.

### <a name="signalr-hubs"></a>SignalR 허브

SignalR에 "허브" 개념이 있습니다. 각 클라이언트 연결 및 Azure Functions에서 보낸 각 메시지는 특정 hub에 범위가 지정 됩니다. 논리적 네임 스페이스에 연결 및 메시지를 구분 하는 방법으로 허브를 사용할 수 있습니다.

## <a name="client-development"></a>클라이언트 개발

SignalR 클라이언트 응용 프로그램에는 SignalR 클라이언트 SDK는 여러 언어 중 하나로 쉽게 연결 하 고 Azure SignalR Service에서 메시지를 받을를 활용할 수 있습니다.

### <a name="configuring-a-client-connection"></a>클라이언트 연결 구성

SignalR Service에 연결 하려면 클라이언트 구성이 단계는 성공적으로 연결 협상을 완료 해야 합니다.

1. 요청을 하는 *협상* 올바른 연결 정보를 얻으려면 위에 설명 된 HTTP 끝점
1. 얻은 액세스 토큰 및 서비스 끝점 URL을 사용 하 여 SignalR Service에 연결 합니다 *협상* 끝점

SignalR 클라이언트 Sdk는 이미 협상 핸드셰이크를 수행 하는 데 필요한 논리를 포함 합니다. 빼기 협상 끝점의 URL을 전달 합니다 `negotiate` sdk의 세그먼트 `HubConnectionBuilder`합니다. Javascript에서 예제는 다음과 같습니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

규칙에 따라 SDK를 자동으로 추가 `/negotiate` URL로 협상을 시작 하는 데 사용 합니다.

> [!NOTE]
> JavaScript/TypeScript SDK 브라우저를 사용 하는 경우 해야 [크로스-원본 자원 공유 (CORS)를 사용 하도록 설정](#enabling-cors) 함수 앱에 있습니다.

SignalR 클라이언트 SDK를 사용 하는 방법에 대 한 자세한 내용은 언어에 대 한 설명서를 참조 하세요.

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>서비스에 클라이언트에서 메시지 보내기

SignalR SDK에서는 클라이언트 응용 프로그램에서 SignalR 허브를 백 엔드 논리를 호출할 수 있지만 SignalR Service를 사용 하 여 Azure Functions를 사용 하 여이 기능은 아직 지원 되지 않습니다. Azure Functions를 호출 하려면 HTTP 사용 하 여 요청 합니다.

## <a name="azure-functions-configuration"></a>Azure Functions 구성

와 같은 기술을 사용 하 여 모든 일반적인 Azure 함수 앱과 같은 Azure SignalR Service와 통합 되는 azure 함수 앱을 배포할 수 있습니다 [연속 배포](../azure-functions/functions-continuous-deployment.md)를 [zip 배포](../azure-functions/deployment-zip-push.md), 및 [패키지에서 실행](../azure-functions/run-functions-from-deployment-package.md)합니다.

그러나 SignalR 서비스 바인딩을 사용 하는 앱에 대 한 특별 고려 사항의 두 가지가 있습니다. 클라이언트는 브라우저에서 실행 하는 경우 CORS는 사용할 수 있어야 합니다. 및 앱에서 인증을 요구 하는 경우 App Service 인증을 사용 하 여 협상 끝점을 통합할 수 있습니다.

### <a name="enabling-cors"></a>CORS 사용

JavaScript/TypeScript 클라이언트 HTTP 요청을 연결 협상을 시작 하는 협상 함수입니다. 클라이언트 응용 프로그램이 Azure 함수 앱을 다른 도메인에서 호스팅되는 경우 함수 앱에서 크로스-원본 자원 공유 (CORS)를 사용할 수 있어야 하거나 브라우저 요청을 차단 합니다.

#### <a name="localhost"></a>localhost

로컬 컴퓨터에서 함수 앱을 실행 하는 경우 추가할 수 있습니다는 `Host` 섹션을 *local.settings.json* CORS를 사용 하도록 설정 합니다. 에 `Host` 섹션, 두 속성을 추가 합니다.

* `CORS` -원본 클라이언트 응용 프로그램에는 기본 URL 입력
* `CORSCredentials` -설정 `true` "withCredentials" 요청을 허용 하도록

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

#### <a name="azure"></a>Azure

CORS를 사용 하도록 Azure 함수 앱에서 CORS 구성 화면으로 이동 합니다 *플랫폼 기능* Azure portal에서 함수 앱의 탭 합니다.

협상 함수를 호출 하려면 SignalR 클라이언트에 대 한 액세스 제어-허용-자격 증명을 사용 하 여 CORS는 활성화 되어야 합니다. 사용 하도록 설정 하려면이 확인란을 선택 합니다.

에 *허용 된 원본* 섹션에서 웹 응용 프로그램의 원본 기본 URL 사용 하 여 항목을 추가 합니다.

![CORS 구성](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>App Service 인증을 사용 하 여

Azure Functions에 Facebook, Twitter, Microsoft 계정, Google 및 Azure Active Directory와 같은 인기 있는 공급자를 지 원하는 기본 제공 인증. 이 기능을 통합할 수는 *SignalRConnectionInfo* 바인딩을 Azure SignalR Service에 대 한 사용자 ID를 인증 된 연결을 만들려면 응용 프로그램에서 사용 하 여 메시지를 보낼 수는 *SignalR* 출력 대상으로 사용자 ID에 해당 하는 바인딩

함수 앱의 Azure portal의 *플랫폼 기능* 탭을 열고 합니다 *인증/권한 부여* 설정 창. 에 대 한 설명서를 수행 [App Service 인증](../app-service/overview-authentication-authorization.md) 원하는 id 공급자를 사용 하 여 인증을 구성 합니다.

인증 된 HTTP 요청에는 구성 되 면 `x-ms-client-principal-name` 고 `x-ms-client-principal-id` 인증된 된 id의 이름과 사용자 ID를 각각 포함 된 헤더입니다.

이러한 헤더를 사용할 수 있습니다 하 *SignalRConnectionInfo* 인증 된 연결을 만드는 바인딩 구성 합니다. 다음은 예제 C# 함수를 사용 하는 협상 합니다 `x-ms-client-principal-id` 헤더입니다.

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

그러면 메시지는 사용자에 게 설정 하 여 보낼 수 있습니다는 `UserId` SignalR 메시지의 속성입니다.

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

다른 언어에 대 한 내용은 참조는 [Azure SignalR Service 바인딩](../azure-functions/functions-bindings-signalr-service.md) 참조 하는 Azure Functions에 대 한 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 개발 및 Azure Functions를 사용 하 여 서버 리스 SignalR Service 응용 프로그램을 구성 하는 방법을 배웠습니다. 응용 프로그램을 만들어 보세요에서 빠른 시작 또는 자습서 중 하나를 사용 하 여 직접 합니다 [SignalR Service 개요 페이지](index.yml)합니다.