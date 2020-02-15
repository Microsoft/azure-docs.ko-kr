---
title: 지도에 이미지 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 지도에 이미지를 오버레이 하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209718"
---
# <a name="add-an-image-layer-to-a-map"></a>맵에 이미지 계층 추가

이 문서에서는 고정 좌표 집합에 이미지를 오버레이 하는 방법을 보여 줍니다. 지도에 중첩 될 수 있는 다양 한 이미지 유형의 몇 가지 예는 다음과 같습니다.

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

<iframe height='500' scrolling='no' title='단순 이미지 계층' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/eQodRo/'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io'>Pen Simple 이미지 계층</a>을 참조하세요.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>KML 파일을 그라운드 오버레이로 가져오기

이 샘플에서는 KML 그라운드 오버레이 정보를 지도에 이미지 계층으로 추가 하는 방법을 보여 줍니다. KML 접지 오버레이는 북쪽, 남쪽, 동쪽 및 서 부 좌표와 시계 반대 방향 회전을 제공 합니다. 그러나 이미지 계층에는 이미지의 각 모퉁이에 대 한 좌표가 필요 합니다. 이 샘플의 KML 그라운드 오버레이는 Chartres cathedral에 대 한 것 이며 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)에서 원본입니다.

이 코드는 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 클래스의 정적 `getCoordinatesFromEdges` 함수를 사용 합니다. KML 접지 오버레이의 북쪽, 남부, 동부, 서 부 및 회전 정보를 사용 하 여 이미지의 네 모퉁이를 계산 합니다.

<br/>

<iframe height='500' scrolling='no' title='이미지 계층으로 KML 지면 오버레이' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/EOJgpj/'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 Pen <a href='https://codepen.io'>이미지 계층으로 KML 지면 오버레이</a>를 참조하세요.
</iframe>

## <a name="customize-an-image-layer"></a>이미지 계층 사용자 지정

이미지 계층에는 다양 한 스타일 옵션이 있습니다. 사용해 볼 수 있는 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='이미지 계층 옵션' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/RqOGzx/'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io'>이미지 계층 옵션</a>을 참조하세요.
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