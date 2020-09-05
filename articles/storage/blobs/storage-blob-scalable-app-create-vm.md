---
title: Azure에서 확장 가능한 애플리케이션에 대한 VM 및 스토리지 계정 만들기
description: Azure Blob Storage를 사용하여 확장 가능한 애플리케이션을 실행하는 데 사용할 VM을 배포하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b8c52b03cb6dec6096565e9eac26b7b2c4a30e4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073253"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>확장 가능한 애플리케이션에 필요한 가상 머신 및 스토리지 계정 만들기

이 자습서는 시리즈의 1부입니다. 이 자습서에서는 Azure Storage 계정으로 대량의 임의 데이터를 업로드하고 다운로드하는 애플리케이션을 배포하는 방법을 설명합니다. 이 자습서를 마치면 대량의 데이터를 스토리지 계정으로 업로드하고 다운로드하는 가상 머신에서 실행되는 콘솔 애플리케이션이 생깁니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 스토리지 계정 만들기
> * 가상 머신 만들기
> * 사용자 지정 스크립트 확장 구성

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기
 
이 샘플은 Azure Storage 계정의 Blob 컨테이너에 50개의 대용량 파일을 업로드합니다. 스토리지 계정은 Azure Storage 데이터 개체의 저장 및 액세스를 위한 고유한 네임스페이스를 제공합니다. 만든 리소스 그룹에 [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount) 명령을 사용하여 스토리지 계정을 만듭니다.

다음 명령에서 `<blob_storage_account>` 자리 표시자는 Blob Storage 계정에 대한 전역적으로 고유한 이름으로 바꿉니다.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

가상 머신 구성을 만듭니다. 이 구성은 가상 머신 이미지, 크기 및 인증 구성 등 가상 머신을 배포할 때 사용되는 설정을 포함합니다. 이 단계를 실행할 때 자격 증명을 묻는 메시지가 나타납니다. 입력하는 값은 가상 머신에 대한 사용자 이름 및 암호로 구성됩니다.

[New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 가상 머신을 만듭니다.

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>구성 배포

이 자습서에서는 필수 구성 요소를 가상 머신에 설치해야 합니다. 사용자 지정 스크립트 확장을 사용하여 다음 작업을 완료하는 PowerShell 스크립트를 실행합니다.

> [!div class="checklist"]
> * .NET Core 2.0 설치
> * Chocolatey 설치
> * Git 설치
> * 샘플 리포지토리 복제
> * NuGet 패키지 복원
> * 임의 데이터가 포함된 1GB 파일 50개 만들기

다음 cmdlet을 실행하여 가상 머신의 구성을 완료합니다. 이 단계를 완료하려면 5-15분이 걸립니다.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>다음 단계

시리즈의 1부에서는 다음을 수행하는 방법과 같이 스토리지 계정을 만들고, 가상 머신을 배포하고, 가상 머신에서 필수 구성 요소를 구성하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 스토리지 계정 만들기
> * 가상 머신 만들기
> * 사용자 지정 스크립트 확장 구성

시리즈의 2부로 이동하여 지수 다시 시도와 병렬 처리를 사용하여 스토리지 계정으로 대량의 데이터를 업로드합니다.

> [!div class="nextstepaction"]
> [스토리지 계정에 대량의 대용량 파일을 병렬로 업로드](storage-blob-scalable-app-upload-files.md)
