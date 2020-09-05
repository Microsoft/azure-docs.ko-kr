---
title: WAF 구성
description: Azure Application Gateway 또는 타사 WAF를 사용하여 App Service Environment 앞에 WAF(웹 애플리케이션 방화벽)를 구성하는 방법에 대해 알아봅니다.
author: ccompy
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: e40a42afc99d505dc48794d5ad919e4d682b7070
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961842"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>App Service Environment에 대한 웹 애플리케이션 방화벽(WAF) 구성
## <a name="overview"></a>개요

WAF(웹 애플리케이션 방화벽)를 통해 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드 및 애플리케이션 DDoS와 기타 공격을 차단하기 위해 인바운드 웹 트래픽을 검사하여 웹 애플리케이션을 보호할 수 있습니다. DLP(데이터 손실 방지)를 위해 백 엔드 웹 서버로부터의 응답도 검사합니다. App Service Environment는 격리와 추가 확장의 조합을 제공합니다. 이 조합은 악의적인 요청과 고용량 트래픽을 견뎌야 하는 호스트 비즈니스 중요한 웹 애플리케이션에 이상적인 환경을 제공합니다. Azure에서는 [Application Gateway](../../application-gateway/overview.md)를 사용하여 WAF 기능을 제공합니다.  Application Gateway와 App Service 환경을 통합하는 방법을 보려면 [Application Gateway와 ILB ASE 통합](./integrate-with-application-gateway.md) 문서를 참고하세요.

