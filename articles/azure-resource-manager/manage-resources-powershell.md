---
title: Azure PowerShell을 사용 하 여 Azure 리소스 관리 | Microsoft Docs
description: 리소스를 관리 하려면 Azure PowerShell 및 Azure 리소스 관리를 사용 합니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 51ef6f3f8ac18b71064f73f32597c1f59ffa1d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075322"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 Azure 리소스 관리

사용 하 여 Azure PowerShell을 사용 하는 방법을 알아봅니다 [Azure Resource Manager](resource-group-overview.md) Azure 리소스를 관리할 수 있습니다. 리소스 그룹 관리에 대 한 참조 [Azure PowerShell을 사용 하 여 관리 하는 Azure 리소스 그룹](./manage-resource-groups-powershell.md)합니다.

리소스를 관리 하는 방법에 대 한 다른 문서:

- [Azure portal을 사용 하 여 Azure 리소스 관리](./manage-resources-portal.md)
- [Azure CLI를 사용 하 여 Azure 리소스 관리](./manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>기존 리소스 그룹에 리소스 배포

Azure PowerShell을 사용 하 여 직접 Azure 리소스를 배포 하거나 Azure 리소스를 만드는 Resource Manager 템플릿을 배포할 수 있습니다.

### <a name="deploy-a-resource"></a>리소스 배포

다음 스크립트는 저장소 계정을 만듭니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>템플릿 배포

다음 스크립트를 만들고 저장소 계정을 만들려면 빠른 시작 템플릿을 배포 합니다. 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)를 참조하세요.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](./resource-group-template-deploy.md)를 참조하세요.

## <a name="deploy-a-resource-group-and-resources"></a>리소스 그룹 및 리소스 배포

리소스 그룹을 만들고 그룹에 리소스를 배포할 수 있습니다. 자세한 내용은 [리소스 그룹 만들기 및 리소스 배포](./deploy-to-subscription.md#create-resource-group-and-deploy-resources)를 참조하세요.

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>여러 구독 또는 리소스 그룹에 리소스 배포

일반적으로 단일 리소스 그룹에 템플릿의 모든 리소스를 배포합니다. 그러나 일단의 리소스를 함께 배포하고 다른 리소스 그룹 또는 구독에 배치하려는 시나리오가 있습니다. 자세한 내용은 [여러 구독 또는 리소스 그룹에 Azure 리소스 배포](./resource-manager-cross-resource-group-deployment.md)합니다.

## <a name="delete-resources"></a>리소스 삭제

다음 스크립트는 저장소 계정을 삭제 하는 방법을 보여줍니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Azure Resource Manager 리소스의 삭제를 정렬 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Resource Manager 리소스 그룹 삭제](./resource-group-delete.md)합니다.

## <a name="move-resources"></a>리소스 이동

다음 스크립트를 다른 리소스 그룹에 하나의 리소스 그룹에서 저장소 계정을 제거 하는 방법을 보여 줍니다.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## <a name="lock-resources"></a>리소스 잠금

잠그면 실수로 삭제 하거나 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요 한 리소스를 수정 하면 조직의 다른 사용자가 수 없습니다. 

다음 스크립트를 계정을 삭제할 수 없게 저장소 계정을 잠급니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

다음 스크립트는 저장소 계정에 대 한 모든 잠금을 가져옵니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

다음 스크립트는 저장소 계정의 잠금을 삭제합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

## <a name="tag-resources"></a>리소스 태그 지정

리소스 그룹 및 리소스를 논리적으로 구성 하는 데 도움이 됩니다. 태그를 지정 합니다. 정보를 참조 하세요 [태그를 사용 하 여 Azure 리소스 구성에](./resource-group-using-tags.md#powershell)입니다.

## <a name="manage-access-to-resources"></a>리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC 및 Azure PowerShell을 사용 하 여 액세스를 관리](../role-based-access-control/role-assignments-powershell.md)합니다.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 알아보려면 [Azure Resource Manager 개요](./resource-group-overview.md)합니다.
- Resource Manager 템플릿 구문에 알아보려면 [구조 및 Azure Resource Manager 템플릿의 구문 이해](./resource-group-authoring-templates.md)합니다.
- 템플릿을 개발 하는 방법에 알아보려면 참조를 [단계별 자습서](/azure/azure-resource-manager/)합니다.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)합니다.
