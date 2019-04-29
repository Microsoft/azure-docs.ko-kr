---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6505b12b35ee436930ba6571c27db30c12030041
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419148"
---
### <a name="gwipnoconnection"></a> 로컬 네트워크 게이트웨이 'GatewayIpAddress'를 수정하려면 - 게이트웨이 연결 없음

연결하려는 VPN 디바이스의 공용 IP 주소가 변경된 경우 해당 변경 내용을 반영하도록 로컬 네트워크 게이트웨이를 수정해야 합니다. 예제를 사용하여 게이트웨이 연결이 없는 로컬 네트워크 게이트웨이를 수정합니다.

이 값을 수정할 때 주소 접두사를 함께 수정할 수도 있습니다. 현재 설정을 덮어쓰려면 로컬 네트워크 게이트웨이의 기존 이름을 사용해야 합니다. 다른 이름을 사용하면 기존 게이트웨이를 덮어쓰지 않고 새 로컬 네트워크 게이트웨이를 만들게 됩니다.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a>로컬 네트워크 게이트웨이 'GatewayIpAddress'를 수정하려면 - 기존 게이트웨이 연결

연결하려는 VPN 디바이스의 공용 IP 주소가 변경된 경우 해당 변경 내용을 반영하도록 로컬 네트워크 게이트웨이를 수정해야 합니다. 게이트웨이 연결이 이미 있는 경우 먼저 연결을 제거해야 합니다. 연결을 제거한 후 게이트웨이 IP 주소를 수정하고 새 연결을 다시 만들 수 있습니다. 이와 동시에 주소 접두사를 수정할 수도 있습니다. 이로 인해 VPN 연결에 약간의 가동 중지 시간이 발생합니다. 게이트웨이 IP 주소를 수정할 때 VPN Gateway를 삭제할 필요가 없습니다. 연결만 제거하면 됩니다.
 

1. 연결을 제거합니다. ‘Get-AzVirtualNetworkGatewayConnection’ cmdlet을 사용하여 연결 이름을 찾을 수 있습니다.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. 'GatewayIpAddress' 값을 수정합니다. 이와 동시에 주소 접두사를 수정할 수도 있습니다. 로컬 네트워크 게이트웨이의 기존 이름으로 현재 설정을 덮어써야 합니다. 이렇게 하지 않으면 기존 게이트웨이를 덮어쓰지 않고 새 로컬 네트워크 게이트웨이를 만들게 됩니다.

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. 연결을 만듭니다. 이 예제에서는 IPsec 연결 형식을 구성합니다. 연결을 다시 만들 때 구성에 대해 지정된 연결 유형을 사용합니다. 추가 연결 형식은 [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 페이지를 참조하세요.  VirtualNetworkGateway 이름을 가져오려면 ‘Get-AzVirtualNetworkGateway’ cmdlet을 실행합니다.
   
    변수를 설정합니다.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    연결을 만듭니다.

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```