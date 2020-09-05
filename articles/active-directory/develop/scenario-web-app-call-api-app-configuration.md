---
title: 웹 API를 호출하는 웹앱 구성 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 웹앱의 코드를 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 8827d413144d8bc6f00c3948a99be3ee3aa2264e
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855442"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>웹 API를 호출하는 웹앱: 코드 구성

[사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md) 시나리오에 나온 것처럼 웹앱은 [OAuth 2.0 인증 코드 흐름](v2-oauth2-auth-code-flow.md)을 사용하여 사용자를 로그인합니다. 이 흐름에는 두 단계가 있습니다.

1. 인증 코드를 요청합니다. 이 부분에서는 사용자와의 프라이빗 대화 상자를 Microsoft ID 플랫폼에 위임합니다. 해당 대화가 진행되는 동안 사용자는 로그인하여 웹 API 사용에 동의합니다. 프라이빗 대화 상자가 성공적으로 종료되면 웹앱은 해당 리디렉션 URI에 대한 인증 코드를 수신합니다.
1. 인증 코드를 교환하여 API에 대한 액세스 토큰을 요청합니다.

[사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md) 시나리오에서는 첫 번째 단계에 대해서만 다룹니다. 사용자를 로그인할 뿐만 아니라 이제 웹 API를 호출하도록 웹앱을 수정하는 방법을 알아봅니다.

## <a name="libraries-that-support-web-app-scenarios"></a>웹앱 시나리오를 지원하는 라이브러리

MSAL(Microsoft 인증 라이브러리)의 다음 라이브러리는 웹앱에 대한 인증 코드 흐름을 지원합니다.

