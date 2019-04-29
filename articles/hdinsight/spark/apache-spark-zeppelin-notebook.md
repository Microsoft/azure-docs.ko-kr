---
title: Azure HDInsight에서 Apache Spark 클러스터와 함께 Zeppelin Notebook 사용
description: Azure HDInsight에서 Apache Spark 클러스터와 함께 Zeppelin Notebook을 사용하는 방법에 대한 단계별 지침입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 9fc18b11b24791c1e154d89d757408da4ab20539
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107554"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용

HDInsight Spark 클러스터에는 [Apache Spark](https://spark.apache.org/) 작업을 실행하는 데 사용할 수 있는 [Apache Zeppelin](https://zeppelin.apache.org/) Notebook이 포함되어 있습니다. 이 문서에서는 HDInsight 클러스터에서 Zeppelin Notebook을 사용하는 방법에 대해 알아 봅니다.

**필수 조건:**

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* 클러스터 기본 저장소에 대 한 URI 체계입니다. 이 됩니다 `wasb://` Azure Blob Storage에 대 한 `abfs://` 에 대 한 Azure Data Lake 저장소 Gen2 또는 `adl://` Azure 데이터 레이크 저장소 Gen1에 대 한 합니다. URI는 것에 대해 Blob Storage 또는 Data Lake 저장소 Gen2 전송 보안을 사용 하는 경우 `wasbs://` 또는 `abfss://`, 각각.  도 참조 하세요 [Azure Storage에서 보안 전송 필요](../../storage/common/storage-require-secure-transfer.md) 자세한 내용은 합니다.

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin Notebook 시작

1. Spark 클러스터에서 **개요**를 선택 **Zeppelin notebook** 에서 **클러스터 대시보드**합니다. 클러스터에 대 한 관리자 자격 증명을 입력 합니다.  

   > [!NOTE]  
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Zeppelin Notebook에 도달할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 새 Notebook을 만듭니다. 헤더 창에서로 이동 **Notebook** > **새 메모 만들기**합니다.

    ![새 Zeppelin Notebook 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "새 Zeppelin Notebook 만들기")

    노트북의 이름을 입력 하 고 선택 **노트 만들기**합니다.

3. Notebook 헤더에 연결 된 상태가 되는지 확인 합니다. 오른쪽 위 모서리에 녹색 점으로 표시됩니다.

    ![Zeppelin Notebook 상태](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook 상태")

4. 샘플 데이터를 임시 테이블에 로드합니다. HDInsight에서 샘플 데이터 파일에서 Spark 클러스터를 만들면 `hvac.csv`에 연결 된 저장소 계정에 복사 됩니다 `\HdiSamples\SensorSampleData\hvac`합니다.

    새 노트북에 기본적으로 만들어지는 빈 단락에 다음 코드 조각을 붙여넣습니다.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    **Shift + Enter**를 누르거나 단락에 대한 **재생** 단추를 클릭하여 코드 조각을 실행합니다. 단락의 오른쪽 모서리 상태가 준비, 보류 중, 실행 중, 완료 순서로 진행됩니다. 출력은 같은 단락 하단에 표시됩니다. 스크린샷은 다음과 같습니다.

    ![원시 데이터에서 임시 테이블 만들기](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "원시 데이터에서 임시 테이블 만들기")

    또한 각 단락에 제목을 제공할 수도 있습니다. 단락의 오른쪽 모서리에서 선택 합니다 **설정을** 아이콘 (sprocket)를 선택한 후 **제목만**합니다.  

    > [!NOTE]  
    > %spark2 인터프리터는 모든 HDInsight 버전의 Zeppelin 노트북에서 지원되지 않으며 %sh 인터프리터는 HDInsight 4.0 이상에서는 지원되지 않습니다.

5. 이제 Spark SQL 문을 실행할 수 있습니다는 `hvac` 테이블입니다. 새 단락에 다음 쿼리를 붙여넣습니다. 이 쿼리는 지정된 날짜에서 각 건물에 대한 건물 ID와 대상 및 실제 온도 간의 차이를 검색합니다. **Shift + Enter**를 누릅니다.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    **%sql** 문의 처음 부분은 Notebook에 Livy Scala 인터프리터를 사용하도록 지시합니다.

6. 선택 된 **가로 막대형 차트** 표시를 변경 하려면 아이콘입니다.  **설정**를 선택한 후 나타나는 **가로 막대형 차트**를 선택할 수 있습니다 **키**, 및 **값**.  다음 스크린샷은 출력을 보여 줍니다.

    ![Notebook을 사용하여 Spark SQL 문 실행](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Notebook을 사용하여 Spark SQL 문 실행")

7. 또한 쿼리에 변수를 사용하여 Spark SQL 문을 실행할 수도 있습니다. 다음 코드 조각에서는 변수를 정의 하는 방법을 보여 줍니다. `Temp`를 사용 하 여 쿼리 하려는 경우 가능한 값을 사용 하 여 쿼리에서 합니다. 쿼리를 처음 실행하면 드롭다운이 변수에 지정한 값으로 자동으로 채워집니다.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    새 단락에 이 코드 조각을 붙여넣고 **Shift + Enter**를 누릅니다. 선택한 **65** 에서 합니다 **Temp** 드롭다운 ist 합니다. 

8. 선택 된 **가로 막대형 차트** 표시를 변경 하려면 아이콘입니다.  선택한 **설정을** 다음과 같이 변경 하 고 있습니다.

   * **그룹:**  추가 **targettemp**합니다.  
   * **값:** 1. 제거할 **날짜**합니다.  2. Add **temp_diff**.  3.  집계를 변경 **합계** 하 **AVG**합니다.  

     다음 스크린샷은 출력을 보여 줍니다.

     ![Notebook을 사용하여 Spark SQL 문 실행](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Notebook을 사용하여 Spark SQL 문 실행")

9. Livy 인터프리터를 다시 시작하여 애플리케이션을 종료합니다. 이렇게 하려면 오른쪽 위 모서리에서 사용자 이름에서 로그인을 선택 하 여 인터프리터 설정을 연 선택한 후 **인터프리터**합니다.  

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

10. 스크롤하여 **livy**를 선택한 후 **다시 시작**합니다.  선택 **확인** 프롬프트.

    ![Livy 인터프리터 다시 시작](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin 인터프리터 다시 시작")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Notebook에서 외부 패키지 사용 방법
포함된-의-기본 클러스터에 없는 외부의 커뮤니티 제공 패키지를 사용 하도록 HDInsight의 Apache Spark 클러스터에서 Zeppelin notebook을 구성할 수 있습니다. 사용할 수 있는 패키지의 전체 목록은 [Maven 리포지토리](https://search.maven.org/) 를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 커뮤니티 제공 패키지의 전체 목록은 [Spark 패키지](https://spark-packages.org/)에서 사용할 수 있습니다.

이 문서에서는 Jupyter Notebook에서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하는 방법을 설명합니다.

1. 인터프리터 설정을 엽니다. 오른쪽 위 모서리에서 로그온 된 사용자 이름을 선택한 다음 선택 **인터프리터**합니다.

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

2. 스크롤하여 **livy**을 선택한 후 **편집**합니다.

    ![인터프리터 설정 변경](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "인터프리터 설정 변경")

3. 라는 새 키를 추가 `livy.spark.jars.packages`, 형식으로 해당 값을 설정 하 고 `group:id:version`입니다. 따라서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하려면 키 값을 `com.databricks:spark-csv_2.10:1.4.0`으로 설정해야 합니다.

    ![인터프리터 설정 변경](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "인터프리터 설정 변경")

    선택 **저장할** 한 다음 Livy 인터프리터 다시 시작 합니다.

4. 위에서 입력한 키 값에 도달하는 방법을 이해하려면 여기를 참조하세요.
   
    a. Maven Repository에서 패키지를 찾습니다. 이 자습서에서는 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)를 사용했습니다.
   
    b. 해당 리포지토리에서 **GroupId**, **ArtifactId** 및 **Version** 값을 수집합니다.
   
    ![Jupyter Notebook에서 외부 패키지 사용](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Jupyter Notebook에서 외부 패키지 사용")
   
    다. 콜론(**:**)으로 구분된 세 개의 값을 연결합니다.
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 저장 위치
Zeppelin Notebook은 클러스터 헤드 노드에 저장됩니다. 따라서 클러스터를 삭제하면 Notebook도 삭제됩니다. 나중에 다른 클러스터에서 사용하기 위해 Notebook을 유지하려면 작업 실행을 완료 한 후 Notebook을 내보내야 합니다. Notebook을 내보내려면 선택 합니다 **내보내기** 아이콘 아래 이미지에 표시 된 대로 합니다.

![Notebook 다운로드](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Notebook 다운로드")

이렇게 하면 Notebook이 다운로드 위치에 JSON 파일로 저장됩니다.

## <a name="livy-session-management"></a>Livy 세션 관리
Zeppelin Notebook에서 첫 번째 코드 단락을 실행하면 HDInsight Spark 클러스터에 새로운 Livy 세션이 만들어집니다. 이 세션은 이후에 만드는 모든 Zeppelin Notebook에서 공유됩니다. 클러스터 재부팅 등 어떤 이유로 Livy 세션이 종료되면 Zeppelin Notebook에서 작업을 실행할 수 없습니다.

이 경우 Zeppelin Notebook에서 작업 실행을 시작하기 전에 다음 단계를 수행해야 합니다.  

1. Zeppelin Notebook에서 Livy 인터프리터를 다시 시작합니다. 이렇게 하려면 오른쪽 위 모서리에서 사용자 이름에서 로그인을 선택 하 여 인터프리터 설정을 연 다음 선택 **인터프리터**합니다.

    ![인터프리터 시작](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 출력")

2. 스크롤하여 **livy**을 선택한 후 **다시 시작**합니다.

    ![Livy 인터프리터 다시 시작](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin 인터프리터 다시 시작")

3. 기존 Zeppelin Notebook에서 코드 셀을 실행합니다. 이렇게 하면 HDInsight 클러스터에 새로운 Livy 세션이 만들어집니다.

## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark Scala 애플리케이션 생성 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
