---
title: Azure HDInsight에서 SparkCruise을 사용 하 여 Apache Spark 쿼리 속도 향상
description: SparkCruise 최적화 플랫폼을 사용 하 여 Apache Spark 쿼리의 효율성을 높이는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 1a73b4707f83d6a23dffc20d95aa7b8a0fa465b3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649060"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>Azure HDInsight의 SparkCruise

이 문서에서는 Apache Spark 계산을 자동으로 재사용 하 여 쿼리 효율성을 높이는 Azure HDInsight 기능 *SparkCruise*에 대해 설명 합니다.

## <a name="overview"></a>개요

Apache Spark와 같은 분석 플랫폼에서 실행 되는 쿼리는 작은 하위 쿼리를 포함 하는 쿼리 계획으로 분해 됩니다. 이러한 하위 쿼리는 여러 쿼리에 대해 쿼리 계획에서 반복적으로 표시 될 수 있습니다. 오류가 발생할 때마다 결과를 반환 하기 위해 다시 실행 됩니다. 그러나 동일한 쿼리를 다시 실행 하는 것은 비효율적 이며 불필요 한 계산 비용을 발생 시킬 수 있습니다.

*SparkCruise* 은 일반적인 계산을 다시 사용 하 여 전체 쿼리 실행 시간 및 데이터 전송 비용을 줄일 수 있는 워크 로드 최적화 플랫폼입니다. 플랫폼은 *구체화 된 뷰의*개념을 사용 합니다 .이는 결과가 미리 계산 된 형식으로 저장 되는 쿼리입니다. 결과를 다시 계산 하는 대신 나중에 쿼리 자체가 다시 표시 될 때 해당 결과를 다시 사용할 수 있습니다.

## <a name="setup-and-installation"></a>설정 및 설치

SparkCruise는 Spark 2.3 또는 2.4를 사용 하는 모든 HDInsight 4.0 클러스터에서 사용할 수 있습니다. SparkCruise 라이브러리 파일은 `/opt/peregrine/` HDInsight 클러스터의 디렉터리에 설치 됩니다. 제대로 작동 하려면 *SparkCruise* 에는 기본적으로 설정 되는 다음과 같은 구성 속성이 필요 합니다.

* `spark.sql.queryExecutionListeners` 는로 설정 되며이를 `com.microsoft.peregrine.spark.listeners.PlanLogListener` 통해 쿼리 계획의 로깅을 사용할 수 있습니다.
* `spark.sql.extensions` 는로 설정 되며,이를 `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` 통해 온라인 구체화 및 다시 사용에 대 한 최적화 프로그램 규칙을 사용할 수 있습니다.

## <a name="computation-reuse-in-spark-sql"></a>Spark SQL에서 계산 재사용

다음 샘플 시나리오에서는 *SparkCruise* 를 사용 하 여 Apache Spark 쿼리를 최적화 하는 방법을 보여 줍니다. 

1. Spark 클러스터의 헤드 노드로 SSH를 이동 합니다.
1. `spark-shell`을 입력합니다.
1. 클러스터의 샘플 데이터를 사용 하 여 몇 가지 기본 쿼리를 실행 하는 다음 코드 조각을 실행 합니다.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. *SparkCruise* 쿼리 분석 도구를 사용 하 여 Spark 응용 프로그램 로그에 저장 된 이전 쿼리의 쿼리 계획을 분석할 수 있습니다. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. 피드백 파일인 분석 프로세스의 출력을 표시 합니다. 이 피드백 파일에는 향후 Spark SQL 쿼리에 대 한 주석이 포함 되어 있습니다. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`analyze`명령은 쿼리 계획을 구문 분석 하 고 작업의 테이블 형식 표현을 만듭니다. 이 작업 테이블은 [HDInsight SparkCruise Samples](https://github.com/Azure-Samples/azure-sparkcruise-samples) 리포지토리에 포함 된 *WorkloadInsights* 노트북을 사용 하 여 쿼리할 수 있습니다. 그런 다음이 `views` 명령은 일반적인 하위 계획 식을 식별 하 고 향후 구체화 및 재사용을 위해 흥미로운 하위 계획 식을 선택 합니다. 출력은 향후 Spark SQL 쿼리에 대 한 주석을 포함 하는 피드백 파일입니다. 

`show`명령은 다음 텍스트와 같은 출력을 표시 합니다.

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

피드백 파일에는 형식의 항목이 포함 되어 있습니다 `subplan-identifier [Materialize|Reuse] input/path/to/action` . 이 예에는 두 개의 고유한 서명이 있습니다. 하나는 처음 두 개의 반복 되는 쿼리를 나타내고 두 번째는 마지막 두 쿼리의 필터 조건자를 나타냅니다. 이 피드백 파일을 사용 하 여 다시 제출 되는 다음 쿼리는 이제 자동으로 일반적인 하위 계획을 구체화 하 고 다시 사용 합니다. 

최적화를 테스트 하려면 다른 샘플 쿼리 집합을 실행 합니다.

1. `spark-shell`을 입력합니다.
1. 다음 코드 조각을 실행 합니다.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. 다시 사용 된 쿼리의 서명을 보려면 피드백 파일을 다시 확인 합니다.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`show`명령은 다음 텍스트와 유사한 출력을 제공 합니다.

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

쿼리의 리터럴 값이에서로 변경 되기는 하지만 `'11%'` `'12%'` , *SparkCruise* 는 리터럴 값 및 데이터 집합 버전의 진화와 같이 약간 변형 된 이전 쿼리를 새 쿼리에 계속 일치 시킬 수 있습니다. 쿼리가 크게 변경 된 경우에는 분석 도구를 다시 실행 하 여 다시 사용할 수 있는 새 쿼리를 식별할 수 있습니다.

내부적으로 *SparkCruise* 는 선택한 하위 계획을 해당 하위 계획을 포함 하는 첫 번째 쿼리에서 구체화 하기 위해 하위 쿼리를 트리거합니다. 나중에 쿼리를 다시 계산 하는 대신 구체화 된 하위 계획를 직접 읽을 수 있습니다. 이 워크 로드에서 하위 계획는 첫 번째 및 세 번째 쿼리로 온라인 방식으로 구체화 됩니다. 일반적인 하위 계획 구체화 된 후 쿼리의 계획 변경 내용을 볼 수 있습니다.

이후 쿼리를 다시 사용할 수 있는 네 개의 구체화 된 하위 식이 이제 표시 되는 것을 볼 수 있습니다.

## <a name="clean-up"></a>정리

피드백 파일, 구체화 된 하위 계획 및 쿼리 로그는 Spark 세션 간에 유지 됩니다. 이러한 파일을 제거 하려면 다음 명령을 실행 합니다.

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>다음 단계

* [워크 로드 Insights 노트북을 사용 하 여 SparkCruise의 이점 확인](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Azure HDInsight IO 캐시를 사용 하 여 Apache Spark 작업의 성능 향상](apache-spark-improve-performance-iocache.md)
* [HDInsight에서 Apache Spark 작업 최적화](./apache-spark-perf.md)
* [SparkCruise: Spark의 Handsfree 계산 재사용](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Azure HDInsight에 대 한 Apache Spark 지침](./spark-best-practices.md)
