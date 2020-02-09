---
title: Azure Functions SignalR Service 바인딩
description: Azure Functions에서 SignalR Service 바인딩을 사용하는 방법을 배웁니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: d5ded7200c438fc734e1af0a42ca6071196bd41c
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120340"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions의 SignalR Service 바인딩

이 문서에서는 Azure Functions에서 SignalR Service 바인딩을 사용하여 [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/)에 연결된 클라이언트에 실시간 메시지를 인증하고 전송하는 방법을 설명합니다. Azure Functions는 SignalR Service에 대한 입력 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

SignalR 서비스 바인딩은 [WebJobs SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet 패키지, 버전 1. *에 제공 됩니다. 이 패키지에 대한 소스 코드는 [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

SignalR 서비스와 Azure Functions를 함께 구성 하 고 사용 하는 방법에 대한 자세한 내용은 [Azure SignalR service를 사용 하 여 개발 및 구성 Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md)을 참조 하세요.

### <a name="annotations-library-java-only"></a>주석 라이브러리 (Java 전용)

Java 함수에서 SignalR 서비스 주석을 사용 하려면 pom .xml에 *SignalR* 아티팩트 (버전 1.0 이상)에 대한 종속성을 추가 해야 합니다.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>입력

클라이언트를 Azure SignalR Service에 연결할 수 있기 전에 서비스 엔드포인트 URL 및 유효한 액세스 토큰을 검색해야 합니다. *SignalRConnectionInfo* 입력 바인딩은 서비스에 연결하는 데 사용되는 SignalR Service 엔드포인트 URL 및 유효한 토큰을 생성합니다. 토큰은 시간 제한적이고 연결에 대해 특정 사용자를 인증하는 데 사용될 수 있으므로 토큰을 캐시하거나 클라이언트 간에 공유해서는 안 됩니다. 이 바인딩을 사용하는 HTTP 트리거는 연결 정보를 검색하기 위해 클라이언트에서 사용할 수 있습니다.

이 바인딩을 사용 하 여 SignalR 클라이언트 SDK에서 사용할 수 있는 "negotiate" 함수를 만드는 방법에 대한 자세한 내용은 SignalR 서비스 개념 설명서의 [Azure Functions 개발 및 구성 문서](../azure-signalr/signalr-concept-serverless-development-config.md) 를 참조 하세요.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

다음 예에서는 *함수 json* 파일의 SignalR 연결 정보 입력 바인딩과 바인딩을 사용 하 여 연결 정보를 반환 하는 [ C# 스크립트 함수](functions-reference-csharp.md) 를 보여 줍니다.

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

스크립트 코드는 C# 다음과 같습니다.

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

다음 예제에서는 *함수 json* 파일의 SignalR 연결 정보 입력 바인딩과 바인딩을 사용 하 여 연결 정보를 반환 하는 [Python 함수](functions-reference-python.md) 를 보여 줍니다.

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

### <a name="authenticated-tokens"></a>인증된 토큰

인증된 클라이언트에서 함수를 트리거하는 경우 생성된 토큰에 사용자 ID 클레임을 추가할 수 있습니다. [App Service 인증](../app-service/overview-authentication-authorization.md)을 사용 하 여 함수 앱에 인증을 쉽게 추가할 수 있습니다.

App Service 인증은 `x-ms-client-principal-id`라는 HTTP 헤더 및 인증된 사용자의 클라이언트 보안 주체 ID 및 이름 각각을 포함하는 `x-ms-client-principal-name`을 설정합니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[바인딩 식](./functions-bindings-expressions-patterns.md)을 사용하여 바인딩의 `UserId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다.

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

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

[바인딩 식](./functions-bindings-expressions-patterns.md)을 사용하여 바인딩의 `userId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다.

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

스크립트 코드는 C# 다음과 같습니다.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

[바인딩 식](./functions-bindings-expressions-patterns.md)을 사용하여 바인딩의 `userId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

[바인딩 식](./functions-bindings-expressions-patterns.md)을 사용하여 바인딩의 `userId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다.

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

# <a name="javatabjava"></a>[Java](#tab/java)

[바인딩 식](./functions-bindings-expressions-patterns.md)을 사용하여 바인딩의 `userId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다.

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

## <a name="output"></a>출력

Azure SignalR Service를 사용하여 하나 이상의 메시지를 보내려면 *SignalR* 출력 바인딩을 사용합니다. 연결된 모든 클라이언트에 메시지를 브로드캐스트할 수 있거나, 지정된 사용자에게 인증된 연결된 클라이언트에만 브로드캐스트할 수 있습니다.

또한 사용자가 속한 그룹을 관리 하는 데 사용할 수 있습니다.

### <a name="broadcast-to-all-clients"></a>모든 클라이언트에 브로드캐스트

다음 예제에서는 모든 연결 된 클라이언트에 대한 출력 바인딩을 사용 하 여 메시지를 보내는 함수를 보여 줍니다. *대상은* 각 클라이언트에서 호출할 메서드의 이름입니다. *인수* 는 클라이언트 메서드에 전달할 0 개 이상의 개체 배열입니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

스크립트 코드는 C# 다음과 같습니다.

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

다음은 Python 코드입니다.

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-user"></a>사용자에게 보내기

SignalR 메시지에서 *사용자 ID* 를 설정 하 여 사용자에 게 인증 된 연결에만 메시지를 보낼 수 있습니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

예제 function.json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

스크립트 코드는 C# 다음과 같습니다.

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

예제 function.json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

다음은 Python 코드입니다.

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-group"></a>그룹으로 보내기

SignalR 메시지에서 *그룹 이름을* 설정 하 여 그룹에 추가 된 연결에만 메시지를 보낼 수 있습니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

예제 function.json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

스크립트 코드는 C# 다음과 같습니다.

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

예제 function.json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

다음은 Python 코드입니다.

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="group-management"></a>그룹 관리

SignalR 서비스를 사용 하면 사용자를 그룹에 추가할 수 있습니다. 그런 다음 그룹에 메시지를 보낼 수 있습니다. `SignalR` 출력 바인딩을 사용 하 여 사용자의 그룹 멤버 자격을 관리할 수 있습니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예에서는 그룹에 사용자를 추가 합니다.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예에서는 그룹에서 사용자를 제거 합니다.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> `ClaimsPrincipal` 올바르게 바인딩되어 있도록 하려면 Azure Functions에서 인증 설정을 구성 해야 합니다.

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예에서는 그룹에 사용자를 추가 합니다.

예제 *함수. json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Csx 실행*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예에서는 그룹에서 사용자를 제거 합니다.

예제 *함수. json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Csx 실행*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> `ClaimsPrincipal` 올바르게 바인딩되어 있도록 하려면 Azure Functions에서 인증 설정을 구성 해야 합니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예에서는 그룹에 사용자를 추가 합니다.

예제 *함수. json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예에서는 그룹에서 사용자를 제거 합니다.

예제 *함수. json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예에서는 그룹에 사용자를 추가 합니다.

예제 *함수. json*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_초기화 py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예에서는 그룹에서 사용자를 제거 합니다.

예제 *함수. json*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_초기화 py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

#### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예에서는 그룹에 사용자를 추가 합니다.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예에서는 그룹에서 사용자를 제거 합니다.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

## <a name="configuration"></a>구성

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

다음 표에서는 *function.json* 파일 및 `SignalRConnectionInfo` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type**| n/a | `signalRConnectionInfo`로 설정해야 합니다.|
|**direction**| n/a | `in`로 설정해야 합니다.|
|**name**| n/a | 연결 정보 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**hubName**|**HubName**| 이 값은 연결 정보가 생성되는 SignalR 허브의 이름으로 설정되어야 합니다.|
|**userId**|**UserId**| 선택 사항: 액세스 키 토큰에서 설정될 사용자 식별자 클레임의 값입니다. |
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 연결 문자열("AzureSignalRConnectionString"에 대한 기본값)을 포함하는 앱 설정의 이름 |

### <a name="signalr"></a>SignalR

다음 표에서는 *function.json* 파일 및 `SignalR` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type**| n/a | `signalR`로 설정해야 합니다.|
|**direction**| n/a | `out`로 설정해야 합니다.|
|**name**| n/a | 연결 정보 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**hubName**|**HubName**| 이 값은 연결 정보가 생성되는 SignalR 허브의 이름으로 설정되어야 합니다.|
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 연결 문자열("AzureSignalRConnectionString"에 대한 기본값)을 포함하는 앱 설정의 이름 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](../azure-signalr/signalr-concept-serverless-development-config.md)
