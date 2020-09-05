---
title: 패턴 정책 정의에 태그 사용
description: 이 Azure Policy 패턴은 매개 변수화된 태그를 추가하거나 정책 정의의 리소스 그룹에서 태그를 상속하는 방법에 대한 예시를 제공합니다.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3016fc7889f68fd13e993c67ca645a4af055c651
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545559"
---
# <a name="azure-policy-pattern-tags"></a>Azure Policy 패턴: 태그

[태그](../../..//azure-resource-manager/management/tag-resources.md)는 Azure 리소스를 관리, 구성 및 관리하는 데 중요한 부분입니다. Azure Policy를 사용하면 [수정](../concepts/effects.md#modify) 효과와 [조정 작업](../how-to/remediate-resources.md)을 통해 새 리소스와 기존 리소스에 대한 태그를 대규모로 구성할 수 있습니다.

## <a name="sample-1-parameterize-tags"></a>샘플 1: 매개 변수화된 태그

이 정책 정의는 두 개의 매개 변수, 즉, **tagName** 및 **tagValue**를 사용하여 정책 할당이 리소스 그룹에서 찾고 있는 항목을 설정합니다. 이 형식을 사용하면 원하는 수의 태그 이름 및 태그 값 조합에 정책 정의를 사용할 수 있지만 단일 정책 정의만 유지 관리합니다.

> [!NOTE]
> 이 정책 정의 패턴은 [패턴: 매개 변수 - 샘플 #1](./pattern-parameters.md#sample-1-string-parameters)의 패턴과 비슷합니다. 이 샘플은 **mode** 및 _All_을 사용하며 리소스 그룹을 대상으로 합니다.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>샘플 1: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

이 샘플에서는 **mode**가 리소스 그룹을 대상으로 하기 때문에 _All_로 설정됩니다. 대부분의 경우 태그를 사용할 때 **mode**를 _Indexed_로 설정해야 합니다. 자세한 내용은 [mode](../concepts/definition-structure.md#resource-manager-modes)를 참조하세요.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

정책 정의의 이 부분에서 `concat`은 매개 변수화된 **tagName** 매개 변수와 `tags['name']` 형식을 결합하여 **필드**에 **tagValue** 매개 변수의 해당 태그를 평가하도록 지시 합니다.
**notEquals**과 사용되었으므로 **tags\[tagName\]** 이 **tagValue**와 일치하지 않으면 **수정** 효과가 트리거됩니다.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

여기서는 매개 변수화된 태그 값을 사용할 때와 동일한 형식을 **addOrReplace** 작업에 사용하여 평가 대상 리소스 그룹에 대해 원하는 값으로 태그를 만들거나 업데이트합니다.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>샘플 2: 리소스 그룹의 태그 값 상속

이 정책 정의는 **tagName** 매개 변수를 사용하여 부모 리소스 그룹에서 상속할 태그 값을 결정합니다.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>샘플 2: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

이 샘플에서 **mode**는 리소스 그룹에서 값을 가져오지만 리소스 그룹 또는 구독을 대상으로 하지 않으므로 _Indexed_로 설정됩니다. 자세한 내용은 [mode](../concepts/definition-structure.md#resource-manager-modes)를 참조하세요.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**policyRule.if**는 [샘플 #1](#sample-1-parameterize-tags) 같은 `concat`을 사용하여 **tagName** 값을 평가하지만 `resourceGroup()` 함수를 사용하여 부모 리소스 그룹에 있는 동일한 태그의 값과 비교합니다. 두 번째 절은 리소스 그룹의 태그가 값을 포함하고 null이 아닌지 확인합니다.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

여기에서 리소스의 **tagName** 태그에 할당되는 값도 `resourceGroup()` 함수를 사용하여 부모 리소스 그룹에서 값을 가져옵니다. 이러한 방식으로 부모 리소스 그룹에서 태그를 상속할 수 있습니다. 이미 리소스를 만들었지만 태그를 추가하지 않은 경우 이 정책 정의와 [수정 작업](../how-to/remediate-resources.md)으로 기존 리소스를 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.