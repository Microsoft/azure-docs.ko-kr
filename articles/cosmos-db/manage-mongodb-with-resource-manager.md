---
title: MongoDB 용 Azure Cosmos DB API에 대한 리소스 관리자 템플릿
description: Azure Resource Manager 템플릿을 사용 하 여 MongoDB 용 Azure Cosmos DB API를 만들고 구성 합니다.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 408f29322af2199259f59be1f2c7e891a80a6b35
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980586"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB MongoDB API 리소스 관리

이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너의 관리를 자동화 하는 다양 한 작업을 수행 하는 방법을 설명 합니다. 이 문서에는 Azure Cosmos DB의 MongoDB 용 API에 대한 예제만 있습니다. 다른 API 형식 계정에 대한 예제를 찾으려면 Azure Cosmos DB의 API를 사용 하 여 [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [테이블](manage-table-with-resource-manager.md) 아티클에 대한 Azure Resource Manager 템플릿 사용을 참조 하세요.

## MongoDB 계정, 데이터베이스 및 컬렉션에 대한 Azure Cosmos DB API 만들기<a id="create-resource"></a>

Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB 리소스를 만듭니다. 이 템플릿은 데이터베이스 수준에서 400 r u/s 처리량을 공유 하는 두 개의 컬렉션을 사용 하 여 MongoDB API 용 Azure Cosmos 계정을 만듭니다. 템플릿을 복사 하 고 아래와 같이 배포 하거나 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) 를 방문 하 여 Azure Portal에서 배포 합니다. 로컬 컴퓨터에 템플릿을 다운로드 하거나 새 템플릿을 만들고 `--template-file` 매개 변수를 사용 하 여 로컬 경로를 지정할 수도 있습니다.

> [!NOTE]
> 계정 이름은 소문자와 44 자이 하 여야 합니다.
> R u/s를 업데이트 하려면 업데이트 된 처리량 속성 값으로 템플릿을 다시 전송 합니다.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-the-azure-cli"></a>Azure CLI를 통해 배포

Azure CLI를 사용 하 여 Azure Resource Manager 템플릿을 배포 하려면 스크립트를 **복사** 하 고 **사용해 보기** 를 선택 하 여 Azure Cloud Shell를 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 명령은 프로 비전 된 후 새로 만든 Azure Cosmos 계정을 보여 줍니다. Cloud Shell를 사용 하는 대신 로컬로 설치 된 Azure CLI 버전을 사용 하도록 선택 하는 경우 [Azure CLI](/cli/azure/) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

- [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
- [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [일반적인 Azure Resource Manager 배포 오류 문제 해결](../azure-resource-manager/templates/common-deployment-errors.md)