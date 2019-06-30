---
title: Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환 | Microsoft Docs
description: Spark 작업을 사용하여 Azure Data Factory 파이프라인에서 Spark 프로그램을 실행함으로써 데이터를 변환하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: c493dbc99edc794dd5a261dfc004c2c8c1cb6d52
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312083"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory에서 Spark 작업을 사용하여 데이터 변환
> [!div class="op_single_selector" title1="사용 하는 Data Factory 서비스 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-spark.md)
> * [현재 버전](transform-data-using-spark.md)

Data Factory [파이프라인](concepts-pipelines-activities.md)에서 Spark 작업은 [사용자 고유](compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 클러스터에서 Spark 프로그램을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](transform-data.md) 문서에서 작성합니다. 주문형 Spark 연결 서비스를 사용하면 Data Factory에서는 적시에 데이터를 처리할 수 있도록 자동으로 Spark 클러스터를 만든 다음, 처리가 완료되면 클러스터를 삭제합니다. 


## <a name="spark-activity-properties"></a>Spark 활동 속성
Spark 작업의 샘플 JSON 정의는 다음과 같습니다.    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

다음 표에서는 JSON 정의에 사용하는 JSON 속성을 설명합니다.

| 자산              | 설명                              | 필수 |
| --------------------- | ---------------------------------------- | -------- |
| name                  | 파이프라인의 작업 이름입니다.    | 예      |
| description           | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.  | 아닙니다.       |
| 형식                  | Spark 작업의 경우 작업 유형은 HDInsightSpark입니다. | 예      |
| linkedServiceName     | Spark 프로그램이 실행되는 HDInsight Spark 연결된 서비스의 이름입니다. 이 연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요. | 예      |
| SparkJobLinkedService | Spark 작업 파일, 종속성 및 로그를 보유하는 Azure Storage 연결된 서비스입니다.  이 속성에 대한 값을 지정하지 않으면 HDInsight 클러스터와 연결된 저장소가 사용됩니다. 이 속성의 값은 Azure Storage 연결된 서비스만 될 수 있습니다. | 아닙니다.       |
| rootPath              | Spark 파일이 포함된 Azure Blob 컨테이너 및 폴더입니다. 파일 이름은 대/소문자를 구분합니다. 이 폴더의 구조에 대한 자세한 내용은 폴더 구조 섹션(다음 섹션)을 참조하세요. | 예      |
| entryFilePath         | Spark 코드/패키지의 루트 폴더에 대한 상대 경로입니다. 항목 파일은 Python 파일이나 .jar 파일이어야 합니다. | 예      |
| className             | 애플리케이션의 Java/Spark main 클래스      | 아닙니다.       |
| arguments             | Spark 프로그램에 대한 명령줄 인수 목록입니다. | 아닙니다.       |
| proxyUser             | Spark 프로그램 실행을 가장하는 사용자 계정 | 아닙니다.       |
| sparkConfig           | Spark 구성 속성에 대한 값을 지정합니다. 이는 다음 항목에 나와 있습니다. [Spark 구성 - 애플리케이션 속성](https://spark.apache.org/docs/latest/configuration.html#available-properties). | 아닙니다.       |
| getDebugInfo          | sparkJobLinkedService에 지정되었거나 HDInsight 클러스터에 사용된 Azure Storage에 Spark 로그 파일을 언제 복사할지 지정합니다. 허용되는 값은 다음과 같습니다. 없음, 항상 또는 실패. 기본값: 없음. | 아닙니다.       |

## <a name="folder-structure"></a>폴더 구조
Spark 작업은 Pig/Hive 작업보다 확장성이 뛰어납니다. Spark 작업의 경우 jar 패키지(java CLASSPATH에 배치), python 파일(PYTHONPATH에 배치) 및 기타 파일과 같은 여러 종속성을 제공할 수 있습니다.

HDInsight 연결된 서비스에서 참조하는 Azure Blob Storage에 다음 폴더 구조를 만듭니다. 그런 다음 종속 파일을 **entryFilePath**로 표시되는 루트 폴더의 해당 하위 폴더에 업로드합니다. 예를 들어 python 파일을 루트 폴더의 pyFiles 하위 폴더에, jar 파일을 jars 하위 폴더에 업로드합니다. 런타임 시, Data Factory 서비스는 Azure Blob Storage에서 다음 폴더 구조를 필요로 합니다.     

| path                  | 설명                              | 필수 | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(root)            | 저장소 연결된 서비스에서 Spark 작업의 루트 경로 | 예      | 폴더 |
| &lt;사용자 정의 &gt; | Spark 작업의 엔트리 파일을 가리키는 경로 | 예      | 파일   |
| ./jars                | 이 폴더 아래 모든 파일이 업로드되고 클러스터의 java classpath에 배치됨 | 아닙니다.       | 폴더 |
| ./pyFiles             | 이 폴더 아래 모든 파일이 업로드되고 클러스터의 PYTHONPATH에 배치됨 | 아닙니다.       | 폴더 |
| ./files               | 이 폴더 아래 모든 파일이 업로드되고 실행기 작업 디렉터리에 배치됨 | 아닙니다.       | 폴더 |
| ./archives            | 이 폴더 아래 모든 파일이 압축 해제됨 | 아닙니다.       | 폴더 |
| ./logs                | Spark 클러스터의 로그를 포함하는 폴더입니다. | 아닙니다.       | 폴더 |

HDInsight 연결된 서비스에서 참조하는 Azure Blob Storage에 두 개의 Spark 작업 파일이 포함된 스토리지에 대한 예는 다음과 같습니다.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요. 

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch 실행 작업](transform-data-using-machine-learning.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
