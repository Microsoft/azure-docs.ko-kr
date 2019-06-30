---
title: 배포 기능-Azure App Service 네트워킹 | Microsoft Docs
description: 다양 한 App Service 네트워킹 기능을 사용 하는 방법
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 362f0b32ea99d0b888ea9f1efa0cd4ea74eb2fa1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338487"
---
# <a name="app-service-networking-features"></a>App Service 네트워킹 기능

여러 가지 방법으로 Azure App Service에서 응용 프로그램을 배포할 수 있습니다. 기본적으로 App Service 호스팅된 앱은 직접 인터넷에 액세스할 수 및 수만 호스트 하는 인터넷 끝점에 연결할 합니다. 그러나 많은 고객 응용 프로그램 인바운드 및 아웃 바운드 네트워크 트래픽을 제어 해야 합니다. 이러한 요구 사항을 충족 하기 위해 App Service에서 사용할 수 있는 몇 가지 기능이 있습니다. 과제 주어진된 문제를 해결 하기 위해 어떤 기능을 사용할지 알아야 합니다. 이 문서는 고객은 몇 가지 예제 사용 사례에 따라 어떤 기능을 사용할지 결정 하는 데 사용 됩니다.

Azure App Service에 대 한 두 가지 주 배포 유형이 있습니다. 무료, 공유, Basic, Standard, Premium 및 Premiumv2의 App Service 계획을 호스트 하는 다중 테 넌 트 공용 서비스에는 가격 책정 Sku입니다. 그런 다음 단일 테 넌 트 앱 서비스 ase (environment)를 Azure Virtual Network (VNet)에서 직접 격리 된 SKU App Service 계획을 호스트 하는 경우 다중 테 넌 트 서비스에서 또는 ASE에서 사용 하는 기능에 달라 집니다. 

## <a name="multi-tenant-app-service-networking-features"></a>다중 테 넌 트 App Service 네트워킹 기능 

Azure App Service는 분산된 시스템입니다. 들어오는 HTTP/HTTPS 요청을 처리 하는 역할에는 프런트 엔드 라고 합니다. 고객 워크 로드를 호스트 하는 역할을 작업자 라고 합니다. App Service 배포에서 역할의 모든 다중 테 넌 트 네트워크에 존재합니다. 동일한 App Service 크기 조정 단위에 여러 고객과 이기 때문에 네트워크에 직접 앱 서비스 네트워크를 연결할 수 없습니다. 네트워크에 연결 하는 대신 응용 프로그램 통신의 다양 한 측면을 처리 하는 기능이 필요 합니다. 앱에서 호출할 때 문제를 해결 하기 위해 앱에 대 한 요청을 처리 하는 기능을 사용할 수 없습니다. 마찬가지로, 앱에 문제를 해결 하기 위해 앱에서 호출에 대 한 문제를 해결 하는 기능을 사용할 수 없습니다.  

| 인바운드 기능 | 아웃 바운드 기능 |
|---------------------|-------------------|
| 주소를 할당 된 앱 | 하이브리드 연결 |
| 액세스 제한 | 게이트웨이 필요 VNet 통합 |
| 서비스 엔드포인트 | VNet 통합 (미리 보기) |

별도로 언급 하지 않는 한 기능을 모두 함께 사용할 수 있는 합니다. 다양 한 문제를 해결 하는 기능을 혼합할 수 있습니다.

## <a name="use-case-and-features"></a>사용 사례 및 기능

지정 된 사용 사례에 대 한 문제를 해결 하는 몇 가지 방법이 있을 수 있습니다.  사용 하는 올바른 기능 자체를 사용 하 여 사례만 이외의 이유로 경우가 있습니다. 다음 인바운드 사용 사례 앱 트래픽을 제어 하는 방법의 문제를 해결 하기 위해 App Service 네트워킹 기능을 사용 하는 방법을 제안 합니다. 
 
