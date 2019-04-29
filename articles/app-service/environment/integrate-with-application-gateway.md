---
title: ILB App Service Environment와 Application Gateway 통합 - Azure
description: ILB App Service Environment의 앱을 Application Gateway와 통합하는 방법에 대한 연습
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ea46b5e57e4e508a3311de8633ae61d346b574eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764929"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>ILB App Service Environment와 Azure Application Gateway 통합 #

[App Service Environment](./intro.md)는 고객의 Azure 가상 네트워크 서브넷에 Azure App Service를 배포한 환경입니다. 앱 액세스를 위해 공용 또는 개인 엔드포인트로 배포할 수 있습니다. 개인 엔드포인트(즉, 내부 부하 분산 장치)를 사용하여 App Service Environment를 배포하는 것을 ILB App Service Environment라고 합니다.  

웹 애플리케이션 방화벽을 통해 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드 및 애플리케이션 DDoS와 기타 공격을 차단하기 위해 인바운드 웹 트래픽을 검사하여 웹 애플리케이션을 보호할 수 있습니다. 데이터 손실 방지 DLP (Data Loss Prevention)에 대한 백엔드 웹 서버로부터의 응답도 검사합니다. Azure 마켓플레이스에서 WAF 디바이스를 얻을 수 있거나 [Azure Application Gateway][appgw]를 사용할 수 있습니다.

Azure Application Gateway는 7계층 부하 분산, SSL 오프로딩 및 WAF(웹 애플리케이션 방화벽) 보호를 제공하는 가상 어플라이언스입니다. 공용 IP 주소에서 수신 대기하고, 트래픽을 애플리케이션 엔드포인트로 라우팅할 수 있습니다. 여기서는 WAF가 구성된 애플리케이션 게이트웨이를 ILB App Service Environment의 앱과 통합하는 방법에 대해 설명합니다.  

애플리케이션 게이트웨이와 ILB App Service Environment의 통합은 앱 수준입니다. ILB App Service Environment에서 애플리케이션 게이트웨이를 구성하는 경우 ILB App Service Environment의 특정 앱에 대해 이를 구성합니다. 이 기술을 사용하면 단일 ILB App Service Environment에서 안전한 다중 테넌트 애플리케이션을 호스트할 수 있습니다.  

![ILB App Service Environment의 앱을 가리키는 애플리케이션 게이트웨이][1]

이 연습에서는 다음을 수행합니다.

* Azure Application Gateway를 만듭니다.
* ILB App Service Environment의 앱을 가리키도록 Application Gateway를 구성합니다.
* 사용자 지정 도메인 이름을 적용하도록 앱을 구성합니다.
* 애플리케이션 게이트웨이를 가리키는 공용 DNS 호스트 이름을 편집합니다.

## <a name="prerequisites"></a>필수 조건

Application Gateway를 ILB App Service Environment와 통합하려면 다음이 필요합니다.

* ILB App Service Environment
* ILB App Service Environment에서 실행되는 앱
* ILB App Service Environment의 앱에서 사용할 인터넷 라우팅 가능 도메인 이름
* ILB App Service Environment에서 사용하는 ILB 주소 이 정보는 App Service Environment 포털의 **설정** > **IP 주소** 아래에 있습니다.

    ![ILB App Service Environment에서 사용하는 IP 주소의 예제 목록][9]
    
* 나중에 Application Gateway를 가리키는 데 사용되는 공용 DNS 이름 

ILB App Service Environment를 만드는 방법에 대한 자세한 내용은 [ILB App Service Environment 만들기 및 사용][ilbase]을 참조하세요.

이 문서에서는 App Service Environment가 배포되는 동일한 Azure Virtual Network에 Application Gateway를 배치하려고 한다고 가정합니다. Application Gateway 만들기를 시작하기 전에 게이트웨이를 호스트하는 데 사용할 서브넷을 선택하거나 만듭니다. 

GatewaySubnet이라는 이름이 아닌 서브넷을 사용해야 합니다. GatewaySubnet에 Application Gateway를 배치하면 나중에 Virtual Network Gateway를 만들 수 없습니다. 

또한 ILB App Service Environment가 사용하는 서브넷에 게이트웨이를 배치할 수도 없습니다. App Service Environment만 이 서브넷에 있을 수 있습니다.

## <a name="configuration-steps"></a>구성 단계 ##

