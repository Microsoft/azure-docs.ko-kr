---
title: 'Azure Express 경로 템플릿: Express 경로 회로 만들기'
description: Azure PowerShell를 사용 하 여 Azure Resource Manager 템플릿을 배포 하 여 Azure Express 경로 회로를 만드는 방법에 대해 알아봅니다.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: charwen
ms.openlocfilehash: 78318726a658b6e0dc966288f386c65ee4c6f0e4
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190456"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 Express 경로 회로 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager 템플릿](expressroute-howto-circuit-resource-manager-template.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (클래식)](expressroute-howto-circuit-classic.md)
>

Azure PowerShell를 사용 하 여 Azure Resource Manager 템플릿을 배포 하 여 Express 경로 회로를 만드는 방법에 대해 알아봅니다. Resource Manager 탬플릿 개발에 대한 자세한 내용은 [Resource Manager 설명서](/azure/azure-resource-manager/) 및 [템플릿 참조](/azure/templates/microsoft.network/expressroutecircuits)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.
* 새 네트워킹 리소스를 만들 권한이 있는지 확인합니다. 적절한 권한이 없는 경우에는 계정 관리자에게 문의합니다.
* 단계를 더 잘 이해하기 위해 시작 전에 [비디오 보기](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)를 할 수 있습니다.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute 회로 만들기 및 프로비전

[Azure 빠른 시작 템플릿에](https://azure.microsoft.com/resources/templates/) 는 리소스 관리자 템플릿 컬렉션이 있습니다. [기존 템플릿](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) 중 하나를 사용 하 여 express 경로 회로를 만듭니다.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

더 많은 관련 템플릿을 보려면 [여기](https://azure.microsoft.com/resources/templates/?term=expressroute)를 선택 합니다.

템플릿을 배포 하 여 Express 경로 회로를 만들려면 다음을 수행 합니다.

1. 다음 코드 블록에서 **시도** 를 선택 하 고 지침에 따라 Azure Cloud shell에 로그인 합니다.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **SKU 계층** 은 express 경로 회로가 [로컬](expressroute-faqs.md#expressroute-local), 표준 또는 [프리미엄](expressroute-faqs.md#expressroute-premium)인지 여부를 결정 합니다. *Local*, * Standard 또는 *Premium*을 지정할 수 있습니다.
   * **SKU 제품군** 은 청구 유형을 결정 합니다. 데이터 요금제에 대해 *unlimiteddata* 를 지정 하 고 무제한 데이터 요금제의 경우 *unlimiteddata로* 을 지정할 수 있습니다. 청구서 유형을 *Metereddata*에서 *Unlimiteddata*로 변경할 수 있지만, *Unlimiteddata*에서 *Metereddata*로는 변경할 수 없습니다. *로컬* 회로는 *unlimiteddata로* 뿐입니다.
   * **피어링 위치**는 Microsoft와 피어링하는 물리적 위치입니다.

     > [!IMPORTANT]
     > 피어링 위치는 Microsoft와 피어링하는 [물리적 위치](expressroute-locations.md)를 나타냅니다. 이 위치는 Azure Network Resource Provider가 있는 지리적 위치를 참조하는 "Location" 속성에 **연결되지 않습니다** . 이 속성에 연결되지 않는 대신 회로의 피어링 위치와 지리적으로 가까운 네트워크 리소스 공급자를 선택 하는 것이 좋습니다.

    리소스 그룹 이름은 **rg** 추가 된 service bus 네임 스페이스 이름입니다.

2. **복사**를 선택하여 PowerShell 스크립트를 복사합니다.
3. 셸 콘솔 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

이벤트 허브를 만드는 데 몇 분 정도 걸립니다.

이 자습서에서는 Azure PowerShell를 사용 하 여 템플릿을 배포 합니다. 다른 템플릿 배포 방법은 다음을 참조 하세요.

* [Azure Portal를 사용](../azure-resource-manager/templates/deploy-portal.md)합니다.
* [Azure CLI 사용](../azure-resource-manager/templates/deploy-cli.md).
* [REST API 사용](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute 회로 프로비저닝 해제 및 삭제

**삭제** 아이콘을 선택 하 여 express 경로 회로를 삭제할 수 있습니다. 다음 정보에 유의하세요.

* 모든 가상 네트워크를 ExpressRoute 회로에서 연결 해제해야 합니다. 이 작업에 실패한 경우 회로에 연결된 가상 네트워크가 있는지 확인하세요.
* Express 경로 회로 서비스 공급자 프로 비전 상태가 **프로 비전** 또는 **프로 비전** 된 경우 서비스 공급자와 협력 하 여 회로를 프로 비전 해제 해야 합니다. 서비스 공급자가 회로의 프로비전을 해제한 다음 통지를 보낼 때까지 리소스가 계속 예약되며 요금이 청구됩니다.
* 서비스 공급자가 회로를 프로 비전 해제 (서비스 공급자 프로 비전 상태가 **프로 비전 되지 않음**으로 설정 된 경우) 회로를 삭제할 수 있습니다. 그러면 회로에 대한 요금 청구가 중지됩니다.

다음 PowerShell 명령을 실행 하 여 Express 경로 회로를 삭제할 수 있습니다.

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>다음 단계

회로를 만든 후에 다음 단계를 진행합니다.

* [ExpressRoute 회로의 라우팅 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
* [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
