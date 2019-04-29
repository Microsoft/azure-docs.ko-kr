---
title: Spark에서 Azure CosmosDB Cassandra API에 대해 수행하는 삭제 작업
description: 이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API의 테이블 데이터를 삭제하는 방법을 자세히 설명합니다.
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 03/18/2019
ms.openlocfilehash: fc9e0dabd226952bea3bd14da0747c5b6292c2e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893968"
---
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->
# <a name="delete-data-in-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API 테이블의 데이터 삭제

이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API의 데이터를 삭제하는 방법을 설명합니다.

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

## <a name="sample-data-generator"></a>샘플 데이터 생성기
이 코드 조각을 사용하여 샘플 데이터를 생성합니다.

```scala
//Create dataframe
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="dataframe-api"></a>데이터 프레임 API

### <a name="delete-rows-that-match-a-condition"></a>조건과 일치하는 행 삭제

```scala
//1) Create dataframe
val deleteBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .filter("book_pub_year = 1887")

//2) Review execution plan
deleteBooksDF.explain

//3) Review table data before execution
println("==================")
println("1) Before")
deleteBooksDF.show
println("==================")

//4) Delete selected records in dataframe
println("==================")
println("2a) Starting delete")

//Reuse connection for each partition
val cdbConnector = CassandraConnector(sc)
deleteBooksDF.foreachPartition(partition => {
  cdbConnector.withSessionDo(session =>
    partition.foreach{ book => 
        val delete = s"DELETE FROM books_ks.books where book_id='"+book.getString(0) +"';"
        session.execute(delete)
    })
})

println("2b) Completed delete")
println("==================")

//5) Review table data after delete operation
println("3) After")
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .show
```

**출력:**

```
== Physical Plan ==
*(1) Filter (isnotnull(book_pub_year#486) && (book_pub_year#486 = 1887))
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@197cfae4 [book_id#482,book_author#483,book_name#484,book_price#485,book_pub_year#486] 
PushedFilters: [IsNotNull(book_pub_year), EqualTo(book_pub_year,1887)], 
ReadSchema: struct<book_id:string,book_author:string,book_name:string,book_price:float,book_pub_year:int>
==================
1) Before
+-------+------------------+------------------+----------+-------------+
|book_id|       book_author|         book_name|book_price|book_pub_year|
+-------+------------------+------------------+----------+-------------+
| b00001|Arthur Conan Doyle|A study in scarlet|     11.33|         1887|
+-------+------------------+------------------+----------+-------------+

==================
==================
2a) Starting delete
2b) Completed delete
==================
3) After
+-------+------------------+--------------------+----------+-------------+
|book_id|       book_author|           book_name|book_price|book_pub_year|
+-------+------------------+--------------------+----------+-------------+
| b00300|Arthur Conan Doyle|The hounds of Bas...|     12.25|         1901|
| b03999|Arthur Conan Doyle|The adventure of ...|      null|         1892|
| b00023|Arthur Conan Doyle|      A sign of four|     22.45|         1890|
| b00501|Arthur Conan Doyle|The memoirs of Sh...|     14.22|         1893|
| b01001|Arthur Conan Doyle|The adventures of...|     19.83|         1892|
| b02999|Arthur Conan Doyle|  A case of identity|      15.0|         1891|
+-------+------------------+--------------------+----------+-------------+

deleteBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_id: string, book_author: string ... 3 more fields]
cdbConnector: com.datastax.spark.connector.cql.CassandraConnector = com.datastax.spark.connector.cql.CassandraConnector@187deb43
```

### <a name="delete-all-the-rows-in-the-table"></a>테이블의 모든 행 삭제

```scala
//1) Create dataframe
val deleteBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

//2) Review execution plan
deleteBooksDF.explain

//3) Review table data before execution
println("==================")
println("1) Before")
deleteBooksDF.show
println("==================")

//4) Delete records in dataframe
println("==================")
println("2a) Starting delete")

//Reuse connection for each partition
val cdbConnector = CassandraConnector(sc)
deleteBooksDF.foreachPartition(partition => {
  cdbConnector.withSessionDo(session =>
    partition.foreach{ book => 
        val delete = s"DELETE FROM books_ks.books where book_id='"+book.getString(0) +"';"
        session.execute(delete)
    })
})

println("2b) Completed delete")
println("==================")

//5) Review table data after delete operation
println("3) After")
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .show
```

**출력:**

```
== Physical Plan ==
*(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@495377d7 [book_id#565,book_author#566,book_name#567,book_price#568,book_pub_year#569] 
PushedFilters: [], 
ReadSchema: struct<book_id:string,book_author:string,book_name:string,book_price:float,book_pub_year:int>
==================
1) Before
+-------+------------------+--------------------+----------+-------------+
|book_id|       book_author|           book_name|book_price|book_pub_year|
+-------+------------------+--------------------+----------+-------------+
| b00300|Arthur Conan Doyle|The hounds of Bas...|     12.25|         1901|
| b03999|Arthur Conan Doyle|The adventure of ...|      null|         1892|
| b00023|Arthur Conan Doyle|      A sign of four|     22.45|         1890|
| b00501|Arthur Conan Doyle|The memoirs of Sh...|     14.22|         1893|
| b01001|Arthur Conan Doyle|The adventures of...|     19.83|         1892|
| b02999|Arthur Conan Doyle|  A case of identity|      15.0|         1891|
+-------+------------------+--------------------+----------+-------------+

==================
==================
2a) Starting delete
2b) Completed delete
==================
3) After
+-------+-----------+---------+----------+-------------+
|book_id|book_author|book_name|book_price|book_pub_year|
+-------+-----------+---------+----------+-------------+
+-------+-----------+---------+----------+-------------+
```

## <a name="rdd-api"></a>RDD API

### <a name="delete-all-the-rows-in-the-table"></a>테이블의 모든 행 삭제
```scala
//1) Create RDD with all rows
val deleteBooksRDD = 
    sc.cassandraTable("books_ks", "books")

