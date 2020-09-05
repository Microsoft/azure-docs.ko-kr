---
title: '자습서: 맵에서 주변 위치 검색 | Microsoft Azure Maps'
description: 지도에서 관심 지점을 검색하는 방법을 알아봅니다. Azure Maps Web SDK를 사용하여 검색 기능 및 대화형 팝업 상자를 맵에 추가하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 8dc430febb25283ab5bd32496bb2f71ba19c895b
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035861"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>자습서: Azure Maps를 사용하여 주변 관심 지점 검색

이 자습서에서는 Azure Maps를 사용하여 계정을 설정한 다음, Maps API를 사용하여 관심 지점을 검색하는 방법을 보여 줍니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Maps 계정 만들기
> * Maps 계정에 대한 기본 키 검색
> * 지도 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * Maps 검색 서비스를 사용하여 주변 관심 지점 찾기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Azure Maps를 사용하여 계정 만들기

다음 단계에 따라 새 Maps 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. *마켓플레이스 검색* 상자에서 **Maps**를 입력합니다.
3. *결과*에서 **Maps**를 선택합니다. 맵 아래에 나타나는 **만들기** 단추를 클릭합니다.
4. **Maps 계정 만들기** 페이지에서 다음 값을 입력합니다.
    * 이 계정에 사용하려는 *구독*.
    * 이 계정에 대한 *리소스 그룹* 이름. *새로 만들기* 또는 *기존* 리소스 그룹 사용을 선택할 수도 있습니다.
    * 새 계정의 *이름*.
    * 이 계정에 대한 *가격 책정 계층*입니다.
    * *라이선스* 및 *개인정보처리방침*을 읽고 조건에 동의하는 확인란을 선택합니다.
    * **만들기** 단추를 클릭합니다.

