---
title: Azure Maps로 마우스 이벤트 처리 | Microsoft Docs
description: 맵 이벤트를 사용하여 대화형 Javascript 맵을 만드는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fce8eae25942d098bb3f3277938bfaa3dafa00b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770451"
---
# <a name="interact-with-the-map---mouse-events"></a>맵과 상호 작용 - 마우스 이벤트

이 문서에서는 [map 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 속성을 사용하여 맵 및 맵의 여러 다른 계층에서 이벤트를 강조 표시하는 방법을 보여 줍니다. 또한 map 클래스 events 속성을 사용하여 HTML 표식과 상호 작용할 때 발생하는 이벤트를 강조 표시하는 방법을 보여 줍니다.

## <a name="interact-with-the-map"></a>맵과 상호 작용

<iframe height='600' scrolling='no' title='맵 – 마우스 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>맵과 상호 작용 - 마우스 이벤트</a>를 참조하세요.
</iframe>

위의 맵을 재생하고 오른쪽에 강조 표시된 해당 마우스 이벤트를 확인합니다. **JS 탭**을 클릭하면 JavaScript 코드를 보고 편집할 수 있습니다. **CodePen에서 편집** 단추를 클릭하고 CodePen에서 코드를 편집할 수도 있습니다.

## <a name="interact-with-map-layers"></a>맵 계층과 상호 작용

<iframe height='600' scrolling='no' title='맵 - 계층 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>맵 - 계층 이벤트와 상호 작용</a>을 참조하세요.
</iframe>

위의 코드에서는 기호 계층과 상호 작용할 때 발생한 이벤트의 이름을 강조 표시합니다. 기호, 거품형, 선 및 다각형 계층은 모두 동일한 이벤트 세트를 지원합니다. 타일 계층은 이러한 이벤트를 지원하지 않습니다.

## <a name="interact-with-html-marker"></a>HTML 표식과 상호 작용

<iframe height='500' scrolling='no' title='맵 - HTML 표식 이벤트와 상호 작용' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>맵 - HTML 표식 이벤트와 상호 작용</a>을 참조하세요.
</iframe>

위의 코드는 HTML 표식에 Javascript 맵 이벤트를 추가합니다. 또한 HTML 표식과 상호 작용할 때 발생한 이벤트의 이름을 강조 표시합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Maps 서비스 모듈을 사용 하 여](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [코드 샘플 페이지](https://aka.ms/AzureMapsSamples)
