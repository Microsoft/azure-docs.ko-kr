---
title: 사용자가 로그인 하는 웹 앱 구성-Microsoft identity platform | Microsoft
description: 사용자를 로그인 하는 웹 앱을 빌드하는 방법 알아보기 (코드 구성)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 64b38d0e776a0e3dab155704dcc368cc738c278e
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855416"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>사용자가 로그인 하는 웹 앱: 코드 구성

사용자가 로그인 하는 웹 앱에 대 한 코드를 구성 하는 방법을 알아봅니다.

## <a name="libraries-for-protecting-web-apps"></a>웹 앱 보호를 위한 라이브러리

<!-- This section can be in an include for web app and web APIs -->
웹 앱 및 웹 API를 보호 하는 데 사용 되는 라이브러리는 다음과 같습니다.

| 플랫폼 | 라이브러리 | 설명 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [.NET 용 id 모델 확장](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | ASP.NET 및 ASP.NET Core에서 직접 사용 되는 .NET 용 Microsoft Identity Model Extensions는 .NET Framework와 .NET Core 둘 다에서 실행 되는 Dll 집합을 제안 합니다. ASP.NET 또는 ASP.NET Core 웹 앱에서 **Tokenvalidationparameters** 클래스 (특히 일부 파트너 시나리오)를 사용 하 여 토큰 유효성 검사를 제어할 수 있습니다. 실제로 복잡성은 [Microsoft. Identity. 웹](https://aka.ms/ms-identity-web) 라이브러리에 캡슐화 됩니다. |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Java 웹 응용 프로그램에 대 한 지원 |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Python 웹 응용 프로그램 지원 |

관심 있는 플랫폼에 해당 하는 탭을 선택 합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

이 문서 및 다음 항목의 코드 조각은 [ASP.NET Core 웹 앱 증분 자습서의 1 장](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)에서 추출 되었습니다.

전체 구현에 대 한 자세한 내용은이 자습서를 참조 하는 것이 좋습니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

이 문서의 코드 조각과 다음은 [ASP.NET 웹 앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)에서 추출 됩니다.

전체 구현에 대 한 자세한 내용은이 샘플을 참조 하는 것이 좋습니다.

# <a name="java"></a>[Java](#tab/java)

이 문서의 코드 조각과 다음은 MSAL Java의 Microsoft graph 샘플을 호출 하는 [java 웹 응용 프로그램](https://github.com/Azure-Samples/ms-identity-java-webapp) 에서 추출 되었습니다.

전체 구현에 대 한 자세한 내용은이 샘플을 참조 하는 것이 좋습니다.

# <a name="python"></a>[Python](#tab/python)

이 문서의 코드 조각과 다음은 MSAL Python에서 Microsoft graph 샘플을 호출 하는 [Python 웹 응용 프로그램](https://github.com/Azure-Samples/ms-identity-python-webapp) 에서 추출 됩니다.

전체 구현에 대 한 자세한 내용은이 샘플을 참조 하는 것이 좋습니다.

---

## <a name="configuration-files"></a>구성 파일

Microsoft id 플랫폼을 사용 하 여 사용자를 로그인 하는 웹 응용 프로그램은 구성 파일을 통해 구성 됩니다. 입력 해야 하는 설정은 다음과 같습니다.

- 예를 들어 `Instance` 국가 클라우드에서 앱을 실행 하려는 경우 클라우드 인스턴스 ()
- 테 넌 트 ID ()의 대상입니다. `TenantId`
- `ClientId`Azure Portal에서 복사한 응용 프로그램의 클라이언트 ID ()입니다.

경우에 따라 응용 프로그램을 매개 변수가 있는 수 있습니다 `Authority` .이는 및의 연결입니다 `Instance` `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core에서 이러한 설정은 파일 [ 의appsettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) "AzureAd" 섹션에 있습니다.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-oidc"
  }
}
```

ASP.NET Core의 다른 파일 ([properties\launchSettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7))에는 `applicationUrl` `sslPort` 응용 프로그램 및 다양 한 프로필에 대 한 URL () 및 TLS/SSL 포트 ()가 포함 되어 있습니다.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Azure Portal에서 응용 프로그램에 대 한 **인증** 페이지에 등록 해야 하는 회신 uri는 이러한 url과 일치 해야 합니다. 위의 두 구성 파일은 `https://localhost:44321/signin-oidc` 입니다. 이유는가 이지만 `applicationUrl` `http://localhost:3110` `sslPort` (44321)가 지정 된 이유입니다. `CallbackPath` 는 `/signin-oidc` 에 정의 된 대로입니다 `appsettings.json` .

동일한 방식으로 로그 아웃 URI는로 설정 됩니다 `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET에서 응용 프로그램은 [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) 파일을 통해 구성 되며, 12-15 줄로 구성 됩니다.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Azure Portal에서 응용 프로그램에 대 한 **인증** 페이지에 등록 해야 하는 회신 uri는 이러한 url과 일치 해야 합니다. 즉,로 지정 해야 `https://localhost:44326/` 합니다.

# <a name="java"></a>[Java](#tab/java)

Java에서 구성은 아래에 있는 [응용 프로그램. 속성](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) 파일에 있습니다 `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Azure Portal에서 응용 프로그램에 대 한 **인증** 페이지에 등록 해야 하는 회신 uri는 `redirectUri` 응용 프로그램에서 정의 하는 인스턴스와 일치 해야 합니다. 즉, 및 여야 합니다 `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me` .

# <a name="python"></a>[Python](#tab/python)

다음은 [app_config py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)의 Python 구성 파일입니다.

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> 이 빠른 시작은 간소화를 위해 클라이언트 암호를 구성 파일에 저장 하는 것을 제안 합니다. 프로덕션 앱에서 [Flask의 설명서](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)에 설명 된 대로 주요 자격 증명 모음 또는 환경 변수와 같은 다른 방법을 사용 하 여 비밀을 저장 하려고 합니다.
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>초기화 코드

초기화 코드는 플랫폼에 따라 다릅니다. ASP.NET Core 및 ASP.NET의 경우 로그인 사용자는 Openid connect Connect 미들웨어에 게 위임 됩니다. ASP.NET 또는 ASP.NET Core 템플릿은 Azure Active Directory (Azure AD) v 1.0 끝점에 대 한 웹 응용 프로그램을 생성 합니다. 일부 구성은 v2.0 (Microsoft identity platform) 끝점에 맞게 조정 해야 합니다. Java의 경우 응용 프로그램의 협력을 통해 스프링에 의해 처리 됩니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

웹 앱 (및 web Api)에서 `[Authorize]` 컨트롤러 또는 컨트롤러 작업에 대 한 특성이 있으므로 응용 프로그램은 보호 됩니다. ASP.NET Core 이 특성은 사용자가 인증 되었는지 확인 합니다. 응용 프로그램을 초기화 하는 코드는 *Startup.cs* 파일에 있습니다.

Microsoft id 플랫폼 (이전의 Azure AD v2.0)으로 인증을 추가 하려면 다음 코드를 추가 해야 합니다. 코드의 주석은 설명이 필요 하지 않습니다.

> [!NOTE]
> Microsoft id 플랫폼용 새 ASP.NET Core 템플릿을 사용 하 여 직접 시작 하려는 경우에는 .NET Core 3.1 및 .NET 5.0 용 프로젝트 템플릿이 포함 된 preview NuGet 패키지를 다운로드할 수 있습니다. 그런 다음 설치 되 면 ASP.NET Core 웹 응용 프로그램 (MVC 또는 Blazor)을 직접 인스턴스화할 수 있습니다. 자세한 내용은 [Microsoft. Identity 웹 앱 프로젝트 템플릿](https://aka.ms/ms-id-web/webapp-project-templates) 을 참조 하세요. 이 방법은 아래의 모든 단계를 수행 하는 가장 간단한 방법입니다.
>
> Visual Studio 내에서 또는 또는를 사용 하 여 현재 기본 ASP.NET Core 웹 프로젝트를 사용 하 여 프로젝트를 시작 하려는 경우 `dotnet new mvc --auth SingleAuth` `dotnet new webapp --auth SingleAuth` 다음과 같은 코드가 표시 됩니다.
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> 이 코드는 Azure AD v1.0 응용 프로그램을 만드는 데 사용 되는 레거시 **AspNetCore AzureAD** NuGet 패키지를 사용 합니다. 이 문서에서는 해당 코드를 대체 하는 Microsoft id 플랫폼 (Azure AD v2.0) 응용 프로그램을 만드는 방법을 설명 합니다.
>

1. 프로젝트에 [Microsoft identity. system.web](https://www.nuget.org/packages/Microsoft.Identity.Web) 및 [microsoft. identity](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) . uiui&gt 패키지를 추가 합니다. AzureAD NuGet 패키지 (있는 경우)를 제거 합니다.

2. 에서 `ConfigureServices` 및 메서드를 사용 하도록 코드를 업데이트 `AddMicrosoftIdentityWebAppAuthentication` `AddMicrosoftIdentityUI` 합니다.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. `Configure` *Startup.cs*의 메서드에서를 호출 하 여 인증을 사용 하도록 설정 합니다.`app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

위의 코드에서 다음을 수행 합니다.
- `AddMicrosoftIdentityWebAppAuthentication`확장 메서드는 **Microsoft. Identity. Web**에 정의 되어 있습니다. 메서드
  - 인증 서비스를 추가 합니다.
  - 구성 파일을 읽는 옵션을 구성 합니다 (여기서는 "AzureAD" 섹션에서).
  - Openid connect Connect 옵션을 구성 하 여 권한이 Microsoft id 플랫폼 엔드포인트가 되도록 합니다.
  - 토큰 발급자의 유효성을 검사 합니다.
  - 이름에 해당 하는 클레임이 `preferred_username` ID 토큰의 클레임에서 매핑되는지 확인 합니다.

- 구성 개체 외에도를 호출할 때 구성 섹션의 이름을 지정할 수 있습니다 `AddMicrosoftIdentityWebAppAuthentication` . 기본적으로 `AzureAd` 입니다.

- `AddMicrosoftIdentityWebAppAuthentication` 고급 시나리오에 대 한 다른 매개 변수가 있습니다. 예를 들어 추적 Openid connect Connect 미들웨어 이벤트는 인증이 작동 하지 않는 경우 웹 응용 프로그램의 문제를 해결 하는 데 도움이 될 수 있습니다. 선택적 매개 변수 `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` 를로 설정 하면 `true` HTTP 응답에서 사용자 id로 진행 되는 ASP.NET Core 미들웨어 집합에서 정보를 처리 하는 방법을 보여 줍니다 `HttpContext.User` .

- `AddMicrosoftIdentityUI`확장 메서드는 **Microsoft. Identity. system.web**. 로그인 및 로그 아웃을 처리 하는 기본 컨트롤러를 제공 합니다.

웹 앱을 만들 수 있는 방법에 대 한 자세한 내용은 <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> 현재는 Azure AD와 외부 로그인 공급자를 사용 하는 경우, 현재 Microsoft. Identity는 **개별 사용자 계정** (앱 내 사용자 계정 저장)의 시나리오를 지원 하지 않습니다. 자세한 내용은 다음을 참조 하세요. [AzureAD/microsoft-identity-web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 웹 앱 및 web Api의 인증과 관련 된 코드는 [App_Start/startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) 파일에 있습니다.

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Java 샘플에서는 스프링 프레임 워크를 사용 합니다. 각 HTTP 응답을 가로채는 필터를 구현 하므로 응용 프로그램은 보호 됩니다. Java 웹 앱에 대 한 빠른 시작에서이 필터는 `AuthFilter` 에 `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` 있습니다.

이 필터는 OAuth 2.0 인증 코드 흐름을 처리 하 고 사용자가 인증 되었는지 확인 `isAuthenticated()` 합니다 (메서드). 사용자가 인증 되지 않은 경우 Azure AD 권한 부여 끝점의 URL을 계산 하 고 브라우저를이 URI로 리디렉션합니다.

인증 코드를 포함 하는 응답은 도착 하면 MSAL Java를 사용 하 여 토큰을 획득 합니다. 마지막으로 토큰 끝점에서 토큰을 수신 하면 (리디렉션 URI) 사용자에 게 로그인 됩니다.

자세한 내용은 `doFilter()` [authfilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)의 메서드를 참조 하세요.

> [!NOTE]
> 의 코드는 `doFilter()` 약간 다른 순서로 작성 되지만 흐름은 설명 된 흐름입니다.

이 메서드가 트리거하는 인증 코드 흐름에 대 한 자세한 내용은 [Microsoft id 플랫폼 및 OAuth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md)을 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

Python 샘플에서는 Flask를 사용 합니다. Flask 및 MSAL Python의 초기화는 [py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)에서 수행 됩니다.

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>다음 단계

다음 문서에서는 로그인 및 로그 아웃을 트리거하는 방법을 알아봅니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](./scenario-web-app-sign-user-sign-in.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](./scenario-web-app-sign-user-sign-in.md?tabs=python)

---