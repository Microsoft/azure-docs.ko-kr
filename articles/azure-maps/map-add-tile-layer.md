---
title: 지도에 타일 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 지도에서 타일 계층을 오버레이 하는 방법에 대해 설명 합니다. 타일 계층을 사용 하면 지도에서 이미지를 렌더링할 수 있습니다.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988603"
---
# <a name="add-a-tile-layer-to-a-map"></a>맵에 타일 계층 추가

이 문서에서는 지도에서 타일 계층을 오버레이 하는 방법을 보여 줍니다. 타일 계층을 사용하여 Azure Maps 기본 맵 타일의 위에 이미지를 겹칠 수 있습니다. 바둑판식 배열 시스템 Azure Maps에 대한 자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md)를 참조 하세요.

타일 계층이 서버에서 타일에 로드 됩니다. 이러한 이미지는 미리 렌더링 되거나 동적으로 렌더링 될 수 있습니다. 미리 렌더링 된 이미지는 타일 계층에서 인식 하는 명명 규칙을 사용 하 여 서버의 다른 이미지와 마찬가지로 저장 됩니다. 동적으로 렌더링 된 이미지는 서비스를 사용 하 여 실시간으로 가까운 이미지를 로드 합니다. Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 클래스에서 지 원하는 세 가지 타일 서비스 명명 규칙은 다음과 같습니다. 

* X, Y, Zoom 표기법-X는 열이 고 Y는 타일 그리드에서 타일의 행 위치 이며 확대/축소는 확대/축소 수준을 기반으로 하는 값입니다.
* Quadkey notation-x, y 및 zoom 정보를 단일 문자열 값으로 결합 합니다. 이 문자열 값은 단일 타일에 대한 고유 식별자가 됩니다.
* 경계 상자-경계 상자 좌표 형식으로 이미지를 지정 합니다. `{west},{south},{east},{north}`합니다. 이 형식은 일반적으로 [WMS (웹 매핑 서비스)](https://www.opengeospatial.org/standards/wms)에서 사용 됩니다.

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)는 맵에서 큰 데이터 세트를 시각화하는 좋은 방법입니다. 이미지에서 타일 계층을 생성할 수 있을 뿐만 아니라 벡터 데이터도 타일 계층으로 렌더링 될 수 있습니다. 지도 컨트롤은 벡터 데이터를 타일 계층으로 렌더링 하 여 파일 크기가 표시 하는 벡터 데이터 보다 작은 타일을 로드 하기만 하면 됩니다. 이 기술은 일반적으로 지도에 수백만 개의 데이터 행을 렌더링 하는 데 사용 됩니다.

타일 계층에 전달 된 타일 URL은 TileJSON 리소스 또는 다음 매개 변수를 사용 하는 타일 URL 템플릿에 대한 http 또는 https URL 이어야 합니다. 

* `{x}` - 타일의 X 위치입니다. 또한 `{y}` 및 `{z}`가 필요합니다.
* `{y}` - 타일의 Y 위치입니다. 또한 `{x}` 및 `{z}`가 필요합니다.
* `{z}` - 타일의 확대/축소 수준입니다. 또한 `{x}` 및 `{y}`가 필요합니다.
* `{quadkey}` - Bing Maps 타일 시스템 명명 규칙에 따라 타일 Quadkey 식별자입니다.
* `{bbox-epsg-3857}` - EPSG 3857 공간 참조 시스템에서 `{west},{south},{east},{north}` 형식을 사용하는 경계 상자 문자열입니다.
* `{subdomain}`-하위 도메인 값에 대한 자리 표시자 (지정 된 경우 `subdomain`를 추가 합니다.

## <a name="add-a-tile-layer"></a>타일 계층 추가

 이 샘플에서는 타일 집합을 가리키는 타일 계층을 만드는 방법을 보여 줍니다. 이 샘플에서는 x, y, zoom 바둑판식 배열 시스템을 사용 합니다. 이 타일 계층의 원본은 [아이오와 주립 대학교의 Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/)에서 받은 날씨 레이더 오버레이입니다. 방사형 데이터를 볼 때 가장 적합 한 사용자는 지도를 탐색할 때 도시 레이블을 명확 하 게 볼 수 있습니다. 이 동작은 `labels` 계층 아래에 타일 계층을 삽입 하 여 구현할 수 있습니다.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

다음은 위의 기능을 실행 하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='X, Y 및 Z를 사용하는 타일 계층' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>X, Y 및 Z를 사용하는 펜 타일 계층</a>을 참조하세요.
</iframe>

## <a name="customize-a-tile-layer"></a>타일 계층 사용자 지정

타일 계층 클래스에는 다양 한 스타일 옵션이 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='타일 계층 옵션' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>타일 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [이미지 계층 추가](./map-add-image-layer.md)
