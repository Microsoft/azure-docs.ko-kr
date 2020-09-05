---
title: Event Hubs에 대 한 Azure 보안 기준
description: Event Hubs에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a6beb1023b3c934cdbb4b7dc479d87e23a7c96be
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230367"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Event Hubs에 대 한 Azure 보안 기준

Event Hubs에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 그룹: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호

**지침**: event hubs와 가상 네트워크 서비스 끝점을 통합 하면 가상 네트워크에 바인딩된 가상 컴퓨터와 같은 작업에서 메시징 기능에 안전 하 게 액세스할 수 있으며, 두 쪽 모두에서 네트워크 트래픽 경로를 안전 하 게 보호할 수 있습니다.

하나 이상의 가상 네트워크 서브넷 서비스 끝점에 바인딩되면 해당 Event Hubs 네임 스페이스는 더 이상 가상 네트워크에 있는 모든 권한이 있는 서브넷의 트래픽을 허용 하지 않습니다. 가상 네트워크 관점에서, Event Hubs 네임 스페이스를 서비스 끝점에 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리 된 네트워킹 터널이 구성 됩니다. 

Azure 개인 링크 서비스를 사용 하 여 Azure Event Hubs 서비스에 안전 하 게 연결 하는 네트워크 인터페이스인 개인 끝점을 만들 수도 있습니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 

방화벽을 사용 하 여 Azure Event Hubs 네임 스페이스를 보호할 수도 있습니다. Azure Event Hubs는 인바운드 방화벽 지원에 대 한 IP 기반 액세스 제어를 지원 합니다. Azure Portal, Azure Resource Manager 템플릿 또는 Azure CLI 또는 Azure PowerShell를 사용 하 여 방화벽 규칙을 설정할 수 있습니다.

Azure Event Hubs에서 virtual network 서비스 끝점을 사용 하는 방법: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

자세한 내용은 azure 개인 링크를 사용 하 여 Azure Event Hubs 통합을 참조 https://docs.microsoft.com/azure/event-hubs/private-link-service 하세요.

Event Hubs 네임 스페이스에서 가상 네트워크 통합 및 방화벽을 사용 하도록 설정 합니다. https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Azure Event Hubs 네임 스페이스에 대 한 IP 방화벽 규칙을 구성 하는 방법: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성과 트래픽에 대한 모니터링 및 기록

**지침**: Azure Security Center을 사용 하 고 네트워크 보호 권장 사항을 따라 Azure에서 Event Hubs 리소스를 보호 합니다. Azure virtual machines를 사용 하 여 event hubs에 액세스 하는 경우 NSG (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고 로그를 트래픽 감사에 대 한 저장소 계정으로 보냅니다.

NSG 흐름 로그를 사용하도록 설정하는 방법: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Security Center에서 제공 하는 네트워크 보안 이해: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS (배포 된 서비스 거부) 공격 으로부터 보호 하기 위해 event hubs와 연결 된 가상 네트워크에서 DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

DDoS 보호를 구성 하는 방법: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../virtual-network/manage-ddos-protection.md)

Azure Security Center 통합 된 위협 인텔리전스에 대 한 자세한 내용은 다음과 같습니다. https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure virtual machines를 사용 하 여 event hubs에 액세스 하는 경우 nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고 로그를 트래픽 감사에 대 한 저장소 계정으로 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

비정상적인 활동을 조사 하는 데 필요한 경우 Network Watcher 패킷 캡처를 사용 하도록 설정 합니다.

NSG 흐름 로그를 사용하도록 설정하는 방법: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

트래픽 분석을 사용하도록 설정하고 사용하는 방법: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Network Watcher를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Azure virtual machines를 사용 하 여 event hubs에 액세스 하는 경우 페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 제공 하는 제품을 선택 합니다. 조직에서 페이로드 검사를 기반으로 하는 침입 탐지 및/또는 방지가 필요 하지 않은 경우 Azure Event Hubs ' 기본 제공 방화벽 기능을 사용할 수 있습니다. 제한 된 범위의 IP 주소 또는 방화벽 규칙을 사용 하 여 특정 IP 주소에 대 한 Event Hubs 네임 스페이스에 대 한 액세스를 제한할 수 있습니다.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

