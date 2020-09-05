---
title: Microsoft ID 플랫폼의 코드 샘플
description: 시나리오별로 구성된 사용 가능한 Microsoft ID 플랫폼(v2.0 엔드포인트) 코드 샘플의 인덱스를 제공합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 06/01/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 142037366e9309f8087a2e42fa46cd0b4137f6b8
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119405"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft ID 플랫폼 코드 샘플(v2.0 엔드포인트)

Microsoft ID 플랫폼을 사용하여 다음을 수행할 수 있습니다.

- 웹 애플리케이션 및 web API에 인증 및 권한 부여 추가.
- 보호되는 web API에 액세스하려면 액세스 토큰 요구.

이 문서에서는 Microsoft ID 플랫폼 엔드포인트에 대해 간략하게 설명하고 샘플 링크를 제공합니다. 이 샘플은 애플리케이션에 사용할 수 있는 코드 조각과 함께 작업 수행 방법을 보여줍니다. 코드 샘플 페이지에서 요구 사항, 설치 및 설정에 도움이 되는 자세한 추가 정보 항목을 찾을 수 있습니다. 코드 안에 있는 주석은 중요한 섹션을 이해하는 데 도움이 됩니다.

> [!NOTE]
> v1.0 샘플에 관심이 있는 경우 [Azure AD 코드 샘플(v1.0 엔드포인트)](../azuread-dev/sample-v1-code.md)을 참조하세요.

각 샘플 형식에 대한 기본 시나리오를 이해하려면 [Microsoft ID 플랫폼 엔드포인트에 대한 앱 형식](v2-app-types.md)을 참조하세요.

GitHub의 샘플에 참여할 수도 있습니다. 자세한 방법은 [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory)를 참조하세요.

## <a name="single-page-applications"></a>단일 페이지 애플리케이션

이 샘플에서는 Microsoft ID 플랫폼으로 보안이 설정된 단일 페이지 애플리케이션을 작성하는 방법을 보여 줍니다. 이 샘플은 MSAL.js의 특징 중 하나를 사용합니다.

