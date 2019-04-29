---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710535"
---
[지속형 함수](../articles/azure-functions/durable-functions-overview.md)에 대한 구성 설정입니다.

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
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정되지 않은 경우 함수 앱의 기본 작업 허브 이름은 **DurableFunctionsHub**입니다. 자세한 내용은 [작업 허브](../articles/azure-functions/durable-functions-task-hubs.md)를 참조하세요.

|자산  |Default | 설명 |
|---------|---------|---------|
|hubName|DurableFunctionsHub|여러 Durable Functions 애플리케이션이 동일한 스토리지 백 엔드를 사용하더라도 대체 [작업 허브](../articles/azure-functions/durable-functions-task-hubs.md) 이름을 사용하면 이러한 애플리케이션을 서로 구분할 수 있습니다.|
|controlQueueBatchSize|32|제어 큐에서 한 번에 끌어올 메시지의 수입니다.|
|partitionCount |4|제어 큐에 대한 파티션 수입니다. 1에서 16 사이의 양의 정수일 수 있습니다.|
|controlQueueVisibilityTimeout |5분|큐에서 제거된 제어 큐 메시지의 표시 여부 시간 제한입니다.|
|workItemQueueVisibilityTimeout |5분|큐에서 제거된 작업 항목 큐 메시지의 표시 여부 시간 제한입니다.|
|maxConcurrentActivityFunctions |현재 컴퓨터에 있는 프로세서 수의 10배입니다.|단일 호스트 인스턴스에서 동시에 처리할 수 있는 작업 함수는 최대 수입니다.|
|maxConcurrentOrchestratorFunctions |현재 컴퓨터에 있는 프로세서 수의 10배입니다.|단일 호스트 인스턴스에서 동시에 처리할 수 있는 오케스트레이터 함수의 최대 개수입니다.|
|maxQueuePollingInterval|30초|최대 제어 및 작업 항목 큐 폴링 간격을 *hh: mm:* 형식입니다. 값이 높을수록 더 높은 메시지 대기 시간을 처리 될 수 있습니다. 값이 낮은 저장소 비용 더 높음 증가 된 저장소 트랜잭션을 인해 발생할 수 있습니다.|
|azureStorageConnectionStringName |AzureWebJobsStorage|기본 Azure Storage 리소스를 관리하는 데 사용되는 Azure Storage 연결 문자열이 있는 앱 설정의 이름입니다.|
|trackingStoreConnectionStringName||기록 및 인스턴스 테이블에 사용할 연결 문자열의 이름입니다. 지정 하지 않으면는 `azureStorageConnectionStringName` 연결을 사용 합니다.|
|trackingStoreNamePrefix||경우에 기록 및 인스턴스에 대 한 사용할 접두사를 테이블 `trackingStoreConnectionStringName` 지정 됩니다. 설정 되지 않은 경우 기본 접두사 값은 됩니다 `DurableTask`합니다. 경우 `trackingStoreConnectionStringName` 지정 하지 않으면 인스턴스 및 기록 테이블을 사용 합니다 `hubName` 값에 대 한 모든 설정을 해당 접두사와 `trackingStoreNamePrefix` 무시 됩니다.|
|traceInputsAndOutputs |false|함수 호출의 입출력을 추적할지 여부를 나타내는 값입니다. 함수 실행 이벤트를 추적할 때의 기본 동작은 함수 호출에 대한 직렬화된 입출력에 바이트 수를 포함하는 것입니다. 이 동작은 입력 및 출력 모양을 없이 로그를 블 로트 하거나 실수로 중요 한 정보를 노출 하는 방법에 대 한 최소한의 정보를 제공 합니다. 이 속성을 true로 설정하면 기본 함수 로깅이 함수 입출력의 전체 내용을 기록하게 됩니다.|
|logReplayEvents|false|Application Insights에 이벤트를 재생하는 오케스트레이션을 작성할 것인지 여부를 나타내는 값입니다.|
|eventGridTopicEndpoint ||Azure Event Grid 사용자 지정 항목 엔드포인트의 URL입니다. 이 속성을 설정 하는 경우 오케스트레이션 수명 주기 알림 이벤트가이 끝점에 게시 됩니다. 이 속성은 앱 설정 해결을 지원합니다.|
|eventGridKeySettingName ||`EventGridTopicEndpoint`에서 Azure Event Grid 사용자 지정 항목으로 인증하는 데 사용되는 키를 포함하는 앱 설정의 이름입니다.|
|eventGridPublishRetryCount|0|Event Grid 항목에 게시가 실패하는 경우 다시 시도 횟수입니다.|
|eventGridPublishRetryInterval|5분|*hh:mm:ss* 형식의 Event Grid 게시 재시도 간격입니다.|
|eventGridPublishEventTypes||Event Grid에 게시 하는 이벤트 유형의 목록입니다. 지정 하지 않으면 모든 이벤트 유형은 게시 됩니다. 값 허용 `Started`, `Completed`를 `Failed`, `Terminated`합니다.|

이러한 설정 중 대부분은 성능을 최적화 하기 위한입니다. 자세한 내용은 [성능 및 크기 조정](../articles/azure-functions/durable-functions-perf-and-scale.md)을 참조하세요.