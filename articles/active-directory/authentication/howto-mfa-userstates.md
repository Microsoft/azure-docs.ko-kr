---
title: 사용자별 Multi-Factor Authentication 사용 - Azure Active Directory
description: 사용자 상태를 변경하여 사용자별 Azure Multi-Factor Authentication을 사용하도록 설정하는 방법을 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b1ff8a54037392ac01402056ada0f3040dbd123
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068935"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>사용자별 Azure Multi-Factor Authentication을 사용하여 로그인 이벤트 보호

Azure AD에서 사용자 로그인 이벤트를 보호 하려면 MFA (multi-factor authentication)를 요구할 수 있습니다. 사용자를 보호 하려면 조건부 액세스 정책을 사용 하 여 Azure Multi-Factor Authentication를 사용 하도록 설정 하는 것이 좋습니다. 조건부 액세스는 특정 시나리오에서 필요에 따라 MFA를 요구 하는 규칙을 적용할 수 있도록 하는 Azure AD Premium P1 또는 P2 기능입니다. 조건부 액세스 사용을 시작하려면 [자습서: Azure Multi-Factor Authentication을 사용하여 사용자 로그인 이벤트 보호](tutorial-enable-azure-mfa.md)를 참조하세요.

조건부 액세스 권한이 없는 Azure AD 무료 테 넌 트의 경우 [보안 기본값을 사용 하 여 사용자를 보호할](../fundamentals/concept-fundamentals-security-defaults.md)수 있습니다. 필요에 따라 사용자에 게 MFA를 입력 하 라는 메시지가 표시 되지만 동작을 제어 하는 고유한 규칙을 정의할 수 없습니다.

필요한 경우 사용자 단위 Azure Multi-Factor Authentication에 대 한 각 계정을 대신 사용 하도록 설정할 수 있습니다. 사용자가 개별적으로 사용 하도록 설정 된 경우 사용자가 로그인 할 때마다 multi-factor authentication을 수행 합니다 (예: 신뢰할 수 있는 IP 주소에서 로그인 하거나 _신뢰할 수 있는 장치에서 MFA를 기억할_ 때).

Azure AD 라이선스에 조건부 액세스가 포함 되지 않고 보안 기본값을 사용 하지 않는 경우 사용자 상태를 변경 하지 않는 것이 좋습니다. MFA를 사용 하도록 설정 하는 다양 한 방법에 대 한 자세한 내용은 [Azure Multi-Factor Authentication의 기능 및 라이선스](concept-mfa-licensing.md)를 참조 하세요.

> [!IMPORTANT]
>
> 이 문서에서는 사용자별 Azure Multi-Factor Authentication의 상태를 보고 변경 하는 방법에 대해 자세히 설명 합니다. 조건부 액세스 또는 보안 기본값을 사용 하는 경우 다음 단계를 사용 하 여 사용자 계정을 검토 하거나 사용 하도록 설정 하지 않습니다.
>
> 조건부 액세스 정책을 통해 Azure Multi-Factor Authentication을 사용하도록 설정해도 사용자의 상태는 변경되지 않습니다. 사용자가 사용 안 함으로 나타나는 경우 놀라지 마십시오. 조건부 액세스는 상태를 변경하지 않습니다.
>
> **조건부 액세스 정책을 사용 하는 경우 사용자별 Azure Multi-Factor Authentication를 사용 하도록 설정 하거나 적용 하지 마십시오.**

## <a name="azure-multi-factor-authentication-user-states"></a>Azure Multi-Factor Authentication 사용자 상태

사용자의 상태는 관리자가 사용자별 Azure Multi-Factor Authentication에 등록 했는지 여부를 반영 합니다. Azure Multi-Factor Authentication의 사용자 계정은 다음과 같은 3가지 상태를 갖습니다.

