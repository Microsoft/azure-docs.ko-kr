---
title: Azure Cosmos DB용 Azure Synapse Link 구성 및 사용(미리 보기)
description: Azure Cosmos 계정에 대해 synapse 링크를 사용하도록 설정하고, 분석 저장소가 사용하도록 설정된 컨테이너를 만들고, Azure Cosmos 데이터베이스를 synapse 작업 영역에 연결하고, 쿼리를 실행하는 방법을 알아봅니다.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: rosouz
ms.openlocfilehash: 4c5f812bf1a5a60a6d1344d6a39fbd95898f55fc
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815575"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Azure Cosmos DB용 Azure Synapse Link 구성 및 사용(미리 보기)

Azure Cosmos DB용 Synapse Link는 클라우드 네이티브 HTAP(하이브리드 트랜잭션 및 분석 처리) 기능으로, 이를 통해 Azure Cosmos DB의 작동 데이터에 대해 근 실시간 분석을 실행할 수 있습니다. Synapse Link를 통해 Azure Cosmos DB와 Azure Synapse Analytics가 긴밀하게 통합됩니다.


> [!IMPORTANT]
> Azure Synapse 링크를 사용 하려면 지원 되는 지역 중 하나에서 azure Cosmos 계정 & Azure Synapse Analytics 작업 영역을 프로 비전 해야 합니다. Azure Synapse 링크는 현재 미국 서 부, 미국 동부, 서 부 미국, 유럽 서 부, 유럽 서부, 남부 중부, 동남 아시아, 오스트레일리아 동부, 동부 U2, 영국 남부의 Azure 지역에서 사용할 수 있습니다.

Azure Cosmos DB용 Synapse Link를 사용하여 분석 쿼리를 실행하려면 다음 단계를 수행합니다.

