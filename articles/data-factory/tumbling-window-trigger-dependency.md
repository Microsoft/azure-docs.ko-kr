---
title: 연속 창 트리거 종속성 만들기
description: Azure Data Factory에서 연속 창 트리거에 대한 종속성을 만드는 방법을 알아봅니다.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 3a4d31cb6986f8fc841a6afe20388e40e9f28c9b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926686"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>연속 창 트리거 종속성 만들기

이 항목에서는 연속 창 트리거에 대한 종속성을 만드는 단계를 제공합니다. 연속 창 트리거에 대한 자세한 내용은 [연속 창 트리거를 만드는 방법](how-to-create-tumbling-window-trigger.md)을 참조하세요.

종속성 체인을 빌드하고 데이터 팩터리에서 다른 트리거를 성공적으로 실행한 후에만 트리거가 실행되도록 하려면 이 고급 기능을 사용하여 연속 창 종속성을 만듭니다.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Data Factory UI에서 종속성 만들기

트리거에 대한 종속성을 만들려면 **트리거 > 고급 > 새로 만들기**를 선택한 다음, 적절한 오프셋 및 크기의 종속 대상 트리거를 선택합니다. **마침**을 선택하고 데이터 팩터리 변경 내용을 게시하여 종속성을 적용합니다.

![종속성 만들기](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "종속성 만들기")

## <a name="tumbling-window-dependency-properties"></a>연속 창 종속성 속성

종속성이 있는 연속 창 트리거의 속성은 다음과 같습니다.

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

다음 표에서는 연속 창 종속성을 정의하는 데 필요한 특성 목록을 제공합니다.

| **속성 이름** | **설명**  | **형식** | **필수** |
|---|---|---|---|
| type  | 이 드롭다운에는 기존의 연속 창 트리거가 모두 표시됩니다. 종속성을 적용할 트리거를 선택합니다.  | TumblingWindowTriggerDependencyReference 또는 SelfDependencyTumblingWindowTriggerReference | yes |
| offset | 종속성 트리거의 오프셋입니다. 시간 범위 형식에 값을 제공 하 고 음수 및 양수 오프셋을 모두 사용할 수 있습니다. 트리거가 자체에 의존 하는 경우이 속성은 필수 이며 다른 모든 경우에는 선택 사항입니다. 자체 종속성은 항상 음수 오프셋이어야 합니다. 값을 지정 하지 않으면 창은 트리거 자체와 동일 합니다. | Timespan<br/>(hh:mm:ss) | 자체 종속성: 예<br/>기타: 아니요 |
| size | 종속성 연속 창의 크기입니다. 양의 timespan 값을 제공 합니다. 이 속성은 선택 사항입니다. | Timespan<br/>(hh:mm:ss) | 아닙니다.  |

> [!NOTE]
> 연속 창 트리거는 최대 두 개의 다른 트리거에 따라 달라질 수 있습니다.

## <a name="tumbling-window-self-dependency-properties"></a>연속 창 자체 종속성 속성

이전 창이 성공적으로 완료 될 때까지 트리거가 다음 창으로 이동 하지 않아야 하는 시나리오에서는 자체 종속성을 작성 합니다. 이전 hr 내에서의 이전 실행의 성공 여부에 따라 달라 지는 자체 종속성 트리거는 다음과 같은 속성을 갖습니다.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>사용 시나리오 및 예제

다음은 연속 window 종속성 속성의 시나리오 및 사용에 대한 그림입니다.

### <a name="dependency-offset"></a>종속성 오프셋

![Offset 예](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Offset 예")

### <a name="dependency-size"></a>종속성 크기

![크기 예](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "크기 예")

### <a name="self-dependency"></a>자체 종속성

![자체 종속성](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "자체 종속성")

### <a name="dependency-on-another-tumbling-window-trigger"></a>다른 연속 창 트리거에 대한 종속성

지난 7 일간의 출력을 집계 하는 다른 일별 작업에 따라 매일 원격 분석 처리 작업을 수행 하면

![종속성 예제](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "종속성 예제")

### <a name="dependency-on-itself"></a>자체 종속성

작업의 출력 스트림에 간격이 없는 일일 작업입니다.

![자체 종속성 예제](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "자체 종속성 예제")

## <a name="monitor-dependencies"></a>종속성 모니터링

트리거 모니터링 실행 페이지에서 종속성 체인 및 해당 windows를 모니터링할 수 있습니다. **모니터링 > 트리거 실행**으로 이동합니다. 작업 열에서 트리거를 다시 실행 하거나 해당 종속성을 볼 수 있습니다.

![트리거 실행 모니터링](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "트리거 실행 모니터링")

' 트리거 종속성 보기 '를 클릭 하면 종속성의 상태를 볼 수 있습니다. 종속성 트리거 중 하나에 오류가 발생 하는 경우 종속 트리거를 실행 하려면 성공적으로 다시 실행 해야 합니다. 연속 창 트리거는 제한 시간이 초과 될 때까지 7 일 동안 종속성을 기다립니다.

![종속성 모니터링](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "종속성 모니터링")

보다 시각적으로 트리거 종속성 일정을 확인 하려면 Gantt 보기를 선택 합니다.

![종속성 모니터링](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "종속성 모니터링")

## <a name="next-steps"></a>다음 단계

* [연속 창 트리거를 만드는 방법](how-to-create-tumbling-window-trigger.md) 검토
