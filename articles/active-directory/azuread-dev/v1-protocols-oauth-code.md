---
title: Azure AD의 OAuth 2.0 인증 코드 흐름 이해
description: 이 문서에서는 Azure Active Directory 및 OAuth 2.0을 사용하여 테넌트에서 웹 애플리케이션 및 Web API에 대한 액세스 권한을 부여하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bc303dc62892f8fac67bb6869e72db0e40f19779
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164033"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  서버에 호출 하려는 리소스를 모르는 경우 서버는 해당 리소스에 대 한 조건부 액세스 정책을 트리거되지 않습니다. 따라서 MFA 트리거를 만들려면 URL에 리소스를 포함 해야 합니다. 
>

Azure AD(Azure Active Directory)는 OAuth 2.0을 사용하여 사용자는 Azure AD 테넌트에서 웹 애플리케이션 및 웹 API에 대한 액세스 권한을 부여할 수 있습니다. 이 가이드는 언어 독립적이며 [오픈 소스 라이브러리](active-directory-authentication-libraries.md)를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다.

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](https://tools.ietf.org/html/rfc6749#section-4.1)에서 설명합니다. 웹앱 및 기본적으로 설치된 앱을 포함하여 대부분의 애플리케이션 형식에서 인증 및 권한 부여를 수행하는 데 사용됩니다.

## <a name="register-your-application-with-your-ad-tenant"></a>AD 테넌트에 애플리케이션 등록
먼저 Azure Active Directory (Azure AD) 테 넌 트에 응용 프로그램을 등록 합니다. 그러면 애플리케이션에 대한 애플리케이션 ID가 제공되며 토큰을 수신하는 데 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
   
1. 페이지의 오른쪽 위 모서리에서 계정을 선택 하 고 **디렉터리 탐색 스위치** 를 선택한 다음 적절 한 테 넌 트를 선택 하 여 Azure AD 테 넌 트를 선택 합니다. 
   - 계정에 Azure AD 테 넌 트가 하나만 있거나 적절 한 Azure AD 테 넌 트를 이미 선택한 경우이 단계를 건너뜁니다.
   
1. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.
   
1. **Azure Active Directory** 왼쪽 메뉴에서 **앱 등록**을 선택 하 고 **새 등록**을 선택 합니다.
   
1. 프롬프트에 따라 새 애플리케이션을 만듭니다. 이 자습서에서는 웹 응용 프로그램이 든 공용 클라이언트 (모바일 & 데스크톱) 응용 프로그램 인지는 중요 하지 않지만, 웹 응용 프로그램 또는 공용 클라이언트 응용 프로그램에 대 한 특정 예제를 원하는 경우 빠른 [시작을 확인 하세요.](v1-overview.md)
   
   - **이름**은 애플리케이션 이름이고 최종 사용자에게 애플리케이션을 설명합니다.
   - **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
   - **리디렉션 URI**를 제공 합니다. 웹 응용 프로그램의 경우 사용자가 로그인 할 수 있는 앱의 기본 URL입니다.  `http://localhost:12345`)을 입력합니다. 공용 클라이언트 (모바일 & 데스크톱)의 경우 Azure AD에서 토큰 응답을 반환 하는 데 사용 합니다. 애플리케이션에 특정한 값을 입력합니다.  `http://MyFirstAADApp`)을 입력합니다.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. 등록이 완료 되 면 Azure AD는 응용 프로그램에 고유한 클라이언트 식별자 ( **응용 프로그램 ID**)를 할당 합니다. 이 값은 다음 섹션에서 필요하므로 애플리케이션 페이지에서 이 값을 복사해 둡니다.
   
1. Azure Portal에서 응용 프로그램을 찾으려면 **앱 등록**를 선택한 다음 **모든 응용 프로그램 보기**를 선택 합니다.

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 권한 부여 흐름

높은 수준에서 애플리케이션에 대한 전체 권한 부여 흐름은 다음과 같습니다.

