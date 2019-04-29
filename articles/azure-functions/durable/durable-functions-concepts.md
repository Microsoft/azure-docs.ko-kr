---
title: 지 속성 함수 패턴 및 Azure Functions에서 기술 개념
description: 어떻게 Azure Functions의 지 속성 함수 확장을 사용 하면 클라우드에서 상태 저장 코드 실행의 이점에 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: aa9563266f6b43e3bc2f21fbc0b340c86c5895ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60862088"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>지 속성 함수 패턴 및 기술 개념 (Azure Functions)

지 속성 함수는의 확장인 [Azure Functions](../functions-overview.md) 하 고 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). 서버 리스 환경에서 상태 저장 함수를 작성할 지 속성 함수를 사용할 수 있습니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다. 

이 문서에서는 Azure Functions 및 일반적인 구현 패턴에 대 한 지 속성 함수 확장의 동작에 대 한 정보를 자세히 설명 합니다. 정보가 도움이 될 수 있습니다 프로그램 개발 과제를 해결 하기 위해 지 속성 함수를 사용 하는 방법을 결정 합니다.

> [!NOTE]
> 지 속성 함수는 모든 응용 프로그램에 적합 하지 않은 Azure Functions의 고급 확장 합니다. 이 문서에서는 강력한 개념에 익숙하다고 있다고 가정 [Azure Functions](../functions-overview.md) 서버 리스 응용 프로그램 개발에 관련 된 문제입니다.

## <a name="patterns"></a>패턴

Durable Functions 유용할 수 있는 몇 가지 일반적인 응용 프로그램 패턴을 설명이 합니다.

### <a name="chaining"></a>패턴 #1: 함수 체이닝

패턴을 체 이닝 함수에서 특정 순서로 일련의 함수를 실행 합니다. 이 패턴에서는 한 함수의 출력이 다른 함수의 입력에 적용 됩니다.

![함수 패턴 체 이닝 다이어그램](./media/durable-functions-concepts/function-chaining.png)

패턴 예제에 나와 있는 것 처럼에 간결 하 게 연결 하는 함수를 구현 하려면 지 속성 함수를 사용할 수 있습니다.

#### <a name="c-script"></a>C# 스크립트

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

> [!NOTE]
> 미리 컴파일된 지 속성 함수를 작성 하는 간에 미묘한 차이가 있습니다 C# 미리 컴파일된 지 속성 함수를 작성 하 고는 C# 예제에 표시 되는 스크립트입니다. 에 C# 미리 컴파일된 함수에 해당 특성을 사용 하 여 지 속성 매개 변수를 데코레이팅 해야 합니다. 예로 `[OrchestrationTrigger]` 특성을 `DurableOrchestrationContext` 매개 변수입니다. 에 C# 매개 변수를 데코 레이트 된 제대로 되지 런타임 함수에 변수를 넣을 수 없습니다, 오류가 발생 하는 경우에 지 속성 함수를 미리 컴파일된 합니다. 더 많은 예제를 참조 하세요. 합니다 [GitHub의 azure functions-지 속성 확장 샘플](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples)합니다.

#### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

이 예제에서는 값 `F1`, `F2`를 `F3`, 및 `F4` 은 함수 앱에서 다른 함수의 이름입니다. 일반적인 명령적 코딩 구조를 사용 하 여 제어 흐름을 구현할 수 있습니다. 코드는 위에서 아래로 실행 됩니다. 코드에는 기존 언어 제어 흐름 의미 체계, 조건 및 루프와 같은 포함 될 수 있습니다. 오류 처리 논리를 포함할 수 있습니다 `try` / `catch` / `finally` 블록입니다.

