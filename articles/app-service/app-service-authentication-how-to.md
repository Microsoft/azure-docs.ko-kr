---
title: 인증/인증의 고급 사용
description: 다양 한 시나리오에 대 한 App Service의 인증 및 권한 부여 기능을 사용자 지정 하 고 사용자 클레임 및 다른 토큰을 가져오는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: 2fa2e3463e057062ba743c2f6989aa571c85c983
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962471"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Azure App Service의 고급 인증 및 권한 부여 사용

이 문서에서는 기본 제공 [App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 사용자 지정하고 애플리케이션에서 ID를 관리하는 방법을 보여줍니다. 

지금 바로 시작하려면 다음 자습서 중 하나를 참조하세요.

* [자습서: Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)
* [Azure Active Directory 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-aad.md)
* [Facebook 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-facebook.md)
* [Google 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-google.md)
* [Microsoft 계정 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-microsoft.md)
* [Twitter 로그인을 사용하도록 앱을 구성하는 방법](configure-authentication-provider-twitter.md)
* [Openid connect Connect 공급자를 사용 하 여 로그인 하도록 앱을 구성 하는 방법 (미리 보기)](configure-authentication-provider-openid-connect.md)

## <a name="use-multiple-sign-in-providers"></a>다중 로그인 공급자 사용

포털 구성은 사용자에게 다중 로그인 공급자를 표시하는 턴키 방법을 제공하지 않습니다(예: Facebook 및 Twitter). 그러나 앱에 기능을 추가하기는 어렵지 않습니다. 단계는 다음과 같이 간략히 설명됩니다.

먼저 Azure Portal의 **인증/권한 부여** 페이지에서 사용하도록 설정하려는 각 ID 공급자를 구성합니다.

**요청이 인증되지 않은 경우 수행할 작업**에서 **익명 요청 허용(작업 없음)** 을 선택합니다.

로그인 페이지, 탐색 모음 또는 앱의 다른 위치에서 사용하도록 설정한 각 공급자에 로그인 링크를 추가합니다(`/.auth/login/<provider>`). 예를 들어:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

사용자가 링크 중 하나를 클릭하면 사용자가 로그인할 수 있는 각 로그인 페이지가 열립니다.

사용자 사후 로그인을 사용자 지정 URL로 리디렉션하려면 `post_login_redirect_url` 쿼리 문자열 매개 변수를 사용합니다(ID 공급자 구성의 리디렉션 URI와 혼동하지 않음). 예를 들어 로그인한 후에 사용자를 `/Home/Index`로 이동하려면 다음 HTML 코드를 사용합니다.

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>공급자에서 토큰 유효성 검사

클라이언트 리디렉션 로그인에 애플리케이션은 사용자가 수동으로 공급자에 로그인한 다음, 유효성 검사를 위해 인증 토큰을 App Service에 제출합니다([인증 흐름](overview-authentication-authorization.md#authentication-flow) 참조). 이 유효성 검사 자체는 실제로 원하는 앱 리소스에 대한 액세스 권한을 부여하지 않지만, 유효성 검사가 성공하면 앱 리소스에 액세스하는 데 사용할 수 있는 세션 토큰이 제공됩니다. 

공급자 토큰의 유효성을 검사하려면 먼저 원하는 공급자를 사용하여 App Service 앱을 구성해야 합니다. 런타임 시 공급자에서 인증 토큰을 검색한 후 유효성 검사를 위해 토큰을 `/.auth/login/<provider>`에 게시합니다. 예를 들어: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

토큰 형식은 공급자에 따라 약간 다릅니다. 자세한 내용은 다음 표를 참조하세요.

| 공급자 값 | 요청 본문에 필요 | 주석 |
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

이 세션 토큰이 있으면 `X-ZUMO-AUTH` 헤더를 HTTP 요청에 추가하여 보호된 앱 리소스에 액세스할 수 있습니다. 예를 들어: 

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

```html
<a href="/.auth/logout">Sign out</a>
```

기본적으로 성공적인 로그아웃은 클라이언트를 `/.auth/logout/done` URL로 리디렉션합니다. `post_logout_redirect_uri` 쿼리 매개 변수를 추가하여 로그아웃 후 리디렉션 페이지를 변경할 수 있습니다. 예를 들어:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

`post_logout_redirect_uri`의 값을 [인코딩](https://wikipedia.org/wiki/Percent-encoding)하는 것이 좋습니다.

정규화된 URL을 사용하는 경우 URL은 동일한 도메인에 호스팅되거나 앱에 대한 허용되는 외부 리디렉션 URL로 구성되어야 합니다. 다음 예제에서 동일한 도메인에서 호스팅되지 않는 `https://myexternalurl.com`으로 리디렉션하려면:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

[Azure Cloud Shell](../cloud-shell/quickstart.md)에서 다음 명령을 실행 합니다.

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

모든 언어로 작성된 코드 또는 프레임워크는 이러한 헤더에서 필요한 정보를 가져올 수 있습니다. ASP.NET 4.6 앱의 경우 **ClaimsPrincipal** 이 적절한 값으로 자동 설정됩니다. 그러나 ASP.NET Core App Service 사용자 클레임과 통합 되는 인증 미들웨어는 제공 하지 않습니다. 해결 방법은 [MaximeRouiller. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)를 참조 하세요.

앱에 대해 [토큰 저장소](overview-authentication-authorization.md#token-store) 를 사용 하는 경우를 호출 하 여 인증 된 사용자에 대 한 추가 세부 정보를 가져올 수도 있습니다 `/.auth/me` . Mobile Apps 서버 SDK는 이 데이터를 사용하기 위한 도우미 메서드를 제공합니다. 자세한 내용은 [Azure Mobile Apps Node.js SDK를 사용하는 방법](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) 및 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)을 참조하세요.

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

클라이언트 코드 (예: 모바일 앱 또는 브라우저 내 JavaScript)에서에 HTTP 요청을 보냅니다 `GET` `/.auth/me` ([토큰 저장소](overview-authentication-authorization.md#token-store) 를 사용 하도록 설정 해야 함). 반환된 JSON에는 공급자별 토큰이 있습니다.

> [!NOTE]
> 액세스 토큰은 공급자 리소스에 액세스하기 위한 것이므로 클라이언트 암호를 사용하여 공급자를 구성하는 경우에만 표시됩니다. 새로 고침 토큰을 가져오는 방법을 알아보려면 액세스 토큰 새로 고침을 참조하세요.

## <a name="refresh-identity-provider-tokens"></a>ID 공급자 토큰 새로 고침

[세션 토큰](#extend-session-token-expiration-grace-period)이 아닌 공급자의 액세스 토큰이 만료되면 사용자를 다시 인증해야 해당 토큰을 다시 사용할 수 있습니다. 애플리케이션의 `/.auth/refresh` 엔드포인트에 대한 `GET` 호출을 수행하면 토큰 만료를 방지할 수 있습니다. 이 호출 되 면 App Service는 인증 된 사용자에 대 한 [토큰 저장소](overview-authentication-authorization.md#token-store) 의 액세스 토큰을 자동으로 새로 고칩니다. 이후에 앱 코드에서 토큰을 요청하면 새로 고쳐진 토큰을 가져옵니다. 단, 토큰 새로 고침을 실행하기 위해서는 토큰 저장소에 사용자 공급자에 대한 [토큰 새로 고침](https://auth0.com/learn/refresh-tokens/)이 포함되어야 합니다. 새로 고침 토큰을 얻는 방법은 각 공급자에서 제공하는 문서에 나와 있으며, 다음 목록은 간단한 요약입니다.

- **Google**: `access_type=offline` 쿼리 문자열 매개 변수를 `/.auth/login/google` API 호출에 추가합니다. Mobile Apps SDK를 사용하는 경우 `LogicAsync` 오버로드 중 하나에 매개 변수를 추가할 수 있습니다([Google 새로 고침 토큰](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) 참조).
- **Facebook**: 새로 고침 토큰을 제공하지 않습니다. 수명이 긴 토큰은 60일 후에 만료됩니다([액세스 토큰의 Facebook 만료 및 확장](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension) 참조).
- **Twitter**: 액세스 토큰이 만료되지 않습니다([Twitter OAuth FAQ](https://developer.twitter.com/en/docs/basics/authentication/FAQ) 참조).
- **Microsoft 계정**: [Microsoft 계정 인증 설정을 구성](configure-authentication-provider-microsoft.md)할 때 `wl.offline_access` 범위를 선택합니다.
- **Azure Active Directory**: [https://resources.azure.com](https://resources.azure.com)에서 다음 단계를 수행합니다.
    1. 페이지의 위쪽에서 **읽기/쓰기**를 선택합니다.
    2. 왼쪽 브라우저에서 **구독** > * *_ \<subscription\_name_** > **resourcegroups** > * *_* \<resource\_group\_name> _* > **공급자**  >  **Microsoft. 웹**  >  **사이트** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**로 이동 합니다. 
    3. **편집**을 클릭합니다.
    4. 다음 속성을 수정합니다. 을 _\<app\_id>_ 액세스 하려는 서비스의 Azure Active Directory 응용 프로그램 ID로 바꿉니다.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. **배치**를 클릭합니다. 

공급자가 구성되면 토큰 저장소에서 [새로 고침 토큰 및 액세스 토큰에 대한 만료 시간 찾을](#retrieve-tokens-in-app-code) 수 있습니다. 

언제 든 지 액세스 토큰을 새로 고치려면 모든 언어로를 호출 하면 `/.auth/refresh` 됩니다. 다음 코드 조각은 jQuery를 사용하여 JavaScript 클라이언트에서 액세스 토큰을 새로 고칩니다.

```javascript
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

Microsoft 계정과 Azure Active Directory는 모두 여러 도메인에서 로그인할 수 있습니다. 예를 들어 Microsoft 계정은 _outlook.com_, _live.com_ 및 _hotmail.com_ 계정을 허용합니다. Azure AD는 로그인 계정에 대해 원하는 수의 사용자 지정 도메인을 허용 합니다. 그러나 사용자의 브랜드 Azure AD 로그인 페이지 (예:)로 직접 사용자를 가속화 하는 것이 좋습니다 `contoso.com` . 로그인 계정의 도메인 이름을 제안 하려면 다음 단계를 수행 합니다.

에서 [https://resources.azure.com](https://resources.azure.com) **구독** > * *_ \<subscription\_name_** > **resourcegroups** > * *_* \<resource\_group\_name> _* > **공급자**인  >  **Microsoft 웹**  >  **사이트** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**로 이동 합니다. 

**편집**을 클릭하고 다음 속성을 수정한 다음, **배치**를 클릭합니다. 을 _\<domain\_name>_ 원하는 도메인으로 바꾸어야 합니다.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

이 설정은 `domain_hint` 쿼리 문자열 매개 변수를 로그인 리디렉션 URL에 추가 합니다. 

> [!IMPORTANT]
> 클라이언트는 `domain_hint` 리디렉션 URL을 받은 후 매개 변수를 제거 하 고 다른 도메인으로 로그인 할 수 있습니다. 따라서이 함수는 편리 하지만 보안 기능이 아닙니다.
>

## <a name="authorize-or-deny-users"></a>사용자 권한 부여 또는 거부

App Service는 가장 간단한 인증 사례 (예: 인증 되지 않은 요청 거부)를 처리 하지만 앱에는 특정 사용자 그룹에만 액세스를 제한 하는 것과 같은 보다 세분화 된 권한 부여 동작이 필요할 수 있습니다. 특정 한 경우에는 로그인 한 사용자에 대 한 액세스를 허용 하거나 거부 하는 사용자 지정 응용 프로그램 코드를 작성 해야 합니다. 다른 경우에는 App Service 또는 id 공급자가 코드를 변경 하지 않고도 도움을 받을 수 있습니다.

- [서버 수준](#server-level-windows-apps-only)
- [Id 공급자 수준](#identity-provider-level)
- [응용 프로그램 수준](#application-level)

### <a name="server-level-windows-apps-only"></a>서버 수준 (Windows 앱에만 해당)

모든 Windows 앱의 경우 *Web.config* 파일을 편집 하 여 IIS 웹 서버의 권한 부여 동작을 정의할 수 있습니다. Linux 앱은 IIS를 사용 하지 않으며 *Web.config*를 통해 구성할 수 없습니다.

1. `https://<app-name>.scm.azurewebsites.net/DebugConsole` 로 이동합니다.

1. App Service 파일의 브라우저 탐색기에서 *site/wwwroot*로 이동 합니다. *Web.config* 없는 경우 **+**  >  **새 파일**을 선택 하 여 만듭니다. 

1. *Web.config* 의 연필을 선택 하 여 편집 합니다. 다음 구성 코드를 추가 하 고 **저장**을 클릭 합니다. *Web.config* 이미 있는 경우 `<authorization>` 요소를 모든 항목에 추가 하면 됩니다. 요소에 허용 하려는 계정을 추가 `<allow>` 합니다.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Id 공급자 수준

Id 공급자는 특정 턴 키 인증을 제공할 수 있습니다. 예를 들어:

- [Azure App Service](configure-authentication-provider-aad.md)의 경우 Azure AD에서 직접 [엔터프라이즈 수준의 액세스를 관리할](../active-directory/manage-apps/what-is-access-management.md) 수 있습니다. 자세한 내용은 [응용 프로그램에 대 한 사용자 액세스를 제거 하는 방법](../active-directory/manage-apps/methods-for-removing-user-access.md)을 참조 하세요.
- [Google](configure-authentication-provider-google.md)의 경우 조직에 속한 google API 프로젝트는 조직의 사용자 에게만 액세스를 허용 하도록 구성할 [수 있습니다 (](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) [Google의 **OAuth 2.0 지원 설정** 페이지](https://support.google.com/cloud/answer/6158849?hl=en)참조).

### <a name="application-level"></a>애플리케이션 수준

다른 수준 중 하나에서 필요한 권한 부여를 제공 하지 않거나 플랫폼 또는 id 공급자가 지원 되지 않는 경우 사용자 [클레임](#access-user-claims)에 따라 사용자에 게 권한을 부여 하는 사용자 지정 코드를 작성 해야 합니다.

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>파일 (미리 보기)을 사용 하 여 구성

필요에 따라 배포에서 제공 하는 파일을 통해 인증 설정을 구성할 수 있습니다. App Service 인증/권한 부여의 특정 미리 보기 기능에 필요할 수 있습니다.

> [!IMPORTANT]
> 응용 프로그램 페이로드와이 파일은 [슬롯과](./deploy-staging-slots.md)같이 환경 간에 이동할 수 있습니다. 각 슬롯에 다른 앱 등록을 고정 하는 것이 좋습니다. 이러한 경우에는 구성 파일을 사용 하는 대신 표준 구성 방법을 계속 사용 해야 합니다.

### <a name="enabling-file-based-configuration"></a>파일 기반 구성 사용

> [!CAUTION]
> 미리 보기 중에 파일 기반 구성을 사용 하도록 설정 하면 Azure Portal, Azure CLI, Azure PowerShell 등의 일부 클라이언트를 통해 응용 프로그램에 대 한 App Service 인증/권한 부여 기능을 관리할 수 없습니다.

1. 프로젝트의 루트에서 구성에 대 한 새 JSON 파일을 만듭니다 (웹/함수 앱의 D:\home\site\wwwroot에 배포 됨). [파일 기반 구성 참조](#configuration-file-reference)에 따라 원하는 구성을 입력 합니다. 기존 Azure Resource Manager 구성을 수정 하는 경우 컬렉션에서 캡처된 속성을 구성 파일로 변환 해야 `authsettings` 합니다.

2. 에서 [Azure Resource Manager](../azure-resource-manager/management/overview.md) api에 캡처되는 기존 구성을 수정 합니다 `Microsoft.Web/sites/<siteName>/config/authsettings` . 이를 수정 하기 위해 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md) 또는 [Azure Resource Explorer](https://resources.azure.com/)같은 도구를 사용할 수 있습니다. Authsettings 컬렉션 내에서 세 개의 속성을 설정 해야 하며, 다른 속성은 제거할 수 있습니다.

    1.  `enabled`"True"로 설정 합니다.
    2.  `isAuthFromFile`"True"로 설정 합니다.
    3.  `authFilePath`파일 이름으로 설정 합니다 (예: "auth.json").

> [!NOTE]
> 의 형식은 `authFilePath` 플랫폼 마다 다릅니다. Windows에서 상대 경로와 절대 경로를 모두 지원 합니다. 상대를 권장 합니다. Linux의 경우 현재 절대 경로만 지원 되므로 설정 값은 "/home/site/wwwroot/auth.json" 또는 유사 해야 합니다.

이 구성을 업데이트 한 후에는 해당 사이트에 대 한 인증/권한 부여 App Service의 동작을 정의 하는 데 파일의 내용이 사용 됩니다. Azure Resource Manager 구성으로 돌아가려면 `isAuthFromFile` 다시 "false"로 설정 하면 됩니다.

### <a name="configuration-file-reference"></a>구성 파일 참조

구성 파일에서 참조 되는 모든 암호는 [응용 프로그램 설정](./configure-common.md#configure-app-settings)으로 저장 해야 합니다. 원하는 것으로 설정의 이름을 바꿀 수 있습니다. 구성 파일의 참조가 동일한 키를 사용 하는지 확인 합니다.

파일 내에서 가능한 구성 옵션은 다음과 같습니다.

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "requireAuthentication": <true|false>,
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "openIdConnectProviders": {
            "provider name": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "secretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scope": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        },
        "login": {
            "routes": {
                "logoutEndpoint": "<logout endpoint>"
            },
            "tokenStore": {
                "enabled": <true|false>,
                "tokenRefreshExtensionHours": "<double>",
                "fileSystem": {
                    "directory": "<directory to store the tokens in if using a file system token store (default)>"
                },
                "azureBlobStorage": {
                    "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
                }
            },
            "preserveUrlFragmentsForLogins": <true|false>,
            "allowedExternalRedirectUrls": [
                "https://uri1.azurewebsites.net/",
                "https://uri2.azurewebsites.net/"
            ],
            "cookieExpiration": {
                "convention": "FixedTime|IdentityProviderDerived",
                "timeToExpiration": "<timespan>"
            },
            "nonce": {
                "validateNonce": <true|false>,
                "nonceExpirationInterval": "<timespan>"
            }
        },
        "httpSettings": {
            "requireHttps": <true|false>,
            "routes": {
                "apiPrefix": "<api prefix>"
            },
            "forwardProxy": {
                "convention": "NoProxy|Standard|Custom",
                "customHostHeaderName": "<host header value>",
                "customProtoHeaderName": "<proto header value>"
            }
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>앱을 특정 인증 런타임 버전에 고정

인증/권한 부여를 사용 하도록 설정 하면 [기능 개요](overview-authentication-authorization.md#how-it-works)에 설명 된 대로 플랫폼 미들웨어가 HTTP 요청 파이프라인에 삽입 됩니다. 이 플랫폼 미들웨어는 정기적인 플랫폼 업데이트의 일부로 새로운 기능 및 향상 된 기능으로 정기적으로 업데이트 됩니다. 기본적으로 웹 또는 함수 앱은이 플랫폼 미들웨어의 최신 버전에서 실행 됩니다. 이러한 자동 업데이트는 항상 이전 버전과 호환 됩니다. 그러나이 자동 업데이트에서 웹 또는 함수 앱에 대 한 런타임 문제가 발생 하는 드문 경우 이지만 이전 미들웨어 버전으로 일시적으로 롤백할 수 있습니다. 이 문서에서는 특정 버전의 인증 미들웨어에 앱을 일시적으로 고정 하는 방법을 설명 합니다.

### <a name="automatic-and-manual-version-updates"></a>자동 및 수동 버전 업데이트 

앱에 대 한 설정을 설정 하 여 특정 버전의 플랫폼 미들웨어에 앱을 고정할 수 있습니다 `runtimeVersion` . 앱은 특정 버전에 명시적으로 다시 고정 하도록 선택 하지 않는 한 항상 최신 버전에서 실행 됩니다. 한 번에 몇 가지 버전이 지원 됩니다. 더 이상 지원 되지 않는 잘못 된 버전에 고정 하면 앱에서 최신 버전을 대신 사용 합니다. 항상 최신 버전을 실행 하려면 `runtimeVersion` ~ 1로 설정 합니다. 

### <a name="view-and-update-the-current-runtime-version"></a>현재 런타임 버전 확인 및 업데이트

앱에서 사용 하는 런타임 버전을 변경할 수 있습니다. 앱을 다시 시작한 후 새 런타임 버전이 적용 됩니다. 

#### <a name="view-the-current-runtime-version"></a>현재 런타임 버전 보기

Azure CLI를 사용 하거나 앱의 built0 버전 HTTP 끝점 중 하나를 통해 플랫폼 인증 미들웨어의 현재 버전을 볼 수 있습니다.

##### <a name="from-the-azure-cli"></a>Azure CLI에서

Azure CLI를 사용 하 여 [az webapp auth show](/cli/azure/webapp/auth?view=azure-cli-latest#az-webapp-auth-show) 명령을 사용 하 여 현재 미들웨어 버전을 확인 합니다.

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

이 코드에서를 `<my_app_name>` 앱 이름으로 바꿉니다. 또한를 `<my_resource_group>` 앱에 대 한 리소스 그룹의 이름으로 바꿉니다.

`runtimeVersion`CLI 출력에 필드가 표시 됩니다. 명확성을 위해 잘린 다음 예제 출력과 유사 합니다. 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>버전 끝점에서

앱에서/.auth/version 끝점을 적중 하 여 앱이 실행 되 고 있는 현재 미들웨어 버전을 볼 수도 있습니다. 다음 예제 출력과 유사 합니다.
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>현재 런타임 버전을 업데이트 합니다.

Azure CLI를 사용 하 여 `runtimeVersion` [az webapp auth update](/cli/azure/webapp/auth?view=azure-cli-latest#az-webapp-auth-update) 명령을 사용 하 여 앱에서 설정을 업데이트할 수 있습니다.

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

를 `<my_app_name>` 앱의 이름으로 바꿉니다. 또한를 `<my_resource_group>` 앱에 대 한 리소스 그룹의 이름으로 바꿉니다. 또한을 (를 `<version>` ) 1.x 런타임의 유효한 버전 또는 최신 버전으로 바꿉니다 `~1` . https://github.com/Azure/app-service-announcements)에 고정할 버전을 결정 하는 데 도움이 되는 다양 한 런타임 버전 [여기] (릴리스 정보)을 찾을 수 있습니다.

앞의 코드 샘플에서 **사용해 보세요.** 를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure/reference-index#az-login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)