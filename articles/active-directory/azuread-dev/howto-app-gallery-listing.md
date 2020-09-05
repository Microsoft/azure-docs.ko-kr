---
title: Azure AD 응용 프로그램 갤러리에 앱 나열 | Microsoft Docs
description: Azure Active Directory 앱 갤러리에서 Single Sign-On을 지원하는 애플리케이션을 나열하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/20/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 662bda03fc2d389ed316bd2c7141d5e6a30cef1a
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814639"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

이 문서에서는 Azure Active Directory (Azure AD) 응용 프로그램 갤러리에 응용 프로그램을 나열 하 고, SSO (Single Sign-On)를 구현 하 고, 목록을 관리 하는 방법을 보여 줍니다.

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD 애플리케이션 갤러리란?

- 고객에게 최상의 SSO(Single Sign-On) 환경을 제공합니다.
- 애플리케이션 구성이 간단하고 최소화됩니다.
- 갤러리에서 빠른 검색에 애플리케이션을 제공합니다.
- 무료, 기본 및 프리미엄 Azure AD 고객은 이 통합을 모두 사용할 수 있습니다.
- 상호 고객을 위한 단계별 구성 자습서가 있습니다.
- [Scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(도메인 간 id 관리)을 위해 시스템을 사용 하는 고객은 동일한 앱에 대 한 프로 비전을 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 페더레이션된 응용 프로그램 (Open ID 및 SAML/WS 급지됨)의 경우 응용 프로그램은 Azure AD 앱 갤러리에 나열 하기 위해 SaaS (software as a service) 모델을 지원 해야 합니다. Enterprise gallery 응용 프로그램은 특정 고객이 아닌 여러 고객 구성을 지원 해야 합니다.
- Open ID Connect의 경우 응용 프로그램을 multitenanted 하 고 [AZURE AD 동의 프레임 워크가](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 응용 프로그램에 대해 올바르게 구현 되어야 합니다. 사용자는 모든 고객이 응용 프로그램에 동의를 제공할 수 있도록 일반 끝점에 로그인 요청을 보낼 수 있습니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 사용자 액세스를 제어할 수 있습니다.
- SAML 2.0/WS 급지됨의 경우 응용 프로그램에 SP 또는 IDP 모드에서 SAML/WS 공급 SSO 통합을 수행할 수 있는 기능이 있어야 합니다. 요청을 제출 하기 전에이 기능이 제대로 작동 하는지 확인 합니다.
- 암호 SSO의 경우 응용 프로그램에서 폼 인증을 지원 하는지 확인 하 여 예상 대로 작동 하도록 Single Sign-On 수 있도록 암호 보관을 수행할 수 있도록 합니다.
- 두 명 이상의 사용자를 등록 하 여 테스트 하려면 영구 계정이 필요 합니다.

**개발자 용 Azure AD를 가져오는 방법**

모든 프리미엄 Azure AD 기능을 제공 하는 무료 테스트 계정 (90 일 무료)을 얻을 수 있으며,이를 통해 개발 작업을 수행 하는 동안 확장할 수 있습니다. https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>포털에서 요청 제출

응용 프로그램 통합이 Azure AD에서 작동 하는지 테스트 한 후에는 [Microsoft 응용 프로그램 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 응용 프로그램 요청을 제출 합니다.

로그인 한 후 다음 페이지가 표시 되 면 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 하세요. 요청을 제출 하는 데 사용할 전자 메일 계정을 제공 합니다. 과 같은 비즈니스 전자 메일 주소를 [name@yourbusiness.com](mailto:name@yourbusiness.com) 선호 합니다. Azure AD 팀이 Microsoft 응용 프로그램 네트워크 포털에 계정을 추가 합니다.

![SharePoint portal의 액세스 요청 메시지](./media/howto-app-gallery-listing/errorimage.png)

계정이 추가 된 후에 Microsoft 응용 프로그램 네트워크 포털에 로그인 할 수 있습니다.

로그인 한 후 다음 페이지가 표시 되 면 텍스트 상자에 액세스 해야 하는 비즈니스 근거를 제공 합니다. 그런 다음 **액세스 요청**을 선택 합니다.

  ![SharePoint portal의 비즈니스 근거 상자](./media/howto-app-gallery-listing/accessrequest.png)

팀이 세부 정보를 검토하고 적절하게 액세스 권한을 부여합니다. 요청이 승인 되 면 포털에 로그인 하 고 홈 페이지에서 요청 **제출 (ISV)** 타일을 선택 하 여 요청을 제출할 수 있습니다.

![홈 페이지의 ISV (요청 제출) 타일](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>포털에 로그인 하는 문제

로그인 하는 동안이 오류가 표시 되는 경우 문제에 대 한 세부 정보와 문제를 해결 하는 방법이 여기에 있습니다.

* 아래와 같이 로그인이 차단 된 경우:

  ![갤러리에서 응용 프로그램을 확인 하는 문제](./media/howto-app-gallery-listing/blocked.png)

**무슨 일이죠:**

게스트 사용자는 Azure AD 이기도 한 홈 테 넌 트에 페더레이션 됩니다. 게스트 사용자는 위험 수준이 높습니다. Microsoft는 높은 위험 수준의 사용자가 해당 리소스에 액세스할 수 없도록 합니다. 모든 위험 수준 사용자 (직원 또는 손님/공급 업체)는 Microsoft 리소스에 액세스 하기 위해 위험을 수정 하거나 종결 해야 합니다. 게스트 사용자의 경우이 사용자 위험은 홈 테 넌 트에서 발생 하 고 정책은 리소스 테 넌 트 (이 경우 Microsoft)에서 제공 됩니다.
 
**보안 솔루션:**

* MFA 등록 게스트 사용자는 자신의 사용자 위험을 수정 합니다. 게스트 사용자가 https://aka.ms/sspr) 해당 홈 테 넌 트에서 (홈 테 넌 트에서 MFA 및 SSPR 필요) 보안 된 암호 변경 또는 재설정을 수행 하 여이 작업을 수행할 수 있습니다. 보안 된 암호 변경 또는 재설정은 온-프레미스이 아닌 Azure AD에서 시작 해야 합니다.

* 게스트 사용자는 관리자에 게 위험을 해결 합니다. 이 경우 관리자가 암호 재설정 (임시 암호 생성)을 수행 합니다. Id 보호는 필요 하지 않습니다. 게스트 사용자의 관리자는로 이동 하 여 https://aka.ms/RiskyUsers ' 암호 재설정 '을 클릭할 수 있습니다.

* 게스트 사용자는 관리자에 게 위험을 종결 하거나 해제 합니다. 이 경우에도 Id 보호가 필요 하지 않습니다. 관리자는로 이동 하 여 https://aka.ms/RiskyUsers ' 사용자 위험 해제 '를 클릭할 수 있습니다. 그러나 관리자는 사용자 위험을 닫기 전에이가 거짓 긍정 위험 평가 인지 확인 하기 위해 기한 성실을 수행 해야 합니다. 그렇지 않으면 조사 하지 않고 위험 평가를 억제 하 여 및 Microsoft의 리소스를 위험에 노출 합니다.

> [!NOTE]
> 액세스에 문제가 있는 경우 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 하세요.

## <a name="implement-sso-by-using-the-federation-protocol"></a>페더레이션 프로토콜을 사용 하 여 SSO 구현

Azure AD 앱 갤러리에 애플리케이션을 나열하려면 먼저 Azure AD에서 지원하는 다음 페더레이션 프로토콜 중 하나를 구현해야 합니다. 또한 Azure AD 응용 프로그램 갤러리 사용 약관에 동의 해야 합니다. [이 웹 사이트](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)에서 Azure AD 응용 프로그램 갤러리의 사용 약관을 읽습니다.

- **Openid connect connect**: Open ID connect 프로토콜을 사용 하 여 응용 프로그램을 Azure AD와 통합 하려면 [개발자의 지침](v1-authentication-scenarios.md)을 따르세요.

    ![갤러리에서 Openid connect Connect 응용 프로그램 나열](./media/howto-app-gallery-listing/openid.png)

    * Openid connect Connect를 사용 하 여 갤러리의 목록에 응용 프로그램을 추가 하려는 경우 다음과 같이 **Openid connect Connect & OAuth 2.0** 을 선택 합니다.
    * 액세스에 문제가 있는 경우 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 하세요.

- **Saml 2.0** 또는 **WS-급지되지**않음: 앱이 saml 2.0을 지 원하는 경우 지침에 따라 [사용자 지정 응용 프로그램을 추가](../manage-apps/view-applications-portal.md)하 여 Azure AD 테 넌 트와 직접 통합할 수 있습니다.

  ![갤러리에 SAML 2.0 또는 WS-급지됨 응용 프로그램 나열](./media/howto-app-gallery-listing/saml.png)

  * **Saml 2.0** 또는 **ws 급지됨**를 사용 하 여 갤러리의 목록에 응용 프로그램을 추가 하려는 경우 표시 된 대로 **saml 2.0/ws 공급** 을 선택 합니다.

  * 액세스에 문제가 있는 경우 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 하세요.

## <a name="implement-sso-by-using-the-password-sso"></a>암호 SSO를 사용 하 여 SSO 구현

HTML 로그인 페이지가 있는 웹 애플리케이션을 만들어서 [암호 기반 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유 해야 하는 시나리오에 유용 합니다.

![갤러리에 암호 SSO 응용 프로그램 나열](./media/howto-app-gallery-listing/passwordsso.png)

* 암호 SSO를 사용 하 여 갤러리의 목록에 응용 프로그램을 추가 하려면 다음과 같이 **암호 sso (사용자 이름 & 암호)** 를 선택 합니다.
* 액세스에 문제가 있는 경우 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 하세요.

## <a name="request-for-user-provisioning"></a>사용자 프로 비전 요청

다음 이미지에 표시 된 프로세스에 따라 사용자 프로 비전을 요청 합니다.

   ![사용자 프로 비전 요청](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>기존 목록을 업데이트 하거나 제거 합니다.

Azure AD 앱 갤러리에서 기존 응용 프로그램을 업데이트 하거나 제거 하려면 먼저 [응용 프로그램 네트워크 포털](https://microsoft.sharepoint.com/teams/apponboarding/Apps)에서 요청을 제출 해야 합니다. Office 365 계정이 있는 경우 이를 사용하여 해당 포털에 로그인합니다. 그렇지 않으면 Outlook 또는 Hotmail과 같은 Microsoft 계정를 사용 하 여 로그인 합니다.

- 다음 그림에 표시 된 것 처럼 적절 한 옵션을 선택 합니다.

    ![갤러리에 SAML 응용 프로그램 나열](./media/howto-app-gallery-listing/updateorremove.png)

    * 기존 응용 프로그램을 업데이트 하려면 요구 사항에 따라 적절 한 옵션을 선택 합니다.
    * Azure AD 앱 갤러리에서 기존 응용 프로그램을 제거 하려면 **갤러리에서 내 응용 프로그램 목록 제거**를 선택 합니다.
    * 액세스에 문제가 있는 경우 [AZURE AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)에 문의 하세요.

## <a name="list-requests-by-customers"></a>고객 별 요청 나열

고객은 **고객이**  >  **새 요청을 제출**하는 앱 요청을 선택 하 여 응용 프로그램을 나열 하는 요청을 제출할 수 있습니다.

![고객이 요청한 앱 타일을 표시 합니다.](./media/howto-app-gallery-listing/customer-submit-request.png)

고객 요청 응용 프로그램의 흐름은 다음과 같습니다.

![고객이 요청한 앱 흐름을 표시 합니다.](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>타임라인

갤러리에서 SAML 2.0 또는 WS (WS) 응용 프로그램을 나열 하는 프로세스에 대 한 타임 라인은 영업일의 7 ~ 10 일입니다.

  ![갤러리에서 SAML 응용 프로그램을 나열 하기 위한 타임 라인](./media/howto-app-gallery-listing/timeline.png)

갤러리에서 Openid connect Connect 응용 프로그램을 나열 하는 프로세스에 대 한 타임 라인은 영업일/영업일입니다.

  ![갤러리에서 Openid connect Connect 응용 프로그램을 나열 하는 타임 라인](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>에스컬레이션

모든 에스컬레이션에 대해 [AZURE AD SSO 통합 팀](mailto:SaaSApplicationIntegrations@service.microsoft.com) 에 전자 메일을 보내면 SaaSApplicationIntegrations@service.microsoft.com 가능한 한 빨리 응답 합니다.