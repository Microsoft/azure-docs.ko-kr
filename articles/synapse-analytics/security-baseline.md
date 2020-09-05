---
title: Synapse Analytics에 대 한 Azure 보안 기준
description: Synapse Analytics 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bbec83c655d568b43b319f30a96da0a8fc1effe8
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230656"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Synapse Analytics에 대 한 Azure 보안 기준

Synapse Analytics에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 개인 링크를 통해 가상 네트워크에 대 한 Azure SQL Server를 보호 합니다. Azure 개인 링크를 사용 하면 가상 네트워크의 개인 끝점을 통해 Azure PaaS 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 사이의 트래픽은 Microsoft 백본 네트워크를 통해 이동합니다.

또는 Synapse SQL 풀에 연결 하는 경우 네트워크 보안 그룹을 사용 하 여 SQL database에 대 한 나가는 연결의 범위를 좁힙니다. Azure 서비스 허용을 OFF로 설정 하 여 공용 끝점을 통해 SQL database에 대 한 모든 Azure 서비스 트래픽을 사용 하지 않도록 설정 합니다. 방화벽 규칙에는 공용 IP 주소가 허용 되지 않아야 합니다.

* [Azure 개인 링크 이해](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Azure Synapse SQL에 대 한 개인 링크 이해](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Virtual Network를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: AZURE Synapse SQL 풀에 연결 하는 경우 nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 했으면 트래픽 감사에 대 한 Azure Storage 계정에 로그를 보냅니다.

또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 사항 없음 이 권장 사항은 Azure 앱 서비스 또는 웹 응용 프로그램을 호스트 하는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: AZURE Synapse SQL에 대해 ATP (Advanced Threat Protection)를 사용 합니다. ATP는 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 하 고, "잠재적인 SQL 삽입" 및 "비정상적인 위치에서 액세스"와 같은 다양 한 경고를 트리거할 수 있습니다. ATP는 광고 (Advanced data security) 제품의 일부 이며 중앙 SQL ADS 포털을 통해 액세스 하 고 관리할 수 있습니다.

분산 된 서비스 거부 공격 으로부터 보호 하기 위해 Azure Synapse SQL과 연결 된 가상 네트워크에서 DDoS Protection 표준을 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

* [Azure Synapse SQL에 대 한 ATP 이해](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Azure SQL Database에 대 한 고급 데이터 보안을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [광고 개요](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [DDoS 보호를 구성 하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Security Center 통합 위협 인텔리전스 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: AZURE Synapse SQL 풀에 연결 하는 경우 nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 했으면 트래픽 감사에 대 한 Azure Storage 계정에 로그를 보냅니다. 또한 흐름 로그를 Log Analytics 작업 영역으로 전송 하거나 Event Hubs로 스트리밍할 수 있습니다. 비정상적인 활동을 조사 하는 데 필요한 경우 Network Watcher 패킷 캡처를 사용 하도록 설정 합니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Network Watcher를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: AZURE Synapse SQL에 대해 ATP (Advanced Threat Protection)를 사용 합니다. ATP는 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 하 고, "잠재적인 SQL 삽입" 및 "비정상적인 위치에서 액세스"와 같은 다양 한 경고를 트리거할 수 있습니다. ATP는 광고 (Advanced data security) 제품의 일부 이며 중앙 SQL ADS 포털을 통해 액세스 하 고 관리할 수 있습니다. 또한 ATP는 경고를 Azure Security Center와 통합 합니다.

* [Azure Synapse SQL에 대 한 ATP 이해](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 사항 없음 이 권장 사항은 Azure 앱 서비스 또는 웹 응용 프로그램을 호스트 하는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

Azure Synapse SQL 풀에 서비스 끝점을 사용 하는 경우 Azure SQL database 공용 IP 주소에 대 한 아웃 바운드가 필요 합니다. 연결을 허용 하려면 Ip를 Azure SQL Database 하기 위해 NSGs (네트워크 보안 그룹)를 열어야 합니다. Azure SQL Database에 대 한 NSG 서비스 태그를 사용 하 여이 작업을 수행할 수 있습니다.

* [Azure SQL Database에 대 한 서비스 끝점을 사용 하 여 서비스 태그 이해](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

* [서비스 태그 이해 및 사용](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy을 사용 하 여 SQL 풀과 관련 된 리소스에 대 한 네트워크 보안 구성을 정의 하 고 구현 합니다. "Microsoft .Sql" 네임 스페이스를 사용 하 여 사용자 지정 정책 정의를 정의 하거나 Azure SQL 데이터베이스/서버 네트워크 보호를 위해 설계 된 기본 제공 정책 정의를 사용할 수 있습니다. Azure SQL Database 서버에 대해 적용 가능한 기본 제공 네트워크 보안 정책의 예는 "SQL Server 가상 네트워크 서비스 끝점을 사용 해야 합니다."입니다.

Azure 청사진을 사용 하 여 azure 리소스 관리 템플릿, azure RBAC (역할 기반 액세스 제어) 및 정책과 같은 주요 환경 아티팩트를 단일 청사진 정의로 패키지화 하 여 대규모 Azure 배포를 간소화 합니다. Blueprint를 새로운 구독 및 환경에 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: nsg (네트워크 보안 그룹) 및 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 AZURE Synapse SQL 풀과 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft는 Azure 리소스에 대 한 시간 소스를 유지 관리 합니다. 계산 배포에 대 한 시간 동기화를 업데이트할 수 있습니다.

* [Azure 계산 리소스에 대 한 시간 동기화를 구성 하는 방법](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: azure Synapse를 호스트 하는 azure의 기본 서버 정책 또는 특정 데이터베이스에 대 한 감사 정책을 정의할 수 있습니다. 서버에 있는 모든 기존 데이터베이스 및 새로 만든 데이터베이스에 서버 정책이 적용됩니다.

서버 감사를 사용 하는 경우에는 항상 데이터베이스에 적용 됩니다. 데이터베이스 감사 설정에 관계없이 데이터베이스를 감사합니다.

감사를 사용 하도록 설정 하면 Azure Storage 계정, Log Analytics 작업 영역 또는 Event Hubs에서 감사 로그에 기록할 수 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [Azure SQL 리소스에 대 한 감사를 설정 하는 방법](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Synapse sql 풀에 대해 Azure sql server 수준에서 감사를 사용 하도록 설정 하 고 감사 로그 (Azure Storage, Log Analytics 또는 Event Hubs)의 저장소 위치를 선택 합니다.

데이터베이스 또는 서버 수준 모두에서 감사를 사용 하도록 설정할 수 있으며, 특정 데이터베이스에 대 한 별도의 데이터 싱크 또는 보존을 구성 해야 하는 경우를 제외 하 고는 서버 수준 에서만 사용 하도록 제안 하는 것이 좋습니다.

* [Azure SQL Database 감사를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)

* [서버에 대 한 감사를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

* [서버 수준 및 데이터베이스 수준 감사 정책의 차이점](https://docs.microsoft.com/azure/sql-database/sql-database-auditing#server-vs-database-level)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 사항 없음 이 벤치 마크는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: 저장소 계정, Log Analytics 작업 영역 또는 event Hubs에서 Synapse SQL 풀과 관련 된 로그를 저장 하는 경우 조직의 규정 준수 규정에 따라 로그 보존 기간을 설정 합니다.

* [Azure Blob Storage 수명 주기 관리](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

* [Log Analytics 작업 영역에서 로그 보존 매개 변수를 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Event Hubs에서 스트리밍 이벤트 캡처](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. Azure Security Center와 함께 Azure SQL Database에 대 한 Advanced Threat Protection을 사용 하 여 SQL Database와 관련 된 비정상적인 활동을 경고 합니다. 또는 SQL database와 관련 된 Azure 활동 로그 항목 또는 메트릭 값을 기반으로 경고를 구성 합니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [Azure SQL Database에 대 한 고급 위협 방지 및 경고 이해](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Azure SQL Database에 대 한 고급 데이터 보안을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Azure SQL Database에 대 한 사용자 지정 경고를 구성 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Security Center와 함께 Azure SQL Database에 대해 ATP (Advanced Threat Protection)를 사용 하 여 비정상적인 활동을 모니터링 하 고 경고 합니다. ATP는 광고 (Advanced data security) 제품의 일부 이며 포털에서 중앙 SQL 광고를 통해 액세스 하 고 관리할 수 있습니다. 광고에는 중요 한 데이터를 검색 및 분류 하 고, 잠재적인 데이터베이스 취약성을 표시 및 완화 하 고, 데이터베이스에 위협을 나타낼 수 있는 비정상적인 활동을 검색 하기 위한 기능이 포함 되어 있습니다.

또는 온보드 데이터를 Azure 센티널로 설정할 수 있습니다.

* [Azure SQL Database에 대 한 고급 위협 방지 및 경고 이해](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Azure SQL Database에 대 한 고급 데이터 보안을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Azure Security Center에서 경고를 관리 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음 Synapse SQL 풀과 관련 된 리소스의 경우 맬웨어 방지 솔루션은 Microsoft에서 기본 플랫폼에 대해 관리 됩니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Synapse SQL 풀과 관련 된 리소스에서 DNS 로그가 생성 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 사항 없음 명령줄 감사는 Azure Synapse SQL에 적용 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: 사용자는 AZURE ACTIVE DIRECTORY 또는 SQL 인증으로 인증 됩니다.

처음으로 Azure SQL을 배포할 때 해당 로그인에 대 한 관리자 로그인 및 연결 된 암호를 지정 합니다. 이 관리 계정을 서버 관리자 라고 합니다. Azure Portal를 열고 서버 또는 관리 되는 인스턴스의 속성 탭으로 이동 하 여 데이터베이스에 대 한 관리자 계정을 식별할 수 있습니다. 모든 관리 권한을 사용 하 여 Azure AD 관리자 계정을 구성할 수도 있습니다 .이는 Azure Active Directory 인증을 사용 하도록 설정 하려는 경우에 필요 합니다.

관리 작업의 경우 명시적으로 할당 해야 하는 Azure 기본 제공 역할을 사용 합니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

* [SQL Database에 대 한 인증](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

* [비관리자 사용자에 대 한 계정 만들기](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

* [인증에 Azure Active Directory 계정 사용](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Azure SQL에서 기존 로그인 및 관리자 계정을 관리 하는 방법](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Azure 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure Active Directory에는 기본 암호 개념이 없습니다. Azure Synapse SQL 풀을 프로 비전 할 때 인증을 Azure Active Directory와 통합 하도록 선택 하는 것이 좋습니다. 이 인증 방법을 사용 하는 경우 사용자는 사용자 계정 이름을 전송 하 고 서비스에서 Azure Active Directory (Azure AD)에 저장 된 자격 증명 정보를 사용 하도록 요청 합니다.

* [Azure SQL을 사용 하 여 Azure Active Directory 인증을 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#active-directory-password-authentication)

* [Azure SQL의 인증 이해](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정의 사용에 대 한 정책과 절차를 만듭니다. Azure Security Center Id 및 액세스 관리를 사용 하 여 Azure Active Directory를 통해 로그인 하는 관리 계정 수를 모니터링 합니다.

데이터베이스의 관리자 계정을 식별하려면 Azure Portal을 열고 서버 또는 관리형 인스턴스의 속성 탭으로 이동합니다.

* [Azure Security Center Id 및 액세스 이해](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure SQL에서 기존 로그인 및 관리자 계정을 관리 하는 방법](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: SSO (Azure Active Directory Single Sign-On)를 사용 하십시오.

**지침**: Azure 앱 등록 (서비스 주체)을 사용 하 여 API 호출을 통해 제어 평면 (Azure Portal)에서 데이터 웨어하우스와 상호 작용 하는 데 사용할 수 있는 토큰을 검색 합니다.

* [Azure REST Api를 호출 하는 방법](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Azure AD를 사용 하 여 클라이언트 응용 프로그램 (서비스 사용자)을 등록 하는 방법](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Synapse SQL REST API 정보](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: Azure AD(Active Directory) MFA(Multi-Factor Authentication)를 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure SQL의 MFA 이해](https://docs.microsoft.com/azure/azure-sql/database/authentication-mfa-ssms-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에는 안전한 Azure 관리 워크스테이션 사용

**지침**: PAW (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 하도록 구성 된 MFA (Multi-Factor Authentication)를 사용 합니다.

* [Privileged Access Workstation에 대한 자세한 정보](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고 생성에 Azure Active Directory 보안 보고서를 사용 합니다.

Azure Security Center와 함께 Azure SQL Database에 대 한 고급 위협 방지를 사용 하 여 비정상적인 작업에서 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 하 고 경고 합니다.

SQL Server 감사를 사용하면 서버 수준 이벤트에 대한 서버 감사 사양과 데이터베이스 수준 이벤트에 대한 데이터베이스 감사 사양을 포함할 수 있는 서버 감사를 만들 수 있습니다. 감사 이벤트는 이벤트 로그 또는 감사 파일에 기록될 수 있습니다.

* [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center에서 사용자 id 및 액세스 활동을 모니터링 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Advanced Threat Protection 및 잠재적 경고 검토](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts)

* [Azure SQL의 로그인 및 사용자 계정 이해](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

* [감사 SQL Server 이해](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 포털 및 Azure 리소스 관리 액세스를 허용 합니다.

* [Azure에서 명명된 위치를 구성하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Synapse SQL 풀에서 Azure SQL Database 서버에 대 한 AD (Azure Active Directory) 관리자를 만듭니다.

* [Azure SQL을 사용 하 여 Azure AD 인증을 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Azure AD 인스턴스를 만들고 구성 하는 방법](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Active Directory 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

SQL 인증을 사용하는 경우 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다. 사용자 그룹에 적절 한 특정 사용 권한이 있는 사용자 지정 데이터베이스 역할에 하나 이상의 데이터베이스 사용자를 두어야 합니다.

* [액세스 검토를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Azure SQL의 로그인 및 사용자 계정 이해](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure SQL을 사용 하 여 AD (Azure Active Directory) 인증을 구성 하 고 Azure Active Directory 사용자 계정에 대 한 진단 설정을 사용 하도록 설정 하 여 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 내에서 원하는 경고를 구성합니다.

SQL 인증을 사용하는 경우 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다. 사용자 그룹에 적절 한 특정 사용 권한이 있는 사용자 지정 데이터베이스 역할에 하나 이상의 데이터베이스 사용자를 두어야 합니다.

* [액세스 검토를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Azure SQL Database를 사용 하 여 Azure AD 인증을 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Azure SQL의 로그인 및 사용자 계정 이해](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 하려면 Azure AD (Azure Active Directory) id 보호 및 위험 검색 기능을 사용 합니다. 또한 추가 조사를 위해 온-보드에 데이터를 수집 하 고 Azure 센티널에 데이터를 수집할 수 있습니다.

SQL 인증을 사용하는 경우 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다. 사용자 그룹에 적절 한 특정 사용 권한이 있는 사용자 지정 데이터베이스 역할에 하나 이상의 데이터베이스 사용자를 두어야 합니다.

* [Azure AD 위험 로그인을 보는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/connect-data-sources)

* [Azure SQL의 로그인 및 사용자 계정 이해](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: Microsoft가 Synapse SQL 풀의 Azure SQL Database와 관련 된 데이터에 액세스 해야 하는 지원 시나리오에서 Azure 고객 Lockbox는 데이터 액세스 요청을 검토 하 고 승인 하거나 거부할 수 있는 인터페이스를 제공 합니다.

* [고객 Lockbox 이해](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

데이터 검색 &amp; 분류는 Azure SYNAPSE SQL에 기본 제공 됩니다. 데이터베이스에서 중요 한 데이터를 검색, 분류, 레이블 지정 및 보고 하는 고급 기능을 제공 합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [데이터 검색 &amp; 분류 이해](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 리소스는 가상 네트워크/서브넷으로 구분 되며, 적절 하 게 태그가 지정 되 고, 네트워크 보안 그룹 또는 Azure 방화벽 내에서 보안이 유지 됩니다. 중요 한 데이터를 저장 하거나 처리 하는 리소스는 격리 되어야 합니다. 개인 링크 사용; 가상 네트워크 내에 Azure SQL Server를 배포 하 고 개인 링크를 사용 하 여 안전 하 게 연결 합니다.

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Azure SQL Database용 Private Link를 설정하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Synapse SQL 풀에서 중요 한 정보를 저장 하거나 처리 하는 모든 Azure SQL Database 경우 태그를 사용 하 여 데이터베이스 및 관련 리소스를 중요 한 것으로 표시 합니다. 중요 한 정보에 대 한 반출을 방지 하기 위해 Azure SQL Database 인스턴스에서 nsg (네트워크 보안 그룹) 서비스 태그와 함께 개인 링크를 구성 합니다.

또한 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse에 대 한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure SQL Database 인스턴스에서 데이터 반출을 방지 하도록 개인 링크 및 nsgs를 구성 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Azure SQL Database에 대 한 Advanced Threat Protection 이해](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 계층 보안을 사용 하 여 동작에서 데이터를 암호화 하 여 데이터를 보호 Azure SQL Database 합니다. SQL Server는 모든 연결에 대해 항상 암호화 (SSL/TLS)를 적용 합니다. 이렇게 하면 연결 문자열에서 Encrypt 또는 TrustServerCertificate의 설정에 관계 없이 모든 데이터가 클라이언트와 서버 간에 "전송 중"으로 암호화 됩니다.

* [전송 중인 Azure SQL 암호화 이해](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: AZURE Synapse SQL 데이터 검색 &amp; 분류 기능을 사용 합니다. 데이터 검색 &amp; 분류는 &amp; 데이터베이스의 중요 한 데이터를 검색, 분류, 레이블 지정 하는 Azure SQL Database에 기본 제공 되는 고급 기능을 제공 합니다.

데이터 검색 &amp; 분류는 고급 SQL 보안 기능을 위한 통합 패키지인 고급 데이터 보안 제품의 일부입니다. 데이터 검색 &amp; 분류는 중앙 SQL ADS 포털을 통해 액세스 하 고 관리할 수 있습니다.

또한 Azure Portal에서 DDM (동적 데이터 마스킹) 정책을 설정할 수 있습니다. DDM 권장 사항 엔진은 잠재적으로 중요 한 필드로 데이터베이스의 특정 필드에 플래그를 지정할 수 있습니다.

* [Azure SQL Server에 대 한 데이터 검색 및 분류를 사용 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

* [Azure Synapse SQL에 대 한 동적 데이터 마스킹 이해](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Synapse sql 풀에서 azure sql database에 대 한 액세스를 관리 합니다.

권한 부여는 사용자 계정의 데이터베이스 역할 구성원 자격 및 개체 수준 권한에 의해 제어됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다.

* [인증을 위해 Azure SQL Server를 Azure Active Directory와 통합 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)

* [Azure SQL Server에서 액세스를 제어 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)

* [Azure SQL의 권한 부여 및 인증 이해](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 사항 없음 Microsoft는 Azure Synapse SQL에 대 한 기본 인프라를 관리 하 고 고객 데이터 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: tde (투명 한 데이터 암호화)는 미사용 데이터를 암호화 하 여 악의적인 오프 라인 활동의 위협 으로부터 AZURE Synapse SQL을 보호 하는 데 도움이 됩니다. 데이터베이스, 연결된 백업 및 미사용 로그 파일 트랜잭션에 애플리케이션을 변경하지 않고 실시간으로 암호화 및 암호 해독을 수행합니다. Azure에서 TDE의 기본 설정은 DEK가 기본 제공 서버 인증서로 보호 되는 것입니다. 또는 TDE에 대 한 고객 관리 TDE (BYOK (Bring Your Own Key) 지원)를 사용할 수 있습니다. 이 시나리오에서 DEK를 암호화 하는 TDE 보호기는 고객이 관리 하는 비대칭 키로, 고객이 소유 하 고 관리 하는 Azure Key Vault (Azure의 클라우드 기반 외부 키 관리 시스템)에 저장 되 고 키 자격 증명 모음을 벗어날 수 없습니다.

* [서비스 관리 투명 한 데이터 암호화 이해](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal)

* [고객 관리 투명 한 데이터 암호화 이해](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [사용자 고유의 키를 사용 하 여 TDE를 설정 하는 방법](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 Synapse SQL 풀 및 기타 중요 하거나 관련 된 리소스의 프로덕션 인스턴스에 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

또한 Azure Portal를 사용 하 여 SQL Synapse 풀의 데이터베이스에 대 한 경고를 설정할 수 있습니다. 메트릭(예: 데이터베이스 크기 또는 CPU 사용량)이 임계값에 도달하면 경고에서 이메일을 보내거나 webhook를 호출할 수 있습니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Azure SQL Synapse에 대 한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 고급 데이터 보안을 사용 하도록 설정 하 고 Azure SQL 데이터베이스에서 취약성 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다.

* [Azure SQL 데이터베이스에서 취약성 평가를 실행 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [고급 데이터 보안을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 취약성 평가는 AZURE Synapse SQL에 기본 제공 되는 검색 서비스입니다. 서비스는 보안 취약성에 플래그를 지정 하는 규칙의 기술 자료를 채택 합니다. 잘못 된 오류, 과도 한 권한 및 보호 되지 않는 중요 데이터와 같은 모범 사례의 차이를 강조 표시 합니다. 취약점 평가는 중앙 SQL 광고 (Advanced Data Security) 포털을 통해 액세스 하 고 관리할 수 있습니다.

* [SQL ADS 포털에서 취약성 평가 검사 관리 및 내보내기](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Azure Security Center에서 제공 하는 기본 위험 등급 (보안 점수)을 사용 합니다.

데이터 검색 &amp; 분류는 Azure SYNAPSE SQL에 기본 제공 됩니다. 데이터베이스에서 중요 한 데이터를 검색, 분류, 레이블 지정 및 보고 하는 고급 기능을 제공 합니다.

* [보안 점수 Azure Security Center 이해](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

* [데이터 검색 &amp; 분류 이해](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 Synapse SQL 풀과 관련 된 모든 리소스를 쿼리하고 검색 합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

클래식 Azure 리소스는 Azure 리소스 그래프를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: Synapse SQL 풀과 관련 된 승인 된 Azure 리소스의 목록을 정의 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스에서 실행 되는 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요한 Synapse SQL 풀과 관련 된 모든 리소스는 조직에 대 한 위험을 초래할 수 있습니다. 자체 가상 머신 및/또는 가상 네트워크 내에서 격리 하 고 Azure 방화벽 또는 네트워크 보안 그룹을 사용 하 여 충분히 안전 하 게 보호 해야 합니다.

* [가상 네트워크를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "Synapse" 네임 스페이스의 별칭을 Azure Policy 사용 하 여 사용자 지정 정책을 만들어 Sql 풀과 관련 된 리소스의 구성을 감사 하거나 적용 합니다. Azure 데이터베이스/서버에 대해 다음과 같은 기본 제공 정책 정의를 사용할 수도 있습니다.
- SQL 서버에 위협 탐지 배포
- SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure devops 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

* [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 해당 사항 없음 Azure Synapse SQL에 구성 가능한 보안 설정이 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Security Center를 활용 하 여 Synapse SQL 풀과 관련 된 모든 리소스에 대 한 기준 검색을 수행 합니다.

* [Azure Security Center에서 권장 사항을 수정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault에서 고객 관리 키를 사용 하는 투명한 데이터 암호화 (tde)는 Tde 보호기 라는 고객 관리 비대칭 키를 사용 하 여 자동으로 생성 된 Dek (데이터베이스 암호화 키)를 암호화할 수 있습니다. 일반적으로 투명한 데이터 암호화를 위한 BYOK(Bring Your Own Key) 지원이라고도 합니다. BYOK 시나리오에서 TDE 보호기는 고객이 소유 하 고 관리 하는 Azure Key Vault에 저장 됩니다. 또한 Azure Key Vault에서 일시 삭제를 사용 하도록 설정 해야 합니다.

* [Azure Key Vault에서 고객 관리 키를 사용 하 여 TDE를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure?tabs=azure-powershell)

* [Azure Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 되는 id를 사용 하 여 AD (Azure Active Directory)에서 자동으로 관리 되는 Id를 Azure 서비스에 제공 합니다. 관리 Id를 사용 하면 코드에 자격 증명 없이 Azure Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

* [자습서: Windows VM 시스템 할당 관리 ID를 사용하여 Azure SQL에 액세스](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)

* [관리 Id를 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리 하는 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft는 기본 플랫폼용 맬웨어 방지를 처리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지는 azure 서비스 (예: AZURE Synapse SQL)를 지 원하는 기본 호스트에서 사용 하도록 설정 됩니다. 그러나 고객 콘텐츠에서 실행 되지 않습니다.

App Service, Data Lake Storage, Blob Storage, Azure SQL Server 등의 비 계산 Azure 리소스에 업로드 되는 콘텐츠를 미리 검색 합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

* [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 이해](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft는 기본 플랫폼용 맬웨어 방지를 처리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Synapse SQL 풀의 스냅숏은 7 일 동안 사용할 수 있는 복원 지점이 생성 되는 날 내내 자동으로 수행 됩니다. 이 보존 기간은 변경할 수 없습니다. SQL 풀은 8 시간 RPO (복구 지점 목표)를 지원 합니다. 지난 7일 동안 수행된 스냅샷 중 하나에서 주 지역의 데이터 웨어하우스를 복원할 수 있습니다. 필요한 경우 스냅숏을 수동으로 트리거할 수도 있습니다.

* [Azure Synapse SQL 풀에서 백업 및 복원](/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: 데이터 웨어하우스의 스냅숏은 7 일 동안 사용할 수 있는 복원 지점이 생성 되는 날 내내 자동으로 수행 됩니다. 이 보존 기간은 변경할 수 없습니다. SQL 풀은 8 시간 RPO (복구 지점 목표)를 지원 합니다. 지난 7일 동안 수행된 스냅샷 중 하나에서 주 지역의 데이터 웨어하우스를 복원할 수 있습니다. 필요한 경우 스냅숏을 수동으로 트리거할 수도 있습니다.

고객 관리 키를 사용 하 여 데이터베이스 암호화 키를 암호화 하는 경우 키를 백업 하 고 있는지 확인 합니다.

* [Azure Synapse SQL 풀에서 백업 및 복원](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Azure Key Vault 키를 백업 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 정기적으로 복원 지점이 테스트 하 여 스냅숏이 유효한 지 확인 합니다. 복원 지점에서 기존 SQL 풀을 복원 하려면 Azure Portal 또는 PowerShell 중 하나를 사용할 수 있습니다. 백업 된 고객이 관리 하는 키의 복원을 테스트 합니다.

* [Azure Key Vault 키를 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Azure Synapse SQL 풀에서 백업 및 복원](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [기존 SQL 풀을 복원 하는 방법](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure SQL Database에서 장기 백업 보존 정책 (LTR)을 사용 하 여 단일 또는 풀링된 데이터베이스를 구성 하 여 최대 10 년 동안 별도의 Azure Blob storage 컨테이너에 데이터베이스 백업을 자동으로 유지할 수 있습니다. Azure Storage의 데이터는 256 비트 AES 암호화를 사용 하 여 투명 하 게 암호화 되 고 해독 되며, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며 FIPS 140-2 규격입니다.

기본적으로 저장소 계정의 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. Microsoft에서 관리 하는 키를 사용 하 여 데이터를 암호화 하거나 자신의 키를 사용 하 여 암호화를 관리할 수 있습니다. Key Vault를 사용 하 여 고유한 키를 관리 하려면 일시 삭제를 사용 하도록 설정 해야 합니다.

* [Azure SQL Database 장기 백업 보존 관리](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure)

* [미사용 데이터에 대한 Azure Storage 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

* [Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 직원의 역할 및 인시던트 처리/관리 단계를 정의 하는 사고 대응 계획을 작성 해야 합니다.

* [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 경고에 대 한 심각도를 할당 하 여 각 경고에 참석 하는 순서의 우선 순위를 지정할 수 있도록 합니다. 따라서 리소스가 손상 되 면 즉시 가져올 수 있습니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

* [Azure Security Center의 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

* [NIST의 게시: IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램 가이드를 참조할 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.

* [Azure Security Center 보안 연락처를 설정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 센티널로 스트리밍할 수 있습니다.

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

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: * [Microsoft Rules of Engagement에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인하세요.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [여기서 Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략 및 실행에 대한 자세한 내용을 확인할 수 있습니다.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
