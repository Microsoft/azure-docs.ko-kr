---
title: '빠른 시작: Resource Manager 템플릿을 사용하여 Windows VM 만들기'
description: 이 빠른 시작에서는 Resource Manager 템플릿을 사용하여 Windows 가상 머신을 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: ded59b2f517c5b109dfd00bde2fb73f8351bf821
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649672"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-an-arm-template"></a>빠른 시작: ARM 플랫폼을 사용하여 Windows 가상 머신 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure에서 Windows VM(가상 머신)을 배포하는 방법을 보여줍니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json":::


이러한 리소스는 템플릿에 정의되어 있습니다.

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): 서브넷을 만듭니다.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): 스토리지 계정을 만듭니다.
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): 공용 IP 주소를 만듭니다.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): 네트워크 보안 그룹을 만듭니다.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): 가상 네트워크를 만듭니다.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): NIC를 만듭니다.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): 가상 머신을 만듭니다.



## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿에서 키 자격 증명 모음 및 비밀이 생성됩니다.

    [![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. 다음 값을 선택하거나 입력합니다. 사용 가능한 경우 기본값을 사용합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: 드롭 다운에서 기존의 리소스 그룹을 선택하거나 **새로 만들기**를 선택하고 리소스 그룹의 고유한 이름을 입력한 다음, **확인**을 클릭합니다.
    - **위치**: 위치를 선택합니다.  예: **미국 중부**
    - **관리 사용자 이름**: *azureuser*와 같은 사용자 이름을 제공합니다.
    - **관리자 암호**: 관리자 계정에 사용할 암호를 제공합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.
    - **DNS 레이블 접두사**: DNS 레이블의 일부로 사용할 고유 식별자를 입력합니다.
    - **Windows OS 버전**: VM에서 실행하려는 Windows 버전을 선택합니다.
    - **VM 크기**: VM에 사용할 [크기](../sizes.md)를 선택합니다.
    - **위치**: 기본값은 리소스 그룹과 같은 위치입니다(이미 있는 경우).
1. **검토 + 만들기**를 선택합니다. 유효성 검사가 완료된 후 **만들기**를 선택하여 VM을 만들고 배포합니다.


Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 만든 VM 및 기타 리소스를 확인할 수 있습니다. 배포가 완료되면 **리소스 그룹으로 이동**을 선택하여 VM 및 기타 리소스를 확인합니다.


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 VM과 모든 리소스가 삭제됩니다. 

1. **리소스 그룹**을 선택합니다.
1. 리소스 그룹 페이지에서 **삭제**를 선택합니다.
1. 메시지가 표시되면 리소스 그룹의 이름을 입력한 다음 **삭제**를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 간단한 가상 머신을 배포했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Linux VM의 자습서를 계속 진행합니다.


> [!div class="nextstepaction"]
> [Azure Windows 가상 머신 자습서](./tutorial-manage-vm.md)