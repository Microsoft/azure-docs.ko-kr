---
title: 지속성 함수의 진단 - Azure
description: Azure Functions의 지속성 함수 확장을 사용하여 문제를 진단하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 167f697d4928d88114a30739a1d39a576c87ac84
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126666"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Azure의 지속성 함수에서 진단

[지속성 함수](durable-functions-overview.md)를 사용하여 문제를 진단하기 위한 몇 가지 옵션이 있습니다. 이러한 옵션 중 일부는 일반 함수와 동일하며 그 중 일부는 지속성 함수에만 고유하게 적용됩니다.

## <a name="application-insights"></a>Application Insights

Azure Functions에서 진단 및 모니터링을 수행하려면 [Application Insights](../../azure-monitor/app/app-insights-overview.md)를 사용하는 것이 좋습니다. 지속성 함수에도 동일하게 적용됩니다. 함수 앱에서 Application Insights를 활용하는 방법에 대한 개요는 [Azure Functions 모니터링](../functions-monitoring.md)을 참조하세요.

또한 Azure Functions 지속성 확장은 오케스트레이션의 종단 간 실행을 추적할 수 있는 *추적 이벤트*를 내보냅니다. 이러한 이벤트는 Azure Portal에서 [Application Insights 분석](../../azure-monitor/app/analytics.md) 도구를 사용하여 검색하고 쿼리할 수 있습니다.

### <a name="tracking-data"></a>추적 데이터

오케스트레이션 인스턴스의 각 수명 주기 이벤트는 추적 이벤트를 Application Insights의 **traces** 컬렉션에 기록합니다. 이 이벤트에는 여러 필드가 있는 **customDimensions** 페이로드가 포함됩니다.  모든 필드 이름 앞에 `prop__`가 추가됩니다.

* **hubName**: 오케스트레이션이 실행 중인 작업 허브의 이름입니다.
* **appName**: 함수 앱의 이름입니다. 여러 함수 앱에서 동일한 Application Insights 인스턴스를 공유하는 경우에 유용합니다.
* **slotName**: 현재 함수 앱이 실행 중인 [배포 슬롯](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/)입니다. 배포 슬롯을 활용하여 오케스트레이션의 버전을 관리하는 경우에 유용합니다.
* **functionName**: 오케스트레이터 또는 작업 함수의 이름입니다.
* **functionType**: **Orchestrator** 또는 **Activity**와 같은 함수 유형입니다.
* **instanceId**: 오케스트레이션 인스턴스의 고유 ID입니다.
* **state**: 인스턴스의 수명 주기 실행 상태입니다. 유효한 값은 다음과 같습니다.
  * **예약됨**: 함수 실행이 예약되었지만 아직 실행되지 않았습니다.
  * **시작됨**: 함수 실행이 시작되었지만 아직 대기하거나 완료되지 않았습니다.
  * **대기**: 오케스트레이터에서 작업을 예약했고 완료되기를 기다리고 있습니다.
  * **수신 대기 중**: 오케스트레이터에서 외부 이벤트 알림을 수신 대기 중입니다.
  * **완료됨**: 함수가 성공적으로 완료되었습니다.
  * **실패**: 함수가 오류로 인해 실패했습니다.
* **이유**: 추적 이벤트와 관련된 추가 데이터입니다. 예를 들어 인스턴스에서 외부 이벤트 알림을 기다리고 있는 경우 이 필드는 대기 중인 이벤트의 이름을 나타냅니다. 함수가 실패하면 이 필드에 오류 세부 정보가 포함됩니다.
* **isReplay**: 재생된 실행에 대한 추적 이벤트인지를 나타내는 부울 값입니다.
* **extensionVersion**: 지속성 작업 확장의 버전입니다. 이는 확장에서 가능한 버그를 보고할 때 특히 중요한 데이터입니다. 장기 실행 인스턴스는 실행되는 동안 업데이트가 발생하는 경우 여러 버전을 보고할 수 있습니다.
* **sequenceNumber**: 이벤트에 대한 실행 시퀀스 번호입니다. 타임스탬프와 결합하면 이벤트를 실행 시간 기준으로 정렬하는 데 도움이 됩니다. *인스턴스가 실행 중인 동안 호스트가 다시 시작되면 이 번호가 0으로 다시 설정되므로, 항상 가장 먼저 타임스탬프를 기준으로 정렬한 후 sequenceNumber를 기준으로 정렬해야 합니다.*

