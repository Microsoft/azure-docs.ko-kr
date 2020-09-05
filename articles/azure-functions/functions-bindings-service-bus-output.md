---
title: Azure Functions에 대 한 Azure Service Bus 출력 바인딩
description: Azure Functions에서 Azure Service Bus 메시지를 보내는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 3fc8f205bff52fad6e55b7aa6692ec80ae5e954a
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212153"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Functions에 대 한 Azure Service Bus 출력 바인딩

Azure Service Bus 출력 바인딩을 사용하여 큐 또는 토픽 메시지를 보냅니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-service-bus-output.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 Service Bus 큐 메시지를 보내는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 Service Bus 출력 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 타이머 트리거를 사용하여 15초마다 큐 메시지를 보냅니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

단일 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

여러 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 Service Bus 출력 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 타이머 트리거를 사용하여 15초마다 큐 메시지를 보냅니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

단일 메시지를 만드는 JavaScript 스크립트 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

여러 메시지를 만드는 JavaScript 스크립트 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 Python에서 Service Bus 큐에 쓰는 방법을 보여 줍니다.

Service Bus 바인딩 정의는 *형식이* 로 설정 된 *function.js* 에 정의 됩니다 `serviceBus` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

Py에서는 값을 메서드에 전달 하 여 큐에 메시지를 작성할 수 있습니다 * _ \_ _ \_ *. `set`

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 `myqueue` HTTP 요청에 의해 트리거될 때 Service Bus 큐로 메시지를 보내는 Java 함수를 보여 줍니다.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 [Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Service Bus 큐에 기록될 함수 매개 변수에 대한 `@QueueOutput` 주석을 사용합니다.  매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 T는 POJO의 원시 Java 형식입니다.

Java 함수는 Service Bus 토픽에도 쓸 수 있습니다. 다음 예제에서는 주석을 사용 하 여 `@ServiceBusTopicOutput` 출력 바인딩에 대 한 구성을 설명 합니다. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)를 사용합니다.

