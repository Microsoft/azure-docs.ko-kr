---
title: Azure Maps에 열 지도 계층 추가 | Microsoft Docs
description: Javascript 맵에 열 지도 계층을 추가하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 82a6d6b2af7df91696844b09fb7650c547cb6bd1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108637"
---
# <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

열 지도(또는 요소 밀도 맵)는 여러 색을 사용하여 데이터 밀도를 나타내기 위해 사용하는 일종의 데이터 시각화입니다. 맵에 데이터 “핫 스폿”을 표시하기 위해 사용되는 경우가 많으며 큰 요소 데이터 세트를 렌더링하는 좋은 방법입니다.  예를 들어, 기호, 지도 보기 내에서 지점 수천 렌더링 대부분 맵 영역을 포함 및 데이터 많은 파악 하기 어려운 서로 겹치는 여러 기호를 초래 합니다. 하지만, 이 동일한 데이터 세트를 열 지도로 시각화하면 가장 밀도가 높은 요소 데이터가 어디인지, 그리고 다른 영역에 대한 상대 밀도를 쉽게 파악할 수 있습니다. 열 지도를 사용하는 많은 시나리오가 있으며, 다음은 몇 가지 예입니다.

* 온도 데이터는 두 데이터 요소 간 온도에 대한 근사치를 제공하므로 일반적으로 열 지도로 렌더링됩니다.
* 노이즈 센서에 대한 데이터를 열 지도로 렌더링하면 센서가 있는 곳의 노이즈 강도를 보여줄 뿐만 아니라 장거리 열 손실에 대한 분석 정보를 제공합니다. 한 사이트의 노이즈 수준은 높지 않을 수 있지만, 여러 센서의 노이즈 커버리지 영역이 겹치는 경우 이 겹치는 영역에 더 높은 노이즈 수준이 발생하여 열 지도에 표시될 수 있습니다.
* GPS 시각화 각 데이터 요소의 강도 속도 따라 가중치가 적용 된 높이 맵으로 속도 포함 하는 추적이 시기에 차량 속도 여기서 볼 수 있는 좋은 방법입니다.

