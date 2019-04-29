---
title: Azure Monitor의 데이터 시각화 | Microsoft Docs
description: Azure Monitor에 저장된 메트릭 및 로그 데이터를 시각화하는 데 사용할 수 있는 방법을 요약해서 보여 줍니다.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/24/2018
ms.author: bwren
ms.openlocfilehash: 4e9bb9bd86e22efa5dd514e742481911b09c5e17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61088298"
---
# <a name="visualizing-data-from-azure-monitor"></a>Azure Monitor의 데이터 시각화
이 문서에서는 Azure Monitor에 저장된 로그 및 메트릭 데이터를 시각화하는 데 사용할 수 있는 방법을 요약해서 보여 줍니다.

차트 및 그래프와 같은 시각화는 모니터링 데이터를 분석하여 문제에 대해 드릴다운하고 패턴을 식별하는 데 도움이 될 수 있습니다. 사용하는 도구에 따라 조직 내부 및 외부의 다른 사용자와 시각화를 공유하는 옵션이 제공될 수도 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-dashboards"></a>Azure 대시보드
[Azure 대시보드](../azure-portal/azure-portal-dashboards.md)는 Azure의 주요 대시보드 기술입니다. 중요한 문제를 빠르게 파악할 수 있는 Azure 인프라 및 서비스에 대한 단일 창을 제공하는 점에서 특히 유용합니다.

![대시보드](media/visualizations/dashboard.png)

### <a name="advantages"></a>장점
- Azure에 긴밀하게 통합됩니다. 메트릭 탐색기, Log Analytics 및 Application Insights를 포함 하 여 여러 Azure 페이지에서 시각화를 대시보드에 고정할 수 있습니다.
- 메트릭 및 로그를 둘 다 지원합니다.
- 출력을 포함 하 여 여러 원본의 데이터를에서 결합할 [메트릭 탐색기](platform/metrics-charts.md), [쿼리를 기록](log-query/log-query-overview.md), 및 [매핑합니다](app/app-map.md) 및 Application Insights에서 가용성입니다.
- 개인 또는 공유 대시보드 옵션을 제공합니다. Azure [역할 기반 인증(RBAC)](../role-based-access-control/overview.md)과 통합됩니다.
- 자동으로 새로 고침됩니다. 메트릭 새로 고침은 시간 범위에 따라 달라지며, 시간 범위는 최소 5분입니다. 로그는 1분마다 새로 고침됩니다.
- 타임스탬프 및 사용자 지정 매개 변수로 메트릭 대시보드가 매개 변수화됩니다.
- 유연한 레이아웃 옵션을 제공합니다.
- 전체 화면 모드를 제공합니다.


### <a name="limitations"></a>제한 사항
- 데이터 테이블이 지원되지 않으므로 로그 시각화를 제한적으로만 제어할 수 있습니다. 총 데이터 계열 수는 10개로 제한되며 추가 데이터 계열은 _다른_ 버킷 아래에서 그룹화됩니다.
- 로그 차트에는 사용자 지정 매개 변수가 지원되지 않습니다.
- 로그 차트는 최근 30일로 제한됩니다.
- 로그 차트는 공유 대시보드에만 고정할 수 있습니다.
- 대시보드 데이터의 대화형 작업은 지원되지 않습니다.
- 상황별 드릴다운이 제한됩니다.

## <a name="azure-monitor-views"></a>Azure Monitor 뷰
[Azure Monitor의 뷰](platform/view-designer.md)를 통해 로그 데이터로 사용자 지정 시각화를 만들 수 있습니다. [모니터링 솔루션](insights/solutions.md)에서 수집한 데이터를 표시하는 데 사용됩니다.

![보기](media/visualizations/view.png)

### <a name="advantages"></a>장점
- 로그 데이터를 다양하게 시각화할 수 있습니다.
- 뷰를 내보내고 가져오는 방법을 통해 다른 리소스 그룹 및 구독으로 전송합니다.
- 작업 영역 및 모니터링 솔루션을 사용하여 Azure Monitor 관리 모델로 통합합니다.
- 사용자 지정 매개 변수에 대해 [필터링](platform/view-designer-filters.md)할 수 있습니다.
- 대화형이며, 여러 수준의 드릴인(다른 뷰로 드릴하는 뷰)을 지원합니다.

### <a name="limitations"></a>제한 사항
- 로그는 지원되지만 메트릭은 지원되지 않습니다.
- 개인 뷰가 없습니다. 작업 영역에 액세스할 수 있는 모든 사용자가 사용할 수 있습니다.
- 자동으로 새로 고침되지 않습니다.
- 제한된 레이아웃 옵션을 제공합니다.
- 여러 작업 영역 또는 Application Insights 애플리케이션에서 쿼리가 지원되지 않습니다.
- 쿼리 응답 크기는 8MB, 쿼리 실행 시간은 110초로 제한됩니다.


