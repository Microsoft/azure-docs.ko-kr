---
title: '자습서: 네트워크 검사 목록'
description: 네트워크 연결 및 네트워크 포트에 대한 네트워크 요구 사항 사전 요구 사항 및 세부 정보입니다.
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: aba5d7767e420b3ade6238621487884e44fbb6e2
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750418"
---
# <a name="networking-checklist-for-azure-vmware-solution"></a>Azure VMware Solution에 대한 네트워킹 검사 목록 

Azure VMware 솔루션은 온-프레미스 및 Azure 기반 환경 또는 리소스의 사용자와 애플리케이션에 액세스할 수 있는 VMware 프라이빗 클라우드 환경을 제공합니다. 이 연결은 Azure ExpressRoute 및 VPN 연결과 같은 네트워킹 서비스를 통해 제공되며, 서비스를 사용하도록 설정하려면 특정 네트워크 주소 범위와 방화벽 포트가 필요합니다. 이 문서에서는 Azure VMware Solution에서 작동하도록 네트워킹을 제대로 구성하는 데 필요한 정보를 제공합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 네트워크 연결 요구 사항
> * Azure VMware Solution의 DHCP

## <a name="virtual--network-and-expressroute-circuit--considerations"></a>가상 네트워크 및 ExpressRoute 회로 고려 사항
구독의 가상 네트워크에서 연결을 만들 때 ExpressRoute 회로는 피어링을 통해 설정되며, Azure Portal에서 요청하는 권한 부여 키와 피어링 ID를 사용합니다. 피어링은 프라이빗 클라우드와 가상 네트워크 간의 일대일 연결입니다.

> [!NOTE] 
> ExpressRoute 회로는 프라이빗 클라우드 배포에 포함되지 않습니다. 온-프레미스 ExpressRoute 회로는 이 문서의 범위를 벗어났습니다. 프라이빗 클라우드에 대한 온-프레미스 연결이 필요한 경우 기존 ExpressRoute 회로 중 하나를 사용하거나 Azure Portal에서 구매할 수 있습니다.

프라이빗 클라우드를 배포할 때 vCenter 및 NSX-T Manager에 대한 IP 주소를 수신합니다. 이러한 관리 인터페이스에 액세스하려면 구독의 가상 네트워크에 추가 리소스를 만들어야 합니다. 자습서에서 이러한 리소스를 만들고 ExpressRoute 개인 피어링을 설정하는 절차를 찾을 수 있습니다.

프라이빗 클라우드 논리 네트워킹에는 미리 프로비저닝된 NSX-T가 제공됩니다. 계층-0 게이트웨이 및 계층-1 게이트웨이는 사용자를 위해 미리 프로비저닝됩니다. 세그먼트를 만들고 기존 계층-1 게이트웨이에 연결하거나 정의한 새 계층-1 게이트웨이에 연결할 수 있습니다. NSX-T 논리 네트워킹 구성 요소는 워크로드 간에 동-서 연결을 제공하고 인터넷 및 Azure 서비스에 대한 북-남 연결을 제공합니다.

## <a name="routing-and-subnet-considerations"></a>라우팅 및 서브넷 고려 사항
AVS 프라이빗 클라우드는 Azure ExpressRoute 연결을 사용하여 Azure 가상 네트워크에 연결됩니다. 높은 대역폭이 높고 대기 시간이 짧은 이 연결을 사용하면 프라이빗 클라우드 환경의 Azure 구독에서 실행되는 서비스에 액세스할 수 있습니다. 라우팅은 BGP(Border Gateway Protocol) 기반이며, 자동으로 프로비저닝되며, 각 프라이빗 클라우드 배포에 대해 기본적으로 사용하도록 설정됩니다. 

AVS 프라이빗 클라우드의 서브넷에는 `/22` CIDR 네트워크 주소 블록 이상이 필요합니다(아래 참조). 이 네트워크는 온-프레미스 네트워크를 보완합니다. 주소 블록은 구독과 온-프레미스 네트워크에 있는 다른 가상 네트워크에서 사용되는 주소 블록과 겹칠 수 없습니다. 이 주소 블록 내에서 관리, 프로비저닝 및 vMotion 네트워크는 자동으로 프로비저닝됩니다.

