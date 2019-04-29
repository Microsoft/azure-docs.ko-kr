---
title: Azure Data Factory에서 Pig 활동을 사용하여 데이터 변환 | Microsoft Docs
description: Azure Data Factory에서 Pig 작업을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Pig 스크립트를 실행하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 78ee2c1ce402a29f1a9dfdd29f31daef09134eba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611330"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Azure Data Factory에서 Pig 활동을 사용하여 데이터 변환
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 작업](data-factory-hive-activity.md) 
> * [Pig 작업](data-factory-pig-activity.md)
> * [MapReduce 작업](data-factory-map-reduce.md)
> * [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
> * [Spark 작업](data-factory-spark.md)
> * [Machine Learning Batch 실행 작업](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 업데이트 리소스 작업](data-factory-azure-ml-update-resource-activity.md)
> * [저장 프로시저 작업](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 작업](data-factory-usql-activity.md)
> * [.NET 사용자 지정 작업](data-factory-use-custom-activities.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory에서 Pig 작업을 사용하여 데이터 변환](../transform-data-using-hadoop-pig.md)을 참조하세요.


Data Factory [파이프라인](data-factory-create-pipelines.md)의 HDInsight Pig 작업은 [사용자 고유](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 기반 HDInsight 클러스터의 Pig 쿼리를 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

> [!NOTE] 
> Azure Data Factory를 처음 접하는 경우 이 문서를 읽기 전에 [Azure Data Factory 소개](data-factory-introduction.md)를 읽고 [첫 번째 데이터 파이프라인 빌드](data-factory-build-your-first-pipeline.md) 자습서를 수행하세요. 

## <a name="syntax"></a>구문

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>구문 세부 정보

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 이름 |작업의 이름 |예 |
| description |작업이 무엇에 사용되는지 설명하는 텍스트입니다. |아닙니다. |
| 형식 |HDinsightPig |예 |
| inputs |Pig 활동에서 사용하는 하나 이상의 입력 |아닙니다. |
| outputs |Pig 활동에서 생성하는 하나 이상의 출력 |예 |
| linkedServiceName |데이터 팩터리에서 연결된 서비스로 등록된 HDInsight 클러스터에 대한 참조 |예 |
| script |Pig 스크립트 인라인 지정 |아닙니다. |
| script path |Azure File Storage는 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 클라우드에서 파일 공유를 제공하는 서비스입니다. 'script' 또는 'scriptPath' 속성을 사용합니다. 둘 모두를 사용할 수는 없습니다. 파일 이름은 대/소문자를 구분합니다. |아닙니다. |
| defines |Pig 스크립트 내에서 참조하기 위해 매개 변수를 키/값 쌍으로 지정 |아닙니다. |

## <a name="example"></a>예
회사에서 출시한 게임을 플레이어가 플레이한 시간을 파악하려는 게임 로그 분석의 예를 살펴보겠습니다.

다음 샘플 게임 로그는 쉼표(,)로 구분된 파일입니다. 이 파일에는 ProfileID, SessionStart, Duration, SrcIPAddress 및 GameType 필드가 포함되어 있습니다.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

이 데이터를 처리하는 **Pig 스크립트** 는 다음과 같습니다.

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

데이터 팩터리 파이프라인에서 이 Pig 스크립트를 실행하려면 다음 단계를 수행합니다.

1. 연결된 서비스를 만들어 [자체적인 HDInsight 컴퓨팅 클러스터](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)를 등록하거나 [주문형 HDInsight 컴퓨팅 클러스터](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 구성합니다. 이 연결된 서비스를 **HDInsightLinkedService**라고 하겠습니다.
2. 데이터를 호스팅하는 Azure Blob Storage로의 연결을 구성하기 위해 [연결된 서비스](data-factory-azure-blob-connector.md) 를 만듭니다. 이 연결된 서비스를 **StorageLinkedService**라고 하겠습니다.
3. 입력 및 출력 데이터를 가리키는 [데이터 세트](data-factory-create-datasets.md)를 만듭니다. 입력 데이터 세트를 **PigSampleIn**이라고 하고, 출력 데이터 세트를 **PigSampleOut**이라고 하겠습니다.
4. 2단계에서 구성한 Azure Blob Storage의 파일에 Pig 쿼리를 복사합니다. 데이터를 호스트하는 Azure Storage가 쿼리 파일을 호스트하는 Azure Storage와 다른 경우에는 별도의 Azure Storage 연결된 서비스를 만듭니다. 활동 구성에서 연결된 서비스를 참조할 수 있습니다. 사용 하 여 **scriptPath** pig 스크립트 파일의 경로를 지정 하 고 **scriptLinkedService**합니다. 
   
   > [!NOTE]
   > **script** 속성을 사용하여 활동 정의에서 Pig 스크립트를 인라인으로 제공할 수도 있습니다. 그러나 이렇게 하면 스크립트의 모든 특수 문자를 이스케이프 처리해야 하므로 디버그 관련 문제가 발생할 수 있기 때문에 이 방식은 사용하지 않는 것이 좋습니다. 모법 사례는 4단계를 수행하는 것입니다.
   >
   >
5. HDInsightPig 활동이 포함된 파이프라인을 만듭니다. 이 활동은 HDInsight 클러스터에서 Pig 스크립트를 실행하여 입력 데이터를 처리합니다.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. 파이프라인을 배포합니다. 자세한 내용은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 
7. 데이터 팩터리 모니터링 및 관리 보기를 사용하여 파이프라인을 모니터링합니다. 자세한 내용은 [데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 문서를 참조하세요.

## <a name="specifying-parameters-for-a-pig-script"></a>Pig 스크립트에 대한 매개 변수 지정
게임 로그가 Azure Blob Storage에 매일 수집되고 날짜 및 시간을 기준으로 분할된 폴더에 저장되는 경우의 예를 들어 보겠습니다. Pig 스크립트에 매개 변수를 사용하여 런타임 동안 입력 폴더 위치를 동적으로 전달하며 날짜 및 시간으로 분할된 출력을 생성하려고 합니다.

매개 변수가 있는 Pig 스크립트를 사용하려면 다음을 수행합니다.

* **defines**에서 매개 변수를 정의합니다.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* Pig 스크립트에서 다음 예와 같이 '**$parameterName**'을 사용하여 매개 변수를 참조합니다.

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>관련 항목
* [Hive 작업](data-factory-hive-activity.md)
* [MapReduce 작업](data-factory-map-reduce.md)
* [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
* [Spark 프로그램 호출](data-factory-spark.md)
* [R 스크립트 호출](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
