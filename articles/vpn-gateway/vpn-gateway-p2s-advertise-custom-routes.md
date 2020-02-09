---
title: 'Azure VPN Gateway: P2S VPN 클라이언트에 대 한 사용자 지정 경로 보급'
description: 지점 및 사이트 간 클라이언트에 사용자 지정 경로를 보급 하는 단계
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 7a904857b8aa0ed2aa18fc2a1b81fe31541e6f9e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151899"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>P2S VPN 클라이언트에 대 한 사용자 지정 경로 보급

모든 지점 및 사이트 간 VPN 클라이언트에 사용자 지정 경로를 보급 하는 것이 좋습니다. 예를 들어 VNet에서 저장소 엔드포인트을 사용 하도록 설정 하 고 원격 사용자가 VPN 연결을 통해 이러한 저장소 계정에 액세스할 수 있도록 하려고 합니다. 저장소 계정에 대 한 트래픽이 공용 인터넷이 아닌 VPN 터널을 통과 하도록 저장소 엔드포인트의 IP 주소를 모든 원격 사용자에 게 알릴 수 있습니다.

![Azure VPN Gateway 다중 사이트 연결 예제](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>사용자 지정 경로를 보급 하려면

사용자 지정 경로를 보급 하려면 `Set-AzVirtualNetworkGateway cmdlet`을 사용 합니다. 다음 예에서는 [Contoso 저장소 계정 테이블](https://contoso.table.core.windows.net)의 IP를 보급 하는 방법을 보여 줍니다.

1. *Contoso.table.core.windows.net* 를 Ping 하 고 IP 주소를 적어둡니다. 예를 들어:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 다음 PowerShell 명령을 실행합니다.

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 여러 사용자 지정 경로를 추가 하려면 쉼표가와 공백을 사용 하 여 주소를 구분 합니다. 예를 들어:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>사용자 지정 경로를 보려면

사용자 지정 경로를 보려면 다음 예제를 사용 합니다.

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>사용자 지정 경로를 삭제 하려면

다음 예제를 사용 하 여 사용자 지정 경로를 삭제 합니다.

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>다음 단계

추가 P2S 라우팅 정보는 지점 및 [사이트 간 라우팅](vpn-gateway-about-point-to-site-routing.md)정보를 참조 하세요.
