---
title: '빠른 시작: 템플릿을 사용하여 공유 쿼리 만들기'
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 OS별 가상 머신 수를 계산하는 Resource Graph 공유 쿼리를 만듭니다.
ms.date: 07/06/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: cc9da201b10b697f125e8ffe7402f23f5eaa8362
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685530"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 공유 쿼리 만들기

Resource Graph 쿼리를 _프라이빗 쿼리_ 또는 _공유 쿼리_로 저장할 수 있습니다. 프라이빗 쿼리는 개인 포털 프로필에 저장되며 다른 사람들에게는 보이지 않습니다. 공유 쿼리는 권한 및 역할 기반 액세스를 통해 다른 사람들과 공유할 수 있는 Resource Manager 개체입니다. 공유 쿼리를 사용하면 보편적이고 일관적인 방법으로 리소스를 검색할 수 있습니다. 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 공유 쿼리를 만듭니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 공유 쿼리를 만들기 위한 ARM 템플릿 배포" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서는 _OS별 VM 수 계산_이라는 공유 쿼리를 만듭니다. SDK 또는 포털에서 Resource Graph Explorer를 사용하여 이 쿼리를 시도해 보려면 [샘플 - OS 유형별로 가상 머신 개수 계산](./samples/starter.md#count-os)을 참조하세요.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

템플릿에 정의된 리소스는 다음과 같습니다.

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>템플릿 배포

> [!NOTE]
> Azure Resource Graph 서비스는 무료입니다. 자세한 내용은 [Azure Resource Graph 개요](./overview.md)를 참조하세요.

1. 다음 이미지를 선택하고 Azure Portal에 로그인하여 템플릿을 엽니다.

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 공유 쿼리를 만들기 위한 ARM 템플릿 배포" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. 다음 값을 선택하거나 입력합니다.

   | Name | 값 |
   |------|-------|
   | Subscription | Azure 구독을 선택합니다. |
   | Resource group | **새로 만들기**를 선택하고 이름을 지정한 다음, **확인**을 선택합니다. |
   | 위치 | 지역을 선택합니다. 예: **미국 중부** |
   | 쿼리 이름 | 기본값인 **OS별 VM 수 계산**을 유지합니다. |
   | 쿼리 코드 | 기본값인 `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)`을 유지합니다. |
   | 쿼리 설명 | 기본값을 유지합니다. **이 공유 쿼리는 OS 유형별로 모든 가상 머신 리소스 수를 계산하고 요약합니다.** |
   | 위에 명시된 사용 약관에 동의함 | (선택) |

1. **구매**를 선택합니다.

몇 가지 추가 리소스:

- 더 많은 샘플 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)을 참조하세요.
- 템플릿 참조를 보려면 [Azure 템플릿 참조](/azure/templates/microsoft.resourcegraph/allversions)를 참조하세요.
- ARM 템플릿을 개발하는 방법을 알아보려면 [Azure Resource Manager 설명서](../../azure-resource-manager/management/overview.md)를 참조하세요.
- 구독 수준 배포에 대해 알아보려면 [구독 수준에서 리소스 그룹 및 리소스 만들기](../../azure-resource-manager/templates/deploy-to-subscription.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

새 공유 쿼리를 실행하려면 다음 단계를 수행합니다.

1. 포털 검색 창에서 **Resource Graph 쿼리**를 검색하여 선택합니다.

1. **OS별 VM 수 계산**이라는 공유 쿼리를 선택한 다음, **개요** 페이지에서 **결과** 탭을 선택합니다.

또는 Resource Graph Explorer에서 공유 쿼리를 열 수 있습니다.

1. 포털 검색 창에서 **Resource Graph Explorer**를 검색하여 선택합니다.

1. **쿼리 열기** 단추를 선택합니다.

1. **형식**을 _공유 쿼리_로 변경합니다. 목록에 **OS별 VM 수 계산**이 보이지 않으면 필터 상자를 사용하여 결과를 제한합니다. **OS별 VM 수 계산**이라는 공유 쿼리가 보이면 이름을 선택합니다.

1. 쿼리가 로드되면 **쿼리 실행** 단추를 선택합니다. 결과는 아래의 **결과** 테이블에 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

만든 공유 쿼리를 제거하려면 다음 단계를 수행합니다.

1. 포털 검색 창에서 **Resource Graph 쿼리**를 검색하여 선택합니다.

1. **OS별 VM 수 계산**이라는 공유 쿼리 옆에 있는 확인란을 선택합니다.

1. 페이지 맨 위에서 **삭제** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Resource Graph 공유 쿼리를 만들었습니다.

공유 쿼리에 대해 자세히 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 쿼리 관리](./tutorials/create-share-query.md)