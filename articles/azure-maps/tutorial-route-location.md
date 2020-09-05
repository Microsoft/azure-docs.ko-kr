---
title: '자습서: 위치에 대한 경로 찾기 | Microsoft Azure Maps'
description: 관심 지점까지의 경로를 찾는 방법을 알아봅니다. 주소 좌표를 설정하는 방법을 참조하고 Azure Maps Route 서비스를 쿼리하여 지점으로 가는 방향을 확인합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 0ff604e920ca3e0708fc21a1cadfe61646f4e30b
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037578"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>자습서: Azure Maps를 사용하여 관심 지점으로 라우팅

이 자습서에서는 Azure Maps 계정과 Route Service SDK를 사용하여 관심 지점까지의 경로를 찾는 방법을 보여줍니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주소 좌표 설정
> * 관심 지점으로의 방향에 대한 Route Service 쿼리

## <a name="prerequisites"></a>필수 구성 요소

계속하기 전에 [계정 만들기](quick-demo-map-app.md#create-an-azure-maps-account)의 지침에 따라 S1 가격 책정 계층을 사용한 구독이 필요합니다. [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account)의 단계를 수행하여 계정에 대한 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>새 지도 만들기

다음 단계에서는 지도 컨트롤 API가 포함된 정적 HTML 페이지를 만드는 방법을 보여줍니다.

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapRoute.html**로 지정합니다.
2. 다음 HTML 구성 요소를 파일에 추가합니다.

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    HTML 헤더는 Azure 맵 컨트롤 라이브러리에서 호스팅하는 CSS 및 JavaScript 리소스 파일을 포함합니다. 페이지의 본문의 `onload` 이벤트는 페이지 본문이 로드될 때 `GetMap` 함수를 호출합니다. 이 함수에는 Azure Maps API에 액세스하는 인라인 JavaScript 코드가 포함됩니다. 

3. 다음 JavaScript 코드를 `GetMap` 함수에 추가합니다. 문자열 `<Your Azure Maps Key>`를 Maps 계정에서 복사한 기본 키로 바꿉니다.

    ```JavaScript
   //Instantiate a map object
   var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
           authType: 'subscriptionKey',
           subscriptionKey: '<Your Azure Maps Key>'
        }
   });
   ```

    `atlas.Map`은 시각적 및 대화형 웹 맵에 대한 컨트롤을 제공하고 Azure 맵 컨트롤 API의 구성 요소입니다.

4. 파일을 저장하고 브라우저에서 엽니다. 이 시점에서 더 자세히 개발할 수 있는 기본 지도가 있습니다.

   ![기본 지도 보기](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>경로가 렌더링되는 방식 정의

이 자습서에서는 경로의 시작 및 끝에 대한 기호 아이콘과 경로에 대한 선을 사용하여 간단한 경로를 렌더링합니다.

1. 맵을 초기화한 후 다음 JavaScript 코드를 추가합니다.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    맵 `ready` 이벤트 처리기에서 경로 선과 출발점 및 도착점을 저장하는 데이터 원본이 만들어집니다. 선 레이어를 만들어 데이터 원본에 연결하여 경로 선이 렌더링되는 방식을 정의합니다. 경로 선은 멋진 파란색 음영으로 렌더링됩니다. 5픽셀의 폭, 둥근 선 결합 및 캡으로 구성됩니다. 맵에 레이어를 추가하면 값이 `'labels'`인 두 번째 매개 변수가 전달됩니다. 이 매개 변수는 맵 레이블 아래의 이 레이어를 렌더링하도록 지정합니다. 이렇게 하면 경로 선이 도로 레이블을 가리지 않습니다. 기호 레이어가 생성되어 데이터 원본에 연결됩니다. 이 계층은 출발점과 도착점을 렌더링하는 방법을 지정합니다. 이 경우 각 지점 개체의 속성에서 아이콘 이미지 및 텍스트 레이블 정보를 검색하는 식이 추가되었습니다. 
    
2. 이 자습서에서는 Microsoft를 시작점으로 설정하고, 시애틀의 주유소를 대상 지점으로 설정합니다. 맵 `ready` 이벤트 처리기에서 다음 코드를 추가합니다.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    이 코드는 경로의 출발점과 도착점을 나타낼 두 개의 [GeoJSON 지점 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만들고 데이터 원본에 해당 지점을 추가합니다. `title` 및 `icon` 속성이 각 지점에 추가됩니다. 마지막 블록은 지도의 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용하여 출발점과 도착점의 위도 및 경도를 통해 카메라 보기를 설정합니다.

3. **MapRoute.html** 파일을 저장하고, 브라우저를 새로 고칩니다. 이제 지도의 중심에 시애틀이 표시되며, 출발점을 표시하는 파란색 핀과 도착점을 표시하는 둥근 파란색 핀을 볼 수 있습니다.

   ![맵에서 경로 시작점과 종료점 보기](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>방향 가져오기

이 섹션에서는 Azure Maps 경로 서비스 API를 사용하는 방법을 보여줍니다. 경로 서비스 API는 지정된 출발점에서 도착점으로의 경로를 찾습니다. 이 서비스는 두 위치 간의 *최소 시간*, *최단 거리*, *최적* 또는 *모험* 경로를 계획할 수 있는 API를 제공합니다. 또한 사용자는 이 서비스를 통해 Azure의 광범위한 교통 기록 데이터베이스를 사용해 미래의 경로를 계획할 수 있습니다. 사용자는 선택한 날짜 및 시간에 경로 기간의 예측을 볼 수 있습니다. 자세한 내용은 [경로 방향 가져오기](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 참조하세요. 다음 기능은 모두 맵 리소스가 액세스할 수 있게 준비된 후 로드되도록 **맵 준비 eventListener 내**에 추가해야 합니다.

1. GetMap 함수에서 다음을 JavaScript 코드에 추가합니다.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`은 구독 키를 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 `SubscriptionKeyCredentialPolicy`를 만듭니다. `atlas.service.MapsURL.newPipeline()`은 `SubscriptionKeyCredential` 정책을 인식하고 [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) 인스턴스를 만듭니다. `routeURL`은 Azure Maps [경로](https://docs.microsoft.com/rest/api/maps/route) 작업에 대한 URL을 나타냅니다.

2. 자격 증명 및 URL을 설정한 후 다음 JavaScript 코드를 추가하여 출발점에서 도착점까지의 경로를 생성합니다. `routeURL`은 Azure Maps 경로 서비스를 요청하여 경로 방향을 계산합니다. 그런 다음, `geojson.getFeatures()` 메서드를 사용하여 응답의 GeoJSON 기능 컬렉션을 추출하고 데이터 원본에 추가합니다.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. **MapRoute.html** 파일을 저장하고, 웹 브라우저를 새로 고칩니다. Maps API와 성공적으로 연결되면 다음과 비슷한 지도가 표시됩니다.

    ![Azure 맵 컨트롤 및 Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주소 좌표 설정
> * 관심 지점으로의 방향에 대한 경로 서비스 쿼리

> [!div class="nextstepaction"]
> [전체 소스 코드 보기](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [라이브 샘플 보기](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

다음 자습서에는 이동 모드 또는 화물 유형과 같은 제한을 적용하여 경로 쿼리를 만든 다음, 같은 지도에 여러 경로를 표시하는 방법을 보여줍니다.

> [!div class="nextstepaction"]
> [여러 이동 모드에 대한 경로 찾기](./tutorial-prioritized-routes.md)
