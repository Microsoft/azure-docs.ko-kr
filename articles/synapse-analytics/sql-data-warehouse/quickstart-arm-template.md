---
title: Azure Resource Manager 템플릿을 사용하여 SQL 풀 만들기
description: Azure Resource Manager 템플릿을 사용하여 Azure Synapse Analytics SQL 풀을 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 29d4e4d696b34aa493714c870ebb466f491c47fe
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641877"
---
# <a name="quickstart-create-an-azure-synapse-analytics-sql-pool-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Synapse Analytics SQL 풀 만들기

이 ARM 템플릿(Azure Resource Manager 템플릿)은 투명한 데이터 암호화가 활성화된 Azure Synapse Analytics SQL 풀을 만듭니다. Synapse SQL 풀은 Azure Synapse에서 일반적으로 사용할 수 있는 엔터프라이즈 데이터 웨어하우징 기능을 나타냅니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

템플릿은 하나의 리소스를 정의합니다.

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 이 템플릿은 Synapse SQL 풀을 만듭니다.
   
   [![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. 다음 값을 입력하거나 업데이트합니다.

   * **구독**: Azure 구독을 선택합니다.
   * **리소스 그룹**: **새로 만들기**를 선택하고, 리소스 그룹의 고유한 이름을 입력하고, **확인**을 선택합니다. 새 리소스 그룹은 리소스 정리를 용이하게 합니다.
   * **지역**: 지역을 선택합니다.  예: **미국 중부**
   * **SQL Server 이름**: 기본 이름을 적용하거나 SQL Server 이름의 이름을 입력합니다.
   * **SQL 관리자 로그인**: SQL Server의 관리자 사용자 이름을 입력합니다.
   * **SQL 관리자 암호**: SQL Server의 관리자 암호를 입력합니다.
   * **데이터 웨어하우스 이름**: SQL 풀 이름을 입력합니다.
   * **투명한 데이터 암호화**: 기본값인 사용을 적용합니다. 
   * **서비스 수준 목표**: 기본값인 DW400c를 적용합니다.
   * **위치**: 리소스 그룹의 기본 위치를 적용합니다.
   * **검토 및 만들기**: 선택합니다.
   * **만들기**: 선택합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 배포된 리소스를 확인하거나 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 배포된 리소스를 나열할 수 있습니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 Azure Synapse Analytics SQL 풀을 만들고 배포의 유효성을 검사했습니다. Azure Synapse Analytics 및 Azure Resource Manager에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Synapse Analytics 개요](sql-data-warehouse-overview-what-is.md) 읽기
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기
- [첫 번째 ARM 템플릿 만들기 및 배포](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
