---
title: Azure Virtual Network와 앱 통합 - Azure App Service
description: 신규 또는 기존 Azure 가상 네트워크에 Azure App Service의 앱을 연결하는 방법을 보여 줍니다.
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 768179f8569eac14166bcbb0a888e1cdbe41d497
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128418"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Azure Virtual Network에 앱 통합
이 문서는 Azure App Service 가상 네트워크 통합 미리 보기 기능을 설명하고 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)에서 앱에 해당 기능을 설정하는 방법을 보여줍니다. [Azure VNet(Virtual Network)][VNETOverview]을 사용하면 다양한 Azure 리소스를 인터넷이 아닌 라우팅 가능한 네트워크에 배치할 수 있습니다. 그러면 이러한 네트워크가 VPN 기술을 사용하여 온-프레미스 네트워크에 연결될 수 있습니다. 

Azure App Service에는 두 가지 형태가 있습니다. 

1. 격리 계층을 제외한 모든 범위의 요금제를 지원하는 다중 테넌트 시스템
2. VNet에 배포되는 ASE(App Service Environment) 

이 문서에서는 다중 테넌트 App Service에서 사용하도록 고안된 VNet 통합 기능에 대해 설명합니다.  앱이 [App Service Environment][ASEintro]에 있는 경우 이미 VNet에 있으므로 VNet 통합 기능을 사용하여 동일한 VNet의 리소스에 연결할 필요가 없습니다.

VNet 통합은 가상 네트워크의 리소스에 대한 액세스 권한을 웹앱에 부여하지만, 가상 네트워크로부터의 웹앱에 대한 개인 액세스 권한은 부여하지 않습니다. 개인 사이트 액세스는 Azure Virtual Network와 같은 개인 네트워크에서만 앱에 액세스할 수 있도록 합니다. 개인 사이트 액세스는 ILB(내부 부하 분산 장치)로 ASE가 구성된 상태에서만 사용할 수 있습니다. ILB ASE 사용 방법에 대한 자세한 내용을 보려면 [ILB ASE 만들기 및 사용][ILBASE] 문서부터 살펴봅니다. 

VNet 통합은 종종 VNet에서 실행되는 데이터베이스 및 웹 서비스에 앱에서 액세스할 수 있도록 하는 데 사용됩니다. VNet 통합을 사용하면 VM의 애플리케이션에 대한 공개 엔드포인트를 노출할 필요가 없지만 인터넷에서 사용할 수 없고 라우팅할 있는 개인 주소를 대신 사용할 수 있습니다. 

VNet 통합 기능에는 다음이 적용됩니다.

* 표준, 프리미엄 또는 프리미엄 V2 요금제가 필요합니다. 
* 클래식 또는 Resource Manager VNet에서 작동합니다. 
* TCP 및 UDP를 지원합니다.
* 웹, 모바일, API 앱 및 함수 앱에서 작동합니다.
* 앱이 한 번에 1개의 VNet에만 연결할 수 있도록 합니다.
* App Service 계획에 VNet을 최대 5개까지 통합할 수 있도록 합니다. 
* App Service 계획에 여러 개의 앱이 동일한 VNet을 사용하도록 허용합니다.
* 지점 및 사이트 간 VPN으로 구성된 가상 네트워크 게이트웨이가 필요합니다.
* 게이트웨이의 SLA에 따라 99.9% SLA를 지원합니다.

VNet 통합에서 지원하지 않는 다음 몇 가지 항목이 있습니다.

* 드라이브 탑재
* AD 통합 
* NetBios
* 개인 사이트 액세스
* ExpressRoute를 통한 리소스 액세스 
* 서비스 엔드포인트를 통한 리소스 액세스 

### <a name="getting-started"></a>시작
웹앱을 가상 네트워크에 연결하기 전에 다음의 몇 가지 사항에 유의해야 합니다.

