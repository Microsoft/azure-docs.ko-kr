---
title: Microsoft id 플랫폼 인증 라이브러리
description: Microsoft id 플랫폼 끝점에 대 한 관련 라이브러리, 소스 및 샘플 링크와 함께 호환 되는 클라이언트 라이브러리 및 서버 미들웨어 라이브러리
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 58b31618ad1edd3eb9d71a56529b8618edf4e43e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688490"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft id 플랫폼 인증 라이브러리

[Microsoft id 플랫폼 끝점](../azuread-dev/azure-ad-endpoint-comparison.md) 은 업계 표준 OAuth 2.0 및 openid connect Connect 1.0 프로토콜을 지원 합니다. MSAL (Microsoft 인증 라이브러리)은 Microsoft id 플랫폼 끝점에서 사용할 수 있도록 설계 되었습니다. OAuth 2.0 및 Openid connect Connect 1.0를 지 원하는 오픈 소스 라이브러리를 사용할 수도 있습니다.

SDL (보안 개발 수명 주기) 방법론을 따르는 프로토콜 도메인 전문가가 작성 한 라이브러리를 사용 하는 것이 좋습니다. 이러한 방법론 [은 Microsoft에서 따르는][Microsoft-SDL]것을 포함 합니다. 프로토콜에 대 한 코드를 직접 실행 하는 경우 Microsoft SDL과 같은 방법을 따라야 합니다. 각 프로토콜에 대 한 표준 사양에서 보안 고려 사항에 주의 하세요.

> [!NOTE]
> ADAL (Azure Active Directory Authentication Library)을 찾고 있나요? [ADAL 라이브러리 가이드](../azuread-dev/active-directory-authentication-libraries.md)를 확인 하세요.

## <a name="types-of-libraries"></a>라이브러리 유형

Microsoft id 플랫폼 끝점은 다음과 같은 두 가지 유형의 라이브러리와 함께 작동 합니다.

* **클라이언트 라이브러리**: 네이티브 클라이언트 및 서버는 클라이언트 라이브러리를 사용 하 여 Microsoft Graph 같은 리소스를 호출 하기 위한 액세스 토큰을 가져옵니다.
* **서버 미들웨어 라이브러리**: 웹앱은 사용자 로그인에 서버 미들웨어 라이브러리를 사용합니다. 웹 API는 서버 미들웨어 라이브러리를 사용하여 네이티브 클라이언트 또는 다른 서버에서 전송되는 토큰의 유효성을 검사합니다.

## <a name="library-support"></a>라이브러리 지원

라이브러리는 두 지원 범주로 제공됩니다.

* **Microsoft 지원**: Microsoft는 이러한 라이브러리에 대한 수정 프로그램을 제공하며, 이러한 라이브러리에 대한 SDL 실사를 완료했습니다.
* **호환 가능**: microsoft는 기본 시나리오에서 이러한 라이브러리를 테스트 하 고 microsoft id 플랫폼 끝점을 사용 하 고 있음을 확인 했습니다. Microsoft는 이러한 라이브러리에 대 한 픽스를 제공 하지 않으며 이러한 라이브러리에 대 한 검토를 수행 하지 않았습니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다.

Microsoft id 플랫폼 끝점을 사용 하는 라이브러리 목록은 다음 섹션을 참조 하십시오.

## <a name="microsoft-supported-client-libraries"></a>Microsoft 지원 클라이언트 라이브러리

클라이언트 인증 라이브러리를 사용 하 여 보호 된 웹 API를 호출 하기 위한 토큰을 가져옵니다.

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참조 | 개념 문서 | 로드맵 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [단일 페이지 앱](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [참조](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [개념 문서](msal-overview.md)| [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL 각도 | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [각도 SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [참조](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [개념 문서](msal-overview.md) | [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [데스크톱 앱](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[개념 문서](msal-overview.md) | [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [샘플](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [문서 읽기](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [참조](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS 및 macOS | MSAL iOS 및 macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 앱](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [macos 앱](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [참조](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [개념 문서](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [중앙 리포지토리](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android 앱](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [개념 문서](msal-overview.md) |[로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 지원 서버 미들웨어 라이브러리

미들웨어 라이브러리를 사용 하 여 웹 응용 프로그램 및 web Api를 보호 합니다. ASP.NET 또는 ASP.NET Core로 작성 된 웹 앱 또는 웹 Api는 미들웨어 라이브러리를 사용 합니다.

| 플랫폼 | 라이브러리 | 다운로드 | 소스 코드 | 샘플 | 참조
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET 보안 |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC 앱](quickstart-v2-aspnet-webapp.md) |[ASP.NET API 참조](/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| .NET용 IdentityModel 확장| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC 앱](quickstart-v2-aspnet-webapp.md) |[참조](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [웹앱](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Microsoft 지원 라이브러리-OS/언어

지원 되는 운영 체제 vs 언어의 용어로는 다음과 같은 매핑이 있습니다.

| 플랫폼    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Xamarin.ios) | MSAL.Net (Xamarin Android)|
| Swift <br> Objective-C |            |            | [iOS 및 macOS용 MSAL](msal-overview.md) | [iOS 및 macOS용 MSAL](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport. 노드 | Passport. 노드 | Passport. 노드 |

또한 [지원 되는 플랫폼 및 언어에의 한 시나리오](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages) 참조

## <a name="compatible-client-libraries"></a>호환 가능한 클라이언트 라이브러리

| 플랫폼 | 라이브러리 이름 | 테스트 버전 | 소스 코드 | 샘플 |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 버전 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[Vue](https://github.com/mvertopoulos/vue-msal) | 버전 3.0.3 |[vue-msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [버전 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu Openid connect Connect 라이브러리](https://github.com/GluuFederation/oxAuth) | [버전 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu Openid connect Connect 라이브러리](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [요청-OAuthlib](https://github.com/requests/requests-oauthlib) | [버전 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [요청-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid connect-클라이언트](https://github.com/panva/node-openid-client) | [버전 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid connect-클라이언트](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [버전 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [네이티브 앱 인증에 반응](https://github.com/FormidableLabs/react-native-app-auth) | [버전 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [네이티브 앱 인증에 반응](https://github.com/FormidableLabs/react-native-app-auth) | |

모든 표준 호환 라이브러리의 경우 Microsoft id 플랫폼 끝점을 사용할 수 있습니다. 지원 위치를 알아야 합니다.

* 라이브러리 코드에서 문제 및 새 기능 요청은 라이브러리 소유자에게 문의합니다.
* 서비스 측 프로토콜 구현에서 문제 및 새 기능 요청은 Microsoft로 문의합니다.
* 프로토콜에 표시 하려는 추가 기능에 대 한 [기능 요청을 파일](https://feedback.azure.com/forums/169401-azure-active-directory) 에 추가 합니다.
* Microsoft id 플랫폼 끝점이 OAuth 2.0 또는 Openid connect Connect 1.0과 호환 되지 않는 문제를 발견 [한 경우 지원 요청을 만듭니다](../../azure-portal/supportability/how-to-create-azure-support-request.md) .

## <a name="related-content"></a>관련 콘텐츠

Microsoft id 플랫폼 끝점에 대 한 자세한 내용은 [microsoft id 플랫폼 개요][AAD-App-Model-V2-Overview]를 참조 하세요.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
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
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
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