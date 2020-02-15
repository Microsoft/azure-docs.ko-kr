---
title: Azure Active Directory B2C에서 Graph API 사용
description: Azure AD Graph API를 호출 하 고 프로세스를 자동화 하는 응용 프로그램 id를 사용 하 여 Azure AD B2C 테 넌 트의 사용자를 관리 하는 방법입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 71b437f57f9d9e6e18af88d6413269cac6f66c47
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161667"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: Azure AD Graph API 사용

Azure Active Directory B2C (Azure AD B2C) 테 넌 트는 수천 또는 수백만 명의 사용자를 가질 수 있습니다. 즉, 많은 일반 테넌트 관리 작업을 프로그래밍 방식으로 수행해야 합니다. 주요 예제는 사용자 관리입니다.

B2C 테넌트에 기존 사용자 저장소를 마이그레이션해야 할 수 있습니다. 고유한 페이지에서 사용자 등록을 호스팅하고 백그라운드에서 Azure AD B2C 디렉터리에 사용자 계정을 만들려고 할 수 있습니다. 이러한 종류의 작업에는 사용자 계정을 만들고, 읽고, 업데이트 하 고, 삭제 하는 기능이 필요 합니다. Azure AD Graph API를 사용 하 여 이러한 작업을 수행할 수 있습니다.

B2C 테 넌 트의 경우 Graph API와 통신 하는 두 가지 기본 모드가 있습니다.

* **대화형**, 한 번 실행 작업의 경우 작업을 수행할 때 B2C 테 넌 트에서 관리자 계정으로 작업 해야 합니다. 이 모드에서는 관리자가 Graph API에 대한 호출을 수행할 수 있기 전에 해당 관리자가 자격 증명으로 로그인해야 합니다.
* **자동화**된 연속 작업의 경우 관리 작업을 수행 하는 데 필요한 권한으로 제공 하는 일종의 서비스 계정을 사용 해야 합니다. Azure AD에서 애플리케이션을 등록하고 Azure AD에 인증하여 이 작업을 수행할 수 있습니다. *OAuth 2.0 클라이언트 자격 증명 부여*를 사용하는 [애플리케이션 ID](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)를 사용하여 수행합니다. 이 경우에 애플리케이션은 사용자로서가 아닌 자체로서 Graph API를 호출합니다.

이 문서에서는 자동화 된 사용 사례를 수행 하는 방법에 대해 알아봅니다. 사용자 만들기, 읽기, 업데이트 및 삭제(CRUD) 작업을 수행하는 .NET 4.5 `B2CGraphClient`을 작성합니다. 클라이언트에는 다양한 메서드를 호출할 수 있도록 하는 Windows CLI(명령줄 인터페이스)가 있습니다. 그러나 코드는 대화형이 아닌 자동화 된 방식으로 동작 하도록 작성 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

애플리케이션 또는 사용자를 만들기 전에 Azure AD B2C 테넌트가 필요합니다. 아직 없는 경우 [Azure Active Directory B2C 테 넌 트를 만듭니다](tutorial-create-tenant.md).

## <a name="register-an-application"></a>애플리케이션 등록

