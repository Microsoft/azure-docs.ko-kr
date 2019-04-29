---
title: Azure의 Linux 가상 머신에 대한 DNS 이름 확인 옵션
description: Azure IaaS의 Linux 가상 머신에 대한 이름 확인 시나리오(제공된 DNS 서비스, 하이브리드 외부 DNS 및 사용자 고유의 DNS 서버 포함)입니다.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: jeconnoc
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: ae8315b2a484cddc500b5c2dd02a019cb4f46d8e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127092"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Azure의 Linux 가상 머신에 대한 DNS 이름 확인 옵션
Azure는 단일 가상 네트워크 내에 포함된 모든 가상 머신에 대해 기본적으로 DNS 이름 확인을 제공합니다. Azure에서 호스트하는 가상 머신에서 자체 DNS 서비스를 구성하여 사용자 고유의 DNS 이름 확인 솔루션을 구현할 수 있습니다. 다음 시나리오는 특정 상황에 적합한 솔루션을 선택하는 데 도움이 됩니다.

* [Azure에서 제공하는 이름 확인](#name-resolution-that-azure-provides)
* [자체 DNS 서버를 사용한 이름 확인](#name-resolution-using-your-own-dns-server)

어떤 방법으로 이름을 확인할지는 사용하는 가상 머신과 역할 인스턴스가 서로 어떻게 통신해야 하는지에 따라 다릅니다.

다음 표에 각 시나리오 별로 해당하는 이름 확인 방법이 나와 있습니다.

| **시나리오** | **해결 방법** | **접미사** |
| --- | --- | --- |
| 동일한 가상 네트워크에 있는 역할 인스턴스 또는 가상 머신 간 이름 확인 |Azure에서 제공하는 이름 확인 |호스트 이름 또는 FQDN(정규화된 도메인 이름) |
| 서로 다른 네트워크에 있는 역할 인스턴스 또는 가상 머신 간 이름 확인 |Azure(DNS 프록시)에서 이름을 확인할 수 있도록 가상 컴퓨터 간에 쿼리를 전달하는 고객이 관리하는 DNS 서버. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server). |FQDN만 |
| Azure의 역할 인스턴스 또는 가상 머신에서 온-프레미스 컴퓨터와 서비스 이름 확인 |고객이 관리하는 DNS 서버(예: 온-프레미스 도메인 컨트롤러, 로컬 읽기 전용 도메인 컨트롤러 또는 영역 전송을 사용하여 동기화된 DNS 보조). [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server). |FQDN만 |
| 온-프레미스 컴퓨터에서 Azure 호스트 이름 확인 |해당하는 가상 네트워크에서 고객이 관리하는 DNS 프록시 서버에 쿼리를 전달합니다. 프록시 서버는 이름 확인을 위해 Azure에 쿼리를 전달합니다. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server). |FQDN만 |
| 내부 IP에 대한 역방향 DNS |[자체 DNS 서버를 사용한 이름 확인](#name-resolution-using-your-own-dns-server) |해당 없음 |

## <a name="name-resolution-that-azure-provides"></a>Azure에서 제공하는 이름 확인
Azure에서는 공용 DNS 이름 확인과 함께, 동일한 가상 네트워크에 있는 가상 머신 및 역할 인스턴스에 대한 내부 이름 확인을 제공합니다. Azure Resource Manager 기반의 가상 네트워크에서 DNS 접미사는 가상 네트워크에서 일관적이며 FQDN이 필요하지 않습니다. DNS 이름은 NIC(네트워크 인터페이스 카드)와 가상 머신에 모두 할당할 수 있습니다. Azure에서 제공하는 이름 확인은 별도로 구성할 필요가 없지만, 앞의 표에 나온 바와 같이 모든 배포 서비스에 적합한 것은 아닙니다.

### <a name="features-and-considerations"></a>기능 및 고려 사항
**기능:**

* Azure에서 제공하는 이름 확인을 사용하기 위해 별도로 구성할 필요가 없습니다.
* Azure에서 제공하는 이름 확인 서비스는 가용성이 높습니다. 자체 DNS 서버 클러스터를 만들고 관리할 필요가 없습니다.
* Azure에서 제공하는 이름 확인 서비스는 자체 DNS 서버와 함께 사용하여 온-프레미스와 Azure 호스트 이름을 모두 확인할 수 있습니다.
* 가상 네트워크의 가상 머신 간에 이름 확인이 제공되며 FQDN은 필요 없습니다.
* 자동으로 생성되는 이름 대신 배포를 가장 잘 설명해주는 호스트 이름을 사용할 수 있습니다.

**고려 사항:**

* Azure에서 만드는 DNS 접미사는 수정할 수 없습니다.
* 사용자 고유의 레코드를 수동으로 등록할 수 없습니다.
* WINS 및 NetBIOS는 지원되지 않습니다.
* 호스트 이름은 DNS와 호환되어야 합니다.
    이름에는 0-9, a-z 및 '-'만 사용이 가능하며, '-'로 시작하거나 끝날 수 없습니다. RFC 3696 섹션을 2를 참조하세요.
* DNS 쿼리 트래픽은 각 가상 컴퓨터에 대해 제한됩니다. 이 제한은 대부분의 애플리케이션에 영향을 주지 않아야 합니다.  요청 제한이 확인되는 경우 클라이언트쪽 캐싱이 사용하도록 설정되었는지 확인합니다.  자세한 내용은 [Azure에서 제공하는 이름 확인 활용](#getting-the-most-from-name-resolution-that-azure-provides)을 참조하세요.

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Azure에서 제공하는 이름 확인 활용
**클라이언트쪽 캐싱:**

일부 DNS 쿼리는 네트워크를 통해 전송되지 않습니다. 클라이언트쪽 캐싱을 사용하면 대기 시간을 줄이고 로컬 캐시에서 되풀이되는 DNS 쿼리를 확인하여 네트워크 불일치에 대한 복원력을 개선하는 데 도움이 됩니다. DNS 레코드는 레코드 새로 고침에 영향을 주지 않으면서 캐시가 가능한 오랫동안 레코드를 저장할 수 있도록 하는 TTL(Time-To-Live)을 포함하고 있습니다. 결과적으로 클라이언트쪽 캐싱은 대부분의 상황에 적합합니다.

일부 Linux 배포판은 기본적으로 캐싱이 포함되지 않습니다. 로컬 캐시가 아직 없는 것으로 확인되면 각 Linux 가상 머신에 캐시를 추가하는 것이 좋습니다.

dnsmasq 같은 여러 가지 DNS 캐싱 패키지를 사용할 수 있습니다. 다음은 가장 일반적인 배포판에 Dnsmasq를 설치하는 단계입니다.

**Ubuntu(resolvconf 사용)**
  * dnsmasq 패키지를 설치합니다("sudo apt-get install dnsmasq").

**SUSE(netconf 사용)**:
1. dnsmasq 패키지를 설치합니다("sudo zypper install dnsmasq").
2. dnsmasq 서비스를 사용하도록 설정합니다("systemctl enable dnsmasq.service").
3. dnsmasq 서비스를 시작합니다("systemctl start dnsmasq.service").
4. "/etc/sysconfig/network/config"를 편집하고 NETCONFIG_DNS_FORWARDER=""를 "dnsmasq"로 변경합니다.
5. 캐시를 로컬 DNS 확인자로 설정하기 위해 resolv.conf("netconfig update")를 업데이트합니다.

**Rogue Wave Software의 CentOS(이전의 OpenLogic. NetworkManager 사용)**
1. dnsmasq 패키지를 설치합니다("sudo yum install dnsmasq").
2. dnsmasq 서비스를 사용하도록 설정합니다("systemctl enable dnsmasq.service").
3. dnsmasq 서비스를 시작합니다("systemctl start dnsmasq.service").
4. "prepend domain-name-servers 127.0.0.1;"을 "/etc/dhclient-eth0.conf"에 추가합니다.
5. 캐시를 로컬 DNS 확인자로 설정하기 위해 네트워크 서비스("service network restart")를 다시 시작합니다.

> [!NOTE]
> : 'dnsmasq' 패키지는 Linux에서 사용할 수 있는 많은 DNS 캐시 중 하나입니다. 사용하기 전에 본인의 요구 사항에 적합한지 확인하고 다른 캐시가 설치되어 있지 않은지 확인합니다.
>
>

**클라이언트쪽 재시도**

DNS는 주로 UDP 프로토콜입니다. UDP 프로토콜은 메시지 배달을 보장하지 않으므로 DNS 프로토콜 자체에서 재시도 논리를 처리합니다. 각 DNS 클라이언트(운영 체제)는 작성자의 기본 설정에 따라 서로 다른 재시도 논리를 나타낼 수 있습니다.

* Windows 운영 체제는 1초 후 재시도한 후 2초, 4초 후 다시 재시도하고 또 다시 4초 후 재시도합니다.
* 기본 Linux 설정에서는 5초 후 재시도합니다.  1초 간격으로 5번 재시도하도록 변경해야 합니다.  

Linux 가상 컴퓨터에서 현재 설정을 확인하려면 'cat /etc/resolv.conf'에서 'options' 줄을 확인합니다. 예를 들면 다음과 같습니다.

    options timeout:1 attempts:5

resolv.conf 파일은 자동으로 생성되며 편집할 수 없습니다. 'options' 줄을 추가하는 구체적인 단계는 배포판마다 다릅니다.

**Ubuntu**(resolvconf 사용)
1. options 줄을 '/etc/resolveconf/resolv.conf.d/head'에 추가합니다.
2. 'resolvconf -u'를 실행하여 업데이트합니다.

**SUSE**(netconf 사용)
1. 'timeout:1 attempts:5'를 '/etc/sysconfig/network/config'의 NETCONFIG_DNS_RESOLVER_OPTIONS="" 매개 변수에 추가합니다.
2. 'netconfig update'를 실행하여 업데이트합니다.

**Rogue Wave Software의 CentOS(이전의 OpenLogic)**(NetworkManager 사용)
1. 'RES_OPTIONS="timeout:1 attempts:5"'를 '/etc/sysconfig/network'에 추가합니다.
2. 'service network restart'를 실행하여 업데이트합니다.

## <a name="name-resolution-using-your-own-dns-server"></a>자체 DNS 서버를 이용한 이름 확인
이름 확인 요구 사항이 Azure에서 제공하는 기능의 범위를 벗어날 수 있습니다. 예를 들어 가상 네트워크 간 DNS 확인이 필요할 수 있습니다. 이 시나리오를 해결하기 위해 자체 DNS 서버를 사용할 수 있습니다.  

가상 네트워크에 있는 DNS 서버는 Azure의 재귀 확인자에게 DNS 쿼리를 전달하여 동일한 가상 네트워크에 있는 호스트 이름을 확인할 수 있습니다. 예를 들어 Azure에서 실행되는 DNS 서버는 자체 DNS 영역 파일에 대한 DNS 쿼리에 응답하고 Azure에 다른 모든 쿼리를 전달할 수 있습니다. 이 기능을 통해 가상 머신은 사용자의 영역 파일과 Azure에서 제공하는(전달자를 통해) 호스트 이름의 항목을 모두 볼 수 있습니다. Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다.

또한 DNS 전달을 통해 가상 네트워크 간 DNS 확인이 가능하고 온-프레미스 컴퓨터가 Azure에서 제공하는 호스트 이름을 확인할 수 있습니다. 가상 머신의 호스트 이름을 확인하려면 DNS 서버 가상 머신이 동일한 가상 네트워크에 있어야 하며 Azure에 호스트 이름 쿼리를 전달하도록 구성되어야 합니다. DNS 접미사는 가상 네트워크마다 다르기 때문에 올바른 가상 네트워크에 DNS 쿼리를 보내도록 조건부 전달 규칙을 사용하여 이름을 확인할 수 있습니다. 다음 이미지는 이 방법을 사용하여 가상 네트워크 간에 DNS를 확인하는 두 가상 네트워크 및 온-프레미스 네트워크를 보여 줍니다.

![가상 네트워크 간의 DNS 확인](./media/azure-dns/inter-vnet-dns.png)

Azure에서 제공하는 이름 확인을 사용하는 경우 DHCP를 사용하여 각 가상 머신에 내부 DNS 접미사가 제공됩니다. 자체 이름 확인 솔루션을 사용하는 경우 이 접미사는 다른 DNS 아키텍처에 방해가 되기 때문에 가상 머신에 제공되지 않습니다. FQDN으로 컴퓨터를 참조하거나 가상 머신에 접미사를 구성하려면 PowerShell 또는 API를 사용하여 접미사를 확인할 수 있습니다.

* Azure Resource Manager에서 관리하는 가상 네트워크의 경우 [네트워크 인터페이스 카드](https://msdn.microsoft.com/library/azure/mt163668.aspx) 리소스를 통해 접미사가 제공됩니다. 또한 `azure network public-ip show <resource group> <pip name>` 명령을 실행하여 NIC의 FQDN을 포함하고 있는 공용 IP 세부 정보를 표시할 수 있습니다.

Azure에 전달하는 쿼리가 사용자 요구에 적합하지 않은 경우 자체 DNS 솔루션을 제공해야 합니다.  DNS 솔루션은 다음을 수행해야 합니다:

* 예를 들어 [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md)를 통해 적절한 호스트 이름 확인을 제공해야 합니다. DDNS를 사용하는 경우 DNS 레코드 청소 기능을 해제해야 할 수도 있습니다. Azure의 DHCP 임대는 기간이 매우 길며 청소를 통해 DNS 레코드가 완전히 제거될 수 있습니다.
* 외부 도메인 이름을 확인할 수 있도록 적절한 재귀 확인을 제공해야 합니다.
* 제공하는 클라이언트에서 액세스 가능해야 하고(포트 53에서 TCP 및 UDP) 인터넷에 액세스할 수 있어야 합니다.
* 외부 에이전트로 인해 나타나는 위험을 완화하기 위해 인터넷의 액세스로부터 보호되어야 합니다.

> [!NOTE]
> 최상의 성능을 얻을 수 있도록 Azure DNS 서버에서 가상 머신을 사용할 때 IPv6를 비활성화하고 각 DNS 서버 가상 머신에 [인스턴스 수준 공용 IP](../../virtual-network/virtual-networks-instance-level-public-ip.md)를 할당합니다.  
>
>
