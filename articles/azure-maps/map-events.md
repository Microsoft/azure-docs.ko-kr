---
title: 맵 이벤트 처리 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 map 이벤트를 사용 하 여 대화형 웹 SDK 맵을 만드는 방법을 알아봅니다.
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 06fdb9733ad9c173c88936e5502214b58f5d10ee
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985920"
---
# <a name="interact-with-the-map"></a>맵과 상호 작용

이 문서에서는 [map events 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)를 사용 하는 방법을 보여 줍니다. 속성은 지도와 지도의 여러 계층에 대한 이벤트를 강조 표시 합니다. HTML 표식과 상호 작용할 때 이벤트를 강조 표시할 수도 있습니다.

## <a name="interact-with-the-map"></a>맵과 상호 작용

아래 맵을 사용 하 여 재생 하 고 오른쪽에 강조 표시 된 해당 마우스 이벤트를 확인 합니다. **JS 탭**을 클릭하면 JavaScript 코드를 보고 편집할 수 있습니다. **CodePen에서 편집** 을 클릭 하 여 CodePen의 코드를 수정할 수도 있습니다.

<br/>

<iframe height='600' scrolling='no' title='맵 – 마우스 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>맵과 상호 작용 - 마우스 이벤트</a>를 참조하세요.
</iframe>

## <a name="interact-with-map-layers"></a>맵 계층과 상호 작용

다음 코드는 기호 계층과 상호 작용할 때 발생 한 이벤트를 강조 표시 합니다. 기호, 거품형, 선 및 다각형 계층은 모두 동일한 이벤트 집합을 지원 합니다. 열 지도 및 타일 계층은 이러한 이벤트를 지원 하지 않습니다.

<br/>

<iframe height='600' scrolling='no' title='맵 - 계층 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>맵 - 계층 이벤트와 상호 작용</a>을 참조하세요.
</iframe>

## <a name="interact-with-html-marker"></a>HTML 표식과 상호 작용

다음 코드에서는 HTML 마커에 Javascript 맵 이벤트를 추가 합니다. 또한 HTML 표식과 상호 작용할 때 발생한 이벤트의 이름을 강조 표시합니다.

<br/>

<iframe height='500' scrolling='no' title='맵 - HTML 표식 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>맵 - HTML 표식 이벤트와 상호 작용</a>을 참조하세요.
</iframe>

다음 표에서는 지원 되는 모든 map 클래스 이벤트를 보여 줍니다.