| 시스템 상태 | 설명 | 영향을 받는 레거시 인증 | 영향 받는 브라우저 앱 | 영향 받는 최신 인증 |
|:---:| --- |:---:|:--:|:--:|
| 사용 안 함 | 사용자 단위 Azure Multi-Factor Authentication에 등록 되지 않은 사용자의 기본 상태입니다. | 아니요 | 아니요 | 예 |
| 사용 | 사용자가 사용자 단위 Azure Multi-Factor Authentication에 등록 되어 있지만 레거시 인증에 암호를 계속 사용할 수 있습니다. 사용자가 MFA 인증 방법을 아직 등록 하지 않은 경우 다음에 최신 인증을 사용 하 여 로그인 할 때 등록 하 라는 메시지가 표시 됩니다 (예: 웹 브라우저를 통해). | 아니요. 레거시 인증은 등록 프로세스가 완료 될 때까지 계속 작동 합니다. | 예. 세션이 만료되면 Azure Multi-Factor Authentication 등록이 필요합니다.| 예. 액세스 토큰이 만료되면 Azure Multi-Factor Authentication 등록이 필요합니다. |
| 적용 | 사용자가 Azure Multi-Factor Authentication에 등록 되어 있습니다. 사용자가 인증 방법을 아직 등록 하지 않은 경우 다음에 최신 인증을 사용 하 여 로그인 할 때 등록 하 라는 메시지가 표시 됩니다 (예: 웹 브라우저를 통해). *활성화* 된 상태에서 등록을 완료 하는 사용자는 자동으로 *적용* 됨 상태로 전환 됩니다. | 예. 앱에 앱 암호가 필요합니다. | 예. 로그인 할 때 Azure Multi-Factor Authentication 필요 합니다. | 예. 로그인 할 때 Azure Multi-Factor Authentication 필요 합니다. |

모든 사용자는 *사용 안 함*으로 시작합니다. 사용자 단위 Azure Multi-Factor Authentication에 사용자를 등록 하면 상태가 *사용*으로 변경 됩니다. 사용된 사용자가 로그인하고 등록 프로세스를 완료하면 상태는 변경 *적용*으로 변경됩니다. 관리자는 *적용* *됨에서 사용* 또는 *사용 안 함*을 포함 하 여 상태 간에 사용자를 이동할 수 있습니다.

> [!NOTE]
> 사용자 단위 MFA가 사용자에 대해 다시 사용 하도록 설정 되 고 사용자가 다시 등록 되지 않은 경우 mfa 상태가 *사용* 에서 MFA 관리 UI에 *적용* 으로 전환 되지 않습니다. 관리자는 *강제로*사용자를 직접 이동 해야 합니다.

## <a name="view-the-status-for-a-user"></a>사용자 상태 보기

