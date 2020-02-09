---
title: Azure Data Factory의 파이프라인 및 작업
description: Azure Data Factory의 파이프라인 및 작업에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 6e466675a9bd86693ce0ee048480712a55829ce6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280730"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure Data Factory의 파이프라인 및 작업

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-create-pipelines.md)
> * [현재 버전](concepts-pipelines-activities.md)

이 문서는 Azure Data Factory의 파이프라인 및 활동을 이해하고 데이터 이동 및 데이터 처리 시나리오를 위한 엔드투엔드 데이터 기반 워크플로 사용하는 데 도움이 됩니다.

## <a name="overview"></a>개요
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 함께 작업을 수행하는 활동의 논리적 그룹화입니다. 예를 들어 파이프라인에는 로그 데이터를 수집 하 고 정리 하는 일련의 작업이 포함 될 수 있습니다. 그런 다음 매핑 데이터 흐름을 시작 하 여 로그 데이터를 분석 합니다. 파이프라인을 사용 하면 각 작업을 개별적이 아니라 하나의 집합으로 관리할 수 있습니다. 활동 대신 파이프라인을 독립적으로 배포 하 고 예약 합니다.

파이프라인의 활동은 데이터에 수행할 작업을 정의합니다. 예를 들어 복사 활동을 사용하여 온-프레미스 SQL Server에서 Azure Blob Storage로 데이터를 복사할 수 있습니다. 그런 다음 데이터 흐름 활동 또는 Databricks 노트북 활동을 사용 하 여 blob storage의 데이터를 처리 하 고 비즈니스 인텔리전스 보고 솔루션을 기반으로 하는 Azure Synapse Analytics 풀로 변환 합니다.

Data Factory에는 [데이터 이동 작업](copy-activity-overview.md), [데이터 변환 작업](transform-data.md)및 [제어 작업](control-flow-web-activity.md)이라는 세 가지 작업 그룹이 있습니다. 활동은 0개 이상의 입력 [데이터 세트](concepts-datasets-linked-services.md)를 받고 하나 이상의 출력 [데이터 세트](concepts-datasets-linked-services.md)를 생성할 수 있습니다. 다음 다이어그램은 데이터 팩터리의 파이프라인, 활동 및 데이터 세트 간 관계를 보여 줍니다.

