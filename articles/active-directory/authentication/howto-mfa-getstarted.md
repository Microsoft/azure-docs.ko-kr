---
title: Azure Multi-Factor Authentication을 위한 배포 고려 사항
description: Azure Multi-Factor Authentication의 성공적인 구현을 위한 배포 고려 사항 및 전략에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: da9a0fe30721a2f55db2c4771f54716868f9972f
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055076"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Azure Multi-Factor Authentication 배포 계획

사용자는 점점 더 복잡한 시나리오에서 조직 리소스에 연결하고 있습니다. 사용자는 대개 여러 플랫폼의 스마트폰, 태블릿, PC 및 랩톱을 사용하여 회사 네트워크 내외부에서 조직 소유, 개인 및 공용 디바이스를 통해 연결합니다. 이처럼 항상 연결된 다중 디바이스 및 다중 플랫폼 환경에서는 사용자 계정의 보안이 그 어느 때보다도 중요합니다. 디바이스, 네트워크 및 플랫폼에서 사용되는 암호는 복잡성과 관계없이 사용자 계정에 대한 보안을 보장하기에 더 이상 충분하지 않으며, 특히 사용자가 여러 계정에서 암호를 다시 사용하는 경향이 있는 경우 더욱더 그렇습니다. 정교한 피싱 및 기타 소셜 엔지니어링 공격의 결과로 사용자 이름 및 암호가 다크 웹에서 게시되고 판매될 수 있습니다.

[Azure MFA(Multi-Factor Authentication)](concept-mfa-howitworks.md)는 데이터와 애플리케이션에 대한 액세스 보호를 지원합니다. 두 번째 형태의 인증을 사용하여 추가 보안 레이어를 제공합니다. 조직에서는 [조건부 액세스](../conditional-access/overview.md)를 사용하여 솔루션을 특정 요구 사항에 맞게 만들 수 있습니다.

이 배포 가이드에서는 Azure Multi-Factor Authentication 출시를 계획하고 테스트하는 방법을 보여 줍니다.

작동 중인 Azure Multi-Factor Authentication을 빠르게 확인한 후 다시 돌아와서 추가 배포 고려 사항을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication 사용](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>사전 요구 사항

Azure Multi-Factor Authentication 배포를 시작하기 전에 고려해야 하는 필수 항목이 있습니다.

| 시나리오 | 필수 요소 |
| --- | --- |
| 최신 인증을 사용하는 **클라우드 전용** ID 환경 | **추가 사전 요구 작업 없음** |
| **하이브리드** ID 시나리오 | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)가 배포되고 사용자 ID가 Azure Active Directory를 사용하여 온-프레미스 Active Directory Domain Services와 동기화되거나 페더레이션됩니다. |
| 클라우드 액세스용으로 게시된 온-프레미스 레거시 애플리케이션 | Azure AD [애플리케이션 프록시](../manage-apps/application-proxy.md)가 배포됩니다. |
| RADIUS 인증과 함께 Azure MFA 사용 | [NPS(네트워크 정책 서버)](howto-mfa-nps-extension.md)가 배포됩니다. |
| 사용자가 Microsoft Office 2010 이하를 보유하거나 iOS 11 이하용 Apple Mail 보유 | [Microsoft Office 2013 이상](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) 및 iOS 12 이상용 Apple Mail로 업그레이드합니다. 레거시 인증 프로토콜에서는 조건부 액세스를 지원하지 않습니다. |

## <a name="plan-user-rollout"></a>사용자 출시 계획

MFA 출시 계획에는 파일럿 배포와 지원 용량 내에 있는 배포 웨이브가 포함되어야 합니다. 소그룹의 파일럿 사용자에 조건부 액세스 정책을 적용하여 출시를 시작합니다. 파일럿 사용자, 사용된 프로세스 및 등록 동작에 미치는 영향을 평가한 후 정책에 그룹을 더 추가하거나 기존 그룹에 사용자를 더 추가할 수 있습니다.

### <a name="user-communications"></a>사용자 통신

계획된 통신으로 사용자에게 예정된 변경 내용, Azure MFA 등록 요구 사항 및 필요한 모든 사용자 작업에 대해 알리는 것이 중요합니다. 통신, 변경 관리 또는 인적 자원 부서와 같은 조직 내 담당자와 함께 통신을 개발하는 것이 좋습니다.

