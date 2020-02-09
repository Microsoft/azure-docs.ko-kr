---
title: 리소스 관리자 템플릿을 사용 하 여 Azure Cosmos DB 만들기 및 관리
description: Azure Resource Manager 템플릿을 사용 하 여 SQL (Core) API에 대한 Azure Cosmos DB 만들기 및 구성
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: ce9ca202ee57fe9c42906ebeb62a44c724bd1a52
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978720"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB SQL (코어) API 리소스 관리

이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너의 관리를 자동화 하는 방법에 대해 알아봅니다.

이 문서에서는 SQL API 계정에 대한 Azure Resource Manager 템플릿 예제만 보여 줍니다. [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)및 [Table](manage-table-with-resource-manager.md) api에 대한 템플릿 예제를 찾을 수도 있습니다.

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기

다음 Azure Resource Manager 템플릿에서는를 사용 하 여 Azure Cosmos 계정을 만듭니다.

* 데이터베이스 수준에서 400 요청 단위 (r u/초) 처리량을 공유 하는 두 개의 컨테이너입니다.
* 전용 400 r u/초 처리량을 가진 하나의 컨테이너

Azure Cosmos DB 리소스를 만들려면 다음 예제 템플릿을 복사 하 고 [PowerShell](#deploy-via-powershell) 또는 [Azure CLI](#deploy-via-azure-cli)를 통해 설명 된 대로 배포 합니다.

* 필요한 경우 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) 를 방문 하 여 Azure Portal에서 템플릿을 배포할 수 있습니다.
* 로컬 컴퓨터에 템플릿을 다운로드 하거나 새 템플릿을 만들고 `--template-file` 매개 변수를 사용 하 여 로컬 경로를 지정할 수도 있습니다.

> [!IMPORTANT]
>
> * Azure Cosmos 계정에 위치를 추가 하거나 제거 하는 경우 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행 해야 합니다.
> * 계정 이름은 44 자 (모두 소문자)로 제한 됩니다.
> * 처리량 값을 변경 하려면 업데이트 된 r u/s를 사용 하 여 템플릿을 다시 전송 합니다.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> 파티션 키가 많은 컨테이너를 만들려면 `partitionKey` 개체 내에 `"version":2` 속성을 포함 하도록 이전 템플릿을 수정 합니다.

### <a name="deploy-via-powershell"></a>PowerShell을 통해 배포

PowerShell을 사용 하 여 Azure Resource Manager 템플릿을 배포 하려면 다음을 수행 합니다.

1. 스크립트를 **복사** 합니다.
2. **시도** 를 선택 하 여 Azure Cloud Shell을 엽니다.
3. Azure Cloud Shell 창을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell 대신 로컬로 설치 된 PowerShell 버전을 사용 하 여 템플릿을 배포 하도록 선택할 수 있습니다. [Azure PowerShell 모듈을 설치](/powershell/azure/install-az-ps)해야 합니다. `Get-Module -ListAvailable Az`를 실행 하 여 필요한 버전을 찾습니다.

### <a name="deploy-via-azure-cli"></a>Azure CLI를 통해 배포

Azure CLI를 사용 하 여 Azure Resource Manager 템플릿을 배포 하려면 다음을 수행 합니다.

1. 스크립트를 **복사** 합니다.
2. **시도** 를 선택 하 여 Azure Cloud Shell을 엽니다.
3. Azure Cloud Shell 창을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` 명령은 프로 비전 된 후 새로 만든 Azure Cosmos 계정을 보여 줍니다. Azure Cloud Shell 대신 로컬로 설치 된 Azure CLI 버전으로 템플릿을 배포 하도록 선택할 수 있습니다. 자세한 내용은 [Azure 명령줄 인터페이스 (CLI)](/cli/azure/) 문서를 참조 하세요.

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>서버 쪽 기능을 사용 하 여 Azure Cosmos DB 컨테이너 만들기

Azure Resource Manager 템플릿을 사용 하 여 저장 프로시저, 트리거 및 사용자 정의 함수를 사용 하 여 Azure Cosmos DB 컨테이너를 만들 수 있습니다.

다음 예제 템플릿을 복사 하 고 [PowerShell](#deploy-with-powershell) 또는 [Azure CLI](#deploy-with-azure-cli)에 설명 된 대로 배포 합니다.

* 필요한 경우 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) 를 방문 하 여 Azure Portal에서 템플릿을 배포할 수 있습니다.
* 로컬 컴퓨터에 템플릿을 다운로드 하거나 새 템플릿을 만들고 `--template-file` 매개 변수를 사용 하 여 로컬 경로를 지정할 수도 있습니다.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-with-powershell"></a>Powershell을 사용하여 배포

PowerShell을 사용 하 여 Azure Resource Manager 템플릿을 배포 하려면 다음을 수행 합니다.

1. 스크립트를 **복사** 합니다.
1. **시도** 를 선택 하 여 Azure Cloud Shell을 엽니다.
1. Azure Cloud Shell 창을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell 대신 로컬로 설치 된 PowerShell 버전을 사용 하 여 템플릿을 배포 하도록 선택할 수 있습니다. [Azure PowerShell 모듈을 설치](/powershell/azure/install-az-ps)해야 합니다. `Get-Module -ListAvailable Az`를 실행 하 여 필요한 버전을 찾습니다.

### <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

Azure CLI를 사용 하 여 Azure Resource Manager 템플릿을 배포 하려면 다음을 수행 합니다.

1. 스크립트를 **복사** 합니다.
2. **시도** 를 선택 하 여 Azure Cloud Shell을 엽니다.
3. Azure Cloud Shell 창을 마우스 오른쪽 단추로 클릭 한 다음 **붙여넣기**를 선택 합니다.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

* [Azure Resource Manager 설명서](/azure/azure-resource-manager/)
* [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [일반적인 Azure Resource Manager 배포 오류 문제 해결](../azure-resource-manager/templates/common-deployment-errors.md)