![Azure Portal에서 Azure Maps 계정 만들기](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>사용자 계정에 대한 기본 키 가져오기

Maps 계정이 성공적으로 만들어지면 Maps API를 쿼리할 수 있는 키를 검색합니다. Azure Maps 서비스를 호출할 때 계정의 기본 키를 구독 키로 사용하는 것이 좋습니다.

1. 포털에서 Maps 계정을 엽니다.
2. 설정 섹션에서 **인증**을 선택합니다.
3. **기본 키**를 클립보드로 복사합니다. 이 자습서의 뒷부분에서 사용하기 위해 로컬로 저장합니다.

![Azure Portal에서 기본 키 가져오기](./media/tutorial-search-location/get-key.png)

Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

<a id="createmap"></a>

## <a name="create-a-new-map"></a>새 지도 만들기

맵 컨트롤 API는 편리한 클라이언트 라이브러리입니다. 이 API를 사용하면 맵을 웹 애플리케이션에 쉽게 통합할 수 있습니다. 기본 REST 서비스 호출의 복잡성을 숨기고 사용자 지정 가능한 구성 요소를 사용하여 생산성을 향상합니다. 다음 단계에서는 지도 컨트롤 API가 포함된 정적 HTML 페이지를 만드는 방법을 보여줍니다.

1. 로컬 컴퓨터에서 새 파일을 만들고 이름을 **MapSearch.html**로 지정합니다.
2. 다음 HTML 구성 요소를 파일에 추가합니다.

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   HTML 헤더는 Azure 맵 컨트롤 라이브러리에서 호스팅하는 CSS 및 JavaScript 리소스 파일을 포함합니다. 페이지의 본문의 `onload` 이벤트는 페이지 본문이 로드될 때 `GetMap` 함수를 호출합니다. `GetMap` 함수에는 Azure Maps API에 액세스하는 인라인 JavaScript 코드가 포함됩니다.

3. 다음 JavaScript 코드를 HTML 파일의 `GetMap` 함수에 추가하세요. 문자열 `<Your Azure Maps Key>`를 Maps 계정에서 복사한 기본 키로 바꿉니다.

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

   이 세그먼트는 Azure Maps 계정 키에 대한 맵 컨트롤 API를 초기화합니다. `atlas`는 API 및 관련 시각적 구성 요소가 포함된 네임스페이스입니다. `atlas.Map`은 시각적 및 대화형 웹 지도에 대한 컨트롤을 제공합니다.

4. 변경 내용을 파일에 저장하고 브라우저에서 해당 HTML 페이지를 엽니다. 표시된 맵은 계정 키를 통해 `atlas.Map`을 호출하여 만들 수 있는 가장 기본적인 지도입니다.

   ![지도 보기](./media/tutorial-search-location/basic-map.png)

5. `GetMap` 함수에서 맵을 초기화한 후 다음 JavaScript 코드를 추가합니다.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   이 코드 세그먼트에서 `ready` 이벤트가 맵에 추가되며, 맵 리소스가 로드되어 맵에 액세스할 준비가 되면 실행됩니다. 맵 `ready` 이벤트 처리기에서, 결과 데이터를 저장하는 데이터 원본이 만들어집니다. 기호 레이어가 생성되어 데이터 원본에 연결됩니다. 이 계층은 데이터 원본의 결과 데이터를 렌더링하는 방법을 지정합니다. 이 경우 결과는 결과 좌표를 중심으로 짙은 파란색 둥근 핀 아이콘으로 렌더링되고 다른 아이콘이 겹칠 수 있습니다. 결과 레이어는 맵 레이어에 추가됩니다.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>검색 기능 추가

이 섹션에서는 Maps [검색 API](https://docs.microsoft.com/rest/api/maps/search)를 사용하여 맵에서 관심 지점을 찾는 방법을 보여줍니다. 이는 개발자가 주소, 관심 지점 및 다른 지리적 위치 정보를 검색할 수 있도록 설계된 RESTful API입니다. 검색 서비스에서 지정된 주소에 위도와 경도 정보를 할당합니다. 아래에서 설명하는 **서비스 모듈**은 Maps Search API를 사용하여 위치를 검색하는 데 사용할 수 있습니다.

### <a name="service-module"></a>서비스 모듈

1. 맵 `ready` 이벤트 처리기에서 다음 JavaScript 코드를 추가하여 검색 서비스 URL을 구성합니다.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   `SubscriptionKeyCredential`은 구독 키를 사용하여 Azure Maps에 대한 HTTP 요청을 인증하는 `SubscriptionKeyCredentialPolicy`를 만듭니다. `atlas.service.MapsURL.newPipeline()`은 `SubscriptionKeyCredential` 정책을 인식하고 [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) 인스턴스를 만듭니다. `searchURL`은 Azure Maps [검색](https://docs.microsoft.com/rest/api/maps/search) 작업에 대한 URL을 나타냅니다.

2. 다음 스크립트 블록을 추가하여 검색 쿼리를 작성합니다. Search Service의 기본 검색 API인 유사 항목 검색 서비스를 사용합니다. 유사 항목 검색 서비스는 주소, 위치, POI(관심 지점) 같은 대부분의 유사 항목 입력을 처리합니다. 이 코드는 제공된 위도와 경도의 지정된 반경 내에서 인접한 주유소를 검색합니다. 그런 다음, `geojson.getFeatures()` 메서드를 사용하여 응답의 GeoJSON 기능 컬렉션을 추출하고 데이터 원본에 추가하면, 기호 레이어를 통해 데이터가 맵에 자동으로 렌더링됩니다. 스크립트의 마지막 부분은 맵의 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 속성을 사용하여 결과의 경계 상자를 사용하는 카메라 보기를 설정합니다.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. **MapSearch.html** 파일을 저장하고, 브라우저를 새로 고칩니다. 해당 지역의 주유소 위치에 둥근 파란색 핀이 있는 시애틀 중심으로 맵이 표시됩니다.

   ![검색 결과가 포함된 지도 보기](./media/tutorial-search-location/pins-map.png)

4. 브라우저에 다음 HTTPRequest를 입력하여 지도에서 렌더링하는 원시 데이터를 볼 수 있습니다. \<Your Azure Maps Key\>를 기본 키로 바꿉니다.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

이 시점에서 MapSearch 페이지에는 유사 항목 검색 쿼리에서 반환된 관심 지점의 위치가 표시될 수 있습니다. 몇 가지 대화형 기능과 위치에 대한 자세한 정보를 추가해 보겠습니다.

## <a name="add-interactive-data"></a>대화형 데이터 추가

지금까지 만든 맵은 검색 결과의 위도/경도 데이터만 살펴봅니다. 그러나 맵 검색 서비스에서 반환하는 원시 JSON에는 각 주유소에 대한 추가 정보가 포함되어 있습니다. 이름과 주소를 포함합니다. 대화형 팝업 상자를 사용하여 해당 데이터를 맵에 통합할 수 있습니다.

1. 맵 `ready` 이벤트 처리기에서 유사 항목 검색 서비스를 쿼리하는 코드 뒤에 다음 코드 줄을 추가합니다. 이 코드에는 팝업 인스턴스가 만들어지고 기호 레이어에 mouseover 이벤트가 추가됩니다.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    API `*atlas.Popup`은 맵의 필수 지점에 고정된 정보 창을 제공합니다. 

2. `GetMap` 함수 내에 mouseover 결과 정보를 팝업에 표시하는 다음 코드를 추가합니다.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. 파일을 저장하고 브라우저를 새로 고칩니다. 이제 브라우저의 지도에서 검색 핀 위를 마우스로 가리키면 정보 팝업이 표시됩니다.

    ![Azure 맵 컨트롤 및 Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Maps를 사용하여 계정 만들기
> * 사용자 계정에 대한 기본 키 가져오기
> * 맵 컨트롤 API를 사용하여 새 웹 페이지 만들기
> * 주변 관심 지점을 찾기 위해 Search Service 사용

> [!div class="nextstepaction"]
> [전체 소스 코드 보기](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [라이브 샘플 보기](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

다음 자습서에서는 두 위치 사이의 경로를 표시하는 방법을 보여 줍니다.

> [!div class="nextstepaction"]
> [대상으로 라우팅](./tutorial-route-location.md)
