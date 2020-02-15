---
title: 'Azure VPN Gateway: 게이트웨이 삭제: PowerShell'
description: Resource Manager 배포 모델에서 PowerShell을 사용하여 가상 네트워크 게이트웨이를 삭제합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162109"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>PowerShell을 사용하여 가상 네트워크 삭제
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell(클래식)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

VPN 게이트웨이 구성에 대한 가상 네트워크 게이트웨이 삭제하려는 경우에 몇 가지 다른 접근 방법을 사용할 수 있습니다.

- 테스트 환경의 경우처럼 모든 항목을 삭제하고 다시 시작하려면 전체 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 그룹 내의 모든 리소스가 삭제됩니다. 이 방법은 리소스 그룹에 리소스를 유지하지 않으려는 경우에만 권장됩니다. 이 방법을 사용할 때는 몇 가지 리소스만 선택적으로 삭제할 수가 없습니다.

- 리소스 그룹에 일부 리소스를 유지하려는 경우 가상 네트워크 게이트웨이를 삭제하는 작업이 좀 더 복잡해집니다. 가상 네트워크 게이트웨이를 삭제하려면 먼저 게이트웨이에 의존하는 모든 리소스를 삭제해야 합니다. 수행하는 단계는 만든 연결의 유형 및 각 연결에 대한 종속 리소스에 따라 달라집니다.

## <a name="before-beginning"></a>시작하기 전에



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. 최신 Azure Resource Manager PowerShell cmdlet을 다운로드 합니다.

최신 버전의 Azure Resource Manager PowerShell cmdlet을 다운로드하고 설치합니다. PowerShell cmdlet 다운로드 및 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

### <a name="2-connect-to-your-azure-account"></a>2. Azure 계정에 연결 합니다.

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

```powershell
Connect-AzAccount
```

계정에 대한 구독을 확인합니다.

```powershell
Get-AzSubscription
```

둘 이상의 구독이 있는 경우 사용할 구독을 지정합니다.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>사이트 간 VPN 게이트웨이 삭제

S2S 구성에 대한 가상 네트워크 게이트웨이를 삭제하려면 먼저 가상 네트워크 게이트웨이와 관련된 각 리소스를 삭제해야 합니다. 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 아래 예제를 사용할 경우 일부 값은 지정되어야 하지만 일부 값은 출력 결과로 나타납니다. 데모를 위해 예제에 다음과 같은 특정 값을 사용합니다.

VNet 이름: VNet1<br>
리소스 그룹 이름: RG1<br>
가상 네트워크 게이트웨이 이름: GW1<br>

다음 단계는 Resource Manager 배포 모델에 적용됩니다.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 삭제할 가상 네트워크 게이트웨이를 가져옵니다.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. 가상 네트워크 게이트웨이에 연결이 있는지 확인 합니다.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. 모든 연결을 삭제 합니다.

각 연결의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. 가상 네트워크 게이트웨이를 삭제 합니다.

게이트웨이의 삭제를 확인하라는 메시지가 표시될 수 있습니다. 이 VNet에 S2S 구성 외에 P2S 구성이 있는 경우 가상 네트워크 게이트웨이를 삭제하면 경고 없이 모든 P2S 클라이언트의 연결이 자동으로 끊어집니다.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

이때 가상 네트워크 게이트웨이가 삭제되었습니다. 다음 단계를 사용하여 더 이상 사용되지 않는 모든 리소스를 삭제할 수 있습니다.

### <a name="5-delete-the-local-network-gateways"></a>5\. 로컬 네트워크 게이트웨이를 삭제합니다.

해당 로컬 네트워크 게이트웨이 목록을 가져옵니다.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

로컬 네트워크 게이트웨이를 삭제합니다. 각 로컬 네트워크 게이트웨이의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. 공용 IP 주소 리소스를 삭제 합니다.

가상 네트워크 게이트웨이의 IP 구성을 가져옵니다.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

이 가상 네트워크 게이트웨이에 사용되는 공용 IP 주소 리소스 목록을 가져옵니다. 가상 네트워크 게이트웨이가 활성-활성인 경우 두 개의 공용 IP 주소가 표시됩니다.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

공용 IP 리소스를 삭제합니다.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. 게이트웨이 서브넷을 삭제 하 고 구성을 설정 합니다.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>VNet 간 VPN 게이트웨이 삭제

V2V 구성에 대한 가상 네트워크 게이트웨이를 삭제하려면 먼저 가상 네트워크 게이트웨이와 관련된 각 리소스를 삭제해야 합니다. 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 아래 예제를 사용할 경우 일부 값은 지정되어야 하지만 일부 값은 출력 결과로 나타납니다. 데모를 위해 예제에 다음과 같은 특정 값을 사용합니다.

VNet 이름: VNet1<br>
리소스 그룹 이름: RG1<br>
가상 네트워크 게이트웨이 이름: GW1<br>

다음 단계는 Resource Manager 배포 모델에 적용됩니다.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 삭제할 가상 네트워크 게이트웨이를 가져옵니다.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. 가상 네트워크 게이트웨이에 연결이 있는지 확인 합니다.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
다른 리소스 그룹의 일부인 가상 네트워크 게이트웨이에 대한 다른 연결이 있을 수 있습니다. 각 추가 리소스 그룹에서 추가 연결을 확인합니다. 이 예제에서 RG2의 연결을 확인합니다. 가상 네트워크 게이트웨이에 연결되어 있을 수 있는 각 리소스 그룹에 대해 이 작업을 실행합니다.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. 양방향으로 연결 목록을 가져옵니다.

