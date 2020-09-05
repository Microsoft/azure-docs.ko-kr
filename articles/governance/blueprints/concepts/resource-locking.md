---
title: 리소스 잠금 이해
description: 청사진을 할당할 때 리소스를 보호 하기 위해 Azure 청사진의 잠금 옵션에 대해 알아봅니다.
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: 5e60724a276bce94770c5fdc33ee0c8b4e7712fe
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051505"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure Blueprints의 리소스 잠금 이해

일관성 있는 환경을 원하는 규모로 만든다고 해도 해당 일관성을 유지할 수 있는 메커니즘이 있어야 환경을 적절하게 활용할 수 있습니다. 이 문서에서는 Azure Blueprints에서 리소스 잠금이 작동하는 방식에 대해 설명합니다. 리소스 잠금 및 _거부 할당_의 응용 프로그램에 대 한 예제를 보려면 [새 리소스 보호](../tutorials/protect-new-resources.md) 자습서를 참조 하세요.

> [!NOTE]
> Azure 청사진에서 배포 하는 리소스 잠금은 청사진 할당을 통해 배포 된 리소스에만 적용 됩니다. 이미 존재 하는 리소스 그룹의 기존 리소스에는 잠금이 추가 되지 않습니다.

## <a name="locking-modes-and-states"></a>잠금 모드 및 상태

잠금 모드는 청사진 할당에 적용 되며, **잠금 안 함**, **읽기 전용**또는 **삭제**안 함과 같은 세 가지 옵션이 있습니다. 잠금 모드는 청사진 할당을 수행하는 동안 아티팩트 배포 중에 구성됩니다. 청사진 할당을 업데이트하여 다른 잠금 모드를 설정할 수 있습니다.
그러나 잠금 모드는 Azure 청사진 외부에서 변경할 수 없습니다.

청사진 할당에서 아티팩트에 의해 생성 된 리소스에는 **잠김 안 함**, **읽기 전용**, **편집/삭제**또는 **삭제할 수**없음의 네 가지 상태가 있습니다. 각 아티팩트 형식은 **잠겨있지 않음** 상태일 수 있습니다. 다음 표를 사용하여 리소스의 상태를 확인할 수 있습니다.

|Mode|아티팩트 리소스 형식|시스템 상태|Description|
|-|-|-|-|
|잠그지 않음|*|잠겨있지 않음|리소스는 Azure 청사진에 의해 보호 되지 않습니다. 이 상태는 또한 청사진 할당 외부에서 **읽기 전용** 또는 **삭제 안 함** 리소스 그룹 아티팩트에 추가된 리소스에 사용됩니다.|
|읽기 전용|Resource group|편집/삭제할 수 없음|리소스 그룹이 읽기 전용이며 리소스 그룹의 태그를 수정할 수 없습니다. **잠겨 있지 않음** 리소스는 이 리소스 그룹에서 추가, 이동, 변경 또는 삭제할 수 있습니다.|
|읽기 전용|리소스 그룹이 아님|읽기 전용|리소스는 어떤 방식으로든 변경할 수 없습니다. 변경 내용 및 삭제할 수 없습니다.|
|삭제 안 함|*|삭제할 수 없음|리소스를 변경할 수 있지만 삭제할 수 없습니다. **잠겨 있지 않음** 리소스는 이 리소스 그룹에서 추가, 이동, 변경 또는 삭제할 수 있습니다.|

## <a name="overriding-locking-states"></a>잠금 상태 재정의

일반적으로 구독에서 ‘소유자’ 역할과 같은 RBAC([역할 기반 액세스 제어](../../../role-based-access-control/overview.md)) 구독이 있는 사용자는 리소스를 변경하거나 삭제할 수 있습니다. 이 액세스는 Azure 청사진에서 배포 된 할당의 일부로 잠금을 적용 하는 경우에는 그렇지 않습니다. 할당이 **읽기 전용** 또는 **삭제 안 함** 옵션으로 설정된 경우에는 구독 소유자여도 보호된 리소스에서 차단된 작업을 수행할 수 없습니다.

이 보안 조치는 정의된 청사진과 해당 청사진을 통해 생성하려는 환경을 실수로/프로그래밍 방식으로 삭제하거나 변경하는 상황을 방지하여 일관성을 유지합니다.

### <a name="assign-at-management-group"></a>관리 그룹에서 할당

구독 소유자가 청사진 할당을 제거 하지 못하도록 방지 하는 추가 옵션은 청사진을 관리 그룹에 할당 하는 것입니다. 이 시나리오에서는 관리 그룹 **소유자** 에게만 청사진 할당을 제거 하는 데 필요한 권한이 있습니다.

구독 대신 관리 그룹에 청사진을 할당 하기 위해 REST API 호출은 다음과 같이 변경 됩니다.

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

