---
title: HDInsight를 통해 YARN에서 Spark의 Azure Cosmos DB Cassandra API에 액세스
description: 이 문서에서는 HDInsight를 통해 YARN에서 Spark의 Azure Cosmos DB Cassandra API로 작업하는 방법을 알아봅니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f728baedf9e325f224ce52e64325064f553d2671
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893705"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>HDInsight를 통해 YARN에서 Spark의 Azure Cosmos DB Cassandra API에 액세스

이 문서에서는 spark 셸에서 HDInsight-Spark를 통해 YARN에서 Spark의 Azure Cosmos DB Cassandra API에 액세스하는 방법을 알아봅니다. HDInsight는 HDFS에 대한 개체 저장소를 활용하는 Azure의 Microsoft Hortonworks Hadoop PaaS로, [Spark](../hdinsight/spark/apache-spark-overview.md)를 포함한 여러 유형으로 제공됩니다.  이 문서의 내용은 HDInsight-Spark를 참조하지만, 모든 Hadoop 배포에 적용할 수 있습니다.  

## <a name="prerequisites"></a>필수 조건

* [Azure Cosmos DB Cassandra API 프로비전](create-cassandra-dotnet.md#create-a-database-account)

* [Azure Cosmos DB Cassandra API 연결 관련 기본 사항 검토](cassandra-spark-generic.md)

* [HDInsight-Spark 클러스터 프로비전](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Cassandra API 사용을 위한 코드 샘플 검토](cassandra-spark-generic.md#next-steps)

* [원하는 경우 cqlsh를 사용하여 유효성 검사](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Spark2에서 Cassandra API 구성** - Cassandra용 Spark 커넥터에서는 Spark 컨텍스트의 일부분으로 Cassandra 연결 세부 정보를 초기화해야 합니다. Jupyter Notebook을 시작할 때 spark 세션 및 컨텍스트가 이미 초기화되므로, HDInsight 기본 Jupyter Notebook 시작의 일부로 모든 구성 집합이 완료되지 않는 한 Spark 컨텍스트를 중지하고 다시 초기화하지 않는 것이 좋습니다. 해결 방법은 Cassandra 인스턴스 세부 정보를 Spark2 서비스 구성인 Ambari에 직접 추가하는 것입니다. 이는 Spark2 서비스를 다시 시작해야 하는 클러스터별 일회성 작업입니다.
 
  1. Spark2 서비스인 Ambari로 이동하고 구성을 선택합니다.

  2. 그런 다음, 사용자 지정 spark2-defaults로 이동하고 다음을 사용하여 새 속성을 추가한 후, Spark2 서비스를 다시 시작합니다.

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Spark 셸에서 Azure Cosmos DB Cassandra API에 액세스

Spark 셸은 테스트/탐색 용도로 사용됩니다.

* 사용자 클러스터의 Spark 버전과 호환되는 필수 Maven 종속성이 포함된 spark 셸을 시작합니다.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* 일부 DDL 및 DML 작업 실행

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* CRUD 작업 실행

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Jupyter Notebooks에서 Azure Cosmos DB Cassandra API에 액세스

HDInsight-Spark는 Zeppelin 및 Jupyter Notebook 서비스와 함께 제공됩니다. 모두 Scala 및 Python을 지원하는 웹 기반 Notebook 환경입니다. Notebook은 대화형 예비 분석 및 공동 작업에 적합하지만 운영/프로덕션 프로세스용은 아닙니다.

다음 Jupyter Notebooks는 HDInsight Spark 클러스터에 업로드할 수 있으며, Azure Cosmos DB Cassandra API를 사용하기 위한 준비 샘플을 제공합니다. 첫 번째 Notebook `1.0-ReadMe.ipynb`를 검토하여 Azure Cosmos DB Cassandra API에 연결하기 위한 Spark 서비스 구성을 검토해 보세요.

머신에서 이러한 Notebook을 [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/)에서 다운로드하세요.
  
### <a name="how-to-upload"></a>업로드하는 방법:
Jupyter를 시작하는 경우 Scala로 이동합니다. 디렉터리를 먼저 만들고 디렉터리에 Notebook을 업로드합니다. 업로드 단추는 오른쪽 위에 있습니다.  

### <a name="how-to-run"></a>실행 방법:
Notebook 및 각 Notebook 셀을 순차적으로 실행합니다.  각 Notebook 위에 있는 실행 단추를 클릭하여 모든 셀을 실행하거나 각 셀에서 Shift + Enter 키를 누릅니다.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Spark Scala 프로그램에서 Azure Cosmos DB Cassandra API로 액세스

프로덕션 환경의 자동화된 프로세스에서는 Spark 프로그램이 [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)을 통해 클러스터에 제출됩니다.

## <a name="next-steps"></a>다음 단계

* [IDE에서 Spark Scala 프로그램을 빌드하고 실행을 위해 Livy를 통해 HDInsight Spark 클러스터에 제출하는 방법](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Spark Scala 프로그램에서 Azure Cosmos DB Cassandra API에 연결하는 방법](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Cassandra API 사용을 위한 코드 샘플의 전체 목록](cassandra-spark-generic.md)
