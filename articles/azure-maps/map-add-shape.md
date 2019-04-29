---
title: Azure Maps로 도형 추가 | Microsoft Docs
description: Javascript 맵에 도형을 추가하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769518"
---
# <a name="add-a-shape-to-a-map"></a>맵에 도형 추가

이 문서에서는 선 및 다각형 계층을 사용 하 여 지도에 기 하 도형을 렌더링 하는 방법을 보여 줍니다. Azure Maps 웹 SDK는 또한 원 기 하 도형 만들기를 지원에 정의 된 대로 합니다 [GeoJSON 스키마가 확장된](extend-geojson.md#circle)합니다. 모든 기능이 기 하 도형의 업데이트할 수도 있습니다 쉽게 사용 하 여 래핑된 경우 합니다 [셰이프](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스입니다.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>맵에 선 추가

`LineString` 및 `MultiLineString` 기능 경로 및 지도에 윤곽선을 나타내는 데 사용 됩니다.

### <a name="add-a-line"></a>선 추가

<iframe height='500' scrolling='no' title='맵에 선 추가' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>맵에 선 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) 개체가 생성되고 데이터 원본에 추가되었습니다.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 선 개체를 렌더링합니다. 마지막 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요. 데이터 원본 및 선 계층 만들어지고에 맵을 추가 합니다 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 지도 완전히 로드 되 면 줄 표시 되는지 확인 하려면.

### <a name="add-symbols-along-a-line"></a>선 따라 기호를 추가 합니다.

