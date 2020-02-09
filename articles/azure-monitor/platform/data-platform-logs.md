---
title: Azure Monitor의 로그 | Microsoft Docs
description: 모니터링 데이터에 대 한 고급 분석에 사용 되는 Azure Monitor의 로그에 대해 설명 합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 2772535ff5eb7cf1e50c40b8ff075f67e71e1326
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751019"
---
# <a name="logs-in-azure-monitor"></a>Azure Monitor의 로그

> [!NOTE]
> Azure Monitor에서 수집 된 모든 데이터는 메트릭 및 로그의 두 가지 기본 형식 중 하나에 적합 합니다. 이 문서에서는 로그에 대해 설명 합니다. 메트릭에 대 한 자세한 설명 및 [Azure Monitor에 의해 수집 된 데이터](data-platform.md) 를 비교 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 메트릭](data-platform-metrics.md) 을 참조 하세요.

Azure Monitor 로그는 다양 한 원본의 데이터에서 복잡 한 분석을 수행 하는 데 특히 유용 합니다. 이 문서에서는 Azure Monitor에서 로그를 구성 하는 방법, 데이터를 사용 하 여 수행할 수 있는 작업 및 로그에 데이터를 저장 하는 다양 한 데이터 원본을 식별 하는 방법을 설명 합니다.

> [!NOTE]
> Azure에서 Azure Monitor 로그와 로그 데이터의 원본을 구분 하는 것이 중요 합니다. 예를 들어 Azure의 구독 수준 이벤트는 Azure Monitor 메뉴에서 볼 수 있는 [활동 로그](platform-logs-overview.md) 에 기록 됩니다. 대부분의 리소스는 다른 위치로 전달할 수 있는 [리소스 로그](platform-logs-overview.md) 에 작업 정보를 기록 합니다. Azure Monitor 로그는 다른 모니터링 데이터와 함께 활동 로그 및 리소스 로그를 수집 하 여 전체 리소스 집합에 대 한 심층 분석을 제공 하는 로그 데이터 플랫폼입니다.

## <a name="what-are-azure-monitor-logs"></a>Azure Monitor 로그 란?

Azure Monitor의 로그에는 각 형식에 대해 서로 다른 속성 집합을 사용 하 여 레코드로 구성 된 다양 한 종류의 데이터가 포함 됩니다. 로그에는 Azure Monitor 메트릭과 같은 숫자 값이 포함 될 수 있지만 일반적으로 자세한 설명이 포함 된 텍스트 데이터가 들어 있습니다. 이러한 데이터는 구조에 따라 달라 지 며 종종 정기적으로 수집 되지 않는 메트릭 데이터와 다릅니다. 이벤트 및 추적과 같은 원격 분석은 모두 분석을 위해 함께 사용할 수 있도록 성능 데이터 외에도 Azure Monitor 로그에 저장 됩니다.

일반적인 유형의 로그 항목은 산발적으로 수집 되는 이벤트입니다. 이벤트는 응용 프로그램이 나 서비스에 의해 생성 되며 일반적으로 전체 컨텍스트를 제공 하기에 충분 한 정보를 포함 합니다. 예를 들어, 이벤트는 특정 리소스가 생성 또는 수정되었거나, 트래픽 증가에 대한 응답으로 새 호스트가 시작되었거나, 애플리케이션에서 오류가 검색되었음을 나타낼 수 있습니다.

 데이터의 형식은 다양할 수 있으므로 애플리케이션은 필요한 구조를 사용하여 사용자 지정 로그를 만들 수 있습니다. 메트릭 데이터를 로그에 저장 하 여 추세 및 기타 데이터 분석을 위해 다른 모니터링 데이터와 결합할 수도 있습니다.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Monitor 로그로 무엇을 할 수 있나요?
다음 표에서는 Azure Monitor에서 로그를 사용할 수 있는 여러 가지 방법을 보여 줍니다.


