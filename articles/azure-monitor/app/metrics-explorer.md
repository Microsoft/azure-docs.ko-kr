---
title: Azure Application Insights에서 메트릭 탐색 | Microsoft Docs
description: 메트릭 탐색기에 차트를 해석하는 방법 및 메트릭 탐색기 블레이드를 사용자 지정하는 방법입니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: mbullwin
ms.openlocfilehash: 5c659ca2f40d47450227d16963499a6b27c9e313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700890"
---
# <a name="exploring-metrics-in-application-insights"></a>Application Insights에서 메트릭 탐색
[Application Insights][start]의 메트릭은 애플리케이션의 원격 분석에서 전송된 측정된 값 및 이벤트 수입니다. 성능 문제를 감지하고 애플리케이션 사용 방식의 추세를 볼 수 있습니다. 다양한 표준 메트릭이 있으며 사용자 고유의 사용자 지정 메트릭 및 이벤트를 만들 수도 있습니다.

> [!NOTE]
> 이 문서에서는 현재 사용되지 않으며 결국 사용 중지될 예정인 클래식 메트릭 탐색기 환경을 설명합니다. [이 문서](../platform/metrics-charts.md)에 설명된 새 환경을 확인해 볼 것을 권장합니다.

메트릭 및 이벤트 수는 합계, 평균 또는 개수 등의 집계된 값에 대한 차트에 표시됩니다.

다음은 차트의 샘플 집합입니다.

![](./media/metrics-explorer/01-overview.png)

Application Insights 포털 어디에나 메트릭 차트가 있습니다. 대부분의 경우 사용자 지정할 수 있으며 블레이드에 차트를 더 추가할 수 있습니다. 개요 블레이드에서 더 자세한 차트("Servers"와 같은 타일이 있음)를 클릭하거나 **메트릭 탐색기**를 클릭하여 사용자 지정 차트를 만들 수 있는 새 블레이드를 엽니다.

## <a name="time-range"></a>시간 범위
모든 블레이드의 차트 또는 표에서 다루는 시간 범위를 변경할 수 있습니다.

![Azure 포털에서 애플리케이션의 개요 블레이드 열기](./media/metrics-explorer/03-range.png)

일부 데이터가 표시되어야 하지만 아직 표시되지 않은 경우 새로 고침을 클릭합니다. 차트는 특정 간격에 따라 자체적으로 새로 고쳐지지만 시간 범위가 더 클 경우 간격이 늘어납니다. 차트에 분석 파이프라인을 내놓기 위한 데이터에는 시간이 걸릴 수 있습니다.

차트의 일부를 확대하려면 해당 부분을 끕니다.

![차트의 일부를 끕니다.](./media/metrics-explorer/12-drag.png)

복원하려면 확대/축소 취소 단추를 클릭합니다.

## <a name="granularity-and-point-values"></a>세분성 및 지점 값
해당 지점에서 메트릭 값을 표시하려면 차트 위로 마우스를 가져갑니다.

![차트 위로 마우스 이동](./media/metrics-explorer/02-focus.png)

특정 지점에서 메트릭 값은 이전 샘플링 간격에 걸쳐 집계됩니다.

샘플링 간격 또는 "세분성"는 블레이드 위쪽에 표시됩니다.

![블레이드의 헤더입니다.](./media/metrics-explorer/11-grain.png)

시간 범위 블레이드에서 세분성을 조정할 수 있습니다.

![블레이드의 헤더입니다.](./media/metrics-explorer/grain.png)

세분성은 선택한 시간 범위에 따라 사용 가능합니다. 명시적 세분성은 시간 범위에 대한 "자동" 세분성의 대안입니다.


## <a name="editing-charts-and-grids"></a>차트 및 표 편집
블레이드에 새 차트를 추가하려면:

![메트릭 탐색기에서 추가 차트 선택](./media/metrics-explorer/04-add.png)

기존 또는 새 차트에서 **편집**을 선택하여 표시되는 항목을 편집합니다.

![하나 이상의 메트릭 선택](./media/metrics-explorer/08-select.png)

함께 표시할 수 있는 조합에 관한 제한이 있지만 차트에 하나 이상의 메트릭을 표시할 수 있습니다. 한 메트릭을 선택하면 일부 다른 메트릭을 사용할 수 없습니다.

[사용자 지정 메트릭][track]을 앱으로 코딩한 경우(TrackMetric 및 TrackEvent 호출) 여기에 나열됩니다.

## <a name="segment-your-data"></a>데이터 분할
메트릭을 속성별로 분할할 수 있습니다. 예를 들어 서로 다른 운영 체제를 사용하는 클라이언트에서 페이지 보기를 비교하려는 경우가 여기에 해당합니다.

