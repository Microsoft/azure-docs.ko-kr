---
title: 소셜 ID가 있는 사용자 마이그레이션
titleSuffix: Azure AD B2C
description: Graph API를 사용 하 여 소셜 id가 있는 사용자를 Azure AD B2C으로 마이그레이션하는 것과 관련 된 핵심 개념을 검토 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 96bb4ef11b960a517054009f14f18b7af23fac14
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848838"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C: 소셜 ID가 있는 사용자 마이그레이션
ID 공급자를 Azure AD B2C로 마이그레이션하려는 경우 소셜 ID가 있는 사용자 계정도 마이그레이션해야 합니다. 이 문서에서는 Facebook, LinkedIn, Microsoft 및 Google 계정 등의 기존 소셜 ID 계정을 Azure AD B2C로 마이그레이션하는 방법을 설명합니다. 이 문서는 페더레이션 ID에도 해당되지만 이러한 마이그레이션은 일반성이 떨어집니다. 이 문서의 나머지 부분에서는 다른 유형의 페더레이션된 계정에도 소셜 계정에 적용 되는 모든 사항을 고려 합니다.

## <a name="prerequisites"></a>필수 조건
이 문서는 사용자 마이그레이션 문서의 연속물이며 소셜 ID 마이그레이션에 중점을 두고 있습니다. 시작에 앞서 [사용자 마이그레이션](user-migration.md)을 참조하세요.

## <a name="social-identities-migration-introduction"></a>소셜 ID 마이그레이션 소개

* Azure AD B2C에서 **로컬 계정** 로그인 이름(사용자 이름 또는 이메일 주소)은 사용자 레코드의 `signInNames` 컬렉션에 저장됩니다. `signInNames`에는 사용자의 로그인 이름을 지정하는 하나 이상의 `signInName` 레코드가 포함됩니다. 각 로그인 이름은 테넌트에서 고유해야 합니다.

* **소셜 계정의** ID는 `userIdentities` 컬렉션에 저장됩니다. 이 항목은 facebook.com과 같은 `issuer`(ID 공급자 이름)와 발급자의 고유 사용자 ID인 `issuerUserId`를 지정합니다. `userIdentities` 특성에는 소셜 ID 공급자의 고유 사용자 ID와 소셜 계정 유형을 지정하는 하나 이상의 UserIdentity 레코드가 포함됩니다.

* **소셜 ID와 로컬 계정 결합**. 언급했듯이 로컬 계정 로그인 이름과 소셜 계정 ID는 다른 특성에 저장됩니다. `signInNames`은 로컬 계정에 사용 되 고 `userIdentities`는 소셜 계정에 사용 됩니다. 단일 Azure AD B2C 계정은 로컬 계정 이거나 소셜 계정만 가능 하거나 한 사용자 레코드에서 하나 이상의 소셜 id와 로컬 계정을 결합할 수 있습니다. 이러한 동작을 통해 단일 계정을 관리할 수 있고 사용자는 로컬 계정 자격 증명이나 소셜 ID로 로그인이 가능합니다.

* `UserIdentity` 형식 - Azure AD B2C 테넌트의 소셜 계정 사용자 ID에 대한 정보가 포함됩니다.
  * `issuer` 사용자 ID를 발급한 ID 공급자의 문자열 표현(예: facebook.com)입니다.
  * 소셜 id 공급자가 사용 하는 고유 사용자 id를 Base64 인코딩 형식으로 `issuerUserId` 합니다.

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* Id 공급자에 따라 **발급자 사용자 ID** 는 응용 프로그램 또는 개발 계정 별로 지정 된 사용자에 대한 고유한 값입니다. 이전에 소셜 공급자나 동일한 개발 계정 내의 다른 응용 프로그램에서 할당 한 것과 동일한 응용 프로그램 ID를 사용 하 여 Azure AD B2C 정책을 구성 합니다.

## <a name="use-graph-api-to-migrate-users"></a>Graph API를 사용하여 사용자 마이그레이션
[Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet)를 통해 Azure AD B2C 사용자 계정을 만듭니다.
Graph API와 통신하려면 먼저 관리자 권한이 있는 서비스 계정이 있어야 합니다. Azure AD에서 Azure AD에 애플리케이션 및 인증을 등록합니다. 애플리케이션 자격 증명은 애플리케이션 ID 및 애플리케이션 비밀입니다. 애플리케이션은 사용자로서가 아닌 자체로서 Graph API를 호출합니다. [사용자 마이그레이션](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration) 문서에서 1 단계의 지침을 따릅니다.

## <a name="required-properties"></a>필수 속성
다음 목록은 사용자를 만들 때 필요한 속성을 보여줍니다.
* **accountEnabled** - true
* **displayName** - 사용자의 주소록에 표시할 이름입니다.
* **passwordProfile** - 사용자의 암호 프로필입니다.

> [!NOTE]
> 로컬 계정 자격 증명이 없는 소셜 계정에 대해서는 여전히 암호를 지정 해야 합니다. Azure AD B2C에서는 소셜 계정에 대해 지정한 암호가 무시됩니다.

