---
title: Azure Virtual Network와 앱 통합
description: Azure App Service Azure Virtual Network와 통합 하는 방법 및 앱을 가상 네트워크에 연결 하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: fasttrack-edit
ms.openlocfilehash: 472fe621fc7a95317f143ef96a1d7f8b5adfe581
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016972"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Azure Virtual Network에 앱 통합
이 문서에서는 Azure App Service 가상 네트워크 통합 기능 및 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)에서 앱을 사용 하 여 설정 하는 방법을 설명 합니다. [Azure vnet (가상 네트워크][VNETOverview] )를 사용 하면 여러 azure 리소스를 인터넷을 통해 라우팅할 수 없는 네트워크에 저장할 수 있습니다.  

Azure App Service에는 두 가지 변형이 있습니다. 

1. 격리 계층을 제외한 모든 범위의 요금제를 지원하는 다중 테넌트 시스템
2. VNet에 배포 되 고 격리 된 가격 책정 계획 앱을 지 원하는 ASE (App Service Environment)

이 문서에서는 다중 테넌트 App Service에 사용 되는 두 개의 VNet 통합 기능을 안내 합니다. 앱이 [App Service Environment][ASEintro]에 있는 경우 이미 vnet에 있으며 vnet 통합 기능을 사용 하 여 동일한 vnet의 리소스에 연결할 필요가 없습니다. 모든 App Service 네트워킹 기능에 대한 자세한 내용은 [네트워킹 기능 App Service](networking-features.md) 를 참조 하세요.

VNet 통합 기능에는 두 가지 형태가 있습니다.

1. 한 버전에서는 동일한 지역에서 Vnet와 통합할 수 있습니다. 이 기능의 형태에는 동일한 지역의 VNet에 서브넷이 필요 합니다. 이 기능은 아직 미리 보기 상태 이지만 아래에 설명 된 몇 가지 주의 사항으로 Windows 앱 프로덕션 워크 로드에 대해 지원 됩니다.
2. 다른 버전에서는 다른 지역 또는 클래식 Vnet와의 통합을 Vnet 수 있습니다. 이 버전의 기능을 사용 하려면 VNet에 Virtual Network 게이트웨이를 배포 해야 합니다. 이는 지점 및 사이트 간 VPN 기반 기능이 며 Windows 앱 에서만 지원 됩니다.

앱은 한 번에 한 가지 형식의 VNet 통합 기능만 사용할 수 있습니다. 그런 다음 사용 해야 하는 기능에 대한 질문입니다. 여러 항목에 대해 사용할 수 있습니다. 분명히 차이점는 다음과 같습니다.

| 문제  | 솔루션 | 
|----------|----------|
| 동일한 지역에서 RFC 1918 주소 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)에 도달 하려고 합니다. | 지역 VNet 통합 |
| 클래식 VNet 또는 다른 지역의 VNet에서 리소스에 도달 하려고 합니다. | 게이트웨이 필요 VNet 통합 |
| Express 경로에서 RFC 1918 엔드포인트에 도달 하려고 합니다. | 지역 VNet 통합 |
| 서비스 엔드포인트에서 리소스에 연결 하려고 합니다. | 지역 VNet 통합 |

두 기능 모두 Express 경로에서 비 RFC 1918 주소에 도달할 수 있습니다. 이렇게 하려면 ASE를 지금 사용 해야 합니다.

지역 VNet 통합을 사용 하는 경우 VNet을 온-프레미스에 연결 하거나 서비스 엔드포인트을 구성 하지 않습니다. 별도의 네트워킹 구성입니다. 지역 VNet 통합을 사용 하면 응용 프로그램에서 이러한 연결 형식에 대한 호출을 수행할 수 있습니다.

사용 된 버전에 관계 없이 VNet 통합은 가상 네트워크의 리소스에 대한 웹 앱 액세스를 제공 하지만 가상 네트워크에서 웹 앱에 대한 인바운드 개인 액세스를 부여 하지 않습니다. 프라이빗 사이트 액세스는 Azure Virtual Network와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 합니다. VNet 통합은 앱에서 VNet으로의 아웃 바운드 호출을 수행 하는 경우에만 사용할 수 있습니다. 

VNet 통합 기능에는 다음이 적용됩니다.

* 표준, 프리미엄 또는 프리미엄 V2 요금제가 필요합니다. 
* TCP 및 UDP를 지원합니다.
* App Service 앱 및 함수 앱에서 작동

