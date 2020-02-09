---
title: Azure Virtual Network 피어링
titlesuffix: Azure Virtual Network
description: Azure의 가상 네트워크 피어링에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: e47c7d7c4e982604814b227544a98bb301a20105
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980097"
---
# <a name="virtual-network-peering"></a>가상 네트워크 피어링

가상 네트워크 피어 링을 사용 하면 [Azure Virtual Network](virtual-networks-overview.md)에서 네트워크를 원활 하 게 연결할 수 있습니다. 가상 네트워크는 연결 목적으로 하나의 가상 네트워크로 표시 됩니다. 가상 컴퓨터 간의 트래픽은 Microsoft 백본 인프라를 사용 합니다. 동일한 네트워크에 있는 가상 컴퓨터 간의 트래픽과 마찬가지로 트래픽은 Microsoft *개인* 네트워크를 통해 라우팅됩니다.

Azure는 다음 피어 링 유형을 지원 합니다.

* 가상 네트워크 피어 링: 동일한 Azure 지역 내에서 가상 네트워크를 연결 합니다.
* 글로벌 가상 네트워크 피어 링: Azure 지역에서 가상 네트워크를 연결 합니다.

로컬 또는 글로벌 가상 네트워크 피어링을 사용하는 이점은 다음과 같습니다.

* 다른 가상 네트워크에 있는 리소스 간에 짧은 대기 시간, 높은 대역폭 연결
* 한 가상 네트워크의 리소스가 다른 가상 네트워크의 리소스와 통신할 수 있는 기능입니다.
* Azure 구독, Azure Active Directory 테넌트, 배포 모델 및 Azure 지역에서 가상 네트워크 간에 데이터를 전송 하는 기능입니다.
* Azure Resource Manager를 통해 만든 가상 네트워크를 피어 링 하는 기능입니다.
* 리소스 관리자를 통해 만든 가상 네트워크를 클래식 배포 모델을 통해 만든 가상 네트워크에 피어 링 하는 기능입니다. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
* 피어링을 만들 때 또는 피어링이 생성된 후에 각 가상 네트워크에 있는 리소스에 대한 가동 중지 시간이 없습니다.

피어링된 가상 네트워크 간의 네트워크 트래픽이 프라이빗 전용입니다. 가상 네트워크 간의 트래픽이 Microsoft 백본 네트워크에서 유지됩니다. 가상 네트워크 간의 통신에 공용 인터넷, 게이트웨이, 또는 암호화가 필요하지 않습니다.

## <a name="connectivity"></a>연결

피어 링 가상 네트워크의 경우 두 가상 네트워크의 리소스는 피어 링 가상 네트워크의 리소스에 직접 연결할 수 있습니다.

동일한 지역에서 피어링된 가상 네트워크에 있는 가상 머신 간의 네트워크 대기 시간은 단일 가상 네트워크 내의 대기 시간과 같습니다. 네트워크 처리량은 해당 크기에 비례하는 가상 머신에 허용되는 대역폭을 기반으로 합니다. 피어링 내에서 대역폭에 대한 추가 제한이 없습니다.

피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 게이트웨이나 공용 인터넷을 통해서가 아니라 Microsoft 백본 인프라를 통해 직접 라우팅됩니다.

두 가상 네트워크에서 네트워크 보안 그룹을 적용 하 여 다른 가상 네트워크 또는 서브넷에 대 한 액세스를 차단할 수 있습니다.
가상 네트워크 피어 링을 구성할 때 가상 네트워크 간의 네트워크 보안 그룹 규칙을 열거나 닫습니다. 피어 링 가상 네트워크 간의 전체 연결을 여는 경우 특정 액세스를 차단 하거나 거부 하는 네트워크 보안 그룹을 적용할 수 있습니다. 기본 옵션은 전체 연결입니다. 네트워크 보안 그룹에 대 한 자세한 내용은 [보안 그룹](security-overview.md)을 참조 하세요.

## <a name="service-chaining"></a>서비스 체이닝

서비스 체인을 사용 하면 한 가상 네트워크에서 사용자 정의 경로를 통해 피어 링 네트워크에 있는 가상 어플라이언스 또는 게이트웨이로 트래픽을 보낼 수 있습니다.

