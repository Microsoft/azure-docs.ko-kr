---
title: 패턴 정책 정의의 count 연산자
description: 이 Azure Policy 패턴은 정책 정의에서 count 연산자를 사용하는 방법에 대한 예를 제공합니다.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 807890b7fb08d790deff6e0be9e08ad91c4ec44d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85565758"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy 패턴: count 연산자

[count](../concepts/definition-structure.md#count) 연산자는 \[\*\] 별칭의 멤버를 평가합니다.

## <a name="sample-policy-definition"></a>샘플 정책 정의

이 정책 정의는 인바운드 RDP(원격 데스크톱 프로토콜) 트래픽을 허용하도록 구성된 네트워크 보안 그룹을 [감사](../concepts/effects.md#audit)합니다.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>설명

**count** 연산자의 핵심 구성 요소는 _필드_, _where_ 및 조건입니다. 각각은 아래 코드 조각에서 강조 표시됩니다.

- _필드_는 멤버를 평가하는 [별칭](../concepts/definition-structure.md#aliases) 수를 알려줍니다. 여기서는 네트워크 보안 그룹의 **securityRules\[\*\]** 별칭 _배열_을 살펴봅니다.
- _여기서_ 정책 언어를 사용하여 해당 조건을 충족하는 _배열_ 멤버를 정의합니다. 이 예제에서 **allOf** 논리 연산자는 별칭 _배열_ 속성의 세 가지 조건 평가(_direction_, _access_ 및 _destinationPortRange_)를 그룹화합니다.
- 이 예제의 개수 조건은 **greater**입니다. 개수는 별칭 _배열_의 하나 이상의 멤버가 _where_ 절과 일치하면 true로 평가됩니다.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.