---
title: 모든 Azure 구독 및 관리 그룹을 관리하는 액세스 권한 상승
description: Azure Portal 또는 REST API를 사용하여 Azure Active Directory에서 모든 구독 및 관리 그룹을 관리하는 글로벌 관리자에 대한 액세스 권한을 상승시키는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/03/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1731c630cb98ac542ebcdc7aedf07f7bb63eaec0
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137454"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>모든 Azure 구독 및 관리 그룹을 관리 하기 위해 액세스 권한 상승

Azure AD(Azure Active Directory)의 글로벌 관리자로서 디렉터리에 있는 모든 구독 및 관리 그룹에 대한 액세스 권한을 가질 수 없습니다. 이 문서에서는 모든 구독 및 관리 그룹에 대한 액세스 권한을 상승시킬 수 있는 방법을 설명합니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>액세스 권한을 상승시켜야 하는 이유는 무엇인가요?

글로벌 관리자인 경우 다음을 수행하려는 경우가 있을 수 있습니다.

- 사용자가 액세스 권한을 상실할 때 Azure 구독 또는 관리 그룹에 대한 액세스 권한 다시 얻기
- 다른 사용자 또는 사용자 자신에게 Azure 구독 또는 관리 그룹에 대한 액세스 권한 부여
- 조직에서 모든 Azure 구독 또는 관리 그룹 확인
- 자동화 앱(예: 송장 또는 감사 앱)이 모든 Azure 구독 또는 관리 그룹에 액세스하도록 허용

## <a name="how-does-elevated-access-work"></a>상승 된 액세스는 어떻게 작동 하나요?

Azure AD와 Azure 리소스는 서로 독립적으로 보호됩니다. 즉, Azure AD 역할을 할당해도 Azure 리소스에 대한 액세스가 부여되지 않고, Azure 역할을 할당해도 Azure AD에 대한 액세스가 부여되지 않습니다. 그러나 Azure AD의 [글로벌 관리자](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions)는 디렉터리에 있는 모든 Azure 구독 및 관리 그룹에 대한 액세스 권한을 자신에게 할당할 수 있습니다. 가상 머신이나 스토리지 계정 같은 Azure 구독 리소스에 액세스할 수 없고, 글로벌 관리자 권한을 사용하여 이러한 리소스에 대한 액세스 권한을 얻고 싶으면 이 기능을 사용하세요.

