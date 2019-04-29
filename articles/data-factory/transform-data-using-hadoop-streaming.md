---
title: Azure Data Factory에서 Hadoop 스트리밍 작업을 사용하여 데이터 변환 | Microsoft Docs
description: Azure Data Factory에서 Hadoop 스트리밍 작업을 사용하여 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행함으로써 데이터를 변환하는 방법을 설명합니다.
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
ms.openlocfilehash: 0d8267f1cd65f78d5e98ae9d288d5fa5c4214420
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848251"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Azure Data Factory에서 Hadoop 스트리밍 작업을 사용하여 데이터 변환
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-hadoop-streaming-activity.md)
> * [현재 버전](transform-data-using-hadoop-streaming.md)

Data Factory [파이프라인](concepts-pipelines-activities.md)의 HDInsight 스트리밍 작업은 [사용자 고유](compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](transform-data.md) 문서에서 작성합니다.

Azure Data Factory를 처음 접하는 경우 [Azure Data Factory 소개](introduction.md)를 읽고 이 문서를 읽기 전에 [자습서: 데이터 변환](tutorial-transform-data-spark-powershell.md)을 수행하세요. 

## <a name="json-sample"></a>JSON 샘플
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
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
| 형식              | Hadoop 스트리밍 작업의 경우 작업 유형은 HDInsightStreaming입니다. | 예      |
| linkedServiceName | Data Factory에서 연결된 서비스로 등록된 HDInsight 클러스터에 대한 참조입니다. 이 연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요. | 예      |
| mapper            | mapper 실행 파일의 이름을 지정합니다. | 예      |
| reducer           | reducer 실행 파일의 이름을 지정합니다. | 예      |
| combiner          | combiner 실행 파일의 이름을 지정합니다. | 아닙니다.       |
| fileLinkedService | 실행할 Mapper, Combiner 및 Reducer 프로그램을 저장하는 데 사용되는 Azure Storage 연결된 서비스에 대한 참조입니다. 이 연결된 서비스를 지정하지 않으면 HDInsight 연결된 서비스에 정의된 Azure Storage 연결된 서비스가 사용됩니다. | 아닙니다.       |
| filePath          | fileLinkedService에서 참조하는 Azure Storage에 저장된 Mapper, Combiner 및 Reducer 프로그램의 경로 배열을 제공합니다. 경로는 대/소문자를 구분합니다. | 예      |
| input             | Mapper에 대한 입력 파일의 WASB 경로를 지정합니다. | 예      |
| output            | Reducer에 대한 출력 파일의 WASB 경로를 지정합니다. | 예      |
| getDebugInfo      | scriptLinkedService에 지정되었거나 HDInsight 클러스터에 사용된 Azure Storage에 로그 파일을 언제 복사할지 지정합니다. 허용되는 값은 다음과 같습니다. 없음, 항상 또는 실패. 기본값: 없음. | 아닙니다.       |
| arguments         | Hadoop 작업에 대한 인수 배열을 지정합니다. 인수는 각 작업에 대한 명령줄 인수로 전달됩니다. | 아닙니다.       |
| defines           | Hive 스크립트 내에서 참조하기 위해 매개 변수를 키/값 쌍으로 지정합니다. | 아닙니다.       | 

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요. 

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch 실행 작업](transform-data-using-machine-learning.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