Microsoft는 [통신 템플릿](https://aka.ms/mfatemplates) 및 [최종 사용자 설명서](../user-help/security-info-setup-signin.md)를 제공하여 통신 초안을 간단하게 작성할 수 있도록 지원합니다. 사용자를 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)으로 보내 해당 페이지에서 **보안 정보** 링크를 선택하여 직접 등록하게 할 수 있습니다.

## <a name="deployment-considerations"></a>배포 고려 사항

Azure Multi-factor Authentication은 조건부 액세스로 정책을 적용하여 배포됩니다. 조건부 액세스 정책을 사용하여 다음과 같은 특정 조건이 충족될 때 사용자가 다단계 인증을 수행하도록 요구할 수 있습니다.

* 모든 사용자, 특정 사용자, 그룹 구성원 또는 할당된 역할
* 액세스되는 특정 클라우드 애플리케이션
* 디바이스 플랫폼
* 디바이스 상태
* 네트워크 위치 또는 지리적으로 위치한 IP 주소
* 클라이언트 애플리케이션
* 로그인 위험(ID 보호 필요)
* 규정 준수 디바이스
* 하이브리드 Azure AD 조인 디바이스
* 승인된 클라이언트 애플리케이션

[다단계 인증 출시 자료](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)에서 사용자 지정 가능한 포스터 및 메일 템플릿을 사용하여 조직에 다단계 인증을 출시할 수 있습니다.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>조건부 액세스를 사용하여 Multi-Factor Authentication 사용

조건부 액세스 정책은 등록을 적용하여 등록되지 않은 사용자가 처음 로그인할 때 등록을 완료하도록 하는 중요한 보안 고려 사항입니다.

[Azure AD ID 보호](../identity-protection/howto-identity-protection-configure-risk-policies.md)는 Azure Multi-Factor Authentication 스토리에 대한 등록 정책과 자동화된 위험 검색 및 수정 정책에 모두 기여합니다. 손상된 ID 위협이 있을 때 암호 변경을 강제하거나 다음 [이벤트](../identity-protection/overview-identity-protection.md)에 의해 로그인이 위험한 것으로 간주되는 경우 MFA를 요구하는 정책을 만들 수 있습니다.

* 유출된 자격 증명
* 익명 IP 주소에서 로그인
* 비정상적 위치로 불가능한 이동
* 알 수 없는 위치에서 로그인
* 감염된 디바이스에서 로그인
* 의심스러운 작업이 있는 IP 주소에서 로그인

Azure Active Directory Identity Protection에서 검색되는 일부 위험 검색은 실시간으로 발생하고 일부는 오프라인 처리가 필요합니다. 관리자는 위험한 동작을 보이는 사용자를 차단하고 수동으로 수정하거나, 암호 변경을 요구하거나, 조건부 액세스 정책의 일부로 다단계 인증을 요구하도록 선택할 수 있습니다.

## <a name="define-network-locations"></a>네트워크 위치 정의

조직에서는 [명명된 위치](../conditional-access/location-condition.md#named-locations)를 사용하여 네트워크를 정의하는 조건부 액세스를 사용하는 것이 좋습니다. 조직에서 ID 보호를 사용하는 경우 명명된 위치 대신 위험 기반 정책을 사용하는 것이 좋습니다.

### <a name="configuring-a-named-location"></a>명명된 위치 구성

1. Azure Portal에서 **Azure Active Directory**를 엽니다.
2. **보안** 선택
3. **관리**에서 **명명된 위치**를 선택합니다.
4. **새 위치**를 선택합니다.
5. **이름** 필드에 유효한 이름을 입력합니다.
6. ‘IP 범위’ 또는 ‘국가/지역’ 중 무엇을 사용하여 위치를 정의할지 선택합니다. 
   1. ‘IP 범위’를 사용하는 경우
      1. ‘신뢰할 수 있는 위치로 표시’ 여부를 결정합니다. 신뢰할 수 있는 위치에서 로그인하면 사용자의 로그인 위험이 줄어듭니다. 입력한 IP 범위가 조직에 설정되어 신뢰할 수 있는 경우 이 위치만 신뢰할 수 있음으로 표시하세요.
      2. IP 범위 지정
   2. ‘국가/지역’을 사용하는 경우
      1. 드롭다운 메뉴를 확장하고 이 명명된 위치에 대해 정의하려는 국가 또는 지역을 선택합니다.
      2. ‘알 수 없는 영역 포함’ 여부를 결정합니다. 알 수 없는 영역은 국가/지역으로 매핑할 수 없는 IP 주소입니다.
7. **만들기**

## <a name="plan-authentication-methods"></a>인증 방법 계획

관리자는 사용자에게 제공할 [인증 방법](../authentication/concept-authentication-methods.md)을 선택할 수 있습니다. 사용자가 기본 방법을 사용할 수 없는 경우 백업 방법을 사용할 수 있도록 하나 이상의 인증 방법을 허용하는 것이 중요합니다. 관리자는 다음 방법을 사용하여 이를 설정할 수 있습니다.

> [!TIP]
> Microsoft는 보다 안전 하 고 향상 된 사용자 환경을 위해 Azure Multi-Factor Authentication의 기본 방법으로 Microsoft Authenticator (모바일 앱)를 사용 하는 것이 좋습니다. 또한 Microsoft Authenticator 앱은 국가 표준 및 기술 인증자 보증 수준을 [충족](https://azure.microsoft.com/resources/microsoft-nist/) 합니다. 

### <a name="notification-through-mobile-app"></a>모바일 앱을 통한 알림

푸시 알림이 모바일 디바이스의 Microsoft Authenticator 앱으로 보내집니다. 사용자는 알림을 보고 **승인**을 선택하여 확인을 완료합니다. 모바일 앱을 통한 푸시 알림이 사용자에게 가장 거슬리지 않는 옵션을 제공합니다. 또한 전화 통신 대신 데이터 연결을 사용하기 때문에 가장 안정적이고 안전한 옵션이기도 합니다.

> [!NOTE]
> 조직에서 중국에 근무하거나 출장을 가는 직원이 있을 경우 **Android 디바이스**에서 **모바일 앱을 통한 알림** 방법은 해당 국가/지역에서 작동하지 않습니다. 이러한 사용자에 대해서는 다른 방법을 사용하도록 해야 합니다.

### <a name="verification-code-from-mobile-app"></a>모바일 앱의 확인 코드

Microsoft Authenticator 앱과 같은 모바일 앱은 30초마다 새로운 OATH 확인 코드를 생성합니다. 사용자는 로그인 인터페이스에 확인 코드를 입력합니다. 휴대폰에 데이터 또는 셀룰러 신호가 있는지와 관계없이 모바일 앱 옵션을 사용할 수 있습니다.

### <a name="call-to-phone"></a>휴대폰에 전화 걸기

사용자에게 자동 음성 통화를 겁니다. 사용자가 전화를 받고 휴대폰 키패드에서 **#** 을 눌러 인증을 승인합니다. 휴대폰 통화는 모바일 앱의 알림 또는 확인 코드 대신 사용할 수 있는 좋은 방법입니다.

### <a name="text-message-to-phone"></a>휴대폰에 문자 메시지 전송

사용자에게 확인 코드가 포함된 문자 메시지가 전송되고 로그인 인터페이스에 확인 코드를 입력하라는 메시지가 표시됩니다.

### <a name="choose-verification-options"></a>확인 옵션 선택

1. **Azure Active Directory**, **사용자**, **Multi-Factor Authentication**으로 이동합니다.

   ![Azure Portal의 Azure AD 사용자 블레이드에서 Multi-Factor Authentication 포털에 액세스](media/howto-mfa-getstarted/users-mfa.png)

1. 열리는 새 탭에서 **서비스 설정**으로 이동합니다.
1. **확인 옵션** 아래에서, 사용자에게 제공할 방법의 모든 확인란을 선택합니다.

   ![Multi-Factor Authentication 서비스 설정 탭에서 인증 방법 구성](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. **Save**를 클릭합니다.
1. **서비스 설정** 탭을 닫습니다.

## <a name="plan-registration-policy"></a>등록 정책 계획

관리자는 어떻게 사용자가 방법을 등록할지 결정해야 합니다. 조직에서는 Azure MFA 및 SSPR(셀프 서비스 암호 재설정)에 [결합된 새 등록 환경을 사용하도록 설정](howto-registration-mfa-sspr-combined.md)해야 합니다. SSPR를 사용하면 사용자가 다단계 인증에 사용하는 것과 동일한 방법으로 암호를 안전하게 재설정할 수 있습니다. 사용자가 두 서비스에 대해 한 번에 등록하는 기능을 제공하므로 이 결합된 등록을 사용하는 것이 좋습니다. SSPR 및 Azure MFA에 대해 동일한 방법을 사용하도록 설정하면 사용자가 두 기능을 모두 사용하도록 등록할 수 있습니다.

### <a name="registration-with-identity-protection"></a>ID 보호를 사용하여 등록

조직에서 Azure Active Directory Identity Protection을 사용하는 경우 [MFA 등록 정책을 구성](../identity-protection/howto-identity-protection-configure-mfa-policy.md)하여 사용자가 다음에 대화형으로 로그인할 때 등록하라는 메시지를 표시하도록 합니다.

### <a name="registration-without-identity-protection"></a>ID 보호를 사용하지 않는 등록

조직에 ID 보호를 사용하는 라이선스가 없는 경우 사용자에게 로그인 시 다음에 MFA를 요구할 때 등록하라는 메시지가 표시됩니다. 사용자가 MFA로 보호된 애플리케이션을 사용하지 않는 경우 MFA에 등록되지 않을 수 있습니다. 모든 사용자를 등록시켜서 악의적인 행위자가 사용자의 암호를 추측하고 사용자를 대신해 MFA에 등록하여 계정을 효과적으로 제어하지 못하도록 하는 것이 중요합니다.

#### <a name="enforcing-registration"></a>등록 적용

다음 단계를 사용하여 조건부 액세스 정책을 통해 사용자가 Multi-Factor Authentication에 등록하도록 할 수 있습니다.

1. 그룹을 만들고 현재 등록되지 않은 모든 사용자를 추가합니다.
2. 조건부 액세스를 사용하여 모든 리소스에 액세스할 수 있도록 이 그룹에 대해 다단계 인증을 적용합니다.
3. 정기적으로 그룹 멤버 자격을 다시 평가하고 등록한 사용자를 그룹에서 제거합니다.

[MSOnline PowerShell 모듈](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)을 사용하는 PowerShell 명령을 사용하여 등록된 Azure MFA 사용자와 등록되지 않은 Azure MFA 사용자를 파악할 수 있습니다.

#### <a name="identify-registered-users"></a>등록된 사용자 파악

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>등록되지 않은 사용자 파악

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자별 MFA의 사용자를 조건부 액세스 기반 MFA로 변환

사용자가 사용자별로 적용된 Azure Multi-Factor Authentication을 사용하도록 설정했다면 다음 PowerShell을 사용하여 조건부 액세스 기반 Azure Multi-Factor Authentication으로 변환하는 데 도움을 받을 수 있습니다.

ISE 창에서 이 PowerShell을 실행하거나 로컬에 실행할 `.PS1` 파일로 저장합니다.

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
> 최근에 위의 동작 및 PowerShell 스크립트를 적절하게 변경했습니다. 이전에는 스크립트에서 MFA 방법을 저장하고 사용하지 않도록 설정했으며 복원했습니다. 이제 사용 안 함에 대한 기본 동작으로 해당 방법이 삭제되지 않으므로 이 작업이 필요하지 않습니다.

## <a name="plan-conditional-access-policies"></a>조건부 액세스 정책 계획

MFA 및 기타 제어가 필요한 시기를 결정하는 조건부 액세스 정책 전략을 계획하려면 [일반 조건부 액세스 정책](../conditional-access/concept-conditional-access-policy-common.md)을 참조하세요.

Azure AD 테넌트에서 실수로 차단되는 것을 방지하는 것이 중요합니다. 테넌트에 두 개 이상의 [응급 액세스 계정](../users-groups-roles/directory-emergency-access.md)을 만들고 이 계정을 조건부 액세스 정책에서 제외하면 실수로 관리 액세스 권한이 부족해 지면서 미치는 영향을 완화할 수 있습니다.

### <a name="create-conditional-access-policy"></a>조건부 액세스 정책 만들기

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
   ![파일럿 그룹의 Azure Portal 사용자에게 MFA를 사용하도록 조건부 액세스 정책 만들기](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 의미 있는 정책 이름을 입력합니다.
1. **사용자 및 그룹** 아래에서:
   * **포함** 탭에서 **모든 사용자** 라디오 단추를 선택합니다.
   * **제외** 탭에서 **사용자 및 그룹** 확인란을 선택하고 응급 액세스 계정을 선택합니다.
   * **Done**을 클릭합니다.
1. **클라우드 앱**에서 **모든 클라우드 앱** 라디오 단추를 선택합니다.
   * 선택 사항: **제외** 탭에서 조직에서 MFA를 요구하지 않는 클라우드 앱을 선택합니다.
   * **Done**을 클릭합니다.
1. **조건** 섹션에서:
   * 선택 사항: Azure Identity Protection을 사용하도록 설정한 경우 정책의 일부로 로그인 위험을 평가하도록 선택할 수 있습니다.
   * 선택 사항: 신뢰할 수 있는 위치를 구성했거나 위치 이름을 지정한 경우 정책에 해당 위치를 포함 또는 제거하도록 지정할 수 있습니다.
1. **권한 부여** 아래에서 **액세스 권한 부여** 라디오 단추를 선택합니다.
    * **Multi-Factor Authentication 필요** 확인란을 선택합니다.
    * **선택**을 클릭합니다.
1. **세션** 섹션을 건너뜁니다.
1. **정책 사용** 토글을 **켜기**로 설정합니다.
1. **만들기**를 클릭합니다.

## <a name="plan-integration-with-on-premises-systems"></a>온-프레미스 시스템과 통합 계획

Azure AD에 대해 직접 인증하지 않는 일부 레거시 및 온-프레미스 애플리케이션에는 다음을 포함하여 MFA를 사용하는 추가 단계가 필요합니다.

* 애플리케이션 프록시를 사용해야 하는 레거시 온-프레미스 애플리케이션
* NPS 서버에서 MFA 어댑터를 사용해야 하는 온-프레미스 RADIUS 애플리케이션
* AD FS 2016 이상에서 MFA 어댑터를 사용해야 하는 온-프레미스 AD FS 애플리케이션

Azure AD에서 직접 인증하고 최신 인증(WS-Fed, SAML, OAuth, OpenID Connect)을 보유하는 애플리케이션은 조건부 액세스 정책을 직접 사용할 수 있습니다.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시로 Azure MFA 사용

온-프레미스에 있는 애플리케이션은 [Azure AD 애플리케이션 프록시](../manage-apps/application-proxy.md)를 통해 Azure AD 테넌트에 게시될 수 있으며 Azure AD 사전 인증을 사용하도록 구성된 경우 Azure Multi-Factor Authentication을 활용할 수 있습니다.

이러한 애플리케이션에는 다른 Azure AD 통합 애플리케이션과 마찬가지로 Azure Multi-Factor Authentication을 적용하는 조건부 액세스 정책이 적용됩니다.

마찬가지로 Azure Multi-Factor Authentication이 모든 사용자 로그인에 적용되는 경우 Azure AD 애플리케이션 프록시로 게시된 온-프레미스 애플리케이션은 보호됩니다.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>네트워크 정책 서버를 Azure Multi-Factor Authentication과 통합

Azure MFA의 NPS(네트워크 정책 서버) 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 MFA 기능을 추가합니다. 네트워크 NPS 확장을 사용하면 전화 통화, 문자 메시지 또는 휴대폰 앱 확인을 기존 인증 흐름에 추가할 수 있습니다. 이 통합에는 다음과 같은 제한 사항이 있습니다.

* CHAPv2 프로토콜을 사용하는 경우 인증자 앱 푸시 알림과 음성 통화만 지원됩니다.
* 조건부 액세스 정책을 적용할 수 없습니다.

NPS 확장은 RADIUS와 클라우드 기반 Azure MFA 사이에서 어댑터 역할을 하여 [VPN](howto-mfa-nps-extension-vpn.md), [원격 데스크톱 게이트웨이 연결](howto-mfa-nps-extension-rdg.md) 또는 다른 RADIUS 지원 애플리케이션을 보호하는 두 번째 인증 단계를 제공합니다. 이 환경에서 Azure MFA에 등록하는 사용자는 모든 인증 시도에서 인증 절차를 거쳐야 하며 조건부 액세스 정책이 부족하면 MFA가 항상 요구됩니다.

#### <a name="implementing-your-nps-server"></a>NPS 서버 구현

NPS 인스턴스를 배포하고 이미 사용하고 있는 경우 [기존 NPS 인프라를 Azure Multi-Factor Authentication과 통합](howto-mfa-nps-extension.md)을 참조합니다. NPS를 처음 설정하는 경우 [NPS(네트워크 정책 서버)](/windows-server/networking/technologies/nps/nps-top)에서 지침을 참조하세요. 문제 해결 지침은 [Azure Multi-factor Authentication용 NPS 확장의 오류 메시지 해결](howto-mfa-nps-extension-errors.md) 문서에 나와 있습니다.

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>MFA에 등록되지 않은 사용자를 위한 NPS 준비

MFA에 등록되지 않은 사용자가 인증을 시도하는 경우 수행할 작업을 선택합니다. 레지스트리 경로 `HKLM\Software\Microsoft\AzureMFA`에서 레지스트리 설정 `REQUIRE_USER_MATCH`를 사용하여 기능 동작을 제어합니다. 이 설정에는 다음과 같은 단일 구성 옵션이 있습니다.

| 키 | 값 | 기본값 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE/FALSE | 설정되지 않음(TRUE와 동일) |

이 설정은 사용자가 MFA에 등록되지 않은 경우 수행할 작업을 결정하기 위한 것입니다. 이 설정 변경의 영향은 아래 표에 나와 있습니다.

| 설정 | 사용자 MFA 상태 | 효과 |
| --- | --- | --- |
| 키가 없음 | 등록되지 않음 | MFA 챌린지 실패 |
| True/설정 안 함으로 설정되는 값 | 등록되지 않음 | MFA 챌린지 실패 |
| False로 설정되는 키 | 등록되지 않음 | MFA를 사용하지 않은 인증 |
| False 또는 True로 설정되는 키 | 등록됨 | MFA를 사용하여 인증해야 함 |

### <a name="integrate-with-active-directory-federation-services"></a>Active Directory Federation Services와 통합

조직이 Azure AD를 사용하여 페더레이션되는 경우 온-프레미스와 클라우드 모두에서 [Azure Multi-Factor Authentication을 사용하여 AD FS 리소스를 보호](multi-factor-authentication-get-started-adfs.md)할 수 있습니다. Azure MFA를 사용하면 암호를 줄이고 더욱 안전하게 인증할 수 있습니다. Windows Server 2016부터는 기본 인증을 위해 Azure MFA를 구성할 수 있습니다.

Windows Server 2012 R2의 AD FS와 달리 AD FS 2016 Azure MFA 어댑터는 Azure AD와 직접 통합되며 온-프레미스 Azure MFA 서버가 필요하지 않습니다. Azure MFA 어댑터는 Windows Server 2016에 기본 제공되며 추가 설치는 필요하지 않습니다.

AD FS 2016과 함께 Azure MFA를 사용하고 대상 애플리케이션에 조건부 액세스 정책이 적용되는 경우 추가로 고려해야 할 사항이 있습니다.

* 애플리케이션이 Azure AD에 대한 신뢰 당사자이고 AD FS 2016 이상과 페더레이션된 경우 조건부 액세스를 사용할 수 있습니다.
* 애플리케이션이 AD FS 2016 또는 AD FS 2019에 대한 신뢰 당사자이며 AD FS 2016 또는 AD FS 2019로 관리되거나 페더레이션된 경우 조건부 액세스를 사용할 수 없습니다.
* AD FS 2016 또는 AD FS 2019가 기본 인증 방법으로 Azure MFA를 사용하도록 구성된 경우에도 조건부 액세스를 사용할 수 없습니다.

#### <a name="ad-fs-logging"></a>AD FS 로깅

Windows 보안 로그 및 AD FS 관리자 로그 모두에서 표준 AD FS 2016 및 2019 로깅에는 인증 요청 및 해당 성공 또는 실패에 대한 정보가 포함됩니다. 이러한 이벤트 내의 이벤트 로그 데이터는 Azure MFA가 사용되었는지를 나타냅니다. 예를 들어 AD FS 감사 이벤트 ID 1200에는 다음이 포함될 수 있습니다.

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>인증서 갱신 및 관리

각 AD FS 서버에서 로컬 컴퓨터 내 저장소에 OU=Microsoft AD FS Azure MFA라는 자체 서명된 Azure MFA 인증서가 있으며 여기에는 인증서 만료 날짜가 포함되어 있습니다. 각 AD FS 서버에서 이 인증서의 유효 기간을 확인하여 만료 날짜를 확인합니다.

인증서의 유효 기간이 곧 만료되는 경우 [각 AD FS 서버에서 새 MFA 인증서를 생성하고 확인합니다](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

다음 지침에서는 AD FS 서버에서 Azure MFA 인증서를 관리하는 방법에 대해 자세히 설명합니다. Azure MFA를 사용하여 AD FS를 구성하는 경우 `New-AdfsAzureMfaTenantCertificate` PowerShell cmdlet을 통해 생성된 인증서는 2년 동안 유효합니다. MFA 서비스에서 중단을 피하려면 만료되기 전에 갱신하고 갱신된 인증서를 설치합니다.

## <a name="implement-your-plan"></a>계획 구현

이제 솔루션을 계획했으므로 다음 단계를 수행하여 구현할 수 있습니다.

1. 필요한 모든 필수 구성 요소 충족
   1. 모든 하이브리드 시나리오에 대해 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 배포
   1. 클라우드 액세스를 위해 게시된 온-프레미스 앱에 대해 [Azure AD 애플리케이션 프록시](../manage-apps/application-proxy.md) 배포
   1. RADIUS 인증을 위한 [NPS](/windows-server/networking/technologies/nps/nps-top) 배포
   1. 사용자가 최신 인증을 사용하도록 설정된 Microsoft Office 지원 버전으로 업그레이드했는지 확인
1. 선택한 [인증 방법](#choose-verification-options) 구성
1. [명명된 네트워크 위치 정의](../conditional-access/location-condition.md#named-locations)
1. 그룹을 선택하여 MFA 배포 시작
1. [조건부 액세스 정책](#create-conditional-access-policy) 구성
1. MFA 등록 정책 구성
   1. [결합된 MFA 및 SSPR](howto-registration-mfa-sspr-combined.md)
   1. [ID 보호](../identity-protection/howto-identity-protection-configure-mfa-policy.md) 포함
1. 사용자 통신을 보내 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에서 사용자가 등록하도록 함
1. [등록된 사용자 추적](#identify-non-registered-users)

> [!TIP]
> 정부 클라우드 사용자는 [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)에서 등록할 수 있습니다.

## <a name="manage-your-solution"></a>솔루션 관리

Azure MFA에 대한 보고서

Azure Multi-Factor Authentication은 Azure Portal을 통해 보고서를 제공합니다.

| 보고서 | 위치 | Description |
| --- | --- | --- |
| 사용량 및 사기 행위 경고 | Azure AD > 로그인 | 지정된 날짜 범위 동안 제출된 사기 행위 경고의 기록을 비롯한 전체 사용량, 사용자 요약 및 사용자 세부 정보에 대한 정보를 제공합니다. |

## <a name="troubleshoot-mfa-issues"></a>MFA 문제 해결

Azure MFA와 관련된 일반적인 문제에 대한 해결 방법의 자세한 내용은 Microsoft 지원 센터의 [Azure Multi-Factor Authentication 문제 해결 문서](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)를 참조하세요.

## <a name="next-steps"></a>다음 단계

작동 중인 Azure Multi-Factor Authentication을 확인하려면 다음 자습서를 완료합니다.

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication 사용](tutorial-enable-azure-mfa.md)
