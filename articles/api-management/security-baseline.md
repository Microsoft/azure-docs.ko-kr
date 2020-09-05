---
title: API Management Azure 보안 기준
description: API Management Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d6c954d6336a0b88f57ba1f70571263af5d1c583
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229806"
---
# <a name="azure-security-baseline-for-api-management"></a>API Management Azure 보안 기준

API Management Azure 보안 기준에는 배포의 보안 상태를 향상시키는 데 도움이 되는 추천 사항이 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호

**지침**: Azure API Management는 네트워크 내의 백 엔드 서비스에 액세스할 수 있도록 Azure Vnet(Virtual Network) 내에 배포할 수 있습니다. 개발자 포털 및 API Management 게이트웨이를 인터넷(외부)에서 또는 Vnet(내부) 내에서만 액세스할 수 있도록 구성할 수 있습니다.
- 외부: 외부 부하 분산 장치를 통해 퍼블릭 인터넷에서 API Management 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.
- 내부: 내부 부하 분산 장치를 통해 가상 네트워크 내에서만 API Management 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.

API Management가 배포된 서브넷으로의 인바운드 및 아웃바운드 트래픽은 네트워크 보안 그룹을 사용하여 제어할 수 있습니다.

* [가상 네트워크에서 Azure API Management를 사용하는 방법](./api-management-using-with-vnet.md)

* [내부 가상 네트워크에서 Azure API Management를 사용하는 방법](./api-management-using-with-internal-vnet.md)

