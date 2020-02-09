---
title: Azure Cosmos DB에 Apache Spark 연결
description: Azure Cosmos DB에 Apache Spark를 연결할 수 있도록 하는 Azure Cosmos DB Spark 커넥터에 대해 알아봅니다.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 2a0e88a439400bc36873ed7160b8eb039a16ebfb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356185"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Apache Spark-Azure Cosmos DB 커넥터를 사용하여 빅 데이터 분석 가속화

Cosmos DB Spark 커넥터를 사용 하 여 Azure Cosmos DB에 저장 된 데이터를 사용 하 여 [spark](https://spark.apache.org/) 작업을 실행할 수 있습니다. Cosmos는 일괄 처리 및 스트림 처리에 사용할 수 있으며 짧은 대기 시간 액세스를 위한 서비스 계층으로 사용할 수 있습니다.

Azure에서 관리 되는 Spark 클러스터를 제공 하는 [Azure Databricks](https://azure.microsoft.com/services/databricks) 또는 [azure HDInsight](https://azure.microsoft.com/services/hdinsight/)에서 커넥터를 사용할 수 있습니다. 다음 표에서는 지원 되는 Spark 버전을 보여 줍니다.

| 구성 요소 | 버전 |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2 및 2.1. x |
| 스칼라 | 2.11 |
| Azure Databricks 런타임 버전 | > 3.4 |

> [!WARNING]
> 이 커넥터는 Azure Cosmos DB의 코어 (SQL) API를 지원 합니다.
> MongoDB API에 대한 Cosmos DB [MongoDB Spark 커넥터](https://docs.mongodb.com/spark-connector/master/)를 사용 합니다.
> Cosmos DB Cassandra API의 경우 [Cassandra Spark 커넥터](https://github.com/datastax/spark-cassandra-connector)를 사용 합니다.
>

## <a name="quickstart"></a>빠른 시작

* [JAVA SDK 시작](sql-api-async-java-get-started.md) 의 단계에 따라 Cosmos DB 계정을 설정 하 고 일부 데이터를 채웁니다.
* [시작 Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) 의 단계에 따라 Azure Databricks 작업 영역 및 클러스터를 설정 합니다.
* 이제 새 노트북을 만들고 Cosmos DB 커넥터 라이브러리를 가져올 수 있습니다. 작업 영역을 설정 하는 방법에 대한 자세한 내용은 [Cosmos DB 커넥터 작업](#bk_working_with_connector) 으로 이동 합니다.
* 다음 섹션에는 커넥터를 사용 하 여 읽고 쓰는 방법에 대한 조각이 있습니다.

### <a name="batch-reads-from-cosmos-db"></a>Cosmos DB에서 일괄 읽기

다음 코드 조각에서는 PySpark의 Cosmos DB에서 읽을 Spark 데이터 프레임을 만드는 방법을 보여 줍니다.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Scala의 동일한 코드 조각:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>일괄 처리 Cosmos DB에 쓰기

다음 코드 조각에서는 PySpark에서 Cosmos DB에 데이터 프레임을 작성 하는 방법을 보여 줍니다.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

Scala의 동일한 코드 조각:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Cosmos DB에서 읽기 스트리밍

다음 코드 조각에서는 Azure Cosmos DB 변경 피드에 연결 하 고 읽는 방법을 보여 줍니다.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
Scala의 동일한 코드 조각:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Cosmos DB에 대한 스트리밍 쓰기

다음 코드 조각에서는 PySpark에서 Cosmos DB에 데이터 프레임을 작성 하는 방법을 보여 줍니다.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

Scala의 동일한 코드 조각:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
더 많은 코드 조각 및 종단 간 샘플은 [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)를 참조 하세요.

## <a name="bk_working_with_connector"></a>커넥터 작업

GitHub의 원본에서 커넥터를 빌드하거나 아래 링크에서 Maven의 uber jar을 다운로드할 수 있습니다.

| Spark | 스칼라 | 최신 버전 |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks 노트북 사용

Azure Databricks 가이드의 지침에 따라 [Azure Cosmos DB Spark 커넥터 사용](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html) > Databricks 작업 영역을 사용 하 여 라이브러리를 만듭니다.

> [!NOTE]
> **Azure Cosmos DB Spark 커넥터 사용** 페이지는 현재 최신 상태가 아닙니다. 6 개의 개별 jar를 6 개의 다른 라이브러리로 다운로드 하는 대신 maven at https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) 에서 uber jar를 다운로드 하 고이 한 jar/라이브러리를 설치할 수 있습니다.
> 

### <a name="using-spark-cli"></a>Spark-cli 사용

Spark cli를 사용 `spark-shell`하 여 ( `spark-submit` `pyspark`즉,,,) 커넥터를 사용 하 여 작업 하려면 커넥터의 [maven 좌표](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)에 매개 변수를 `--packages` 사용할 수 있습니다.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyter 노트북 사용

HDInsight 내에서 jupyter 노트북을 사용 하는 경우 spark-매직 `%%configure` 셀을 사용 하 여 커넥터의 maven 좌표를 지정할 수 있습니다.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> 를 포함 `spark.jars.excludes` 하는 것은 커넥터, Apache Spark 및 Livy 간의 잠재적 충돌을 제거 하는 데만 적용 됩니다.

### <a name="build-the-connector"></a>커넥터 빌드

현재이 커넥터 프로젝트는를 `maven` 사용 하 여 종속성 없이 빌드하기 위해 다음을 실행할 수 있습니다.

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>샘플 사용

[Cosmos DB Spark GitHub 리포지토리에](https://github.com/Azure/azure-cosmosdb-spark) 는 다음 샘플 노트북 및 스크립트를 사용해 볼 수 있습니다.

* **시애틀 (Spark 및 Cosmos DB)의 시간별 성능** [ip를 b](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): HDInsight Jupyter 노트북 서비스를 사용 하 여 Spark SQL, GraphFrames을 소개 하 고 ML 파이프라인을 사용 하 여 비행 지연을 예측 하는 Cosmos DB에 Spark를 연결 합니다.
* **Apache Spark 및 Azure Cosmos DB 변경 피드가 포함 된 Twitter 원본**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Apache Spark를 사용 하 여 Cosmos DB 그래프 쿼리**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* 를 사용 하 여 `azure-cosmosdb-spark` **[Azure Cosmos DB에 Azure Databricks 연결](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  여기에 연결 된 Azure Databricks 버전의 [온-비행기 성능 노트북](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)도 있습니다.
* **[Azure Cosmos DB 및 HDInsight를 사용 하는 람다 아키텍처 (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** : Cosmos DB 및 Spark를 사용 하 여 빅 데이터 파이프라인을 유지 관리 하는 작업 오버 헤드를 줄일 수 있습니다.

## <a name="more-information"></a>추가 정보

`azure-cosmosdb-spark` [Wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) 에는 다음과 같은 추가 정보가 있습니다.

* [Azure Cosmos DB Spark 커넥터 사용자 가이드](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [집계 예제](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>구성 및 설정

* [Spark 커넥터 구성](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark Cosmos DB 커넥터 설정](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (진행 중)
* [HDI (Apache Spark를 통해 Azure Cosmos DB에 직접 쿼리 Power BI 구성](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>문제 해결

* [Cosmos DB 집계 사용](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [알려진 문제](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>성능

* [성능 팁](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [테스트 실행 쿼리](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [테스트 실행 작성](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>피드 변경

* [Azure Cosmos DB 변경 피드 및 Apache Spark를 사용 하 여 스트림 처리 변경 내용](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [변경 피드 데모](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [구조적 스트림 데모](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>모니터링

* [Application insights를 사용 하 여 Spark 작업 모니터링](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>다음 단계

아직 없는 경우 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 리포지토리에서 Azure Cosmos DB 커넥터로 Spark를 다운로드합니다. 리포지토리에서 다음 추가 리소스를 살펴보세요.

* [집계 예제](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [샘플 스크립트 및 노트북](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

또한 [Apache Spark SQL, DataFrame 및 데이터 세트 가이드](https://spark.apache.org/docs/latest/sql-programming-guide.html) 및 [Azure HDInsight의 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 문서를 검토할 수도 있습니다.
