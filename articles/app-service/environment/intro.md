---
title: 소개
description: Azure App Service Environments를 통해 완전히 격리된 전용 환경에서 앱을 확장, 보호 및 최적화하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: e9bd97dc4afe0937ebd0553117640e008a72722e
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961750"
---
# <a name="introduction-to-the-app-service-environments"></a>App Service Environment 소개 #
 
## <a name="overview"></a>개요 ##

Azure App Service Environment는 Azure App Service 앱을 매우 높은 확장성으로 안전하게 실행하기 위해 완전히 격리된 전용 환경을 제공하는 Azure App Service 기능입니다. 이 기능은 다음을 호스트할 수 있습니다.

* Windows 웹앱
* Linux 웹앱 
* Docker 컨테이너
* 모바일 앱
* Functions

ASE(App Service Environment)는 다음을 필요로 하는 애플리케이션 워크로드에 적합합니다.

* 매우 높은 확장성
* 격리 및 보안된 네트워크 액세스
* 높은 메모리 사용률

고객은 단일 Azure 지역 내 또는 여러 Azure 지역에 걸쳐서 여러 ASE를 만들 수 있습니다. 따라서 ASE는 높은 RPS 워크로드를 지원하여 상태 비저장 애플리케이션 계층을 수평적으로 크기 조정하는 데 적합합니다.

ASE는 한 고객에서만 애플리케이션을 호스팅하고 VNet 중 하나에서 애플리케이션을 실행합니다. 고객은 인바운드 및 아웃바운드 애플리케이션 네트워크 트래픽을 세부적으로 제어할 수 있습니다. 애플리케이션은 온-프레미스 회사 리소스에 VPN을 통한 고속 보안 연결을 설정할 수도 있습니다.

* ASE에는 고유한 가격 책정 계층이 있습니다. [격리 솔루션](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment)이 하이퍼스케일 및 보안을 구현하는 데 어떻게 도움이 되는지 알아보세요.
* [App Service Environments v2](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment)는 네트워크의 서브넷에서 앱을 보호하는 환경을 제공하고, Azure App Service의 자체 프라이빗 배포를 지원합니다.
* 여러 ASE를 수평적 크기 조정에 사용할 수 있습니다. 자세한 내용은 [지리적으로 분산된 앱 설치 공간 설정 방법](app-service-app-service-environment-geo-distributed-scale.md)을 참조하세요.
* AzureCon 심층 분석에 표시된 대로 ASE는 보안 아키텍처를 구성하는 데 사용할 수 있습니다. AzureCon 심층 분석에 표시된 보안 아키텍처가 어떻게 구성되었는지 확인하려면 App Service Environment를 사용하여 [계층화된 보안 아키텍처를 구현하는 방법에 대한 문서](app-service-app-service-environment-layered-security.md)를 참조하세요.
* ASE에서 실행 중인 앱은 WAF(웹 애플리케이션 방화벽) 등의 업스트림 디바이스에서 제어된 액세스를 가질 수 있습니다. 자세한 내용은 [WAF(웹 애플리케이션 방화벽)][AppGW]를 참조하세요.
* 영역 고정을 사용하여 App Service Environments를 AZ(가용성 영역)에 배포할 수 있습니다.  자세한 내용은 [가용성 영역에 대한 App Service Environment 지원][ASEAZ]을 참조하세요.

## <a name="dedicated-environment"></a>전용 환경 ##

ASE는 단일 구독에 단독으로 전용되어 100개의 App Service 계획 인스턴스를 호스팅할 수 있습니다. 단일 App Service 계획의 100개 인스턴스부터 100개의 단일 인스턴스 App Service 계획까지 가능하며 모든 항목은 이 두 계획 사이에 있습니다.

ASE는 프런트 엔드 및 작업자로 구성됩니다. 프런트 엔드는 ASE 내의 앱 요청에 대한 자동 부하 분산 및 HTTP/HTTPS 종료를 담당합니다. 프런트 엔드는 ASE의 App Service 계획이 스케일 아웃됨에 따라 자동으로 추가됩니다.

작업자는 고객 앱을 호스트하는 역할입니다. 작업자는 3가지 고정된 크기로 사용할 수 있습니다.

