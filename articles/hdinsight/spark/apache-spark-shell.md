---
title: Azure HDInsight에서 대화형 Spark 셸 사용
description: 대화형 Spark 셸은 한 번에 하나의 Spark 명령을 실행하고 결과를 보기 위한 읽기 실행 인쇄 프로세스를 제공합니다.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 9044ed3ad9cf9ffa2f54d130bb50b37df121b86f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116076"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Spark 셸에서 Apache Spark 실행

대화형 [Apache Spark](https://spark.apache.org/) 셸은 한 번에 하나의 Spark 명령을 실행하고 결과를 보기 위한 REPL(읽기 실행 인쇄 반복) 환경을 제공합니다. 이 프로세스는 개발 및 디버깅에 유용합니다. Spark는 지원되는 각 언어, Scala, Python 및 R에 대한 하나의 셸을 제공합니다.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>SSH를 사용하여 Apache Spark 셸로 가져오기

SSH를 사용해서 클러스터의 주 헤드 노드에 연결하여 HDInsight의 Apache Spark 셸에 액세스합니다.

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Azure Portal에서 클러스터에 대한 전체 SSH 명령을 얻을 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. HDInsight Spark 클러스터에 대한 창으로 이동합니다.
3. SSH(Secure Shell)를 선택합니다.

    ![Azure Portal의 HDInsight 창](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. 표시되는 SSH 명령을 복사하고 터미널에서 실행합니다.

    ![Azure Portal의 HDInsight SSH 창](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

SSH를 사용하여 HDInsight에 연결에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="run-an-apache-spark-shell"></a>Apache Spark 셸 실행

Spark는 Scala(spark 셸), Python(pyspark) 및 R(sparkR)에 대한 셸을 제공합니다. HDInsight 클러스터 헤드 노드의 SSH 세션에서 다음 명령 중 하나를 입력합니다.

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

이제 해당 언어로 Spark 명령을 입력할 수 있습니다.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession 및 SparkContext 인스턴스

기본적으로 Spark 셸을 실행할 때 SparkSession 및 SparkContext의 인스턴스는 자동으로 인스턴스화됩니다.

SparkSession 인스턴스에 액세스하려면 `spark`를 입력합니다. SparkContext 인스턴스에 액세스하려면 `sc`를 입력합니다.

## <a name="important-shell-parameters"></a>중요한 셸 매개 변수

Spark 셸 명령(`spark-shell`, `pyspark` 또는 `sparkR`)은 여러 명령줄 매개 변수를 지원합니다. 매개 변수의 전체 목록을 보려면 스위치 `--help`를 사용하여 Spark 셸을 시작합니다. 이러한 매개 변수 중 일부는 Spark 셸에서 래핑하는 `spark-submit`에만 적용될 수 있습니다.

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
