---
title: 'Azure Express 경로: Express 경로 직접 구성'
description: Azure PowerShell를 사용 하 여 전 세계에서 피어 링 위치에 있는 Microsoft 글로벌 네트워크에 직접 연결 하도록 Azure Express 경로 다이렉트를 구성 하는 방법을 알아봅니다.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: how-to
ms.date: 01/22/2020
ms.author: jaredro
ms.openlocfilehash: 42803cbc7901be01c88145e2d98f2982434710a1
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192755"
---
# <a name="how-to-configure-expressroute-direct"></a>Express 경로 다이렉트를 구성 하는 방법

ExpressRoute Direct는 전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 연결하는 기능을 제공합니다. 자세한 내용은 [ExpressRoute Direct 정보](expressroute-erdirect-about.md)를 참조하세요.

## <a name="create-the-resource"></a><a name="resources"></a>리소스 만들기

1. Azure에 로그인하고 구독을 선택합니다. ExpressRoute Direct 리소스와 ExpressRoute 회로가 동일한 구독에 있어야 합니다.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Expressrouteportslocation 및 expressrouteport Api에 액세스 하려면 구독을 Microsoft. 네트워크에 다시 등록 합니다.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. ExpressRoute Direct가 지원되는 모든 위치를 나열합니다.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **예제 출력**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
4. 위에 나열된 위치에 사용 가능한 대역폭이 있는지 확인

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **예제 출력**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
5. 위에서 선택한 위치를 기준으로 ExpressRoute Direct 리소스 만들기

   ExpressRoute Direct는 QinQ 및 Dot1Q VLAN 캡슐화를 둘 다 지원합니다. QinQ를 선택한 경우 ExpressRoute Direct 리소스 전체에서 고유하게 식별되는 S-Tag가 각 ExpressRoute 회로에 동적으로 할당됩니다. 회로의 각 C-Tag는 회로에서 고유해야 하지만 ExpressRoute Direct 전체에서 고유할 필요는 없습니다.  

   Dot1Q 캡슐화가 선택된 경우 전체 ExpressRoute Direct 리소스에서 C-Tag(VLAN)의 고유성을 관리해야 합니다.  

   > [!IMPORTANT]
   > ExpressRoute Direct에는 한 가지 유형으로만 캡슐화할 수 있습니다. ExpressRoute Direct를 만든 후에는 캡슐화를 변경할 수 없습니다.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > 캡슐화 속성을 Dot1Q로 설정할 수도 있습니다. 
   >

   **예제 출력:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>링크의 관리 상태 변경

  이 프로세스를 사용하여 계층 1 테스트를 수행하고 각 교차 연결이 1차 및 2차 포트에 대한 각 라우터에 제대로 패치되도록 합니다.
1. ExpressRoute Direct 세부 정보를 가져옵니다.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. 링크를 사용으로 설정합니다. 이 단계를 반복하여 각 링크를 사용으로 설정합니다.

   링크[0]은 1차 포트이고 링크[1]은 2차 포트입니다.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **예제 출력:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   `AdminState = "Disabled"`로 동일한 절차를 사용하여 포트 작동을 중단합니다.

## <a name="create-a-circuit"></a><a name="circuit"></a>회로 만들기

기본적으로 ExpressRoute Direct 리소스가 있는 구독에서 10개의 회로를 만들 수 있습니다. 이 제한은 지원 서비스에서 늘릴 수 있습니다. 사용자는 프로비전된 대역폭과 사용된 대역폭을 둘 다 추적할 책임이 있습니다. 프로비전된 대역폭은 ExpressRoute Direct 리소스에 있는 모든 회로의 대역폭 합계이고, 사용된 대역폭은 기본 물리적 인터페이스의 물리적 사용량입니다.

위에 설명된 시나리오를 지원에 한해 ExpressRoute Direct에서 사용할 수 있는 추가 회로 대역폭은 40Gbps 및 100Gbps입니다.

지역, 표준 또는 프리미엄 일 **수 있습니다.**

Unlimiteddata는 Express 경로 직접 지원 되지 않으므로 무제한으로 사용할 **수 있어야 합니다** .

ExpressRoute Direct 리소스에서 회로를 만듭니다.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  기타 대역폭에는 5.0, 10.0 및 40.0이 있습니다.

  **예제 출력:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>다음 단계

Express 경로 직접에 대 한 자세한 내용은 [개요](expressroute-erdirect-about.md)를 참조 하세요.