* 대상 가상 네트워크가 앱에 연결되려면 먼저 경로 기반 게이트웨이를 사용하여 지점 및 사이트 간 VPN을 사용하도록 설정되어 있어야 합니다. 
* VNet은 ASP(App Service 계획)와 동일한 구독 내에 있어야 합니다.
* VNet과 통합되는 앱은 VNet에 대해 지정된 DNS를 사용합니다.

## <a name="enabling-vnet-integration"></a>VNet 통합 사용

미리 보기에는 VNet 통합 기능의 새 버전이 있습니다. 이는 지점 및 사이트 간 VPN을 사용하지 않으며, ExpressRoute 또는 서비스 엔드 포인트를 통한 리소스 액세스를 지원합니다. 새 미리 보기 기능에 대한 자세한 내용은 이 문서의 끝부분을 참조하세요. 

### <a name="set-up-a-gateway-in-your-vnet"></a>VNet에 게이트웨이 설정 ###

지점 및 사이트 간 주소로 구성된 게이트웨이가 이미 있는 경우 앱에서 VNet 통합을 구성하는 단계로 건너뛸 수 있습니다.  
게이트웨이를 만들려면,

1. VNet에 [게이트웨이 서브넷을 만듭니다][creategatewaysubnet].  

1. [VPN 게이트웨이를 만듭니다][creategateway]. 경로 기반 VPN 종류를 선택합니다.

1. [지점 및 사이트 간 주소를 설정합니다][setp2saddresses]. 게이트웨이가 기본 SKU에 없으면 지점 및 사이트 간 구성에서 IKEV2를 사용하지 않도록 설정해야 하며 SSTP를 선택해야 합니다. 주소 공간은 다음 주소 블록 중 하나에 있어야 합니다.

* 10.0.0.0/8 - IP 주소 범위가 10.0.0.0 ~ 10.255.255.255입니다.
* 172.16.0.0/12 - IP 주소 범위가 172.16.0.0 ~ 172.31.255.255입니다. 
* 192.168.0.0/16 - IP 주소 범위가 192.168.0.0 ~ 192.168.255.255입니다.

도 게이트웨이를 만드는 앱 서비스 VNet 통합을 사용 하 여 사용, 인증서를 업로드할 필요가 없습니다. 게이트웨이를 만드는 데 30분이 걸릴 수 있습니다. 게이트웨이가 프로비전될 때까지는 VNet과 앱을 통합할 수 없습니다. 

### <a name="configure-vnet-integration-with-your-app"></a>앱에서 VNet 통합 구성 ###

앱에서 VNet 통합을 사용하도록 설정하려면, 

1. Azure Portal에서 앱으로 이동하고, 앱 설정을 열고, 네트워킹 > VNet 통합을 차례로 선택합니다. VNet 통합을 구성하기 전에 ASP를 표준 SKU 이상으로 확장합니다. 
 ![VNet 통합 UI][1]

1. **VNet 추가**를 선택합니다. 
 ![VNet 통합 추가][2]

1. VNet을 선택합니다. 
  ![VNet 선택][8]
  
이 마지막 단계가 완료되면 앱이 다시 시작됩니다.  

## <a name="how-the-system-works"></a>시스템 작동 방식
VNet 통합 기능은 지점 및 사이트 간 VPN 기술을 기반으로 합니다. Azure App Service의 앱은 다중 테넌트 시스템에서 호스팅되므로 앱을 VNet에 직접 프로비전할 수 없습니다. 지점 및 사이트 간 기술은 앱을 호스팅하는 가상 머신에 대한 네트워크 액세스만 제한합니다. 앱은 하이브리드 연결 또는 VNet 통합을 통해서만 트래픽을 인터넷으로 보내도록 제한됩니다. 

![VNet 통합의 작동 방식][3]

## <a name="managing-the-vnet-integrations"></a>VNet 통합 관리
VNet에 연결하고 연결을 해제하는 기능은 앱 수준에 있습니다. 여러 앱에서 VNet 통합에 영향을 줄 수 있는 작업은 App Service 계획 수준에 있습니다. 앱 UID에서 VNet에 대한 세부 정보를 가져올 수 있습니다. ASP 수준에서도 동일한 정보가 표시됩니다. 

 ![VNet 세부 정보][4]

