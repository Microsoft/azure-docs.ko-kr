---
title: Azure Monitor에서 모니터링할 내용
description: Azure Monitor에서 모니터링 하는 모든 서비스 및 기타 리소스에 대한 참조입니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: d605b852d37af3b1e2ef0868084f3c0b99c24784
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765732"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Monitor에서 모니터링 되는 항목
이 문서에서는 Azure Monitor에 의해 모니터링 되는 다양 한 응용 프로그램 및 서비스에 대해 설명 합니다. 

## <a name="insights-and-core-solutions"></a>통찰력 및 핵심 솔루션
핵심 정보 및 솔루션은 Azure Monitor의 일부로 간주 되며 Azure에 대한 지원 및 서비스 수준 계약을 따릅니다. Azure Monitor를 사용할 수 있는 모든 Azure 지역에서 지원 됩니다.

### <a name="insights"></a>인사이트

Insights는 특정 응용 프로그램 및 서비스에 대한 사용자 지정 모니터링 환경을 제공 합니다. 로그 및 메트릭을 수집 하 고 분석 합니다.

| 인사이트 | Description |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 모든 플랫폼에서 라이브 웹 응용 프로그램을 모니터링 하는 확장 가능한 APM (응용 프로그램 성능 관리) 서비스입니다. |
| [컨테이너 Azure Monitor](insights/container-insights-overview.md) | AKS (Azure Kubernetes Service)에서 호스트 되는 Azure Container Instances 또는 관리 되는 Kubernetes 클러스터에 배포 된 컨테이너 워크 로드의 성능을 모니터링 합니다. |
| [Cosmos DB에 대한 Azure Monitor (미리 보기)](insights/cosmosdb-insights-overview.md) | 통합 된 대화형 환경에서 모든 Azure Cosmos DB 리소스의 전반적인 성능, 오류, 용량 및 작업 상태에 대한 보기를 제공 합니다. |
| [네트워크에 대한 Azure Monitor (미리 보기)](insights/network-insights-overview.md) | 모든 네트워크 리소스에 대한 상태 및 메트릭의 포괄적인 보기를 제공 합니다. 고급 검색 기능을 사용 하 여 리소스 종속성을 식별 하 고 웹 사이트 이름을 검색 하기만 하면 웹 사이트를 호스트 하는 리소스를 식별 하는 등의 시나리오를 사용할 수 있습니다. |
[리소스 그룹에 대한 Azure Monitor (미리 보기)](insights/resource-group-insights.md) |  전체 리소스 그룹의 상태 및 성능에 대한 컨텍스트를 제공 하는 동시에 개별 리소스에 발생 하는 문제를 심사 하 고 진단 합니다. |
| [저장소에 대한 Azure Monitor (미리 보기)](insights/storage-insights-overview.md) | Azure Storage 서비스 성능, 용량 및 가용성에 대한 통합 보기를 제공 하 여 Azure Storage 계정에 대한 포괄적인 모니터링을 제공 합니다. |
| [VM용 Azure Monitor (미리 보기)](insights/container-insights-overview.md) | Azure VM (가상 머신) 및 가상 머신 확장 집합을 대규모로 모니터링 합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다. |

### <a name="core-solutions"></a>핵심 솔루션

솔루션은 특정 응용 프로그램이 나 서비스에 대해 사용자 지정 된 로그 쿼리 및 보기를 기반으로 합니다. 로그를 수집 및 분석 하 고, 정보를 통해 시간에 따라 더 이상 사용 되지 않습니다.

| 솔루션 | Description |
|:---|:---|
| [에이전트 상태](insights/solution-agenthealth.md) | Log Analytics 에이전트의 상태와 구성을 분석 합니다. |
| [경고 관리](platform/alert-management-solution.md) | System Center Operations Manager, Nagios 또는 Zabbix에서 수집 된 경고를 분석 합니다. |
| [서비스 맵](insights/service-map.md) | 는 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색 하 고 서비스 간 통신을 매핑합니다. |



