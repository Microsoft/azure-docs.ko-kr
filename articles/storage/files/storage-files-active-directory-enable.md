---
title: Azure AD Domain Services를 사용 하 여 SMB를 통해 파일 데이터에 대 한 액세스 권한 부여
description: Azure Active Directory Domain Services를 통해 Azure Files에 대해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 사용 하도록 설정 하는 방법에 대해 알아봅니다. 그런 다음 도메인에 가입 된 Windows Vm (가상 머신)은 Azure AD 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06ff14b23057755a643e5a57fbaf711798cca00e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210485"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Files에서 Azure Active Directory Domain Services 인증 사용

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure Files에서 지원 되는 id 기반 인증에 대 한 개요는 [Azure Files에 대 한 SMB를 통한 Azure Active Directory 인증 개요](storage-files-active-directory-overview.md)를 참조 하세요. 이 문서는 Azure Files에서 Azure Active Directory Domain Services (Azure AD DS)를 사용 하 여 인증을 사용 하도록 설정 하는 방법에 중점을 두었습니다. 
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>워크플로의 개요

Azure Files에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하기 전에 Azure AD 및 Azure Storage 환경이 제대로 구성 되어 있는지 확인 합니다. 필요한 모든 단계를 완료 했는지 확인 하기 위해 [필수 구성 요소](#prerequisites) 를 안내 하는 것이 좋습니다.

그런 후에는 다음 단계를 수행하여 Azure AD 자격 증명을 사용한 Azure Files 리소스 액세스 권한을 부여합니다.

1. 저장소 계정에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하 여 연결 된 Azure AD DS 배포에 저장소 계정을 등록 합니다.
2. Azure AD ID(사용자, 그룹 또는 서비스 주체)에 공유 액세스 권한을 할당합니다.
3. 디렉터리와 파일에 대해 SMB를 통한 NTFS 권한을 구성합니다.
4. 도메인 조인 VM에서 Azure 파일 공유를 탑재합니다.

다음 다이어그램에서는 Azure Files에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하는 종단 간 워크플로를 보여 줍니다.

![Azure Files용 SMB를 통한 Azure AD 워크플로를 보여 주는 다이어그램](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>사전 요구 사항

Azure Files에 대한 SMB를 통한 Azure AD를 사용하도록 설정하기 전에 다음 필수 조건을 완료했는지 확인합니다.

1.  **Azure AD 테넌트를 선택하거나 만듭니다.**

    SMB를 통한 Azure AD 인증에는 새 테넌트나 기존 테넌트를 사용할 수 있습니다. 액세스하려는 파일 공유와 테넌트는 같은 구독과 연결되어 있어야 합니다.

    새 Azure AD 테넌트를 만들려는 경우 [Azure AD 테넌트 및 Azure AD 구독을 추가](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)하면 됩니다. 기존 Azure AD 테넌트가 있지만 Azure Files에 사용할 새 테넌트를 만들려는 경우에는 [Azure Active Directory 테넌트 만들기](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)를 참조하세요.

2.  **Azure AD 테넌트에서 Azure AD Domain Services를 사용하도록 설정합니다.**

    Azure AD 자격 증명을 사용한 인증을 지원하려면 Azure AD 테넌트에 대해 Azure AD Domain Services를 사용하도록 설정해야 합니다. Azure AD 테넌트의 관리자가 아니라면 관리자에게 문의하고 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/tutorial-create-instance.md)를 위한 단계별 지침을 따르세요.

    Azure AD DS 배포를 완료 하는 데 일반적으로 약 15 분이 걸립니다. 다음 단계로 진행 하기 전에 암호 해시 동기화를 사용 하 여 Azure AD DS의 상태가 **실행 중**으로 표시 되는지 확인 합니다.

3.  **Azure AD DS을 사용 하 여 Azure VM에 도메인 가입 합니다.**

    VM에서 Azure AD 자격 증명을 사용 하 여 파일 공유에 액세스 하려면 VM이 Azure AD DS에 도메인에 가입 되어 있어야 합니다. VM을 도메인 조인하는 방법에 대한 자세한 내용은 [Windows Server 가상 머신을 관리되는 도메인에 조인](../../active-directory-domain-services/join-windows-vm.md)을 참조하세요.

    > [!NOTE]
    > Azure Files를 사용 하는 SMB를 통한 azure AD DS 인증은 Windows 7 또는 Windows Server 2008 R2 위의 OS 버전에서 실행 되는 Azure Vm 에서만 지원 됩니다.

4.  **Azure 파일 공유를 선택하거나 만듭니다.**

    Azure AD 테넌트와 같은 구독에 연결된 신규 또는 기존 파일 공유를 선택합니다. 새 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure Files에 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요.
    최적의 성능을 위해서는 공유에 액세스 하려는 VM과 동일한 지역에 파일 공유를 사용 하는 것이 좋습니다.

5.  **스토리지 계정 키를 사용하여 Azure 파일 공유를 탑재해 Azure Files 연결을 확인합니다.**

    VM 및 파일 공유가 적절하게 구성되었는지 확인하려면 스토리지 계정 키를 사용하여 파일 공유를 탑재해 봅니다. 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>계정에 대해 Azure AD DS 인증 사용

Azure Files에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하려면 Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여 저장소 계정에 대 한 속성을 설정할 수 있습니다. 이 속성을 설정 하면 연결 된 Azure AD DS 배포와 함께 저장소 계정이 암시적으로 "도메인 조인" 됩니다. 그러면 저장소 계정의 모든 신규 및 기존 파일 공유에 대해 SMB를 통한 Azure AD DS 인증을 사용할 수 있습니다.

Azure AD DS를 Azure AD 테 넌 트에 성공적으로 배포한 후에만 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조 하세요.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)에서 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 기존 저장소 계정으로 이동 하거나 [저장소 계정을 만듭니다](../common/storage-account-create.md).
2. **설정** 섹션에서 **구성**을 선택합니다.
3. **Azure 파일 인증을 위한 id 기반 디렉터리 서비스** 드롭다운 목록에서 **Azure Active Directory Domain Services (azure AD DS)** 를 선택 합니다.

