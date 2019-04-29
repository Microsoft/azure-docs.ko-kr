---
title: 지속성 함수의 작업 허브 - Azure
description: Azure Functions의 지속성 함수 확장에 있는 작업 허브 및 작업 허브 구성 방법을 알아봅니다.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2017
ms.date: 02/21/2019
ms.author: v-junlch
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730273"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>지속성 함수의 작업 허브(Azure Functions)

[지속형 함수](durable-functions-overview.md)의 *작업 허브*는 오케스트레이션에 사용되는 Azure Storage 리소스에 대한 논리적 컨테이너입니다. 오케스트레이터 및 작업 함수는 동일한 작업 허브에 속할 때만 상호 작용할 수 있습니다.

여러 함수 앱이 한 스토리지 계정을 공유하는 경우 *반드시* 함수 앱마다 별도의 작업 허브 이름으로 구성해야 합니다. 한 스토리지 계정에 여러 작업 허브가 포함될 수 있습니다. 다음 다이어그램에서는 공유 및 전용 저장소 계정의 함수 앱당 단일 작업 허브를 보여 줍니다.

![공유 및 전용 저장소 계정을 표시하는 다이어그램](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage 리소스

작업 허브는 다음과 같은 저장소 리소스로 구성됩니다.

- 하나 이상의 제어 큐
- 하나의 작업 항목 큐
- 하나의 기록 테이블
- 하나의 인스턴스 테이블
- 하나 이상의 저장소 Blob을 포함하는 하나의 저장소 컨테이너

오케스트레이터 또는 작업 함수가 실행되거나 실행되도록 예약된 경우 이러한 모든 리소스는 기본 Azure Storage 계정에 자동으로 만들어집니다. [성능 및 크기 조정](durable-functions-perf-and-scale.md) 문서에서는 이러한 리소스를 사용하는 방법을 설명합니다.

## <a name="task-hub-names"></a>작업 허브 이름

작업 허브는 다음 예제와 같이 *host.json*에 선언된 이름으로 식별됩니다.

### <a name="hostjson-functions-1x"></a>host.json(Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json(Functions 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

다음 *host.json* 예제 파일과 같이 앱 설정을 사용하여 작업 허브를 구성할 수도 있습니다.

### <a name="hostjson-functions-1x"></a>host.json(Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json(Functions 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

작업 허브 이름은 `MyTaskHub` 앱 설정의 값으로 설정됩니다. 다음 `local.settings.json`은 `MyTaskHub` 설정을 `samplehubname`으로 정의하는 방법을 보여 줍니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

[OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html)을 사용하여 앱 설정으로 구성된 작업 허브에서 작업하는 함수를 작성하는 방법의 미리 컴파일된 C# 예제는 다음과 같습니다.

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

또한 JavaScript에 필요한 구성은 다음과 같습니다. `function.json` 파일의 작업 허브 속성은 앱 설정을 통해 설정됩니다.

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정하지 않으면 기본 이름은 **DurableFunctionsHub**입니다.

> [!NOTE]
> 이 이름은 공유 저장소 계정에 여러 작업 허브가 있을 때 작업 허브를 다른 작업 허브에서 구분해줍니다. 여러 함수 앱이 한 공유 스토리지 계정을 공유하는 경우 *host.json* 파일의 각 작업 허브에 서로 다른 이름을 명시적으로 구성해야 합니다. 그렇지 않으면 여러 함수 앱이 메시지를 두고 서로 경쟁하게 되며, 이로 인해 정의되지 않은 동작이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)

<!-- Update_Description: wording update -->