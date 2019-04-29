---
title: Azure Maps에서 지도 컨트롤 추가 | Microsoft Docs
description: Azure Maps에서 지도에 확대/축소 컨트롤, 피치 컨트롤, 회전 컨트롤 및 스타일 선택기를 추가하는 방법입니다.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c1f5dd329f34d64478d605c21d229d8c75a99300
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108722"
---
# <a name="add-map-controls-to-azure-maps"></a>Azure Maps에 지도 컨트롤 추가

이 문서에서는 지도에 지도 컨트롤을 추가하는 방법을 보여 줍니다. 또한 모든 컨트롤 및 [스타일 선택기](https://docs.microsoft.com/azure/azure-maps/choose-map-style)를 사용하여 지도를 만드는 방법도 알아봅니다.

## <a name="add-zoom-control"></a>확대/축소 컨트롤 추가

<iframe height='500' scrolling='no' title='확대/축소 컨트롤 추가' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>확대/축소 컨트롤 추가</a>를 참조하세요.
</iframe>

첫 번째 코드 블록은 스타일을 미리 설정하지 않고 구독 키를 설정하고 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

확대/축소 컨트롤은 지도를 확대/축소하는 기능을 추가합니다. 두 번째 코드 블록은 atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol)을 사용하여 확대/축소 컨트롤 개체를 만들고 지도의 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도에 해당 개체를 추가합니다. 확대/축소 컨트롤은 지도가 완전히 로드된 후에 로드되도록 지도 **이벤트 수신기** 내에 포함됩니다.

## <a name="add-pitch-control"></a>피치 컨트롤 추가

<iframe height='500' scrolling='no' title='피치 컨트롤 추가' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>피치 컨트롤 추가</a>를 참조하세요.
</iframe>

첫 번째 코드 블록은 스타일을 미리 설정하지 않고 구독 키를 설정하고 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

피치 컨트롤은 지도의 피치를 변경하는 기능을 추가합니다. 두 번째 코드 블록은 atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol)을 사용하여 피치 컨트롤 개체를 만들고 지도의 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도에 해당 개체를 추가합니다. 피치 컨트롤은 지도가 완전히 로드된 후에 로드되도록 지도 **이벤트 수신기** 내에 포함됩니다.

## <a name="add-compass-control"></a>나침반 컨트롤 추가

<iframe height='500' scrolling='no' title='회전 컨트롤 추가' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>회전 컨트롤 추가</a>를 참조하세요.
</iframe>

첫 번째 코드 블록은 스타일을 미리 설정하지 않고 구독 키를 설정하고 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 atlas [나침반 컨트롤](/javascript/api/azure-maps-control/atlas.control.compasscontrol)을 사용하여 나침반 컨트롤 개체를 만듭니다. 또한 지도의 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도에 나침반 컨트롤을 추가합니다. 나침반 컨트롤은 지도가 완전히 로드된 후에 로드되도록 지도 **이벤트 수신기** 내에 포함됩니다.

## <a name="a-map-with-all-controls"></a>모든 컨트롤이 있는 지도

<iframe height='500' scrolling='no' title='모든 컨트롤이 있는 지도' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>모든 컨트롤이 있는 지도</a>를 참조하세요.
</iframe>

첫 번째 코드 블록은 스타일을 미리 설정하지 않고 구독 키를 설정하고 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol)을 사용하여 나침반 컨트롤 개체를 만들고 지도의 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도에 해당 개체를 추가합니다.

세 번째 코드 블록은 atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol)을 사용하여 확대/축소 컨트롤 개체를 만들고 지도의 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도에 해당 개체를 추가합니다.

네 번째 코드 블록은 atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol)을 사용하여 피치 컨트롤 개체를 만들고 지도의 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도에 해당 개체를 추가합니다.

네 번째 코드 블록은 atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)을 사용하여 스타일 선택기 개체를 만들고 지도의 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도에 해당 개체를 추가합니다. 모든 컨트롤 개체는 지도가 완전히 로드된 후에 로드되도록 지도 **이벤트 수신기** 내에 추가됩니다.

스크립트의 컨트롤 개체 순서에 따라 지도에서 해당 개체가 표시되는 순서가 결정됩니다. 지도의 컨트롤 순서를 변경하는 경우 스크립트에서 컨트롤의 순서를 변경하면 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

전체 코드는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [핀 추가](./map-add-pin.md)

> [!div class="nextstepaction"]
> [팝업 추가](./map-add-popup.md)
