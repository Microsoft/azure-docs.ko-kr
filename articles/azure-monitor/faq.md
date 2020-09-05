---
title: Azure Monitor FAQ | Microsoft Docs
description: Azure Monitor에 대한 질문과 대답입니다.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/15/2020
ms.openlocfilehash: a78e1b9cc1d9ca8a815fdb586287983020232fd1
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782943"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor 질문과 대답

이 Microsoft FAQ는 Azure Monitor에 대한 일반적인 질문과 대답 목록입니다.

## <a name="general"></a>일반

### <a name="what-is-azure-monitor"></a>Azure Monitor란?
[Azure Monitor](overview.md)는 Azure, 다른 클라우드 환경 또는 온-프레미스의 애플리케이션 및 서비스에 대한 성능 및 가용성 모니터링을 제공하는 Azure 서비스입니다. Azure Monitor는 여러 원본에서 데이터를 공통 데이터 플랫폼으로 수집하여 추세 및 비정상을 분석할 수 있습니다. Azure Monitor의 다양한 기능을 통해 애플리케이션에 영향을 줄 수 있는 중요한 상황을 빠르게 식별하고 대응할 수 있습니다.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure Monitor, Log Analytics 및 Application Insights의 차이점은 무엇인가요?
2018년 9월 Microsoft는 Azure Monitor, Log Analytics 및 Application Insights를 단일 서비스로 결합하여 애플리케이션 및 해당 구성 요소에 대한 강력한 엔드투엔드 모니터링을 제공합니다. Log Analytics 및 Application Insights의 기능은 변경되지 않았지만, 일부 기능은 새 범위를 더 잘 반영하기 위해 Azure Monitor로 다시 브랜딩되었습니다. Log Analytics의 로그 데이터 엔진 및 쿼리 언어는 이제 Azure Monitor 로그라고 합니다. [Azure Monitor 용어 업데이트](terminology.md)를 참조하세요.

