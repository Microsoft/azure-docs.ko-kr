---
title: Azure HDInsight의 Spark에서 Jupyter와 함께 사용자 지정 Maven 패키지 사용
description: HDInsight Spark 클러스터에서 사용할 수 있는 Jupyter 노트북을 사용자 지정 Maven 패키지를 사용하도록 구성하는 방법에 대한 단계별 지침입니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: hrasheed
ms.openlocfilehash: 6af25b95aa3a38c4a2e9f3bd8147604dccae0abb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124179"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook과 함께 외부 패키지 사용
> [!div class="op_single_selector"]
> * [셀 매직 사용](apache-spark-jupyter-notebook-use-external-packages.md)
> * [스크립트 작업 사용](apache-spark-python-package-installation.md)

클러스터에 기본적으로 포함되지 않는 외부의 커뮤니티 제공 Apache **maven** 패키지를 사용하도록 HDInsight의 Apache Spark 클러스터에서 [Jupyter Notebook](https://jupyter.org/)을 구성하는 방법을 알아봅니다. 

사용할 수 있는 패키지의 전체 목록은 [Maven 리포지토리](https://search.maven.org/) 를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 커뮤니티 제공 패키지의 전체 목록은 [Spark 패키지](https://spark-packages.org/)에서 사용할 수 있습니다.

이 문서에서는 Jupyter 노트북에서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건
다음이 있어야 합니다.

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter 노트북에서 외부 패키지 사용
1. [Azure Portal](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터에 대한 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.   

1. Spark 클러스터 블레이드에서 **빠른 연결**을 클릭한 다음 **클러스터 대시보드** 블레이드에서 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

    > [!NOTE]  
    > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter Notebook에 접근할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. 새 Notebook을 만듭니다. **새로 만들기**를 클릭한 후 **Spark**를 클릭합니다.
   
    ![새 Jupyter 노트북 만들기](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "새 Jupyter 노트북 만들기")

1. 새 노트북이 만들어지고 Untitled.pynb 이름으로 열립니다. 맨 위에서 노트북 이름을 클릭하고 식별하기 쉬운 이름을 입력합니다.
   
    ![노트북 이름 제공](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "노트북 이름 제공")

1. `%%configure` Magic을 사용하여 외부 패키지를 사용하도록 Notebook을 구성합니다. 외부 패키지를 사용하는 Notebook에서 `%%configure` Magic을 호출해야 합니다. 이렇게 하면 커널은 세션이 시작되기 전에 패키지를 사용하도록 구성됩니다.

    >[!IMPORTANT]  
    >첫 번째 셀에서 커널을 구성하는 것을 잊은 경우 `%%configure`과 `-f` 매개 변수를 사용할 수 있지만 세션이 다시 시작되고 모든 진행률이 손실됩니다.

    | HDInsight 버전 | 명령 |
    |-------------------|---------|
    |HDInsight 3.3 및 HDInsight 3.4용 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | HDInsight 3.5 및 HDInsight 3.6용 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. 위의 코드 조각에는 Maven Center Repository의 외부 패키지에 대한 Maven 좌표가 필요합니다. 이 코드 조각에서 `com.databricks:spark-csv_2.10:1.4.0` 는 **spark-csv** 패키지에 대한 Maven 좌표입니다. 패키지의 좌표를 생성하는 방법은 다음과 같습니다.
   
    a. Maven Repository에서 패키지를 찾습니다. 이 자습서에서는 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)를 사용합니다.
   
    b. 해당 리포지토리에서 **GroupId**, **ArtifactId** 및 **Version** 값을 수집합니다. 수집하는 값이 클러스터와 일치하는지 확인합니다. 이 경우에는 Scala 2.10 및 Spark 1.4.0 패키지를 사용하고 있지만 클러스터의 해당 Scala 또는 Spark 버전에 대해 다른 버전을 선택해야 할 수 있습니다. Spark Jupyter 커널에서 또는 Spark 제출 시 `scala.util.Properties.versionString`을 실행하여 클러스터에서 Scala 버전을 찾을 수 있습니다. Jupyter Notebook에서 `sc.version`을 실행하여 클러스터에서 Spark 버전을 찾을 수 있습니다.
   
    ![Jupyter Notebook에서 외부 패키지 사용](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Jupyter Notebook에서 외부 패키지 사용")
   
    다. 콜론(**:**)으로 구분된 세 개의 값을 연결합니다.
   
        com.databricks:spark-csv_2.10:1.4.0

1. `%%configure` Magic을 사용하여 코드 셀을 실행합니다. 이렇게 하면 제공된 패키지를 사용하도록 기본 Livy 세션이 구성됩니다. 이제 아래와 같이 노트북의 다음 셀에서 패키지를 사용할 수 있습니다.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    HDInsight 3.6의 경우 다음 코드 조각을 사용해야 합니다.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. 아래와 같이 코드 조각을 실행하여 이전 단계에서 만든 데이터 프레임의 데이터를 볼 수 있습니다.
   
        df.show()
   
        df.select("Time").count()

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

* [HDInsight Linux의 Apache Spark 클러스터에서 Jupyter 노트북과 함께 외부 python 패키지 사용](apache-spark-python-package-installation.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 애플리케이션 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
