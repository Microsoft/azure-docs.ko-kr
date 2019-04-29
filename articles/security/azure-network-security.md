---
title: Azure 네트워크 보안 | Microsoft Docs
description: 애플리케이션 또는 엔터프라이즈의 요구 사항을 충족하기 위해 자동으로 확장하거나 축소할 수 있는 다양한 계산 인스턴스와 서비스를 포함하는 클라우드 기반 컴퓨팅 서비스에 대해 알아봅니다.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f684a9d7bca77a8aa3aa60f5079dda0ce3b58a1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587468"
---
# <a name="azure-network-security"></a>Azure 네트워크 보안

보안은 클라우드의 작업이며 Azure 보안에 대한 정확하고 시기 적절한 정보를 찾는 것이 얼마나 중요한지 알고 있습니다. Azure를 애플리케이션과 서비스에 사용하는 가장 좋은 이유 중 하나는 광범위한 보안 도구와 기능을 활용한다는 점입니다. 이러한 도구 및 기능을 통해 Azure 플랫폼에서 안전한 솔루션을 만들 수 있습니다.

Microsoft Azure는 고객 데이터의 기밀성, 무결성 및 가용성을 제공하는 한편 투명한 책임도 가능하게 합니다. 고객 관점에서 Microsoft Azure 내에 구현된 네트워크 보안 제어 모음을 더 잘 이해할 수 있도록 Microsoft Azure에서 제공되는 네트워크 보안 제어를 포괄적으로 살펴볼 수 있는 "Azure 네트워크 보안" 문서가 작성되었습니다.

