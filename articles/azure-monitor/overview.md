---
title: Azure Monitor 개요 | Microsoft Docs
description: Azure 서비스 및 애플리케이션에 대한 전체 모니터링 전략에 작용하는 Microsoft 서비스 및 기능을 대략적으로 설명합니다.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: bwren
ms.openlocfilehash: 0485f8e3b377ce94ec23a4a1a94eb7e189b0232b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787193"
---
# <a name="azure-monitor-overview"></a>Azure Monitor 개요

Azure Monitor는 클라우드 및 온-프레미스 환경에서 원격 분석 데이터를 수집, 분석하고 그에 따라 조치를 취하는 포괄적인 솔루션을 제공함으로써 애플리케이션의 성능과 가용성을 최대화합니다. 애플리케이션을 수행하는 방법과 애플리케이션 및 종속된 리소스에 영향을 주는 문제를 사전에 식별하는 방법을 파악할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>개요
다음 다이어그램은 Azure Monitor에 대한 개요를 제공합니다. 다이어그램의 중심에는 Azure Monitor의 두 가지 데이터 사용 기본 유형인 메트릭 및 로그를 저장하는 데이터 저장소가 있습니다. 왼쪽에는 이러한 [데이터 저장소](platform/data-platform.md)를 채우는 [모니터링 데이터의 원본](platform/data-sources.md)이 있습니다. 오른쪽에는 이렇게 수집된 데이터를 사용하여 Azure Monitor가 수행하는 분석, 경고, 외부 시스템으로 스트리밍 등의 여러 함수가 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

