---
title: '자습서: Azure Portal에서 쿼리 관리'
description: 이 자습서에서는 Resource Graph 쿼리를 만들고 Azure Portal의 다른 사용자와 새 쿼리를 공유합니다.
ms.date: 06/29/2020
ms.topic: tutorial
ms.openlocfilehash: f602a6ed26f467f2d3395bdf5e3346c83684b108
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005182"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>자습서: Azure Portal에서 Azure Resource Graph 쿼리 만들기 및 공유

Azure Resource Graph Explorer를 사용하여 Azure Portal에 직접 Resource Graph 쿼리를 저장할 수 있습니다. 쿼리 유형으로는 _프라이빗_ 쿼리와 _공유_ 쿼리가 있습니다. 프라이빗 쿼리는 Azure Portal 설정에 저장됩니다. 반면 공유 쿼리는 Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 관리하고 리소스 잠금으로 보호할 수 있는 Azure Resource Manager 리소스입니다. 두 유형의 쿼리는 모두 미사용으로 암호화됩니다.

Azure Portal에 쿼리를 저장하면 자주 사용하는 쿼리를 찾는 데 걸리는 시간을 절약할 수 있습니다. 쿼리를 공유하면 팀이 반복을 통해 일관성 및 효율성 목표를 실현하는 데 도움이 됩니다.

이 자습서에서는 다음 작업을 완료할 것입니다.

> [!div class="checklist"]
> - 프라이빗 쿼리 만들기 및 삭제
> - 공유 쿼리 만들기
> - 공유 쿼리 검색
> - 공유 쿼리 삭제

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/)을 만드세요.

## <a name="create-and-delete-a-private-query"></a>프라이빗 쿼리 만들기 및 삭제

프라이빗 쿼리는 프라이빗 쿼리를 만드는 계정에서만 액세스하고 볼 수 있습니다. 프라이빗 쿼리는 계정의 Azure Portal 설정에 저장되므로 Azure Portal 내부에서만 만들고, 사용하고, 삭제할 수 있습니다. 프라이빗 쿼리는 Resource Manager 리소스가 아닙니다. 새 프라이빗 쿼리를 만들려면 다음 단계를 수행합니다.

1. 포털 메뉴에서 **모든 서비스**를 선택하거나 모든 페이지의 위쪽에 있는 Azure 검색 상자를 사용합니다. **Resource Graph Explorer**를 검색하여 선택합니다.

