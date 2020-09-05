---
title: Microsoft ID 플랫폼 및 OAuth 2.0 인증 코드 흐름 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼에서 구현된 OAuth 2.0 인증 프로토콜을 사용하여 웹 애플리케이션을 빌드합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6648cfb717ade4b842e8ff470a46bf744b630363
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612319"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft ID 플랫폼 및 OAuth 2.0 인증 코드 흐름

OAuth 2.0 인증 코드 권한은 디바이스에 설치된 앱에서 사용하여 Web API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. Microsoft ID 플랫폼에서 구현된 OAuth 2.0을 사용하여, 로그인 및 모바일 및 API 액세스를 데스크톱 앱에 추가할 수 있습니다.

이 문서에서는 임의 언어를 사용하여 애플리케이션에서 프로토콜에 대해 직접 프로그래밍을 수행하는 방법을 설명합니다.  가능하면 [토큰을 획득하고 보안 Web API를 호출](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)하는 대신, 지원되는 MSAL(Microsoft 인증 라이브러리)을 사용하는 것이 좋습니다.  [MSAL을 사용하는 샘플 앱](sample-v2-code.md)도 살펴봅니다.

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](https://tools.ietf.org/html/rfc6749)에서 설명합니다. [단일 페이지 앱](v2-app-types.md#single-page-apps-javascript), [웹앱](v2-app-types.md#web-apps) 및 [기본적으로 설치된 앱](v2-app-types.md#mobile-and-native-apps)을 포함하여 대부분의 앱 형식에서 인증 및 권한 부여를 수행하는 데 사용됩니다. 이 흐름을 사용하면 앱이 Microsoft ID 플랫폼 엔드포인트에서 보호되는 리소스에 액세스하는 데 사용할 수 있는 access_token 뿐만 아니라 로그인한 사용자에 대한 추가 access_token 및 ID 토큰을 가져오기 위한 새로 고침 토큰도 안전하게 획득할 수 있습니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

높은 수준에서 애플리케이션에 대한 전체 인증 흐름은 다음과 같습니다.

![OAuth 인증 코드 흐름](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>단일 페이지 앱에 필요한 리디렉션 URI 설정

단일 페이지 애플리케이션에 대한 인증 코드 흐름에는 몇 가지 추가 설정이 필요합니다.  [단일 페이지 응용 프로그램을 만들기](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) 위한 지침에 따라 리디렉션 URI를 CORS에 대해 사용 하도록 설정 된 것으로 올바르게 표시 합니다. CORS를 사용 하도록 기존 리디렉션 URI를 업데이트 하려면 매니페스트 편집기를 열고 `type` 섹션에서 리디렉션 uri에 대 한 필드를로 설정 `spa` `replyUrlsWithType` 합니다. 인증 탭의 "웹" 섹션에서 리디렉션 URI를 클릭 하 고 권한 부여 코드 흐름을 사용 하 여 마이그레이션하려는 uri를 선택할 수도 있습니다.

`spa`리디렉션 형식이 암시적 흐름과 이전 버전과 호환 됩니다. 현재 암시적 흐름을 사용 하 여 토큰을 가져오는 앱은 `spa` 문제 없이 리디렉션 URI 형식으로 이동 하 고 암시적 흐름을 계속 사용할 수 있습니다.

인증 코드 흐름을 사용하려고 하면 다음 오류가 표시됩니다.

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

그런 다음, 앱 등록을 방문하고 앱의 리디렉션 URI를 `spa` 형식으로 업데이트해야 합니다.

## <a name="request-an-authorization-code"></a>인증 코드 요청

인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 엔드포인트로 보내는 것으로 시작됩니다. 이 요청에서 클라이언트는 사용자로부터 `openid`, `offline_access` 및 `https://graph.microsoft.com/mail.read ` 권한을 요청합니다.  예를 들어, `Directory.ReadWrite.All`을 사용하여 조직의 디렉터리에 데이터를 쓰는 것과 같은 일부 권한은 관리자가 제한합니다. 애플리케이션이 조직 사용자에게 이러한 사용 권한 중 하나에 대한 액세스를 요청하는 경우 사용자에게는 앱의 사용 권한에 동의할 권한이 부여되지 않음을 나타내는 오류 메시지가 표시됩니다. 관리자 제한 범위에 대한 액세스를 요청하려면 회사 관리자에게 직접 요청해야 합니다.  자세한 내용은 [관리자 제한 권한](v2-permissions-and-consent.md#admin-restricted-permissions)을 참조하세요.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> 이 요청을 실행하려면 아래 링크를 클릭하세요. 로그인하면 브라우저가 주소 표시줄에서 `code` 과 함께 `https://localhost/myapp/` 으로 리디렉션됩니다.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 매개 변수    | 필수/선택 | Description |
|--------------|-------------|--------------|
| `tenant`    | required    | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.  |
| `client_id`   | required    | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 앱에 할당한 **애플리케이션(클라이언트) ID**입니다.  |
| `response_type` | required    | 인증 코드 흐름에 대한 `code`를 포함해야 합니다.       |
| `redirect_uri`  | required | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. 네이티브 및 모바일 앱의 경우 `https://login.microsoftonline.com/common/oauth2/nativeclient`의 기본값을 사용해야 합니다.   |
| `scope`  | required    | 사용자가 동의하게 할 공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다.  요청의 `/authorize` 레그에서는 여러 리소스를 포함할 수 있으므로 앱은 사용자가 호출하려는 여러 웹 API에 대한 동의를 받을 수 있습니다. |
| `response_mode`   | 권장 | 결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 다음 중 하나일 수 있습니다.<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`는 리디렉션 URI에 코드를 쿼리 문자열 매개 변수로 제공합니다. 암시적 흐름을 사용하여 ID 토큰을 요청하는 경우 `query`는 [OpenID 사양](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)에서 명시한 대로 사용할 수 없습니다. 코드만 요청하는 경우 `query`, `fragment` 또는 `form_post`를 사용할 수 있습니다. `form_post`는 리디렉션 URI에 대한 코드가 포함된 POST를 실행합니다. 자세한 내용은 [OpenID Connect 프로토콜](../azuread-dev/v1-protocols-openid-connect-code.md)을 참조하세요.  |
| `state`                 | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 이 값은 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩할 수 있습니다. |
| `prompt`  | 선택 사항    | 필요한 사용자 상호 작용 유형을 나타냅니다. 이 경우 유효한 값은 `login`, `none` 및 `consent`뿐입니다.<br/><br/>- `prompt=login`은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다.<br/>- `prompt=none`은 그 반대로 사용자에게 어떠한 대화형 프롬프트도 표시되지 않도록 합니다. Single-Sign On을 통해 요청이 자동으로 완료될 수 없는 경우에 Microsoft ID 플랫폼 엔드포인트는 `interaction_required` 오류를 반환합니다.<br/>- `prompt=consent`는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다.<br/>- `prompt=select_account`는 세션의 모든 계정 또는 저장된 계정을 나열하는 계정 선택 환경이나 다른 계정을 모두 사용하도록 선택하는 옵션을 제공하면서 Single Sign-On을 중단시킵니다.<br/> |
| `login_hint`  | 선택 사항    | 사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다.   |
| `domain_hint`  | 선택 사항    | 포함된 경우, 로그인 페이지를 거친 사용자가 좀 더 효율적인 사용자 경험을 가질 수 있도록 이메일 기반 검색 프로세스를 건너뜁니다(예: 페더레이션 ID 공급자로 보내기). 앱이 이전 로그인 작업에서 `tid` 를 추출하여 재인증 과정에서 이 매개 변수를 종종 사용합니다. `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용해야 합니다. 그렇지 않으면 `domain_hint=organizations`를 사용합니다.  |
| `code_challenge`  | 권장/필수 | PKCE(Proof Key for Code Exchange)를 통해 권한 부여 코드를 보호하는 데 사용됩니다. `code_challenge_method`가 포함되면 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. 이제 모든 애플리케이션 유형(네이티브 앱, SPA 및 웹앱과 같은 기밀 클라이언트)에서 권장됩니다. |
| `code_challenge_method` | 권장/필수 | `code_challenge` 매개 변수에 대한 `code_verifier`를 인코딩하는 데 사용되는 메서드입니다. 이 *는* 여야 `S256` 하지만 `plain` 어떤 이유로 클라이언트가 SHA256을 지원할 수 없는 경우에는를 사용할 수 있습니다. <br/><br/>제외할 경우 `code_challenge`가 포함되면 `code_challenge`가 일반 텍스트로 간주됩니다. Microsoft ID 플랫폼은 `plain` 및 `S256`을 모두 지원합니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. [인증 코드 흐름을 사용하는 단일 페이지 앱](reference-third-party-cookies-spas.md)에 필요합니다.|


이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. Microsoft ID 플랫폼 엔드포인트는 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지도 확인합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](v2-permissions-and-consent.md).

사용자가 인증하고 동의하면 Microsoft ID 플랫폼 엔드포인트가 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다.

#### <a name="successful-response"></a>성공적인 응답

`response_mode=query` 를 사용한 성공적인 응답은 다음과 같습니다.

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 매개 변수 | Description  |
|-----------|--------------|
| `code` | 앱이 요청한 authorization_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. authorization_code는 수명이 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| `state` | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

또한 액세스 토큰 및 ID 토큰을 요청하며, 애플리케이션 등록에서 암시적 부여를 사용하도록 설정한 경우 이러한 액세스 토큰 및 ID 토큰을 수신할 수 있습니다.  이것을 경우에 따라 "하이브리드 흐름"이라고도 하며 ASP.NET과 같은 프레임워크에서 사용됩니다.

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```HTTP
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
| `unauthorized_client` | 클라이언트 애플리케이션이 인증 코드를 요청할 수 없습니다. | 이 오류는 일반적으로 클라이언트 애플리케이션이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `access_denied`  | 리소스 소유자가 동의 거부  | 클라이언트 애플리케이션이 사용자의 동의를 받지 않으면 계속 진행할 수 없다고 알릴 수 있습니다. |
| `unsupported_response_type` | 권한 부여 서버가 요청에 해당 응답 형식을 지원하지 않습니다. | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다.  |
| `server_error`  | 서버에 예기치 않은 오류가 발생했습니다.| 요청을 다시 시도하십시오. 이러한 오류는 일시적인 상태 때문에 발생할 수 있습니다. 클라이언트 애플리케이션이 일시적 오류 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| `temporarily_unavailable`   | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 요청을 다시 시도하십시오. 클라이언트 애플리케이션이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| `invalid_resource`  | 대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. | 이 오류는 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `login_required` | 사용자가 너무 많거나 없습니다. | 클라이언트에서 자동 인증(`prompt=none`)을 요청했지만 단일 사용자를 찾을 수 없습니다. 이는 세션에서 여러 사용자가 활성 상태이거나 사용자가 없음을 의미할 수 있습니다. 이 경우 선택한 테넌트를 고려합니다. 예를 들어 두 개의 Azure AD 계정이 활성 상태이고 하나의 Microsoft 계정이 있고 `consumers`가 선택되면 자동 인증이 작동합니다. |
| `interaction_required` | 요청을 위해 사용자 상호 작용이 필요합니다. | 추가 인증 단계 또는 동의가 필요합니다. `prompt=none`을 사용하지 않고 요청을 다시 시도하세요. |

## <a name="request-an-access-token"></a>액세스 토큰 요청

authorization_code를 획득하고 사용자가 사용 권한을 부여했으므로 `code`를 원하는 리소스에 대한 `access_token`으로 교환할 수 있습니다. 이렇게 하려면 `/token` 엔드포인트에 `POST` 요청을 보내면 됩니다.

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Postman에서 이 요청을 실행해 보세요. (`code`를 바꾸지 않아야 함)[![Postman에서 이 요청을 실행해 보세요.](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| 매개 변수  | 필수/선택 | Description     |
|------------|-------------------|----------------|
| `tenant`   | required   | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.  |
| `client_id` | required  | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당한 애플리케이션(클라이언트) ID입니다. |
| `grant_type` | required   | 인증 코드 흐름에 대한 `authorization_code` 여야 합니다.   |
| `scope`      | 선택적   | 공백으로 구분된 범위 목록입니다. 이 범위는 OIDC 범위(`profile`, `openid`, `email`)와 마찬가지로 모두 단일 리소스에 속해야 합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요. 이는 인증 코드 흐름에 대 한 Microsoft 확장으로, 앱이 토큰 상환 중에 토큰을 원하는 리소스를 선언할 수 있도록 하기 위한 것입니다.|
| `code`          | required  | 흐름의 첫 번째 레그에서 얻은 authorization_code입니다. |
| `redirect_uri`  | required  | authorization_code를 획득하는 데 사용된 값과 동일한 redirect_uri 값입니다. |
| `client_secret` | 기밀 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 애플리케이션 암호입니다. 디바이스 또는 웹 페이지에 client_secrets를 안정적으로 저장할 수 없기 때문에 네이티브 앱 또는 단일 페이지 앱에서 애플리케이션 암호를 사용하면 안 됩니다. 서버 쪽에서 client_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다.  클라이언트 암호는 전송되기 전에 URL로 인코딩되어야 합니다. URI 인코딩에 대한 자세한 내용은 [URI 일반 구문 사양](https://tools.ietf.org/html/rfc3986#page-12)을 참조하세요. |
| `code_verifier` | 권장  | authorization_code를 얻는 데 사용된 동일한 code_verifier입니다. 인증 코드 부여 요청에 PKCE가 사용된 경우에는 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |

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
| `scope`         | access_token이 유효한 범위입니다. 선택 사항-표준이 아닙니다. 생략 하면 흐름의 초기 레그에서 요청 된 범위에 대 한 토큰이 됩니다. |
| `refresh_token` | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 액세스 토큰이 만료된 후 이 토큰을 사용하여 추가 액세스 토큰을 획득할 수 있습니다. refresh_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 액세스 토큰 새로 고침에 대한 자세한 내용은 [아래 섹션](#refresh-the-access-token)을 참조하세요. <br> **참고:** `offline_access` 범위가 요청된 경우에만 제공됩니다. |
| `id_token`      | JWT(JSON Web Token) 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시 하 고 표시할 수 있으며, 기밀 클라이언트는 권한 부여에이를 사용할 수 있습니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |

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
| `invalid_request`  | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청 또는 앱 등록을 수정하고 요청을 다시 제출합니다.   |
| `invalid_grant`    | 권한 부여 코드 또는 PKCE 코드 확인자가 잘못되었거나 만료되었습니다. | `/authorize` 엔드포인트에 대한 새 요청을 시도하고 code_verifier 매개 변수가 잘못되었는지 확인합니다.  |
| `unauthorized_client` | 인증된 클라이언트가 이 권한 부여 유형을 사용할 권한이 없습니다. | 이 오류는 일반적으로 클라이언트 애플리케이션이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| `invalid_client` | 클라이언트 인증에 실패했습니다.  | 클라이언트 자격 증명이 잘못되었습니다. 해결하려면 애플리케이션 관리자가 자격 증명을 업데이트합니다.   |
| `unsupported_grant_type` | 권한 부여 서버가 해당 권한 부여 유형을 지원하지 않습니다. | 요청에서 권한 부여 유형을 변경하십시오. 이 유형의 오류는 개발 중에만 발생하며 초기 테스트 중에 검색됩니다. |
| `invalid_resource` | 대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. | 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다.  |
| `interaction_required` | 비 비표준. OIDC 사양은 끝점 에서만이를 호출 합니다 `/authorize` . 요청에는 사용자 조작이 필요 합니다. 예를 들어 추가 인증 단계가 필요합니다. | 동일한 범위를 사용 하 여 요청을 다시 시도 하세요 `/authorize` . |
| `temporarily_unavailable` | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 약간의 지연 후 요청을 다시 시도 합니다. 클라이언트 애플리케이션이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
|`consent_required` | 요청에는 사용자 동의가 필요 합니다. 이 오류는 일반적으로 OIDC 사양에 따라 끝점 에서만 반환 되므로 비표준입니다 `/authorize` . `scope`클라이언트 앱에 요청할 권한이 없는 코드 상환 흐름에서 매개 변수가 사용 된 경우 반환 됩니다.  | 클라이언트는 `/authorize` 동의를 트리거하기 위해 올바른 범위의 끝점으로 사용자를 다시 보내야 합니다. |
|`invalid_scope` | 앱에서 요청 된 범위가 잘못 되었습니다.  | 인증 요청에서 범위 매개 변수 값을 유효한 값으로 업데이트 합니다. |

> [!NOTE]
> 단일 페이지 앱에는 '단일 페이지 애플리케이션' 클라이언트 유형에 대해서만 원본 간 토큰 상환이 허용됨을 나타내는 `invalid_request` 오류가 표시될 수 있습니다.  이것은 토큰을 요청하는 데 사용되는 리디렉션 URI가 `spa` 리디렉션 URI로 표시되지 않았음을 나타냅니다.  이 흐름을 사용하도록 설정하는 방법에 대해서는 [애플리케이션 등록 단계](#redirect-uri-setup-required-for-single-page-apps)를 검토합니다.

## <a name="use-the-access-token"></a>액세스 토큰 사용

`access_token`을 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 Web API에 대한 요청에 토큰을 사용할 수 있습니다.

> [!TIP]
> Postman에서 이 요청을 실행하세요. (`Authorization` 헤더를 먼저 바꿈)[![Postman에서 이 요청을 실행해 보세요.](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>액세스 토큰 새로 고침

access_token은 수명이 짧으며, 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 이렇게 하려면 다른 `POST` 요청을 `/token` 엔드포인트에 제출해야 하며, 이번에는 `code` 대신 `refresh_token`을 제공해야 합니다.  새로 고침 토큰은 클라이언트가 이미 동의를 받은 모든 권한에 유효합니다. 따라서 `scope=mail.read`에 대한 요청에서 발행된 새로 고침 토큰을 사용하여 `scope=api://contoso.com/api/UseResource`에 대한 새 액세스 토큰을 요청할 수 있습니다.

웹앱 및 네이티브 앱에 대한 새로 고침 토큰에는 지정된 수명이 없습니다. 일반적으로 새로 고침 토큰의 수명은 비교적 깁니다. 그러나 새로 고침 토큰이 만료되거나 해지되거나 원하는 작업을 위한 충분한 권한이 없는 경우가 있습니다. 애플리케이션은 [토큰 발급 엔드포인트에서 반환하는 오류](#error-codes-for-token-endpoint-errors)를 예상하고 정확히 처리해야 합니다. 그러나 단일 페이지 앱은 수명이 24시간인 토큰을 가져와서 매일 새 인증을 요구합니다.  타사 쿠키를 사용하도록 설정하면 iframe에서 이 작업을 자동으로 수행할 수 있지만 Safari와 같이 타사 쿠키를 사용하지 않는 브라우저에서는 최상위 수준 프레임(전체 페이지 탐색 또는 팝업)에서 수행해야 합니다.

새로 고침 토큰은 새 액세스 토큰을 획득하는 데 사용될 경우 해지되지 않지만 이전 새로 고침 토큰은 삭제해야 합니다. [OAuth 2.0 사양](https://tools.ietf.org/html/rfc6749#section-6)에는 다음과 같이 명시되어 있습니다. "권한 부여 서버는 새 새로 고침 토큰을 발급할 수 있지만 이 경우 클라이언트는 이전 새로 고침 토큰을 삭제하고 새 새로 고침 토큰으로 바꾸어야 합니다. 권한 부여 서버는 클라이언트에 새 새로 고침 토큰을 발급한 후 이전 새로 고침 토큰을 해지할 수 있습니다."

>[!IMPORTANT]
> `spa`로 등록된 리디렉션 URI로 전송되는 새로 고침 토큰의 경우 24시간 후에 만료됩니다. 초기 새로 고침 토큰을 사용하여 획득한 추가 새로 고침 토큰은 해당 만료 시간까지 계속 사용되므로 앱은 대화형 인증을 사용하여 인증 코드 흐름을 다시 실행함으로써 24시간마다 새 새로 고침 토큰을 가져올 수 있도록 준비해야 합니다. 사용자는 자격 증명을 입력할 필요가 없고 일반적으로 UX도 표시되지 않으며 애플리케이션이 다시 로드되기만 합니다. 그렇지만 브라우저에서 로그인 세션을 보려면 최상위 프레임의 로그인 페이지를 방문해야 합니다.  이것은 [타사 쿠키를 차단하는 브라우저의 개인 정보 보호 기능](reference-third-party-cookies-spas.md) 때문입니다.

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Postman에서 이 요청을 실행해 보세요. (`refresh_token`를 바꾸지 않아야 함)[![Postman에서 이 요청을 실행해 보세요.](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| 매개 변수     | Type           | Description        |
|---------------|----------------|--------------------|
| `tenant`        | required     | 요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요.   |
| `client_id`     | required    | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 앱에 할당한 **애플리케이션(클라이언트) ID**입니다. |
| `grant_type`    | required    | 이 인증 코드 흐름 범례에 대한 `refresh_token` 이어야 합니다. |
| `scope`         | required    | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 원래 authorization_code 요청 레그에서 요청된 범위와 동일하거나 하위 집합이어야 합니다. 이 요청에 지정된 범위가 여러 리소스 서버에 걸쳐 있는 경우 Microsoft ID 플랫폼 엔드포인트는 첫 번째 범위에 지정된 리소스에 대한 토큰을 반환합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](v2-permissions-and-consent.md)를 참조하세요. |
| `refresh_token` | required    | 흐름의 두 번째 레그에서 얻은 refresh_token입니다. |
| `client_secret` | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 애플리케이션 암호입니다. 디바이스에 client_secret을 안정적으로 저장할 수 없으므로 네이티브 앱에서는 사용하면 안 됩니다. 서버 쪽에서 client_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다. 이 암호는 URL로 인코딩해야 합니다. 자세한 내용은 [URI 일반 구문 사양](https://tools.ietf.org/html/rfc3986#page-12)을 참조하세요. |

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
