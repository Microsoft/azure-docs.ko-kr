---
title: OAuth 2.0을 사용 하 여 서비스 간 인증 흐름 Microsoft Docs
description: 이 문서에서는 OAuth 2.0 On-Behalf-Of 흐름을 사용하여 서비스 간 인증을 구현하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.openlocfilehash: fbef4d84e86c3d35e35a8bbeb04a399ad87a1b89
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164085"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>On-Behalf-Of 흐름에서 위임된 사용자 ID를 사용하는 서비스 간 호출

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth 2.0 OBO(On-Behalf-Of) 흐름을 사용하면 사용자 인증을 다른 서비스 또는 웹 API로 전송하기 위해 서비스 또는 웹 API 호출하는 애플리케이션을 사용할 수 있습니다. OBO 흐름은 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파합니다. 중간 계층 서비스가 다운스트림 서비스에 대해 인증된 요청을 수행하도록 하려면 사용자를 대신하여 Azure AD(Azure Active Directory)에서 액세스 토큰을 보호해야 합니다.

> [!IMPORTANT]
> 2018년 5월을 기준으로 `id_token`은 On-Behalf-Of 흐름에 사용할 수 없습니다.  SPA(단일 페이지 앱)는 OBO 흐름을 수행하려면 중간 계층 기밀 클라이언트에 액세스 토큰을 전달해야 합니다. On-Behalf-Of 호출을 수행할 수 있는 클라이언트에 대한 자세한 내용은 [제한](#client-limitations)을 참조하세요.

## <a name="on-behalf-of-flow-diagram"></a>On-Behalf-Of 흐름 다이어그램

[OAuth 2.0 권한 부여 코드 부여 흐름](v1-protocols-oauth-code.md)을 사용하는 애플리케이션에 대해 사용자를 인증한 후 OBO 흐름이 시작됩니다. 이 시점에 애플리케이션은 액세스 토큰(토큰 A)을 사용자의 클레임 및 중간 계층 웹 API(API A)에 액세스하는 데 필요한 동의를 포함하는 API A에 전송합니다. 다음으로, API A는 다운스트림 웹 API(API B)에 인증된 요청을 수행합니다.

이러한 단계는 단계별 흐름을 구성 합니다. ![는 OAuth 2.0 for flow의 단계를 보여 줍니다](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. 클라이언트 애플리케이션은 토큰 A와 함께 API A에 요청합니다.
1. API A는 Azure AD 토큰 발급 엔드포인트를 인증하고 API B에 액세스하기 위해 토큰을 요청합니다.
1. Azure AD 토큰 발급 엔드포인트는 토큰 A와 함께 API A의 자격 증명의 유효성을 검사하고 API B(토큰 B)에 대한 액세스 토큰을 발급합니다.
1. API B에 대한 요청에는 권한 부여 헤더의 토큰 B가 포함됩니다.
1. API B는 보안 리소스에서 데이터를 반환합니다.

>[!NOTE]
>다운스트림 서비스에 대한 토큰을 요청하는 데 사용된 액세스 토큰의 대상 클레임은 OBO 요청을 만드는 서비스의 ID여야 합니다. 토큰은 Azure Active Directory 글로벌 서명 키로 서명해야 합니다(이 키는 포털에서 **앱 등록**을 통해 등록된 애플리케이션의 기본값입니다).

## <a name="register-the-application-and-service-in-azure-ad"></a>Azure AD에서 애플리케이션 및 서비스 등록

중간 계층 서비스 및 클라이언트 애플리케이션을 모두 Azure AD에 등록합니다.

### <a name="register-the-middle-tier-service"></a>중간 계층 서비스 등록

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 위쪽 표시줄에서 계정을 선택하고 **디렉터리** 목록에서 검색하여 애플리케이션용 Active Directory 테넌트를 선택합니다.
1. 왼쪽 창에서 **더 많은 서비스**를 선택하고 **Azure Active Directory**를 선택합니다.
1. **앱 등록** , **새 등록**을 차례로 선택 합니다.
1. 애플리케이션의 이름을 입력하고 애플리케이션 형식을 선택합니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
1. 기본 URL에 대 한 리디렉션 URI를 설정 합니다.
1. **등록**을 선택하여 애플리케이션을 만듭니다.
1. Azure Portal을 종료하기 전에 클라이언트 비밀을 생성합니다.
1. Azure Portal에서 응용 프로그램을 선택 하 고 **인증서 & 암호**를 선택 합니다.
1. **새 클라이언트 암호** 를 선택 하 고 1 년 또는 2 년의 기간을 사용 하 여 암호를 추가 합니다.
1. 이 페이지를 저장 하면 Azure Portal 비밀 값이 표시 됩니다. 비밀 값을 복사 하 여 안전한 위치에 저장 합니다.

> [!IMPORTANT]
> 구현에서 응용 프로그램 설정을 구성 하려면 암호가 필요 합니다. 이 비밀 값은 다시 표시 되지 않으며 다른 수단으로 검색할 수 없습니다. Azure Portal에 표시되자마자 이 키 값을 기록합니다.

### <a name="register-the-client-application"></a>클라이언트 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 위쪽 표시줄에서 계정을 선택하고 **디렉터리** 목록에서 검색하여 애플리케이션용 Active Directory 테넌트를 선택합니다.
1. 왼쪽 창에서 **더 많은 서비스**를 선택하고 **Azure Active Directory**를 선택합니다.
1. **앱 등록** , **새 등록**을 차례로 선택 합니다.
1. 애플리케이션의 이름을 입력하고 애플리케이션 형식을 선택합니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
1. 기본 URL에 대 한 리디렉션 URI를 설정 합니다.
1. **등록**을 선택하여 애플리케이션을 만듭니다.
1. 애플리케이션에 대한 권한을 구성합니다. **Api 권한**에서 **사용 권한 추가** 를 선택 하 고 **내 api**를 선택 합니다.
1. 텍스트 필드에 중간 계층 서비스의 이름을 입력합니다.
1. **권한 선택** 을 선택 하 고 **서비스 이름 > \<액세스**를 선택 합니다.

### <a name="configure-known-client-applications"></a>알려진 클라이언트 애플리케이션 구성

이 시나리오에서는 사용자 상호 작용 없이 다운스트림 API에 액세스하려면 중간 계층 서비스에서 사용자 동의를 얻어야 합니다. 다운스트림 API에 대한 액세스 권한을 부여하기 위한 옵션은 인증 과정에서 동의 단계의 일부로 미리 제공되어야 합니다.

중간 계층 서비스의 등록을 사용하여 Azure AD에서 클라이언트 앱 등록을 명시적으로 바인딩하려면 다음 단계를 따릅니다. 이 작업을 통해 클라이언트와 중간 계층에서 필요한 동의가 단일 대화 상자로 병합됩니다.

1. 중간 계층 서비스 등록으로 이동하고 **매니페스트**를 선택하여 매니페스트 편집기를 엽니다.
1. `knownClientApplications` 배열 속성을 찾고 클라이언트 애플리케이션의 클라이언트 ID를 요소로 추가합니다.
1. **저장**을 선택하여 매니페스트를 저장합니다.

## <a name="service-to-service-access-token-request"></a>서비스 간 액세스 토큰 요청

액세스 토큰을 요청하려면 다음 매개 변수로 테넌트별 Azure AD 엔드포인트에 HTTP POST를 만듭니다.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

클라이언트 애플리케이션은 공유 비밀 또는 인증서로 보안이 설정됩니다.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용한 액세스 토큰 요청

공유 암호를 사용할 경우 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | Description |
| --- | --- | --- |
| grant_type |required | 토큰 요청의 형식입니다. OBO 요청은 JWT(JSON Web Token)을 사용하므로 값은 **urn:ietf:params:oauth:grant-type:jwt-bearer**이어야 합니다. |
| 어설션 |required | 요청에 사용된 액세스 토큰 값입니다. |
| client_id |required | Azure AD에 등록하는 동안 호출 서비스에 할당된 앱 ID입니다. Azure Portal에서 앱 ID를 찾으려면 **Active Directory**, 디렉터리 및 애플리케이션 이름을 차례로 선택합니다. |
| client_secret |required | Azure AD에서 서비스를 호출하기 위해 등록된 키입니다. 이 값은 등록 시 메모해 두어야 합니다. |
| resource |required | 수신 서비스(보안 리소스)의 앱 ID URI입니다. Azure Portal에서 앱 ID URI를 찾으려면 **Active Directory** 및 디렉터리를 차례로 선택합니다. 애플리케이션 이름, **모든 설정** 및 **속성**을 차례로 선택합니다. |
| requested_token_use |required | 요청 처리 방법을 지정합니다. On-Behalf-Of 흐름에서 이 값은 **on_behalf_of**여야 합니다. |
| scope |required | 토큰 요청에 대해 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우, 범위 **openid**를 지정해야 합니다.|

#### <a name="example"></a>예제

다음 HTTP POST는 https://graph.windows.net 웹 API용 액세스 토큰을 요청합니다. `client_id`는 액세스 토큰을 요청하는 서비스를 식별합니다.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용한 액세스 토큰 요청

인증서를 사용한 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | Description |
| --- | --- | --- |
| grant_type |required | 토큰 요청의 형식입니다. OBO 요청은 JWT 액세스 토큰을 사용하므로 값은 **urn:ietf:params:oauth:grant-type:jwt-bearer**이어야 합니다. |
| 어설션 |required | 요청에 사용된 토큰 값입니다. |
| client_id |required | Azure AD에 등록하는 동안 호출 서비스에 할당된 앱 ID입니다. Azure Portal에서 앱 ID를 찾으려면 **Active Directory**, 디렉터리 및 애플리케이션 이름을 차례로 선택합니다. |
| client_assertion_type |required |값은 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`이어야 합니다. |
| client_assertion |required | 애플리케이션의 자격 증명으로 등록한 인증서를 사용하여 만들고 서명하는 JSON Web Token입니다. 인증서 등록 방법 및 어설션 형식에 대한 자세한 내용은 [인증서 자격 증명](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요.|
| resource |required | 수신 서비스(보안 리소스)의 앱 ID URI입니다. Azure Portal에서 앱 ID URI를 찾으려면 **Active Directory** 및 디렉터리를 차례로 선택합니다. 애플리케이션 이름, **모든 설정** 및 **속성**을 차례로 선택합니다. |
| requested_token_use |required | 요청 처리 방법을 지정합니다. On-Behalf-Of 흐름에서 이 값은 **on_behalf_of**여야 합니다. |
| scope |required | 토큰 요청에 대해 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우, 범위 **openid**를 지정해야 합니다.|

이러한 매개 변수는 `client_secret parameter`가 `client_assertion_type` 및 `client_assertion` 두 매개 변수로 대체되는 경우를 제외하고 공유된 비밀을 통한 요청과 거의 동일합니다.

#### <a name="example"></a>예제

다음 HTTP POST는 인증서가 있는 https://graph.windows.net 웹 API용 액세스 토큰을 요청합니다. `client_id`는 액세스 토큰을 요청하는 서비스를 식별합니다.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>서비스 간 액세스 토큰 응답

성공 응답은 다음 매개 변수가 있는 JSON OAuth 2.0 응답입니다.

| 매개 변수 | Description |
| --- | --- |
| token_type |토큰 유형 값을 나타냅니다. Azure AD는 **전달자**유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| scope |토큰에 부여된 액세스 범위입니다. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| expires_on |액세스 토큰이 만료되는 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. 이 값은 캐시된 토큰의 수명을 결정하는 데 사용됩니다. |
| resource |수신 서비스(보안 리소스)의 앱 ID URI입니다. |
| access_token |요청된 액세스 토큰입니다. 호출 서비스는 이 토큰을 사용하여 수신 서비스에 인증할 수 있습니다. |
| id_token |요청된 ID 토큰입니다. 호출 서비스는 이 토큰을 사용하여 사용자 ID를 확인하고 사용자와 함께 세션을 시작할 수 있습니다. |
| refresh_token |요청된 액세스 토큰에 대한 새로 고침 토큰입니다. 호출 서비스는 이 토큰을 사용하여 현재 액세스 토큰이 만료된 후 다른 액세스 토큰을 요청할 수 있습니다. |

### <a name="success-response-example"></a>성공 응답 예제

다음 예제에서는 https://graph.windows.net 웹 API용 액세스 토큰 요청에 대한 성공 응답을 보여 줍니다.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>오류 응답 예제

Azure AD 토큰 끝점은 조건부 액세스 정책 (예: multi-factor authentication)을 사용 하 여 설정 된 다운스트림 API에 대 한 액세스 토큰을 획득 하려고 할 때 오류 응답을 반환 합니다. 클라이언트 응용 프로그램이 조건부 액세스 정책을 충족 하는 사용자 조작을 제공할 수 있도록 중간 계층 서비스에서이 오류를 클라이언트 응용 프로그램에 노출 해야 합니다.

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

중간 계층 서비스는 `Authorization` 헤더에서 토큰을 설정하여 다운스트림 웹 API에 대해 인증된 요청을 수행하려면 획득한 액세스 토큰을 사용할 수 있습니다.

### <a name="example"></a>예제

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>OAuth2.0 OBO 흐름을 통해 획득한 SAML 어설션

일부 OAuth 기반 웹 서비스는 비대화형 흐름에서 SAML 어설션을 수락하는 다른 웹 서비스 API에 액세스해야 합니다. Azure Active Directory는 SAML 기반 웹 서비스를 사용하는 On-Behalf-Of 흐름에 대응하여 SAML 어설션을 대상 리소스로 제공할 수 있습니다.

>[!NOTE]
>이 어설션은 OAuth2 기반 애플리케이션이 SAML 토큰을 사용하는 웹 서비스 API 엔드포인트에 액세스할 수 있는 OAuth 2.0 On-Behalf-Of 흐름에 대한 비표준 확장입니다.

> [!TIP]
> 프런트 엔드 웹 애플리케이션에서 SAML로 보호되는 웹 서비스를 호출하는 경우 간단히 API를 호출하고 사용자 기존 세션을 사용하여 일반적인 대화형 인증 흐름을 시작할 수 있습니다. 서비스 간 호출이 사용자 컨텍스트를 제공하기 위해 SAML 토큰을 요구할 경우에만 OBO 흐름을 사용해야 합니다.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>공유 비밀을 통해 OBO 요청을 사용하여 SAML 토큰 가져오기

SAML 어설션에 대한 서비스 간 요청에는 다음 매개 변수가 포함됩니다.

| 매개 변수 |  | Description |
| --- | --- | --- |
| grant_type |required | 토큰 요청의 형식입니다. JWT를 사용하는 요청의 경우 값은 **urn:ietf:params:oauth:grant-type:jwt-bearer**이어야 합니다. |
| 어설션 |required | 요청에 사용된 액세스 토큰 값입니다.|
| client_id |required | Azure AD에 등록하는 동안 호출 서비스에 할당된 앱 ID입니다. Azure Portal에서 앱 ID를 찾으려면 **Active Directory**, 디렉터리 및 애플리케이션 이름을 차례로 선택합니다. |
| client_secret |required | Azure AD에서 서비스를 호출하기 위해 등록된 키입니다. 이 값은 등록 시 메모해 두어야 합니다. |
| resource |required | 수신 서비스(보안 리소스)의 앱 ID URI입니다. SAML 토큰의 대상이 될 리소스입니다. Azure Portal에서 앱 ID URI를 찾으려면 **Active Directory** 및 디렉터리를 차례로 선택합니다. 애플리케이션 이름, **모든 설정** 및 **속성**을 차례로 선택합니다. |
| requested_token_use |required | 요청 처리 방법을 지정합니다. On-Behalf-Of 흐름에서 이 값은 **on_behalf_of**여야 합니다. |
| requested_token_type | required | 요청된 토큰의 형식을 지정합니다. 값은 액세스된 리소스의 요구 사항에 따라 **urn:ietf:params:oauth:token-type:saml2** 또는 **urn:ietf:params:oauth:token-type:saml1**입니다. |

응답에는 UTF8 및 Base64url로 인코딩된 SAML 토큰이 포함됩니다.

- **OBO 호출에서 소싱된 SAML 어설션에 대한 SubjectConfirmationData**: 대상 애플리케이션에 **SubjectConfirmationData**의 받는 사람 값이 필요한 경우 값은 리소스 애플리케이션 구성에서 비와일드카드 회신 URL이어야 합니다.
- **SubjectConfirmationData 노드**: 이 노드는 SAML 응답에 속하지 않으므로 **InResponseTo** 특성을 포함할 수 없습니다. SAML 토큰을 수신하는 애플리케이션은 **InResponseTo** 특성이 없는 SAML 어설션을 수락할 수 있어야 합니다.

- **동의**: OAuth 흐름에서 사용자 데이터를 포함하는 SAML 토큰을 수락하려면 동의가 부여되어야 합니다. 사용 권한 및 관리자 동의를 가져오는 방법에 대한 내용은 [Azure Active Directory v1.0 엔드포인트의 사용 권한 및 동의](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent)를 참조하세요.

### <a name="response-with-saml-assertion"></a>SAML 어설션을 사용하여 응답

| 매개 변수 | Description |
| --- | --- |
| token_type |토큰 유형 값을 나타냅니다. Azure AD는 **전달자**유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| scope |토큰에 부여된 액세스 범위입니다. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| expires_on |액세스 토큰이 만료되는 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. 이 값은 캐시된 토큰의 수명을 결정하는 데 사용됩니다. |
| resource |수신 서비스(보안 리소스)의 앱 ID URI입니다. |
| access_token |SAML 어설션을 반환하는 매개 변수입니다. |
| refresh_token |토큰 새로 고침. 호출 서비스는 이 토큰을 사용하여 현재 SAML 어설션이 만료된 후 다른 액세스 토큰을 요청할 수 있습니다. |

- token_type: 전달자
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- resource: `https://api.contoso.com`
- access_token: \<SAML 어설션\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<토큰 새로 고침\>

## <a name="client-limitations"></a>클라이언트 제한 사항

와일드카드 회신 URL이 있는 공용 클라이언트는 OBO 흐름에 대해 `id_token`을 사용할 수 없습니다. 그러나 기밀 클라이언트는 공용 클라이언트에 등록된 와일드카드 회신 URI가 있는 경우에도 암시적 권한 부여 흐름을 통해 획득한 **액세스** 토큰을 계속 회수할 수 있습니다.

## <a name="next-steps"></a>다음 단계

OAuth 2.0 프로토콜 및 클라이언트 자격 증명을 사용하는 서비스 간 인증을 수행하기 위한 다른 방법에 대해 자세히 알아봅니다.

* [Azure AD에서 OAuth 2.0 클라이언트 자격 증명 부여를 사용하여 서비스 간 인증](v1-oauth2-client-creds-grant-flow.md)
* [Azure AD의 OAuth 2.0](v1-protocols-oauth-code.md)
