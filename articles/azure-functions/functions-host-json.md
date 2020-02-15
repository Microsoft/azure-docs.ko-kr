---
title: Azure Functions 2.x에 대한 host.json 참조
description: v2 런타임을 사용하는 Azure Functions host.json 파일에 대한 참조 설명서입니다.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: cc982d3f810c944a5273cbf0cf9778076d119692
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208827"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Azure Functions 2.x 이상에 대 한 호스트 json 참조 

> [!div class="op_single_selector" title1="사용 중인 Azure Functions 런타임 버전을 선택 합니다. "]
> * [버전 1](functions-host-json-v1.md)
> * [버전 2 +](functions-host-json.md)

*host.json* 메타데이터 파일에는 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함됩니다. 이 문서에서는 Azure Functions 런타임의 버전 2.x부터 사용 가능한 설정을 나열 합니다.  

> [!NOTE]
> 이 문서는 Azure Functions 2.x 이상 버전에 대 한 것입니다.  Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

기타 함수 앱 구성 옵션은 [앱 설정](functions-app-settings.md)에서 관리합니다.

일부 host.json 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 파일에서 로컬로 실행할 때만 사용됩니다.

## <a name="sample-hostjson-file"></a>샘플 host.json 파일

버전 2.x +에 대 한 다음 샘플 *호스트 json* 파일에는 모든 가능한 옵션이 지정 되어 있습니다 (내부용 으로만 사용 되는 경우 제외).

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
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 1.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 0.1,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

이 문서의 다음 섹션에서는 각 최상위 속성에 대해 설명합니다. 달리 명시되지 않을 경우 모두 선택 사항입니다.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

