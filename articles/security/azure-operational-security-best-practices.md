---
title: Azure 운영 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 Azure 운영 보안을 위한 모범 사례 모음을 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: e2678eb7d75921f43a1e51b6a8cefc9925a9adc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587022"
---
# <a name="azure-operational-security-best-practices"></a>Azure 운영 보안 모범 사례
Azure 운영 보안은 사용자가 Azure에서 자신의 데이터, 애플리케이션 및 기타 자산을 보호하는 데 사용할 수 있는 서비스, 제어 및 기능을 지칭합니다. Azure 운영 보안은 [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl), [Microsoft 보안 대응 센터](https://www.microsoft.com/msrc?rtc=1) 프로그램 및 사이버 보안 위협 상황에 대한 심층 인식을 포함하여 Microsoft 고유의 다양한 기능을 통해 얻은 지식을 통합한 프레임워크를 기반으로 합니다.

이 문서에서는 보안 모범 사례 모음에 대해 설명합니다. 이러한 모범 사례는 Azure 데이터베이스 보안 경험과 여러분과 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음과 같이 설명합니다.
-   각 모범 사례
-   해당 모범 사례를 사용해야 하는 이유
-   해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
- 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure 운영 보안 모범 사례 문서는 이 문서가 작성된 당시에 있었던 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>저장소 서비스에서 예기치 않은 동작 변경 모니터링
클라우드 환경에서 호스팅되는 분산 애플리케이션의 문제를 진단하고 해결하는 과정은 기존 환경보다 복잡할 수 있습니다. 애플리케이션은 PaaS 또는 IaaS 인프라, 온-프레미스, 모바일 디바이스 또는 이들 중 일부가 조합된 환경에 배포될 수 있습니다. 애플리케이션의 네트워크 트래픽은 공용 네트워크와 개인 네트워크로 전송될 수 있으며, 애플리케이션이 여러 가지 저장 기술을 사용할 수도 있습니다.

따라서 애플리케이션이 사용하는 스토리지 서비스를 지속적으로 모니터링해 응답 속도 저하 등의 예기치 않은 동작 변경을 파악해야 합니다. 로깅을 사용하여 자세한 데이터를 수집하고 문제를 심층 분석합니다. 모니터링 및 로깅을 통해 얻은 진단 정보를 토대로 애플리케이션에 발생한 문제의 근본 원인을 확인할 수 있습니다. 그러면 문제를 해결하고 해당 문제를 완화하기 위한 적절한 단계를 결정할 수 있습니다.

[Azure 스토리지 분석](../storage/storage-analytics.md)은 로깅을 수행하며 Azure Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고, 사용량 추세를 분석하고, 저장소 계정에 대한 문제를 진단하는 것이 좋습니다.

## <a name="prevent-detect-and-respond-to-threats"></a>위협 방지, 검색 및 대응
[Azure Security Center](../security-center/security-center-intro.md)는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. Azure 구독 전반에 걸친 통합 보안 모니터링 및 정책 관리 기능을 제공하는 Azure Security Center를 활용하면 일반적으로는 찾아내기가 어려운 위협을 검색할 수 있으며 다양한 보안 솔루션을 활용할 수 있습니다.

Security Center의 무료 계층에서는 Azure 리소스에만 사용 가능한 제한적 보안 기능이 제공됩니다. 표준 계층에서는 이러한 기능이 온-프레미스 및 기타 클라우드로 확대 제공됩니다. Security Center 표준을 사용하면 보안 취약성을 찾아서 수정하고, 액세스 및 애플리케이션 제어를 적용하여 악성 활동을 차단하고, 분석 및 인텔리전스를 사용하여 위협을 검색하고, 공격을 받을 때 신속하게 대응할 수 있습니다. 처음 60일 동안 추가 비용 없이 Security Center 표준을 사용해 볼 수 있습니다. [Security Center 표준 계층에서 Azure 구독을 온보딩](../security-center/security-center-get-started.md)하는 것이 좋습니다.

Security Center를 사용하면 모든 Azure 리소스의 보안 상태를 중앙에서 살펴볼 수 있습니다. 적합한 보안 제어 기능이 마련되어 있으며 올바르게 구성되어 있는지를 한눈에 확인하고 주의가 필요한 리소스를 빠르게 확인할 수 있습니다.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>종단 간 시나리오 기반 네트워크 모니터링
고객은 가상 네트워크, ExpressRoute, Application Gateway, 부하 분산 장치 등의 네트워크 리소스를 결합하여 Azure에서 종단 간 네트워크를 구축합니다. 모니터링은 각 네트워크 리소스에서 사용할 수 있습니다.

[Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)는 지역별 서비스입니다. 이 서비스에서 제공하는 진단 및 시각화 도구를 사용하면 Azure 내/외부 통신에 사용되는 네트워크 시나리오 수준의 상태를 모니터링하고 진단할 수 있습니다.

사용 가능한 도구 및 네트워크 모니터링 관련 모범 사례는 다음과 같습니다.

**모범 사례**: 패킷 캡처를 사용하여 원격 네트워크 모니터링을 자동화합니다.  
**세부 정보**: VM에 로그인하지 않고 Network Watcher를 사용하여 네트워킹 문제를 모니터링 및 진단합니다. 경고를 설정하여 [패킷 캡처](../network-watcher/network-watcher-alert-triggered-packet-capture.md)를 트리거하고 패킷 수준에서 실시간 성능 정보에 액세스합니다. 문제를 발견하면 자세히 조사하여 더 정확히 진단할 수 있습니다.

**모범 사례**: 흐름 로그를 사용하여 네트워크 트래픽에 대한 인사이트를 얻습니다.  
**세부 정보**: [네트워크 보안 그룹 흐름 로그](../network-watcher/network-watcher-nsg-flow-logging-overview.md)를 사용하여 네트워크 트래픽 패턴을 심층 파악합니다. 흐름 로그의 정보를 확인하면 준수, 감사 및 네트워크 보안 프로필 모니터링에 필요한 데이터를 수집할 수 있습니다.

**모범 사례**: VPN 연결 문제를 진단합니다.  
**세부 정보**: Network Watcher를 사용하면 [자주 발생하는 VPN Gateway 및 연결 문제를 진단](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)할 수 있습니다. 문제를 파악할 수 있을 뿐 아니라 자세한 로그를 사용하여 추가로 조사할 수도 있습니다.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>검증된 DevOps 도구를 사용하여 배포 보안 유지
기업과 팀의 생산성과 효율성을 높이려면 다음의 DevOps 모범 사례를 따르세요.

**모범 사례**: 서비스 빌드 및 배포를 자동화합니다.  
**세부 정보**: [IaaC(Infrastructure as code)](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)는 IT 전문가가 일상적으로 수행하는 모듈식 인프라 빌드 및 관리 작업 부담을 없애 주는 기술 및 구성 요소 세트입니다. IT 전문가는 IaaC를 통해 소프트웨어 개발자가 애플리케이션 코드를 작성하고 유지 관리하는 것과 유사한 방식으로 최신 서버 환경을 빌드하고 유지 관리할 수 있습니다.

[Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/)를 사용하면 선언적 템플릿을 통해 애플리케이션을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 애플리케이션 수명 주기의 각 단계에서 애플리케이션을 반복 배포합니다.

**모범 사례**: 자동으로 Azure 웹앱 또는 클라우드 서비스에 빌드 및 배포합니다.  
**세부 정보**: Azure 웹앱 또는 클라우드 서비스에 [자동으로 빌드 및 배포](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)하도록 Azure Pipelines를 사용할 수 있습니다. Azure Pipelines는 코드 체크인 후 매번 Azure에 빌드한 뒤 이진 파일을 자동으로 배포합니다. 패키지 빌드 프로세스는 Visual Studio의 Package 명령과 동일하며 게시 단계는 Visual Studio의 Publish 명령과 동일합니다.

**모범 사례**: 지속적인 배포를 사용합니다.  
**세부 정보**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)는 다단계 배포 자동화 및 릴리스 프로세스 관리를 위한 솔루션입니다. 관리되는 지속적인 배포 파이프라인을 만들어 빠르고 쉽게 자주 릴리스합니다. Azure Pipelines를 통해 릴리스 프로세스를 자동화하고, 미리 정의된 승인 워크플로를 적용할 수 있습니다. 필요에 맞게 온-프레미스 및 클라우드에 배포하고 확장하며 사용자 지정합니다.