## <a name="azure-services"></a>Azure 서비스
다음 표에서는 Azure 서비스와이 서비스에서 수집 하는 데이터를 Azure Monitor 나열 합니다. 

- 메트릭-서비스는 메트릭을 Azure Monitor 메트릭에 자동으로 수집 합니다. 
- 로그-서비스는 Azure Monitor 로그에 대한 플랫폼 로그 및 메트릭을 수집할 수 있는 진단 설정을 지원 합니다.
- 통찰력-서비스에 대한 사용자 지정 모니터링 환경을 제공 하는 서비스에 대한 통찰력을 제공 합니다.

| 서비스 | 메트릭 | 로그 | 인사이트 | 메모 |
|:---|:---|:---|:---|:---|
|Active Directory | 아닙니다. | 예 | [예](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Active Directory Domain Services | 아닙니다. | 예 | 아닙니다. |  |
|활동 로그 | 아닙니다. | 예 | 아닙니다. | |
|Advanced Threat Protection | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Advisor | 아닙니다. | 아닙니다. | 아닙니다. |  |
|AI Builder | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Analysis Services | 예 | 예 | 아닙니다. |  |
|FHIR 용 API | 아닙니다. | 아닙니다. | 아닙니다. |  |
|API Management | 예 | 예 | 아닙니다. |  |
|App Service | 예 | 예 | 아닙니다. |  |
|AppConfig | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Application Gateway | 예 | 예 | 아닙니다. |  |
|증명 서비스 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Automation | 예 | 예 | 아닙니다. |  |
|Azure Service Manager (RDFE) | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Backup | 아닙니다. | 예 | 아닙니다. |  |
|Bastion | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Batch | 예 | 예 | 아닙니다. |  |
|Batch AI | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Blockchain Service | 아닙니다. | 예 | 아닙니다. |  |
|Blueprints | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Bot Service | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Cloud Services | 예 | 예 | 아닙니다. | 에이전트가 게스트 운영 체제 및 워크플로를 모니터링 하는 데 필요 합니다.  |
|Cloud Shell | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Cognitive Services | 예 | 예 | 아닙니다. |  |
|Container Instances | 예 | 아닙니다. | 아닙니다. |  |
|Container Registry | 예 | 예 | 아닙니다. |  |
|CDN(Content Delivery Network) | 아닙니다. | 예 | 아닙니다. |  |
|Cosmos DB | 예 | 예 | [예](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Data Box | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Data Catalog Gen2 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|데이터 탐색기 | 예 | 예 | 아닙니다. |  |
|Data Factory | 예 | 예 | 아닙니다. |  |
|Data Factory v2 | 아닙니다. | 예 | 아닙니다. |  |
|데이터 공유 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|MariaDB 용 데이터베이스 | 예 | 예 | 아닙니다. |  |
|MySQL 용 데이터베이스 | 예 | 예 | 아닙니다. |  |
|PostgreSQL 용 데이터베이스 | 예 | 예 | 아닙니다. |  |
|Database Migration Service | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Databricks | 아닙니다. | 예 | 아닙니다. |  |
|DDoS Protection | 예 | 예 | 아닙니다. |  |
|DevOps | 아닙니다. | 아닙니다. | 아닙니다. |  |
|DNS | 예 | 아닙니다. | 아닙니다. |  |
|도메인 이름 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|않으면 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Dynamics 365 고객 참여 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Dynamics 365 재무 및 작업 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Event Grid | 예 | 아닙니다. | 아닙니다. |  |
|Event Hubs | 예 | 예 | 아닙니다. |  |
|Express Route | 예 | 예 | 아닙니다. |  |
|방화벽 | 예 | 예 | 아닙니다. |  |
|Front Door | 예 | 예 | 아닙니다. |  |
|Functions | 예 | 예 | 아닙니다. |  |
|HDInsight | 아닙니다. | 예 | 아닙니다. |  |
|HPC 캐시 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Information Protection | 아닙니다. | 예 | 아닙니다. |  |
|Intune | 아닙니다. | 예 | 아닙니다. |  |
|IoT Central | 아닙니다. | 아닙니다. | 아닙니다. |  |
|IoT Hub | 예 | 예 | 아닙니다. |  |
|Key Vault | 예 | 예 | 아닙니다. |  |
|AKS(Azure Kubernetes Service) | 아닙니다. | 아닙니다. | [예](insights/container-insights-overview.md)  |  |
|Load Balancer | 예 | 예 | 아닙니다. |  |
|Logic Apps | 예 | 예 | 아닙니다. |  |
|Machine Learning Service | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Managed Applications  | 아닙니다. | 아닙니다. | 아닙니다. |  |
|지도  | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Media Services | 예 | 예 | 아닙니다. |  |
|Microsoft Flow | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Microsoft에서 관리 하는 데스크톱 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Microsoft PowerApps | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Microsoft Social Engagement | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Microsoft Stream | 예 | 예 | 아닙니다. |  |
|마이그레이션 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|다중 인증(Multi-Factor Authentification) | 아닙니다. | 예 | 아닙니다. |  |
|Network Watcher | 예 | 예 | 아닙니다. |  |
|Notification Hubs | 예 | 아닙니다. | 아닙니다. |  |
|Open Datasets | 아닙니다. | 아닙니다. | 아닙니다. |  |
|정책 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Power BI | 예 | 예 | 아닙니다. |  |
|Power BI Embedded | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Private Link | 아닙니다. | 아닙니다. | 아닙니다. |  |
|프로젝트 스풀 통신 플랫폼 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Red Hat OpenShift | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Redis Cache | 예 | 예 | 아닙니다. |  |
|리소스 그룹 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Resource Manager | 아닙니다. | 아닙니다. | 아닙니다. |  |
|소매점 검색 – Bing | 아닙니다. | 아닙니다. | 아닙니다. |  |
|검색 | 예 | 예 | 아닙니다. |  |
|Service Bus | 예 | 예 | 아닙니다. |  |
|Service Fabric | 아닙니다. | 예 | 아닙니다. | 에이전트가 게스트 운영 체제 및 워크플로를 모니터링 하는 데 필요 합니다.  |
|등록 포털 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Site Recovery | 아닙니다. | 예 | 아닙니다. |  |
|스프링 클라우드 서비스 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|SQL Data Warehouse | 예 | 예 | 아닙니다. |  |
|SQL Database | 예 | 예 | 아닙니다. |  |
|SQL Server Stretch Database | 예 | 예 | 아닙니다. |  |
|스택 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Storage | 예 | 아닙니다. | [예](insights/storage-insights-overview.md) |  |
|저장소 캐시 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|저장소 동기화 서비스 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Stream Analytics | 예 | 예 | 아닙니다. |  |
|Time Series Insights | 예 | 예 | 아닙니다. |  |
|TINA | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Traffic Manager | 예 | 예 | 아닙니다. |  |
|유니버설 인쇄 | 아닙니다. | 아닙니다. | 아닙니다. |  |
|Virtual Machine Scale Sets | 아닙니다. | 예 | [예](insights/vminsights-overview.md) | 에이전트가 게스트 운영 체제 및 워크플로를 모니터링 하는 데 필요 합니다. |
|가상 머신 | 예 | 예 | [예](insights/vminsights-overview.md) | 에이전트가 게스트 운영 체제 및 워크플로를 모니터링 하는 데 필요 합니다. |
|Virtual Network | 예 | 예 | [예](insights/network-insights-overview.md) |  |
|Virtual Network-NSG 흐름 로그 | 아닙니다. | 예 | 아닙니다. |  |
|VPN Gateway | 예 | 예 | 아닙니다. |  |
|Windows Virtual Desktop | 아닙니다. | 아닙니다. | 아닙니다. |  |


## <a name="product-integrations"></a>제품 통합
다음 표의 서비스 및 솔루션은 Azure Monitor에 의해 수집 된 다른 로그 데이터를 사용 하 여 분석할 수 있도록 해당 데이터를 Log Analytics 작업 영역에 저장 합니다.

| 제품/서비스 | Description |
|:---|:---|
| [Azure Automation](/azure/automation/) | Windows 및 Linux 컴퓨터에서 운영 체제 업데이트를 관리 하 고 변경 내용을 추적 합니다. [변경 내용 추적](../automation/change-tracking.md) 및 [업데이트 관리](../automation/automation-update-management.md)를 참조 하세요. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | 문서와 전자 메일을 분류 하 고 필요에 따라 보호 합니다. [Azure Information Protection에 대한 중앙 보고를](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)참조 하세요. |
| [Azure Security Center](/azure/security-center/) | 보안 이벤트를 수집 및 분석 하 고 위협 분석을 수행 합니다. [Azure Security Center의 데이터 수집을](/azure/security-center/security-center-enable-data-collection) 참조 하세요. |
| [Azure Sentinel](/azure/sentinel/) | Office 365 및 Amazon Web Services 클라우드 내역을 비롯 한 다양 한 원본에 연결 합니다. [데이터 원본 연결](/azure/sentinel/connect-data-sources)을 참조 하세요. |
| [Key Vault 분석](insights/azure-key-vault.md) | Azure Key Vault AuditEvent 로그를 분석 합니다. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Azure Monitor로 로그를 보낼 진단 설정을 만듭니다. [Intune (미리 보기)에서 저장소, 이벤트 허브 또는 log analytics로 로그 데이터 전송](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)을 참조 하세요.  |
| 네트워크  | [네트워크 성능 모니터](insights/network-performance-monitor.md) -서비스 및 응용 프로그램 엔드포인트에 대한 네트워크 연결 및 성능을 모니터링 합니다.<br>[Azure 애플리케이션 게이트웨이](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) -Azure 애플리케이션 게이트웨이에서 로그 및 메트릭을 분석 합니다.<br>[트래픽 분석](/azure/network-watcher/traffic-analytics) -Network Watcher nsg (네트워크 보안 그룹) 흐름 로그를 분석 하 여 Azure 클라우드의 트래픽 흐름에 대한 정보를 제공 합니다. |
| [Office 365](insights/solution-office-365.md) | Office 365 환경을 모니터링 합니다. Azure 센티널을 통해 향상 된 온 보 딩이 제공 되는 업데이트 된 버전입니다. |
| [SQL Analytics](insights/azure-sql.md) | 여러 구독에서 Azure SQL database, 탄력적 풀 및 관리 되는 인스턴스의 성능을 모니터링 합니다. |
| [Surface Hub](insights/surface-hubs.md) | Surface Hub 장치의 상태와 사용 현황을 추적 합니다. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Operations Manager 에이전트에서 관리 그룹을 Azure Monitor에 연결 하 여 데이터를 수집 합니다. [Azure Monitor에 Operations Manager 연결을](platform/om-agents.md) 참조 하세요.<br> [Operations Manager 평가](insights/scom-assessment.md) 솔루션을 사용 하 여 System Center Operations Manager 관리 그룹의 위험 및 상태를 평가 합니다. |
| [Microsoft 팀 대화방](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft 팀 대화방 장치를 통합 하 고 종단 간 관리 합니다. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | 응용 프로그램을 빌드, 테스트 및 배포 하 고, 상태 및 사용 현황을 모니터링 합니다. [App Center 및 Application Insights를 사용 하 여 모바일 앱 분석 시작을](learn/mobile-center-quickstart.md)참조 하세요. |
| Windows | [Windows 업데이트 준수](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) -Windows 데스크톱 업그레이드를 평가 합니다.<br>[데스크톱 분석](https://docs.microsoft.com/configmgr/desktop-analytics/overview) -Configuration Manager와 통합 하 여 Windows 클라이언트의 업데이트 준비에 대한 더 많은 의사 결정을 내릴 수 있는 통찰력 및 인텔리전스를 제공 합니다. |



## <a name="other-solutions"></a>기타 솔루션
다른 솔루션은 다양 한 응용 프로그램 및 서비스를 모니터링 하는 데 사용할 수 있지만, 활성 개발은 중지 되었으며 일부 지역에서는 사용 하지 못할 수 있습니다. Azure Log Analytics 데이터 수집 서비스 수준 계약에 포함 됩니다.

| 솔루션 | Description |
|:---|:---|
| [Active Directory 평가](insights/ad-assessment.md) | Active Directory 환경의 위험 및 상태를 평가 합니다. |
| [Active Directory 복제 상태](insights/ad-replication-status.md) | 복제 오류에 대한 Active Directory 환경을 정기적으로 모니터링 합니다. |
| [활동 로그 분석](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | 미리 정의 된 로그 쿼리 및 뷰를 사용 하 여 활동 로그 항목을 분석 합니다. |
| [DNS 분석 (미리 보기)](insights/dns-analytics.md) | DNS 서버에서 Windows DNS 분석 및 감사 로그와 기타 관련 데이터를 수집 하 고, 분석 하 고, 상관 관계를 합니다. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 여러 배포에서 Cloud Foundry 시스템 상태 및 성능 메트릭을 수집, 확인 및 분석 합니다. |
| [컨테이너](insights/containers.md) | Docker 및 Windows 컨테이너 호스트를 보고 관리 합니다. |
| [주문형 평가](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | 온-프레미스, 하이브리드 및 클라우드 Microsoft 기술 환경의 가용성, 보안 및 성능을 평가 하 고 최적화 합니다. |
| [SQL 평가](insights/sql-assessment.md) | SQL Server 환경의 위험 및 상태를 평가 합니다.  |
| [실시간 데이터](insights/wire-data.md) | Log Analytics 에이전트를 사용 하 여 Windows 연결 및 Linux에 연결 된 컴퓨터에서 수집 된 통합 네트워크 및 성능 데이터입니다. |


## <a name="third-party-integration"></a>타사 통합

| 솔루션 | Description |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | ITSMC(IT 서비스 관리 커넥터)를 사용하면 Azure와 지원되는 ITSM(IT 서비스 관리) 제품/서비스를 연결할 수 있습니다.  |


## <a name="resources-outside-of-azure"></a>Azure 외부의 리소스
다음 표에 나열 된 방법을 사용 하 여 Azure 외부의 리소스에서 데이터를 수집할 수 Azure Monitor.

| 리소스 | 방법 |
|:---|:---|
| 애플리케이션 | Application Insights를 사용 하 여 Azure 외부에서 웹 응용 프로그램을 모니터링 합니다. [Application Insights 란?을](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)참조 하세요. |
| Virtual Machines | Log Analytics 에이전트를 사용 하 여 다른 클라우드 환경 또는 온-프레미스에 있는 가상 머신의 게스트 운영 체제에서 데이터를 수집 합니다. [Log Analytics 에이전트를 사용 하 여 로그 데이터 수집](platform/log-analytics-agent.md)을 참조 하세요. |
| REST API 클라이언트 | 별도의 Api를 사용 하 여 REST API 클라이언트의 Azure Monitor 로그 및 메트릭에 데이터를 쓸 수 있습니다. 로그에 대한 [HTTP 데이터 수집기 API를 사용 하 여 Azure Monitor에 로그 데이터 보내기](platform/data-collector-api.md) 및 메트릭에 대한 [REST API를 사용 하 여 Azure 리소스에 대한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소에 전송](platform/metrics-store-custom-rest-api.md) 을 참조 하세요. |



## <a name="next-steps"></a>다음 단계

- [정보 및 솔루션에 의해 수집 된 로그 및 메트릭을 저장 하는 Azure Monitor 데이터 플랫폼](platform/data-platform.md)에 대해 자세히 알아보세요.
- [Azure 리소스 모니터링에 대한 자습서](learn/tutorial-resource-logs.md)를 완료 합니다.
- [로그 쿼리를 작성 하 여 Azure Monitor 로그의 데이터를 분석 하는 방법에 대한 자습서](learn/tutorial-resource-logs.md)를 완료 합니다.
- 메트릭 [차트를 만들어 Azure Monitor 메트릭의 데이터를 분석 하는 방법에 대한 자습서](learn/tutorial-metrics-explorer.md)를 완료 합니다.

 
