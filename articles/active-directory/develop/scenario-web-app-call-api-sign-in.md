---
title: 로그 아웃 시 토큰 캐시에서 계정 제거-Microsoft identity platform | Microsoft
description: 로그 아웃 시 토큰 캐시에서 계정을 제거 하는 방법에 대해 알아봅니다.
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758873"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>웹 Api를 호출 하는 웹 앱: 전역 로그 아웃 시 토큰 캐시에서 계정 제거

[로그인 및 로그 아웃 사용자를 로그인 하는 웹 앱](scenario-web-app-sign-user-sign-in.md)의 웹 앱에 로그인을 추가 하는 방법을 배웠습니다.

웹 api를 호출 하는 웹 앱의 경우에는 로그 아웃이 다릅니다. 사용자가 응용 프로그램에서 로그 아웃 하거나 응용 프로그램에서 로그 아웃 하면 토큰 캐시에서 해당 사용자와 연결 된 토큰을 제거 해야 합니다.

## <a name="intercept-the-callback-after-single-sign-out"></a>단일 로그 아웃 후 콜백을 가로챕니다.

로그 아웃 한 계정에 연결 된 토큰 캐시 엔트리를 지우기 위해 응용 프로그램은 after `logout` 이벤트를 가로챌 수 있습니다. Web apps는 토큰 캐시에 있는 각 사용자에 대 한 액세스 토큰을 저장 합니다. 웹 응용 프로그램은 `logout` 후 콜백을 가로채 면 캐시에서 사용자를 제거할 수 있습니다.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core의 경우 [WebAppServiceCollectionExtensions # L151](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)의 `AddMsal()` 메서드에 가로채기 메커니즘이 설명 되어 있습니다.

이전에 응용 프로그램에 등록 한 로그 아웃 URL을 사용 하 여 single sign-on을 구현할 수 있습니다. Microsoft id 플랫폼 `logout` 엔드포인트은 로그 아웃 URL을 호출 합니다. 이 호출은 웹 앱 또는 다른 웹 앱 또는 브라우저에서 로그 아웃을 시작한 경우에 발생 합니다. 자세한 내용은 [Single sign-on](v2-protocols-oidc.md#single-sign-out)을 참조 하세요.

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

`RemoveAccountAsync`에 대 한 코드는 [L288 # L264](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)에서 사용할 수 있습니다.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

Java 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 샘플은 전역 로그 아웃 시 캐시에서 계정을 제거 하지 않습니다.

---

## <a name="next-steps"></a>다음 단계

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [웹 앱에 대 한 토큰 획득](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
