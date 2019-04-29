---
title: Azure Monitor 데이터 플랫폼 | Microsoft Docs
description: Azure Monitor에서 수집된 데이터 모니터링은 간단한 메트릭으로 구분되며 고급 분석에 사용되는 실시간 시나리오 및 로그를 지원할 수 있습니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808941"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor 데이터 플랫폼

오늘날의 복잡 한 컴퓨팅 환경을 클라우드 및 온-프레미스 서비스에 의존 하는 분산된 응용 프로그램 실행 간에 관찰성을 사용 하려면 모든 계층 및 분산된 시스템의 모든 구성 요소에서 운영 데이터 수집 합니다. 조직에서 다양 한 관련자를 지원 하기 위해 다른 큐브 뷰를 사용 하 여 투명 효과의 단일 창으로 통합 하 고이 데이터에서 깊이 있는 통찰력을 수행 하는 일을 할 수 해야 합니다.

[Azure Monitor](../overview.md) 수집 및 분석, 시각화 및 경고에 대해 사용할 수 있는 일반적인 데이터 플랫폼으로 여러 원본에서에서 데이터를 집계 합니다. 여러 원본에서 제공 하는 깊이 있는 통찰력의 데이터와도 모니터링 된 모든 리소스에서 Azure Monitor에 데이터를 저장 하는 다른 서비스에서 데이터를 기반으로 일관 된 환경을 제공 합니다.


