---
title: 네트워킹 기능
description: Azure App Service의 네트워크 기능 및 보안 또는 기능을 위해 네트워크에 필요한 기능에 대해 알아봅니다.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d2b74af723e3ba8b1d71e9f481bf96d009540a52
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962097"
---
# <a name="app-service-networking-features"></a>App Service 네트워킹 기능

Azure App Service의 응용 프로그램은 여러 가지 방법으로 배포할 수 있습니다. 기본적으로 App Service 호스팅된 앱은 인터넷에 직접 액세스할 수 있으며 인터넷에 호스트 된 끝점에만 연결할 수 있습니다. 그러나 많은 고객 응용 프로그램에서 인바운드 및 아웃 바운드 네트워크 트래픽을 제어 해야 합니다. 이러한 요구를 충족 하기 위해 App Service에서 사용할 수 있는 몇 가지 기능이 있습니다. 문제는 지정 된 문제를 해결 하는 데 사용 해야 하는 기능을 파악 하는 것입니다. 이 문서는 고객이 몇 가지 예제 사용 사례에 따라 어떤 기능을 사용 해야 하는지 결정 하는 데 도움이 됩니다.

Azure App Service에 대 한 두 가지 기본 배포 유형이 있습니다. 무료, 공유, 기본, 표준, 프리미엄 및 Premiumv2 가격 책정 Sku에서 App Service 계획을 호스트 하는 다중 테 넌 트 공용 서비스가 있습니다. 그런 다음 Azure Virtual Network (VNet)에서 직접 격리 된 SKU App Service 계획을 호스트 하는 ASE (단일 테 넌 트 App Service Environment)가 있습니다. 다중 테 넌 트 서비스 또는 ASE에 있는 경우 사용 하는 기능이 달라 집니다. 

## <a name="multi-tenant-app-service-networking-features"></a>다중 테 넌 트 App Service 네트워킹 기능 

Azure App Service 분산 시스템입니다. 들어오는 HTTP/HTTPS 요청을 처리 하는 역할을 프런트 엔드 라고 합니다. 고객 작업을 호스트 하는 역할을 작업자 라고 합니다. App Service 배포의 모든 역할은 다중 테 넌 트 네트워크에 있습니다. 동일한 App Service 배율 단위에 여러 고객이 있으므로 App Service 네트워크를 네트워크에 직접 연결할 수 없습니다. 네트워크를 연결 하는 대신 응용 프로그램 통신의 다양 한 측면을 처리 하는 기능이 필요 합니다. 앱에 대 한 요청을 처리 하는 기능은 앱에서 호출할 때 문제를 해결 하는 데 사용할 수 없습니다. 마찬가지로 앱의 호출에 대 한 문제를 해결 하는 기능은 앱에 대 한 문제를 해결 하는 데 사용할 수 없습니다.  

| 인바운드 기능 | 아웃 바운드 기능 |
|---------------------|-------------------|
| 앱 할당 주소 | 하이브리드 연결 |
| 액세스 제한 | 게이트웨이 필요 VNet 통합 |
| 서비스 엔드포인트 | VNet 통합 |

별도로 언급 하지 않는 한 모든 기능을 함께 사용할 수 있습니다. 다양 한 문제를 해결 하는 기능을 혼합할 수 있습니다.

## <a name="use-case-and-features"></a>사용 사례 및 기능

지정 된 사용 사례에 대해 몇 가지 방법으로 문제를 해결할 수 있습니다.  사용할 수 있는 올바른 기능은 사용 사례 자체 외의 이유로 인해 발생할 수 있습니다. 다음 인바운드 사용 사례는 App Service 네트워킹 기능을 사용 하 여 앱으로 이동 하는 트래픽 제어와 관련 된 문제를 해결 하는 방법을 제안 합니다. 
 
