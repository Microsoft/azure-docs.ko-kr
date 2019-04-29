---
title: App Service Environment로 지역 분산된 규모 - Azure
description: Traffic Manager 및 App Service 환경으로 지역 분포를 사용하여 앱을 수평으로 확장하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 769e6b9936ad6d3cb963e208cec4c49813f2b6d3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130724"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>App Service Environment로 지역 분산된 규모
## <a name="overview"></a>개요

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

대규모를 필요로 하는 애플리케이션 시나리오는 앱의 단일 배포에 사용할 수 있는 계산 리소스 용량을 초과할 수 있습니다.  투표 애플리케이션, 스포츠 이벤트 및 방송된 엔터테인먼트 이벤트는 대규모를 필요로 하는 시나리오를 모두 포함하는 예입니다. 대규모를 요구하면 수평으로 앱을 확장하여 단일 지역내에서 뿐만 아니라 지역에 걸쳐 수행되는 여러 앱 배포에 맞게 큰 부하 요구 사항을 처리합니다.

App Service Environment는 수평적 규모 확장에 대한 이상적인 플랫폼입니다.  알려진된 요청 속도 지원할 수 있는 App Service Environment 구성을 선택하면 개발자는 "쿠키 커터" 방식으로 추가 App Service Environment를 배포하여 원하는 최대 부하 용량을 얻을 수 있습니다.

예를 들어 App Service Environment의 구성에서 실행 중인 앱이 초당 20K(RPS) 요청을 처리하는 테스트를 거쳤다고 가정합니다.  원하는 최대 부하 용량이 100K RPS인 경우 다섯 가지(5) App Service Environment를 만들고 구성하여 애플리케이션이 최대 예상된 부하를 처리할 수 있도록 할 수 있습니다.

일반적으로 고객이 사용자 지정(또는 베니티) 도메인을 사용하여 앱에 액세스하기 때문에 개발자는 모든 App Service Environment 인스턴스에 앱 요청을 분산하는 방법이 있어야 합니다.  이 작업을 수행할 수 있는 좋은 방법은 [Azure Traffic Manager 프로필][AzureTrafficManagerProfile]을 사용하여 사용자 지정 도메인을 해결하는 것입니다.  Traffic Manager 프로필은 모든 개별 App Service 환경을 가리키도록 구성될 수 있습니다.  Traffic Manager는 Traffic Manager 프로필의 설정에서 부하 분산에 따라 모든 App Service 환경에 고객을 배포하도록 자동으로 처리합니다.  이 방법은 단일 모든 App Service Environment가 Azure 지역에 배포되는지 전세계의 여러 Azure 지역에 배포되는지에 관계 없이 작동합니다.

또한 고객이 베니티 도메인을 통해 앱에 액세스하므로 고객은 앱을 실행하는 App Service Environment의 수를 인식하지 않습니다.  결과적으로 개발자는 App Service Environment를 관찰된 트래픽 부하에 따라 쉽고 빠르게 추가 및 제거할 수 있습니다.

아래의 개념적 다이어그램은 단일 지역 내에서 세 가지 App Service Environment에 걸쳐 수평으로 확장된 앱을 보여줍니다.

![개념적 아키텍처][ConceptualArchitecture] 

이 항목의 나머지 부분에서는 여러 App Service 환경을 사용하여 샘플 앱에 대한 분산된 토폴로지를 설정하는 단계를 안내합니다.

## <a name="planning-the-topology"></a>토폴로지 계획
분산된 앱 공간을 빌드하기 전에 미리 약간 정보가 있는 편이 좋습니다.

