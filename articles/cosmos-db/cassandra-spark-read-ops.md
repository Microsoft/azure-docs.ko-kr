---
title: Spark를 사용하여 Cassandra API 테이블 데이터 읽기
titleSufix: Azure Cosmos DB
description: 이 문서에서는 Azure Cosmos DB의 Cassandra API 테이블의 데이터를 읽는 방법을 설명합니다.
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
origin.date: 12/06/2018
ms.date: 03/18/2019
ms.custom: seodec18
ms.openlocfilehash: 01a9582062d8eb0d039473a03901fc83fe179020
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893403"
---
<!--Verify sucessfully-->
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Spark를 사용하여 Azure Cosmos DB Cassandra API 테이블의 데이터 읽기

 이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API에 저장된 데이터를 읽는 방법을 설명합니다.

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

### <a name="read-table-using-sessionreadformat-command"></a>session.read.format 명령을 사용하여 테이블 읽기

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>spark.read.cassandraFormat을 사용하여 테이블 읽기 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>테이블의 특정 열 읽기

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>필터 적용

현재는 조건자 푸시다운이 지원되지 않습니다. 아래 샘플은 클라이언트 쪽 필터링을 반영합니다. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

## <a name="rdd-api"></a>RDD API

### <a name="read-table"></a>테이블 읽기
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>테이블의 특정 열 읽기

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL 보기 

### <a name="create-a-temporary-view-from-a-dataframe"></a>데이터 프레임에서 임시 보기 만들기

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>보기에 대해 쿼리 실행

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>다음 단계

아래에는 Spark에서 Azure Cosmos DB Cassandra API로 작업하는 방법을 소개하는 추가 문서가 나와 있습니다.

 * [upsert 작업](cassandra-spark-upsert-ops.md)
 * [삭제 작업](cassandra-spark-delete-ops.md)
 * [집계 작업](cassandra-spark-aggregation-ops.md)
 * [테이블 복사 작업](cassandra-spark-table-copy-ops.md)

<!--Verify sucessfully-->
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->