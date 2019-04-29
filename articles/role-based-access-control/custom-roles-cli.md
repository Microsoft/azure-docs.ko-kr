---
title: Azure CLI를 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기 | Microsoft Docs
description: Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어)로 Azure 리소스에 대한 사용자 지정 역할을 만드는 방법을 알아봅니다. 여기에는 사용자 지정 역할을 나열, 생성, 업데이트 및 삭제하는 방법이 포함됩니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118776"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Azure CLI를 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기

[Azure 리소스에 대한 기본 제공 역할](built-in-roles.md)이 조직의 특정 요구 사항을 충족하지 않는 경우 사용자 지정 역할을 만들면 됩니다. 이 문서에서는 Azure CLI를 사용하여 사용자 지정 역할을 만들고 관리하는 방법에 대해 설명합니다.

사용자 지정 역할을 만드는 방법에 대한 단계별 자습서는 [자습서: Azure CLI를 사용 하 여 Azure 리소스에 대 한 사용자 지정 역할을 만드는](tutorial-custom-role-cli.md)합니다.

## <a name="prerequisites"></a>필수 조건

사용자 지정 역할을 만들려면 다음이 필요합니다.

- 사용자 지정 역할을 만들 수 있는 권한(예: [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator))
- [Azure Cloud Shell](../cloud-shell/overview.md) 또는 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>사용자 지정 역할 나열

할당에 사용할 수 있는 사용자 지정 역할을 나열하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용합니다. 다음 예제에서는 현재 구독의 모든 사용자 지정 역할을 나열합니다.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>사용자 지정 역할 정의 나열

사용자 지정 역할 정의 나열 하려면 사용 하 여 [az 역할 정의 목록](/cli/azure/role/definition#az-role-definition-list)합니다. 기본 제공 역할을 사용 하면 동일한 명령입니다.

```azurecli
az role definition list --name <role_name>
```

다음 예제에서는 목록 합니다 *Virtual Machine Operator* 역할 정의:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

다음 예제에서는 방금의 작업을 나열 합니다 *Virtual Machine Operator* 역할:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
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
]
```

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 [az role definition create](/cli/azure/role/definition#az-role-definition-create)를 사용합니다. 역할 정의는 JSON 설명이거나, JSON 설명이 포함된 파일에 대한 경로가 될 수 있습니다.

```azurecli
az role definition create --role-definition <role_definition>
```

다음 예제에서는 *Virtual Machine Operator*라는 사용자 지정 역할을 만듭니다. 이 사용자 지정 역할은 *Microsoft.Compute*, *Microsoft.Storage* 및 *Microsoft.Network* 리소스 공급자의 모든 읽기 작업에 대한 액세스 권한을 부여하고 가상 머신을 시작, 다시 시작 및 모니터링할 수 있는 권한을 부여합니다. 두 구독 모두에서 사용자 지정 역할을 사용할 수 있습니다. 이 예제에서는 입력으로 JSON 파일을 사용합니다.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 업데이트하려면 먼저 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용하여 역할 정의를 검색합니다. 그런 다음 역할 정의를 원하는 대로 변경합니다. 마지막으로 [az role definition update](/cli/azure/role/definition#az-role-definition-update)를 사용하여 업데이트된 역할 정의를 저장합니다.

```azurecli
az role definition update --role-definition <role_definition>
```

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할의 *Actions*에 *Microsoft.Insights/diagnosticSettings/* 작업을 추가합니다.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

사용자 지정 역할을 삭제하려면 [az role definition delete](/cli/azure/role/definition#az-role-definition-delete)를 사용합니다. 삭제할 역할을 지정하려면 역할 이름이나 역할 ID를 사용합니다. 역할 ID를 결정하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용합니다.

```azurecli
az role definition delete --name <role_name or role_id>
```

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 삭제합니다.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure CLI를 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기](tutorial-custom-role-cli.md)
- [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)
- [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)