|  |  |
|:---|:---|
| 분석 | Azure Portal에서 [Log Analytics](../log-query/get-started-portal.md) 를 사용 하 여 [로그 쿼리](../log-query/log-query-overview.md) 를 작성 하 고 강력한 데이터 탐색기 분석 엔진을 사용 하 여 로그 데이터를 대화형으로 분석할 수 있습니다.<br>Azure Portal에서 [Application Insights analytics 콘솔](../app/analytics.md) 을 사용 하 여 로그 쿼리를 작성 하 고 Application Insights에서 대화형으로 로그 데이터를 분석 합니다. |
| 시각화 | 테이블이 나 차트로 렌더링 되는 쿼리 결과를 [Azure 대시보드에](../../azure-portal/azure-portal-dashboards.md)고정 합니다.<br>대화형 보고서에서 여러 데이터 집합과 결합할 [통합 문서](../app/usage-workbooks.md) 를 만듭니다. <br>쿼리의 결과를 [Power BI](powerbi.md)로 내보내 서로 다른 시각화를 사용하고 Azure 외부의 사용자와 공유합니다.<br>쿼리 결과를 [Grafana](grafana-plugin.md) 로 내보내 해당 일점 탑승를 활용 하 고 다른 데이터 원본과 결합 합니다.|
| 경고 | 쿼리의 결과가 특정 결과와 일치할 때 알림을 보내거나 [자동화된 작업](action-groups.md)을 수행하는 [로그 경고 규칙](alerts-log.md)을 구성합니다.<br>메트릭으로 추출 된 특정 로그 데이터 로그에 대 한 [메트릭 경고 규칙](alerts-metric-logs.md) 을 구성 합니다. |
| 장치 | [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)를 사용 하 여 명령줄에서 로그 쿼리 결과에 액세스 합니다.<br>[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)을 사용 하 여 명령줄에서 로그 쿼리 결과에 액세스 합니다.<br>[REST API](https://dev.loganalytics.io/)를 사용 하 여 사용자 지정 응용 프로그램에서 로그 쿼리 결과에 액세스 합니다. |
| 내보내기 | 로그 데이터를 검색 하 고 [Logic Apps](~/articles/logic-apps/index.yml)를 사용 하 여 외부 위치에 복사 하는 워크플로를 작성 합니다. |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure Monitor 로그의 데이터는 어떻게 구조화 되나요?
Azure Monitor 로그에 의해 수집 된 데이터는 [Log Analytics 작업 영역](../platform/design-logs-deployment.md)에 저장 됩니다. 각 작업 영역에는 각각 특정 원본의 데이터를 저장 하는 여러 테이블이 있습니다. 모든 테이블은 [몇 가지 공통 속성](log-standard-properties.md)을 공유 하지만 저장 하는 데이터의 종류에 따라 각각 고유한 속성 집합이 있습니다. 새 작업 영역에는 표준 테이블 집합이 포함 되 고 다른 모니터링 솔루션 및 작업 영역에 기록 하는 다른 서비스에 의해 추가 테이블이 추가 됩니다.

Application Insights의 로그 데이터는 작업 영역과 동일한 Log Analytics 엔진을 사용 하지만 모니터링 되는 각 응용 프로그램에 대해 별도로 저장 됩니다. 각 응용 프로그램에는 응용 프로그램 요청, 예외 및 페이지 보기와 같은 데이터를 저장 하는 표준 테이블 집합이 있습니다.

로그 쿼리는 Log Analytics 작업 영역 또는 Application Insights 응용 프로그램의 데이터를 사용 합니다. [리소스 간 쿼리](../log-query/cross-workspace-query.md) 를 사용 하 여 다른 로그 데이터와 함께 응용 프로그램 데이터를 분석 하거나 여러 작업 영역 또는 응용 프로그램을 포함 하는 쿼리를 만들 수 있습니다.

![작업 영역](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>로그 쿼리
Azure Monitor 로그의 데이터는 [Kusto 쿼리 언어로](../log-query/get-started-queries.md)작성 된 [로그 쿼리](../log-query/log-query-overview.md) 를 사용 하 여 검색 됩니다. 그러면 수집 된 데이터를 신속 하 게 검색, 통합 및 분석할 수 있습니다. [Log Analytics](../log-query/portals.md) 를 사용 하 여 Azure Portal에서 로그 쿼리를 작성 및 테스트 합니다. 이를 통해 결과를 대화형으로 작업 하거나 대시보드에 고정 하 여 다른 시각화를 사용 하 여 볼 수 있습니다.

![Log Analytics](media/data-platform-logs/log-analytics.png)

[Application Insights에서 Log Analytics](../app/analytics.md) 를 열어 Application Insights 데이터를 분석 합니다.

![Application Insights 분석](media/data-platform-logs/app-insights-analytics.png)

[LOG ANALYTICS API](https://dev.loganalytics.io/documentation/overview) 및 [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)를 사용 하 여 로그 데이터를 검색할 수도 있습니다.


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor 로그의 원본
Azure Monitor는 Azure 내와 온-프레미스 리소스의 다양한 원본에서 로그 데이터를 수집할 수 있습니다. 다음 표에서는 Azure Monitor 로그에 데이터를 쓰는 다양 한 리소스에서 사용할 수 있는 다양 한 데이터 원본을 나열 합니다. 각에는 필요한 모든 구성에 대 한 세부 정보 링크가 있습니다.

### <a name="azure-tenant-and-subscription"></a>Azure 테넌트 및 구독

| 데이터 | Description |
|:---|:---|
| 감사 로그 Azure Active Directory | 각 디렉터리에 대 한 진단 설정을 통해 구성 됩니다. [AZURE AD 로그를 Azure Monitor 로그와 통합을](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)참조 하세요.  |
| 활동 로그 | 기본적으로 개별적으로 저장 되며 거의 실시간으로 경고 하는 데 사용할 수 있습니다. Log Analytics 작업 영역에 쓸 활동 로그 분석 솔루션을 설치 합니다. [Log Analytics에서 Azure 활동 로그 수집 및 분석을](activity-log-collect.md)참조 하세요. |

### <a name="azure-resources"></a>Azure 리소스

| 데이터 | Description |
|:---|:---|
| 리소스 진단 | Log Analytics 작업 영역에 대 한 메트릭을 포함 하 여 진단 데이터에 쓰도록 진단 설정을 구성 합니다. [Log Analytics에 대 한 Azure 리소스 로그 스트리밍을](resource-logs-collect-storage.md)참조 하세요. |
| 모니터링 솔루션 | 모니터링 솔루션은 Log Analytics 작업 영역에 수집 하는 데이터를 작성 합니다. 솔루션 목록은 [Azure의 관리 솔루션에 대 한 데이터 수집 세부 정보](../insights/solutions-inventory.md) 를 참조 하세요. 솔루션 설치 및 사용에 대 한 자세한 내용은 [Azure Monitor의 솔루션 모니터링](../insights/solutions.md) 을 참조 하세요. |
| 메트릭 | Azure Monitor 리소스에 대 한 플랫폼 메트릭을 Log Analytics 작업 영역에 보내어 긴 기간 동안 로그 데이터를 보존 하 고 [Kusto 쿼리 언어](/azure/kusto/query/)를 사용 하 여 다른 데이터 형식으로 복잡 한 분석을 수행할 수 있습니다. [Log Analytics에 대 한 Azure 리소스 로그 스트리밍을](resource-logs-collect-storage.md)참조 하세요. |
| Azure 테이블 스토리지 | 일부 Azure 리소스가 모니터링 데이터를 쓰는 Azure storage에서 데이터를 수집 합니다. [Log Analytics 있는 이벤트에 대해서는 IIS 용 azure blob storage 및 azure table Storage 사용](azure-storage-iis-table.md)을 참조 하세요. |

### <a name="virtual-machines"></a>가상 머신

| 데이터 | Description |
|:---|:---|
|  에이전트 데이터 원본 | [Windows](agent-windows.md) 및 [Linux](../learn/quick-collect-linux-computer.md) 에이전트에서 수집 된 데이터 원본에는 이벤트, 성능 데이터 및 사용자 지정 로그가 포함 됩니다. 데이터 원본 목록 및 구성에 대 한 세부 정보는 [Azure Monitor의 에이전트 데이터 원본](data-sources.md) 을 참조 하세요. |
| 모니터링 솔루션 | 모니터링 솔루션은 에이전트에서 수집한 데이터를 Log Analytics 작업 영역으로 작성 합니다. 솔루션 목록은 [Azure의 관리 솔루션에 대 한 데이터 수집 세부 정보](../insights/solutions-inventory.md) 를 참조 하세요. 솔루션 설치 및 사용에 대 한 자세한 내용은 [Azure Monitor의 솔루션 모니터링](../insights/solutions.md) 을 참조 하세요. |
| System Center Operations Manager | Operations Manager 관리 그룹을 Azure Monitor에 연결 하 여 온-프레미스 에이전트에서 로그로 이벤트 및 성능 데이터를 수집 합니다. 이 구성에 대 한 자세한 내용은 [Log Analytics에 연결 Operations Manager을](om-agents.md) 참조 하세요. |


### <a name="applications"></a>애플리케이션

| 데이터 | Description |
|:---|:---|
| 요청 및 예외 | 응용 프로그램 요청 및 예외에 대 한 자세한 데이터는 _요청_, 페이지 _보기_및 _예외_ 테이블에 있습니다. [외부 구성 요소](../app/asp-net-dependencies.md) 에 대 한 호출은 _종속성_ 테이블에 있습니다. |
| 사용량 및 성능 | 응용 프로그램의 성능은 _요청_, _Browsertimings_ 및 _performanceCounters_ 테이블에서 사용할 수 있습니다. [사용자 지정 메트릭에](../app/api-custom-events-metrics.md#trackevent) 대 한 데이터는 _custommetrics_ 테이블에 있습니다.|
| 추적 데이터 | [분산 추적](../app/distributed-tracing.md) 의 결과는 _추적_ 테이블에 저장 됩니다. |
| 가용성 테스트 | [가용성 테스트](../app/monitor-web-app-availability.md) 의 요약 데이터는 _availabilityResults_ 테이블에 저장 됩니다. 이러한 테스트의 자세한 데이터는 별도의 저장소에 있으며 Azure Portal의 Application Insights에서 액세스할 수 있습니다. |

### <a name="insights"></a>인사이트

| 데이터 | Description |
|:---|:---|
| 컨테이너용 Azure Monitor | [컨테이너의 Azure Monitor에](../insights/container-insights-overview.md)의해 수집 된 인벤토리 및 성능 데이터입니다. 테이블 목록은 [컨테이너 데이터 수집 세부 정보](../insights/container-insights-log-search.md#container-records) 를 참조 하세요. |
| VM용 Azure Monitor | [VM용 Azure Monitor](../insights/vminsights-overview.md)에 의해 수집 된 맵 및 성능 데이터입니다. 이 데이터 쿼리에 대 한 자세한 내용은 [VM용 Azure Monitor에서 로그를 쿼리 하는 방법](../insights/vminsights-log-search.md) 을 참조 하세요. |

### <a name="custom"></a>맞춤형 서비스 

| 데이터 | Description |
|:---|:---|
| REST API | 모든 REST 클라이언트에서 Log Analytics 작업 영역에 데이터를 씁니다. 자세한 내용은 [HTTP 데이터 수집기 API를 사용 하 여 Azure Monitor에 로그 데이터 전송을](data-collector-api.md) 참조 하세요.
| 논리 앱 | **Azure Log Analytics 데이터 수집기** 작업을 사용 하 여 논리 앱 워크플로에서 Log Analytics 작업 영역에 데이터를 씁니다. |

### <a name="security"></a>보안

| 데이터 | Description |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) 은 수집 하는 데이터를 다른 로그 데이터를 사용 하 여 분석할 수 있는 Log Analytics 작업 영역에 저장 합니다. 작업 영역 구성에 대 한 자세한 내용은 [Azure Security Center의 데이터 수집](../../security-center/security-center-enable-data-collection.md) 을 참조 하세요. |
| Azure Sentinel | [Azure 센티널](/azure/sentinel/) 은 데이터 원본의 데이터를 Log Analytics 작업 영역에 저장 합니다. [데이터 원본 연결](/azure/sentinel/connect-data-sources)을 참조 하세요.  |


## <a name="next-steps"></a>다음 단계

- [Azure Monitor data platform](data-platform.md)에 대해 자세히 알아보세요.
- [Azure Monitor의 메트릭에](data-platform-metrics.md)대해 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
