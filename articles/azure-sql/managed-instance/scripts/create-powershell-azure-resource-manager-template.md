---
title: 관리되는 인스턴스 만들기(ARM 템플릿 및 PowerShell)
titleSuffix: Azure SQL Managed Instance
description: 이 Azure PowerShell 예제 스크립트를 사용하여 관리되는 인스턴스를 만듭니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 6758aed67949032bb93086527623b60af33e716e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079560"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Azure Resource Manager 템플릿과 함께 PowerShell을 사용하여 관리되는 인스턴스 만들기

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Azure PowerShell 라이브러리 및 Azure Resource Manager 템플릿을 사용하여 관리되는 인스턴스를 만들 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

Azure PowerShell 명령은 미리 정의된 Azure Resource Manager 템플릿을 사용하여 배포를 시작할 수 있습니다. 템플릿에서 다음 속성을 지정할 수 있습니다.

- 관리형 인스턴스 이름
- SQL 관리자 사용자 이름 및 암호
- 인스턴스의 크기(코어 수 및 최대 스토리지 크기)
- 인스턴스가 배치될 VNet 및 서브넷입니다.
- 인스턴스의 서버 수준 데이터 정렬(미리 보기)입니다.

인스턴스 이름, SQL 관리자 사용자 이름, VNet/subnet 및 데이터 정렬을 나중에 변경할 수 없습니다. 다른 인스턴스 속성을 변경할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 샘플에서는 관리되는 인스턴스에 대한 [유효한 네트워크 환경을 만들](../virtual-network-subnet-create-arm-template.md)거나 [기존 VNet을 수정](../vnet-existing-add-subnet.md)했다고 가정합니다. 필요한 경우 별도의 [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)을 사용하여 네트워크 환경을 준비할 수 있습니다. 


샘플에서는 cmdlet [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) 및 [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)를 사용하므로 다음 PowerShell 모듈을 설치해야 합니다.

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿


다음 스크립트를 .json 파일에 저장하고 파일 위치를 확인합니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

이전에 저장한 .json 파일에 대해 올바른 파일 경로로 다음 PowerShell 스크립트를 업데이트하고 스크립트에서 개체의 이름을 변경합니다.

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

스크립트가 완성되면 모든 Azure 서비스 및 구성된 IP 주소에서 관리되는 인스턴스에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure SQL Managed Instance에 대한 추가 PowerShell 스크립트 샘플은 [Azure SQL Managed Instance PowerShell 스크립트](../../database/powershell-script-content-guide.md)에서 찾을 수 있습니다.
