---
title: Azure 가상 네트워크 계획 | Microsoft Docs
description: 격리, 연결 및 위치 요구 사항을 기반으로 가상 네트워크를 계획하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: kumud
ms.openlocfilehash: acd7a88acb31b9d3bd3ba714387561e91b3524a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034742"
---
# <a name="plan-virtual-networks"></a>가상 네트워크 계획

실험할 가상 네트워크 하나를 만드는 것은 간단하지만, 시간이 지나면서 조직의 프로덕션 요구사항을 지원하기 위해 대개의 경우 여러 개의 가상 네트워크를 배포하게 됩니다. 이를 대비하여 몇 가지 계획을 세운다면 보다 효율적으로 가상 네트워크를 배포하고 필요한 리소스를 연결할 수 있습니다. 이 문서의 정보는 가상 네트워크에 이미 익숙하고 관련 작업에 대한 경험이 있는 경우에 가장 유용합니다. 가상 네트워크를 잘 모르는 경우 [가상 네트워크 개요](virtual-networks-overview.md)를 읽어보는 것이 좋습니다.

## <a name="naming"></a>이름 지정

모든 Azure 리소스에는 이름이 있습니다. 이름은 각 리소스 종류에 따라 다를 수 있는 범위 내에서 고유해야 합니다. 예를 들어 가상 네트워크의 이름은 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) 내에서 고유해야 하지만 [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 또는 Azure [지역](https://azure.microsoft.com/regions/#services) 내에서는 중복될 수 있습니다. 리소스 이름을 지정할 때 일관적으로 사용할 수 있는 명명 규칙을 정의하면 시간이 지남에 따라 여러 네트워크 리소스를 관리할 때 유용합니다. 권장 사항은 [명명 규칙](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking)을 참조하세요.

## <a name="regions"></a>영역

모든 Azure 리소스는 Azure 지역 및 구독에서 만들어집니다. 리소스는 해당 리소스와 동일한 지역 및 구독에 있는 가상 네트워크에서만 만들 수 있습니다. 그러나 다른 구독 및 지역에 있는 가상 네트워크를 연결할 수 있습니다. 자세한 내용은 [연결](#connectivity)을 참조하세요. 리소스를 배포할 지역을 결정할 때는 리소스 사용자가 실제로 있는 위치를 고려해야 합니다.

- 리소스 사용자는 일반적으로 해당 리소스에 대해 가장 낮은 네트워크 대기 시간을 원합니다. 지정된 위치와 Azure 지역 간의 상대적 대기 시간을 확인하려면 [상대적 대기 시간 보기](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- 데이터 상주, 주권, 규정 준수 또는 복원력 요구 사항이 있나요? 그렇다면 요구 사항에 맞는 지역을 선택하는 것이 매우 중요합니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조하세요.
- 배포하는 리소스에 대해 동일한 Azure 지역 내의 Azure 가용성 영역에서 복원력이 필요한가요? VM(가상 머신)과 같은 리소스를 동일한 가상 네트워크 내의 여러 가용성 영역에 배포할 수 있습니다. 하지만 일부 Azure 지역은 가용성 영역을 지원하지 않습니다. 가용성 영역 및 이를 지원하는 지역에 대한 자세한 내용은 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

## <a name="subscriptions"></a>구독

각 구독 내에서 [한도](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)까지 필요한 개수의 가상 네트워크를 배포할 수 있습니다. 일부 조직은 예를 들어 부서마다 구독이 다릅니다. 자세한 내용 및 구독에 대한 고려 사항은 [구독 거버넌스](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy)를 참조하세요.

## <a name="segmentation"></a>분할

구독 및 지역 단위로 여러 가상 네트워크를 만들 수 있습니다. 또한 각 가상 네트워크 내에 여러 서브넷을 만들 수 있습니다. 필요한 가상 네트워크 및 서브넷 수를 결정하는 데 도움이 되는 고려 사항은 다음과 같습니다.

### <a name="virtual-networks"></a>가상 네트워크

가상 네트워크는 Azure 공용 네트워크의 격리된 가상 부분입니다. 각 가상 네트워크는 구독에 전용됩니다. 하나의 가상 네트워크를 만들지 또는 여러 가상 네트워크를 만들지 결정할 때 고려할 사항은 다음과 같습니다.

- 트래픽을 별도의 가상 네트워크로 격리하기 위해 존재하는 조직의 보안 요구 사항이 있나요? 가상 네트워크의 연결 여부를 선택할 수 있습니다. 가상 네트워크를 연결하는 경우 방화벽과 같은 네트워크 가상 어플라이언스를 구현하여 가상 네트워크 간의 트래픽 흐름을 제어할 수 있습니다. 자세한 내용은 [보안](#security) 및 [연결](#connectivity)을 참조하세요.
- 가상 네트워크를 별도의 [구독](#subscriptions) 또는 [지역](#regions)으로 격리하기 위해 존재하는 조직의 요구 사항이 있나요?
- [네트워크 인터페이스](virtual-network-network-interface.md)를 사용하면 VM에서 다른 리소스와 통신할 수 있습니다. 각 네트워크 인터페이스에는 하나 이상의 개인 IP 주소가 할당되어 있습니다. 가상 네트워크에 필요한 네트워크 인터페이스 및 [개인 IP 주소](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)는 몇 개인가요? 가상 네트워크에서 유지할 수 있는 네트워크 인터페이스 및 개인 IP 주소의 개수에는 [제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)이 있습니다.
- 가상 네트워크를 다른 가상 네트워크 또는 온-프레미스 네트워크에 연결하고 싶으신가요? 일부 가상 네트워크는 서로 또는 온-프레미스 네트워크에 연결하고, 나머지 가상 네트워크는 연결하지 않을 수 있습니다. 자세한 내용은 [연결](#connectivity)을 참조하세요. 다른 가상 네트워크 또는 온-프레미스 네트워크에 연결하는 각 가상 네트워크에는 고유한 주소 공간이 있어야 합니다. 각 가상 네트워크에는 해당 주소 공간에 할당된 하나 이상의 공용 또는 개인 주소 범위가 있습니다. 주소 범위는 CIDR(클래스 없는 인터넷 도메인 라우팅) 형식(예: 10.0.0.0/16)으로 지정됩니다. 가상 네트워크의 [주소 범위](manage-virtual-network.md#add-or-remove-an-address-range)에 대해 자세히 알아보세요.
- 여러 가상 네트워크의 리소스에 대한 조직의 관리 요구 사항이 있나요? 그렇다면 리소스를 별도의 가상 네트워크로 구분하여 조직의 개인에 대한 [사용 권한 할당](#permissions)을 간소화하거나 가상 네트워크마다 다른 정책을 할당할 수 있습니다.
- 일부 Azure 서비스 리소스를 가상 네트워크에 배포하는 경우 고유한 가상 네트워크가 만들어집니다. Azure 서비스에서 자체 가상 네트워크를 만들지 여부를 결정하면 [가상 네트워크에 배포할 수 있는 각 Azure 서비스](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)에 대한 정보를 참조하세요.

### <a name="subnets"></a>서브넷

가상 네트워크를 [한도](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)까지 하나 이상의 서브넷으로 분할할 수 있습니다. 하나의 서브넷을 만들지 또는 한 구독에 여러 가상 네트워크를 만들지 결정할 때 고려할 사항은 다음과 같습니다.

- 각 서브넷에는 가상 네트워크의 주소 공간 내에 CIDR 형식으로 지정된 고유한 주소 범위가 있어야 합니다. 주소 범위는 가상 네트워크의 다른 서브넷과 겹칠 수 없습니다.
- 일부 Azure 서비스 리소스를 가상 네트워크에 배포하려는 경우 고유한 서브넷을 만들거나 필요할 수 있으므로 이를 위해 할당되지 않은 충분한 공간이 있어야 합니다. Azure 서비스에서 자체 서브넷을 만드는지 여부를 확인하려면 [가상 네트워크에 배포할 수 있는 각 Azure 서비스](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)에 대한 정보를 참조하세요. 예를 들어 Azure VPN 게이트웨이를 사용하여 온-프레미스 네트워크에 가상 네트워크를 연결하는 경우 가상 네트워크에 해당 게이트웨이 전용 서브넷이 있어야 합니다. 자세한 내용은 [게이트웨이 서브넷](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)을 참조하세요.
- 기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 네트워크 트래픽을 라우팅합니다. 서브넷 간의 Azure 라우팅을 방지하거나 네트워크 가상 어플라이언스를 통해 서브넷 간에 트래픽을 라우팅하도록 Azure의 기본 라우팅을 재정의할 수 있습니다. NVA(네트워크 가상 어플라이언스)를 통해 동일한 가상 네트워크 흐름에서 리소스 간에 이러한 트래픽이 필요한 경우 서로 다른 서브넷에 리소스를 배포합니다. 자세한 내용을 [보안](#security)을 참조하세요.
- Azure Storage 계정 또는 Azure SQL 데이터베이스와 같은 Azure 리소스에 대한 액세스를 가상 네트워크 서비스 엔드포인트가 있는 특정 서브넷으로 제한할 수 있습니다. 또한 인터넷에서 리소스에 대한 액세스를 거부할 수 있습니다. 여러 서브넷을 만들고 일부 서브넷의 서비스 엔드포인트만 사용하도록 설정할 수 있습니다. [서비스 엔드포인트](virtual-network-service-endpoints-overview.md) 및 이를 사용하도록 설정할 수 있는 Azure 리소스에 대해 자세히 알아보세요.
- 가상 네트워크의 각 서브넷에 하나의 네트워크 보안 그룹을 연결하거나 연결하지 않을 수 있습니다. 각 서브넷에 동일하거나 다른 네트워크 보안 그룹을 연결할 수 있습니다. 각 네트워크 보안 그룹에는 원본과 대상 간의 트래픽을 허용하거나 거부하는 규칙이 있습니다. [네트워크 보안 그룹](#traffic-filtering)에 대해 자세히 알아보세요.

## <a name="security"></a>보안

네트워크 보안 그룹 및 네트워크 가상 어플라이언스를 사용하여 가상 네트워크에서 리소스를 보내고 받는 네트워크 트래픽을 필터링할 수 있습니다. Azure에서 서브넷의 트래픽을 라우팅하는 방법을 제어할 수 있습니다. 또한 조직에서 가상 네트워크의 리소스를 사용할 수 있는 사람을 제한할 수 있습니다.

### <a name="traffic-filtering"></a>트래픽 필터링

- 네트워크 보안 그룹, 네트워크 트래픽을 필터링하는 NVA 또는 둘 다를 사용하여 가상 네트워크의 리소스 간에 네트워크 트래픽을 필터링할 수 있습니다. 방화벽과 같은 NVA를 배포하여 네트워크 트래픽을 필터하려면 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1)를 참조하세요. NVA를 사용할 때는 서브넷의 트래픽을 NVA로 라우팅할 사용자 지정 경로를 만듭니다. 자세한 내용은 [트래픽 라우팅](#traffic-routing)을 참조하세요.
- 네트워크 보안 그룹에는 리소스에서 보내고 받는 트래픽을 허용하거나 거부하는 여러 기본 보안 규칙이 있습니다. 네트워크 보안 그룹을 네트워크 인터페이스, 네트워크 인터페이스가 있는 서브넷 또는 둘 다에 연결할 수 있습니다. 보안 규칙의 관리를 간소화하려면 가능한 한 네트워크 보안 그룹을 서브넷 내의 개별 네트워크 인터페이스 대신 개별 서브넷에 연결하는 것이 좋습니다.
- 서브넷의 VM마다 다른 보안 규칙을 적용해야 하는 경우 VM의 네트워크 인터페이스를 하나 이상의 애플리케이션 보안 그룹에 연결할 수 있습니다. 보안 규칙은 원본, 대상 또는 둘 다에서 애플리케이션 보안 그룹을 지정할 수 있습니다. 그러면 해당 규칙은 애플리케이션 보안 그룹의 구성원인 네트워크 인터페이스에만 적용됩니다. 자세한 내용은 [네트워크 보안 그룹](security-overview.md) 및 [애플리케이션 보안 그룹](security-overview.md#application-security-groups)을 참조하세요.
- Azure에서는 각 네트워크 보안 그룹 내에 여러 기본 보안 규칙을 만듭니다. 하나의 기본 규칙은 모든 트래픽이 가상 네트워크의 모든 리소스 간에 흐르도록 허용합니다. 이 동작을 재정의하려면 네트워크 보안 그룹 사용자 지정 라우팅을 사용하여 트래픽을 NVA 또는 둘 다로 라우팅합니다. Azure의 모든 [기본 보안 규칙](security-overview.md#default-security-rules)을 알고 네트워크 보안 그룹 규칙이 리소스에 적용되는 방식을 이해하는 것이 좋습니다.

[NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 또는 [네트워크 보안 그룹](virtual-networks-dmz-nsg.md)을 사용하여 Azure와 인터넷 간의 DMZ를 구현하는 샘플 디자인을 볼 수 있습니다.

### <a name="traffic-routing"></a>트래픽 라우팅

Azure에서는 서브넷의 아웃바운드 트래픽에 대한 여러 기본 경로를 만듭니다. 경로 테이블을 만들고 서브넷에 연결하여 Azure의 기본 라우팅을 재정의할 수 있습니다. Azure의 기본 라우팅을 재정의하는 일반적인 이유는 다음과 같습니다.
- 서브넷 간의 트래픽이 NVA를 통과하도록 하기 위해서입니다. [트래픽이 NVA를 통과하도록 경로 테이블을 구성](tutorial-create-route-table-portal.md)하는 방법에 대해 자세히 알아보기 위해
- 인터넷 바인딩된 모든 트래픽이 NVA를 통과하거나 온-프레미스에서 Azure VPN 게이트웨이를 통과하도록 하기 위해서입니다. 검사 및 로깅을 위해 온-프레미스에서 인터넷 트래픽을 강제로 설정하는 것을 강제 터널링이라고도 합니다. [강제 터널링](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)을 구성하는 방법에 대해 자세히 알아보세요.

사용자 지정 라우팅을 구현해야 하는 경우 [Azure의 라우팅](virtual-networks-udr-overview.md)을 이해하는 것이 좋습니다.

## <a name="connectivity"></a>연결

가상 네트워크 피어링을 사용하여 가상 네트워크를 다른 가상 네트워크에 연결하거나 Azure VPN 게이트웨이를 사용하여 온-프레미스 네트워크에 연결할 수 있습니다.

### <a name="peering"></a>피어링

[가상 네트워크 피어링](virtual-network-peering-overview.md)을 사용하는 경우 가상 네트워크는 지원되는 동일하거나 다른 Azure 지역에 있을 수 있습니다. 가상 네트워크는 동일하거나 다른 Azure 구독에 있을 수 있습니다(다른 Azure Active Directory 테넌트에 속하는 구독에도 해당됨). 피어링을 만들기 전에 모든 피어링 [요구 사항 및 제약 조건](virtual-network-manage-peering.md#requirements-and-constraints)을 이해하는 것이 좋습니다. 리소스가 같은 가상 네트워크에 있는 것처럼 동일한 지역에서 피어링된 가상 네트워크의 리소스 간 대역폭은 동일합니다.

### <a name="vpn-gateway"></a>VPN 게이트웨이

Azure [VPN 게이트웨이](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하여 [사이트 간 VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)와의 전용 연결을 통해 가상 네트워크를 온-프레미스 네트워크에 연결할 수 있습니다.

피어링과 VPN 게이트웨이를 결합하여 [허브 및 스포크 네트워크](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)를 만들 수 있습니다. 예를 들어 여기서 스포크 가상 네트워크는 허브 가상 네트워크에 연결되고 허브는 온-프레미스 네트워크에 연결됩니다.

### <a name="name-resolution"></a>이름 확인

특정 가상 네트워크의 리소스는 Azure의 [기본 제공 DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)를 사용하여 피어링된 가상 네트워크 내 리소스의 이름을 확인할 수 없습니다. 피어링된 가상 네트워크에서 이름을 확인하려면 [자체 DNS 서버를 배포](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)하거나 [Azure DNS 개인 도메인](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 사용합니다. 가상 네트워크와 온-프레미스 네트워크의 리소스 간에 이름을 확인하려면 자체 DNS 서버도 배포해야 합니다.

## <a name="permissions"></a>권한

Azure에서는 리소스에 대한 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 활용합니다. 사용 권한은 다음 계층의 [범위](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope)에 할당됩니다. 구독, 관리 그룹, 리소스 그룹 및 개별 리소스. 계층에 대한 자세한 내용은 [리소스 구성](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. Azure 가상 네트워크 및 모든 관련 기능(예: 피어링, 네트워크 보안 그룹, 서비스 엔드포인트 및 경로 테이블)을 사용하기 위해 조직의 구성원을 기본 제공 [소유자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor) 또는 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당한 다음 역할을 적절한 범위에 할당할 수 있습니다. 가상 네트워크의 하위 집합에 특정 사용 권한을 할당하려는 경우 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 만들고 [가상 네트워크](manage-virtual-network.md#permissions), [ 서브넷 및 서비스 엔드포인트](virtual-network-manage-subnet.md#permissions), [네트워크 인터페이스](virtual-network-network-interface.md#permissions), [피어링](virtual-network-manage-peering.md#permissions), [네트워크 및 애플리케이션 보안 그룹](manage-network-security-group.md#permissions) 또는 [경로 테이블](manage-route-table.md#permissions)에 필요한 특정 사용 권한을 할당합니다.

## <a name="policy"></a>정책

Azure Policy를 사용하여 정책 정의를 만들고, 할당하고, 관리할 수 있습니다. 정책 정의는 리소스에 대해 다양한 규칙을 적용하여 리소스를 조직의 표준 및 서비스 수준 계약을 준수하는 상태로 유지합니다. Azure Policy는 리소스 평가를 실행하여 사용자의 정책 정의를 준수하지 않는 리소스를 검색합니다. 예를 들어 특정 리소스 그룹 또는 지역에서만 가상 네트워크를 만들도록 허용하는 정책을 정의하고 적용할 수 있습니다. 또 다른 정책은 모든 서브넷에 연결된 네트워크 보안 그룹을 필요로 할 수 있습니다. 이러한 정책은 리소스를 만들고 업데이트할 때 평가됩니다.

정책은 다음 계층에 적용됩니다. 구독, 관리 그룹 및 리소스 그룹. [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아보거나 일부 가상 네트워크 [정책 템플릿](policy-samples.md) 샘플을 배포하세요.

## <a name="next-steps"></a>다음 단계

[가상 네트워크](manage-virtual-network.md), [서브넷 및 서비스 엔드포인트](virtual-network-manage-subnet.md), [네트워크 인터페이스](virtual-network-network-interface.md), [피어 링](virtual-network-manage-peering.md), [네트워크 및 애플리케이션 보안 그룹](manage-network-security-group.md) 또는 [경로 테이블](manage-route-table.md)에 대한 모든 작업, 설정 및 옵션을 알아봅니다.
