---
title: 지속성 오케스트레이션 - Azure Functions
description: Azure Durable Functions의 오케스트레이션 기능을 소개합니다.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 5eec15871279f3ca38c726fcd1ef1b21d0d38699
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750195"
---
# <a name="durable-orchestrations"></a>지속성 오케스트레이션

Durable Functions은 [Azure Functions](../functions-overview.md)의 확장입니다. *오케스트레이터 함수*를 사용하여 함수 앱 내에서 다른 지속성 함수의 실행을 오케스트레이션할 수 있습니다. 오케스트레이터 함수에는 다음과 같은 특징이 있습니다.

* 오케스트레이터 함수는 절차적 코드를 사용하여 함수 워크플로를 정의합니다. 선언적 스키마 또는 디자이너가 필요하지 않습니다.
* 오케스트레이터 함수는 다른 지속성 함수를 동기적 및 비동기적으로 호출할 수 있습니다. 호출된 함수의 출력은 로컬 변수에 안전하게 저장할 수 있습니다.
* 오케스트레이터 함수는 지속적이고 신뢰할 수 있습니다. 함수에서 "대기" 또는 "생성"할 때 실행 진행률 검사점이 자동으로 설정됩니다. 프로세스가 재활용되거나 VM이 다시 부팅되더라도 로컬 상태가 손실되지 않습니다.
* 오케스트레이터 함수는 장기 실행될 수 있습니다. *오케스트레이션 인스턴스*의 총 수명은 초, 일, 월 또는 무한대일 수 있습니다.

이 문서에서는 오케스트레이터 함수에 대한 개요 및 이를 통해 다양한 앱 개발 문제를 해결하는 데 도움이 되는 방법을 제공합니다. Durable Functions 앱에서 사용할 수 있는 함수 형식에 익숙하지 않으면 먼저 [Durable Functions 형식](durable-functions-types-features-overview.md) 문서를 참조하세요.

## <a name="orchestration-identity"></a>오케스트레이션 ID

오케스트레이션의 각 *인스턴스*에는 인스턴스 식별자(*인스턴스 ID*라고도 함)가 있습니다. 기본적으로 각 인스턴스 ID는 자동으로 생성된 GUID입니다. 그러나 인스턴스 ID는 사용자가 생성한 문자열 값일 수도 있습니다. 각 오케스트레이션 인스턴스 ID는 [작업 허브](durable-functions-task-hubs.md) 내에서 고유해야 합니다.

인스턴스 ID에 대한 몇 가지 규칙은 다음과 같습니다.

* 인스턴스 ID는 1-256자여야 합니다.
* 인스턴스 ID는 `@`로 시작할 수 없습니다.
* 인스턴스 ID는 `/`, `\`, `#` 또는 `?` 문자를 포함할 수 없습니다.
* 인스턴스 ID는 제어 문자를 포함할 수 없습니다.

> [!NOTE]
> 일반적으로 가능한 경우 자동으로 생성된 인스턴스 ID를 사용하는 것이 좋습니다. 사용자가 생성한 인스턴스 ID는 오케스트레이션 인스턴스와 일부 외부 애플리케이션 특정 엔터티(예: 구매 주문 또는 문서) 간에 일대일 매핑이 있는 시나리오를 위한 것입니다.

