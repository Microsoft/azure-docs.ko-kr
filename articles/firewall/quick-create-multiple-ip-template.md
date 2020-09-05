---
title: '빠른 시작: 여러 공용 IP 주소로 Azure Firewall 만들기 - Resource Manager 템플릿'
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 여러 공용 IP 주소로 Azure Firewall을 만드는 방법에 대해 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 41190cd52e29b187182589f93e9d494d05943cf8
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705438"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---arm-template"></a>빠른 시작: 여러 공용 IP 주소로 Azure Firewall 만들기 - ARM 템플릿

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 여러 공용 IP 주소로 Azure Firewall을 배포합니다. 배포된 방화벽에는 두 개의 Windows Server 2019 가상 머신에 대한 RDP 연결을 허용하는 NAT 규칙 수집 규칙이 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

여러 공용 IP 주소를 사용하는 Azure 방화벽에 대한 자세한 내용은 [Azure PowerShell을 사용하여 여러 공용 IP 주소로 Azure Firewall 배포](deploy-multi-public-ip-powershell.md)를 참조하세요.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>템플릿 검토

이 템플릿은 Azure Firewall을 지원하는 데 필요한 리소스와 함께 두 개의 공용 IP 주소로 Azure Firewall을 만듭니다.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/fw-docs-qs)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json":::

템플릿에는 여러 개의 Azure 리소스가 정의되어 있습니다.

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>템플릿 배포

Azure에 ARM 템플릿을 배포합니다.

1. **Azure에 배포**를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 템플릿은 Azure Firewall, 네트워크 인프라 및 두 개의 가상 머신을 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. 포털의 **여러 IP 주소로 Azure Firewall 만들기** 페이지에서 다음 값을 입력하거나 선택합니다.
   - 구독: 기존 구독에서 선택 
   - 리소스 그룹:  기존 리소스 그룹에서 선택하거나 **새로 만들기**를 선택하고 **확인**을 선택합니다.
   - 위치: 위치 선택
   - 관리자 사용자 이름: 관리자 사용자 계정에 대한 사용자 이름 입력 
   - 관리자 암호: 관리자 암호 또는 키 입력

3. **위에 명시된 사용 약관에 동의함**을 선택한 다음, **구매**를 선택합니다. 배포를 완료하는 데 10분 이상 걸릴 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

Azure Portal에서 배포된 리소스를 검토합니다. 방화벽 공용 IP 주소를 확인합니다.  

원격 데스크톱 연결을 사용하여 방화벽 공용 IP 주소에 연결합니다. 성공적인 연결에서는 백 엔드 서버에 대한 연결을 허용하는 방화벽 NAT 규칙을 보여줍니다.

## <a name="clean-up-resources"></a>리소스 정리

방화벽으로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 방화벽과 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure Portal을 사용하여 하이브리드 네트워크에서 Azure Firewall 배포 및 구성](tutorial-hybrid-portal.md)