**모범 사례**: 시작하거나 프로덕션 환경에 업데이트를 배포하기 전에 앱의 성능을 확인합니다.  
**세부 정보**: Azure Test Plans를 사용하여 다음 작업을 수행해 클라우드 기반 [부하 테스트](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)를 실행합니다.

- 앱의 성능 문제를 찾습니다.
- 배포 품질을 개선합니다.
- 앱이 항상 사용 가능한 상태인지 확인합니다.
- 앱이 차기 출시 또는 마케팅 캠페인을 위한 트래픽을 처리할 수 있는지 확인합니다.

**모범 사례**: 애플리케이션 성능을 모니터링합니다.  
**세부 정보**: [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. Application Insights를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. 성능 이상을 자동으로 검색합니다. 또한 문제를 진단하고 앱을 사용하여 실제로 수행하는 작업을 파악할 수 있는 분석 도구도 포함되어 있습니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

## <a name="mitigate-and-protect-against-ddos"></a>DoS 완화 및 방지
DDoS(배포된 서비스 거부)는 애플리케이션 리소스를 고갈시키려는 공격 유형입니다. 공격 목표는 애플리케이션의 가용성과 합법적인 요청을 처리하는 기능을 약화시키는 것입니다. 최근에는 이러한 공격이 점점 정교해지고 그 규모와 영향도 점점 커지고 있습니다. 인터넷을 통해 공개적으로 연결 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다.

DDoS 발생 시 복원하는 기능을 설계하고 작성하려면 다양한 오류 모드에 대한 계획과 설계가 필요합니다.

Azure에서 DDoS 공격 시 복원이 가능한 서비스를 구축하기 위한 모범 사례는 다음과 같습니다.

**모범 사례**: 디자인 및 구현에서 배포 및 작업까지 애플리케이션의 전체 수명 주기에서 보안을 최우선으로 고려해야 합니다. 애플리케이션에는 양이 그다지 많지 않은 요청에서 많은 리소스를 사용하여 결국 서비스 중단으로 이어지게 만드는 버그가 있을 수 있습니다.  
**세부 정보**: Microsoft Azure에서 실행되는 서비스를 보호하기 위해 애플리케이션 아키텍처를 정확하게 이해하고 [소프트웨어 품질의 5가지 핵심 요소](https://docs.microsoft.com/azure/architecture/guide/pillars)에 집중해야 합니다. 일반적인 트래픽 볼륨, 애플리케이션과 다른 애플리케이션 간의 연결 모델, 공용 인터넷에 노출되는 서비스 엔드포인트를 알아야 합니다.

애플리케이션 자체를 대상으로 하는 서비스 거부 공격을 처리할 수 있도록 애플리케이션의 복원력을 높이는 것이 가장 중요합니다. [SDL(Security Development Lifecycle)](https://www.microsoft.com/en-us/sdl)부터 보안 및 개인 정보 보호 기능이 Azure 플랫폼에 기본 제공됩니다. SDL은 모든 개발 단계에서 보안 문제를 해결하고 Azure를 지속적으로 업데이트하여 더욱 안전하게 보호합니다.

**모범 사례**: 증폭되는 부하, 특히 DDoS 공격 시 증폭되는 부하 수요를 충족할 수 있도록 애플리케이션이 [수평으로 확장 가능](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)하도록 설계합니다. 애플리케이션이 서비스의 단일 인스턴스에 종속된 경우 단일 실패 지점이 생깁니다. 여러 인스턴스를 프로비전하면 시스템에 복원성 및 확장성이 증가하게 됩니다.  
**세부 정보**: [Azure App Service](../app-service/app-service-value-prop-what-is.md)의 경우 여러 인스턴스를 제공하는 [App Service 계획](../app-service/overview-hosting-plans.md)을 선택합니다.

Azure Cloud Services의 경우 각각의 역할을 [여러 인스턴스](../cloud-services/cloud-services-choose-me.md)를 사용하도록 구성합니다.

[Azure Virtual Machines](../virtual-machines/windows/overview.md)의 경우 VM 아키텍처가 둘 이상의 VM을 포함하는지와 각 VM이 [가용성 집합](../virtual-machines/virtual-machines-windows-manage-availability.md)에 포함되는지 확인합니다. 자동 크기 조정 기능을 활용할 수 있는 Virtual Machine Scale Sets를 사용하는 것이 좋습니다.

**모범 사례**: 애플리케이션에서 보안 방어를 계층화하면 공격이 성공할 가능성이 줄어듭니다. Azure 플랫폼의 기본 제공 기능을 사용하여 안전한 애플리케이션 디자인을 구현합니다.  
**세부 정보**: 애플리케이션의 크기(노출 영역)가 커지면 공격 위험도 증가합니다. 부하 분산 장치([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) 및 [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md))에 필요하지 않은 노출된 IP 주소 공간 및 수신 대기 포트를 종료하기 위해 허용 목록을 사용하여 노출 영역을 줄일 수 있습니다.

[네트워크 보안 그룹](../virtual-network/security-overview.md)은 공격 표면을 줄이기 위한 또 다른 방법입니다. [서비스 태그](../virtual-network/security-overview.md#service-tags) 및 [애플리케이션 보안 그룹](../virtual-network/security-overview.md#application-security-groups)을 사용하여 보안 규칙을 만드는 복잡성을 최소화하고 애플리케이션 구조의 기본 확장으로 네트워크 보안을 구성할 수 있습니다.

가능하면 [가상 네트워크](../virtual-network/virtual-networks-overview.md)에 Azure 서비스를 배포해야 합니다. 이렇게 하면 서비스 리소스가 비공개 IP 주소를 통해 통신할 수 있습니다. 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다.

[서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md) 사용 시에는 서비스 트래픽이 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 비공개 주소를 원본 IP 주소로 사용하도록 전환됩니다.

종종 고객의 온-프레미스 리소스가 Azure의 해댱 리소스와 함께 공격당하는 모습을 볼 수 있습니다. 온-프레미스 환경을 Azure에 연결하는 경우 공용 인터넷에 노출되는 온-프레미스 리소스를 최소화합니다.

Azure에는 네트워크 공격으로부터 리소스를 보호하는 다음의 두 가지 DDoS [서비스](../virtual-network/ddos-protection-overview.md)가 제공됩니다.

- 기본 보호는 기본적으로 추가 비용 없이 Azure에 통합됩니다. 전역 배포 Azure 네트워크의 규모 및 용량은 항상 작동하는 트래픽 모니터링 및 실시간 위험 완화를 통해 일반적인 네트워크 레이어 공격을 방어합니다. 사용자가 구성하거나 애플리케이션을 변경하지 않아도 사용이 가능한 기본 보호 기능을 활용하면 Azure DNS 등의 PaaS 서비스를 포함한 모든 Azure 서비스를 보호할 수 있습니다.
- 표준 보호는 네트워크 공격에 대한 고급 DDoS 완화 기능을 제공합니다. 특정 Azure 리소스를 보호하도록 자동으로 조정됩니다. 가상 네트워크를 만드는 동안 간단하게 보호를 사용하도록 설정할 수 있습니다. 생성 후에도 설정 가능하며 애플리케이션 또는 리소스를 변경할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure 보안 모범 사례 및 패턴](security-best-practices-and-patterns.md)에서 Azure를 사용하여 클라우드 솔루션을 디자인하고, 배포하고, 관리할 때 사용할 수 있는 더 많은 보안 모범 사례를 참조하세요.

Azure 보안 및 관련 Microsoft 서비스에 대한 보다 일반적인 정보를 제공하는 다음 리소스도 확인할 수 있습니다.
* [Azure 보안 팀 블로그](https://blogs.msdn.microsoft.com/azuresecurity/) – Azure Security 관련 최신 정보를 확인할 수 있습니다.
* [Microsoft 보안 응답 센터](https://technet.microsoft.com/library/dn440717.aspx) - Azure와 관련된 문제를 비롯한 Microsoft 보안 취약점을 보고하거나 secure@microsoft.com으로 이메일을 보낼 수 있습니다.
