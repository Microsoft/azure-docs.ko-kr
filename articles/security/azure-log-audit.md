---
title: Azure 로깅 및 감사 | Microsoft Docs
description: 로깅 데이터를 사용하여 애플리케이션에 대해 깊이 이해할 수 있는 방법에 대해 알아봅니다.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: 6ab6ec6a88dbba066be9ecf9919be876090cfdff
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121504"
---
# <a name="azure-logging-and-auditing"></a>Azure 로깅 및 감사

Azure는 보안 정책 및 메커니즘의 차이를 식별할 수 있도록 다양한 구성 가능한 보안 감사 및 로깅 옵션을 제공합니다. 이 문서에서는 Azure에서 호스팅되는 서비스의 보안 로그 생성, 수집 및 분석에 대해 설명합니다.

> [!Note]
> 이 문서의 특정 권장 사항으로 인해 데이터, 네트워크 또는 계산 리소스의 사용량이 증가하고, 라이선스 또는 구독 비용이 증가할 수 있습니다.

## <a name="types-of-logs-in-azure"></a>Azure의 로그 유형

클라우드 애플리케이션은 이동하는 부분이 많아 복잡합니다. 로그는 애플리케이션을 계속 유지하고 실행하는 데 도움이 되는 데이터를 제공합니다. 로그는 과거의 문제를 해결하거나 잠재적인 문제를 방지하는 데 도움이 됩니다. 또한 애플리케이션 성능 또는 유지 관리 용이성을 향상시키거나 수동 개입이 필요한 작업을 자동화할 수 있습니다.

Azure 로그는 다음과 같은 유형으로 분류됩니다.
* **제어/관리 로그**는 Azure Resource Manager의 CREATE, UPDATE 및 DELETE 작업에 대한 정보를 제공합니다. 자세한 내용은 [Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)를 참조하세요.

* **데이터 평면 로그**는 Azure 리소스 사용의 일부로 발생한 이벤트에 대한 정보를 제공합니다. 이 로그 유형의 예로 VM(가상 머신)의 Windows 이벤트 시스템, 보안 및 애플리케이션 로그와 Azure Monitor를 통해 구성된 [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)가 있습니다.

* **처리된 이벤트**는 사용자를 대신하여 처리되어 분석된 이벤트/경고에 대한 정보를 제공합니다. 이 로그 유형의 예로 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에서 구독을 처리 및 분석하고 간결한 보안 경고를 제공하는 [Azure Security Center 경고](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)가 있습니다.

다음 표에는 Azure에서 사용할 수 있는 가장 중요한 유형의 로그가 나와 있습니다.