* [내부 VNET의 API Management를 Application Gateway와 통합](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성과 트래픽에 대한 모니터링 및 기록

**지침**: API Management가 배포된 서브넷으로의 인바운드 및 아웃바운드 트래픽은 NSG(네트워크 보안 그룹)를 사용하여 제어할 수 있습니다. NSG를 API Management 서브넷에 배포하고, NSG 흐름 로그를 사용하도록 설정하며, 트래픽 감사를 위해 로그를 Azure Storage 계정에 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

주의: API Management 서브넷에서 NSG를 구성하는 경우 열려 있어야 하는 포트 세트가 있습니다. 이러한 포트를 사용할 수 없는 경우 API Management가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다.

* [Azure API Management를 위한 NSG 구성 이해](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 중요한 웹/HTTP API를 보호하려면 Vnet(Virtual Network) 내의 API Management를 내부 모드로 구성하고 Azure Application Gateway를 구성합니다. Application Gateway는 PaaS 서비스입니다. 역방향 프록시로 작동하며, L7 부하 분산, 라우팅, WAF(웹 애플리케이션 방화벽) 및 기타 서비스를 제공합니다.

내부 Vnet에서 프로비저닝된 API Management를 Application Gateway 프런트 엔드와 결합하면 다음 시나리오가 가능합니다.
- 단일 API Management 리소스를 사용하여 모든 API를 내부 및 외부 소비자 모두에 공개합니다.
- 단일 API Management 리소스를 사용하여 API의 하위 세트를 외부 소비자에 공개합니다.
- 퍼블릭 인터넷에서 API Management로의 액세스를 설정하고 해제하는 방법을 제공합니다.

참고: 이 기능은 API Management의 프리미엄 및 개발자 계층에서 사용할 수 있습니다.

* [내부 VNET의 API Management를 Application Gateway와 통합하는 방법](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure Application Gateway 이해](../application-gateway/index.yml)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Vnet(Virtual Network) 내의 API Management를 내부 모드로 구성하고 Azure Application Gateway를 구성합니다. Application Gateway는 PaaS 서비스입니다. 역방향 프록시로 작동하며, L7 부하 분산, 라우팅, WAF(웹 애플리케이션 방화벽) 및 기타 서비스를 제공합니다.

내부 Vnet에서 프로비저닝된 API Management를 Application Gateway 프런트 엔드와 결합하면 다음 시나리오가 가능합니다.
- 단일 API Management 리소스를 사용하여 모든 API를 내부 및 외부 소비자 모두에 공개합니다.
- 단일 API Management 리소스를 사용하여 API의 하위 세트를 외부 소비자에 공개합니다.
- 퍼블릭 인터넷에서 API Management로의 액세스를 설정하고 해제하는 방법을 제공합니다.

참고: 이 기능은 API Management의 프리미엄 및 개발자 계층에서 사용할 수 있습니다.

API Management 배포와 연결된 Vnet에서 Azure DDoS Protection 표준을 사용하도록 설정하여 DDoS(분산 서비스 거부) 공격으로부터 보호합니다.

Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용되지 않는 인터넷 IP 주소와의 통신을 거부합니다.

* [내부 VNET의 API Management를 Application Gateway와 통합하는 방법](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure Application Gateway 이해](../application-gateway/index.yml)

* [DDoS Protection 표준을 구성하는 방법](../virtual-network/manage-ddos-protection.md)

* [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/threat-protection.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: API Management가 배포된 서브넷으로의 인바운드 및 아웃바운드 트래픽은 NSG(네트워크 보안 그룹)를 사용하여 제어할 수 있습니다. NSG를 API Management 서브넷에 배포하고, NSG 흐름 로그를 사용하도록 설정하며, 트래픽 감사를 위해 로그를 Azure Storage 계정에 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

주의: API Management 서브넷에서 NSG를 구성하는 경우 열려 있어야 하는 포트 세트가 있습니다. 이러한 포트를 사용할 수 없는 경우 API Management가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다.

* [Azure API Management를 위한 NSG 구성 이해](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지 시스템/침입 방지 시스템) 배포

**지침**: Vnet(Virtual Network) 내의 API Management를 내부 모드로 구성하고 Azure Application Gateway를 구성합니다. Application Gateway는 PaaS 서비스입니다. 역방향 프록시로 작동하며, L7 부하 분산, 라우팅, WAF(웹 애플리케이션 방화벽) 및 기타 서비스를 제공합니다. Application Gateway WAF는 일반적인 보안 악용 및 취약성으로부터 보호하고 다음 두 가지 모드에서 실행할 수 있습니다.
- 탐지 모드: 모든 위협 경고를 모니터링하고 기록합니다. [진단] 섹션에서 Application Gateway에 대한 진단 로깅을 설정할 수 있습니다. WAF 로그가 선택되고 설정되어 있는지 확인해야 합니다. 웹 애플리케이션 방화벽을 탐지 모드로 실행하면 수신 요청을 차단하지 않습니다.
- 방지 모드: 규칙에서 탐지하는 침입 및 공격을 차단합니다. 공격자는 "403 무단 액세스" 예외를 수신하고, 연결이 종료됩니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

내부 Vnet에서 프로비저닝된 API Management를 Application Gateway 프런트 엔드와 결합하면 다음 시나리오가 가능합니다.
- 단일 API Management 리소스를 사용하여 모든 API를 내부 및 외부 소비자 모두에 공개합니다.
- 단일 API Management 리소스를 사용하여 API의 하위 세트를 외부 소비자에 공개합니다.
- 퍼블릭 인터넷에서 API Management로의 액세스를 설정하고 해제하는 방법을 제공합니다.

참고: 이 기능은 API Management의 프리미엄 및 개발자 계층에서 사용할 수 있습니다.

* [내부 VNET의 API Management를 Application Gateway와 통합하는 방법](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure Application Gateway WAF 이해](../web-application-firewall/ag/ag-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: Web/HTTP API로 흐르는 트래픽을 관리하기 위해 API Management를 외부 또는 내부 모드에서 App Service Environment와 연결된 Vnet(Virtual Network)에 배포합니다.

내부 모드에서는 Azure Application Gateway를 API Management 앞에 구성합니다. Application Gateway는 PaaS 서비스입니다. 역방향 프록시로 작동하며, L7 부하 분산, 라우팅, WAF(웹 애플리케이션 방화벽) 및 기타 서비스를 제공합니다. Application Gateway WAF는 일반적인 보안 악용 및 취약성으로부터 보호합니다.

내부 Vnet에서 프로비저닝된 API Management를 Application Gateway 프런트 엔드와 결합하면 다음 시나리오가 가능합니다.
- 단일 API Management 리소스를 사용하여 모든 API를 내부 및 외부 소비자 모두에 공개합니다.
- 단일 API Management 리소스를 사용하여 API의 하위 세트를 외부 소비자에 공개합니다.
- 퍼블릭 인터넷에서 API Management로의 액세스를 설정하고 해제하는 방법을 제공합니다.

참고: 이 기능은 API Management의 프리미엄 및 개발자 계층에서 사용할 수 있습니다.

* [프라이빗 API를 외부 소비자에 공개하는 방법](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Vnet 내에서 API Management를 사용하는 방법](./api-management-using-with-vnet.md)

* [Azure Application Gateway의 Azure 웹 애플리케이션 방화벽](../web-application-firewall/ag/ag-overview.md)

* [Azure Application Gateway 이해](../application-gateway/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Vnet(Virtual Network) 서비스 태그를 사용하여 API Management 서브넷에서 사용되는 NSG(네트워크 보안 그룹)에 대한 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

주의: API Management 서브넷에서 NSG를 구성하는 경우 열려 있어야 하는 포트 세트가 있습니다. 이러한 포트를 사용할 수 없는 경우 API Management가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다.

* [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

* [API Management에 필요한 포트](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure API Management 배포와 관련된 네트워크 설정에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.ApiManagement" 및 "Microsoft.Network" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure API Management 배포 및 관련 리소스에 대한 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. Azure Virtual Networks에 대해 다음과 같은 기본 제공 정책 정의를 사용할 수도 있습니다.
- DDoS Protection 표준을 사용하도록 설정해야 합니다.

또한 Azure Blueprints에서 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, RBAC(역할 기반 액세스 제어) 및 정책)를 단일 청사진 정의로 패키지하여 대규모 Azure 배포를 간소화할 수 있습니다. 청사진을 새 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어와 관리를 세부적으로 조정할 수 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: NSG(네트워크 보안 그룹) 및 네트워크 보안/트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 들어오고 나가는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간 등을 지정할 수 있습니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure API Management 배포와 연결된 네트워크 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft에서 Azure API Management에 대한 시간 원본을 유지 관리합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하거나, 장기/보관 저장 또는 오프라인 분석을 위해 로그를 Azure Storage에 보내거나, Azure Event Hubs를 사용하여 로그를 Azure 및 기타 위치의 다른 분석 솔루션으로 내보냅니다. Azure API Management는 기본적으로 로그 및 메트릭을 Azure Monitor로 출력합니다. 로깅 세부 정보 표시는 서비스 전체 및 API별로 구성할 수 있습니다.

Azure Monitor 외에도 Azure API Management는 하나 이상의 Azure Application Insights 서비스와 통합될 수 있습니다. Application Insights에 대한 로깅 설정은 서비스별 또는 API별로 구성할 수 있습니다.

필요에 따라 데이터를 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)에 사용하도록 설정하고 온보딩합니다.

* [진단 설정을 구성하는 방법](../azure-monitor/platform/diagnostic-settings.md#create-in-azure-portal)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [사용자 지정 로깅 및 분석 파이프라인을 만드는 방법](./api-management-howto-log-event-hubs.md)

* [Azure Application Insights와 통합하는 방법](./api-management-howto-app-insights.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용하도록 설정하고, 활동 로그를 보고 및 분석을 위해 Log Analytics 작업 영역으로, 안전한 장기 보관을 위해 Azure Storage로, Azure 및 기타 위치의 다른 분석 솔루션의 내보내기를 위해 Azure Event Hubs로 보냅니다. Azure 활동 로그 데이터를 사용하면 Azure API Management 서비스의 제어 평면 수준에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 결정할 수 있습니다.

데이터 평면 감사 로깅의 경우 진단 로그는 감사 및 문제 해결에 중요한 작업 및 오류에 대한 다양한 정보를 제공합니다. 진단 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행된 작업에 대한 정보를 제공합니다. 진단 로그는 리소스에서 수행하는 작업에 대한 정보를 제공합니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/platform/activity-log.md)

* [진단 설정을 Azure API Management에 사용하도록 설정하는 방법](./api-management-howto-use-azure-monitor.md#activity-logs)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규칙에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. Azure Storage 계정을 장기/보관 스토리지에 사용합니다.

* [Log Analytics 작업 영역에 대한 로그 보존 기간 매개 변수를 설정하는 방법](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [로그를 Azure Storage 계정에 보관하는 방법](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure API Management는 로그와 메트릭을 지속적으로 Azure Monitor에 내보내 API의 상태를 거의 실시간으로 파악할 수 있는 가시성을 제공합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용하면 API Management 및 관련 리소스에서 제공되는 메트릭과 로그에 대한 작업을 검토, 쿼리, 시각화, 라우팅, 보관, 경고 구성 및 수행할 수 있습니다. 로그를 분석하고 모니터링하여 비정상 동작이 있는지 확인하고 결과를 정기적으로 검토합니다.

필요에 따라 API Management를 Azure Application Insights와 통합하여 기본 또는 보조 모니터링, 추적, 보고 및 경고 도구로 사용합니다.

* [Azure API Management에 대한 로그를 모니터링 및 검토하는 방법](./api-management-howto-use-azure-monitor.md)

* [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

* [Log Analytics 작업 영역 이해](../azure-monitor/log-query/get-started-portal.md)

* [Azure Application Insights와 통합하는 방법](./api-management-howto-app-insights.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure 활동 로그 진단 설정 및 Azure API Management 인스턴스 진단 설정을 사용하도록 설정하고, 로그를 Log Analytics 작업 영역에 보냅니다. Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 파악하며, 패턴을 분석하고, 수집된 데이터를 기반으로 하여 다양한 인사이트를 제공합니다. Log Analytics 작업 영역 쿼리를 기반으로 하여 경고를 만들 수 있습니다.

예기치 않은 상황이 발생하는 경우를 알려주는 메트릭 경고를 만듭니다. 예를 들어 Azure API Management 인스턴스에서 특정 기간 동안 예상 최대 용량을 초과했거나 미리 정의된 기간 동안 특정 수의 권한이 없는 게이트웨이 요청 또는 오류가 발생한 경우 알림을 받습니다.

필요에 따라 API Management를 Azure Application Insights와 통합하여 기본 또는 보조 모니터링, 추적, 보고 및 경고 도구로 사용합니다.

필요에 따라 데이터를 Azure Sentinel 또는 타사 SIEM에 사용하도록 설정하고 온보딩할 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/platform/activity-log.md)

* [진단 설정을 Azure API Management에 사용하도록 설정하는 방법](./api-management-howto-use-azure-monitor.md#activity-logs)

* [권한 없는 요청에 대한 경고 규칙을 구성하는 방법](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule-for-unauthorized-request)

* [Azure API Management 인스턴스의 용량 메트릭을 확인하는 방법](./api-management-capacity.md)

* [Azure Application Insights와 통합하는 방법](./api-management-howto-app-insights.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 없음. Azure API Management는 맬웨어 방지 관련 로그를 처리하거나 생성하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 없음. Azure API Management는 사용자가 액세스할 수 있는 DNS 관련 로그를 처리하거나 생성하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure API Management 제어 평면(Azure Portal)에 대한 관리 권한이 있는 계정의 인벤토리를 유지 관리합니다.

Azure AD(Active Directory)에는 명시적으로 할당되고 쿼리할 수 있는 기본 제공 역할이 있습니다. API Management는 이러한 역할 및 역할 기반 액세스 제어를 사용하여 API Management 서비스 및 엔터티에 대한 세분화된 액세스 관리를 지원합니다.

또한 API Management에는 API Management의 사용자 시스템에 기본 제공 관리자 그룹이 포함되어 있습니다. API Management 그룹의 그룹은 개발자 포털에서 API의 가시성을 제어하고, 관리자 그룹의 멤버는 모든 API를 볼 수 있습니다.

관리 계정의 관리 및 유지 관리에 대한 Azure Security Center의 추천 사항을 따릅니다.

* [Azure API Management에서 역할 기반 액세스 제어를 사용하는 방법](./api-management-role-based-access-control.md)

* [Azure API Management 인스턴스에서 사용자 목록을 가져오는 방법](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [PowerShell을 사용하여 Azure AD에서 디렉터리 역할에 할당 된 사용자 목록을 가져오는 방법](/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [PowerShell을 사용하여 Azure AD에서 디렉터리 역할 정의를 가져오는 방법](/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [Azure Security Center의 ID 및 액세스 추천 사항 이해](../security-center/recommendations-reference.md#recs-identity)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure API Management에는 기본 암호/키에 대한 개념이 없습니다.

그러나 API에 대한 액세스를 보호하는 한 가지 방법으로 생성된 구독 키 쌍이 Azure API Management 구독에 제공됩니다. 고객은 언제든지 이러한 구독 키를 다시 생성할 수 있습니다.

* [Azure API Management 구독 이해](./api-management-subscriptions.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

* [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](../security-center/security-center-identity-access.md)

* [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure AD에서 제공하는 SSO 기능을 활용하기 위해 개발자 포털에서 사용자를 인증하기 위한 ID 공급자로 Azure Active Directory를 활용하도록 Azure API Management를 구성할 수 있습니다. 일단 구성되면 새 개발자 포털 사용자는 먼저 Azure AD를 통해 인증한 다음, 인증이 완료된 포털에서 가입 프로세스를 완료하여 기본 가입 프로세스를 따르도록 선택할 수 있습니다.

* [Azure API Management에서 Azure Active Directory를 사용하여 개발자 계정에 권한 부여](./api-management-howto-aad.md)

또는 위임을 통해 로그인/가입 프로세스를 추가로 사용자 지정할 수 있습니다. 위임을 사용하면 개발자 포털에서 기본 제공 기능을 사용하는 대신 기존 웹 사이트를 사용하여 개발자 로그인/가입 및 제품 구독을 처리할 수 있습니다. 이렇게 하면 웹 사이트에서 사용자 데이터를 소유하고 이러한 단계의 유효성 검사를 사용자 지정 방식으로 수행 할 수 있습니다.

* [사용자 등록 및 제품 구독을 위임하는 방법](./api-management-howto-setup-delegation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Active Directory) MFA(Multi-Factor Authentication)를 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 머신(Privileged Access Workstation) 사용

**지침**: Azure 리소스에 로그인하여 구성하도록 구성된 MFA(Multi-Factor Authentication)를 통해 PAW(Privileged Access Workstation)를 사용합니다.

* [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하려면 Azure AD(Active Directory) PIM(Privileged Identity Management)을 사용합니다.

또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

* [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 Azure Portal에 액세스할 수 있도록 허용합니다.

* [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: 가능하면 Azure AD를 중앙 인증 및 권한 부여 시스템으로 사용합니다. AAD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Azure Active Directory를 사용하여 개발자 계정을 인증하도록 Azure API Management 개발자 포털을 구성합니다.

Azure AD(Active Directory)에서 OAuth 2.0 프로토콜을 사용하여 API를 보호하도록 Azure API Management 인스턴스를 구성합니다.

* [Azure API Management에서 Azure Active Directory를 사용하여 권한을 개발자 계정에 부여하는 방법](./api-management-howto-aad.md)

* [Azure Active Directory 및 API Management에서 OAuth 2.0을 사용하여 API를 보호하는 방법](./api-management-howto-protect-backend-with-aad.md)

* [AAD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 고객은 Azure ID 액세스 검토를 활용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 정기적으로 검토하여 올바른 사용자만 적절한 액세스 권한을 계속 유지하도록 보장할 수 있습니다.

고객은 API Management 사용자 계정의 인벤토리를 유지 관리하고 필요에 따라 액세스를 조정할 수 있습니다. API Management에서 개발자는 API Management에 공개된 API의 소비자입니다. 기본적으로 새로 만든 개발자 계정은 활성 상태이며, 개발자 그룹과 연결됩니다. 활성 상태의 개발자 계정은 구독을 사용하는 모든 API에 액세스하는 데 사용할 수 있습니다.

관리자는 사용자 지정 그룹을 만들거나 연결된 Azure Active Directory 테넌트에서 외부 그룹을 활용할 수 있습니다. 사용자 지정 및 외부 그룹은 시스템 그룹과 함께 사용되어 개발자에게 API 제품에 대한 표시 여부 및 액세스를 제공합니다.

* [Azure API Management에서 사용자 계정을 관리하는 방법](./api-management-howto-create-or-invite-developers.md)

* [API Management 사용자 목록을 가져오는 방법](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Azure API Management에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법](./api-management-howto-create-groups.md)

* [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 대한 액세스 시도 모니터링

**지침**: Azure API Management에서 Azure Active Directory를 ID 공급자로 사용하여 개발자 계정을 인증하도록 Azure API Management 인스턴스를 구성합니다.

Azure AD(Active Directory)에서 OAuth 2.0 프로토콜을 사용하여 API를 보호하도록 Azure API Management 인스턴스를 구성합니다.

유효한 토큰의 존재와 유효성을 적용하는 데 도움이 되도록 들어오는 API 요청에 대한 JWT 유효성 검사 정책을 구성합니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고, 감사 로그 및 로그인 로그를 Log Analytics 작업 영역에 보냅니다. Log Analytics 내에서 원하는 경고를 구성합니다. 또한 Log Analytics 작업 영역을 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

`log-to-eventhub` 정책을 사용하여 API Management에서 고급 모니터링을 구성하고, 보안 분석에 필요한 추가 컨텍스트 정보를 캡처하여 Azure Sentinel 또는 타사 SIEM에 보냅니다.

* [Azure API Management에서 Azure Active Directory를 사용하여 권한을 개발자 계정에 부여하는 방법](./api-management-howto-aad.md)

* [Azure Active Directory 및 API Management에서 OAuth 2.0을 사용하여 API를 보호하는 방법](./api-management-howto-protect-backend-with-aad.md)

* [API Management 액세스 제한 정책](./api-management-access-restriction-policies.md)

* [Azure AD 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [고급 API 모니터링](./api-management-log-to-eventhub-sample.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: 제어 평면(Azure Portal)의 계정 로그인 동작 편차의 경우 Azure AD(Active Directory) ID 보호 및 위험 탐지 기능을 사용하여 사용자 ID와 관련하여 탐지된 의심스러운 작업에 대한 자동화된 대응을 구성합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 현재 사용할 수 없음. Customer Lockbox는 현재 Azure API Management에서 지원되지 않습니다.

* [Customer Lockbox 지원 서비스 목록](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위해 별도의 구독 및/또는 관리 그룹을 구현합니다. Azure API Management 인스턴스는 VNet(가상 네트워크)/서브넷으로 구분하고 태그를 적절하게 지정해야 합니다.

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [가상 네트워크에서 Azure API Management를 사용하는 방법](./api-management-using-with-vnet.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 현재 사용할 수 없음. 데이터 식별, 분류 및 손실 방지 기능은 현재 Azure API Management에서 사용할 수 없습니다.

Microsoft는 Azure API Management에 대한 기본 인프라를 관리하고, 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 관리 평면 호출은 TLS를 통해 Azure Resource Manager에서 수행됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다. 데이터 평면 호출은 TLS 및 지원되는 인증 메커니즘(예: 클라이언트 인증서 또는 JWT) 중 하나를 사용하여 보호할 수 있습니다.

* [Azure API Management의 데이터 보호 이해](./api-management-security-controls.md#data-protection)

* [Azure API Management에서 TLS 설정 관리](./api-management-howto-manage-protocols-ciphers.md)

* [Azure Active Directory를 사용하여 Azure API Management에서 API 보호](./api-management-howto-protect-backend-with-aad.md)

* [Azure Active Directory B2C를 사용하여 Azure API Management에서 API 보호](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 현재 사용할 수 없음. 데이터 식별, 분류 및 손실 방지 기능은 현재 Azure API Management에서 사용할 수 없습니다. 중요한 정보를 처리할 수 있는 Azure API Management 서비스에 태그를 지정하고, 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: 역할 기반 액세스 제어를 사용하여 Azure API Management에 대한 액세스를 제어합니다. Azure API Management는 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 API Management 서비스 및 엔터티 (예: Api 및 정책)에 대 한 세분화 액세스 관리를 지원 합니다.

* [Azure API Management에서 역할 기반 액세스 제어를 사용하는 방법](./api-management-role-based-access-control.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

Microsoft는 Azure API Management에 대한 기본 인프라를 관리하고, 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 명명된 인증서, 키 및 비밀 값과 같은 중요한 데이터는 서비스 인스턴스 키당 서비스 관리형 키를 사용하여 암호화됩니다. 모든 암호화 키는 서비스 인스턴스당 키이며 서비스에서 관리됩니다.

* [Azure API Management를 사용한 저장 데이터 보호/암호화 이해](./api-management-security-controls.md#data-protection)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure Monitor에서 Azure 활동 로그를 사용하여 프로덕션 Azure Functions 앱 및 기타 중요하거나 관련된 리소스가 변경되는 경우에 대한 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

* [Azure API Management에서 Azure Monitor 및 Azure 활동 로그를 사용하는 방법](./api-management-howto-use-azure-monitor.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 현재 사용할 수 없음. Azure Security Center의 취약성 평가는 현재 Azure API Management에서 사용할 수 없습니다.

Microsoft에서 검사하고 패치하는 기본 플랫폼입니다. 서비스 구성 관련 취약성을 줄이기 위해 사용 가능한 보안 제어를 검토합니다.

* [Azure API Management에서 사용할 수 있는 보안 제어 이해](./api-management-security-controls.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 현재 사용할 수 없음. Azure Security Center의 취약성 평가는 현재 Azure API Management에서 사용할 수 없습니다.

Microsoft에서 검사하고 패치하는 기본 플랫폼입니다. 고객이 서비스 구성 관련 취약성을 줄이기 위해 사용 가능한 보안 제어를 검토합니다.

* [Azure API Management에서 사용할 수 있는 보안 제어 이해](./api-management-security-controls.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

* [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

* [태그를 만들고 활용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 애플리케이션 목록 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: 사용자가 스크립트를 통해 Azure Resource Manager와 상호 작용하는 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

* [Azure API Management의 역할 기반 액세스 제어](./api-management-role-based-access-control.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용하여 Azure API Management 서비스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.ApiManagement" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure API Management 서비스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy를 사용하여 Azure API Management 서비스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.ApiManagement" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure API Management 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure Repos를 사용하여 Azure API Management 서비스 구성을 안전하게 저장하고 관리합니다.

* [파일을 Azure DevOps에 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](/azure/devops/repos/index?view=azure-devops)

* [Azure API Management DevOps 리소스 키트 이해](./api-management-security-controls.md#configuration-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure Policy를 사용하여 Azure API Management 서비스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.ApiManagement" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure API Management 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure API Management DevOps 리소스 키트를 사용하여 Azure API Management에 대한 구성 관리를 수행합니다.

또한 Azure Policy를 사용하여 Azure API Management 서비스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.ApiManagement" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure API Management 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

* [Azure API Management DevOps 리소스 키트 이해](./api-management-security-controls.md#configuration-management)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Key Vault를 사용하여 인증서를 관리하고 이러한 인증서를 autorotate(자동 회전)로 설정합니다. Azure Key Vault를 사용하여 사용자 지정 도메인 SSL 인증서를 관리하는 경우 인증서가 비밀이 아니라 인증서로 Key Vault에 삽입되었는지 확인합니다.

* [Key Vault 키 순환에 대한 지침을 사용하여 사용자 지정 도메인 이름을 설정하는 방법](./configure-custom-domain.md)

**Azure Security Center 모니터링**: 예

**책임**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure AD(Active Directory)에서 생성된 관리 서비스 ID를 사용하여 API Management 인스턴스에서 다른 Azure AD 보호 리소스(예: Azure Key Vault)에 쉽고 안전하게 액세스할 수 있도록 허용합니다.

* [API Management 인스턴스에 대한 관리 ID를 만드는 방법](./api-management-howto-use-managed-service-identity.md)

* [관리 ID를 사용하여 인증하는 정책](./api-management-authentication-policies.md#ManagedIdentity)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스(예: Azure API Management)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: 해당 없음. 이 추천 사항은 데이터를 저장하도록 설계된 비 컴퓨팅 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스(예: Azure API Management)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 추천 사항은 데이터를 저장하도록 설계된 비 컴퓨팅 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스(예: Azure API Management)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure API Management를 통해 API를 게시 및 관리하면 기존에는 수동으로 설계, 구현 및 관리하던 내결함성 및 인프라 기능을 활용할 수 있습니다. API Management는 다중 지역 배포를 지원하지만 운영 오버헤드를 추가하지 않고 데이터 평면이 지역 장애의 영향을 받지 않습니다.

API Management의 서비스 백업 및 복원 기능은 재해 복구 전략을 구현하는 데 필요한 구성 요소를 제공합니다. 백업 및 복원 작업은 수동 또는 자동으로 수행할 수 있습니다.

* [API Management 데이터 평면을 여러 지역에 배포하는 방법](./api-management-howto-deploy-multi-region.md)

* [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [API Management 백업 작업을 호출하는 방법](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [API Management 복원 작업을 호출하는 방법](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure API Management에서 제공하는 백업 및 복원 작업은 전체 시스템 백업 및 복원을 수행합니다.

관리 ID는 Azure Key Vault에서 API Management 사용자 지정 도메인 이름에 대한 인증서를 가져오는 데 사용할 수 있습니다. Azure Key Vault 내에 저장되는 인증서를 모두 백업합니다.

* [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Azure Key Vault 인증서를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 백업에서 서비스 및 인증서의 테스트 복원을 수행하여 백업의 유효성을 검사합니다.

* [API Management 복원 작업을 호출하는 방법](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [Azure Key Vault 인증서를 복원하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure API Management는 백업을 고객 소유의 Azure Storage 계정에 씁니다. Azure Storage 보안 추천 사항에 따라 백업을 보호합니다.

* [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Blob 스토리지에 대한 보안 추천 사항](../storage/blobs/security-recommendations.md)

Key Vault에서 일시 삭제를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

* [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

* [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

* [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

* [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

* [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

* [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

* [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

* [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 활용하여 "Logic Apps"를 통해 보안 경고 및 추천 사항에 대한 대응을 자동으로 트리거합니다.

* [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행하고, 모든 중요한 보안 결과를 60일 이내에 수정해야 합니다.

**지침**: * [Microsoft Rules of Engagement에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인하세요.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [여기서 Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략 및 실행에 대한 자세한 내용을 확인할 수 있습니다.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
