---
title: App Service Environment의 네트워크 아키텍처 개요 - Azure
description: 앱 서비스 환경의 네트워크 토폴로지의 아키텍처 개요입니다.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 0d7d4af46e54ad89e0d084cb15af13e56115e996
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765314"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>App Service Environment의 네트워크 아키텍처 개요
## <a name="introduction"></a>소개
App Service 환경은 [가상 네트워크][virtualnetwork]의 서브넷에서 항상 만들어지고, App Service 환경에서 실행되는 앱은 동일한 가상 네트워크 토폴로지 내에 위치한 개인 엔드포인트와 통신할 수 있습니다.  고객은 그들의 가상 네트워크 일부를 잠글 수 있기 때문에 App Service Environment에서 일어나는 네트워크 통신 흐름의 유형을 이해하는 것은 중요합니다.

## <a name="general-network-flow"></a>일반 네트워크 흐름
ASE(App Service Environment)가 앱에 공용 VIP(가상 IP 주소)를 사용하는 경우 모든 인바운드 트래픽이 해당 공용 VIP에 도착합니다.  여기에는 FTP에 대한 다른 트래픽, 원격 디버깅 기능, Azure 관리 작업과 마찬가지로 앱의 HTTP와 HTTPS 트래픽이 포함됩니다.  공용 VIP에서 사용할 수 있는 특정 포트(필수 및 선택적)의 전체 목록은 App Service 환경의 [인바운드 트래픽 제어][controllinginboundtraffic] 문서를 참조하세요. 

또한 App Service Environment는 ILB(내부 부하 분산 장치) 주소라고도 하는 가상 네트워크 내부 주소에만 바인딩되는 앱 실행을 지원합니다.  ILB 지원 ASE에서 앱에 대한 HTTP 및 HTTPS 트래픽과 원격 디버깅 호출은 ILB 주소에 도착합니다.  가장 일반적인 ILB-ASE 구성에서는 FTP/FTPS 트래픽도 ILB 주소에 도착합니다.  그러나 Azure 관리 작업은 여전히 ILB 지원 ASE의 공용 VIP에 있는 포트 454/455로 이동합니다.

아래 다이어그램은 앱이 공용 가상 IP 주소에 바인딩되는 App Service Environment에 대한 다양한 인바운드 및 아웃바운드 네트워크 흐름을 대략적으로 보여 줍니다.

![일반 네트워크 흐름][GeneralNetworkFlows]

App Service Environment는 다양한 개인 고객 엔드포인트와 통신할 수 있습니다.  예를들어, App Service Environment에서 실행 중인 앱은 동일한 가상 네트워크 토폴로지 안의 Iaas 가상 머신에서 실행되는 데이터베이스 서버에 연결할 수 있습니다.

> [!IMPORTANT]
> 네트워크 다이어그램을 보면 "다른 Compute 리소스"는 App Service Environment에서 다른 서브넷에 배포됩니다. ASE와 동일한 서브넷에 리소스를 배포하면 (특정 ASE 내 라우팅을 제외하고) ASE에서 해당 리소스로 연결을 차단합니다. 대신 (동일한 VNET에서) 다른 서브넷에 배포합니다. App Service Environment에 연결할 수 있게 됩니다. 추가 구성은 필요하지 않습니다.
> 
> 

App Service Environment 역시 관리 및 운영에 필요한 sql DB 및 Azure Storage와 통신할 수 있습니다.  App Service Environment와 통신하는 일부 SQL 및 저장소 리소스는 App Service Environment는와 같은 지역에 위치해 있는 반면, 나머지는 Azure 지역과 멀리 위치해 있습니다.  결과적으로, 인터넷에 대한 아웃 바운드 연결은 항상 제대로 작동하는 App Service Environment에 필요 합니다. 

서브넷에 배포된 App Service Environment 때문에, 네트워크 보안 그룹은 서브넷에 인바운드 트래픽을 제어할 때 사용할 수 있습니다.  App Service 환경에 대한 인바운드 트래픽을 제어하는 방법에 대한 자세한 내용은 다음 [문서][controllinginboundtraffic]를 참조하세요.

App Service 환경으로부터 아웃바운드 인터넷 연결을 허용하는 방법에 대한 세부 정보는 [Express 경로][ExpressRoute]로 작업하는 다음 문서를 참조하세요.  사이트와 사이트를 연결 및 강제 터널링을 사용할 때는 문서에 설명된 동일한 방법을 적용합니다.

## <a name="outbound-network-addresses"></a>아웃 바운드 네트워크 주소
App Service Environment가 아웃바운드를 호출하는 경우, IP 주소는 항상 아웃바운드 호출과 연관이 있습니다.  특정 IP 주소는 엔드포인트 호출이 이 가상 네트워크 토폴로지 내에 있는지 혹은 밖에 있는지에 따라서 달라집니다.

