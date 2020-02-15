---
title: 웹 API를 호출 하는 토큰 얻기 (모바일 앱) | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법을 알아봅니다. 앱에 대 한 토큰을 가져옵니다.
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 9427235f47a31da75426559a4285634ab2837577
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132457"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>웹 Api를 호출 하는 모바일 앱에 대 한 토큰 가져오기

앱이 보호 된 웹 Api를 호출 하려면 액세스 토큰이 필요 합니다. 이 문서에서는 MSAL (Microsoft 인증 라이브러리)을 사용 하 여 토큰을 가져오는 프로세스를 안내 합니다.

## <a name="define-a-scope"></a>범위 정의

토큰을 요청 하는 경우 범위를 정의 해야 합니다. 범위는 앱이 액세스할 수 있는 데이터를 결정 합니다.  

범위를 정의 하는 가장 쉬운 방법은 원하는 web API의 `App ID URI` `.default`범위와 결합 하는 것입니다. 이 정의는 앱에서 포털에 설정 된 모든 범위가 필요한 Microsoft id 플랫폼을 나타냅니다.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>토큰 가져오기

### <a name="acquire-tokens-via-msal"></a>MSAL을 통해 토큰 획득

MSAL을 사용 하면 앱에서 토큰을 자동으로 및 대화형으로 가져올 수 있습니다. `AcquireTokenSilent()` 또는 `AcquireTokenInteractive()`를 호출할 때 MSAL은 요청 된 범위에 대 한 액세스 토큰을 반환 합니다. 올바른 패턴은 자동 요청을 만든 다음 대화형 요청으로 대체 하는 것입니다.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

먼저 토큰을 자동으로 획득 해 봅니다.

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
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
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

MSAL이 `MSALErrorInteractionRequired`을 반환 하는 경우 토큰을 대화형으로 시도 합니다.

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

