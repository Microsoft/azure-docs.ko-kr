---
title: Azure의 IP 주소 유형(클래식)
titlesuffix: Azure Virtual Network
description: Azure에서 공용 및 개인 IP 주소(기본)에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 9e7a5772dd1e10abf43eddf0548833d625ecfb24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742125"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Azure의 IP 주소 유형 및 할당 방법
다른 Azure 리소스, 온-프레미스 네트워크 및 인터넷과 통신하기 위해 Azure 리소스에 IP 주소를 할당할 수 있습니다. Azure에서 사용할 수 있는 IP 주소는 공용 및 개인의 두 종류가 있습니다.

공용 IP 주소는 Azure 공용 웹 서비스를 포함한 인터넷과의 통신에 사용됩니다.

개인 IP 주소는 VPN 게이트웨이 또는 ExpressRoute 회로를 사용하여 Azure로 네트워크를 확장할 때 Azure 가상 네트워크(VNet), 클라우드 서비스 및 온-프레미스 네트워크 내에서 통신하는 데 사용됩니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한  [Resource Manager 및 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자를 사용하는 것이 좋습니다. [IP 주소](virtual-network-ip-addresses-overview-arm.md) 문서에서 Resource Manager의 IP 주소에 관해 알아봅니다.

## <a name="public-ip-addresses"></a>공용 IP 주소
공용 IP 주소를 사용하면 Azure 리소스가 [Azure Cache for Redis](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL Database](../sql-database/sql-database-technical-overview.md) 및 [Azure Storage](../storage/common/storage-introduction.md)와 같은 Azure의 공용 서비스 및 인터넷과 통신할 수 있습니다.

공용 IP 주소는 다음 리소스 유형과 연결됩니다.

* 클라우드 서비스
* IaaS VM(Virtual Machines)
* PaaS 역할 인스턴스
* VPN 게이트웨이
* 애플리케이션 게이트웨이

### <a name="allocation-method"></a>할당 방법
공용 IP 주소를 Azure 리소스에 할당해야 하는 경우 리소스가 생성된 위치 내 사용 가능한 공용 IP 주소 풀에서 *동적으로* 할당됩니다. 이 IP 주소는 리소스가 중지되면 해제됩니다. 클라우드 서비스의 경우 모든 역할 인스턴스가 중지되면 이러한 상황이 발생하며, *정적* (예약된) IP 주소를 사용하면 이를 방지할 수 있습니다([Cloud Services](#cloud-services)참조).

> [!NOTE]
> 공용 IP 주소를 Azure 리소스에 할당할 때 사용되는 IP 범위 목록은 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 게시되어 있습니다.
> 
> 

### <a name="dns-hostname-resolution"></a>DNS 호스트 이름 확인
클라우드 서비스 또는 IaaS VM을 만들 때는 Azure의 모든 리소스에서 고유한 클라우드 서비스 DNS 이름을 제공해야 합니다. 이는 Azure 관리 DNS 서버에서 *dnsname*.cloudapp.net과 리소스의 공용 IP 주소에 대한 매핑을 만듭니다. 예를 들어, **contoso**라는 클라우드 서비스 DNS 이름으로 클라우드 서비스를 만들면 정규화된 도메인 이름(FQDN) **contoso.cloudapp.net**이 클라우드 서비스의 공용 IP 주소(VIP)로 확인됩니다. 이 FQDN을 사용하여 Azure의 공용 IP 주소를 가리키는 사용자 지정 도메인 CNAME 레코드를 만들 수 있습니다.

### <a name="cloud-services"></a>클라우드 서비스
클라우드 서비스에는 항상 가상 IP 주소(VIP)라고 하는 공용 IP 주소가 있습니다. 클라우드 서비스 내에 엔드포인트를 만들어 VIP의 여러 포트를 클라우드 서비스 내 VM 및 역할 인스턴스의 내부 포트로 연결할 수 있습니다. 

클라우드 서비스에는 여러 IaaS VM, PaaS 역할 인스턴스가 포함될 수 있으며 모두 동일한 클라우드 서비스 VIP를 통해 노출됩니다. [클라우드 서비스에 여러 VIP](../load-balancer/load-balancer-multivip.md)를 할당하고, SSL 기반 웹 사이트를 사용하는 다중 테넌트 환경과 같은 다중 VIP 시나리오를 구현할 수 있습니다.

[예약된 IP](virtual-networks-reserved-public-ip.md)라고 하는 *정적* 공용 IP 주소를 사용하면 모든 역할 인스턴스가 중지되더라도 클라우드 서비스의 공용 IP 주소를 동일하게 유지할 수 있습니다. 특정 위치에 정적(예약된) IP 리소스를 만들고 해당 위치의 클라우드 서비스에 할당할 수 있습니다. 예약된 IP의 실제 IP 주소는 지정할 수 없습니다. 이는 생성된 위치에서 사용 가능한 IP 주소 풀에서 할당됩니다. 이 IP 주소는 명시적으로 삭제될 때까지 해제되지 않습니다.

정적(예약된) 공용 IP 주소는 일반적으로 클라우드 서비스가 다음과 같은 시나리오에서 사용됩니다.

* 최종 사용자가 방화벽 규칙을 설정해야 하는 경우
* 외부 DNS 이름 확인에 따라 달라지며, 동적 IP를 위해 A 레코드 업데이트가 필요한 경우
* IP 기반 보안 모델을 사용하는 외부 웹 서비스를 사용하는 경우
* IP 주소에 연결된 SSL 인증서를 사용하는 경우

> [!NOTE]
> 클래식 VM을 만들 때 컨테이너 *클라우드 서비스* 가 Azure에 의해 만들어지며 VIP(가상 IP 주소)를 포함합니다. 포털을 통해 작성을 완료하는 경우 기본 RDP 또는 SSH *엔드포인트* 가 포털에 의해 구성되어 클라우드 서비스 VIP를 통해 VM에 연결할 수 있습니다. 이 클라우드 서비스 VIP는 예약할 수 있으며 VM에 연결하는 데 예약된 IP 주소를 효과적으로 제공하도록 합니다. 더 많은 엔드포인트를 구성하여 추가 포트를 열 수 있습니다.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VM 및 PaaS 역할 인스턴스
클라우드 서비스 내에 있는 IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 PaaS 역할 인스턴스에 공용 IP 주소를 직접 할당할 수 있습니다. 이를 인스턴스 수준 공용 IP 주소([ILPIP](virtual-networks-instance-level-public-ip.md))라고 합니다. 이 공용 IP 주소는 동적 방식만 가능합니다.

> [!NOTE]
> 클라우드 서비스에는 여러 IaaS VM, PaaS 역할 인스턴스가 포함될 수 있으며 모두 동일한 클라우드 서비스 VIP를 통해 노출되기 때문에 IaaS VMs 또는 PaaS 역할 인스턴스에 대한 컨테이너인 클라우드 서비스의 VIP와는 다릅니다.
> 
> 

### <a name="vpn-gateways"></a>VPN 게이트웨이
[VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md) 는 Azure VNet를 다른 Azure VNet 또는 온-프레미스 네트워크에 연결하는 데 사용할 수 있습니다. VPN 게이트웨이는 공용 IP 주소가 *동적으로*할당되며, 원격 네트워크와의 통신을 지원합니다.

### <a name="application-gateways"></a>애플리케이션 게이트웨이
Azure [애플리케이션 게이트웨이](../application-gateway/application-gateway-introduction.md)는 HTTP 기반 네트워크 트래픽을 라우팅하는 Layer7 부하 분산에 사용할 수 있습니다. 애플리케이션 게이트웨이에는 부하 분산된 VIP 역할을 하는 공용 IP 주소가 *동적으로*할당됩니다.

### <a name="at-a-glance"></a>개요
아래 테이블에서는 각 리소스 유형과 사용 가능한 할당 방법(동적/정적), 그리고 여러 공용 IP 주소를 할당할 수 있는지 여부를 보여 줍니다.

| 리소스 | 동적 | 공용 | 여러 IP 주소 |
| --- | --- | --- | --- |
| 클라우드 서비스 |예 |예 |예 |
| IaaS VM 또는 PaaS 역할 인스턴스 |예 |아니오 |아닙니다. |
| VPN 게이트웨이 |예 |아니오 |아닙니다. |
| 애플리케이션 게이트웨이 |예 |아니오 |아닙니다. |

## <a name="private-ip-addresses"></a>개인 IP 주소
개인 IP 주소를 사용하면 Azure 리소스가 인터넷 연결이 가능한 IP 주소를 사용하지 않고 VPN 게이트웨이 또는 ExpressRoute 회로를 통해 클라우드 서비스 또는 [가상 네트워크](virtual-networks-overview.md)(VNet) 또는 온-프레미스 네트워크의 다른 리소스와 통신할 수 있습니다.

Azure 클래식 배포 모델에서 개인 IP 주소는 다음의 Azure 리소스에 할당될 수 있습니다.

* IaaS VM 및 PaaS 역할 인스턴스
* 내부 부하 분산 장치
* 애플리케이션 게이트웨이

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VM 및 PaaS 역할 인스턴스
클래식 배포 모델을 사용하여 만든 가상 머신(VM)는 항상 PaaS 역할 인스턴스와 유사한 클라우드 서비스에 배치됩니다. 따라서 사설 IP 주소의 동작은 이러한 리소스와 비슷합니다.

클라우드 서비스를 배포할 수 있는 방법이 두 가지라는 점을 알아두는 것이 중요합니다.

* 가상 네트워크 내에 있지 않은 *독립 실행형* 클라우드 서비스로 배포
* 가상 네트워크의 일부로 배포

#### <a name="allocation-method"></a>할당 방법
*독립 실행형* 클라우드 서비스의 경우 리소스가 Azure 데이터 센터의 개인 IP 주소 범위에서 *동적으로* 할당된 개인 IP 주소를 가져옵니다. 동일한 클라우드 서비스 내 다른 VM과의 통신에만 사용할 수 있습니다. 이 IP 주소는 리소스를 중지하고 시작할 때 변경될 수 있습니다.

가상 네트워크 내에 배포되는 클라우드 서비스의 경우 리소스가 (네트워크 구성에 지정된 대로) 할당된 서브넷의 주소 범위에서 할당된 개인 IP 주소를 가져옵니다. 이 개인 IP 주소는 VNet 내 모든 VM 간의 통신에 사용할 수 있습니다.

또한 VNet 내 클라우드 서비스의 경우 기본적으로 개인 IP 주소가 (DHCP를 사용하여) *동적으로* 할당됩니다. 이는 리소스를 중지 및 시작할 때 변경될 수 있습니다. IP 주소가 동일하게 유지되려면 할당 방법을 *정적*으로 설정하고 해당 주소 범위 내에서 유효한 IP 주소를 제공해야 합니다.

정적 개인 IP 주소가 일반적으로 사용되는 대상은 다음과 같습니다.

* 도메인 컨트롤러 또는 DNS 서버 역할을 하는 VM
* IP 주소를 사용하는 방화벽 규칙이 필요한 VM
* IP 주소를 통해 다른 앱에서 액세스하는 서비스를 실행 중인 VM

#### <a name="internal-dns-hostname-resolution"></a>내부 DNS 호스트 이름 확인
모든 Azure VM 및 PaaS 역할 인스턴스는 명시적으로 사용자 지정 DNS 서버를 구성하지 않으면 기본적으로 [Azure 관리 DNS 서버](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) 로 구성됩니다. 이러한 DNS 서버는 동일한 VNet 또는 클라우드 서비스 내에 있는 VM 및 역할 인스턴스에 대한 내부 이름 확인을 제공합니다.

VM을 만들 때 개인 IP 주소에 대한 호스트 이름 매핑이 Azure 관리 DNS 서버에 추가됩니다. 다중 NIC VM의 경우 기본 NIC의 사설 IP 주소에 호스트 이름이 매핑됩니다. 그러나 이 매핑 정보는 동일한 클라우드 서비스 또는 VNet 내에 있는 리소스로 제한됩니다.

*독립 실행형* 클라우드 서비스의 경우 동일한 클라우드 서비스 내에 있는 모든 VM/역할 인스턴스의 호스트 이름만 확인할 수 있습니다. VNet 내에 있는 클라우드 서비스의 경우 VNet 내에 있는 모든 VM/역할 인스턴스의 호스트 이름을 확인할 수 있습니다.

### <a name="internal-load-balancers-ilb--application-gateways"></a>ILB(내부 부하 분산 장치) 및 애플리케이션 게이트웨이
[Azure 내부 부하 분산 디바이스](../load-balancer/load-balancer-internal-overview.md)(ILB) 또는 [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)의 **프런트 엔드** 구성에 사설 IP 주소를 할당할 수 있습니다. 이 개인 IP 주소는 가상 네트워크(VNet) 내 리소스와 VNet에 연결된 원격 네트워크에만 액세스할 수 있는 내부 엔드포인트로 사용됩니다. 프런트 엔드 구성에 동적 또는 정적 개인 IP 주소를 할당할 수 있습니다. 또한 여러 개인 IP 주소를 할당하여 다중 vip 시나리오를 구현할 수도 있습니다.

### <a name="at-a-glance"></a>개요
아래 테이블에서는 각 리소스 유형과 사용 가능한 할당 방법(동적/정적), 그리고 여러 개인 IP 주소를 할당할 수 있는지 여부를 보여 줍니다.

| 리소스 | 동적 | 공용 | 여러 IP 주소 |
| --- | --- | --- | --- |
| VM(*독립 실행형* 클라우드 서비스 또는 VNet에서) |예 |예 |예 |
| PaaS 역할 인스턴스(*독립 실행형* 클라우드 서비스 또는 VNet에서) |예 |아니오 |아닙니다. |
| 내부 부하 분산 장치 프런트 엔드 |예 |예 |예 |
| 애플리케이션 게이트웨이 프런트 엔드 |예 |예 |예 |

## <a name="limits"></a>제한
아래 테이블은 구독 당 Azure에서 IP 주소에 적용된 제한을 보여줍니다. 비즈니스에 따라 최대 한도까지 기본 제한을 증가시키려면 [지원에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 하세요.

|  | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 공용 IP 주소(동적) |5 |지원에 문의 |
| 예약된 공용 IP 주소 |20 |지원에 문의 |
| 배포당 공용 VIP(클라우드 서비스) |5 |지원에 문의 |
| 배포당 개인 VIP(ILB)(클라우드 서비스) |1 |1 |

Azure에서 [네트워킹에 대한 제한](../azure-subscription-service-limits.md#networking-limits) 에 대한 전체 내용을 확인해야 합니다.

## <a name="pricing"></a>가격
대부분의 경우에 공용 IP 주소는 무료입니다. 추가 및/또는 정적 공용 IP 주소를 사용하는 데 명목 요금이 있습니다. [공용 IP에 대한 가격 책정 구조](https://azure.microsoft.com/pricing/details/ip-addresses/)를 이해하도록 합니다.

## <a name="differences-between-resource-manager-and-classic-deployments"></a>리소스 관리자와 클래식 배포 간 차이점
아래는 리소스 관리자와 클래식 배포 모델의 IP 주소 기능을 비교한 것입니다.

|  | 리소스 | 클래식 | Resource Manager |
| --- | --- | --- | --- |
| **공용 IP 주소** |***VM*** |ILPIP(동적 전용)로 참조 |공용 IP(동적 또는 정적)로 참조 |
|  ||IaaS VM 또는 PaaS 역할 인스턴스에 할당 |VM의 NIC에 연결 |
|  |***인터넷 연결 부하 분산 장치*** |VIP(동적) 또는 예약된 IP(정적)로 참조 |공용 IP(동적 또는 정적)로 참조 |
|  ||클라우드 서비스에 할당 |부하 분산 장치의 프런트 엔드 구성에 연결 |
|  | | | |
| **개인 IP 주소** |***VM*** |DIP로 참조 |개인 IP 주소로 참조 |
|  ||IaaS VM 또는 PaaS 역할 인스턴스에 할당 |VM의 NIC에 할당 |
|  |***ILB(내부 부하 분산 장치)*** |ILB(동적 또는 정적)에 할당 |ILB의 프런트 엔드 구성(동적 또는 정적)에 할당 |

## <a name="next-steps"></a>다음 단계
* Azure Portal을 사용하여 [고정 개인 IP 주소를 사용하는 VM 배포](virtual-networks-static-private-ip-classic-pportal.md)

