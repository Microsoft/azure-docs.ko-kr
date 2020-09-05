---
title: 리소스의 배열 속성에 대한 작성자 정책
description: 배열 매개 변수 및 배열 언어 식을 사용하고, [*] 별칭을 평가하고, Azure Policy 정의 규칙을 사용하여 요소를 추가하는 방법을 알아봅니다.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 5b9392a943e264ae5eca989ee87eb9ff09b36972
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048485"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure 리소스의 배열 속성에 대한 작성자 정책

Azure Resource Manager 속성은 일반적으로 문자열 및 부울로 정의됩니다. 일 대 다 관계가 있는 경우 복합 속성이 배열로 대신 정의됩니다. Azure Policy에서는 배열이 여러 가지 방법으로 사용됩니다.

- 여러 옵션을 제공하기 위한 [정의 매개 변수](../concepts/definition-structure.md#parameters) 유형
- **in** 또는 **notIn** 조건을 사용하는 [정책 규칙](../concepts/definition-structure.md#policy-rule)의 일부
- [\[\*\] 별칭](../concepts/definition-structure.md#understanding-the--alias)을 평가하는 정책 규칙의 일부:
  - **없음**, **임의** 또는 **모두** 같은 시나리오
  - **count**를 사용하는 복잡한 시나리오
- 기존 배열을 바꾸거나 기존 배열에 추가하는 [추가 효과](../concepts/effects.md#append)

이 문서에서는 Azure Policy의 각 사용법을 설명하고 몇 가지 예제 정의를 제공합니다.

## <a name="parameter-arrays"></a>매개 변수 배열

### <a name="define-a-parameter-array"></a>매개 변수 배열 정의

매개 변수를 배열로 정의하면 둘 이상의 값이 필요한 경우 유연한 정책이 가능합니다.
이 정책 정의를 사용하면 매개 변수 **allowedLocations**에 단일 위치를 사용할 수 있으며 기본값은 _eastus2_입니다.

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

**type**이 _string_이었으므로 정책을 할당할 때 하나의 값만 설정할 수 있습니다. 이 정책이 할당되면 범위의 리소스가 단일 Azure 지역 내에서만 허용됩니다. 대부분의 정책 정의는 _eastus2_, _eastus_ 및 _westus2_ 허용 같은 승인된 옵션 목록을 허용해야 합니다.

여러 옵션을 허용하는 정책 정의를 만들려면 _array_ **type**을 사용합니다. 동일한 정책을 다음과 같이 다시 작성할 수 있습니다.

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> 정책 정의를 저장한 후에는 매개 변수의 **type** 속성을 변경할 수 없습니다.

이 새 매개 변수 정의는 정책 할당 중에 둘 이상의 값을 사용합니다. **allowedValues**라는 배열 속성을 정의하면 할당 중에 사용 가능한 값이 미리 정의된 옵션 목록으로 추가 제한됩니다. **allowedValues** 사용은 선택 사항입니다.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>할당 중에 매개 변수 배열에 값 전달

Azure Portal을 통해 정책을 할당하는 경우 **type** _array_의 매개 변수가 단일 텍스트 상자로 표시됩니다. 힌트에는 "값을 구분하려면 ;을 사용하세요. (예: 런던;뉴욕)"라고 표시됩니다. _eastus2_, _eastus_ 및 _westus2_의 허용되는 위치 값을 매개 변수에 전달하려면 다음 문자열을 사용합니다.

`eastus2;eastus;westus2`

Azure CLI, Azure PowerShell 또는 REST API를 사용하는 경우 매개 변수 값의 형식이 다릅니다. 값은 매개 변수의 이름도 포함하는 JSON 문자열을 통해 전달됩니다.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

각 SDK에서 이 문자열을 사용하려면 다음 명령을 사용합니다.

- Azure CLI: **params** 매개 변수가 지정된 [az policy assignment create](/cli/azure/policy/assignment#az-policy-assignment-create) 명령
- Azure PowerShell: **PolicyParameter** 매개 변수가 지정된 [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) cmdlet
- REST API: 요청 본문에 **properties.parameters** 속성 값으로 포함된 _PUT_ [create](/rest/api/resources/policyassignments/create) 작업

## <a name="policy-rules-and-arrays"></a>정책 규칙 및 배열

### <a name="array-conditions"></a>배열 조건

매개 변수의 _array_
**type**을 사용할 수 있는 정책 규칙 [조건](../concepts/definition-structure.md#conditions)은 `in` 및 `notIn`으로 제한됩니다. `equals` 조건을 사용하는 다음 정책 정의를 예로 들어 보겠습니다.

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Azure Portal을 통해 이 정책 정의를 만들려고 하면 다음 오류 메시지와 같은 오류가 발생합니다.

- "유효성 검사 오류로 인해 '{GUID}' 정책을 매개 변수화할 수 없습니다. 정책 매개 변수가 제대로 정의되어 있는지 확인하세요. '언어 식 '[parameters('allowedLocations')]'의 평가 결과가 'Array' 유형입니다. 올바른 유형은 'String'입니다.'라는 내부 예외가 발생했습니다."

`equals` 조건의 올바른 **type**은 _string_입니다. **allowedLocations**가 **type** _array_로 정의되었으므로 정책 엔진이 언어 식을 평가하고 오류를 throw합니다. `in` 및 `notIn` 조건을 사용하면 정책 엔진이 언어 식에서 **type** _array_를 예상합니다. 이 오류 메시지를 해결하려면 `equals`를 `in` 또는 `notIn`으로 변경하세요.

### <a name="evaluating-the--alias"></a>[*] 별칭 평가

이름에 **\[\*\]** 가 연결된 별칭은 **type**이 _array_임을 의미합니다. 전체 배열의 값을 계산하는 대신 **\[\*\]** 를 사용하면 배열의 각 요소 사이에 논리적 AND를 사용하여 해당 요소를 개별적으로 평가할 수 있습니다. 이러한 항목별 평가가 유용한 세 가지 표준 시나리오는 일치하는 요소가 _없음_, _임의_ 또는 _모두_인 경우입니다. 복잡한 시나리오의 경우 [count](../concepts/definition-structure.md#count)를 사용합니다.

정책 엔진은 **이때** 정책이 true인 **경우**에만 **효과**를 트리거합니다.
이 사실은 **\[\*\]** 가 배열의 각 개별 요소를 평가하는 방법을 이해하는 데 중요합니다.

아래 시나리오 테이블의 정책 규칙 예:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

아래 시나리오 테이블의 **ipRules** 배열은 다음과 같습니다.

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

아래의 각 조건 예에서 `<field>`는 `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`로 바꿉니다.

다음 결과는 이 조건 및 예제 정책 규칙과 상기 기존 값 배열의 조합에 대한 결과입니다.

|조건 |결과 | 시나리오 |설명 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |없음 |일치 항목 없음 |한 배열 요소가 false(127.0.0.1 != 127.0.0.1)로 평가되고, 한 배열 요소는 true(127.0.0.1 != 192.168.1.1)로 평가되므로 **notEquals** 조건이 _false_이고 효과는 트리거되지 않습니다. |
|`{<field>,"notEquals":"10.0.4.1"}` |정책 효과 |일치 항목 없음 |두 배열 요소가 true(10.0.4.1 != 127.0.0.1 및 10.0.4.1 != 192.168.1.1)로 평가되므로 **notEquals** 조건이 _true_이고 효과가 트리거됩니다. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |정책 효과 |하나 이상 일치 |한 배열 요소가 false(127.0.0.1 != 127.0.0.1)로 평가되고, 한 배열 요소는 true(127.0.0.1 != 192.168.1.1)로 평가되므로 **notEquals** 조건이 _false_입니다. 논리 연산자가 true(_false_ **아님**)로 평가되므로 효과가 트리거됩니다. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |없음 |하나 이상 일치 |두 배열 요소가 true(10.0.4.1 != 127.0.0.1 및 10.0.4.1 != 192.168.1.1)로 평가되므로 **notEquals** 조건이 _true_입니다. 논리 연산자가 false(_true_ **아님**)로 평가되므로 효과가 트리거되지 않습니다. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |정책 효과 |일부만 일치 |한 배열 요소가 true(127.0.0.1 == 127.0.0.1)로 평가되고, 한 배열 요소는 false(127.0.0.1 == 192.168.1.1)로 평가되므로 **Equals** 조건이 _false_입니다. 논리 연산자가 true(_false_ **아님**)로 평가되므로 효과가 트리거됩니다. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |정책 효과 |일부만 일치 |두 배열 요소가 false(10.0.4.1 == 127.0.0.1 및 10.0.4.1 == 192.168.1.1)로 평가되므로 **Equals** 조건이 _false_입니다. 논리 연산자가 true(_false_ **아님**)로 평가되므로 효과가 트리거됩니다. |
|`{<field>,"Equals":"127.0.0.1"}` |없음 |모두 일치 |한 배열 요소가 true(127.0.0.1 == 127.0.0.1)로 평가되고, 한 배열 요소는 false(127.0.0.1 == 192.168.1.1)로 평가되므로 **Equals** 조건이 _false_이고 효과는 트리거되지 않습니다. |
|`{<field>,"Equals":"10.0.4.1"}` |없음 |모두 일치 |두 배열 요소가 false(10.0.4.1 == 127.0.0.1 및 10.0.4.1 == 192.168.1.1)로 평가되므로 **Equals** 조건이 _false_이고 효과는 트리거되지 않습니다. |

## <a name="the-append-effect-and-arrays"></a>추가 효과 및 배열

[추가 효과](../concepts/effects.md#append)는 **details.field**가 **\[\*\]** 별칭인지 여부에 따라 다르게 동작합니다.

- **\[\*\]** 별칭이 아닐 경우 추가 효과는 전체 배열을 **값** 속성으로 바꿉니다.
- **\[\*\]** 별칭일 경우 추가 효과는 **값** 속성을 기존 배열에 추가하거나 새 배열을 만듭니다.

자세한 내용은 [추가 예제](../concepts/effects.md#append-examples)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](programmatically-create.md)하는 방법을 이해합니다.
- [규정 비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
