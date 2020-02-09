---
title: 보호 된 웹 API 앱 구성 | Microsoft
titleSuffix: Microsoft identity platform
description: 보호 된 웹 API를 빌드하고 응용 프로그램의 코드를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 975117ad5c58bed77002a33f0dc5370d0f1c17e2
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931474"
---
# <a name="protected-web-api-code-configuration"></a>보호 된 웹 API: 코드 구성

보호 된 web API에 대 한 코드를 구성 하려면 다음을 이해 해야 합니다.

- 보호 되는 Api를 정의 합니다.
- 전달자 토큰을 구성 하는 방법
- 토큰의 유효성을 검사 하는 방법입니다.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>ASP.NET 및 ASP.NET Core Api를 보호 된 것으로 정의 하는 것은 무엇 인가요?

웹 앱과 마찬가지로 ASP.NET 및 ASP.NET Core 웹 Api는 해당 컨트롤러 작업에 **[권한 부여]** 특성이 접두사로 추가 되기 때문에 보호 됩니다. 인증 된 id를 사용 하 여 API를 호출 하는 경우에만 컨트롤러 작업을 호출할 수 있습니다.

다음 질문을 고려 합니다.

- 앱만 웹 API를 호출할 수 있습니다. API는 API를 호출 하는 앱의 id를 어떻게 알 수 있나요?
- 앱이 사용자를 대신 하 여 API를 호출 하는 경우 사용자의 id는 무엇 인가요?

## <a name="bearer-token"></a>전달자 토큰

앱이 호출 될 때 헤더에 설정 된 전달자 토큰에는 앱 id에 대 한 정보가 포함 됩니다. 웹 앱이 디먼 앱에서 서비스 간 호출을 허용 하지 않는 한 사용자에 대 한 정보도 포함 됩니다.

다음은 .NET C# 용 Microsoft 인증 라이브러리 (MSAL.NET)를 사용 하 여 토큰을 얻은 후 API를 호출 하는 클라이언트를 보여 주는 코드 예제입니다.

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> 클라이언트 응용 프로그램은 *웹 API에 대 한*Microsoft id 플랫폼 엔드포인트에 전달자 토큰을 요청 합니다. 웹 API는 토큰을 확인 하 고 포함 된 클레임을 확인 해야 하는 유일한 응용 프로그램입니다. 클라이언트 앱은 토큰의 클레임을 검사 해 서 사용해 서는 안 됩니다.
>
> 이후에 웹 API에서 토큰을 암호화 해야 할 수도 있습니다. 이 요구 사항은 액세스 토큰을 볼 수 있는 클라이언트 앱에 대 한 액세스를 방지 합니다.

## <a name="jwtbearer-configuration"></a>JwtBearer 구성

이 섹션에서는 전달자 토큰을 구성 하는 방법을 설명 합니다.

### <a name="config-file"></a>Config 파일

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>코드 초기화

**[권한 부여]** 특성을 보유 하는 컨트롤러 작업에 대해 앱이 호출 되 면 ASP.NET 및 ASP.NET Core가 권한 부여 헤더의 전달자 토큰에서 액세스 토큰을 추출 합니다. 그런 다음 액세스 토큰은 .NET 용 Microsoft System.identitymodel Extensions를 호출 하는 JwtBearer 미들웨어에 전달 됩니다.

ASP.NET Core에서이 미들웨어는 Startup.cs 파일에서 초기화 됩니다.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

미들웨어는 다음 명령으로 web API에 추가 됩니다.

```csharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 현재 ASP.NET Core 템플릿은 조직 또는 조직 내에서 사용자를 로그인 하는 Azure Active Directory (Azure AD) 웹 Api를 만듭니다. 개인 계정으로 사용자를 로그인 하지 않습니다. 그러나 다음 코드를 Startup.cs에 추가 하 여 Microsoft id 플랫폼 엔드포인트을 사용 하도록 템플릿을 변경할 수 있습니다.

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

위의 코드 조각은 [WebApiServiceCollectionExtensions/L50 #-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)의 ASP.NET CORE web API 증분 자습서에서 추출 되었습니다. 조각으로 표시 되는 것 보다 많은 **AddProtectedWebApi** 메서드는 Startup.cs에서 호출 됩니다.

## <a name="token-validation"></a>토큰 유효성 검사

위의 코드 조각에서 web apps의 Openid connect Connect 미들웨어와 같은 JwtBearer 미들웨어는 `TokenValidationParameters`값을 기준으로 토큰의 유효성을 검사 합니다. 필요에 따라 토큰의 암호를 해독 하 고, 클레임을 추출 하 고, 서명을 확인 합니다. 그러면 미들웨어는 다음 데이터를 확인 하 여 토큰의 유효성을 검사 합니다.

- 대상: 토큰은 웹 API를 대상으로 합니다.
- Sub: web API를 호출할 수 있는 앱에 대해 발급 되었습니다.
- 발급자: 신뢰할 수 있는 STS (보안 토큰 서비스)에서 발급 되었습니다.
- 만료: 수명이 범위 내에 있습니다.
- 서명: 변조 되지 않았습니다.

특별 한 유효성 검사를 수행할 수도 있습니다. 예를 들어 토큰에 포함 된 경우 서명 키가 신뢰 되 고 토큰이 재생 되 고 있지 않은지 확인할 수 있습니다. 마지막으로 일부 프로토콜에는 특정 유효성 검사가 필요 합니다.

### <a name="validators"></a>유효성 검사기

유효성 검사 단계는 [.net 용 Microsoft System.identitymodel Extensions](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) 오픈 소스 라이브러리에서 제공 하는 유효성 검사기에서 캡처됩니다. 유효성 검사기는 라이브러리 소스 파일 [system.identitymodel/유효성 검사기 .cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)에 정의 되어 있습니다.

다음 표에서는 유효성 검사기에 대해 설명 합니다.

| 유효성 검사기 | Description |
|---------|---------|
| **ValidateAudience** | 토큰의 유효성을 검사 하는 응용 프로그램에 대 한 토큰을 확인 합니다. |
| **ValidateIssuer** | 신뢰할 수 있는 STS에서 토큰을 발급 했는지 확인 합니다. 즉, 신뢰할 수 있는 STS에서 토큰을 발급 했는지 확인 합니다. |
| **ValidateIssuerSigningKey** | 토큰의 유효성을 검사 하는 응용 프로그램에서 토큰을 서명 하는 데 사용 된 키를 신뢰 하는지 확인 합니다. 키가 토큰에 포함 되는 특별 한 경우가 있습니다. 그러나이 경우는 일반적으로 발생 하지 않습니다. |
| **ValidateLifetime** | 토큰을 아직 또는 이미 사용할 수 있는지 확인 합니다. 유효성 검사기는 토큰의 수명이 **notbefore** 및 **expires** 클레임으로 지정 된 범위 내에 있는지 확인 합니다. |
| **ValidateSignature** | 토큰이 변조 되지 않았는지 확인 합니다. |
| **ValidateTokenReplay** | 토큰을 재생 하지 않도록 합니다. Onetime 프로토콜을 사용 하는 특별 한 경우가 있습니다. |

유효성 검사기는 **Tokenvalidationparameters** 클래스의 속성과 연결 되어 있습니다. 속성은 ASP.NET 및 ASP.NET Core 구성에서 초기화 됩니다.

대부분의 경우 매개 변수를 변경할 필요가 없습니다. 단일 테넌트가 아닌 앱은 예외입니다. 이러한 웹 앱은 모든 조직이 나 개인 Microsoft 계정의 사용자를 허용 합니다. 이 경우 발급자의 유효성을 검사 해야 합니다.

## <a name="token-validation-in-azure-functions"></a>Azure Functions에서 토큰 유효성 검사

Azure Functions에서 들어오는 액세스 토큰의 유효성을 검사할 수도 있습니다. [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [Nodejs](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)및 [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)에서 이러한 유효성 검사의 예제를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [코드에서 범위 및 앱 역할 확인](scenario-protected-web-api-verification-scope-app-roles.md)