### <a name="what-does-azure-monitor-cost"></a>Azure Monitor 비용은 어떻게 되나요?
메트릭 및 활동 로그 수집과 같이 자동으로 사용하도록 설정하는 Azure Monitor 기능은 무료로 제공됩니다. 로그 쿼리 및 경고와 같은 다른 기능과 관련된 비용이 있습니다. 자세한 가격 책정 정보는 [Azure Monitor 가격 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.

### <a name="how-do-i-enable-azure-monitor"></a>Azure Monitor를 사용하도록 설정하려면 어떻게 해야 하나요?
Azure Monitor는 새 Azure 구독을 만들 때 사용하도록 설정하고, [활동 로그](./platform/platform-logs-overview.md) 및 플랫폼 [메트릭](platform/data-platform-metrics.md)이 자동으로 수집됩니다. [진단 설정](platform/diagnostic-settings.md)을 만들어 Azure 리소스 작업에 대한 자세한 정보를 수집하고, [모니터링 솔루션](insights/solutions.md) 및 [인사이트](insights/insights-overview.md)를 추가하여 특정 서비스에 대해 수집된 데이터에 대한 추가 분석을 제공합니다. 

### <a name="how-do-i-access-azure-monitor"></a>Azure Monitor에 액세스하려면 어떻게 해야 하나요?
Azure Portal의 **모니터** 메뉴에서 모든 Azure Monitor 기능 및 데이터에 액세스합니다. 다른 Azure 서비스 메뉴의 **모니터링** 섹션에서는 특정 리소스로 필터링된 데이터를 사용하여 동일한 도구에 액세스할 수 있습니다. 또한 Azure Monitor 데이터는 CLI, PowerShell 및 REST API를 사용하는 다양한 시나리오에 액세스할 수 있습니다.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>온-프레미스 버전의 Azure Monitor가 있나요?
아니요. Azure Monitor는 대량의 데이터를 처리하고 저장하는 확장 가능한 클라우드 서비스이며, 온-프레미스 및 다른 클라우드에 있는 리소스를 모니터링할 수도 있습니다.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Azure Monitor에서 온-프레미스 리소스를 모니터링할 수 있나요?
예, Azure Monitor는 Azure 리소스에서 모니터링 데이터를 수집하는 것 외에도 다른 클라우드 및 온-프레미스의 가상 머신 및 애플리케이션에서 데이터를 수집할 수 있습니다. [Azure Monitor 모니터링의 데이터 원본](platform/data-sources.md)을 참조하세요.

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor는 System Center Operations Manager와 통합되나요?
기존 System Center Operations Manager 관리 그룹을 Azure Monitor에 연결하여 데이터를 에이전트에서 Azure Monitor 로그로 수집할 수 있습니다. 이렇게 하면 로그 쿼리와 솔루션을 사용하여 에이전트에서 수집된 데이터를 분석할 수 있습니다. 또한 기존 System Center Operations Manager 에이전트에서 데이터를 Azure Monitor로 직접 보내도록 구성할 수 있습니다. [Azure Monitor에 Operations Manager 연결](platform/om-agents.md)을 참조하세요.

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure Monitor에서 사용하는 IP 주소는 어떻게 되나요?
에이전트 및 기타 외부 리소스에서 Azure Monitor에 액세스하는 데 필요한 IP 주소 및 포트 목록은 [Application Insights 및 Log Analytics에서 사용되는 IP 주소](app/ip-addresses.md)를 참조하세요. 

## <a name="monitoring-data"></a>데이터 모니터링

### <a name="where-does-azure-monitor-get-its-data"></a>Azure Monitor는 어디서 데이터를 가져오나요?
Azure Monitor는 Azure 플랫폼 및 리소스, 사용자 지정 애플리케이션 및 가상 머신에서 실행되는 에이전트의 로그와 메트릭을 포함하여 다양한 원본에서 데이터를 수집합니다. Azure Security Center 및 Network Watcher와 같은 기타 서비스는 Azure Monitor 데이터를 사용하여 분석할 수 있도록 데이터를 Log Analytics 작업 영역에 수집합니다. 또한 REST API를 로그 또는 메트릭에 사용하여 사용자 지정 데이터를 Azure Monitor에 보낼 수 있습니다. [Azure Monitor 모니터링의 데이터 원본](platform/data-sources.md)을 참조하세요.

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure Monitor에서 수집하는 데이터는 무엇인가요? 
Azure Monitor는 데이터를 다양한 원본에서 [로그](platform/data-platform-logs.md) 또는 [메트릭](platform/data-platform-metrics.md)으로 수집합니다. 각 데이터 형식에는 자체의 상대적 장점이 있으며, 각각 Azure Monitor의 특정 기능 세트를 지원합니다. Azure 구독마다 하나의 메트릭 데이터베이스가 있지만, 요구 사항에 따라 여러 Log Analytics 작업 영역을 만들어 로그를 수집할 수 있습니다. [Azure Monitor 데이터 플랫폼](platform/data-platform.md)을 참조하세요.

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Azure Monitor에서 수집할 수 있는 최대 데이터 양이 있나요?
수집할 수 있는 메트릭 데이터의 양에는 제한이 없으며, 이 데이터는 최대 93일 동안 저장됩니다. [메트릭 보존](platform/data-platform-metrics.md#retention-of-metrics)을 참조하세요. 수집할 수 있는 로그 데이터의 양에는 제한이 없지만, Log Analytics 작업 영역에 대해 선택하는 가격 책정 계층의 영향을 받을 수 있습니다. [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Azure Monitor에서 수집된 데이터에 액세스하려면 어떻게 해야 하나요?
인사이트와 솔루션은 Azure Monitor에 저장된 데이터를 사용할 수 있는 사용자 지정 환경을 제공합니다. KQL(Kusto 쿼리 언어)로 작성된 로그 쿼리를 사용하여 로그 데이터를 직접 사용할 수 있습니다. Azure Portal에서는 Log Analytics를 사용하여 쿼리를 작성 및 실행하고 데이터를 대화형으로 분석할 수 있습니다. Azure Portal에서 메트릭 탐색기를 사용하여 메트릭을 분석합니다. [Azure Monitor에서 로그 데이터 분석](log-query/log-query-overview.md) 및 [Azure 메트릭 탐색기 시작](platform/metrics-getting-started.md)을 참조하세요.

## <a name="solutions-and-insights"></a>솔루션 및 인사이트

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure Monitor의 인사이트는 무엇인가요?
인사이트는 특정 Azure 서비스에 대한 사용자 지정 모니터링 환경을 제공합니다. Azure Monitor의 다른 기능과 동일한 메트릭과 로그를 사용하지만, 추가 데이터를 수집하고 Azure Portal에서 고유한 환경을 제공할 수 있습니다. [Azure Monitor의 인사이트](insights/insights-overview.md)를 참조하세요.

Azure Portal에서 인사이트를 보려면 **모니터** 메뉴의 **인사이트** 섹션 또는 서비스 메뉴의 **모니터링** 섹션을 참조하세요.

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure Monitor의 솔루션은 무엇인가요?
모니터링 솔루션은 Azure Monitor 기능을 기반으로 하여 특정 애플리케이션 또는 서비스를 모니터링하도록 패키지된 논리 세트입니다. Azure Monitor에서 로그 데이터를 수집하고, Azure Portal의 일반적인 환경을 사용하여 분석을 위한 로그 쿼리와 보기를 제공합니다. [Azure Monitor의 모니터링 솔루션](insights/solutions.md)을 참조하세요.

Azure Portal에서 솔루션을 보려면 **모니터** 메뉴의 **인사이트** 섹션에서 **자세히**를 클릭합니다. **추가**를 클릭하여 추가 솔루션을 작업 영역에 추가합니다.

## <a name="logs"></a>로그

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure Monitor 로그와 Azure Data Explorer의 차이점은 무엇인가요?
Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Monitor 로그는 Azure Data Explorer를 기반으로 하여 구축되며, 약간의 차이가 있지만 동일한 KQL(Kusto 쿼리 언어)을 사용합니다. [Azure Monitor 로그 쿼리 언어 차이점](log-query/data-explorer-difference.md)을 참조하세요.

### <a name="how-do-i-retrieve-log-data"></a>로그 데이터를 검색하려면 어떻게 해야 하나요?
모든 데이터는 KQL(Kusto 쿼리 언어)로 작성된 로그 쿼리를 사용하여 Log Analytics 작업 영역에서 검색됩니다. 사용자 고유의 쿼리를 작성하거나 특정 애플리케이션 또는 서비스에 대한 로그 쿼리가 포함된 솔루션과 인사이트를 사용할 수 있습니다. [Azure Monitor의 로그 쿼리 개요](log-query/log-query-overview.md)를 참조하세요.

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 데이터를 삭제할 수 있나요?
[보존 기간](platform/manage-cost-storage.md#change-the-data-retention-period)에 따라 작업 영역에서 데이터가 제거 됩니다. 개인 정보나 규정 준수 상의 이유로 특정 데이터를 삭제할 수 있습니다. 자세한 내용은 [개인 데이터를 내보내고 삭제 하는 방법을](platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) 참조 하세요.


### <a name="what-is-a-log-analytics-workspace"></a>Log Analytics 작업 영역은 무엇인가요?
Azure Monitor에서 수집된 모든 로그 데이터는 Log Analytics 작업 영역에 저장됩니다. 작업 영역은 기본적으로 다양한 원본에서 로그 데이터가 수집되는 컨테이너입니다. 모든 모니터링 데이터에 대한 단일 Log Analytics 작업 영역이 있거나 여러 작업 영역에 대한 요구 사항이 있을 수 있습니다. [Azure Monitor 로그 배포 디자인](platform/design-logs-deployment.md)을 참조하세요.

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>기존 Log Analytics 작업 영역을 다른 Azure 구독으로 이동할 수 있나요?
작업 영역은 리소스 그룹 또는 구독 간에 이동할 수 있지만, 다른 지역으로 이동할 수는 없습니다. [Log Analytics 작업 영역을 다른 구독 또는 리소스 그룹으로 이동](platform/move-workspace.md)을 참조하세요.

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Log Analytics에서 쿼리 탐색기 및 [저장] 단추가 표시되지 않는 이유는 무엇인가요?

[쿼리 범위](log-query/scope.md)가 특정 리소스로 설정되어 있으면 **쿼리 탐색기**, **저장** 및 **새 경고 규칙** 단추를 사용할 수 없습니다. 경고를 만들거나, 쿼리를 저장 또는 로드하려면 Log Analytics의 범위가 작업 영역으로 지정되어야 합니다. 작업 영역 컨텍스트에서 Log Analytics를 열려면 **Azure Monitor** 메뉴에서 **로그**를 선택합니다. 마지막으로 사용한 작업 영역이 선택되지만 다른 작업 영역을 선택할 수 있습니다. [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](log-query/scope.md)를 참조하세요.

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>VM에서 Log Analytics를 열면 "이 구독에 대한 'Microsoft.Insights' 리소스 공급자를 등록하여 이 쿼리를 사용하도록 설정하세요."라는 오류가 표시되는 이유는 무엇인가요? 
대부분 리소스 공급자는 자동으로 등록되지만 일부 리소스 공급자는 수동으로 등록해야 할 수도 있습니다. 등록 범위는 항상 해당 구독입니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요.

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>VM에서 Log Analytics를 열면 '액세스 권한 없음' 오류 메시지가 표시되는 이유는 무엇인가요? 
VM 로그를 보려면 VM 로그를 저장하는 작업 영역에 대한 읽기 권한을 부여 받아야 합니다. 이런 경우 관리자가 Azure에서 내게 권한을 부여해 줘야 합니다.

## <a name="metrics"></a>메트릭

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>내 Azure 가상 머신의 게스트 OS에서 메트릭이 메트릭 탐색기에 표시되지 않는 이유는 무엇인가요?
[플랫폼 메트릭](insights/monitor-azure-resource.md#monitoring-data)은 Azure 리소스에 대해 자동으로 수집됩니다. 가상 머신의 게스트 OS에서 메트릭을 수집하려면 일부 항목을 구성해야 합니다. Windows VM의 경우 [WAD(Windows Azure 진단) 확장 설치 및 구성](platform/diagnostics-extension-windows-install.md)에서 설명한 대로 진단 확장을 설치하고 Azure Monitor 싱크를 구성합니다. Linux의 경우 [InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집](platform/collect-custom-metrics-linux-telegraf.md)에서 설명한 대로 Telegraf 에이전트를 설치합니다.

## <a name="alerts"></a>경고

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure Monitor의 경고는 무엇인가요?
경고는 모니터링 데이터에서 중요한 조건이 발견되면 사전에 알려줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 여러 종류의 경고가 있습니다.

- 메트릭 - 메트릭 값이 임계값을 초과합니다.
- 로그 쿼리 - 로그 쿼리의 결과가 정의된 조건과 일치합니다.
- 활동 로그 - 활동 로그 이벤트가 정의된 조건과 일치합니다.
- 웹 테스트 - 가용성 테스트 결과가 정의된 조건과 일치합니다.


[Microsoft Azure의 경고 개요](platform/alerts-overview.md)를 참조하세요.


### <a name="what-is-an-action-group"></a>작업 그룹은 무엇인가요?
작업 그룹은 경고로 트리거될 수 있는 알림 및 작업의 모음입니다. 여러 경고에서 하나의 작업 그룹을 사용하여 일반적인 알림 및 작업 세트를 활용할 수 있습니다. [Azure Portal에서 작업 그룹 만들기 및 관리](platform/action-groups.md)를 참조하세요.


### <a name="what-is-an-action-rule"></a>작업 규칙은 무엇인가요?
작업 규칙을 사용하면 특정 조건과 일치하는 경고 세트의 동작을 수정할 수 있습니다. 이렇게 하면 유지 관리 기간 동안 경고 작업을 사용 하지 않도록 설정 하는 등의 요구 사항을 수행할 수 있습니다. 또한 작업 그룹을 경고 규칙에 직접 적용하는 대신 경고 세트에 적용할 수 있습니다. [작업 규칙](platform/alerts-action-rules.md)을 참조하세요.

## <a name="agents"></a>에이전트

### <a name="does-azure-monitor-require-an-agent"></a>Azure Monitor에는 에이전트가 필요한가요?
에이전트는 운영 체제 및 가상 머신의 워크로드에서만 데이터를 수집하면 됩니다. 가상 머신은 Azure, 다른 클라우드 환경 또는 온-프레미스에 있을 수 있습니다. [Azure Monitor 에이전트 개요](platform/agents-overview.md)를 참조하세요.


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure Monitor 에이전트의 차이점은 무엇인가요?
Azure 진단 확장은 Azure 가상 머신을 위한 것이며, 데이터를 Azure Monitor 메트릭, Azure Storage 및 Azure Event Hubs에 수집합니다. Log Analytics 에이전트는 Azure, 다른 클라우드 환경 또는 온-프레미스에 있는 가상 머신을 위한 것이며, 데이터를 Azure Monitor 로그에 수집합니다. 종속성 에이전트에는 Log Analytics 에이전트 및 수집된 프로세스 세부 정보와 종속성이 필요합니다. [Azure Monitor 에이전트 개요](platform/agents-overview.md)를 참조하세요.


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>내 에이전트 트래픽에서 내 ExpressRoute 연결을 사용하나요?
Azure Monitor로의 트래픽은 Microsoft 피어링 ExpressRoute 회로를 사용합니다. 다양한 유형의 ExpressRoute 트래픽에 대한 설명은 [ExpressRoute 설명서](../expressroute/expressroute-faqs.md#supported-services)를 참조하세요. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Log Analytics 에이전트에서 Azure Monitor와 통신할 수 있는지 확인하려면 어떻게 해야 하나요?
에이전트 컴퓨터의 제어판에서 **보안 & 설정**, * * Microsoft Monitoring Agent를 선택 합니다. **Azure Log Analytics(OMS)** 탭 아래에서 녹색 확인 표시 아이콘은 에이전트에서 Azure 서비스와 통신할 수 있음을 나타냅니다. 노란색 경고 아이콘은 에이전트에 문제가 있음을 나타냅니다. 한 가지 일반적인 이유는 **Microsoft Monitoring Agent** 서비스가 중지되었다는 것입니다. 서비스 제어 관리자를 사용하여 서비스를 다시 시작합니다.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Log Analytics 에이전트에서 Azure Monitor와 통신하지 못하도록 중지하려면 어떻게 해야 하나요?
Log Analytics에 직접 연결된 에이전트의 경우 [제어판]을 열고, **보안 및 설정**, **Microsoft Monitoring Agent**를 차례로 선택합니다. **Azure Log Analytics(OMS)** 탭 아래에서 나열된 모든 작업 영역을 제거합니다. System Center Operations Manager의 Log Analytics 관리형 컴퓨터 목록에서 컴퓨터를 제거합니다. Operations Manager는 Log Analytics에 더 이상 보고하지 않도록 에이전트의 구성을 업데이트합니다. 

### <a name="how-much-data-is-sent-per-agent"></a>에이전트당 얼마나 많은 데이터가 전송되나요?
에이전트당 전송되는 데이터의 양에 따라 달라집니다.

* 사용하도록 설정한 솔루션
* 수집되는 로그 및 성능 카운터 수
* 로그에 있는 데이터 볼륨

자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](platform/manage-cost-storage.md)를 참조하세요.

WireData 에이전트를 실행할 수 있는 컴퓨터의 경우 다음 쿼리를 사용하여 전송 중인 데이터의 양을 확인합니다.

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>데이터를 Azure Monitor에 전송할 때 MMA(Microsoft Management Agent)에서 사용하는 네트워크 대역폭은 얼마나 되나요?
대역폭은 전송된 데이터 양에 대한 기능입니다. 네트워크를 통해 데이터가 전송되는 동안 데이터가 압축됩니다.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Log Analytics 에이전트의 데이터 수집이 중지되면 어떻게 알 수 있나요?

[새 로그 경고 만들기](platform/alerts-metric.md)에서 설명한 단계를 사용하여 데이터 수집이 중지될 때 알림을 받을 수 있습니다. 다음 설정을 경고 규칙에 사용합니다.

- **경고 조건 정의**: Log Analytics 작업 영역을 리소스 대상으로 지정합니다.
- **경고 기준** 
   - **신호 이름**: *사용자 지정 로그 검색*
   - **검색 쿼리**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **경고 논리**: **기준** *결과 수*, **조건** *보다 큼*, **임계값** *0*
   - **평가 기준**: **기간(분)** *30*, **빈도(분)** *10*
- **경고 세부 정보 정의** 
   - **Name**: *데이터 수집 중지됨*
   - **심각도**: *경고*

로그 경고가 조건과 일치할 때 하트비트가 15분 넘게 누락되면 알림을 받도록 기존 또는 새 [작업 그룹](platform/action-groups.md)을 지정합니다.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure Monitor 에이전트에 대한 방화벽 요구 사항은 무엇인가요?
방화벽 요구 사항에 대한 자세한 내용은 [네트워크 방화벽 요구 사항](platform/log-analytics-agent.md#network-requirements)을 참조하세요.


## <a name="visualizations"></a>시각화

### <a name="why-cant-i-see-view-designer"></a>보기 디자이너가 표시되지 않는 이유는 무엇인가요?

보기 디자이너는 Log Analytics 작업 영역에서 기여자 권한 이상이 할당된 사용자만 사용할 수 있습니다.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>구성 문제
*다음을 설정하는 데 문제가 있습니다.*

* [.NET 앱](app/asp-net-troubleshoot-no-data.md)
* [이미 실행 중인 앱 모니터링](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure 진단](platform/diagnostics-extension-to-application-insights.md)
* [Java 웹앱](app/java-troubleshoot.md)

*내 서버에서 데이터를 가져오지 않습니다.:*

* [방화벽 예외 설정](app/ip-addresses.md)
* [ASP.NET 서버 설정](app/monitor-performance-live-website-now.md)
* [Java 서버 설정](app/java-agent.md)

*배포 해야 하는 Application Insights 리소스 수:*

* [Application Insights 배포를 설계하는 방법: 하나 또는 여러 개의 Application Insights 리소스인가요?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Application Insights와 같이 사용할 수 있나요...?

* [Azure VM 또는 Azure 가상 머신 확장 집합의 IIS 서버에 있는 웹앱](app/azure-vm-vmss-apps.md)
* [IIS 서버의 웹앱 - 온-프레미스 또는 VM](app/asp-net.md)
* [Java 웹앱](app/java-get-started.md)
* [Node.js 앱](app/nodejs.md)
* [Azure의 Web Apps](app/azure-web-apps.md)
* [Azure의 Cloud Services](app/cloudservices.md)
* [Docker에서 실행되는 앱 서버](app/docker.md)
* [단일 페이지 웹앱](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows 데스크톱 앱](app/windows-desktop.md)
* [기타 플랫폼](app/platforms.md)

### <a name="is-it-free"></a>무료입니까?

예, 실험용입니다. 기본 가격 책정 계획에서, 사용자 애플리케이션은 매월 무료로 특정 데이터 할당량을 보낼 수 있습니다. 무료 할당량은 적은 수의 사용자에 대한 앱 개발 및 게시에 충분합니다. 지정된 데이터 크기보다 더 많이 처리하지 않도록 한도를 설정할 수 있습니다.

더 큰 볼륨의 원격 분석은 GB 단위로 청구됩니다. [요금 제한](app/pricing.md) 방법에 대한 몇 가지 팁을 제공합니다.

엔터프라이즈 계획에서는 각 웹 서버 노드에서 원격 분석을 전송하는 각 날짜에 대해 요금이 발생합니다. 대규모로 연속 내보내기를 사용하려는 경우에 적합합니다.

[가격 책정 계획을 참조하세요](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>비용은 얼마나 되나요?

* Application Insights 리소스에서 **사용량 및 예상 비용 페이지**를 엽니다. 최근 사용 현황에 대한 차트가 있습니다. 원하는 경우 데이터 볼륨 한도를 설정할 수 있습니다.
* [Azure 청구 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)를 열어 모든 리소스에 대한 청구서를 확인합니다.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights에서 내 프로젝트를 어떻게 수정하나요?
세부 정보는 프로젝트의 유형에 따라 달라집니다. 웹 애플리케이션의 경우:

* 프로젝트에 다음 파일이 추가됩니다.
  * ApplicationInsights.config
  * ai.js
* 다음 NuGet 패키지가 설치됩니다.
  * *Application Insights API* - 핵심 API
  * *웹 애플리케이션용 Application Insights API* - 서버에서 원격 분석을 보내는 데 사용
  * *JavaScript 애플리케이션용 Application Insights API* - 클라이언트에서 원격 분석을 보내는 데 사용
* 패키지에는 다음 어셈블리가 포함됩니다.
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 항목 삽입 위치:
  * Web.config
  * packages.config
* (새 프로젝트에만 해당 - [Application Insights를 기존 프로젝트에 추가][start]하는 경우 이 작업을 수동으로 수행해야 합니다.) 클라이언트 및 서버 코드에 코드 조각을 삽입하여 Application Insights 리소스 ID로 해당 코드를 초기화합니다. 예를 들어 MVC 앱에서 코드는 Views/Shared/\_Layout.cshtml 마스터 페이지에 삽입합니다.

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>이전 SDK 버전에서 업그레이드하려면 어떻게 해야 합니까?
사용자의 애플리케이션 유형에 적합한 SDK는 [릴리스 정보](app/release-notes.md)를 참조하세요.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>내 프로젝트에서 데이터를 보내는 Azure 리소스를 변경하려면 어떻게 해야 하나요?
솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에서 기존 또는 새 리소스로 데이터를 보낼 수 있습니다. 업데이트 마법사는 서버 SDK에서 데이터를 전송하는 위치를 결정하는 ApplicationInsights.config의 계측 키를 변경합니다. "모두 업데이트"를 선택 취소하지 않은 한, 웹 페이지에 표시되는 키도 변경됩니다.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>`providers('Microsoft.Insights', 'components').apiVersions[0]`는 내 Azure Resource Manager 배포에서 사용할 수 있나요?

이 방법은 API 버전을 채우는 데 사용하지 않는 것이 좋습니다. 최신 버전은 호환성이 손상되는 변경이 포함될 수 있는 미리 보기 릴리스를 나타낼 수 있습니다. 미리 보기가 아닌 최신 릴리스에서도 API 버전이 기존 템플릿 이전 버전과 항상 호환되지는 않으며, 경우에 따라 일부 구독에서 API 버전을 사용하지 못할 수도 있습니다.

### <a name="what-is-status-monitor"></a>상태 모니터란?

웹앱에서 Application Insights를 구성하기 위해 IIS 웹 서버에서 사용할 수 있는 데스크톱 앱입니다. 원격 분석을 수집하지 않으며 앱을 구성하지 않는 경우 중지할 수 있습니다. 

[자세히 알아보기](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>어떤 원격 분석이 Application Insights에서 수집되나요?

서버 웹앱:

* HTTP 요청
* [종속성](app/asp-net-dependencies.md). 호출: SQL Database, 외부 서비스(Azure Cosmos DB, 테이블, Blob Storage 및 큐)에 대한 HTTP 호출. 
* [예외](app/asp-net-exceptions.md) 및 스택 추적.
* [성능 카운터](app/performance-counters.md) - [상태 모니터](app/monitor-performance-live-website-now.md), [App Services에 대한 Azure 모니터링](app/azure-web-apps.md), [VM 또는 가상 머신 확장 집합에 대한 Azure 모니터링](app/azure-vm-vmss-apps.md) 또는 [Application Insights collectd 기록기](app/java-collectd.md)를 사용하는 경우
* 코딩하는 [사용자 지정 이벤트 및 메트릭](app/api-custom-events-metrics.md).
* 적절한 수집기를 구성한 경우 [추적 로그](app/asp-net-trace-logs.md).

[클라이언트 웹 페이지](app/javascript.md):

* [페이지 보기 수](app/usage-overview.md)
* 실행되는 스크립트의 [AJAX 호출](app/asp-net-dependencies.md) 요청.
* 페이지 보기 로드 데이터
* 사용자 및 세션 수
* [인증된 사용자 ID](app/api-custom-events-metrics.md#authenticated-users)

다른 원본(구성한 경우):

* [Azure 진단](platform/diagnostics-extension-to-application-insights.md)
* [Analytics로 가져오기](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>일부 원격 분석을 필터링하거나 수정할 수 있나요?

예, 서버에서 다음과 같이 할 수 있습니다.

* 원격 분석 프로세서를 작성하여 앱에서 전송되기 전에 선택한 원격 분석 항목을 필터링하거나 속성을 추가할 수 있습니다.
* 원격 분석 이니셜라이저를 작성하여 원격 분석의 모든 항목에 속성을 추가할 수 있습니다.

[ASP.NET](app/api-filtering-sampling.md) 또는 [Java](app/java-filter-telemetry.md)에 대해 자세히 알아보세요.

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>도시, 국가/지역 및 기타 지리적 위치 데이터는 어떻게 계산되나요?

[GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)를 사용하여 웹 클라이언트의 IP 주소(IPv4 또는 IPv6)를 조회합니다.

* 브라우저 원격 분석: 보낸 사람의 IP 주소를 수집합니다.
* 서버 원격 분석: Application Insights 모듈에서 클라이언트 IP 주소를 수집합니다. `X-Forwarded-For`가 설정된 경우에는 수집되지 않습니다.
* IP 주소 및 지리적 위치 데이터를 수집 하는 방법에 대 한 자세한 내용은이 [문서](./app/ip-collection.md)를 참조 Application Insights.


`ClientIpHeaderTelemetryInitializer`를 구성하여 다른 헤더에서 IP 주소를 가져올 수 있습니다. 예를 들어 일부 시스템에서는 프록시, 부하 분산 장치 또는 CDN에 의해 `X-Originating-IP`로 이동됩니다. [자세히 알아보기](https://apmtips.com/posts/2016-07-05-client-ip-address/).

[Power BI를 사용](app/export-power-bi.md )하여 요청 원격 분석을 지도에 표시할 수 있습니다.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>데이터가 포털에 얼마나 오래 보존되나요? 안전한가요?
[데이터 보존 및 개인 정보][data]를 살펴보세요.

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>서버 또는 디바이스에서 Azure와의 연결이 끊어지면 Application Insights의 원격 분석은 어떻게 되나요?

웹 SDK를 포함한 모든 SDK에는 "신뢰할 수 있는 전송" 또는 "강력한 전송"이 포함되어 있습니다. 서버 또는 디바이스에서 Azure와의 연결이 끊어지면 원격 분석이 [파일 시스템(서버 SDK) 또는 HTML5 세션 스토리지(웹 SDK)에 로컬로 저장](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)됩니다. 수집 서비스에서 "부실"로 간주할 때까지 SDK는 이 원격 분석을 주기적으로 보내려고 다시 시도합니다(로그의 경우 48시간, 메트릭의 경우 30분). 부실 원격 분석은 삭제됩니다. 다시 시도하는 경우(예: 로컬 스토리지가 가득 찬 경우)에 따라 수행되지 않습니다.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>개인 데이터를 원격 분석에 보낼 수 있나요?

코드에서 이러한 데이터를 전송하는 경우 가능합니다. 스택 추적의 변수에 개인 데이터가 포함된 경우에도 전송될 수 있습니다. 개발 팀은 개인 데이터가 제대로 처리되도록 위험 평가를 수행해야 합니다. [데이터 보존 및 개인 정보](app/data-retention-privacy.md)에 대해 자세히 알아보세요.

클라이언트 웹 주소의 **모든** 8진수는 지리적 위치 특성을 조회한 후에 항상 0으로 설정됩니다.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>내 계측 키가 내 웹 페이지 원본에 표시됩니다. 

* 모니터링 솔루션에서 일반적으로 사용됩니다.
* 데이터를 훔치는 데 사용할 수 없습니다.
* 데이터를 기울이거나 경고를 트리거하는 데 사용할 수 있습니다.
* 고객에게 이러한 문제가 발생한 경우는 없었습니다.

다음과 같이 할 수 있습니다.

* 별도의 두 가지 계측 키(별도의 Application Insights 리소스)를 클라이언트 및 서버 데이터에 사용합니다. 또는
* 서버에서 실행되는 프록시를 작성하고 웹 클라이언트에서 해당 프록시를 통해 데이터를 전송하도록 합니다.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>진단 검색에서 POST 데이터를 어떻게 확인하나요?
POST 데이터를 자동으로 기록 하지 않지만 TrackTrace 호출을 사용할 수 있습니다. 메시지 매개 변수에 데이터를 배치합니다. 이것은 필터링할 수 없지만 문자열 속성에 대한 제한보다 긴 제한이 있습니다.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>단일 또는 여러 Application Insights 리소스를 사용해야 하나요?

단일 비즈니스 시스템의 모든 구성 요소 또는 역할에 대해 단일 리소스를 사용합니다. 개발, 테스트 및 릴리스 버전과 독립 애플리케이션에 대해 별도의 리소스를 사용합니다.

* [여기에서 토론 참조](app/separate-resources.md)
* [예 - 작업자 및 웹 역할이 포함된 클라우드 서비스](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>계측 키를 동적으로 변경하려면 어떻게 해야 하나요?

* [여기에서 토론 참조](app/separate-resources.md)
* [예 - 작업자 및 웹 역할이 포함된 클라우드 서비스](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>사용자 및 세션 수란?

* JavaScript SDK는 웹 클라이언트에서 사용자 쿠키를 설정하여 다시 방문하는 사용자와 그룹 작업에 대한 세션 쿠키를 식별합니다.
* 클라이언트 쪽 스크립트가 없으면 [서버에서 쿠키를 설정](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/)할 수 있습니다.
* 한 명의 실제 사용자가 특정 사이트를 다른 브라우저에서 사용하거나, in-private/incognito 검색을 통해 사용하거나, 다른 컴퓨터에서 사용하는 경우 두 번 이상 계산됩니다.
* 여러 컴퓨터 및 브라우저에서 로그인한 사용자를 식별하려면 [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users)에 대한 호출을 추가합니다.

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Application Insights의 모든 기능을 사용하도록 어떻게 설정하나요?
| 표시 내용 | 시작 방법 | 원하는 이유 |
| --- | --- | --- |
| 가용성 차트 |[웹 테스트](app/monitor-web-app-availability.md) |웹 앱이 작동 중인지 확인 |
| 서버 앱 성능: 응답시간. ... |[프로젝트에 Application Insights 추가](app/asp-net.md) 또는 [서버에 AI 상태 모니터 설치](app/monitor-performance-live-website-now.md)(또는 [종속성 추적](app/api-custom-events-metrics.md#trackdependency)을 위한 자체 코드 작성) |성능 문제 검색 |
| 종속성 원격 분석 |[서버에 AI 상태 모니터 설치](app/monitor-performance-live-website-now.md) |데이터베이스 또는 다른 외부 구성 요소의 문제 진단 |
| 예외에서 스택 추적 가져오기 |[TrackException 호출을 코드에 삽입](app/asp-net-exceptions.md)(하지만 일부는 자동으로 보고됨) |예외 감지 및 진단 |
| 로그 추적 검색 |[로깅 어댑터 추가](app/asp-net-trace-logs.md) |예외, 성능 문제 진단 |
| 클라이언트 사용 기본 사항: 페이지 보기, 세션,... |[웹 페이지의 JavaScript 이니셜라이저](app/javascript.md) |사용량 현황 분석 |
| 클라이언트 사용자 지정 메트릭 |[웹 페이지에서 추적 호출](app/api-custom-events-metrics.md) |사용자 환경 향상 |
| 서버 사용자 지정 메트릭 |[서버에서 호출 추적](app/api-custom-events-metrics.md) |비즈니스 인텔리전스 |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>검색 및 메트릭 차트의 수가 다른 이유는 무엇인가요?

[샘플링](app/sampling.md)을 하면 실제로 앱에서 포털로 전송되는 원격 분석 항목(요청, 사용자 지정 이벤트 등)의 수가 줄어듭니다. 검색에는 실제로 받은 항목 수가 표시됩니다. 이벤트 수를 표시하는 메트릭 차트에는 발생된 원래 이벤트 수가 표시됩니다. 

전송되는 각 항목은 해당 항목이 나타내는 원래 이벤트의 수를 보여 주는 `itemCount` 속성을 전달합니다. 작업 중인 샘플링을 관찰하려면 분석에서 다음 쿼리를 실행할 수 있습니다.

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Application Insights 구성

Azure Resource Monitor를 통해 [PowerShell 스크립트를 작성](app/powershell.md)하여 다음을 수행할 수 있습니다.

* Application Insights 리소스를 만들고 업데이트합니다.
* 가격 책정 계획을 설정합니다.
* 계측 키를 가져옵니다.
* 메트릭 경고를 추가합니다.
* 가용성 테스트를 추가합니다.

메트릭 탐색기 보고서를 설정하거나 연속 내보내기를 설정할 수는 없습니다.

#### <a name="querying-the-telemetry"></a>원격 분석 쿼리

[REST API](https://dev.applicationinsights.io/)를 사용하여 [분석](./log-query/log-query-overview.md) 쿼리를 실행합니다.

### <a name="how-can-i-set-an-alert-on-an-event"></a>이벤트에 대한 경고를 설정하려면 어떻게 해야 하나요?

Azure 경고는 메트릭에 대해서만 설정됩니다. 이벤트가 발생할 때마다 값 임계값을 초과하는 사용자 지정 메트릭을 만듭니다. 그런 다음 메트릭에 대해 경고를 설정합니다. 메트릭이 어느 방향으로든 임계값을 초과할 때마다 알림을 받게 됩니다. 초기 값이 높거나 낮은지 여부에 관계없이 처음 초과할 때까지는 알림을 받지 않습니다. 항상 몇 분 정도의 대기 시간이 있습니다.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure 웹앱과 Application Insights 간 데이터 전송 요금이 있나요?

* Azure 웹앱이 Application Insights 컬렉션 엔드포인트가 있는 데이터 센터에서 호스트되는 경우 무료입니다. 
* 호스트 데이터 센터에 컬렉션 엔드포인트가 없으면 앱의 원격 분석에 [Azure 발신 요금](https://azure.microsoft.com/pricing/details/bandwidth/)이 부과됩니다.

이 요금은 Application insights 리소스가 호스트되는 위치에 따라 달라지지 않습니다. 엔드포인트의 배포에 따라 달라집니다.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Application Insights 포털에 원격 분석을 보낼 수 있나요?

저희가 제공하는 SDK 및 [SDK API](app/api-custom-events-metrics.md)를 사용하는 것이 좋습니다. 다양한 [플랫폼](app/platforms.md)에 따라 여러 SDK 변형이 있습니다. 이러한 SDK는 버퍼링, 압축, 제한, 다시 시도 등을 처리합니다. 그러나 [수집 스키마](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) 및 [엔드포인트 프로토콜](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)은 공용입니다.

### <a name="can-i-monitor-an-intranet-web-server"></a>인트라넷 웹 서버를 모니터링할 수 있나요?

예, 하지만 방화벽 예외 또는 프록시 리디렉션을 통해 Microsoft 서비스로 전송되는 트래픽을 허용해야 합니다.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


서비스와 IP 주소의 전체 목록은 [여기](app/ip-addresses.md)서 검토하세요.

#### <a name="firewall-exception"></a>방화벽 예외

웹 서버가 엔드포인트에 원격 분석을 전송할 수 있습니다. 

#### <a name="gateway-redirect"></a>게이트웨이 리디렉션

구성의 엔드포인트를 덮어쓰는 방식으로 서버에서 인트라넷의 게이트웨이로 트래픽을 라우팅합니다. 구성에 이러한 "엔드포인트" 설정이 없으면 해당 클래스는 아래의 예제 ApplicationInsights.config에 나와 있는 기본값을 사용합니다. 

게이트웨이는 엔드포인트의 기준 주소로 트래픽을 라우팅해야 합니다. 구성에서 기본값을 `http://<your.gateway.address>/<relative path>`로 바꿉니다.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>기본 엔드포인트가 포함된 ApplicationInsights.config 예제:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider는 v2.6.0부터 사용할 수 있습니다.



#### <a name="proxy-passthrough"></a>프록시 통과

프록시 통과는 머신 수준 또는 애플리케이션 수준 프록시를 구성하여 구현할 수 있습니다.
자세한 내용은 [DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)에 대한 dotnet 문서를 참조하세요.
 
 Web.config 예제:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>인트라넷 서버에서 가용성 웹 테스트를 실행할 수 있나요?

[웹 테스트](app/monitor-web-app-availability.md)는 전 세계에 배포된 클라이언트 로그인 지점에서 실행됩니다. 두 가지 해결 방법이 있습니다.

* 방화벽 문 - [길고 변경 가능한 웹 테스트 에이전트 목록](app/ip-addresses.md)에서 서버에 대한 요청을 허용합니다.
* 인트라넷 내부에서 서버에 주기적으로 요청을 보내는 코드를 직접 작성합니다. 이러한 목적으로 Visual Studio 웹 테스트를 실행할 수 있습니다. 테스터는 TrackAvailability() API를 사용하여 결과를 Application Insights에 보낼 수 있습니다.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>원격 분석을 수집하려면 시간이 얼마나 걸리나요?

대부분의 Application Insights 데이터에는 5분 이하의 대기 시간이 포함됩니다. 일부 데이터(일반적으로 대규모 로그 파일)의 경우 시간이 오래 걸릴 수 있습니다. 자세한 내용은 [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)를 참조하세요.



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>HTTP 502 및 503 응답은 Application Insights에서 항상 캡처되지는 않습니다.

"502 잘못 된 게이트웨이" 및 "503 서비스를 사용할 수 없음" 오류는 Application Insights에서 항상 캡처되지는 않습니다. 클라이언트 쪽 JavaScript만 모니터링에 사용 되는 경우 모니터링 JavaScript 코드 조각이 렌더링 되는 HTML 헤더를 포함 하는 페이지 보다 먼저 오류 응답이 반환 되기 때문에이 동작이 예상 됩니다. 

서버 쪽 모니터링을 사용 하는 서버에서 502 또는 503 응답을 보낸 경우 Application Insights SDK에서 오류를 수집 합니다. 

그러나 응용 프로그램의 웹 서버에서 서버 쪽 모니터링을 사용 하도록 설정한 경우에도 Application Insights에서 502 또는 503 오류가 캡처되지 않는 경우도 있습니다. 많은 최신 웹 서버에서는 클라이언트가 직접 통신할 수 없으며 대신 역방향 프록시와 같은 솔루션을 사용 하 여 클라이언트와 프런트 엔드 웹 서버 간에 정보를 전달 합니다. 

이 시나리오에서는 역방향 프록시 계층에서 문제가 발생 하 여 502 또는 503 응답이 클라이언트에 반환 될 수 있으며이는 Application Insights 하 여 기본으로 캡처되지 않습니다. 이 계층에서 문제를 검색 하려면 역방향 프록시에서 Log Analytics로 로그를 전달 하 고 502/503 응답을 확인 하는 사용자 지정 규칙을 만들어야 할 수 있습니다. 502 및 503 오류의 일반적인 원인에 대해 자세히 알아보려면 ["502 잘못 된 게이트웨이" 및 "503 Service를 사용할 수 없음"에 대 한 Azure App Service 문제 해결 문서](../app-service/troubleshoot-http-502-http-503.md)를 참조 하세요.     

## <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor

Microsoft FAQ는 컨테이너용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

### <a name="health-feature-is-in-private-preview"></a>상태 기능이 공개 미리 보기에 있음

기능을 추가하고 피드백을 처리하기 위해 일련의 변경을 수행할 예정입니다. 상태 기능은 2020년 6월 말에 공개 미리 보기로 전환되며, 추가 정보에 대해서는 다음 [Azure 업데이트 공지](https://azure.microsoft.com/updates/ci-health-limited-preview/)를 검토하세요.

### <a name="what-does-other-processes-represent-under-the-node-view"></a>[노드] 보기 아래의 *기타 프로세스*에서 나타내는 것은 무엇인가요?

**기타 프로세스**는 노드의 리소스 사용량이 많은 근본 원인을 명확히 이해하는 데 도움을 주기 위한 것입니다. 이렇게 하면 컨테이너화된 프로세스와 컨테이너화되지 않은 프로세스 간의 사용량을 구분할 수 있습니다.

이러한 **기타 프로세스**는 무엇일까요? 

이러한 프로세스는 노드에서 실행되는 컨테이너화되지 않은 프로세스입니다.  

이를 계산하려면 어떻게 해야 하나요?

**기타 프로세스** = *CAdvisor의 총 사용량* - *컨테이너화되지 않은 프로세스의 사용량*

**기타 프로세스**는 다음과 같습니다.

- 자체 관리형 또는 Kubernetes 관리형 컨테이너화되지 않은 프로세스 

- 컨테이너 런타임 프로세스  

- kubelet  

- 노드에서 실행되는 시스템 프로세스 

- 노드 하드웨어 또는 VM에서 실행되는 기타 비 Kubernetes 워크로드 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>ContainerLog 테이블을 쿼리하면 채워지는 Image 및 Name 속성 값이 표시되지 않습니다.

ciprod12042019 버전 이상의 에이전트에서는 수집된 로그 데이터에서 발생하는 비용을 최소화하기 위해 기본적으로 이 두 가지 속성이 모든 로그 줄에 대해 채워지지 않습니다. 이러한 속성이 값과 함께 포함된 테이블을 쿼리할 수 있는 두 가지 옵션이 있습니다.

#### <a name="option-1"></a>옵션 1 

다른 테이블을 조인하여 이러한 속성 값을 결과에 포함시킵니다.

ContainerID 속성에 조인하여 ```ContainerInventory``` 테이블의 Image 및 ImageTag 속성을 포함하도록 쿼리를 수정합니다. ContainerID 속성에 조인하여 KubepodInventory 테이블의 ContaineName 필드에서 Name 속성(이전에는 ```ContainerLog``` 테이블에 표시됨)을 포함할 수 있습니다. 이는 추천되는 옵션입니다.

다음 예제는 조인을 사용하여 이러한 필드 값을 가져오는 방법을 설명하는 자세한 쿼리 샘플입니다.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>옵션 2

모든 컨테이너 로그 줄에 대해 이러한 속성에 대한 컬렉션을 다시 사용하도록 설정합니다.

관련된 쿼리 변경으로 인해 첫 번째 옵션이 편리하지 않은 경우 [데이터 컬렉션 구성 설정](insights/container-insights-agent-config.md)에서 설명한 대로 에이전트 구성 맵에서 ```log_collection_settings.enrich_container_logs``` 설정을 사용하도록 설정하여 이러한 필드의 컬렉션을 사용하도록 다시 설정할 수 있습니다.

> [!NOTE]
> 두 번째 옵션은 이 보강을 수행하기 위해 클러스터의 모든 노드에서 API 서버 호출을 생성하므로 50개가 넘는 노드가 있는 대형 클러스터에는 추천되지 않습니다. 또한 이 옵션은 수집된 모든 로그 줄에 대한 데이터 크기를 늘립니다.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Grafana에서 수집된 메트릭을 볼 수 있나요?

컨테이너용 Azure Monitor는 Grafana 대시보드의 Log Analytics 작업 영역에 저장된 메트릭을 볼 수 있도록 지원합니다. Grafana의 [대시보드 리포지토리](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)에서 다운로드하여 시작하고, 모니터링할 클러스터에서 추가 데이터를 쿼리하여 사용자 지정 Grafana 대시보드에서 시각화하는 방법을 습득하는 데 도움이 되기 위해 참조할 수 있는 템플릿을 제공했습니다. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>컨테이너용 Azure Monitor를 사용하여 내 AKS 엔진 클러스터를 모니터링할 수 있나요?

컨테이너용 Azure Monitor는 Azure에서 호스팅되는 AKS 엔진(이전의 ACS 엔진) 클러스터에 배포된 컨테이너 워크로드를 모니터링할 수 있도록 지원합니다. 이 시나리오에 대한 모니터링을 사용하도록 설정하는 데 필요한 단계에 대한 자세한 내용 및 개요는 [AKS 엔진에 컨테이너용 Azure Monitor 사용](https://github.com/microsoft/OMS-docker/tree/aks-engine)을 참조하세요.

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>내 Log Analytics 작업 영역에 데이터가 표시되지 않는 이유는 무엇인가요?

매일 특정 시간에 Log Analytics 작업 영역에서 데이터를 볼 수 없는 경우 매일 수집할 데이터 양을 제어하기 위해 지정된 기본 500MB 제한 또는 일별 한도에 도달했을 수 있습니다. 해당 일에 대한 제한에 도달하면 데이터 수집이 중지되고 다음 일에만 다시 시작됩니다. 예상 사용량 패턴에 따라 데이터 사용량을 검토하고 다른 가격 책정 계층으로 업데이트하려면 [로그 데이터 사용량 및 비용](platform/manage-cost-storage.md)을 참조하세요. 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 테이블에 지정된 컨테이너 상태는 무엇인가요?

ContainerInventory 테이블에는 중지된 컨테이너와 실행 중인 컨테이너 둘 다에 대한 정보가 포함되어 있습니다. 이 테이블은 Docker에서 모든 컨테이너(실행 중 및 중지)를 쿼리하고 해당 데이터를 Log Analytics 작업 영역에 전달하는 에이전트 내 워크플로로 채워집니다.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>*구독 등록 누락* 오류를 해결하려면 어떻게 해야 하나요?

**Microsoft.OperationsManagement에 대한 구독 등록 누락** 오류가 표시되면 **Microsoft.OperationsManagement** 리소스 공급자를 작업 영역이 정의된 구독에 등록하여 해결할 수 있습니다. 이 작업을 수행하는 방법에 대한 설명서는 [여기](../azure-resource-manager/templates/error-register-resource-provider.md)에서 찾을 수 있습니다.

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>RBAC 사용 AKS 클러스터에 대한 지원이 있나요?

컨테이너 모니터링 솔루션은 RBAC를 지원하지 않지만 컨테이너용 Azure Monitor에서 지원됩니다. 솔루션 세부 정보 페이지에 이러한 클러스터에 대한 데이터를 표시하는 블레이드에 올바른 정보가 표시되지 않을 수 있습니다.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Helm을 통해 kube-system 네임스페이스의 컨테이너에 대해 로그 수집을 사용하도록 설정하려면 어떻게 하나요?

kube-system 네임스페이스에 포함된 컨테이너의 로그 수집은 기본적으로 사용하지 않도록 설정됩니다. omsagent에 환경 변수를 설정하여 로그 수집을 사용하도록 설정할 수 있습니다. 자세한 내용은 [컨테이너용 Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub 페이지를 참조하세요. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>omsagent를 최신 릴리스 버전으로 업데이트하려면 어떻게 하나요?

에이전트를 업그레이드하는 방법을 알아보려면 [에이전트 관리](insights/container-insights-manage-agent.md)를 참조하세요.

### <a name="how-do-i-enable-multi-line-logging"></a>여러 줄 로깅을 사용하도록 설정하려면 어떻게 하나요?

컨테이너용 Azure Monitor는 현재 여러 줄 로깅을 지원하지 않지만 해결 방법이 있습니다. JSON 형식으로 쓰도록 모든 서비스를 구성하면 Docker/Moby가 해당 데이터를 한 줄에 씁니다.

예를 들어, 샘플 node.js 애플리케이션에 대한 아래 예제와 같이 로그를 JSON 개체로 래핑할 수 있습니다.

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

이 데이터는 쿼리할 때 로그에 대한 Azure Monitor에서 다음 예제와 같이 표시됩니다.

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

이 문제를 자세히 살펴보려면 다음 [GitHub 링크](https://github.com/moby/moby/issues/22920)를 검토하세요.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>라이브 로그를 사용하도록 설정할 때 발생하는 Azure AD 오류를 해결하려면 어떻게 해야 하나요? 

다음 오류가 표시될 수 있습니다. **요청에 지정된 회신 URL이 '<application ID\>' 애플리케이션에 대해 구성된 회신 URL과 일치하지 않습니다**. 이 오류를 해결하는 방법은 [컨테이너용 Azure Monitor를 사용하여 컨테이너 데이터를 실시간으로 보는 방법](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) 문서에서 확인할 수 있습니다. 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>온보딩 후 클러스터를 업그레이드할 수 없는 이유는 무엇인가요?

컨테이너용 Azure Monitor를 AKS 클러스터에 사용하도록 설정한 후에 클러스터를 업그레이드하려고 할 때 클러스터에서 해당 데이터를 보낸 Log Analytics 작업 영역을 삭제하면 업그레이드 작업이 실패합니다. 이 문제를 해결하려면 모니터링을 사용하지 않도록 설정한 다음, 구독의 다른 유효한 작업 영역을 참조하여 사용하도록 다시 설정해야 합니다. 클러스터 업그레이드를 다시 수행하려고 하면 성공적으로 처리되고 완료됩니다.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>에이전트에 대해 열고 허용 목록에 추가해야 하는 포트와 도메인은 무엇인가요?

Azure, Azure US Government 및 Azure 중국 21Vianet 클라우드를 사용하여 컨테이너화된 에이전트에 필요한 프록시 및 방화벽 구성 정보는 [네트워크 방화벽 요구 사항](insights/container-insights-onboard.md#network-firewall-requirements)을 참조하세요.

## <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor
Microsoft FAQ는 VM용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

### <a name="can-i-onboard-to-an-existing-workspace"></a>기존 작업 영역에 온보딩할 수 있나요?
가상 컴퓨터가 이미 Log Analytics 작업 영역에 연결 되어 있는 경우 지원 되는 [지역](insights/vminsights-configure-workspace.md#supported-regions)중 하나에 있는 경우 VM용 Azure Monitor에 등록할 때 해당 작업 영역을 계속 사용할 수 있습니다.


### <a name="can-i-onboard-to-a-new-workspace"></a>새 작업 영역에 온보딩할 수 있나요? 
현재 VM이 기존 Log Analytics 작업 영역에 연결되지 않은 경우 데이터를 저장할 새 작업 영역을 만들어야 합니다. Azure Portal을 통해 VM용 Azure Monitor에 대해 단일 Azure VM을 구성한 경우 새 기본 작업 영역을 만드는 작업이 자동으로 수행됩니다.

스크립트 기반 방법을 사용하도록 선택하는 경우 이러한 단계는 [Azure PowerShell 또는 Resource Manager 템플릿을 사용하여 VM용 Azure Monitor 사용](./insights/vminsights-enable-powershell.md) 문서에서 설명합니다. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>내 VM에서 이미 기존 작업 영역에 보고하는 경우 어떻게 해야 하나요?
이미 가상 머신의 데이터를 수집하는 경우 기존 Log Analytics 작업 영역에 데이터를 보고하도록 이미 구성되었을 수 있습니다.  해당 작업 영역이 지원되는 지역 중 하나라면 기존 작업 영역에 VM용 Azure Monitor를 사용하도록 설정할 수 있습니다.  이미 사용하고 있는 작업 영역이 지원되는 지역 중 하나가 아닌 경우 지금은 VM용 Azure Monitor에 온보딩할 수 없습니다.  본사에서는 추가 지역을 지원하기 위해 적극적으로 노력하고 있습니다.


### <a name="why-did-my-vm-fail-to-onboard"></a>내 VM이 온보딩하지 못한 이유는?
Azure Portal에서 Azure VM을 온보딩한 경우 다음 단계가 수행됩니다.

* 해당 옵션을 선택한 경우 기본 Log Analytics 작업 영역이 만들어집니다.
* 필요한 경우 Log Analytics 에이전트는 VM 확장을 사용하여 설치됩니다.  
* 필요한 경우 VM용 Azure Monitor 맵 종속성 에이전트는 VM 확장을 사용하여 Azure VM에 설치됩니다. 

온보딩 프로세스 중에 포털에서 알림 상태를 반환하는 위의 각 상태에 대해 확인합니다. 작업 영역 및 에이전트 설치의 구성에는 일반적으로 5~10분 정도가 걸립니다. 포털에서 모니터링 데이터를 보는 데 5~10분이 더 걸립니다.  

온보딩을 시작하고 VM이 온보딩되어야 함을 나타내는 메시지가 표시된 경우 VM이 프로세스를 완료하려면 최대 30분이 허용됩니다. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>내 VM의 성능 차트에 일부 또는 모든 데이터가 표시되지 않습니다
성능 차트에서 *InsightsMetrics* 테이블에 저장된 데이터를 사용하도록 업데이트되었습니다.  이러한 차트의 데이터를 보려면 새 VM 인사이트 솔루션을 사용하도록 업그레이드해야 합니다.  자세한 내용은 [GA FAQ](insights/vminsights-ga-release-faq.md)를 참조하세요.

디스크 테이블 또는 일부 성능 차트에 성능 데이터가 표시되지 않는 경우 작업 영역에서 성능 카운터가 구성되지 않을 수 있습니다. 이 문제를 해결하려면 다음 [PowerShell 스크립트](./insights/vminsights-enable-powershell.md)를 실행합니다.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>VM용 Azure Monitor 맵 기능과 서비스 맵이 어떻게 다른가요?
VM용 Azure Monitor 맵 기능은 서비스 맵을 기반으로 하지만 다음과 같은 차이점이 있습니다.

* 맵 보기는 VM 블레이드 및 Azure Monitor의 VM용 Azure Monitor에서 액세스할 수 있습니다.
* 이제 맵에서 연결은 클릭 가능하고 선택한 연결에 대한 사이드 패널에서 연결 메트릭 데이터 보기를 표시합니다.
* 더 복잡한 맵을 지원하기 위해 맵을 만드는 데 사용되는 새 API가 있습니다.
* 이제 모니터링된 VM은 클라이언트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 가상 머신 및 모니터링되지 않는 가상 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 이제 모니터링된 가상 머신은 서버 포트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 머신 및 모니터링되지 않는 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 맵 스타일은 Application insights에서 앱 맵을 사용하여 더욱 일관되게 업데이트되었습니다.
* 사이드 패널이 업데이트되었으며, 서비스 맵에서 지원되는 전체 통합 세트(업데이트 관리, 변경 내용 추적, 보안 및 서비스 데스크)를 제공하지 않습니다. 
* 맵에 대한 그룹 및 머신을 선택하는 옵션이 업데이트되어 이제 구독, 리소스 그룹, Azure 가상 머신 확장 집합 및 클라우드 서비스를 지원합니다.
* VM용 Azure Monitor 맵 기능에서 새 서비스 맵 머신 그룹을 만들 수 없습니다.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>내 성능 차트가 점선을 표시하는 이유는?
다음과 같은 이유로 발생할 수 있습니다.  데이터 수집에 간격이 있는 경우에 줄이 점선으로 그려집니다.  사용하도록 설정된 성능 카운터에 대한 데이터 샘플링 빈도를 수정한 경우(기본 설정은 60초마다 데이터를 수집하는 것임) 차트에 대해 좁은 시간 범위를 선택하고 샘플링 빈도가 차트에 사용된 버킷 크기 미만이면(예: 샘플링 빈도는 10분 간격이며 차트의 각 버킷은 5분 간격임) 차트에서 점선을 볼 수 있습니다.  광범위한 시간 범위를 보도록 선택하면 이 경우에 차트 선이 점선이 아닌 실선으로 표시됩니다.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>VM용 Azure Monitor에서 그룹이 지원되나요?
예, 종속성 에이전트가 설치되면 VM에서 정보를 수집하여 구독, 리소스 그룹, 가상 머신 확장 집합 및 클라우드 서비스에 기반한 그룹을 표시합니다.  서비스 맵을 사용하고 있고 시스템 그룹을 만든 경우에도 이러한 그룹이 표시됩니다.  또한 보고 있는 작업 영역에 대한 컴퓨터 그룹을 만든 경우에도 그룹 필터에 표시됩니다. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>집계 성능 차트에서 95번째 백분위수 줄에 영향을 주는 항목에 대한 세부 정보를 표시하려면 어떻게 할까요?
기본적으로 목록은 5번째 백분위수의 최솟값을 포함한 머신을 보여주는 사용 가능한 메모리 차트를 제외하고 선택된 메트릭에 대해 95번째 백분위수에 대한 최댓값이 있는 VM을 표시하도록 정렬됩니다.  차트를 클릭하면 적절한 메트릭이 선택된 **상위 N개 목록** 보기가 열립니다.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>맵 기능이 다른 VNet 및 서브넷에서 중복 IP를 어떻게 처리하나요?
서브넷 및 VNet에서 VM 또는 Azure 가상 머신 확장 집합을 사용하여 IP 범위를 복제하는 경우 VM용 Azure Monitor에서 잘못된 정보를 표시할 수 있습니다. 알려진 문제이며 본사에서는 이 환경을 개선하는 옵션을 개발하는 중입니다.

### <a name="does-map-feature-support-ipv6"></a>맵 기능이 IPv6을 지원하나요?
맵 기능은 현재 IPv4만 지원하며 본사에서는 IPv6에 대한 지원을 개발하는 중입니다. IPv6 내에서 터널링된 IPv4도 지원합니다.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>리소스 그룹 또는 다른 대규모 그룹에 대한 맵을 로드하는 경우 맵을 보기 어렵습니다.
크고 복잡한 구성을 처리하도록 맵을 개선하는 동안 맵에서는 많은 노드, 연결 및 노드가 클러스터로 작동할 수 있습니다.  계속 확장성을 증가시키도록 지원을 강화하기 위해 최선을 다하고 있습니다.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>성능 탭의 네트워크 차트가 Azure VM 개요 페이지의 네트워크 차트와 다르게 보이는 이유는?

Azure VM의 개요 페이지에는 게스트 VM에서 작업의 호스트 측정값에 따라 차트가 표시됩니다.  Azure VM 개요에 대한 네트워크 차트의 경우 청구되는 네트워크 트래픽만 표시합니다.  여기에는 가상 네트워크 간 트래픽이 포함되지 않습니다.  VM용 Azure Monitor에 대해 표시되는 데이터와 차트는 게스트 VM의 데이터를 기반으로 하고, 네트워크 차트는 가상 네트워크 간 트래픽을 포함하여 해당 VM에 대한 인바운드 및 아웃바운드인 모든 TCP/IP 트래픽을 표시합니다.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>VMConnection에 저장되고 연결 패널 및 통합 문서에 표시되는 데이터에 대한 응답 시간은 어떻게 측정되나요?

응답 시간은 근사값입니다. 애플리케이션 코드를 계측하지 않으므로 요청이 시작되는 시기와 응답이 도착하는 시기를 정확히 알 수 없습니다. 대신 연결에서 나가는 데이터를 관찰한 다음, 해당 연결에서 다시 들어오는 데이터를 관찰합니다. 에이전트는 이러한 송수신을 추적하고 페어링을 시도합니다. 일련의 송신과 이어지는 일련의 수신이 요청/응답 쌍으로 해석됩니다. 이러한 작업 간의 타이밍은 응답 시간입니다. 여기에는 네트워크 대기 시간과 서버 처리 시간이 포함됩니다.

이 근사값은 요청/응답 기반 프로토콜에 적합합니다. 연결에서 단일 요청을 보내고 단일 응답을 받습니다. 이는 HTTP(S)의 경우(파이프라이닝 없음)이지만 다른 프로토콜에서는 충족되지 않습니다.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Log Analytics 무료 가격 플랜을 사용하고 있는 경우 제한 사항이 있나요?
*무료* 가격 책정 계층을 사용하여 Log Analytics 작업 영역에 Azure Monitor를 구성한 경우 VM용 Azure Monitor 맵 기능은 5대의 작업 영역에 연결된 머신만 지원합니다. 무료 작업 영역에 5대의 VM이 연결되어 있는 경우 VM 중 하나의 연결을 끊은 후 나중에 새 VM을 연결하면 맵 페이지에서 새 VM이 모니터링 및 반영되지 않습니다.  

이 조건에서는 VM을 열고 이미 해당 VM에 설치된 후에도 왼쪽 창에서 **인사이트**를 선택하면 **지금 사용해 보기** 옵션이 포함된 메시지가 표시됩니다.  그러나 이 VM이 VM용 Azure Monitor에 등록되지 않은 경우에는 일반적인 경우처럼 옵션이 포함된 메시지가 표시되지 않습니다. 


## <a name="next-steps"></a>다음 단계
질문에 대한 대답이 여기에 없으면 다음 포럼에서 추가 질문 및 대답을 참조할 수 있습니다.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Azure Monitor에 대한 일반적인 피드백은 [피드백 포럼](https://feedback.azure.com/forums/34192--general-feedback)을 방문하세요.