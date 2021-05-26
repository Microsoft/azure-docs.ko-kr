---
title: 지속성 함수의 사용자 지정 오케스트레이션 상태 - Azure
description: 지속성 함수의 사용자 지정 오케스트레이션 상태를 구성하고 사용하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: azfuncdf
ms.openlocfilehash: de74c2d8c4e7abf5735dad0b1c04f2cce88aa2c1
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370929"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>지속성 함수의 사용자 지정 오케스트레이션 상태(Azure Functions)

사용자 지정 오케스트레이션 상태를 사용하면 오케스트레이터 함수의 사용자 지정 상태 값을 설정할 수 있습니다. 이 상태는 오케스트레이션 클라이언트 개체의 [HTTP GetStatus API](durable-functions-http-api.md#get-instance-status) 또는 동등한 [SDK API](durable-functions-instance-management.md#query-instances)를 통해 제공됩니다.

## <a name="sample-use-cases"></a>샘플 사용 사례

### <a name="visualize-progress"></a>진행률 시각화

클라이언트는 상태 엔드포인트를 폴링하고 현재 실행 단계를 시각화하는 진행률 UI를 표시할 수 있습니다. 다음 샘플은 진행률 공유를 보여줍니다.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> 이러한 C# 예제는 Durable Functions 2.x용으로 작성되었으며 Durable Functions 1.x와 호환되지 않습니다. 버전 간의 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    context.SetCustomStatus("Tokyo");
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    context.SetCustomStatus("Seattle");
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
    context.SetCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E1_HelloSequence` 오케스트레이터 함수:

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const outputs = [];

    outputs.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    context.df.setCustomStatus("Tokyo");
    outputs.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    context.df.setCustomStatus("Seattle");
    outputs.push(yield context.df.callActivity("E1_SayHello", "London"));
    context.df.setCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
});
```

`E1_SayHello` 작업 함수:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```
# <a name="python"></a>[Python](#tab/python)

### <a name="e1_hellosequence-orchestrator-function"></a>`E1_HelloSequence` 오케스트레이터 함수
```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    
    output1 = yield context.call_activity('E1_SayHello', 'Tokyo')
    context.set_custom_status('Tokyo')
    output2 = yield context.call_activity('E1_SayHello', 'Seattle')
    context.set_custom_status('Seattle')
    output3 = yield context.call_activity('E1_SayHello', 'London')
    context.set_custom_status('London')
    
    return [output1, output2, output3]

main = df.Orchestrator.create(orchestrator_function)
```

### <a name="e1_sayhello-activity-function"></a>`E1_SayHello` 작업 함수
```python
def main(name: str) -> str:
    return f"Hello {name}!"

```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

### <a name="e1_hellosequence-orchestrator-function"></a>`E1_HelloSequence` 오케스트레이터 함수
```powershell
param($Context)

$output = @()

$output += Invoke-DurableActivity -FunctionName 'E1_SayHello' -Input 'Tokyo'
Set-DurableCustomStatus -CustomStatus 'Tokyo'

$output += Invoke-DurableActivity -FunctionName 'E1_SayHello' -Input 'Seattle'
Set-DurableCustomStatus -CustomStatus 'Seattle'

$output += Invoke-DurableActivity -FunctionName 'E1_SayHello' -Input 'London'
Set-DurableCustomStatus -CustomStatus 'London'


return $output
```

### <a name="e1_sayhello-activity-function"></a>`E1_SayHello` 작업 함수
```powershell
param($name)

"Hello $name"
```
---

그리고 이 클라이언트는 `CustomStatus` 필드가 "런던"으로 설정된 경우에만 오케스트레이션 출력을 수신합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, (string)eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
        await Task.Delay(200);
        durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
        Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    let durableOrchestrationStatus = await client.getStatus(instanceId);
    while (durableOrchestrationStatus.customStatus.toString() !== "London") {
        await new Promise((resolve) => setTimeout(resolve, 200));
        durableOrchestrationStatus = await client.getStatus(instanceId);
    }

    const httpResponseMessage = {
        status: 200,
        body: JSON.stringify(durableOrchestrationStatus),
    };

    return httpResponseMessage;
};
```

> [!NOTE]
> JavaScript에서 `customStatus` 필드는 다음 `yield` 또는 `return` 작업이 예약될 때 설정됩니다.

# <a name="python"></a>[Python](#tab/python)
```python
import json
import logging
import azure.functions as func
import azure.durable_functions as df
from time import sleep

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)    
    instance_id = await client.start_new(req.params.functionName, None, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")

    durable_orchestration_status = await client.get_status(instance_id)
    while durable_orchestration_status.custom_status != 'London':
        sleep(0.2)
        durable_orchestration_status = await client.get_status(instance_id)

    return func.HttpResponse(body='Success', status_code=200, mimetype='application/json')
```

> [!NOTE]
> Python에서 `custom_status` 필드는 다음 `yield` 또는 `return` 작업이 예약될 때 설정됩니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

이 기능은 현재 PowerShell에서 구현되지 않습니다.

---

### <a name="output-customization"></a>출력 사용자 지정

또 다른 흥미로운 시나리오는 고유한 특성 또는 상호 작용에 따라 사용자 지정된 출력을 반환하여 사용자를 분할하는 것입니다. 사용자 지정 오케스트레이션 상태 덕분에 클라이언트 측 코드는 일반 상태를 유지합니다. 다음 샘플과 같이 모든 주요 수정 작업은 서버 쪽에서 발생합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] IDurableOrchestrationContext context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="cityrecommender-orchestrator"></a>`CityRecommender` 오케스트레이터

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userChoice = context.df.getInput();

    switch (userChoice) {
        case 1:
            context.df.setCustomStatus({
                recommendedCities: [ "Tokyo", "Seattle" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
        case 2:
            context.df.setCustomStatus({
                recommendedCities: [ "Seattle", "London" ],
                recommendedSeasons: [ "Summer" ],
            });
            break;
        case 3:
            context.df.setCustomStatus({
                recommendedCity: [ "Tokyo", "London" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
    }

    // Wait for user selection and refine the recommendation
});
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="cityrecommender-orchestrator"></a>`CityRecommender` 오케스트레이터

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    userChoice = int(context.get_input())

    if userChoice == 1:
        context.set_custom_status({
            'recommendedCities' : ['Tokyo', 'Seattle'], 
            'recommendedSeasons' : ['Spring', 'Summer']
        }))
    if userChoice == 2:
        context.set_custom_status({
            'recommendedCities' : ['Seattle', 'London']
            'recommendedSeasons' : ['Summer']
        }))
    if userChoice == 3:
        context.set_custom_status({
            'recommendedCities' : ['Tokyo', 'London'], 
            'recommendedSeasons' : ['Spring', 'Summer']
        }))



    # Wait for user selection and refine the recommendation

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

#### <a name="cityrecommender-orchestrator"></a>`CityRecommender` 오케스트레이터

```powershell
param($Context)

$userChoice = $Context.Input -as [int]

if ($userChoice -eq 1) {
    Set-DurableCustomStatus -CustomStatus @{ recommendedCities = @('Tokyo', 'Seattle'); 
                                             recommendedSeasons = @('Spring', 'Summer') 
                                            }  
}

if ($userChoice -eq 2) {
    Set-DurableCustomStatus -CustomStatus @{ recommendedCities = @('Seattle', 'London'); 
                                             recommendedSeasons = @('Summer') 
                                            }  
}

if ($userChoice -eq 3) {
    Set-DurableCustomStatus -CustomStatus @{ recommendedCities = @('Tokyo', 'London'); 
                                             recommendedSeasons = @('Spring', 'Summer') 
                                            }  
}

# Wait for user selection and refine the recommendation
```
---

### <a name="instruction-specification"></a>지침 사양

오케스트레이터는 사용자 지정 상태를 통해 클라이언트에 고유한 지침을 제공할 수 있습니다. 사용자 지정 상태 지침은 오케스트레이션 코드의 단계에 매핑됩니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] IDurableOrchestrationContext context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userId = context.df.getInput();

    const discount = yield context.df.callActivity("CalculateDiscount", userId);

    context.df.setCustomStatus({
        discount,
        discountTimeout = 60,
        bookingUrl = "https://www.myawesomebookingweb.com",
    });

    const isBookingConfirmed = yield context.df.waitForExternalEvent("bookingConfirmed");

    context.df.setCustomStatus(isBookingConfirmed
        ? { message: "Thank you for confirming your booking." }
        : { message: "The booking was not confirmed on time. Please try again." }
    );

    return isBookingConfirmed;
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    userId = int(context.get_input())

    discount = yield context.call_activity('CalculateDiscount', userId)

    status = { 'discount' : discount,
        'discountTimeout' : 60,
        'bookingUrl' : "https://www.myawesomebookingweb.com",
    }
    context.set_custom_status(status)

    is_booking_confirmed = yield context.wait_for_external_event('BookingConfirmed')
    context.set_custom_status({'message': 'Thank you for confirming your booking.'} if is_booking_confirmed 
        else {'message': 'The booking was not confirmed on time. Please try again.'})
    return is_booking_confirmed

