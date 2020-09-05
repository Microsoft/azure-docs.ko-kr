---
title: Logic Apps에 대 한 Azure 보안 기준
description: Logic Apps에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 263f018155aa6effada3d509c907d825b65a8d45
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228395"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Logic Apps에 대 한 Azure 보안 기준

Logic Apps에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: "전역" 다중 테 넌 트 Logic Apps 서비스에서 실행 되는 커넥터는 Microsoft에서 배포 하 고 관리 합니다. 이러한 커넥터는 Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint 등을 포함 하 여 클라우드 서비스, 온-프레미스 시스템 또는 둘 다에 액세스 하기 위한 트리거와 작업을 제공 합니다. 네트워크 보안 그룹 또는 Azure 방화벽에 대 한 규칙을 지정 하는 경우 AzureConnectors 서비스 태그를 사용 하 여 관련 리소스에 대 한 액세스를 허용할 수 있습니다.

Azure 가상 네트워크의 리소스에 직접 액세스 해야 하는 논리 앱의 경우 전용 리소스에서 논리 앱을 빌드, 배포 및 실행할 수 있는 ISE (통합 서비스 환경)를 만들 수 있습니다. 일부 Azure virtual network는 azure에서 호스트 되는 Azure Storage, Azure Cosmos DB, Azure SQL Database, 파트너 서비스 또는 고객 서비스와 같은 Azure PaaS 서비스에 대 한 액세스를 제공 하기 위해 개인 끝점 (Azure 개인 링크)을 사용 합니다. 논리 앱에서 개인 끝점을 사용 하는 가상 네트워크에 액세스 해야 하는 경우에는 ISE 내에서 해당 논리 앱을 만들고, 배포 하 고, 실행 해야 합니다.

ISE를 만들 때 내부 또는 외부 액세스 끝점 중 하나를 사용 하도록 선택할 수 있습니다. 선택은 ISE의 논리 앱에 대 한 요청 또는 webhook 트리거가 가상 네트워크 외부에서 호출을 받을 수 있는지 여부를 결정 합니다. 내부 및 외부 액세스 끝점은 가상 네트워크 내부 또는 외부에서 실행에 대 한 입력 및 출력을 포함 하 여 논리 앱의 실행 기록을 볼 수 있는지 여부에도 영향을 줍니다.

ISE와 관련 된 모든 가상 네트워크 서브넷 배포에 네트워크 보안 그룹이 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 특정 한 네트워크 액세스 제어와 함께 적용 되었는지 확인 합니다. ISE에서 논리 앱을 배포 하는 경우 개인 링크를 사용 합니다. Azure 개인 링크를 사용 하면 가상 네트워크의 개인 끝점을 통해 azure PaaS 서비스 및 Azure에서 호스트 되는 고객 소유/파트너 서비스에 액세스할 수 있습니다. 또는 특정 사용 사례가 있는 경우 Azure 방화벽을 구현 하 여이 요구 사항을 충족할 수 있습니다. 보안 규칙을 설정할 때 복잡성을 줄이려면 특정 Azure 서비스에 대 한 IP 주소 접두사 그룹을 나타내는 서비스 태그를 사용 합니다.

* [Logic Apps 커넥터 이해](../connectors/apis-list.md)

* [Azure의 서비스 태그 이해](../virtual-network/service-tags-overview.md)

* [ISEs (integration service environment)를 사용 하 여 Azure Logic Apps에서 Azure Virtual Network 리소스에 대 한 액세스 이해](./connect-virtual-network-vnet-isolated-environment-overview.md)

* [Virtual Network 서비스 끝점 이해](../virtual-network/virtual-network-service-endpoints-overview.md)

* [Azure 개인 링크 이해](../private-link/private-link-overview.md)