1. Azure Portal에서 **새로 만들기** > **네트워크** > **Application Gateway**로 이동합니다.

2. **기본 사항** 영역에서 다음을 수행합니다.

   a. **이름**에 Application Gateway의 이름을 입력합니다.

   b. **계층**에 대해 **WAF**를 선택합니다.

   다. **구독**에 대해 App Service Environment 가상 네트워크에서 사용하는 동일한 구독을 선택합니다.

   d. **리소스 그룹**에 대해 리소스 그룹을 만들거나 선택합니다.

   e. **위치**에 대해 App Service Environment 가상 네트워크의 위치를 선택합니다.

   ![새 Application Gateway 만들기 기본 사항][2]

3. **설정** 영역에서 다음을 수행합니다.

   a. **가상 네트워크**에 대해 App Service Environment 가상 네트워크를 선택합니다.

   b. **서브넷**에 대해 Application Gateway를 배포해야 하는 서브넷을 선택합니다. VPN Gateway를 만들 수 없게 하므로 GatewaySubnet은 사용하지 마세요.

   다. **IP 주소 유형**으로 **공용**을 선택합니다.

   d. **공용 IP 주소**에 대해 공용 IP 주소를 선택합니다. 공용 IP 주소가 없으면 하나 만듭니다.

   e. **프로토콜**로 **HTTP** 또는 **HTTPS**를 선택합니다. HTTPS를 구성하려면 PFX 인증서를 제공해야 합니다.

   f. **웹 애플리케이션 방화벽**에 대해 방화벽을 사용하도록 설정하고, **검색** 또는 **방지**에 대해서도 적합하게 설정할 수 있습니다.

   ![새 Application Gateway 만들기 설정][3]
    
4. **요약** 섹션에서 설정을 검토하고 **확인**을 선택합니다. Application Gateway에서 설정을 완료하는 데 30분 정도 걸릴 수 있습니다.  

5. Application Gateway에서 설정을 완료하면 Application Gateway 포털로 이동합니다. **백 엔드 풀**을 선택합니다. ILB App Service Environment에 대한 ILB 주소를 추가합니다.

   ![백 엔드 풀 구성][4]

6. 백 엔드 풀 구성 프로세스가 완료되면 **상태 프로브**를 선택합니다. 앱에 사용하려는 도메인 이름에 대한 상태 프로브를 만듭니다. 

   ![상태 프로브 구성][5]
    
7. 상태 프로브 구성 프로세스가 완료되면 **HTTP 설정**을 선택합니다. 기존 설정을 편집하고, **사용자 지정 프로브 사용**을 선택한 후 구성한 프로브를 선택합니다.

   ![HTTP 설정 구성][6]
    
8. Application Gateway의 **개요** 섹션으로 이동한 후 Application Gateway에서 사용하는 공용 IP 주소를 복사합니다. 해당 IP 주소를 앱 도메인 이름에 대한 A 레코드로 설정하거나 CNAME 레코드에서 해당 주소의 DNS 이름을 사용합니다. 공용 IP 주소를 선택하고, Application Gateway **개요** 섹션의 링크에서 복사하는 대신 공용 IP 주소 UI에서 복사하는 것이 더 쉽습니다. 

   ![Application Gateway 포털][7]

9. ILB App Service Environment에서 앱에 대한 사용자 지정 도메인 이름을 설정합니다. 포털에서 앱으로 이동하고 **설정** 아래에서 **사용자 지정 도메인**을 선택합니다.

   ![앱에 사용자 지정 도메인 이름 설정][8]

웹앱에 대한 사용자 지정 도메인 이름을 설정하는 방법은 [웹앱에 대한 사용자 지정 도메인 이름 설정][custom-domain] 문서에 있습니다. 그렇지만 ILB App Service Environment의 앱에서는 도메인 이름에 대한 유효성 검사가 없습니다. 앱 엔드포인트를 관리하는 DNS를 소유하고 있으므로 원하는 항목을 여기에 배치할 수 있기 때문입니다. 이 경우에 추가하는 사용자 지정 도메인 이름은 DNS에 포함될 필요는 없지만 앱으로 구성해야 합니다. 

설정이 완료되고 DNS 변경 내용을 전파하는 데 약간의 시간이 걸리면 만든 사용자 지정 도메인 이름을 사용하여 앱에 액세스할 수 있습니다. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
