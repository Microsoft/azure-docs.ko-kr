---
title: Azure Maps의 지도 스타일 기능 | Microsoft Docs
description: Azure Maps 스타일 관련 기능에 대해 알아봅니다.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795890"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Azure Maps에서 지도 스타일 선택

Azure Maps에서는 네 가지 다른 지도 스타일 중에서 선택할 수 있습니다. 지도 스타일에 대한 자세한 내용은 [Azure Maps의 지원되는 지도 스타일](./supported-map-styles.md)을 참조하세요. 이 문서에서는 스타일 관련 기능을 사용하여 지도 로드 시 스타일을 설정하고, 새 스타일을 설정하고, 스타일 선택 컨트롤을 사용하는 방법을 보여 줍니다.

## <a name="set-style-on-map-load"></a>지도 로드 시 스타일 설정

<iframe height='500' scrolling='no' title='지도 로드 시 스타일 설정' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>지도 로드 시 스타일 설정</a>을 참조하세요.
</iframe>

위의 코드 블록은 스타일을 grayscale_dark로 설정하여 구독 키를 설정하고 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

## <a name="update-the-style"></a>스타일 업데이트

<iframe height='500' scrolling='no' title='스타일 업데이트' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>스타일 업데이트</a>를 참조하세요.
</iframe>

위의 코드 블록은 스타일을 미리 설정하지 않고 구독 키를 설정하고 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 지도의 [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도 스타일을 위성으로 설정합니다.

## <a name="add-the-style-picker"></a>스타일 선택 추가

<iframe height='500' scrolling='no' title='스타일 선택 추가' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>스타일 선택 추가</a>를 참조하세요.
</iframe>

위 코드의 첫 번째 코드 블록은 스타일을 grayscale_dark로 미리 설정하여 구독 키를 설정하고 지도 개체를 만듭니다. 지도 만드는 방법에 대한 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 생성자를 사용하여 스타일 선택을 생성합니다.

스타일 선택을 사용하여 지도의 스타일을 선택할 수 있습니다. 세 번째 코드 블록은 지도의 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 메서드를 사용하여 지도에 스타일 선택을 추가합니다. 스타일 선택기는 지도가 완전히 로드된 후에 로드되도록 지도 **이벤트 수신기** 내에 포함됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아려보면 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

지도에 컨트롤 추가:

> [!div class="nextstepaction"]
> [맵 컨트롤 추가](./map-add-controls.md)

지도 핀을 추가:

> [!div class="nextstepaction"]
> [핀 추가](./map-add-pin.md)
