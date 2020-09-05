---
title: Azure Cosmos DB에서 자동 크기 조정 처리량 프로비전
description: Azure Portal, CLI, PowerShell 및 기타 다양한 SDK를 사용하여 Azure Cosmos DB의 컨테이너 및 데이터베이스 수준에서 자동 크기 조정 처리량을 프로비전하는 방법을 알아봅니다.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4e7c5f3f4bf84b7a267cb883df5f375f2a8cf981
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017144"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db"></a>Azure Cosmos DB의 데이터베이스 또는 컨테이너에 자동 크기 조정 처리량 프로비전

이 문서에서는 Azure Cosmos DB에서 데이터베이스 또는 컨테이너(컬렉션, 그래프 또는 테이블)에 자동 크기 조정 처리량을 프로비전하는 방법을 설명합니다. 단일 컨테이너에서 자동 크기 조정을 사용하도록 설정하거나 데이터베이스에서 자동 크기 조정 처리량을 프로비전하고 데이터베이스의 모든 컨테이너 간에 공유할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

### <a name="create-new-database-or-container-with-autoscale"></a>자동 크기 조정을 사용하여 새 데이터베이스 또는 컨테이너 만들기

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Cosmos DB 탐색기](https://cosmos.azure.com/)에 로그인합니다.

1. Azure Cosmos DB 계정으로 이동하여 **데이터 탐색기** 탭을 엽니다.

1. **새 컨테이너**를 선택합니다. 데이터베이스, 컨테이너 및 파티션 키의 이름을 입력합니다. **처리량**에서 **자동 크기 조정** 옵션을 선택하고 데이터베이스 또는 컨테이너의 크기를 조정하려는 [최대 처리량(RU/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works)을 설정합니다.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="컨테이너 만들기 및 자동 크기 조정 프로비전된 처리량 구성":::

1. **확인**을 선택합니다.

공유 처리량 데이터베이스에 자동 크기 조정을 프로비전하려면 새 데이터베이스를 만들 때 **데이터베이스 처리량 프로비전** 옵션을 선택합니다. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>기존 데이터베이스 또는 컨테이너에서 자동 크기 조정 사용

> [!IMPORTANT]
> 현재 릴리스에서 Azure Portal은 자동 크기 조정 및 표준(수동) 프로비전된 처리량 간에 마이그레이션하는 유일한 방법입니다. 

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Cosmos DB 탐색기](https://cosmos.azure.com/)에 로그인합니다.

1. Azure Cosmos DB 계정으로 이동하여 **데이터 탐색기** 탭을 엽니다.

1. 컨테이너에 대한 **크기 조정 및 설정** 또는 데이터베이스에 대한 **크기 조정**을 선택합니다.

1. **크기 조정**에서 **자동 크기 조정** 옵션, **저장**을 선택합니다.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="기존 컨테이너에서 자동 크기 조정 사용":::

> [!NOTE]
> 기존 데이터베이스 또는 컨테이너에서 자동 크기 조정을 사용하도록 설정하면 현재의 수동 프로비전된 처리량 설정 및 스토리지에 따라 최대 RU/s의 시작 값이 시스템에 의해 결정됩니다. 작업이 완료된 후 필요하면 최대 RU/s를 변경할 수 있습니다. [자세한 정보](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk-for-sql-api"></a>SQL API용 Azure Cosmos DB .NET V3 SDK

SQL API용 Azure Cosmos DB .NET SDK의 [버전 3.9 이상](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)을 사용하여 자동 크기 조정 리소스를 관리합니다. 

> [!IMPORTANT]
> .NET SDK를 사용하여 새 자동 크기 조정 리소스를 만들 수 있습니다. 이 SDK는 자동 크기 조정 처리량과 표준(수동) 처리량 간의 마이그레이션을 지원하지 않습니다. 마이그레이션 시나리오는 현재 Azure Portal에서만 지원됩니다. 

### <a name="create-database-with-shared-throughput"></a>공유 처리량을 사용하여 데이터베이스 만들기

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>전용 처리량을 사용하여 컨테이너 만들기

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>현재 처리량(RU/s) 읽기

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>자동 크기 조정 최대 처리량(RU/s) 변경

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk-for-sql-api"></a>SQL API용 Azure Cosmos DB Java V4 SDK

SQL API용 Azure Cosmos DB Java SDK의 [버전 4.0 이상](https://mvnrepository.com/artifact/com.azure/azure-cosmos)을 사용하여 자동 크기 조정 리소스를 관리할 수 있습니다.

> [!IMPORTANT]
> Java SDK를 사용하여 새 자동 크기 조정 리소스를 만들 수 있습니다. 이 SDK는 자동 크기 조정 처리량과 표준(수동) 처리량 간의 마이그레이션을 지원하지 않습니다. 마이그레이션 시나리오는 현재 Azure Portal에서만 지원됩니다.

### <a name="create-database-with-shared-throughput"></a>공유 처리량을 사용하여 데이터베이스 만들기

#### <a name="async"></a>[비동기](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[동기화](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>전용 처리량을 사용하여 컨테이너 만들기

#### <a name="async"></a>[비동기](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[동기화](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>현재 처리량(RU/s) 읽기

#### <a name="async"></a>[비동기](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[동기화](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>자동 크기 조정 최대 처리량(RU/s) 변경

#### <a name="async"></a>[비동기](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[동기화](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="cassandra-api"></a>Cassandra API

[CQL commands](manage-scale-cassandra.md#use-autoscale), [Azure CLI](cli-samples.md), [Azure PowerShell](powershell-samples.md) 또는 [Azure Resource Manager 템플릿을](resource-manager-samples.md)사용 하 여 자동 크기 조정을 위해 Cassandra API에 대 한 Azure Cosmos DB 계정을 프로 비전 할 수 있습니다.

## <a name="azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB API for MongoDB

MongoDB API에 대 한 Azure Cosmos DB 계정은 [MongoDB 확장 명령](mongodb-custom-commands.md), [Azure CLI](cli-samples.md), [Azure PowerShell](powershell-samples.md) 또는 [Azure Resource Manager 템플릿을](resource-manager-samples.md)사용 하 여 자동 크기 조정을 위해 프로 비전 할 수 있습니다.

## <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager 템플릿을 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 예제는 [Azure Cosmos DB Azure Resource Manager 템플릿을](resource-manager-samples.md) 참조 하세요.

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 샘플은 [Azure Cosmos DB Azure CLI 샘플](cli-samples.md)을 참조 하세요.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell를 사용 하 여 데이터베이스에 대 한 자동 크기 조정 처리량 또는 모든 Azure Cosmos DB Api에 대 한 컨테이너 수준 리소스를 프로 비전 할 수 있습니다. 샘플은 [Azure Cosmos DB Azure PowerShell 샘플](powershell-samples.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [자동 크기 조정을 사용하는 프로비전된 처리량의 이점](provision-throughput-autoscale.md#benefits-of-autoscale)에 대해 알아봅니다.
* [수동 및 자동 크기 조정 처리량 중 선택하는 방법](how-to-choose-offer.md)을 알아봅니다.
* [자동 크기 조정 FAQ](autoscale-faq.md)를 검토합니다.
