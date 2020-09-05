---
title: Azure 방화벽 SNAT 개인 IP 주소 범위
description: SNAT에 대 한 IP 주소 범위를 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: 272f5b747efbc3776b1b2ba7c3546ade717c2452
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231370"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 방화벽 SNAT 개인 IP 주소 범위

Azure 방화벽은 공용 IP 주소에 대 한 모든 아웃 바운드 트래픽에 대해 자동 SNAT를 제공 합니다. 기본적으로 Azure 방화벽은 대상 IP 주소가 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)당 개인 ip 주소 범위에 있는 경우 네트워크 규칙을 사용 하 여 SNAT 하지 않습니다. 응용 프로그램 규칙은 대상 IP 주소에 관계 없이 항상 [투명 프록시](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) 를 사용 하 여 적용 됩니다.

이 논리는 트래픽을 인터넷으로 직접 라우팅하는 경우에 효과적입니다. 그러나 [강제 터널링](forced-tunneling.md)을 사용 하도록 설정한 경우 인터넷 바인딩된 트래픽은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 연결 되어 온-프레미스 방화벽에서 원본을 숨깁니다.

조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 그러나 공용 IP 주소 범위를 SNAT **하지 않도록** Azure 방화벽을 구성할 수 있습니다. 예를 들어 개별 IP 주소를 지정 하려면 다음과 같이 지정할 수 있습니다 `192.168.1.10` . IP 주소 범위를 지정 하려면 다음과 같이 지정할 수 있습니다 `192.168.1.0/24` .

대상 IP 주소에 관계 없이 Azure 방화벽을 SNAT 하지 않도록 구성 하려면 개인 IP 주소 범위로 **0.0.0.0/0** 을 사용 합니다. 이 구성을 사용 하는 경우 Azure 방화벽은 트래픽을 인터넷으로 직접 라우팅할 수 없습니다. 대상 주소에 관계 없이 항상 SNAT를 사용 하도록 방화벽을 구성 하려면 전용 IP 주소 범위로 **255.255.255.255/32** 를 사용 합니다.

> [!IMPORTANT]
> 사용자 고유의 개인 IP 주소 범위를 지정 하 고 기본 IANA RFC 1918 주소 범위를 유지 하려면 사용자 지정 목록에 IANA RFC 1918 범위가 계속 포함 되어 있는지 확인 합니다. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT 개인 IP 주소 범위 구성-Azure PowerShell

Azure PowerShell를 사용 하 여 방화벽에 대 한 개인 IP 주소 범위를 지정할 수 있습니다.

### <a name="new-firewall"></a>새 방화벽

새 방화벽의 경우 Azure PowerShell 명령은 다음과 같습니다.

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges는 Azure 방화벽의 현재 기본값으로 확장 되 고 다른 범위는 추가 됩니다. Private 범위 사양에 IANAPrivateRanges 기본값을 유지 하려면 `PrivateRange` 다음 예에 표시 된 것 처럼 사양에 유지 되어야 합니다.

자세한 내용은 [AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)를 참조 하세요.

### <a name="existing-firewall"></a>기존 방화벽

기존 방화벽을 구성 하려면 다음 Azure PowerShell 명령을 사용 합니다.

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>템플릿

섹션에 다음을 추가할 수 있습니다 `additionalProperties` .

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT 개인 IP 주소 범위 구성-Azure Portal

Azure Portal를 사용 하 여 방화벽에 대 한 개인 IP 주소 범위를 지정할 수 있습니다.

1. 리소스 그룹을 선택한 다음 방화벽을 선택 합니다.
2. **개요** 페이지의 **개인 IP 범위**에서 기본값 **IANA RFC 1918**을 선택 합니다.

   **개인 IP 접두사 편집** 페이지가 열립니다.

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="개인 IP 접두사 편집":::

1. 기본적으로 **IANAPrivateRanges** 가 구성 되어 있습니다.
2. 사용자 환경에 대 한 개인 IP 주소 범위를 편집 하 고 **저장**을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 방화벽 강제 터널링](forced-tunneling.md)에 대해 알아봅니다.