1. Azure Resource Graph Explorer 페이지의 **쿼리 1** 탭에서 다음 쿼리를 입력합니다.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   **쿼리 실행**을 선택하여 아래쪽 창에서 쿼리 결과를 확인합니다.

   이 쿼리에 대한 자세한 내용은 [샘플 – OS 유형별로 가상 머신 개수 계산](../samples/starter.md#count-os)을 참조하세요.

1. **저장** 또는 **다른 이름으로 저장**을 선택하고, 이름으로 **OS별로 VM 수 계산**을 입력하고, 유형을 **프라이빗 쿼리**로 그대로 두고, **쿼리 저장** 창의 아래쪽에서 **저장**을 선택합니다. 탭 제목이 **쿼리 1**에서 **OS별로 VM 수 계산**으로 변경됩니다.

1. Azure Portal에서, Azure Resource Graph Explorer에서 나왔다가 다시 돌아갑니다. 저장된 쿼리가 더 이상 표시되지 않고 **쿼리 1** 탭이 반환됩니다.

1. **쿼리 열기**를 선택합니다. 유형이 **프라이빗 쿼리**인지 확인합니다. 이제 저장된 이름 **OS별로 VM 수 계산**이 **쿼리 이름** 목록에 표시됩니다. 저장된 쿼리의 제목 링크를 선택하면 해당 쿼리 이름을 사용하는 새 탭에 로드됩니다.

   > [!NOTE] 
   > 저장된 쿼리가 열려 있고 탭이 해당 이름을 표시하는 경우 **저장** 단추를 선택하면 저장된 쿼리가 변경 내용으로 업데이트됩니다. 열려 있는 쿼리에서 저장된 쿼리를 새로 만들려면 **다른 이름으로 저장**을 선택하고, 새 쿼리를 저장할 때처럼 계속 진행합니다.

1. 저장된 쿼리를 삭제하려면 **쿼리 열기**를 다시 선택하고, **유형** 필드가 **프라이빗 쿼리**로 설정되었는지 확인합니다. 저장된 `Count VMs by OS` 쿼리의 행에서 **삭제**(휴지통 아이콘)를 선택합니다. 확인 대화 상자에서 **예**를 선택하여 쿼리 삭제를 완료합니다.
   그런 다음, **쿼리 열기** 창을 닫습니다.

## <a name="create-a-shared-query"></a>공유 쿼리 만들기

프라이빗 쿼리와 달리, 공유 쿼리는 Resource Manager 리소스입니다. 즉, 쿼리가 리소스 그룹에 저장되고, Azure RBAC를 사용하여 관리 및 제어할 수 있으며, 리소스 잠금으로 보호할 수도 있습니다. 리소스이기 때문에 적절한 권한이 있는 모든 사람이 공유 쿼리를 보고 사용할 수 있습니다.
새 공유 쿼리를 만들려면 다음 단계를 수행합니다.

1. 포털 메뉴에서 **모든 서비스**를 선택하거나, 모든 페이지의 위쪽에 있는 Azure 검색 상자를 사용하여 **Resource Graph Explorer**를 검색하고 선택합니다.

1. Azure Resource Graph Explorer 페이지의 **쿼리 1** 탭에서 다음 쿼리를 입력합니다.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   **쿼리 실행**을 선택하여 아래쪽 창에서 쿼리 결과를 확인합니다.

   이 쿼리에 대한 자세한 내용은 [샘플 – OS 유형별로 가상 머신 개수 계산](../samples/starter.md#count-os)을 참조하세요.

1. **저장** 또는 **다른 이름으로 저장**을 선택합니다.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="저장 단추를 사용하여 새 쿼리 저장" border="false":::

1. **쿼리 저장** 창에서 쿼리 이름으로 **OS별로 VM 수 계산**을 입력합니다.

1. 유형을 **공유 쿼리**으로 변경하고, 설명을 **OS 유형별로 가상 머신 수 계산**으로 설정하고, **구독**을 설정하여 쿼리 리소스를 생성할 위치를 지정합니다.

1. **resource-graph-queries 리소스 그룹에 게시** 확인란을 선택하고 **리소스 그룹 위치**를 **(미국) 미국 중서부**로 설정합니다.

1. **쿼리 저장** 창의 아래쪽에서 **저장**을 선택합니다. 탭 제목이 **쿼리 1**에서 **OS별로 VM 수 계산**으로 변경됩니다. **resource-graph-queries** 리소스 그룹을 처음으로 사용할 때 리소스 그룹이 생성되므로 저장 시간이 예상보다 오래 걸립니다.
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="새 쿼리를 공유 쿼리로 저장" border="false":::

   > [!NOTE] 
   > 공유 쿼리를 저장할 기존 리소스 그룹의 이름을 직접 입력하려면 **resource-graph-queries 리소스 그룹에 게시** 확인란의 선택을 취소하면 됩니다. 쿼리에 대해 지정되는 기본 리소스 그룹을 사용하면 공유 쿼리를 좀 더 쉽게 검색할 수 있습니다. 또한 해당 리소스 그룹의 용도를 더욱 명확히 할 수 있습니다. 그러나 보안상의 이유로 기존 권한을 기반으로 기존 리소스 그룹을 선택하도록 할 수도 있습니다.

1. Azure Portal에서, Azure Resource Graph Explorer에서 나왔다가 다시 돌아갑니다. 저장된 쿼리가 더 이상 표시되지 않고 **쿼리 1** 탭이 반환됩니다.

1. **쿼리 열기**를 선택합니다. 유형이 **공유 쿼리**로 설정되어 있고 **구독**과 **리소스 그룹**의 조합이 쿼리를 저장한 위치와 일치하는지 확인합니다. 이제 저장된 **OS별로 VM 수 계산** 항목이 **쿼리 이름** 목록에 표시됩니다. 저장된 쿼리의 제목 링크를 선택하여 해당 쿼리 이름을 사용하는 새 탭에 쿼리를 로드합니다. 공유 쿼리이므로 공유 쿼리임을 나타내는 아이콘이 제목 옆의 탭에 표시됩니다.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="아이콘을 사용하여 공유 쿼리 표시" border="false":::

   > [!NOTE] 
   > 저장된 쿼리가 열려 있고 탭이 해당 이름을 표시하는 경우 **저장** 단추를 누르면 저장된 쿼리가 변경 내용으로 업데이트됩니다. 저장된 쿼리를 새로 만들려면 **다른 이름으로 저장**을 선택하고, 새 쿼리를 저장할 때처럼 계속 진행합니다.

## <a name="discover-shared-queries"></a>공유 쿼리 검색

공유 쿼리는 Resource Manager 리소스이므로 다음과 같은 여러 가지 방법으로 찾을 수 있습니다.

- Resource Graph Explorer에서 **쿼리 열기**를 선택하고 유형을 **공유 쿼리**로 설정합니다.
- Resource Graph 쿼리 포털 페이지에서 찾습니다.
- 공유 쿼리가 저장된 리소스 그룹에서 찾습니다.
- Resource Graph 쿼리를 통해 찾습니다.

### <a name="view-resource-graph-queries"></a>Resource Graph 쿼리 보기

Azure Portal의 Resource Graph 쿼리 페이지에는 로그인한 계정에서 액세스할 수 있는 공유 쿼리가 표시됩니다. 이 페이지에서는 Resource Graph 쿼리의 이름, 구독, 리소스 그룹 및 기타 속성을 기준으로 필터링할 수 있습니다. 또한 이 인터페이스를 사용하여 Resource Graph 쿼리를 내보내고, 삭제하고, 태그를 지정할 수 있습니다.

쿼리 중 하나를 선택하면 Resource Graph 쿼리 페이지가 열립니다. 다른 Resource Manager 리소스와 마찬가지로, 이 페이지는 활동 로그, 액세스 제어 및 태그와 함께 대화형 개요를 제공합니다. 또한 이 페이지에서 직접 리소스 잠금을 적용할 수 있습니다.

**모든 서비스**를 선택하거나 모든 페이지의 위쪽에 있는 Azure 검색 상자를 사용하여 포털 메뉴에서 Resource Graph 쿼리 페이지로 이동합니다. **Resource Graph Explorer**를 검색하고 선택합니다.

### <a name="list-resource-groups-resources"></a>리소스 그룹 리소스 나열

Resource Graph 쿼리는 리소스 그룹에 포함된 다른 리소스와 함께 나열됩니다.
Resource Graph 쿼리를 선택하면 해당 쿼리에 대한 페이지가 열립니다. 줄임표 및 바로 가기 메뉴 옵션(마우스 오른쪽 단추를 클릭하면 트리거됨)은 Resource Graph 쿼리 페이지와 동일한 방식으로 작동합니다.

### <a name="query-resource-graph"></a>Resource Graph 쿼리

Resource Graph 쿼리를 통해 Resource Graph 쿼리를 찾을 수 있습니다. 다음 Resource Graph 쿼리는 `Microsoft.ResourceGraph/queries` 유형으로 제한한 다음, `project`를 사용하여 이름, 수정된 시간 및 쿼리 자체만 나열합니다.

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>공유 쿼리 실행

리소스 그래프 공유 쿼리는 `{{shared-query-uri}}` 구문(미리 보기)을 사용하여 실행할 수 있습니다. 자세한 내용은 [공유 쿼리 구문](../concepts/query-language.md#shared-query-syntax)을 참조하세요.

## <a name="delete-a-shared-query"></a>공유 쿼리 삭제

공유 쿼리가 더 이상 필요 없는 경우 삭제합니다. 공유 쿼리를 삭제하면 해당하는 Resource Manager 리소스가 제거됩니다. 결과 차트가 고정된 대시보드는 오류 메시지를 표시합니다. 이 오류 메시지가 표시되면 **대시보드에서 제거** 단추를 사용하여 대시보드를 정리합니다.

다음 인터페이스를 통해 공유 쿼리를 삭제할 수 있습니다.
- Resource Graph 쿼리 페이지
- Resource Graph 쿼리 페이지
- Resource Graph Explorer의 **쿼리 열기** 페이지
- 리소스 그룹 페이지

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 마친 후에는 이 자습서에서 만든 프라이빗 쿼리와 공유 쿼리 중에서 더 이상 필요 없는 것들을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 프라이빗 및 공유 쿼리를 만들었습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](../concepts/query-language.md)