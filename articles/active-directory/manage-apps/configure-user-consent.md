---
title: 최종 사용자가 Azure AD를 사용 하 여 응용 프로그램에 동의 하는 방법 구성
description: 사용자가 조직의 데이터에 액세스할 수 있는 응용 프로그램에 동의 하는 방법 및 시기를 관리 하는 방법에 대해 알아봅니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443400"
---
# <a name="configure-how-end-users-consent-to-applications"></a>최종 사용자가 응용 프로그램에 동의 하는 방식 구성

응용 프로그램은 Microsoft Id 플랫폼과 통합 하 여 사용자가 Azure Active Directory (Azure AD)에서 회사 또는 학교 계정을 사용 하 여 로그인 하 고 조직의 데이터에 액세스 하 여 풍부한 데이터 기반 환경을 제공할 수 있도록 합니다. 다른 사용 권한을 사용 하면 응용 프로그램에서 사용자와 조직의 데이터에 서로 다른 수준의 액세스를 사용할 수 있습니다.

기본적으로 사용자는 일부 사용 권한에 대해서만 조직의 데이터에 액세스 하는 응용 프로그램에 동의할 수 있습니다. 예를 들어, 기본적으로 사용자는 사용자가 소유 하는 팀에 대 한 사용자의 사서함 또는 팀 대화에 대 한 액세스를 허용 하는 것에 동의할 수 있지만, 조직의 모든 SharePoint 사이트에 대 한 읽기 및 쓰기에 대 한 앱 무인 액세스를 허용 하는 것에 동의할 수 없습니다. 사용자가 자신에 게 동의할 수 있도록 허용 하는 경우에는 사용자가 Microsoft 365, Azure 및 기타 서비스와 통합 하는 유용한 응용 프로그램을 쉽게 획득할 수 있으며, 사용 하지 않거나 모니터링 하는 경우 위험을 나타낼 수 있습니다.

사용자의 노출 영역을 줄이고이 위험을 완화 하기 위해 향후 사용자 동의 작업을 사용 하지 않도록 설정 하는 것이 좋습니다. 사용자 동의가 사용 하지 않도록 설정 된 경우 이전 동의 부여는 여전히 적용 되지만 관리자는 이후의 모든 승인 작업을 수행 해야 합니다. 사용자가 통합 [관리자 동의 요청 워크플로](configure-admin-consent-workflow.md) 를 통하거나 사용자의 지원 프로세스를 통해 테넌트 전체 관리자 동의를 요청할 수 있습니다. 자세한 내용은 [id 인프라를 보호 하는 5 단계를](../../security/fundamentals/steps-secure-identity.md) 참조 하세요.

## <a name="configure-user-consent-to-applications"></a>응용 프로그램에 대 한 사용자 동의 구성
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Azure Portal에서 사용자 동의 사용 또는 사용 안 함

Azure Portal를 사용 하 여 조직의 데이터에 액세스 하는 응용 프로그램에 동의 하는 사용자의 기능을 사용 하지 않거나 사용 하도록 설정할 수 있습니다.