차트 또는 표를 선택하고 그룹으로 전환하여 그룹별로 속성을 선택합니다.

![그룹화를 선택한 다음 그룹별에서 속성 선택](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> 그룹화를 사용하면 영역형 차트 및 가로 막대형 차트 유형이 누적형 디스플레이를 제공합니다. 이것은 집계 방법이 합계일 때 적합합니다. 하지만 집계 유형이 평균이면 꺾은선형 또는 표 표시 형식을 선택합니다.
>
>

[사용자 지정 메트릭][track]을 앱으로 코딩하고 속성 값을 포함하는 경우 목록에서 속성을 선택할 수 있게 됩니다.

데이터를 분할하기에 차트가 너무 작나요? 높이 조정:

![슬라이더 조정](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>집계 형식
기본적으로 옆쪽의 범례는 차트의 기간에 걸쳐 집계된 값을 일반적으로 보여 줍니다. 차트 위로 마우스를 가져가면 해당 지점의 값이 표시됩니다.

차트에서 각 데이터 요소는 이전 샘플링 간격 또는 "세분성"으로 받은 데이터 값의 집계입니다. 세분성은 블레이드 위쪽에 표시되며 차트의 전체적인 시간 간격에 따라 달라집니다.

메트릭은 다른 방법으로 집계할 수 있습니다.

* **개수**는 샘플링 간격에서 받은 이벤트 개수입니다. 요청과 같은 이벤트에 사용됩니다. 차트 높이의 편차는 이벤트가 발생하는 비율의 변동을 나타냅니다. 그러나 샘플링 간격을 변경하면 숫자 값이 변경됩니다.
* **합계** 는 샘플링 간격 또는 차트의 기간 동안 받은 모든 데이터 요소의 값을 더합니다.
* **평균** 은 합계를 간격을 통해 받은 데이터 요소 수로 나눕니다.
* **고유** 개수는 사용자 및 계정의 수를 세는 데 사용됩니다. 그림에서는 샘플링 간격 또는 차트의 기간 동안 해당 시간에 표시된 서로 다른 사용자의 수를 보여 줍니다.
* **%** - 각 집계의 백분율 버전은 세그먼트 차트에서만 사용됩니다. 합계는 항상 최대 100%이며, 차트에는 합계의 여러 구성 요소의 상대적 기여도가 표시됩니다.

    ![백분율 집계](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>집계 형식 변경

![차트를 편집한 다음 집계를 선택합니다.](./media/metrics-explorer/05-aggregation.png)

각 메트릭에 대한 기본 방법은 새 차트를 만들거나 모든 메트릭을 선택 취소할 때 표시됩니다.

![모든 메트릭의 선택을 취소하여 기본값 표시](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Y축 고정 
기본적으로 차트는 값 퀀텀을 시각적으로 나타내기 위해 0부터 시작해서 데이터 범위의 최대값까지 Y축 값을 표시합니다. 그렇지만 경우에 따라 값의 경미한 변화를 시각적으로 확인하기 위해 컨텀 이상에 관심이 있을 수 있습니다. 이와 같은 사용자 지정을 위해서는 Y축 범위 편집 기능을 사용하여 Y축 최소값 또는 최대값을 원하는 위치에 고정합니다.
"고급 설정" 확인란을 클릭하여 Y축 범위 설정 표시

![고급 설정을 클릭하고, 사용자 지정 범위를 선택한 후 최소값 및 최대값 지정](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>데이터 필터링
속성 값의 선택한 집합에 대한 메트릭 보기:

![필터를 클릭하고 속성을 확장하여 값 선택](./media/metrics-explorer/19-filter.png)

특정 속성에 대한 값을 선택하지 않은 경우 모두 선택한 것과 동일합니다. 즉, 해당 속성에는 필터가 없습니다.

각 속성 값과 함께 이벤트 수를 확인합니다. 한 속성 값을 선택하면 다른 속성 값과 함께 수가 조정됩니다.

필터는 블레이드의 모든 차트에 적용됩니다. 여러 차트에 서로 다른 필터를 적용하려면 다른 메트릭 블레이드를 만들고 저장합니다. 필요한 경우 다른 블레이드의 차트를 대시보드에 고정하여 서로 나란히 볼 수 있습니다.

### <a name="remove-bot-and-web-test-traffic"></a>봇 및 웹 테스트 트래픽 제거
**실제 또는 가상 트래픽** 필터를 사용하여 **실제**를 확인합니다.

**가상 트래픽 소스**로 필터링할 수도 있습니다.

### <a name="to-add-properties-to-the-filter-list"></a>필터 목록에 속성을 추가하려면
직접 선택한 범주에서 원격 분석을 필터링하려고 하시나요? 예를 들어 사용자를 서로 다른 범주로 나누고 데이터를 이러한 범주로 분할하려고 할 수 있습니다.

[사용자 고유의 속성을 만듭니다](../../azure-monitor/app/api-custom-events-metrics.md#properties). [원격 분석 이니셜라이저](../../azure-monitor/app/api-custom-events-metrics.md#defaults) 에서 이를 설정하여 다른 SDK 모듈에서 보낸 표준 원격 분석을 포함하여 모든 원격 분석에 표시되도록 합니다.

## <a name="edit-the-chart-type"></a>차트 종류 편집
표와 그래프 간에 전환할 수 있습니다.

![표 또는 그래프를 선택한 다음 차트 종류 선택](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>매트릭 블레이드 저장
차트를 만든 경우 즐겨찾기로 저장합니다. 조직 계정을 사용하는 경우 다른 팀 구성원과 이를 공유할지 선택할 수 있습니다.

![즐겨찾기 선택](./media/metrics-explorer/21-favorite-save.png)

블레이드를 다시 보려면 **개요 블레이드로 이동** 하여 즐겨찾기를 엽니다.

![개요 블레이드에서 즐겨찾기 선택](./media/metrics-explorer/22-favorite-get.png)

저장했을 때 상대 시간을 선택한 경우 해당 블레이드가 최신 메트릭으로 업데이트됩니다. 절대 시간 범위를 선택한 경우 매번 동일한 데이터가 표시됩니다.

## <a name="reset-the-blade"></a>블레이드 다시 설정
블레이드를 편집하지만 저장된 원본 세트로 되돌아가려는 경우 재설정을 클릭하면 됩니다.

![메트릭 탐색기 위쪽에 있는 단추](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>라이브 메트릭 스트림

원격 분석을 더 빠르게 즉시 보려면 [라이브 스트림](live-stream.md)을 엽니다. 대부분의 메트릭은 집계 프로세스 때문에 몇 분 정도 지나야 표시됩니다. 반면 라이브 메트릭은 짧은 대기 시간에 최적화되었습니다. 

## <a name="set-alerts"></a>경고 설정
메트릭의 비정상적인 값에 대한 알림을 메일로 받으려면 경고를 추가합니다. 계정 관리자나 특정 메일 주소로 메일을 보내도록 선택할 수 있습니다.

![메트릭 탐색기에서 경고 규칙, 경고 추가 선택](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[경고에 대해 알아봅니다][alerts].


## <a name="continuous-export"></a>연속 내보내기
데이터를 외부에서 처리할 수 있도록 지속적으로 내보내려면 [연속 내보내기](../../azure-monitor/app/export-telemetry.md)를 사용하는 것이 좋습니다.

### <a name="power-bi"></a>Power BI
보다 풍부한 데이터 보기를 사용하려는 경우 [Power BI를 내보낼](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)수 있습니다.

## <a name="analytics"></a>분석
[분석](../../azure-monitor/app/analytics.md) 은 강력한 쿼리 언어를 사용하여 원격 분석을 분석하는 더욱 유용한 방법입니다. 메트릭의 결과를 결합하거나 계산하려는 경우 또는 앱의 최근 성능을 면밀히 조사하려는 경우에 사용합니다. 

메트릭 차트에서 분석 아이콘을 클릭하여 해당하는 분석 쿼리를 직접 가져올 수 있습니다.

## <a name="troubleshooting"></a>문제 해결
*차트에 데이터가 표시되지 않습니다.*

* 필터는 블레이드의 모든 차트에 적용됩니다. 하나의 필터에 포커스를 둔 동안 다른 차트에서 모든 데이터를 제외하는 필터를 설정하지 않았는지 확인하세요.

    여러 차트에서 서로 다른 필터를 설정하려면 해당 차트를 서로 다른 블레이드를 만들어 별도의 즐겨찾기로 저장합니다. 필요한 경우 대시보드에 고정하여 서로 나란히 볼 수 있습니다.
* 메트릭에 정의되지 않은 속성으로 차트를 그룹화한 경우 차트에 아무 것도 표시되지 않습니다. '그룹화 기준'을 지우거나 다른 그룹화 속성을 선택하세요.
* 성능 데이터(CPU, IO 속도 등)는 Java 웹 서비스, Windows 데스크톱 앱, [IIS Web Apps 및 서비스(상태 모니터를 설치한 경우)](../../azure-monitor/app/monitor-performance-live-website-now.md) 및 [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md)에 사용할 수 있습니다. Azure 웹 사이트에는 사용할 수는 없습니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>다음 단계
* [Application Insights를 사용하여 사용량 모니터링](../../azure-monitor/app/usage-overview.md)
* [진단 검색 사용](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
