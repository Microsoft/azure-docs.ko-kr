---
title: Azure Monitor FAQ | Microsoft Docs
description: Azure Monitor에 대한 자주 묻는 질문에 대한 대답입니다.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: 9f377f93ab8fef2c1ad713da6fcd6c6f14107c3f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986821"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor 질문과 대답

이 Microsoft FAQ는 Azure Monitor에 대한 자주 묻는 질문의 목록입니다.

## <a name="general"></a>일반

### <a name="what-is-azure-monitor"></a>Azure Monitor란?
[Azure Monitor](overview.md) 는 azure, 다른 클라우드 환경 또는 온-프레미스의 응용 프로그램 및 서비스에 대한 성능 및 가용성 모니터링을 제공 하는 azure의 서비스입니다. Azure Monitor는 여러 원본의 데이터를 일반적인 데이터 플랫폼으로 수집 하 여 추세 및 비정상을 분석할 수 있습니다. Azure Monitor의 다양 한 기능을 통해 응용 프로그램에 영향을 줄 수 있는 중요 한 상황을 신속 하 게 식별 하 고 대응할 수 있습니다.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure Monitor, Log Analytics 및 Application Insights 간의 차이점은 무엇 인가요?
2018 년 9 월에 Microsoft는 Azure Monitor, Log Analytics 및 Application Insights를 단일 서비스로 결합 하 여 응용 프로그램 및 응용 프로그램에서 사용 하는 구성 요소에 대한 강력한 종단 간 모니터링 기능을 제공 합니다. Log Analytics 및 Application Insights의 기능은 변경 되지 않습니다. 그러나 새로운 범위를 더 잘 반영 하기 위해 일부 기능을 Azure Monitor 하는 것이 더 나을 수 있습니다. 이제 Log Analytics의 로그 데이터 엔진과 쿼리 언어를 Azure Monitor 로그 라고 합니다. [Azure Monitor 용어 업데이트](terminology.md)를 참조 하세요.

### <a name="what-does-azure-monitor-cost"></a>Azure Monitor 비용은 무엇 인가요?
메트릭 및 활동 로그 컬렉션과 같이 자동으로 설정 되는 Azure Monitor 기능은 무료로 제공 됩니다. 로그 쿼리 및 경고와 같은 다른 기능과 관련 된 비용이 있습니다. 자세한 가격 정보는 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/) 를 참조 하세요.

### <a name="how-do-i-enable-azure-monitor"></a>Azure Monitor 사용 어떻게 할까요??
Azure Monitor는 새 Azure 구독을 만들 때 사용 하도록 설정 되며 [활동 로그](platform/activity-logs-overview.md) 및 플랫폼 [메트릭이](platform/data-platform-metrics.md) 자동으로 수집 됩니다. [진단 설정을](platform/diagnostic-settings.md) 만들어 Azure 리소스 작업에 대한 자세한 정보를 수집 하 고 [모니터링 솔루션](insights/solutions.md) 및 [정보](insights/insights-overview.md) 를 추가 하 여 특정 서비스에 대해 수집 된 데이터에 대한 추가 분석을 제공 합니다. 

### <a name="how-do-i-access-azure-monitor"></a>Azure Monitor 액세스를 어떻게 할까요? 하 시겠습니까?
Azure Portal의 **모니터** 메뉴에서 모든 Azure Monitor 기능 및 데이터에 액세스 합니다. 서로 다른 Azure 서비스에 대한 메뉴의 **모니터링** 섹션에서는 특정 리소스로 필터링 된 데이터를 사용 하 여 동일한 도구에 대한 액세스를 제공 합니다. Azure Monitor 데이터는 CLI, PowerShell 및 REST API를 사용 하는 다양 한 시나리오에도 액세스할 수 있습니다.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Azure Monitor 온-프레미스 버전이 있나요?
아닙니다. Azure Monitor은 많은 양의 데이터를 처리 하 고 저장 하는 확장 가능한 클라우드 서비스 이며, Azure Monitor 온-프레미스 및 다른 클라우드의 리소스를 모니터링할 수 있습니다.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>온-프레미스 리소스를 Azure Monitor 모니터링할 수 있나요?
예, Azure 리소스의 모니터링 데이터를 수집 하는 것 외에도 다른 클라우드 및 온-프레미스의 가상 컴퓨터 및 응용 프로그램에서 데이터를 수집할 수 Azure Monitor. [Azure Monitor에 대한 모니터링 데이터 원본](platform/data-sources.md)을 참조 하세요.

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>System Center Operations Manager와 통합 Azure Monitor?
기존 System Center Operations Manager 관리 그룹을 Azure Monitor에 연결 하 여 에이전트에서 Azure Monitor 로그로 데이터를 수집할 수 있습니다. 이를 통해 로그 쿼리 및 솔루션을 사용 하 여 에이전트에서 수집 된 데이터를 분석할 수 있습니다. 또한 Azure Monitor에 직접 데이터를 보내도록 기존 System Center Operations Manager 에이전트를 구성할 수 있습니다. [Azure Monitor에 Operations Manager 연결을](platform/om-agents.md)참조 하세요.

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure Monitor 사용 하는 IP 주소는 무엇 인가요?
Azure Monitor 액세스 하기 위해 에이전트 및 기타 외부 리소스에 필요한 IP 주소 및 포트 목록을 보려면 [Application Insights 및 Log Analytics에서 사용 하는 ip 주소](app/ip-addresses.md) 를 참조 하세요. 

## <a name="monitoring-data"></a>데이터 모니터링

