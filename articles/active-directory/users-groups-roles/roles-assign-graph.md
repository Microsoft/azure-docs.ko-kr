---
title: Microsoft Graph API를 사용 하 여 Azure AD 관리자 역할 할당 | Microsoft Docs
description: Azure Active Directory에서 Graph API를 사용 하 여 Azure AD 관리자 역할 할당 및 제거
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f5be5829843e9857239ca5ea9a7395f569f563a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025335"
---
# <a name="assign-custom-admin-roles-using-graph-api-in-azure-active-directory"></a>Azure Active Directory에서 Graph API를 사용 하 여 사용자 지정 관리자 역할 할당 

API Microsoft Graph 사용자 계정에 역할을 할당 하는 방법을 자동화할 수 있습니다. 이 문서에서는 roleAssignments에 대 한 POST, GET 및 DELETE 작업에 대해 설명 합니다.

## <a name="required-permissions"></a>필요한 사용 권한

전역 관리자 계정 또는 권한 있는 Id 관리자를 사용 하 여 Azure AD 테넌트에 연결 하 여 역할을 할당 하거나 제거 합니다.

## <a name="post-operations-on-roleassignment"></a>RoleAssignment에 대 한 POST 작업

사용자와 역할 정의 간에 역할 할당을 만들기 위한 HTTP 요청입니다.

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

response

``` HTTP
HTTP/1.1 201 Created
```

주 또는 역할 정의가 없는 역할 할당을 만들기 위한 HTTP 요청

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

response

``` HTTP
HTTP/1.1 404 Not Found
```

기본 제공 역할 정의에 대 한 단일 리소스 범위 역할 할당을 만들기 위한 HTTP 요청입니다.

> [!NOTE] 
> 현재 기본 제공 역할에는 "/" 조직 전체의 범위 또는 "/AU/*" 범위로만 범위를 지정할 수 있는 제한 사항이 있습니다. 단일 리소스 범위 지정은 기본 제공 역할에 대해 작동 하지 않지만 사용자 지정 역할에 대해 작동 합니다.

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
}
```

response

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>RoleAssignment에 대 한 작업 가져오기

지정 된 보안 주체에 대 한 역할 할당을 가져오기 위한 HTTP 요청

가져오기

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=principalId eq ‘<object-id-of-principal>’
```

response

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

지정 된 역할 정의에 대 한 역할 할당을 가져오기 위한 HTTP 요청입니다.

가져오기

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

response

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

ID로 역할 할당을 가져오기 위한 HTTP 요청입니다.

가져오기

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

response

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>RoleAssignment에 대 한 삭제 작업

사용자와 역할 정의 간의 역할 할당을 삭제 하는 HTTP 요청입니다.

삭제

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

response
``` HTTP
HTTP/1.1 204 No Content
```

더 이상 존재 하지 않는 역할 할당을 삭제 하는 HTTP 요청

삭제

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

response

``` HTTP
HTTP/1.1 404 Not Found
```

자체 역할과 기본 제공 역할 정의 간의 역할 할당을 삭제 하는 HTTP 요청

삭제

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

response

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
* 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
