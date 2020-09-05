---
title: 셀프 서비스 등록 흐름에 사용자 지정 승인 추가-Azure AD
description: 외부 Id의 사용자 지정 승인 워크플로에 대 한 API 커넥터 추가 셀프 서비스 등록-Azure Active Directory (Azure AD)
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
ms.openlocfilehash: d664d7cd169593924917bb02a0220e4047eb0cdb
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165249"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>셀프 서비스 등록에 사용자 지정 승인 워크플로 추가

[API 커넥터](api-connectors-overview.md)를 사용 하 여 테 넌 트에 생성 된 게스트 사용자 계정을 관리할 수 있도록 셀프 서비스 등록을 사용 하 여 사용자 지정 승인 워크플로와 통합할 수 있습니다.

이 문서에서는 승인 시스템과 통합 하는 방법에 대 한 예를 제공 합니다. 이 예제에서는 셀프 서비스 등록 사용자 흐름이 등록 프로세스 중에 사용자 데이터를 수집 하 고 승인 시스템에 전달 합니다. 그러면 승인 시스템에서 다음을 수행할 수 있습니다.

- 사용자를 자동으로 승인 하 고 Azure AD에서 사용자 계정을 만들도록 허용 합니다.
- 수동 검토를 트리거합니다. 요청이 승인 되 면 승인 시스템은 Microsoft Graph를 사용 하 여 사용자 계정을 프로 비전 합니다. 승인 시스템은 사용자에 게 계정이 생성 되었음을 알릴 수도 있습니다.

## <a name="register-an-application-for-your-approval-system"></a>승인 시스템용 응용 프로그램 등록

