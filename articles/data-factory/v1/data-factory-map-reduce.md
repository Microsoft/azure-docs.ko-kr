---
title: Azure 데이터 팩터리에서 MapReduce 프로그램 호출
description: Azure HDInsight 클러스터에서 Azure 데이터 팩터리의 MapReduce 프로그램을 실행하여 데이터를 처리하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 715c595f7a8757842ddf10de1c5d5c0a905e9d53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824221"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>데이터 팩터리에서 MapReduce 프로그램 호출
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
> 이 문서의 내용은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory에서 MapReduce 작업을 사용하여 데이터 변환](../transform-data-using-hadoop-map-reduce.md)을 참조하세요.


Data Factory [파이프라인](data-factory-create-pipelines.md)의 HDInsight MapReduce 작업은 [사용자 고유](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 기반 HDInsight 클러스터에서 MapReduce 프로그램을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

> [!NOTE] 
> Azure Data Factory를 처음 접하는 경우 이 문서를 읽기 전에 [Azure Data Factory 소개](data-factory-introduction.md)를 읽고 [첫 번째 데이터 파이프라인 빌드](data-factory-build-your-first-pipeline.md) 자습서를 수행하세요.  

## <a name="introduction"></a>소개
Azure 데이터 팩터리의 파이프라인은 연결된 컴퓨팅 서비스를 사용하여 스토리지 서비스의 데이터를 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 이 문서에서는 HDInsight MapReduce 작업을 사용하는 방법을 설명합니다.

HDInsight Pig 및 Hive를 사용하여 파이프라인에서 Windows/Linux 기반 HDInsight 클러스터에 대해 Pig/Hive 스크립트를 실행하는 방법에 대한 자세한 내용은 [Pig](data-factory-pig-activity.md) 및 [Hive](data-factory-hive-activity.md) 문서를 참조하세요. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>HDInsight MapReduce 작업에 대한 JSON
HDInsight 작업에 대한 JSON 정의에서 다음을 수행합니다: 

1. **activity**의 **type**을 **HDInsight**로 설정합니다.
2. **className** 속성에 대한 클래스 이름을 지정합니다.
3. **jarFilePath** 속성의 JAR 파일 경로(파일 이름 포함)를 지정합니다.
4. **jarLinkedService** 속성의 JAR 파일이 포함된 Azure Blob Storage를 참조하는 연결된 서비스를 지정합니다.   
5. **arguments** 섹션에 MapReduce 프로그램의 모든 인수를 지정합니다. 런타임에 MapReduce 프레임워크의 몇 개 인수(예: mapreduce.job.tags)가 추가로 표시됩니다. MapReduce 인수와 사용자 인수를 구분하려면 다음 예제와 같이 옵션과 값을 둘 다 인수로 사용하는 것이 좋습니다(-s, --input, --output 등은 바로 뒤에 해당 값이 있는 옵션임).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   HDInsight MapReduce 작업을 사용하여 HDInsight 클러스터에서 모든 MapReduce jar 파일을 실행할 수 있습니다. 다음 파이프라인의 샘플 JSON 정의에서 HDInsight 작업은 Mahout JAR 파일을 실행하도록 구성되어 있습니다.

## <a name="sample-on-github"></a>GitHub의 샘플
HDInsight MapReduce 작업을 사용하는 샘플은 [GitHub의 Data Factory 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample)에서 다운로드할 수 있습니다.  

## <a name="running-the-word-count-program"></a>Word Count 프로그램 실행
이 예제의 파이프라인에서는 Azure HDInsight 클러스터에서 Word Count Map/Reduce 프로그램을 실행합니다.   

### <a name="linked-services"></a>연결된 서비스
우선 Azure HDInsight 클러스터에서 사용되는 Azure Storage를 Azure 데이터 팩터리에 연결하도록 연결된 서비스를 생성합니다. 다음 코드를 복사하여 붙여넣을 경우 **계정 이름**과 **계정 키**를 Azure Storage의 이름과 키로 바꾸는 것을 잊지 마세요. 

#### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 연결된 서비스
다음으로, Azure HDInsight 클러스터를 Azure 데이터 팩터리에 연결하도록 연결된 서비스를 만듭니다. 다음 코드를 복사하여 붙여넣는 경우, **HDInsight 클러스터 이름** 을 사용자의 HDInsight 클러스터 이름으로 바꾸고 사용자 이름과 암호 값을 변경합니다.   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>데이터 세트
#### <a name="output-dataset"></a>출력 데이터 세트
이 예제의 파이프라인은 input을 포함하지 않습니다. HDInsight MapReduce 작업에 대한 출력 데이터 세트를 지정합니다. 이 데이터 세트는 파이프라인 일정을 진행하는데 필요한 더미 데이터 세트입니다.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>파이프라인
이 예제의 파이프라인은 다음 형식의 작업을 하나만 포함합니다. HDInsightMapReduce. JSON의 중요한 속성에 대한 예를 들면 다음과 같습니다. 

| 자산 | 메모 |
|:--- |:--- |
| 형식 |type은 **HDInsightMapReduce**로 설정되어야 합니다. |
| className |클래스 이름은 **wordcount** |
| jarFilePath  |클래스를 포함하는 jar 파일의 경로입니다. 다음 코드를 복사하여 붙여넣는 경우 클러스터의 이름을 변경해야 합니다. |
| jarLinkedService |jar 파일을 포함하는 Azure Storage 연결된 서비스입니다. 이 연결된 서비스는 HDInsight 클러스터와 연결되는 저장소를 지칭합니다. |
| arguments |Wordcount 프로그램에서는 input과 output의 두 가지 인수를 사용합니다. input 파일은 davinci.txt 파일입니다. |
| frequency/interval |이러한 속성의 값은 출력 데이터 세트와 일치합니다. |
| linkedServiceName |이전에 만든 HDInsight 연결된 서비스를 말합니다. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Spark 프로그램 실행
MapReduce 작업을 사용하여 HDInsight Spark 클러스터에서 Spark 프로그램을 실행할 수 있습니다. 자세한 내용은 [Azure Data Factory에서 Spark 프로그램 호출](data-factory-spark.md) 을 참조하세요.  

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>관련 항목
* [Hive 작업](data-factory-hive-activity.md)
* [Pig 작업](data-factory-pig-activity.md)
* [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
* [Spark 프로그램 호출](data-factory-spark.md)
* [R 스크립트 호출](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

