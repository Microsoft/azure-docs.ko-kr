---
title: Azure Monitor data platform | Microsoft Docs
description: Azure Monitor에서 수집된 데이터 모니터링은 간단한 메트릭으로 구분되며 고급 분석에 사용되는 실시간 시나리오 및 로그를 지원할 수 있습니다.
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
ms.openlocfilehash: 74ede523c9747de4746fe1854bd1e352eba2f7e3
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148898"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor 데이터 플랫폼

클라우드 및 온-프레미스 서비스를 모두 사용 하는 분산 응용 프로그램을 실행 하는 오늘날의 복잡 한 컴퓨팅 환경에서 관찰성를 사용 하도록 설정 하려면 모든 계층의 작업 데이터와 분산 시스템의 모든 구성 요소를 수집 해야 합니다. 이 데이터에 대 한 심층 분석을 수행 하 고 조직에서 다 수의 관련자를 지 원하는 다양 한 관점을 갖춘 단일 창으로 통합할 수 있어야 합니다.

[Azure Monitor](../overview.md) 는 다양 한 소스의 데이터를 수집 하 고 분석, 시각화 및 경고에 사용할 수 있는 공통 데이터 플랫폼으로 집계 합니다. 여러 원본의 데이터를 기반으로 하는 일관 된 환경을 제공 합니다 .이를 통해 모니터링 되는 모든 리소스에 대 한 심층 통찰력을 제공 하 고 데이터를 Azure Monitor 저장 하는 다른 서비스의 데이터를 사용할 수 있습니다.


