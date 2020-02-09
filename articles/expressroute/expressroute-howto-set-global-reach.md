---
title: 'Azure Express 경로: Global Reach 구성'
description: 이 문서는 온-프레미스 네트워크 간의 프라이빗 네트워크를 설정하고 Global Reach를 사용하도록 설정하기 위해 ExpressRoute 회로를 함께 연결하는 데 유용합니다.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083456"
---
# <a name="configure-expressroute-global-reach"></a>ExpressRoute Global Reach 구성

이 문서는 PowerShell을 사용하여 ExpressRoute Global Reach를 구성하는 데 도움이 됩니다. 자세한 내용은 [ExpressRoute Global Reach](expressroute-global-reach.md)를 참조하세요.

 ## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음을 확인합니다.

* Express 경로 회로 프로 비전 [워크플로](expressroute-workflows.md)를 이해 합니다.
* Express 경로 회로는 프로 비전 된 상태입니다.
* Azure 개인 피어 링은 Express 경로 회로에서 구성 됩니다.
* PowerShell을 로컬로 실행 하려면 최신 버전의 Azure PowerShell이 컴퓨터에 설치 되어 있는지 확인 합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>회로 식별

1. 구성을 시작 하려면 Azure 계정에 로그인 하 고 사용 하려는 구독을 선택 합니다.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. 사용할 Express 경로 회로를 식별 합니다. 지원 되는 국가/지역에 있고 서로 다른 피어 링 위치에서 생성 된 경우 두 개의 Express 경로 회로 간에 Express 경로 Global Reach를 사용 하도록 설정할 수 있습니다. 

   * 사용자 구독이 두 회로 모두를 소유하는 경우 다음 섹션에서 구성을 실행할 회로를 하나 선택할 수 있습니다.
   * 두 회로가 다른 Azure 구독에 있는 경우 하나의 Azure 구독에서 권한을 부여해야 합니다. 그런 후 다른 Azure 구독에서 구성 명령을 실행할 때 권한 부여 키를 제공합니다.

## <a name="enable-connectivity"></a>연결 설정

온-프레미스 네트워크 간의 연결을 사용 하도록 설정 합니다. 동일한 Azure 구독에 있는 회로와 다른 구독의 회로에 대한 별도의 지침 집합이 있습니다.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>동일한 Azure 구독의 Express 경로 회로

1. 다음 명령을 사용하여 회로 1 및 회로 2를 가져옵니다. 두 회로는 같은 구독에 있습니다.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. 회로 1에서 다음 명령을 실행하고 회로 2의 프라이빗 피어링 ID를 전달합니다. 이 명령을 실행할 때 다음에 유의합니다.

   * 프라이빗 피어링 ID는 다음 예제와 비슷합니다. 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix*는 /29 IPv4 서브넷이어야 합니다(예: "10.0.0.0/29"). 두 ExpressRoute 회로 간의 연결을 설정하는 데 이 서브넷의 IP 주소가 사용됩니다. Azure Virtual Network 또는 온-프레미스 네트워크에서 이 서브넷의 주소를 사용하면 안 됩니다.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. 회로 1에서 다음과 같이 구성을 저장합니다.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

이전 작업이 완료 되 면 두 개의 Express 경로 회로를 통해 온-프레미스 네트워크와 양쪽의 연결을 갖게 됩니다.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>서로 다른 Azure 구독의 ExpressRoute 회로

두 회로가 동일한 Azure 구독에 있지 않으면 권한 부여가 필요합니다. 다음 구성에서는 회로 2의 구독에서 권한 부여가 생성되고 권한 부여 키가 회로 1에 전달됩니다.

1. 권한 부여 키를 생성합니다.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   회로 2의 프라이빗 피어링 ID와 권한 부여 키를 기록해 둡니다.
2. 회로 1에서 다음 명령을 실행합니다. 회로 2의 프라이빗 피어링 ID와 권한 부여 키를 제공합니다.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. 회로 1에서 구성을 저장합니다.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

이전 작업이 완료 되 면 두 개의 Express 경로 회로를 통해 온-프레미스 네트워크와 양쪽의 연결을 갖게 됩니다.

## <a name="verify-the-configuration"></a>구성 확인

구성이 수행된 회로(예를 들어, 이전 예제의 회로 1)에서 구성을 확인하려면 다음 명령을 사용합니다.
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

PowerShell에서 *$ckt1*만 실행하는 경우 *CircuitConnectionStatus*가 출력됩니다. 연결이 설정되면 "연결됨", 설정되지 않으면 "연결 끊김"이 표시됩니다. 

## <a name="disable-connectivity"></a>연결 해제

온-프레미스 네트워크 간의 연결을 사용 하지 않도록 설정 하려면 구성이 이루어진 회로에 대해 명령을 실행 합니다 (예: 이전 예제의 회로 1).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

가져오기 작업을 실행하여 상태를 확인할 수 있습니다.

이전 작업이 완료되면 사용자의 ExpressRoute 회로를 통해 온-프레미스 네트워크 간에 연결이 해제됩니다.

## <a name="next-steps"></a>다음 단계
1. [ExpressRoute Global Reach에 대해 자세히 알아봅니다.](expressroute-global-reach.md)
2. [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
3. [Azure Virtual Network에 ExpressRoute 회로 연결](expressroute-howto-linkvnet-arm.md)
