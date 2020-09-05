---
title: Apache Spark에 대한 데이터 스토리지 최적화 - Azure HDInsight
description: Azure HDInsight에서 Apache Spark와 함께 사용할 데이터 스토리지를 최적화하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperfq1
ms.openlocfilehash: 092757728e791f60616d9dceca43e109e7f0019e
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757815"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Apache Spark에 대 한 데이터 저장소 최적화

이 문서에서는 Azure HDInsight에서 효율적인 Apache Spark 작업 실행을 위해 데이터 스토리지를 최적화하는 전략을 설명합니다.

## <a name="overview"></a>개요

Spark는 csv, json, xml, parquet, orc, avro 등의 여러 가지 형식을 지원합니다. Spark는 외부 데이터 소스를 사용하여 더 많은 형식을 지원하도록 확장될 수 있습니다. 자세한 내용은 [Apache Spark 패키지](https://spark-packages.org)를 참조하세요.

성능에 가장 적합한 형식은 *snappy 압축*을 사용하는 parquet으로, Spark 2.x의 기본값입니다. Parquet은 데이터를 열 형식으로 저장하며 Spark에서 매우 최적화되어 있습니다.

## <a name="choose-data-abstraction"></a>데이터 추상화 선택

초기 Spark 버전에서는 RDD를 사용하여 데이터를 추상화하며, Spark 1.3과 1.6에서는 각각 데이터 프레임과 데이터 세트가 도입되었습니다. 다음 상대적인 장점을 고려합니다.

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
    * 새 사용자 지정 RDD를 빌드할 필요가 없는 한 RDD를 사용할 필요가 없습니다.
    * Catalyst를 통해 쿼리 최적화 안 함
    * 전체 단계 코드 생성 안 함
    * 높은 GC 오버헤드
    * Spark 1.x 레거시 API 사용 필수

## <a name="select-default-storage"></a>기본 스토리지 선택

새 Spark 클러스터를 만들 때 Azure Blob Storage 또는 Azure Data Lake Storage를 클러스터의 기본 스토리지로 선택할 수 있습니다. 두 옵션 모두 임시 클러스터를 위한 장기 스토리지의 이점을 제공합니다. 따라서 클러스터를 삭제할 때 데이터가 자동으로 삭제되지 않습니다. 임시 클러스터를 다시 만들고 해당 데이터에 계속 액세스할 수 있습니다.

| 저장소 유형 | 파일 시스템 | 속도 | 임시 | 사용 사례 |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:** //url/ | **Standard** | 예 | 임시 클러스터 |
| Azure Blob Storage(보안) | **wasbs:** //url/ | **Standard** | 예 | 임시 클러스터 |
| Azure Data Lake Storage Gen 2| **abfs:** //url/ | **보다 빠름** | 예 | 임시 클러스터 |
| Azure Data Lake Storage Gen 1| **adl:** //url/ | **보다 빠름** | 예 | 임시 클러스터 |
| 로컬 HDFS | **hdfs:** //url/ | **가장 빠름** | 예 | 대화형 24/7 클러스터 |

스토리지 옵션에 대한 자세한 설명은 [Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교](../hdinsight-hadoop-compare-storage-options.md)를 참조하세요.

## <a name="use-the-cache"></a>캐시 사용

Spark는 `.persist()`, `.cache()`, `CACHE TABLE`과 같은 다양한 방법을 통해 사용할 수 있는 자체 기본 캐싱 메커니즘을 제공합니다. 이 기본 캐싱은 작은 데이터 세트와 중간 결과를 캐시해야 하는 ETL 파이프라인에서 효과적입니다. 그러나 현재 Spark 기본 캐싱은 캐시된 테이블이 분할 데이터를 유지하지 않으므로 분할에서 잘 작동하지 않습니다. 보다 일반적이며 신뢰할 수 있는 캐싱 기술은 *스토리지 계층 캐싱*입니다.

* 기본 Spark 캐싱(권장하지 않음)
    * 작은 데이터 세트에 적합
    * 분할에서는 작동하지 않으며 향후 Spark 릴리스에서 변경될 수 있습니다.

* 스토리지 수준 캐싱(권장)
    * [IO 캐시](apache-spark-improve-performance-iocache.md) 기능을 사용하여 HDInsight에서 구현할 수 있습니다.
    * 메모리 내 캐싱 및 SSD 캐싱 사용

* 로컬 HDFS(권장)
    * `hdfs://mycluster` 경로
    * SSD 캐싱 사용
    * 클러스터를 삭제하면 캐시된 데이터가 손실되므로 캐시를 다시 빌드해야 함

## <a name="optimize-data-serialization"></a>데이터 serialization 최적화

Spark 작업은 분산되므로 최상의 성능을 위해서는 적합한 데이터 serialization이 중요합니다.  Spark에는 두 가지 serialization 옵션이 있습니다.

* Java serialization이 기본값입니다.
* `Kryo` serialization은 최신 형식으로 Java보다 빠르고 간력한 serialization이 가능합니다.  `Kryo`는 프로그램에 클래스를 등록해야 하며 아직 모든 직렬화 가능 유형을 지원하지는 않습니다.

## <a name="use-bucketing"></a>버킷팅 사용

버킷팅은 데이터 분할과 유사합니다. 그러나 각 버킷은 단 하나의 값이 아닌 열 값 세트를 보유할 수 있습니다. 이 메서드는 제품 식별자와 같은 값이 많은(수백만 개 이상) 분할에 적합합니다. 버킷은 행의 버킷 키를 해시하여 결정됩니다. 버킷팅 테이블은 버킷 및 정렬 방법에 대한 메타데이터를 저장하기 때문에 고유한 최적화를 제공합니다.

다음은 일부 고급 버킷팅 기능입니다.

* 버킷팅 메타 정보를 기반으로 쿼리 최적화
* 최적화된 집계
* 최적화된 조인

분할과 버킷팅을 동시에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Apache Spark에 대한 데이터 처리 최적화](optimize-cluster-configuration.md)
* [Apache Spark에 대한 메모리 사용량 최적화](optimize-memory-usage.md)
* [Apache Spark에 대한 클러스터 구성 최적화](optimize-cluster-configuration.md)
