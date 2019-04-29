---
title: Azure Event Grid에 게시하는 Durable Functions(미리 보기)
description: Durable Functions에 대한 자동 Azure Event Grid 게시를 구성하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733774"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Azure Event Grid에 게시하는 Durable Functions(미리 보기)

이 문서에서는 오케스트레이션 수명 주기 이벤트(예: 만들어짐, 완료됨 및 실패)를 사용자 지정 [Azure Event Grid 토픽](https://docs.microsoft.com/azure/event-grid/overview)에 게시하도록 Durable Functions를 설정하는 방법을 설명합니다.

이 기능이 유용한 몇 가지 시나리오는 다음과 같습니다.

* **DevOps 시나리오(예: 파란색/녹색 배포)**: [병렬 배포 전략](durable-functions-versioning.md#side-by-side-deployments)을 구현하기 전에 실행 중인 작업이 있는지 여부를 알아야 할 수 있습니다.

* **고급 모니터링 및 진단 지원**: 쿼리에 최적화된 외부 저장소(예: SQL 데이터베이스 또는 CosmosDB)에서 오케스트레이션 상태 정보를 추적할 수 있습니다.

* **장기 실행 백그라운드 작업**: 장기 실행 백그라운드 작업에 Durable Functions를 사용하는 경우 이 기능을 사용하면 현재 상태를 알 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Durable Functions 프로젝트에서 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc 이상을 설치합니다.
* [Azure Storage 에뮬레이터](https://docs.microsoft.com/azure/storage/common/storage-use-emulator)를 설치합니다.
* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 설치하거나 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 사용합니다.

## <a name="create-a-custom-event-grid-topic"></a>사용자 지정 event grid 항목 만들기

Durable Functions에서 이벤트를 보내기 위한 event grid 토픽을 만듭니다. 다음 지침에서는 Azure CLI를 사용하여 토픽을 만드는 방법을 보여 줍니다. PowerShell 또는 Azure Portal을 사용하여 수행하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [EventGrid 빠른 시작: 사용자 지정 이벤트 만들기 - PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid 빠른 시작: 사용자 지정 이벤트 만들기 - Azure Portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

`az group create` 명령을 사용하여 리소스 그룹을 만듭니다. 현재 Azure Event Grid는 모든 영역을 지원 하지 않습니다. 지원 되는 지역에 대 한 내용은 참조 하세요. 합니다 [Azure Event Grid 개요](https://docs.microsoft.com/azure/event-grid/overview)합니다.

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기

Event grid 토픽에 이벤트를 게시할 사용자 정의 엔드포인트를 제공 합니다. `<topic_name>`을 토픽의 고유한 이름으로 바꿉니다. 토픽 이름은 DNS 항목이 되므로 고유해야 합니다.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>엔드포인트 및 키 가져오기

토픽의 엔드포인트를 가져옵니다. `<topic_name>`을 선택한 이름으로 바꿉니다.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

토픽 키를 가져옵니다. `<topic_name>`을 선택한 이름으로 바꿉니다.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

이제 토픽에 이벤트를 보낼 수 있습니다.

## <a name="configure-azure-event-grid-publishing"></a>Azure Event Grid 게시 구성

Durable Functions 프로젝트에서 `host.json` 파일을 찾습니다.

`durableTask` 속성에 `eventGridTopicEndpoint` 및 `eventGridKeySettingName`을 추가합니다.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Azure Event Grid 구성 가능한 속성에서 찾을 수 있습니다 합니다 [host.json 설명서](../functions-host-json.md#durabletask)합니다. 구성한 후의 `host.json` 파일을 함수 앱 수명 주기 이벤트를 event grid 토픽을 보냅니다. 로컬 및 Azure에서 함수 앱을 실행할 때 작동 합니다. ' '

Function App 및 `local.setting.json`에서 토픽 키에 대한 앱 설정을 지정합니다. 다음 JSON은 로컬 디버깅에 대한 `local.settings.json` 샘플입니다. `<topic_key>`를 토픽 키로 바꿉니다.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

[저장소 에뮬레이터](https://docs.microsoft.com/azure/storage/common/storage-use-emulator)가 작동하는지 확인합니다. 실행하기 전에 `AzureStorageEmulator.exe clear all` 명령을 실행하는 것이 좋습니다.

## <a name="create-functions-that-listen-for-events"></a>이벤트를 수신 대기하는 함수 만들기

Function App을 만듭니다. Event grid 토픽와 동일한 지역에서 찾습니다 하는 것이 좋습니다.

### <a name="create-an-event-grid-trigger-function"></a>Event grid 트리거 함수 만들기

수명 주기 이벤트를 받는 함수를 만듭니다. **사용자 지정 함수**를 선택합니다.

![사용자 지정 함수 만들기 선택](./media/durable-functions-event-publishing/functions-portal.png)

Event Grid 트리거를 선택하고, `C#`을 선택합니다.

![Event Grid 트리거 선택](./media/durable-functions-event-publishing/eventgrid-trigger.png)

함수 이름을 입력한 다음, `Create`를 선택합니다.

![Event Grid 트리거 만들기](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

다음 코드가 있는 함수가 만들어집니다.

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

`Add Event Grid Subscription`를 선택합니다. 이 작업에는 사용자가 만든 event grid 토픽에 대 한 event grid 구독을 추가 합니다. 자세한 내용은 [Azure Event Grid의 개념](https://docs.microsoft.com/azure/event-grid/concepts)을 참조하세요.

![Event Grid 트리거 링크 선택](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

**토픽 종류**에 대해 `Event Grid Topics`를 선택합니다. Event grid 토픽에 대해 만든 리소스 그룹을 선택 합니다. Event grid 토픽의 인스턴스를 선택 합니다. `Create`를 누릅니다.

![Event Grid 구독을 만듭니다.](./media/durable-functions-event-publishing/eventsubscription.png)

이제 수명 주기 이벤트를 받을 준비가 되었습니다.

## <a name="create-durable-functions-to-send-the-events"></a>이벤트를 보내는 Durable Functions 만들기

Durable Functions 프로젝트에서 로컬 컴퓨터에 대한 디버깅을 시작합니다.  다음 코드는 Durable Functions에 대한 템플릿 코드와 동일합니다. 이미 로컬 컴퓨터에 `host.json` 및 `local.settings.json`을 구성했습니다.

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Postman 또는 브라우저를 통해 `Sample_HttpStart`를 호출하면 Durable Functions에서 수명 주기 이벤트를 보내기 시작합니다. 로컬 디버깅에 대한 엔드포인트는 일반적으로 `http://localhost:7071/api/Sample_HttpStart`입니다.

Azure Portal에서 만든 함수의 로그를 봅니다.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>이벤트 스키마

다음 목록에서는 수명 주기 이벤트 스키마를 설명합니다.

* **`id`**: Event grid 이벤트에 대 한 고유 식별자입니다.
* **`subject`**: 이벤트 주체에 대한 경로입니다. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`는 `Running`, `Completed`, `Failed` 및 `Terminated`입니다.  
* **`data`**: Durable Functions 특정 매개 변수입니다.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) 이름입니다.
  * **`functionName`**: 오케스트레이터 함수 이름입니다.
  * **`instanceId`**: Durable Functions instanceId입니다.
  * **`reason`**: 추적 이벤트와 관련된 추가 데이터입니다. 자세한 내용은 [Durable Functions의 진단(Azure Functions)](durable-functions-diagnostics.md)을 참조하세요.
  * **`runtimeStatus`**: 오케스트레이션 런타임 상태입니다. 실행 중, 완료됨, 실패, 취소됨입니다.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: 이벤트 시간(UTC)입니다.
* **`dataVersion`**: 수명 주기 이벤트 스키마의 버전입니다.
* **`metadataVersion`**:  메타데이터의 버전입니다.
* **`topic`**: Event grid 토픽 리소스입니다.

## <a name="how-to-test-locally"></a>로컬로 테스트하는 방법

로컬로 테스트하려면 [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok)를 사용합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Durable Functions의 인스턴스 관리 알아보기](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Durable Functions의 버전 관리 알아보기](durable-functions-versioning.md)
