---
title: '빠른 시작: 템플릿을 사용하여 새 정책 할당'
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 비규격 리소스를 식별하는 정책 할당을 만듭니다.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 2f012f9cc8f986f8f887096098961e44c4b7dffb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689850"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 비규격 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다.
이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만드는 과정을 단계별로 안내합니다. 이 과정이 끝나면 관리 디스크를 사용하지 않는 가상 머신이 식별됩니다. 이 가상 머신은 정책 할당을 _비준수_합니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 Azure Policy를 할당하기 위한 ARM 템플릿 배포" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서는 정책 할당을 만들고 관리 디스크를 사용하지 않는 VM 감사(_Audit VMs that do not use managed disks_)라는 기본 제공 정책 정의를 할당합니다. 사용 가능한 기본 제공 정책의 부분 목록은 [Azure Policy 샘플](./samples/index.md)을 참조하세요.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json":::

템플릿에 정의된 리소스는 다음과 같습니다.

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>템플릿 배포

> [!NOTE]
> Azure Policy 서비스는 무료입니다. 자세한 내용은 [Azure Policy 개요](./overview.md)를 참조하세요.

1. 다음 이미지를 선택하고 Azure Portal에 로그인하여 템플릿을 엽니다.

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 Azure Policy를 할당하기 위한 ARM 템플릿 배포" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. 다음 값을 선택하거나 입력합니다.

   | Name | 값 |
   |------|-------|
   | Subscription | Azure 구독을 선택합니다. |
   | Resource group | **새로 만들기**를 선택하고 이름을 지정한 다음, **확인**을 선택합니다. 스크린샷에서 리소스 그룹 이름은 _mypolicyquickstart\<Date in MMDD\>rg_입니다. |
   | 위치 | 지역을 선택합니다. 예: **미국 중부** |
   | 정책 할당 이름 | 정책 할당 이름을 지정합니다. 원하는 경우 정책 정의 표시 이름을 사용할 수 있습니다. 예: _관리 디스크를 사용하지 않는 VM 감사_. |
   | Rg 이름 | 정책을 할당할 리소스 그룹 이름을 지정합니다. 이 빠른 시작에서는 기본값인 **[resourceGroup().name]** 을 사용합니다. **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** 은 리소스 그룹을 검색하는 템플릿 함수입니다. |
   | 정책 정의 ID | **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**를 지정합니다. |
   | 위에 명시된 사용 약관에 동의함 | (선택) |

1. **구매**를 선택합니다.

몇 가지 추가 리소스:

- 더 많은 샘플 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)을 참조하세요.
- 템플릿 참조를 보려면 [Azure 템플릿 참조](/azure/templates/microsoft.authorization/allversions)를 참조하세요.
- ARM 템플릿을 개발하는 방법을 알아보려면 [Azure Resource Manager 설명서](../../azure-resource-manager/management/overview.md)를 참조하세요.
- 구독 수준 배포에 대해 알아보려면 [구독 수준에서 리소스 그룹 및 리소스 만들기](../../azure-resource-manager/templates/deploy-to-subscription.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

페이지 왼쪽에서 **준수**를 선택합니다. 그런 다음, 앞에서 만든 _관리 디스크를 사용하지 않는 감사 VM_ 정책 할당을 찾습니다.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="정책 규정 준수 개요 페이지" border="false":::

이 새로운 할당을 준수하지 않는 기존 리소스가 있는 경우 **비준수 리소스** 아래에 표시됩니다.

자세한 내용은 [규정 준수 작동 방식](./how-to/get-compliance-data.md#how-compliance-works)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

만든 할당을 제거하려면 다음 단계를 따르세요.

1. Azure Policy 페이지 왼쪽에서 **준수**(또는 **할당**)를 선택하고 앞에서 만든 _관리 디스크를 사용하지 않는 감사 VM_ 정책 할당을 찾습니다.

1. _관리 디스크를 사용하지 않는 VM 감사_ 정책 할당을 마우스 오른쪽 단추로 클릭하고 **할당 삭제**를 선택합니다.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="규정 준수 요약 페이지에서 할당 삭제" border="false":::

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 범위에 기본 제공 정책 정의를 할당하고 규정 준수 보고서를 평가했습니다. 정책 정의는 범위 내 모든 리소스가 규정을 준수하는지 확인하고, 규정을 준수하지 않는 리소스를 파악합니다.

새 리소스가 규정을 준수하는지 확인하는 정책을 할당하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)