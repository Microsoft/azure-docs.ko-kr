---
title: '빠른 시작: Resource Manager 템플릿을 사용하여 웹 트래픽 보내기'
titleSuffix: Azure Application Gateway
description: Resource Manager 템플릿을 사용하여 백 엔드 풀의 가상 머신에 웹 트래픽을 보내는 Azure Application Gateway를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ebcb51dc4de9283752d61831eb0cad801ec1ac4b
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705948"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>빠른 시작: Azure Application Gateway를 통해 웹 트래픽 보내기 - ARM 템플릿

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Application Gateway를 만듭니다. 그런 다음, 애플리케이션 게이트웨이를 테스트하여 제대로 작동하는지 확인합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) 또는 [Azure CLI](quick-create-cli.md)를 사용하여 이 빠른 시작을 완료할 수도 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>템플릿 검토

간단히 나타내기 위해 이 템플릿에서는 퍼블릭 프런트 엔드 IP 1개, 애플리케이션 게이트웨이에 단일 사이트를 호스트하는 기본 수신기 1개, 기본 요청 라우팅 규칙 1개 및 백 엔드 풀의 가상 머신 2개를 이용한 간단한 설정을 만듭니다.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/ag-docs-qs/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

템플릿에는 여러 개의 Azure 리소스가 정의되어 있습니다.

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): 애플리케이션 게이트웨이에 대해 하나, 가상 머신용으로 두 개가 있습니다.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): 두 개의 가상 머신
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): 가상 머신용으로 두 개
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions): IIS 및 웹 페이지 구성

## <a name="deploy-the-template"></a>템플릿 배포

Azure에 ARM 템플릿을 배포합니다.

1. **Azure에 배포**를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 템플릿은 IIS를 실행하는 백 엔드 풀에 애플리케이션 게이트웨이, 네트워크 인프라 및 두 개의 가상 머신을 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. 리소스 그룹을 선택하거나 만들고, 가상 머신 관리자의 사용자 이름 및 암호를 입력합니다.
3. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다.

   배포를 완료하는 데 20분 이상 걸릴 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

애플리케이션 게이트웨이를 만들기 위해 반드시 IIS가 필요한 것은 아니지만, Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지 확인하기 위해 설치했습니다. IIS를 사용하여 애플리케이션 게이트웨이 테스트:

1. **개요** 페이지에서 애플리케이션 게이트웨이에 대한 공용 IP 주소를 찾습니다.![애플리케이션 게이트웨이 공용 IP 주소를 기록](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)하거나, **모든 리소스**를 선택하고 검색 상자에 *myAGPublicIPAddress*를 입력한 후 검색 결과에서 선택합니다. Azure는 공용 IP 주소를 **개요** 페이지에 표시합니다.
2. 공용 IP 주소를 복사한 다음, 브라우저의 주소 표시줄에 붙여 넣어 해당 IP 주소를 찾습니다.
3. 응답을 확인합니다. 응답이 유효하면 애플리케이션 게이트웨이가 성공적으로 만들어졌으며 백 엔드에 성공적으로 연결할 수 있다는 의미입니다.

   ![애플리케이션 게이트웨이 테스트](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   브라우저를 여러 번 새로 고치면 myVM1 및 myVM2에 대한 연결이 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 게이트웨이로 만든 리소스가 더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 애플리케이션 게이트웨이 및 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)
