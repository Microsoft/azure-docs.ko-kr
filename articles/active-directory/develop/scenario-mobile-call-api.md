---
title: 모바일 앱에서 web API 호출 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법을 알아봅니다. 웹 API를 호출 합니다.
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
ms.openlocfilehash: bd848fa6f74f049f97956ef1736ac2b08f3a6148
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160154"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>모바일 앱에서 web API 호출

앱이 사용자에 게 로그인 하 고 토큰을 받은 후 MSAL (Microsoft 인증 라이브러리)은 사용자, 사용자 환경 및 발급 된 토큰에 대 한 정보를 제공 합니다. 앱은 이러한 값을 사용 하 여 web API를 호출 하거나 사용자에 게 환영 메시지를 표시할 수 있습니다.

이 문서에서는 먼저 MSAL 결과를 살펴보겠습니다. 그런 다음 `AuthenticationResult` 또는 `result`에서 액세스 토큰을 사용 하 여 보호 된 웹 API를 호출 하는 방법을 살펴보겠습니다.

## <a name="msal-result"></a>MSAL 결과
MSAL은 다음 값을 제공 합니다. 

- `AccessToken`는 HTTP 전달자 요청에서 보호 된 웹 Api를 호출 합니다.
- `IdToken`에는 로그인 한 사용자에 대 한 유용한 정보가 포함 되어 있습니다. 이 정보에는 사용자 이름, 홈 테 넌 트 및 저장소에 대 한 고유 식별자가 포함 됩니다.
- `ExpiresOn`는 토큰의 만료 시간입니다. MSAL은 앱의 자동 새로 고침을 처리 합니다.
- `TenantId`은 사용자가 로그인 한 테 넌 트의 식별자입니다. Azure Active Directory (Azure AD) B2B에서 게스트 사용자의 경우이 값은 사용자가 로그인 한 테 넌 트를 식별 합니다. 값은 사용자의 홈 테 넌 트를 식별 하지 않습니다.  
- `Scopes`는 토큰과 함께 부여 된 범위를 나타냅니다. 부여 된 범위는 요청한 범위의 하위 집합일 수 있습니다.

또한 MSAL은 `Account` 값에 대 한 추상화를 제공 합니다. `Account` 값은 현재 사용자의 로그인 된 계정을 나타냅니다.

- `HomeAccountIdentifier`은 사용자의 홈 테 넌 트를 식별 합니다.
- `UserName`은 사용자의 기본 설정 된 사용자 이름입니다. Azure AD B2C 사용자의 경우이 값은 비어 있을 수 있습니다.
- `AccountIdentifier`는 로그인 한 사용자를 식별 합니다. 대부분의 경우이 값은 사용자가 다른 테 넌 트의 게스트가 아닌 `HomeAccountIdentifier` 값과 동일 합니다.

## <a name="call-an-api"></a>API 호출

액세스 토큰을 만든 후에는 web API를 호출할 수 있습니다. 앱은 토큰을 사용 하 여 HTTP 요청을 작성 한 다음 요청을 실행 합니다.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL

토큰을 획득 하는 메서드는 `MSALResult` 개체를 반환 합니다. `MSALResult` `accessToken` 속성을 노출 합니다. `accessToken`를 사용 하 여 web API를 호출할 수 있습니다. 를 호출 하기 전에이 속성을 HTTP 권한 부여 헤더에 추가 하 여 보호 된 web API에 액세스 합니다.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>여러 API 요청 만들기

동일한 API를 여러 번 호출 해야 하거나 여러 Api를 호출 해야 하는 경우에는 앱을 빌드할 때 다음과 같은 주제를 고려해 야 합니다.

- **증분 동의**: Microsoft id 플랫폼을 사용 하면 앱이 시작 시가 아니라 권한이 필요할 때 사용자 동의를 받을 수 있습니다. 앱이 API를 호출할 준비가 될 때마다 필요한 범위만 요청 해야 합니다.

- **조건부 액세스**: 여러 API 요청을 수행할 때 특정 시나리오에서는 추가 조건부 액세스 요구 사항을 충족 해야 할 수 있습니다. 첫 번째 요청에 조건부 액세스 정책이 없고 앱에서 조건부 액세스가 필요한 새 API에 자동으로 액세스 하려고 하는 경우 이러한 방식으로 요구 사항이 증가할 수 있습니다. 이 문제를 처리 하려면 자동 요청에서 오류를 파악 하 고 대화형 요청을 준비 해야 합니다.  자세한 내용은 [조건부 액세스에 대 한 지침](../azuread-dev/conditional-access-dev-guide.md)을 참조 하세요.

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>증분 동의 및 조건부 액세스를 사용 하 여 여러 Api 호출

동일한 사용자에 대해 여러 Api를 호출 해야 하는 경우 사용자에 대 한 토큰을 가져온 후에는 다음을 `AcquireTokenSilent` 호출 하 여 토큰을 가져오기 위해 사용자에 게 자격 증명을 묻는 메시지를 반복적으로 표시 하지 않을 수 있습니다.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

다음과 같은 경우 상호 작용이 필요 합니다.

- 사용자가 첫 번째 API에 대해 동의한 이제 더 많은 범위에 동의 해야 합니다. 이 경우에는 증분 동의를 사용 합니다.
- 첫 번째 API에는 multi-factor authentication이 필요 하지 않지만 다음 API는이를 수행 합니다.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션으로 이동](scenario-mobile-production.md)