이 샘플에는 지도에 선 따라 화살표 아이콘을 추가 하는 방법을 보여 줍니다. 기호 선 따라 렌더링 되 고 아이콘 회전 기호 계층을 사용 하 여 "line"를 "배치" 옵션을 설정 하는 경우 (0도 = right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="선 따라 화살표를 표시 합니다." src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/drBJwX/'>선 따라 표시 화살표</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

### <a name="line-stroke-gradient"></a> 스트로크 그라데이션 선 추가

줄에 단일 스트로크 색을 적용할 수 있을 뿐 아니라 다음 하나의 선분 전환 표시할 색 그라데이션을 사용 하 여 줄을 채울 수 있습니다. 예를 들어, 줄 그라데이션 개체의 연결 된 줄에서 시간 및 거리 또는 다른 온도 통해 변경 내용을 나타내는 데 사용할 수 있습니다. 데이터 소스에 줄을이 기능을 적용 하기 위해 있어야 합니다 `lineMetrics` 옵션이 true로 설정 하 고 색 그라데이션 식에 전달 될 수 있습니다는 `strokeColor` 선의 옵션입니다. 스트로크 그라데이션 식에 대 한 참조를 `['line-progress']` 식 계산된 줄 메트릭을 표시 하는 데이터 식이 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="스트로크 그라데이션 선" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>스트로크 그라데이션 선</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

### <a name="customize-a-line-layer"></a>선 계층 사용자 지정

선 계층 여러 스타일 지정 옵션입니다. 시도해볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='선 계층 옵션' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>선 계층 옵션</a>을 참조하세요.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>맵에 다각형 추가

`Polygon` 및 `MultiPolygon` 기능은 지도의 영역을 나타내는 데 종종 사용 됩니다. 

### <a name="use-a-polygon-layer"></a>다각형 계층을 사용 하 여 

다각형의 영역을 렌더링 하는 다각형 계층. 

<iframe height='500' scrolling='no' title='맵에 다각형 추가 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>맵에 다각형 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [다각형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)은 좌표의 배열에서 생성되어 데이터 원본에 추가됩니다. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 마지막 코드 블록은 다각형 계층을 만들고 지도에 추가합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본 및 다각형 계층 만들어지고에 맵을 추가 합니다 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 다각형 지도 완전히 로드 되 면 표시 되는지 확인 하려면.

### <a name="use-a-polygon-and-line-layer-together"></a>다각형 및 선 계층을 함께 사용

다각형의 개요를 렌더링 하는 선 계층을 사용할 수 있습니다. 

<iframe height='500' scrolling='no' title='다각형을 추가하기 위한 다각형 및 선 계층' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>다각형을 추가하기 위한 다각형 및 선 계층</a>을 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. [다각형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)은 좌표의 배열에서 생성되어 데이터 원본에 추가됩니다. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 선 배열입니다. [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요. 세 번째 코드 블록은 다각형 및 선 계층을 만듭니다.

마지막 코드 블록은 다각형 및 선 계층을 지도에 추가합니다. 데이터 원본 및 계층 만들어지고에 맵을 추가 합니다 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 다각형 지도 완전히 로드 되 면 표시 되는지 확인 하려면.

> [!TIP]
> 기본적으로 줄 계층을 데이터 소스의 줄 뿐만 아니라 다각형의 좌표를 렌더링 합니다. 기능 집합을 같은 렌더링 하는 것만 LineString 계층을 제한 하는 `filter` 속성은 계층의 `['==', ['geometry-type'], 'LineString']` 또는 `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` MultiLineString 기능도 포함 하려는 경우.

### <a name="fill-a-polygon-with-a-pattern"></a>다각형 패턴으로 채우기

다각형 색을 사용 하 여 입력 하는 것 외에도 이미지 패턴을 사용할 수도 있습니다. 지도 이미지 스프라이트 리소스에는 이미지 패턴을 로드 하 고 다음이 이미지를 참조 합니다 `fillPattern` 다각형 계층 속성.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 채우기 패턴" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>다각형 무늬</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>

### <a name="customize-a-polygon-layer"></a>다각형 계층 사용자 지정

다각형 계층에는 몇 가지 스타일 지정 옵션만 있습니다. 시도해볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>를 참조하세요.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>맵에 원 추가

설명 했 듯이 원에 대 한 정의 제공 하는 GeoJSON 스키마의 확장된 버전을 사용 하 여 azure Maps [여기](extend-geojson.md#circle)합니다. 만들어 맵에 원을 렌더링할 수 있습니다는 `Point` 는 기능을를 `subType` 속성의 값으로 `"Circle"` 및 `radius` 미터 단위의 반지름을 나타내는 수 있는 속성입니다. 예를 들면 다음과 같습니다.

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps 웹 SDK 변환 이러한 `Pooint` 기능으로 `Polygon` 내부적 기능과 같이 다각형 및 선 계층을 사용 하 여 지도에 렌더링할 수 있습니다.

<iframe height='500' scrolling='no' title='맵에 원 추가' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>맵에 원 추가</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. 원은 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 의 [지점](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 있고를 `subType` 속성으로 설정 `"Circle"` 및 `radius` 미터에서 속성 값입니다. 때 사용 하 여 지점 기능을 `subType` 의 `"Circle"` 구조 내의 순환 다각형으로 변환 하는 데이터 원본에 추가 됩니다.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 마지막 코드 블록은 다각형 계층을 만들고 지도에 추가합니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본 및 다각형 계층 만들어지고에 맵을 추가 합니다 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 지도 완전히 로드 되 면 원을 표시 되는지 확인 하려면.

## <a name="make-a-geometry-easy-to-update"></a>기 하 도형을 보다 쉽게 업데이트

A `Shape` 클래스를 래핑하고 [기 하 도형](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) 또는 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 고 쉽게 업데이트 하 고 유지 관리 합니다.
`new Shape(data: Feature<data.Geometry, any>)`는 도형 개체를 생성하고 지정된 기능으로 초기화합니다.

<br/>

<iframe height='500' scrolling='no' title='도형 속성 업데이트' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>도형 속성 업데이트</a>를 참조하세요.
</iframe>

위에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

점은 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 클래스의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 중 하나입니다. 두 번째 코드 블록은 HTML 슬라이더 요소의 반경 값을 초기화한 다음, 점 개체를 생성하고 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스 개체에 래핑합니다.

세 번째 코드 블록은 HTML 범위 슬라이더 요소에서 값을 받고 shape 클래스 [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 메서드를 사용하여 반경 값을 변경하는 함수를 만듭니다.

네 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. 그런 다음, 점이 데이터 원본에 추가됩니다.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 데이터를 지도에 렌더링합니다. 세 번째 코드 블록은 다각형 계층을 만듭니다. [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)에서 다각형 계층의 속성을 참조하세요. 데이터 원본, click 이벤트 처리기 및 다각형 계층 만들어지고에 맵을 추가 합니다 [이벤트 처리기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 지도 완전히 로드 되 면 지점 표시 되는지 확인 하려면.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