오케스트레이션의 인스턴스 ID는 대부분의 [인스턴스 관리 작업](durable-functions-instance-management.md)에 필요한 매개 변수입니다. 또한 문제 해결 또는 분석을 위해 Application Insights에서 [오케스트레이션 추적 데이터를 검색](durable-functions-diagnostics.md#application-insights)하는 것과 같은 진단에도 중요합니다. 이러한 이유로 생성된 인스턴스 ID는 나중에 쉽게 참조할 수 있는 일부 외부 위치(예: 데이터베이스 또는 애플리케이션 로그)에 저장하는 것이 좋습니다.

## <a name="reliability"></a>안정성

오케스트레이터 함수는 [이벤트 소싱](/azure/architecture/patterns/event-sourcing) 디자인 패턴을 사용하여 실행 상태를 안정적으로 유지합니다. 지속성 작업 프레임워크는 오케스트레이션의 현재 상태를 직접 저장하는 대신, 추가 전용 저장소를 사용하여 함수 오케스트레이션에서 수행하는 일련의 작업 전체를 기록합니다. 추가 전용 저장소는 전체 런타임 상태를 "덤프"하는 것에 비해 많은 이점을 누릴 수 있습니다. 즉 향상된 성능, 확장성 및 응답성을 얻을 수 있습니다. 또한 트랜잭션 데이터와 전체 감사 내역 및 기록에 대한 최종 일관성을 얻을 수 있습니다. 감사 내역은 안정적인 보정 작업을 지원합니다.

Durable Functions는 이벤트 소싱을 투명하게 사용합니다. 오케스트레이터 함수의 `await`(C#) 또는 `yield`(JavaScript/Python) 연산자는 내부적으로 오케스트레이터 스레드의 제어를 지속성 작업 프레임워크 디스패처에 다시 생성합니다. 그런 다음 디스패처는 오케스트레이터에서 예약한 새 작업(예: 하나 이상의 자식 함수 호출 또는 지속성 타이머 예약)을 스토리지에 커밋합니다. 투명한 커밋 작업은 오케스트레이션 인스턴스의 실행 기록에 추가됩니다. 기록은 스토리지 테이블에 저장됩니다. 그런 다음 커밋 작업은 실제 작업을 예약하는 큐에 메시지를 추가합니다. 이 시점에서 오케스트레이터 함수는 메모리에서 언로드할 수 있습니다.

오케스트레이션 함수에서 더 많은 작업을 수행하는 경우(예: 응답 메시지를 받거나 지속성 타이머가 만료되는 경우), 오케스트레이터는 전체 함수를 처음부터 다시 시작하고 다시 실행하여 로컬 상태를 다시 작성합니다. 재생 중에 코드에서 함수를 호출하거나 다른 비동기 작업을 수행하려고 하는 경우, 지속성 작업 프레임워크는 현재 오케스트레이션의 실행 기록을 참조합니다. [활동 함수](durable-functions-types-features-overview.md#activity-functions)가 이미 실행되어 결과를 생성한 경우, 해당 함수의 결과를 재생하고 오케스트레이터 코드가 계속 실행됩니다. 함수 코드가 완료되거나 새 비동기 작업이 예약될 때까지 재생이 계속됩니다.

> [!NOTE]
> 재생 패턴이 정확하고 안정적으로 작동하려면 오케스트레이터 함수 코드가 *결정적*이어야 합니다. 오케스트레이터 함수의 코드 제한 사항에 대한 자세한 내용은 [오케스트레이터 함수 코드 제약 조건](durable-functions-code-constraints.md) 항목을 참조하세요.

> [!NOTE]
> 오케스트레이터 함수에서 로그 메시지를 내보내는 경우 재생 동작으로 인해 중복된 로그 메시지를 내보낼 수 있습니다. 이 동작이 발생하는 이유와 해결하는 방법에 대한 자세한 내용은 [로깅](durable-functions-diagnostics.md#app-logging) 항목을 참조하세요.

## <a name="orchestration-history"></a>오케스트레이션 기록

지속성 작업 프레임워크의 이벤트 소싱 동작은 작성하는 오케스트레이터 함수 코드와 밀접하게 연관되어 있습니다. 다음 오케스트레이터 함수와 같은 활동 체이닝 오케스트레이터 함수가 있다고 가정해 보세요.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

각 `await`(C#) 또는 `yield`(JavaScript/Python) 문에서 지속성 작업 프레임워크는 함수의 실행 상태 검사점을 일부 지속성 스토리지 백 엔드(일반적으로 Azure Table 스토리지)에 설정합니다. 이 상태를 *오케스트레이션 기록*이라고 합니다.

### <a name="history-table"></a>기록 테이블

일반적으로 지속성 작업 프레임워크는 각 검사점에서 다음을 수행합니다.

1. 실행 기록을 Azure Storage 테이블에 저장합니다.
2. 오케스트레이터에서 호출하려고 하는 함수의 메시지를 큐에 넣습니다.
3. 오케스트레이터 자체의 메시지(예: 지속성 타이머 메시지)를 큐에 넣습니다.&mdash;

검사점이 완료되면 오케스트레이터 함수는 더 많은 작업이 수행될 때까지 메모리에서 자유롭게 제거할 수 있습니다.

> [!NOTE]
> Azure Storage는 테이블 스토리지와 큐 간의 데이터 저장에 대한 트랜잭션 보장을 제공하지 않습니다. 지속성 함수 스토리지 공급자는 실패를 처리하기 위해 *최종 일관성* 패턴을 사용합니다. 이러한 패턴은 검사점 중간에 연결이 충돌하거나 손실되는 경우 데이터가 손실되지 않도록 합니다.

완료되면 앞에서 보여 준 함수의 기록은 Azure Table Storage에서 다음 표와 비슷합니다(예시를 위해 간략히 설명함).

| PartitionKey(InstanceId)                     | EventType             | 타임스탬프               | 입력 | Name             | 결과                                                    | 상태 |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Completed           |

열 값에 대한 몇 가지 참고 사항:

* **PartitionKey**: 오케스트레이션 인스턴스 ID를 포함합니다.
* **EventType**: 이벤트 유형을 나타냅니다. 다음 유형 중 하나일 수 있습니다.
  * **OrchestrationStarted**: 오케스트레이터 함수가 대기 상태에서 다시 시작되었거나 처음으로 실행되고 있습니다. `Timestamp` 열은 `CurrentUtcDateTime`(.NET), `currentUtcDateTime`(JavaScript) 및 `current_utc_datetime`(Python) API에 대한 결정적 값을 채우는 데 사용됩니다.
  * **ExecutionStarted**: 오케스트레이터 함수가 처음으로 실행되기 시작했습니다. 또한 이 이벤트의 `Input` 열에 함수 입력이 포함됩니다.
  * **TaskScheduled**: 작업 함수가 예약되었습니다. `Name` 열에 작업 함수의 이름이 캡처됩니다.
  * **TaskCompleted**: 작업 함수가 완료되었습니다. `Result` 열에 함수 결과가 있습니다.
  * **TimerCreated**: 지속성 타이머가 만들어졌습니다. `FireAt` 열에 타이머가 만료되는 예약된 UTC 시간이 포함됩니다.
  * **TimerFired**: 지속성 타이머가 실행되었습니다.
  * **EventRaised**: 외부 이벤트가 오케스트레이션 인스턴스로 전송되었습니다. `Name` 열에 이벤트 이름이, `Input` 열에 이벤트의 페이로드가 캡처됩니다.
  * **OrchestratorCompleted**: 오케스트레이터 함수가 기다리고 있습니다.
  * **ContinueAsNew**: 오케스트레이터 함수가 완료되고 새 상태로 다시 시작되었습니다. `Result` 열에 다시 시작되는 인스턴스의 입력으로 사용되는 값이 포함됩니다.
  * **ExecutionCompleted**: 오케스트레이터 함수가 실행되어 완료되었거나 실패했습니다. `Result` 열에 함수 또는 오류 세부 정보의 출력이 저장됩니다.
* **타임스탬프**: 기록 이벤트의 UTC 타임스탬프입니다.
* **Name**: 호출된 함수의 이름입니다.
* **입력**: JSON 형식의 함수 입력입니다.
* **결과**: 함수의 출력, 즉, 반환 값입니다.

> [!WARNING]
> 디버깅 도구로 유용하지만 이 테이블에 대한 종속성은 사용하지 마세요. 지속성 함수 확장이 진화함에 따라 변경될 수 있습니다.

함수가 `await`(C#) 또는 `yield`(JavaScript/Python)에서 다시 시작될 때마다 지속성 작업 프레임워크는 오케스트레이터 함수를 처음부터 다시 실행합니다. 다시 실행할 때마다 실행 기록을 참조하여 현재 비동기 작업이 수행되었는지 여부를 확인합니다.  작업이 수행되었으면 프레임워크에서 해당 작업의 결과를 즉시 재생하고 다음 `await`(C#) 또는 `yield`(JavaScript/Python)로 이동합니다. 전체 기록이 재생될 때까지 이 프로세스가 계속됩니다. 현재 기록이 재생되면 로컬 변수가 이전 값으로 복원됩니다.

## <a name="features-and-patterns"></a>기능 및 패턴

다음 섹션에서는 오케스트레이터 함수의 기능과 패턴에 대해 설명합니다.

### <a name="sub-orchestrations"></a>하위 오케스트레이션

오케스트레이터 함수는 활동 함수뿐만 아니라 다른 오케스트레이터 함수도 호출할 수 있습니다. 예를 들어 오케스트레이터 함수 라이브러리에서 더 큰 오케스트레이션을 작성할 수 있습니다. 또는 오케스트레이터 함수의 여러 인스턴스를 병렬로 실행할 수 있습니다.

자세한 내용 및 예제는 [하위 오케스트레이션](durable-functions-sub-orchestrations.md) 문서를 참조하세요.

### <a name="durable-timers"></a>지속형 타이머

오케스트레이션에서는 *지속성 타이머*를 예약하여 지연을 구현하거나 비동기 작업에 대한 시간 제한 처리를 설정할 수 있습니다. 지속성 타이머는 `Thread.Sleep` 및 `Task.Delay`(C#) 또는 `setTimeout()` 및 `setInterval()`(JavaScript) 또는 `time.sleep()`(Python) 대신 오케스트레이터 함수에서 사용합니다.

자세한 내용 및 예제는 [지속성 타이머](durable-functions-timers.md) 문서를 참조하세요.

### <a name="external-events"></a>외부 이벤트

오케스트레이터 함수는 외부 이벤트가 오케스트레이션 인스턴스를 업데이트할 때까지 대기할 수 있습니다. 이 Durable Functions 기능은 종종 사용자 개입 또는 다른 외부 콜백을 처리하는 데 유용합니다.

자세한 내용 및 예제는 [외부 이벤트](durable-functions-external-events.md) 문서를 참조하세요.

### <a name="error-handling"></a>오류 처리

오케스트레이터 함수는 프로그래밍 언어의 오류 처리 기능을 사용할 수 있습니다. `try`/`catch`와 같은 기존 패턴은 오케스트레이션 코드에서 지원됩니다.

또한 오케스트레이터 함수는 호출하는 활동 또는 하위 오케스트레이터 함수에 재시도 정책을 추가할 수 있습니다. 활동 또는 하위 오케스트레이터 함수가 예외로 인해 실패하는 경우 지정된 재시도 정책에서 지정된 횟수만큼 실행을 자동으로 지연하고 다시 시도할 수 있습니다.

> [!NOTE]
> 오케스트레이터 함수에 처리되지 않은 예외가 있는 경우 오케스트레이션 인스턴스가 `Failed` 상태로 완료됩니다. 실패한 오케스트레이션 인스턴스는 다시 시도할 수 없습니다.

자세한 내용 및 예제는 [오류 처리](durable-functions-error-handling.md) 문서를 참조하세요.

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>임계 섹션(Durable Functions 2.x, 현재 .NET에만 해당)

오케스트레이션 인스턴스는 단일 스레드이므로 오케스트레이션 *내*의 경합 상태에 대해 걱정할 필요가 없습니다. 그러나 오케스트레이션에서 외부 시스템과 상호 작용할 때 경합 상태가 발생할 수 있습니다. 오케스트레이터 함수는 외부 시스템과 상호 작용할 때 경합 상태를 완화하기 위해 .NET의 `LockAsync` 메서드를 사용하여 *임계 영역*을 정의할 수 있습니다.

다음 샘플 코드에서는 임계 영역을 정의하는 오케스트레이터 함수를 보여 줍니다. `LockAsync` 메서드를 사용하여 임계 영역에 들어갑니다. 이 메서드를 사용하려면 잠금 상태를 지속적으로 관리하는 [지속성 엔터티](durable-functions-entities.md)에 하나 이상의 참조를 전달해야 합니다. 이 오케스트레이션의 단일 인스턴스만 임계 영역의 코드를 한 번에 실행할 수 있습니다.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

`LockAsync`는 지속성 잠금을 획득하고, 삭제될 때 임계 영역을 종료하는 `IDisposable`을 반환합니다. 이 `IDisposable` 결과를 `using` 블록과 함께 사용하여 임계 영역에 대한 구문 표현을 가져올 수 있습니다. 오케스트레이터 함수가 임계 영역에 들어가면 하나의 인스턴스만 해당 코드 블록을 실행할 수 있습니다. 이전 인스턴스에서 임계 영역을 종료할 때까지 임계 영역에 들어가려는 다른 모든 인스턴스는 차단됩니다.

또한 임계 영역 기능은 지속성 엔터티에 대한 변경 내용을 조정하는 데에도 유용합니다. 임계 영역에 대한 자세한 내용은 [지속성 엔터티 "엔터티 조정"](durable-functions-entities.md#entity-coordination) 항목을 참조하세요.

> [!NOTE]
> 임계 영역은 Durable Functions 2.0 이상에서 사용할 수 있습니다. 현재는 .NET 오케스트레이션만 이 기능을 구현합니다.

### <a name="calling-http-endpoints-durable-functions-2x"></a>HTTP 엔드포인트 호출 (Durable Functions 2.x)

[오케스트레이터 함수 코드 제약 조건](durable-functions-code-constraints.md)에서 설명한 대로 오케스트레이터 함수는 I/O를 수행할 수 없습니다. 이 제한 사항에 대한 일반적인 해결 방법은 활동 함수에서 I/O를 수행해야 하는 코드를 래핑하는 것입니다. 외부 시스템과 상호 작용하는 오케스트레이션은 활동 함수를 사용하여 HTTP 호출을 수행하고 결과를 오케스트레이션에 반환합니다.

# <a name="c"></a>[C#](#tab/csharp)

오케스트레이터 함수는 이 일반적인 패턴을 간소화하기 위해 `CallHttpAsync` 메서드를 사용하여 HTTP API를 직접 호출할 수 있습니다.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

이 메서드는 기본 요청/응답 패턴을 지원하는 것 외에도 일반적인 비동기 HTTP 202 폴링 패턴을 자동으로 처리하도록 지원하고, [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용한 외부 서비스 인증도 지원합니다.

자세한 내용 및 예제는 [HTTP 기능](durable-functions-http-features.md) 문서를 참조하세요.

> [!NOTE]
> 오케스트레이터 함수에서 HTTP 엔드포인트를 직접 호출하는 기능은 Durable Functions 2.0 이상에서 사용할 수 있습니다.

### <a name="passing-multiple-parameters"></a>여러 매개 변수 전달

활동 함수에는 여러 매개 변수를 직접 전달할 수 없습니다. 개체 또는 복합 개체의 배열로 전달하는 것이 좋습니다.

# <a name="c"></a>[C#](#tab/csharp)

.NET에서는 [ValueTuples](/dotnet/csharp/tuples) 개체를 사용할 수도 있습니다. 다음 샘플은 [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples)로 추가된 [ValueTuples](/dotnet/csharp/tuples)의 새로운 기능을 사용하는 것입니다.

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>오케스트레이터

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather` 작업

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>오케스트레이터

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` 작업

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오케스트레이터 코드 제약 조건](durable-functions-code-constraints.md)
