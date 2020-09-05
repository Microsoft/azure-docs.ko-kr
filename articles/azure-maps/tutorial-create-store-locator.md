---
title: '자습서: Azure Maps를 사용하여 매장 로케이터 애플리케이션 만들기 | Microsoft Azure Maps'
description: 스토어 로케이터 웹 애플리케이션을 만드는 방법에 대해 알아봅니다. Azure Maps Web SDK를 사용하여 웹 페이지를 만들고, 검색 서비스를 쿼리하고, 지도에 결과를 표시합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: 1ec4dbb1ce55919fda6c73d198100db34f5f57ea
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121258"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>자습서: Azure Maps를 사용하여 매장 로케이터 만들기

이 자습서에서는 Azure Maps를 사용하여 간단한 매장 로케이터를 만드는 과정을 안내합니다. 매장 로케이터는 일반적인 프로그램입니다. 이 유형의 애플리케이션에서 사용되는 개념 대부분이 다른 많은 애플리케이션에도 적용됩니다. 고객에게 매장 로케이터를 제공하는 일은 소비자에게 직접 제품을 판매하는 대부분의 기업에게 있어서 반드시 필요한 작업입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.
    
> [!div class="checklist"]
> * Azure Map Control API를 사용하여 새 웹 페이지를 만듭니다.
> * 파일에서 사용자 지정 데이터를 로드하고 지도에 표시합니다.
> * Azure Maps Search 서비스를 사용하여 주소를 찾거나 쿼리를 입력합니다.
> * 브라우저에서 사용자의 위치를 가져온 후 지도에 표시합니다.
> * 여러 계층을 조합하여 지도에 사용자 지정 기호를 만듭니다.  
> * 데이터 요소를 클러스터로 묶습니다.  
> * 지도에 확대/축소 컨트롤을 추가합니다.

<a id="Intro"></a>