이 설정은 [logging](#logging)의 자식입니다.

[샘플링 옵션](./functions-monitoring.md#configure-sampling)을 포함 하 여 Application Insights에 대 한 옵션을 제어 합니다.

전체 JSON 구조는 이전 [예제 호스트. json 파일](#sample-hostjson-file)을 참조 하세요.

> [!NOTE]
> 로그 샘플링으로 인해 Application Insights 모니터 블레이드에 일부 실행이 표시되지 않을 수 있습니다. 로그 샘플링을 방지 하려면 `samplingExcludedTypes: "Request"` `applicationInsights` 값에 추가 합니다.

| 속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| samplingSettings | 해당 없음 | [Applicationinsights. samplingSettings](#applicationinsightssamplingsettings)를 참조 하세요. |
| samplingExcludedTypes | null | 샘플링 하지 않으려는 형식의 세미콜론으로 구분 된 목록입니다. 인식되는 형식: 종속성, 이벤트, 예외, 페이지 보기, 요청, 추적 지정 된 형식의 모든 인스턴스가 전송 됩니다. 지정 되지 않은 형식이 샘플링 됩니다. |
| samplingIncludedTypes | null | 샘플링할 형식의 세미콜론으로 구분 된 목록입니다. 빈 목록은 모든 형식을 의미 합니다. 여기에 나열 된 형식 `samplingExcludedTypes` 재정의 형식에 나열 됩니다. 인식되는 형식: 종속성, 이벤트, 예외, 페이지 보기, 요청, 추적 지정 된 형식의 모든 인스턴스가 전송 됩니다. 지정 되지 않은 형식이 샘플링 됩니다. |
| enableLiveMetrics | true | 라이브 메트릭 수집을 사용 하도록 설정 합니다. |
| enableDependencyTracking | true | 종속성 추적을 사용 합니다. |
| enablePerformanceCountersCollection | true | Kudu 성능 카운터 수집을 사용 하도록 설정 합니다. |
| liveMetricsInitializationDelay | 00:00:15 | 내부 전용입니다. |
| httpAutoCollectionOptions | 해당 없음 | [Applicationinsights](#applicationinsightshttpautocollectionoptions)를 참조 하세요. |
| snapshotConfiguration | 해당 없음 | [Applicationinsights](#applicationinsightssnapshotconfiguration)를 참조 하세요. |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| isEnabled | true | 샘플링을 사용 여부를 설정합니다. | 
| maxTelemetryItemsPerSecond | 20 | 각 서버 호스트에서 초당 기록한 원격 분석 항목의 수입니다. 앱이 여러 호스트에서 실행 되는 경우이 값을 줄여서 전체 대상 트래픽 요금 내에 유지 합니다. | 
| evaluationInterval | 01:00:00 | 현재 원격 분석 속도를 다시 평가 하는 간격입니다. 평가는 이동 평균으로 수행됩니다. 원격 분석이 급격히 증가하는 경우 이 간격을 줄일 수 있습니다. |
| initialSamplingPercentage| 1.0 | 백분율을 동적으로 변경 하기 위해 샘플링 프로세스가 시작 될 때 적용 되는 초기 샘플링 비율입니다. 디버깅 하는 동안 값을 줄이지 마십시오. |
| samplingPercentageIncreaseTimeout | 00:00:01 | 샘플링 비율 값이 변경 되 면이 속성은 이후 Application Insights에서 더 많은 데이터를 캡처하기 위해 샘플링 비율을 다시 발생 시킬 수 있는 시간을 결정 합니다. |
| samplingPercentageDecreaseTimeout | 00:00:01 | 샘플링 비율 값이 변경 되 면이 속성은 더 적은 데이터를 캡처하기 위해 샘플링 비율을 다시 낮출 수 있는 Application Insights 시간을 결정 합니다. |
| minSamplingPercentage | 0.1 | 샘플링 비율이 변경 됨에 따라이 속성은 허용 되는 최소 샘플링 비율을 결정 합니다. |
| maxSamplingPercentage | 0.1 | 샘플링 비율이 변경 됨에 따라이 속성은 허용 되는 최대 샘플링 비율을 결정 합니다. |
| movingAverageRatio | 1.0 | 이동 평균 계산에서 가중치는 가장 최근의 값에 할당됩니다. 1보다 작거나 같은 값을 사용합니다. 값이 작을수록 알고리즘은 갑작스런 변화에 덜 반응합니다. |

### <a name="applicationinsightshttpautocollectionoptions"></a>httpAutoCollectionOptions

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | HTTP 트리거에 대 한 확장 된 HTTP 요청 정보를 사용 하거나 사용 하지 않도록 설정 합니다. 들어오는 요청 상관 관계 헤더, 다중 계측 키 지원, HTTP 메서드, 경로 및 응답입니다. |
| enableW3CDistributedTracing | true | W3C 분산 추적 프로토콜을 지원 하거나 사용 하지 않도록 설정 하 고 레거시 상관 관계 스키마를 설정 합니다. `enableHttpTriggerExtendedInfoCollection` true 이면 기본적으로 사용 하도록 설정 됩니다. `enableHttpTriggerExtendedInfoCollection` false 인 경우이 플래그는 들어오는 요청이 아닌 보내는 요청에만 적용 됩니다. |
| enableResponseHeaderInjection | true | 다중 구성 요소 상관 관계 헤더의 삽입을 응답으로 사용 하거나 사용 하지 않도록 설정 합니다. 주입을 사용 하도록 설정 하면 여러 개의 계측 키를 사용할 때 응용 프로그램을 구성 하 Application Insights 수 있습니다. `enableHttpTriggerExtendedInfoCollection` true 이면 기본적으로 사용 하도록 설정 됩니다. `enableHttpTriggerExtendedInfoCollection` false 인 경우에는이 설정이 적용 되지 않습니다. |

### <a name="applicationinsightssnapshotconfiguration"></a>snapshotConfiguration

스냅숏에 대 한 자세한 내용은 [.net 앱의 예외에 대 한 스냅숏 디버그](/azure/azure-monitor/app/snapshot-debugger) 및 [Application Insights 스냅숏 디버거 또는 스냅숏 보기를 사용 하도록 설정 하는 문제 해결](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)을 참조 하세요.

|속성 | 기본값 | Description |
| --------- | --------- | --------- | 
| agentEndpoint | null | Application Insights 스냅숏 디버거 서비스에 연결 하는 데 사용 되는 끝점입니다. Null 인 경우 기본 끝점이 사용 됩니다. |
| captureSnapshotMemoryWeight | 0.5 | 스냅숏을 만들기에 충분 한 메모리가 있는지 확인할 때 현재 프로세스 메모리 크기에 지정 된 가중치입니다. 예상 값은 적절 한 소수 부분 (0 < CaptureSnapshotMemoryWeight < 1) 보다 큽니다. |
| failedRequestLimit | 3 | 원격 분석 프로세서를 사용 하지 않도록 설정 하기 전에 스냅숏을 요청 하는 데 실패 한 요청 수에 대 한 제한입니다.|
| handleUntrackedExceptions | true | Application Insights 원격 분석에서 추적 하지 않는 예외 추적을 사용 하거나 사용 하지 않도록 설정 합니다. |
| isEnabled | true | 스냅숏 수집을 사용 하거나 사용 하지 않도록 설정 합니다. | 
| isEnabledInDeveloperMode | false | 개발자 모드에서 스냅숏 컬렉션을 사용 하거나 사용 하지 않도록 설정 합니다. |
| isEnabledWhenProfiling | true | Application Insights Profiler에서 자세한 프로 파일링 세션을 수집 하는 경우에도 스냅숏 만들기를 사용 하거나 사용 하지 않도록 설정 합니다. |
| isExceptionSnappointsEnabled | false | 예외 필터링을 사용 하거나 사용 하지 않도록 설정 합니다. |
| isLowPrioritySnapshotUploader | true | SnapshotUploader 프로세스를 보통 우선 순위로 실행할지 여부를 결정 합니다. |
| Maximumcollection설계도 크기 | 50 | 1에서 9999 사이의 범위에서 언제 든 지 추적할 수 있는 최대 문제 수입니다. |
| maximumSnapshotsRequired | 3 | 1 ~ 999 범위에서 단일 문제에 대해 수집 된 최대 스냅숏 수입니다. 응용 프로그램에서 개별 throw 문으로 문제를 생각할 수 있습니다. 문제에 대해 수집 된 스냅숏 수가이 값에 도달 하면 문제 카운터가 다시 설정 될 때까지 해당 문제에 대해 더 이상 스냅숏이 수집 되지 않습니다 (`problemCounterResetInterval`참조) `thresholdForSnapshotting` 한도에 다시 도달 합니다. |
| problemCounterResetInterval | 24:00:00 | 1 분에서 7 일 사이에 문제 카운터를 다시 설정 하는 빈도입니다. 이 간격에 도달 하면 모든 문제 수가 0으로 다시 설정 됩니다. 스냅숏 수행에 대 한 임계값에 이미 도달 했지만 `maximumSnapshotsRequired`의 스냅숏 수를 아직 생성 하지 않은 기존 문제는 활성 상태로 유지 됩니다. |
| provideAnonymousTelemetry | true | 익명 사용 및 오류 원격 분석을 Microsoft로 보낼지 여부를 결정 합니다. Microsoft에 문의 하 여 스냅숏 디버거 문제를 해결 하는 데 도움이 되는 경우이 원격 분석을 사용할 수 있습니다. 사용 패턴을 모니터링 하는 데도 사용 됩니다. |
| reconnectInterval | 00:15:00 | 스냅숏 디버거 끝점에 다시 연결 하는 빈도입니다. 허용 되는 범위는 1 분에서 1 일 사이입니다. |
| shadowCopyFolder | null | 이진 파일을 섀도 복사 하는 데 사용할 폴더를 지정 합니다. 설정 하지 않은 경우 다음 환경 변수에 지정 된 폴더는 Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP 순서로 시도 됩니다. |
| shareUploaderProcess | true | True 이면 SnapshotUploader의 한 인스턴스만 InstrumentationKey을 공유 하는 여러 앱에 대 한 스냅숏을 수집 하 고 업로드 합니다. False로 설정 하면 각 (ProcessName, InstrumentationKey) 튜플에 대해 SnapshotUploader가 고유 합니다. |
| snapshotInLowPriorityThread | true | 낮은 IO 우선 순위 스레드에서 스냅숏 처리 여부를 결정 합니다. 스냅숏 만들기는 빠른 작업 이지만, 스냅숏을 스냅숏 디버거 서비스에 업로드 하려면 먼저 디스크에 미니 덤프로 써야 합니다. SnapshotUploader 프로세스에서 발생 합니다. 이 값을 true로 설정 하면 우선 순위가 낮은 IO를 사용 하 여 리소스에 대 한 응용 프로그램과 경쟁 하지 않는 미니 덤프를 작성 합니다. 이 값을 false로 설정 하면 응용 프로그램의 속도가 느려지는 대신 미니 덤프 생성 속도가 빨라집니다. |
| snapshotsPerDayLimit | 30 | 1 일 (24 시간)에 허용 되는 최대 스냅숏 수입니다. 이 제한은 Application Insights 서비스 쪽에도 적용 됩니다. 업로드는 응용 프로그램 (즉, 계측 키 당) 당 50로 제한 됩니다. 이 값을 사용 하면 업로드 중에 결국 거부 될 추가 스냅숏이 생성 되지 않습니다. 값이 0 이면 제한을 완전히 제거 하지 않는 것이 좋습니다. |
| snapshotsPerTenMinutesLimit | 1 | 10 분 내에 허용 되는 최대 스냅숏 수입니다. 이 값에 상한이 없지만 응용 프로그램의 성능에 영향을 줄 수 있으므로 프로덕션 워크 로드에 대해 주의를 기울여야 합니다. 스냅숏 만들기는 빠르지만 스냅숏의 미니 덤프를 만들어 스냅숏 디버거 서비스에 업로드 하는 작업은 리소스 (CPU 및 i/o 모두)에 대해 응용 프로그램과 경쟁 하는 훨씬 느린 작업입니다. |
| t | null | 미니 덤프 및 업 로더 로그 파일을 쓸 폴더를 지정 합니다. 설정 하지 않으면 *%TEMP%\Dumps* 가 사용 됩니다. |
| Thresholdforsnapshotting은 | 1 | Application Insights에서 스냅숏을 요청 하기 전에 예외가 표시 되어야 하는 횟수입니다. |
| uploaderProxy | null | 스냅숏 업 로더 프로세스에서 사용 되는 프록시 서버를 재정의 합니다. 응용 프로그램이 프록시 서버를 통해 인터넷에 연결 하는 경우이 설정을 사용 해야 할 수 있습니다. Snapshot Collector는 응용 프로그램의 프로세스 내에서 실행 되 고 동일한 프록시 설정을 사용 합니다. 그러나 스냅숏 업 로더는 별도의 프로세스로 실행 되며 프록시 서버를 수동으로 구성 해야 할 수도 있습니다. 이 값이 null 인 경우 Snapshot Collector는 시스템 .Net. WebRequest. DefaultWebProxy를 검사 하 고 값을 Snapshot 업 로더로 전달 하 여 프록시 주소를 자동 검색 합니다. 이 값이 null이 아닌 경우 인코딩이가 사용 되지 않으며 여기에 지정 된 프록시 서버가 스냅숏 업 로더에 사용 됩니다. |

## <a name="cosmosdb"></a>cosmosDb

구성 설정은 [Cosmos DB 트리거 및 바인딩](functions-bindings-cosmosdb-v2.md#host-json)에서 찾을 수 있습니다.

## <a name="durabletask"></a>durableTask

구성 설정은 [지속형 함수에 대한 바인딩](durable/durable-functions-bindings.md#host-json)에서 찾을 수 있습니다.

## <a name="eventhub"></a>eventHub

구성 설정은 [이벤트 허브 트리거 및 바인딩](functions-bindings-event-hubs.md#host-json)에서 찾을 수 있습니다. 

## <a name="extensions"></a>확장

[http](#http), [eventHub](#eventhub) 등의 모든 바인딩 관련 설정이 포함된 개체를 반환하는 속성입니다.

## <a name="extensionbundle"></a>extensionBundle 

확장 번들을 사용 하면 호환 되는 함수 바인딩 확장 집합을 함수 앱에 추가할 수 있습니다. 자세히 알아보려면 [로컬 개발용 확장 번들](functions-bindings-register.md#extension-bundles)을 참조 하세요.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

작업 호스트가 실행하는 함수 목록입니다. 빈 배열은 모든 함수를 실행한다는 의미입니다. [로컬로 실행](functions-run-local.md)할 때만 사용할 수 있습니다. Azure의 함수 앱에서는 이 설정을 사용하는 대신 [Azure Functions에서 함수를 사용하지 않도록 설정하는 방법](disable-function.md)의 단계를 수행하여 특정 함수를 사용하지 않도록 설정해야 합니다.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

모든 함수에 대한 시간 제한 기간을 나타냅니다. Timespan 문자열 형식을 따릅니다. 서버리스 사용 계획에서 유효한 범위는 1초에서 10분 사이이고 기본값은 5분입니다.  

프리미엄 계획에서 유효한 범위는 1 초에서 60 분 사이 이며 기본값은 30 분입니다.

전용 (App Service) 계획에서는 전체 제한이 없으며 기본값은 30 분입니다. `-1` 값은 바인딩되지 않은 실행을 나타내지만 고정 상한을 유지 하는 것이 좋습니다.

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

|속성  |기본값 | Description |
|---------|---------|---------| 
|사용|true|기능의 사용 여부를 지정합니다. | 
|healthCheckInterval|10초|정기적인 백그라운드 상태 검사 사이의 간격 | 
|healthCheckWindow|2분|`healthCheckThreshold` 설정과 함께 사용되는 슬라이딩 시간 범위| 
|healthCheckThreshold|6|호스트 재생이 시작되기 전에 상태 검사 실패가 용인되는 최대 횟수| 
|counterThreshold|0.80|성능 카운터가 비정상으로 간주되는 임계값| 

## <a name="http"></a>http

구성 설정은 [http 트리거 및 바인딩](functions-bindings-http-webhook-output.md#hostjson-settings)에서 찾을 수 있습니다.

## <a name="logging"></a>logging

Application Insights를 포함한 함수 앱의 로깅 동작을 제어합니다.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
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

|속성  |기본값 | Description |
|---------|---------|---------|
|fileLoggingMode|debugOnly|활성화할 파일 로깅의 수준을 정의합니다.  옵션은 `never`, `always`, `debugOnly`입니다. |
|logLevel|해당 없음|앱의 함수에 대한 로그 범주 필터링을 정의하는 개체입니다. 버전 2.x 이상에서는 로그 범주 필터링을 위한 ASP.NET Core 레이아웃을 따릅니다. 이 설정을 통해 특정 함수에 대 한 로깅을 필터링 할 수 있습니다. 자세한 내용은 ASP.NET Core 설명서의 [로그 필터링](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)을 참조하세요. |
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

|속성  |기본값 | Description |
|---------|---------|---------| 
|isEnabled|false|콘솔 로깅을 사용하거나 사용하지 않도록 설정합니다.| 

## <a name="manageddependency"></a>managedDependency

관리 되는 종속성은 현재 PowerShell 기반 함수 에서만 지원 되는 기능입니다. 이를 통해 서비스에서 종속성을 자동으로 관리할 수 있습니다. `enabled` 속성이 `true`로 설정 된 경우 `requirements.psd1` 파일이 처리 됩니다. 부 버전이 릴리스되면 종속성이 업데이트 됩니다. 자세한 내용은 PowerShell 문서의 [관리 되는 종속성](functions-reference-powershell.md#dependency-management) 을 참조 하세요.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

구성 설정은 [스토리지 큐 트리거 및 바인딩](functions-bindings-storage-queue.md#host-json)에서 찾을 수 있습니다.  

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

|속성  |기본값 | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|함수 수준 잠금이 적용되는 기간입니다. 잠금은 자동 갱신됩니다.| 
|listenerLockPeriod|00:01:00|수신기 잠금이 적용되는 기간입니다.| 
|listenerLockRecoveryPollingInterval|00:01:00|시작할 때 수신기 잠금을 가져올 수 없는 경우 수신기 잠금 복구에 사용되는 시간 간격입니다.| 
|lockAcquisitionTimeout|00:01:00|런타임이 잠금을 확보하려고 시도하는 최대 시간입니다.| 
|lockAcquisitionPollingInterval|해당 없음|잠금 확보 시도 사이의 간격입니다.| 

## <a name="version"></a>버전

이 값은 host-a의 스키마 버전을 나타냅니다. 버전 문자열 `"version": "2.0"` v2 runtime 또는 이후 버전을 대상으로 하는 함수 앱에 필요 합니다. V2와 v3 사이에는 호스트 json 스키마가 변경 되지 않습니다.

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