VNet 통합에서 지원하지 않는 다음 몇 가지 항목이 있습니다.

* 드라이브 탑재
* AD 통합 
* NetBios

## <a name="regional-vnet-integration"></a>지역 VNet 통합 

> [!NOTE]
> 피어 링은 아직 Linux 기반 App Service에서 사용할 수 없습니다.
>

앱과 동일한 지역에서 VNet 통합을 Vnet와 함께 사용 하는 경우 32 개 이상의 주소가 있는 위임 된 서브넷을 사용 해야 합니다. 서브넷은 다른 항목에 사용할 수 없습니다. 앱에서 수행 되는 아웃 바운드 호출은 위임 된 서브넷의 주소에서 생성 됩니다. 이 버전의 VNet 통합을 사용 하는 경우 VNet의 주소에서 호출이 수행 됩니다. VNet에서 주소를 사용 하면 앱이 다음을 수행할 수 있습니다.

* 서비스 엔드포인트 보안 서비스를 호출 합니다.
* Express 경로 연결에서 리소스 액세스
* 연결 된 VNet의 리소스에 액세스 합니다.
* Express 경로 연결을 포함 하 여 피어 링 연결에서 리소스에 액세스

이 기능은 미리 보기 상태 이지만 다음과 같은 제한 사항이 있는 Windows 앱 프로덕션 워크 로드에 대해 지원 됩니다.

* RFC 1918 범위에 있는 주소에만 도달할 수 있습니다. 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 주소 블록의 주소입니다.
* 전역 피어 링 연결에서 리소스에 연결할 수 없습니다.
* 앱에서 들어오는 트래픽에 대한 경로를 VNet으로 설정할 수 없습니다.
* 이 기능은 PremiumV2 App Service 계획을 지 원하는 최신 App Service 배율 단위 에서만 사용할 수 있습니다. 이는 앱이 PremiumV2 SKU에서 실행 되어야 한다는 의미는 아닙니다. PremiumV2 옵션을 사용할 수 있는 App Service 계획에서 실행 되어야 한다는 것을 의미 합니다 (이는 VNet 통합 기능을 사용할 수 있는 최신 배율 단위).
* 통합 서브넷은 하나의 App Service 계획 에서만 사용할 수 있습니다.
* 이 기능은 App Service Environment에 있는 격리 된 계획 앱에서 사용할 수 없습니다.
* 이 기능을 사용 하려면 리소스 관리자 VNet에서 32 주소 이상인/27의 사용 하지 않는 서브넷이 필요 합니다.
* 앱과 VNet은 동일한 지역에 있어야 합니다.
* VNet은 통합된 앱을 사용하여 삭제할 수 없습니다. 먼저 통합을 제거 해야 합니다. 
* App Service 계획 마다 하나의 지역 VNet 통합만 사용할 수 있습니다. 동일한 App Service 계획의 여러 앱에서 동일한 VNet을 사용할 수 있습니다. 
* 지역 VNet 통합을 사용 하는 앱이 있는 동안에는 앱 또는 App Service 계획의 구독을 변경할 수 없습니다.

각 App Service 계획 인스턴스마다 하나의 주소가 사용됩니다. 앱을 5 개의 인스턴스로 크기 조정 하면 5 개의 주소가 사용 됩니다. 할당 후에는 서브넷 크기를 변경할 수 없으므로 앱이 도달할 수 있는 모든 규모를 수용할 수 있을 정도로 큰 서브넷을 사용 해야 합니다. 64 주소를 포함 하는/26은 권장 크기입니다. App Service 계획의 크기를 변경 하지 않은 경우 32 주소가 포함 된 a/27은 프리미엄 App Service 계획 20 인스턴스를 수용 합니다. App Service 계획의 크기를 조정 하거나 축소 하는 경우 짧은 시간 동안 두 번의 주소가 필요 합니다. 

다른 App Service 응용 프로그램이 다른 App Service 계획의 앱에 의해 이미 연결 되어 있는 VNet에 연결 하려는 경우 기존 VNet 통합에서 사용 하는 것과 다른 서브넷을 선택 해야 합니다.  

이 기능은 Linux 용 미리 보기에도 있습니다. 동일한 지역에서 VNet 통합 기능을 리소스 관리자 VNet과 함께 사용 하려면 다음을 수행 합니다.

1. 포털에서 네트워킹 UI로 이동합니다. 앱에서 새 기능을 사용할 수 있는 경우 VNet (미리 보기)을 추가 하는 옵션이 표시 됩니다.  

   ![VNet 통합 선택][6]

