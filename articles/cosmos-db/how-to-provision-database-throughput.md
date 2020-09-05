---
title: Azure Cosmos DB에서 데이터베이스 처리량 프로비전
description: Azure Portal, CLI, PowerShell 및 기타 다양한 SDK를 사용하여 Azure Cosmos DB의 데이터베이스 수준에서 처리량을 프로비저닝하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/28/2019
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 668aa51bdb57dc4bcde0e3a95c481bb60e3d8ed3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997373"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db"></a>Azure Cosmos DB의 데이터베이스에 대한 표준(수동) 처리량 프로비전

이 문서에서는 Azure Cosmos DB의 데이터베이스에 대한 표준(수동) 처리량을 프로비저닝하는 방법을 설명합니다. 단일 [컨테이너](how-to-provision-container-throughput.md) 또는 데이터베이스의 처리량을 프로비전하고 그 안에 있는 컨테이너 간에 처리량을 공유할 수 있습니다. 컨테이너 수준 및 데이터베이스 수준 처리량을 언제 사용하는지 알아보려면 [컨테이너 및 데이터베이스의 처리량 프로비저닝 사용 사례](set-throughput.md) 문서를 참조하세요. Azure Portal 또는 Azure Cosmos DB SDK를 사용하여 데이터베이스 수준 처리량을 프로비저닝할 수 있습니다.

## <a name="provision-throughput-using-azure-portal"></a>Azure Portal을 사용하여 처리량 프로비전

### <a name="sql-core-api"></a><a id="portal-sql"></a>SQL(Core) API

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-sql-api-dotnet.md#create-account) 기존 Azure Cosmos 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 데이터베이스**를 선택합니다. 다음과 같은 세부 정보를 제공합니다.

   * 데이터베이스 ID를 입력합니다.
   * **처리량 프로비전**을 선택합니다.
   * 처리량을 입력합니다(예: 1000RU).
   * **확인**을 선택합니다.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png" alt-text="새 데이터베이스 대화 상자 스크린샷":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell을 사용하여 처리량 프로비전

공유 처리량을 사용하여 데이터베이스를 만들려면 다음을 참조하세요.

* [Azure CLI를 사용하여 데이터베이스 만들기](manage-with-cli.md#create-a-database-with-shared-throughput)
* [PowerShell을 사용하여 데이터베이스 만들기](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>.NET SDK를 사용하여 처리량 프로비전

> [!Note]
> SQL API용 Cosmos SDK를 사용하여 모든 API의 처리량을 프로비저닝할 수 있습니다. 필요에 따라 Cassandra API에도 아래 예제를 사용할 수 있습니다.

### <a name="all-apis"></a><a id="dotnet-all"></a>모든 API

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

CQL 규격 드라이버를 통해 비슷한 명령을 실행할 수 있습니다.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```
 
## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 프로비저닝된 처리량에 대한 자세한 내용은 다음 문서를 참조하세요.

* [프로비저닝된 처리량을 전역적으로 크기 조정](scaling-throughput.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [컨테이너에 표준(수동) 처리량을 프로비저닝하는 방법](how-to-provision-container-throughput.md)
* [컨테이너에 자동 크기 조정 처리량을 프로비저닝하는 방법](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)
