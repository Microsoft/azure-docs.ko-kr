---
title: Azure Cosmos DB 및 HDInsight(Apache Spark)를 사용하는 람다 아키텍처
description: 이 문서에서는 Azure Cosmos DB, HDInsight 및 Spark를 사용하여 람다 아키텍처를 구현하는 방법을 설명합니다.
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 6902b1a26d02efbf1a31fe9a3a25253a6b5a5604
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043791"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB는 Azure 플랫폼에 람다 아키텍처 구현 

람다 아키텍처는 대규모 데이터 집합의 효율적인 데이터 처리를 가능하게 합니다. 람다 아키텍처는 일괄 처리, 스트림 처리 및 서비스 계층을 사용하여 빅 데이터를 쿼리하는 데 관련된 대기 시간을 최소화합니다. 

Azure에서 람다 아키텍처를 구현하려면 다음 기술을 결합하여 실시간 빅 데이터 분석을 가속화합니다.
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) - 업계 최초로 전 세계적으로 배포된 다중 모델 데이터베이스 서비스입니다. 
* [Azure HDInsight용 Apache Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) - 대규모 데이터 분석 애플리케이션을 실행하는 처리 프레임워크입니다.
* Azure Cosmos DB [변경 피드](change-feed.md) - HDInsight의 일괄 처리 계층에 새 데이터를 스트리밍하여 처리합니다.
* [Spark-Azure Cosmos DB 커넥터](spark-connector.md)

이 문서에서는 원래의 다중 계층 설계를 기반으로 한 람다 아키텍처의 기본 사항과 작업을 간소화하는 "재개발된" 람다 아키텍처의 이점에 대해 설명합니다.  

