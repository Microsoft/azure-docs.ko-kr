---
title: Azure Multi-Factor Authentication 배포-Azure Active Directory
description: Microsoft Azure Multi-Factor Authentication 배포 계획
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6da67589b15b4ab043510c0375c26c12f645adb
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155149"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>클라우드 기반 Azure Multi-Factor Authentication 배포 계획

사용자는 점점 더 복잡 한 시나리오에서 조직 리소스에 연결 하 고 있습니다. 사람들은 종종 여러 플랫폼에서 스마트폰, 태블릿, Pc 및 랩톱을 사용 하 여 회사 네트워크를 통해 회사 네트워크에 연결 하 고 장치를 연결 합니다. 이 항상 연결 된 다중 장치 및 다중 플랫폼 세계에서 사용자 계정의 보안은 그 어느 때 보다 더 중요 합니다. 장치, 네트워크 및 플랫폼에서 사용 되는 복잡성에 관계 없이 암호는 사용자 계정에 대 한 보안을 보장 하기에 더 이상 충분 하지 않습니다. 특히 사용자가 계정에서 암호를 다시 사용 하는 경향이 있습니다. 정교한 피싱 및 기타 소셜 엔지니어링 공격으로 인해 사용자 이름 및 암호를 진한 웹에서 게시 하 고 판매할 수 있습니다.

[MFA (Azure Multi-Factor Authentication)](concept-mfa-howitworks.md) 는 데이터 및 응용 프로그램에 대 한 액세스를 보호 하는 데 도움이 됩니다. 두 번째 형태의 인증을 사용 하 여 추가 보안 계층을 제공 합니다. 조직에서는 [조건부 액세스](../conditional-access/overview.md) 를 사용 하 여 솔루션을 특정 요구 사항에 맞게 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure Multi-Factor Authentication 배포를 시작 하기 전에 고려해 야 하는 필수 조건 항목이 있습니다.

| 시나리오 | 필수 조건 |
| --- | --- |
| 최신 인증을 사용 하는 **클라우드 전용** id 환경 | **추가 필수 구성 요소 작업 없음** |
| **하이브리드** id 시나리오 | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 배포 되 고 사용자 id가 Azure Active Directory를 사용 하 여 온-프레미스 Active Directory Domain Services와 동기화 되거나 페더레이션 됩니다. |
| 클라우드 액세스용으로 게시 된 온-프레미스 레거시 응용 프로그램 | Azure AD [응용 프로그램 프록시가](../manage-apps/application-proxy.md) 배포 됩니다. |
| RADIUS 인증과 함께 Azure MFA 사용 | [NPS (네트워크 정책 서버)](howto-mfa-nps-extension.md) 가 배포 됩니다. |
| 사용자에 게 Microsoft Office 2010 이전 또는 iOS 11 이전 Apple 메일 | [Microsoft Office 2013 이상](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) 및 iOS 12 이상용 Apple 메일로 업그레이드 합니다. 레거시 인증 프로토콜은 조건부 액세스를 지원 하지 않습니다. |

## <a name="plan-user-rollout"></a>사용자 롤아웃 계획

MFA 출시 계획에는 파일럿 배포와 지원 용량 내에 있는 배포 전파가 포함 되어야 합니다. 작은 파일럿 사용자 그룹에 조건부 액세스 정책을 적용 하 여 롤아웃을 시작 합니다. 파일럿 사용자, 사용 된 프로세스 및 등록 동작에 미치는 영향을 평가한 후 정책에 그룹을 더 추가 하거나 기존 그룹에 사용자를 더 추가할 수 있습니다.

### <a name="user-communications"></a>사용자 통신

계획 된 통신, 예정 된 변경 내용, Azure MFA 등록 요구 사항 및 필요한 모든 사용자 작업에 대해 사용자에 게 알리는 것이 중요 합니다. 통신, 변경 관리 또는 인적 자원 부서와 같은 조직 내 담당자와 함께 통신을 개발 하는 것이 좋습니다.