IOS 및 macOS 용 MSAL은 토큰을 대화형으로 또는 자동으로 가져오기 위해 다양 한 한정자를 지원 합니다.
* [토큰을 가져오기 위한 일반 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [대화형 토큰을 가져오기 위한 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [자동 토큰을 가져오기 위한 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

다음 예제에서는 토큰을 대화형으로 가져오는 최소한의 코드를 보여 줍니다. 이 예제에서는 Microsoft Graph를 사용 하 여 사용자의 프로필을 읽습니다.

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

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET의 필수 매개 변수

`AcquireTokenInteractive`에는 필수 매개 변수 `scopes`하나만 있습니다. `scopes` 매개 변수는 토큰을 필요로 하는 범위를 정의 하는 문자열을 열거 합니다. Microsoft Graph에 대 한 토큰의 경우 각 Microsoft Graph API에 대 한 API 참조에서 필요한 범위를 찾을 수 있습니다. 참조에서 "사용 권한" 섹션으로 이동 합니다. 

예를 들어 [사용자의 연락처를 나열](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)하려면 "User. read", "Contact. read" 범위를 사용 합니다. 자세한 내용은 [Microsoft Graph 권한 참조](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)를 참조 하세요.

Android에서 `PublicClientApplicationBuilder`를 사용 하 여 앱을 만들 때 부모 활동을 지정할 수 있습니다. 이때 부모 활동을 지정 하지 않으면 나중에 다음 섹션과 같이 `.WithParentActivityOrWindow`를 사용 하 여 지정할 수 있습니다. 부모 작업을 지정 하는 경우 토큰은 상호 작용 후 해당 부모 작업으로 돌아갑니다. 지정 하지 않으면 `.ExecuteAsync()` 호출에서 예외가 throw 됩니다.

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET의 특정 선택적 매개 변수

다음 섹션에서는 MSAL.NET의 선택적 매개 변수를 설명 합니다. 

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 매개 변수는 프롬프트를 지정 하 여 사용자와의 상호 작용을 제어 합니다.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

클래스는 다음 상수를 정의 합니다.

- `SelectAccount`은 STS (보안 토큰 서비스)가 계정 선택 대화 상자를 표시 하도록 합니다. 이 대화 상자에는 사용자에 게 세션이 있는 계정이 포함 되어 있습니다. 사용자가 다른 id 중에서 선택할 수 있도록 하려는 경우이 옵션을 사용할 수 있습니다. 이 옵션은 MSAL을 구동 하 여 id 공급자에 `prompt=select_account`을 보냅니다. 
    
    `SelectAccount` 상수는 기본값 이며, 사용 가능한 정보를 바탕으로 가능한 최상의 환경을 제공 합니다. 사용 가능한 정보에는 계정, 사용자에 대 한 세션의 현재 상태 등이 포함 될 수 있습니다. 이 작업을 수행 해야 하는 이유가 없으면이 기본값을 변경 하지 마세요.
- `Consent`를 사용 하면 이전에 동의가 부여 된 경우에도 사용자에 게 동의 여부를 묻는 메시지를 표시할 수 있습니다. 이 경우 MSAL은 id 공급자에 `prompt=consent`를 보냅니다. 

    조직의 거 버 넌 스에서 사용자가 응용 프로그램을 사용할 때마다 승인 대화 상자를 표시 해야 하는 보안 중심 응용 프로그램에서 `Consent` 상수를 사용 하는 것이 좋습니다.
- `ForceLogin`은 프롬프트가 필요 하지 않은 경우에도 서비스에서 사용자에 게 자격 증명을 묻는 메시지를 표시 하도록 합니다. 

    이 옵션은 토큰 획득에 실패 하 고 사용자가 다시 로그인 할 수 있도록 하려는 경우에 유용할 수 있습니다. 이 경우 MSAL은 id 공급자에 `prompt=login`를 보냅니다. 조직의 거 버 넌 스에서 사용자가 응용 프로그램의 특정 부분에 액세스할 때마다 로그인 해야 하는 보안 중심 응용 프로그램에서이 옵션을 사용 하는 것이 좋습니다.
- `Never`은 .NET 4.5 및 Windows 런타임 (WinRT)에만 해당 됩니다. 이 상수는 사용자에 게 메시지를 표시 하지 않지만 숨겨진 포함 된 웹 보기에 저장 된 쿠키를 사용 하려고 합니다. 자세한 내용은 [MSAL.NET에서 웹 브라우저 사용](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)을 참조 하세요.

    이 옵션을 사용할 수 없는 경우 `AcquireTokenInteractive`는 UI 상호 작용이 필요 하다는 알림을 표시 하는 예외를 throw 합니다. 그런 다음 다른 `Prompt` 매개 변수를 사용 해야 합니다.
- `NoPrompt`는 id 공급자에 게 프롬프트를 보내지 않습니다. 

    이 옵션은 Azure Active Directory B2C의 편집 프로필 정책에만 유용 합니다. 자세한 내용은 [B2C 구체적인](https://aka.ms/msal-net-b2c-specificities)항목을 참조 하세요.

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

사용자가 여러 리소스에 대 한 사전 동의를 제공 하려는 고급 시나리오에서 `WithExtraScopeToConsent` 한정자를 사용 합니다. MSAL.NET 또는 Microsoft id 플랫폼 2.0에서 일반적으로 사용 되는 증분 동의를 사용 하지 않으려는 경우이 한정자를 사용할 수 있습니다. 자세한 내용은 [사용자에 게 여러 리소스에 대 한 사전 승인이 필요](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)합니다 .를 참조 하세요.

다음은 코드 예제입니다. 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>기타 선택적 매개 변수

`AcquireTokenInteractive`에 대 한 다른 선택적 매개 변수에 대 한 자세한 내용은 [AcquireTokenInteractiveParameterBuilder에 대 한 참조 설명서](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)를 참조 하세요.

### <a name="acquire-tokens-via-the-protocol"></a>프로토콜을 통해 토큰 획득

토큰을 가져오는 프로토콜을 직접 사용 하지 않는 것이 좋습니다. 이렇게 하면 앱이 SSO (Single Sign-On), 장치 관리 및 조건부 액세스를 포함 하는 일부 시나리오를 지원 하지 않습니다.

프로토콜을 사용 하 여 모바일 앱에 대 한 토큰을 가져오는 경우 다음 두 요청을 수행 합니다. 

* 인증 코드를 가져옵니다.
* 토큰에 대 한 코드를 교환 합니다.

#### <a name="get-an-authorization-code"></a>권한 부여 코드 가져오기

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>액세스 권한 가져오기 및 토큰 새로 고침

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Web API 호출](scenario-mobile-call-api.md)
