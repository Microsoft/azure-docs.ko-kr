---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 526d8a6a103e7623bac459004bf9ac79e4927541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81686974"
---
1. 선택한 직접 피어링에 대한 연결을 봅니다.
    ```powershell
    $directPeering.Connections

    PeeringDBFacilityId         : 6
    UseForPeeringService        : False
    SessionAddressProvider      : Peer
    SessionPrefixV4             : 12.245.179.20/30
    ConnectionIdentifier        : 3f196dfe-c174-4900-ab1c-fb7b50ad1535
    SessionPrefixV6             :
    MicrosoftSessionIPv4Address : 12.245.179.22
    BandwidthInMbps             : 100000
    SessionStateV4              : Established
    SessionStateV6              : None
    ConnectionState             : Active
    ```
1. Peering Service에 사용할 연결을 선택합니다. 이 예제에서는 사용 가능한 연결만 사용합니다.
    ```powershell
    $directPeering.Connections[1] = $directPeering.Connections[1] | Set-AzPeeringDirectConnectionObject -UseForPeeringService $true

    PeeringDBFacilityId         : 6
    UseForPeeringService        : True
    SessionAddressProvider      : Peer
    SessionPrefixV4             : 12.245.179.20/30
    ConnectionIdentifier        : 3f196dfe-c174-4900-ab1c-fb7b50ad1535
    SessionPrefixV6             :
    MicrosoftSessionIPv4Address : 12.245.179.22
    BandwidthInMbps             : 100000
    SessionStateV4              : Established
    SessionStateV6              : None
    ConnectionState             : Active
    ```
1. 이제 다음 명령을 사용하여 직접 피어링에 대한 변경 내용을 저장합니다.
    ```powershell
    $directPeering | Update-AzPeering
    ```
    
    샘플 출력은 다음과 같습니다.
    
    ```powershell
        Name                 : SeattleDirectPeering
        Sku.Name             : Basic_Premium_Free
        Kind                 : Direct
        Connections          : {71,71}
        PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
        UseForPeeringService : True
        PeeringLocation      : Seattle
        ProvisioningState    : Succeeded
        Location             : centralus
        Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
        Type                 : Microsoft.Peering/peerings
        Tags                 : {}
    ```
    