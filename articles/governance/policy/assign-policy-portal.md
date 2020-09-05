---
title: '빠른 시작: 포털을 사용하여 새 정책 할당'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 비규격 리소스를 식별하는 Azure Policy 할당을 만듭니다.
ms.date: 08/17/2020
ms.topic: quickstart
ms.openlocfilehash: eb3f97ab2f8da3ff2809cb969c8442779e173983
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548382"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>빠른 시작: 비준수 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다.
이 빠른 시작에서는 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만들고 할당하는 과정을 단계별로 안내합니다.

이 과정이 끝나면 관리 디스크를 사용하지 않는 가상 머신이 식별됩니다. 이 가상 머신은 정책 할당을 _비준수_합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고 ‘관리 디스크를 사용하지 않는 VM 감사’ 정책 정의를 할당합니다. 

1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="모든 서비스에서 정책 검색" border="false":::

1. Azure Policy 페이지의 왼쪽에서 **할당**을 선택합니다. 할당은 특정 범위 내에서 수행하도록 할당된 정책입니다.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="정책 개요 페이지에서 할당 페이지 선택" border="false":::

1. **정책 - 할당** 창의 위쪽에서 **정책 할당**을 선택합니다.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="할당 페이지에서 정책 정의 할당" border="false":::

1. **정책 할당** 페이지에서 줄임표를 선택한 다음, 관리 그룹 또는 구독을 선택하여 **범위**를 설정합니다. 원하는 경우 리소스 그룹을 선택합니다. 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 그런 다음, **범위** 페이지 하단에 있는 **선택** 단추를 사용합니다.

   이 예제에서는 **Contoso** 구독을 사용합니다. 자신의 구독은 다를 수 있습니다.

1. **범위**에 따라 리소스를 제외할 수 있습니다. **제외**는 **범위** 수준보다 한 수준 아래에서 시작됩니다. **제외**는 원하는 경우에만 적용하면 되므로 지금은 비워 둡니다.

1. **정책 정의** 줄임표를 선택하여 사용 가능한 정의 목록을 엽니다. Azure Policy에는 사용 가능한 기본 제공 정책 정의가 포함되어 있습니다. 다음과 같은 여러 정의를 사용할 수 있습니다.

   - 태그 및 해당 값 강제 적용
   - 태그 및 해당 값 적용
   - 누락된 경우 리소스 그룹에서 태그 상속

   사용 가능한 기본 제공 정책의 부분 목록은 [Azure Policy 샘플](./samples/index.md)을 참조하세요.

1. 정책 정의 목록을 검색하여 _관리 디스크를 사용하지 않는 VM 감사_ 정의를 찾습니다. 해당 정책을 선택한 다음, **선택** 단추를 사용합니다.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="올바른 정책 정의 찾기" border="false":::

1. 선택한 정책 이름이 **할당 이름**에 자동으로 채워지지만, 할당 이름을 변경할 수 있습니다. 이 예제에서는 _관리 디스크를 사용하지 않는 VM 감사_를 사용합니다. 선택적인 **설명**을 추가할 수도 있습니다. 설명은 이 정책 할당에 대한 세부 정보를 제공합니다.
   **할당한 사람**은 로그인한 사용자를 기준으로 자동으로 입력됩니다. 이 필드는 선택 사항이므로 사용자 지정 값을 입력할 수 있습니다.

1. **관리 ID 만들기**는 선택하지 않은 상태로 유지합니다. 정책이나 이니셔티브에 [deployIfNotExists](./concepts/effects.md#deployifnotexists)가 적용된 정책이 있으면 이 확인란을 _선택해야 합니다_. 이 빠른 시작에 사용되는 정책의 경우 해당 항목이 적용되지 않으므로 이 확인란을 비워 둡니다. 자세한 내용은 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md) 및 [수정 보안의 작동 방식](./how-to/remediate-resources.md#how-remediation-security-works)을 참조하세요.

1. **할당**을 선택합니다.

이제 비규격 리소스를 식별하여 환경의 규정 준수 상태를 파악할 준비가 되었습니다.

## <a name="identify-non-compliant-resources"></a>규정 비준수 리소스 식별

페이지 왼쪽에서 **준수**를 선택합니다. 그런 다음, 앞에서 만든 _관리 디스크를 사용하지 않는 감사 VM_ 정책 할당을 찾습니다.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="정책 규정 준수 페이지의 규정 준수 세부 정보" border="false":::

이 새로운 할당을 준수하지 않는 기존 리소스가 있는 경우 **비준수 리소스** 아래에 표시됩니다.

기존 리소스에 대해 조건을 평가한 결과 이것이 사실로 확인된 경우 해당 리소스는 정책 비준수 항목으로 표시됩니다. 다음 표는 여러 정책 효과가 조건 평가와 함께 작동하여 어떤 준수 상태로 이어지는지 보여줍니다. Azure Portal에는 평가 논리가 표시되지 않지만 규정 준수 상태 결과가 표시됩니다. 규정 준수 상태 결과는 준수 또는 비준수입니다.

| **리소스 상태** | **효과** | **정책 평가** | **규정 준수 상태** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | 비준수 |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | 준수 |
| 새로 만들기 | Audit, AuditIfNotExist\* | True | 비준수 |
| 새로 만들기 | Audit, AuditIfNotExist\* | False | 준수 |

\* Append, DeployIfNotExist 및 AuditIfNotExist 효과는 IF 문이 TRUE여야 합니다.
또한 이 효과는 비준수가 되려면 존재 조건이 FALSE가 되어야 합니다. TRUE인 경우 IF 조건이 관련 리소스에 대한 존재 조건의 평가를 트리거합니다.

## <a name="clean-up-resources"></a>리소스 정리

만든 할당을 제거하려면 다음 단계를 따르세요.

1. Azure Policy 페이지 왼쪽에서 **준수**(또는 **할당**)를 선택하고 앞에서 만든 _관리 디스크를 사용하지 않는 감사 VM_ 정책 할당을 찾습니다.

1. _관리 디스크를 사용하지 않는 VM 감사_ 정책 할당을 마우스 오른쪽 단추로 클릭하고 **할당 삭제**를 선택합니다.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="규정 준수 페이지에서 할당 삭제" border="false":::

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 범위에 정책 정의를 할당하고 규정 준수 보고서를 평가했습니다.
정책 정의는 범위 내 모든 리소스가 규정을 준수하는지 확인하고, 규정을 준수하지 않는 리소스를 파악합니다.

새 리소스가 규정을 준수하는지 확인하는 정책을 할당하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)