//2) Review table data before execution
println("==================")
println("1) Before")
deleteBooksRDD.collect.foreach(println)
println("==================")

//3) Delete selected records in dataframe
println("==================")
println("2a) Starting delete")

/* Option 1: 
// Not supported currently
sc.cassandraTable("books_ks", "books")
  .where("book_pub_year = 1891")
  .deleteFromCassandra("books_ks", "books")
*/

//Option 2: CassandraConnector and CQL
//Reuse connection for each partition
val cdbConnector = CassandraConnector(sc)
deleteBooksRDD.foreachPartition(partition => {
    cdbConnector.withSessionDo(session =>
    partition.foreach{book => 
        val delete = s"DELETE FROM books_ks.books where book_id='"+ book.getString(0) +"';"
        session.execute(delete)
    }
   )
})

println("Completed delete")
println("==================")

println("2b) Completed delete")
println("==================")

//5) Review table data after delete operation
println("3) After")
sc.cassandraTable("books_ks", "books").collect.foreach(println)
```
**출력:**

```
==================
1) Before
CassandraRow{book_id: b00300, book_author: Arthur Conan Doyle, book_name: The hounds of Baskerville, book_price: 12.25, book_pub_year: 1901}
CassandraRow{book_id: b00001, book_author: Arthur Conan Doyle, book_name: A study in scarlet, book_price: 11.33, book_pub_year: 1887}
CassandraRow{book_id: b00023, book_author: Arthur Conan Doyle, book_name: A sign of four, book_price: 22.45, book_pub_year: 1890}
CassandraRow{book_id: b00501, book_author: Arthur Conan Doyle, book_name: The memoirs of Sherlock Holmes, book_price: 14.22, book_pub_year: 1893}
CassandraRow{book_id: b01001, book_author: Arthur Conan Doyle, book_name: The adventures of Sherlock Holmes, book_price: 19.83, book_pub_year: 1892}
==================
==================
2a) Starting delete
Completed delete
==================
2b) Completed delete
==================
3) After
deleteBooksRDD: com.datastax.spark.connector.rdd.CassandraTableScanRDD[com.datastax.spark.connector.CassandraRow] = CassandraTableScanRDD[126] at RDD at CassandraRDD.scala:19
cdbConnector: com.datastax.spark.connector.cql.CassandraConnector = com.datastax.spark.connector.cql.CassandraConnector@317927
```

### <a name="delete-specific-columns"></a>특정 열 삭제

```scala
//1) Create RDD 
val deleteBooksRDD = 
    sc.cassandraTable("books_ks", "books")

//2) Review table data before execution
println("==================")
println("1) Before")
deleteBooksRDD.collect.foreach(println)
println("==================")

//3) Delete specific column values
println("==================")
println("2a) Starting delete of book price")

sc.cassandraTable("books_ks", "books")
  .deleteFromCassandra("books_ks", "books",SomeColumns("book_price"))

println("Completed delete")
println("==================")

println("2b) Completed delete")
println("==================")

//5) Review table data after delete operation
println("3) After")
sc.cassandraTable("books_ks", "books").take(4).foreach(println)
```

**출력:**

```
==================
1) Before
CassandraRow{book_id: b00300, book_author: Arthur Conan Doyle, book_name: The hounds of Baskerville, book_price: 20.0, book_pub_year: 1901}
CassandraRow{book_id: b00001, book_author: Arthur Conan Doyle, book_name: A study in scarlet, book_price: 23.0, book_pub_year: 1887}
CassandraRow{book_id: b00023, book_author: Arthur Conan Doyle, book_name: A sign of four, book_price: 11.0, book_pub_year: 1890}
CassandraRow{book_id: b00501, book_author: Arthur Conan Doyle, book_name: The memoirs of Sherlock Holmes, book_price: 5.0, book_pub_year: 1893}
CassandraRow{book_id: b01001, book_author: Arthur Conan Doyle, book_name: The adventures of Sherlock Holmes, book_price: 10.0, book_pub_year: 1892}
==================
==================
2a) Starting delete of book price
Completed delete
==================
2b) Completed delete
==================
3) After
CassandraRow{book_id: b00300, book_author: Arthur Conan Doyle, book_name: The hounds of Baskerville, book_price: null, book_pub_year: 1901}
CassandraRow{book_id: b00001, book_author: Arthur Conan Doyle, book_name: A study in scarlet, book_price: null, book_pub_year: 1887}
CassandraRow{book_id: b00023, book_author: Arthur Conan Doyle, book_name: A sign of four, book_price: null, book_pub_year: 1890}
CassandraRow{book_id: b00501, book_author: Arthur Conan Doyle, book_name: The memoirs of Sherlock Holmes, book_price: null, book_pub_year: 1893}
deleteBooksRDD: com.datastax.spark.connector.rdd.CassandraTableScanRDD[com.datastax.spark.connector.CassandraRow] = CassandraTableScanRDD[145] at RDD at CassandraRDD.scala:19
```

## <a name="next-steps"></a>다음 단계

집계 및 데이터 복사 작업을 수행하려면 다음을 참조하세요.

* [집계 작업](cassandra-spark-aggregation-ops.md)
* [테이블 복사 작업](cassandra-spark-table-copy-ops.md)

<!--Verify sucessfully-->
<!--Update_Description: new articles on cassandra spark delete ops -->
<!--ms.date: 03/18/2019-->