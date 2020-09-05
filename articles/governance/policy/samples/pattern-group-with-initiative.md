---
title: 패턴 이니셔티브가 포함된 그룹 정책 정의
description: 이 Azure Policy 패턴에서는 정책 정의를 이니셔티브로 그룹화하는 방법에 대한 예를 제공합니다.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 065e7632d76feccdd6d2d5a411ba7cdf50aeaf68
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85565707"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Policy 패턴: 그룹 정책 정의

이니셔티브는 정책 정의의 그룹입니다. 관련 정책 정의를 단일 개체로 그룹화하면 예전처럼 할당을 여러 개 생성하지 않아도 됩니다.

## <a name="sample-initiative-definition"></a>샘플 이니셔티브 정의

이 이니셔티브는 각각 **tagName** 및 **tagValue** 매개 변수를 사용하는 두 개의 정책 정의를 배포합니다. 이니셔티브 자체에는 **costCenterValue** 및 **productNameValue**의 두 매개 변수가 있습니다.
이러한 이니셔티브 매개 변수는 각각 그룹화된 정책 정의에 제공됩니다. 이 디자인은 기존 정책 정의의 재사용을 최대화하는 동시에 필요에 따라 이를 구현하기 위해 생성된 할당 수를 제한합니다.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>설명

#### <a name="initiative-parameters"></a>이니셔티브 매개 변수

이니셔티브는 고유한 매개 변수를 정의한 다음, 그룹화된 정책 정의로 전달할 수 있습니다.
이 예제에서 **costCenterValue** 및 **productNameValue**는 모두 이니셔티브 매개 변수로 정의됩니다. 이니셔티브가 할당될 때 값이 제공됩니다.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>정책 정의 포함

정책 정의에서 매개 변수를 허용하는 경우 포함된 각 정책 정의는 **policyDefinitionId** 및 **매개 변수** 배열을 제공해야 합니다. 아래 코드 조각에서 포함된 정책 정의는 **tagName** 및 **tagValue**의 두 매개 변수를 사용합니다. **tagName**은 리터럴로 정의되지만 **tagValue**는 이니셔티브에서 정의한 매개 변수 **costCenterValue**를 사용합니다. 이 값의 통과는 재사용을 향상시킵니다.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.