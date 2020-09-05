---
title: 'Azure Data Factory에서 Spark를 사용하여 데이터 변환 '
description: 이 자습서에서는 Azure Data Factory에서 Spark 작업을 사용하여 데이터를 변환하는 단계별 지침을 제공합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 5b0bcdd66e17fb93a560b6073c13e3170e3ab37b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "81409255"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Azure Data Factory에서 Spark 작업을 사용하여 클라우드의 데이터 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Azure Portal을 사용하여 Azure Data Factory 파이프라인을 만듭니다. 이 파이프라인은 Spark 활동 및 주문형 HDInsight 연결된 서비스를 사용하여 데이터를 변환합니다. 

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * Spark 활동을 사용하는 파이프라인을 만듭니다.
> * 파이프라인 실행 트리거
> * 파이프라인 실행을 모니터링합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure Storage 계정**. Python 스크립트와 입력 파일을 만들고 Azure Storage에 업로드합니다. Spark 프로그램의 출력은 이 스토리지 계정에 저장됩니다. 주문형 Spark 클러스터는 기본 스토리지와 동일한 스토리지 계정을 사용합니다.  

> [!NOTE]
> HdInsight는 표준 계층을 포함하는 범용 스토리지 계정만 지원합니다. 계정이 프리미엄 또는 BLOB 전용 스토리지 계정인지 확인합니다.

* **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)의 지침을 따르세요.


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Blob Storage 계정에 Python 스크립트 업로드
1. 다음 내용이 포함된 **WordCount_Spark.py**라는 Python 파일을 만듭니다. 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. *&lt;storageAccountName&gt;* 을 Azure Storage 계정 이름으로 바꿉니다. 그런 다음 파일을 저장합니다. 
1. Azure Blob Storage에 아직 없는 경우 **adftutorial**이라는 컨테이너를 만듭니다. 
1. **spark**라는 폴더를 만듭니다.
1. **spark** 폴더 아래에 **script**라는 하위 폴더를 만듭니다. 
1. **script** 하위 폴더에 **WordCount_Spark.py** 파일을 업로드합니다. 


