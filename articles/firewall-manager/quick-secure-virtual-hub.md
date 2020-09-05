---
title: '빠른 시작: Azure Firewall Manager를 사용하여 가상 허브 보호 - Resource Manager 템플릿'
description: Azure Firewall Manager를 사용하여 가상 허브를 보호하는 방법을 알아봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: bfa957d8fb0eeb8020472026e18c41dc6e65fdc3
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705761"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>빠른 시작: Azure Firewall Manager를 사용하여 가상 허브 보호 - ARM 템플릿

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Firewall Manager를 통해 가상 허브를 보호합니다. 배포된 방화벽에는 `www.microsoft.com`에 대한 연결을 허용하는 애플리케이션 규칙이 있습니다. 방화벽을 테스트하기 위해 두 개의 Windows Server 2019 가상 머신이 배포됩니다. 단일 점프 서버를 사용하여 작업 서버에 연결합니다. 작업 서버에서 `www.microsoft.com`에만 연결할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Firewall Manager에 대한 자세한 내용은 [Azure Firewall Manager란?](overview.md)을 참조하세요.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>템플릿 검토

이 템플릿은 시나리오를 지원하는 데 필요한 리소스와 함께 Azure Firewall Manager를 사용하여 보안 가상 허브를 만듭니다.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/fwm-docs-qs/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

템플릿에는 여러 개의 Azure 리소스가 정의되어 있습니다.

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>템플릿 배포

Azure에 ARM 템플릿을 배포합니다.

1. **Azure에 배포**를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 템플릿은 Azure Firewall, 가상 WAN 및 가상 허브, 네트워크 인프라 및 두 개의 가상 머신을 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. 포털의 **보안 가상 허브** 페이지에서 다음 값을 입력하거나 선택합니다.
   - 구독: 기존 구독에서 선택 
   - 리소스 그룹:  기존 리소스 그룹에서 선택하거나 **새로 만들기**를 선택하고 **확인**을 선택합니다.
   - 위치: 위치 선택
   - 관리자 사용자 이름: 관리자 사용자 계정에 대한 사용자 이름 입력 
   - 관리자 암호: 관리자 암호 또는 키 입력

3. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다. 배포를 완료하는 데 10분 이상 걸릴 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

이제 방화벽 규칙이 예상대로 작동하는지 테스트합니다.

1. Azure Portal에서 **Workload-Srv** 가상 머신에 대한 네트워크 설정을 검토하고 프라이빗 IP 주소를 참고합니다.
2. 원격 데스크톱을 **Jump-Srv** 가상 머신과 연결하고 로그인합니다. 여기에서 원격 데스크톱 연결을 **Workload-Srv** 프라이빗 IP 주소로 엽니다.

3. Internet Explorer를 열고 [https://www.google.com]\(`www.microsoft.com`)을 찾습니다.
4. Internet Explorer 보안 경고에서 **확인** > **닫기**를 선택합니다.

   Microsoft 홈페이지가 표시됩니다.

5. [https://www.microsoft.com]\(`www.google.com`) 로 이동합니다.

   방화벽에서 차단해야 합니다.

이제 방화벽 규칙이 작동하는지 확인했습니다.

- 다른 모든 FQDN이 아닌 허용된 FQDN 하나만 찾아볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

방화벽으로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 방화벽과 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [보안 파트너 공급자에 대해 알아보기](trusted-security-partners.md)
