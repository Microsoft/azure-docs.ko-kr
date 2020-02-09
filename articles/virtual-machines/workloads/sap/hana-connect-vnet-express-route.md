---
title: 가상 네트워크에서 Azure (large instances)의 SAP HANA로 설정 되는 연결 | Microsoft Docs
description: Azure (large instances)에서 SAP HANA를 사용 하도록 가상 네트워크에서 설정 되는 연결입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fbcc6ace4923d8aa2d938cd9fffe7a16c4fc1ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206737"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>가상 네트워크를 HANA 대규모 인스턴스에 연결

Azure 가상 네트워크를 만든 후 해당 네트워크를 Azure 대규모 인스턴스의 SAP HANA에 연결합니다. 가상 네트워크에 Azure ExpressRoute 게이트웨이를 만듭니다. 이 게이트웨이를 사용 하면 HANA Large Instance 스탬프에서 고객 테넌트에 연결 하는 Express 경로 회로에 가상 네트워크를 연결할 수 있습니다.

> [!NOTE] 
> 이 단계를 완료하려면 최대 30분이 걸릴 수 있습니다. 새 게이트웨이는 지정된 Azure 구독에 만들어진 다음, 지정된 Azure 가상 네트워크에 연결됩니다.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

게이트웨이가 이미 있는 경우 ExpressRoute 게이트웨이인지 여부를 확인합니다. Express 경로 게이트웨이가 아닌 경우 게이트웨이를 삭제 하 고 Express 경로 게이트웨이로 다시 만듭니다. ExpressRoute 게이트웨이가 이미 설정된 경우 이 문서의 다음 섹션 "가상 네트워크 연결"을 참조하세요. 

- [Azure Portal](https://portal.azure.com/) 또는 PowerShell을 사용하여 가상 네트워크에 연결된 ExpressRoute VPN 게이트웨이를 만듭니다.
  - Azure Portal을 사용하는 경우 새 **가상 네트워크 게이트웨이**를 추가한 다음, 게이트웨이 유형으로 **ExpressRoute**를 선택합니다.
  - PowerShell을 사용하는 경우 먼저 최신 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)를 다운로드하여 사용합니다. 
 
다음 명령은 ExpressRoute 게이트웨이를 만듭니다. _$_ 뒤에 오는 텍스트는 특정 정보로 업데이트 해야 하는 사용자 정의 변수입니다.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

이 예제에서는 HighPerformance 게이트웨이 SKU를 사용했습니다. 옵션은 Azure(대규모 인스턴스)의 SAP HANA에 대해 지원되는 유일한 게이트웨이 SKU인 HighPerformance 또는 UltraPerformance입니다.

> [!IMPORTANT]
> Type II 클래스 SKU의 HANA 대규모 인스턴스의 경우, UltraPerformance 게이트웨이 SKU를 사용해야 합니다.

## <a name="link-virtual-networks"></a>가상 네트워크 연결

이제 Azure 가상 네트워크에 ExpressRoute 게이트웨이가 만들어졌습니다. Microsoft에서 제공 하는 권한 부여 정보를 사용 하 여 Express 경로 게이트웨이를 SAP HANA(대규모 인스턴스) Express 경로 회로에 연결 합니다. Azure Portal 또는 PowerShell을 사용하여 연결할 수 있습니다. PowerShell 지침은 다음과 같습니다. 

각 연결에 대해 다른 AuthGUID를 사용 하 여 각 Express 경로 게이트웨이에 대해 다음 명령을 실행 합니다. 다음 스크립트에 표시된 처음 두 항목은 Microsoft에서 제공하는 정보에서 가져옵니다. 또한 AuthGUID는 모든 가상 네트워크 및 해당 게이트웨이에 지정됩니다. 다른 Azure 가상 네트워크를 추가 하려는 경우 HANA large instances를 Microsoft의 Azure에 연결 하는 Express 경로 회로에 대 한 다른 AuthID를 가져와야 합니다. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** 명령의 마지막 매개 변수는 Express 경로 빠른 경로를 사용 하는 새로운 매개 변수입니다. HANA Large Instance 단위와 Azure Vm 간의 네트워크 대기 시간을 줄이는 기능입니다. 이 기능은 5 월 2019에 추가 되었습니다. 자세한 내용은 [SAP HANA (Large Instances) 네트워크 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)문서를 참조 하세요. 명령을 실행 하기 전에 최신 버전의 PowerShell cmdlet을 실행 하 고 있는지 확인 합니다.

