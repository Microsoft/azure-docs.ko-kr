---
title: 일반화된 VHD를 Azure PowerShell 스크립트 샘플에 업로드
description: 리소스 관리자 배포 모델과 Managed Disks를 사용하여 Azure에 일반화된 VHD를 업로드하고 새 VM을 만드는 PowerShell 샘플 스크립트입니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03e3ea745e00773272cd141aebb845465ee9890c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072488"
---
# <a name="sample-script-to-upload-a-vhd-to-azure-and-create-a-new-vm"></a>Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트

이 스크립트는 일반화된 VM에서 로컬 .vhd 파일을 가져와서 Azure에 업로드하고, Managed Disk 이미지를 만들고, 새 VM을 만드는 데 사용합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>샘플 스크립트

```powershell
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'EastUS'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$containername = 'mycontainer'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vmName = 'myVM'
$imageName = 'myImage'
$vhdName = 'myUploadedVhd.vhd'
$diskSizeGB = '128'
$subnetName = 'mySubnet'
$vnetName = 'myVnet'
$ipName = 'myPip'
$nicName = 'myNic'
$nsgName = 'myNsg'
$ruleName = 'myRdpRule'
$computerName = 'myComputerName'
$vmSize = 'Standard_DS1_v2'

# Get the username and password to be used for the administrators account on the VM. 
# This is used when connecting to the VM using RDP.

$cred = Get-Credential

# Upload the VHD
New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
    -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ('https://' + $storageaccount + '.blob.core.windows.net/' + $containername + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath

# Note: Uploading the VHD may take awhile!

# Create a managed image from the uploaded VHD 
$imageConfig = New-AzImageConfig -Location $location
$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized `
    -BlobUri $urlOfUploadedImageVhd
$image = New-AzImage -ImageName $imageName -ResourceGroupName $resourceGroup -Image $imageConfig
 
# Create the networking resources
$singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
$pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $resourceGroup -Location $location `
    -AllocationMethod Dynamic
$rdpRule = New-AzNetworkSecurityRuleConfig -Name $ruleName -Description 'Allow RDP' -Access Allow `
    -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
$nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $resourceGroup -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
$vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName

# Start building the VM configuration
$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize

# Set the VM image as source image for the new VM
$vm = Set-AzVMSourceImage -VM $vm -Id $image.Id

# Finish the VM configuration and add the NIC.
$vm = Set-AzVMOSDisk -VM $vm  -DiskSizeInGB $diskSizeGB -CreateOption FromImage -Caching ReadWrite
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

# Create the VM
New-AzVM -VM $vm -ResourceGroupName $resourceGroup -Location $location

# Verify that the VM was created
$vmList = Get-AzVM -ResourceGroupName $resourceGroup
$vmList.Name


```


<!-- 
[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")] -->

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령                                                                                                             | 메모                                                                                                                                                                                |
|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)                           | 모든 리소스가 저장되는 리소스 그룹을 만듭니다.                                                                                                                          |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)                         | 스토리지 계정을 만듭니다.                                                                                                                                                           |
| [Add-AzVhd](/powershell/module/az.compute/add-azvhd)                                               | 온-프레미스 가상 머신에서 Azure의 클라우드 스토리지 계정에 있는 blob으로 가상 하드 디스크를 업로드합니다.                                                                       |
| [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig)                               | 구성 가능한 이미지 개체를 만듭니다.                                                                                                                                                 |
| [Set-AzImageOsDisk](/powershell/module/az.compute/set-azimageosdisk)                               | 이미지 개체의 운영 체제 디스크 속성을 설정합니다.                                                                                                                        |
| [New-AzImage](/powershell/module/az.compute/new-azimage)                                           | 새 이미지를 만듭니다.                                                                                                                                                                 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 서브넷 구성을 만듭니다. 이 구성은 가상 네트워크 만들기 프로세스에서 사용됩니다.                                                                                |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)                         | 가상 네트워크를 만듭니다.                                                                                                                                                           |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)                       | 공용 IP 주소를 만듭니다.                                                                                                                                                         |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)                     | 네트워크 인터페이스를 만듭니다.                                                                                                                                                         |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)   | 네트워크 보안 그룹 규칙 구성을 만듭니다. 이 구성은 NSG가 만들어질 때 NSG 규칙을 만드는 데 사용됩니다.                                                       |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)             | 네트워크 보안 그룹을 만듭니다.                                                                                                                                                    |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)                         | 리소스 그룹의 가상 네트워크를 가져옵니다.                                                                                                                                          |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)                                     | VM 구성을 만듭니다. 이 구성은 VM 이름, 운영 체제 및 관리자 자격 증명 등의 정보를 포함합니다. 이 구성은 VM을 만드는 중에 사용됩니다. |
| [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)                           | 가상 머신에 대한 이미지를 지정합니다.                                                                                                                                            |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk)                                     | 가상 머신의 운영 체제 디스크 속성을 설정합니다.                                                                                                                      |
| [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)                   | 가상 머신의 운영 체제 디스크 속성을 설정합니다.                                                                                                                      |
| [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)                 | 가상 머신에 네트워크 인터페이스를 추가합니다.                                                                                                                                       |
| [New-AzVM](/powershell/module/az.compute/new-azvm)                                                 | 가상 머신을 만듭니다.                                                                                                                                                            |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)                     | 리소스 그룹 및 포함된 모든 리소스를 제거합니다.                                                                                                                         |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
