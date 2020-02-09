---
title: Azure Active Directory 조건부 액세스의 조건 이란? | Microsoft Docs
description: Azure Active Directory 조건부 액세스에서 정책을 트리거할 수 조건을 사용 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e6c18b7ffca83c8e0fe9576ec275f89b6db7b4f
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509005"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 조건 이란?

사용자를 사용 하 여 클라우드 앱에 액세스 하는 방법을 제어할 수 있습니다 [Azure Active Directory (Azure AD) 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)합니다. 조건부 액세스 정책을 응답 정의 ("Then do this") ("When this happens") 정책을 트리거하는 이유를 합니다.

![이유 및 응답](./media/conditions/10.png)

조건부 액세스의 컨텍스트에서 **이 경우** 호출 되는 **조건**합니다. **When this happens(이 경우)** 를 **액세스 제어**라 합니다. 조건 및 액세스 제어의 조합이 조건부 액세스 정책을 나타냅니다.

![조건부 액세스 정책](./media/conditions/61.png)

조건부 액세스 정책에서 구성 하지 않은 조건이 적용 되지 않습니다. 일부 조건은 [필수](best-practices.md) 환경에 조건부 액세스 정책을 적용 합니다.

이 문서는 조건 및 조건부 액세스 정책에서 사용 하는 방법의 개요입니다. 

## <a name="users-and-groups"></a>개요

사용자 및 그룹 조건은 조건부 액세스 정책에서 필수입니다. 정책에서 **모든 사용자**를 선택할 수도 있고 특정 사용자 및 그룹을 선택할 수도 있습니다.

![개요](./media/conditions/111.png)

**모든 사용자**를 선택하면 게스트 사용자를 포함하여 디렉터리에 있는 모든 사용자에게 정책이 적용됩니다.

**사용자 및 그룹**을 선택하면 다음 옵션을 설정할 수 있습니다.

* **모든 게스트 사용자**는 정책의 대상을 B2B 게스트 사용자로 합니다. 이 조건은 **userType** 특성이 **guest**로 설정된 모든 사용자 계정과 일치합니다. Azure AD의 초대 흐름에 계정이 생성되는 즉시 정책을 적용해야 하는 경우 이 설정을 사용합니다.
* **디렉터리 역할**은 사용자의 역할 할당을 기반으로 정책을 대상으로 합니다. 이 조건은 **전역 관리자** 또는 **암호 관리자**와 같은 디렉터리 역할을 지원합니다.
* **사용자 및 그룹**은 특정 사용자 집합을 대상으로 합니다. 예를 들어 클라우드 앱으로 선택된 HR 앱의 경우 HR 부서의 모든 구성원을 포함하는 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

정책에서 특정 사용자 또는 그룹을 제외할 수도 있습니다. 정책에서 다단계 인증(MFA)을 적용하는 경우의 일반적인 사용 사례 중 하나는 서비스 계정입니다.

새 정책을 배포할 때에는 특정 사용자 집합을 대상으로 지정하는 것이 유리합니다. 새 정책에서 정책 동작의 유효성을 검사하기 위해 초기 사용자 집합만 대상으로 지정해야 합니다.

## <a name="cloud-apps-and-actions"></a>클라우드 앱 및 작업

