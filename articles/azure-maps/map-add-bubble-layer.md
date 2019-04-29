---
title: Azure Maps에 거품형 계층 추가 | Microsoft Docs
description: Javascript 맵에 거품형 계층을 추가하는 방법
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769688"
---
# <a name="add-a-bubble-layer-to-a-map"></a>맵에 거품형 계층 추가

이 문서에서는 맵에 거품형 계층으로 데이터 원본에서 요소 데이터를 렌더링할 수 있는 방법을 보여줍니다. 거품형 계층은 고정된 픽셀 반경을 사용하여 요소를 맵에 원으로 렌더링합니다. 

> [!TIP]
> 기본적으로 거품형 계층은 데이터 원본에 있는 모든 도형의 좌표를 렌더링합니다. 기능 집합을 같은 렌더링 하는 것만 기 하 도형 점 계층을 제한 하는 `filter` 속성은 계층의 `['==', ['geometry-type'], 'Point']` 또는 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` MultiPoint 기능도 포함 하려는 경우.

## <a name="add-a-bubble-layer"></a>거품형 계층 추가

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록에서는 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 개체의 배열이 정의되고 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 개체에 추가됩니다.

[거품형 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)은 [데이터 원본](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 원으로 렌더링합니다. 마지막 코드 블록은 거품형 계층을 만들고 지도에 추가합니다. [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)에서 거품형 계층의 속성을 확인합니다.

Point 개체 배열, 데이터 원본 및 거품형 계층이 생성되고 맵의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 맵이 완전히 로드된 후 원이 표시되도록 합니다.

## <a name="show-labels-with-a-bubble-layer"></a>거품형 계층을 사용하여 레이블 표시

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>를 참조하세요.
</iframe>

위 코드에서는 지도에 데이터를 시각화하고 레이블을 지정하는 방법을 보여 줍니다. 위에서 첫 번째 코드 블록은 지도 개체를 생성합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 개체를 만듭니다. 그런 다음, [data source](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 데이터 원본 개체를 만들고 데이터 원본에 점을 추가합니다.

[거품형 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)은 [데이터 원본](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 원으로 렌더링합니다. 세 번째 코드 블록은 거품형 계층을 만들고 지도에 추가합니다. [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)에서 거품형 계층의 속성을 확인합니다.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다. 마지막 코드 블록은 거품형의 텍스트 레이블을 렌더링하는 기호 계층을 만들고 지도에 추가합니다. [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)에서 기호 계층의 속성을 참조하세요.

데이터 원본 및 계층이 생성되고 맵의 [event listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 함수 내에 추가되어 맵이 완전히 로드된 후 데이터가 표시되도록 합니다.

## <a name="customize-a-bubble-layer"></a>거품형 계층 사용자 지정

거품형 계층에는 몇 가지 스타일 지정 옵션만 있습니다. 사용할 도구는 다음과 같습니다.

<br/>

<iframe height='700' scrolling='no' title='거품형 계층 옵션' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>거품형 계층 옵션</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)