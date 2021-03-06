---
title: 'P2S VPN을 사용 하 여 VNet에 연결 & 여러 인증 유형: 포털'
titleSuffix: Azure VPN Gateway
description: Azure Portal에서 여러 인증 유형을 사용 하 여 P2S를 통해 VNet에 연결 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745685"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>여러 인증 유형을 사용 하 여 VNet에 지점 및 사이트 간 VPN 연결 구성: Azure Portal

이 문서는 Windows, Linux 또는 macOS를 실행 하는 개별 클라이언트를 Azure VNet에 안전 하 게 연결 하는 데 도움이 됩니다. 지점 및 사이트 간 VPN 연결은 집 또는 회의에서 원격 통신하는 경우와 같이 원격 위치에서 VNet에 연결하려는 경우에 유용합니다. 또한 VNet에 연결해야 하는 몇 가지 클라이언트만 있는 경우 사이트 간 VPN 대신 P2S를 사용할 수도 있습니다. P2S 연결을 작동하는 데는 VPN 디바이스 또는 공용 IP 주소가 필요하지 않습니다. P2S는 SSTP(Secure Socket Tunneling Protocol) 또는 IKEv2를 통한 VPN 연결을 만듭니다. 지점 및 사이트 간 VPN에 대한 자세한 내용은 [지점 및 사이트 간 VPN 정보](point-to-site-about.md)를 참조하세요.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="컴퓨터에서 Azure VNet 지점 및 사이트 간 연결 다이어그램에 연결":::

