---
title: 고급 인증 및 권한 부여 사용 - Azure App Service | Microsoft Docs
description: App Service의 인증 및 권한 부여를 사용자 지정하고, 사용자 클레임 및 서로 다른 토큰을 가져오는 방법을 보여 줍니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 97764db40807214e756f119ca95fd640164f0cf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851426"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Azure App Service의 고급 인증 및 권한 부여 사용

이 문서에서는 기본 제공 [App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 사용자 지정하고 애플리케이션에서 ID를 관리하는 방법을 보여줍니다. 

지금 바로 시작하려면 다음 자습서 중 하나를 참조하세요.

* [자습서: Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여(Windows)](app-service-web-tutorial-auth-aad.md)
* [자습서: Azure App Service에서 Linux용 엔드투엔드 사용자 인증 및 권한 부여](containers/tutorial-auth-aad.md)
* [Azure Active Directory 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-aad.md)
* [Facebook 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-facebook.md)
* [Google 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-google.md)
* [Microsoft 계정 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-microsoft.md)
* [Twitter 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>다중 로그인 공급자 사용

포털 구성은 사용자에게 다중 로그인 공급자를 표시하는 턴키 방법을 제공하지 않습니다(예: Facebook 및 Twitter). 그러나 앱에 기능을 추가하기는 어렵지 않습니다. 단계는 다음과 같이 간략히 설명됩니다.

먼저 Azure Portal의 **인증/권한 부여** 페이지에서 사용하도록 설정하려는 각 ID 공급자를 구성합니다.

**요청이 인증되지 않은 경우 수행할 작업**에서 **익명 요청 허용(작업 없음)** 을 선택합니다.

로그인 페이지, 탐색 모음 또는 앱의 다른 위치에서 사용하도록 설정한 각 공급자에 로그인 링크를 추가합니다(`/.auth/login/<provider>`). 예를 들면 다음과 같습니다.

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

사용자가 링크 중 하나를 클릭하면 사용자가 로그인할 수 있는 각 로그인 페이지가 열립니다.

사용자 사후 로그인을 사용자 지정 URL로 리디렉션하려면 `post_login_redirect_url` 쿼리 문자열 매개 변수를 사용합니다(ID 공급자 구성의 리디렉션 URI와 혼동하지 않음). 예를 들어 로그인한 후에 사용자를 `/Home/Index`로 이동하려면 다음 HTML 코드를 사용합니다.

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>공급자에서 토큰 유효성 검사

클라이언트 리디렉션 로그인에 애플리케이션은 사용자가 수동으로 공급자에 로그인한 다음, 유효성 검사를 위해 인증 토큰을 App Service에 제출합니다([인증 흐름](overview-authentication-authorization.md#authentication-flow) 참조). 이 유효성 검사 자체는 실제로 원하는 앱 리소스에 대한 액세스 권한을 부여하지 않지만, 유효성 검사가 성공하면 앱 리소스에 액세스하는 데 사용할 수 있는 세션 토큰이 제공됩니다. 

공급자 토큰의 유효성을 검사하려면 먼저 원하는 공급자를 사용하여 App Service 앱을 구성해야 합니다. 런타임 시 공급자에서 인증 토큰을 검색한 후 유효성 검사를 위해 토큰을 `/.auth/login/<provider>`에 게시합니다. 예를 들면 다음과 같습니다. 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

토큰 형식은 공급자에 따라 약간 다릅니다. 자세한 내용은 다음 표를 참조하세요.

| 공급자 값 | 요청 본문에 필요 | 설명 |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in` 속성은 선택 사항입니다. <br/>Live 서비스에서 토큰을 요청하는 경우 항상 `wl.basic` 범위를 요청합니다. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` 속성은 선택 사항입니다. 지정된 경우 필요에 따라 `redirect_uri` 속성을 포함할 수도 있습니다. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Facebook에서 유효한 [사용자 액세스 토큰](https://developers.facebook.com/docs/facebook-login/access-tokens)을 사용합니다. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

공급자 토큰의 유효성 검사가 성공적으로 수행되면 API는 세션 토큰인 응답 본문에 `authenticationToken`을 반환합니다. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

이 세션 토큰이 있으면 `X-ZUMO-AUTH` 헤더를 HTTP 요청에 추가하여 보호된 앱 리소스에 액세스할 수 있습니다. 예를 들면 다음과 같습니다. 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>세션에서 로그아웃

사용자는 앱의 `/.auth/logout` 엔드포인트에 `GET` 요청을 전송하여 로그아웃을 시작할 수 있습니다. `GET` 요청은 다음을 수행합니다.

- 현재 세션에서 인증 쿠키를 지웁니다.
- 토큰 저장소에서 현재 사용자의 토큰을 삭제합니다.
- Azure Active Directory 및 Google의 경우 ID 공급자에서 서버 쪽 로그아웃을 수행합니다.

웹 페이지의 단순 로그아웃 링크는 다음과 같습니다.

```HTML
<a href="/.auth/logout">Sign out</a>
```

기본적으로 성공적인 로그아웃은 클라이언트를 `/.auth/logout/done` URL로 리디렉션합니다. `post_logout_redirect_uri` 쿼리 매개 변수를 추가하여 로그아웃 후 리디렉션 페이지를 변경할 수 있습니다. 예를 들면 다음과 같습니다.

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

`post_logout_redirect_uri`의 값을 [인코딩](https://wikipedia.org/wiki/Percent-encoding)하는 것이 좋습니다.

정규화된 URL을 사용하는 경우 URL은 동일한 도메인에 호스팅되거나 앱에 대한 허용되는 외부 리디렉션 URL로 구성되어야 합니다. 다음 예제에서 동일한 도메인에서 호스팅되지 않는 `https://myexternalurl.com`으로 리디렉션하려면:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

[Azure Cloud Shell](../cloud-shell/quickstart.md)에서 다음 명령을 실행해야 합니다.

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL 조각 유지

사용자가 앱에 로그인한 후 일반적으로 동일한 페이지의 같은 섹션으로 리디렉션되기를 원합니다(예 `/wiki/Main_Page#SectionZ`). 그러나 [URL 조각](https://wikipedia.org/wiki/Fragment_identifier)(예: `#SectionZ`)은 서버에 전송되지 않으므로 OAuth 로그인이 완료되고 앱으로 다시 리디렉션된 후 기본적으로 유지되지 않습니다. 그런 다음, 사용자는 원하는 앵커로 다시 이동해야 할 때 최적이 아닌 환경을 가져옵니다. 이 제한은 모든 서버 쪽 인증 솔루션에 적용됩니다.

App Service 인증에서 OAuth 로그인에 대해 URL 조각을 유지할 수 있습니다. 이렇게 하려면 `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT`라는 앱 설정을 `true`로 설정해야 합니다. [Azure Portal](https://portal.azure.com)에서 수행하거나 [Azure Cloud Shell](../cloud-shell/quickstart.md)에서 단순히 다음 명령을 실행할 수 있습니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>사용자 클레임 액세스

App Service는 특수 헤더를 사용하여 사용자 클레임을 애플리케이션에 전달합니다. 외부 요청은 이러한 헤더를 설정하도록 허용되지 않았으므로 App Service에서 설정한 경우에만 표시됩니다. 다음은 이러한 헤더의 예입니다.

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

모든 언어로 작성된 코드 또는 프레임워크는 이러한 헤더에서 필요한 정보를 가져올 수 있습니다. ASP.NET 4.6 앱의 경우 **ClaimsPrincipal** 이 적절한 값으로 자동 설정됩니다.

또한 애플리케이션에서 `/.auth/me`를 호출하여 인증된 사용자에 대한 추가 세부 사항을 가져올 수 있습니다. Mobile Apps 서버 SDK는 이 데이터를 사용하기 위한 도우미 메서드를 제공합니다. 자세한 내용은 [Azure Mobile Apps Node.js SDK를 사용하는 방법](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) 및 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)을 참조하세요.

## <a name="retrieve-tokens-in-app-code"></a>앱 코드에서 토큰 검색

서버 코드에서 공급자별 토큰은 쉽게 액세스할 수 있도록 요청 헤더에 주입됩니다. 다음 표에 가능한 토큰 헤더 이름이 나와 있습니다.

| 공급자 | 헤더 이름 |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook 토큰 | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft 계정 | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

클라이언트 코드(예: 모바일 앱 또는 브라우저 내 JavaScript)에서 HTTP `GET` 요청을 `/.auth/me`에 전송합니다. 반환된 JSON에는 공급자별 토큰이 있습니다.

> [!NOTE]
> 액세스 토큰은 공급자 리소스에 액세스하기 위한 것이므로 클라이언트 암호를 사용하여 공급자를 구성하는 경우에만 표시됩니다. 새로 고침 토큰을 가져오는 방법을 알아보려면 액세스 토큰 새로 고침을 참조하세요.

## <a name="refresh-identity-provider-tokens"></a>ID 공급자 토큰 새로 고침

[세션 토큰](#extend-session-token-expiration-grace-period)이 아닌 공급자의 액세스 토큰이 만료되면 사용자를 다시 인증해야 해당 토큰을 다시 사용할 수 있습니다. 애플리케이션의 `/.auth/refresh` 엔드포인트에 대한 `GET` 호출을 수행하면 토큰 만료를 방지할 수 있습니다. 호출되면 App Service에서는 인증된 사용자에 대한 토큰 저장소의 액세스 토큰을 자동으로 새로 고칩니다. 이후에 앱 코드에서 토큰을 요청하면 새로 고쳐진 토큰을 가져옵니다. 단, 토큰 새로 고침을 실행하기 위해서는 토큰 저장소에 사용자 공급자에 대한 [토큰 새로 고침](https://auth0.com/learn/refresh-tokens/)이 포함되어야 합니다. 새로 고침 토큰을 얻는 방법은 각 공급자에서 제공하는 문서에 나와 있으며, 다음 목록은 간단한 요약입니다.

- **Google**: `access_type=offline` 쿼리 문자열 매개 변수를 `/.auth/login/google` API 호출에 추가합니다. Mobile Apps SDK를 사용하는 경우 `LogicAsync` 오버로드 중 하나에 매개 변수를 추가할 수 있습니다([Google 새로 고침 토큰](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) 참조).
- **Facebook**: 새로 고침 토큰을 제공하지 않습니다. 수명이 긴 토큰은 60일 후에 만료됩니다([액세스 토큰의 Facebook 만료 및 확장](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension) 참조).
- **Twitter**: 액세스 토큰이 만료되지 않습니다([Twitter OAuth FAQ](https://developer.twitter.com/en/docs/basics/authentication/FAQ) 참조).
- **Microsoft 계정**: [Microsoft 계정 인증 설정을 구성](configure-authentication-provider-microsoft.md)할 때 `wl.offline_access` 범위를 선택합니다.
- **Azure Active Directory**: [https://resources.azure.com](https://resources.azure.com)에서 다음 단계를 수행합니다.
    1. 페이지의 위쪽에서 **읽기/쓰기**를 선택합니다.
    2. 왼쪽 브라우저에서 **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **사이트** > _**\<app\_name>**_ > **config** > **authsettings**로 이동합니다. 
    3. **편집**을 클릭합니다.
    4. 다음 속성을 수정합니다. _\<app\_id&gt;_ 를 액세스하려는 서비스의 Azure Active Directory 애플리케이션 ID로 바꿉니다.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. **배치**를 클릭합니다. 

공급자가 구성되면 토큰 저장소에서 [새로 고침 토큰 및 액세스 토큰에 대한 만료 시간 찾을](#retrieve-tokens-in-app-code) 수 있습니다. 

언제든지 액세스 토큰을 새로 고치려면 아무 언어에서나 `/.auth/refresh`를 호출하면 됩니다. 다음 코드 조각은 jQuery를 사용하여 JavaScript 클라이언트에서 액세스 토큰을 새로 고칩니다.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

사용자가 사용자 앱에 부여된 사용 권한을 취소하는 경우 `/.auth/me`에 대한 호출이 `403 Forbidden` 응답과 함께 실패할 수도 있습니다. 오류를 진단하려면 세부 정보에 대한 애플리케이션 로그를 확인합니다.

## <a name="extend-session-token-expiration-grace-period"></a>세션 토큰 만료 유예 기간 연장

인증된 세션은 8시간 후에 만료됩니다. 인증된 세션이 만료된 후에는 기본적으로 72시간의 유예 기간이 있습니다. 이 유예 기간 내에는 사용자를 다시 인증하지 않고 App Service를 통해 세션 토큰을 새로 고칠 수 있습니다. 세션 쿠키가 무효화되는 경우에는 `/.auth/refresh`만 호출하면 토큰 만료를 직접 추적할 필요가 없습니다. 72시간의 유예 기간이 지나면 사용자는 다시 로그인하여 유효한 세션 토큰을 가져와야 합니다.

72시간이 충분치 않은 경우 이 만료 기간을 확장할 수 있습니다. 오랜 기간 동안 만료를 연장하면 중요한 보안 결과가 발생할 수 있습니다(예: 인증 토큰이 유출되거나 도난당하는 경우). 따라서 기본 72시간으로 유지하거나 확장 기간을 가장 작은 값으로 설정해야 합니다.

기본 만료 기간을 확장하려면 [Cloud Shell](../cloud-shell/overview.md)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 유예 기간은 ID 공급자의 토큰이 아닌 App Service 인증된 세션에만 적용됩니다. 만료된 공급자 토큰에 대한 유예 기간은 없습니다. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>로그인 계정의 도메인 제한

Microsoft 계정과 Azure Active Directory는 모두 여러 도메인에서 로그인할 수 있습니다. 예를 들어 Microsoft 계정은 _outlook.com_, _live.com_ 및 _hotmail.com_ 계정을 허용합니다. Azure Active Directory는 로그인 계정에 대해 여러 사용자 지정 도메인을 허용합니다. 이 동작은 _outlook.com_ 계정을 사용하는 사람이 액세스하는 것을 원치 않는 내부 앱의 경우 바람직하지 않을 수도 있습니다. 로그인 계정의 도메인 이름을 제한하려면 다음 단계를 수행합니다.

[https://resources.azure.com](https://resources.azure.com)에서 **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **사이트** > _**\<app\_name>**_ > **config** > **authsettings**로 이동합니다. 

**편집**을 클릭하고 다음 속성을 수정한 다음, **배치**를 클릭합니다. _\<domain\_name>_ 을 원하는 도메인으로 바꿨는지 확인합니다.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 엔드투엔드 사용자 인증 및 권한 부여(Windows)](app-service-web-tutorial-auth-aad.md)
> [자습서: 엔드투엔드 사용자 인증 및 권한 부여(Linux)](containers/tutorial-auth-aad.md)
