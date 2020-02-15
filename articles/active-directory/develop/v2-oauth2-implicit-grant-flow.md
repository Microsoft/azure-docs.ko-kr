---
title: OAuth 2.0 암시적 권한 부여 흐름-Microsoft identity platform | Microsoft
description: Microsoft id 플랫폼 암시적 흐름을 사용 하 여 단일 페이지 앱을 보호 합니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d7f27ad2adc5d4abf2b5ec993b3398ebf1370f52
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159678"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft id 플랫폼 및 암시적 허용 흐름

Microsoft id 플랫폼 끝점을 사용 하 여 Microsoft의 개인 및 회사 또는 학교 계정을 사용 하 여 단일 페이지 앱에 사용자를 로그인 할 수 있습니다. 주로 브라우저에서 실행되는 단일 페이지 앱 및 기타 JavaScript 앱에는 인증과 관련하여 해결해야 하는 몇 가지 문제가 있습니다.

* 이러한 앱의 보안 특성은 기존의 서버 기반 웹 애플리케이션과 상당히 다릅니다.
* 대다수 권한 부여 서버 및 ID 공급자는 CORS 요청을 지원하지 않습니다.
* 앱에서 멀어지는 전체 페이지 브라우저 리디렉션은 사용자 환경에 특히 방해가 됩니다.

