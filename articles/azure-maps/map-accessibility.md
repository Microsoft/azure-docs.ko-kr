---
title: Azure Maps로 액세스 가능한 애플리케이션 만들기 | Microsoft Docs
description: Azure Maps를 사용하여 액세스 가능한 애플리케이션을 작성하는 방법
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769705"
---
# <a name="building-an-accessible-application"></a>액세스 가능한 애플리케이션 작성

이 문서에서는 화면 판독기에서 사용할 수 있는 지도 애플리케이션을 작성하는 방법을 보여줍니다.

## <a name="understand-the-code"></a>코드 이해

<iframe height='500' scrolling='no' title='액세스 가능한 애플리케이션 만들기' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>액세스 가능한 애플리케이션 만들기</a>를 참조하세요.
</iframe>

맵에는 손쉬운 사용 기능이 미리 구축되어 있습니다. 사용자는 키보드를 사용하여 맵을 탐색할 수 있습니다. 화면 판독기가 실행 중이면 맵 상태 변경 내용 알림이 사용자에게 표시됩니다.
예를 들어 맵을 이동하거나 확대/축소하면 맵 변경 내용 알림이 사용자에게 표시됩니다. 기본 지도에 추가되는 모든 정보에는 화면 판독기 사용자를 위한 텍스트 정보가 있어야 합니다.

[팝업](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) 사용이 이런 기능을 구현하는 한가지 방법입니다. 위의 검색 예에서는 지도에 배치된 모든 핀에 대한 텍스트 정보 팝업이 지도에 추가됩니다. [팝업](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)의 [attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) 메서드를 사용하면 맵에 팝업을 시각적으로 표시하지 않고도 화면 판독기에서 팝업을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 팝업 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [팝업](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

추가 코드 샘플을 확인해 보세요.

> [!div class="nextstepaction"]
> [코드 샘플 페이지](https://aka.ms/AzureMapsSamples)
