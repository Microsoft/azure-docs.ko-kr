---
title: 파일 포함
titleSuffix: Azure
description: 파일 포함
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 610bac5c08d7f3aa3c93e273bc6573a08ca1239f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680801"
---
다음 예에서는 시애틀에서 10mbps Direct 피어 링을 만드는 방법을 보여 줍니다.

PowerShell cmdlet **AzPeeringDirectConnectionObject** 를 사용 하 여 새 피어 링 요청에 사용할 directconnection 개체를 만듭니다.

이 예에서는 DirectConnection 개체를 만드는 방법을 보여 줍니다.

```powershell
$connection1 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.31.0/31 `
    -SessionPrefixV6 fe01::3e:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

> [!NOTE]
> <index>이전 예의 **[] $peeringLocation** 에 대 한 값은 <index> 선택한 피어 링 위치와 일치 해야 합니다.

지정 된 피어 링 위치에 중복이 필요한 경우 다른 연결을 만듭니다.

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

PowerShell cmdlet **AzPeering** 를 사용 하 여 새 직접 피어 링을 만듭니다. 이 명령에는 여기에 표시 된 대로 검색할 수 있는 ASN 리소스 ID가 필요 합니다.


```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeeringLocation  $peeringLocation[0].PeeringLocation `
    -PeerAsnResourceId $asn.Id `
    -DirectConnection $connection1 [, $connection2]
```
&nbsp;

이 예제에서는 요청이 성공적으로 처리 된 경우의 응답을 보여 줍니다.

```powershell

    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : 71
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}

```
이 출력의 **{subscriptionId}** 대신 실제 구독 ID가 표시 됩니다.
