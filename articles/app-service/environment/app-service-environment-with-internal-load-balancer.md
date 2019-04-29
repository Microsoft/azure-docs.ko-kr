---
title: App Service 환경에서 내부 부하 분산 장치 만들기 및 사용 - Azure | Microsoft Docs
description: ILB를 사용하여 ASE 만들기 및 사용
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 88f100bc780d8df0202cfcce9b390085a71fc905
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130605"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>App Service Environment에서 내부 부하 분산 장치 사용

> [!NOTE] 
> 이 문서는 ASE(App Service Environment) v1에 관한 내용입니다. 사용하기가 더 쉽고 더 강력한 인프라에서 실행되는 최신 버전의 App Service Environment가 있습니다. 새 버전에 대한 자세한 내용은 [App Service Environment 소개](intro.md)를 참조하세요.
>

ASE(App Service Environment) 기능은 다중 테넌트 증명(stamp)에서 사용할 수 없는 향상된 구성 기능을 제공하는 Azure App Service의 프리미엄 서비스 옵션입니다. ASE 기능은 기본적으로 Azure Virtual Network(VNet)에 Azure App Service를 배포합니다. App Service 환경에서 제공되는 기능에 대한 자세한 내용은 [App Service 환경 정의][WhatisASE] 설명서를 참조하세요. VNet에서 작업할 때의 이점을 잘 모르는 경우 [Azure Virtual Network FAQ][virtualnetwork]를 읽어보세요. 

## <a name="overview"></a>개요
ASE는 VNet의 인터넷 액세스 가능 엔드포인트 또는 IP 주소를 사용하여 배포할 수 있습니다. IP 주소를 VNet 주소로 설정하기 위해서는 ILB(내부 부하 분산 장치)를 사용하여 ASE를 배포해야 합니다. ASE가 ILB로 구성된 경우 다음을 제공합니다.

* 고유한 도메인 또는 하위 도메인. 작업을 용이하게 하기 위해 이 문서에서는 하위 도메인을 가정하지만, 두 가지 모두 구성 가능합니다. 
* HTTPS에 사용되는 인증서
* 하위 도메인에 대한 DNS 관리 

결과적으로 다음과 같은 작업을 수행할 수 있습니다.

* 사이트 간 또는 ExpressRoute VPN을 통해 액세스하는 클라우드에서 인트라넷 애플리케이션(예: LOB(기간 업무) 애플리케이션)을 안전하게 호스트
* 공용 DNS 서버에 나열되지 않은 클라우드에 앱 호스트
* 프런트 엔드 앱이 안전하게 통합될 수 있는 인터넷 격리 백 엔드 앱 만들기

#### <a name="disabled-functionality"></a>사용되지 않도록 설정된 기능
ILB ASE를 사용하는 경우 수행할 수 없는 작업도 있습니다. 여기에는 다음이 포함됩니다.

* IPSSL 사용
* 특정 앱에 IP 주소 할당
* 포털을 통해 인증서를 구매하여 앱에 사용 물론 Azure 포털을 통해서가 아니라 인증 기관에서 직접 인증서를 구한 후 앱에 사용할 수도 있습니다.

## <a name="creating-an-ilb-ase"></a>ILB ASE 만들기
ILB ASE를 만드는 과정은 일반적으로 ASE를 만드는 과정과 크게 다르지 않습니다. ASE를 만드는 방법에 대한 자세한 내용은 [App Service 환경을 만드는 방법][HowtoCreateASE]을 참조하세요. ILB ASE를 만드는 프로세스를 ASE 생성 중에 VNet을 만드는 경우와 기존 VNet을 선택하는 경우에서 동일합니다. ILB ASE를 만들려면 

1. Azure Portal에서 **리소스 만들기 -> 웹 + 모바일 -> App Service Environment**를 선택합니다.
2. 구독을 선택합니다.
3. 리소스 그룹을 선택하거나 만듭니다.
4. VNet을 선택하거나 만듭니다.
5. VNet을 선택하는 경우 서브넷 만듭니다.
6. **Virtual Network/위치 -> VNet 구성**을 선택하고 VIP 유형을 내부로 설정합니다.
7. 하위 도메인 이름을 제공합니다. (이 이름은 해당 ASE에서 만든 앱에 사용되는 하위 도메인입니다.)
8. **확인** 및 **만들기**를 차례로 선택합니다.

![][1]

Virtual Network 창에 외부 VIP 또는 내부 VIP를 선택할 수 있는 VNet 구성 옵션이 있습니다. 기본값은 외부입니다. 외부로 설정하는 경우 ASE는 인터넷 액세스 가능 VIP를 사용합니다. 내부를 선택하는 경우 ASE가 VNet 내의 IP 주소에 있는 ILB로 구성됩니다. 