![Azure Monitor 개요](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>데이터 플랫폼 모니터링
Azure Monitor가 수집하는 모든 데이터는 두 가지 기본 유형인 [메트릭 및 로그](platform/data-platform.md) 중 한 쪽에 적합합니다. [메트릭](platform/data-platform-metrics.md)은 시간상 특정 지점에서 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 간단하며 실시간에 가까운 시나리오를 지원할 수 있습니다. [로그](platform/data-platform-logs.md)에는 각 형식에 대해 다양한 속성 집합이 포함된 레코드로 구성된 다양한 데이터 형식이 포함됩니다. 이벤트 및 추적과 같은 원격 분석은 분석을 위해 모두 결합될 수 있도록 성능 데이터 외에도 로그로 저장됩니다.

많은 Azure 리소스의 경우 Azure Monitor가 수집한 데이터는 Azure Portal의 [개요] 페이지에서 바로 볼 수 있습니다. 예를 들어 아무 가상 머신을 살펴보면 성능 메트릭을 표시하는 여러 차트를 볼 수 있습니다. 아무 그래프나 클릭하면 Azure Portal의 [메트릭 탐색기](platform/metrics-charts.md)에서 데이터가 열리고, 시간에 따른 여러 메트릭의 값을 차트로 볼 수 있습니다.  대화형으로 차트를 보거나 다른 시각화 요소를 사용하여 보려는 대시보드에 고정할 수 있습니다.

![메트릭](media/overview/metrics.png)

Azure Monitor로 수집한 로그 데이터는 수집된 데이터를 신속하게 검색, 통합 및 분석하는 [쿼리](log-query/log-query-overview.md)로 분석할 수 있습니다.  만들고 사용 하 여 쿼리를 테스트할 수 있습니다 [Log Analytics](log-query/portals.md) Azure portal 및 다음에서 직접 이러한 도구를 사용 하 여 데이터를 분석 또는 사용에 대 한 쿼리를 저장할 [시각화](visualizations.md) 또는 [경고 규칙](platform/alerts-overview.md)합니다.

Azure Monitor는 Azure Data Explorer에서 사용되는 [Kusto 쿼리 언어](/azure/kusto/query/)의 한 버전을 사용합니다. Kusto 쿼리 언어는 단순 로그 쿼리에 적합하지만 집계, 조인, 스마트 분석 등의 고급 기능도 포함합니다. [여러 강좌](log-query/get-started-queries.md)를 통해 쿼리 언어를 빠르게 배울 수 있습니다.  [SQL](log-query/sql-cheatsheet.md) 및 [Splunk](log-query/splunk-cheatsheet.md)에 이미 익숙한 사용자에게는 특정 지침이 제공됩니다.

![로그](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Azure Monitor는 어떤 데이터를 수집하나요?
Azure Monitor는 다양한 원본에서 데이터를 수집할 수 있습니다. 애플리케이션, 애플리케이션에서 사용하는 운영 체제 및 서비스부터 플랫폼 자체까지, 계정의 애플리케이션에 대한 모든 데이터를 모니터링한다고 생각하시면 됩니다. Azure Monitor는 다음과 같은 각 계층에서 데이터를 수집합니다.

- **애플리케이션 모니터링 데이터**: 플랫폼에 관계없이, 작성한 코드의 성능 및 기능에 대한 데이터입니다.
- **게스트 OS 모니터링 데이터**: 애플리케이션이 실행되고 있는 운영 체제에 대한 데이터입니다. Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있습니다. 
- **Azure 리소스 모니터링 데이터**: Azure 리소스의 작업에 대한 데이터입니다.
- **Azure 구독 모니터링 데이터**: Azure 구독의 운영 및 관리에 대한 데이터와 Azure 자체의 상태 및 작업에 대한 데이터입니다. 
- **Azure 테넌트 모니터링 데이터**: Azure Active Directory 등, 테넌트 수준 Azure 서비스의 작업에 대한 데이터입니다.

Azure 구독을 만들고 가상 머신이나 웹앱 같은 리소스 추가를 시작하는 즉시, Azure Monitor가 데이터 수집을 시작합니다.  리소스가 생성되거나 수정되면 [활동 로그](platform/activity-logs-overview.md)가 기록됩니다. [메트릭](platform/data-platform.md)은 리소스가 수행되는 방법 및 사용되는 리소스를 알려줍니다. 

[진단을 사용하도록 설정](platform/diagnostic-logs-overview.md)하고 계산 리소스에 [에이전트를 추가](platform/agent-windows.md)하여 수집하는 데이터를 실제 리소스 작업으로 확장할 수 있습니다. 이렇게 하면 리소스의 내부 작업에 대한 원격 분석 데이터가 수집되며, Windows 및 Linux 게스트 운영 체제에서 로그 및 메트릭을 수집하도록 여러 [데이터 원본](platform/agent-data-sources.md)을 구성할 수 있습니다. 

[애플리케이션에 계측 패키지를 추가](app/azure-web-apps.md)하고 페이지 보기, 애플리케이션 요청 및 예외를 포함하여 애플리케이션에 대한 자세한 정보를 수집하도록 Application Insights를 설정하세요. 사용자 트래픽을 시뮬레이션하도록 [가용성 테스트](app/monitor-web-app-availability.md)를 구성하여 애플리케이션의 가용성을 추가로 확인하세요.

### <a name="custom-sources"></a>사용자 지정 원본
Azure Monitor는 [데이터 수집기 API](platform/data-collector-api.md)를 사용하여 REST 클라이언트에서 로그 데이터를 수집할 수 있습니다. 그러면 사용자 정의 모니터링 시나리오를 작성하고 다른 소스를 통해 원격 분석을 표시하지 않는 리소스까지 모니터링을 확장할 수 있습니다.



## <a name="insights"></a>자세한 정보
데이터 모니터링은 계산 환경의 작업에 대한 가시성을 높일 수 있는 경우에만 유용합니다. Azure Monitor는 애플리케이션 및 애플리케이션에서 사용하는 다른 리소스에 대한 소중한 인사이트를 제공하는 여러 기능과 도구를 포함하고 있습니다. [모니터링 솔루션](insights/solutions.md) 및 기능(예: [Application Insights](app/app-insights-overview.md) 및 [컨테이너용 Azure Monitor](insights/container-insights-overview.md))은 애플리케이션 및 특정 Azure 서비스의 다양한 측면에 대한 심층적인 인사이트를 제공합니다. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md)는 클라우드 또는 온-프레미스에 호스트되는 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링할 수 있습니다. Azure Monitor의 강력한 데이터 분석 플랫폼을 활용하여 애플리케이션의 작업에 대한 심층 인사이트를 제공하고, 사용자가 오류를 보고할 때까지 기다리지 않고 오류를 진단합니다. Application Insights는 다양한 개발 도구에 대한 연결점을 포함하고 있으며 Visual Studio와 통합되어 DevOps 프로세스를 지원합니다.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor
[컨테이너용 Azure Monitor](insights/container-insights-overview.md)는 AKS(Azure Kubernetes Service)에서 호스팅되는 관리형 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링하도록 설계된 기능입니다. Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 컨테이너 로그도 수집됩니다.  Kubernetes 클러스터에서 모니터링을 사용하도록 설정하면, 이러한 메트릭 및 로그가 Linux용 Log Analytics 에이전트의 컨테이너화된 버전을 통해 자동으로 수집됩니다.

![컨테이너 상태](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor
[VM용 Azure Monitor](insights/vminsights-overview.md)는 다양한 프로세스와 다른 리소스 및 외부 프로세스에 대한 상호 연결된 종속성을 포함하여 Windows 및 Linux VM의 성능과 상태를 분석하여 Azure VM(가상 머신)을 규모에 맞게 모니터링합니다. 이 솔루션에는 온-프레미스 또는 다른 클라우드 공급자에 호스트되는 VM의 성능 및 애플리케이션 종속성 모니터링에 대한 지원이 포함됩니다.  


![VM 인사이트](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>모니터링 솔루션
Azure Monitor의 [관리 솔루션](insights/solutions.md)은 특정 애플리케이션이나 서비스를 깊이 있게 이해하도록 하는 패키지된 논리 집합입니다. 여기에는 애플리케이션 또는 서비스에 대한 모니터링 데이터를 수집하기 위한 논리, 해당 데이터를 분석하기 위한 [쿼리](log-query/log-query-overview.md) 및 시각화를 위한 [뷰](../log-analytics/log-analytics-view-designer.md)가 포함됩니다. 모니터링 솔루션은 다양한 Azure 서비스 및 다른 애플리케이션에 대한 모니터링이 가능하도록 [Microsoft](insights/solutions-inventory.md) 및 파트너 업체에서 제공합니다.

![모니터링 솔루션](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>중요한 상황에 대응
효과적인 모니터링 솔루션이라면 대화형으로 모니터링 데이터를 분석하는 기능 외에도 수집한 데이터에서 식별된 위험 조건에 선제적으로 대응할 수 있어야 합니다. 예를 들어 문제를 조사할 책임이 있는 관리자에게 텍스트 또는 이메일을 보낼 수 있습니다. 또는 오류 상태를 해결하려고 시도하는 자동화 프로세스를 시작할 수 있습니다.


### <a name="alerts"></a>경고
[Azure Monitor의 경고](platform/alerts-overview.md)는 위험 조건을 사전에 알리고 잠재적으로 수정 작업을 시도합니다. 메트릭 기반의 경고 규칙은 숫자 값을 기반으로 거의 실시간에 가까운 경고를 제공합니다. 반면 로그 기반의 규칙은 여러 원본의 데이터 간에 복잡한 논리를 처리할 수 있습니다.

Azure Monitor의 경고 규칙은 고유한 수신자 집합 및 여러 규칙 간에 공유할 수 있는 작업을 포함하는 [작업 그룹](platform/action-groups.md)을 사용합니다. 요구 사항에 따라, 웹후크를 사용하여 경고가 외부 작업을 시작하도록 하거나 ITSM 도구와 통합하는 등의 작업을 작업 그룹이 수행할 수 있습니다.

![경고](media/overview/alerts.png)

### <a name="autoscale"></a>자동 크기 조정
자동 크기 조정을 사용하면 애플리케이션의 부하를 처리하기 위해 적절한 양의 리소스가 실행되도록 할 수 있습니다. Azure Monitor에서 수집한 메트릭을 사용하여 부하 증가를 처리하는 데 필요한 리소스를 자동으로 추가하고 유휴 상태인 리소스를 제거하여 비용을 절약하는 시기를 결정하는 규칙을 만들 수 있습니다. 리소스를 늘리거나 줄이는 시기를 결정하기 위한 최대/최소 인스턴스 수 및 논리를 지정할 수 있습니다.

![자동 크기 조정](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>모니터링 데이터 시각화
차트 및 표 같은 [시각화](visualizations.md)는 모니터링 데이터를 요약하여 여러 대상에게 보여주는 효과적인 도구입니다. Azure Monitor는 모니터링 데이터를 시각화하는 고유의 기능을 갖고 있으며 다른 Azure 서비스를 활용하여 모니터링 데이터를 여러 대상에 게시합니다.

### <a name="dashboards"></a>대시보드
[Azure 대시보드](../azure-portal/azure-portal-dashboards.md)에서는 메트릭 및 로그를 포함한 여러 종류의 데이터를 [Azure Portal](https://portal.azure.com)의 단일 창으로 결합할 수 있습니다. 필요에 따라 대시보드를 다른 Azure 사용자와 공유할 수 있습니다. 로그 쿼리 또는 메트릭 차트의 출력 외에도 Azure Monitor 전체의 요소를 Azure 대시보드에 추가할 수 있습니다. 예를 들어 메트릭 그래프, 활동 로그 표, Application Insights의 사용량 차트, 로그 쿼리를 나타내는 타일이 조합된 대시보드를 만들 수 있습니다.

![대시보드](media/overview/dashboard.png)

### <a name="views"></a>뷰
[뷰](../log-analytics/log-analytics-view-designer.md)는 Azure Monitor의 로그 데이터를 시각적으로 표시합니다.  각 보기에는 중요한 데이터를 요약하는 목록 외에도 막대형 차트 및 꺾은선형 차트 같은 시각화 조합으로 드릴다운하는 단일 타일이 포함되어 있습니다.  모니터링 솔루션은 특정 애플리케이션에 대한 데이터를 요약하는 보기를 포함하며, 로그 쿼리에서 데이터를 표시하기 위해 자신만의 보기를 만들 수 있습니다. Azure Monitor의 다른 요소와 마찬가지로, 보기를 Azure 대시보드에 추가할 수 있습니다.

![보기](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com)는 다양한 데이터 원본에서 대화형 시각화를 제공하는 비즈니스 분석 서비스이며, 조직 내부 및 외부의 사람들에게 데이터를 제공하는 효과적인 수단입니다. [Azure Monitor에서 자동으로 로그 데이터를 가져오도록](../log-analytics/log-analytics-powerbi.md) Power BI를 구성하여 이러한 추가 시각화를 활용할 수 있습니다.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>데이터 통합 및 내보내기
Azure Monitor를 다른 시스템과 통합하여 모니터링 데이터를 사용하는 사용자 지정 솔루션을 빌드해야 하는 요구 사항이 자주 있습니다. 다른 Azure 서비스는 Azure Monitor와 함께 작동하여 이러한 통합을 제공합니다.

### <a name="event-hub"></a>이벤트 허브
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs)는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 데이터를 변환하고 저장할 수 있는 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs를 사용 하 여 [Azure Monitor 데이터 스트림](platform/stream-monitoring-data-event-hubs.md) 파트너 SIEM 및 모니터링 도구입니다.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps)는 다른 시스템 및 서비스와 통합되는 워크플로를 사용하여 작업 및 비즈니스 프로세스를 자동화할 수 있는 서비스입니다. Azure Monitor에서 메트릭 및 로그를 읽고 쓰는 작업을 사용할 수 있으며, 이를 통해 다양한 시스템과 통합되는 워크플로를 빌드할 수 있습니다.


### <a name="api"></a>API
생성된 경고에 액세스할 수 있을 뿐 아니라 Azure Monitor의 메트릭을 읽고 쓸 수 있는 여러 API가 제공됩니다. 또한 경고를 구성하고 검색할 수 있습니다. 이렇게 하면 근본적으로 Azure Monitor와 통합되는 사용자 지정 솔루션을 빌드할 수 있는 무한한 가능성이 열립니다.

## <a name="next-steps"></a>다음 단계
다음에 대해 자세히 알아봅니다.

* Azure Monitor에서 수집한 데이터에 대한 [메트릭 및 로그](platform/data-platform.md).
* 애플리케이션의 다양한 구성 요소가 원격 분석을 전송하는 방법에 대한 [데이터 원본](platform/data-sources.md).
* 수집한 데이터를 분석하기 위한 [로그 쿼리](log-query/log-query-overview.md)