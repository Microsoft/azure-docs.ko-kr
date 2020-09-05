---
title: '자습서: 여행 모드별 여러 경로 찾기 | Microsoft Azure Maps'
description: Azure Maps를 사용하여 관심 지점까지의 특정 이동 모드 경로를 찾는 방법을 알아봅니다. 지도에 여러 경로를 표시하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 7d17b9474edef245f7db16f33c72d722f356f712
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037595"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>자습서: Azure Maps를 사용하여 여러 여행 모드에 대한 경로 찾기

이 자습서에서는 Azure Maps 계정과 경로 서비스를 사용하는 방법을 보여줍니다. 경로 서비스는 사용자의 여행 모드에 따라 우선 순위를 지정하여 관심 지점에 대한 경로를 찾을 수 있습니다. 맵에 서로 다른 두 개의 경로를 표시할 수 있습니다. 하나는 자동차이고 하나는 트럭입니다. 라우팅 서비스는 차량의 높이와 무게 때문에 또는 차량에 위험한 화물을 운반하는 경우 제한 사항을 고려합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 지도에서 교통 흐름 시각화
> * 여행 모드를 선언하는 경로 쿼리 만들기
> * 지도에 여러 경로 표시

## <a name="prerequisites"></a>사전 요구 사항
계속하기 전에 [계정 만들기](quick-demo-map-app.md#create-an-azure-maps-account)의 지침을 수행하고, S1 가격 책정 계층을 선택합니다. [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account)의 단계를 수행하여 계정에 대한 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

## <a name="create-a-new-map"></a>새 지도 만들기

다음 단계에서는 지도 컨트롤 API가 포함된 정적 HTML 페이지를 만드는 방법을 보여줍니다.

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapTruckRoute.html**로 지정합니다.
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

    `atlas.Map` 클래스는 시각적인 대화형 웹 맵에 대한 컨트롤을 제공하며 Azure 맵 컨트롤 API의 구성 요소입니다.

4. 파일을 저장하고 브라우저에서 엽니다. 이 시점에서 더 자세히 개발할 수 있는 기본 지도가 있습니다.

   ![기본 지도 보기](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>교통 흐름 시각화

1. 지도에 교통 흐름 표시를 추가합니다. 맵 `ready` 이벤트는 맵 리소스가 로드되어 안전하게 상호 작용할 준비가 될 때까지 기다립니다.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    맵 `ready` 이벤트 처리기에서 맵에 대한 트래픽 흐름 설정은 자유 흐름에 대한 상대 속도인 `relative`로 설정됩니다. 또한 도로의 `absolute` 속도 또는 자유 흐름과 다른 상대 속도를 표시하는 `relative-delay`로 설정할 수도 있습니다.

2. **MapTruckRoute.html** 파일을 저장하고, 브라우저에서 페이지를 새로 고칩니다. 지도와 상호 작용하고 로스엔젤레스를 확대하는 경우 현재 교통 데이터가 있는 거리가 보입니다.

   ![맵에서 트래픽 보기](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>경로가 렌더링되는 방식 정의

이 자습서에서는 두 개의 경로를 계산하여 지도에 렌더링합니다. 한 경로는 승용차가 접근할 수 있는 도로를 사용하고 다른 경로는 트럭이 접근할 수 있는 도로를 사용합니다. 경로가 렌더링되면 경로의 출발과 도착에 대한 기호 아이콘이 표시되고 각 경로는 서로 다른 색상의 선으로 표시됩니다.

1. 맵을 초기화한 후 맵 `ready` 이벤트 처리기에 다음 JavaScript 코드를 추가합니다.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
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
    
    맵 `ready` 이벤트 처리기에서 경로 선과 출발점 및 도착점을 저장하는 데이터 원본이 만들어집니다. 선 레이어를 만들어 데이터 원본에 연결하여 경로 선이 렌더링되는 방식을 정의합니다. 식은 경로 선 기능의 속성에서 선 너비 및 색상을 검색하는 데 사용됩니다. 맵에 레이어를 추가하면 값이 `'labels'`인 두 번째 매개 변수가 전달됩니다. 이 매개 변수는 맵 레이블 아래의 이 레이어를 렌더링하도록 지정합니다. 이렇게 하면 경로 선이 도로 레이블을 포함하지 않습니다. 기호 레이어가 생성되어 데이터 원본에 연결됩니다. 이 계층은 출발점과 도착점을 렌더링하는 방법을 지정합니다. 이 경우 각 지점 개체의 속성에서 아이콘 이미지 및 텍스트 레이블 정보를 검색하는 식이 추가되었습니다. 
    
2. 이 자습서에서는 출발 지점을 시애틀 소재의 Fabrikam이라는 가상 회사로, 도착 지점을 Microsoft 본사로 설정합니다. 맵 `ready` 이벤트 처리기에서 다음 코드를 추가합니다.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    이 코드는 경로의 출발점과 도착점을 나타낼 두 개의 [GeoJSON 개체](https://en.wikipedia.org/wiki/GeoJSON)를 만듭니다. `title` 및 `icon` 속성이 각 지점에 추가됩니다.

3. 다음으로, JavaScript 코드를 추가하여 맵에 출발점과 종료점에 대한 핀을 추가합니다.

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    시작 지점과 끝 지점이 데이터 원본에 추가됩니다. `atlas.data.BoundingBox.fromData` 함수를 사용하여 시작 및 끝 지점의 경계 상자가 계산됩니다. 이 경계 상자는 `map.setCamera` 함수를 사용하여 전체 경로에 대해 맵 카메라 보기를 설정하는 데 사용됩니다. 기호 아이콘의 픽셀 크기를 보정하기 위해 안쪽 여백이 추가됩니다.

4. 파일을 저장하고, 브라우저를 새로 고쳐 지도에 표시된 핀을 확인합니다. 이제 지도 중심이 시애틀로 이동됩니다. 출발점을 표시하는 둥근 파란색 핀과 도착점을 표시하는 파란색 핀을 볼 수 있습니다.

   ![출발 지점과 도착 지점이 포함된 지도 보기](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>여행 모드에 따라 우선 순위가 지정된 경로 렌더링

이 섹션에서는 Maps 경로 서비스 API를 사용하는 방법을 보여줍니다. 경로 API는 운송 모드에 따라 지정된 출발점과 도착점 간의 여러 경로를 찾는 데 사용됩니다. 경로 서비스는 *최소 시간*, *최단 거리*, *에코* 또는 *모험* 경로를 계획할 수 있는 API를 제공합니다. API는 두 위치 간의 경로를 계획할 뿐만 아니라 현재 교통 상황도 고려합니다. 

경로 API를 통해 Azure의 광범위한 교통 기록 데이터베이스를 사용해 향후 경로를 계획할 수 있습니다. API는 지정된 날짜와 시간에 대한 경로 기간을 예측할 수 있습니다. 자세한 내용은 [경로 방향 가져오기](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)를 참조하세요. 

맵이 완전히 로드된 후에 로드되도록 하려면 다음 코드 블록을 모두 **맵 로드 eventListener 내**에 추가해야 합니다.

1. GetMap 함수에서 다음 JavaScript 코드를 추가합니다.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`은 구독 키를 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 `SubscriptionKeyCredentialPolicy`를 만듭니다. `atlas.service.MapsURL.newPipeline()`은 `SubscriptionKeyCredential` 정책을 인식하고 [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) 인스턴스를 만듭니다. `routeURL`은 Azure Maps [경로](https://docs.microsoft.com/rest/api/maps/route) 작업에 대한 URL을 나타냅니다.

2. 자격 증명 및 URL을 설정한 후 다음과 같은 JavaScript 코드를 추가하여 USHazmatClass2 등급 화물을 운반하는 트럭의 출발점~도착점 경로를 생성하고 결과를 표시합니다.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    위의 이 코드 조각은 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) 메서드를 통해 Azure Maps 라우팅 서비스를 쿼리합니다. 그런 후 `geojson.getFeatures()` 메서드를 사용하여 추출한 응답의 GeoJSON 기능 컬렉션에서 경로 선이 추출됩니다. 그런 다음, 경로 선이 데이터 원본에 추가됩니다. 인덱스 0은 데이터 원본의 다른 어떤 줄보다도 먼저 렌더링되도록 합니다. 이는 트럭 경로 계산이 승용차 경로 계산보다 느린 경우가 많기 때문에 수행됩니다. 승용차 경로 다음에 트럭 경로 선이 데이터 원본에 추가되면 그 위에 렌더링됩니다. 두 속성(멋진 파란색 음영의 스트로크 색 및 9픽셀의 스트로크 너비)이 트럭 경로 선에 추가됩니다.

3. 다음 JavaScript 코드를 추가하여 승용차 경로를 생성하고 결과를 표시합니다.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    위의 이 코드 조각은 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) 메서드를 통해 Azure Maps 라우팅 서비스를 쿼리합니다. 그런 후 `geojson.getFeatures()` 메서드를 사용하여 추출한 응답의 GeoJSON 기능 컬렉션에서 경로 선이 추출됩니다. 그런 다음, 경로 선이 데이터 원본에 추가됩니다. 두 속성(보라색 음영의 스트로크 색 및 5픽셀의 스트로크 너비)이 승용차 경로 선에 추가됩니다.  

4. **MapTruckRoute.html** 파일을 저장하고, 브라우저를 새로 고쳐 결과를 확인합니다. Maps API와 성공적으로 연결되면 다음과 비슷한 지도가 표시됩니다.

    ![Azure Route Service를 사용하여 우선 순위가 지정된 경로](./media/tutorial-prioritized-routes/prioritized-routes.png)

    트럭 경로는 진한 파란색이며 승용차 경로는 엷은 자주색입니다. 승용차 경로는 I-90 도로를 통해 워싱턴 호수를 가로질러 주거 지역 아래의 터널을 통과합니다. 터널은 주거 지역에 가깝기 때문에 위험한 폐기 화물은 제한됩니다. USHazmatClass2 화물 유형을 지정한 트럭 경로는 다른 고속 도로를 사용하도록 지시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 지도에서 교통 흐름 시각화
> * 여행 모드를 선언하는 경로 쿼리 만들기
> * 지도에 여러 경로 표시

> [!div class="nextstepaction"]
> [전체 소스 코드 보기](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [라이브 샘플 보기](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

다음 자습서에서는 Azure Maps를 사용하여 간단한 저장소 로케이터를 만드는 과정을 보여 줍니다.

> [!div class="nextstepaction"]
> [Azure Maps를 사용하여 저장소 로케이터 만들기](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)