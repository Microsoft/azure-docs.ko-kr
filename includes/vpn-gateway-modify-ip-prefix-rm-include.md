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
ms.openlocfilehash: 13089a2514229c5c5bc7b40d9447719247b23405
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419150"
---
### <a name="noconnection"></a>로컬 네트워크 게이트웨이 IP 주소 접두사를 수정하려면 - 게이트웨이 연결 없음

추가 주소 접두사를 추가하려면:

1. LocalNetworkGateway에 대한 변수를 설정합니다.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. 접두사를 수정합니다.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

주소 접두사를 제거하려면:

  더 이상 필요하지 않은 접두사는 생략합니다. 이 예제에서는 이전 예제의 10.101.2.0/24 접두사가 더 이상 필요하지 않으므로 해당 접두사를 제외하고 로컬 네트워크 게이트웨이를 업데이트합니다.

1. LocalNetworkGateway에 대한 변수를 설정합니다.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. 업데이트된 접두사를 사용하는 게이트웨이를 설정합니다.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```

### <a name="withconnection"></a>로컬 네트워크 게이트웨이 IP 주소 접두사를 수정하려면 - 기존 게이트웨이 연결

게이트웨이 연결이 있고 로컬 네트워크 게이트웨이에 포함된 IP 주소 접두사를 추가 또는 제거하려면 다음 단계를 순서대로 수행해야 합니다. 이로 인해 VPN 연결에 약간의 가동 중지 시간이 발생합니다. IP 주소 접두사를 수정할 때 VPN Gateway를 삭제할 필요가 없습니다. 연결만 제거하면 됩니다.

1. 연결을 제거합니다.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. 수정된 주소 접두사를 사용하는 로컬 네트워크 게이트웨이를 설정합니다.
   
   LocalNetworkGateway에 대한 변수를 설정합니다.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
   
   접두사를 수정합니다.
   
   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
3. 연결을 만듭니다. 이 예제에서는 IPsec 연결 형식을 구성합니다. 연결을 다시 만들 때 구성에 대해 지정된 연결 유형을 사용합니다. 추가 연결 형식은 [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 페이지를 참조하세요.
   
   VirtualNetworkGateway에 대한 변수를 설정합니다.

   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
   ```
   
   연결을 만듭니다. 이 예제에서는 2단계에서 설정한 $local 변수를 사용합니다.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1 -Location 'East US' `
   -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec `
   -RoutingWeight 10 -SharedKey 'abc123'
   ```