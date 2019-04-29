---
title: 지속성 함수의 검사점 및 재생 - Azure
description: Azure Functions의 지속성 함수 확장에서 검사점 설정 및 회신이 작동하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: b1fd31a758501620129fdbbc532b8defcf927045
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648502"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>지속성 함수의 검사점 및 재생(Azure Functions)

지속성 함수의 키 특성 중 하나는 **신뢰할 수 있는 실행**입니다. 오케스트레이터 함수 및 작업 함수가 데이터 센터 내의 다른 VM에서 실행 중일 수 있으며 해당 VM 또는 기본 네트워킹 인프라는 100% 신뢰할 수 없습니다.

그럼에도 불구하고 지속성 함수는 오케스트레이션을 안정적으로 실행합니다. 이렇게 하려면 저장소 큐를 사용하여 함수 호출을 수행하고 저장소 테이블에 실행 기록 검사점을 정기적으로 설정합니다([이벤트 소싱](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)으로 알려진 클라우드 디자인 패턴 사용). 그런 다음 해당 기록을 재생하여 오케스트레이터 함수의 메모리 내 상태를 자동으로 다시 작성할 수 있습니다.

## <a name="orchestration-history"></a>오케스트레이션 기록

다음 오케스트레이터 함수가 있다고 가정합니다.

### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