이러한 응용 프로그램 (AngularJS, Ember.js, node.js 등)의 경우 Microsoft id 플랫폼은 OAuth 2.0 암시적 권한 부여 흐름을 지원 합니다. 암시적 흐름은 [OAuth 2.0 사양(영문)](https://tools.ietf.org/html/rfc6749#section-4.2)에 설명되어 있습니다. 주요 장점은 앱이 백 엔드 서버 자격 증명 교환을 수행 하지 않고도 Microsoft id 플랫폼에서 토큰을 가져올 수 있다는 것입니다. 따라서 앱은 사용자 로그인, 세션 유지 관리, 다른 웹 API에 대한 토큰 가져오기를 모두 클라이언트 JavaScript 코드 내에서 수행할 수 있습니다. 암시적 흐름을 사용하는 경우 보안과 관련된 몇 가지 중요 사항(특히 [클라이언트](https://tools.ietf.org/html/rfc6749#section-10.3) 및 [사용자 가장](https://tools.ietf.org/html/rfc6749#section-10.3) 관련 사항)을 고려해야 합니다.

이 문서에서는 응용 프로그램에서 프로토콜에 대해 직접 프로그래밍 하는 방법을 설명 합니다.  가능 하면 [토큰을 획득 하 고 보안 웹 api를 호출](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)하는 대신 지원 되는 Msal (Microsoft 인증 라이브러리)을 사용 하는 것이 좋습니다.  [MSAL을 사용 하는 샘플 앱](sample-v2-code.md)에 대해서도 살펴봅니다.

하지만 단일 페이지 앱에서 라이브러리를 사용하지 않고 직접 프로토콜 메시지를 보내려는 경우에는 아래의 일반적인 단계를 따릅니다.

> [!NOTE]
> 모든 Azure Active Directory (Azure AD) 시나리오 및 기능이 Microsoft id 플랫폼 끝점에서 지원 되는 것은 아닙니다. Microsoft id 플랫폼 끝점을 사용 해야 하는지 확인 하려면 [microsoft id 플랫폼 제한 사항](active-directory-v2-limitations.md)을 참조 하세요.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 암시적 허용에 적합한 시나리오

OAuth2 사양에서는 사용자 에이전트 애플리케이션, 즉 JavaScript 애플리케이션을 브라우저 내에서 실행할 수 있도록 하기 위해 암시적 허용이 고안되었다고 선언하고 있습니다. 이러한 애플리케이션의 결정적인 특징은 JavaScript 코드를 사용하여 서버 리소스(대개 웹 API)에 액세스하고, 이에 따라 애플리케이션 사용자 환경을 업데이트한다는 것입니다. 받은 편지함에서 메시지를 선택할 때 메시지 시각화 패널에만 새 선택 내용이 표시되고 나머지 페이지는 수정되지 않은 채로 남아 있는 Gmail 또는 Outlook Web Access와 같은 애플리케이션을 생각해 보세요. 이 특징은 모든 사용자 상호 작용으로 인해 전체 페이지를 다시 게시하고 새 서버 응답의 전체 페이지를 렌더링하는 기존 리디렉션 기반 웹앱과는 대조적입니다.

JavaScript 기반 접근 방법을 극한까지 확장하는 애플리케이션을 단일 페이지 애플리케이션 또는 SPA라고 합니다. 이러한 애플리케이션은 초기 HTML 페이지 및 연결된 JavaScript만 제공하고, 모든 후속 상호 작용은 JavaScript를 통해 수행된 Web API 호출에 의해 구동됩니다. 그러나 애플리케이션이 대부분 포스트백을 기반으로 하지만 가끔 JS 호출을 수행하는 하이브리드 방법은 흔치 않습니다. 암시적 흐름 사용에 관한 설명은 그러한 경우에도 관련됩니다.

일반적으로 리디렉션 기반 애플리케이션은 쿠키를 통해 해당 요청을 보호하지만 해당 접근 방식은 JavaScript 애플리케이션에도 작동하지 않습니다. 쿠키는 생성된 도메인에 대해서만 작동하지만 JavaScript 호출은 다른 도메인으로 편향될 수 있습니다. 사실 이러한 경우는 아주 많습니다. 모두 애플리케이션을 제공한 도메인의 외부에 상주하는 Graph API, Office API, Azure API를 호출하는 애플리케이션을 생각해 보세요. JavaScript 애플리케이션이 증가하는 추세는 타사 웹 API에 전적으로 의존하여 비즈니스 기능을 구현하는 백 엔드가 전혀 없다는 것입니다.

현재 웹 API에 대한 호출을 보호하는 기본 방법은 모든 호출이 OAuth2 액세스 토큰과 함께 동반되는 OAuth2 전달자 토큰 접근 방법을 사용하는 것입니다. 웹 API는 들어오는 액세스 토큰을 검사하고 필요한 범위에서 찾으면 요청된 작업에 대한 액세스를 부여합니다. 암시적 흐름은 JavaScript 애플리케이션이 웹 API에 대한 액세스 토큰을 가져오기에 편리한 메커니즘을 제공하여 쿠키에 관한 다양한 장점을 제공합니다.

* 토큰을 원래 호출과 교차할 필요 없이 신뢰성 있게 가져올 수 있음 - 토큰을 반환하는 리디렉션 URI의 필수 등록을 통해 토큰이 이동되지 않도록 보증
* JavaScript 애플리케이션이 대상으로 하는 웹 API 수만큼 액세스 토큰을 필요한 대로 가져올 수 있음 - 도메인에 대한 제한 없음
* 세션 또는 로컬 스토리지와 같은 HTML5 기능이 토큰 캐싱 및 수명 관리를 완전히 제어할 수 있으면서도 쿠키 관리는 앱에서 신경쓸 필요가 없음
* 액세스 토큰이 교차 사이트 요청 위조(CSRF) 공격에 취약하지 않음

암시적 허용 흐름은 대부분 보안상의 이유로 새로 고침 토큰을 발급하지 않습니다. 새로 고침 토큰은 액세스 토큰으로 범위가 한정 되지 않고, 훨씬 더 많은 기능을 부여 하므로 누수가 발생 하는 경우 훨씬 더 손상이 좁지. 암시적 흐름에서 토큰은 URL로 전달 되므로 가로채기의 위험은 인증 코드 부여 보다 높습니다.

그러나 JavaScript 애플리케이션에서는 사용자에게 자격 증명을 반복적으로 요구하지 않고 액세스 토큰을 갱신하는 또 다른 메커니즘을 마음대로 사용할 수 있습니다. 애플리케이션은 숨겨진 iframe을 사용하여 Azure AD의 권한 부여 엔드포인트에 대해 새 토큰 요청을 수행할 수 있습니다. 즉, 브라우저가 Azure AD 도메인에 대해 활성 세션을 여전히 가지고 있으면(의미: 세션 쿠키를 가짐) 사용자 상호 작용이 필요 없이 인증 요청이 성공적으로 이루어질 수 있습니다.

이 모델은 JavaScript 애플리케이션에 액세스 토큰을 독립적으로 갱신하고 새 API에 대한 새 액세스 토큰을 획득하는 기능을 부여합니다(이전에 사용자가 동의한 경우에만). 이렇게 하면 새로 고침 토큰과 같은 높은 값의 아티팩트를 획득, 유지 관리 및 보호하는 부담을 방지합니다. 자동 갱신을 가능하게 하는 아티팩트인 Azure AD 세션 쿠키는 애플리케이션 외부에서 관리됩니다. 이 방법의 다른 장점은 사용자가 브라우저 탭 중 하나에서 실행하는 Azure AD에 로그인하는 애플리케이션을 사용하여 Azure AD에서 로그아웃할 수 있는 것입니다. 이로 인해 Azure AD 세션 쿠키가 삭제되고 JavaScript 애플리케이션은 로그아웃된 사용자에 대한 토큰을 갱신하는 기능을 자동으로 손실합니다.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>암시적 허용이 내 앱에 적합할까요?

암시적 권한 부여는 다른 권한 부여 보다 더 많은 위험을 표시 하며 주의 해야 하는 영역 (예: [암시적 흐름에서 리소스 소유자를 가장 하는 데 액세스 토큰 오용] [OAuth2] 및 [OAuth 2.0 위협 모델)이 잘 문서화 되어 있습니다. 및 보안 고려 사항] [OAuth2]). 그러나 더 높은 위험 프로필은 주로 원격 리소스에서 브라우저에 제공한 활성 코드를 실행하는 애플리케이션을 사용하도록 설정하는 것을 의미한다는 사실 때문입니다. SPA 아키텍처를 계획하는 경우 백 엔드 구성 요소가 없거나 JavaScript를 통해 웹 API를 호출하려고 하므로 토큰 획득을 위해 암시적 흐름을 사용하는 것이 좋습니다.