| MSAL 라이브러리 | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | .NET Framework 및 .NET Core 플랫폼을 지원합니다. UWP(유니버설 Windows 플랫폼), Xamarin.iOS 및 Xamarin.Android는 퍼블릭 클라이언트 애플리케이션을 빌드하는 데 사용되는 플랫폼이므로 지원되지 않습니다. 웹 앱 및 web Api ASP.NET Core MSAL.NET는 더 높은 수준의 라이브러리 ( [Microsoft. Identity](https://aka.ms/ms-identity-web) )에 캡슐화 되어 있습니다.|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> Python용 MSAL | Python 웹 애플리케이션 지원. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Java용 MSAL | Java 웹 애플리케이션 지원. |

관심 있는 플랫폼에 대한 탭을 선택합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

웹앱에서 Microsoft.Identity.Web을 사용할 때 보호되는 API를 호출할 수 있도록 하려면 `AddWebAppCallsProtectedWebApi`를 호출하고 토큰 캐시 직렬화 형식(예: 메모리 내 토큰 캐시)을 지정하기만 하면 됩니다.

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddMicrosoftIdentityWebAppAuthentication(Configuration,
                                                      "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                    initialScopes: new string[] { "user.read" })
                .AddInMemoryTokenCaches();

    // more code here
}
```

토큰 캐시에 대해 자세히 알아보려면 [토큰 캐시 직렬화 옵션](#token-cache)을 참조하세요.

> [!NOTE]
> 여기에서 코드 예제를 완전히 이해하려면 [ASP.NET Core 기본](/aspnet/core/fundamentals), 특히 [종속성 주입](/aspnet/core/fundamentals/dependency-injection) 및 [옵션](/aspnet/core/fundamentals/configuration/options)에 대해 잘 알고 있어야 합니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

사용자 로그인은 OIDC(Open ID Connect) 미들웨어에 위임되므로 OIDC 프로세스와 상호 작용해야 합니다. 상호 작용하는 방법은 사용하는 프레임워크에 따라 다릅니다.

ASP.NET의 경우 미들웨어 OIDC 이벤트를 구독합니다.

- ASP.NET Core에서 Open ID Connect 미들웨어를 통해 인증 코드를 요청하게 됩니다. ASP.NET 또는 ASP.NET Core를 통해 사용자가 로그인하고 동의할 수 있습니다.
- 웹앱을 구독하여 인증 코드를 수신합니다. 이 구독은 C# 대리자를 사용하여 수행됩니다.
- 인증 코드를 받으면 MSAL 라이브러리를 사용하여 해당 코드를 교환합니다. 결과 액세스 토큰과 새로 고침 토큰은 토큰 캐시에 저장됩니다. 컨트롤러와 같은 애플리케이션의 다른 부분에서 캐시를 사용하여 다른 토큰을 자동으로 가져올 수 있습니다.

이 문서 및 다음 예제의 코드 예제는 [ASP.NET 웹앱 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)에서 추출됩니다. 전체 구현에 대한 자세한 내용은 해당 샘플을 참조하는 것이 좋습니다.

# <a name="java"></a>[Java](#tab/java)

이 문서 및 다음 예제의 코드 예제는 Java용 MSAL을 사용하는 웹앱 샘플인 [Microsoft Graph를 호출하는 Java 웹 애플리케이션](https://github.com/Azure-Samples/ms-identity-java-webapp)에서 추출됩니다.
샘플은 현재 Java용 MSAL에서 권한 부여 코드 URL을 생성하고 Microsoft ID 플랫폼에 대한 권한 부여 엔드포인트 탐색을 처리합니다. 스프린트 보안을 사용하여 사용자를 로그인할 수도 있습니다. 전체 구현에 대한 자세한 내용은 샘플을 참조하는 것이 좋습니다.

# <a name="python"></a>[Python](#tab/python)

이 문서 및 다음 문서의 코드 예제는 MSAL.Python을 사용하는 웹앱 샘플인 [Microsoft Graph를 호출하는 Python 웹 애플리케이션](https://github.com/Azure-Samples/ms-identity-python-webapp)에서 추출됩니다.
샘플은 현재 MSAL.Python에서 권한 부여 코드 URL을 생성하고 Microsoft ID 플랫폼에 대한 권한 부여 엔드포인트 탐색을 처리합니다. 전체 구현에 대한 자세한 내용은 샘플을 참조하는 것이 좋습니다.

---

## <a name="code-that-redeems-the-authorization-code"></a>인증 코드를 교환하는 코드입니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web은 올바른 OpenID Connect 설정을 지정하고, 코드를 받은 이벤트를 구독하고, 코드를 교환하여 코드를 단순화합니다. 인증 코드를 교환하는 데 필요한 추가 코드는 없습니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [Microsoft의 system.web 소스 코드](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) 를 참조 하십시오.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET은 OpenID Connect의 구성과 `OnAuthorizationCodeReceived` 이벤트에 대한 구독이 [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) 파일에서 발생한다는 점을 제외하고 ASP.NET Core와 유사한 작업을 처리합니다. ASP.NET에서는 `RedirectUri`를 [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)에서 지정해야 한다는 점을 제외하고 개념도 ASP.NET Core와 유사합니다. 이 구성은 애플리케이션을 배포할 때 변경해야 하기 때문에 ASP.NET Core에서보다 약간 덜 강력합니다.

Startup.Auth.cs에 대한 코드는 다음과 같습니다.

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

[사용자를 로그인하는 웹앱: 코드 구성](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)을 참조하여 Java 샘플에서 인증 코드를 가져오는 방법을 알아보세요. 앱에서 코드를 수신한 후, [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)에서 `AuthHelper.processAuthenticationCodeRedirect` 메서드로 위임합니다.
1. `getAuthResultByAuthCode`를 호출합니다.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

`getAuthResultByAuthCode` 메서드는 [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)에 정의되어 있습니다. MSAL `ConfidentialClientApplication`을 만든 다음, 인증 코드에서 만든 `AuthorizationCodeParameters`를 사용하여 `acquireToken()`을 호출합니다.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

인증 코드 흐름은 [사용자를 로그인하는 웹앱: 코드 구성](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)에 표시된 대로 요청됩니다. 그런 다음, Flask가 `/getAToken` URL에서 라우팅되는 `authorized` 함수에서 코드가 수신됩니다. 이 코드의 전체 컨텍스트는 [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)를 참조하세요.

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

클라이언트 암호 대신 클라이언트 인증서 또는 클라이언트 어설션을 사용하여 기밀 클라이언트 애플리케이션에서 해당 ID를 증명할 수도 있습니다.
클라이언트 어설션을 사용하는 것은 고급 시나리오로, [클라이언트 어설션](msal-net-client-assertions.md)에 자세히 설명되어 있습니다.

## <a name="token-cache"></a>토큰 캐시

> [!IMPORTANT]
> 웹앱 또는 웹 API에 대한 토큰 캐시 구현은 데스크톱 애플리케이션에 대한 구현과 다르며, 이는 종종 [파일 기반](scenario-desktop-acquire-token.md#file-based-token-cache)입니다.
> 보안 및 성능상의 이유로, 웹앱 및 웹 API의 경우 사용자 계정마다 하나의 토큰 캐시가 있는지 확인하는 것이 중요합니다. 각 계정의 토큰 캐시를 직렬화해야 합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core 자습서에서는 종속성 주입을 사용하여 애플리케이션에 대한 Startup.cs 파일에서 토큰 캐시 구현을 결정할 수 있습니다. Microsoft.Identity.Web에는 [토큰 캐시 직렬화](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)에 설명된 미리 작성된 토큰 캐시 직렬 변환기가 제공됩니다. ASP.NET Core [분산 메모리 캐시](/aspnet/core/performance/caching/distributed#distributed-memory-cache)를 선택할 수도 있습니다.

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

토큰 캐시 공급자에 대 한 자세한 내용은 ASP.NET Core 웹 앱 자습서 뿐만 아니라 Microsoft의 [token cache serialization](https://aka.ms/ms-id-web/token-cache-serialization) 문서를 참조 하세요. [ ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) 웹 앱 자습서의 토큰 캐시 단계입니다.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

웹앱 또는 웹 API에 대한 토큰 캐시 구현은 데스크톱 애플리케이션에 대한 구현과 다르며, 이는 종종 [파일 기반](scenario-desktop-acquire-token.md#file-based-token-cache)입니다.

웹앱 구현에서는 ASP.NET 세션 또는 서버 메모리를 사용할 수 있습니다. 예를 들어 [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)에서 MSAL.NET 애플리케이션을 만든 후 캐시 구현이 후크되는 방법을 참조하세요.

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java는 토큰 캐시를 직렬화 및 역직렬화하는 메서드를 제공합니다. Java 샘플은 [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)의 `getAuthResultBySilentFlow` 메서드에 표시된 것처럼 세션에서 직렬화를 처리합니다.

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

`SessionManagementHelper` 클래스의 세부 정보는 [Java용 MSAL 샘플](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)에 제공됩니다.

# <a name="python"></a>[Python](#tab/python)

Python 샘플에서 각 요청에 대한 기밀 클라이언트 애플리케이션을 다시 만든 다음, Flask 세션 캐시에서 직렬화하여 계정마다 하나의 캐시가 있는지 확인합니다.

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>다음 단계

이 시점에서 사용자가 로그인하면 토큰이 토큰 캐시에 저장됩니다. 웹앱의 다른 부분에서 사용되는 방법을 살펴보겠습니다.

> [!div class="nextstepaction"]
> [웹 API를 호출하는 웹앱: 글로벌 로그아웃 시 캐시에서 계정 제거](scenario-web-app-call-api-sign-in.md)