---
title: Microsoft ID 플랫폼의 코드 샘플
description: 시나리오별로 구성된 사용 가능한 Microsoft ID 플랫폼 코드 샘플의 인덱스를 제공합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dc8c498d80a25fce15577d6df96dc288c1201bf9
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108071814"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft ID 플랫폼 코드 샘플(v2.0 엔드포인트)

Microsoft ID 플랫폼을 사용하여 다음을 수행할 수 있습니다.

- 웹 애플리케이션 및 web API에 인증 및 권한 부여 추가.
- 보호되는 web API에 액세스하려면 액세스 토큰 요구.

이 문서에서는 Microsoft ID 플랫폼에 대해 간략하게 설명하고 샘플 링크를 제공합니다. 이 샘플은 애플리케이션에 사용할 수 있는 코드 조각과 함께 작업 수행 방법을 보여줍니다. 코드 샘플 페이지에서 요구 사항, 설치 및 설정에 도움이 되는 자세한 추가 정보 항목을 찾을 수 있습니다. 코드 안에 있는 주석은 중요한 섹션을 이해하는 데 도움이 됩니다.

각 샘플 형식에 대한 기본 시나리오를 이해하려면 [Microsoft ID 플랫폼에 대한 앱 형식](v2-app-types.md)을 참조하세요.

GitHub의 샘플에 참여할 수도 있습니다. 자세한 방법은 [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory)를 참조하세요.

## <a name="single-page-applications"></a>단일 페이지 애플리케이션

이 샘플에서는 Microsoft ID 플랫폼으로 보안이 설정된 단일 페이지 애플리케이션을 작성하는 방법을 보여 줍니다. 이 샘플은 MSAL.js의 특징 중 하나를 사용합니다.

