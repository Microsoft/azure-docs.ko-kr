---
title: 앱 역할 추가 및 토큰에서 가져오기 | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory에 등록된 애플리케이션에서 앱 역할을 추가하고 이 역할에 사용자와 그룹을 할당하여 토큰의 `roles` 클레임으로 받는 방법을 알아봅니다.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5a2acb08971bc0878c943047c42c9dc2a9525794
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141434"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>방법: 애플리케이션에 앱 역할 추가 및 토큰으로 수신

RBAC(역할 기반 액세스 제어)는 애플리케이션에서 권한 부여를 적용하는 데 널리 사용되는 메커니즘입니다. RBAC를 사용하는 경우, 관리자는 개별 사용자나 그룹이 아닌 역할에 사용 권한을 부여합니다. 그런 다음 관리자는 다른 사용자 및 그룹에 역할을 할당하여 누가 어떤 컨텐츠와 기능에 액세스 권한을 갖는지를 제어할 수 있습니다.

애플리케이션 역할 및 역할 클레임과 함께 RBAC를 사용하면 개발자는 노력을 거의 들이지 않고 자신의 앱에서 권한 부여를 안전하게 적용할 수 있습니다.

또 다른 방법은 [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet)의 내용처럼 Azure AD 그룹 및 그룹 클레임을 사용하는 것입니다. Azure AD 그룹 및 애플리케이션 역할은 전혀 상호 배타적이지 않습니다. 보다 세분화된 액세스 제어를 제공하기 위해 동시에 사용할 수 있습니다.

## <a name="declare-roles-for-an-application"></a>애플리케이션에 대한 역할 선언

이러한 애플리케이션 역할은 애플리케이션의 등록 매니페스트의 [Azure Portal](https://portal.azure.com)에서 정의됩니다.  사용자가 애플리케이션에 로그인하면 Azure AD는 사용자가 사용자에게 개별적으로 부여한 역할 및 자신의 그룹 멤버 자격에서 부여한 각각의 역할에 대해 `roles` 클레임을 발행합니다.  역할에 사용자 및 그룹을 할당하는 작업은 포털의 UI를 통해 수행하거나 [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview)를 사용하여 프로그래밍 방식으로 수행할 수 있습니다.

### <a name="declare-app-roles-using-azure-portal"></a>Azure Portal을 사용하여 앱 역할 선언

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **즐겨찾기** 또는 **모든 디렉터리** 목록에서 애플리케이션을 등록하려는 Active Directory 테넌트를 선택합니다.
1. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.
1. **Azure Active Directory** 창에서 **앱 등록**을 선택하여 모든 애플리케이션 목록을 봅니다.
1. 앱 역할을 정의하려는 애플리케이션을 선택합니다. 그런 후 **매니페스트**를 선택합니다.
1. `appRoles` 설정을 찾고 모든 애플리케이션 역할을 추가하여 앱 매니페스트를 편집합니다.

     > [!NOTE]
     > 이 매니페스트의 각 앱 역할 정의에는 `id` 속성의 매니페스트 컨텍스트 내에서 유효한 다른 GUID가 있어야 합니다.
     >
     > 각 앱 역할 정의의 `value` 속성은 애플리케이션의 코드에 사용되는 문자열과 정확히 일치해야 합니다. `value` 속성은 공백을 포함할 수 없습니다. 이 경우 매니페스트를 저장하면 오류가 표시됩니다.

1. 매니페스트를 저장합니다.

### <a name="examples"></a>예제

다음 예제는 `users`에게 할당할 수 있는 `appRoles`를 보여줍니다.

> [!NOTE]
>`id`는 고유 GUID여야 합니다.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>에는 `displayName` 공백이 포함 될 수 있습니다.

앱 역할은 `users`, `applications` 또는 둘 다를 대상으로 정의할 수 있습니다. 에서 사용할 수 있는 경우 `applications` 앱 역할은 api 권한 > **관리** 섹션에서 응용 프로그램 권한으로 표시 **> Api > 사용 권한 추가 > api > 응용 프로그램 사용 권한을 선택**합니다. 다음 예제는 `Application`을 대상으로 하는 앱 역할을 보여줍니다.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

정의된 역할 수는 애플리케이션 매니페스트에 포함된 제한에 영향을 줍니다. 이 내용은 [매니페스트 제한](./reference-app-manifest.md#manifest-limits) 페이지에서 자세히 설명합니다.

### <a name="assign-users-and-groups-to-roles"></a>역할에 사용자 및 그룹 할당

애플리케이션에서 앱 역할을 추가하고 나면 이 역할에 사용자 및 그룹을 할당 할 수 있습니다.

1. **Azure Active Directory** 창에 있는 **Azure Active Directory** 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 선택합니다.
1. **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

     여기에 원하는 애플리케이션이 보이지 않으면 **모든 애플리케이션** 목록의 맨 위에서 다양한 필터를 사용하여 목록을 제한하거나 목록을 아래로 스크롤하여 애플리케이션을 찾습니다.

1. 역할에 사용자 또는 보안 그룹을 할당할 애플리케이션을 선택합니다.
1. 애플리케이션의 왼쪽 탐색 메뉴에서 **사용자 및 그룹** 창을 선택합니다.
1. **사용자 및 그룹** 목록의 맨 위에서 **사용자 추가** 단추를 선택하여 **할당 추가** 창을 엽니다.
1. **할당 추가** 창에서 **사용자 및 그룹** 선택기를 선택합니다.

     특정 사용자 및 그룹을 검색하고 찾는 텍스트 상자와 함께 사용자 및 보안 그룹의 목록이 표시됩니다. 이 화면에서는 여러 사용자 및 그룹을 한 번에 선택할 수 있습니다.

1. 사용자 및 그룹을 모두 선택하고 나면 맨 아래 **선택** 단추를 눌러서 다음 부분으로 이동합니다.
1. **할당 추가** 창에서 **역할 선택** 선택기를 선택합니다. 앞서 앱 매니페스트에서 선언한 모든 역할이 표시됩니다.
1. 역할을 선택하고 **선택** 단추를 누릅니다.
1. 맨 아래 **할당** 단추를 눌러서 앱에 사용자 및 그룹의 할당을 완료합니다.
1. 추가한 사용자 및 그룹이 업데이트된 **사용자 및 그룹** 목록에 보이는지 확인합니다.

### <a name="receive-roles-in-tokens"></a>토큰에서 역할 수신

다양 한 앱 역할에 할당 된 사용자가 응용 프로그램에 로그인 하면 해당 토큰에는 클레임에 할당 된 역할이 있습니다 `roles` .

## <a name="more-information"></a>자세한 정보

- [ASP.NET Core 웹앱에 앱 역할 및 역할 클레임을 사용하는 권한 부여 추가](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Microsoft id 플랫폼을 사용 하 여 응용 프로그램에서 권한 부여 구현 (비디오)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory에 그룹 클레임 및 애플리케이션 역할 포함](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory 앱 매니페스트](./reference-app-manifest.md)
- [AAD 액세스 토큰](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)