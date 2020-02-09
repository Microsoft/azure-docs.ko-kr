---
title: Azure Active Directory Domain Services 문제 해결 | Microsoft Docs '
description: Azure Active Directory Domain Services를 만들거나 관리할 때 일반적인 오류를 해결 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 2c6f594b16aac40abf885e0d058c7aba48d32f9c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512626"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 대한 일반적인 오류 및 문제 해결 단계

응용 프로그램에 대한 id 및 인증의 핵심 요소로 Azure Active Directory Domain Services (Azure AD DS)에 문제가 있을 수도 있습니다. 문제가 발생 하는 경우 몇 가지 일반적인 오류 메시지 및 관련 문제 해결 단계를 수행 하 여 작업을 다시 실행 하는 데 도움이 됩니다. 언제 든 지 추가 문제 해결 지원을 위해 [Azure 지원 요청을 열][azure-support] 수도 있습니다.

이 문서에서는 Azure AD DS의 일반적인 문제에 대한 문제 해결 단계를 제공 합니다.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Azure AD 디렉터리에 대해 Azure AD Domain Services를 사용할 수 없습니다.

Azure AD DS을 사용 하도록 설정 하는 데 문제가 있는 경우 다음과 같은 일반적인 오류 및 해결 방법을 검토 합니다.