애플리케이션이 네이티브 클라이언트인 경우 암시적 흐름은 그다지 적합하지 않습니다. 네이티브 클라이언트 상황에서 Azure AD 세션 쿠키가 없으면 오래 지속되는 세션을 유지 관리하는 수단에서 애플리케이션을 사용하지 않게 됩니다. 즉 애플리케이션은 새 리소스에 대한 액세스 토큰을 가져올 때 사용자에게 반복해서 메시지를 표시합니다.

백 엔드가 포함된 웹 애플리케이션을 개발하고 해당 백 엔드 코드에서 API를 사용하는 경우에도 암시적 흐름은 그다지 적합하지 않습니다. 다른 권한 부여는 훨씬 더 많은 전원을 제공합니다. 예를 들어 OAuth2 클라이언트 자격 증명 부여는 사용자 위임이 아니라 애플리케이션 자체에 할당된 사용 권한을 반영하는 토큰을 가져올 수 있는 기능을 제공합니다. 즉 클라이언트는 사용자가 세션에 있지 않은 경우 등에도 리소스에 대한 프로그래밍 방식의 액세스를 유지하는 기능을 갖습니다. 뿐만 아니라 이러한 부여는 더 높은 보안성 보증을 제공합니다. 예를 들어 액세스 토큰은 사용자 브라우저를 통해 전송되지 않고 브라우저 기록 등에 저장되는 위험을 감수하지 않습니다. 또한 클라이언트 애플리케이션은 토큰을 요청할 때 강력한 인증을 수행할 수 있습니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

아래 다이어그램에는 암시적 로그인 흐름의 전체적인 형태가 나와 있습니다. 다이어그램 아래의 섹션에서는 각 단계에 대해 더 자세히 설명합니다.

