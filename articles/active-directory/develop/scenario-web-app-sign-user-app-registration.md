---
title: 사용자에 게 로그인 하는 웹 앱 등록-Microsoft id 플랫폼 | Microsoft
description: 사용자가 로그인 하는 웹 앱을 등록 하는 방법 알아보기
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701572"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>사용자가 로그인 하는 웹 앱: 앱 등록

이 문서에서는 사용자를 로그인 하는 웹 앱에 대 한 앱 등록 세부 사항을 설명 합니다.

응용 프로그램을 등록 하려면 다음을 사용할 수 있습니다.

- [웹 앱](#register-an-app-by-using-the-quickstarts)빠른 시작입니다. 응용 프로그램을 만들 때 뛰어난 첫 번째 경험이 될 뿐만 아니라, Azure Portal의 빠른 시작에는 **이 변경을 위해**이름이 지정 된 단추가 포함 되어 있습니다. 이 단추를 사용 하 여 기존 응용 프로그램의 경우에도 필요한 속성을 설정할 수 있습니다. 이러한 속성의 값은 사용자의 사례에 맞게 조정 해야 합니다. 특히 앱에 대 한 web API URL은 제안 된 기본값과 다를 수 있으며,이는 로그 아웃 URI에도 영향을 줍니다.
- [응용 프로그램을 수동으로 등록](#register-an-app-by-using-the-azure-portal)하는 Azure Portal입니다.
- PowerShell 및 명령줄 도구.

## <a name="register-an-app-by-using-the-quickstarts"></a>빠른 시작을 사용 하 여 앱 등록

다음 링크를 사용 하 여 웹 응용 프로그램을 만들 수 있습니다.

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 앱 등록

> [!NOTE]
> 사용할 포털은 응용 프로그램이 Microsoft Azure 공용 클라우드 또는 국가 또는 소 버린 클라우드에서 실행 되는지에 따라 달라 집니다. 자세한 내용은 [국가별 클라우드](./authentication-national-cloud.md#app-registration-endpoints)를 참조 하세요.


1. [Azure Portal](https://portal.azure.com)에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다. 또는 국가별 클라우드의 선택 Azure Portal에 로그인 합니다.
1. 계정이 둘 이상의 테넌트에 대 한 액세스를 제공 하는 경우 오른쪽 위 모서리에서 계정을 선택 합니다. 그런 다음 포털 세션을 원하는 Azure Active Directory (Azure AD) 테넌트로 설정 합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택한 다음 **앱 등록** > **새 등록**을 선택 합니다.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 응용 프로그램에 대해 지원 되는 계정 유형을 선택 합니다. ( [지원 되는 계정 유형](./v2-supported-account-types.md)을 참조 하세요.)
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다. 예를 들어 **AspNetCore-WebApp**를 입력 합니다.
   1. **리디렉션 uri**의 경우 인증에 성공한 후 반환 된 토큰 응답을 받을 응용 프로그램의 형식 및 URI 대상을 추가 합니다. 예를 들어 **https://localhost:44321** 을 입력합니다. 그런 다음 **등록**을 선택합니다.
1. **인증** 메뉴를 선택한 후 다음 정보를 추가합니다.
   1. **회신 URL**의 경우 **웹**형식의 **https://localhost:44321/signin-oidc** 를 추가 합니다.
   1. **고급 설정** 섹션에서 **로그아웃 URL**을 **https://localhost:44321/signout-oidc** 으로 설정합니다.
   1. **암시적 허용**에서 **ID 토큰**을 선택합니다.
   1. **저장**을 선택합니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. 응용 프로그램에 대해 지원 되는 계정 유형을 선택 합니다. ( [지원 되는 계정 유형](./v2-supported-account-types.md)을 참조 하세요.)
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다. 예를 들어 **Mailapp-openidconnect-v2**를 입력 합니다.
   1. **Uri 리디렉션 (선택 사항)** 섹션의 콤보 상자에서 **웹** 을 선택 하 고 다음 리디렉션 URI를 입력 **https://localhost:44326/** 합니다.
1. **등록**을 선택하여 애플리케이션을 만듭니다.
1. **인증** 메뉴를 선택 합니다.
1. **고급 설정** | **암시적 허용** 섹션에서 **ID 토큰**을 선택 합니다. 이 샘플을 사용 하려면 사용자에 게 로그인 할 수 있는 [암시적 grant 흐름이](v2-oauth2-implicit-grant-flow.md) 필요 합니다.
1. **저장**을 선택합니다.

# <a name="javatabjava"></a>[Java](#tab/java)

1. **응용 프로그램 등록 페이지가** 표시 되 면 응용 프로그램에 대 한 표시 이름을 입력 합니다. 예를 들어 **java-webapp**을 입력 합니다.
1. **모든 조직 디렉터리와 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)에서 계정을**선택 하 고 **응용 프로그램 유형**으로 **웹 앱/** a p i를 선택 합니다.
1. **등록** 을 선택 하 여 응용 프로그램을 등록 합니다.
1. 왼쪽 메뉴에서 **인증**을 선택 합니다. **Uri 리디렉션**에서 **웹**을 선택 합니다.

1. 두 개의 리디렉션 Uri (로그인 페이지의 경우 하나, 그래프 페이지의 경우 하나)를 입력 합니다. 둘 다에 대해 동일한 호스트 및 포트 번호를 사용 하 고, 로그인 페이지에 **/msal4jsample/secure/aad** 을, 사용자 정보 페이지에 대해 **msal4jsample/graph/me** 를 사용 합니다.

   기본적으로이 샘플은 다음을 사용 합니다.

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  그런 다음 **저장**을 선택합니다.

1. 메뉴에서 **인증서 & 암호** 를 선택 합니다.
1. **클라이언트 암호** 섹션에서 **새 클라이언트 암호**를 선택 하 고 다음을 수행 합니다.

   1. 키 설명을 입력 합니다.
   1. **1 년**동안 키 기간을 선택 합니다.
   1. **추가**를 선택합니다.
   1. 키 값이 표시 되 면 나중에 해당 키 값을 복사 합니다. 이 값은 다시 표시 되거나 다른 방법으로 검색할 수 없습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다. 예를 들어 **python-webapp**을 입력 합니다.
   1. **모든 조직 디렉터리와 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)에서** **지원 되는 계정 유형을** 계정으로 변경 합니다.
   1. **Uri 리디렉션 (선택 사항)** 섹션의 콤보 상자에서 **웹** 을 선택 하 고 다음 리디렉션 URI를 입력 **http://localhost:5000/getAToken** 합니다.
1. **등록**을 선택하여 애플리케이션을 만듭니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트의 Visual Studio 구성 파일을 구성하는 데 필요합니다.
1. 왼쪽 메뉴에서 **인증서 & 암호**를 선택 합니다.
1. **클라이언트 암호** 섹션에서 **새 클라이언트 암호**를 선택 하 고 다음을 수행 합니다.

   1. 키 설명을 입력 합니다.
   1. **1년 후** 키 기간을 선택합니다.
   1. **추가**를 선택합니다.
   1. 키 값이 표시 되 면 복사 합니다. 나중에 필요합니다.
---

## <a name="register-an-app-by-using-powershell"></a>PowerShell을 사용 하 여 앱 등록

> [!NOTE]
> 현재 Azure AD PowerShell은 다음과 같은 지원 되는 계정 유형만 포함 된 응용 프로그램을 만듭니다.
>
> - MyOrg (이 조직 디렉터리의 계정에만 해당)
> - AnyOrg (모든 조직 디렉터리의 계정)
>
> 개인 Microsoft 계정 (예: Skype, Xbox 또는 Outlook.com)을 사용 하 여 사용자를 로그인 하는 응용 프로그램을 만들 수 있습니다. 먼저 다중 테넌트 응용 프로그램을 만듭니다. 지원 되는 계정 유형은 모든 조직 디렉터리의 계정입니다. 그런 다음 Azure Portal에서 응용 프로그램 매니페스트의 `signInAudience` 속성을 변경 합니다. 자세한 내용은 ASP.NET Core 자습서에서 [1.3 단계](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) 를 참조 하세요. 모든 언어로 된 웹 앱에이 단계를 일반화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱의 코드 구성](scenario-web-app-sign-user-app-configuration.md)
