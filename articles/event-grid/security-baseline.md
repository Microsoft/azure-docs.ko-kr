---
title: Event Grid에 대 한 Azure 보안 기준
description: Event Grid 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 132a6d9a6f5ca702b13e84402ee9bb71eca57d7c
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229704"
---
# <a name="azure-security-baseline-for-event-grid"></a>Event Grid에 대 한 Azure 보안 기준

Microsoft Azure Event Grid의 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다. 이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다. 자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](/azure/security/benchmarks/security-control-network-security)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 개인 끝점을 사용 하 여 공용 인터넷을 통하지 않고 개인 링크를 통해 안전 하 게 가상 네트워크에서 Event Grid 토픽 및 도메인으로 직접 이벤트를 수신 하도록 허용할 수 있습니다. Event Grid 토픽 또는 도메인에 대 한 개인 끝점을 만드는 경우 VNet의 클라이언트와 Event Grid 리소스 간에 보안 연결을 제공 합니다. 개인 끝점에는 가상 네트워크의 IP 주소 범위에서 IP 주소가 할당 됩니다. 개인 끝점과 Event Grid 서비스 간의 연결은 보안 개인 링크를 사용 합니다.

또한 Azure Event Grid는 토픽 및 도메인에 게시 하기 위한 공용 IP 기반 액세스 제어를 지원 합니다. IP 기반 컨트롤을 사용 하면 게시자를 토픽 또는 도메인으로 승인 된 컴퓨터 및 클라우드 서비스 집합 으로만 제한할 수 있습니다. 이 기능은 Event Grid에서 지 원하는 인증 메커니즘을 보완 합니다. 

