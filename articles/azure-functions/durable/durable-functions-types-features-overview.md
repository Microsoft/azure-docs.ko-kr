---
title: 함수 유형 및 Azure Functions의 지 속성 함수 확장에는 기능
description: Functions 및 Azure Functions의 지 속성 함수 오케스트레이션에서 함수에서 함수 통신을 지 원하는 역할을 형식에 알아봅니다.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731121"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>지 속성 함수 유형 및 기능 (Azure Functions)

지 속성 함수는의 확장인 [Azure Functions](../functions-overview.md)합니다. 상태 저장 오케스트레이션을 함수 실행에 대 한 지 속성 함수를 사용할 수 있습니다. 지 속성 함수는 다른 Azure functions의 구성 된 솔루션. 함수는 지 속성 함수 오케스트레이션에서 다양 한 역할을 수행할 수 있습니다. 

이 문서에서는 지 속성 함수 오케스트레이션에서 사용할 수 있는 함수 유형의 개요를 제공 합니다. 문서 함수에 연결 하 여 몇 가지 일반적인 패턴을 포함 합니다. Durable Functions 하는 방법에 응용 프로그램 개발 과제 해결에 대해 알아봅니다.

![지 속성 함수의 형식을 보여 주는 이미지][1]  

## <a name="types-of-durable-functions"></a>지속형 함수 유형

Azure Functions의 지 속성 함수는 사용할 수 있습니다: 작업, 오 케 스트레이 터를 및 클라이언트입니다.

### <a name="activity-functions"></a>활동 함수

작업 함수는 지 속성 함수 오케스트레이션에서 작업의 기본 단위입니다. 작업 함수는 함수 및 프로세스의 조율 되는 작업. 예를 들어 주문을 처리 하는 지 속성 함수를 만들 수 있습니다. 인벤토리를 확인, 고객, 청구 및 배송 항목을 만드는 작업에 포함 됩니다. 각 태스크는 작업 함수는 것입니다. 