VNet 간 구성이기 때문에 양방향 연결 목록이 필요합니다.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
이 예제에서 RG2의 연결을 확인합니다. 가상 네트워크 게이트웨이에 연결되어 있을 수 있는 각 리소스 그룹에 대해 이 작업을 실행합니다.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. 모든 연결을 삭제 합니다.

각 연결의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. 가상 네트워크 게이트웨이를 삭제 합니다.

가상 네트워크 게이트웨이의 삭제를 확인하라는 메시지가 표시될 수 있습니다. VNet에 V2V 구성 외에 P2S 구성이 있는 경우 가상 네트워크 게이트웨이를 삭제하면 경고 없이 모든 P2S 클라이언트의 연결이 자동으로 끊어집니다.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

이때 가상 네트워크 게이트웨이가 삭제되었습니다. 다음 단계를 사용하여 더 이상 사용되지 않는 모든 리소스를 삭제할 수 있습니다.

### <a name="6-delete-the-public-ip-address-resources"></a>6. 공용 IP 주소 리소스를 삭제 합니다.

가상 네트워크 게이트웨이의 IP 구성을 가져옵니다.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

이 가상 네트워크 게이트웨이에 사용되는 공용 IP 주소 리소스 목록을 가져옵니다. 가상 네트워크 게이트웨이가 활성-활성인 경우 두 개의 공용 IP 주소가 표시됩니다.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

공용 IP 리소스를 삭제합니다. 공용 IP의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. 게이트웨이 서브넷을 삭제 하 고 구성을 설정 합니다.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>지점 및 사이트 간 VPN Gateway 삭제

P2S 구성에 대한 가상 네트워크 게이트웨이를 삭제하려면 먼저 가상 네트워크 게이트웨이와 관련된 각 리소스를 삭제해야 합니다. 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 아래 예제를 사용할 경우 일부 값은 지정되어야 하지만 일부 값은 출력 결과로 나타납니다. 데모를 위해 예제에 다음과 같은 특정 값을 사용합니다.

VNet 이름: VNet1<br>
리소스 그룹 이름: RG1<br>
가상 네트워크 게이트웨이 이름: GW1<br>

다음 단계는 Resource Manager 배포 모델에 적용됩니다.


>[!NOTE]
> VPN Gateway를 삭제하면 경고 없이 VNet에서 연결된 모든 클라이언트의 연결이 끊어집니다.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 삭제할 가상 네트워크 게이트웨이를 가져옵니다.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. 가상 네트워크 게이트웨이를 삭제 합니다.

가상 네트워크 게이트웨이의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

이때 가상 네트워크 게이트웨이가 삭제되었습니다. 다음 단계를 사용하여 더 이상 사용되지 않는 모든 리소스를 삭제할 수 있습니다.

### <a name="3-delete-the-public-ip-address-resources"></a>3. 공용 IP 주소 리소스를 삭제 합니다.

가상 네트워크 게이트웨이의 IP 구성을 가져옵니다.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

이 가상 네트워크 게이트웨이에 사용되는 공용 IP 주소 목록을 가져옵니다. 가상 네트워크 게이트웨이가 활성-활성인 경우 두 개의 공용 IP 주소가 표시됩니다.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

공용 IP를 삭제합니다. 공용 IP의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. 게이트웨이 서브넷을 삭제 하 고 구성을 설정 합니다.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>리소스 그룹을 삭제하여 VPN 게이트웨이 삭제

리소스 그룹에 리소스를 유지하지 않고 새로 시작하려는 경우 전체 리소스 그룹을 삭제하면 됩니다. 모든 항목을 제거하는 빠른 방법입니다. 다음 단계는 Resource Manager 배포 모델에만 적용됩니다.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. 구독의 모든 리소스 그룹 목록을 가져옵니다.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. 삭제 하려는 리소스 그룹을 찾습니다.

삭제하려는 리소스 그룹을 찾고 해당 리소스 그룹의 리소스 목록을 확인합니다. 이 예제에서 리소스 그룹의 이름은 RG1입니다. 모든 리소스 목록을 검색하도록 예제를 수정합니다.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. 목록에서 리소스를 확인 합니다.

목록이 반환되면 리소스 그룹의 모든 리소스와 리소스 그룹 자체를 삭제할 것인지도 확인합니다. 리소스 그룹에 일부 리소스를 유지하려면 이 문서에 있는 이전 섹션의 단계를 사용하여 게이트웨이를 삭제합니다.

### <a name="4-delete-the-resource-group-and-resources"></a>4. 리소스 그룹 및 리소스를 삭제 합니다.

리소스 그룹 및 리소스 그룹에 포함된 모든 리소스를 삭제하려면 예제를 수정하고 실행합니다.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. 상태를 확인 합니다.

Azure에서 모든 리소스를 삭제하려면 다소 시간이 소요됩니다. 다음 cmdlet을 사용하여 리소스 그룹의 상태를 확인할 수 있습니다.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

반환되는 결과에는 'Succeeded'가 표시됩니다.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
