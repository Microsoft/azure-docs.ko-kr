---
title: Microsoft id 플랫폼 암시적 흐름을 사용 하 여 단일 페이지 응용 프로그램 보안 | Azure
description: Microsoft id 플랫폼 구현을 암시적 흐름을 사용 하 여 단일 페이지 앱에 대 한 구성 웹 응용 프로그램.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d517828b30629cd9dfba5459b1d90913d8bc4f77
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112151"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft id 플랫폼 및 암시적 권한 부여 흐름

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft id 플랫폼 끝점을 사용 하 여 사용자를 모두 개인 및 회사 또는 학교 Microsoft 계정 사용 하 여 단일 페이지 앱에 서명할 수 있습니다. 주로 브라우저에서 실행되는 단일 페이지 앱 및 기타 JavaScript 앱에는 인증과 관련하여 해결해야 하는 몇 가지 문제가 있습니다.

* 이러한 앱의 보안 특성은 기존의 서버 기반 웹 애플리케이션과 상당히 다릅니다.
* 대다수 권한 부여 서버 및 ID 공급자는 CORS 요청을 지원하지 않습니다.
* 앱에서 멀어지는 전체 페이지 브라우저 리디렉션은 사용자 환경에 특히 방해가 됩니다.

이러한 응용 프로그램 (예: AngularJS, Ember.js, React.js 및 등)에 대 한 Microsoft id 플랫폼 OAuth 2.0 암시적 허용 흐름을 지원합니다. 암시적 흐름은 [OAuth 2.0 사양(영문)](https://tools.ietf.org/html/rfc6749#section-4.2)에 설명되어 있습니다. 주요 이점은 앱에서에서 토큰을 가져오는 Microsoft id 플랫폼 백 엔드 서버를 수행 하지 않고 자격 증명 교환을 수 있다는 점입니다. 따라서 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있습니다. 암시적 흐름을 사용하는 경우 보안과 관련된 몇 가지 중요 사항(특히 [클라이언트](https://tools.ietf.org/html/rfc6749#section-10.3) 및 [사용자 가장](https://tools.ietf.org/html/rfc6749#section-10.3) 관련 사항)을 고려해야 합니다.

암시적 흐름 및 Microsoft id 플랫폼을 사용 하 여 JavaScript 앱에 인증 추가 하려는 경우 오픈 소스 JavaScript 라이브러리를 사용 하는 것이 좋습니다 [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)합니다.

하지만 단일 페이지 앱에서 라이브러리를 사용하지 않고 직접 프로토콜 메시지를 보내려는 경우에는 아래의 일반적인 단계를 따릅니다.

> [!NOTE]
> 일부 Azure Active Directory (Azure AD) 시나리오 및 기능 Microsoft id 플랫폼 끝점에서 지원 됩니다. Microsoft id 플랫폼 끝점을 사용 해야 하는 경우를 확인 하려면에 대해 알아보세요 [Microsoft identity 플랫폼 제한](active-directory-v2-limitations.md)합니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

아래 다이어그램에는 암시적 로그인 흐름의 전체적인 형태가 나와 있습니다. 다이어그램 아래의 섹션에서는 각 단계에 대해 더 자세히 설명합니다.

![OpenID Connect 스윔 레인](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>로그인 요청 보내기

처음에 사용자 앱에 로그인 하려면 보낼 수 있습니다는 [OpenID Connect](v2-protocols-oidc.md) 인증 요청 및 가져오기는 `id_token` Microsoft id 플랫폼 끝점에서.

> [!IMPORTANT]
> 성공적으로 ID 토큰을 앱 등록을 요청 합니다 [Azure portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지에는 암시적 허용 흐름을 선택 하 여 올바르게 사용할 수 있어야 합니다. **액세스 토큰을** 및 **ID 토큰** 아래의 합니다 **암시적 권한 부여** 섹션입니다. 사용 하지 않는 경우는 `unsupported_response` 오류가 반환 됩니다. **입력 매개 변수 'response_type'에 대해 제공된 값은 이 클라이언트에 대해 허용되지 않습니다. 필요한 값은 'code'입니다.** 입니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 암시적 흐름을 사용하여 로그인을 테스트하려면 <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize..를 클릭합니다.</a> 로그인하면 브라우저가 주소 표시줄에서 `id_token`과 함께 `https://localhost/myapp/`으로 리디렉션됩니다.
>

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| `tenant` | 필수 |요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| `client_id` | 필수 | 응용 프로그램 (클라이언트) ID입니다 합니다 [Azure portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지 앱에 할당 합니다. |
| `response_type` | 필수 |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다. response_type `token`을 포함할 수도 있습니다. `token` 을 여기서 사용하면 앱은 권한 부여 엔드포인트에 두 번째 요청을 수행하지 않고 권한 부여 엔드포인트에서 즉시 액세스 토큰을 받을 수 있습니다. `token` response_type을 사용하는 경우 `scope` 매개 변수는 토큰을 발행할 리소스를 나타내는 범위를 포함해야 합니다. |
| `redirect_uri` | 권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. |
| `scope` | 필수 |공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다. OpenID Connect의 경우 동의 UI에서 "로그인" 권한으로 해석되는 `openid`범위가 포함되어야 합니다. 필요에 따라 추가 사용자 데이터에 액세스하기 위해 `email` 또는 `profile` 범위를 포함할 수도 있습니다. 다양한 리소스에 대한 동의를 요청하기 위해 이 요청에 다른 범위를 포함할 수도 있습니다. |
| `response_mode` | 선택 사항 |결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 기본적으로 액세스 토큰을 쿼리하지만 요청에 id_token이 포함된 경우에는 조각화됩니다. |
| `state` | 권장 |토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| `nonce` | 필수 |결과 id_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. id_token 요청 시에만 필수입니다. |
| `prompt` | 선택 사항 |필요한 사용자 상호 작용 유형을 나타냅니다. 현재 유효한 값은 'login', 'none', 'select_account', 'consent'뿐입니다. `prompt=login` 은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다. `prompt=none` -반대 되는 사용자가 어떠한 대화형 프롬프트도 표시 되지 않도록 합니다. Single sign-on을 통해 요청을 자동으로 완료할 수 없습니다, 하는 경우 Microsoft id 플랫폼 끝점 오류가 반환 됩니다. `prompt=select_account`는 세션에 저장된 모든 계정이 표시되는 계정 선택기로 사용자를 전송합니다. `prompt=consent` 는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| `login_hint`  |선택 사항 |사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다.|
| `domain_hint` | 선택 사항 |`consumers` 또는 `organizations` 중 하나일 수 있습니다. 전자 메일 기반 검색 프로세스를 건너뜁니다 고 포함 하는 경우 해당 사용자 거친 로그인 페이지에는 좀 더 효율적인된 사용자 경험을 합니다. 앱이 id_token에서 `tid` 클레임을 추출하여 재인증 과정에서 이 매개 변수를 종종 사용합니다. `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`(Microsoft 계정 소비자 테넌트)인 경우 `domain_hint=consumers`를 사용해야 합니다. 그렇지 않으면 재인증 중에 `domain_hint=organizations`를 사용할 수 있습니다. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. Microsoft id 플랫폼 끝점에서 표시 된 사용 권한에 사용자가 동의 했음을 확인 합니다는 `scope` 쿼리 매개 변수입니다. 사용자가 해당 권한 중 어떤 항목에도 동의하지 **않은** 경우에는 필요한 권한에 동의하라는 메시지가 표시됩니다. 자세한 내용은. [권한, 동의 및 다중 테넌트 앱](v2-permissions-and-consent.md)을 참조하세요.

Microsoft id 플랫폼 끝점에서 표시 된 앱에 대 한 응답을 반환 합니다 사용자를 인증 하 고 동의 `redirect_uri`에 지정 된 메서드를 사용 하는 `response_mode` 매개 변수입니다.

#### <a name="successful-response"></a>성공적인 응답

`response_mode=fragment` 및 `response_type=id_token+token` 사용 시의 정상 응답은 다음과 같습니다. 쉽게 읽을 수 있도록 아래 예제에는 줄 바꿈이 적용되어 있습니다.

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 매개 변수 | 설명 |
| --- | --- |
| `access_token` |`response_type`이 `token`을 포함하는 경우 포함됩니다. Microsoft Graph의 경우 앱에서 요청한 액세스 토큰입니다. 액세스 토큰을 디코딩할 또는 기타 방식으로 검사 하지 않아야, 불투명 문자열로 간주 해야 합니다. |
| `token_type` |`response_type`이 `token`을 포함하는 경우 포함됩니다. 항상 `Bearer`입니다. |
| `expires_in`|`response_type`이 `token`을 포함하는 경우 포함됩니다. 캐싱 목적으로 토큰이 유효한 시간(초)을 나타냅니다. |
| `scope` |`response_type`이 `token`을 포함하는 경우 포함됩니다. access_token이 유효한 범위를 나타냅니다. 모두 포함 되지 않을, 요청 된 범위 (의 경우 Azure AD 전용 범위 개인 계정을 사용 하는 로그인 할 때 요청 된) 사용자에 게 적용 하지 않은 경우. |
| `id_token` | 서명된 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시 하 고 표시할 수 있습니다 하지만 권한 부여 또는 보안 경계에 대 한에 의존 하지 않아야 합니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |
| `state` |요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| --- | --- |
| `error` |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="validate-the-idtoken"></a>id_token 유효성 검사

사용자를 인증 하는 데 충분 하지 않아 id_token을 받는 것 만으로는 또한 id_token의 서명 유효성을 검사 하 고 앱의 요구 사항에 따라 토큰의 클레임을 확인 해야 합니다. Microsoft id 플랫폼 끝점 사용 [Jwt (JSON 웹 토큰)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화 토큰을 서명 하 고 유효한 것을 확인 합니다.

클라이언트 코드에서 `id_token`의 유효성을 검사하도록 선택할 수 있지만, 일반적으로 `id_token`을 백 엔드 서버에 보내서 그 곳에서 유효성 검사를 수행합니다. Id_token의 서명의 유효성을 검사 한 후 몇 개 클레임을 확인 하는 데 필요한 수 있습니다. [토큰 유효성 검사](id-tokens.md#validating-an-id_token) 및 [서명 키 롤오버에 대한 중요한 정보](active-directory-signing-key-rollover.md)를 포함한 자세한 내용은 [`id_token` 참조](id-tokens.md)를 확인하세요. 대부분의 언어 및 플랫폼에서 사용할 수 있는 하나 이상의 토큰의 구문 분석 및 유효성 검사에 대한 라이브러리를 사용하는 것이 좋습니다.

시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

* 사용자/조직이 앱에 등록했는지 확인
* 사용자에게 적절한 권한이 부여되었으며 필요한 권한이 있는지 확인
* 다단계 인증과 같은 특정 강도의 인증이 발생했는지 확인

Id_token 유효성을 검사 한 후에 사용자를 사용 하 여 세션을 시작 하 고 id_token의 클레임을 사용 하 여 앱에서 사용자에 대 한 정보를 얻을 수 있습니다. 표시, 레코드, 개인 설정 등에 대 한이 정보를 사용할 수 있습니다.

## <a name="get-access-tokens"></a>액세스 토큰 가져오기

Web Api와 같은 Microsoft id 플랫폼에서 보호를 호출에 대 한 액세스 토큰을 가져올 수는 단일 페이지 앱에 사용자를 등록 했다면, 이제는 [Microsoft Graph](https://developer.microsoft.com/graph)합니다. `token` response_type을 사용하여 토큰을 이미 받았더라도 이 방법을 사용하여 사용자가 다시 로그인하도록 리디렉션하지 않고 추가 리소스에 대한 토큰을 얻을 수 있습니다.

일반 OpenID Connect/OAuth 흐름에서 수행한이 Microsoft id 플랫폼에는 요청을 수행 하 여 `/token` 끝점입니다. 그러나 Microsoft id 플랫폼 끝점 이므로 AJAX 호출을 가져오고 새로 고침 토큰을 통해 질문에서 CORS 요청을 지원 하지 않습니다. 그 대신, 숨겨진 iFrame에 암시적 흐름을 사용하여 다른 웹 API에 대한 새 토큰을 가져올 수 있습니다. 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

URL의 쿼리 매개 변수에 대한 자세한 내용은 [로그인 요청 보내기](#send-the-sign-in-request)를 참조하세요.

> [!TIP]
> 아래 요청을 브라우저 탭에 복사하여 붙여 넣으세요! (`login_hint` 값을 사용자에 대한 올바른 값으로 바꾸는 것을 잊지 마세요)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

`prompt=none` 매개 변수로 인해, 이 요청은 즉시 성공하거나 실패하고 애플리케이션에 반환됩니다. 성공적인 응답은 `response_mode` 매개 변수에 지정된 메서드를 사용하여 지정된 `redirect_uri`의 앱으로 전송됩니다.

#### <a name="successful-response"></a>성공적인 응답

`response_mode=fragment` 를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| 매개 변수 | 설명 |
| --- | --- |
| `access_token` |`response_type`이 `token`을 포함하는 경우 포함됩니다. Microsoft Graph의 경우 앱에서 요청한 액세스 토큰입니다. 액세스 토큰을 디코딩할 또는 기타 방식으로 검사 하지 않아야, 불투명 문자열로 간주 해야 합니다. |
| `token_type` | 항상 `Bearer`입니다. |
| `expires_in` | 캐싱 목적으로 토큰이 유효한 시간(초)을 나타냅니다. |
| `scope` | access_token이 유효한 범위를 나타냅니다. 모두 포함 되지 않을, 요청 된 범위 (의 경우 Azure AD 전용 범위 개인 계정을 사용 하는 로그인 할 때 요청 된) 사용자에 게 적용 하지 않은 경우. |
| `id_token` | 서명된 JWT(JSON 웹 토큰)입니다. `response_type`이 `id_token`을 포함하는 경우 포함됩니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시 하 고 표시할 수 있습니다 하지만 권한 부여 또는 보안 경계에 대 한에 의존 하지 않아야 합니다. id_tokens에 대한 자세한 내용은 [`id_token` 참조](id-tokens.md)를 확인하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |
| `state` |요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다. `prompt=none`의 경우, 예상되는 오류는 다음과 같습니다.

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 매개 변수 | 설명 |
| --- | --- |
| `error` |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

iFrame 요청에 이러한 오류를 수신하면, 사용자는 새 토큰을 얻기 위해 대화형으로 다시 로그인해야 합니다. 어떠한 방식이든 애플리케이션에 적합한 방식으로 이러한 경우를 처리하도록 선택할 수 있습니다.

## <a name="validating-access-tokens"></a>액세스 토큰의 유효성 검사

access_token를 받으면 다음 클레임뿐 아니라 토큰 서명의 유효성을 검사해야 합니다. 시나리오에 따라 추가 클레임의 유효성을 검사하도록 선택할 수도 있습니다.

* **대상** 클레임 - 앱에 제공하려고 의도했던 토큰이 맞는지 확인합니다.
* **발급자** 클레임-Microsoft id 플랫폼 끝점에서 앱에 토큰을 발급 했는지 확인
* **이전이 아님** 및 **만료 시간** 클레임 - 토큰이 만료되지 않았는지 확인합니다.

액세스 토큰에 있는 클레임에 대한 자세한 내용은 [액세스 토큰 참조](access-tokens.md)를 확인하세요.

## <a name="refreshing-tokens"></a>토큰 새로 고침

암시적 권한 부여는 새로 고침 토큰을 제공하지 않습니다. `id_token`과 `access_token`은 모두 짧은 시간 안에 만료되기 때문에 앱은 주기적으로 토큰을 새로 고치도록 준비가 되어야 합니다. 두 가지 형식의 토큰을 새로 고치려면를 사용 하 여 위와 동일한 숨겨진된 iframe 요청을 수행할 수 있습니다는 `prompt=none` 매개 변수는 id 플랫폼의 동작을 제어 합니다. 새 `id_token`을 받으려면 `response_type=id_token` 및 `scope=openid`는 물론 `nonce` 매개 변수를 사용해야 합니다.

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기

OpenID Connect `end_session_endpoint` 앱 사용자의 세션을 종료 하 여 Microsoft id 플랫폼 끝점에서 설정한 쿠키를 지울 Microsoft id 플랫폼 끝점에 요청을 보낼 수 있습니다. 앱은 웹 애플리케이션에서 특정 사용자를 완전히 로그아웃시키기 위해 일반적으로 토큰 캐시를 지우거나 쿠키를 삭제하여 고유한 사용자 세션을 종료한 다음, 브라우저를 아래 주소로 리디렉션합니다.

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 매개 변수 |  | 설명 |
| --- | --- | --- |
| `tenant` |필수 |요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| `post_logout_redirect_uri` | 권장 | 로그아웃이 완료된 후 사용자가 반환되어야 하는 URL입니다. 이 값은 애플리케이션에 대해 등록된 리디렉션 URI 중 하나와 일치해야 합니다. 포함 되지 않은 경우 사용자 Microsoft id 플랫폼 끝점에서 일반 메시지를 표시 합니다. |

## <a name="next-steps"></a>다음 단계

* [MSAL JS 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)을 검토하여 코딩을 시작합니다.