Azure AD B2C 테 넌 트가 있으면 [Azure Portal](https://portal.azure.com)를 사용 하 여 관리 응용 프로그램을 등록 해야 합니다. 또한 자동화 된 스크립트나 관리 응용 프로그램을 대신 하 여 관리 작업을 수행 하는 데 필요한 권한도 부여 해야 합니다.

### <a name="register-application-in-azure-active-directory"></a>Azure Active Directory에 응용 프로그램 등록

B2C 테 넌 트와 함께 Azure AD Graph API을 사용 하려면 Azure Active Directory 응용 프로그램 등록 워크플로를 사용 하 여 응용 프로그램을 등록 해야 합니다.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>API 액세스 권한 할당

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>클라이언트 암호 만들기

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

이제 Azure AD B2C 테 넌 트에서 사용자를 *만들고*, *읽고*, *업데이트할* 수 있는 권한을 가진 응용 프로그램이 있습니다. 다음 섹션으로 이동 하 여 사용자 *삭제* 및 *암호 업데이트* 권한을 추가 합니다.

## <a name="add-user-delete-and-password-update-permissions"></a>사용자 삭제 및 암호 업데이트 권한 추가

이전에 부여한 *디렉터리 데이터 읽기 및 쓰기* 권한에는 사용자를 삭제 하거나 암호를 업데이트 하는 기능이 포함 되어 **있지 않습니다** .

응용 프로그램에 사용자를 삭제 하거나 암호를 업데이트 하는 기능을 제공 하려는 경우 해당 사용자에 게 *사용자 관리자* 역할을 부여 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. Azure Portal에서 **Azure AD B2C**를 검색 하 고 선택 합니다.
1. **관리**에서 **역할 및 관리자**를 선택 합니다.
1. **사용자 관리자** 역할을 선택 합니다.
1. **할당 추가**를 선택 합니다.
1. **선택** 텍스트 상자에 이전에 등록 한 응용 프로그램의 이름 (예: *managementapp1*)을 입력 합니다. 검색 결과에 표시 되는 응용 프로그램을 선택 합니다.
1. **추가**를 선택합니다. 권한이 완전히 전파 되는 데 몇 분 정도 걸릴 수 있습니다.

이제 Azure AD B2C 응용 프로그램에 사용자를 삭제 하거나 B2C 테 넌 트에서 암호를 업데이트 하는 데 필요한 추가 권한이 있습니다.

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

코드 샘플은 [ADAL (Active Directory 인증 라이브러리)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) 을 사용 하 여 Azure AD Graph API와 상호 작용 하는 .net 콘솔 응용 프로그램입니다. 이 코드에서는 API를 호출 하 여 Azure AD B2C 테 넌 트에서 사용자를 프로그래밍 방식으로 관리 하는 방법을 보여 줍니다.

샘플 아카이브 (\*.zip) [를 다운로드](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) 하거나 GitHub 리포지토리를 복제할 수 있습니다.

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

코드 샘플을 가져온 후 사용자 환경에 맞게 구성한 다음 프로젝트를 빌드합니다.

1. Visual Studio에서 `B2CGraphClient\B2CGraphClient.sln` 솔루션을 엽니다.
1. **B2CGraphClient** 프로젝트에서 *app.config* 파일을 엽니다.
1. `<appSettings>` 섹션을 다음 XML로 바꿉니다. 그런 다음 `{your-b2c-tenant}`을 테 넌 트의 이름으로 바꾸고 `{Application ID}` 및 `{Client secret}` 앞에서 기록한 값으로 바꿉니다.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. 솔루션을 빌드합니다. 솔루션 탐색기에서 **B2CGraphClient** 솔루션을 마우스 오른쪽 단추로 클릭 한 다음 **솔루션 다시 빌드**를 선택 합니다.

빌드에 성공 하면 `B2CGraphClient\bin\Debug`에서 `B2C.exe` 콘솔 응용 프로그램을 찾을 수 있습니다.

## <a name="review-the-sample-code"></a>샘플 코드 검토

B2CGraphClient를 사용 하려면 명령 프롬프트 (`cmd.exe`)를 열고 프로젝트의 `Debug` 디렉터리로 변경 합니다. 그런 다음 `B2C Help` 명령을 실행 합니다.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

`B2C Help` 명령은 사용 가능한 하위 명령에 대 한 간단한 설명을 표시 합니다. 해당 하위 명령 중 하나를 호출할 때마다 `B2CGraphClient` Azure AD Graph API에 요청을 보냅니다.

다음 섹션에서는 응용 프로그램 코드가 Azure AD Graph API를 호출 하는 방법을 설명 합니다.

### <a name="get-an-access-token"></a>액세스 토큰 가져오기

Azure AD Graph API에 대 한 모든 요청에는 인증을 위한 액세스 토큰이 필요 합니다. `B2CGraphClient`은 ADAL (오픈 소스 Active Directory 인증 라이브러리)을 사용 하 여 액세스 토큰을 가져오는 데 도움을 줍니다. ADAL을 사용 하면 도우미 API를 제공 하 고 액세스 토큰 캐싱과 같은 몇 가지 중요 한 세부 정보를 처리 하 여 토큰을 보다 쉽게 가져올 수 있습니다. 그러나 ADAL을 사용 하 여 토큰을 가져올 필요는 없습니다. 대신 HTTP 요청을 수동으로 작성 하 여 토큰을 가져올 수 있습니다.

> [!NOTE]
> Azure AD Graph API와 함께 사용할 수 있는 액세스 토큰을 가져오려면 ADAL v2 이상을 사용 해야 합니다. ADAL v1은 사용할 수 없습니다.

`B2CGraphClient`를 실행 하면 `B2CGraphClient` 클래스의 인스턴스를 만듭니다. 이 클래스의 생성자는 ADAL 인증 스 캐 폴딩을 설정 합니다.

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

`B2C Get-User` 명령을 사용 하 여 예로 살펴보겠습니다.

추가 인수 없이 `B2C Get-User`를 호출 하면 응용 프로그램은 `B2CGraphClient.GetAllUsers()` 메서드를 호출 합니다. 그런 다음 `GetAllUsers()` `B2CGraphClient.SendGraphGetRequest()`를 호출 하 여 HTTP GET 요청을 Azure AD Graph API 전송 합니다. `B2CGraphClient.SendGraphGetRequest()` GET 요청을 보내기 전에 먼저 ADAL을 사용 하 여 액세스 토큰을 가져옵니다.

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

ADAL `AuthenticationContext.AcquireToken()` 메서드를 호출하여 Graph API에 대한 액세스 토큰을 가져올 수 있습니다. 그러면 ADAL은 애플리케이션의 ID를 나타내는 `access_token`를 반환합니다.

### <a name="read-users"></a>사용자 읽기

사용자 목록을 가져오거나 Azure AD Graph API에서 특정 사용자를 가져오는 경우 HTTP `GET` 요청을 `/users` 끝점으로 보낼 수 있습니다. 테넌트의 모든 사용자에 대한 요청은 다음과 같습니다.

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

이 요청을 보려면 다음을 실행합니다.

 ```cmd
 B2C Get-User
 ```

유의해야 할 두 가지 중요한 사항은 다음과 같습니다.

* ADAL을 사용 하 여 가져온 액세스 토큰은 `Bearer` 체계를 사용 하 여 `Authorization` 헤더에 추가 됩니다.
* B2C 테넌트의 경우 쿼리 매개 변수 `api-version=1.6`를 사용해야 합니다.

이러한 세부 사항은 모두 `B2CGraphClient.SendGraphGetRequest()` 메서드에서 처리됩니다.

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>소비자 사용자 계정 만들기

B2C 테 넌 트에서 사용자 계정을 만들 때 HTTP `POST` 요청을 `/users` 끝점으로 보낼 수 있습니다. 다음 HTTP `POST` 요청은 테 넌 트에서 만들 예제 사용자를 표시 합니다.

소비자 사용자를 만들려면 다음 요청의 대부분 속성이 필요 합니다. 설명에 대 한 `//` 주석은 실제 요청에 포함 되지 않습니다.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

요청을 확인하려면 다음 명령 중 하나를 실행합니다.

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 명령은 사용자 개체의 JSON 표현을 포함 하는 JSON 파일을 입력 매개 변수로 사용 합니다. 코드 샘플에는 `usertemplate-email.json` 및 `usertemplate-username.json`의 두 가지 샘플 JSON 파일이 있습니다. 필요에 따라 이러한 파일을 수정할 수 있습니다. 위의 필수 필드 외에도 파일에 몇 가지 선택적 필드가 포함 됩니다.

필수 및 선택적 필드에 대 한 자세한 내용은 [엔터티 및 복합 형식 참조를 참조 하세요. 참조를 Graph API](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference)합니다.

`B2CGraphClient.SendGraphPostRequest()`에서 POST 요청을 생성 하는 방법을 확인할 수 있습니다.

* 액세스 토큰을 요청의 `Authorization` 헤더에 연결합니다.
* `api-version=1.6`을 설정합니다.
* 요청 본문에 JSON 사용자 개체를 포함합니다.

> [!NOTE]
> 기존 사용자 저장소에서 마이그레이션하려는 계정이 [Azure AD B2C에 의해 적용 되는 강력한 암호](user-flow-password-complexity.md)강도 보다 낮은 암호를 사용 하는 경우 `passwordPolicies` 속성에 `DisableStrongPassword` 값을 사용 하 여 강력한 암호 요구 사항을 사용 하지 않도록 설정할 수 있습니다. 예를 들어 이전 사용자 만들기 요청을 다음과 같이 수정할 수 있습니다. `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>소비자 사용자 계정 업데이트

사용자 개체를 업데이트 하는 경우 프로세스는 사용자 개체를 만드는 데 사용 하는 프로세스와 유사 하지만 HTTP `PATCH` 메서드를 사용 합니다.

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

JSON 파일의 일부 값을 수정 하 여 사용자를 업데이트 한 후 `B2CGraphClient`를 사용 하 여 다음 명령 중 하나를 실행 합니다.

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

이 요청을 보내는 방법에 대한 세부 정보는 `B2CGraphClient.SendGraphPatchRequest()` 메서드를 검사합니다.

### <a name="search-users"></a>사용자 검색

B2C 테 넌 트에서 사용자를 검색 하려면 다음과 같은 방법을 사용할 수 있습니다.

* 사용자의 **개체 ID**를 참조 합니다.
* `signInNames` 속성의 로그인 식별자를 참조 합니다.
* 유효한 OData 매개 변수를 참조 합니다. 예: ' givenName ', ' 성 ', ' displayName ' 등

다음 명령 중 하나를 실행 하 여 사용자를 검색 합니다.

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

다음은 그 예입니다.

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>사용자 삭제

사용자를 삭제 하려면 HTTP `DELETE` 메서드를 사용 하 고 사용자의 개체 ID를 사용 하 여 URL을 생성 합니다.

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

예제를 보려면 이 명령을 입력하고 콘솔에 출력된 삭제 요청을 봅니다.

```cmd
B2C Delete-User <object-id-of-user>
```

이 요청을 보내는 방법에 대한 세부 정보는 `B2CGraphClient.SendGraphDeleteRequest()` 메서드를 검사합니다.

사용자 관리 외에도 Azure AD Graph API를 사용하여 다른 많은 작업을 수행할 수 있습니다. [Azure AD Graph API 참조](/previous-versions/azure/ad/graph/api/api-catalog) 는 샘플 요청과 함께 각 작업의 세부 정보를 제공합니다.

## <a name="use-custom-attributes"></a>사용자 지정 특성 사용

많은 소비자 애플리케이션은 특정 유형의 사용자 지정 사용자 프로필 정보를 저장해야 합니다. B2C 테 넌 트에 사용자 지정 특성을 정의 하는 방법 중 하나를 사용할 수 있습니다. 그런 다음 사용자 개체의 다른 속성을 처리 하는 것과 같은 방법으로 해당 특성을 취급할 수 있습니다. 특성을 업데이트 및 삭제하고 특성으로 쿼리하며 특성을 로그인 토큰에서 클레임으로 보낼 수 있습니다.

B2C 테넌트에서 사용자 지정 특성을 정의하려면 [B2C 사용자 지정 특성 참조](user-flow-custom-attributes.md)를 참조하세요.

다음 `B2CGraphClient` 명령을 사용 하 여 B2C 테 넌 트에 정의 된 사용자 지정 특성을 볼 수 있습니다.

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

출력은 각 사용자 지정 특성의 세부 정보를 표시 합니다. 다음은 그 예입니다.

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

`extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`와 같은 전체 이름을 사용자 개체의 속성으로 사용할 수 있습니다. 새 속성 및 속성에 대 한 값을 사용 하 여 JSON 파일을 업데이트 한 후 다음을 실행 합니다.

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>다음 단계

`B2CGraphClient`를 사용하여 B2C 테넌트 사용자를 프로그래밍 방식으로 관리할 수 있는 서비스 애플리케이션이 있습니다. `B2CGraphClient`는 고유한 애플리케이션 ID를 사용하여 Azure AD Graph API에 인증합니다. 또한 클라이언트 암호를 사용하여 토큰을 획득합니다.

사용자 고유의 응용 프로그램에이 기능을 통합 하는 경우 B2C 응용 프로그램에 대 한 몇 가지 주요 사항을 명심 해야 합니다.

* 테 넌 트에서 필요한 권한을 응용 프로그램에 부여 합니다.
* Graph API를 호출할 때 `api-version=1.6`을 사용합니다.
* 소비자 사용자를 만들고 업데이트하는 경우 위에서 설명한 대로 필수적인 몇 가지 속성이 있습니다.
