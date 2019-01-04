---
title: Azure Data Factory에서 파이프라인 실행 및 트리거 | Microsoft Docs
description: 이 문서에서는 요청 시 또는 트리거를 만들어 Azure Data Factory에서 파이프라인을 실행하는 방법에 대한 정보를 제공합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: shlo
ms.openlocfilehash: 58fffafe9658919a96d1aef2881424c0d324e688
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876480"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Azure Data Factory에서 파이프라인 실행 및 트리거
> [!div class="op_single_selector" title1="사용중인 Data Factory 서비스의 버전을 선택하십시오:"]
> * [버전 1](v1/data-factory-scheduling-and-execution.md)
> * [현재 버전](concepts-pipeline-execution-triggers.md)

Azure Data Factory의 _파이프라인 실행_은 파이프라인 실행의 인스턴스를 정의합니다. 예를 들어 오전 8시, 오전 9시 및 오전 10시에 실행하는 파이프라인이 있다고 합시다. 이 경우 파이프라인 또는 파이프라인 실행이라는 별도의 세 가지 실행이 있습니다. 각 파이프라인 실행에는 고유한 파이프라인 실행 ID가 있습니다. 실행 ID는 특정 파이프라인 실행을 고유하게 정의하는 GUID입니다. 

파이프라인 실행은 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 인스턴스화됩니다. 수동으로 또는 _트리거_를 사용하여 파이프라인을 실행할 수 있습니다. 이 문서는 파이프라인을 실행하는 두 가지 방법 모두에 대한 자세한 정보를 제공합니다.

## <a name="manual-execution-on-demand"></a>수동 실행(요청 시)
파이프라인의 수동 실행도 _주문형_ 실행이라고 합니다.

예를 들어 실행하려는 **copyPipeline**이라는 기본 파이프라인이 있다고 가정해 보겠습니다. 이 파이프라인에는 Azure Blob 저장소 원본 폴더에서 동일한 저장소의 대상 폴더에 복사하는 단일 활동이 있습니다. 다음 JSON 정의는 이 샘플 파이프라인을 보여 줍니다.

```json
{
    "name": "copyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "name": "CopyBlobtoBlob",
                "inputs": [
                    {
                        "referenceName": "sourceBlobDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "sinkBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "parameters": {
            "sourceBlobContainer": {
                "type": "String"
            },
            "sinkBlobContainer": {
                "type": "String"
            }
        }
    }
}
```

JSON 정의에서 파이프라인은 **sourceBlobContainer** 및 **sinkBlobContainer** 두 매개 변수를 취합니다. 런타임 시 이 매개 변수에 값을 전달합니다.

다음 방법 중 하나를 사용하여 파이프라인을 수동으로 실행할 수 있습니다.
- .NET SDK
- Azure PowerShell 모듈
- REST API
- Python SDK

### <a name="rest-api"></a>REST API
다음 샘플 명령은 REST API를 사용하여 파이프라인을 수동으로 실행하는 방법을 보여 줍니다.  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

전체 샘플은 [빠른 시작: REST API를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md)를 참조하세요.

### <a name="azure-powershell"></a>Azure PowerShell
다음 샘플 명령은 Azure PowerShell을 사용하여 파이프라인을 수동으로 실행하는 방법을 보여 줍니다.

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

매개 변수는 요청 페이로드의 본문에서 전달합니다. .NET SDK, Azure PowerShell 및 Python SDK에서는 호출에 인수로 전달되는 사전에서 값을 전달합니다.

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

응답 페이로드는 파이프라인 실행의 고유 ID입니다.

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

전체 샘플은 [빠른 시작: Azure PowerShell을 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)를 참조하세요.

### <a name="net-sdk"></a>.NET SDK
다음 샘플 호출은 .NET SDK를 사용하여 파이프라인을 수동으로 실행하는 방법을 보여 줍니다.

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

전체 샘플은 [빠른 시작: .NET SDK를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)를 참조하세요.

