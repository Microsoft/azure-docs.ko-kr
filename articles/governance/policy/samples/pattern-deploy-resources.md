---
title: 패턴 정책 정의를 사용하여 리소스 배포
description: 이 Azure Policy 패턴은 정책 정의에서 리소스를 배포하는 방법에 대한 예제를 제공합니다.
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 705c751183a0b71c8628a505f08ef112bca1cbb7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545610"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure Policy 패턴: 리소스 배포

[deployIfNotExists](../concepts/effects.md#deployifnotexists) 효과를 사용하면 호환되지 않는 리소스를 만들거나 업데이트할 때 [Azure Resource Manager 템플릿](../../../azure-resource-manager/templates/overview.md)(ARM 템플릿)을 배포할 수 있습니다. 이 방식은 리소스가 계속 생성될 수 있도록 허용하면서도 규정 준수를 위한 변경을 보장하므로 [deny](../concepts/effects.md#deny) 효과를 사용하는 것보다 나을 수 있습니다.

## <a name="sample-policy-definition"></a>샘플 정책 정의

이 정책 정의는 **field** 연산자를 사용하여 생성 또는 업데이트된 리소스의 `type`을 평가합니다. 해당 리소스가 _Microsoft.Network/virtualNetworks_인 경우 정책은 새 리소스 또는 업데이트된 리소스의 위치에서 네트워크 감시자를 찾습니다. 일치하는 네트워크 감시자를 찾지 못하면 ARM 템플릿이 배포되어 누락된 리소스를 만듭니다.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>설명

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

**properties.policyRule.then.details** 블록은 **properties.policyRule.if** 블록의 생성 또는 업데이트된 리소스와 관련된 찾을 내용을 Azure Policy에 알려 줍니다. 이 예제에서는 새 리소스 또는 업데이트된 리소스의 위치와 동일하게 **field** `location`인 리소스 그룹 **networkWatcherRG**의 네트워크 감시자가 있어야 합니다. `field()` 함수를 사용하면 **existenceCondition**에서 새 리소스 또는 업데이트된 리소스의 속성(특히 `location` 속성)에 액세스할 수 있습니다.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

**properties.policyRule.then.details** 블록의 **roleDefinitionIds** _array_ 속성은 포함된 Resource Manager 템플릿을 배포하기 위해 관리 ID에 필요한 권한을 정책 정의에 알려 줍니다. 이 속성은 템플릿 배포에 필요한 사용 권한이 있는 역할을 포함하도록 설정되어야 하지만, ‘최소 권한 원칙’ 개념을 사용해야 하며 필요한 작업만 있어야 합니다.

#### <a name="deployment-template"></a>배포 템플릿

정책 정의의 **deployment** 부분에는 세 가지 핵심 구성 요소를 정의하는 **properties** 블록이 있습니다.

- **mode** - 이 속성은 템플릿의 [배포 모드](../../../azure-resource-manager/templates/deployment-modes.md)를 설정합니다.

- **template** - 이 속성은 템플릿 자체를 포함합니다. 이 예제에서 **location** 템플릿 매개 변수는 새 네트워크 감시자 리소스의 위치를 설정합니다.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parameters** - 이 속성은 **template**에 제공되는 매개 변수를 정의합니다. 매개 변수 이름은 **template**에 정의된 이름과 일치해야 합니다. 이 예제에서는 매개 변수의 이름이 **location**으로 일치합니다. **location**의 값은 `field()` 함수를 다시 사용하여 평가된 리소스의 값을 가져옵니다. 이 값은 **policyRule.if** 블록의 가상 네트워크입니다.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.