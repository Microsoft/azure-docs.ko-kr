---
title: Service Fabric에 대 한 Azure 보안 기준
description: Service Fabric 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9054fa72ee4204d7a09e6ce8de4fe6d4ed4dac81
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227715"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Service Fabric에 대 한 Azure 보안 기준

Service Fabric에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.



## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 모든 Virtual Network 서브넷 배포에는 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 특정 한 네트워크 액세스 제어와 함께 적용 되는 네트워크 보안 그룹이 있는지 확인 합니다.

* [템플릿을 사용하여 Azure Firewall 배포](../firewall/deploy-template.md)

* [Azure NSGs (네트워크 보안 그룹)를 사용 하 여 경계 네트워크 만들기](../security/fundamentals/service-fabric-best-practices.md#use-network-isolation-and-security-with-azure-service-fabric)

* [Azure Service Fabric 클러스터를 기존 가상 네트워크와 통합 하는 방법](./service-fabric-patterns-networking.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center을 사용 하 고 Azure Service Fabric 클러스터의 보안을 유지 하는 데 사용 되는 가상 네트워크, 서브넷 및 네트워크 보안 그룹에 대 한 네트워크 보호 권장 사항을 재구성 합니다. NSG (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고 로그를 트래픽 감사에 대 한 Azure Storage 계정으로 보냅니다. Azure Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 azure 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수도 있습니다. Azure 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고 핫 스폿을 식별 하 고, 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Azure 트래픽 분석을 사용 하도록 설정 하 고 사용 하는 방법](../network-watcher/traffic-analytics.md)

* [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 프런트 엔드 게이트웨이를 제공 하 여 사용자, 장치 또는 기타 응용 프로그램에 대 한 단일 수신 지점을 제공 합니다. Azure API Management는 Service Fabric와 직접 통합 되어 백 엔드 서비스에 대 한 액세스를 보호 하 고, 제한을 사용 하 여 DOS 공격을 방지 하 고, API 키, JWT 토큰, 인증서 및 기타 자격 증명을 확인할 수 있습니다.

들어오는 트래픽의 추가 검사를 위해 중요 한 웹 응용 프로그램 앞에 Azure WAF (웹 응용 프로그램 방화벽)를 배포 하는 것이 좋습니다. WAF에 대해 진단 설정을 사용 하도록 설정 하 고 로그를 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역에 수집 합니다.

* [Service Fabric 및 API Management 개요](./service-fabric-api-management-overview.md)

* [내부 VNET의 API Management를 Application Gateway와 통합](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [Azure WAF를 배포 하는 방법](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격 으로부터 보호 하려면 azure Service Fabric 클러스터가 배포 되는 가상 네트워크에서 Azure DDoS Standard 보호를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용 하 여 알려진 악성 또는 사용 하지 않는 인터넷 IP 주소와의 통신을 거부 합니다.

* [DDoS 보호를 구성 하는 방법](../virtual-network/manage-ddos-protection.md)

* [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/threat-protection.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Azure Service Fabric 클러스터를 보호 하는 데 사용 되는 서브넷에 연결 된 nsg에 대 한 nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 합니다. Azure Storage 계정에 NSG 흐름 로그를 기록 하 여 흐름 레코드를 생성 합니다. 비정상적인 활동을 조사 하는 데 필요한 경우 Azure Network Watcher 패킷 캡처를 사용 하도록 설정 합니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

* [트래픽 분석을 사용 하 여 NSG 흐름 로그 시각화](../network-watcher/traffic-analytics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 제공 하는 제품을 선택 합니다. 페이로드 검사에 기반한 침입 탐지 및/또는 방지 기능이 요구 사항이 아니면, 위협 인텔리전스가 포함된 Azure Firewall을 사용할 수 있습니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

악의적인 트래픽을 감지 하거나 거부 하기 위해 각 조직의 네트워크 경계에서 원하는 방화벽 솔루션을 배포 합니다.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure 방화벽을 사용 하 여 경고를 구성 하는 방법](../firewall/threat-intel.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 신뢰할 수 있는 인증서에 대해 HTTPS/SSL을 사용 하도록 설정 된 웹 응용 프로그램용 Azure 애플리케이션 게이트웨이를 배포 합니다.

* [Application Gateway 배포 하는 방법](../application-gateway/quick-create-portal.md)

* [HTTPS를 사용 하도록 Application Gateway를 구성 하는 방법](../application-gateway/create-ssl-portal.md)

* [Azure 웹 응용 프로그램 게이트웨이를 사용 하 여 계층 7 부하 분산 이해](../application-gateway/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용 하 여 Azure Service Fabric 클러스터를 배포 하는 서브넷에 연결 된 nsg (네트워크 보안 그룹)에 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

* [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)

* [Service Fabric 네트워킹 모범 사례](./service-fabric-best-practices-networking.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Service Fabric 클러스터와 관련 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "ServiceFabric" 및 "" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Service Fabric 클러스터의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

또한 Azure 청사진을 사용 하 여 단일 청사진 정의에서 Azure Resource Manager 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키지화 하 여 대규모 Azure 배포를 간소화할 수 있습니다. Blueprint를 새로운 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

* [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: Azure Service Fabric 클러스터와 연결 된 네트워크 보안 및 트래픽 흐름과 관련 된 nsgs (네트워크 보안 그룹) 및 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI (명령줄 인터페이스)를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 azure Service Fabric 배포와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft는 Azure Service Fabric 클러스터 구성 요소에 대 한 시간 원본을 유지 관리 하므로 계산 배포에 대 한 시간 동기화를 업데이트할 수 있습니다.

* [Azure 계산 리소스에 대 한 시간 동기화를 구성 하는 방법](../virtual-machines/windows/time-sync.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 클러스터에서 생성 된 보안 데이터를 집계 하기 위해 Azure Service Fabric 클러스터를 Azure Monitor에 등록할 수 있습니다. Service Fabric 진단 문제 및 솔루션 예를 참조 하세요.

* [Service Fabric와 Azure Monitor 로그 통합 구성](./service-fabric-diagnostics-oms-setup.md)

* [Azure Service Fabric의 모니터링 컨테이너에 대 한 Azure Monitor 로그 설정](./service-fabric-tutorial-monitoring-wincontainers.md)

* [일반적인 Service Fabric 시나리오 진단](./service-fabric-diagnostics-common-scenarios.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Service Fabric 클러스터에 대해 Azure Monitor를 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 보냅니다. 그러면 모든 Azure Service Fabric 클러스터 노드에 대 한 관련 클러스터 정보 및 OS 메트릭이 기록 됩니다.

* [Service Fabric와 Azure Monitor 로그 통합 구성](./service-fabric-diagnostics-oms-setup.md)

* [Azure Service Fabric의 모니터링 컨테이너에 대 한 Azure Monitor 로그 설정](./service-fabric-tutorial-monitoring-wincontainers.md)

* [노드에 Log Analytics 에이전트를 배포 하는 방법](./service-fabric-diagnostics-oms-agent.md)

* [로그 검색 Log Analytics](../azure-monitor/log-query/log-query-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: Azure Monitor에 Azure Service Fabric 클러스터를 등록 합니다. 사용 된 Log Analytics 작업 영역에 조직의 규정 준수 규정에 따라 설정 된 로그 보존 기간이 있는지 확인 합니다.

* [Service Fabric와 Azure Monitor 로그 통합 구성](./service-fabric-diagnostics-oms-setup.md)

* [Azure Service Fabric의 모니터링 컨테이너에 대 한 Azure Monitor 로그 설정](./service-fabric-tutorial-monitoring-wincontainers.md)

* [노드에 Log Analytics 에이전트를 배포 하는 방법](./service-fabric-diagnostics-oms-agent.md)

* [Log Analytics 작업 영역 보존 기간을 구성 하는 방법](../azure-monitor/platform/manage-cost-storage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor에 Azure Service Fabric 클러스터를 등록 합니다. 사용 된 Log Analytics 작업 영역에 조직의 규정 준수 규정에 따라 설정 된 로그 보존 기간이 있는지 확인 합니다.

* [Service Fabric와 Azure Monitor 로그 통합 구성](./service-fabric-diagnostics-oms-setup.md)

* [Azure Service Fabric의 모니터링 컨테이너에 대 한 Azure Monitor 로그 설정](./service-fabric-tutorial-monitoring-wincontainers.md)

* [노드에 Log Analytics 에이전트를 배포 하는 방법](./service-fabric-diagnostics-oms-agent.md)

* [Log Analytics 작업 영역 보존 기간을 구성 하는 방법](../azure-monitor/platform/manage-cost-storage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: azure Log Analytics 작업 영역 쿼리를 사용 하 여 azure Service Fabric 로그를 쿼리 합니다.

* [로그 검색 Log Analytics](../azure-monitor/log-query/log-query-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: azure Log Analytics 작업 영역을 사용 하 여 azure Service Fabric 클러스터와 관련 된 이벤트 및 보안 로그의 비정상 활동을 모니터링 하 고 경고 합니다.

* [Azure Security Center에서 경고를 관리 하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

* [Log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 기본적으로 windows Defender는 windows Server 2016에 설치 됩니다. Windows Defender를 사용 하지 않는 경우 구성 규칙에 대 한 Anon Ale웨어 설명서를 참조 하세요. Windows Defender는 Linux에서 지원 되지 않습니다.

* [자세한 내용은 windows Server 2016의 Windows Defender 바이러스 백신을 참조 하십시오.](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: DNS 로깅을 위한 타사 솔루션을 구현 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 노드 단위로 콘솔 로깅을 수동으로 구성 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Service Fabric 클러스터의 클러스터 프로 비전 중에 생성 되는 로컬 관리자 계정 및 사용자가 만드는 다른 모든 계정에 대 한 기록을 유지 관리 합니다. 또한 Azure AD 통합을 사용 하는 경우 Azure AD에는 명시적으로 할당 되어야 하 고 따라서 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

또한 Azure Security Center Id 및 액세스 관리 권장 사항을 사용할 수 있습니다.

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: 클러스터를 프로 비전 할 때 Azure는 웹 포털에 대 한 새 암호를 만들어야 합니다. 변경할 기본 암호는 없지만 웹 포털 액세스에 대해 다른 암호를 지정할 수 있습니다.

* [Azure Portal에서 만들기](./service-fabric-cluster-creation-via-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure Active Directory를 사용 하 여 Azure Service Fabric에 대 한 인증을 통합 합니다. 전용 관리 계정의 사용에 대 한 정책과 절차를 만듭니다.

또한 Azure Security Center Id 및 액세스 관리 권장 사항을 사용할 수 있습니다.

* [Azure Active Directory 클라이언트 인증 설정](./service-fabric-tutorial-create-vnet-and-windows-cluster.md#set-up-azure-active-directory-client-authentication)

* [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 가능 하면 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory SSO를 사용 합니다. Azure Security Center Id 및 액세스 관리 권장 사항을 사용 합니다.

* [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD MFA를 사용하도록 설정하고 Azure Security Center ID 및 액세스 관리 권장 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: MFA (multi-factor authentication)가 구성 된 paw (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure Service Fabric 클러스터 및 관련 리소스에 로그인 하 고 구성 합니다.

* [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하려면 Azure AD(Active Directory) PIM(Privileged Identity Management)을 사용합니다. 또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

* [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

* [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Service Fabric 클러스터의 관리 끝점에 대 한 액세스를 보호 하기 위해 중앙 인증 및 권한 부여 시스템으로 AAD (Azure Active Directory)를 사용 합니다. AAD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 AAD는 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.

* [AAD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Service Fabric 클라이언트 인증에 대 한 설치 Azure Active Directory](./service-fabric-cluster-creation-setup-aad.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Service Fabric 클러스터에서 AAD (Azure Active Directory) 인증을 사용 합니다. AAD는 부실 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

* [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="311-alert-on-account-login-behavior-deviation"></a>3.11: 계정 로그인 동작 편차에 대 한 경고

**지침**: 비활성화 되는 계정에 대 한 액세스 시도를 모니터링 하려면 AAD (Azure Active Directory) 로그인 및 감사 로그를 사용 합니다. 이러한 로그는 타사 SIEM/모니터링 도구에 통합할 수 있습니다.

AAD 사용자 계정에 대 한 진단 설정을 만들어 Azure Log Analytics 작업 영역에 감사 로그 및 로그인 로그를 전송 하 여이 프로세스를 간소화할 수 있습니다. Azure Log Analytics 작업 영역 내에서 원하는 경고를 구성 합니다.

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure AD 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 사용할 수 없음 고객 Lockbox Azure Service Fabric에 대해 아직 지원 되지 않습니다.

* [지원 되 고객 Lockbox 서비스 목록](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: azure Service Fabric 클러스터 배포와 관련 된 리소스에 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 azure 리소스를 추적 하는 데 도움을 줍니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 리소스는 VNet/서브넷으로 구분 되며, 적절 하 게 태그가 지정 되 고, NSG 또는 Azure 방화벽으로 보호 됩니다. 중요 한 데이터를 저장 하거나 처리 하는 리소스는 충분히 격리 되어야 합니다. 중요 한 데이터를 저장 하거나 처리 Virtual Machines 하는 경우 정책 및 프로시저를 구현 하 여 사용 하지 않을 때이를 해제 합니다.

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure 방화벽을 사용 하 여 경고 또는 경고 및 거부를 구성 하는 방법](../firewall/threat-intel.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 네트워크 경계에 자동화 된 도구를 배포 하 여 중요 한 정보를 무단으로 전송 하는 것을 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송 차단

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전 하 게 유지 하기 위해 Microsoft는 강력한 데이터 보호 제어 제품군을 구현 하 고 유지 관리 합니다.

역량.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요 한 정보를 암호화 합니다. Azure 리소스에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다.

해당 하는 경우 미사용 암호화 및 전송 중인 암호화에 대 한 Azure Security Center 권장 사항을 따릅니다.

* [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [서비스 패브릭 클러스터 보안 시나리오](./service-fabric-cluster-security.md)

* [TLS 구성에 대 한 Service Fabric 문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 계산 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: 해당 없음. 이 추천 사항은 데이터를 저장하도록 설계된 비 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: Azure Service Fabric 클러스터에서 중요 한 정보를 저장 하거나 처리 하려면 태그를 사용 하 여 클러스터 및 관련 리소스를 중요 한 것으로 표시 합니다. 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 계산 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 모든 Azure 리소스에서 미사용 암호화를 사용 합니다. Azure에서 암호화 키를 관리 하도록 허용 하는 것이 좋지만 일부 인스턴스에서는 사용자 고유의 키를 관리할 수 있는 옵션이 있습니다.

* [Azure에서 미사용 암호화 이해](../security/fundamentals/encryption-atrest.md)

* [고객이 관리 하는 암호화 키를 구성 하는 방법](../storage/common/storage-encryption-keys-portal.md)

* [Windows에서 Azure Service Fabric 클러스터 노드에 대해 디스크 암호화 사용](./service-fabric-enable-azure-disk-encryption-windows.md)

* [Linux에서 Azure Service Fabric 클러스터 노드에 대해 디스크 암호화 사용](./service-fabric-enable-azure-disk-encryption-linux.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 중요 한 Azure 리소스에 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 정기적으로 Service Fabric 오류 분석 서비스 및 비정상 서비스를 실행 하 여 클러스터 전체에서 오류를 시뮬레이션 하 여 서비스의 견고성과 안정성을 평가 합니다.

Azure virtual machines 및 컨테이너 이미지에 대 한 취약성 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다.

타사 솔루션을 사용 하 여 네트워크 장치 및 웹 응용 프로그램에 대 한 취약성 평가를 수행 합니다. 원격 검색을 수행할 때 단일 영구 관리자 계정을 사용 하지 마십시오. 검색 계정에 대 한 JIT 프로 비전 방법론을 구현 하는 것이 좋습니다. 검색 계정에 대 한 자격 증명을 보호 하 고, 모니터링 하 고, 취약성 검색에만 사용 해야 합니다.

* [Service Fabric 오류 분석 서비스 소개](./service-fabric-testability-overview.md)

* [Service Fabric 클러스터에서 제어 되는 비정상 상황 유도](./service-fabric-controlled-chaos.md)

* [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: Azure Service Fabric 클러스터의 가상 머신 확장 집합에서 자동 OS 이미지 업그레이드를 사용 하도록 설정 합니다.

또는 프로덕션으로 전환 하기 전에 먼저 OS 패치를 테스트 하려면 확장 집합의 OS 이미지를 업그레이드 하는 데 수동 트리거를 사용 합니다. 수동 트리거 옵션은 기본 제공 롤백 기능을 제공 하지 않습니다. Azure Automation에서 업데이트 관리를 사용 하 여 OS 패치를 모니터링 합니다.

* [Service Fabric 클러스터 노드에 대 한 패치 관리](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Azure 가상 머신 확장 집합을 사용 하 여 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [최신 확장 집합 모델로 VM을 최신 상태로 유지하는 방법](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

* [Azure Automation - 업데이트 관리 개요](../automation/update-management/update-mgmt-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: Azure Service Fabric 클러스터의 가상 머신 확장 집합에서 자동 OS 이미지 업그레이드를 사용 하도록 설정 합니다. POA (패치 오케스트레이션 응용 프로그램)는 Azure 외부에서 호스트 되는 Service Fabric 클러스터에 적합 한 대체 솔루션입니다. POA는 추가 호스팅 오버 헤드를 포함 하 여 Azure 클러스터에서 사용할 수 있습니다.

* [Service Fabric 클러스터 노드에 대 한 패치 관리](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Azure 가상 머신 확장 집합을 사용 하 여 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [Service Fabric 클러스터에 대 한 OS 패치 일정을 구성 하는 방법](./service-fabric-patch-orchestration-application.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 검색 결과를 일관 된 간격으로 내보내고 결과를 비교 하 여 취약성이 수정 되었는지 확인 합니다. Azure Security Center에서 제안 하는 취약성 관리 권장 사항을 사용 하는 경우 선택한 솔루션의 포털로 피벗 하 여 기록 검색 데이터를 볼 수 있습니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 일반적인 위험 채점 프로그램(예: Common Vulnerability Scoring System) 또는 타사 검사 도구에 제공된 기본 위험 등급을 사용합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

* [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류 체계로 구성합니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 컴퓨팅 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어를 정의합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

허용되지 않는 리소스 종류

허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 승인 되지 않은 소프트웨어 응용 프로그램에 대 한 클러스터 노드를 모니터링 하는 타사 솔루션을 구현 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 azure Service Fabric 클러스터를 비롯 한 모든 리소스 (예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색 합니다. 승인되지 않은 Azure 리소스가 검색되면 제거합니다. Azure Service Fabric 클러스터 노드의 경우 승인 되지 않은 소프트웨어를 제거 하거나 경고 하는 타사 솔루션을 구현 합니다.

* [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Azure Service Fabric 클러스터 노드의 경우 타사 솔루션을 구현 하 여 권한 없는 소프트웨어가 실행 되지 않도록 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 사용자 환경에서 프로 비전 할 수 있는 서비스를 제한 합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: Azure Service Fabric 클러스터 노드의 경우 타사 솔루션을 구현 하 여 권한 없는 파일 형식이 실행 되지 않도록 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure 리소스 관리자와 상호 작용 하는 기능을 제한 합니다.

* [Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 운영 체제별 구성 또는 타사 리소스를 사용 하 여 Azure compute 리소스 내에서 스크립트를 실행 하는 사용자의 기능을 제한 합니다.

* [예를 들어 Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요 하지만 조직에서 더 높은 위험을 초래할 수 있는 소프트웨어, 자체 가상 머신 및/또는 가상 네트워크 내에서 격리 하 고 Azure 방화벽 또는 네트워크 보안 그룹을 사용 하 여 충분히 안전 하 게 보호 해야 합니다.

* [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "ServiceFabric" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Service Fabric 클러스터의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

* [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Azure Service Fabric 운영 체제 이미지는 Microsoft에서 관리 하 고 유지 관리 합니다. 고객은 클러스터 노드의 운영 체제에 대 한 보안 구성을 구현 해야 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 Azure Service Fabric 클러스터 및 관련 리소스에 대 한 보안 설정을 적용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: Microsoft에서 관리 하 고 유지 관리 하는 Azure Service Fabric 클러스터 운영 체제 이미지입니다. OS 수준 상태 구성을 구현 하는 고객을 담당 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure devops 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

* [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 사용자 지정 이미지를 사용 하는 경우 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 컨테이너 이미지의 경우 Azure Container Registry에 저장 하 고 Azure RBAC를 활용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다.

* [Azure RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Container Registry에 대 한 Azure RBAC 이해](../container-registry/container-registry-roles.md)

* [Azure RBAC를 구성 하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: "ServiceFabric" 네임 스페이스에 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: "ServiceFabric" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Service Fabric 클러스터의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

* [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center을 사용 하 여 컨테이너에 대 한 OS 및 Docker 설정에 대 한 기준 검색을 수행 합니다.

* [Azure Security Center 컨테이너 권장 사항 이해](../security-center/container-security.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다.

* [Service Fabric에서 Azure에 관리 id 사용](./concepts-managed-identity.md)

* [새 Service Fabric 클러스터에 대 한 관리 id 지원 구성](./configure-new-azure-service-fabric-enable-managed-identity.md)

* [Service Fabric 응용 프로그램에서 관리 id 사용](./how-to-managed-identity-service-fabric-app-code.md)

* [Service Fabric 응용 프로그램에 대 한 KeyVaultReference 지원](./service-fabric-keyvault-references.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 되는 Id는 azure에 배포 된 Service Fabric 클러스터와 azure 리소스로 배포 된 응용 프로그램에서 사용할 수 있습니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

* [Service Fabric에서 Azure에 관리 id 사용](./concepts-managed-identity.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: Azure Service Fabric 배포와 관련 된 코드를 사용 하는 경우 코드 내에서 자격 증명을 식별 하는 자격 증명 스캐너를 구현할 수 있습니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

Azure Key Vault를 사용 하 여 클러스터 인증서를 자동으로 회전 Service Fabric 합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

* [Service Fabric 클러스터의 인증서 관리](./cluster-security-certificate-management.md#certificate-rotation)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리 맬웨어 방지 소프트웨어 사용

**지침**: windows Defender 바이러스 백신은 기본적으로 windows Server 2016에 설치 됩니다. 사용자 인터페이스가 기본적으로 일부 SKU에 설치되지만 필요하지는 않습니다.

Windows Defender를 사용하지 않는 경우 구성 규칙에 대한 맬웨어 방지 설명서를 참조하세요. Linux에서는 Windows Defender가 지원되지 않습니다.

* [Windows Server 2016에 대 한 Windows Defender 바이러스 백신 이해](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: 해당 없음. 이 추천 사항은 데이터를 저장하도록 설계된 비 컴퓨팅 리소스를 위한 것입니다. Microsoft 맬웨어 방지는 Azure 서비스 (예: Service Fabric)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 추천 사항은 데이터를 저장하도록 설계된 비 컴퓨팅 리소스를 위한 것입니다. Microsoft 맬웨어 방지는 Azure 서비스 (예: Service Fabric)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Service Fabric의 백업 및 복원 서비스를 사용 하면 상태 저장 서비스에 저장 된 정보를 쉽고 자동으로 백업할 수 있습니다. 애플리케이션 데이터를 정기적으로 백업하는 것은 데이터 손실 및 서비스 비가용성을 방지하는 데 필수적입니다. Service Fabric은 백업 및 복원 서비스(선택 사항)를 제공하므로 추가 코드를 작성할 필요 없이 상태 저장 Reliable Services(Actor Services 포함)의 정기적인 백업을 구성할 수 있습니다. 또한 이전에 수행한 백업 복원을 수월하게 수행할 수 있습니다.

* [Azure Service Fabric 클러스터에서 정기 백업 및 복원](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Service Fabric 클러스터에서 백업 복원 서비스를 사용 하도록 설정 하 고, 정기적으로 요청 시 상태 저장 서비스를 백업 하는 백업 정책을 만듭니다. Azure Key Vault 내에서 고객이 관리 하는 키를 백업 합니다.

* [Azure Service Fabric 클러스터에서 정기 백업 및 복원](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

* [Azure Service Fabric의 정기 백업 구성 이해](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 백업 구성 정보 및 사용 가능한 백업을 정기적으로 검토 하 여 백업 복원 서비스에서 복원을 수행할 수 있는지 확인 합니다. 백업 된 고객이 관리 하는 키의 복원을 테스트 합니다.

* [Azure Service Fabric의 정기 백업 구성 이해](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Azure Service Fabric에서 백업 복원](./service-fabric-backup-restore-service-trigger-restore.md)

* [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Service Fabric 백업 복원 서비스에서의 백업은 구독의 Azure Storage 계정을 사용 합니다. Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대 한 추가 제어를 위해 고객이 관리 하는 키를 제공 하 여 저장소 데이터를 암호화할 수 있습니다.

고객 관리 키를 사용 하는 경우 실수로 또는 악의적으로 삭제 되지 않도록 키를 보호 하기 위해 Key Vault에서 일시 삭제를 사용 하도록 설정 해야 합니다.

* [Azure Storage 미사용 데이터 암호화](../storage/common/storage-service-encryption.md)

* [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 개발 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 해결 방법을 확인 합니다.

* [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 태그를 사용 하 여 구독을 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 하 고 분류 하는 명명 시스템을 만듭니다. 사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다.

* [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

* [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

* [IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램에 대 한 NIST 가이드](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

* [Azure Security Center 보안 연락처를 설정 하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고 Sentinel을 스트리밍할 수 있습니다.

* [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

* [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

* [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.