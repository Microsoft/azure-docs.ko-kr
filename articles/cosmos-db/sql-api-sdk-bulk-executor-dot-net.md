---
title: Azure Cosmos DB는 대량 실행기 .NET API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB bulk executor .NET SDK의 각 버전 간 변경 내용을 포함하여 bulk executor .NET API 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 5944cbeea2e26d7209cde7c926f862683c7cab7e
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590203"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 대량 실행기 라이브러리: 정보 다운로드 

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
| **설명**| 클라이언트 애플리케이션은 .NET 대량 실행기 라이브러리를 통해 Azure Cosmos DB 계정에서 대량 작업을 수행할 수 있습니다. 이 라이브러리는 BulkImport, BulkUpdate 및 BulkDelete 네임스페이스를 제공합니다. BulkImport 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 수집할 수 있습니다. BulkUpdate 모듈은 Azure Cosmos 컨테이너의 기존 데이터를 패치로 대량 업데이트할 수 있습니다. BulkDelete 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 삭제할 수 있습니다.|
|**SDK 다운로드**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Github의 대량 실행기**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 설명서**|[.NET API 참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**시작**|[Bulk Executor 라이브러리 .NET SDK 시작](bulk-executor-dot-net.md)|
| **현재 지원되는 프레임워크**| Microsoft .NET Framework 4.5.2, 4.6.1 및 .NET Standard 2.0 |

> [!NOTE]
> 대량 실행기를 사용하는 경우 SDK에 대량 실행기가 기본으로 제공되는 [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md)의 최신 3.x 버전을 참조하세요. 

## <a name="release-notes"></a>릴리스 정보

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* 재시도 횟수를 포함하여 총 시간을 정확하게 측정하기 위해 BulkDelete 응답의 TotalElapsedTime 수정.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-preview

* SDK 종속성을 2.5.1 이상으로 변경

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* 꼭짓점 및 가장자리에서 ttl을 허용하도록 그래프 대량 실행기에 대한 지원 추가

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* 게이트웨이 모드에서 실행 시 Azure Cosmos DB의 탄력적 크기 조정 도중 예외가 발생하는 문제 해결. 이로써 1.4.1 릴리스와 기능적으로 차이가 없어집니다.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* SQL API 계정에서 삭제할 파티션 키, 문서 ID 튜플을 수락하도록 BulkDelete 지원 추가. 이로써 1.4.0 릴리스와 기능적으로 차이가 없어집니다.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* .NET Standard 2.0을 지원하는 MongoBulkExecutor가 포함되어 있습니다. 이 기능은 1.3.0 릴리스와 기능적으로 동일하며, 대상 프레임워크로 .NET Standard 2.0 지원이 추가되었습니다.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* 대량 실행기 라이브러리가 .NET Core 애플리케이션과 함께 작동하도록 지원되는 대상 프레임워크 중 하나로 .NET Standard 2.0 추가.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* 이스케이프 된 따옴표가 있는 값이 입력 매개 변수로 전달 되는 경우 BulkDeleteAsync 문제가 해결 되었습니다.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* 패딩 추가 및 경우에 따라 최대 문서 크기 제한 초과로 인해 문서 크기를 예기치 않게 늘린 MongoBulkExecutor 관련 문제 해결.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* 컬렉션에 중첩된 파티션 키 경로가 있는 경우 BulkDeleteAsync와 관련된 문제 해결.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* 이제 MongoBulkExecutor는 IDisposable을 구현하며, 사용 후에는 삭제될 것입니다.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* SDK 버전에 대한 잠금 제거. 이제 패키지는 SDK 2.5.1 이상에 종속됩니다.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* 숫자 값을 포함한 POCO 개체 목록을 사용하여 BulkImport 호출 시 식별자 처리 수정.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* 재시도 횟수를 포함하여 총 시간을 정확하게 측정하기 위해 BulkDelete 응답의 TotalElapsedTime 수정.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* 특정 시나리오에서 높은 CPU 사용량 수정.
* 이제 추적에서 TraceSource를 사용합니다. 사용자가 `BulkExecutorTrace` 소스에 대한 수신기를 정의할 수 있습니다.
* 크기가 약 2Mb인 문서를 보낼 때 잠금을 유발할 수 있는 드문 시나리오 수정.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* 이제 최신 버전의 Azure Cosmos DB .NET SDK(2.4.0)를 사용하도록 대량 실행기를 업데이트했습니다.

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* 꼭짓점 및 가장자리에서 ttl을 허용하도록 그래프 대량 실행기에 대한 지원 추가

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* 게이트웨이 모드에서 실행 시 Azure Cosmos DB의 탄력적 크기 조정 도중 예외가 발생하는 문제 해결.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* SQL API 계정에서 삭제할 파티션 키, 문서 ID 튜플을 수락하도록 BulkDelete 지원 추가.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* 대량 실행기에서 사용하는 사용자 에이전트에서 발생하는 서식 문제 수정.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* 스토리지가 예외를 throw 하지 않고 현재 용량을 초과하면 Cosmos 컨테이너의 탄력적 크기 조정에 맞게 투명하게 조정되도록 대량 실행기 가져오기 및 업데이트 API가 개선되었습니다.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* DocumentDB .NET SDK 종속성이 버전 2.1.3으로 향상되었습니다.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* 수정된 컬렉션으로 가져오는 동안 대량 실행기가 JSRT 오류를 throw하는 문제가 수정되었습니다.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Azure Cosmos DB SQL API 계정용 BulkDelete 작업에 대한 지원이 추가되었습니다.
* Azure Cosmos DB의 MongoDB API 계정용 BulkImport 작업에 대한 지원이 추가되었습니다.
* DocumentDB.NET SDK 종속성이 버전 2.0.0으로 향상되었습니다. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Azure Cosmos DB Gremlin API 계정용 BulkImport 작업에 대한 지원이 추가되었습니다.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Azure Cosmos DB SQL API 계정용 BulkImport 작업에 대한 사소한 버그가 수정되었습니다.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Azure Cosmos DB SQL API 계정용 BulkImport 및 BulkUpdate 작업에 대한 지원이 추가되었습니다.

## <a name="next-steps"></a>다음 단계

대량 실행기 Java 라이브러리에 대해 자세히 알아보려면 다음 문서를 참조하세요.

[Java 대량 실행기 라이브러리 SDK 및 릴리스 정보](sql-api-sdk-bulk-executor-java.md)
