---
title: Azure Cosmos DB는 SQL Async Java API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL 비동기 Java SDK의 각 버전 간 변경 내용을 포함하여 SQL 비동기 Java API 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: b8f45fe07b825008b673534bcc3b28b9364c4cae
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587296"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API용 Azure Cosmos DB Async Java SDK: 릴리스 정보 및 리소스
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
> * 단계 (/rest/api
> * [REST 리소스 공급자](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행자-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

SQL API 비동기 Java SDK는 [Netty 라이브러리](https://netty.io/)를 지원하고 비동기 작업을 제공하기 때문에 SQL API Java SDK와는 다릅니다. 기존 [SQL API Java SDK](sql-api-sdk-java.md)는 비동기 작업을 지원하지 않습니다. 

> [!IMPORTANT]  
> Azure Cosmos DB의 최신 Java SDK가 아닙니다! 프로젝트에 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md)를 사용하는 것이 좋습니다. 업그레이드하려면 [Azure Cosmos DB Java SDK v4로 마이그레이션하기](migrate-java-v4-sdk.md) 안내서 및 [Reactor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 안내서의 지침을 따르세요. 
>

| |  |
|---|---|
| **SDK 다운로드** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API 설명서** |[Java API 참조 설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**SDK에 참여** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**시작** | [비동기 Java SDK 시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**코드 샘플** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **성능 팁**| [Github 추가 정보](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **지원되는 최소 런타임**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

