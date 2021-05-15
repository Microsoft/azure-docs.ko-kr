---
title: 지속성 함수의 바인딩 - Azure
description: Azure Functions의 Durable Functions 확장에 트리거 및 바인딩을 사용하는 방법입니다.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 26c9da85b27ba46e9c48bdc7872a65d452a7acb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607096"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>지속성 함수의 바인딩(Azure Functions)

[지속성 함수](durable-functions-overview.md) 확장은 오케스트레이터 및 작업 함수의 실행을 제어하는 새로운 두 가지 트리거 바인딩을 도입하고 있습니다. 또한 지속성 함수 런타임에 대한 클라이언트 역할을 하는 출력 바인딩도 도입하고 있습니다.

## <a name="orchestration-trigger"></a>오케스트레이션 트리거

오케스트레이션 트리거를 사용하여 [지속성 오케스트레이터 함수](durable-functions-types-features-overview.md#orchestrator-functions)를 작성할 수 있습니다. 이 트리거는 새 오케스트레이터 함수 인스턴스를 시작하고 작업을 "대기 중인" 기존의 오케스트레이터 함수 인스턴스를 다시 시작할 수 있도록 지원합니다.

Azure Functions에 Visual Studio 도구를 사용하는 경우 오케스트레이션 트리거는 [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .NET 특성을 사용하여 구성됩니다.

스크립팅 언어(예: JavaScript 또는 C# 스크립팅)에서 오케스트레이터 함수를 작성하는 경우 오케스트레이션 트리거는 *function.json* 파일의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`은 이 오케스트레이터 함수의 새 인스턴스를 시작하려고 할 때 클라이언트에서 사용해야 하는 오케스트레이션 이름입니다. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 스토리지 계정에 있는 일련의 큐를 폴링합니다. 이러한 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 오케스트레이션 트리거에 대한 몇 가지 참고 사항입니다.

* **단일 스레드** - 단일 디스패처 스레드는 단일 호스트 인스턴스에서 모든 오케스트레이터 함수를 실행하는 데 사용됩니다. 이러한 이유로 오케스트레이터 함수 코드가 효율적이고 모든 I/O를 수행하지 않도록 하는 것이 중요합니다. 또한 이 스레드에서 지속성 함수 관련 작업 종류를 기다리는 경우를 제외하고는 비동기 작업을 수행하지 않도록 하는 것이 중요합니다.
* **포이즌 메시지 처리** - 오케스트레이션 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 오케스트레이션 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값은 JSON으로 직렬화되고 Azure Table Storage의 오케스트레이션 기록 테이블에 유지됩니다. 이러한 반환 값은 나중에 설명할 오케스트레이션 클라이언트 바인딩을 통해 쿼리할 수 있습니다.

> [!WARNING]
> 오케스트레이터 함수는 오케스트레이션 트리거 바인딩 이외의 입력 또는 출력 바인딩을 절대로 사용하면 안됩니다. 이렇게 하면 그러한 바인딩에서 단일 스레딩 및 I/O 규칙을 따르지 않을 수 있으므로 지속성 작업 확장에서 문제가 발생할 수 있습니다. 다른 바인딩을 사용하려면, 이를 Orchestrator 함수에서 호출한 작업 함수에 추가합니다.

> [!WARNING]
> JavaScript 오케스트레이터 함수는 `async`로 선언되지 않아야 합니다.

### <a name="trigger-usage-net"></a>트리거 사용(.NET)

오케스트레이션 트리거 바인딩은 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **입력** - .NET 오케스트레이션 함수는 `DurableOrchestrationContext`만 매개 변수 형식으로 지원합니다. 함수 시그니처에서 직접적인 역직렬화 입력은 지원되지 않습니다. 코드에서는 오케스트레이터 함수 입력을 가져오기 위해`GetInput<T>`또는 `getInput`(JavaScript) 메서드를 사용해야 합니다. 이러한 입력은 JSON 직렬화 가능 형식이어야 합니다.
* **출력** - 오케스트레이션 트리거는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다. .NET 함수에서 `Task` 또는 `void`를 반환하면 `null` 값이 출력으로 저장됩니다.

### <a name="trigger-sample"></a>트리거 샘플

다음은 가장 간단한 "Hello World" 오케스트레이터 함수 예제입니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> 이전 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `IDurableOrchestrationContext` 대신 `DurableOrchestrationContext`를 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> JavaScript의 `context` 개체는 DurableOrchestrationContext가 아니라 [전체적으로 함수 컨텍스트](../functions-reference-node.md#context-object)를 나타냅니다. `context` 개체의 `df` 속성을 통해 오케스트레이션 메서드에 액세스할 수 있습니다.

> [!NOTE]
> JavaScript 오케스트레이터는 `return`을 사용해야 합니다. `durable-functions` 라이브러리는 `context.done` 메서드 호출을 처리합니다.

대부분의 오케스트레이터 함수에서 작업 함수를 호출하므로 작업 함수를 호출하는 방법을 보여 주는 "Hello World" 예제가 여기에 있습니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> 이전 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `IDurableOrchestrationContext` 대신 `DurableOrchestrationContext`를 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>작업 트리거

작업 트리거를 사용하면 [작업 함수](durable-functions-types-features-overview.md#activity-functions)라고 하는 오케스트레이터 함수에서 호출하는 함수를 작성할 수 있습니다.

Visual Studio를 사용하는 경우 작업 트리거는 `ActivityTriggerAttribute` .NET 특성을 사용하여 구성됩니다.

개발을 위해 VS Code 또는 Azure Portal을 사용하는 경우 작업 트리거는 *function.json* 의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`은 작업의 이름입니다. 이 값은 오케스트레이터 함수에서 이 작업 함수를 호출하는 데 사용하는 이름입니다. 이 속성은 선택 사항입니다. 지정하지 않으면 함수의 이름이 사용됩니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 스토리지 계정에 있는 큐를 폴링합니다. 이 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 작업 트리거에 대한 몇 가지 참고 사항입니다.

* **스레딩** - 오케스트레이션 트리거와 달리 작업 트리거에는 스레딩 또는 I/O에 대한 제한이 없습니다. 일반 함수처럼 처리될 수 있습니다.
* **포이즌 메시지 처리** - 작업 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 작업 트리거 메시지가 구성 가능한 기간 동안 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 반환 값은 JSON으로 직렬화되고 Azure Table Storage의 오케스트레이션 기록 테이블에 유지됩니다.

> [!WARNING]
> 작업 함수에 대한 스토리지 백 엔드는 구현 세부 정보이며, 사용자 코드는 이러한 스토리지 엔터티와 직접 상호 작용하면 안됩니다.

### <a name="trigger-usage-net"></a>트리거 사용(.NET)

작업 트리거 바인딩은 오케스트레이션 트리거와 마찬가지로 입력과 출력을 모두 지원합니다. 다음은 입력 및 출력 처리에 대해 알고 있어야 할 몇 가지 사항입니다.

* **입력** - .NET 작업 함수는 기본적으로 `DurableActivityContext`를 매개 변수 형식으로 사용합니다. 또는 JSON 직렬화 가능 매개 변수 형식으로 선언될 수 있습니다. `DurableActivityContext`를 사용하면 `GetInput<T>`를 호출하여 작업 함수 입력을 가져오고 역직렬화할 수 있습니다.
* **출력** - 작업 함수는 입력뿐만 아니라 출력 값도 지원합니다. 함수의 반환 값은 출력 값을 할당하는 데 사용되며 JSON 직렬화 가능해야 합니다. .NET 함수에서 `Task` 또는 `void`를 반환하면 `null` 값이 출력으로 저장됩니다.
* **메타데이터** - .NET 활동 함수는 `string instanceId` 매개 변수에 바인딩하여 부모 오케스트레이션의 인스턴스 ID를 가져올 수 있습니다.

### <a name="trigger-sample"></a>트리거 샘플

다음은 간단한 "Hello World" 작업 함수 예제를 보여주는 예제 코드입니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> 이전 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `IDurableActivityContext` 대신 `DurableActivityContext`를 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

.NET `ActivityTriggerAttribute` 바인딩의 기본 매개 변수 형식은 `IDurableActivityContext`입니다. 그러나 .NET 작업 트리거는 JSON 직렬화 가능 형식(기본 형식 포함)에 대한 직접 바인딩도 지원하므로 동일한 함수를 다음과 같이 단순화할 수 있습니다.

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript(Functions 2.0만 해당)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript 바인딩도 추가 매개 변수로 전달될 수 있으므로 동일한 함수를 다음과 같이 단순화할 수 있습니다.

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>입력 및 출력 바인딩 사용

작업 트리거 바인딩 외에도 일반 입력 및 출력 바인딩을 사용할 수 있습니다. 예를 들어, 작업 바인딩에 대한 입력을 가져오고 EventHub 출력 바인딩을 사용하여 EventHub로 메시지를 보낼 수 있습니다.

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>오케스트레이션 클라이언트

오케스트레이션 클라이언트 바인딩을 사용하면 오케스트레이터 함수와 상호 작용하는 함수를 작성할 수 있습니다. 해당 함수를 [클라이언트 함수](durable-functions-types-features-overview.md#client-functions)라고도 합니다. 예를 들어 오케스트레이션 인스턴스에서 다음과 같은 방법으로 작동할 수 있습니다.

* 인스턴스를 시작합니다.
* 해당 상태를 쿼리합니다.
* 인스턴스를 종료합니다.
* 실행하는 동안 이벤트를 보냅니다.
* 인스턴스 기록을 제거합니다.

Visual Studio를 사용하는 경우, Durable Functions 1.0의 `OrchestrationClientAttribute` .NET 특성을 사용하여 오케스트레이션 클라이언트에 바인딩할 수 있습니다. Durable Functions 2.0부터 `DurableClientAttribute` .NET 특성을 사용하여 오케스트레이션 클라이언트에 바인딩할 수 있습니다.

개발을 위해 스크립팅 언어(예: *.csx* 또는 *.js* 파일)를 사용하는 경우 오케스트레이션 트리거는 *function.json* 의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 여러 함수 앱에서 동일한 스토리지 계정을 공유하지만 서로 격리되어야 하는 시나리오에 사용됩니다. 지정하지 않으면 `host.json`의 기본값이 사용됩니다. 이 값은 대상 오케스트레이터 함수에서 사용하는 값과 일치해야 합니다.
* `connectionName` - 스토리지 계정 연결 문자열을 포함하는 앱 설정의 이름입니다. 이 연결 문자열에서 나타내는 스토리지 계정은 대상 오케스트레이터 함수에서 사용하는 계정과 동일해야 합니다. 지정하지 않으면 함수 앱에 대한 기본 스토리지 계정 연결 문자열이 사용됩니다.

> [!NOTE]
> 대부분의 경우 이러한 속성을 생략하고 기본 동작을 사용하는 것이 좋습니다.

### <a name="client-usage"></a>클라이언트 사용

.NET 함수에서는 일반적으로 `IDurableOrchestrationClient`에 바인딩하며, 이는 Durable Functions에서 지원하는 모든 오케스트레이션 클라이언트 API에 대한 모든 액세스 권한을 부여합니다. 이전 Durable Functions 2.x 릴리스에서는 대신 `DurableOrchestrationClient` 클래스에 바인딩합니다. JavaScript에서 동일한 API는 `getClient`에서 반환된 개체를 통해 공개됩니다. 클라이언트 개체에 대한 API는 다음과 같습니다.

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

또는, .NET 함수는 `T`가 `StartOrchestrationArgs` 또는 `JObject`인 `IAsyncCollector<T>`에 바인딩할 수 있습니다.

해당 작업에 대한 자세한 내용은 `IDurableOrchestrationClient` API 설명서를 참조하세요.

### <a name="client-sample-visual-studio-development"></a>클라이언트 샘플(Visual Studio 개발)

다음은 "HelloWorld" 오케스트레이션을 시작하는 큐 트리거 함수 예제입니다.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 이전 C# 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `DurableClient` 특성 대신 `OrchestrationClient` 특성을 사용해야 하며 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 형식을 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

### <a name="client-sample-not-visual-studio"></a>클라이언트 샘플(Visual Studio 사용 안 함)

개발을 위해 Visual Studio를 사용하지 않는 경우 다음 *function.json* 파일을 만들 수 있습니다. 이 예제에서는 지속성 함수 오케스트레이션 클라이언트 바인딩을 사용하는 큐 트리거 함수를 구성하는 방법을 보여 줍니다.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> 이전 JSON은 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우, 트리거 형식으로 `durableClient` 대신 `orchestrationClient`를 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

다음은 새 오케스트레이터 함수 인스턴스를 시작하는 언어 관련 샘플입니다.

#### <a name="c-script-sample"></a>C# 스크립트 샘플

다음 샘플에서는 지속성 오케스트레이션 클라이언트 바인딩을 사용하여 큐에서 트리거한 C# 함수에서 새 함수 인스턴스를 시작하는 방법을 보여 줍니다.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 이전 코드는 Durable Functions 2.x용입니다. Durable Functions 1.x의 경우 `IDurableOrchestrationClient` 대신 `DurableOrchestrationClient` 매개 변수 유형을 사용해야 합니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

#### <a name="javascript-sample"></a>JavaScript 샘플

다음 샘플에서는 지속성 오케스트레이션 클라이언트 바인딩을 사용하여 JavaScript 함수에서 새 함수 인스턴스를 시작하는 방법을 보여줍니다.

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

인스턴스 시작에 대한 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md)에서 찾을 수 있습니다.

## <a name="entity-trigger"></a>엔터티 트리거

엔터티 트리거를 사용하면 [엔터티 함수](durable-functions-entities.md)를 작성할 수 있습니다. 이 트리거는 특정 엔터티 인스턴스에 대한 이벤트 처리를 지원합니다.

Azure Functions에 Visual Studio 도구를 사용하는 경우, 엔터티 트리거는 `EntityTriggerAttribute` .NET 특성을 사용하여 구성됩니다.

> [!NOTE]
> 엔터티 트리거는 Durable Functions 2.x부터 사용할 수 있습니다.

내부적으로 이 트리거 바인딩은 함수 앱에 대한 기본 스토리지 계정에 있는 일련의 큐를 폴링합니다. 이러한 큐는 확장에 대한 내부 구현 세부 정보이며, 이는 바인딩 속성에서 명시적으로 구성되지 않은 이유입니다.

### <a name="trigger-behavior"></a>트리거 동작

다음은 엔터티 트리거에 대한 몇 가지 참고 사항입니다.

* **단일 스레드**: 단일 디스패처 스레드를 사용하여 특정 엔터티에 대한 작업을 처리합니다. 여러 메시지를 단일 엔터티로 동시에 보내는 경우, 작업은 한 번에 하나씩 처리됩니다.
* **포이즌 메시지 처리** - 엔터티 트리거에는 포이즌 메시지 지원이 없습니다.
* **메시지 가시성** - 구성 가능한 기간 동안 엔터티 트리거 메시지가 큐에서 제거되고 보이지 않게 유지됩니다. 함수 앱이 실행되고 있고 정상으로 유지되는 동안은 이러한 메시지의 가시성이 자동으로 갱신됩니다.
* **반환 값** - 엔터티 함수는 반환 값을 지원하지 않습니다. 상태를 저장하거나 값을 다시 오케스트레이션에 전달하는 데 사용할 수 있는 특정 API가 있습니다.

실행 중에 엔터티 상태를 변경하면 실행이 완료된 후 자동으로 유지 됩니다.

### <a name="trigger-usage-net"></a>트리거 사용(.NET)

모든 엔터티 함수에는 다음 멤버를 포함하는 매개 변수 형식 `IDurableEntityContext`가 있습니다.

* **EntityName**: 현재 실행 중인 엔터티의 이름입니다.
* **EntityKey**: 현재 실행 중인 엔터티의 키입니다.
* **EntityId**: 현재 실행 중인 엔터티의 ID입니다.
* **OperationName**: 현재 작업의 이름입니다.
* **HasState**: 엔터티가 존재하는지 여부 즉, 엔터티에 상태가 있는지를 나타냅니다. 
* **GetState\<TState>()** : 엔터티의 현재 상태를 가져옵니다. 아직 존재하지 않는 경우, 생성되어 `default<TState>`로 초기화됩니다. `TState` 매개 변수는 기본 형식 또는 JSON 직렬화 가능 형식이어야 합니다. 
* **Getstate \<TState> (initfunction)** : 엔터티의 현재 상태를 가져옵니다. 아직 존재하지 않는 경우 제공된 `initfunction` 매개 변수를 호출하여 생성됩니다. `TState` 매개 변수는 기본 형식 또는 JSON 직렬화 가능 형식이어야 합니다. 
* **SetState(arg)** : 엔터티의 상태를 만들거나 업데이트합니다. `arg` 매개 변수는 JSON 일련화 가능 개체 또는 기본 형식이어야 합니다.
* **DeleteState()** : 엔터티의 상태를 삭제합니다. 
* **GetInput\<TInput>()** : 현재 작업에 대한 입력을 가져옵니다. `TInput` 형식 매개 변수는 기본 형식 또는 JSON 직렬화 가능 형식이어야 합니다.
* **Return(arg)** : 작업을 호출한 오케스트레이션에 값을 반환합니다. `arg` 매개 변수는 기본 형식 또는 JSON 직렬화 가능 개체여야 합니다.
* **SignalEntity(EntityId, scheduledTimeUtc, operation, input)** : 엔터티에 단방향 메시지를 보냅니다. `operation`매개 변수는 Null이 아닌 문자열이어야 하고, 선택적 `scheduledTimeUtc`는 작업을 호출하는 UTC 날짜/시간이어야 하며, `input` 매개 변수는 기본 또는 JSON 일련화 가능 개체여야 합니다.
* **CreateNewOrchestration(orchestratorFunctionName, input)** : 새 오케스트레이션을 시작합니다. `input`매개 변수는 기본 형식 또는 JSON 직렬화 가능 개체여야 합니다.

엔터티 함수에 전달되는 `IDurableEntityContext` 개체는 `Entity.Current` async-local 속성을 사용하여 액세스할 수 있습니다. 이 방법은 클래스 기반 프로그래밍 모델을 사용하는 경우에 편리합니다.

### <a name="trigger-sample-c-function-based-syntax"></a>트리거 샘플(C# 함수 기반 구문)

다음 코드는 지속성 함수로 구현된 간단한 *Counter* 엔터티의 예제입니다. 이 함수는 각각 정수 상태에서 작동하는 세 가지 작업(`add`, `reset` 및 `get`)을 정의합니다.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

함수 기반 구문과 이를 사용하는 방법에 대한 자세한 내용은 [함수 기반 구문](durable-functions-dotnet-entities.md#function-based-syntax)을 참조하세요.

### <a name="trigger-sample-c-class-based-syntax"></a>트리거 샘플(C# 클래스 기반 구문)

다음 예제에서는 클래스와 메서드를 사용하여 `Counter` 엔터티를 동일하게 구현합니다.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

이 엔터티의 상태는 카운터의 현재 값을 저장하는 필드가 포함된 `Counter` 형식의 개체입니다. 이 개체를 스토리지에 유지하기 위해 [Json.NET](https://www.newtonsoft.com/json) 라이브러리에서 직렬화 및 역직렬화합니다. 

클래스 기반 구문과 이를 사용하는 방법에 대한 자세한 내용은 [엔터티 클래스 정의](durable-functions-dotnet-entities.md#defining-entity-classes)를 참조하세요.

> [!NOTE]
> 엔터티 클래스를 사용하는 경우 `[FunctionName]` 특성이 있는 함수 진입점 메서드를 `static`으로 *선언해야* 합니다. 비정적 진입점 메서드를 사용하면 여러 개체가 초기화되고 잠재적으로 정의되지 않은 다른 동작이 발생할 수 있습니다.

엔터티 클래스에는 바인딩 및 .NET 종속성 주입과 상호 작용하기 위한 특수 메커니즘이 있습니다. 자세한 내용은 [엔터티 구성](durable-functions-dotnet-entities.md#entity-construction)을 참조하세요.

### <a name="trigger-sample-javascript"></a>트리거 샘플(JavaScript)

다음 코드는 JavaScript로 작성된 지속성 함수로 구현된 간단한 *Counter* 엔터티의 예제입니다. 이 함수는 각각 정수 상태에서 작동하는 세 가지 작업(`add`, `reset` 및 `get`)을 정의합니다.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> 지속성 엔터티는 `durable-functions` npm 패키지의 버전 **1.3.0** 부터 JavaScript에서 사용할 수 있습니다.

## <a name="entity-client"></a>엔터티 클라이언트

엔터티 클라이언트 바인딩을 사용하면 [엔터티 함수](#entity-trigger)를 비동기적으로 트리거할 수 있습니다. 해당 함수를 [클라이언트 함수](durable-functions-types-features-overview.md#client-functions)라고도 합니다.

Visual Studio를 사용하는 경우, `DurableClientAttribute` .NET 특성을 사용하여 엔터티 클라이언트에 바인딩할 수 있습니다.

> [!NOTE]
> `[DurableClientAttribute]`를 사용하여 [오케스트레이션 클라이언트](#orchestration-client)에 바인딩할 수도 있습니다.

개발을 위해 스크립팅 언어(예: *.csx* 또는 *.js* 파일)를 사용하는 경우, 엔터티 트리거는 *function.json* 의 `bindings` 배열에 있는 다음 JSON 개체에서 정의됩니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - 여러 함수 앱에서 동일한 스토리지 계정을 공유하지만 서로 격리되어야 하는 시나리오에 사용됩니다. 지정하지 않으면 `host.json`의 기본값이 사용됩니다. 이 값은 대상 엔터티 함수에서 사용하는 값과 일치해야 합니다.
* `connectionName` - 스토리지 계정 연결 문자열을 포함하는 앱 설정의 이름입니다. 이 연결 문자열에서 나타내는 스토리지 계정은 대상 엔터티 함수에서 사용하는 계정과 동일해야 합니다. 지정하지 않으면 함수 앱에 대한 기본 스토리지 계정 연결 문자열이 사용됩니다.

> [!NOTE]
> 대부분의 경우 선택적 속성을 생략하고 기본 동작을 사용하는 것이 좋습니다.

### <a name="entity-client-usage"></a>엔터티 클라이언트 사용

.NET 함수에서는 일반적으로 `IDurableEntityClient`에 바인딩하며, 이는 지속성 엔터티에서 지원하는 모든 클라이언트 API에 대한 전체 액세스 권한을 부여합니다. 엔터티와 오케스트레이션의 클라이언트 API에 대한 액세스를 제공하는 `IDurableOrchestrationClient` 인터페이스에 바인딩할 수도 있습니다. 클라이언트 개체에 대한 API는 다음과 같습니다.

* **ReadEntityStateAsync\<T>** : 엔터티의 상태를 읽습니다. 대상 엔터티가 있는지 여부를 나타내는 응답을 반환하고, 그러한 경우 상태를 반환합니다.
* **SignalEntityAsync**: 엔터티에 단방향 메시지를 보내고 큐에 대기될 때까지 기다립니다.
* **ListEntitiesAsync**: 여러 엔터티의 상태를 쿼리합니다. 엔터티는 *이름* 및 *마지막 작업 시간* 으로 쿼리할 수 있습니다.

신호를 보내기 전에 대상 엔터티를 만들 필요는 없습니다. 신호를 처리하는 엔터티 함수 내에서 엔터티 상태를 만들 수 있습니다.

> [!NOTE]
> 클라이언트에서 전송된 "신호"는 큐에 넣은 후 나중에 비동기식으로 처리된다는 것을 이해하는 것이 중요합니다. 특히 `SignalEntityAsync`는 일반적으로 엔터티에서 작업을 시작하기 전에 반환하며 반환 값을 다시 가져오거나 예외를 관찰할 수 없습니다. 워크플로의 경우와 같이 더 강력한 보증이 필요한 경우에는 *오케스트레이터 함수* 를 사용해야 합니다. 이 함수를 사용하면 엔터티 작업이 완료될 때까지 기다릴 수 있으며 반환 값을 처리하고 예외를 관찰할 수 있습니다.

### <a name="example-client-signals-entity-directly---c"></a>예: 클라이언트에서 직접 엔터티에 신호 - C#

다음은 "Counter" 엔터티를 호출하는 큐 트리거 함수 예제입니다.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>예: 클라이언트가 인터페이스-C#을 통해 엔터티에 신호를 보냅니다.

더 많은 형식 검사를 제공하므로 가능하면 [인터페이스 ](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)를 통해 엔터티에 액세스하는 것이 좋습니다. 예를 들어, 앞에서 언급한 `Counter` 엔터티가 다음과 같이 정의된 `ICounter` 인터페이스를 구현했다고 가정합니다.

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

클라이언트 코드는 `SignalEntityAsync<ICounter>`를 사용하여 형식이 안전한 프록시를 생성할 수 있습니다.

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

`proxy` 매개 변수는 `Add`에 대한 호출을 내부적으로 해당(형식화되지 않은) `SignalEntityAsync` 호출로 변환하는 `ICounter`의 동적으로 생성된 인스턴스입니다.

> [!NOTE]
> `SignalEntityAsync` API는 단방향 작업을 나타냅니다. 엔터티 인터페이스에서 `Task<T>`을 반환하는 경우, `T` 매개 변수의 값은 항상 Null 또는 `default`입니다.

특히 값을 반환하지 않으므로 `Get` 작업에 신호를 보내는 것은 의미가 없습니다. 대신, 클라이언트는 `ReadStateAsync`를 사용하여 카운터 상태에 직접 액세스하거나 `Get` 작업을 호출하는 오케스트레이터 함수를 시작할 수 있습니다.

### <a name="example-client-signals-entity---javascript"></a>예: 클라이언트에서 엔터티에 신호 보내기 - JavaScript

다음은 JavaScript의 "카운터" 엔터티에 신호를 전달하는 큐 트리거 함수 예제입니다.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> 지속성 엔터티는 `durable-functions` npm 패키지의 버전 **1.3.0** 부터 JavaScript에서 사용할 수 있습니다.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 설정

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인스턴스 관리에 대한 기본 제공 HTTP API 참조](durable-functions-http-api.md)
