---
title: Data Factory에서 예약 및 실행 | Microsoft Docs
description: Azure Data Factory 애플리케이션 모델의 예약 및 실행에 대한 내용을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2d7fc45faf1fb77c7d9181e5a2419096dd1ad0f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258956"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory 예약 및 실행
> [!NOTE]
> 이 문서의 내용은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [파이프라인 실행 및 트리거](../concepts-pipeline-execution-triggers.md) 문서를 참조하세요.

이 문서에서는 Azure Data Factory 애플리케이션 모델의 예약 및 실행에 대한 내용을 설명합니다. 이 문서는 사용자가 작업, 파이프라인, 연결된 서비스 및 데이터 세트와 같은 Data Factory 애플리케이션 모델 개념을 이해하고 있다고 가정합니다. Azure Data Factory의 기본 개념은 다음 문서를 참조하세요.

* [데이터 팩터리 소개](data-factory-introduction.md)
* [파이프라인](data-factory-create-pipelines.md)
* [데이터 세트](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>파이프라인의 시작 및 종료 시간
파이프라인은 **시작** 시간과 **종료** 시간 사이에서만 활성화됩니다. 시작 시간 이전 또는 종료 시간 이후에 실행되지 않습니다. 파이프라인이 일시 중지되면 시작 및 종료 시간에 관계없이 실행되지 않습니다. 실행될 파이프라인의 경우 일시 중지되지 않아야 합니다. 파이프라인 정의에서 이러한 설정(start, end, paused)을 찾습니다. 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

이러한 속성에 대한 자세한 내용은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 


## <a name="specify-schedule-for-an-activity"></a>활동 일정 지정
실행되는 파이프라인이 아닙니다. 파이프라인의 전체 컨텍스트에서 실행되는 것은 파이프라인의 활동입니다. 활동 JSON의 **scheduler** 섹션을 사용하여 활동에 대한 되풀이 일정을 지정할 수 있습니다. 예를 들어 활동을 다음과 같이 시간별로 실행하도록 예약할 수 있습니다.  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

다음 다이어그램과 같이 활동 일정을 지정하면 파이프라인 시작 및 종료 시간에 일련의 연속 창이 생성됩니다. 연속 창은 일련의 고정된 크기의 겹치지 않는 연속적인 시간 간격입니다. 활동에 대한 이러한 논리적 연속 창을 **활동 기간**이라고 합니다.

![활동 스케줄러 예제](media/data-factory-scheduling-and-execution/scheduler-example.png)

활동에 대한 **scheduler** 속성은 선택 사항입니다. 이 속성을 지정하면 활동에 대한 출력 데이터 세트 정의에 지정하는 빈도와 일치해야 합니다. 현재 출력 데이터 세트는 일정을 작동하는 것입니다. 따라서 활동에서 출력을 생성하지 않더라도 출력 데이터 세트를 만들어야 합니다. 

## <a name="specify-schedule-for-a-dataset"></a>데이터 세트 일정 지정
Data Factory 파이프라인의 활동은 0개 이상의 입력 **데이터 세트**를 받고, 하나 이상의 출력 데이터 세트를 생성할 수 있습니다. 활동의 경우 데이터 세트 정의의 **availability** 섹션을 사용하여 입력 데이터를 사용할 수 있거나 출력 데이터를 생성하는 빈도를 지정할 수 있습니다. 

**availability** 섹션의 **frequency**는 시간 단위를 지정합니다. 빈도에 허용되는 값은 Minute, Hour, Day, Week 및 Month입니다. availability 섹션의 **interval** 속성은 frequency에 대한 승수를 지정합니다. 예를 들어 출력 데이터 세트에 대해 frequency가 Day로 설정되고 interval이 1로 설정되면 출력 데이터를 매일 생성합니다. frequency를 Minute로 지정하는 경우 interval을 15 이상으로 설정하는 것이 좋습니다. 

다음 예제에서 입력 데이터는 매시간 사용할 수 있으며 출력 데이터는 매시간(`"frequency": "Hour", "interval": 1`) 생성됩니다. 

**입력 데이터 세트:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**출력 데이터 세트**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

현재 **출력 데이터 세트는 일정을 작동하고 있습니다**. 즉 출력 데이터 세트에 대해 지정된 일정은 런타임에 활동을 실행하는 데 사용됩니다. 따라서 활동에서 출력을 생성하지 않더라도 출력 데이터 세트를 만들어야 합니다. 활동이 입력을 가져오지 않으면 입력 데이터 세트 만들기를 건너뛸 수 있습니다. 

다음 파이프라인 정의에서 **scheduler** 속성은 활동에 대한 일정을 지정하는 데 사용됩니다. 이 속성은 선택 사항입니다. 현재 활동에 대한 일정은 출력 데이터 세트에 지정된 일정과 일치해야 합니다.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

이 예제에서는 활동은 파이프라인의 시작과 끝 시간 사이에서 매시간 실행됩니다. 출력 데이터는 3시간 기간(오전 8-9시, 오전 9-10시, 오전 10-11시) 동안 매시간 생성됩니다. 

활동 실행에서 사용하거나 생성한 데이터의 각 단위를 **데이터 조각**이라고 합니다. 다음 다이어그램에서는 입력 데이터 세트와 출력 데이터 세트가 하나씩 포함된 활동의 예를 보여 줍니다. 

![가용성 스케줄러](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

위 다이어그램에서는 입력 및 출력 데이터 세트에 대한 매시간 데이터 조각을 보여 줍니다. 다이어그램은 처리할 준비가 된 입력 조각 3개를 보여 줍니다. 오전 10-11시 작업이 진행 중이며 오전 10-11시 출력 조각이 생성되고 있습니다. 

데이터 세트 JSON에서 현재 조각과 관련된 시간 간격에 액세스하려면 [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) 및 [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) 변수를 사용하면 됩니다. 마찬가지로 WindowStart 및 WindowEnd를 사용하여 활동 기간과 관련된 시간 간격에 액세스할 수 있습니다. 활동 일정은 해당 활동의 출력 데이터 세트 일정과 일치해야 합니다. 따라서 SliceStart 및 SliceEnd 값은 각각 WindowStart 및 WindowEnd 값과 동일합니다. 이러한 변수에 대한 자세한 내용은 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md#data-factory-system-variables) 문서를 참조하세요.  

이러한 변수를 작업 JSON에서 다양한 용도로 사용할 수 있습니다. 예를 들어 시계열 데이터(예: 오전 8시에서 오전 9시)를 나타내는 입력 및 출력 데이터 세트에서 데이터를 선택하는 데 사용할 수 있습니다. 또한 이 예제에서는 **WindowStart** 및 **WindowEnd**를 사용하여 활동 실행에 대한 관련 데이터를 선택하고 적절한 **folderPath**의 Blob에 이 데이터를 복사합니다. **folderPath** 는 매시간 별도의 폴더를 포함하도록 매개 변수화됩니다.  

앞의 예에서 입력 및 출력 데이터 세트에 대해 지정된 일정은 동일합니다(매시간). 활동에 대한 입력 데이터 세트를 다른 빈도(예: 15분마다)에서 사용할 수 있는 경우 출력 데이터 세트가 활동 일정을 작동하는 것이므로 이 출력 데이터 세트를 생성하는 활동은 한 시간에 한 번씩 계속 실행됩니다. 자세한 내용은 [다양한 빈도로 데이터 세트 모델링](#model-datasets-with-different-frequencies)을 참조하세요.

## <a name="dataset-availability-and-policies"></a>데이터 세트 가용성 및 정책
데이터 세트 정의의 availability 섹션에서 frequency 및 interval 속성의 사용을 살펴보았습니다. 활동의 예약 및 실행에 영향을 주는 몇 가지 다른 속성이 있습니다. 

### <a name="dataset-availability"></a>데이터 세트 가용성 
다음 표에서는 **availability** 섹션에서 사용할 수 있는 속성을 설명합니다.

| 자산 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| frequency |데이터 세트 조각 생성을 위한 시간 단위를 지정합니다.<br/><br/><b>지원되는 빈도</b>: Minute, Hour, Day, Week, Month |예 |해당 없음 |
| interval |빈도 승수를 지정합니다.<br/><br/>"빈도 x 간격"은 조각을 생성하는 빈도를 결정합니다.<br/><br/>데이터 세트를 시간 단위로 조각화해야 하는 경우 <b>frequency</b>를 <b>Hour</b>로, <b>interval</b>을 <b>1</b>로 설정합니다.<br/><br/><b>참고</b>: 빈도를 Minute(분)으로 지정하면 15 이상으로 간격을 설정하는 것이 좋습니다. |예 |해당 없음 |
| style |간격의 시작/끝에 조각을 생성해야 하는지를 지정합니다.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Frequency를 Month로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 월의 마지막 날에 생성합니다. style을 StartOfInterval로 설정하는 경우 조각을 달의 첫 번째 날에 생성합니다.<br/><br/>frequency를 Day로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 일의 마지막 시간에 생성합니다.<br/><br/>Frequency를 Hour로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 시간의 끝에 생성합니다. 예를 들어 오후 1~2시 기간에 대한 조각은 오후 2시에 생성됩니다. |아닙니다. |EndOfInterval |
| anchorDateTime |스케줄러에서 사용하는 시간에 절대 위치를 정의하여 데이터 세트 조각 경계를 계산합니다. <br/><br/><b>참고</b>: AnchorDateTime에 빈도보다 더 세분화된 날짜 부분이 있는 경우 더 세분화된 부분을 무시합니다. <br/><br/>예를 들어 <b>간격</b>이 <b>매시간</b>으로 설정됩니다(frequency: hour and interval: 1)이고 <b>AnchorDateTime</b>에서 <b>분 및 초</b>를 포함하는 경우 AnchorDateTime의 <b>분 및 초</b> 부분은 무시됩니다. |아닙니다. |01/01/0001 |
| offset |모든 데이터 세트 조각의 시작과 끝이 이동에 의한 Timespan입니다. <br/><br/><b>참고</b>: anchorDateTime 및 offset이 모두 지정되면 결과적으로 이동이 결합됩니다. |아닙니다. |해당 없음 |

### <a name="offset-example"></a>offset example
기본적으로 매일(`"frequency": "Day", "interval": 1`) 조각은 UTC 시간으로 오전 12시(자정)에서 시작합니다. 대신, 시작 시간을 UTC 시간으로 오전 6시로 설정하려면 다음 코드 조각과 같이 오프셋을 설정합니다. 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime 예제
다음 예제에서는 데이터 세트가 23시간마다 생성됩니다. 첫 번째 조각은 `2017-04-19T08:00:00`(UTC 시간)으로 설정된 anchorDateTime 지정 시간에 시작됩니다.

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>offset/style 예제
다음 데이터 세트는 월별 데이터 세트로, 매월 3일, 오전 8시(`3.08:00:00`)에 생성됩니다.

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>데이터 세트 정책
데이터 세트에는 조각 실행으로 생성된 데이터를 사용하기 전에 유효성 검사하는 방법을 지정하는 유효성 검사 정책이 정의되어 있을 수 있습니다. 이러한 경우 조각의 실행이 완료된 후 출력 조각 상태는 **Validation**의 하위 상태를 포함한 **Waiting**으로 변경됩니다. 조각의 유효성이 검사되었으면 조각 상태는 **Ready**로 변경됩니다. 데이터 조각이 생성되었으나 유효성 검사를 통과하지 못하면 이 조각에 따라 다운스트림 조각에 대한 작업 실행은 처리되지 않습니다. [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 에서는 Data Factory에 있는 데이터 조각의 다양한 상태에 대해 다룹니다.

데이터 세트 정의의 **정책** 섹션에서 데이터 세트 조각이 충족해야 하는 기준 또는 조건을 정의합니다. 다음 표에서는 **policy** 섹션에서 사용할 수 있는 속성을 설명합니다.

| 정책 이름 | 설명 | 에 적용 | 필수 | 기본값 |
| --- | --- | --- | --- | --- |
| minimumSizeMB | **Azure Blob** 에서 데이터가 최소 크기 요구 사항(메가바이트)을 충족하는지 확인합니다. |Azure Blob |아닙니다. |해당 없음 |
| minimumRows | **Azure SQL Database** 또는 **Azure 테이블**에서 데이터가 최소 행 수를 포함하는지 확인합니다. |<ul><li>Azure SQL Database</li><li>Azure 테이블</li></ul> |아닙니다. |해당 없음 |

#### <a name="examples"></a>예
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

이러한 속성과 예제에 대한 자세한 내용은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 

## <a name="activity-policies"></a>활동 정책
정책은 특히 테이블의 조각을 처리할 때 활동의 런타임 동작에 영향을 줍니다. 다음 테이블에서는 자세한 내용을 제공합니다.

| 자산 | 허용된 값 | 기본값 | 설명 |
| --- | --- | --- | --- |
| 동시성 |정수  <br/><br/>최댓값: 10 |1 |작업의 동시 실행 수입니다.<br/><br/>다른 조각에 발생할 수 있는 병렬 작업 실행 횟수를 결정합니다. 예를 들어 활동이 사용 가능한 많은 데이터 집합을 거쳐야 하는 경우 동시성 값을 높이면 데이터 처리가 빨라집니다. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |처리 중인 데이터 조각의 순서를 결정합니다.<br/><br/>예를 들어 2개의 조각이 있으며(각각 오후 4시 및 오후 5시에 발생) 둘 다 실행 보류 상태입니다. executionPriorityOrder를 설정하여 NewestFirst가 되도록 하면 오후 5시에 조각이 먼저 처리됩니다. 마찬가지로 executionPriorityORder를 OldestFIrst로 설정하면 오후 4시의 조각이 처리됩니다. |
| retry |정수 <br/><br/>최대값이 10이 될 수 있음 |0 |조각에 대한 데이터 처리 전에 다시 시도 횟수가 실패로 표시됩니다. 데이터 조각에 대한 활동 실행은 지정된 재시도 횟수까지 다시 시도됩니다. 재시도는 실패 후 가능한 한 빨리 수행합니다. |
| 시간 제한 |TimeSpan |00:00:00 |활동에 대한 시간 제한입니다. 예제: 00:10:00(시간 제한 10분을 의미함)<br/><br/>값이 지정되지 않거나 0인 경우 시간 제한은 무한입니다.<br/><br/>조각의 데이터 처리 시간이 시간 제한 값을 초과하면 취소되고 시스템이 처리를 다시 시도합니다. 다시 시도 횟수는 다시 시도 속성에 따라 달라집니다. 시간 제한이 발생할 때 상태는 TimedOut으로 설정됩니다. |
| delay |TimeSpan |00:00:00 |조각의 데이터 처리 작업이 시작되기 전에 지연을 지정합니다.<br/><br/>데이터 조각에 대한 활동의 실행은 지연이 예상된 실행 시간을 지난 후에 시작됩니다.<br/><br/>예제: 00:10:00(10분 지연을 의미함) |
| longRetry |정수 <br/><br/>최댓값: 10 |1 |조각 실행이 실패하기 전까지의 긴 재시도 횟수입니다.<br/><br/>longRetry 시도는 longRetryInterval에 따라 간격이 조정됩니다. 따라서 재시도 간의 시간을 지정해야 하는 경우 longRetry를 사용합니다. Retry 및 longRetry를 둘 다 지정하면 각 longRetry 시도에는 Retry 시도가 포함되고 최대 시도 횟수는 Retry * longRetry가 됩니다.<br/><br/>예를 들어 활동 정책에 다음 설정이 있는 경우:<br/>다시 시도: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>실행할 조각이 하나뿐이며(Waiting 상태) 작업 실행이 매번 실패한다고 가정합니다. 우선 3번 연속 실행 시도를 합니다. 시도한 후 각 조각 상태는 다시 시도입니다. 처음 3번 시도 후에 조각 상태는 LongRetry입니다.<br/><br/>한 시간(즉, longRetryInteval의 값) 후에 3번 연속 실행이 다시 시도됩니다. 그 후에 조각 상태가 실패이면 다시 시도는 더 이상 시도하지 않습니다. 즉, 전체적으로 6번의 시도가 일어납니다.<br/><br/>모든 실행에 성공하면 조각 상태는 준비 상태가 되며 더 이상 다시 시도하지 않습니다.<br/><br/>longRetry는 종속 데이터가 명확하지 않은 시간에 도착하거나 데이터 처리가 발생하는 전체적인 환경을 신뢰할 수 없는 상황에서 사용될 수 있습니다. 이러한 경우 하나씩 다시 시도를 수행해도 도움이 되지 않을 수 있으며 특정 시간 간격 후에 시도할 경우 출력이 나타납니다.<br/><br/>주의: longRetry 또는 longRetryInterval에 높은 값을 설정하지 마세요. 일반적으로 더 높은 값을 지정하면 다른 시스템 문제가 발생합니다. |
| longRetryInterval |TimeSpan |00:00:00 |긴 다시 시도 간의 지연 |

자세한 내용은 [파이프라인](data-factory-create-pipelines.md) 문서를 참조하세요. 

## <a name="parallel-processing-of-data-slices"></a>데이터 조각의 병렬 처리
파이프라인에 대해 과거의 시작 날짜를 설정할 수 있습니다. 이렇게 하면 Data Factory에서 과거의 모든 데이터 조각을 자동으로 계산(뒤 채우기)하고 처리를 시작합니다. 예를 들어 시작 날짜가 2017년 4월 1일이고 현재 날짜가 2017년 4월 10일인 파이프라인을 만드는 경우가 있습니다. 출력 데이터 세트의 빈도가 매일이면 시작 날짜가 과거이므로 Data Factory에서 2017년 4월 1일에서 2017년 4월 9일까지의 모든 조각을 즉시 처리하기 시작합니다. availability 섹션의 style 속성 값은 기본적으로 EndOfInterval이므로 2017년 4월 10일의 조각은 아직 처리되지 않습니다. executionPriorityOrder의 기본값이 OldestFirst이므로 가장 오래된 조각이 먼저 처리됩니다. style 속성에 대한 내용은 [데이터 세트 가용성](#dataset-availability) 섹션을 참조하세요. executionPriorityOrder 섹션에 대한 내용은 [활동 정책](#activity-policies) 섹션을 참조하세요. 

활동 JSON의 **policy** 섹션에서 **concurrency** 속성을 설정하여 뒷면 채우기된 데이터 조각을 병렬로 처리하도록 구성할 수 있습니다. 이 속성은 여러 조각에 발생할 수 있는 병렬 활동 실행 수를 결정합니다. concurrency 속성의 기본값은 1입니다. 따라서 기본적으로 조각이 한 번에 하나씩 처리됩니다. 최대값은 10입니다. 파이프라인이 사용 가능한 많은 데이터 집합을 거쳐야 하는 경우 concurrency 값을 높이면 데이터 처리가 더 빨라집니다. 

## <a name="rerun-a-failed-data-slice"></a>실패한 데이터 조각 다시 실행
데이터 조각을 처리하는 동안 오류가 발생하면 Azure Portal 블레이드 또는 모니터 및 관리 앱을 사용하여 조각 처리가 실패한 이유를 확인할 수 있습니다. 자세한 내용은 [Azure 포털 블레이드를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 또는 [앱 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

두 활동을 보여주는 다음 예제를 살펴보세요. 활동 1 및 활동 2가 있습니다. 활동 1은 데이터 세트 1 조각을 사용하고 데이터 세트 2 조각을 생성합니다. 활동 2에서는 입력으로 데이터 세트 2 조각을 사용하여 최종 데이터 세트의 조각을 생성합니다.

![실패한 조각](./media/data-factory-scheduling-and-execution/failed-slice.png)

다이어그램은 최근 3개 조각 중 데이터 세트 2에 대한 오전 9-10시 조각을 생성하는 데 오류가 있음을 보여줍니다. Data Factory는 시계열 데이터 세트에 대한 종속성을 자동으로 추적합니다. 따라서 오전 9-10시 다운스트림 조각에 대한 작업 실행은 시작하지 않습니다.

Data Factory 모니터링 및 관리 도구를 사용하면 실패한 조각에 대한 진단 로그를 자세히 보고 문제에 대한 근본 원인을 쉽게 파악하여 해결할 수 있습니다. 문제를 해결했으면 작업 실행을 쉽게 시작하여 실패한 조각을 생성할 수 있습니다. 데이터 조각의 상태 전환을 다시 실행하고 이해하는 방법에 대한 자세한 내용은 [Azure Portal 블레이드를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 또는 [앱 모니터링 및 관리 앱](data-factory-monitor-manage-app.md)을 참조하세요.

**Dataset2**에 대한 오전 9-10시 조각을 다시 실행한 후 Data Factory는 최종 데이터 세트에서 오전 9-10시 종속 조각에 대한 실행을 시작합니다.

![실패한 조각 다시 실행](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>파이프라인의 여러 활동
그렇지만 하나의 파이프라인에 여러 개의 활동이 있을 수 있습니다. 파이프라인에 여러 활동이 있고 활동의 출력이 다른 활동의 입력이 아닌 경우, 해당 활동에 대한 입력 데이터 조각이 준비되면 활동을 병렬로 실행할 수 있습니다.

한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정하여 두 활동을 연결하면 해당 활동을 차례로 실행할 수 있습니다. 활동은 동일한 파이프라인 또는 다른 파이프라인에 있을 수 있습니다. 두 번째 활동은 첫 번째 활동이 완료된 경우에만 실행됩니다.

예를 들어 파이프라인에 다음 두 활동이 있는 경우를 살펴보겠습니다.

1. 외부 입력 데이터 세트 D1이 필요하고 출력 데이터 세트 D2를 생성하는 활동 A1
2. 데이터 세트 D2로부터의 입력이 필요하고 출력 데이터 세트 D3을 생성하는 활동 A2

이 시나리오에서는 활동 A1과 A2가 동일한 파이프라인에 있습니다. 활동 A1은 외부 데이터를 사용할 수 있고 예약된 가용성 빈도에 도달할 때 실행됩니다. 활동 A2는 D2에서 예약된 조각을 사용할 수 있고 예약된 가용성 빈도에 도달할 때 실행됩니다. 데이터 세트 D2의 조각 중 하나에 오류가 있으면 해당 조각을 사용할 수 있을 때까지 A2가 실행되지 않습니다.

동일한 파이프라인에서 두 활동을 사용하는 다이어그램 뷰는 다음 다이어그램과 같습니다.

![동일한 파이프라인에서 활동 연결](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

앞에서 설명한 대로 활동은 다른 파이프라인에 있을 수 있습니다. 이러한 시나리오의 다이어그램은 다음과 같습니다.

![두 개의 파이프라인에서 활동 연결](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

예제는 부록의 순차적으로 복사 섹션을 참조하세요.

## <a name="model-datasets-with-different-frequencies"></a>다양한 빈도로 데이터 세트 모델링
샘플에서 입력 및 출력 데이터 세트와 작업 일정 창에 대한 빈도는 동일합니다. 일부 시나리오에서는 하나 이상의 입력 빈도와 다른 빈도로 출력을 생성하는 기능이 필요합니다. Data Factory가 이러한 시나리오의 모델링을 지원합니다.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>샘플 1: 매시간 제공되는 입력 데이터에 대해 일별 출력 보고서 생성
Azure Blob Storage에서 매시간 사용 가능한 센서로부터 입력 측정값 데이터가 있는 시나리오를 살펴보겠습니다. [Data Factory hive 작업](data-factory-hive-activity.md)을 사용하여 평균, 최대값, 최소값 등 특정일의 통계가 포함된 일별 집계 보고서를 생성하려고 합니다.

다음은 Data Factory로 이 시나리오를 모델링하는 방법입니다.

**입력 데이터 세트**

매시간 입력 파일이 지정된 날에 대한 폴더에서 삭제됩니다. 입력에 대한 Availability가 **Hour**로 설정됩니다(frequency: Hour, interval: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**출력 데이터 세트**

하나의 출력 파일이 그 날에 대한 폴더에서 매일 생성됩니다. 출력에 대한 Availability가 **Day**로 설정됩니다(frequency: Day and interval: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**작업: 파이프라인에서 hive 작업**

hive 스크립트는 적절한 *DateTime* 정보를 매개 변수로 받아 **WindowStart** 변수를 다음 코드 조각에 표시된 대로 사용합니다. hive 스크립트는 이 변수를 사용하여 그 날의 정확한 폴더에서 데이터를 로드하고 집계를 실행하여 출력을 생성합니다.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

다음 다이어그램은 데이터 종속성 관점에서 시나리오를 보여 줍니다.

![데이터 종속성](./media/data-factory-scheduling-and-execution/data-dependency.png)

매일 출력 조각은 입력 데이터 세트에서 24시간 조각에 따라 달라집니다. Data Factory는 동일한 기간에 속하는 입력 데이터 조각을 생성할 출력 조각으로 파악하여 이러한 종속성을 자동으로 계산합니다. 24개의 입력 조각 모두를 사용할 수 없는 경우 Data Factory는 입력 조각이 준비될 때까지 기다렸다가 일별 작업 실행을 시작합니다.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>샘플 2: 식 및 데이터 Data Factory 함수로 종속성 지정
다른 시나리오를 살펴보겠습니다. 두 개의 입력 데이터 세트를 처리하는 hive 작업이 있다고 가정합니다. 그 중 하나는 매일 새 데이터가 제공되지만 다른 하나는 매주 새 데이터를 가져옵니다. 두 입력에 조인을 수행하여 매일 출력을 생성하고 싶습니다.

Data Factory가 출력 데이터 조각의 기간에 맞추어 처리할 적절한 입력 조각을 자동으로 확인하는 단순한 접근법은 작동하지 않습니다.

모든 작업 실행에 대해 지정해야 하며 Data Factory에서 매주 입력 데이터 세트에 대해 마지막 주의 데이터 조각을 사용해야 합니다. 다음 코드 조각에 나와 있는 것처럼 Azure Data Factory 함수를 사용하여 이 동작을 구현할 수 있습니다.

**입력 1: Azure blob**

첫 번째 입력은 매일 업데이트된 Azure Blob입니다.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**입력 2: Azure blob**

입력2는 매주 업데이트된 Azure Blob입니다.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**출력: Azure blob**

하나의 출력 파일이 그 날에 대한 폴더에서 매일 생성됩니다. 출력에 대한 Availability가 **day**로 설정됩니다(frequency: Day, interval: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**작업: 파이프라인에서 hive 작업**

hive 작업에서는 2개의 입력을 받아 매일 출력 조각을 생성합니다. 다음과 같이 매주 입력에 대한 이전 주의 입력 조각에 따라 매일 출력 조각을 지정할 수 있습니다.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Data Factory에서 지원하는 함수 및 시스템 변수 목록은 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md) 를 참조하세요.

## <a name="appendix"></a>부록

### <a name="example-copy-sequentially"></a>예제: 순차적으로 복사
순차/순서가 지정된 방식으로 하나씩 여러 복사 작업을 실행하는 것이 가능합니다. 예를 들어 파이프라인에 두 개의 복사 활동, 즉 다음과 같은 입력 데이터 및 출력 데이터 세트를 포함하는 CopyActivity1과 CopyActivity2가 있을 수 있습니다.   

CopyActivity1

입력: Dataset. 출력 Dataset2.

CopyActivity2

입력: Dataset2.  출력 Dataset3.

CopyActivity2는 CopyActivity1을 성공적으로 실행하고 Dataset2를 사용할 수 있는 경우에만 실행됩니다.

샘플 파이프라인 JSON은 다음과 같습니다.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

예제에서는 첫 번째 복사 활동의 출력 데이터 세트(Dataset2)가 두 번째 활동의 입력으로 지정되어 있습니다. 따라서 첫 번째 활동의 출력 데이터 세트가 준비되어야 두 번째 활동이 실행됩니다.  

예제에서 CopyActivity2에 다른 입력(예: Dataset3)을 지정할 수 있지만 CopyActivity2에 대한 입력으로 Dataset2를 지정해야 CopyActivity1을 끝낼 때까지 작업이 실행되지 않습니다. 예를 들면 다음과 같습니다.

CopyActivity1

입력: Dataset1. 출력 Dataset2.

CopyActivity2

입력: Dataset3, Dataset2. 출력 Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

예제에서는 두 번째 복사 활동에 대해 입력 데이터 세트 두 개가 지정되어 있습니다. 여러 입력을 지정하는 경우 첫 번째 입력 데이터 세트만 데이터를 복사하는 데 사용되고 다른 데이터 세트는 종속성으로 사용됩니다. CopyActivity2는 다음 조건을 충족한 후에만 시작합니다.

* CopyActivity1이 성공적으로 완료되고 Dataset2가 사용 가능합니다. 이 데이터 세트는 Dataset4에 데이터를 복사할 때 사용되지 않습니다. CopyActivity2에 대한 일정 종속성으로만 작동합니다.   
* Dataset3을 사용할 수 있습니다. 이 데이터 세트는 대상에 복사되는 데이터를 나타냅니다. 
