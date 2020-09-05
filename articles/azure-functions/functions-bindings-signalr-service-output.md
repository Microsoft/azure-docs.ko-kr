---
title: Azure Functions SignalR 서비스 출력 바인딩
description: Azure Functions에서 SignalR 서비스 메시지를 보내는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: b401ff20b553629341c5190deb4cfc54c474d789
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212580"
---
# <a name="signalr-service-output-binding-for-azure-functions"></a>Azure Functions에 대 한 SignalR 서비스 출력 바인딩

Azure SignalR Service를 사용하여 하나 이상의 메시지를 보내려면 *SignalR* 출력 바인딩을 사용합니다. 다음과 같이 메시지를 브로드캐스트할 수 있습니다.

- 연결 된 모든 클라이언트
- 특정 사용자에 게 인증 된 연결 된 클라이언트

출력 바인딩을 사용 하 여 그룹을 관리할 수도 있습니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-signalr-service.md)를 참조하세요.

## <a name="broadcast-to-all-clients"></a>모든 클라이언트에 브로드캐스트

다음 예제에서는 모든 연결 된 클라이언트에 대 한 출력 바인딩을 사용 하 여 메시지를 보내는 함수를 보여 줍니다. *대상은* 각 클라이언트에서 호출할 메서드의 이름입니다. *Arguments* 속성은 클라이언트 메서드에 전달할 0 개 이상의 개체 배열입니다.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

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

C # 스크립트 코드는 다음과 같습니다.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="send-to-a-user"></a>사용자에게 보내기

SignalR 메시지에서 *사용자 ID* 를 설정 하 여 사용자에 게 인증 된 연결에만 메시지를 보낼 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

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

C # 스크립트 코드는 다음과 같습니다.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="send-to-a-group"></a>그룹으로 보내기

SignalR 메시지에서 *그룹 이름을* 설정 하 여 그룹에 추가 된 연결에만 메시지를 보낼 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

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

C # 스크립트 코드는 다음과 같습니다.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="group-management"></a>그룹 관리

SignalR 서비스를 사용 하면 사용자를 그룹에 추가할 수 있습니다. 그런 다음 그룹에 메시지를 보낼 수 있습니다. `SignalR`출력 바인딩을 사용 하 여 사용자의 그룹 멤버 자격을 관리할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

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

### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

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
> `ClaimsPrincipal`올바르게 바인딩되도록 하려면 Azure Functions에서 인증 설정을 구성 해야 합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예에서는 그룹에 사용자를 추가 합니다.

*function.js의* 예

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

### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예에서는 그룹에서 사용자를 제거 합니다.

*function.js의* 예

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
> `ClaimsPrincipal`올바르게 바인딩되도록 하려면 Azure Functions에서 인증 설정을 구성 해야 합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예에서는 그룹에 사용자를 추가 합니다.

*function.js의* 예

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

### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예에서는 그룹에서 사용자를 제거 합니다.

*function.js의* 예

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

# <a name="python"></a>[Python](#tab/python)

### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예에서는 그룹에 사용자를 추가 합니다.

*function.js의* 예

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

### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예에서는 그룹에서 사용자를 제거 합니다.

*function.js의* 예

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

# <a name="java"></a>[Java](#tab/java)

### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

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

### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

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
|**type**| 해당 없음 | `signalRConnectionInfo`로 설정해야 합니다.|
|**direction**| 해당 없음 | `in`로 설정해야 합니다.|
|**name**| 해당 없음 | 연결 정보 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**hubName**|**HubName**| 이 값은 연결 정보가 생성되는 SignalR 허브의 이름으로 설정되어야 합니다.|
|**Id**|**Id**| 선택 사항: 액세스 키 토큰에서 설정될 사용자 식별자 클레임의 값입니다. |
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 연결 문자열("AzureSignalRConnectionString"에 대한 기본값)을 포함하는 앱 설정의 이름 |

### <a name="signalr"></a>SignalR

다음 표에서는 *function.json* 파일 및 `SignalR` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type**| 해당 없음 | `signalR`로 설정해야 합니다.|
|**direction**| 해당 없음 | `out`로 설정해야 합니다.|
|**name**| 해당 없음 | 연결 정보 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**hubName**|**HubName**| 이 값은 연결 정보가 생성되는 SignalR 허브의 이름으로 설정되어야 합니다.|
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 연결 문자열("AzureSignalRConnectionString"에 대한 기본값)을 포함하는 앱 설정의 이름 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>다음 단계

- [서비스 끝점 URL 및 액세스 토큰 반환 (입력 바인딩)](./functions-bindings-signalr-service-input.md)
