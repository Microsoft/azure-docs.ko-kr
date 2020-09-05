---
title: Azure Functions SignalR 서비스 입력 바인딩
description: Azure Functions에서 SignalR 서비스 끝점 URL 및 액세스 토큰을 반환 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 326115a2a6cf29fcf211cdbd918edd0994fe45ed
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212121"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Azure Functions에 대 한 SignalR Service 입력 바인딩

클라이언트를 Azure SignalR Service에 연결할 수 있기 전에 서비스 엔드포인트 URL 및 유효한 액세스 토큰을 검색해야 합니다. *SignalRConnectionInfo* 입력 바인딩은 서비스에 연결하는 데 사용되는 SignalR Service 엔드포인트 URL 및 유효한 토큰을 생성합니다. 토큰은 시간 제한적이고 연결에 대해 특정 사용자를 인증하는 데 사용될 수 있으므로 토큰을 캐시하거나 클라이언트 간에 공유해서는 안 됩니다. 이 바인딩을 사용하는 HTTP 트리거는 연결 정보를 검색하기 위해 클라이언트에서 사용할 수 있습니다.

이 바인딩을 사용 하 여 SignalR 클라이언트 SDK에서 사용할 수 있는 "negotiate" 함수를 만드는 방법에 대 한 자세한 내용은 SignalR 서비스 개념 설명서의 [Azure Functions 개발 및 구성 문서](../azure-signalr/signalr-concept-serverless-development-config.md) 를 참조 하세요.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-signalr-service.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 입력 바인딩을 사용하여 SignalR 연결 정보를 획득하고 HTTP를 통해 해당 정보를 반환하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.js파일의* SignalR 연결 정보 입력 바인딩과 바인딩을 사용 하 여 연결 정보를 반환 하는 [c # 스크립트 함수](functions-reference-csharp.md) 를 보여 줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

C # 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 SignalR 연결 정보 입력 바인딩 및 바인딩을 사용하여 연결 정보를 반환하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예에서는 *function.js파일의* SignalR 연결 정보 입력 바인딩과 바인딩을 사용 하 여 연결 정보를 반환 하는 [Python 함수](functions-reference-python.md) 를 보여 줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

다음은 Python 코드입니다.

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 입력 바인딩을 사용 하 여 SignalR 연결 정보를 획득 하 고 HTTP를 통해 반환 하는 [Java 함수](functions-reference-java.md) 를 보여 줍니다.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>인증된 토큰

인증된 클라이언트에서 함수를 트리거하는 경우 생성된 토큰에 사용자 ID 클레임을 추가할 수 있습니다. [App Service 인증](../app-service/overview-authentication-authorization.md)을 사용 하 여 함수 앱에 인증을 쉽게 추가할 수 있습니다.

App Service 인증은 `x-ms-client-principal-id`라는 HTTP 헤더 및 인증된 사용자의 클라이언트 보안 주체 ID 및 이름 각각을 포함하는 `x-ms-client-principal-name`을 설정합니다.

# <a name="c"></a>[C#](#tab/csharp)

바인딩 `UserId` [식을](./functions-bindings-expressions-patterns.md)사용 하 여 두 헤더의 값에 대 한 바인딩의 속성을 설정할 수 있습니다. `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` .

```cs
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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

바인딩 `userId` [식을](./functions-bindings-expressions-patterns.md)사용 하 여 두 헤더의 값에 대 한 바인딩의 속성을 설정할 수 있습니다. `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` .

예제 function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

C # 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

바인딩 `userId` [식을](./functions-bindings-expressions-patterns.md)사용 하 여 두 헤더의 값에 대 한 바인딩의 속성을 설정할 수 있습니다. `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` .

예제 function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

바인딩 `userId` [식을](./functions-bindings-expressions-patterns.md)사용 하 여 두 헤더의 값에 대 한 바인딩의 속성을 설정할 수 있습니다. `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` .

예제 function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

다음은 Python 코드입니다.

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

바인딩 `userId` [식을](./functions-bindings-expressions-patterns.md)사용 하 여 두 헤더의 값에 대 한 바인딩의 속성을 설정할 수 있습니다. `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>다음 단계

- [SignalR 서비스 메시지 보내기 (출력 바인딩)](./functions-bindings-signalr-service-output.md) 