Microsoft는 통신을 간단 하 게 작성할 수 있도록 [통신 템플릿과](https://aka.ms/mfatemplates) [최종 사용자 설명서](../user-help/security-info-setup-signin.md) 를 제공 합니다. 해당 페이지에서 **보안 정보** 링크를 선택 하 여 [https://myprofile.microsoft.com](https://myprofile.microsoft.com) 사용자에 게 직접 등록할 수 있습니다.

## <a name="deployment-considerations"></a>배포 고려 사항

Azure Multi-factor Authentication은 조건부 액세스로 정책을 적용 하 여 배포 됩니다. [조건부 액세스 정책을](../conditional-access/overview.md) 사용 하 여 다음과 같은 특정 조건이 충족 될 때 사용자가 multi-factor authentication을 수행 하도록 요구할 수 있습니다.

* 모든 사용자, 특정 사용자, 그룹 구성원 또는 할당 된 역할
* 액세스 되는 특정 클라우드 응용 프로그램
* 디바이스 플랫폼
* 장치 상태
* 네트워크 위치 또는 지리적으로 배치 되는 IP 주소
* 클라이언트 애플리케이션
* 로그인 위험 (Id 보호 필요)
* 규정 준수 디바이스
* 하이브리드 Azure AD 조인 디바이스
* 승인 된 클라이언트 응용 프로그램

Multi-factor [authentication 출시 자료](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) 에서 사용자 지정 가능한 포스터 및 전자 메일 템플릿을 사용 하 여 조직에 multi-factor authentication을 롤아웃할 수 있습니다.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>조건부 액세스를 사용하여 Multi-Factor Authentication 사용

조건부 액세스 정책은 등록을 강제 적용 하 여 처음 로그인 할 때 등록을 완료 해야 하는 중요 한 보안 고려 사항입니다.

[Azure AD ID 보호](../identity-protection/howto-configure-risk-policies.md) 는 Azure Multi-Factor Authentication 스토리에 대 한 등록 정책과 자동화 된 위험 검색 및 수정 정책을 모두 제공 합니다. 다음 [이벤트](../reports-monitoring/concept-risk-events.md)에 의해 로그인이 위험한 것으로 간주 되는 경우, 손상 된 id의 위협이 발생 하거나 MFA를 요구 하는 경우 암호 변경을 강제로 적용 하도록 정책을 만들 수 있습니다.

* 유출된 자격 증명
* 익명 IP 주소에서 로그인
* 비정상적 위치로 불가능한 이동
* 알 수 없는 위치에서 로그인
* 감염된 디바이스에서 로그인
* 의심 스러운 활동을 포함 하는 IP 주소에서의 로그인

Azure Active Directory Identity Protection에서 검색 되는 일부 위험 검색은 실시간으로 발생 하 고 일부는 오프 라인 처리가 필요 합니다. 관리자는 위험한 동작을 수행 하는 사용자를 차단 하거나 수동으로 재구성 하거나, 암호 변경을 요구 하거나, 조건부 액세스 정책의 일부로 multi-factor authentication을 요구 하도록 선택할 수 있습니다.

## <a name="define-network-locations"></a>네트워크 위치 정의

조직에서는 [명명 된 위치](../conditional-access/location-condition.md#named-locations)를 사용 하 여 네트워크를 정의 하는 조건부 액세스를 사용 하는 것이 좋습니다. 조직에서 Id 보호를 사용 하는 경우 명명 된 위치 대신 위험 기반 정책을 사용 하는 것이 좋습니다.

### <a name="configuring-a-named-location"></a>명명 된 위치 구성

1. Azure Portal에서 **Azure Active Directory** 를 엽니다.
2. **보안** 선택
3. **관리**아래에서 **명명 된 위치** 를 선택 합니다.
4. **새 위치** 선택
5. **이름** 필드에 의미 있는 이름을 입력 합니다.
6. *IP 범위* 또는 *국가/지역을* 사용 하 여 위치를 정의 하는지 여부를 선택 합니다.
   1. *IP 범위* 를 사용 하는 경우
      1. *신뢰할 수 있는 위치로*표시할지 여부를 결정 합니다. 신뢰할 수 있는 위치에서 로그인하면 사용자의 로그인 위험이 줄어듭니다. 입력 한 IP 범위를 조직에 설정 하 고 신뢰할 수 있는 하는 경우에만이 위치를 신뢰할 수 있는 것으로 표시 합니다.
      2. IP 범위 지정
   2. *국가/지역을* 사용 하는 경우
      1. 드롭다운 메뉴를 확장 하 고이 명명 된 위치에 대해 정의 하려는 국가 또는 지역을 선택 합니다.
      2. *알 수 없는 영역을 포함할지*여부를 결정 합니다. 알 수 없는 영역은 국가/지역에 매핑할 수 없는 IP 주소입니다.
7. **만들기**

## <a name="plan-authentication-methods"></a>인증 방법 계획

관리자는 사용자가 사용할 수 있도록 설정할 [인증 방법을](../authentication/concept-authentication-methods.md) 선택할 수 있습니다. 기본 메서드를 사용할 수 없는 경우 사용자에 게 백업 메서드를 사용할 수 있도록 단일 인증 방법을 허용 하는 것이 중요 합니다. 관리자는 다음 방법을 사용 하 여를 설정할 수 있습니다.

### <a name="notification-through-mobile-app"></a>모바일 앱을 통한 알림

푸시 알림은 모바일 장치의 Microsoft Authenticator 앱에 전송 됩니다. 사용자는 알림을 확인 하 고 **승인** 을 선택 하 여 확인을 완료 합니다. 모바일 앱을 통한 푸시 알림은 사용자에 게 가장 방해가 되지 않는 옵션을 제공 합니다. 이러한 옵션은 전화 통신 대신 데이터 연결을 사용 하기 때문에 가장 안정적이 고 안전한 옵션 이기도 합니다.

> [!NOTE]
> 조직에서 근무 하는 직원이 있거나 중국으로 여행 하는 경우 **Android 장치** 에서 **모바일 앱 방법을 통한 알림이** 해당 국가에서 작동 하지 않습니다. 이러한 사용자에 대해 다른 방법을 사용할 수 있습니다.

### <a name="verification-code-from-mobile-app"></a>모바일 앱의 확인 코드

Microsoft Authenticator 앱과 같은 모바일 앱은 30 초 마다 새 OATH 확인 코드를 생성 합니다. 사용자는 로그인 인터페이스에 확인 코드를 입력합니다. 휴대폰에 데이터 또는 셀룰러 신호가 있는지 여부에 관계 없이 모바일 앱 옵션을 사용할 수 있습니다.

### <a name="call-to-phone"></a>휴대폰에 전화 걸기

자동 음성 통화가 사용자에 게 배치 됩니다. 사용자는 전화를 받고 전화 키패드에서 **#** 를 눌러 인증을 승인 합니다. 휴대폰에 대 한 호출은 모바일 앱에서 알림 또는 확인 코드를 위한 좋은 백업 방법입니다.

### <a name="text-message-to-phone"></a>휴대폰에 문자 메시지 전송

사용자에 게 확인 코드가 포함 된 문자 메시지가 전송 되 면 사용자에 게 로그인 인터페이스에 확인 코드를 입력 하 라는 메시지가 표시 됩니다.

### <a name="choose-verification-options"></a>확인 옵션 선택

1. **Azure Active Directory**, **사용자**, **Multi-Factor Authentication**으로 이동합니다.

   ![Azure Portal의 Azure AD 사용자 블레이드에서 Multi-Factor Authentication 포털에 액세스](media/howto-mfa-getstarted/users-mfa.png)

1. 열리는 새 탭에서 **서비스 설정**으로 이동합니다.
1. **확인 옵션** 아래에서, 사용자에게 제공할 방법의 모든 확인란을 선택합니다.

   ![Multi-Factor Authentication 서비스 설정 탭에서 인증 방법 구성](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. **Save**를 클릭합니다.
1. **서비스 설정** 탭을 닫습니다.

## <a name="plan-registration-policy"></a>등록 정책 계획

관리자는 사용자가 메서드를 등록 하는 방법을 결정 해야 합니다. 조직은 Azure MFA 및 SSPR (셀프 서비스 암호 재설정)에 대해 [새롭게 결합 된 등록 환경을 사용 하도록 설정](howto-registration-mfa-sspr-combined.md) 해야 합니다. SSPR를 사용 하면 사용자가 multi-factor authentication에 사용 하는 것과 동일한 방법으로 암호를 안전 하 게 재설정할 수 있습니다. 사용자가 두 서비스에 대해 한 번 등록 하는 기능을 제공 하므로 현재 공개 미리 보기로 제공 되는이 결합 된 등록을 사용 하는 것이 좋습니다. SSPR 및 Azure MFA에 대해 동일한 메서드를 사용 하도록 설정 하면 사용자가 두 기능을 모두 사용 하도록 등록할 수 있습니다.

### <a name="registration-with-identity-protection"></a>Id 보호를 사용 하 여 등록

조직에서 Azure Active Directory Identity Protection를 사용 하는 경우 다음에 대화형으로 로그인 할 때 사용자에 게 등록 하 라는 메시지를 표시 하도록 [MFA 등록 정책을 구성](../identity-protection/howto-mfa-policy.md) 합니다.

### <a name="registration-without-identity-protection"></a>Id 보호를 사용 하지 않는 등록

조직에 Id 보호를 사용 하는 라이선스가 없는 경우 로그인 시 다음에 MFA를 요구 하는 경우 사용자에 게 등록 하 라는 메시지가 표시 됩니다. 사용자가 MFA로 보호 된 응용 프로그램을 사용 하지 않는 경우 MFA에 등록 되지 않을 수 있습니다. 잘못 된 행위자가 사용자의 암호를 추측 하 고 사용자를 대신 하 여 MFA에 등록 하 여 계정에 대 한 제어를 효과적으로 수행 하도록 등록 된 모든 사용자를 가져오는 것이 중요 합니다.

#### <a name="enforcing-registration"></a>등록 적용

다음 단계를 사용 하 여 조건부 액세스 정책을 통해 사용자가 Multi-Factor Authentication에 등록 하도록 할 수 있습니다.

1. 그룹을 만들고 현재 등록 되지 않은 모든 사용자를 추가 합니다.
2. 조건부 액세스를 사용 하 여 모든 리소스에 액세스할 수 있도록이 그룹에 대해 multi-factor authentication을 적용 합니다.
3. 정기적으로 그룹 멤버 자격을 다시 평가 하 고 그룹에서 등록 한 사용자를 제거 합니다.

[Msonline powershell 모듈](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)을 사용 하는 powershell 명령을 사용 하 여 등록 및 등록 되지 않은 Azure MFA 사용자를 식별할 수 있습니다.

#### <a name="identify-registered-users"></a>등록 된 사용자 식별

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>등록 되지 않은 사용자 식별

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자 단위 MFA의 사용자를 조건부 액세스 기반 MFA로 변환

사용자 단위 사용을 사용 하도록 설정 하 고 Azure Multi-Factor Authentication 적용 한 사용자의 경우 다음 PowerShell을 사용 하 여 Azure Multi-Factor Authentication를 기반으로 조건부 액세스를 변환 하는 데 도움을 받을 수 있습니다.

ISE 창에서이 PowerShell을 실행 하거나로 저장 합니다. 로컬로 실행할 PS1 파일입니다.

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
> 최근 그에 따라 위의 동작 및 PowerShell 스크립트를 변경 했습니다. 이전에는 스크립트에서 MFA 메서드를 사용 하지 않도록 설정 하 고 MFA를 사용 하지 않도록 설정 하 고 메서드를 복원 했습니다. 더 이상 사용 하지 않도록 설정 하는 기본 동작이 메서드를 지우지 않으므로 이제는이 작업이 필요 하지 않습니다.

## <a name="plan-conditional-access-policies"></a>조건부 액세스 정책 계획

MFA 및 기타 컨트롤이 필요한 시기를 결정 하는 조건부 액세스 정책 전략을 계획 하려면 [Azure Active Directory의 조건부 액세스 란?](../conditional-access/overview.md)을 참조 하세요.

Azure AD 테넌트에서 실수로 잠기는 것을 방지 하는 것이 중요 합니다. [테넌트에 두 개 이상의 응급 액세스 계정을 만들고](../users-groups-roles/directory-emergency-access.md) 조건부 액세스 정책에서 제외 하 여 실수로 관리 액세스가 누락 되는 경우의 영향을 완화할 수 있습니다.

### <a name="create-conditional-access-policy"></a>조건부 액세스 정책 만들기

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **조건부 액세스** >  > **보안** **Azure Active Directory** 로 이동 합니다.
1. **새 정책**을 선택합니다.
   ![하 여 파일럿 그룹의 Azure Portal 사용자에 대해 MFA를 사용 하도록 설정 하는 조건부 액세스 정책을 만듭니다](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 의미 있는 정책 이름을 입력합니다.
1. **사용자 및 그룹** 아래에서:
   * **포함** 탭에서 **모든 사용자** 라디오 단추를 선택합니다.
   * **제외** 탭에서 **사용자 및 그룹** 의 확인란을 선택 하 고 응급 액세스 계정을 선택 합니다.
   * **Done**을 클릭합니다.
1. **클라우드 앱**에서 **모든 클라우드 앱** 라디오 단추를 선택합니다.
   * 선택 사항: **제외** 탭에서, 조직에서 MFA를 요구하지 않는 클라우드 앱을 선택합니다.
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

## <a name="plan-integration-with-on-premises-systems"></a>온-프레미스 시스템과의 통합 계획

Azure AD에 대해 직접 인증 하지 않는 일부 레거시 및 온-프레미스 응용 프로그램에는 다음을 포함 하 여 MFA를 사용 하는 추가 단계가 필요 합니다.

* 응용 프로그램 프록시를 사용 해야 하는 레거시 온-프레미스 응용 프로그램
* NPS 서버에서 MFA 어댑터를 사용 해야 하는 온-프레미스 RADIUS 응용 프로그램
* 온-프레미스 AD FS 응용 프로그램은 AD FS 2016 이상에서 MFA 어댑터를 사용 해야 합니다.

Azure AD에서 직접 인증 하 고 최신 인증 (WS 급지됨, SAML, OAuth, Openid connect Connect)을 보유 하는 응용 프로그램은 조건부 액세스 정책을 직접 사용할 수 있습니다.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시에서 Azure MFA 사용

온-프레미스에 상주 하는 응용 프로그램은 azure [AD 응용 프로그램 프록시](../manage-apps/application-proxy.md) 를 통해 azure ad 테넌트에 게시 될 수 있으며 azure ad 사전 인증을 사용 하도록 구성 된 경우 azure Multi-Factor Authentication를 활용할 수 있습니다.

이러한 응용 프로그램에는 다른 Azure AD 통합 응용 프로그램과 마찬가지로 Azure Multi-Factor Authentication를 적용 하는 조건부 액세스 정책이 적용 됩니다.

마찬가지로 Azure Multi-Factor Authentication 모든 사용자 로그인에 적용 되는 경우 Azure AD 응용 프로그램 프록시로 게시 된 온-프레미스 응용 프로그램은 보호 됩니다.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>네트워크 정책 서버와 Azure Multi-Factor Authentication 통합

Azure MFA의 NPS(네트워크 정책 서버) 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 MFA 기능을 추가합니다. NPS 확장을 사용 하 여 전화 통화, 문자 메시지 또는 휴대폰 앱 확인을 기존 인증 흐름에 추가할 수 있습니다. 이 통합에는 다음과 같은 제한 사항이 있습니다.

* CHAPv2 프로토콜을 사용 하는 경우 인증자 앱 푸시 알림과 음성 호출만 지원 됩니다.
* 조건부 액세스 정책을 적용할 수 없습니다.

NPS 확장은 RADIUS 및 클라우드 기반 Azure MFA 사이에서 어댑터 역할을 하 여 [VPN](howto-mfa-nps-extension-vpn.md), [원격 데스크톱 게이트웨이 연결](howto-mfa-nps-extension-rdg.md)또는 기타 RADIUS 지원 응용 프로그램을 보호 하는 두 번째 인증 단계를 제공 합니다. 이 환경에서 Azure MFA에 등록 하는 사용자는 모든 인증 시도에 문제가 될 수 있으므로, 조건부 액세스 정책이 없으면 MFA가 항상 필요 합니다.

#### <a name="implementing-your-nps-server"></a>NPS 서버 구현

NPS 인스턴스를 배포 하 여 이미 사용 중인 경우 [기존 Nps 인프라를 Azure Multi-Factor Authentication와 통합](howto-mfa-nps-extension.md)을 참조 하세요. NPS를 처음 설정 하는 경우에 대 한 자세한 내용은 [nps (네트워크 정책 서버)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) 를 참조 하세요. 문제 해결 지침은 [Azure Multi-Factor Authentication에 대 한 NPS 확장에서 오류 메시지 해결](howto-mfa-nps-extension-errors.md)문서에서 찾을 수 있습니다.

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>MFA에 등록 되지 않은 사용자를 위한 NPS 준비

MFA에 등록 되지 않은 사용자가 인증을 시도 하는 경우 수행할 작업을 선택 합니다. 레지스트리 경로 `HKLM\Software\Microsoft\AzureMFA` `REQUIRE_USER_MATCH` 레지스트리 설정을 사용 하 여 기능 동작을 제어 합니다. 이 설정에는 단일 구성 옵션이 있습니다.

| 키 | 값 | 기본값 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE/FALSE | 설정되지 않음(TRUE와 동일) |

이 설정은 사용자가 MFA에 등록되지 않은 경우 수행할 작업을 결정하기 위한 것입니다. 이 설정이 변경 되는 영향은 아래 표에 나와 있습니다.

| 설정 | 사용자 MFA 상태 | 효과 |
| --- | --- | --- |
| 키가 없습니다. | 등록 되지 않음 | MFA 챌린지 실패 |
| True/설정 안 함으로 설정 되는 값 | 등록 되지 않음 | MFA 챌린지 실패 |
| 키가 False로 설정 되었습니다. | 등록 되지 않음 | MFA 없는 인증 |
| False 또는 True로 설정 되는 키 | 등록됨 | MFA를 사용 하 여 인증 해야 합니다. |

### <a name="integrate-with-active-directory-federation-services"></a>Active Directory Federation Services와 통합

조직이 Azure AD와 페더레이션 되는 경우 Azure Multi-Factor Authentication를 사용 하 여 온-프레미스와 클라우드에서 [AD FS 리소스를 보호할](multi-factor-authentication-get-started-adfs.md)수 있습니다. Azure MFA를 사용 하 여 암호를 줄이고 보다 안전한 인증 방법을 제공할 수 있습니다. Windows Server 2016 부터는 기본 인증을 위해 Azure MFA를 지금 구성할 수 있습니다.

Windows Server 2012 r 2의 AD FS와 달리 AD FS 2016 Azure MFA 어댑터는 Azure AD와 직접 통합 되며 온-프레미스 Azure MFA 서버가 필요 하지 않습니다. Azure MFA 어댑터는 Windows Server 2016에 기본 제공 되며 추가 설치는 필요 하지 않습니다.

AD FS 2016와 함께 Azure MFA를 사용 하 고 대상 응용 프로그램에 조건부 액세스 정책이 적용 되는 경우 추가로 고려해 야 할 사항이 있습니다.

* 응용 프로그램이 Azure AD에 대 한 신뢰 당사자이 고 AD FS 2016 이상 페더레이션 된 경우 조건부 액세스를 사용할 수 있습니다.
* 응용 프로그램이 2016 또는 AD FS 2019 AD FS에 대 한 신뢰 당사자 이며 관리 되거나 AD FS 2016 또는 AD FS 2019와 페더레이션 된 경우 조건부 액세스를 사용할 수 없습니다.
* AD FS 2016 또는 AD FS 2019이 기본 인증 방법으로 Azure MFA를 사용 하도록 구성 된 경우에도 조건부 액세스를 사용할 수 없습니다.

#### <a name="ad-fs-logging"></a>AD FS 로깅

Windows 보안 로그 및 AD FS 관리자 로그에 표준 AD FS 2016 및 2019 로깅은 인증 요청 및 해당 성공 또는 실패에 대 한 정보를 포함 합니다. 이러한 이벤트 내의 이벤트 로그 데이터는 Azure MFA가 사용 되었는지 여부를 나타냅니다. 예를 들어 AD FS 감사 이벤트 ID 1200에는 다음이 포함 될 수 있습니다.

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>인증서 갱신 및 관리

각 AD FS 서버에서 로컬 컴퓨터 내 저장소에는 인증서 만료 날짜를 포함 하는 OU = Microsoft AD FS Azure MFA 라는 자체 서명 된 Azure MFA 인증서가 있습니다. 각 AD FS 서버에서이 인증서의 유효 기간을 확인 하 여 만료 날짜를 확인 합니다.

인증서의 유효 기간이 곧 만료 되는 경우 [각 AD FS 서버에서 새 MFA 인증서를 생성 하 고 확인](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)합니다.

다음 지침에서는 AD FS 서버에서 Azure MFA 인증서를 관리 하는 방법에 대해 자세히 설명 합니다. Azure MFA를 사용 하 여 AD FS를 구성 하는 경우 `New-AdfsAzureMfaTenantCertificate` PowerShell cmdlet을 통해 생성 된 인증서는 2 년 동안 유효 합니다. MFA 서비스의 ovoid 중단에 만료 되기 전에 갱신 된 인증서를 갱신 하 고 설치 합니다.

## <a name="implement-your-plan"></a>요금제 구현

이제 솔루션을 계획 했으므로 다음 단계를 수행 하 여를 구현할 수 있습니다.

1. 필요한 모든 필수 구성 요소 충족
   1. 모든 하이브리드 시나리오에 대 한 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 배포
   1. 클라우드 액세스를 위해 게시 된 온-프레미스 앱에 대 한 [Azure AD 응용 프로그램 프록시](../manage-apps/application-proxy.md) 배포
   1. RADIUS 인증을 위한 [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) 배포
   1. 사용자가 최신 인증을 사용 하도록 설정 된 Microsoft Office 지원 되는 버전으로 업그레이드 했는지 확인 합니다.
1. 선택한 [인증 방법](#choose-verification-options) 구성
1. [명명 된 네트워크 위치](../conditional-access/location-condition.md#named-locations) 정의
1. MFA를 배포 하기 시작 하려면 그룹을 선택 합니다.
1. [조건부 액세스 정책](#create-conditional-access-policy) 구성
1. MFA 등록 정책 구성
   1. [결합 된 MFA 및 SSPR](howto-registration-mfa-sspr-combined.md)
   1. [Id 보호](../identity-protection/howto-mfa-policy.md) 사용
1. 사용자 통신을 보내고 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 에서 등록할 수 있도록 사용자 가져오기
1. [등록 된 사용자를 추적 합니다.](#identify-non-registered-users)

> [!TIP]
> 정부 클라우드 사용자는 [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup) 에서 등록할 수 있습니다.

## <a name="manage-your-solution"></a>솔루션 관리

Azure MFA에 대 한 보고서

Azure Multi-Factor Authentication는 Azure Portal를 통해 보고서를 제공 합니다.

| 보고서 | 위치 | Description |
| --- | --- | --- |
| 사용량 및 사기 행위 경고 | Azure AD > 로그인 | 지정된 날짜 범위 동안 제출된 사기 행위 경고의 기록을 비롯한 전체 사용량, 사용자 요약 및 사용자 세부 정보에 대한 정보를 제공합니다. |

## <a name="troubleshoot-mfa-issues"></a>MFA 문제 해결

Microsoft 지원 센터의 [azure Multi-Factor Authentication 문제 해결 문서](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) 에서 azure MFA와 관련 된 일반적인 문제에 대 한 솔루션을 찾습니다.

## <a name="next-steps"></a>다음 단계

* [인증 방법이란?](concept-authentication-methods.md)
* [Azure Multi-Factor Authentication 및 Azure AD 셀프 서비스 암호 재설정에 융합 등록 사용](concept-registration-mfa-sspr-converged.md)
* 사용자에게 MFA를 수행하라는 메시지가 표시되거나 표시되지 않는 이유는? [Azure Multi-Factor Authentication 문서에서 보고서의 Azure AD 로그인 보고서](howto-mfa-reporting.md#azure-ad-sign-ins-report) 섹션을 참조하세요.