네트워크 기능 상태 페이지에서 앱이 VNet에 연결되어 있는지 볼 수 있습니다. 어떤 이유로든 VNet 게이트웨이가 중단되면 상태가 '연결되지 않음'으로 표시됩니다. 

앱 수준 VNet 통합 UI에 제공되는 정보는 ASP에서 얻는 세부 정보와 동일합니다. 해당 항목은 다음과 같습니다.

* VNet 이름 - 가상 네트워크 UI에 연결됩니다.
* 위치 - VNet의 위치를 반영합니다. 다른 위치에서 VNet과 통합하면 앱에 대기 시간 문제가 발생할 수 있습니다. 
* 인증서 상태 - 인증서가 App Service 계획과 VNet 간에 동기화되면 반영됩니다.
* 게이트웨이 상태 - 어떠한 이유로든 게이트웨이가 다운되면 앱은 VNet의 리소스에 액세스할 수 없습니다. 
* VNet 주소 공간 - VNet에 대한 IP 주소 공간을 표시합니다. 
* 지점 및 사이트 간 주소 공간 - VNet에 대한 지점 및 사이트 간 IP 주소 공간을 표시합니다. VNet에 호출할 때 앱 FROM 주소는 이러한 주소 중 하나가 됩니다. 
* 사이트 간 주소 공간 - 사이트 간 VPN을 사용하여 VNet을 온-프레미스 리소스 또는 다른 VNet에 연결할 수 있습니다. 해당 VPN 연결을 사용하여 정의된 IP 범위가 여기에 표시됩니다.
* DNS 서버 - VNet에 구성된 DNS 서버를 표시합니다.
* VNet으로 라우팅된 IP 주소 - 트래픽을 VNet에 전달하는 데 사용되는 라우팅된 주소 블록을 표시합니다. 

VNet 통합의 앱 보기에 가져올 수 있는 유일한 작업은 현재 연결되어 있는 VNet과 앱의 연결을 해제하는 것입니다. VNet에서 앱의 연결을 끊으려면 **연결 끊기**를 선택합니다. VNet과의 연결을 끊으면 앱이 다시 시작됩니다. 연결을 끊더라도 VNet은 변경되지 않습니다. VNet 및 게이트웨이를 비롯한 VNet 구성은 변경되지 않고 유지됩니다. VNet을 삭제하려면 게이트웨이를 포함하여 VNet에 속하는 리소스를 먼저 삭제해야 합니다. 

ASP VNet 통합 UI에 도달하려면 ASP UI를 열고 **네트워킹**을 선택합니다.  VNet 통합 아래에서 **구성하려면 여기를 클릭**을 선택하여 네트워크 기능 상태 UI를 엽니다.

![ASP VNet 통합 정보][5]

ASP VNet 통합 UI에는 ASP의 앱에서 사용하는 모든 VNet이 표시됩니다. App Service 계획에 있는 여러 앱에서 최대 5개의 VNet을 연결할 수 있습니다. 각 앱에는 하나의 통합만 구성할 수 있습니다. 각 VNet에 대한 세부 정보를 보려면 관심 있는 VNet을 클릭합니다. 여기서 수행할 수 있는 두 가지 작업은 다음과 같습니다.

* **네트워크 동기화**. 네트워크 동기화 작업은 인증서와 네트워크 정보가 동기화되도록 합니다. VNet의 DNS를 추가하거나 변경하면 **네트워크 동기화** 작업을 수행해야 합니다. 이 작업이 수행되면 이 VNet을 사용하는 모든 앱이 다시 시작됩니다.
* **경로 추가**. 경로를 추가하면 아웃바운드 트래픽이 VNet으로 전달됩니다.

**라우팅** VNet에 정의된 경로는 트래픽을 앱에서 VNet으로 전달하는 데 사용됩니다. 아웃바운드 트래픽을 VNet에 추가로 보내야 하는 경우 해당 주소 블록을 여기에 추가할 수 있습니다.   

