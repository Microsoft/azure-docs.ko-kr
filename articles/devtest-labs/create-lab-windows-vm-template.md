---
title: Azure DevTest Labs 및 Azure Resource Manager 템플릿을 사용하여 랩 만들기
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure DevTest Labs에서 랩을 만듭니다. 랩 관리자는 랩을 설정하고, 랩에 VM을 만들고, 정책을 구성합니다.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 93bc4cf91b228de72c217c61ec03d61e87046dd3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653956"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>빠른 시작: Azure DevTest Labs ARM 템플릿을 사용하여 랩 설정
이 빠른 시작에서는 ARM(Azure Resource Manager) 템플릿을 사용하여 Windows Server 2019 Datacenter VM에서 랩을 만듭니다. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

이 빠른 시작에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 템플릿 검토 
> * 템플릿 배포
> * 템플릿 확인
> * 리소스 정리

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

템플릿에 정의된 리소스는 다음과 같습니다.

- [**Microsoft.DevTestLab/labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft.DevTestLab labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft.DevTestLab labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

더 많은 샘플 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)에서 찾을 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포
배포를 자동으로 실행하려면 다음 단추를 클릭합니다. 

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. 나중에 쉽게 정리할 수 있도록 **새 리소스 그룹**을 만듭니다.
1. 리소스 그룹의 **위치**를 선택합니다. 
1. **랩에 대한 이름**을 입력합니다. 
1. **VM에 대한 이름**을 입력합니다. 
1. VM에 액세스할 수 있는 **사용자 이름**을 입력합니다. 
1. 사용자에 대한 **암호**를 입력합니다. 
1. **위에 명시된 사용 약관에 동의함**을 선택합니다. 
1. 그런 다음, **구매**를 선택합니다.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="템플릿 배포 페이지":::

## <a name="validate-the-deployment"></a>배포 유효성 검사
1. 배포 상태를 확인하려면 맨 위에 있는 **알림**을 선택하고 **배포 진행 중**을 클릭합니다.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="배포 알림":::
2. **배포 - 개요** 페이지에서 배포가 완료될 때까지 기다립니다. 이 작업(특히 VM 만들기)은 완료하는 데 다소 시간이 걸립니다. 그런 다음, 다음 이미지에 표시된 것처럼 **리소스 그룹으로 이동** 또는 **리소스 그룹의 이름**을 선택합니다. 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="리소스 그룹으로 이동":::
3. **리소스 그룹** 페이지에서 리소스 그룹의 리소스 목록이 표시됩니다. 리소스에서 `DevTest Lab` 유형의 랩이 표시되는지 확인합니다. 리소스 그룹의 가상 네트워크 및 가상 머신과 같은 종속 리소스도 표시됩니다. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="리소스 그룹의 홈 페이지":::
4. 랩의 홈 페이지를 보려면 리소스 목록에서 랩을 선택합니다. **내 가상 머신** 목록에서 Windows Server 2019 Datacenter VM이 표시되는지 확인합니다. 다음 이미지에서 **기본 정보** 섹션은 최소화되어 있습니다. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="랩에 대한 홈 페이지":::

    > [!IMPORTANT] 
    > 이 페이지를 열어 두고 다음 섹션의 지침에 따라 Azure에서 랩 및 VM을 실행하는 데 드는 비용을 방지하기 위해 리소스를 정리합니다. 랩에서 VM에 대한 액세스를 테스트하기 위해 다음 자습서를 진행하려는 경우 해당 자습서를 진행한 후 리소스를 정리합니다. 

## <a name="clean-up-resources"></a>리소스 정리

1. 먼저 리소스 그룹을 삭제할 수 있도록 랩을 삭제합니다. 랩이 포함된 리소스 그룹은 삭제할 수 없습니다. 랩을 삭제하려면 도구 모음에서 **삭제**를 선택합니다. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="랩 삭제 단추":::
 2. 확인 페이지에서 **랩 이름**을 입력하고 **삭제**를 선택합니다. 
 3. 랩이 삭제될 때까지 기다립니다. **벨** 아이콘을 선택하여 삭제 작업의 알림을 확인합니다. 이 프로세스는 다소 시간이 걸립니다. 랩 삭제를 확인한 다음, 이동 경로 탐색 메뉴에서 **리소스 그룹**을 선택합니다. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="알림에서 VM 삭제 확인":::
 1. **리소스 그룹** 페이지에서 도구 모음에 있는 **리소스 그룹 삭제**를 선택합니다. 확인 페이지에서 **리소스 그룹 이름**을 입력하고 **삭제**를 선택합니다. 알림을 확인하여 리소스 그룹이 삭제되었는지 확인합니다.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="리소스 그룹 삭제 단추":::

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 VM을 사용하여 랩을 만들었습니다. 랩에 액세스하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [자습서: 랩 액세스](tutorial-use-custom-lab.md)
