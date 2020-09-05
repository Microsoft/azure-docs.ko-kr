---
title: Azure Functions에 대 한 Azure Service Bus 트리거
description: Azure Service Bus 메시지가 만들어질 때 Azure 함수를 실행 하는 방법을 알아봅니다.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 262a6612c50148232e814befc76707989befb18b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212138"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Functions에 대 한 Azure Service Bus 트리거

Service Bus 트리거를 사용하여 Service Bus 큐 또는 토픽의 메시지에 응답합니다. 확장 버전 3.1.0부터 세션 사용 큐 또는 토픽을 트리거할 수 있습니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-service-bus-output.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 [메시지 메타데이터](#message-metadata)를 읽고 Service Bus 큐 메시지를 기록하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 Service Bus 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 [메시지 메타데이터](#message-metadata)를 읽고 Service Bus 큐 메시지를 기록합니다.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 Service Bus 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 [메시지 메타데이터](#message-metadata)를 읽고 Service Bus 큐 메시지를 기록합니다. 

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

# <a name="python"></a>[Python](#tab/python)

다음 예에서는 트리거를 통해 Service Bus 큐 메시지를 읽는 방법을 보여 줍니다.

Service Bus 바인딩은 *형식이* 로 설정 된 *function.js* 에 정의 됩니다 `serviceBusTrigger` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

* _ \_ _ \_ Py* 의 코드는 매개 변수를로 선언 합니다 `func.ServiceBusMessage` . 그러면 함수에서 큐 메시지를 읽을 수 있습니다.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

다음 Java 함수는 `@ServiceBusQueueTrigger` [java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime) 의 주석을 사용 하 여 Service Bus 큐 트리거의 구성을 설명 합니다. 함수는 큐에 배치 된 메시지를 가져와 하 고 로그에 추가 합니다.

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

Service Bus 토픽에 메시지가 추가 될 때도 Java 함수를 트리거할 수 있습니다. 다음 예에서는 주석을 사용 하 여 `@ServiceBusTopicTrigger` 트리거 구성을 설명 합니다.

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

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서는 다음 특성을 사용하여 Service Bus 트리거를 구성합니다.

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  특성의 생성자는 큐의 이름 또는 토픽과 구독을 사용합니다. Azure Functions 버전 1.x에서는 연결의 액세스 권한을 지정할 수도 있습니다. 액세스 권한을 지정하지 않으면 기본값은 `Manage`입니다. 자세한 내용은 [트리거 - 구성](#configuration) 섹션을 참조하세요.

  문자열 매개 변수와 함께 사용되는 특성을 보여주는 예제는 다음과 같습니다.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  `Connection`속성이 정의 되어 있지 않으므로 함수는 `AzureWebJobsServiceBus` Service Bus 연결 문자열의 기본 이름인 이라는 앱 설정을 찾습니다. `Connection`다음 예제와 같이 속성을 설정 하 여 사용할 Service Bus 연결 문자열을 포함 하는 응용 프로그램 설정의 이름을 지정할 수도 있습니다.

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  전체 예제는 [트리거-예제](#example)를 참조 하세요.

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueTrigger`주석을 사용 하면 Service Bus 큐 메시지를 만들 때 실행 되는 함수를 만들 수 있습니다. 사용 가능한 구성 옵션에는 큐 이름 및 연결 문자열 이름이 있습니다.

`ServiceBusTopicTrigger`주석을 사용 하면 함수를 트리거하는 데이터를 대상으로 하는 토픽 및 구독을 지정할 수 있습니다.

자세한 내용은 트리거 [예제](#example) 를 참조 하십시오.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `ServiceBusTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "serviceBusTrigger"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "in"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐 또는 토픽 메시지를 나타내는 변수의 이름입니다. |
|**queueName**|**QueueName**|모니터링할 큐의 이름입니다.  토픽이 아닌 큐를 모니터링하는 경우에만 설정합니다.
|**topicName**|**TopicName**|모니터링할 토픽의 이름입니다. 큐가 아닌 토픽을 모니터링하는 경우에만 설정합니다.|
|**subscriptionName**|**SubscriptionName**|모니터링할 구독의 이름입니다. 큐가 아닌 토픽을 모니터링하는 경우에만 설정합니다.|
|**connection**|**연결**|이 바인딩에 사용할 Service Bus 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 이름의 나머지만을 지정할 수 있습니다. 예를 들어을 `connection` "MyServiceBus"로 설정 하는 경우 함수 런타임은 이름이 "AzureWebJobsMyServiceBus" 인 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 "AzureWebJobsServiceBus"라는 앱 설정에서 기본 Service Bus 연결 문자열을 사용합니다.<br><br>연결 문자열을 얻으려면 [관리 자격 증명 가져오기](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)에 나온 단계를 따릅니다. 연결 문자열은 Service Bus 네임스페이스에 대한 것이어야 하며, 특정 큐 또는 항목으로 제한되지 않습니다. |
|**accessRights**|**Access**|연결 문자열에 대한 액세스 권한입니다. 사용 가능한 값은 `manage` 및 `listen`입니다. 기본값은 `manage`이며, `connection`에 **관리** 권한이 있음을 의미합니다. **관리** 권한이 없는 연결 문자열을 사용하는 경우 `accessRights`을 "listen"으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업 시도를 실패할 수 있습니다. Azure Functions 버전 2.x 이상에서는 Service Bus SDK의 최신 버전이 관리 작업을 지원 하지 않으므로이 속성을 사용할 수 없습니다.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`[세션 인식](../service-bus-messaging/message-sessions.md) 큐 또는 구독에 연결 하는 경우 `false` 그렇지 않으면 기본값인입니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

큐 또는 토픽 메시지에 사용할 수 있는 매개 변수 유형은 다음과 같습니다.

* `string` - 메시지가 텍스트인 경우
* `byte[]` - 이진 데이터에 유용합니다.
* 사용자 지정 형식 - 메시지에 JSON이 포함된 경우 Azure Functions는 JSON 데이터를 역직렬화하려고 합니다.
* `BrokeredMessage` - [BrokeredMessage \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 메서드를 사용 하 여 deserialize 된 메시지를 제공 합니다.
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet) -메시지 컨테이너에서 메시지를 수신 하 고 승인 하는 데 사용 [`autoComplete`](functions-bindings-service-bus-output.md#hostjson-settings) 됩니다 (가로 설정 된 경우 필수 `false` ).

이러한 매개 변수 형식은 Azure Functions 버전 1.x에 대 한 것입니다. 2.x 이상에서는 대신을 사용 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) `BrokeredMessage` 합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

큐 또는 토픽 메시지에 사용할 수 있는 매개 변수 유형은 다음과 같습니다.

* `string` - 메시지가 텍스트인 경우
* `byte[]` - 이진 데이터에 유용합니다.
* 사용자 지정 형식 - 메시지에 JSON이 포함된 경우 Azure Functions는 JSON 데이터를 역직렬화하려고 합니다.
* `BrokeredMessage` - [BrokeredMessage \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 메서드를 사용 하 여 deserialize 된 메시지를 제공 합니다.

이러한 매개 변수는 Azure Functions 버전 1.x에 대 한 것입니다. 2.x 이상에서는 대신을 사용 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) `BrokeredMessage` 합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

을 사용 하 여 큐 또는 토픽 메시지에 액세스 `context.bindings.<name from function.json>` 합니다. Service Bus 메시지가 문자열 또는 JSON 개체로 함수에 전달됩니다.

# <a name="python"></a>[Python](#tab/python)

큐 메시지는 함수에서로 형식화 된 매개 변수를 통해 사용할 수 있습니다 `func.ServiceBusMessage` . Service Bus 메시지가 문자열 또는 JSON 개체로 함수에 전달됩니다.

# <a name="java"></a>[Java](#tab/java)

들어오는 Service Bus 메시지는 `ServiceBusQueueMessage` 또는 매개 변수를 통해 사용할 수 있습니다 `ServiceBusTopicMessage` .

[자세한 내용은 예제를 참조](#example)하세요.

---

## <a name="poison-messages"></a>포이즌 메시지

포이즌 메시지 처리는 Azure Functions에서 제어되거나 구성될 수 없습니다. Service Bus는 스스로 포이즌 메시지를 처리합니다.

## <a name="peeklock-behavior"></a> PeekLock 동작

Functions 런타임은 [PeekLock 모드](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)로 메시지를 수신합니다. 함수가 성공적으로 완료된 경우 메시지에서 `Complete`를 호출하고, 함수가 실패한 경우 `Abandon`을 호출합니다. 함수가 `PeekLock` 시간 제한보다 오래 실행되는 경우 함수가 실행되면 잠금이 자동으로 갱신됩니다. 

`maxAutoRenewDuration`은 [OnMessageOptions.MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)에 매핑되는 *host.json*에서 구성할 수 있습니다. 이 설정에 대해 허용되는 최대값음 Service Bus 설명서에 따라 5분입니다. 반면 함수 제한 시간 기본값은 5분에서 10분으로 늘릴 수 있습니다. Service Bus 함수의 경우 Service Bus 갱신 제한을 초과하기 때문에 이 작업을 하지 않는 것이 좋습니다.

## <a name="message-metadata"></a>메시지 메타 데이터

Service Bus 트리거는 몇 가지 [메타데이터 속성](./functions-bindings-expressions-patterns.md#trigger-metadata)을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이러한 속성은 [Message](/dotnet/api/microsoft.azure.servicebus.message?view=azure-dotnet) 클래스의 멤버입니다.

|속성|형식|설명|
|--------|----|-----------|
|`ContentType`|`string`|응용 프로그램 관련 논리에 대해 보낸 사람 및 수신자가 사용한 콘텐츠 형식 식별자입니다.|
|`CorrelationId`|`string`|상관관계 ID입니다.|
|`DeadLetterSource`|`string`|배달 못한 편지 원본입니다.|
|`DeliveryCount`|`Int32`|배달 수입니다.|
|`EnqueuedTimeUtc`|`DateTime`|큐에 대기된 시간(UTC)입니다.|
|`ExpiresAtUtc`|`DateTime`|만료 시간(UTC)입니다.|
|`Label`|`string`|응용 프로그램 관련 레이블입니다.|
|`MessageId`|`string`|Service Bus에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우).|
|`MessageReceiver`|`MessageReceiver`|메시지 수신자를 Service Bus 합니다. 메시지를 중단, 완료 또는 배달 못한 데 사용할 수 있습니다.|
|`MessageSession`|`MessageSession`|세션을 사용 하는 큐 및 토픽에 대 한 메시지 받는 사람입니다.|
|`ReplyTo`|`string`|큐 주소에 대한 회신입니다.|
|`SequenceNumber`|`long`|Service Bus에 의해 메시지에 할당되는 고유 번호입니다.|
|`To`|`string`|주소로 보내기입니다.|
|`UserProperties`|`IDictionary<string, object>`|보낸 사람에 의해 설정 되는 속성입니다.|

이 아티클의 앞부분에서 이러한 속성을 사용하는 [코드 예제](#example)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Functions에서 Azure Service Bus 메시지 보내기 (출력 바인딩)](./functions-bindings-service-bus-output.md)
