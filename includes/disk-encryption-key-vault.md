---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 398da52ba424c08bd1bbdc6f02641109e136f45c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511500"
---
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

*리소스 그룹이 이미 있는 경우 [키 자격 증명 모음 만들기](#create-a-key-vault)로 건너뛸 수 있습니다.*

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

[Az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI 명령, [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell 명령 또는 [Azure Portal](https://portal.azure.com)를 사용 하 여 리소스 그룹을 만듭니다.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

*키 자격 증명 모음이 이미 있는 경우 [키 자격 증명 모음 고급 액세스 정책 설정](#set-key-vault-advanced-access-policies)으로 건너뛸 수 있습니다.*

[Az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI 명령을 사용 하 여 키 자격 증명 모음을 만듭니다. [AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell 명령, [Azure Portal](https://portal.azure.com)또는 [리소스 관리자 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)사용 합니다.

>[!WARNING]
> 암호화 비밀이 지역 경계를 넘지 않도록 Azure Disk Encryption Key Vault와 Vm을 동일한 지역에 함께 배치 해야 합니다. 암호화할 Vm과 동일한 지역에 있는 Key Vault를 만들고 사용 합니다. 

각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서는 <your-unique-keyvault-name>을 Key Vault의 이름으로 바꿉니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용 하 여 키 자격 증명 모음을 만들 때 "--enabled-encryption" 플래그를 추가 합니다.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell를 사용 하 여 키 자격 증명 모음을 만들 때 "-EnabledForDiskEncryption" 플래그를 추가 합니다.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager 템플릿

[리소스 관리자 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)사용 하 여 주요 자격 증명 모음을 만들 수도 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭합니다.
2. 구독, 리소스 그룹, 리소스 그룹 위치, Key Vault 이름, 개체 ID, 약관 및 규약을 선택 하 고 **구매**를 클릭 합니다. 


##  <a name="set-key-vault-advanced-access-policies"></a>Key vault 고급 액세스 정책 설정

Azure 플랫폼은 VM을 부팅하고 볼륨을 해독할 수 있도록 Key Vault의 암호화 키 또는 비밀에 액세스해야 합니다. 

이전 단계에서 설명한 대로 디스크 암호화, 배포 또는 템플릿 배포를 만들 때 키 자격 증명 모음을 사용 하도록 설정 하지 않은 경우 고급 액세스 정책을 업데이트 해야 합니다.  

### <a name="azure-cli"></a>Azure CLI

[az keyvault update](/cli/azure/keyvault#az-keyvault-update)를 사용하여 키 자격 증명 모음에 대한 디스크 암호화를 사용하도록 설정합니다. 

 - **디스크 암호화에 Key Vault 사용:** Enabled-for-disk-encryption이 필요합니다. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **필요한 경우 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 리소스를 만들 때(예: 가상 머신 만들기) 참조되는 경우 Microsoft.Compute 리소스 공급자에서 이 키 자격 증명 모음으로부터 비밀을 검색할 수 있도록 합니다.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **필요한 경우 템플릿 배포에 Key Vault 사용:** Resource Manager에서 자격 증명 모음으로부터 비밀을 검색할 수 있도록 허용합니다.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Key vault PowerShell cmdlet [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 을 사용 하 여 키 자격 증명 모음에 대한 디스크 암호화를 사용 하도록 설정 합니다.

  - **디스크 암호화에 Key Vault 사용:** Azure Disk Encryption에 EnabledForDiskEncryption이 필요합니다.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **필요한 경우 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 리소스를 만들 때(예: 가상 머신 만들기) 참조되는 경우 Microsoft.Compute 리소스 공급자에서 이 키 자격 증명 모음으로부터 비밀을 검색할 수 있도록 합니다.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **필요한 경우 템플릿 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 템플릿 배포에서 참조되는 경우 Azure Resource Manager에서 이 키 자격 증명 모음으로부터 비밀을 가져올 수 있도록 합니다.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. 키 자격 증명 모음을 선택 하 고 **액세스 정책**으로 이동한 다음 **고급 액세스 정책을 클릭 하 여 표시**합니다.
2. **볼륨 암호화를 위해 Azure Disk Encryption에 대한 액세스 사용**이라는 레이블이 있는 상자를 선택합니다.
3. 필요한 경우 **배포를 위해 Azure Virtual Machines에 대한 액세스 사용** 및/또는 **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용**을 선택합니다. 
4. 페이지 맨 아래에 있는 **저장**을 참조하세요.

    ![Azure 키 자격 증명 모음에 대한 고급 액세스 정책](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>키 암호화 키 설정 (KEK)

암호화 키에 대한 추가 보안 계층에 KEK(키 암호화 키)를 사용하려면 키 자격 증명 모음에 KEK를 추가합니다. 키 암호화 키가 지정되면 Azure Disk Encryption에서 해당 키를 사용하여 Key Vault에 쓰기 전에 암호화 비밀을 래핑합니다.

Azure CLI [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 명령, Azure PowerShell [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 또는 [Azure Portal](https://portal.azure.com/)를 사용 하 여 새 KEK을 생성할 수 있습니다. RSA 키 유형을 생성 해야 합니다. Azure Disk Encryption은 아직 타원 Curve 키 사용을 지원 하지 않습니다.

대신 온-프레미스 키 관리 HSM에서 KEK를 가져올 수 있습니다. 자세한 내용은 [Key Vault 설명서](/azure/key-vault/key-vault-hsm-protected-keys)를 참조하세요.

주요 자격 증명 모음 KEK Url의 버전을 지정 해야 합니다. Azure에서 이 버전 관리 제한을 적용합니다. 유효한 비밀과 KEK URL은 다음 예제를 참조하세요.

* 유효한 비밀 URL의 예: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 올바른 KEK URL의 예: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption은 키 자격 증명 모음 비밀 및 KEK URL의 일부로 포트 번호 지정을 지원하지 않습니다. 지원되거나 지원되지 않는 키 자격 증명 모음 URL에 대한 예제는 다음 예제를 참조하세요.

  * 허용 되는 키 자격 증명 모음 URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 허용 되지 않는 키 자격 증명 모음 URL: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Azure CLI [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 명령을 사용 하 여 새 KEK를 생성 하 고 키 자격 증명 모음에 저장 합니다.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. 대신 Azure CLI [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 명령을 사용 하 여 개인 키를 가져올 수 있습니다.

두 경우 모두 Azure CLI [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --KEK 매개 변수에 대한 이름을 제공 합니다. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Azure PowerShell [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) cmdlet을 사용 하 여 새 KEK를 생성 하 고 키 자격 증명 모음에 저장 합니다.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

대신 Azure PowerShell [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 명령을 사용 하 여 개인 키를 가져올 수 있습니다.

두 경우 모두 KEK key Vault의 ID와 KEK URL을 Azure PowerShell [AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId 및-하면 keyencryptionkeyurl 매개 변수에 제공 합니다. 이 예에서는 사용자가 디스크 암호화 키와 KEK 모두에 동일한 키 자격 증명 모음을 사용 한다고 가정 합니다.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
