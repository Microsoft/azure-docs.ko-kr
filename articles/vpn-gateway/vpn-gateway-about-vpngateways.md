---
title: Azure VPN Gateway 정보
description: VPN Gateway가 무엇인지, VPN Gateway를 사용하여 IPsec IKE 사이트 간, VNet 간, 지점 및 사이트 간 VPN 가상 네트워크에 연결하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/25/2020
ms.author: cherylmc
ms.openlocfilehash: c7fbea977904145aa2e8851f45a4b70f9ce0c560
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855604"
---
# <a name="what-is-vpn-gateway"></a>VPN Gateway란?

VPN Gateway는 공용 인터넷을 통해 Azure 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 전송하는 데 사용되는 특정 유형의 가상 네트워크 게이트웨이입니다. VPN Gateway를 사용하여 Microsoft 네트워크를 통해 Azure 가상 네트워크 간에 암호화된 트래픽을 보낼 수도 있습니다. VPN Gateway는 각 가상 네트워크당 하나만 사용할 수 있습니다. 그러나 동일한 VPN Gateway에 대해 여러 연결을 만들 수 있습니다. 동일한 VPN Gateway에 대해 여러 연결을 만들면 모든 VPN 터널이 사용 가능한 게이트웨이 대역폭을 공유합니다.

VPN 게이트웨이는 Azure 가용성 영역에서 배포할 수 있습니다. 그러면 가상 네트워크 게이트웨이에 복원력, 확장성 및 고가용성이 제공됩니다. Azure Availability Zones에서 게이트웨이를 배포하면 Azure에서 영역 수준 오류로의 온-프레미스 네트워크 연결성을 보호하면서 물리적 및 논리적으로 영역 내 게이트웨이를 구분합니다. [Azure 가용성 영역의 영역 중복 가상 네트워크 게이트웨이 정보](about-zone-redundant-vnet-gateways.md)를 참조하세요.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>가상 네트워크 게이트웨이란?

가상 네트워크 게이트웨이는 사용자가 만드는 *게이트웨이 서브넷*이라는 특정 서브넷에 배포되는 둘 이상의 VM으로 구성됩니다. 가상 네트워크 게이트웨이 VM은 라우팅 테이블을 포함하며 특정 게이트웨이 서비스를 실행합니다. 이러한 VM은 가상 네트워크 게이트웨이를 만들 때 생성됩니다. 가상 네트워크 게이트웨이의 일부인 VM은 직접 구성할 수 없습니다.