> [!NOTE]
> .NET SDK를 사용하여 Azure Functions, 사용자 고유의 웹 서비스 등에서 Data Factory 파이프라인을 호출할 수 있습니다.

<h2 id="triggers">트리거 실행</h2>
트리거는 파이프라인 실행을 실행할 수 있는 또 다른 방법입니다. 트리거는 파이프라인 실행을 시작해야 하는 시기를 결정하는 처리 단위를 나타냅니다. 현재 Data Factory는 세 가지 형식의 트리거를 지원합니다.

- 일정 트리거: 벽시계 일정에 따라 파이프라인을 호출하는 트리거입니다.

- 연속 창 트리거: 상태를 유지하면서 일정한 간격에 작동하는 트리거입니다.

- 이벤트 기반 트리거: 이벤트에 응답하는 트리거입니다.

파이프라인 및 트리거는 다 대 다 관계를 가지고 있습니다. 다중 트리거는 단일 파이프라인을 시작할 수 있고, 단일 트리거는 여러 파이프라인을 시작할 수 있습니다. 다음 트리거 정의에서 **pipelines** 속성은 특정 트리거가 트리거한 파이프라인의 목록을 가리킵니다. 속성 정의는 파이프라인 매개 변수에 대한 값을 포함합니다.

### <a name="basic-trigger-definition"></a>기본 트리거 정의

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>일정 트리거
일정 트리거는 벽시계 일정에 따라 파이프라인을 실행합니다. 이 트리거는 정기적인 고급 일정 옵션을 지원합니다. 예를 들어 트리거는 "매주" 또는 "월요일 오후 5시 및 목요일 오후 9시"와 같은 간격을 지원합니다. 데이터 세트 패턴은 중립적이며 트리거는 시계열 및 비시계열 데이터 간을 구분하지 않으므로 일정 트리거가 유연합니다.

일정 트리거에 대한 자세한 내용 및 예제는 [일정 트리거 만들기](how-to-create-schedule-trigger.md)를 참조하세요.

## <a name="schedule-trigger-definition"></a>일정 트리거 정의
일정 트리거를 만들 때 JSON 정의를 사용하여 일정 및 되풀이를 지정합니다. 