## <a name="workbooks"></a>통합 문서
[통합 문서](../azure-monitor/app/usage-workbooks.md)는 팀 내 데이터, 조사, 공동 작업에 대한 깊이 있는 인사이트를 제공하는 대화형 문서입니다. 통합 문서가 효율적으로 활용되는 구체적인 예는 문제 해결 가이드 및 인시던트 사후 검토에 나와 있습니다.

![통합 문서](media/visualizations/workbook.png)

### <a name="advantages"></a>장점
- 메트릭 및 로그를 둘 다 지원합니다.
- 테이블의 요소를 선택하면 연결된 차트 및 시각화가 동적으로 업데이트되는, 매개 변수를 사용할 수 있는 대화형 보고서를 지원합니다.
- 문서와 유사한 흐름입니다.
- 개인 또는 공유 통합 문서 옵션을 제공합니다.
- 쉽고 공동 작업이 용이한 작성 환경을 제공합니다.
- 템플릿에서 공용 GitHub 기반 템플릿 갤러리를 지원합니다.

### <a name="limitations"></a>제한 사항
- 자동으로 새로 고침되지 않습니다.
- 대시보드 같은 덴스 레이아웃이 없으므로 단일 창과 같이 통합 문서의 유용성을 낮춥니다. 더 심층적인 인사이트를 제공하기 위한 것입니다.


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)는 장기적인 KPI 추세를 분석하는 보고서뿐만 아니라 비즈니스 중심 대시보드와 보고서를 만드는 데 특히 유용합니다. [로그 쿼리 결과를](platform/powerbi.md) Power BI 데이터 세트로 가져오면 여러 원본의 데이터를 결합하고 웹 및 모바일 디바이스에서 보고서를 공유하는 등 해당 기능을 활용할 수 있습니다.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>장점
- 다양하게 시각화할 수 있습니다.
- 확대 및 교차 필터링을 포함한 광범위한 대화형 작업을 제공합니다.
- 손쉽게 조직 전체에서 공유할 수 있습니다.
- 여러 데이터 원본의 다른 데이터와 통합됩니다.
- 결과가 큐브에 캐시되므로 성능이 향상됩니다.


### <a name="limitations"></a>제한 사항
- 로그는 지원되지만 메트릭은 지원되지 않습니다.
- Azure에 통합되지 않습니다. Azure Resource Manager를 통해 대시보드 및 모델을 관리할 수 없습니다.
- 쿼리 결과를 구성하려면 Power BI 모델로 가져와야 합니다. 결과 크기와 새로 고침이 제한됩니다.
- 데이터 새로 고침이 하루 8번으로 제한됩니다.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/)는 뛰어난 운영 대시보드를 제공하는 개방형 플랫폼입니다. 운영 인시던트를 감지하고, 격리하고, 심사하는 데 특히 유용합니다. Azure 구독에 [Grafana Azure Monitor 데이터 원본 플러그 인](platform/grafana-plugin.md)을 추가하여 Azure 메트릭 데이터가 시각화되도록 할 수 있습니다.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>장점
- 다양하게 시각화할 수 있습니다.
- 다양한 데이터 원본 에코시스템을 제공합니다.
- 확대를 포함한 데이터 대화형 작업을 제공합니다.
- 매개 변수를 지원합니다.

### <a name="limitations"></a>제한 사항
- Azure에 통합되지 않습니다. Azure Resource Manager를 통해 대시보드 및 모델을 관리할 수 없습니다.
- 추가 Grafana 인프라를 지원하는 데 비용이 들거나 Grafana Cloud에 대한 추가 비용이 필요합니다.


## <a name="build-your-own-custom-application"></a>고유한 사용자 지정 애플리케이션 빌드
고유한 사용자 지정 웹 사이트 및 애플리케이션을 빌드할 수 있는 REST 클라이언트를 사용하면 해당 API를 통해 Azure Monitor의 로그 및 메트릭 데이터에 액세스할 수 있습니다.

### <a name="advantages"></a>장점
- UI, 시각화, 대화형 작업 및 기능에서 완벽한 유연성이 확보됩니다.
- 메트릭 및 로그 데이터를 다른 데이터 원본과 결합할 수 있습니다.

### <a name="disadvantages"></a>단점
- 상당한 엔지니어링 작업이 필요합니다.


## <a name="next-steps"></a>다음 단계
- [Azure Monitor에서 수집하는 데이터](platform/data-platform.md)에 대해 알아봅니다.
- [Azure 대시보드](../azure-portal/azure-portal-dashboards.md)에 대해 알아봅니다.
- [Azure Monitor의 뷰](platform/view-designer.md)에 대해 알아봅니다.
- 에 대 한 자세한 [통합 문서](../azure-monitor/app/usage-workbooks.md)합니다.
- [Power BI로 로그 데이터 가져오기](../azure-monitor/platform/powerbi.md)에 대해 알아봅니다.
- [Grafana Azure Monitor 데이터 원본 플러그 인](../azure-monitor/platform/grafana-plugin.md)에 대해 알아봅니다.

