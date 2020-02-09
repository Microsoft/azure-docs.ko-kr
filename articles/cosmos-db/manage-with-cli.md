---
title: Azure CLI를 사용하여 Azure Cosmos DB 리소스 관리
description: Azure CLI를 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mjbrown
ms.openlocfilehash: 325840f8961fac49e599f1aa567ad8d4137820b4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705805"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure CLI를 사용하여 Azure Cosmos 리소스 관리

다음 가이드에서는 Azure CLI를 사용 하 여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너의 관리를 자동화 하는 일반적인 명령을 설명 합니다. 모든 Azure Cosmos DB CLI 명령에 대한 참조 페이지는 [Azure CLI 참조](https://docs.microsoft.com/cli/azure/cosmosdb)에서 제공됩니다. 더 많은 예제는 [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples.md)에서 확인할 수 있습니다. 여기에는 MongoDB, Gremlin, Cassandra 및 Table API에 대한 Cosmos DB 계정, 데이터베이스 및 컨테이너 만들기 및 관리 방법이 포함되어 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

SQL API를 사용 하 여 Azure Cosmos DB 계정, 미국 서 부 2 및 미국 동부 2 지역에서 세션 일관성을 만듭니다.

> [!IMPORTANT]
> Azure Cosmos 계정 이름은 소문자 여야 하 고 31 자 미만 이어야 합니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 31 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

## <a name="add-or-remove-regions"></a>영역 추가 또는 제거

두 개의 지역이 있는 Azure Cosmos 계정을 만들고 지역을 추가 하 고 지역을 제거 합니다.

> [!NOTE]
> `locations` 영역을 추가하거나 제거하면서 동시에 Azure Cosmos 계정에 대한 다른 속성을 변경할 수 없습니다. 지역 수정은 계정 리소스에 대한 다른 변경 사항보다 별도의 작업으로 수행해야 합니다.
> [!NOTE]
> 이 명령을 사용하면 지역을 추가 및 제거할 수 있지만 장애 조치(failover) 우선 순위를 수정하거나 수동 장애 조치(failover)를 트리거할 수 없습니다. [장애 조치 우선 순위 설정](#set-failover-priority) 및 [수동 장애 조치 트리거](#trigger-manual-failover)를 참조 하세요.

```azurecli-interactive
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount' # must be lower case and <31 characters

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName= "South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False
```

## <a name="enable-multiple-write-regions"></a>여러 쓰기 영역 사용

Cosmos 계정에 다중 마스터 사용

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

## <a name="set-failover-priority"></a>장애 조치 우선 순위 설정

자동 장애 조치 (failover)를 위해 구성 된 Azure Cosmos 계정에 대한 장애 조치 우선 순위 설정

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2'=0 'South Central US'=1 'East US 2'=2
```

## <a name="enable-automatic-failover"></a>자동 장애 조치 (failover) 사용

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

## <a name="trigger-manual-failover"></a>수동 장애 조치 트리거

> [!CAUTION]
> 우선 순위가 0 인 지역을 변경 하면 Azure Cosmos 계정에 대한 수동 장애 조치 (failover)가 트리거됩니다. 다른 우선 순위 변경은 장애 조치(failover)를 트리거하지 않습니다.

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2'=0 'South Central US'=1 'West US 2'=2
```

## <a id="list-account-keys"></a>모든 계정 키 나열

Cosmos 계정에 대한 모든 키를 가져옵니다.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-read-only-account-keys"></a>읽기 전용 계정 키 나열

Cosmos 계정에 대한 읽기 전용 키를 가져옵니다.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

## <a name="list-connection-strings"></a>연결 문자열 나열

Cosmos 계정에 대한 연결 문자열을 가져옵니다.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="regenerate-account-key"></a>계정 키 다시 생성

Cosmos 계정에 대한 새 키를 다시 생성 합니다.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="create-a-database"></a>데이터베이스 만들기

Cosmos 데이터베이스를 만듭니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

## <a name="create-a-database-with-shared-throughput"></a>공유 처리량이 있는 데이터베이스 만들기

공유 처리량이 있는 Cosmos 데이터베이스를 만듭니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

## <a name="change-the-throughput-of-a-database"></a>데이터베이스의 처리량 변경

Cosmos 데이터베이스의 처리량을 1000 o s/s로 늘립니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

## <a name="create-a-container"></a>컨테이너 만들기

기본 인덱스 정책, 파티션 키 및 Cosmos/s (400)를 사용 하 여 컨테이너를 만듭니다.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

## <a name="create-a-container-with-ttl"></a>TTL을 사용 하 여 컨테이너 만들기

TTL을 사용 하는 Cosmos 컨테이너를 만듭니다.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'
databaseName = 'database1'
containerName = 'container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl = 86400
```

## <a name="create-a-container-with-a-custom-index-policy"></a>사용자 지정 인덱스 정책을 사용 하 여 컨테이너 만들기

사용자 지정 인덱스 정책, 공간 인덱스, 복합 인덱스, 파티션 키 및 Cosmos/s (400)를 사용 하 여 컨테이너를 만듭니다.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

## <a name="change-the-throughput-of-a-container"></a>컨테이너 처리량 변경

Cosmos 컨테이너의 처리량을 1000 o s/s로 늘립니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 다음을 참조하세요.

- [Azure CLI 설치](/cli/azure/install-azure-cli)
- [Azure CLI 참조](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Azure Cosmos DB에 대한 Azure CLI 추가 샘플](cli-samples.md)
