---
title: Azure Maps를 사용 하 여 맵 만들기 Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 웹 페이지에서 지도를 렌더링 하는 방법을 알아봅니다.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988586"
---
# <a name="create-a-map"></a>맵 만들기

이 문서에서는 맵을 만들고 맵을 애니메이션하는 방법을 보여줍니다.  

## <a name="loading-a-map"></a>지도 로드

지도를 로드 하려면 [map 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)의 새 인스턴스를 만듭니다. 맵을 초기화할 때 DIV 요소 ID를 전달 하 여 맵을 렌더링 하 고 지도를 로드할 때 사용할 옵션 집합을 전달 합니다. `atlas` 네임 스페이스에 기본 인증 정보가 지정 되지 않은 경우 지도를 로드할 때 맵 옵션에서이 정보를 지정 해야 합니다. 맵은 성능을 위해 여러 리소스를 비동기적으로 로드 합니다. 이와 같이 map 인스턴스를 만든 후 맵에 `ready` 또는 `load` 이벤트를 연결한 다음 맵과 상호 작용 하는 추가 코드를 이벤트 처리기에 추가 합니다. `ready` 이벤트는 map에서 프로그래밍 방식으로 상호 작용할 수 있는 충분 한 리소스를 로드 하는 즉시 발생 합니다. `load` 이벤트는 초기 맵 뷰가 완전히 로드 된 후에 발생 합니다. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="기본 지도 부하" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 펜 <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>기본 맵 로드</a> 를 참조 하세요.
</iframe>

> [!TIP]
> 여러 맵을 동일한 페이지에 로드할 수 있습니다. 동일한 페이지의 여러 지도에서 동일 하거나 다른 인증 및 언어 설정을 사용할 수 있습니다.

## <a name="show-a-single-copy-of-the-world"></a>전 세계의 단일 복사본 표시

넓은 화면에서 지도를 축소 하면 전 세계의 여러 복사본이 가로로 표시 됩니다. 이 옵션은 일부 시나리오에는 유용 하지만 다른 응용 프로그램의 경우에는 전 세계의 단일 복사본을 확인 하는 것이 좋습니다. 이 동작은 map `renderWorldCopies` 옵션을 `false`설정 하 여 구현 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> 를 참조 하세요.
</iframe>

## <a name="controlling-the-map-camera"></a>지도 카메라 제어

지도의 카메라를 사용 하 여 지도의 표시 된 영역을 설정 하는 방법에는 두 가지가 있습니다. 지도를 로드할 때 카메라 옵션을 설정할 수 있습니다. 또는 map이 로드 된 후 언제 든 지 `setCamera` 옵션을 호출 하 여 맵 뷰를 프로그래밍 방식으로 업데이트할 수 있습니다.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>카메라 설정

다음 코드에서는 [Map 개체가](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 만들어지고 중심 및 확대/축소 옵션이 설정 됩니다. 가운데 및 확대/축소 수준과 같은 지도 속성은 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)의 일부입니다.

<br/>

<iframe height='500' scrolling='no' title='CameraOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>통해 `CameraOptions` via 맵 만들기 </a>를 참조 하세요.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>카메라 경계 설정

다음 코드에서 [Map 개체](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 는 `new atlas.Map()`를 통해 생성 됩니다. `CameraBoundsOptions`와 같은 맵 속성은 맵 클래스의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 함수를 통해 정의될 수 있습니다. 경계 및 안쪽 여백 속성은 `setCamera`를 사용하여 설정됩니다.

<br/>

<iframe height='500' scrolling='no' title='CameraBoundsOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>통해 `CameraBoundsOptions` via 맵 만들기 </a>를 참조 하세요.
</iframe>

### <a name="animate-map-view"></a>맵 보기 애니메이션

다음 코드에서 첫 번째 코드 블록은 지도를 만들고 입력 및 확대/축소 맵 스타일을 설정 합니다. 두 번째 코드 블록에서 애니메이션 효과에 대한 click 이벤트 처리기가 만들어집니다. 이 단추를 클릭 하면 [CameraOptions 및](/javascript/api/azure-maps-control/atlas.cameraoptions) [옵션](/javascript/api/azure-maps-control/atlas.animationoptions)에 대한 임의 값을 사용 하 여 `setCamera` 함수가 호출 됩니다.

<br/>

<iframe height='500' scrolling='no' title='맵 보기 애니메이션' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>의 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 제공되는 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>맵 보기 애니메이션</a>을 참조하세요.
</iframe>

## <a name="try-out-the-code"></a>코드 사용해 보기

코드 샘플을 확인 합니다. **JS 탭** 내에서 JavaScript 코드를 편집 하 고 **결과 탭**에서 맵 보기 변경 내용을 볼 수 있습니다. 오른쪽 위 모서리에서 **CodePen에서 편집**을 클릭 하 고 CodePen에서 코드를 수정할 수도 있습니다.

<a id="relatedReference"></a>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [애니메이션 옵션](/javascript/api/azure-maps-control/atlas.animationoptions)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [지도의 스타일 변경](choose-map-style.md)

> [!div class="nextstepaction"]
> [지도에 컨트롤 추가](map-add-controls.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)
