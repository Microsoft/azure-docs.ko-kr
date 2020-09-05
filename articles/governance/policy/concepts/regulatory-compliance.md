---
title: 이니셔티브 정의의 규정 준수
description: 이니셔티브 정의를 사용 하 여 Access Control, 구성 관리 등의 규정 도메인별 정책을 그룹화 하는 방법을 설명 합니다.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 296febc1aad62d06c8e9dfcf4f285b16cacf0a40
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544624"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Azure Policy의 규정 준수

Azure Policy의 규정 준수는 기본 제공 이니셔티브 정의를 제공 하 여 책임 (_고객_, _Microsoft_, _공유_)에 따라 **제어** 및 **규정 준수 도메인** 목록을 볼 수 있습니다.
Microsoft에서 담당 하는 제어를 위해 타사 증명을 기반으로 하는 감사 결과와 해당 규정 준수를 실현 하는 구현 세부 정보를 추가로 제공 합니다.
Microsoft에서 담당 하는 컨트롤은 `type` [정적](./definition-structure.md#type)입니다.

> [!NOTE]
> 규정 준수는 미리 보기 기능입니다. 업데이트 된 기본 기능의 경우 이니셔티브 컨트롤은 해당 하는 준수 표준에 매핑됩니다. 기존 준수 표준 이니셔티브는 규정 준수를 지원 하도록 업데이트 되 고 있습니다.

## <a name="regulatory-compliance-defined"></a>규정 준수 정의 됨

규정 준수는 이니셔티브 정의의 [그룹화](./initiative-definition-structure.md#policy-definition-groups) 부분을 기반으로 합니다. 기본 제공에서 이니셔티브 정의의 각 그룹화는 이름 (**컨트롤**), 범주 (**준수 도메인**)를 정의 하 고 해당 **컨트롤**에 대 한 정보를 포함 하는 [policymetadata](./initiative-definition-structure.md#metadata-objects) 개체에 대 한 참조를 제공 합니다. 규정 준수 이니셔티브 정의에는 `category` **규정 준수**로 설정 된 속성이 있어야 합니다. 달리 표준 이니셔티브 정의 인 규정 준수 이니셔티브는 동적 할당을 만들기 위한 [매개 변수](./initiative-definition-structure.md#parameters) 를 지원 합니다.

고객은 자신의 규정 준수 이니셔티브를 만들 수 있습니다. 이러한 정의는 기존 기본 제공 정의에서 원본 또는 복사 될 수 있습니다. 기본 제공 규정 준수 이니셔티브 정의를 참조로 사용 하는 경우 [Azure Policy GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)리포지토리에서 규정 준수 정의의 원본을 모니터링 하는 것이 좋습니다.

사용자 지정 규정 준수 이니셔티브를 Azure Security Center 대시보드에 연결 하려면 [Azure Security Center-사용자 지정 보안 정책 사용](../../../security-center/custom-security-policies.md)을 참조 하세요.

## <a name="regulatory-compliance-in-portal"></a>포털의 규정 준수

[그룹](./initiative-definition-structure.md#policy-definition-groups)을 사용 하 여 이니셔티브 정의를 만든 경우 포털의 **준수** 정보 페이지에는 추가 정보가 포함 됩니다. 

새 **탭이 페이지에 추가 됩니다.** 필터링은 **규정 준수 도메인** 에서 사용할 수 있으며 정책 정의는 `title` **policymetadata** 개체의 필드를 기준으로 그룹화 됩니다. 각 행은 준수 상태,이를 포함 하는 **규정 준수 도메인** , 책임 정보 및 해당 **컨트롤**을 구성 하는 비규격 및 규격 정책 정의의 수를 표시 하는 **컨트롤** 을 나타냅니다.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="NIST SP 800-53 4. 기본 제공 정의에 대 한 규정 준수 개요 샘플입니다.":::

**컨트롤** 을 선택 하면 해당 컨트롤에 대 한 세부 정보 페이지가 열립니다. **개요** 에는 및의 정보가 포함 되어 있습니다 `description` `requirements` . **정책** 탭에는이 **컨트롤**에 기여 하는 이니셔티브의 개별 정책 정의가 모두 있습니다. **리소스 준수** 탭은 현재 표시 된 **컨트롤**의 멤버 정책에 의해 평가 되는 각 리소스에 대 한 세부적인 뷰를 제공 합니다.

> [!NOTE]
> **Microsoft에서 관리** 하는 평가 유형은 [정적](./definition-structure.md#type) 정책 정의를 위한 것입니다 `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="NIST SP 800-53 4, 기본 제공 정의의 시스템 및 통신 보호 도메인에 대 한 경계 보호 제어의 규정 준수 정책 정의 샘플입니다.":::

동일한 **컨트롤** 페이지에서 **리소스 준수** 탭으로 변경 하면이 **컨트롤**의 정책 정의에 포함 된 모든 리소스가 표시 됩니다. 이름 또는 ID, 준수 상태, 리소스 유형 및 위치에 대 한 필터를 사용할 수 있습니다.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="NIST SP 800-53 4. 기본 제공 정의의 시스템 및 통신 보호 도메인에 대 한 경계 보호 제어에서 정책 정의의 영향을 받는 규제 준수 리소스의 샘플입니다.":::

## <a name="regulatory-compliance-in-sdk"></a>SDK의 규정 준수

이니셔티브 정의에 규정 준수를 사용 하는 경우 평가 검색 레코드, 이벤트 및 정책 상태 SDK는 각각 추가 속성을 반환 합니다. 이러한 추가 속성은 규정 준수 상태별로 그룹화 되며 각 상태에 있는 그룹 수에 대 한 정보를 제공 합니다.

다음 코드는 호출에서 추가 된 결과의 예입니다 `summarize` .

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>다음 단계

- [이니셔티브 정의 구조](./initiative-definition-structure.md) 를 참조 하세요.
- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [정책 효과 이해](./effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
