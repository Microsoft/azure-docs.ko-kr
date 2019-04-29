---
title: Azure Monitor의 로그 | Microsoft Docs
description: 데이터 모니터링의 고급 분석에 사용 되는 Azure Monitor의 로그를 설명 합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: ec037b16840afe669ac3934beaa832f850cdcfb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809117"
---
# <a name="logs-in-azure-monitor"></a>Azure Monitor의 로그

> [!NOTE]
> Azure Monitor에서 수집한 모든 데이터는 두 가지 기본 형식, 메트릭 및 로그 중 하나에 적합 합니다. 이 문서에서는 로그를 설명 합니다. 가리킵니다 [Azure Monitor의 메트릭](data-platform-metrics.md) 메트릭의 및에 대 한 자세한 설명은 [Azure Monitor에서 수집한 모니터링 데이터](data-platform.md) 두 비교 합니다.

Azure Monitor의 로그는 다양 한 원본의 데이터 간 복잡 한 분석을 수행 하는 데 특히 유용 합니다. 이 문서에서는 로그 데이터와 함께 수행할 수 있는 작업, Azure Monitor에서 구조와 로그에 데이터를 저장 하는 다른 데이터 원본을 식별 하는 방법을 설명 합니다.

> [!NOTE]
> Azure Monitor 로그 및 Azure에서 로그 데이터의 원본을 구별 하는 것이 반드시 합니다. 예를 들어, Azure에서 구독 수준 이벤트는 쓸를 [활동 로그](activity-logs-overview.md) Azure Monitor 메뉴에서 확인할 수 있습니다. 대부분의 리소스에 작업 정보를 기록 합니다는 [진단 로그](diagnostic-logs-overview.md) 는 서로 다른 위치에 전달할 수 있습니다. Azure Monitor 로그는 활동 로그 및 리소스 집합 전체에서 심층 분석을 위해 다른 모니터링 데이터와 함께 진단 로그를 수집 하는 로그 데이터 플랫폼입니다.

## <a name="what-are-azure-monitor-logs"></a>Azure Monitor 로그 란?

Azure Monitor의 로그에는 여러 가지 다른 각 형식에 대 한 속성 집합이 포함 된 레코드로 구성 된 데이터 포함 됩니다. 로그는 Azure Monitor 메트릭 같은 숫자 값을 포함할 수 있지만 일반적으로 자세한 설명이 포함 된 텍스트 데이터를 포함 합니다. 추가로 다릅니다 메트릭 데이터에서는 해당 구조에 따라 다양 하며 일정 한 간격은 수집 하지 않은 경우가 많습니다. 원격 분석 이벤트 및 추적과 같은 저장 된 Azure Monitor 로그 성능 데이터 외에도 모든 될 수 있도록 결합할 분석 합니다.

로그 항목의 일반적인 형식은 산발적으로 수집 되는 이벤트입니다. 이벤트는 응용 프로그램 또는 서비스에서 생성 되 고 일반적으로 자체적으로 완전 한 컨텍스트를 제공할 수 있는 충분 한 정보가 포함 됩니다. 예를 들어, 이벤트는 특정 리소스가 생성 또는 수정되었거나, 트래픽 증가에 대한 응답으로 새 호스트가 시작되었거나, 애플리케이션에서 오류가 검색되었음을 나타낼 수 있습니다.

 데이터의 형식은 다양할 수 있으므로 애플리케이션은 필요한 구조를 사용하여 사용자 지정 로그를 만들 수 있습니다. 메트릭 데이터를 다른 데이터 분석 및 추세에 대 한 기타 모니터링 데이터를 결합 하는 로그에도 저장할 수 있습니다.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 수행할 수 있습니까?
다음 표에서 Azure Monitor의 로그를 사용할 수 있는 다양 한 방법을 나열 합니다.


