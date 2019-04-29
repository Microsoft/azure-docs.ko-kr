---
title: Azure Maps로 방향 표시 | Microsoft Docs
description: Javascript 맵에서 두 위치 간의 방향을 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: b8205383c25ba04212126e0e6ca1bd44e4efad1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768906"
---
# <a name="show-directions-from-a-to-b"></a>A에서 B로의 방향 표시

이 문서에서는 경로 요청을 만들고 맵에 경로를 표시하는 방법을 설명합니다.

그렇게 하려면 두 가지 방법이 있습니다. 첫 번째 방법은 서비스 모듈을 통해 [Azure Maps 경로 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 쿼리하는 것입니다. 두 번째 방법은 사용 하는 [API 인출](https://fetch.spec.whatwg.org/) 검색 요청을 수행 하는 [Azure Maps 경로 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)합니다. 두 방법 모두 아래에 설명되어 있습니다.

## <a name="query-the-route-via-service-module"></a>서비스 모듈을 통해 경로 쿼리

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/RBZbep/'>맵에서 A에서 B로의 방향 표시(서비스 모듈)</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 map 개체를 생성 하 고 구독 키를 사용 하는 인증 메커니즘을 설정 합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록을 만듭니다는 `SubscriptionKeyCredentialPolicy` 구독 키를 사용 하 여 Azure Maps로 HTTP 요청을 인증 합니다. `atlas.service.MapsURL.newPipeline()`은 `SubscriptionKeyCredential` 정책을 인식하고 [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) 인스턴스를 만듭니다. `routeURL`은 Azure Maps [경로](https://docs.microsoft.com/rest/api/maps/route) 작업에 대한 URL을 나타냅니다.

세 번째 코드 블록을 만들고 추가 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 지도 개체입니다.

네 번째 코드 블록을 만들고 시작 및 끝 [지점](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 개체 및 데이터 원본 개체에 추가 합니다.

선은 LineString의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 중 하나입니다. [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 선 개체를 지도에 선으로 렌더링합니다. 네 번째 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다. 다섯 번째 코드 블록을 만들고 지도에 기호가 레이어를 추가 합니다.

코드 블록은 여섯 번째 구성 요소인 Azure Maps 라우팅 서비스에서 쿼리의 합니다 [서비스 모듈](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js)합니다. 합니다 [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) 메서드는 RouteURL의 시작점과 끝점 간의 경로 가져오는 데 사용 됩니다. 응답에서 GeoJSON 기능 컬렉션을 사용 하 여 추출 되는 `geojson.getFeatures()` 메서드 데이터 소스에 추가 됩니다. 그런 다음, 응답을 지도에 경로로 렌더링합니다. 지도에 선을 추가하는 방법에 대한 자세한 내용은 [지도에 선 추가](./map-add-shape.md#addALine)를 참조하세요.

지도의 사용 하 여 지도 요소의 범위를 설정 하는 코드의 마지막 블록 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성입니다.

맵이 완전히 로드된 후 결과가 표시되도록 경로 쿼리, 데이터 원본, 기호, 선 계층 및 카메라 범위가 맵의 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 내에 생성되고 설정됩니다.

## <a name="query-the-route-via-fetch-api"></a>쿼리의 페치 API를 통해 경로

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>A에서 B로의 방향 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 map 개체를 생성 하 고 구독 키를 사용 하는 인증 메커니즘을 설정 합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 개체를 만든 후 지도에 추가합니다.

세 번째 코드 블록은 경로의 시작 및 끝 지점을 만들고 데이터 원본에 추가합니다. [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 사용 방법에 대한 지침은 [지도에 핀 추가](map-add-pin.md)를 참조하세요.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 선 개체를 지도에 선으로 렌더링합니다. 네 번째 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다. 다섯 번째 코드 블록을 만들고 지도에 기호가 레이어를 추가 합니다. [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)에서 기호 계층의 속성을 참조하세요.

다음 코드 블록은 시작 및 대상 지점에서 `SouthWest` 및 `NorthEast` 지점을 만들고 Map의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용하여 지도의 경계를 설정합니다.

마지막 코드 블록을 사용 하는 [API 인출](https://fetch.spec.whatwg.org/) 검색 요청을 수행 하는 [Azure Maps 경로 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)합니다. 응답 한 다음 구문 분석 됩니다. 응답에 성공한 경우 위도 및 경도 정보를 이러한 점을 연결 하 여 배열을 줄을 만드는 데 사용 됩니다. 줄 데이터를 지도에 경로 렌더링 하기 위해 데이터 원본에 추가 됩니다. 지침은 [맵에 선 추가](./map-add-shape.md#addALine)를 참조하세요.

맵이 완전히 로드된 후 결과가 표시되도록 경로 쿼리, 데이터 원본, 기호, 선 계층 및 카메라 범위가 맵의 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) 내에 생성되고 설정됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [맵에 트래픽 표시](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [맵 - 마우스 이벤트 상호 작용](./map-events.md)