![Azure Monitor 개요](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure Monitor의 관찰성 데이터
메트릭, 로그 및 분산 추적을 일반적으로 관찰성의 세 핵심 요소 라고 합니다. 모니터링 대상 시스템에 충분 한 관찰성을 제공 하기 위해 모니터링 도구에서 수집 하 고 분석 해야 하는 다양 한 종류의 데이터입니다. 관찰성는 여러 핵심 요소의 데이터를 상호 연결 하 고 모니터링 되는 전체 리소스 집합에서 데이터를 집계 하 여 달성할 수 있습니다. Azure Monitor는 여러 원본의 데이터를 함께 저장 하므로 공통 된 도구 집합을 사용 하 여 데이터를 상호 연결 하 고 분석할 수 있습니다. 또한 다른 서비스에 대 한 데이터를 호스팅하는 것 외에도 여러 Azure 구독 및 테 넌 트 간에 데이터의 상관 관계를 유지 합니다.

Azure 리소스는 상당한 양의 모니터링 데이터를 생성 합니다. Azure Monitor는이 데이터를 다른 원본에서 메트릭 또는 로그 플랫폼으로 모니터링 하는 데이터와 함께 통합 합니다. 각은 특정 모니터링 시나리오에 맞게 최적화 되며, 각각 Azure Monitor의 다양 한 기능을 지원 합니다. 데이터 분석, 시각화, 경고 등의 기능을 위해서는 필요한 시나리오를 가장 효율적이 고 비용 효율적인 방식으로 구현할 수 있도록 차이점을 이해 해야 합니다. [Application Insights](../app/app-insights-overview.md) 또는 [VM용 Azure Monitor](../insights/vminsights-overview.md) 와 같은 Azure Monitor의 정보에는 두 가지 데이터 형식 간의 차이점을 이해 하지 않고도 특정 모니터링 시나리오에 집중할 수 있는 분석 도구가 있습니다. 


### <a name="metrics"></a>메트릭
[메트릭](data-platform-metrics.md)은 시간상 특정 지점에서 시스템의 일부 측면을 설명하는 숫자 값입니다. 이러한 항목은 일정 한 간격으로 수집 되며 타임 스탬프, 이름, 값 및 하나 이상의 정의 레이블로 식별 됩니다. 다른 메트릭과 비교 하 여 다양 한 알고리즘을 사용 하 여 메트릭을 집계 하 고 시간에 따른 추세를 분석할 수 있습니다. 

Azure Monitor 메트릭은 타임 스탬프 데이터를 분석 하는 데 최적화 된 시계열 데이터베이스에 저장 됩니다. 이를 통해 메트릭은 문제에 대 한 경고 및 빠른 검색에 특히 적합 합니다. 시스템에서 수행 하는 방식을 알 수 있지만 일반적으로 로그와 결합 하 여 문제의 근본 원인을 파악 해야 합니다.

메트릭은 [Azure 메트릭 탐색기](../platform/metrics-getting-started.md)를 사용 하 여 Azure Portal에서 대화형 분석에 사용할 수 있습니다. 다른 데이터와 함께 시각화를 위해 [Azure 대시보드에](../learn/tutorial-app-dashboards.md) 추가 하 고 거의 실시간으로 [경고](alerts-metric.md)하는 데 사용할 수 있습니다.

[Azure Monitor의 메트릭에](data-platform-metrics.md)데이터 원본을 포함 한 Azure Monitor 메트릭에 대해 자세히 알아보세요.

### <a name="logs"></a>로그
[로그](data-platform-logs.md) 는 시스템 내에서 발생 한 이벤트입니다. 다른 종류의 데이터를 포함할 수 있으며 타임 스탬프를 사용 하는 구조화 또는 자유 형식 텍스트 일 수 있습니다. 환경에서 이벤트는 로그 항목을 생성 하 고 부하가 높은 시스템은 일반적으로 더 많은 로그 볼륨을 생성 하므로 산발적으로 만들어질 수 있습니다.

Azure Monitor의 로그는 강력한 분석 엔진과 [풍부한 쿼리 언어](/azure/kusto/query/)를 제공 하는 [Azure 데이터 탐색기](/azure/data-explorer/) 을 기반으로 하는 Log Analytics 작업 영역에 저장 됩니다. 일반적으로 로그는 식별 되는 문제의 전체 컨텍스트를 제공 하는 데 충분 한 정보를 제공 하며 문제의 근본 사례를 식별 하는 데 중요 합니다.

> [!NOTE]
> Azure에서 Azure Monitor 로그와 로그 데이터의 원본을 구분 하는 것이 중요 합니다. 예를 들어 Azure의 구독 수준 이벤트는 Azure Monitor 메뉴에서 볼 수 있는 [활동 로그](platform-logs-overview.md) 에 기록 됩니다. 대부분의 리소스는 다른 위치로 전달할 수 있는 [리소스 로그](platform-logs-overview.md) 에 작업 정보를 기록 합니다. Azure Monitor 로그는 다른 모니터링 데이터와 함께 활동 로그 및 리소스 로그를 수집 하 여 전체 리소스 집합에 대 한 심층 분석을 제공 하는 로그 데이터 플랫폼입니다.


 Azure Portal에서 [Log Analytics](../log-query/portals.md) 를 사용 하 여 대화형으로 [로그 쿼리](../log-query/log-query-overview.md) 를 작업 하거나 다른 데이터와 함께 시각화를 위해 [Azure 대시보드에](../learn/tutorial-app-dashboards.md) 결과를 추가할 수 있습니다. 또한 일정 쿼리의 결과에 따라 경고를 트리거하는 [로그 경고](alerts-log.md) 를 만들 수 있습니다.

[Azure Monitor의 로그](data-platform-logs.md)에 데이터 원본이 포함 된 Azure Monitor 로그에 대해 자세히 알아보세요.

### <a name="distributed-traces"></a>분산 추적
추적은 분산 시스템을 통한 사용자 요청을 따르는 일련의 관련 이벤트입니다. 응용 프로그램 코드의 동작과 다른 트랜잭션의 성능을 결정 하는 데 사용할 수 있습니다. 로그는 분산 시스템의 개별 구성 요소에 의해 생성 되는 반면, 추적은 전체 구성 요소 집합에서 응용 프로그램의 작업 및 성능을 측정 합니다.

Azure Monitor의 분산 추적은 [APPLICATION INSIGHTS SDK](../app/distributed-tracing.md)를 사용 하 여 설정 되며 추적 데이터는 Application Insights에서 수집한 다른 응용 프로그램 로그 데이터와 함께 저장 됩니다. 이를 통해 로그 쿼리, 대시보드 및 경고를 비롯 한 다른 로그 데이터와 동일한 분석 도구를 사용할 수 있습니다.

[분산 추적 이란?](../app/distributed-tracing.md)에서 분산 추적에 대해 자세히 알아보세요.


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure Monitor 메트릭 및 로그 비교

다음 표에서는 Azure Monitor의 메트릭과 로그를 비교 합니다.

| 특성  | 메트릭 | 로그 |
|:---|:---|:---|
| 이점 | 경량 이며 경고와 같은 거의 실시간 시나리오를 지원 합니다. 문제를 신속 하 게 검색 하는 데 적합 합니다. | 풍부한 쿼리 언어를 사용 하 여 분석 됩니다. 심층 분석 및 근본 원인을 식별 하는 데 적합 합니다. |
| 데이터 | 숫자 값만 | 텍스트 또는 숫자 데이터 |
| 구조체 | 샘플 시간, 모니터링 되는 리소스, 숫자 값을 포함 하는 표준 속성 집합입니다. 일부 메트릭에는 추가 정의를 위해 여러 차원이 포함 됩니다. | 로그 유형에 따라 고유한 속성 집합입니다. |
| 컬렉션 | 정기적으로 수집 됩니다. | 이벤트가 생성 될 레코드를 트리거할 때 산발적으로 수집 될 수 있습니다. |
| Azure Portal에서 보기 | 메트릭 탐색기 | Log Analytics |
| 데이터 원본 포함 | Azure 리소스에서 수집 된 플랫폼 메트릭입니다.<br>Application Insights에서 모니터링 하는 응용 프로그램입니다.<br>응용 프로그램 또는 API에 의해 정의 된 사용자 지정입니다. | 응용 프로그램 및 리소스 로그.<br>솔루션 모니터링.<br>에이전트 및 VM 확장.<br>응용 프로그램 요청 및 예외.<br>Azure Security Center.<br>데이터 수집기 API. |

## <a name="collect-monitoring-data"></a>모니터링 데이터 수집
[Azure Monitor에 대 한 데이터 원본은](data-sources.md) Log Analytics 작업 영역 (로그) 또는 Azure Monitor 메트릭 데이터베이스 (메트릭) 중 하나 또는 둘 다에 기록 됩니다. 일부 소스는 이러한 데이터 저장소에 직접 기록 되 고 다른 일부는 Azure storage와 같은 다른 위치에 작성 될 수 있으며, 일부 구성에서 로그 또는 메트릭을 채울 수 있습니다. 

각 유형을 채우는 여러 데이터 원본의 목록을 보려면 [Azure Monitor의 메트릭과](data-platform-metrics.md) [Azure Monitor의 로그](data-platform-logs.md) 를 참조 하세요.


## <a name="stream-data-to-external-systems"></a>외부 시스템으로 데이터 스트리밍
Azure에서 도구를 사용하여 모니터링 데이터를 분석하는 것 외에, SIEM(보안 정보 및 이벤트 관리) 제품과 같은 외부 도구로 전달하기 위한 요구 사항이 있을 수 있습니다. 이 전달은 일반적으로 [Azure Event Hubs](/azure/event-hubs/)를 통해 모니터링된 리소스에서 직접 수행됩니다. 논리 앱과 같은 다른 프로세스를 사용 하 여 필요한 데이터를 검색할 수 있는 반면, 일부 소스는 이벤트 허브로 직접 데이터를 보내도록 구성할 수 있습니다. 자세한 내용은 [외부 도구에서 사용 하기 위해 이벤트 허브로 Azure 모니터링 데이터 스트림을](stream-monitoring-data-event-hubs.md) 참조 하세요.



## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 메트릭에](data-platform-metrics.md)대해 자세히 알아보세요.
- 로그에 대 한 자세한 내용은 [Azure Monitor를](data-platform-logs.md)참조 하세요.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