Application Insights로 내보낸 추적 데이터에 대한 자세한 정보는 `host.json` 파일의 `logger`(Functions 1.x) 또는 `logging`(Functions 2.x) 섹션에서 구성할 수 있습니다.

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

기본적으로 모든 재생되지 않는 추적 이벤트를 내보냅니다. 예외 상황에서만 추적 이벤트를 내보내는 경우 `Host.Triggers.DurableTask`를 `"Warning"` 또는 `"Error"`로 설정하여 데이터의 양을 줄일 수 있습니다.

자세한 오케스트레이션 재생 이벤트를 내보내도록 설정하려면 다음에 표시된 대로 `durableTask` 아래의 `host.json` 파일에서 `LogReplayEvents`를 `true`로 설정할 수 있습니다.

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> 기본적으로 데이터를 너무 자주 내보내지 않도록 방지하기 위해 Azure Functions 런타임에서 Application Insights 원격 분석을 샘플링합니다. 이로 인해 짧은 기간 동안 많은 수명 주기 이벤트가 발생하면 추적 정보가 손실될 수 있습니다. [Azure Functions 모니터링 문서](../functions-monitoring.md#configure-sampling)에서는 이 동작을 구성하는 방법에 대해 설명합니다.

### <a name="single-instance-query"></a>단일 인스턴스 쿼리

다음 쿼리에서는 [Hello 시퀀스](durable-functions-sequence.md) 함수 오케스트레이션의 단일 인스턴스에 대한 추적 기록 데이터를 보여 줍니다. [AIQL(Application Insights Query Language)](https://aka.ms/LogAnalyticsLanguageReference)을 사용하여 작성되었습니다. *논리* 실행 경로만 표시되도록 재생 실행을 필터링합니다. 아래 쿼리처럼 `timestamp` 및 `sequenceNumber`를 기준으로 이벤트를 정렬할 수 있습니다.

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

그 결과는 실행 시간 오름차순으로 정렬된 작업 함수를 포함하여 오케스트레이션의 실행 경로를 보여주는 추적 이벤트 목록입니다.

![Application Insights 쿼리](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>인스턴스 요약 쿼리

다음 쿼리에서는 지정된 시간 범위에서 실행된 모든 오케스트레이션 인스턴스의 상태를 표시합니다.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

결과는 인스턴스 ID 및 현재 런타임 상태의 목록입니다.

![Application Insights 쿼리](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>로깅

오케스트레이터 함수에서 로그를 직접 작성할 때 오케스트레이터 재생 동작을 고려하는 것이 중요합니다. 예를 들어 다음 오케스트레이터 함수를 살펴보세요.

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

결과 로그 데이터는 다음과 같이 표시됩니다.

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> 로그는 F1, F2 및 F3을 호출한다고 주장하지만 이러한 함수는 처음 발생할 때만 *실제로* 호출됩니다. 재생 중에 발생하는 후속 호출은 건너뛰고, 출력은 오케스트레이터 논리로 재생됩니다.

재생되지 않는 실행에만 로그온하려면 `IsReplaying`이 `false`인 경우에만 기록할 조건식을 작성할 수 있습니다. 위의 예제를 살펴보았지만 이번에는 재생 확인을 사용합니다.

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

이 변경으로 인한 로그 출력은 다음과 같습니다.

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>사용자 지정 상태

사용자 지정 오케스트레이션 상태를 사용하면 오케스트레이터 함수의 사용자 지정 상태 값을 설정할 수 있습니다. 이 상태는 HTTP 상태 쿼리 API 또는 `DurableOrchestrationClient.GetStatusAsync` API를 통해 제공됩니다. 사용자 지정 오케스트레이션 상태를 통해 오케스트레이터 함수를 보다 자세히 모니터링할 수 있습니다. 예를 들어, 오케스트레이터 함수 코드는 장기 실행 작업에 대한 진행 상태를 업데이트하기 위한 `DurableOrchestrationContext.SetCustomStatus` 호출을 포함할 수 있습니다. 따라서 웹 페이지 또는 다른 외부 시스템과 같은 클라이언트가 HTTP 상태 쿼리 API를 주기적으로 쿼리하여 보다 다양한 진행률 정보를 얻을 수 있습니다. `DurableOrchestrationContext.SetCustomStatus` 사용 샘플은 아래에 제공됩니다.

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

오케스트레이션이 실행되는 동안 외부 클라이언트가 이 사용자 지정 상태를 가져올 수 있습니다.

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

클라이언트는 다음과 같은 응답을 받습니다.

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> 사용자 지정 상태 페이로드는 Azure Table Storage 열에 맞아야 하므로 16KB의 UTF-16 JSON 텍스트로 제한됩니다. 더 큰 페이로드가 필요한 경우 외부 저장소를 사용하면 됩니다.

## <a name="debugging"></a>디버그

Azure Functions는 디버깅 함수 코드를 직접 지원하며, Azure 또는 로컬에서 실행하는지 여부와 관계 없이 동일한 지원이 지속성 함수에 전달됩니다. 그러나 디버그할 때 고려해야 할 몇 가지 동작이 있습니다.

* **재생**: 오케스트레이터 함수는 새 입력을 받으면 정기적으로 재생됩니다. 즉 오케스트레이터 함수의 단일 *논리* 실행은 특히 함수 코드의 초기에 설정된 경우 동일한 중단점에 여러 번 도달할 수 있습니다.
* **대기**: `await`가 발생할 때마다 지속성 작업 프레임워크 디스패처로 제어가 반환됩니다. 특정 `await`가 처음 발생한 경우 관련 작업이 *절대로* 다시 시작되지 않습니다 . 작업이 다시 시작되지 않으므로 실제로 대기(Visual Studio에서 F10)를 *단계별로 실행*할 수 없습니다. 작업이 재생되는 경우에만 단계별 실행이 작동합니다.
* **메시지 시간 제한**: 지속성 함수는 내부적으로 큐 메시지를 사용하여 오케스트레이터 함수와 작업 함수를 모두 실행합니다. 다중 VM 환경에서 오랜 시간 동안 디버그하는 경우 다른 VM에서 메시지를 선택하여 중복 실행이 발생할 수 있습니다. 이 동작은 일반 큐 트리거 함수에도 적용되지만, 큐가 구현 세부 정보이므로 이 컨텍스트에서 지시하는 것이 중요합니다.

> [!TIP]
> 중단점을 설정할 때 재생되지 않는 실행에서만 중단하려면 `IsReplaying`이 `false`인 경우에만 중단되는 조건부 중단점을 설정할 수 있습니다.

## <a name="storage"></a>Storage

기본적으로 지속성 함수는 Azure Storage에 상태를 저장합니다. 즉 [Microsoft Azure Storage 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)와 같은 도구를 사용하여 오케스트레이션 상태를 검사할 수 있습니다.

![Azure Storage 탐색기 스크린샷](./media/durable-functions-diagnostics/storage-explorer.png)

이 도구는 오케스트레이션의 상태를 정확히 표시하기 때문에 디버깅에 유용합니다. 또한 큐의 메시지를 검사하여 보류 중인 작업(경우에 따라 중지)을 파악할 수도 있습니다.

> [!WARNING]
> Table Storage의 실행 기록을 확인하는 것이 편리하지만 이 테이블에 대한 종속성은 사용하지 마세요. 지속성 함수 확장이 진화함에 따라 변경될 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지속성 타이머 사용](durable-functions-timers.md)
