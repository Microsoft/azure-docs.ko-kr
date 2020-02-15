---
title: Azure Managed Disks의 서버 쪽 암호화-PowerShell
description: Azure Storage는 저장소 클러스터에 유지 하기 전에 미사용 데이터를 암호화 하 여 데이터를 보호 합니다. Microsoft 관리 키를 사용 하 여 관리 디스크의 암호화를 사용 하거나, 고객 관리 키를 사용 하 여 사용자 고유의 키로 암호화를 관리할 수 있습니다.
author: roygara
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 73a505ce68ed046eba011d3b36991616f441506e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206307"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure managed disks의 서버 쪽 암호화

Azure managed disks는 클라우드로 데이터를 유지할 때 기본적으로 데이터를 자동으로 암호화 합니다. 서버 쪽 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. Azure managed disks의 데이터는 256 비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용 하 여 투명 하 게 암호화 되 고, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며, FIPS 140-2 규격입니다.   

암호화는 관리 디스크의 성능에 영향을 주지 않습니다. 암호화에 대 한 추가 비용은 없습니다.

암호화 모듈 기본 Azure managed disks에 대 한 자세한 내용은 [암호화 API: 차세대](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) 을 참조 하세요.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

플랫폼 관리 키를 사용 하 여 관리 디스크의 암호화를 사용 하거나 사용자 고유의 키를 사용 하 여 암호화를 관리할 수 있습니다. 사용자 고유의 키를 사용 하 여 암호화를 관리 하도록 선택 하는 경우 관리 디스크의 모든 데이터를 암호화 하 고 암호 해독 하는 데 사용할 *고객 관리 키* 를 지정할 수 있습니다. 

다음 섹션에서는 키 관리에 대 한 각 옵션을 더 자세히 설명 합니다.

## <a name="platform-managed-keys"></a>플랫폼 관리 키

기본적으로 관리 디스크는 플랫폼 관리 암호화 키를 사용 합니다. 2017 년 6 월 10 일부 터 모든 새 관리 디스크, 스냅숏, 이미지 및 기존 관리 디스크에 기록 된 새 데이터는 플랫폼 관리 키를 사용 하 여 미사용 상태로 자동으로 암호화 됩니다. 

## <a name="customer-managed-keys"></a>고객 관리형 키

사용자 고유의 키를 사용 하 여 각 관리 디스크의 수준에서 암호화를 관리 하도록 선택할 수 있습니다. 고객이 관리 하는 키를 사용 하는 관리 디스크에 대 한 서버 쪽 암호화는 Azure Key Vault 통합 된 환경을 제공 합니다. Key Vault [rsa 키](../../key-vault/key-vault-hsm-protected-keys.md) 를 가져오거나 Azure Key Vault에서 새 rsa 키를 생성할 수 있습니다. Azure managed disks는 [봉투 (envelope) 암호화](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)를 사용 하 여 완전히 투명 한 방식으로 암호화 및 암호 해독을 처리 합니다. 키를 사용 하 여 보호 되는 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 기반 dek (데이터 암호화 키)를 사용 하 여 데이터를 암호화 합니다. DEK를 암호화 하 고 암호 해독 하는 데 키를 사용 하려면 Key Vault에서 관리 디스크에 대 한 액세스 권한을 부여 해야 합니다. 이를 통해 데이터 및 키를 완전히 제어할 수 있습니다. 언제 든 지 키를 사용 하지 않도록 설정 하거나 관리 디스크에 대 한 액세스를 취소할 수 있습니다. 또한 Azure Key Vault 모니터링을 사용 하 여 암호화 키 사용을 감사 하 여 관리 디스크 또는 다른 신뢰할 수 있는 Azure 서비스만 키에 액세스할 수 있도록 할 수 있습니다.

다음 다이어그램에서는 관리 디스크에서 Azure Active Directory 및 Azure Key Vault를 사용 하 여 고객이 관리 하는 키를 사용 하 여 요청 하는 방법을 보여 줍니다.

