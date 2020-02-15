---
title: OAuth 인증 코드 흐름-Microsoft identity platform | Microsoft
description: OAuth 2.0 인증 프로토콜의 Microsoft id 플랫폼 구현을 사용 하 여 웹 응용 프로그램을 빌드합니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 8a847afa2253223ebe9450d350cd18f5f659e0e3
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159780"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft id 플랫폼 및 OAuth 2.0 인증 코드 흐름

OAuth 2.0 인증 코드 권한은 디바이스에 설치된 앱에서 사용하여 Web API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. OAuth 2.0의 Microsoft id 플랫폼 구현을 사용 하 여 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. 이 가이드는 언어 독립적이며 [Azure 공개 소스 인증 라이브러리](reference-v2-libraries.md)를 사용하지 않고 HTTP 메시지를 수신하는 방법을 설명합니다.

이 문서에서는 응용 프로그램에서 프로토콜에 대해 직접 프로그래밍 하는 방법을 설명 합니다.  가능 하면 [토큰을 획득 하 고 보안 웹 api를 호출](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)하는 대신 지원 되는 Msal (Microsoft 인증 라이브러리)을 사용 하는 것이 좋습니다.  [MSAL을 사용 하는 샘플 앱](sample-v2-code.md)에 대해서도 살펴봅니다.

