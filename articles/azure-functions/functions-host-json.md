---
title: Azure Functions 2.x에 대한 host.json 참조
description: v2 런타임을 사용하는 Azure Functions host.json 파일에 대한 참조 설명서입니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 89c4723e83979f89721677146810abdf99fb5d11
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310477"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Azure Functions 2.x에 대한 host.json 참조  

> [!div class="op_single_selector" title1="사용 하는 Azure Functions 런타임의 버전을 선택 합니다. "]
> * [버전 1](functions-host-json-v1.md)
> * [버전 2](functions-host-json.md)

*host.json* 메타데이터 파일에는 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함됩니다. 이 문서에는 v2 런타임에 사용 가능한 설정이 나열되어 있습니다.  

> [!NOTE]
> 이 문서는 Azure Functions 2.x에 대한 것입니다.  Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

기타 함수 앱 구성 옵션은 [앱 설정](functions-app-settings.md)에서 관리합니다.

일부 host.json 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 파일에서 로컬로 실행할 때만 사용됩니다.

## <a name="sample-hostjson-file"></a>샘플 host.json 파일

다음 샘플 *host.json* 파일에는 가능한 모든 옵션이 지정되어 있습니다.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "managedDependency": {
        "enabled": true
    }
}
```

이 문서의 다음 섹션에서는 각 최상위 속성에 대해 설명합니다. 달리 명시되지 않을 경우 모두 선택 사항입니다.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

이 설정은 [logging](#logging)의 자식입니다.

[Application Insights에서 샘플링 기능](./functions-monitoring.md#configure-sampling)을 제어합니다.

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

> [!NOTE]
> 로그 샘플링으로 인해 Application Insights 모니터 블레이드에 일부 실행이 표시되지 않을 수 있습니다.

|자산  |Default | 설명 |
|---------|---------|---------| 
|isEnabled|true|샘플링을 사용 여부를 설정합니다.| 
|maxTelemetryItemsPerSecond|5|샘플링이 시작되는 임계값입니다.| 

## <a name="cosmosdb"></a>cosmosDb

구성 설정은 [Cosmos DB 트리거 및 바인딩](functions-bindings-cosmosdb-v2.md#host-json)에서 찾을 수 있습니다.

## <a name="durabletask"></a>durableTask

구성 설정은 [지속형 함수에 대한 바인딩](durable/durable-functions-bindings.md#host-json)에서 찾을 수 있습니다.

## <a name="eventhub"></a>eventHub

구성 설정은 [이벤트 허브 트리거 및 바인딩](functions-bindings-event-hubs.md#host-json)에서 찾을 수 있습니다. 

## <a name="extensions"></a>확장

[http](#http), [eventHub](#eventhub) 등의 모든 바인딩 관련 설정이 포함된 개체를 반환하는 속성입니다.

## <a name="functions"></a>functions

작업 호스트가 실행하는 함수 목록입니다. 빈 배열은 모든 함수를 실행한다는 의미입니다. [로컬로 실행](functions-run-local.md)할 때만 사용할 수 있습니다. Azure의 함수 앱에서는 이 설정을 사용하는 대신 [Azure Functions에서 함수를 사용하지 않도록 설정하는 방법](disable-function.md)의 단계를 수행하여 특정 함수를 사용하지 않도록 설정해야 합니다.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

모든 함수에 대한 시간 제한 기간을 나타냅니다. 서버리스 사용 계획에서 유효한 범위는 1초에서 10분 사이이고 기본값은 5분입니다. 전용된 App Service 계획에서 전체 제한이 이며 기본값은 30 분입니다. 값 `-1` 무제한 실행을 나타냅니다.

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
|enabled|true|기능의 사용 여부를 지정합니다. | 
|healthCheckInterval|10초|정기적인 백그라운드 상태 검사 사이의 간격 | 
|healthCheckWindow|2분|`healthCheckThreshold` 설정과 함께 사용되는 슬라이딩 시간 범위| 
|healthCheckThreshold|6|호스트 재생이 시작되기 전에 상태 검사 실패가 용인되는 최대 횟수| 
|counterThreshold|0.80|성능 카운터가 비정상으로 간주되는 임계값| 

## <a name="http"></a>http

구성 설정은 [http 트리거 및 바인딩](functions-bindings-http-webhook.md)에서 찾을 수 있습니다.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>logging

Application Insights를 포함한 함수 앱의 로깅 동작을 제어합니다.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------|
|fileLoggingMode|debugOnly|활성화할 파일 로깅의 수준을 정의합니다.  옵션은 `never`, `always`, `debugOnly`입니다. |
|logLevel|해당 없음|앱의 함수에 대한 로그 범주 필터링을 정의하는 개체입니다. 버전 2.x는 로그 범주 필터링용 ASP.NET Core 레이아웃을 따릅니다. 따라서 특정 함수의 로깅을 필터링할 수 있습니다. 자세한 내용은 ASP.NET Core 설명서의 [로그 필터링](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)을 참조하세요. |
|console|해당 없음| [콘솔](#console) 로깅 설정입니다. |
|applicationInsights|해당 없음| [applicationInsights](#applicationinsights) 설정입니다. |

## <a name="console"></a>console

이 설정은 [logging](#logging)의 자식입니다. 디버깅 모드가 아닌 경우 콘솔 로깅을 제어합니다.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|자산  |Default | 설명 |
|---------|---------|---------| 
|isEnabled|false|콘솔 로깅을 사용하거나 사용하지 않도록 설정합니다.| 

## <a name="queues"></a>queues

구성 설정은 [저장소 큐 트리거 및 바인딩](functions-bindings-storage-queue.md#host-json)에서 찾을 수 있습니다.  

## <a name="sendgrid"></a>sendGrid

구성 설정은 [SendGrid 트리거 및 바인딩](functions-bindings-sendgrid.md#host-json)에서 찾을 수 있습니다.

## <a name="servicebus"></a>serviceBus

구성 설정은 [Service Bus 트리거 및 바인딩](functions-bindings-service-bus.md#host-json)에서 찾을 수 있습니다.

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

## <a name="version"></a>버전

v2 런타임을 대상으로 하는 함수 앱에서는 버전 문자열 `"version": "2.0"`이 필수 항목입니다.

## <a name="watchdirectories"></a>watchDirectories

변경 내용을 모니터링해야 하는 [공유 코드 디렉터리](functions-reference-csharp.md#watched-directories) 집합입니다.  이 디렉터리의 코드가 변경되면 변경 내용이 함수에 의해 선택되도록 합니다.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

관리 되는 종속성은 현재 미리 보기 기능 PowerShell 기반 함수 에서만 지원 됩니다. 종속성을 서비스에 의해 자동으로 관리할 수 있습니다. 활성화 속성이 설정 된 경우 true로 합니다 [requirements.psd1](functions-reference-powershell.md#dependency-management) 파일 처리 됩니다. 종속성 모든 부 버전이 새로 릴리스될 때 업데이트 됩니다.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [host.json 파일을 업데이트하는 방법 알아보기](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [환경 변수의 전역 설정 보기](functions-app-settings.md)
