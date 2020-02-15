---
title: 맵의 클러스터링 지점 데이터 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 지점 데이터를 클러스터링 하 고 지도에서 렌더링 하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190273"
---
# <a name="clustering-point-data"></a>클러스터링 지점 데이터

지도에서 많은 데이터 요소를 시각화할 때 데이터 요소는 서로 겹칠 수 있습니다. 겹치는 경우 맵을 읽을 수 없게 되 고 사용 하기 어려울 수 있습니다. 지점 데이터 클러스터링은 서로 가까이 있는 지점 데이터를 결합하여 맵에 단일 클러스터형 데이터 요소로 표시하는 프로세스입니다. 사용자가 맵을 확대하면 클러스터는 개별 데이터 요소로 분리됩니다. 많은 수의 데이터 요소를 작업할 때 클러스터링 프로세스를 사용 하 여 사용자 환경을 개선 합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>데이터 원본에서 클러스터링 사용

`cluster` 옵션을 true로 설정 하 여 `DataSource` 클래스에서 클러스터링을 사용 하도록 설정 합니다. 주변 요소를 선택 하 여 클러스터로 결합 하려면 `ClusterRadius` 설정 합니다. `ClusterRadius`의 값은 픽셀 단위입니다. 클러스터링 논리를 사용 하지 않도록 설정할 확대/축소 수준을 지정 하려면 `clusterMaxZoom`을 사용 합니다. 데이터 원본에서 클러스터링을 사용 하도록 설정 하는 방법의 예는 다음과 같습니다.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> 두 데이터 요소를 함께 사용 하는 경우 사용자가 어느 정도를 확대 하든지에 관계 없이 클러스터가 분리 되지 않습니다. 이를 해결 하기 위해 `clusterMaxZoom` 옵션을 설정 하 여 클러스터링 논리를 사용 하지 않도록 설정 하 고 모든 항목을 표시 하기만 하면 됩니다.

다음은 `DataSource` 클래스가 클러스터링을 위해 제공 하는 추가 메서드입니다.

