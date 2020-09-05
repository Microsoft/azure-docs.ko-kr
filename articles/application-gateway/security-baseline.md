---
title: Azure 애플리케이션 Gateway에 대 한 Azure 보안 기준
description: Azure 애플리케이션 Gateway에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b470fa72518f805c10403931280bdec96bda0fbe
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226542"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>Azure 애플리케이션 Gateway에 대 한 Azure 보안 기준

Azure 애플리케이션 게이트웨이의 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 모든 Virtual Network Azure 애플리케이션 게이트웨이 서브넷 배포에는 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 특정 한 네트워크 액세스 제어와 함께 nsg (네트워크 보안 그룹)가 적용 되어 있는지 확인 합니다. 네트워크 보안 그룹은 Azure 애플리케이션 게이트웨이에서 지원 되지만, NSG 및 Azure 애플리케이션 게이트웨이가 예상 대로 작동 하기 위해 준수 해야 하는 몇 가지 제한 사항 및 요구 사항이 있습니다.

* [Azure 애플리케이션 게이트웨이에서 NSGs 사용에 대 한 제한 사항 이해](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure 애플리케이션 게이트웨이 서브넷과 연결 된 nsgs (네트워크 보안 그룹)에 대해 nsgs 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

참고: Azure 애플리케이션 게이트웨이 서브넷과 연결 된 NSG 흐름 로그가 허용 된 트래픽을 표시 하지 않는 경우가 있습니다. 구성이 다음 시나리오와 일치하는 경우 NSG 흐름 로그에 허용된 트래픽이 표시되지 않습니다.
- Application Gateway v2를 배포했습니다.
- 애플리케이션 게이트웨이 서브넷에 NSG가 있습니다.
- 이 NSG에서 NSG 흐름 로그를 사용하도록 설정했습니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Azure 애플리케이션 게이트웨이의 진단 및 로깅에 대 한 FAQ](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 들어오는 트래픽의 추가 검사를 위해 중요 한 웹 응용 프로그램 앞에 AZURE Waf (웹 응용 프로그램 방화벽)를 배포 합니다. WAF (웹 응용 프로그램 방화벽)는 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 하는 서비스 (Azure 애플리케이션 게이트웨이의 기능)입니다. Azure WAF는 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드, DDoS 공격 등의 공격을 차단 하기 위해 인바운드 웹 트래픽을 검사 하 여 Azure App Service 웹 앱을 보호 하는 데 도움이 될 수 있습니다. WAF는 OWASP(Open Web Application Security Project) 핵심 규칙 집합 3.1(WAF_v2만 해당), 3.0 또는 2.2.9의 규칙에 기반합니다.

* [Azure 애플리케이션 게이트웨이 기능 이해](https://docs.microsoft.com/azure/application-gateway/features)

* [Azure WAF 이해](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Azure WAF를 배포 하는 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격 으로부터 보호 하기 위해 Azure 애플리케이션 게이트웨이의 프로덕션 인스턴스와 연결 된 Azure 가상 네트워크에서 DDoS Standard 보호를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용 하 여 알려진 악성 IP 주소와의 통신을 거부 합니다.

* [DDoS 보호를 구성 하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Security Center 통합 위협 인텔리전스 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Azure 애플리케이션 게이트웨이 서브넷과 연결 된 nsgs (네트워크 보안 그룹)에 대해 nsgs 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

참고: Azure 애플리케이션 게이트웨이 서브넷과 연결 된 NSG 흐름 로그가 허용 된 트래픽을 표시 하지 않는 경우가 있습니다. 구성이 다음 시나리오와 일치하는 경우 NSG 흐름 로그에 허용된 트래픽이 표시되지 않습니다.
- Application Gateway v2를 배포했습니다.
- 애플리케이션 게이트웨이 서브넷에 NSG가 있습니다.
- 이 NSG에서 NSG 흐름 로그를 사용하도록 설정했습니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Azure 애플리케이션 게이트웨이의 진단 및 로깅에 대 한 FAQ](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: 들어오는 트래픽의 추가 검사를 위해 중요 한 웹 응용 프로그램 앞에 AZURE Waf (웹 응용 프로그램 방화벽)를 배포 합니다. WAF (웹 응용 프로그램 방화벽)는 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 하는 서비스 (Azure 애플리케이션 게이트웨이의 기능)입니다. Azure WAF는 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드, DDoS 공격 등의 공격을 차단 하기 위해 인바운드 웹 트래픽을 검사 하 여 Azure App Service 웹 앱을 보호 하는 데 도움이 될 수 있습니다. WAF는 OWASP(Open Web Application Security Project) 핵심 규칙 집합 3.1(WAF_v2만 해당), 3.0 또는 2.2.9의 규칙에 기반합니다.

또는 IDS/IPS 기능을 포함 하는 Azure Marketplace에서 사용할 수 있는 Azure 용 Barracuda WAF와 같은 여러 marketplace 옵션이 있습니다.

* [Azure 애플리케이션 게이트웨이 기능 이해](https://docs.microsoft.com/azure/application-gateway/features)

* [Azure WAF 이해](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Azure WAF를 배포 하는 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Barracuda WAF 클라우드 서비스 이해](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 신뢰할 수 있는 인증서에 대해 HTTPS/SSL을 사용 하도록 설정 된 웹 응용 프로그램용 Azure 애플리케이션 게이트웨이를 배포 합니다.

* [Application Gateway 배포 하는 방법](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [HTTPS를 사용 하도록 Application Gateway를 구성 하는 방법](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [Azure 웹 응용 프로그램 게이트웨이를 사용 하 여 계층 7 부하 분산 이해](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Virtual Network 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: Gmanager)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

Azure 애플리케이션 Gateway 서브넷과 연결 된 NSGs (네트워크 보안 그룹)의 경우, Application Gateway v1 SKU에 대 한 TCP 포트 65503-65534에서 들어오는 인터넷 트래픽을 허용 하 고, 대상 서브넷이 있는 v2 SKU의 경우 TCP 포트 65200-65535을 사용 하도록 허용 해야 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호 (잠김) 됩니다. 이러한 끝점에서는 외부 엔터티 (해당 게이트웨이의 고객 포함)를 통신할 수 없습니다.

* [서비스 태그 이해 및 사용](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Azure 애플리케이션 게이트웨이 구성 개요](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure 애플리케이션 게이트웨이 배포와 관련 된 네트워크 설정에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure 애플리케이션 게이트웨이, Azure 가상 네트워크 및 네트워크 보안 그룹의 네트워크 구성을 감사 하거나 적용 합니다. 기본 제공 정책 정의를 사용할 수도 있습니다.

또한 Azure 청사진을 사용 하 여 Azure Resource Manager 템플릿, azure RBAC (역할 기반 액세스 제어) 및 단일 청사진 정의의 정책과 같은 주요 환경 아티팩트를 패키지 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 청사진을 새 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어와 관리를 세부적으로 조정할 수 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스 뿐만 아니라 Azure 애플리케이션 Gateway 서브넷과 연결 된 nsgs (네트워크 보안 그룹)에 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure 정책 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Virtual Network를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [보안 구성을 사용하여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure 애플리케이션 Gateway 배포와 관련 된 네트워크 설정 및 리소스에 대 한 변경 내용을 검색 합니다. 중요 한 네트워크 설정 또는 리소스에 대 한 변경이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft는 Azure App Service와 같은 Azure 리소스에 사용 되는 시간 원본을 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역, azure 이벤트 허브 또는 azure storage 계정으로 로그를 보냅니다. Azure 활동 로그 데이터를 사용 하 여 Azure 애플리케이션 게이트웨이의 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정 하 고, Azure 애플리케이션 게이트웨이 서브넷을 보호 하는 데 사용 되는 NSGs (네트워크 보안 그룹)와 같은 관련 리소스를 결정할 수 있습니다.

활동 로그 외에도 Azure 애플리케이션 게이트웨이 배포에 대 한 진단 설정을 구성할 수 있습니다. 진단 설정은 사용자가 선택한 대상 (저장소 계정, Event Hubs 및 Log Analytics)으로 리소스에 대 한 플랫폼 로그 및 메트릭의 스트리밍 내보내기를 구성 하는 데 사용 됩니다.

또한 Azure 애플리케이션 게이트웨이는 Azure 애플리케이션 Insights와의 기본 제공 통합 기능을 제공 합니다. Application Insights는 로그, 성능 및 오류 데이터를 수집 합니다. Application Insights는 성능 변칙을 자동으로 감지 하 고, 문제를 진단 하 고 웹 앱을 사용 하는 방법을 이해 하는 데 도움이 되는 강력한 분석 도구를 포함 합니다. 연속 내보내기를 사용 하 여 Application Insights에서 중앙 위치에 원격 분석을 내보내 표준 보존 기간 보다 오래 지속 되도록 데이터를 유지할 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure 애플리케이션 Gateway에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Application Insights를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역, azure 이벤트 허브 또는 azure storage 계정으로 로그를 보냅니다. Azure 활동 로그 데이터를 사용 하 여 Azure 애플리케이션 게이트웨이의 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정 하 고, Azure 애플리케이션 게이트웨이 서브넷을 보호 하는 데 사용 되는 NSGs (네트워크 보안 그룹)와 같은 관련 리소스를 결정할 수 있습니다.

활동 로그 외에도 Azure 애플리케이션 게이트웨이 배포에 대 한 진단 설정을 구성할 수 있습니다. 진단 설정은 사용자가 선택한 대상 (저장소 계정, Event Hubs 및 Log Analytics)으로 리소스에 대 한 플랫폼 로그 및 메트릭의 스트리밍 내보내기를 구성 하는 데 사용 됩니다.

또한 Azure 애플리케이션 게이트웨이는 Azure 애플리케이션 Insights와의 기본 제공 통합 기능을 제공 합니다. Application Insights는 로그, 성능 및 오류 데이터를 수집 합니다. Application Insights는 성능 변칙을 자동으로 감지 하 고, 문제를 진단 하 고 웹 앱을 사용 하는 방법을 이해 하는 데 도움이 되는 강력한 분석 도구를 포함 합니다. 연속 내보내기를 사용 하 여 Application Insights에서 중앙 위치에 원격 분석을 내보내 표준 보존 기간 보다 오래 지속 되도록 데이터를 유지할 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure 애플리케이션 Gateway에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Application Insights를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규칙에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. Azure Storage 계정을 장기/보관 스토리지에 사용합니다.

* [Log Analytics 작업 영역에 대한 로그 보존 기간 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure 활동 로그 진단 설정 및 Azure 애플리케이션 게이트웨이의 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 파악하며, 패턴을 분석하고, 수집된 데이터를 기반으로 하여 다양한 인사이트를 제공합니다.

네트워크에 대 한 Azure Monitor를 사용 하 여 Azure 애플리케이션 게이트웨이를 비롯 한 모든 배포 된 네트워크 리소스에 대 한 상태 및 메트릭의 포괄적인 보기를 사용 합니다.

필요에 따라 데이터를 Azure Sentinel 또는 타사 SIEM에 사용하도록 설정하고 온보딩할 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure 애플리케이션 Gateway에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [네트워크에 Azure Monitor를 사용 하는 방법](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 들어오는 트래픽에 대 한 추가 검사를 위해 중요 한 웹 응용 프로그램 앞에 AZURE Waf (웹 응용 프로그램 방화벽) v2 SKU를 배포 합니다. WAF (웹 응용 프로그램 방화벽)는 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 하는 서비스 (Azure 애플리케이션 게이트웨이의 기능)입니다. Azure WAF는 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드, DDoS 공격 등의 공격을 차단 하기 위해 인바운드 웹 트래픽을 검사 하 여 Azure App Service 웹 앱을 보호 하는 데 도움이 될 수 있습니다. WAF는 OWASP(Open Web Application Security Project) 핵심 규칙 집합 3.1(WAF_v2만 해당), 3.0 또는 2.2.9의 규칙에 기반합니다.

Azure 활동 로그 진단 설정 및 Azure WAF의 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 파악하며, 패턴을 분석하고, 수집된 데이터를 기반으로 하여 다양한 인사이트를 제공합니다. Log Analytics 작업 영역 쿼리를 기반으로 하여 경고를 만들 수 있습니다.

네트워크에 대 한 Azure Monitor를 사용 하 여 Azure 애플리케이션 게이트웨이를 비롯 한 모든 배포 된 네트워크 리소스에 대 한 상태 및 메트릭의 포괄적인 보기를 사용 합니다. 네트워크 콘솔의 Azure Monitor 내에서 Azure 애플리케이션 게이트웨이에 대 한 경고를 보고 만들 수 있습니다.

* [Azure WAF를 배포 하는 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure 애플리케이션 Gateway에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [네트워크에 Azure Monitor를 사용 하는 방법](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

* [Azure 내에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 들어오는 트래픽에 대 한 추가 검사를 위해 중요 한 웹 응용 프로그램 앞에 AZURE Waf (웹 응용 프로그램 방화벽) v2를 배포 합니다. WAF (웹 응용 프로그램 방화벽)는 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 하는 서비스 (Azure 애플리케이션 게이트웨이의 기능)입니다. Azure WAF는 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드, DDoS 공격 등의 공격을 차단 하기 위해 인바운드 웹 트래픽을 검사 하 여 Azure App Service 웹 앱을 보호 하는 데 도움이 될 수 있습니다.

Azure 애플리케이션 게이트웨이 배포에 대 한 진단 설정을 구성 합니다. 진단 설정은 사용자가 선택한 대상 (저장소 계정, Event Hubs 및 Log Analytics)으로 리소스에 대 한 플랫폼 로그 및 메트릭의 스트리밍 내보내기를 구성 하는 데 사용 됩니다.

* [Azure WAF를 배포 하는 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Azure WAF에 대 한 진단 설정을 구성 하는 방법](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Azure 애플리케이션 게이트웨이는 사용자가 액세스할 수 있는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: AD (Azure Active Directory)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure 애플리케이션 게이트웨이에 대 한 제어 평면 액세스는 AD (Azure Active Directory)를 통해 제어 됩니다. Azure AD에는 기본 암호 개념이 없습니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

* [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure Policy를 사용하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure 앱 등록 (서비스 주체)을 사용 하 여 API 호출을 통해 Azure 애플리케이션 게이트웨이와 상호 작용 하는 데 사용할 수 있는 토큰을 검색 합니다.

* [Azure REST Api를 호출 하는 방법](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Azure AD를 사용 하 여 클라이언트 응용 프로그램 (서비스 사용자)을 등록 하는 방법](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Recovery Services API 정보](https://docs.microsoft.com/rest/api/recoveryservices/)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD MFA를 사용하도록 설정하고 Azure Security Center ID 및 액세스 관리 권장 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 머신(권한 있는 액세스 워크스테이션) 사용

**지침**: MFA가 구성된 PAW(Privileged Access Workstation)를 사용하여 Azure 리소스에 로그인하고 구성합니다.

* [권한 있는 액세스 워크스테이션에 대해 알아보기](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고 생성에 Azure Active Directory 보안 보고서를 사용 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

* [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

* [Azure에서 명명된 위치를 구성하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: AZURE ACTIVE DIRECTORY (AAD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. AAD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 AAD는 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.

* [AAD 인스턴스를 만들고 구성하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

* [Azure AD 보고 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure ID 액세스 검토를 사용하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 소스에 액세스가 가능하므로 SIEM/모니터링 툴과 통합할 수 있습니다.

Azure Active Directory 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD ID 보호 및 위험 검색 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 해당 사항 없음 고객 Lockbox Azure 애플리케이션 게이트웨이에는 적용 되지 않습니다.

* [Customer Lockbox 지원 서비스 목록](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스를 추적 하는 데 도움을 줍니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 모든 Virtual Network Azure 애플리케이션 게이트웨이 서브넷 배포에 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 특정 한 네트워크 액세스 제어와 함께 NSG (네트워크 보안 그룹)가 적용 되어 있는지 확인 합니다. 네트워크 보안 그룹은 Azure 애플리케이션 게이트웨이에서 지원 되지만, NSG 및 Azure 애플리케이션 게이트웨이가 예상 대로 작동 하기 위해 준수 해야 하는 몇 가지 제한 사항 및 요구 사항이 있습니다.

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Azure 애플리케이션 게이트웨이에서 NSGs 사용에 대 한 제한 사항 이해](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 모든 Virtual Network Azure 애플리케이션 게이트웨이 서브넷 배포에는 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 특정 한 네트워크 액세스 제어와 함께 nsg (네트워크 보안 그룹)가 적용 되어 있는지 확인 합니다. 아웃 바운드 트래픽을 신뢰할 수 있는 위치로 제한 하 여 데이터 exfiltration의 위협을 완화할 수 있습니다. 네트워크 보안 그룹은 Azure 애플리케이션 게이트웨이에서 지원 되지만, NSG 및 Azure 애플리케이션 게이트웨이가 예상 대로 작동 하기 위해 준수 해야 하는 몇 가지 제한 사항 및 요구 사항이 있습니다.

* [Azure 애플리케이션 게이트웨이에서 NSGs 사용에 대 한 제한 사항 이해](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure 애플리케이션 게이트웨이에 대해 TLS를 사용 하 여 종단 간 암호화를 구성 합니다.

* [Azure 애플리케이션 Gateway를 사용 하 여 종단 간 TLS를 구성 하는 방법](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 해당 없음 Azure 애플리케이션 게이트웨이는 미사용 고객 데이터를 저장 하지 않습니다.

Microsoft는 Azure 애플리케이션 Gateway에 대 한 기본 인프라를 관리 하 고, 고객 데이터 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure 애플리케이션 게이트웨이 제어 평면 (Azure Portal)에 대 한 액세스를 제어 합니다.

* [Azure RBAC를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 해당 사항 없음 Azure 애플리케이션 게이트웨이에서는 고객 데이터를 저장 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 변경 내용이 프로덕션 Azure 애플리케이션 게이트웨이 인스턴스 및 기타 중요 하거나 관련 된 리소스에 적용 되는 경우에 대 한 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 현재 사용할 수 없음 Azure Security Center의 취약성 평가는 아직 Azure 애플리케이션 게이트웨이에 사용할 수 없습니다.

Microsoft에서 검사하고 패치하는 기본 플랫폼입니다. 서비스 구성 관련 취약성을 줄이기 위해 Azure 애플리케이션 게이트웨이에 사용할 수 있는 보안 제어를 검토 합니다.

* [Azure PaaS 서비스에 대 한 기능 범위 (취약성 평가 포함)](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 아직 사용할 수 없음 Azure Security Center의 취약성 평가는 아직 Azure 애플리케이션 게이트웨이에 사용할 수 없습니다.

Microsoft에서 검사하고 패치하는 기본 플랫폼입니다. 서비스 구성 관련 취약성을 줄이기 위해 Azure 애플리케이션 게이트웨이에 사용할 수 있는 보안 제어를 검토 합니다.

* [Azure PaaS 서비스에 대 한 기능 범위 (취약성 평가 포함)](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 아직 사용할 수 없음 Azure Security Center의 취약성 평가는 아직 Azure 애플리케이션 게이트웨이에 사용할 수 없습니다.

Microsoft에서 검사하고 패치하는 기본 플랫폼입니다. 서비스 구성 관련 취약성을 줄이기 위해 Azure 애플리케이션 게이트웨이에 사용할 수 있는 보안 제어를 검토 합니다.

* [Azure PaaS 서비스에 대 한 기능 범위 (취약성 평가 포함)](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 Asset Discovery 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류 체계로 구성합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 승인 된 Azure 리소스를 정의 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스의 유형에 대 한 제한을 설정할 수 있습니다.

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 모든 Virtual Network Azure 애플리케이션 게이트웨이 서브넷 배포에 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 특정 한 네트워크 액세스 제어와 함께 NSG (네트워크 보안 그룹)가 적용 되어 있는지 확인 합니다. 네트워크 보안 그룹은 Azure 애플리케이션 게이트웨이에서 지원 되지만, NSG 및 Azure 애플리케이션 게이트웨이가 예상 대로 작동 하기 위해 준수 해야 하는 몇 가지 제한 사항 및 요구 사항이 있습니다.

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Azure 애플리케이션 게이트웨이에서 NSGs 사용에 대 한 제한 사항 이해](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure 애플리케이션 게이트웨이 배포와 관련 된 네트워크 설정에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure 애플리케이션 게이트웨이, Azure 가상 네트워크 및 네트워크 보안 그룹의 네트워크 구성을 감사 하거나 적용 합니다. 기본 제공 정책 정의를 사용할 수도 있습니다.

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: azure 정책 [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 azure 리소스에서 보안 설정을 적용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

* [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 네트워크" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 네트워크" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure 정책을 사용 하 여 azure 리소스에 대 한 구성을 자동으로 적용 하려면 [감사], [거부] 및 [없는 경우 배포]를 사용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 관리 되는 id를 사용 하 여 AD (Azure Active Directory)에서 Azure 애플리케이션 게이트웨이를 자동으로 관리 되는 id로 제공 합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

Azure Key Vault를 사용 하 여 인증서를 안전 하 게 저장 합니다. Azure Key Vault는 암호, 키 및 SSL 인증서를 보호 하는 데 사용할 수 있는 플랫폼 관리 암호 저장소입니다. Azure Application Gateway는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 이 지원은 Application Gateway v2 SKU로 제한됩니다.

* [Azure PowerShell를 사용 하 여 Key Vault 인증서로 SSL 종료를 구성 하는 방법](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 되는 id를 사용 하 여 AD (Azure Active Directory)에서 Azure 애플리케이션 게이트웨이를 자동으로 관리 되는 id로 제공 합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

Azure Key Vault를 사용 하 여 인증서를 안전 하 게 저장 합니다. Azure Key Vault는 암호, 키 및 SSL 인증서를 보호 하는 데 사용할 수 있는 플랫폼 관리 암호 저장소입니다. Azure Application Gateway는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 이 지원은 Application Gateway v2 SKU로 제한됩니다.

* [Azure PowerShell를 사용 하 여 Key Vault 인증서로 SSL 종료를 구성 하는 방법](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리 맬웨어 방지 소프트웨어 사용

**지침**: 들어오는 트래픽에 대 한 추가 검사를 위해 중요 한 웹 응용 프로그램 앞에 AZURE Waf (웹 응용 프로그램 방화벽) v2를 배포 합니다. WAF (웹 응용 프로그램 방화벽)는 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 하는 서비스 (Azure 애플리케이션 게이트웨이의 기능)입니다. Azure WAF는 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드, DDoS 공격 등의 공격을 차단 하기 위해 인바운드 웹 트래픽을 검사 하 여 Azure App Service 웹 앱을 보호 하는 데 도움이 될 수 있습니다.

Azure 애플리케이션 게이트웨이 배포에 대 한 진단 설정을 구성 합니다. 진단 설정은 사용자가 선택한 대상 (저장소 계정, Event Hubs 및 Log Analytics)으로 리소스에 대 한 플랫폼 로그 및 메트릭의 스트리밍 내보내기를 구성 하는 데 사용 됩니다.

* [Azure WAF를 배포 하는 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Azure WAF에 대 한 진단 설정을 구성 하는 방법](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: 해당 사항 없음 Azure 애플리케이션 게이트웨이에서는 고객 데이터를 저장 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: AZURE Waf (웹 응용 프로그램 방화벽)를 사용 하는 경우 waf 정책을 구성할 수 있습니다. WAF 정책은 고객에 의해 작성 된 사용자 지정 규칙과 Azure에서 관리 하는 미리 구성 된 규칙 집합의 컬렉션인 관리 되는 규칙 집합 이라는 두 가지 유형의 보안 규칙으로 구성 됩니다. Azure 관리형 규칙 세트는 일반적인 보안 위협에 대한 보호를 쉽게 배포할 수 있는 방법을 제공합니다. 이러한 규칙 세트는 Azure에서 관리되므로 새로운 공격 서명으로부터 보호하기 위해 필요에 따라 규칙을 업데이트합니다.

* [Azure 관리 WAF 규칙 집합 이해](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-policy)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure 애플리케이션 게이트웨이에는 고객 데이터가 저장 되지 않습니다. 그러나 사용자 지정 Azure 정책 정의를 사용 하는 경우 Azure DevOps 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

Azure DevOps Services는 여러 Azure storage 기능을 활용 하 여 하드웨어 오류, 서비스 중단 또는 지역 재해 시 데이터 가용성을 보장 합니다. 또한 Azure DevOps 팀은 데이터를 실수로 또는 악의적으로 삭제 하지 않도록 보호 하는 절차를 따릅니다.

* [Azure DevOps의 데이터 가용성 이해](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure Key Vault 내에서 고객 관리 인증서를 백업 합니다.

* [Azure에서 주요 자격 증명 모음 인증서를 백업 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 백업 된 고객 관리 인증서의 복원 테스트

* [Key vault 인증서를 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure Key Vault에 대해 일시 삭제를 사용 하도록 설정 되어 있는지 확인 합니다. 일시 삭제는 키, 암호 및 인증서와 같은 삭제 된 key vault 및 자격 증명 모음 개체를 복구할 수 있습니다.

* [Azure Key Vault의 일시 삭제를 사용 하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

* [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

* [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

* [Azure Security Center 보안 연락처를 설정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고 Sentinel을 스트리밍할 수 있습니다.

* [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

* [경고를 Azure Sentinel로 스트림하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화와 Logic Apps를 구성하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: 

* [Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 레드 팀 및 라이브 사이트 침투 테스트 실행에 대한 자세한 내용은 ](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 참조하세요.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
