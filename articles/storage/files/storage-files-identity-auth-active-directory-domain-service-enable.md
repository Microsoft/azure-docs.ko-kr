---
title: Azure AD Domain Services를 사용 하 여 SMB를 통해 파일 데이터에 대 한 액세스 권한 부여
description: Azure Active Directory Domain Services를 통해 Azure Files에 대해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 사용 하도록 설정 하는 방법에 대해 알아봅니다. 그런 다음 도메인에 가입 된 Windows Vm (가상 머신)은 Azure AD 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d4aad485741abc1c0f54e14e00cd8654a8addbca
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930804"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Files에서 Azure Active Directory Domain Services 인증 사용

[Azure Files](storage-files-introduction.md)   는 온-프레미스 Active Directory Domain Services (AD DS) 및 Azure Active Directory Domain Services (Azure AD DS)의 두 가지 유형의 도메인 서비스를 통해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 지원 합니다. 인증을 위해 올바른 도메인 서비스를 선택 하는 [것은 작동 방법 섹션](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works) 을 검토 하는 것이 좋습니다. 설정은 선택한 도메인 서비스에 따라 다릅니다. 이 문서에서는 azure 파일 공유에서 인증을 위해 Azure AD DS를 사용 하도록 설정 하 고 구성 하는 방법을 중점적으로 다룹니다

Azure 파일 공유를 처음 접하는 경우 다음 일련의 문서를 읽기 전에 [계획 가이드](storage-files-planning.md) 를 읽어 보는 것이 좋습니다.

> [!NOTE]
> Azure Files는 RC4-HMAC 암호화를 사용 하 여 Azure AD DS에서 Kerberos 인증을 지원 합니다. AES Kerberos 암호화는 아직 지원 되지 않습니다.
> Azure Files azure AD와 전체 동기화를 사용 하 여 Azure AD DS 인증을 지원 합니다. Azure AD에서 제한 된 id 집합만 동기화 하는 Azure AD DS에서 범위 동기화를 사용 하도록 설정한 경우 인증 및 권한 부여는 지원 되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 파일 공유에 대해 SMB를 통해 Azure AD를 사용 하도록 설정 하기 전에 다음 필수 구성 요소를 완료 했는지 확인 합니다.

1.  **Azure AD 테넌트를 선택하거나 만듭니다.**

    SMB를 통한 Azure AD 인증에는 새 테넌트나 기존 테넌트를 사용할 수 있습니다. 액세스하려는 파일 공유와 테넌트는 같은 구독과 연결되어 있어야 합니다.

    새 Azure AD 테넌트를 만들려는 경우 [Azure AD 테넌트 및 Azure AD 구독을 추가](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)하면 됩니다. 기존 Azure AD 테 넌 트가 있지만 Azure 파일 공유에 사용할 새 테 넌 트를 만들려는 경우 [Azure Active Directory 테 넌 트 만들기](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)를 참조 하세요.

1.  **Azure AD 테넌트에서 Azure AD Domain Services를 사용하도록 설정합니다.**

    Azure AD 자격 증명을 사용한 인증을 지원하려면 Azure AD 테넌트에 대해 Azure AD Domain Services를 사용하도록 설정해야 합니다. Azure AD 테넌트의 관리자가 아니라면 관리자에게 문의하고 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/tutorial-create-instance.md)를 위한 단계별 지침을 따르세요.

    Azure AD DS 배포를 완료 하는 데 일반적으로 약 15 분이 걸립니다. 다음 단계로 진행 하기 전에 암호 해시 동기화를 사용 하 여 Azure AD DS의 상태가 **실행 중**으로 표시 되는지 확인 합니다.

1.  **Azure AD DS을 사용 하 여 Azure VM에 도메인 가입 합니다.**

    VM에서 Azure AD 자격 증명을 사용 하 여 파일 공유에 액세스 하려면 VM이 Azure AD DS에 도메인에 가입 되어 있어야 합니다. VM을 도메인 조인하는 방법에 대한 자세한 내용은 [Windows Server 가상 머신을 관리되는 도메인에 조인](../../active-directory-domain-services/join-windows-vm.md)을 참조하세요.

    > [!NOTE]
    > Azure 파일 공유를 사용 하 여 SMB를 통한 azure AD DS 인증은 Windows 7 또는 Windows Server 2008 R2 위의 OS 버전에서 실행 되는 Azure Vm 에서만 지원 됩니다.

1.  **Azure 파일 공유를 선택하거나 만듭니다.**

    Azure AD 테넌트와 같은 구독에 연결된 신규 또는 기존 파일 공유를 선택합니다. 새 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure Files에 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요.
    최적의 성능을 위해서는 공유에 액세스 하려는 VM과 동일한 지역에 파일 공유를 사용 하는 것이 좋습니다.