1. **VNet 추가(미리 보기)** 를 선택합니다.  

1. 통합하려는 Resource Manager VNet을 선택한 다음, 새 서브넷을 만들거나 기존의 빈 서브넷을 선택합니다. 통합을 완료하는 데 1분 미만이 걸립니다. 통합하는 동안에 앱이 다시 시작됩니다.  통합이 완료되면 통합된 VNet에 대한 세부 정보와 위쪽에서 해당 기능이 미리 보기로 있음을 알려주는 배너가 표시됩니다.

   ![VNet 및 서브넷 선택][7]

앱이 VNet과 통합 되 면 VNet이 구성 된 것과 동일한 DNS 서버를 사용 하 게 됩니다. 

지역 VNet 통합을 사용 하려면 통합 서브넷이 Microsoft 웹에 위임 되어야 합니다.  VNet 통합 UI가 자동으로 서브넷을 Microsoft. 웹에 위임 합니다. 계정에이를 설정할 수 있는 충분 한 네트워킹 권한이 없는 경우 통합 서브넷의 특성을 설정 하 여 서브넷을 위임 하는 사람이 필요 합니다. 통합 서브넷을 수동으로 위임 하려면 Azure Virtual Network 서브넷 UI로 이동 하 고 Microsoft 웹에 대한 위임을 설정 합니다.

VNet과의 앱 연결을 끊으려면 **연결 끊기**를 선택합니다. 그러면 웹앱이 다시 시작됩니다. 


#### <a name="web-app-for-containers"></a>Web App for Containers

기본 제공 이미지를 사용 하 여 Linux에서 App Service를 사용 하는 경우 지역 VNet 통합 기능은 추가 변경 없이 작동 합니다. Web App for Containers 사용 하는 경우 VNet 통합을 사용 하기 위해 docker 이미지를 수정 해야 합니다. Docker 이미지에서 하드 코드 된 포트 번호를 사용 하는 대신 포트 환경 변수를 주 웹 서버의 수신 대기 포트로 사용 합니다. 포트 환경 변수는 컨테이너 시작 시간에 App Service 플랫폼에 의해 자동으로 설정 됩니다. SSH를 사용 하는 경우 지역 VNet 통합을 사용 하는 경우 SSH_PORT 환경 변수에 지정 된 포트 번호를 수신 하도록 SSH 디먼을 구성 해야 합니다.

### <a name="service-endpoints"></a>서비스 엔드포인트

새 VNet 통합 기능을 사용하면 서비스 엔드포인트를 사용할 수 있습니다.  앱에서 서비스 엔드 포인트를 사용하려면 새 VNet 통합을 사용하여 선택한 VNet에 연결한 다음, 통합에 사용한 서브넷에서 서비스 엔드포인트를 구성합니다. 


### <a name="how-vnet-integration-works"></a>VNet 통합의 작동 방식

App Service의 앱은 작업자 역할에서 호스팅됩니다. 기본 및 더 높은 가격 책정 요금제는 동일한 작업자에서 실행 되는 다른 고객 작업이 없는 전용 호스팅 계획입니다. VNet 통합은 위임 된 서브넷의 주소를 사용 하 여 가상 인터페이스를 탑재 하는 방식으로 작동 합니다. 보낸 사람 주소는 vnet에 있기 때문에 vnet의 VM과 마찬가지로 VNet을 통해 또는에서 대부분의 항목에 액세스할 수 있습니다. 네트워킹 구현은 VNet에서 VM을 실행 하는 것과 다르며이 기능을 사용 하는 동안 일부 네트워킹 기능을 아직 사용할 수 없습니다.

![VNet 통합](media/web-sites-integrate-with-vnet/vnet-integration.png)

VNet 통합이 사용 하도록 설정 되 면 앱은 정상적으로 동일한 채널을 통해 인터넷에 대한 아웃 바운드 호출을 수행 합니다. 앱 속성 포털에 나열 된 아웃 바운드 주소는 여전히 앱에서 사용 하는 주소입니다. 앱에 대한 변경 내용, 서비스 엔드포인트 보안 서비스 또는 RFC 1918 주소에 대한 호출이 VNet에 전달 됩니다. 