작업 함수에서 할 수 있는 작업의 형식에서 제한 되지 않습니다. 작업 함수를 작성할 수 있습니다 [Durable Functions에서 지 원하는 언어](durable-functions-overview.md#language-support)합니다. 지속형 작업 프레임워크는 호출된 각 활동 함수가 오케스트레이션 중에 한 번 이상 실행되도록 합니다.

사용 하 여는 [작업 트리거](durable-functions-bindings.md#activity-triggers) 작업 함수를 트리거할 수 있습니다. .NET 함수 수신 된 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 매개 변수로 합니다. 트리거를 다른 개체에 바인딩하여 함수에 입력을 전달할 수도 있습니다. JavaScript에서 입력을 통해 액세스할 수 있습니다는 `<activity trigger binding name>` 속성을 [ `context.bindings` 개체](../functions-reference-node.md#bindings)합니다.

작업 함수는 오 케 스트레이 터에 값을 반환할 수도 있습니다. 보내거나 작업 함수에서 많은 수의 값을 반환 하는 경우 사용할 수 있습니다 [튜플이 나 배열을](durable-functions-bindings.md#passing-multiple-parameters)합니다. 오케스트레이션 인스턴스가 에서만 작업 함수를 트리거할 수 있습니다. 작업 함수 및 다른 함수 (예: HTTP 트리거 함수) 일부 코드를 공유할 수 있습니다, 있지만 각 함수에는 트리거는 하나만 있을 수 있습니다.

자세한 정보 및 예제를 참조 하세요 [작업 함수](durable-functions-bindings.md#activity-triggers)합니다.

### <a name="orchestrator-functions"></a>오케스트레이터 함수

오 케 스트레이 터 함수는 작업 실행 방법 및 작업 실행 되는 순서를 설명 합니다. 오 케 스트레이 터 함수 코드에서 오케스트레이션을 설명 (C# 또는 JavaScript)에 표시 된 대로 [Durable Functions 패턴 및 기술 개념](durable-functions-concepts.md)합니다. 오케스트레이션에 서 다양 한 유형의 작업을 포함 하 여 있을 수 있습니다 [작업 함수](#activity-functions), [하위 오케스트레이션](#sub-orchestrations)합니다 [외부 이벤트를 대기](#external-events), 및 [타이머](#durable-timers)합니다. 

오케스트레이터 함수는 [오케스트레이션 트리거](durable-functions-bindings.md#orchestration-triggers)로 트리거되어야 합니다.

오 케 스트레이 터에 의해 시작 되는 [오 케 스트레이 터 클라이언트](#client-functions)합니다. (HTTP, 큐, 이벤트 스트림)에 모든 원본에서 오 케 스트레이 터를 트리거할 수 있습니다. 각 인스턴스는 오케스트레이션의 인스턴스 식별자를 갖습니다. 인스턴스 식별자 (권장) 하는 자동으로 생성 되거나 사용자가 생성 합니다. 인스턴스 식별자를 사용할 수 있습니다 [인스턴스를 관리](durable-functions-instance-management.md) 오케스트레이션 합니다.

자세한 정보 및 예제를 참조 하세요 [오케스트레이션 트리거](durable-functions-bindings.md#orchestration-triggers)합니다.

### <a name="client-functions"></a>클라이언트 함수

클라이언트 함수는 오케스트레이션의 새 인스턴스를 만드는 트리거된 함수입니다. 클라이언트 함수는 지 속성 함수 오케스트레이션 인스턴스를 만들기 위한 진입점입니다. (HTTP, 큐, 이벤트 스트림) 모든 원본의 클라이언트 함수를 트리거할 수 있습니다. 앱에서 지원 되는 언어로 클라이언트 함수를 작성할 수 있습니다. 

클라이언트 기능 수도 있는 [오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client) 바인딩. 클라이언트 함수 오케스트레이션 클라이언트 바인딩을 만들고 지 속성 오케스트레이션 관리를 사용할 수 있습니다. 

클라이언트 함수의 가장 기본적인 예제는 HTTP 트리거 함수는 오 케 스트레이 터 함수를 시작한 다음 상태 검사 응답을 반환 하는 경우 예를 들어 참조 [HTTP API URL 검색](durable-functions-http-api.md#http-api-url-discovery)합니다.

자세한 정보 및 예제를 참조 하세요 [오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client)합니다.

## <a name="features-and-patterns"></a>기능 및 패턴

다음 섹션에서는 지 속성 함수 형식 패턴에 설명합니다.

### <a name="sub-orchestrations"></a>하위 오케스트레이션

오 케 스트레이 터 함수는 작업 함수를 호출할 수는 있지만 다른 오 케 스트레이 터 함수를 호출할 수도 있습니다. 예를 들어 오케스트레이터 함수 라이브러리에서 더 큰 오케스트레이션을 작성할 수 있습니다. 또는 오 케 스트레이 터 함수의 여러 인스턴스에 동시에 실행할 수 있습니다.

자세한 정보 및 예제를 참조 하세요 [하위 오케스트레이션](durable-functions-sub-orchestrations.md)합니다.

### <a name="durable-timers"></a>지속형 타이머

[지 속성 함수](durable-functions-overview.md) 제공 *지 속성 타이머* 지연을 구현 하거나 비동기 작업에 대 한 시간 제한을 설정 하려면 오 케 스트레이 터 함수에서 사용할 수 있는 합니다. 대신 오 케 스트레이 터 함수에서 지 속성 타이머를 사용 하 여 `Thread.Sleep` 하 고 `Task.Delay` (C#) 또는 `setTimeout()` 하 고 `setInterval()` (JavaScript).

자세한 정보 및 예제를 참조 하세요 [지 속성 타이머](durable-functions-timers.md)합니다.

### <a name="external-events"></a>외부 이벤트

오케스트레이터 함수는 외부 이벤트가 오케스트레이션 인스턴스를 업데이트할 때까지 대기할 수 있습니다. 종종이 Durable Functions 기능은 사용자 상호 작용 또는 다른 외부 콜백을 처리 하기 위한 유용 합니다.

자세한 정보 및 예제를 참조 하세요 [외부 이벤트](durable-functions-external-events.md)합니다.

### <a name="error-handling"></a>오류 처리

코드를 사용 하 여 지 속성 함수 오케스트레이션 구현 합니다. 프로그래밍 언어의 오류 처리 기능을 사용할 수 있습니다. 같은 패턴 `try` / `catch` 오케스트레이션에 서 작동 합니다. 

지 속성 함수는 또한 기본 제공 재시도 정책을 사용 하 여 제공 됩니다. 작업을 지연 하 고 예외가 발생 하면 작업을 자동으로 다시 시도 수 있습니다. 오케스트레이션을 중단 하지 않고 일시적 예외 처리를 다시 시도 사용할 수 있습니다.

자세한 정보 및 예제를 참조 하세요 [오류 처리](durable-functions-error-handling.md)합니다.

### <a name="cross-function-app-communication"></a>함수 앱 간 통신

단일 함수 앱의 컨텍스트에서 실행 되는 지 속성 오케스트레이션 하지만 여러 함수 앱에서 오케스트레이션 조정 하기 위해 패턴을 사용할 수 있습니다. 앱 간 통신은 HTTP를 통해 발생할 수 있습니다 하지만 지 속성 프레임 워크를 사용 하 여 각 활동에 대 한 두 개의 앱에서 지 속성 프로세스를 계속 유지할 수 있습니다.

다음 예제에서는 오케스트레이션에 교차-함수 앱을 보여 줍니다 C# 및 JavaScript입니다. 각 예제에서는 하나의 활동 외부 오케스트레이션을 시작합니다. 다른 활동을 검색 하 고 상태를 반환 합니다. 상태 수를 대기 하는 오 케 스트레이 터 `Complete` 계속 하기 전에 합니다.

교차-함수 앱 오케스트레이션의 몇 가지 예는 다음과 같습니다.

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.chinacloudsites.cn/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.chinacloudsites.cn/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>다음 단계

시작 하려면에서 첫 번째 지 속성 함수를 만듭니다 [ C# ](durable-functions-create-first-csharp.md) 하거나 [JavaScript](quickstart-js-vscode.md)합니다.

> [!div class="nextstepaction"]
> [지 속성 함수에 대해 자세히 알아보기](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png

<!-- Update_Description: wording update -->