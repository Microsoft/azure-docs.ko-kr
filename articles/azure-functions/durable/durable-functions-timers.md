---
title: 지속성 함수의 타이머 - Azure
description: Azure Functions의 지속성 함수 확장에서 지속성 타이머를 구현하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: a05f75a7e38ee7cd4dc056629d9acaacad875e08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730228"
---
# <a name="timers-in-durable-functions-azure-functions"></a>지속성 함수의 타이머(Azure Functions)

[지속성 함수](durable-functions-overview.md)는 지연을 구현하거나 비동기 작업에 대한 시간 제한을 설정하기 위해 오케스트레이터 함수에 사용할 *지속성 타이머*를 제공합니다. 지속성 타이머는 `Thread.Sleep` 및 `Task.Delay`(C#) 또는 `setTimeout()` 및 `setInterval()`(JavaScript) 대신, 오케스트레이터 함수에 사용해야 합니다.

.NET에서 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)의 [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) 메서드를 호출하거나 JavaScript에서 `DurableOrchestrationContext`의 `createTimer`메서드를 호출하여 지속형 타이머를 만듭니다. 이 메서드는 지정된 날짜와 시간에 다시 시작하는 작업을 반환합니다.

## <a name="timer-limitations"></a>타이머 제한 사항

오후 4시 30분에 만료되는 타이머를 만들면 기본 지속성 작업 프레임워크에서 오후 4시 30분에만 표시되는 메시지를 큐에 넣습니다. Azure Functions 소비 계획에서 실행될 때 새로 표시되는 타이머 메시지는 함수 앱이 적절한 VM에서 활성화되도록 합니다.

> [!NOTE]
> * 지속성 타이머는 Azure Storage의 제한으로 인해 7일을 초과하여 지속될 수 없습니다. [7일을 초과할 수 있도록 타이머를 연장하기 위한 기능](https://github.com/Azure/azure-functions-durable-extension/issues/14)을 요청하고 있습니다.
> * 지속형 타이머의 상대적인 최종 기한을 계산할 때는 항상 아래 예제와 같이 .NET의 `DateTime.UtcNow` 대신 [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime)을 사용하고 JavaScript의 `Date.now` 또는 `Date.UTC` 대신 `currentUtcDateTime`을 사용합니다.

## <a name="usage-for-delay"></a>지연 사용

다음 예제에서는 실행 지연에 지속성 타이머를 사용하는 방법을 보여 줍니다. 여기서는 청구 알림을 10일 동안 매일 발급하고 있습니다.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> 오케스트레이터 함수에는 무한 루프를 사용하지 않도록 방지하세요. 무한 루프 시나리오를 안전하고 효율적으로 구현하는 방법에 대한 자세한 내용은 [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)을 참조하세요.

## <a name="usage-for-timeout"></a>시간 제한 사용

이 예제에서는 지속성 타이머를 사용하여 시간 제한을 구현하는 방법을 보여 줍니다.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> 코드가 완료될 때까지 기다리지 않는 경우 `CancellationTokenSource`를 사용하여 지속성 타이머를 취소하거나(C#) 반환된 `TimerTask`에 대해 `cancel()`를 호출합니다(JavaScript). 지 속성 작업 프레임 워크에는 오케스트레이션의 상태를 "완료" 처리 중인 모든 작업이 완료 되거나 취소 될 때까지 변경 되지 않습니다.

이 메커니즘은 실제로 진행 중인 작업 함수 실행을 종료하지 않습니다. 대신 단순히 오케스트레이터 함수를 사용하여 결과를 무시하고 계속 진행할 수 있습니다. 함수 앱에서 소비 계획을 사용하는 경우 중단된 작업 함수에서 사용되는 시간과 메모리에 대해 요금이 계속 청구됩니다. 기본적으로 소비 계획에서 실행되는 함수의 시간 제한은 5분입니다. 이 제한을 초과하면 모든 실행을 중지하고 런어웨이 청구 상황을 방지하기 위해 Azure Functions 호스트가 재활용됩니다. [함수 시간 제한은 구성 가능](../functions-host-json.md#functiontimeout)합니다.

오케스트레이터 함수에서 시간 제한을 구현하는 방법에 대한 자세한 예제는 [인간 상호 작용 및 시간 제한 - 전화 확인](durable-functions-phone-verification.md) 연습을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [외부 이벤트 발생 및 처리](durable-functions-external-events.md)