| 로그 범주 | 로그 형식 | 사용 현황 | 통합 |
| ------------ | -------- | ------ | ----------- |
|[활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Azure Resource Manager 리소스에 대한 제어 평면 이벤트|   구독의 리소스에서 수행된 작업에 대한 인사이트를 제공합니다.|    Rest API, [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure 진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|구독에서 Azure Resource Manager 리소스 작업에 대한 빈번한 데이터|    리소스 자체에서 수행한 작업에 대한 인사이트를 제공합니다.| Azure Monitor, [스트림](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Azure AD 보고](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|로그 및 보고서 | 사용자 및 그룹 관리에 대한 사용자 로그인 활동 및 시스템 활동 정보를 보고합니다.|[그래프 API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[가상 머신 및 클라우드 서비스](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Windows 이벤트 로그 서비스 및 Linux Syslog|  가상 머신에서 시스템 데이터와 로깅 데이터를 캡처하고 사용자가 선택한 저장소 계정으로 해당 데이터를 전송합니다.|   Azure Monitor의 Windows([WAD](https://docs.microsoft.com/azure/azure-diagnostics)[Microsoft Azure Diagnostics 스토리지] 사용) 및 Linux|
|[Azure Storage 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|저장소 로깅(저장소 계정에 대한 메트릭 데이터 제공)|추적 요청에 대한 인사이트를 제공하고, 사용 추세를 분석하며, 저장소 계정과 관련된 문제를 진단합니다.|   REST API 또는 [클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[NSG(네트워크 보안 그룹) 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON 형식(규칙에 따라 아웃바운드 및 인바운드 흐름 표시)|네트워크 보안 그룹을 통해 수신 및 송신 IP 트래픽에 대한 정보를 표시합니다.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insight](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|로그, 예외 및 사용자 지정 진단|   여러 플랫폼에서 웹 개발자를 위한 APM(애플리케이션 성능 모니터링) 서비스를 제공합니다.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|데이터 처리/보안 경고|    Azure Security Center 경고, 로그 및 Azure Monitor 경고|    보안 정보 및 경고를 제공합니다.|  REST API, JSON|

### <a name="activity-logs"></a>활동 로그

[Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 활동 로그는 구독에 대한 [제어 평면 이벤트](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/)를 보고했으므로 이전에는 "감사 로그" 또는 "작업 로그"라고 했습니다. 

활동 로그는 쓰기 작업(즉 PUT, POST 또는 DELETE)에 대한 "대상, 행위자 및 수행 시간"을 확인하는 데 도움이 됩니다. 또한 운영 상태 및 기타 관련 속성도 파악할 수 있습니다. 활동 로그에는 읽기(GET) 작업은 포함되지 않습니다.

이 문서에서 PUT, POST 및 DELETE는 리소스에 대해 활동 로그에 포함된 모든 쓰기 작업을 참조합니다. 예를 들어 문제를 해결할 때 활동 로그를 사용하여 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

![활동 로그 다이어그램](./media/azure-log-audit/azure-log-audit-fig1.png)

Azure Portal, [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell cmdlet 및 [Azure Monitor REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough)를 사용하여 활동 로그에서 이벤트를 검색할 수 있습니다. 활동 로그에는 90일간의 데이터 보존 기간이 있습니다.

활동 로그 이벤트에 대한 통합 시나리오는 다음과 같습니다.

* [활동 로그 이벤트에서 트리거하는 이메일 또는 웹후크 경고를 만듭니다.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

* 타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)에서 수집할 수 있도록 [이벤트 허브로 스트림합니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs).

* [PowerBI 콘텐츠 팩](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 PowerBI에서 분석합니다.

* [보관하거나 수동으로 검사할 수 있도록 저장소 계정에 저장합니다](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). 로그 프로필을 사용하여 보존 기간(일)을 지정할 수 있습니다.

* Azure Portal에서 쿼리하고 봅니다.

* PowerShell Cmdlet, Azure CLI 또는 REST API를 통해 쿼리합니다.

* 로그 프로필을 사용 하 여 활동 로그 내보내기 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)합니다.

로그를 내보내는 저장소와 동일한 구독에 있지 않은 저장소 계정 또는 [이벤트 허브 네임스페이스](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive)를 사용할 수 있습니다. 설정을 구성하는 사용자는 두 구독 모두에 대해 적절한 [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 액세스 권한을 가지고 있어야 합니다.

### <a name="azure-diagnostics-logs"></a>Azure 진단 로그

Azure 진단 로그는 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공하는 리소스에서 내보냅니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다. 예를 들어 [Windows 이벤트 시스템 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)는 VM에 대한 진단 로그의 범주이고, [Blob, 테이블 및 큐 로그](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)는 저장소 계정에 대한 진단 로그의 범주입니다. 진단 로그는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공하는 활동 로그와 다릅니다.

![Azure 진단 로그 다이어그램](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure 진단 로그는 Azure Portal, PowerShell, Azure CLI 및 REST API와 같은 여러 구성 옵션을 제공합니다.

**통합 시나리오**

* 감사 또는 수동 검사를 위해 [저장소 계정](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs)에 저장합니다. 진단 설정을 사용하여 보존 기간(일)을 지정할 수 있습니다.

* 타사 서비스 또는 사용자 지정 분석 솔루션(예: [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/))에서 수집할 수 있도록 진단 로그를 [이벤트 허브로 스트림](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs)합니다.

* 사용 하 여 분석 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)합니다.

**지원되는 서비스, 진단 로그에 대한 스키마 및 지원되는 리소스 종류별 로그 범주**


| 서비스 | 스키마 및 설명서 | 리소스 종류 | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [부하 분산 장치 (미리 보기)에 대 한 azure Monitor 로그](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|네트워크 보안 그룹|[네트워크 보안 그룹에 대 한 azure Monitor 로그](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Application Gateway에 대한 진단 로깅](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault 로그](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[검색 트래픽 분석 설정 및 사용](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Data Lake Store에 대한 진단 로그 액세스](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|감사<br>요청|
|Azure 데이터 레이크 분석|[Data Lake Analytics에 대한 진단 로그 액세스](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|감사<br>요청|
|Azure Logic Apps|[Logic Apps B2B 사용자 지정 추적 스키마](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch 진단 로그](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Automation에 대 한 azure Monitor 로그](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Event Hubs 진단 로그](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[작업 진단 로그](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|실행<br>작성|
|Azure Service Bus|[Service Bus 진단 로그](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory 보고

Azure AD(Azure Active Directory)에는 사용자의 디렉터리에 대한 보안, 활동 및 감사 보고서가 포함됩니다. [Azure AD 감사 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)를 사용하면 사용자의 Azure AD 인스턴스에서 발생한 권한 있는 작업을 식별할 수 있습니다. 권한 있는 작업에는 권한 상승 변경(예: 역할 만들기 또는 암호 재설정), 정책 구성 변경(예: 암호 정책) 또는 디렉터리 구성 변경(예: 도메인 페더레이션 설정 변경)이 포함됩니다.

보고서는 이벤트 이름, 작업을 수행한 사용자, 변경으로 인해 영향을 받은 대상 리소스, 날짜 및 시간(UTC)에 대한 감사 레코드를 제공합니다. 사용자는 [감사 로그 보기](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)에서 설명한 대로 [Azure Portal](https://portal.azure.com/)을 통해 Azure AD에 대한 감사 이벤트 목록을 검색할 수 있습니다. 

다음 표에는 포함된 보고서가 나와 있습니다.

| 보안 보고서 | 작업 보고서 | 감사 보고서 |
| :--------------- | :--------------- | :------------ |
|알 수 없는 원본에서 로그인| 애플리케이션 사용: 요약| 디렉터리 감사 보고서|
|여러 번의 실패 후 로그인|  애플리케이션 사용: 세부||
|여러 지역에서의 로그인|    애플리케이션 대시보드||
|의심스러운 작업이 있는 IP 주소에서 로그인|   계정 프로비전 오류||
|비정상적인 로그인 작업|    개별 사용자 디바이스||
|감염 가능성이 있는 디바이스에서 로그인|   개별 사용자 활동||
|비정상적인 로그인 활동을 포함하는 사용자| 그룹 활동 보고서||
||암호 재설정 등록 활동 보고서||
||암호 재설정 활동||

이러한 보고서의 데이터는 SIEM(보안 정보 및 이벤트 관리) 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 애플리케이션에 유용할 수 있습니다. Azure AD Reporting API는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 이러한 [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started)는 다양한 프로그래밍 언어와 도구에서 호출할 수 있습니다.

Azure AD 감사 보고서의 이벤트는 180일 동안 보존됩니다.

> [!Note]
> 보고서 보존에 대한 자세한 내용은 [Azure AD 보고서 보존 정책](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)을 참조하세요.

감사 이벤트를 더 오래 유지하려면 보고 API를 사용하여 정기적으로 [감사 이벤트](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events)를 별도의 데이터 저장소로 가져옵니다.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Azure Diagnostics를 사용하는 가상 머신 로그

[Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics)는 배포된 애플리케이션에서 진단 데이터를 수집할 수 있는 Azure의 기능입니다. 여러 원본 중 하나에서 진단 확장을 사용할 수 있습니다. 현재 [Azure 클라우드 서비스 웹 및 작업자 역할](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me)이 지원되고 있습니다.

![Azure Diagnostics를 사용하는 가상 머신 로그](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>Microsoft Windows와 [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)을 실행하는 [Azure 가상 머신](/learn/paths/deploy-a-website-with-azure-virtual-machines/)

다음 중 하나를 수행하여 가상 머신에서 Azure Diagnostics를 사용하도록 설정할 수 있습니다.

* [Visual Studio를 사용하여 Azure 가상 머신 추적](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Azure 가상 머신에서 원격으로 Azure Diagnostics 설정](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [PowerShell을 사용하여 Azure 가상 머신에 진단 설정](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Azure Resource Manager를 사용하여 모니터링 및 진단을 갖춘 Windows 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>저장소 분석

[Azure 스토리지 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)은 스토리지 계정에 대한 메트릭 데이터를 기록하고 제공합니다. 이 데이터를 사용하여 요청을 추적하고 사용량 추세를 분석하며 저장소 계정에 대한 문제를 진단할 수 있습니다. 스토리지 분석 로깅은 [Azure Blob, Azure Queue 및 Azure Table Storage 서비스](https://docs.microsoft.com/azure/storage/storage-introduction)에서 사용할 수 있습니다. 스토리지 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다.

이 정보를 사용하여 개별 요청을 모니터링하고 저장소 서비스 관련 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다. 서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 스토리지 계정에 Blob 엔드포인트의 활동은 있지만 테이블 또는 큐 엔드포인트의 활동이 없는 경우 Blob Storage 서비스와 관련된 로그만 만들어집니다.

저장소 분석을 사용하려면 모니터링하려는 각 서비스에 대해 개별적으로 저장소 분석을 사용하도록 설정합니다. [Azure Portal](https://portal.azure.com/)에서 사용하도록 설정할 수 있습니다. 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. [서비스 속성 설정] 작업을 사용하여 각 서비스에 대해 개별적으로 저장소 분석을 사용하도록 설정합니다.

집계된 데이터는 Blob Storage 서비스 및 Table Storage 서비스 API를 사용하여 액세스할 수 있는 잘 알려진 Blob(로깅용) 및 테이블(메트릭)에 저장됩니다.

저장소 분석에서는 저장소 계정의 총 한도와 관계없이 저장되는 데이터의 양에 대해 20TB(테라바이트)로 제한하고 있습니다. 모든 로그는 $logs라는 컨테이너의 [블록 Blob](https://docs.microsoft.com/azure/storage/storage-analytics)에 저장됩니다. 이 컨테이너는 스토리지 계정에 대해 스토리지 분석을 사용하도록 설정하면 자동으로 만들어집니다.

> [!Note]
> * 청구 및 데이터 보존 정책에 대한 자세한 내용은 [저장소 분석 및 청구](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)를 참조하세요.
> * 스토리지 계정 제한에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](https://docs.microsoft.com/azure/storage/storage-scalability-targets)를 참조하세요.

스토리지 분석에서 로깅하는 인증된 요청과 익명 요청의 유형은 다음과 같습니다.

| 인증됨  | 익명|
| :------------- | :-------------|
| 성공한 요청 | 성공한 요청 |
|실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류) | 실패한 요청 및 성공한 요청을 포함하여 공유 액세스 서명을 사용하는 요청 |
| 실패한 요청 및 성공한 요청을 포함하여 공유 액세스 서명을 사용하는 요청 |클라이언트와 서버 모두에 대한 시간 제한 오류 |
|   분석 데이터에 대한 요청 |    304(수정되지 않음) 오류 코드로 실패한 GET 요청 |
| 로그 만들기/삭제 등 저장소 분석 자체에서 수행한 요청은 기록되지 않습니다. 기록된 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 분석 로그 형식](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)에 설명되어 있습니다. | 기타 모든 실패한 익명 요청은 기록되지 않습니다. 기록된 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 분석 로그 형식](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)에 설명되어 있습니다. |

### <a name="azure-networking-logs"></a>Azure 네트워킹 로그

Azure의 네트워킹 로깅 및 모니터링은 포괄적이며 다음 두 가지 범주를 포함합니다.

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): 시나리오 기반 모니터링이 Network Watcher 기능과 함께 제공됩니다. 이 서비스에는 패킷 캡처, 다음 홉, IP 흐름 확인, 보안 그룹 보기, NSG 흐름 로그가 포함됩니다. 시나리오 수준 모니터링에서는 개별 네트워크 리소스 모니터링과 달리 네트워크 리소스의 종단 간 보기를 제공합니다.

* [리소스 모니터링](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): 리소스 수준 모니터링은 진단 로그, 메트릭, 문제 해결 및 리소스 상태의 네 가지 기능으로 구성됩니다. 이러한 모든 기능은 네트워크 리소스 수준에서 구현됩니다.

![Azure 네트워킹 로그](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 인사이트를 얻을 수 있습니다.

### <a name="network-security-group-flow-logging"></a>네트워크 보안 그룹 흐름 로깅

[NSG 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)는 NSG를 통한 수신 및 송신 IP 트래픽에 대한 정보를 보는 데 사용할 수 있는 Network Watcher의 기능입니다. 이러한 흐름 로그는 JSON 형식으로 작성되며, 다음 항목을 보여줍니다.
* 규칙 단위 기반의 아웃바운드 및 인바운드 흐름
* 흐름이 적용되는 NIC
* 흐름에 대한 5개 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)
* 트래픽이 허용 또는 거부되었는지 여부

흐름 로그는 NSG를 대상으로 하지만 다른 로그와 동일한 방식으로 표시되지는 않습니다. 흐름 로그는 스토리지 계정에만 저장됩니다.

다른 로그에서 볼 수 있는 동일한 보존 정책이 흐름 로그에 적용됩니다. 로그에는 1-365일을 설정할 수 있는 보존 정책이 있습니다. 보존 정책을 설정하지 않으면 로그는 계속 유지됩니다.

**진단 로그**

정기적이 고 자동적인 이벤트가 네트워크 리소스에서 생성 및 storage 계정에서 로그 및 이벤트 허브 또는 Azure Monitor 로그로 전송 됩니다. 로그는 리소스 상태에 대한 인사이트를 제공하며, Power BI 및 Azure Monitor 로그와 같은 도구에서 볼 수 있습니다. 진단 로그를 보는 방법에 알아보려면 참조 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)합니다.

![진단 로그](./media/azure-log-audit/azure-log-audit-fig5.png)

진단 로그는 [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), 경로 및 [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)에서 사용할 수 있습니다.

Network Watcher는 진단 로그 보기를 제공합니다. 이 보기에는 진단 로깅을 지원하는 모든 네트워킹 리소스가 포함됩니다. 이 보기에서 네트워킹 리소스를 빠르고 편리하게 사용하거나 사용하지 않도록 설정할 수 있습니다.


앞에서 언급한 로깅 기능 외에도 Network Watcher에는 현재 다음과 같은 기능이 있습니다.
- [토폴로지](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): 리소스 그룹의 네트워크 리소스 간 다양한 연결 및 상호 연결을 보여주는 네트워크 수준 보기를 제공합니다.

- [변수 패킷 캡처](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): 가상 머신의 내부 및 외부 패킷 데이터를 캡처합니다. 고급 필터링 옵션과 미세 조정 컨트롤(예: 시간 제한 및 크기 제한 설정)은 다양성을 제공합니다. 패킷 데이터는 Blob 저장소 또는 로컬 디스크에 *.cap* 파일 형식으로 저장할 수 있습니다.

- [IP 흐름 확인](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): 흐름 정보의 5가지 튜플 패킷 매개 변수(즉 대상 IP, 원본 IP, 대상 포트, 원본 포트 및 프로토콜)에 따라 패킷이 허용되거나 거부되는지 여부를 확인합니다. 보안 그룹에서 패킷을 거부하면 해당 패킷을 거부한 규칙과 그룹이 반환됩니다.

- [다음 홉](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Azure 네트워크 패브릭에서 라우팅되는 패킷의 다음 홉을 결정하여 잘못 구성된 사용자 정의 경로를 진단할 수 있도록 합니다.

- [보안 그룹 보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): VM에 적용되는 효과적이고 실용적인 보안 규칙을 가져옵니다.

- [Virtual Network 게이트웨이 및 연결 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): 가상 네트워크 게이트웨이 및 연결 문제를 해결하는 데 도움이 됩니다.

- [네트워크 구독 제한](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): 제한 대비 네트워크 리소스 사용량을 볼 수 있습니다.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM 서비스입니다. 라이브 웹 애플리케이션을 모니터링하는 데 사용합니다. 성능 이상을 자동으로 검색합니다. 사용자가 문제를 진단하고 앱을 사용하여 실제로 수행할 작업을 이해할 수 있도록 돕는 강력한 분석 도구를 포함합니다.

Application Insights는 성능 및 유용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

온-프레미스 또는 클라우드에서 호스팅되는지 여부에 관계없이 .NET, Node.js 및 Java EE를 포함하여 다양한 플랫폼의 애플리케이션에서 작동합니다. DevOps 프로세스와 통합되며, 다양한 개발 도구와의 연결 지점이 있습니다.

![Application Insights 다이어그램](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights는 애플리케이션 팀에서 앱의 작동 방식과 사용 방식을 이해하는 데 도움을 주기 위해 고안되었습니다. 다음 사항을 모니터링합니다.

* **요청 속도, 응답 시간 및 실패율**: 하루 중 어떤 시간에, 어떤 사용자 위치에서, 어떤 페이지가 가장 인기 있는지 확인합니다. 어떤 페이지가 가장 성능이 우수한지 확인합니다. 더 많은 요청이 있을 때 응답 시간과 실패율이 높으면 리소스 문제가 있을 수 있습니다.

* **종속성 비율, 응답 시간 및 실패율**: 외부 서비스로 인해 속도가 느려지는지 확인합니다.

* **예외**: 집계된 통계를 분석하거나 특정 인스턴스를 선택하여 스택 추적 및 관련 요청을 자세히 분석합니다. 서버 및 브라우저 예외가 전부 보고됩니다.

* **페이지 보기 및 로드 성능**: 사용자의 브라우저에서 보고서를 가져옵니다.

* **AJAX 호출**: 웹 페이지 속도, 응답 시간 및 실패율을 가져옵니다.

* **사용자 및 세션 수**.

* **성능 카운터**: Windows 또는 Linux 서버 머신에서 CPU, 메모리 및 네트워크 사용량과 같은 데이터를 가져옵니다.

* **호스트 진단**: Docker 또는 Azure에서 데이터를 가져옵니다.

* **진단 추적 로그**: 추적 이벤트와 요청의 상관 관계를 지정할 수 있도록 앱에서 데이터를 가져옵니다.

* **사용자 지정 이벤트 및 메트릭**: 판매된 품목 또는 게임 승리와 같은 비즈니스 이벤트를 추적하기 위해 클라이언트 또는 서버 코드에 직접 작성하는 데이터를 가져옵니다.

다음 표에서는 통합 시나리오를 나열하고 설명합니다.

| 통합 시나리오 | 설명 |
| --------------------- | :---------- |
|[애플리케이션 맵](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|주요 메트릭 및 경고가 포함된 앱의 구성 요소입니다.|
|[인스턴스 데이터에 대한 진단 검색](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| 요청, 예외, 종속성 호출, 로그 추적 및 페이지 보기와 같은 이벤트를 검색하고 필터링할 수 있습니다.|
|[집계된 데이터에 대한 메트릭 탐색기](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|요청, 오류 및 예외의 비율과 응답 시간, 페이지 로드 시간과 같은 집계된 데이터를 탐색, 필터링 및 분할할 수 있습니다.|
|[대시보드](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|여러 리소스의 데이터를 매시업한 후 다른 사용자와 공유할 수 있습니다. 다중 구성 요소 애플리케이션에서 사용하거나 단체방에 연속으로 표시하는 데 유용합니다.|
|[라이브 메트릭 스트림](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|새 빌드를 배포할 때 이러한 실시간에 가까운 성능 표시기를 확인하여 모든 항목이 예상대로 작동하는지 알 수 있습니다.|
|[분석](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|이 강력한 쿼리 언어를 사용하여 앱의 성능 및 사용 현황에 대한 까다로운 질문에 답변할 수 있습니다.|
|[자동 및 수동 경고](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|자동 경고는 응용 프로그램의 일반적인 원격 분석 패턴에 맞게 조정되고, 일반적인 패턴을 벗어나는 항목이 있으면 트리거됩니다. 특정 수준의 사용자 지정 또는 표준 메트릭에 대해 경고를 설정할 수도 있습니다.|
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|코드의 성능 데이터를 봅니다. 스택 추적의 코드로 이동하세요.|
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|사용 현황 메트릭을 다른 비즈니스 인텔리전스와 통합합니다.|
|[REST API](https://dev.applicationinsights.io/)|메트릭 및 원시 데이터에 대한 쿼리를 실행하는 코드를 작성합니다.|
|[연속 내보내기](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|원시 데이터가 도착하는 즉시 대량으로 저장소에 내보냅니다.|

### <a name="azure-security-center-alerts"></a>Azure Security Center 경고

Azure Security Center 위협 탐지는 Azure 리소스, 네트워크 및 연결된 파트너 솔루션에서 보안 정보를 자동으로 수집하여 작동합니다. 위협을 식별하도록 종종 여러 소스의 정보를 상호 연결하는 이 정보를 분석합니다. 보안 경고는 위협을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에서 우선 순위가 지정됩니다. 자세한 내용은 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 참조하세요.

![Azure Security Center 다이어그램](./media/azure-log-audit/azure-log-audit-fig7.png)

보안 센터는 서명 기반 방식을 뛰어 넘는 고급 보안 분석을 사용합니다. 큰 데이터 및 [기계 학습](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) 기술의 혁신을 적용하여 전체 클라우드 패브릭에서 이벤트를 평가합니다. 이러한 방식으로 수동 방법을 사용하고 공격의 진화를 예측하여 식별할 수 없는 위협을 탐지합니다. 이러한 보안 분석은 다음과 같습니다.

* **통합된 위협 인텔리전스**: Microsoft 제품과 서비스, Microsoft DCU(Digital Crimes Unit), MSRC(Microsoft 보안 대응 센터) 및 외부 피드에서 글로벌 위협 인텔리전스를 적용하여 알려진 악성 행위자를 찾습니다.

* **동작 분석**: 알려진 패턴을 적용하여 악의적인 동작을 검색합니다.

* **변칙 탐지**: 통계적 프로파일링을 사용하여 기록 초기 계획을 작성합니다. 잠재적 공격 벡터를 준수하는 설정된 기준에서 편차에 대해 경고합니다.

많은 보안 운영 및 사고 대응 팀에서는 보안 경고를 심사하고 조사하기 위한 시작 지점으로서 SIEM 솔루션을 사용합니다. Azure 로그 통합을 사용 하 여 Security Center 경고 및 Azure 진단 및 감사 로그, Azure Monitor 로그 또는 SIEM 솔루션과 거의 실시간으로 수집한 가상 머신 보안 이벤트를 동기화 할 수 있습니다.

## <a name="azure-monitor-logs"></a>Azure Monitor 로그

Azure Monitor 로그는 생성 되는 클라우드 리소스에서 온-프레미스 환경의 데이터 수집 및 분석 하도록 도와주는 Azure의 서비스입니다. 통합 검색 및 사용자 지정 대시보드를 사용하여 실제 위치에 관계없이 모든 워크로드 및 서버에서 수백만 개의 레코드를 쉽게 분석함으로써 실시간 인사이트를 제공합니다.

![Azure Monitor 로그 다이어그램](./media/azure-log-audit/azure-log-audit-fig8.png)

Azure Monitor의 중앙 로그를 Azure에 호스트 된 Log Analytics 작업 영역에서은입니다. Azure Monitor 로그 데이터 원본을 구성 하 고 구독에 솔루션을 추가 하 여 연결 된 원본에서 작업 영역에서 데이터를 수집 합니다. 데이터 원본과 솔루션은 각각 서로 다른 레코드 종류를 만들며, 각 레코드 종류에는 고유한 속성 집합이 있습니다. 그러나 원본과 솔루션은 여전히 작업 영역에 대한 쿼리에서 함께 분석될 수 있습니다. 이 기능을 사용하면 동일한 도구와 방법을 사용하여 다양한 원본에서 수집한 다양한 데이터로 작업할 수 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

연결 된 원본은 컴퓨터 및 Azure Monitor 로그에서 수집 되는 데이터를 생성 하는 다른 리소스입니다. 원본에는 직접 연결된 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) 및 [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) 컴퓨터에 설치된 에이전트 또는 [연결된 System Center Operations Manager 관리 그룹](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)의 에이전트가 포함될 수 있습니다. Azure Monitor 로그에서 데이터를 수집할 수도 있습니다는 [Azure storage 계정](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)합니다.

[데이터 원본](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources)은 연결된 원본 각각에서 수집되는 다양한 종류의 데이터입니다. 원본에는 [IIS 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) 및 [사용자 지정 텍스트 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs)와 같은 원본뿐만 아니라 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) 및 Linux 에이전트의 이벤트와 [성능 데이터](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)도 포함됩니다. 수집할 각 데이터 원본을 구성하면 각 연결된 원본에 구성이 자동으로 전달됩니다.

[Azure 서비스에 대한 로그와 메트릭을 수집](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)하는 네 가지 방법은 다음과 같습니다.

* Azure Monitor 로그에 직접 azure 진단 (**진단** 표에)

* Azure Monitor를 Azure storage에 azure 진단 로그 (**저장소** 표에)

* Azure 서비스용 커넥터(다음 표의 **커넥터**)

* 수집 하 고 그런 다음 Azure Monitor 로그 (다음 테이블에 나열 되지 않은 서비스에 대 한 빈 셀)에 데이터를 게시 하는 스크립트

| 서비스 | 리소스 종류 | 로그 | 메트릭 | 해결 방법 |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  진단|진단|    [Azure 애플리케이션](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)[Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     커넥터|  커넥터|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [커넥터(미리 보기)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure Automation 계정| Microsoft.Automation/<br>AutomationAccounts|    진단||       [자세한 정보](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Azure Batch 계정|  Microsoft.Batch/<br>batchAccounts|  진단|    진단||
|클래식 Cloud Services||       Storage||       [자세한 정보](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>계정|       진단|||
|Azure 데이터 레이크 분석| Microsoft.DataLakeAnalytics/<br>계정|   진단|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>계정|   진단|||
|Azure Event Hub 네임스페이스| Microsoft.EventHub/<br>namespaces|  진단|    진단||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     진단||
|Azure Key Vault|   Microsoft.KeyVault/<br>vaults|  진단  || [Key Vault 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    진단|||
|Azure Logic Apps|  Microsoft.Logic/<br>workflows|  진단|    진단||
||Microsoft.Logic/<br>integrationAccounts||||
|네트워크 보안 그룹|   Microsoft.Network/<br>networksecuritygroups|진단||   [Azure 네트워크 보안 그룹 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|복구 자격 증명|   Microsoft.RecoveryServices/<br>vaults|||[Azure Recovery Services 분석(미리 보기)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Search 서비스|   Microsoft.Search/<br>searchServices|    진단|    진단||
|Service Bus 네임스페이스| Microsoft.ServiceBus/<br>namespaces|    진단|진단|    [Service Bus 분석(미리 보기)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric 분석(미리 보기)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL(v12)| Microsoft.Sql/<br>servers/<br>데이터베이스||       진단||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Storage|||         스크립트| [Azure Storage 분석(미리 보기)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>virtualMachines|  내선 번호|  내선 번호||
||||진단||
|가상 머신 크기 집합|    Microsoft.Compute/<br>virtualMachines    ||진단||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|웹 서버 팜|Microsoft.Web/<br>serverfarms||   진단
|Websites|  Microsoft.Web/<br>sites ||      진단|    [자세한 정보](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots||||


## <a name="log-integration-with-on-premises-siem-systems"></a>온-프레미스 SIEM 시스템과 Log Integration의 통합

Azure Log Integration을 사용하면 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템과 통합할 수 있습니다. AzLog 다운로드는 2018년 6월 27일부로 사용할 수 없습니다. 향후 작업 진행 방향에 대한 지침은 게시물 [Azure Monitor를 사용하여 SIEM 도구와 통합](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)을 검토하세요.

![Log Integration 다이어그램](./media/azure-log-audit/azure-log-audit-fig9.png)

Log Integration은 Windows 가상 머신, Azure 활동 로그, Azure Security Center 경고 및 Azure 리소스 공급자 로그에서 Azure 진단을 수집합니다. 이 통합은 온 프레미스 또는 클라우드 중 어디에 있든 모든 자산에 대한 통합 대시보드를 제공하므로 보안 이벤트에 대한 집계, 상관 관계 지정, 분석 및 경고를 수행할 수 있습니다.

Log Integration은 현재 Windows 가상 머신의 Azure 활동 로그 및 Windows 이벤트 로그를 Azure 구독, Azure Security Center 경고, Azure 진단 로그 및 Azure AD 감사 로그와 통합할 수 있도록 지원합니다.

| 로그 형식 | Azure Monitor 로그 지원 JSON (Splunk, ArcSight, 및 IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Azure AD 감사 로그|   예|
|활동 로그| 예|
|Security Center 경고 |예|
|진단 로그(리소스 로그)|  예|
|VM 로그|   예(JSON이 아니라 전달된 이벤트를 통해)|

[Azure Log Integration 시작](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): 이 자습서에서는 Azure Log Integration을 설치하고, Azure 스토리지, Azure 활동 로그, Azure Security Center 경고의 로그 및 Azure AD 감사 로그를 통합하는 방법을 안내합니다.

SIEM에 대한 통합 시나리오는 다음과 같습니다.

* [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure Log Integration을 구성하는 방법을 보여 줍니다.

* [Azure Log Integration FAQ](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): 이 문서는 Azure 로그 통합에 대한 질문에 답변합니다.

* [Security Center 경고와 Azure Log Integration의 통합](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Azure Monitor 로그 또는 SIEM 솔루션과 Azure 감사 로그 및이 문서에서는 Security Center 경고, Azure 진단 로그에서 수집 하는 가상 머신 보안 이벤트를 동기화 하는 방법을 설명 합니다.

## <a name="next-steps"></a>다음 단계

- [감사 및 로깅](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): 가시성을 유지하고 시기 적절한 보안 경고에 빠르게 대응하여 데이터를 보호합니다.

- [Azure 내에서 보안 로깅 및 감사 로그 수집](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Azure 인스턴스에서 올바른 보안 및 감사 로그를 수집하도록 이러한 설정을 적용합니다.

- [사이트 모음에 대한 감사 설정 구성](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): 사이트 모음 관리자인 경우 개별 사용자의 작업 기록과 특정 날짜 범위 동안 수행된 작업 기록을 검색할 수 있습니다. 

- [Office 365 보안 및 준수 센터에서 감사 로그 검색](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Office 365 보안 및 준수 센터를 사용하여 Office 365 조직에서 통합 감사 로그를 검색하고 사용자와 관리자의 활동을 확인합니다.


