---
title: Azure Cosmos DB에 Apache Spark 연결
description: Azure Cosmos DB에 Apache Spark를 연결할 수 있도록 하는 Azure Cosmos DB Spark 커넥터에 대해 알아봅니다.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: ramkris
ms.openlocfilehash: 60afd8128224050d456699e798d814a259e106ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330249"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Apache Spark-Azure Cosmos DB 커넥터를 사용하여 빅 데이터 분석 가속화

실행할 수 있습니다 [Spark](https://spark.apache.org/) Cosmos DB Spark 커넥터를 사용 하 여 Azure Cosmos DB에 저장 된 데이터를 사용 하 여 작업 합니다. Cosmos 대기 시간이 짧은 액세스를 위한 일괄 처리 및 스트림 처리 및 서비스 계층으로 사용할 수 있습니다.

커넥터를 사용할 수 있습니다 [Azure Databricks](https://azure.microsoft.com/services/databricks) 하거나 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), Azure에서 관리 되는 Spark 클러스터를 제공 하는 합니다. 다음 표에서 지원 되는 Spark 버전을 보여 줍니다.

| 구성 요소 | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x, and 2.1.x |
| 스칼라 | 2.11 |
| Azure Databricks 런타임 버전 | > 3.4 |

> [!WARNING]
> 이 커넥터는 Azure Cosmos DB의 코어 (SQL) API를 지원합니다.
> MongoDB API에 대 한 Cosmos DB를 사용 합니다 [MongoDB Spark 커넥터](https://docs.mongodb.com/spark-connector/master/)합니다.
> Cosmos DB Cassandra API를 사용 합니다 [Cassandra Spark 커넥터](https://github.com/datastax/spark-cassandra-connector)합니다.
>

## <a name="quickstart"></a>빠른 시작

* 단계에 따라 [Java SDK를 사용 하 여 시작](sql-api-async-java-get-started.md) Cosmos DB 계정을 설정 하 여 특정 데이터를 채웁니다.
* 단계에 따라 [Azure Databricks 시작](https://docs.azuredatabricks.net/getting-started/index.html) Azure Databricks 작업 영역 및 클러스터를 설정 합니다.
* 이제 새 노트북을 만들고 Cosmos DB 커넥터 라이브러리를 가져올 수 있습니다. 이동할 [Cosmos DB 커넥터를 사용 하 여 작업](#bk_working_with_connector) 작업 영역을 설정 하는 방법에 대 한 자세한 내용은 합니다.
* 다음 섹션에서는 읽기 및 쓰기 커넥터를 사용 하는 방법에 코드 조각에 있습니다.

### <a name="reading-from-cosmos-db"></a>Cosmos DB에서 읽기

다음 코드 조각에는 PySpark에서 Cosmos DB에서 읽는를 Spark DataFrame을 만드는 방법을 보여 줍니다.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

및 scala에서 같은 코드 조각:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
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

### <a name="writing-to-cosmos-db"></a>Cosmos DB에 쓰기

다음 코드 조각은 PySpark에서 Cosmos DB를 데이터 프레임을 작성 하는 방법을 보여 줍니다.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

및 scala에서 같은 코드 조각:

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

더 자세한 코드 조각 및 종단 간 샘플을 참조 하세요 [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)합니다.

## <a name="bk_working_with_connector"></a> 커넥터를 사용 하 여 작업

Github의 원본에서 커넥터를 빌드할 수도 있고 아래 링크에서 Maven에서 uber jar를 다운로드할 수 있습니다.

| Spark | 스칼라 | 최신 버전 |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks notebook을 사용 하 여

Databricks 작업 영역을 사용 하 여 Azure Databricks 가이드의 지침에 따라 라이브러리 만들기 > [Azure Cosmos DB Spark 커넥터를 사용 합니다.](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> 에 **Azure Cosmos DB Spark 커넥터를 사용 하 여** 페이지 현재 최신 상태가 아닙니다. 6 개의 서로 다른 라이브러리에 6 개의 별도 jar를 다운로드 하는 대신에 maven에서 uber jar을 다운로드할 수 있습니다 https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) 이 단일 jar/라이브러리를 설치 합니다.
> 

### <a name="using-spark-cli"></a>Spark-cli를 사용 하 여

Spark-cli를 사용 하 여 커넥터를 사용 하려면 (즉, `spark-shell`, `pyspark`, `spark-submit`)를 사용할 수 있습니다를 `--packages` 커넥터의 매개 변수 [maven 좌표](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>Jupyter notebook을 사용 하 여

HDInsight에서 Jupyter notebook을 사용 하는 경우에 spark magic을 사용할 수 있습니다 `%%configure` 커넥터의 maven 좌표를 지정 하는 셀입니다.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> 참고, 포함 된 `spark.jars.excludes` Livy, Apache Spark 및 커넥터 간의 잠재적인 충돌을 제거 하는 합니다.

### <a name="build-the-connector"></a>커넥터 빌드

이 커넥터 프로젝트는 현재 `maven` 종속성 없이 빌드를 실행할 수 있습니다.

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>샘플 사용

합니다 [Cosmos DB Spark GitHub 리포지토리](https://github.com/Azure/azure-cosmosdb-spark) 다음과 같은 샘플 notebook 및 시도할 수 있는 스크립트를 포함 합니다.

* **Spark와 Cosmos DB (Seattle)을 사용 하 여 비행 운항 정시 성** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): HDInsight Jupyter 노트북 서비스를 사용 하 여 Spark SQL, GraphFrames, 및 ML 파이프라인을 사용 하 여 예측 비행 연착 정보를 보여 주기 위해 제공 하는 Cosmos DB에 Spark를 연결 합니다.
* **[Cosmos DB 변경 피드를 사용 하 여 Spark를 연결](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: Spark를 Cosmos DB 변경 피드에 연결 하는 방법은 빠른 소개 합니다.
* **Apache Spark와 Azure Cosmos DB 변경 피드를 사용 하 여 원본 twitter**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Apache Spark를 사용 하 여 Cosmos DB 그래프 쿼리에**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Azure Cosmos DB에 연결 하는 Azure Databricks](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  사용 하 여 `azure-cosmosdb-spark`입니다.  여기에 연결 된 버전은 또한 Azure Databricks의 버전을 [비행 운항 정시 성 notebook](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)합니다.
* **[HDInsight (Apache Spark) 및 Azure Cosmos DB 사용 하 여 람다 아키텍처](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: Cosmos DB와 Spark를 사용 하 여 빅 데이터 파이프라인을 유지 관리 작업 오버 헤드를 줄일 수 있습니다.

## <a name="more-information"></a>추가 정보

자세한 내용은에 있는 합니다 `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) 포함:

* [Azure Cosmos DB Spark 커넥터 설명서](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [집계 예제](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>구성 및 설정

* [Spark 커넥터 구성](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark를 Cosmos DB 커넥터 설정](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (진행)
* [Apache Spark (HDI)를 통해 Azure cosmos DB Power BI 직접 쿼리를 구성합니다.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>문제 해결

* [Cosmos DB 집계를 사용 하 여](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [알려진 문제](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>성능

* [성능 팁](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [쿼리 테스트 실행](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [쓰기 테스트 실행](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>피드 변경

* [Azure Cosmos DB 변경 피드 및 Apache Spark를 사용 하 여 Stream 처리 변경](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [변경 피드 데모](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [구조화 된 Stream 데모](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>모니터링

* [Application insights 사용 하 여 Spark 작업 모니터링](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>다음 단계

아직 없는 경우 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 리포지토리에서 Azure Cosmos DB 커넥터로 Spark를 다운로드합니다. 리포지토리에서 다음 추가 리소스를 살펴보세요.

* [집계 예제](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [샘플 스크립트 및 노트북](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

또한 [Apache Spark SQL, DataFrame 및 데이터 세트 가이드](https://spark.apache.org/docs/latest/sql-programming-guide.html) 및 [Azure HDInsight의 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 문서를 검토할 수도 있습니다.
