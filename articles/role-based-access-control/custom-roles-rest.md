---
title: REST API를 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기 - Azure | Microsoft Docs
description: REST API를 사용하여 RBAC(역할 기반 액세스 제어)로 Azure 리소스에 대한 사용자 지정 역할을 만드는 방법을 알아봅니다. 여기에는 사용자 지정 역할을 나열, 생성, 업데이트 및 삭제하는 방법이 포함됩니다.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4024f6fdb40c752ef61f348d15f681e81d81c08c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596686"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>REST API를 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기

[Azure 리소스에 대한 기본 제공 역할](built-in-roles.md)이 조직의 특정 요구 사항을 충족하지 않는 경우 사용자 지정 역할을 만들면 됩니다. 이 문서에서는 REST API를 사용하여 사용자 지정 역할을 만들고 관리하는 방법에 대해 설명합니다.

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

디렉터리에서 모든 사용자 지정 역할을 나열 하려면 사용 합니다 [역할 정의-목록](/rest/api/authorization/roledefinitions/list) REST API입니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 바꿉니다 *{filter}* 역할 형식입니다.

    | Filter | 설명 |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | CustomRole 유형을 기준으로 필터링 |

## <a name="list-custom-roles-at-a-scope"></a>범위에서 사용자 지정 역할 나열

범위에서 사용자 지정 역할을 나열 하려면 사용 합니다 [역할 정의-목록](/rest/api/authorization/roledefinitions/list) REST API입니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 나열하려는 역할에 대한 범위로 바꿉니다.

    | 범위 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. 바꿉니다 *{filter}* 역할 형식입니다.

    | Filter | 설명 |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | CustomRole 유형을 기준으로 필터링 |

## <a name="list-a-custom-role-definition-by-name"></a>이름별으로 사용자 지정 역할 정의 나열