엔드포인트가 가상 네트워크 토폴로지 **외부** 에서 호출된 경우, 아웃바운드 주소(즉, 아웃바운드 NAT 주소)는 App Service Environment의 공용 VIP로 사용된 것입니다.  속성 블레이드의 App Service Environment에 대한 포털 사용자 인터페이스에서 이 주소를 찾을 수 있습니다.

![아웃 바운드 IP 주소][OutboundIPAddress]

App Service Environment에서 앱을 만든 다음 앱 주소에 대해 *nslookup* 을 수행하여 공용 VIP만 있는 ASE에 대해 이 주소를 확인할 수도 있습니다. 결과 IP 주소는 공용 VIP, App Service Environment의 아웃바운드 NAT 주소 둘 다 해당됩니다.

엔드포인트가 가상 네트워크 토폴리지 **내**에서 호출된 경우, 호출한 응용 프로그램의 아웃바운드 주소는 개별 계산 리소스의 내부 IP 주소입니다.  그러나 앱에 내부 IP 주소를 가상 네트워크의 지속적으로 매핑하지 않습니다.  앱은 다른 계산 리소스에 걸쳐 이동할 수 있고, App Service Environment의 사용 가능한 계산 리소스의 풀은 크기 조정 때문에 변경됩니다.

그러나 App Service Environment는 항상 서브넷 내에 위치하므로, 앱을 실행하는 계산 리소스의 내부 IP 주소가 서브넷의 CIDR 범위에 놓인다는 점을 보장 받을 수 있습니다.  결과적으로 세분화 된 ACL 또는 네트워크 보안 그룹은 가상 네트워크 내의 다른 엔드포인트의 액세스를 보호하는 데 사용되고, App Service Environment에 대한 서브넷 범위 조정은 액세스 권한을 부여해야 합니다.

다음 다이어그램에서는 이러한 개념을 자세히 보여줍니다.

![아웃 바운드 네트워크 주소][OutboundNetworkAddresses]

위의 다이어그램:

* App Service Environment의 공용 VIP가 192.23.1.2 이므로, "Internet" 엔드포인트에 대한 호출을 만들 때 사용하는 아웃 바운드 IP 주소입니다.
* App Service Environment에 대한 조정 서브넷을 포함하는 CIDR 범위가 10.0.1.0/26 입니다.  동일한 가상 네트워크 인프라 내에서 다른 엔드포인트는 주소 범위 내에서 발생 한 것처럼 앱에서 호출을 보게 됩니다.

## <a name="calls-between-app-service-environments"></a>App Service Environment 간의 호출
동일 가상 네트워크의 여러 App Service Environment에 배포하고 다른 App Service Environment에서 다른 App Service Environment로 아웃바운드 호출을 작성하는 경우 더 복잡한 시나리오가 발생할 수 있습니다.  이러한 종류의 크로스 App Service Environment 호출은 "Internet" 호출로도 처리됩니다.

다음 다이어그램은 두 번째 App Service Environment(예: 인터넷에서 액세스할 수 없는 내부 백 엔드 API 앱)에서 앱을 호출하는 하나의 App Service Environment(예: "프런트 도어" 웹앱)에서 앱으로 계층화된 아키텍처의 예제를 보여 줍니다. 

![App Service Environment 간의 호출][CallsBetweenAppServiceEnvironments] 

위의 예제에서 App Service Environment "ASE One"에는 192.23.1.2의 아웃 바운드 IP 주소가 있습니다.  App Service Environment에서 실행되는 앱이 동일한 가상 네트워크에 위치한 두 번째 App Service Environment("ASE Two")에서 실행 중인 앱에 아웃 바운드 호출을 하면 아웃 바운드 호출은 "인터넷" 호출로 처리됩니다.  결과적으로 두 번째 App Service Environment에 도착하는 네트워크 트래픽은 192.23.1.2에서 만들어진 것입니다.(즉, 첫 번째 App Service Environment의 서브넷 주소 범위가 아님)

다른 App Service Environment 간의 호출이 "Internet" 호출로 처리되더라도, 두 App Service Environment가 모두 동일한 Azure 지역에 있으면 네트워크 트래픽은 지역 Azure 네트워크에 그대로 유지되며 물리적으로 공용 인터넷을 통해 이동하지 않습니다.  결과적으로 첫 번째 App Service Environment(아웃바운드 IP 주소는 192.23.1.2임)에서의 인바운드 호출을 허용하도록 두번째 App Service Environment의 서브넷에서 네트워크 보안 그룹을 사용하여, App Service Environment 간의 보안 통신을 유지할 수 있습니다.

## <a name="additional-links-and-information"></a>추가 링크 및 정보
App Service 환경에서 사용된 인바운드 포트의 세부 정보 및 인바운드 트래픽 제어를 위한 네트워크 보안 그룹 사용은 [여기][controllinginboundtraffic]에서 사용 가능합니다.

App Service 환경에 아웃바운드 인터넷 액세스 권한을 부여하는 사용자 정의 경로 사용에 대한 자세한 세부 정보는 이 [문서][ExpressRoute]에서 사용 가능합니다. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

