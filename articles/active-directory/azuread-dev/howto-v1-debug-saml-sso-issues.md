---
title: Azure Active Directory에서 SAML 기반 Single Sign-On 디버그 | Microsoft Docs
description: Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: ad948521a376dc8bdb3d868de643ce103f56735d
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164605"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[SAML(Security Assertion Markup Language) 2.0](../manage-apps/what-is-single-sign-on.md)을 지원하는 Azure AD(Azure Active Directory)에서 애플리케이션에 대한 [단일 로그온](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) 문제를 찾고 수정하는 방법을 알아봅니다. 

## <a name="before-you-begin"></a>시작하기 전에

[내 앱 보안 로그인 확장](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)을 설치하는 것이 좋습니다. 이 브라우저 확장을 사용 하면 Single Sign-On 관련 된 문제를 해결 하는 데 필요한 SAML 요청 및 SAML 응답 정보를 쉽게 수집할 수 있습니다. 확장을 설치할 수 없는 경우 이 문서에서는 확장이 설치되거나 설치되지 않았을 때의 문제를 해결하는 방법을 보여줍니다.

내 앱 보안 로그인 확장을 다운로드하고 설치하려면 다음 링크 중 하나를 사용합니다.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>SAML 기반 Single Sign-On 테스트

Azure AD와 대상 응용 프로그램 간에 SAML 기반 Single Sign-On를 테스트 하려면 다음을 수행 합니다.

