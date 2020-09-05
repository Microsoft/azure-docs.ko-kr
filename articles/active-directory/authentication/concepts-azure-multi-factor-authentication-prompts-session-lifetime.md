---
title: Azure Multi-Factor Authentication 프롬프트 및 세션 수명
description: Azure Multi-Factor Authentication을 사용 하 여 메시지를 재인증 하 고 세션 수명을 적용 하는 방법에 대 한 권장 구성에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0019f7d8195dc39127b992a31ebd8c33e55452f6
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179354"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>재인증 프롬프트를 최적화 하 고 Azure Multi-Factor Authentication의 세션 수명 이해

Azure AD (Azure Active Directory)에는 사용자가 다시 인증 해야 하는 빈도를 결정 하는 여러 설정이 있습니다. 이러한 재인증은 암호, FIDO 또는 암호 없는 Microsoft Authenticator 같은 첫 번째 요소를 사용 하거나 MFA (multi-factor authentication)를 수행할 수 있습니다. 사용자 환경 및 원하는 사용자 환경에 맞게 필요에 따라 이러한 재인증 설정을 구성할 수 있습니다.

사용자 로그인 빈도에 대 한 Azure AD 기본 구성은 90 일의 롤링 기간입니다. 사용자에 게 자격 증명을 요청 하는 것이 중요 한 것 처럼 보일 수 있지만,이 경우에는 문제가 발생 합니다. 사용자가 생각 하지 않고 자격 증명을 입력 하도록 학습 된 경우 실수로 악의적인 자격 증명 프롬프트에 해당 자격 증명을 제공할 수 있습니다.

IT 정책 위반으로 인해 세션이 취소 되는 경우 사용자에 게 다시 로그인 하 라는 메시지가 표시 되는 것을 오류가 심각한 증가 수 있습니다. 몇 가지 예로는 암호 변경, incompliant 장치 또는 계정 비활성화 작업이 있습니다. [PowerShell을 사용 하 여 사용자 세션](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)을 명시적으로 해지할 수도 있습니다.

이 문서에서는 권장 되는 구성과 다양 한 설정이 작동 하 고 상호 작용 하는 방법에 대해 자세히 설명 합니다.

## <a name="recommended-settings"></a>권장 설정

사용자에 게 적절 한 빈도로 로그인 하도록 요청 하 여 적절 한 보안 및 사용 편의성을 제공 하기 위해 다음 구성을 사용 하는 것이 좋습니다.

* Azure AD Premium 있는 경우:
    * [관리 장치](../devices/overview.md) 또는 [원활한 SSO](../hybrid/how-to-connect-sso.md)를 사용 하 여 응용 프로그램에서 sso (Single Sign-On)를 사용 하도록 설정 합니다.
    * 재인증이 필요한 경우 조건부 액세스 [로그인 빈도 정책을](../conditional-access/howto-conditional-access-session-lifetime.md)사용 합니다.
    * 관리 되지 않는 장치 또는 모바일 장치 시나리오에서 로그인 하는 사용자의 경우 조건부 액세스를 사용 하 여 영구 브라우저 세션 및 로그인 빈도 정책을 사용 하도록 설정 합니다.
* Office 365 앱 라이선스 또는 무료 Azure AD 계층이 있는 경우:
    * [관리 장치](../devices/overview.md) 또는 [원활한 SSO](../hybrid/how-to-connect-sso.md)를 사용 하 여 응용 프로그램에서 sso (Single Sign-On)를 사용 하도록 설정 합니다.
    * 로그인 된 *상태로 유지* 옵션을 사용 하도록 설정 하 고 사용자에 게 동의 하도록 안내 합니다.
* 모바일 장치 시나리오의 경우 사용자가 Microsoft Authenticator 앱을 사용 하는지 확인 합니다. 이 앱은 다른 Azure AD 페더레이션된 앱에 대 한 브로커로 사용 되며, 장치에서 인증 프롬프트가 줄어듭니다.