* [Azure Cosmos 계정에 대해 Synapse Link를 사용하도록 설정](#enable-synapse-link)
* [Azure Cosmos 컨테이너를 사용하도록 설정된 분석 저장소 만들기](#create-analytical-ttl)
* [Synapse 작업 영역에 Azure Cosmos 데이터베이스 연결](#connect-to-cosmos-database)
* [Synapse Spark를 사용하여 분석 저장소 쿼리](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Azure Cosmos 계정에 대해 Azure Synapse Link를 사용하도록 설정

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure 계정을 만들거나](create-sql-api-dotnet.md#create-account) 기존 Azure Cosmos 계정을 선택합니다.

1. Azure Cosmos 계정으로 이동하여 **기능** 창을 엽니다.

1. 기능 목록에서 **Synapse Link**를 선택합니다.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Synapse Link 미리 보기 기능 찾기":::

1. 그런 다음, 계정에서 synapse link를 사용하도록 설정하라는 메시지를 표시합니다. 사용을 선택합니다.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Synapse Link 기능 사용":::

1. 이제 계정이 Synapse Link를 사용하도록 설정되었습니다. 다음으로 트랜잭션 저장소에서 분석 저장소로 작업 데이터 복제를 자동으로 시작하도록 분석 저장소 사용 컨테이너를 만드는 방법을 참조하세요.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

[Azure Resource Manager 템플릿](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store)은 SQL API용 Synapse Link 사용 Azure Cosmos 계정을 만듭니다. 이 템플릿은 분석 TTL이 설정된 컨테이너와 수동 또는 자동 크기 조정 처리량을 사용하는 옵션을 통해 한 지역에 Core(SQL) API 계정을 만듭니다. 이 템플릿을 배포하려면 추가 정보 페이지에서 **Azure에 배포**를 클릭합니다.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> 분석 저장소를 사용하여 Azure Cosmos 컨테이너 만들기

컨테이너를 만드는 동안 Azure Cosmos 컨테이너에서 분석 저장소를 켤 수 있습니다. Azure Cosmos DB SDK를 사용하여 컨테이너를 만드는 동안 Azure Portal을 사용하거나 `analyticalTTL` 속성을 구성할 수 있습니다.

> [!NOTE]
> 현재 새 계정 및 기존 계정 모두 **새** 컨테이너에 대해 분석 저장소를 사용하도록 설정할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/) 또는 [Azure Cosmos explorer](https://cosmos.azure.com/)에 로그인합니다.

1. Azure Cosmos 계정으로 이동하여 **데이터 탐색기** 탭을 엽니다.

1. **새 컨테이너**를 선택하고 데이터베이스, 컨테이너, 파티션 키 및 처리량 정보에 대한 이름을 입력합니다. **분석 저장소** 옵션을 설정합니다. 분석 저장소를 사용하도록 설정하면 `AnalyicalTTL` 속성이 기본값 -1(무한 보존)로 설정된 컨테이너가 만들어집니다. 레코드의 모든 기록 버전을 유지하는 분석 저장소입니다.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Azure Cosmos 컨테이너에 대한 분석 저장소 설정":::

1. 이 계정에 대해 이전에 Synapse Link를 사용하도록 설정하지 않은 경우 분석 저장소를 사용하도록 설정된 컨테이너를 만들기 위한 필수 구성 요소이므로 그렇게 하라는 메시지가 표시됩니다. 메시지가 표시되면 **Synapse Link 사용**을 선택합니다.

1. **확인**을 선택하여 Azure Cosmos 컨테이너를 사용하도록 설정된 분석 저장소를 만듭니다.

### <a name="net-sdk"></a>.NET SDK

다음 코드는 .NET SDK를 사용하여 분석 저장소와 함께 컨테이너를 만듭니다. 분석 TTL 속성을 필요한 값으로 설정합니다. 허용되는 값 목록은 [분석 TTL 지원 값](analytical-store-introduction.md#analytical-ttl) 문서를 참조하세요.

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

다음 코드는 Java V4 SDK를 사용하여 분석 저장소와 함께 컨테이너를 만듭니다. `AnalyticalStoreTimeToLiveInSeconds` 속성을 필요한 값으로 설정합니다.

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Python V4 SDK

Python 2.7 및 Azure Cosmos DB SDK 4.1.0는 필요한 최소 버전이 며 SDK는 SQL API와만 호환 됩니다.

첫 번째 단계는 [Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)의 버전 4.1.0 이상을 사용 하 고 있는지 확인 하는 것입니다.

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
다음 단계에서는 Azure Cosmos DB Python SDK를 사용 하 여 분석 저장소가 있는 컨테이너를 만듭니다.

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> 분석 저장소 시간을 실시간으로 업데이트

특정 TTL 값으로 분석 저장소를 사용하도록 설정한 후 나중에 다른 유효한 값으로 업데이트할 수 있습니다. Azure Portal 또는 SDK를 사용하여 값을 업데이트할 수 있습니다. 다양한 분석 TTL 구성 옵션에 대한 자세한 내용은 [분석 TTL 지원 값](analytical-store-introduction.md#analytical-ttl) 문서를 참조하세요.

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 통해 분석 저장소를 사용하도록 설정된 컨테이너를 만든 경우 -1의 기본 분석 TTL이 포함됩니다. 다음 단계를 사용하여 이 값을 업데이트합니다.

1. [Azure Portal](https://portal.azure.com/) 또는 [Azure Cosmos explorer](https://cosmos.azure.com/)에 로그인합니다.

1. Azure Cosmos 계정으로 이동하여 **데이터 탐색기** 탭을 엽니다.

1. 분석 저장소가 사용하도록 설정된 기존 컨테이너를 선택합니다. 확장하고 다음 값을 수정합니다.

  * **배율 및 설정** 창을 엽니다.
  * **설정**에서 ** 분석 스토리지 TTL(Time to live)**을 찾습니다.
  * **설정(기본값 없음)** 를 선택하거나 **켜기**를 선택하고 TTL 값을 설정합니다.
  * **저장**을 클릭하여 변경 내용을 저장합니다.

#### <a name="net-sdk"></a>.NET SDK

다음 코드는 .NET SDK를 사용하여 분석 저장소에 대한 TTL을 업데이트하는 방법을 보여 줍니다.

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Java V4 SDK

다음 코드에서는 Java V4 SDK를 사용하여 분석 저장소에 대한 TTL을 업데이트하는 방법을 보여 줍니다.

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Synapse 작업 영역에 연결

Azure Synapse Link를 사용하여 Azure Synapse Analytics Studio에서 Azure Cosmos DB 데이터베이스에 액세스하는 방법에 대해 [Azure Synapse Link에 연결](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md)의 지침을 사용합니다.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a> Synapse Spark를 사용하여 쿼리

Synapse Spark를 사용하여 쿼리하는 방법에 대해 [Azure Cosmos DB 분석 저장소 쿼리](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) 문서의 지침을 사용합니다. 이 문서에서는 Synapse 제스처에서 분석 저장소와 상호 작용하는 방법에 대한 몇 가지 예를 제공합니다. 컨테이너를 마우스 오른쪽 단추로 클릭하면 해당 제스처가 표시됩니다. 제스처를 사용하면 코드를 빠르게 생성하고 필요에 맞게 조정할 수 있습니다. 제스처는 한 번의 클릭으로 데이터를 검색하는 데에도 적합합니다.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Azure Synpase Link 시작 - 샘플

[GitHub](https://aka.ms/cosmosdb-synapselink-samples)에서 Azure Synapse 링크를 시작 하는 샘플을 찾을 수 있습니다. 이 샘플은 IoT 및 소매 시나리오를 사용하여 엔드투엔드 솔루션을 보여 줍니다.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Cosmos DB용 Azure Synapse Link](synapse-link.md)

* [Azure Cosmos DB 분석 저장소 개요](analytical-store-introduction.md)

* [Azure Cosmos DB용 Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics의 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)

* [Azure Synapse Analytics의 SQL 서버리스/주문형](../synapse-analytics/sql/on-demand-workspace-overview.md)
