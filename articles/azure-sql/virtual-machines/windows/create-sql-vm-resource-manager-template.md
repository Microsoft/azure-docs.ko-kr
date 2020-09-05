---
title: ARM 템플릿을 사용하여 SQL Server VM 만들기
description: Azure VM(Virtual Machine)에서 Azure Resource Manager 템플릿(ARM 템플릿)을 사용하여 SQL Server를 만드는 방법에 대해 알아봅니다.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 21909a9c7a1edda5059b9e83cf8dda243862b966
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660210"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 SQL Server VM 만들기

이 Azure Resource Manager 템플릿(ARM 템플릿)을 사용하여 Azure VM(Virtual Machine)에 SQL Server를 배포합니다. 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

SQL Server VM ARM 템플릿을 사용하려면 다음이 필요합니다.

- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 및/또는 [PowerShell](/powershell/scripting/install/installing-powershell) 
- [가상 네트워크](../../../virtual-network/quick-create-portal.md) 및 [서브넷](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)이 준비된 미리 구성된 [리소스 그룹](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)
- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.


## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

템플릿에는 다음과 같은 5개의 Azure 리소스가 정의되어 있습니다. 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): 공용 IP 주소를 만듭니다. 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): 네트워크 보안 그룹을 만듭니다. 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): 네트워크 인터페이스를 구성합니다. 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Azure에서 가상 머신을 만듭니다. 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): 가상 머신을 SQL VM 리소스 공급자에 등록합니다. 

더 많은 SQL Server Azure VM 템플릿 샘플은 [빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.


## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 의도한 SQL Server 버전이 설치된 가상 머신을 만들어서 SQL VM 리소스 공급자에 등록합니다. 

   [![Azure에 배포](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. 다음 값을 선택하거나 입력합니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: SQL Server VM에 대해 준비된 리소스 그룹입니다. 
    * **지역**: 지역을 선택합니다.  예: **미국 중부**
    * **가상 머신 이름**: SQL Server 가상 머신의 이름을 입력합니다. 
    * **가상 머신 크기**: 드롭다운에서 가상 머신의 적절한 크기를 선택합니다.
    * **기존 가상 네트워크 이름**: SQL Server VM에 대해 준비된 가상 네트워크의 이름을 입력합니다. 
    * **기존 Vnet 리소스 그룹**: 가상 네트워크가 준비된 리소스 그룹을 입력합니다. 
    * **기존 서브넷 이름**: 준비된 서브넷의 이름입니다. 
    * **이미지 제품**: 비즈니스 요구 사항에 가장 적합한 SQL Server 및 Windows Server 이미지를 선택합니다. 
    * **SQL Sku**: 비즈니스 요구 사항에 가장 적합한 SQL Server SKU 버전을 선택합니다. 
    * **관리자 사용자 이름**: 가상 머신의 관리 사용자 이름입니다. 
    * **관리자 암호**: VM 관리자 계정에 사용되는 암호입니다. 
    * **스토리지 워크로드 유형**:  비즈니스에 가장 적합한 워크로드 스토리지 유형입니다. 
    * **Sql 데이터 디스크 수**:  SQL Server가 데이터 파일에 사용하는 디스크 수입니다.  
    * **데이터 경로**:  SQL Server 데이터 파일의 경로입니다. 
    * **Sql 로그 디스크 수**:  SQL Server가 로그 파일에 사용하는 디스크 수입니다. 
    * **로그 경로**:  SQL Server 로그 파일의 경로입니다. 
    * **위치**:  모든 리소스의 위치입니다. 이 값은 기본값인 `[resourceGroup().location]`으로 유지해야 합니다. 

3. **검토 + 만들기**를 선택합니다. SQL Server VM이 성공적으로 배포되면 다음과 같이 알림을 받게 됩니다.

Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

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

SQL Server VM을 배포하는 다른 방법은 다음 문서를 참조하세요. 
- [Azure Portal](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

자세한 내용은 [Azure VM의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)를 참조하세요.
