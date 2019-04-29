---
title: Azure Functions 1.x에 대한 host.json 참조
description: v1 런타임을 사용하는 Azure Functions host.json 파일에 대한 참조 설명서입니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 44bc5a245d1bcbc8ff53991af4193ef86f7cd704
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107077"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Azure Functions 1.x에 대한 host.json 참조

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [버전 1](functions-host-json-v1.md)
> * [버전 2](functions-host-json.md)

*host.json* 메타데이터 파일에는 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함됩니다. 이 문서에는 v1 런타임에 사용 가능한 설정이 나열되어 있습니다. JSON 스키마는 http://json.schemastore.org/host 에 있습니다.

> [!NOTE]
> 이 문서는 Azure Functions 1.x에 대한 것입니다.  Functions 2.x에서 host.json의 참조는 [Azure Functions 2.x에 대한 host.json 참조](functions-host-json.md)를 참조하세요.

기타 함수 앱 구성 옵션은 [앱 설정](functions-app-settings.md)에서 관리합니다.

일부 host.json 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 파일에서 로컬로 실행할 때만 사용됩니다.

## <a name="sample-hostjson-file"></a>샘플 host.json 파일

다음 샘플 *host.json* 파일에는 가능한 모든 옵션이 지정되어 있습니다.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

이 문서의 다음 섹션에서는 각 최상위 속성에 대해 설명합니다. 달리 명시되지 않을 경우 모두 선택 사항입니다.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

[Event Hub 트리거 및 바인딩](functions-bindings-event-hubs.md)에 대한 구성 설정입니다.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

작업 호스트가 실행하는 함수 목록입니다. 빈 배열은 모든 함수를 실행한다는 의미입니다. [로컬로 실행](functions-run-local.md)할 때만 사용할 수 있습니다. Azure의 함수 앱에서는 이 설정을 사용하는 대신 [Azure Functions에서 함수를 사용하지 않도록 설정하는 방법](disable-function.md)의 단계를 수행하여 특정 함수를 사용하지 않도록 설정해야 합니다.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

모든 함수에 대한 시간 제한 기간을 나타냅니다. 서버리스 사용 계획에서 유효한 범위는 1초에서 10분 사이이고 기본값은 5분입니다. 앱 서비스 계획의 경우 전체 제한은 없으며 기본값은 런타임 버전에 따라 달라집니다.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

[호스트 상태 모니터](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)에 대한 구성 설정

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------| 
|사용|true|기능의 사용 여부를 지정합니다. | 
|healthCheckInterval|10초|정기적인 백그라운드 상태 검사 사이의 간격 | 
|healthCheckWindow|2분|`healthCheckThreshold` 설정과 함께 사용되는 슬라이딩 시간 범위| 
|healthCheckThreshold|6|호스트 재생이 시작되기 전에 상태 검사 실패가 용인되는 최대 횟수| 
|counterThreshold|0.80|성능 카운터가 비정상으로 간주되는 임계값| 

## <a name="http"></a>http

[http 트리거 및 바인딩](functions-bindings-http-webhook.md)에 대한 구성 설정입니다.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*버전 1.x 전용*입니다.

작업 호스트의 고유 ID입니다. 대시가 제거된 소문자 GUID일 수 있습니다. 로컬에서 실행될 때 필요합니다. Azure에서 실행할 때는 ID 값을 설정하지 않는 것이 좋습니다. `id`를 생략하면 ID가 Azure에서 자동으로 생성됩니다. 

여러 함수 앱에서 Storage 계정을 공유하는 경우 각 함수 앱의 `id`가 다른지 확인합니다. `id` 속성을 생략하거나 수동으로 각 함수 앱의 `id`를 다른 값으로 설정할 수 있습니다. 타이머 트리거는 함수 앱이 여러 인스턴스로 확장되는 경우 저장소 잠금을 사용하여 하나의 타이머 인스턴스만이 존재하도록 합니다. 두 개의 함수 앱이 동일한 `id`를 공유하고 각각 타이머 트리거를 사용하는 경우 하나의 타이머만이 실행됩니다.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