다음 이미지는 저장소 계정에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하는 방법을 보여 줍니다.

![Azure Portal에서 SMB를 통한 Azure AD 인증 사용](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Azure PowerShell에서 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하려면 최신 Az module (2.4 이상) 또는 Az. Storage 모듈 (1.5 이상)을 설치 합니다. PowerShell을 설치 하는 방법에 대 한 자세한 내용은 [PowerShellGet을 사용 하 여 Windows에 Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/install-Az-ps)를 참조 하세요.

새 저장소 계정을 만들려면 [AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)를 호출 하 고 **EnableAzureActiveDirectoryDomainServicesForFile** 매개 변수를 **true**로 설정 합니다. 다음 예제에서는 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 이전 미리 보기 모듈을 사용 하는 경우 기능 사용에 대 한 매개 변수는 **EnableAzureFilesAadIntegrationForSMB**입니다.

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

기존 저장소 계정에서이 기능을 사용 하도록 설정 하려면 다음 명령을 사용 합니다.

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 SMB를 통해 Azure AD 인증을 사용 하도록 설정 하려면 최신 CLI 버전 (버전 2.0.70 이상)을 설치 합니다. Azure CLI를 설치 하는 방법에 대 한 자세한 내용은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조 하세요.

새 저장소 계정을 만들려면[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)를 호출 하 고 `--enable-files-aadds` 속성을 **true**로 설정 합니다. 다음 예제에서는 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 이전 미리 보기 모듈을 사용 하는 경우 기능 사용에 대 한 매개 변수는 **file-aad**입니다.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

기존 저장소 계정에서이 기능을 사용 하도록 설정 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>ID에 액세스 권한 할당

Azure AD 자격 증명을 사용 하 여 리소스 Azure Files에 액세스 하려면 id (사용자, 그룹 또는 서비스 사용자)에 게 공유 수준에서 필요한 권한이 있어야 합니다. 이 프로세스는 특정 사용자가 파일 공유에 대해 갖는 액세스 유형을 지정 하는 Windows 공유 사용 권한을 지정 하는 것과 비슷합니다. 이 섹션의 지침에서는 파일 공유에 대한 읽기, 쓰기 또는 삭제 권한을 ID에 할당하는 방법을 보여줍니다.

사용자에 게 공유 수준 권한을 부여 하기 위한 두 개의 Azure 기본 제공 역할이 도입 되었습니다.

- **저장소 파일 데이터 Smb 공유 판독기** 는 smb를 통한 Azure Storage 파일 공유에서 읽기 권한을 허용 합니다.
- **저장소 파일 데이터 Smb 공유 참가자** 는 smb를 통해 Azure Storage 파일 공유에 대 한 읽기, 쓰기 및 삭제 액세스를 허용 합니다.
- **저장소 파일 데이터 Smb 공유 승격 된 참가자** 는 SMB를 통한 Azure Storage 파일 공유에서 NTFS 읽기, 쓰기, 삭제 및 수정 권한을 허용 합니다.

> [!IMPORTANT]
> ID에 역할을 할당하는 기능을 비롯하여 파일 공유에 대한 모든 관리 권한을 할당하려면 스토리지 계정 키를 사용해야 합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다.

Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 공유 수준 권한을 부여 하는 사용자의 Azure AD id에 기본 제공 역할을 할당할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal
[Azure Portal](https://portal.azure.com)를 사용 하 여 Azure AD ID에 RBAC 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 파일 공유로 이동 하거나 [Azure Files에서 파일 공유를 만듭니다](storage-how-to-create-file-share.md).
2. **Access Control(IAM)** 을 선택합니다.
3. **역할 할당 추가** 선택
4. **역할 할당 추가** 블레이드의 **역할** 목록에서 적절 한 기본 제공 역할 (저장소 파일 데이터 Smb 공유 판독기, 저장소 파일 데이터 smb 공유 참가자)을 선택 합니다. 기본 설정인 **AZURE AD 사용자, 그룹 또는 서비스 주체**에 대 **한 액세스 권한 부여 옵션을** 유지 합니다. 이름 또는 전자 메일 주소를 기준으로 대상 Azure AD id를 선택 합니다.
5. **저장** 을 선택 하 여 역할 할당 작업을 완료 합니다.

#### <a name="powershell"></a>PowerShell

다음 PowerShell 명령은 로그인 이름을 기반으로 하 여 Azure AD id에 RBAC 역할을 할당 하는 방법을 보여 줍니다. PowerShell을 사용하여 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure PowerShell을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-powershell.md)를 참조하세요.

다음 샘플 스크립트를 실행 하기 전에 대괄호를 포함 하 여 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI

다음 CLI 2.0 명령은 로그인 이름을 기반으로 하 여 Azure AD id에 RBAC 역할을 할당 하는 방법을 보여 줍니다. Azure CLI에서 RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 [rbac 및 Azure CLI를 사용 하 여 액세스 관리](../../role-based-access-control/role-assignments-cli.md)를 참조 하세요.

다음 샘플 스크립트를 실행 하기 전에 대괄호를 포함 하 여 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>SMB를 통한 NTFS 권한 구성
RBAC를 사용하여 공유 수준 권한할당을 한 후에는 루트, 디렉터리 또는 파일 수준에서 적절한 NTFS 권한을 할당해야 합니다. 공유 수준 사용 권한은 사용자가 공유에 액세스할 수 있는지 여부를 결정 하는 상위 수준 게이트 키퍼로 생각 하면 됩니다. NTFS 권한은 사용자가 디렉터리 또는 파일 수준에서 수행할 수 있는 작업을 결정 하기 위해 보다 세분화 된 수준으로 작동 합니다.

Azure Files는 NTFS 기본 및 고급 권한의 전체 집합을 지원합니다. 공유를 탑재 한 다음 Windows 파일 탐색기를 사용 하거나 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 또는 [Set ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 명령을 실행 하 여 Azure 파일 공유의 디렉터리 및 파일에 대 한 NTFS 권한을 확인 하 고 구성할 수 있습니다.

수퍼유저 권한으로 NTFS를 구성 하려면 도메인에 가입 된 VM의 저장소 계정 키를 사용 하 여 공유를 탑재 해야 합니다. 다음 섹션의 지침에 따라 명령 프롬프트에서 Azure 파일 공유를 탑재 하 고 NTFS 사용 권한을 적절 하 게 구성 합니다.

파일 공유의 루트 디렉터리에서 지원되는 권한 집합은 다음과 같습니다.

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>명령 프롬프트에서 파일 공유 탑재

Windows **net use** 명령을 사용하여 Azure 파일 공유를 탑재합니다. 다음 예제의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 파일 공유 탑재에 대한 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Windows 파일 탐색기를 사용 하 여 NTFS 사용 권한 구성
Windows 파일 탐색기를 사용 하 여 루트 디렉터리를 포함 하 여 파일 공유 아래의 모든 디렉터리 및 파일에 대 한 모든 권한을 부여 합니다.

1. Windows 파일 탐색기를 열고 파일/디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **속성** 을 선택 합니다.
2. **보안** 탭을 클릭 합니다.
3. **편집 ...** 을 클릭 합니다. 사용 권한을 변경 하는 단추
4. 기존 사용자의 사용 권한을 변경 하거나 **추가** ...를 클릭 하 여 새 사용자에 게 사용 권한을 부여할 수 있습니다.
5. 새 사용자 추가에 대 한 프롬프트 창에서 **선택할 개체 이름을 입력 하십시오** . 상자에 권한을 부여 하려는 대상 사용자 이름을 입력 하 고 **이름 확인** 을 클릭 하 여 대상 사용자의 전체 UPN 이름을 찾습니다.
7.  설정 메뉴에서 **확인**
8.  보안 탭에서 새로 추가 하는 사용자에 게 부여 하려는 모든 사용 권한을 선택 합니다.
9.  **적용**을 클릭합니다.

### <a name="configure-ntfs-permissions-with-icacls"></a>icacls를 사용하여 NTFS 권한 구성
루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 다음 Windows 명령을 사용합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Icacls를 사용 하 여 NTFS 권한 및 지원 되는 다양 한 권한 유형을 설정 하는 방법에 대 한 자세한 내용은 [icacls 명령줄 참조](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)를 참조 하세요.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>도메인 조인 VM에서 파일 공유 탑재

다음 프로세스는 Azure AD 자격 증명이 올바르게 설정 되었는지 확인 하 고 도메인에 가입 된 VM에서 Azure 파일 공유에 액세스할 수 있는지 확인 합니다.

다음 그림에 표시 된 것 처럼 권한을 부여한 Azure AD id를 사용 하 여 VM에 로그인 합니다.

![사용자 인증용 Azure AD 로그인 화면이 표시된 스크린샷](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

다음 명령을 사용 하 여 Azure 파일 공유를 탑재 합니다. 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 이미 인증 되었으므로 저장소 계정 키 또는 Azure AD 사용자 이름 및 암호를 제공할 필요가 없습니다. SMB를 통한 azure AD는 Azure AD 자격 증명을 사용 하는 Single Sign-On 환경을 지원 합니다.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

이제 SMB를 통해 Azure AD 인증을 사용 하도록 설정 하 고 azure AD id를 사용 하 여 Azure 파일 공유에 대 한 액세스를 제공 하는 사용자 지정 역할을 할당 했습니다. 추가 사용자에 게 파일 공유에 대 한 액세스 권한을 부여 하려면 [id에 대 한 액세스 권한 할당](#assign-access-permissions-to-an-identity) 및 [SMB를 통한 NTFS 사용 권한 구성](#configure-ntfs-permissions-over-smb) 섹션의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

Azure Files 및 SMB를 통해 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

- [Azure Files 소개](storage-files-introduction.md)
- [Azure Files용 SMB를 통한 Azure Active Directory 인증 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
