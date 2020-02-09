---
title: 웹 API를 호출 하는 토큰 얻기 (데스크톱 앱) | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하 여 앱에 대 한 토큰을 획득 하는 데스크톱 앱을 빌드하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 50ac62ded92b69f44324f4f9c5eacee939159449
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834130"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>웹 Api를 호출 하는 데스크톱 앱: 토큰 획득

공용 클라이언트 응용 프로그램의 인스턴스를 빌드한 후에는이를 사용 하 여 웹 API를 호출 하는 데 사용할 토큰을 가져옵니다.

## <a name="recommended-pattern"></a>권장 패턴

웹 API는 해당 `scopes`에 의해 정의 됩니다. 응용 프로그램에서 제공 하는 경험이 무엇이 든 사용할 패턴은 다음과 같습니다.

- `AcquireTokenSilent`를 호출 하 여 토큰 캐시에서 토큰 가져오기를 체계적으로 시도 합니다.
- 이 호출이 실패 하면 사용 하려는 `AcquireToken` 흐름을 사용 합니다 .이 흐름은 `AcquireTokenXX`에 표시 됩니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

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

# <a name="javatabjava"></a>[Java](#tab/java)

```java
CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();
    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL

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

데스크톱 응용 프로그램에서 토큰을 획득 하는 다양 한 방법은 다음과 같습니다.

## <a name="acquire-a-token-interactively"></a>대화형으로 토큰 획득

다음 예에서는 Microsoft Graph를 사용 하 여 사용자 프로필을 읽기 위해 대화형으로 토큰을 가져오는 최소한의 코드를 보여 줍니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)
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

`AcquireTokenInteractive`에는 필수 매개 변수 ``scopes``하나만 있습니다 .이 매개 변수는 토큰을 필요로 하는 범위를 정의 하는 문자열의 열거형을 포함 합니다. Microsoft Graph에 대 한 토큰의 경우 필요한 범위는 "Permissions" 섹션의 각 Microsoft Graph API에 대 한 API 참조에서 찾을 수 있습니다. 예를 들어 [사용자의 연락처를 나열](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)하려면 "사용자. 읽기", "연락처. 읽기" 범위를 사용 해야 합니다. 자세한 내용은 [Microsoft Graph 권한 참조](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)를 참조 하세요.

또한 Android에서 표시 된 대로 `.WithParentActivityOrWindow`를 사용 하 여 부모 작업을 지정 해야 합니다. 그러면 토큰이 상호 작용 후 해당 부모 작업으로 돌아갑니다. 지정 하지 않으면 `.ExecuteAsync()`를 호출할 때 예외가 throw 됩니다.

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET의 특정 선택적 매개 변수

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI는 대화형 이므로 중요 합니다. `AcquireTokenInteractive`에는이를 지 원하는 플랫폼에서 부모 UI를 지정할 수 있는 하나의 특정 선택적 매개 변수가 있습니다. 데스크톱 응용 프로그램에서 사용 하는 경우 `.WithParentActivityOrWindow`은 플랫폼에 따라 다른 형식을 사용 합니다.

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

- .NET Standard에서 예상 `object`는 Android, iOS의 `UIViewController`, MAC에서는 `NSWindow`, Windows의 경우 `IWin32Window` 또는 `IntPr` `Activity` 됩니다.
- Windows에서는 포함 된 브라우저가 적절 한 UI 동기화 컨텍스트를 가져오기 위해 UI 스레드에서 `AcquireTokenInteractive`를 호출 해야 합니다. UI 스레드에서를 호출 하지 않으면 메시지가 UI를 사용 하 여 제대로 펌프 되지 않고 교착 상태가 발생 하지 않을 수 있습니다. Ui 스레드를 사용 하지 않는 경우 UI 스레드에서 MSALs (Microsoft 인증 라이브러리)를 호출 하는 한 가지 방법은 WPF에서 `Dispatcher`를 사용 하는 것입니다.
- Wpf를 사용 하는 경우 WPF 컨트롤에서 창을 가져오려면 `WindowInteropHelper.Handle` 클래스를 사용할 수 있습니다. 그런 다음 WPF 컨트롤 (`this`)에서 호출을 수행 합니다.

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`는 프롬프트를 지정 하 여 사용자와의 상호 작용을 제어 하는 데 사용 됩니다.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