로 정의 된 관리 그룹은 `{assignmentMG}` 관리 그룹 계층 구조 내에 있거나 청사진 정의가 저장 된 관리 그룹 이어야 합니다.

청사진 할당의 요청 본문은 다음과 같습니다.

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

이 요청 본문의 주요 차이점과 구독에 할당 되는 항목은 `properties.scope` 속성입니다. 이 필수 속성은 청사진 할당이 적용 되는 구독으로 설정 되어야 합니다. 구독은 청사진 할당이 저장 된 관리 그룹 계층의 직계 자식 이어야 합니다.

> [!NOTE]
> 관리 그룹 범위에 할당 된 청사진은 여전히 구독 수준 청사진 할당으로 작동 합니다. 유일한 차이점은 구독 소유자가 할당 및 연관 된 잠금을 제거 하지 못하도록 청사진 할당을 저장 하는 위치입니다.

## <a name="removing-locking-states"></a>잠금 상태 제거

할당에서 보호되는 리소스를 수정하거나 삭제해야 하는 경우 두 가지 방법이 있습니다.

- 청사진 할당을 **잠그지 않음** 잠금 모드로 업데이트
- 청사진 할당 삭제

할당이 제거 되 면 Azure 청사진에서 만든 잠금이 제거 됩니다. 그러나 리소스는 그대로 남아 있으므로 일반적인 방법으로 삭제해야 합니다.

## <a name="how-blueprint-locks-work"></a>청사진 잠금의 작동 방식

할당에서 **읽기 전용** 또는 **삭제 안 함** 옵션을 선택한 경우 RBAC [거부 할당](../../../role-based-access-control/deny-assignments.md) 거부 동작이 청사진 할당 중에 아티팩트 리소스에 적용됩니다. 거부 동작은 청사진 할당의 관리 ID를 통해 추가되며 같은 관리 ID를 통해서만 아티팩트 리소스에서 제거할 수 있습니다. 이 보안 조치로 인해 잠금 메커니즘이 적용 되며 Azure 청사진 외부에서 청사진 잠금이 제거 되지 않습니다.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="리소스 그룹에 대 한 청사진 거부 할당" border="false":::

각 모드의 [거부 할당 속성](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) 은 다음과 같습니다.

|Mode |사용 권한. 작업 |Permissions 작업 |보안 주체 [i]. 입력할 |ExcludePrincipals [i]. A-id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|읽기 전용 |**\*** |**\*/read** |SystemDefined (Everyone) |**excludedPrincipals** 의 청사진 할당 및 사용자 정의 |리소스 그룹- _true_; 리소스- _false_ |
|삭제 안 함 |**\*/delete** | |SystemDefined (Everyone) |**excludedPrincipals** 의 청사진 할당 및 사용자 정의 |리소스 그룹- _true_; 리소스- _false_ |

> [!IMPORTANT]
> Azure Resource Manager는 최대 30분 동안 역할 할당 정보를 캐시합니다. 결과적으로, 청사진 리소스에 대한 거부 할당 거부 동작은 즉시 완전히 적용되지는 않을 수도 있습니다. 이 기간 동안 청사진 잠금으로 보호해야 하는 리소스가 삭제될 수도 있습니다.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>거부 할당에서 보안 주체 제외

일부 디자인 또는 보안 시나리오에서는 청사진 할당이 만드는 [거부 할당](../../../role-based-access-control/deny-assignments.md) 에서 보안 주체를 제외 해야 할 수 있습니다. 이 단계는 [할당을 만들](/rest/api/blueprints/assignments/createorupdate)때 **Locks** 속성의 **excludedPrincipals** 배열에 최대 5 개의 값을 추가 하 여 REST API에서 수행 됩니다. 다음 할당 정의는 **excludedPrincipals**을 포함 하는 요청 본문의 예입니다.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>거부 할당에서 작업 제외

청사진 할당에서 [거부 할당](../../../role-based-access-control/deny-assignments.md) 에 대 한 [보안 주체를 제외](#exclude-a-principal-from-a-deny-assignment) 하는 것과 마찬가지로 특정 [RBAC 작업](../../../role-based-access-control/resource-provider-operations.md)을 제외할 수 있습니다. **ExcludedPrincipals** 가 있는 것과 동일한 위치에 있는 **속성. locks** 블록 내에서 **excludedActions** 를 추가할 수 있습니다.

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

**ExcludedPrincipals** 는 명시적 이어야 하지만 **EXCLUDEDACTIONS** 항목은 `*` RBAC 작업과 와일드 카드 일치를 위해를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [새 리소스 보호](../tutorials/protect-new-resources.md) 자습서를 따르세요.
- [청사진 수명 주기](./lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](./parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](./sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결