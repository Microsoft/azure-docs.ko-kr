---
title: '자습서: 태그 거버넌스 관리'
description: 이 자습서에서는 Azure Policy의 Modify 효과를 사용하여 신규 및 기존 리소스에 대한 태그 거버넌스 모델을 만들고 적용합니다.
ms.date: 08/17/2020
ms.topic: tutorial
ms.openlocfilehash: f49eedb00c98d3c362140fdca9b195a086903f10
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545508"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>자습서: Azure Policy를 사용하여 태그 거버넌스 관리

[태그](../../../azure-resource-manager/management/tag-resources.md)는 Azure 리소스를 분류 체계로 구성하는 데 중요한 부분입니다. [태그 관리 모범 사례](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)를 수행하는 동안 태그는 Azure Policy를 통해 비즈니스 정책을 적용하거나 [Cost Management를 통해 비용을 추적](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources)하기 위한 기초가 될 수 있습니다.
태그를 사용하는 방식이나 이유에 관계없이 Azure 리소스에서 태그를 빠르게 추가, 변경 및 제거할 수 있어야 합니다. Azure 리소스가 태그 지정을 지원하는지 확인하려면 [태그 지원](../../../azure-resource-manager/management/tag-support.md)을 참조하세요.

Azure Policy의 [Modify](../concepts/effects.md#modify) 효과는 리소스 거버넌스 단계에 상관없이 태그 거버넌스에 도움이 되도록 설계되었습니다. **수정**이 도움이 되는 경우:

- 클라우드를 처음 사용하고 태그 거버넌스가 없는 경우
- 태그 거버넌스가 없는 수천 개의 리소스가 이미 있는 경우
- 변경해야 하는 기존 분류 체계가 이미 있는 경우

이 자습서에서는 다음 작업을 완료할 것입니다.

> [!div class="checklist"]
> - 비즈니스 요구 사항 식별
> - 각 요구 사항을 정책 정의에 매핑
> - 태그 정책을 이니셔티브로 그룹화

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/)을 만드세요.

## <a name="identify-requirements"></a>ID 요구 사항

거버넌스 제어를 구현하는 경우와 마찬가지로, 비즈니스 요구에 맞는 요구 사항을 준비하여 기술 제어를 만들기 전에 잘 이해해야 합니다. 이 시나리오 자습서의 경우, 비즈니스 요구 사항은 다음과 같습니다.

- 모든 리소스에 필요한 태그 두 개: _CostCenter_ 및 _Env_
- _CostCenter_는 모든 컨테이너와 개별 리소스에 존재해야 합니다.
  - 리소스는 포함된 컨테이너에서 상속되지만 개별적으로 재정의될 수 있습니다.
- _Env_는 모든 컨테이너와 개별 리소스에 존재해야 합니다.
  - 리소스는 컨테이너 이름 지정 체계를 통해 환경을 확인하며, 재정의될 수 없습니다.
  - 컨테이너의 모든 리소스는 동일한 환경의 일부입니다.

## <a name="configure-the-costcenter-tag"></a>CostCenter 태그 구성

Azure Policy로 관리되는 Azure 환경과 관련하여 _CostCenter_ 태그 요구 사항은 다음과 같은 결과를 호출합니다.

- _CostCenter_ 태그가 누락된 Deny 리소스 그룹
- 누락된 경우 부모 리소스 그룹의 _CostCenter_ 태그를 추가하도록 리소스 수정

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>CostCenter 태그가 누락된 Deny 리소스 그룹

