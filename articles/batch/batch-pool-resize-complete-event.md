---
title: Azure Batch 풀 크기 조정 완료 이벤트
description: Batch 풀 크기 조정 완료 이벤트에 대한 참조입니다. 크기를 늘려 성공적으로 완료 된 풀의 예를 참조 하세요.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022225"
---
# <a name="pool-resize-complete-event"></a>풀 크기 조정 완료 이벤트

 이 이벤트는 풀 크기 조정이 완료되거나 실패하면 내보내집니다.

 다음 예에서는 크기가 늘어나고 성공적으로 완료된 풀에 대한 풀 크기 조정 완료 이벤트의 본문을 보여 줍니다.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|요소|유형|메모|
|-------------|----------|-----------|
|`id`|String|풀의 ID입니다.|
|`nodeDeallocationOption`|String|풀 크기가 감소하는 경우 풀에서 노드를 제거할 수 있는 시기를 지정합니다.<br /><br /> 가능한 값은 다음과 같습니다.<br /><br /> **requeue** – 실행 중인 태스크를 종료하고 해당 태스크를 다시 대기열에 추가합니다. 작업이 활성화되면 태스크가 다시 실행됩니다. 태스크가 종료되는 즉시 노드를 제거합니다.<br /><br /> **terminate** – 실행 중인 태스크를 종료합니다. 태스크가 다시 실행되지 않습니다. 태스크가 종료되는 즉시 노드를 제거합니다.<br /><br /> **taskcompletion** – 현재 실행 중인 태스크가 완료될 때까지 기다립니다. 대기하는 동안 새 태스크를 예약하지 않습니다. 모든 태스크가 완료되면 노드를 제거합니다.<br /><br /> **Retaineddata** -현재 실행 중인 태스크가 완료될 때까지 기다린 후 모든 태스크 데이터 보존 기간이 만료될 때까지 기다립니다. 대기하는 동안 새 태스크를 예약하지 않습니다. 모든 태스크 보존 기간이 만료되면 노드를 제거합니다.<br /><br /> 기본값은 requeue입니다.<br /><br /> 풀 크기가 증가하는 경우 값이 **invalid**로 설정됩니다.|
|`currentDedicatedNodes`|Int32|현재 풀에 할당 된 전용 계산 노드 수입니다.|
|`targetDedicatedNodes`|Int32|풀에 대해 요청 되는 전용 계산 노드 수입니다.|
|`currentLowPriorityNodes`|Int32|현재 풀에 할당 된 우선 순위가 낮은 계산 노드 수입니다.|
|`targetLowPriorityNodes`|Int32|풀에 대해 요청 된 우선 순위가 낮은 계산 노드 수입니다.|
|`enableAutoScale`|Bool|풀 크기가 시간이 지남에 따라 자동으로 조정되는지 여부를 지정합니다.|
|`isAutoPool`|Bool|풀이 작업의 자동 풀 메커니즘을 통해 만들어졌는지 여부를 지정합니다.|
|`startTime`|DateTime|풀 크기 조정이 시작된 시간입니다.|
|`endTime`|DateTime|풀 크기 조정이 완료된 시간입니다.|
|`resultCode`|String|크기 조정의 결과입니다.|
|`resultMessage`|String| 결과에 대한 자세한 메시지입니다.<br /><br /> 크기 조정이 성공적으로 완료되면 작업에 성공했다고 표시됩니다.|
