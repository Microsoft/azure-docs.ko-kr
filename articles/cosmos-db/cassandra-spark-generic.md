---
title: Spark에서 Azure Cosmos DB Cassandra API를 사용하여 작업
description: 이 문서는 Spark에서 Cosmos DB Cassandra API 통합에 대한 기본 페이지입니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 75d2930363b6ad1aeace22d7529df04f31deefe5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893639"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API에 연결

이 문서는 Spark에서 Azure Cosmos DB Cassandra API 통합에 대한 문서 시리즈 중 하나입니다. 문서는 연결, DDL(데이터 정의 언어) 작업, 기본 DML(데이터 조작 언어) 작업 및 Spark에서 고급 Azure Cosmos DB Cassandra API 통합을 다룹니다. 

## <a name="prerequisites"></a>필수 조건
* [Azure Cosmos DB Cassandra API 계정을 프로비전합니다](create-cassandra-dotnet.md#create-a-database-account).

* 선택한 Spark 환경 [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | 기타]를 프로비전합니다.

## <a name="dependencies-for-connectivity"></a>연결에 대한 종속성
* **Cassandra용 Spark 커넥터:** Spark 커넥터는 Azure Cosmos DB Cassandra API에 연결하는 데 사용됩니다.  Spark 및 Spark 환경의 Scala 버전과 호환되는 [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector)에 위치한 커넥터의 버전을 식별하고 사용합니다.

* **Cassandra API용 Azure Cosmos DB 도우미 라이브러리:** Spark 커넥터 외에 Azure Cosmos DB에서 [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar)라는 다른 라이브러리가 필요합니다. 이 라이브러리는 사용자 지정 연결 팩터리 및 다시 시도 정책 클래스를 포함합니다.

  Azure Cosmos DB의 재시도 정책은 HTTP 상태 코드 429("요청 빈도가 높음") 예외를 처리하도록 구성됩니다. Azure Cosmos DB Cassandra API는 Cassandra 네이티브 프로토콜에서 이러한 예외를 오버로드된 오류로 변환하고, back-off를 사용하여 다시 시도할 수 있습니다. Azure Cosmos DB는 프로비전된 처리량 모델을 사용하므로 수신/송신 요금이 증가하는 경우 요청 속도 제한 예외가 발생합니다. 재시도 정책은 일시적으로 컬렉션에 할당된 처리량을 초과하는 데이터 급증에 대해 spark 작업을 보호합니다.

  > [!NOTE] 
  > 재시도 정책은 순간적인 급증에 대해서만 spark 작업을 보호할 수 있습니다. 워크로드를 실행하는 데 필요한 충분한 RU를 구성하지 않은 경우 다시 시도 정책이 적용되지 않고 재시도 정책 클래스에서 예외를 다시 throw합니다.

* **Azure Cosmos DB 계정 연결 세부 정보:** Azure Cassandra API 계정 이름, 계정 엔드포인트 및 키입니다.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark 커넥터 처리량 구성 매개 변수

다음 표에서는 커넥터에서 제공된 Azure Cosmos DB Cassandra API 관련 처리량 구성 매개 변수를 나열합니다. 모든 구성 매개 변수의 자세한 목록은 Spark Cassandra 커넥터 GitHub 리포지토리의 [구성 참조](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) 페이지를 참조하세요.

| **속성 이름** | **기본값** | **설명** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |단일 일괄 처리당 행 수입니다. 이 매개 변수를 1로 설정합니다. 이 매개 변수는 과도한 워크로드에 대한 높은 처리량을 달성하는 데 사용됩니다. |
| spark.cassandra.connection.connections_per_executor_max  | 없음 | 실행기별 노드당 연결의 최대 수입니다. 10*n은 n개 노드 Cassandra 클러스터의 노드당 10개의 연결과 같습니다. 따라서 5개 노드 Cassandra 클러스터에 대한 실행기별 노드당 5개의 연결이 필요한 경우 이 구성을 25로 설정해야 합니다. 이 값을 병렬 처리 수준 또는 spark 작업이 구성된 실행기 수에 따라 수정합니다.   |
| spark.cassandra.output.concurrent.writes  |  100 | 실행기당 발생할 수 있는 병렬 쓰기 수를 정의합니다. "batch.size.rows"를 1로 설정했으므로 이 값을 적절하게 크기 조정해야 합니다. 이 값을 병렬 처리 수준 또는 워크로드에 대해 달성하려는 처리량에 따라 수정합니다. |
| spark.cassandra.concurrent.reads |  512 | 실행기당 발생할 수 있는 병렬 읽기 수를 정의합니다. 이 값을 병렬 처리 수준 또는 워크로드에 대해 달성하려는 처리량에 따라 수정합니다.  |
| spark.cassandra.output.throughput_mb_per_sec  | 없음 | 실행기당 총 쓰기 처리량을 정의합니다. 이 매개 변수는 spark 작업 처리량의 상한 및 Cosmos DB 컬렉션의 프로비전된 처리량을 기준으로 사용될 수 있습니다.   |
| spark.cassandra.input.reads_per_sec| 없음   | 실행기당 총 읽기 처리량을 정의합니다. 이 매개 변수는 spark 작업 처리량의 상한 및 Cosmos DB 컬렉션의 프로비전된 처리량을 기준으로 사용될 수 있습니다.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Cassandra API에 보내기 전에 메모리에 저장할 수 있는 단일 spark 작업당 일괄 처리 수를 정의합니다. |
| spark.cassandra.connection.keep_alive_ms | 60000 | 사용되지 않는 연결을 사용할 수 있는 기간을 정의합니다. | 

spark 작업에 대해 예상하는 워크로드 및 Cosmos DB 계정에 대해 프로비전한 처리량에 따라 처리량 및 이러한 매개 변수의 병렬 처리 수준을 조정합니다.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API에 연결

### <a name="cqlsh"></a>cqlsh
다음 명령은 cqlsh에서 Azure CosmosDB Cassandra API에 연결하는 방법을 자세히 설명합니다.  Spark에서 샘플을 통해 실행할 때 유효성 검사에 유용합니다.<br>
**Linux/Unix/Mac에서:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
아래 문서에서는 Azure Databricks 클러스터 프로비전, Azure Cosmos DB Cassandra API에 연결을 위한 클러스터 구성 및 DDL 작업, DML 작업 등을 다루는 몇 가지 샘플 노트북을 설명합니다.<BR>
[Azure databricks에서 Azure Cosmos DB Cassandra API를 사용하여 작업](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Azure HDInsight-Spark
아래 문서에서는 HDinsight-Spark 서비스, 프로비전, Azure Cosmos DB Cassandra API에 연결을 위한 클러스터 구성 및 DDL 작업, DML 작업 등을 다루는 몇 가지 샘플 노트북을 설명합니다.<BR>
[Azure HDInsight-Spark에서 Azure Cosmos DB Cassandra API를 사용하여 작업](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  일반적인 Spark 환경
위의 섹션은 Azure Spark 기반 PaaS 서비스에 관련되었던 반면 이 섹션에서는 일반적인 Spark 환경을 다룹니다.  커넥터 종속성, 가져오기 및 Spark 세션 구성은 아래에서 자세히 설명됩니다. "다음 단계" 섹션에서는 DDL 작업, DML 작업 등에 대한 코드 샘플을 다룹니다.  

#### <a name="connector-dependencies"></a>커넥터 종속성:

1. maven 좌표를 추가하여 [Spark용 Cassandra 커넥터](cassandra-spark-generic.md#dependencies-for-connectivity) 가져오기
2. Cassandra API의 [Azure Cosmos DB 도우미 라이브러리](cassandra-spark-generic.md#dependencies-for-connectivity)에 대한 maven 좌표 추가

#### <a name="imports"></a>가져오기:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark 세션 구성:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Azure Cosmos DB Cassandra API와 Spark 통합을 보여줍니다. 
 
* [DDL 작업](cassandra-spark-ddl-ops.md)
* [만들기/삽입 작업](cassandra-spark-create-ops.md)
* [읽기 작업](cassandra-spark-read-ops.md)
* [Upsert 작업](cassandra-spark-upsert-ops.md)
* [삭제 작업](cassandra-spark-delete-ops.md)
* [집계 작업](cassandra-spark-aggregation-ops.md)
* [테이블 복사 작업](cassandra-spark-table-copy-ops.md)