클래스는 다음 상수를 정의 합니다.

- ``SelectAccount`` STS가 사용자에 게 세션이 있는 계정을 포함 하는 계정 선택 대화 상자를 표시 하도록 합니다. 이 옵션은 응용 프로그램 개발자가 다양 한 id 중에서 선택할 수 있도록 하려는 경우에 유용 합니다. 이 옵션은 MSAL을 구동 하 여 id 공급자에 ``prompt=select_account``을 보냅니다. 이 옵션이 기본값입니다. 사용자에 대 한 세션의 현재 상태와 같은 사용 가능한 정보에 따라 최상의 환경을 제공 하는 것이 좋습니다. 이 작업을 수행 해야 하는 경우를 제외 하 고는 변경 하지 마십시오.
- ``Consent``를 통해 응용 프로그램 개발자는 이전에 동의가 부여 된 경우에도 사용자에 게 동의 여부를 묻는 메시지가 표시 되도록 할 수 있습니다. 이 경우 MSAL은 id 공급자에 `prompt=consent`를 보냅니다. 이 옵션은 조직에서 응용 프로그램이 사용 될 때마다 사용자에 게 동의 대화 상자가 표시 되는 것을 요구 하는 일부 보안 중심 응용 프로그램에서 사용할 수 있습니다.
- 이 사용자 프롬프트가 필요 하지 않은 경우에도 응용 프로그램 개발자가 사용자에 게 서비스에서 자격 증명을 묻는 메시지를 표시 하도록 ``ForceLogin`` 수 있습니다. 이 옵션은 토큰을 획득 하는 데 실패 하는 경우 사용자가 다시 로그인 하도록 하는 데 유용할 수 있습니다. 이 경우 MSAL은 id 공급자에 `prompt=login`를 보냅니다. 조직에서 사용자가 응용 프로그램의 특정 부분에 액세스할 때마다 다시 로그인 하도록 요구 하는 보안 중심 응용 프로그램에서 사용 되는 경우가 있습니다.
- ``Never`` (.NET 4.5 및 WinRT에만 해당)은 사용자에 게 메시지를 표시 하지 않고 대신 숨겨진 포함 웹 보기에 저장 된 쿠키를 사용 하려고 합니다. 자세한 내용은 MSAL.NET의 웹 보기를 참조 하십시오. 이 옵션을 사용 하면 실패할 수 있습니다. 이 경우 `AcquireTokenInteractive`는 예외를 throw 하 여 UI 조작이 필요 함을 알립니다. 다른 `Prompt` 매개 변수를 사용 해야 합니다.
- ``NoPrompt``은 id 공급자에 게 메시지를 보내지 않습니다. 이 옵션은 Azure Active Directory (Azure AD) B2C 프로필 정책 편집에만 유용 합니다. 자세한 내용은 [Azure AD B2C](https://aka.ms/msal-net-b2c-specificities)관련 항목을 참조 하세요.

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

이 한정자는 사용자가 사전에 여러 리소스에 대 한 동의를 원하는 고급 시나리오에서 사용 되며, 일반적으로 MSAL.NET/the Microsoft id 플랫폼과 함께 사용 되는 증분 동의를 사용 하지 않으려고 합니다. 자세한 내용은 [사용자에 게 여러 리소스에 대 한 사전 승인이 필요](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)합니다 .를 참조 하세요.

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

웹 UI는 브라우저를 호출 하는 메커니즘입니다. 이 메커니즘은 전용 UI WebBrowser 컨트롤 이거나 브라우저 열기를 위임 하는 방법일 수 있습니다.
MSAL은 대부분의 플랫폼에 대 한 웹 UI 구현을 제공 하지만 브라우저를 직접 호스트 하려는 경우가 있습니다.

- MSAL에서 명시적으로 적용 되지 않는 플랫폼 (예: 데스크톱의 Blazor, Unity 및 Mono)입니다.
- 응용 프로그램의 UI를 테스트 하 고 Selenium와 함께 사용할 수 있는 자동화 된 브라우저를 사용 하려고 합니다.
- MSAL을 실행 하는 브라우저와 앱은 별도의 프로세스에 있습니다.

##### <a name="at-a-glance"></a>한눈에 보기

이를 위해 최종 사용자가 자신의 사용자 이름과 같은 항목을 입력할 수 있도록 선택한 브라우저에 표시 해야 하는 MSAL `start Url`에 제공 합니다.
인증이 완료 된 후 앱은 Azure AD에서 제공 하는 코드를 포함 하는 MSAL `end Url`에 다시 전달 해야 합니다.
`end Url`의 호스트는 항상 `redirectUri`됩니다. `end Url`가로채기 위해 다음 중 하나를 수행 합니다.

- `redirect Url` 적중 될 때까지 브라우저 리디렉션을 모니터링 합니다.
- 사용자가 모니터링 하는 URL로 브라우저가 리디렉션됩니다.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi는 확장성 지점입니다.

`WithCustomWebUi`는 공용 클라이언트 응용 프로그램에서 사용자 고유의 UI를 제공 하는 데 사용할 수 있는 확장성 지점입니다. 사용자가 id 공급자의/권한 부여 엔드포인트을 통과 하 여 로그인 하 고 동의할 수 있게 할 수도 있습니다. 그런 다음 MSAL.NET는 인증 코드를 교환 하 고 토큰을 가져올 수 있습니다. 예를 들어 Visual Studio에서 파괴 응용 프로그램 (예: Visual Studio 사용자 의견)이 웹 상호 작용을 제공 하는 데 사용 되며 대부분의 작업을 수행 하는 데 MSAL.NET로 유지 됩니다. UI 자동화를 제공 하려는 경우에도 사용할 수 있습니다. 공용 클라이언트 응용 프로그램에서 MSAL.NET는 PKCE (증명 키 교환) 표준을 사용 하 여 보안이 적용 되도록 합니다. MSAL.NET만 코드를 사용할 수 있습니다. 자세한 내용은 [RFC 7636-OAuth 공용 클라이언트의 코드 교환에 대 한 증명 키](https://tools.ietf.org/html/rfc7636)를 참조 하세요.

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>WithCustomWebUi 사용

`.WithCustomWebUI`를 사용 하려면 다음 단계를 수행 합니다.

  1. `ICustomWebUi` 인터페이스를 구현합니다. 자세한 내용은 [이 웹 사이트](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)를 참조 하세요. `AcquireAuthorizationCodeAsync`메서드 하나를 구현 하 고 MSAL.NET에서 계산 된 인증 코드 URL을 적용 합니다. 그런 다음 사용자가 id 공급자와의 상호 작용을 통해 id 공급자가 구현 하는 데 사용 하는 URL을 다시 인증 코드와 함께 반환 하도록 합니다. 문제가 발생 하는 경우에는 MSAL과의 적절 한 상호 작용을 위해 구현에서 `MsalExtensionException` 예외를 throw 해야 합니다.
  2. `AcquireTokenInteractive` 호출에서 사용자 지정 웹 UI의 인스턴스를 전달 하는 `.WithCustomUI()` 한정자를 사용 합니다.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>테스트 자동화의 ICustomWebUi 구현 예: SeleniumWebUI

MSAL.NET 팀은이 확장성 메커니즘을 사용 하도록 UI 테스트를 다시 작성 했습니다. 관심이 있는 경우 MSAL.NET 소스 코드에서 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) 클래스를 확인 하세요.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>SystemWebViewOptions를 사용 하 여 뛰어난 환경 제공

MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)에서 다음을 지정할 수 있습니다.

