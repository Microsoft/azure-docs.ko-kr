---
title: 맵의 셰이프에서 데이터 가져오기 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 지도에 그려진 셰이프 데이터를 가져오는 방법에 대해 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 63d947b85e75e3809445c5bc65577aeaed38caa1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209667"
---
# <a name="get-shape-data"></a>도형 데이터 가져오기

이 문서에서는 지도에 그려진 모양의 데이터를 가져오는 방법을 보여 줍니다. **DrawingManager ()** 함수는 [드로잉 관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--)내에서 사용 됩니다. 그려진 모양의 geojson 데이터를 추출 하 고 다른 곳에서 사용 하려는 경우에는 여러 가지 시나리오가 있습니다.  


## <a name="get-data-from-drawn-shape"></a>그려지는 도형에서 데이터 가져오기

다음 함수는 그려지는 셰이프의 소스 데이터를 가져와 화면에 출력 합니다. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

다음은 도형을 그려 기능을 테스트할 수 있는 전체 실행 코드 샘플입니다.

<br/>

<iframe height="686" title="도형 데이터 가져오기" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 펜으로 <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>셰이프 데이터 가져오기</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조 하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

그리기 도구 모듈의 추가 기능을 사용 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [그리기 이벤트에 반응](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [상호 작용 유형 및 바로 가기 키](drawing-tools-interactions-keyboard-shortcuts.md)

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [드로잉 관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [그리기 도구 모음](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
