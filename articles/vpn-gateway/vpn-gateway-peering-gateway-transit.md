---
title: 가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성
description: 가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: 3917101ee7ac151cf624e5be0f51ccf01c8cb1cc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161888"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성

이 문서는 가상 네트워크 피어링을 위한 게이트웨이 전송을 구성하는 데 유용합니다. [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)은 두 Azure 가상 네트워크를 원활하게 연결하여 연결 목적을 위해 두 개의 가상 네트워크를 하나로 병합합니다. [게이트웨이 전송](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)은 크로스-프레미스 또는 VNet 대 VNet 연결에 대한 피어링된 가상 네트워크에서 VPN 게이트웨이를 활용하도록 하나의 가상 네트워크를 사용하도록 설정하는 피어링 속성입니다. 다음 다이어그램은 가상 네트워크 피어링을 통한 게이트웨이 전송의 작동 방식을 보여 줍니다.

![게이트웨이 전송](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

다이어그램에서 피어링된 가상 네트워크는 게이트웨이 전송을 통해 Hub-RM에서 Azure VPN 게이트웨이를 사용할 수 있습니다. S2S, P2S 및 VNet 대 VNet 연결을 포함한 VPN 게이트웨이에서 가능한 연결은 세 개의 가상 네트워크 모두에 적용됩니다. 전송 옵션은 같거나 다른 배포 모델 간의 피어링에 사용할 수 있습니다. 제약 조건은 다이어그램에 표시된 대로 VPN 게이트웨이는 Resource Manager 배포 모델을 사용하여 가상 네트워크에만 있을 수 있다는 점입니다.

허브 및 스포크 네트워크 아키텍처에서 스포크 가상 네트워크는 게이트웨이 전송을 통해 모든 스포크 가상 네트워크에서 VPN 게이트웨이를 배포하는 대신 허브에서 VPN 게이트웨이를 공유할 수 있습니다. 게이트웨이에 연결된 가상 네트워크 또는 온-프레미스 네트워크에 대한 경로는 게이트웨이 전송을 사용하여 피어링된 가상 네트워크에 대한 라우팅 테이블에 전파됩니다. VPN 게이트웨이에서 자동 경로 전파를 비활성화할 수 있습니다. “**BGP 경로 전파를 사용하지 않도록 설정**” 옵션을 선택한 라우팅 테이블을 만들고, 경로 테이블을 서브넷에 연결하여 해당 서브넷에 대한 경로 배포를 방지합니다. 자세한 내용은 [Virtual Network 라우팅 테이블](../virtual-network/manage-route-table.md)을 참조하세요.

이 문서에서 설명하는 두 가지 시나리오는 다음과 같습니다.

1. 두 가상 네트워크는 Resource Manager 배포 모델을 사용합니다.
2. 스포크 가상 네트워크는 클래식이며, 게이트웨이를 사용하는 허브 가상 네트워크는 Resource Manager에 있습니다.

## <a name="requirements"></a>요구 사항



이 문서의 예제에서는 다음 리소스를 만들어야 합니다.

1. VPN 게이트웨이를 사용하는 허브-RM 가상 네트워크
2. 스포크-RM 가상 네트워크
3. 클래식 배포 모델을 사용하는 스포크-클래식 가상 네트워크
4. 사용자가 사용하는 계정에는 필요한 역할 및 권한이 있어야 합니다. 자세한 내용은 이 문서의 [권한](#permissions) 섹션을 참조하세요.

지침은 다음 문서를 참조하세요.

1. [가상 네트워크에서 VPN 게이트웨이 만들기](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [동일한 배포 모델로 가상 네트워크 피어링 만들기](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [다른 배포 모델로 가상 네트워크 피어링 만들기](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a>권한

가상 네트워크 피어링을 만드는 데 사용하는 계정에는 필요한 역할 또는 권한이 있어야 합니다. 아래 예에서 허브-RM 및 스포크-클래식이라는 두 가상 네트워크를 피어링하는 경우 계정에는 각 가상 네트워크에 대한 다음과 같은 역할 또는 권한이 있어야 합니다.
    
|가상 네트워크|배포 모델|역할|사용 권한|
|---|---|---|---|
|허브-RM|리소스 관리자|[네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |클래식|[클래식 네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|해당 없음|
|스포크-클래식|리소스 관리자|[네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||클래식|[클래식 네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 및 [사용자 지정 역할](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 권한 할당(Resource Manager만 해당)에 대해 자세히 알아보세요.

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>게이트웨이 전송으로 Resource Manager 대 Resource Manager 피어링

지침에 따라 가상 네트워크 피어링을 만들고 게이트웨이 전송을 활성화하도록 업데이트합니다.

1. Azure Portal에서 스포크-RM에서 허브-RM으로 가상 네트워크 피어링을 만들고 업데이트합니다. 스포크-RM 가상 네트워크 리소스로 이동하고 “피어링”을 클릭한 다음, “추가”를 클릭합니다.
    - “Resource Manager” 옵션을 설정합니다.
    - 해당하는 구독에서 허브-RM 가상 네트워크를 선택합니다.
    - “가상 네트워크 액세스 허용”이 “사용”으로 설정되어 있는지 확인합니다.
    - “**원격 게이트웨이 사용**” 옵션을 설정합니다.
    - “확인”을 클릭합니다.

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. 피어링을 이미 만든 경우 피어링 리소스로 이동한 다음, 단계 (1)에 표시된 스크린샷과 유사하게 “**원격 게이트웨이 사용**” 옵션을 사용하도록 설정합니다.

3. Azure Portal에서 허브-RM에서 스포크-RM으로 가상 네트워크 피어링을 만들고 업데이트합니다. 허브-RM 가상 네트워크 리소스로 이동하고 “피어링”을 클릭한 다음, “추가”를 클릭합니다.
    - “Resource Manager” 옵션을 설정합니다.
    - “가상 네트워크 액세스 허용”이 “사용”으로 설정되어 있는지 확인합니다.
    - 해당하는 구독에서 “스포크-RM” 가상 네트워크를 선택합니다.
    - “**게이트웨이 전송 허용**” 옵션을 설정합니다.
    - “확인”을 클릭합니다.

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. 피어링을 이미 만든 경우 피어링 리소스로 이동한 다음, 단계 (3)에 표시된 스크린샷과 유사하게 “**게이트웨이 전송 허용**” 옵션을 사용하도록 설정합니다.

5. 두 가상 네트워크 모두에서 피어링 상태가 “**연결됨**”인지 확인합니다.

### <a name="powershell-sample"></a>PowerShell 샘플

또한 위의 예제에서 PowerShell을 사용하여 피어링을 만들거나 업데이트할 수 있습니다. 변수를 사용자의 가상 네트워크 및 리소스 그룹의 이름으로 바꿉니다.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>게이트웨이 전송으로 클래식 대 Resource Manager 피어링

작업이 허브-RM 가상 네트워크에만 적용되는 점 외에는 Resource Manager 예제와 단계가 비슷합니다.

1. Azure Portal에서 허브-RM에서 스포크-RM으로 가상 네트워크 피어링을 만들고 업데이트합니다. 허브-RM 가상 네트워크 리소스로 이동하고 “피어링”을 클릭한 다음, “추가”를 클릭합니다.
   - 가상 네트워크 배포 모델에 대한 “클래식” 옵션을 설정합니다.
   - 해당하는 구독에서 “스포크-클래식” 가상 네트워크를 선택합니다.
   - “가상 네트워크 액세스 허용”이 “사용”으로 설정되어 있는지 확인합니다.
   - “**게이트웨이 전송 허용**” 옵션을 설정합니다.
   - “확인”을 클릭합니다.

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. 피어링을 이미 만든 경우 피어링 리소스로 이동한 다음, 단계 (1)에 표시된 스크린샷과 유사하게 “**게이트웨이 전송 허용**” 옵션을 사용하도록 설정합니다.

3. 스포크-클래식 가상 네트워크에 대한 작업이 없습니다.

4. 허브-RM 가상 네트워크에서 피어링 상태가 “**연결됨**”인지 확인합니다.

상태가 “연결됨”으로 표시되면 스포크 가상 네트워크는 허브 가상 네트워크에서 VPN 게이트웨이 통해 VNet 대 VNet 또는 크로스-프레미스 연결을 사용하여 시작할 수 있습니다.

### <a name="powershell-sample"></a>PowerShell 샘플

또한 위의 예제에서 PowerShell을 사용하여 피어링을 만들거나 업데이트할 수 있습니다. 변수 및 구독 ID를 사용자의 가상 네트워크와 리소스 그룹 및 구독 값으로 바꿉니다. 허브 가상 네트워크에서 가상 네트워크 피어링을 만들기만 하면 됩니다.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>다음 단계

* 프로덕션 환경에 사용하기 위한 가상 네트워크 피어링을 만들기 전에 [가상 네트워크 피어링 제약 조건 및 동작](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) 및 [가상 네트워크 피어링 설정](../virtual-network/virtual-network-manage-peering.md#create-a-peering)에 대해 자세히 알아봅니다.
* 가상 네트워크 피어링 및 게이트웨이 전송을 통해 [허브 및 스포크 네트워크 토폴로지를 만드는](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) 방법을 알아봅니다.