지정 된 IP 주소에 대 한 Event Hubs에서 방화벽 규칙을 추가 하는 방법:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center 모니터링**: 아직 사용할 수 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 해당 사항에 해당 하지 않는 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램에 적합 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용 하 여 Azure Event Hubs 네임 스페이스와 연결 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft EventHub" 및 "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Event Hubs 네임 스페이스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같이 Azure Event Hubs와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.

- 이벤트 허브는 가상 네트워크 서비스 끝점을 사용 해야 합니다.

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Event Hubs 네임 스페이스에 대 한 Azure 기본 제공 정책:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



네트워킹을 위한 Azure Policy 샘플: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Azure Blueprint를 만드는 방법: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: event hubs와 연결 된 네트워크 보안 및 트래픽 흐름과 관련 된 가상 네트워크 및 기타 리소스에 대 한 태그를 사용 합니다.

태그를 만들고 사용하는 방법: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 azure Event Hubs 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

Azure 활동 로그 이벤트를 확인하고 검색하는 방법: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor에서 경고를 만드는 방법: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 그룹: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: 해당 사항 없음 Microsoft는 로그의 타임 스탬프에 대해 Azure Event Hubs와 같은 Azure 리소스에 사용 되는 시간 원본을 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor 내에서 활동 로그 및 이벤트 허브 진단 설정 내에서 event hubs와 관련 된 로그를 구성 하 여 장기 보관 저장소에 대 한 저장소 계정으로 쿼리하거나 쿼리 될 Log Analytics 작업 영역으로 로그를 보냅니다.

Azure Event Hubs에 대 한 진단 설정을 구성 하는 방법: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Azure 활동 로그 이해: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Event Hubs 네임 스페이스에 대 한 진단 설정을 사용 하도록 설정 합니다. Azure Event Hubs에 대 한 진단 설정에는 보관 로그, 작업 로그, 자동 크기 조정 로그의 세 가지 범주가 있습니다. 작업 로그를 사용 하 여 Event Hubs 작업 중 발생 하는 작업, 특히 이벤트 허브 만들기, 사용 된 리소스, 작업 상태 등의 작업 유형에 대 한 정보를 캡처할 수 있습니다.

또한 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Azure Storage 계정, 이벤트 허브 또는 Log Analytics 작업 영역으로 보낼 수 있습니다. 활동 로그는 Azure Event Hubs 및 기타 리소스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. 활동 로그를 사용 하 여 Azure Event Hubs 네임 스페이스에 대해 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정할 수 있습니다.

Azure Event Hubs에 대 한 진단 설정을 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 하 여 이벤트 허브 관련 인시던트를 캡처하고 검토 합니다.

Log Analytics 작업 영역에 대 한 로그 보존 매개 변수를 설정 하는 방법: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 event hubs와 관련 된 결과를 정기적으로 검토 합니다. Azure Monitor의 Log Analytics를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.
 

Log Analytics 작업 영역에 대 한 자세한 내용은 다음을 수행 하십시오. https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Azure Sentinel을 온보딩하는 방법: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure Monitor 내에서 활동 로그 내에서 Azure Event Hubs 관련 된 로그를 구성 하 고, 장기 보관 저장소에 대 한 저장소 계정으로 쿼리 또는 저장소 계정에 로그를 전송 하 Log Analytics는 진단 설정을 Event Hubs 합니다. Log Analytics 작업 영역을 사용 하 여 보안 로그 및 이벤트에 있는 비정상적인 활동에 대 한 경고를 만들 수 있습니다.

또는 온보드 데이터를 Azure 센티널로 설정할 수 있습니다. 