액세스 권한을 높이면 Azure의 루트 범위([)에서 ](built-in-roles.md#user-access-administrator)사용자 액세스 관리자`/` 역할이 할당됩니다. 이를 통해 모든 리소스를 보고 디렉터리의 모든 구독 또는 관리 그룹에서 액세스 권한을 할당할 수 있습니다. 사용자 액세스 관리자 역할 할당은 Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 제거할 수 있습니다.

루트 범위에서 필요한 변경 작업을 마친 후에는 상승된 액세스 권한을 제거해야 합니다.

![액세스 권한 상승](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure 포털

### <a name="elevate-access-for-a-global-administrator"></a>전역 관리자에 대한 액세스 권한 상승

다음 단계에 따라 Azure Portal을 사용하여 전역 관리자에 대한 액세스 권한을 상승시킵니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 글로벌 관리자로 로그인합니다.

1. **Azure Active Directory**를 검색하고 선택합니다.

   ![Azure Active Directory 선택-스크린샷](./media/elevate-access-global-admin/search-for-azure-active-directory.png)

1. **관리**아래에서 **속성**을 선택 합니다.

   ![Azure Active Directory 속성에 대 한 속성 선택-스크린샷](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. **Azure 리소스에 대한 액세스 관리**에서 토글을 **예**로 설정합니다.

   ![Azure 리소스에 대한 액세스 관리 - 스크린샷](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   설정/해제를 **예**로 설정 하면 루트 범위 (/)에서 Azure RBAC의 사용자 액세스 관리자 역할이 할당 됩니다. 그러면 이 Azure AD 디렉터리와 연결된 모든 Azure 구독 및 관리 그룹의 역할을 할당할 수 있는 권한이 부여됩니다. 이 토글은 Azure AD에서 글로벌 관리자 역할이 할당된 사용자만 사용할 수 있습니다.

   토글을 **아니요**로 설정하면 Azure RBAC의 사용자 액세스 관리자 역할이 사용자 계정에서 제거됩니다. 그러면 이 Azure AD 디렉터리와 연결된 모든 Azure 구독 및 관리 그룹의 역할을 더 이상 할당할 수 없습니다. 액세스 권한이 부여된 Azure 구독 및 관리 그룹만 살펴보고 관리할 수 있습니다.

    > [!NOTE]
    > [PIM (Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md))을 사용 하는 경우 역할 할당을 비활성화 해도이 토글은 **아니요**로 변경 되지 않습니다. 최소 권한 액세스를 유지 하려면 역할 할당을 비활성화 하기 전에이 토글을 **아니요** 로 설정 하는 것이 좋습니다.
    
1. **Save**를 클릭하여 설정을 저장합니다.

   이 설정은 글로벌 속성이 아니며 현재 로그인된 사용자에게만 적용됩니다. 글로벌 관리자 역할의 모든 멤버에 대한 액세스 권한을 상승시킬 수 없습니다.

1. 로그아웃하고 다시 로그인하여 액세스를 새로 고칩니다.

    이제 이 디렉터리의 모든 구독 및 관리 그룹에 대한 액세스 권한을 갖습니다. 액세스 제어 (IAM) 창을 보면 루트 범위에서 사용자 액세스 관리자 역할이 할당 된 것을 알 수 있습니다.

   ![루트 범위의 구독 역할 할당 - 스크린샷](./media/elevate-access-global-admin/iam-root.png)

1. 권한이 상승된 액세스를 확인하는 데 필요한 변경을 수행합니다.

    역할 할당에 대한 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](role-assignments-portal.md)를 참조하세요. Azure AD PIM(Privileged Identity Management)을 사용하는 경우 [PIM에서 관리할 Azure 리소스 검색](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) 또는 [PIM에서 Azure 리소스 역할 할당](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)을 참조하세요.

### <a name="remove-elevated-access"></a>상승된 액세스 제거

루트 범위 (`/`)에서 사용자 액세스 관리자 역할 할당을 제거 하려면 다음 단계를 수행 합니다.

1. 액세스 권한을 상승 시키는 데 사용 된 것과 동일한 사용자로 로그인 합니다.

1. 탐색 목록에서 **Azure Active Directory**를 클릭한 다음, **속성**을 클릭합니다.

1. **Azure 리소스에 대 한 액세스 관리** **를 다시**설정/해제로 설정 합니다. 사용자별 설정이므로 액세스 권한을 상승시키는 데 사용했던 동일한 사용자로 로그인해야 합니다.

    액세스 제어 (IAM) 창에서 사용자 액세스 관리자 역할 할당을 제거 하려고 하면 다음과 같은 메시지가 표시 됩니다. 역할 할당을 제거 하려면 다시 설정/해제를 **아니요** 로 설정 하거나 Azure PowerShell, Azure CLI 또는 REST API를 사용 하도록 설정 해야 합니다.

    ![루트 범위를 사용 하 여 역할 할당 제거](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>루트 범위 (/)에서 역할 할당 나열

루트 범위 (`/`)에서 사용자에 대 한 사용자 액세스 관리자 역할 할당을 나열 하려면 [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 명령을 사용 합니다.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>상승된 액세스 제거

사용자에 대 한 사용자 액세스 관리자 역할 할당 또는 루트 범위 (`/`)에서 다른 사용자를 제거 하려면 다음 단계를 수행 합니다.

1. 상승된 액세스 권한을 제거할 수 있는 사용자로 로그인합니다. 이 사용자는 액세스 권한을 상승 시키는 데 사용 되거나 루트 범위에서 관리자 권한으로 액세스 하는 다른 전역 관리자가 될 수 있습니다.

1. [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 명령을 사용하여 사용자 액세스 관리자 역할 할당을 제거합니다.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-role-assignment-at-root-scope-"></a>루트 범위 (/)에서 역할 할당 나열

루트 범위 (`/`)에서 사용자에 대 한 사용자 액세스 관리자 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list) 명령을 사용 합니다.

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>상승된 액세스 제거

사용자에 대 한 사용자 액세스 관리자 역할 할당 또는 루트 범위 (`/`)에서 다른 사용자를 제거 하려면 다음 단계를 수행 합니다.

1. 상승된 액세스 권한을 제거할 수 있는 사용자로 로그인합니다. 이 사용자는 액세스 권한을 상승 시키는 데 사용 되거나 루트 범위에서 관리자 권한으로 액세스 하는 다른 전역 관리자가 될 수 있습니다.

1. [Az role 할당 delete](/cli/azure/role/assignment#az-role-assignment-delete) 명령을 사용 하 여 사용자 액세스 관리자 역할 할당을 제거 합니다.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>전역 관리자에 대한 액세스 권한 상승

다음과 같은 기본 단계를 사용하여 REST API를 사용하는 전역 관리자에 대한 액세스 권한을 상승시킵니다.

1. REST를 사용 하 여 루트 범위 (`/`)에서 사용자 액세스 관리자 역할을 부여 하는 `elevateAccess`를 호출 합니다.

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. 모든 범위의 모든 역할을 할당하는 [역할 할당](/rest/api/authorization/roleassignments)을 만듭니다. 다음 예제에서는 루트 범위 (`/`)에서 {roleDefinitionID} 역할을 할당 하기 위한 속성을 보여 줍니다.

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. 사용자 액세스 관리자는 루트 범위 (`/`)에서 역할 할당을 제거할 수도 있습니다.

1. 다시 필요할 때까지 사용자 액세스 관리자 권한을 제거합니다.

### <a name="list-role-assignments-at-root-scope-"></a>루트 범위 (/)에서 역할 할당 나열

루트 범위 (`/`)에서 사용자에 대 한 모든 역할 할당을 나열할 수 있습니다.

- [가 역할 할당을 검색하려는 사용자의 개체 ID인 경우 ](/rest/api/authorization/roleassignments/listforscope)GET roleAssignments`{objectIdOfUser}`를 호출합니다.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>루트 범위 (/)에서 거부 할당 나열

루트 범위 (`/`)에서 사용자에 대 한 모든 거부 할당을 나열할 수 있습니다.

- `{objectIdOfUser}`가 거부 할당을 검색하려는 사용자의 개체 ID인 경우 GET denyAssignments를 호출합니다.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>상승된 액세스 제거

`elevateAccess`를 호출 하는 경우 자신에 대 한 역할 할당을 만들어 해당 권한을 취소 하려면 루트 범위 (`/`)에서 자신에 대 한 사용자 액세스 관리자 역할 할당을 제거 해야 합니다.

1. [이 사용자 액세스 관리자인 ](/rest/api/authorization/roledefinitions/get)GET roleDefinitions`roleName`를 호출하여 사용자 액세스 관리자 역할의 ID 이름을 확인합니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    `name` 같은 경우에 `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` 매개 변수의 ID를 저장합니다.

1. 또한 디렉터리 범위에서 디렉터리 관리자의 역할 할당을 나열해야 합니다. 액세스 권한 상승 호출을 수행한 디렉터리 관리자의 `principalId`에 대한 디렉터리 범위에 포함되는 모든 할당을 나열합니다. 그러면 objectid에 대한 디렉터리의 모든 할당이 나열됩니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >디렉터리 관리자는 할당이 많지 않아야 합니다. 이전 쿼리에서 너무 많은 할당을 반환하는 경우 디렉터리 범위 수준에서만 모든 할당을 쿼리한 다음, 결과를 필터링할 수도 있습니다. `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. 이전 호출은 역할 할당 목록을 반환합니다. 범위가 `"/"`이고 `roleDefinitionId`가 1단계에서 찾은 역할 이름 ID로 끝나는 역할 할당을 찾고 `principalId`가 디렉터리 관리자의 objectId와 일치합니다. 
    
    샘플 역할 할당:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    다시 `name` 매개 변수 (이 경우 11111111-1111-1111-1111-111111111111)에서 ID를 저장 합니다.

1. 마지막으로 역할 할당 ID를 사용하여 `elevateAccess`에 의해 추가된 할당을 제거합니다.

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>다음 단계

- [Azure의 다양한 역할 이해](rbac-and-directory-admin-roles.md)
- [RBAC 및 REST API를 사용하여 Azure 리소스에 대한 액세스 관리](role-assignments-rest.md)
