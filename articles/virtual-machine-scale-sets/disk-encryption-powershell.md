---
title: Azure PowerShell를 사용 하 여 Azure 확장 집합에 대 한 디스크 암호화
description: Azure PowerShell을 사용하여 Windows 가상 머신 확장 집합에서 VM 인스턴스 및 연결된 디스크를 암호화하는 방법 알아보기
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: cac2f57ccb5fd5d0aa251533bd4a5dd1179ec058
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069768"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 가상 머신 확장 집합에서 OS 및 연결 된 데이터 디스크 암호화

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다.  이 문서에서는 Azure PowerShell를 사용 하 여 가상 머신 확장 집합을 만들고 암호화 하는 방법을 보여 줍니다. 가상 머신 확장 집합에 Azure Disk Encryption를 적용 하는 방법에 대 한 자세한 내용은 [Virtual Machine Scale Sets Azure Disk Encryption](disk-encryption-overview.md)를 참조 하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>디스크 암호화가 활성화된 Azure Key Vault 만들기

Azure Key Vault는 애플리케이션 및 서비스에 안전하게 구현할 수 있는 키와 암호를 저장할 수 있습니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다.

[AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)를 사용 하 여 Key Vault를 만듭니다. Key Vault를 디스크 암호화에 사용할 수 있도록 허용하려면 *EnabledForDiskEncryption* 매개 변수를 설정합니다. 또한 다음 예제에서는 리소스 그룹 이름, Key Vault 이름 및 위치에 대한 변수를 정의합니다. 사용자 고유의 Key Vault 이름을 제공합니다.

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>기존 Key Vault 사용

이 단계는 디스크 암호화로 사용하려는 기존 Key Vault가 있는 경우에만 필요합니다. 이전 섹션에서 Key Vault를 만든 경우 이 단계를 건너뜁니다.

[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy)를 사용하여 확장 집합과 동일한 구독 및 지역에 있는 기존의 Key Vault에서 디스크 암호화를 사용하도록 설정할 수 있습니다. 다음과 같이 *$vaultName* 변수에서 기존 Key Vault의 이름을 정의합니다.


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>확장 집합 만들기

먼저 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1)을 사용하여 VM 인스턴스에 대한 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzVmss](/powershell/module/az.compute/new-azvmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다. 부하 분산 장치는 TCP 포트 80에서 트래픽을 분산할 뿐만 아니라 TCP 포트 3389의 원격 데스크톱 트래픽 및 TCP 포트 5985의 PowerShell 원격을 허용하는 규칙을 포함합니다.

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>암호화 사용

확장 집합에서 VM 인스턴스를 암호화하려면 먼저 [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault)를 사용하여 Key Vault URI 및 리소스 ID에 대한 정보를 얻습니다. 이러한 변수는 [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension)으로 암호화 프로세스를 시작하는 데 사용됩니다.


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

메시지가 나타나면 *y*를 입력하여 확장 집합 VM 인스턴스에서 디스크 암호화 프로세스를 계속합니다.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>KEK를 사용하여 암호화를 사용하여 키 래핑

가상 머신 확장 집합을 암호화할 때 보안을 강화하기 위해 키 암호화 키를 사용할 수도 있습니다.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  disk-encryption-keyvault 매개 변수 값에 대한 구문은 다음과 같은 전체 식별자 문자열입니다.</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> key-encryption-key 매개 변수의 값 구문은 다음과 같이 KEK의 전체 URI입니다.</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>암호화 진행 확인

디스크 암호화의 상태를 확인하려면 [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption)을 사용합니다.


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

VM 인스턴스가 암호화될 때 *EncryptionSummary* 코드는 다음 예제 출력에 표시된 대로 *ProvisioningState/succeeded*를 보고합니다.

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>암호화 사용 안 함

암호화된 VM 인스턴스 디스크를 더 이상 사용하지 않으려는 경우 다음과 같이 [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption)을 사용하여 암호화를 비활성화할 수 있습니다.


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>다음 단계

- 이 문서에서는 가상 머신 확장 집합을 암호화하는 데 Azure PowerShell을 사용했습니다. [Azure CLI](disk-encryption-cli.md) 또는 [Azure Resource Manager 템플릿을](disk-encryption-azure-resource-manager.md)사용할 수도 있습니다.
- 다른 확장이 프로비저닝된 후 Azure Disk Encryption을 적용하려는 경우 [확장 시퀀싱](virtual-machine-scale-sets-extension-sequencing.md)을 사용할 수 있습니다.