Azure 활동 로그를 이해 합니다. https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Azure Event Hubs에 대 한 진단 설정을 구성 하는 방법: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Log Analytics 작업 영역 로그 데이터를 경고 하는 방법: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Azure Sentinel을 온보딩하는 방법: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center 모니터링**: 아직 사용할 수 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음 이벤트 허브는 맬웨어 방지 로깅을 처리 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Event Hubs는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 그룹: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: AD (Azure Active Directory)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다. 

PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Event Hubs에 대 한 제어 평면 액세스는 AZURE ACTIVE DIRECTORY (AD)를 통해 제어 됩니다. Azure AD에는 기본 암호 개념이 없습니다.

Event Hubs에 대 한 데이터 평면 액세스는 관리 되는 Id 또는 앱 등록 및 공유 액세스 서명을 사용 하 여 Azure AD를 통해 제어 됩니다. 공유 액세스 서명은 event hubs에 연결 하는 클라이언트에서 사용 되며 언제 든 지 다시 생성할 수 있습니다.

Event Hubs에 대 한 공유 액세스 서명 이해: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.

- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.

- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법 (미리 보기): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Policy 사용 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Microsoft Azure에서는 AZURE ACTIVE DIRECTORY (AD)를 기반으로 하는 리소스 및 응용 프로그램에 대 한 통합 액세스 제어 관리 기능을 제공 합니다. Azure Event Hubs에서 Azure AD를 사용 하는 경우의 주요 이점은 더 이상 코드에 자격 증명을 저장할 필요가 없다는 것입니다. 대신 Microsoft Id 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 토큰을 요청 하는 리소스 이름은 https: \/ /eventhubs.azure.net/입니다. Azure AD는 응용 프로그램을 실행 하는 보안 주체 (사용자, 그룹 또는 서비스 사용자)를 인증 합니다. 인증에 성공 하면 Azure AD는 응용 프로그램에 액세스 토큰을 반환 하 고 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Event Hubs 리소스에 대 한 요청에 권한을 부여할 수 있습니다.

Event Hubs 리소스에 액세스 하기 위해 Azure AD를 사용 하 여 응용 프로그램을 인증 하는 방법: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Azure AD를 사용 하 여 SSO 이해: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: MFA (Azure Active Directory Multi-Factor Authentication)를 사용 하도록 설정 하 고 Azure Security Center id 및 액세스 관리 권장 사항에 따라 이벤트 허브 사용 리소스를 보호 합니다.

Azure에서 MFA를 사용하도록 설정하는 방법: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: MFA (권한 있는 액세스 워크스테이션)를 사용 하 여 PAW (권한 Multi-Factor Authentication 있는 액세스 워크스테이션)를 사용 하 여 이벤트 허브 사용 리소스에 로그인 하 고 구성 합니다.

Privileged Access Workstation에 대한 자세한 정보: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 사용하도록 설정하는 방법: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하려면 Azure AD(Active Directory) PIM(Privileged Identity Management)을 사용합니다. Azure AD 위험 감지를 사용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 봅니다. 추가 로깅을 수행 하려면 Azure Security Center 위험 검색 경고를 Azure Monitor에 보내고 작업 그룹을 사용 하 여 사용자 지정 경고/알림을 구성 합니다.

PIM(Privileged Identity Management)을 배포하는 방법: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure AD 위험 검색 이해: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

사용자 지정 경고 및 알림에 대 한 작업 그룹을 구성 하는 방법: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.



Azure에서 명명된 위치를 구성하는 방법: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Event Hubs와 같은 Azure 리소스에 대 한 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. 이를 통해 RBAC (역할 기반 액세스 제어)가 중요 한 리소스를 관리할 수 있습니다.

 Azure AD 인스턴스를 만들고 구성 하는 방법: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Azure Event Hubs Azure Active Directory (AAD)와 통합 하는 방법에 대해 알아보려면 Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여를 참조 하세요. https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: AD (Azure Active Directory)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

추가로 Event Hubs의 공유 액세스 서명을 정기적으로 회전 합니다.

