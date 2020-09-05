---
title: SQL API 용 스프링 데이터 Azure Cosmos DB v3 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간의 변경 내용을 포함 하 여 SQL API 용 스프링 데이터 Azure Cosmos DB v3에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e67e6911eeac29036dee2b68c19395b34e1d11da
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228038"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Core 용 스프링 데이터 Azure Cosmos DB v3 (SQL) API: 릴리스 정보 및 리소스
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

Core 용 스프링 데이터 Azure Cosmos DB v3 (SQL)을 통해 개발자는 스프링 응용 프로그램에서 Azure Cosmos DB를 활용할 수 있습니다. 스프링 데이터 Azure Cosmos DB는 데이터베이스 및 컬렉션을 조작 하 고, 문서 작업을 수행 하 고, 쿼리를 실행 하기 위한 스프링 데이터 인터페이스를 제공 합니다. 동기화 및 비동기 (대응식) Api는 모두 동일한 Maven 아티팩트에서 지원 됩니다. 

스프링 데이터 Azure Cosmos DB는 스프링 데이터 프레임 워크에 종속 됩니다. Azure Cosmos DB SDK 팀은 스프링 데이터 v 2.2 및 v 2.3에 대 한 Maven 아티팩트를 해제 합니다.

[스프링 프레임 워크](https://spring.io/projects/spring-framework) 는 Java 응용 프로그램 개발을 간소화 하는 프로그래밍 및 구성 모델입니다. 조직의 웹 사이트를 인용 하기 위해 스프링은 종속성 주입을 사용 하 여 응용 프로그램의 "배관"을 간소화 합니다. 응용 프로그램 빌드 및 테스트로 인해 스프링과 같은 많은 개발자 들이 더욱 간편 합니다. [스프링 부팅](https://spring.io/projects/spring-boot) 은 웹 응용 프로그램 및 마이크로 서비스 개발을 중심으로 하는이를 처리 하는 이러한 아이디어를 확장 합니다. [스프링 데이터](https://spring.io/projects/spring-data) 는 스프링 또는 스프링 부팅 응용 프로그램의 컨텍스트에서 Azure Cosmos DB와 같은 데이터 저장소에 액세스 하기 위한 프로그래밍 모델 및 프레임 워크입니다. 

[Azure 스프링 클라우드](https://azure.microsoft.com/services/spring-cloud/) 응용 프로그램에서 스프링 데이터 Azure Cosmos DB를 사용할 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 스프링 데이터 Azure Cosmos DB의 v3 용입니다. [여기](sql-api-sdk-java-spring-v2.md)에서 v2 릴리스 정보를 찾을 수 있습니다. 
>
> 스프링 데이터 Azure Cosmos DB는 SQL API만 지원 합니다.
>
> 다음 가이드에서는 다른 Azure Cosmos DB Api에 대 한 스프링 데이터를 지원 합니다.
> * [Azure Cosmos DB에서 Apache Cassandra에 대 한 스프링 데이터](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB로 스프링 데이터 MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB로 스프링 데이터 Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>여기에서 시작

# <a name="explore"></a>[탐색](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>위의 탭에서 기본 스프링 데이터 Azure Cosmos DB 샘플을 탐색 합니다.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>종속성 구성

두 개의 스프링 데이터 Azure Cosmos DB v3 Maven 아티팩트를 사용할 수 있습니다.

스프링 데이터 프레임 워크 v 2.2에 종속 된 아티팩트:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

스프링 데이터 프레임 워크 v 2.3에 종속 된 아티팩트:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[연결](#tab/connect)

### <a name="connect"></a>연결

Azure Cosmos DB 계정 및 컨테이너 세부 정보를 지정 합니다. 스프링 데이터 Azure Cosmos DB 자동으로 클라이언트를 만들고 컨테이너에 연결 합니다.

[응용 프로그램. 속성](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc ops](#tab/docs)

### <a name="document-operations"></a>문서 작업

[만들기](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[삭제](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[쿼리](#tab/queries)

### <a name="query"></a>쿼리

[쿼리](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>유용한 내용

| Content | 스프링 데이터 프레임 워크 v 2.2 | 스프링 데이터 프레임 워크 v 2.3 |
|---|---|
| **SDK 다운로드** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**SDK에 참여** | [GitHub의 스프링 데이터 Azure Cosmos DB 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [GitHub의 스프링 데이터 Azure Cosmos DB 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**자습서**| [GitHub의 스프링 데이터 Azure Cosmos DB 자습서](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [GitHub의 스프링 데이터 Azure Cosmos DB 자습서](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>릴리스 기록

### <a name="300-beta2-unreleased"></a>3.0.0-beta. 2 (릴리스되지 않음)

### <a name="300-beta1-2020-08-17"></a>3.0.0-beta. 1 (2020-08-17)
#### <a name="new-features"></a>새 기능
* 그룹 ID를로 업데이트 했습니다 `com.azure` .
* 아티팩트 ID를로 업데이트 했습니다 `azure-spring-data-2-3-cosmos` .
* Azure cosmos SDK 종속성을로 업데이트 했습니다 `4.3.2-beta.2` .
* 엔터티 감사 지원-createdBy, Subscription.createddate, lastModifiedBy 및 lastModifiedDate 주석이 추가 된 필드의 자동 관리.
* `@GeneratedValue` 유형의 ID 필드에 대 한 자동 id 생성에 대 한 주석 지원 `String` .
* 여러 데이터베이스를 포함 하는 단일 cosmos 계정 및 여러 데이터베이스를 사용 하는 여러 cosmos 계정에 대 한 다중 데이터베이스 구성 지원.
* `@Version`모든 문자열 필드에 주석을 지원 합니다.
* 업데이트 된 sync Api `Iterable` 는 대신 형식으로 형식을 반환 `List` 합니다.
* `CosmosClientBuilder`COSMOS SDK에서 스프링 bean로 클래스에 노출 `@Configuration` 됩니다.
* `CosmosConfig`쿼리 메트릭 및 응답 진단 프로세서 구현을 포함 하도록 업데이트 되었습니다.
* `Optional`단일 결과 쿼리에 대 한 데이터 형식의 반환 지원.
#### <a name="renames"></a>바꾸기
* `CosmosDbFactory` to `CosmosFactory` .
* `CosmosDBConfig` to `CosmosConfig` .
* `CosmosDBAccessException` to `CosmosAccessException` .
* `Document` 주석에 대 한 주석 `Container` 입니다.
* `DocumentIndexingPolicy` 주석에 대 한 주석 `CosmosIndexingPolicy` 입니다.
* `DocumentQuery` to `CosmosQuery` .
* application. 속성은 `populateQueryMetrics` 에 플래그를 지정 `queryMetricsEnabled` 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* `Parallel`Netty i/o 스레드를 차단 하지 않도록 진단 로깅 작업을 스레드에 예약 합니다.
* 삭제 작업에 대 한 낙관적 잠금을 수정 했습니다.
* 절의 이스케이프 쿼리와 관련 된 문제가 해결 `IN` 되었습니다.
* 의 데이터 형식을 허용 하 여 문제 `long` 를 해결 `@Id` 했습니다.
* ,,를 `boolean` `long` `int` `double` 주석에 대 한 데이터 형식으로 허용 하 여 문제를 해결 `@PartitionKey` 했습니다.
* `IgnoreCase`  &  `AllIgnoreCase` Case 쿼리 무시를 위한 고정 키워드입니다.
* 컨테이너를 자동으로 만들 때 4000의 기본 요청 단위 값을 제거 했습니다.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

스프링 프레임 워크에 대 한 자세한 내용은 [프로젝트 홈 페이지](https://spring.io/projects/spring-framework)를 참조 하세요.

스프링 부팅에 대해 자세히 알아보려면 [프로젝트 홈 페이지](https://spring.io/projects/spring-boot)를 참조 하세요.

스프링 데이터에 대해 자세히 알아보려면 [프로젝트 홈 페이지](https://spring.io/projects/spring-data)를 참조 하세요.