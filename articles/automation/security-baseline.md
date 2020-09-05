---
title: Azure Automation에 대 한 Azure 보안 기준
description: Automation의 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: abc8ee3b79b43676ce114094af5614b1a2d945bf
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230996"
---
# <a name="azure-security-baseline-for-automation"></a>Automation의 Azure 보안 기준

Automation의 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure Automation 계정은 개인 끝점을 통해 서비스에 대 한 액세스를 제한 하기 위해 Azure 개인 링크를 아직 지원 하지 않습니다. Azure의 리소스에 대해 인증 되 고 실행 되는 runbook은 Azure 샌드박스에서 실행 되며, Microsoft가 서로 격리 하는 공유 백 엔드 리소스를 활용 합니다. 해당 네트워킹은 무제한 이며 공용 리소스에 액세스할 수 있습니다. 현재 Azure Automation에는 Hybrid Runbook Worker에 대 한 지원 이외에 개인 네트워킹에 대 한 가상 네트워크 통합이 없습니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Runbook에 대 한 추가 격리를 얻으려면 Azure virtual machines에서 실행 되는 Hybrid Runbook Worker를 사용할 수 있습니다. Azure 가상 컴퓨터를 만들 때 VNet (가상 네트워크)을 만들거나 기존 VNet을 사용 하 고 서브넷을 사용 하 여 Vm을 구성 해야 합니다. 배포 된 모든 서브넷에 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 특정 한 네트워크 액세스 제어와 함께 적용 되는 네트워크 보안 그룹이 있는지 확인 합니다. 서비스 관련 요구 사항에 대해서는 해당 특정 서비스에 대 한 보안 권장 사항을 참조 하세요.

또는 특정 요구 사항이 있는 경우 Azure 방화벽을 충족 하는 데 사용할 수도 있습니다.