**인증서** VNet 통합을 사용하도록 설정하면 연결 보안을 위해 필요한 인증서 교환이 있습니다. 인증서와 함께 네트워크를 설명하는 DNS 구성, 경로 및 다른 비슷한 항목이 있습니다.
인증서 또는 네트워크 정보가 변경되면 "네트워크 동기화"를 클릭해야 합니다. “네트워크 동기화”를 클릭하면 앱과 VNet 간의 연결이 잠시 중단됩니다. 앱이 다시 시작되지는 않지만, 연결 손실로 인해 사이트가 제대로 작동하지 않을 수 있습니다. 

## <a name="accessing-on-premises-resources"></a>온-프레미스 리소스 액세스
앱은 사이트 간 연결이 있는 VNet과 통합되어 온-프레미스 리소스에 액세스할 수 있습니다. 온-프레미스 리소스에 액세스하려면 온-프레미스 VPN 게이트웨이 경로를 지점 및 사이트 간 주소 블록으로 업데이트해야 합니다. 사이트 간 VPN을 처음 설정하는 경우 이를 구성하는 데 사용되는 스크립트에서 경로를 올바르게 설정해야 합니다. 사이트 간 VPN을 만든 후에 지점 및 사이트 간 VPN을 추가하는 경우 해당 경로를 수동으로 업데이트해야 합니다. 이 작업을 수행하는 방법에 대한 세부 정보는 게이트웨이마다 다르며 여기에 설명되어 있지 않습니다. 또한 BGP는 사이트 간 VPN 연결을 사용하여 구성할 수 없습니다.

> [!NOTE]
> VNet 통합 기능은 ExpressRoute 게이트웨이가 있는 VNet과 앱을 통합하지 않습니다. ExpressRoute 게이트웨이가 [동시 사용 모드][VPNERCoex]로 구성되어 있더라도 VNet 통합은 작동하지 않습니다. ExpressRoute 연결을 통해 리소스에 액세스해야 하는 경우에는 VNet에서 실행되는 [App Service Environment][ASE]를 사용할 수 있습니다. 
> 
> 

## <a name="peering"></a>피어링
VNet 통합을 사용하여 연결한 VNet에 피어링된 VNet의 리소스에 액세스할 수 있습니다. 앱에서 작동하도록 피어링을 구성하려면,

1. 앱이 연결되는 VNet에 피어링 연결을 추가합니다. 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용**을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **게이트 전송 허용**을 선택합니다.
1. 연결된 VNet에 피어링되는 VNet에 피어링 연결을 추가합니다. 대상 VNet에 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용**을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **원격 게이트웨이 허용**을 선택합니다.
1. 포털에서 App Service 계획 > 네트워킹 > VNet 통합 UI로 차례로 이동합니다.  앱이 연결되는 VNet을 선택합니다. 라우팅 섹션 아래에서 앱이 연결되는 VNet과 피어링되는 VNet의 주소 범위를 추가합니다.  


## <a name="pricing-details"></a>가격 정보
VNet 통합 기능의 사용과 관련된 다음 세 가지 요금이 있습니다.

* ASP 가격 책정 계층 요구 사항
* 데이터 전송 비용
* VPN Gateway 비용

앱은 표준, 프리미엄 또는 프리미엄 V2 App Service 계획에 있어야 합니다. 해당 비용에 대한 자세한 내용은 [App Service 가격][ASPricing]을 참조하세요. 

VNet이 동일한 데이터 센터에 있더라도 데이터 송신에 대한 요금이 있습니다. 이러한 요금은 [데이터 전송 가격 정보][DataPricing]에서 설명하고 있습니다. 

지점 및 사이트 간 VPN에 필요한 VNet 게이트웨이에 대한 비용이 있습니다. 자세한 내용은 [VPN Gateway 가격 페이지][VNETPricing]에 있습니다.

