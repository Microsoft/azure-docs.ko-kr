---
title: 'Azure Resource Manager VNet에 클래식 가상 네트워크를 연결하는 방법: PowerShell | Microsoft Docs'
description: VPN Gateway 및 PowerShell을 사용하여 클래식 VNet 및 Resource Manager VNet 간에 VPN 연결을 만듭니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 2263996b84b17f7de9826c07eb28e4b7668cd915
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095593"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>PowerShell을 사용하여 다양한 배포 모델에서 가상 네트워크 연결

이 아티클에서는 Resource Manager VNet에 클래식 VNet을 연결하여 별도의 배포 모델에 있는 리소스가 서로 통신하도록 허용하는 데 도움을 줍니다. 이 문서의 단계는 PowerShell을 사용하지만 이 목록에서 문서를 선택하여 Azure Portal을 사용하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [포털](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

클래식 VNet을 Resource Manager VNet에 연결하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. 다른 구독 및 다른 지역에 있는 VNet 간에 연결을 만들 수 있습니다. 또한 함께 구성된 게이트웨이가 동적이거나 경로 기반이면 온-프레미스 네트워크에 이미 연결된 VNet을 연결할 수 있습니다. VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 FAQ](#faq)를 참조하세요. 

가상 네트워크 게이트웨이가 아직 없으며 만들지 않으려면 VNet 피어링을 사용하여 VNet을 연결하는 것이 좋습니다. VNet 피어링은 VPN Gateway를 사용하지 않습니다. 자세한 내용은 [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

## <a name="before"></a>시작하기 전에

다음 단계에서는 각 VNet에 대한 동적 또는 경로 기반 게이트웨이를 구성하고 게이트웨이 간에 VPN 연결을 만드는 데 필요한 설정을 안내합니다. 이 구성은 고정 또는 정책 기반 게이트웨이를 지원하지 않습니다.

### <a name="pre"></a>필수 조건

* 두 VNet이 이미 생성되었습니다. Resource Manager 가상 네트워크를 만들어야 하는 경우 [리소스 그룹 및 가상 네트워크 만들기](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network)를 참조하세요. 클래식 가상 네트워크를 만들려면 [클래식 VNet 만들기](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic)를 참조하세요.
* VNet에 대한 주소 범위는 서로 겹치거나 게이트웨이가 연결되어 있는 다른 연결의 범위와 겹치지 않습니다.
* 최신 PowerShell cmdlet을 설치했습니다. 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 을 참조하세요. SM(서비스 관리)와 RM(Resource Manager) cmdlet을 모두 설치해야 합니다. 

### <a name="exampleref"></a>예제 설정

이러한 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다.

**클래식 VNet 설정**

VNet 이름 = ClassicVNet  <br>
Location = West US <br>
Virtual Network Address Spaces = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Local Network Name = RMVNetLocal <br>
 GatewayType = DynamicRouting

**Resource Manager VNet 설정**

VNet 이름 = RMVNet  <br>
Resource Group = RG1 <br>
Virtual Network IP Address Spaces = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Location = East US <br>
Gateway public IP name = gwpip <br>
Local Network Gateway = ClassicVNetLocal <br>
Virtual Network Gateway name = RMGateway <br>
 게이트웨이 IP 주소 구성 = gwipconfig

## <a name="createsmgw"></a>섹션 1 - 클래식 VNet 구성
### <a name="1-download-your-network-configuration-file"></a>1. 네트워크 구성 파일 다운로드
1. 관리자 권한으로 PowerShell 콘솔에서 Azure 계정에 로그인합니다. 다음 cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다. 이 섹션에서는 클래식 SM(Service Management) Azure PowerShell cmdlet이 사용됩니다.

   ```azurepowershell
   Add-AzureAccount
   ```

   Azure 구독을 가져옵니다.

   ```azurepowershell
   Get-AzureSubscription
   ```

   둘 이상의 구독이 있는 경우 사용할 구독을 선택합니다.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. 다음 명령을 실행하여 Azure 네트워크 구성 파일을 내보냅니다. 필요한 경우 다른 위치로 내보낼 파일의 위치를 변경할 수 있습니다.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. 다운로드한 .xml 파일을 열고 편집합니다. 네트워크 구성 파일의 예는 [네트워크 구성 스키마](https://msdn.microsoft.com/library/jj157100.aspx)를 참조하세요.

### <a name="2-verify-the-gateway-subnet"></a>2. 게이트웨이 서브넷 확인
**VirtualNetworkSites**요소에 게이트웨이 서브넷을 아직 만들지 않은 경우 VNet에 추가합니다. 네트워크 구성 파일로 작업할 때 게이트웨이 서브넷의 이름은 "GatewaySubnet"이어야 합니다. 또는 Azure에서 게이트웨이 서브넷으로 인식하고 사용할 수 없습니다.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**예제:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. 로컬 네트워크 사이트 추가
추가한 로컬 네트워크 사이트는 연결하려는 RM VNet을 나타냅니다. 존재하지 않는 경우 파일에 **LocalNetworkSites** 요소를 추가합니다. 구성의 이 시점에서 Resource Manager VNet에 대한 게이트웨이 아직 만들지 않았기 때문에 VPNGatewayAddress는 유효한 공용 IP 주소일 수 있습니다. 게이트웨이를 만들고 나면 이 자리 표시자 IP 주소를 RM 게이트웨이에 할당된 올바른 공용 IP 주소로 바꿉니다.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. 로컬 네트워크 사이트와 VNet 연결
이 섹션에서는 VNet을 연결하려는 로컬 네트워크 사이트를 지정합니다. 이 경우에 앞서 참조한 Resource Manager VNet입니다. 이름이 일치해야 합니다. 이 단계에서는 게이트웨이를 만들지 않습니다. 게이트웨이를 연결할 로컬 네트워크를 지정합니다.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. 파일 저장 및 업로드
파일을 저장하고 다음 명령을 실행하여 Azure에 가져옵니다. 사용자 환경에 필요한 대로 파일 경로를 변경해야 합니다.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

가져오기가 성공했음을 보여 주는 유사한 결과가 표시됩니다.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. 게이트웨이 만들기

이 예제를 실행하기 전에 Azure에 필요한 정확한 이름에 대해서는 다운로드한 네트워크 구성 파일을 참조하세요. 네트워크 구성 파일에는 클래식 가상 네트워크에 대한 값이 포함되어 있습니다. 배포 모델의 차이 때문에 Azure Portal에서 클래식 Vnet 설정을 만들 때 네트워크 구성 파일에서 클래식 Vnet의 이름이 변경되는 경우가 있습니다. 예를 들어 Azure Portal을 사용하여 'Classic VNet'이라는 클래식 VNet을 만들고 'ClassicRG'라는 리소스 그룹에 만들면 네트워크 구성 파일에 포함된 이름은 'Group ClassicRG Classic VNet'으로 변환됩니다. 공백이 포함된 VNet의 이름을 지정할 때는 값을 따옴표로 묶습니다.


다음 예제를 사용하여 동적 라우팅 게이트웨이를 만듭니다.

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

**Get-AzureVNetGateway** cmdlet을 사용하여 게이트웨이의 상태를 확인할 수 있습니다.

## <a name="creatermgw"></a>섹션 2 - RM VNet 게이트웨이 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

RM VNet을 이미 만들었다고 가정합니다. 이 단계에서는 RM VNet에 대한 VPN Gateway를 만듭니다. 클래식 VNet의 게이트웨이에 대한 공용 IP 주소를 검색할 때까지 이 단계를 시작하지 않습니다. 

1. PowerShell 콘솔에서 Azure 계정에 로그인합니다. 다음 cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음, Azure PowerShell에 사용할 수 있도록 계정 설정이 다운로드됩니다. 필요에 따라 "Try It" 기능을 사용하여 브라우저에서 Azure Cloud Shell을 시작할 수 있습니다.

   Azure Cloud Shell을 사용하는 경우 다음 cmdlet을 건너뜁니다.

   ```azurepowershell
   Connect-AzAccount
   ``` 
   올바른 구독을 사용하고 있는지 확인하려면 다음 cmdlet을 실행합니다.  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   둘 이상의 구독이 있는 경우 사용할 구독을 지정합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. 로컬 네트워크 게이트웨이를 만듭니다. 가상 네트워크에서 로컬 네트워크 게이트웨이는 일반적으로 온-프레미스 위치를 가리킵니다. 이 경우에 로컬 네트워크 게이트웨이는 클래식 VNet을 가리킵니다. Azure에서 참조할 수 있는 이름을 지정하고 주소 공간 접두사를 지정합니다. Azure는 지정된 IP 주소 접두사를 사용하여 온-프레미스 위치로 보낼 트래픽을 식별합니다. 나중에 게이트웨이를 만들기 전에 여기서 정보를 조정하는 경우 값을 수정하고 샘플을 다시 실행할 수 있습니다.
   
   **-Name**은 로컬 네트워크 게이트웨이에 참조하기 위해 할당하려는 이름입니다.<br>
   **-AddressPrefix**는 클래식 VNet에 대한 주소 공간입니다.<br>
   **-GatewayIpAddress**는 클래식 VNet 게이트웨이의 공용 IP 주소입니다. 다음 샘플 텍스트 "n.n.n.n"을 올바른 IP 주소를 반영하도록 변경해야 합니다.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. 리소스 관리자 VNet에 대한 가상 네트워크 게이트웨이에 할당할 공용 IP 주소를 요청합니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 가상 네트워크 게이트웨이에 동적으로 할당됩니다. 그러나 이로 인해 IP 주소가 변경되지는 않습니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 가상 네트워크 게이트웨이 IP 주소가 변경됩니다. 크기 조정, 다시 설정 또는 게이트웨이의 기타 내부 유지 관리/업그레이드를 변경하지 않습니다.

   이 단계에서는 이후 단계에 사용되는 변수도 설정합니다.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. 가상 네트워크에 게이트웨이 서브넷이 있는지 확인합니다. 게이트웨이 서브넷이 없다면 추가합니다. 게이트웨이 서브넷의 이름을 *GatewaySubnet*으로 지정해야 합니다.
5. 다음 명령을 실행하여 게이트웨이에 사용된 서브넷을 검색 합니다. 이 단계에서는 다음 단계에서 사용할 변수도 설정합니다.
   
   **-Name**은 리소스 관리자 VNet의 이름입니다.<br>
   **-ResourceGroupName**은 VNet과 연결된 리소스 그룹입니다. 제대로 작동하려면 게이트웨이 서브넷이 이 VNet에 이미 존재하고 이름을 *GatewaySubnet* 으로 지정해야 합니다.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. 게이트웨이 IP 주소 지정 구성을 만듭니다. 게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 다음 샘플을 사용하여 게이트웨이 구성을 만듭니다.

   **-SubnetId** 및 **-PublicIpAddressId** 매개 변수는 각각 서브넷 및 IP 주소 개체에서 id 속성이 전달되어야 합니다. 간단한 문자열을 사용할 수 없습니다. 이러한 변수는 공용 IP를 요청하는 단계 및 서브넷을 검색하는 단계에서 설정됩니다.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. 리소스 관리자 가상 네트워크 게이트웨이를 만듭니다. `-VpnType` 는 *RouteBased*여야 합니다. 게이트웨이를 만드는 데에는 45분 이상이 걸릴 수 있습니다.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. VPN 게이트웨이가 만들어지면 공용 IP 주소를 복사합니다. 클래식 VNet에 대한 로컬 네트워크 설정을 구성할 때 사용합니다. 다음 cmdlet을 사용하여 공용 IP 주소를 검색할 수 있습니다. 공용 IP 주소가 *IpAddress*로 반환에 나열됩니다.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="localsite"></a>섹션 3 - 클래식 VNet 로컬 사이트 설정 수정

이 섹션에서는 클래식 VNet을 사용합니다. 리소스 관리자 VNet 게이트웨이에 연결하는 데 사용될 로컬 사이트 설정을 지정할 때 사용한 자리 표시자 IP 주소를 바꿉니다. 클래식 VNet으로 작업하므로 Azure Cloud Shell TryIt이 아닌 컴퓨터에 로컬로 설치된 PowerShell을 사용해야 합니다.

1. 네트워크 구성 파일을 내보냅니다.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. 텍스트 편집기를 사용하여 VPNGatewayAddress에 대한 값을 수정합니다. 자리 표시자 IP 주소를 Resource Manager 게이트웨이의 공용 IP 주소로 바꾼 후 변경 내용을 저장합니다.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. 수정된 네트워크 구성 파일을 Azure로 가져옵니다.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="connect"></a>섹션 4 - 게이트웨이 간의 연결 만들기
게이트웨이 간의 연결을 만들려면 PowerShell이 필요합니다. 클래식 버전의 PowerShell cmdlet을 사용하려면 Azure 계정을 추가해야 합니다. 이 작업에는 **Add-AzureAccount**를 사용합니다.

1. PowerShell 콘솔에서 공유 키를 설정합니다. 이러한 cmdlet을 실행하기 전에 Azure에 필요한 정확한 이름에 대해서는 다운로드한 네트워크 구성 파일을 참조하세요. 공백이 포함된 VNet의 이름을 지정할 때는 값을 작은따옴표로 묶습니다.<br><br>다음 예제에서 **-VNetName**은 클래식 VNet의 이름이고 **-LocalNetworkSiteName**은 로컬 네트워크 사이트에 대해 지정한 이름입니다. **-SharedKey**는 사용자가 생성하고 지정하는 값입니다. 이 예제에서는 'abc123'을 사용했으나 좀 더 복잡한 항목을 생성하여 사용할 수 있습니다. 중요한 점은 여기에서 지정한 값이 연결을 만드는 다음 단계에서 지정한 것과 동일한 값이어야 한다는 것입니다. 반환 값에는 **상태: 성공**이 표시됩니다.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. 다음 명령을 실행하여 VPN 연결을 만듭니다.
   
   변수를 설정합니다.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   연결을 만듭니다. **-ConnectionType**은 Vnet2Vnet이 아닌 IPsec입니다.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="verify"></a>섹션 5 - 연결 확인

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>클래식 VNet에서 Resource Manager VNet으로의 연결을 확인하려면

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Resource Manager VNet에서 클래식 VNet으로의 연결을 확인하려면

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>VNet 간 FAQ

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