1.  **스토리지 계정 키를 사용하여 Azure 파일 공유를 탑재해 Azure Files 연결을 확인합니다.**

    VM 및 파일 공유가 적절하게 구성되었는지 확인하려면 스토리지 계정 키를 사용하여 파일 공유를 탑재해 봅니다. 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

## <a name="regional-availability"></a>국가별 가용성

[모든 Azure 공용 지역](https://azure.microsoft.com/global-infrastructure/locations/)에서 azure AD DS를 사용 하 여 Azure Files 인증을 사용할 수 있습니다.

## <a name="overview-of-the-workflow"></a>워크플로의 개요

Azure 파일 공유에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하기 전에 Azure AD 및 Azure Storage 환경이 제대로 구성 되어 있는지 확인 합니다. 필요한 모든 단계를 완료 했는지 확인 하기 위해 [필수 구성 요소](#prerequisites) 를 안내 하는 것이 좋습니다.

다음으로, Azure AD 자격 증명을 사용 하 여 Azure Files 리소스에 대 한 액세스 권한을 부여 하려면 다음 작업을 수행 합니다.

1. 저장소 계정에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하 여 연결 된 Azure AD DS 배포에 저장소 계정을 등록 합니다.
2. Azure AD ID(사용자, 그룹 또는 서비스 주체)에 공유 액세스 권한을 할당합니다.
3. 디렉터리와 파일에 대해 SMB를 통한 NTFS 권한을 구성합니다.
4. 도메인 조인 VM에서 Azure 파일 공유를 탑재합니다.

다음 다이어그램에서는 Azure Files에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하는 종단 간 워크플로를 보여 줍니다.

![Azure Files용 SMB를 통한 Azure AD 워크플로를 보여 주는 다이어그램](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>계정에 대해 Azure AD DS 인증 사용

Azure Files에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하려면 Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여 저장소 계정에 대 한 속성을 설정할 수 있습니다. 이 속성을 설정 하면 연결 된 Azure AD DS 배포와 함께 저장소 계정이 암시적으로 "도메인 조인" 됩니다. 그러면 저장소 계정의 모든 신규 및 기존 파일 공유에 대해 SMB를 통한 Azure AD DS 인증을 사용할 수 있습니다.

Azure AD DS를 Azure AD 테 넌 트에 성공적으로 배포한 후에만 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조 하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com)에서 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 기존 저장소 계정으로 이동 하거나 [저장소 계정을 만듭니다](../common/storage-account-create.md).
1. **설정** 섹션에서 **구성**을 선택합니다.
1. **파일 공유에 대 한 id 기반 액세스** 에서 **AAD (Azure Active Directory 도메인 서비스)** 를 **사용**으로 전환 합니다.
1. **저장**을 선택합니다.

다음 이미지는 저장소 계정에 대해 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하는 방법을 보여 줍니다.

![Azure Portal에서 SMB를 통해 Azure AD DS 인증 사용](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell에서 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하려면 최신 Az module (2.4 이상) 또는 Az. Storage 모듈 (1.5 이상)을 설치 합니다. PowerShell을 설치 하는 방법에 대 한 자세한 내용은 [PowerShellGet을 사용 하 여 Windows에 Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/install-Az-ps)를 참조 하세요.

새 저장소 계정을 만들려면 [AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)를 호출 하 고 **EnableAzureActiveDirectoryDomainServicesForFile** 매개 변수를 **true**로 설정 합니다. 다음 예제에서는 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 이전 미리 보기 모듈을 사용 하는 경우 기능을 사용 하도록 설정 하는 매개 변수는 **EnableAzureFilesAadIntegrationForSMB**입니다.

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


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 SMB를 통해 Azure AD 인증을 사용 하도록 설정 하려면 최신 CLI 버전 (버전 2.0.70 이상)을 설치 합니다. Azure CLI를 설치 하는 방법에 대 한 자세한 내용은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조 하세요.

새 저장소 계정을 만들려면 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)를 호출 하 고 `--enable-files-aadds` 속성을 **true**로 설정 합니다. 다음 예제에서는 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 이전 미리 보기 모듈을 사용 하는 경우 기능 사용에 대 한 매개 변수는 **file-aad**입니다.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

기존 저장소 계정에서이 기능을 사용 하도록 설정 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

이제 SMB를 통해 Azure AD DS 인증을 사용 하도록 설정 하 고 azure AD id를 사용 하 여 Azure 파일 공유에 대 한 액세스를 제공 하는 사용자 지정 역할을 할당 했습니다. 추가 사용자에 게 파일 공유에 대 한 액세스 권한을 부여 하려면 [액세스 권한 할당](#assign-access-permissions-to-an-identity) 을 사용 하 여 id를 사용 하 고 [SMB를 통한 NTFS 사용 권한 구성 섹션](#configure-ntfs-permissions-over-smb)의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

Azure Files 및 SMB를 통해 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

- [SMB 액세스에 대한 Azure Files ID 기반 인증 지원 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