| 인바운드 사용 사례 | 기능 |
|---------------------|-------------------|
| 앱에 대 한 IP 기반 SSL 요구를 지원 합니다. | 주소를 할당 된 앱 |
| 공유 하지 않음, 앱에 대 한 인바운드 전용된 주소 | 주소를 할당 된 앱 |
| 잘 정의 된 주소 집합에서 앱에 액세스 제한 | 액세스 제한 |
| 내 앱에서 VNet 내에서 개인 Ip 노출 합니다. | ILB ASE </br> 서비스 끝점을 사용 하 여 응용 프로그램 게이트웨이 |
| VNet의 리소스에서 내 앱에 액세스 제한 | 서비스 엔드포인트 </br> ILB ASE |
| 내 앱 내 VNet에서 개인 ip를 노출 합니다. | ILB ASE </br> 서비스 끝점을 사용 하 여 Application Gateway의 개인 IP에 대 한 인바운드 |
| WAF 사용 하 여 앱 보호 | Application Gateway + ILB ASE </br> 서비스 끝점을 사용 하 여 응용 프로그램 게이트웨이 </br> 액세스가 제한 된 azure 프런트 도어 |
| 서로 다른 지역에서 내 앱에 트래픽 부하 분산 | 액세스가 제한 된 azure 프런트 도어 | 
| 동일한 지역에서 트래픽 부하 분산 | 서비스 끝점을 사용 하 여 응용 프로그램 게이트웨이 | 

다음 아웃 바운드 사용 사례에는 앱에 대 한 아웃 바운드 액세스 요구 사항을 해결 하도록 App Service 네트워킹 기능을 사용 하는 방법을 제안 합니다. 

| 아웃 바운드 사용 사례 | 기능 |
|---------------------|-------------------|
| 동일한 지역의 Azure Virtual Network의 리소스에 액세스 | VNet 통합 </br> ASE |
| 다른 지역의 Azure Virtual Network의 리소스에 액세스 | 게이트웨이 필요 VNet 통합 </br> ASE 및 VNet 피어 링 |
| 서비스 끝점을 사용 하 여 보안 리소스에 액세스 | VNet 통합 </br> ASE |
| Azure에 연결 되지 않은 개인 네트워크의 리소스에 액세스 | 하이브리드 연결 |
| ExpressRoute 회로에서 리소스에 액세스 | VNet 통합 (지금은 RFC 1918 주소 제한 됨) </br> ASE | 


### <a name="default-networking-behavior"></a>기본 네트워킹 동작

Azure App Service 크기 조정 단위는 각 배포에 많은 고객을 지원합니다. 무료 및 공유 SKU 요금제는 다중 테 넌 트 작업자에 고객 워크 로드를 호스트 합니다. 기본, 이상 계획 고객 워크 로드를 호스트 하나의 App Service 계획 (ASP)에 사용 됩니다. 표준 App Service 계획을 설치한 경우 동일한 작업자에서 실행 됩니다 해당 계획에 앱을 모두 합니다. 작업자를 확장 하는 경우 다음 해당 ASP에서 앱을 모두에 복제 됩니다 ASP의 각 인스턴스에 대 한 새 작업자입니다. Premiumv2에 사용 되는 작업자는 다른 계획에 사용 되는 작업자와 다릅니다. 각 App Service 배포에는 모든 인바운드 트래픽을 앱 App Service 배포에 사용 되는 하나의 IP 주소가 있습니다. 하지만 가지 아무 곳 이나 4에서 아웃 바운드 호출에 사용 되는 11 주소입니다. 이러한 주소는 해당 App Service 배포에서 모든 앱으로 공유 됩니다. 아웃 바운드 주소는 다른 작업자 형식에 따라 달라 집니다. 즉, 무료, 공유, 기본, 표준 및 프리미엄 Asp에서 사용 하는 주소는 Premiumv2 Asp에서 아웃 바운드 호출에 사용 되는 주소와 다릅니다. 앱에 대 한 속성에서 표시 하는 경우 앱에서 사용 되는 인바운드 및 아웃 바운드 주소를 볼 수 있습니다. IP ACL을 사용 하 여 종속성을 잠그는 해야 할 경우에 possibleOutboundAddresses 사용 합니다. 