| **샘플 오류 메시지** | **해결 방법** |
| --- |:--- |
| *Contoso.com 이름이이 네트워크에서 이미 사용 중입니다. 사용 하지 않는 이름을 지정 하십시오.* |[가상 네트워크에서 도메인 이름 충돌](troubleshoot.md#domain-name-conflict) |
| *이 Azure AD 테넌트에서 도메인 서비스를 사용 하도록 설정할 수 없습니다. 서비스에 ' Azure AD Domain Services Sync ' 응용 프로그램에 대한 적절 한 권한이 없습니다. ' Azure AD Domain Services Sync ' 라는 응용 프로그램을 삭제 한 다음 Azure AD 테넌트에 대해 도메인 서비스를 사용 하도록 설정 해 보세요.* |[도메인 서비스에 Azure AD Domain Services 동기화 응용 프로그램에 대한 적절 한 권한이 없습니다.](troubleshoot.md#inadequate-permissions) |
| *이 Azure AD 테넌트에서 도메인 서비스를 사용 하도록 설정할 수 없습니다. Azure AD 테넌트의 도메인 서비스 응용 프로그램에 도메인 서비스를 사용 하도록 설정 하는 데 필요한 권한이 없습니다. 응용 프로그램 식별자 d87dcbc6-a371-462e-88e3-28ad15ec4e64를 사용 하 여 응용 프로그램을 삭제 한 다음 Azure AD 테넌트에 대해 도메인 서비스를 사용 하도록 설정 합니다.* |[Azure AD 테넌트에서 도메인 서비스 응용 프로그램이 제대로 구성 되지 않았습니다.](troubleshoot.md#invalid-configuration) |
| *이 Azure AD 테넌트에서 도메인 서비스를 사용 하도록 설정할 수 없습니다. Azure AD 테넌트에서 Microsoft Azure AD 응용 프로그램을 사용할 수 없습니다. 응용 프로그램 식별자 00000002-0000-0000-c000-000000000000를 사용 하 여 응용 프로그램을 사용 하도록 설정한 다음 Azure AD 테넌트에 대해 도메인 서비스를 사용 하도록 설정 합니다.* |[Azure AD 테넌트에서 Microsoft Graph 애플리케이션을 사용할 수 없음](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>도메인 이름 충돌

**오류 메시지**

*Contoso.com 이름이이 네트워크에서 이미 사용 중입니다. 사용 하지 않는 이름을 지정 하십시오.*

**해결 방법**

동일 하거나 피어 링 가상 네트워크에 동일한 도메인 이름을 사용 하는 기존 AD DS 환경이 없는지 확인 합니다. 예를 들어 Azure Vm에서 실행 되는 *contoso.com* 라는 AD DS 도메인이 있을 수 있습니다. 가상 네트워크에서 동일한 도메인 이름 *contoso.com* 를 사용 하 여 Azure AD DS 관리 되는 도메인을 사용 하도록 설정 하려고 하면 요청 된 작업이 실패 합니다.

이 오류는 가상 네트워크의 도메인 이름에 대한 이름 충돌 때문에 발생 합니다. DNS 조회는 기존 AD DS 환경이 요청 된 도메인 이름에 응답 하는지 확인 합니다. 이 오류를 해결 하려면 다른 이름을 사용 하 여 Azure AD DS 관리 되는 도메인을 설정 하거나 기존 AD DS 도메인의 프로 비전을 해제 한 후 다시 시도 하 여 Azure AD DS을 사용 하도록 설정 합니다.

### <a name="inadequate-permissions"></a>부적절한 권한

**오류 메시지**

*이 Azure AD 테넌트에서 도메인 서비스를 사용 하도록 설정할 수 없습니다. 서비스에 ' Azure AD Domain Services Sync ' 응용 프로그램에 대한 적절 한 권한이 없습니다. ' Azure AD Domain Services Sync ' 라는 응용 프로그램을 삭제 한 다음 Azure AD 테넌트에 대해 도메인 서비스를 사용 하도록 설정 해 보세요.*

**해결 방법**

Azure AD 디렉터리에 *Azure AD Domain Services Sync* 라는 응용 프로그램이 있는지 확인 합니다. 이 응용 프로그램이 있는 경우 해당 응용 프로그램을 삭제 한 다음 다시 시도 하 여 Azure AD DS를 사용 하도록 설정 합니다. 기존 응용 프로그램을 확인 하 고 필요한 경우 삭제 하려면 다음 단계를 완료 합니다.

1. Azure Portal의 왼쪽 탐색 메뉴에서 **Azure Active Directory** 를 선택 합니다.
1. **Enterprise 애플리케이션**을 선택합니다. **응용 프로그램 유형** 드롭다운 메뉴에서 *모든 응용 프로그램* 을 선택 하 고 **적용**을 선택 합니다.
1. 검색 상자에 *Azure AD Domain Services Sync*를 입력 합니다. 응용 프로그램이 있는 경우 해당 응용 프로그램을 선택 하 고 **삭제**를 선택 합니다.
1. 응용 프로그램을 삭제 한 후에는 Azure AD DS를 다시 사용 하도록 설정 해 보세요.

### <a name="invalid-configuration"></a>유효하지 않은 구성

**오류 메시지**

*이 Azure AD 테넌트에서 도메인 서비스를 사용 하도록 설정할 수 없습니다. Azure AD 테넌트의 도메인 서비스 응용 프로그램에 도메인 서비스를 사용 하도록 설정 하는 데 필요한 권한이 없습니다. 응용 프로그램 식별자 d87dcbc6-a371-462e-88e3-28ad15ec4e64를 사용 하 여 응용 프로그램을 삭제 한 다음 Azure AD 테넌트에 대해 도메인 서비스를 사용 하도록 설정 합니다.*

**해결 방법**

Azure AD 디렉터리에서 응용 프로그램 식별자가 *d87dcbc6-a371-462e-88e3-28ad15ec4e64* 인 *AzureActiveDirectoryDomainControllerServices* 이라는 기존 응용 프로그램이 있는지 확인 합니다. 이 응용 프로그램이 있는 경우 해당 응용 프로그램을 삭제 한 다음 다시 시도 하 여 Azure AD DS를 사용 하도록 설정 합니다.

다음 PowerShell 스크립트를 사용 하 여 기존 응용 프로그램 인스턴스를 검색 하 고 필요한 경우 삭제 합니다.

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph 사용 안 함

**오류 메시지**

*이 Azure AD 테넌트에서 도메인 서비스를 사용 하도록 설정할 수 없습니다. Azure AD 테넌트에서 Microsoft Azure AD 응용 프로그램을 사용할 수 없습니다. 응용 프로그램 식별자 00000002-0000-0000-c000-000000000000를 사용 하 여 응용 프로그램을 사용 하도록 설정한 다음 Azure AD 테넌트에 대해 도메인 서비스를 사용 하도록 설정 합니다.*

**해결 방법**

*00000002-0000-0000-c000-000000000000*식별자를 사용 하 여 응용 프로그램을 사용 하지 않도록 설정 했는지 확인 합니다. 이 애플리케이션은 Microsoft Azure AD 애플리케이션이며 Azure AD 테넌트에 Graph API 액세스를 제공합니다. Azure AD 테넌트를 동기화 하려면이 응용 프로그램을 사용 하도록 설정 해야 합니다.

이 응용 프로그램의 상태를 확인 하 고 필요한 경우 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. Azure Portal의 왼쪽 탐색 메뉴에서 **Azure Active Directory** 를 선택 합니다.
1. **Enterprise 애플리케이션**을 선택합니다. **응용 프로그램 유형** 드롭다운 메뉴에서 *모든 응용 프로그램* 을 선택 하 고 **적용**을 선택 합니다.
1. 검색 상자에 *00000002-0000-0000-c000-00000000000*를 입력 합니다. 응용 프로그램을 선택한 다음 **속성**을 선택 합니다.
1. **사용자가 로그인 할 수 있도록** 설정 됨이 *아니요*로 설정 되어 있으면 값을 *예*로 설정 하 고 **저장**을 선택 합니다.
1. 응용 프로그램을 사용 하도록 설정한 후에는 Azure AD DS를 다시 사용 하도록 설정 해 봅니다.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>사용자는 Azure AD 도메인 서비스 관리된 도메인에 로그인할 수 없습니다.

Azure AD 테넌트에서 하나 이상의 사용자가 Azure AD DS 관리 되는 도메인에 로그인 할 수 없는 경우 다음 문제 해결 단계를 완료 합니다.

* **자격 증명 형식** -UPN 형식을 사용 하 여 `dee@contoso.onmicrosoft.com`와 같은 자격 증명을 지정 합니다. UPN 형식은 Azure AD DS에서 자격 증명을 지정 하는 데 권장 되는 방법입니다. 이 UPN이 Azure AD에서 올바르게 구성 되어 있는지 확인 합니다.

    사용자의 계정에 대한 *SAMAccountName* (예: *CONTOSO\driley* )은 테넌트에서 동일한 upn 접두사를 사용 하는 여러 사용자가 있거나 upn 접두사가 너무 긴 경우 자동으로 생성 될 수 있습니다. 따라서 계정에 대한 *SAMAccountName* 형식은 사용자의 온-프레미스 도메인에서 사용 하거나 사용 하는 것과 다를 수 있습니다.

* **암호 동기화** - [Azure AD Connect를 사용 하 여][hybrid-phs] [클라우드 전용 사용자][cloud-only-passwords] 또는 하이브리드 환경에 대해 암호 동기화를 사용 하도록 설정 했는지 확인 합니다.
    * **하이브리드 동기화 된 계정:** 영향을 받는 사용자 계정이 온-프레미스 디렉터리에서 동기화 되는 경우 다음 영역을 확인 합니다.
    
      * [Azure AD Connect의 최신 권장 릴리스](https://www.microsoft.com/download/details.aspx?id=47594)를 배포 하거나 업데이트 했습니다.
      * [전체 동기화를 수행][hybrid-phs]하도록 Azure AD Connect를 구성 했습니다.
      * 디렉터리 크기에 따라 Azure AD DS에서 사용자 계정 및 자격 증명 해시를 사용 하는 데 다소 시간이 걸릴 수 있습니다. 관리 되는 도메인에 대해 인증을 시도 하기 전에 충분 한 시간 동안 기다려야 합니다.
      * 이전 단계를 확인 한 후에도 문제가 지속 되 면 *Microsoft Azure AD 동기화 서비스*를 다시 시작 하십시오. [관리 VM][management-vm]에서 명령 프롬프트를 열고 다음 명령을 실행 합니다.
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **클라우드 전용 계정**: 영향을 받는 사용자 계정이 클라우드 전용 사용자 계정인 경우 [Azure AD DS를 사용 하도록 설정한 후 사용자가 암호를 변경][cloud-only-passwords]했는지 확인 합니다. 이 암호 재설정을 수행 하면 Azure AD Domain Services에 필요한 자격 증명 해시가 생성 됩니다.

* **사용자 계정이 활성 상태 인지 확인**: 기본적으로 관리 되는 도메인에서 2 분 이내에 잘못 된 암호를 5 번 시도 하면 30 분 동안 사용자 계정이 잠깁니다. 계정이 잠겨 있는 동안에는 사용자가 로그인 할 수 없습니다. 30 분 후에 사용자 계정이 자동으로 잠금 해제 됩니다.
  * Azure AD DS 관리 되는 도메인에 대한 잘못 된 암호 시도는 Azure AD에서 사용자 계정을 잠그지 않습니다. 사용자 계정은 관리 되는 도메인 내 에서만 잠깁니다. Azure AD가 아니라 [관리 VM][management-vm]을 사용 하 여 *adac (Active Directory 관리 콘솔)* 에서 사용자 계정 상태를 확인 합니다.
  * [세분화 된 암호 정책을 구성][password-policy] 하 여 기본 잠금 임계값 및 기간을 변경할 수도 있습니다.

* **외부 계정** -영향을 받는 사용자 계정이 Azure AD 테넌트에서 외부 계정이 아님을 확인 합니다. 외부 계정의 예로는 `dee@live.com` 또는 외부 Azure AD 디렉터리의 사용자 계정과 같은 Microsoft 계정이 있습니다. Azure AD DS는 관리 되는 도메인에 로그인 할 수 없도록 외부 사용자 계정에 대한 자격 증명을 저장 하지 않습니다.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>관리되는 도메인에서 하나 이상의 경고 발생

Azure AD DS 관리 되는 도메인에 활성 경고가 있으면 인증 프로세스가 올바르게 작동 하지 않을 수 있습니다.

활성 경고가 있는지 확인 하려면 [Azure AD DS 관리 되는 도메인의 상태를 확인][check-health]합니다. 경고가 표시 되 면 문제를 해결 하 [고 해결][troubleshoot-alerts]합니다.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Azure AD 테넌트에서는 제거되지만 관리되는 도메인에서는 제거되지 않는 사용자

Azure AD는 실수로 사용자 개체를 삭제 하지 않도록 보호 합니다. Azure AD 테넌트에서 사용자 계정을 삭제 하면 해당 사용자 개체가 휴지통으로 이동 됩니다. 이 삭제 작업이 Azure AD DS 관리 되는 도메인과 동기화 되 면 해당 사용자 계정이 사용 안 함으로 표시 됩니다. 이 기능은 사용자 계정을 복구 하거나 삭제 취소 하는 데 도움이 됩니다.

Azure AD 디렉터리에서 동일한 UPN을 사용 하 여 사용자 계정을 다시 만들 경우에도 사용자 계정은 Azure AD DS 관리 되는 도메인에서 사용 안 함 상태로 유지 됩니다. Azure AD DS 관리 되는 도메인에서 사용자 계정을 제거 하려면 Azure AD 테넌트에서 강제로 삭제 해야 합니다.

Azure AD DS 관리 되는 도메인에서 사용자 계정을 완전히 제거 하려면 `-RemoveFromRecycleBin` 매개 변수와 함께 [Set-msoluser][Remove-MsolUser] PowerShell cmdlet을 사용 하 여 azure AD 테넌트에서 사용자를 영구적으로 삭제 합니다.

## <a name="next-steps"></a>다음 단계

문제가 계속 되 면 [Azure 지원 요청을 열어][azure-support] 추가 문제 해결 지원을 요청 하세요.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