* **앱의 사용자 지정 도메인:**  고객이 앱에 액세스하는 데 사용할 사용자 지정 도메인 이름은 무엇인가요?  샘플 앱에 대 한 사용자 지정 도메인 이름은 `www.scalableasedemo.com`
* **트래픽 관리자 도메인:**  도메인 이름은 [Azure Traffic Manager 프로필][AzureTrafficManagerProfile]을 만들 때 선택해야 합니다.  이 이름은 *trafficmanager.net* 접미사와 결합하여 Traffic Manager에서 관리되는 도메인 항목을 등록합니다.  샘플 앱의 경우 선택한 이름은 *scalable-ase-demo*입니다.  결과적으로 Traffic Manager에서 관리되는 전체 도메인 이름은 *scalable-ase-demo.trafficmanager.net*입니다.
* **앱 공간을 크기 조정하는 전략:**  애플리케이션 공간이 단일 Azure 지역의 여러 App Service Environment에 분산되나요?  여러 영역?  두 방법을 혼합 및 일치?  결정은 고객 트래픽이 생성되는 기대치 뿐만 아니라 백 엔드 인프라를 지원하는 앱의 나머지 부분이 확장할 수 있는 방법에 기반해야 합니다.  예를 들어 100% 상태 비저장 애플리케이션의 경우 Azure 지역 마다 여러 App Service Environment의 조합을 사용하여 앱을 크게 확장할 수 있으며 여러 Azure 지역에 걸쳐 배포된 App Service Environment로 곱해집니다.  선택할 수 있는 15+ 공용 Azure 지역으로 고객은 전세계 하이퍼 규모의 애플리케이션 공간을 진정으로 구축할 수 있습니다.  이 문서에 사용되는 샘플 앱의 경우 세 가지 App Service Environment를 단일 Azure 지역(미국 중남부)에서 만들었습니다.
* **App Service Environment에 대한 명명 규칙:**  각 App Service Environment에 고유한 이름이 필요합니다.  하나 또는 두 개의 App Service Environment 외에도 각 App Service Environment를 식별하는 데 도움이 되는 명명 규칙을 파악하는 것이 좋습니다.  샘플 앱의 경우 간단한 명명 규칙을 사용했습니다.  세 가지 App Service Environment의 이름은 *fe1ase*, *fe2ase*, 및 *fe3ase*입니다.
* **앱에 대한 명명 규칙:**  앱의 여러 인스턴스가 배포되기 때문에 배포된 앱의 인스턴스마다 이름이 필요합니다.  하나의 잘 알려지지 않지만 매우 편리한 App Service Environment의 기능은 동일한 앱 이름을 여러 App Service Environment에 걸쳐 사용할 수 있다는 점입니다.  각 App Service Environment에 고유한 도메인 접미사가 있으므로 개발자는 각 환경에 정확히 동일한 앱 이름을 다시 사용하도록 선택할 수 있습니다.  예를 들어 개발자가 앱을 *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net* 등과 같이 명명할 수 있습니다.  그러나 샘플 앱의 경우 각 앱 인스턴스에도 고유한 이름이 지정됩니다.  앱 인스턴스에 사용되는 이름은 *webfrontend1*, *webfrontend2* 및 *webfrontend3*입니다.

## <a name="setting-up-the-traffic-manager-profile"></a>Traffic Manager 프로필 설정
여러 App Service Environment에서 앱의 여러 인스턴스를 배포하면 개별 앱 인스턴스는 Traffic Manager를 사용하여 등록할 수 있습니다.  샘플 앱의 경우 Traffic Manager 프로필은 다음 배포된 앱 인스턴스에 고객을 라우팅할 수 있는 *scalable-ase-demo.trafficmanager.net* 에 필요합니다.

* **webfrontend1.fe1ase.p.azurewebsites.net:**  첫 번째 App Service Environment에 배포된 샘플 앱의 인스턴스입니다.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  두 번째 App Service Environment에 배포된 샘플 앱의 인스턴스입니다.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  세 번째 App Service Environment에 배포된 샘플 앱의 인스턴스입니다.

**동일한** Azure 지역에서 실행되는 여러 Azure App Service 엔드포인트를 등록하는 가장 쉬운 방법은 Powershell [Azure Resource Manager Traffic Manager 지원][ARMTrafficManager]을 사용하는 것입니다.  

첫 번째 단계는 Azure Traffic Manager 프로필을 만드는 것입니다.  아래 코드에서는 샘플 앱에 프로필을 만든 방법을 보여줍니다.

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

*RelativeDnsName* 매개 변수가 어떻게 *scalable-ase-demo*로 설정되는지 확인합니다.  도메인 이름 *scalable-ase-demo.trafficmanager.net* 이 만들어지고 Traffic Manager 프로필에 연결되는 방법입니다.

*TrafficRoutingMethod* 매개 변수는 부하 분산 정책을 정의하고 Traffic Manager는 사용 가능한 모든 엔드포인트에서 고객 부하를 분산하는 방법을 결정하는 데 사용합니다.  이 예제에서 *가중치* 메서드가 선택되었습니다.  이렇게 하면 고객 요청이 각 엔드포인트에 연결된 상대 가중치에 따라 등록된 애플리케이션 엔드포인트에 걸쳐 분산됩니다. 

만든 프로필을 사용하여 각 앱 인스턴스를 프로필에 네이티브 Azure 엔드포인트로 추가합니다.  아래 코드는 각 프런트 엔드 웹앱에 대한 참조를 인출하고 *TargetResourceId* 매개 변수의 방법으로 각 앱을 Traffic Manager 엔드포인트로 추가합니다.

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