![앱 속성](media/networking-features/app-properties.png)

App Service에 서비스를 관리 하는 데 사용 되는 끝점 수가 있습니다.  이러한 주소 별도 문서에 게시 하 고 AppServiceManagement IP 서비스 태그에도 포함 되어 있습니다. AppServiceManagement 태그를 사용 하 여 서비스 환경 (ASE (App) 트래픽을 허용 해야 하는 사용 됩니다. App Service 인바운드 주소 AppService IP 서비스 태그에서 추적 됩니다. App Service에서 사용 하는 아웃 바운드 주소를 포함 하는 IP 서비스 태그 없는 경우 

![App Service의 인바운드 및 아웃 바운드 다이어그램](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>주소를 할당 된 앱 

앱 할당 된 주소 기능은 IP 기반 SSL 기능을 offshoot 고 앱을 사용 하 여 SSL을 설정 하 여 액세스 됩니다. IP 기반 SSL 호출에 대 한이 기능을 사용할 수 있지만 앱에만 주소를 지정 하는 데 사용 수 수도 수 있습니다. 

![주소 다이어그램 할당 된 앱](media/networking-features/app-assigned-address.png)

주소를 할당 하는 앱을 사용 하면 App Service의 배율 단위에 모든 들어오는 트래픽을 처리 하는 동일한 프런트 엔드 역할을 통해 트래픽을 계속 이동 합니다. 그러나 앱에 할당 된 주소를 앱에 사용 됩니다. 이 기능에 대 한 사용 사례는 다음과 같습니다.

* 앱에 대 한 IP 기반 SSL 요구를 지원 합니다.
* 다른 요소와 공유 되지 않는 앱에 대 한 전용된 주소 설정

이 자습서를 사용 하 여 앱에 주소를 설정 하는 방법을 알아볼 수 있습니다 [구성 IP 기반 SSL][appassignedaddress]합니다. 

### <a name="access-restrictions"></a>액세스 제한 

필터링 할 액세스 제한 기능 수 **인바운드** 원래 IP 주소를 기반으로 요청 합니다. 필터링 작업에는 앱의 실행 되는 작업자 굴리기에서 업스트림는 프런트 엔드 역할에서 수행이 됩니다. 프런트 엔드 역할에서 작업자에서 업스트림 되므로 액세스 제한 기능을 앱에 대 한 네트워크 수준 보호가 간주할 수 있습니다. 기능을 사용 하면 목록을 빌드할 수 있습니다 허용 및 우선 순위에 따라 계산 되는 주소 블록을 거부 합니다. Azure 네트워킹에 존재 하는 네트워크 보안 그룹 (NSG) 기능과 비슷합니다.  ASE 또는 다중 테 넌 트 서비스에서이 기능을 사용할 수 있습니다. ILB ASE를 사용 하는 경우에 개인 주소 블록에서 액세스를 제한할 수 있습니다.

![액세스 제한](media/networking-features/access-restrictions.png)

액세스 제한 기능을 앱에 연결할 수 있는 IP 주소를 제한 하려는 시나리오에 도움이 됩니다. 사용 하 여가이 기능에 대 한 사례 같습니다.

* 잘 정의 된 주소 집합에서 앱에 액세스 제한 
* 부하 분산 서비스, Azure 프런트 도어 같은 유입에 대 한 액세스를 제한 합니다. 트래픽을 Azure 프런트 도어를 잠그려면 원한다 면 147.243.0.0/16 2a01:111:2050에서 트래픽을 허용 하도록 규칙을 만듭니다:: / 44입니다. 

![첫 번째 관문을 사용 하 여 액세스 제한](media/networking-features/access-restrictions-afd.png)

Azure Virtual Network (VNet)의 리소스에서만 도달할 수 있도록 앱에 대 한 액세스를 잠그는 원한다 면 VNet에서 무엇이 원본 정적 공용 주소를 해야 합니다. 리소스에 공용가 없는 경우 대신 서비스 끝점 기능을 사용 해야 합니다. 이 자습서를 사용 하 여이 기능을 사용 하는 방법을 알아봅니다 [액세스 제한 구성][iprestrictions]합니다.

### <a name="service-endpoints"></a>서비스 엔드포인트

서비스 끝점을 사용 하면를 잠그는 **인바운드** 는 원본 주소에서 선택한 서브넷의 집합 이어야 합니다. 앱에 액세스 합니다. 이 기능은 IP 액세스 제한 사항과 함께에서 작동합니다. 서비스 끝점 IP 액세스 제한 사항으로 동일한 사용자 환경에 설정 됩니다. Vnet에 서브넷 뿐 아니라 공용 주소를 포함 하는 액세스 규칙의 허용/거부 목록을 작성할 수 있습니다. 이 기능은 같은 시나리오를 지원 합니다.

![서비스 엔드포인트](media/networking-features/service-endpoints.png)

* 앱으로의 인바운드 트래픽을 잠그는 데 앱을 사용 하 여 응용 프로그램 게이트웨이 설정
* VNet의 리소스에 앱에 대 한 액세스를 제한 합니다. Vm, Ase 또는 VNet 통합을 사용 하는 다른 앱 포함할 수 있습니다. 

![응용 프로그램 게이트웨이 사용 하 여 서비스 끝점](media/networking-features/service-endpoints-appgw.png)

에 자습서에서 앱을 사용 하 여 서비스 끝점을 구성 하는 방법에 대 한 자세히 알아볼 수 있습니다 [서비스 끝점 액세스 제한 구성][serviceendpoints]
 
### <a name="hybrid-connections"></a>하이브리드 연결

App Service 하이브리드 연결 하도록 앱을 사용 하도록 설정 **아웃 바운드** 지정된 TCP 끝점을 호출 합니다. 어디서 나 트래픽을 허용 하는 아웃 바운드 포트 443에서 Azure로 또는 끝점에는 온-프레미스 및 VNet의 수 있습니다. Windows Server 2012 또는 최신 호스트에서 하이브리드 연결 관리자 (HCM)를 호출 하는 릴레이 에이전트를 설치를 해야 하는 기능입니다. HCM이 Azure Relay 포트 443에서 연결할 수 있어야 합니다. HCM은 포털에서 App Service 하이브리드 연결 UI에서 다운로드할 수 있습니다. 

![하이브리드 연결 네트워크 흐름](media/networking-features/hybrid-connections.png)

App Service 하이브리드 연결 기능은 Azure 릴레이 하이브리드 연결 기능을 기반으로 합니다. App Service는 특수 한 형태의 TCP 호스트 및 포트에 앱에서 만드는 아웃 바운드 호출을 지 원하는 기능을 사용 합니다. 이 호스트 및 포트 하기만 HCM이 설치 되어 있는 호스트에서 해결 됩니다. App Service에서 앱을 호스트 및 하이브리드 연결에 정의 된 포트에서 DNS 조회를 수행 하는 경우는 트래픽이 자동으로 리디렉션됩니다 하이브리드 연결을 통해 및 out 하이브리드 연결 관리자를 이동 합니다. 하이브리드 연결에 대 한 자세한 내용은 설명서를 참조에서 [App Service 하이브리드 연결][hybridconn]

이 기능을 일반적으로 사용 됩니다.

* VPN 또는 ExpressRoute를 사용 하 여 Azure에 연결 되지 않은 개인 네트워크의 리소스에 액세스
* 또한 지원 데이터베이스를 이동 하지 않고도 리프트 앤 시프트 온-프레미스 앱을 App Service 지원  
* 단일 호스트 및 하이브리드 연결당 포트 액세스를 안전 하 게 제공 합니다. 대부분의 네트워킹 기능을 네트워크에 액세스할 수 있도록 있으며 하이브리드 연결을 사용 하 여 단일 호스트 및 포트에 도달할 수 있습니다.
* 시나리오를 다루지 않는 다른 아웃 바운드 연결 메서드
* App Service는 앱 수 쉽게 활용 하 여 온-프레미스 리소스에서 개발을 수행 합니다. 

기능에는 인바운드 방화벽 빈틈 없이 온-프레미스 리소스에 액세스할 수 있도록, 이므로 개발자와 인기 있는 합니다. 아웃 바운드 App Service 네트워킹 기능을 매우 Azure 가상 네트워킹 관련 됩니다. 하이브리드 연결에는 VNet을 통한 이동에 대 한 종속성이 없는 및 다양 한 네트워킹 요구 사항에 사용할 수 있습니다. App Service 하이브리드 연결 기능을 처리 하거나 그 위에 수행 하는 것을 알고 하지 않는 하는 것이 반드시 합니다. 데이터베이스, 웹 서비스 또는 메인프레임에서 임의의 TCP 소켓에 액세스를 사용할 수 있습니다. 기능에는 기본적으로 TCP 패킷을 터널링 합니다. 

하이브리드 연결 개발을 위한 인기 있는 상태인도 다양 한 프로덕션 응용 프로그램 에서도 사용 됩니다. 웹 서비스 또는 데이터베이스에 액세스 하는 데 적합 하지만 많은 연결을 만들기를 포함 하는 상황에 적합 하지 않습니다. 

### <a name="gateway-required-vnet-integration"></a>게이트웨이 필요 VNet 통합 

게이트웨이에 필요한 앱 서비스 VNet 통합 기능을 사용 하도록 앱 **아웃 바운드** Azure Virtual Network에 요청 합니다. 앱에서 실행 되는 지점-사이트 간 VPN 사용 하 여 VNet에 가상 네트워크 게이트웨이 호스트에 연결 하 여이 기능의 작동 합니다. 기능을 구성 하면 앱의 각 인스턴스에 할당 된 지점-사이트 간 주소 하나를 가져옵니다. 이 기능을 사용 하면 클래식 또는 Resource Manager Vnet에서 모든 지역에서 리소스에 액세스할 수 있습니다. 

![게이트웨이 필요 VNet 통합](media/networking-features/gw-vnet-integration.png)

이 기능은 다른 Vnet의 리소스에 액세스 하는 문제가 해결 되었습니다 및 다른 Vnet 또는 온-프레미스 VNet을 통한 연결에 사용할 수 있습니다. ExpressRoute 연결을 사용 하 여 작동 하지 않습니다 하지만 Vnet 사이트 간 VPN 사용 하 여 네트워크를 연결 합니다. ASE가 VNet에 이미 있으므로 앱 서비스 환경 (ASE)에서 앱에서이 기능을 사용 하려면 일반적으로 적합 하지 않습니다. 이 기능을 해결 하는 용도 다음과 같습니다.

* Azure 가상 네트워크의 개인 Ip에 대 한 리소스에 액세스 
* 리소스에 액세스 온-프레미스 사이트 간 VPN을 경우 
* 피어 링 된 Vnet의 리소스에 액세스 

이 기능을 사용 하는 경우 앱 대상 VNet에 구성 된 DNS 서버를 사용 합니다. 자세한 내용은 대 한 설명서에서는이 기능 [앱 서비스 VNet 통합][vnetintegrationp2s]합니다. 

### <a name="vnet-integration"></a>VNet 통합

게이트웨이 VNet 통합 기능은 매우 유용 하지만 여전히에서 ExpressRoute 리소스에 액세스 하는 해결 되지 않습니다 필요 합니다. ExpressRoute 연결을 통해 연결할 필요를 기반으로 서비스를 보호 하는 서비스 끝점에 대 한 호출을 확인 하려면 앱에 대 한 요구가 있습니다. 이러한 추가 요구 사항을 모두를 해결 하기 위해 다른 VNet 통합 기능 추가 되었습니다. 새 VNet 통합 기능을 사용 하면 동일한 지역에 리소스 관리자 VNet의 서브넷에서 앱의 백 엔드를 배치할 수 있습니다. 이 기능은 VNet에 이미 있는 앱 서비스 환경에서 사용할 수 없습니다. 이 기능을 사용하여 다음과 같이 할 수 있습니다.

* 동일한 지역에 리소스 관리자 Vnet의 리소스에 액세스
* 서비스 끝점을 사용 하 여 보안이 유지 되는 리소스에 액세스 
* ExpressRoute 또는 VPN 연결을 통해 액세스할 수 있는 리소스에 액세스

![VNet 통합](media/networking-features/vnet-integration.png)

이 기능은 미리 보기 상태에서 이며 프로덕션 워크 로드에 쓰일 수 없습니다. 이 기능에 대 한 자세한 내용은의 문서를 읽을 [앱 서비스 VNet 통합][vnetintegration]합니다.

## <a name="app-service-environment"></a>App Service 환경 

(ASE (App Service 환경)는 VNet에서 실행 되는 Azure App Service의 단일 테 넌 트 배포. ASE와 같은 사용 사례를 수 있습니다.

* VNet의 리소스에 액세스
* ExpressRoute에서 리소스에 액세스
* VNet에서 개인 주소를 사용 하 여 앱을 노출 합니다. 
* 서비스 끝점을 통해 리소스에 액세스 

ASE를 사용 하 여 ASE가 VNet에 이미 있으므로 VNet 통합 또는 서비스 끝점 등의 기능을 사용할 필요가 없습니다. 서비스 끝점을 통해 SQL 또는 Storage와 같은 리소스에 액세스 하려는 경우 ASE 서브넷에서 서비스 끝점을 사용 하도록 설정 합니다. VNet의 리소스에 액세스 하려는 경우 추가 구성이 필요 하지 않습니다.  ExpressRoute 리소스에 액세스 하려는 경우에 VNet에 포함 되어 및 ASE 또는 앱 내에서 특별히 구성할 필요가 없습니다. 

ILB ASE의 앱에 개인 IP 주소에 노출 될 수 있습니다, 되므로 앱에만 인터넷 하 고 나머지를 안전 하 게 유지를 노출 하려면 WAF 장치를 쉽게 추가할 수 있습니다. 이 다중 계층 응용 프로그램의 간편한 개발 적합합니다. 

몇 가지 아직 다중 테 넌 트 서비스에서 가능 하지 않은 ASE에 있는 합니다. 이러한 등이 포함 됩니다.

* 개인 IP 주소에서 응용 프로그램 노출
* 앱의 일부분이 아닌 네트워크 제어를 사용 하 여 모든 아웃 바운드 트래픽 보안 
* 단일 테 넌 트 서비스에서 앱을 호스트합니다 
* 다중 테 넌 트 서비스에서 가능한 것 보다 더 많은 인스턴스를 여러 확장 
* 끝점을 보호 하는 개인 CA를 사용 하 여 앱에 사용 하기 위해 개인 CA 클라이언트 인증서를 로드 
* 모든 기능이 앱 수준에서 사용 하지 않도록 설정 하지 않고 시스템에 호스팅된 앱에서 TLS 1.1을 강제 적용 
* 모든 고객과 공유 되지 않은 ASE에서 앱을 모두에 대 한 전용된 아웃 바운드 주소를 제공 합니다. 

![VNet에 ASE](media/networking-features/app-service-environment.png)

ASE 격리 및 전용 앱 호스팅에 대 한 모범 사례를 제공 하지만 몇 가지 관리 과제를 수반지 않습니다. Operational ASE를 사용 하기 전에 고려해 야 할 몇 가지 사항은 다음과 같습니다.
 
 * ASE는 VNet 내에서 실행 되지만 VNet 외부 종속성이 있는지 않습니다. 이러한 종속성을 허용 되어야 합니다. 자세한 내용은 [App Service Environment에 대 한 네트워킹 고려 사항][networkinfo]
 * 다중 테 넌 트 서비스와 마찬가지로 ASE 즉시 확장 되지 않습니다. 사후 확장 하는 것이 아니라 확장 요구를 예상 해야 합니다. 
 * ASE에 더 높은 인덱싱한 비용도 부과 합니다. ASE 최대한을 얻으려면 있습니다 해야 다양 한 워크 로드를 하나의 ASE 넣는 계획 보다 작은 노력 하는 데 사용
 * ASE의 앱은 ASE 중 등의 일부 앱에 대 한 액세스를 제한할 수 없습니다.
 * ASE 서브넷 이며 모든 네트워킹 규칙이 해당 ASE에서 모든 트래픽에 적용 됩니다. 하나의 앱에 대 한 인바운드 트래픽 규칙을 할당 하려는 경우 액세스 제한을 사용 합니다. 

## <a name="combining-features"></a>기능을 결합 

보다 정교한 사용 사례를 해결 하기 위해 다중 테 넌 트 서비스에서 설명한 기능을 함께 사용할 수 있습니다. 보다 일반적인 사용 사례 중 두 가지 여기에 설명 되어 있지만 예제 일 뿐입니다. 다양 한 기능 수행할 작업을 이해 하 여 거의 모든 시스템 아키텍처 요구 사항을 해결할 수 있습니다.

### <a name="inject-app-into-a-vnet"></a>VNet에 앱을 삽입

일반적인 요청 VNet에서 앱을 배치 하는 방법을 켜져 있습니다. VNet에 앱을 넣는 VNet 내에서 앱에 대 한 인바운드 및 아웃 바운드 끝점은 의미 합니다. ASE는이 문제를 해결 하는 최고의 솔루션을 제공 하지만 대부분의 필요한 것으로 다중 테 넌 트 서비스의 기능을 결합 하 여 가져올 수 있습니다. 예를 들어 개인 인바운드 및 아웃 바운드 주소를 사용 하 여 인트라넷 전용 응용 프로그램을 호스트할 수 있습니다.

* 개인 인바운드 및 아웃 바운드 주소를 사용 하 여 응용 프로그램 게이트웨이 만들기
* 서비스 끝점을 사용 하 여 앱에 대 한 인바운드 트래픽 보안 
* VNet에서 앱의 백 엔드는 새 VNet 통합 사용 

이 배포 스타일을 인터넷에 아웃 바운드 트래픽에 대 한 전용된 주소를 제공 하거나 앱에서 모든 아웃 바운드 트래픽을 차단 하는 기능을 제공 되지 않습니다.  이 배포 스타일만 그렇지 않은 경우 제공 ase의 많은 제공 합니다. 

### <a name="create-multi-tier-applications"></a>다중 계층 응용 프로그램 만들기

다중 계층 응용 프로그램을 백 엔드 API 앱만 액세스할 수 있는 프런트 엔드 계층에서 응용 프로그램입니다. 다중 계층 응용 프로그램을 만들려면 다음을 수행할 수 있습니다.

* VNet 통합을 사용 하 여 VNet의 서브넷과 프런트 엔드 웹 앱의 백 엔드를 연결할
* 서비스 끝점을 사용 하 여 API 앱으로의 인바운드 트래픽을 프런트 엔드 웹 앱에서 사용 하는 서브넷에서 들어오는을 보호 하려면

![다중 계층 앱](media/networking-features/multi-tier-app.png)

다른 프런트 엔드 앱 및 해당 서브넷을 사용 하 여 API 앱에서 서비스 끝점에서 VNet 통합을 사용 하 여 동일한 API 앱을 사용 하는 여러 프런트 엔드 앱을 사용할 수 있습니다.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
