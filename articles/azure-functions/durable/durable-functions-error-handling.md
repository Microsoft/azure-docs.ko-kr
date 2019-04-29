---
title: 지속성 함수의 오류 처리 - Azure
description: Azure Functions의 지속성 함수 확장에서 오류를 처리하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: f3d7f916d31a03d7b868749026f541dd646459f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732521"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>지속성 함수의 오류 처리(Azure Functions)

지속성 함수 오케스트레이션은 코드로 구현되며 프로그래밍 언어의 오류 처리 기능을 사용할 수 있습니다. 이를 고려하여 오케스트레이션에 오류 처리 및 보정을 통합할 때 알아야 할 새로운 개념은 없습니다. 그러나 알고 있어야 하는 몇 가지 동작이 있습니다.

## <a name="errors-in-activity-functions"></a>작업 함수의 오류

작업 함수에서 throw되는 모든 예외는 오케스트레이터 함수에 다시 마샬링되고 `FunctionFailedException`으로 throw됩니다. 오케스트레이터 함수에서 요구 사항에 적합한 오류 처리 및 보정 코드를 작성할 수 있습니다.

예를 들어 한 계정에서 다른 계정으로 자금을 이체하는 다음 오케스트레이터 함수를 살펴보세요.

### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

대상 계정에 대해 **CreditAccount** 함수를 호출하지 못하면 오케스트레이터 함수는 자금을 원본 계정에 다시 입금하여 보정합니다.

## <a name="automatic-retry-on-failure"></a>실패 시 자동 다시 시도

작업 함수 또는 하위 오케스트레이션 함수를 호출할 때 자동으로 다시 시도하는 정책을 지정할 수 있습니다. 다음 예제에서는 함수를 최대 3회 호출하려고 하고 각각의 다시 시도 간에 5초 동안 기다립니다.

### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

`CallActivityWithRetryAsync`(.NET) 또는 `callActivityWithRetry`(JavaScript) API는 `RetryOptions` 매개 변수를 사용합니다. `CallSubOrchestratorWithRetryAsync`(.NET) 또는 `callSubOrchestratorWithRetry`(JavaScript) API를 사용하는 하위 오케스트레이션 호출은 이처럼 동일한 다시 시도 정책을 사용할 수 있습니다.

자동 다시 시도 정책을 사용자 지정하기 위한 몇 가지 옵션이 있습니다. 이러한 옵션은 다음과 같습니다.

* **최대 시도 횟수**: 최대 재시도 횟수입니다.
* **첫 번째 다시 시도 간격**: 첫 번째 다시 시도를 수행할 때까지 기다리는 시간입니다.
* **백오프 계수**: 백오프의 증가율을 결정하는 데 사용되는 계수입니다. 기본값은 1입니다.
* **최대 다시 시도 간격**: 다시 시도 간에 기다리는 최대 시간입니다.
* **다시 시도 시간 제한**: 다시 시도하는 데 소요되는 최대 시간입니다. 기본 동작은 무기한으로 다시 시도하는 것입니다.
* **처리**: 함수 호출을 다시 시도해야 하는지 여부를 결정하는 사용자 정의 콜백을 지정할 수 있습니다.

## <a name="function-timeouts"></a>함수 시간 제한

오케스트레이터 함수가 완료되는 데 시간이 너무 오래 걸리는 경우 함수 호출을 중단할 수도 있습니다. 현재 이 작업을 제대로 수행하는 방법은 다음 예제와 같이 `Task.WhenAny`(.NET) 또는 `context.df.Task.any`(JavaScript)와 함께 `context.CreateTimer`(.NET) 또는 `context.df.createTimer`(JavaScript)를 사용하여 [지속성 타이머](durable-functions-timers.md)를 만드는 것입니다.

### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> 이 메커니즘은 실제로 진행 중인 작업 함수 실행을 종료하지 않습니다. 대신 단순히 오케스트레이터 함수를 사용하여 결과를 무시하고 계속 진행할 수 있습니다. 자세한 내용은 [타이머](durable-functions-timers.md#usage-for-timeout) 설명서를 참조하세요.

## <a name="unhandled-exceptions"></a>처리되지 않은 예외

처리되지 않은 예외로 인해 오케스트레이터 함수가 실패하면 예외 정보가 자세히 기록되고 인스턴스가 `Failed` 상태로 완료됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [문제 진단](durable-functions-diagnostics.md)
