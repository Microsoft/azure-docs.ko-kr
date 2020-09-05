---
title: Azure Functions SignalR Service 트리거 바인딩
description: Azure Functions에서 SignalR 서비스 메시지를 보내는 방법에 대해 알아봅니다.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: e2651afbcdc3bae71bb531aa0e821f83264c295d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212595"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Azure Functions에 대 한 SignalR Service 트리거 바인딩

*SignalR* 트리거 바인딩을 사용 하 여 Azure SignalR Service에서 보낸 메시지에 응답 합니다. 함수가 트리거되면 함수에 전달 된 메시지는 json 개체로 구문 분석 됩니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-signalr-service.md)를 참조하세요.

## <a name="example"></a>예제

다음 예제에서는 트리거 바인딩을 사용 하 여 메시지를 수신 하 고 메시지를 기록 하는 함수를 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

C #에 대 한 SignalR Service 트리거 바인딩에는 두 개의 프로그래밍 모델이 있습니다. 클래스 기반 모델 및 기존 모델 클래스 기반 모델은 일관 된 SignalR 서버 쪽 프로그래밍 환경을 제공할 수 있습니다. 및 기존 모델은 다른 함수 바인딩과 더 많은 유연성과 비슷한 기능을 제공 합니다.

### <a name="with-class-based-model"></a>클래스 기반 모델 사용

자세한 내용은 [클래스 기반 모델](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) 을 참조 하세요.

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>기존 모델 사용

기존 모델은 c #에서 개발한 Azure Function의 규칙을 따르는 합니다. 잘 모르는 경우 [문서](./functions-dotnet-class-library.md)에서 배울 수 있습니다.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>특성 `[SignalRParameter]` 을 사용 하 여 단순화 `ParameterNames`

이를 사용 하는 것이 다소 복잡 하므로 `ParameterNames` `SignalRParameter` 동일한 용도를 얻기 위해가 제공 됩니다.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

C # 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

다음은 Python 코드입니다.

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>구성

### <a name="signalrtrigger"></a>SignalRTrigger

다음 표에서는 *function.json* 파일 및 `SignalRTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type**| 해당 없음 | `SignalRTrigger`로 설정해야 합니다.|
|**direction**| 해당 없음 | `in`로 설정해야 합니다.|
|**name**| 해당 없음 | 트리거 호출 컨텍스트 개체에 대 한 함수 코드에 사용 되는 변수 이름입니다. |
|**hubName**|**HubName**| 이 값은 트리거할 함수의 SignalR hub 이름으로 설정 해야 합니다.|
|**category**|**범주**| 이 값은 트리거되는 함수에 대 한 메시지 범주로 설정 되어야 합니다. 범주는 다음 값 중 하나일 수 있습니다. <ul><li>**연결**: *연결* 및 *연결이 끊어진* 이벤트 포함</li><li>**메시지**: *연결* 범주에 있는 이벤트를 제외한 다른 모든 이벤트 포함</li></ul> |
|**event**|**이벤트**| 이 값은 함수가 트리거될 메시지의 이벤트로 설정 되어야 합니다. *메시지* 범주의 경우 이벤트는 클라이언트에서 보내는 [호출 메시지](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) 의 *대상* 입니다. *연결* 범주에 대해서는 *연결* 된 연결 및 연결 *끊김* 만 사용 됩니다. |
|**parameterNames**|**ParameterNames**| 필드 매개 변수에 바인딩되는 이름 목록입니다. |
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 연결 문자열("AzureSignalRConnectionString"에 대한 기본값)을 포함하는 앱 설정의 이름 |

## <a name="payload"></a>페이로드

트리거 입력 형식은 `InvocationContext` 또는 사용자 지정 형식으로 선언됩니다. 선택 하 `InvocationContext` 는 경우 요청 콘텐츠에 대 한 전체 액세스 권한을 얻습니다. 사용자 지정 형식의 경우 런타임은 JSON 요청 본문의 구문을 분석하여 개체 속성을 설정하려고 합니다.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext는 SignalR 서비스에서 보내는 메시지의 모든 콘텐츠를 포함 합니다.

|InvocationContext의 속성 | 설명|
|------------------------------|------------|
|인수| *메시지* 범주에 사용할 수 있습니다. [호출 메시지](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) 의 *인수* 를 포함 합니다.|
|오류| *연결* 되지 않은 이벤트에 사용할 수 있습니다. 오류가 없는 연결을 닫거나 오류 메시지를 포함 하는 경우 비워 둘 수 있습니다.|
|허브| 메시지가 속한 허브 이름입니다.|
|Category| 메시지의 범주입니다.|
|이벤트| 메시지의 이벤트입니다.|
|ConnectionId| 메시지를 보내는 클라이언트의 연결 ID입니다.|
|UserId| 메시지를 보내는 클라이언트의 사용자 id입니다.|
|헤더| 요청의 헤더입니다.|
|쿼리| 클라이언트가 서비스에 연결 하는 경우 요청에 대 한 쿼리입니다.|
|클레임| 클라이언트의 클레임입니다.|

## <a name="using-parameternames"></a>`ParameterNames` 사용

의 속성 `ParameterNames` 을 `SignalRTrigger` 사용 하면 호출 메시지의 인수를 함수의 매개 변수에 바인딩할 수 있습니다. 이렇게 하면의 인수에 보다 편리 하 게 액세스할 수 있습니다 `InvocationContext` .

`broadcast`두 개의 인수를 사용 하 여 Azure Function에서 메서드를 호출 하려고 하는 JavaScript SignalR client가 있다고 가정해 보겠습니다.

```javascript
await connection.invoke("broadcast", message1, message2);
```

을 사용 하 여 매개 변수에서 이러한 두 인수에 액세스할 수 있을 뿐만 아니라 매개 변수의 형식을 할당할 수 있습니다 `ParameterNames` .

### <a name="remarks"></a>설명

매개 변수 바인딩의 경우 순서가 중요 합니다. 를 사용 하는 경우 `ParameterNames` 의 순서는 `ParameterNames` 클라이언트에서 호출 하는 인수의 순서와 일치 합니다. C #에서 특성을 사용 하는 경우 `[SignalRParameter]` Azure 함수 메서드에서 인수의 순서는 클라이언트의 인수 순서와 일치 합니다.

`ParameterNames` 및 특성은 동시 `[SignalRParameter]` 에 사용할 수 **없습니다** . 그렇지 않으면 예외가 발생 합니다.

## <a name="send-messages-to-signalr-service-trigger-binding"></a>SignalR Service 트리거 바인딩에 메시지 보내기

Azure Function은 SignalR Service 트리거 바인딩의 URL을 생성 하 고 다음과 같이 형식이 지정 됩니다.

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

는 `API_KEY` Azure Function에 의해 생성 됩니다. `API_KEY`SignalR Service 트리거 바인딩을 사용 하는 Azure Portal에서를 가져올 수 있습니다.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API 키":::

SignalR Service의 업스트림 설정에서이 URL을 설정 해야 합니다 `UrlTemplate` .

## <a name="next-steps"></a>다음 단계

* [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](../azure-signalr/signalr-concept-serverless-development-config.md)
* [SignalR Service 트리거 바인딩 샘플](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)
