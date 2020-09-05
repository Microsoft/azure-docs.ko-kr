---
title: 기밀 클라이언트 앱 인스턴스화 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET)을 사용 하 여 구성 옵션을 사용 하 여 기밀 클라이언트 응용 프로그램을 인스턴스화하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 140385a26dbfd754438d632ce327c3c4a3ea1b61
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166181"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET를 사용 하 여 구성 옵션을 사용 하 여 기밀 클라이언트 응용 프로그램 인스턴스화

이 문서에서는 .NET 용 Microsoft Authentication Library (MSAL.NET)를 사용 하 여 [기밀 클라이언트 응용 프로그램](msal-client-applications.md) 을 인스턴스화하는 방법을 설명 합니다.  응용 프로그램은 설정 파일에 정의 된 구성 옵션을 사용 하 여 인스턴스화됩니다.

응용 프로그램을 초기화 하려면 먼저 앱이 Microsoft id 플랫폼과 통합 될 수 있도록 해당 응용 프로그램을 [등록](quickstart-register-app.md) 해야 합니다. 등록 후에는 다음 정보가 필요할 수 있습니다 (Azure Portal 참조).

- 클라이언트 ID (GUID를 나타내는 문자열)
- 응용 프로그램에 대 한 id 공급자 URL (인스턴스 이름) 및 로그인 대상이 됩니다. 이러한 두 매개 변수를 통칭 하 여 기관 이라고 합니다.
- 조직 전용 lob (단일 테 넌 트 응용 프로그램) 응용 프로그램을 작성 하는 경우 테 넌 트 ID입니다.
- 기밀 클라이언트 앱 인 경우 응용 프로그램 암호 (클라이언트 암호 문자열) 또는 인증서 (X509Certificate2 형식)입니다.
- 웹 앱 및 공용 클라이언트 앱의 경우 (특히 앱에서 broker를 사용 해야 하는 경우) id 공급자가 응용 프로그램에 보안 토큰을 다시 연결 하는 redirectUri도 설정 해야 합니다.

## <a name="configure-the-application-from-the-config-file"></a>구성 파일에서 응용 프로그램 구성
MSAL.NET에 있는 옵션의 속성 이름은 ASP.NET Core의 속성 이름과 일치 `AzureADOptions` 하므로 glue 코드를 작성할 필요가 없습니다.

ASP.NET Core 응용 프로그램 구성은 파일 *의appsettings.js* 에 설명 되어 있습니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

MSAL.NET v3. x부터 구성 파일에서 기밀 클라이언트 응용 프로그램을 구성할 수 있습니다.

응용 프로그램을 구성 하 고 인스턴스화할 클래스에서 개체를 선언 해야 `ConfidentialClientApplicationOptions` 합니다.  Microsoft.Extensions.Configuration의 메서드를 사용 하 여 소스 (파일에 appconfig.js포함)에서 응용 프로그램 옵션 인스턴스로 읽은 구성을 바인딩합니다 `IConfigurationRoot.Bind()` [. 바인더 nuget 패키지](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

이렇게 하면 *appsettings.js파일에* 있는 "AzureAD" 섹션의 내용이 개체의 해당 속성에 바인딩될 수 있습니다 `ConfidentialClientApplicationOptions` .  다음으로 개체를 빌드합니다 `ConfidentialClientApplication` .

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>런타임 구성 추가
기밀 클라이언트 응용 프로그램에서는 일반적으로 사용자 당 캐시가 있습니다. 따라서 사용자와 연결 된 캐시를 가져와서 사용 하려는 응용 프로그램 빌더를 알려 주어 야 합니다. 동일한 방식으로 동적으로 계산 된 리디렉션 URI가 있을 수 있습니다. 이 경우 코드는 다음과 같습니다.

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