이 기능은 작업자 당 하나의 가상 인터페이스만 지원 합니다.  작업자 당 하나의 가상 인터페이스는 App Service 계획 당 하나의 지역 VNet 통합을 의미 합니다. 동일한 App Service 계획의 모든 앱은 동일한 VNet 통합을 사용할 수 있지만 추가 VNet에 연결 하는 앱이 필요한 경우 다른 App Service 계획을 만들어야 합니다. 사용 되는 가상 인터페이스는 고객이 직접 액세스할 수 있는 리소스가 아닙니다.

이 기술이 작동 하는 방법의 특성으로 인해 VNet 통합에 사용 되는 트래픽은 Network Watcher 또는 NSG 흐름 로그에 표시 되지 않습니다.  

## <a name="gateway-required-vnet-integration"></a>게이트웨이 필요 VNet 통합 

게이트웨이는 VNet 통합 기능이 필요 합니다.

* 리소스 관리자 또는 클래식 Vnet 된 지역에서 Vnet에 연결 하는 데 사용할 수 있습니다.
* 앱이 한 번에 1 개의 VNet에만 연결할 수 있도록 합니다.
* App Service 계획에서 최대 5 개의 Vnet를 통합할 수 있습니다. 
* App Service 계획에서 사용할 수 있는 총 개수에 영향을 주지 않고 App Service 계획의 여러 앱에서 동일한 VNet을 사용할 수 있습니다.  동일한 App Service 계획에서 동일한 VNet을 사용 하는 앱이 6 개 있으면 사용 되는 VNet 1 개로 계산 됩니다. 
* 지점 및 사이트 간 VPN을 사용 하 여 구성 된 Virtual Network 게이트웨이가 필요 합니다.
* 게이트웨이의 SLA로 인해 99.9% SLA를 지원 합니다.

이 기능은 다음을 지원 하지 않습니다.
* Linux 앱과 함께 사용
* ExpressRoute를 통한 리소스 액세스 
* 서비스 엔드포인트를 통한 리소스 액세스 

### <a name="getting-started"></a>시작

웹앱을 가상 네트워크에 연결하기 전에 다음의 몇 가지 사항에 유의해야 합니다.

* 대상 가상 네트워크가 앱에 연결되려면 먼저 경로 기반 게이트웨이를 사용하여 지점 및 사이트 간 VPN을 사용하도록 설정되어 있어야 합니다. 
* VNet은 ASP(App Service 계획)와 동일한 구독 내에 있어야 합니다.
* VNet과 통합되는 앱은 VNet에 대해 지정된 DNS를 사용합니다.

### <a name="set-up-a-gateway-in-your-vnet"></a>VNet에 게이트웨이 설정 ###

지점 및 사이트 간 주소로 구성된 게이트웨이가 이미 있는 경우 앱에서 VNet 통합을 구성하는 단계로 건너뛸 수 있습니다.  
게이트웨이를 만들려면,

1. VNet에 [게이트웨이 서브넷을 만듭니다][creategatewaysubnet] .  

1. [VPN gateway를 만듭니다][creategateway]. 경로 기반 VPN 종류를 선택합니다.

1. [지점과 사이트 간 주소를 설정][setp2saddresses]합니다. 게이트웨이가 기본 SKU에 없으면 지점 및 사이트 간 구성에서 IKEV2를 사용하지 않도록 설정해야 하며 SSTP를 선택해야 합니다. 주소 공간은 RFC 1918 주소 블록 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)에 있어야 합니다.

App Service VNet 통합에 사용할 게이트웨이를 만드는 경우 인증서를 업로드할 필요가 없습니다. 게이트웨이를 만드는 데 30분이 걸릴 수 있습니다. 게이트웨이가 프로비전될 때까지는 VNet과 앱을 통합할 수 없습니다. 

### <a name="configure-vnet-integration-with-your-app"></a>앱에서 VNet 통합 구성 

앱에서 VNet 통합을 사용하도록 설정하려면, 

1. Azure Portal에서 앱으로 이동하고, 앱 설정을 열고, 네트워킹 > VNet 통합을 차례로 선택합니다. ASP는 VNet 통합 기능을 사용 하는 표준 SKU 이상에 있어야 합니다. 
 ![VNet 통합 UI][1]

1. **VNet 추가**를 선택합니다. 
 ![VNet 통합 추가][2]

1. VNet을 선택합니다. 
  ![VNet 선택][8]
  
이 마지막 단계가 완료되면 앱이 다시 시작됩니다.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>게이트웨이 필수 VNet 통합 기능 작동 방식