![OAuth 인증 코드 흐름](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>인증 코드 요청

인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 엔드포인트로 보내는 것으로 시작됩니다. 이 요청에서 클라이언트는 사용자로부터 얻어야 하는 사용 권한을 나타냅니다. Azure Portal에서 **앱 등록 > 엔드포인트**를 차례로 선택하여 테넌트에 대한 OAuth 2.0 권한 부여 엔드포인트를 가져올 수 있습니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| 매개 변수 |  | Description |
| --- | --- | --- |
| tenant |required |요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 테넌트 독립 토큰에 대한 테넌트 식별자(예: `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com`, `common`)입니다. |
| client_id |required |Azure AD에 등록할 때 앱에 할당된 애플리케이션 ID입니다. Azure Portal에서 이러한 값을 확인할 수 있습니다. 서비스 세로 막대에서 **Azure Active Directory**를 클릭하고, **앱 등록**을 클릭하고, 애플리케이션을 선택합니다. |
| response_type |required |인증 코드 흐름에 대한 `code`를 포함해야 합니다. |
| redirect_uri |권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. 네이티브 및 모바일 앱의 경우 `https://login.microsoftonline.com/common/oauth2/nativeclient`의 기본값을 사용해야 합니다. |
| response_mode |선택 사항 |결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. `query`, `fragment` 또는 `form_post`일 수 있습니다. `query`는 리디렉션 URI에 코드를 쿼리 문자열 매개 변수로 제공합니다. 암시적 흐름을 사용 하 여 ID 토큰을 요청 하는 경우 [openid connect 사양](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)에 지정 된 대로 `query`를 사용할 수 없습니다. 코드만 요청 하는 경우 `query`, `fragment`또는 `form_post`를 사용할 수 있습니다. `form_post`는 리디렉션 URI에 대한 코드가 포함된 POST를 실행합니다. 기본값은 코드 흐름에 대한 `query`입니다.  |
| state |권장 |토큰 응답에도 반환되는 요청에 포함된 값입니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| resource | 권장 |대상 웹 API의 앱 ID URI(보안 리소스)입니다. 앱 ID URI을 찾으려면 Azure Portal에서 **Azure Active Directory**, **애플리케이션 등록**을 차례로 클릭하고, 서비스 애플리케이션의 **설정** 페이지를 연 다음, **속성**을 클릭합니다. `https://graph.microsoft.com`과 같은 외부 리소스일 수도 있습니다. 이는 권한 부여 또는 토큰 요청 중 하나에서 필요합니다. 인증 프롬프트의 수를 줄이기 위해 사용자의 동의를 받을 수 있도록 하는 권한 부여 요청에 배치합니다. |
| scope | **무시됨** | v1 Azure AD 앱의 경우 Azure Portal의 애플리케이션 **설정**, **필수 권한** 아래에서 범위를 정적으로 구성해야 합니다. |
| prompt |선택 사항 |필요한 사용자 상호 작용 유형을 나타냅니다.<p> 유효한 값은 다음과 같습니다. <p> *로그인*: 사용자에게 재인증할지 묻는 메시지가 표시되어야 합니다. <p> *select_account*: 사용자에게 Single Sign On을 중단하는 계정을 선택하라는 메시지가 표시됩니다. 사용자는 기존에 로그인한 계정을 선택하고, 저장된 계정의 자격 증명을 입력하거나 다른 계정을 함께 사용하도록 선택할 수 있습니다. <p> *동의*: 사용자 동의가 허용되었지만 업데이트해야 합니다. 사용자에게 동의하는지 묻는 메시지가 표시되어야 합니다. <p> *admin_consent*: 관리자에게 조직의 사용자를 대신하여 동의하는지 묻는 메시지가 표시되어야 합니다. |
| login_hint |선택 사항 |사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다. |
| domain_hint |선택 사항 |사용자가 로그인하는 데 사용해야 하는 테넌트 또는 도메인에 대한 힌트를 제공합니다. domain_hint의 값은 테넌트에 대해 등록된 도메인입니다. 테넌트가 온-프레미스 디렉터리로 페더레이션된 경우, AAD는 지정된 테넌트 페더레이션 서버로 리디렉션됩니다. |
| code_challenge_method | 권장    | `code_verifier` 매개 변수에 대한 `code_challenge`를 인코딩하는 데 사용되는 메서드입니다. `plain` 또는 `S256` 중 하나일 수 있습니다. 제외할 경우 `code_challenge`가 포함되면 `code_challenge`가 일반 텍스트로 간주됩니다. Azure AAD v1.0은 `plain` 및 `S256`을 모두 지원합니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |
| code_challenge        | 권장    | 네이티브 또는 공용 클라이언트에서 PKCE(Proof Key for Code Exchange)를 통해 권한 부여 코드를 보호하는 데 사용됩니다. `code_challenge_method`가 포함되면 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요. |

> [!NOTE]
> 사용자가 조직에 소속된 경우 조직 관리자는 사용자 대신 동의 또는 거부하거나 사용자가 동의하도록 허용할 수 있습니다. 사용자에게는 관리자가 허용하는 경우에만 동의하는 옵션이 제공됩니다.
>
>

이 시점에서 사용자는 자신의 자격 증명을 입력하고 Azure Portal에서 앱이 요청한 권한에 동의하도록 요청받습니다. 사용자가 동의를 인증하고 승인하면 Azure AD에서 코드와 함께 요청한 `redirect_uri` 주소로 앱에 응답을 보냅니다.

### <a name="successful-response"></a>성공적인 응답
성공적인 응답은 다음과 같습니다.

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| 매개 변수 | Description |
| --- | --- |
| admin_consent |관리자가 동의 요청 프롬프트에 동의한 경우 값은 True입니다. |
| 코드 |애플리케이션에서 요청한 인증 코드입니다. 애플리케이션은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. |
| session_state |현재 사용자 세션을 식별하는 고유 값입니다. 이 값은 GUID이지만 검사 없이 전달되는 불투명 값으로 처리되어야 합니다. |
| state |요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 응답을 사용하기 전에 애플리케이션에서 요청 및 응답의 상태 값이 동일한지 확인하는 것이 좋습니다. 이 값은 클라이언트에 대한 [교차 사이트 요청 위조(CSRF) 공격](https://tools.ietf.org/html/rfc6749#section-10.12) 을 감지하는 데 도움이 됩니다. |

### <a name="error-response"></a>오류 응답
애플리케이션이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | Description |
| --- | --- |
| error |[OAuth 2.0 권한 부여 프레임워크](https://tools.ietf.org/html/rfc6749)의 섹션 5.2에 정의된 오류 코드 값입니다. 다음 테이블은 Azure AD가 반환하는 오류 코드를 설명합니다. |
| error_description |오류에 대한 자세한 설명입니다. 이 메시지는 최종 사용자에게 친숙한 내용이 아닙니다. |
| state |상태 값은 교차 사이트 요청 위조(CSRF) 공격을 방지하기 위해 요청에서 전송하고 응답에서 반환하는 무작위로 생성되고 다시 사용되지 않는 값입니다. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>권한 부여 엔드포인트 오류에 대한 오류 코드
다음 테이블은 오류 응답의 `error` 매개 변수에 반환될 수 있는 여러 오류 코드를 설명합니다.

| 오류 코드 | Description | 클라이언트 작업 |
| --- | --- | --- |
| invalid_request |프로토콜 오류(예: 필수 매개 변수 누락). |요청을 수정하여 다시 제출하십시오. 일반적으로 초기 테스트 중에 발견되는 개발 오류입니다. |
| unauthorized_client |클라이언트 애플리케이션이 인증 코드를 요청할 수 없습니다. |이 오류는 일반적으로 클라이언트 애플리케이션이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| access_denied |리소스 소유자가 동의 거부 |클라이언트 애플리케이션이 사용자의 동의를 받지 않으면 계속 진행할 수 없다고 알릴 수 있습니다. |
| unsupported_response_type |권한 부여 서버가 요청에 해당 응답 형식을 지원하지 않습니다. |요청을 수정하여 다시 제출하십시오. 일반적으로 초기 테스트 중에 발견되는 개발 오류입니다. |
| server_error |서버에 예기치 않은 오류가 발생했습니다. |요청을 다시 시도하십시오. 이러한 오류는 일시적인 상태 때문에 발생할 수 있습니다. 클라이언트 애플리케이션이 일시적 오류 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| temporarily_unavailable |서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. |요청을 다시 시도하십시오. 클라이언트 애플리케이션이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| invalid_resource |대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. |리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>인증 코드를 사용하여 액세스 토큰 요청
인증 코드를 획득하고 사용자가 사용 권한을 부여했으므로 이제 POST 요청을 `/token` 엔드포인트로 보내 액세스 토큰에 대한 코드를 원하는 리소스에 대한 코드로 교환할 수 있습니다.

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| 매개 변수 |  | Description |
| --- | --- | --- |
| tenant |required |요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 테넌트 독립 토큰에 대한 테넌트 식별자(예: `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com`, `common`)입니다. |
| client_id |required |Azure AD에 등록할 때 앱에 할당된 애플리케이션 ID입니다. Azure Portal에서 이러한 값을 확인할 수 있습니다. 애플리케이션 ID가 앱 등록의 설정에 표시됩니다. |
| grant_type |required |인증 코드 흐름에 대한 `authorization_code` 여야 합니다. |
| 코드 |required |이전 섹션에서 획득한 `authorization_code` 입니다. |
| redirect_uri |required | 클라이언트 응용 프로그램에 등록 된 `redirect_uri`입니다. |
| client_secret |웹앱에 필요하지만 공용 클라이언트에 허용되지 않습니다. |**키** 아래에 있는 앱에 대해 Azure Portal에서 만든 애플리케이션 비밀입니다. 디바이스에 client_secret을 안정적으로 저장할 수 없으므로 네이티브 앱(공용 클라이언트)에서는 사용할 수 없습니다. 서버 쪽에서 `client_secret`을 안전하게 저장할 수 있는 웹앱 및 Web API(모두 기밀 클라이언트)에 필요합니다. client_secret는 전송되기 전에 인코딩된 URL이어야 합니다. |
| resource | 권장 |대상 웹 API의 앱 ID URI(보안 리소스)입니다. 앱 ID URI을 찾으려면 Azure Portal에서 **Azure Active Directory**, **애플리케이션 등록**을 차례로 클릭하고, 서비스 애플리케이션의 **설정** 페이지를 연 다음, **속성**을 클릭합니다. `https://graph.microsoft.com`과 같은 외부 리소스일 수도 있습니다. 이는 권한 부여 또는 토큰 요청 중 하나에서 필요합니다. 인증 프롬프트의 수를 줄이기 위해 사용자의 동의를 받을 수 있도록 하는 권한 부여 요청에 배치합니다. 권한 부여 요청과 토큰 요청 모두에서 리소스의 매개 변수가 일치해야 합니다. | 
| code_verifier | 선택 사항 | authorization_code를 얻는 데 사용된 동일한 code_verifier입니다. 인증 코드 부여 요청에 PKCE가 사용된 경우에는 필수입니다. 자세한 내용은 [PKCE RFC](https://tools.ietf.org/html/rfc7636)를 참조하세요.   |

앱 ID URI을 찾으려면 Azure Portal에서 **Azure Active Directory**, **애플리케이션 등록**을 차례로 클릭하고, 서비스 애플리케이션의 **설정** 페이지를 연 다음, **속성**을 클릭합니다.

### <a name="successful-response"></a>성공적인 응답
Azure AD는 성공적인 응답 시 [액세스 토큰](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 반환합니다. 클라이언트 애플리케이션에서의 네트워크 호출 및 그와 연결된 대기 시간을 최소화하려면 클라이언트 애플리케이션이 OAuth 2.0 응답에 지정된 토큰 수명에 대한 액세스 토큰을 캐시해야 합니다. 토큰 수명을 결정하려면 `expires_in` 또는 `expires_on` 매개 변수 값을 사용합니다.

웹 API 리소스가 `invalid_token` 오류 코드를 반환하는 경우, 이는 리소스가 토큰이 만료되었다고 결정했음을 나타낼 수 있습니다. 클라이언트 및 리소스 시계 시간이 서로 다른 경우("시간차"라 함) 리소스는 토큰을 클라이언트 캐시에서 지우기 전에 토큰이 만료된 것으로 인식할 수 있습니다. 이 문제가 발생하면 계산된 토큰 수명이 아직 남아 있더라도 토큰을 캐시에서 지웁니다.

성공적인 응답은 다음과 같습니다.

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| 매개 변수 | Description |
| --- | --- |
| access_token |요청된 액세스 토큰입니다.  불투명 문자열입니다 .이는 리소스가 수신 해야 하는 항목에 따라 다르며 클라이언트에서 볼 수 있는 것은 아닙니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스에 인증할 수 있습니다. |
| token_type |토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| expires_on |액세스 토큰이 만료되는 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. 이 값은 캐시된 토큰의 수명을 결정하는 데 사용됩니다. |
| resource |웹 API (보안 리소스)의 앱 ID URI. |
| scope |클라이언트 애플리케이션에 부여된 권한을 가장합니다. 기본 권한은 `user_impersonation`입니다. 보안 리소스의 소유자는 Azure AD에서 추가 값을 등록할 수 있습니다. |
| refresh_token |OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 액세스 토큰이 만료된 후 이 토큰을 사용하여 추가 액세스 토큰을 획득할 수 있습니다. 새로 고침 토큰은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. |
| id_token |[ID 토큰](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 나타내는, 서명되지 않은 JWT(JSON Web Token)입니다. 앱은 이 토큰의 세그먼트를 base64Url로 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. |

JSON 웹 토큰에 대한 자세한 내용은 [JWT IETF 초안 사양](https://go.microsoft.com/fwlink/?LinkId=392344)을 참조하세요.   `id_tokens`에 대한 자세한 내용은 [v1.0 OpenID Connect 흐름](v1-protocols-openid-connect-code.md)을 참조하세요.

### <a name="error-response"></a>오류 응답
토큰 발급 엔드포인트 오류는 클라이언트가 토큰 발급 엔드포인트를 직접 호출하기 때문에 발생한 HTTP 오류 코드입니다. Azure AD 발급 엔드포인트는 HTTP 상태 코드 외에 오류를 설명하는 개체가 포함된 JSON 문서도 반환합니다.

샘플 오류 응답은 다음과 같습니다.

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| 매개 변수 | Description |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| error_codes |진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다. |
| timestamp |오류가 발생한 시간입니다. |
| trace_id |진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| correlation_id |여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

#### <a name="http-status-codes"></a>HTTP 상태 코드
다음 테이블은 토큰 발급 엔드포인트가 반환하는 HTTP 상태 코드를 나열합니다. 오류 코드가 응답을 설명하기에 충분한 경우도 있지만 오류의 경우 함께 제공되는 JSON 문서를 구문 분석하여 오류 코드를 검사해야 합니다.

| HTTP 코드 | Description |
| --- | --- |
| 400 |기본 HTTP 코드입니다. 대부분의 경우에 사용되며 일반적으로 잘못된 형식의 요청으로 인해 발생합니다. 요청을 수정하여 다시 제출하십시오. |
| 401 |인증에 실패했습니다. 예를 들어 요청에 client_secret 매개 변수가 누락되었습니다. |
| 403 |권한 부여에 실패했습니다. 예를 들어 사용자가 리소스에 액세스할 수 있는 사용 권한이 없습니다. |
| 500 |서비스에서 내부 오류가 발생했습니다. 요청을 다시 시도하십시오. |

#### <a name="error-codes-for-token-endpoint-errors"></a>토큰 엔드포인트 오류에 대한 오류 코드
| 오류 코드 | Description | 클라이언트 작업 |
| --- | --- | --- |
| invalid_request |프로토콜 오류(예: 필수 매개 변수 누락). |요청을 수정하여 다시 제출 |
| invalid_grant |권한 부여 코드가 잘못되었거나 만료되었습니다. |`/authorize` 엔드포인트에 새 요청을 시도합니다. |
| unauthorized_client |인증된 클라이언트가 이 권한 부여 유형을 사용할 권한이 없습니다. |이 오류는 일반적으로 클라이언트 애플리케이션이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| invalid_client |클라이언트 인증에 실패했습니다. |클라이언트 자격 증명이 잘못되었습니다. 해결하려면 애플리케이션 관리자가 자격 증명을 업데이트합니다. |
| unsupported_grant_type |권한 부여 서버가 해당 권한 부여 유형을 지원하지 않습니다. |요청에서 권한 부여 유형을 변경하십시오. 이 유형의 오류는 개발 중에만 발생하며 초기 테스트 중에 검색됩니다. |
| invalid_resource |대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다. |리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 애플리케이션이 사용자에게 애플리케이션을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| interaction_required |요청을 위해 사용자 상호 작용이 필요합니다. 예를 들어 추가 인증 단계가 필요합니다. | 동일한 리소스에 대해 비대화형 요청 대신, 대화형 권한 부여 요청을 다시 시도해 보세요. |
| temporarily_unavailable |서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. |요청을 다시 시도하십시오. 클라이언트 애플리케이션이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |

## <a name="use-the-access-token-to-access-the-resource"></a>리소스에 액세스하는 데 액세스 토큰 사용
`access_token`을(를) 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 Web API에 대한 요청에 토큰을 사용할 수 있습니다. [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) 사양에서는 HTTP 요청에 전달자 토큰을 사용하여 보호된 리소스에 액세스하는 방법을 설명합니다.

### <a name="sample-request"></a>샘플 요청
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>오류 응답
RFC 6750 문제 HTTP 상태 코드를 구현하는 보안된 리소스입니다. 요청이 인증 자격 증명을 포함하지 않거나 토큰이 누락된 경우 응답에 `WWW-Authenticate` 헤더가 포함됩니다. 요청이 실패한 경우 리소스 서버가 HTTP 상태 코드 및 오류 코드로 응답합니다.

다음은 클라이언트 요청에 전달자 토큰이 포함되지 않은 경우 성공하지 못한 응답의 예입니다.

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>오류 매개 변수
| 매개 변수 | Description |
| --- | --- |
| authorization_uri |권한 부여 서버의 URI(실제 엔드포인트)입니다. 이 값은 검색 엔드포인트에서 서버에 관한 자세한 정보를 가져오기 위한 조회 키로도 사용됩니다. <p><p> 클라이언트는 권한 부여 서버가 신뢰할 수 있는지 확인해야 합니다. 리소스가 Azure AD에 의해 보호되는 경우 이 값은 URL이 https://login.microsoftonline.com 또는 Azure AD가 지원하는 다른 호스트 이름으로 시작하는지 확인하기에 충분합니다. 테넌트별 리소스는 언제나 테넌트별 권한 부여 URI를 반환해야 합니다. |
| error |[OAuth 2.0 권한 부여 프레임워크](https://tools.ietf.org/html/rfc6749)의 섹션 5.2에 정의된 오류 코드 값입니다. |
| error_description |오류에 대한 자세한 설명입니다. 이 메시지는 최종 사용자에게 친숙한 내용이 아닙니다. |
| resource_id |리소스의 고유 ID를 반환합니다. 클라이언트 애플리케이션은 리소스에 대한 토큰을 요청할 때 이 ID를 `resource` 매개 변수의 값으로 사용할 수 있습니다. <p><p> 클라이언트 애플리케이션이 이 값을 확인하는 것이 중요합니다. 그렇지 않으면 악성 서비스가 **권한 상승** 공격을 유도할 수 있습니다. <p><p> 공격을 방지하기 위해 권장되는 전략은 `resource_id`이(가) 액세스 중인 웹 API URL의 기본과 일치하는지 확인하는 것입니다. 예를 들어 https://service.contoso.com/data에 액세스하는 경우 `resource_id`는 https://service.contoso.com/일 수 있습니다. 클라이언트 애플리케이션은 ID를 확인하는 신뢰할 수 있는 대체 방법이 있지 않은 한 기본 URL로 시작하지 않는 `resource_id`를 거부해야 합니다. |

#### <a name="bearer-scheme-error-codes"></a>전달자 체계 오류 코드
RFC 6750 사양은 응답에 WWW-authenticate 헤더와 전달자 체계를 사용하는 리소스에 대한 다음과 같은 오류를 정의합니다.

| HTTP 상태 코드 | 오류 코드 | Description | 클라이언트 작업 |
| --- | --- | --- | --- |
| 400 |invalid_request |요청의 형식이 잘못되었습니다. 예를 들어 매개 변수가 누락되었거나 동일한 매개 변수를 두 번 사용 중일 수 있습니다. |오류를 해결하고 요청을 다시 시도하십시오. 이 유형의 오류는 개발 중에만 발생하며 초기 테스트에서 검색됩니다. |
| 401 |invalid_token |액세스 토큰이 누락되었거나 잘못되었거나 해지되었습니다. error_description 매개 변수의 값이 추가 세부 정보를 제공합니다. |권한 부여 서버에서 새 토큰을 요청합니다. 새 토큰이 실패한 경우 예기치 않은 오류가 발생했습니다. 오류 메시지를 사용자에게 보내고 무작위 지연 시간 후에 다시 시도하시시오. |
| 403 |insufficient_scope |액세스 토큰에 리소스에 액세스하는 데 필요한 가장 권한이 포함되어 있지 않습니다. |권한 부여 엔드포인트에 새 권한 부여 요청을 보냅니다. 응답에 범위 매개 변수가 포함된 경우 리소스 요청에 해당 범위 값을 사용합니다. |
| 403 |insufficient_access |토큰의 제목에 리소스에 액세스하는 데 필요한 사용 권한이 업습니다. |사용자에게 다른 계정을 사용하거나 지정된 리소스에 대한 사용 권한을 요청하라는 메시지를 표시합니다. |

## <a name="refreshing-the-access-tokens"></a>액세스 토큰 새로 고침

액세스 토큰은 수명이 짧으며, 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 다른 `access_token` 요청을 `POST` 엔드포인트에 제출하여 `/token`을(를) 새로 고칠 수 있지만 이번에는 `refresh_token` 대신 `code`을(를) 제공해야 합니다.  새로 고침 토큰은 클라이언트가 이미 액세스 동의를 받은 모든 리소스에 유효합니다. 따라서 `resource=https://graph.microsoft.com`에 대한 요청에서 발행된 새로 고침 토큰을 사용하여 `resource=https://contoso.com/api`에 대한 새 액세스 토큰을 요청할 수 있습니다. 

새로 고침 토큰에는 지정된 수명이 없습니다. 일반적으로 새로 고침 토큰의 수명은 비교적 깁니다. 그러나 새로 고침 토큰이 만료되거나 해지되거나 원하는 작업을 위한 충분한 권한이 없는 경우가 있습니다. 애플리케이션은 토큰 발급 엔드포인트에서 반환하는 오류를 예상하고 정확히 처리해야 합니다.

새로 고침 토큰 오류가 포함된 응답을 받으면 현재 새로 고침 토큰을 삭제하고 새 인증 코드 또는 액세스 토큰을 요청합니다. 특히 인증 코드 부여 흐름에 새로 고침 토큰을 사용하는 경우 `interaction_required` 또는 `invalid_grant` 오류 코드가 포함된 응답을 받으면 새로 고침 토큰을 삭제하고 새 인증 코드를 요청합니다.

새로 고침 토큰을 사용하여 새 액세스 토큰을 얻는 **테넌트별** 엔드포인트(**common** 엔드포인트도 사용 가능)에 대한 샘플 요청은 다음과 같습니다.

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>성공적인 응답
성공적인 토큰 응답은 다음과 같습니다.

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| 매개 변수 | Description |
| --- | --- |
| token_type |토큰 유형입니다. 현재 지원되는 유일한 값은 **bearer**입니다. |
| expires_in |토큰의 남은 수명(초)입니다. 일반적인 값은 3600(한 시간)입니다. |
| expires_on |토큰이 만료되는 날짜 및 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. |
| resource |액세스하는 데 액세스 토큰을 사용할 수 있는 보안 리소스를 식별합니다. |
| scope |네이티브 클라이언트 애플리케이션에 부여된 권한을 가장합니다. 기본 권한은 **user_impersonation**입니다. 대상 리소스의 소유자는 Azure AD에서 대체 값을 등록할 수 있습니다. |
| access_token |요청된 새 액세스 토큰입니다. |
| refresh_token |이 응답에 있는 토큰이 만료될 때 새 액세스 토큰을 요청하는 데 사용할 수 있는 새 OAuth 2.0 refresh_token입니다. |

### <a name="error-response"></a>오류 응답
샘플 오류 응답은 다음과 같습니다.

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| 매개 변수 | Description |
| --- | --- |
| error |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error_description |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| error_codes |진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다. |
| timestamp |오류가 발생한 시간입니다. |
| trace_id |진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| correlation_id |여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

오류 코드 및 권장되는 클라이언트 작업에 대한 설명은 [토큰 엔드포인트 오류에 대한 오류 코드](#error-codes-for-token-endpoint-errors)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure AD v2.0 끝점에 대 한 자세한 내용과 웹 응용 프로그램 및 웹 Api에 인증 및 권한 부여를 추가 하는 방법에 대 한 자세한 내용은 [샘플 응용 프로그램](sample-v1-code.md)을 참조 하세요.
