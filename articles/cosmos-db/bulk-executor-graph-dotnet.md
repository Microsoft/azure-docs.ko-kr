---
title: Azure Cosmos DB Gremlin API에서 Graph Bulk Executor .NET 라이브러리 사용
description: Bulk Executor 라이브러리를 사용하여 그래프 데이터를 대량으로 Azure Cosmos DB Gremlin API 컨테이너로 가져오는 방법을 알아봅니다.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: lbosq
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 6fd81a844832fbe6ad7410ec786baa431ca9930c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004105"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Graph Bulk Executor .NET 라이브러리를 사용하여 Azure Cosmos DB Gremlin API에서 대량 작업 수행

이 자습서는 Azure Cosmos DB의 Bulk Executor .NET 라이브러리를 사용하여 그래프 개체를 Azure Cosmos DB Gremlin API 컨테이너로 가져오고 업데이트하는 방법에 대한 지침을 제공합니다. 이 프로세스에서는 [Bulk Executor 라이브러리](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview)의 Graph 클래스를 사용하여 꼭짓점 및 에지 개체를 프로그래밍 방식으로 만든 다음, 네트워크 요청에 따라 여러 개체를 삽입합니다. 이 동작은 데이터베이스와 로컬 메모리 리소스 사용을 최적화하도록 Bulk Executor 라이브러리를 통해 구성할 수 있습니다.

Gremlin을 데이터베이스로 보낼 때 명령을 평가한 후 한 번에 하나씩 실행하는 것과는 다르게, Bulk Executor 라이브러리를 사용하면 개체를 로컬로 만들고 유효성을 검사해야 합니다. 개체를 만든 후에는 라이브러리를 사용하여 데이터베이스 서비스에 순차적으로 그래프 개체를 보낼 수 있습니다. 이 방법을 사용하면 데이터 수집 속도를 100배까지 높일 수 있으므로 초기 데이터 마이그레이션 또는 정기 데이터 이동 작업에 이상적입니다. 자세한 내용을 알아보려면 [Azure Cosmos DB Graph Bulk Executor 샘플 애플리케이션](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)의 GitHub 페이지를 방문하세요.

## <a name="bulk-operations-with-graph-data"></a>그래프 데이터를 사용한 대량 작업

[Bulk Executor 라이브러리](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet)는 그래프 개체를 만들고 가져오는 기능을 제공하는 `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` 네임 스페이스를 포함하고 있습니다. 

다음 프로세스는 Gremlin API 컨테이너에 데이터 마이그레이션을 사용하는 방법을 설명합니다.
1. 데이터 원본에서 레코드를 검색합니다.
2. 가져온 레코드에서 `GremlinVertex` 및 `GremlinEdge` 개체를 생성하여 `IEnumerable` 데이터 구조에 추가합니다. 데이터 원본이 그래프 데이터베이스가 아닌 경우 애플리케이션의 이 부분에서 관계를 검색하여 추가하는 논리를 구현해야 합니다.
3. [Graph BulkImportAsync 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet)를 사용하여 그래프 개체를 컬렉션에 삽입합니다.

이 메커니즘은 Gremlin 클라이언트를 사용하는 방법과 비교하여 데이터 마이그레이션 효율성이 향상됩니다. 효율성이 향상되는 이유는, Gremlin을 사용하여 데이터를 삽입할 경우 애플리케이션이 데이터를 만들기 위해 유효성을 검사하고 평가하고 실행해야 할 쿼리를 한 번에 보내야 하기 때문입니다. Bulk Executor 라이브러리는 애플리케이션에서 유효성 검사를 처리하고 네트워크 요청마다 여러 그래프 개체를 한 번에 보냅니다.

### <a name="creating-vertices-and-edges"></a>꼭짓점 및 에지 만들기

`GraphBulkExecutor`는 `GremlinVertex` 또는 `GremlinEdge` 개체의 `IEnumerable` 목록이 필요한 `BulkImportAsync` 메서드를 제공하며, 두 개체는 모두 `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element` 네임스페이스에서 정의됩니다. 이 샘플에서는 에지와 꼭짓점을 두 개의 BulkExecutor 가져오기 작업으로 분리했습니다. 아래 예제를 참조하세요.

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Bulk Executor 라이브러리의 매개 변수에 대한 자세한 내용은 [Azure Cosmos DB 토픽으로 데이터 대량으로 가져오기](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)를 참조하세요.

페이로드를 `GremlinVertex` 및 `GremlinEdge` 개체로 인스턴스화해야 합니다. 이러한 개체를 만드는 방법은 다음과 같습니다.

**꼭짓점**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**에지**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Bulk Executor 유틸리티는 에지를 추가하기 전에 기존 꼭짓점을 자동으로 확인하지 않습니다. BulkImport 작업을 실행하기 전에 애플리케이션에서 이 부분의 유효성을 검사해야 합니다.

## <a name="sample-application"></a>샘플 애플리케이션

