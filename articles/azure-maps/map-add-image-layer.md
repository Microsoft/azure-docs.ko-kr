---
title: Azure Maps에 이미지 계층 추가 | Microsoft Docs
description: Javascript 맵에 이미지 계층을 추가하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769569"
---
# <a name="add-an-image-layer-to-a-map"></a>맵에 이미지 계층 추가

이 문서에서는 맵에서 이미지를 좌표의 고정 세트에 오버레이할 수 있는 방법을 보여 줍니다. 맵에서 이미지를 오버레이를 완료하는 많은 시나리오가 있습니다. 맵에서 종종 오버레이되는 이미지 형식의 몇 가지 예제입니다.

* 드론에서 캡처된 이미지입니다.
* 빌딩 평면도입니다.
* 역사적 또는 기타 특수한 맵 이미지입니다.
* 작업 사이트의 청사진입니다.
* 날씨 레이더 이미지입니다.

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)는 맵에 이미지를 오버레이할 수 있는 빠르고 쉬운 방법입니다. 그러나 이미지가 클 경우 브라우저는 이미지를 로드하느라 버거울 수 있습니다. 이 경우에는 이미지를 타일로 나눈 후 [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)로 맵에 로드해 보세요.

## <a name="add-an-image-layer"></a>이미지 계층 추가

이 샘플에서는 맵에 [1922년 뉴어크 뉴저지 맵](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)의 이미지를 오버레이하는 방법을 보여줍니다.

<br/>

<iframe height='500' scrolling='no' title='단순 이미지 계층' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Pen Simple 이미지 계층</a>을 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`에서 4개의 모서리의 이미지 및 좌표에 대한 URL을 전달하면 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)가 만들어집니다.

## <a name="import-a-kml-ground-overlay"></a>KML 지면 오버레이 가져오기

이 샘플은 KML 지면 오버레이 정보를 맵에서 이미지 계층으로 오버레이하는 방법을 보여줍니다. KML 지면 오버레이는 북, 남, 동, 서 좌표 및 시계반대방향 회전을 제공하며, 여기서 이미지 계층은 이미지의 각 모서리에 대한 좌표를 예상합니다. 이 샘플에서 KML 지면 오버레이는 출처가 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)인 샤르트르 대성당입니다.

<br/>

<iframe height='500' scrolling='no' title='이미지 계층으로 KML 지면 오버레이' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>이미지 계층으로 KML 지면 오버레이</a>를 참조하세요.
</iframe>

위의 코드는 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 클래스의 고정 `getCoordinatesFromEdges` 함수를 사용하여 KML 지면 오버레이에서 북, 남, 동, 서 및 회전 정보로부터 이미지의 네 모서리를 계산합니다.


## <a name="customize-an-image-layer"></a>이미지 계층 사용자 지정

이미지 계층에 여러 스타일 지정 옵션이 있습니다. 사용할 도구는 다음과 같습니다.

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