게이트웨이 필수 VNet 통합 기능은 지점 및 사이트 간 VPN 기술을 기반으로 합니다. 지점 및 사이트 간 기술은 앱을 호스팅하는 가상 머신에 대한 네트워크 액세스만 제한합니다. 앱은 하이브리드 연결 또는 VNet 통합을 통해서만 트래픽을 인터넷으로 보내도록 제한됩니다. 

![VNet 통합의 작동 방식][3]

## <a name="managing-vnet-integration"></a>VNet 통합 관리
VNet에 연결하고 연결을 해제하는 기능은 앱 수준에 있습니다. 여러 앱에서 VNet 통합에 영향을 줄 수 있는 작업은 App Service 계획 수준에 있습니다. 앱 > 네트워킹 > VNet 통합 포털에서 VNet에 대한 세부 정보를 가져올 수 있습니다. ASP > 네트워킹 > VNet 통합 포털의 ASP 수준에서 지정 된 통합을 사용 하는 App Service 계획의 앱을 포함 하 여 유사한 정보를 볼 수 있습니다.

 ![VNet 세부 정보][4]

VNet 통합 UI에서 사용할 수 있는 정보는 앱과 ASP 포털 간에 동일 합니다.

* VNet 이름 - 가상 네트워크 UI에 연결됩니다.
* 위치 - VNet의 위치를 반영합니다. 다른 위치에서 VNet과 통합하면 앱에 대기 시간 문제가 발생할 수 있습니다. 
* 인증서 상태 - 인증서가 App Service 계획과 VNet 간에 동기화되면 반영됩니다.
* 게이트웨이 상태-게이트웨이를 필요로 하는 VNet 통합을 사용 하는 경우 게이트웨이 상태를 볼 수 있습니다.
* VNet 주소 공간 - VNet에 대한 IP 주소 공간을 표시합니다. 
* 지점 및 사이트 간 주소 공간 - VNet에 대한 지점 및 사이트 간 IP 주소 공간을 표시합니다. 게이트웨이 필요한 기능을 사용 하는 동안 VNet을 호출 하는 경우 주소의 앱은 다음 주소 중 하나가 됩니다. 
* 사이트 간 주소 공간 - 사이트 간 VPN을 사용하여 VNet을 온-프레미스 리소스 또는 다른 VNet에 연결할 수 있습니다. 해당 VPN 연결을 사용하여 정의된 IP 범위가 여기에 표시됩니다.
* DNS 서버 - VNet에 구성된 DNS 서버를 표시합니다.
* VNet으로 라우팅된 IP 주소 - 트래픽을 VNet에 전달하는 데 사용되는 라우팅된 주소 블록을 표시합니다. 

VNet 통합의 앱 보기에 가져올 수 있는 유일한 작업은 현재 연결되어 있는 VNet과 앱의 연결을 해제하는 것입니다. VNet에서 앱의 연결을 끊으려면 **연결 끊기**를 선택합니다. VNet과의 연결을 끊으면 앱이 다시 시작됩니다. 연결을 끊더라도 VNet은 변경되지 않습니다. 서브넷 또는 게이트웨이는 제거 되지 않습니다. 그런 다음 VNet을 삭제 하려면 먼저 VNet에서 앱의 연결을 끊고 게이트웨이에서 리소스를 삭제 해야 합니다. 

ASP VNet 통합 UI에 도달하려면 ASP UI를 열고 **네트워킹**을 선택합니다.  VNet 통합 아래에서 **구성하려면 여기를 클릭**을 선택하여 네트워크 기능 상태 UI를 엽니다.

![ASP VNet 통합 정보][5]

ASP VNet 통합 UI에는 ASP의 앱에서 사용하는 모든 VNet이 표시됩니다. 각 VNet에 대한 세부 정보를 보려면 관심 있는 VNet을 클릭합니다. 여기서 수행할 수 있는 두 가지 작업은 다음과 같습니다.

* **네트워크 동기화**. 네트워크 동기화 작업은 게이트웨이 종속 VNet 통합 기능에만 사용할 수 있습니다. 네트워크 동기화 작업을 수행 하면 인증서와 네트워크 정보가 동기화 됩니다. VNet의 DNS를 추가 하거나 변경 하는 경우 **동기화 네트워크** 작업을 수행 해야 합니다. 이 작업이 수행되면 이 VNet을 사용하는 모든 앱이 다시 시작됩니다.
* **경로 추가**. 경로를 추가하면 아웃바운드 트래픽이 VNet으로 전달됩니다.