### <a name="prerequisites"></a>필수 구성 요소
* Azure 개발 워크로드를 사용하는 Visual Studio 2019. [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/)을 무료로 시작할 수 있습니다.
* Azure 구독 [여기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db)서 무료 Azure 계정을 만들 수 있습니다. 또는 Azure 구독 없이 [Azure Cosmos DB 평가판 사용해보기](https://azure.microsoft.com/try/cosmosdb/)로 Cosmos 데이터베이스 계정을 만들 수 있습니다.
* **무제한 컬렉션**을 사용하는 Azure Cosmos DB Gremlin API 데이터베이스. 이 가이드에서는 [.NET의 Azure Cosmos DB Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet)를 시작하는 방법을 보여줍니다.
* Git. 자세한 내용은 [Git 다운로드 페이지](https://git-scm.com/downloads)를 참조하세요.

### <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제
이 자습서에서는 GitHub에 호스트된 [Azure Cosmos DB Graph BulkExecutor 샘플](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)을 사용하여 시작하는 단계를 수행합니다. 이 애플리케이션은 임의로 꼭짓점 및 에지 개체를 생성한 후 지정된 그래프 데이터베이스 계정에 대량으로 삽입하는 .NET 솔루션으로 구성됩니다. 이 애플리케이션을 받으려면 아래의 `git clone` 명령을 실행합니다.

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

이 리포지토리에는 다음 파일이 포함된 GraphBulkExecutor 샘플이 들어 있습니다.

파일|Description
---|---
`App.config`|여기서 애플리케이션 및 데이터베이스 관련 매개 변수를 지정합니다. 대상 데이터베이스 및 컬렉션에 연결하도록 이 파일을 먼저 수정해야 합니다.
`Program.cs`| 이 파일에는 `DocumentClient` 컬렉션을 만들고 Bulk Executor 요청의 정리 및 전송을 처리하는 논리가 들어 있습니다.
`Util.cs`| 이 파일에는 테스트 데이터를 만들고 데이터베이스 및 컬렉션이 있는지 확인하는 논리를 포함하는 도우미 클래스가 들어 있습니다.

`App.config` 파일에서, 다음은 입력 가능한 구성 값입니다.

설정|Description
---|---
`EndPointUrl`|Azure Cosmos DB Gremlin API 데이터베이스 계정의 개요 블레이드에서 찾을 수 있는 **.NET SDK 엔드포인트**입니다. `https://your-graph-database-account.documents.azure.com:443/` 형식입니다.
`AuthorizationKey`|Azure Cosmos DB 계정 아래에 나열되는 기본 키 또는 보조 키입니다. [Azure Cosmos DB 리소스에 대한 액세스 보호](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys)에 대해 자세히 알아보세요.
`DatabaseName`, `CollectionName`|**대상 데이터베이스 및 컬렉션 이름**입니다. `CollectionThroughput`과 함께 `ShouldCleanupOnStart`를 `true`로 설정하면 이러한 값을 사용하여 데이터베이스 및 컬렉션이 삭제되고 새 데이터베이스 및 컬렉션이 생성됩니다. 마찬가지로, `ShouldCleanupOnFinish`를 `true`로 설정하면 수집이 끝나는 즉시 이러한 값을 사용하여 데이터베이스가 삭제됩니다. 대상 컬렉션은 **무제한 컬렉션**이어야 합니다.
`CollectionThroughput`|`ShouldCleanupOnStart` 옵션을 `true`로 설정할 경우 새 컬렉션을 만드는 데 사용됩니다.
`ShouldCleanupOnStart`|이렇게 하면 프로그램을 실행하기 전에 데이터베이스 계정 및 컬렉션이 삭제되고, `DatabaseName`, `CollectionName` 및 `CollectionThroughput` 값을 사용하여 새로 생성됩니다.
`ShouldCleanupOnFinish`|이렇게 하면 프로그램이 실행된 후 지정된 `DatabaseName` 및 `CollectionName`을 사용하여 데이터베이스 계정과 컬렉션이 삭제됩니다.
`NumberOfDocumentsToImport`|이렇게 하면 샘플에 부여할 테스트 꼭짓점 및 에지의 수가 결정됩니다. 이 숫자는 꼭짓점과 에지 둘 다에 적용됩니다.
`NumberOfBatches`|이렇게 하면 샘플에 부여할 테스트 꼭짓점 및 에지의 수가 결정됩니다. 이 숫자는 꼭짓점과 에지 둘 다에 적용됩니다.
`CollectionPartitionKey`|테스트 꼭짓점 및 에지를 만드는 데 사용되며, 이 속성은 자동으로 할당됩니다. 또한 `ShouldCleanupOnStart` 옵션을 `true`로 설정할 경우 데이터베이스 및 컬렉션을 다시 만드는 데 사용됩니다.

### <a name="run-the-sample-application"></a>샘플 애플리케이션 실행

1. `App.config`에서 특정 데이터베이스 구성 매개 변수를 추가합니다. DocumentClient 인스턴스를 만드는 데 사용됩니다. 데이터베이스 및 컨테이너가 아직 생성되지 않은 경우 자동으로 생성됩니다.
2. 애플리케이션을 실행합니다. `BulkImportAsync`를 두 번 호출합니다. 한 번은 꼭짓점을 가져오는 호출이고, 다른 한 번은 에지를 가져오는 호출입니다. 개체를 삽입할 때 개체에서 오류가 발생할 경우 해당 개체는 `.\BadVertices.txt` 또는 `.\BadEdges.txt`에 추가됩니다.
3. 그래프 데이터베이스를 쿼리하여 결과를 평가하세요. `ShouldCleanupOnFinish` 옵션을 true로 설정하면 데이터베이스가 자동으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계

* 대량 실행자 .NET 라이브러리의 NuGet 패키지 정보 및 릴리스 정보에 대해 자세히 알아보려면 [대량 실행자 SDK 세부 정보](sql-api-sdk-bulk-executor-dot-net.md)를 참조 하세요. 
* Bulk Executor 사용을 더 최적화하는 방법은 [성능 팁](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips)을 확인하세요.
* [BulkExecutor.Graph 참조 문서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet)를 검토하여 이 네임 스페이스에 정의된 클래스 및 메서드에 대해 자세히 알아보세요.