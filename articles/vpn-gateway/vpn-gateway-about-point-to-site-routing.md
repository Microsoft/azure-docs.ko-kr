---
title: Azure 지점 및 사이트 간 라우팅 정보 | Microsoft Docs
description: 이 문서는 지점 및 사이트 간 VPN 라우팅이 작동하는 방법을 이해하는 데 도움이 됩니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: anzaman
ms.openlocfilehash: 486a910226db5dc7b36aaf873e7bb8115eb78805
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653615"
---
# <a name="about-point-to-site-vpn-routing"></a>지점 및 사이트 간 VPN 라우팅 정보

이 문서는 Azure 지점 및 사이트 간 VPN 라우팅이 작동하는 방법을 이해하는 데 도움이 됩니다. P2S(지점 및 사이트 간) VPN 라우팅 동작은 클라이언트 OS, VPN 연결에 사용된 프로토콜 및 VNet(가상 네트워크)이 서로 연결된 방식에 따라 다릅니다.

Azure는 현재 원격 액세스에 대해 두 가지 프로토콜, 즉 IKEv2 및 SSTP를 지원합니다. IKEv2는 Windows, Linux, MacOS, Android 및 iOS를 포함한 많은 클라이언트 운영 체제에서 지원됩니다. SSTP는 Windows에서만 지원됩니다. 네트워크의 토폴로지를 변경하고 Windows VPN 클라이언트를 사용하는 경우, 변경 내용을 클라이언트에 적용하기 위해 Windows 클라이언트용 VPN 클라이언트 패키지를 다운로드하여 다시 설치해야 합니다.

> [!NOTE]
> 이 문서는 IKEv2에만 적용됩니다.
>

## <a name="diagrams"></a>다이어그램에 대한 정보

이 문서에는 서로 다른 여러 가지 다이어그램이 있습니다. 각 섹션에서는 다른 토폴로지 또는 구성을 보여 줍니다. 이 문서에서는 S2S(사이트 간) 및 VNet 간 연결이 모두 IPsec 터널이므로 동일한 방식으로 작동합니다. 이 문서의 모든 VPN 게이트웨이는 경로 기반입니다.

## <a name="isolatedvnet"></a>격리된 단일 VNet

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 다른 가상 네트워크(VNet1)와 연결되거나 피어링되지 않은 VNet에 대한 연결입니다. 이 예에서 SSTP 또는 IKEv2를 사용하는 클라이언트는 VNet1에 액세스할 수 있습니다.

![격리된 VNet 라우팅](./media/vpn-gateway-about-point-to-site-routing/1.jpg "격리된 VNet 라우팅")

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비 Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1에 액세스할 수 있습니다.

* 비Windows 클라이언트는 VNet1에 액세스할 수 있습니다.

## <a name="multipeered"></a>피어링된 여러 VNet

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 VNet2와 피어링됩니다. VNet2는 VNet3과 피어링됩니다. VNet1은 VNet4와 피어링됩니다. VNet1 및 VNet3 간에는 직접 피어링되지 않습니다. VNet1에는 "게이트웨이 전송 허용"이 있으며, VNet2에는 "원격 게이트웨이 사용"을 사용하도록 설정되어 있습니다.

Windows를 사용하는 클라이언트는 피어링된 VNet에 직접 액세스할 수 있지만, VNet 피어링 또는 네트워크 토폴로지가 변경되면 VPN 클라이언트를 다시 다운로드해야 합니다. 비Windows 클라이언트는 피어링된 VNet에 직접 액세스할 수 있습니다. 액세스는 전이되지 않으며, 직접 피어링된 VNet으로만 제한됩니다.

![피어링된 여러 VNet](./media/vpn-gateway-about-point-to-site-routing/2.jpg "피어링된 여러 VNet")

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* 비 Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1, VNet2 및 VNet4에 액세스할 수 있지만, 토폴로지 변경 내용을 적용하려면 VPN 클라이언트를 다시 다운로드해야 합니다.

* 비Windows 클라이언트는 VNet1, VNet2 및 VNet4에 액세스할 수 있습니다.

## <a name="multis2s"></a>S2S VPN을 사용하여 여러 VNet 연결

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 사이트 간 VPN 연결을 사용하여 VNet2에 연결됩니다. VNet2는 사이트 간 VPN 연결을 사용하여 VNet3에 연결됩니다. VNet1 및 VNet3 간에는 직접 피어링 또는 사이트 간 VPN 연결이 없습니다. 모든 사이트 간 연결에서 라우팅을 위해 BGP를 실행하지 않습니다.

Windows 또는 지원되는 다른 OS를 사용하는 클라이언트는 VNet1에만 액세스할 수 있습니다. 추가 VNet에 액세스하려면 BGP를 사용해야 합니다.

![여러 VNet 및 S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "여러 VNet 및 S2S")

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비 Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

* 비Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

## <a name="multis2sbgp"></a>S2S VPN(BGP)을 사용하여 여러 VNet 연결

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 사이트 간 VPN 연결을 사용하여 VNet2에 연결됩니다. VNet2는 사이트 간 VPN 연결을 사용하여 VNet3에 연결됩니다. VNet1 및 VNet3 간에는 직접 피어링 또는 사이트 간 VPN 연결이 없습니다. 모든 사이트 간 연결에서 라우팅을 위해 BGP를 실행합니다.