개별 응용 프로그램 인스턴스에 *Add-AzureTrafficManagerEndpointConfig* 로 한 개의 호출이 있습니다.  각 Powershell 명령에서 *TargetResourceId* 매개 변수는 세 개의 배포된 앱 인스턴스 중 하나를 참조합니다.  Traffic Manager 프로필은 프로필에 등록된 세 개의 모든 엔드포인트에 걸쳐 부하를 분산합니다.

세 엔드포인트는 모두 *가중치* 매개 변수에 동일한 값(10)을 사용합니다.  그러면 Traffic Manager에서 세 가지 앱 인스턴스에 상대적으로 균일하게 고객 요청을 분산하게 됩니다. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Traffic Manager 도메인에서 앱의 사용자 지정 도메인 가리키기
필요한 마지막 단계는 Traffic Manager 도메인에서 앱의 사용자 지정 도메인을 가리키는 것입니다.  샘플 앱에 대 한 즉 `www.scalableasedemo.com` 에서 `scalable-ase-demo.trafficmanager.net`합니다.  이 단계는 사용자 지정 도메인을 관리하는 도메인 등록 기관으로 완료해야 합니다.  

등록 기관의 도메인 관리 도구를 사용하여 CNAME 기록은 Traffic Manager 도메인에서 사용자 지정 도메인을 가리키도록 만들어야 합니다.  아래 그림은 해당 CNAME 구성이 다음과 같다는 예를 보여줍니다.

![사용자 지정 도메인에 대한 CNAME][CNAMEforCustomDomain] 

이 항목에서 설명하지 않았지만 각 개별 앱 인스턴스도 등록된 사용자 지정 도메인이 있어야 합니다.  그렇지 않은 경우 앱 인스턴스로 요청을 하고 애플리케이션에 앱으로 등록된 사용자 지정 도메인이 없다면 요청은 실패합니다.  

이 예제에서는 사용자 지정 도메인은 `www.scalableasedemo.com`, 각 응용 프로그램 인스턴스에 연결 된 사용자 지정 도메인 및 합니다.

![사용자 지정 도메인][CustomDomain] 

Azure App Service 앱으로 사용자 지정 도메인을 등록하는 요점은 [사용자 지정 도메인 등록][RegisterCustomDomain]의 다음 문서를 참조하세요.

## <a name="trying-out-the-distributed-topology"></a>배포된 토폴로지 사용
Traffic Manager 및 DNS 구성의 최종 결과 대 한 요청은 `www.scalableasedemo.com` 다음 시퀀스를 통과 합니다.

1. 브라우저 또는 장치에 대 한 DNS 조회를 확인 합니다. `www.scalableasedemo.com`
2. 도메인 등록 기관에서 CNAME 항목은 DNS를 조회하여 Azure Traffic Manager로 리디렉션됩니다.
3. DNS 조회는 Azure Traffic Manager DNS 서버 중 하나에 대한 *scalable-ase-demo.trafficmanager.net* 에 대해 수행합니다.
4. 부하 분산 정책에 따라(Traffic Manager 프로필을 만들 때 이전에 사용된 *TrafficRoutingMethod* 매개 변수) Traffic Manager는 구성된 엔드포인트 중 하나를 선택하고 브라우저 또는 디바이스에 해당 엔드포인트의 FQDN을 반환합니다.
5. 엔드포인트의 FQDN이 App Service Environment에서 실행 중인 앱 인스턴스의 URL이기 때문에 브라우저 또는 디바이스는 Microsoft Azure DNS 서버에 요청하여 FQDN을 IP 주소로 해결합니다. 
6. 브라우저 또는 디바이스는 IP 주소에 HTTP/S 요청을 보냅니다.  
7. 요청은 App Service Environment 중 하나에서 실행 중인 앱 인스턴스 중 하나에 도착합니다.

아래 그림의 콘솔에서는 세 개의 샘플 App Service Environment 중 하나에서 실행 중인 앱 인스턴스를 성공적으로 해결하는 샘플 앱의 사용자 지정 도메인에 대한 DNS 조회를 보여줍니다.(이 경우 세 개의 App Service Environments 중 두 번째) 

![DNS 조회][DNSLookup] 

## <a name="additional-links-and-information"></a>추가 링크 및 정보
Powershell [Azure Resource Manager Traffic Manager 지원][ARMTrafficManager]에 대한 설명서입니다.  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