내부를 선택하면 ASE에 더 많은 IP 주소를 추가하는 기능이 제거되고, 대신 ASE의 하위 도메인을 제공해야 합니다. 외부 VIP가 있는 ASE에서 ASE의 이름은 해당 ASE에서 만든 앱에 대한 하위 도메인에 사용됩니다. ASE가 ***contosotest***로 지칭되고 해당 ASE의 앱이 ***mytest***로 지칭되면 하위 도메인 형식은 ***contosotest.p.azurewebsites.net***이고 해당 앱의 URL은 ***mytest.contosotest.p.azurewebsites.net***이 됩니다. VIP 유형을 내부로 설정한 경우 ASE 이름은 해당 ASE의 하위 도메인에서 사용되지 않습니다. 하위 도메인을 명시적으로 지정해야 합니다. 하위 도메인이 ***contoso.corp.net***이고 해당 ASE에서 ***timereporting***이라는 앱을 만들었으면 해당 앱의 URL은 ***timereporting.contoso.corp.net***이 됩니다.

## <a name="apps-in-an-ilb-ase"></a>ILB ASE의 앱
ILB ASE에서 앱을 만드는 과정은 일반적으로 ASE에서 앱을 만드는 과정과 같습니다. 

1. Azure Portal에서 **리소스 만들기 -> 웹 + 모바일 -> 웹** 또는 **모바일** 또는 **API 앱**을 선택합니다.
2. 앱의 이름을 입력합니다.
3. 구독을 선택합니다.
4. 리소스 그룹을 선택하거나 만듭니다.
5. App Service 계획(ASP)을 선택하거나 만듭니다. 새 ASE를 만드는 경우 위치로 ASE를 선택하고 ASP를 생성할 작업자 풀을 선택합니다. ASP를 만들 때 위치 및 작업자 풀로 ASE를 선택합니다. 앱의 이름을 지정하면 앱 이름 아래의 하위 도메인이 ASE에 대한 하위 도메인으로 바뀌는 것을 볼 수 있습니다. 
6. **만들기**를 선택합니다. 앱을 대시보드에 표시하려면 **대시보드에 고정** 확인란을 선택해야 합니다. 

![][2]

앱 이름 아래의 하위 도메인 이름이 ASE의 하위 도메인을 반영하도록 업데이트됩니다. 

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 만들기 유효성 검사 게시
ILB ASE는 비 ILB ASE와는 약간 다릅니다. 앞서 명시한 것처럼 사용자 고유의 DNS를 관리해야 하므로 HTTPS 연결에 대 한 사용자 고유의 인증서를 제공해야 합니다. 

ASE를 만든 후에는 해당 하위 도메인이 사용자가 지정한 하위 도메인을 표시하며, **설정** 메뉴에는 **ILB 인증서**라는 새 항목이 나타납니다. ASE는 HTTPS를 쉽게 테스트하는 자체 서명된 인증서로 만들어집니다. 포털에서는 HTTPS에 고유한 인증서를 제공해야 한다고 지시할 수 있지만 이로 인해 하위 도메인으로 사용하는 인증서를 만들게 됩니다. 

![][3]

인증서 만드는 방법을 잘 모르고 시험적으로 수행해보려는 경우 IIS MMC 콘솔 애플리케이션을 사용하여 자체 서명된 인증서를 만들 수 있습니다. 이 인증서가 생성되면 .pfx 파일로 내보낸 다음, ILB 인증서 UI에 업로드할 수 있습니다. 자체 서명된 인증서로 보안이 유지된 사이트에 액세스하면 인증서 유효성을 검사할 수 없으므로 액세스하려는 사이트가 안전하지 않다는 경고가 브라우저에 표시됩니다. 해당 경고가 발생하지 않도록 하려면 하위 도메인과 일치하고, 브라우저에서 인식할 수 있는 신뢰 체인을 포함하는 적절히 서명된 인증서가 필요합니다.

![][6]

고유한 인증서를 사용하여 흐름을 시도하고 ASE에 대한 HTTP 및 HTTPS 액세스를 테스트하려면:

1. ASE를 만든 후에 ASE UI, **ASE -> 설정 -> ILB 인증서**로 이동합니다.
2. 인증서 pfx 파일을 선택하여 ILB 인증서를 설정하고 암호를 제공합니다. 이 단계를 처리하는 데 잠시 시간이 소요되며, 크기 조정 작업이 진행 중이라는 메시지가 표시됩니다.
3. ASE의 ILB 주소를 가져옵니다(**ASE -> 속성 -> 가상 IP 주소**).
4. ASE를 만든 후에 ASE에서 웹앱을 만듭니다. 
5. 해당 VNET에 없는 경우 VM을 만듭니다(ASE가 있는 동일한 서브넷이 아니거나 연결이 끊긴 경우).
6. 하위 도메인에 대한 DNS를 설정합니다. DNS에 하위 도메인과 와일드카드를 사용할 수도 있고, 몇 가지 간단한 테스트를 수행하려는 경우 VM의 호스트 파일을 편집하여 웹앱 이름을 VIP IP 주소로 설정할 수 있습니다. ASE에 .ilbase.com이라는 하위 도메인이 있고 웹앱 mytestapp을 만들었으면 mytestapp.ilbase.com에서 주소가 지정되고 해당 사항이 호스트 파일에 설정됩니다. (Windows에서 호스트 파일은 C:\Windows\System32\drivers\etc에 있습니다\).)
7. 해당 VM의 브라우저를 사용하여 https://mytestapp.ilbase.com(또는 하위 도메인이 포함된 웹앱 이름)으로 이동합니다.
8. 해당 VM에서 브라우저를 사용하여 https://mytestapp.ilbase.com로 이동합니다. 자체 서명된 인증서를 사용하는 경우 보안 부족에 동의해야 합니다. 

ILB에 대한 IP 주소는 속성에 가상 IP 주소로 표시됩니다.

![][4]

## <a name="using-an-ilb-ase"></a>ILB ASE 사용
#### <a name="network-security-groups"></a>네트워크 보안 그룹
ILB ASE는 앱에 대한 네트워크 격리를 설정합니다. 해당 앱은 액세스할 수 없거나 인터넷으로도 알려지지 않았습니다. 이러한 기능은 LOB(기간 업무) 애플리케이션과 같은 인트라넷 사이트를 호스트하는 데 아주 적합합니다. 액세스를 좀 더 제한해야 할 경우, NSG(네트워크 보안 그룹)를 사용하여 네트워크 수준에서 액세스를 제어할 수도 있습니다. 

NSG를 사용하여 액세스를 좀 더 제한하려는 경우 ASE의 작동에 필요한 통신이 끊어지지 않도록 해야 합니다. HTTP/HTTPS 액세스가 ASE에 사용되는 ILB를 통해서만 진행되더라도 ASE에는 VNet 외부의 리소스가 필요합니다. 여전히 필요한 네트워크 액세스를 확인하려면 [App Service 환경에 대한 인바운드 트래픽 제어][ControlInbound] 및 [ExpressRoute를 사용하는 App Service 환경에 대한 네트워크 구성 세부 정보][ExpressRoute]를 참조하세요. 

ASE를 구성하려면 Azure에서 ASE 관리를 위해 사용하는 IP 주소를 알아야 합니다. 인터넷 요청도 수행하는 경우 해당 IP 주소는 ASE의 아웃바운드 IP 주소입니다. ASE에 대한 아웃바운드 IP 주소는 ASE 수명 동안 정적 상태로 유지됩니다. ASE를 삭제하고 다시 만들면 새 IP 주소를 얻게 됩니다. 이 IP 주소를 찾으려면 **설정 -> 속성**으로 이동한 후 **아웃바운드 IP 주소**를 찾습니다. 

![][5]

#### <a name="general-ilb-ase-management"></a>일반 ILB ASE 관리
대체적으로 ILB ASE 관리는 일반적인 ASE 관리와 동일합니다. 추가 ASP 인스턴스를 호스트하려면 작업자 풀을 확장해야 하고, 증가하는 HTTP/HTTPS 트래픽 양을 처리하려면 프런트 엔드 서버를 확장해야 합니다. ASE의 구성 관리에 대한 일반적인 내용을 보려면 [App Service 환경 구성][ASEConfig]을 참조하세요. 

추가 관리 항목은 인증서 관리 및 DNS 관리입니다. ILB ASE를 만든 후에 HTTPS에 사용되는 인증서를 구하여 업로드하고 만료되기 전에 교체해야 합니다. Azure는 기본 도메인을 소유하므로 ASE에 대한 인증서에 외부 VIP를 제공할 수 있습니다. ILB ASE에 사용되는 하위 도메인에는 제한이 없으므로 HTTPS에 대한 자체 인증서를 제공해야 합니다. 

#### <a name="dns-configuration"></a>DNS 구성
외부 VIP를 사용하는 경우 DNS가 Azure에서 관리됩니다. ASE에서 만든 모든 앱은 공용 DNS에 해당하는 Azure DNS에 자동으로 추가됩니다. ILB ASE에서 자체 DNS를 관리해야 합니다. contoso.corp.net과 같은 특정 하위 도메인의 경우 다음에 대해 ILB 주소를 가리키는 DNS A 레코드를 만들어야 합니다.

    * 
    *.scm ftp 게시 


## <a name="getting-started"></a>시작
App Service 환경을 시작하려면 [App Service 환경 소개][WhatisASE]를 참조하세요.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