사용할 수는 `context` 매개 변수 [DurableOrchestrationContext] \(.NET\) 고 `context.df` 개체 (JavaScript) 이름으로 다른 함수를 호출할 매개 변수를 전달 하 고 함수를 반환 합니다. 출력입니다. 호출 될 때마다 `await` (C#) 또는 `yield` (JavaScript), 지 속성 함수 프레임 워크 검사점 현재 함수 인스턴스의 진행 상태입니다. 함수 인스턴스가 이전에서 다시 시작 되는 경우 프로세스 또는 VM 실행 중간에 재활용 `await` 또는 `yield` 호출 합니다. 자세한 내용은 다음 섹션에서는 패턴 # 2를 참조 하세요. 팬 아웃/팬 인에서 합니다.

> [!NOTE]
> `context` javascript에서 개체 전체를 나타내는 [함수 컨텍스트](../functions-reference-node.md#context-object)뿐만 아니라,는 [DurableOrchestrationContext] 매개 변수입니다.

### <a name="fan-in-out"></a>패턴 #2: 팬 아웃/팬

팬 아웃/팬 인 패턴의와 동시에 여러 함수를 실행 한 다음 모든 함수가 완료 될 때까지 기다립니다. 종종 일부 집계 작업 함수에서 반환 되는 결과에서 수행 됩니다.

![다이어그램의 팬 아웃/팬 인 패턴](./media/durable-functions-concepts/fan-out-fan-in.png)

일반 함수를 사용 하 여 큐에 여러 메시지를 보내도록 하 여 팬아웃 수 있습니다. 다시 팬 인하은 훨씬 더 어렵습니다. 일반 함수에서, 팬에 큐 트리거 함수가 종료 및 다음 저장소 함수 출력 시기를 추적 하는 코드를 작성할 수 있습니다. 

지 속성 함수 확장 비교적 간단한 코드로이 패턴을 처리합니다.

#### <a name="c-script"></a>C# 스크립트

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

#### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

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

팬아웃 작업의 여러 인스턴스에 배포 되는 `F2` 함수입니다. 작업의 동적 목록을 사용 하 여 작업을 추적 합니다. .NET `Task.WhenAll` API 또는 JavaScript `context.df.Task.all` 대기를 완료 하려면 모든 호출된 함수에 대 한 API를 호출 합니다. 그런 다음, `F2` 동적 작업 목록에서 집계 되 고 전달 된 함수 출력의 `F3` 함수입니다.

발생 하는 자동 검사점을 `await` 또는 `yield` 에서 호출 `Task.WhenAll` 또는 `context.df.Task.all` 는 중간에 있는 잠재적 작동 중단 또는 다시 부팅 하지 않아도 이미 완료 된 작업을 다시 시작 되도록 합니다.

### <a name="async-http"></a>패턴 #3: 비동기 HTTP API

비동기 HTTP Api 패턴 외부 클라이언트를 사용 하 여 장기 실행 작업의 상태를 조정 하는 문제를 해결 합니다. 이 패턴을 구현 하는 일반적인 방법은 HTTP 트리거는 장기 실행 작업을 호출 하 여 됩니다. 그런 다음 클라이언트 폴링하여 작업이 완료 되 면 자세한 상태 끝점에 클라이언트를 리디렉션하십시오.

![HTTP API 패턴의 다이어그램](./media/durable-functions-concepts/async-http-api.png)

지 속성 함수는 장기 실행 함수 실행과 상호 작용을 작성 하는 코드를 단순화 하는 기본 제공 Api를 제공 합니다. Durable Functions 빠른 시작 샘플 ([ C# ](durable-functions-create-first-csharp.md) 하 고 [JavaScript](quickstart-js-vscode.md)) 새 오 케 스트레이 터 함수 인스턴스를 시작 하는 데 사용할 수 있는 간단한 REST 명령을 보여 줍니다. 인스턴스가 시작 된 후 확장 webhook 오 케 스트레이 터 함수 상태를 쿼리 하는 HTTP Api를 노출 합니다. 

다음 예제에서는 오 케 스트레이 터를 시작 하 고 해당 상태를 쿼리 하는 REST 명령을 보여 줍니다. 명확성을 위해 일부 세부 정보가 예제에서 생략되었습니다.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Durable Functions 런타임 상태를 관리 하기 때문에 자신의 상태 추적 메커니즘을 구현할 필요가 없습니다.

지 속성 함수 확장에 장기 실행 오케스트레이션을 관리 하는 기본 제공 웹 후크가 있습니다. 사용자 고유의 함수 트리거 (예: HTTP, 큐 또는 Azure Event Hubs)를 사용 하 여 직접이 패턴을 구현할 수 있습니다 및 `orchestrationClient` 바인딩. 예를 들어 종료를 트리거할 큐 메시지를 사용할 수 있습니다. 또는 인증을 위해 생성 된 키를 사용 하는 기본 제공 웹 후크 대신 Azure Active Directory 인증 정책에 의해 보호 되는 HTTP 트리거를 사용할 수 있습니다.

HTTP API 패턴을 사용 하는 방법의 몇 가지 예는 다음과 같습니다.

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> 개발 하는 경우 로컬에서 메서드를 사용 하려면 javascript에서 `DurableOrchestrationClient`, 환경 변수를 설정 해야 합니다 `WEBSITE_HOSTNAME` 하 `localhost:<port>` (예를 들어 `localhost:7071`). 이 요구 사항에 대 한 자세한 내용은 참조 하세요. [GitHub 문제 28](https://github.com/Azure/azure-functions-durable-js/issues/28)합니다.

.NET에서 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` 매개 변수는 Durable Functions 확장의 일부인 `orchestrationClient` 출력 바인딩의 값입니다. JavaScript에서 이 개체는 `df.getClient(context)`를 호출하여 반환됩니다. 이러한 개체에는 새로운 또는 기존 오 케 스트레이 터 함수 인스턴스에 대 한 시작, 이벤트 보내기, 종료 및 쿼리를 사용할 수 있습니다 하는 방법을 제공 합니다.

앞의 예제는 HTTP 트리거 함수는를 `functionName` 값은 들어오는 URL에서 값을 전달 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)합니다. 합니다 [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) 포함 하는 응답을 반환 API를 다음 바인딩를 `Location` 헤더 및 인스턴스에 대 한 추가 정보. 시작된 하는 인스턴스 상태를 조회 하거나 인스턴스를 종료 하는 정보를 나중에 사용할 수 있습니다.

### <a name="monitoring"></a>패턴 #4: 모니터

모니터링 패턴 워크플로에서 유연 하 고 되풀이 프로세스를 가리킵니다. 예로 특정 조건이 충족 될 때까지 폴링합니다. 일반을 사용할 수 있습니다 [타이머 트리거](../functions-bindings-timer.md) 기본 주소에 정기적인 정리 작업과 하지만 해당 간격 등의 시나리오에서는 정적 이며 인스턴스 수명을 관리 하는 것이 복잡 해질 합니다. 유연한 되풀이 간격을 만들기, 작업 수명 관리 및 단일 오케스트레이션에 서 여러 모니터링 프로세스 만들기를 지 속성 함수를 사용할 수 있습니다.

이전의 비동기 HTTP API 시나리오를 되돌리려면 모니터 패턴의 예가입니다. 장기 실행 작업을 모니터링 하는 외부 클라이언트에 대 한 끝점을 노출 하는 대신 장기 실행 모니터는 외부 끝점을 사용 하 고 상태가 변경 될 때까지 기다립니다.

![모니터링 패턴의 다이어그램](./media/durable-functions-concepts/monitor.png)

몇 줄의 코드로, 임의의 엔드포인트를 관찰 하는 여러 모니터를 만드는 지 속성 함수를 사용할 수 있습니다. 모니터 조건이 충족 되 면 실행을 종료할 수 있습니다 또는 [DurableOrchestrationClient](durable-functions-instance-management.md) 모니터를 종료할 수 있습니다. 모니터를 변경할 수 있습니다 `wait` 특정 조건 (예를 들어, 지 수 백오프.)를 기반으로 간격 

다음 코드는 기본 모니터를 구현합니다.

#### <a name="c-script"></a>C# 스크립트

```csharp
public static async Task Run(DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
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

요청을 받으면 해당 작업 ID에 대해 새 오케스트레이션 인스턴스가 만들어집니다. 조건이 충족되고 루프가 종료될 때까지 인스턴스는 상태를 폴링합니다. 지 속성 타이머는 폴링 간격을 제어합니다. 그런 다음, 더 많은 작업을 수행 하거나 오케스트레이션을 종료할 수 있습니다. 경우는 `context.CurrentUtcDateTime` (.NET) 또는 `context.df.currentUtcDateTime` (JavaScript)를 초과 합니다 `expiryTime` 값을 모니터가 종료 됩니다.

### <a name="human"></a>패턴 #5: 사용자 개입

많은 자동화 된 프로세스는 일종의 인간 상호 작용을 포함합니다. 자동화 된 프로세스에서 인간과 관련 된 사람으로 고가용성 및 cloud services로 응답 성능이 동일 하지 않아 어렵습니다. 자동화 된 프로세스는 시간 제한 및 보정 논리를 사용 하 여이 수 있습니다.

승인 프로세스는 인간 상호 작용을 포함 하는 비즈니스 프로세스의 예시입니다. 승인 관리자에 게 특정 금액을 초과 하는 경비 보고서에 대 한 필요할 수 있습니다. 관리자 (manager 아마도 휴가 발생) 72 시간 내 경비 보고서를 승인 하지 않는, 하는 경우를 에스컬레이션 프로세스가 시작 되어 다른 사람 (아마도 관리자의 관리자)에서 승인을 받아야 합니다.

![인간 상호 작용 패턴의 다이어그램](./media/durable-functions-concepts/approval.png)

이 예제는 오 케 스트레이 터 함수를 사용 하 여 패턴을 구현할 수 있습니다. 오 케 스트레이 터를 사용 하는 [지 속성 타이머](durable-functions-timers.md) 승인을 요청할 수 있습니다. Orchestrator는 시간 제한이 발생 하는 경우 에스컬레이션 합니다. Orchestrator에 대 한 대기를 [외부 이벤트](durable-functions-external-events.md), 인간 상호 작용 하 여 생성 되는 알림과 같이 합니다.

이러한 예제는 인간 상호 작용 패턴을 보여 주기 위해 승인 프로세스를 만듭니다.

#### <a name="c-script"></a>C# 스크립트

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

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

지 속성 타이머를 만들려면 호출 `context.CreateTimer` (.NET) 또는 `context.df.createTimer` (JavaScript). 알림은 `context.WaitForExternalEvent`(.NET) 또는 `context.df.waitForExternalEvent`(JavaScript)에서 수신됩니다. 차례로 `Task.WhenAny` (.NET) 또는 `context.df.Task.any` (JavaScript) 에스컬레이션 여부를 결정 하기 위해 호출 됩니다 (시간 제한이 먼저 발생) 또는 프로세스 (시간 제한 전에 승인 수신) 승인 합니다.

외부 클라이언트 중 하나를 사용 하 여 대기 중인 오 케 스트레이 터 함수는 이벤트 알림을 배달할 수는 [기본 제공 HTTP Api](durable-functions-http-api.md#raise-event) 또는 사용 하 여 합니다 [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) 에서 API 다른 함수:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="the-technology"></a>기술

내부적으로 지 속성 함수 확장의 맨 위에 빌드되는 [지 속성 작업 프레임 워크](https://github.com/Azure/durabletask), 지 속성 작업 오케스트레이션을 만드는 데 사용 되는 GitHub에서 오픈 소스 라이브러리를 합니다. Azure Functions를 Azure WebJobs의 서버 리스 발전와 같은 지 속성 함수는 지 속성 작업 프레임 워크의 서버 리스 진화 합니다. Microsoft 및 기타 조직 사용 하 여 지 속성 작업 프레임 워크 광범위 하 게 중요 프로세스를 자동화할 수 있습니다. 이는 서버를 사용하지 않는 Azure Functions 환경에 적합합니다.

### <a name="event-sourcing-checkpointing-and-replay"></a>이벤트 소싱, 검사점 설정 및 재생

오 케 스트레이 터 함수를 사용 하 여 해당 실행 상태를 안정적으로 유지 합니다 [이벤트 소싱](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) 디자인 패턴입니다. 오케스트레이션의 현재 상태에 직접 저장 하는 대신 지 속성 함수 확장 전체 일련의 함수 오케스트레이션에 서 사용 하는 작업을 기록 하는 추가 전용 저장소를 사용 합니다. 추가 전용 저장소를 "덤프" 전체 런타임 상태에 비해 많은 장점이 있습니다. 향상 된 성능, 확장성 및 응답성 혜택에 포함 됩니다. 또한 트랜잭션 데이터 및 전체 감사 내역 및 기록에 대 한 최종 일관성을 가져옵니다. 감사 내역 신뢰할 수 있는 보정 작업을 지원 합니다.

Durable Functions는 이벤트 소싱을 투명 하 게 사용 합니다. 내부적으로 `await` (C#) 또는 `yield` 오 케 스트레이 터 함수에 연산자 (JavaScript)를 지 속성 작업 프레임 워크 디스패처에 다시 오 케 스트레이 터 스레드의 제어를 생성 합니다. 그런 다음 디스패처는 오케스트레이터에서 예약한 새 작업(예: 하나 이상의 자식 함수 호출 또는 지속성 타이머 예약)을 저장소에 커밋합니다. 투명 한 커밋 작업은 오케스트레이션 인스턴스의 실행 기록에 추가합니다. 기록은 저장소 테이블에 저장됩니다. 그런 다음 커밋 작업은 실제 작업을 예약하는 큐에 메시지를 추가합니다. 이 시점에서 오케스트레이터 함수는 메모리에서 언로드할 수 있습니다. 

Azure Functions 소비 계획을 사용 하는 경우 오 케 스트레이 터 함수에 대 한 청구를 중지 합니다. 함수 다시 시작을 위해 많은 작업 및 해당 상태를 재구성 하는 경우.

오케스트레이션 함수 많은 작업을 수행를 지정 된 경우 (예를 들어, 응답 메시지 수신 또는 영구 타이머 만료)는 오 케 스트레이 터 절전 및 로컬 상태를 다시 시작에서 함수 전체를 다시 실행 합니다. 

코드에서 함수를 호출 하려고 하는 경우 재생 하는 동안 (또는 다른 비동기 작업), 지 속성 작업 프레임 워크는 현재 오케스트레이션의 실행 기록을 확인 합니다. 찾으면 합니다 [작업 함수](durable-functions-types-features-overview.md#activity-functions) 이미 실행 하 고 해당 함수의 결과 재생 및 오 케 스트레이 터 코드는 계속 실행 결과 생성 합니다. 재생 하거나 새 비동기 작업을 예약 된 함수 코드는 완료 될 때까지 계속 됩니다.

### <a name="orchestrator-code-constraints"></a>오케스트레이터 코드 제약 조건

재생 동작은 오 케 스트레이 터 코드는 오 케 스트레이 터 함수에서 작성할 수 있는 코드의 형식에 제약 조건을 만듭니다. 예를 들어, 여러 번 재생 됩니다 될 때마다 동일한 결과 생성 해야 하기 때문에 오 케 스트레이 터 코드는 결정적 이어야 합니다. 참조 제약 조건의 전체 목록은 [오 케 스트레이 터 코드 제약 조건](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints)합니다.

## <a name="monitoring-and-diagnostics"></a>모니터링 및 진단

지 속성 함수 확장에 구조적된 추적 데이터를 자동으로 내보냅니다 [Application Insights](../functions-monitoring.md) Azure Application Insights 계측 키를 사용 하 여 함수 앱을 설정 하는 경우. 작업 및 오케스트레이션에 서의 진행률을 모니터링 하 고 추적 데이터를 사용할 수 있습니다.

지 속성 함수 추적 이벤트 모양을 Application Insights 포털에서 사용 하는 경우의 예로 [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Application Insights 쿼리 결과](./media/durable-functions-concepts/app-insights-1.png)

유용한 구조적된 데이터를 찾을 수 있습니다는 `customDimensions` 각 로그 항목의 필드입니다. 완전히 확장 되는 항목의 예는 다음과 같습니다.

![Application Insights 쿼리의 customDimensions 필드](./media/durable-functions-concepts/app-insights-2.png)

지속성 작업 프레임워크 디스패처의 재생 동작으로 인해 재생된 작업에 대해 중복된 로그 항목이 표시될 수 있습니다. 중복 된 로그 항목이 핵심 엔진의 재생 동작을 이해 하면 도움이 됩니다. 합니다 [진단](durable-functions-diagnostics.md) 문서 "실시간" 로그만 볼 수 있도록 재생 로그를 필터링 하는 샘플 쿼리를 보여 줍니다.

## <a name="storage-and-scalability"></a>저장소 및 확장성

지 속성 함수 확장 실행 기록 상태 및 트리거 함수 실행을 유지 하기 위해 Azure Storage에 큐, 테이블 및 blob를 사용 합니다. 기본 저장소 계정을 사용 하 여 함수 앱에 대 한 또는 별도 저장소 계정을 구성할 수 있습니다. 저장소 처리량 제한에 따라 별도 계정이 필요할 수 있습니다. 작성 하는 오 케 스트레이 터 코드는 이러한 저장소 계정에 엔터티와 상호 작용할 하지 않습니다. 지 속성 작업 프레임 워크 구현 세부 정보로 직접 엔터티를 관리합니다.

오케스트레이터 함수는 작업 함수를 예약하고 내부 큐 메시지를 통해 이러한 함수의 응답을 받습니다. Azure Functions 소비 계획에서 함수 앱을 실행 하는 경우는 [Azure Functions 크기 조정 컨트롤러](../functions-scale.md#how-the-consumption-and-premium-plans-work) 이러한 큐를 모니터링 합니다. 필요에 따라 새 계산 인스턴스가 추가 됩니다. 여러 Vm을 확장 하는 경우 오 케 스트레이 터 함수는 오 케 스트레이 터 함수 호출 하 여 여러 다른 Vm에서 실행 될 수 있는 작업 함수 중 하나의 VM에서 실행할 수 있습니다. 지 속성 함수의 크기 조정 동작에 대 한 자세한 내용은 참조 [성능과 확장성](durable-functions-perf-and-scale.md)합니다.

테이블 저장소에 오 케 스트레이 터 계정에 대 한 실행 기록을 저장 됩니다. 인스턴스는 특정 VM에서 리하이드레이션 할 때마다는 오 케 스트레이 터 해당 로컬 상태를 다시 빌드할 수 있도록 table storage에서 실행 기록과 인출 합니다. 테이블 저장소에서 사용할 수 있는 기록의 측면을 편리 하 게 됩니다와 같은 도구를 사용할 수 있습니다 [Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md) 오케스트레이션의 기록을 볼 수 있습니다.

Storage blob 주로 여러 Vm에서 오케스트레이션 인스턴스 확장을 조정 하기 위해 임대 메커니즘으로 사용 됩니다. Storage blob는 테이블 또는 큐에 직접 저장할 수 없는 큰 메시지에 대 한 데이터를 저장 합니다.

![Azure Storage 탐색기의 스크린샷](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> 쉽고 편리 하 게 테이블 저장소의 실행 기록을 확인 하는 아니지만이 테이블에 종속성 확인 하지 마십시오. 테이블 지 속성 함수 확장이 진화 함에 따라 변경 될 수 있습니다.

## <a name="known-issues"></a>알려진 문제

알려진 모든 문제는 [GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues) 목록에서 추적해야 합니다. 문제에 직면 하 고 GitHub에서 문제를 찾을 수 없는 경우 새 문제를 엽니다. 문제에 대 한 자세한 설명을 포함 합니다.

## <a name="next-steps"></a>다음 단계

Durable Functions에 대 한 자세한 내용은 참조 하세요 [지 속성 함수의 함수 유형 및 기능](durable-functions-types-features-overview.md)합니다. 

시작하기:

> [!div class="nextstepaction"]
> [첫 번째 지속형 함수 만들기](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
