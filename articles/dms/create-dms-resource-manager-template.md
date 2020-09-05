---
title: DMS의 인스턴스 만들기(Azure Resource Manager 템플릿)
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Database Migration Service를 만드는 방법을 알아봅니다.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: 24a0d16a6ff052df4b7a9bcdd078542987b4fd50
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661183"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Database Migration Service의 인스턴스 만들기

이 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Database Migration Service의 인스턴스를 배포합니다. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure Database Migration Service ARM 템플릿에는 다음이 필요합니다. 

- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 및/또는 [PowerShell](/powershell/scripting/install/installing-powershell). 
- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

템플릿에 정의된 세 가지 Azure 리소스는 다음과 같습니다. 

- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): 가상 네트워크를 만듭니다. 
- [Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets): 서브넷을 만듭니다. 
- [Microsoft.DataMigration/services](/azure/templates/microsoft.datamigration/services): Azure Database Migration Service의 인스턴스를 배포합니다. 

추가 Azure Database Migration Services 템플릿은 [빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.


## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 Azure Database Migration Service의 인스턴스를 만듭니다. 

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. 다음 값을 선택하거나 입력합니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: 드롭다운에서 기존 리소스 그룹을 선택하거나 **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다. 
    * **지역**: 리소스가 배포될 위치입니다.
    * **서비스 이름**: 새 마이그레이션 서비스의 이름입니다.
    * **위치**: 리소스 그룹의 위치를 `[resourceGroup().location]`의 기본값으로 둡니다.
    * **Vnet 이름**: 새 가상 네트워크의 이름입니다.
    * **서브넷 이름**: 가상 네트워크와 연결된 새 서브넷의 이름입니다.



3. **검토 + 만들기**를 선택합니다. Azure Database Migration Service의 인스턴스가 성공적으로 배포되면 알림이 표시됩니다. 


Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure CLI를 사용하여 배포된 리소스를 확인할 수 있습니다. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


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

템플릿 만들기 프로세스를 안내하는 단계별 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [ 자습서: 첫 번째 ARM 템플릿 만들기 및 배포](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

Azure Database Migration Service를 배포하는 다른 방법은 다음을 참조하세요. 
- [Azure Portal](quickstart-create-data-migration-service-portal.md)

자세히 알아보려면 [Azure Database Migration Service 개요](dms-overview.md)를 참조하세요.