게이트웨이를 구독과 연결된 두 개 이상의 ExpressRoute 회로에 연결하려면 이 단계를 두 번 이상 실행해야 할 수 있습니다. 예를 들어 가상 네트워크를 온-프레미스 네트워크에 연결하는 ExpressRoute 회로에 동일한 가상 네트워크 게이트웨이를 연결해야 할 가능성이 높습니다.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>기존 HANA Large Instance Express 경로 회로에 Express 경로 빠른 경로 적용
지금까지 살펴본 문서에서는 HANA Large Instance 배포를 사용 하 여 만든 새 Express 경로 회로를 Azure 가상 네트워크 중 하나의 Azure Express 경로 게이트웨이에 연결 하는 방법을 설명 했습니다. 하지만 많은 고객이 이미 Express 경로 회로를 설정 하 고 해당 가상 네트워크를 HANA Large Instances에 이미 연결 했습니다. 새로운 Express 경로 빠른 경로를 사용 하 여 네트워크 대기 시간을 줄일 수 있으므로이 기능을 사용 하기 위해 변경 내용을 적용 하는 것이 좋습니다. 새 ExpreesRoute 회로를 연결 하 고 기존 Express 경로 회로를 변경 하는 명령은 동일 합니다. 따라서 사용 하도록 기존 회로를 변경 하려면이 일련의 PowerShell 명령을 실행 해야 합니다. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

위 표시 된 마지막 매개 변수를 추가 하 여 Express 경로 빠른 경로 기능을 사용 하도록 설정 하는 것이 중요 합니다.


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
두 가지 시나리오 중 하나 또는 둘 다에 대해 Global Reach를 사용 하도록 설정 합니다.

 - 추가 프록시 또는 방화벽이 없는 HANA 시스템 복제
 - 시스템 복사본 또는 시스템 새로 고침을 수행 하기 위해 서로 다른 두 지역의 HANA 대량 인스턴스 단위 간 백업 복사

다음 사항을 고려해 야 합니다.

- /29 주소 공간의 주소 공간 범위를 제공 해야 합니다. 이 주소 범위는 HANA Large Instances를 Azure에 연결 하는 데 사용한 다른 주소 공간 범위와 겹칠 수 없으며, Azure 또는 온-프레미스의 다른 곳에서 사용한 IP 주소 범위와 겹칠 수 없습니다.
- HANA 큰 인스턴스에 대 한 온-프레미스 경로를 보급 하는 데 사용할 수 있는 ASNs (자치 시스템 번호)에는 제한이 있습니다. 온-프레미스에서 65000 – 65020 또는 65515 범위의 개인 ASNs를 사용 하 여 경로를 알리지 않아야 합니다. 
- HANA Large instances에 대 한 온-프레미스 직접 액세스를 연결 하는 시나리오의 경우 Azure에 연결 하는 회로에 대 한 요금을 계산 해야 합니다. 가격에 대해서는 [Global Reach 추가 기능](https://azure.microsoft.com/pricing/details/expressroute/)에 대 한 가격을 확인 하세요.

배포에 적용 되는 시나리오 중 하나 또는 둘 다를 가져오려면 [HANA Large Instances에 대 한 지원 요청 열기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances) 에 설명 된 대로 Azure에서 지원 메시지를 엽니다.

Microsoft에서 요청에 대 한 라우팅 및 실행을 위해 사용 해야 하는 필요한 데이터 및 키워드는 다음과 같습니다.

- 서비스: SAP HANA Large Instance
- 문제 유형: 구성 및 설정
- 문제 하위 유형: 문제가 위에 나열 되어 있지 않습니다.
- ' 내 네트워크 수정-Global Reach 추가 ' 제목
- 세부 정보: ' hana large instance 테넌트에 HANA large instance에 Global Reach 추가 또는 ' 온-프레미스에 hana Large Instance 테넌트에 Global Reach 추가 합니다.
- Hana Large Instance에서 HANA Large Instance 테넌트 케이스에 대 한 추가 세부 정보: 연결할 두 개의 테넌트를 찾고 **/29 IP 주소 범위** 를 제출 **해야 하는** **두 개의 Azure 지역을** 정의 해야 합니다.
- 온-프레미스에서 HANA 대량 인스턴스 테넌트 사례에 대 한 추가 정보: HANA Large Instance 테넌트가 배포 되는 **Azure 영역** 을 정의 하 여 직접 연결 합니다. 또한 온-프레미스와 Azure 간에 Express 경로 회로를 설정할 때 받은 **인증 GUID** 및 **회로 피어 ID** 를 제공 해야 합니다. 또한 **ASN**의 이름을로 해야 합니다. 마지막 결과물은 Express 경로 Global Reach에 대 한 **/29 IP 주소 범위** 입니다.

> [!NOTE]
> 두 경우를 모두 처리 하려면 지금까지 사용 되는 다른 IP 주소 범위와 겹치지 않는 두 개의 다른/29 IP 주소 범위를 제공 해야 합니다. 




## <a name="next-steps"></a>다음 단계

- [HLI에 대한 추가 네트워크 요구 사항](hana-additional-network-requirements.md)
