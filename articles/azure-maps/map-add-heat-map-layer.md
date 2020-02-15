---
title: 지도에 열 지도 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 지도에 열 지도 계층을 추가 하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4a853871ef5f66881235e5a6ffec0886b81f5a92
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208542"
---
# <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

점 밀도 맵이 라고도 하는 열 맵은 데이터 시각화의 유형입니다. 색 범위를 사용 하 여 데이터 밀도를 나타내는 데 사용 되며 지도에 "핫 스폿" 데이터를 표시 합니다. 열 맵은 많은 수의 점이 있는 데이터 집합을 렌더링 하는 좋은 방법입니다. 

수십 개의 요소를 기호로 렌더링 하면 대부분의 맵 영역을 처리할 수 있습니다. 이 경우 많은 기호가 겹칠 수 있습니다. 데이터를 보다 잘 이해 하기 어렵게 만듭니다. 그러나이 데이터 집합을 열 맵으로 시각화 하면 각 데이터 요소의 밀도 및 상대 밀도를 쉽게 확인할 수 있습니다.

다음과 같은 다양 한 시나리오에서 열 지도를 사용할 수 있습니다.

- **온도 데이터**: 두 데이터 요소 사이의 온도에 대 한 근사치을 제공 합니다.
- **노이즈 센서에 대 한 데이터**: 센서가 있는 노이즈의 강도 뿐만 아니라 거리를 통한 분산에 대 한 통찰력을 제공할 수도 있습니다. 한 사이트의 노이즈 수준이 높지 않을 수 있습니다. 여러 센서의 노이즈 범위 영역이 겹치면 겹치는 영역에서 더 높은 의미의 소음을 겪을 수 있습니다. 따라서 겹쳐진 영역은 열 지도에 표시 됩니다.
- **GPS 추적**: 속도를 가중치 높이 맵으로 포함 합니다. 여기서 각 데이터 요소의 강도는 속도를 기반으로 합니다. 예를 들어이 기능을 통해 차량의 속도를 빠르게 확인할 수 있습니다.