* [Azure의 가상 네트워크 및 가상 머신](../virtual-machines/network-overview.md)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure 방화벽을 배포 및 구성 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Runbook 실행 환경](./automation-runbook-execution.md#runbook-execution-environment)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: 현재 Azure Automation는 Hybrid Runbook worker에 대 한 지원 이외에 개인 네트워킹에 대 한 가상 네트워크 통합이 없습니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Azure virtual machines에서 지 원하는 Hybrid Runbook Worker를 사용 하는 경우 해당 작업자를 포함 하는 서브넷이 NSG (네트워크 보안 그룹)를 사용 하도록 설정 되어 있는지 확인 하 고 흐름 로그를 구성 하 여 트래픽 감사를 위해 저장소 계정에 로그를 전달 합니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 전달 하 고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

NSG 규칙 및 사용자 정의 경로는 개인 끝점에 적용 되지 않지만 아웃 바운드 연결에 대 한 NSG 흐름 로그 및 모니터링 정보는 계속 지원 되며 사용할 수 있습니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 현재는 Hybrid Runbook worker에 대 한 지원 이외에 개인 네트워킹에 대 한 가상 네트워크 통합이 Azure Automation. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Azure virtual machines에서 지원 되는 Hybrid Runbook Worker를 사용 하는 경우 DDoS 공격 으로부터 보호 하기 위해 Hybrid Runbook Worker를 호스트 하는 가상 네트워크에서 DDoS (Distributed Service Services) 표준 보호를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용 하 여 알려진 악성 IP 주소와의 통신을 거부할 수 있습니다. 위협 인텔리전스를 사용 하 여 각 Virtual Network 세그먼트에서 Azure 방화벽을 구성 하 고 악의적인 네트워크 트래픽에 대해 **경고 및 거부** 하도록를 구성 합니다.

Azure Security Center의 Just-in-time 네트워크 액세스를 사용 하 여 제한 된 시간 동안 Windows 가상 컴퓨터의 노출을 승인 된 IP 주소로 제한할 수 있습니다. 또한 NSG 구성에 대 한 적응 네트워크 강화 권장 사항을 Azure Security Center 사용 하 여 실제 트래픽 및 위협 인텔리전스에 따라 포트 및 원본 Ip를 제한 합니다.

* [DDoS 보호를 구성 하는 방법](../virtual-network/manage-ddos-protection.md)

* [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/threat-protection.md)

* [적응 네트워크 강화 Azure Security Center 이해](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center Just-in-time 네트워크 Access Control 이해](../security-center/security-center-just-in-time.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: 현재는 Hybrid Runbook worker에 대 한 지원 외에도 개인 네트워킹에 대 한 가상 네트워크 통합을 Azure Automation 하지 않습니다. hybrid worker가 없는 기본 제공 서비스를 사용 하는 경우에는이 제어를 적용할 수 없습니다.

Azure virtual machines에서 지 원하는 Hybrid Runbook Worker를 사용 하는 경우 저장소 계정에 NSG 흐름 로그를 기록 하 여 Runbook worker 역할을 하는 Azure Virtual Machines에 대 한 흐름 레코드를 생성할 수 있습니다. 비정상적인 활동을 조사할 때 네트워크 트래픽을 비정상적이 고 예기치 않은 활동에 대해 검토할 수 있도록 Network Watcher 패킷 캡처를 사용 하도록 설정할 수 있습니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 현재는 Hybrid Runbook worker에 대 한 지원 이외에 개인 네트워킹에 대 한 가상 네트워크 통합이 Azure Automation. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Azure virtual machines에서 호스트 되는 Hybrid Runbook Worker를 사용 하는 경우 Network Watcher에서 제공 하는 패킷 캡처와 오픈 소스 ID 도구를 결합 하 여 해당 작업자 컴퓨터에 대해 광범위 한 위협에 대 한 네트워크 침입 검색을 수행할 수 있습니다. 또한 위협 인텔리전스를 사용 하도록 설정 하 고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성 하 여 Virtual Network 세그먼트에 Azure 방화벽을 적절 하 게 배포할 수 있습니다.

* [Network Watcher 및 오픈 소스 도구를 사용하여 네트워크 침입 검색 수행](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure 방화벽을 사용 하 여 경고를 구성 하는 방법](../firewall/threat-intel.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Virtual Network 서비스 태그를 사용 하 여 azure에서 구성 된 네트워크 보안 그룹 또는 azure 방화벽에서 자동화 리소스에 액세스 해야 하는 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: GuestAndHybridManagement)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

* [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용 하 여 Azure Automation에서 사용 하는 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.

Azure 청사진을 사용 하 여 azure 리소스 관리자 템플릿, Azure RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 단일 청사진 정의로 패키지화 하 여 대규모 Azure 배포를 간소화할 수도 있습니다. 청사진을 새 구독에 적용 하 고 버전 관리를 통해 제어 및 관리를 세부적으로 조정할 수 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [네트워킹에 대 한 Azure Policy 샘플](/azure/governance/policy/samples/#network)

* [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 네트워크 보안 및 트래픽 흐름과 관련 된 nsgs 및 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 리소스 구성을 모니터링 하 고 네트워크 리소스에 대 한 변경 내용을 검색 합니다. 중요 한 리소스의 변경 내용이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft는 Azure 리소스에 대 한 시간 소스를 유지 관리 합니다. 그러나 Windows 가상 머신에서 실행 되는 모든 Hybrid Runbook Worker에 대 한 시간 동기화 설정을 관리할 수 있는 옵션이 있습니다.

* [Azure 계산 리소스에 대 한 시간 동기화를 구성 하는 방법](../virtual-machines/windows/time-sync.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 로그 데이터를 Azure Monitor 로그에 전달 하 여 Azure Automation 리소스에 의해 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 로그 쿼리를 사용 하 여 분석을 검색 및 수행 하 고 장기/보관 저장소에 대 한 Azure Storage 계정을 사용 합니다. Azure Automation는 runbook 작업 상태, 작업 스트림, 자동화 상태 구성 데이터, 업데이트 관리, 변경 내용 추적 또는 인벤토리 로그를 Log Analytics 작업 영역으로 보낼 수 있습니다. 이 정보는 간단한 조사를 수행 하는 데 사용할 수 있는 Azure Portal, Azure PowerShell 및 Azure Monitor 로그 API에서 볼 수 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md)

* [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Azure Monitor 로그에 Azure Automation 작업 데이터 전달](./automation-manage-send-joblogs-log-analytics.md)

* [Azure Monitor 로그와 DSC 통합](./automation-dsc-diagnostics.md)

* [연결된 Log Analytics 작업 영역에 대해 지원되는 지역](./how-to/region-mappings.md)

* [업데이트 관리 로그 쿼리](./update-management/update-mgmt-query-logs.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 이벤트 원본, 날짜, 사용자, 타임 스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소를 포함 하는 감사 및 활동 로그에 대 한 액세스를 Azure Monitor를 사용 하도록 설정 합니다.

* [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md)

* [Azure 활동 로그 이벤트를 보고 검색하기](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 다중 테 넌 트 runbook 작업자와 Azure Automation를 사용 하는 경우이 제어는 적용 되지 않으며 플랫폼은 기본 가상 머신을 처리 합니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 Azure Security Center Windows 가상 머신에 대 한 보안 이벤트 로그 모니터링을 제공 합니다. 조직에서 보안 이벤트 로그 데이터를 유지 하려는 경우 데이터 컬렉션 계층 내에 저장할 수 있으며,이 시점에서 Log Analytics 쿼리할 수 있습니다. 다음과 같은 다양 한 계층이 있습니다. 최소, 공통 및 모두, 다음 링크에 자세히 설명 되어 있습니다.

* [Azure Security Center 내에서 데이터 컬렉션 계층 구성](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. Azure Storage 계정을 장기/보관 스토리지에 사용합니다.

* [Log Analytics에서 데이터 보존 기간 변경](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Automation 계정에 대 한 데이터 보존 세부 정보](./automation-managing-data.md#data-retention)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. Azure Monitor 로그 쿼리를 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행 합니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [Azure Monitor의 로그 쿼리 이해](../azure-monitor/log-query/get-started-portal.md)

* [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 보안 로그 및 이벤트에 있는 비정상적인 활동을 모니터링 하 고 경고 하는 데 Azure Monitor와 함께 Azure Security Center를 사용 합니다.

또는 온보드 데이터를 Azure 센티널로 설정할 수 있습니다.

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

* [Azure Security Center에서 경고를 관리 하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

* [Azure Monitor 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 다중 테 넌 트 runbook worker와 Azure Automation를 사용 하는 경우이 컨트롤은 적용 되지 않으며 플랫폼은 기본 가상 머신을 처리 합니다.

그러나 Hybrid Runbook Worker 기능을 사용 하는 경우 Azure Cloud Services 및 virtual machines에 대해 Microsoft 맬웨어 방지 프로그램을 사용할 수 있습니다. Azure Storage 계정에 이벤트를 기록 하도록 가상 컴퓨터를 구성 합니다. 저장소 계정에서 이벤트를 수집 하 고 해당 하는 경우 경고를 생성 하도록 Log Analytics 작업 영역을 구성 합니다. Azure Security Center "계산 앱"의 권장 사항을 따릅니다 &amp; .

* [Cloud Services 및 Virtual Machines에 대해 Microsoft 맬웨어 방지를 구성 하는 방법](../security/fundamentals/antimalware.md)

* [가상 컴퓨터에 대 한 게스트 수준 모니터링을 사용 하도록 설정 하는 방법](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: DNS 로깅 솔루션에 대 한 Azure Marketplace에서 조직의 요구에 따라 타사 솔루션을 구현 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 다중 테 넌 트 runbook 작업자와 Azure Automation를 사용 하는 경우이 제어는 적용 되지 않으며 플랫폼은 기본 가상 머신을 처리 합니다.

그러나 Hybrid Runbook Worker 기능을 사용 하는 경우 Azure Security Center는 Azure virtual machines에 대 한 보안 이벤트 로그 모니터링을 제공 합니다. Security Center은 지원 되는 모든 Azure Vm에 대 한 Log Analytics 에이전트와 자동 프로비저닝을 사용 하도록 설정 된 경우 생성 되는 새 Vm을 프로 비전 합니다. 또는 에이전트를 수동으로 설치할 수 있습니다. 에이전트를 사용 하면 프로세스 만들기 이벤트 4688 및 이벤트 4688 내의 명령줄 필드를 사용할 수 있습니다. VM에서 생성 되는 새 프로세스는 이벤트 로그에 기록 되 고 Security Center의 검색 서비스에 의해 모니터링 됩니다.

* [Azure Security Center에서 데이터 수집](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: 명시적으로 할당 될 수 있고 쿼리할 수 있는 Azure Active Directory 기본 제공 관리자 역할을 사용 합니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다. Runbook에 Automation 계정 실행 계정을 사용 하는 경우 항상 상승 된 권한이 있는 경우에도 이러한 서비스 사용자를 인벤토리에 추적 해야 합니다. 노출 된 공격 노출 영역을 최소화 하려면 사용 하지 않는 실행 계정을 삭제 합니다.

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [실행 또는 클래식 실행 계정 삭제](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Azure Automation 실행 계정 관리](./manage-runas-account.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure Automation 계정에는 기본 암호 개념이 없습니다. 고객은 서비스 또는 해당 Hybrid Runbook Worker에서 실행 되는 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 마켓플레이스 서비스를 담당 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다. Runbook에 Automation 계정 실행 계정을 사용 하는 경우 항상 상승 된 권한이 있는 경우에도 이러한 서비스 사용자를 인벤토리에 추적 해야 합니다. Runbook이 자동화 된 프로세스를 성공적으로 수행 하기 위해 필요한 최소 권한 있는 권한으로 이러한 id의 범위를 부여 합니다. 노출 된 공격 노출 영역을 최소화 하려면 사용 하지 않는 실행 계정을 삭제 합니다.

Microsoft 서비스에 대해 Azure AD Privileged Identity Management 권한 있는 역할을 사용 하 고 Azure Resource Manager 하 여 Just-in-time/간단 하 게 액세스할 수 있도록 설정할 수도 있습니다.

* [Privileged Identity Management에 대 한 자세한 정보](../active-directory/privileged-identity-management/index.yml)

* [실행 또는 클래식 실행 계정 삭제](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Azure Automation 실행 계정 관리](./manage-runas-account.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 가능 하면 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신 AZURE ACTIVE DIRECTORY에서 SSO를 사용 합니다. Azure Security Center Id 및 액세스 관리 권장 사항을 사용 합니다.

* [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

* [Azure AD를 사용하여 Azure에 인증](./automation-use-azure-ad.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: AZURE AD MFA (multi-factor authentication)를 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터를 사용 하십시오.

**지침**: 프로덕션 환경에서 Azure Automation 계정 리소스에 로그인 하 고 구성 하도록 구성 된 multi-factor authentication과 함께 paw를 사용 합니다.

* [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure AD 위험 검색을 활용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 봅니다. 필요에 따라 고객이 작업 그룹을 사용 하 여 Azure Monitor 하 고 사용자 지정 경고/알림을 구성할 Azure Security Center 위험 검색 경고를 전달할 수 있습니다.

* [Azure Security Center 위험 탐지 이해 (의심 스러운 활동)](../active-directory/identity-protection/overview-identity-protection.md)

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [사용자 지정 경고 및 알림에 대 한 작업 그룹을 구성 하는 방법](../azure-monitor/platform/action-groups.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 명명 된 조건부 액세스 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 하는 것이 좋습니다.

* [Azure에서 명명 된 위치를 구성 하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다. Hybrid Runbook Worker를 사용 하는 경우 실행 계정 대신 관리 되는 id를 활용 하 여 보다 원활한 보안 권한을 사용할 수 있습니다.

* [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory-domain-services/tutorial-create-instance.md)

* [관리 id로 runbook 인증 사용](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. Runbook에 대 한 Automation 계정 실행 계정을 사용 하는 경우 언제 든 지 관리자 권한이 있는 경우에도 이러한 서비스 사용자를 인벤토리에 추적할 수 있습니다. 노출 된 공격 노출 영역을 최소화 하려면 사용 하지 않는 실행 계정을 삭제 합니다.

* [Azure AD 보고 이해](../active-directory/reports-monitoring/index.yml)

* [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

* [실행 또는 클래식 실행 계정 삭제](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Azure Automation 실행 계정 관리](./manage-runas-account.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 소스에 액세스가 가능하므로 SIEM/모니터링 툴과 통합할 수 있습니다.

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics Workspace로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD 위험 및 id 보호 기능을 사용 하 여 네트워크 리소스에 대 한 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: Azure Automation 계정의 경우 Microsoft 지원에서 다른 도구를 사용 하지 않고 오픈 지원 케이스 중에 플랫폼 리소스 메타 데이터에 액세스할 수 있습니다.

그러나 Azure virtual machines에서 지원 되는 Hybrid Runbook Worker를 사용 하 고 타사에서 고객 데이터에 액세스 해야 하는 경우 (예: 지원 요청 시) Azure virtual machines에 대해 고객 Lockbox (미리 보기)를 사용 하 여 고객 데이터 액세스 요청을 검토 하 고 승인 하거나 거부 합니다.

* [고객 Lockbox 이해](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure Automation 리소스를 추적 하는 데 도움을 줍니다.

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 별도의 자동화 계정 리소스를 사용 하 여 환경을 격리 합니다. Hybrid Runbook Worker와 같은 리소스는 가상 네트워크/서브넷으로 분리 되 고, 적절 하 게 태그가 지정 되 고, NSG (네트워크 보안 그룹) 또는 Azure 방화벽 내에서 보호 되어야 합니다. 중요 한 데이터를 저장 하거나 처리 하는 가상 컴퓨터의 경우 정책 및 프로시저를 구현 하 여 사용 하지 않을 때이를 해제 합니다.

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md)

* [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure 방화벽을 사용 하 여 경고 또는 경고 및 거부를 구성 하는 방법](../firewall/threat-intel.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Hybrid Runbook Worker 기능을 사용 하는 경우 중요 한 정보를 무단으로 전송 하는 작업을 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송 작업을 차단 하는 네트워크 경계의 Azure Marketplace에서 타사 솔루션을 활용할

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요 한 정보를 암호화 합니다. Azure 가상 네트워크의 Azure 리소스에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다. Azure Automation은 TLS (전송 계층) 1.2 및 모든 클라이언트 호출을 모든 외부 HTPS 끝점 (webhook, DSC 노드, 하이브리드 runbook worker를 통해)에 대해 완벽 하 게 지원 하 고 적용 합니다.

해당 하는 경우 미사용 암호화 및 전송 중인 암호화에 대 한 Azure Security Center 권장 사항을 따릅니다.

* [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure Automation TLS 1.2 적용](https://azure.microsoft.com/updates/azure-automation-tls12-enforcement/)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 타사 활성 검색 도구를 사용 하 여 온사이트 또는 원격 서비스 공급자를 비롯 하 여 조직의 기술 시스템에서 저장, 처리 또는 전송 하는 중요 한 정보를 모두 식별 하 고 조직의 중요 한 정보 인벤토리를 업데이트 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 기본 제공 역할 정의를 사용 하 여 Azure Automation 리소스에 대 한 액세스를 제어 하 고, 최소 권한 또는 ' 간단히 ' 액세스 모델에 따라 자동화 리소스에 액세스 하는 사용자에 게 액세스 권한을 할당 합니다. Hybrid Runbook Worker를 사용 하는 경우 해당 가상 컴퓨터에 대 한 관리 id를 활용 하 여 서비스 사용자를 사용 하지 않도록 합니다. 다중 테 넌 트 또는 Hybrid Runbook Worker를 모두 사용 하는 경우 runbook worker의 id에 대해 적절 하 게 범위가 지정 된 Azure RBAC 권한을 적용 해야 합니다.

* [Azure RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

* [Hybrid Runbook Worker에 대한 Runbook 권한](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [역할 권한 및 보안 관리](./automation-role-based-access-control.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: Azure Automation는 현재 기본 다중 테 넌 트 runbook worker의 가상 머신을 노출 하지 않으며,이는 플랫폼에 의해 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Azure virtual machines에서 지원 되는 Hybrid Runbook Worker를 사용 하는 경우 호스트 된 Hybrid Runbook Worker 가상 컴퓨터에 대 한 액세스 제어를 적용 하려면 타사 호스트 기반 데이터 손실 방지 솔루션을 사용 해야 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Automation에서 고객 관리 키를 사용 합니다. Azure Automation는 고객 관리 키를 사용 하 여 자격 증명, 인증서, 연결, 암호화 된 변수 등의 모든 ' 보안 자산 '을 암호화 하는 기능을 지원 합니다. 의도 하지 않은 노출을 방지 하기 위해 모든 영구 변수 조회 요구 사항에 대해 runbook과 함께 암호화 된 변수를 활용 합니다.

Hybrid Runbook Worker를 사용 하는 경우 가상 컴퓨터의 가상 디스크는 서버 쪽 암호화 또는 ADE (Azure disk encryption)를 사용 하 여 미사용에 암호화 됩니다. Azure disk encryption은 Windows의 BitLocker 기능을 활용 하 여 게스트 VM 내에서 고객 관리 키를 사용 하 여 관리 디스크를 암호화 합니다. 고객 관리형 키를 사용하는 서버 쪽 암호화는 스토리지 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 유형 및 이미지를 사용할 수 있도록 설정하여 ADE에서 향상됩니다.

* [Azure managed disks의 서버 쪽 암호화](../virtual-machines/windows/disk-encryption.md)

* [Windows Vm에 대 한 Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md)

* [Automation 계정에 대해 고객 관리형 키 사용](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure Automation에서 관리 되는 변수](./shared-resources/variables.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 네트워킹 구성 요소, Azure Automation 계정, runbook과 같은 중요 한 Azure 리소스에 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

* [네트워크 보안 그룹에 대한 진단 로깅](../private-link/private-link-overview.md#logging-and-monitoring)

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure 리소스에 대 한 취약성 평가를 수행 하는 Azure Security Center의 권장 사항 준수

* [Azure Security Center의 보안 권장 사항](../security-center/security-center-recommendations.md)

* [Security Center 권장 사항 참조](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: Azure Automation는 현재 기본 다중 테 넌 트 runbook worker의 가상 머신을 노출 하지 않으며,이는 플랫폼에 의해 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Azure virtual machines에서 지원 되는 Hybrid Runbook Worker를 사용 하는 경우 Azure 업데이트 관리를 사용 하 여 가상 머신에 대 한 업데이트 및 패치를 관리 합니다. 업데이트 관리는 로컬로 구성 된 업데이트 리포지토리를 사용 하 여 지원 되는 Windows 시스템을 패치 합니다. System Center Updates Publisher (Updates Publisher)와 같은 도구를 사용 하 여 WSUS (Windows Server Update Services)에 사용자 지정 업데이트를 게시할 수 있습니다. 이 시나리오를 사용 하면 Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어를 사용 하 여 업데이트 리포지토리로 패치 업데이트 관리 수 있습니다.

* [Azure의 업데이트 관리](./update-management/update-mgmt-overview.md)

* [Vm에 대 한 업데이트 및 패치 관리](./update-management/update-mgmt-manage-updates-for-vm.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: Azure Automation는 현재 기본 다중 테 넌 트 runbook worker의 가상 머신을 노출 하지 않으며이는 플랫폼에서 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Azure virtual machines에서 지원 되는 Hybrid Runbook Worker를 사용 하는 경우 Azure 업데이트 관리를 사용 하 여 가상 머신에 대 한 업데이트 및 패치를 관리할 수 있습니다. 업데이트 관리는 로컬로 구성 된 업데이트 리포지토리를 사용 하 여 지원 되는 Windows 시스템을 패치 합니다. System Center Updates Publisher (Updates Publisher)와 같은 도구를 사용 하면 사용자 지정 업데이트를 WSUS (Windows Server Update Services)에 게시할 수 있습니다. 이 시나리오를 통해 업데이트 관리은 Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어로 업데이트 리포지토리로 패치할 수 있습니다.

* [Azure의 업데이트 관리 솔루션](./update-management/update-mgmt-overview.md)

* [Azure VM에 대한 업데이트 및 패치 관리](./update-management/update-mgmt-manage-updates-for-vm.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 검색 결과를 일관 된 간격으로 내보내고 결과를 비교 하 여 취약성이 수정 되었는지 확인 합니다. Azure Security Center에서 제안 하는 취약성 관리 권장 사항을 사용 하는 경우 고객은 선택한 솔루션의 포털로 피벗 하 여 기록 검색 데이터를 볼 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Azure Security Center에서 제공 하는 기본 위험 등급 (보안 점수)을 사용 하 여 검색 된 취약성에 대 한 수정의 우선 순위를 지정 합니다.

* [보안 점수 Azure Security Center 이해](../security-center/secure-score-security-controls.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 Asset Discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 모든 Azure Automation 리소스를 쿼리하고 검색 합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

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

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure Automation 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다. 노출 된 공격 노출 영역을 최소화 하려면 사용 하지 않는 실행 계정을 삭제 합니다.

* [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

* [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

* [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

* [실행 또는 클래식 실행 계정 삭제](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Azure Automation 실행 계정 관리](./manage-runas-account.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대해 승인 된 소프트웨어의 인벤토리를 만들어야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 이는 저장소 계정을 사용 하는 환경 등의 높은 보안 기반 환경에서 유용할 수 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

* [Azure Automation에 대 한 Azure Policy 샘플 기본 제공](./policy-samples.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure Automation 제품은 현재 기본 다중 테 넌 트 runbook worker의 가상 컴퓨터를 노출 하지 않으며이는 플랫폼에 의해 처리 됩니다. Hybrid Worker가 없는 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다. 그러나 runbook이 포털 또는 cmdlet을 통해 액세스할 수 있는 PowerShell 또는 Python 모듈을 설치, 제거 및 관리할 수 있습니다. Runbook에 대해 승인 되지 않았거나 이전 모듈을 제거 하거나 업데이트 해야 합니다.

Azure Virtual Machines에서 지 원하는 Hybrid Runbook Worker를 사용 하는 경우 Azure Automation는 워크 로드 및 리소스의 배포, 운영 및 서비스 해제 중에 완전 한 제어를 제공 합니다. Azure Virtual Machine 인벤토리를 활용 하 여 Virtual Machines의 모든 소프트웨어에 대 한 정보 수집을 자동화할 수 있습니다. Azure Portal에서 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간을 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대 한 액세스 권한을 얻으려면 게스트 수준 진단을 사용 하도록 설정 하 고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.

* [Azure Automation 소개](./automation-intro.md)

* [Azure VM 인벤토리를 사용 하도록 설정 하는 방법](./automation-tutorial-installed-software.md)

* [Azure Automation에서 모듈 관리](./shared-resources/modules.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 고객은 고객의 회사 지침에 따라 리소스를 만들거나 사용 하는 데 필요한 Azure Policy를 방지할 수 있습니다. 권한 없는 리소스를 제거 하는 고유한 프로세스를 구현할 수 있습니다. Azure Automation에서 제공 하는 PowerShell 또는 runbook을 통해 포털 또는 cmdlet을 통해 액세스할 수 있는 Python 모듈을 설치, 제거 및 관리할 수 있습니다. Runbook에 대해 승인 되지 않았거나 이전 모듈을 제거 하거나 업데이트 해야 합니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Automation에서 모듈 관리](./shared-resources/modules.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Hybrid Runbook Worker 기능을 사용 하는 경우 Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한 있는 소프트웨어만 실행 되 고 모든 권한이 없는 소프트웨어가 Azure Virtual Machines에서 실행 되지 않도록 차단할 수 있습니다.

* [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: Hybrid Runbook Worker 기능을 사용 하는 경우 하이브리드 작업자 가상 머신과 Azure Security Center 적응 응용 프로그램 제어 기능을 사용할 수 있습니다.

적응 응용 프로그램 제어는 Azure 및 비 Azure 컴퓨터 (Windows 및 Linux)에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Azure Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 조직의 요구 사항에 맞지 않는 경우 타사 솔루션을 구현 합니다.

* [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure 조건부 액세스 정책을 사용 하 여 보안 되지 않거나 승인 되지 않은 위치 또는 장치에서 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 하 여 사용자의 Azure Resource Manager 상호 작용 하는 기능을 제한 합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: Hybrid Runbook Worker 기능을 사용할 때 스크립트의 유형에 따라 운영 체제별 구성 또는 타사 리소스를 사용 하 여 Azure compute 리소스 내에서 스크립트를 실행 하는 사용자의 기능을 제한할 수 있습니다. Azure Security Center 적응 응용 프로그램 제어를 활용 하 여 권한 있는 소프트웨어만 실행 되 고 권한이 없는 모든 소프트웨어가 Azure Virtual Machines에서 실행 되지 않도록 차단할 수도 있습니다.

* [Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: azure 환경에 배포 된 위험도 높은 응용 프로그램은 가상 네트워크, 서브넷, 구독, 관리 그룹 등의 구문을 사용 하 여 별도의 네트워크 및 리소스 컨테이너를 사용 하 여 격리할 수 있으며, azure 방화벽, Waf (웹 응용 프로그램 방화벽) 또는 nsg (네트워크 보안 그룹)를 사용 하 여 충분히 안전 하 게 보호할 수 있습니다.

* [Azure의 가상 네트워크 및 가상 머신](../virtual-machines/network-overview.md)

* [Azure 방화벽 개요](../firewall/overview.md)

* [Azure 웹 응용 프로그램 방화벽 개요](../web-application-firewall/overview.md)

* [네트워크 보안 그룹](../virtual-network/security-overview.md)

* [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)

* [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)

* [구독 관련 결정 가이드](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure Automation 및 관련 리소스의 구성을 감사 하거나 적용 합니다. 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용할 수도 있습니다.

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

* [Azure Automation에 대 한 Azure Policy 샘플 기본 제공](./policy-samples.md)

* [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

* [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Azure Automation는 현재 기본 다중 테 넌 트 runbook worker의 가상 머신 또는 OS를 노출 하지 않습니다. 이는 플랫폼에 의해 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 Azure Security Center 권장 사항 [Virtual Machines에서 보안 구성의 취약성 해결]을 사용 하 여 가상 컴퓨터에 대 한 보안 구성을 유지 합니다.

* [Azure Security Center 권장 사항을 모니터링 하는 방법](../security-center/security-center-recommendations.md)

* [Azure Security Center 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Resource Manager 템플릿 및 Azure Policy를 사용 하 여 Azure Automation와 연결 된 Azure 리소스를 안전 하 게 구성 합니다. Azure Resource Manager 템플릿은 Azure 리소스를 배포 하는 데 사용 되는 JSON 기반 파일 이며, 사용자 지정 템플릿은 코드 리포지토리에서 안전 하 게 저장 하 고 유지 관리 해야 합니다. 원본 제어 통합 기능을 사용 하 여 소스 제어 리포지토리의 스크립트를 사용 하 여 자동화 계정의 runbook을 최신 상태로 유지 합니다. Azure 리소스 전체에서 보안 설정을 적용 하려면 [거부] 및 [없는 경우 배포] Azure Policy 사용 합니다.

* [원본 제어 통합 사용](./source-control-integration.md)

* [Azure Resource Manager 템플릿 만들기에 대 한 정보](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

* [Azure Resource Manager 템플릿을 사용 하 여 Automation 계정 배포](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure Automation에 대 한 Azure Policy 샘플 기본 제공](./policy-samples.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: Azure Automation는 현재 기본 다중 테 넌 트 runbook worker의 가상 머신 또는 OS를 노출 하지 않으며이는 플랫폼에서 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 배포에 대 한 Azure 가상 머신에 대 한 보안 구성을 유지 관리할 수 있는 몇 가지 옵션이 있습니다.

- Azure Resource Manager 템플릿: Azure Portal에서 VM을 배포 하는 데 사용 되는 JSON 기반 파일이 며, 사용자 지정 템플릿을 유지 관리 해야 합니다. Microsoft는 기본 템플릿에 대 한 유지 관리를 수행 합니다.
- 사용자 지정 VHD (가상 하드 디스크): 다른 방법을 통해 관리할 수 없는 복잡 한 환경을 처리할 때와 같이 사용자 지정 VHD 파일을 사용 해야 하는 경우도 있습니다.
- Azure Automation 상태 구성: 기본 OS가 배포 되 면이를 사용 하 여 보다 세부적인 설정을 제어 하 고 자동화 프레임 워크를 통해 적용할 수 있습니다.

대부분의 시나리오에서 Azure Automation 상태 구성과 결합 된 Microsoft 기본 VM 템플릿은 보안 요구 사항을 충족 하 고 유지 관리 하는 데 도움이 될 수 있습니다.

* [VM 템플릿을 다운로드 하는 방법에 대 한 정보](../virtual-machines/windows/download-template.md)

* [ARM 템플릿 만들기에 대 한 정보](../virtual-machines/windows/ps-template.md)

* [Azure에 사용자 지정 VM VHD를 업로드 하는 방법](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure devops를 사용 하 여 사용자 지정 azure 정책, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 Azure Active Directory에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다. 원본 제어 통합 기능을 사용 하 여 소스 제어 리포지토리의 스크립트를 사용 하 여 자동화 계정의 runbook을 최신 상태로 유지 합니다.

* [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

* [원본 제어 통합 사용](./source-control-integration.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: Azure Automation는 현재 기본 다중 테 넌 트 runbook worker의 가상 머신 또는 OS를 노출 하지 않으며이는 플랫폼에서 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 권한 있는 사용자만 이미지에 액세스할 수 있도록 저장소 계정에 있는 사용자 지정 OS 이미지에 대 한 액세스를 적절 하 게 제한 해야 합니다.

* [Azure RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Azure RBAC를 구성 하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. Azure Policy 별칭을 사용 하 여 Azure 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure Automation에 대 한 Azure Policy 샘플 기본 제공](./policy-samples.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: Azure Automation는 현재 기본 다중 테 넌 트 runbook worker의 가상 머신 또는 OS를 노출 하지 않으며이는 플랫폼에서 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 클라우드 또는 온-프레미스 데이터 센터에서 DSC (Desired State Configuration) 노드에 대 한 구성 관리 서비스인 Runbook worker에서 Azure Automation 상태 구성을 사용 합니다. 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다.

* [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](./automation-dsc-onboarding.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Policy을 사용 하 여 Azure 리소스 구성을 경고 및 감사 하 고, 정책을 사용 하 여 개인 끝점으로 구성 되지 않은 특정 리소스를 검색할 수 있습니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 Azure Security Center를 활용 하 여 Azure 가상 머신에 대 한 기준 검색을 수행 합니다. 자동 구성에 대 한 추가 방법에는 Azure Automation 상태 구성이 포함 됩니다.

* [Azure Security Center에서 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)

* [Azure Automation 상태 구성 시작하기](./automation-dsc-getting-started.md)

* [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

* [Azure Automation에 대 한 Azure Policy 샘플 기본 제공](./policy-samples.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Automation 제품은 현재 기본 다중 테 넌 트 runbook worker 가상 머신 또는 OS를 노출 하지 않으며이는 플랫폼에서 처리 됩니다. Hybrid Worker가 없는 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 클라우드 또는 온-프레미스 데이터 센터에서 DSC (Desired State Configuration) 노드에 대 한 구성 관리 서비스인 Runbook worker에 대 한 Azure Automation 상태 구성을 사용 합니다. 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다.

* [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](./automation-dsc-onboarding.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다.

* [Automation 계정에 대해 고객 관리형 키 사용](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [관리 id로 runbook 인증 사용](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [Key Vault를 만드는 방법](../key-vault/secrets/quick-create-portal.md)

* [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](../key-vault/general/managed-identity.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 Id를 azure 서비스에 제공 합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

* [관리 Id를 구성 하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Automation 계정에 대해 고객 관리형 키 사용](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리 맬웨어 방지 소프트웨어 사용

**지침**: Azure Automation 제품은 현재 기본 다중 테 넌 트 runbook worker 가상 머신 또는 OS를 노출 하지 않으며이는 플랫폼에서 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 Azure Windows 가상 머신에 대 한 Microsoft 맬웨어 방지 프로그램을 사용 하 여 runbook worker 리소스를 지속적으로 모니터링 하 고 방어할 수 있습니다.

* [Cloud Services 및 Virtual Machines에 대해 Microsoft 맬웨어 방지를 구성 하는 방법](../security/fundamentals/antimalware.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: 해당 사항 없음 서비스로 Azure Automation 파일을 저장 하지 않습니다. Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Automation)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 콘텐츠에서 실행 되지 않습니다.

* [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 이해](../security/fundamentals/antimalware.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: Azure Automation는 현재 기본 다중 테 넌 트 runbook worker의 가상 머신 또는 OS를 노출 하지 않으며이는 플랫폼에서 처리 됩니다. Hybrid Runbook Worker 없이 기본 서비스를 사용 하는 경우에는이 컨트롤을 적용할 수 없습니다.

Hybrid Runbook Worker 기능을 사용 하는 경우 Azure 용 Microsoft 맬웨어 방지 프로그램을 사용 하 여 기본적으로 최신 서명, 플랫폼 및 엔진 업데이트를 runbook worker에 자동으로 설치 합니다. Azure Security Center "계산 앱"의 권장 사항을 따라 &amp; 모든 끝점이 최신 서명으로 최신 상태를 유지 하도록 합니다. Windows OS를 추가 보안으로 추가로 보호 하 여 Azure Security Center와 통합 되는 Microsoft Defender Advanced Threat Protection 서비스에 대 한 바이러스 또는 맬웨어 기반 공격의 위험을 제한할 수 있습니다.

* [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 프로그램을 배포 하는 방법](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Resource Manager을 사용 하 여 Azure Automation 계정 및 관련 리소스를 배포할 수 있습니다. Azure Resource Manager은 Azure Automation 계정 및 관련 리소스를 복원 하기 위해 백업으로 사용할 수 있는 템플릿을 내보내는 기능을 제공 합니다. Azure Automation를 사용 하 여 정기적으로 Azure Resource Manager 템플릿 내보내기 API를 호출 합니다.

원본 제어 통합 기능을 사용 하 여 소스 제어 리포지토리의 스크립트를 사용 하 여 자동화 계정의 runbook을 최신 상태로 유지 합니다.

* [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)

* [Azure Automation 리소스에 대 한 Azure Resource Manager 템플릿 참조](/azure/templates/microsoft.automation/allversions)

* [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](./quickstart-create-automation-account-template.md)

* [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

* [리소스 그룹-템플릿 내보내기](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure Automation 소개](./automation-intro.md)

* [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Automation 계정에 대해 고객 관리형 키 사용](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [원본 제어 통합 사용](./source-control-integration.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure Resource Manager을 사용 하 여 Azure Automation 계정 및 관련 리소스를 배포할 수 있습니다. Azure Resource Manager은 Azure Automation 계정 및 관련 리소스를 복원 하기 위해 백업으로 사용할 수 있는 템플릿을 내보내는 기능을 제공 합니다. Azure Automation를 사용 하 여 정기적으로 Azure Resource Manager 템플릿 내보내기 API를 호출 합니다. Azure Key Vault 내에서 고객이 관리 하는 키를 백업 합니다. Azure Portal 또는 PowerShell을 사용 하 여 runbook을 스크립트 파일로 내보낼 수 있습니다.

* [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)

* [Azure Automation 리소스에 대 한 Azure Resource Manager 템플릿 참조](/azure/templates/microsoft.automation/allversions)

* [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](./quickstart-create-automation-account-template.md)

* [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

* [리소스 그룹-템플릿 내보내기](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure Automation 소개](./automation-intro.md)

* [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Automation 계정에 대해 고객 관리형 키 사용](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Automation 계정에 대 한 Azure 데이터 백업](./automation-managing-data.md#data-backup)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 필요한 경우 격리 된 구독에 정기적으로 Azure Resource Manager 템플릿 배포를 정기적으로 수행할 수 있는지 확인 합니다. 백업 된 고객이 관리 하는 키의 복원을 테스트 합니다.

* [ARM 템플릿을 사용 하 여 리소스 배포 및 Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Automation 계정에 대해 고객 관리형 키 사용](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure devops를 사용 하 여 Azure Resource Manager 템플릿과 같은 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps에서 관리 하는 리소스를 보호 하기 위해 Azure DevOps와 통합 된 경우 Azure Active Directory에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory.

원본 제어 통합 기능을 사용 하 여 소스 제어 리포지토리의 스크립트를 사용 하 여 자동화 계정의 runbook을 최신 상태로 유지 합니다.

* [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

* [원본 제어 통합 사용](./source-control-integration.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

* [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

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

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

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

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 Workflow Automation 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

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
