---
title: Azure HDInsight에서 대화형 Spark 셸 사용
description: 대화형 Spark 셸은 한 번에 하나의 Spark 명령을 실행하고 결과를 보기 위한 읽기 실행 인쇄 프로세스를 제공합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162806"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spark 셸에서 Apache Spark 실행

대화형 [Apache Spark](https://spark.apache.org/) 셸은 한 번에 하나의 Spark 명령을 실행하고 결과를 보기 위한 REPL(읽기 실행 인쇄 반복) 환경을 제공합니다. 이 프로세스는 개발 및 디버깅에 유용합니다. Spark는 지원되는 각 언어, Scala, Python 및 R에 대한 하나의 셸을 제공합니다.

## <a name="run-an-apache-spark-shell"></a>Apache Spark 셸 실행

1. [Ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용 하 여 클러스터에 연결 합니다. CLUSTERNAME을 클러스터의 이름으로 바꿔서 아래 명령을 편집 하 고 명령을 입력 합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark는 Scala (spark-shell) 및 Python (pyspark)에 대 한 셸을 제공 합니다. SSH 세션에서 다음 명령 *중 하나* 를 입력 합니다.

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    선택적 구성을 사용 하려는 경우 먼저 [Apache Spark OutOfMemoryError 예외](./apache-spark-troubleshoot-outofmemory.md)를 검토 해야 합니다.

1. 몇 가지 기본 예제 명령입니다. 관련 언어를 선택 합니다.

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. CSV 파일을 쿼리 합니다. 참고 아래 언어는 `spark-shell` 및 `pyspark`에 대해 작동 합니다.

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. CSV 파일을 쿼리하고 결과를 변수에 저장 합니다.

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. 결과 표시:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. 끝내기

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession 및 SparkContext 인스턴스

기본적으로 Spark 셸을 실행할 때 SparkSession 및 SparkContext의 인스턴스는 자동으로 인스턴스화됩니다.

SparkSession 인스턴스에 액세스하려면 `spark`를 입력합니다. SparkContext 인스턴스에 액세스하려면 `sc`를 입력합니다.

## <a name="important-shell-parameters"></a>중요한 셸 매개 변수

Spark Shell 명령 (`spark-shell`또는 `pyspark`)은 많은 명령줄 매개 변수를 지원 합니다. 매개 변수의 전체 목록을 보려면 스위치 `--help`를 사용하여 Spark 셸을 시작합니다. 이러한 매개 변수 중 일부는 Spark 셸이 래핑하는 `spark-submit`에만 적용 될 수 있습니다.

| 스위치 | description | 예제 |
| --- | --- | --- |
| --master MASTER_URL | 마스터 URL을 지정합니다. HDInsight에서 이 값은 항상 `yarn`입니다. | `--master yarn`|
| --jars JAR_LIST | 드라이버 및 실행기 클래스 경로에서 포함하도록 쉼표로 구분된 로컬 jar의 목록입니다. HDInsight에서 이 목록은 Azure Storage 또는 Data Lake Storage에서 기본 파일 시스템에 대한 경로로 구성됩니다. | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | 드라이버 및 실행기 클래스 경로에서 포함하도록 쉼표로 구분된 jars의 maven 좌표의 목록입니다. 로컬 maven 리포지토리를 검색한 다음 maven 중앙을 검색한 후 `--repositories`로 지정된 모든 추가 원격 리포지토리를 검색합니다. 좌표 형식은 *그룹 ID*:*아티팩트 ID*:*버전*입니다. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Python의 경우 PYTHONPATH에 배치하도록 쉼표로 구분된 .zip, .egg 또는 .py 파일의 목록입니다. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure HDInsight의 Apache Spark 소개](apache-spark-overview.md)를 참조하세요.
- Spark 클러스터 및 SparkSQL을 사용하려면 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
- Spark로 스트리밍 데이터를 처리하는 애플리케이션을 작성하려면 [Apache Spark 구조적 스트리밍이란?](apache-spark-streaming-overview.md)을 참조하세요.
