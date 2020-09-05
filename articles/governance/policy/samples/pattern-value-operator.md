---
title: 패턴 정책 정의의 값 연산자
description: 이 Azure Policy 패턴은 정책 정의에서 값 연산자를 사용하는 방법에 대한 예를 제공합니다.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: e246e3a5e2517fa80626081227070bcb2f967784
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85565667"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy 패턴: 값 연산자

[값](../concepts/definition-structure.md#value) 연산자는 [매개 변수](../concepts/definition-structure.md#parameters), [지원되는 템플릿 함수](../concepts/definition-structure.md#policy-functions) 또는 리터럴을 지정된 [조건](../concepts/definition-structure.md#conditions)에 대해 제공된 값으로 평가합니다.

> [!WARNING]
> _템플릿 함수_의 결과가 오류이면 정책 평가가 실패합니다. 실패한 평가는 암시적 **거부**입니다. 자세한 내용은 [템플릿 오류 방지](../concepts/definition-structure.md#avoiding-template-failures)를 참조하세요.

## <a name="sample-policy-definition"></a>샘플 정책 정의

이 정책 정의는 리소스의 매개 변수 **tagName**(_string_)에 지정된 태그를 추가하거나 바꾸고 리소스가 있는 리소스 그룹에서 **tagName**의 값을 상속합니다. 이 평가는 리소스를 만들거나 업데이트할 때 발생합니다. [수정](../concepts/effects.md#modify) 효과로서, [수정 작업](../how-to/remediate-resources.md)을 통해 기존 리소스에서 수정을 실행할 수 있습니다.

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>설명

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

**값** 연산자는 **속성** 내의 **policyRule.if** 블록 내에서 사용됩니다. 이 예제에서 [논리 연산자](../concepts/definition-structure.md#logical-operators) **allOf**는 **수정** 효과가 발생하려면 두 조건문이 모두 true이어야 함을 명시하는 데 사용됩니다.

**값**은 템플릿 함수 [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) 결과를 빈 값의 **notEquals** 조건으로 평가합니다. 부모 리소스 그룹의 **tagName**에 제공된 태그 이름이 있으면 조건부가 true로 평가됩니다.

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.