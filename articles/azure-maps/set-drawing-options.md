---
title: 그리기 도구 모듈 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 그리기 옵션 데이터를 설정 하는 방법에 대해 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f3634149b744b9a03f0ed89aafbc20932701bdbc
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208185"
---
# <a name="use-the-drawing-tools-module"></a>그리기 도구 모듈 사용

Azure Maps 웹 SDK는 *그리기 도구 모듈*을 제공 합니다. 이 모듈을 사용 하면 마우스 또는 터치 화면과 같은 입력 장치를 사용 하 여 지도의 셰이프를 쉽게 그리거나 편집할 수 있습니다. 이 모듈의 핵심 클래스는 [드로잉 관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)입니다. 드로잉 관리자는 맵에 셰이프를 그리거나 편집 하는 데 필요한 모든 기능을 제공 합니다. 직접 사용할 수 있으며 사용자 지정 도구 모음 UI와 통합 됩니다. 기본 제공 [그리기 도구 모음](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 클래스를 사용할 수도 있습니다. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>웹 페이지에서 그리기 도구 모듈 로드

1. 새 HTML 파일을 만들고 [평소와 같이 맵을 구현](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)합니다.
2. Azure Maps 그리기 도구 모듈을 로드 합니다. 다음 두 가지 방법 중 하나로 로드할 수 있습니다.
    - Azure Maps services 모듈의 전역적으로 호스팅된 Azure Content Delivery Network 버전을 사용 합니다. 파일의 `<head>` 요소에 JavaScript 및 CSS 스타일 시트에 대 한 참조를 추가 합니다.

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.js"></script>
        ```

    - 또는 [npm 패키지를 사용](https://www.npmjs.com/package/azure-maps-drawing-tools) 하 여 AZURE MAPS 웹 SDK 소스 코드의 그리기 도구 모듈을 로컬로 로드 한 다음 앱을 사용 하 여 호스트할 수 있습니다. 이 패키지에는 TypeScript 정의도 포함됩니다. 다음 명령을 사용 합니다.
    
        > **npm 설치-맵-그리기-도구**
    
        그런 다음 파일의 `<head>` 요소에 JavaScript 및 CSS 스타일 시트에 대 한 참조를 추가 합니다.

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>드로잉 관리자 직접 사용

그리기 도구 모듈이 응용 프로그램에 로드 되 면 그리기 [관리자](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)를 사용 하 여 그리기 및 편집 기능을 사용 하도록 설정할 수 있습니다. 드로잉 관리자에 대 한 옵션을 인스턴스화하거나 또는 `drawingManager.setOptions()` 함수를 사용 하 여 지정할 수 있습니다.

### <a name="set-the-drawing-mode"></a>그리기 모드 설정

다음 코드는 그리기 관리자의 인스턴스를 만들고 그리기 **모드** 옵션을 설정 합니다. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

아래 코드는 그리기 관리자의 그리기 모드를 설정 하는 방법의 전체 실행 예입니다. 지도를 클릭 하 여 다각형 그리기를 시작 합니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="다각형 그리기" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 펜으로 <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>다각형 그리기</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조 하세요.
</iframe>


### <a name="set-the-interaction-type"></a>상호 작용 유형 설정

드로잉 관리자는 지도와 상호 작용 하 여 모양을 그리는 세 가지 다른 방법을 지원 합니다.

* 마우스 또는 터치를 클릭 하면 `click` 좌표가 추가 됩니다.
* 지도에서 마우스 또는 터치를 끌 때 `freehand ` 좌표가 추가 됩니다. 
* 마우스 또는 터치를 클릭 하거나 끌 때 `hybrid` 좌표가 추가 됩니다.

다음 코드는 다각형 그리기 모드를 사용 하도록 설정 하 고 그리기 관리자가 `freehand`를 준수 해야 하는 그리기 상호 작용의 유형을 설정 합니다. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 이 코드 샘플은 지도에 다각형을 그리는 기능을 구현 합니다. 마우스 왼쪽 단추를 누른 상태에서 자유롭게 끌어 놓습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="자유 그리기" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>자유 그리기</a> 를 참조 하세요.
</iframe>


### <a name="customizing-drawing-options"></a>그리기 옵션 사용자 지정

이전 예제에서는 드로잉 관리자를 인스턴스화하는 동안 그리기 옵션을 사용자 지정 하는 방법을 보여 주었습니다. `drawingManager.setOptions()` 함수를 사용 하 여 그리기 관리자 옵션을 설정할 수도 있습니다. 다음은 setOptions 함수를 사용 하 여 드로잉 관리자에 대 한 모든 옵션의 사용자 지정을 테스트 하는 도구입니다.

<br/>

<iframe height="685" title="드로잉 관리자 사용자 지정" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 펜으로 <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>셰이프 데이터 가져오기</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조 하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

그리기 도구 모듈의 추가 기능을 사용 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [드로잉 도구 모음 추가](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [셰이프 데이터 가져오기](map-get-shape-data.md)

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
