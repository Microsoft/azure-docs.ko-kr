---
title: 암시적 흐름을 사용 하는 단일 페이지 로그인
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 OAuth 2.0 암시적 흐름을 사용 하 여 단일 페이지 로그인을 추가 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: efef55ab42313854ab27d323824a76488d520ad1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847382"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 OAuth 2.0 암시적 흐름을 사용 하 여 단일 페이지 로그인

많은 최신 응용 프로그램에는 주로 JavaScript로 작성 된 단일 페이지 앱 프런트 엔드가 있습니다. 앱은 종종 반응, 각도 또는 Vue와 같은 프레임 워크를 사용 하 여 작성 됩니다. 주로 브라우저에서 실행되는 단일 페이지 앱 및 기타 JavaScript 앱에는 인증에 대한 몇 가지 추가 과제가 있습니다.

- 이러한 앱의 보안 특성은 기존의 서버 기반 웹 응용 프로그램과는 다릅니다.
- 많은 권한 부여 서버 및 ID 공급자에서 CORS(원본 간 리소스 공유) 요청을 지원하지 않습니다.
- 응용 프로그램에서 전체 페이지 브라우저 리디렉션은 사용자 환경에 침입 될 수 있습니다.

이러한 애플리케이션을 지원하기 위해 Azure AD B2C(Azure Active Directory B2C)에서는 OAuth 2.0 암시적 흐름을 사용합니다. OAuth 2.0 암시적 권한 부여 흐름은 [OAuth 2.0 사양의 4.2 섹션](https://tools.ietf.org/html/rfc6749)(영문)에서 설명하고 있습니다. 암시적 흐름에서 앱은 서버 간 교환 없이 Azure AD(Azure Active Directory) 권한 부여 엔드포인트에서 직접 토큰을 받습니다. 모든 인증 논리 및 세션 처리는 페이지 리디렉션 또는 팝업 상자를 사용 하 여 JavaScript 클라이언트에서 전적으로 수행 됩니다.

Azure AD B2C는 표준 OAuth 2.0 암시적 흐름을 확장하여 단순한 인증 및 권한 부여보다 더 많은 작업을 수행합니다. Azure AD B2C는 [정책 매개 변수](user-flow-overview.md)를 도입했습니다. 정책 매개 변수를 사용하면 OAuth 2.0을 통해 가입, 로그인 및 프로필 관리 사용자 흐름과 같은 정책을 앱에 추가할 수 있습니다. 이 문서의 예제 HTTP 요청에서 **{테넌트}. onmicrosoft .com** 이 예로 사용 되었습니다. `{tenant}`은 테넌트의 이름으로 바꾸고 사용자 흐름을 만든 경우에는 해당 테넌트의 이름으로 바꿉니다.

암시적 로그인 흐름은 다음 그림과 비슷합니다. 각 단계는 이 문서의 뒷부분에서 자세히 설명합니다.

![스윔 레인-Openid connect Connect 암시적 흐름을 보여 주는 스타일 다이어그램](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>인증 요청 보내기

웹 응용 프로그램이 사용자를 인증 하 고 사용자 흐름을 실행 해야 하는 경우 사용자를 `/authorize` 엔드포인트으로 보낼 수 있습니다. 사용자가 사용자 흐름에 따라 동작을 수행 합니다.

이 요청에서 클라이언트는 `scope` 매개 변수에서 사용자 로부터 얻어야 하는 사용 권한 및 사용자의 실행 흐름을 표시 합니다. 요청이 어떻게 작동 하는지 확인 하려면 브라우저에 요청을 붙여넣은 후 실행 해 보세요. `{tenant}`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`를 테넌트에 이전에 등록 한 응용 프로그램의 앱 ID로 바꿉니다. `{policy}`를 테넌트에서 만든 정책 이름으로 바꿉니다 (예: `b2c_1_sign_in`.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
|넌| 예 | Azure AD B2C 테넌트의 이름|
|policy| 예| 실행할 사용자 흐름입니다. Azure AD B2C 테넌트에서 만든 사용자 흐름의 이름을 지정 합니다. 예: `b2c_1_sign_in`, `b2c_1_sign_up`또는 `b2c_1_edit_profile`. |
| client_id | 예 | [Azure Portal](https://portal.azure.com/) 응용 프로그램에 할당 된 응용 프로그램 ID입니다. |
| response_type | 예 | OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다. `token` 응답 형식이 포함될 수도 있습니다. `token`을 사용하면 앱에서 권한 부여 엔드포인트에 대한 두 번째 요청을 수행하지 않고도 권한 부여 엔드포인트에서 액세스 토큰을 즉시 받을 수 있습니다.  `token` 응답 형식을 사용하는 경우 `scope` 매개 변수에는 토큰을 발급할 리소스를 나타내는 범위가 포함되어야 합니다. |
| redirect_uri | 아닙니다. | 앱이 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| response_mode | 아닙니다. | 결과 토큰을 앱으로 다시 보내는 데 사용할 메서드를 지정합니다.  암시적 흐름의 경우 `fragment`를 사용합니다. |
| scope | 예 | 공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. `offline_access` 범위는 웹앱에 대한 선택 사항입니다. 리소스에 장기간 액세스하기 위한 새로 고침 토큰이 앱에 필요함을 나타냅니다. |
| state | 아닙니다. | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 사용하려는 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 state(상태)는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지)를 인코딩하는 데에도 사용됩니다. |
| nonce | 예 | 앱에서 생성한 요청에 포함된 값이며, 결과 ID 토큰에 클레임으로 포함됩니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| prompt | 아닙니다. | 필요한 사용자 상호 작용의 형식입니다. 현재 유효한 값은 `login`뿐이며, 이 매개 변수는 사용자가 해당 요청에 대한 자격 증명을 입력 하도록 합니다. Single sign-on은 적용 되지 않습니다. |

이 시점에서 정책의 워크플로를 완료하도록 사용자에게 요청합니다. 사용자는 사용자 이름 및 암호를 입력 하거나, 소셜 id로 로그인 하거나, 디렉터리에 등록 하거나, 다른 단계를 수행 해야 할 수 있습니다. 사용자 작업은 사용자 흐름을 정의한 방식에 따라 다릅니다.

사용자가 사용자 흐름을 완료하면 Azure AD에서 앱에 대한 응답을 `redirect_uri`에 사용한 값으로 반환합니다. `response_mode` 매개 변수에 지정된 메서드를 사용합니다. 실행된 사용자 흐름과 관계 없이 사용자 작업 시나리오 각각에 대한 응답은 정확히 동일합니다.

### <a name="successful-response"></a>성공적인 응답
`response_mode=fragment` 및 `response_type=id_token+token`을 사용하는 성공적인 응답은 다음과 같으며, 쉽게 읽을 수 있도록 줄 바꿈이 적용되었습니다.

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| 매개 변수 | Description |
| --------- | ----------- |
| access_token | 앱에서 요청한 액세스 토큰입니다. |
| token_type | 토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| expires_in | 액세스 토큰이 유효한 시간(초)입니다. |
| scope | 토큰이 유효한 범위입니다. 또한 나중에 사용할 수 있도록 범위를 사용하여 토큰을 캐시할 수도 있습니다. |
| id_token | 앱이 요청한 ID 토큰입니다. ID 토큰을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. ID 토큰 및 해당 내용에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](tokens-overview.md)를 참조하세요. |
| state | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |

### <a name="error-response"></a>오류 응답
오류 응답은 앱에서 적절히 처리할 수 있도록 리디렉션 URI에 보낼 수도 있습니다.

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | Description |
| --------- | ----------- |
| error | 발생 한 오류 유형을 분류 하는 데 사용 되는 코드입니다. |
| error_description | 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다.|

## <a name="validate-the-id-token"></a>ID 토큰 유효성 검사

ID 토큰을 받는 것만으로는 사용자를 인증할 수 없습니다. ID 토큰의 서명 유효성을 검사 하 고 앱의 요구 사항에 따라 토큰의 클레임을 확인 합니다. Azure AD B2C는 [JWT(JSON 웹 토큰)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다.

사용하려는 언어에 따라 JWT의 유효성을 검사하는 데 사용할 수 있는 다양한 오픈 소스 라이브러리가 있습니다. 사용자 고유의 유효성 검사 논리를 구현하는 대신 사용 가능한 오픈 소스 라이브러리를 탐색하는 것이 좋습니다. 이 문서의 정보를 통해 이러한 라이브러리를 올바르게 사용하는 방법을 알아볼 수 있습니다.

Azure AD B2C에는 OpenID Connect 메타데이터 엔드포인트가 있습니다. 앱에서 런타임에 Azure AD B2C에 대한 정보를 가져오는 데 엔드포인트를 사용할 수 있습니다. 이 정보에는 엔드포인트, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. Azure AD B2C 테넌트의 각 사용자 흐름에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 fabrikamb2c.onmicrosoft.com 테넌트의 b2c_1_sign_in 사용자 흐름에 대한 메타데이터 문서는 다음 위치에 있습니다.

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

이 구성 문서의 속성 중 하나가 `jwks_uri`입니다. 동일한 사용자 흐름에 대한 값은 다음과 같습니다.

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

ID 토큰에 서명하는 데 사용된 사용자 흐름(및 메타데이터를 가져올 위치)을 결정하려면 두 가지 옵션이 있습니다. 먼저 사용자 흐름 이름이 `id_token`의 `acr` 클레임에 포함됩니다. ID 토큰에서 클레임을 구문 분석하는 방법에 대한 내용은 [Azure AD B2C 토큰 참조](tokens-overview.md)를 참조하세요. 다른 옵션은 요청을 발급할 때 사용자 흐름을 `state` 매개 변수의 값으로 인코딩한 다음, `state` 매개 변수를 디코딩하여 어떤 사용자 흐름이 사용되었는지 확인하는 것입니다. 두 방법 중 하나는 유효합니다.

OpenID Connect 메타데이터 엔드포인트에서 메타데이터 문서를 가져오면 이 엔드포인트에 있는 RSA-256 공개 키를 사용하여 ID 토큰의 서명에 대한 유효성을 검사할 수 있습니다. 지정된 시간에 이 엔드포인트에 나열된 키가 여러 개 있을 수 있으며, 각 키는 `kid`로 식별됩니다. `id_token`의 헤더에는 `kid` 클레임도 포함되어 있으며, 이는 이러한 키 중에서 ID 토큰 서명에 사용된 키를 나타냅니다. [토큰 유효성을 검사하는 방법](tokens-overview.md)을 포함하여 자세한 내용은 [Azure AD B2C 토큰 참조](tokens-overview.md)를 참조하세요.
<!--TODO: Improve the information on this-->

ID 토큰의 서명에 대한 유효성을 검사한 후에는 확인해야 할 몇 가지 클레임이 있습니다. 예:

* `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
* `aud` 클레임의 유효성을 검사하여 앱에 대한 ID 토큰이 발급되었는지 확인합니다. 해당 값이 앱의 애플리케이션 ID여야 합니다.
* `iat` 및 `exp` 클레임의 유효성을 검사하여 ID 토큰이 만료되지 않았는지 확인합니다.

수행 해야 하는 여러 유효성 검사에 대한 자세한 내용은 [Openid connect Connect Core 사양](https://openid.net/specs/openid-connect-core-1_0.html)을 참조 하십시오. 시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

* 사용자 또는 조직이 앱에 등록했는지 확인합니다.
* 사용자에게 적절한 권한 부여 및 권한이 있는지 확인합니다.
* Azure Multi-Factor Authentication을 사용하는 것과 같이 특정 강도의 인증이 발생했는지 확인합니다.

ID 토큰의 클레임에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](tokens-overview.md)를 참조하세요.

ID 토큰의 유효성을 검사한 후에는 사용자가 포함된 세션을 시작할 수 있습니다. 앱에서 ID 토큰의 클레임을 사용하여 사용자 관련 정보를 가져올 수 있으며, 이 정보는 표시, 레코드, 권한 부여 등에 사용될 수 있습니다.

## <a name="get-access-tokens"></a>액세스 토큰 가져오기
웹앱에서 사용자 흐름만 실행해야 하는 경우 다음 몇 가지 섹션을 건너뛸 수 있습니다. 다음 섹션의 정보는 웹 API에 대한 인증 된 호출을 수행 해야 하는 웹 앱에만 적용 되 고 Azure AD B2C에 의해 보호 됩니다.

사용자를 단일 페이지 앱에 로그인 했으므로 Azure AD로 보호 되는 웹 Api를 호출 하기 위한 액세스 토큰을 가져올 수 있습니다. `token` 응답 형식을 사용하여 토큰을 이미 받았더라도 이 메서드를 사용하여 사용자가 다시 로그인하도록 리디렉션하지 않고도 추가 리소스에 대한 토큰을 얻을 수 있습니다.

일반적인 웹 앱 흐름에서는 `/token` 엔드포인트에 대한 요청을 수행 합니다. 그러나 엔드포인트은 CORS 요청을 지원 하지 않으므로 AJAX 호출을 통해 새로 고침 토큰을 가져오는 것은 옵션이 아닙니다. 대신 숨겨진 HTML iFrame 요소에서 암시적 흐름을 사용하여 다른 웹 API에 대한 새 토큰을 가져올 수 있습니다. 다음은 쉽게 읽을 수 있도록 줄 바꿈을 적용한 예제입니다.

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| 매개 변수 | 필수 여부 | Description |
| --- | --- | --- |
|넌| 필수 | Azure AD B2C 테넌트의 이름|
policy| 필수| 실행할 사용자 흐름입니다. Azure AD B2C 테넌트에서 만든 사용자 흐름의 이름을 지정 합니다. 예: `b2c_1_sign_in`, `b2c_1_sign_up`또는 `b2c_1_edit_profile`. |
| client_id |필수 |[Azure Portal](https://portal.azure.com)에서 앱에 할당된 애플리케이션 ID입니다. |
| response_type |필수 |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다.  `token` 응답 형식이 포함될 수도 있습니다. 여기서 `token`을 사용하면 앱에서 권한 부여 엔드포인트에 대한 두 번째 요청을 수행하지 않고도 권한 부여 엔드포인트에서 액세스 토큰을 즉시 받을 수 있습니다. `token` 응답 형식을 사용하는 경우 `scope` 매개 변수에는 토큰을 발급할 리소스를 나타내는 범위가 포함되어야 합니다. |
| redirect_uri |권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. URL로 인코딩해야 한다는 점을 제외하고는 포털에서 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| scope |필수 |공백으로 구분된 범위 목록입니다.  토큰을 가져오려면 원하는 리소스에 필요한 모든 범위를 포함합니다. |
| response_mode |권장 |결과 토큰을 앱으로 다시 보내는 데 사용되는 메서드를 지정합니다. 암시적 흐름의 경우 `fragment`를 사용 합니다. 다른 두 가지 모드를 지정 하 고 `query` 하 고 `form_post`수 있지만 암시적 흐름에서는 작동 하지 않습니다. |
| state |권장 |토큰 응답에 반환되는 요청에 포함된 값입니다.  사용하려는 콘텐츠의 문자열일 수 있습니다.  일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다.  또한 state(상태)는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보를 인코딩하는 데에도 사용됩니다. 예를 들어 사용자가 있던 페이지 또는 보기입니다. |
| nonce |필수 |앱에서 생성한 요청에 포함된 값이며, 결과 ID 토큰에 클레임으로 포함됩니다.  그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| prompt |필수 |숨겨진 iFrame에서 토큰을 새로 고치고 가져오려면 `prompt=none`을 사용하여 iFrame이 로그인 페이지에 머물지 않고 즉시 반환되는지 확인합니다. |
| login_hint |필수 |숨겨진 iFrame에서 토큰을 새로 고치고 가져오려면 사용자가 지정된 시간에 가질 수 있는 여러 세션을 구분하기 위해 이 힌트에 해당 사용자의 사용자 이름을 포함합니다. `preferred_username` 클레임을 사용 하 여 이전 로그인에서 사용자 이름을 추출할 수 있습니다 (`preferred_username` 클레임을 받기 위해 `profile` 범위가 필요 함). |
| domain_hint |필수 |`consumers` 또는 `organizations`일 수 있습니다.  숨겨진 iframe에서 토큰을 새로 고치고 가져오는 경우 요청에 `domain_hint` 값을 포함 합니다.  이전 로그인의 ID 토큰에서 `tid` 클레임을 추출 하 여 사용할 값을 결정 합니다 (`tid` 클레임을 받기 위해 `profile` 범위가 필요 함). `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용합니다.  그렇지 않으면 `domain_hint=organizations`를 사용합니다. |

`prompt=none` 매개 변수를 설정하면 이 요청이 즉시 성공하거나 실패하고 애플리케이션으로 돌아갑니다.  성공적인 응답은 `response_mode` 매개 변수에 지정된 메서드를 사용하여 표시된 리디렉션 URI에서 앱으로 보내집니다.

### <a name="successful-response"></a>성공적인 응답
`response_mode=fragment`를 사용한 성공적인 응답은 다음 예제와 같습니다.

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| 매개 변수 | Description |
| --- | --- |
| access_token |앱이 요청한 토큰입니다. |
| token_type |토큰 형식은 항상 Bearer(전달자)입니다. |
| state |요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 `state` 값이 동일한지 확인해야 합니다. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| scope |access_token이 유효한 범위입니다. |

### <a name="error-response"></a>오류 응답
오류 응답은 앱에서 적절히 처리할 수 있도록 리디렉션 URI에 보낼 수도 있습니다.  `prompt=none`의 경우 예상 되는 오류는 다음 예제와 같습니다.

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 매개 변수 | Description |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있는 오류 코드 문자열입니다. 문자열을 사용하여 오류에 대응할 수도 있습니다. |
| error_description |인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

iFrame 요청에 이러한 오류를 수신하면, 사용자는 새 토큰을 얻기 위해 대화형으로 다시 로그인해야 합니다.

## <a name="refresh-tokens"></a>새로 고침 토큰
ID 토큰 및 액세스 토큰은 모두 짧은 기간 후에 만료됩니다. 이러한 토큰을 정기적으로 새로 고치려면 앱을 준비해야 합니다.  두 가지 형식의 토큰을 새로 고치려면 `prompt=none` 매개 변수를 통해 Azure AD 단계를 제어하여 이전 예제에서 사용한 것과 동일한 숨겨진 iFrame 요청을 수행합니다.  새 `id_token` 값을 받으려면 `response_type=id_token`, `scope=openid` 및 `nonce` 매개 변수를 사용해야 합니다.

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기
사용자를 앱에서 로그 아웃 하려는 경우 사용자를 Azure AD로 리디렉션하여 로그 아웃 합니다. 사용자를 리디렉션하지 않으면 자격 증명을 다시 입력 하지 않고 앱에 다시 인증할 수 있습니다. Azure AD에 유효한 Single Sign-On 세션이 있기 때문입니다.

[ID 토큰 유효성 검사](#validate-the-id-token)에서 설명한 동일한 OpenID Connect 메타데이터 문서에 나열된 `end_session_endpoint`로 사용자를 리디렉션할 수 있습니다. 예:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| 넌 | 예 | Azure AD B2C 테넌트의 이름 |
| policy | 예 | 애플리케이션에서 사용자를 로그아웃하는 데 사용하려는 사용자 흐름입니다. |
| post_logout_redirect_uri | 아닙니다. | 성공적으로 로그 아웃 한 후에 사용자가 리디렉션되는 URL입니다. 포함 되어 있지 않으면 Azure AD B2C 사용자에 게 일반 메시지를 표시 합니다. |
| state | 아닙니다. | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 응용 프로그램은 요청 및 응답의 `state` 값이 동일한 지 확인 해야 합니다. |


> [!NOTE]
> 사용자를 `end_session_endpoint`로 전달하면 Azure AD B2C로 사용자의 Single Sign-On 상태 중 일부를 지웁니다. 그러나 사용자의 소셜 ID 공급자 세션에서 사용자를 서명하지 않습니다. 사용자가 후속 로그인 중에 동일한 id 공급자를 선택 하는 경우 자격 증명을 입력 하지 않고 사용자를 다시 인증 합니다. 사용자가 Azure AD B2C 애플리케이션에서 로그아웃하려는 경우 반드시 Facebook과 같은 계정에서 완전히 로그아웃하려는 것은 아닙니다. 그러나 로컬 계정의 경우 사용자의 세션이 제대로 종료됩니다.
>

## <a name="next-steps"></a>다음 단계

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>코드 샘플: Azure AD B2C 포함 된 hello.exe

[Azure AD B2C를 사용 하 여 hello .js에 빌드된 단일 페이지 응용 프로그램][github-hello-js-example] (GitHub)

GitHub의이 샘플은 사용자가 [hello.exe][github-hello-js] 를 기반으로 하는 간단한 웹 응용 프로그램에서 Azure AD B2C를 시작 하 고 팝업 스타일 인증을 사용 하는 데 도움을 주기 위한 것입니다.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp
[github-hello-js]: https://github.com/MrSwitch/hello.js
