---
title: OWIN 기반 웹 Api를 b2clogin.com로 마이그레이션
titleSuffix: Azure AD B2C
description: 응용 프로그램을 b2clogin.com로 마이그레이션하는 동안 .NET web API를 사용 하 여 여러 토큰 발급자가 발급 한 토큰을 지 원하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e6a92d12e7f2f24289aafa7e4a9acc9edccbd34c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840403"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>OWIN 기반 web API를 b2clogin.com로 마이그레이션

이 문서에서는 [OWIN (Open Web Interface for .net)](http://owin.org/)를 구현 하는 웹 api에서 여러 토큰 발급자에 대한 지원을 사용 하도록 설정 하는 기술에 대해 설명 합니다. 여러 토큰 엔드포인트을 지 원하는 것은 Azure Active Directory B2C (Azure AD B2C) Api 및 해당 응용 프로그램을 *login.microsoftonline.com* 에서 *b2clogin.com*로 마이그레이션하는 경우에 유용 합니다.

API에서 b2clogin.com 및 login.microsoftonline.com 둘 다에 의해 발급 된 토큰을 허용 하는 지원을 추가 하 여 API에서 login.microsoftonline.com 발급 된 토큰에 대한 지원을 제거 하기 전에 웹 응용 프로그램을 단계적으로 마이그레이션할 수 있습니다.

다음 섹션에서는 Katana ( [MICROSOFT OWIN][katana] 미들웨어 구성 요소)를 사용 하는 web API에서 여러 발급자를 사용 하도록 설정 하는 방법의 예를 제공 합니다. 코드 예제는 Microsoft OWIN 미들웨어와 관련 되어 있지만 일반적인 기술은 다른 OWIN 라이브러리에 적용 해야 합니다.

> [!NOTE]
> 이 문서는 현재 배포 된 Api 및 `login.microsoftonline.com`를 참조 하 고 권장 되는 `b2clogin.com` 엔드포인트으로 마이그레이션하려는 응용 프로그램을 Azure AD B2C 고객을 대상으로 합니다. 새 응용 프로그램을 설정 하는 경우 지시 된 대로 [b2clogin.com](b2clogin.md) 를 사용 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 계속 하기 전에 다음 Azure AD B2C 리소스를 준비 해야 합니다.

* [사용자 흐름](tutorial-create-user-flows.md) 또는 테넌트에서 만든 [사용자 지정 정책](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>토큰 발급자 엔드포인트 가져오기

먼저 API에서 지원 하려는 각 발급자에 대해 토큰 발급자 엔드포인트 Uri를 가져와야 합니다. Azure AD B2C 테넌트에서 지 원하는 *b2clogin.com* 및 *login.microsoftonline.com* 엔드포인트를 가져오려면 Azure Portal에서 다음 절차를 사용 합니다.

기존 사용자 흐름 중 하나를 선택 하 여 시작 합니다.

1. [Azure Portal](https://portal.azure.com) 에서 Azure AD B2C 테넌트로 이동 합니다.
1. **정책**에서 **사용자 흐름 (정책)** 을 선택 합니다.
1. *B2C_1_signupsignin1*예를 들어 기존 정책을 선택한 다음 **사용자 흐름 실행** 을 선택 합니다.
1. 페이지 위쪽의 **사용자 흐름 실행** 제목 아래에서 하이퍼링크를 선택 하 여 해당 사용자 흐름에 대한 openid connect Connect 검색 엔드포인트로 이동 합니다.

    ![Azure Portal에서 지금 실행 페이지의 잘 알려진 URI 하이퍼링크](media/multi-token-endpoints/portal-01-policy-link.png)

1. 브라우저에서 열리는 페이지에서 `issuer` 값을 기록 합니다. 예를 들면 다음과 같습니다.

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. **도메인 선택** 드롭다운을 사용 하 여 다른 도메인을 선택 하 고 이전 두 단계를 다시 한 번 수행 하 여 `issuer` 값을 기록 합니다.

이제 다음과 같은 두 개의 Uri를 기록 해야 합니다.

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>사용자 지정 정책

사용자 흐름이 아닌 사용자 지정 정책을 사용 하는 경우 유사한 프로세스를 사용 하 여 발급자 Uri를 가져올 수 있습니다.

1. Azure AD B2C 테넌트로 이동 합니다.
1. **ID 경험 프레임워크**를 선택합니다.
1. 신뢰 당사자 정책 중 하나를 선택 합니다 (예: *B2C_1A_signup_signin*
1. **도메인 선택 드롭다운을** 사용 하 여 도메인 (예: *yourtenant.b2clogin.com* )을 선택 합니다.
1. **Openid connect Connect 검색 엔드포인트** 아래에 표시 되는 하이퍼링크를 선택 합니다.
1. `issuer` 값 기록
1. 다른 도메인에 대해 4-6 단계를 수행 합니다 (예: *login.microsoftonline.com* ).

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

이제 두 토큰 엔드포인트 Uri가 모두 있으므로 코드를 업데이트 하 여 두 엔드포인트이 모두 유효한 발급자 임을 지정 해야 합니다. 예제를 살펴보려면 샘플 응용 프로그램을 다운로드 하거나 복제 한 다음 두 엔드포인트을 모두 유효한 발급자로 지원 하도록 샘플을 업데이트 합니다.

보관 파일을 다운로드 합니다. [active-directory-b2c-dotnet-webapp-and-webapi-master.][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Web API에서 여러 발급자 사용

이 섹션에서는 두 토큰 발급자 엔드포인트이 유효함을 지정 하도록 코드를 업데이트 합니다.

1. Visual Studio에서 B2C-WebAPI-DotNet 솔루션을 엽니다 **.**
1. **Taskservice** 프로젝트에서 편집기의 *taskservice\\App_Start\\* * Startup.Auth.cs** * 파일을 엽니다.
1. 다음 `using` 지시어를 파일의 맨 위에 추가 합니다.

    `using System.Collections.Generic;`
1. [`ValidIssuers`][validissuers] 속성을 [`TokenValidationParameters`][tokenvalidationparameters] 정의에 추가 하 고 이전 섹션에서 기록한 두 uri를 모두 지정 합니다.

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`는 MSAL.NET에서 제공 되며 *Startup.Auth.cs*의 다음 코드 섹션에서 OWIN 미들웨어에 의해 사용 됩니다. 유효한 발급자를 여러 개 지정 하면 OWIN 응용 프로그램 파이프라인이 두 토큰 엔드포인트이 모두 유효한 발급자 임을 인식 합니다.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

앞서 언급 했 듯이, 다른 OWIN 라이브러리는 일반적으로 여러 발급자를 지 원하는 유사한 기능을 제공 합니다. 모든 라이브러리에 대한 예제를 제공 하는 것은이 문서의 범위를 벗어나므로 대부분의 라이브러리에 대해 유사한 기술을 사용할 수 있습니다.

## <a name="switch-endpoints-in-web-app"></a>웹 앱에서 엔드포인트 전환

이제 web API에서 두 Uri를 모두 지원 하므로 b2clogin.com 엔드포인트에서 토큰을 검색 하도록 웹 응용 프로그램을 업데이트 해야 합니다.

예를 들어 **TaskWebApp** 프로젝트의 *TaskWebApp\\* * web.config** * 파일에서 `ida:AadInstance` 값을 수정 하 여 새 엔드포인트을 사용 하도록 샘플 웹 응용 프로그램을 구성할 수 있습니다.

`login.microsoftonline.com`대신 `{your-b2c-tenant-name}.b2clogin.com`를 참조 하도록 *TaskWebApp의 web.config* 에서 `ida:AadInstance` 값을 변경 합니다.

이전:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

이후 (`{your-b2c-tenant}`을 B2C 테넌트의 이름으로 대체):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

웹 앱을 실행 하는 동안 엔드포인트 문자열이 생성 되 면 토큰을 요청할 때 b2clogin.com 기반 엔드포인트이 사용 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 발급자 엔드포인트의 토큰을 허용 하도록 Katana (Microsoft OWIN 미들웨어)를 구현 하는 web API를 구성 하는 방법을 제공 했습니다. 사용자 고유의 테넌트에 대해 이러한 프로젝트를 빌드하고 실행 하려는 경우 변경 해야 할 TaskService 및 TaskWebApp 프로젝트의 *web.config 파일에* 는 여러 가지 다른 문자열이 있습니다. 제대로 작동 하는지 확인 하려는 경우 프로젝트를 적절 하 게 수정 하는 것이 좋습니다. 그러나이 작업을 수행 하는 전체 연습은이 문서의 범위를 벗어납니다.

Azure AD B2C에서 내보내는 다양 한 형식의 보안 토큰에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요](tokens-overview.md)를 참조 하세요.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
