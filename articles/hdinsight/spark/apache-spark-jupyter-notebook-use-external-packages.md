---
title: Spark에서 Jupyter와 함께 사용자 지정 Maven 패키지 사용 - Azure HDInsight
description: HDInsight Spark 클러스터에서 사용 가능한 Jupyter Notebook을 사용자 지정 Maven 패키지 사용이 가능하도록 구성하는 방법에 대한 단계별 설명.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 3ddfdfdfe10d5b6ea7c2d5cd99d325564163c0dd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866015"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook과 함께 외부 패키지 사용

클러스터에 기본적으로 포함되지 않는 외부의 커뮤니티에서 제공하는 Apache **maven** 패키지를 사용하도록 HDInsight의 Apache Spark 클러스터에서 [Jupyter Notebook](https://jupyter.org/)을 구성하는 방법을 알아봅니다.

사용할 수 있는 패키지의 전체 목록은 [Maven 리포지토리](https://search.maven.org/) 를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 커뮤니티 제공 패키지의 전체 목록은 [Spark 패키지](https://spark-packages.org/)에서 사용할 수 있습니다.

이 문서에서는 Jupyter Notebook에서 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 패키지를 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

* HDInsight의 Spark에서 Jupyter Notebook을 사용하는 방법 이해. 자세한 내용은 [HDInsight의 Apache Spark로 데이터 로드 및 쿼리 실행](./apache-spark-load-data-run-query.md)을 참조하세요.

* 클러스터 기본 스토리지에 대한 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme)입니다. Azure Storage는 `wasb://`, Azure Data Lake Storage Gen2는 `abfs://`, Azure Data Lake Storage Gen1은 `adl://`입니다. Azure Storage 또는 Data Lake Storage Gen2에 보안 전송을 사용하는 경우 URI는 각각 `wasbs://` 또는 `abfss://`가 됩니다. [보안 전송](../../storage/common/storage-require-secure-transfer.md)도 참조하세요.

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter 노트북에서 외부 패키지 사용

1. Apache Spark 클러스터의 이름인 `https://CLUSTERNAME.azurehdinsight.net/jupyter`곳`CLUSTERNAME`으로 이동합니다.

1. 새 Notebook을 만듭니다. **새로 만들기** 를 선택한 다음, **Spark** 를 선택합니다.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png " alt-text="새 Spark Jupyter Notebook 만들기" border="true":::

1. 새 노트북이 만들어지고 Untitled.pynb 이름으로 열립니다. 맨 위에서 노트북 이름을 클릭하고, 사용하기 편한 이름을 입력합니다.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png " alt-text="노트북에 대한 이름 제공" border="true":::

1. `%%configure` Magic을 사용하여 외부 패키지를 사용하도록 노트북을 구성합니다. 외부 패키지를 사용하는 Notebook에서 `%%configure` Magic을 호출해야 합니다. 이렇게 하면 커널은 세션이 시작되기 전에 패키지를 사용하도록 구성됩니다.

    >[!IMPORTANT]  
    >첫 번째 셀에서 커널을 구성하는 것을 잊은 경우 `%%configure`과 `-f` 매개 변수를 사용할 수 있지만 세션이 다시 시작되고 모든 진행률이 손실됩니다.

    | HDInsight 버전 | 명령 |
    |-------------------|---------|
    | HDInsight 3.5 및 HDInsight 3.6용 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |HDInsight 3.3 및 HDInsight 3.4용 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. 위의 코드 조각에는 Maven Center Repository의 외부 패키지에 대한 Maven 좌표가 필요합니다. 이 코드 조각에서 `com.databricks:spark-csv_2.11:1.5.0` 는 **spark-csv** 패키지에 대한 Maven 좌표입니다. 패키지의 좌표를 생성하는 방법은 다음과 같습니다.

    a. Maven Repository에서 패키지를 찾습니다. 이 문서에서는 [spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv)를 사용합니다.

    b. 해당 리포지토리에서 **GroupId**, **ArtifactId** 및 **Version** 값을 수집합니다. 수집하는 값이 클러스터와 일치하는지 확인합니다. 이 경우에는 Scala 2.11 및 Spark 1.5.0 패키지를 사용하고 있지만, 해당 클러스터에 적합한 다른 Scala 또는 Spark 버전을 선택해야 할 수 있습니다. Spark Jupyter 커널에서 또는 Spark 제출 시 `scala.util.Properties.versionString`을 실행하여 클러스터에서 Scala 버전을 찾을 수 있습니다. Jupyter Notebook에서 `sc.version`을 실행하여 클러스터에서의 Spark 버전을 찾을 수 있습니다.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png " alt-text="Jupyter Notebook에서 외부 패키지 사용" border="true":::

    다. 콜론(**:**)으로 구분된 세 개의 값을 연결합니다.

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. `%%configure` Magic을 사용하여 코드 셀을 실행합니다. 이렇게 하면 제공된 패키지를 사용하도록 기본 Livy 세션이 구성됩니다. 이제 아래와 같이 노트북의 다음 셀에서 패키지를 사용할 수 있습니다.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    HDInsight 3.4 이하의 경우 다음 코드 조각을 사용해야 합니다.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. 아래와 같이 코드 조각을 실행하여 이전 단계에서 만든 데이터 프레임의 데이터를 볼 수 있습니다.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>참고 항목

* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오

* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HVAC 데이터를 사용하여 건물 온도를 분석하는 데 HDInsight의 Spark 사용](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장

* [HDInsight Linux의 Apache Spark 클러스터에서 Jupyter Notebook과 함께 외부 python 패키지 사용](apache-spark-python-package-installation.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 애플리케이션 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리

* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