각각의 `await`(C#) 또는 `yield`(JavaScript) 문에서 지속성 작업 프레임워크는 함수의 실행 상태 검사점을 Table Storage에 설정합니다. 이 상태를 *오케스트레이션 기록*이라고 합니다.

## <a name="history-table"></a>기록 테이블

일반적으로 지속성 작업 프레임워크는 각 검사점에서 다음을 수행합니다.

1. 실행 기록을 Azure Storage 테이블에 저장합니다.
2. 오케스트레이터에서 호출하려고 하는 함수의 메시지를 큐에 넣습니다.
3. 오케스트레이터 자체의 메시지(예: 지속성 타이머 메시지)를 큐에 넣습니다.&mdash;

검사점이 완료되면 오케스트레이터 함수는 더 많은 작업이 수행될 때까지 메모리에서 자유롭게 제거할 수 있습니다.

> [!NOTE]
> Azure Storage는 테이블 스토리지와 큐 간의 데이터 저장에 대한 트랜잭션 보장을 제공하지 않습니다. 지속성 함수 저장소 공급자는 실패를 처리하기 위해 *최종 일관성* 패턴을 사용합니다. 이러한 패턴은 검사점 중간에 연결이 충돌하거나 손실되는 경우 데이터가 손실되지 않도록 합니다.

완료 시 이전에 표시된 함수의 기록은 Azure Table Storage에서 다음과 비슷합니다(이해를 돕기 위해 약어로 표시).

| PartitionKey(InstanceId)                     | EventType             | 타임 스탬프               | 입력 | 이름             | 결과                                                    | 상태 |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Completed           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

열 값에 대한 몇 가지 참고 사항:

* **PartitionKey**: 오케스트레이션 인스턴스 ID를 포함합니다.
* **EventType**: 이벤트 유형을 나타냅니다. 다음 유형 중 하나일 수 있습니다.
  * **OrchestrationStarted**: 오케스트레이터 함수가 대기 상태에서 다시 시작되었거나 처음으로 실행되고 있습니다. `Timestamp` 열은 [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API에 대한 결정적 값을 채우는 데 사용됩니다.
  * **ExecutionStarted**: 오케스트레이터 함수가 처음으로 실행되기 시작했습니다. 또한 이 이벤트의 `Input` 열에 함수 입력이 포함됩니다.
  * **TaskScheduled**: 작업 함수가 예약되었습니다. `Name` 열에 작업 함수의 이름이 캡처됩니다.
  * **TaskCompleted**: 작업 함수가 완료되었습니다. `Result` 열에 함수 결과가 있습니다.
  * **TimerCreated**: 지속성 타이머가 만들어졌습니다. `FireAt` 열에 타이머가 만료되는 예약된 UTC 시간이 포함됩니다.
  * **TimerFired**: 지속성 타이머가 실행되었습니다.
  * **EventRaised**: 외부 이벤트가 오케스트레이션 인스턴스로 전송되었습니다. `Name` 열에 이벤트 이름이, `Input` 열에 이벤트의 페이로드가 캡처됩니다.
  * **OrchestratorCompleted**: 오케스트레이터 함수가 기다리고 있습니다.
  * **ContinueAsNew**: 오케스트레이터 함수가 완료되고 새 상태로 다시 시작되었습니다. `Result` 열에 다시 시작되는 인스턴스의 입력으로 사용되는 값이 포함됩니다.
  * **ExecutionCompleted**: 오케스트레이터 함수가 실행되어 완료되었거나 실패했습니다. `Result` 열에 함수 또는 오류 세부 정보의 출력이 저장됩니다.
* **Timestamp**: 기록 이벤트의 UTC 타임스탬프입니다.
* **Name**: 호출된 함수의 이름입니다.
* **입력**: JSON 형식의 함수 입력입니다.
* **결과**: 함수의 출력, 즉, 반환 값입니다.

> [!WARNING]
> 디버깅 도구로 유용하지만 이 테이블에 대한 종속성은 사용하지 마세요. 지속성 함수 확장이 진화함에 따라 변경될 수 있습니다.

함수가 `await`(C#) 또는 `yield`(JavaScript)에서 다시 시작될 때마다 지속성 작업 프레임워크는 오케스트레이터 함수를 처음부터 다시 실행합니다. 다시 실행할 때마다 실행 기록을 검토하여 현재의 비동기 작업이 수행되었는지 여부를 결정합니다.  작업이 수행되었으면 프레임워크에서 해당 작업의 결과를 즉시 재생하고 다음 `await`(C#) 또는 `yield`(JavaScript)로 이동합니다. 이 프로세스는 전체 기록을 재생할 때까지 계속되며, 이 시점에서 오케스트레이터 함수의 모든 지역 변수가 이전 값으로 복원됩니다.

## <a name="orchestrator-code-constraints"></a>오케스트레이터 코드 제약 조건

재생 동작은 오케스트레이터 함수에 작성될 수 있는 코드 형식에 대한 제약 조건을 만듭니다.

* 오케스트레이터 코드는 **결정적**이어야 합니다. 여러 번 재생되며 매번 동일한 결과가 생성되어야 합니다. 예를 들어 현재 날짜/시간을 가져오거나, 난수를 가져오거나, 임의의 GUID를 생성하거나, 원격 엔드포인트로 호출하는 직접 호출이 없습니다.

  오케스트레이터 코드에서 현재 날짜/시간을 가져와야 하는 경우 안전하게 재생할 수 있는 [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime)(.NET) 또는 `currentUtcDateTime`(JavaScript) API를 사용해야 합니다.

  오케스트레이터 코드는 임의 GUID를 생성해야 하는 경우 안전하게 재생할 수 있는 [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid)(.NET) API를 사용하거나 이 예제처럼 작업 함수(JavaScript)에 GUID 생성을 위임해야 합니다.

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  비결정적 작업은 작업 함수에서 수행되어야 합니다. 여기에는 다른 입력 또는 출력 바인딩과의 상호 작용이 포함됩니다. 이렇게 하면 모든 비결정적 값이 첫 번째 실행에서 한 번 생성되고 실행 기록에 저장됩니다. 그런 다음 후속 실행에서 저장된 값을 자동으로 사용합니다.

* 오케스트레이터 코드는 **비차단**이어야 합니다. 예를 들어 I/O가 없거나 `Thread.Sleep`(.NET) 또는 해당 API에 대한 호출이 없음을 의미합니다.

  오케스트레이터가 지연해야 하는 경우 [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_)(.NET) 또는 `createTimer`(JavaScript) API를 사용하면 됩니다.

* 오케스트레이터 코드는 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API 또는 `context.df` 개체의 API를 사용하는 경우를 제외하고 **어떤 비동기 작업도 시작하면 안 됩니다**. .NET의 `Task.Run`, `Task.Delay`, `HttpClient.SendAsync` 또는 JavaScript의 `setTimeout()`, `setInterval()`을 예로 들 수 있습니다. 지속성 작업 프레임워크는 오케스트레이터 코드를 단일 스레드에서 실행하며, 다른 비동기 API에서 예약할 수 있는 다른 스레드와 상호 작용할 수 없습니다.

* 오케스트레이터 코드에서 **무한 루프를 사용하면 안됩니다**. 지속성 작업 프레임워크는 오케스트레이션 함수가 진행됨에 따라 실행 기록을 저장하기 때문에 무한 루프로 인해 오케스트레이터 인스턴스의 메모리가 부족해질 수 있습니다. 무한 루프 시나리오의 경우 [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_)(.NET) 또는 `continueAsNew`(JavaScript) 같은 API를 사용하여 함수 실행을 다시 시작하고 이전 실행 기록을 버립니다.

* JavaScript 오케스트레이터 함수는 `async`이면 안 됩니다. 동기 생성기 함수로 선언해야 합니다.

이러한 제약 조건이 처음에는 까다롭게 보일 수 있지만 실제로는 따르기가 어렵지 않습니다. 지속성 작업 프레임워크는 위의 규칙 위반을 감지하려고 하고 `NonDeterministicOrchestrationException`을 throw합니다. 그러나 이 감지 동작은 최상의 노력이므로 이에 의존하면 안됩니다.

> [!NOTE]
> 이러한 규칙은 모두 `orchestrationTrigger` 바인딩에서 트리거된 함수에만 적용됩니다. `activityTrigger` 바인딩에서 트리거된 작업 함수와 `orchestrationClient` 바인딩을 사용하는 함수에는 이러한 제한 이 없습니다.

## <a name="durable-tasks-net"></a>지속성 작업(.NET)

> [!NOTE]
> 이 섹션에서는 지속성 작업 프레임워크의 내부 구현에 대해 자세히 설명합니다. 이 정보를 알지 못해도 지속성 함수를 사용할 수 있습니다. 여기서는 재생 동작을 이해하는 데에만 도움이 됩니다.

오케스트레이터 함수에서 안전하게 기다릴 수 있는 작업을 종종 *지속성 작업*이라고 합니다. 이러한 작업은 지속성 작업 프레임워크에서 만들고 관리하는 작업입니다. 예제는 `CallActivityAsync`, `WaitForExternalEvent` 및 `CreateTimer`에서 반환된 작업입니다.

이러한 *지속성 작업*은 `TaskCompletionSource` 개체의 목록을 사용하여 내부적으로 관리됩니다. 이러한 작업은 재생하는 동안 오케스트레이터 코드 실행의 일부로 만들어지고 디스패처에서 해당 기록 이벤트를 열거하는 대로 완료됩니다. 모든 기록이 재생될 때까지 단일 스레드를 사용하여 모든 작업이 동기적으로 수행됩니다. 기록 재생이 끝날 때까지 완료되지 않은 모든 지속성 작업에는 적절한 조치가 수행됩니다. 예를 들어 메시지를 큐에 추가하여 작업 함수를 호출할 수 있습니다.

여기서 설명한 실행 동작은 오케스트레이터 함수 코드에서 비지속성 작업을 절대로 `await`(대기)하면 안되는 이유를 이해하는 데 도움이 됩니다. 즉 디스패처 스레드가 완료될 때까지 기다릴 수 없으며, 해당 작업의 콜백으로 인해 오케스트레이터 함수의 추적 상태가 손상될 수 있습니다. 일부 런타임 검사가 이를 방지하기 위해 수행됩니다.

지속성 작업 프레임워크에서 오케스트레이터 함수를 실행하는 방법에 대한 자세한 내용은 [GitHub의 지속성 작업 소스 코드](https://github.com/Azure/durabletask)를 참조하세요. 특히 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 및 [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인스턴스 관리](durable-functions-instance-management.md)
