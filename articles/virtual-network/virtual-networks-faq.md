---
title: Azure Virtual Network FAQ
titlesuffix: Azure Virtual Network
description: Microsoft Azure Virtual Network에 대해 자주 묻는 질문에 답변합니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: 19fdf2e7e1c7c56b6bfe8ddbf7329d3722f4e8de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713612"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure Virtual Network FAQ(질문과 대답)

## <a name="virtual-network-basics"></a>Virtual Network 기본 사항

### <a name="what-is-an-azure-virtual-network-vnet"></a>Azure VNet(Virtual Network)이란?
Azure VNet(Virtual Network)은 클라우드의 사용자 네트워크를 나타내는 표현입니다. 구독 전용 Azure 클라우드를 논리적으로 격리한 것이 가상 네트워크입니다. VNet을 사용하여 Azure에서 VPN(가상 사설망)을 프로비전 및 관리할 수 있으며 필요에 따라 VNet을 Azure에서 다른 VNet 또는 온-프레미스 IT 인프라와 연결하여 하이브리드 또는 크로스-프레미스 솔루션을 만들 수 있습니다. 생성한 각 VNet은 각각의 CIDR 블록을 가지며 CIDR 블록이 겹치지 않는 한 다른 VNet 및 온-프레미스 네트워크에 연결할 수 있습니다. 또한 VNet에 대한 DNS 서버 설정 및 VNet의 서브넷으로 구분에 대한 제어권을 가집니다.

VNet을 다음에 사용합니다.

* 전용 사설 클라우드 전용 VNet을 만듭니다. 경우에 따라 솔루션에 대해 프레미스간 구성이 필요하지 않을 수 있습니다. VNet을 만들 때 VNet 내의 서비스 및 VM은 클라우드 내에서 안전하게 직접 서로 통신할 수 있습니다. 솔루션의 일부로 인터넷 통신이 필요한 VM 및 서비스에 대한 엔드포인트 연결을 계속 구성할 수 있습니다.
* 데이터 센터를 안전하게 확장합니다. VNet을 사용하여 기존의 사이트 간(S2S) VPN을 빌드하여 데이터 센터 용량을 안전하게 확장할 수 있습니다. S2S VPN은 IPSEC를 사용하여 회사 VPN Gateway와 Azure 간의 보안 연결을 제공합니다.
* 하이브리드 클라우드 시나리오가 가능하도록 합니다. VNet은 다양한 하이브리드 클라우드 시나리오를 지원할 수 있는 유연성을 제공합니다. 메인프레임 및 Unix 시스템과 같은 모든 형식의 온-프레미스 시스템에 클라우드 기반 애플리케이션을 안전하게 연결할 수 있습니다.

