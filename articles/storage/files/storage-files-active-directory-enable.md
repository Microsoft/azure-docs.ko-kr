---
title: Azure Files용 SMB(미리 보기)를 통해 Azure Active Directory 인증 사용 - Azure Storage
description: Azure AD(Active Directory) Domain Services를 사용하여 Azure Files용 SMB(서버 메시지 블록)(미리 보기)를 통해 ID 기반 인증을 사용하도록 설정하는 방법을 알아봅니다. 도메인 조인 Windows VM(가상 머신)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: rogarana
ms.openlocfilehash: 974a4341bd140da60c5e229a644657fe7ab02535
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766407"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Azure Files용 SMB(미리 보기)를 통해 Azure Active Directory 인증 사용
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure Files용 SMB를 통한 Azure AD 인증의 개요는 [Azure Files용 SMB(미리 보기)를 통한 Azure Active Directory 인증의 개요](storage-files-active-directory-overview.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>워크플로의 개요
Azure Files용 SMB를 통해 Azure AD를 사용할 수 있도록 설정하기 전에 Azure AD 및 Azure Storage 환경이 적절하게 구성되어 있는지 확인합니다. [필수 구성 요소](#prerequisites)를 검토하여 필요한 모든 단계를 수행했는지 확인하는 것이 좋습니다. 

그런 후에는 다음 단계를 수행하여 Azure AD 자격 증명을 사용한 Azure Files 리소스 액세스 권한을 부여합니다. 

1. 저장소 계정에 대해 SMB를 통한 Azure AD 인증을 사용하도록 설정하여 연결된 Azure AD Domain Services 배포에 저장소 계정을 등록합니다.
2. Azure AD ID(사용자, 그룹 또는 서비스 주체)에 공유 액세스 권한을 할당합니다.
3. 디렉터리와 파일에 대해 SMB를 통한 NTFS 권한을 구성합니다.
4. 도메인 조인 VM에서 Azure 파일 공유를 탑재합니다.

아래 다이어그램에는 Azure Files용 SMB를 통해 Azure AD 인증을 사용하도록 설정하는 전체 워크플로가 나와 있습니다. 

![Azure Files용 SMB를 통한 Azure AD 워크플로를 보여 주는 다이어그램](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>필수 조건 

Azure Files에 대한 SMB를 통한 Azure AD를 사용하도록 설정하기 전에 다음 필수 조건을 완료했는지 확인합니다.

1.  **Azure AD 테넌트를 선택하거나 만듭니다.**

    SMB를 통한 Azure AD 인증에는 새 테넌트나 기존 테넌트를 사용할 수 있습니다. 액세스하려는 파일 공유와 테넌트는 같은 구독과 연결되어 있어야 합니다.

    새 Azure AD 테넌트를 만들려는 경우 [Azure AD 테넌트 및 Azure AD 구독을 추가](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)하면 됩니다. 기존 Azure AD 테넌트가 있지만 Azure Files에 사용할 새 테넌트를 만들려는 경우에는 [Azure Active Directory 테넌트 만들기](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)를 참조하세요.

2.  **Azure AD 테넌트에서 Azure AD Domain Services를 사용하도록 설정합니다.**

    Azure AD 자격 증명을 사용한 인증을 지원하려면 Azure AD 테넌트에 대해 Azure AD Domain Services를 사용하도록 설정해야 합니다. Azure AD 테넌트의 관리자가 아니라면 관리자에게 문의하고 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/active-directory-ds-getting-started.md)를 위한 단계별 지침을 따르세요.

    일반적으로는 Azure AD Domain Services 배포를 완료하려면 15분 정도 걸립니다. 다음 단계로 진행하기 전에 Azure AD Domain Services의 상태가 **실행 중**으로 표시되고 암호 해시 동기화가 사용하도록 설정되어 있는지 확인합니다.

3.  **Azure VM을 Azure AD Domain Services에 도메인 조인합니다.**

    VM에서 Azure AD 자격 증명을 사용하여 파일 공유에 액세스하려면 VM을 Azure AD Domain Services에 도메인 조인해야 합니다. VM을 도메인 조인하는 방법에 대한 자세한 내용은 [Windows Server 가상 머신을 관리되는 도메인에 조인](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md)을 참조하세요.

    > [!NOTE]
    > Azure Files용 SMB를 통한 Azure AD 인증은 Windows 7 또는 Windows Server 2008 R2 이상의 OS 버전에서 실행 중인 Azure VM에서만 지원됩니다.

4.  **Azure 파일 공유를 선택하거나 만듭니다.**

    Azure AD 테넌트와 같은 구독에 연결된 신규 또는 기존 파일 공유를 선택합니다. 새 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure Files에 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요. 
    성능을 최적화하려면 파일 공유를 해당 공유에 액세스하려는 VM과 같은 지역에 배치하는 것이 좋습니다.

5.  **저장소 계정 키를 사용하여 Azure 파일 공유를 탑재해 Azure Files 연결을 확인합니다.**

    VM 및 파일 공유가 적절하게 구성되었는지 확인하려면 저장소 계정 키를 사용하여 파일 공유를 탑재해 봅니다. 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

## <a name="enable-azure-ad-authentication-for-your-account"></a>사용자 계정에 Azure AD 인증 사용

Azure Files용 SMB를 통해 Azure AD 인증을 사용하도록 설정하려는 경우 Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 2018년 9월 24일 이후에 만든 저장소 계정에서 속성을 설정할 수 있습니다. 이 속성을 설정하면 연결된 Azure AD Domain Services 배포에 저장소 계정이 등록됩니다. 그러면 저장소 계정의 모든 신규 및 기존 파일 공유에 대해 SMB를 통한 Azure AD 인증이 사용하도록 설정됩니다. 

Azure AD Domain Services를 Azure AD 테넌트에 정상적으로 배포해야 SMB를 통한 Azure AD 인증을 사용하도록 설정할 수 있습니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조하세요.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)을 사용하여 SMB를 통한 Azure AD 인증을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 기존 저장소 계정으로 이동하거나 [저장소 계정을 만듭니다](../common/storage-quickstart-create-account.md).
2. **설정** 섹션에서 **구성**을 선택합니다.
3. **Azure Files용 Azure Active Directory 인증(미리 보기)** 을 사용하도록 설정합니다.

저장소 계정에 대해 SMB를 통한 Azure AD 인증을 사용하도록 설정하는 방법이 다음 이미지에 나와 있습니다.

![Azure Portal에서 SMB를 통한 Azure AD 인증 사용](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Azure PowerShell에서 SMB를 통한 Azure AD 인증을 사용하도록 설정하려면 먼저 Azure AD를 지원하는 `Az.Storage` 모듈의 미리 보기 빌드를 설치합니다. PowerShell을 설치하는 방법에 대한 자세한 내용은 [PowerShellGet을 사용하여 Windows에 Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/install-Az-ps)를 참조하세요.

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

다음으로, 새 스토리지 계정을 만든 다음, [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount)를 호출하고, **EnableAzureFilesAadIntegrationForSMB** 매개 변수를 **true**로 설정합니다. 아래 예제의 자리 표시자 값은 실제 값으로 바꾸세요.

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI 2.0에서 SMB를 통한 Azure AD 인증을 사용하도록 설정하려면 먼저 `storage-preview` 확장을 설치합니다.

```cli-interactive
az extension add --name storage-preview
```
  
다음으로 새 스토리지 계정을 만든 다음, [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)를 호출하고 `--file-aad` 속성을 **true**로 설정합니다. 아래 예제의 자리 표시자 값은 실제 값으로 바꾸세요.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>ID에 액세스 권한 할당 

Azure AD 자격 증명을 사용하여 Azure Files 리소스에 액세스하려는 경우 ID(사용자, 그룹 또는 서비스 주체)에 공유 수준에서 필요한 권한이 있어야 합니다. 이 섹션의 지침에서는 파일 공유에 대한 읽기, 쓰기 또는 삭제 권한을 ID에 할당하는 방법을 보여줍니다.

> [!IMPORTANT]
> ID에 역할을 할당하는 기능을 비롯하여 파일 공유에 대한 모든 관리 권한을 할당하려면 저장소 계정 키를 사용해야 합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다. 

### <a name="define-a-custom-role"></a>사용자 지정 역할 정의

공유 수준 권한을 부여하려면 사용자 지정 RBAC 역할을 정의한 다음 특정 ID에 할당하고 범위를 특정 파일 공유로 지정합니다. 이 프로세스는 파일 공유에 대한 특정 사용자의 액세스 권한 유형을 지정하는 Windows 공유 권한 지정 과정과 비슷합니다.  

다음 섹션에 나와 있는 템플릿은 파일 공유에 대한 읽기 또는 변경 권한을 제공합니다. 사용자 지정 역할을 정의하려면 JSON 파일을 만들고 적절한 템플릿을 해당 파일에 복사합니다. 사용자 지정 RBAC 역할을 정의하는 방법에 대한 자세한 내용은 [Azure의 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조하세요.

**공유 수준 변경 권한을 위한 역할 정의**  
다음 사용자 지정 역할 템플릿은 공유 수준 변경 권한을 제공하여 공유에 대한 읽기, 쓰기 및 삭제 권한을 ID에 부여합니다.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share over SMB",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**공유 수준 읽기 권한을 위한 역할 정의**  
다음 사용자 지정 역할 템플릿은 공유 수준 읽기 권한을 제공하여 공유에 대한 읽기 권한을 ID에 부여합니다.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share over SMB",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

### <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 PowerShell 또는 Azure CLI를 사용합니다. 

#### <a name="powershell"></a>PowerShell

다음 PowerShell 명령은 샘플 템플릿 중 하나를 기반으로 사용자 지정 역할을 만듭니다.

```powershell
#Create a custom role based on the sample template above
New-AzRoleDefinition -InputFile "<custom-role-def-json-path>"
```

#### <a name="cli"></a>CLI 

다음 Azure CLI 명령은 샘플 템플릿 중 하나를 기반으로 사용자 지정 역할을 만듭니다.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
```

### <a name="assign-the-custom-role-to-the-target-identity"></a>대상 ID에 사용자 지정 역할 할당

다음으로는 PowerShell 또는 Azure CLI를 사용하여 Azure AD ID에 사용자 지정 역할을 할당합니다. 

#### <a name="powershell"></a>PowerShell

다음 PowerShell 명령은 사용 가능한 사용자 지정 역할을 나열한 다음, 사용자 지정 역할을 로그인 이름을 기반으로 하는 Azure AD ID에 할당하는 방법을 보여줍니다. PowerShell을 사용하여 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure PowerShell을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-powershell.md)를 참조하세요.

다음 샘플 스크립트를 실행할 때 대괄호를 포함한 자리 표시자 값을 사용자 고유의 값으로 변경해야 합니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
다음 CLI 2.0 명령은 사용 가능한 사용자 지정 역할을 나열한 다음, 사용자 지정 역할을 로그인 이름을 기반으로 하는 Azure AD ID에 할당하는 방법을 보여줍니다. Azure CLI를 사용하여 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리](../../role-based-access-control/role-assignments-cli.md)를 참조하세요. 

다음 샘플 스크립트를 실행할 때 대괄호를 포함한 자리 표시자 값을 사용자 고유의 값으로 변경해야 합니다.

```azurecli-interactive
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custom-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>SMB를 통한 NTFS 권한 구성 
RBAC를 사용하여 공유 수준 권한할당을 한 후에는 루트, 디렉터리 또는 파일 수준에서 적절한 NTFS 권한을 할당해야 합니다. 공유 수준 권한은 사용자가 공유에 액세스할 수 있는지 여부를 결정하는 높은 수준의 게이트키퍼 역할을 하는 반면, NTFS 권한은 더 세분화된 수준에서 작동하여 디렉터리 또는 파일 수준에서 사용자가 수행할 수 있는 작업을 결정합니다. 

Azure Files는 NTFS 기본 및 고급 권한의 전체 집합을 지원합니다. 공유를 탑재한 다음 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 또는 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 명령을 실행하여 Azure 파일 공유의 디렉터리와 파일에 대한 NTFS 권한을 확인하고 구성할 수 있습니다. 

> [!NOTE]
> 미리 보기 릴리스에서는 Windows 파일 탐색기를 사용한 권한 보기만 지원됩니다. 권한 편집은 아직 지원되지 않습니다.

슈퍼 사용자 권한으로 NTFS 권한을 구성하려면 도메인 조인 VM에서 저장소 계정 키를 사용하여 공유를 탑재해야 합니다. 다음 섹션의 지침에 따라 명령 프롬프트에서 Azure 파일 공유를 탑재하고 NTFS 권한을 적절하게 구성하세요.

파일 공유의 루트 디렉터리에서 지원되는 권한 집합은 다음과 같습니다.

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>명령 프롬프트에서 파일 공유 탑재

Windows **net use** 명령을 사용하여 Azure 파일 공유를 탑재합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 파일 공유 탑재에 대한 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>icacls를 사용하여 NTFS 권한 구성
루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 다음 Windows 명령을 사용합니다. 예제의 대괄호 안에 표시된 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

icacls를 사용하여 NTFS 권한을 설정하는 방법과 지원되는 여러 권한 유형에 대한 자세한 내용은 [icacls용 명령줄 참조](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)를 참조하세요.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>도메인 조인 VM에서 파일 공유 탑재 

이제 Azure AD 자격 증명을 사용해 도메인 조인 VM에서 Azure 파일 공유에 액세스하여 위의 단계를 올바르게 완료했는지를 확인할 준비가 되었습니다. 먼저 아래 그림과 같이 권한을 부여한 Azure AD ID를 사용하여 VM에 로그인합니다.

![사용자 인증용 Azure AD 로그인 화면이 표시된 스크린샷](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

그런 후에 다음 명령을 사용하여 Azure 파일 공유를 탑재합니다. 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 이미 인증이 되었기 때문에 저장소 계정 키 또는 Azure AD 사용자 이름과 암호는 입력하지 않아도 됩니다. SMB를 통한 Azure AD에서는 Azure AD 자격 증명을 사용하는 Single Sign-On 환경을 지원합니다.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

이제 SMB를 통한 Azure AD 인증을 올바르게 사용하도록 설정했으며, 파일 공유 액세스 권한을 제공하는 사용자 지정 역할을 Azure AD ID에 할당했습니다. 추가 사용자에게 파일 공유 액세스 권한을 부여하려면 2 및 3단계에 나와 있는 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

Azure Files 및 SMB를 통해 Azure AD를 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [Azure Files 소개](storage-files-introduction.md)
- [Azure Files용 SMB(미리 보기)를 통한 Azure Active Directory 인증 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