일정 트리거가 파이프라인 실행을 시작하게 하려면 특정 파이프라인의 파이프라인 참조를 트리거 정의에 포함합니다. 파이프라인 및 트리거는 다 대 다 관계를 가지고 있습니다. 다중 트리거는 단일 파이프라인을 시작할 수 있습니다. 단일 트리거는 여러 파이프라인을 시작할 수 있습니다.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
            {
              "day": <<Monday-Sunday>>,
              "occurrence": <<1-5>>
            }
          ]
        }
      }
    },
  "pipelines": [
    {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "<Name of your pipeline>"
      },
      "parameters": {
        "<parameter 1 Name>": {
          "type": "Expression",
          "value": "<parameter 1 Value>"
        },
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> **매개 변수** 속성은 **pipelines** 요소의 필수 속성입니다. 파이프라인에서 매개 변수를 사용하지 않는 경우에도 **parameters** 속성에 대해 빈 JSON 정의를 포함해야 합니다.

### <a name="schema-overview"></a>스키마 개요
다음 표에서는 되풀이 및 트리거 예약과 관련된 주요 스키마 요소에 대한 개괄적인 개요를 제공합니다.

| JSON 속성 | 설명 |
|:--- |:--- |
| **startTime** | 날짜-시간 값입니다. 기본 일정의 경우 **startTime** 속성의 값이 첫 번째 되풀이 항목에 적용됩니다. 복잡한 일정의 경우 트리거는 특정 **startTime** 값 이후에 시작합니다. |
| **endTime** | 트리거의 종료 날짜 및 시간입니다. 트리거는 지정된 종료 날짜 및 시간 이후에 실행되지 않습니다. 속성에 대한 값은 이전에 있을 수 없습니다. <!-- This property is optional. --> |
| **timeZone** | 표준 시간대입니다. 현재는 UTC 표준 시간대만 지원됩니다. |
| **recurrence** | 트리거에 대한 되풀이 규칙을 지정하는 recurrence 개체입니다. recurrence 개체는 **frequency**, **interval**, **endTime**, **count** 및 **schedule** 요소를 지원합니다. recurrence 개체가 정의된 경우 **frequency** 요소는 필수입니다. 되풀이 개체의 다른 요소는 선택적입니다. |
| **frequency** | 트리거가 되풀이되는 빈도 단위입니다. 지원되는 값은 "minute", "hour", "day", "week" 및 "month"입니다. |
| **interval** | **frequency** 값에 대한 간격을 나타내는 양의 정수입니다. **frequency** 값은 트리거가 실행되는 빈도를 결정합니다. 예를 들어 **interval**이 3이고 **frequency**가 "week"인 경우 트리거가 매 3주마다 다시 발생합니다. |
| **schedule** | 트리거에 대한 되풀이 일정입니다. 지정된 **frequency** 값을 가진 트리거는 되풀이 일정을 기반으로 되풀이를 변경합니다. **schedule** 속성에는 분, 시간, 요일, 날짜, 주차를 기반으로 하는 되풀이에 대한 수정 내용이 포함됩니다.

### <a name="schedule-trigger-example"></a>일정 트리거 예제

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>스키마 기본값, 제한 및 예제

| JSON 속성 | 형식 | 필수 | 기본값 | 유효한 값 | 예 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | string | 예 | 없음 | ISO 8601 날짜-시간 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | object | 예 | 없음 | 되풀이 개체 | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | number | 아니오 | 1 | 1~1000 | `"interval":10` |
| **endTime** | string | 예 | 없음 | 미래의 시간을 나타내는 날짜-시간 값 | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | object | 아니요 | 없음 | 일정 개체 | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime 속성
다음 표는 **startTime** 속성이 트리거 실행을 제어하는 방법을 보여줍니다.

| startTime 값 | 일정 없이 되풀이 | 일정대로 되풀이 |
|:--- |:--- |:--- |
| **시작 시간이 과거** | 시작 시간 이후 미래의 첫 번째 실행 시간을 계산하고 해당 시간에 실행합니다.<br /><br />이어지는 후속 실행은 마지막 실행 시간에서 계산하여 실행합니다.<br /><br />이 테이블 다음에 나오는 예제를 참조하세요. | 트리거는 지정된 시작 시간 _이후에_ 시작합니다. 첫 번째 되풀이 항목은 시작 시간부터 계산되는 일정에 따라 실행됩니다.<br /><br />되풀이 일정에 따라 후속 실행을 수행합니다. |
| **시작 시간이 미래 또는 현재** | 지정된 시작 시간에 한 번 실행됩니다.<br /><br />이어지는 후속 실행은 마지막 실행 시간에서 계산하여 실행합니다. | 트리거는 지정된 시작 시간 _이후에_ 시작합니다. 시작 시간에서 계산된 일정에 따라 첫 번째 되풀이 항목을 실행합니다.<br /><br />되풀이 일정에 따라 후속 실행을 수행합니다. |

예를 들어 시작 시간이 과거이고 되풀이가 있지만 일정이 없는 경우 발생되는 상황을 살펴보겠습니다. 현재 시간이 2017-04-08 오후 1시이고, 시작 시간이 2017-04-07 오후 2시이고, 되풀이는 2일마다라고 가정합니다. (**recurrence** 값은 **frequency** 속성을 "day"로 **interval** 속성을 2로 설정하여 정의됩니다.) **startTime** 값이 현재 시간보다 이전임에 유의하십시오.

이러한 조건 하에서 첫 번째 실행은 2017-04-09 오후 2시입니다. Scheduler 엔진이 시작 시간에서 되풀이 실행 시간을 계산합니다. 현재보다 이전의 모든 인스턴스는 무시됩니다. 엔진은 이후에 발생하는 다음 인스턴스를 사용합니다. 이 시나리오에서 시작 시간은 2017-04-07 오후 2시입니다. 다음 인스턴스는 이 시간에서 2일 후인 2017-04-09 오후 2시입니다.

**startTime**이 2017-04-05 오후 2시 또는 2017-04-01 오후 2시인지 여부에 관계없이 첫 번째 실행 시간은 동일합니다. 첫 번째 실행 후 후속 실행은 일정을 사용하여 계산됩니다. 따라서 후속 실행은 2017-04-11 오후 2시, 2017-04-13 오후 2시, 2017-04-15 오후 2시 등에 수행됩니다.

마지막으로, 트리거에 대한 일정에 시간 또는 분이 설정되지 않은 경우 첫 번째 실행의 시간 또는 분이 기본값으로 사용됩니다.

### <a name="schedule-property"></a>일정 속성
**schedule**을 사용하여 트리거 실행의 횟수를 *제한*할 수 있습니다. 예를 들어 월 단위 빈도의 트리거가 31일에만 실행되도록 예약된 경우, 트리거는 31일이 있는 달에만 실행됩니다.

**schedule**을 사용하여 트리거 실행의 횟수를 *증가*시킬 수도 있습니다. 예를 들어 월 단위 빈도의 트리거가 1일 및 2일에 실행되도록 예약된 경우, 트리거는 한 달에 한 번이 아니라 매월 첫 번째 및 두 번째 일에 실행됩니다.

여러 **schedule** 요소가 지정된 경우, 가장 큰 것부터 가장 작은 것 순으로, 즉 주차, 월 일, 요일, 시간, 분의 순서로 계산됩니다.

다음 테이블에서는 **일정** 요소의 세부 정보를 설명합니다.

| JSON 요소 | 설명 | 유효한 값 |
|:--- |:--- |:--- |
| **minutes** | 트리거가 실행될 시간(분)입니다. |- 정수<br />- 정수 배열|
| **hours** | 트리거가 실행될 일(시간)입니다. |- 정수<br />- 정수 배열|
| **weekDays** | 트리거가 실행될 요일입니다. 값은 주 단위 빈도로만 지정할 수 있습니다.|<br />- Monday<br />- Tuesday<br />- Wednesday<br />- Thursday<br />- Friday<br />- Saturday<br />- Sunday<br />- 날짜 값의 배열(최대 배열 크기: 7)<br /><br />날짜 값은 대/소문자 구분 안 함|
| **monthlyOccurrences** | 트리거가 실행되는 날짜입니다. 값은 빈도가 월인 경우에만 지정될 수 있습니다. |- **monthlyOccurrence** 개체의 배열: `{ "day": day,  "occurrence": occurrence }`<br />- **day** 특성은 트리거가 실행되는 요일입니다. 예를 들어 `{Sunday}`의 **day** 값을 가진 **monthlyOccurrences** 속성은 해당 월의 매주 일요일을 의미합니다. **day** 특성은 필수입니다.<br />- **occurrence** 특성은 월 중 지정된 **day**의 되풀이 항목입니다. 예를 들어 `{Sunday, -1}`의 **day** 및 **occurrence** 값을 가진 **monthlyOccurrences** 속성은 해당 월의 마지막 일요일을 의미합니다. **occurrence** 특성은 선택 사항입니다.|
| **monthDays** | 트리거가 실행되는 날짜입니다. 값은 빈도가 월인 경우에만 지정될 수 있습니다. |- 1 이상 및 31 이하의 모든 값<br />- 1 이하 및 31 이상의 모든 값<br />- 값의 배열|

## <a name="tumbling-window-trigger"></a>연속 창 트리거
연속 창 트리거는 상태를 유지하면서 지정된 시작 시간부터 주기적 시간 간격으로 실행되는 트리거 유형입니다. 연속 창은 고정된 크기의 겹치지 않고 연속적인 일련의 시간 간격입니다.

연속 창 트리거에 대한 자세한 내용 및 예제는 [연속 창 트리거 만들기](how-to-create-tumbling-window-trigger.md)를 참조하세요.

## <a name="event-based-trigger"></a>이벤트 기반 트리거

이벤트 기반 트리거는 파일 도착, 파일 삭제 등의 이벤트에 응답하여 Azure Blob Storage에서 실행 파이프라인을 트리거합니다.

이벤트 기반 트리거에 대한 자세한 내용은 [이벤트에 대한 응답으로 파이프라인을 실행하는 트리거 만들기](how-to-create-event-trigger.md)를 참조하세요.

## <a name="examples-of-trigger-recurrence-schedules"></a>트리거 되풀이 일정의 예
이 섹션에서는 되풀이 일정의 예제를 제공합니다. **schedule** 개체 및 해당 요소를 중점적으로 다루고 있 습니다.

예제에서 **interval** 값은 1이고 **frequency** 값은 일정 정의에 따라 올바르다고 가정합니다. 예를 들어 **frequency** 값으로 "day"를 사용할 수 없으며, **schedule** 개체의 **monthDays**를 수정할 수도 있습니다. 이러한 종류의 제한 사항은 앞 섹션의 표에서 설명하고 있습니다.

| 예 | 설명 |
|:--- |:--- |
| `{"hours":[5]}` | 매일 오전 5시에 실행됩니다. |
| `{"minutes":[15], "hours":[5]}` | 매일 오전 5시 15분에 실행됩니다. |
| `{"minutes":[15], "hours":[5,17]}` | 매일 오전 5시 15분 및 오후 5시 15분에 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17]}` | 매일 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행됩니다. |
| `{"minutes":[0,15,30,45]}` | 15분마다 실행됩니다. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 매시간 실행됩니다.<br /><br />이 트리거는 매 1시간마다 실행합니다. 값이 지정된 경우 분은 **startTime** 값으로 제어됩니다. 값이 지정되지 않은 경우 분은 생성 시간을 기준으로 제어됩니다. 예를 들어 시작 시간 또는 생성 시간(둘 중 해당하는 것)이 오후 12:25이라면 트리거는 00:25, 01:25, 02:25, …, 23:25에 실행됩니다.<br /><br />이 일정은 트리거의 **frequency** 값이 "hour"이고, **interval** 값이 1이고, **schedule**이 없는 트리거가 있는 것과 동일합니다. 다른 **frequency** 및 **interval** 값으로 이 일정을 사용하여 다른 트리거를 만들 수 있습니다. 예를 들어 **frequency** 값이 "month"인 경우, 일정은 **frequency** 값이 "day"인 경우의 매일이 아닌 한 달에 한 번만 실행됩니다. |
| `{"minutes":[0]}` | 매시간 정각에 실행됩니다.<br /><br />이 트리거는 오전 12시, 오전 1시, 오전 2시 등에 시작하는 시간에 매시간 실행됩니다.<br /><br />이 일정은 **frequency** 값이 "hour"이고, **startTime** 값이 0분이고, **schedule**이 없지만 **frequency** 값이 "day"인 트리거와 동일합니다. **frequency** 값이 "week" 또는 "month"인 경우, 일정은 각각 주에 하루 또는 월에 하루만 실행됩니다. |
| `{"minutes":[15]}` | 매시간 15분에 실행됩니다.<br /><br />이 트리거는 오전 0시 15분, 오전 1시 15분, 오전 2시 15분 등에 시작하여 오후 11시 15분에 끝나는 시간 이후에 매시간 15분에 실행됩니다. |
| `{"hours":[17], "weekDays":["saturday"]}` | 매주 토요일 오후 5시에 실행됩니다. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 매주 월요일, 수요일, 금요일 오후 5시에 실행됩니다. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 매주 월요일, 수요일, 금요일 오후 5시 15분 및 오후 5시 45분에 실행됩니다. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 월요일부터 금요일까지 15분마다 실행됩니다. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 월요일부터 금요일까지 오전 9시에서 오후 4시 45분 사이에 15분마다 실행됩니다. |
| `{"weekDays":["tuesday", "thursday"]}` | 화요일과 목요일의 지정된 시작 시간에 실행됩니다. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | 매월 28일 오전 6시에 실행됩니다(**frequency** 값이 "month"라고 가정). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 월의 마지막 날짜 오전 6시에 실행됩니다.<br /><br />월의 마지막 날짜에 트리거를 실행하려면 28, 29, 30 또는 31 대신 -1을 사용합니다. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 매월 1일과 말일의 오전 6시에 실행됩니다. |
| `{monthDays":[1,14]}` | 매월 1일 및 14일에 지정된 시작 시간에 실행됩니다. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 매월 첫 번째 금요일 오전 5시에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 매월 첫 번째 금요일의 지정된 시작 시간에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 매월 마지막 달의 세 번째 금요일의 지정된 시작 시간에 실행됩니다. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 매월 첫 번째와 마지막 금요일 오전 5시 15분에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 매월 첫 번째와 마지막 금요일의 지정된 시작 시간에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 매월 다섯 번째 금요일의 지정된 시작 시간에 실행됩니다.<br /><br />월에 5번째 금요일이 없으면 파이프라인이 실행되지 않습니다. 해당 월의 마지막 금요일에 트리거를 실행되도록 하려면 **occurrence** 값으로 5대신 -1을 사용하면 됩니다. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 매월 마지막 금요일에 15분마다 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 매월 세 번째 수요일에 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행됩니다. |

## <a name="trigger-type-comparison"></a>트리거 형식 비교
연속 창 트리거와 일정 트리거는 둘 다 시간 하트비트에서 작동합니다. 어떻게 다를까요?

다음 표는 연속 창 트리거와 일정 트리거의 비교를 제공합니다.

|  | 연속 창 트리거 | 일정 트리거 |
|:--- |:--- |:--- |
| **백필 시나리오** | 지원됩니다. 이전의 창에 대해 파이프라인 실행을 예약할 수 있습니다. | 지원되지 않습니다. 파이프라인 실행은 현재 시간 및 미래 시간의 기간에서만 실행될 수 있습니다. |
| **신뢰성** | 100% 신뢰성 파이프라인 실행은 간격 없이 지정된 시작 날짜에서 모든 창에 대해 예약될 수 있습니다. | 낮은 신뢰성 |
| **다시 시도 기능** | 지원됩니다. 실패한 파이프라인 실행에는 0의 기본 다시 시도 정책 또는 트리거 정의에서 사용자가 지정한 정책이 있습니다. 동시성/서버/제한 한도(즉, 상태 코드 400: 사용자 오류, 429: 요청이 너무 많음, 500: 내부 서버 오류)로 인해 파이프라인 실행이 실패하는 경우 자동으로 다시 시도합니다. | 지원되지 않습니다. |
| **동시성** | 지원됩니다. 사용자는 트리거에 대한 동시성 제한을 명시적으로 설정할 수 있습니다. 동시에 트리거되는 1~50개의 파이프라인 실행을 허용합니다. | 지원되지 않습니다. |
| **시스템 변수** | **WindowStart** 및 **WindowEnd** 시스템 변수의 사용을 지원합니다. 사용자는 트리거 정의에서 트리거 시스템 변수로 `triggerOutputs().windowStartTime` 및 `triggerOutputs().windowEndTime`에 액세스할 수 있습니다. 값은 각각 창 시작 시간 및 창 종료 시간으로 사용됩니다. 예를 들어 매 시간 실행되는 연속 창 트리거의 경우 창 오전 1시~오전 2시에 대해 정의는 `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` 및 `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`입니다. | 지원되지 않습니다. |
| **파이프라인-트리거 관계** | 일대일 관계를 지원합니다. 하나의 파이프라인만 트리거될 수 있습니다. | 다대다 관계를 지원합니다. 다중 트리거는 단일 파이프라인을 시작할 수 있습니다. 단일 트리거는 여러 파이프라인을 시작할 수 있습니다. | 

## <a name="next-steps"></a>다음 단계
다음 자습서를 참조하세요.

- [빠른 시작: .NET SDK를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)
- [일정 트리거 만들기](how-to-create-schedule-trigger.md)
- [연속 창 트리거 만들기](how-to-create-tumbling-window-trigger.md)
