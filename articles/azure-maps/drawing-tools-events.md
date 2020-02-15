---
title: 지도에 드로잉 도구 모음 추가 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 지도에 드로잉 도구 모음을 추가 하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198312"
---
# <a name="drawing-tool-events"></a>그리기 도구 이벤트

지도에서 그리기 도구를 사용 하는 경우 사용자가 맵에 그릴 때 특정 이벤트에 반응 하는 것이 유용 합니다. 다음 표에서는 `DrawingManager` 클래스에서 지 원하는 모든 이벤트를 나열 합니다.

| 행사 | Description |
|-------|-------------|
| `drawingchanged` | 도형의 모든 좌표가 추가 되거나 변경 된 경우 발생 합니다. | 
| `drawingchanging` | 셰이프의 미리 보기 좌표가 표시 될 때 발생 합니다. 예를 들어이 이벤트는 좌표를 끌 때 여러 번 발생 합니다. | 
| `drawingcomplete` | 셰이프를 그리거나 편집 모드에서 벗어난 경우에 발생 합니다. |
| `drawingmodechanged` | 그리기 모드가 변경 될 때 발생 합니다. 새 그리기 모드가 이벤트 처리기에 전달 됩니다. |
| `drawingstarted` | 사용자가 도형 그리기를 시작 하거나 셰이프를 편집 모드로 전환 하는 경우 발생 합니다.  |

다음 코드에서는 그리기 도구 모듈의 이벤트가 작동 하는 방법을 보여 줍니다. 지도에 셰이프를 그리고 이벤트가 발생 하는 것을 감시 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그리기 도구 이벤트" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>의 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>그리기 도구 이벤트</a> 를 참조 하세요.
</iframe>

<br/>

## <a name="examples"></a>예

그리기 도구 이벤트를 사용 하는 몇 가지 일반적인 시나리오를 살펴보겠습니다.

### <a name="select-points-in-polygon-area"></a>다각형 영역에서 요소 선택

이 코드는 사용자 그리기 셰이프의 이벤트를 모니터링 하는 방법을 보여 줍니다. 이 예제에서 코드는 다각형, 사각형 및 원의 모양을 모니터링 합니다. 그런 다음 지도에서 그려지는 영역 내에 있는 데이터 요소를 결정 합니다. `drawingcomplete` 이벤트는 선택 논리를 트리거하는 데 사용 됩니다. Select 논리에서 코드는 맵의 모든 데이터 요소를 반복 합니다. Point와 그려지는 모양의 영역이 교차 하는지 확인 합니다. 이 예제에서는 오픈 소스 [Turf](https://turfjs.org/) 라이브러리를 사용 하 여 공간 교차점 계산을 수행 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="그린 다각형 영역에서 데이터 선택" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/XWJdeja'>그리기 다각형에서 데이터 선택</a> 을 참조 하세요.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>다각형 영역에서 그리기 및 검색

이 코드는 사용자가 셰이프 그리기를 마친 후 셰이프의 영역 내에서 취미의 요소를 검색 합니다. 프레임의 오른쪽 위 모퉁이에 있는 ' 코드에서 편집 ' 펜을 클릭 하 여 코드를 수정 하 고 실행할 수 있습니다. `drawingcomplete` 이벤트는 검색 논리를 트리거하는 데 사용 됩니다. 사용자가 사각형 또는 다각형을 그리면 기 하 도형 내에서 검색이 수행 됩니다. 원을 그리면 반지름과 중심 위치가 사용 되어 관심 영역 검색을 수행 합니다. `drawingmodechanged` 이벤트는 사용자가 그리기 모드로 전환 하는 시기를 결정 하는 데 사용 되며,이 이벤트는 그리기 캔버스를 지웁니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 영역에서 그리기 및 검색" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 통해 펜 <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>그리기 및 다각형에서 검색 영역</a> 을 참조 하세요.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>측정 도구 만들기

아래 코드에서는 그리기 이벤트를 사용 하 여 측정 도구를 만드는 방법을 보여 줍니다. 셰이프를 그릴 때이를 모니터링 하는 데 `drawingchanging`를 사용 합니다. 사용자가 마우스를 움직이면 도형의 크기가 계산 됩니다. `drawingcomplete` 이벤트는 도형이 그려진 후에 최종 계산을 수행 하는 데 사용 됩니다. `drawingmodechanged` 이벤트는 사용자가 그리기 모드로 전환 하는 시기를 결정 하는 데 사용 됩니다. 또한 `drawingmodechanged` 이벤트는 그리기 캔버스를 지우고 이전 측정 정보를 지웁니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="측정 도구" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>측정 도구</a> 를 참조 하세요.
</iframe>

<br/>

## <a name="next-steps"></a>다음 단계

그리기 도구 모듈의 추가 기능을 사용 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [셰이프 데이터 가져오기](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)

서비스 모듈에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [서비스 모듈](how-to-use-services-module.md)

추가 코드 샘플을 확인해 보세요.

> [!div class="nextstepaction"]
> [코드 샘플 페이지](https://aka.ms/AzureMapsSamples)
