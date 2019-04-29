---
title: Azure Functions의 Azure Service Bus 바인딩
description: Azure Functions에서 Azure Service Bus 트리거 및 바인딩을 사용하는 방법을 파악합니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버리스 아키텍처
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: e1cd7d9e135f5e3196f02237076c5c8069048fb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442735"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions의 Azure Service Bus 바인딩

이 문서에서는 Azure Functions에서 Azure Service Bus 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 Service Bus 큐 및 토픽에 대한 트리거 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Service Bus 바인딩은 [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 패키지 버전 2.x에서 제공됩니다. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

Service Bus 바인딩은 [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>트리거

Service Bus 트리거를 사용하여 Service Bus 큐 또는 토픽의 메시지에 응답합니다. 

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [C#](#trigger---c-example)
* [C# 스크립트(.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>트리거 - C# 예제

다음 예제에서는 [메시지 메타데이터](#trigger---message-metadata)를 읽고 Service Bus 큐 메시지를 기록하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

이 예제는 Azure Functions 버전 1.x에 대한 것입니다. 이 코드를 2.x에 대해 작동하도록 하려면:

- [액세스 권한 매개 변수 생략](#trigger---configuration)
- 로그 매개 변수의 유형을 `TraceWriter`에서 `ILogger`로 변경
- `log.Info`에서 `log.LogInformation`으로 변경

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예에서는 *function.json* 파일의 Service Bus 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 [메시지 메타데이터](#trigger---message-metadata)를 읽고 Service Bus 큐 메시지를 기록합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>트리거 - F# 예제

다음 예에서는 *function.json* 파일의 Service Bus 트리거 바인딩 및 바인딩을 사용하는 [F# 함수](functions-reference-fsharp.md)를 보여줍니다. 이 함수는 Service Bus 큐 메시지를 기록합니다. 

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

F# 스크립트 코드는 다음과 같습니다.

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>트리거 - Java 예제

다음 Java 함수를 사용 하는 `@ServiceBusQueueTrigger` 주석을 합니다 [Java 런타임 라이브러리 함수](/java/api/overview/azure/functions/runtime) Service Bus 큐 트리거에 대 한 구성을 설명 하 합니다. 함수는 큐에 배치 된 메시지를 가져와 로그에 추가 합니다.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Service Bus 토픽에 메시지 추가 되 면 Java 함수를 트리거할 수도 있습니다. 다음 예제에서는 `@ServiceBusTopicTrigger` 트리거 구성을 설명 하는 주석입니다.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>트리거 - JavaScript 예제

다음 예에서는 *function.json* 파일의 Service Bus 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 [메시지 메타데이터](#trigger---message-metadata)를 읽고 Service Bus 큐 메시지를 기록합니다. 

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

JavaScript 스크립트 코드는 다음과 같습니다.

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

## <a name="trigger---attributes"></a>트리거 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서는 다음 특성을 사용하여 Service Bus 트리거를 구성합니다.

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  특성의 생성자는 큐의 이름 또는 토픽과 구독을 사용합니다. Azure Functions 버전 1.x에서는 연결의 액세스 권한을 지정할 수도 있습니다. 액세스 권한을 지정하지 않으면 기본값은 `Manage`입니다. 자세한 내용은 [트리거 - 구성](#trigger---configuration) 섹션을 참조하세요.

  문자열 매개 변수와 함께 사용되는 특성을 보여주는 예제는 다음과 같습니다.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  다음 예와 같이 사용할 Service Bus 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  전체 예제는 [트리거 - C# 예제](#trigger---c-example)를 참조하세요.

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  사용할 Service Bus 계정을 지정하는 다른 방법을 제공합니다. 생성자는 Service Bus 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 매개 변수, 메서드 또는 클래스 수준에서 특성을 적용할 수 있습니다. 다음 예제에서는 클래스 수준 및 메서드 수준을 보여줍니다.

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

사용할 Service Bus 계정은 다음과 같은 순서로 결정됩니다.

* `ServiceBusTrigger` 특성의 `Connection` 속성
* `ServiceBusTrigger` 특성과 동일한 매개 변수에 적용된 `ServiceBusAccount` 특성
* 함수에 적용된 `ServiceBusAccount` 특성
* 클래스에 적용된 `ServiceBusAccount` 특성
* "AzureWebJobsServiceBus" 앱 설정입니다.

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `ServiceBusTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | "serviceBusTrigger"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "in"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐 또는 토픽 메시지를 나타내는 변수의 이름입니다. "$return"으로 설정하여 함수 반환 값을 참조합니다. |
|**queueName**|**QueueName**|모니터링할 큐의 이름입니다.  토픽이 아닌 큐를 모니터링하는 경우에만 설정합니다.
|**topicName**|**TopicName**|모니터링할 토픽의 이름입니다. 큐가 아닌 토픽을 모니터링하는 경우에만 설정합니다.|
|**subscriptionName**|**SubscriptionName**|모니터링할 구독의 이름입니다. 큐가 아닌 토픽을 모니터링하는 경우에만 설정합니다.|
|**연결**|**연결**|이 바인딩에 사용할 Service Bus 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyServiceBus"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyServiceBus"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 "AzureWebJobsServiceBus"라는 앱 설정에서 기본 Service Bus 연결 문자열을 사용합니다.<br><br>연결 문자열을 얻으려면 [관리 자격 증명 가져오기](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)에 나온 단계를 따릅니다. 연결 문자열은 Service Bus 네임스페이스에 대한 것이어야 하며, 특정 큐 또는 항목으로 제한되지 않습니다. |
|**accessRights**|**Access**|연결 문자열에 대한 액세스 권한입니다. 사용 가능한 값은 `manage` 및 `listen`입니다. 기본값은 `manage`이며, `connection`에 **관리** 권한이 있음을 의미합니다. **관리** 권한이 없는 연결 문자열을 사용하는 경우 `accessRights`을 "listen"으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업 시도를 실패할 수 있습니다. Azure Functions 버전 2.x에서는 최신 버전의 Storage SDK가 관리 작업을 지원하지 않으므로 이 속성을 사용할 수 없습니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>트리거 - 사용

C# 및 C# 스크립트에서 큐 또는 토픽 메시지에 대해 다음 매개 변수 형식을 사용할 수 있습니다.

* `string` - 메시지가 텍스트인 경우
* `byte[]` - 이진 데이터에 유용합니다.
* 사용자 지정 형식 - 메시지에 JSON이 포함된 경우 Azure Functions는 JSON 데이터를 deserialize하려고 합니다.
* `BrokeredMessage` - [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 메서드를 사용하는 deserialize된 메시지가 표시됩니다.

이러한 매개 변수는 Azure Functions 버전 1.x용이므로 2.x의 경우 `BrokeredMessage` 대신 [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)를 사용합니다.

JavaScript에서 `context.bindings.<name from function.json>`를 사용하여 큐 또는 토픽 메시지에 액세스합니다. Service Bus 메시지가 문자열 또는 JSON 개체로 함수에 전달됩니다.

## <a name="trigger---poison-messages"></a>트리거 - 포이즌 메시지

포이즌 메시지 처리는 Azure Functions에서 제어되거나 구성될 수 없습니다. Service Bus는 스스로 포이즌 메시지를 처리합니다.

## <a name="trigger---peeklock-behavior"></a>트리거 - PeekLock 동작

Functions 런타임은 [PeekLock 모드](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)로 메시지를 수신합니다. 함수가 성공적으로 완료된 경우 메시지에서 `Complete`를 호출하고, 함수가 실패한 경우 `Abandon`을 호출합니다. 함수가 `PeekLock` 시간 제한보다 오래 실행되는 경우 함수가 실행되면 잠금이 자동으로 갱신됩니다. 

`maxAutoRenewDuration`은 [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)에 매핑되는 *host.json*에서 구성할 수 있습니다. 이 설정에 대해 허용되는 최대값음 Service Bus 설명서에 따라 5분입니다. 반면 함수 제한 시간 기본값은 5분에서 10분으로 늘릴 수 있습니다. Service Bus 함수의 경우 Service Bus 갱신 제한을 초과하기 때문에 이 작업을 하지 않는 것이 좋습니다.

## <a name="trigger---message-metadata"></a>트리거 - 메시지 메타데이터

Service Bus 트리거는 몇 가지 [메타데이터 속성](./functions-bindings-expressions-patterns.md#trigger-metadata)을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 클래스의 속성은 다음과 같습니다.

|자산|Type|설명|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|배달 수입니다.|
|`DeadLetterSource`|`string`|배달 못한 편지 원본입니다.|
|`ExpiresAtUtc`|`DateTime`|만료 시간(UTC)입니다.|
|`EnqueuedTimeUtc`|`DateTime`|큐에 대기된 시간(UTC)입니다.|
|`MessageId`|`string`|Service Bus에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우).|
|`ContentType`|`string`|애플리케이션별 논리의 보낸 사람 및 받는 사람에 의해 활용되는 콘텐츠 형식 식별자입니다.|
|`ReplyTo`|`string`|큐 주소에 대한 회신입니다.|
|`SequenceNumber`|`Int64`|Service Bus에 의해 메시지에 할당되는 고유 번호입니다.|
|`To`|`string`|주소로 보내기입니다.|
|`Label`|`string`|애플리케이션별 레이블입니다.|
|`CorrelationId`|`string`|상관관계 ID입니다.|

> [!NOTE]
> 현재 트리거는 세션을 사용하지 않는 큐 및 구독에 대해서만 작동합니다. 이 기능에 대한 추가 업데이트는 [이 기능 항목](https://github.com/Azure/azure-functions-host/issues/563)을 추적하세요. 

이 아티클의 앞부분에서 이러한 속성을 사용하는 [코드 예제](#trigger---example)를 참조하세요.

## <a name="trigger---hostjson-properties"></a>트리거 - host.json 속성

[host.json](functions-host-json.md#servicebus) 파일에는 Service Bus 트리거 동작을 제어하는 설정이 포함됩니다.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------|
|maxConcurrentCalls|16|메시지 펌프가 시작되어야 하는 콜백에 대한 최대 동시 호출 수입니다. 기본적으로 함수 런타임은 여러 개의 메시지를 동시에 처리합니다. 런타임이 큐 또는 토픽 메시지를 한 번에 하나만 처리하도록 하려면, `maxConcurrentCalls`를 1로 설정합니다. |
|prefetchCount|해당 없음|기본 MessageReceiver에서 사용할 기본 PrefetchCount입니다.|
|maxAutoRenewDuration|00:05:00|메시지 잠금이 자동으로 갱신되는 최대 기간입니다.|

## <a name="output"></a>출력

Azure Service Bus 출력 바인딩을 사용하여 큐 또는 토픽 메시지를 보냅니다.

## <a name="output---example"></a>출력 - 예제

언어 관련 예제를 참조하세요.

* [C#](#output---c-example)
* [C# 스크립트(.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>출력 - C# 예제

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

### <a name="output---c-script-example"></a>출력 - C# 스크립트 예제

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
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>출력 - F# 예제

다음 예에서는 *function.json* 파일의 Service Bus 출력 바인딩 및 바인딩을 사용하는 [F# 스크립트 함수](functions-reference-fsharp.md)를 보여줍니다. 함수는 타이머 트리거를 사용하여 15초마다 큐 메시지를 보냅니다.

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

단일 메시지를 만드는 F# 스크립트 코드는 다음과 같습니다.

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>출력 - Java 예제

다음 예제에서는 HTTP 요청으로 트리거될 때 `myqueue` Service Bus 큐에 메시지를 보내는 Java 함수를 보여 줍니다.

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

Java 함수는 Service Bus 토픽에 쓸 수도 있습니다. 다음 예제에서는 `@ServiceBusTopicOutput` 출력 바인딩에 대 한 구성을 설명 하는 주석입니다. 

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

### <a name="output---javascript-example"></a>출력 - JavaScript 예제

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

## <a name="output---attributes"></a>출력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)를 사용합니다.

특성의 생성자는 큐의 이름 또는 토픽과 구독을 사용합니다. 연결의 액세스 권한을 지정할 수도 있습니다. 액세스 권한을 설정을 선택하는 방법은 [출력 - 구성](#output---configuration) 섹션에서 설명합니다. 함수의 반환 값에 적용된 특성을 보여주는 예제는 다음과 같습니다.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

다음 예와 같이 사용할 Service Bus 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

전체 예제는 [출력 - C# 예제](#output---c-example)를 참조하세요.

`ServiceBusAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 사용할 Service Bus 계정을 지정합니다.  자세한 내용은 [트리거 - 특성](#trigger---attributes)을 참조하세요.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일 및 `ServiceBus` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | "serviceBus"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐 또는 토픽을 나타내는 변수의 이름입니다. "$return"으로 설정하여 함수 반환 값을 참조합니다. |
|**queueName**|**QueueName**|큐의 이름입니다.  토픽이 아닌 큐 메시지를 보내는 경우에만 설정합니다.
|**topicName**|**TopicName**|모니터링할 토픽의 이름입니다. 큐가 아닌 토픽 메시지를 보내는 경우에만 설정합니다.|
|**연결**|**연결**|이 바인딩에 사용할 Service Bus 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyServiceBus"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyServiceBus"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 "AzureWebJobsServiceBus"라는 앱 설정에서 기본 Service Bus 연결 문자열을 사용합니다.<br><br>연결 문자열을 얻으려면 [관리 자격 증명 가져오기](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)에 나온 단계를 따릅니다. 연결 문자열은 Service Bus 네임스페이스에 대한 것이어야 하며, 특정 큐 또는 항목으로 제한되지 않습니다.|
|**accessRights**|**Access**|연결 문자열에 대한 액세스 권한입니다. 사용 가능한 값은 `manage` 및 `listen`입니다. 기본값은 `manage`이며, `connection`에 **관리** 권한이 있음을 의미합니다. **관리** 권한이 없는 연결 문자열을 사용하는 경우 `accessRights`을 "listen"으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업 시도를 실패할 수 있습니다. Azure Functions 버전 2.x에서는 최신 버전의 Storage SDK가 관리 작업을 지원하지 않으므로 이 속성을 사용할 수 없습니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>출력 - 사용

Azure Functions 1.x에서 큐가 존재하지 않고 `accessRights`를 `manage`로 설정한 경우 런타임은 큐를 만듭니다. Functions 버전 2.x에서는 큐 또는 토픽이 이미 있어야 합니다. 존재하지 않는 큐 또는 토픽을 지정하면 함수가 실패합니다. 

C# 및 C# 스크립트에서 출력 바인딩에 대해 다음 매개 변수 형식을 사용할 수 있습니다.

* `out T paramName` - `T`는 JSON 직렬화 가능 형식일 수 있습니다. 함수가 종료될 때 매개 변수 값이 null이면 함수는 null 개체와 메시지를 만듭니다.
* `out string` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out byte[]` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `out BrokeredMessage` - 함수가 종료될 때 매개 변수 값이 null인 경우 함수는 메시지를 만들지 않습니다.
* `ICollector<T>` 또는 `IAsyncCollector<T>` - 여러 개의 메시지를 만들려는 경우. 메시지는 `Add` 메서드를 호출할 때 생성됩니다.

비동기 함수에서는 `out` 매개 변수 대신, 반환 값 또는 `IAsyncCollector`를 사용합니다.

이러한 매개 변수는 Azure Functions 버전 1.x용이므로 2.x의 경우 `BrokeredMessage` 대신 [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)를 사용합니다.

JavaScript에서 `context.bindings.<name from function.json>`를 사용하여 큐 또는 토픽에 액세스합니다. 문자열, 바이트 배열 또는 Javascript 개체(JSON으로 deserialize됨)를 `context.binding.<name>`에 할당할 수 있습니다.

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참조 |
|---|---|
| Service Bus | [Service Bus 오류 코드](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus 한도](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x에서 이 바인딩에 사용할 수 있는 글로벌 구성 설정을 설명합니다. 아래 예제 host.json 파일에는 이 바인딩에 대한 버전 2.x 설정만 포함되어 있습니다. 버전 2.x의 글로벌 구성 설정에 대한 자세한 내용은 [Azure Functions 버전 2.x에 대한 host.json 참조](functions-host-json.md)를 참조하세요.

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|메시지 잠금이 자동으로 갱신되는 최대 기간입니다.|
|autoComplete|true|트리거에서 즉시 완료(자동 완성)로 표시해야 할지 처리가 완료될 때까지 기다려야 하는지 여부입니다.|
|maxConcurrentCalls|16|메시지 펌프가 시작되어야 하는 콜백에 대한 최대 동시 호출 수입니다. 기본적으로 함수 런타임은 여러 개의 메시지를 동시에 처리합니다. 런타임이 큐 또는 토픽 메시지를 한 번에 하나만 처리하도록 하려면, `maxConcurrentCalls`를 1로 설정합니다. |
|prefetchCount|해당 없음|기본 MessageReceiver에서 사용할 기본 PrefetchCount입니다.|


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
