---
title: 웹 API를 호출 하는 토큰 얻기 (디먼 앱)-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 디먼 앱을 빌드하는 방법 알아보기 (토큰 획득)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b2d388160c6ca744b10c17bda17c59e22940f98b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775250"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>웹 Api를 호출 하는 디먼 앱-토큰 획득

기밀 클라이언트 응용 프로그램을 생성 한 후에는 `AcquireTokenForClient`를 호출 하 고, 범위를 전달 하 고, 선택적으로 토큰을 강제로 새로 고침 하 여 앱에 대 한 토큰을 가져올 수 있습니다.

## <a name="scopes-to-request"></a>요청할 범위

클라이언트 자격 증명 흐름에 대해 요청할 범위는 리소스 이름 뒤에 `/.default`입니다. 이 표기법은 응용 프로그램을 등록 하는 동안 정적으로 선언 된 *응용 프로그램 수준 권한을* 사용 하도록 Azure Active Directory (Azure AD)에 지시 합니다. 또한 이러한 API 권한은 테넌트 관리자가 부여 해야 합니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python에서 구성 파일은 다음 코드 조각과 같습니다.

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD (v1.0) 리소스

클라이언트 자격 증명에 사용 되는 범위는 항상 리소스 ID와 `/.default`입니다.

> [!IMPORTANT]
> MSAL에서 버전 1.0 액세스 토큰을 허용 하는 리소스에 대 한 액세스 토큰을 요청 하는 경우 Azure AD는 마지막 슬래시 앞에 있는 모든 항목을 사용 하 고 리소스 식별자로 사용 하 여 요청 된 범위에서 원하는 대상 그룹을 구문 분석 합니다.
> 따라서 Azure SQL Database (**https:\//database.windows.net**)와 마찬가지로 리소스에는 슬래시 (Azure SQL Database, `https://database.windows.net/`)로 끝나는 대상이 필요 하므로 `https://database.windows.net//.default`범위를 요청 해야 합니다. (이중 슬래시를 적어둡니다.) 참고 항목: MSAL.NET issue [#747: 리소스 url의 후행 슬래시가 생략 되었으며이로 인해 sql 인증 오류가 발생](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)했습니다.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

앱에 대 한 토큰을 얻으려면 플랫폼에 따라 `AcquireTokenForClient` 또는 이와 동등한 기능을 사용 합니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="javatabjava"></a>[Java](#tab/java)

이 코드는 [Msal Java dev 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)에서 추출 됩니다.

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* Call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>프로토콜

선택한 언어에 대 한 라이브러리가 아직 없는 경우 프로토콜을 직접 사용 하는 것이 좋습니다.

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용 하 여 토큰 요청에 액세스

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>두 번째 사례: 인증서를 사용 하 여 토큰 요청에 액세스

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

자세한 내용은 프로토콜 설명서 ( [Microsoft id 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md))를 참조 하세요.

## <a name="application-token-cache"></a>응용 프로그램 토큰 캐시

MSAL.NET에서 `AcquireTokenForClient`는 응용 프로그램 토큰 캐시를 사용 합니다. 다른 모든 AcquireToken*XX* 메서드는 사용자 토큰 캐시를 사용 합니다. `AcquireTokenSilent` *사용자* 토큰 캐시를 사용 하기 때문에 `AcquireTokenForClient`를 호출 하기 전에 `AcquireTokenSilent`를 호출 하지 마세요. `AcquireTokenForClient`는 *응용 프로그램* 토큰 캐시 자체를 확인 하 고 업데이트 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="did-you-use-the-resourcedefault-scope"></a>리소스/. 기본 범위를 사용 했나요?

잘못 된 범위를 사용 했음을 나타내는 오류 메시지가 표시 되 면 `resource/.default` 범위를 사용 하지 않았을 수 있습니다.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>관리자 동의를 제공 해야 하나요? 디먼 앱에 필요 합니다!

API를 호출할 때 **작업 오류를 완료할 수 있는 권한이** 없는 경우 테넌트 관리자는 응용 프로그램에 권한을 부여 해야 합니다. 위의 클라이언트 앱 등록 6 단계를 참조 하세요.
일반적으로 다음과 같은 오류가 표시 됩니다.

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [디먼 앱-web API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [디먼 앱-web API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [디먼 앱-web API 호출](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
