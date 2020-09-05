---
title: 웹 API 호출을 위한 토큰 획득(데스크톱 앱) | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 데스크톱 앱을 빌드하여 앱의 토큰을 획득하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 300bc6acbe7821841b578dcc2166ecfc498ad750
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141298"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>웹 API를 호출하는 데스크톱 앱: 토큰 획득

공용 클라이언트 애플리케이션의 인스턴스를 빌드한 후에는 이 인스턴스를 사용하여 토큰을 획득한 다음 웹 API를 호출하는 데 사용합니다.

## <a name="recommended-pattern"></a>권장 패턴

웹 API는 `scopes`에 의해 정의됩니다. 애플리케이션을 통해 제공하려는 경험이 무엇이든, 다음과 같은 패턴을 사용합니다.

- `AcquireTokenSilent`를 호출하여 토큰 캐시에서 시스템적으로 토큰 가져오기를 시도합니다.
- 이 호출이 실패하면 사용하려는 `AcquireToken` 흐름을 사용합니다. 여기서는 이 흐름이 `AcquireTokenXX`로 표현됩니다.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>MSAL.NET에서

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에서

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```
---

다음은 데스크톱 애플리케이션에서 토큰을 획득하는 다양한 방법입니다.

## <a name="acquire-a-token-interactively"></a>대화형으로 토큰 획득

다음 예에서는 Microsoft Graph로 사용자 프로필을 읽기 위해 대화형으로 토큰을 가져오는 최소한의 코드를 보여 줍니다.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>MSAL.NET에서

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>필수 매개 변수

`AcquireTokenInteractive`에는 오직 하나의 필수 매개 변수 ``scopes``가 있습니다. 이 매개 변수에는 토큰이 필요한 범위를 정의하는 문자열 열거형이 포함되어 있습니다. 토큰이 Microsoft Graph용인 경우, 필요한 범위는 각 Microsoft Graph API의 API 참조의 “권한” 섹션에서 확인할 수 있습니다. 예를 들어, [사용자 연락처를 나열하려면](/graph/api/user-list-contacts) “User.Read”, “Contacts.Read” 범위를 사용해야 합니다. 자세한 내용은 [Microsoft Graph 사용 권한 참조](/graph/permissions-reference)를 참조하세요.

Android에서는 예에서 볼 수 있듯이 `.WithParentActivityOrWindow`를 사용하여 부모 작업도 지정해야 합니다. 이렇게 해야 토큰이 이 상호 작용 후에 부모 작업으로 돌아갑니다. 부모 작업을 지정하지 않으면 `.ExecuteAsync()`를 호출할 때 예외가 발생합니다.

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET의 특정 선택적 매개 변수

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI는 대화형이기 때문에 중요합니다. `AcquireTokenInteractive`에는 부모 UI를 지원하는 플랫폼에서 부모 UI를 지정할 수 있는 선택적 매개 변수가 하나 있습니다. 데스크톱 애플리케이션에서 사용할 경우 `.WithParentActivityOrWindow`는 플랫폼에 따라 다른 형식을 갖습니다.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

설명:

- .NET Standard에서 필요한 `object`는 Android의 경우 `Activity`, iOS의 경우`UIViewController`, MAC의 경우 `NSWindow`, Windows의 경우 `IWin32Window` 또는 `IntPr`입니다.
- Windows에서는 임베디드 브라우저가 올바른 UI 동기화 컨텍스트를 가져올 수 있도록 UI 스레드에서 `AcquireTokenInteractive`를 호출해야 합니다. UI 스레드에서 호출하지 않으면 메시지가 올바르게 펌프되지 않고 UI가 교착 상태에 빠질 수 있습니다. 이미 UI 스레드에 있지 않은 경우 UI 스레드에서 MSAL(Microsoft 인증 라이브러리)을 호출하는 한 가지 방법은 WPF에서 `Dispatcher`를 호출하는 것입니다.
- WPF를 사용하는 경우, WPF 컨트롤에서 창을 가져오려면 `WindowInteropHelper.Handle` 클래스를 사용할 수 있습니다. 그러면 호출이 WPF 컨트롤(`this`)에서 이루어집니다.

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`는 프롬프트를 지정하여 사용자와의 대화형 작업을 제어하는 데 사용됩니다.

