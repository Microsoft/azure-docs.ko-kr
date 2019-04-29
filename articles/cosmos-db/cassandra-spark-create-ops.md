---
title: Spark에서 Azure Cosmos DB Cassandra API에서 데이터 만들기/삽입
description: 이 문서에서는 Azure Cosmos DB Cassandra API 테이블에 데이터를 삽입하는 방법을 자세히 설명합니다.
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 03/18/2019
ms.openlocfilehash: aea646e7a390d5b53f0d4b388cfecd0c80fb19da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894048"
---
<!--Verify sucessfully-->
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API에서 데이터 만들기/삽입

이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API의 테이블에 샘플 데이터를 삽입하는 방법을 설명합니다.

## <a name="cassandra-api-configuration"></a>Cassandra API 구성

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.cn")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>데이터 프레임 API

### <a name="create-a-dataframe-with-sample-data"></a>샘플 데이터를 사용하여 데이터 프레임 만들기

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> 행 수준에서 “없는 경우 만들기” 기능은 아직 지원되지 않습니다.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API로 유지

또한 데이터를 저장할 때 다음 예제와 같이 Time-to-Live 및 일관성 정책 설정을 설정할 수 있습니다.

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> 열 수준 TTL은 아직 지원되지 않습니다.

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>RDD(복원력 있는 분산된 데이터베이스) API

### <a name="create-a-rdd-with-sample-data"></a>샘플 데이터로 RDD 만들기
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> 없는 경우 만들기 기능은 아직 지원되지 않습니다.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API로 유지

또한 Cassandra API에 데이터를 저장할 때 다음 예제와 같이 Time-to-Live 및 일관성 정책 설정을 설정할 수 있습니다.

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB Cassandra API 테이블에 데이터를 삽입한 후 Cosmos DB Cassandra API에 저장된 데이터에 대해 다른 작업을 수행하려면 다음 문서를 계속 진행합니다.

* [읽기 작업](cassandra-spark-read-ops.md)
* [Upsert 작업](cassandra-spark-upsert-ops.md)
* [삭제 작업](cassandra-spark-delete-ops.md)
* [집계 작업](cassandra-spark-aggregation-ops.md)
* [테이블 복사 작업](cassandra-spark-table-copy-ops.md)

<!--Verify sucessfully-->
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->