**라우팅** VNet에 정의된 경로는 트래픽을 앱에서 VNet으로 전달하는 데 사용됩니다. 아웃바운드 트래픽을 VNet에 추가로 보내야 하는 경우 해당 주소 블록을 여기에 추가할 수 있습니다. 이 기능은 게이트웨이 필수 VNet 통합 에서만 작동 합니다.

**인증서** 게이트웨이를 사용 하도록 설정 하는 데 VNet 통합이 사용 하도록 설정 된 경우 연결의 보안을 보장 하기 위해 필요한 인증서 교환이 필요 합니다. 인증서와 함께 네트워크를 설명하는 DNS 구성, 경로 및 다른 비슷한 항목이 있습니다.
인증서 또는 네트워크 정보가 변경되면 "네트워크 동기화"를 클릭해야 합니다. “네트워크 동기화”를 클릭하면 앱과 VNet 간의 연결이 잠시 중단됩니다. 앱이 다시 시작되지는 않지만, 연결 손실로 인해 사이트가 제대로 작동하지 않을 수 있습니다. 

## <a name="accessing-on-premises-resources"></a>온-프레미스 리소스 액세스
앱은 사이트 간 연결이 있는 VNet과 통합되어 온-프레미스 리소스에 액세스할 수 있습니다. 게이트웨이를 사용 하는 데 필요한 VNet 통합을 사용 하는 경우 지점 및 사이트 간 주소 블록을 사용 하 여 온-프레미스 VPN gateway 경로를 업데이트 해야 합니다. 사이트 간 VPN을 처음 설정하는 경우 이를 구성하는 데 사용되는 스크립트에서 경로를 올바르게 설정해야 합니다. 사이트 간 VPN을 만든 후에 지점 및 사이트 간 VPN을 추가하는 경우 해당 경로를 수동으로 업데이트해야 합니다. 이 작업을 수행하는 방법에 대한 세부 정보는 게이트웨이마다 다르며 여기에 설명되어 있지 않습니다. BGP를 사이트 간 VPN 연결로 구성할 수 없습니다.

VNet 및 온-프레미스를 통해 지역 VNet 통합 기능에 도달 하는 데 필요한 추가 구성은 없습니다. Express 경로 또는 사이트 간 VPN을 사용 하 여 VNet을 온-프레미스에 연결 하기만 하면 됩니다. 

> [!NOTE]
> 게이트웨이 필수 VNet 통합 기능은 Express 경로 게이트웨이가 있는 VNet에 앱을 통합 하지 않습니다. Express 경로 게이트웨이가 [공존 모드][VPNERCoex] 에서 구성 된 경우에도 VNet 통합은 작동 하지 않습니다. Express 경로 연결을 통해 리소스에 액세스 해야 하는 경우에는 VNet에서 실행 되는 지역 VNet 통합 기능 또는 [App Service Environment][ASE]를 사용할 수 있습니다. 
> 
> 

## <a name="peering"></a>피어링
지역 VNet 통합을 사용 하 여 피어 링을 사용 하는 경우에는 추가 구성을 수행할 필요가 없습니다. 

피어 링과 VNet 통합이 필요한 게이트웨이를 사용 하는 경우 몇 가지 추가 항목을 구성 해야 합니다. 앱에서 작동하도록 피어링을 구성하려면,

1. 앱이 연결되는 VNet에 피어링 연결을 추가합니다. 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용**을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **게이트 전송 허용**을 선택합니다.
1. 연결된 VNet에 피어링되는 VNet에 피어링 연결을 추가합니다. 대상 VNet에 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용**을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **원격 게이트웨이 허용**을 선택합니다.
1. 포털에서 App Service 계획 > 네트워킹 > VNet 통합 UI로 차례로 이동합니다.  앱이 연결되는 VNet을 선택합니다. 라우팅 섹션 아래에서 앱이 연결되는 VNet과 피어링되는 VNet의 주소 범위를 추가합니다.  


## <a name="pricing-details"></a>가격 정보
지역 VNet 통합 기능에는 ASP 가격 책정 계층 요금 외에 추가 요금이 부과 되지 않습니다.

게이트웨이 필수 VNet 통합 기능 사용에 대한 세 가지 관련 요금이 있습니다.

* ASP 가격 책정 계층 요금-앱이 Standard, Premium 또는 PremiumV2 App Service 계획에 있어야 합니다. [가격 책정][ASPricing]에서 해당 비용에 대한 자세한 내용을 볼 수 있습니다 App Service. 
* 데이터 전송 비용-VNet이 동일한 데이터 센터에 있더라도 데이터 송신 요금이 부과 됩니다. 이러한 요금은 [데이터 전송 가격 정보][DataPricing]에 설명 되어 있습니다. 
* VPN Gateway 비용-지점 및 사이트 간 VPN에 필요한 VNet 게이트웨이의 비용이 있습니다. 세부 정보는 [VPN Gateway 가격 책정][VNETPricing] 페이지에 있습니다.