![암시적 로그인 흐름을 보여 주는 다이어그램](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>로그인 요청 보내기

처음에 사용자를 앱에 로그인 하려면 [Openid connect Connect](v2-protocols-oidc.md) 인증 요청을 보내고 Microsoft id 플랫폼 끝점에서 `id_token`를 가져올 수 있습니다.

> [!IMPORTANT]
> ID 토큰 및/또는 액세스 토큰을 성공적으로 요청 하려면 **암시적 권한** 부여 섹션에서 **id 토큰** 및. 또는 **액세스 토큰** 을 선택 하 여 [Azure Portal 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지의 앱 등록에서 해당 하는 암시적 권한 부여 흐름을 사용 하도록 설정 해야 합니다. 사용 하도록 설정 되지 않은 경우 `unsupported_response` 오류가 반환 됩니다. **입력 매개 변수 ' response_type '에 대해 제공 된 값이이 클라이언트에 허용 되지 않습니다. 필요한 값은 ' 코드 '입니다** .

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
> 암시적 흐름을 사용 하 여 로그인을 테스트 하려면 <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize..를 클릭 합니다.</a> 로그인 한 후 브라우저는 주소 표시줄에 `id_token`를 사용 하 여 `https://localhost/myapp/`으로 리디렉션됩니다.
>

| 매개 변수 |  | Description |
| --- | --- | --- |
| `tenant` | required |요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| `client_id` | required | [Azure Portal 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당 된 응용 프로그램 (클라이언트) ID입니다. |
| `response_type` | required |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다. response_type `token`을 포함할 수도 있습니다. `token` 을 여기서 사용하면 앱은 권한 부여 엔드포인트에 두 번째 요청을 수행하지 않고 권한 부여 엔드포인트에서 즉시 액세스 토큰을 받을 수 있습니다. `token` response_type를 사용 하는 경우 `scope` 매개 변수는 토큰을 발급할 리소스를 나타내는 범위를 포함 해야 합니다 (예: Microsoft Graph에 대 한 사용자 읽기).  |
| `redirect_uri` | 권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. |
| `scope` | required |공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다. Openid connect Connect (id_tokens)의 경우 동의 UI의 "로그인" 권한으로 변환 되는 범위 `openid`를 포함 해야 합니다. 필요에 따라 추가 사용자 데이터에 대 한 액세스 권한을 얻기 위해 `email` 및 `profile` 범위를 포함할 수도 있습니다. 액세스 토큰을 요청 하는 경우 다양 한 리소스에 대 한 동의를 요청 하기 위해이 요청에 다른 범위를 포함할 수도 있습니다. |
| `response_mode` | 선택 사항 |결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 는 기본적으로 액세스 토큰에 대해서만 쿼리 하지만 요청에 id_token 포함 된 경우에는 fragment를 사용 합니다. |
| `state` | 권장 |토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| `nonce` | required |결과 id_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. id_token 요청 시에만 필수입니다. |
| `prompt` | 선택 사항 |필요한 사용자 상호 작용 유형을 나타냅니다. 현재 유효한 값은 'login', 'none', 'select_account', 'consent'뿐입니다. `prompt=login` 은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다. `prompt=none`은 그 반대 이며, 사용자에 게 대화형 프롬프트가 표시 되지 않습니다. Single sign-on을 통해 요청을 자동으로 완료할 수 없는 경우 Microsoft identity platform 끝점은 오류를 반환 합니다. `prompt=select_account`는 세션에 저장된 모든 계정이 표시되는 계정 선택기로 사용자를 전송합니다. `prompt=consent` 는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| `login_hint`  |선택 사항 |사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다.|
| `domain_hint` | 선택 사항 |포함 되는 경우 사용자가 로그인 페이지에서 이동 하는 전자 메일 기반 검색 프로세스를 건너뛰고 약간 더 간소화 된 사용자 환경을 제공 합니다. 이는 단일 테 넌 트에서 작동 하는 lob (기간 업무) 앱에 일반적으로 사용 되며, 지정 된 테 넌 트 내에서 도메인 이름을 제공 합니다.  이렇게 하면 사용자를 해당 테 넌 트의 페더레이션 공급자로 전달 합니다.  이렇게 하면 게스트가이 응용 프로그램에 로그인 할 수 없습니다.  |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. 또한 Microsoft identity platform 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시 된 사용 권한에 동의한 확인 합니다. 사용자가 해당 권한 중 어떤 항목에도 동의하지 **않은** 경우에는 필요한 권한에 동의하라는 메시지가 표시됩니다. 자세한 내용은. [권한, 동의 및 다중 테넌트 앱](v2-permissions-and-consent.md)을 참조하세요.

사용자가 인증 하 고 동의 하면 Microsoft id 플랫폼 끝점이 `response_mode` 매개 변수에 지정 된 메서드를 사용 하 여 지정 된 `redirect_uri`에서 앱에 대 한 응답을 반환 합니다.

#### <a name="successful-response"></a>성공적인 응답

`response_mode=fragment` 및 `response_type=id_token+token` 사용 시의 정상 응답은 다음과 같습니다. 쉽게 읽을 수 있도록 아래 예제에는 줄 바꿈이 적용되어 있습니다.

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 매개 변수 | Description |
| --- | --- |
| `access_token` |`response_type`이 `token`을 포함하는 경우 포함됩니다. 앱에서 요청한 액세스 토큰입니다. 액세스 토큰을 디코드 하거나 검사 하지 않아야 합니다 .이 토큰은 불투명 문자열로 처리 되어야 합니다. |
| `token_type` |`response_type`이 `token`을 포함하는 경우 포함됩니다. 항상 `Bearer`입니다. |
| `expires_in`|`response_type`이 `token`을 포함하는 경우 포함됩니다. 캐싱 목적으로 토큰이 유효한 시간(초)을 나타냅니다. |
| `scope` |`response_type`이 `token`을 포함하는 경우 포함됩니다. access_token이 유효한 범위를 나타냅니다. 사용자에 게 적용할 수 없는 경우 요청 된 범위를 모두 포함 하지 않을 수 있습니다 (개인 계정을 사용 하 여 로그인 할 때 Azure AD 전용 범위를 요청 하는 경우). |
| `id_token` | 서명된 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시 하 고 표시할 수 있지만 권한 부여 또는 보안 경계에는이 값을 사용 하지 않아야 합니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |
| `state` |요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | Description |
| --- | --- |
| `error` |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="getting-access-tokens-silently-in-the-background"></a>백그라운드에서 자동으로 액세스 토큰 가져오기

사용자를 단일 페이지 앱에 로그인 했으므로 Microsoft id 플랫폼에 의해 보호 되는 웹 Api를 호출 하기 위한 액세스 토큰을 자동으로 가져올 수 있습니다 (예: [Microsoft Graph](https://developer.microsoft.com/graph)). `token` response_type을 사용하여 토큰을 이미 받았더라도 이 방법을 사용하여 사용자가 다시 로그인하도록 리디렉션하지 않고 추가 리소스에 대한 토큰을 얻을 수 있습니다.

일반적인 Openid connect Connect/OAuth 흐름에서 Microsoft identity platform `/token` 끝점에 요청 하 여이 작업을 수행 합니다. 그러나 Microsoft identity platform 끝점은 CORS 요청을 지원 하지 않으므로 AJAX 호출을 통해 토큰을 가져오고 새로 고치는 것은 문제가 되지 않습니다. 그 대신, 숨겨진 iFrame에 암시적 흐름을 사용하여 다른 웹 API에 대한 새 토큰을 가져올 수 있습니다. 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

URL의 쿼리 매개 변수에 대한 자세한 내용은 [로그인 요청 보내기](#send-the-sign-in-request)를 참조하세요.

> [!TIP]
> 아래 요청을 브라우저 탭에 복사하여 붙여 넣으세요! (`login_hint` 값을 사용자에 대한 올바른 값으로 바꾸는 것을 잊지 마세요)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

`prompt=none` 매개 변수로 인해, 이 요청은 즉시 성공하거나 실패하고 애플리케이션에 반환됩니다. 성공적인 응답은 `redirect_uri` 매개 변수에 지정된 메서드를 사용하여 지정된 `response_mode`의 앱으로 전송됩니다.

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

| 매개 변수 | Description |
| --- | --- |
| `access_token` |`response_type`이 `token`을 포함하는 경우 포함됩니다. Microsoft Graph의 경우 앱에서 요청한 액세스 토큰입니다. 액세스 토큰을 디코드 하거나 검사 하지 않아야 합니다 .이 토큰은 불투명 문자열로 처리 되어야 합니다. |
| `token_type` | 항상 `Bearer`입니다. |
| `expires_in` | 캐싱 목적으로 토큰이 유효한 시간(초)을 나타냅니다. |
| `scope` | access_token이 유효한 범위를 나타냅니다. 사용자에 게 적용할 수 없는 경우 요청 된 범위를 모두 포함 하지 않을 수 있습니다 (개인 계정을 사용 하 여 로그인 할 때 Azure AD 전용 범위를 요청 하는 경우). |
| `id_token` | 서명된 JWT(JSON 웹 토큰)입니다. `response_type`이 `id_token`을 포함하는 경우 포함됩니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시 하 고 표시할 수 있지만 권한 부여 또는 보안 경계에는이 값을 사용 하지 않아야 합니다. id_tokens에 대한 자세한 내용은 [`id_token` 참조](id-tokens.md)를 확인하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |
| `state` |요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다. `prompt=none`의 경우, 예상되는 오류는 다음과 같습니다.

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 매개 변수 | Description |
| --- | --- |
| `error` |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

iFrame 요청에 이러한 오류를 수신하면, 사용자는 새 토큰을 얻기 위해 대화형으로 다시 로그인해야 합니다. 어떠한 방식이든 애플리케이션에 적합한 방식으로 이러한 경우를 처리하도록 선택할 수 있습니다.

## <a name="refreshing-tokens"></a>토큰 새로 고침

암시적 권한 부여는 새로 고침 토큰을 제공하지 않습니다. `id_token`과 `access_token`은 모두 짧은 시간 안에 만료되기 때문에 앱은 주기적으로 토큰을 새로 고치도록 준비가 되어야 합니다. 두 가지 유형의 토큰을 새로 고치려면 `prompt=none` 매개 변수를 사용 하 여 위와 동일한 숨겨진 iframe 요청을 수행 하 여 id 플랫폼의 동작을 제어할 수 있습니다. 새 `id_token`을 받으려면 `response_type` 및 `scope=openid`에서 `id_token`를 사용 하 고 `nonce` 매개 변수를 사용 해야 합니다.

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기

Openid connect Connect `end_session_endpoint`를 사용 하면 앱에서 Microsoft id 플랫폼 끝점으로 요청을 보내 사용자 세션을 종료 하 고 Microsoft id 플랫폼 끝점에서 설정한 쿠키를 지울 수 있습니다. 앱은 웹 애플리케이션에서 특정 사용자를 완전히 로그아웃시키기 위해 일반적으로 토큰 캐시를 지우거나 쿠키를 삭제하여 고유한 사용자 세션을 종료한 다음, 브라우저를 아래 주소로 리디렉션합니다.

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 매개 변수 |  | Description |
| --- | --- | --- |
| `tenant` |required |요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| `post_logout_redirect_uri` | 권장 | 로그아웃이 완료된 후 사용자가 반환되어야 하는 URL입니다. 이 값은 애플리케이션에 대해 등록된 리디렉션 URI 중 하나와 일치해야 합니다. 포함 되지 않은 경우 사용자가 Microsoft id 플랫폼 끝점에서 일반 메시지를 표시 합니다. |

## <a name="next-steps"></a>다음 단계

* [MSAL JS 샘플](sample-v2-code.md)을 검토하여 코딩을 시작합니다.
