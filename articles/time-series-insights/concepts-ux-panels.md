---
title: TSI 탐색기에서 데이터 시각화-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights 탐색기에서 제공 되는 기능 및 옵션에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/27/2020
ms.custom: seodec18
ms.openlocfilehash: 31ed5f93fc7d1b44ae8a4afb1f4368d0b52eb2fd
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227052"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights 탐색기

이 문서에서는 Azure Time Series Insights Gen2 [Demo 환경](https://insights.timeseries.azure.com/preview/demo)에서 사용할 수 있는 다양 한 기능 및 옵션을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Time Series Insights 탐색기를 시작 하려면 다음을 수행 해야 합니다.

* Azure Time Series Insights Gen2 환경을 프로 비전 합니다. [Azure Time Series Insights Gen2](./time-series-insights-update-create-environment.md) 자습서를 읽어 인스턴스를 프로 비전 하는 방법에 대해 자세히 알아보세요.
* 계정에 대해 만든 Azure Time Series Insights Gen2 환경에 대 한 [데이터 액세스를 제공](./time-series-insights-data-access.md) 합니다. 자신과 다른 사용자에 대한 액세스 권한을 모두 부여할 수 있습니다.
* Azure Time Series Insights Gen2 환경에 이벤트 소스를 추가 하 여 환경에 데이터를 푸시합니다.
  * [이벤트 허브에 연결 하는 방법](./time-series-insights-how-to-add-an-event-source-eventhub.md) 알아보기
  * [IoT hub에 연결 하는 방법](./time-series-insights-how-to-add-an-event-source-iothub.md) 알아보기

## <a name="explore-the-azure-time-series-insights-explorer"></a>Azure Time Series Insights 탐색기 살펴보기

Azure Time Series Insights 탐색기는 다음과 같은 7 개의 요소로 구성 됩니다.

[![Azure Time Series Insights 탐색기 개요](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [환경 패널](#1-environment-panel): 모든 Azure Time Series Insights Gen2 환경을 표시 합니다.
1. [탐색 모음](#2-navigation-bar): **분석** 및 **모델** 페이지 간을 전환할 수 있습니다.
1. [계층 구조 트리 및 검색 패널](#3-hierarchy-tree-and-search-panel): 차트를 만들 특정 데이터 요소를 선택 하 고 검색할 수 있습니다.
1. 시계열 [웰](#4-time-series-well): 현재 선택한 데이터 요소를 모두 표시 합니다.
1. [차트 패널](#5-chart-panel): 현재 작업 차트를 표시 합니다.
1. [타임 라인](#6-time-editor-panel): 작업 시간 범위를 수정할 수 있습니다.
1. [앱 바](#7-app-bar): 사용자 관리 옵션 (예: 현재 테 넌 트)을 포함 하 고, 언어 설정을 변경할 수 있습니다.

## <a name="1-environment-panel"></a>1. 환경 패널

환경 패널에는 사용자가 액세스할 수 있는 모든 Azure Time Series Insights Gen2 환경이 표시 됩니다. 이 목록에는 Gen 1 환경 뿐만 아니라 Gen2 환경이 포함 됩니다. 즉시 사용 하려는 환경을 선택 하기만 하면 됩니다.

1. 표시 된 환경 옆에 있는 드롭다운 화살표를 선택 합니다.

   [![환경 패널](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 그런 다음 원하는 환경을 선택 합니다.

## <a name="2-navigation-bar"></a>2. 탐색 모음

  [![탐색 모음](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

탐색 모음을 사용 하 여 두 뷰 중에서 선택 합니다.

* **분석**: 모델링 되거나 모델링 되지 않은 시계열 데이터에 대 한 다양 한 분석을 수행 하 고 차트를 사용 합니다.
* **모델**: 새 Azure Time Series Insights Gen2 형식, 계층 및 인스턴스를 시계열 모델에 푸시하는 데 사용 합니다.

### <a name="model-authoring"></a>모델 작성

Azure Time Series Insights Gen2는 시계열 모델에 대 한 모든 CRUD (만들기, 읽기, 업데이트 및 삭제) 작업을 지원 합니다.

[![모델 검색 패널](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* 시계열 **모델 유형**: 시계열 모델 유형을 사용 하 여 계산을 수행 하는 변수나 수식을 정의할 수 있습니다. 지정 된 시계열 모델 인스턴스와 연결 됩니다. 형식에는 하나 이상의 변수가 포함될 수 있습니다.
* **시계열 모델 계층 구조**: 계층은 데이터의 체계적 조직입니다. 계층은 시계열 모델의 서로 다른 인스턴스 간의 관계를 나타냅니다.
* 시계열 **모델 인스턴스**: 인스턴스는 시계열입니다. 대부분의 경우에는 해당 환경에서 자산의 고유 식별자 인 **DeviceID** 또는 **AssetID**입니다.

시계열 모델에 대 한 자세한 내용은 [시계열 모델](./concepts-model-overview.md)을 읽어 보세요.

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 계층 트리 및 검색 패널

계층 구조 트리와 검색 패널을 사용 하 여 [시계열 모델](./concepts-model-overview.md) 계층 구조를 쉽게 검색 하 고 탐색 하 여 차트에 표시할 특정 시계열 인스턴스를 찾을 수 있습니다. 인스턴스를 선택 하는 경우 해당 인스턴스는 현재 차트에 추가 될 뿐만 아니라 데이터 웰에도 추가 됩니다.

[![계층 트리 및 검색 패널](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

검색 결과 창을 사용 하 여 계층 뷰나 목록 뷰에서 결과를 표시 하 여 표시 하려는 인스턴스를 쉽게 찾을 수도 있습니다.

## <a name="4-time-series-well"></a>4. 시계열 웰

선택한 시계열 모델 인스턴스와 연결 된 인스턴스 필드 및 기타 메타 데이터를 잘 표시 합니다. 오른쪽에 있는 확인란을 선택 하 여 현재 차트의 특정 인스턴스를 숨기 거 나 표시할 수 있습니다.

  [![Gen2 웰](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

요소의 왼쪽에 있는 빨간색 **삭제** (휴지통) 컨트롤을 선택 하 여 현재 데이터에서 특정 데이터 요소를 제거할 수 있습니다. 를 사용 하면 각 요소가 차트에 표시 되는 방식을 제어할 수도 있습니다. Min/max 그림자를 추가 하 고, 데이터 요소를 시간으로 이동 하 고, 인스턴스를 단계별 방식으로 시각화 하도록 선택할 수 있습니다.

또한 탐색 컨트롤을 사용 하 여 시간 이동 및 분산형 차트를 쉽게 만들 수 있습니다.  

  [![잘 레이아웃 옵션](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 다음 메시지가 표시 되 면 선택한 시간 범위 동안 인스턴스에 데이터가 없는 것입니다. 이 문제를 해결 하려면 시간 범위를 늘리거나 인스턴스가 데이터를 푸시하는 지 확인 합니다.
>
> ![데이터 알림 없음](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 차트 패널

차트를 사용 하면 시계열 인스턴스를 선으로 표시할 수 있습니다. 웹 컨트롤을 클릭해서 환경 패널, 데이터 모델 및 시간 범위 제어 패널을 접으면 차트를 확장할 수 있습니다.

  [![Gen2 차트 개요](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **차트 종류**: 시각화에 사용할 수 있는 데이터 요소를 제어 합니다.

1. **간격 크기**: 간격 크기 슬라이더 도구를 사용 하면 동일한 시간 범위에서 간격을 확대 및 축소할 수 있습니다. 이렇게 하면 데이터의 세부적인 고해상도 컷을 검토할 수 있도록, 밀리초 만큼 작은 조각으로 부드러운 추세를 표시 하는 큰 시간 조각 간의 움직임을 보다 정확 하 게 제어할 수 있습니다. 슬라이더의 기본 시작점이 선택에서 가장 최적의 데이터 뷰로 설정 되어 있습니다. 분산 해상도, 쿼리 속도 및 세분성입니다.

1. **확대/축소 및 이동**: 차트를 확대/축소 하 고 이동 하려면이 컨트롤을 선택 합니다.

1. **Y 축 컨트롤**: 사용 가능한 y 축 뷰 옵션을 순환 합니다.

    * `Stacked`: 각 줄에 개별 Y 축이 있습니다.
    * `Overlap`: 선택한 줄을 기준으로 Y 축 데이터를 변경 하 여 동일한 Y 축에 여러 줄을 누적 하는 데 사용 합니다.
    * `Shared`: 모든 Y 축 데이터를 함께 표시 합니다.

1. **Marker 요소**: 현재 선택한 데이터 요소 및 관련 세부 정보입니다.

마우스를 누른 채 선택한 영역을 선택한 끝점으로 끌어와 서 현재 그래프의 데이터 요소를 마우스 **왼쪽 단추로 클릭** 하 여 특정 데이터 조각을 자세히 살펴볼 수 있습니다. 파란색 선택 영역을 **마우스 오른쪽 단추로 클릭** 하 고 아래와 같이 **확대/축소** 를 선택 합니다. 선택한 timespan에서 원격 분석 이벤트를 보고 다운로드할 수도 있습니다.

  [![Gen2 차트 확대/축소](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

**확대/축소** 작업을 수행 하면 선택한 데이터 집합이 표시 됩니다. 서식 컨트롤을 선택 하 여 데이터의 3 가지 y 축 표현을 순환 합니다.

  [![Gen2 차트 y 축](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

여기에는 **겹치는 차트** 의 예가 제공 됩니다.

  [![겹치는 차트 옵션](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

**추가 작업** 단추는 확장 되어 **CSV로 다운로드**를 표시 하 고, **Power BI에 연결**하 고, **차트 데이터를 테이블로 표시**하 고, **원시 이벤트** 옵션을 탐색 합니다.

  [![추가 작업 옵션](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

[Power BI 커넥터](concepts-power-bi.md)에서 **Power BI에 연결** 옵션에 대해 자세히 알아보세요.

## <a name="6-time-editor-panel"></a>6. 시간 편집기 패널

Azure Time Series Insights Gen2 작업 하는 경우 먼저 시간 범위를 선택 합니다. 선택한 시간 범위는 Azure Time Series Insights Gen2 업데이트 위젯을 사용 하 여 조작에 사용할 수 있는 데이터 집합을 제어 합니다.

  [![시간 선택 패널](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 타임 라인의 일부는 웜 저장소에서 사용할 수 있는 데이터의 범위를 나타내기 위해 주황색 또는 주황색으로 강조 표시 됩니다.

다음 웹 컨트롤은 Azure Time Series Insights Gen2에서 작업 시간 범위를 선택 하는 데 사용할 수 있습니다.

  [![탐색 웰 컨트롤](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **내부 날짜 범위 슬라이더 컨트롤**: 원하는 시간 범위 위로 끌어 두 끝점 컨트롤을 사용 합니다. 이 내부 날짜 범위는 외부 날짜 범위 슬라이더 컨트롤에 의해 제한 됩니다.

1. **날짜 범위 단추 늘리기 및 줄이기**: 원하는 간격 중 하나를 선택 하 여 시간 범위를 늘리거나 줄입니다.

1. **시간 범위 축소 컨트롤**:이 웹 컨트롤을 사용 하면 내부 날짜 범위 슬라이더 도구를 제외한 모든 컨트롤을 숨길 수 있습니다.

1. **외부 날짜 범위 슬라이더 컨트롤**: 끝점 컨트롤을 사용 하 여 내부 날짜 범위 컨트롤에 사용할 수 있는 외부 날짜 범위를 선택 합니다.

1. **시간 범위 슬라이더 컨트롤**: 지난 **30 분**, **지난 12 시간**또는 **사용자 지정 범위**와 같이 미리 설정 된 시간 범위 선택 사이를 빠르게 전환 하는 데 사용 합니다. 이 값을 변경하면 내부 크기 슬라이더 도구에서 설명하는 사용 가능한 간격 범위도 변경됩니다.

   [![선택 패널로/에서](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 앱 바

Azure Time Series Insights Gen2 탐색 패널이 앱 위쪽에 표시 됩니다. 다음과 같은 기능을 제공 합니다.

### <a name="current-session-share-link-control"></a>현재 세션 공유 링크 컨트롤

  [![공유 아이콘](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

새 **공유** 아이콘을 선택 하 여 팀과 URL 링크를 공유 합니다.

  [![인스턴스 URL 공유](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>테넌트 섹션

  [![테 넌 트 선택](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 현재 Azure Time Series Insights Gen2 로그인 계정 정보를 표시 합니다.
* 사용 가능한 테마 사이를 전환 하는 데 사용 합니다.
* 이를 사용 하 여 Gen2 [Demo 환경을](https://insights.timeseries.azure.com/preview/demo)볼 수 있습니다.

### <a name="theme-selection"></a>테마 선택

새 테마를 선택 하려면 오른쪽 위 모퉁이에 있는 프로필 아이콘을 선택 합니다. 그런 다음 **테마 변경**을 선택 합니다.

  [![테마 선택](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 프로필 아이콘을 선택 하 여 언어를 선택할 수도 있습니다.

Azure Time Series Insights 탐색기는 두 가지 테마를 지원 합니다.

* **밝은 테마**:이 문서 전체에 표시 되는 기본 테마입니다.
* **어두운 테마**: 다음과 같이 탐색기를 렌더링 합니다.

  [![선택한 짙은 테마](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen-1-environment-controls"></a>Gen 1 환경 컨트롤

### <a name="gen2-terms-panel"></a>Gen2 용어 패널

이 섹션은 업데이트 된 UI에서 탐색기를 사용 하려고 시도 하는 기존 Gen 1 환경에만 적용 됩니다. Gen 1 제품 및 Gen2 제품을 조합 하 여 사용 하는 것이 좋습니다. 기존 UI의 일부 기능을 업데이트 된 탐색기에 추가 했지만 새 Azure Time Series Insights 탐색기에서 Gen 1 환경에 대 한 전체 UI 환경을 얻을 수 있습니다.

계층 구조 대신 Azure Time Series Insights Gen2 용어 창이 표시 됩니다. 용어 패널을 사용 하 여 사용자 환경에서 쿼리를 정의할 수 있습니다. 조건자를 기준으로 데이터를 필터링 하는 데도 사용 됩니다.

  [![Where 쿼리 패널](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Azure Time Series Insights Gen2 용어 편집기 패널에는 다음 매개 변수가 사용 됩니다.

**Where**: where 절을 사용 하 여 다음 표에 나열 된 피연산자 집합을 사용 하 여 이벤트를 신속 하 게 필터링 합니다. 피연산자를 선택하여 검색을 수행하는 경우 조건자는 해당 검색에 따라 자동으로 업데이트됩니다. 지원 되는 피연산자 유형은 다음과 같습니다.

| 작업(Operation)    | 지원되는 형식    | 참고 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | 모든 연산자는 같은 형식이거나 NULL 상수여야 합니다. |
| `HAS` | String | 오른쪽에는 상수 문자열 리터럴만 사용할 수 있습니다. 빈 문자열 및 NULL은 허용 되지 않습니다. |

지원 되는 쿼리 작업 및 데이터 형식에 대해 자세히 알아보려면 [TSX (Time Series Expression)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)를 참조 하세요.

### <a name="examples-of-where-clauses"></a>Where 절의 예

  [![Where 절 예](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Measure**: 현재 차트의 요소로 사용할 수 있는 모든 숫자 열 (**double**)을 표시 하는 드롭다운 목록입니다.

**분할 기준**:이 드롭다운 목록에서 데이터를 그룹화 할 수 있는 사용 가능한 모든 범주 열 (문자열)을 모델에 표시 합니다. 동일한 x 축에 대해 볼 수 있는 최대 5 개의 용어를 추가할 수 있습니다. 원하는 매개 변수를 입력 한 다음 **추가** 를 선택 하 여 새 용어를 추가 합니다.

  [![쿼리 및 필터링 된 뷰 1](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

다음 그림에 표시 된 것 처럼 표시 되는 아이콘을 선택 하 여 차트 패널에서 요소를 표시 하거나 숨길 수 있습니다. 쿼리를 완전히 제거 하려면 빨간색 **X**를 선택 합니다.

  [![쿼리 및 필터링 된 옵션 취소](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>다음 단계

* 사용자 환경에 [데이터를 수집](./concepts-ingestion-overview.md) 하는 방법에 대해 알아봅니다.

* [저장소](concepts-storage.md)에 대 한 문서를 확인 하세요.

* Azure Time Series Insights Gen2의 [데이터 모델링](./concepts-model-overview.md) 에 대해 읽어 보세요.

* 환경을 [진단 하 고 문제를 해결 하는 방법을](./time-series-insights-update-how-to-troubleshoot.md) 알아봅니다.
