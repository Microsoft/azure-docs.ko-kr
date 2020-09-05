---
title: 정책 할당 구조의 세부 정보
description: Azure Policy에서 평가를 위해 리소스에 정책 정의 및 매개 변수를 연결 하는 데 사용 하는 정책 할당 정의에 대해 설명 합니다.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 969274d72724c8d0a8f10f86f614fe2c50d066f7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520716"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy 할당 구조

정책 할당은 Azure Policy에서 사용 하 여 정책이 나 이니셔티브에 할당 되는 리소스를 정의 합니다. 정책 할당은 할당 시 해당 리소스 그룹에 대 한 매개 변수 값을 결정할 수 있으므로 준수에 대 한 다른 요구 사항과 동일한 리소스 속성을 처리 하는 정책 정의를 다시 사용할 수 있습니다.

JSON을 사용 하 여 정책 할당을 만듭니다. 정책 할당에는 다음에 대 한 요소가 포함 됩니다.

- 표시 이름
- description
- metadata
- 적용 모드
- 제외 된 범위
- 정책 정의
- 매개 변수

예를 들어 다음 JSON은 동적 매개 변수를 사용 하는 _DoNotEnforce_ 모드의 정책 할당을 보여 줍니다.

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

모든 Azure Policy 샘플은 [Azure Policy 샘플](../samples/index.md)에 있습니다.

## <a name="display-name-and-description"></a>표시 이름 및 설명

**DisplayName** 및 **description** 을 사용 하 여 정책 할당을 식별 하 고 특정 리소스 집합과 함께 사용 하기 위한 컨텍스트를 제공 합니다. **displayName**은 최대 길이가 _128_자이고 **description**은 최대 길이가 _512_자입니다.

## <a name="enforcement-mode"></a>적용 모드

**EnforcementMode** 속성은 [Azure 활동 로그](../../../azure-monitor/platform/platform-logs-overview.md)에서 정책 효과를 시작 하거나 항목을 트리거하지 않고 기존 리소스에 대 한 정책 결과를 테스트 하는 기능을 고객에 게 제공 합니다. 이 시나리오를 일반적으로 "What If" 이라고 하며 안전 배포 방법에 맞춥니다. **enforcementMode** 는 [사용 하지 않도록 설정](./effects.md#disabled) 된 효과와 다릅니다. 이러한 효과는 리소스 평가가 전혀 발생 하지 않도록 방지 합니다.

이 속성의 값은 다음과 같습니다.

|Mode |JSON 값 |형식 |수동으로 재구성 |활동 로그 항목 |설명 |
|-|-|-|-|-|-|
|사용 |기본값 |문자열 |예 |예 |정책 효과는 리소스를 만들거나 업데이트 하는 동안 적용 됩니다. |
|사용 안 함 |DoNotEnforce |문자열 |예 |예 | 정책 효과는 리소스를 만들거나 업데이트 하는 동안 적용 되지 않습니다. |

**EnforcementMode** 가 정책 또는 이니셔티브 정의에 지정 되지 않은 경우에는 _기본값_ 을 사용 합니다. **EnforcementMode** 가 _DoNotEnforce_로 설정 된 경우에도 [deployifnotexists](./effects.md#deployifnotexists) 정책에 대해 [재구성 작업](../how-to/remediate-resources.md) 을 시작할 수 있습니다.

## <a name="excluded-scopes"></a>제외 된 범위

할당의 **범위** 는 모든 자식 리소스 컨테이너 및 자식 리소스를 포함 합니다. 자식 리소스 컨테이너 또는 자식 리소스에 정의가 적용 되지 않아야 하는 경우에는 **Notscopes**을 설정 하 여 계산에서 제외할 수 있습니다. 이 속성은 하나 이상의 리소스 컨테이너 또는 리소스를 평가에서 제외할 수 있도록 하는 배열입니다. **Notscopes** 은 초기 할당을 만든 후 추가 하거나 업데이트할 수 있습니다.

## <a name="policy-definition-id"></a>정책 정의 ID

이 필드는 정책 정의 또는 이니셔티브 정의의 전체 경로 이름 이어야 합니다.
`policyDefinitionId` 는 배열이 아니라 문자열입니다. 여러 정책을 함께 할당 하는 것이 아니라 [이니셔티브](./initiative-definition-structure.md) 를 사용 하는 것이 좋습니다.

## <a name="parameters"></a>매개 변수

정책 할당의이 세그먼트는 [정책 정의 또는 이니셔티브 정의](./definition-structure.md#parameters)에 정의 된 매개 변수에 대 한 값을 제공 합니다. 이 설계를 통해 여러 리소스를 사용 하 여 정책 또는 이니셔티브 정의를 다시 사용할 수 있지만 다른 비즈니스 값 또는 결과를 확인할 수 있습니다.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

이 예제에서 정책 정의에 이전에 정의 된 매개 변수는 `prefix` 및 `suffix` 입니다. 이 특정 정책 할당 `prefix` **DeptA** `suffix` **은 비-LC**로 설정 됩니다. 동일한 정책 정의는 다른 부서에 대 한 다른 매개 변수 집합을 사용 하 여 다시 사용할 수 있으므로 유연성을 제공 하면서 정책 정의의 중복 및 복잡성을 줄일 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.