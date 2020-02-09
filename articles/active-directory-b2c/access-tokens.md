---
title: 액세스 토큰 요청-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 액세스 토큰을 요청 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d808bca17012d46d1c32658ec0069ca2e2e6a3ac
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962036"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 액세스 토큰 요청

*액세스 토큰* 에는 Azure Active Directory B2C (Azure AD B2C)에서 api에 부여 된 사용 권한을 식별 하는 데 사용할 수 있는 클레임이 포함 되어 있습니다. 리소스 서버를 호출 하는 경우에는 HTTP 요청에 액세스 토큰이 있어야 합니다. 액세스 토큰은 Azure AD B2C 응답에서 **access_token** 로 표시 됩니다.

이 문서에서는 웹 응용 프로그램 및 web API에 대한 액세스 토큰을 요청 하는 방법을 보여 줍니다. Azure AD B2C 토큰에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요](tokens-overview.md)를 참조 하세요.

> [!NOTE]
> **Azure AD B2C에서는 Web API 체인(On-Behalf-Of)을 지원하지 않습니다.** -대부분의 아키텍처에는 다른 다운스트림 웹 API를 호출 해야 하는 web API가 포함 되어 있으며 둘 다 Azure AD B2C으로 보안이 유지 됩니다. 이 시나리오는 웹 API 백 엔드가 있는 클라이언트에서 일반적으로 다른 서비스를 호출 합니다. On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 JWT 전달자 자격 증명 권한 부여를 사용하여 이 연결된 웹 API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C에 구현되어 있지 않습니다.

## <a name="prerequisites"></a>필수 조건

- 사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).
- 아직 수행 하지 않은 경우 [Azure Active Directory B2C 테넌트에 WEB API 응용 프로그램을 추가](add-web-application.md)합니다.

## <a name="scopes"></a>범위

범위는 보호 된 리소스에 대한 사용 권한을 관리 하는 방법을 제공 합니다. 액세스 토큰이 요청 될 때 클라이언트 응용 프로그램은 요청의 **범위** 매개 변수에서 원하는 권한을 지정 해야 합니다. 예를 들어 `https://contoso.onmicrosoft.com/api`의 **앱 ID URI** 가 있는 API에 대한 `read` **범위 값** 을 지정 하려면 범위가 `https://contoso.onmicrosoft.com/api/read`됩니다.

범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 동일한 요청에서 여러 권한을 얻으려면 요청에서 단일 **범위** 매개 변수에 여러 항목을 공백으로 구분 하 여 추가할 수 있습니다.

다음 예제에서는 URL에서 디코딩된 범위를 보여 줍니다.

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

다음 예제에서는 URL에서 인코딩된 범위를 보여 줍니다.

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

클라이언트 응용 프로그램에 부여 된 것 보다 더 많은 범위를 요청 하는 경우 하나 이상의 사용 권한이 부여 되 면 호출이 성공 합니다. 결과 액세스 토큰의 **scp** 클레임은 성공적으로 부여 된 사용 권한으로 채워집니다. Openid connect Connect 표준은 몇 가지 특수 범위 값을 지정 합니다. 다음 범위는 사용자의 프로필에 액세스할 수 있는 권한을 나타냅니다.

- **openid connect** -ID 토큰을 요청 합니다.
- **offline_access** - [인증 코드 흐름](authorization-code-flow.md)을 사용 하 여 새로 고침 토큰을 요청 합니다.

`/authorize` 요청의 **response_type** 매개 변수에 `token`포함 된 경우 **범위** 매개 변수는 `openid` 및 `offline_access`이 아닌 하나 이상의 리소스 범위를 포함 해야 합니다. 그렇지 않으면 `/authorize` 요청이 실패 합니다.

## <a name="request-a-token"></a>토큰 요청

액세스 토큰을 요청 하려면 인증 코드가 필요 합니다. 다음은 인증 코드의 `/authorize` 엔드포인트에 대한 요청의 예입니다. 사용자 지정 도메인은 액세스 토큰과 함께 사용 하도록 지원 되지 않습니다. 요청 URL에서 tenant-name.onmicrosoft.com 도메인을 사용 합니다.

다음 예제에서는 다음 값을 바꿉니다.

- `<tenant-name>` - Azure AD B2C 테넌트의 이름.
- `<policy-name>` - 사용자 지정 정책 또는 사용자 흐름의 이름.
- `<application-ID>`-사용자 흐름을 지원 하기 위해 등록 한 웹 응용 프로그램의 응용 프로그램 식별자입니다.
- `<redirect-uri>` - 클라이언트 애플리케이션을 등록할 때 입력한 **리디렉션 URI**.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

인증 코드에 대한 응답은 다음 예제와 유사 해야 합니다.

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

인증 코드를 성공적으로 받은 후에는이를 사용 하 여 액세스 토큰을 요청할 수 있습니다.

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

다음 응답과 유사한 내용이 표시 되어야 합니다.

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

https://jwt.ms 를 사용 하 여 반환 된 액세스 토큰을 검사 하는 경우 다음 예제와 유사한 내용이 표시 되어야 합니다.

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C에서 토큰을 구성](configure-tokens.md) 하는 방법에 대해 알아봅니다.