Microsoft research는 이러한 설정이 대부분의 테 넌 트에 적합 하다는 것을 보여 줍니다. *MFA를 잊지* 않고 *singed로 유지*하는 등 이러한 설정의 일부 조합은 사용자가 너무 자주 인증 하도록 요청을 받을 수 있습니다. 일반 재인증 메시지는 사용자 생산성에 적합 하지 않으며 공격에 더 취약 해질 수 있습니다.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD 세션 수명 구성 설정

사용자에 대 한 인증 프롬프트의 빈도를 최적화 하기 위해 Azure AD 세션 수명 옵션을 구성할 수 있습니다. 비즈니스 및 사용자의 요구 사항을 이해 하 고 사용자 환경에 가장 적합 한 균형을 제공 하는 설정을 구성 합니다.

### <a name="evaluate-session-lifetime-policies"></a>세션 수명 정책 평가

세션 수명 설정이 없으면 브라우저 세션에 영구적인 쿠키가 없습니다. 사용자가 브라우저를 닫고 열 때마다 재인증에 대 한 프롬프트가 표시 됩니다. Office 클라이언트에서 기본 기간은 90 일의 롤링 기간입니다. 이 기본 Office 구성을 사용 하는 경우 사용자가 암호를 재설정 하거나 90 일 넘게 비활성 상태인 경우 사용자는 모든 필수 요소 (첫 번째 및 두 번째 요소)로 다시 인증 해야 합니다.

사용자에 게 Azure AD에서 id가 없는 장치에 여러 개의 MFA 프롬프트가 표시 될 수 있습니다. 각 응용 프로그램에 다른 클라이언트 앱과 공유 되지 않는 자체 OAuth 새로 고침 토큰이 있는 경우 여러 프롬프트가 표시 됩니다. 이 시나리오에서 mfa는 각 응용 프로그램이 MFA를 사용 하 여 유효성을 검사할 OAuth 새로 고침 토큰을 요청 하므로 여러 번 표시 됩니다.

Azure AD에서 세션 수명에 대해 가장 제한적인 정책은 사용자가 다시 인증 해야 하는 시기를 결정 합니다. 다음 시나리오를 고려하세요.

* 영구 브라우저 쿠키를 사용 하는 로그인 상태를 *유지*하도록 설정 합니다.
* *14 일 동안 MFA 기억을* 사용할 수도 있습니다.

이 예제 시나리오에서는 사용자가 14 일 마다 다시 인증 해야 합니다. 이 동작은 *로그인 유지* 가 사용자에 게 브라우저에서 재인증을 요구 하지 않더라도 가장 제한적인 정책을 따릅니다.

### <a name="managed-devices"></a>관리되는 디바이스

Azure ad 조인 또는 하이브리드 Azure AD Join을 사용 하 여 Azure AD에 연결 된 장치는 응용 프로그램 간에 SSO (Single Sign-On)를 사용 하는 [기본 새로 고침 토큰 (PRT)](../devices/concept-primary-refresh-token.md) 을 받습니다. 이 PRT를 사용 하면 사용자가 장치에서 한 번 로그인 할 수 있으며, IT 직원이 보안 및 규정 준수에 대 한 표준을 충족 하는지 확인할 수 있습니다. 일부 앱 또는 시나리오에서 사용자에 게 가입 된 장치에 대해 더 자주 로그인 하도록 요청 해야 하는 경우 [조건부 액세스 로그인 빈도](../conditional-access/howto-conditional-access-session-lifetime.md)를 사용 하 여이를 수행할 수 있습니다.

### <a name="show-option-to-remain-signed-in"></a>로그인 상태를 유지 하는 옵션 표시

로그인 하는 동안 사용자가 로그인 *유지?* 옵션 **을 선택 하면** 브라우저에 영구 쿠키가 설정 됩니다. 이 영구 쿠키는 첫 번째와 두 번째 요소를 모두 기억 하며 브라우저의 인증 요청에만 적용 됩니다.

