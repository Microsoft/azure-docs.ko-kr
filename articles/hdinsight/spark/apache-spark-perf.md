---
title: 성능을 위한 Spark 작업 최적화 - Azure HDInsight
description: 최상의 Spark 클러스터 성능을 얻기 위한 일반적인 전략을 보여 줍니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: dfbbdf3f2414f7b00445b271b667ad761ba93df0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098882"
---
# <a name="optimize-apache-spark-jobs"></a>Apache Spark 작업 최적화

특정 워크로드에 대해 [Apache Spark](https://spark.apache.org/) 클러스터 구성을 최적화하는 방법에 대해 알아봅니다.  가장 일반적인 문제는 부적절한 구성(특히 크기가 틀린 실행기), 장기 실행 작업, 카티전 작업으로 이어지는 작업으로 인한 메모리 압력입니다. 적절한 캐싱을 사용하고 [데이터 기울이기](#optimize-joins-and-shuffles)를 허용하여 작업 속도를 높일 수 있습니다. 최고의 성능을 얻기 위해 장기 실행 및 리소스 소모 Spark 작업 실행을 모니터링하고 검토합니다.

다음 섹션에서는 일반적인 Spark 작업 최적화 및 권장 사항에 대해 설명합니다.

## <a name="choose-the-data-abstraction"></a>데이터 추상화 선택

Spark 버전 Rdd를 사용 하 여 데이터를 Spark 1.3을 추상화 하 고 1.6 Dataframe 및 데이터 집합을 도입 각각 했습니다. 다음 상대적인 장점을 고려합니다.

* **데이터 프레임**
    * 대부분의 상황에서 최선의 선택
    * Catalyst를 통해 쿼리 최적화 제공
    * 전체 단계 코드 생성
    * 직접 메모리 액세스
    * 낮은 GC(가비지 수집) 오버헤드
    * 컴파일 시간 검사나 도메인 개체 프로그래밍이 없으므로 데이터 세트처럼 개발자에게 친숙하지 않음
* **데이터 세트**
    * 성능에 미치는 영향이 허용되는 복잡한 ETL 파이프라인에서 우수함
    * 성능에 미치는 영향이 큰 집계에서는 좋지 않음
    * Catalyst를 통해 쿼리 최적화 제공
    * 도메인 개체 프로그래밍 및 컴파일 시간 검사 기능을 제공하므로 개발자에게 친숙함
    * serialization/deserialization 오버헤드 추가
    * 높은 GC 오버헤드
    * 전체 단계 코드 생성 중단
* **RDD**
    * 새 사용자 지정 RDD를 빌드할 필요가 없는 경우 RDD를 사용할 필요가 없습니다.
    * Catalyst를 통해 쿼리 최적화 안 함
    * 전체 단계 코드 생성 안 함
    * 높은 GC 오버헤드
    * Spark 1.x 레거시 API 사용 필수

## <a name="use-optimal-data-format"></a>최적의 데이터 형식 사용

Spark는 csv, json, xml, parquet, orc, avro 등의 여러 가지 형식을 지원합니다. Spark는 외부 데이터 소스를 사용하여 더 많은 형식을 지원하도록 확장될 수 있습니다. 자세한 내용은 [Apache Spark 패키지](https://spark-packages.org)를 참조하세요.

성능에 가장 적합한 형식은 *snappy 압축*을 사용하는 parquet으로, Spark 2.x의 기본값입니다. Parquet은 데이터를 열 형식으로 저장하며 Spark에서 매우 최적화되어 있습니다.

## <a name="select-default-storage"></a>기본 저장소 선택

새 Spark 클러스터를 만들 때 Azure Blob Storage 또는 Azure Data Lake Storage를 클러스터의 기본 스토리지로 선택할 수 있습니다. 두 옵션 모두 임시 클러스터의 장기간 저장소의 이점을 제공하므로 클러스터를 삭제할 때 데이터가 자동으로 삭제되지 않습니다. 임시 클러스터를 다시 만들고 해당 데이터에 계속 액세스할 수 있습니다.

| 저장소 유형 | 파일 시스템 | 속도 | 임시 | 사용 사례 |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb[s]:**//url/ | **Standard** | 예 | 임시 클러스터 |
| Azure Data Lake Storage Gen 2| **abfs[s]:**//url/ | **보다 빠름** | 예 | 임시 클러스터 |
| Azure Data Lake Storage Gen 1| **adl:**//url/ | **보다 빠름** | 예 | 임시 클러스터 |
| 로컬 HDFS | **hdfs:**//url/ | **가장 빠름** | 아닙니다. | 대화형 24/7 클러스터 |

## <a name="use-the-cache"></a>캐시 사용

Spark는 `.persist()`, `.cache()`, `CACHE TABLE`과 같은 다양한 방법을 통해 사용할 수 있는 자체 기본 캐싱 메커니즘을 제공합니다. 이 기본 캐싱은 중간 결과를 캐시해야 하는 ETL 파이프라인뿐만 아니라 작은 데이터 집합에서도 효과적입니다. 그러나 현재 Spark 기본 캐싱은 캐시된 테이블이 분할 데이터를 유지하지 않으므로 분할에서 잘 작동하지 않습니다. 보다 일반적이며 신뢰할 수 있는 캐싱 기술은 *저장소 계층 캐싱*입니다.

* 기본 Spark 캐싱(권장하지 않음)
    * 작은 데이터 세트에 적합
    * 분할에서는 작동하지 않으며 향후 Spark 릴리스에서 변경될 수 있음

* 저장소 수준 캐싱(권장)
    * [Alluxio](https://www.alluxio.org/)를 사용하여 구현될 수 있음
    * 메모리 내 캐싱 및 SSD 캐싱 사용

* 로컬 HDFS(권장)
    * `hdfs://mycluster` 경로
    * SSD 캐싱 사용
    * 클러스터를 삭제하면 캐시된 데이터가 손실되므로 캐시를 다시 빌드해야 함

## <a name="use-memory-efficiently"></a>메모리를 효율적으로 사용

Spark는 데이터를 메모리에 두고 작업하기 때문에 Spark 작업 실행을 최적화하려면 메모리 리소스 관리가 중요합니다.  클러스터 메모리를 효율적으로 사용하기 위해 적용할 수 있는 여러 가지 기술이 있습니다.

* 더 작은 데이터 파티션을 선호하고 분할 전략에서 데이터 크기, 유형 및 배포를 고려합니다.
* 기본 Java serialization보다 더 새롭고 효율적인 [Kryo 데이터 serialization](https://github.com/EsotericSoftware/kryo)을 고려합니다.
* YARN은 배치별로 `spark-submit`를 구분하므로 YARN을 사용하는 것이 좋습니다.
* Spark 구성 설정을 모니터링하고 조정합니다.

참조용으로 Spark 메모리 구조와 일부 핵심 실행기 메모리 매개 변수가 다음 이미지에 표시됩니다.

### <a name="spark-memory-considerations"></a>Spark 메모리 고려 사항

[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)을 사용하는 경우 YARN은 각 Spark 노드에서 모든 컨테이너에서 사용되는 메모리의 최대 합계를 제어합니다.  다음 다이어그램에서는 주요 개체 및 해당 관계를 보여 줍니다.

![YARN Spark 메모리 관리](./media/apache-spark-perf/yarn-spark-memory.png)

'메모리 부족' 메시지를 해결하려면 다음을 시도합니다.

* DAG 관리 순서 섞기를 검토합니다. 맵 쪽에서 원본 데이터를 줄이거나 사전 분할(또는 버킷화)하여 줄이고 단일 순서 섞기로 최대화하여 전송된 데이터 양을 줄입니다.
* 집계, 창 작업 및 기타 함수를 제공하지만 메모리 제한은 없는 `GroupByKey`보다 고정된 메모리 제한이 있는 `ReduceByKey`을 선호합니다.
* 드라이버에서 모든 작업을 수행하는 `Reduce`보다 실행기 또는 파티션에서 더 많은 작업을 수행하는 `TreeReduce`를 선호합니다.
* 하위 수준의 RDD 개체보다 데이터 프레임을 활용합니다.
* "상위 N개", 다양한 집계 또는 창 작업 같이 작업을 캡슐화하는 ComplexTypes를 만듭니다.

## <a name="optimize-data-serialization"></a>데이터 serialization 최적화

Spark 작업은 분산되므로 최상의 성능을 위해서는 적합한 데이터 serialization이 중요합니다.  Spark에는 두 가지 serialization 옵션이 있습니다.

* Java serialization이 기본값입니다.
* Kryo serialization은 최신 형식으로 Java serialization보다 더 빠르고 간결합니다.  Kryo는 프로그램에 클래스를 등록해야 하며 아직 모든 직렬화 가능 유형을 지원하지는 않습니다.

## <a name="use-bucketing"></a>버킷팅 사용

버킷팅은 데이터 분할과 유사하지만 각 버킷은 단 하나의 값이 아닌 일련의 열 값 집합을 가질 수 있습니다. 버킷팅은 제품 식별자와 같은 값이 많은(수백만 개 이상) 분할에 적합합니다. 버킷은 행의 버킷 키를 해시하여 결정됩니다. 버킷팅 테이블은 버킷 및 정렬 방법에 대한 메타데이터를 저장하기 때문에 고유한 최적화를 제공합니다.

다음은 일부 고급 버킷팅 기능입니다.

* 버킷팅 메타 정보를 기반으로 쿼리 최적화
* 최적화된 집계
* 최적화된 조인

분할과 버킷팅을 동시에 사용할 수 있습니다.

## <a name="optimize-joins-and-shuffles"></a>조인 및 순서 섞기 최적화

조인 또는 순서 섞기에서 작업 속도가 느린 경우 그 원인은 *데이터 기울이기* 때문일 수 있으며 이는 작업 데이터의 비대칭입니다. 예를 들어 맵 작업에는 20초 정도 걸릴 수 있지만 데이터가 조인되거나 순서를 섞는 작업을 실행하는 데는 몇 시간 정도 걸릴 수 있습니다.   데이터 기울이기를 수정하려면 전체 키를 솔트하거나 일부 하위 키 집합에만 *격리된 솔트*를 사용해야 합니다.  격리된 솔트를 사용하는 경우 맵 조인에서 솔트된 하위 키 집합을 격리하려면 추가로 필터링해야 합니다. 또 다른 옵션은 버킷 열을 도입하고 버킷에 미리 집계하는 것입니다.

조인 유형에 따라 느린 조인이 발생할 수도 있습니다. 기본적으로 Spark는 `SortMerge` 조인 유형을 사용합니다. 이 유형의 조인은 큰 데이터 집합에 가장 적합하지만 병합하기 전에 먼저 데이터의 왼쪽과 오른쪽을 정렬해야 하기 때문에 계산을 많이 해야 합니다.

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

병렬 처리를 관리하기 위해 특히 카티전 조인의 경우 중첩 구조, 창 작업을 추가하면 Spark 작업에서 하나 이상의 단계를 건너뛸 수 있습니다.

## <a name="customize-cluster-configuration"></a>클러스터 구성 사용자 지정

Spark 클러스터 워크로드에 따라 기본이 아닌 Spark 구성을 사용하여 보다 최적화된 Spark 작업이 실행되도록 할 수 있습니다.  샘플 워크로드로 벤치마크 테스트를 수행하여 기본이 아닌 클러스터 구성의 유효성을 검사합니다.

다음은 조정할 수 있는 몇 가지 공통 매개 변수입니다.

* `--num-executors`에서 실행기의 적절한 수를 설정합니다.
* `--executor-cores`에서 각 실행기의 코어 수를 설정합니다. 일반적으로 다른 프로세스가 사용 가능한 메모리 중 일부를 소비하기 때문에 중간 규모의 실행기를 사용해야 합니다.
* `--executor-memory`에서 각 실행기의 메모리 크기를 설정하며 이를 통해 YARN에서 힙 크기를 제어합니다. 실행 오버헤드를 위해 약간의 메모리를 유지해야 합니다.

### <a name="select-the-correct-executor-size"></a>올바른 실행기 크기 선택

실행기 구성을 결정할 때 Java GC(가비지 수집) 오버헤드를 고려합니다.

* 실행기 크기를 줄이는 요소:
    1. GC 오버헤드를 10% 미만으로 유지하려면 힙 크기를 32GB 미만으로 줄입니다.
    2. GC 오버헤드를 10% 미만으로 유지하려면 코어 수를 줄입니다.

* 실행기 크기를 늘리는 요소:
    1. 실행기 간 통신 오버헤드를 줄입니다.
    2. 대규모 클러스터(100개가 넘는 실행기)에서 실행기(N2) 간의 열린 연결 수를 줄입니다.
    3. 메모리를 많이 사용하는 작업에 적합하도록 힙 크기를 늘립니다.
    4. 선택 사항: 실행기당 메모리 오버헤드를 줄입니다.
    5. 선택 사항: CPU를 초과 구독하여 사용률 및 동시성을 늘립니다.

실행기 크기를 선택할 때 일반적 규칙은 다음과 같습니다.
    
1. 실행기당 30GB로 시작하고 사용 가능한 컴퓨터 코어를 배포합니다.
2. 대규모 클러스터(100개가 넘는 실행기)에 대해서는 실행기 코어 수를 늘립니다.
3. 평가판 실행과 GC 오버헤드와 같은 이전 요인을 모두 고려하여 크기를 늘리거나 줄입니다.

동시 쿼리를 실행할 때는 다음 사항을 고려합니다.

1. 실행기당 30GB와 모든 컴퓨터 코어를 사용하여 시작합니다.
2. CPU를 초과 구독하여(약 30%의 대기 시간 개선) 여러 병렬 Spark 애플리케이션을 만듭니다.
3. 병렬 애플리케이션에서 쿼리를 배포합니다.
4. 평가판 실행과 GC 오버헤드와 같은 이전 요인을 모두 고려하여 크기를 늘리거나 줄입니다.

타임라인 보기, SQL 그래프, 작업 통계 등을 확인하여 이상값 또는 다른 성능 문제의 쿼리 성능을 모니터링합니다. 경우에 따라 하나 또는 몇 개의 실행기가 다른 실행기보다 느린 경우 작업을 실행하는 데 훨씬 많은 시간이 소요될 수 있습니다. 이러한 현상은 대규모 클러스터(30개가 넘는 노드)에서 자주 발생합니다. 이 경우 스케줄러가 느린 작업을 보정할 수 있도록 작업을 더 많은 수로 나눕니다. 예를 들어 애플리케이션에서 실행기 코어의 수보다 최소한 두 배의 작업이 있어야 합니다. `conf: spark.speculation = true`를 사용하면 작업을 추측에 근거하여 실행해 볼 수 있습니다.

## <a name="optimize-job-execution"></a>작업 실행 최적화

* 예를 들어 데이터를 두 번 사용한 다음 캐시하는 경우와 같이 필요에 따라 캐시합니다.
* 모든 실행기에 변수를 브로드캐스트합니다. 변수는 한 번만 직렬화되므로 빨리 조회할 수 있습니다.
* 드라이버의 스레드 풀을 사용하면 많은 작업이 더 빨라집니다.

성능 문제에 대해 정기적으로 실행 중인 작업을 모니터링합니다. 특정 문제에 대한 자세한 통찰력이 필요한 경우 다음과 같은 성능 프로파일링 도구 중 하나를 고려합니다.

* [Intel PAL 도구](https://github.com/intel-hadoop/PAT)는 CPU, 저장소 및 네트워크 대역폭 사용률을 모니터링합니다.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html)은 Spark 및 실행기 코드를 프로파일링합니다.

Spark 2.x 쿼리 성능의 핵심은 텅스텐 엔진으로, 전단계 코드 생성에 따라 달라집니다. 경우에 따라 전단계 코드 생성이 사용하지 않도록 설정될 수 있습니다. 예를 들어 집계식에서 변경이 불가능한 유형(`string`)을 사용하면 `HashAggregate` 대신 `SortAggregate`이 표시됩니다. 예를 들어 성능을 향상시키려면 다음을 시도한 다음 코드 생성을 다시 사용하도록 설정합니다.

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight에서 실행 중인 Apache Spark 작업 디버그](apache-spark-job-debugging.md)
* [HDInsight에서 Apache Spark 클러스터용 리소스 관리](apache-spark-resource-manager.md)
* [Apache Spark REST API를 사용하여 Apache Spark 클러스터에 원격 작업 제출](apache-spark-livy-rest-interface.md)
* [Apache Spark 조정](https://spark.apache.org/docs/latest/tuning.html)
* [작동하도록 Apache Spark 작업을 실제로 조정하는 방법](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo Serialization](https://github.com/EsotericSoftware/kryo)
