---
title: '빠른 시작: 가용성 영역을 사용하여 Azure Firewall 만들기 - Resource Manager 템플릿'
description: 템플릿을 사용하여 Azure Firewall을 배포합니다. 이 가상 네트워크에는 세 개의 서브넷이 있는 하나의 VNet이 포함됩니다. 두 개의 Windows Server 가상 머신이 배포됩니다. 점프 상자와 서버입니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 104e2e9f6593d95caf77c0796f8b9465eebcfcee
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705132"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---arm-template"></a>빠른 시작: 가용성 영역을 사용하여 Azure Firewall 배포 - ARM 템플릿

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 세 개의 가용성 영역에서 Azure Firewall을 배포합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

템플릿은 방화벽을 사용하여 테스트 네트워크 환경을 만듭니다. 네트워크에는 다음 세 개의 서브넷이 있는 하나의 VNet(가상 네트워크)이 포함됩니다. *AzureFirewallSubnet*, *ServersSubnet* 및 *JumpboxSubnet*. *ServersSubnet* 및 *JumpboxSubnet* 서브넷에는 각각 하나의 2개 코어 Windows Server 가상 머신이 있습니다.

방화벽은 *AzureFirewallSubnet* 서브넷에 있으며, `www.microsoft.com`에 대한 액세스를 허용하는 단일 규칙이 포함된 애플리케이션 규칙 컬렉션이 포함됩니다.

사용자 정의 경로는 방화벽 규칙이 적용된 방화벽을 통해 *ServersSubnet* 서브넷에서의 네트워크 트래픽을 가리킵니다.

Azure Firewall에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)을 참조하세요.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>템플릿 검토

이 템플릿은 Azure Firewall을 지원하는 데 필요한 리소스와 함께 가용성 영역을 사용하여 Azure Firewall을 만듭니다.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json":::

템플릿에는 여러 개의 Azure 리소스가 정의되어 있습니다.

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>템플릿 배포

Azure에 ARM 템플릿을 배포합니다.

1. **Azure에 배포**를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 템플릿은 Azure Firewall, 네트워크 인프라 및 두 개의 가상 머신을 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. 포털의 **영역을 사용하여 Azure Firewall의 샌드박스 설정 만들기** 페이지에서 다음 값을 입력하거나 선택합니다.
   - **리소스 그룹**: **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력하고 **확인**을 선택합니다. 
   - **가상 네트워크 이름**: 새 VNet의 이름을 입력합니다.
   - **관리자 사용자 이름**: 관리자 사용자 계정에 대한 사용자 이름을 입력합니다.
   - **관리자 암호**: 관리자 암호를 입력합니다.

3. 사용 약관을 읽은 다음, **위에 명시된 사용 약관에 동의함**을 선택한 다음, **구매**를 선택합니다. 배포를 완료하는 데 10분 이상 걸릴 수 있습니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

방화벽을 통해 만들어진 리소스를 검색합니다.

템플릿에서 방화벽의 JSON 구문 및 속성에 대해 알아보려면 [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 `Remove-AzResourceGroup` PowerShell 명령을 실행하여 리소스 그룹, 방화벽 및 모든 관련 리소스를 제거할 수 있습니다. *MyResourceGroup*이라는 리소스 그룹을 제거하려면 다음을 실행합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

방화벽 모니터링 자습서를 계속 진행하려면 리소스 그룹과 방화벽을 제거하지 마세요. 

## <a name="next-steps"></a>다음 단계

그런 다음, Azure Firewall 로그를 모니터링할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: Azure Firewall 로그 모니터링](tutorial-diagnostics.md)