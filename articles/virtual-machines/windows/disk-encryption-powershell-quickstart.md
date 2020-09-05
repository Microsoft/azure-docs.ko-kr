---
title: Azure PowerShell을 사용하여 Windows VM 만들기 및 암호화
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Windows 가상 머신을 만들고 암호화하는 방법을 배웁니다.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a50b62a7c6064672dfbf7d609b6053d7be6fdb77
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079492"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure에서 Windows 가상 머신 만들기 및 암호화

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다. 이 빠른 시작에서는 Azure PowerShell 모듈을 사용하여 Windows 가상 머신을 만들고 암호화 키 스토리지용 Key Vault를 만들고 VM을 암호화하는 방법을 보여 줍니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 Azure 가상 머신을 만듭니다. cmdlet에 자격 증명을 제공해야 합니다. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

VM 배포에는 몇 분 정도 걸립니다. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>암호화 키에 대해 구성된 Key Vault 만들기

Azure Disk Encryption은 Azure Key Vault에 암호화 키를 저장합니다. [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault)를 사용하여 Key Vault를 만듭니다. Key Vault를 사용하여 암호화 키를 저장하려면 -EnabledForDiskEncryption 매개 변수를 사용합니다.

> [!Important]
> 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서는 이름이 *myKV*인 Key Vault를 만들지만 원하는 이름을 지정해야 합니다.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>가상 머신 암호화

[Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension)을 사용하여 VM을 암호화합니다. 

Set-AzVmDiskEncryptionExtension을 사용하려면 Key Vault 개체의 일부 값이 필요합니다. [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault)에 Key Vault의 고유 이름을 전달하면 이 값을 얻을 수 있습니다.

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

몇 분 후 프로세스가 다음 결과를 반환합니다.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)를 실행하여 암호화 프로세스를 확인할 수 있습니다.

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

암호화를 사용하는 경우 반환된 출력에 다음이 표시됩니다.

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 가상 머신을 만들고 암호화 키에 사용된 Key Vault를 만들고 VM을 암호화했습니다.  IaaS VM용 Azure Disk Encryption 필수 구성 요소에 대해 자세히 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Disk Encryption 개요](disk-encryption-overview.md)
