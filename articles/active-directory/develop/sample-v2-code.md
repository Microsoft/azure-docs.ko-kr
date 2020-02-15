---
title: Microsoft id 플랫폼에 대 한 코드 샘플 | Microsoft Docs
description: 시나리오 별로 구성 된 사용 가능한 v2.0 끝점 (Microsoft identity platform) 코드 샘플의 인덱스를 제공 합니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0282336cb5df7d010571a15e9d1f9d93b82d461e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160325"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft id 플랫폼 코드 샘플 (v2.0 끝점)

Microsoft ID 플랫폼을 사용하여 다음을 수행할 수 있습니다.

- 웹 애플리케이션 및 web API에 인증 및 권한 부여 추가.
- 보호되는 web API에 액세스하려면 액세스 토큰 요구.

이 문서에서는 Microsoft id 플랫폼 끝점에 대 한 샘플에 대 한 링크를 간략하게 설명 하 고 제공 합니다. 이러한 샘플은 작업을 수행 하는 방법을 보여 주고 응용 프로그램에서 사용할 수 있는 코드 조각도 제공 합니다. 코드 샘플 페이지에는 요구 사항, 설치 및 설정에 도움이 되는 자세한 추가 정보 항목을 찾을 수 있습니다. 코드 내의 주석은 중요 한 부분을 이해 하는 데 도움이 됩니다.

> [!NOTE]
> V1.0 샘플에 관심이 있는 경우 [AZURE AD 코드 샘플 (v1.0 끝점)](../azuread-dev/sample-v1-code.md)을 참조 하세요.

각 샘플 형식에 대 한 기본 시나리오를 이해 하려면 [Microsoft id 플랫폼 끝점에 대 한 앱 유형](v2-app-types.md)을 참조 하세요.