| 방법 | 반환 형식 | Description |
|--------|-------------|-------------|
| getClusterChildren (clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 다음 확대/축소 수준에서 지정된 클러스터의 자식 요소를 검색합니다. 이러한 자식 요소는 도형과 하위 클러스터의 조합일 수 있습니다. 하위 클러스터는 ClusteredProperties와 일치하는 속성이 있는 기능입니다. |
| getClusterExpansionZoom (clusterId: number) | Promise&lt;number&gt; | 클러스터가 확장 또는 분리를 시작하는 확대/축소 수준을 계산합니다. |
| getClusterLeaves (clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 클러스터의 모든 지점을 검색합니다. `limit`를 설정하여 지점의 하위 세트를 반환하고, `offset`을 사용하여 지점을 통해 페이지를 이동합니다. |

## <a name="display-clusters-using-a-bubble-layer"></a>거품형 계층을 사용 하 여 클러스터 표시

거품형 계층은 클러스터형 요소를 렌더링 하는 좋은 방법입니다. 식을 사용 하 여 반지름의 크기를 조정 하 고 클러스터의 요소 수에 따라 색을 변경 합니다. 거품형 계층을 사용 하 여 클러스터를 표시 하는 경우에는 별도의 계층을 사용 하 여 비클러스터형 데이터 요소를 렌더링 해야 합니다.

클러스터의 크기를 거품형 위에 표시 하려면 텍스트와 함께 기호 계층을 사용 하 고 아이콘을 사용 하지 마십시오.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 거품형 계층 클러스터링" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 펜 <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>기본 거품형 계층 클러스터링</a> 을 참조 하세요.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>기호 계층을 사용 하 여 클러스터 표시

데이터 요소를 시각화할 때 기호 계층은 클리너 사용자 인터페이스를 위해 서로 겹치는 기호를 자동으로 숨깁니다. 지도에 데이터 요소 밀도를 표시 하려는 경우이 기본 동작은 바람직하지 않을 수 있습니다. 그러나 이러한 설정은 변경할 수 있습니다. 모든 기호를 표시 하려면 기호 계층 `iconOptions` 속성의 `allowOverlap` 옵션을 `true`로 설정 합니다. 

클러스터를 사용 하 여 클린 사용자 인터페이스를 유지 하면서 데이터 요소 밀도를 표시 합니다. 아래 샘플에서는 기호 계층을 사용 하 여 사용자 지정 기호를 추가 하 고 클러스터 및 개별 데이터 요소를 나타내는 방법을 보여 줍니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터형 기호 계층" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>클러스터링 기호 계층</a> 을 참조 하세요.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>클러스터링 및 열 지도 계층

열 맵은 지도의 데이터 밀도를 표시 하는 좋은 방법입니다. 이 시각화 메서드는 자체에서 많은 수의 데이터 요소를 처리할 수 있습니다. 데이터 요소가 클러스터 된 경우 클러스터 크기가 열 지도의 가중치로 사용 되 면 열 지도에서 더 많은 데이터를 처리할 수 있습니다. 이 옵션을 얻으려면 열 지도 계층의 `weight` 옵션을 `['get', 'point_count']`로 설정 합니다. 클러스터 반지름이 작은 경우 열 맵은 비클러스터형 데이터 요소를 사용 하 여 열 맵과 거의 동일 하 게 보이지만 훨씬 더 잘 수행 됩니다. 그러나 클러스터 반지름이 작을수록 열 지도가 더 정확 하 게 나타나지만 성능상의 이점이 적습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 가중치가 적용 되는 열 지도" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>클러스터 가중치 열 지도</a> 를 참조 하세요.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>클러스터 된 데이터 요소의 마우스 이벤트

클러스터 된 데이터 요소를 포함 하는 계층에서 마우스 이벤트가 발생 하면 클러스터형 데이터 지점은 이벤트를 GeoJSON point 기능 개체로 반환 합니다. 이 지점 기능에는 다음과 같은 속성이 있습니다.

| 속성 이름             | Type    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 기능이 클러스터를 표시하는지 여부를 나타냅니다. |
| `cluster_id`              | 문자열  | DataSource `getClusterExpansionZoom`, `getClusterChildren` 및 `getClusterLeaves` 메서드에 사용할 수 있는 클러스터의 고유 ID입니다. |
| `point_count`             | number  | 클러스터에 포함된 지점의 수입니다.  |
| `point_count_abbreviated` | 문자열  | Long 인 경우 `point_count` 값을 줄여서 표시 하는 문자열입니다. (예: 4,000은 4K)  |

이 예제에서는 클러스터 요소를 렌더링 하 고 클릭 이벤트를 추가 하는 거품형 계층을 사용 합니다. Click 이벤트가 트리거될 때이 코드는 맵을 계산 하 고이를 다음 확대/축소 수준으로 축소 하 여 클러스터를 분리 합니다. 이 기능은 `DataSource` 클래스의 `getClusterExpansionZoom` 메서드와 클릭 된 클러스터형 데이터 요소의 `cluster_id` 속성을 사용 하 여 구현 됩니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 펜 <a href='https://codepen.io/azuremaps/pen/moZWeV/'>클러스터 getClusterExpansionZoom</a> 를 참조 하세요.
</iframe>

## <a name="display-cluster-area"></a>클러스터 영역 표시 

클러스터가 나타내는 지점 데이터는 영역에 분산 됩니다. 이 샘플에서는 마우스로 클러스터를 마우스로 가리킬 때 두 가지 주요 동작이 발생 합니다. 먼저 클러스터에 포함 된 개별 데이터 요소를 사용 하 여 볼록 선체를 계산 합니다. 그러면 볼록 선체가 지도에 표시 되어 영역을 표시 합니다.  볼록 선체는 탄력적 밴드와 같은 일련의 점을 래핑하고 `atlas.math.getConvexHull` 메서드를 사용 하 여 계산할 수 있는 다각형입니다. 클러스터에 포함 된 모든 요소는 `getClusterLeaves` 메서드를 사용 하 여 데이터 원본에서 검색할 수 있습니다.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 영역 볼록 선체" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>클러스터 영역 볼록 선체</a> 를 참조 하세요.
</iframe>

## <a name="aggregating-data-in-clusters"></a>클러스터의 데이터 집계

클러스터에 포함 된 요소 수와 함께 기호를 사용 하 여 클러스터를 표시 하는 경우가 많습니다. 그러나 경우에 따라 추가 메트릭을 사용 하 여 클러스터 스타일을 사용자 지정 하는 것이 좋습니다. 클러스터 집계를 사용 하면 [집계 식](data-driven-style-expressions-web-sdk.md#aggregate-expression) 계산을 사용 하 여 사용자 지정 속성을 만들고 채울 수 있습니다.  클러스터 집계는 `DataSource``clusterProperties` 옵션에서 정의할 수 있습니다.

다음 샘플에서는 집계 식을 사용 합니다. 이 코드는 클러스터에 있는 각 데이터 요소의 엔터티 형식 속성을 기반으로 수를 계산 합니다. 사용자가 클러스터를 클릭 하면 클러스터에 대 한 추가 정보가 포함 된 팝업이 표시 됩니다.

<iframe height="500" style="width: 100%;" scrolling="no" title="클러스터 집계" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>클러스터 집계</a> 를 참조 하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [DataSource 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas. math 네임 스페이스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [열 지도 계층 추가](map-add-heat-map-layer.md)