1. 전역 관리자 또는 애플리케이션을 관리할 권한이 있는 다른 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 블레이드에서 **Azure Active Directory**를 선택한 후 **엔터프라이즈 응용 프로그램**을 선택 합니다. 
1. 엔터프라이즈 응용 프로그램 목록에서 Single Sign-On 테스트 하려는 응용 프로그램을 선택 하 고 왼쪽에 있는 옵션에서 **single sign-on**을 선택 합니다.
1. SAML 기반 Single Sign-On 테스트 환경을 열려면 **테스트 Single Sign-On** (5 단계)로 이동 합니다. **테스트** 단추가 회색으로 표시 되 면 필요한 특성을 입력 하 고 **기본 SAML 구성** 섹션에 먼저 저장 해야 합니다.
1. **Single Sign-On 테스트** 블레이드에서 대상 애플리케이션에 로그인하려면 회사 자격 증명을 사용합니다. 현재 사용자 또는 다른 사용자로 로그인할 수 있습니다. 다른 사용자로 로그인하는 경우 인증을 요구하는 메시지가 나타납니다.

    ![SAML SSO 페이지 테스트를 보여 주는 스크린샷](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

성공적으로 로그인하면 테스트가 통과된 것입니다. 이 경우 Azure AD는 애플리케이션에 SAML 응답 토큰을 발급합니다. 애플리케이션은 SAML 토큰을 사용해 성공적으로 로그인했습니다.

회사 로그인 페이지 또는 애플리케이션 페이지에 오류가 있는 경우 해당 오류를 해결하려면 다음 섹션 중 하나를 사용합니다.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>회사 로그인 페이지에서 로그인 오류 해결

로그인 하려고 하면 회사 로그인 페이지에 다음 예와 비슷한 오류가 표시 될 수 있습니다.

![회사 로그인 페이지의 오류를 보여 주는 예제](./media/howto-v1-debug-saml-sso-issues/error.png)

이 오류를 디버그하려면 오류 메시지 및 SAML 요청이 필요합니다. 내 앱 보안 로그인 확장은 자동으로 이 정보를 수집하고 Azure AD에 해결 지침을 표시합니다.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>내 앱 보안 로그인 확장을 설치 하 여 로그인 오류를 해결 하려면

1. 오류가 발생 하면 확장은 Azure AD **테스트 Single Sign-On** 블레이드로 다시 리디렉션합니다.
1. **테스트 Single Sign-On** 블레이드에서 **SAML 요청 다운로드**를 선택 합니다.
1. SAML 요청의 오류 및 값에 따른 특정 해결 지침이 표시되어야 합니다.
1. 문제를 해결 하기 위해 Azure AD에서 구성을 자동으로 업데이트 하는 **수정 it** 단추가 표시 됩니다. 이 단추가 표시 되지 않으면 Azure AD의 잘못 된 구성으로 인해 로그인 문제가 발생 하지 않습니다.

로그인 오류에 대 한 해결 방법이 제공 되지 않으면 피드백 텍스트 상자를 사용 하 여 microsoft에 알려 주는 것이 좋습니다.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장을 설치 하지 않고 오류를 해결 하려면

1. 페이지의 오른쪽 아래 모서리에서 오류 메시지를 복사합니다. 오류 메시지에는 다음이 포함됩니다.
    - CorrelationID 및 TimeStamp. 이러한 값은 엔지니어가 문제를 식별하고 문제에 대한 정확한 해결책을 제공하는 데 유용하므로 Microsoft를 통해 지원 사례를 만들 때 중요합니다.
    - 문제의 근본 원인을 확인하는 명령문입니다.
1. Azure AD로 다시 돌아가 **Single Sign-On 테스트** 블레이드를 찾습니다.
1. 위의 **해결 지침 가져오기** 텍스트 상자에서 오류 메시지를 붙여넣습니다.
1. **해결 지침 가져오기**를 클릭하여 문제 해결을 위한 단계를 표시합니다. 이 지침은 SAML 요청 또는 SAML 응답에서 정보를 요구할 수 있습니다. 내 앱 보안 로그인 확장을 사용 하지 않는 경우 SAML 요청 및 응답을 검색 하는 [Fiddler](https://www.telerik.com/fiddler) 와 같은 도구가 필요할 수 있습니다.
1. SAML 요청의 대상이 Azure AD에서 가져온 SAML Single Sign-on 서비스 URL에 해당 하는지 확인 합니다.
1. SAML 요청의 발급자가 Azure AD에서 응용 프로그램에 대해 구성한 것과 동일한 식별자 인지 확인 합니다. Azure AD는 사용자 디렉터리에서 애플리케이션을 찾는 데 발급자를 사용합니다.
1. 응용 프로그램이 Azure AD에서 SAML 토큰을 수신할 것으로 예상 하는 AssertionConsumerServiceURL 확인 합니다. Azure AD에서이 값을 구성할 수 있지만 SAML 요청에 포함 된 경우에는 필수 항목이 아닙니다.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>애플리케이션 페이지에서 로그인 오류 해결

성공적으로 로그인한 다음, 애플리케이션 페이지에 오류가 표시될 수 있습니다. Azure AD가 애플리케이션에 토큰을 발급했지만 애플리케이션이 응답을 적용하지 않는 경우에 발생합니다.

이 오류를 해결 하려면 다음이 단계를 수행 합니다.

1. 응용 프로그램이 Azure AD 갤러리에 있는 경우 응용 프로그램을 Azure AD와 통합 하기 위한 모든 단계를 수행 했는지 확인 합니다. 애플리케이션에 대한 통합 지침을 찾으려면 [SaaS 애플리케이션 통합 자습서 목록](../saas-apps/tutorial-list.md)을 참조합니다.
1. SAML 응답을 검색합니다.
    - 내 앱 보안 로그인 확장이 설치된 경우 **Single Sign-On 테스트** 블레이드에서 **SAML 응답 다운로드**를 클릭합니다.
    - 확장이 설치되지 않은 경우 [Fiddler](https://www.telerik.com/fiddler) 같은 도구를 사용하여 SAML 응답을 검색합니다.
1. SAML 응답 토큰에서 이러한 요소를 확인합니다.
   - NameID 값과 형식의 사용자 고유 식별자
   - 토큰에서 발급된 클레임
   - 토큰에 서명하는 데 사용된 인증서입니다.

     SAML 응답에 대한 자세한 내용은 [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조합니다.

1. SAML 응답을 검토 했으므로 문제를 해결 하는 방법에 대 한 지침은 [로그인 한 후 응용 프로그램 페이지의 오류](../manage-apps/application-sign-in-problem-application-error.md) 를 참조 하세요. 
1. 여전히 성공적으로 로그인 할 수 없는 경우 응용 프로그램 공급 업체에 SAML 응답에 누락 된 항목을 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 응용 프로그램에 대 한 Single Sign-On 작업 중 이므로 [SaaS 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전](../manage-apps/user-provisioning.md) 해제를 자동화 하거나 [조건부 액세스를 시작할](../conditional-access/app-based-conditional-access.md)수 있습니다.