1. [전역 관리자 권한](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
2. **Azure Active Directory**, **엔터프라이즈 응용 프로그램**, **사용자 설정**을 차례로 선택 합니다.
3. **사용자가 대신 회사 데이터에 액세스 하는 앱에 동의할 수**있는 컨트롤을 사용 하 여 사용자 동의를 사용 하거나 사용 하지 않도록 설정 합니다.
4. 필드 앱에 동의할 수 없는 사용자가 승인을 요청할 수 있도록 [관리자 동의 요청 워크플로](configure-admin-consent-workflow.md) 를 구성 합니다.

> [!TIP]
> 사용자가 동의할 수 없는 응용 프로그램에 대 한 관리자의 검토를 요청할 수 있도록 하려면 (예: 사용자 동의가 사용 하지 않도록 설정 되어 있거나 응용 프로그램에서 사용자에 게 허용 되지 않는 권한을 요청 하는 경우) [관리자 동의 워크플로를 구성](configure-admin-consent-workflow.md)하는 것이 좋습니다.

### <a name="disable-or-enable-user-consent-using-powershell"></a>PowerShell을 사용 하 여 사용자 동의 사용 또는 사용 안 함

Azure AD PowerShell v1 모듈 ([Msonline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0))을 사용 하 여 조직의 데이터에 액세스 하는 응용 프로그램에 동의 하는 사용자의 기능을 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

1. 이 cmdlet을 실행 하 여 조직에 로그인 합니다.

    ```powershell
    Connect-MsolService
    ```

2. 이 cmdlet을 실행 하 여 사용자 동의가 사용 되는지 확인 합니다.

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. 사용자 동의를 사용 하거나 사용 하지 않도록 설정 합니다. 예를 들어 사용자 동의를 사용 하지 않도록 설정 하려면이 cmdlet을 실행 합니다.

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>그룹 데이터에 액세스 하는 앱에 대 한 그룹 소유자 동의 구성

> [!IMPORTANT]
> 다음 정보는 그룹 소유자가 응용 프로그램에 해당 그룹 데이터에 대 한 액세스 권한을 부여할 수 있도록 하는 예정 된 기능에 대 한 정보입니다. 이 기능을 해제 하면 기본적으로 사용 하도록 설정 됩니다. 이 기능은 아직 널리 릴리스되지 않았지만 이러한 지침을 사용 하 여 해당 기능을 해제 하는 데 사용할 수 있습니다.

그룹 소유자는 응용 프로그램 (예: 타사 공급 업체에서 게시 한 응용 프로그램)에 게 그룹에 연결 된 조직의 데이터에 액세스할 수 있는 권한을 부여할 수 있습니다. 예를 들어 팀 소유자 (팀에 대 한 Office 365 그룹의 소유자)는 앱이 팀의 모든 팀 메시지를 읽을 수 있도록 허용 하거나 그룹 멤버의 기본 프로필을 나열할 수 있습니다.

> [!NOTE]
> 이 설정에 관계 없이 그룹 소유자는 항상 다른 사용자 또는 앱을 그룹 소유자로 직접 추가할 수 있습니다.

### <a name="configure-group-owner-consent-using-powershell"></a>PowerShell을 사용 하 여 그룹 소유자 동의 구성

Azure AD PowerShell Preview 모듈 ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview))을 사용 하 여 소유 하 고 있는 그룹에 대 한 조직의 데이터에 액세스 하는 응용 프로그램에 동의 하는 그룹 소유자의 기능을 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

1. [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 모듈을 사용 하 고 있는지 확인 합니다. [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) 모듈과 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 모듈을 모두 설치한 경우이 단계가 중요 합니다.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Azure AD PowerShell에 연결 합니다.

   ```powershell
   Connect-AzureAD
   ```

3. 테넌트에서 *동의 정책 설정* 디렉터리 설정에 대 한 현재 값을 검색 합니다. 이 기능을 사용 하려면 해당 디렉터리 설정 템플릿의 값을 사용 하 여이 기능에 대 한 디렉터리 설정이 만들어졌는지 확인 해야 합니다.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. 설정 값을 이해 합니다. 앱에서 해당 그룹의 데이터에 액세스 하도록 허용할 수 있는 사용자를 정의 하는 두 가지 설정 값이 있습니다.

    | 설정       | 유형         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | 부울 |  그룹 소유자가 그룹 특정 사용 권한을 부여할 수 있는지 여부를 나타내는 플래그입니다. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | _EnableGroupSpecificConsent_ 를 "True"로 설정 하 고이 값을 그룹의 개체 ID로 설정 하는 경우 식별 된 그룹의 멤버는 자신이 소유 하는 그룹에 대 한 그룹 특정 권한을 부여할 수 있는 권한이 부여 됩니다. |

5. 원하는 구성에 대 한 설정 값을 업데이트 합니다.

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. 설정을 저장 합니다.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>다음 단계

[관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)

[응용 프로그램에 대 한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)

[Microsoft id 플랫폼에서 사용 권한 및 동의](../develop/active-directory-v2-scopes.md)

[StackOverflow의 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
