---
title: Microsoft ID 플랫폼 및 OAuth 2.0 On-Behalf-Of 흐름 | Azure
titleSuffix: Microsoft identity platform
description: 이 문서는 OAuth 2.0 On-Behalf-Of 흐름을 사용하여 서비스 간 인증을 구현하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 018d67b3e4e730cd46eb524a8927b3a6d68d74e8
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958663"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft ID 플랫폼 및 OAuth 2.0 On-Behalf-Of 흐름


OAuth 2.0 OBO(On-Behalf-Of) 흐름은 애플리케이션이 서비스/웹 API를 호출하고 차례로 다른 서비스/웹 API를 호출해야 하는 사용 사례를 제공합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. 중간 계층 서비스가 다운스트림 서비스에 대해 인증된 요청을 수행하도록 하려면 사용자를 대신하여 Microsoft ID 플랫폼에서 액세스 토큰을 보호해야 합니다.

이 문서에서는 애플리케이션에서 프로토콜에 대해 직접 프로그래밍을 수행하는 방법을 설명합니다.  가능하면 [토큰을 획득하고 보안 Web API를 호출](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)하는 대신, 지원되는 MSAL(Microsoft 인증 라이브러리)을 사용하는 것이 좋습니다.  [MSAL을 사용하는 샘플 앱](sample-v2-code.md)도 살펴봅니다.

