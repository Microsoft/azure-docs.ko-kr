---
title: Azure Maps로 팝업 추가 | Microsoft Docs
description: Javascript 맵에 팝업을 추가하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a6c8a8aa954379036ce566a205b8cb4e97952727
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769552"
---
# <a name="add-a-popup-to-the-map"></a>맵에 팝업 추가

이 문서에서는 맵에 있는 지점에 팝업을 추가하는 방법을 보여 줍니다.

## <a name="understand-the-code"></a>코드 이해

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Azure Maps를 사용하여 팝업 추가' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)가 제공하는 <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure Maps를 사용하여 팝업 추가</a> 펜을 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 지도 개체를 만듭니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요. 그러면 팝업 안에 HTML 콘텐츠가 표시됩니다.

두 번째 코드 블록에서는 데이터 원본 개체가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 클래스를 사용하여 생성됩니다. 점은 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 클래스의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 중 하나입니다. 그러면 이름 및 설명 속성이 있는 점 개체가 만들어져 데이터 원본에 추가됩니다.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다.  기호 계층은 세 번째 코드 블록에서 생성됩니다. 데이터 원본이 기호 계층에 추가된 다음, 맵에 추가됩니다.

네 번째 코드 블록은 `new atlas.Popup()`을 통해 [팝업 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)를 만듭니다. position 및 pixelOffset과 같은 팝업 속성은 [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)의 일부입니다. PopupOptions는 popup 생성자나 popup 클래스의 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 함수를 통해 정의할 수 있습니다. 기호 계층에 대한 `mouseover` 이벤트 수신기가 생성됩니다.

마지막 코드 블록에서는 `mouseover` 이벤트 수신기에 의해 트리거되는 함수가 생성됩니다. 여기서 팝업 속성 및 콘텐츠를 설정하고 맵에 팝업 개체를 추가합니다.

## <a name="reusing-a-popup-with-multiple-points"></a>여러 점을 사용하여 팝업을 다시 사용

점이 많이 있고 한 번에 하나의 팝업을 표시하려는 경우 각 점 기능에 대해 팝업을 만드는 대신 한 팝업을 만들어 다시 사용하는 것이 가장 좋은 방법입니다. 이 작업을 수행하면 애플리케이션에서 생성되는 DOM 요소 수가 크게 줄어들어 더 나은 성능을 제공할 수 있습니다. 이 샘플에서는 3개 점 기능을 만듭니다. 그 중 하나를 클릭하면 해당 점 기능에 대한 콘텐츠와 함께 팝업이 표시됩니다.

<br/>

<iframe height='500' scrolling='no' title='여러 점을 사용하여 팝업을 다시 사용' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>여러 점을 사용하여 팝업을 다시 사용</a>을 참조하세요.
</iframe>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [팝업](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

전체 코드 샘플에 대한 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML 표식 추가](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [도형 추가](./map-add-shape.md)