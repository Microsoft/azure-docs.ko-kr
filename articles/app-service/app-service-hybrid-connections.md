---
title: 하이브리드 연결
description: Azure App Service에서 하이브리드 연결을 만들고 사용 하 여 서로 다른 네트워크의 리소스에 액세스 하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: fasttrack-edit
ms.openlocfilehash: ffc5ee32541cfbbda2ae54fd229c1436f133d730
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671518"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service 하이브리드 연결 #

하이브리드 연결은 Azure의 서비스인 동시에 Azure App Service의 기능입니다. 서비스로서, App Service에서 사용되는 것 이상의 기능과 용도가 있습니다. 하이브리드 연결 및 App Service 외부에서의 사용에 대한 자세한 내용은 [Azure Relay 하이브리드 연결][HCService]를 참조 하세요.

App Service 내에서 하이브리드 연결은 다른 네트워크의 애플리케이션 리소스에 액세스하는 데 사용될 수 있습니다. 이를 통해 앱에서 애플리케이션 엔드포인트에 액세스할 수 있습니다. 애플리케이션에 액세스하는 대체 기능으로는 사용되지 않습니다. App Service에서 사용되는 것처럼 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합에 상호 연결됩니다. 즉, TCP 수신 대기 포트에 액세스하는 한, 하이브리드 연결 엔드포인트는 모든 운영 체제 및 모든 애플리케이션에 있을 수 있습니다. 하이브리드 연결 기능은 애플리케이션 프로토콜이 무엇인지 또는 사용자가 무엇에 액세스하고 있는지 인식하거나 상관하지 않습니다. 단지 네트워크 액세스를 제공합니다.  


## <a name="how-it-works"></a>작동 원리 ##
하이브리드 연결 기능은 Azure Service Bus Relay에 대한 두 개의 아웃바운드 호출로 구성됩니다. 앱이 App Service에서 실행 중인 호스트의 라이브러리와 연결되어 있습니다. HCM(하이브리드 연결 관리자)과 Service Bus Relay 간에도 연결이 있습니다. HCM은 액세스하려는 리소스를 호스팅하는 네트워크 내에서 배포하는 릴레이 서비스입니다. 

앱에는 이 두 개의 결합된 연결을 통해 HCM의 다른 쪽에 고정 호스트:포트 조합에 대한 TCP 터널이 포함됩니다. 연결은 인증 및 권한 부여를 위한 보안 및 SAS(공유 액세스 서명) 키에 TLS 1.2를 사용합니다.    

![하이브리드 연결 대략적인 흐름의 다이어그램][1]

앱이 구성된 하이브리드 연결 엔드포인트와 일치하는 DNS 요청을 실행하면 아웃바운드 TCP 트래픽이 하이브리드 연결을 통해 리디렉션됩니다.  

> [!NOTE]
> 이는 항상 하이브리드 연결에 DNS 이름을 사용해야 함을 의미합니다. 일부 클라이언트 소프트웨어는 엔드포인트에서 IP 주소를 사용하는 경우 DNS 조회를 수행하지 않습니다. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service 하이브리드 연결의 장점 ###

하이브리드 연결 기능에는 다음과 같은 많은 장점이 있습니다.

- 앱이 온-프레미스 시스템 및 서비스에 안전하게 액세스할 수 있습니다.
- 이 기능에는 인터넷에 액세스할 수 있는 엔드포인트가 필요하지 않습니다.
- 빠르고 쉽게 설정할 수 있습니다. 
- 각 하이브리드 연결이 단일 호스트:포트 조합과 일치하므로 보안에 도움이 됩니다.
- 일반적으로 방화벽 구멍이 필요하지 않습니다. 연결은 모두 표준 웹 포트를 통한 아웃바운드입니다.
- 기능이 네트워크 수준이므로 이는 앱에서 사용되는 언어 및 엔드포인트에서 사용되는 기술과 관계없음을 의미합니다.
- 단일 앱에서 여러 네트워크에 액세스하는 데 사용될 수 있습니다. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>하이브리드 연결로 할 수 없는 작업 ###

하이브리드 연결로 수행할 수 없는 작업에는 다음이 포함됩니다.

- 드라이브를 탑재합니다.
- UDP를 사용합니다.
- FTP 수동 모드 또는 확장 수동 모드 같은 동적 포트를 사용하는 TCP 기반 서비스에 액세스합니다.
- UDP가 필요할 수 있으므로 LDAP를 지원합니다.
- App Service 작업자를 도메인에 가입할 수 없으므로 Active Directory를 지원합니다.

