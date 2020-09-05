---
title: Azure 가상 네트워크에서 공용 IPv6 주소 및 주소 범위 예약
titlesuffix: Azure Virtual Network
description: Azure 가상 네트워크에서 공용 IPv6 주소 및 주소 범위를 예약 하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ac79e1eb5c4f7448dc17804cd8aac3cba582497e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509946"
---
# <a name="reserve-public-ipv6-address-prefix"></a>공용 IPv6 주소 접두사 예약
Azure VNet(가상 네트워크)용 IPv6을 사용하면 가상 네트워크 내부에서 또한 인터넷과 IPv6 및 IPv4 연결을 사용하여 Azure에서 애플리케이션을 호스트할 수 있습니다. 개별 IPv6 주소를 예약 하는 것 외에도 사용에 대 한 Azure IPv6 주소의 연속 범위 (IP 접두사 라고도 함)를 예약할 수 있습니다. 이 문서에서는 Azure PowerShell 및 CLI를 사용 하 여 IPv6 공용 IP 주소 및 주소 범위를 만드는 방법을 설명 합니다.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>단일 예약 된 IPv6 공용 IP 만들기

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

다음과 같이 [AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 와 함께 Azure PowerShell를 사용 하 여 예약 된 단일 (정적) IPV6 공용 IP 주소를 만들 수 있습니다.

```azurepowershell
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Azure CLI 사용

 다음과 같이 [az network 공용 ip create](/cli/azure/network/public-ip) 를 사용 하 여 Azure CLI 하나의 예약 된 (정적) IPV6 공용 ip 주소를 만들 수 있습니다.

```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>예약 된 IPv6 접두사 (범위) 만들기

IPv6 접두사를 예약 하려면 IPv4 접두사를 만드는 데 사용 되는 것과 동일한 명령에 i p v 6의 IP 주소 패밀리를 추가 합니다. 다음 명령은 크기/125 (IPv6 주소 8)의 접두사를 만듭니다.

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

다음과 같이 [az network 공용 ip create](/powershell/module/az.network/new-azpublicipprefix) 를 사용 하 여 Azure CLI를 사용 하 여 공용 IPv6 주소를 만들 수 있습니다.
```azurepowershell
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Azure CLI 사용

다음과 같이 Azure CLI를 사용 하 여 공용 IPv6 주소를 만들 수 있습니다.

```azurecli
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>예약 된 IPv6 접두사에서 공용 IP 주소 할당

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

 `-PublicIpPrefix`Azure PowerShell를 사용 하 여 공용 ip를 만들 때 인수를 추가 하 여 예약 된 접두사에서 고정 IPv6 공용 ip를 만듭니다. 다음 예에서는 접두사를 만들어 *$MyOwnIPv 6prefix*라는 PowerShell 변수에 저장 했다고 가정 합니다.

```azurepowershell
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예에서는 접두사가 생성 되어 *IPv6PrefixWestUS*이라는 CLI 변수에 저장 되어 있다고 가정 합니다.

```azurecli
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>다음 단계
- [IPv6 주소 접두사](ipv6-public-ip-address-prefix.md)에 대해 자세히 알아보세요.
- [IPv6 주소](ipv6-overview.md)에 대해 자세히 알아보세요.