![관리 되는 디스크 및 고객이 관리 하는 키 워크플로. 관리자는 Azure Key Vault 만든 다음 디스크 암호화 집합을 만들고 디스크 암호화 집합을 설정 합니다. 집합은 디스크가 Azure AD를 사용 하 여 인증할 수 있도록 하는 VM에 연결 되어 있습니다.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


다음 목록에서는 다이어그램에 대해 보다 자세히 설명 합니다.

1. Azure Key Vault 관리자가 주요 자격 증명 모음 리소스를 만듭니다.
1. 키 자격 증명 모음 관리자는 RSA 키를 가져와 Key Vault 하거나 Key Vault에서 새 RSA 키를 생성 합니다.
1. 관리자는 Azure Key Vault ID와 키 URL을 지정 하 여 디스크 암호화 집합 리소스의 인스턴스를 만듭니다. 디스크 암호화 집합은 관리 디스크에 대 한 키 관리를 간소화 하기 위해 도입 된 새로운 리소스입니다. 
1. 디스크 암호화 집합이 생성 되 면 [시스템 할당 관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 는 AD (Azure Active Directory)에서 생성 되 고 디스크 암호화 집합과 연결 됩니다. 
1. 그런 다음 Azure key vault 관리자는 키 자격 증명 모음에서 작업을 수행할 수 있는 관리 id 권한을 부여 합니다.
1. VM 사용자는 디스크를 디스크 암호화 집합에 연결 하 여 디스크를 만듭니다. 또한 VM 사용자는 기존 리소스에 대해 고객 관리 키를 사용 하 여 서버 쪽 암호화를 사용 하도록 설정 하 여 디스크 암호화 집합에 연결할 수 있습니다. 
1. 관리 디스크는 관리 id를 사용 하 여 Azure Key Vault에 요청을 보냅니다.
1. 데이터를 읽거나 쓰기 위해 managed disks는 데이터 암호화 및 암호 해독을 수행 하기 위해 데이터 암호화 키를 암호화 (래핑) 하 고 해독 (래핑 해제) 하는 Azure Key Vault 요청을 보냅니다. 

고객 관리 키에 대 한 액세스를 취소 하려면 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 및 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조 하세요. 액세스를 취소 하면 Azure Storage 의해 암호화 키에 액세스할 수 없으므로 저장소 계정의 모든 데이터에 대 한 액세스가 효과적으로 차단 됩니다.

### <a name="supported-regions"></a>지원되는 지역

현재 다음 지역만 지원 됩니다.

- 미국 동부, 미국 서 부 2 및 미국 서 부 지역에서 GA 제품으로 제공 됩니다.
- 미국 서 부, 미국 동부 2, 캐나다 중부 및 북아메리카 유럽 지역에서 공개 미리 보기로 제공 됩니다.

### <a name="restrictions"></a>제한

현재, 고객 관리 키에는 다음과 같은 제한 사항이 있습니다.

- 크기 2080의 ["소프트" 및 "하드" RSA 키](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) 만 지원 되 고 다른 키 나 크기는 지원 되지 않습니다.
- 서버 쪽 암호화 및 고객 관리 키를 사용 하 여 암호화 된 사용자 지정 이미지에서 만든 디스크는 동일한 고객 관리 키를 사용 하 여 암호화 해야 하며 동일한 구독에 있어야 합니다.
- 서버 쪽 암호화 및 고객이 관리 하는 키로 암호화 된 디스크에서 만든 스냅숏은 동일한 고객 관리 키를 사용 하 여 암호화 해야 합니다.
- 서버 쪽 암호화 및 고객 관리 키를 사용 하 여 암호화 된 사용자 지정 이미지는 공유 이미지 갤러리에서 사용할 수 없습니다.
- 고객 관리 키 (Azure 키 자격 증명 모음, 디스크 암호화 집합, Vm, 디스크 및 스냅숏)와 관련 된 모든 리소스는 동일한 구독 및 지역에 있어야 합니다.
- 고객 관리 키로 암호화 된 디스크, 스냅숏 및 이미지는 다른 구독으로 이동할 수 없습니다.
- Azure Portal를 사용 하 여 디스크 암호화 집합을 만드는 경우 지금은 스냅숏을 사용할 수 없습니다.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault 및 Diskset 설정

1. 최신 [Azure PowerShell 버전](/powershell/azure/install-az-ps)을 설치 했는지 확인 하 고, AzAccount를 사용 하 여의 Azure 계정에 로그인 했는지 확인 합니다.

1. Azure Key Vault 및 암호화 키의 인스턴스를 만듭니다.

    Key Vault 인스턴스를 만들 때 일시 삭제 및 보호 제거를 사용 하도록 설정 해야 합니다. 일시 삭제를 사용 하면 Key Vault 지정 된 보존 기간 (90 일 기본값) 동안 삭제 된 키를 보유 하 게 됩니다. 보호 제거를 사용 하면 보존 기간이 경과 때까지 삭제 된 키를 영구적으로 삭제할 수 없습니다. 이러한 설정은 실수로 인 한 삭제로 인해 데이터가 손실 되지 않도록 보호 합니다. 이러한 설정은 관리 디스크를 암호화 하는 데 Key Vault를 사용 하는 경우 필수입니다.

    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  DiskEncryptionSet의 인스턴스를 만듭니다. 
    
    ```powershell
    $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned

    $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
    ```

1.  키 자격 증명 모음에 대해 DiskEncryptionSet 리소스 액세스 권한을 부여 합니다.

    > [!NOTE]
    > Azure가 Azure Active Directory에서 DiskEncryptionSet의 id를 만드는 데 몇 분 정도 걸릴 수 있습니다. 다음 명령을 실행할 때 "Active Directory 개체를 찾을 수 없습니다."와 같은 오류가 발생 하면 몇 분 정도 기다린 후 다시 시도 하세요.
    
    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
     
    New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace 이미지를 사용 하 여 VM 만들기, 고객이 관리 하는 키를 사용 하 여 OS 및 데이터 디스크 암호화

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>고객이 관리 하는 키를 사용 하 여 서버 쪽 암호화를 사용 하 여 암호화 된 빈 디스크 만들기 및 VM에 연결

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-unattached-managed-disks"></a>연결 되지 않은 기존 관리 디스크 암호화 

다음 스크립트를 사용 하 여 기존 디스크를 암호화 하려면 실행 중인 VM에 연결 해서는 안 됩니다.

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace 이미지를 사용 하 여 가상 머신 확장 집합 만들기, 고객이 관리 하는 키를 사용 하 여 OS 및 데이터 디스크 암호화

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

> [!IMPORTANT]
> 고객 관리 키는 azure AD (Azure Active Directory 기능) 인 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 고객 관리 키를 구성 하는 경우 관리 id는 내부적으로 리소스에 자동으로 할당 됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 [AZURE AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조 하세요.

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 고객 관리 키는 azure AD (Azure Active Directory 기능) 인 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 고객 관리 키를 구성 하는 경우 관리 id는 내부적으로 리소스에 자동으로 할당 됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 [AZURE AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조 하세요.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>서버 쪽 암호화 및 Azure disk encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) 는 Windows의 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 기능과 Linux의 [DM-자리](https://en.wikipedia.org/wiki/Dm-crypt) 를 활용 하 여 게스트 VM 내에서 고객 관리 키를 사용 하 여 관리 디스크를 암호화 합니다.  고객 관리 키를 사용 하는 서버 쪽 암호화는 저장소 서비스의 데이터를 암호화 하 여 Vm에 대 한 모든 OS 유형 및 이미지를 사용할 수 있도록 하 여 ADE에서 향상 됩니다.

## <a name="next-steps"></a>다음 단계

- [고객 관리 키를 사용 하 여 암호화 된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../../key-vault/key-vault-overview.md)
- [고객 관리 키를 사용 하도록 설정 된 디스크를 사용 하 여 컴퓨터 복제](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell을 사용 하 여 Azure에 VMware Vm의 재해 복구 설정](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell 및 Azure Resource Manager를 사용 하 여 Hyper-v Vm 용 Azure에 대 한 재해 복구 설정](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