가상 네트워크 게이트웨이를 구성할 때 게이트웨이 유형을 지정하는 설정을 구성합니다. 게이트웨이 유형에 따라 가상 네트워크 게이트웨이 사용 방법과 게이트웨이가 수행하는 작업이 결정합니다. 게이트웨이 유형 'Vpn'은 생성된 가상 네트워크 게이트웨이의 유형이 'VPN 게이트웨이'임을 지정합니다. 이렇게 하면 다른 게이트웨이 유형을 사용하는 ExpressRoute 게이트웨이와 구별됩니다. 가상 네트워크에는 두 개의 가상 네트워크 게이트웨이(하나의 VPN Gateway와 하나의 ExpressRoute 게이트웨이)가 있을 수 있습니다. 자세한 내용은 [게이트웨이 유형](vpn-gateway-about-vpn-gateway-settings.md#gwtype)을 참조하세요.

가상 네트워크 게이트웨이 만들기는 완료되는 데 최대 45분까지 소요됩니다. 가상 네트워크 게이트웨이를 만들 때 게이트웨이 VM은 게이트웨이 서브넷에 배포되고 지정한 설정으로 구성됩니다. VPN Gateway를 만든 후에 해당 VPN Gateway와 다른 VPN Gateway(VNet 대 VNet) 간에 IPsec/IKE VPN 터널 연결을 만들거나, VPN Gateway와 온-프레미스 VPN 디바이스 간(사이트 간)의 크로스-프레미스 IPsec/IKE VPN 터널 연결을 생성할 수 있습니다. 지점과 사이트 간 VPN 연결(OpenVPN, IKEv2 또는 SSTP를 통한 VPN)을 생성하여 회의실 또는 집과 같은 원격 위치에서 가상 네트워크에 연결할 수 있습니다.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>VPN Gateway 구성

VPN Gateway 연결은 특정 설정으로 구성된 여러 리소스에 따라 다릅니다. 대부분의 리소스는 개별적으로 구성할 수 있지만 일부 리소스는 특정 순서로 구성해야 합니다.

### <a name="design-connection-topology-diagrams"></a><a name="diagrams"></a>디자인: 연결 토폴로지 다이어그램

VPN Gateway 연결에 사용할 수 있는 다양한 구성이 있다는 사실을 꼭 기억하시기 바랍니다. 그 중에서 필요에 맞는 최상의 구성을 결정해야 합니다. 예를 들어 지점 및 사이트 간, 사이트 간 및 공존 ExpressRoute/사이트 간 연결 모두에는 서로 다른 지침과 구성 요구 사항이 있습니다. 디자인 및 연결 토폴로지 다이어그램에 대한 자세한 내용은 [디자인](design.md)을 참조하세요.

### <a name="settings"></a><a name="settings"></a>설정

각 리소스에 선택하는 설정은 성공적인 연결을 만드는 데 매우 중요합니다. VPN Gateway의 개별 리소스 및 설정에 대한 정보는 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md)를 참조하세요. 이 아티클에는 게이트웨이 유형, 게이트웨이 SKU, VPN 유형, 연결 유형, 게이트웨이 서브넷, 로컬 네트워크 게이트웨이 및 고려해야 할 다른 다양한 리소스 설정을 이해하는 데 유용한 정보가 포함되어 있습니다.

### <a name="deployment-tools"></a><a name="tools"></a>배포 도구

Azure Portal과 같은 하나의 구성 도구를 사용하여 리소스를 시작하고 구성할 수 있습니다. 나중에 PowerShell과 같은 다른 도구로 전환하도록 결정하여 추가 리소스를 구성하거나 해당하는 경우 기존 리소스를 수정할 수 있습니다. 현재, Azure Portal에서 모든 리소스 및 리소스 설정을 구성할 수 없습니다. 각 연결 토폴로지에 대한 문서의 지침은 특정 구성 도구가 필요한지 여부를 지정합니다.

### <a name="planning-table"></a><a name="planningtable"></a>계획 표

다음 테이블은 솔루션에 대한 최상의 연결 옵션을 결정하는 데 도움이 될 수 있습니다.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gateway-skus"></a><a name="gwsku"></a>게이트웨이 SKU

가상 네트워크 게이트웨이를 만들 때는 사용하려는 게이트웨이 SKU를 지정합니다. 작업 부하, 처리량, 기능 및 SLA의 종류를 기반으로 하는 요구 사항을 충족하는 SKU를 선택합니다.

* 지원되는 기능, 프로덕션 및 개발-테스트, 구성 단계를 포함한 게이트웨이 SKU에 대한 자세한 내용은 [VPN Gateway 설정 - 게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) 문서를 참조하세요.
* 레거시 SKU 정보는 [레거시 SKU를 사용하여 작업](vpn-gateway-about-skus-legacy.md)을 참조하세요.

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>터널, 연결 및 처리량별 게이트웨이 SKU

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="pricing"></a><a name="pricing"></a>가격 책정

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

VPN Gateway용 게이트웨이 SKU에 대한 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.

## <a name="faq"></a><a name="faq"></a>FAQ

VPN Gateway에 대한 자주 묻는 질문은 [VPN Gateway FAQ](vpn-gateway-vpn-faq.md)를 참조하세요.

## <a name="whats-new"></a><a name="new"></a>새로운 기능

RSS 피드를 구독하고 [Azure 업데이트](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway) 페이지에서 최신 VPN Gateway 기능 업데이트를 확인합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [VPN Gateway FAQ](vpn-gateway-vpn-faq.md)를 참조하세요.
- [구독 및 서비스 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)를 참조하세요.
- Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.
