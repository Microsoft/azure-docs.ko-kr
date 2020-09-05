---
title: 'Azure Resource Manager: Azure SQL Managed Instance 만들기'
description: Azure Resource Manager 템플릿을 사용하여 Azure SQL Managed Instance를 만드는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/22/2020
ms.openlocfilehash: d405cfda3c72ce9c724a7d96925d3346428a3089
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660617"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure SQL Managed Instance 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 배포하여 Azure SQL Managed Instance 및 vNet을 만드는 프로세스에 중점을 둡니다. [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)는 SQL Server 데이터베이스 엔진과 거의 100% 기능 패리티를 갖춘 완전 관리형이자 확장 가능한 인텔리전트 클라우드 데이터베이스입니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

이러한 리소스는 템플릿에 정의되어 있습니다.

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft.Sql/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

추가 템플릿 샘플은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포

다음 PowerShell 코드 블록에서 **사용해 보세요**를 선택하여 Azure Cloud Shell을 엽니다.

> [!IMPORTANT]
> 관리형 인스턴스 배포는 장기 실행 작업입니다. 서브넷에서 첫 번째 인스턴스를 배포하려면 기존 관리형 인스턴스가 있는 서브넷에 배포하는 것보다 일반적으로 훨씬 오래 걸립니다. 평균적인 프로비저닝 시간에 대한 내용은 [SQL Managed Instance 관리 작업](sql-managed-instance-paas-overview.md#management-operations)을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>배포된 리소스 검토

[Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)을 방문하여 관리되는 인스턴스가 선택한 리소스 그룹에 있는지 확인합니다. 관리되는 인스턴스를 만드는 데 다소 시간이 걸릴 수 있으므로 리소스 그룹의 **개요** 페이지에서 **배포** 링크를 확인해야 할 수도 있습니다.

- Azure 가상 머신에서 SQL Managed Instance에 연결하는 방법을 보여주는 빠른 시작은 [Azure 가상 머신 연결 구성](connect-vm-instance-configure.md)을 참조하세요.
- 온-프레미스 클라이언트 컴퓨터에서 지점 및 사이트 간 연결을 사용하여 SQL Managed Instance에 연결하는 방법을 보여주는 빠른 시작은 [지점 및 사이트 간 연결 구성](point-to-site-p2s-configure.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)로 이동하려면 관리되는 인스턴스를 유지하되 추가 자습서를 완료한 후 관리되는 인스턴스 및 관련 리소스를 삭제합니다. 관리되는 인스턴스를 삭제한 후 [관리되는 인스턴스를 삭제한 후 서브넷 삭제](virtual-cluster-delete.md)를 참조하세요.


리소스 그룹을 삭제하려면 다음을 수행합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure SQL Managed Instance에 연결하도록 Azure VM 구성](connect-vm-instance-configure.md)
