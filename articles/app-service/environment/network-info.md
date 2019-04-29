---
title: App Service Environment에 대한 네트워킹 고려 사항 - Azure
description: ASE 네트워크 트래픽 및 ASE를 사용하여 NSG 및 UDR을 설정하는 방법을 설명합니다.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 73175b326c25d5d9a78155d0d9d888b655da1bfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226811"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>App Service Environment에 대한 네트워킹 고려 사항 #

## <a name="overview"></a>개요 ##

 Azure [App Service Environment][Intro]는 Azure App Service를 Azure VNet(Virtual Network) 내 서브넷에 배포한 것입니다. ASE(App Service Environment)에는 두 가지 배포 유형이 있습니다.

- **외부 ASE**: ASE에서 호스트되는 앱을 인터넷 액세스가 가능한 IP 주소에 표시합니다. 자세한 내용은 [외부 ASE 만들기][MakeExternalASE]를 참조하세요.
- **ILB ASE**: ASE에서 호스트되는 앱을 VNet 내부의 IP 주소에 표시합니다. 내부 엔드포인트는 ILB(내부 부하 분산 장치)이므로 ILB ASE라고 합니다. 자세한 내용은 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

App Service Environment에는 ASEv1 및 ASEv2라는 두 가지 버전이 있습니다. ASEv1에 대한 자세한 내용은 [App Service Environment v1 소개][ASEv1Intro]를 참조하세요. ASEv1은 클래식 또는 Resource Manager VNet에서 배포할 수 있습니다. ASEv2는 Resource Manager VNet에만 배포할 수 있습니다.

인터넷으로 이동하는 ASE의 모든 호출은 VNet을 출발하여 ASE에 할당된 VIP로 갑니다. 이 VIP의 공용 IP는 인터넷으로 이동하는 ASE의 모든 호출에 대한 원본 IP입니다. 사용자의 ASE에 있는 앱이 VNet 또는 VPN의 리소스를 호출하는 경우 원본 IP는 ASE에서 사용되는 서브넷의 IP 중 하나가 됩니다. ASE가 VNet 내에 있으므로 추가 구성 없이 VNet 내 리소스에 액세스할 수 있습니다. VNet이 온-프레미스 네트워크에 연결되어 있으면 ASE의 앱도 추가 구성 없이 해당 네트워크의 리소스에 액세스할 수 있습니다.

![외부 ASE][1] 

외부 ASE가 있는 경우에는 공용 VIP가 다음 항목용으로 ASE 앱을 확인하는 엔드포인트로도 사용됩니다.

* HTTP/S 
* FTP/S 
* 웹 배포
* 원격 디버깅

![ILB ASE][2]

ILB ASE가 있는 경우에는 ILB의 주소가 HTTP/S, FTP/S, 웹 배포 및 원격 디버깅용 엔드포인트입니다.

기본 앱 액세스 포트는 다음과 같습니다.

| 사용 | 원본 | 받는 사람 |
|----------|---------|-------------|
|  HTTP/HTTPS  | 사용자 구성 가능 |  80, 443 |
|  FTP/FTPS    | 사용자 구성 가능 |  21, 990, 10001-10020 |
|  Visual Studio 원격 디버깅  |  사용자 구성 가능 |  4020, 4022, 4024 |
|  웹 서비스 배포 | 사용자 구성 가능 | 8172 |