Azure AD를 사용 하 여 인증 하 고 사용자를 만들 수 있는 권한이 있는 Azure AD 테 넌 트의 응용 프로그램으로 승인 시스템을 등록 해야 합니다. [Microsoft Graph에 대 한 인증 및 권한 부여 기본 사항](https://docs.microsoft.com/graph/auth/auth-concepts)에 대해 자세히 알아보세요.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory**를 선택합니다.
3. 왼쪽 메뉴에서 **앱 등록**를 선택 하 고 **새 등록**을 선택 합니다.
4. 응용 프로그램의 **이름** (예: _등록 승인)_ 을 입력 합니다.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. **등록**을 선택합니다. 다른 필드의 기본값을 그대로 둘 수 있습니다.

   ![애플리케이션 페이지 등록](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. 왼쪽 메뉴의 **관리** 에서 **API 권한**을 선택 하 고 **사용 권한 추가**를 선택 합니다.
7. **API 권한 요청** 페이지에서 **Microsoft Graph**를 선택 하 고 **응용 프로그램 사용 권한**을 선택 합니다.
8. **권한 선택**에서 **사용자**를 확장 한 다음, **사용자. ReadWrite** 확인란을 선택 합니다. 이 사용 권한을 통해 승인 시스템은 승인 될 때 사용자를 만들 수 있습니다. 그런 다음 **권한 추가**를 선택합니다.

   ![애플리케이션 페이지 등록](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. **API 사용 권한** 페이지에서 **(테 넌 트 이름)에 대해 관리자 동의 부여**를 선택 하 고 **예**를 선택 합니다.
10. 왼쪽 메뉴의 **관리** 에서 **인증서 & 암호**를 선택 하 고 **새 클라이언트 암호**를 선택 합니다.
11. 비밀에 대 한 **설명** (예: _승인 클라이언트 암호_)을 입력 하 고 클라이언트 암호가 **만료**되는 기간을 선택 합니다. 그런 다음, **추가**를 선택합니다.
12. 클라이언트 암호의 값을 복사 합니다.

    ![승인 시스템에서 사용할 클라이언트 암호를 복사 합니다.](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. **응용 프로그램 ID** 를 클라이언트 ID로 사용 하 고 Azure AD를 인증 하기 위해 생성 한 **클라이언트 암호** 를 사용 하도록 승인 시스템을 구성 합니다.

## <a name="create-the-api-connectors"></a>API 커넥터 만들기

다음으로 셀프 서비스 등록 사용자 흐름에 대 한 [API 커넥터를 만듭니다](self-service-sign-up-add-api-connector.md#create-an-api-connector) . 승인 시스템 API에는 아래에 표시 된 예제와 같이 두 개의 커넥터와 해당 끝점이 필요 합니다. 이러한 API 커넥터는 다음을 수행 합니다.

- **승인 상태를 확인**합니다. 사용자가 id 공급자를 사용 하 여 로그인 한 후 즉시 승인 시스템에 대 한 호출을 보내 사용자에 게 기존 승인 요청이 있는지 또는 이미 거부 되었는지 확인 합니다. 승인 시스템에서 자동 승인 결정만 수행 하는 경우에는이 API 커넥터가 필요 하지 않을 수 있습니다. "승인 상태 확인" API 커넥터의 예입니다.

  ![승인 상태 API 커넥터 구성 확인](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **승인 요청** -사용자가 특성 컬렉션 페이지를 완료 한 후, 사용자 계정을 만들기 전에 승인을 요청 하기 전에 승인 시스템에 대 한 호출을 보냅니다. 승인 요청을 자동으로 부여 하거나 수동으로 검토할 수 있습니다. "요청 승인" API 커넥터의 예입니다. 

  ![요청 승인 API 커넥터 구성](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

이러한 커넥터를 만들려면 [API 커넥터 만들기](self-service-sign-up-add-api-connector.md#create-an-api-connector)의 단계를 따르세요.

## <a name="enable-the-api-connectors-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용 하도록 설정

이제 다음 단계를 사용 하 여 셀프 서비스 등록 사용자 흐름에 API 커넥터를 추가 합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory**를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID**를 선택합니다.
4. **사용자 흐름 (미리 보기)** 을 선택한 다음 API 커넥터를 사용 하도록 설정 하려는 사용자 흐름을 선택 합니다.
5. **Api 커넥터**를 선택 하 고 사용자 흐름에서 다음 단계에 호출 하려는 api 끝점을 선택 합니다.

   - **Id 공급자를 사용 하 여 로그인 한 후**승인 상태 API 커넥터를 선택 합니다. 예를 들어 _승인 상태를 확인_합니다.
   - **사용자를 만들기 전에**: 승인 요청 API 커넥터 (예: _승인 요청_)를 선택 합니다.

   ![사용자 흐름에 Api 추가](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. **저장**을 선택합니다.

## <a name="control-the-sign-up-flow-with-api-responses"></a>API 응답을 사용 하 여 등록 흐름 제어

승인 시스템은 등록 흐름을 제어 하기 위해 호출 될 때 응답을 사용할 수 있습니다. 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>"승인 상태 확인" API 커넥터에 대 한 요청 및 응답

"승인 상태 확인" API 커넥터에서 API가 받은 요청의 예:

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

#### <a name="continuation-response-for-check-approval-status"></a>"승인 상태 확인"에 대 한 연속 응답

**승인 확인 상태** API 끝점은 다음과 같은 경우에 연속 응답을 반환 해야 합니다.

- 사용자가 이전에 승인을 요청 하지 않았습니다.

연속 응답의 예:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>"승인 상태 확인"에 대 한 차단 응답

**승인 확인 상태** API 끝점은 다음과 같은 경우 차단 응답을 반환 해야 합니다.

- 사용자 승인이 보류 중입니다.
- 사용자가 거부 되었으며 승인을 다시 요청할 수 없습니다.

차단 응답의 예는 다음과 같습니다.

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>"요청 승인" API 커넥터에 대 한 요청 및 응답

"요청 승인" API 커넥터에서 API가 받은 HTTP 요청의 예:

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

#### <a name="continuation-response-for-request-approval"></a>"요청 승인"에 대 한 연속 응답

**요청 승인** API 끝점은 다음과 같은 경우 연속 응답을 반환 해야 합니다.

- 사용자가 **_자동으로 승인_** 될 수 있습니다.

연속 응답의 예:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> 연속 응답이 수신 되 면 Azure AD는 사용자 계정을 만들고 사용자를 응용 프로그램으로 보냅니다.

#### <a name="blocking-response-for-request-approval"></a>"요청 승인"에 대 한 차단 응답

**요청 승인** API 끝점은 다음과 같은 경우 차단 응답을 반환 해야 합니다.

- 사용자 승인 요청이 만들어졌으며 현재 보류 중입니다.
- 사용자 승인 요청이 자동으로 거부 되었습니다.

차단 응답의 예는 다음과 같습니다.

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

`userMessage`응답의는 사용자에 게 표시 됩니다. 예를 들면 다음과 같습니다.

![승인 보류 중인 예 페이지](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>수동 승인 후 사용자 계정 만들기

수동 승인을 얻은 후 사용자 지정 승인 시스템은 [Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)를 사용 하 여 [사용자](https://docs.microsoft.com/graph/azuread-users-concept-overview) 계정을 만듭니다. 승인 시스템이 사용자 계정을 프로 비전 하는 방법은 사용자가 사용 하는 id 공급자에 따라 다릅니다.

### <a name="for-a-federated-google-or-facebook-user"></a>페더레이션된 Google 또는 Facebook 사용자의 경우

> [!IMPORTANT]
> 승인 시스템은이를 명시적으로 확인 하 고 `identities` `identities[0]` `identities[0].issuer` `identities[0].issuer` 이 메서드를 사용 하려면 ' facebook ' 또는 ' google '과 동일한 지 확인 해야 합니다.

사용자가 Google 또는 Facebook 계정으로 로그인 한 경우 [사용자 만들기 API](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0&tabs=http)를 사용할 수 있습니다.

1. 승인 시스템은를 사용 하 여 사용자 흐름에서 HTTP 요청을 받습니다.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 승인 시스템은 Microsoft Graph를 사용 하 여 사용자 계정을 만듭니다.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| 매개 변수                                           | 필수 | Description                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | 예      | `email`API에 전송 된 클레임을 사용 하 여를 생성 하 고, `@` 문자를로 바꾸고 `_` ,을로 미리 보류할 수 있습니다 `#EXT@<tenant-name>.onmicrosoft.com` . |
| accountEnabled                                      | 예      | `true`로 설정해야 합니다.                                                                                                                                                 |
| mail                                                | 예      | `email`API로 전송 된 클레임에 해당 합니다.                                                                                                               |
| userType                                            | 예      | `Guest`이어야 합니다. 이 사용자를 게스트 사용자로 지정 합니다.                                                                                                                 |
| ID                                          | 예      | 페더레이션된 id 정보입니다.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | 예       | `displayName`, 및 기타와 같은 기타 기본 제공 특성 `city` 매개 변수 이름은 API 커넥터에서 보낸 매개 변수와 같습니다.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | 예       | 사용자에 대 한 사용자 지정 특성입니다. 매개 변수 이름은 API 커넥터에서 보낸 매개 변수와 같습니다.                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>페더레이션된 Azure Active Directory 사용자의 경우

사용자가 페더레이션된 Azure Active Directory 계정으로 로그인 하는 경우 [초대 api](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0) 를 사용 하 여 사용자를 만든 다음 필요에 따라 사용자에 게 더 많은 특성을 할당 하는 [사용자 업데이트 api](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0) 를 사용 해야 합니다.

1. 승인 시스템은 사용자 흐름에서 HTTP 요청을 수신 합니다.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 승인 시스템은 `email` API 커넥터에서 제공 된를 사용 하 여 초대를 만듭니다.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

응답의 예는 다음과 같습니다.

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. 승인 시스템은 초대 된 사용자의 ID를 사용 하 여 사용자 특성을 수집 하 여 사용자의 계정을 업데이트 합니다 (선택 사항).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Function 빠른 시작 샘플](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)을 사용 하 여 시작 하세요.
- [수동 승인 예제를 사용 하 여 게스트 사용자에 대 한 셀프 서비스 등록](code-samples-self-service-sign-up.md#custom-approval-workflows)을 체크 아웃 합니다. 
