---
title: Spark에서 Azure Cosmos DB Cassandra API에 데이터 Upsert
description: 이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API의 테이블로 Upsert하는 방법을 자세히 설명합니다.
author: rockboyfor
ms.author: v-yeche
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
origin.date: 09/24/2018
ms.date: 03/18/2019
ms.openlocfilehash: 7770e7fbe846defc865b3fcc702fcb00bae1b73c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893420"
---
<!--Verify sucessfully-->
# <a name="upsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API에 데이터 Upsert

이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API로 데이터를 Upsert하는 방법을 설명합니다.

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

### <a name="create-a-dataframe"></a>데이터 프레임 만들기 

```scala
// (1) Update: Changing author name to include prefix of "Sir"
// (2) Insert: adding a new book

val booksUpsertDF = Seq(
    ("b00001", "Sir Arthur Conan Doyle", "A study in scarlet", 1887),
    ("b00023", "Sir Arthur Conan Doyle", "A sign of four", 1890),
    ("b01001", "Sir Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
    ("b00501", "Sir Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
    ("b00300", "Sir Arthur Conan Doyle", "The hounds of Baskerville", 1901),
    ("b09999", "Sir Arthur Conan Doyle", "The return of Sherlock Holmes", 1905)
    ).toDF("book_id", "book_author", "book_name", "book_pub_year")
booksUpsertDF.show()
```

### <a name="upsert-data"></a>데이터 Upsert

```scala
// Upsert is no different from create
booksUpsertDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .save()
```

### <a name="update-data"></a>데이터 업데이트

```scala
//This runs on the driver, leverage only for one off updates
cdbConnector.withSessionDo(session => session.execute("update books_ks.books set book_price=99.33 where book_id ='b00300';"))
```

## <a name="rdd-api"></a>RDD API
> [!NOTE]
> RDD API에서의 Upsert는 만들기 작업과 같습니다. 

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB Cassandra API 테이블에 저장된 데이터에 대해 다른 작업을 수행하려면 다음 문서를 계속 진행합니다.

* [삭제 작업](cassandra-spark-delete-ops.md)
* [집계 작업](cassandra-spark-aggregation-ops.md)
* [테이블 복사 작업](cassandra-spark-table-copy-ops.md)

<!--Verify sucessfully-->
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->