클라우드 앱에는 웹 사이트, 서비스 또는 Azure AD 응용 프로그램 프록시에 의해 보호 되는 엔드포인트은입니다. 지원되는 클라우드 앱에 대한 자세한 설명은 [클라우드 앱 할당](technical-reference.md#cloud-apps-assignments)을 참조하세요. 합니다 **클라우드 앱 또는 작업** 조건은 조건부 액세스 정책에서 필수입니다. 정책에서 선택할 수도 있습니다 **모든 클라우드 앱** 사용 하 여 앱을 지정 하거나 **앱 선택**합니다.

조직은 다음 중에서 선택할 수 있습니다.

* **모든 클라우드 앱** 조직 전체에 적용 하는 기준 정책을 적용 하는 경우. 모든 클라우드 앱에 대 한 로그인 위험이 감지 되 면 multi-factor authentication을 요구 하는 정책에 대 한이 선택을 사용 합니다. 모든 클라우드 앱에 적용 되는 정책이 액세스를 적용할 모든 웹 사이트 및 서비스에 있습니다. 이 설정을 선택 하는 앱 목록에 표시 되는 클라우드 앱 제한 되지 않습니다.
* 정책에 의해 특정 서비스를 대상으로 지정하려면 **앱 선택**을 선택합니다. 예를 들어 사용자가 SharePoint Online에 액세스 하려면 준수 장치가 요구할 수 있습니다. 이 정책은 사용자가 SharePoint 콘텐츠 팀에 액세스할 때 다른 서비스에도 적용됩니다. Microsoft Teams를 예로 들 수 있습니다.

> [!NOTE]
> 특정 앱을 정책에서 제외할 수 있습니다. 그러나 사용자가 액세스하는 서비스에 적용되는 정책이 계속 적용됩니다.

**사용자 작업** 은 사용자가 수행할 수 있는 작업입니다. 현재 지원 되는 작업만 **보안 정보 (미리 보기)를 등록**, 조건부 액세스 정책이 결합 된 등록에 대해 설정 된 사용자의 보안을 등록 하려고 하는 경우 적용할 수 있는 정보입니다. 자세한 내용은 문서에서 찾을 수 있습니다 [보안 정보 등록 (미리 보기)를 결합 하는 사용](../authentication/howto-registration-mfa-sspr-combined.md)합니다.

## <a name="sign-in-risk"></a>로그인 위험

로그인 위험은 로그인을 사용자 계정의 합법적인 소유자가 수행하지 않았을 가능성의 지표(높음, 중간 또는 낮음)입니다. Azure AD는 사용자 로그인 중에 로그인 위험 수준을 계산합니다. 계산 된 로그인 위험 수준을 조건부 액세스 정책에서 조건으로 사용할 수 있습니다.

![로그인 위험 수준](./media/conditions/22.png)

이 조건을 사용하려면 [Azure Active Directory Id 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable)를 사용하도록 설정해야 합니다.
 
이 조건의 일반적인 사용 사례는 다음 보호가 적용된 정책입니다. 

- 로그인 위험이 높은 사용자를 차단합니다. 이 보호는 비합적일 수 있는 사용자가 클라우드 앱에 액세스하지 못하게 차단합니다. 
- 로그인 위험 수준이 보통인 사용자에게 다단계 인증을 요구합니다. 다단계 인증을 사용하면 로그인이 합법적 계정 소유자에 의해 수행된다는 추가 신뢰를 제공할 수 있습니다.

자세한 내용은 [세션 위험이 감지되면 액세스 차단](app-sign-in-risk.md)을 참조하세요.  

## <a name="device-platforms"></a>디바이스 플랫폼

디바이스 플랫폼은 다음 디바이스에서 실행되는 운영 체제를 특징으로 합니다. Azure AD는 디바이스에서 제공하는 정보(예: 사용자 에이전트)를 사용하여 플랫폼을 식별합니다. 이 정보는 확인되지 않습니다. 모든 플랫폼에 정책을 적용하는 것이 좋습니다. 정책은 액세스를 차단하거나, Microsoft Intune 정책 준수를 요구하거나, 디바이스 도메인 가입을 요구해야 합니다. 기본값은 모든 디바이스 플랫폼에 정책을 적용하는 것입니다. 

![디바이스 옵션 구성](./media/conditions/24.png)

지원되는 디바이스 플랫폼 목록은 [디바이스 플랫폼 조건](technical-reference.md#device-platform-condition)을 참조하세요.

이 조건의 일반적인 사용 사례는 클라우드 앱에 대한 액세스를 [관리 디바이스](require-managed-devices.md)로 제한하는 정책입니다. 장치 플랫폼 조건을 포함 하 여 더 많은 시나리오를 참조 하세요 [Azure Active Directory 앱 기반 조건부 액세스](app-based-conditional-access.md)합니다.

## <a name="device-state"></a>디바이스 상태

장치 상태 조건을 하이브리드 Azure AD 조인 장치 및 조건부 액세스 정책에서 규정 준수로 표시 된 장치를 제외 합니다. 

![디바이스 상태 구성](./media/conditions/112.png)

이 조건은 추가적인 세션 보안을 제공하기 위해 관리되지 않는 디바이스에만 정책을 적용하는 경우에 유용합니다. 예를 들어 디바이스가 관리되지 않는 경우 Microsoft Cloud App Security 세션 제어만 적용합니다. 

## <a name="locations"></a>위치

위치를 사용하여 연결이 시도된 위치를 기준으로 조건을 정의할 수 있습니다. 

![위치 구성](./media/conditions/25.png)

이 조건의 일반적인 사용 사례는 다음 보호가 적용된 정책입니다.

- 회사 네트워크 외부에 있을 때 서비스에 액세스 하는 사용자에 대 한 multi-factor authentication을 요구 합니다.  
- 특정 국가 또는 지역에서 서비스에 액세스하는 사용자를 차단합니다. 

자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건 이란?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)합니다.

## <a name="client-apps"></a>클라이언트 앱

기본적으로 조건부 액세스 정책은 다음 앱에 적용 됩니다.

- **[브라우저 앱](technical-reference.md#supported-browsers)** - 브라우저 앱에는 SAML, WS-Federation 또는 OpenID Connect 웹 SSO 프로토콜을 사용하는 웹 사이트가 포함됩니다. 이는 OAuth 기밀 클라이언트로 등록된 모든 웹 사이트 또는 웹 서비스에도 적용됩니다. 예를 들어 Office 365 SharePoint 웹 사이트가 있습니다. 
- **[최신 인증을 사용하는 모바일 및 데스크톱 앱](technical-reference.md#supported-mobile-applications-and-desktop-clients)** - 이러한 앱에는 Office 데스크톱 앱과 휴대폰 앱이 포함됩니다. 


또한 예를 들어 최신 인증을 사용하지 않는 특정 클라이언트 앱에 대해 정책을 대상으로 지정할 수 있습니다.

- **[Exchange ActiveSync 클라이언트](conditions.md#exchange-activesync-clients)** - 정책에 따라 Exchange ActiveSync 사용이 차단되면 영향을 받는 사용자는 차단된 이유에 대한 정보가 포함된 격리 이메일을 하나 받습니다. 필요한 경우 이메일에는 디바이스를 Intune에 등록하는 방법에 대한 지침이 포함됩니다.
- **[다른 클라이언트](block-legacy-authentication.md)** - 이러한 앱에는 IMAP, MAPI, POP, SMTP 및 최신 인증을 사용하지 않는 이전 Office 앱과 같은 메일 프로토콜과 함께 기본 인증을 사용하는 클라이언트가 포함됩니다. 자세한 내용은 [Office 2013 및 Office 2016 클라이언트 앱에 대한 최신 인증 작동 방식](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016)을 참조하세요.

![클라이언트 앱](./media/conditions/41.png)

이 조건의 일반적인 사용 사례는 다음 요구 사항이 적용된 정책입니다.

- 디바이스에 데이터를 다운로드하는 모바일 및 데스크톱 애플리케이션에 대해 **[관리 디바이스를 요구](require-managed-devices.md)** 합니다. 동시에 모든 디바이스에서 브라우저 액세스를 허용합니다. 이 시나리오는 관리되지 않는 디바이스에 문서를 저장하고 동기화하지 못하도록 합니다. 이 방법으로 디바이스를 분실하거나 도난당한 경우 데이터 손실 가능성을 줄일 수 있습니다.
- ActiveSync를 사용하여 Exchange Online에 액세스하는 앱에 대해 **[관리 디바이스를 요구](require-managed-devices.md)** 합니다.
- Azure AD(기타 클라이언트)에 대해 **[레거시 인증을 차단](block-legacy-authentication.md)** 합니다.
- 웹 애플리케이션 액세스를 차단하지만, 모바일 및 데스크톱 애플리케이션 액세스는 허용합니다.

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 클라이언트

다음과 같은 경우 **Exchange ActiveSync 클라이언트만** 선택할 수 있습니다.

- Microsoft Office 365 Exchange Online은 선택한 유일한 클라우드 앱입니다.

    ![클라우드 앱](./media/conditions/32.png)

- 정책에 다른 조건이 구성되어 있지 않습니다. 그러나 [지원되는 플랫폼](technical-reference.md#device-platform-condition)에만 적용하도록 이 조건의 범위를 좁힐 수 있습니다.
 
    ![지원되는 플랫폼에만 정책 적용](./media/conditions/33.png)

[관리 디바이스](require-managed-devices.md)가 필요해서 액세스가 차단된 경우 영향을 받는 사용자는 Intune을 사용하라고 안내해 주는 메일을 하나 받습니다. 

승인된 앱이 필요한 경우 영향을 받는 Outlook 모바일 클라이언트를 설치해서 사용하라는 지침을 받습니다.

다른 경우, 예를 들어 MFA가 필요한 경우 기본 인증을 사용하는 클라이언트가 MFA를 지원하지 않으므로 영향을 받는 사용자가 차단됩니다.

사용자 및 그룹에 대해서만 이 설정을 대상으로 지정할 수 있습니다. 게스트나 역할을 지원하지 않습니다. 게스트 또는 역할 상태를 구성 하는 경우 조건부 액세스 정책이 사용자에 게 적용 될 경우를 확인할 수 없으므로 모든 사용자가 차단 됩니다.

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 조건부 액세스를 위한 SharePoint Online 및 Exchange Online 설정](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)합니다.
- [Azure Active Directory 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)합니다. 

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성 하는 방법을 알아보려면 참조 [빠른 시작: Azure Active Directory 조건부 액세스를 사용 하 여 특정 앱에 대 한 mfa](app-based-mfa.md)합니다.
- 사용자 환경에 대 한 조건부 액세스 정책을 구성 하려면 참조는 [Azure Active Directory의 조건부 액세스 모범 사례](best-practices.md)합니다. 
