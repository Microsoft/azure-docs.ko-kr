---
title: Azure Functions의 타이머 트리거
description: Azure Functions에서 타이머 트리거를 사용하는 방법을 파악합니다.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 45f704afce28967237b2905ef068678ba05ae085
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206648"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions의 타이머 트리거 

이 문서에서는 Azure Functions에서 타이머 트리거를 사용하는 방법을 설명합니다. 타이머 트리거를 사용하면 일정에 따라 함수를 실행할 수 있습니다. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

타이머 트리거 함수를 수동으로 실행 하는 방법에 대 한 자세한 내용은 [비 HTTP 트리거 함수 수동 실행](./functions-manually-run-non-http.md)을 참조 하세요.

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

타이머 트리거는 [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 패키지 버전 2.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

타이머 트리거는 [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 분이 5로 나눌 때마다 실행 되는 [c # 함수](functions-dotnet-class-library.md) 를 보여 줍니다 (예: 함수가 18:57:00에서 시작 하는 경우 다음 성능은 19:00:00에 있습니다). [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)개체는 함수에 전달 됩니다.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 타이머 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 누락된 일정으로 인해 이 함수 호출이 발생했는지를 나타내는 로그를 씁니다. [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)개체는 함수에 전달 됩니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# 스크립트 코드는 다음과 같습니다.

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 타이머 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 누락된 일정으로 인해 이 함수 호출이 발생했는지를 나타내는 로그를 씁니다. [타이머 개체가](#usage) 함수에 전달 됩니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript 코드는 다음과 같습니다.

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 파일 * 의function.js* 에 설명 된 구성의 타이머 트리거 바인딩을 사용 합니다. 바인딩을 사용 하는 실제 [Python 함수](functions-reference-python.md) 는 * __init__py* 파일에 설명 되어 있습니다. 함수에 전달 되는 개체는 [azure. 함수인 request 개체](/python/api/azure-functions/azure.functions.timerrequest)유형입니다. 함수 논리는 현재 호출이 누락 된 일정 발생으로 인 한 것인지 여부를 나타내는 로그에 기록 합니다. 

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

다음은 Python 코드입니다.

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

# <a name="java"></a>[Java](#tab/java)

다음 예제 함수는 5분 간격으로 트리거되고 실행됩니다. 함수의 `@TimerTrigger` 주석은 [CRON 식](https://en.wikipedia.org/wiki/Cron#CRON_expression)과 같은 문자열 형식을 사용하여 일정을 정의합니다.

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs) 특성을 사용합니다.

특성의 생성자는 CRON 식 또는 `TimeSpan`을 사용합니다. `TimeSpan`함수 앱이 App Service 계획에서 실행 되는 경우에만를 사용할 수 있습니다. `TimeSpan` 는 소비 또는 탄력적 프리미엄 함수에서 지원 되지 않습니다.

다음 예제는 CRON 식을 보여줍니다.

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

함수의 `@TimerTrigger` 주석은 [CRON 식](https://en.wikipedia.org/wiki/Cron#CRON_expression)과 같은 문자열 형식을 사용하여 일정을 정의합니다.

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `TimerTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "timerTrigger"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "in"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 타이머 개체를 나타내는 변수의 이름입니다. | 
|**일정과**|**ScheduleExpression**|[CRON 식](#ncrontab-expressions) 또는 [TimeSpan](#timespan) 값입니다. App Service 계획에서 함수 앱을 실행 중인 경우에만 `TimeSpan`을 사용할 수 있습니다. 앱 설정에 일정 식을 배치 하 고이 속성을 **%** "% ScheduleAppSetting%" 예제와 같이 기호에서 래핑된 앱 설정 이름으로 설정할 수 있습니다. |
|**runOnStartup**|**RunOnStartup**|`true`인 경우 함수는 런타임이 시작될 때 호출됩니다. 예를 들어 비활성으로 인해 유휴 상태로 전환된 후에 함수 앱이 작동될 때 런타임이 시작됩니다. 함수 앱이 함수 변경으로 인해 다시 시작 되 고 함수 앱이 확장 될 때 따라서 **Runonstartup** 은 특히 프로덕션에서로 설정 하는 경우 거의 없습니다 `true` . |
|**useMonitor**|**UseMonitor**|`true` 또는 `false`로 설정하여 일정을 모니터링해야 하는지를 나타냅니다. 일정 모니터링은 일정 발생을 유지하여 함수 앱 인스턴스가 다시 시작하는 경우에도 일정을 올바르게 유지하도록 지원합니다. 명시적으로 설정 하지 않은 경우 `true` 되풀이 간격이 1 분 보다 크거나 같은 일정의 경우 기본값은입니다. 분당 한 번 넘게 트리거되는 일정에서 기본값은 `false`입니다.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> 프로덕션 환경에서는 **runOnStartup**을 `true`로 설정하지 않는 것이 좋습니다. 이 설정을 사용하면 매우 예측할 수 없는 시간에 코드가 실행됩니다. 특정 프로덕션 환경에서 이러한 추가 실행으로 인해 소비 계획에서 호스팅되는 앱의 비용이 상당히 높아질 수 있습니다. 예를 들어 **Runonstartup** 을 사용 하도록 설정 하면 함수 앱이 확장 될 때마다 트리거가 호출 됩니다. 프로덕션 환경에서 **runOnStartup**을 사용하도록 설정하기 전에 함수의 프로덕션 동작을 완전히 이해했는지 확인하세요.   

## <a name="usage"></a>사용량

타이머 트리거 함수를 호출 하면 timer 개체가 함수에 전달 됩니다. 다음 JSON은 타이머 개체의 예제 표현입니다.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

현재 함수 호출이 일정보다 늦은 경우 `IsPastDue` 속성은 `true`입니다. 예를 들어 함수 앱을 다시 시작하면 호출이 누락될 수 있습니다.

## <a name="ncrontab-expressions"></a>NCRONTAB 식 

Azure Functions [NCronTab](https://github.com/atifaziz/NCrontab) 라이브러리를 사용 하 여 NCronTab 식을 해석 합니다. NCRONTAB 식은 시간 전체 자릿수 (초)에 사용할 시작 부분에 추가 여섯 번째 필드를 포함 한다는 점을 제외 하 고는 CRON 식과 비슷합니다.

`{second} {minute} {hour} {day} {month} {day-of-week}`

각 필드에는 다음과 같은 형식의 값 중 하나가 포함될 수 있습니다.

|Type  |예제  |트리거될 때  |
|---------|---------|---------|
|특정 값 |<nobr>"0 5 * * * *"</nobr>|hh:05:00에서 hh는 매시간임(시간당 한 번)|
|모든 값(`*`)|<nobr>"0 * 5 * * *"</nobr>|5:mm:00에서 mm은 해당 시간의 매분임(하루 60번)|
|범위(`-` 연산자)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05,hh:mm:06 및 hh:mm:07에서 hh:mm은 매시간의 매분임(분당 3번)|
|값 집합(`,` 연산자)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05,hh:mm:08 및 hh:mm:10에서 hh:mm은 매시간의 매분임(분당 3번)|
|간격 값(`/` 연산자)|<nobr>"0 */5 * * * *"</nobr>|hh: 00:00, hh: 05:00, hh: 10:00, hh: 55:00, hh는 매시간 (12 번 a 시간)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB 예제

Azure Functions에서 타이머 트리거에 사용할 수 있는 NCRONTAB 식의 몇 가지 예는 다음과 같습니다.

|예제|트리거될 때  |
|---------|---------|
|`"0 */5 * * * *"`|5분마다 한 번|
|`"0 0 * * * *"`|1시간이 시작할 때마다 한 번|
|`"0 0 */2 * * *"`|2시간마다 한 번|
|`"0 0 9-17 * * *"`|오전 9시에서 오후 5시까지 1시간마다 한 번|
|`"0 30 9 * * *"`|매일 오전 9시 30분|
|`"0 30 9 * * 1-5"`|평일 오전 9:30|
|`"0 30 9 * Jan Mon"`|1월 매주 월요일 오전 9:30|


### <a name="ncrontab-time-zones"></a>NCRONTAB 표준 시간대

CRON 식에 있는 숫자는 시간 범위가 아닌 시간 및 날짜를 가리킵니다. 예를 들어 `hour` 필드에 있는 5는 5시간마다 한 번이 아닌 오전 5시를 가리킵니다.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 App Service 계획에서 함수 앱을 실행 중인 경우에만 `TimeSpan`을 사용할 수 있습니다.

CRON 식과 다르게 `TimeSpan` 값은 각 함수 호출 간의 시간 간격을 지정합니다. 함수가 지정된 간격보다 오랫동안 실행한 후에 완료되면 타이머는 즉시 함수를 다시 호출합니다.

`hh`이 24 미만인 경우 문자열로 표현되는 `TimeSpan` 형식은 `hh:mm:ss`입니다. 처음 두 자리가 24 이상인 경우 형식은 `dd:hh:mm`입니다. 다음은 몇 가지 예입니다.

|예제 |트리거될 때  |
|---------|---------|
|"01:00:00" | 매시간        |
|"00:01:00"|매분         |
|"24:00:00" | 매일        |
|"1.00:00:00" | 매일        |

## <a name="scale-out"></a>확장

함수 앱이 여러 인스턴스로 확장하는 경우 모든 인스턴스에서 타이머 트리거 함수의 단일 인스턴스만을 실행합니다.

## <a name="function-apps-sharing-storage"></a>스토리지를 공유하는 함수 앱

App service에 배포 되지 않은 함수 앱에서 저장소 계정을 공유 하는 경우 각 앱에 호스트 ID를 명시적으로 할당 해야 할 수 있습니다.

| Functions 버전 | 설정                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x 이상  | `AzureFunctionsWebHost__hostid`환경 변수 |
| 1.x               | `id` 의 *host.js*                                  |

식별 값을 생략 하거나 각 함수 앱의 식별 구성을 다른 값으로 수동으로 설정할 수 있습니다.

타이머 트리거는 저장소 잠금을 사용 하 여 함수 앱이 여러 인스턴스로 확장 될 때 하나의 타이머 인스턴스만 있는지 확인 합니다. 두 함수 앱이 동일한 식별 구성을 공유 하 고 각각 타이머 트리거를 사용 하는 경우 하나의 타이머만 실행 됩니다.

## <a name="retry-behavior"></a>다시 시도 동작

큐 트리거와 다르게 타이머 트리거는 함수가 실패한 후에 다시 시도하지 않습니다. 함수가 실패한 경우 일정에 따라 다음 시도까지 다시 호출되지 않습니다.

## <a name="troubleshooting"></a>문제 해결

타이머 트리거가 예상대로 작동하지 않는 경우 수행할 작업에 대한 정보는 [타이머 트리거 함수가 작동하지 않는 문제 조사 및 보고](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [타이머 트리거를 사용하는 빠른 시작으로 이동](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
