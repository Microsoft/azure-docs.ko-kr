---
title: Azure의 IP 주소 유형
titlesuffix: Azure Virtual Network
description: Azure의 공용 및 개인 IP 주소에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms.openlocfilehash: 69fd70fc45f734f8267db6d770d09b14f3a071f5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108147"
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>IP 주소 유형 및 Azure에서 할당 메서드

다른 Azure 리소스, 온-프레미스 네트워크 및 인터넷과 통신하기 위해 Azure 리소스에 IP 주소를 할당할 수 있습니다. Azure에서 두 가지 유형의 IP 주소를 사용할 수 있습니다.

* **공용 IP 주소**: Azure 공용 서비스를 포함하여 인터넷과의 통신에 사용됩니다.
* **사설 IP 주소**: VPN 게이트웨이 또는 ExpressRoute 회로를 사용하여 Azure로 네트워크를 확장할 때 Azure VNet(가상 네트워크) 및 온-프레미스 네트워크 내에서 통신하는 데 사용됩니다.

공용 IP 접두사를 통해 고정 공용 IP 주소의 연속 범위를 만들 수도 있습니다. [공용 IP 접두사에 대해 알아보세요.](public-ip-address-prefix.md)

> [!NOTE]
> Azure에는 리소스를 만들고 사용하기 위한  [Resource Manager 및 클래식](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 [클래식 배포 모델](virtual-network-ip-addresses-overview-classic.md) 대신 이 모델을 사용하도록 권장합니다.
> 

클래식 배포 모델에 익숙한 경우 [클래식과 Resource Manager 간 IP 주소 지정의 차이](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)를 확인하십시오.

## <a name="public-ip-addresses"></a>공용 IP 주소

공용 IP 주소를 통해 인터넷 리소스가 Azure 리소스에 대한 인바운드와 통신할 수 있습니다. 또한 공용 IP 주소를 사용하면 Azure 리소스가 리소스에 할당된 IP 주소에 인터넷 및 공용 Azure 서비스에 대한 아웃바운드와 통신할 수 있습니다. 주소는 사용자가 할당 해제하지 않는 한 리소스에 전용됩니다. 공용 IP 주소가 리소스에 할당되지 않은 경우, 리소스는 여전히 인터넷에 대한 아웃바운드와 통신할 수 있지만, Azure는 리소스에 전용되지 않는 사용 가능한 IP 주소를 동적으로 할당 합니다. Azure에서 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

Azure 리소스 관리자에서 [공용 IP](virtual-network-public-ip-address.md) 주소는 고유 속성을 가진 리소스입니다. 공용 IP 주소 리소스를 연결할 수 있는 리소스는 다음과 같습니다.

* 가상 머신 네트워크 인터페이스
* 인터넷 연결 부하 분산 장치
* VPN 게이트웨이
* 애플리케이션 게이트웨이

### <a name="ip-address-version"></a>IP 주소 버전

공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 공용 IPv6 주소는 인터넷 연결 부하 분산 장치에만 할당할 수 있습니다.

### <a name="sku"></a>SKU

공용 IP 주소는 다음 SKU 중 하나로 만들어집니다.

>[!IMPORTANT]
> SKU 일치는 Load Balancer 및 공용 IP 리소스에 대해 사용되어야 합니다. 기본 SKU 리소스와 표준 SKU 리소스를 함께 사용할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 리소스의 가상 머신 또는 가상 머신 확장 집합 리소스를 두 SKU에 동시에 연결할 수 없습니다.  새 디자인에서는 표준 SKU 리소스를 사용하도록 고려해야 합니다.  자세한 내용은 [표준 Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 검토하세요.

#### <a name="basic"></a>Basic

SKU 도입 전에 생성된 모든 공용 IP 주소는 기본 SKU 공용 IP 주소입니다. SKU의 도입으로 공용 IP 주소로 선택하려는 SKU를 지정하는 옵션이 만들어집니다. 기본 SKU 주소는 다음과 같습니다.

- 고정 또는 동적 할당 방법으로 할당됩니다.
- 조정 가능한 인바운드 발생 흐름 유휴 시간 제한은 4-30분(기본값은 4분)으로, 고정 아웃바운드 발생 흐름 유휴 시간 제한은 4분으로 정합니다.
- 기본적으로 열려 있습니다.  네트워크 보안 그룹을 사용하는 것이 좋지만 인바운드 또는 아웃바운드 트래픽을 제한하는 것은 선택 사항입니다.
- 네트워크 인터페이스, VPN Gateway, Application Gateway 및 인터넷 연결 부하 분산 장치 등 공용 IP 주소를 할당할 수 있는 모든 Azure 리소스에 할당됩니다.
- 가용성 영역 시나리오를 지원하지 않습니다.  가용성 영역 시나리오에 대한 표준 SKU 공용 IP를 사용해야 합니다. 사용 가능한 영역에 대해 자세히 알아보려면 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [표준 부하 분산 장치 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

#### <a name="standard"></a>Standard

표준 SKU 공용 IP 주소는 다음과 같습니다.

- 항상 고정 할당 메서드를 사용합니다.
- 조정 가능한 인바운드 발생 흐름 유휴 시간 제한은 4-30분(기본값은 4분)으로, 고정 아웃바운드 발생 흐름 유휴 시간 제한은 4분으로 정합니다.
- 기본적으로 보호되고 인바운드 트래픽에 닫혀 있습니다. [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용하여 허용된 인바운드 트래픽을 명시적으로 허용해야 합니다.
- 네트워크 인터페이스, 표준 공용 Load Balancer, Application Gateway 또는 VPN Gateway에 할당됩니다. 표준 Load Balancer에 대한 자세한 내용은 [Azure 표준 Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- 기본적으로 영역 중복되며 필요에 따라 영역별로 지정할 수 있습니다(영역별로 생성할 수 있으며 특정 가용성 영역에서 보장 가능). 사용 가능한 영역에 대해 자세히 알아보려면 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [표준 부하 분산 장치 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.
 
> [!NOTE]
> [네트워크 보안 그룹](security-overview.md#network-security-groups)을 만들어 연결하고 원하는 인바운드 트래픽을 명시적으로 허용해야 표준 SKU 리소스와 인바운드 통신할 수 있습니다.

> [!NOTE]
> 사용 하는 경우 기본 SKU 사용 하 여 공용 IP 주소를 사용할 수만 [인스턴스 메타 데이터 서비스 IMDS](../virtual-machines/windows/instance-metadata-service.md)합니다. 표준 SKU는 지원 되지 않습니다.

### <a name="allocation-method"></a>할당 방법

기본 및 표준 SKU 공용 IP 주소는 모두 *고정* 할당 방법을 지원합니다.  리소스는 생성 시에 IP 주소를 할당하고 삭제될 때 IP 주소를 해제합니다.

기본 SKU 공용 IP 주소도 할당 방법을 지정하지 않으면 기본값으로 설정되는 *동적* 할당 방법을 지원합니다.  기본 공용 IP 주소 리소스에 *동적* 할당 방법을 선택하면 IP 주소가 리소스 생성 시에 할당되지 **않음**을 의미합니다.  가상 머신과 공용 IP 주소를 연결하거나 첫 번째 가상 머신 인스턴스를 기본 부하 분산 장치의 백 엔드 풀에 배치할 때 공용 IP 주소가 할당됩니다.   리소스를 중지(또는 삭제)하면 IP 주소가 해제됩니다.  예를 들어 IP 주소는 리소스 A에서 해제된 후에 다른 리소스에 할당될 수 있습니다. 리소스 A가 중지된 동안 IP 주소가 다른 리소스에 할당되면 리소스 A를 다시 시작할 때 다른 IP 주소가 할당됩니다. 기본 공용 IP 주소 리소스의 할당 방법을 *고정*에서 *동적*으로 변경하는 경우 주소가 해제됩니다. 연결된 리소스의 IP 주소를 동일하게 유지하려면 할당 방법을 명시적으로 *static*으로 설정하면 됩니다. 고정 IP 주소가 즉시 할당됩니다.

> [!NOTE]
> 할당 방법을 *고정*으로 설정한 경우에도 공용 IP 주소 리소스에 할당된 실제 IP 주소를 지정할 수 없습니다. Azure는 리소스가 생성된 Azure 위치에서 사용 가능한 IP 주소 풀의 IP 주소를 할당됩니다.
>

정적 공용 IP 주소는 일반적으로 다음과 같은 시나리오에서 사용됩니다.

* Azure 리소스와 통신하도록 방화벽 규칙을 업데이트해야 하는 경우
* DNS 이름을 확인, IP 주소를 변경하려면 A 레코드를 업데이트해야 하는 경우
* Azure 리소스가 IP 기반 보안 모델을 사용하는 다른 앱 또는 서비스와 통신하는 경우
* IP 주소에 연결된 SSL 인증서를 사용하는 경우

> [!NOTE]
> Azure는 각 Azure 클라우드 지역의 고유한 범위에서 공용 IP 주소를 할당합니다. Azure [공용](https://www.microsoft.com/download/details.aspx?id=56519), [US 정부](https://www.microsoft.com/download/details.aspx?id=57063), [중국](https://www.microsoft.com/download/details.aspx?id=57062) 및 [독일](https://www.microsoft.com/download/details.aspx?id=57064) 클라우드의 범위(접두사) 목록을 다운로드할 수 있습니다.
>

### <a name="dns-hostname-resolution"></a>DNS 호스트 이름 확인
Azure 관리 DNS 서버에서 공용 IP 주소에 대한 *domainnamelabel*.*location*.cloudapp.azure.com 매핑을 만드는 공용 IP 리소스에 대한 DNS 도메인 이름 레이블을 지정할 수 있습니다. 예를 들어 **미국 서부** Azure *위치*에서 *domainnamelabel*로 **contoso**를 사용하여 공용 IP 리소스를 만들면 FQDN(정규화된 도메인 이름) **contoso.westus.cloudapp.azure.com**이 리소스의 공용 IP 주소로 결정됩니다. FQDN을 사용하여 Azure의 공용 IP 주소를 가리키는 사용자 지정 도메인 CNAME 레코드를 만들 수 있습니다. 기본 접미사로 DNS 이름 레이블을 사용하는 것 대신 또는 그 외에 Azure DNS 서비스를 사용하여 공용 IP 주소로 확인하는 사용자 지정 접미사로 DNS 이름을 구성할 수 있습니다. 자세한 내용은 [Azure 공용 IP 주소로 Azure DNS 사용](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)을 참조하세요.

> [!IMPORTANT]
> 생성된 각 도메인 이름은 Azure 위치 내에서 고유해야 합니다.  
>

### <a name="virtual-machines"></a>가상 머신

공용 IP 주소를 **네트워크 인터페이스**에 할당하여 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신과 연결할 수 있습니다. 가상 머신에 동적 또는 고정 공용 IP 주소를 할당할 수 있습니다. [네트워크 인터페이스에 IP 주소를 할당](virtual-network-network-interface-addresses.md)하는 방법에 대해 자세히 알아봅니다.

### <a name="internet-facing-load-balancers"></a>인터넷 연결 부하 분산 장치

공용 IP 주소를 부하 분산 장치 **프런트 엔드** 구성에 할당하여 [SKU](#sku) 또는 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)와 연결할 수 있습니다. 공용 IP 주소는 부하가 분산된 VIP(가상 IP 주소)로 사용됩니다. 부하 분산 장치 프런트 엔드에 동적 또는 정적 공용 IP 주소를 할당할 수 있습니다. 또한 부하 분산 장치 프런트 엔드에 여러 공용 IP 주소를 할당하여 SSL 기반 웹 사이트를 사용하는 다중 테넌트 환경과 같은 [다중 VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 시나리오를 구현할 수도 있습니다. Azure Load Balancer SKU에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

### <a name="vpn-gateways"></a>VPN 게이트웨이

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)는 Azure 가상 네트워크를 다른 Azure 가상 네트워크 또는 온-프레미스 네트워크에 연결합니다. 공용 IP 주소를 VPN Gateway에 할당하여 원격 네트워크와의 통신을 구현할 수 있습니다. VPN Gateway에 기본 *동적* 공용 IP 주소만을 할당할 수 있습니다.

### <a name="application-gateways"></a>애플리케이션 게이트웨이

공용 IP 주소를 게이트웨이의 [프런트 엔드](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)구성에 할당하여 Azure **Application Gateway**와 연결할 수 있습니다. 이 공용 IP 주소는 부하 분산 VIP로 사용됩니다. 만 할당할 수 있습니다는 *동적* 응용 프로그램 게이트웨이 V1 프런트 엔드 구성에 공용 IP 주소를 기본만 *정적* V2 프런트 엔드 구성에 표준 SKU 주소입니다.

### <a name="at-a-glance"></a>요약
다음 표는 공용 IP 주소를 최상위 리소스와 연결할 수 있는 특정 속성 및 사용 가능한 할당 메서드(동적 또는 고정)를 보여줍니다.

| 최상위 리소스 | IP 주소 연결 | 동적 | 공용 |
| --- | --- | --- | --- |
| 가상 머신 |Linux |예 |예 |
| 인터넷 연결 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| VPN 게이트웨이 |게이트웨이 IP 구성 |예 |예 |
| 애플리케이션 게이트웨이 |프런트 엔드 구성 |예(V1에만 해당) |예(V2에만 해당) |

## <a name="private-ip-addresses"></a>개인 IP 주소
개인 IP 주소를 사용하면 Azure 리소스가 인터넷 연결이 가능한 IP 주소를 사용하지 않고 VPN 게이트웨이 또는 ExpressRoute 회로를 통해 [가상 네트워크](virtual-networks-overview.md) 또는 온-프레미스 네트워크의 다른 리소스와 통신할 수 있습니다.

Azure Resource Manager 배포 모델에서 개인 IP 주소는 다음과 같은 유형의 Azure 리소스에 연결됩니다.

* 가상 머신 네트워크 인터페이스
* 내부 부하 분산 장치(ILB)
* 애플리케이션 게이트웨이

### <a name="ip-address-version"></a>IP 주소 버전

개인 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 개인 IPv6 주소는 동적 할당 메서드를 사용하여 할당될 수 있습니다. 가상 네트워크의 개인 IPv6 주소 간에 통신할 수 없습니다. 인터넷 연결 부하 분산 장치를 통해 인터넷의 개인 IPv6 주소에 대한 인바운드와 통신할 수 있습니다. 자세한 내용은 [IPv6으로 인터넷 연결 부하 분산 장치 만들기](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

### <a name="allocation-method"></a>할당 방법

리소스가 배포된 가상 네트워크 서브넷의 주소 범위에서 개인 IP 주소가 할당됩니다. Azure는 각 서브넷 주소 범위에서 처음 4개 주소를 예약하므로, 주소를 리소스에 할당할 수 없습니다. 예를 들어 서브넷의 주소 범위가 10.0.0.0/16인 경우, 주소 10.0.0.0-10.0.0.3은 리소스에 할당할 수 없습니다. 서브넷 주소 범위 내의 IP 주소는 한 번에 하나의 리소스에만 할당할 수 있습니다. 

개인 IP 주소를 할당하는 방법에는 두 가지가 있습니다.

- **동적**: Azure는 서브넷의 주소 범위에서 할당되지 않았거나 예약되지 않은 다음 사용 가능한 IP 주소를 할당합니다. 예를 들어 Azure는 주소 10.0.0.4-10.0.0.9가 다른 리소스에 이미 할당된 경우 10.0.0.10를 새 리소스에 할당합니다. 동적이 기본 할당 방법입니다. 할당되면 네트워크 인터페이스가 삭제되거나 동일한 가상 네트워크 내에서 다른 서브넷에 할당되거나 또는 할당 메서드가 고정으로 변경된 경우 동적 IP 주소만 해제되고 다른 IP 주소가 지정됩니다. 기본적으로 할당 메서드를 동적에서 고정으로 변경하는 경우 Azure는 이전에 동적으로 할당된 주소를 고정 주소로 할당합니다.
- **고정**: 서브넷의 주소 범위에서 할당되지 않았거나 예약되지 않은 IP 주소를 선택하여 할당합니다. 예를 들어 서브넷 주소 범위가 10.0.0.0/16이며 주소 10.0.0.4-10.0.0.9가 이미 다른 리소스에 할당된 경우 모든 주소를 10.0.0.10-10.0.255.254 사이에 할당할 수 있습니다. 고정 주소는 네트워크 인터페이스가 삭제되는 경우에만 해제됩니다. 할당 메서드를 동적으로 변경하는 경우 Azure는 주소가 서브넷 주소 범위의 사용 가능한 다음 주소가 아닌 경우에도 이전에 할당한 고정 IP 주소를 동적 주소로 할당합니다. 네트워크 인터페이스가 동일한 가상 네트워크 내의 다른 서브넷에 할당되는 경우에도 주소가 변경됩니다. 하지만 네트워크 인터페이스를 다른 서브넷에 할당하려면 먼저 할당 메서드를 고정에서 동적으로 변경해야 합니다. 네트워크 인터페이스를 다른 서브넷에 할당하면 할당 메서드를 다시 고정으로 변경하고 새로운 서브넷의 주소 범위에서 IP 주소를 할당할 수 있습니다.

### <a name="virtual-machines"></a>가상 머신

하나 이상의 개인 IP 주소는 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신에 있는 하나 이상의 **네트워크 인터페이스**에 할당됩니다. 각 개인 IP 주소에 대한 할당 방법을 동적 또는 고정으로 지정할 수 있습니다.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>내부 DNS 호스트 이름 확인(가상 머신)

모든 Azure 가상 머신은 명시적으로 사용자 지정 DNS 서버를 구성하지 않으면 기본적으로 [Azure 관리 DNS 서버](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)로 구성됩니다. 이러한 DNS 서버는 동일한 가상 네트워크 내에 있는 가상 머신에 대한 내부 이름 확인을 제공합니다.

가상 머신을 만들 때 개인 IP 주소에 호스트 이름을 매핑하는 작업이 Azure 관리 DNS 서버에 추가됩니다. 가상 컴퓨터에 다중 네트워크 인터페이스 또는 네트워크 인터페이스에 대한 다중 IP 구성이 있는 경우 기본 네트워크 인터페이스의 기본 IP 구성에 대한 개인 IP 주소에 호스트 이름이 매핑됩니다.

Azure 관리 DNS 서버를 사용하여 구성된 가상 머신은 동일한 가상 네트워크 내에 있는 모든 가상 머신의 호스트 이름을 해당 개인 IP 주소로 결정할 수 있게 됩니다. 연결된 가상 네트워크에서 가상 머신의 호스트 이름을 확인하려면 사용자 지정 DNS 서버를 사용해야 합니다.

### <a name="internal-load-balancers-ilb--application-gateways"></a>ILB(내부 부하 분산 장치) 및 애플리케이션 게이트웨이

[Azure 내부 부하 분산 장치](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)(ILB) 또는 [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 **프런트 엔드** 구성에 개인 IP 주소를 할당할 수 있습니다. 이 개인 IP 주소는 가상 네트워크 내의 리소스와 가상 네트워크에 연결된 원격 네트워크에만 액세스할 수 있는 내부 엔드포인트로 사용됩니다. 프런트 엔드 구성에 동적 또는 고정 개인 IP 주소를 할당할 수 있습니다.

### <a name="at-a-glance"></a>요약
다음 표는 개인 IP 주소가 최상위 리소스와 연결될 수 있는 특정 속성 및 사용 가능한 할당 메서드(동적 또는 정적)를 보여 줍니다.

| 최상위 리소스 | IP 주소 연결 | 않는 | 공용 |
| --- | --- | --- | --- |
| 가상 머신 |Linux |예 |예 |
| 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| 애플리케이션 게이트웨이 |프런트 엔드 구성 |예 |예 |

## <a name="limits"></a>제한
IP 주소 지정에 적용되는 제한은 Azure에서 [네트워킹에 대한 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) 전체 집합에 나와 있습니다. 제한은 지역별, 구독별로 적용됩니다. 비즈니스에 따라 최대 한도까지 기본 제한을 증가시키려면 [지원에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 하세요.

## <a name="pricing"></a>가격
공용 IP 주소에는 명목 요금이 부과될 수 있습니다. Azure의 IP 주소 가격 책정에 대한 자세한 내용은 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure 포털을 사용하여 고정 공용 IP를 사용하는 VM 배포](virtual-network-deploy-static-pip-arm-portal.md)
* [Azure 포털을 사용하여 고정 개인 IP 주소를 사용하는 VM 배포](virtual-networks-static-private-ip-arm-pportal.md)
