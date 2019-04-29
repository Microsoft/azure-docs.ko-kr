---
title: Azure Maps에서 지점 데이터를 클러스터링 | Microsoft Docs
description: 웹 SDK의 지점 데이터를 클러스터링 하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795937"
---
# <a name="clustering-point-data"></a>지점 데이터를 클러스터링합니다.

지도에 여러 데이터 요소를 시각화 하는 경우 지점 서로 겹치는, 지도 혼잡 찾아 보고를 사용 하는 것이 어려워집니다. 이 사용자 환경을 개선 하기 위해 지점 데이터의 클러스터링을 사용할 수 있습니다. 지점 데이터를 클러스터링 하는 것은 서로 가까이 있는 지점 데이터 결합 및 단일 클러스터 된 데이터 지점으로 맵에서 나타내는 프로세스입니다. 사용자를 map으로 확대/축소, 처럼 클러스터 나눌 해당 개별 데이터 요소.

## <a name="enabling-clustering-on-a-data-source"></a>데이터 원본에 대해 클러스터링을 사용 하도록 설정

클러스터링 쉽게에서 사용할 수 있습니다 합니다 `DataSource` 설정 하 여 클래스를 `cluster` 옵션을 true로 합니다. 또한 클러스터로 결합 하려면 주변 지점 선택 픽셀 반지름 설정할 수 있습니다는 `clusterRadius` 확대/축소 수준을 사용 하 여 클러스터링 논리를 사용 하지 않도록 설정 하는 지정할 수 있습니다는 `clusterMaxZoom` 옵션입니다. 데이터 원본에서 클러스터링을 사용 하도록 설정 하는 방법의 예는 다음과 같습니다.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> 두 개의 데이터 요소가 지 면에서 서로 가까이 인 경우 클러스터에 얼마나 근접 사용자 확대에 관계 없이 떨어져 중지 하지 않아야 합니다. 이 해결 하기 위해 설정할 수 있습니다는 `clusterMaxZoom` 클러스터링 논리를 사용 하지 않도록 설정 하기만 하면 모든 항목을 표시 하는 확대/축소 수준에서 지정 하는 데이터 원본 옵션입니다.

`DataSource` 클래스도 클러스터링과 관련 된 다음 메서드를 포함 합니다.

| 방법 | 반환 형식 | 설명 |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | 약속&lt;기능&lt;기 하 도형 모든&gt; \| 셰이프&gt; | 다음 확대/축소 수준에서 지정 된 클러스터의 자식을 검색합니다. 이러한 자식 모양 및 subclusters의 조합일 수 있습니다. subclusters ClusteredProperties 일치 하는 속성을 사용 하 여 기능 됩니다. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | 클러스터는 시작 확장 또는 분리를 확대/축소 수준을 계산 합니다. |
| getClusterLeaves (clusterId: 수를 제한 합니다: number, 오프셋: 수) | 약속&lt;기능&lt;기 하 도형 모든&gt; \| 셰이프&gt; | 클러스터의 모든 요소를 검색합니다. 설정 된 `limit` 요소의 하위 집합을 반환 하 고 사용 하는 `offset` 지점을 통해 페이지로 합니다. |

## <a name="display-clusters-using-a-bubble-layer"></a>거품형 계층을 사용 하 여 클러스터를 표시 합니다.

거품형 계층에는 쉽게 반지름을 확장할 수 있으며 식을 사용 하 여 클러스터에 있는 점의 개수에 따라 색을 변경 하는 대로 클러스터 된 요소를 렌더링 하는 좋은 방법입니다. 거품형 계층을 사용 하 여 클러스터를 표시 하는 경우 클러스터링 해제 된 데이터 요소를 렌더링 하기 위한 별도 계층을도 사용 해야 합니다. 종종 수 있으려면 또한 거품을 기반으로 클러스터의 크기를 표시 하는 것이 유용 합니다. 이 동작을 달성 하기 위해 텍스트와 아이콘 없음 기호 계층을 사용할 수 있습니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 거품형 계층 클러스터링" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>기본 거품형 계층 클러스터링</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>기호 계층을 사용 하 여 클러스터를 표시 합니다.