- 시스템 웹 브라우저에서 로그인 또는 동의 오류가 발생할 경우 표시할 대상 (`BrowserRedirectError`) 또는 HTML 조각 (`HtmlMessageError`)으로 이동할 URI입니다.
- 성공적으로 로그인 하거나 동의할 경우 (`BrowserRedirectSuccess`) 또는 표시할 HTML 조각 (`HtmlMessageSuccess`)으로 이동할 URI입니다.
- 시스템 브라우저를 시작 하기 위해 실행할 작업입니다. `OpenBrowserAsync` 대리자를 설정 하 여 고유한 구현을 제공할 수 있습니다. 또한 클래스는 두 개의 브라우저에 대해 기본 구현을 제공 합니다. `OpenWithEdgeBrowserAsync` 및 `OpenWithChromeEdgeBrowserAsync` for Microsoft Edge 및 [Microsoft edge For Chromium](https://www.windowscentral.com/faq-edge-chromium)를 각각 제공 합니다.

이 구조를 사용 하려면 다음 예제와 같은 내용을 작성 합니다.

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

#### <a name="other-optional-parameters"></a>기타 선택적 매개 변수

`AcquireTokenInteractive`에 대 한 다른 모든 선택적 매개 변수에 대 한 자세한 내용은 [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)를 참조 하세요.

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java는 대화형 획득 토큰 메서드를 직접 제공 하지 않습니다. 대신, 권한 부여 코드를 얻기 위해 응용 프로그램에서 사용자 상호 작용 흐름의 구현에 권한 부여 요청을 보내야 합니다. 그런 다음이 코드를 `acquireToken` 메서드로 전달 하 여 토큰을 가져올 수 있습니다.

```java
AuthorizationCodeParameters parameters =  AuthorizationCodeParameters.builder(
                authorizationCode, redirectUri)
                .build();
CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();
    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python은 대화형 획득 토큰 메서드를 직접 제공 하지 않습니다. 대신, 권한 부여 코드를 얻기 위해 응용 프로그램에서 사용자 상호 작용 흐름의 구현에 권한 부여 요청을 보내야 합니다. 그런 다음이 코드를 `acquire_token_by_authorization_code` 메서드로 전달 하 여 토큰을 가져올 수 있습니다.

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>IOS 및 macOS 용 MSAL

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

도메인 또는 Azure AD 가입 컴퓨터에서 도메인 사용자를 로그인 하려면 IWA (Windows 통합 인증)를 사용 합니다.

### <a name="constraints"></a>제약 조건

- 통합 Windows 인증은 *페더레이션 +* 사용자 에게만 사용할 수 있습니다. 즉, Active Directory에서 만들어지고 Azure AD에서 지원 됩니다. *관리 되는* 사용자 라고 하는 Active Directory 백업 없이 Azure AD에서 직접 만든 사용자는이 인증 흐름을 사용할 수 없습니다. 이 제한은 사용자 이름 및 암호 흐름에 영향을 주지 않습니다.
- IWA는 .NET Framework, .NET Core 및 유니버설 Windows 플랫폼 (UWP) 플랫폼용으로 작성 된 앱에 대 한 것입니다.
- IWA는 MFA (다단계 인증)를 무시 하지 않습니다. Mfa가 구성 된 경우 mfa에 사용자 조작이 필요 하기 때문에 mfa 챌린지가 필요한 경우 IWA가 실패할 수 있습니다.
  > [!NOTE]
  > 이 방법은 복잡 합니다. IWA는 비 대화형 이지만 MFA를 사용 하려면 사용자의 상호 작용이 필요 합니다. Id 공급자가 MFA를 수행 하도록 요청 하는 시간을 제어 하지 않습니다. 테넌트 관리자가 수행 합니다. 이러한 관찰에서 MFA는 VPN을 통해 회사 네트워크에 연결 되어 있지 않은 경우, 때로는 VPN을 통해 연결 된 경우에도 다른 국가에서 로그인 할 때 필요 합니다. 결정적 규칙 집합은 필요 하지 않습니다. Azure AD는 AI를 사용 하 여 MFA가 필요한 경우 지속적으로 학습 합니다. IWA 실패 하는 경우 대화형 인증 또는 장치 코드 흐름과 같은 사용자 프롬프트로 대체 합니다.

- `PublicClientApplicationBuilder` 전달 된 기관은 다음을 충족 해야 합니다.
  - 테넌트 `https://login.microsoftonline.com/{tenant}/`형식입니다. 여기서 `tenant`은 테넌트 ID 또는 테넌트와 연결 된 도메인을 나타내는 GUID입니다.
  - 회사 및 학교 계정의 경우: `https://login.microsoftonline.com/organizations/`합니다.
  - Microsoft 개인 계정은 지원 되지 않습니다. /Common 또는/소비자 테넌트를 사용할 수 없습니다.

- Windows 통합 인증은 자동 흐름입니다.
  - 응용 프로그램의 사용자에 게는 응용 프로그램을 사용 하기 위해 이전에 동의한 있어야 합니다.
  - 또는 테넌트 관리자가 응용 프로그램을 사용 하려면 테넌트의 모든 사용자에 게 이전에 동의한 해야 합니다.
  - 즉, 다음을 수행 합니다.
    - 개발자가 자신에 대 한 Azure Portal에서 **Grant** 단추를 선택 했습니다.
    - 또는 테넌트 관리자가 응용 프로그램 등록의 **API 사용 권한** 탭에 있는 **{테넌트 도메인}에 대 한 관리자 동의 허용/취소** 단추를 선택 했습니다. 자세한 내용은 [웹 api에 액세스할 수 있는 권한 추가](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)를 참조 하세요.
    - 또는 사용자가 응용 프로그램에 동의할 수 있는 방법을 제공 했습니다. 자세한 내용은 [개별 사용자 동의 요청](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)을 참조 하세요.
    - 또는 테넌트 관리자가 응용 프로그램에 동의할 수 있는 방법을 제공 했습니다. 자세한 내용은 [관리자 동의](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)를 참조 하세요.

- 이 흐름은 .NET 데스크톱, .NET Core 및 UWP 앱에 대해 사용 하도록 설정 됩니다.

승인에 대 한 자세한 내용은 [Microsoft id 플랫폼 권한 및 동의](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)를 참조 하세요.

### <a name="learn-how-to-use-it"></a>사용 방법 알아보기

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

MSAL.NET에서 다음을 사용 해야 합니다.

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

일반적으로 하나의 매개 변수 (`scopes`)만 필요 합니다. Windows 관리자가 정책을 설정 하는 방식에 따라 Windows 컴퓨터의 응용 프로그램에서 로그인 한 사용자를 조회 하지 못할 수 있습니다. 이 경우 두 번째 메서드인 `.WithUsername()`를 사용 하 고 로그인 한 사용자의 사용자 이름을 UPN 형식으로 전달 합니다 (예: `joe@contoso.com`). .Net core에서는 .NET Core 플랫폼이 사용자 이름을 OS에 요청할 수 없기 때문에 사용자 이름을 사용 하는 오버 로드만 사용할 수 있습니다.

다음 샘플에서는 가장 최신 사례를 제공 하며,이를 통해 얻을 수 있는 예외의 종류와 완화 방법을 설명 합니다.

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

AcquireTokenByIntegratedWindowsAuthentication에서 사용할 수 있는 한정자 목록은 [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)를 참조 하세요.

# <a name="javatabjava"></a>[Java](#tab/java)

이 추출은 [Msal Java dev 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)에서 가져온 것입니다. 다음은 샘플을 구성 하기 위해 MSAL Java dev 샘플에서 사용 되는 클래스입니다. [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
         .authority(TestData.AUTHORITY_ORGANIZATION)
         .telemetryConsumer(new Telemetry.MyTelemetryConsumer().telemetryConsumer)
         .build();

 IntegratedWindowsAuthenticationParameters parameters =
         IntegratedWindowsAuthenticationParameters.builder(
                 Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE), TestData.USER_NAME)
                 .build();

 Future<IAuthenticationResult> future = app.acquireToken(parameters);

 IAuthenticationResult result = future.get();

 return result;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

이 흐름은 MSAL Python에서 아직 지원 되지 않습니다.

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

이 흐름은 MacOS에 적용 되지 않습니다.

---

## <a name="username-and-password"></a>사용자 이름 및 암호

사용자 이름 및 암호를 제공 하 여 토큰을 가져올 수도 있습니다. 이 흐름은 제한적 이며 권장 되지 않지만 필요한 경우 사용 사례가 계속 사용 됩니다.

### <a name="this-flow-isnt-recommended"></a>이 흐름은 권장 되지 않습니다.

응용 프로그램에서 사용자에 게 암호를 묻는 메시지를 표시 하는 것은 안전 하지 않으므로이 흐름은 *권장 되지 않습니다* . 자세한 내용은 [암호의 증가 하는 문제에 대 한 해결책은 무엇 인가요?를](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)참조 하세요. Windows 도메인 가입 컴퓨터에서 자동으로 토큰을 획득 하는 기본 흐름은 [Windows 통합 인증](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)입니다. [장치 코드 흐름](https://aka.ms/msal-net-device-code-flow)을 사용할 수도 있습니다.

> [!NOTE]
> 사용자 이름 및 암호를 사용 하는 것은 DevOps 시나리오와 같은 경우에 유용 합니다. 그러나 사용자 고유의 UI를 제공 하는 대화형 시나리오에서 사용자 이름 및 암호를 사용 하려는 경우에는 외부로 이동 하는 방법을 고려해 야 합니다. 사용자 이름 및 암호를 사용 하 여 다음과 같은 여러 가지를 제공 합니다.
>
> - 최신 id의 핵심 개념. 공유 암호를 가로챌 수 있으므로 암호를 피싱 하 고 재생할 수 있습니다. Passwordless와 호환 되지 않습니다.
> - MFA를 수행 해야 하는 사용자는 상호 작용이 없으므로 로그인 할 수 없습니다.
> - 사용자는 SSO (Single Sign-On)를 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

다음 제약 조건도 적용 됩니다.

- 사용자 이름 및 암호 흐름은 조건부 액세스 및 다단계 인증과 호환 되지 않습니다. 결과적으로 테넌트 관리자가 다단계 인증을 필요로 하는 Azure AD 테넌트에서 앱이 실행 되는 경우이 흐름을 사용할 수 없습니다. 많은 조직에서이 작업을 수행 합니다.
- 회사 및 학교 계정 (MSA 아님)에 대해서만 작동 합니다.
- 흐름은 .NET 데스크톱 및 .NET Core에서 사용할 수 있지만 UWP에서는 사용할 수 없습니다.

### <a name="b2c-specifics"></a>B2C 세부 정보

자세한 내용은 [B2C를 사용 하는 ROPC (리소스 소유자 암호 자격 증명)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)를 참조 하세요.

### <a name="use-it"></a>사용

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

`IPublicClientApplication``AcquireTokenByUsernamePassword`메서드를 포함 합니다.

다음 샘플에서는 간단한 사례를 제공 합니다.

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

다음 샘플에서는 가장 최신 사례를 제공 하며,이를 통해 얻을 수 있는 예외의 종류와 완화 방법을 설명 합니다.

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

`AcquireTokenByUsernamePassword`에 적용할 수 있는 모든 한정자에 대 한 자세한 내용은 [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)를 참조 하세요.

# <a name="javatabjava"></a>[Java](#tab/java)

다음 추출은 [Msal Java dev 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)에서 가져온 것입니다. 다음은 샘플을 구성 하기 위해 MSAL Java dev 샘플에서 사용 되는 클래스입니다. [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_ORGANIZATION)
        .build();

UserNamePasswordParameters parameters = UserNamePasswordParameters.builder(
        Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
        TestData.USER_NAME,
        TestData.USER_PASSWORD.toCharArray())
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();

    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

이 추출은 [Msal Python dev 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)에서 가져온 것입니다.

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

이 흐름은 macOS 용 MSAL에서 지원 되지 않습니다.

---

## <a name="command-line-tool-without-a-web-browser"></a>웹 브라우저가 없는 명령줄 도구

### <a name="device-code-flow"></a>디바이스 코드 흐름

웹 컨트롤이 없고 이전 흐름을 사용 하지 않을 수 있는 명령줄 도구를 작성 하는 경우 장치 코드 흐름을 사용 해야 합니다.

Azure AD를 사용한 대화형 인증에는 웹 브라우저가 필요 합니다. 자세한 내용은 [웹 브라우저 사용](https://aka.ms/msal-net-uses-web-browser)을 참조 하세요. 웹 브라우저를 제공 하지 않는 장치 또는 운영 체제에서 사용자를 인증 하기 위해 장치 코드 흐름을 사용 하면 사용자가 컴퓨터나 휴대폰 등의 다른 장치를 사용 하 여 대화형으로 로그인 할 수 있습니다. 응용 프로그램은 장치 코드 흐름을 사용 하 여 이러한 장치 또는 Os 용으로 설계 된 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로는 iOT 또는 CLI (명령줄 도구)에서 실행 되는 응용 프로그램이 있습니다. 그 이유는 다음과 같습니다.

1. 사용자 인증이 필요한 경우 앱은 사용자에 대 한 코드를 제공 합니다. 사용자는 URL (예: `https://microsoft.com/devicelogin`)으로 이동 하기 위해 인터넷에 연결 된 스마트폰 등의 다른 장치를 사용 하 라는 메시지가 표시 됩니다. 그런 다음 사용자에 게 코드를 입력 하 라는 메시지가 표시 됩니다. 이 작업이 완료 되 면 웹 페이지에서 사용자에 게 승인 프롬프트 및 다단계 인증 (필요한 경우)을 포함 하는 일반 인증 환경을 통해 안내 합니다.

2. 인증에 성공 하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신 하 고이를 사용 하 여 필요한 웹 API 호출을 수행 합니다.

### <a name="use-it"></a>사용

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`에 `AcquireTokenWithDeviceCode`라는 메서드가 포함 되어 있습니다.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

이 메서드는를 매개 변수로 사용 합니다.

- 액세스 토큰을 요청할 `scopes`입니다.
- `DeviceCodeResult`받는 콜백입니다.

  ![DeviceCodeResult 속성](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

다음 샘플 코드는 가장 최신 사례를 제공 하며,이를 통해 얻을 수 있는 예외의 종류와 완화 방법을 설명 합니다.

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
# <a name="javatabjava"></a>[Java](#tab/java)

이 추출은 [Msal Java dev 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)에서 가져온 것입니다. 다음은 샘플을 구성 하기 위해 MSAL Java dev 샘플에서 사용 되는 클래스입니다. [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();

Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) -> {
    System.out.println(deviceCode.message());
};

CompletableFuture<IAuthenticationResult> future = app.acquireToken(
        DeviceCodeFlowParameters.builder(
                Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                deviceCodeConsumer)
                .build());

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception of type - " + ex.getClass());
        System.out.println("message - " + ex.getMessage());
        return "Unknown!";
    }
    System.out.println("Returned ok - " + res);

    return res;
});

future.join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

이 추출은 [Msal Python dev 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)에서 가져온 것입니다.

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

이 흐름은 MacOS에 적용 되지 않습니다.

---

## <a name="file-based-token-cache"></a>파일 기반 토큰 캐시

MSAL.NET에서는 메모리 내 토큰 캐시가 기본적으로 제공됩니다.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serialization은 Windows 데스크톱 앱 및 웹 앱 또는 웹 Api에서 사용자 지정할 수 있습니다.

.NET Framework 및 .NET Core의 경우 추가 작업을 수행 하지 않으면 메모리 내 토큰 캐시가 응용 프로그램 기간 동안 지속 됩니다. Serialization이 즉시 제공 되지 않는 이유를 이해 하려면 MSAL .NET 데스크톱 또는 .NET Core 응용 프로그램은 콘솔 또는 Windows 응용 프로그램 (파일 시스템에 액세스할 수 있음) 뿐만 *아니라* 웹 응용 프로그램 또는 웹 api 일 수 있습니다. 이러한 웹 앱 및 웹 Api는 데이터베이스, 분산 캐시 및 Redis 캐시와 같은 특정 캐시 메커니즘을 사용할 수 있습니다. .NET 데스크톱 또는 .NET Core에서 영구 토큰 캐시 응용 프로그램을 만들려면 serialization을 사용자 지정 해야 합니다.

토큰 캐시 직렬화와 관련 된 클래스 및 인터페이스는 다음과 같은 형식입니다.

- ``ITokenCache``는 토큰 캐시 serialization 요청을 구독할 이벤트를 정의 하 고 다양 한 형식으로 캐시를 직렬화 하거나 deserialize 하는 메서드를 정의 합니다 (adal v 3.0, msal 2.x 및 msal 에서처럼 = adal v 5.0).
- ``TokenCacheCallback``은 직렬화를 처리할 수 있도록 이벤트에 전달되는 콜백입니다. ``TokenCacheNotificationArgs`` 형식의 인수를 사용하여 호출됩니다.
- ``TokenCacheNotificationArgs``는 응용 프로그램 ``ClientId`` 및 토큰을 사용할 수 있는 사용자에 대 한 참조만 제공 합니다.

  ![토큰 캐시 serialization 다이어그램](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> 사용자가 애플리케이션의 `UserTokenCache` 및 `AppTokenCache` 속성을 호출하면 MSAL.NET은 사용자 대신 토큰 캐시를 만들고 사용자에게 `IToken` 캐시를 제공합니다. 인터페이스를 직접 구현할 필요가 없습니다. 사용자는 사용자 지정 토큰 캐시 직렬화를 구현할 때 다음과 같은 일만 하면 됩니다.
>
> - `BeforeAccess` 및 `AfterAccess` 이벤트 또는 *비동기* 대응에 대응 합니다. `BeforeAccess` 대리자는 캐시 deserialize를 담당 합니다. `AfterAccess` 대리자는 캐시를 직렬화 합니다.
> - 이러한 이벤트의 일부는 이벤트 인수를 통해 원하는 저장소에 전달 되는 blob을 저장 하거나 로드 합니다.

이 전략은 데스크톱과 같은 공용 클라이언트 응용 프로그램에 대 한 토큰 캐시 serialization을 작성 하는 경우 또는 웹 앱 또는 웹 API와 같은 기밀 클라이언트 응용 프로그램 또는 디먼 앱에 따라 달라 집니다.

MSAL v2. x부터 여러 가지 옵션을 사용할 수 있습니다. 캐시를 MSAL.NET 형식 으로만 serialize 할지 여부에 따라 선택 하는 것이 좋습니다 .이는 MSAL과 플랫폼 전체에서 공통적으로 사용 되는 통합 형식 캐시입니다. 또는 ADAL v3의 [레거시](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) 토큰 캐시 직렬화를 지원할 수도 있습니다.

ADAL.NET, ADAL.NET 및 MSAL.NET 간에 SSO 상태를 공유 하는 토큰 캐시 serialization의 사용자 지정은 sample [active directory-dotnet-v1-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)의 일부로 설명 됩니다.

### <a name="simple-token-cache-serialization-msal-only"></a>간단한 토큰 캐시 직렬화(MSAL만 해당)

다음 예제는 데스크톱 응용 프로그램에 대 한 토큰 캐시의 사용자 지정 serialization의 naive 구현입니다. 여기서 사용자 토큰 캐시는 응용 프로그램과 동일한 폴더에 있는 파일에 있습니다.

응용 프로그램을 빌드한 후에는 ``TokenCacheHelper.EnableSerialization()``를 호출 하 고 응용 프로그램 `UserTokenCache`를 전달 하 여 serialization을 사용 하도록 설정 합니다.

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

Windows, Mac 및 Linux에서 실행 되는 데스크톱 응용 프로그램의 공용 클라이언트 응용 프로그램에 대 한 제품 품질 토큰 캐시 파일 기반 serializer의 미리 보기는 [Microsoft](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 다음 NuGet 패키지의 응용 프로그램에이를 포함할 수 [있습니다..](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)

> [!NOTE]
> 고 지 사항: MSAL.NET 라이브러리는 확장 프로그램을 확장 한 것입니다. 이러한 라이브러리의 클래스는 나중에 또는 주요 변경 내용으로 MSAL.NET 될 수 있습니다.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>이중 토큰 캐시 serialization (MSAL 통합 캐시 + ADAL v3)

통합 캐시 형식을 사용 하 여 토큰 캐시 serialization을 구현 하는 것이 좋습니다. 이 형식은 동일한 플랫폼에서 ADAL.NET 및 MSAL.NET 2.x에 공통적으로 사용 되며, 동일한 세대 또는 이전 버전의 다른 MSALs와 함께 사용 됩니다. 다음 코드로 아이디어를 가져옵니다.

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
> [데스크톱 앱에서 web API 호출](scenario-desktop-call-api.md)
