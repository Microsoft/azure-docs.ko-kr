---
title: Spark에서 Azure Cosmos DB Cassandra API의 DDL 작업
description: 이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API에 대한 키스페이스 및 테이블 DDL 작업을 자세히 설명합니다.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5c12787cd6e0df19fd842dd44da49aa5ea97aa05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898885"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API의 DDL 작업

이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API에 대한 키스페이스 및 테이블 DDL 작업을 자세히 설명합니다.

## <a name="cassandra-api-related-configuration"></a>Cassandra API 관련 구성 

```scala
import org.apache.spark.sql.cassandra._

//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
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

## <a name="keyspace-ddl-operations"></a>키스페이스 DDL 작업

### <a name="create-a-keyspace"></a>키스페이스 만들기

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

cqlsh에서 다음 명령을 실행하고 앞에서 만든 키스페이스가 표시되어야 합니다.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>키스페이스 삭제

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>테이블 DDL 작업

**고려 사항:**  

- create table 문을 사용하여 테이블 수준에서 처리량을 할당할 수 있습니다.  
- 하나의 파티션 키는 10GB의 데이터를 저장할 수 있습니다.  
- 하나의 레코드는 최대 2MB의 데이터를 저장할 수 있습니다.  
- 하나의 파티션 키 범위는 여러 파티션 키를 저장할 수 있습니다.

### <a name="create-a-table"></a>테이블 만들기

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

cqlsh에서 다음 명령을 실행하고 "books"라는 테이블이 표시되어야 합니다. 

```bash
USE books_ks;
DESCRIBE books;
```

프로비전된 처리량 및 기본 TTL 값은 이전 명령의 출력에 표시되지 않습니다. 포털에서 이러한 값을 가져올 수 있습니다.

### <a name="alter-table"></a>alter table

alter table 명령을 사용하여 다음 값을 변경할 수 있습니다.

* 프로비전된 처리량 
* TTL(Time to Live) 값
<br>열 변경은 현재 지원되지 않습니다.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>테이블 삭제

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>cqlsh에서 유효성 검사

cqlsh에서 다음 명령을 실행하고 "books" 테이블이 더 이상 지원되지 않는다고 표시되어야 합니다.

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>다음 단계

키스페이스 및 테이블을 만들면 CRUD 작업 등에 대한 다음 문서를 진행합니다.
 
* [만들기/삽입 작업](cassandra-spark-create-ops.md)  
* [읽기 작업](cassandra-spark-read-ops.md)  
* [Upsert 작업](cassandra-spark-upsert-ops.md)  
* [삭제 작업](cassandra-spark-delete-ops.md)  
* [집계 작업](cassandra-spark-aggregation-ops.md)  
* [테이블 복사 작업](cassandra-spark-table-copy-ops.md)  
