---
title: 'Azure Express 경로: 회로 피어 링 다시 설정'
description: Azure PowerShell를 사용 하 여 Azure Express 경로 회로의 피어 링을 사용 하지 않도록 설정 하 고 설정 하는 방법을 알아봅니다. 피어 링을 구성 하면 기본적으로 사용 하도록 설정 됩니다.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: e576fe2f08d54d2642603e3b7974e00feec8ce93
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192181"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute 회로 피어링 다시 설정

이 문서에서는 PowerShell을 사용하여 ExpressRoute 회로의 피어링을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다. 피어링을 사용하지 않도록 설정하는 경우 ExpressRoute 회로의 기본 연결 및 보조 연결 둘 다에서 BGP 세션이 종료됩니다. 이 피어링을 통한 Microsoft와의 연결이 손실됩니다. 피어링을 사용하도록 설정하는 경우 ExpressRoute 회로의 기본 연결 및 보조 연결 둘 다에서 BGP 세션이 설정됩니다. 이 피어링을 통한 Microsoft와의 연결이 다시 설정됩니다. ExpressRoute 회로에서 Microsoft 피어링 및 Azure 프라이빗 피어링을 별도로 사용 및 사용하지 않도록 설정할 수 있습니다. ExpressRoute 회로에서 피어링을 처음 구성할 때는 피어링이 기본적으로 사용되도록 설정됩니다.

ExpressRoute 피어링을 다시 설정하는 것이 도움이 되는 몇 가지 시나리오가 있습니다.
* 재해 복구 디자인 및 구현을 테스트합니다. 예를 들어 두 개의 ExpressRoute 회로가 있습니다. 한 회로의 피어링은 사용하지 않도록 설정하고 네트워크 트래픽을 다른 회로로 강제로 장애 조치(Failover)할 수 있습니다.
* Azure 개인 피어 링 또는 Express 경로 회로의 Microsoft 피어 링에서 BFD (양방향 전달 검색)를 사용 하도록 설정 합니다. 1 2018 년 1 10 2020 월 1 일 이후에 Express 경로 회로가 생성 된 경우 Microsoft 피어 링에서 Express 경로 및 Microsoft 피어 링 후에 Azure 개인 피어 링에서 BFD가 기본적으로 사용 하도록 설정 됩니다. 그 이전에 회로를 만들었으면 BFD가 사용되도록 설정되지 않습니다. 피어링을 사용하지 않도록 설정했다가 다시 사용하도록 설정하여 BFD를 사용하도록 설정할 수 있습니다. 

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>피어링 다시 설정

1. PowerShell을 로컬로 실행하는 경우 상승된 권한으로 PowerShell 콘솔을 열고, 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

   ```azurepowershell
   Connect-AzAccount
   ```
2. Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. 사용할 구독을 지정합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. ExpressRoute 회로를 검색하려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. 사용하거나 사용하지 않도록 설정하려는 피어링을 식별합니다. *피어링*은 배열입니다. 다음 예제에서 피어링[0]은 Azure 프라이빗 피어링이고 피어링[1]은 Microsoft 피어 링입니다.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. 피어링의 상태를 변경하려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   피어링은 설정한 상태를 유지합니다. 

## <a name="next-steps"></a>다음 단계
ExpressRoute 문제 해결에 도움이 필요한 경우 다음 문서를 확인합니다.
* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
