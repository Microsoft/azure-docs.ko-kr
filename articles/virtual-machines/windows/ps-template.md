---
title: Azure에서 템플릿을 사용하여 Windows VM 만들기 | Microsoft Docs
description: Resource Manager 템플릿 및 PowerShell을 사용하여 새 Windows VM을 쉽게 만들 수 있습니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58c8ca9bb9b4dfd58c801eb4dd1dad95c1969c18
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127619"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Windows 가상 머신 만들기

Azure Cloud shell에서 Azure Resource Manager 템플릿과 Azure PowerShell을 사용 하 여 Windows 가상 컴퓨터를 만드는 방법에 알아봅니다. 이 문서에 사용 되는 템플릿은 단일 서브넷을 사용 하 여 새 가상 네트워크에서 Windows Server를 실행 하는 단일 가상 머신을 배포 합니다. Linux 가상 머신 만들기를 참조 하세요 [Azure Resource Manager 템플릿을 사용 하 여 Linux 가상 머신을 만드는 방법](../linux/create-ssh-secured-vm-from-template.md)합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

일반적으로 Azure 가상 머신을 만들 두 단계가 포함 됩니다.

- 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신보다 먼저 리소스 그룹을 만들어야 합니다.
- 가상 머신을 만듭니다.

다음 예제에서 VM을 만듭니다는 [Azure 빠른 시작 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)합니다. 다음은 템플릿의 복사본입니다.

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

PowerShell 스크립트를 실행 하려면 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 **붙여**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

설치 하 고 of 로컬로 Azure Cloud shell에서 PowerShell을 사용 하려는 경우이 자습서는 Azure PowerShell 모듈이 필요 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

이전 예제에서는 GitHub에 저장된 템플릿을 지정했습니다. 또한 템플릿을 다운로드하거나 만들고 `--template-file` 매개 변수로 로컬 경로를 지정할 수도 있습니다.

다음은 몇 가지 추가 리소스입니다.

- Resource Manager 템플릿을 개발하는 방법을 알아보려면 [Azure Resource Manager 설명서](/azure/azure-resource-manager/)를 참조하세요.
- Azure 가상 머신 스키마를 참조 하세요 [Azure 템플릿 참조](/azure/templates/microsoft.compute/allversions)합니다.
- 더 많은 가상 컴퓨터 템플릿 샘플을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)합니다.

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

이전 스크립트에서 마지막으로 PowerShell 명령에는 가상 컴퓨터 이름이 표시 됩니다. 가상 컴퓨터에 연결 하려면을 참조 하세요 [연결에 Windows를 실행 하는 Azure 가상 머신에 로그인 하는 방법을](./connect-logon.md)합니다.

## <a name="next-steps"></a>다음 단계

- 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../../resource-manager-common-deployment-errors.md)을 살펴봅니다.
- [Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 가상 머신을 만들고 관리하는 방법을 알아봅니다.

템플릿 만들기에 대해 자세히 알아보려면 배포한 리소스 종류의 JSON 구문 및 속성을 확인하세요.

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