> [!TIP]
> 기본적으로 열 지도 레이어는 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 렌더링 가리킨 기 하 도형 기능에만 설정할 수 있도록 레이어를 제한 하는 `filter` 속성은 계층의 `['==', ['geometry-type'], 'Point']` 또는 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` MultiPoint 기능도 포함 하려는 경우.

## <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

요소의 데이터 원본을 열 지도로 렌더링하려면 데이터 원본을 `HeatMapLayer` 클래스 인스턴스로 전달하여 맵에 추가하세요(그림 참조).

<br/>

<iframe height='500' scrolling='no' title='단순 열 지도 계층' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>단순 열 지도 계층</a>을 참조하세요.
</iframe>

이 샘플에서는 각 열 요소의 반경은 모든 확대/축소 수준에서 10픽셀입니다. 열 지도 계층에 지도를 추가할 때이 샘플 레이블이 열 지도 위에 명확 하 게 표시 되는 대로 더 나은 사용자 환경을 만들려면 레이블 계층 아래 삽입 합니다. 이 샘플 데이터에서 소싱 된 합니다 [USGS 지진 위험한 프로그램](https://earthquake.usgs.gov/) 지난 30 일 동안에서 발생 한 중요 한 지진을 나타냅니다.

## <a name="customizing-the-heat-map-layer"></a>열 지도 계층 사용자 지정

이전 예제는 반경 및 불투명도 옵션을 설정하여 열 지도를 사용자 지정했습니다. 열 지도 계층은 사용자 지정에 대한 다양한 옵션을 제공합니다.

* `radius`: 각 데이터 요소를 렌더링할 픽셀 반경을 정의합니다. 반경은 정수 또는 식으로 설정할 수 있습니다. 식을 사용 하 여, 지도 (예: 5 마일 반경)에서 일관 된 공간 영역을 나타내는 표시 되는 확대/축소 수준에 따라 radius 확장 가능성이 있습니다.
* `color`: 열 지도에 색이 지정되는 방식을 지정합니다. 색 그라데이션 열 지도에 대개 및를 이룰 수는 `interpolate` 식입니다. 사용 하는 `step` 윤곽선 또는 방사형 스타일 맵을 더 유사한 범위로 시각적 밀도를 분할 하 여 식의 열 지도 색을 지정 합니다. 이러한 색상표는 색을 최소에서 최대 밀도 값까지 정의합니다. 열 지도에 대한 색 값은 `heatmap-density` 값에서 식으로 지정됩니다. 인덱스 보간 식에서 0에 있는 색 또는 단계 식의 기본 색 영역 색을 정의 합니다. 데이터가 없는 경우 배경색을 정의 하려면 사용할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다. 색 식의 예가 있습니다.

| 보간 색 식 | 계단식 색 식 | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50 ' 노랑 '<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

* `opacity`: 열 지도 계층의 불투명도 또는 투명도를 지정합니다.
* `intensity`: Heatmap의 전반적인 강도 늘릴 각 데이터 지점의 가중치로 승수를 적용 하 고 쉽게 시각화할 수 있게 하는 데이터 지점의 가중치의 작은 차이 확인 하는 데 도움이 됩니다.
* `weight`: 기본적으로 모든 데이터 요소의 가중치는 1이므로, 모든 데이터 요소의 가중치는 동일합니다. 가중치 옵션은 승수 역할을 하며 숫자 또는 식으로 설정할 수 있습니다. 가중치가 예를 들어, 숫자 2로 설정된 경우 맵의 각 데이터 요소가 두 배가 되므로, 밀도도 두 배가 됩니다. 숫자가 가중치 옵션을 설정하면 밀도 옵션을 사용하는 것과 비슷한 방식으로 열 지도를 렌더링합니다. 그러나 식을 사용 하는 경우에 각 데이터 지점의 가중치 각 데이터 요소의 속성에 기반 할 수 있습니다. 예를 들어 지진 데이터의 경우 각 데이터 요소가 지진을 나타냅니다. 각 지진 데이터 요소의 중요한 메트릭은 크기 값입니다. 지진은 항상 발생하지만, 대부분 진도가 낮으며 느껴지지 않습니다. 식에서 크기 값을 사용 하 여 각 데이터에 가중치를 할당할 지점은 좀 더 중요 한 지진을 열 지도 내에서 더 잘 나타낼 수 있습니다.
* 최소/최대 확대/축소, 표시 및 필터 등의 기본 계층 옵션 이외에도 데이터 원본을 업데이트하려는 경우에는 `source` 옵션, 데이터 원본이 벡터 타일 원본인 경우에는 `source-layer` 옵션도 있습니다.

다양한 열 지도 계층 옵션을 시험해 볼 수 있는 도구가 있습니다.

<br/>

<iframe height='700' scrolling='no' title='열 지도 계층 옵션' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>열 지도 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>일치 가능한 열 지도

기본적으로 열 지도 계층에 렌더링 하는 데이터 요소의 반지름 모든 확대/축소 수준에 대 한 고정된 픽셀 반지름을 적용 합니다. 지도 확대/축소 하는 대로 함께 데이터 집계와 열 지도 계층에 다르게 보입니다. `zoom` 식에서는 맵의 동일한 물리적 영역을 각 데이터 요소는 각 확대/축소 수준에 대 한 radius 확장 데 사용할 수 있습니다. 더 정적이 고 일관 된 표시 열 지도 계층을 확인 합니다. 맵의 각 확대/축소 수준에 배의 픽셀 세로 및 가로로 이전 확대/축소 수준으로 각 확대/축소 수준으로 두 배로 만듭니다는 반지름 크기 조정 모든 확대/축소 수준에서 일관 된 보이는 열 지도를 만듭니다. 이 사용 하 여 수행할 수 있습니다 합니다 `zoom` 밑 2를 사용 하 여 `exponential interpolation` 아래 샘플에서와 같이 식입니다. 열 지도 확대/축소 수준으로 확장 하는 방법을 보려면 맵에서 확대/축소 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="일치 가능한 열 지도" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>일치 가능한 열 지도</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

> [!TIP]
> 데이터 원본에서 클러스터링을 사용하도록 설정하면 서로 인접한 요소는 클러스터된 요소로 그룹화됩니다. 각 클러스터의 요소 수를 열 지도에 대한 가중치 식으로 사용하면 렌더링해야 할 요소 수를 크게 줄일 수 있습니다. 클러스터의 포인트에 저장 됩니다는 `point_count` 아래와 같이 지점 기능 속성입니다. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 클러스터링 반경이 단 몇 개의 픽셀로 이루어진 경우에는 렌더링에 시각적 차이가 거의 없습니다. 반경이 클수록 각 클러스터로 더 많은 요소를 그룹화하고 열 지도의 성능이 개선되지만 차이가 더 많이 눈에 띄게 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)