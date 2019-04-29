---
title: Azure Active Directory v2.0 인증 라이브러리 | Microsoft Docs
description: Azure Active Directory v2.0 엔드포인트에 대한 호환 가능한 클라이언트 라이브러리 및 서버 미들웨어 라이브러리 및 관련된 라이브러리, 원본 및 샘플 링크.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ecac4122e8c7e016790cd816e4a858ffff47eb1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111195"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 인증 라이브러리

[Azure AD(Azure Active Directory) v2.0 엔드포인트](active-directory-v2-compare.md)는 산업 표준 OAuth 2.0 및 OpenID Connect 1.0 프로토콜을 지원합니다. MSAL(Microsoft Authentication Library)은 Azure AD v2.0 엔드포인트에서 작동하도록 설계되었습니다. 또한 OAuth 2.0 및 OpenID Connect 1.0을 지원하는 오픈 소스 라이브러리를 사용할 수도 있습니다.

[Microsoft에서 따르는 방법론][Microsoft-SDL]과 같은 SDL(보안 개발 수명 주기) 방법론을 따르는 프로토콜 도메인 전문가가 작성한 라이브러리를 사용하는 것이 좋습니다. 프로토콜에 대한 코드를 직접 지원하려는 경우 Microsoft의 SDL 방법론을 따르고 각 프로토콜에 대한 표준 사양의 보안 고려 사항에 세심한 주의를 기울여야 합니다.

> [!NOTE]
> ADAL(Azure AD v1.0 라이브러리)에 대한 내용을 찾으시나요? [ADAL 라이브러리 가이드](active-directory-authentication-libraries.md)를 참조하세요.

## <a name="types-of-libraries"></a>라이브러리 유형

Azure AD v2.0 엔드포인트에서는 두 가지 유형의 라이브러리를 사용할 수 있습니다.

* **클라이언트 라이브러리**: 네이티브 클라이언트 및 서버는 클라이언트 라이브러리를 사용하여 Microsoft Graph와 같은 리소스를 호출하기 위한 액세스 토큰을 가져옵니다.
* **서버 미들웨어 라이브러리**: 웹앱은 사용자 로그인에 서버 미들웨어 라이브러리를 사용합니다. 웹 API는 서버 미들웨어 라이브러리를 사용하여 네이티브 클라이언트 또는 다른 서버에서 전송되는 토큰의 유효성을 검사합니다.

## <a name="library-support"></a>라이브러리 지원

라이브러리는 두 지원 범주로 제공됩니다.

* **Microsoft 지원**: Microsoft는 이러한 라이브러리에 대한 수정 프로그램을 제공하며, 이러한 라이브러리에 대한 SDL 실사를 완료했습니다.
* **호환 가능**: Microsoft는 기본 시나리오에서 이러한 라이브러리를 테스트하고 v2.0 엔드포인트에서 작동하는지를 확인했습니다. Microsoft는 이러한 라이브러리에 대한 수정 사항을 제공하지 않으며 이러한 라이브러리의 검토를 완료하지 않았습니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다.

v2.0 엔드포인트를 사용하는 라이브러리 목록은 이 문서의 다음 섹션을 참조하세요.

## <a name="microsoft-supported-client-libraries"></a>Microsoft 지원 클라이언트 라이브러리

클라이언트 인증 라이브러리는 보호된 웹 API를 호출하는 토큰을 확보하는 데 사용됩니다.

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참조 | 개념 문서 | 로드맵 |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js(미리 보기) | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [단일 페이지 앱](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  | [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)|
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular(미리 보기) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL .NET(미리 보기) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [데스크톱 앱](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![iOS/Objective C 또는 swift](media/sample-v2-code/logo_iOS.png) | MSAL obj_c (미리 보기) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 앱](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL(미리 보기) | [중앙 리포지토리](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android 앱](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 지원 서버 미들웨어 라이브러리

미들웨어 라이브러리는 웹 애플리케이션 및 웹 API를 보호하는 데 사용됩니다. ASP.NET 또는 ASP.NET Core를 사용하여 작성된 웹앱 또는 웹 API의 경우 ASP.NET/ASP.NET Core에서 미들웨어 라이브러리가 사용됩니다.

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참조
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET 보안 |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC 앱](quickstart-v2-aspnet-webapp.md) |[ASP.NET API 참조](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| .NET용 IdentityModel 확장| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC 앱](quickstart-v2-aspnet-webapp.md) |[참조](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [웹앱](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>호환 가능한 클라이언트 라이브러리

| 플랫폼 | 라이브러리 이름 | 테스트 버전 | 소스 코드 | 샘플 |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![자바](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [버전 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![자바](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect 라이브러리](https://github.com/GluuFederation/oxAuth) | [Version 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect 라이브러리](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [버전 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Version 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [버전 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [React Native 앱 인증](https://github.com/FormidableLabs/react-native-app-auth) | [버전 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native 앱 인증](https://github.com/FormidableLabs/react-native-app-auth) | |

표준 준수 라이브러리의 경우 v2.0 끝점을 사용할 수 있으므로 지원하려는 위치를 알아야 합니다.

* 라이브러리 코드에서 문제 및 새 기능 요청은 라이브러리 소유자에게 문의합니다.
* 서비스 측 프로토콜 구현에서 문제 및 새 기능 요청은 Microsoft로 문의합니다.
* 프로토콜에서 확인하려는 추가 기능에 대한 [기능 요청을 작성합니다](https://feedback.azure.com/forums/169401-azure-active-directory).
* Azure AD v2.0 엔드포인트가 OAuth 2.0 또는 OpenID Connect 1.0과 호환되지 않는 문제가 발견되면 [지원 요청을 만듭니다](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="related-content"></a>관련 콘텐츠

Azure AD v2.0 엔드포인트에 대한 자세한 내용은 [Azure AD 앱 모델 v2.0 개요][AAD-App-Model-V2-Overview]를 참조하세요.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