## <a name="troubleshooting"></a>문제 해결
기능을 설정하기 쉽다고 해서 환경에 문제가 없는 것은 아닙니다. 원하는 엔드포인트에 액세스하다가 문제가 발생하는 경우, 앱 콘솔에서 연결을 테스트하는 데 사용할 수 있는 유틸리티가 있습니다. 사용할 수 있는 두 개의 콘솔이 있습니다. 하나는 Kudu 콘솔이고, 다른 하나는 Azure Portal의 콘솔입니다. 앱에서 Kudu 콘솔에 도달하려면 도구 -> Kudu로 차례로 이동합니다. 이것은 [sitename].scm.azurewebsites.net으로 이동하는 것과 마찬가지입니다. 해당 콘솔이 열리면 [디버그] 콘솔 탭으로 이동합니다. Azure 포털에 호스트되는 콘솔로 이동하려면 앱에서 도구 -> 콘솔로 이동합니다. 

#### <a name="tools"></a>도구
**ping**, **nslookup** 및 **tracert** 도구는 보안 제약 조건으로 인해 콘솔을 통해 작동되지 않습니다. 공백을 채우기 위해 별도의 두 가지 도구가 추가되었습니다. DNS 기능을 테스트하기 위해 nameresolver.exe라는 도구가 추가되었습니다. 구문은 다음과 같습니다.

    nameresolver.exe hostname [optional: DNS Server]

**nameresolver**를 사용하여 앱에서 사용하는 호스트 이름을 확인합니다. 이렇게 하면 DNS에 잘못 구성된 항목이 있는지 또는 DNS 서버에 대한 액세스가 없는지를 테스트할 수 있습니다.

다음 도구는 호스트와 포트에 대한 TCP 연결을 테스트하는 데 사용할 수 있습니다. 이 도구는 **tcpping**이라고 하며, 구문은 다음과 같습니다.

    tcpping.exe hostname [optional: port]

특정 호스트 및 포트에 연결할 수 있으면 **tcpping** 유틸리티에서 이를 알려줍니다. 호스트 및 포트 조합에서 수신 대기하는 애플리케이션이 있고 네트워크를 통해 앱에서 지정된 호스트 및 포트에 액세스할 수 있는 경우에만 성공으로 표시될 수 있습니다.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>VNet에서 호스트되는 리소스에 대한 액세스 디버깅
특정 호스트와 포트에 앱이 도달하지 못하도록 방해할만한 요소는 많이 있습니다. 대부분의 경우 다음 세 가지 중 하나입니다.

* **중간에 방화벽이 있음.** 중간에 방화벽이 있으면 TCP 시간 제한에 도달합니다. 이 경우 TCP 시간 제한은 21초입니다. **tcpping** 도구를 사용하여 연결을 테스트합니다. TCP 시간 초과는 방화벽 뒤에 많은 항목이 있으나 방화벽 뒤에서 시작하기 때문일 수 있습니다. 
* **DNS에 액세스할 수 없음.** DNS 시간 제한은 DNS 서버당 3초입니다. DNS 서버가 2개 있으면 제한 시간은 6초입니다. DNS가 작동하는지 확인하려면 nameresolver를 사용합니다. VNet이 구성된 DNS를 사용하지 않으므로 nslookup을 사용할 수 없음에 주의하세요.
* **잘못된 지점 및 사이트 간 주소 범위.** 지점 및 사이트 간 IP 범위는 RFC 1918 사설 IP 범위(10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)에 있어야 합니다. 이 범위를 벗어나는 IP가 사용되면 제대로 작동하지 않습니다. 

해당 항목이 문제에 응답하지 않는 경우 먼저 다음과 같이 간단한 사항을 확인합니다. 

* 게이트웨이가 포털에서 실행되고 있나요?
* 인증서가 동기화된 상태로 표시되나요?
* 누군가가 영향을 받는 ASP에서 Sync Network(네트워크 동기화)를 수행하지 않고 네트워크 구성을 변경했나요? 

게이트웨이가 다운되어 있으면 다시 가동시킵니다. 인증서가 동기화되지 않은 상태이면 VNet 통합의 ASP 보기로 이동하여 “네트워크 동기화”를 클릭합니다. ASP와 동기화되지 않은 VNet 구성이 변경되었다고 의심되는 경우 "네트워크 동기화"를 누릅니다.  "네트워크 동기화" 작업은 해당 VNet과 통합된 ASP의 모든 앱을 다시 시작합니다. 