## <a name="troubleshooting"></a>문제 해결
기능을 설정하기 쉽다고 해서 환경에 문제가 없는 것은 아닙니다. 원하는 엔드포인트에 액세스하다가 문제가 발생하는 경우, 앱 콘솔에서 연결을 테스트하는 데 사용할 수 있는 유틸리티가 있습니다. 사용할 수 있는 두 개의 콘솔이 있습니다. 하나는 Kudu 콘솔이고, 다른 하나는 Azure Portal의 콘솔입니다. 앱에서 Kudu 콘솔에 도달하려면 도구 -> Kudu로 차례로 이동합니다. [Sitename]. Kudo 콘솔에 연결할 수도 있습니다. 웹 사이트가 로드 되 면 디버그 콘솔 탭으로 이동 합니다. Azure Portal 호스트 된 콘솔로 이동 하려면 앱에서 도구-> 콘솔로 이동 합니다. 

#### <a name="tools"></a>도구
**ping**, **nslookup** 및 **tracert** 도구는 보안 제약 조건으로 인해 콘솔을 통해 작동되지 않습니다. 공백을 채우기 위해 별도의 두 가지 도구가 추가되었습니다. DNS 기능을 테스트하기 위해 nameresolver.exe라는 도구가 추가되었습니다. 구문은 다음과 같습니다.

    nameresolver.exe hostname [optional: DNS Server]

**nameresolver**를 사용하여 앱에서 사용하는 호스트 이름을 확인합니다. 이렇게 하면 DNS에 잘못 구성된 항목이 있는지 또는 DNS 서버에 대한 액세스가 없는지를 테스트할 수 있습니다. 환경 변수 WEBSITE_DNS_SERVER 및 WEBSITE_DNS_ALT_SERVER를 살펴보면 앱이 콘솔에서 사용할 DNS 서버를 볼 수 있습니다.

다음 도구는 호스트와 포트에 대한 TCP 연결을 테스트하는 데 사용할 수 있습니다. 이 도구는 **tcpping**이라고 하며, 구문은 다음과 같습니다.

    tcpping.exe hostname [optional: port]

특정 호스트 및 포트에 연결할 수 있으면 **tcpping** 유틸리티에서 이를 알려줍니다. 호스트 및 포트 조합에서 수신 대기하는 애플리케이션이 있고 네트워크를 통해 앱에서 지정된 호스트 및 포트에 액세스할 수 있는 경우에만 성공으로 표시될 수 있습니다.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>VNet에서 호스트되는 리소스에 대한 액세스 디버깅
특정 호스트와 포트에 앱이 도달하지 못하도록 방해할만한 요소는 많이 있습니다. 대부분의 경우 다음 세 가지 중 하나입니다.

* **중간에 방화벽이 있음.** 중간에 방화벽이 있으면 TCP 시간 제한에 도달합니다. 이 경우 TCP 시간 제한은 21초입니다. **tcpping** 도구를 사용하여 연결을 테스트합니다. TCP 시간 초과는 방화벽 뒤에 많은 항목이 있으나 방화벽 뒤에서 시작하기 때문일 수 있습니다. 
* **DNS에 액세스할 수 없음.** DNS 시간 제한은 DNS 서버당 3초입니다. DNS 서버가 2개 있으면 제한 시간은 6초입니다. DNS가 작동하는지 확인하려면 nameresolver를 사용합니다. VNet이 구성된 DNS를 사용하지 않으므로 nslookup을 사용할 수 없음에 주의하세요. 액세스할 수 없는 경우 DNS에 대한 액세스를 차단 하는 방화벽 또는 NSG가 있을 수 있습니다.

이러한 항목으로 인해 문제가 해결 되지 않으면 먼저 다음을 확인 하세요. 

**지역 VNet 통합**
* 대상 사용자가 RFC 1918 주소 인지
* 통합 서브넷에서 송신을 차단 하는 NSG가 있나요?
* Express 경로 또는 VPN을 통해 이동 하는 경우 온-프레미스 게이트웨이는 트래픽을 Azure로 라우팅하도록 구성 되나요? VNet의 엔드포인트에 연결할 수 있지만 온-프레미스에 연결할 수 없는 경우 확인 하는 것이 좋습니다.

