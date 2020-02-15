---
title: 지도에 경로 방향 표시 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 맵의 두 위치 사이에 방향을 표시 하는 방법을 알아봅니다.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 63f7eac52a555be1877f235bd2f7f1d9315d53d5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208508"
---
# <a name="show-directions-from-a-to-b"></a>A에서 B로의 방향 표시

이 문서에서는 경로 요청을 만들고 맵에 경로를 표시하는 방법을 설명합니다.

그렇게 하려면 두 가지 방법이 있습니다. 첫 번째 방법은 서비스 모듈을 통해 [Azure Maps 경로 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 쿼리하는 것입니다. 두 번째 방법은 [FETCH api](https://fetch.spec.whatwg.org/) 를 사용 하 여 [Azure Maps 경로 api](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)에 대 한 검색 요청을 수행 하는 것입니다. 두 방법 모두 아래에 설명되어 있습니다.

## <a name="query-the-route-via-service-module"></a>서비스 모듈을 통해 경로 쿼리

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/RBZbep/'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io'>맵에서 A에서 B로의 방향 표시(서비스 모듈)</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 블록은 맵 개체를 생성 하 고 액세스 토큰을 사용 하도록 인증 메커니즘을 설정 합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 액세스 토큰을 사용 하 여 Azure Maps HTTP 요청을 인증 하는 `TokenCredential`을 만듭니다. 그런 다음 `atlas.service.MapsURL.newPipeline()`에 `TokenCredential`를 전달 하 고 [파이프라인](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) 인스턴스를 만듭니다. `routeURL`은 Azure Maps [경로](https://docs.microsoft.com/rest/api/maps/route) 작업에 대한 URL을 나타냅니다.

세 번째 코드 블록은 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 개체를 만들어 맵에 추가 합니다.

네 번째 코드 블록은 시작 및 끝 [요소](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 개체를 만들어 dataSource 개체에 추가 합니다.

줄은 LineString의 [기능](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 입니다. [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 선 개체를 지도에 선으로 렌더링합니다. 네 번째 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) 은 텍스트 또는 아이콘을 사용 하 여 데이터 [원본](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 지점 기반 데이터를 렌더링 합니다. 텍스트 또는 아이콘은 지도에서 기호로 렌더링 됩니다. 다섯 번째 코드 블록은 지도에 기호 계층을 만들고 추가 합니다.

여섯 번째 코드 블록은 [서비스 모듈](how-to-use-services-module.md)의 일부인 Azure Maps 라우팅 서비스를 쿼리 합니다. RouteURL의 [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) 메서드는 시작점과 끝점 간의 경로를 가져오는 데 사용 됩니다. 그런 다음 `geojson.getFeatures()` 메서드를 사용 하 여 응답에서 GeoJSON feature 컬렉션을 추출 하 고 데이터 원본에 추가 합니다. 그런 다음, 응답을 지도에 경로로 렌더링합니다. 지도에 선을 추가하는 방법에 대한 자세한 내용은 [지도에 선 추가](map-add-line-layer.md)를 참조하세요.

마지막 코드 블록은 지도의 [Setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용 하 여 지도의 범위를 설정 합니다.

경로 쿼리, 데이터 원본, 기호, 선 계층 및 카메라 경계는 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)내에 생성 됩니다. 이 코드 구조는 맵이 완전히 로드 된 후에만 결과가 표시 되도록 합니다.

## <a name="query-the-route-via-fetch-api"></a>Fetch API를 통해 경로 쿼리

<iframe height='500' scrolling='no' title='맵에 A에서 B로의 방향 표시' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/zRyNmP/'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의해 펜 <a href='https://codepen.io'>A에서 B로의 방향 표시</a>를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 생성 하 고 액세스 토큰을 사용 하도록 인증 메커니즘을 설정 합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 개체를 만든 후 지도에 추가합니다.

세 번째 코드 블록은 경로에 대 한 시작 및 대상 위치를 만듭니다. 그런 다음 데이터 소스에 추가 합니다. [addPins](map-add-pin.md) 사용 방법에 대한 지침은 [지도에 핀 추가](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)를 참조하세요.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)는 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 선 개체를 지도에 선으로 렌더링합니다. 네 번째 코드 블록은 선 계층을 만들고 지도에 추가합니다. [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)에서 선 계층의 속성을 참조하세요.

[기호 계층](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)은 텍스트 또는 아이콘을 사용하여 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)에 래핑된 점 기반 데이터를 지도에 기호로 렌더링합니다. 다섯 번째 코드 블록은 지도에 기호 계층을 만들고 추가 합니다. [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)에서 기호 계층의 속성을 참조하세요.

다음 코드 블록은 시작 및 대상 지점에서 `SouthWest` 및 `NorthEast` 지점을 만들고 Map의 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용하여 지도의 경계를 설정합니다.

마지막 코드 블록은 [FETCH api](https://fetch.spec.whatwg.org/) 를 사용 하 여 [Azure Maps 경로 api](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)에 대 한 검색 요청을 수행 합니다. 그런 다음 응답을 구문 분석 합니다. 응답이 성공한 경우 위도 및 경도 정보를 사용 하 여 해당 점에 연결 하 여 배열을 만듭니다. 그런 다음 데이터 원본에 선 데이터를 추가 하 여 맵에 경로를 렌더링 합니다. 지침은 [맵에 선 추가](map-add-line-layer.md)를 참조하세요.

경로 쿼리, 데이터 원본, 기호, 선 계층 및 카메라 경계는 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)내에 생성 됩니다. 지도를 완전히 로드 한 후에도 결과가 표시 되도록 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

전체 코드 예제는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [맵에 트래픽 표시](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [맵 - 마우스 이벤트 상호 작용](./map-events.md)