사용자 상태를 확인 하 고 관리 하려면 다음 단계를 완료 하 여 Azure Portal 페이지에 액세스 합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Azure Active Directory*를 검색하고 선택한 다음, **사용자** > **모든 사용자**를 선택합니다.
1. **Multi-Factor Authentication**을 선택합니다. 이 메뉴 옵션을 보려면 오른쪽으로 스크롤해야 할 수 있습니다. 다음 스크린샷 예를 선택 하 여 전체 Azure Portal 창 및 메뉴 위치를 확인 합니다. [ ![ Azure AD의 사용자 창에서 Multi-Factor Authentication를 선택 합니다.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 다음 예제와 같이 사용자 상태를 표시하는 새 페이지가 열립니다.
   ![Azure Multi-Factor Authentication에 대한 예제 사용자 상태 정보를 보여 주는 스크린샷](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>사용자 상태 변경

사용자에 대 한 사용자 단위 Azure Multi-Factor Authentication 상태를 변경 하려면 다음 단계를 완료 합니다.

1. 이전 단계를 사용 하 여 사용자가 Azure Multi-Factor Authentication **사용자** 페이지로 이동할 수 있는 [상태를 확인](#view-the-status-for-a-user) 합니다.
1. 사용자별 Azure Multi-Factor Authentication에 대해 사용 하도록 설정할 사용자를 찾습니다. 위쪽에서 보기를 **사용자**로 변경해야 할 수도 있습니다.
   ![사용자 탭에서 상태를 변경할 사용자 선택](./media/howto-mfa-userstates/enable1.png)
1. 상태를 변경할 사용자의 이름 옆에 있는 확인란을 선택합니다.
1. 오른쪽의 **빠른 단계**에서 **사용** 또는 **사용 안 함**을 선택합니다. 다음 예제에서 사용자 *John Smith*는 이름 옆에 확인이 있으며 사용할 수 있도록 설정되어 있습니다. ![빠른 단계 메뉴에서 사용을 클릭하여 선택한 사용자 사용](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Azure Multi-Factor Authentication에 등록하면 *사용* 사용자는 *적용*으로 자동으로 전환됩니다. 사용자가 이미 등록 되어 있지 않거나 사용자가 레거시 인증 프로토콜에 대 한 연결을 중단 하는 것이 허용 되는 경우에만 사용자 상태를 *강제로 적용* 하도록 수동으로 변경 하지 마십시오.

1. 열리는 팝업 창에서 선택한 내용을 확인합니다.

사용자를 사용으로 설정한 후 전자 메일을 통해 알립니다. 사용자에게 다음에 로그인할 때 등록하도록 요청하라는 메시지가 표시됨을 알려 줍니다. 또한 조직에서 최신 인증을 지원하지 않는 비브라우저 앱을 사용하는 경우 앱 암호를 만들어야 합니다. 자세한 내용은 [Azure Multi-Factor Authentication 최종 사용자 가이드](../user-help/multi-factor-authentication-end-user-first-time.md)를 참조하여 시작하는 데 도움을 받으세요.

## <a name="change-state-using-powershell"></a>PowerShell을 사용하여 상태 변경

[Azure AD PowerShell](/powershell/azure/)을 사용하여 사용자 상태를 변경하려면 사용자 계정에 대한 `$st.State` 매개 변수를 변경합니다. 사용자 계정에 대한 세 가지 가능한 상태가 있습니다.

* *Enabled*
* *적용*
* *사용 안 함*  

일반적으로 MFA에 대해 아직 등록 하지 않은 경우 사용자를 *강제 적용* 상태로 직접 이동 하지 마세요. 이렇게 하면 사용자가 Azure Multi-Factor Authentication 등록을 거치지 않았고 [앱 암호](howto-mfa-app-passwords.md)를 얻 었으 므로 레거시 인증 앱 작동이 중단 됩니다. 일부 경우에는이 동작을 원할 수도 있지만 사용자가 등록할 때 까지는 사용자 환경에 영향을 줍니다.

시작하려면 다음과 같이 [Install-Module](/powershell/module/powershellget/install-module)을 사용하여 *MSOnline* 모듈을 설치합니다.

```PowerShell
Install-Module MSOnline
```

다음으로 [Connect-MsolService](/powershell/module/msonline/connect-msolservice)를 사용하여 연결합니다.

```PowerShell
Connect-MsolService
```

다음 예제 PowerShell 스크립트는 *bsimon@contoso.com* 이라는 개별 사용자에 대해 MFA를 사용하도록 설정합니다.

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

PowerShell은 사용자를 대량 설정해야 할 때 적합한 옵션입니다. 다음 스크립트는 사용자 목록을 반복하고 해당 계정에서 MFA를 사용하도록 설정합니다. 다음과 같이 `$users`에 대한 첫 번째 줄에 설정하는 사용자 계정을 정의합니다.

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

MFA를 사용하지 않도록 설정하기 위해 다음 예제에서는 [Get-MsolUser](/powershell/module/msonline/get-msoluser)를 사용하여 사용자를 가져온 다음, [Set-MsolUser](/powershell/module/msonline/set-msoluser)를 사용하여 정의된 사용자에 대한 *StrongAuthenticationRequirements* 세트를 제거합니다.

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

다음과 같이 [Set-MsolUser](/powershell/module/msonline/set-msoluser)를 사용하여 사용자에 대해 MFA를 직접 사용하지 않도록 설정할 수도 있습니다.

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>사용자별 MFA 사용자를 조건부 액세스로 변환

다음 PowerShell은 조건부 액세스 기반 Azure Multi-Factor Authentication으로 변환하는 데 도움이 될 수 있습니다.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 사용자가 MFA를 다시 사용 하도록 설정 하 고 사용자가 다시 등록 되지 않은 경우 mfa 상태는 *사용* 에서 MFA 관리 UI에 *적용* 으로 전환 되지 않습니다. 이 경우 관리자는 사용자를 직접 *적용*하도록 이동 해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Multi-Factor Authentication 설정을 구성 하려면  [azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md)을 참조 하세요.

Azure Multi-Factor Authentication에 대한 사용자 설정을 관리하려면 [Azure Multi-Factor Authentication을 사용하여 사용자 설정 관리](howto-mfa-userdevicesettings.md)를 참조하세요.

사용자에게 메시지가 표시되거나 MFA를 수행하라는 메시지가 표시되지 않는 이유를 이해하려면 [Azure Multi-Factor Authentication 보고서](howto-mfa-reporting.md)를 참조하세요.