**게이트웨이 필요 VNet 통합**
* RFC 1918 범위 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)의 지점 및 사이트 간 주소 범위 입니까?
* 게이트웨이가 포털에서 실행되고 있나요? 게이트웨이가 다운되어 있으면 다시 가동시킵니다.
* 인증서가 동기화 된 것으로 표시 되거나 네트워크 구성이 변경 된 것으로 의심 됩니까?  인증서가 동기화 되지 않은 상태 이거나, Asp와 동기화 되지 않은 VNet 구성의 변경 내용이 있는 것으로 의심 되는 경우 "네트워크 동기화"를 누릅니다.
* Express 경로 또는 VPN을 통해 이동 하는 경우 온-프레미스 게이트웨이는 트래픽을 Azure로 라우팅하도록 구성 되나요? VNet의 엔드포인트에 연결할 수 있지만 온-프레미스에 연결할 수 없는 경우 확인 하는 것이 좋습니다.

네트워킹 문제를 디버깅 하는 것은 특정 호스트에 대한 액세스를 차단 하는 것을 확인할 수 없기 때문입니다. 포트 조합입니다. 여기에 해당하는 경우는 다음과 같습니다.

* 호스트에 방화벽이 실행 중이며 지점과 사이트 간 IP 범위에서 애플리케이션 포트에 대한 액세스를 방해합니다. 보통 교차 서브넷에는 공용 액세스가 필요합니다.
* 대상 호스트가 다운되었습니다.
* 애플리케이션이 다운되었습니다.
* IP 또는 호스트 이름이 잘못되었습니다.
* 애플리케이션이 예상치 않은 다른 포트를 수신 대기 중입니다. 엔드포인트 호스트에서 "netstat -aon"을 사용하여 프로세스 ID를 수신 대기 포트와 일치시킬 수 있습니다. 
* 네트워크 보안 그룹이 지점과 사이트 간 IP 범위에서 애플리케이션 호스트 및 포트에 대한 액세스를 금지하도록 구성되어 있습니다.

앱이 실제로 사용 하는 주소는 알지 못합니다. 통합 서브넷 또는 지점 및 사이트 간 주소 범위의 모든 주소일 수 있으므로 전체 주소 범위에서 액세스를 허용 해야 합니다. 

추가적인 디버그 단계에는 다음 내용이 포함됩니다.

* VNet의 VM에 연결하고 이 위치에서 리소스 호스트:포트에 연결하려고 시도합니다. TCP 액세스를 테스트하려면 **test-netconnection** PowerShell 명령을 사용합니다. 구문은 다음과 같습니다.

      test-netconnection hostname [optional: -Port]

* VM에서 응용 프로그램을 가져오고 **tcpping** 을 사용 하 여 앱에서 해당 호스트 및 포트에 대한 액세스를 테스트 합니다.

#### <a name="on-premises-resources"></a>온-프레미스 리소스 ####

앱에서 온-프레미스 리소스에 연결할 수 없으면 VNet에서 리소스에 연결할 수 있는지 확인합니다. **test-netconnection** PowerShell 명령을 사용하여 TCP 액세스를 확인합니다. VM이 온-프레미스 리소스에 연결할 수 없는 경우 VPN 또는 Express 경로 연결이 제대로 구성 되지 않을 수 있습니다.

VNet 호스팅된 VM에서 온-프레미스 시스템에 연결할 수 있지만 앱에서는 연결할 수 없는 경우 다음과 같은 이유 중 하나가 원인일 수 있습니다.

* 사용자의 경로가 서브넷 또는 온-프레미스 게이트웨이의 지점 및 사이트 간 주소 범위를 사용 하 여 구성 되지 않았습니다.
* 네트워크 보안 그룹에서 지점 및 사이트 간 IP 범위에 대한 액세스를 차단하고 있음
* 온-프레미스 방화벽에서 지점 및 사이트 간 IP 범위의 트래픽을 차단하고 있음
* 지역 VNet 통합 기능을 사용 하 여 비 RFC 1918 주소에 도달 하려고 합니다.


## <a name="powershell-automation"></a>PowerShell 자동화

PowerShell을 사용하여 App Service를 Azure Virtual Network와 통합할 수 있습니다. 실행 준비 스크립트의 경우 [Azure App Service에서 Azure Virtual Network에 앱 연결](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)을 참조하세요.


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
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