### <a name="prerequisites"></a>전제 조건 ###
 - Windows App service가 필요 합니다. Windows 에서만 사용할 수 있습니다.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>앱에 하이브리드 연결 추가 및 만들기 ##

하이브리드 연결을 만들려면 [Azure Portal][portal] 로 이동 하 여 앱을 선택 합니다. **네트워킹** > **하이브리드 연결 엔드포인트 구성**을 선택합니다. 여기에서 앱에 구성된 하이브리드 연결을 볼 수 있습니다.  

![하이브리드 연결 목록 스크린샷][2]

새 하이브리드 연결을 추가하려면 **[+]하이브리드 연결 추가**를 선택합니다.  이미 만든 하이브리드 연결 목록이 표시됩니다. 앱에 하나 이상을 추가하려면 원하는 항목을 선택한 다음 **선택한 하이브리드 연결 추가**를 선택합니다.  

![하이브리드 연결 포털 스크린샷][3]

새 하이브리드 연결을 만들려면 **새 하이브리드 연결 만들기**를 선택합니다. 다음을 지정합니다. 

- 하이브리드 연결 이름
- 엔드포인트 호스트 이름
- 엔드포인트 포트
- 사용할 Service Bus 네임스페이스

![새 하이브리드 연결 만들기 대화 상자 스크린샷][4]

모든 하이브리드 연결은 Service Bus 네임스페이스에 연결되고 각 Service Bus 네임스페이스는 Azure 지역에 있습니다. 네트워크로 인한 대기 시간을 피하려면 앱과 같은 지역에서 Service Bus 네임스페이스 사용을 시도하는 것이 중요합니다.

앱에서 하이브리드 연결을 제거하려면 해당 항목을 마우스 오른쪽 단추로 클릭하고 **연결 끊기**를 선택합니다.  

하이브리드 연결이 앱에 추가되면 간단히 해당 항목을 선택하여 세부 정보를 볼 수 있습니다. 