이 문서는 Azure에서 배포하는 솔루션의 보안을 강화하기 위해 구성할 수 있는 다양한 범위의 네트워크 제어에 대한 정보를 제공하기 위해 작성되었습니다. Microsoft가 Azure 플랫폼의 네트워크 패브릭 보안 유지를 위해 수행하는 작업에 관심이 있는 경우 [Microsoft 보안 센터](https://microsoft.com/en-us/trustcenter/cloudservices/azure)의 Azure 보안 섹션을 참조하세요.

## <a name="azure-platform"></a>Azure 플랫폼

Azure는 다양한 운영 체제, 프로그래밍 언어, 프레임워크, 도구, 데이터베이스 및 디바이스를 지원하는 공용 클라우드 서비스 플랫폼입니다.  Docker 통합으로 Linux 컨테이너를 실행할 수 있습니다. JavaScript, Python, .NET, PHP, Java 및 Node.js를 사용하여 앱을 빌드할 수 있습니다. iOS, Android 및 Windows 디바이스용 백 엔드를 빌드할 수 있습니다. Azure 클라우드 서비스는 수백만의 개발자 및 IT 전문가가 이미 믿고 사용하고 있는 동일한 수준의 기술을 지원합니다.

IT 자산을 만들거나 공용 클라우드 서비스 공급자로 마이그레이션하는 경우 조직에서 제공하는 서비스와 제어를 통해 애플리케이션과 데이터를 보호할 수 있는 해당 조직의 능력에 의존하여 클라우드 기반 자산의 보안을 관리합니다.

Azure의 인프라는 수백만 고객을 동시에 호스팅하는 애플리케이션에 맞게 시설로부터 설계되었으며, 비즈니스에서 보안 요구 사항을 충족할 수 있는 신뢰할 수 있는 기반을 제공합니다. 또한 Azure는 구성 가능한 포괄적인 보안 옵션 모음과 이를 제어하는 기능을 제공하므로 보안을 사용자 지정하여 조직의 고유한 배포 요구 사항을 충족할 수 있습니다.

## <a name="abstract"></a>요약

Microsoft 공용 클라우드 서비스는 대규모 서비스와 인프라, 엔터프라이즈급 기능 및 여러 하이브리드 연결 옵션을 제공합니다. 사용자는 인터넷이나, 사설 네트워크 연결을 제공하는 Azure ExpressRoute를 통해 이러한 서비스에 액세스할 수 있습니다. 사용자는 Microsoft Azure 플랫폼을 통해 인프라를 클라우드로 자연스럽게 확장하고 다중 계층 아키텍처를 빌드할 수 있습니다. 또한 타사 보안 서비스 및 가상 어플라이언스를 통해 향상된 기능을 구현할 수 있습니다.

Azure의 네트워크 서비스는 기본적으로 유연성, 가용성, 탄력성, 보안 및 무결성을 최대화합니다. 이 백서는 Azure의 네트워킹 기능과 고객이 Azure의 네이티브 보안 기능을 사용하여 정보 자산을 보호하는 방법에 대한 자세한 내용을 제공합니다.

이 백서의 대상 사용자는 다음과 같습니다.

- Azure에서 사용 가능하고 지원되는 보안 솔루션을 찾고 있는 기술 관리자, 네트워크 관리자 및 개발자

-   Azure 공용 클라우드의 네트워크 보안과 관련해서 논의 중인 Azure 네트워킹 기술 및 서비스에 대한 고급 정보를 원하는 SME 또는 비즈니스 프로세스 실무 담당자

## <a name="azure-networking-big-picture"></a>Azure 네트워킹의 전반적 이해
Microsoft Azure에는 사용자의 애플리케이션과 서비스 연결 요구 사항을 지원하기 위한 강력한 네트워킹 인프라가 포함되어 있습니다. 네트워크 연결은 Azure에 위치한 리소스 간, 온-프레미스와 Azure 호스팅 리소스 간, 그리고 인터넷과 Azure 간에 가능합니다.

![Azure 네트워킹의 전반적 이해](media/azure-network-security/azure-network-security-fig-1.png)

[Azure 네트워크 인프라](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines)를 사용하면 Azure 리소스와 VNet(가상 네트워크)을 서로 안전하게 연결할 수 있습니다. VNet은 클라우드에 있는 사용자의 네트워크를 나타내며, 구독 전용 Azure 클라우드 네트워크를 논리적으로 격리한 것이 VNet입니다. 또한 온-프레미스 네트워크에 VNet을 연결할 수 있습니다.

Azure는 온-프레미스 네트워크에 대한 전용 WAN 링크 연결과 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 통한 Azure Virtual Network를 지원합니다. Azure와 사이트 간의 링크는 공용 인터넷을 사용하지 않는 전용 연결을 사용합니다. Azure 애플리케이션이 여러 데이터 센터에서 실행되는 경우 [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)를 사용하여 사용자 요청을 적절하게 여러 애플리케이션 인스턴스에 라우팅할 수 있습니다. 또한 인터넷에서 액세스할 수 있는 경우 Azure에서 실행되지 않는 서비스로 트래픽을 라우팅할 수 있습니다.

## <a name="enterprise-view-of-azure-networking-components"></a>Azure 네트워킹 구성 요소의 엔터프라이즈 보기
Azure는 네트워크 보안 논의가 이루어지는 많은 네트워킹 구성 요소를 포함합니다. 여기서는 이러한 네트워킹 구성 요소에 대해 설명하고 관련 보안 문제를 집중적으로 살펴봅니다.

> [!Note]
> Azure 네트워킹의 모든 측면을 설명하지는 못하며, Azure에서 배포하는 서비스 및 애플리케이션과 관련된 보안 네트워크 인프라의 계획 및 디자인에 중요한 측면만 다룹니다.

이 문서에서는 다음 Azure 네트워킹 엔터프라이즈 기능을 소개합니다.

-   기본 네트워크 연결

-   하이브리드 연결

-   Single Sign On 설정

-   네트워크 유효성 검사

### <a name="basic-network-connectivity"></a>기본 네트워크 연결

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 서비스를 사용하면 Azure 리소스와 가상 네트워크(VNet)를 서로 안전하게 연결할 수 있습니다. VNet은 클라우드에 있는 사용자의 네트워크를 나타내며, VNet은 구독 전용 Azure 네트워크 인프라의 논리적 격리입니다. 사이트 간 VPN 및 전용 [WAN 링크](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 사용하여 VNet을 서로 연결하고 온-프레미스 네트워크에 연결할 수도 있습니다.

![기본 네트워크 연결](media/azure-network-security/azure-network-security-fig-2.png)

VM을 사용하여 Azure에서 서버가 호스트된다는 사실을 이해하고 있다면 해당 VM이 네트워크에 연결되는 방식이 궁금할 것입니다. VM은 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)에 연결됩니다.

Azure Virtual Network는 Microsoft Hyper-V 또는 VMware 등의 고유한 가상화 플랫폼 솔루션과 함께 온-프레미스에서 사용하는 가상 네트워크와 같습니다.

#### <a name="intra-vnet-connectivity"></a>VNet 내 연결

VNet을 서로 연결하여 VNet에 연결된 리소스가 VNet을 통해 서로 통신하도록 할 수 있습니다. 다음 옵션 중 하나 또는 둘 다를 사용하여 VNet을 서로 연결할 수 있습니다.

- **피어링:** 같은 Azure 지역 내의 서로 다른 Azure VNet에 연결되어 있는 리소스가 상호 통신할 수 있도록 합니다. VNet 내의 대역폭 및 대기 시간은 리소스가 동일한 VNet에 연결된 경우와 같습니다. 피어링에 대한 자세히 내용은 [가상 네트워크 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 참조하세요.

  ![피어링](media/azure-network-security/azure-network-security-fig-3.png)

- **VNet 간 연결:** 같거나 서로 다른 Azure 지역에서 서로 다른 Azure VNet에 연결되어 있는 리소스가 상호 통신할 수 있도록 합니다. 피어링과 달리, 트래픽이 Azure VPN Gateway를 통과해야 하기 때문에 VNet 간에 대역폭이 제한됩니다.

![VNet 간 연결](media/azure-network-security/azure-network-security-fig-4.png)


VNet 간 연결을 사용하여 VNet을 연결하는 방법에 대한 자세한 내용은 [VNet 간 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

#### <a name="azure-virtual-network-capabilities"></a>Azure Virtual Network 기능:

아시다시피 Azure Virtual Network는 안전한 방법으로 다른 네트워크 리소스에 연결할 수 있도록 네트워크에 연결하기 위한 가상 머신을 제공합니다. 그러나 기본적인 연결 기능 외에도 다양한 기능을 제공합니다. Azure Virtual Network 서비스의 다음 기능은 Azure Virtual Network의 보안 특성을 보여 줍니다.

-   격리

-   인터넷 연결

-   Azure 리소스 연결

-   VNet 연결

-   온-프레미스 연결

-   트래픽 필터링

-   라우팅

**격리**

VNet은 서로 완전히 [격리](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)됩니다. 동일한 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 주소 블록을 사용하는 개발, 테스트 및 프로덕션 환경에 대해 개별 VNet을 만들 수 있습니다. 반대로, 다른 CIDR 주소 블록을 사용하고 네트워크를 함께 연결하는 여러 VNet을 만들 수도 있습니다. VNet을 여러 서브넷으로 분할할 수 있습니다.

Azure는 VNet에 연결되는 VM 및 [Cloud Services](https://azure.microsoft.com/services/cloud-services/) 역할 인스턴스에 대한 내부 이름 확인을 제공합니다. 필요에 따라 Azure 내부 이름 확인을 사용하는 대신, 고유한 DNS 서버를 사용하도록 VNet을 구성할 수 있습니다.

각 Azure [구독](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 Azure [지역](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) 내에서 여러 VNet을 구현할 수 있습니다. 각 VNet은 다른 VNet에서 격리됩니다. 각 VNet에 대해 다음을 수행할 수 있습니다.

-   공용 및 개인(RFC 1918) 주소를 사용하여 사용자 지정 개인 IP 주소 공간을 지정합니다. Azure에서는 VNet에 연결된 리소스를 사용자가 할당한 주소 공간의 개인 IP 주소에 할당합니다.

-   VNet을 하나 이상의 서브넷으로 분할하고 VNet 주소 공간의 일부를 각 서브넷에 할당합니다.

-   Azure에서 제공되는 이름 확인을 사용하거나 VNet에 연결된 리소스에서 사용할 수 있도록 자체 DNS 서버를 지정합니다. VNet의 이름 확인에 대한 자세한 내용은 [VM 및 Cloud Services의 이름 확인](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)을 참조하세요.

**인터넷 연결**

VNet에 연결되는 모든 [Azure VM(Virtual Machines)](https://docs.microsoft.com/azure/virtual-machines/windows/) 및 Cloud Services 역할 인스턴스는 기본적으로 인터넷에 액세스할 수 있습니다. 또한 필요에 따라 특정 리소스에 대한 인바운드 액세스를 사용하도록 설정할 수도 있습니다. (VM) 및 VNet에 연결된 Cloud Services 역할 인스턴스는 기본적으로는 인터넷에 액세스할 수 있습니다. 또한 필요에 따라 특정 리소스에 대한 인바운드 액세스를 사용하도록 설정할 수 있습니다.

VNet에 연결된 모든 리소스는 기본적으로 인터넷에 아웃바운드로 연결됩니다. 리소스의 개인 IP 주소는 Azure 인프라에서 공용 IP 주소로 변환하는 원본 네트워크 주소입니다. 사용자 지정 라우팅 및 트래픽 필터링을 구현하여 기본 연결을 변경할 수 있습니다. 아웃바운드 인터넷 연결에 대한 자세한 내용은 [Azure에서 아웃바운드 연결 이해](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

인터넷에서 Azure 리소스에 인바운드로 통신하거나 SNAT 없이 인터넷에 아웃바운드로 통신하려면 공용 IP 주소에 리소스가 할당되어야 합니다. 공용 IP 주소에 대해 자세히 알아보려면 [공용 IP 주소](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)를 참조하세요.

**Azure 리소스 연결**

Cloud Services 및 VM과 같은 [Azure 리소스](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)를 동일한 VNet에 연결할 수 있습니다. 리소스는 서로 다른 서브넷에 있더라도 개인 IP 주소를 사용하여 서로 연결할 수 있습니다. Azure는 서브넷, VNet 및 온-프레미스 네트워크 간에 기본 라우팅을 제공하므로 경로를 구성하고 관리할 필요가 없습니다.

VM(Virtual Machines), Cloud Services, App Service Environment 및 Virtual Machine Scale Sets와 같은 일부 Azure 리소스를 VNet에 연결할 수 있습니다. VM은 NIC(네트워크 인터페이스)를 통해 VNet 내의 서브넷에 연결합니다. NIC에 대한 자세한 내용은 [네트워크 인터페이스](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)를 참조하세요.

**VNet 연결**

[VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)을 서로 연결하여 어떤 VNet에 연결된 리소스도 다른 VNet의 모든 리소스와 통신하도록 할 수 있습니다.

VNet을 서로 연결하여 VNet에 연결된 리소스가 VNet을 통해 서로 통신하도록 할 수 있습니다. 다음 옵션 중 하나 또는 둘 다를 사용하여 VNet을 서로 연결할 수 있습니다.

- **피어링:** 같은 Azure 지역 내의 서로 다른 Azure VNet에 연결되어 있는 리소스가 상호 통신할 수 있도록 합니다. VNet 내의 대역폭 및 대기 시간은 리소스가 동일한 VNet에 연결된 경우와 같습니다. 피어링에 대한 자세한 내용은 [Virtual Network 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 참조하세요.

- **VNet 간 연결:** 같거나 서로 다른 Azure 지역에서 서로 다른 Azure VNet에 연결되어 있는 리소스가 상호 통신할 수 있도록 합니다. 피어링과 달리, 트래픽이 Azure VPN Gateway를 통과해야 하기 때문에 VNet 간에 대역폭이 제한됩니다. VNet 간 연결을 사용하여 VNet을 연결하는 방법을 알아보세요. 자세한 내용은 [VNet 간 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

**온-프레미스 연결**

VNet을 네트워크와 Azure 간의 개인 네트워크 연결을 통해 또는 인터넷의 사이트 간 VPN 연결을 통해 [온-프레미스](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 네트워크에 연결할 수 있습니다.

다음 옵션을 조합하여 온-프레미스 네트워크를 VNet에 연결할 수 있습니다.

- **지점 및 사이트 간 VPN(가상 사설망):** 네트워크에 연결된 단일 PC와 VNet 간에 설정됩니다. 이 연결 유형은 기존 네트워크를 거의 변경할 필요가 없으므로 Azure을 이제 막 시작하는 사용자나 개발자에게 적합합니다. 이 연결은 SSTP 프로토콜을 사용하여 PC 및 VNet 간에 인터넷을 통한 암호화된 통신을 제공합니다. 트래픽이 인터넷을 통과하므로 지점 및 사이트 간 VPN의 대기 시간은 예측할 수 없습니다.

- **사이트 간 VPN:** VPN 디바이스와 Azure VPN Gateway 간에 설정됩니다. 이 연결 유형을 사용하면 권한을 부여한 모든 온-프레미스 리소스에서 VNet에 액세스할 수 있습니다. 이 연결은 온-프레미스 디바이스와 Azure VPN Gateway 간에 인터넷을 통한 암호화된 통신을 제공하는 IPSec/IKE VPN입니다. 트래픽이 인터넷을 통과하므로 사이트 간 연결의 대기 시간은 예측 가능하지 않습니다.

- **Azure ExpressRoute:** ExpressRoute 파트너를 통해 사용자 네트워크와 Azure 간에 설정됩니다. 이 연결은 사설 전용입니다. 트래픽이 인터넷을 통과하지 않습니다. 트래픽이 인터넷을 통과하지 않으므로 ExpressRoute 연결에 대한 대기 시간을 예측할 수 있습니다. 모든 이전 연결 옵션에 대한 자세한 내용은 [연결 토폴로지 다이어그램](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json)을 읽어보세요.

**트래픽 필터링**

VM 및 Cloud Services 역할 인스턴스 [네트워크 트래픽](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)을 원본 IP 주소 및 포트, 대상 IP 주소 및 포트 및 프로토콜별로 인바운드 및 아웃바운드에 따라 필터링할 수 있습니다.

다음 옵션 중 하나 또는 둘 다를 사용하여 서브넷 간의 네트워크 트래픽을 필터링할 수 있습니다.

- **NSG(네트워크 보안 그룹):** 각 NSG에는 원본 및 대상 IP 주소, 포트 및 프로토콜을 기준으로 트래픽을 필터링할 수 있는 여러 개의 인바운드 및 아웃바운드 보안 규칙이 포함될 수 있습니다. NSG는 VM의 각 NIC에 적용할 수 있습니다. NIC 또는 Azure 리소스가 연결된 서브넷에 NSG를 적용할 수도 있습니다. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)을 참조하세요.

- **Virtual Network 어플라이언스:** 가상 네트워크 어플라이언스는 방화벽과 같은 네트워크 기능을 수행하는 소프트웨어를 실행하는 VM입니다. Azure Marketplace에서 사용할 수 있는 NVA 목록을 확인하세요. WAN 최적화 및 기타 네트워크 트래픽 기능을 제공하는 NVA도 사용할 수 있습니다. NVA는 일반적으로 사용자 정의 경로 또는 BGP 경로와 함께 사용됩니다. 또한 NVA를 사용하여 VNet 간의 트래픽을 필터링할 수도 있습니다.

**라우팅**

자체 경로를 구성하여 또는 네트워크 게이트웨이 통해 BGP 경로를 사용하여 Azure의 기본 라우팅을 선택적으로 재정의할 수 있습니다.

Azure는 VNet의 모든 서브넷에 연결된 리소스가 서로 통신할 수 있도록 하는 경로 테이블을 기본적으로 생성합니다. 다음 옵션 중 하나 또는 둘 다를 구현하여 Azure에서 생성되는 기본 경로를 재정의할 수 있습니다.

- **사용자 정의 경로**: 각 서브넷에 대해 트래픽이 라우팅되는 위치를 제어하는 경로를 사용하여 사용자 지정 경로 테이블을 만들 수 있습니다. 사용자 정의 경로에 대해 자세히 알아보려면 [사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)를 참조하세요.

- **BGP 경로:** Azure VPN Gateway 또는 ExpressRoute 연결을 사용하여 온-프레미스 네트워크에 VNet을 연결하는 경우 VNet으로 BGP 경로를 전파할 수 있습니다.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>하이브리드 인터넷 연결: 온-프레미스 네트워크에 연결
다음 옵션을 조합하여 온-프레미스 네트워크를 VNet에 연결할 수 있습니다.

-   인터넷 연결

-   P2S VPN(지점-사이트 간 VPN)

-   S2S VPN(사이트 간 VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>인터넷 연결

이름에서 알 수 있듯이 인터넷 연결은 가상 네트워크 내에 존재하는 작업에 다른 공용 엔드포인트를 노출하도록 하여 인터넷에서 작업에 액세스할 수 있도록 해줍니다. 이러한 작업은 [인터넷 연결 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)를 사용하거나 단순히 VM에 공용 IP 주소를 할당하여 노출할 수 있습니다. 이러한 방식으로 인터넷의 모든 항목은 호스트 방화벽, [NSG(네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 및 [사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)가 허용할 경우 해당 가상 컴퓨터에 연결될 수 있습니다.

이 시나리오에서는 인터넷에 공개되어야 하고, 작업의 구성에 따라 어디에서든지 또는 특정 위치에서만 액세스할 수 있어야 하는 애플리케이션을 노출할 수 있습니다.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>지점 및 사이트 간 VPN 또는 사이트 간 VPN
이러한 두 가지 방식은 같은 범주에 속합니다. 두 방식 모두 VNet에 VPN Gateway가 필요하며, [지점-사이트 간 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)의 일부로 워크스테이션에 대한 VPN 클라이언트를 사용하여 연결하거나 사이트 간 VPN을 종료할 수 있게 온-프레미스 [VPN 디바이스](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)를 구성할 수 있습니다. 이러한 방식으로 온-프레미스 디바이스는 VNet 내에서 리소스에 연결할 수 있습니다.

지점 및 사이트 간(P2S) 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 안전한 연결을 만들 수 있습니다. P2S는 SSTP를 통한 VPN 연결입니다(보안 소켓 터널링 프로토콜).

![지점 및 사이트 간 VPN](media/azure-network-security/azure-network-security-fig-5.png)

지점 및 사이트 간 연결은 집 또는 회의 센터와 같은 원격 위치에서 VNet에 연결하려는 경우 또는 몇 명의 클라이언트만 가상 네트워크에 연결해야 하는 경우 유용합니다.

P2S 연결에는 VPN 디바이스 또는 공용 IP 주소가 필요하지 않습니다. 클라이언트 컴퓨터에서 VPN 연결을 설정합니다. 따라서 여러 온-프레미스 디바이스 및 컴퓨터에서 Azure 네트워크로의 영구 연결이 필요한 경우에는 Azure에 연결하는 데 P2S가 권장되지 않습니다.

![사이트 간 VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> 지점 및 사이트 간 연결에 대한 자세한 내용은 [지점 및 사이트 간 FAQ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)를 참조하세요.

사이트 간 VPN Gateway 연결은 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통해 온-프레미스 네트워크를 Azure Virtual Network에 연결하는 데 사용됩니다.

이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 디바이스를 필요로 합니다. 이 연결은 인터넷을 통해 발생하며 네트워크와 Azure 사이의 암호화된 링크 내부에서 정보를 "터널링"할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포해 온 안전하고 완성도 높은 기술입니다. 터널 암호화는 [IPsec 터널 모드](https://technet.microsoft.com/library/cc786385.aspx)를 사용하여 수행됩니다.

사이트 간 VPN이 신뢰할 수 있고 안정적이며 인정받은 기술이지만 터널 내부 트래픽은 인터넷을 트래버스합니다. 뿐만 아니라 대역폭이 약 200Mbps로 제한됩니다.

크로스-프레미스 연결에 강력한 보안 또는 성능 수준이 필요한 경우 크로스-프레미스 연결에 Azure ExpressRoute를 사용하는 것이 좋습니다. ExpressRoute는 온-프레미스 위치 또는 Exchange 호스팅 공급자 사이의 WAN 링크입니다. 이 연결은 telco 연결이기 때문에 데이터가 인터넷을 통해 전달되지 않고, 따라서 인터넷 통신의 본질적인 잠재적 위험에 노출되지 않습니다.

> [!Note]
> VPN Gateway에 대한 자세한 내용은 [VPN Gateway 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)를 참조하세요.

#### <a name="dedicated-wan-link"></a>전용 WAN 링크
Microsoft Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 전용 개인 연결을 통해 온-프레미스 네트워크를 Azure로 확장할 수 있습니다.

ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 이 기능을 사용하면 ExpressRoute 연결은 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.

![ 전용 WAN 링크](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> ExpressRoute를 사용하여 네트워크를 Microsoft에 연결하는 방법에 대한 내용은 [ExpressRoute 연결 모델](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 및 [ExpressRoute 기술 개요](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 참조하세요.

사이트 간 VPN 옵션을 통해 ExpressRoute에서 반드시 하나의 VNet에만 있지는 않은 리소스에 연결할 수 있습니다. 실제로 SKU에 따라 10개의 VNet에 연결할 수 있습니다. [프리미엄 추가 기능](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)이 있는 경우 대역폭에 따라 최대 100개의 VNet에 대한 연결이 가능합니다. 이러한 유형의 연결이 나타내는 형태에 대한 자세한 내용은 [연결 토폴로지 다이어그램](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

### <a name="security-controls"></a>보안 컨트롤
Azure Virtual Network는 다른 가상 네트워크와 분리되고 온-프레미스 네트워크에서 사용하는 많은 보안 컨트롤을 지원하는 안전한 논리적 네트워크를 제공합니다. 고객은 서브넷을 사용하여 자체 구조를 만듭니다. 즉, 자체 개인 IP 주소 범위를 사용하고, 경로 테이블, 네트워크 보안 그룹, ACL(액세스 제어 목록), 게이트웨이, 가상 어플라이언스가 클라우드에서 해당 작업을 실행하도록 구성합니다.

다음은 Azure Virtual Network에서 사용할 수 있는 보안 컨트롤입니다.

-   네트워크 액세스 컨트롤

-   사용자 정의 경로

-   네트워크 보안 어플라이언스

-   Application Gateway

-   Azure 웹 애플리케이션 방화벽

-   네트워크 가용성 컨트롤

#### <a name="network-access-controls"></a>네트워크 액세스 컨트롤
Azure VNet(Virtual Network)은 Azure 네트워킹 모델의 토대이며 격리 및 보호를 제공하지만, [NSG(네트워크 보안 그룹)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/)는 네트워크 수준에서 네트워크 트래픽 규칙을 적용 및 제어하는 데 사용하는 기본 도구입니다.

![ 네트워크 액세스 컨트롤](media/azure-network-security/azure-network-security-fig-8.png)


사용자는 크로스 프레미스 연결을 통한 고객 네트워크 상의 시스템이나 직접 인터넷 통신을 통해서 고객 네트워크 시스템으로부터 Virtual Network 내 워크로드 간 통신을 허용하거나 거부하여 액세스를 제어할 수 있습니다.

이 다이어그램에서 Azure의 전체 보안 스택에 있는 특정 계층에 VNet 및 NSG가 둘 다 상주합니다. 여기서는 NSG, UDR 및 네트워크 가상 어플라이언스를 사용하여 보안 경계를 만들어 보호되는 네트워크에서의 애플리케이션 배포를 보호할 수 있습니다.

NSG는 5튜플을 사용하여 트래픽을 평가하며, NSG에 대해 구성하는 규칙에 사용됩니다.

-   [원본 및 대상 IP 주소](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [원본 및 대상 포트](https://technet.microsoft.com/library/dd197515)

-   프로토콜: [TCP(Transmission Control Protocol)](https://technet.microsoft.com/library/cc940037.aspx) 또는 [UDP(User Datagram Protocol)](https://technet.microsoft.com/library/cc940034.aspx)

즉, 단일 VM과 VM 그룹 간, 단일 VM과 다른 단일 VM 간 또는 전체 서브넷 간의 액세스를 제어할 수 있습니다. 또한 전체 패킷 검사가 아닌 간단한 상태 저장 패킷 필터링이 사용됩니다. 네트워크 보안 그룹에는 프로토콜 유효성 검사나 네트워크 수준 IDS 또는 IPS 기능이 없습니다.

NSG는 알고 있어야 하는 몇 가지 기본 제공 규칙을 제공합니다. 다음과 같습니다.

-   **특정 가상 네트워크 내에서 모든 트래픽 허용:** 동일한 Azure Virtual Network의 모든 VM이 서로 통신할 수 있습니다.

-   **인바운드에 대한 Azure 부하 분산 허용:** 이 규칙을 사용하면 Azure Load Balancer를 위해 모든 원본 주소에서 대상 주소로의 트래픽이 허용됩니다.

-   **모든 인바운드 거부:** 이 규칙은 명시적으로 허용되는 인터넷에서 수신되는 모든 트래픽을 차단합니다.

-   **모든 인터넷 아웃바운드 트래픽 허용:** 이 규칙을 사용하면 VM이 인터넷에 연결을 시작할 수 있습니다. 이러한 연결을 시작하지 않으려면 해당 연결을 차단하거나 강제 터널링을 적용하는 규칙을 만들어야 합니다.

#### <a name="system-routes-and-user-defined-routes"></a>시스템 경로 및 사용자 정의 경로

Azure에서 VNet(가상 네트워크)에 VM(가상 머신)을 추가하면 네트워크를 통해 다른 VM과 통신할 수 있는지 자동으로 확인할 수 있습니다. 서로 다른 서브넷에 있는 VM 간에도 게이트웨이를 지정할 필요가 없습니다.

VM에서 공용 인터넷에 통신하는 경우는 물론, Azure와 사용자 고유의 데이터 센터 간 하이브리드 연결이 있는 경우 사용자의 온-프레미스 네트워크에 통신하는 경우에도 마찬가지입니다 .

![시스템 경로](media/azure-network-security/azure-network-security-fig-9.png)

이러한 통신 전달이 가능한 이유는 Azure가 일련의 시스템 경로를 사용하여 IP 트래픽 전달 방식을 정의하기 때문입니다. 다음과 같은 시나리오에서 시스템 경로가 통신 전달을 제어합니다.

-   동일한 서브넷 내의 통신

-   단일 VNet 내의 서로 다른 서브넷 간 통신

-   VM에서 인터넷으로의 통신

-   VPN Gateway를 통한 서로 다른 VNet 간 통신

-   Vnet 피어링([서비스 체이닝](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview))을 통한 다른 VNet 간 통신.

-   VPN Gateway를 통한 VNet과 사용자의 온-프레미스 네트워크 간 통신.

대부분의 기업에서는 특정 정책 적용을 위해 모든 네트워크 패킷에 대한 온-프레미스 검사를 요구하는 엄격한 보안 및 준수 요구 사항을 유지합니다. Azure는 [강제 터널링](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling)이라는 메커니즘을 제공합니다. 이 메커니즘은 사용자 지정 경로 만들거나 ExpressRoute 또는 VPN을 통한 [BGP(Border Gateway Protocol)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) 보급으로 VM에서 온-프레미스로 트래픽을 라우팅합니다.

Azure에서 강제 터널링은 가상 네트워크 UDR(사용자 정의 경로)을 통해 구성됩니다. 온-프레미스 사이트에 트래픽을 리디렉션하는 것은 Azure VPN Gateway에 기본 경로로 표현됩니다.

다음 섹션에서는 Azure Virtual Network에 대한 라우팅 테이블 및 경로의 현재 제한 사항을 나열합니다.

- 각 가상 네트워크 서브넷에는 기본 제공 시스템 라우팅 테이블이 있습니다. 시스템 라우팅 테이블에는 다음 3개의 경로 그룹이 있습니다.

  -  **로컬 VNet 경로**: 동일한 가상 네트워크의 대상 VM에 바로 연결

  - **온-프레미스 경로:** Azure VPN 게이트웨이로

  -  **기본 경로**: 인터넷에 바로 연결됩니다. 이전의 두 경로를 벗어나는 개인 IP 주소로 향하는 패킷은 삭제됩니다.

- 사용자 정의 경로가 릴리스되면서 라우팅 테이블을 만들어 기본 경로에 추가한 다음 라우팅 테이블을 VNet 서브넷에 연결하여 해당 서브넷에 강제 터널링을 사용할 수 있습니다.

- 가상 네트워크에 연결된 크로스-프레미스 로컬 사이트 사이에서 "기본 사이트"를 설정해야 합니다.

- 강제 터널링은 동적 라우팅 VPN Gateway(정적 게이트웨이 아님)가 있는 VNet에 연결되어야 합니다.

- ExpressRoute 강제 터널링은 이 메커니즘을 통해 구성되지 않지만 대신 ExpressRoute BGP 피어링 세션을 통해 기본 경로를 보급하여 활성화됩니다.

> [!Note]
> 자세한 내용은 [ExpressRoute 설명서](https://azure.microsoft.com/documentation/services/expressroute/)를 참조하세요.

#### <a name="network-security-appliances"></a>네트워크 보안 어플라이언스
네트워크 보안 그룹 및 사용자 정의 라우팅이 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model)의 네트워크 및 전송 계층에서 어느 정도의 네트워크 보안을 제공할 수 있지만 상위 네트워킹 스택에서 보안을 강화해야 하는 여러 상황이 발생할 것입니다. 이러한 상황에서는 Azure 파트너가 제공하는 가상 네트워크 보안 어플라이언스를 배포하는 것이 좋습니다.

![네트워크 보안 어플라이언스](./media/azure-network-security/azure-network-security-fig-10.png)

Azure 네트워크 보안 어플라이언스는 VNet 보안 및 네트워크 기능을 개선하며 [Azure Marketplace](https://azuremarketplace.microsoft.com)를 통해 다양한 공급업체의 어플라이언스를 사용할 수 있습니다. 이러한 보안 가상 어플라이언스는 다음을 제공하기 위해 배포할 수 있습니다.

-   고가용성 방화벽

-   침입 방지

-   침입 탐지

-   WAF(웹 애플리케이션 방화벽)

-   WAN 최적화

-   라우팅

-   부하 분산

-   VPN

-   인증서 관리

-   Active Directory

-   Multi-Factor Authentication

#### <a name="application-gateway"></a>애플리케이션 게이트웨이

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)는 ADC(애플리케이션 배달 컨트롤러)를 서비스로 제공하는 전용 가상 어플라이언스입니다.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Application Gateway를 통해 Application Gateway에 CPU 집약적인 SSL 종료를 오프로드(SSL 오프로딩)하여 웹 팜 성능 및 가용성을 최적화할 수도 있습니다. 또한 다음을 비롯한 기타 계층 7 라우팅 기능도 제공합니다.

-   들어오는 트래픽의 라운드 로빈 배포

-   쿠키 기반 세션 선호도

-   URL 경로 기반 라우팅

-   단일 Application Gateway 뒤에 여러 웹 사이트를 호스트하는 기능


[WAF(웹 애플리케이션 방화벽)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 또한 Application Gateway의 일부로 제공됩니다. 이 기능은 일반적인 웹 취약점 및 악용으로부터 웹 애플리케이션을 보호합니다. Application Gateway는 인터넷 연결 게이트웨이, 내부 전용 게이트웨이 또는 둘의 조합으로 구성할 수 있습니다.

Application Gateway WAF는 탐지 또는 방지 모드에서 실행할 수 있습니다. 일반적인 사용 사례는 관리자가 트래픽에서 악의적인 패턴을 관찰하기 위해 탐지 모드에서 실행하는 경우입니다. 악용 가능성이 감지되면 방지 모드로 전환되어 의심스러운 수신 트래픽을 차단합니다.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

또한 Application Gateway WAF는 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) 및 [Azure Security Center](https://azure.microsoft.com/services/security-center/)에 통합된 실시간 WAF 로그를 사용하여 WAF 경고를 추적하고 추세를 쉽게 모니터링하여 웹 애플리케이션을 공격으로부터 보호할 수 있도록 합니다.

JSON 형식 로그는 고객의 저장소 계정으로 직접 이동됩니다. 이러한 로그를 완전히 제어하고 자체 보존 정책을 적용할 수 있습니다.

[Azure 로그 통합](https://aka.ms/AzLog)을 사용하여 이러한 로그를 분석 시스템으로 수집할 수도 있습니다. 또한 WAF 로그와 통합 되 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 정교 하 고 세분화 된 쿼리를 실행 하려면 Azure Monitor 로그를 사용할 수 있습니다.

#### <a name="azure-web-application-firewall-waf"></a>Azure WAF(웹 애플리케이션 방화벽)

웹 애플리케이션은 점점 더 많이 SQL 주입, 사이트 간 스크립팅 공격 및 [OWASP 상위 10개 공격](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)에 나타나는 기타 공격 등의 알려진 일반적인 취약점을 악용하는 악의적인 공격의 목표가 되고 있습니다. 애플리케이션에서 이러한 악용을 방지하려면 애플리케이션 토폴로지의 여러 계층에서 엄격한 유지 관리, 패치 적용 및 모니터링이 필요할 수 있습니다.

 ![Azure WAF(웹 애플리케이션 방화벽)](./media/azure-network-security/azure-network-security-fig-13.png)

중앙 집중식 [WAF(웹 애플리케이션 방화벽)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)는 애플리케이션을 변경할 필요 없이 웹 공격으로부터 보호하고 보안 관리를 간소화합니다.

또한 WAF 솔루션은 각각의 웹 애플리케이션을 보호하는 대신 중앙의 위치에서 알려진 취약점에 패치를 적용하여 보다 신속하게 보안 위협에 대응할 수 있습니다. 기존 Application Gateway는 웹 애플리케이션 방화벽을 사용한 Application Gateway로 쉽게 변환될 수 있습니다.

#### <a name="network-availability-controls"></a>네트워크 가용성 컨트롤

Microsoft Azure를 사용하여 네트워크 트래픽을 분산하는 다양한 옵션이 있습니다. 이러한 옵션은 서로 다르게 작동하며 다른 기능 모음을 포함하고 다양한 시나리오를 지원합니다. 각각을 따로 사용하거나 조합해서 사용할 수 있습니다.

네트워크 가용성 컨트롤은 다음과 같습니다.

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Azure Load Balancer**

애플리케이션에 고가용성 및 네트워크 성능을 제공합니다. 이 장치는 부하 분산 장치 집합에 정의된 서비스의 정상 인스턴스 간에 들어오는 트래픽을 분산하는 계층 4(TCP, UDP) 부하 분산 장치입니다.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Azure Load Balancer를 다음과 같이 구성할 수 있습니다.

-   들어오는 인터넷 트래픽을 가상 머신에 부하 분산합니다. 이 구성을 [인터넷 연결 부하 분산](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)이라고 합니다.

-   가상 네트워크의 가상 머신 간, 클라우드 서비스의 가상 머신 간, 또는 크로스-프레미스 가상 네트워크의 온-프레미스 컴퓨터와 가상 머신 간에 트래픽을 부하 분산합니다. 이 구성을 [내부 부하 분산](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview)이라고 합니다.

-   외부 트래픽을 특정 가상 머신에 전달합니다.

클라우드의 모든 리소스에는 인터넷에서 연결할 수 있는 공용 IP 주소가 필요합니다. Azure의 클라우드 인프라는 리소스에 대해 라우팅할 수 없는 IP 주소를 사용합니다. 공용 IP 주소를 통한 NAT(Network Address Translation)를 사용하여 인터넷과 통신합니다.

 **Application Gateway**

 Application Gateway는 애플리케이션 계층(OSI 네트워크 참조 스택의 계층 7)에서 작동합니다. 이 게이트웨이는 역방향 프록시 서비스 역할을 하며 클라이언트 연결을 종료하고 백 엔드 엔드포인트로 요청을 전달합니다.

 **Traffic Manager**

Microsoft Azure Traffic Manager를 사용하면 다양한 데이터 센터에서 서비스 엔드포인트에 대한 사용자 트래픽의 배포를 제어할 수 있습니다. Traffic Manager에서 지원되는 서비스 엔드포인트는 Azure VM, Web Apps 및 클라우드 서비스를 포함합니다. 또한 외부, Azure가 아닌 엔드포인트로 Traffic Manager를 사용할 수 있습니다.

Traffic Manager는 DNS(Domain Name System)를 사용하여 클라이언트 요청을 [트래픽 라우팅 메서드](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) 및 엔드포인트의 상태를 기반으로 가장 적절한 엔드포인트로 리디렉션합니다. Traffic Manager는 다양한 애플리케이션 요구 사항, 엔드포인트 상태 [모니터링](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) 및 자동 장애 조치에 잘 맞는 트래픽 라우팅 메서드를 제공합니다. Traffic Manager는 전체 Azure 지역의 오류를 포함한, 오류에 대해 복원력을 갖습니다.

Azure Traffic Manager를 사용하면 애플리케이션 엔드포인트에 트래픽 분산을 제어할 수 있습니다. 엔드포인트는 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다.

Traffic Manager는 다음과 같은 두 가지 주요 이점을 제공합니다.

-   여러 가지 [트래픽 라우팅 방법](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) 중 하나에 따라 트래픽 배포

-   [엔드포인트 상태 연속 모니터링](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) 및 엔드포인트가 실패할 경우 자동 장애 조치(failover)

클라이언트가 서비스에 연결하려고 시도하면 먼저 IP 주소에 대한 서비스의 DNS 이름을 확인합니다. 그런 다음 클라이언트는 해당 IP 주소에 연결하여 서비스에 액세스합니다. Traffic Manager는 DNS를 사용하여 트래픽 라우팅 메서드의 규칙에 따라 클라이언트를 특정 서비스 엔드포인트에 연결합니다. 클라이언트는 선택한 엔드포인트에 직접 연결됩니다. Traffic Manager는 프록시 또는 게이트웨이가 아닙니다. Traffic Manager는 클라이언트와 서비스 간에 전달되는 트래픽을 표시하지 않습니다.

### <a name="azure-network-validation"></a>Azure 네트워크 유효성 검사

Azure 네트워크 유효성 검사는 Azure 네트워크가 구성된 대로 작동하는지 확인하며 이러한 유효성 검사는 네트워크 모니터링에 사용할 수 있는 서비스 및 기능을 사용하여 수행할 수 있습니다. Azure Network Watcher를 사용하여 다양한 로깅 및 진단 기능에 액세스함으로써 네트워크 성능과 상태를 보다 깊이 있기 이해할 수 있습니다. 이러한 기능은 포털, Powershell, CLI, Rest API 및 SDK를 통해 액세스할 수 있습니다.

Azure 운영 보안은 사용자가 Microsoft Azure에서 자신의 데이터, 애플리케이션 및 기타 자산을 보호할 수 있는 서비스, 제어 및 기능을 나타냅니다. Azure 운영 보안은 Microsoft SDL(Security Development Lifecycle), Microsoft 보안 대응 센터 프로그램 및 사이버 보안 위협 상황에 대한 심층 인식을 포함하여 Microsoft 고유의 다양한 기능을 통해 얻은 지식을 통합한 프레임워크를 기반으로 합니다.

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure 리소스 관리자

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Microsoft Azure를 운영하는 사용자와 프로세스가 이 플랫폼에서 가장 중요한 보안 역할을 할 것입니다. 이 섹션에서는 보안, 연속성 및 개인 정보 보호를 강화하고 유지하는 데 도움이 되는 Microsoft의 글로벌 데이터 센터 인프라의 기능에 대해 설명합니다.

애플리케이션에 대한 인프라는 일반적으로 가상 머신, 스토리지 계정 및 가상 네트워크 또는 웹앱, 데이터베이스, 데이터베이스 서버 및 타사 서비스 등의 많은 구성 요소를 만듭니다. 이러한 구성 요소를 별도 엔터티로 표시하지 않으면, 대신 관련된 단일 엔터티의 상호 종속적으로 부분으로 표시됩니다. 그룹으로 배포, 관리 및 모니터링하려고 합니다. Azure 리소스 관리자를 사용하면 솔루션에서 리소스를 그룹으로 사용할 수 있습니다.

조정된 단일 작업에서 솔루션에 대한 모든 리소스를 배포, 업데이트 또는 삭제할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 리소스 관리자는 보안, 감사 및 태그 기능을 제공하여 배포 후에 리소스를 관리할 수 있습니다.

**Resource Manager를 사용할 경우의 이점**

리소스 관리자는 다음과 같은 여러 이점이 있습니다.

-   이 리소스를 개별적으로 처리하는 것이 아니라 솔루션에 대한 모든 리소스를 그룹으로 배포, 관리 및 모니터링할 수 있습니다.

-   개발 수명 주기 내내 솔루션을 반복적으로 배포하며 안심하고 일관된 상태로 리소스를 배포할 수 있습니다.

-   스크립트가 아닌 선언적 템플릿을 통해 인프라를 관리할 수 있습니다.

-   올바른 순서로 배포되므로 리소스 간의 종속성을 정의할 수 있습니다.

-   역할 기반 Access Control(RBAC)가 관리 플랫폼으로 통합되기 때문에 리소스 그룹의 모든 서비스에 대해 Access Control를 적용할 수 있습니다.

-   리소스에 태그를 적용하여 구독에서 모든 리소스를 논리적으로 구성할 수 있습니다.

-   태그를 공유하는 리소스 그룹에 대한 비용을 확인하여 조직의 청구를 명확히 할 수 있습니다.

> [!Note]
> 리소스 관리자는 솔루션을 배포 및 관리하는 새로운 방식을 제공합니다. 이전의 배포 모델을 사용한 경우 변경 사항을 알아보려면 [리소스 관리자 배포 및 클래식 배포 이해](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)를 참조하세요.

## <a name="azure-network-logging-and-monitoring"></a>Azure 네트워크 로깅 및 모니터링

Azure는 네트워크 보안 이벤트를 모니터링하고, 예방하고, 검색하고, 응답하는 여러 가지 도구를 제공합니다. 이 영역에 사용할 수 있는 가장 강력한 도구 중 일부는 다음과 같습니다.

-   Network Watcher

-   네트워크 리소스 수준 모니터링

-   Azure Monitor 로그

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) - Network Watcher 기능과 함께 시나리오 기반 모니터링이 제공됩니다. 이 서비스에는 패킷 캡처, 다음 홉, IP 흐름 확인, 보안 그룹 보기, NSG 흐름 로그가 포함됩니다. 시나리오 수준 모니터링에서는 개별 네트워크 리소스 모니터링과 달리 네트워크 리소스의 종단 간 보기를 제공합니다.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다.

Network Watcher는 현재 다음과 같은 기능을 제공합니다.

#### <a name="topology"></a>토폴로지

[토폴로지](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview)는 가상 네트워크에서 네트워크 리소스의 그래프를 반환합니다. 이 그래프는 리소스 간 상호 연결을 보여 주고 종단 간 네트워크 연결을 나타냅니다. 포털에서 토폴로지는 가상 네트워크를 기반으로 리소스 개체를 반환합니다. 관계는 Network Watcher 지역 외부에 있는 리소스 간을 선으로 연결해서 나타내며 리소스 그룹에 표시되지 않더라도 무방합니다. 포털 보기에서 반환된 리소스는 그래프 표시된 네트워킹 구성 요소의 하위 집합입니다. 전체 네트워킹 리소스 목록을 보려면 [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) 또는 [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest)를 사용할 수 있습니다.

리소스가 반환되면 리소스 간 연결은 두 관계로 모델링됩니다.

- **포함** - Virtual Network는 NIC를 포함하는 서브넷을 포함합니다.

- **연결됨** - NIC는 VM과 연결됩니다.

#### <a name="variable-packet-capture"></a>가변 패킷 캡처

Network Watcher [가변 패킷 캡처](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)를 사용하면 가상 컴퓨터 간에 트래픽을 추적하는 패킷 캡처 세션을 만들 수 있습니다. 패킷 캡처를 통해 사후 및 사전 대응적으로 네트워크 예외를 진단할 수 있습니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버깅할 수 있습니다.

패킷 캡처는 Network Watcher를 통해 원격으로 시작되는 가상 머신 확장입니다. 이 기능은 원하는 가상 머신에서 수동으로 패킷 캡처를 실행하는 부담을 줄이고 시간을 단축합니다. 포털, PowerShell, CLI 또는 REST API를 통해 패킷 캡처를 트리거할 수 있습니다. 패킷 캡처를 트리거하는 방식에 대한 한 가지 예는 Virtual Machine 경고를 사용하는 것입니다.

#### <a name="ip-flow-verify"></a>IP 흐름 확인

[IP 흐름 확인](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)은 5개 튜플 정보에 따라 가상 컴퓨터 간 패킷이 허용되거나 거부되는지를 확인합니다. 이 정보는 방향, 프로토콜, 로컬 IP, 원격 IP, 로컬 포트 및 원격 포트로 구성됩니다. 보안 그룹에서 패킷을 거부하면 해당 패킷을 거부한 규칙의 이름이 반환됩니다. 모든 원본 또는 대상 IP를 선택할 수 있는 동안 이 기능을 통해 관리자는 인터넷 간 및 온-프레미스 환경 간 연결 문제를 신속하게 진단할 수 있습니다.

IP 흐름 확인은 가상 머신의 네트워크 인터페이스를 대상으로 합니다. 그런 다음 해당 네트워크 인터페이스 간에서 구성된 설정에 따라 트래픽 흐름이 확인됩니다. 이 기능은 네트워크 보안 그룹에서 규칙이 가상 머신 간의 수신 또는 송신 트래픽을 차단하는 경우를 확인하는 데 유용합니다.

#### <a name="next-hop"></a>다음 홉

Azure 네트워크 패브릭에서 라우팅되는 패킷의 [다음 홉](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)을 결정하여 잘못 구성된 사용자 정의 경로를 진단할 수 있습니다. VM의 트래픽은 NIC와 연결된 유효 경로에 따라 대상에 전송됩니다. 다음 홉은 특정 가상 컴퓨터 및 NIC에서 다음 홉 유형 및 패킷의 IP 주소를 가져옵니다. 이를 통해 패킷이 대상으로 연결되거나 블랙홀이 되는 트래픽인 경우를 확인할 수 있습니다.

또한 다음 홉은 다음 홉과 연결된 경로 테이블을 반환합니다. 다음 홉을 쿼리할 때 경로가 사용자 정의 경로로 정의된 경우 해당 경로가 반환됩니다. 그렇지 않은 경우 다음 홉은 "시스템 경로"를 반환합니다.

#### <a name="security-group-view"></a>보안 그룹 보기

VM에 적용되는 효과적이고 실용적인 보안 규칙을 가져옵니다. 네트워크 보안 그룹은 서브넷 수준 또는 NIC 수준에서 연결됩니다. 서브넷 수준에서 연결되는 경우 서브넷의 모든 VM 인스턴스에 적용됩니다. 네트워크 [보안 그룹 보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview)는 가상 머신에 대한 NIC 및 서브넷 수준에서 연결된 모든 구성된 NSG 및 규칙을 반환하여 구성을 이해하기 쉽게 합니다. 또한 VM의 각 NIC에 대해 유효 보안 규칙이 반환됩니다. 네트워크 보안 그룹 보기를 사용하여 열린 포트와 같은 네트워크 취약점에 대해 VM을 평가할 수 있습니다. 또한 네트워크 보안 그룹이 [구성된 보안 규칙 및 유효 보안 규칙 간의 비교](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell)에 따라 예상대로 작동하는지 검증할 수도 있습니다.

#### <a name="nsg-flow-logging"></a>NSG 흐름 로깅

 네트워크 보안 그룹의 흐름 로그를 사용하면 그룹의 보안 규칙에 의해 허용되거나 거부되는 트래픽과 관련된 로그를 캡처할 수 있습니다. 흐름은 원본 IP, 대상 IP, 원본 포트, 대상 포트 및 프로토콜의 5개 튜플 정보로 정의됩니다.

[네트워크 보안 그룹 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)는 네트워크 보안 그룹을 통해 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있는 Network Watcher의 기능입니다.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Virtual Network 게이트웨이 및 연결 문제 해결

Network Watcher는 Azure에서 네트워크 리소스를 이해하는 데 관련된 다양한 기능을 제공합니다. 이러한 기능 중 하나는 리소스 문제 해결입니다. [리소스 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)은 PowerShell, CLI 또는 REST API에서 호출될 수 있습니다. Network Watcher가 호출되면 Virtual Network 게이트웨이 또는 연결의 상태를 검사하거나 해당 결과를 반환합니다.

이 섹션에서는 리소스 문제 해결을 위해 현재 사용할 수 있는 여러 관리 태스크를 설명합니다.

-   [Virtual Network 게이트웨이 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [연결 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>네트워크 구독 제한

[네트워크 구독 제한](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)은 사용 가능한 최대 리소스 수와 개별 지역에 속한 개별 구독의 각 네트워크 리소스 사용량을 대조한 세부 정보를 제공합니다.

#### <a name="configuring-diagnostics-log"></a>진단 로그 구성

Network Watcher는 [진단 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 보기를 제공합니다. 이 보기에는 진단 로깅을 지원하는 모든 네트워킹 리소스가 포함됩니다. 이 보기에서 네트워킹 리소스를 빠르고 편리하게 사용하거나 사용하지 않도록 설정할 수 있습니다.

### <a name="network-resource-level-monitoring"></a>네트워크 리소스 수준 모니터링

리소스 수준 모니터링에서 사용할 수 있는 기능은 다음과 같습니다.

#### <a name="audit-log"></a>감사 로그

네트워크 구성의 일부로 수행되는 작업을 기록합니다. 이러한 감사 로그는 다양한 규정 준수를 설정하는 데 중요합니다. 이러한 로그는 Azure Portal에서 보거나 Power BI 또는 타사 도구와 같은 Microsoft 도구를 사용하여 검색할 수 있습니다. 감사 로그는 포털, PowerShell, CLI 및 Rest API를 통해 사용할 수 있습니다.

> [!Note]
> 감사 로그에 대한 자세한 내용은 [Resource Manager로 작업 감사](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
감사 로그는 모든 네트워크 리소스에서 수행된 작업에 사용할 수 있습니다.


#### <a name="metrics"></a>메트릭

메트릭은 일정 기간 동안 수집된 성능 측정 및 카운터입니다. 메트릭은 현재 Application Gateway에서 사용할 수 있습니다. 메트릭을 사용하여 임계값에 기반한 경고를 트리거할 수 있습니다. Azure Application Gateway는 기본적으로 백 엔드 풀의 모든 리소스 상태를 모니터링하고 풀에서 비정상으로 간주한 모든 리소스를 자동으로 제거합니다. Application Gateway는 비정상 인스턴스를 계속 모니터링하며 사용할 수 있게 되고 상태 프로브에 응답하게 되면 정상 백 엔드 풀에 다시 추가합니다. 애플리케이션 게이트웨이에서 상태 프로브를 백 엔드 HTTP 설정에 정의된 동일한 포트와 함께 보냅니다. 이 구성으로 프로브에서 사용자가 백 엔드에 연결하는 데 사용하는 것과 동일한 포트를 테스트합니다.

> [!Note]
> 메트릭을 사용하여 경고를 만드는 방법을 알아보려면 [Application Gateway 진단](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)을 참조하세요.

#### <a name="diagnostic-logs"></a>진단 로그

정기적이 고 자동적인 이벤트가 네트워크 리소스에서 생성 되 고 이벤트 허브로 전송 되는 저장소 계정에 로그인 하거나 Azure Monitor를 기록 합니다. 이러한 로그는 리소스 상태에 대한 정보를 제공하며, Power BI 및 Azure Monitor 로그와 같은 도구에서 이러한 로그를 볼 수 있습니다. 진단 로그를 보는 방법에 알아보려면 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)합니다.

진단 로그는 [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), 경로 및 [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)에서 사용할 수 있습니다.

Network Watcher는 진단 로그 보기를 제공합니다. 이 보기에는 진단 로깅을 지원하는 모든 네트워킹 리소스가 포함됩니다. 이 보기에서 네트워킹 리소스를 빠르고 편리하게 사용하거나 사용하지 않도록 설정할 수 있습니다.

### <a name="azure-monitor-logs"></a>Azure Monitor 로그

[Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 는 클라우드를 모니터링 하 고 온-프레미스 환경의 해당 가용성 및 성능을 유지 하는 Azure의 서비스입니다. 이 서비스는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성되고 여러 원본에 대한 분석을 제공하는 다른 모니터링 도구에서 생성된 데이터를 수집합니다.

Azure Monitor 로그는 네트워크 모니터링에 대 한 다음 솔루션을 제공 합니다.

-   NPM(네트워크 성능 모니터)

-   Azure Application Gateway 분석

-   Azure 네트워크 보안 그룹 분석

#### <a name="network-performance-monitor-npm"></a>NPM(네트워크 성능 모니터)
[네트워크 성능 모니터](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) 관리 솔루션은 네트워크의 상태, 가용성 및 연결 가능성을 모니터링하는 네트워크 모니터링 솔루션입니다.

다음 항목 간의 연결을 모니터링하는 데 사용됩니다.

-   공용 클라우드 및 온-프레미스

-   데이터 센터와 사용자 위치(지점)

-   다중 계층 애플리케이션의 다양한 계층을 호스팅하는 서브넷.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Azure Monitor 로그에서 azure application gateway 분석

Application Gateway에는 다음 로그가 지원됩니다.

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Application Gateway에는 다음 메트릭이 지원됩니다.

-   5분 처리량

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Azure Monitor 로그에서 azure 네트워크 보안 그룹 분석

[네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)에는 다음 로그가 지원됩니다.

- **NetworkSecurityGroupEvent:** VM 및 MAC 주소 기반 인스턴스 역할에 적용된 NSG 규칙에 대한 항목을 포함합니다. 이러한 규칙에 대한 상태는 60초마다 수집됩니다.

- **NetworkSecurityGroupRuleCounter:** 트래픽을 허용하거나 거부하기 위해 각 NSG 규칙이 적용된 횟수에 대한 항목을 포함합니다.

## <a name="next-steps"></a>다음 단계
다음과 같은 심층적인 일부 보안 항목을 참조하여 보안에 대한 자세한 내용을 확인할 수 있습니다.

-   [네트워크 보안 그룹 (Nsg)에 대 한 azure Monitor 로그](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [클라우드 중단을 유도하는 네트워킹 혁신](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: Microsoft 글로벌 클라우드를 지원하는 네트워킹 전환 소프트웨어](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Microsoft에서 빠르고 신뢰할 수 있는 글로벌 네트워크를 구축하는 방법](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [네트워크 혁신 조명](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