서비스 체인을 사용 하도록 설정 하려면 피어 링 가상 네트워크의 가상 컴퓨터를 *다음 홉* IP 주소로 가리키는 사용자 정의 경로를 구성 합니다. 또한 사용자 정의 경로는 가상 네트워크 게이트웨이를 가리켜 서비스 연결을 사용할 수 있습니다.

허브 가상 네트워크에서 네트워크 가상 어플라이언스 또는 VPN 게이트웨이와 같은 인프라 구성 요소를 호스트 하는 허브 및 스포크 네트워크를 배포할 수 있습니다. 모든 스포크 가상 네트워크는 허브 가상 네트워크와 피어링할 수 있습니다. 트래픽은 허브 가상 네트워크에서 네트워크 가상 어플라이언스 또는 VPN 게이트웨이를 통해 흐릅니다.

가상 네트워크 피어링을 사용하면 사용자 정의 경로의 다음 홉이 피어링된 가상 네트워크 또는 VPN 게이트웨이에 있는 가상 머신의 IP 주소가 될 수 있습니다. Azure Express 경로 게이트웨이를 다음 홉 유형으로 지정 하는 사용자 정의 경로를 사용 하 여 가상 네트워크 간에 라우팅할 수 없습니다. 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 개요](virtual-networks-udr-overview.md#user-defined)를 참조하세요. 허브 및 스포크 네트워크 토폴로지를 만드는 방법을 알아보려면 [Azure의 허브-스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조 하세요.

## <a name="gateways-and-on-premises-connectivity"></a>게이트웨이 및 온-프레미스 연결

피어 링 가상 네트워크를 포함 한 각 가상 네트워크에는 자체 게이트웨이가 있을 수 있습니다. 가상 네트워크는 게이트웨이를 사용 하 여 온-프레미스 네트워크에 연결할 수 있습니다. 피어 링 가상 네트워크의 경우에도 게이트웨이를 사용 하 여 가상 [네트워크-가상 네트워크 연결을](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 구성할 수도 있습니다.

가상 네트워크 상호 연결과에 대 한 두 옵션을 모두 구성 하는 경우 가상 네트워크 간의 트래픽은 피어 링 구성을 통해 흐릅니다. 트래픽은 Azure 백본을 사용 합니다.

또한 피어 링 가상 네트워크의 게이트웨이를 온-프레미스 네트워크에 대 한 전송 지점으로 구성할 수 있습니다. 이 경우 원격 게이트웨이를 사용 하는 가상 네트워크에는 자체 게이트웨이가 있을 수 없습니다. 가상 네트워크에는 하나의 게이트웨이만 있습니다. 게이트웨이는 다음 다이어그램에 표시 된 것 처럼 피어 링 가상 네트워크의 로컬 또는 원격 게이트웨이입니다.

![가상 네트워크 피어링 전송](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

가상 네트워크 피어 링과 글로벌 가상 네트워크 피어 링은 모두 게이트웨이 전송을 지원 합니다.

다른 배포 모델을 통해 만든 가상 네트워크 간의 게이트웨이 전송은 지원 됩니다. 게이트웨이는 리소스 관리자 모델의 가상 네트워크에 있어야 합니다. 전송을 위한 게이트웨이 사용에 관해 자세히 알아보려면 [가상 네트워크 피어링에서 전송을 위한 VPN 게이트웨이 구성](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

단일 Azure Express 경로 연결을 공유 하는 가상 네트워크를 피어 링 하는 경우 두 네트워크 간의 트래픽은 피어 링 관계를 거칩니다. 해당 트래픽은 Azure 백본 네트워크를 사용 합니다. 로컬 게이트웨이 각각에서 가상 네트워크를 사용하여 온-프레미스 회로에 연결할 수 있습니다. 그렇지 않으면 공유 게이트웨이를 사용 하 고 온-프레미스 연결에 대 한 전송을 구성할 수 있습니다.

## <a name="troubleshoot"></a>문제 해결

가상 네트워크가 피어 링 확인 하려면 유효 경로를 확인할 수 있습니다. 가상 네트워크의 모든 서브넷에서 네트워크 인터페이스에 대 한 경로를 확인 합니다. 가상 네트워크 피어링이 존재하는 경우에는 가상 네트워크 내의 모든 서브넷에는 피어링된 각 가상 네트워크의 각 주소 공간에 대해 다음 홉 형식의 *VNet 피어링*을 사용하는 경로가 있습니다. 자세한 내용은 [가상 컴퓨터 라우팅 문제 진단](diagnose-network-routing-problem.md)을 참조 하세요.

Azure Network Watcher를 사용 하 여 피어 링 가상 네트워크의 가상 머신에 대 한 연결 문제를 해결할 수도 있습니다. 연결 확인을 통해 원본 가상 컴퓨터의 네트워크 인터페이스에서 대상 가상 컴퓨터의 네트워크 인터페이스로 트래픽을 라우팅하는 방법을 확인할 수 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure Network Watcher 연결 문제 해결](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)을 참조 하세요.

[가상 네트워크 피어 링 문제 해결](virtual-network-troubleshoot-peering-issues.md)을 시도할 수도 있습니다.

## 피어 링 가상 네트워크에 대 한 제약 조건<a name="requirements-and-constraints"></a>

가상 네트워크가 글로벌 피어링될 때만 다음 제약 조건이 적용됩니다.

* 한 가상 네트워크의 리소스는 전역적으로 피어 링 된 가상 네트워크에 있는 ILB (기본 내부 Load Balancer)의 프런트 엔드 IP 주소와 통신할 수 없습니다.
* 기본 부하 분산 장치를 사용 하는 일부 서비스는 글로벌 가상 네트워크 피어 링을 통해 작동 하지 않습니다. 자세한 내용은 [글로벌 VNet 피어 링 및 부하 분산 장치와 관련 된 제약 조건](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)을 참조 하세요.

자세한 내용은 [요구 사항 및 제약 조건](virtual-network-manage-peering.md#requirements-and-constraints)을 참조 하세요. 지원 되는 피어 링 수에 대 한 자세한 내용은 [네트워킹 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조 하세요.

## <a name="permissions"></a>권한

가상 네트워크 피어 링을 만드는 데 필요한 사용 권한에 대 한 자세한 내용은 [사용 권한](virtual-network-manage-peering.md#permissions)을 참조 하세요.

## <a name="pricing"></a>가격

가상 네트워크 피어 링 연결을 사용 하는 수신 및 송신 트래픽에 대 한 명목 요금이 부과 됩니다. 자세한 내용은 [Virtual Network 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)을 참조 하세요.

게이트웨이 전송은 가상 네트워크가 피어 링 가상 네트워크에서 VPN/Express 경로 게이트웨이를 활용할 수 있도록 하는 피어 링 속성입니다. 게이트웨이 전송은 크로스-프레미스와 네트워크 간 연결 모두에 대해 작동 합니다. 피어 링 가상 네트워크에서 게이트웨이에 대 한 트래픽 (수신 또는 송신)에는 가상 네트워크 피어 링 요금이 발생 합니다. 자세한 내용은 VPN gateway 요금에 대 한 [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway/) 및 express 경로 게이트웨이 요금에 대 한 Express 경로 게이트웨이 가격 책정을 참조 VPN Gateway 하세요.

>[!NOTE]
> 이 문서의 이전 버전에서는 가상 네트워크 피어 링 요금이 게이트웨이 전송에 적용 되지 않습니다. 이제 가격 책정 페이지에 따라 정확한 가격이 반영 됩니다.

## <a name="next-steps"></a>다음 단계

* 두 가상 네트워크 간에 피어 링을 만들 수 있습니다. 네트워크는 동일한 구독, 동일한 구독에 있는 다른 배포 모델 또는 다른 구독에 속할 수 있습니다. 다음 시나리오 중 하나에 대한 자습서를 완료합니다.

    |Azure 배포 모델             | Subscription  |
    |---------                          |---------|
    |둘 다 Resource Manager              |[동일](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[다름](create-peering-different-subscriptions.md)|
    |하나는 Resource Manager, 다른 하나는 클래식  |[동일](create-peering-different-deployment-models.md)|
    |                                   |[다름](create-peering-different-deployment-models-subscriptions.md)|

* 허브 및 스포크 네트워크 토폴로지를 만드는 방법을 알아보려면 [Azure의 허브-스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조 하세요.
* 모든 가상 네트워크 피어 링 설정에 대해 알아보려면 [가상 네트워크 피어 링 만들기, 변경 또는 삭제](virtual-network-manage-peering.md)를 참조 하세요.
* 일반적인 가상 네트워크 피어 링 및 글로벌 가상 네트워크 피어 링 질문에 대 한 답변은 [VNet 피어 링](virtual-networks-faq.md#vnet-peering)을 참조 하세요.
