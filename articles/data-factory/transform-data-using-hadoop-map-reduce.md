---
title: Azure Data Factory에서 Hadoop MapReduce 활동을 사용하여 데이터 변환 | Microsoft Docs
description: Azure HDInsight 클러스터에서 Azure Data Factory의 Hadoop MapReduce 프로그램을 실행하여 데이터를 처리하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: ccc194dd4120762a30da3ad28cdabed6faf53ba2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611511"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Azure Data Factory에서 Hadoop MapReduce 활동을 사용하여 데이터 변환
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-map-reduce.md)
> * [현재 버전](transform-data-using-hadoop-map-reduce.md)

Data Factory [파이프라인](concepts-pipelines-activities.md)의 HDInsight MapReduce 활동은 [사용자 고유](compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 클러스터에서 MapReduce 프로그램을 호출합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](transform-data.md) 문서에서 작성합니다.

Azure Data Factory를 처음 접하는 경우 이 문서를 읽기 전에 [Azure Data Factory 소개](introduction.md)를 읽고 [자습서: 데이터 변환](tutorial-transform-data-spark-powershell.md)을 수행하세요.

HDInsight Pig 및 Hive를 사용하여 파이프라인에서 HDInsight 클러스터에 대해 Pig/Hive 스크립트를 실행하는 방법에 대한 자세한 내용은 [Pig](transform-data-using-hadoop-pig.md) 및 [Hive](transform-data-using-hadoop-hive.md) 문서를 참조하세요.

## <a name="syntax"></a>구문

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>구문 세부 정보

| 자산          | 설명                              | 필수 |
| ----------------- | ---------------------------------------- | -------- |
| 이름              | 작업의 이름                     | 예      |
| description       | 작업이 무엇에 사용되는지 설명하는 텍스트입니다. | 아닙니다.       |
| 형식              | MapReduce 작업의 경우 작업 유형은 HDinsightMapReduce입니다. | 예      |
| linkedServiceName | Data Factory에서 연결된 서비스로 등록된 HDInsight 클러스터에 대한 참조입니다. 이 연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요. | 예      |
| className         | 실행할 클래스의 이름         | 예      |
| jarLinkedService  | Jar 파일을 저장하는 데 사용되는 Azure Storage 연결된 서비스에 대한 참조입니다. 이 연결된 서비스를 지정하지 않으면 HDInsight 연결된 서비스에 정의된 Azure Storage 연결된 서비스가 사용됩니다. | 아닙니다.       |
| jarFilePath        | jarLinkedService에서 참조하는 Azure Storage에 저장된 Jar 파일의 경로를 제공합니다. 파일 이름은 대/소문자를 구분합니다. | 예      |
| jarlibs           | jarLinkedService에 정의된 Azure Storage에 저장된 작업에서 참조하는 Jar 라이브러리 파일의 경로에 대한 문자열의 배열입니다. 파일 이름은 대/소문자를 구분합니다. | 아닙니다.       |
| getDebugInfo      | jarLinkedService에 지정되었거나 HDInsight 클러스터에 사용된 Azure Storage에 로그 파일을 언제 복사할지 지정합니다. 허용되는 값은 다음과 같습니다. 없음, 항상 또는 실패. 기본값: 없음. | 아닙니다.       |
| arguments         | Hadoop 작업에 대한 인수 배열을 지정합니다. 인수는 각 작업에 대한 명령줄 인수로 전달됩니다. | 아닙니다.       |
| defines           | Hive 스크립트 내에서 참조하기 위해 매개 변수를 키/값 쌍으로 지정합니다. | 아닙니다.       |



## <a name="example"></a>예
HDInsight MapReduce 작업을 사용하여 HDInsight 클러스터에서 모든 MapReduce jar 파일을 실행할 수 있습니다. 다음 파이프라인의 샘플 JSON 정의에서 HDInsight 작업은 Mahout JAR 파일을 실행하도록 구성되어 있습니다.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
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
    }
}
```
**arguments** 섹션에 MapReduce 프로그램의 모든 인수를 지정할 수 있습니다. 런타임에 MapReduce 프레임워크의 몇 개 인수(예: mapreduce.job.tags)가 추가로 표시됩니다. MapReduce 인수와 사용자 인수를 구분하려면 다음 예제와 같이 옵션과 값을 둘 다 인수로 사용하는 것이 좋습니다(-s, --input, --output 등은 바로 뒤에 해당 값이 있는 옵션임).

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요.

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch 실행 작업](transform-data-using-machine-learning.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
