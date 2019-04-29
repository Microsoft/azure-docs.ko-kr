---
title: Azure Batch 풀 크기 조정 완료 이벤트 | Microsoft Docs
description: Batch 풀 크기 조정 완료 이벤트에 대한 참조입니다.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 87c98b89a49adbad88841dccbd4ba47d370b2be7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776425"
---
# <a name="pool-resize-complete-event"></a>풀 크기 조정 완료 이벤트

 이 이벤트는 풀 크기 조정이 완료되거나 실패하면 내보내집니다.

 다음 예에서는 크기가 늘어나고 성공적으로 완료된 풀에 대한 풀 크기 조정 완료 이벤트의 본문을 보여 줍니다.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|요소|형식|메모|
|-------------|----------|-----------|
|id|String|풀의 ID입니다.|
|nodeDeallocationOption|String|풀 크기가 감소하는 경우 풀에서 노드를 제거할 수 있는 시기를 지정합니다.<br /><br /> 가능한 값은 다음과 같습니다.<br /><br /> **requeue** – 실행 중인 태스크를 종료하고 해당 태스크를 다시 대기열에 추가합니다. 작업이 활성화되면 태스크가 다시 실행됩니다. 태스크가 종료되는 즉시 노드를 제거합니다.<br /><br /> **terminate** – 실행 중인 태스크를 종료합니다. 태스크가 다시 실행되지 않습니다. 태스크가 종료되는 즉시 노드를 제거합니다.<br /><br /> **taskcompletion** – 현재 실행 중인 태스크가 완료될 때까지 기다립니다. 대기하는 동안 새 태스크를 예약하지 않습니다. 모든 태스크가 완료되면 노드를 제거합니다.<br /><br /> **Retaineddata** -현재 실행 중인 태스크가 완료될 때까지 기다린 후 모든 태스크 데이터 보존 기간이 만료될 때까지 기다립니다. 대기하는 동안 새 태스크를 예약하지 않습니다. 모든 태스크 보존 기간이 만료되면 노드를 제거합니다.<br /><br /> 기본값은 requeue입니다.<br /><br /> 풀 크기가 증가하는 경우 값이 **invalid**로 설정됩니다.|
|currentDedicated|Int32|현재 풀에 할당된 계산 노드 수입니다.|
|targetDedicated|Int32|풀에 대해 요청된 계산 노드 수입니다.|
|enableAutoScale|Bool|풀 크기가 시간이 지남에 따라 자동으로 조정되는지 여부를 지정합니다.|
|isAutoPool|Bool|풀이 작업의 자동 풀 메커니즘을 통해 만들어졌는지 여부를 지정합니다.|
|startTime|DateTime|풀 크기 조정이 시작된 시간입니다.|
|endTime|DateTime|풀 크기 조정이 완료된 시간입니다.|
|resultCode|String|크기 조정의 결과입니다.|
|resultMessage|String|크기 조정 오류에 결과의 세부 정보가 포함됩니다.<br /><br /> 크기 조정이 성공적으로 완료되면 작업에 성공했다고 표시됩니다.|