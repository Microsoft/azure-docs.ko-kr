---
title: Azure에서 테넌트 간에 갤러리 이미지 공유
description: 공유 이미지 갤러리를 사용 하 여 Azure 테넌트 간에 VM 이미지를 공유 하는 방법을 알아봅니다.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/15/2019
ms.author: cynthn
ms.openlocfilehash: 9b7e7066f186017b7cc4408cd4f7edcc7e5f0dcd
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065510"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure 테넌트 간에 갤러리 VM 이미지 공유

공유 이미지 갤러리를 사용 하면 RBAC를 사용 하 여 이미지를 공유할 수 있습니다. RBAC를 사용 하 여 테넌트 내에서 이미지를 공유 하 고 테넌트 외부의 사용자도 공유할 수 있습니다. 이 간단한 공유 옵션에 대 한 자세한 내용은 [갤러리 공유](/azure/virtual-machines/windows/shared-images-portal#share-the-gallery)를 참조 하세요.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> 포털을 사용 하 여 다른 azure 테넌트의 이미지에서 VM을 배포할 수 없습니다. 테넌트 간에 공유 되는 이미지에서 VM을 만들려면 [Azure CLI](../linux/share-images-across-tenants.md) 또는 Powershell을 사용 해야 합니다.

## <a name="create-a-vm-using-powershell"></a>PowerShell을 사용 하 여 VM 만들기

응용 프로그램 ID, 암호 및 테넌트 ID를 사용 하 여 두 테넌트에 로그인 합니다. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

앱 등록에 대 한 권한이 있는 리소스 그룹에 VM을 만듭니다. 이 예제의 정보를 사용자 고유의 정보로 바꿉니다.



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Set a variable for the image version in Tenant 1 using the full image ID of the shared image version
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Networking pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using the $image variable to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $image | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="next-steps"></a>다음 단계

[Azure Portal](shared-images-portal.md)를 사용 하 여 공유 이미지 갤러리 리소스를 만들 수도 있습니다.