- [Event Grid 전용 끝점에 대 한 자세한 내용](network-security.md#private-endpoints)

- [Event Grid IP 방화벽에 대 한 자세한 내용](network-security.md#ip-firewall)

- [네트워크 보안 Azure Event Grid](network-security.md) 

- [Azure Private Link 개요](../private-link/private-link-overview.md)

- [Azure 네트워크 보안 그룹](../virtual-network/security-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center을 사용 하 고 네트워크 보호 권장 사항을 따라 Azure에서 Event Grid 리소스를 보호 합니다. Azure virtual machines를 사용 하 여 Event Grid 리소스에 액세스 하는 경우 NSG (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고 트래픽을 위한 저장소 계정에 로그를 보냅니다.

감사.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 선택 된 ip 주소 또는 ip 주소 범위 집합 에서만 공용 인터넷을 통해 액세스를 제한 하도록 Event Grid 리소스에 대 한 ip 방화벽을 구성할 수 있습니다.

선택한 가상 네트워크 에서만 액세스를 제한 하도록 개인 끝점을 구성할 수 있습니다.

DDoS (분산 서비스 거부) 공격 으로부터 보호 하기 위해 이러한 가상 네트워크에 DDoS Protection 표준을 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다. 자세한 내용은 다음 아티클을 참조하세요. 

- [Azure Event Grid 토픽 또는 도메인에 대 한 개인 끝점을 구성 하는 방법](configure-private-endpoints.md)

- [DDoS 보호를 구성 하는 방법](../virtual-network/manage-ddos-protection.md)

- [Azure Security Center 통합 위협 인텔리전스에 대 한 자세한 내용은](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Azure virtual machines를 사용 하 여 Event Grid 리소스에 액세스 하는 경우 nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고 로그를 트래픽 감사에 대 한 저장소 계정으로 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

참고 Event Grid에 대해 개인 끝점을 만들 때 기본적으로 네트워크 정책은 사용 하지 않도록 설정 되므로 위의 워크플로가 작동 하지 않을 수 있습니다.

비정상적인 활동을 조사 하는 데 필요한 경우 Network Watcher 패킷 캡처를 사용 하도록 설정 합니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 제공 하는 제품을 선택 합니다.  페이로드 검사가 필요 하지 않은 경우 Azure 방화벽 위협 인텔리전스를 사용할 수 있습니다. Azure 방화벽 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과의 트래픽을 경고 및/또는 차단 하는 데 사용 됩니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

각 조직의 네트워크 경계에서 원하는 방화벽 솔루션을 배포 하 여 악성 트래픽을 검색 및/또는 차단할 수 있습니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure 방화벽을 사용 하 여 경고를 구성 하는 방법](../firewall/threat-intel.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Event Grid 리소스에 액세스 해야 하는 가상 네트워크의 리소스에 대 한 Virtual Network 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: AzureEventGrid)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

- [Azure Event Grid에 대 한 서비스 태그를 사용 하는 방법](network-security.md#service-tags)

- [서비스 태그를 사용 하는 방법에 대 한 자세한 내용](../virtual-network/service-tags-overview.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용 하 여 Azure Event Grid 네임 스페이스와 연결 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft EventGrid" 및 "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Event Grid 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 

Azure Event Grid와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.-Azure Event Grid 도메인은 개인 링크를 사용 해야 합니다. Azure Event Grid 항목은 개인 링크를 사용 해야 합니다. Azure
- [Event Grid 리소스에 대 한 기본 제공 정책](../governance/policy/samples/built-in-policies.md#event-grid)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 논리적으로 분류로 구성 하기 위해 Azure Event Grid 리소스와 연결 된 네트워크 리소스에 대 한 태그를 사용 합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Event Grid와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](/azure/security/benchmarks/security-control-logging-monitoring)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor을 통해 로그를 수집 하 여 Azure Event Grid에서 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기/보관 저장소에 저장소 계정을 사용 합니다. 또는 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다.

- [Azure Event Grid에 대 한 진단 로그를 사용 하도록 설정 하는 방법](diagnostic-logs.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 진단 설정을 통해 사용자는 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역 중 하나에서 게시 및 배달 오류 로그를 캡처 및 볼 수 Event Grid 있습니다.

- [Azure event grid 토픽 또는 도메인에 대 한 진단 로그 사용](enable-diagnostic-logs-topic.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure Event Grid 리소스와 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.

- [로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 Azure Event Grid의 결과를 정기적으로 검토 합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행할 수 있습니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용 하도록 설정할 수 있습니다. 

- [Azure event grid 토픽 또는 도메인에 대 한 진단 로그 사용](enable-diagnostic-logs-topic.md)

- [Log Analytics 작업 영역에서 Azure Event Grid에 대 한 쿼리를 수행 하는 방법](diagnostic-logs.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Log Analytics 쿼리 시작](../azure-monitor/log-query/get-started-portal.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 게시 및 배달 오류 로그에 대 한 액세스를 위해 event grid에서 진단 설정을 사용 하도록 설정 합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다. Log Analytics 작업 영역으로 로그를 보낼 수 있습니다. 보안 로그 및 이벤트에 있는 비정상적인 활동을 모니터링 하 고 경고 하는 데 Log Analytics와 함께 Azure Security Center를 사용 합니다. 

Azure Event Grid 메트릭 및 활동 로그 작업에 대 한 경고를 만들 수도 있습니다. Azure Event Grid 리소스 (토픽 및 도메인)에 대 한 게시 및 배달 메트릭에 대 한 경고를 만들 수 있습니다. 

또한 Log Analytics 작업 영역을 Azure 센티널에 등록 하 여 보안 오케스트레이션 자동화 된 응답 (대화 충성도) 솔루션을 제공할 수 있습니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다.

- [Azure Event Grid 토픽 또는 도메인에 대해 진단 로그를 사용 하도록 설정 하는 방법](enable-diagnostic-logs-topic.md)

- [Azure Event Grid 메트릭 및 활동 로그에 대 한 경고 설정](set-alerts.md)

- [Event Grid 진단 로그 스키마의 세부 정보](diagnostic-logs.md)

- [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-log.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음 Azure Event Grid는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Azure Event Grid는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](/azure/security/benchmarks/security-control-identity-access-control)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Event Grid를 사용 하면 이벤트 구독 나열, 새 구독 만들기 및 키 생성과 같은 다양 한 관리 작업을 수행 하기 위해 다른 사용자에 게 제공 되는 액세스 수준을 제어할 수 있습니다. Event Grid는 Azure의 RBAC(역할 기반 액세스 제어)를 사용합니다. Event Grid은 기본 제공 역할 및 사용자 지정 역할을 지원 합니다.

Azure 역할 기반 액세스 제어 (RBAC)를 사용 하 여 역할 할당을 통해 Azure 리소스에 대 한 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당할 수 있습니다. 특정 리소스에 대 한 미리 정의 된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal와 같은 도구를 통해 인벤토리 또는 쿼리할 수 있습니다.

- [Event Grid 리소스에 대 한 액세스 권한 부여](security-authorization.md)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Event Grid 리소스에 대 한 액세스 관리는 AD (Azure Active Directory)를 통해 제어 됩니다. Azure AD에는 기본 암호 개념이 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다.

Azure AD Privileged Identity Management 및 Azure Resource Manager를 사용 하 여 Just-in-time 액세스를 사용 하도록 설정할 수도 있습니다.

Azure event Grid 토픽 또는 도메인에 대해 관리 서비스 id를 사용 하도록 설정 하 고이를 사용 하 여 Service Bus 큐 및 토픽, event hubs, 저장소 계정 등의 지원 되는 대상으로 이벤트를 전달할 수 Event Grid. SAS (공유 액세스 서명) 토큰은 Azure Event Grid에 이벤트를 게시 하는 데 사용 됩니다. 이러한 계정을 사용 하 여 이벤트 액세스, 전달 및 게시에 대 한 표준 운영 절차를 만듭니다.

- [이벤트 처리기에 대 한 이벤트 배달 인증 (Azure Event Grid)](security-authentication.md)

- [게시 클라이언트 인증 (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Privileged Identity Management에 대 한 자세한 정보](/azure/active-directory/privileged-identity-management/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 해당 사항 없음 Event Grid 서비스는 SSO를 지원 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: 해당 사항 없음 Event Grid 서비스에서 multi-factor authentication을 사용 하지 않음

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 해당 사항 없음 권한 있는 액세스 워크스테이션이 필요한 Event Grid 시나리오는 없습니다. 

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Active Directory 보안 보고서 및 모니터링을 사용 하 여 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 시기를 검색 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 해당 사항 없음 Event Grid는 Azure AD를 사용 하 여 이벤트 게시 클라이언트를 인증 하지 않습니다. SAS 키를 통한 인증을 지원 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Azure event Grid 토픽 또는 도메인에 대해 관리 서비스 id를 사용 하도록 설정 하 고이를 사용 하 여 Service Bus 큐 및 토픽, event hubs, 저장소 계정 등의 지원 되는 대상으로 이벤트를 전달할 수 Event Grid. SAS (공유 액세스 서명) 토큰은 Azure Event Grid에 이벤트를 게시 하는 데 사용 됩니다. 

- [이벤트 처리기에 대 한 이벤트 배달 인증 (Azure Event Grid)](security-authentication.md)

- [게시 클라이언트 인증 (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure AD id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 
 
환경에서 의심 스러운 작업이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 AD (Azure Active Directory) Privileged Identity Management (PIM)를 사용 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring)

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure AD PIM(Privileged Identity Management) 배포](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: siem/모니터링 도구와 통합할 수 있도록 하는 Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure Monitor와 Azure 활동 로그를 통합 하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD ID 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.
 
 
 
- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 해당 사항 없음 Event Grid 서비스는 현재 고객 Lockbox를 지원 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](/azure/security/benchmarks/security-control-data-protection)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.
 
 
 
- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다. 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 Azure 리소스에 대 한 액세스 수준을 제한할 수 있습니다. Azure Active Directory RBAC를 통해 Azure 리소스에 대 한 액세스를 제어할 수 있습니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: microsoft에서 관리 하는 기본 플랫폼의 경우 microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure Event Grid에는 https를 게시 하 고 웹 후크 끝점에 이벤트를 전달 하는 https를 지원 합니다. Azure Global에서 Event Grid는 1.1 및 1.2 버전의 TLS를 모두 지원 하지만 1.2 버전을 사용 하는 것이 좋습니다. 중국의 21Vianet에서 운영 하는 Azure Government 및 Azure와 같은 국가별 클라우드에서는 1.2 버전의 TLS만 지원 Event Grid. 

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Event Grid 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현 합니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 관리

**지침**: Azure Event Grid에서는 AZURE ACTIVE DIRECTORY (AD)를 사용 하 여 Event Grid 리소스에 대 한 요청에 권한을 부여할 수 있습니다. Azure AD를 사용 하면 RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다.

- [Event Grid 리소스에 대 한 액세스 권한 부여](security-authorization.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 Azure Event Grid 리소스 및 기타 중요 하거나 관련 된 리소스의 프로덕션 인스턴스로 변경 될 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](/azure/security/benchmarks/security-control-vulnerability-management)를 참조 하세요.*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검색 비교

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](/azure/security/benchmarks/security-control-inventory-asset-management)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.
 
 
 
- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

* 허용되지 않는 리소스 종류
* 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)
- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

* 허용되지 않는 리소스 종류
* 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure AD 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure 리소스 관리자와 상호 작용 하는 기능을 제한 합니다.
 
 
 
- [ Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 계산 리소스에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](/azure/security/benchmarks/security-control-secure-configuration)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용 하 여 Azure Event Grid 서비스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft EventGrid" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Event Grid 서비스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

Azure Resource Manager은 구성이 배포 전에 조직의 보안 요구 사항을 충족 하는지 확인 하기 위해 검토 해야 하는 템플릿을 JavaScript Object Notation (JSON)에서 내보낼 수 있습니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다. 또한 Azure Resource Manager 템플릿을 사용 하 여 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Event Grid 또는 관련 리소스에 대 한 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: "Microsoft eventgrid" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Security Center을 사용 하 여 Azure 리소스에 대 한 기준 검색을 수행 합니다. 또한 Azure Policy를 사용 하 여 Azure 리소스 구성을 경고 하 고 감사 합니다.

- [Azure Security Center에서 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Event Grid는 공유 액세스 서명 (SAS) 토큰을 사용 하 여 이벤트를 Event Grid 토픽 또는 도메인에 게시 합니다. 제한 된 기간에 필요한 리소스에만 액세스할 수 있는 SAS 토큰을 생성 합니다.

Azure Key Vault와 함께 관리 되는 id를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 합니다.

- [게시 클라이언트 인증 (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Azure 리소스에 관리 되는 id를 사용 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault를 만드는 방법](/azure/key-vault/quick-create-portal)

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](/azure/key-vault/managed-identity)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure Event Grid 토픽 또는 도메인에 대해 관리 서비스 id를 사용 하도록 설정할 수 Event Grid. 이 ID를 사용하여 Service Bus 큐 및 토픽, Event Hubs, 스토리지 계정 등의 지원되는 대상으로 이벤트를 전달합니다.

- [관리 id를 사용 하 여 이벤트 배달](managed-service-identity.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](/azure/security/benchmarks/security-control-malware-defense)를 참조 하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Event Grid)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

비 계산 Azure 리소스에 업로드 되는 모든 콘텐츠를 미리 검색 하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 사용자를 대신해 서 고객 콘텐츠의 맬웨어 방지 검색을 수행할 수 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](/azure/security/benchmarks/security-control-data-recovery)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Event Grid에는 새로운 기능에 대 한 메타 데이터의 자동 지역 재해 복구 (geodr) 뿐만 아니라 기존의 모든 도메인, 토픽 및 이벤트 구독만 있습니다. 전체 Azure 지역이 중단 되는 경우 Event Grid는 이미 쌍을 이루는 지역에 동기화 된 모든 이벤트 관련 인프라 메타 데이터를 포함 합니다.

- [Azure Event Grid의 서버 쪽 지역 재해 복구](geo-disaster-recovery.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Event Grid에는 새로운 기능에 대 한 메타 데이터의 자동 지역 재해 복구 (geodr) 뿐만 아니라 기존의 모든 도메인, 토픽 및 이벤트 구독만 있습니다. 전체 Azure 지역이 중단 되는 경우 Event Grid는 이미 쌍을 이루는 지역에 동기화 된 모든 이벤트 관련 인프라 메타 데이터를 포함 합니다.

현재 Event Grid는 고객이 관리 하는 키를 지원 하지 않습니다. 

- [Azure Event Grid의 서버 쪽 지역 재해 복구](geo-disaster-recovery.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: Event Grid에는 새로운 기능에 대 한 메타 데이터의 자동 지역 재해 복구 (geodr) 뿐만 아니라 기존의 모든 도메인, 토픽 및 이벤트 구독만 있습니다. 전체 Azure 지역이 중단 되는 경우 Event Grid는 이미 쌍을 이루는 지역에 동기화 된 모든 이벤트 관련 인프라 메타 데이터를 포함 합니다.

현재 Event Grid는 고객이 관리 하는 키를 지원 하지 않습니다. 

- [Azure Event Grid의 서버 쪽 지역 재해 복구](geo-disaster-recovery.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하 여 실수로 또는 악의적인 삭제 로부터 키를 보호 합니다. 
 

현재 Event Grid는 고객이 관리 하는 키를 지원 하지 않습니다. 

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](/azure/security/benchmarks/security-control-incident-response)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 개발 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 해결 방법을 확인 합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 Security Center를 찾고 있는 경우와 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 따라 결정 됩니다.

 
 
 
 또한 태그를 사용 하 여 구독을 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 하 고 분류 하는 명명 시스템을 만듭니다. 사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약한 지점과 격차를 확인 한 후 필요에 따라 응답 계획을 수정 합니다.
 
 
 
- [ NIST의 게시-IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.
 
 
 
- [ Azure Security Center 보안 연락처를 설정 하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되도록 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: 워크플로 자동화 기능 Azure Security Center 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 응답을 자동으로 트리거합니다.

- [Security Center에서 워크플로 자동화를 구성 하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
