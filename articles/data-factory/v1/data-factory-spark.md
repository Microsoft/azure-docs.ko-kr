---
title: Azure Data Factory에서 Spark 프로그램 호출 | Microsoft Docs
description: MapReduce 작업을 사용하여 Azure Data Factory에서 Spark 프로그램을 호출하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 95c49eec6964984894f75ecd0a9e50c9c947683b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61257645"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Azure Data Factory 파이프라인에서 Spark 프로그램 호출

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 작업](data-factory-hive-activity.md)
> * [Pig 작업](data-factory-pig-activity.md)
> * [MapReduce 작업](data-factory-map-reduce.md)
> * [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
> * [Spark 작업](data-factory-spark.md)
> * [Machine Learning Batch 실행 작업](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 업데이트 리소스 작업](data-factory-azure-ml-update-resource-activity.md)
> * [저장 프로시저 작업](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 활동](data-factory-usql-activity.md)
> * [.NET 사용자 지정 작업](data-factory-use-custom-activities.md)

> [!NOTE]
> 이 문서는 일반 공급되는 Azure Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용하는 경우, [Data Factory에서 Apache Spark 작업을 사용하여 데이터 변환](../transform-data-using-spark.md)을 참조하세요.

## <a name="introduction"></a>소개
Spark 작업은 Data Factory에서 지원하는 [데이터 변환 작업](data-factory-data-transformation-activities.md) 중 하나입니다. 이 작업은 Azure HDInsight의 Spark 클러스터에서 지정된 Spark 프로그램을 실행합니다. 

> [!IMPORTANT]
> - Spark 작업은 Azure Data Lake Store를 기본 저장소로 사용하는 HDInsight Spark 클러스터를 지원하지 않습니다.
> - Spark 작업은 사용자 고유의 기존 HDInsight Spark 클러스터만 지원합니다. 주문형 HDInsight 연결된 서비스는 지원하지 않습니다.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>연습: Spark 활동이 있는 파이프라인 만들기
Spark 작업이 포함된 데이터 팩터리 파이프라인을 만드는 일반적인 단계는 다음과 같습니다. 

* 데이터 팩터리를 만듭니다.
* Azure Storage 연결된 서비스를 만들어 HDInsight Spark 클러스터와 연결된 스토리지를 데이터 팩터리에 연결합니다.
* HDInsight 연결된 서비스를 만들어 HDInsight의 Spark 클러스터를 데이터 팩터리에 연결합니다.
* Storage 연결된 서비스를 참조하는 데이터 세트를 만듭니다. 현재 생성 중인 출력이 없더라도 작업의 출력 데이터 세트를 지정해야 합니다. 
* 만든 HDInsight 연결된 서비스를 참조하는 Spark 작업이 있는 파이프라인을 만듭니다. 이 작업은 이전 단계에서 출력 데이터 세트로 만든 데이터 세트를 통해 구성됩니다. 출력 데이터 세트는 일정(매시간, 매일)을 구동하는 것입니다. 따라서 작업에서 실제로 출력을 생성하지 않더라도 출력 데이터 세트를 지정해야 합니다.

### <a name="prerequisites"></a>필수 조건
1. [저장소 계정 만들기](../../storage/common/storage-quickstart-create-account.md)의 지침에 따라 범용 저장소 계정을 만듭니다.

1. [HDInsight에서 Spark 클러스터 만들기](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)의 지침에 따라 HDInsight에서 Spark 클러스터를 만듭니다. 1단계에서 만든 저장소 계정을 이 클러스터와 연결합니다.

1. [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)에 있는 Python 스크립트 파일 **test.py**를 다운로드하고 검토합니다.

1. Blob Storage의 **adfspark** 컨테이너에 있는 **test.py**를 **pyFiles** 폴더로 업로드합니다. 컨테이너와 폴더가 없으면 만듭니다.

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.
데이터 팩터리를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **새로 만들기** > **데이터 + 분석** > **Data Factory**를 차례로 선택합니다.

1. **새 데이터 팩터리** 블레이드의 **이름** 아래에서 **SparkDF**를 입력합니다.

   > [!IMPORTANT]
   > Azure Data Factory 이름은 전역적으로 고유해야 합니다. "SparkDF 데이터 팩터리 이름은 사용할 수 없습니다"라는 오류 메시지가 표시되면 데이터 팩터리의 이름을 변경합니다. 예를 들어 yournameSparkDFdate를 사용하고 데이터 팩터리를 다시 만듭니다. 명명 규칙에 대한 자세한 내용은 [Data Factory: 명명 규칙](data-factory-naming-rules.md)을 참조하세요.

1. **구독** 아래에서 데이터 팩터리를 만들려는 Azure 구독을 선택합니다.

1. 기존 리소스 그룹을 선택하거나 Azure 리소스 그룹을 만듭니다.

1. **대시보드에 고정** 확인란을 선택합니다.

1. **만들기**를 선택합니다.

   > [!IMPORTANT]
   > Data Factory 인스턴스를 만들려면 구독/리소스 그룹 수준에서 [Data Factory 참가자](../../role-based-access-control/built-in-roles.md#data-factory-contributor) 역할의 구성원이어야 합니다.

1. Azure Portal의 대시보드에서 만들어질 때 데이터 팩터리가 표시됩니다.

1. 데이터 팩터리가 만들어지면 **데이터 팩터리** 페이지가 표시되며, 여기에 데이터 팩터리의 내용이 표시됩니다. **데이터 팩터리** 페이지가 표시되지 않으면 대시보드에서 데이터 팩터리의 타일을 선택합니다.

    ![데이터 팩터리 블레이드](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>연결된 서비스 만들기
이 단계에서는 두 개의 연결된 서비스를 만듭니다. 한 서비스는 Spark 클러스터를 데이터 팩터리에 연결하고, 다른 서비스는 저장소를 데이터 팩터리에 연결합니다. 

#### <a name="create-a-storage-linked-service"></a>Storage 연결된 서비스 만들기
이 단계에서는 저장소 계정을 데이터 팩터리에 연결합니다. 이 연습의 뒷부분에서 만드는 데이터 세트는 이 연결된 서비스를 참조합니다. 다음 단계에서 정의하는 HDInsight 연결된 서비스는 이 연결된 서비스를 참조합니다. 

1. **Data Factory** 블레이드에서 **작성자 및 배포**를 선택합니다. 데이터 팩터리 편집기가 표시됩니다.

1. **새 데이터 저장소**를 선택하고 **Azure Storage**를 선택합니다.

   ![새 데이터 저장소](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Storage 연결된 서비스를 만드는 데 사용하는 JSON 스크립트가 편집기에 표시됩니다.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. **계정 이름** 및 **계정 키**를 저장소 계정의 이름 및 액세스 키로 바꿉니다. 저장소 액세스 키를 가져오는 방법을 알아보려면 [저장소 계정 관리](../../storage/common/storage-account-manage.md#access-keys)에서 저장소 액세스 키를 보고, 복사하고, 다시 생성하는 방법을 참조하세요.

1. 연결된 서비스를 배포하려면 명령 모음에서 **배포**를 선택합니다. 연결된 서비스가 성공적으로 배포되면 후 초안 1 창이 사라집니다. 왼쪽의 트리 뷰에서 **AzureStorageLinkedService**가 표시됩니다.

#### <a name="create-an-hdinsight-linked-service"></a>HDInsight 연결된 서비스 만들기
이 단계에서는 HDInsight 연결된 서비스를 만들어 HDInsight Spark 클러스터를 데이터 팩터리에 연결합니다. HDInsight 클러스터는 이 샘플에서 파이프라인의 Spark 활동에 지정된 Spark 프로그램을 실행하는 데 사용됩니다. 

1. 데이터 팩터리 편집기에서 **자세히** > **새 계산** > **HDInsight 클러스터**를 차례로 선택합니다.

    ![HDInsight 연결된 서비스 만들기](media/data-factory-spark/new-hdinsight-linked-service.png)

1. 다음 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다. JSON 편집기에서 다음 단계를 수행합니다.

    a. HDInsight Spark 클러스터에 대한 URI를 지정합니다. 예: `https://<sparkclustername>.azurehdinsight.net/`

    b. Spark 클러스터에 액세스할 수 있는 사용자의 이름을 지정합니다.

    다. 사용자의 암호를 지정합니다.

    d. HDInsight Spark 클러스터와 연결되는 Storage 연결된 서비스를 지정합니다. 이 예제에서는 AzureStorageLinkedService입니다.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Spark 작업은 Azure Data Lake Store를 기본 저장소로 사용하는 HDInsight Spark 클러스터를 지원하지 않습니다.
    > - Spark 작업은 사용자 고유의 기존 HDInsight Spark 클러스터만 지원합니다. 주문형 HDInsight 연결된 서비스는 지원하지 않습니다.

    HDInsight 연결된 서비스에 대한 자세한 내용은 [HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)를 참조하세요.

1. 연결된 서비스를 배포하려면 명령 모음에서 **배포**를 선택합니다. 

### <a name="create-the-output-dataset"></a>출력 데이터 세트 만들기
출력 데이터 세트는 일정(매시간, 매일)을 구동하는 것입니다. 따라서 작업에서 출력을 생성하지 않더라도 파이프라인의 Spark 작업에 대한 출력 데이터 세트를 지정해야 합니다. 활동에 대한 입력 데이터 세트를 지정하는 것은 선택 사항입니다.

1. 데이터 팩터리 편집기에서 **자세히** > **새 데이터 집합** > **Azure Blob Storage**를 차례로 선택합니다.

1. 다음 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다. JSON 조각은 **OutputDataset**이라는 데이터 세트를 정의합니다. 또한 결과를 **adfspark**라는 Blob 컨테이너와 **pyFiles/output**이라는 폴더에 저장하도록 지정합니다. 앞에서 언급한 대로 이 데이터 세트는 더미 데이터 세트입니다. 이 예제의 Spark 프로그램은 출력을 생성하지 않습니다. **availability** 섹션에서는 출력 데이터 세트를 매일 생성하도록 지정하고 있습니다. 

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. 데이터 세트를 배포하려면 명령 모음에서 **배포**를 선택합니다.


### <a name="create-a-pipeline"></a>파이프라인을 만듭니다.
이 단계에서는 HDInsightSpark 작업이 있는 파이프라인을 만듭니다. 현재 출력 데이터 세트가 일정을 결정하므로 작업이 출력을 생성하지 않는 경우에도 출력 데이터 세트를 만들어야 합니다. 활동이 입력을 가져오지 않으면 입력 데이터 세트 만들기를 건너뛸 수 있습니다. 따라서 이 예제에서는 입력 데이터 세트를 지정하지 않습니다.

1. 데이터 팩터리 편집기에서 **자세히** > **새 파이프라인**을 차례로 선택합니다.

1. Draft-1 창의 스크립트를 다음 스크립트로 바꿉니다.

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    다음 사항에 유의하세요.

    a. **type** 속성은 **HDInsightSpark**로 설정됩니다.

    b. **rootPath** 속성은 **adfspark\\pyFiles**로 설정되며, 여기서 adfspark는 Blob 컨테이너이고, pyFiles는 해당 컨테이너의 파일 폴더입니다. 이 예제에서 Blob Storage는 Spark 클러스터와 연결된 스토리지입니다. 파일은 다른 저장소 계정에 업로드할 수 있습니다. 이렇게 하면 Storage 연결된 서비스를 만들어 해당 스토리지 계정을 데이터 팩터리에 연결합니다. 그런 다음 연결된 서비스의 이름을 **sparkJobLinkedService** 속성의 값으로 지정합니다. 이 속성과 Spark 작업에서 지원하는 다른 속성에 대한 자세한 내용은 [Spark 작업 속성](#spark-activity-properties)을 참조하세요.

    다. **entryFilePath** 속성은 Python 파일인 **test.py**로 설정됩니다.

    d. **getDebugInfo** 속성은 **Always**로 설정되며, 이는 로그 파일이 항상 생성(성공 또는 실패)된다는 것을 의미합니다.

    > [!IMPORTANT]
    > 문제를 해결하지 않는 한 프로덕션 환경에서는 이 속성을 `Always`로 설정하지 않는 것이 좋습니다.

    e. **outputs** 섹션에는 하나의 출력 데이터 세트만 있습니다. Spark 프로그램에서 출력을 생성하지 않더라도 출력 데이터 세트를 지정해야 합니다. 출력 데이터 세트는 파이프라인에 대한 일정(매시간, 매일)을 구동합니다. 

    Spark 작업에서 지원하는 속성에 대한 자세한 내용은 [Spark 작업 속성](#spark-activity-properties) 섹션을 참조하세요.

1. 파이프라인을 배포하려면 명령 모음에서 **배포**를 선택합니다.

### <a name="monitor-a-pipeline"></a>파이프라인 모니터링
1. **데이터 팩터리** 블레이드에서 **모니터링 및 관리**를 선택하여 다른 탭에서 모니터링 애플리케이션을 시작합니다.

    ![타일 모니터링 및 관리](media/data-factory-spark/monitor-and-manage-tile.png)

1. 위쪽의 **시작 시간** 필터를 **2/1/2017**로 변경하고 **적용**을 선택합니다.

1. 파이프라인의 시작 시간(2017년 2월 1일)과 종료 시간(2017년 2월 2일) 사이에는 하루가 있기 때문에 하나의 활동 창만 표시됩니다. 데이터 조각이 **준비** 상태인지 확인합니다.

    ![파이프라인 모니터링](media/data-factory-spark/monitor-and-manage-app.png)

1. **활동 창** 목록에서 하나의 활동 실행을 선택하여 세부 정보를 확인합니다. 오류가 있으면 오른쪽 창에 오류에 대한 세부 정보가 표시됩니다.

### <a name="verify-the-results"></a>결과 확인

1. [이 웹 사이트](https://CLUSTERNAME.azurehdinsight.net/jupyter)로 이동하여 HDInsight Spark 클러스터에 대한 Jupyter Notebook을 시작합니다. 또한 HDInsight Spark 클러스터에 대한 클러스터 대시보드를 연 다음, Jupyter Notebook을 시작할 수도 있습니다.

1. **새로 만들기** > **PySpark**를 차례로 선택하여 새 노트북을 시작합니다.

    ![새 Jupyter 노트북](media/data-factory-spark/jupyter-new-book.png)

1. 텍스트를 복사하여 붙여넣고 두 번째 명령문의 끝에서 Shift+Enter를 눌러 다음 명령을 실행합니다.

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. hvac 테이블의 데이터가 표시되는지 확인합니다. 

    ![Jupyter 쿼리 결과](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
자세한 지침은 [Spark SQL 쿼리 실행](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 섹션을 참조하세요. 

### <a name="troubleshooting"></a>문제 해결
getDebugInfo를 **Always**로 설정했으므로 Blob 컨테이너의 pyFiles 폴더에 log 하위 폴더가 표시됩니다. log 폴더의 로그 파일에서 추가 정보를 제공합니다. 이 로그 파일은 오류가 발생할 때 특히 유용합니다. 프로덕션 환경에서는 이 오류를 **실패**로 설정할 수 있습니다.

문제를 추가로 해결하려면 다음 단계를 수행합니다.


1. `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`로 이동합니다.

    ![YARN UI 애플리케이션](media/data-factory-spark/yarnui-application.png)

1. 실행 시도 중 하나에 대한 **로그**를 선택합니다.

    ![애플리케이션 페이지](media/data-factory-spark/yarn-applications.png)

1. 로그 페이지에 다음과 같은 추가 오류 정보가 표시됩니다.

    ![로그 오류](media/data-factory-spark/yarnui-application-error.png)

다음 섹션에서는 데이터 팩터리에서 Spark 클러스터 및 Spark 작업을 사용하는 데이터 팩터리 엔터티에 대한 정보를 제공합니다.

## <a name="spark-activity-properties"></a>Spark 활동 속성
다음은 Spark 작업이 있는 파이프라인의 JSON 정의 샘플입니다. 

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

다음 표에서는 JSON 정의에서 사용되는 JSON 속성을 설명합니다.

| 자산 | 설명 | 필수 |
| -------- | ----------- | -------- |
| 이름 | 파이프라인의 작업 이름입니다. | 예 |
| description | 작업에서 수행하는 동작을 설명하는 텍스트입니다. | 아닙니다. |
| 형식 | 이 속성은 HDInsightSpark로 설정해야 합니다. | 예 |
| linkedServiceName | Spark 프로그램이 실행되는 HDInsight 연결된 서비스의 이름입니다. | 예 |
| rootPath | Spark 파일이 포함된 Blob 컨테이너 및 폴더입니다. 파일 이름은 대/소문자를 구분합니다. | 예 |
| entryFilePath | Spark 코드/패키지의 루트 폴더에 대한 상대 경로입니다. | 예 |
| className | 애플리케이션의 Java/Spark main 클래스입니다. | 아닙니다. |
| arguments | Spark 프로그램에 대한 명령줄 인수 목록입니다. | 아닙니다. |
| proxyUser | Spark 프로그램을 실행하기 위해 가장하는 사용자 계정입니다. | 아닙니다. |
| sparkConfig | Spark 구성 속성에 대한 값을 지정합니다. 이는 [Spark 구성: 애플리케이션 속성](https://spark.apache.org/docs/latest/configuration.html#available-properties)에 나열되어 있습니다. | 아닙니다. |
| getDebugInfo | HDInsight 클러스터에서 사용되거나 sparkJobLinkedService에서 지정된 저장소에 Spark 로그 파일을 복사하는 시기를 지정합니다. 허용되는 값은 None, Always 또는 Failure입니다. 기본값은 None입니다. | 아닙니다. |
| sparkJobLinkedService | Spark 작업 파일, 종속성 및 로그를 보유하는 Storage 연결된 서비스입니다. 이 속성에 대한 값을 지정하지 않으면 HDInsight 클러스터와 연결된 저장소가 사용됩니다. | 아닙니다. |

## <a name="folder-structure"></a>폴더 구조
Pig 및 Hive 작업에서 수행하는 것처럼 Spark 작업은 인라인 스크립트를 지원하지 않습니다. Spark 작업은 Pig/Hive 작업보다 확장성이 뛰어납니다. Spark 작업의 경우 jar 패키지(java CLASSPATH에 배치), Python 파일(PYTHONPATH에 배치) 및 기타 파일과 같은 여러 종속성을 제공할 수 있습니다.

HDInsight 연결된 서비스에서 참조하는 Blob Storage에 다음 폴더 구조를 만듭니다. 그런 다음 **entryFilePath**로 표시된 루트 폴더의 해당 하위 폴더에 종속 파일을 업로드합니다. 예를 들어 Python 파일은 루트 폴더의 pyFiles 하위 폴더에 업로드하고, jar 파일은 jars 하위 폴더에 업로드합니다. 런타임 시, Data Factory 서비스에 필요한 Blob Storage의 폴더 구조는 다음과 같습니다. 

| path | 설명 | 필수 | Type |
| ---- | ----------- | -------- | ---- |
| . | 저장소 연결된 서비스의 Spark 작업에 대한 루트 경로입니다. | 예 | 폴더 |
| &lt;사용자 정의 &gt; | Spark 작업의 입력 파일을 가리키는 경로입니다. | 예 | 파일 |
| ./jars | 이 폴더 아래의 모든 파일이 업로드되고, 클러스터의 Java classpath에 배치됩니다. | 아닙니다. | 폴더 |
| ./pyFiles | 이 폴더 아래의 모든 파일이 업로드되고, 클러스터의 PYTHONPATH에 배치됩니다. | 아닙니다. | 폴더 |
| ./files | 이 폴더 아래의 모든 파일이 업로드되고, 실행기 작업 디렉터리에 배치됩니다. | 아닙니다. | 폴더 |
| ./archives | 이 폴더 아래의 모든 파일이 압축 해제됩니다. | 아닙니다. | 폴더 |
| ./logs | Spark 클러스터의 로그가 저장되는 폴더| 아닙니다. | 폴더 |

HDInsight 연결된 서비스에서 참조하는 Blob Storage에 있는 두 개의 Spark 작업 파일이 포함된 스토리지에 대한 예제는 다음과 같습니다.

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
