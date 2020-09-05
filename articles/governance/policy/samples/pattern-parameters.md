---
title: 패턴 정책 정의의 매개 변수
description: 이 Azure Policy 패턴은 정책 정의에서 매개 변수를 사용하는 방법에 대한 예제를 제공합니다.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 6d938cdf2f31d30932d3631e99254b7d833f2941
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545576"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy 패턴: 매개 변수

[매개 변수](../concepts/definition-structure.md#parameters)를 사용하여 필요한 정책 정의의 수를 줄이기 위해 정책 정의를 동적으로 만들 수 있습니다. 매개 변수는 정책 할당 중에 정의됩니다. 매개 변수에는 매개 변수 및 사용 방법을 설명하는 미리 정의된 속성 세트가 있습니다.

## <a name="sample-1-string-parameters"></a>샘플 1: String 매개 변수

이 정책 정의는 두 개의 매개 변수, 즉 **tagName** 및 **tagValue**를 사용하여 정책 할당에서 리소스에 대해 찾고 있는 항목을 설정합니다. 이 형식을 사용하면 원하는 수의 태그 이름 및 태그 값 조합에 정책 정의를 사용할 수 있지만 단일 정책 정의만 유지 관리합니다.

> [!NOTE]
> _모든_ **모드**를 사용하고 리소스 그룹과 작동하는 태그 샘플은 [패턴: 태그 - 샘플 #1](./pattern-tags.md#sample-1-parameterize-tags)을 참조하세요.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>샘플 1: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

이 정책 정의의 부분에서 **tagName** 매개 변수는 _string_으로 정의되며 사용할 수 있도록 설명이 제공됩니다.

그런 다음, **policyRule.if** 블록에서 매개 변수를 사용하여 정책을 동적으로 만듭니다. 여기에서 계산되는 필드를 정의하는 데 사용됩니다. 이는 **tagName**값이 포함된 태그입니다.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>샘플 2: Array 매개 변수

이 정책 정의는 단일 매개 변수인 **listOfBandwidthinMbps**를 사용하여 Express 경로 회로 리소스가 승인된 값 중 하나로 대역폭 설정을 구성했는지 확인합니다. 일치하지 않는 경우 리소스에 대한 만들기 또는 업데이트가 [거부됩니다](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>샘플 2: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

이 정책 정의의 부분에서 **listOfBandwidthinMbps** 매개 변수는 _array_로 정의되며 사용할 수 있도록 설명이 제공됩니다. _array_로 일치하는 값이 여러 개 있습니다.

그런 다음, **policyRule.if** 블록에서 매개 변수를 사용합니다. _array_ 매개 변수로 _array_
[condition](../concepts/definition-structure.md#conditions)의 **in** 또는 **notIn**이 사용되어야 합니다.
여기에서 정의된 값 중 하나로 **serviceProvider.bandwidthInMbps** 별칭에 대해 사용됩니다.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>샘플 3: 매개 변수화된 효과

정책 정의를 다시 사용할 수 있도록 하는 일반적인 방법은 효과 자체를 매개 변수화하는 것입니다. 이 예제에서는 단일 매개 변수 **효과**를 사용합니다. 효과를 매개 변수화하면 다른 효과를 가진 범위에 동일한 정의를 할당할 수 있습니다.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>샘플 3: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

정책 정의의 이 부분에서 **효과** 매개 변수는 _문자열_로 정의됩니다. 정책 정의는 할당의 기본값을 _감사_로 설정하고 다른 옵션을 _사용 안함_ 및 _거부_로 제한합니다.

그런 다음, _효과_에 대한 **policyRule.then** 블록에서 매개 변수를 사용합니다.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.