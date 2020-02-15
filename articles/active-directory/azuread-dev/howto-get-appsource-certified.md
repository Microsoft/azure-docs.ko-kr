---
title: Azure Active Directory에 대해 인증된 AppSource 가져오는 방법 | Microsoft Docs
description: Azure Active Directory에 대해 인증된 애플리케이션 AppSource 가져오는 방법에 대한 세부 정보.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 8b966df3f0ca59edbaa304212f05daffeb9ef17d
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164761"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Azure Active Directory에 대해 인증된 AppSource 가져오는 방법

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/)는 기간 업무 SaaS 애플리케이션을 검색, 시도 및 관리하는 비즈니스 사용자에 대한 대상입니다(기존 Microsoft SaaS 제품에 대한 독립 실행형 SaaS 및 추가 기능).

AppSource에서 독립 실행형 SaaS 애플리케이션을 나열하려면 애플리케이션은 Azure AD(Azure Active Directory)가 있는 모든 회사 또는 조직의 작업 계정에서 Single Sign-On을 허용해야 합니다. 로그인 프로세스는 [OpenID Connect](v1-protocols-openid-connect-code.md) 또는 [OAuth 2.0](v1-protocols-oauth-code.md) 프로토콜을 사용해야 합니다. SAML 통합은 AppSource 인증에 허용되지 않습니다.

## <a name="guides-and-code-samples"></a>지침 및 코드 샘플