표시 이름을 사용 하 여 사용자 지정 역할에 대 한 정보를 가져오려면를 사용 합니다 [역할 정의 가져오기](/rest/api/authorization/roledefinitions/get) REST API입니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. URI 내에서 *{scope}* 를 나열하려는 역할에 대한 범위로 바꿉니다.

    | 범위 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. 바꿉니다 *{filter}* 역할에 대 한 표시 이름을 사용 합니다.

    | Filter | 설명 |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | 역할의 정확한 표시 이름에 대한 URL 인코딩 형식을 사용합니다. 예: `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>ID 별로 사용자 지정 역할 정의 나열

정보를 가져오려면 사용자 지정 역할을 하는 방법에 대 한 고유 식별자를 사용 합니다 [역할 정의 가져오기](/rest/api/authorization/roledefinitions/get) REST API입니다.

1. [역할 정의 - 나열](/rest/api/authorization/roledefinitions/list) REST API를 사용하여 역할에 대한 GUID 식별자를 가져옵니다.

1. 다음 요청으로 시작합니다.

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 나열하려는 역할에 대한 범위로 바꿉니다.

    | 범위 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. *{roleDefinitionId}* 를 역할 정의의 GUID 식별자로 바꿉니다.

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 [역할 정의 - 만들기 또는 업데이트](/rest/api/authorization/roledefinitions/createorupdate) REST API를 사용합니다. 이 API를 호출 하려면 있습니다 서명 될 있는 역할이 할당 된 사용자로 로그인 합니다 `Microsoft.Authorization/roleDefinitions/write` 모두에 대 한 권한이 `assignableScopes`. 기본 제공 역할의 경우만 [소유자](built-in-roles.md#owner) 하 고 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 이 사용 권한을 포함 합니다.

1. 사용자 지정 역할에 대한 권한을 만드는 데 사용할 수 있는 [리소스 공급자 작업](resource-provider-operations.md) 목록을 검토합니다.

1. GUID 도구를 사용하여 사용자 지정 역할 식별자에 사용할 고유 식별자를 생성합니다. 식별자의 형식은 `00000000-0000-0000-0000-000000000000`입니다.

1. 다음 요청 및 본문으로 시작합니다.

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. URI 내에서 *{scope}* 를 사용자 지정 역할의 첫 번째 `assignableScopes`로 바꿉니다.

    | 범위 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. *{roleDefinitionId}* 를 사용자 지정 역할의 GUID 식별자로 바꿉니다.

1. 요청 본문 내의 `assignableScopes` 속성에서 *{roleDefinitionId}* 를 GUID 식별자로 바꿉니다.

1. *{subscriptionId}* 를 구독 ID로 바꿉니다.

1. `actions` 속성에서 역할에서 수행할 수 있는 작업을 추가합니다.

1. `notActions` 속성에서 허용된 `actions`로부터 제외되는 작업을 추가합니다.

1. `roleName` 및 `description` 속성에서 고유한 역할 이름 및 설명을 지정합니다. 속성에 대한 자세한 내용은 [사용자 지정 역할](custom-roles.md)을 참조하세요.

    요청 본문의 예제는 다음과 같습니다.

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 업데이트하려면 [역할 정의 - 만들기 또는 업데이트](/rest/api/authorization/roledefinitions/createorupdate) REST API를 사용합니다. 이 API를 호출 하려면 있습니다 서명 될 있는 역할이 할당 된 사용자로 로그인 합니다 `Microsoft.Authorization/roleDefinitions/write` 모두에 대 한 권한이 `assignableScopes`. 기본 제공 역할의 경우만 [소유자](built-in-roles.md#owner) 하 고 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 이 사용 권한을 포함 합니다.

1. [역할 정의 - 나열 ](/rest/api/authorization/roledefinitions/list) 또는 [ 역할 정의 - 가져오기](/rest/api/authorization/roledefinitions/get) REST API를 사용하여 사용자 지정 역할에 대한 정보를 가져옵니다. 자세한 내용은 참조는 앞서 [사용자 지정 역할 나열](#list-custom-roles) 섹션입니다.

1. 다음 요청으로 시작합니다.

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 사용자 지정 역할의 첫 번째 `assignableScopes`로 바꿉니다.

    | 범위 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. *{roleDefinitionId}* 를 사용자 지정 역할의 GUID 식별자로 바꿉니다.

1. 사용자 지정 역할에 대한 정보에 따라 다음 형식의 요청 본문을 만듭니다.

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. 요청 본문을 사용자 지정 역할에 적용하려는 변경 내용으로 업데이트합니다.

    새 진단 설정 작업이 추가된 요청 본문의 예제는 다음과 같습니다.

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

사용자 지정 역할을 삭제하려면 [역할 정의 - 삭제](/rest/api/authorization/roledefinitions/delete) REST API를 사용합니다. 이 API를 호출 하려면 있습니다 서명 될 있는 역할이 할당 된 사용자로 로그인 합니다 `Microsoft.Authorization/roleDefinitions/delete` 모두에 대 한 권한이 `assignableScopes`. 기본 제공 역할의 경우만 [소유자](built-in-roles.md#owner) 하 고 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 이 사용 권한을 포함 합니다.

1. [역할 정의 - 나열 ](/rest/api/authorization/roledefinitions/list) 또는 [역할 정의 - 가져오기](/rest/api/authorization/roledefinitions/get) REST API를 사용하여 사용자 지정 역할의 GUID 식별자를 가져옵니다. 자세한 내용은 참조는 앞서 [사용자 지정 역할 나열](#list-custom-roles) 섹션입니다.

1. 다음 요청으로 시작합니다.

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 내에서 *{scope}* 를 삭제하려는 사용자 지정 역할에 대한 범위로 바꿉니다.

    | 범위 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 구독 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 리소스 그룹 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 리소스 |

1. *{roleDefinitionId}* 를 사용자 지정 역할의 GUID 식별자로 바꿉니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)
- [RBAC 및 REST API를 사용하여 Azure 리소스에 대한 액세스 관리](role-assignments-rest.md)
- [Azure REST API 참조](/rest/api/azure/)