main = df.Orchestrator.create(orchestrator_function)
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$userId = $Context.Input -as [int]

$discount = Invoke-DurableActivity -FunctionName 'CalculateDiscount' -Input $userId

$status = @{
            discount = $discount;
            discountTimeout = 60;
            bookingUrl = "https://www.myawesomebookingweb.com"
            }

Set-DurableCustomStatus -CustomStatus $status

$isBookingConfirmed = Invoke-DurableActivity -FunctionName 'BookingConfirmed'

if ($isBookingConfirmed) {
    Set-DurableCustomStatus -CustomStatus @{message = 'Thank you for confirming your booking.'}
} else {
    Set-DurableCustomStatus -CustomStatus @{message = 'The booking was not confirmed on time. Please try again.'}
}

return $isBookingConfirmed
```
---

## <a name="sample"></a>샘플

다음 샘플에서는 사용자 지정 상태가 가장 먼저 설정됩니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { nextActions: [ "A", "B", "C" ], foo: 2, };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    custom_status = {'nextActions': ['A','B','C'], 'foo':2}
    context.set_custom_status(custom_status)

    # ...do more work...

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

# ...do work...

Set-DurableCustomStatus -CustomStatus @{ nextActions = @('A', 'B', 'C'); 
                                         foo = 2 
                                        }  

# ...do more work...
```
---

오케스트레이션이 실행되는 동안 외부 클라이언트가 이 사용자 지정 상태를 가져올 수 있습니다.

```http
GET /runtime/webhooks/durabletask/instances/instance123
```

클라이언트는 다음과 같은 응답을 받습니다.

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2019-10-06T18:30:24Z",
  "lastUpdatedTime": "2019-10-06T19:40:30Z"
}
```

> [!WARNING]
> 사용자 지정 상태 페이로드는 16KB의 UTF-16 JSON 텍스트로 제한됩니다. 더 큰 페이로드가 필요한 경우 외부 스토리지를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지속성 타이머에 대해 알아보기](durable-functions-timers.md)
