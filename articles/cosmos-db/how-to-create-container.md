---
title: Azure Cosmos DB에서 컨테이너 만들기
description: Azure Portal, .Net, Java, Python, node.js 및 기타 Sdk를 사용 하 여 Azure Cosmos DB에서 컨테이너를 만드는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873712"
---
# <a name="create-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너 만들기

이 문서에서는 Azure Cosmos 컨테이너(컬렉션, 테이블 또는 그래프)를 만드는 여러 가지 방법을 설명합니다. 이를 위해 Azure Portal, Azure CLI 또는 지원되는 SDK를 사용할 수 있습니다. 이 문서에서는 컨테이너를 만들고 파티션 키를 지정하고 처리량을 프로비저닝하는 방법을 보여줍니다.

## <a name="create-a-container-using-azure-portal"></a>Azure Portal을 사용하여 컨테이너 만들기

### <a id="portal-sql"></a>SQL API

1. [Azure portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-sql-api-dotnet.md#create-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 컨테이너**를 선택 합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * 컨테이너 ID를 입력 하십시오.
   * 파티션 키를 입력합니다.
   * 프로비저닝할 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    ![새 컨테이너가 강조 표시 된 데이터 탐색기 창의 스크린샷](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>Azure Cosmos DB API for MongoDB

1. [Azure portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-mongodb-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 컨테이너**를 선택 합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * 컨테이너 ID를 입력 하십시오.
   * 분할 키를 입력합니다.
   * 프로비저닝할 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    ![MongoDB API에 대한 Azure Cosmos DB API, 컨테이너 추가 대화 상자 스크린샷](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. [Azure portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-cassandra-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 테이블**을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 키스페이스를 만드는지 아니면 기존 키스페이스를 사용하는지 표시합니다.
   * 테이블 이름을 입력합니다.
   * 속성을 입력하고 기본 키를 지정합니다.
   * 프로비저닝할 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    ![Cassandra API, 테이블 추가 대화 상자의 스크린샷](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API의 경우 기본 키가 파티션 키로 사용됩니다.

### <a id="portal-gremlin"></a>Gremlin API

1. [Azure portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-graph-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 그래프**를 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 새 데이터베이스를 만드는지 아니면 기존 데이터베이스를 사용하는지 표시합니다.
   * Graph ID를 입력합니다.
   * **무제한** 스토리지 용량을 선택합니다.
   * 꼭짓점의 파티션 키를 입력합니다.
   * 프로비저닝할 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    ![Gremlin API, 그래프 추가 대화 상자의 스크린샷](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Table API

1. [Azure portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-table-dotnet.md#create-a-database-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 테이블**을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 테이블 ID를 입력합니다.
   * 프로비저닝할 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    ![Table API, 테이블 추가 대화 상자의 스크린샷](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Table API의 경우 새 행을 추가할 때마다 파티션 키가 지정됩니다.

## Azure CLI를 사용 하 여 컨테이너 만들기<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

아래 링크는 Azure CLI를 사용 하 여 Azure Cosmos DB에 대한 컨테이너 리소스를 만드는 방법을 보여 줍니다.

모든 Azure Cosmos DB Api에 대한 모든 Azure CLI 샘플 목록은 [SQL api](cli-samples.md), [CASSANDRA API](cli-samples-cassandra.md), [MongoDB api](cli-samples-mongodb.md), [Gremlin api](cli-samples-gremlin.md)및 [Table API](cli-samples-table.md) 를 참조 하세요.

* [Azure CLI를 사용 하 여 컨테이너 만들기](manage-with-cli.md#create-a-container)
* [Azure CLI를 사용 하 여 MongoDB API에 대한 Azure Cosmos DB 컬렉션 만들기](./scripts/cli/mongodb/create.md)
* [Azure CLI를 사용 하 여 Cassandra 테이블 만들기](./scripts/cli/cassandra/create.md)
* [Azure CLI를 사용 하 여 Gremlin 그래프 만들기](./scripts/cli/gremlin/create.md)
* [Azure CLI를 사용 하 여 Table API 테이블 만들기](./scripts/cli/table/create.md)

## PowerShell<a id="ps-sql"> </a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin">를 사용 하 여 컨테이너 만들기<a id="ps-table"></a>

아래 링크는 PowerShell을 사용 하 여 Azure Cosmos DB에 대한 컨테이너 리소스를 만드는 방법을 보여 줍니다.

모든 Azure Cosmos DB Api에 대한 모든 Azure CLI 샘플 목록은 [SQL api](powershell-samples-sql.md), [CASSANDRA API](powershell-samples-cassandra.md), [MongoDB api](powershell-samples-mongodb.md), [Gremlin api](powershell-samples-gremlin.md)및 [Table API](powershell-samples-table.md) 를 참조 하세요.

* [Powershell을 사용 하 여 컨테이너 만들기](manage-with-powershell.md#create-container)
* [Powershell을 사용 하 여 MongoDB API에 대한 Azure Cosmos DB 컬렉션 만들기](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Powershell을 사용 하 여 Cassandra 테이블 만들기](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Powershell을 사용 하 여 Gremlin 그래프 만들기](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Powershell을 사용 하 여 Table API 테이블 만들기](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>.NET SDK를 사용하여 컨테이너 만들기

### <a id="dotnet-sql-graph"></a>SQL API 및 Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>Azure Cosmos DB API for MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB 유선 프로토콜은 [요청 단위](request-units.md) 개념을 이해하지 못합니다. 프로비저닝된 처리량을 사용하여 새 컬렉션을 만들려면 SQL API용 Azure Portal 또는 Cosmos DB SDK를 사용합니다.

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](account-overview.md)
