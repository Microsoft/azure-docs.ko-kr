---
title: 'Azure Cosmos DB: 대량 실행자 .NET API, SDK & 리소스'
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB bulk executor .NET SDK의 각 버전 간 변경 내용을 포함하여 bulk executor .NET API 및 SDK에 대한 모든 것을 알아봅니다.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169409"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET Bulk Executor 라이브러리: 다운로드 정보 

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
> * [대량 실행자-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행자-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **설명**| .Net bulk executor 라이브러리를 사용 하면 클라이언트 응용 프로그램이 Azure Cosmos DB 계정에서 대량 작업을 수행할 수 있습니다. 이 라이브러리는 대량 가져오기, 대량 업데이트 및 대량 삭제 네임 스페이스를 제공 합니다. BulkImport 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 수집할 수 있습니다. 대량 업데이트 모듈은 Azure Cosmos 컨테이너의 기존 데이터를 패치로 대량 업데이트할 수 있습니다. BulkDelete 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 삭제할 수 있습니다.|
|**SDK 다운로드**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **GitHub의 대량 실행자 라이브러리**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API 설명서**|[.NET API 참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**시작**|[Bulk Executor 라이브러리 .NET SDK 시작](bulk-executor-dot-net.md)|
| **현재 지원되는 프레임워크**| Microsoft .NET Framework 4.5.2, 4.6.1 및 .NET Standard 2.0 |

> [!NOTE]
> 대량 실행자를 사용 하는 경우 SDK에 기본 제공 되는 대량 실행자를 포함 하는 [.NET sdk](tutorial-sql-api-dotnet-bulk-import.md)의 최신 버전을 참조 하세요. 

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name241-preview241-preview"></a><a name="2.4.1-preview"/>2.4.1-미리 보기

* 재시도 횟수를 포함 하 여 총 시간을 정확 하 게 측정 하기 위해 대량 삭제 응답의 TotalElapsedTime 수정 되었습니다.

### <a name="a-name240-preview240-preview"></a><a name="2.4.0-preview"/>2.4.0-미리 보기

* SDK 종속성을 > = 2.5.1로 변경 했습니다.

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* 꼭 짓 점 및 가장자리에서 ttl을 허용 하도록 그래프 대량 실행자에 대한 지원이 추가 됨

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* 게이트웨이 모드에서 실행 될 때 Azure Cosmos DB 탄력적으로 크기를 조정 하는 동안 예외가 발생 하는 문제를 해결 했습니다. 이 수정 프로그램은 1.4.1 릴리스와 기능적으로 동일 합니다.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* 삭제할 파티션 키, 문서 id 튜플을 허용 하는 SQL API 계정에 대한 대량 삭제 지원이 추가 되었습니다. 이러한 변경으로 인해 1.4.0 릴리스와 기능적으로 동일 합니다.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* .NET Standard 2.0을 지원하는 MongoBulkExecutor가 포함되어 있습니다. 이 기능은 1.3.0 릴리스와 기능적으로 동일하며, 대상 프레임워크로 .NET Standard 2.0 지원이 추가되었습니다.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* 대량 실행자 라이브러리가 .NET Core 응용 프로그램과 함께 작동 하도록 지원 되는 대상 프레임 워크 중 하나로 .NET Standard 2.0를 추가 했습니다.

### <a name="a-name188188"></a><a name="1.8.8"/>1.8.8

* 패딩 및 경우에 따라 최대 문서 크기 제한을 초과 하 여 문서 크기를 예기치 않게 늘린 MongoBulkExecutor의 문제를 해결 했습니다.

### <a name="a-name187187"></a><a name="1.8.7"/>1.8.7

* 컬렉션에 중첩 된 파티션 키 경로가 있는 경우 BulkDeleteAsync와 관련 된 문제가 해결 되었습니다.

### <a name="a-name186186"></a><a name="1.8.6"/>1.8.6

* 이제 MongoBulkExecutor는 IDisposable을 구현 하며 사용 후 삭제 되어야 합니다.

### <a name="a-name185185"></a><a name="1.8.5"/>1.8.5

* SDK 버전에 대한 잠금을 제거 했습니다. 이제 패키지가 SDK > = 2.5.1에 종속 됩니다.

### <a name="a-name184184"></a><a name="1.8.4"/>1.8.4

* 숫자 값을 사용 하 여 POCO 개체 목록을 사용 하 여 대량 가져오기를 호출할 때 식별자 처리가 수정 되었습니다.

### <a name="a-name183183"></a><a name="1.8.3"/>1.8.3

* 재시도 횟수를 포함 하 여 총 시간을 정확 하 게 측정 하기 위해 대량 삭제 응답의 TotalElapsedTime 수정 되었습니다.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* 특정 시나리오에서 높은 CPU 사용량을 수정 했습니다.
* 이제 추적은 TraceSource를 사용 합니다. 사용자는 `BulkExecutorTrace` 소스에 대한 수신기를 정의할 수 있습니다.
* 거의 크기가 2Mb 가까이 문서를 보낼 때 잠금을 유발할 수 있는 드문 시나리오를 수정 했습니다.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* 이제 최신 버전의 Azure Cosmos DB .NET SDK (2.4.0)를 사용 하도록 대량 실행자를 업데이트 했습니다.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* 꼭 짓 점 및 가장자리에서 ttl을 허용 하도록 그래프 대량 실행자에 대한 지원이 추가 됨

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* 게이트웨이 모드에서 실행 될 때 Azure Cosmos DB 탄력적으로 크기를 조정 하는 동안 예외가 발생 하는 문제를 해결 했습니다.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* 삭제할 파티션 키, 문서 id 튜플을 허용 하는 SQL API 계정에 대한 대량 삭제 지원이 추가 되었습니다.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* 대량 실행자에서 사용 하는 사용자 에이전트에서 서식 지정 문제가 발생 한 문제를 해결 했습니다.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 저장소에서 예외를 발생 시 키 지 않고 현재 용량을 초과 하는 경우 Cosmos 컨테이너의 탄력적인 크기 조정에 투명 하 게 적응 하도록 대량 실행자 가져오기 및 업데이트 Api를 개선 했습니다.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* DocumentDB .NET SDK 종속성이 버전 2.1.3으로 향상되었습니다.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 고정 된 컬렉션으로 가져오는 동안 대량 실행자에서 JSRT 오류를 throw 하는 문제가 해결 되었습니다.

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

대량 실행자 Java 라이브러리에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

[Java bulk executor 라이브러리 SDK 및 릴리스 정보](sql-api-sdk-bulk-executor-java.md)