Azure AD 보고 이해: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID 액세스 검토를 사용하는 방법: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Event Hubs에 대 한 공유 액세스 서명 이해: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 대한 액세스 시도 모니터링

**지침**: siem/모니터링 도구와 통합할 수 있는 AD (Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

Azure 활동 로그를 Azure Monitor에 통합하는 방법: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한을 부여 합니다. https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory의 id 보호 및 위험 검색 기능을 사용 하 여 Event Hubs 사용 가능한 리소스와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다. 조직의 보안 응답을 구현 하기 위해 Azure 센티널을 통해 자동화 된 응답을 사용 하도록 설정 해야 합니다.

Azure AD 위험한 로그인을 확인하는 방법: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Id 보호 위험 정책을 구성 하 고 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel을 온보딩하는 방법: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 현재 사용할 수 없음 고객 Lockbox는 Event Hubs에 대해 아직 지원 되지 않습니다.

고객 Lockbox 지원 서비스 목록: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 그룹: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스를 추적 하는 데 도움이 되도록 Event Hubs와 관련 된 리소스에 태그를 사용 합니다.

그룹을 만들고 사용하는 방법: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. Event Hubs 네임 스페이스는 서비스 끝점을 사용 하도록 설정 하 고 적절 하 게 태그를 지정 하 여 가상 네트워크로 구분 해야

방화벽을 사용 하 여 Azure Event Hubs 네임 스페이스를 보호할 수도 있습니다. Azure Event Hubs는 인바운드 방화벽 지원에 대 한 IP 기반 액세스 제어를 지원 합니다. Azure Portal, Azure Resource Manager 템플릿 또는 Azure CLI 또는 Azure PowerShell를 사용 하 여 방화벽 규칙을 설정할 수 있습니다.

추가 Azure 구독을 만드는 방법: https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법: https://docs.microsoft.com/azure/governance/management-groups/create

Azure Event Hubs 네임 스페이스에 대 한 IP 방화벽 규칙을 구성 합니다. https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

태그를 만들고 활용 하는 방법: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Virtual Network를 만드는 방법: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: virtual machines를 사용 하 여 event hubs에 액세스할 때 가상 네트워크, 서비스 끝점, Event Hubs 방화벽, 네트워크 보안 그룹 및 서비스 태그를 활용 하 여 데이터 exfiltration의 가능성을 완화 합니다.

Microsoft는 Azure Event Hubs에 대 한 기본 인프라를 관리 하 고 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

Azure Event Hubs 네임 스페이스에 대 한 IP 방화벽 규칙을 구성 합니다. https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Azure Event Hubs를 사용 하 여 Virtual Network 서비스 끝점 이해: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Azure 개인 링크와 Azure Event Hubs 통합: https://docs.microsoft.com/azure/event-hubs/private-link-service

네트워크 보안 그룹 및 서비스 태그 이해: https://docs.microsoft.com/azure/virtual-network/security-overview

Azure의 고객 데이터 보호 이해: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure Event Hubs는 기본적으로 TLS 암호화 통신을 적용 합니다. TLS 버전 1.0, 1.1 및 1.2는 현재 지원 됩니다. 그러나 TLS 1.0 및 1.1은 업계 전체를 사용 하지 않는 경로에 있으므로 가능 하면 TLS 1.2를 사용 합니다.

Event Hubs의 보안 기능을 이해 하려면 네트워크 보안:  https://docs.microsoft.com/azure/event-hubs/network-security

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Event Hubs에 대 한 데이터 식별, 분류 및 손실 방지 기능을 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure Event Hubs는 AZURE ACTIVE DIRECTORY (AD)를 사용 하 여 Event Hubs 리소스에 대 한 요청에 권한을 부여할 수 있도록 지원 합니다. Azure AD를 사용 하면 RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다.

Azure Event Hubs에 대 한 Azure AD RBAC 및 사용 가능한 역할 이해: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

Microsoft는 Event Hubs에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

Azure의 고객 데이터 보호 이해: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Event Hubs는 Microsoft에서 관리 하는 키 또는 고객이 관리 하는 키를 사용 하 여 미사용 데이터를 암호화 하는 옵션을 지원 합니다. 이 기능을 사용 하면 미사용 Azure Event Hubs 데이터를 암호화 하는 데 사용 되는 고객 관리 키에 대 한 액세스를 만들고, 회전 하 고, 사용 하지 않도록 설정할 수 있습니다.

Azure Event Hubs을 암호화 하기 위해 고객이 관리 하는 키를 구성 하는 방법: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: azure 활동 로그에서 Azure Monitor를 사용 하 여 azure Event Hubs의 프로덕션 인스턴스 및 기타 중요 한 리소스 또는 관련 된 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

Azure 활동 로그 이벤트에 대한 경고를 만드는 방법: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 그룹: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 해당 사항 없음 Microsoft는 Event Hubs을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 Microsoft는 Event Hubs을 지 원하는 기본 시스템에서 패치 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 해당 사항 없음 Microsoft는 Event Hubs을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 해당 사항 없음 Microsoft는 Event Hubs을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 그룹: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 모든 리소스 (azure Event Hubs 네임 스페이스 포함)를 쿼리하고 검색 합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

Azure Resource Graph를 사용하여 쿼리를 만드는 방법: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 확인하는 방법: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC 이해: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

태그를 만들고 사용하는 방법: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 적절 한 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure Event Hubs 네임 스페이스 및 관련 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

추가 Azure 구독을 만드는 방법: https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법: https://docs.microsoft.com/azure/governance/management-groups/create

그룹을 만들고 사용하는 방법: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스와 Azure 전체를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다.

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph를 사용하여 쿼리를 만드는 방법: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스와 Azure 전체를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 애플리케이션 목록 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>스크립트를 통해 Azure Resource Manager와 상호 작용하는 사용자 기능 제한</div>

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 그룹: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Event Hubs 배포에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft EventHub" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같이 Azure Event Hubs에 대 한 기본 제공 정책 정의를 사용할 수도 있습니다.

- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.

- Event Hub는 가상 네트워크 서비스 엔드포인트를 사용해야 함

Event Hubs 네임 스페이스에 대 한 Azure 기본 제공 정책: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

사용 가능한 Azure 정책 별칭을 확인하는 방법: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 Event Hubs 사용 가능한 리소스에서 보안 설정을 적용 합니다. 

Azure Policy를 구성 하 고 관리 하는 방법:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Azure Policy 효과에 대 한 자세한 내용은 다음과 같습니다.  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Event Hubs 또는 관련 리소스에 대 한 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

코드를 Azure DevOps에 저장하는 방법: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 설명서: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: "Microsoft EventHub" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft EventHub" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure Event Hubs 배포 및 관련 리소스에 대 한 구성을 자동으로 적용 하려면 Azure Policy [감사], [거부] 및 [배포 되지 않은 경우 배포]를 사용 합니다.

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: event hubs에 액세스 하는 데 사용 되는 Azure App Service에서 실행 되는 azure 가상 머신 또는 웹 응용 프로그램의 경우 Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 azure Event Hubs 배포에 대 한 공유 액세스 서명 관리를 간소화 하 고 보호 합니다. Key Vault 일시 삭제를 사용 하도록 설정 했는지 확인 합니다.

Event Hubs 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Event Hubs에 대 한 고객 관리 키 구성: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Azure 관리 ID와 통합하는 방법: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault를 만드는 방법: https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리 ID를 사용하여 Key Vault 인증을 제공하는 방법: https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: event hubs에 액세스 하는 데 사용 되는 Azure App Service에서 실행 되는 azure 가상 머신 또는 웹 응용 프로그램의 경우 Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 azure Event Hubs를 간소화 하 고 보호 합니다. Key Vault 일시 삭제를 사용 하도록 설정 했는지 확인 합니다.

관리 ID를 사용하여 Azure AD(Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리 Id를 사용 하면 코드에 자격 증명 없이 Azure Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

Event Hubs 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Event Hubs에 대 한 고객 관리 키 구성: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

관리 ID를 구성하는 방법: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure 관리 ID와 통합하는 방법: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

자격 증명 스캐너를 설정하는 방법: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 그룹: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스(예: Azure App Service)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: 비 계산 azure 리소스 (예: azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL 등)로 업로드 되는 콘텐츠를 미리 검색 합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

Microsoft 맬웨어 방지는 Azure 서비스 (예: Azure Cache for Redis)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 그룹: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Event Hubs에 대 한 지역 재해 복구를 구성 합니다. (가용성 영역을 사용하지 않는 경우)전체 Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생하면 데이터 처리가 다른 지역 또는 데이터 센터에서 계속 작동되는 것이 중요합니다. 따라서 지리적 재해 복구 및 지리적 복제는 기업에 중요한 기능입니다. Azure Event Hubs는 네임스페이스 수준에서 지리적 재해 복구 및 지리적 복제를 둘 다 지원합니다. 

Azure Event Hubs에 대 한 지역 재해 복구 이해: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: azure Event Hubs는 azure SSE (Azure Storage 서비스 암호화)를 사용 하 여 미사용 데이터의 암호화를 제공 합니다. Event Hubs는 Azure Storage를 사용 하 여 데이터를 저장 하 고, 기본적으로 Azure Storage와 함께 저장 되는 모든 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. Azure Key Vault를 사용 하 여 고객이 관리 하는 키를 저장 하는 경우 키의 자동 백업을 정기적으로 수행 해야 합니다.

다음 PowerShell 명령을 사용 하 여 Key Vault 암호를 정기적으로 자동 백업 하도록 합니다. Backup-AzKeyVaultSecret

미사용 Azure Event Hubs 데이터를 암호화 하기 위해 고객이 관리 하는 키를 구성 하는 방법: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Key Vault 비밀을 백업 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 백업 된 고객 관리 키의 복원 테스트

 

Azure에서 주요 자격 증명 모음 키를 복원 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Key Vault에서 일시 삭제를 사용 하도록 설정 하 여 실수로 또는 악의적인 삭제 로부터 키를 보호 합니다. Azure Event Hubs에는 고객이 관리 하는 키에 일시 삭제 및 구성 된 제거 안 함이 필요 합니다.

Event Hubs 데이터를 캡처하는 데 사용 되는 Azure Storage 계정에 대해 일시 삭제를 구성 합니다. 이 기능은 Azure Data Lake Storage Gen 2에서 아직 지원 되지 않습니다.

Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

키를 사용 하 여 키 자격 증명 모음을 설정 합니다. https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Azure Storage blob에 대 한 일시 삭제: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 그룹: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 직원의 역할 및 인시던트 처리/관리 단계를 정의 하는 사고 대응 계획을 작성 해야 합니다.

Azure Security Center 내에서 워크플로 자동화를 구성하는 방법: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 경고에 대 한 심각도를 할당 하 여 각 경고에 참석 하는 순서의 우선 순위를 지정할 수 있도록 합니다. 따라서 리소스가 손상 되 면 즉시 가져올 수 있습니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 응답 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

NIST 게시물: IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드 참조: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.  문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

Azure Security Center 보안 연락처를 설정하는 방법: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Sentinel로 스트림할 수 있습니다.

연속 내보내기를 구성하는 방법: https://docs.microsoft.com/azure/security-center/continuous-export

경고를 Azure Sentinel로 스트림하는 방법: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 "Logic Apps"를 통해 보안 경고 및 추천 사항에 대한 대응을 자동으로 트리거합니다.

워크플로 자동화 및 Logic Apps를 구성하는 방법: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 그룹: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 60일 이내에 수정

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 하세요.
Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트에 대 한 Microsoft의 전략 및 실행에 대 한 자세한 내용은 다음에서 확인할 수 있습니다. https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