|  |  |
|:---|:---|
| 분석 | 사용 하 여 [Log Analytics](../log-query/get-started-portal.md) 쓸 Azure portal에서 [로그 쿼리](../log-query/log-query-overview.md) 및 대화형으로 강력한 데이터 탐색기 분석 엔진을 사용 하 여 로그 데이터를 분석 합니다.<br>사용 된 [Application Insights analytics 콘솔](../app/analytics.md) 로그 쿼리를 작성 하 여 대화형으로 Application Insights에서 로그 데이터를 분석 하는 Azure 포털에서. |
| 시각화 | 고정 테이블 또는 차트를 렌더링 하는 쿼리 결과 [Azure 대시보드에](../../azure-portal/azure-portal-dashboards.md)합니다.<br>만들기는 [통합 문서](../app/usage-workbooks.md) 여러 대화형 보고서에서 데이터 집합으로 결합 합니다. <br>쿼리의 결과를 [Power BI](powerbi.md)로 내보내 서로 다른 시각화를 사용하고 Azure 외부의 사용자와 공유합니다.<br>쿼리 결과 내보냅니다 [Grafana](grafana-plugin.md) 해당 대시보드를 활용 하 여 다른 데이터 원본으로 결합 합니다.|
| 경고 | 쿼리의 결과가 특정 결과와 일치할 때 알림을 보내거나 [자동화된 작업](action-groups.md)을 수행하는 [로그 경고 규칙](alerts-log.md)을 구성합니다.<br>구성 된 [메트릭 경고 규칙](alerts-metric-logs.md) 메트릭으로 추출 된 특정 로그 데이터 로그 합니다. |
| 장치 | 사용 하 여 명령줄에서 로그 쿼리 결과 액세스할 [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)합니다.<br>사용 하 여 명령줄에서 로그 쿼리 결과 액세스할 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)합니다.<br>사용 하 여 사용자 지정 응용 프로그램에서 로그 쿼리 결과 액세스할 [REST API](https://dev.loganalytics.io/)합니다. |
| 내보내기 | 로그 데이터를 검색 하 여 외부 위치에 복사 하는 워크플로 만들 [Logic Apps](~/articles/logic-apps/index.yml)합니다. |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure Monitor 로그 구조화 된 데이터를 어떻게 되나요?
Azure Monitor 로그에서 수집 된 데이터에 저장 되는 [Log Analytics 작업 영역](../platform/manage-access.md)합니다. 할 수 있습니다 [여러 작업 영역을 만들고](manage-access.md#determine-the-number-of-workspaces-you-need) 다양 한 로그 데이터 집합을 관리 하기 위해 구독에서. 각 작업 영역은 각 특정 원본에서 데이터를 저장 하는 여러 테이블을 포함 합니다. 모든 테이블을 공유 하는 동안 [몇 가지 일반적인 속성](log-standard-properties.md)를 저장 하는 데이터의 종류에 따라 속성의 고유한 집합이 각 합니다. 새 작업 영역을 테이블의 표준 집합 있고 다양 한 모니터링 솔루션 및 작업 영역에 작성 하는 다른 서비스에서 테이블을 더 추가 됩니다.

Application Insights에서 로그 데이터는 작업 영역을 동일한 Log Analytics 엔진을 사용 하지만 각 모니터링된 응용 프로그램에 대해 개별적으로 저장 됩니다. 각 응용 프로그램에 응용 프로그램 요청, 예외 및 페이지 보기와 같은 데이터를 저장할 테이블의 표준 집합입니다.

로그 쿼리를 Application Insights 응용 프로그램 또는 Log Analytics 작업 영역에서 데이터 사용 됩니다. 사용할 수는 [리소스 간 쿼리](../log-query/cross-workspace-query.md) 다른 로그 데이터와 함께 응용 프로그램 데이터를 분석 하거나 여러 작업 영역 또는 응용 프로그램을 포함 하 여 쿼리를 만들 수 있습니다.

![작업 영역](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>로그 쿼리
Azure Monitor 로그에서 데이터를 사용 하 여 검색 됩니다는 [로그 쿼리의](../log-query/log-query-overview.md) 로 작성 된 합니다 [Kusto 쿼리 언어](../log-query/get-started-queries.md), 신속 하 게 검색을 통합 하 고 수집 된 데이터를 분석할 수 있습니다. 사용 하 여 [Log Analytics](../log-query/portals.md) 작성 및 Azure portal에서 로그 쿼리를 테스트 합니다. 결과 대화형으로 사용 하거나 다른 시각화 요소를 사용 하 여 보려는 대시보드에 고정할 수 있습니다.

![Log Analytics](media/data-platform-logs/log-analytics.png)

오픈 [Application Insights에서 Log Analytics](../app/analytics.md) Application Insights 데이터를 분석 합니다.

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

사용 하 여 로그 데이터를 검색할 수도 있습니다는 [Log Analytics API](https://dev.loganalytics.io/documentation/overview) 하며 [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)합니다.


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor 로그 원본
Azure Monitor는 Azure 내와 온-프레미스 리소스의 다양한 원본에서 로그 데이터를 수집할 수 있습니다. 다음 표에서 Azure Monitor 로그에 데이터를 기록 하는 다른 리소스에서 사용할 수 있는 다른 데이터 원본을 보여 줍니다. 각각에 필요한 모든 구성에 세부 정보에 대 한 링크입니다.

### <a name="azure-tenant-and-subscription"></a>Azure 테 넌 트 및 구독

| Data | 설명 |
|:---|:---|
| Azure Active Directory 감사 로그 | 각 디렉터리에 대 한 진단 설정을 통해 구성 됩니다. 참조 [Azure Monitor 로그를 사용 하 여 Azure AD 통합 로그](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)합니다.  |
| 활동 로그 | 기본적으로 개별적으로 저장 하 고 근 실시간 경고에 사용할 수 있습니다. Log Analytics 작업 영역에 쓸 Activity Log Analytics 솔루션을 설치 합니다. 참조 [Log Analytics에서 Azure 활동 로그 수집 및 분석](collect-activity-logs.md)합니다. |

### <a name="azure-resources"></a>Azure 리소스

| Data | 설명 |
|:---|:---|
| 리소스 진단 | Log Analytics 작업 영역에 대 한 메트릭을 비롯 하 여 진단 데이터를 쓸 진단 설정을 구성 합니다. 참조 [Log Analytics로 Azure 진단 로그를 Stream](diagnostic-logs-stream-log-store.md)합니다. |
| 모니터링 솔루션 | 모니터링 솔루션 Log Analytics 작업 영역에이 솔루션은 수집한 데이터를 씁니다. 참조 [Azure의 관리 솔루션에 대 한 데이터 수집 세부 정보](../insights/solutions-inventory.md) 솔루션의 목록은 합니다. 참조 [Azure Monitor에서 솔루션 모니터링](../insights/solutions.md) 설치 하 고 솔루션을 사용 하 여 대 한 자세한 내용은 합니다. |
| 메트릭 | 플랫폼 메트릭을 Azure Monitor 리소스에 대 한 로그 데이터를 장기간 보존을 사용 하 여 다른 데이터 형식을 사용 하 여 복잡 한 분석을 수행 하는 Log Analytics 작업 영역으로 보낼 합니다 [Kusto 쿼리 언어](/azure/kusto/query/)합니다. 참조 [Log Analytics로 Azure 진단 로그를 Stream](diagnostic-logs-stream-log-store.md)합니다. |
| Azure Table Storage | 일부 Azure 리소스를 작성할 수 있는 Azure storage에서 데이터를 수집할 데이터를 모니터링 합니다. 참조 [Log Analytics를 사용 하 여 이벤트에 대 한 IIS와 Azure table storage에 대 한 사용 하 여 Azure blob storage](azure-storage-iis-table.md)합니다. |

### <a name="virtual-machines"></a>Virtual Machines

| Data | 설명 |
|:---|:---|
|  에이전트 데이터 원본 | 수집 된 데이터 원본 [Windows](agent-windows.md) 하 고 [Linux](../learn/quick-collect-linux-computer.md) 에이전트 이벤트, 성능 데이터 및 사용자 지정 로그를 포함 합니다. 참조 [Azure Monitor의 데이터 원본 에이전트](data-sources.md) 목록은 데이터 원본 및 구성에 대 한 세부 정보입니다. |
| 모니터링 솔루션 | 모니터링 솔루션 Log Analytics 작업 영역에 에이전트에서 수집한 데이터를 씁니다. 참조 [Azure의 관리 솔루션에 대 한 데이터 수집 세부 정보](../insights/solutions-inventory.md) 솔루션의 목록은 합니다. 참조 [Azure Monitor에서 솔루션 모니터링](../insights/solutions.md) 설치 하 고 솔루션을 사용 하 여 대 한 자세한 내용은 합니다. |
| System Center Operations Manager | 온-프레미스 에이전트에서 로그에 이벤트 및 성능 데이터를 수집 하려면 Azure Monitor에 Operations Manager 관리 그룹을 연결 합니다. 참조 [Log Analytics에 Operations Manager 연결](om-agents.md) 이 구성에 대 한 자세한 내용은 합니다. |


### <a name="applications"></a>애플리케이션

| Data | 설명 |
|:---|:---|
| 요청 및 예외 | 응용 프로그램 요청 및 예외에 대 한 자세한 데이터를 _요청_를 _pageViews_, 및 _예외_ 테이블입니다. 에 대 한 호출 [외부 구성 요소](../app/asp-net-dependencies.md) 에 _종속성_ 테이블입니다. |
| 사용량 및 성능 | 응용 프로그램의 성능은에서 사용할 수는 _요청_, _browserTimings_ 하 고 _performanceCounters_ 테이블입니다. 에 대 한 데이터 [사용자 지정 메트릭을](../app/api-custom-events-metrics.md#trackevent) 에 _customMetrics_ 테이블입니다.|
| 추적 데이터 | 결과 [분산 추적](../app/distributed-tracing.md) 에 저장 되는 _추적_ 테이블입니다. |
| 가용성 테스트 | 요약 데이터로 [가용성 테스트](../app/monitor-web-app-availability.md) 에 저장 되는 _availabilityResults_ 테이블. 이러한 테스트에서 자세한 데이터로 별도 저장소에 있으며 Azure portal에서 Application Insights에서 액세스 합니다. |

### <a name="insights"></a>자세한 정보

| Data | 설명 |
|:---|:---|
| 컨테이너용 Azure Monitor | 인벤토리 및 성능 데이터를 수집한 [컨테이너에 대 한 Azure Monitor](../insights/container-insights-overview.md)합니다. 참조 [컨테이너 데이터 수집 세부 정보](../insights/container-insights-log-search.md#container-records) 테이블 목록은 합니다. |
| VM용 Azure Monitor | 지도 및 성능 데이터를 수집한 [Vm에 대 한 Azure Monitor](../insights/vminsights-overview.md)합니다. 참조 [Vm에 대 한 Azure Monitor의 로그를 쿼리 하는 방법을](../insights/vminsights-log-search.md) 자세한 방법은이 데이터를 쿼리 합니다. |

### <a name="custom"></a>사용자 지정 

| Data | 설명 |
|:---|:---|
| REST API | 모든 REST 클라이언트에서 Log Analytics 작업 영역에 데이터를 씁니다. 참조 [HTTP 데이터 수집기 API를 사용 하 여 Azure Monitor에 로그 데이터 보내기](data-collector-api.md) 세부 정보에 대 한 합니다.
| 논리 앱 | Log Analytics 작업 영역에 사용 하 여 논리 앱 워크플로에서 데이터를 작성 합니다 **Azure Log Analytics Data Collector** 동작 합니다. |

### <a name="security"></a>보안

| Data | 설명 |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) 다른 로그 데이터를 사용 하 여 분석할 수 있는 Log Analytics 작업 영역에서 수집 하는 데이터를 저장 합니다. 참조 [Azure Security Center에서 데이터 수집](../../security-center/security-center-enable-data-collection.md) 작업 영역 구성에 대 한 자세한 내용은 합니다. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) Log Analytics 작업 영역으로 데이터 원본에서 데이터를 저장 합니다. 참조 [데이터 원본 연결](/azure/sentinel/connect-data-sources)합니다.  |


## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 정보는 [Azure Monitor 데이터 플랫폼](data-platform.md)합니다.
- 에 대 한 자세한 [Azure Monitor의 메트릭](data-platform-metrics.md)합니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