![로그인 상태를 유지 하는 예제 프롬프트 스크린샷](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Azure AD Premium 1 라이선스를 사용 하는 경우 *영구 브라우저 세션*에 대 한 조건부 액세스 정책을 사용 하는 것이 좋습니다. 이 정책은 *로그인 상태 유지* 를 덮어쓰므로 향상 된 사용자 환경을 제공 합니다. Azure AD Premium 1 라이선스가 없는 경우 사용자에 게 로그인 상태 유지 설정을 사용 하는 것이 좋습니다.

사용자가 로그인 상태를 유지 하도록 하는 옵션을 구성 하는 방법에 대 한 자세한 내용은 [AZURE AD 로그인 페이지 사용자 지정](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page)을 참조 하세요.

### <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication 저장  

이 설정을 사용 하면 사용자가 로그인 시 **X 일 동안 다시 묻지 않음** 옵션을 선택 하면 1-365 일 사이의 값을 구성 하 고 브라우저에서 영구 쿠키를 설정할 수 있습니다.

![로그인 요청을 승인 하는 예제 프롬프트 스크린샷](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

이 설정은 웹 앱에 대 한 인증 수를 줄임으로써 Office 클라이언트와 같은 최신 인증 클라이언트에 대 한 인증 수를 늘립니다. 이러한 클라이언트는 일반적으로 암호 다시 설정 또는 90 일의 비활성 후에만 메시지를 표시 합니다. 그러나이 값을 90 일 미만으로 설정 하면 Office 클라이언트에 대 한 기본 MFA 프롬프트가 단축 되 고 재인증 빈도가 높아집니다. 에서 사용 되는 경우 **로그인** 또는 조건부 액세스 정책과 함께 사용 되는 경우 인증 요청 수가 늘어날 수 있습니다.

*MFA 기억을* 사용 하 고 Azure AD Premium 1 라이선스를 사용 하는 경우 이러한 설정을 조건부 액세스 로그인 빈도로 마이그레이션하는 것이 좋습니다. 그렇지 않으면 *로그인 유지* 를 대신 사용 하는 것이 좋습니다.

자세한 내용은 [Multi-Factor Authentication 기억을](howto-mfa-mfasettings.md#remember-multi-factor-authentication)참조 하세요.

### <a name="authentication-session-management-with-conditional-access"></a>조건부 액세스를 사용한 인증 세션 관리

관리자는 **로그인 빈도** 를 사용 하 여 클라이언트와 브라우저 모두에서 첫 번째와 두 번째 요소에 적용 되는 로그인 빈도를 선택할 수 있습니다. 중요 한 비즈니스 응용 프로그램의 경우와 같이 인증 세션을 제한 해야 하는 경우에는 관리 되는 장치를 사용 하는 경우와 함께 이러한 설정을 사용 하는 것이 좋습니다.

**영구 브라우저 세션** 을 사용 하면 사용자가 브라우저 창을 닫았다가 다시 연 후 로그인 상태를 유지할 수 있습니다. *로그인 상태 유지* 설정과 마찬가지로 브라우저에서 영구 쿠키를 설정 합니다. 그러나 관리자가 구성 했기 **때문에 사용자** 가 *로그인 유지?* 옵션을 선택 하지 않아도 되므로 더 나은 사용자 환경을 제공 합니다. *로그인 상태 유지?* 옵션을 사용 하는 경우 대신 **영구 브라우저 세션** 정책을 사용 하도록 설정 하는 것이 좋습니다.

자세한 내용은 [조건부 액세스를 사용 하 여 인증 세션 관리 구성을](../conditional-access/howto-conditional-access-session-lifetime.md)참조 하세요.

### <a name="configurable-token-lifetimes"></a>구성 가능한 토큰 수명

이 설정은 Azure Active Directory에서 발급 한 토큰의 수명을 구성할 수 있습니다. 이 정책은 *인증 세션 관리에서 조건부 액세스*로 바뀝니다. 지금 *구성 가능한 토큰 수명을* 사용 하는 경우 조건부 액세스 정책으로의 마이그레이션을 시작 하는 것이 좋습니다.

## <a name="review-your-tenant-configuration"></a>테 넌 트 구성 검토  

이제 다양 한 설정의 작동 원리와 권장 구성을 이해 했으므로 테 넌 트 구성을 확인 하 고 적절 하 게 변경 해야 합니다.

*로그인 상태 유지* 옵션을 구성 하거나 검토 하려면 다음 단계를 완료 합니다.

1. Azure AD 포털에서 *Azure Active Directory*를 검색 하 고 선택 합니다.
1. **회사 브랜딩**을 선택 하 고 각 로캘에 대해 **로그인 상태를 유지 하려면 옵션 표시를**선택 합니다.
1. *예*를 선택 하 고 **저장**을 선택 합니다.

Multi-factor authentication 설정을 기억을 하려면 다음 단계를 완료 합니다.

1. Azure AD 포털에서 *Azure Active Directory*를 검색 하 고 선택 합니다.
1. **보안**, **MFA**를 차례로 선택 합니다.
1. **구성**아래에서 **추가 클라우드 기반 MFA 설정을**선택 합니다.
1. *Multi-factor authentication 서비스 설정* 페이지에서 **multi-factor authentication 설정 기억을**스크롤합니다. 확인란의 선택을 취소 하 여 설정을 해제 합니다.

로그인 빈도 및 영구 브라우저 세션에 대 한 조건부 액세스 정책을 구성 하려면 다음 단계를 완료 합니다.

1. Azure AD 포털에서 *Azure Active Directory*를 검색 하 고 선택 합니다.
1. **보안**, **조건부 액세스**를 차례로 선택 합니다.
1. 이 문서에 설명 된 권장 세션 관리 옵션을 사용 하 여 정책을 구성 합니다.

토큰 수명을 검토 하려면 [AZURE Ad PowerShell을 사용 하 여 AZURE ad 정책을 쿼리](../develop/active-directory-configurable-token-lifetimes.md#prerequisites)합니다. 현재 보유 하 고 있는 모든 정책을 사용 하지 않도록 설정 합니다.

테 넌 트에서 둘 이상의 설정을 사용 하는 경우 사용 가능한 라이선스에 따라 설정을 업데이트 하는 것이 좋습니다. 예를 들어 Azure AD premium 라이선스가 있는 경우 *로그인 빈도* 및 *영구 브라우저 세션*의 조건부 액세스 정책만 사용 해야 합니다. Office 365 앱 또는 Azure AD 무료 라이선스를 사용 하는 경우 *로그인 상태 유지?* 구성을 사용 해야 합니다.

구성 가능한 토큰 수명을 사용 하는 경우이 기능은 곧 제거 될 예정입니다. 조건부 액세스 정책으로의 마이그레이션을 계획 합니다.

다음 표에는 라이선스에 따른 권장 사항이 요약 되어 있습니다.

|              | Azure AD Free 및 Office 365 앱 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | [AZURE ad 조인](../devices/concept-azure-ad-join.md) 또는 [하이브리드 azure ad 조인](../devices/concept-azure-ad-join-hybrid.md)또는 관리 되지 않는 장치에 대 한 [원활한 SSO](../hybrid/how-to-connect-sso.md) . | Azure AD 조인<br />하이브리드 Azure AD 조인 |
| **재인증 설정** | 로그인 상태 유지                  | 로그인 빈도 및 영구 브라우저 세션에 대 한 조건부 액세스 정책 사용 |

## <a name="next-steps"></a>다음 단계

시작 하려면 [azure Multi-Factor Authentication를 사용 하 여 사용자 로그인 이벤트를 보호](tutorial-enable-azure-mfa.md) 하는 자습서를 완료 하거나 [사용자 로그인에 대 한 위험 검색을 사용 하 여 azure Multi-Factor Authentication를 트리거합니다](tutorial-risk-based-sspr-mfa.md).