| 인바운드 사용 사례 | 기능 |
|---------------------|-------------------|
| 앱에 대 한 IP 기반 SSL 요구 사항 지원 | 앱 할당 주소 |
| 공유 안 함, 앱에 대 한 전용 인바운드 주소 | 앱 할당 주소 |
| 잘 정의 된 주소 집합에서 앱에 대 한 액세스 제한 | 액세스 제한 |
| VNet의 리소스에서 내 앱에 대 한 액세스 제한 | 서비스 엔드포인트 </br> ILB ASE </br> 개인 끝점 (미리 보기) |
| 내 VNet의 개인 IP에서 내 앱 노출 | ILB ASE </br> 서비스 끝점을 사용 하는 Application Gateway에서 인바운드에 대 한 개인 IP </br> 서비스 끝점 (미리 보기) |
| WAF를 사용 하 여 내 앱 보호 | Application Gateway + ILB ASE </br> 서비스 엔드포인트가 있는 애플리케이션 게이트웨이 </br> 액세스 제한이 있는 Azure Front 도어 |
| 다른 지역의 내 앱에 트래픽 부하 분산 | 액세스 제한이 있는 Azure Front 도어 | 
| 동일한 지역에서 트래픽 부하 분산 | [서비스 엔드포인트가 있는 애플리케이션 게이트웨이][appgwserviceendpoints] | 

다음 아웃 바운드 사용 사례는 App Service 네트워킹 기능을 사용 하 여 앱에 대 한 아웃 바운드 액세스 요구를 해결 하는 방법을 제안 합니다. 

| 아웃 바운드 사용 사례 | 기능 |
|---------------------|-------------------|
| 동일한 지역에 있는 Azure Virtual Network의 리소스에 액세스 | VNet 통합 </br> ASE |
| 다른 지역의 Azure Virtual Network에 있는 리소스에 액세스 | 게이트웨이 필요 VNet 통합 </br> ASE 및 VNet 피어 링 |
| 서비스 끝점으로 보안이 유지 되는 리소스에 액세스 | VNet 통합 </br> ASE |
| Azure에 연결 되지 않은 개인 네트워크의 리소스에 액세스 | 하이브리드 연결 |
| Express 경로 회로에서 리소스 액세스 | VNet 통합 </br> ASE | 
| 웹 앱에서 아웃 바운드 트래픽 보호 | VNet 통합 및 네트워크 보안 그룹 </br> ASE | 
| 웹 앱에서 아웃 바운드 트래픽 라우팅 | VNet 통합 및 경로 테이블 </br> ASE | 


### <a name="default-networking-behavior"></a>기본 네트워킹 동작

Azure App Service 배율 단위는 각 배포에서 많은 고객을 지원 합니다. 무료 및 공유 SKU 요금제는 다중 테 넌 트 작업자에 대 한 고객 작업을 호스팅합니다. 기본 및 위의 계획은 하나의 ASP (App Service 계획)만을 전담 하는 고객 작업을 호스팅합니다. 표준 App Service 계획이 있는 경우 해당 계획의 모든 앱이 동일한 작업자에서 실행 됩니다. 작업자를 규모 확장 하는 경우 asp의 각 인스턴스에 대해 해당 ASP의 모든 앱이 새 작업자에 복제 됩니다. Premiumv2에 사용 되는 작업자는 다른 계획에 사용 되는 작업자와 다릅니다. 각 App Service 배포에는 해당 App Service 배포에서 앱에 대 한 모든 인바운드 트래픽에 사용 되는 IP 주소가 하나 있습니다. 그러나 아웃 바운드 호출에 사용 되는 주소는 4 ~ 11입니다. 이러한 주소는 해당 App Service 배포의 모든 앱에서 공유 됩니다. 아웃 바운드 주소는 다른 작업자 유형에 따라 다릅니다. 즉, 무료, 공유, 기본, 표준 및 프리미엄 Asp에서 사용 되는 주소는 Premiumv2 Asp의 아웃 바운드 호출에 사용 되는 주소와 다릅니다. 앱에 대 한 속성을 살펴보면 앱에서 사용 하는 인바운드 및 아웃 바운드 주소를 볼 수 있습니다. IP ACL을 사용 하 여 종속성을 잠가야 하는 경우 possibleOutboundAddresses를 사용 합니다. 