리소스 그룹의 _CostCenter_는 리소스 그룹의 이름으로 확인할 수 없기 때문에 리소스 그룹을 생성하는 요청에 태그가 정의되어 있어야 합니다. [Deny](../concepts/effects.md#deny) 효과가 포함된 다음 정책 규칙은 _CostCenter_ 태그가 없는 리소스 그룹이 생성되거나 업데이트되지 않도록 합니다.

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> 이 정책 규칙은 리소스 그룹을 대상으로 하기 때문에 정책 정의의 _mode_는 'Indexed'가 아닌 'All'이어야 합니다.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>누락된 경우 CostCenter 태그를 상속하도록 리소스 수정

두 번째 _CostCenter_ 요구 사항은 태그가 없는 경우 리소스가 부모 리소스 그룹에서 태그를 상속하는 것입니다. 리소스에 태그가 이미 정의되어 있으면, 부모 리소스 그룹과 다르더라도 태그를 그대로 두어야 합니다. 다음 정책 규칙은 [Modify](../concepts/effects.md#modify)를 사용합니다.

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

이 정책 규칙은 기존 리소스를 [수정](../how-to/remediate-resources.md)할 때 태그 값이 있으면 변경하지 않기 위해 **addOrReplace** 대신 **add** 작업을 사용합니다. 또한 `[resourcegroup()]` 템플릿 함수를 사용하여 부모 리소스 그룹에서 태그 값을 가져옵니다.

> [!NOTE]
> 이 정책 규칙은 태그를 지원하는 리소스를 대상으로 하기 때문에 정책 정의의 _mode_는 'Indexed'여야 합니다. 이 구성을 사용하면 이 정책이 리소스 그룹을 건너뜁니다.

## <a name="configure-the-env-tag"></a>Env 태그 구성

Azure Policy로 관리되는 Azure 환경과 관련하여 _Env_ 태그 요구 사항은 다음 결과를 호출합니다.

- 리소스 그룹의 이름 지정 체계를 기반으로 리소스 그룹의 _Env_ 태그를 수정합니다.
- 리소스 그룹의 모든 리소스에서 _Env_ 태그를 부모 리소스 그룹과 동일하게 수정합니다.

### <a name="modify-resource-groups-env-tag-based-on-name"></a>이름을 기반으로 리소스 그룹 Env 태그 수정

[Modify](../concepts/effects.md#modify) 정책은 Azure 환경에 존재하는 각 환경마다 필요합니다. 각각에 대한 Modify 정책은 아래 정책 정의와 유사합니다.

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> 이 정책 규칙은 리소스 그룹을 대상으로 하기 때문에 정책 정의의 _mode_는 'Indexed'가 아닌 'All'이어야 합니다.

이 정책은 리소스 그룹을 프로덕션 리소스에 사용되는 샘플 이름 지정 체계 `prd-`와만 매칭합니다. 이 예제와 같이 **like**만 사용하는 대신 여러 가지 **match** 조건을 사용하면 더 복잡한 이름 지정 체계를 구현할 수 있습니다.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Env 태그를 상속하도록 리소스 수정

비즈니스 요구 사항에 따라 모든 리소스에는 상위 리소스 그룹에 있는 _Env_ 태그가 있어야 합니다. 이 태그는 재정의할 수 없기 때문에 **addOrReplace** 작업을[Modify](../concepts/effects.md#modify) 효과와 함께 사용합니다. 샘플 Modify 정책은 다음 규칙과 유사합니다.

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> 이 정책 규칙은 태그를 지원하는 리소스를 대상으로 하기 때문에 정책 정의의 _mode_는 'Indexed'여야 합니다. 이 구성을 사용하면 이 정책이 리소스 그룹을 건너뜁니다.

이 정책 규칙은 _Env_ 태그에 대한 부모 리소스 그룹 값이 없거나 _Env_ 태그가 없는 리소스를 찾습니다. 리소스에 태그가 이미 있지만 값이 다른 경우에도, 일치 리소스에는 부모 리소스 그룹 값으로 설정된 _Env_ 태그가 있습니다.

## <a name="assign-the-initiative-and-remediate-resources"></a>이니셔티브 할당 및 리소스 수정

위의 태그 정책이 생성되면 태그 거버넌스를 위한 단일 이니셔티브에 조인하여 관리 그룹 또는 구독에 할당합니다. 그러면 이니셔티브 및 포함된 정책이 기존 리소스의 준수 여부를 평가하고 정책 규칙의 **if** 속성과 일치하는 신규 리소스나 업데이트된 리소스에 대한 요청을 변경합니다. 단, 이 정책은 기존 비준수 리소스를 정의된 태그 변경 내용으로 자동 업데이트하지 않습니다.

[deployIfNotExists](../concepts/effects.md#deployifnotexists) 정책처럼 **Modify** 정책은 수정 작업을 사용하여 기존 비준수 리소스를 변경합니다. [리소스를 수정하는 방법](../how-to/remediate-resources.md)의 지침에 따라 비준수 **Modify** 리소스를 식별하고 정의된 분류 체계에 맞게 태그를 수정할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 리소스를 모두 마쳤으면 다음 단계를 사용하여 위에서 만든 할당 또는 정의를 삭제합니다.

1. Azure Policy 페이지의 왼쪽 창에 있는 **작성** 아래에서 **정의**(또는 할당을 삭제하려는 경우 **할당**을 선택)를 선택합니다.

1. 제거할 새 이니셔티브 또는 정책 정의(또는 할당)를 검색합니다.

1. 행을 마우스 오른쪽 단추로 클릭하거나 정의(또는 할당) 끝에 있는 줄임표를 선택하고 **정의 삭제**(또는 **할당 삭제**)를 선택합니다.

## <a name="review"></a>검토

이 자습서에서는 다음 작업에 대해 알아보았습니다.

> [!div class="checklist"]
> - 비즈니스 요구 사항 식별
> - 각 요구 사항을 정책 정의에 매핑
> - 태그 정책을 이니셔티브로 그룹화

## <a name="next-steps"></a>다음 단계

정책 정의의 구조에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Policy 정의 구조](../concepts/definition-structure.md)