[ILogger 개체](functions-monitoring.md#write-logs-in-c-functions) 또는 [context.log](functions-monitoring.md#write-logs-in-javascript-functions)에 의해 기록된 로그 필터링을 제어합니다.

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------| 
|categoryFilter|해당 없음|범주별 필터링을 지정합니다.| 
|defaultLevel|정보|`categoryLevels` 배열에 지정되지 않은 범주가 있으면 이 수준 이상의 로그를 Application Insights로 보내십시오.| 
|categoryLevels|해당 없음|각 범주에 대해 Application Insight에 보낼 최소 로그 수준을 지정하는 범주 배열입니다. 여기에 지정된 범주는 동일한 값으로 시작하는 모든 범주를 제어하며 긴 값이 우선합니다. 앞의 샘플 *host.json* 파일에서 "Host.Aggregator"로 시작하는 모든 범주는 `Information` 수준으로 기록됩니다. "Host.Executor"와 같이 "Host"로 시작하는 다른 모든 범주는 `Error` 수준으로 기록됩니다.| 

## <a name="queues"></a>queues

[저장소 큐 트리거 및 바인딩](functions-bindings-storage-queue.md)에 대한 구성 설정입니다.

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------| 
|maxPollingInterval|60000|큐 폴링 사이 최대 간격(밀리초)입니다.| 
|visibilityTimeout|0|메시지 처리가 실패하는 경우 재시도 사이의 간격입니다.| 
|batchSize|16|함수 런타임이 동시에 검색하고 병렬로 처리하는 큐 메시지 수입니다. 처리되는 개수가 `newBatchThreshold`로 감소하면 런타임은 다른 일괄 처리를 가져와 해당 메시지의 처리를 시작합니다. 따라서 함수당 처리되는 최대 동시 메시지 수는 `batchSize` + `newBatchThreshold`입니다. 이 제한은 큐 트리거 함수에 개별적으로 적용됩니다. <br><br>하나의 큐에 수신된 메시지에 대해 병렬 실행을 방지하려면 `batchSize`을 1로 설정합니다. 그러나 이 설정은 함수 앱이 단일 VM(가상 머신)에서 실행되는 동안에만 동시성을 제거합니다. 함수 앱이 여러 VM에 확장되면 각 VM은 각 큐 트리거 함수의 인스턴스 하나를 실행할 수 있습니다.<br><br>최대 `batchSize`은 32입니다. | 
|maxDequeueCount|5|포이즌 큐로 이동하기 전에 메시지 처리를 시도할 횟수입니다.| 
|newBatchThreshold|batchSize/2|동시에 처리되는 메시지의 수가 이 숫자로 내려갈 때마다 런타임은 다른 일괄 처리를 검색합니다.| 

## <a name="servicebus"></a>serviceBus

[Service Bus 트리거 및 바인딩](functions-bindings-service-bus.md)에 대한 구성 설정입니다.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------| 
|maxConcurrentCalls|16|메시지 펌프가 시작되어야 하는 콜백에 대한 최대 동시 호출 수입니다. 기본적으로 함수 런타임은 여러 개의 메시지를 동시에 처리합니다. 런타임이 큐 또는 토픽 메시지를 한 번에 하나만 처리하도록 하려면, `maxConcurrentCalls`를 1로 설정합니다. | 
|prefetchCount|해당 없음|기본 MessageReceiver에서 사용할 기본 PrefetchCount입니다.| 
|autoRenewTimeout|00:05:00|메시지 잠금이 자동으로 갱신되는 최대 기간입니다.| 

## <a name="singleton"></a>singleton

Singleton 잠금 동작에 대한 구성 설정입니다. 자세한 내용은 [singleton 지원에 대한 GitHub 문제](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)를 참조하세요.

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------| 
|lockPeriod|00:00:15|함수 수준 잠금이 적용되는 기간입니다. 잠금은 자동 갱신됩니다.| 
|listenerLockPeriod|00:01:00|수신기 잠금이 적용되는 기간입니다.| 
|listenerLockRecoveryPollingInterval|00:01:00|시작할 때 수신기 잠금을 가져올 수 없는 경우 수신기 잠금 복구에 사용되는 시간 간격입니다.| 
|lockAcquisitionTimeout|00:01:00|런타임이 잠금을 확보하려고 시도하는 최대 시간입니다.| 
|lockAcquisitionPollingInterval|해당 없음|잠금 확보 시도 사이의 간격입니다.| 

## <a name="tracing"></a>tracing

*버전 1.x*

`TraceWriter` 개체를 사용하여 만드는 로그에 대한 구성 설정입니다. [C# 로깅](functions-reference-csharp.md#logging) 및 [Node.js 로깅](functions-reference-node.md#writing-trace-output-to-the-console)을 참조하세요.

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------| 
|consoleLevel|info|콘솔 로깅의 추적 수준입니다. 옵션은 `off`, `error`, `warning`, `info` 및 `verbose`입니다.|
|fileLoggingMode|debugOnly|파일 로깅에 대한 추적 수준입니다. 옵션은 `never`, `always`, `debugOnly`입니다.| 

## <a name="watchdirectories"></a>watchDirectories

변경 내용을 모니터링해야 하는 [공유 코드 디렉터리](functions-reference-csharp.md#watched-directories) 집합입니다.  이 디렉터리의 코드가 변경되면 변경 내용이 함수에 의해 선택되도록 합니다.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [host.json 파일을 업데이트하는 방법 알아보기](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [환경 변수의 전역 설정 보기](functions-app-settings.md)
