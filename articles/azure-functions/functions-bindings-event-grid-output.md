---
title: Azure Functions에 대한 Azure Event Grid 출력 바인딩
description: Azure Functions에서 Event Grid 이벤트를 보내는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 6bd4d5d82af213063b2000693e46d22744604480
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214112"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions에 대한 Azure Event Grid 출력 바인딩

Event Grid 출력 바인딩을 사용하여 사용자 지정 항목에 이벤트를 씁니다. 유효한 [사용자 지정 항목에 대한 액세스 키](../event-grid/security-authenticate-publishing-clients.md)가 있어야 합니다.

설정 및 구성 세부 정보에 대한 자세한 내용은 [개요](./functions-bindings-event-grid.md)를 참조하세요.

> [!NOTE]
> Event Grid 출력 바인딩은 공유 액세스 서명(SAS 토큰)을 지원하지 않습니다. 항목의 액세스 키를 사용해야 합니다.

> [!IMPORTANT]
> Event Grid 출력 바인딩은 Functions 2.x 이상에서만 사용할 수 있습니다.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 메서드 반환 값을 출력으로 사용하여 Event Grid 사용자 지정 항목에 메시지를 쓰는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

다음 예제에서는 `IAsyncCollector` 인터페이스를 사용하여 일괄 처리 메시지를 보내는 방법을 보여줍니다.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예제에서는 *function.json* 파일에 있는 Event Grid 출력 바인딩 데이터를 보여줍니다.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

단일 이벤트를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

여러 이벤트를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예제에서는 *function.json* 파일에 있는 Event Grid 출력 바인딩 데이터를 보여줍니다.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

단일 이벤트를 만드는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

여러 이벤트를 만드는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여줍니다. 그런 다음에 지정 된 대로 사용자 지정 토픽에 이벤트를 보냅니다 `topicEndpointUri` .

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

을 설정 하 여 사용자 지정 토픽에 이벤트를 보내는 Python 샘플은 다음과 같습니다 `EventGridOutputEvent` .

```python
import logging
import azure.functions as func
import datetime


def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

# <a name="java"></a>[Java](#tab/java)

Java에서는 Event Grid 출력 바인딩을 사용할 수 없습니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)의 경우 [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) 특성을 사용합니다.

특성의 생성자는 사용자 지정 항목 이름이 포함된 앱 설정 이름과 항목 키가 포함된 앱 설정 이름을 사용합니다. 이러한 설정에 대한 자세한 내용은 [출력 - 구성](#configuration)을 참조하세요. `EventGrid` 특성 예제는 다음과 같습니다.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

전체 예제는 [예제](#example)를 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 Event Grid 출력 바인딩을 사용할 수 없습니다.

# <a name="java"></a>[Java](#tab/java)

Java에서는 Event Grid 출력 바인딩을 사용할 수 없습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `EventGrid` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "eventGrid"로 설정해야 합니다. |
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 이벤트를 나타내는 함수 코드에서 사용되는 변수 이름입니다. |
|**topicEndpointUri** |**TopicEndpointUri** | `MyTopicEndpointUri`와 같이 사용자 지정 항목에 대한 URI를 포함하는 앱 설정의 이름입니다. |
|**topicKeySetting** |**TopicKeySetting** | 사용자 지정 항목에 대한 액세스 키가 포함된 앱 설정의 이름입니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> `TopicEndpointUri` 구성 속성의 값을 사용자 지정 항목의 URI가 포함된 앱 설정 이름으로 설정했는지 확인합니다. 이 속성에서 직접 사용자 지정 항목의 URI를 지정하지 마세요.

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

`out EventGridEvent paramName`과 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. 여러 메시지를 쓰려면 `out EventGridEvent` 대신 `ICollector<EventGridEvent>` 또는 `IAsyncCollector<EventGridEvent>`를 사용할 수 있습니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

`out EventGridEvent paramName`과 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. 여러 메시지를 쓰려면 `out EventGridEvent` 대신 `ICollector<EventGridEvent>` 또는 `IAsyncCollector<EventGridEvent>`를 사용할 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>`을 사용하여 출력 이벤트에 액세스합니다. 여기서 `<name>`은 *function.json*의 `name` 속성에 지정된 값입니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 Event Grid 출력 바인딩을 사용할 수 없습니다.

# <a name="java"></a>[Java](#tab/java)

Java에서는 Event Grid 출력 바인딩을 사용할 수 없습니다.

---

## <a name="next-steps"></a>다음 단계

* [Event Grid 이벤트 디스패치](./functions-bindings-event-grid-trigger.md)