### <a name="how-do-i-get-started"></a>어떻게 시작하나요?
[가상 네트워크 설명서](https://docs.microsoft.com/azure/virtual-network/)를 방문하여 시작합니다. 이 콘텐츠는 모든 VNet 기능에 대한 개요 및 배포 정보를 제공합니다.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>크로스-프레미스 연결 없이 VNet을 사용할 수 있습니까?
예. VNet을 온-프레미스에 연결하지 않고도 사용할 수 있습니다. 예를 들어 Azure VNet에서만 Microsoft Windows Server Active Directory 도메인 컨트롤러와 SharePoint 팜을 실행할 수 있습니다.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>VNet 간 또는 VNet과 온-프레미스 데이터 센터 간에 WAN 최적화를 수행할 수 있습니까?
예. Azure Marketplace를 통해 여러 공급업체에서 [WAN 최적화 네트워크 가상 어플라이언스](https://azure.microsoft.com/marketplace/?term=wan+optimization)를 배포할 수 있습니다.

## <a name="configuration"></a>구성

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>VNet을 만들려면 어떤 도구를 사용합니까?
다음 도구를 사용하여 VNet을 만들거나 구성할 수 있습니다.

* Azure portal
* PowerShell
* Azure CLI
* 네트워크 구성 파일(netcfg - 클래식 VNet 전용) [네트워크 구성 파일을 사용하여 VNet 구성](virtual-networks-using-network-configuration-file.md) 문서를 참조하세요.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>VNet 내에서 사용할 수 있는 주소 범위는 무엇입니까?
[RFC 1918](https://tools.ietf.org/html/rfc1918)에 정의되어 있는 모든 IP 주소 범위를 사용할 수 있습니다. 예를 들어 10.0.0.0/16을 사용할 수 있습니다. 다음 주소 범위는 추가할 수 없습니다.
* 224.0.0.0/4(멀티캐스트)
* 255.255.255.255/32(브로드캐스트)
* 127.0.0.0/8(루프백)
* 169.254.0.0/16(링크-로컬)
* 168.63.129.16/32(내부 DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>VNet 내에서 공용 IP 주소를 가질 수 있습니까?
예. 공용 IP 주소 범위에 대한 자세한 내용은 [가상 네트워크 만들기](manage-virtual-network.md#create-a-virtual-network)를 참조하세요. 공용 IP 주소는 인터넷에서 직접 액세스할 수 없습니다.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>VNet 내에서 서브넷 수에 제한이 있습니까?
예. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)을 참조하세요. 서브넷 주소 공간은 서로 겹칠 수 없습니다.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?
예. Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 서브넷마다 첫 번째와 마지막 IP 주소가 Azure 서비스에 사용되는 각 서브넷의 x.x.x.1-x.x.x.3 주소와 함께 프로토콜 적합성을 위해 예약됩니다.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>VNet 및 서브넷은 얼마나 크고 얼마나 작을 수 있습니까?
지원되는 가장 작은 서브넷은 /29이며 가장 큰 서브넷은 /8(CIDR 서브넷 정의 사용)입니다.

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>VNet을 사용하여 내 VLAN을 Azure에 가져올 수 있습니까?
아니요. VNet은 계층 3 오버레이입니다. Azure는 모든 계층 2 의미 체계를 지원하지 않습니다.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>VNet 및 서브넷에 사용자 지정 라우팅 정책을 지정할 수 있습니까?
예. 경로 테이블을 만들어서 서브넷에 연결할 수 있습니다. Azure의 라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md#custom-routes)를 참조하세요.

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNet은 멀티 캐스트 또는 브로드캐스트를 지원합니까?
아니요. 멀티캐스트와 브로드캐스트는 지원되지 않습니다.

### <a name="what-protocols-can-i-use-within-vnets"></a>VNet 내에서 사용할 수 있는 프로토콜은 무엇입니까?
VNet 내에서 TCP, UDP 및 ICMP TCP/IP 프로토콜을 사용할 수 있습니다. 유니캐스트(원본 포트 UDP/68 / 대상 포트 UDP/67)를 통한 DHCP(Dynamic Host Configuration Protocol)를 제외하고 유니캐스트는 VNet 내에서 지원됩니다. 멀티 캐스트, 브로드캐스트, IP-IP 캡슐화 패킷 및 GRE(일반 라우팅 캡슐화) 패킷은 VNet 내에서 차단됩니다. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>내 기본 라우터를 VNet 내에서 ping할 수 있습니까?
아니요.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Tracert를 사용하여 연결을 진단할 수 있습니까?
아니요.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>VNet을 만든 후 서브넷을 추가할 수 있습니까?
예. 서브넷 주소 범위가 다른 서브넷에 속하지 않고 가상 네트워크의 주소 범위에 사용 가능한 공간이 남아있기만 하면 서브넷을 VNet에 언제든지 추가할 수 있습니다.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>서브넷을 만든 후 크기를 수정할 수 있습니까?
예. VNet 내에서 배포된 VM 또는 서비스가 없는 경우 서브넷을 추가, 제거, 확장 또는 축소할 수 있습니다.

### <a name="can-i-modify-subnets-after-i-created-them"></a>서브넷을 만든 후 수정할 수 있습니까?
예. VNet에서 사용되는 CIDR 블록을 추가, 제거 및 수정할 수 있습니다.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>VNet에서 서비스를 실행 중인 경우 인터넷에 연결할 수 있습니까?
예. VNet 내에 배포된 모든 서비스는 인터넷에 아웃바운드로 연결할 수 있습니다. Azure의 아웃바운드 인터넷 연결에 대해 자세히 알아보려면 [아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. Resource Manager를 통해 배포된 리소스에 인바운드로 연결하려면 리소스에 공용 IP 주소가 할당되어 있어야 합니다. 공용 IP 주소에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-public-ip-address.md)를 참조하세요. Azure에서 배포된 모든 Azure Cloud Service에는 공개적으로 주소를 지정할 수 있는 VIP가 할당되어 있습니다. 이러한 서비스가 인터넷 연결을 허용하도록 하려면 PaaS 역할에 대한 입력 엔드포인트 및 가상 머신에 대한 엔드포인트를 정의해야 합니다.

### <a name="do-vnets-support-ipv6"></a>VNet은 IPv6를 지원합니까?
아니요. VNet과 함께 IPv6를 사용할 수 없습니다. 단, Azure 부하 분산 장치에 IPv6 주소를 할당하여 가상 머신의 부하를 분산할 수 있습니다. 자세한 내용은 [Azure Load Balancer의 IPv6에 대한 개요](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

### <a name="can-a-vnet-span-regions"></a>VNet을 사용하여 지역을 확장할 수 있습니까?
아니요. VNet은 단일 지역으로 제한됩니다. 그러나 가상 네트워크는 가용성 영역을 확장합니다. 사용 가능한 영역에 대한 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 가상 네트워크 피어링을 사용하여 다른 지역의 가상 네트워크를 연결할 수 있습니다. 자세한 내용은 [가상 네트워크 피어링 개요](virtual-network-peering-overview.md)를 참조하세요.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Azure에서 다른 VNet에 VNet을 연결할 수 있습니까?
예. 다음 중 하나를 사용하여 하나의 VNet을 다른 VNet에 연결할 수 있습니다.
- **가상 네트워크 피어링**: 자세한 내용은 [VNet 피어링 개요](virtual-network-peering-overview.md)를 참조하세요.
- **Azure VPN Gateway**: 자세한 내용은 [VNet 간 연결 구성](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. 

## <a name="name-resolution-dns"></a>이름 확인(DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>VNet에 대한 DNS 옵션은 무엇입니까?
사용 가능한 모든 DNS 옵션을 안내하는 [VM 및 역할 인스턴스에 대한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md) 페이지의 의사 결정 테이블을 사용하세요.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>VNet에 대한 DNS 서버를 지정할 수 있습니까?
예. VNet 설정에서 DNS 서버 IP 주소를 지정할 수 있습니다. 이 설정은 VNet의 모든 VM에 대한 기본 DNS 서버로 적용됩니다.

### <a name="how-many-dns-servers-can-i-specify"></a>얼마나 많은 DNS 서버 수를 지정할 수 있습니까?
[Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)을 참조하세요.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>네트워크를 생성 한 후 DNS 서버를 수정할 수 있습니까?
예. VNet에 대한 DNS 서버 목록을 언제든지 변경할 수 있습니다. DNS 서버 목록을 변경하는 경우 새로운 DNS 서버를 선택하기 위해 VNet에서 각 VM을 다시 시작해야 합니다.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Azure에서 제공하는 DNS란 무엇이며 VNet으로 작동합니까?
Azure에서 제공하는 DNS는 Microsoft에서 제공하는 다중 테넌트 DNS 서비스입니다. Azure는 이 서비스에 모든 VM 및 클라우드 서비스 역할 인스턴스를 등록합니다. 이 서비스는 동일한 클라우드 서비스 내에 포함된 VM 및 역할 인스턴스에 대한 호스트 이름 및 동일한 VNet에서 VM 및 역할 인스턴스에 대한 FQDN으로 이름 확인을 제공합니다. DNS에 대해 자세히 알아보려면 [VM 및 Cloud Services 역할 인스턴스에 대한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

Azure에서 제공하는 DNS를 사용한 테넌트 간 이름 확인에 대한 VNet의 첫 100개의 클라우드 서비스에 제한이 있습니다. 사용자 고유의 DNS 서버를 사용하는 경우 이러한 제한이 적용되지 않습니다.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>VM 당/클라우드 서비스 단위로 DNS 설정을 재정의할 수 있습니까?
예. VM 또는 클라우드 서비스 별로 DNS 서버를 설정하여 기본 네트워크 설정을 재정의할 수 있습니다. 그러나 가능한 한 네트워크 전체의 DNS를 사용하는 것이 좋습니다.

### <a name="can-i-bring-my-own-dns-suffix"></a>고유한 DNS 접미사를 가져올 수 있습니까?
아니요. VNet에 대해 사용자 지정 DNS 접미사를 지정할 수 없습니다.

## <a name="connecting-virtual-machines"></a>가상 머신 연결

### <a name="can-i-deploy-vms-to-a-vnet"></a>VNet에 VM을 배포할 수 있습니까?
예. 리소스 관리자 배포 모델을 통해 배포된 VM에 연결된 모든 NIC(네트워크 인터페이스)는 VNet에 연결되어야 합니다. 클래식 배포 모델을 통해 배포된 VM은 VNet에 선택적으로 연결할 수 있습니다.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>VM에 지정할 수 있는 IP 주소 유형에는 무엇이 있습니까?
* **개인:** 각 VM 내에서 각 NIC에 할당됩니다. 주소는 고정 또는 동적 방법을 사용하여 할당됩니다. VNet의 서브넷 설정에 지정한 범위에서 개인 IP 주소가 할당됩니다. VNet에 연결되지 않은 경우에도 클래식 배포 모델을 통해 배포된 리소스에는 개인 IP 주소가 할당됩니다. 할당 메서드의 동작은 리소스가 Resource Manager로 배포되었는지 또는 클래식 배포 모델과 함께 배포되었는지에 따라 다릅니다. 

  - **Resource Manager**: 동적 또는 정적 메서드로 할당된 개인 IP 주소는 리소스가 삭제될 때까지 가상 머신(Resource Manager)에 할당된 상태로 유지됩니다. 차이점은 정적 방법을 사용하면 할당할 주소를 사용자가 선택하고 동적 방법을 사용하면 Azure에서 선택된다는 점입니다. 
  - **클래식**: 가상 머신(클래식) VM이 중지(할당 취소됨)된 상태에서 다시 시작될 때 동적 메서드로 할당된 개인 IP 주소가 변경될 수 있습니다. 클래식 배포 모델을 통해 배포된 리소스의 개인 IP 주소가 절대 변경되지 않도록 하려면 정적 메서드로 개인 IP 주소를 할당합니다.

* **공용:** 필요에 따라 Azure Resource Manager 배포 모델을 통해 배포된 VM에 연결된 NIC에 할당됩니다. 정적 또는 동적 할당 메서드를 사용하여 주소를 할당할 수 있습니다. 클래식 배포 모델을 통해 배포된 모든 VM 및 Cloud Services 역할 인스턴스는 클라우드 서비스 내에 존재하며 *동적*, 공용 VIP(가상 IP) 주소가 할당됩니다. [예약된 IP 주소](virtual-networks-reserved-public-ip.md)라고 하는 공용 *정적* IP 주소는 필요에 따라 VIP로 할당될 수 있습니다. 클래식 배포 모델을 통해 배포된 개별 VM 또는 Cloud Services 역할 인스턴스에 공용 IP 주소를 할당할 수 있습니다. 이러한 주소는 [ILPIP(인스턴스 수준 공용 IP)](virtual-networks-instance-level-public-ip.md) 주소라고 하며 동적으로 할당될 수 있습니다.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>나중에 만들 VM에 대한 개인 IP 주소를 예약할 수 있습니까?
아니요. 개인 IP 주소를 예약할 수 없습니다. 개인 IP 주소가 사용 가능한 경우 DHCP 서버에서 VM 또는 역할 인스턴스에 할당됩니다. VM은 개인 IP 주소를 할당하려는 VM일 수도 있고 그렇지 않을 수도 있습니다. 그러나 이미 만든 VM의 개인 IP 주소를 사용 가능한 모든 개인 IP 주소로 변경할 수 있습니다.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>VNet에서 VM에 대한 개인 IP 주소가 변경됩니까?
경우에 따라 다릅니다. Resource Manager를 통해 VM이 배포된 경우 IP 주소가 정적 할당 메서드로 할당되었는지 또는 동적 할당 메서드로 할당되었는지에 관계없이 변경되지 않습니다. 클래식 배포 모델을 통해 VM을 배포한 경우 동적 IP 주소는 VM이 중지된(할당 취소됨) 상태에서 시작될 때 변경될 수 있습니다. 이 주소는 VM을 삭제할 때 두 배포 모델 중 하나를 통해 배포된 VM에서 해제됩니다

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>VM 운영 체제 내에서 IP 주소를 NIC에 수동으로 할당할 수 있습니까?
예. 하지만 가상 머신에 여러 IP 주소를 할당하는 경우와 같이 필요한 경우가 아니면 권장하지 않습니다. 자세한 내용은 [가상 머신에 여러 IP 주소 추가](virtual-network-multiple-ip-addresses-portal.md#os-config)를 참조하세요. VM에 연결된 Azure NIC에 할당된 IP 주소가 변경되고 VM 운영 체제 내의 IP 주소가 다르면, VM에 대한 연결이 끊어집니다.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>클라우드 서비스 배포 슬롯을 중지하거나 운영 체제 내에서 VM을 종료하는 경우 IP 주소는 어떻게 됩니까?
아무 일도 일어나지 않습니다. IP 주소(공용 VIP, 공용 및 개인)는 클라우드 서비스 배포 슬롯 또는 VM에 할당된 상태로 유지됩니다.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>VM을 다시 배포하지 않고 VNet에서 하나의 서브넷에서 다른 서브넷으로 이동할 수 있습니까?
예. [다른 서브넷으로 VM 또는 역할 인스턴스를 이동하는 방법](virtual-networks-move-vm-role-to-subnet.md) 문서에서 자세한 정보를 확인할 수 있습니다.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>VM에 대해 정적 MAC 주소를 구성할 수 있습니까?
아니요. MAC 주소를 정적으로 구성할 수 없습니다.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>MAC 주소가 만들어진 후 VM에 대해 동일하게 유지됩니까?
예, MAC 주소는 삭제될 때까지 리소스 관리자 또는 클래식 배포 모델을 통해 배포된 VM에 대해 동일하게 유지됩니다. 이전에 MAC 주소는 VM이 중지(할당 취소)되는 경우에 해제되었지만 이제 VM이 할당 취소된 상태에 있을 때에도 MAC 주소는 유지됩니다.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>VNet의 VM에서 인터넷에 연결할 수 있습니까?
예. VNet 내에 배포된 모든 VM 및 Cloud Services 역할 인스턴스는 인터넷에 연결할 수 있습니다.

## <a name="azure-services-that-connect-to-vnets"></a>VNet에 연결하는 Azure 서비스

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>VNet에 Azure App Service Web Apps를 사용할 수 있습니까?
예. ASE(App Service Environment)를 사용하여 VNet 내부에 Web Apps를 배포할 수 있습니다. VNet에 대해 지점 및 사이트 간 연결이 구성된 경우 VNet에서 모든 Web Apps를 안전하게 연결하고 리소스에 액세스할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* [App Service 환경에서 Web Apps 만들기](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Azure Virtual Network에 앱 통합](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Web Apps를 통해 VNet 통합 및 하이브리드 연결 사용](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>VNet에서 웹 및 작업자 역할(PaaS)을 사용하여 Cloud Services를 배포할 수 있습니까?
예. (선택 사항) VNet 내에서 Cloud Services 역할 인스턴스를 배포할 수 있습니다. 이를 수행하려면 서비스 구성의 네트워크 구성 섹션에서 VNet 이름 및 역할/서브넷 매핑을 지정합니다. 이진 파일을 업데이트할 필요가 없습니다.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>VMSS(Virtual Machine Scale Set)를 VNet에 연결할 수 있습니까?
예. VMSS를 VNet에 연결해야 합니다.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>VNet에 리소스를 배포할 수 있는 전체 Azure 서비스 목록이 있습니까?
네, 자세한 내용은 [Azure 서비스에 대한 가상 네트워크 통합](virtual-network-for-azure-services.md)을 참조하세요.

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>VNet에 대한 액세스를 제한할 수 있는 Azure PaaS 리소스는 무엇입니까?

일부 Azure PaaS 서비스(예: Azure Storage 및 Azure SQL Database)를 통해 배포된 리소스는 가상 네트워크 서비스 엔드포인트를 사용하여 VNet의 리소스만으로 네트워크 액세스를 제한할 수 있습니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트 개요](virtual-network-service-endpoints-overview.md)를 참조하세요.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>서비스를 VNet 내부 및 외부로 이동할 수 있습니까?
아니요. 서비스를 VNet 내부 및 외부로 이동할 수 없습니다. 리소스를 다른 VNet으로 옮기려면 리소스를 삭제하고 다시 배포해야 합니다.

## <a name="security"></a>보안

### <a name="what-is-the-security-model-for-vnets"></a>VNet에 대한 보안 모델은 무엇입니까?
VNet은 서로 격리되고 Azure 인프라에서 호스팅되는 다른 서비스와 격리됩니다. VNet은 트러스트 경계입니다.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>VNet에 연결된 리소스에 대해 인바운드 또는 아웃바운드 트래픽 흐름을 제한할 수 있습니까?
예. VNet 또는 둘 다에 연결된 VNet, NIC 내에서 개별 서브넷에 [네트워크 보안 그룹](security-overview.md)을 적용할 수 있습니다.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>VNet에 연결된 리소스 간에 방화벽을 구현할 수 있습니까?
예. Azure Marketplace를 통해 여러 공급업체에서 [방화벽 네트워크 가상 어플라이언스](https://azure.microsoft.com/marketplace/?term=firewall)를 배포할 수 있습니다.

### <a name="is-there-information-available-about-securing-vnets"></a>VNet 보안에 사용 가능한 정보가 있습니까?
예. 자세한 내용은 [Azure 네트워크 보안 개요](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="apis-schemas-and-tools"></a>API, 스키마 및 도구

### <a name="can-i-manage-vnets-from-code"></a>코드에서 VNet을 관리할 수 있습니까?
예. [Azure Resource Manager](/rest/api/virtual-network) 및 [클래식(서비스 관리)](https://go.microsoft.com/fwlink/?LinkId=296833) 배포 모델에서 VNet에 대해 REST API를 사용할 수 있습니다.

### <a name="is-there-tooling-support-for-vnets"></a>VNet에 대한 도구 지원이 있습니까?
예. 사용에 대한 자세한 정보:
- [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) 및 [클래식](virtual-networks-create-vnet-classic-pportal.md) 배포 모델을 통해 VNet을 배포하는 Azure Portal.
- [리소스 관리자](/powershell/module/az.network) 및 [클래식](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) 배포 모델을 통해 배포된 VNet을 관리하는 PowerShell.
- [Resource Manager](/cli/azure/network/vnet) 및 [클래식](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) 배포 모델을 통해 배포된 VNet을 배포하고 관리하는 Azure CLI(명령줄 인터페이스).  

## <a name="vnet-peering"></a>VNet 피어링

### <a name="what-is-vnet-peering"></a>VNet 피어링이란?
VNet 피어링(또는 가상 네트워크 피어링)을 통해 가상 네트워크에 연결할 수 있습니다. 가상 네트워크 간의 VNet 피어링 연결을 사용하면 IPv4 주소를 통해 개인적으로 가상 네트워크 간의 트래픽을 라우팅할 수 있습니다. 피어링된 VNet에서 가상 머신은 동일한 네트워크 내에 있는 것처럼 서로 통신할 수 있습니다. 이러한 가상 네트워크는 동일한 지역 또는 서로 다른 지역에 있을 수 있습니다(글로벌 VNet 피어링으로도 알려짐). Azure 구독 간에 VNet 피어링 연결을 만들 수도 있습니다.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>다른 지역의 VNet에 피어로 연결할 수 있나요?
예. 글로벌 VNet 피어링을 사용하면 다른 지역의 VNet에 피어로 연결할 수 있습니다. 글로벌 VNet 피어링은 모든 Azure 공용 지역 및 중국 클라우드 지역에서 사용할 수 있습니다. Azure 공용 지역에서 국가별 클라우드 지역으로 전역적으로 피어링할 수 없습니다. 글로벌 피어링은 현재 국가별 클라우드에서 제공되지 않습니다.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>글로벌 VNet 피어 링 및 부하 분산 장치 관련 된 제약 조건 이란?
두 가상 네트워크가 다른 지역 (글로벌 VNet 피어 링)에 있는 경우에 기본 Load Balancer를 사용 하는 리소스에 연결할 수 없습니다. 표준 Load Balancer를 사용 하는 리소스에 연결할 수 있습니다.
다음 리소스는 글로벌 VNet 피어 링에서 통신할 수 없어 해당 의미는 기본 부하 분산 장치를 사용 합니다.
- 기본 부하 분산 장치 뒤의 Vm
- 기본 부하 분산 장치를 사용 하 여 VM Scale Sets 
- Redis Cache 
- Application Gateway (v1) SKU
- Service Fabric
- SQL Always on
- SQL MI
- API 관리
- ADDS
- Logic Apps
- HD Insight
-   Azure Batch
- AKS
- App Service 환경

VNet 대 VNet을 통해 VNet 게이트웨이 또는 ExpressRoute를 통해 이러한 리소스에 연결할 수 있습니다.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>내 가상 네트워크가 다른 Azure Active Directory 테넌트의 구독에 속한 경우 VNet 피어링을 사용할 수 있나요?
예. 현재 구독이 다른 Azure Active Directory 테넌트에 속한 경우 로컬 또는 글로벌 VNet 피어링을 설정할 수 없습니다. PowerShell 또는 CLI에서는 피어링을 설정할 수 있습니다. 포털은 아직 지원되지 않습니다.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>내 VNet 피어링 연결이 *시작됨* 상태입니다. 왜 연결할 수 없나요?
피어링 연결이 시작됨 상태라면 한 링크만 만든 것입니다. 성공적인 연결을 위해서는 양방향 연결을 만들어야 합니다. 예를 들어, VNet A를 VNet B에 피어로 연결하려면 VNetA로부터 VNetB와, VNetB로부터 VNetA로의 연결을 만들어야 합니다. 두 연결을 만들면 상태가 *연결됨*으로 바뀝니다.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>내 VNet 피어링 연결이 *연결 끊김* 상태에 있습니다. 피어링 연결을 만들 수 없는 이유는 무엇인가요?
VNet 피어링 연결이 연결 끊김 상태에 있는 경우 만들어진 링크 중 하나가 삭제된 것을 의미합니다. 피어링 연결을 다시 설정하기 위해 링크를 삭제하고 다시 만들어야 합니다.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>다른 구독의 VNet과 내 VNet을 피어로 연결할 수 있나요?
예. 구독 및 지역 간에 VNet을 피어로 연결할 수 있습니다.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>주소 범위가 일치하거나 겹치는 두 VNet을 피어로 연결할 수 있나요?
아니요. VNet 피어링을 사용하려면 주소 공간이 겹치지 않아야 합니다.

### <a name="how-much-do-vnet-peering-links-cost"></a>VNet 피어링 연결 비용은 얼마나 되나요?
VNet 피어링 연결을 만드는 데는 비용이 없습니다. 피어링 연결 간의 데이터 전송에는 요금이 청구됩니다. [여기](https://azure.microsoft.com/pricing/details/virtual-network/)를 참조하세요.

### <a name="is-vnet-peering-traffic-encrypted"></a>VNet 피어링 트래픽은 암호화되나요?
아니요. 피어로 연결된 VNet의 리소스 간 트래픽은 비공개이며 격리됩니다. Microsoft 백본에 그대로 남아 있습니다.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>내 피어링 연결이 끊어진 상태인 이유는 무엇인가요?
VNet 피어링 연결은 한 VNet 연결이 삭제되면 *연결 끊김* 상태가 됩니다. 다시 피어링 연결에 성공하려면 두 링크를 모두 삭제해야 합니다.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>VNetA와 VNetB,VNetB와 VNetC를 피어로 연결하면 VNetA와 VNetC가 피어로 연결된 것인가요?
아니요. 전이적 피어링은 지원되지 않습니다. 이를 위해서는 VNetA와 VNetC를 피어로 연결해야 합니다.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>피어링 연결에 대역폭 제한이 있나요?
아니요. 로컬이든 글로벌이든 VNet 피어링에는 대역폭 제한이 없습니다. 대역폭은 VM 또는 계산 리소스에 의해서만 제한됩니다.

## <a name="virtual-network-tap"></a>가상 네트워크 TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>가상 네트워크 TAP을 사용할 수 있는 Azure 지역은 어디인가요?
가상 네트워크 탭 미리 보기는 모든 Azure 지역에서 사용할 수 있습니다. 모니터링된 네트워크 인터페이스, 가상 네트워크 TAP 리소스 및 수집기 또는 분석 솔루션은 동일한 지역에 배포되어야 합니다.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Virtual Network TAP은 미러링된 패킷에서 모든 필터링 기능을 지원하나요?
필터링 기능은 가상 네트워크 TAP 미리 보기에서 지원되지 않습니다. TAP 구성이 네트워크 인터페이스에 추가되는 경우 네트워크 인터페이스에서 모든 송수신 트래픽의 전체 복사본은 TAP 대상으로 스트리밍됩니다.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>여러 TAP 구성을 모니터링된 네트워크 인터페이스에 추가할 수 있나요?
모니터링된 네트워크 인터페이스에는 하나의 TAP 구성만 있을 수 있습니다. 기능에 대한 개별 [파트너 솔루션](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)이 사용자가 선택한 분석 도구로 TAP 트래픽의 여러 복사본을 스트리밍하는지 확인합니다.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>동일한 가상 네트워크 TAP 리소스가 하나를 초과하는 가상 네트워크의 모니터링된 네트워크 인터페이스에서 트래픽을 집계할 수 있나요?
예. 동일한 구독 또는 다른 구독에서 피어링된 가상 네트워크의 모니터링된 네트워크 인터페이스에서 미러링된 트래픽을 집계하는 데 동일한 가상 네트워크 TAP 리소스를 사용할 수 있습니다. 가상 네트워크 TAP 리소스 및 대상 부하 분산 장치 또는 대상 네트워크 인터페이스는 동일한 구독에 있어야 합니다. 모든 구독은 동일한 Azure Active Directory 테넌트 아래 있어야 합니다.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>네트워크 인터페이스에서 가상 네트워크 TAP 구성을 사용하도록 설정하는 경우 프로덕션 트래픽에 대한 성능 고려 사항이 있나요?

가상 네트워크 탭 미리 보기입니다. 미리 보기 중에는 서비스 수준 계약이 없습니다. 프로덕션 워크 로드에 기능을 사용할 수 없습니다. TAP 구성을 통해 가상 머신 네트워크 인터페이스를 사용하도록 설정하는 경우 Azure 호스트에서 프로덕션 트래픽을 보내기 위해 가상 머신에 할당된 동일한 리소스는 미러링 함수를 수행하고 미러링된 패킷을 보내는 데 사용됩니다. 올바른 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신 크기를 선택하여 가상 머신이 프로덕션 트래픽 및 미러링된 트래픽을 보내기 위해 충분한 리소스를 사용할 수 있는지 확인합니다.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>[Linux](create-vm-accelerated-networking-cli.md) 또는 [Windows](create-vm-accelerated-networking-powershell.md)에 대해 가속화된 네트워킹은 가상 네트워크 TAP에서 지원되나요?

가속화된 네트워킹에서 사용하도록 설정된 가상 머신과 연결된 네트워크 인터페이스에 TAP 구성을 추가할 수 있습니다. 하지만 현재 Azure 가속 네트워킹에서 미러링 트래픽에 대한 오프로드를 지원하지 않으므로 TAP 구성을 추가하여 가상 머신의 성능 및 대기 시간에 영향을 주게 됩니다.

## <a name="virtual-network-service-endpoints"></a>가상 네트워크 서비스 엔드포인트

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Azure 서비스에 대한 서비스 엔드포인트를 설정하기 위한 올바른 작업 순서는 무엇인가요?
서비스 엔드포인트를 통해 Azure 서비스 리소스를 보호하는 두 단계는 다음과 같습니다.
1. Azure 서비스에 대한 서비스 엔드포인트를 켭니다.
2. Azure 서비스에서 VNet ACL을 설정합니다.

첫 번째 단계는 네트워크 쪽 작업이고, 두 번째 단계는 서비스 리소스 쪽 작업입니다. 두 단계 모두 관리자 역할에 부여된 RBAC 권한을 기준으로 동일한 관리자 또는 서로 다른 관리자가 수행할 수 있습니다. Azure 서비스 쪽에서 VNet ACL을 설정하기 전에 먼저 가상 네트워크에 대한 서비스 엔드포인트를 켜는 것이 좋습니다. 따라서 VNet 서비스 엔드포인트를 설정하려면 위에 나열된 순서대로 단계를 수행해야 합니다.

>[!NOTE]
> 위에서 설명한 작업을 둘 다 완료해야 허용된 VNet 및 서브넷에 대한 Azure 서비스 액세스를 제한할 수 있습니다. 네트워크 쪽에서만 Azure 서비스에 대한 서비스 엔드포인트를 켜면 제한된 액세스가 제공되지 않습니다. Azure 서비스 쪽에서도 VNet ACL을 설정해야 합니다.

특정 서비스(예: SQL 및 CosmosDB)는 **IgnoreMissingVnetServiceEndpoint** 플래그를 통해 위의 시퀀스에 대한 예외를 허용합니다. 플래그를 **True**로 설정하면 네트워크 쪽에서 서비스 엔드포인트를 설정하기 전에 Azure 서비스 쪽에서 VNet ACL을 설정할 수 있습니다. Azure 서비스는 Azure 서비스에 특정 IP 방화벽이 구성된 경우 고객을 지원하기 위해 이 플래그를 제공하며, 네트워크 쪽에서 서비스 엔드포인트를 켜면 원본 IP가 공용 IPv4 주소에서 개인 주소로 변경되어 연결이 삭제될 수 있습니다. 네트워크 쪽에서 서비스 엔드포인트를 설정하기 전에 Azure 서비스 쪽에서 VNet ACL을 설정하면 연결 삭제를 방지할 수 있습니다.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>모든 Azure 서비스가 고객이 제공하는 Azure 가상 네트워크에 상주하나요? VNet 서비스 엔드포인트가 Azure 서비스에서 어떻게 작동하나요?

아니요, 일부 Azure 서비스는 고객의 가상 네트워크에 상주하지 않습니다. Azure Storage, Azure SQL, Azure Cosmos DB 등 대부분의 Azure 데이터 서비스는 공용 IP 주소를 통해 액세스할 수 있는 다중 테넌트 서비스입니다. [여기서](virtual-network-for-azure-services.md) Azure 서비스에 대한 가상 네트워크 통합을 자세히 알아볼 수 있습니다. 

VNet 서비스 엔드포인트 기능(네트워크 쪽에서 VNet 서비스 엔드포인트를 켜고 Azure 서비스 쪽에서 적절한 VNet ACL 설정)을 사용하는 경우, Azure 서비스에 대한 액세스가 허용된 VNet 및 서브넷으로 제한됩니다.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>VNet 서비스 엔드포인트는 어떻게 보안을 제공하나요?

VNet 서비스 엔드포인트 기능(네트워크 쪽에서 VNet 서비스 엔드포인트를 켜고 Azure 서비스 쪽에서 적절한 VNet ACL 설정)은 Azure 서비스 액세스를 허용된 VNet 및 서브넷으로 제한하므로 네트워크 수준 보안 및 Azure 서비스 트래픽 격리를 제공합니다. VNet 서비스 엔드포인트를 사용하는 모든 트래픽이 Microsoft 백본을 통해 전송되므로 공용 인터넷에서 또 다른 격리 계층이 제공됩니다. 또한 고객이 Azure 서비스 리소스에 대한 공용 인터넷 액세스를 완전히 제거하고 해당 가상 네트워크에서 IP 방화벽 및 VNet ACL의 조합을 통과하는 트래픽만 허용하도록 선택하여 Azure 서비스 리소스를 무단 액세스로부터 보호할 수 있습니다.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>VNet 서비스 엔드포인트는 VNet 리소스 또는 Azure 서비스 중 무엇을 보호하나요?
VNet 서비스 엔드포인트는 Azure 서비스 리소스 보호에 도움이 됩니다. VNet 리소스는 NSG(네트워크 보안 그룹)를 통해 보호됩니다.

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>VNet 서비스 엔드포인트를 사용하는 데 비용이 드나요?

아니요, VNet 서비스 엔드포인트를 사용하는 데 필요한 추가 비용은 없습니다.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>가상 네트워크 및 Azure 서비스 리소스가 서로 다른 구독에 속해 있는 경우 VNet 서비스 엔드포인트를 켜고 VNet ACL을 설정할 수 있나요?

예, 구성할 수 있습니다. 가상 네트워크 및 Azure 서비스 리소스는 동일한 구독이나 서로 다른 구독에 있을 수 있습니다. 유일한 요구사항은 가상 네트워크와 Azure 서비스 리소스가 모두 AD(Active Directory) 테넌트에 있어야 한다는 것입니다.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>가상 네트워크 및 Azure 서비스 리소스가 서로 다른 AD 테넌트에 속해 있는 경우 VNet 서비스 엔드포인트를 켜고 VNet ACL을 설정할 수 있나요?
아니요, AD 테넌트 간에는 VNet 서비스 엔드포인트와 VNet AC이 지원되지 않습니다.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-express-route-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Azure Virtual Network 게이트웨이(VPN) 또는 Express 경로 게이트웨이를 통해 연결되는 온-프레미스 디바이스의 IP 주소가 VNet 서비스 엔드포인트를 통해 Azure PaaS Service에 액세스할 수 있나요?
기본적으로 가상 네트워크에 대해 보호된 Azure 서비스 리소스는 온-프레미스 네트워크에서 연결할 수 없습니다. 온-프레미스의 트래픽을 허용하려는 경우 온-프레미스 또는 ExpressRoute의 공용 IP 주소(일반적으로 NAT)도 허용해야 합니다. Azure 서비스 리소스에 대한 IP 방화벽 구성을 통해 해당 IP 주소를 추가할 수 있습니다.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-with-in-a-virtual-network-or-across-multiple-virtual-networks"></a>VNet 서비스 엔드포인트 기능을 사용하여 여러 가상 네트워크 또는 단일 가상 네트워크의 여러 서브넷에 대한 Azure 서비스를 보호할 수 있나요?
여러 가상 네트워크 또는 단일 가상 네트워크의 여러 서브넷에 대한 Azure 서비스를 보호하려면 각 서브넷의 네트워크 쪽에서 독립적으로 서비스 엔드포인트를 사용하도록 설정한 다음, Azure 서비스 쪽에서 적절한 VNet ACL을 설정하여 모든 서브넷에 대한 Azure 서비스 리소스를 보호합니다.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>가상 네트워크에서 Azure 서비스로 전송되는 아웃바운드 트래픽을 필터링하고 서비스 엔드포인트를 계속 사용하려면 어떻게 해야 하나요?
가상 네트워크에서 Azure 서비스로 대상이 지정된 트래픽을 검사하거나 필터링하려는 경우 가상 네트워크 내에 네트워크 가상 어플라이언스를 배포할 수 있습니다. 네트워크 가상 어플라이언스를 배포한 서브넷에 서비스 엔드포인트를 적용하고, VNet ACL을 통해 이 서브넷에 대한 Azure 서비스 리소스만 보호할 수 있습니다. 네트워크 가상 어플라이언스 필터링을 사용하여 가상 네트워크에서 특정 Azure 리소스로의 Azure 서비스 액세스만 제한하려는 경우에도 이 시나리오가 유용할 수 있습니다. 자세한 내용은 [네트워크 가상 어플라이언스에서 송신](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)을 참조하세요.

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>VNet 외부에서 가상 네트워크 ACL(액세스 제어 목록)이 사용하도록 설정된 Azure 서비스 계정에 액세스하면 어떻게 되나요?
HTTP 403 또는 HTTP 404 오류가 반환됩니다.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>각 지역에 만든 가상 네트워크의 서브넷이 다른 지역에 있는 Azure 서비스 계정에 액세스할 수 있나요? 
예, 대부분의 Azure 서비스에서 각 지역에 만든 가상 네트워크가 VNet 서비스 엔드포인트를 통해 다른 지역의 Azure 서비스에 액세스할 수 있습니다. 예를 들어 Azure Cosmos DB 계정이 미국 서부 또는 미국 동부에 있고 가상 네트워크가 여러 지역에 있는 경우 가상 네트워크가 Azure Cosmos DB에 액세스할 수 있습니다. Storage 및 SQL은 예외이며, 기본적으로 특정 지역과 관련이 있고 가상 네트워크와 Azure 서비스가 둘 다 동일한 지역에 있어야 합니다.
  
### <a name="can-an-azure-service-have-both-vnet-acl-and-an-ip-firewall"></a>Azure 서비스에 VNet ACL과 IP 방화벽을 둘 다 사용할 수 있나요?
예, VNet ACL과 IP 방화벽은 공존할 수 있습니다. 두 기능이 서로 보완하여 격리 및 보안을 보장합니다.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Azure 서비스에 대해 서비스 엔드포인트가 켜진 가상 네트워크 또는 서브넷을 삭제하면 어떻게 되나요?
VNets 및 서브넷 삭제는 독립적인 작업이며, Azure 서비스에 대해 서비스 엔드포인트가 켜진 경우에도 지원됩니다. Azure 서비스에 VNet ACL이 설정되어 있는 경우 VNet 서비스 엔드포인트가 켜진 VNet 또는 서브넷을 삭제하면 해당 VNet 및 서브넷에 대해 Azure 서비스와 연결된 VNet ACL 정보를 사용할 수 없게 됩니다.
 
### <a name="what-happens-if-azure-service-account-that-has-vnet-service-endpoint-enabled-is-deleted"></a>VNet 서비스 엔드포인트가 사용하도록 설정된 Azure 서비스 계정을 삭제하면 어떻게 되나요?
Azure 서비스 계정 삭제는 독립적인 작업이며, 네트워크 쪽에서 서비스 엔드포인트가 사용하도록 설정되고 Azure 서비스 쪽에서 VNet ACL이 설정된 경우에도 지원됩니다. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>VNet 서비스 엔드포인트가 사용하도록 설정된, 서브넷의 VM과 같은 리소스의 원본 IP 주소는 어떻게 되나요?
가상 네트워크 서비스 엔드포인트를 사용하도록 설정한 경우 가상 네트워크 서브넷에 있는 리소스의 원본 IP 주소가 Azure 서비스로의 트래픽에 대해 공용 IPV4 주소 사용에서 Azure 가상 네트워크의 개인 IP 주소로 전환됩니다. 이로 인해 Azure 서비스에서 이전에 공용 IPV4 주소로 설정된 특정 IP 방화벽이 실패할 수 있습니다. 

### <a name="does-service-endpoint-route-always-take-precedence"></a>서비스 엔드포인트가 항상 우선 적용되나요?
서비스 엔드포인트는 BGP 경로보다 우선 적용되는 시스템 경로를 추가하고 서비스 엔드포인트 트래픽에 대해 최적의 라우팅을 제공합니다. 서비스 엔드포인트는 가상 네트워크의 서비스 트래픽을 직접 Microsoft Azure 백본 네트워크의 서비스로 항상 이동합니다. Azure에서 경로 선택 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure 가상 네트워크 트래픽 라우팅을](virtual-networks-udr-overview.md)합니다.
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>서브넷의 NSG는 서비스 엔드포인트에서 어떻게 작동하나요?
Azure 서비스에 연결하려면 NSG에서 아웃바운드 연결을 허용해야 합니다. NSG가 모든 인터넷 아웃바운드 트래픽에 대해 열려 있는 경우 서비스 엔드포인트 트래픽이 작동합니다. 서비스 태그만 사용하여 아웃바운드 트래픽을 서비스 IP로 제한할 수도 있습니다.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>서비스 엔드포인트를 설정하는 데 필요한 사용 권한은 무엇인가요?
가상 네트워크에 대한 쓰기 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 엔드포인트를 구성할 수 있습니다. VNet에 대한 Azure 서비스 리소스를 보호하려면 사용자는 추가되는 서브넷의 **Microsoft.Network/JoinServicetoaSubnet**에 대한 사용 권한이 있어야 합니다. 이 권한은 기본적으로 기본 제공 서비스 관리자 역할에 포함되고 사용자 지정 역할을 만들어 수정할 수 있습니다. 기본 제공 역할 및 [사용자 지정 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 사용 권한을 할당하는 방법에 대해 자세히 알아보세요.
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Azure 서비스에 대한 가상 네트워크 트래픽을 필터링하여 VNet 서비스 엔드포인트를 통해 특정 Azure 서비스 리소스만 허용할 수 있습니다. 

VNet(가상 네트워크) 서비스 엔드포인트 정책을 통해 가상 네트워크 트래픽을 Azure 서비스로 필터링하여 서비스 엔드포인트를 통해 특정 Azure 서비스 리소스만 허용할 수 있습니다. 엔드포인트 정책은 Azure 서비스로 전송되는 가상 네트워크 트래픽에 대한 세부적인 액세스 제어를 제공합니다. [여기서](virtual-network-service-endpoint-policies-overview.md) 서비스 엔드포인트 정책에 대해 자세히 알아볼 수 있습니다.
 
### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>VNet에서 설정할 수 있는 VNet 서비스 엔드포인트 개수에 대한 제한이 있나요?
가상 네트워크에서 VNet 서비스 엔드포인트의 총수에 대한 제한은 없습니다. Azure 서비스 리소스(예: Azure Storage 계정)의 경우 서비스는 리소스 보안에 사용되는 서브넷의 수에 제한을 적용할 수 있습니다. 다음 표에서는 몇 가지 제한 예를 보여 줍니다. 

|||
|---|---|
|Azure 서비스| VNet 규칙에 대한 제한|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> 제한은 Azure 서비스에서 임의로 변경될 수 있습니다. 서비스에 대한 자세한 내용은 해당 서비스 문서를 참조하세요. 




  



