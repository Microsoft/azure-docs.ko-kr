---
title: 포함 파일
description: 포함 파일
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 284aad7dd5b51268b1c8ff8a02f4489d6f1cd3d9
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279487"
---
[지속형 함수](../articles/azure-functions/durable-functions-overview.md)에 대한 구성 설정입니다.

### <a name="durable-functions-1x"></a>1\.x Durable Functions

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
}
```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정되지 않은 경우 함수 앱의 기본 작업 허브 이름은 **DurableFunctionsHub**입니다. 자세한 내용은 [작업 허브](../articles/azure-functions/durable-functions-task-hubs.md)를 참조하세요.

|속성  |기본값 | Description |
|---------|---------|---------|
|hubName|DurableFunctionsHub|여러 Durable Functions 애플리케이션이 동일한 스토리지 백 엔드를 사용하더라도 대체 [작업 허브](../articles/azure-functions/durable-functions-task-hubs.md) 이름을 사용하면 이러한 애플리케이션을 서로 구분할 수 있습니다.|
|controlQueueBatchSize|32|제어 큐에서 한 번에 끌어올 메시지의 수입니다.|
|controlQueueBufferThreshold|256|한 번에 메모리에 버퍼링 될 수 있는 제어 큐 메시지 수입니다 .이 시점에서 디스패처는 추가 메시지를 큐에서 제거할 때까지 대기 합니다.|
|partitionCount |4|제어 큐에 대한 파티션 수입니다. 1에서 16 사이의 양의 정수일 수 있습니다.|
|controlQueueVisibilityTimeout |5분|큐에서 제거된 제어 큐 메시지의 표시 여부 시간 제한입니다.|
|workItemQueueVisibilityTimeout |5분|큐에서 제거된 작업 항목 큐 메시지의 표시 여부 시간 제한입니다.|
|maxConcurrentActivityFunctions |현재 컴퓨터에 있는 프로세서 수의 10배입니다.|단일 호스트 인스턴스에서 동시에 처리할 수 있는 작업 함수는 최대 수입니다.|
|maxConcurrentOrchestratorFunctions |현재 컴퓨터에 있는 프로세서 수의 10배입니다.|단일 호스트 인스턴스에서 동시에 처리할 수 있는 오케스트레이터 함수의 최대 개수입니다.|
|maxQueuePollingInterval|30초|*Hh: mm: ss* 형식의 최대 제어 및 작업 항목 큐 폴링 간격입니다. 값이 높을수록 메시지 처리 대기 시간이 높아질 수 있습니다. 값이 낮을수록 저장소 트랜잭션이 증가 하 여 저장소 비용이 높아질 수 있습니다.|
|azureStorageConnectionStringName |AzureWebJobsStorage|기본 Azure Storage 리소스를 관리하는 데 사용되는 Azure Storage 연결 문자열이 있는 앱 설정의 이름입니다.|
|trackingStoreConnectionStringName||기록 및 인스턴스 테이블에 사용할 연결 문자열의 이름입니다. 지정 하지 않으면 `azureStorageConnectionStringName` 연결이 사용 됩니다.|
|trackingStoreNamePrefix||`trackingStoreConnectionStringName` 지정 된 경우 기록 및 인스턴스 테이블에 사용할 접두사입니다. 설정 하지 않으면 기본 접두사 값이 `DurableTask`됩니다. `trackingStoreConnectionStringName` 지정 하지 않으면 기록 및 인스턴스 테이블은 `hubName` 값을 접두사로 사용 하 고 `trackingStoreNamePrefix`에 대한 모든 설정은 무시 됩니다.|
|traceInputsAndOutputs |false|함수 호출의 입출력을 추적할지 여부를 나타내는 값입니다. 함수 실행 이벤트를 추적할 때의 기본 동작은 함수 호출에 대한 직렬화된 입출력에 바이트 수를 포함하는 것입니다. 이 동작은 로그를 블 로트 하거나 실수로 중요 한 정보를 노출 하지 않고 입력 및 출력에 대한 최소 정보를 제공 합니다. 이 속성을 true로 설정하면 기본 함수 로깅이 함수 입출력의 전체 내용을 기록하게 됩니다.|
|logReplayEvents|false|Application Insights에 이벤트를 재생하는 오케스트레이션을 작성할 것인지 여부를 나타내는 값입니다.|
|eventGridTopicEndpoint ||Azure Event Grid 사용자 지정 항목 엔드포인트의 URL입니다. 이 속성을 설정 하면 오케스트레이션 수명 주기 알림 이벤트가이 엔드포인트에 게시 됩니다. 이 속성은 앱 설정 해결을 지원합니다.|
|eventGridKeySettingName ||`EventGridTopicEndpoint`에서 Azure Event Grid 사용자 지정 항목으로 인증하는 데 사용되는 키를 포함하는 앱 설정의 이름입니다.|
|eventGridPublishRetryCount|0|Event Grid 항목에 게시가 실패하는 경우 다시 시도 횟수입니다.|
|eventGridPublishRetryInterval|5분|*hh:mm:ss* 형식의 Event Grid 게시 재시도 간격입니다.|
|eventGridPublishEventTypes||Event Grid에 게시할 이벤트 유형 목록입니다. 지정 하지 않으면 모든 이벤트 유형이 게시 됩니다. 허용 되는 값은 `Started`, `Completed`, `Failed`, `Terminated`입니다.|
|useGracefulShutdown|false|모드 호스트를 종료 하 여 in-process 함수 실행이 실패할 가능성을 줄이려면 정상적으로 종료를 사용 하도록 설정 합니다.|

이러한 설정 중 상당수는 성능을 최적화 하기 위한 것입니다. 자세한 내용은 [성능 및 크기 조정](../articles/azure-functions/durable-functions-perf-and-scale.md)을 참조하세요.