| 이벤트               | Description |
|---------------------|-------------|
| `boxzoomend`        | "상자 확대/축소" 상호 작용이 종료 될 때 발생 합니다.|
| `boxzoomstart`      | "상자 확대/축소" 조작이 시작 될 때 발생 합니다.|
| `click`             | 포인팅 장치를 눌렀다가 동일한 지점에서 눌렀다 놓을 때 발생 합니다.|
| `close`             | 수동으로 또는 프로그래밍 방식으로 팝업을 닫을 때 발생 합니다.|
| `contextmenu`       | 마우스 오른쪽 단추를 클릭할 때 발생 합니다.|
| `data`              | 지도 데이터가 로드 되거나 변경 될 때 발생 합니다. |
| `dataadded`         | 셰이프가 `DataSource`에 추가 될 때 발생 합니다.|
| `dataremoved`       | `DataSource`에서 셰이프를 제거할 때 발생 합니다.|
| `datasourceupdated` | `DataSource` 개체가 업데이트 될 때 발생 합니다.|
| `dblclick`          | 포인팅 장치를 맵의 동일한 지점에서 두 번 클릭할 때 발생 합니다.|
| `drag`              | 지도, 팝업 또는 HTML 표식에서 "위아래로 이동" 하는 동안 반복적으로 발생 합니다.|
| `dragend`           | 지도, 팝업 또는 HTML 표식에서 "이동 하기" 상호 작용이 종료 될 때 발생 합니다.|
| `dragstart`         | 지도, 팝업 또는 HTML 표식에서 "이동 하기" 상호 작용이 시작 될 때 발생 합니다.|
| `error`             | 오류가 발생할 때 발생 합니다.|
| `idle`              | <p>맵이 "유휴" 상태가 되기 전에 렌더링 된 마지막 프레임 후에 발생 합니다.<ul><li>카메라 전환이 진행 중이 아닙니다.</li><li>현재 요청 된 모든 타일이 로드 되었습니다.</li><li>모든 페이드/전환 애니메이션이 완료 되었습니다.</li></ul></p>|
| `keydown`           | 키를 누를 때 발생 합니다.|
| `keypress`          | Typable 문자 (ANSI 키)를 생성 하는 키를 누를 때 발생 합니다.|
| `keyup`             | 키를 놓을 때 발생 합니다.|
| `layeradded`        | 지도에 계층이 추가 될 때 발생 합니다.|
| `layerremoved`      | 지도에서 레이어가 제거 될 때 발생 합니다.|
| `load`              | 필요한 모든 리소스를 다운로드 하 고 처음으로 지도를 시각적으로 완료 한 후 즉시 발생 합니다.|
| `mousedown`         | 지도 내에서 포인팅 장치를 누르거나 요소 위에 있을 때 발생 합니다.|
| `mouseenter`        | 포인팅 장치가 처음에 지도 나 요소로 이동 될 때 발생 합니다. |
| `mouseleave`        | 포인팅 장치를 지도 나 요소로 이동할 때 발생 합니다. |
| `mousemove`         | 포인팅 장치를 지도 나 요소 내에서 이동할 때 발생 합니다.|
| `mouseout`          | 지점 장치에서 지도의 캔버스를 벗어나면 발생 합니다.|
| `mouseover`         | 포인팅 장치를 지도 나 요소로 이동할 때 발생 합니다.|
| `mouseup`           | 포인팅 장치가 맵 내에서 해제 되거나 요소 위에 있을 때 발생 합니다.|
| `move`              | 사용자 상호 작용이 나 메서드의 결과로 한 뷰에서 다른 뷰로 애니메이션 전환을 수행 하는 동안 반복적으로 발생 합니다.|
| `moveend`           | 사용자 상호 작용이 나 메서드의 결과로 map이 한 뷰에서 다른 뷰로의 전환을 완료 한 후에 발생 합니다.|
| `movestart`         | 사용자 상호 작용이 나 메서드의 결과로 맵이 한 뷰에서 다른 뷰로 전환을 시작 하기 직전에 발생 합니다.|
| `open`              | 팝업을 수동으로 또는 프로그래밍 방식으로 열 때 발생 합니다.|
| `pitch`             | 사용자 상호 작용이 나 메서드의 결과로 지도의 피치 (기울기)가 변경 될 때마다 발생 합니다.|
| `pitchend`          | 사용자 상호 작용이 나 메서드의 결과로 지도의 피치 (기울기)가 변경 되 면 즉시 발생 합니다.|
| `pitchstart`        | 사용자 상호 작용 또는 메서드의 결과로 지도의 피치 (기울기)가 변경 될 때마다 발생 합니다.|
| `ready`             | 지도를 프로그래밍 방식으로 상호 작용할 수 있도록 하기 위해 필요한 최소한의 map 리소스가 로드 될 때 발생 합니다.|
| `render`            | <p>다음의 결과로 지도를 화면에 그릴 때마다 발생 합니다.<ul><li>지도의 위치, 확대/축소, 피치 또는 베어링에 대한 변경 내용입니다.</li><li>지도의 스타일에 대한 변경 내용입니다.</li><li>`DataSource` 소스에 대한 변경 내용입니다.</li><li>벡터 타일, GeoJSON 파일, 문자 모양 또는 스프라이트를 로드 하는 중입니다.</li></ul></p>|
| `resize`            | 맵의 크기가 조정 된 직후에 발생 합니다.|
| `rotate`            | "회전 하려면 끌기" 작업을 수행 하는 동안 반복적으로 발생 합니다.|
| `rotateend`         | "끌기를 끌 때" 상호 작용이 종료 될 때 발생 합니다.|
| `rotatestart`       | "회전 하려면 끌기" 조작이 시작 될 때 발생 합니다.|
| `shapechanged`      | 셰이프 개체 속성이 변경 될 때 발생 합니다.|
| `sourcedata`        | 원본에 속하는 타일이 로드 되거나 변경 되는 경우를 포함 하 여 지도의 원본 중 하나가 로드 되거나 변경 될 때 발생 합니다. |
| `sourceadded`       | `DataSource` 또는 `VectorTileSource` 맵에 추가 될 때 발생 합니다.|
| `sourceremoved`     | 맵에서 `DataSource` 또는 `VectorTileSource` 제거 될 때 발생 합니다.|
| `styledata`         | 지도의 스타일이 로드 되거나 변경 될 때 발생 합니다.|
| `styleimagemissing` | 존재 하지 않는 이미지 스프라이트에서 레이어가 이미지를 로드 하려고 할 때 발생 합니다. |
| `tokenacquired`     | AAD 액세스 토큰을 가져올 때 발생 합니다.|
| `touchcancel`       | 지도 내에서 touchcancel 이벤트가 발생할 때 발생 합니다.|
| `touchend`          | 지도 내에서 touchend 이벤트가 발생할 때 발생 합니다.|
| `touchmove`         | 지도 내에서 system.windows.uielement.touchmove> 이벤트가 발생할 때 발생 합니다.|
| `touchstart`        | 지도 내에서 touchstart 이벤트가 발생할 때 발생 합니다.|
| `wheel`             | 지도 내에서 마우스 휠 이벤트가 발생할 때 발생 합니다.|
| `zoom`              | 사용자 상호 작용이 나 메서드의 결과로 한 확대/축소 수준에서 다른 확대/축소 수준으로의 애니메이션 전환을 반복 하는 동안 반복적으로 발생 합니다.|
| `zoomend`           | 사용자 상호 작용이 나 메서드의 결과로 map이 한 확대/축소 수준에서 다른 확대/축소 수준으로의 전환을 완료 한 후에 발생 합니다.|
| `zoomstart`         | 사용자 상호 작용이 나 메서드의 결과로 맵이 한 확대/축소 수준에서 다른 확대/축소 수준으로의 전환을 시작 하기 직전에 발생 합니다.|


## <a name="next-steps"></a>다음 단계

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Maps Services 모듈 사용](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)