> [!NOTE]
> 2018년 5월을 기준으로 일부 암시적 흐름 파생 `id_token`은 OBO 흐름에 사용할 수 없습니다. SPA(단일 페이지 앱)는 OBO 흐름을 수행하려면, 대신 중간 계층 기밀 클라이언트에 **액세스** 토큰을 전달해야 합니다. OBO 호출을 수행할 수 있는 클라이언트에 대한 자세한 내용은 [제한 사항](#client-limitations)을 참조하세요.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

사용자가 [OAuth 2.0 권한 부여 코드 부여 흐름](v2-oauth2-auth-code-flow.md) 또는 다른 로그인 흐름을 사용하여 애플리케이션에 대해 인증되었다고 가정합니다. 이 시점에서 애플리케이션에는 사용자의 클레임 및 중간 계층 웹 API(API A) 액세스에 대한 동의가 있는 API A용 액세스 토큰(토큰 A)이 있습니다. 이제 API A는 다운스트림 웹 API(API B)에 대해 인증된 요청을 해야 합니다.

다음 단계는 OBO 흐름을 구성하며 다음 다이어그램을 통해 쉽게 이해할 수 있습니다.

![OAuth 2.0 On-Behalf-Of 흐름을 표시합니다.](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. 클라이언트 애플리케이션은 토큰 A(API A의 `aud` 클레임 포함)를 사용하여 API A에 요청합니다.
1. API A는 Microsoft ID 플랫폼 토큰 발급 엔드포인트에 인증하고 API B에 액세스하기 위한 토큰을 요청합니다.
1. Microsoft ID 플랫폼 토큰 발급 엔드포인트는 토큰 A와 함께 API A의 자격 증명의 유효성을 검사하고 API B에 대한 액세스 토큰(토큰 B)을 API A에 발급합니다.
1. API A에 의해 토큰 B가 API B에 대한 요청의 권한 부여 헤더에 설정됩니다.
1. 보안 리소스의 데이터는 API B에 의해 API A로 반환되고, 여기에서 클라이언트로 반환됩니다.

> [!NOTE]
> 이 시나리오에서는 중간 계층 서비스에서 다운스트림 API에 액세스하기 위해 사용자 동의를 얻기 위한 사용자 상호 작용이 없습니다. 따라서 다운스트림 API에 대한 액세스를 부여할 수 있는 옵션이 인증 과정에서 동의 단계 중 일부로 미리 제공됩니다. 앱에 대해 이를 설정하는 방법을 알아보려면 [중간 계층 애플리케이션에 대한 동의 얻기](#gaining-consent-for-the-middle-tier-application)를 참조하세요.

## <a name="middle-tier-access-token-request"></a>중간 계층 액세스 토큰 요청

액세스 토큰을 요청하려면 다음 매개 변수로 테넌트별 Microsoft ID 플랫폼 토큰 엔드포인트에 HTTP POST를 만듭니다.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

클라이언트 애플리케이션이 공유 암호 또는 인증서 중에서 어떤 방식으로 보호되도록 선택되는지에 따라 두 가지 사례가 있습니다.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 비밀을 사용하여 액세스 토큰 요청

공유 암호를 사용할 경우 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 | Type | 설명 |
| --- | --- | --- |
| `grant_type` | 필수 | 토큰 요청의 형식입니다. JWT를 사용하는 요청의 경우 값은 `urn:ietf:params:oauth:grant-type:jwt-bearer`여야 합니다. |
| `client_id` | 필수 | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당한 애플리케이션(클라이언트) ID입니다. |
| `client_secret` | 필수 | Azure Portal - 앱 등록 페이지에서 앱에 대해 생성한 클라이언트 암호입니다. |
| `assertion` | 필수 | 중간 계층 API에 전송 된 액세스 토큰입니다.  이 토큰에는 `aud` 이 OBO 요청 (필드로 표시 된 앱)을 만드는 앱의 대상 그룹 () 클레임이 있어야 합니다 `client-id` . 응용 프로그램은 다른 응용 프로그램에 대 한 토큰을 사용할 수 없습니다. 예를 들어 클라이언트가 MS Graph 용 토큰을 전송 하는 경우에는 API가 OBO를 사용 하 여이 API를 사용할 수 없습니다.  대신 토큰을 거부 해야 합니다.  |
| `scope` | 필수 | 토큰 요청에 대해 공백으로 구분된 범위 목록입니다. 자세한 내용은 [범위](v2-permissions-and-consent.md)를 참조하세요. |
| `requested_token_use` | 필수 | 요청 처리 방법을 지정합니다. OBO 흐름에서는 값을 `on_behalf_of`로 설정해야 합니다. |

#### <a name="example"></a>예제

다음 HTTP POST는 `user.read` 범위의 https://graph.microsoft.com 웹 API용 액세스 토큰과 새로 고침 토큰을 요청합니다.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용하여 액세스 토큰 요청

인증서를 사용한 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 | Type | 설명 |
| --- | --- | --- |
| `grant_type` | 필수 | 토큰 요청의 형식입니다. JWT를 사용하는 요청의 경우 값은 `urn:ietf:params:oauth:grant-type:jwt-bearer`여야 합니다. |
| `client_id` | 필수 |  [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당한 애플리케이션(클라이언트) ID입니다. |
| `client_assertion_type` | 필수 | 값은 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`여야 합니다. |
| `client_assertion` | 필수 | 애플리케이션의 자격 증명으로 등록한 인증서를 사용하여 만들고 서명해야 하는 어설션(JSON Web Token)입니다. 인증서 등록 방법 및 어설션 형식에 대한 자세한 내용은 [인증서 자격 증명](active-directory-certificate-credentials.md)을 참조하세요. |
| `assertion` | 필수 |  중간 계층 API에 전송 된 액세스 토큰입니다.  이 토큰에는 `aud` 이 OBO 요청 (필드로 표시 된 앱)을 만드는 앱의 대상 그룹 () 클레임이 있어야 합니다 `client-id` . 응용 프로그램은 다른 응용 프로그램에 대 한 토큰을 사용할 수 없습니다. 예를 들어 클라이언트가 MS Graph 용 토큰을 전송 하는 경우에는 API가 OBO를 사용 하 여이 API를 사용할 수 없습니다.  대신 토큰을 거부 해야 합니다.  |
| `requested_token_use` | 필수 | 요청 처리 방법을 지정합니다. OBO 흐름에서는 값을 `on_behalf_of`로 설정해야 합니다. |
| `scope` | 필수 | 토큰 요청에 대해 공백으로 구분된 범위 목록입니다. 자세한 내용은 [범위](v2-permissions-and-consent.md)를 참조하세요.|

`client_secret` 매개 변수가 두 개의 매개 변수 `client_assertion_type` 및 `client_assertion`으로 바뀐다는 것을 제외하고 공유 비밀에 따른 요청 사례와 매개 변수는 거의 동일합니다.

#### <a name="example"></a>예제

다음 HTTP POST는 인증서가 있는 `user.read` 범위의 https://graph.microsoft.com 웹 API용 액세스 토큰을 요청합니다.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>중간 계층 액세스 토큰 응답

성공 응답은 다음 매개 변수가 있는 JSON OAuth 2.0 응답입니다.

| 매개 변수 | Description |
| --- | --- |
| `token_type` | 토큰 유형 값을 나타냅니다. Microsoft ID 플랫폼에서 지원하는 유일한 형식은 `Bearer`입니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| `scope` | 토큰에 부여된 액세스 범위입니다. |
| `expires_in` | 액세스 토큰이 유효한 시간(초)입니다. |
| `access_token` | 요청된 액세스 토큰입니다. 호출 서비스는 이 토큰을 사용하여 수신 서비스에 인증할 수 있습니다. |
| `refresh_token` | 요청된 액세스 토큰에 대한 새로 고침 토큰입니다. 호출 서비스는 이 토큰을 사용하여 현재 액세스 토큰이 만료된 후 다른 액세스 토큰을 요청할 수 있습니다. 새로 고침 토큰은 `offline_access` 범위가 요청된 경우에만 제공됩니다. |

### <a name="success-response-example"></a>성공 응답 예제

다음 예제에서는 https://graph.microsoft.com 웹 API용 액세스 토큰 요청에 대한 성공 응답을 보여 줍니다.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

> [!NOTE]
> 위의 액세스 토큰은 Microsoft Graph에 대 한 v 1.0 형식 토큰입니다. 토큰 형식이 액세스 되는 **리소스** 를 기반으로 하 고 요청 하는 데 사용 되는 끝점과 관련이 없기 때문입니다. Microsoft Graph는 v1.0 토큰을 허용하도록 설정되어 있으므로 클라이언트가 Microsoft Graph에 대한 토큰을 요청할 때 Microsoft ID 플랫폼이 v1.0 액세스 토큰을 생성합니다. 다른 앱은 v2.0 형식 토큰, v 1.0 형식 토큰 또는 독점적 이거나 암호화 된 토큰 형식을 원합니다.  V1.0 및 v2.0 끝점 모두 토큰 형식을 내보낼 수 있습니다. 이렇게 하면 클라이언트가 토큰을 요청한 방법이 나 위치에 관계 없이 리소스에서 항상 올바른 토큰 형식을 가져올 수 있습니다. 
>
> 애플리케이션에만 액세스 토큰이 표시되어야 합니다. 클라이언트는 이를 검사하지 **않아야 합니다**. 코드의 다른 앱에 대 한 액세스 토큰을 검사 하면 앱에서 토큰의 형식을 변경 하거나 해당 앱의 암호화를 시작할 때 앱이 예기치 않게 중단 됩니다. 

### <a name="error-response-example"></a>오류 응답 예제

다운스트림 API에 설정된 조건부 액세스 정책(예: [다단계 인증](../authentication/concept-mfa-howitworks.md))이 있는 경우, 다운스트림 API에 대한 액세스 토큰을 얻으려고 할 때 토큰 엔드포인트에서 오류 응답이 반환됩니다. 중간 계층 서비스는 이 오류를 클라이언트 애플리케이션에 전달하여 클라이언트 애플리케이션이 조건부 액세스 정책을 충족시키기 위해 사용자 상호 작용을 제공할 수 있도록 해야 합니다.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>보안 리소스에 액세스하는 데 액세스 토큰 사용

이제 중간 계층 서비스는 위에서 획득한 토큰을 사용하고 `Authorization` 헤더에서 토큰을 설정하여 다운스트림 웹 API에 대해 인증된 요청을 할 수 있습니다.

### <a name="example"></a>예제

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>OAuth2.0 OBO 흐름을 통해 획득한 SAML 어설션

일부 OAuth 기반 웹 서비스는 비대화형 흐름에서 SAML 어설션을 수락하는 다른 웹 서비스 API에 액세스해야 합니다. Azure Active Directory는 SAML 기반 웹 서비스를 사용하는 On-Behalf-Of 흐름에 대응하여 SAML 어설션을 대상 리소스로 제공할 수 있습니다.

>[!NOTE]
>이 어설션은 OAuth2 기반 애플리케이션이 SAML 토큰을 사용하는 웹 서비스 API 엔드포인트에 액세스할 수 있는 OAuth 2.0 On-Behalf-Of 흐름에 대한 비표준 확장입니다.

> [!TIP]
> 프런트 엔드 웹 애플리케이션에서 SAML로 보호되는 웹 서비스를 호출하는 경우 간단히 API를 호출하고 사용자 기존 세션을 사용하여 일반적인 대화형 인증 흐름을 시작할 수 있습니다. 서비스 간 호출이 사용자 컨텍스트를 제공하기 위해 SAML 토큰을 요구할 경우에만 OBO 흐름을 사용해야 합니다.

## <a name="gaining-consent-for-the-middle-tier-application"></a>중간 계층 애플리케이션에 대한 동의 얻기

애플리케이션의 아키텍처 또는 사용에 따라, OBO 흐름을 성공적으로 유지하기 위해 다른 전략을 고려할 수 있습니다. 모든 경우에서 최종 목표는 클라이언트 앱이 중간 계층 앱을 호출할 수 있도록 적절한 동의가 제공되고 중간 계층 앱에 백 엔드 리소스를 호출할 수 있는 권한이 있는지 확인하는 것입니다.

> [!NOTE]
> 이전에는 Microsoft 계정 시스템(개인 계정)이 "알려진 클라이언트 애플리케이션" 필드를 지원하지 않았고 결합된 동의가 표시되지 않았습니다.  이 필드가 추가되었으며 Microsoft ID 플랫폼의 모든 앱은 OBO 호출에 대한 동의를 얻기 위해 알려진 클라이언트 애플리케이션 접근 방식을 사용할 수 있습니다.

### <a name="default-and-combined-consent"></a>/.default 및 결합된 승인

중간 계층 애플리케이션이 해당 매니페스트의 알려진 클라이언트 애플리케이션 목록에 클라이언트를 추가하면, 클라이언트는 자기 자신과 중간 계층 애플리케이션을 위해 결합된 동의 흐름을 트리거할 수 있습니다. Microsoft ID 플랫폼 엔드포인트에서 이 작업은 [`/.default` 범위](v2-permissions-and-consent.md#the-default-scope)를 사용하여 수행됩니다. 알려진 클라이언트 애플리케이션 및 `/.default`를 사용하여 동의 화면을 트리거하는 경우 동의 화면은 중간 계층 API에 대한 **두** 클라이언트의 사용 권한을 표시하고, 중간 계층 API에 필요한 권한을 요청합니다. 사용자가 두 애플리케이션에 대한 동의를 제공하면 OBO 흐름이 작동합니다.

### <a name="pre-authorized-applications"></a>사전 승인된 애플리케이션

리소스는 지정된 애플리케이션이 항상 특정 범위를 수신할 수 있는 권한이 있음을 나타낼 수 있습니다. 이러한 특성은 프런트 엔드 클라이언트와 백 엔드 리소스가 좀 더 원활하게 연결되도록 하는 데 특히 유용합니다. 리소스는 여러 사전 승인된 애플리케이션을 선언할 수 있습니다. 이러한 애플리케이션은 OBO 흐름에서 이러한 권한을 요청하고, 사용자가 동의를 제공하지 않아도 수신할 수 있습니다.

### <a name="admin-consent"></a>관리자 동의

테넌트 관리자는 중간 계층 애플리케이션에 대한 관리자 동의를 제공하여 애플리케이션이 필요한 해당 API를 호출할 수 있는 권한을 갖도록 보장할 수 있습니다. 이렇게 하기 위해 관리자는 해당 테넌트에서 중간 계층 애플리케이션을 찾고, 필수 사용 권한 페이지를 열고, 앱에 대한 권한을 부여하도록 선택할 수 있습니다. 관리자 동의에 대한 자세한 내용은 [동의 및 권한 설명서](v2-permissions-and-consent.md)를 참조하세요.

### <a name="use-of-a-single-application"></a>단일 애플리케이션 사용

일부 시나리오에서는 중간 계층 및 프런트 엔드 클라이언트의 단일 페어링만 유지할 수 있습니다. 이 시나리오에서는 이러한 항목을 보다 쉽게 단일 애플리케이션으로 만들 수 있으므로 중간 계층 애플리케이션이 전혀 필요하지 않습니다. 프런트 엔드 및 Web API 간에 인증을 수행하기 위해 쿠키, id_token 또는 애플리케이션 자체에서 요청한 액세스 토큰을 사용할 수 있습니다. 그런 다음 이 단일 애플리케이션에서 백 엔드 리소스로의 동의를 요청합니다.

## <a name="client-limitations"></a>클라이언트 제한 사항

클라이언트가 암시적 흐름을 사용하여 id_token을 가져오는 경우 해당 클라이언트에는 회신 URL에 와일드카드가 있어야 하며 id_token은 OBO 흐름에 사용할 수 없습니다.  그러나 암시적 권한 부여 흐름을 통해 획득한 액세스 토큰은 시작하는 클라이언트에 등록된 와일드카드 회신 URL이 경우에도 기밀 클라이언트에서 계속 회수될 수 있습니다.

## <a name="next-steps"></a>다음 단계

OAuth 2.0 프로토콜 및 클라이언트 자격 증명을 사용하여 서비스 간 인증을 수행하는 다른 방법에 대해 자세히 알아보세요.

* [Microsoft ID 플랫폼에서 OAuth 2.0 클라이언트 자격 증명 부여](v2-oauth2-client-creds-grant-flow.md)
* [Microsoft ID 플랫폼의 OAuth 2.0 코드 흐름](v2-oauth2-auth-code-flow.md)
* [`/.default` 범위](v2-permissions-and-consent.md#the-default-scope) 사용
