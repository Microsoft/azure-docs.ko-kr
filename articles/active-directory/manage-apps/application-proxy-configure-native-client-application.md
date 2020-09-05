---
title: 네이티브 클라이언트 앱 게시 - Azure AD | Microsoft Docs
description: Azure AD 애플리케이션 프록시 커넥터와 통신하는 네이티브 클라이언트 앱을 사용하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f8494852bcff49602645c940470b529302f119f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165076"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>네이티브 클라이언트 앱이 프록시 애플리케이션과 상호 작용할 수 있도록 설정하는 방법

Azure AD(Azure Active Directory) 애플리케이션 프록시를 사용하여 웹 애플리케이션을 게시할 수 있지만 MSAL(Microsoft Authentication Library)로 구성된 네이티브 클라이언트 애플리케이션을 게시하는 데 사용할 수도 있습니다. 네이티브 클라이언트 애플리케이션은 디바이스에 설치되는 반면 웹앱은 브라우저를 통해 액세스되므로 웹앱과 다릅니다.

네이티브 클라이언트 애플리케이션을 지원하기 위해 애플리케이션 프록시는 헤더에 전송되는 Azure AD가 발급한 토큰을 허용합니다. 애플리케이션 프록시 서비스는 사용자에 대한 인증을 수행합니다. 이 솔루션은 인증에 대해 애플리케이션 토큰을 사용하지 않습니다.

![최종 사용자, Azure AD 및 게시된 애플리케이션 간의 관계](./media/application-proxy-configure-native-client-application/richclientflow.png)

네이티브 애플리케이션을 게시하려면 인증을 처리하고 다양한 클라이언트 환경을 지원하는 Microsoft Authentication Library를 사용합니다. 애플리케이션 프록시는 [로그인한 사용자를 대신하여 웹 API를 호출하는 데스크톱 앱](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) 시나리오에 적합합니다.

이 문서에서는 애플리케이션 프록시 및 Azure AD 인증 라이브러리를 사용하여 네이티브 애플리케이션을 게시하는 네 가지 단계를 안내합니다.

## <a name="step-1-publish-your-proxy-application"></a>1단계: 프록시 애플리케이션 게시

다른 애플리케이션과 마찬가지로 프록시 애플리케이션을 게시하고 애플리케이션에 액세스하도록 사용자를 할당합니다. 자세한 내용은 [애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)를 참조하세요.

## <a name="step-2-register-your-native-application"></a>2단계: 네이티브 애플리케이션 등록

이제 다음과 같이 Azure AD에서 애플리케이션을 등록해야 합니다.

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에 로그인합니다. **Azure Active Directory 관리 센터**에 대한 **대시보드**가 표시됩니다.
1. 사이드바에서 **Azure Active Directory**를 선택합니다. **Azure Active Directory** 개요 페이지가 표시됩니다.
1. Azure AD 개요 사이드바에서 **앱 등록**을 선택합니다. 모든 앱 등록 목록이 표시됩니다.
1. **새 등록**을 선택합니다. **애플리케이션 등록** 페이지가 나타납니다.

   ![Azure Portal에서 새 앱 등록을 만듭니다.](./media/application-proxy-configure-native-client-application/create.png)

1. **이름** 제목에서 애플리케이션에 대한 사용자용 표시 이름을 지정합니다.
1. **지원되는 계정 유형** 제목 아래에서 다음 지침을 사용하여 액세스 수준을 선택합니다.

   - 조직의 내부 계정만을 대상으로 하려면 **이 조직 디렉터리의 계정만**을 선택합니다.
   - 비즈니스 또는 교육용 고객만 대상으로 하려면 **모든 조직 디렉터리의 계정**을 선택합니다.
   - 가장 광범위한 Microsoft ID를 대상으로 하려면 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