Azure Application Gateway 외에도 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf?tab=PlansAndPrice)에서 사용할 수 있는 [Azure용 Barracuda WAF](https://www.barracuda.com/programs/azure)와 같은 여러 마켓플레이스 옵션이 있습니다. 이 문서의 나머지 부분에서는 Barracuda WAF 디바이스와 App Service 환경을 통합하는 방법에 중점을 둡니다.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>설치 프로그램
이 문서에서는 Barracuda WAF의 다중 부하 분산 인스턴스 뒤의 App Service Environment를 구성하여 WAF의 트래픽만이 App Service Environment에 도달할 수 있게 하고 DMZ로부터는 접근할 수 없습니다. Azure Traffic Manager를 Azure 데이터 센터와 지역 간의 작업 부하를 위해 Barracuda WAF 앞에 놓겠습니다. 설치 프로그램의 높은 수준의 다이어그램은 다음 이미지와 비슷합니다.

![Architecture][Architecture] 

> [!NOTE]
> [App Service 환경에 대한 ILB 지원](app-service-environment-with-internal-load-balancer.md)의 도입으로 DMZ에서 ASE에 액세스할 수 없고 프라이빗 네트워크에만 사용할 수 있도록 구성할 수 있습니다. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>App Service Environment 구성
App Service Environment를 구성하려면 해당 제목의 [설명서](app-service-web-how-to-create-an-app-service-environment.md)를 참조하세요. App Service Environment를 만들면 Web Apps, API Apps 및 [Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)를 다음 섹션에서 구성할 WAF로 보호 받는 환경에서 만들 수 있습니다.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Barracuda WAF 클라우드 서비스를 구성합니다.
Barracuda에는 Azure의 가상 머신에 WAF를 배포하는 방법에 대한 [자세한 문서](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) 가 있습니다. 이 설명서를 따라할 때 중복성을 원하고 단일 실패 지점을 도입하지 않는 것뿐만 아니라, 동일한 클라우드 서비스 안에 최소 2개의 WAF 인스턴스 VM을 배포하는 것을 원합니다.

### <a name="adding-endpoints-to-cloud-service"></a>클라우드 서비스에 엔드포인트 추가
클라우드 서비스 내에 2개 이상의 WAF VM이 있다면 [Azure portal](https://portal.azure.com/) 을 사용하여 다음 이미지처럼 애플리케이션에서 사용하는 HTTP와 HTTPS 엔드포인트를 추가할 수 있습니다.

![엔드포인트 구성][ConfigureEndpoint]

애플리케이션에서 다른 엔드포인트를 사용하는 경우 이 목록에 추가해야 합니다. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>해당 관리 포털을 통해 WAF Barracuda 구성
해당 관리 포털을 통해 Barracuda WAF를 사용하여 TCP Port 8000에 대해 구성합니다. WAF VM의 여러 인스턴스가 있기 때문에 각 VM에 이 단계를 반복해서 수행해야 합니다. 

> [!NOTE]
> WAF 구성이 끝났다면, WAF 보안을 유지하기 위해 모든 WAF VM에서 TCP/8000 엔드포인트를 제거합니다.
> 
> 

다음 이미지와 같이 관리 엔드포인트를 추가하여 Barracuda WAF를 구성합니다.

![관리 엔드포인트 추가][AddManagementEndpoint]

브라우저를 사용하여 클라우드 서비스에서 관리 엔드포인트로 이동 합니다. Cloud Service 이름이 test.cloudapp.net이라면 `http://test.cloudapp.net:8000` 으로 이동하여 이 엔드포인트에 액세스합니다. 로그인 페이지를 참조해야 다음 이미지와 같은 WAF VM 설치 단계에서 지정한 자격 증명을 사용하여 로그인할 수 있습니다.

![관리 로그인 페이지][ManagementLoginPage]

로그인하면 다음 이미지의 1처럼 WAF 보호에 관한 기본 통계를 표시하는 대시보드가 표시됩니다.

![관리 대시보드][ManagementDashboard]

**서비스** 탭을 클릭하여 WAF가 보호하는 서비스에 대해 구성할 수 있습니다. Barracuda WAF를 구성하는 방법에 대한 자세한 내용은 [해당 설명서](https://techlib.barracuda.com/waf/getstarted1)를 참조하세요. 다음 예제에서는 HTTP 및 HTTPS에 트래픽을 제공하는 App Service 앱이 구성되었습니다.

![관리 서비스를 추가 합니다.][ManagementAddServices]

> [!NOTE]
> 애플리케이션이 어떻게 구성되었는지, App Service Environment에서 어떤 기능들이 쓰이고 있는지에 따라서 트래픽을 80 및 443이 아닌 TCP 포트로 전달해야 합니다(예: App Service 앱에 대한 IP TLS를 설치한 경우). App Service Environment에서 사용되는 네트워크 포트의 목록은 [인바운드 트래픽을 제어 설명서](app-service-app-service-environment-control-inbound-traffic.md)의 네트워크 포트 섹션을 참조하세요.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Microsoft Azure Traffic Manager를 구성합니다. (선택 사항)
애플리케이션이 여러 지역에서 사용할 수 있는 경우, [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)를 사용하여 부하 분산하고자 합니다. 이 작업을 수행하려면 다음 이미지와 같이 Traffic Manager 프로필의 WAF 클라우드 서비스 이름을 이용하여 [Azure Portal](https://portal.azure.com)에서 엔드포인트를 추가합니다. 

![Traffic Manager 엔드포인트][TrafficManagerEndpoint]

애플리케이션에 대해 인증이 필요한 경우, 애플리케이션의 가용성에 대해 ping하는 Traffic Manager에 대한 어떤 자격 증명도 필요하지 않는 리소스가 남아 있는지 확인합니다. 다음 이미지와 같이 [Azure Portal](https://portal.azure.com)의 **구성** 페이지에서 URL을 구성할 수 있습니다.

![Traffic Manager를 구성하는 방법][ConfigureTrafficManager]

다음 예제와 같이 WAF에서 애플리케이션으로 Traffic Manager Ping을 전송하려면, 웹 사이트 번역을 Barracuda WAF에 설치하여 트래픽을 애플리케이션으로 전송하게 합니다.

![웹사이트 번역][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>NSG(네트워크 보안 그룹)을 사용하여 App Service Environment에 대한 트래픽 보안
클라우드 서비스의 VIP 주소만 사용하여 WAF에서 App Service Environment로 들어오는 트래픽을 제한하는 방법에 대한 자세한 내용은 [인바운드 트래픽 제어 설명서](app-service-app-service-environment-control-inbound-traffic.md) 를 참조하세요. TCP 포트 80에 대한 작업을 수행 하기 위한 샘플 PowerShell 명령은 다음과 같습니다.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
```

프로그램 WAF 클라우드 서비스의 가상 IP 주소 (VIP)는 SourceAddres Prefix를 바꿉니다.

> [!NOTE]
> 클라우드 서비스의 VIP는 클라우드 서비스를 삭제하고 다시 만들 때 변경 됩니다. 이렇게 하면 네트워크 리소스 그룹의 IP 주소를 업데이트해야 합니다. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png