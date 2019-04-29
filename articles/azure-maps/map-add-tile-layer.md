---
title: Azure Maps에 타일 계층 추가 | Microsoft Docs
description: Javascript 맵에 타일 계층을 추가하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3a773c24993d229f20df698113ff7535fea634ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769229"
---
# <a name="add-a-tile-layer-to-a-map"></a>맵에 타일 계층 추가

이 문서에서는 맵에 타일 계층을 오버레이하는 방법을 보여줍니다. 타일 계층을 사용하여 Azure Maps 기본 맵 타일의 위에 이미지를 겹칠 수 있습니다. Azure Maps의 타일 배치 체계에 대한 자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md) 설명서에서 볼 수 있습니다.

타일 계층은 서버에서 타일에 로드됩니다. 이러한 이미지는 타일 계층을 이해하는 명명 규칙 또는 즉석에서 이미지를 생성하는 동적 서비스를 사용하는 서버에 있는 다른 이미지처럼 미리 렌더링되거나 저장될 수 있습니다. Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 클래스로 지원되는 세 가지 다른 타일 서비스 명명 규칙이 있습니다. 

* X, Y, 확대/축소 표기법-확대/축소 수준에 따라 타일 그리드에서 x는 열 위치이고 y는 행 위치입니다.
* Quadkey 표기법-x, y 조합은 정보를 타일의 고유 식별자인 단일 문자열 값으로 확대/축소합니다.
* 경계 상자-경계 상자 좌표를 사용하여 일반적으로 [웹 매핑 서비스(WMS)](https://www.opengeospatial.org/standards/wms)에 사용하는 `{west},{south},{east},{north}` 형식으로 이미지를 지정할 수 있습니다.

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)는 맵에서 큰 데이터 세트를 시각화하는 좋은 방법입니다. 이미지에서 타일 계층을 생성할 있을 뿐 아니라 타일 계층으로 벡터 데이터도 렌더링할 수 있습니다. 맵 컨트롤은 타일 계층으로 벡터 데이터를 렌더링함으로써, 맵 컨트롤은 표현하는 벡터 데이터보다 파일 크기가 훨씬 더 작은 타일만 로드해야 합니다. 맵에서 데이터 행 수백만 개를 렌더링해야 하는 대부분의 사용자가 이 기술을 사용합니다.

타일 계층으로 전달된 타일 URL은 다음 매개 변수를 사용하는 TileJSON 리소스에 대한 http/https URL 또는 타일 URL 템플릿이어야 합니다. 

* `{x}` - 타일의 X 위치입니다. 또한 `{y}` 및 `{z}`가 필요합니다.
* `{y}` - 타일의 Y 위치입니다. 또한 `{x}` 및 `{z}`가 필요합니다.
* `{z}` - 타일의 확대/축소 수준입니다. 또한 `{x}` 및 `{y}`가 필요합니다.
* `{quadkey}` - Bing Maps 타일 시스템 명명 규칙에 따라 타일 Quadkey 식별자입니다.
* `{bbox-epsg-3857}` - EPSG 3857 공간 참조 시스템에서 `{west},{south},{east},{north}` 형식을 사용하는 경계 상자 문자열입니다.
* `{subdomain}` - 지정된 경우 하위 도메인 값이 추가될 자리 표시자입니다.

## <a name="add-a-tile-layer"></a>타일 계층 추가

 이 샘플은 x, y, 확대/축소 타일 배치 체계를 사용하는 타일 세트를 가리키는 타일 계층을 만드는 방법을 보여줍니다. 이 타일 계층의 원본은 [아이오와 주립 대학교의 Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/)에서 받은 날씨 레이더 오버레이입니다.

<br/>

<iframe height='500' scrolling='no' title='X, Y 및 Z를 사용하는 타일 계층' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>X, Y 및 Z를 사용하는 펜 타일 계층</a>을 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서 타일 서비스, 타일 크기 및 불투명도로 서식이 지정된 URL을 전달하면 반투명한 [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)가 만들어집니다. 또한 맵에 타일 계층을 추가하는 경우에는 `labels` 계층 아래에 추가되어 레이블이 선명하게 표시됩니다.

## <a name="customize-a-tile-layer"></a>타일 계층 사용자 지정

타일 계층에만 다양한 스타일링 옵션이 있습니다. 사용할 도구는 다음과 같습니다.

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
> [이미지 레이어를 추가 합니다.](./map-add-image-layer.md)