> [!NOTE]
> 모든 Azure Active Directory 시나리오 & 기능이 Microsoft id 플랫폼 끝점에서 지원 되는 것은 아닙니다. Microsoft id 플랫폼 끝점을 사용 해야 하는지 확인 하려면 [microsoft id 플랫폼 제한 사항](active-directory-v2-limitations.md)을 참조 하세요.

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](https://tools.ietf.org/html/rfc6749)에서 설명합니다. [웹 앱](v2-app-types.md#web-apps) 및 [기본적으로 설치 된 앱](v2-app-types.md#mobile-and-native-apps)을 비롯 하 여 대부분의 앱 형식에서 인증 및 권한 부여를 수행 하는 데 사용 됩니다. 이 흐름을 사용 하면 앱이 Microsoft id 플랫폼 끝점에서 보호 되는 리소스에 액세스 하는 데 사용할 수 있는 access_tokens을 안전 하 게 가져올 수 있습니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

높은 수준에서 네이티브/모바일 애플리케이션에 대한 전체 인증 흐름은 다음과 같습니다.

![OAuth 인증 코드 흐름](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>인증 코드 요청

인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 엔드포인트로 보내는 것으로 시작됩니다. 이 요청에서 클라이언트는 사용자의 `openid`, `offline_access`및 `https://graph.microsoft.com/mail.read `권한을 요청 합니다.  예를 들어 `Directory.ReadWrite.All`를 사용 하 여 조직의 디렉터리에 데이터를 작성 하는 등의 일부 권한은 관리자가 제한 합니다. 응용 프로그램이 조직 사용자에 게 이러한 사용 권한 중 하나에 대 한 액세스를 요청 하는 경우 사용자는 앱의 사용 권한에 동의할 수 있는 권한이 없다는 오류 메시지를 받게 됩니다. 관리자 제한 범위에 대 한 액세스를 요청 하려면 회사 관리자에 게 직접 요청 해야 합니다.  자세한 내용은 [관리자 제한 권한](v2-permissions-and-consent.md#admin-restricted-permissions)을 참조 하세요.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> 이 요청을 실행하려면 아래 링크를 클릭하세요. 로그인하면 브라우저가 주소 표시줄에서 `https://localhost/myapp/` 과 함께 `code` 으로 리디렉션됩니다.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 매개 변수    | 필수/선택 | Description |
|--------------|-------------|--------------|
| `tenant`    | required    | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.  |
| `client_id`   | required    | [Azure Portal – 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 앱에 할당 한 **응용 프로그램 (클라이언트) ID** 입니다.  |
| `response_type` | required    | 인증 코드 흐름에 대한 `code`를 포함해야 합니다.       |
| `redirect_uri`  | required | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. 네이티브 및 모바일 앱의 경우 `https://login.microsoftonline.com/common/oauth2/nativeclient`의 기본값을 사용해야 합니다.   |
| `scope`  | required    | 사용자가 동의하게 할 공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다.  요청의 `/authorize` 레그의 경우 여러 리소스를 포함할 수 있으므로 앱에서 호출 하려는 여러 웹 Api에 대 한 동의를 받을 수 있습니다. |
| `response_mode`   | 권장 | 결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 다음 중 하나일 수 있습니다.<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`는 리디렉션 URI에 코드를 쿼리 문자열 매개 변수로 제공합니다. 암시적 흐름을 사용 하 여 ID 토큰을 요청 하는 경우 [openid connect 사양](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)에 지정 된 대로 `query`를 사용할 수 없습니다. 코드만 요청 하는 경우 `query`, `fragment`또는 `form_post`를 사용할 수 있습니다. `form_post`는 리디렉션 URI에 대한 코드가 포함된 POST를 실행합니다. 자세한 내용은 [OpenID Connect 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)을 참조하세요.  |
| `state`                 | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 이 값은 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩할 수 있습니다. |
| `prompt`  | 선택 사항    | 필요한 사용자 상호 작용 유형을 나타냅니다. 이 경우 유효한 값은 `login`, `none` 및 `consent`뿐입니다.<br/><br/>- `prompt=login`은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다.<br/>- `prompt=none`는 반대 이며, 사용자에 게 대화형 프롬프트를 표시 하지 않습니다. Single sign-on을 통해 요청을 자동으로 완료할 수 없는 경우 Microsoft identity platform 끝점은 `interaction_required` 오류를 반환 합니다.<br/>- `prompt=consent`는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| `login_hint`  | 선택 사항    | 사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다.   |
| `domain_hint`  | 선택 사항    | `consumers` 또는 `organizations` 중 하나일 수 있습니다.<br/><br/>포함 되는 경우 사용자가 로그인 페이지에서 거치는 전자 메일 기반 검색 프로세스를 건너뛰고 약간 더 간소화 된 사용자 환경을 제공 합니다. 앱이 이전 로그인 작업에서 `tid` 를 추출하여 재인증 과정에서 이 매개 변수를 종종 사용합니다. `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용해야 합니다. 그렇지 않으면 `domain_hint=organizations`를 사용합니다.  |
| `code_challenge_method` | 선택 사항    | `code_verifier` 매개 변수에 대한 `code_challenge`를 인코딩하는 데 사용되는 메서드입니다. 다음 값 중 하나를 사용할 수 있습니다.<br/><br/>- `plain` <br/>- `S256`<br/><br/>제외할 경우 `code_challenge`가 포함되면 `code_challenge`가 일반 텍스트로 간주됩니다. Microsoft id 플랫폼은 `plain` 및 `S256`를 모두 지원 합니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |
| `code_challenge`  | 선택 사항 | 네이티브 클라이언트의 PKCE(Proof Key for Code Exchange)를 통해 권한 부여 코드를 보호하는 데 사용됩니다. `code_challenge_method`가 포함되면 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. 또한 Microsoft identity platform 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시 된 사용 권한에 동의한 확인 합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](v2-permissions-and-consent.md).

사용자가 인증 하 고 동의 하면 Microsoft id 플랫폼 끝점이 `response_mode` 매개 변수에 지정 된 메서드를 사용 하 여 지정 된 `redirect_uri`에서 앱에 대 한 응답을 반환 합니다.

#### <a name="successful-response"></a>성공적인 응답

`response_mode=query` 를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 매개 변수 | Description  |
|-----------|--------------|
| `code` | 앱이 요청한 authorization_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. Authorization_codes 수명이 짧고, 일반적으로 약 10 분 후에 만료 됩니다. |
| `state` | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | Description  |
|----------|------------------|
| `error`  | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>권한 부여 엔드포인트 오류에 대한 오류 코드

다음 테이블은 오류 응답의 `error` 매개 변수에 반환될 수 있는 여러 오류 코드를 설명합니다.

| 오류 코드  | Description    | 클라이언트 작업   |
|-------------|----------------|-----------------|
| `invalid_request` | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다. |
| `unauthorized_client` | 클라이언트 응용 프로그램에서 권한 부여 코드를 요청할 수 없습니다. | 이 오류는 일반적으로 클라이언트 응용 프로그램이 Azure AD에 등록 되지 않았거나 사용자의 Azure AD 테 넌 트에 추가 되지 않은 경우에 발생 합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `access_denied`  | 리소스 소유자가 동의 거부  | 클라이언트 응용 프로그램은 사용자가 동의 때까지 계속할 수 없다고 사용자에 게 알릴 수 있습니다. |
| `unsupported_response_type` | 권한 부여 서버가 요청에 해당 응답 형식을 지원하지 않습니다. | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다.  |
| `server_error`  | 서버에 예기치 않은 오류가 발생했습니다.| 요청을 다시 시도하십시오. 이러한 오류는 일시적인 상태 때문에 발생할 수 있습니다. 클라이언트 애플리케이션이 일시적 오류 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| `temporarily_unavailable`   | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 요청을 다시 시도하십시오. 클라이언트 응용 프로그램은 일시적인 상태 때문에 응답이 지연 되었음을 사용자에 게 설명할 수 있습니다. |
| `invalid_resource`  | 대상 리소스가 존재 하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성 되지 않았기 때문에 잘못 되었습니다. | 이 오류는 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `login_required` | 사용자가 너무 많거나 없습니다. | 클라이언트에서 자동 인증(`prompt=none`)을 요청했지만 단일 사용자를 찾을 수 없습니다. 이는 세션에서 여러 사용자가 활성 상태이거나 사용자가 없음을 의미할 수 있습니다. 이는 선택한 테 넌 트를 고려 합니다. 예를 들어 두 개의 Azure AD 계정이 활성 상태이 고 `consumers` Microsoft 계정 하나가 있는 경우 자동 인증은 작동 합니다. |
| `interaction_required` | 요청을 위해 사용자 상호 작용이 필요합니다. | 추가 인증 단계 또는 동의가 필요합니다. `prompt=none`을 사용하지 않고 요청을 다시 시도하세요. |

## <a name="request-an-access-token"></a>액세스 토큰 요청

authorization_code를 획득하고 사용자가 사용 권한을 부여했으므로 `code`를 원하는 리소스에 대한 `access_token`으로 교환할 수 있습니다. 이렇게 하려면 `POST` 엔드포인트에 `/token` 요청을 보내면 됩니다.

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Postman에서 이 요청을 실행해 보세요. (`code`를 바꾸어야 함) [Postman에서이 요청을 실행![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| 매개 변수  | 필수/선택 | Description     |
|------------|-------------------|----------------|
| `tenant`   | required   | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.  |
| `client_id` | required  | [Azure Portal – 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당 된 응용 프로그램 (클라이언트) ID입니다. |
| `grant_type` | required   | 인증 코드 흐름에 대한 `authorization_code` 여야 합니다.   |
| `scope`      | required   | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 첫 번째 레그에서 요청된 범위와 동일하거나 하위 집합이어야 합니다. 모든 범위는 단일 리소스에서 비롯 된 OIDC 범위 (`profile`, `openid`, `email`)와 함께 사용 해야 합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요. |
| `code`          | required  | 흐름의 첫 번째 레그에서 얻은 authorization_code입니다. |
| `redirect_uri`  | required  | authorization_code를 획득하는 데 사용된 값과 동일한 redirect_uri 값입니다. |
| `client_secret` | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 애플리케이션 암호입니다. Client_secrets 장치에 안정적으로 저장할 수 없기 때문에 네이티브 앱에서 응용 프로그램 암호를 사용 하면 안 됩니다. 서버 쪽에 안전 하 게 client_secret를 저장할 수 있는 웹 앱과 web Api에 필요 합니다.  클라이언트 암호는 전송되기 전에 URL로 인코딩되어야 합니다.  |
| `code_verifier` | 선택 사항  | authorization_code를 얻는 데 사용된 동일한 code_verifier입니다. 인증 코드 부여 요청에 PKCE가 사용된 경우에는 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |

### <a name="successful-response"></a>성공적인 응답

성공적인 토큰 응답은 다음과 같습니다.

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 매개 변수     | Description   |
|---------------|------------------------------|
| `access_token`  | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스를 인증할 수 있습니다.  |
| `token_type`    | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| `expires_in`    | 액세스 토큰이 유효한 기간(초)입니다. |
| `scope`         | access_token이 유효한 범위입니다. |
| `refresh_token` | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 액세스 토큰이 만료된 후 이 토큰을 사용하여 추가 액세스 토큰을 획득할 수 있습니다. refresh_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 액세스 토큰 새로 고침에 대한 자세한 내용은 [아래 섹션](#refresh-the-access-token)을 참조하세요. <br> **참고:** `offline_access` 범위가 요청된 경우에만 제공됩니다. |
| `id_token`      | JWT(JSON Web Token) 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |

### <a name="error-response"></a>오류 응답

오류 응답은 다음과 같습니다.

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 매개 변수         | Description    |
|-------------------|----------------|
| `error`       | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| `error_codes` | 진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다.  |
| `timestamp`   | 오류가 발생한 시간입니다. |
| `trace_id`    | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| `correlation_id` | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

### <a name="error-codes-for-token-endpoint-errors"></a>토큰 엔드포인트 오류에 대한 오류 코드

| 오류 코드         | Description        | 클라이언트 작업    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청을 수정하여 다시 제출   |
| `invalid_grant`    | 권한 부여 코드 또는 PKCE 코드 확인자가 잘못되었거나 만료되었습니다. | `/authorize` 엔드포인트에 대한 새 요청을 시도하고 code_verifier 매개 변수가 잘못되었는지 확인합니다.  |
| `unauthorized_client` | 인증 된 클라이언트는이 권한 부여 유형을 사용할 권한이 없습니다. | 이는 일반적으로 클라이언트 응용 프로그램이 Azure AD에 등록 되지 않았거나 사용자의 Azure AD 테 넌 트에 추가 되지 않은 경우에 발생 합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `invalid_client` | 클라이언트 인증에 실패했습니다.  | 클라이언트 자격 증명이 유효 하지 않습니다. 해결하려면 애플리케이션 관리자가 자격 증명을 업데이트합니다.   |
| `unsupported_grant_type` | 권한 부여 서버가 해당 권한 부여 유형을 지원하지 않습니다. | 요청에서 권한 부여 유형을 변경하십시오. 이 유형의 오류는 개발 중에만 발생하며 초기 테스트 중에 검색됩니다. |
| `invalid_resource` | 대상 리소스가 존재 하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성 되지 않았기 때문에 잘못 되었습니다. | 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다.  |
| `interaction_required` | 요청을 위해 사용자 상호 작용이 필요합니다. 예를 들어 추가 인증 단계가 필요합니다. | 동일한 리소스를 사용하여 요청을 다시 시도하십시오.  |
| `temporarily_unavailable` | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 요청을 다시 시도하십시오. 클라이언트 응용 프로그램은 일시적인 상태 때문에 응답이 지연 되었음을 사용자에 게 설명할 수 있습니다. |

## <a name="use-the-access-token"></a>액세스 토큰 사용

`access_token`을 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 Web API에 대한 요청에 토큰을 사용할 수 있습니다.

> [!TIP]
> Postman에서 이 요청을 실행하세요. (`Authorization` 헤더를 먼저 바꿉니다.) [Postman에서이 요청을 실행![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>액세스 토큰 새로 고침

access_token은 수명이 짧으며, 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 이렇게 하려면 다른 `POST` 요청을 `/token` 엔드포인트에 제출해야 하며, 이번에는 `refresh_token` 대신 `code`을 제공해야 합니다.  새로 고침 토큰은 클라이언트가 이미 동의를 받은 모든 권한에 유효합니다. 따라서 `scope=mail.read`에 대한 요청에서 발행된 새로 고침 토큰을 사용하여 `scope=api://contoso.com/api/UseResource`에 대한 새 액세스 토큰을 요청할 수 있습니다.  

새로 고침 토큰에는 지정된 수명이 없습니다. 일반적으로 새로 고침 토큰의 수명은 비교적 깁니다. 그러나 새로 고침 토큰이 만료되거나 해지되거나 원하는 작업을 위한 충분한 권한이 없는 경우가 있습니다. 애플리케이션은 [토큰 발급 엔드포인트에서 반환하는 오류](#error-codes-for-token-endpoint-errors)를 예상하고 정확히 처리해야 합니다. 

새로 고침 토큰은 새 액세스 토큰을 획득 하는 데 사용 될 때 해지 되지 않지만 이전 새로 고침 토큰을 삭제 해야 합니다. [OAuth 2.0 spec](https://tools.ietf.org/html/rfc6749#section-6) : "권한 부여 서버에서 새 새로 고침 토큰을 발급할 수 있습니다 .이 경우 클라이언트는 이전 새로 고침 토큰을 삭제 하 고 새 새로 고침 토큰으로 바꾸어야 합니다. 권한 부여 서버에서 새 새로 고침 토큰을 클라이언트에 발급 한 후 이전 새로 고침 토큰을 해지할 수 있습니다. "  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Postman에서 이 요청을 실행해 보세요. (`refresh_token`를 바꾸어야 함) [Postman에서이 요청을 실행![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| 매개 변수     |                | Description        |
|---------------|----------------|--------------------|
| `tenant`        | required     | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.   |
| `client_id`     | required    | [Azure Portal – 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 앱에 할당 한 **응용 프로그램 (클라이언트) ID** 입니다. |
| `grant_type`    | required    | 이 인증 코드 흐름 범례에 대한 `refresh_token` 이어야 합니다. |
| `scope`         | required    | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 원래 authorization_code 요청 레그에서 요청된 범위와 동일하거나 하위 집합이어야 합니다. 이 요청에 지정 된 범위가 여러 리소스 서버에 걸쳐 있는 경우 Microsoft id 플랫폼 끝점은 첫 번째 범위에 지정 된 리소스에 대 한 토큰을 반환 합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요. |
| `refresh_token` | required    | 흐름의 두 번째 레그에서 얻은 refresh_token입니다. |
| `client_secret` | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 애플리케이션 암호입니다. Client_secrets 장치에 안정적으로 저장할 수 없기 때문에 네이티브 앱에서 사용 하면 안 됩니다. 서버 쪽에 안전 하 게 client_secret를 저장할 수 있는 웹 앱과 web Api에 필요 합니다. |

#### <a name="successful-response"></a>성공적인 응답

성공적인 토큰 응답은 다음과 같습니다.

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 매개 변수     | Description         |
|---------------|-------------------------------------------------------------|
| `access_token`  | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스를 인증할 수 있습니다. |
| `token_type`    | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| `expires_in`    | 액세스 토큰이 유효한 기간(초)입니다.   |
| `scope`         | access_token이 유효한 범위입니다.    |
| `refresh_token` | 새 OAuth 2.0 새로 고침 토큰입니다. 이전 새로 고침 토큰을 새로 얻은 새로 고침 토큰으로 대체하여 새로 고침 토큰을 최대한 오랫동안 유효한 상태로 유지해야 합니다. <br> **참고:** `offline_access` 범위가 요청된 경우에만 제공됩니다.|
| `id_token`      | 서명되지 않은 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |

#### <a name="error-response"></a>오류 응답

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 매개 변수         | Description                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다.           |
| `error_codes` |진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다. |
| `timestamp` | 오류가 발생한 시간입니다. |
| `trace_id` | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| `correlation_id` | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

오류 코드 및 권장되는 클라이언트 작업에 대한 설명은 [토큰 엔드포인트 오류에 대한 오류 코드](#error-codes-for-token-endpoint-errors)를 참조하세요.
