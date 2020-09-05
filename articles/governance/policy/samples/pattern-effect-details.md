---
title: 패턴 정책 정의의 효과
description: 이 Azure Policy 패턴은 정책 정의의 다른 효과를 사용하는 방법에 대한 예제를 제공합니다.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 9c7e7689aa837a2dba0992112df3cd36ac9affe6
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85565738"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy 패턴: 효과

Azure Policy에는 서비스에서 비준수 리소스에 반응하는 방법을 결정하는 다양한 [효과](../concepts/effects.md)가 있습니다. 일부 효과는 간단하며 정책 정의에 추가 속성이 필요하지 않지만 다른 효과에는 여러 가지 속성이 필요합니다.

## <a name="sample-1-simple-effect"></a>샘플 1: 간단한 효과

이 정책 정의는 매개 변수 **tagName**에 정의된 태그가 평가된 리소스에 있는지 확인합니다. 태그가 아직 없는 경우 [modify](../concepts/effects.md#modify) 효과가 트리거되고 매개 변수 **tagValue**에 값이 포함된 태그를 추가합니다.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>샘플 1: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

**modify** 효과를 적용하려면 **roleDefinitionIds** 및 **operations**를 정의하는 **policyRule.then.details** 블록이 필요합니다. 이러한 매개 변수는 태그를 추가하고 리소스를 수정하는 데 필요한 역할 및 수행할 **modify** 작업을 Azure Policy에 알려 줍니다. 이 예제에서 **operation**이 _추가_되며 태그와 해당 값을 설정하는 데 매개 변수가 사용됩니다.

## <a name="sample-2-complex-effect"></a>샘플 2: 복합 효과

이 정책 정의는 매개 변수 **publisher** 및 **type**에 정의된 확장이 없을 때 각 가상 머신을 감사합니다. [auditIfNotExists](../concepts/effects.md#auditifnotexists)를 사용하여 가상 머신과 관련된 리소스를 확인하여 정의된 매개 변수와 일치하는 인스턴스가 있는지 확인합니다. 이 예제에서는 **extensions** 형식을 확인합니다.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>샘플 2: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

**auditIfNotExists** 효과를 적용하려면 **policyRule.then.details** 블록에서 찾을 **type** 및 **existenceCondition** 모두를 정의해야 합니다. **existenceCondition**은 [논리 연산자](../concepts/definition-structure.md#logical-operators)와 같은 정책 언어 요소를 사용하여 일치하는 관련 리소스가 있는지 확인합니다. 이 예제서는 각 [별칭](../concepts/definition-structure.md#aliases)에 대해 확인된 값이 매개 변수에 정의됩니다.

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.