Open ID connect를 사용 하 여 응용 프로그램을 Azure AD와 통합 하는 방법을 알아보려면 [Azure Active Directory 개발자 가이드](v1-overview.md#get-started "개발자 용 Azure AD 시작")의 가이드 및 코드 샘플을 따르세요.

## <a name="multi-tenant-applications"></a>다중 테넌트 애플리케이션

*다중 테넌트 애플리케이션*은 별도 인스턴스, 구성 또는 배포를 요구하지 않고 Azure AD가 있는 모든 회사 또는 조직에서 사용자의 로그인을 허용하는 애플리케이션입니다. AppSource에서는 애플리케이션이 *단일 클릭* 평가판 체험을 활성화하도록 다중 테넌트를 구현하는 것을 권장합니다.

애플리케이션에서 다중 테넌트를 활성화하려면 다음 단계를 수행합니다.
1. `Multi-Tenanted`Azure Portal`Yes`의 애플리케이션 등록 정보에서 [ 속성을 ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)로 설정합니다. 기본적으로 Azure Portal에서 만든 애플리케이션은 *[단일 테넌트](#single-tenant-applications)* 로 구성됩니다.
1. `common` 엔드포인트에 요청을 보내도록 코드를 업데이트합니다. 이렇게 하려면 엔드포인트를 `https://login.microsoftonline.com/{yourtenant}`에서 `https://login.microsoftonline.com/common*`으로 업데이트합니다.
1. ASP .NET과 같은 일부 플랫폼의 경우 여러 발급자를 허용하도록 코드를 업데이트해야 합니다.

다중 테넌트에 대한 정보는 [다중 테넌트 애플리케이션 패턴을 사용하여 모든 Azure AD(Azure Active Directory) 사용자를 로그인하는 방법](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요.

### <a name="single-tenant-applications"></a>단일 테넌트 애플리케이션

*단일 테넌트 애플리케이션*은 정의된 Azure AD 인스턴스의 사용자의 로그인만 허용하는 애플리케이션입니다. 외부 사용자(다른 조직의 회사 또는 학교 계정 또는 개인 계정 포함)는 각 사용자를 게스트 계정으로 애플리케이션이 등록된 Azure AD 인스턴스에 추가한 후 단일 테넌트 애플리케이션에 로그인 할 수 있습니다. 

[Azure AD B2B 협업](../b2b/what-is-b2b.md)을 통해 사용자를 게스트 계정으로 Azure AD에 추가할 수 있으며, 이 작업은 [프로그래밍 방식으로](../../active-directory-b2c/code-samples.md) 수행할 수 있습니다. B2B를 사용하는 경우 사용자는 로그인하라는 초대장이 필요하지 않은 셀프 서비스 포털을 만들 수 있습니다. 자세한 내용은 [Azure AD B2B 협업 등록을 위한 셀프 서비스 포털](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)을 참조하세요.

단일 테넌트 애플리케이션은 *연락처* 환경을 활성화할 수 있지만 AppSource가 권장하는 단일 클릭/평가판 체험을 활성화하려는 경우 애플리케이션에서 다중 테넌트를 대신 활성화합니다.

## <a name="appsource-trial-experiences"></a>AppSource 평가판 체험

### <a name="free-trial-customer-led-trial-experience"></a>평가판(고객 주도 평가판 체험)

고객 주도 평가판은 애플리케이션에 대한 단일 클릭 액세스를 제공하므로 AppSource에서 권장하는 체험입니다. 다음 예제에서는이 환경을 보여 줍니다.

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>사용자는 AppSource 웹 사이트에서 애플리케이션을 찾습니다.</li><li>‘평가판’ 옵션을 선택합니다.</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource는 웹 사이트의 URL로 사용자를 리디렉션합니다.</li><li>웹 사이트는 <i>Single Sign-On</i> 프로세스를 자동으로 시작합니다(페이지 로드에서).</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>사용자는 Microsoft 로그인 페이지로 리디렉션됩니다.</li><li>사용자는 로그인하기 위해 자격 증명을 제공합니다.</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>사용자는 애플리케이션에 대한 동의를 제공합니다.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>로그인을 완료하고 사용자는 웹 사이트로 다시 리디렉션됩니다.</li><li>사용자는 평가판을 시작합니다.</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>연락처(파트너 주도 평가판 체험)

사용자/회사를 프로비전하기 위해 수동 또는 장기 작업을 발생해야 하는 경우에 파트너 평가판 체험을 사용할 수 있습니다. 예를 들어 애플리케이션은 가상 머신, 데이터베이스 인스턴스 또는 완료하는 데 시간이 많이 걸리는 작업을 프로비전해야 합니다. 이 경우 사용자가 **평가판 요청** 단추를 선택하고 양식을 채운 후 AppSource는 사용자의 연락처 정보를 보냅니다. 이 정보를 받으면 환경을 프로비전하고 평가판 체험에 액세스하는 방법에 대한 지침을 사용자에게 보냅니다.<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>사용자는 AppSource 웹 사이트에서 애플리케이션을 찾습니다.</li><li>'연락처' 옵션을 선택합니다.</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>연락처 정보로 양식을 작성합니다.</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>사용자 정보를 수신합니다.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>환경을 설정합니다.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>평가판 정보로 사용자에게 연락합니다.</td>
        </tr>
        </table><br/><br/>
        <ul><li>사용자의 정보를 받고 평가판 인스턴스를 설정합니다.</li><li>애플리케이션이 사용자에게 액세스하기 위한 하이퍼링크를 보냅니다.</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>사용자는 애플리케이션에 액세스하고 Single Sign-On 프로세스를 완료합니다.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>사용자는 애플리케이션에 대한 동의를 제공합니다.</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>로그인을 완료하고 사용자는 웹 사이트로 다시 리디렉션됩니다.</li><li>사용자는 평가판을 시작합니다.</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>자세한 정보

AppSource 평가판 체험에 대한 자세한 내용은 [이 비디오](https://aka.ms/trialexperienceforwebapps)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

- Azure AD 로그인을 지원하는 애플리케이션 구축에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios)를 참조하세요.
- AppSource에 SaaS 애플리케이션을 나열하는 방법에 대한 내용은 [AppSource 파트너 정보](https://appsource.microsoft.com/partners)를 참조하세요.

## <a name="get-support"></a>지원 받기

Azure AD 통합의 경우 지원을 제공하기 위해 커뮤니티와 함께 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)를 사용합니다.

먼저 Stack Overflow에 질문하고 이전에 다른 사용자가 질문했는지 확인하기 위해 기존 문제를 찾아보는 것이 좋습니다. 질문이나 의견이 [`[azure-active-directory]` 및 `[appsource]`](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)로 태그가 지정되어 있는지 확인합니다.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