그러나 기호 계층을 자동으로 숨기려면는 기본적으로 사용 하 여 지점 데이터를 시각화 기호 겹치는 클리너 환경을 만들 서로 때 맵에서 지점이 높지 않을 원하는 환경이 데이터의 밀도 확인 하려는 경우. 설정 합니다 `allowOverlap` 기호 계층의 옵션 `iconOptions` 속성을 `true` 이 환경을 사용 하지 않도록 설정 하지만 표시 되 고 모든 기호 생성 됩니다. 클러스터링을 사용 하 여 정리 훌륭한 사용자 환경을 구축 하는 동안 모든 데이터의 밀도 볼 수 있습니다. 이 샘플에서는 클러스터 및 개별 데이터 요소를 나타내는 사용자 지정 기호를 사용 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 된 기호 계층" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>클러스터형 기호 레이어</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>클러스터링 및 열 계층을 매핑합니다.

열 지도 지도에 데이터의 밀도 표시할 수 있는 좋은 방법입니다. 이 시각화는 다 수의 데이터 지점 자체적으로 처리할 수 있습니다. 있지만 데이터 요소에 클러스터 되 고 클러스터 크기가 열 지도의 가중치로 사용 됩니다. 더 많은 데이터를 처리할 수 있습니다. 설정 된 `weight` 에 열 지도 계층의 옵션 `['get', 'point_count']` 이렇게 하려면. 클러스터 radius 작을 때 열 지도 클러스터링 해제 된 데이터 요소를 사용 하 여 열 지도 거의 동일한 보이지만 성능이 향상 됩니다. 그러나 더 적은 클러스터 radius, 보다 정확한 열 지도 않고 됩니다 성능 더 적은 노력으로 혜택.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 가중치 열 지도" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>클러스터 가중치 열 지도</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>클러스터 된 데이터 요소에 마우스 이벤트

클러스터 된 데이터 요소를 포함 하는 계층의 마우스 이벤트가 발생할 때 클러스터 된 데이터 요소의 이벤트 GeoJSON 지점 기능 개체로 반환 됩니다. 이 지점 기능 다음 속성을 갖습니다.

| 속성 이름 | 형식 | 설명 |
|---------------|------|-------------|
| cluster | 부울 | 기능에 클러스터를 나타내는지 여부를 나타냅니다. |
| cluster_id | 문자열 | 데이터 소스를 사용 하 여 사용할 수 있는 클러스터에 대 한 고유 ID `getClusterExpansionZoom`, `getClusterChildren`, 및 `getClusterLeaves` 메서드. |
| point_count | number | 클러스터에 포함 된 요소의 수입니다. |
| point_count_abbreviated | 문자열 | 긴 경우 point_count 값을 줄여서 표시 하는 문자열입니다. (예: 4k가 4,000) |

이 예제에서는 클러스터 요소를 렌더링 하며 클릭 이벤트를 추가 하는 거품형 계층은 트리거되면 계산 및 지도는 클러스터의 연결이 끊어집니다 분리를 사용 하 여 다음 확대/축소 수준으로 확대/축소를 `getClusterExpansionZoom` 메서드는 `DataSource` 클래스 및는 `cluster_id` 속성을 클릭 한 데이터 요소를 클러스터링 합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/moZWeV/'>클러스터 getClusterExpansionZoom</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

## <a name="display-cluster-area"></a>클러스터 영역 표시 

클러스터를 나타내는 지점 데이터 영역을 통해 분산 됩니다. 이 예제의 마우스 (리프)를 포함 하는 지점 (convex hull)을 계산 하는 데 사용 되며 영역을 표시 하려면 지도에 표시 하는 개별 데이터는 클러스터 위로 가져갈 때. 클러스터에 포함 된 모든 요소를 사용 하 여 데이터 원본에서 검색할 수는 `getClusterLeaves` 메서드. 점의 집합을 래핑하는 다각형을 탄력적 대역 외 등을 사용 하 여 계산할 수 있습니다 (convex hull)은 여 `atlas.math.getConvexHull` 메서드.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 영역 (convex hull)" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>클러스터 영역 (convex hull)</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [DataSource 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math 네임 스페이스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [열 지도 계층 추가](map-add-heat-map-layer.md)
