---
title: 지도에 컨트롤 추가 | Microsoft Azure 맵
description: Microsoft Azure 맵의 지도에 확대/축소 컨트롤, 피치 컨트롤, 회전 컨트롤 및 스타일 선택기를 추가 하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: bbcfe4935843a00bc5274273e79f4ffc72dc25ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051058"
---
# <a name="add-controls-to-a-map"></a>지도에 컨트롤 추가

이 문서에서는 지도에 컨트롤을 추가 하는 방법을 보여 줍니다. 또한 모든 컨트롤과 [스타일 선택](./choose-map-style.md)으로 지도를 만드는 방법을 알아봅니다.

## <a name="add-zoom-control"></a>확대/축소 컨트롤 추가

확대/축소 컨트롤은 지도를 확대/축소 하는 단추를 추가 합니다. 다음 코드 샘플은 확대/확대 [컨트롤](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 클래스의 인스턴스를 만들고 지도의 오른쪽 아래 모퉁이에 추가 합니다.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='확대/축소 컨트롤 추가' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>확대/축소 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="add-pitch-control"></a>피치 컨트롤 추가

피치 컨트롤은 피치를 기준으로 tilting 하는 단추를 추가 합니다. 다음 코드 샘플에서는 [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 클래스의 인스턴스를 만듭니다. 지도의 오른쪽 위 모퉁이에 PitchControl를 추가 합니다.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='피치 컨트롤 추가' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>피치 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="add-compass-control"></a>나침반 컨트롤 추가

나침반 컨트롤은 지도를 회전 하는 단추를 추가 합니다. 다음 코드 샘플에서는 [Compsmi-s 컨트롤](/javascript/api/azure-maps-control/atlas.control.compasscontrol) 클래스의 인스턴스를 만들고 지도의 왼쪽 아래 모퉁이에 추가 합니다.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.CompassControl(), {
    position: 'bottom-left'
});
```

다음은 위의 기능을 실행하는 전체 코드 샘플입니다.

<br/>

<iframe height='500' scrolling='no' title='회전 컨트롤 추가' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>회전 컨트롤 추가</a>를 참조하세요.
</iframe>

## <a name="a-map-with-all-controls"></a>모든 컨트롤이 있는 지도

여러 컨트롤을 배열에 배치 하 고 한 번에 모두 맵에 추가 하 여 개발을 간소화할 수 있습니다. 다음은이 방법을 사용 하 여 맵에 표준 탐색 컨트롤을 추가 합니다.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

다음 코드 샘플에서는 지도의 오른쪽 위 모퉁이에 확대/축소, 나침반, 피치 및 스타일 선택 컨트롤을 추가 합니다. 자동으로 스택 하는 방법을 확인 합니다. 스크립트의 컨트롤 개체 순서에 따라 지도에서 해당 개체가 표시되는 순서가 결정됩니다. 맵의 컨트롤 순서를 변경 하려면 배열에서 해당 순서를 변경할 수 있습니다.

<br/>

<iframe height='500' scrolling='no' title='모든 컨트롤이 있는 지도' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>모든 컨트롤이 있는 지도</a>를 참조하세요.
</iframe>

스타일 선택 컨트롤은 [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 클래스에 의해 정의 됩니다. 스타일 선택 컨트롤을 사용 하는 방법에 대 한 자세한 내용은 [지도 스타일 선택](choose-map-style.md)을 참조 하세요.

## <a name="customize-controls"></a>컨트롤 사용자 지정

컨트롤을 사용자 지정 하는 다양 한 옵션을 테스트 하는 도구는 다음과 같습니다.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="탐색 제어 옵션" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>탐색 제어 옵션</a> 을 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

사용자 지정 된 탐색 컨트롤을 만들려면 클래스에서 확장 `atlas.Control` 하거나 HTML 요소를 만들고 지도 div 위에 배치 하는 클래스를 만듭니다. 이 UI 컨트롤이 map 함수를 호출 `setCamera` 하 여 맵을 이동 하도록 합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [나침반 컨트롤](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

전체 코드는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [핀 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [팝업 추가](./map-add-popup.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)