![프롬프트 구조의 필드를 표시 하는 이미지입니다. 이러한 상수 값은 WithPrompt () 메서드에서 표시 하는 프롬프트 형식을 정의 하 여 사용자와의 상호 작용을 제어 합니다.](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

클래스는 다음과 같은 상수를 정의합니다.

- ``SelectAccount``는 사용자가 세션을 보유한 계정을 포함하는 계정 선택 대화 상자를 제시하도록 STS를 강제합니다. 이 옵션은 애플리케이션 개발자가 여러 ID 중에서 사용자가 선택할 수 있도록 하려는 경우에 유용합니다. 이 옵션은 MSAL이 ID 공급자에게 ``prompt=select_account``를 보내도록 유도합니다. 이 옵션이 기본값입니다. 이 옵션은 계정, 사용자의 세션 유무와 같은 사용 가능한 정보를 기반으로 최상의 환경을 제공합니다. 이 옵션은 반드시 변경해야 할 이유가 있지 않은 한 변경하지 마세요.
- ``Consent``는 애플리케이션 개발자가 이전에 동의가 부여된 경우에도 사용자에게 동의를 묻는 프롬프트를 강제로 표시할 수 있도록 지원합니다. 이 경우 MSAL이 ID 공급자에게 `prompt=consent`를 보냅니다. 이 옵션은 조직 거버넌스에서 애플리케이션이 사용될 때마다 사용자에게 동의 대화 상자를 표시하도록 요구하는 일부 보안 우선 애플리케이션에서 사용할 수 있습니다.
- ``ForceLogin``은 애플리케이션 개발자가 사용자 프롬프트가 필요하지 않은 경우에도 서비스가 사용자에게 자격 증명 프롬프트를 표시하도록 설정하는 것을 지원합니다. 이 옵션은 토큰 획득에 실패한 경우 사용자가 다시 로그인하도록 할 때 유용합니다. 이 경우 MSAL이 ID 공급자에게 `prompt=login`을 보냅니다. 이 옵션은 조직 거버넌스에서 사용자가 애플리케이션의 특정 부분에 액세스할 때마다 다시 로그인하도록 요구하는 보안 우선 애플리케이션에서 사용되기도 합니다.
- ``Never``(.NET 4.5 및 WinRT만 해당)는 사용자에게 프롬프트를 표시하지 않으며 그 대신 숨겨진 임베디드 웹 보기에 저장된 쿠키를 사용하려고 합니다. 자세한 내용은 MSAL.NET의 웹 보기를 참조하세요. 이 옵션을 사용하는 것은 실패할 수 있습니다. 실패하는 경우 `AcquireTokenInteractive`가 UI 상호 작용이 필요함을 알리기 위해 예외를 발생시킵니다. 이 경우 다른 `Prompt` 매개 변수를 사용해야 합니다.
- ``NoPrompt``는 ID 공급자에게 어떤 프롬프트도 보내지 않습니다. 이 옵션은 Azure AD(Azure Active Directory) B2C 프로필 편집 정책에서만 유용합니다. 자세한 내용은 [Azure AD B2C specifics](https://aka.ms/msal-net-b2c-specificities)(Azure AD B2C 관련 사항)를 참조하세요.

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

이 한정자는 사용자가 처음에 몇 가지 리소스에 미리 동의하도록 하고자 하며 MSAL.NET/Microsoft ID 플랫폼에서 통상적으로 사용되는 증분 동의를 사용하지 않으려는 고급 시나리오에서 사용됩니다. 자세한 내용은 [처음에 여러 리소스에 대한 사용자 동의 받기](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)를 참조하세요.

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

웹 UI는 브라우저를 호출하는 메커니즘입니다. 이 메커니즘은 전용 UI WebBrowser 컨트롤일 수도 있고 브라우저 열기를 위임하는 방법일 수도 있습니다.
MSAL은 대부분의 플랫폼에 대한 웹 UI 구현을 제공하지만, 다음과 같이 브라우저를 직접 호스트해야 하는 경우가 있습니다.

- MSAL에서 명시적으로 지원하지 않는 플랫폼(예: 데스크톱용 Mono, Blazor, Unity).
- 애플리케이션의 UI를 테스트할 때 Selenium과 함께 사용할 수 있는 자동화된 브라우저를 사용하려는 경우.
- MSAL을 실행하는 앱과 브라우저가 서로 다른 프로세스에 있는 경우.

##### <a name="at-a-glance"></a>개요

이를 위해서는 MSAL에 `start Url`을 제공해야 합니다. Start Url은 최종 사용자가 사용자 이름과 같은 항목을 입력할 수 있도록 브라우저에 표시되어야 합니다.
인증이 완료되면 앱에서 MSAL로 Azure AD에서 제공한 코드를 포함하는 `end Url`을 전달해야 합니다.
`end Url`의 호스트는 항상 `redirectUri`입니다. `end Url`을 가로채려면 다음 중 하나를 수행하세요.

- `redirect Url`이 적중될 때까지 브라우저 리디렉션을 모니터링합니다.
- 개발자가 모니터링하는 URL로 브라우저가 리디렉션되도록 합니다.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi는 확장성 지점임

`WithCustomWebUi`는 공용 클라이언트 애플리케이션에서 자체 UI를 제공하는 데 사용할 수 있는 확장성 지점입니다. 사용자가ID 공급자의 /Authorize 엔드포인트를 통과하여 로그인 및 동의하도록 할 수도 있습니다. 이렇게 하면 MSAL.NET이 인증 코드를 사용하고 토큰을 가져올 수 있습니다. 예를 들어, WithCustomWebUi는 Visual Studio에서 Electrons 애플리케이션(예: Visual Studio Feedback)으로 하여금 웹 상호 작용을 제공하되 대부분의 작업을 MSAL.NET이 처리하도록 하려는 경우에 사용됩니다. UI 자동화를 제공하려는 경우에도 사용할 수 있습니다. 공용 클라이언트 애플리케이션에서 MSAL.NET은 PKCE(Proof Key for Code Exchange) 표준을 사용하여 보안이 준수되도록 합니다. 오직 MSAL.NET만 코드를 사용할 수 있습니다. 자세한 내용은 [RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636)(RFC 7636 - OAuth 공용 클라이언트에 의한 코드 교환을 위한 증명 키)를 참조하세요.

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>WithCustomWebUi 사용

`.WithCustomWebUI`를 사용하려면 다음 단계를 수행합니다.

  1. `ICustomWebUi` 인터페이스를 구현합니다. 자세한 내용은 [이 웹 사이트](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)를 참조하세요. `AcquireAuthorizationCodeAsync` 메서드 하나를 구현하고 MSAL.NET에 의해 계산된 인증 코드 URL을 적용합니다. 그런 다음 사용자가 ID 공급자와의 상호 작용을 통과하도록 한 후, ID 공급자가 구현을 호출하는 데 사용했을 URL과 인증 코드를 함께 반환하도록 합니다. 문제가 발생하면 MSAL과의 매끄러운 상호 작용을 위해 구현에서 `MsalExtensionException` 예외를 발생시켜야 합니다.
  2. `AcquireTokenInteractive` 호출에서, 사용자 지정 웹 UI 인스턴스를 전달하는 `.WithCustomUI()` 한정자를 사용합니다.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>테스트 자동화에서 사용되는 ICustomWebUi의 구현 예 SeleniumWebUI

MSAL.NET 팀은 UI 테스트가 이 확장성 메커니즘을 사용할 수 있도록 수정했습니다. 관심이 있다면 MSAL.NET 소스 코드에서 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) 클래스를 살펴보세요.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>SystemWebViewOptions를 사용하여 우수한 환경 제공하기

MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)에서 다음을 지정할 수 있습니다.