* [ISE 끝점 액세스 이해](./connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure 방화벽을 배포 및 구성 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [ISE에 대 한 액세스를 사용 하도록 설정 하는 방법](./connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: 외부 액세스 지점을 사용 하는 ISE (integration service environment)에서 논리 앱을 실행 하는 경우 nsg (네트워크 보안 그룹)를 사용 하 여 데이터 exfiltration의 위험을 줄일 수 있습니다. NSG 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 Azure Storage 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

* [ISE 끝점 액세스 이해](./connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 논리 앱에서 요청 기반 트리거를 사용 하 여 요청 또는 Webhook 트리거와 같은 들어오는 호출 또는 요청을 수신 하는 경우 권한 있는 클라이언트만 논리 앱을 호출할 수 있도록 액세스를 제한할 수 있습니다.

ISE (integration service environment)에서 논리 앱을 실행 하는 경우 DDoS 공격 으로부터 보호 하기 위해 ISE와 연결 된 가상 네트워크에서 DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

위협 인텔리전스를 사용 하도록 설정 하 고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성 된 각 조직의 네트워크 경계에서 Azure 방화벽을 배포 합니다.

Azure Security Center Just-in-time 네트워크 액세스를 사용 하 여 제한 된 기간 동안 끝점의 노출을 승인 된 IP 주소로 제한 하는 NSGs를 구성 합니다.

적응 네트워크 강화 Azure Security Center 사용 하 여 실제 트래픽 및 위협 인텔리전스에 따라 포트와 원본 Ip를 제한 하는 NSG 구성을 권장 합니다.

* [Logic Apps에 대 한 인바운드 호출을 보호 하는 방법](logic-apps-securing-a-logic-app.md#access-for-inbound-calls-to-request-based-triggers)

* [인바운드 IP 주소를 제한 하는 방법](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

* [DDoS 보호를 구성 하는 방법](../virtual-network/manage-ddos-protection.md)

* [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/threat-protection.md)

* [적응 네트워크 강화 Azure Security Center 이해](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center Just-in-time 네트워크 Access Control 이해](../security-center/security-center-just-in-time.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: 외부 액세스 지점을 사용 하는 ISE (integration service environment)에서 논리 앱을 실행 하는 경우 nsg (네트워크 보안 그룹)를 사용 하 여 데이터 exfiltration의 위험을 줄일 수 있습니다. NSG 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 Azure Storage 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

네트워크 트래픽에 대 한 추가 보호 및 정보를 제공 하기 위해 각 Application Gateway 인스턴스에서 사용 하도록 설정한 경우에만 생성 되는 액세스 로그를 참조할 수 있습니다. 이 로그를 사용하여 Application Gateway 액세스 패턴을 확인하고 중요한 정보를 분석할 수 있습니다. 여기에는 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 바이트 입출력이 포함됩니다.

그렇지 않으면 marketplace에서 타사 솔루션을 활용 하 여이 요구 사항을 충족할 수 있습니다.

* [ISE 끝점 액세스 이해](./connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

* [Application Gateway를 사용 하 여 내부 VNET에 API Management를 통합 하는 방법](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [WAF 액세스 로그를 이해 하는 방법](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 제공 하는 제품을 선택 합니다. 페이로드 검사에 기반한 침입 탐지 및/또는 방지 기능이 요구 사항이 아니면, 위협 인텔리전스가 포함된 Azure Firewall을 사용할 수 있습니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

악의적인 트래픽을 감지 하거나 거부 하기 위해 각 조직의 네트워크 경계에서 원하는 방화벽 솔루션을 배포 합니다.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure 방화벽을 사용 하 여 경고를 구성 하는 방법](../firewall/threat-intel.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: ISE (integration service environment)에서 논리 앱을 실행 하는 경우 Azure 애플리케이션 Gateway를 배포 합니다.

* [내부 VNET의 API Management를 Application Gateway와 통합하는 방법](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [HTTPS를 사용 하도록 Application Gateway를 구성 하는 방법](../application-gateway/create-ssl-portal.md)

* [Azure 웹 응용 프로그램 게이트웨이를 사용 하 여 계층 7 부하 분산 이해](../application-gateway/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Logic Apps 인스턴스에 액세스 해야 하는 리소스의 경우 가상 네트워크 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: LogicApps, LogicAppsManagement)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

* [서비스 태그를 사용 하는 방법에 대 한 자세한 내용](../virtual-network/service-tags-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용 하 여 Azure Logic Apps 인스턴스와 관련 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft 논리" 및 "Microsoft. 네트워크" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure 논리 앱 인스턴스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같은 기본 제공 정책 정의를 사용할 수도 있습니다.

Logic Apps의 진단 로그를 사용하도록 설정해야 함

DDoS Protection 표준을 사용하도록 설정해야 합니다.

또한 Azure 청사진을 사용 하 여 단일 청사진 정의에서 Azure Resource Manager 템플릿, azure RBAC (역할 기반 액세스 제어) 및 정책과 같은 주요 환경 아티팩트를 패키지화 하 여 대규모 Azure 배포를 간소화할 수 있습니다. Blueprint를 새로운 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 개별 nsg 규칙에 대해 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 모든 규칙에 대 한 비즈니스 필요 및/또는 기간 (등)을 지정할 수 있습니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

* [Logic Apps에 대 한 Azure Policy 정의 목록](./policy-samples.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Logic Apps 인스턴스와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft는 로그의 타임 스탬프에 대 한 Azure Logic Apps와 같은 Azure 리소스에 사용 되는 시간 원본을 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 런타임 중에 논리 앱에 대 한 다양 한 디버깅 정보를 얻기 위해 Azure Monitor 로그를 설정 하 고 사용 하 여 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 런타임 데이터 및 이벤트에 대 한 정보를 기록 하 고 Log Analytics 작업 영역에 저장할 수 있습니다. Azure Monitor를 사용하면 클라우드 및 온-프레미스 환경을 모니터링하여 가용성과 성능을 더 쉽게 유지할 수 있습니다. Azure Monitor 로그를 사용 하 여이 정보를 수집 하 고 검토 하는 데 도움이 되는 로그 쿼리를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 다른 Azure 서비스에서이 진단 데이터를 사용할 수 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/platform/activity-log.md)

* [Azure Monitor 로그를 설정 하 고 Azure Logic Apps에 대 한 진단 데이터를 수집 하는 방법](./monitor-logic-apps-log-analytics.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 런타임 중에 논리 앱에 대 한 다양 한 디버깅 정보를 얻기 위해 Azure Monitor 로그를 설정 하 고 사용 하 여 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 런타임 데이터 및 이벤트에 대 한 정보를 기록 하 고 Log Analytics 작업 영역에 저장할 수 있습니다. Azure Monitor를 사용하면 클라우드 및 온-프레미스 환경을 모니터링하여 가용성과 성능을 더 쉽게 유지할 수 있습니다. Azure Monitor 로그를 사용 하 여이 정보를 수집 하 고 검토 하는 데 도움이 되는 로그 쿼리를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 다른 Azure 서비스에서이 진단 데이터를 사용할 수 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/platform/activity-log.md)

* [Azure Monitor 로그를 설정 하 고 Azure Logic Apps에 대 한 진단 데이터를 수집 하는 방법](./monitor-logic-apps-log-analytics.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: 논리 앱을 만들고 실행 한 후에는 논리 앱의 실행 상태, 실행 기록, 트리거 기록 및 성능을 확인할 수 있습니다. 실시간 이벤트 모니터링과 풍부한 디버깅을 위해 Azure Monitor 로그를 사용 하 여 논리 앱에 대 한 진단 로깅을 설정 합니다. 이 Azure 서비스를 사용 하면 클라우드 및 온-프레미스 환경을 모니터링 하 여 해당 가용성 및 성능을 더 쉽게 유지할 수 있습니다. 그런 다음 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 이벤트를 찾아 볼 수 있습니다. Azure Monitor 로그에이 정보를 저장 하 여이 정보를 찾고 분석 하는 데 도움이 되는 로그 쿼리를 만들 수 있습니다. 또한 Azure Storage 및 Azure Event Hubs와 같은 다른 Azure 서비스에서이 진단 데이터를 사용할 수 있습니다.

Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure Logic Apps 인스턴스와 연결 된 로그에 대 한 로그 보존 기간을 설정 합니다.

* [실행 상태를 모니터링 하 고, 트리거 기록을 검토 하 고, Azure Logic Apps에 대 한 경고를 설정 하는 방법](./monitor-logic-apps.md)

* [로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 논리 앱에 대 한 로깅을 설정 하려면 논리 앱을 만들 때 Log Analytics를 사용 하도록 설정 하거나 기존 논리 앱에 대 한 Log Analytics 작업 영역에 Logic Apps 관리 솔루션을 설치할 수 있습니다. 이 솔루션은 논리 앱 실행에 대 한 집계 된 정보를 제공 하며 상태, 실행 시간, 다시 전송 상태 및 상관 관계 Id와 같은 특정 세부 정보를 포함 합니다. 그런 다음이 정보에 대 한 로깅을 사용 하도록 설정 하 고 쿼리를 만들려면 Azure Monitor 로그를 설정 합니다.

Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보낼 수도 있습니다. Log Analytics에서 쿼리를 수행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, Azure Logic Apps에 대해 수집 되었을 수 있는 활동 로그 데이터를 기반으로 다양 한 통찰력을 제공 합니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [Azure Monitor 로그를 설정 하 고 Azure Logic Apps에 대 한 진단 데이터를 수집 하는 방법](./monitor-logic-apps-log-analytics.md)

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/platform/activity-log.md)

* [Azure Monitor의 Log Analytics에서 Azure 활동 로그를 수집 하 고 분석 하는 방법](../azure-monitor/platform/activity-log.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 보안 로그 및 이벤트에 있는 비정상적인 활동을 모니터링 하 고 경고 하는 데 Log Analytics와 함께 Azure Security Center를 사용 합니다.

또는 온보드 데이터를 Azure 센티널로 설정할 수 있습니다.

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [Azure Security Center에서 경고를 관리 하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

* [Log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음 Azure Logic Apps는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Azure Logic Apps는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: AD (Azure Active Directory)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

로그인하지 않고 Azure Active Directory(Azure AD)로 보호되는 다른 리소스에 쉽게 액세스하여 ID를 인증하기 위해 논리 앱에서 자격 증명 또는 비밀 대신 관리 ID(이전의 MSI(관리 서비스 ID))를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다.

논리 앱의 모든 요청 끝점에는 끝점의 URL에 SAS (공유 액세스 서명)가 있습니다. 요청 기반 트리거에 대한 엔드포인트 URL을 다른 상대방과 공유하는 경우 특정 키를 사용하고 만료 날짜가 있는 콜백 URL을 생성할 수 있습니다. 이렇게 하면 특정 시간 범위에 따라 키를 원활하게 롤링하거나 논리 앱을 트리거하는 액세스를 제한할 수 있습니다.

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Azure Logic Apps에서 관리 ID를 사용하여 Azure 리소스에 대한 액세스 인증](./create-managed-service-identity.md)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [SAS를 사용 하 여 Azure Logic Apps에서 액세스 및 데이터를 보호 하는 방법](logic-apps-securing-a-logic-app.md#access-for-inbound-calls-to-request-based-triggers)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure Active Directory 및 Azure Logic Apps에는 기본 암호 개념이 없습니다.

기본 인증을 사용 하는 경우 사용자 이름 및 암호를 지정 해야 합니다. 이러한 자격 증명을 만들 때 인증을 위해 강력한 암호를 구성 해야 합니다.

인프라를 코드로 사용 하는 경우에는 코드에 암호를 저장 하지 않고 대신 Azure Key Vault를 사용 하 여 자격 증명을 저장 하 고 검색 합니다.

* [Logic Apps에서 데이터를 보호 하 고 액세스 하는 방법](logic-apps-securing-a-logic-app.md)

* [Azure Key Vault에서 비밀을 설정 하 고 검색 하는 방법](../key-vault/secrets/quick-create-portal.md)

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

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure 앱 등록 (서비스 주체)을 사용 하 여 API 호출을 통해 Recovery Services 자격 증명 모음과 상호 작용 하는 데 사용할 수 있는 토큰을 검색 합니다.

또한 대부분의 커넥터에서는 먼저 대상 서비스 또는 시스템에 대 한 연결을 만들고 인증 자격 증명 또는 기타 구성 정보를 제공 해야 논리 앱에서 트리거 또는 동작을 사용할 수 있습니다. 예를 들어 데이터에 액세스 하거나 사용자 대신 게시 하기 위해 Twitter 계정에 대 한 연결에 권한을 부여 해야 합니다.]

Azure Active Directory (Azure AD) OAuth를 사용 하는 커넥터의 경우 연결을 만들면 액세스 토큰이 암호화 되어 Azure 비밀 저장소에 안전 하 게 저장 되는 Office 365, Salesforce 또는 GitHub와 같은 서비스에 로그인 하는 것을 의미 합니다. FTP 및 SQL과 같은 다른 커넥터에는 서버 주소, 사용자 이름 및 암호와 같은 구성 세부 정보를 포함 하는 연결이 필요 합니다. 이러한 연결 구성 세부 정보 또한 암호화된 후 안전하게 저장됩니다.

* [Azure REST Api를 호출 하는 방법](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Azure AD를 사용 하 여 클라이언트 응용 프로그램 (서비스 사용자)을 등록 하는 방법](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [워크플로 트리거 API 정보](/rest/api/logic/workflowtriggers)

* [커넥터 구성 이해](../connectors/apis-list.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Active Directory) MFA(Multi-Factor Authentication)를 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 머신(Privileged Access Workstation) 사용

**지침**: Azure 리소스에 로그인하여 구성하도록 구성된 MFA(Multi-Factor Authentication)를 통해 PAW(Privileged Access Workstation)를 사용합니다.

* [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하려면 Azure AD(Active Directory) PIM(Privileged Identity Management)을 사용합니다.

또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

* [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 Azure Portal에 액세스할 수 있도록 허용합니다.

또한 논리 앱의 모든 요청 끝점에는 끝점의 URL에 SAS (공유 액세스 서명)가 있습니다. 특정 IP 주소의 요청만 수락 하도록 논리 앱을 제한할 수 있습니다.

* [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

* [Logic Apps에서 인바운드 IP 주소를 제한 하는 방법 이해](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Logic Apps 인스턴스의 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Logic Apps에 대 한 지원이 있는 경우 관리 되는 id를 사용 하 여 Azure Active Directory (Azure AD)로 보호 되는 다른 리소스에 쉽게 액세스 하 고 자격 증명이 나 암호 대신 로그인 하지 않고 id를 인증 합니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다.

Azure Logic Apps는 시스템 할당 및 사용자 할당 관리 id를 모두 지원 합니다. 논리 앱은 시스템이 할당한 ID 또는 단일 사용자가 할당한 ID를 사용할 수 있습니다. 이러한 ID는 논리 앱 그룹 간에 공유할 수 있지만 둘 다 공유할 수는 없습니다. 현재 특정 기본 제공 트리거 및 작업만 관리 ID를 지원하며, 관리형 커넥터 또는 연결은 지원하지 않습니다. 예를 들어 다음과 같습니다.
- HTTP
- Azure 기능
- Azure API Management
- Azure App Services

* [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Azure Logic Apps에서 관리 ID를 사용하여 Azure 리소스에 대한 액세스 인증](./create-managed-service-identity.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: AD (Azure Active Directory)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

* [Azure AD 보고 이해](../active-directory/reports-monitoring/index.yml)

* [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure Logic Apps 인스턴스의 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으며,이를 통해 Azure 센티널 또는 타사 SIEM과 통합할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 현재 사용할 수 없음 고객 Lockbox는 Azure Logic Apps에 대해 아직 지원 되지 않습니다.

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

**지침**: "전역" 다중 테 넌 트 Logic Apps 서비스에서 실행 되는 커넥터는 Microsoft에서 배포 하 고 관리 합니다. 이러한 커넥터는 Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint 등을 포함 하 여 클라우드 서비스, 온-프레미스 시스템 또는 둘 다에 액세스 하기 위한 트리거와 작업을 제공 합니다.

Azure 가상 네트워크의 리소스에 직접 액세스 해야 하는 논리 앱의 경우 전용 리소스에서 논리 앱을 빌드, 배포 및 실행할 수 있는 ISE (통합 서비스 환경)를 만들 수 있습니다. 일부 Azure virtual network는 azure에서 호스트 되는 Azure Storage, Azure Cosmos DB, Azure SQL Database, 파트너 서비스 또는 고객 서비스와 같은 Azure PaaS 서비스에 대 한 액세스를 제공 하기 위해 개인 끝점 (Azure 개인 링크)을 사용 합니다. 논리 앱에서 개인 끝점을 사용 하는 가상 네트워크에 액세스 해야 하는 경우에는 ISE 내에서 해당 논리 앱을 만들고, 배포 하 고, 실행 해야 합니다.

ISE를 만들 때 내부 또는 외부 액세스 끝점 중 하나를 사용 하도록 선택할 수 있습니다. 선택은 ISE의 논리 앱에 대 한 요청 또는 webhook 트리거가 가상 네트워크 외부에서 호출을 받을 수 있는지 여부를 결정 합니다.

또한 환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다. 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 Azure 리소스에 대 한 액세스 수준을 제한할 수 있습니다. Azure RBAC (역할 기반 액세스 제어)를 통해 Azure 리소스에 대 한 액세스를 제어할 수 있습니다.

* [Logic Apps 커넥터 이해](../connectors/apis-list.md)

* [ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](./connect-virtual-network-vnet-isolated-environment-overview.md)

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 현재 사용할 수 없음 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Logic Apps 사용할 수 없습니다.

중요 한 정보를 무단으로 전송 하는 것을 모니터링 하 고 정보 보안 전문가를 경고 하는 동안 이러한 전송을 차단 하는 네트워크 경계의 Azure Marketplace에서 타사 솔루션을 활용 합니다.

Microsoft는 Azure Logic Apps에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요 한 정보를 암호화 합니다. Azure Logic Apps에서 논리 앱 실행 중의 모든 데이터는 TLS (Transport Layer Security) 및 미사용을 사용 하 여 전송 중에 암호화 됩니다. 논리 앱의 실행 기록을 볼 때는 Logic Apps가 액세스를 인증한 다음, 각 실행의 요청과 응답에 대한 입력 및 출력 링크를 제공합니다. 단, 암호, 비밀, 키 또는 기타 중요한 정보를 처리하는 작업의 경우 다른 사용자가 해당 데이터를 보거나 액세스하지 못하도록 하는 것이 좋습니다. 예를 들어, Azure Key Vault에서 논리 앱이 HTTP 작업을 인증할 때 사용할 비밀을 가져오는 경우, 이 비밀을 시야에서 숨기는 것이 좋습니다.

요청 트리거는 인바운드 요청에 대해 TLS (전송 계층 보안) 1.2만 지원 합니다. Azure 리소스에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다. HTTP 커넥터를 사용 하는 아웃 바운드 호출은 TLS (Transport Layer Security) 1.0, 1.1 및 1.2을 지원 합니다.

해당 하는 경우 미사용 암호화 및 전송 중인 암호화에 대 한 Azure Security Center 권장 사항을 따릅니다.

* [Azure Logic Apps에서 액세스 및 데이터 보안](logic-apps-securing-a-logic-app.md)

* [Azure Logic Apps에서 인바운드 HTTPS 요청 수신 및 응답](logic-apps-securing-a-logic-app.md#access-for-inbound-calls-to-request-based-triggers)

* [Azure Logic Apps에서 HTTP 또는 HTTPS를 통해 서비스 엔드포인트 호출](logic-apps-securing-a-logic-app.md#access-for-inbound-calls-to-request-based-triggers)

* [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure를 사용 하 여 미사용 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md)

* [고객 관리 키를 설정 하 여 ISEs (integration service environment)에 대 한 미사용 데이터를 암호화 Azure Logic Apps](./customer-managed-keys-integration-service-environment.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Logic Apps에서 많은 트리거와 작업에는 논리 앱의 실행 기록에서 해당 데이터를 표시 하 여 입력, 출력 또는 둘 다를 보호 하는 데 사용할 수 있는 설정이 있습니다.

Microsoft는 Azure Logic Apps에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [실행 기록 데이터에 대 한 액세스 보호](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: 특정 사용자나 그룹만 논리 앱 관리, 편집 및 보기와 같은 특정 작업을 실행 하도록 허용할 수 있습니다. 사용자의 권한을 제어 하려면 azure 구독에서 구성원에 사용자 지정 또는 기본 제공 역할을 할당할 수 있도록 azure RBAC (역할 기반 액세스 제어)를 사용 합니다.
- 논리 앱 참가자: 논리 앱을 관리할 수 있지만 앱에 대한 액세스는 변경할 수 없습니다.
- 논리 앱 운영자: 논리 앱을 읽고 사용하거나 사용하지 않도록 설정할 수는 있지만 편집하거나 업데이트할 수 없습니다.

다른 사람이 논리 앱을 변경하거나 삭제하지 못하게 하려면 Azure 리소스 잠금을 사용할 수 있습니다. 이 기능은 다른 사람이 프로덕션 리소스를 변경하거나 삭제하지 못하도록 합니다.

* [Azure Logic Apps 작업에 대 한 보안 액세스](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft는 Azure Logic Apps에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure 고객 데이터 보호](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Logic Apps은 Azure Storage를 사용 하 여 미사용 데이터를 저장 하 고 자동으로 암호화 합니다. 이 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. 기본적으로 Azure Storage는 Microsoft 관리 키를 사용 하 여 데이터를 암호화 합니다.

논리 앱을 호스팅하기 위한 ISE (통합 서비스 환경)를 만들 때 Azure Storage에 사용 되는 암호화 키를 더 많이 제어 하려는 경우 Azure Key Vault를 사용 하 여 사용자 고유의 키를 설정, 사용 및 관리할 수 있습니다. 이 기능은 BYOK ("Bring Your Own Key") 라고도 하며, 키를 "고객 관리 키" 라고 합니다.

* [Azure Logic Apps의 통합 서비스 환경에 대 한 미사용 데이터 암호화](./customer-managed-keys-integration-service-environment.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 Azure Logic Apps 뿐만 아니라 기타 중요 하거나 관련 된 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: [해당 사항 없음] Microsoft는 Azure Logic Apps을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.]

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 해당 사항 없음 Microsoft는 Azure Logic Apps을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 해당 사항 없음 Microsoft는 Azure Logic Apps을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 Asset Discovery 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 리소스 그래프를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

* [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure Policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 (예: 커넥터)와 계산 리소스에 대해 승인 된 소프트웨어의 인벤토리를 만듭니다.

참고: Google의 데이터 및 개인 정보 취급 방침으로 인해 Google 승인 된 서비스만 Gmail 커넥터를 사용할 수 있습니다. 이러한 상황은 진화 하 고 있으며 나중에 다른 Google 커넥터에 영향을 줄 수 있습니다.

* [모든 Logic Apps 커넥터 목록](/connectors/connector-reference/connector-reference-logicapps-connectors)

* [Gmail 커넥터에 대 한 문제 및 제한 사항 이해](/connectors/gmail/#known-issues-and-limitations)

* [Google의 개인 정보 취급 방침에 대 한 자세한 정보](../connectors/connectors-google-data-security-privacy-policy.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스의 유형에 대 한 제한을 설정할 수 있습니다.

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

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

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요한 Logic Apps 관련 된 리소스 이지만 조직에 대 한 위험이 높아질 수 있으며, 자체 가상 머신 및/또는 가상 네트워크 내에서 격리 되 고 Azure 방화벽 또는 네트워크 보안 그룹을 사용 하 여 충분히 안전 하 게 보호 되어야 합니다.

비즈니스 작업을 수행 하는 데 필요 하지만 조직에서 더 높은 위험을 초래할 수 있는 Logic Apps, 특정 권한 및 Azure RBAC 경계를 포함 하는 별도의 리소스 그룹을 통해 가능한 한 격리 해야 합니다.

* [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [Azure RBAC를 통해 Logic Apps에 대 한 액세스를 보호 하는 방법](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용 하 여 Azure Logic Apps 인스턴스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft 논리" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Logic Apps 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 예를 들어 액세스를 제한 하려는 리소스에 대 한 연결을 다른 사용자가 만들거나 사용 하지 못하도록 차단할 수 있습니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

또한 보안 매개 변수를 사용 하 여 중요 한 데이터 및 비밀을 보호 합니다.

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Logic Apps 커넥터에서 만든 연결 차단](./block-connections-connectors.md)

* [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

* [Azure Logic Apps 용 Azure Resource Manager 템플릿을 배포 하는 방법](logic-apps-deploy-azure-resource-manager-templates.md)

* [보안 작업 매개 변수 이해](logic-apps-securing-a-logic-app.md#secure-action-parameters)

* [매개 변수에 대한 보안 권장 사항](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

Azure Policy를 사용 하 여 Azure Logic Apps 인스턴스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft 논리" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Logic Apps 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 예를 들어 액세스를 제한 하려는 리소스에 대 한 연결을 다른 사용자가 만들거나 사용 하지 못하도록 차단할 수 있습니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

또한 난독 처리를 사용 하 여 실행 기록의 데이터를 보호 해야 합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

* [Azure Logic Apps 커넥터에서 만든 연결 차단](./block-connections-connectors.md)

* [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

* [Azure Logic Apps 용 Azure Resource Manager 템플릿을 배포 하는 방법](logic-apps-deploy-azure-resource-manager-templates.md)

* [실행 기록 입력 및 출력에 대 한 액세스 보호](logic-apps-securing-a-logic-app.md#obfuscate)

* [매개 변수 입력에 안전 하 게 액세스](logic-apps-securing-a-logic-app.md#secure-action-parameters)

* [매개 변수에 대한 보안 권장 사항](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure devops 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

* [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](/azure/devops/repos/index?view=azure-devops)

* [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 기본 제공 Azure Policy 정의 및 "Microsoft 논리" 네임 스페이스의 별칭 Azure Policy 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure Policy 별칭을 사용 하 여 Azure 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy 정의 및 "Microsoft 논리" 네임 스페이스의 별칭 Azure Policy 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure 리소스에 대 한 구성을 자동으로 적용 하려면 [감사], [deny] 및 [없는 경우 배포] Azure Policy 사용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 난독 처리를 사용 하 여 논리 앱 실행 기록의 입력 및 출력을 보호 합니다. 여러 환경에서를 배포 하는 경우 해당 환경에 따라 달라 지는 논리 앱의 워크플로 정의에 값을 매개 변수화 하는 것이 좋습니다. 이렇게 하면 Azure Resource Manager 템플릿을 사용하여 논리 앱을 배포하기 때문에 하드 코딩된 데이터를 피할 수 있고, 보안 매개 변수를 정의하여 이 데이터를 별도의 입력으로 전달하기 때문에(매개 변수 파일을 사용하여 템플릿의 매개 변수를 통해) 중요한 데이터를 보호할 수 있습니다. Key Vault를 사용 하 여 중요 한 데이터를 저장 하 고 배포 시 Key Vault에서 해당 값을 검색 하는 보안 템플릿 매개 변수를 사용할 수 있습니다. 그런 다음 매개 변수 파일에서 키 자격 증명 모음 및 암호를 참조할 수 있습니다.

논리 앱을 호스팅하기 위한 ISE (통합 서비스 환경)를 만들 때 Azure Storage에 사용 되는 암호화 키를 더 많이 제어 하려는 경우 Azure Key Vault를 사용 하 여 사용자 고유의 키를 설정, 사용 및 관리할 수 있습니다. 이 기능은 BYOK ("Bring Your Own Key") 라고도 하며, 키를 "고객 관리 키" 라고 합니다.

* [Azure Logic Apps에서 실행 기록의 입력 및 출력 보안](logic-apps-securing-a-logic-app.md#obfuscate)

* [매개 변수에 대한 보안 권장 사항](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

* [Azure Logic Apps에서 매개 변수 입력에 안전 하 게 액세스](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

* [Azure Key Vault를 사용 하 여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)

* [고객 관리 키를 설정 하 여 ISEs (integration service environment)에 대 한 미사용 데이터를 암호화 Azure Logic Apps](./customer-managed-keys-integration-service-environment.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure Active Directory (Azure AD)로 보호 되는 다른 리소스에 쉽게 액세스 하 고 로그인 하지 않고 id를 인증 하기 위해 논리 앱은 자격 증명이 나 비밀이 아닌 관리 id (이전의 관리 서비스 ID 또는 MSI)를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다.

현재 특정 기본 제공 트리거 및 작업만 관리 ID를 지원하며, 관리형 커넥터 또는 연결은 지원하지 않습니다. 예를 들어 다음과 같습니다.
- HTTP
- Azure 기능
- Azure API Management
- Azure App Services

* [Azure Logic Apps에서 관리 되는 id를 사용 하 여 Azure 리소스에 대 한 액세스를 인증 하는 방법](./create-managed-service-identity.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 난독 처리를 사용 하 여 논리 앱 실행 기록의 입력 및 출력을 보호 합니다. 여러 환경에서를 배포 하는 경우 해당 환경에 따라 달라 지는 논리 앱의 워크플로 정의에 값을 매개 변수화 하는 것이 좋습니다. 이렇게 하면 Azure Resource Manager 템플릿을 사용하여 논리 앱을 배포하기 때문에 하드 코딩된 데이터를 피할 수 있고, 보안 매개 변수를 정의하여 이 데이터를 별도의 입력으로 전달하기 때문에(매개 변수 파일을 사용하여 템플릿의 매개 변수를 통해) 중요한 데이터를 보호할 수 있습니다. Key Vault를 사용 하 여 중요 한 데이터를 저장 하 고 배포 시 Key Vault에서 해당 값을 검색 하는 보안 템플릿 매개 변수를 사용할 수 있습니다. 그런 다음 매개 변수 파일에서 키 자격 증명 모음 및 암호를 참조할 수 있습니다.

코드 내에서 자격 증명을 식별 하는 자격 증명 스캐너를 구현할 수도 있습니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [Azure Logic Apps에서 실행 기록의 입력 및 출력 보안](logic-apps-securing-a-logic-app.md#obfuscate)

* [매개 변수에 대한 보안 권장 사항](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

* [Azure Logic Apps에서 매개 변수 입력에 안전 하 게 액세스](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

* [Azure Key Vault를 사용 하 여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft 맬웨어 방지는 Azure 서비스 (예: Azure Logic Apps)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Backup)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 콘텐츠에서 실행 되지 않습니다.

App Service, Data Lake Storage, Blob Storage 등의 비 계산 Azure 리소스에 업로드 되는 파일을 미리 검색 합니다.

데이터 서비스에 대 한 Azure Security Center의 위협 검색을 사용 하 여 저장소 계정에 업로드 된 맬웨어를 검색 합니다.

* [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 이해](../security/fundamentals/antimalware.md)

* [데이터 서비스에 대 한 Azure Security Center의 위협 검색 이해](../security-center/threat-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 데이터를 보호 하 고, 중요 한 비즈니스 기능을 지 원하는 리소스를 신속 하 게 복원 하 고, 업무 연속성 (BC)을 유지 하기 위해 작업을 계속 실행할 수 있도록 DR (재해 복구) 솔루션을 구현 합니다.

이 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 장애 조치 (failover) 하도록 기본 논리 앱을 설정 하는 데 중점을 둔 것입니다. 이렇게 하면 주 복제본이 손실, 중단 또는 실패 하는 경우 보조 데이터베이스에서 작업을 수행할 수 있습니다. 이 전략을 사용 하려면 보조 논리 앱 및 종속 리소스를 대체 위치에 이미 배포 하 고 준비 해야 합니다.

또한 논리 앱의 기본 워크플로 정의를 Azure Resource Manager 템플릿으로 확장 해야 합니다. 이 템플릿은 논리 앱을 프로 비전 하 고 배포 하기 위한 인프라, 리소스, 매개 변수 및 기타 정보를 정의 합니다.

* [Azure Logic Apps에 대 한 비즈니스 연속성 및 재해 복구에 대해 자세히 알아보세요.](./business-continuity-disaster-recovery-guidance.md)

* [Azure Resource Manager 템플릿을 사용 하 여 Azure Logic Apps에 대 한 배포를 자동화 하는 방법](logic-apps-azure-resource-manager-templates-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: 데이터를 보호 하 고, 중요 한 비즈니스 기능을 지 원하는 리소스를 신속 하 게 복원 하 고, 업무 연속성 (BC)을 유지 하기 위해 작업을 계속 실행할 수 있도록 DR (재해 복구) 솔루션을 구현 합니다.

이 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 장애 조치 (failover) 하도록 기본 논리 앱을 설정 하는 데 중점을 둔 것입니다. 이렇게 하면 주 복제본이 손실, 중단 또는 실패 하는 경우 보조 데이터베이스에서 작업을 수행할 수 있습니다. 이 전략을 사용 하려면 보조 논리 앱 및 종속 리소스를 대체 위치에 이미 배포 하 고 준비 해야 합니다.

또한 논리 앱의 기본 워크플로 정의를 Azure Resource Manager 템플릿으로 확장 해야 합니다. 이 템플릿은 논리 앱을 프로 비전 하 고 배포 하기 위한 인프라, 리소스, 매개 변수 및 기타 정보를 정의 합니다.

논리 앱의 모든 요청 끝점에는 끝점의 URL에 SAS (공유 액세스 서명)가 있습니다. Azure Key Vault를 사용 하 여 암호를 저장 하는 경우 키와 Url의 자동 백업을 정기적으로 수행 해야 합니다.

* [Azure Logic Apps에 대 한 비즈니스 연속성 및 재해 복구에 대해 자세히 알아보세요.](./business-continuity-disaster-recovery-guidance.md)

* [Azure Resource Manager 템플릿을 사용 하 여 Azure Logic Apps에 대 한 배포를 자동화 하는 방법](logic-apps-azure-resource-manager-templates-overview.md)

* [SAS를 사용 하 여 Azure Logic Apps에서 액세스 및 데이터를 보호 하는 방법](logic-apps-securing-a-logic-app.md#access-for-inbound-calls-to-request-based-triggers)

* [Key Vault 키를 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 장애 조치 (failover) 하도록 주 논리 앱을 설정 하는 데 집중 해야 합니다. 이렇게 하면 주 복제본이 손실, 중단 또는 실패 하는 경우 보조 데이터베이스에서 작업을 수행할 수 있습니다. 이 전략을 사용 하려면 보조 논리 앱 및 종속 리소스를 대체 위치에 이미 배포 하 고 준비 해야 합니다.

백업 된 고객이 관리 하는 키의 복원을 테스트 합니다. 이는 ISE (integration service environment)에서 실행 되는 Logic Apps에만 적용 됩니다.

* [Azure Logic Apps에 대 한 비즈니스 연속성 및 재해 복구에 대해 자세히 알아보세요.](./business-continuity-disaster-recovery-guidance.md)

* [고객 관리 키를 설정 하 여 ISEs (integration service environment)에 대 한 미사용 데이터를 암호화 Azure Logic Apps](./customer-managed-keys-integration-service-environment.md)

* [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 장애 조치 (failover) 하도록 주 논리 앱을 설정 하는 데 집중 해야 합니다. 이렇게 하면 주 복제본이 손실, 중단 또는 실패 하는 경우 보조 데이터베이스에서 작업을 수행할 수 있습니다. 이 전략을 사용 하려면 보조 논리 앱 및 종속 리소스를 대체 위치에 이미 배포 하 고 준비 해야 합니다.

백업 하는 고객 관리 키를 보호 합니다. 이는 ISE (integration service environment)에서 실행 되는 Logic Apps에만 적용 됩니다.

Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하 여 실수로 또는 악의적으로 삭제 되지 않도록 키를 보호 합니다.

* [Azure Logic Apps에 대 한 비즈니스 연속성 및 재해 복구에 대해 자세히 알아보세요.](./business-continuity-disaster-recovery-guidance.md)

* [고객 관리 키를 설정 하 여 ISEs (integration service environment)에 대 한 미사용 데이터를 암호화 Azure Logic Apps](./customer-managed-keys-integration-service-environment.md)

* [Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하는 방법](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

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

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

* [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

* [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

* [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

* [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 Workflow Automation 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

* [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