![앱 속성](media/networking-features/app-properties.png)

App Service에는 서비스를 관리 하는 데 사용 되는 여러 끝점이 있습니다.  이러한 주소는 개별 문서에 게시 되며 AppServiceManagement IP 서비스 태그에도 있습니다. AppServiceManagement 태그는 이러한 트래픽을 허용 해야 하는 ASE (App Service Environment) 에서만 사용 됩니다. App Service 인바운드 주소는 AppService IP 서비스 태그에서 추적 됩니다. App Service에서 사용 하는 아웃 바운드 주소를 포함 하는 IP 서비스 태그는 없습니다. 

![인바운드 및 아웃 바운드 다이어그램 App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>앱 할당 주소 

앱에 할당 된 주소 기능은 IP 기반 SSL 기능을 오프 하 고 앱으로 SSL을 설정 하 여 액세스할 수 있습니다. 이 기능은 IP 기반 SSL 호출에 사용할 수 있지만 앱에만 있는 주소를 제공 하는 데 사용할 수도 있습니다. 

![앱 할당 주소 다이어그램](media/networking-features/app-assigned-address.png)

앱 할당 주소를 사용 하는 경우 트래픽은 들어오는 모든 트래픽을 App Service 배율 단위로 처리 하는 동일한 프런트 엔드 역할을 계속 거칩니다. 그러나 앱에 할당 되는 주소는 앱 에서만 사용 됩니다. 이 기능에 대 한 사용 사례는 다음과 같습니다.

* 앱에 대 한 IP 기반 SSL 요구 사항 지원
* 다른 항목과 공유 되지 않는 앱의 전용 주소를 설정 합니다.

[Azure App Service에서 TLS/SSL 인증서 추가][appassignedaddress]의 자습서를 사용 하 여 앱의 주소를 설정 하는 방법을 알아볼 수 있습니다. 

### <a name="access-restrictions"></a>액세스 제한 

액세스 제한 기능을 사용 하면 원래 IP 주소에 따라 **인바운드** 요청을 필터링 할 수 있습니다. 필터링 작업은 앱이 실행 되는 작업자 역할에서 업스트림 인 프런트 엔드 역할에 대해 수행 됩니다. 프런트 엔드 역할은 작업자의 업스트림 이므로 액세스 제한 기능을 앱에 대 한 네트워크 수준 보호로 간주할 수 있습니다. 이 기능을 사용 하면 우선 순위에 따라 평가 되는 허용 및 거부 주소 블록 목록을 작성할 수 있습니다. Azure 네트워킹에 존재 하는 NSG (네트워크 보안 그룹) 기능과 유사 합니다.  ASE 또는 다중 테 넌 트 서비스에서이 기능을 사용할 수 있습니다. ILB ASE와 함께 사용 하는 경우 개인 주소 블록에서 액세스를 제한할 수 있습니다.

![액세스 제한](media/networking-features/access-restrictions.png)

액세스 제한 기능은 앱에 연결 하는 데 사용할 수 있는 IP 주소를 제한 하려는 경우에 유용 합니다. 이 기능에 대 한 사용 사례 중 하나는 다음과 같습니다.

* 잘 정의 된 주소 집합에서 앱에 대 한 액세스 제한 
* Azure Front 도어와 같은 부하 분산 서비스를 통해 들어오는 액세스를 제한 합니다. Azure Front 도어로의 인바운드 트래픽을 잠그려면 147.243.0.0/16 및 2a01:111:2050::/44의 트래픽을 허용 하는 규칙을 만듭니다. 

![전면 도어를 사용한 액세스 제한](media/networking-features/access-restrictions-afd.png)

Azure Virtual Network (VNet)의 리소스에서만 연결할 수 있도록 앱에 대 한 액세스를 잠그려면 사용자의 VNet에 있는 모든 원본에 대 한 정적 공용 주소가 필요 합니다. 리소스에 공용 주소가 없는 경우 서비스 끝점 기능을 대신 사용 해야 합니다. [액세스 제한 구성][iprestrictions]에 대 한 자습서에서이 기능을 사용 하도록 설정 하는 방법에 대해 알아봅니다.

### <a name="service-endpoints"></a>서비스 엔드포인트

서비스 끝점을 사용 하면 원본 주소를 선택 하는 서브넷 집합에서 가져와야 하는 앱에 대 한 **인바운드** 액세스를 잠글 수 있습니다. 이 기능은 IP 액세스 제한과 함께 작동 합니다. 서비스 끝점은 IP 액세스 제한과 동일한 사용자 환경에서 설정 됩니다. Vnet에 서브넷 뿐만 아니라 공용 주소를 포함 하는 액세스 규칙의 허용/거부 목록을 작성할 수 있습니다. 이 기능은 다음과 같은 시나리오를 지원 합니다.

![서비스 엔드포인트](media/networking-features/service-endpoints.png)

* 앱에 대 한 Application Gateway 설정 하 여 앱에 대 한 인바운드 트래픽 잠금
* VNet의 리소스로 앱에 대 한 액세스를 제한 합니다. 여기에는 Vm, Ase 또는 VNet 통합을 사용 하는 다른 앱도 포함 될 수 있습니다. 

![application gateway를 사용 하는 서비스 끝점](media/networking-features/service-endpoints-appgw.png)

[서비스 끝점 액세스 제한 구성][serviceendpoints] 의 자습서에서 앱을 사용 하 여 서비스 끝점을 구성 하는 방법에 대해 자세히 알아볼 수 있습니다.

### <a name="private-endpoint-preview"></a>개인 끝점 (미리 보기)

개인 끝점은 Azure 개인 링크를 통해 웹 앱에 안전 하 게 안전 하 게 연결 하는 네트워크 인터페이스입니다. 개인 끝점은 VNet의 개인 IP 주소를 사용 하 여 웹 앱을 VNet에 효과적으로 제공 합니다. 이 기능은 웹 앱에 대 한 **인바운드** 흐름에만 해당 됩니다.
[Azure 웹앱용 프라이빗 엔드포인트 사용(미리 보기)][privateendpoints]
 
### <a name="hybrid-connections"></a>하이브리드 연결

App Service 하이브리드 연결를 사용 하면 앱에서 지정 된 TCP 끝점에 대 한 **아웃 바운드** 호출을 수행할 수 있습니다. 끝점은 온-프레미스, VNet 또는 포트 443에서 Azure로의 아웃 바운드 트래픽을 허용 하는 모든 곳에 있을 수 있습니다. 이 기능을 사용 하려면 Windows Server 2012 이상 호스트에서 HCM (하이브리드 연결 관리자) 이라는 릴레이 에이전트를 설치 해야 합니다. HCM은 443 포트에서 Azure Relay에 도달할 수 있어야 합니다. HCM은 포털의 App Service 하이브리드 연결 UI에서 다운로드할 수 있습니다. 

![네트워크 흐름 하이브리드 연결](media/networking-features/hybrid-connections.png)

App Service 하이브리드 연결 기능은 Azure Relay 하이브리드 연결 기능을 기반으로 합니다. App Service는 앱에서 TCP 호스트 및 포트에 대 한 아웃 바운드 호출을 지 원하는 특수 한 형식의 기능을 사용 합니다. 이 호스트 및 포트는 HCM이 설치 된 호스트 에서만 확인 하면 됩니다. App Service 앱이 하이브리드 연결에 정의 된 호스트 및 포트에서 DNS 조회를 수행 하는 경우에는 하이브리드 연결을 통해 이동 하 여 하이브리드 연결 관리자 하는 것으로 트래픽이 자동으로 리디렉션됩니다. 하이브리드 연결에 대 한 자세한 내용은 App Service 설명서를 참조 하세요 [하이브리드 연결][hybridconn]

이 기능은 다음과 같은 경우에 일반적으로 사용 됩니다.

* VPN 또는 Express 경로를 사용 하 여 Azure에 연결 되지 않은 개인 네트워크의 리소스에 액세스
* 지원 데이터베이스를 이동할 필요 없이 온-프레미스 앱의 리프트 앤 시프트를 App Service으로 지원  
* 하이브리드 연결당 단일 호스트 및 포트에 대 한 액세스를 안전 하 게 제공 합니다. 대부분의 네트워킹 기능에서는 네트워크에 대 한 액세스를 개방 하 고 하이브리드 연결 하 여 연결할 수 있는 호스트와 포트가 하나만 있습니다.
* 다른 아웃 바운드 연결 방법에서 다루지 않는 시나리오를 소개 합니다.
* 앱이 온-프레미스 리소스를 쉽게 활용할 수 있는 App Service에서 개발을 수행 합니다. 

이 기능은 인바운드 방화벽을 사용 하지 않고 온-프레미스 리소스에 대 한 액세스를 허용 하므로 개발자는 널리 사용 됩니다. 다른 아웃 바운드 App Service 네트워킹 기능은 매우 Azure 가상 네트워킹 관련 기능입니다. 하이브리드 연결는 VNet을 통과 하는 종속성이 없으며 광범위 한 네트워킹 요구 사항에 사용할 수 있습니다. App Service 하이브리드 연결 기능은이 기능을 통해 수행 하는 작업을 고려 하거나 알지 못합니다. 즉, 데이터베이스, 웹 서비스 또는 메인프레임의 임의 TCP 소켓에 액세스 하는 데 사용할 수 있습니다. 이 기능은 기본적으로 TCP 패킷을 터널링 합니다. 

하이브리드 연결는 개발에 널리 사용 되지만 많은 프로덕션 응용 프로그램 에서도 사용 됩니다. 웹 서비스 또는 데이터베이스에 액세스 하는 것이 좋지만 많은 연결을 만드는 경우에는 적합 하지 않습니다. 

### <a name="gateway-required-vnet-integration"></a>게이트웨이 필요 VNet 통합 

VNet 통합 기능 App Service 필요한 게이트웨이를 사용 하면 앱에서 Azure Virtual Network **아웃 바운드** 요청을 수행할 수 있습니다. 이 기능은 앱이 실행 되는 호스트를 지점 및 사이트 간 VPN을 사용 하 여 VNet의 Virtual Network 게이트웨이에 연결 하는 방식으로 작동 합니다. 기능을 구성할 때 앱은 각 인스턴스에 할당 된 지점 및 사이트 간 주소 중 하나를 가져옵니다. 이 기능을 사용 하면 모든 지역에서 클래식 또는 리소스 관리자 Vnet의 리소스에 액세스할 수 있습니다. 

![게이트웨이 필요 VNet 통합](media/networking-features/gw-vnet-integration.png)

이 기능은 다른 Vnet에서 리소스에 액세스 하는 문제를 해결 하며 VNet을 통해 다른 Vnet 또는 온-프레미스에 연결 하는 데도 사용할 수 있습니다. Express 경로 연결 Vnet에서는 작동 하지 않지만 사이트 간 VPN 연결 네트워크를 사용 합니다. ASE가 VNet에 이미 있으므로 ASE (App Service Environment)에서이 기능을 사용 하는 것은 일반적으로 적합 하지 않습니다. 이 기능이 해결 하는 사용 사례는 다음과 같습니다.

* Azure 가상 네트워크에서 개인 Ip의 리소스 액세스 
* 사이트 간 VPN이 있는 경우 온-프레미스 리소스 액세스 
* 피어 링 Vnet의 리소스 액세스 

이 기능을 사용 하도록 설정 하면 앱은 대상 VNet이 구성 된 DNS 서버를 사용 합니다. 이 기능에 대 한 자세한 내용은 [App Service VNet 통합][vnetintegrationp2s]에 대 한 설명서에서 확인할 수 있습니다. 

### <a name="vnet-integration"></a>VNet 통합

게이트웨이 필수 VNet 통합 기능은 매우 유용 하지만 Express 경로에서 리소스에 대 한 액세스를 아직 해결 하지 않습니다. Express 경로 연결을 통해 연결 해야 하는 경우 앱이 서비스 끝점 보안 서비스를 호출할 수 있어야 합니다. 이러한 추가 요구 사항을 모두 해결 하기 위해 다른 VNet 통합 기능이 추가 되었습니다. 새 VNet 통합 기능을 사용 하면 동일한 지역의 리소스 관리자 VNet에 있는 서브넷에 앱 백 엔드를 추가할 수 있습니다. 이 기능은 이미 VNet에 있는 App Service Environment에서 사용할 수 없습니다. 이 기능을 통해 다음을 수행할 수 있습니다.

* 동일한 지역에서 리소스 관리자 Vnet의 리소스에 액세스
* 서비스 끝점으로 보안이 유지 되는 리소스에 액세스 
* Express 경로 또는 VPN 연결을 통해 액세스할 수 있는 리소스에 액세스
* 모든 아웃 바운드 트래픽 보안 
* 모든 아웃 바운드 트래픽을 강제로 터널링 합니다. 

![VNet 통합](media/networking-features/vnet-integration.png)

이 기능에 대해 자세히 알아보려면 [App Service VNet 통합][vnetintegration]에 대 한 문서를 참조 하세요.

## <a name="app-service-environment"></a>App Service 환경 

ASE (App Service Environment)는 VNet에서 실행 되는 Azure App Service의 단일 테 넌 트 배포입니다. ASE를 사용 하면 다음과 같은 사용 사례를 사용할 수 있습니다.

* VNet의 리소스에 액세스
* Express 경로에서 리소스 액세스
* VNet에서 개인 주소를 사용 하 여 앱 노출 
* 서비스 끝점 간 리소스 액세스 

Ase를 사용 하면 ASE가 VNet에 이미 있으므로 VNet 통합 또는 서비스 끝점과 같은 기능을 사용할 필요가 없습니다. 서비스 끝점을 통해 SQL 또는 저장소와 같은 리소스에 액세스 하려면 ASE 서브넷에서 서비스 끝점을 사용 하도록 설정 합니다. VNet의 리소스에 액세스 하려는 경우 추가 구성이 필요 하지 않습니다.  Express 경로에서 리소스에 액세스 하려는 경우 이미 VNet에 있으며 ASE 또는 그 안에 있는 앱에서 어떤 것도 구성할 필요가 없습니다. 

ILB ASE의 앱은 개인 IP 주소에 노출 될 수 있으므로, 인터넷에 원하는 앱만 노출 하 고 나머지는 안전 하 게 유지 하도록 WAF 장치를 쉽게 추가할 수 있습니다. 다중 계층 응용 프로그램을 쉽게 개발할 수 있습니다. 

ASE의 다중 테 넌 트 서비스에서 아직 사용할 수 없는 몇 가지 작업이 있습니다. 여기에는 다음과 같은 항목이 포함 됩니다.

* 개인 IP 주소에 앱 노출
* 응용 프로그램의 일부가 아닌 네트워크 제어를 사용 하 여 모든 아웃 바운드 트래픽 보호 
* 단일 테 넌 트 서비스에서 앱 호스트 
* 다중 테 넌 트 서비스에서 가능한 것 보다 많은 인스턴스로 확장 
* 개인 CA 보안 끝점을 사용 하 여 앱에서 사용할 개인 CA 클라이언트 인증서를 로드 합니다. 
* 앱 수준에서 사용 하지 않도록 설정 하는 기능 없이 시스템에서 호스트 되는 모든 앱에서 TLS 1.1을 강제 적용 합니다. 
* 모든 고객과 공유 되지 않는 ASE의 모든 앱에 대 한 전용 아웃 바운드 주소를 제공 합니다. 

![VNet의 ASE](media/networking-features/app-service-environment.png)

ASE는 격리 되 고 전용 응용 프로그램 호스팅에 대 한 최상의 스토리를 제공 하지만 몇 가지 관리 과제가 제공 됩니다. 작업 ASE를 사용 하기 전에 고려해 야 할 몇 가지 사항은 다음과 같습니다.
 
 * ASE는 VNet 내에서 실행 되지만 VNet 외부에 종속성이 있습니다. 이러한 종속성은 허용 되어야 합니다. [App Service Environment에 대 한 네트워킹 고려 사항][networkinfo] 자세히 읽기
 * ASE는 다중 테 넌 트 서비스 처럼 즉시 확장 되지 않습니다. 대응적 크기 조정 보다는 크기 조정이 필요 합니다. 
 * ASE에는 더 높은 전면 비용이 연결 되어 있습니다. ASE를 최대한 활용 하기 위해 작은 노력에 사용 하는 것 보다 많은 워크 로드를 하나의 ASE에 배치 하는 계획을 세워야 합니다.
 * ASE의 앱은 다른 ASE가 아닌 ASE의 일부 앱에 대 한 액세스를 제한할 수 없습니다.
 * ASE는 서브넷에 있고 모든 네트워킹 규칙은 해당 ASE로 들어오고 나가는 모든 트래픽에 적용 됩니다. 하나의 앱에 대해서만 인바운드 트래픽 규칙을 할당 하려면 액세스 제한을 사용 합니다. 

## <a name="combining-features"></a>기능 결합 

다중 테 넌 트 서비스에 대해 언급 된 기능을 함께 사용 하 여 보다 정교한 사용 사례를 해결할 수 있습니다. 이러한 두 가지 일반적인 사용 사례는 여기에 설명 되어 있지만 단지 예제입니다. 다양 한 기능을 이해 하면 거의 모든 시스템 아키텍처 요구 사항을 해결할 수 있습니다.

### <a name="inject-app-into-a-vnet"></a>VNet에 앱 삽입

일반적인 요청은 VNet에 앱을 배치 하는 방법에 대 한 것입니다. 앱을 VNet에 배치 하는 것은 앱에 대 한 인바운드 및 아웃 바운드 끝점이 VNet 내에 있다는 것을 의미 합니다. ASE는이 문제를 해결 하는 최상의 솔루션을 제공 하지만 기능을 결합 하 여 다중 테 넌 트 서비스에서 필요한 대부분의 기능을 얻을 수 있습니다. 예를 들어 다음을 수행 하 여 전용 인바운드 및 아웃 바운드 주소로 인트라넷 전용 응용 프로그램을 호스트할 수 있습니다.

* 개인 인바운드 및 아웃 바운드 주소를 사용 하 여 Application Gateway 만들기
* 서비스 끝점을 사용 하 여 앱에 대 한 인바운드 트래픽 보안 
* 새 VNet 통합을 사용 하 여 앱의 백 엔드가 VNet에 있습니다. 

이 배포 스타일은 인터넷에 대 한 아웃 바운드 트래픽의 전용 주소를 제공 하지 않거나 앱에서 모든 아웃 바운드 트래픽을 잠글 수 있는 기능을 제공 하지 않습니다.  이 배포 스타일은 ASE를 사용 하 여 다른 방법으로 가져올 수 있는 많은 작업을 제공 합니다. 

### <a name="create-multi-tier-applications"></a>다중 계층 응용 프로그램 만들기

다중 계층 응용 프로그램은 프런트 엔드 계층 에서만 API 백 엔드 앱에 액세스할 수 있는 응용 프로그램입니다. 다중 계층 응용 프로그램을 만들려면 다음을 수행할 수 있습니다.

* VNet 통합을 사용 하 여 VNet의 서브넷과 프런트 엔드 웹 앱의 백 엔드 연결
* 서비스 끝점을 사용 하 여 API 앱에 대 한 인바운드 트래픽을 보안 하 고 프런트 엔드 웹 앱에서 사용 하는 서브넷 에서만 가져오도록 합니다.

![다중 계층 앱](media/networking-features/multi-tier-app.png)

다른 프런트 엔드 앱에서 VNet 통합을 사용 하 여 여러 프런트 엔드 앱에서 동일한 API 앱을 사용 하 고 API 앱에서 해당 서브넷을 사용 하 여 서비스 끝점을 사용할 수 있습니다.  

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md