[라이브 매장 로케이터 예제](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) 또는 [소스 코드](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)로 이동합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [S1 가격 책정 계층을 사용하여 Azure Maps 계정 만들기](quick-demo-map-app.md#create-an-azure-maps-account)
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.

## <a name="design"></a>디자인

코드로 이동하기 전에 디자인부터 시작하는 것이 좋습니다. 매장 로케이터를 원하는 만큼 단순하거나 복잡하게 구성할 수 있습니다. 이 자습서에서는 간단한 매장 로케이터를 만듭니다. 원할 경우 일부 기능을 확장하는 데 도움이 되도록 몇 가지 팁을 전체적으로 포함할 것입니다. Contoso Coffee라는 가상의 회사를 위한 매장 로케이터를 만듭니다. 다음 그림에서는 이 자습서에서 구축하는 매장 로케이터의 일반 레이아웃 와이어프레임을 보여 줍니다.

![Contoso 커피숍 위치에 대한 매장 로케이터 애플리케이션의 와이어프레임](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)

이 매장 로케이터의 유용성을 최대화하기 위해 사용자의 화면 너비가 700픽셀보다 작은 경우 조정되는 반응형 레이아웃을 포함합니다. 반응형 레이아웃이 있으면 모바일 디바이스와 같은 작은 화면에서 매장 로케이터를 쉽게 사용할 수 있습니다. 소형 화면 레이아웃의 와이어프레임은 다음과 같습니다.  

![모바일 디바이스의 Contoso Coffee 매장 로케이터 애플리케이션 와이어프레임](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</

이 와이어프레임은 매우 간단한 애플리케이션을 보여 줍니다. 애플리케이션에는 검색 상자, 인근 상점 목록, 기호 등 일부 마커가 있는 지도가 있습니다. 또한 사용자가 마커를 선택할 때 추가 정보를 표시하는 팝업 창이 있습니다. 이 자습서에서 이 매장 로케이터에 구축하는 기능을 좀 더 자세히 살펴보면 다음과 같습니다.

* 가져온 탭으로 구분된 데이터 파일의 모든 위치가 지도에 로드됩니다.
* 사용자는 지도를 이동 및 확대/축소하고, 검색하고, 내 위치 GPS 단추를 선택할 수 있습니다.
* 페이지 레이아웃은 디바이스 화면 너비에 따라 조정됩니다.  
* 헤더에는 매장 로고가 표시됩니다.  
* 사용자는 검색 상자 및 검색 단추를 사용하여 주소, 우편 번호 또는 구/군/시와 같은 위치를 검색할 수 있습니다. 
* 사용자가 Enter 키를 누르면 검색 상자에 추가된 `keypress` 이벤트가 트리거됩니다. 이 기능은 종종 간과되지만 더 나은 사용자 환경을 만들어 줍니다.
* 지도가 이동되면 지도 중앙에서 각 위치까지의 거리가 계산됩니다. 결과 목록이 업데이트되면서 지도 맨 위에 가장 가까운 위치가 표시됩니다.  
* 결과 목록에서 결과를 선택하면 지도 중심이 선택한 위치로 이동되며, 해당 위치에 대한 정보가 팝업 창에 표시됩니다.  
* 지도에서 특정 위치를 선택해도 팝업 창이 트리거됩니다.
* 사용자가 축소하면 위치가 클러스터로 그룹화됩니다. 클러스터는 내부에 숫자가 있는 원으로 표시됩니다. 사용자가 확대/축소 수준을 변경하면 클러스터가 형성되고 분리됩니다.
* 클러스터를 선택하면 지도가 두 수준으로 확대되고, 클러스터 위치가 중심이 됩니다.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>매장 위치 데이터 세트 만들기

매장 로케이터 애플리케이션을 개발하기 전에 지도에 표시하려는 매장의 데이터 세트를 만들어야 합니다. 이 자습서에서는 Contoso Coffee라는 가상의 커피숍에 대한 데이터 세트를 사용합니다. 이 간단한 매장 로케이터의 데이터 세트는 Excel 통합 문서에서 관리됩니다. 데이터 세트에는 미국, 캐나다, 영국, 프랑스, 독일, 이탈리아, 네덜란드, 덴마크 및 스페인의 9개 국가/지역에 분산되어 있는 10,213개의 Contoso Coffee 커피숍 위치가 포함되어 있습니다. 다음은 이러한 데이터를 보여 주는 스크린샷입니다.

![Excel 통합 문서에 있는 매장 로케이터 데이터의 스크린샷](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)

[Excel 통합 문서를 다운로드](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)할 수 있습니다.

데이터의 스크린샷을 살펴보면 다음을 확인할 수 있습니다.

* 위치 정보는 **AddressLine**, **City**, **Municipality**(준주), **AdminDivision**(시/도), **PostCode**(우편 번호) 및 **Country** 열을 사용하여 저장합니다.  
* **Latitude** 및 **Longitude** 열에는 각 Contoso Coffee 커피숍 위치의 좌표가 포함됩니다. 좌표 정보가 없으면 Azure Maps의 Search Service를 사용하여 위치 좌표를 확인할 수 있습니다.
* 몇 개의 추가 열에 커피숍에 관련된 메타데이터, 즉 전화 번호, 부울 열 및 매장 개장 및 마감 시간(24시간 형식)이 포함됩니다. 부울 열은 Wi-Fi 핫스폿 및 휠체어 이용 가능성을 위한 것입니다. 위치 데이터와 관련성이 좀 더 깊은 메타데이터를 포함하는 고유한 열을 만들 수 있습니다.

> [!NOTE]
> Azure Maps는 구형 Mercator 프로젝션 "EPSG:3857"의 데이터를 렌더링하되, WGS84 데이터를 사용하는 "EPSG:4325"의 데이터를 읽습니다.

애플리케이션에 데이터 세트를 노출하는 방법은 다양합니다. 한 가지 방법은 데이터베이스에 데이터를 로드하고, 데이터를 쿼리하는 웹 서비스를 노출하는 것입니다. 그런 다음, 결과를 사용자의 브라우저로 보낼 수 있습니다. 이 옵션은 대형 데이터 세트 또는 자주 업데이트되는 데이터 세트에 이상적입니다. 그러나 이 옵션을 제공하려면 더 많은 개발 작업이 필요하며 비용도 많이 듭니다.

다른 방법은 이 데이터 세트를 브라우저가 쉽게 구문 분석할 수 있는 일반 텍스트 파일로 변환하는 것입니다. 파일 자체는 애플리케이션의 나머지와 함께 유지될 수 있습니다. 이 옵션을 사용하면 데이터를 보다 간단히 유지할 수 있지만, 사용자가 모든 데이터를 다운로드하게 되므로 데이터 세트 크기가 작을 때만 적절합니다. 이 데이터 세트의 경우 데이터 파일 크기가 1MB 미만이기 때문에 일반 텍스트 파일을 사용합니다.  

통합 문서를 일반 텍스트 파일로 변환하려면 통합 문서를 탭으로 구분된 파일로 저장합니다. 각 열은 탭 문자로 구분되므로 코드에서 열을 쉽게 구문 분석할 수 있습니다. CSV(쉼표로 구분된 값) 형식을 사용할 수 있지만 이 옵션을 사용하려면 더 많은 구문 분석 논리가 필요합니다. 주변에 쉼표가 있는 모든 필드는 따옴표로 묶어야 합니다. 이 데이터를 Excel에 탭으로 구분된 파일로 내보내려면 **다른 이름으로 저장**을 선택합니다. **저장 형식** 드롭다운 목록에서 **텍스트(탭 구분)(*.txt)** 를 선택합니다. 파일 이름을 *ContosoCoffee.txt*로 지정합니다.

![저장 형식 대화 상자 스크린샷](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)

텍스트 파일을 메모장에서 열면 다음 그림과 유사하게 나타납니다.

![탭으로 구분된 데이터 세트를 보여주는 메모장 파일 스크린샷](./media/tutorial-create-store-locator/StoreDataTabFile.png)

## <a name="set-up-the-project"></a>프로젝트 설정

프로젝트를 만들려면 [Visual Studio](https://visualstudio.microsoft.com) 또는 원하는 코드 편집기를 사용할 수 있습니다. 프로젝트 폴더에서 세 개의 파일 *index.html*, *index.css*, 및 *index.js*를 만듭니다. 이러한 파일은 애플리케이션에 대한 레이아웃, 스타일 및 논리를 정의합니다. *data*라는 폴더를 만들고 이 폴더에 *ContosoCoffee.txt*를 추가합니다. *images*라는 다른 폴더를 만듭니다. 이 애플리케이션에서는 지도의 아이콘, 단추 및 마커에 10개의 이미지를 사용합니다. [이러한 이미지를 다운로드](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)할 수 있습니다. 이제 프로젝트 폴더는 다음 그림과 같이 표시됩니다.

![간단한 매장 로케이터 프로젝트 폴더 스크린샷](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)

## <a name="create-the-user-interface"></a>사용자 인터페이스 만들기

사용자 인터페이스를 만들려면 *index.html*에 코드를 추가합니다.

1. 다음 `meta` 태그를 *index.html*의 `head`에 추가합니다. `charset` 태그는 문자 집합(UTF-8)을 정의합니다. `http-equiv` 값은 Internet Explorer 및 Microsoft Edge에서 최신 브라우저 버전을 사용하고 있음을 뜻합니다. 마지막 `meta` 태그는 반응형 레이아웃에 적합한 뷰포트를 지정합니다.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Azure Maps 웹 컨트롤 JavaScript 및 CSS 파일에 대한 참조를 추가합니다.

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Azure Maps 서비스 모듈에 대한 참조를 추가합니다. 이 모듈은 Azure Maps REST 서비스를 래핑하고 JavaScript에서 쉽게 사용할 수 있도록 하는 JavaScript 라이브러리입니다. 이 모듈은 검색 기능을 강화하는 데 유용합니다.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. *index.js* 및 *index.css*에 대한 참조를 추가합니다.

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. 문서 본문에 `header` 태그를 추가합니다. `header` 태그 내부에 로고 및 회사 이름을 추가합니다.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. `main` 태그를 추가하고, 텍스트 상자 및 검색 단추가 있는 검색 패널을 만듭니다. 또한 지도, 목록 패널 및 내 위치 GPS 단추에 대한 `div` 참조를 추가합니다.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

완료되면 *index.html*은 [이 예제 index.html 파일](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html)과 같습니다.

다음 단계는 CSS 스타일을 정의하는 것입니다. CSS 스타일은 애플리케이션 구성 요소가 배치되는 방식과 애플리케이션 모양을 정의합니다. *index.css*를 열고 다음 코드를 추가합니다. `@media` 스타일은 화면 너비가 700픽셀보다 작을 때 사용할 대체 스타일 옵션을 정의합니다.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

지금 애플리케이션을 실행하면 헤더, 검색 상자 및 검색 단추가 나타납니다. 하지만, 맵이 아직 로드되지 않았으므로 표시되지 않습니다. 검색을 수행하려는 경우 아무 작업도 발생하지 않습니다. 다음 섹션에서 설명하는 JavaScript 논리를 설정해야 합니다. 이 논리는 저장 로케이터의 모든 기능에 액세스합니다.

## <a name="wire-the-application-with-javascript"></a>애플리케이션을 JavaScript에 연결

이제 모든 항목이 사용자 인터페이스에서 설정됩니다. JavaScript를 추가하여 데이터를 로드 및 구문 분석한 다음, 지도에서 데이터를 렌더링해야 합니다. 시작하려면 *index.js*를 열고 다음 단계에 설명된 대로 코드를 추가합니다.

1. 설정을 보다 쉽게 업데이트할 수 있도록 하는 전역 옵션을 추가합니다. 지도, 팝업 창, 데이터 원본, 아이콘 계층 및 HTML 표식에 대한 변수를 정의합니다. 검색 영역의 중심을 나타내도록 HTML 표식을 설정합니다. 그리고 Azure Maps 검색 서비스 클라이언트의 인스턴스를 정의합니다.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. *index.js*에 코드를 추가합니다. 다음 코드는 맵을 초기화합니다. 페이지 로드가 완료될 때까지 대기하도록 [이벤트 수신기](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)를 추가했습니다. 그런 다음, 이벤트를 연결하여 지도의 로드를 모니터링하고 검색 단추와 내 위치 단추에 기능을 제공합니다.

   사용자가 검색 단추를 선택하거나 검색 상자에 위치를 입력한 다음, Enter를 누르면 사용자의 쿼리에 대한 유사 항목 검색이 시작됩니다. 국가/지역 ISO 2개 값 배열을 `countrySet` 옵션에 제공하여 검색 결과를 해당 국가/지역으로 제한할 수 있습니다. 검색할 국가/지역을 제한하면 반환되는 결과의 정확도를 높이는 데 도움이 됩니다. 
  
   검색이 완료되면 첫 번째 결과를 가져와 해당 영역 위에 지도 카메라를 설정합니다. 사용자가 내 위치 단추를 선택하면 HTML5 지리적 위치 API를 사용하여 사용자의 위치를 검색합니다. 이 API는 브라우저에 기본 제공됩니다. 그런 다음, 지도의 위치를 가운데에 맞춥니다.  

   > [!Tip]
   > 팝업 창을 사용할 경우 단일 `Popup` 인스턴스를 만든 후 해당 콘텐츠 및 위치를 업데이트하여 인스턴스를 다시 사용하는 것이 좋습니다. 코드에 추가하는 모든 `Popup` 인스턴스에 대해 페이지에 여러 DOM 요소가 추가됩니다. 페이지에 더 많은 DOM 요소가 있을수록 브라우저가 추적할 내용이 더 많아집니다. 너무 많은 항목이 있는 경우 브라우저가 느려질 수 있습니다.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country/region ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country/region ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. 지도의 `ready` 이벤트 수신기에서 확대/축소 컨트롤과 검색 영역 중앙에 표시할 HTML 표식을 추가합니다.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. 지도의 `ready` 이벤트 수신기에서 데이터 원본을 추가합니다. 그런 후 데이터 세트를 로드한 후 구문 분석하기 위한 호출을 수행합니다. 데이터 원본에서 클러스터링을 사용하도록 설정합니다. 데이터 원본에 대해 클러스터링을 지정하면 겹치는 지점이 하나의 클러스터로 그룹화됩니다. 이러한 클러스터는 사용자가 확대하면 개별 점으로 분리됩니다. 이 동작은 더 나은 사용자 환경을 제공하고 성능을 향상시킵니다.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. 지도의 `ready` 이벤트 수신기에 데이터 세트를 로드한후 에 데이터를 렌더링하기 위한 계층 세트를 정의합니다. 클러스터형 데이터 요소를 렌더링하는 데는 풍선 계층이 사용됩니다. 각 클러스터의 요소 수를 풍선 계층 위에 렌더링하는 데 기호 계층이 사용됩니다. 두 번째 기호 계층은 지도의 개별 위치에 대한 사용자 지정 아이콘을 렌더링합니다.

   풍선 및 아이콘 계층에 `mouseover` 및 `mouseout` 이벤트를 추가하여 사용자가 지도에서 클러스터 또는 아이콘 위로 마우스를 가져갈 때 마우스 커서가 변경되도록 합니다. 클러스터 풍선 계층에 `click` 이벤트를 추가합니다. 이 `click` 이벤트는 두 수준으로 지도를 확대하고 사용자가 클러스터를 선택하면 지도 중심을 해당 클러스터로 이동합니다. 아이콘 계층에 `click` 이벤트를 추가합니다. 이 `click` 이벤트는 사용자가 개별 위치 아이콘을 선택할 때 커피숍 세부 정보를 표시하는 팝업 창을 표시합니다. 지도 이동이 완료되면 모니터링할 지도에 이벤트를 추가합니다. 이 이벤트가 발생하면 목록 패널에서 항목이 업데이트됩니다.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. 커피숍 데이터 세트가 로드되면 목록 패널이 먼저 다운로드됩니다. 그런 다음, 텍스트 파일이 줄로 분할됩니다. 첫 번째 줄에는 헤더 정보가 포함됩니다. 코드를 보다 쉽게 따라가도록 하기 위해 헤더를 개체로 구문 분석합니다. 그러면 이러한 개체를 사용하여 각 속성의 셀 인덱스를 조회할 수 있습니다. 첫 번째 줄 이후에 나머지 줄을 반복하면서 지점 피쳐를 만듭니다. 데이터 원본에 지점 피쳐를 추가합니다. 마지막으로 목록 패널을 업데이트합니다.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. 목록 패널이 업데이트되면 거리가 계산됩니다. 이 거리는 지도의 중심에서 현재 지도 보기의 모든 지점 기능까지 이르는 거리입니다. 그런 후 피쳐가 거리별로 정렬됩니다. 목록 패널에 각 위치를 표시하기 위해 HTML이 생성됩니다.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. 사용자가 목록 패널에서 항목을 선택하면 항목과 관련된 도형이 데이터 원본에서 검색됩니다. 도형에 저장된 속성 정보를 기준으로 하는 팝업 창이 생성됩니다. 지도 중심이 도형으로 이동됩니다. 지도 폭이 700픽셀보다 작으면 팝업 창이 표시되도록 지도 뷰가 오프셋됩니다.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

이제 모든 기능을 갖춘 매장 로케이터가 구현되었습니다. 웹 브라우저에서 매장 로케이터에 대한 *index.html* 파일을 엽니다. 클러스터가 지도에 표시되면 검색 상자를 사용하거나, 내 위치 단추를 선택하거나, 클러스터를 선택하거나, 지도를 확대하여 개별 위치를 확인하는 방식으로 위치를 검색할 수 있습니다.

사용자가 내 위치 단추를 처음 선택하면 브라우저에 사용자 위치에 액세스하기 위한 권한을 요구하는 보안 경고가 표시됩니다. 사용자가 해당 위치를 공유하는 데 동의하면 지도가 사용자 위치에서 확대되고 근처의 커피숍이 표시됩니다.

![사용자의 위치에 액세스하기 위한 브라우저 요청 스크린샷](./media/tutorial-create-store-locator/GeolocationApiWarning.png)

커피숍 위치가 포함된 영역에서 충분히 가깝게 확대하면 클러스터가 개별 위치로 분리됩니다. 지도에서 아이콘 중 하나를 선택하거나 측면 패널에서 항목을 선택하여 팝업 창을 표시합니다. 선택한 위치에 대한 정보가 팝업에 표시됩니다.

![완료된 매장 로케이터 스크린샷](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)

브라우저 창 너비를 700픽셀보다 작게 조정하거나 모바일 디바이스에서 애플리케이션을 열 경우 작은 화면에 더 잘 맞게 레이아웃이 변경됩니다.

![작은 화면 버전의 매장 로케이터 스크린샷](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Maps를 사용하여 기본 매장 로케이터를 만드는 방법을 알아봅니다. 이 자습서에서 만드는 매장 로케이터에는 필요한 모든 기능이 있을 수 있습니다. 매장 로케이터에 기능을 추가하거나 더 많은 사용자 지정 사용자 환경을 위해 더 많은 고급 기능을 사용할 수 있습니다. 

> [!div class="checklist"]
> * 검색 상자에서 [입력할 때 제안 사항](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI)이 표시되도록 설정합니다.  
> * [여러 언어 지원](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization)을 추가합니다. 
> * [경로를 따라 위치를 필터링](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route)하도록 합니다. 
> * [필터를 설정](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property)하는 기능을 추가합니다. 
> * 쿼리 문자열을 사용하여 초기 검색 값을 지정하는 지원을 추가합니다. 매장 로케이터에 이 옵션을 포함하면 사용자가 검색에 책갈피를 지정하고 공유할 수 있습니다. 또한 다른 페이지에서 이 페이지로 검색을 전달하는 쉬운 방법도 제공됩니다.  
> * 매장 로케이터를 [Azure App Service Web App](https://docs.microsoft.com/azure/app-service/quickstart-html)으로 배포합니다. 
> * 데이터를 데이터베이스에 저장하고 근처 위치를 검색합니다. 자세한 내용은 [SQL Server 공간 데이터 형식 개요](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) 및 [공간 데이터에서 가장 인접한 항목 쿼리](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017)를 참조하세요.

> [!div class="nextstepaction"]
> [전체 소스 코드 보기](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [라이브 샘플 보기](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Azure Maps의 적용 범위 및 기능에 대해 자세히 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md)

더 많은 코드 예제와 대화형 코딩 환경을 살펴보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [맵 컨트롤을 사용하는 방법](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