GitHub의 샘플에 참여할 수도 있습니다. 자세한 방법은 [Microsoft Azure Active Directory 샘플 및 설명서](https://github.com/Azure-Samples?page=3&query=active-directory)를 참조하세요.

## <a name="single-page-applications"></a>단일 페이지 애플리케이션

이러한 샘플은 Microsoft id 플랫폼을 사용 하 여 보호 되는 단일 페이지 응용 프로그램을 작성 하는 방법을 보여 줍니다. 이러한 샘플은 MSAL의 특징 중 하나를 사용 합니다.

| 플랫폼 | Description | 링크 |
| -------- | --------------------- | -------- |
| 이 이미지 ![javascript 로고](media/sample-v2-code/logo_js.png) [javascript (msal .js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) 를 보여 줍니다. | Microsoft Graph 호출 |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| 이 이미지 ![javascript 로고](media/sample-v2-code/logo_js.png) [javascript (msal .js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) 를 보여 줍니다. | B2C 호출 |[b2c-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| 이 이미지 ![javascript 로고](media/sample-v2-code/logo_js.png) [javascript (msal .js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) 를 보여 줍니다. | 자체 web API를 호출 합니다. |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| 이 이미지 ![[MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)](media/sample-v2-code/logo_angular.png)의 각도 JS 로고를 표시 합니다.| Microsoft Graph 호출  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| 이 이미지 ![JavaScript](media/sample-v2-code/logo_angular.png)의 각도 로고 [(MSAL 각도)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) 를 보여 줍니다.| Microsoft Graph 호출  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>웹 애플리케이션

다음 샘플은 사용자를 로그인하는 웹 애플리케이션을 보여 줍니다. 일부 샘플은 사용자 ID를 사용하여 Microsoft Graph 또는 사용자 고유의 웹 API를 호출하는 애플리케이션도 보여 줍니다.

| 플랫폼 | 사용자만 로그인 | 사용자를 로그인하고 Microsoft Graph를 호출 |
| -------- | ------------------- | --------------------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시 합니다.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [ASP.NET Core WebApp 로그인 사용자 자습서](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core 웹 앱에서 Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) 단계를 호출 하는 동일한 샘플 |
| ![이 이미지는 ASP.NET 로고를 보여줍니다.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 빠른 시작](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![이 이미지는 Java 로고를 표시 합니다.](media/sample-v2-code/logo_java.png)  |                   | [webapp--id](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![이 이미지는 Python 로고를 표시 합니다.](media/sample-v2-code/logo_python.png)  |                   | [webapp-python](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![이 이미지는 node.js 로고를 표시 합니다.](media/sample-v2-code/logo_nodejs.png)  |                   | [Node.js 빠른 시작](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![이 이미지는 Ruby 로고를 표시 합니다.](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>데스크톱 및 모바일 공용 클라이언트 앱

다음 샘플에서는 Microsoft Graph API에 액세스 하는 공용 클라이언트 응용 프로그램 (데스크톱 또는 모바일 응용 프로그램) 또는 사용자의 이름을 사용 하는 고유한 web API를 보여 줍니다. 이러한 모든 클라이언트 응용 프로그램은 MSAL (Microsoft 인증 라이브러리)을 사용 합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET Core 2.0 웹 API를 호출 합니다. |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 데스크톱(WPF)      | ![이 이미지는 .NET/C# 로고를 보여줍니다.](media/sample-v2-code/logo_NET.png) | [대화형](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C# (데스크톱) 로고를 보여줍니다.](media/sample-v2-code/logo_NET.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시 합니다.](media/sample-v2-code/logo_java.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) |[ms-id-java-데스크톱](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C# (데스크톱) 로고를 보여줍니다.](media/sample-v2-code/logo_NETcore.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM를 사용 하는 데스크톱 (콘솔)  | ![이 이미지는 .NET/C# (데스크톱) 로고를 보여줍니다.](media/sample-v2-code/logo_NETcore.png) | [WAM와 interactive](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시 합니다.](media/sample-v2-code/logo_java.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[ms-id-java-데스크톱](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Python 로고를 표시 합니다.](media/sample-v2-code/logo_python.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[m i d-python-데스크톱](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| 모바일(Android, iOS, UWP)   | ![이 이미지는 .NET/C# (Xamarin) 로고를 보여줍니다.](media/sample-v2-code/logo_xamarin.png) | [대화형](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| 모바일(iOS)       | ![이 이미지는 iOS/목표-C 또는 Swift를 보여 줍니다.](media/sample-v2-code/logo_iOS.png) | [대화형](msal-authentication-flows.md#interactive) |[swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| 데스크톱 (macOS)       | macOS | [대화형](msal-authentication-flows.md#interactive) |[macOS-swift-v c-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| 모바일 (Android-Java)   | ![이 이미지는 Android 로고를 표시 합니다.](media/sample-v2-code/logo_Android.png) | [대화형](msal-authentication-flows.md#interactive) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 모바일 (Android-Kotlin)   | ![이 이미지는 Android 로고를 표시 합니다.](media/sample-v2-code/logo_Android.png) | [대화형](msal-authentication-flows.md#interactive) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>디먼 애플리케이션

다음 샘플은 고유 ID(사용자 없음)로 Microsoft Graph API에 액세스하는 애플리케이션을 보여 줍니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- | ---------- | -------------------- |
| 콘솔 | ![이 이미지는 .NET Core 로고를 보여줍니다.](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| 웹앱 | ![이 이미지는 ASP.NET 로고를 보여줍니다.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| 콘솔 | ![이 이미지는 Java 로고를 표시 합니다.](media/sample-v2-code/logo_java.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-id-java-디먼](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 콘솔 | ![이 이미지는 Python 로고를 표시 합니다.](media/sample-v2-code/logo_python.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-chap-python-디먼](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>헤드리스 애플리케이션

다음 샘플은 웹 브라우저가 없는 디바이스에서 실행되는 공용 클라이언트 애플리케이션을 보여 줍니다. 앱은 명령줄 도구, Linux 또는 Mac에서 실행 되는 앱 또는 IoT 응용 프로그램 일 수 있습니다. 이 샘플은 다른 장치 (예: 휴대폰)에서 대화형으로 로그인 한 사용자의 이름으로 Microsoft Graph API에 액세스 하는 앱의 기능을 제공 합니다. 이 클라이언트 응용 프로그램은 MSAL (Microsoft 인증 라이브러리)을 사용 합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- |  ----------| ---------- |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C# (데스크톱) 로고를 보여줍니다.](media/sample-v2-code/logo_NETcore.png) | [장치 코드 흐름](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시 합니다.](media/sample-v2-code/logo_java.png) | [장치 코드 흐름](msal-authentication-flows.md#device-code) |[devicecodeflow--id](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 데스크톱(콘솔)   | ![이 이미지는 Python 로고를 표시 합니다.](media/sample-v2-code/logo_python.png) | [장치 코드 흐름](msal-authentication-flows.md#device-code) |[devicecodeflow-python](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Web API

다음 샘플에서는 Microsoft id 플랫폼 끝점을 사용 하 여 web API를 보호 하는 방법 및 web API에서 다운스트림 API를 호출 하는 방법을 보여 줍니다.

| 플랫폼 | 샘플 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시 합니다.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) 의 ASP.NET CORE web API (서비스)  |
| ![이 이미지는 ASP.NET 로고를 보여줍니다.](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [Webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) 웹 API (서비스) |
| ![이 이미지는 Java 로고를 표시 합니다.](media/sample-v2-code/logo_java.png) | 웹 API (서비스) ( [webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) ) |

## <a name="azure-functions-as-web-apis"></a>웹 Api로 Azure Functions

다음 샘플에서는 HttpTrigger를 사용 하 여 Azure 함수를 보호 하 고 Microsoft id 플랫폼 끝점을 사용 하 여 web API를 노출 하는 방법과 web API에서 다운스트림 API를 호출 하는 방법을 보여 줍니다.

| 플랫폼 | 샘플 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시 합니다.](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core web API (서비스) Azure Function ( [dotnet-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) )  |
| ![이 이미지는 node.js 로고를 표시 합니다.](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Nodejs 및 passport의 웹 API (서비스) [-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![이 이미지는 Python 로고를 표시 합니다.](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) 의 Web API (서비스) |
| ![이 이미지는 node.js 로고를 표시 합니다.](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [다음을 대신 사용 하 여 Nodejs 및 passport](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) 의 웹 API (서비스) |

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 [샘플](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) 및 자습서에 대한 내용은 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure Active Directory (v1.0) 개발자 가이드](../azuread-dev/v1-overview.md)
- [Azure AD Graph API 개념 및 참조](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD Graph API 도우미 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