![데이터 세트, 작업 및 파이프라인 간 관계](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

입력 데이터 세트는 파이프라인의 작업에 대한 입력을 나타내고 출력 데이터 세트는 작업에 대한 출력을 나타냅니다. 데이터 세트는 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다. 데이터 세트를 만든 후 파이프라인의 작업에 사용할 수 있습니다. 예를 들어 데이터 세트는 복사 작업 또는 HDInsightHive 작업의 입력/출력 데이터 세트일 수 있습니다. 데이터 세트에 대한 자세한 내용은 [Azure Data Factory의 데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요.

## <a name="data-movement-activities"></a>데이터 이동 활동

데이터 팩터리의 복사 활동은 원본 데이터 저장소의 데이터를 싱크 데이터 저장소로 복사합니다. Data Factory는 이 섹션의 테이블에 나열한 데이터 소스를 지원합니다. 모든 소스의 데이터를 모든 싱크에 쓸 수 있습니다. 데이터 저장소를 클릭하면 해당 저장소에서/저장소로 데이터를 복사하는 방법을 확인할 수 있습니다.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

자세한 내용은 [작업 복사 - 개요](copy-activity-overview.md)를 참조하세요.

## <a name="data-transformation-activities"></a>데이터 변환 활동
Azure Data Factory는 개별적 또는 다른 작업과 연계하여 파이프라인에 추가할 수 있는 다음과 같은 변환 작업을 지원합니다.

데이터 변환 작업 | 컴퓨팅 환경
---------------------------- | -------------------
[데이터 흐름](control-flow-execute-data-flow-activity.md) | Azure Data Factory에서 관리 하는 Azure Databricks
[Azure Function](control-flow-azure-function-activity.md) | Azure 기능
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[사용](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop 스트리밍](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Machine Learning 작업: Batch 실행 및 업데이트 리소스](transform-data-using-machine-learning.md) | Azure VM
[저장 프로시저](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Data Warehouse 또는 SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure 데이터 레이크 분석
[사용자 지정 작업](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks Notebook](transform-data-databricks-notebook.md) | Azure Databricks
[Databricks Jar 활동](transform-data-databricks-jar.md) | Azure Databricks
[Databricks Python 활동](transform-data-databricks-python.md) | Azure Databricks

자세한 내용은 [데이터 변환 작업](transform-data.md) 문서를 참조하세요.

## <a name="control-flow-activities"></a>제어 흐름 작업
다음과 같은 제어 흐름 작업이 지원 됩니다.

제어 작업 | 설명
---------------- | -----------
[변수 추가](control-flow-append-variable-activity.md) | 기존 배열 변수에 값을 추가 합니다.
[파이프라인 실행](control-flow-execute-pipeline-activity.md) | 파이프라인 실행 작업을 사용하면 하나의 Data Factory 파이프라인에서 다른 파이프라인을 호출할 수 있습니다.
[Filter](control-flow-filter-activity.md) | 입력 배열에 필터 식 적용
[For Each](control-flow-for-each-activity.md) | ForEach 작업은 파이프라인의 반복 제어 흐름을 정의합니다. 이 작업을 사용하여 컬렉션을 반복하고 루프의 지정된 작업을 실행합니다. 이 작업의 루프 구현은 프로그래밍 언어에서 구조를 반복하는 Foreach와 비슷합니다.
[메타데이터 가져오기](control-flow-get-metadata-activity.md) | GetMetadata 작업은 Azure Data Factory에 있는 모든 데이터의 메타데이터를 검색하는 데 사용할 수 있습니다.
[If 조건 작업](control-flow-if-condition-activity.md) | If 조건을 사용하여 True 또는 False로 평가되는 조건을 기반으로 분기할 수 있습니다. If 조건 작업은 if 문에서 프로그래밍 언어로 제공하는 것과 동일한 기능을 제공합니다. 조건이 `true`로 평가되면 작업 집합을 평가하고, 조건이 `false`로 평가되면 다른 작업 집합을 평가합니다.
[조회 작업](control-flow-lookup-activity.md) | 조회 작업은 모든 외부 소스에서 레코드/테이블 이름/값을 읽거나 조회하는 데 사용할 수 있습니다. 이 출력을 다음 작업에서 추가로 참조할 수 있습니다.
[변수 설정](control-flow-set-variable-activity.md) | 기존 변수의 값을 설정 합니다.
[Until 작업](control-flow-until-activity.md) | 프로그래밍 언어의 Do-Until 루핑 구조와 유사한 Do-Until 루프를 구현합니다. 작업과 관련된 조건이 참으로 평가될 때까지 일단의 반복 작업을 실행합니다. Data Factory에서 until 작업의 시간 제한 값을 지정할 수 있습니다.
[유효성 검사 작업](control-flow-validation-activity.md) | 참조 데이터 집합이 존재 하거나 지정 된 조건을 충족 하거나 시간 제한에 도달 하는 경우에만 파이프라인이 실행을 계속 하도록 합니다.
[Wait 작업](control-flow-wait-activity.md) | 파이프라인에서 대기 작업을 사용하는 경우 파이프라인은 후속 작업을 계속 실행하기 전에 지정된 기간 동안 대기합니다.
[웹 작업](control-flow-web-activity.md) | 웹 작업은 Data Factory 파이프라인에서 사용자 지정 REST 엔드포인트를 호출하는 데 사용할 수 있습니다. 작업에서 사용하고 액세스하도록 데이터 세트 및 연결된 서비스를 전달할 수 있습니다.
[Webhook 활동](control-flow-webhook-activity.md) | Webhook 활동을 사용 하 여 엔드포인트을 호출 하 고 콜백 URL을 전달 합니다. 파이프라인 실행은 다음 작업을 진행 하기 전에 콜백이 호출 될 때까지 기다립니다.

## <a name="pipeline-json"></a>파이프라인 JSON
파이프라인은 다음과 같은 방식에 따라 JSON 형식으로 정의됩니다.

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
        },
        "concurrency": <your max pipeline concurrency>,
        "annotations": [
        ]
    }
}
```

태그 | 설명 | 에 | 필수
--- | ----------- | ---- | --------
name | 파이프라인의 이름입니다. 파이프라인이 수행하는 작업을 나타내는 이름을 지정합니다. <br/><ul><li>최대 문자 수: 140개</li><li>문자, 숫자 또는 밑줄(\_)로 시작해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\”</li></ul> | 문자열 | 예
설명 | 파이프라인의 용도를 설명하는 텍스트를 지정합니다. | 문자열 | 아니오
작업 | **활동** 섹션에는 내부에서 정의된 하나 이상의 활동이 있을 수 있습니다. JSON 작업 요소에 대한 자세한 내용은 [JSON 작업](#activity-json) 섹션을 참조하세요. | String | 예
parameters | **매개 변수** 섹션은 파이프라인 내에 정의된 매개 변수 한 개 이상을 포함할 수 있으므로 파이프라인을 유연하게 다시 사용할 수 있습니다. | 나열 | 아니오
동시성 | 파이프라인에 포함할 수 있는 최대 동시 실행 수입니다. 기본적으로 최대값이 없습니다. 동시성 한도에 도달 하면 추가 파이프라인 실행은 이전에 완료 될 때까지 큐에 대기 됩니다. | NUMBER | 아니오 
달 | 파이프라인과 연결 된 태그 목록입니다. | String | 아니오

## <a name="activity-json"></a>활동 JSON
**활동** 섹션에는 내부에서 정의된 하나 이상의 활동이 있을 수 있습니다. 작업에는 실행 및 제어 작업의 두 가지 주요 유형이 있습니다.

### <a name="execution-activities"></a>실행 작업
실행 작업은 [데이터 이동](#data-movement-activities) 및 [데이터 변환 작업](#data-transformation-activities)을 포함합니다. 각 작업에 다음과 같은 최상위 수준 구조가 있습니다.

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

다음 표에서는 활동 JSON 정의의 속성을 설명합니다.

태그 | 설명 | 필수
--- | ----------- | ---------
name | 활동의 이름입니다. 활동이 수행하는 작업을 나타내는 이름을 지정합니다. <br/><ul><li>최대 문자 수: 55개</li><li>문자, 숫자 또는 밑줄(\_)로 시작해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\” | 예</li></ul>
설명 | 활동의 용도를 설명하는 텍스트입니다. | 예
type | 활동의 형식입니다. 작업의 여러 가지 유형에 대해서는 [데이터 이동 작업](#data-movement-activities), [데이터 변환 작업](#data-transformation-activities) 및 [제어 작업](#control-flow-activities) 섹션을 참조하세요. | 예
linkedServiceName | 작업에서 사용하는 연결된 서비스의 이름입니다.<br/><br/>작업은 필요한 컴퓨팅 환경에 연결하는 연결된 서비스를 지정해야 할 수 있습니다. | HDInsight 작업, Azure Machine Learning 일괄 처리 점수 매기기 작업, 저장 프로시저 작업의 경우 예입니다. <br/><br/>다른 모든 사용자의 경우 아니요
typeProperties | typeProperties 섹션의 속성은 각 작업 유형에 따라 달라집니다. 활동의 형식 속성을 보려면 이전 섹션의 활동 링크를 클릭합니다. | 아니오
policy(정책) | 작업의 런타임 동작에 영향을 주는 정책입니다. 이 속성은 시간 제한 및 다시 시도 동작을 포함합니다. 지정하지 않으면 기본값을 사용합니다. 자세한 내용은 [작업 정책](#activity-policy)을 참조하세요. | 아니오
dependsOn | 이 속성을 사용하여 작업 종속성 및 이후 작업이 이전 작업에 따라 달라지는 방법을 정의합니다. 자세한 내용은 [작업 종속성](#activity-dependency) 참조 | 아니오

### <a name="activity-policy"></a>작업 정책
정책은 작업의 런타임 동작에 영향을 미치며 구성 기능 옵션을 제공합니다. 작업 정책은 실행 작업에 대해서만 사용할 수 있습니다.

### <a name="activity-policy-json-definition"></a>작업 정책 JSON 정의

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity",
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

JSON 이름 | 설명 | 허용되는 값 | 필수
--------- | ----------- | -------------- | --------
시간 제한 | 작업 실행에 대한 시간 제한을 지정합니다. | Timespan | 아니요. 기본 시간 제한은 7일입니다.
retry | 최대 다시 시도 횟수 | 정수, | 아니요. 기본값은 0입니다.
retryIntervalInSeconds | 다시 시도 사이의 지연(초) | 정수, | 아니요. 기본값은 30초입니다.
secureOutput | true로 설정된 경우 작업의 출력은 안전하다고 여기고 모니터링에 기록되지 않습니다. | Boolean | 아니요. 기본값은 false입니다.

### <a name="control-activity"></a>제어 작업
제어 작업에는 다음과 같은 최상위 수준 구조가 있습니다.

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

태그 | 설명 | 필수
--- | ----------- | --------
name | 활동의 이름입니다. 활동이 수행하는 작업을 나타내는 이름을 지정합니다.<br/><ul><li>최대 문자 수: 55개</li><li>문자, 숫자 또는 밑줄(\_)로 시작해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\” | 예</li><ul>
설명 | 활동의 용도를 설명하는 텍스트입니다. | 예
type | 활동의 형식입니다. 작업의 여러 가지 유형에 대해서는 [데이터 이동 작업](#data-movement-activities), [데이터 변환 작업](#data-transformation-activities) 및 [제어 작업](#control-flow-activities) 섹션을 참조하세요. | 예
typeProperties | typeProperties 섹션의 속성은 각 작업 유형에 따라 달라집니다. 활동의 형식 속성을 보려면 이전 섹션의 활동 링크를 클릭합니다. | 아니오
dependsOn | 이 속성을 사용하여 작업 종속성 및 이후 작업이 이전 작업에 따라 달라지는 방법을 정의합니다. 자세한 내용은 [작업 종속성](#activity-dependency)을 참조하세요. | 아니오

### <a name="activity-dependency"></a>작업 종속성
작업 종속성은 이후 작업이 이전 작업에 따라 달라지는 방법을 정의하여 다음 작업의 실행을 계속할지 여부에 대한 조건을 결정합니다. 작업은 서로 다른 종속성 조건을 포함하는 한 개 또는 여러 이전 작업에 따라 달라질 수 있습니다.

서 로다른 종속성 조건: 성공, 실패, 건너뜀, 완료됨.

예를 들어 파이프라인이 작업 A -> 작업 B라는 조건을 포함한 경우 일어날 수 있는 서로 다른 시나리오는 다음과 같습니다.

- 작업 B가 작업 A에 대해 **성공** 종속성 조건을 가지면 작업 B는 작업 A의 최종 상태가 성공인 경우에만 실행
- 작업 B가 작업 A에 대해 **실패** 종속성 조건을 가지면 작업 B는 작업 A의 최종 상태가 실패인 경우에만 실행
- 작업 B가 작업 A에 대해 **완료됨** 종속성 조건을 가지면 작업 B는 작업 A의 최종 상태가 성공 또는 실패인 경우에만 실행
- 작업 B가 작업 A에 대해 **건너뜀** 종속성 조건을 가지면 작업 B는 작업 A의 최종 상태가 건너뜀인 경우에만 실행합니다. 건너뜀은 각 작업이 이전 작업이 성공한 경우에만 실행하는 작업 X -> 작업 Y -> 작업 Z의 시나리오에서 발생합니다. 작업 X가 실패하면 작업 Y는 실행하지 않기 때문에 "건너뜀" 상태를 가집니다. 마찬가지로 작업 Z도 "건너뜀" 상태를 가집니다.

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>예: 작업 2는 작업 1의 성공에 따라 달라짐

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>샘플 복사 파이프라인
다음 샘플 파이프라인에는 **Copy** in the **활동** 유형의 하나의 활동이 있습니다. 샘플에서 [복사 활동](copy-activity-overview.md)은 Azure Blob 스토리지의 데이터를 Azure SQL 데이터베이스에 복사합니다.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
다음 사항에 유의하세요.

- 작업 섹션에는 **형식**이 **복사**로 설정된 작업만 있습니다.
- 작업에 대한 입력을 **InputDataset**으로 설정하고 작업에 대한 출력을 **OutputDataset**으로 설정합니다. JSON의 데이터 세트 정의에 대해서는 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요.
- **typeProperties** 섹션에서 **BlobSource**를 원본 유형으로 지정하고 **SqlSink**를 싱크 유형으로 지정합니다. [데이터 이동 작업](#data-movement-activities) 섹션에서 소스 또는 싱크로 사용할 데이터 저장소를 클릭하여 해당 데이터 저장소로/부터 데이터를 이동하는 방법을 알아봅니다.

이 파이프라인 만드는 전체 연습은 [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)를 참조하세요.

## <a name="sample-transformation-pipeline"></a>샘플 변환 파이프라인
다음 샘플 파이프라인에는 **HDInsightHive** in the **활동** 유형의 하나의 활동이 있습니다. 이 샘플에서 [HDInsight Hive 활동](transform-data-using-hadoop-hive.md) 은 Azure HDInsight Hadoop 클러스터에서 Hive 스크립트 파일을 실행하여 Azure Blob Storage에서 데이터를 변환합니다.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
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
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
다음 사항에 유의하세요.

- activities 섹션에는 **type**이 **HDInsightHive**로 설정된 작업만 있습니다.
- Hive 스크립트 파일 **partitionweblogs.hql**은 Azure 스토리지 계정(AzureStorageLinkedService라는 scriptLinkedService에 의해 지정됨) 및 컨테이너 `adfgetstarted`의 스크립트 폴더에 저장됩니다.
- `defines` 섹션은 Hive 스크립트에 Hive 구성 값(예: $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`)으로 전달되는 런타임 설정을 지정하는 데 사용됩니다.

**typeProperties** 섹션은 각 변환 활동마다 다릅니다. 변환 작업에 대해 지원되는 형식 속성에 대해 알아보려면 [데이터 변환 작업](#data-transformation-activities)에서 변환 작업을 클릭합니다.

이 파이프라인을 만드는 전체 연습은 [자습서: Spark를 사용하여 데이터 변환](tutorial-transform-data-spark-powershell.md)을 참조하세요.

## <a name="multiple-activities-in-a-pipeline"></a>파이프라인의 여러 활동
이전에 나온 두 개의 샘플 파이프라인에는 활동이 하나만 있습니다. 그렇지만 하나의 파이프라인에 여러 개의 활동이 있을 수 있습니다. 파이프라인에 여러 작업이 있고 이후 작업이 이전 작업에 종속되지 않는 경우 작업을 병렬로 실행할 수 있습니다.

[작업 종속성](#activity-dependency)을 사용하여 두 작업을 연결할 수 있으며, 작업 종속성은 이후 작업이 이전 작업에 따라 달라지는 방법을 정의하여 다음 작업의 실행을 계속할지 여부에 대한 조건을 결정합니다. 여러 종속성 조건이 있는 하나 이상의 이전 작업에 따라 작업이 달라질 수 있습니다.

## <a name="scheduling-pipelines"></a>파이프라인 일정 계획
파이프라인은 트리거에 의해 일정 계획됩니다. 다양한 유형의 트리거가 있습니다(벽시계 일정에 따라 파이프라인을 트리거할 수 있는 스케줄러 트리거 및 요청 시 파이프라인을 트리거하는 수동 트리거). 트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md) 문서를 참조하세요.

트리거가 파이프라인 실행을 시작하게 하려면 특정 파이프라인의 파이프라인 참조를 트리거 정의에 포함해야 합니다. 파이프라인 및 트리거는 n-m 관계를 가지고 있습니다. 다중 트리거는 단일 파이프라인을 시작할 수 있으며 같은 트리거가 여러 파이프라인을 시작할 수 있습니다. 트리거가 정의된 후 트리거를 시작하여 파이프라인 트리거를 시작하도록 해야 합니다. 트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md) 문서를 참조하세요.

예를 들어 파이프라인 "MyCopyPipeline"를 시작 하려는 스케줄러 트리거 "트리거 A"가 있다고 가정해 보겠습니다. 다음 예제와 같이 트리거를 정의합니다.

### <a name="trigger-a-definition"></a>트리거 A 정의

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>다음 단계
작업을 포함하는 파이프라인을 만드는 단계별 지침은 다음 자습서를 참조하세요.

- [복사 작업을 포함하는 데이터 파이프라인 만들기](quickstart-create-data-factory-powershell.md)
- [데이터 변환 활동을 사용하여 파이프라인 빌드](tutorial-transform-data-spark-powershell.md)