외부 ASE를 사용하든, ILB ASE를 사용하든 위 표에 나와 있는 포트가 사용됩니다. 외부 ASE를 사용하는 경우 공용 VIP에서 해당 포트에 액세스합니다. ILB ASE를 사용하는 경우에는 ILB에서 해당 포트에 액세스합니다. 포트 443을 잠그면 포털에 표시되는 일부 기능이 영향을 받을 수 있습니다. 자세한 내용은 [포털 종속성](#portaldep)을 참조하세요.

## <a name="ase-subnet-size"></a>ASE 서브넷 크기 ##

ASE를 배포한 후에는 ASE를 호스팅하는 데 사용되는 서브넷의 크기를 변경할 수 없습니다.  ASE는 격리된 각 App Service 계획 인스턴스뿐만 아니라 각 인프라 역할에 대한 주소를 사용합니다.  또한 생성된 모든 서브넷에 대해 Azure Networking에서 사용하는 5개의 주소가 있습니다.  App Service 계획이 아예 없는 ASE는 앱을 만들기 전에 12개의 주소를 사용합니다.  ILB ASE인 경우 해당 ASE에서 앱을 만들기 전에 13개의 주소를 사용합니다. ASE를 규모 확장할 때 인프라 역할은 App Service 계획 인스턴스의 15 및20개의 배수마다 추가됩니다.

   > [!NOTE]
   > ASE 이외의 항목을 서브넷에 포함해서는 안 됩니다. 향후 확장이 가능한 주소 공간을 선택해야 합니다. 이 설정은 나중에 변경할 수 없습니다. 주소 256개를 포함할 수 있는 `/24` 크기를 사용하는 것이 좋습니다.

규모를 확장 또는 축소할 때 적절한 크기의 새 역할이 추가된 다음, 워크로드가 현재 크기에서 대상 크기로 마이그레이션됩니다. 앱이 마이그레이션된 후에만 원래 VM이 제거됩니다. 즉, 100개의 ASP 인스턴스가 있는 ASE가 있는 경우 VM 수를 두 배로 늘려야 하는 기간이 있습니다.  이러한 이유로 필요할 수 있는 모든 변경 사항을 수용하기 위해 '/24'를 사용하는 것이 좋습니다.  

## <a name="ase-dependencies"></a>ASE 종속성 ##

### <a name="ase-inbound-dependencies"></a>ASE 인바운드 종속성 ###

ASE 인바운드 액세스 종속성은 다음과 같습니다.

| 사용 | 원본 | 받는 사람 |
|-----|------|----|
| 관리 | App Service 관리 주소 | ASE 서브넷: 454, 455 |
|  ASE 내부 통신 | ASE 서브넷: 모든 포트 | ASE 서브넷: 모든 포트
|  Azure Load Balancer 인바운드 허용 | Azure Load Balancer | ASE 서브넷: 모든 포트
|  IP 주소가 할당된 앱 | 주소가 할당된 앱 | ASE 서브넷: 모든 포트

인바운드 관리 트래픽은 시스템 모니터링 외에도 ASE의 명령 및 제어를 제공합니다. 이 트래픽의 원본 주소 목록은 [ASE 관리 주소][ASEManagement] 문서에 있습니다. 즉, 네트워크 보안 구성이 포트 454 및 455에서 모든 IP로부터의 액세스를 허용해야 합니다. 해당 주소의 액세스를 차단하면 ASE가 비정상화된 다음, 일시 중단됩니다.

ASE 서브넷 내에는 내부 구성 요소 통신에 사용된 많은 포트가 있으며 변경할 수 있습니다.  이를 위해서는 ASE 서브넷에 있는 모든 포트를 ASE 서브넷에서 액세스할 수 있어야 합니다. 

Azure Load Balancer 및 ASE 서브넷 간의 통신을 위해서는 최소 포트 454, 455 및 16001이 열려 있어야 합니다. 16001 포트는 부하 분산 장치 및 ASE 사이에 활성 트래픽을 유지하는 데 사용됩니다. ILB ASE를 사용하는 경우 454, 455, 16001 포트로만 트래픽을 잠글 수 있습니다.  외부 ASE를 사용하는 경우 정상적인 앱 액세스 포트를 고려해야 합니다.  주소가 할당된 앱을 사용하는 경우 모든 포트에 대해 열려 있어야 합니다.  특정 앱에 주소를 할당하면 부하 분산 장치는 HTTP 및 HTTPS 트래픽을 ASE로 보내기 전에 알려지지 않은 포트를 사용합니다.

IP 주소가 할당된 앱을 사용하는 경우 앱에 할당된 IP에서 ASE 서브넷으로 트래픽을 허용해야 합니다.

포트 454 및 455에서 들어오는 TCP 트래픽은 동일한 VIP에서 되돌아 가야 합니다. 그렇지 않으면 비대칭 라우팅 문제가 발생합니다. 

### <a name="ase-outbound-dependencies"></a>ASE 아웃바운드 종속성 ###

아웃 바운드 액세스의 경우 ASE는 여러 외부 시스템에 종속됩니다. 그러한 시스템 종속성은 대부분 DNS 이름으로 정의되며 고정된 IP 주소 집합에 매핑되지 않습니다. 따라서 ASE는 여러 포트를 통해 ASE 서브넷에서 모든 외부 IP에 아웃바운드로 액세스할 수 있어야 합니다. 

아웃바운드 종속성의 전체 목록은 [App Service 환경 아웃바운드 트래픽 잠금](./firewall-integration.md)을 설명하는 문서에 있습니다. ASE가 해당 종속성에 액세스하지 못하는 경우 작동이 중지됩니다. 이러한 작동 중지 상태가 오랜 기간 지속되면 ASE가 일시 중단됩니다. 

### <a name="customer-dns"></a>고객 DNS ###

VNet이 고객 정의 DNS 서버로 구성된 경우 테넌트 워크로드가 해당 서버를 사용합니다. ASE는 여전히 관리 목적을 위해 Azure DNS와 통신해야 합니다. 

VNet이 VPN 반대쪽의 고객 DNS로 구성된 경우 ASE가 포함된 서브넷에서 DNS 서버에 연결할 수 있어야 합니다.

웹앱에서 해결을 테스트하기 위해 콘솔 명령 *nameresolver*를 사용할 수 있습니다. 앱에 대한 scm 사이트에서 디버그 창으로 이동하거나 포털의 앱으로 이동하고 콘솔을 선택합니다. 셸 프롬프트에서 조회하려는 주소와 함께 *nameresolver* 명령을 실행할 수 있습니다. 얻게 되는 결과는 동일한 조회를 수행하는 동안 앱이 얻는 결과와 동일합니다. nslookup을 사용하는 경우 대신 Azure DNS를 사용하여 조회를 수행합니다.

ASE가 있는 VNet의 DNS 설정을 변경하면 ASE를 재부팅해야 합니다. ASE를 재부팅하지 않으려면 ASE를 만들기 전에 VNet에 대한 DNS 설정을 구성하는 것이 좋습니다.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>포털 종속성 ##

ASE의 기능적 종속성 외에 포털 환경과 관련된 몇 가지 추가 항목이 있습니다. Azure Portal의 기능 중 일부는 _SCM 사이트_에 대한 직접 액세스에 의존합니다. Azure App Service의 모든 앱에는 URL이 두 개 있습니다. 첫 번째 URL은 앱에 액세스하는 것입니다. 두 번째 URL은 _Kudu 콘솔_이라고도 하는 SCM 사이트에 액세스하는 것입니다. SCM 사이트를 사용하는 기능은 다음과 같습니다.

-   웹 작업
-   Functions
-   스트리밍 로그
-   Kudu
-   확장
-   Process Explorer
-   콘솔

ILB ASE를 사용할 때는 VNet 외부에서 인터넷을 통해 SCM 사이트에 액세스할 수 없습니다. ILB ASE에서 앱이 호스트되는 경우 포털에서 일부 기능이 작동하지 않습니다.  

SCM 사이트에 종속된 이러한 기능 중 많은 부분은 Kudu 콘솔에서 직접 사용할 수 있습니다. Portal을 사용하는 대신 콘솔에 직접 연결할 수 있습니다. 앱이 ILB ASE에서 호스트되는 경우에는 게시 자격 증명을 사용하여 로그인합니다. ILB ASE에 호스팅된 앱의 SCM 사이트에 액세스하는 URL은 다음 형식으로 표시됩니다. 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

ILB ASE 도메인 이름이 *contoso.net*이고 앱 이름이 *testapp*이면 *testapp.contoso.net*에서 앱에 연결할 수 있습니다. 그리고 앱과 함께 제공되는 SCM 사이트는 *testapp.scm.contoso.net*에서 연결할 수 있습니다.

## <a name="functions-and-web-jobs"></a>함수 및 웹 작업 ##

함수 및 웹 작업은 SCM 사이트에 따라 다르지만, 브라우저가 SCM 사이트에 연결될 수 있는 한, 앱이 ILB ASE에 있더라도 포털에서 사용할 수 있도록 지원됩니다.  ILB ASE에 자체 서명된 인증서를 사용하는 경우 브라우저에서 해당 인증서를 신뢰하도록 설정해야 합니다.  IE와 Microsoft Edge의 경우 인증서가 컴퓨터의 신뢰 저장소에 있어야 함을 의미합니다.  크롬을 사용하는 경우 브라우저에서 scm 사이트를 이미 직접 방문하여 이전에 인증서를 수락했음을 의미합니다.  가장 좋은 방법은 브라우저의 신뢰 체인에 있는 상용 인증서를 사용하는 것입니다.  

## <a name="ase-ip-addresses"></a>ASE IP 주소 ##

ASE에는 알고 있어야 할 몇 가지 IP 주소가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- **공용 인바운드 IP 주소**: 외부 ASE의 앱 트래픽 및 외부 ASE와 ILB ASE 둘 다의 관리 트래픽에 사용됩니다.
- **아웃바운드 공용 IP**: VNet에서 시작되는 ASE로부터의 아웃바운드 연결(VPN으로 라우팅되지 않음)의 "시작" IP로 사용됩니다.
- **ILB IP 주소**: ILB ASE를 사용하는 경우
- **앱에 할당된 IP 기반 SSL 주소**: 외부 ASE를 사용하며 IP 기반 SSL이 구성되어 있을 때만 사용 가능합니다.

이러한 모든 IP 주소는 ASE UI에서 Azure Portal의 ASEv2에 쉽게 확인할 수 있습니다. ILB ASE를 사용하는 경우 ILB용 IP가 나열됩니다.

   > [!NOTE]
   > 이러한 IP 주소는 ASE가 켜져 있고 실행 중이기만 하다면 변경되지 않습니다.  ASE가 일시 중단되고 복원되면 ASE에서 사용하는 주소가 변경됩니다. ASE가 일시 중단되는 일반적인 원인은 인바운드 관리 액세스를 차단하거나 ASE 종속성에 대한 액세스를 차단하는 경우입니다. 

![IP 주소][3]

### <a name="app-assigned-ip-addresses"></a>앱에 할당된 IP 주소 ###

외부 ASE를 사용하면 IP 주소를 개별 앱에 할당할 수 있습니다. ILB ASE에서는 IP 주소를 할당할 수 없습니다. 앱용으로 고유한 IP 주소를 구성하는 방법에 대한 자세한 내용은 [Azure App Service에 기존 사용자 지정 SSL 인증서 바인딩](../app-service-web-tutorial-custom-ssl.md)을 참조하세요.

앱에 고유한 IP 기반 SSL 주소가 있는 경우 ASE는 해당 IP 주소에 매핑하도록 두 개의 포트를 예약합니다. 한 포트는 HTTP 트래픽용이고, 다른 포트는 HTTPS용입니다. 이러한 포트는 IP 주소 섹션의 ASE UI에 나열됩니다. 트래픽은 VIP에서 해당 포트에 연결할 수 있어야 합니다. 그렇지 않으면 앱에 액세스할 수 없습니다. NSG(네트워크 보안 그룹)를 구성할 때는 이 요구 사항을 고려해야 합니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹 ##

[네트워크 보안 그룹][NSGs]은 VNet 내에서 네트워크 액세스를 제어하는 기능을 제공합니다. 포털을 사용할 때는 모든 항목을 거부하는 최저 우선 순위의 암시적 거부 규칙이 있습니다. 허용 규칙은 사용자가 직접 작성합니다.

ASE에서 사용자는 ASE 자체를 호스팅하는 데 사용된 VM에 대한 액세스 권한이 없습니다. Microsoft에서 관리하는 구독을 통해 이러한 VM에 액세스할 수 있습니다. ASE에서 앱에 대한 액세스를 제한하려는 경우 ASE 서브넷에 대해 NSG를 설정합니다. 이 과정에서 ASE 종속성에 유의하세요. 종속성을 차단하면 ASE 작동이 중지됩니다.

NSG는 Azure Portal 또는 PowerShell을 통해 구성할 수 있습니다. 이 문서에서는 Azure Portal의 정보를 제공합니다. **네트워킹** 아래에서 최상위 리소스로 포털에서 NSG를 만들고 관리합니다.

인바운드 및 아웃바운드 요구 사항을 고려할 때, NSG는 이 예제에 나와 있는 NSG와 비슷하게 표시되어야 합니다. VNet 주소 범위는 _192.168.250.0/23_이고 ASE가 있는 서브넷은 _192.168.251.128/25_입니다.

ASE가 작동하기 위한 가장 우선적인 인바운드 요구 사항 두 가지가 이 예제의 목록 맨 위에 나와 있습니다. 이는 ASE 관리를 가능케 하고 ASE가 자체적으로 통신할 수 있도록 합니다. 다른 항목은 모두 테넌트에서 구성할 수 있으며, ASE에서 호스트되는 애플리케이션에 대한 네트워크 액세스를 관리하는 데 사용할 수 있습니다. 

![인바운드 보안 규칙][4]

기본 규칙을 사용하면 VNet의 IP가 ASE 서브넷과 통신할 수 있습니다. 공용 VIP라고도 하는 부하 분산 장치가 ASE와 통신할 수 있도록 하는 또 다른 기본 규칙도 있습니다. 기본 규칙을 확인하려면 **추가** 아이콘 옆에 있는 **기본 규칙**을 선택합니다. 표시된 NSG 규칙 다음에 기타 모든 항목을 거부하는 규칙을 배치하면 VIP와 ASE 간의 트래픽이 차단됩니다. VNet 내에서 들어오는 트래픽을 차단하려면 인바운드를 허용하는 규칙을 직접 추가합니다. 이때 대상이 **Any**이고 포트 범위가 **\*** 인 AzureLoadBalancer와 같은 원본을 사용합니다. NSG 규칙은 ASE 서브넷에 적용되므로 대상을 구체적으로 지정할 필요는 없습니다.

앱에 IP 주소를 할당한 경우 포트를 열어 두어야 합니다. 포트를 확인하려면 **App Service Environment** > **IP 주소**를 선택합니다.  

다음 아웃바운드 규칙에 표시된 모든 항목이 필요합니다(마지막 항목은 제외). 이러한 항목을 통해 네트워크에서 이 문서 앞부분에서 언급했던 ASE 종속성에 액세스할 수 있습니다. 이러한 항목 중 하나라도 차단하면 ASE의 작동이 중지됩니다. 목록의 마지막 항목은 사용자의 ASE가 VNet의 다른 리소스와 통신할 수 있도록 설정합니다.

![아웃바운드 보안 규칙][5]

NSG를 정의한 후 ASE가 있는 서브넷에 할당합니다. ASE VNet 또는 서브넷을 기억하지 못하는 경우 ASE 포털 페이지에서 확인할 수 있습니다. 서브넷에 NSG를 할당하려면 서브넷 UI로 이동한 다음 NSG를 선택합니다.

## <a name="routes"></a>경로 ##

강제 터널링은 아웃바운드 트래픽이 인터넷으로 직접 가지는 않지만 ExpressRoute 게이트웨이 또는 가상 어플라이언스와 같은 다른 곳으로 이동하도록 VNet에서 경로를 설정하는 경우입니다.  이러한 방식으로 ASE를 구성해야 하는 경우 [강제 터널링을 사용하여 App Service 환경 구성][forcedtunnel]의 문서를 참조하세요.  이 문서에서는 ExpressRoute 및 강제 터널링 작업에 사용할 수 있는 옵션을 알려줍니다.

포털에서 ASE를 만들 때 ASE를 사용하여 만든 서브넷에 경로 테이블의 집합도 만듭니다.  이러한 경로는 단순히 아웃바운드 트래픽을 인터넷에 직접 보내도록 합니다.  
동일한 경로를 수동으로 만들려면 다음 단계를 수행합니다.

1. Azure Portal로 이동합니다. **네트워킹** > **경로 테이블**을 선택합니다.

2. VNet과 동일한 지역에 새 경로 테이블을 만듭니다.

3. 경로 테이블 UI 내에서 **경로** > **추가**를 선택합니다.

4. **다음 홉 형식**을 **인터넷**으로, **주소 접두사**를 **0.0.0.0/0**으로 설정합니다. **저장**을 선택합니다.

    다음과 유사한 출력이 표시됩니다.

    ![기능 경로][6]

5. 새 경로 테이블을 만든 후 ASE가 포함된 서브넷으로 이동합니다. Portal의 목록에서 경로 테이블을 선택합니다. 변경 내용을 저장하면 서브넷이 표시된 경로와 NSG를 확인할 수 있습니다.

    ![NSG 및 경로][7]

## <a name="service-endpoints"></a>서비스 엔드포인트 ##

서비스 엔드포인트를 사용하면 Azure 가상 네트워크 및 서브넷의 집합에 다중 테넌트 서비스에 대한 액세스를 제한할 수 있습니다. 서비스 엔드포인트에 대한 자세한 내용은 [Virtual Network 서비스 엔드포인트][serviceendpoints] 설명서에서 확인할 수 있습니다. 

리소스에서 서비스 엔드포인트를 사용하는 경우 다른 모든 경로에 우선해 만든 경로가 있습니다. 강제 터널링된 ASE를 통해 서비스 엔드포인트를 사용하는 경우 Azure SQL 및 Azure Storage 관리 트래픽은 강제 터널링되지 않습니다. 

Azure SQL 인스턴스를 통해 서브넷에서 서비스 엔드포인트가 사용되는 경우 해당 서브넷에서 연결된 모든 Azure SQL 인스턴스는 서비스 엔드포인트를 사용할 수 있어야 합니다. 동일한 서브넷에서 여러 Azure SQL 인스턴스에 액세스하려는 경우 다른 Azure SQL 인스턴스가 아닌 한 Azure SQL 인스턴스에서 서비스 엔드포인트를 사용할 수 없습니다. Azure Storage는 Azure SQL과 동일하게 동작하지 않습니다. Azure Storage를 통해 서비스 엔드포인트를 사용하는 경우 사용자의 서브넷에서 해당 리소스에 대한 액세스를 잠글 수 있지만 서비스 엔드포인트를 사용할 수 없는 경우에도 여전히 다른 Azure Storage 계정에 액세스할 수 있습니다.  

![서비스 엔드포인트][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