지점 및 사이트 간 VPN에 대 한 자세한 내용은 지점 및 [사이트 간 Vpn 정보](point-to-site-about.md)를 참조 하세요. Azure PowerShell를 사용 하 여이 구성을 만들려면 [Azure PowerShell를 사용 하 여 지점 및 사이트 간 VPN 구성](vpn-gateway-howto-point-to-site-rm-ps.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

동일한 VPN 게이트웨이에서 여러 인증 유형이 OpenVPN 터널 유형 으로만 지원 됩니다.

### <a name="example-values"></a><a name="example"></a>예제 값

다음 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 보다 정확하게 이해할 수 있습니다.

* **VNet 이름:** VNet1
* **주소 공간:** 10.1.0.0/16<br>이 예제에서는 하나의 주소 공간만 사용합니다. VNet에는 둘 이상의 주소 공간을 포함할 수 있습니다.
* **서브넷 이름:** FrontEnd
* **서브넷 주소 범위:** 10.1.0.0/24
* **구독:** 구독이 2개 이상 있는 경우 올바른 구독을 사용 중인지 확인합니다.
* **리소스 그룹:** TestRG1
* **위치:** 미국 동부
* **게이트웨이 서브넷:** 10.1.255.0/27<br>
* **가상 네트워크 게이트웨이 이름:** VNet1GW
* **게이트웨이 유형:** VPN
* **VPN 유형:** 경로 기반
* **공용 IP 주소 이름:** VNet1GWpip
* **연결 형식:** 지점 및 사이트 간
* **클라이언트 주소 풀:** 172.16.201.0/24<br>이 지점 및 사이트 간 연결을 사용하여 VNet에 연결되는 VPN 클라이언트는 클라이언트 주소 풀에서 IP 주소를 받습니다.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>가상 네트워크 만들기

시작하기 전에 Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>가상 네트워크 게이트웨이

이 단계에서는 VNet용 가상 네트워크 게이트웨이를 만듭니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

>[!NOTE]
>기본 게이트웨이 SKU는 OpenVPN 터널 형식을 지원 하지 않습니다.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>클라이언트 주소 풀

클라이언트 주소 풀은 사용자가 지정한 개인 IP 주소 범위입니다. 지점 및 사이트 간 VPN을 통해 연결하는 클라이언트는 동적으로 이 범위의 IP 주소를 수신합니다. 연결 원본이 되는 온-프레미스 위치 또는 연결 대상이 되는 VNet과 겹치지 않는 개인 IP 주소 범위를 사용합니다. 여러 프로토콜을 구성 하 고 SSTP가 프로토콜 중 하나인 경우 구성 된 주소 풀이 구성 된 프로토콜 간에 동일 하 게 분할 됩니다.

1. 가상 네트워크 게이트웨이가 생성된 후에는 가상 네트워크 게이트웨이 페이지의 **설정** 섹션으로 이동합니다. **설정** 에서 지점 및 **사이트 간 구성을** 선택 합니다. **지금 구성** 을 선택 하 여 구성 페이지를 엽니다.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="지점 및 사이트 간 구성 페이지의 스크린샷" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. 지점 및 **사이트 간 구성** 페이지에서 다양 한 설정을 구성할 수 있습니다. **주소 풀** 상자에 사용 하려는 개인 IP 주소 범위를 추가 합니다. VPN 클라이언트는 동적으로 지정된 범위에서 IP 주소를 수신합니다. 활성/수동 구성의 경우 최소 서브넷 마스크는 29 비트이 고 활성/비활성 구성의 경우 28 비트입니다.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="주소 풀의 스크린샷":::

1. 다음 섹션으로 계속 진행 하 여 인증 및 터널 유형을 구성 합니다.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>인증 및 터널 유형

이 섹션에서는 인증 유형 및 터널 유형을 구성 합니다. 지점 및 **사이트 간 구성** 페이지에서 **터널 유형** 또는 **인증 유형이** 표시 되지 않으면 게이트웨이에서 기본 SKU를 사용 하 고 있는 것입니다. 기본 SKU는 IKEv2 또는 RADIUS 인증을 지원하지 않습니다. 이러한 설정을 사용 하려면 다른 게이트웨이 SKU를 사용 하 여 게이트웨이를 삭제 하 고 다시 만들어야 합니다.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="인증 유형 스크린샷.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>터널 종류

지점 및 **사이트 간 구성** 페이지에서 **OPENVPN (SSL)** 을 터널 유형으로 선택 합니다.

### <a name="authentication-type"></a><a name="authenticationtype"></a>인증 유형

**인증 유형** 으로 원하는 유형을 선택 합니다. 옵션은 다음과 같습니다.

* Azure 인증서
* RADIUS
* Azure Active Directory

선택 된 인증 유형에 따라 채워야 하는 다른 구성 설정 필드가 표시 됩니다. 필요한 정보를 입력 하 고 페이지 맨 위에 있는 **저장** 을 선택 하 여 모든 구성 설정을 저장 합니다.

인증 유형에 대 한 자세한 내용은 다음을 참조 하세요.

* [Azure 인증서](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>VPN 클라이언트 구성 패키지

클라이언트 구성 설정을 사용 하 여 VPN 클라이언트를 구성 해야 합니다. VPN 클라이언트 구성 패키지에는 P2S 연결을 통해 VNet에 연결 하기 위해 VPN 클라이언트를 구성 하는 설정이 포함 된 파일이 포함 되어 있습니다.

VPN 클라이언트 구성 파일을 생성 하 고 설치 하는 방법에 대 한 지침은 구성과 관련 된 문서를 사용 합니다.

* [네이티브 Azure 인증서 인증 P2S 구성에 대 한 VPN 클라이언트 구성 파일을 만들고 설치](point-to-site-vpn-client-configuration-azure-cert.md)합니다.
* [Azure Active Directory 인증: P2S openvpn 프로토콜 연결에 대 한 vpn 클라이언트를 구성](openvpn-azure-ad-client.md)합니다.

## <a name="point-to-site-faq"></a><a name="faq"></a>지점 및 사이트 간 FAQ

이 섹션에는 지점 및 사이트 간 구성과 관련 된 FAQ 정보가 포함 되어 있습니다. VPN Gateway에 대 한 추가 정보는 [VPN GATEWAY FAQ](vpn-gateway-vpn-faq.md) 를 볼 수도 있습니다.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 자세한 내용은 [Virtual Machines](../index.yml)를 참조 하세요. 네트워킹 및 가상 머신에 대한 자세한 내용은 [Azure 및 Linux VM 네트워크 개요](../virtual-machines/network-overview.md)를 참조하세요.

P2S 문제 해결 정보는 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.
