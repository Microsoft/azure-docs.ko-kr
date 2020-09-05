---
title: 셀프 서비스 등록 흐름에 API 커넥터 추가-Azure AD
description: 사용자 흐름에서 사용할 웹 API를 구성 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f241fd038d0d7309d8e1e5578dd77f950261b68
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165178"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>사용자 흐름에 API 커넥터 추가

[Api 커넥터](api-connectors-overview.md)를 사용 하려면 먼저 api 커넥터를 만든 다음 사용자 흐름에서 사용 하도록 설정 합니다.

## <a name="create-an-api-connector"></a>API 커넥터 만들기

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory**를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID**를 선택합니다.
4. **모든 api 커넥터 (미리 보기)** 를 선택한 다음 **새 api 커넥터**를 선택 합니다.

   ![새 API 커넥터 추가](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. 호출에 대 한 표시 이름을 제공 합니다. 예를 들어 **승인 상태를 확인**합니다.
6. API 호출에 대 한 **끝점 URL** 을 제공 합니다.
7. API에 대 한 인증 정보를 제공 합니다.

   - 현재 기본 인증만 지원 됩니다. 개발 목적으로 기본 인증 없이 API를 사용 하려는 경우에는 API에서 무시할 수 있는 더미 **사용자 이름** 및 **암호** 를 입력 하기만 하면 됩니다. API 키를 사용 하 여 Azure 함수와 함께 사용 하기 위해 코드를 **끝점 URL** 에 쿼리 매개 변수로 포함할 수 있습니다 (예: https []() ://contoso.azurewebsites.net/api/endpoint<b>? code = 0123456789</b>).

   ![새 API 커넥터 추가](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. **저장**을 선택합니다.

> [!IMPORTANT]
> 이전에는 API에 보낼 사용자 특성 (' 보낼 클레임 ') 및 API에서 수락할 사용자 특성 (' 수신 클레임 ')을 구성 해야 했습니다. 이제 값이 있는 경우 모든 사용자 특성은 기본적으로 전송 되 고, 모든 사용자 특성은 API에서 ' 연속 ' 응답으로 반환 될 수 있습니다.

## <a name="the-request-sent-to-your-api"></a>API로 전송 된 요청
API 커넥터는 **HTTP POST** 요청으로 구체화 되어 사용자 특성 (' 클레임 ')을 JSON 본문의 키-값 쌍으로 보냅니다. 특성은 [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties) 사용자 속성과 유사 하 게 직렬화 됩니다. 

**요청 예**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

**Azure Active Directory**  >  **외부 id**  >  **사용자 지정 사용자 특성** 환경에 나열 된 사용자 속성 및 사용자 지정 특성만 요청에서 보낼 수 있습니다.

사용자 지정 특성은 디렉터리의 **extension_ \<extensions-app-id> _AttributeName** 형식으로 존재 합니다. API는 동일한 직렬화 된 형식으로 클레임을 수신 해야 합니다. 사용자 지정 특성에 대 한 자세한 내용은 [셀프 서비스 등록 흐름에 대 한 사용자 지정 특성 정의](user-flow-add-custom-attributes.md)를 참조 하세요.

또한 **UI 로캘 (' ui_locales ')** 클레임은 모든 요청에 기본적으로 전송 됩니다. API에서 다국어 응답을 반환 하는 데 사용할 수 있는 사용자의 로캘을 장치에 구성 된 대로 제공 합니다.

> [!IMPORTANT]
> 보낼 클레임에 API 끝점이 호출 될 때 값이 없으면 클레임은 API로 보내지지 않습니다. API는 예상 되는 값을 명시적으로 확인 하도록 디자인 되어야 합니다.

> [!TIP] 
> [**id (' id ')**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) 및 **전자 메일 주소 (' 메일 ')** 클레임은 API에서 사용자가 테 넌 트에 계정을 포함 하기 전에 사용자를 식별 하는 데 사용할 수 있습니다. ' Id ' 클레임은 사용자가 Google 또는 Facebook과 같은 id 공급자를 사용 하 여 인증할 때 전송 됩니다. ' email '은 항상 전송 됩니다.

## <a name="enable-the-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용 하도록 설정

다음 단계를 수행 하 여 API 커넥터를 셀프 서비스 등록 사용자 흐름에 추가 합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory**를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID**를 선택합니다.
4. **사용자 흐름 (미리 보기)** 을 선택한 다음 API 커넥터를 추가 하려는 사용자 흐름을 선택 합니다.
5. **Api 커넥터**를 선택 하 고 사용자 흐름에서 다음 단계에 호출 하려는 api 끝점을 선택 합니다.

   - **Id 공급자를 사용 하 여 로그인 한 후**
   - **사용자를 만들기 전에**

   ![사용자 흐름에 Api 추가](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. **저장**을 선택합니다.

## <a name="after-signing-in-with-an-identity-provider"></a>Id 공급자를 사용 하 여 로그인 한 후

사용자가 id 공급자 (Google, Facebook, Azure AD)를 사용 하 여 인증 하는 즉시 등록 프로세스의이 단계에서 API 커넥터가 호출 됩니다. 이 단계는 사용자 특성을 수집 하기 위해 사용자에 게 표시 되는 형식인 ***특성 컬렉션 페이지***앞에 나옵니다. 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

API로 전송 되는 정확한 클레임은 id 공급자가 제공 하는 정보에 따라 달라 집니다. ' email '은 항상 전송 됩니다.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 web API의 예상 응답 형식

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 다음과 같은 응답을 반환할 수 있습니다.

- 연속 응답
- 차단 응답

#### <a name="continuation-response"></a>연속 응답

연속 응답은 사용자 흐름이 특성 컬렉션 페이지의 다음 단계로 계속 진행 되어야 함을 나타냅니다.

연속 응답에서 API는 클레임을 반환할 수 있습니다. API에서 클레임을 반환 하는 경우 클레임은 다음을 수행 합니다.

- 특성 컬렉션 페이지의 입력 필드를 미리 채웁니다.

[연속 응답](#example-of-a-continuation-response)의 예를 참조 하세요.

#### <a name="blocking-response"></a>차단 응답

차단 응답은 사용자 흐름을 종료 합니다. 사용자에 게 블록 페이지를 표시 하 여 사용자 흐름의 연속을 중지 하기 위해 API에서 의도적으로 실행할 수 있습니다. 블록 페이지에는 API에서 제공 하는가 표시 됩니다 `userMessage` .

[차단 응답](#example-of-a-blocking-response)의 예를 참조 하세요.

## <a name="before-creating-the-user"></a>사용자를 만들기 전에

등록 프로세스의이 단계에서 API 커넥터는 포함 된 경우 특성 컬렉션 페이지 다음에 호출 됩니다. 이 단계는 항상 Azure AD에서 사용자 계정을 만들기 전에 호출 됩니다. 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
API로 전송 되는 정확한 클레임은 사용자 로부터 수집 된 정보나 id 공급자가 제공 하는 정보에 따라 달라 집니다.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 web API의 예상 응답 형식

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 다음과 같은 응답을 반환할 수 있습니다.

- 연속 응답
- 차단 응답
- 유효성 검사 응답

#### <a name="continuation-response"></a>연속 응답

연속 응답은 사용자 흐름이 다음 단계로 계속 진행 되어야 함을 나타냅니다. 디렉터리에서 사용자를 만듭니다.

연속 응답에서 API는 클레임을 반환할 수 있습니다. API에서 클레임을 반환 하는 경우 클레임은 다음을 수행 합니다.

- 특성 컬렉션 페이지에서 클레임에 이미 할당 된 모든 값을 재정의 합니다.

[연속 응답](#example-of-a-continuation-response)의 예를 참조 하세요.

#### <a name="blocking-response"></a>차단 응답
차단 응답은 사용자 흐름을 종료 합니다. 사용자에 게 블록 페이지를 표시 하 여 사용자 흐름의 연속을 중지 하기 위해 API에서 의도적으로 실행할 수 있습니다. 블록 페이지에는 API에서 제공 하는가 표시 됩니다 `userMessage` .

[차단 응답](#example-of-a-blocking-response)의 예를 참조 하세요.

### <a name="validation-error-response"></a>유효성 검사-오류 응답
 API가 유효성 검사 오류 응답으로 응답 하는 경우 사용자 흐름은 특성 컬렉션 페이지에 유지 `userMessage` 되 고 사용자에 게 표시 됩니다. 그런 다음 사용자는 양식을 편집 하 고 다시 제출할 수 있습니다. 이 유형의 응답을 입력 유효성 검사에 사용할 수 있습니다.

[유효성 검사 오류 응답](#example-of-a-validation-error-response)의 예를 참조 하세요.

## <a name="example-responses"></a>예제 응답

### <a name="example-of-a-continuation-response"></a>연속 응답의 예

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| 매개 변수                                          | Type              | 필수 | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 버전                                            | String            | 예      | API 버전입니다.                                                                                                                                                                                                                                                                |
| action                                             | String            | 예      | 값은 `Continue`이어야 합니다.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 예       | 사용자 흐름에 대 한 API 커넥터 구성 및 **사용자 특성** 에서 **받을 클레임** 으로 선택한 경우에는 해당 값을 디렉터리에 저장할 수 있습니다. **응용 프로그램 클레임**으로 선택한 경우 토큰에서 값을 반환할 수 있습니다.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 예       | 반환 된 클레임에는를 포함할 필요가 없습니다 `_<extensions-app-id>_` . 사용자 흐름에 대 한 API 커넥터 구성 및 **사용자 특성** 에서 **받을 클레임** 으로 선택한 경우에는 값이 디렉터리에 저장 됩니다. 사용자 지정 특성은 토큰으로 다시 보낼 수 없습니다. |

### <a name="example-of-a-blocking-response"></a>차단 응답의 예

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| 매개 변수   | Type   | 필수 | Description                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| 버전     | String | 예      | API 버전입니다.                                                    |
| action      | String | 예      | 값은 이어야 합니다.`ShowBlockPage`                                              |
| userMessage | String | 예      | 사용자에게 표시할 메시지입니다.                                            |
| code        | String | 예       | 오류 코드 디버깅을 위해 사용할 수 있습니다. 사용자에 게 표시 되지 않습니다. |

**차단 응답이 있는 최종 사용자 환경**

![예제 블록 페이지](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>유효성 검사 오류 응답의 예

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| 매개 변수   | Type    | 필수 | Description                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| 버전     | String  | 예      | API 버전입니다.                                                    |
| action      | String  | 예      | 값은 `ValidationError`이어야 합니다.                                           |
| 상태      | 정수 | 예      | `400`ValidationError 응답의 값 이어야 합니다.                        |
| userMessage | String  | 예      | 사용자에게 표시할 메시지입니다.                                            |
| code        | String  | 예       | 오류 코드 디버깅을 위해 사용할 수 있습니다. 사용자에 게 표시 되지 않습니다. |

**유효성 검사 오류 응답과 함께 최종 사용자 환경**

![유효성 검사 페이지 예](./media/api-connectors-overview/validation-error-postal-code.png)

## <a name="using-azure-functions"></a>Azure Functions 사용
API 커넥터에서 사용할 API 끝점을 만드는 간단한 방법으로 Azure Functions에서 HTTP 트리거를 사용할 수 있습니다. [예](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)를 들어 Azure 함수를 사용 하 여 유효성 검사 논리를 수행 하 고 등록을 특정 도메인으로 제한할 수 있습니다. 또한 광범위 한 시나리오를 위해 Azure 함수에서 다른 웹 Api, 사용자 저장소 및 기타 클라우드 서비스를 호출 하 고 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- [셀프 서비스 등록에 사용자 지정 승인 워크플로를 추가](self-service-sign-up-add-approvals.md) 하는 방법을 알아봅니다.
- [Azure Function 빠른 시작 샘플](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)을 사용 하 여 시작 하세요.
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
