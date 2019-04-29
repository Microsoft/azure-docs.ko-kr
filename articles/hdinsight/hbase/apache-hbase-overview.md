---
title: Azure HDInsight의 HBase란?
description: Hadoop을 기반으로 하는 NoSQL 데이터베이스인 HDInsight의 Apache HBase를 소개합니다. 사용 사례에 대해 알아보고 HBase를 다른 Hadoop 클러스터와 비교합니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: d109a1a233aaaf1f3922561106caa15781e31e01
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114437"
---
# <a name="what-is-apache-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-apache-hadoop"></a>HDInsight의 Apache HBase: Apache Hadoop에 BigTable 같은 기능을 제공하는 NoSQL 데이터베이스
[Apache HBase](https://hbase.apache.org/)는 [Apache Hadoop](https://hadoop.apache.org/)을 기반으로 하고 [Google BigTable](https://cloud.google.com/bigtable/) 이후에 모델링된 오픈 소스 NoSQL 데이터베이스입니다. HBase는 열 패밀리로 구성된 스키마 없는 데이터베이스에서 구조화되지 않은/반구조화된 대량 데이터에 대해 임의 액세스 및 강력한 일관성을 제공합니다.

사용자 관점에서 볼 때 HBase는 데이터베이스와 유사합니다. 데이터는 테이블의 행과 열에 저장되고 행 내의 데이터는 열 제품군으로 그룹화됩니다. HBase는 사용 전에 열과 열에 저장되는 데이터 형식을 정의할 필요가 없다는 점에서 스키마 없는 데이터베이스입니다. 오픈 소스 코드는 수천 대의 노드에 있는 페타바이트 크기의 데이터를 처리할 수 있을 정도로 선형으로 확장됩니다. Hadoop 에코시스템의 분산 애플리케이션이 제공하는 데이터 중복, 일괄 처리 및 기타 기능을 사용할 수 있습니다.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Azure HDInsight에서 Apache HBase를 구현하는 방법

HDInsight HBase는 Azure 환경에 통합된 관리 클러스터로 제공합니다. 이 클러스터는 [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md)에 직접 데이터를 저장하도록 구성되며, 그러면 대기 시간이 짧고 성능 및 비용 선택 시 탄력성이 높습니다. 따라서 고객은 대규모 데이터 세트로 작업하는 대화형 웹 사이트를 구축하고, 수백만 개의 엔드포인트에서 발생하는 센서 및 원격 분석 데이터를 저장하는 서비스를 구축하고, Hadoop 작업을 사용해 이 데이터를 분석할 수 있습니다. HBase 및 Hadoop은 Azure의 빅데이터 프로젝트에 유용한 시작점이며, 특히 실시간 애플리케이션이 대형 데이터 세트로 작동하도록 할 수 있습니다.

HDInsight 구현은 HBase의 규모 확장 아키텍처를 활용하여 테이블 자동 분할, 읽기 및 쓰기에 대한 강력한 일관성 및 자동 장애 조치(Failover)를 제공합니다. 읽기를 위한 메모리 내 캐싱과 쓰기를 위한 높은 처리량 스트리밍을 통해 성능이 향상됩니다. HBase 클러스터는 가상 네트워크 내에 만들 수 있습니다. 자세한 내용은 [Azure Virtual Network에 HDInsight 클러스터 만들기](./apache-hbase-provision-vnet.md)를 참조하세요.

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>HDInsight HBase에서 데이터를 관리하는 방법
HBase의 HBase 셸에서 `create`, `get`, `put` 및 `scan` 명령을 사용하여 데이터를 관리할 수 있습니다. 데이터는 `put`을 사용하여 데이터베이스에 기록되고 `get`을 사용하여 읽습니다. `scan` 명령은 테이블의 여러 행에서 데이터를 가져오는 데 사용됩니다. HBase REST API에서 클라이언트 라이브러리를 제공하는 HBase C# API를 사용하여 데이터를 관리할 수도 있습니다. 또한 [Apache Hive](https://hive.apache.org/)를 사용하여 HBase 데이터베이스를 쿼리할 수 있습니다. 이러한 프로그래밍 모델 소개는 [HDInsight에서 Apache Hadoop와 함께 Apache HBase를 사용하여 시작](./apache-hbase-tutorial-get-started-linux.md)을 참조하세요. 데이터베이스를 호스트하는 노드에서 데이터를 처리할 수 있도록 하는 보조 프로세서를 사용할 수도 있습니다.

> [!NOTE]  
> Thrift는 HDInsight의 HBase에서 지원되지 않습니다.

## <a name="scenarios-use-cases-for-apache-hbase"></a>시나리오: Apache HBase 사용 사례
웹 검색에서 BigTable 및 확장에 의해 HBase를 만드는 정식 사용 사례입니다. 검색 엔진은 엔진을 포함하는 웹 페이지에 용어를 매핑하는 인덱스를 구축합니다. 그러나 HBase가 적합한 다른 많은 사용 사례가 있으며, 몇 가지 사례가 이 섹션에 나와 있습니다.

* 키-값 저장소
  
    HBase를 키-값 저장소로 사용할 수 있으며 메시지 시스템 관리에 적합합니다. Facebook은 해당 메시징 시스템에 HBase를 사용하며 인터넷 통신 저장 및 관리에 유용합니다. WebTable은 HBase를 사용하여 웹 페이지에서 추출된 테이블을 검색하고 관리합니다.
* 센서 데이터
  
    HBase는 다양한 소스에서 증분 방식으로 수집된 데이터를 캡처하는 데 유용합니다. 여기에는 소셜 분석, 시계열, 추세 및 카운터로 대화형 대시보드를 최신 상태로 유지, 감사 로그 시스템 관리가 포함됩니다. 예를 들어 서버 시스템 상태에 대해 수집된 메트릭을 저장하고 액세스를 제공하는 OpenTSDB(Open Time Series Database) 및 Bloomberg 중개인 터미널이 있습니다.
* 실시간 쿼리
  
    [Apache Phoenix](https://phoenix.apache.org/)는 Apache HBase용 SQL 쿼리 엔진입니다. JDBC 드라이버로 액세스되며 SQL을 사용하여 HBase 테이블을 쿼리하고 관리할 수 있도록 합니다.
* HBase를 플랫폼으로 사용
  
    HBase를 데이터 저장소로 사용하여 HBase 위에서 애플리케이션을 실행할 수 있습니다. 예를 들어 Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji, Titan 등이 있습니다. 애플리케이션이 HBase와 통합될 수도 있습니다. [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/), [Apache Drill](https://drill.apache.org/) 등을 예로 들 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [HDInsight에서 Hadoop을 통해 Apache HBase 사용 시작](./apache-hbase-tutorial-get-started-linux.md)
* [Azure Virtual Network에 HDInsight 클러스터 만들기](./apache-hbase-provision-vnet.md)
* [HDInsight에서 Apache HBase 복제 구성](apache-hbase-replication.md)
* [Apache Maven을 사용하여 HDInsight(Hadoop)에서 Apache HBase를 사용하는 Java 애플리케이션 빌드](./apache-hbase-build-java-maven-linux.md)

## <a name="see-also"></a>참고 항목
* [Apache HBase](https://hbase.apache.org/)
* [Apache HBase Reference Guide](https://hbase.apache.org/book.html)(Apache HBase 참조 설명서)
* [Bigtable: 구조화된 데이터의 분산 저장소 시스템](https://research.google.com/archive/bigtable.html)
