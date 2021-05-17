---
title: 스토리지 계정 만들기
titleSuffix: Azure Storage
description: Blob, 파일, 큐, 테이블을 저장할 스토리지 계정을 만드는 방법에 대해 알아봅니다. Azure 스토리지 계정은 데이터를 읽고 쓰기 위해 Microsoft Azure에 고유한 네임스페이스를 제공합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/11/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 73bef1648b08f5c1e0664ef1da15375bc44149cc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045351"
---
# <a name="create-a-storage-account"></a>스토리지 계정 만들기

Azure Storage 계정에는 Blob, 파일, 큐, 테이블, 디스크 등, 모든 Azure Storage 데이터 개체가 포함됩니다. 스토리지 계정은 HTTP 또는 HTTPS를 통해 전 세계 어디서나 액세스할 수 있는 Azure Storage 데이터에 고유한 네임스페이스를 제공합니다. Azure Storage 계정의 데이터는 지속적이고 가용성이 높으묘 안전하고 대규모로 확장 가능합니다.

이 방법 문서에서는 [Azure Portal](https://portal.azure.com/), [Azure PowerShell](/powershell/azure/), [Azure CLI](/cli/azure) 또는 [Azure Resource Manager 템플릿](../../azure-resource-manager/management/overview.md)을 사용하여 스토리지 계정을 만드는 방법에 대해 알아봅니다.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

없음

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Azure Storage 계정을 만들려면 Azure PowerShell 모듈 Az 버전 0.7 이상을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

현재 버전을 확인하려면 다음 명령을 실행합니다.

```powershell
Get-InstalledModule -Name "Az"
```

Azure PowerShell을 설치 또는 업그레이드하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure에 로그인하고 다음 두 방법 중 하나로 Azure CLI 명령을 실행할 수 있습니다.

- Azure Portal 내에서 Azure Cloud Shell을 사용하여 CLI 명령을 실행합니다.
- CLI를 설치하고 로컬로 CLI를 실행합니다.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell 사용

Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성됩니다. Azure Portal의 오른쪽 위 섹션에 있는 메뉴에서 **Cloud Shell** 단추를 클릭합니다.

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

이 단추는 이 방법 문서에 간략히 설명된 단계를 실행하는 데 사용할 수 있는 대화형 셸을 시작합니다.

[![포털에서 Cloud Shell 창을 보여 주는 스크린샷](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>로컬에서 CLI 설치

Azure CLI를 로컬에서 설치하여 사용할 수도 있습니다. 이 문서의 예제에는 Azure CLI 버전 2.0.4 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

# <a name="template"></a>[템플릿](#tab/template)

없음

---

## <a name="sign-in-to-azure"></a>Azure에 로그인

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com)에 로그인합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell을 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

CLI의 로컬 설치에 로그인하려면 [az login](/cli/azure/reference-index#az-login) 명령을 실행합니다.

```azurecli-interactive
az login
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

모든 스토리지 계정은 Azure 리소스 그룹에 속해야 합니다. 리소스 그룹은 Azure 리소스를 그룹화하기 위한 논리적 컨테이너입니다. 스토리지 계정을 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있는 옵션이 있습니다. 이 문서에서는 새 리소스 그룹을 만드는 방법을 보여줍니다.

**범용 v2** 저장소 계정은 모든 Azure Storage 서비스(Blob, 파일, 큐, 테이블 및 디스크)에 대한 액세스를 제공합니다. 여기서 설명하는 단계에서는 범용 v2 스토리지 계정을 만들지만, 모든 유형의 스토리지 계정을 만드는 단계가 비슷합니다. 스토리지 계정 유형 및 기타 스토리지 계정 설정에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 PowerShell을 통해 새 리소스 그룹을 만듭니다.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

`-Location` 매개 변수에 어떤 지역을 지정할지 확실하지 않으면 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 명령을 사용하여 해당 구독에 대해 지원되는 지역 목록을 검색할 수 있습니다.

```powershell
Get-AzLocation | select Location
```

그런 다음 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) 명령을 사용하여 읽기 액세스 지역 중복 스토리지(RA-GRS)를 사용하는 범용 v2 스토리지 계정을 만듭니다. 스토리지 계정의 이름은 Azure 전체에서 고유해야 하므로, 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)를 사용하려는 경우 이 매개 변수 목록에 `-EnableHierarchicalNamespace $True`을(를) 포함합니다.

다른 복제 옵션을 사용하여 범용 v2 스토리지 계정을 만들려면 아래 테이블에서 원하는 값을 **SkuName** 매개 변수로 대체합니다.

|복제 옵션  |SkuName 매개 변수  |
|---------|---------|
|LRS(로컬 중복 스토리지)     |Standard_LRS         |
|ZRS(영역 중복 스토리지)     |Standard_ZRS         |
|GRS(지역 중복 스토리지)     |Standard_GRS         |
|읽기 액세스 GRS(지역 중복 스토리지)     |Standard_RAGRS         |
|GZRS(지역 영역 중복 스토리지)    |Standard_GZRS         |
|RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 Azure CLI를 통해 새 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

`--location` 매개 변수에 어떤 지역을 지정할지 확실하지 않으면 [az account list-locations](/cli/azure/account#az_account_list) 명령을 사용하여 해당 구독에 대해 지원되는 지역을 검색할 수 있습니다.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

그런 다음 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용하여 읽기 액세스 지역 중복 스토리지를 사용하는 범용 v2 스토리지 계정을 만듭니다. 스토리지 계정의 이름은 Azure 전체에서 고유해야 하므로, 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)를 사용하려는 경우 이 매개 변수 목록에 `--enable-hierarchical-namespace true`을(를) 포함합니다.

다른 복제 옵션을 사용하여 범용 v2 스토리지 계정을 만들려면 아래 테이블에서 원하는 값을 **sku** 매개 변수로 대체합니다.

|복제 옵션  |sku 매개 변수  |
|---------|---------|
|LRS(로컬 중복 스토리지)     |Standard_LRS         |
|ZRS(영역 중복 스토리지)     |Standard_ZRS         |
|GRS(지역 중복 스토리지)     |Standard_GRS         |
|읽기 액세스 GRS(지역 중복 스토리지)     |Standard_RAGRS         |
|GZRS(지역 영역 중복 스토리지)    |Standard_GZRS         |
|RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)    |Standard_RAGZRS         |

# <a name="template"></a>[템플릿](#tab/template)

Azure PowerShell 또는 Azure CLI를 사용하여 스토리지 계정을 만드는 Resource Manager 템플릿을 배포할 수 있습니다. 이 방법 문서에 사용된 템플릿은 [Azure Resource Manager 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-storage-account-create/)에서 가져온 것입니다. 스크립트를 실행하려면 **사용해 보세요.** 를 선택하여 Azure Cloud Shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> 이 템플릿은 예제로만 사용됩니다. 이 템플릿의 일부로 구성되지 않은 스토리지 계정 설정이 많이 있습니다. 예를 들어 [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)를 사용하려는 경우 `StorageAccountPropertiesCreateParameters` 개체의 `isHnsEnabledad` 속성을 `true`(으)로 선택하여 이 템플릿을 수정합니다. 

이 템플릿을 수정하거나 새 템플릿을 만드는 방법에 대해 알아보려면 다음을 참조하세요.

- [Azure Resource Manager 설명서](../../azure-resource-manager/index.yml)
- [스토리지 계정 템플릿 참조](/azure/templates/microsoft.storage/allversions).
- [추가 스토리지 계정 템플릿 샘플](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>스토리지 계정 삭제

스토리지 계정 삭제는 계정에 있는 모든 데이터를 포함한 전체 계정을 삭제하며, 실행 취소할 수 없습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. **삭제** 를 클릭합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

스토리지 계정을 삭제하려면 [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) 명령을 사용합니다.

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

스토리지 계정을 삭제하려면 [az storage account delete](/cli/azure/storage/account#az-storage-account-delete) 명령을 사용합니다.

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[템플릿](#tab/template)

스토리지 계정을 삭제하려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

또는 리소스 그룹을 삭제하여 해당 리소스 그룹에 있는 스토리지 계정과 기타 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하는 방법에 대한 자세한 내용은 [리소스 그룹 및 리소스 삭제](../../azure-resource-manager/management/delete-resource-group.md)를 참조하세요.

> [!WARNING]
> 삭제된 스토리지 계정을 복원할 수 없거나 삭제 전에 포함된 콘텐츠를 검색할 수 없습니다. 계정을 삭제하기 전에 저장할 내용을 백업했는지 확인합니다. 또한 해당 계정의 리소스에 대해 true를 유지합니다. Blob, 테이블, 큐 또는 파일을 삭제하면 영구적으로 삭제됩니다.
>
> Azure 가상 머신과 연결된 스토리지 계정을 삭제하려고 하는 경우 아직 사용 중인 스토리지 계정에 대한 오류가 발생할 수 있습니다. 이 오류의 문제를 해결하는 도움말은 [스토리지 계정 삭제 시 발생하는 오류 문제 해결](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Storage 계정 개요](storage-account-overview.md)
- [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)
- [Azure Storage 계정을 다른 지역으로 이동](storage-account-move.md)
- [삭제된 스토리지 계정 복구](storage-account-recover.md)