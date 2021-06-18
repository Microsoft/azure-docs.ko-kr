---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 478856b6e62b06259ae37b049774c9e22c840d4a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720636"
---
`Resize-AzVirtualNetworkGateway` PowerShell cmdlet을 사용하여 Generation1.xml 또는 Generation2 SKU를 업그레이드하거나 다운그레이드할 수 있습니다(기본 SKU를 제외한 모든 VpnGw SKU의 크기를 조정할 수 있음). 기본 게이트웨이 SKU를 사용하는 경우 [대신 이러한 지침을 사용하여](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) 게이트웨이의 크기를 조정합니다.

다음 PowerShell 예제에서는 VpnGw2로 크기가 조정되는 게이트웨이 SKU를 보여 줍니다.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```
