---
title: '빠른 시작: Resource Manager 템플릿을 사용하여 가상 네트워크 만들기'
titleSuffix: Azure Virtual Network
description: Resource Manager 템플릿을 사용하여 Azure 가상 네트워크를 만드는 방법을 알아봅니다.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c9e9ea2e59537a1c0c8e372a766fba3aa9a1b9a0
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122161"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>빠른 시작: 가상 네트워크 만들기 - Resource Manager 템플릿

이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 두 개의 서브넷이 있는 가상 네트워크를 만드는 방법을 알아봅니다. 가상 네트워크는 Azure에서 프라이빗 네트워크의 기본 구성 요소입니다. 이 네트워크를 사용하면 VM과 같은 Azure 리소스가 서로 인터넷을 통해 안전하게 통신할 수 있습니다.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) 또는 [Azure CLI](quick-create-cli.md)를 사용하여 이 빠른 시작을 완료할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

템플릿에는 다음과 같은 Azure 리소스가 정의되어 있습니다.
- [**Microsoft.Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks): Azure 가상 네트워크를 만듭니다.
-  [**Microsoft.Network/virtualNetworks/subnets**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets) - 서브넷을 만듭니다.

## <a name="deploy-the-template"></a>템플릿 배포

Azure에 Resource Manager 템플릿 배포:

1. **Azure에 배포**를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 템플릿은 두 개의 서브넷이 있는 가상 네트워크를 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. 포털의 **두 개의 서브넷이 있는 Virtual Network 만들기** 페이지에서 다음 값을 입력하거나 선택합니다.
   - **리소스 그룹**: **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력하고 **확인**을 선택합니다.
   - **가상 네트워크 이름**: 새 가상 네트워크의 이름을 입력합니다.
3. **검토 및 만들기**를 선택한 후 **만들기**를 선택합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

가상 네트워크를 통해 만들어진 리소스를 검색합니다.

템플릿에서 가상 네트워크의 JSON 구문 및 속성에 대해 알아보려면 [Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

가상 네트워크로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 가상 네트워크와 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 두 개의 서브넷이 있는 Azure 가상 네트워크를 배포했습니다. Azure 가상 네트워크에 대해 자세히 알아보려면 가상 네트워크의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md)