![Azure Monitor 개요](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure Monitor의 관찰성 데이터
메트릭, 로그 및 분산된 추적 일반적으로 라고 관찰성의 세 가지 핵심 요소입니다. 이들은 여러 가지 모니터링 도구를 수집 및 분석 하는 모니터링 되는 시스템의 충분 한 관찰성을 제공 해야 하는 데이터입니다. 관찰성 여러 영역에서 데이터를 상호 연결 하 고 모니터링 되는 리소스의 전체 집합에서 데이터를 집계 하 여 수행할 수 있습니다. 함께 여러 원본의 데이터를 저장 하는 Azure Monitor, 때문에 데이터를 상호 연결 하 고 공통 도구 집합을 사용 하 여 분석 합니다. 또한 여러 Azure 구독 및 테 넌 트에 다른 서비스에 대 한 데이터를 호스트 하는 것 외에도 데이터 상호 연결 합니다.

Azure 리소스 모니터링 데이터의 상당한을 생성 합니다. Azure Monitor는 메트릭 또는 로그 플랫폼 중 하나로 모니터링 되는 다른 원본에서 데이터와 함께이 데이터를 통합 합니다. 각각은 특정 모니터링 시나리오에 최적화 하 고 Azure Monitor에서 다양 한 기능을 지 원하는 각 키를 누릅니다. 데이터 분석, 시각화 및 경고와 같은 기능을 사용 하면 가장 효율적이 고 비용 효율적인 방식으로 필요한 시나리오를 구현할 수 있도록 차이 이해 해야 합니다. Insights와 같은 Azure Monitor [Application Insights](../app/app-insights-overview.md) 또는 [Vm에 대 한 Azure Monitor](../insights/vminsights-overview.md) 분석 도구가 알 필요 없이 특정 모니터링 시나리오에 초점을 맞출 수 있도록 합니다 두 가지 유형의 데이터 간의 차이점입니다. 


### <a name="metrics"></a>메트릭
[메트릭](data-platform-metrics.md)은 시간상 특정 지점에서 시스템의 일부 측면을 설명하는 숫자 값입니다. 정기적으로 수집 하 고 타임 스탬프, 이름, 값 및 하나 이상의 정의 레이블을 사용 하 여 식별 됩니다. 메트릭은 다양 한 알고리즘, 다른 메트릭과 비교 하 고 시간에 따른 추세에 대 한 분석을 사용 하 여 집계할 수 있습니다. 

Azure Monitor의 메트릭 타임 스탬프 데이터 분석을 위해 최적화 된 시계열 데이터베이스에 저장 됩니다. 특히 경고에 대 한 적합 한 빠르고 메트릭을 사용 하면이 문제를 검색 합니다. 시스템 수행 하는 방법을 알려 따르면 일반적으로 문제의 근본 원인을 파악 하기 위해 로그를 사용 하 여 결합 해야 합니다.

메트릭을 사용 하 여 Azure portal에서 대화식 분석을 위해 사용할 수 있습니다 [메트릭 탐색기](../app/metrics-explorer.md)합니다. 에 추가할 수 있습니다는 [Azure 대시보드에](../learn/tutorial-app-dashboards.md) 다른 데이터와 함께에서 시각화에 대 한 거의 실시간에 사용할 [경고](alerts-metric.md)합니다.

해당 소스에서 데이터를 포함 하 여 Azure Monitor 메트릭에 대해 자세히 알아보세요 [Azure Monitor의 메트릭](data-platform-metrics.md)합니다.

### <a name="logs"></a>로그
[로그](data-platform-logs.md) 시스템 내에서 발생 한 이벤트입니다. 구조 수 있고 자유 형식 텍스트 타임 스탬프를 사용 하 여 여러 종류의 데이터를 포함할 수 있습니다 이러한. 만들 수 있습니다 산발적으로 환경에서 이벤트 로그 항목을 생성 하 고 부하가 시스템 일반적으로 자세한 로그 볼륨을 생성 합니다.

Azure Monitor의 로그를 기반으로 하는 Log Analytics 작업 영역에 저장 됩니다 [Azure 데이터 탐색기](/azure/data-explorer/) 강력한 분석 엔진을 제공 하 고 [풍부한 쿼리 언어](/azure/kusto/query/)합니다. 로그는 일반적으로 식별 된 문제의 전체 컨텍스트를 제공 하려면 충분 한 정보를 제공 하 고 문제의 근본 원인 식별 하는 데 유용 합니다.

> [!NOTE]
> Azure Monitor 로그 및 Azure에서 로그 데이터의 원본을 구별 하는 것이 반드시 합니다. 예를 들어, Azure에서 구독 수준 이벤트는 쓸를 [활동 로그](activity-logs-overview.md) Azure Monitor 메뉴에서 확인할 수 있습니다. 대부분의 리소스에 작업 정보를 기록 합니다는 [진단 로그](diagnostic-logs-overview.md) 는 서로 다른 위치에 전달할 수 있습니다. Azure Monitor 로그는 활동 로그 및 리소스 집합 전체에서 심층 분석을 위해 다른 모니터링 데이터와 함께 진단 로그를 수집 하는 로그 데이터 플랫폼입니다.


 작업할 수 있습니다 [쿼리를 로깅](../log-query/log-query-overview.md) 사용 하 여 대화형으로 [Log Analytics](../log-query/portals.md) Azure portal에서 결과를 추가 또는 [Azure 대시보드에](../learn/tutorial-app-dashboards.md) 함께에서 시각화에 대 한 다른 데이터입니다. 만들 수도 있습니다 [로그 경고](alerts-log.md) 일정 쿼리의 결과에 따라 경고를 트리거합니다.

해당 소스에서 데이터를 포함 하 여 Azure Monitor 로그에 대해 자세히 알아보세요 [Azure Monitor의 로그](data-platform-logs.md)합니다.

### <a name="distributed-traces"></a>분산된 추적
추적 된 일련의 분산된 시스템을 통해 사용자 요청을 수행 하는 관련된 이벤트입니다. 다른 트랜잭션의 성능과 응용 프로그램 코드의 동작을 결정할 수 있습니다. 로그는 분산된 시스템의 개별 구성 요소에 의해 만들어집니다 자주, 하는 동안 추적 구성 요소의 전체 집합에서 작업 및 응용 프로그램의 성능을 측정 합니다.

Azure Monitor에서 분산된 추적을 사용 하 여 사용할 수는 [Application Insights SDK](../app/distributed-tracing.md), Application Insights에서 수집 된 다른 응용 프로그램 로그 데이터를 사용 하 여 추적 데이터가 저장 됩니다. 이렇게 하면 로그 쿼리, 대시보드 및 경고를 포함 하 여 다른 로그 데이터와 같은 분석 도구를 사용할 수 있습니다.

자세히 추적 distributed [분산 추적 이란?](../app/distributed-tracing.md)합니다.


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure Monitor 메트릭 및 로그를 비교 합니다.

다음 표에서 메트릭 및 Azure Monitor의 로그를 비교 합니다.

| 특성  | 메트릭 | 로그 |
|:---|:---|:---|
| 이점 | 간단 하 고 거의 실시간으로 경고 하는 등의 시나리오의 수입니다. 빠른 검색 문제에 적합 합니다. | 풍부한 분석 쿼리 언어입니다. 심층 분석 하 고 근본 원인을 식별에 적합 합니다. |
| Data | 숫자 값만 | 텍스트 또는 숫자 데이터 |
| 구조 | 샘플 시간 모니터링 되는 리소스, 숫자 값을 포함 하는 속성의 표준 집합입니다. 일부 메트릭은 추가 정의 대 한 여러 차원을 포함합니다. | 고유한 로그 유형에 따라 속성 집합입니다. |
| 컬렉션 | 정기적으로 수집 합니다. | 이벤트 트리거는 레코드를 만들 수를 산발적으로 수집할 수 있습니다. |
| Azure 포털에서 보기 | 메트릭 탐색기 | Log Analytics |
| 데이터 원본 포함 | Azure 리소스의 플랫폼 메트릭을 수집 합니다.<br>Application Insights에서 모니터링 하는 응용 프로그램입니다.<br>응용 프로그램 또는 API에 의해 정의 된 사용자 지정 합니다. | 응용 프로그램 및 진단 로그입니다.<br>솔루션을 모니터링 합니다.<br>에이전트 및 VM 확장 합니다.<br>응용 프로그램 요청 및 예외입니다.<br>Azure Security Center입니다.<br>데이터 수집기 API입니다. |

## <a name="collect-monitoring-data"></a>모니터링 데이터를 수집 합니다.
다른 [Azure Monitor에 대 한 데이터 원본을](data-sources.md) Log Analytics 작업 영역 (로그) 또는 Azure Monitor 메트릭 데이터베이스 (메트릭) 또는 둘 다를 기록 합니다. Azure storage와 같은 다른 위치에 기록 하 고 로그 또는 메트릭을 채우는 데 몇 가지 구성이 필요할 수 있습니다 다른 일부 원본은 이러한 데이터 저장소에 직접 작성 합니다. 

참조 [Azure Monitor의 메트릭](data-platform-metrics.md) 하 고 [Azure Monitor의 로그](data-platform-logs.md) 유형별로 채우는 다른 데이터 원본의 목록은.


## <a name="stream-data-to-external-systems"></a>외부 시스템으로 데이터 스트리밍
Azure에서 도구를 사용하여 모니터링 데이터를 분석하는 것 외에, SIEM(보안 정보 및 이벤트 관리) 제품과 같은 외부 도구로 전달하기 위한 요구 사항이 있을 수 있습니다. 이 전달은 일반적으로 [Azure Event Hubs](/azure/event-hubs/)를 통해 모니터링된 리소스에서 직접 수행됩니다. 일부 소스는 논리 앱과 같은 다른 프로세스를 사용 하 여 필요한 데이터를 검색 하는 동안 데이터를 이벤트 허브로 직접 보내도록 구성할 수 있습니다. 참조 [외부 도구에서 소비에 대 한 이벤트 허브로 데이터를 모니터링 하는 Stream Azure](stream-monitoring-data-event-hubs.md) 세부 정보에 대 한 합니다.



## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [Azure Monitor의 메트릭](data-platform-metrics.md)합니다.
- 에 대해 자세히 알아보세요 [Azure Monitor의 로그](data-platform-logs.md)합니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