1. **리디렉션 URI**에서 **공용 클라이언트(모바일 및 데스크톱)** 를 선택하고 애플리케이션에 대한 리디렉션 URI`https://login.microsoftonline.com/common/oauth2/nativeclient`를 입력합니다.
1. **Microsoft 플랫폼 정책**을 선택하고 검토한 후 **등록**을 선택합니다. 새 애플리케이션 등록에 대한 개요 페이지가 생성되고 표시됩니다.

새 애플리케이션 등록 만들기에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../develop/quickstart-register-app.md)을 참조하세요.

## <a name="step-3-grant-access-to-your-proxy-application"></a>3단계: 프록시 애플리케이션에 대한 액세스 권한 부여

이제 네이티브 애플리케이션을 등록했으므로 디렉터리의 다른 애플리케이션에 대한 액세스 권한을 제공할 수 있습니다. 이 경우에는 프록시 애플리케이션에 대한 액세스 권한을 제공합니다. 네이티브 애플리케이션이 프록시 애플리케이션에 노출될 수 있게 하려면 다음을 수행합니다.

1. 새 애플리케이션 등록 페이지의 사이드바에서 **API 사용 권한**을 선택합니다. 새 애플리케이션 등록을 위한 **API 사용 권한** 페이지가 나타납니다.
1. **사용 권한 추가**를 선택합니다. **API 사용 권한 요청** 페이지가 나타납니다.
1. **API 선택** 설정에서 **내 조직에서 사용하는 API**를 선택합니다. API를 노출하는 디렉터리의 애플리케이션이 포함된 목록이 표시됩니다.
1. [1단계: 프록시 애플리케이션 게시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-native-client-application#step-1-publish-your-proxy-application)에서 게시한 프록시 애플리케이션을 검색 상자에 입력하거나 스크롤하여 찾은 다음 프록시 애플리케이션을 선택합니다.
1. **애플리케이션에 어떤 유형의 권한이 필요한가요?** 제목에서 사용 권한 유형을 선택합니다. 네이티브 애플리케이션에서 로그인한 사용자로 프록시 애플리케이션 API에 액세스해야 하는 경우 **위임된 권한**을 선택합니다.
1. **사용 권한 선택** 제목에서 원하는 사용 권한을 선택하고 **사용 권한 추가**를 선택합니다. 이제 네이티브 애플리케이션에 대한 **API 사용 권한** 페이지에 추가된 프록시 애플리케이션 및 사용 권한 API가 표시됩니다.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>4단계: 코드에 Microsoft 인증 라이브러리 추가(.NET C# 샘플)

MSAL(Microsoft Authentication Library)의 인증 컨텍스트에서 네이티브 애플리케이션 코드를 편집하여 다음 텍스트를 포함합니다. 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

샘플 코드의 필수 정보는 다음과 같이 Azure AD 포털에서 찾을 수 있습니다.

| 필요한 정보 | Azure AD 포털에서 이 정보를 찾는 방법 |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory** > **속성** > **디렉터리 ID** |
| \<App ID of the Native app> | **애플리케이션 등록** > *네이티브 애플리케이션* > **개요** > **애플리케이션 ID** |
| \<Scope> | **애플리케이션 등록** > *네이티브 애플리케이션* > **API 사용 권한** > 사용 권한 API 클릭(user_impersonation) > **user_impersonation** 캡션이 포함된 패널이 오른쪽에 나타납니다. > 범위는 편집 상자에 있는 URL입니다.
| \<Proxy App Url> | 외부 Url 및 API 경로

이러한 매개 변수를 사용하여 MSAL 코드를 편집한 후에는 사용자가 회사 네트워크 외부에 있는 경우에도 네이티브 클라이언트 애플리케이션에 인증할 수 있습니다.

## <a name="next-steps"></a>다음 단계

네이티브 애플리케이션 흐름에 대한 자세한 내용은 [Azure Active Directory의 네이티브 앱](../azuread-dev/native-app.md)을 참조하세요.

[Azure Active Directory의 애플리케이션에 대한 Single Sign-On](sso-options.md#choosing-a-single-sign-on-method) 설정 방법에 대해 알아봅니다.