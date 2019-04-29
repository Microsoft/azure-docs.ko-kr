---
title: Azure Batch 태스크 실패 이벤트 | Microsoft Docs
description: Batch 태스크 실패 이벤트에 대한 참조입니다.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/15/2018
ms.author: v-junlch
ms.openlocfilehash: f37769ceb761b8c8bc4834568813bb1b7af7f66a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549991"
---
# <a name="task-fail-event"></a>태스크 실패 이벤트

 이 이벤트는 태스크가 오류와 함께 완료되면 내보내집니다. 현재 0이 아닌 종료 코드는 모두 오류로 간주됩니다. 이 이벤트는 태스크 완료 이벤트와 *별도로* 내보내지고, 태스크가 실패한 시기를 감지하는 데 사용될 수 있습니다.


 다음 예에서는 태스크 실패 이벤트의 본문을 보여 줍니다.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|요소 이름|Type|메모|
|------------------|----------|-----------|
|jobId|String|태스크가 포함된 작업의 ID입니다.|
|id|String|태스크의 ID입니다.|
|taskType|String|태스크의 유형입니다. 이는 작업 관리자 태스크를 나타내는 'JobManager' 또는 작업 관리자 태스크가 아님을 나타내는 'User'가 될 수 있습니다. 작업 준비 태스크, 작업 릴리스 태스크 또는 시작 태스크의 경우 이 이벤트가 내보내지지 않습니다.|
|systemTaskVersion|Int32|태스크에 대한 내부 재시도 카운터입니다. 내부적으로 Batch 서비스는 일시적인 문제를 해결하기 위해 태스크를 다시 시도할 수 있습니다. 이러한 문제에는 내부 일정 오류 또는 불량 상태의 계산 노드 복구를 위한 시도가 포함될 수 있습니다.|
|[nodeInfo](#nodeInfo)|복합 형식|태스크가 실행된 계산 노드에 대한 정보를 포함합니다.|
|[multiInstanceSettings](#multiInstanceSettings)|복합 형식|여러 계산 노드가 필요한 다중 인스턴스 태스크임을 지정합니다.  자세한 내용은 [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task)를 참조하세요.|
|[constraints](#constraints)|복합 형식|이 태스크에 적용되는 실행 제약 조건입니다.|
|[executionInfo](#executionInfo)|복합 형식|태스크 실행에 대한 정보를 포함합니다.|

###  <a name="nodeInfo"></a> nodeInfo

|요소 이름|Type|메모|
|------------------|----------|-----------|
|poolId|String|태스크가 실행된 풀의 ID입니다.|
|nodeId|String|태스크가 실행된 노드의 ID입니다.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|요소 이름|Type|메모|
|------------------|----------|-----------|
|numberOfInstances|Int32|태스크에 필요한 계산 노드 수입니다.|

###  <a name="constraints"></a> constraints

|요소 이름|Type|메모|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|태스크를 다시 시도할 수 있는 최대 횟수입니다. 종료 코드가 0이 아니면 Batch 서비스가 태스크를 다시 시도합니다.<br /><br /> 이 값은 구체적으로 재시도 횟수를 제어합니다. Batch 서비스는 태스크를 한 번 시도한 후 이 한도까지 다시 시도할 수 있습니다. 예를 들어 최대 재시도 횟수가 3일 경우 Batch는 최대 4회까지 태스크를 시도합니다(초기 시도 1회와 재시도 3회).<br /><br /> 최대 재시도 횟수가 0일 경우 Batch 서비스는 태스크를 다시 시도하지 않습니다.<br /><br /> 최대 재시도 횟수가 -1일 경우 Batch 서비스는 태스크를 무제한으로 다시 시도합니다.<br /><br /> 기본값은 0(재시도 안 함)입니다.|


###  <a name="executionInfo"></a> executionInfo

|요소 이름|Type|메모|
|------------------|----------|-----------|
|startTime|DateTime|태스크가 실행되기 시작한 시간입니다. 'Running'은 **실행 중** 상태에 해당하므로 태스크가 리소스 파일 또는 애플리케이션 패키지를 지정할 경우 시작 시간에는 태스크가 이를 다운로드 또는 배포하기 시작한 시간이 반영됩니다.  태스크가 다시 시작되거나 다시 시도된 경우 이는 태스크가 실행을 시작한 가장 최근의 시간을 나타냅니다.|
|endTime|DateTime|태스크가 완료된 시간입니다.|
|exitCode|Int32|태스크의 종료 코드입니다.|
|retryCount|Int32|Batch 서비스에서 태스크를 다시 시도한 횟수입니다. 태스크가 0이 아닌 종료 코드와 함께 종료될 경우 지정된 MaxTaskRetryCount까지 다시 시도됩니다.|
|requeueCount|Int32|사용자 요청으로 인해 Batch 서비스에서 태스크를 대기열에 다시 추가한 횟수입니다.<br /><br /> 사용자가 풀 크기 조정 또는 축소를 통해 풀에서 노드를 제거하거나 작업이 비활성화될 경우 사용자는 노드에서 실행 중인 태스크를 실행 대기열에 다시 추가하도록 지정할 수 있습니다. 이 개수는 태스크가 이러한 이유로 대기열에 다시 추가된 횟수를 추적합니다.|

<!-- Update_Description: update metedata properties -->