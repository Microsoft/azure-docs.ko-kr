---
title: Azure Cosmos DB는 대량 실행기 Java API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Bulk Executor Java SDK의 각 버전 간 변경 내용을 포함하여 Bulk Executor Java API 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 60c2b3e194fc7703ad1c3f1d3138f9a2c6301f33
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585052"
---
# <a name="java-bulk-executor-library-download-information"></a>Java Bulk Executor 라이브러리: 정보 다운로드

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

| |  |
|---|---|
|**설명**|클라이언트 애플리케이션은 Bulk Executor 라이브러리를 통해 Azure Cosmos DB 계정에서 대량 작업을 수행할 수 있습니다. Bulk Executor 라이브러리는 BulkImport 및 BulkUpdate 네임스페이스를 제공합니다. BulkImport 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 수집할 수 있습니다. BulkUpdate 모듈은 Azure Cosmos 컨테이너의 기존 데이터를 패치로 대량 업데이트할 수 있습니다.|
|**SDK 다운로드**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub의 대량 실행기 라이브러리**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API 설명서**| [Java API 참조 설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**시작**|[Bulk Executor 라이브러리 Java SDK 시작](bulk-executor-java.md)|
|**지원되는 최소 런타임**|[JDK(Java Development Kit) 7 이상](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>릴리스 정보

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* DocumentAnalyzer.java가 json에서 중첩 파티션 키 값을 정확하게 추출하는 데 필요한 픽스

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* BulkDelete 작업에 특정 오류를 다시 시도할 수 있는 기능을 추가하고, 다시 시도할 수 있는 사용자에게 오류 목록을 반환합니다.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Cosmos SDK 버전 2.4.7에 대한 업데이트입니다.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* 'id' 및 파티션 키 값이 업데이트된 항목 목록에 추가된 후 패치가 적용된 문서 속성이 배치되도록 'id' 및 파티션 키 값에 대한 'mergeAll'을 수행하는 데 필요한 픽스입니다.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* 동시성의 시작 수준을 1로 업데이트하고 minibatch의 디버그 로그를 추가했습니다.