## <a name="what-is-a-lambda-architecture"></a>람다 아키텍처란?
[Nathan Marz](https://twitter.com/nathanmarz)가 기술한 대로, 람다 아키텍처는 일괄 처리 및 짧은 대기 시간 시나리오를 처리하기 위한 확장 가능하고 내결함성이 있는 일반적인 데이터 처리 아키텍처입니다.

![람다 아키텍처를 보여 주는 다이어그램](./media/lambda-architecture/lambda-architecture-intro.png)

원본: http://lambda-architecture.net/

람다 아키텍처의 기본 원칙은 [http://lambda-architecture.net](http://lambda-architecture.net/)에 따라 앞의 다이어그램에서 설명하고 있습니다.

 1. 모든 **데이터**는 *일괄 처리 계층* 및 *속도 계층* *모두*로 푸시됩니다.
 2. **일괄 처리 계층**에는 마스터 데이터 세트(변경 불가능한 추가 전용 원시 데이터 세트)가 있으며, 일괄 처리 보기가 미리 계산됩니다.
 3. **서비스 계층**에는 빠른 쿼리에 대한 일괄 처리 보기가 있습니다. 
 4. **속도 계층**은 서비스 계층에 대한 처리 시간을 보정하고 최근 데이터만 처리합니다.
 5. 일괄 처리 보기와 실시간 보기의 결과를 병합하거나 개별적으로 ping하여 모든 쿼리에 응답할 수 있습니다.

계속 참조해 가면서 다음을 사용하여 이 아키텍처를 구현할 수 있습니다.

* Azure Cosmos DB 컬렉션
* HDInsight(Apache Spark 2.1) 클러스터
* Spark 커넥터 [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>속도 계층

작업 측면에서 데이터의 올바른 상태를 유지하면서 두 개의 데이터 스트림을 유지하는 것은 복잡한 작업일 수 있습니다. 작업을 간소화하려면 [Azure Cosmos DB 변경 피드 지원](change-feed.md)을 활용하여 *속도 계층*에 대한 *변경 피드 API*를 통해 Azure Cosmos DB 변경 로그를 표시하면서 *일괄 처리 계층*에 대한 상태를 유지합니다.  
![새 데이터, 속도 계층 및 마스터 데이터 세트 부분을 강조 표시한 람다 아키텍처 다이어그램](./media/lambda-architecture/lambda-architecture-change-feed.png)

이러한 계층에 대한 중요 사항

 1. 모든 **데이터**가 *Azure Cosmos DB에만* 푸시됩니다. 이에 따라 멀티캐스팅 문제를 방지할 수 있습니다.
 2. **일괄 처리 계층**에는 마스터 데이터 세트(변경 불가능한 추가 전용 원시 데이터 세트)가 있으며, 일괄 처리 보기가 미리 계산됩니다.
 3. **서비스 계층**은 다음 섹션에서 설명합니다.
 4. **속도 계층**은 HDInsight(Apache Spark)를 활용하여 Azure Cosmos DB 변경 피드를 읽습니다. 이렇게 하면 데이터를 유지하고 동시에 쿼리하고 처리할 수 있습니다.
 5. 일괄 처리 보기와 실시간 보기의 결과를 병합하거나 개별적으로 ping하여 모든 쿼리에 응답할 수 있습니다.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>코드 예제: Azure Cosmos DB 변경 피드에 대한 Spark 구조적 스트리밍
Azure Cosmos DB 변경 피드의 빠른 프로토타입을 **속도 계층**의 일부로 실행하려면, Twitter 데이터를 [Azure Cosmos DB 변경 피드 및 Apache Spark를 사용하여 스트림 처리 변경](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) 예제의 일부로 사용하여 테스트할 수 있습니다. Twitter 출력을 빠르게 시작하려면 [Twitter에서 Cosmos DB로 스트림 피드](https://github.com/tknandu/TwitterCosmosDBFeed)의 코드 샘플을 참조하세요. 앞의 예제를 사용하면 Twitter 데이터를 Azure Cosmos DB에 로드한 다음, HDInsight(Apache Spark) 클러스터를 변경 피드에 연결하도록 설정할 수 있습니다. 이 구성을 설정하는 방법에 대한 자세한 내용은 [Apache Spark-Azure Cosmos DB 커넥터 설정](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)을 참조하세요.  

다음 코드 조각에서는 실시간 Twitter 데이터 스트림을 검토하는 Azure Cosmos DB 변경 피드에 연결하기 위해 구조화된 스트리밍 작업을 실행하도록 `spark-shell`을 구성하여 실행 간격 수를 계산하는 방법을 보여 줍니다.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

완전한 코드 샘플은 [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)를 참조하세요.
* 여기에는 [Streaming Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) 및
* [Streaming Tags Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)가 있습니다.

이 출력은 Azure Cosmos DB 변경 피드의 Twitter 데이터에 대한 간격 수를 계산하는 구조화된 스트리밍 작업을 지속적으로 실행하는 `spark-shell` 콘솔입니다. 다음 이미지에서는 스트림 작업의 출력과 간격 수를 보여 줍니다.

![Azure Cosmos DB 변경 피드의 Twitter 데이터에 대한 간격 수를 보여 주는 스트리밍 출력](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Azure Cosmos DB 변경 피드에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Cosmos DB에서 변경 피드 지원 사용](change-feed.md)
* [Azure CosmosDB 변경 피드의 프로세서 라이브러리에 대한 소개](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [스트림 처리 변경: Azure CosmosDB 변경 피드 + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>일괄 처리 및 서비스 계층
새 데이터가 Azure Cosmos DB(속도 계층에 사용되는 변경 피드)에 로드되기 때문에 **마스터 데이터 세트**(변경 불가능한 추가 전용 원시 데이터 세트)가 있는 위치입니다. 이제부터는 HDInsight(Apache Spark)를 사용하여 다음 이미지와 같이 **일괄 처리 계층**에서 **서비스 계층**으로 미리 계산 기능을 수행합니다.

![일괄 처리 계층 및 서비스 계층을 강조 표시한 람다 아키텍처 다이어그램](./media/lambda-architecture/lambda-architecture-batch-serve.png)

이러한 계층에 대한 중요 사항

 1. 멀티캐스팅 문제를 방지하기 위해 모든 **데이터**가 Azure Cosmos DB에만 푸시됩니다.
 2. **일괄 처리 계층**에는 Azure Cosmos DB에 저장되는 마스터 데이터 세트(변경 불가능한 추가 전용 원시 데이터 세트)가 있습니다. HDI Spark를 사용하면 집계를 미리 계산하여 계산된 일괄 처리 보기에 저장할 수 있습니다.
 3. **서비스 계층**은 마스터 데이터 세트 및 계산된 일괄 처리 보기에 대한 컬렉션이 있는 Azure Cosmos DB 데이터베이스입니다.
 4. **속도 계층**은 이 문서의 뒷부분에서 설명합니다.
 5. 일괄 처리 보기와 실시간 보기의 결과를 병합하거나 개별적으로 ping하여 모든 쿼리에 응답할 수 있습니다.

### <a name="code-example-pre-computing-batch-views"></a>코드 예제: 사전 컴퓨팅 일괄 처리 보기
Apache Spark에서 Azure Cosmos DB로 연결되는 **마스터 데이터 세트**에 대해 미리 계산된 보기를 실행하는 방법을 보여 주기 위해, 노트북에 있는 [람다 아키텍처 재개발 - 일괄 처리 계층](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) 및 [람다 아키텍처 재개발 - 일괄 처리 및 서비스 계층](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) 코드 조각을 사용합니다. 이 시나리오에서는 Azure Cosmos DB에 저장된 Twitter 데이터를 사용합니다.

아래 PySpark 코드를 사용하여 Azure Cosmos DB 내의 Twitter 데이터에 대한 구성 연결을 만드는 것으로 시작해 보겠습니다.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

다음으로, 아래 Spark SQL 문을 실행하여 트윗 집합의 상위 10개 해시태그를 확인해 보겠습니다. 이 Spark SQL 쿼리의 경우 이 코드 조각 바로 뒤에 출력 막대형 차트가 없는 Jupyter 노트북에서 이 쿼리를 실행합니다.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![해시태그당 트윗 수를 보여 주는 차트](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

이제 쿼리를 수행했으므로 출력 데이터를 다른 컬렉션에 저장하기 위해 Spark 커넥터를 사용하여 컬렉션에 다시 저장해 보겠습니다.  이 예제에서는 Scala를 사용하여 연결을 보여 줍니다. 이전 예제와 비슷하게 Apache Spark 데이터 프레임을 다른 Azure Cosmos DB 컬렉션에 저장하도록 구성 연결을 만듭니다.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

`SaveMode`(문서에 대한 `Overwrite` 또는 `Append` 중 어떤 작업인지 여부를 나타냄)가 지정되면 이전 예제의 Spark SQL 쿼리와 비슷한 `tweets_bytags` 데이터 프레임을 만듭니다.  `tweets_bytags` 데이터 프레임이 만들어지면 이전에 지정한 `writeConfig`를 사용하는 `write` 메서드를 통해 이 데이터 프레임을 저장할 수 있습니다.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

이제 이 마지막 명령문에서 Spark 데이터 프레임이 새 Azure Cosmos DB 컬렉션에 저장되었습니다. 람다 아키텍처 측면에서 이는 **서비스 계층** 내의 **일괄 처리 보기**입니다.
 
#### <a name="resources"></a>리소스

완전한 코드 샘플은 [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)를 참조하세요.
* 여기에는 람다 아키텍처 재개발 - 일괄 처리 계층 [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) 및
* 람다 아키텍처 재개발 - 일괄 처리 및 서비스 계층 [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)가 있습니다.

## <a name="speed-layer"></a>속도 계층
앞에서 언급한 대로 Azure Cosmos DB 변경 피드 라이브러리를 사용하면 일괄 처리 및 속도 계층 간의 작업을 간소화할 수 있습니다. 이 아키텍처에서는 HDInsight를 통해 Apache Spark를 사용하여 데이터에 대해 *구조화된 스트리밍* 쿼리를 수행합니다. 또한 다른 시스템에서 이 데이터를 액세스할 수 있도록 구조화된 스트리밍 쿼리의 결과를 일시적으로 유지하려고 할 수도 있습니다.

![속도 계층을 강조 표시한 람다 아키텍처 다이어그램](./media/lambda-architecture/lambda-architecture-speed.png)

이렇게 하려면 별도의 Azure Cosmos DB 컬렉션을 만들어 구조화된 스트리밍 쿼리의 결과를 저장합니다.  이렇게 하면 Apache Spark뿐 아니라 다른 시스템에서도 이 정보에 액세스할 수 있습니다. Cosmos DB TTL(Time-to-Live) 기능뿐만 아니라 설정된 기간 후에 문서가 자동으로 삭제되도록 구성할 수 있습니다.  Azure Cosmos DB TTL 기능에 대한 자세한 내용은 [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB 컬렉션의 데이터 만료](time-to-live.md)를 참조하세요.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>람다 아키텍처: 재개발
이전 섹션에서 언급한 대로 다음 구성 요소를 사용하여 원래의 람다 아키텍처를 간소화할 수 있습니다.
* Azure Cosmos DB
* - 일괄 처리와 속도 계층 간에 데이터를 멀티캐스팅할 필요가 없도록 하는 Azure Cosmos DB 변경 피드 라이브러리입니다.
* HDInsight의 Apache Spark
* Azure Cosmos DB용 Spark 커넥터

![Azure Cosmos DB, Spark 및 Azure Cosmos DB 변경 피드 API를 사용한 람다 아키텍처의 재개발을 보여 주는 다이어그램](./media/lambda-architecture/lambda-architecture-re-architected.png)

이 설계를 사용하면 Azure Cosmos DB와 HDInsight의 두 가지 관리 서비스만 필요합니다. 이와 결합하여 람다 아키텍처의 일괄 처리, 서비스 및 속도 계층을 처리합니다. 이 경우 작업뿐만 아니라 데이터 흐름도 간소화합니다. 
 1. 모든 데이터는 처리를 위해 Azure Cosmos DB에 푸시됩니다.
 2. 일괄 처리 계층에는 마스터 데이터 세트(변경 불가능한 추가 전용 원시 데이터 세트)가 있으며, 일괄 처리 보기가 미리 계산됩니다.
 3. 서비스 계층에는 빠른 쿼리에 대한 데이터의 일괄 처리 보기가 있습니다.
 4. 속도 계층은 서비스 계층에 대한 처리 시간을 보정하고 최근 데이터만 처리합니다.
 5. 일괄 처리 보기와 실시간 보기의 결과를 병합하여 모든 쿼리에 응답할 수 있습니다.

### <a name="resources"></a>리소스

* **새 데이터**: 새 데이터를 Azure Cosmos DB로 푸시하는 메커니즘인 [Twitter에서 CosmosDB로의 스트림 피드](https://github.com/tknandu/TwitterCosmosDBFeed)입니다.
* **일괄 처리 계층:** 일괄 처리 계층은 *마스터 데이터 세트*(변경 불가능한 추가 전용 원시 데이터 세트) 및 **서비스 계층**에 푸시된 데이터의 일괄 처리 보기를 미리 컴퓨팅할 수 있는 기능으로 구성됩니다.
   * **람다 아키텍처 재개발 - 일괄 처리 계층** 노트북 [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html)은 일괄 처리 보기의 *마스터 데이터 세트*를 쿼리합니다.
* **서비스 계층:** **서비스 계층**은 미리 컴퓨팅된 데이터로 구성되어 빠른 쿼리에 대한 일괄 처리 보기(예: 집계, 특정 슬라이서 등)를 생성합니다.
  * **람다 아키텍처 재개발 - 일괄 처리 및 서비스 계층** 노트북 [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html)은 일괄 처리 데이터를 서비스 계층으로 푸시합니다. 즉 Spark에서 트윗의 일괄 처리 컬렉션을 쿼리하고, 처리하고, 다른 컬렉션(계산된 일괄 처리)에 저장합니다.
    * **속도 계층:** **속도 계층**은 Azure Cosmos DB 변경 피드를 활용하여 즉시 읽고 작업을 수행하는 Spark로 구성됩니다. 또한 데이터가 *계산된 RT*에 저장되어 다른 시스템에서 실시간 쿼리를 실행하는 것과 달리 처리된 실시간 데이터를 쿼리할 수 있습니다.
  * [Streaming Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) Scala 스크립트는 Azure Cosmos DB 변경 피드의 스트리밍 쿼리를 실행하여 spark-shell의 간격 수를 계산합니다.
  * [Streaming Tags Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) Scala 스크립트는 Azure Cosmos DB 변경 피드의 스트리밍 쿼리를 실행하여 spark-shell의 태그 간격 수를 계산합니다.
  
## <a name="next-steps"></a>다음 단계
Cosmos DB와 Spark를 아직 연결하지 않았으면 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 리포지토리에서 Spark-Azure Cosmos DB 커넥터를 다운로드하고 다음 리포지토리에서 추가 리소스를 탐색합니다.
* [람다 아키텍처](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [분산 집계 예제](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [샘플 스크립트 및 노트북](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [구조화된 스트리밍 데모](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [변경 피드 데모](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Azure Cosmos DB 변경 피드 및 Apache Spark를 사용하여 스트림 처리 변경](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

또한 [Apache Spark SQL, DataFrames, and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)(Apache Spark SQL, DataFrame 및 데이터 세트 가이드) 및 [Azure HDInsight의 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 문서를 검토할 수도 있습니다.