특성의 생성자는 큐의 이름 또는 토픽과 구독을 사용합니다. 연결의 액세스 권한을 지정할 수도 있습니다. 액세스 권한을 설정을 선택하는 방법은 [출력 - 구성](#configuration) 섹션에서 설명합니다. 함수의 반환 값에 적용된 특성을 보여주는 예제는 다음과 같습니다.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

`Connection`다음 예제와 같이 사용할 Service Bus 연결 문자열을 포함 하는 앱 설정의 이름을 지정 하도록 속성을 설정할 수 있습니다.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

전체 예제는 [출력-예제](#example)를 참조 하세요.

`ServiceBusAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 사용할 Service Bus 계정을 지정합니다.  자세한 내용은 [트리거 - 특성](functions-bindings-service-bus-trigger.md#attributes-and-annotations)을 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput`및 `ServiceBusTopicOutput` 주석은 함수 출력으로 메시지를 작성 하는 데 사용할 수 있습니다. 이러한 주석을 사용 하 여 데코레이팅된 매개 변수는로 선언 해야 합니다 `OutputBinding<T>` `T` . 여기서은 메시지의 형식에 해당 하는 형식입니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `ServiceBus` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "serviceBus"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐 또는 토픽 메시지를 나타내는 변수의 이름입니다. "$return"으로 설정하여 함수 반환 값을 참조합니다. |
|**queueName**|**QueueName**|큐의 이름입니다.  토픽이 아닌 큐 메시지를 보내는 경우에만 설정합니다.
|**topicName**|**TopicName**|항목의 이름입니다. 큐가 아닌 토픽 메시지를 보내는 경우에만 설정합니다.|
|**connection**|**연결**|이 바인딩에 사용할 Service Bus 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 이름의 나머지만을 지정할 수 있습니다. 예를 들어을 `connection` "MyServiceBus"로 설정 하는 경우 함수 런타임은 이름이 "AzureWebJobsMyServiceBus" 인 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 "AzureWebJobsServiceBus"라는 앱 설정에서 기본 Service Bus 연결 문자열을 사용합니다.<br><br>연결 문자열을 얻으려면 [관리 자격 증명 가져오기](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)에 나온 단계를 따릅니다. 연결 문자열은 Service Bus 네임스페이스에 대한 것이어야 하며, 특정 큐 또는 항목으로 제한되지 않습니다.|
|**accessRights** (v1에만 해당)|**Access**|연결 문자열에 대한 액세스 권한입니다. 사용 가능한 값은 `manage` 및 `listen`입니다. 기본값은 `manage`이며, `connection`에 **관리** 권한이 있음을 의미합니다. **관리** 권한이 없는 연결 문자열을 사용하는 경우 `accessRights`을 "listen"으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업 시도를 실패할 수 있습니다. Azure Functions 버전 2.x 이상에서는 Service Bus SDK의 최신 버전이 관리 작업을 지원 하지 않으므로이 속성을 사용할 수 없습니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용량

Azure Functions 1.x에서 큐가 존재하지 않고 `accessRights`를 `manage`로 설정한 경우 런타임은 큐를 만듭니다. 함수 버전 2.x 이상에서는 큐 또는 항목이 이미 존재 해야 합니다. 존재 하지 않는 큐 또는 토픽을 지정 하면 함수가 실패 합니다. 

# <a name="c"></a>[C#](#tab/csharp)

출력 바인딩에 대해 다음 매개 변수 형식을 사용 합니다.

* `out T paramName` - `T`는 JSON 직렬화 가능 형식일 수 있습니다. 함수가 종료될 때 매개 변수 값이 null이면 함수는 null 개체와 메시지를 만듭니다.
* `out string` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out byte[]` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out BrokeredMessage` -함수가 종료 될 때 매개 변수 값이 null 이면 함수는 (함수 1. x의 경우) 메시지를 만들지 않습니다.
* `out Message` -함수가 종료 될 때 매개 변수 값이 null 이면 함수가 메시지를 만들지 않습니다 (함수 2.x 이상).
* `ICollector<T>` 또는 `IAsyncCollector<T>` (비동기 메서드의 경우)-여러 메시지를 만드는 데 사용할입니다. 메시지는 `Add` 메서드를 호출할 때 생성됩니다.

C # 함수를 사용 하는 경우:

* 비동기 함수에는 매개 변수 대신 반환 값이 필요 `IAsyncCollector` `out` 합니다.

* 세션 ID에 액세스 하려면 형식에 바인딩하고 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 속성을 사용 합니다 `sessionId` .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

출력 바인딩에 대해 다음 매개 변수 형식을 사용 합니다.

* `out T paramName` - `T`는 JSON 직렬화 가능 형식일 수 있습니다. 함수가 종료될 때 매개 변수 값이 null이면 함수는 null 개체와 메시지를 만듭니다.
* `out string` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out byte[]` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out BrokeredMessage` -함수가 종료 될 때 매개 변수 값이 null 이면 함수는 (함수 1. x의 경우) 메시지를 만들지 않습니다.
* `out Message` -함수가 종료 될 때 매개 변수 값이 null 이면 함수가 메시지를 만들지 않습니다 (함수 2.x 이상).
* `ICollector<T>` 또는 `IAsyncCollector<T>` - 여러 개의 메시지를 만들려는 경우. 메시지는 `Add` 메서드를 호출할 때 생성됩니다.

C # 함수를 사용 하는 경우:

* 비동기 함수에는 매개 변수 대신 반환 값이 필요 `IAsyncCollector` `out` 합니다.

* 세션 ID에 액세스 하려면 형식에 바인딩하고 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 속성을 사용 합니다 `sessionId` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

을 사용 하 여 큐 또는 토픽에 액세스 `context.bindings.<name from function.json>` 합니다. 문자열, 바이트 배열 또는 JavaScript 개체 (JSON으로 deserialize)를에 할당할 수 있습니다 `context.binding.<name>` .

# <a name="python"></a>[Python](#tab/python)

기본 제공 출력 바인딩이 아닌 [AZURE SERVICE BUS SDK](../service-bus-messaging/index.yml) 를 사용 합니다.

# <a name="java"></a>[Java](#tab/java)

기본 제공 출력 바인딩이 아닌 [AZURE SERVICE BUS SDK](../service-bus-messaging/index.yml) 를 사용 합니다.

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참조 |
|---|---|
| Service Bus | [Service Bus 오류 코드](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Service Bus 한도](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x 이상에서이 바인딩에 사용할 수 있는 전역 구성 설정에 대해 설명 합니다. 아래 파일에 host.js예제에는이 바인딩에 대 한 설정만 포함 되어 있습니다. 전역 구성 설정에 대 한 자세한 내용은 [ Azure Functions 버전에 대 한 참조host.js](functions-host-json.md)를 참조 하세요.

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

`isSessionsEnabled`을로 설정한 경우 `true` 이 적용 됩니다 `sessionHandlerOptions` .  `isSessionsEnabled`을로 설정한 경우 `false` 이 적용 됩니다 `messageHandlerOptions` .

|속성  |기본값 | Description |
|---------|---------|---------|
|prefetchCount|0|메시지 수신자가 동시에 요청할 수 있는 메시지 수를 가져오거나 설정 합니다.|
|maxAutoRenewDuration|00:05:00|메시지 잠금이 자동으로 갱신되는 최대 기간입니다.|
|autoComplete|true|트리거가 처리 후 자동으로 완료를 호출 해야 하는지, 아니면 함수 코드가 수동으로 complete를 호출 하는지 여부입니다.<br><br>을로 설정 하 `false` 는 것은 c # 에서만 지원 됩니다.<br><br>로 설정 `true` 된 경우 함수 실행이 성공적으로 완료 되 면 트리거가 자동으로 메시지를 완료 하 고, 그렇지 않으면 메시지를 무시 합니다.<br><br>로 설정 하면 `false` [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet) 메서드를 호출 하 여 메시지를 완료 하거나 중단 하거나 배달 못한 편지를 배달 해야 합니다. 예외를 throw 하 고 메서드를 호출 하지 않은 경우에 `MessageReceiver` 는 잠금이 유지 됩니다. 잠금이 만료 되 면 메시지는 증가 된로 다시 큐에 대기 되며 `DeliveryCount` 잠금이 자동으로 갱신 됩니다.<br><br>비 C # 함수에서 함수의 예외는 백그라운드에서 런타임 호출을 발생 합니다 `abandonAsync` . 예외가 발생 하지 않으면 `completeAsync` 가 백그라운드에서 호출 됩니다. |
|maxConcurrentCalls|16|메시지 펌프가 확장 인스턴스당 시작 해야 하는 콜백에 대 한 최대 동시 호출 수입니다. 기본적으로 함수 런타임은 여러 개의 메시지를 동시에 처리합니다.|
|maxConcurrentSessions|2000|크기 조정 된 인스턴스당 동시에 처리할 수 있는 최대 세션 수입니다.|

## <a name="next-steps"></a>다음 단계

- [Service Bus 큐 또는 토픽 메시지가 생성 될 때 함수 실행 (트리거)](./functions-bindings-service-bus-trigger.md)