![하이브리드 연결 세부 정보 스크린샷][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Azure Relay 포털에서 하이브리드 연결 만들기 ###

앱 내의 포털 환경 외에도 Azure Relay 포털에서 하이브리드 연결을 만들 수 있습니다. 하이브리드 연결을 App Service에서 사용하려면:

* 클라이언트 권한 부여가 필요합니다.
* 호스트:포트 조합을 값으로 포함하는 endpoint라는 메타데이터 항목이 있어야 합니다.

## <a name="hybrid-connections-and-app-service-plans"></a>하이브리드 연결 및 App Service 계획 ##

App Service 하이브리드 연결은 기본, 표준, 프리미엄 및 격리 요금제 SKU에서만 사용할 수 있습니다. 요금제에는 연결된 제한이 있습니다.  

| 요금제 | 요금제에서 사용 가능한 하이브리드 연결 수 |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| 격리 | 200 |

App Service 계획 UI는 어떤 앱에서 얼마나 많은 하이브리드 연결을 사용하고 있는지 보여줍니다.  

![App Service 계획 속성 스크린샷][6]

세부 정보를 확인할 하이브리드 연결을 선택합니다. 앱 보기에서 확인한 모든 정보를 볼 수 있습니다. 동일한 계획에서 해당 하이브리드 연결을 사용하고 있는 다른 앱의 수도 확인할 수 있습니다.

App Service 계획에 사용할 수 있는 하이브리드 연결 엔드포인트 수에는 제한이 있습니다. 하지만 사용된 각 하이브리드 연결은 해당 계획의 여러 앱에서 사용할 수 있습니다. 예를 들어 App Service 계획에서 5개의 개별 앱에 사용되는 단일 하이브리드 연결은 하나의 하이브리드 연결로 간주됩니다.

### <a name="pricing"></a>가격 ###

App Service 계획 SKU 요구 사항을 충족해야 할 뿐만 아니라 하이브리드 연결을 사용할 때는 추가 비용도 발생합니다. 하이브리드 연결에서 사용되는 각 수신기에 대해 요금이 부과됩니다. 수신기는 하이브리드 연결 관리자입니다. 두 개의 하이브리드 연결 관리자가 5개의 하이브리드 연결을 지원하는 경우 수신기는 10개가 됩니다. 자세한 내용은 [Service Bus 가격][sbpricing]을 참조하세요.

## <a name="hybrid-connection-manager"></a>하이브리드 연결 관리자 ##

하이브리드 연결 기능에는 하이브리드 연결 엔드포인트를 호스트하는 네트워크의 릴레이 에이전트가 필요합니다. 이 릴레이 에이전트를 HCM(하이브리드 연결 관리자)이라고 합니다. HCM을 다운로드 하려면 [Azure Portal][portal]의 앱에서 **네트워킹** > 선택 하 여 **하이브리드 연결 엔드포인트을 구성**합니다.  

이 도구는 Windows Server 2012 이상에서 실행됩니다. HCM은 서비스로 실행되며, 포트 443에서 Azure Relay에 아웃바운드로 연결됩니다.  

HCM을 설치한 후 HybridConnectionManagerUi.exe를 실행하여 도구의 UI를 사용할 수 있습니다. 이 파일은 하이브리드 연결 관리자 설치 디렉터리에 있습니다. Windows 10에서는 검색 상자에서 *하이브리드 연결 관리자 UI*를 검색할 수도 있습니다.  

![하이브리드 연결 관리자 스크린샷][7]

HCM UI를 시작하면 가장 먼저 이 HCM 인스턴스로 구성된 하이브리드 연결을 모두 나열한 테이블이 표시됩니다. 변경하려는 경우 먼저 Azure로 인증합니다. 

HCM에 하나 이상의 하이브리드 연결을 추가하려면 다음을 수행합니다.

1. HCM UI를 시작합니다.
2. **Configure another Hybrid Connection**(다른 하이브리드 연결 구성)을 클릭합니다.
![새 하이브리드 연결 구성 스크린샷][8]

1. Azure 계정으로 로그인 하 여 구독에 사용할 수 있는 하이브리드 연결을 받으세요. HCM은 계속 해 서 Azure 계정을 사용 하지 않습니다. 
1. 구독을 선택합니다.
1. HCM이 릴레이할 하이브리드 연결을 선택합니다.
![하이브리드 연결 스크린샷][9]

1. **저장**을 선택합니다.

이제 추가한 하이브리드 연결을 볼 수 있습니다. 구성된 하이브리드 연결을 선택하여 세부 정보를 볼 수도 있습니다.

![하이브리드 연결 세부 정보 스크린샷][10]

구성된 하이브리드 연결을 지원하려면 HCM에 다음이 필요합니다.

- 포트 443을 통한 Azure에 대한 TCP 액세스
- 하이브리드 연결 엔드포인트에 대한 TCP 액세스
- 엔드포인트 호스트 및 Service Bus 네임스페이스에서 DNS 조회를 수행하는 기능

> [!NOTE]
> Azure Relay는 연결을 위해 웹 소켓을 사용합니다. 이 기능은 Windows Server 2012 이상에서만 사용할 수 있습니다. 따라서 HCM은 Windows Server 2012 이전 버전에서 지원되지 않습니다.
>

### <a name="redundancy"></a>중복성 ###

각 HCM은 여러 하이브리드 연결을 지원할 수 있습니다. 특정 하이브리드 연결이 여러 HCM에서 지원될 수도 있습니다. 기본 동작은 특정 엔드포인트에 대해 구성된 HCM을 통해 트래픽을 라우팅하는 것입니다. 네트워크에서 하이브리드 연결에 대한 고가용성이 필요한 경우에는 개별 컴퓨터에서 여러 HCM을 실행합니다. Relay 서비스가 HCM에 트래픽을 분산하는 데 사용하는 부하 분산 알고리즘은 임의 할당입니다. 

### <a name="manually-add-a-hybrid-connection"></a>수동으로 하이브리드 연결 추가 ###

구독 외부에서 사용자가 특정 하이브리드 연결에 대한 HCM 인스턴스를 호스트할 수 있도록 하려면 해당 하이브리드 연결에 대한 게이트웨이 연결 문자열을 공유합니다. [Azure Portal][portal]하이브리드 연결 속성에서 게이트웨이 연결 문자열을 볼 수 있습니다. 이 문자열을 사용하려면 HCM에서 **수동으로 입력**을 선택하고 게이트웨이 연결 문자열에 붙여넣습니다.

![수동으로 하이브리드 연결 추가][11]

### <a name="upgrade"></a>업그레이드 ###

문제를 해결하거나 개선 기능을 제공하기 위해 하이브리드 연결 관리자가 정기적으로 업데이트됩니다. 업그레이드가 릴리스되면 HCM UI에 팝업이 표시됩니다. 업그레이드를 적용하면 변경 내용이 적용되고 HCM이 다시 시작됩니다. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>프로그래밍 방식으로 앱에 하이브리드 연결 추가 ##

아래 설명된 API를 사용하여 앱에 연결된 하이브리드 연결을 직접 관리할 수 있습니다. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

하이브리드 연결과 관련된 JSON 개체는 다음과 같습니다.

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

이 정보를 사용 하는 한 가지 방법은 [armclient][armclient] GitHub 프로젝트에서 가져올 수 있는 armclient를 사용 하는 것입니다. 앱에 기존 하이브리드 연결을 적용하는 예제는 다음과 같습니다. 다음과 같이 위 스키마에 따라 JSON 파일을 만듭니다.

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

이 API를 사용하려면 송신 키 및 릴레이 리소스 ID가 필요합니다. filename hctest.json을 사용하여 정보를 저장한 경우 다음 명령을 실행하여 앱에 하이브리드 연결을 적용합니다. 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>하이브리드 연결 보안 ##

기본 Azure Service Bus Relay에 대한 충분 한 권한이 있는 사용자가 기존 하이브리드 연결을 다른 App Service Web Apps에 추가할 수 있습니다. 즉, 다른 사용자가 동일한 하이브리드 연결을 다시 사용 하지 못하도록 해야 하는 경우 (예: 대상 리소스가 무단 액세스를 차단 하는 추가 보안 측정값이 없는 서비스인 경우) Azure에 대한 액세스를 잠가야 합니다. Service Bus Relay.

릴레이에 대한 `Reader` 액세스 권한이 있는 사용자는 Azure Portal에서 웹 앱에 하이브리드 연결을 추가 하려고 할 때 하이브리드 연결을 _볼_ 수 있지만 릴레이 연결을 설정 하는 데 사용 되는 연결 문자열을 검색 하는 권한이 없는 사용자는이 연결을 _추가할_ 수 없습니다. 하이브리드 연결을 성공적으로 추가 하려면 `listKeys` (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`) 권한이 있어야 합니다. 릴레이에 대해이 사용 권한을 포함 하는 `Contributor` 역할 또는 다른 모든 역할은 사용자가 하이브리드 연결을 사용 하 여 자신의 Web Apps에 추가 하도록 허용 합니다.

## <a name="troubleshooting"></a>문제 해결 ##

"연결됨" 상태는 하나 이상의 HCM이 해당 하이브리드 연결로 구성되어 Azure에 연결할 수 있음을 의미합니다. 하이브리드 연결의 상태가 **연결됨**으로 표시되지 않으면 Azure에 액세스할 수 있는 HCM에 하이브리드 연결이 구성되어 있지 않은 것입니다.

클라이언트가 엔드포인트에 연결할 수 없는 주된 이유는 엔드포인트가 DNS 이름이 아닌 IP 주소를 사용하여 지정되었기 때문입니다. 앱이 원하는 엔드포인트에 도달할 수 없고 IP 주소가 사용되었으면 HCM이 실행 중인 호스트에서 유효한 DNS 이름을 사용하도록 전환합니다. 또한 HCM을 실행 중인 호스트에서 DNS 이름이 제대로 확인되는지 확인합니다. HCM을 실행 중인 호스트에서 하이브리드 연결 엔드포인트까지 연결이 있는지 확인합니다.  

App Service에서 **tcpping** 명령줄 도구는 고급 도구 (Kudu) 콘솔에서 호출할 수 있습니다. 이 도구를 통해 TCP 엔드포인트에 액세스할 수 있는지 알 수 있지만 하이브리드 연결 엔드포인트에 액세스할 수 있는지는 알 수 없습니다. 하이브리드 연결 엔드포인트에 대해 콘솔의 도구를 사용하면 호스트:포트 조합을 사용한다는 것만 확인하게 됩니다.  

엔드포인트에 대한 명령줄 클라이언트가 있는 경우 응용 프로그램 콘솔에서 연결을 테스트할 수 있습니다. 예를 들어, 말아 넘기기를 사용 하 여 웹 서버 엔드포인트에 대한 액세스를 테스트할 수 있습니다.

## <a name="biztalk-hybrid-connections"></a>BizTalk 하이브리드 연결 ##

이 기능의 초기 형식을 BizTalk 하이브리드 연결이라고 지칭했습니다. 이 기능은 2018년 5월 31일에 지원이 종료되었으며 작동이 중단되었습니다. BizTalk 하이브리드 연결이 모든 앱에서 제거되었으므로 포털 또는 API를 통해 액세스할 수 없습니다. 이러한 이전 연결이 하이브리드 연결 관리자에 여전히 구성되어 있는 경우 중단됨 상태가 표시되고, 아래쪽에 수명 종료 설명이 표시됩니다.

![HCM의 BizTalk 하이브리드 연결][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