> [!TIP]
> 열 지도 계층은 기본적으로 데이터 원본에 있는 모든 기 하 도형의 좌표를 렌더링 합니다. 점 기 하 도형 기능만 렌더링 하도록 계층을 제한 하려면 계층의 `filter` 속성을 `['==', ['geometry-type'], 'Point']`로 설정 합니다. MultiPoint 기능도 포함 하려면 계층의 `filter` 속성을 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`로 설정 합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>열 지도 계층 추가

지점의 데이터 소스를 열 맵으로 렌더링 하려면 데이터 소스를 `HeatMapLayer` 클래스의 인스턴스로 전달 하 고 맵에 추가 합니다.

다음 코드에서 각 열 점은 모든 확대/축소 수준에서 10 픽셀의 반지름을 갖습니다. 더 나은 사용자 환경을 보장 하기 위해 열 맵은 레이블 계층 아래에 있습니다. 레이블은 명확 하 게 표시 됩니다. 이 샘플의 데이터는 [USGS 지진 위험 프로그램](https://earthquake.usgs.gov/)에서 가져온 것입니다. 최근 30 일 동안 발생 한 중요 한 지진입니다.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

위의 코드를 실행 하는 전체 코드 샘플은 다음과 같습니다.

<br/>

<iframe height='500' scrolling='no' title='단순 열 지도 계층' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/gQqdQB/'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io'>단순 열 지도 계층</a>을 참조하세요.
</iframe>

## <a name="customize-the-heat-map-layer"></a>열 지도 계층 사용자 지정

이전 예제는 반경 및 불투명도 옵션을 설정하여 열 지도를 사용자 지정했습니다. 열 지도 계층은 다음을 비롯 한 사용자 지정을 위한 몇 가지 옵션을 제공 합니다.

* `radius`: 각 데이터 요소를 렌더링할 픽셀 반지름을 정의 합니다. 반지름을 고정 숫자나 식으로 설정할 수 있습니다. 식을 사용 하 여 확대/축소 수준에 따라 반지름의 크기를 조정 하 고 지도의 일관 된 공간 영역을 나타낼 수 있습니다 (예: 5 마일 반지름).
* `color`: 열 지도의 색이 지정 되는 방법을 지정 합니다. 색 그라데이션은 열 지도의 일반적인 기능입니다. `interpolate` 식으로 효과를 달성할 수 있습니다. 열 지도를 색으로 표시 하는 데 `step` 식을 사용할 수도 있습니다. 밀도를 윤곽선 또는 방사형 스타일 맵과 비슷한 범위로 시각적으로 나눕니다. 이러한 색상표는 색을 최소에서 최대 밀도 값까지 정의합니다. 

  열 맵의 색 값을 `heatmap-density` 값에 대 한 식으로 지정 합니다. "보간" 식의 인덱스 0에 데이터가 정의 되어 있지 않은 영역의 색 또는 "단계별" 식의 기본 색입니다. 배경색을 정의 하는 데이 값을 사용할 수 있습니다. 일반적으로이 값은 투명 또는 반투명 검정으로 설정 됩니다. 
   
  색 식의 예는 다음과 같습니다.

  | 보간 색 식 | 단계별 색 식 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, ' 녹색 ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, ' 노랑 ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, ' red '<br/>\] |   

- `opacity`: 열 지도 계층이 불투명 하거나 투명 하 게 지정 되는 방식을 지정 합니다.
- `intensity`: 각 데이터 요소의 가중치에 승수를 적용 하 여 열 지도의 전체 강도를 늘립니다. 이로 인해 데이터 요소의 가중치가 다르므로 시각화를 쉽게 만들 수 있습니다.
- `weight`: 기본적으로 모든 데이터 요소의 가중치는 1 이며 동일 하 게 가중치가 적용 됩니다. Weight 옵션은 승수 역할을 하며 숫자 또는 식으로 설정할 수 있습니다. 숫자가 가중치로 설정 된 경우 맵에 각 데이터 요소를 두 번 배치 하는 것과 동일 합니다. 예를 들어 가중치가 2 인 경우 밀도는 2 배가 됩니다. 숫자가 가중치 옵션을 설정하면 밀도 옵션을 사용하는 것과 비슷한 방식으로 열 지도를 렌더링합니다. 

  그러나 식을 사용 하는 경우 각 데이터 요소의 가중치는 각 데이터 요소의 속성을 기반으로 할 수 있습니다. 예를 들어 각 데이터 요소가 지진을 나타내는 경우 크기 값은 각 지진 데이터 요소에 대 한 중요 한 메트릭입니다. 지진은 항상 발생 하지만 대부분 낮은 크기를 가지 며,이는 발견 되지 않습니다. 식에서 크기 값을 사용 하 여 각 데이터 요소에 가중치를 할당 합니다. 크기 값을 사용 하 여 무게를 할당 하면 열 지도 내에서 지진의 의미를 보다 잘 표현할 수 있습니다.
- `source` 및 `source-layer`: 데이터 원본을 업데이트할 수 있습니다.

다른 열 지도 계층 옵션을 테스트 하는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='열 지도 계층 옵션' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/WYPaXr/'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io'>열 지도 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>일관 된 확대/열 지도

기본적으로 열 지도 계층에 렌더링 되는 데이터 요소의 반지름에는 모든 확대/축소 수준에 대 한 고정 픽셀 반지름이 있습니다. 지도를 확대할 때 데이터가 함께 집계 되 고 열 지도 계층이 다르게 보입니다. 

각 데이터 요소가 지도의 동일한 실제 영역을 포함 하도록 각 확대/축소 수준에 대 한 반지름의 크기를 조정 하려면 `zoom` 식을 사용 합니다. 이 식은 열 지도 계층을 더 정적이 고 일관 되 게 보이게 합니다. 지도의 각 확대/축소 수준에는 이전 확대/축소 수준과 가로 및 세로로 두 배의 픽셀이 있습니다. 

각 확대/축소 수준에서 두 배가 되도록 반지름의 크기를 조정 하면 모든 확대/축소 수준에서 일치 하는 열 지도를 만듭니다. 이러한 크기 조정을 적용 하려면 다음 샘플과 같이 기본 2 `exponential interpolation` 식으로 `zoom`를 사용 합니다. 지도를 확대 하 여 확대/축소 수준으로 열 지도의 크기를 조정 하는 방법을 확인 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="일관 된 확대/열 지도" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 일치 확대/ <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>열 지도</a> 를 볼 수 있습니다.
</iframe>

> [!TIP]
> 데이터 원본에서 클러스터링을 사용 하도록 설정 하면 서로 가까운 점이 클러스터형 점으로 그룹화 됩니다. 각 클러스터의 점 개수를 열 지도의 가중치 식으로 사용할 수 있습니다. 이렇게 하면 렌더링 되는 지점의 수를 크게 줄일 수 있습니다. 클러스터의 점 개수는 point 기능의 `point_count` 속성에 저장 됩니다. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 클러스터링 반지름이 몇 픽셀만 이면 렌더링에 약간의 시각적 차이가 있습니다. 더 큰 radius 그룹은 각 클러스터에 대 한 더 많은 요소를 추가 하 고 열 지도의 성능을 향상 시킵니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