- 시스템 웹 브라우저에서 로그인 또는 동의 오류가 발생한 경우 이동할 URI(`BrowserRedirectError`) 또는 표시할 HTML 조각(`HtmlMessageError`).
- 성공적인 로그인 또는 동의가 이루어진 경우 이동할 URI(`BrowserRedirectSuccess`) 또는 표시할 HTML 조각(`HtmlMessageSuccess`).
- 시스템 브라우저를 시작하기 위해 실행할 작업. `OpenBrowserAsync` 대리자를 설정하여 자체 구현을 제공할 수 있습니다. 이 클래스는 두 개의 브라우저를 위한 기본 구현도 제공합니다. Microsoft Edge의 경우 `OpenWithEdgeBrowserAsync`이고, [Chromium의 Microsoft Edge](https://www.windowscentral.com/faq-edge-chromium)의 경우 `OpenWithChromeEdgeBrowserAsync`입니다.

이 구조체를 사용하려면 다음 예와 같은 코드를 작성합니다.

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>그 밖의 선택적 매개 변수

`AcquireTokenInteractive`의 다른 모든 선택적 매개 변수에 대해 자세히 알아보려면 [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)를 참조하세요.

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python은 대화형 토큰 획득 방법을 직접적으로 제공하지 않습니다. 그 대신 애플리케이션이 사용자 상호 작용 흐름 구현에서 인증 요청을 보내서 인증 코드를 획득하도록 요구합니다. 이 코드를 `acquire_token_by_authorization_code` 메서드로 전달하면 토큰을 가져올 수 있습니다.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에서

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

도메인 또는 Azure AD에 가입된 컴퓨터에서 도메인 사용자를 로그인하려면 IWA(Windows 통합 인증)를 사용합니다.

### <a name="constraints"></a>제약 조건

- Windows 통합 인증은 ‘페더레이션’ 사용자(즉, Active Directory에서 만들어지고 Azure AD에서 지원하는 사용자)만 지원합니다. Active Directory 지원 없이 Azure AD에서 직접 만들어진 사용자(‘관리형’ 사용자)는 이 인증 흐름을 사용할 수 없습니다. 이러한 제한은 사용자 이름 및 암호 흐름에 영향을 주지 않습니다.
- IWA는 .NET Framework, .NET Core 및 UWP(유니버설 Windows 플랫폼)용으로 작성된 앱을 위한 것입니다.
- IWA는 [MFA(다단계 인증)](../authentication/concept-mfa-howitworks.md)를 우회하지 않습니다. MFA가 구성된 경우, MFA 질문이 필요하면 IWA가 실패할 수 있습니다. MFA에는 사용자 상호 작용이 필요하기 때문입니다.
  > [!NOTE]
  > 이 부분은 까다로운 지점입니다. IWA는 비대화형이지만 MFA에는 사용자 상호 작용이 필요합니다. ID 공급자가 MFA를 언제 요구할지는 개발자가 아니라 테넌트 관리자가 제어합니다. 일반적으로 다른 국가/지역에서 로그인할 때, VPN을 통해 회사 네트워크에 연결되어 있지 않을 때, 그리고 간혹 VPN을 통해 연결되어 있을 때도 MFA가 필요합니다. 결정적 규칙 세트가 적용되지 않습니다. Azure AD는 AI를 사용하여 MFA가 필요한지 여부를 계속해서 학습합니다. IWA가 실패할 경우 대화형 인증이나 디바이스 코드 흐름과 같은 사용자 프롬프트를 사용하세요.

- `PublicClientApplicationBuilder`에 포함되어 전달되는 인증 기관은 다음이어야 합니다.
  - 테넌트(`https://login.microsoftonline.com/{tenant}/` 형식, 여기서 `tenant`는 테넌트 ID 또는 테넌트와 연결된 도메인을 나타내는 GUID).
  - 회사 및 학교 계정의 경우: `https://login.microsoftonline.com/organizations/`.
  - Microsoft 개인 계정은 지원되지 않습니다. /common 테넌트나 /consumers 테넌트는 사용할 수 없습니다.

- Windows 통합 인증은 자동 흐름이기 때문에,
  - 애플리케이션의 사용자가 사전에 애플리케이션 사용에 동의했어야 합니다.
  - 또는 테넌트 관리자자 사전에 테넌트의 모든 사용자에 대해 애플리케이션 사용을 동의했어야 합니다.
  - 다시 말하면,
    - 개발자가 직접 Azure Portal에서 **권한 부여** 단추를 선택했어야 합니다.
    - 또는 테넌트 관리자가 애플리케이션 등록의 **API 권한** 탭에서 **{테넌트 도메인}에 대한 관리자 동의 권한 부여/해지**를 선택했어야 합니다. 자세한 내용은 [웹 API 액세스 권한 추가](./quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)를 참조하세요.
    - 또는 사용자가 애플리케이션에 동의할 방법을 제공했어야 합니다. 자세한 내용은 [개별 사용자의 동의 요청](./v2-permissions-and-consent.md#requesting-individual-user-consent)을 참조하세요.
    - 또는 테넌트 관리자가 애플리케이션에 동의할 방법을 제공했어야 합니다. 자세한 내용은 [관리자 동의](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)를 참조하세요.

- 이 흐름은 .NET 데스크톱, .NET Core 및 UWP 앱에 대해 사용하도록 설정됩니다.

동의에 대한 자세한 내용은 [Microsoft ID 플랫폼 엔드포인트의 권한 및 동의](./v2-permissions-and-consent.md)를 참조하세요.

### <a name="learn-how-to-use-it"></a>사용 방법 알아보기

# <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET에서 다음을 사용해야 합니다.

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

통상적으로는 하나의 매개 변수만 필요합니다(`scopes`). Windows 관리자가 정책을 어떻게 설정했는지에 따라, Windows 컴퓨터의 애플리케이션이 로그인한 사용자를 조회하는 것이 허용되지 않을 수 있습니다. 이 경우 두 번째 방법인 `.WithUsername()`을 사용하여 로그인한 사용자의 사용자 이름을 UPN 형식으로 전달합니다(예: `joe@contoso.com`). .NET Core 플랫폼은 OS에 사용자 이름을 물을 수 없으므로 .NET Core에서는 사용자 이름을 받는 오버로드만 사용할 수 있습니다.

다음 샘플에서는 가장 최근 사례를 보여 줍니다. 이때 발생할 수 있는 예외와 그 대응 방법에 대한 설명도 제시합니다.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

AcquireTokenByIntegratedWindowsAuthentication의 가능한 한정자 목록은 [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)를 참조하세요.

# <a name="java"></a>[Java](#tab/java)

다음 발췌본은 [MSAL Java 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)에서 가져온 것입니다.

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

이 흐름은 MSAL Python에서 아직 지원되지 않습니다.

# <a name="macos"></a>[macOS](#tab/macOS)

이 흐름은 macOS에 적용되지 않습니다.

---

## <a name="username-and-password"></a>사용자 이름 및 암호

사용자 이름과 암호를 제공하여 토큰을 획득할 수도 있습니다. 이 흐름은 제한적이며 권장되지 않지만, 이 흐름이 필요한 몇 가지 사용 사례가 존재합니다.

### <a name="this-flow-isnt-recommended"></a>이 흐름은 권장되지 않습니다.

애플리케이션이 사용자에게 암호를 묻는 것은 안전하지 않으므로 이 흐름은 ‘권장되지 않습니다’. 자세한 내용은 [What’s the solution to the growing problem of passwords?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)(늘어나는 암호 문제에 대한 해결책)를 참조하세요. Windows 도메인에 가입된 컴퓨터에서 자동으로 토큰을 획득하는 기본 흐름은 [Windows 통합 인증](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)입니다. [디바이스 코드 흐름](https://aka.ms/msal-net-device-code-flow)을 사용할 수도 있습니다.

> [!NOTE]
> 사용자 이름과 암호를 사용하는 것은 DevOps 시나리오와 같은 몇몇 경우에 유용합니다. 그러나 자체 UI를 제공하는 대화형 시나리오에서 사용자 이름과 암호를 사용하려는 경우에는 다른 방법을 고민하는 것이 좋습니다. 사용자 이름과 암호를 사용하면 다음을 포기하는 것입니다.
>
> - 최신 ID의 핵심 개념. 공유 비밀이 가로채기를 당할 수 있으므로 암호는 피싱될 수 있고 리플레이될 수 있습니다. 암호 없는 인증과 호환되지 않습니다.
> - MFA를 사용해야 하는 사용자의 경우 상호 작용이 없어 로그인할 수 없습니다.
> - 사용자가 SSO(Single Sign-On)를 사용할 수 없습니다.

### <a name="constraints"></a>제약 조건

다음과 같은 제약 조건도 적용됩니다.

- 사용자 이름 및 암호 흐름은 조건부 액세스 및 다단계 인증과 호환되지 않습니다. 따라서 많은 조직에서 그러하듯 테넌트 관리자가 다단계 인증을 요구하는 Azure AD 테넌트에서 앱이 실행되는 경우에는 이 흐름을 사용할 수 없습니다.
- MSA가 아닌 회사 및 학교 계정에서만 사용할 수 있습니다.
- 이 흐름은 .NET 데스크톱 및 .NET Core에서 사용할 수 있지만 UWP에서는 사용할 수 없습니다.

### <a name="b2c-specifics"></a>B2C 관련 사항

자세한 내용은 [Resource Owner Password Credentials (ROPC) with B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)(B2C를 사용한 ROPC(리소스 소유자 암호 자격 증명))를 참조하세요.

### <a name="use-it"></a>사용해 보기

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`은 `AcquireTokenByUsernamePassword` 메서드를 포함합니다.

다음 샘플에서는 간단한 사례를 보여 줍니다.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

다음 샘플에서는 가장 최근 사례를 보여 줍니다. 이때 발생할 수 있는 예외와 그 대응 방법에 대한 설명도 제시합니다.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

`AcquireTokenByUsernamePassword`에 적용할 수 있는 모든 한정자에 대한 자세한 내용은 [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)를 참조하세요.

# <a name="java"></a>[Java](#tab/java)

다음 발췌본은 [MSAL Java 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)에서 가져온 것입니다.

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

다음 발췌본은 [MSAL Python 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)에서 가져온 것입니다.

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

이 흐름은 macOS용 MSAL에서 지원되지 않습니다.

---

## <a name="command-line-tool-without-a-web-browser"></a>웹 브라우저가 없는 명령줄 도구

### <a name="device-code-flow"></a>디바이스 코드 흐름

웹 컨트롤이 없는 명령줄 도구를 작성하고 있는데 앞에 나온 흐름을 사용할 수 없거나 사용하지 않고 싶다면 디바이스 코드 흐름을 사용해야 합니다.

Azure AD를 사용한 대화형 인증에는 웹 브라우저가 필요합니다. 자세한 내용은 [Usage of web browsers](https://aka.ms/msal-net-uses-web-browser)(웹 브라우저의 용도)를 참조하세요. 디바이스 코드 흐름은 웹 브라우저를 제공하지 않는 디바이스나 운영 체제에서 사용자를 인증하기 위해 사용자가 컴퓨터나 휴대폰과 같은 다른 디바이스를 사용하여 대화형으로 로그인할 수 있도록 지원합니다. 애플리케이션은 디바이스 코드 흐름을 사용하여 이러한 디바이스 또는 OS용으로 설계된 2단계 프로세스를 통해 토큰을 가져옵니다. 이러한 애플리케이션의 예로 iOT 또는 CLI(명령줄) 도구에서 실행되는 애플리케이션을 들 수 있습니다. 기본 개념은 다음과 같습니다.

1. 사용자 인증이 필요할 때마다 앱이 사용자에게 코드를 제공합니다. 사용자는 다른 디바이스(예: 인터넷에 연결된 스마트폰)를 사용하여 URL(예: `https://microsoft.com/devicelogin`)로 이동해야 합니다. 그런 다음 사용자에게 코드를 입력하라는 메시지가 표시됩니다. 사용자가 코드를 입력하면 웹 페이지는 동의 프롬프트, 다단계 인증(필요한 경우) 등을 포함하는 일반적인 인증 환경으로 사용자를 안내합니다.

2. 인증에 성공하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신하고 이를 사용하여 필요한 웹 API 호출을 수행합니다.

### <a name="use-it"></a>사용해 보기

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`은 `AcquireTokenWithDeviceCode` 메서드를 포함합니다.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

이 메서드는 다음과 같은 매개 변수를 받습니다.

- 액세스 토큰을 요청할 `scopes`.
- `DeviceCodeResult`를 수신하는 콜백.

  ![DeviceCodeResult 속성](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

다음 샘플 코드에서는 가장 최근 사례를 보여 줍니다. 이때 발생할 수 있는 예외와 그 대응 방법에 대한 설명도 제시합니다.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

다음 발췌본은 [MSAL Java 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)에서 가져온 것입니다.

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

다음 발췌본은 [MSAL Python 개발자 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)에서 가져온 것입니다.

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[macOS](#tab/macOS)

이 흐름은 macOS에 적용되지 않습니다.

---

## <a name="file-based-token-cache"></a>파일 기반 토큰 캐시

MSAL.NET에서는 메모리 내 토큰 캐시가 기본적으로 제공됩니다.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Windows 데스크톱 앱과 웹앱 또는 웹 API에서 사용자 지정 가능한 직렬화

.NET Framework 및 .NET Core의 경우, 개발자가 추가 작업을 수행하지 않으면 메모리 내 토큰 캐시는 애플리케이션이 실행되는 동안 지속됩니다. 추가 설정 없이는 직렬화가 제공되지 않는 이유를 이해하려면, MSAL .NET 데스크톱 또는 .NET Core 애플리케이션은 (파일 시스템에 액세스할 수 있는) 콘솔이나 Windows 애플리케이션일 수 있지만 웹 애플리케이션이나 웹 API일 수도 있다는 점을 기억해야 합니다. 웹앱과 웹 API는 데이터베이스, 분산 캐시, Redis 캐시와 같은 특정 캐시 메커니즘을 사용할 수 있습니다. .NET 데스크톱 또는 .NET Core에서 영구 토큰 캐시 애플리케이션을 사용하려면 직렬화를 사용자 지정해야 합니다.

다음의 클래스 및 인터페이스는 토큰 캐시 직렬화에 사용됩니다.

- ``ITokenCache``: 토큰 캐시 직렬화 요청을 구독하는 이벤트, 그리고 캐시를 다양한 형식(ADAL v3.0, MSAL 2.x 및 MSAL 3.x = ADAL v5.0)으로 직렬화 또는 역직렬화하는 메서드를 정의합니다.
- ``TokenCacheCallback``은 직렬화를 처리할 수 있도록 이벤트에 전달되는 콜백입니다. ``TokenCacheNotificationArgs`` 형식의 인수를 사용하여 호출됩니다.
- ``TokenCacheNotificationArgs``는 사용자에게 토큰을 사용할 수 있는 애플리케이션 ``ClientId``와 참조만 제공합니다.

  ![토큰 캐시 직렬화 다이어그램](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> 사용자가 애플리케이션의 `UserTokenCache` 및 `AppTokenCache` 속성을 호출하면 MSAL.NET은 사용자 대신 토큰 캐시를 만들고 사용자에게 `IToken` 캐시를 제공합니다. 사용자가 인터페이스를 직접 구현할 필요가 없습니다. 사용자는 사용자 지정 토큰 캐시 직렬화를 구현할 때 다음과 같은 일만 하면 됩니다.
>
> - `BeforeAccess` 및 `AfterAccess` 이벤트(또는 그 ‘비동기’ 버전)에 대응합니다. `BeforeAccess` 대리자는 캐시를 역직렬화하는 역할을 담당하고, `AfterAccess` 대리자는 캐시를 직렬화하는 역할을 담당합니다.
> - 이러한 이벤트의 일부는 Blob을 저장하거나 로드하며, Blob은 이벤트 인수를 통해 사용자가 원하는 스토리지에 전달됩니다.

사용자가 작성하려는 토큰 캐시 직렬화가 공용 클라이언트 애플리케이션(예: 데스크톱)을 위한 것인지 아니면 기밀 클라이언트 애플리케이션(예: 웹앱/웹 API, 디먼 앱)을 위한 것인지에 따라 전략이 달라집니다.

MSAL v2.x부터는 몇 가지 옵션을 사용할 수 있습니다. 어느 것을 선택할 것인지는 캐시를 MSAL뿐 아니라 여러 플랫폼에서도 공통으로 사용되는 통합 형식 캐시인 MSAL.NET 형식으로만 직렬화할 것인지 여부에 따라 달라집니다. ADAL v3의 [레거시](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) 토큰 캐시 직렬화를 지원하고자 할 수도 있습니다.

ADAL.NET 3.x, ADAL.NET 5.x 및 MSAL.NET 간에 SSO 상태를 공유하도록 토큰 캐시 직렬화를 사용자 지정하는 방법은 [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) 샘플에 설명되어 있습니다.

### <a name="simple-token-cache-serialization-msal-only"></a>간단한 토큰 캐시 직렬화(MSAL만 해당)

다음은 데스크톱 애플리케이션의 토큰 캐시를 사용자 지정 직렬화하는 간단한 예입니다. 여기서 사용자 토큰 캐시는 애플리케이션과 동일한 폴더에 있는 파일에 있습니다.

애플리케이션을 빌드한 후에 ``TokenCacheHelper.EnableSerialization()``을 호출하고 애플리케이션 `UserTokenCache`를 전달하여 직렬화를 사용하도록 설정합니다.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

이 도우미 클래스는 다음 코드 조각과 같습니다.

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

공용 클라이언트 애플리케이션(Windows, Mac 및 Linux에서 실행되는 데스크톱 애플리케이션)을 위한 제품 품질 토큰 캐시 파일 기반 직렬 변환기의 미리 보기는 [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 오픈 소스 라이브러리에서 확인할 수 있습니다. 다음 NuGet 패키지에서 이 미리 보기를 애플리케이션에 포함할 수 있습니다. [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> 고지 사항: Microsoft.Identity.Client.Extensions.Msal 라이브러리는 MSAL.NET의 확장입니다. 이 라이브러리의 클래스는 있는 그대로 또는 호환성이 손상되는 변경과 함께 향후 MSAL.NET에 포함될 수 있습니다.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>이중 토큰 캐시 직렬화(MSAL 통합 캐시 및 ADAL v3)

토큰 캐시 직렬화를 통합 캐시 형식으로 구현하는 것이 좋습니다. 이 형식은 ADAL.NET 4.x와 MSAL.NET 2.x에서, 그리고 동일한 플랫폼에서 동일 세대 또는 이전 세대의 다른 MSAL에서 공통적으로 적용됩니다. 다음 코드를 살펴보세요.

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

이번에는 도우미 클래스가 다음 코드와 같습니다.

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱에서 웹 API 호출](scenario-desktop-call-api.md)