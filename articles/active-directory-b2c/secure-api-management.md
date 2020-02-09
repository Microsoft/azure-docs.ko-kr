---
title: Azure Active Directory B2C를 사용 하 여 Azure API Management API 보안 유지
description: Azure Active Directory B2C에서 발급 한 액세스 토큰을 사용 하 여 Azure API Management API 엔드포인트의 보안을 유지 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 942c565c885d59a14d64e7ec06beee0354e7c4ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641631"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Azure AD B2C를 사용 하 여 Azure API Management API 보호

Azure Active Directory B2C (Azure AD B2C)를 사용 하 여 인증 된 클라이언트에 대한 APIM (Azure API Management) API에 대한 액세스를 제한 하는 방법을 알아봅니다. 이 문서의 단계를 수행 하 여 유효한 Azure AD B2C 발급 된 액세스 토큰을 포함 하는 요청에만 액세스를 제한 하는 APIM에서 인바운드 정책을 만들고 테스트 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 계속 하기 전에 다음 리소스를 준비 해야 합니다.

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* 테넌트에서 [등록된 애플리케이션](tutorial-register-applications.md)
* 테넌트에서 [만든 사용자 흐름](tutorial-create-user-flows.md)
* Azure API Management의 [게시 된 API](../api-management/import-and-publish.md)
* 보안 액세스를 테스트 하는 [Postman](https://www.getpostman.com/) (선택 사항)

## <a name="get-azure-ad-b2c-application-id"></a>응용 프로그램 ID Azure AD B2C 가져오기

Azure API Management에서 Azure AD B2C를 사용 하 여 API를 보호 하는 경우 APIM에서 만드는 [인바운드 정책](../api-management/api-management-howto-policies.md) 에 대한 여러 값이 필요 합니다. 먼저 Azure AD B2C 테넌트에서 이전에 만든 응용 프로그램의 응용 프로그램 ID를 기록 합니다. 필수 구성 요소에서 만든 응용 프로그램을 사용 하는 경우 *webbapp1*의 응용 프로그램 ID를 사용 합니다.

현재 **응용 프로그램** 환경 또는 새로운 통합 **앱 등록 (미리 보기)** 환경을 사용 하 여 응용 프로그램 ID를 가져올 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하 고 **Azure AD B2C**을 검색 하 고 선택 합니다.
1. **관리**에서 **응용 프로그램**을 선택 합니다.
1. *Webapp1* 또는 이전에 만든 다른 응용 프로그램의 **응용 프로그램 ID** 열에 값을 기록 합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록 (미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하 고 **Azure AD B2C**을 검색 하 고 선택 합니다.
1. **앱 등록 (미리 보기)** 를 선택 하 고 **소유 하는 응용 프로그램** 탭을 선택 합니다.
1. *Webapp1* 또는 이전에 만든 다른 응용 프로그램의 **응용 프로그램 (클라이언트) ID** 열에 값을 기록 합니다.

* * *

## <a name="get-token-issuer-endpoint"></a>토큰 발급자 엔드포인트 가져오기

다음으로 Azure AD B2C 사용자 흐름 중 하나에 대한 잘 알려진 구성 URL을 가져옵니다. 또한 Azure API Management에서 지원 하려는 토큰 발급자 엔드포인트 URI가 필요 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure AD B2C 테넌트로 이동 합니다.
1. **정책**에서 **사용자 흐름 (정책)** 을 선택 합니다.
1. 기존 정책 (예: *B2C_1_signupsignin1*)을 선택 하 고 **사용자 흐름 실행**을 선택 합니다.
1. 페이지 맨 위 근처의 **사용자 흐름 실행** 제목 아래에 표시 되는 하이퍼링크에 URL을 기록 합니다. 이 URL은 사용자 흐름에 대한 Openid connect 연결의 잘 알려진 검색 엔드포인트 이며, Azure API Management에서 인바운드 정책을 구성할 때 다음 섹션에서 사용 합니다.

    ![Azure Portal에서 지금 실행 페이지의 잘 알려진 URI 하이퍼링크](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 하이퍼링크를 선택 하 여 Openid connect Connect의 잘 알려진 구성 페이지로 이동 합니다.
1. 브라우저에서 열리는 페이지에서 `issuer` 값을 기록 합니다. 예를 들면 다음과 같습니다.

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Azure API Management에서 API를 구성 하는 경우 다음 섹션에서이 값을 사용 합니다.

이제 다음 섹션인 Openid connect 연결의 잘 알려진 구성 엔드포인트 URL 및 발급자 URI에 사용 하기 위해 기록 된 두 개의 Url이 있어야 합니다. 예:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Azure API Management에서 인바운드 정책 구성

이제 API 호출의 유효성을 검사 하는 Azure API Management 인바운드 정책을 추가할 준비가 되었습니다. 액세스 토큰의 대상 및 발급자를 확인 하는 [JWT 유효성 검사](../api-management/api-management-access-restriction-policies.md#ValidateJWT) 정책을 추가 하 여 유효한 토큰이 있는 API 호출만 허용 되도록 할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure API Management 인스턴스를 찾습니다.
1. **API**를 선택합니다.
1. Azure AD B2C를 사용 하 여 보안을 유지할 API를 선택 합니다.
1. **디자인** 탭을 선택합니다.
1. **인바운드 처리**에서 **\</\>** 를 선택 하 여 정책 코드 편집기를 엽니다.
1. `<inbound>` 정책 안에 다음 `<validate-jwt>` 태그를 추가 합니다.

    1. `<openid-config>` 요소의 `url` 값을 정책의 잘 알려진 구성 URL로 업데이트 합니다.
    1. B2C 테넌트에서 이전에 만든 응용 프로그램의 응용 프로그램 ID로 `<audience>` 요소를 업데이트 합니다 (예: *webapp1*).
    1. `<issuer>` 요소를 앞에서 기록한 토큰 발급자 엔드포인트으로 업데이트 합니다.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>보안 API 액세스의 유효성 검사

인증 된 호출자만 API에 액세스할 수 있도록 하기 위해 [Postman](https://www.getpostman.com/)을 사용 하 여 api를 호출 하 여 Azure API Management 구성의 유효성을 검사할 수 있습니다.

API를 호출 하려면 Azure AD B2C에서 발급 한 액세스 토큰과 APIM 구독 키가 모두 필요 합니다.

### <a name="get-an-access-token"></a>액세스 토큰 가져오기

먼저 Postman의 `Authorization` 헤더에서 사용 하기 위해 Azure AD B2C에서 발급 한 토큰이 필요 합니다. 필수 구성 요소 중 하나로 만들어야 하는 등록/로그인 사용자 흐름의 **지금 실행** 기능을 사용 하 여 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure AD B2C 테넌트로 이동 합니다.
1. **정책**에서 **사용자 흐름 (정책)** 을 선택 합니다.
1. 기존 등록/로그인 사용자 흐름 (예: *B2C_1_signupsignin1*)을 선택 합니다.
1. **응용 프로그램**에 대해 *webapp1*를 선택 합니다.
1. **회신 URL**에 대해 `https://jwt.ms`를 선택 합니다.
1. **사용자 흐름 실행**을 선택합니다.

    ![Azure Portal에서 등록 로그인 사용자 흐름에 대한 사용자 흐름 페이지를 실행 합니다.](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 로그인 프로세스를 완료합니다. `https://jwt.ms`으로 리디렉션해야 합니다.
1. 브라우저에 표시 되는 인코딩된 토큰 값을 기록 합니다. Postman의 Authorization 헤더에이 토큰 값을 사용 합니다.

    ![Jwt.ms에 표시 되는 인코딩된 토큰 값](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>API 구독 키 가져오기

게시 된 API를 호출 하는 클라이언트 응용 프로그램 (이 경우 Postman)에는 API에 대한 HTTP 요청에 유효한 API Management 구독 키가 포함 되어야 합니다. Postman HTTP 요청에 포함할 구독 키를 가져오려면:

1. [Azure Portal](https://portal.azure.com)에서 Azure API Management 서비스 인스턴스로 이동 합니다.
1. **구독**을 선택합니다.
1. Product에 대한 줄임표 **: 제한 없음**을 선택 하 고 **키 표시/숨기기**를 선택 합니다.
1. 제품에 대한 **기본 키** 를 기록 합니다. Postman의 HTTP 요청에서 `Ocp-Apim-Subscription-Key` 헤더에이 키를 사용 합니다.

![Azure Portal에서 선택 된 키 표시/숨기기가 있는 구독 키 페이지](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>보안 API 호출 테스트

액세스 토큰 및 APIM 구독 키가 기록 되 면 API에 대한 보안 액세스를 올바르게 구성 했는지 테스트할 준비가 되었습니다.

1. [Postman](https://www.getpostman.com/)에서 새 `GET` 요청을 만듭니다. 요청 URL에 대한 필수 구성 요소 중 하나로 게시 한 API의 스피커 목록 엔드포인트을 지정 합니다. 예:

    `https://contosoapim.azure-api.net/conference/speakers`

1. 다음 헤더를 추가 합니다.

    | 키 | 값 |
    | --- | ----- |
    | `Authorization` | 앞에서 기록한 토큰 값으로, `Bearer ` 접두사가 붙습니다 ("전달자" 뒤에 공백 포함). |
    | `Ocp-Apim-Subscription-Key` | 이전에 기록한 APIM 구독 키 |

    **GET** 요청 URL과 **헤더** 는 다음과 유사 하 게 표시 됩니다.

    ![GET 요청 URL 및 헤더를 표시 하는 postman UI](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Postman에서 **보내기** 단추를 선택 하 여 요청을 실행 합니다. 모든 항목을 올바르게 구성한 경우에는 회의 발표자 컬렉션과 함께 JSON 응답을 제공 해야 합니다 (여기에 잘린 것으로 표시 됨).

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>안전 하지 않은 API 호출 테스트

이제 요청이 성공적으로 완료 되 면 실패 사례를 테스트 하 여 *잘못* 된 토큰이 포함 된 API에 대한 호출이 예상 대로 거부 되는지 확인 합니다. 테스트를 수행 하는 한 가지 방법은 토큰 값에 몇 개의 문자를 추가 하거나 변경 하 고 이전과 동일한 `GET` 요청을 실행 하는 것입니다.

1. 토큰 값에 여러 문자를 추가 하 여 잘못 된 토큰을 시뮬레이트합니다. 예를 들어 토큰 값에 "INVALID"를 추가 합니다.

    ![토큰에 잘못 된 추가를 표시 하는 Postman UI의 헤더 섹션](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. **보내기** 단추를 선택 하 여 요청을 실행 합니다. 잘못 된 토큰을 사용 하는 경우 예상 결과는 `401` 권한이 없는 상태 코드입니다.

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

`401` 상태 코드가 표시 되 면 Azure AD B2C에서 발급 한 유효한 액세스 토큰이 있는 호출자만 Azure API Management API에 대한 성공적인 요청을 수행할 수 있음을 확인 했습니다.

## <a name="support-multiple-applications-and-issuers"></a>여러 응용 프로그램 및 발급자 지원

여러 응용 프로그램은 일반적으로 단일 REST API와 상호 작용 합니다. API를 사용 하 여 여러 응용 프로그램에 적합 한 토큰을 허용 하려면 APIM 인바운드 정책의 `<audiences>` 요소에 해당 응용 프로그램 Id를 추가 합니다.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

마찬가지로, 여러 토큰 발급자를 지원 하려면 해당 엔드포인트 Uri를 APIM 인바운드 정책의 `<issuers>` 요소에 추가 합니다.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>B2clogin.com로 마이그레이션

레거시 `login.microsoftonline.com` 엔드포인트에서 발급 한 토큰의 유효성을 검사 하는 APIM API가 있는 경우 API와 API를 호출 하는 응용 프로그램을 [b2clogin.com](b2clogin.md)에서 발급 한 토큰을 사용 하도록 마이그레이션해야 합니다.

이 일반적인 프로세스를 따라 단계적 마이그레이션을 수행할 수 있습니다.

1. B2clogin.com 및 login.microsoftonline.com 둘 다에서 발급 된 토큰에 대한 APIM 인바운드 정책에 대한 지원을 추가 합니다.
1. 응용 프로그램을 한 번에 하나씩 업데이트 하 여 b2clogin.com 엔드포인트에서 토큰을 가져옵니다.
1. 모든 응용 프로그램이 b2clogin.com에서 토큰을 제대로 가져오면 API에서 login.microsoftonline.com 발급 된 토큰에 대한 지원을 제거 합니다.

다음 예제 APIM 인바운드 정책은 b2clogin.com 및 login.microsoftonline.com 둘 다에서 발급 된 토큰을 허용 하는 방법을 보여 줍니다. 또한 두 응용 프로그램의 API 요청을 지원 합니다.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>다음 단계

Azure API Management 정책에 대한 자세한 내용은 [Apim 정책 참조 인덱스](../api-management/api-management-policies.md)를 참조 하세요.

OWIN 기반 웹 Api 및 해당 응용 프로그램을 b2clogin.com로 마이그레이션하는 방법에 대한 정보는 [OWIN 기반 웹 api를 b2clogin.com로 마이그레이션](multiple-token-endpoints.md)에 대한 정보를 찾을 수 있습니다.
