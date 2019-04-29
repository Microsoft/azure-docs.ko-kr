---
title: Azure Cosmos DB는 Bulk Executor .NET API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Bulk Executor .NET SDK의 각 버전 간 변경 내용을 포함하여 Bulk Executor .NET API 및 SDK에 대한 모든 것을 알아봅니다.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 4f2d8b3246901f139695998224dfe036cccb9833
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627153"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET Bulk Executor 라이브러리: 정보 다운로드 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **설명**| 클라이언트 애플리케이션은 Bulk Executor 라이브러리를 통해 Azure Cosmos DB 계정에서 대량 작업을 수행할 수 있습니다. Bulk Executor 라이브러리는 BulkImport, BulkUpdate 및 BulkDelete 네임스페이스를 제공합니다. BulkImport 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 수집할 수 있습니다. BulkUpdate 모듈은 Azure Cosmos DB 컨테이너의 기존 데이터를 패치로 대량 업데이트할 수 있습니다. BulkDelete 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 삭제할 수 있습니다.|
|**SDK 다운로드**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub의 BulkExecutor 라이브러리**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 설명서**|[.NET API 참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**시작**|[Bulk Executor 라이브러리 .NET SDK 시작](bulk-executor-dot-net.md)|
| **현재 지원되는 프레임워크**| Microsoft .NET Framework 4.5.2, 4.6.1 및 .NET Standard 2.0 |

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* .NET Standard 2.0을 지원하는 MongoBulkExecutor가 포함되어 있습니다. 이 기능은 1.3.0 릴리스와 기능적으로 동일하며, 대상 프레임워크로 .NET Standard 2.0 지원이 추가되었습니다.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* BulkExecutor 라이브러리가 .NET Core 애플리케이션과 함께 작동하도록 지원되는 대상 프레임워크 중 하나로 .NET Standard 2.0이 추가되었습니다.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* SQL API 계정에서 삭제할 파티션 키, 문서 id 튜플을 수락하도록 BulkDelete 작업의 오버로드가 추가되었습니다.
* 입력 쿼리에서 삭제할 문서를 지정하는 필터로 사용될 뿐 아니라 파티션 키의 값을 지정하는 파티션 키가 포함된 RequestOptions를 SQL API 계정이 수락하도록 BulkDelete 작업의 오버로드가 추가되었습니다.
* BulkExecutor에서 사용하는 사용자 에이전트에서 발생하는 서식 문제가 수정되었습니다.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 스토리지가 예외를 throw 하지 않고 현재 용량을 초과하면 Cosmos DB 컨테이너의 탄력적 크기 조정에 맞게 투명하게 조정되도록 BulkExecutor 가져오기 및 업데이트 API가 개선되었습니다.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* DocumentDB .NET SDK 종속성이 버전 2.1.3으로 향상되었습니다.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 수정된 컬렉션으로 가져오는 동안 BulkExecutor가 JSRT 오류를 throw 하는 문제가 수정되었습니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Azure Cosmos DB SQL API 계정용 BulkDelete 작업에 대한 지원이 추가되었습니다.
* Azure Cosmos DB의 MongoDB API 계정용 BulkImport 작업에 대한 지원이 추가되었습니다.
* DocumentDB.NET SDK 종속성이 버전 2.0.0으로 향상되었습니다. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Azure Cosmos DB Gremlin API 계정용 BulkImport 작업에 대한 지원이 추가되었습니다.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Azure Cosmos DB SQL API 계정용 BulkImport 작업에 대한 사소한 버그가 수정되었습니다.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Azure Cosmos DB SQL API 계정용 BulkImport 및 BulkUpdate 작업에 대한 지원이 추가되었습니다.

## <a name="next-steps"></a>다음 단계

Bulk Executor Java 라이브러리에 대해 자세히 알아보려면 다음 문서를 참조하세요.

[Java Bulk Executor 라이브러리 SDK 및 릴리스 정보](sql-api-sdk-bulk-executor-java.md)
