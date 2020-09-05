---
title: Azure AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성
description: 이 문서에서는 Linux Vm에 대해 Microsoft Azure 디스크 암호화를 사용 하기 위한 필수 구성 요소를 제공 합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 4b533fa23d3c128b5f9f75737fb88d39aec94905
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950071"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release-for-linux-vms"></a>Linux Vm 용 Azure AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption에 대 한 키 자격 증명 모음 만들기 및 구성

**Azure Disk Encryption의 새 릴리스는 Azure AD 응용 프로그램 매개 변수를 제공 하 여 VM 디스크 암호화를 사용 하도록 설정 하는 요구 사항을 제거 합니다. 새 릴리스부터는 암호화 사용 단계에서 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 새 릴리스를 사용 하 여 Azure AD 응용 프로그램 매개 변수 없이 모든 새 Vm을 암호화 해야 합니다. 새 릴리스를 사용 하 여 VM 디스크 암호화를 사용 하도록 설정 하는 지침을 보려면 [Azure Disk Encryption](disk-encryption-overview.md)를 참조 하세요. 이미 Azure AD 응용 프로그램 매개 변수를 사용 하 여 암호화 된 Vm은 계속 지원 되며 AAD 구문을 사용 하 여 계속 유지 되어야 합니다.**

Azure Disk Encryption은 Azure Key Vault를 사용하여 키 디스크 암호화 키와 비밀을 제어하고 관리합니다.  키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../../key-vault/general/overview.md) 및 [키 자격 증명 모음 보안](../../key-vault/general/secure-your-key-vault.md)을 참조하세요. 

Azure AD (이전 릴리스)에서 Azure Disk Encryption와 함께 사용할 주요 자격 증명 모음을 만들고 구성 하는 단계는 다음 세 단계로 구성 됩니다.

1. 키 자격 증명 모음을 만듭니다. 
2. Azure AD 애플리케이션 및 서비스 주체를 설정합니다.
3. Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책을 설정합니다.
4. 키 자격 증명 모음에 대한 고급 액세스 정책을 설정합니다.
 
원할 경우 KEK(키 암호화 키)를 생성하거나 가져올 수도 있습니다.

[도구를 설치 하 고 Azure에 연결](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)하는 방법에 대 한 단계는 기본 [Azure Disk Encryption에 대 한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md) 문서를 참조 하세요.

> [!Note]
> 이 문서의 단계는 [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started) 및 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)에서 자동화됩니다.


## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기 
Azure Disk Encryption은 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)와 통합되어 키 자격 증명 모음 구독에서 디스크 암호화 키 및 비밀을 제어하고 관리할 수 있습니다. Azure Disk Encryption에 대한 키 자격 증명 모음을 만들거나 기존 키 자격 증명 모음을 사용할 수 있습니다. 키 자격 증명 모음에 대한 자세한 내용은 [Azure Key Vault 시작](../../key-vault/general/overview.md) 및 [키 자격 증명 모음 보안](../../key-vault/general/secure-your-key-vault.md)을 참조하세요. Resource Manager 템플릿, Azure PowerShell 또는 Azure CLI를 사용하여 키 자격 증명 모음을 만들 수 있습니다. 


>[!WARNING]
>암호화 비밀이 지역 경계를 넘지 않도록 하려면 Azure Disk Encryption에서 Key Vault와 VM을 동일한 지역에 공동 배치해야 합니다. 암호화할 VM과 동일한 지역에 있는 Key Vault를 만들고 사용합니다. 


### <a name="create-a-key-vault-with-powershell"></a><a name="bkmk_KVPSH"></a> PowerShell을 사용하여 키 자격 증명 모음 만들기

[AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet을 사용 하 여 Azure PowerShell를 사용 하 여 키 자격 증명 모음을 만들 수 있습니다. Key Vault에 대 한 추가 cmdlet은 [Az. KeyVault](/powershell/module/az.keyvault/)를 참조 하세요. 

1. [AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)를 사용 하 여 필요한 경우 새 리소스 그룹을 만듭니다.  데이터 센터 위치를 나열 하려면 [AzLocation](/powershell/module/az.resources/get-azlocation)를 사용 합니다. 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. [AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) 를 사용 하 여 새 key vault 만들기
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. 나중에 디스크를 암호화할 때 사용하기 위해 반환되는 **자격 증명 모음 이름**, **리소스 그룹 이름**, **리소스 ID**, **자격 증명 모음 URI** 및 **개체 ID**를 적어 둡니다. 


### <a name="create-a-key-vault-with-azure-cli"></a><a name="bkmk_KVCLI"></a> Azure CLI를 사용하여 키 자격 증명 모음 만들기
[az keyvault](/cli/azure/keyvault#commands) 명령을 사용하여 Azure CLI를 통해 키 자격 증명 모음을 관리할 수 있습니다. 키 자격 증명 모음을 만들려면 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)를 사용합니다.

1. 필요한 경우 [az group create](/cli/azure/group#az-group-create)를 사용하여 새 리소스 그룹을 만듭니다. 위치를 나열하려면 [az account list-locations](/cli/azure/account#az-account-list)를 사용합니다. 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. [az keyvault create](/cli/azure/keyvault#az-keyvault-create)를 사용하여 새 키 자격 증명 모음을 만듭니다.
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. 나중에 사용하기 위해 반환되는 **자격 증명 모음 이름**(name), **리소스 그룹 이름**, **리소스 ID**(ID), **자격 증명 모음 URI** 및 **개체 ID**를 적어 둡니다. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a><a name="bkmk_KVRM"></a> Resource Manager 템플릿을 사용하여 키 자격 증명 모음 만들기

[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)을 사용하여 키 자격 증명 모음을 만들 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭합니다.
2. 구독, 리소스 그룹, 리소스 그룹 위치, Key Vault 이름, 개체 ID, 약관 및 규약을 선택한 다음, **구매**를 클릭합니다. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a><a name="bkmk_ADapp"></a> Azure AD 응용 프로그램 및 서비스 주체 설정 
Azure에서 실행 중인 VM에서 암호화를 사용하도록 설정해야 하는 경우 Azure Disk Encryption이 암호화 키를 생성하고 Key Vault에 기록합니다. Key Vault에서 암호화 키를 관리하려면 Azure AD 인증이 필요합니다. 이를 위해 Azure AD 애플리케이션을 만듭니다. 인증을 위해 클라이언트 암호 기반 인증 또는 [클라이언트 인증서 기반 AZURE AD 인증](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)을 사용할 수 있습니다.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a><a name="bkmk_ADappPSH"></a> Azure PowerShell을 사용하여 Azure AD 응용 프로그램 및 서비스 주체 설정 
다음 명령을 실행하려면 [Azure AD PowerShell 모듈](/powershell/azure/active-directory/install-adv2)을 가져와 사용합니다. 

1. [AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell cmdlet을 사용 하 여 Azure AD 응용 프로그램을 만듭니다. MyApplicationHomePage 및 MyApplicationUri는 원하는 값이 될 수 있습니다.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId는 Azure AD ClientID이고, $aadClientSecret은 나중에 Azure Disk Encryption을 사용하도록 설정하는 데 사용할 클라이언트 비밀입니다. Azure AD 클라이언트 비밀을 적절하게 보호합니다. `$azureAdApplication.ApplicationId`를 실행하면 ApplicationID가 표시됩니다.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a><a name="bkmk_ADappCLI"></a> Azure CLI를 사용하여 Azure AD 응용 프로그램 및 서비스 주체 설정

[az ad sp](/cli/azure/ad/sp) 명령을 사용하여 Azure CLI를 통해 서비스 주체를 관리할 수 있습니다. 자세한 내용은 [Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조 하세요.

1. 새 서비스 주체를 만듭니다.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  반환된 appId는 다른 명령에 사용되는 Azure AD ClientID이며, az keyvault set-policy에 사용할 SPN이기도 합니다. 암호는 나중에 Azure Disk Encryption을 사용하도록 설정하는 데 사용해야 하는 클라이언트 비밀입니다. Azure AD 클라이언트 비밀을 적절하게 보호합니다.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a><a name="bkmk_ADappRM"></a> Azure Portal을 통해 Azure AD 응용 프로그램 및 서비스 주체 설정
[포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 문서의 단계를 사용하여 Azure AD 애플리케이션을 만듭니다. 아래에 나열된 각 단계는 수행할 문서 섹션으로 바로 이동합니다. 

1. [필요한 권한 확인](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Azure Active Directory 애플리케이션 만들기](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - 애플리케이션을 만들 때 원하는 이름과 로그온 URL을 사용할 수 있습니다.
3. [애플리케이션 ID 및 인증 키 가져오기](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 
     - 인증 키는 클라이언트 암호 이며 AzVMDiskEncryptionExtension의 AadClientSecret로 사용 됩니다. 
        - 인증 키는 애플리케이션에서 Azure AD에 로그인하기 위한 자격 증명으로 사용됩니다. Azure Portal에서 이 비밀은 키라고 하지만, 키 자격 증명 모음과는 아무런 관련이 없습니다. 이 비밀을 적절하게 보호하세요. 
     - 응용 프로그램 ID는 나중에 AzVMDiskEncryptionExtension에 대 한 AadClientId 및 ServicePrincipalName (AzKeyVaultAccessPolicy의 경우)로 사용 됩니다. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a><a name="bkmk_KVAP"></a> Azure AD 앱에 대 한 키 자격 증명 모음 액세스 정책 설정
지정된 Key Vault에 암호화 비밀을 쓰려면 Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure Active Directory 애플리케이션의 클라이언트 ID와 클라이언트 비밀이 Azure Disk Encryption에 필요합니다. 

> [!NOTE]
> Azure Disk Encryption에서는 Azure AD 클라이언트 애플리케이션에 _WrapKey_ 및 _Set_ 권한과 같은 액세스 정책을 구성해야 합니다.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a><a name="bkmk_KVAPPSH"></a> Azure PowerShell을 사용하여 Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책 설정
Azure AD 애플리케이션에 자격 증명 모음의 키 또는 암호에 액세스할 권한이 필요합니다. [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet을 사용 하 여 응용 프로그램을 등록할 때 생성 된 클라이언트 ID를 _– ServicePrincipalName_ 매개 변수 값으로 사용 하 여 응용 프로그램에 권한을 부여 합니다. 자세한 내용은 블로그 게시물 [Azure Key Vault - 단계별](/archive/blogs/kv/azure-key-vault-step-by-step)을 참조하세요. 

1. PowerShell을 사용하여 AD 애플리케이션에 대한 키 자격 증명 모음 액세스 정책을 설정합니다.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a><a name="bkmk_KVAPCLI"></a> Azure CLI를 사용하여 Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책 설정
[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)를 사용하여 액세스 정책을 설정합니다. 자세한 내용은 [CLI 2.0을 사용하여 Key Vault 관리](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)를 참조하세요.

다음 명령을 사용하여 비밀 및 래핑 키를 가져오려면 Azure CLI 액세스를 통해 만든 서비스 주체에게 액세스 권한을 부여합니다.

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a><a name="bkmk_KVAPRM"></a> 포털을 사용하여 Azure AD 응용 프로그램에 대한 키 자격 증명 모음 액세스 정책 설정

1. 키 자격 증명 모음을 사용하여 리소스 그룹을 엽니다.
2. 키 자격 증명 모음을 선택하고, **액세스 정책**으로 이동한 다음, **새로 추가**를 클릭합니다.
3. **보안 주체 선택** 아래에서 만든 Azure AD 애플리케이션을 검색하여 선택합니다. 
4. **키 권한**에 대해 **암호화 작업** 아래에서 **키 래핑**을 선택합니다.
5. **비밀 권한**에 대해 **비밀 관리 작업** 아래에서 **설정**을 선택합니다.
6. **확인**을 클릭하여 액세스 정책을 저장합니다. 

![Azure Key Vault 암호화 작업 - 키 래핑](./media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault 비밀 권한 - 설정](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a><a name="bkmk_KVper"></a> Key vault 고급 액세스 정책 설정
Azure 플랫폼은 VM을 부팅하고 볼륨을 해독할 수 있도록 Key Vault의 암호화 키 또는 비밀에 액세스해야 합니다. 키 자격 증명 모음에서 디스크 암호화를 사용하도록 설정하지 않으면 배포가 실패합니다.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a><a name="bkmk_KVperPSH"></a> Azure PowerShell을 사용하여 키 자격 증명 모음에 대한 고급 액세스 정책 설정
 Key vault PowerShell cmdlet [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 을 사용 하 여 키 자격 증명 모음에 대 한 디스크 암호화를 사용 하도록 설정 합니다.

  - **디스크 암호화에 Key Vault 사용:** Azure Disk Encryption에 EnabledForDiskEncryption이 필요합니다.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **필요한 경우 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 리소스를 만들 때(예: 가상 머신 만들기) 참조되는 경우 Microsoft.Compute 리소스 공급자에서 이 키 자격 증명 모음으로부터 비밀을 검색할 수 있도록 합니다.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **필요한 경우 템플릿 배포에 Key Vault 사용:** 이 키 자격 증명 모음이 템플릿 배포에서 참조되는 경우 Azure Resource Manager에서 이 키 자격 증명 모음으로부터 비밀을 가져올 수 있도록 합니다.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a><a name="bkmk_KVperCLI"></a> Azure CLI를 사용하여 키 자격 증명 모음에 대한 고급 액세스 정책 설정
[az keyvault update](/cli/azure/keyvault#az-keyvault-update)를 사용하여 키 자격 증명 모음에 대한 디스크 암호화를 사용하도록 설정합니다. 

 - **디스크 암호화에 Key Vault 사용:** Enabled-for-disk-encryption이 필요합니다. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **필요한 경우 배포에 Key Vault 사용:** Virtual Machines에서 자격 증명 모음으로부터 비밀로 저장된 인증서를 검색할 수 있도록 허용합니다.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **필요한 경우 템플릿 배포에 Key Vault 사용:** Resource Manager에서 자격 증명 모음으로부터 비밀을 검색할 수 있도록 허용합니다.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a><a name="bkmk_KVperrm"></a> Azure Portal을 통해 키 자격 증명 모음에 대한 고급 액세스 정책 설정

1. 키 자격 증명 모음을 선택하고, **액세스 정책**, **클릭하여 고급 액세스 정책 표시**로 차례로 이동합니다.
2. **볼륨 암호화를 위해 Azure Disk Encryption에 대한 액세스 사용**이라는 레이블이 있는 상자를 선택합니다.
3. 필요한 경우 **배포를 위해 Azure Virtual Machines에 대한 액세스 사용** 및/또는 **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용**을 선택합니다. 
4. **저장**을 클릭합니다.

![Azure 키 자격 증명 모음에 대한 고급 액세스 정책](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a><a name="bkmk_KEK"></a> 키 암호화 키 설정 (선택 사항)
암호화 키에 대한 추가 보안 계층에 KEK(키 암호화 키)를 사용하려면 키 자격 증명 모음에 KEK를 추가합니다. [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet을 사용 하 여 키 자격 증명 모음에 키 암호화 키를 만듭니다. 또한 온-프레미스 키 관리 HSM에서 KEK를 가져올 수도 있습니다. 자세한 내용은 [Key Vault 설명서](../../key-vault/keys/hsm-protected-keys.md)를 참조 하세요. 키 암호화 키가 지정되면 Azure Disk Encryption에서 해당 키를 사용하여 Key Vault에 쓰기 전에 암호화 비밀을 래핑합니다. 

* 키를 생성할 때 RSA 키 유형을 사용 합니다. Azure Disk Encryption은 아직 타원 Curve 키 사용을 지원 하지 않습니다.

* Key Vault 비밀 및 KEK URL 버전을 지정해야 합니다. Azure에서 이 버전 관리 제한을 적용합니다. 유효한 비밀과 KEK URL은 다음 예제를 참조하세요.

  * 유효한 비밀 URL의 예는 다음과 같습니다.   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 유효한 KEK URL의 예는 다음과 같습니다.   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption은 키 자격 증명 모음 비밀 및 KEK URL의 일부로 포트 번호 지정을 지원하지 않습니다. 지원되거나 지원되지 않는 키 자격 증명 모음 URL에 대한 예제는 다음 예제를 참조하세요.

  * 허용 되지 않는 주요 자격 증명 모음 URL  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 허용 되는 주요 자격 증명 모음 URL:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a><a name="bkmk_KEKPSH"></a> Azure PowerShell을 사용하여 키 암호화 키 설정 
PowerShell 스크립트를 사용하기 전에 스크립트의 단계를 이해할 수 있도록 Azure Disk Encryption 필수 구성 요소를 잘 알고 있어야 합니다. 샘플 스크립트는 사용자 환경에 맞게 변경해야 할 수도 있습니다. 이 스크립트는 모든 Azure Disk Encryption 필수 구성 요소를 만들고, 기존 IaaS VM을 암호화하여 키 암호화 키로 디스크 암호화 키를 래핑합니다. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a><a name="bkmk_Cert"></a> 인증서 기반 인증(선택 사항)
인증서 인증을 사용하려면 키 자격 증명 모음에 인증서를 업로드하고 이 인증서를 클라이언트에 배포할 수 있습니다. PowerShell 스크립트를 사용하기 전에 스크립트의 단계를 이해할 수 있도록 Azure Disk Encryption 필수 구성 요소를 잘 알고 있어야 합니다. 샘플 스크립트는 사용자 환경에 맞게 변경해야 할 수도 있습니다.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a><a name="bkmk_CertKEK"></a> 인증서 기반 인증 및 KEK(선택 사항)

인증서 인증을 사용하고 KEK로 암호화 키를 래핑하려면 아래 스크립트를 예로 사용할 수 있습니다. PowerShell 스크립트를 사용하기 전에 스크립트의 단계를 이해할 수 있도록 이전의 Azure Disk Encryption 필수 구성 요소를 모두 잘 알고 있어야 합니다. 샘플 스크립트는 사용자 환경에 맞게 변경해야 할 수도 있습니다.

> [!IMPORTANT]
> Azure AD 인증서 기반 인증은 현재 Linux VM에서 지원되지 않습니다.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>다음 단계

[Linux Vm에서 Azure AD를 사용 하 여 Azure Disk Encryption 사용 (이전 릴리스)](disk-encryption-linux-aad.md)
