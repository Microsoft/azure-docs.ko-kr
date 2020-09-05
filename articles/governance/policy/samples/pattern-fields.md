---
title: 패턴 정책 정의의 필드 속성
description: 이 Azure Policy 패턴은 정책 정의에서 필드 속성을 사용하는 방법에 대한 예를 제공합니다.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 87c550e5fb3ef0efbf219efc738a69071d2e89ba
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85565711"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy 패턴: 필드 속성

[field](../concepts/definition-structure.md#fields) 연산자는 지정된 속성 또는 [별칭](../concepts/definition-structure.md#aliases)을 지정된 [조건](../concepts/definition-structure.md#conditions)에 대해 제공된 값으로 평가합니다.

## <a name="sample-policy-definition"></a>샘플 정책 정의

이 정책 정의를 사용하면 조직의 지리적 위치 요구 사항을 충족하는 허용된 지역을 정의할 수 있습니다. 허용되는 리소스는 매개 변수 **listOfAllowedLocations**(_배열_)에 정의되어 있습니다. 정의와 일치하는 리소스는 [거부](../concepts/effects.md#deny)됩니다.

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>설명

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

**field** 연산자는 [논리 연산자](../concepts/definition-structure.md#logical-operators) **allOf** 내에서 세 번 사용됩니다.

- 첫 번째 용도는 **notIn** 조건이 있는 `location` 속성을 **listOfAllowedLocations** 매개 변수로 평가합니다. **notIn**은 _배열_이 필요한 만큼 작동하며 매개 변수는 _배열_입니다. 생성되거나 업데이트된 리소스의 `location`이 승인된 목록에 없는 경우 이 요소는 true로 평가됩니다.
- 두 번째 용도는 `location` 속성도 평가하지만 **notEquals** 조건을 사용하여 리소스가 _글로벌_인지 확인합니다. 생성되거나 업데이트된 리소스의 `location`이 _글로벌_이 아닌 경우 이 요소는 true로 평가됩니다.
- 마지막 용도는 `type` 속성을 평가하고 **notEquals** 조건을 사용하여 리소스 형식이 _Microsoft.AzureActiveDirectory/b2cDirectories_가 아닌지 확인합니다. 그렇지 않은 경우 이 요소는 true로 평가됩니다.

**allOf** 논리 연산자의 세 조건문이 모두 true로 평가되면 리소스 생성 또는 업데이트는 Azure Policy에 의해 차단됩니다.

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.