---
title: Azure 가상 네트워크에서 리소스에 대한 이름 확인
titlesuffix: Azure Virtual Network
description: 'Azure IaaS, 하이브리드 솔루션, 서로 다른 클라우드 서비스, Active Directory, 자체 DNS 서버 사용 시의 이름 확인 시나리오 '
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/25/2019
ms.author: rohink
ms.openlocfilehash: fe63b76589c841706ae335c61e56a57c3c33fb3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640460"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Azure 가상 네트워크에서 리소스에 대한 이름 확인

IaaS, PaaS, 하이브리드 솔루션 호스팅에 Azure를 어떻게 사용할지에 따라 VM(가상 머신) 및 가상 네트워크에 배포된 다른 리소스가 서로 통신하도록 설정해야 할 수도 있습니다. IP 주소를 이용해 통신할 수도 있지만, 기억하기 쉽고 변경되지 않는 이름을 사용하면 보다 간편합니다. 

가상 네트워크에 배포된 리소스가 도메인 이름으로 내부 IP 주소를 확인하는 방법은 다음의 두 가지가 있습니다.

* [Azure에서 제공하는 이름 확인](#azure-provided-name-resolution)
* [자체 DNS 서버를 이용한 이름 확인](#name-resolution-that-uses-your-own-dns-server) (Azure에서 제공하는 DNS 서버에 쿼리를 전달할 수 있음)

어떤 방법으로 이름을 확인할지는 사용하는 리소스가 서로 어떻게 통신해야 하는지에 따라 다릅니다. 다음 표에 각 시나리오 별로 해당하는 이름 확인 방법이 나와 있습니다.

> [!NOTE]
> 시나리오에 따라, 현재 공개 미리 보기 상태인 Azure DNS 개인 영역 기능을 사용할 수도 있습니다. 자세한 내용은 [사설 도메인에 Azure DNS 사용](../dns/private-dns-overview.md)을 참조하세요.
>

| **시나리오** | **해결 방법** | **접미사** |
| --- | --- | --- |
| 동일한 클라우드 서비스의 Azure Cloud Services 역할 인스턴스 또는 동일한 가상 네트워크에 위치한 VM 간 이름을 확인합니다. | [Azure DNS 개인 영역](../dns/private-dns-overview.md) 또는 [Azure 제공 이름 확인](#azure-provided-name-resolution) |호스트 이름 또는 FQDN |
| 다른 클라우드 서비스의 역할 인스턴스 또는 다른 가상 네트워크의 VM 간 이름을 확인합니다. |Azure(DNS 프록시)에서 이름을 확인할 수 있도록 가상 머신 간에 쿼리를 전달하는 [Azure DNS 개인 영역](../dns/private-dns-overview.md) 또는 고객이 관리하는 DNS 서버. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-that-uses-your-own-dns-server). |FQDN만 |
| 동일한 가상 네트워크에 있는 VM 또는 역할 인스턴스에 대한 가상 네트워크 통합을 사용하여 Azure App Service(웹앱, 함수, 봇)에서 이름을 확인합니다. |Azure(DNS 프록시)에서 이름을 확인할 수 있도록 가상 네트워크 간에 쿼리를 전달하는 고객이 관리하는 DNS 서버. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-that-uses-your-own-dns-server). |FQDN만 |
| App Service Web Apps로부터 동일한 가상 네트워크의 VM에 대한 이름을 확인합니다. |Azure(DNS 프록시)에서 이름을 확인할 수 있도록 가상 네트워크 간에 쿼리를 전달하는 고객이 관리하는 DNS 서버. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-that-uses-your-own-dns-server). |FQDN만 |
| 한 가상 네트워크의 App Service Web Apps로부터 다른 가상 네트워크의 VM까지 이름을 확인합니다. |Azure(DNS 프록시)에서 이름을 확인할 수 있도록 가상 네트워크 간에 쿼리를 전달하는 고객이 관리하는 DNS 서버. 자체 DNS 서버를 사용한 이름 확인을 참조하세요. |FQDN만 |
| Azure의 VM 또는 역할 인스턴스에서 온-프레미스 컴퓨터와 서비스 이름을 확인합니다. |고객이 관리하는 DNS 서버(예: 온-프레미스 도메인 컨트롤러, 로컬 읽기 전용 도메인 컨트롤러 또는 영역 전송을 사용하여 동기화된 DNS 보조). [자체 DNS 서버를 이용한 이름 확인](#name-resolution-that-uses-your-own-dns-server). |FQDN만 |
| 온-프레미스 컴퓨터에서 Azure 호스트 이름 확인. |해당 가상 네트워크에서 고객이 관리하는 DNS 프록시 서버에 쿼리를 전달하면 프록시 서버는 이름 확인을 위해 Azure에 쿼리를 전달합니다. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-that-uses-your-own-dns-server). |FQDN만 |
| 내부 IP에 대한 역방향 DNS |[자체 DNS 서버를 이용한 이름 확인](#name-resolution-that-uses-your-own-dns-server) |해당 없음 |
| 서로 다른 클라우드 서비스에 위치하며 가상 네트워크에 존재하지 않는 VM 또는 역할 인스턴스 간 이름 확인 |사용할 수 없습니다. 가상 네트워크 외부에 있는 VM과 역할 인스턴스가 서로 다른 클라우드 서비스에 위치한 경우에는 연결을 지원하지 않습니다. |해당 없음|

## <a name="azure-provided-name-resolution"></a>Azure에서 제공하는 이름 확인

Azure에서는 공용 DNS 이름 확인과 함께, 동일한 가상 네트워크 또는 클라우드 서비스 내에 있는 VM 및 역할 인스턴스에 대한 내부 이름 확인을 제공합니다. 클라우드 서비스의 VM 및 인스턴스는 동일한 DNS 접미사를 공유하므로 호스트 이름만으로 충분합니다. 그러나 클래식 배포 모델을 사용하여 배표된 가상 네트워크에서는 클라우드 서비스마다 다른 DNS 접미사를 사용합니다. 이 경우 서로 다른 클라우드 서비스 간 이름 확인을 위해 FQDN이 필요합니다. Azure Resource Manager 배포 모델을 사용하여 배포된 가상 네트워크에서 DNS 접미사는 가상 네트워크에서 일관되므로 FQDN이 필요하지 않습니다. DNS 이름은 VM 및 네트워크 인터페이스 모두에 할당할 수 있습니다. Azure에서 제공한 이름 확인은 별도로 구성할 필요가 없으나, 앞의 표에 자세히 설명된 바와 같이 모든 배포 서비스에서 선택할 수 있는 것은 아닙니다.

> [!NOTE]
> 클라우드 서비스 웹 역할 및 작업자 역할을 사용할 때 Azure 서비스 관리 REST API를 사용하면 역할 인스턴스의 내부 IP 주소에 액세스할 수도 있습니다. 자세한 내용은 [서비스 관리 REST API 참조](https://msdn.microsoft.com/library/azure/ee460799.aspx)를 참조하세요. 주소는 역할 이름 및 인스턴스 번호를 기반으로 합니다. 
>
>

### <a name="features"></a>기능

Azure 제공 이름 확인에는 다음과 같은 기능이 포함됩니다.
* 사용 편의성. 구성이 필요하지 않습니다.
* 고가용성. 자체 DNS 서버 클러스터를 만들고 관리할 필요가 없습니다.
* 온-프레미스와 Azure 호스트 이름을 모두 확인하기 위해 이 서비스를 자체 DNS 서버와 함께 사용할 수 있습니다.
* 동일한 클라우드 서비스 내에서 역할 인스턴스 및 VM 간 이름 확인을 사용할 수 있으며 FQDN은 필요하지 않습니다.
* Azure Resource Manager 배포 모델을 사용하는 가상 네트워크에서 VM 간에 이름 확인을 사용할 수 있으며 FQDN은 필요하지 않습니다. 다른 클라우드 서비스에서 이름을 확인할 때 클래식 배포 모델에서 가상 네트워크는 FQDN을 필요로 합니다. 
* 자동으로 생성되는 이름 대신 배포를 가장 잘 설명해주는 호스트 이름을 사용할 수 있습니다.

### <a name="considerations"></a>고려 사항

Azure 제공 이름 확인을 사용할 때 고려해야 할 사항입니다.
* Azure에서 만든 DNS 접미사는 수정할 수 없습니다.
* 사용자 고유의 레코드를 수동으로 등록할 수 없습니다.
* WINS 및 NetBIOS는 지원되지 않습니다. Windows 탐색기에 VM은 표시되지 않습니다.
* 호스트 이름은 DNS와 호환되어야 합니다. 이름에는 0-9, a-z 및 '-'만 사용이 가능하며, '-'로 시작하거나 끝날 수 없습니다.
* DNS 쿼리 트래픽은 각 VM에 대해 제한됩니다. 이 제한은 대부분의 애플리케이션에 영향을 주지 않아야 합니다. 요청 제한이 확인되는 경우 클라이언트쪽 캐싱이 사용하도록 설정되었는지 확인합니다. 자세한 내용은 [DNS 클라이언트 구성](#dns-client-configuration)을 참조하세요.
* 처음 180개의 클라우드 서비스 내에서 VM만 클래식 배포 모델 내의 가상 네트워크에 대해 등록됩니다. 이 제약은 Azure Resource Manager의 가상 네트워크에는 적용되지 않습니다.
* Azure DNS IP 주소는 168.63.129.16입니다. 고정 IP 주소 이며 변경 되지 않습니다.

## <a name="dns-client-configuration"></a>DNS 클라이언트 구성

이 섹션에서는 클라이언트 쪽 캐싱 및 클라이언트 쪽 재시도에 대해 설명합니다.

### <a name="client-side-caching"></a>클라이언트 쪽 캐싱 

모든 DNS 쿼리를 네트워크를 통해 전송해야 하는 것은 아닙니다. 클라이언트 쪽 캐싱을 사용하면 대기 시간을 줄이고 로컬 캐시에서 되풀이되는 DNS 쿼리를 확인하여 네트워크 블립에 대한 복원력을 개선하는 데 도움이 됩니다. DNS 레코드는 레코드 새로 고침에 영향을 주지 않으면서 캐시가 가능한 오랫동안 레코드를 저장할 수 있도록 하는 TTL(Time-To-Live) 메커니즘을 포함합니다. 따라서 클라이언트 쪽 캐싱은 대부분의 상황에 적합합니다.

기본 Windows DNS 클라이언트에는 기본 제공되는 DNS 캐시가 있습니다. 일부 Linux 배포판은 기본적으로 캐싱이 포함되지 않습니다. 로컬 캐시가 아직 없다는 것을 확인한 후에는 각 Linux VM에 DNS 캐시를 추가합니다.

사용할 수 있는 다양한 DNS 캐싱 패키지가 있습니다(예: dnsmasq). 다음은 가장 일반적인 배포판에 dnsmasq를 설치하는 방법입니다.

* **Ubuntu(resolvconf 사용)**:
  * `sudo apt-get install dnsmasq`를 사용하여 dnsmasq 패키지를 설치합니다.
* **SUSE(netconf 사용)**:
  * `sudo zypper install dnsmasq`를 사용하여 dnsmasq 패키지를 설치합니다.
  * `systemctl enable dnsmasq.service`를 사용하여 dnsmasq 서비스를 사용하도록 설정합니다. 
  * `systemctl start dnsmasq.service`를 사용하여 dnsmasq 서비스를 시작합니다. 
  * **/etc/sysconfig/network/config**를 편집하고 *NETCONFIG_DNS_FORWARDER=""* 를 *dnsmasq*로 변경합니다.
  * 캐시를 로컬 DNS 확인자로 설정하기 위해 resolv.conf를 `netconfig update`로 업데이트합니다.
* **OpenLogic(NetworkManager 사용)**:
  * `sudo yum install dnsmasq`를 사용하여 dnsmasq 패키지를 설치합니다.
  * `systemctl enable dnsmasq.service`를 사용하여 dnsmasq 서비스를 사용하도록 설정합니다.
  * `systemctl start dnsmasq.service`를 사용하여 dnsmasq 서비스를 시작합니다.
  * *prepend domain-name-servers 127.0.0.1;* 을 **/etc/dhclient-eth0.conf**에 추가합니다.
  * 캐시를 로컬 DNS 확인자로 설정하기 위해 `service network restart`로 네트워크 서비스를 다시 시작합니다.

> [!NOTE]
> dnsmasq 패키지는 여러 DNS 캐시 중에 Linux에 사용할 수 있는 유일한 캐시입니다. 사용하기 전에 특정 요구 사항에 대한 적합성을 확인하고 다른 캐시가 설치되어 있지 않은지 확인합니다.
>
>
    
### <a name="client-side-retries"></a>클라이언트 쪽 재시도

DNS는 주로 UDP 프로토콜입니다. UDP 프로토콜은 메시지 배달을 보장하지 않으므로 DNS 프로토콜 자체에서 재시도 논리가 처리됩니다. 각 DNS 클라이언트(운영 체제)는 작성자의 기본 설정에 따라 서로 다른 재시도 논리를 나타낼 수 있습니다.

* Windows 운영 체제는 1초 후 재시도한 후 2초, 4초 후 다시 재시도하고 또 다시 4초 후 재시도합니다. 
* 기본 Linux 설정에서는 5초 후 재시도합니다. 다시 시도 사양을 1초 간격으로 다섯 번으로 변경하는 것이 좋습니다.

`cat /etc/resolv.conf`를 통해 Linux VM의 현재 설정을 확인합니다. 예를 들어 *options* 줄을 살펴봅니다.

```bash
options timeout:1 attempts:5
```

resolv.conf 파일은 일반적으로 자동으로 생성되며 편집할 수 없습니다. *options* 줄을 추가하는 구체적인 단계는 배포마다 다릅니다.

* **Ubuntu** (resolvconf 사용):
  1. *options* 줄을 **/etc/resolvconf/resolv.conf.d/tail**에 추가합니다.
  2. `resolvconf -u`를 실행하여 업데이트합니다.
* **SUSE** (netconf 사용):
  1. *timeout:1 attempts:5*를 **/etc/sysconfig/network/config**의 **NETCONFIG_DNS_RESOLVER_OPTIONS=""** 매개 변수에 추가합니다.
  2. `netconfig update`를 실행하여 업데이트합니다.
* **OpenLogic** (NetworkManager 사용):
  1. *echo "options timeout:1 attempts:5"* 를 **/etc/NetworkManager/dispatcher.d/11-dhclient**에 추가합니다.
  2. `service network restart`로 업데이트합니다.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>자체 DNS 서버를 사용하는 이름 확인

이 섹션에서는 VM, 역할 인스턴스 및 웹앱에 대해 설명합니다.

### <a name="vms-and-role-instances"></a>VM 및 역할 인스턴스

이름 확인 요구 사항이 Azure에서 제공하는 기능의 범위를 벗어날 수 있습니다. 예를 들어 Microsoft Windows Server Active Directory 도메인을 사용하여 가상 네트워크 간 DNS 이름을 확인해야 할 수 있습니다. 이러한 시나리오를 해결하기 위해 Azure는 자체 DNS 서버를 사용할 수 있는 기능을 제공합니다.

가상 네트워크 내의 DNS 서버는 Azure의 재귀 확인자로 DNS 쿼리를 전달할 수 있습니다. 이렇게 하면 해당 가상 네트워크 내에서 호스트 이름을 확인할 수 있습니다. 예를 들어, Azure에서 실행 중인 DC(도메인 컨트롤러)는 해당 도메인에 대한 DNS 쿼리에 응답하고 Azure에 다른 모든 쿼리를 전달할 수 있습니다. 쿼리 전달을 통해 VM은 온-프레미스 리소스(DC를 통해)와 Azure에서 제공하는 호스트 이름(전달자를 통해)을 확인할 수 있습니다. Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다.

또한 DNS 전달로 가상 네트워크 간 DNS 확인이 가능하며 온-프레미스 컴퓨터는 Azure에서 제공하는 호스트 이름을 확인할 수 있습니다. VM의 호스트 이름을 확인하려면 DNS 서버 VM이 동일한 가상 네트워크에 있어야 하며 Azure에 호스트 이름 쿼리를 전달하도록 구성되어야 합니다. DNS 접미사는 가상 네트워크마다 다르기 때문에 올바른 가상 네트워크에 DNS 쿼리를 보내도록 조건부 전달 규칙을 사용하여 이름을 확인할 수 있습니다. 다음 이미지는 이 방법을 사용하여 가상 네트워크 간에 DNS를 확인하는 두 가상 네트워크 및 온-프레미스 네트워크를 보여 줍니다. 예제 DNS 전달자는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) 및 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)에서 사용할 수 있습니다.

> [!NOTE]
> 역할 인스턴스는 동일한 가상 네트워크 내에서 VM의 이름 확인을 수행할 수 있습니다. 이를 위해 VM의 호스트 이름과 **internal.cloudapp.net** DNS 접미사로 구성되는 FQDN을 사용하면 됩니다. 그러나 이 경우 역할 인스턴스가 [역할 스키마(.cscfg 파일)](https://msdn.microsoft.com/library/azure/jj156212.aspx)에서 정의된 이름을 가지는 경우에만 이름 확인에 성공합니다.
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> 다른 가상 네트워크(**internal.cloudapp.net** 접미사를 사용하는 FQDN)에서 VM 이름 확인을 수행해야 하는 역할 인스턴스는 이 섹션(두 가상 네트워크 간의 사용자 지정 DNS 서버 전달)에 설명된 방법을 사용하여 이 작업을 수행해야 합니다.
>

![가상 네트워크 간의 DNS 다이어그램](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Azure 제공 이름 확인을 사용하는 경우 Azure DHCP(Dynamic Host Configuration Protocol)는 각 VM에 내부 DNS 접미사(**. internal.cloudapp.net**)를 제공합니다. 호스트 이름 레코드가 **internal.cloudapp.net** 영역에 있으므로 이 접미사를 통해 호스트 이름 확인을 수행할 수 있습니다. 자체 이름 확인 솔루션을 사용하는 경우 이 접미사는 다른 DNS 아키텍처에 방해가 되기 때문에 VM에 제공되지 않습니다(예: 도메인 가입 시나리오). 대신 작동하지 않는 자리 표시자(*reddog.microsoft.com*)가 제공됩니다.

필요한 경우 PowerShell 또는 API를 사용하여 내부 DNS 접미사를 확인할 수 있습니다.

* Azure Resource Manager 배포 모델에서 가상 네트워크에 대 한 접미사를 통해 사용 합니다 [네트워크 인터페이스 REST API](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), [Get AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell cmdlet 및 [ az network nic show](/cli/azure/network/nic#az-network-nic-show) Azure CLI 명령입니다.
* 클래식 배포 모델에서 접미사는 [배포 API 가져오기](https://msdn.microsoft.com/library/azure/ee460804.aspx) 호출 또는 [Get-AzureVM -Debug](/powershell/module/servicemanagement/azure/get-azurevm) cmdlet을 통해 사용할 수 있습니다.

Azure에 전달하는 쿼리가 사용자 요구에 적합하지 않은 경우 자체 DNS 솔루션을 제공해야 합니다. DNS 솔루션은 다음을 수행해야 합니다:

* [DDNS](virtual-networks-name-resolution-ddns.md) 등을 통해 적절한 호스트 이름 확인을 제공합니다. DDNS를 사용하는 경우 DNS 레코드 청소 기능을 해제해야 할 수도 있습니다. Azure DHCP 임대는 기간이 매우 길며 청소를 통해 DNS 레코드가 완전히 제거될 수 있습니다. 
* 외부 도메인 이름을 확인할 수 있도록 적절한 재귀 확인을 제공해야 합니다.
* 제공하는 클라이언트에서 액세스 가능해야 하고(포트 53에서 TCP 및 UDP) 인터넷에 액세스할 수 있어야 합니다.
* 외부 에이전트로 인해 나타나는 위험을 완화하기 위해 인터넷의 액세스로부터 보호되어야 합니다.

> [!NOTE]
> 최상의 성능을 위해 Azure VM을 DNS 서버로 사용할 경우 IPv6를 사용하지 않도록 설정해야 합니다. [공용 IP](virtual-network-public-ip-address.md)를 각 DNS 서버 VM에 할당해야 합니다. Windows Server를 DNS 서버로 사용할 때의 추가 성능 분석 및 최적화는 [재귀적 Windows DNS Server 2012 R2의 이름 확인 성능](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx)을 참조하세요.
> 
> 

### <a name="web-apps"></a>웹 앱
가상 네트워크 또는 동일한 가상 네트워크의 VM에 연결된 App Service를 사용하여 빌드된 웹앱에서 이름 확인을 수행해야 한다고 가정합니다. Azure(가상 IP 168.63.129.16)로 쿼리를 전달하는 DNS 전달자가 있는 사용자 지정 DNS 서버를 설정하는 것 외에 다음 단계를 수행합니다.
1. [가상 네트워크와 앱 통합](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에서 설명한 대로 웹앱에 대해 가상 네트워크 통합을 사용하도록 설정합니다(아직 수행하지 않은 경우).
2. Azure Portal에서 웹앱을 호스트하는 AppService 계획에 대해 **네트워킹**의 **가상 네트워크 통합**에서 **네트워크 동기화**를 선택합니다.

    ![가상 네트워크 이름 확인 스크린샷](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

가상 네트워크에 연결된 App Service를 사용하여 빌드된 웹앱으로부터 다른 가상 네트워크의 VM으로의 이름 확인을 수행할 필요가 있는 경우 다음과 같이 두 가상 네트워크 모두에서 사용자 지정 DNS 서버를 사용해야 합니다.

* Azure의 재귀 확인자(가상 IP 168.63.129.16)에도 쿼리를 전달할 수 있는 VM에서 대상 가상 네트워크에 DNS 서버를 설정합니다. 예제 DNS 전달자는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) 및 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)에서 사용할 수 있습니다. 
* VM에서 원본 가상 네트워크의 DNS 전달자를 설정합니다. 대상 가상 네트워크의 DNS 서버로 쿼리를 전달하도록 이 DNS 전달자를 구성합니다.
* 원본 가상 네트워크의 설정에 원본 DNS 서버를 구성합니다.
* [가상 네트워크와 앱 통합](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 지침에 따라 원본 가상 네트워크에 웹앱을 연결하기 위해 가상 네트워크 통합을 사용하도록 설정합니다.
* Azure Portal에서 웹앱을 호스트하는 AppService 계획에 대해 **네트워킹**의 **가상 네트워크 통합**에서 **네트워크 동기화**를 선택합니다.

## <a name="specify-dns-servers"></a>DNS 서버 지정
사용자 고유의 DNS 서버를 사용할 때 Azure는 가상 네트워크당 자체 DNS 서버를 지정할 수 있는 기능을 제공합니다. 또한 네트워크 인터페이스(Azure Resource Manager용) 또는 클라우드 서비스(클래식 배포 모델)당 여러 DNS 서버를 지정할 수도 있습니다. 네트워크 인터페이스 또는 클라우드 서비스에 대해 지정된 DNS 서버가 가상 네트워크에 대해 지정된 서버보다 우선적으로 사용됩니다.

> [!NOTE]
> DNS 서버 IP와 같은 네트워크 연결 속성은 Windows VM 내에서 직접 편집하지 않는 것이 좋습니다. 가상 네트워크 어댑터가 교체될 때 서비스 복구 동안 지워질 수 있기 때문입니다.
>
>

Azure Resource Manager 배포 모델을 사용하는 경우 가상 네트워크 및 가상 인터페이스에 대해 DNS 서버를 지정할 수 있습니다. 자세한 내용은 [가상 네트워크 관리](manage-virtual-network.md) 및 [네트워크 인터페이스 관리](virtual-network-network-interface.md)를 참조합니다.

> [!NOTE]
> 가상 네트워크에 사용자 지정 DNS 서버를 포함하려는 경우에는 DNS 서버 IP 주소를 하나 이상 지정해야 합니다. 이렇게 하지 않으면 가상 네트워크에서 구성을 무시하고 Azure에서 제공하는 DNS를 대신 사용합니다.
>
>

클래식 배포 모델을 사용할 때 Azure Portal 또는 [네트워크 구성 파일](https://msdn.microsoft.com/library/azure/jj157100)에서 가상 네트워크에 대한 DNS 서버를 지정할 수 있습니다. 클라우드 서비스의 경우 DNS 서버는 [서비스 구성 파일](https://msdn.microsoft.com/library/azure/ee758710)을 통해 또는 [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm)을 사용하여 지정할 수 있습니다.

> [!NOTE]
> 이미 배포된 가상 네트워크 또는 가상 머신에 대한 DNS 설정을 변경하면 영향을 받는 각 VM을 다시 시작해야 변경사항이 적용됩니다.
>
>

## <a name="next-steps"></a>다음 단계

Azure Resource Manager 배포 모델:

* [가상 네트워크 관리](manage-virtual-network.md)
* [네트워크 인터페이스 관리](virtual-network-network-interface.md)

클래식 배포 모델:

* [Azure 서비스 구성 스키마](https://msdn.microsoft.com/library/azure/ee758710)
* [Virtual Network 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100)
* [네트워크 구성 파일을 사용하여 Virtual Network 구성](virtual-networks-using-network-configuration-file.md)