`/22` CIDR 네트워크 주소 블록의 예: `10.10.0.0/22`

서브넷:

| 네트워크 사용량             | 서브넷 | 예제        |
| ------------------------- | ------ | -------------- |
| 프라이빗 클라우드 관리  | `/24`  | `10.10.0.0/24` |
| vMotion 네트워크           | `/24`  | `10.10.1.0/24` |
| VM 워크로드              | `/24`  | `10.10.2.0/24` |
| ExpressRoute 피어링      | `/24`  | `10.10.3.8/30` |


### <a name="network-ports-required-to-communicate-with-the-service"></a>서비스와 통신하는 데 필요한 네트워크 포트

| 원본 | 대상 | 프로토콜 | 포트 | Description  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| 프라이빗 클라우드 DNS 서버 | 온-프레미스 DNS 서버 | UDP | 53 | 온-프레미스 DNS 쿼리에 대한 PC vCenter의 DNS 클라이언트 - 전달 요청(아래의 DNS 섹션 확인) |  
| 온-프레미스 DNS 서버   | 프라이빗 클라우드 DNS 서버 | UDP | 53 | 온-프레미스 서비스에서 프라이빗 클라우드 DNS 서버로의 DNS 클라이언트 - 전달 요청(아래의 DNS 섹션 확인) |  
| 온-프레미스 네트워크  | 프라이빗 클라우드 vCenter 서버  | TCP(HTTP)  | 80 | vCenter Server에는 직접 HTTP 연결용 80 포트가 필요합니다. 80 포트는 요청을 443 HTTPS 포트로 리디렉션합니다. 이 리디렉션은  `https://server` 대신  `http://server`를 사용하는 경우에 유용합니다.   <br><br>WS-Management(443 포트도 열어야 함) <br><br>vCenter Server에서 번들로 제공되는 SQL Server 2008 데이터베이스가 아니라 사용자 지정 Microsoft SQL 데이터베이스를 사용하는 경우 SQL Reporting Services에서 80 포트가 사용됩니다. vCenter Server를 설치하면 설치 관리자에서 vCenter Server의 HTTP 포트를 변경하라는 메시지가 표시됩니다. 설치를 성공적으로 수행하려면 vCenter Server HTTP 포트를 사용자 지정 값으로 변경합니다. Microsoft IIS(인터넷 정보 서비스)도 80 포트를 사용합니다. 80 포트에 대한 vCenter Server와 IIS 간의 충돌을 참조하세요. |  
| 프라이빗 클라우드 관리 네트워크 | 온-프레미스 Active Directory  | TCP  | 389 | 이 포트는 vCenter Server의 로컬 및 모든 원격 인스턴스에서 열려 있어야 합니다. 이 포트는 vCenter Server 그룹의 디렉터리 서비스에 대한 LDAP 포트 번호입니다. 이 vCenter Server 인스턴스를 Linked Mode 그룹에 조인하지 않더라도 vCenter Server 시스템에서 389 포트에 바인딩해야 합니다. 이 포트에서 다른 서비스가 실행되는 경우 해당 서비스를 제거하거나 해당 포트를 다른 포트로 변경하는 것이 더 적합할 수 있습니다. LDAP 서비스는 1025~65535의 모든 포트에서 실행할 수 있습니다.  이 인스턴스가 Microsoft Windows Active Directory로 사용되는 경우 포트 번호를 389에서 1025~65535의 사용 가능한 포트로 변경합니다. 이 포트는 선택 사항이며, 프라이빗 클라우드 vCenter에서 온-프레미스 AD를 ID 원본으로 구성하기 위한 것입니다. |  
| 온-프레미스 네트워크  | 프라이빗 클라우드 vCenter 서버  | TCP(HTTPS)  | 443 | 이 포트를 사용하면 온-프레미스 네트워크에서 vCenter에 액세스할 수 있습니다. vCenter Server 시스템에서 vSphere Client의 연결을 수신 대기하는 데 사용하는 기본 포트입니다. vCenter Server 시스템에서 vSphere Client의 데이터를 받을 수 있도록 하려면 방화벽에서 443 포트를 엽니다. 또한 vCenter Server 시스템은 443 포트를 사용하여 SDK 클라이언트로부터의 데이터 전송을 모니터링합니다. 이 포트는 다음 서비스에도 사용됩니다. WS-Management(80 포트도 열어야 함), vSphere 업데이트 관리자에 대한 vSphere Client 액세스, vCenter Server에 대한 타사 네트워크 관리 클라이언트 연결, 호스트에 대한 타사 네트워크 관리 클라이언트 액세스 |  
| 웹 브라우저  | 하이브리드 클라우드 관리자  | TCP(HTTPS) | 9443 | 하이브리드 클라우드 관리자 시스템 구성을 위한 하이브리드 클라우드 관리자 가상 어플라이언스 관리 인터페이스입니다. |
| 관리자 네트워크  | 하이브리드 클라우드 관리자 | SSH | 22 | 하이브리드 클라우드 관리자에 대한 관리자 SSH 액세스입니다. |
| HCM | 클라우드 게이트웨이 | TCP(HTTPS) | 8123 | 호스트 기반 복제 서비스 지침을 하이브리드 클라우드 게이트웨이로 보냅니다. |
| HCM | 클라우드 게이트웨이 | HTTP TCP(HTTPS) | 9443 | REST API를 사용하여 관리 지침을 로컬 하이브리드 클라우드 게이트웨이로 보냅니다. |
| 클라우드 게이트웨이 | L2C | TCP(HTTPS) | 443 | L2C에서 하이브리드 클라우드 게이트웨이와 동일한 경로를 사용하는 경우 관리 지침을 클라우드 게이트웨이에서 L2C로 보냅니다. |
| 클라우드 게이트웨이 | ESXi 호스트 | TCP | 80,902 | 관리 및 OVF 배포 |
| 클라우드 게이트웨이(로컬)| 클라우드 게이트웨이(원격) | UDP | 4500 | IPSEC Internet Key Exchange(IKEv2)가<br>   양방향 터널에 대한 워크로드를 캡슐화하는 데 필요합니다. NAT-T(Network Address Translation-Traversal)도 지원됩니다. |
| 클라우드 게이트웨이(로컬) | 클라우드 게이트웨이(원격)  | UDP | 500 | 양방향 터널에 대한<br> IPSEC Internet Key Exchange(ISAKMP)에 필요합니다. |
| 온-프레미스 vCenter 네트워크 | 프라이빗 클라우드 관리 네트워크 | TCP | 8000 |  온-프레미스 vCenter에서 프라이빗 클라우드 vCenter로의 VM vMotion   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>DHCP 및 DNS 확인 고려 사항
프라이빗 클라우드 환경에서 실행되는 애플리케이션 및 워크로드에는 조회 및 IP 주소 할당을 위한 이름 확인 및 DHCP 서비스가 필요합니다. 이러한 서비스를 제공하려면 적절한 DHCP 및 DNS 인프라가 필요합니다. 프라이빗 클라우드 환경에서 이러한 서비스를 제공하도록 가상 머신을 구성할 수 있습니다.  

WAN을 통해 브로드캐스트 DHCP 트래픽을 온-프레미스로 다시 라우팅하는 대신, NSX에 기본 제공되어 있거나 프라이빗 클라우드에서 로컬 DHCP 서버를 사용하는 DHCP 서비스를 사용하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * 네트워크 연결 요구 사항
> * Azure VMware Solution의 DHCP

적절한 네트워킹이 제대로 준비되면 Azure VMware Solution 프라이빗 클라우드를 만들기 위해 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: Azure VMware Solution 프라이빗 클라우드 만들기](tutorial-create-private-cloud.md)
