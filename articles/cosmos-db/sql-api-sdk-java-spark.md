---
title: SQL API 릴리스 정보 및 리소스에 대 한 Azure Cosmos DB Apache Spark 커넥터
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간의 변경 내용을 포함 하 여 SQL API 용 Azure Cosmos DB Apache Spark 커넥터에 대 한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590012"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API에 대 한 Azure Cosmos DB Apache Spark 커넥터: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 변경 피드 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [스프링 데이터 v2](sql-api-sdk-java-spring-v2.md)
> * [스프링 데이터 v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 커넥터](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](/rest/api/cosmos-db/)
> * [REST 리소스 공급자](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행자-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

Core (SQL)에 대 한 Azure Cosmos DB Apache Spark 커넥터를 사용 하 여 빅 데이터 분석을 가속화 합니다. Spark 커넥터를 사용 하면 Azure Cosmos DB에 저장 된 데이터에서 [spark ](https://spark.apache.org/) 작업을 실행할 수 있습니다. 일괄 처리 및 스트림 처리가 지원 됩니다.

Azure에서 관리 되는 Spark 클러스터를 제공 하는 [Azure Databricks](https://azure.microsoft.com/services/databricks) 또는 [azure HDInsight](https://azure.microsoft.com/services/hdinsight/)에서 커넥터를 사용할 수 있습니다. 다음 표에서는 지원 되는 Spark 버전을 보여 줍니다.

| 구성 요소 | 버전 |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2 및 2.1. x |
| Scala | 2.11 |
| Azure Databricks 런타임 버전 | > 3.4 |

> [!WARNING]
> 이 커넥터는 Azure Cosmos DB의 코어 (SQL) API를 지원 합니다.
> MongoDB API에 대 한 Cosmos DB [MongoDB Spark 커넥터](https://docs.mongodb.com/spark-connector/master/)를 사용 합니다.
> Cosmos DB Cassandra API의 경우 [Cassandra Spark 커넥터](https://github.com/datastax/spark-cassandra-connector)를 사용 합니다.
>

## <a name="helpful-content"></a>유용한 내용

| 콘텐츠 | 링크 |
|---|---|
| **SDK 다운로드** | [Apache Spark에서 다운로드](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**API 설명서** | [Spark 커넥터 참조]() |
|**SDK에 참여** | [GitHub의 Apache Spark에 대 한 Azure Cosmos DB 커넥터](https://github.com/Azure/azure-cosmosdb-spark) | 
|**시작** | [Apache Spark-Azure Cosmos DB 커넥터를 사용하여 빅 데이터 분석 가속화](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Apache Kafka 및 Azure Cosmos DB에서 Apache Spark 정형 스트림 사용](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>릴리스 기록

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* "Id"에서 "ChangeFeedMaxPagesPerBatch" 구성이 적용 된 "|" 문자를 포함 하는 스트리밍 검사점에 지 사례를 수정 합니다.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>새로운 기능
* 중첩 된 파티션 키를 사용 하는 경우 대량 업데이트에 대 한 지원을 추가 합니다.
* Cosmos DB에 쓰는 동안 Decimal 및 Float 데이터 형식에 대 한 지원을 추가 합니다.
* Long (unix Epoch)를 값으로 사용 하는 경우 타임 스탬프 형식에 대 한 지원을 추가 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* "WriteThroughputBudget" 구성을 사용 하는 경우 형식 캐스트 예외를 수정 합니다.

### <a name="307"></a>3.0.7 이상을
#### <a name="new-features"></a>새로운 기능
* 대량 실패에 대 한 오류 정보를 예외 및 로그에 추가 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 스트리밍 검사점 문제를 수정 합니다.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 노이즈를 줄이기 위해 잘못 된 수준 오류 메시지를 남겨진 메시지의 로그 수준을 수정 합니다.

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 파티션을 분할 하는 동안 구조적 스트리밍에서 버그를 수정 합니다 .이 경우 일부 변경 피드 레코드가 누락 되거나 검사점 쓰기에 대 한 Null 예외가 표시 될 수 있습니다.

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* ReadStream에 대해 제공 된 사용자 지정 스키마가 무시 되는 버그를 수정 합니다.

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 재발을 수정 합니다 (unshaded JAR에는 모든 음영 처리 된 종속성 포함). 빌드 시간을 50% 증가 시켰습니다.

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* TCP를 통한 직접 전송이 RequestTimeoutException을 사용 하 여 실패 하 게 하는 종속성 문제를 해결 합니다.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>새로운 기능
* 연결 관리 및 연결 풀링을 개선 하 여 메타 데이터 호출 수 줄이기
#### <a name="key-bug-fixes"></a>핵심 버그 수정

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계

Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

Apache Spark에 대 한 자세한 내용은 [홈 페이지](https://spark.apache.org/)를 참조 하세요.