이 모든 사항에 문제가 없으면 좀 더 깊이 살펴봐야 합니다.

* 동일한 VNet에서 리소스에 도달하는 데 VNet 통합을 사용하는 다른 앱이 있나요? 
* 그 앱의 콘솔로 이동하여 tcpping을 사용해서 VNet에 속하는 다른 리소스에 도달할 수 있나요? 

위 질문에 대한 답이 참이라면 VNet 통합에는 문제가 없고 어딘가 다른 부분에 문제가 있는 것입니다. 이런 경우에는 호스트:포트에 도달할 수 없는 이유를 알 수 있는 간단한 방법이 없기 때문에 해결하기가 더 어려워집니다. 여기에 해당하는 경우는 다음과 같습니다.

* 호스트에 방화벽이 실행 중이며 지점과 사이트 간 IP 범위에서 애플리케이션 포트에 대한 액세스를 방해합니다. 보통 교차 서브넷에는 공용 액세스가 필요합니다.
* 대상 호스트가 다운되었습니다.
* 애플리케이션이 다운되었습니다.
* IP 또는 호스트 이름이 잘못되었습니다.
* 애플리케이션이 예상치 않은 다른 포트를 수신 대기 중입니다. 엔드포인트 호스트에서 "netstat -aon"을 사용하여 프로세스 ID를 수신 대기 포트와 일치시킬 수 있습니다. 
* 네트워크 보안 그룹이 지점과 사이트 간 IP 범위에서 애플리케이션 호스트 및 포트에 대한 액세스를 금지하도록 구성되어 있습니다.

지점 및 사이트 간 IP 범위 내에서 앱이 사용할 IP를 알 수 없으므로 전체 범위에서 액세스할 수 있도록 허용해야 합니다. 

추가적인 디버그 단계에는 다음 내용이 포함됩니다.

* VNet의 VM에 연결하고 이 위치에서 리소스 호스트:포트에 연결하려고 시도합니다. TCP 액세스를 테스트하려면 **test-netconnection** PowerShell 명령을 사용합니다. 구문은 다음과 같습니다.

      test-netconnection hostname [optional: -Port]

* VM에서 애플리케이션을 실행하고, 앱에서 콘솔의 해당 호스트와 포트에 대한 액세스를 테스트합니다.

#### <a name="on-premises-resources"></a>온-프레미스 리소스 ####

앱에서 온-프레미스 리소스에 연결할 수 없으면 VNet에서 리소스에 연결할 수 있는지 확인합니다. **test-netconnection** PowerShell 명령을 사용하여 TCP 액세스를 확인합니다. VM이 온-프레미스 리소스에 도달할 수 없으면 사이트 간 VPN 연결이 작동하는지 확인합니다. 제대로 작동하는 경우에는, 앞서 언급한 사항을 확인하고 온-프레미스 게이트웨이 구성 및 상태를 확인합니다. 

VNet 호스팅된 VM에서 온-프레미스 시스템에 연결할 수 있지만 앱에서는 연결할 수 없는 경우 다음과 같은 이유 중 하나가 원인일 수 있습니다.

* 경로를 온-프레미스 게이트웨이에서 지점과 사이트 간 IP 범위로 구성하지 않음
* 네트워크 보안 그룹에서 지점 및 사이트 간 IP 범위에 대한 액세스를 차단하고 있음
* 온-프레미스 방화벽에서 지점 및 사이트 간 IP 범위의 트래픽을 차단하고 있음


## <a name="powershell-automation"></a>PowerShell 자동화

PowerShell을 사용하여 App Service를 Azure Virtual Network와 통합할 수 있습니다. 실행 준비 스크립트의 경우 [Azure App Service에서 Azure Virtual Network에 앱 연결](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)을 참조하세요.

