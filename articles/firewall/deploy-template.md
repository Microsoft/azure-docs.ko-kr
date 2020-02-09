---
title: 템플릿을 사용하여 Azure Firewall 배포
description: 템플릿을 사용 하 여 Azure 방화벽을 배포 합니다. 만든 네트워크에 3 개의 서브넷이 있는 VNet이 하나 있습니다. 2 코어 Windows Server 가상 머신이 배포 됩니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169193"
---
# <a name="deploy-azure-firewall-using-a-template"></a>템플릿을 사용하여 Azure Firewall 배포

[AzureFirewall 샌드박스 설정 만들기 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)은 방화벽을 사용하여 테스트 네트워크 환경을 만듭니다. 네트워크에는 세 개의 서브넷 ( *AzureFirewallSubnet*, *ServersSubnet*및 *JumpboxSubnet*)이 있는 하나의 VNet (가상 네트워크)이 있습니다. *ServersSubnet* 및 *JumpboxSubnet* 서브넷에는 각각 하나의 2개 코어 Windows Server 가상 머신이 있습니다.

방화벽은 *AzureFirewallSubnet* 서브넷에 있고 `www.microsoft.com`에 대한 액세스를 허용 하는 단일 규칙을 포함 하는 응용 프로그램 규칙 컬렉션을 포함 합니다.

사용자 정의 경로는 방화벽 규칙이 적용된 방화벽을 통해 *ServersSubnet* 서브넷에서의 네트워크 트래픽을 가리킵니다.

Azure Firewall에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)을 참조하세요.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>템플릿을 사용하여 Azure Firewall 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

**템플릿을 사용하여 Azure Firewall을 설치 및 배포하려면**

1. [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)에서 템플릿에 액세스합니다.
   
1. 소개 부분을 읽어보고 배포할 준비가 되면 **Azure에 배포**를 선택합니다.
   
1. 필요한 경우 Azure Portal에 로그인합니다. 

1. 포털의 **AzureFirewall의 샌드박스 설정 만들기** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **리소스 그룹**: **새로 만들기**를 선택 하 고, 리소스 그룹의 이름을 입력 하 고, **확인**을 선택 합니다. 
   - **Virtual Network 이름**: 새 VNet의 이름을 입력 합니다. 
   - 관리자 **사용자 이름**: 관리자 사용자 계정의 사용자 이름을 입력 합니다.
   - 관리자 **암호**: 관리자 암호를 입력 합니다. 
   
1. 사용 약관을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.
   
1. **구매**를 선택합니다.
   
   리소스를 만드는 데 몇 분이 걸립니다. 
   
1. 방화벽을 통해 만들어진 리소스를 검색합니다. 

템플릿에서 방화벽의 JSON 구문 및 속성에 대해 알아보려면 [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 경우 [AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell 명령을 실행 하 여 리소스 그룹, 방화벽 및 관련 된 모든 리소스를 제거할 수 있습니다. *MyResourceGroup*이라는 리소스 그룹을 제거하려면 다음을 실행합니다. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
방화벽 모니터링 자습서를 계속 진행하려면 아직 리소스 그룹과 방화벽을 제거하지 마세요. 

## <a name="next-steps"></a>다음 단계

다음으로, Azure Firewall 로그를 모니터링할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