### <a name="upload-the-input-file"></a>입력 파일 업로드
1. 일부 텍스트가 포함된 **minecraftstory.txt**라는 파일을 만듭니다. Spark 프로그램은 이 텍스트의 단어 수를 계산합니다. 
1. **spark** 폴더에 **inputfiles**라는 하위 폴더를 만듭니다. 
1. **inputfiles** 하위 폴더에 **minecraftstory.txt** 파일을 업로드합니다. 

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **새로 만들기**를 선택하고 **데이터 + 분석**을 선택한 다음 **데이터 팩터리**를 선택합니다. 
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. **새 데이터 팩터리** 창에서 **이름** 아래에 **ADFTutorialDataFactory**를 입력합니다. 
      
   !["새 데이터 팩터리" 창](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 *전역적으로 고유*해야 합니다. 다음과 같은 오류가 표시되면 데이터 팩터리의 이름을 변경합니다. (예를 들어 **&lt;yourname&gt;ADFTutorialDataFactory** 사용). 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
   ![이름을 사용할 수 없는 경우 오류](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. **구독**에 대해 데이터 팩터리를 만들려는 위치에 Azure 구독을 선택합니다. 
1. **리소스 그룹**에 대해 다음 단계 중 하나를 사용합니다.
     
   - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
   - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
   이 빠른 시작의 일부 단계에서는 리소스 그룹에 **ADFTutorialResourceGroup**이라는 이름을 사용한다고 가정합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
1. **버전**에서 **V2**를 선택합니다.
1. **위치**에 데이터 팩터리의 위치를 선택합니다. 

   현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics**를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용하는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

1. **만들기**를 선택합니다.

1. 만들기가 완료되면 **데이터 팩터리** 페이지가 표시됩니다. **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 Data Factory UI 애플리케이션을 시작합니다.

    !["작성 및 모니터링" 타일이 있는 데이터 팩터리에 대한 홈페이지](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>연결된 서비스 만들기
이 섹션에서는 두 개의 연결된 서비스를 작성합니다. 
    
- Azure Storage 계정을 데이터 팩터리에 연결하는 **Azure Storage 연결된 서비스**. 이 스토리지는 주문형 HDInsight 클러스터에서 사용됩니다. 실행될 Spark 스크립트도 포함되어 있습니다. 
- **주문형 HDInsight 연결된 서비스**. Azure Data Factory는 HDInsight 클러스터를 자동으로 만들고 Spark 프로그램을 실행합니다. 그런 다음 클러스터가 미리 구성된 시간 동안 유휴 상태를 유지하면 HDInsight 클러스터를 삭제합니다. 

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기

1. **시작하기** 페이지에서 왼쪽 패널의 **편집** 탭으로 전환합니다. 

   !["시작하기" 페이지](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. 창의 아래쪽에서 **연결**, **+ 새로 만들기**를 차례로 클릭합니다. 

   ![새 연결 만들기에 대한 단추](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. **새 연결된 서비스** 창에서 **데이터 저장소** > **Azure Blob Storage**를 선택한 다음, **계속**을 선택합니다. 

   !["Azure Blob Storage" 타일 선택](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. **스토리지 계정 이름**의 목록에서 이름을 선택한 후 **저장**을 선택합니다. 

   ![스토리지 계정 이름을 지정하는 상자](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>주문형 HDInsight 연결된 서비스 만들기

1. **+ 새로 만들기** 단추를 다시 선택하여 또 하나의 연결된 서비스를 만듭니다. 
1. **새 연결된 서비스** 창에서 **Compute** > **Azure HDInsight**를 선택한 후 **계속**을 선택합니다. 

   !["Azure HDInsight" 타일 선택](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. **새 연결된 서비스** 창에서 다음 단계를 완료합니다. 

   a. **이름**에 **AzureHDInsightLinkedService**를 입력합니다.
   
   b. **유형**에 **주문형 HDInsight**가 선택되었는지 확인합니다.
   
   다. **Azure Storage 연결 서비스**에 대해 **AzureBlobStorage1**을 선택합니다. 이 연결된 서비스는 앞에서 먼저 만들었습니다. 다른 이름을 사용한 경우 여기에 올바른 이름을 지정합니다. 
   
   d. **클러스터 유형**에 **spark**를 선택합니다.
   
   e. **서비스 주체 ID**에는 HDInsight 클러스터를 만들 수 있는 권한이 있는 서비스 주체의 ID를 입력합니다. 
   
      이 서비스 주체는 클러스터를 만든 구독 또는 리소스 그룹의 참가자 역할의 구성원이어야 합니다. 자세한 내용은 [Azure Active Directory 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요. **서비스 주체 ID**는 ‘애플리케이션 ID’와 동일하고, **서비스 주체 키**는 ‘클라이언트 비밀’ 값과 동일합니다.  
   
   f. **서비스 주체 키**에 키를 입력합니다. 
   
   g. **리소스 그룹**에 데이터 팩터리를 만들 때 사용한 것과 동일한 리소스 그룹을 선택합니다. Spark 클러스터가 이 리소스 그룹에 만들어집니다. 
   
   h. **OS 유형**을 펼칩니다.
   
   i. **클러스터 사용자 이름**에 대한 이름을 입력합니다. 
   
   j. 사용자에 대한 **클러스터 암호**를 입력합니다. 
   
   k. **마침**을 선택합니다. 

   ![HDInsight 연결된 서비스 설정](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight는 지원하는 각 Azure 지역에서 사용할 수 있는 총 코어 수를 제한합니다. 주문형 HDInsight 연결된 서비스의 경우 HDInsight 클러스터는 기본 스토리지로 사용되는 Azure Storage와 동일한 위치에 만들어집니다. 클러스터를 성공적으로 만드는 데 충분한 코어 할당량이 있는지 확인합니다. 자세한 내용은 [Hadoop, Spark, Kafka 등으로 HDInsight에서 클러스터 설정](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요. 

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. **+** (더하기) 단추를 선택한 다음 메뉴에서 **파이프라인**을 선택합니다.

   ![새 파이프라인 만들기에 대한 단추](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. **활동** 도구 상자에서 **HDInsight**를 확장합니다. **활동** 도구 상자에서 **Spark** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

   ![Spark 활동 끌어서 놓기](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. 아래쪽의 **Spark** 활동 창에 대한 속성에서 다음 단계를 완료합니다. 

   a. **HDI 클러스터** 탭으로 전환합니다.
   
   b. 이전 절차에서 만든 **AzureHDInsightLinkedService**를 선택합니다. 
        
   ![HDInsight 연결된 서비스 지정](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. **스크립트/Jar** 탭으로 전환하고 다음 단계를 완료합니다. 

   a. **작업 연결 서비스**에 **AzureBlobStorage1**을 선택합니다.
   
   b. **스토리지 찾아보기**를 선택합니다.

   !["스크립트/Jar" 탭에서 Spark 스크립트 지정](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   다. **adftutorial/spark/script** 폴더로 이동한 후 **WordCount_Spark.py**를 선택한 후 **마침**을 선택합니다.      

1. 파이프라인에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사** 단추를 선택합니다. **>>** (오른쪽 화살표) 단추를 선택하여 유효성 검사 창을 닫습니다. 
    
   !["유효성 검사" 단추](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. **모두 게시**를 선택합니다. Data Factory UI는 엔터티(연결된 서비스 및 파이프라인)를 Azure Data Factory 서비스에 게시합니다. 
    
   !["모두 게시" 단추](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>파이프라인 실행 트리거
도구 모음에서 **트리거 추가**를 선택한 다음, **지금 트리거**를 선택합니다. 

!["트리거" 및 "지금 트리거" 단추](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. **모니터** 탭으로 전환합니다. 파이프라인 실행이 표시되는지 확인합니다. Spark 클러스터를 만드는 데 약 20분이 걸립니다. 
   
1. 주기적으로 **새로 고침**을 선택하여 파이프라인 실행 상태를 확인합니다. 

   !["새로 고침" 단추가 있는 파이프라인 실행 모니터링에 대한 탭](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기**를 선택합니다.

   ![파이프라인 실행 상태](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   위쪽의 **모든 파이프라인 실행** 링크를 선택하여 파이프라인 실행 보기로 다시 전환할 수 있습니다.

   !["활동 실행" 보기](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>출력 확인
출력 파일이 adftutorial 컨테이너의 spark/otuputfiles/wordcount 폴더에 만들어졌는지 확인합니다. 

![출력 파일의 위치](./media/tutorial-transform-data-spark-portal/verity-output.png)

파일에는 입력 텍스트 파일의 각 단어 및 해당 단어가 파일에 나타난 횟수가 있어야 합니다. 다음은 그 예입니다. 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인에서 Spark 활동 및 주문형 HDInsight 연결된 서비스를 사용하여 데이터를 변환합니다. 구체적으로 다음 작업 방법을 알아보았습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * Spark 활동을 사용하는 파이프라인을 만듭니다.
> * 파이프라인 실행 트리거
> * 파이프라인 실행을 모니터링합니다.

가상 네트워크에 있는 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요. 

> [!div class="nextstepaction"]
> [자습서: Azure Virtual Network에서 Hive를 사용하여 데이터 변환](tutorial-transform-data-hive-virtual-network-portal.md)





