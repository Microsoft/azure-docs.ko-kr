---
title: Azure Maps로 맵 만들기 | Microsoft Docs
description: Javascript 맵을 만드는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 222fc5e9083c03ff0d4e31927363c5f517cf32a9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108602"
---
# <a name="create-a-map"></a>맵 만들기

이 문서에서는 맵을 만들고 맵을 애니메이션하는 방법을 보여줍니다.  

## <a name="understand-the-code"></a>코드 이해

두 가지 방법으로 맵을 만들 수 있습니다. 중심점 및 확대/축소 수준을 지정하여 맵의 카메라를 설정하거나 남서쪽 및 북동쪽 경계 지점을 지정하여 맵의 카메라 범위를 설정할 수 있습니다.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>카메라 설정

<iframe height='500' scrolling='no' title='CameraOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Azure Location Based Services에서 제공되는 `CameraOptions` </a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

위의 코드에서 [맵 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)는 `new atlas.Map()`을 통해 만들어지고 중심 및 확대/축소가 설정됩니다. 중심 및 확대/축소 수준과 같은 맵 속성은 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)의 일부입니다.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>카메라 경계 설정

<iframe height='500' scrolling='no' title='CameraBoundsOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Azure Maps에서 제공되는 `CameraBoundsOptions`</a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

위의 코드에서 [맵 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)는 `new atlas.Map()`을 통해 생성됩니다. `CameraBoundsOptions`와 같은 맵 속성은 맵 클래스의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 함수를 통해 정의될 수 있습니다. 경계 및 안쪽 여백 속성은 `setCamera`를 사용하여 설정됩니다.

### <a name="animate-map-view"></a>맵 보기 애니메이션

<iframe height='500' scrolling='no' title='맵 보기 애니메이션' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 제공되는 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>맵 보기 애니메이션</a>을 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 `new atlas.Map()`을 통해 [맵 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)를 만듭니다. 중심 및 확대/축소 수준과 같은 맵 속성은 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)의 일부입니다. `CameraOptions`는 맵 생성자에서 정의하거나 맵 클래스의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 함수를 통해 정의할 수 있습니다. [맵 스타일](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)은 `road`로 설정됩니다.

두 번째 코드 블록은 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 함수를 통해 [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)를 정의하여 맵 보기에서 변경 내용에 애니메이션을 적용하는 애니메이션 맵 함수를 만듭니다. 함수는 각 클릭 시 임의의 확대/축소 수준을 생성하는 '맵 애니메이션' 단추에서 트리거됩니다.

## <a name="try-out-the-code"></a>코드 사용해 보기

위의 샘플 코드를 살펴보세요. 왼쪽에 있는 **JS 탭**에서 JavaScript 코드를 편집하고 오른쪽에 있는 **결과 탭**에서 맵 보기 변경 내용을 확인할 수 있습니다. 또한 **CodePen에서 편집** 단추를 클릭하고 CodePen에서 코드를 편집할 수 있습니다.

<a id="relatedReference"></a>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [지도 스타일 선택](choose-map-style.md)

> [!div class="nextstepaction"]
> [맵 컨트롤 추가](map-add-controls.md)