## <a name="hybrid-connections-and-app-service-environments"></a>하이브리드 연결 및 App Service Environment
VNet에 호스트되는 리소스에 액세스할 수 있도록 하는 기능은 3가지 입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

* VNet 통합
* 하이브리드 연결
* App Service Environment

하이브리드 연결을 사용하려면 네트워크에 하이브리드 연결 관리자(HCM)라고 하는 릴레이 에이전트를 설치해야 합니다. HCM이 Azure와 애플리케이션에 연결할 수 있어야 합니다. 하이브리드 연결에는 VPN 연결에 필요한 것처럼 원격 네트워크에 대한 인터넷 액세스 가능한 인바운드 엔드포인트가 필요하지 않습니다. HCM은 Windows에서만 실행되며 고가용성을 제공하기 위해 인스턴스를 최대 5개까지 실행하도록 할 수 있습니다. 하이브리드 연결은 TCP만 지원하며 각 HC 엔드포인트는 특정 호스트:포트 조합과 일치해야 합니다. 

App Service Environment 기능을 사용하면 VNet에서 Azure App Service의 단일 테넌트 인스턴스를 실행할 수 있습니다. 앱이 App Service Environment에 있는 경우 추가 단계 없이 VNet의 리소스에 액세스할 수 있습니다. App Service Environment를 사용 하 여 앱이 더 강력한 작업자에서 실행 하 고 최대 100 개 ASP 인스턴스로 확장할 수 있습니다. App Service Environment는 ExpressRoute 및 서비스 엔드포인트를 포함한 모든 네트워킹 기능에서 작동합니다.  

일부 사용 사례가 겹치는 경우도 있지만, 이러한 기능 중 어떤 것도 다른 기능을 대체할 수 없습니다. 무엇이 필요한지에 따라 사용할 기능이 달라집니다. 예를 들면 다음과 같습니다.

* 개발자로서 책상 아래의 워크스테이션에 있는 데이터베이스에 액세스할 수 있는 사이트를 Azure에서 운영하려면 하이브리드 연결을 사용하는 것이 가장 쉽습니다. 
* 다수의 웹 속성을 공용 클라우드에 보관해 놓고 자사의 네트워크에서 해당 속성을 관리하기를 원하는 규모가 큰 조직의 경우 App Service 환경이 적합합니다. 
* VNet에서 리소스에 액세스해야 하는 앱이 여러 개 있는 경우 [VNet 통합]을 사용하는 것이 좋습니다. 

VNet이 온-프레미스 네트워크에 이미 연결되어 있는 경우 VNet 통합 또는 App Service Environment를 사용하면 온-프레미스 리소스를 손쉽게 사용할 수 있습니다. VNet이 온-프레미스 네트워크에 연결되어 있지 않은 경우 VNet을 사용하여 사이트 간 VPN을 설정하면 HCM을 설치하는 것과 비교할 때 훨씬 더 많은 오버헤드가 발생합니다. 

기능적인 차이 외에 가격의 차이도 있습니다. App Service Environment 기능은 프리미엄 서비스 제품이지만 대부분의 네트워크 구성 가능성을 제공하고 다른 좋은 기능도 추가적으로 제공합니다. VNet 통합은 표준 또는 프리미엄 ASP에서 사용할 수 있으며 다중 테넌트 App Service에서 VNet의 리소스를 안전하게 사용하기에 가장 적합한 환경입니다. 하이브리드 연결은 현재 BizTalk 계정에 의존하고 있으며, BizTalk의 가격 수준은 무료에서 시작해서 필요한 양에 따라 점차적으로 비용이 높아집니다. 하지만 수많은 네트워크에서 작업이 진행되는 경우, 100개가 훨씬 넘는 네트워크(분리된)의 리소스에 액세스하기에는 하이브리드 연결만한 기능이 없습니다. 

## <a name="new-vnet-integration"></a>새 VNet 통합 ##

지점 및 사이트 간 VPN 기술을 사용하지 않는 새 버전의 VNet 통합 기능이 있습니다. 기존 기능과 달리 새 미리 보기 기능은 ExpressRoute 및 서비스 엔드포인트에서 작동합니다. 