* vCPU/3.5GB RAM 1개
* vCPU/7GB RAM 2개
* vCPU/14GB RAM 4개

고객은 프런트 엔드와 작업자를 관리할 필요가 없습니다. 모든 인프라는 고객이 App Service 계획을 스케일 아웃함에 따라 자동으로 추가됩니다. ASE에서 App Service 계획을 생성하거나 규모를 변경함에 따라 필요한 인프라가 적절하게 추가되거나 제거됩니다.

인프라에 대해 대금을 지급하고 ASE 크기에 따라 변경되지 않는 ASE의 월정액이 있습니다. 거기에 App Service 계획 vCPU당 비용이 발생합니다. ASE에 호스트되는 모든 앱은 격리 가격 책정 SKU에 해당합니다. ASE의 가격 책정에 대한 정보는 [App Service 가격 책정][Pricing] 페이지를 참조하고 ASE에 대한 사용 가능한 옵션을 검토하세요.

## <a name="virtual-network-support"></a>가상 네트워크 지원 ##

ASE 기능은 고객의 Azure Resource Manager 가상 네트워크에 직접 Azure App Service를 배포하는 것입니다. Azure 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 FAQ](../../virtual-network/virtual-networks-faq.md)를 참조하세요. ASE는 항상 가상 네트워크 내 존재하며, 더 정확하게는 가상 네트워크의 서브넷 내에 있습니다. 가상 네트워크의 보안 기능을 사용하여 앱에 대한 인바운드 및 아웃바운드 네트워크 통신을 제어할 수 있습니다.

ASE는 공개 IP 주소가 있는 인터넷 연결이거나 Azure ILB(내부 부하 분산 장치) 주소만 있는 내부 연결일 수 있습니다.

[네트워크 보안 그룹][NSGs]은 ASE가 있는 서브넷에 대한 인바운드 네트워크 통신을 제한합니다. NSG를 사용하여 WAF 및 네트워크 SaaS 공급자와 같은 업스트림 디바이스 및 서비스 뒤에서 앱을 실행할 수 있습니다.

또한 앱에서는 내부 데이터베이스 및 웹 서비스와 같은 회사 리소스에 자주 액세스해야 합니다. 온-프레미스 네트워크에 VPN이 연결되어 있는 가상 네트워크에 ASE를 배포하는 경우 ASE의 앱은 온-프레미스 리소스에 액세스할 수 있습니다. 이 기능은 VPN이 [사이트 간](../../vpn-gateway/vpn-gateway-multi-site.md)이든 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) VPN이든 마찬가지입니다.

ASE가 가상 네트워크 및 온-프레미스 네트워크와 함께 어떻게 작동하는지에 대한 자세한 내용은 [App Service Environment 네트워크 고려 사항][ASENetwork]을 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service 환경 v1 ##

App Service Environment에는 ASEv1 및 ASEv2라는 두 가지 버전이 있습니다. 위의 정보는 ASEv2를 기준으로 작성된 것입니다. 이 섹션은 ASEv1과 ASEv2의 차이를 보여줍니다. 

ASEv1에서는 모든 리소스를 수동으로 관리해야 합니다. 여기에는 IP 기반 SSL에 사용되는 프런트 엔드, 작업자 및 IP 주소가 포함됩니다. App Service 계획을 스케일 아웃하기 전에 호스트할 작업자 풀을 먼저 스케일 아웃해야 합니다.

ASEv1은 ASEv2와는 다른 가격 책정 모델을 사용합니다. ASEv1에서는 할당된 각 vCPU에 대한 비용을 지불합니다. 여기에는 워크로드를 호스트하지 않는 프런트 엔드 또는 작업자에 사용된 vCPU가 포함됩니다. ASEv1에서 ASE의 기본 최대 규모 크기는 총 55개의 호스트입니다. 여기에는 작업자 및 프런트 엔드가 포함됩니다. ASEv1의 한 가지 장점은 클래식 가상 네트워크 및 Resource Manager 가상 네트워크에 배포할 수 있다는 것입니다. ASEv1에 대해 자세히 알아보려면 [App Service Environment v1 소개][ASEv1Intro]를 참조하세요.

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
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
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html