### <a name="where-does-azure-monitor-get-its-data"></a>어디에서 데이터를 가져올 Azure Monitor 있나요?
Azure Monitor는 Azure 플랫폼과 리소스, 사용자 지정 응용 프로그램 및 가상 머신에서 실행 되는 에이전트의 로그 및 메트릭을 포함 하 여 다양 한 원본에서 데이터를 수집 합니다. Azure Security Center 및 Network Watcher와 같은 기타 서비스는 데이터를 Azure Monitor 데이터를 사용 하 여 분석할 수 있도록 Log Analytics 작업 영역으로 수집 합니다. 로그 나 메트릭에 대한 REST API를 사용 하 여 Azure Monitor에 사용자 지정 데이터를 보낼 수도 있습니다. [Azure Monitor에 대한 모니터링 데이터 원본](platform/data-sources.md)을 참조 하세요.

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure Monitor에서 수집 되는 데이터는 무엇 인가요? 
Azure Monitor는 다양 한 원본의 데이터를 [로그](platform/data-platform-logs.md) 나 [메트릭에](platform/data-platform-metrics.md)수집 합니다. 각 데이터 형식에는 자체의 상대적인 장점이 있으며, 각각은 Azure Monitor의 특정 기능 집합을 지원 합니다. 각 Azure 구독에 대한 단일 메트릭 데이터베이스가 있으며 요구 사항에 따라 로그를 수집 하는 여러 Log Analytics 작업 영역을 만들 수 있습니다. [Azure Monitor 데이터 플랫폼](platform/data-platform.md)을 참조 하세요.

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Azure Monitor에서 수집할 수 있는 최대 데이터 양이 있나요?
수집할 수 있는 메트릭 데이터의 양에는 제한이 없지만이 데이터는 최대 93 일 동안 저장 됩니다. [메트릭 보존을](platform/data-platform-metrics.md#retention-of-metrics)참조 하세요. 수집할 수 있는 로그 데이터의 양에는 제한이 없지만 Log Analytics 작업 영역에 대해 선택한 가격 책정 계층의 영향을 받을 수 있습니다. [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Azure Monitor에 의해 수집 된 데이터에 액세스 어떻게 할까요??
정보 및 솔루션은 Azure Monitor에 저장 된 데이터로 작업 하기 위한 사용자 지정 환경을 제공 합니다. Kusto Query Language (KQL)로 작성 된 로그 쿼리를 사용 하 여 로그 데이터를 직접 사용할 수 있습니다. Azure Portal에서 Log Analytics를 사용 하 여 쿼리를 작성 및 실행 하 고 대화형으로 데이터를 분석할 수 있습니다. 메트릭 탐색기를 사용 하 여 Azure Portal의 메트릭을 분석 합니다. [Azure Monitor에서 로그 데이터 분석](log-query/log-query-overview.md) 및 [Azure 메트릭 탐색기 시작](platform/metrics-getting-started.md)을 참조 하세요.





## <a name="solutions-and-insights"></a>솔루션 및 정보

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure Monitor의 정보는 무엇 인가요?
Insights는 특정 Azure 서비스에 대한 사용자 지정 모니터링 환경을 제공 합니다. Azure Monitor의 다른 기능과 동일한 메트릭과 로그를 사용 하지만 추가 데이터를 수집 하 고 Azure Portal에서 고유한 환경을 제공할 수 있습니다. [Azure Monitor의 정보를](insights/insights-overview.md)참조 하세요.

Azure Portal에서 정보를 보려면 **모니터** 메뉴의 **insights** 섹션 또는 서비스 메뉴의 **모니터링** 섹션을 참조 하세요.

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure Monitor 솔루션 이란?
모니터링 솔루션은 Azure Monitor 기능에 따라 특정 응용 프로그램 또는 서비스를 모니터링 하는 논리 집합으로 패키지 됩니다. Azure Monitor에서 로그 데이터를 수집 하 고 Azure Portal에서 일반적인 환경을 사용 하 여 분석에 대한 로그 쿼리 및 보기를 제공 합니다. [Azure Monitor에서 솔루션 모니터링을](insights/solutions.md)참조 하세요.

Azure Portal에서 솔루션을 보려면 **모니터** 메뉴의 **Insights** 섹션에서 **자세히** 를 클릭 합니다. **추가** 를 클릭 하 여 작업 영역에 솔루션을 추가 합니다.






## <a name="logs"></a>로그

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure Monitor 로그와 Azure 데이터 탐색기 간의 차이점은 무엇 인가요?
Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Monitor 로그는 Azure 데이터 탐색기를 기반으로 구축 되며 약간의 차이가 있는 동일한 Kusto 쿼리 언어 (KQL)를 사용 합니다. [Azure Monitor 로그 쿼리 언어 차이점](log-query/data-explorer-difference.md)을 참조 하세요.

### <a name="how-do-i-retrieve-log-data"></a>로그 데이터를 검색 어떻게 할까요??
KQL (Kusto Query Language)를 사용 하 여 작성 된 로그 쿼리를 사용 하 여 Log Analytics 작업 영역에서 모든 데이터를 검색 합니다. 사용자 고유의 쿼리를 작성 하거나 특정 응용 프로그램 또는 서비스에 대한 로그 쿼리를 포함 하는 솔루션 및 정보를 사용할 수 있습니다. [Azure Monitor의 로그 쿼리 개요](log-query/log-query-overview.md)를 참조 하세요.

### <a name="what-is-a-log-analytics-workspace"></a>Log Analytics 작업 영역이란 무엇인가요?
Azure Monitor에 의해 수집 된 모든 로그 데이터는 Log Analytics 작업 영역에 저장 됩니다. 작업 영역은 기본적으로 다양 한 원본에서 로그 데이터를 수집 하는 컨테이너입니다. 모든 모니터링 데이터에 대한 단일 Log Analytics 작업 영역이 있거나 여러 작업 영역에 대한 요구 사항이 있을 수 있습니다. [Azure Monitor 로그 배포 디자인](platform/design-logs-deployment.md)을 참조 하세요.

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>기존 Log Analytics 작업 영역을 다른 Azure 구독으로 이동할 수 있나요?
리소스 그룹 또는 구독 간에 작업 영역을 이동할 수 있지만 다른 지역으로 이동할 수는 없습니다. [Log Analytics 작업 영역을 다른 구독 또는 리소스 그룹으로 이동을](platform/move-workspace.md)참조 하세요.

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Log Analytics에 쿼리 탐색기 및 저장 단추가 표시 되지 않는 이유는 무엇 인가요?

쿼리 [범위가](log-query/scope.md) 특정 리소스로 설정 된 경우 **쿼리 탐색기**, **저장** 및 **새 경고 규칙** 단추를 사용할 수 없습니다. 경고를 만들고 쿼리를 저장 하거나 로드 하려면 Log Analytics 작업 영역으로 범위가 지정 되어야 합니다. 작업 영역 컨텍스트에서 Log Analytics를 열려면 **Azure Monitor** 메뉴에서 **로그** 를 선택 합니다. 마지막으로 사용한 작업 영역이 선택되지만 다른 작업 영역을 선택할 수 있습니다. [Azure Monitor의 로그 쿼리 범위 및 시간 범위](log-query/scope.md) 를 참조 하세요 Log Analytics

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>오류가 발생 하는 이유는 다음과 같습니다. VM에서 Log Analytics를 열 때 "이 구독에 대한 리소스 공급자 '를 등록 하 여이 쿼리를 사용 하도록 설정 하십시오." 
많은 리소스 공급자가 자동으로 등록 되지만 일부 리소스 공급자를 수동으로 등록 해야 할 수도 있습니다. 등록 범위는 항상 해당 구독입니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요.

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>VM에서 Log Analytics를 열 때 액세스 권한 없음 오류 메시지가 표시 되는 이유는 무엇 인가요? 
VM 로그를 보려면 VM 로그를 저장하는 작업 영역에 대한 읽기 권한을 부여 받아야 합니다. 이런 경우 관리자가 Azure에서 내게 권한을 부여해 줘야 합니다.




## <a name="alerts"></a>경고

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure Monitor 경고는 무엇입니까?
경고는 모니터링 데이터에서 중요한 조건이 발견되면 사전에 알려줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 여러 종류의 경고가 있습니다.

- 메트릭-메트릭 값이 임계값을 초과 합니다.
- 로그 쿼리-정의 된 조건에 일치 하는 로그 쿼리 결과입니다.
- 활동 로그-활동 로그 이벤트가 정의 된 조건과 일치 합니다.
- 웹 테스트-가용성 테스트 일치 정의 조건 결과입니다.


[Microsoft Azure의 경고 개요](platform/alerts-overview.md)를 참조 하세요.


### <a name="what-is-an-action-group"></a>작업 그룹 이란?
작업 그룹은 경고에 의해 트리거될 수 있는 알림 및 작업 모음입니다. 여러 경고에서 단일 작업 그룹을 사용 하 여 일반적인 알림 및 작업 집합을 활용할 수 있습니다. [Azure Portal에서 작업 그룹 만들기 및 관리](platform/action-groups.md)를 참조 하세요.


### <a name="what-is-an-action-rule"></a>작업 규칙 이란?
작업 규칙을 사용 하면 특정 조건과 일치 하는 경고 집합의 동작을 수정할 수 있습니다. 이렇게 하면 유지 관리 기간 동안 경고 작업을 사용 하지 않도록 설정 하는 등의 요구 사항을 수행할 수 있습니다. 경고 규칙에 직접 적용 하는 대신 경고 집합에 작업 그룹을 적용할 수도 있습니다. [작업 규칙](platform/alerts-action-rules.md)을 참조 하세요.


## <a name="agents"></a>에이전트

### <a name="does-azure-monitor-require-an-agent"></a>에이전트가 필요 Azure Monitor?
에이전트는 가상 컴퓨터의 운영 체제 및 워크 로드에서 데이터를 수집 하는 데만 필요 합니다. 가상 머신은 Azure, 다른 클라우드 환경 또는 온-프레미스에 있을 수 있습니다. [Azure Monitor 에이전트 개요](platform/agents-overview.md)를 참조 하세요.


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure Monitor 에이전트의 차이점은 무엇 인가요?
Azure 진단 확장은 Azure virtual machines 용 이며 Azure Monitor 메트릭, Azure Storage, Azure Event Hubs에 대한 데이터를 수집 합니다. Log Analytics 에이전트는 Azure, 다른 클라우드 환경 또는 온-프레미스의 가상 컴퓨터용 이며 Azure Monitor 로그에 데이터를 수집 합니다. 종속성 에이전트에 Log Analytics 에이전트와 수집 된 프로세스 세부 정보 및 종속성이 필요 합니다. [Azure Monitor 에이전트 개요](platform/agents-overview.md)를 참조 하세요.


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>내 에이전트 트래픽이 Express 경로 연결을 사용 하나요?
Azure Monitor에 대한 트래픽은 Microsoft 피어 링 Express 경로 회로를 사용 합니다. 여러 유형의 Express 경로 트래픽에 대한 설명은 [express 경로 설명서](../expressroute/expressroute-faqs.md#supported-services) 를 참조 하세요. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Log Analytics 에이전트가 Azure Monitor와 통신할 수 있는지 확인 하려면 어떻게 해야 하나요?
에이전트 컴퓨터의 제어판에서 **보안 & 설정** **Microsoft Monitoring Agent** 를 선택 합니다. **Azure Log Analytics (OMS)** 탭에서 녹색 확인 표시 아이콘은 에이전트가 Azure Monitor와 통신할 수 있는지 확인 합니다. 노란색 경고 아이콘은 에이전트에 문제가 있음을 의미 합니다. 한 가지 일반적인 이유는 **Microsoft Monitoring Agent** 서비스가 중지 된 것입니다. 서비스 제어 관리자를 사용하여 서비스를 다시 시작합니다.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Log Analytics 에이전트가 Azure Monitor와의 통신을 중지 어떻게 할까요??
Log Analytics에 직접 연결 된 에이전트의 경우 제어판을 열고 **보안 & 설정**, **Microsoft Monitoring Agent**을 선택 합니다. **Azure Log Analytics (OMS)** 탭에서 나열 된 모든 작업 영역을 제거 합니다. System Center Operations Manager에서 Log Analytics 관리 컴퓨터 목록에서 컴퓨터를 제거 합니다. Operations Manager는 Log Analytics에 더 이상 보고하지 않도록 에이전트의 구성을 업데이트합니다. 

### <a name="how-much-data-is-sent-per-agent"></a>에이전트당 얼마나 많은 데이터가 전송되나요?
에이전트당 전송되는 데이터의 양에 따라 달라집니다.

* 사용하도록 설정한 솔루션
* 수집되는 로그 및 성능 카운터 수
* 로그에 있는 데이터 볼륨

자세한 내용은 [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](platform/manage-cost-storage.md) 를 참조 하세요.

WireData 에이전트를 실행할 수 있는 컴퓨터의 경우 다음 쿼리를 사용하여 전송 중인 데이터의 양을 확인합니다.

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Azure Monitor로 데이터를 보낼 때 Microsoft Management Agent (MMA)에서 사용 되는 네트워크 대역폭은 얼마나 되나요?
대역폭은 전송된 데이터 양에 대한 기능입니다. 네트워크를 통해 데이터가 전송되는 동안 데이터가 압축됩니다.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Log Analytics 에이전트의 데이터 수집을 중지 하면 어떻게 알 수 있나요?

[새 로그 경고 만들기](platform/alerts-metric.md)에서 설명한 단계를 사용하여 데이터 수집이 중지될 때 알림을 받을 수 있습니다. 경고 규칙에 대해 다음 설정을 사용 합니다.

- **경고 조건 정의**: Log Analytics 작업 영역을 리소스 대상으로 지정 합니다.
- **경고 기준** 
   - **신호 이름**: *사용자 지정 로그 검색*
   - **검색 쿼리**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **경고 논리**: *결과 수*, **조건이** *보다 큼*, **임계값** *0*
   - **평가 기준**: **기간 (분)** *30*, **빈도 (분)** *10*
- **경고 세부 정보 정의** 
   - **이름**: *데이터 수집이 중지 되었습니다* .
   - **심각도**: *경고*

로그 경고가 조건과 일치 하는 경우 15 분 넘게 하트 비트가 누락 된 경우 알림이 표시 되도록 기존 또는 새 [작업 그룹](platform/action-groups.md) 을 지정 합니다.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure Monitor 에이전트에 대한 방화벽 요구 사항은 무엇 인가요?
방화벽 요구 사항에 대한 자세한 내용은 [네트워크 방화벽 요구 사항](platform/log-analytics-agent.md#network-firewall-requirements)을 참조 하세요.


## <a name="visualizations"></a>시각화

### <a name="why-cant-i-cant-see-view-designer"></a>뷰 디자이너를 볼 수 없는 이유는 무엇입니까?

보기 디자이너는 Log Analytics 작업 영역에서 참가자 권한 이상이 할당 된 사용자 에게만 사용할 수 있습니다.


## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>구성 문제
*다음을 설정하는 데 문제가 있습니다.*

* [.NET 앱](app/asp-net-troubleshoot-no-data.md)
* [이미 실행 중인 앱 모니터링](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure 진단](platform/diagnostics-extension-to-application-insights.md)
* [Java 웹앱](app/java-troubleshoot.md)

*내 서버에서 데이터를 가져오지 않습니다.*

* [방화벽 예외 설정](app/ip-addresses.md)
* [ASP.NET 서버 설정](app/monitor-performance-live-website-now.md)
* [Java 서버 설정](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Application Insights와 같이 사용할 수 있나요...?

* [Azure VM 또는 Azure virtual machine scale set의 IIS 서버에 있는 웹 앱](app/azure-vm-vmss-apps.md)
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

### <a name="how-much-does-it-cost"></a>비용은 얼마인가요?

* Application Insights 리소스에서 **사용량 및 예상 비용 페이지**를 엽니다. 최근 사용 현황에 대한 차트가 있습니다. 원하는 경우 데이터 볼륨 한도를 설정할 수 있습니다.
* [Azure 청구 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)를 열어 모든 리소스에 대한 청구서를 확인합니다.

### <a name="q14"></a>Application Insights에서 내 프로젝트를 어떻게 수정하나요?
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
* (새 프로젝트에만 해당- [기존 프로젝트에 Application Insights을 추가][start]하는 경우이 작업을 수동으로 수행 해야 합니다.) 클라이언트 및 서버 코드에 코드 조각을 삽입 하 여 Application Insights 리소스 ID를 사용 하 여 초기화 합니다. 예를 들어 MVC 앱에서 코드는 마스터 페이지 Views/Shared/\_레이아웃에 삽입 됩니다. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>이전 SDK 버전에서 업그레이드하려면 어떻게 해야 합니까?
사용자의 애플리케이션 유형에 적합한 SDK는 [릴리스 정보](app/release-notes.md)를 참조하세요.

### <a name="update"></a>내 프로젝트에서 데이터를 보내는 Azure 리소스를 변경하려면 어떻게 해야 하나요?
솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에서 기존 또는 새 리소스로 데이터를 보낼 수 있습니다. 업데이트 마법사는 서버 SDK에서 데이터를 전송하는 위치를 결정하는 ApplicationInsights.config의 계측 키를 변경합니다. "모두 업데이트"를 선택 취소하지 않은 한, 웹 페이지에 표시되는 키도 변경됩니다.

### <a name="what-is-status-monitor"></a>상태 모니터란?

웹앱에서 Application Insights를 구성하기 위해 IIS 웹 서버에서 사용할 수 있는 데스크톱 앱입니다. 원격 분석을 수집하지 않으며 앱을 구성하지 않는 경우 중지할 수 있습니다. 

[자세히 알아보기](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>어떤 원격 분석이 Application Insights에서 수집되나요?

서버 웹앱:

* HTTP 요청
* [종속성](app/asp-net-dependencies.md). 호출: SQL Database, 외부 서비스(Azure Cosmos DB, 테이블, Blob Storage 및 큐)에 대한 HTTP 호출. 
* [예외](app/asp-net-exceptions.md) 및 스택 추적.
* [성능 카운터](app/performance-counters.md) - [상태 모니터](app/monitor-performance-live-website-now.md)를 사용 하는 경우 [App Services에 대한 azure 모니터링](app/azure-web-apps.md), [VM 또는 가상 머신 확장 집합에 대한 azure 모니터링](app/azure-vm-vmss-apps.md)또는 [Application Insights collectd writer](app/java-collectd.md)를 사용 합니다.
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

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>도시, 국가/지역 및 기타 지리적 위치 데이터는 어떻게 계산 되나요?

[GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)를 사용하여 웹 클라이언트의 IP 주소(IPv4 또는 IPv6)를 조회합니다.

* 브라우저 원격 분석: 보낸 사람의 IP 주소를 수집합니다.
* 서버 원격 분석: Application Insights 모듈에서 클라이언트 IP 주소를 수집합니다. `X-Forwarded-For`가 설정된 경우에는 수집되지 않습니다.
* IP 주소 및 지리적 위치 데이터를 수집 하는 방법에 대한 자세한 내용은이 [문서](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)를 참조 Application Insights.


`ClientIpHeaderTelemetryInitializer`를 구성하여 다른 헤더에서 IP 주소를 가져올 수 있습니다. 예를 들어 일부 시스템에서는 프록시, 부하 분산 장치 또는 CDN에 의해 `X-Originating-IP`로 이동됩니다. [자세히 알아보기](https://apmtips.com/blog/2016/07/05/client-ip-address/).

[Power BI를 사용](app/export-power-bi.md )하여 요청 원격 분석을 지도에 표시할 수 있습니다.


### <a name="data"></a>데이터가 포털에 얼마나 오래 보존되나요? 안전한가요?
[데이터 보존 및 개인 정보][data]를 살펴보세요.

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>서버 또는 장치가 Azure와의 연결을 잃을 때 응용 프로그램 정보는 어떻게 되나요?

웹 SDK를 비롯 한 모든 Sdk에는 "신뢰할 수 있는 전송" 또는 "강력한 전송"이 포함 됩니다. 서버 또는 장치가 Azure와의 연결이 끊어지면 원격 분석은 파일 시스템 (서버 Sdk) 또는 HTML5 세션 저장소 (웹 SDK)에 [로컬로 저장](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) 됩니다. 수집 서비스에서 "오래 된" (로그의 경우 48 시간, 메트릭에는 30 분)를 고려할 때까지 SDK는 주기적으로이 원격 분석 전송을 다시 시도 합니다. 오래 된 원격 분석이 삭제 됩니다. 로컬 저장소가 꽉 찬 경우와 같은 일부 경우에는 다시 시도가 수행 되지 않습니다.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>개인 데이터를 원격 분석에 보낼 수 있나요?

코드에서 이러한 데이터를 전송하는 경우 가능합니다. 스택 추적의 변수에 개인 데이터가 포함된 경우에도 전송될 수 있습니다. 개발 팀은 개인 데이터가 제대로 처리되도록 위험 평가를 수행해야 합니다. [데이터 보존 및 개인 정보](app/data-retention-privacy.md)에 대해 자세히 알아보세요.

클라이언트 웹 주소의 **모든** 8진수는 지리적 위치 특성을 조회한 후에 항상 0으로 설정됩니다.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>내 계측 키는 내 웹 페이지 원본에서 볼 수 있습니다. 

* 모니터링 솔루션에서 일반적으로 사용됩니다.
* 데이터를 훔치는 데 사용할 수 없습니다.
* 데이터를 기울이거나 경고를 트리거하는 데 사용할 수 있습니다.
* 고객에게 이러한 문제가 발생한 경우는 없었습니다.

다음과 같이 할 수 있습니다.

* 클라이언트 및 서버 데이터에 대해 두 개의 개별 계측 키 (별도의 Application Insights 리소스)를 사용 합니다. 또는
* 서버에서 실행되는 프록시를 작성하고 웹 클라이언트에서 해당 프록시를 통해 데이터를 전송하도록 합니다.

### <a name="post"></a>진단 검색에서 POST 데이터를 어떻게 확인하나요?
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
* 클라이언트 쪽 스크립트가 없으면 [서버에서 쿠키를 설정](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)할 수 있습니다.
* 한 명의 실제 사용자가 특정 사이트를 다른 브라우저에서 사용하거나, in-private/incognito 검색을 통해 사용하거나, 다른 컴퓨터에서 사용하는 경우 두 번 이상 계산됩니다.
* 여러 컴퓨터 및 브라우저에서 로그인한 사용자를 식별하려면 [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users)에 대한 호출을 추가합니다.

### <a name="q17"></a> Application Insights의 모든 기능을 사용하도록 어떻게 설정하나요?
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

[REST API](https://dev.applicationinsights.io/)를 사용하여 [분석](app/analytics.md) 쿼리를 실행합니다.

### <a name="how-can-i-set-an-alert-on-an-event"></a>이벤트에 대한 경고를 설정하려면 어떻게 해야 하나요?

Azure 경고는 메트릭에 대해서만 설정됩니다. 이벤트가 발생할 때마다 값 임계값을 초과하는 사용자 지정 메트릭을 만듭니다. 그런 다음 메트릭에 대해 경고를 설정합니다. 메트릭이 임계값을 초과할 때마다 알림을 받게 됩니다. 초기 값이 높음 또는 낮음 인지 여부에 관계 없이 처음 교차 될 때까지 알림을 받지 않습니다. 항상 몇 분의 대기 시간이 있습니다.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure 웹앱과 Application Insights 간 데이터 전송 요금이 있나요?

* Azure 웹앱이 Application Insights 컬렉션 엔드포인트가 있는 데이터 센터에서 호스트되는 경우 무료입니다. 
* 호스트 데이터 센터에 컬렉션 엔드포인트가 없으면 앱의 원격 분석에 [Azure 발신 요금](https://azure.microsoft.com/pricing/details/bandwidth/)이 부과됩니다.

이 요금은 Application insights 리소스가 호스트되는 위치에 따라 달라지지 않습니다. 엔드포인트의 배포에 따라 달라집니다.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Application Insights 포털에 원격 분석을 보낼 수 있나요?

저희가 제공하는 SDK 및 [SDK API](app/api-custom-events-metrics.md)를 사용하는 것이 좋습니다. 다양한 [플랫폼](app/platforms.md)에 따라 여러 SDK 변형이 있습니다. 이러한 SDK는 버퍼링, 압축, 제한, 다시 시도 등을 처리합니다. 그러나 [수집 스키마](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) 및 [엔드포인트 프로토콜](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)은 공용입니다.

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
> ApplicationIdProvider는 v 2.6.0부터 사용할 수 있습니다.



#### <a name="proxy-passthrough"></a>프록시 통과

프록시 통과는 컴퓨터 수준 또는 응용 프로그램 수준 프록시를 구성 하 여 달성할 수 있습니다.
자세한 내용은 [Defaultproxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)의 dotnet 문서를 참조 하세요.
 
 Web.config 예:
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



## <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor

Microsoft FAQ는 컨테이너용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>ContainerLog 테이블을 쿼리할 때 채운 이미지 및 이름 속성 값이 표시 되지 않습니다.

에이전트 버전 ciprod12042019 이상에서는 수집 된 로그 데이터에 대해 발생 하는 비용을 최소화 하기 위해 모든 로그 줄에 대해 기본적으로 이러한 두 속성이 채워지지 않습니다. 이러한 속성을 포함 하는 테이블을 해당 값으로 쿼리 하는 두 가지 옵션이 있습니다.

#### <a name="option-1"></a>옵션 1 

다른 테이블을 조인 하 여 이러한 속성 값을 결과에 포함 합니다.

ContainerID 속성에 조인 하 여 ```ContainerInventory``` 테이블에서 이미지 및 ImageTag 속성을 포함 하도록 쿼리를 수정 합니다. KubepodInventory 테이블의 ContaineName 필드에서 ContainerID 속성에 조인 하 여 이름 속성 (이전에는 ```ContainerLog``` 테이블에 표시 됨)을 포함할 수 있습니다. 이 옵션은 권장 되는 옵션입니다.

다음 예제는 조인을 사용 하 여 이러한 필드 값을 가져오는 방법을 설명 하는 샘플 상세 쿼리입니다.

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

모든 컨테이너 로그 줄에 대해 이러한 속성에 대해 컬렉션을 다시 사용 하도록 설정 합니다.

쿼리 변경 내용으로 인해 첫 번째 옵션이 편리 하지 않을 경우 [데이터 컬렉션 구성 설정](insights/container-insights-agent-config.md)에 설명 된 대로 에이전트 구성 맵에서 ```log_collection_settings.enrich_container_logs``` 설정을 사용 하도록 설정 하 여 이러한 필드 수집을 다시 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 클러스터의 모든 노드에서 API 서버 호출을 생성 하 여이 보강를 수행 하기 때문에 두 번째 옵션은 노드가 50 이상인 대량 클러스터에는 권장 되지 않습니다. 이 옵션은 수집 된 모든 로그 줄에 대한 데이터 크기도 늘립니다.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Grafana에서 수집 된 메트릭을 볼 수 있나요?

컨테이너 Azure Monitor는 Grafana 대시보드의 Log Analytics 작업 영역에 저장 된 메트릭 보기를 지원 합니다. 사용자 지정 Grafana 대시보드를 시각화 하기 위해 모니터링 되는 클러스터의 추가 데이터를 쿼리 하는 방법을 배우는 데 도움이 되는 Grafana의 [대시보드 리포지토리에서](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) 다운로드할 수 있는 템플릿을 제공 했습니다. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>컨테이너에 대한 Azure Monitor를 사용 하 여 AKS-engine 클러스터를 모니터링할 수 있나요?

컨테이너 Azure Monitor는 Azure에서 호스트 되는 AKS (이전의 ACS 엔진) 클러스터에 배포 된 컨테이너 작업을 모니터링 하도록 지원 합니다. 이 시나리오에 대한 모니터링을 사용 하도록 설정 하는 데 필요한 단계에 대한 자세한 내용과 개요는 [AKS의 컨테이너에 Azure Monitor 사용](https://github.com/microsoft/OMS-docker/tree/aks-engine)을 참조 하세요.

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>내 Log Analytics 작업 영역에 데이터가 표시 되지 않는 이유는 무엇 인가요?

매일 특정 시간에 Log Analytics 작업 영역에서 데이터를 볼 수 없는 경우 매일 수집할 데이터의 양을 제어 하기 위해 지정 된 기본 500 MB 제한 또는 일일 상한에 도달 했을 수 있습니다. 해당 일에 대한 제한이 충족 되 면 데이터 수집은 다음 날에만 중지 하 고 다시 시작 합니다. 예상 사용 패턴을 기준으로 데이터 사용량을 검토 하 고 다른 가격 책정 계층으로 업데이트 하려면 [로그 데이터 사용량 및 비용](platform/manage-cost-storage.md)을 참조 하세요. 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 테이블에 지정 된 컨테이너 상태는 무엇 인가요?

ContainerInventory 테이블에는 중지된 컨테이너와 실행 중인 컨테이너 둘 다에 대한 정보가 포함되어 있습니다. 이 테이블은 Docker에서 모든 컨테이너(실행 중 및 중지)를 쿼리하고 해당 데이터를 Log Analytics 작업 영역에 전달하는 에이전트 내 워크플로로 채워집니다.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>누락 된 *구독 등록* 오류를 해결 어떻게 할까요? 있습니까?

**Microsoft.operationsmanagement에 대한 구독 등록 누락**오류가 표시 되 면 작업 영역이 정의 된 구독에서 리소스 공급자 **microsoft.operationsmanagement** 를 등록 하 여 해결할 수 있습니다. 이 작업을 수행하는 방법에 대한 설명서는 [여기](../azure-resource-manager/templates/error-register-resource-provider.md)에서 찾을 수 있습니다.

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>RBAC 사용 AKS 클러스터에 대한 지원이 있나요?

컨테이너 모니터링 솔루션은 RBAC를 지원 하지 않지만 컨테이너에 대한 Azure Monitor에서 지원 됩니다. 솔루션 세부 정보 페이지에 이러한 클러스터에 대한 데이터를 표시하는 블레이드에 올바른 정보가 표시되지 않을 수 있습니다.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Helm을 통해 kube-system 네임스페이스의 컨테이너에 대해 로그 수집을 사용하도록 설정하려면 어떻게 하나요?

kube-system 네임스페이스에 포함된 컨테이너의 로그 수집은 기본적으로 사용하지 않도록 설정됩니다. omsagent에 환경 변수를 설정하여 로그 수집을 사용하도록 설정할 수 있습니다. 자세한 내용은 [컨테이너 GitHub에 대한 Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) 페이지를 참조 하세요. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>omsagent를 최신 릴리스 버전으로 업데이트하려면 어떻게 하나요?

에이전트를 업그레이드하는 방법을 알아보려면 [에이전트 관리](insights/container-insights-manage-agent.md)를 참조하세요.

### <a name="how-do-i-enable-multi-line-logging"></a>여러 줄 로깅을 사용하도록 설정하려면 어떻게 하나요?

현재 컨테이너용 Azure Monitor는 여러 줄 로깅을 지원하지 않지만 해결 방법이 있습니다. JSON 형식으로 쓰도록 모든 서비스를 구성하면 Docker/Moby가 해당 데이터를 한 줄에 씁니다.

예를 들어, 샘플 node.js 애플리케이션에 대한 아래 예제와 같이 로그를 JSON 개체로 래핑할 수 있습니다.

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

이 데이터는 쿼리를 수행 하는 경우 로그에 대한 Azure Monitor에서 다음 예제와 같이 표시 됩니다.

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

문제에 대한 자세한 내용을 보려면 다음 [GitHub 링크](https://github.com/moby/moby/issues/22920)를 검토 하세요.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>라이브 로그를 사용 하도록 설정할 때 Azure AD 오류를 해결할 어떻게 할까요? 있나요? 

다음 오류가 표시 될 수 있습니다. **요청에 지정 된 회신 url이 응용 프로그램에 대해 구성 된 회신 url (' < 응용 프로그램 ID\>')과 일치 하지**않습니다. 이 문제를 해결 하는 [방법은 컨테이너에 대한 Azure Monitor를 사용 하 여 실시간으로 컨테이너 데이터를 보는 방법](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)문서에서 찾을 수 있습니다. 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>온 보 딩 후 클러스터를 업그레이드할 수 없는 이유는 무엇입니까?

AKS 클러스터의 컨테이너에 대한 Azure Monitor를 사용 하도록 설정한 후 클러스터를 업그레이드 하려고 할 때 클러스터가 데이터를 전송 하는 Log Analytics 작업 영역을 삭제 하면 오류가 발생 합니다. 이 문제를 해결 하려면 모니터링을 사용 하지 않도록 설정한 다음 구독에서 다른 유효한 작업 영역을 참조 하 여 다시 사용 하도록 설정 해야 합니다. 클러스터 업그레이드를 다시 수행 하려고 하면 성공적으로 처리 되 고 완료 됩니다.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>에이전트에 대해 열기/허용 목록 해야 하는 포트 및 도메인은 무엇 인가요?

Azure, Azure 미국 정부 및 Azure 중국 21Vianet 클라우드를 사용 하 여 컨테이너 화 된 에이전트에 필요한 프록시 및 방화벽 구성 정보는 [네트워크 방화벽 요구 사항](insights/container-insights-onboard.md#network-firewall-requirements) 을 참조 하세요.

## <a name="azure-monitor-for-vms-preview"></a>VM용 Azure Monitor (미리 보기)
Microsoft FAQ는 VM용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

### <a name="can-i-onboard-to-an-existing-workspace"></a>기존 작업 영역에 온보딩할 수 있나요?
가상 머신이 Log Analytics 작업 영역에 이미 연결된 경우 VM용 Azure Monitor에 온보딩되면 해당 작업 영역을 계속 사용하도록 [여기](insights/vminsights-enable-overview.md#prerequisites)에서 나열된 지원되는 지역 중 하나에서 제공될 수 있습니다.

온보딩하면 VM용 Azure Monitor에서 표시하고 분석할 이 정보의 수집을 시작하기 위해 모든 VM이 작업 영역에 데이터를 보고하게 되는 작업 영역에 대한 성능 카운터를 구성합니다.  결과적으로, 선택된 작업 영역에 연결된 VM의 모든 성능 데이터가 표시됩니다.  상태 및 맵 기능은 온보딩하도록 지정된 VM에 대해서만 활성화됩니다.

사용 가능한 성능 카운터에 대한 자세한 내용은 [사용 개요](insights/vminsights-enable-overview.md#performance-counters-enabled) 문서를 참조 하세요.

### <a name="can-i-onboard-to-a-new-workspace"></a>새 작업 영역에 온보딩할 수 있나요? 
현재 VM이 기존 Log Analytics 작업 영역에 연결되지 않은 경우 데이터를 저장할 새 작업 영역을 만들어야 합니다. Azure Portal을 통해 VM용 Azure Monitor에 대해 단일 Azure VM을 구성한 경우 새 기본 작업 영역을 만드는 작업이 자동으로 수행됩니다.

스크립트 기반 방법을 사용 하도록 선택 하는 경우 이러한 단계는 [Azure PowerShell 또는 리소스 관리자 템플릿 사용 VM용 Azure Monitor (미리 보기)](insights/vminsights-enable-at-scale-powershell.md) 사용 문서에서 설명 합니다. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>내 VM에서 이미 기존 작업 영역에 보고하는 경우 어떻게 해야 하나요?
이미 가상 머신의 데이터를 수집하는 경우 기존 Log Analytics 작업 영역에 데이터를 보고하도록 이미 구성되었을 수 있습니다.  해당 작업 영역이 지원되는 지역 중 하나라면 기존 작업 영역에 VM용 Azure Monitor를 사용하도록 설정할 수 있습니다.  이미 사용 중인 작업 영역이 지원되는 지역 중 하나가 아닌 경우 이번에는 VM용 Azure Monitor에 온보딩할 수 없습니다.  본사에서는 추가 지역을 지원하기 위해 적극적으로 노력하고 있습니다.

>[!NOTE]
>VM용 Azure Monitor에 온보딩하도록 선택했는지 여부와 상관 없이 작업 영역에 보고는 모든 VM에 영향을 주는 작업 영역에 대한 성능 카운터를 구성합니다. 작업 영역에 대해 성능 카운터를 구성하는 방법에 대한 자세한 내용은 [설명서](platform/data-sources-performance-counters.md)를 참조하세요. VM용 Azure Monitor에 대해 구성 된 카운터에 대한 자세한 내용은 [VM용 Azure Monitor 사용](insights/vminsights-enable-overview.md#performance-counters-enabled) 문서를 참조 하세요.  

### <a name="why-did-my-vm-fail-to-onboard"></a>내 VM이 온보딩하지 못한 이유는?
Azure Portal에서 Azure VM을 온보딩한 경우 다음 단계가 수행됩니다.

* 해당 옵션을 선택한 경우 기본 Log Analytics 작업 영역이 만들어집니다.
* 선택한 작업 영역에 대한 성능 카운터가 구성됩니다. 이 단계가 실패하면 일부 성능 차트 및 표에서는 온보딩한 VM에 대한 데이터를 표시하지 않습니다. [여기](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters)에 문서화된 PowerShell 스크립트를 실행하여 이 문제를 해결할 수 있습니다.
* 필요한 경우 Log Analytics 에이전트는 VM 확장을 사용하여 설치됩니다.  
* 필요한 경우 VM용 Azure Monitor 맵 종속성 에이전트는 VM 확장을 사용하여 Azure VM에 설치됩니다.  
* 필요한 경우 Azure Monitor 구성 요소가 상태 기능을 지원하도록 구성하고, VM이 상태 데이터를 보고하도록 구성합니다.

온보딩 프로세스 중에 포털에서 알림 상태를 반환하는 위의 각 상태에 대해 확인합니다. 작업 영역 및 에이전트 설치의 구성에는 일반적으로 5~10분 정도가 걸립니다. 포털에서 모니터링 및 상태 데이터를 보려면 5~10분이 더 걸립니다.  

온보딩을 시작하고 VM이 온보딩되어야 함을 나타내는 메시지가 표시된 경우 VM이 프로세스를 완료하려면 최대 30분이 허용됩니다. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>VM용 Azure Monitor만 사용하도록 설정했는데 상태 기능에서 모니터링되는 모든 VM이 표시되는 이유는 무엇인가요?
상태 기능은 단일 VM에서 작업이 시작된 경우에도 Log Analytics 작업 영역에 연결된 모든 VM에 사용하도록 설정됩니다.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>상태 조건이 상황을 평가하게 되는 일정을 수정할 수 있나요?
아니요. 이 릴리스에서는 상태 조건의 기간 및 빈도를 수정할 수 없습니다. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>모니터링할 필요가 없는 상황에서 상태 조건을 사용하지 않도록 설정할 수 있나요?
이 릴리스에서는 상태 조건을 사용하지 않도록 설정할 수 없습니다.

### <a name="are-the-health-alert-severities-configurable"></a>상태 경고 심각도를 구성할 수 있나요?  
상태 경고 심각도는 수정할 수 없으며, 사용하거나 사용하지 않도록 설정할 수만 있습니다. 또한 일부 경고 심각도는 상태 조건의 상태에 따라 업데이트됩니다. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>특정 상태 조건의 설정을 다시 구성하는 경우 특정 인스턴스로 범위를 지정할 수 있나요?  
상태 조건 인스턴스의 설정을 수정하면 Azure VM에서 동일한 유형의 모든 상태 조건 인스턴스가 수정됩니다. 예를 들어 C: 논리 디스크에 해당하는 사용 가능한 디스크 공간 상태 조건 인스턴스의 임계값을 수정할 경우 이 임계값은 동일한 VM에 대해 검색 및 모니터링되는 다른 모든 논리 디스크에 적용됩니다.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>상태 기능이 논리 프로세서 및 코어를 모니터링하나요?
아니요. 개별 프로세서 및 논리적 프로세서 수준 상태 조건은 Windows에 포함되지 않으며, Azure VM에 사용할 수 있는 논리 CPU의 총 수에 따라 CPU 압력을 효과적으로 평가하기 위해 기본적으로 총 CPU 사용률만 모니터링됩니다. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>모든 상태 조건 임계값을 구성할 수 있나요?  
상태가 *실행 중* 또는 *사용 가능*으로 설정되어 있는 Windows VM을 대상으로 하는 상태 조건의 임계값은 수정할 수 없습니다. [워크로드 모니터 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)에서 상태를 쿼리할 때 다음과 같은 경우 서비스 또는 엔터티에 대해 *comparisonOperator* 값 **LessThan** 또는 **GreaterThan**과 *threshold* 값 **4**가 표시됩니다.
   - DNS 클라이언트 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - DHCP 클라이언트 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - RPC 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - Windows 방화벽 서비스 상태 – 서비스가 실행 중이 아닙니다.
   - Windows 이벤트 로그 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - 서버 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - Windows 원격 관리 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - 파일 시스템 오류 또는 손상 - 논리 디스크를 사용할 수 없습니다.

상태가 이미 *true*로 설정되어 있는 다음 Linux 상태 조건의 임계값은 수정할 수 없습니다. 워크로드 모니터링 API에서 엔터티에 대해 쿼리할 때 컨텍스트에 따라 상태에 *comparisonOperator* 값 **LessThan** 및 *threshold* 값 **1**이 표시됩니다.
   - 논리 디스크 상태 - 논리 디스크가 온라인/사용 가능 상태가 아님
   - 디스크 상태 - 디스크가 온라인/사용 가능 상태가 아님
   - 네트워크 어댑터 상태 - 네트워크 어댑터가 사용 안 함임

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>상태 기능에 포함된 경고를 수정하려면 어떻게 해야 하나요?
각 상태 조건에 대해 정의된 경고 규칙은 Azure Portal에 표시되지 않습니다. [워크로드 모니터 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)에서만 상태 경고 규칙을 사용하거나 사용하지 않도록 설정할 수 있습니다. 또한 Azure Portal에서는 상태 경고에 대해 [Azure Monitor 작업 그룹](platform/action-groups.md)을 할당할 수 없습니다. 오직 알림 설정 API를 사용하여 상태 경고가 발생할 때마다 트리거되도록 작업 그룹을 구성할 수 있습니다. 현재, VM에 대해 발생한 모든 *상태 경고*가 동일한 작업 그룹을 트리거하도록 VM에 대해 작업 그룹을 할당할 수 있습니다. 기존의 Azure 경고와 달리, 각 상태 경고 규칙에 대한 별도의 작업 그룹이라는 개념이 없습니다. 또한 상태 경고가 트리거될 때 메일 또는 SMS 알림을 제공하도록 구성된 작업 그룹만 지원됩니다. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>내 VM의 성능 차트에 일부 또는 모든 데이터가 표시되지 않습니다
디스크 테이블 또는 일부 성능 차트에 성능 데이터가 표시되지 않는 경우 작업 영역에서 성능 카운터를 구성할 수 없습니다. 이 문제를 해결하려면 다음 [PowerShell 스크립트](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)를 실행합니다.

### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>VM용 Azure Monitor 맵 기능과 서비스 맵이 어떻게 다른가요?
VM용 Azure Monitor 맵 기능은 서비스 맵을 기반으로 하지만 다음과 같은 차이점이 있습니다.

* 맵 보기는 VM 블레이드 및 Azure Monitor의 VM용 Azure Monitor에서 액세스할 수 있습니다.
* 이제 맵에서 연결은 클릭 가능하고 선택한 연결에 대한 사이드 패널에서 연결 메트릭 데이터 보기를 표시합니다.
* 더 복잡한 맵을 지원하기 위해 맵을 만드는 데 사용되는 새 API가 있습니다.
* 이제 모니터링된 VM은 클라이언트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 가상 머신 및 모니터링되지 않는 가상 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 이제 모니터링된 가상 머신은 서버 포트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 머신 및 모니터링되지 않는 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 맵 스타일은 Application insights에서 앱 맵을 사용하여 더욱 일관되게 업데이트되었습니다.
* 측면 패널이 업데이트 되었으며 서비스 맵 업데이트 관리, 변경 내용 추적, 보안 및 서비스 데스크에서 지원 되는 통합의 전체 집합이 없습니다. 
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

Azure VM의 개요 페이지에는 게스트 VM에서 작업의 호스트 측정값에 따라 차트가 표시됩니다.  Azure VM 개요에 대한 네트워크 차트의 경우 청구되는 네트워크 트래픽만 표시합니다.  여기에는 가상 네트워크 간 트래픽이 포함 되지 않습니다.  VM용 Azure Monitor에 대해 표시 되는 데이터와 차트는 게스트 VM의 데이터를 기반으로 하며, 네트워크 차트는 가상 네트워크 간을 포함 하 여 해당 VM에 대한 인바운드 및 아웃 바운드 인 모든 TCP/IP 트래픽을 표시 합니다.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>VMConnection에 저장 되 고 연결 패널 및 통합 문서에 표시 되는 데이터에 대한 응답 시간은 어떻게 측정 되나요?

응답 시간은 근사값입니다. 응용 프로그램 코드를 계측 하지 않으므로 요청이 시작 되는 시기와 응답이 도착할 때를 알 수 없습니다. 대신 연결에서 전송 되는 데이터를 관찰 한 다음 해당 연결에서 데이터를 다시 가져올 것입니다. 에이전트는 이러한 송신 및 수신을 추적 하 고 쌍으로 연결을 시도 합니다. 즉, 일련의 전송 후 일련의 수신이 요청/응답 쌍으로 해석 됩니다. 이러한 작업 간의 타이밍은 응답 시간입니다. 여기에는 네트워크 대기 시간 및 서버 처리 시간이 포함 됩니다.

이러한 근사값은 요청/응답을 기반으로 하는 프로토콜에 대해 효과적으로 작동 합니다. 단, 단일 요청은 연결에서 수행 되 고 단일 응답은 도착 합니다. 이는 HTTP (S) (파이프라인 제외)의 경우 이지만 다른 프로토콜에는 충족 되지 않습니다.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Log Analytics 무료 가격 플랜을 사용하고 있는 경우 제한 사항이 있나요?
*무료* 가격 책정 계층을 사용하여 Log Analytics 작업 영역에 Azure Monitor를 구성한 경우 VM용 Azure Monitor 맵 기능은 5대의 작업 영역에 연결된 머신만 지원합니다. 무료 작업 영역에 5대의 VM이 연결되어 있는 경우 VM 중 하나의 연결을 끊은 후 나중에 새 VM을 연결하면 맵 페이지에서 새 VM이 모니터링 및 반영되지 않습니다.  

따라서 **Insights(미리 보기)** 가 VM에 이미 설치된 후라도 해당 VM을 열고 왼쪽 창에서 Insights(미리 보기)를 선택하면 **지금 사용해 보기** 옵션이 포함된 메시지가 표시됩니다.  그러나 이 VM이 VM용 Azure Monitor에 등록되지 않은 경우에는 일반적인 경우처럼 옵션이 포함된 메시지가 표시되지 않습니다. 


## <a name="next-steps"></a>다음 단계
여기에서 질문에 대답할 수 없는 경우 다음 포럼에서 추가 질문과 대답을 참조할 수 있습니다.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Azure Monitor에 대한 일반적인 피드백은 [사용자 의견 포럼](https://feedback.azure.com/forums/34192--general-feedback)을 참조 하세요.
