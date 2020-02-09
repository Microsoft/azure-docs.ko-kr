---
title: 지도에 이미지 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 지도에 이미지를 오버레이 하는 방법에 대해 설명 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfdf7dfd4c16f70065e338f8983d2124d3f6f0ef
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933207"
---
# <a name="add-an-image-layer-to-a-map"></a>맵에 이미지 계층 추가

이 문서에서는 지도의 고정 좌표 집합에 이미지를 오버레이 하는 방법을 보여 줍니다. 지도에 종종 중첩 된 이미지 형식의 예는 다음과 같습니다.

* 드 론에서 캡처된 이미지
* Floorplans 빌드
* 기록 또는 기타 특수 지도 이미지
* 작업 사이트의 청사진
* 날씨 레이더 이미지

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 은 지도에서 이미지를 오버레이 하는 쉬운 방법입니다. 브라우저에서 많은 이미지를 로드 하는 데 어려움이 있을 수 있습니다. 이 경우 이미지를 타일로 나누고 [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)로 맵에 로드 하는 것이 좋습니다.

이미지 계층은 다음과 같은 이미지 형식을 지원 합니다.

- JPEG
- PNG
- BMP
- GIF (애니메이션 없음)

## <a name="add-an-image-layer"></a>이미지 계층 추가

다음 코드는 map에서 [1922의 New Jersey 뉴어크 맵](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 이미지를 오버레이 합니다. [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 는 이미지에 URL을 전달 하 고 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`형식으로 4 개의 모퉁이를 조정 하 여 만들어집니다.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

위의 코드를 실행 하는 전체 코드 샘플은 다음과 같습니다.

<br/>

<iframe height='500' scrolling='no' title='단순 이미지 계층' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Pen Simple 이미지 계층</a>을 참조하세요.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>KML 파일을 그라운드 오버레이로 가져오기

다음 샘플에서는 KML 그라운드 오버레이 정보를 지도에 이미지 계층으로 오버레이 하는 방법을 보여 줍니다. KML 접지 오버레이는 북쪽, 남쪽, 동쪽 및 서쪽 좌표를 제공 하는 반면, 이미지 계층에는 이미지의 각 모퉁이에 대한 좌표가 필요 합니다. 이 샘플의 KML 그라운드 오버레이는 Chartres cathedral이 고 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)의 원본입니다.

다음 코드에서는 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 클래스의 정적 `getCoordinatesFromEdges` 함수를 사용 합니다. KML 접지 오버레이의 북쪽, 남부, 동부, 서 부 및 회전 정보에서 이미지의 네 모퉁이를 계산 합니다.

<br/>

<iframe height='500' scrolling='no' title='이미지 계층으로 KML 지면 오버레이' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>이미지 계층으로 KML 지면 오버레이</a>를 참조하세요.
</iframe>

## <a name="customize-an-image-layer"></a>이미지 계층 사용자 지정

이미지 계층에는 다양 한 스타일 옵션이 있습니다. 사용해 볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='이미지 계층 옵션' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>이미지 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [타일 계층 추가](./map-add-tile-layer.md)