* **userPrincipalName** - 사용자 계정 이름(someuser@contoso.com)입니다. 사용자 계정 이름에는 테넌트의 확인된 도메인 중 하나가 반드시 포함되어야 합니다. UPN을 지정하려면 새 GUID 값을 생성하고 `@` 및 테넌트 이름으로 연결합니다.
* **mailNickname** - 사용자의 메일 별칭입니다. 이 값은 userPrincipalName에 사용하는 ID와 동일할 수 있습니다.
* **signInNames** - 사용자의 로그인 이름을 지정하는 하나 이상의 SignInName 레코드입니다. 각 로그인 이름은 회사/테넌트에서 고유해야 합니다. 소셜 계정에 한하여 이 속성을 비워 둘 수 있습니다.
* **userIdentities** - 소셜 ID 공급자의 고유 사용자 ID와 소셜 계정 유형을 지정하는 하나 이상의 UserIdentity 레코드입니다.
* [선택 사항] **otherMails** – 소셜 계정 전용 사용자의 이메일 주소입니다.

자세한 내용은 [Graph API 참조](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)를 참조하세요.

## <a name="migrate-social-account-only"></a>소셜 계정(전용) 마이그레이션
로컬 계정 자격 증명이 없는 소셜 계정만 만들려면 Graph API에 HTTPS POST 요청을 보냅니다. 요청 본문에는 만들려는 소셜 계정 사용자의 속성이 포함됩니다. 최소한 필수 속성을 지정해야 합니다.


https://graph.windows.net/tenant-name.onmicrosoft.com/users **게시**

다음과 같은 양식 데이터를 제출합니다.

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>로컬 계정과 소셜 계정 마이그레이션
소셜 id를 사용 하 여 결합 된 로컬 계정을 만들려면 HTTPS POST 요청을 Graph API 보냅니다. 요청 본문에는 로컬 계정의 로그인 이름을 포함 하 여 만들 소셜 계정 사용자의 속성이 포함 됩니다. 최소한 필수 속성을 지정해야 합니다.

https://graph.windows.net/tenant-name.onmicrosoft.com/users **게시**

다음과 같은 양식 데이터를 제출합니다.

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)
### <a name="how-can-i-know-the-issuer-name"></a>발급자 이름은 어떻게 알 수 있나요?
발급자 이름 또는 ID 공급자 이름은 정책에 구성됩니다. `issuer`에 지정할 값을 알지 못하는 경우 다음 절차를 수행합니다.
1. 소셜 계정 중 하나로 로그인합니다.
2. JWT 토큰에서 `sub` 값을 복사합니다. `sub`에는 일반적으로 Azure AD B2C 사용자의 개체 ID가 포함됩니다. 또는 Azure Portal에서 사용자의 속성을 열고 개체 ID를 복사합니다.
3. [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net)를 엽니다.
4. 관리자로 로그인합니다.
5. 다음 GET 요청을 실행합니다. userObjectId를 복사한 사용자 ID로 바꿉니다. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Azure AD B2C에서 반환된 JSON 내에서 `userIdentities` 요소를 찾습니다.
7. [선택 사항] `issuerUserId` 값을 디코드할 수도 있습니다.

> [!NOTE]
> B2C 테넌트에 대해 로컬인 B2C 테넌트 관리자 계정을 사용합니다. 계정 이름 구문은 admin@tenant-name.onmicrosoft.com입니다.

### <a name="is-it-possible-to-add-a-social-identity-to-an-existing-user"></a>기존 사용자에 게 소셜 id를 추가할 수 있나요?
예. Azure AD B2C 계정이 만들어진 후 소셜 id를 추가할 수 있습니다 (로컬 계정이 나 소셜 계정 인지 또는 이들의 조합). HTTPS 패치 요청을 실행 합니다. userObjectId를 업데이트할 사용자 ID로 바꿉니다.

**패치** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

다음과 같은 양식 데이터를 제출합니다.

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>여러 소셜 ID를 추가할 수 있나요?
예. 단일 Azure AD B2C 계정에 대해 여러 소셜 ID를 추가할 수 있습니다. HTTPS PATCH 요청을 실행합니다. userObjectId를 사용자 ID로 바꿉니다.

**패치** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

다음과 같은 양식 데이터를 제출합니다.

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[선택 사항] 사용자 마이그레이션 애플리케이션 샘플
[샘플 앱 V2 다운로드 및 실행](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). 샘플 앱 V2는 로컬 계정, 소셜 계정, 단일 계정의 로컬 및 소셜 ID을 비롯한 더미 사용자 데이터가 포함된 JSON 파일을 사용합니다.  JSON 파일을 편집하려면 `AADB2C.UserMigration.sln` Visual Studio 솔루션을 엽니다. `AADB2C.UserMigration` 프로젝트에서 `UsersData.json` 파일을 엽니다. 이 파일에는 사용자 엔터티 목록이 포함되어 있습니다. 각 사용자 엔터티에는 다음과 같은 속성이 있습니다.
* **signInName** - 로컬 계정의 로그인 할 이메일 주소
* **displayName** - 사용자의 표시 이름
* **firstName** - 사용자의 이름
* **lastName** - 사용자의 성
* **password** 로컬 계정의 사용자의 암호(비워 둘 수 있음)
* **issuer** - 소셜 계정의 ID 공급자 이름
* **issuerUserId** - 소셜 계정의 소셜 ID 공급자가 사용하는 고유한 사용자 ID입니다. 값은 일반 텍스트여야 합니다. 샘플 앱이 값을 base64로 인코딩합니다.
* **email** 소셜 계정 전용(결합되지 않음) 사용자의 이메일 주소

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> 샘플의 UsersData.json 파일을 자신의 데이터로 업데이트하지 않으면 샘플 로컬 계정 자격 증명으로 로그인할 수 있지만 소셜 계정 예제로는 로그인할 수 없습니다. 소셜 계정을 마이그레이션하려면 실제 데이터를 제공합니다.

샘플 앱을 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory B2C: 사용자 마이그레이션](user-migration.md)을 참조하세요.