| 플랫폼 | Description | 링크 |
| -------- | --------------------- | -------- |
| ![JavaScript 로고를 보여주는 이미지](media/sample-v2-code/logo_js.png) [JavaScript(MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA에서 인증 코드 흐름(PKCE 포함)을 사용하여 Microsoft Graph를 호출 |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![JavaScript 로고를 보여주는 이미지](media/sample-v2-code/logo_js.png) [JavaScript(MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA는 인증 코드 흐름(PKCE 포함)을 사용하여 B2C를 호출합니다. |[b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![JavaScript 로고를 보여주는 이미지](media/sample-v2-code/logo_js.png) [JavaScript(MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA는 Microsoft Graph를 호출하는 사용자 지정 웹 API를 호출합니다.  | [ms-identity-javascript-tutorial-chapter4-obo](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![Angular 로고를 보여주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA에서 Microsoft Graph 호출  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA에서 인증 코드 흐름(PKCE 포함)을 사용하여 Microsoft Graph를 호출 | [ms-identity-javascript-angular-spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA에서 사용자 지정 웹 API 호출 | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Angular 로고를 보여주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA에서 B2C 호출 |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA에서 앱 역할 및 보안 그룹을 사용하여 사용자 지정 웹 API 호출 |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![React 로고를 보여주는 이미지](media/sample-v2-code/logo_react.png) [React(MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA에서 인증 코드 흐름(PKCE 포함)을 사용하여 Microsoft Graph를 호출 | [ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![React 로고를 보여주는 이미지](media/sample-v2-code/logo_react.png) [React(MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA에서 사용자 지정 웹 API 호출 | [ms-identity-javascript-react-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api) |
| ![React 로고를 보여주는 이미지](media/sample-v2-code/logo_react.png) [React(MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA에서 사용자 지정 웹 API를 호출, 이는 Microsoft Graph 호출  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Blazor 로고를 보여주는 이미지](media/sample-v2-code/logo-blazor.png) [Blazor WebAssembly(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Azure Active Directory를 사용하여 사용자를 로그인하고 API를 호출하기 위한 Blazor WebAssembly 자습서 |[ms-identity-blazor-wasm](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>웹 애플리케이션

다음 샘플은 사용자를 로그인하는 웹 애플리케이션을 보여 줍니다. 일부 샘플은 사용자 ID를 사용하여 Microsoft Graph 또는 사용자 고유의 웹 API를 호출하는 애플리케이션도 보여 줍니다.

| 플랫폼 | 사용자만 로그인 | 사용자를 로그인하고 Microsoft Graph를 호출 |
| -------- | ------------------- | --------------------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core WebApp 로그인 사용자 자습서](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core 웹앱에서 Microsoft Graph 호출](https://aka.ms/aspnetcore-webapp-call-msgraph) 단계와 동일한 샘플</p>고급 샘플 [백그라운드 앱, API 및 서비스에서 로그인한 사용자의 토큰 캐시에 액세스](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) |
| ![이 이미지는 ASP.NET Framework 로고를 보여줍니다.](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [개발자를 위한 AD FS에서 Azure AD로의 애플리케이션 마이그레이션 플레이북](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad): AD FS(Active Directory Federation Services)와 통합된 애플리케이션을 Azure AD(Azure Active Directory)로 안전하게 마이그레이션하는 방법을 알아봅니다. | |
| ![이 이미지는 ASP.NET Framework 로고를 보여줍니다.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 빠른 시작](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> [msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) |
| ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png)  |[Java Servlet 자습서 - 1.1장](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD로 로그인| |
| ![이 이미지는 Java 로고를 보여 줍니다.](media/sample-v2-code/logo_java.png)  |[Java Servlet 자습서 - 1.2장](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) B2C로 로그인 |
| ![이 이미지는 Java 로고를 보여 줍니다.](media/sample-v2-code/logo_java.png)  | | [Java Servlet 자습서 - 2.1장](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD로 로그인하고 Graph 호출|
| ![이 이미지는 Java 로고를 보여 줍니다.](media/sample-v2-code/logo_java.png)  |[Java Servlet 자습서 - 3.1장](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD로 로그인하고 역할 클레임으로 액세스 제어| |
| ![이 이미지는 Java 로고를 보여 줍니다.](media/sample-v2-code/logo_java.png)  | | [Java Servlet 자습서 - 3.2장](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD로 로그인하고 그룹 클레임으로 액세스 제어|
| ![이 이미지는 Java 로고를 보여 줍니다.](media/sample-v2-code/logo_java.png) | |[Java Servlet 자습서 - 4.1장](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Azure App Service에 배포|
| ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png)  | | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png)  | [ms-identity-b2c-java-servlet-webapp-authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(MSAL 노드) | [기본 웹앱 로그인 사용자 자습서](https://github.com/Azure-Samples/ms-identity-node) | |
| ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png)  | [Python Flask 자습서 - 1.1장](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) AAD로 로그인  |  |
| ![이 이미지는 Python 로고를 보여 줍니다.](media/sample-v2-code/logo_python.png)  | [Python Flask 자습서 - 1.2장](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) B2C로 로그인                    |  |
| ![이 이미지는 Python 로고를 보여 줍니다.](media/sample-v2-code/logo_python.png)  | | [Python Flask 자습서 - 2.1장](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) AAD로 로그인하고 Graph 호출 |
| ![이 이미지는 Python 로고를 보여 줍니다.](media/sample-v2-code/logo_python.png)  | |[Python Flask 자습서 - 3.1장](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Azure App Service에 배포  |
| ![이 이미지는 Python 로고를 보여 줍니다.](media/sample-v2-code/logo_python.png)  | [Python Django 자습서 - 1.1장](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) AAD로 로그인  | |
| ![이 이미지는 Python 로고를 보여 줍니다.](media/sample-v2-code/logo_python.png)  | [Python Django 자습서 - 1.2장](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) B2C로 로그인                    |  |
| ![이 이미지는 Python 로고를 보여 줍니다.](media/sample-v2-code/logo_python.png)  | | [Python Django 자습서 - 2.1장](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) AAD로 로그인하고 Graph 호출|
| ![이 이미지는 Python 로고를 보여 줍니다.](media/sample-v2-code/logo_python.png)  | | [Python Django 자습서 - 3.1장](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Azure App Service에 배포                    |
| ![이 이미지는 Python 로고를 보여 줍니다.](media/sample-v2-code/logo_python.png)  | | [Python Flask 웹앱](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![이 이미지는 Ruby 로고를 표시](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![이 이미지는 Blazor 로고를 보여줍니다.](media/sample-v2-code/logo-blazor.png)</p>Blazor 서버 | [Blazor Server 앱 로그인 사용자 자습서](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [Blazor Server 앱 호출 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Chapterwise 자습서: [Azure Active Directory를 사용하여 사용자를 로그인하고 API를 호출하는 Blazor Server 앱](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>데스크톱 및 모바일 공용 클라이언트 앱

다음 샘플은 사용자 이름으로 Microsoft Graph API 또는 사용자 고유의 Web API에 액세스하는 퍼블릭 클라이언트 애플리케이션(데스크톱/모바일 애플리케이션)을 보여 줍니다. *WAM을 사용하는 데스크톱(콘솔)* 샘플 외에도 이러한 클라이언트 애플리케이션은 모두 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET Core Web API 호출 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 바탕 화면 자습서(.NET Core) - 필요에 따라 다음을 사용합니다.</p>- 플랫폼 간 토큰 캐시</p>- 사용자 지정 웹 UI | ![이 이미지는 .NET/C# 로고를 보여 줍니다.](media/sample-v2-code/logo_NETcore.png) | [인증 코드](msal-authentication-flows.md#authorization-code)| [ms-identity-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| 데스크톱(WPF)      | ![이 이미지는 .NET 데스크톱/C# 로고를 보여줍니다.](media/sample-v2-code/logo_NET.png) | [인증 코드](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 데스크톱(콘솔)   | ![.NET/C#(데스크톱) 로고를 보여주는 이미지](media/sample-v2-code/logo_NET.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![.NET/C#(데스크톱) 로고입니다.](media/sample-v2-code/logo_NETcore.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM을 사용하는 데스크톱(콘솔)  | ![.NET/C#(데스크톱)의 로고입니다.](media/sample-v2-code/logo_NETcore.png) | [웹 계정 관리자](/windows/uwp/security/web-account-manager)(WAM)를 사용하는 대화형 |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| 데스크톱(전자)   | ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(MSAL 노드) | [인증 코드(PKCE)](msal-authentication-flows.md#authorization-code) |[ms-identity-javascript-nodejs-desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| 모바일(Android, iOS, UWP)   | ![이 이미지는 .NET/C#(Xamarin) 로고를 표시](media/sample-v2-code/logo_xamarin.png) | [인증 코드](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| 모바일(iOS)       | ![이 이미지는 iOS/Objective-C 또는 Swift를 표시](media/sample-v2-code/logo_iOS.png) | [인증 코드](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| 데스크톱(macOS)       | macOS | [인증 코드](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| 모바일(Android-Java)   | ![이 이미지는 Android 로고를 보여 줍니다.](media/sample-v2-code/logo_Android.png) | [인증 코드](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 모바일(Android-Kotlin)   | ![이 이미지는 Android 로고를 보여 줍니다.](media/sample-v2-code/logo_Android.png) | [인증 코드](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>디먼 애플리케이션

다음 샘플은 고유 ID(사용자 없음)로 Microsoft Graph API에 액세스하는 애플리케이션을 보여 줍니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- | ---------- | -------------------- |
| 콘솔 | ![이 이미지는 .NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| 웹앱 | ![ASP.NET 로고를 보여주는 스크린샷.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| 콘솔 | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 콘솔 | ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(MSAL 노드)| [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-identity-javascript-nodejs-console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| 콘솔 | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>헤드리스 애플리케이션

다음 샘플은 웹 브라우저가 없는 디바이스에서 실행되는 공용 클라이언트 애플리케이션을 보여 줍니다. 앱은 명령줄 도구이거나 Linux나 Mac에서 실행되는 앱 또는 IoT 애플리케이션일 수 있습니다. 샘플에는 다른 디바이스(예: 휴대폰)에서 대화형으로 로그인하는 사용자 이름으로 Microsoft Graph API에 액세스하는 앱이 있습니다. 이 클라이언트 애플리케이션은 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- |  ----------| ---------- |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C#(데스크톱) 로고를 표시](media/sample-v2-code/logo_NETcore.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 데스크톱(콘솔)   | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>다중 테넌트 SaaS 애플리케이션

다음 샘플에서는 모든 Azure AD(Azure Active Directory) 테넌트에서 로그인을 허용하도록 애플리케이션을 구성하는 방법을 보여 줍니다. *다중 테넌트* 가 되도록 애플리케이션을 구성하면 **SaaS(Software as a Service)** 애플리케이션을 많은 조직에 제공할 수 있으므로 해당 동의한 후에 애플리케이션에 로그인할 수 있습니다.

| 플랫폼 | Description | 링크 |
| -------- | --------------------- | -------- |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | 다중 테넌트 SPA에서 다중 테넌트 사용자 지정 웹 API 호출 |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![ASP.NET Core 로고를 보여 주는 이미지](media/sample-v2-code/logo_NETcore.png) [.NET Core(MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC 웹 애플리케이션 호출 Graph API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![ASP.NET Core 로고를 보여 주는 이미지](media/sample-v2-code/logo_NETcore.png) [.NET Core(MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC 웹 애플리케이션 호출 ASP.NET Core API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API

다음 샘플에서는 Microsoft ID 플랫폼을 사용하여 웹 API를 보호하는 방법과 웹 API에서 다운스트림 API를 호출하는 방법을 보여줍니다.

| 플랫폼 | 샘플 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)의 ASP.NET Core Web API(서비스)  |
| ![이 이미지는 ASP.NET 로고를 표시](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof)의 Web API(서비스) |
| ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(Passport.js)| [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(Passport.js)| [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)의 B2C Web API(서비스) |

## <a name="azure-functions-as-web-apis"></a>Web API로의 Azure Functions

다음 샘플에서는 Microsoft ID 플랫폼을 사용하여 웹 API를 노출하고 HttpTrigger를 사용하여 Azure 함수를 보호하는 방법 및 웹 API에서 다운스트림 API를 호출하는 방법을 보여줍니다.

| 플랫폼 | 샘플 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)의 ASP.NET Core Web API(서비스) Azure 함수  |
| ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(Passport.js)| [Node.js 및 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(Passport.js)| [다음을 대신 사용하는 Node.js 및 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions)의 Web API(서비스) |

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 [샘플](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) 및 자습서에 대한 내용은 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참고 항목

[Microsoft Graph API 개념 및 참조](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