새 기능은 최신 Azure App Service 배율 단위에서만 사용할 수 있습니다. 프리미엄 V2로 확장할 수 있는 경우 최신의 App Service 배율 단위에 있습니다. 포털의 VNet 통합 UI는 앱에서 새 VNet 통합 기능을 사용할 수 있는지 여부를 알려줍니다. 

새 버전은 미리 보기에 있으며 다음과 같은 특징이 있습니다.

* 게이트웨이는 새 VNet 통합 기능을 사용하는 데 필요하지 않습니다.
* VNet에 연결된 ExpressRoute와의 통합을 제외하고는 추가 구성 없이 ExpressRoute 연결을 통해 리소스에 액세스할 수 있습니다.
* 앱과 VNet은 동일한 지역에 있어야 합니다.
* 새 기능을 사용하려면 Resource Manager VNet에서 사용되지 않는 서브넷이 필요합니다.
* App Service 계획은 표준, 프리미엄 또는 프리미엄 V2 요금제여야 합니다.
* 새 기능이 미리 보기에 있는 동안에는 프로덕션 워크로드가 지원되지 않습니다.
* 앱은 프리미엄 v2까지 확장할 수 있는 Azure App Service 배포에 있어야 합니다.
* 새 VNet 통합 기능은 App Service Environment의 앱에서 작동하지 않습니다.
* VNet은 통합된 앱을 사용하여 삭제할 수 없습니다.  
* 경로 테이블과 글로벌 피어링은 새 VNet 통합에서 아직 사용할 수 없습니다.  
* 각 App Service 계획 인스턴스마다 하나의 주소가 사용됩니다. 서브넷 크기는 할당 후에 변경할 수 없으므로 최대 규모 크기를 처리할 수 있는 서브넷을 사용합니다. 32개의 주소를 사용하는 /27은 20개의 인스턴스로 확장된 App Service 계획을 수용할 수 있는 권장 크기입니다.
* 새 VNet 통합 기능을 사용하여 서비스 엔드포인트 보안 리소스를 사용할 수 있습니다. 이렇게 하려면 VNet 통합에 사용되는 서브넷에서 서비스 엔드포인트를 사용하도록 설정합니다.

새 기능을 사용하려면,

1. 포털에서 네트워킹 UI로 이동합니다. 앱에서 새로운 기능을 사용할 수 있는 경우 새 미리 보기 기능을 사용할 수 있는 기능이 표시됩니다.  

   ![새 미리 보기 VNet 통합 선택][6]

1. **VNet 추가(미리 보기)** 를 선택합니다.  

1. 통합하려는 Resource Manager VNet을 선택한 다음, 새 서브넷을 만들거나 기존의 빈 서브넷을 선택합니다. 통합을 완료하는 데 1분 미만이 걸립니다. 통합하는 동안에 앱이 다시 시작됩니다.  통합이 완료되면 통합된 VNet에 대한 세부 정보와 위쪽에서 해당 기능이 미리 보기로 있음을 알려주는 배너가 표시됩니다.

   ![VNet 및 서브넷 선택][7]

앱에서 VNet이 구성된 DNS 서버를 사용하도록 설정하려면 앱에 대한 애플리케이션 설정을 만듭니다. 여기서 이름은 WEBSITE_DNS_SERVER이고, 값은 서버의 IP 주소입니다.  보조 DNS 서버가 있는 경우 이름이 WEBSITE_DNS_ALT_SERVER이고 값이 서버의 IP 주소인 다른 애플리케이션 설정을 만듭니다. 

VNet과의 앱 연결을 끊으려면 **연결 끊기**를 선택합니다. 그러면 웹앱이 다시 시작됩니다. 

새 VNet 통합 기능을 사용하면 서비스 엔드포인트를 사용할 수 있습니다.  앱에서 서비스 엔드 포인트를 사용하려면 새 VNet 통합을 사용하여 선택한 VNet에 연결한 다음, 통합에 사용한 서브넷에서 서비스 엔드포인트를 구성합니다. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
