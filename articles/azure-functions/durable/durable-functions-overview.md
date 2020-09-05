---
title: 지속성 함수 개요 - Azure
description: Azure Functions의 지속성 함수 확장을 소개합니다.
author: cgillum
ms.topic: overview
ms.date: 03/12/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: d1c4f62f19a36867ebc85a98b0cd38bbbf8ce757
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660685"
---
# <a name="what-are-durable-functions"></a>Durable Functions란?

*Durable Functions*는 서버리스 컴퓨팅 환경에서 상태 저장 함수를 작성할 수 있는 [Azure Functions](../functions-overview.md)의 확장입니다. 확장을 통해 Azure Functions 프로그래밍 모델에서 [*오케스트레이터 함수*](durable-functions-orchestrations.md)를 작성하여 상태 저장 워크플로를 정의하고, [*엔터티 함수*](durable-functions-entities.md)를 작성하여 상태 저장 엔터티를 정의할 수 있습니다. 확장은 내부적으로 상태, 검사점 및 다시 시작을 관리하므로 비즈니스 논리에 집중할 수 있습니다.

## <a name="supported-languages"></a><a name="language-support"></a>지원되는 언어

Durable Functions는 현재 다음 언어를 지원합니다.

* **C#** : [미리 컴파일된 클래스 라이브러리](../functions-dotnet-class-library.md) 및 [C# 스크립트](../functions-reference-csharp.md) 모두
* **JavaScript**: Azure Functions 런타임 버전 2.x에서만 지원됩니다. Durable Functions 확장 버전 1.7.0 이상이 필요합니다. 
* **Python**: Durable Functions 확장 버전 1.8.5 이상이 필요합니다. 
* **F#** : 미리 컴파일된 클래스 라이브러리 및 F# 스크립트. F# 스크립트는 Azure Functions 런타임 버전 1.x에서만 지원됩니다.
* **PowerShell**: Durable Functions에 대한 지원은 현재 공개 미리 보기로 제공됩니다. Azure Functions 런타임 및 PowerShell 7 버전 3.x에서만 지원됩니다. Durable Functions 확장 버전 2.2.2 또는 이후 버전이 필요합니다. 현재 지원되는 패턴은 다음과 같습니다. [함수 체이닝](#chaining), [팬아웃/팬인](#fan-in-out), [비동기 HTTP API](#async-http).

Durable Functions는 모든 [Azure Functions 언어](../supported-languages.md)를 지원하는 것을 목표로 합니다. 추가 언어를 지원하기 위한 최신 작업 상태는 [Durable Functions 문제 목록](https://github.com/Azure/azure-functions-durable-extension/issues)를 참조하세요.

Azure Functions와 마찬가지로 [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) 및 [Azure Portal](durable-functions-create-portal.md)을 사용하여 Durable Functions를 개발하는 데 도움이 되는 템플릿이 있습니다.

## <a name="application-patterns"></a>애플리케이션 패턴

Durable Functions에 대한 기본 사용 사례는 서버리스 애플리케이션에서 복잡한 상태 저장 조정 요구 사항을 단순화하는 것입니다. 다음 섹션에서는 Durable Functions의 이점을 누릴 수 있는 일반적인 애플리케이션 패턴에 대해 설명합니다.

* [함수 체이닝](#chaining)
* [팬아웃/팬인](#fan-in-out)
* [비동기 HTTP API](#async-http)
* [Monitoring](#monitoring)
* [사용자 개입](#human)
* [집계(상태 저장 엔터티)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>패턴 #1: 함수 체이닝

함수 체이닝 패턴에서는 일련의 함수가 특정 순서로 실행됩니다. 이 패턴에서 한 함수의 출력은 다른 함수의 입력에 적용됩니다.

![함수 체이닝 패턴의 다이어그램](./media/durable-functions-concepts/function-chaining.png)

다음 예제와 같이 Durable Functions를 사용하여 함수 체이닝 패턴을 간결하게 구현할 수 있습니다.

이 예제에서 `F1`, `F2`, `F3` 및 `F4` 값은 동일한 함수 앱에 있는 다른 함수의 이름입니다. 일반적인 명령적 코딩 구문을 사용하여 제어 흐름을 구현할 수 있습니다. 코드는 위에서 아래로 실행됩니다. 코드에는 조건부 및 루프와 같은 기존 언어 제어 흐름 의미 체계가 포함될 수 있습니다. `try`/`catch`/`finally` 블록에는 오류 처리 논리가 포함될 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

`context` 매개 변수를 사용하여 이름을 기준으로 다른 함수를 호출하고, 매개 변수를 전달하며, 함수 출력을 반환할 수 있습니다. 코드에서 `await`를 호출할 때마다 Durable Functions 프레임워크는 현재 함수 인스턴스의 진행률 검사점을 설정합니다. 프로세스 또는 가상 머신이 실행 중간에 재생되면 함수 인스턴스가 이전 `await` 호출에서 다시 시작됩니다. 자세한 내용은 다음 섹션인 '패턴 #2: 팬아웃/팬인'을 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

`context.df` 개체를 사용하여 이름을 기준으로 다른 함수를 호출하고, 매개 변수를 전달하며, 함수 출력을 반환할 수 있습니다. 코드에서 `yield`를 호출할 때마다 Durable Functions 프레임워크는 현재 함수 인스턴스의 진행률 검사점을 설정합니다. 프로세스 또는 가상 머신이 실행 중간에 재생되면 함수 인스턴스가 이전 `yield` 호출에서 다시 시작됩니다. 자세한 내용은 다음 섹션인 '패턴 #2: 팬아웃/팬인'을 참조하세요.

> [!NOTE]
> JavaScript의 `context` 개체는 [함수 컨텍스트](../functions-reference-node.md#context-object) 전체를 나타냅니다. 기본 컨텍스트의 `df` 속성을 사용하여 Durable Functions 컨텍스트에 액세스하세요.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

`context` 개체를 사용하여 이름을 기준으로 다른 함수를 호출하고, 매개 변수를 전달하며, 함수 출력을 반환할 수 있습니다. 코드에서 `yield`를 호출할 때마다 Durable Functions 프레임워크는 현재 함수 인스턴스의 진행률 검사점을 설정합니다. 프로세스 또는 가상 머신이 실행 중간에 재생되면 함수 인스턴스가 이전 `yield` 호출에서 다시 시작됩니다. 자세한 내용은 다음 섹션인 '패턴 #2: 팬아웃/팬인'을 참조하세요.

> [!NOTE]
> Python의 `context` 개체는 오케스트레이션 컨텍스트를 나타냅니다. 오케스트레이션 컨텍스트의 `function_context` 속성을 사용하여 주 Azure Functions 컨텍스트에 액세스하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-ActivityFunction -FunctionName 'F1'
$Y = Invoke-ActivityFunction -FunctionName 'F2' -Input $X
$Z = Invoke-ActivityFunction -FunctionName 'F3' -Input $Y
Invoke-ActivityFunction -FunctionName 'F4' -Input $Z
```

`Invoke-ActivityFunction` 명령을 사용하여 이름을 기준으로 다른 함수를 호출하고, 매개 변수를 전달하며, 함수 출력을 반환할 수 있습니다. 코드에서 `NoWait` 스위치 없이 `Invoke-ActivityFunction`을 호출할 때마다 Durable Functions 프레임워크는 현재 함수 인스턴스의 진행률 검사점을 설정합니다. 프로세스 또는 가상 머신이 실행 중간에 재생되면 함수 인스턴스가 이전 `Invoke-ActivityFunction` 호출에서 다시 시작됩니다. 자세한 내용은 다음 섹션인 '패턴 #2: 팬아웃/팬인'을 참조하세요.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>패턴 #2: 팬아웃/팬인

팬아웃/팬인 패턴에서는 여러 함수를 병렬로 실행한 다음, 모든 함수가 완료될 때까지 기다립니다. 일부 집계 작업은 함수에서 반환된 결과에 대해 수행되는 경우가 많습니다.

![팬아웃/팬인 패턴의 다이어그램](./media/durable-functions-concepts/fan-out-fan-in.png)

일반 함수를 사용하면 함수에서 여러 메시지를 큐에 보내도록 하여 팬아웃할 수 있습니다. 다시 팬인하는 것은 훨씬 더 어렵습니다. 일반 함수에서 팬인하려면 큐 트리거 함수가 종료되는 시점을 추적한 다음, 함수 출력을 저장하는 코드를 작성해야 합니다.

Durable Functions 확장에서는 비교적 간단한 코드를 사용하여 이 패턴을 처리합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

팬아웃 작업은 `F2` 함수의 여러 인스턴스에 배포됩니다. 작업은 동적 작업 목록을 사용하여 추적됩니다. 호출된 모든 함수가 완료될 때까지 기다리기 위해 `Task.WhenAll`이 호출됩니다. 그런 다음, `F2` 함수 출력이 동적 작업 목록에서 집계되어 `F3` 함수에 전달됩니다.

`Task.WhenAll`의 `await` 호출에서 검사점이 자동으로 설정되어 잠재적인 중간 충돌 또는 다시 부팅에서 이미 완료된 작업을 다시 시작할 필요가 없습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

팬아웃 작업은 `F2` 함수의 여러 인스턴스에 배포됩니다. 작업은 동적 작업 목록을 사용하여 추적됩니다. 호출된 모든 함수가 완료될 때까지 기다리기 위해 `context.df.Task.all` API가 호출됩니다. 그런 다음, `F2` 함수 출력이 동적 작업 목록에서 집계되어 `F3` 함수에 전달됩니다.

`context.df.Task.all`의 `yield` 호출에서 검사점이 자동으로 설정되어 잠재적인 중간 충돌 또는 다시 부팅에서 이미 완료된 작업을 다시 시작할 필요가 없습니다.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

팬아웃 작업은 `F2` 함수의 여러 인스턴스에 배포됩니다. 작업은 동적 작업 목록을 사용하여 추적됩니다. 호출된 모든 함수가 완료될 때까지 기다리기 위해 `context.task_all` API가 호출됩니다. 그런 다음, `F2` 함수 출력이 동적 작업 목록에서 집계되어 `F3` 함수에 전달됩니다.

`context.task_all`의 `yield` 호출에서 검사점이 자동으로 설정되어 잠재적인 중간 충돌 또는 다시 부팅에서 이미 완료된 작업을 다시 시작할 필요가 없습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-ActivityFunction -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-ActivityFunction -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-ActivityFunction -FunctionName 'F3' -Input $Total
```

팬아웃 작업은 `F2` 함수의 여러 인스턴스에 배포됩니다. `F2` 함수 호출에서 `NoWait` 스위치 사용법에 유의하세요. 이 스위치를 사용하면 오케스트레이터가 작업을 완료하지 않고 `F2`를 계속 호출할 수 있습니다. 작업은 동적 작업 목록을 사용하여 추적됩니다. 호출된 모든 함수가 완료될 때까지 기다리기 위해 `Wait-ActivityFunction` 명령이 호출됩니다. 그런 다음, `F2` 함수 출력이 동적 작업 목록에서 집계되어 `F3` 함수에 전달됩니다.

`Wait-ActivityFunction` 호출에서 발생하는 검사점이 자동으로 설정되어 잠재적인 중간 충돌 또는 다시 부팅에서 이미 완료된 작업을 다시 시작할 필요가 없습니다.

---

> [!NOTE]
> 드문 경우이지만 활동 함수가 완료된 후 해당 완료가 오케스트레이션 기록에 저장되기 전에 창에서 충돌이 발생할 수 있습니다. 이 경우 프로세스가 복구되면 활동 함수가 처음부터 다시 실행됩니다.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>패턴 #3: 비동기 HTTP API

비동기 HTTP API 패턴은 외부 클라이언트와 장기 실행 작업의 상태를 조정하는 문제를 해결합니다. 이 패턴을 구현하는 일반적인 방법은 HTTP 엔드포인트에서 장기 실행 작업을 트리거하도록 하는 것입니다. 그런 다음, 클라이언트를 클라이언트에서 폴링하는 상태 엔드포인트로 리디렉션하여 작업이 완료된 시점을 알아봅니다.

![HTTP API 패턴의 다이어그램](./media/durable-functions-concepts/async-http-api.png)

Durable Functions는 이 패턴에 대한 **기본 제공 지원**을 제공하여 장기 실행 함수의 실행과 상호 작용하기 위해 작성해야 하는 코드를 간소화하거나 제거합니다. 예를 들어 Durable Functions 빠른 시작 샘플([C#](durable-functions-create-first-csharp.md) 및 [JavaScript](quickstart-js-vscode.md))에서는 새 오케스트레이터 함수 인스턴스를 시작하는 데 사용할 수 있는 간단한 REST 명령을 보여 줍니다. 인스턴스가 시작되면 확장에서 오케스트레이터 함수 상태를 쿼리하는 웹후크 HTTP API를 공개합니다. 

다음 예제에서는 오케스트레이터를 시작하고 해당 상태를 쿼리하는 REST 명령을 보여 줍니다. 명확히 하기 위해 예제에서 일부 프로토콜 세부 정보가 생략되었습니다.

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Durable Functions 런타임에서 사용자를 위해 상태를 관리하므로 사용자 고유의 상태 추적 메커니즘을 구현할 필요가 없습니다.

Durable Functions 확장은 장기 실행 오케스트레이션을 관리하는 기본 제공 HTTP API를 공개합니다. 또는 사용자 고유의 함수 트리거(예: HTTP, 큐 또는 Azure Event Hubs)와 [오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client)을 사용하여 이 패턴을 직접 구현할 수 있습니다. 예를 들어 큐 메시지를 사용하여 종료를 트리거할 수 있습니다. 또는 생성된 키를 인증에 사용하는 기본 제공 HTTP API 대신 Azure Active Directory 인증 정책으로 보호되는 HTTP 트리거를 사용할 수 있습니다.

자세한 내용은 [HTTP 기능](durable-functions-http-features.md) 문서를 참조하세요. 이 문서에서는 Durable Functions 확장을 사용하여 HTTP를 통해 비동기 장기 실행 프로세스를 공개할 수 있는 방법을 설명합니다.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>패턴 #4: 모니터

모니터 패턴은 워크플로에서 유연한 되풀이 프로세스를 나타냅니다. 예를 들어 특정 조건이 충족될 때까지 폴링하는 경우가 있습니다. 일반 [타이머 트리거](../functions-bindings-timer.md)를 사용하여 정기적인 정리 작업과 같은 기본 시나리오를 처리할 수 있지만, 간격이 정적이고 인스턴스 수명 관리가 복잡해집니다. Durable Functions를 사용하여 유연한 되풀이 간격을 만들고, 작업 수명을 관리하며, 단일 오케스트레이션에서 여러 모니터 프로세스를 만들 수 있습니다.

모니터 패턴의 예로 앞의 비동기 HTTP API 시나리오를 반대로 바꾸는 것이 있습니다. 장기 실행 모니터는 외부 클라이언트의 엔드포인트를 공개하여 장기 실행 작업을 모니터링하는 대신, 외부 엔드포인트를 사용하여 상태 변경을 기다립니다.

![모니터 패턴의 다이어그램](./media/durable-functions-concepts/monitor.png)

몇 줄의 코드에서 Durable Functions를 사용하여 임의의 엔드포인트를 관찰하는 여러 모니터를 만들 수 있습니다. 조건이 충족되면 모니터에서 실행을 종료하거나, 다른 함수에서 지속성 오케스트레이션 클라이언트를 사용하여 모니터를 종료할 수 있습니다. 특정 조건(예: 지수 백오프)에 따라 모니터의 `wait` 간격을 변경할 수 있습니다. 

다음 코드에서는 기본 모니터를 구현합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

모니터는 현재 PowerShell에서 지원되지 않습니다.

---

요청을 받으면 해당 작업 ID에 대해 새 오케스트레이션 인스턴스가 만들어집니다. 조건이 충족되고 루프가 종료될 때까지 인스턴스는 상태를 폴링합니다. 지속성 타이머는 폴링 간격을 제어합니다. 그런 다음, 더 많은 작업을 수행하거나 오케스트레이션을 종료할 수 있습니다. `nextCheck`가 `expiryTime`을 초과하면 모니터가 종료됩니다.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>패턴 #5: 사용자 개입

자동화된 많은 프로세스에는 일종의 사용자 개입이 포함됩니다. 사용자는 클라우드 서비스만큼 가용성과 응답성이 높지 않으므로 자동화된 프로세스에 사람을 참여시키는 것은 까다로운 작업입니다. 자동화된 프로세스에서는 시간 제한 및 보정 논리를 사용하여 이 상호 작용을 허용할 수 있습니다.

승인 프로세스는 사용자 개입을 수반하는 비즈니스 프로세스의 한 예입니다. 특정 달러 금액을 초과하는 경비 보고서에는 관리자의 승인이 필요할 수도 있습니다. 관리자가 72시간 이내에 경비 보고서를 승인하지 않으면(관리자가 휴가 중이었을 수도 있음) 에스컬레이션 프로세스가 시작되어 다른 사람(아마도 관리자의 관리자)의 승인을 받습니다.

![사용자 개입 패턴의 다이어그램](./media/durable-functions-concepts/approval.png)

이 예제에서는 오케스트레이터 함수를 사용하여 패턴을 구현할 수 있습니다. 오케스트레이터는 [지속성 타이머](durable-functions-timers.md)를 사용하여 승인을 요청합니다. 시간이 초과되면 오케스트레이터가 에스컬레이션됩니다. 오케스트레이터는 사용자 개입으로 생성된 알림과 같은 [외부 이벤트](durable-functions-external-events.md)를 기다립니다.

다음 예제에서는 사용자 개입 패턴을 보여 주는 승인 프로세스를 만듭니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

지속성 타이머를 만들려면 `context.CreateTimer`를 호출합니다. `context.WaitForExternalEvent`에서 알림을 받습니다. 그런 다음, `Task.WhenAny`를 호출하여 에스컬레이션할지(시간 제한이 먼저 발생함) 또는 승인을 처리할지(시간 제한 전에 승인이 수신됨)를 결정합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

지속성 타이머를 만들려면 `context.df.createTimer`를 호출합니다. `context.df.waitForExternalEvent`에서 알림을 받습니다. 그런 다음, `context.df.Task.any`를 호출하여 에스컬레이션할지(시간 제한이 먼저 발생함) 또는 승인을 처리할지(시간 제한 전에 승인이 수신됨)를 결정합니다.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

지속성 타이머를 만들려면 `context.create_timer`를 호출합니다. `context.wait_for_external_event`에서 알림을 받습니다. 그런 다음, `context.task_any`를 호출하여 에스컬레이션할지(시간 제한이 먼저 발생함) 또는 승인을 처리할지(시간 제한 전에 승인이 수신됨)를 결정합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

사용자 상호 작용은 현재 PowerShell에서 지원되지 않습니다.

---

외부 클라이언트는 [기본 제공 HTTP API](durable-functions-http-api.md#raise-event)를 사용하여 이벤트 알림을 대기 중인 오케스트레이터 함수에 전달할 수 있습니다.

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

동일한 함수 앱의 다른 함수에서 지속성 오케스트레이션 클라이언트를 사용하여 이벤트를 발생시킬 수도 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

사용자 상호 작용은 현재 PowerShell에서 지원되지 않습니다.

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>패턴 #6: 집계(상태 저장 엔터티)

여섯 번째 패턴은 일정 기간 동안의 이벤트 데이터를 주소 지정 가능한 단일 *엔터티*로 집계하는 것입니다. 이 패턴에서 집계되는 데이터는 여러 원본에서 제공되거나, 일괄 처리로 전달되거나, 장기간에 걸쳐 분산될 수 있습니다. 집계는 도착한 이벤트 데이터에 대한 작업을 수행해야 할 수 있으며, 외부 클라이언트는 집계된 데이터를 쿼리해야 할 수도 있습니다.

![집계 다이어그램](./media/durable-functions-concepts/aggregator.png)

일반적인 상태 비저장 함수를 사용하여 이러한 패턴을 구현하려고 할 때 어려운 점은 동시성 제어가 엄청난 과제가 된다는 것입니다. 여러 스레드에서 동일한 데이터를 동시에 수정하는 것과 집계가 한 번에 하나의 VM에서만 실행되도록 하는 것에 대해 걱정할 필요가 있습니다.

[지속성 엔터티](durable-functions-entities.md)를 사용하여 이 패턴을 단일 함수로 쉽게 구현할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

지속성 엔터티는 .NET에서 클래스로 모델링할 수도 있습니다. 작업 목록이 고정되어 커지는 경우 이 모델이 유용할 수 있습니다. 다음 예제에서는 .NET 클래스와 메서드를 사용하여 `Counter` 엔터티를 동일하게 구현합니다.

```csharp
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

지속성 엔터티는 현재 Python에서 지원되지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

지속성 엔터티는 현재 PowerShell에서 지원되지 않습니다.

---

클라이언트는 [엔터티 클라이언트 바인딩](durable-functions-bindings.md#entity-client)을 사용하여 엔터티 함수에 대한 *작업*("신호 보내기"라고도 함)을 큐에 넣을 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> 동적으로 생성된 프록시는 .NET에서 엔터티에 대한 신호를 형식이 안전한 방식으로 보내는 데에도 사용할 수 있습니다. 그리고 신호를 보내는 것 외에도 클라이언트는 오케스트레이션 클라이언트 바인딩에서 [형식이 안전한 메서드](durable-functions-bindings.md#entity-client-usage)를 사용하여 엔터티 함수의 상태를 쿼리할 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

지속성 엔터티는 현재 Python에서 지원되지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

지속성 엔터티는 현재 PowerShell에서 지원되지 않습니다.

---

엔터티 함수는 C# 및 JavaScript에 대해 [Durable Functions 2.0](durable-functions-versions.md) 이상에서 사용할 수 있습니다.

## <a name="the-technology"></a>기술

Durable Functions 확장은 내부적으로 코드에서 워크플로를 작성하는 데 사용되는 GitHub의 오픈 소스 라이브러리인 [지속성 작업 프레임워크](https://github.com/Azure/durabletask)를 기반으로 하여 빌드됩니다. Azure Functions가 Azure WebJobs의 서버리스 진화인 것처럼 Durable Functions는 지속성 작업 프레임워크의 서버리스 진화입니다. Microsoft 및 기타 조직에서는 지속성 작업 프레임워크를 광범위하게 사용하여 중요 업무용 프로세스를 자동화합니다. 이는 서버를 사용하지 않는 Azure Functions 환경에 적합합니다.

## <a name="code-constraints"></a>코드 제약 조건

안정적이고 장기 실행되는 실행을 보장하기 위해 오케스트레이터 함수에는 따라야 하는 코딩 규칙 세트가 있습니다. 자세한 내용은 [오케스트레이터 함수 코드 제약 조건](durable-functions-code-constraints.md) 문서를 참조하세요.

## <a name="billing"></a>결제

Durable Functions 요금은 Azure Functions와 동일하게 청구됩니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)을 참조하세요. Azure Functions [소비 계획](../functions-scale.md#consumption-plan)에서 오케스트레이터 함수를 실행할 때 알고 있어야 할 몇 가지 청구 동작이 있습니다. 이러한 동작에 대한 자세한 내용은 [Durable Functions 청구](durable-functions-billing.md) 문서를 참조하세요.

## <a name="jump-right-in"></a>지금 바로 시작

다음 언어별 빠른 시작 자습서 중 하나를 완료하여 10분 이내에 Durable Functions를 시작할 수 있습니다.

* [Visual Studio 2019를 사용하는 C#](durable-functions-create-first-csharp.md)
* [Visual Studio Code를 사용하는 JavaScript](quickstart-js-vscode.md)
* [Visual Studio Code를 사용하는 Python](quickstart-python-vscode.md)
* [Visual Studio Code를 사용하는 PowerShell](quickstart-powershell-vscode.md)

이러한 빠른 시작에서는 "hello world" 지속성 함수를 로컬에서 만들고 테스트합니다. 그런 후 함수 코드를 Azure에 게시합니다. 생성한 함수는 다른 함수에 대한 호출을 오케스트레이션하고 함께 연결합니다.

## <a name="learn-more"></a>자세한 정보

다음 비디오는 Durable Functions의 이점을 강조합니다.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Durable Functions 및 기본 기술에 대한 자세한 내용은 다음 비디오를 참조하세요(.NET에 중점을 두고 있지만 개념은 지원되는 다른 언어에도 적용됨).

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Durable Functions는 [Azure Functions](../functions-overview.md)의 고급 확장이므로 모든 애플리케이션에 적합하지는 않습니다. 다른 Azure 오케스트레이션 기술과 비교해 보려면 [Azure Functions 및 Azure Logic Apps 비교](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Durable Functions 함수 형식 및 기능](durable-functions-types-features-overview.md)