Windows 또는 지원되는 다른 OS를 사용하는 클라이언트는 사이트 간 VPN 연결을 사용하여 연결된 모든 VNet에 액세스할 수 있지만, 연결된 VNet에 대한 경로를 Windows 클라이언트에 수동으로 추가해야 합니다.

![여러 VNet 및 S2S(BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "여러 VNet 및 S2S BGP")

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16

* 비 Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1, VNet2 및 VNet3에 액세스할 수 있지만, VNet2 및 VNet3에 대한 경로를 수동으로 추가해야 합니다.

* 비Windows 클라이언트는 VNet1, VNet2 및 VNet3에 액세스할 수 있습니다.

## <a name="vnetbranch"></a>단일 VNet 및 단일 지점

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 다른 가상 네트워크와 연결되거나 피어링되지 않지만, BGP를 실행하지 않는 사이트 간 VPN 연결을 통해 온-프레미스 사이트에 연결됩니다.

Windows 및 비Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

![VNet 및 지점과의 라우팅](./media/vpn-gateway-about-point-to-site-routing/5.jpg "VNet 및 지점과의 라우팅")

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비 Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

* 비Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

## <a name="vnetbranchbgp"></a>단일 VNet 및 단일 지점(BGP)

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 다른 가상 네트워크와 연결되거나 피어링되지 않지만, BGP를 실행하는 사이트 간 VPN 연결을 통해 온-프레미스 사이트(Site1)에 연결됩니다.

Windows 클라이언트는 VNet 및 지점(Site1)에 액세스할 수 있지만, Site1에 대한 경로를 클라이언트에 수동으로 추가해야 합니다. 비Windows 클라이언트는 VNet 및 온-프레미스 지점에 액세스할 수 있습니다.

![단일 VNet 및 단일 지점(BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "단일 VNet 및 단일 지점")

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>추가된 경로

* Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비 Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1 및 Site1에 액세스할 수 있지만, Site1에 대한 경로를 수동으로 추가해야 합니다.

* 비Windows 클라이언트는 VNet1 및 Site1에 액세스할 수 있습니다.


## <a name="multivnets2sbranch"></a>S2S와 지점을 사용하여 여러 VNet 연결

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 사이트 간 VPN 연결을 사용하여 VNet2에 연결됩니다. VNet2는 사이트 간 VPN 연결을 사용하여 VNet3에 연결됩니다. VNet1 및 VNet3 네트워크 간에는 직접 피어링 또는 사이트 간 VPN 터널이 없습니다. VNet3은 사이트 간 VPN 연결을 사용하여 지점(Site1)에 연결됩니다. 모든 VPN 연결에서 BGP를 실행하지 않습니다.

모든 클라이언트는 VNet1에만 액세스할 수 있습니다.

![여러 VNet S2S 및 지점](./media/vpn-gateway-about-point-to-site-routing/7.jpg "여러 VNet S2S 및 지점")

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>추가된 경로

* 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비 Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

* 비Windows 클라이언트는 VNet1에만 액세스할 수 있습니다.

## <a name="multivnets2sbranchbgp"></a>S2S와 지점(BGP)을 사용하여 여러 VNet 연결

이 예의 지점 및 사이트 간 VPN 게이트웨이 연결은 VNet1에 대한 연결입니다. VNet1은 사이트 간 VPN 연결을 사용하여 VNet2에 연결됩니다. VNet2는 사이트 간 VPN 연결을 사용하여 VNet3에 연결됩니다. VNet1 및 VNet3 네트워크 간에는 직접 피어링 또는 사이트 간 VPN 터널이 없습니다. VNet3은 사이트 간 VPN 연결을 사용하여 지점(Site1)에 연결됩니다. 모든 VPN 연결에서 BGP를 실행합니다.

Windows를 사용하는 클라이언트는 사이트 간 VPN 연결을 사용하여 연결된 VNet 및 사이트에 액세스할 수 있지만, VNet2, VNet3 및 Site1에 대한 경로를 클라이언트에 수동으로 추가해야 합니다. 비Windows 클라이언트는 수동 개입 없이 사이트 간 VPN 연결을 사용하여 연결된 VNet 및 사이트에 액세스할 수 있습니다. 액세스는 전이되며, 클라이언트는 연결된 모든 VNet 및 사이트(온-프레미스)의 리소스에 액세스할 수 있습니다.

![여러 VNet S2S 및 지점](./media/vpn-gateway-about-point-to-site-routing/8.jpg "여러 VNet S2S 및 지점")

### <a name="address-space"></a>주소 공간

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>추가된 경로

* 클라이언트에 추가된 경로: 10.1.0.0/16, 192.168.0.0/24

* 비 Windows 클라이언트에 추가된 경로: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows 클라이언트는 VNet1, VNet2, VNet3 및 Site1에 액세스할 수 있지만, VNet2, VNet3 및 Site1에 대한 경로를 클라이언트에 수동으로 추가해야 합니다.

* 비Windows 클라이언트는 VNet1, VNet2, VNet3 및 Site1에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 P2S VPN 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)를 참조하여 P2S VPN 만들기를 시작합니다.
