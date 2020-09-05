---
title: Apache Spark에 대한 데이터 처리 최적화 - Azure HDInsight
description: Azure HDInsight를 사용하여 Apache Spark에서 데이터를 처리하는 가장 효율적인 작업을 선택하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperfq1
ms.openlocfilehash: 3ed4f8d4d8ca0a68a4ccf01a38ae5f8e66cc26df
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757832"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Apache Spark에 대 한 데이터 처리 최적화

이 문서에서는 Azure HDInsight에서 최상의 성능을 위해 Apache Spark 클러스터의 구성을 최적화하는 방법을 설명합니다.

## <a name="overview"></a>개요

조인 또는 순서 섞기에서 작업 속도가 느린 경우 그 원인은 *데이터 기울이기* 때문일 수 있습니다. 데이터 기울이기는 작업 데이터에서 비대칭입니다. 예를 들어 맵 작업에는 20초가 소요될 수 있습니다. 그러나 데이터가 조인되거나 순서를 섞는 작업을 실행하는 데는 몇 시간이 걸립니다. 데이터 기울이기를 수정하려면 전체 키를 솔트하거나 일부 하위 키 집합에만 *격리된 솔트*를 사용해야 합니다. 격리된 솔트를 사용하는 경우 맵 조인에서 솔트된 하위 키 집합을 격리하려면 추가로 필터링해야 합니다. 또 다른 옵션은 버킷 열을 도입하고 버킷에 미리 집계하는 것입니다.

조인 유형에 따라 느린 조인이 발생할 수도 있습니다. 기본적으로 Spark는 `SortMerge` 조인 유형을 사용합니다. 이 유형의 조인은 대량 데이터 세트에 가장 적합합니다. 그러나 병합하기 전에 먼저 데이터의 왼쪽과 오른쪽을 정렬해야 하기 때문에 계산 비용이 많이 듭니다.

`Broadcast` 조인은 더 작은 데이터 집합이나 조인의 한 쪽이 다른 쪽보다 훨씬 작은 경우에 가장 적합합니다. 이 유형의 조인은 모든 실행자에게 한 쪽을 브로드캐스트하므로 일반적으로 브로드캐스트에 더 많은 메모리가 필요합니다.

`spark.sql.autoBroadcastJoinThreshold`를 설정하여 구성에서 조인 유형을 변경하거나 데이터 프레임 API(`dataframe.join(broadcast(df2))`)를 사용하여 조인 힌트를 설정할 수 있습니다.

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

버킷 테이블을 사용하는 경우 세 번째 조인 유형인 `Merge` 조인이 있습니다. 데이터 세트가 올바르게 미리 분할되고 미리 정렬되어 있으면 `SortMerge` 조인에서 비용이 많이 드는 정렬 단계를 건너뛸 수 있습니다.

조인 순서는 더 복잡한 쿼리에서 특히 중요합니다. 가장 많이 선택하는 조인으로 시작합니다. 또한 가능한 경우 집계 후 행 수가 늘어나는 조인을 이동합니다.

Cartesian 조인의 병렬 처리를 관리하기 위해 중첩 구조, 창 작업을 추가하면 Spark 작업에서 하나 이상의 단계를 건너뛸 수 있습니다.

## <a name="optimize-job-execution"></a>작업 실행 최적화

* 예를 들어 데이터를 두 번 사용한 다음 캐시하는 경우와 같이 필요에 따라 캐시합니다.
* 모든 실행기에 변수를 브로드캐스트합니다. 변수는 한 번만 직렬화되므로 빨리 조회할 수 있습니다.
* 드라이버의 스레드 풀을 사용하면 많은 작업이 더 빨라집니다.

성능 문제에 대해 정기적으로 실행 중인 작업을 모니터링합니다. 특정 문제에 대한 자세한 통찰력이 필요한 경우 다음과 같은 성능 프로파일링 도구 중 하나를 고려합니다.

* [Intel PAL 도구](https://github.com/intel-hadoop/PAT)는 CPU, 스토리지 및 네트워크 대역폭 사용률을 모니터링합니다.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html)은 Spark 및 실행기 코드를 프로파일링합니다.

Spark 2.x 쿼리 성능의 핵심은 텅스텐 엔진으로, 전단계 코드 생성에 따라 달라집니다. 경우에 따라 전단계 코드 생성이 사용하지 않도록 설정될 수 있습니다. 예를 들어 집계식에서 변경이 불가능한 유형(`string`)을 사용하면 `HashAggregate` 대신 `SortAggregate`이 표시됩니다. 예를 들어 성능을 향상시키려면 다음을 시도한 다음 코드 생성을 다시 사용하도록 설정합니다.

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>다음 단계

* [Apache Spark에 대한 데이터 스토리지 최적화](optimize-data-storage.md)
* [Apache Spark에 대한 메모리 사용량 최적화](optimize-memory-usage.md)
* [Apache Spark에 대한 클러스터 구성 최적화](optimize-cluster-configuration.md)
