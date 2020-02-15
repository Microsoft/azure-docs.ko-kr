---
title: 지도에 다각형 입체 면 계층 추가 | Microsoft Azure 맵
description: 다각형 밀어내기 계층을 Microsoft Azure Maps 웹 SDK에 추가 하는 방법입니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 38a7e6c5f4c139343e735ae82616c11a224ae7ca
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209684"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>지도에 다각형 입체 면 계층 추가

이 문서에서는 다각형 밀어내기 계층을 사용 하 여 `Polygon` 및 `MultiPolygon` 기능 형상의 영역을 입체 셰이프로 렌더링 하는 방법을 보여 줍니다. Azure Maps 웹 SDK는 [확장 된 GeoJSON 스키마](extend-geojson.md#circle)에 정의 된 원 기 하 도형 렌더링을 지원 합니다. 이러한 원은 지도에서 렌더링 될 때 다각형으로 변환 될 수 있습니다. 모든 기능 기 하 도형은 atlas로 래핑할 때 쉽게 업데이트할 수 있습니다 [. Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 클래스입니다.

## <a name="use-a-polygon-extrusion-layer"></a>다각형 입체 면 계층 사용

[다각형 입체 면 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) 을 데이터 원본에 연결 합니다. 그런 다음 맵에 로드 합니다. 다각형 입체 면 계층은 `Polygon` 영역을 입체 도형으로 `MultiPolygon` 기능을 렌더링 합니다. 다각형 입체 면 계층의 `height` 및 `base` 속성은 입체 도형의 너비와 높이의 기본 거리를 **미터**단위로 정의 합니다. 다음 코드에서는 다각형을 만들고 데이터 소스에 추가 하 고 다각형 입체 면 계층 클래스를 사용 하 여 렌더링 하는 방법을 보여 줍니다.

> [!Note]
> 다각형 입체 면 계층에 정의 된 `base` 값은 `height`보다 작거나 같아야 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="돌출 다각형" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 펜 <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>돌출 다각형</a> 을 참조 하세요.</iframe>


## <a name="add-data-driven-polygons"></a>데이터 중심 다각형 추가

다각형 입체 면 레이어를 사용 하 여 choropleth 지도를 렌더링할 수 있습니다. 밀어내기 계층의 `height` 및 `fillColor` 속성을 `Polygon` 및 `MultiPolygon` 기능 기 하 도형에서 통계 변수의 측정으로 설정 합니다. 다음 코드 샘플은 상태별 모집단 밀도의 측정을 기반으로 하는 choropleth의 돌출 된 지도를 보여 줍니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="돌출 choropleth 지도" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 펜 <a href='https://codepen.io/azuremaps/pen/eYYYNox'>돌출 choropleth map</a> 을 참조 하세요.
</iframe>

## <a name="add-a-circle-to-the-map"></a>지도에 원 추가

Azure Maps에는 [여기](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)에 나와 있는 것 처럼 원에 대 한 정의를 제공 하는 GeoJSON 스키마의 확장 된 버전이 사용 됩니다. `Circle`의 `subType` 속성을 사용 하 여 `point` 기능을 만들고 **미터**단위를 나타내는 번호가 매겨진 `Radius` 속성을 만들어 표에서 돌출 원을 렌더링할 수 있습니다. 다음은 그 예입니다.

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps 웹 SDK는 이러한 `Point` 기능을 내부적으로 `Polygon` 기능으로 변환 합니다. 다음 코드 샘플에 표시 된 것 처럼 다각형 밀어내기 계층을 사용 하 여 지도에서 이러한 `Point` 기능을 렌더링할 수 있습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="드 론 어 스페이스 다각형" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 통해 펜 <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>drone에 어 스페이스 다각형</a> 을 참조 하세요.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>다각형 입체 면 계층 사용자 지정

다각형 밀어내기 계층에는 여러 스타일 옵션이 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조 하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [다각형 입체 면 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

추가 리소스:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 사양 확장](extend-geojson.md#circle)