| 플랫폼 | Description | 링크 |
| -------- | --------------------- | -------- |
| ![JavaScript 로고를 보여 주는 이미지](media/sample-v2-code/logo_js.png) [JavaScript(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA에서 Microsoft Graph 호출 |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![JavaScript 로고를 보여 주는 이미지](media/sample-v2-code/logo_js.png) [JavaScript(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA에서 인증 코드 흐름(PKCE 포함)을 사용하여 Microsoft Graph를 호출 |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![JavaScript 로고를 보여 주는 이미지](media/sample-v2-code/logo_js.png) [JavaScript(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA에서 B2C 호출 |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [JavaScript(MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA에서 Microsoft Graph 호출  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA에서 사용자 지정 Web API 호출 | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA에서 B2C 호출 |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![React 로고를 보여 주는 이미지](media/sample-v2-code/logo_react.png) [React(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA에서 사용자 지정 웹 API를 호출, 이는 Microsoft Graph 호출  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA에서 앱 역할 및 보안 그룹을 사용하여 사용자 지정 웹 API 호출 |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>웹 애플리케이션

다음 샘플은 사용자를 로그인하는 웹 애플리케이션을 보여 줍니다. 일부 샘플은 사용자 ID를 사용하여 Microsoft Graph 또는 사용자 고유의 웹 API를 호출하는 애플리케이션도 보여 줍니다.

| 플랫폼 | 사용자만 로그인 | 사용자를 로그인하고 Microsoft Graph를 호출 |
| -------- | ------------------- | --------------------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core WebApp 로그인 사용자 자습서](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core 웹앱에서 Microsoft Graph 호출](https://aka.ms/aspnetcore-webapp-call-msgraph) 단계와 동일한 샘플 |
| ![이 이미지는 ASP.NET 로고를 표시](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 빠른 시작](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![이 이미지는 Ruby 로고를 표시](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>데스크톱 및 모바일 공용 클라이언트 앱

다음 샘플은 사용자 이름으로 Microsoft Graph API 또는 사용자 고유의 Web API에 액세스하는 퍼블릭 클라이언트 애플리케이션(데스크톱/모바일 애플리케이션)을 보여 줍니다. *WAM을 사용하는 데스크톱(콘솔)* 샘플 외에도 이러한 클라이언트 애플리케이션은 모두 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET Core Web API 호출 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 데스크톱(WPF)      | ![이 이미지는 .NET/C# 로고를 보여 줍니다.](media/sample-v2-code/logo_NET.png) | [인증 코드](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C#(데스크톱) 로고를 표시](media/sample-v2-code/logo_NET.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C#(데스크톱) 로고를 표시](media/sample-v2-code/logo_NETcore.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM을 사용하는 데스크톱(콘솔)  | ![이 이미지는 .NET/C#(데스크톱) 로고를 표시](media/sample-v2-code/logo_NETcore.png) | [웹 계정 관리자](/windows/uwp/security/web-account-manager)(WAM)를 사용하는 대화형 |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
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
| 웹앱 | ![이 이미지는 ASP.NET 로고를 표시](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| 콘솔 | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 콘솔 | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>헤드리스 애플리케이션

다음 샘플은 웹 브라우저가 없는 디바이스에서 실행되는 공용 클라이언트 애플리케이션을 보여 줍니다. 앱은 명령줄 도구이거나 Linux나 Mac에서 실행되는 앱 또는 IoT 애플리케이션일 수 있습니다. 샘플에는 다른 디바이스(예: 휴대폰)에서 대화형으로 로그인하는 사용자 이름으로 Microsoft Graph API에 액세스하는 앱이 있습니다. 이 클라이언트 애플리케이션은 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- |  ----------| ---------- |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C#(데스크톱) 로고를 표시](media/sample-v2-code/logo_NETcore.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 보여 줍니다.](media/sample-v2-code/logo_java.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 데스크톱(콘솔)   | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>다중 테넌트 SaaS 애플리케이션

다음 샘플에서는 모든 Azure AD(Azure Active Directory) 테넌트에서 로그인을 허용하도록 애플리케이션을 구성하는 방법을 보여 줍니다. *다중 테넌트*가 되도록 애플리케이션을 구성하면 **SaaS(Software as a Service)** 애플리케이션을 많은 조직에 제공할 수 있으므로 사용자가 자신의 계정을 사용하도록 동의한 후에 애플리케이션에 로그인할 수 있습니다.

| 플랫폼 | Description | 링크 |
| -------- | --------------------- | -------- |
| ![JavaScript 로고를 보여 주는 이미지](media/sample-v2-code/logo_js.png) [JavaScript(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | 다중 테넌트 SPA 호출 Graph API |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | 다중 테넌트 SPA에서 다중 테넌트 사용자 지정 Web API 호출 |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![ASP.NET Core 로고를 보여 주는 이미지](media/sample-v2-code/logo_NETcore.png) [.NET Core(MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC 웹 애플리케이션 호출 Graph API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![ASP.NET Core 로고를 보여 주는 이미지](media/sample-v2-code/logo_NETcore.png) [.NET Core(MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC 웹 애플리케이션 호출 ASP.NET Core API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API

다음 샘플에서는 Microsoft ID 플랫폼 엔드포인트를 사용하여 Web API를 보호하는 방법 및 Web API에서 다운스트림 API를 호출하는 방법을 보여 줍니다.

| 플랫폼 | 샘플 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)의 ASP.NET Core Web API(서비스)  |
| ![이 이미지는 ASP.NET 로고를 표시](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof)의 Web API(서비스) |
| ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png) | [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png) | [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)의 B2C Web API(서비스) |

## <a name="azure-functions-as-web-apis"></a>Web API로의 Azure Functions

다음 샘플에서는 Microsoft ID 플랫폼 엔드포인트를 사용하여 Web API를 노출하고 HttpTrigger를 사용하여 Azure 함수를 보호하는 방법 및 Web API에서 다운스트림 API를 호출하는 방법을 보여 줍니다.

| 플랫폼 | 샘플 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)의 ASP.NET Core Web API(서비스) Azure 함수  |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [NodeJS 및 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)의 Web API(서비스) |
| ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [다음을 대신 사용한 NodeJS 및 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions)의 Web API(서비스) |

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 [샘플](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) 및 자습서에 대한 내용은 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure Active Directory(v1.0) 개발자 가이드](../azuread-dev/v1-overview.md)
- [Microsoft Graph API 개념 및 참조](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta)