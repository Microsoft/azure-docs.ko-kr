---
title: '자습서: Google Maps에서 웹앱 마이그레이션 | Microsoft Azure Maps'
description: Google Maps에서 Microsoft Azure Maps로 웹앱을 마이그레이션하는 방법을 설명합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d9b873a058410219bc55abc4f575823b519a646b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989116"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Google Maps에서 웹앱 마이그레이션

Google Maps를 사용하는 대부분의 웹앱은 Google Maps V3 JavaScript SDK를 사용합니다. Azure Maps 웹 SDK는 마이그레이션에 적합한 Azure 기반 SDK입니다. Azure Maps 웹 SDK를 사용하면 고유한 콘텐츠와 이미지를 사용하여 대화형 맵을 사용자 지정할 수 있습니다. 웹 또는 모바일 애플리케이션 모두에서 앱을 실행할 수 있습니다. 이 컨트롤을 통해 WebGL을 사용하여 성능이 높은 대형 데이터 집합을 렌더링할 수 있습니다. JavaScript 또는 TypeScript를 사용하여 이 SDK로 개발하세요.

기존 웹 애플리케이션을 마이그레이션하는 경우 오픈 소스 맵 컨트롤 라이브러리를 사용하고 있는지 확인하세요. 오픈 소스 맵 컨트롤 라이브러리의 예로 Cesium, Leaflet 및 OpenLayers가 있습니다. 사용 중이고 Azure Maps 웹 SDK를 사용하지 않으려는 경우에는 애플리케이션을 마이그레이션하는 또 다른 옵션으로 오픈 소스 지도 컨트롤을 계속 사용하여 Azure Maps 타일 서비스([도로 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [위성 타일](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile))에 연결하는 방법이 있습니다. 다음은 일반적으로 사용되는 오픈 소스 맵 컨트롤 라이브러리 중 일부에서 Azure Maps를 사용하는 방법에 대해 자세히 설명합니다.

- Cesium - 웹용 3D 지도 컨트롤입니다. [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [설명서](https://cesiumjs.org/)
- Leaflet – 웹용 경량 2D 지도 컨트롤입니다. [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [설명서](https://leafletjs.com/)
- OpenLayers - 프로젝션을 지원하는 웹용 2D 지도 컨트롤입니다. [코드 샘플](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [설명서](https://openlayers.org/)

## <a name="key-features-support"></a>주요 기능 지원

다음 표에는 Google Maps V3 JavaScript SDK의 주요 API 기능 및 Azure Maps 웹 SDK에서 지원되는 API 기능이 나와 있습니다.

| Google Maps 기능     | Azure Maps 웹 SDK 지원 |
|-------------------------|:--------------------------:|
| 표식                 | ✓                          |
| 표식 클러스터링       | ✓                          |
| 폴리라인 및 다각형    | ✓                          |
| 데이터 레이어             | ✓                          |
| 그라운드 오버레이         | ✓                          |
| 열 지도               | ✓                          |
| 타일 레이어             | ✓                          |
| KML 레이어               | ✓                          |
| 그리기 도구           | ✓                          |
| Geocoder 서비스        | ✓                          |
| 방향 서비스      | ✓                          |
| 거리 행렬 서비스 | ✓                          |
| 권한 상승 서비스       | 계획                    |

## <a name="notable-differences-in-the-web-sdks"></a>웹 SDK의 주목할 만한 차이점

알고 있어야 하는 Google Maps와 Azure Maps 웹 SDK의 주요 차이점 중 일부는 다음과 같습니다.

- Azure Maps 웹 SDK에 액세스하기 위한 호스트된 엔드포인트를 제공하는 것 외에도, 원하는 경우 NPM 패키지를 사용하여 웹 SDK를 앱에 포함할 수 있습니다. 자세한 내용은 이 [설명서](how-to-use-map-control.md)를 참조하세요. 이 패키지에는 TypeScript 정의도 포함됩니다.
- Azure Maps에서 Map 클래스의 인스턴스를 만든 후, 코드에서는 맵 `ready` 또는 `load` 이벤트가 발생할 때까지 기다렸다가 맵과 상호 작용합니다. 이 순서는 모든 맵 리소스가 로드되어 액세스할 준비가 되었는지 확인합니다.
- 두 플랫폼 모두 기본 맵에 비슷한 바둑판식 배열 시스템을 사용하지만, Google Maps의 타일은 크기가 256픽셀이고 Azure Maps의 타일은 512픽셀입니다. 따라서 Azure Maps에서 Google Maps와 동일한 맵 보기를 만들려면 Azure Maps에서는 Google Maps에 사용되는 확대/축소 수준에서 1을 빼야 합니다.
- Google Maps에서 좌표는 "위도, 경도"라고 하며, Azure Maps에서는 "경도, 위도"를 사용합니다. Azure Maps 형식은 대부분의 GIS 플랫폼에서 따르는 `[x, y]` 표준과 일치합니다.
- Azure Maps 웹 SDK의 도형은 GeoJSON 스키마를 기반으로 합니다. 도우미 클래스는 [*atlas.data* 네임스페이스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest)를 통해 노출됩니다. [*atlas.Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) 클래스도 있습니다. 이 클래스는 GeoJSON 개체를 래핑하고, 이를 데이터 바인딩 가능한 방식으로 쉽게 업데이트하고 유지 관리하는 데 사용할 수 있습니다.
- Azure Maps에서 좌표는 Position 개체로 정의됩니다. 좌표는 `[longitude, latitude]` 형식의 숫자 배열로 지정되거나, 새 atlas.data.Position(경도, 위도)을 사용하여 지정됩니다.
    > [!TIP]
    > Position 클래스에는 "위도, 경도" 형식의 좌표를 가져올 수 있는 정적 도우미 메서드가 있습니다. [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) 메서드는 Google Maps 코드의 `new google.maps.LatLng` 메서드로 바꿀 수 있는 경우가 많습니다.
- 맵에 추가되는 각 도형에서 스타일 정보를 지정하는 대신, Azure Maps는 데이터에서 스타일을 분리합니다. 데이터는 데이터 원본에 저장되며 렌더링 레이어에 연결됩니다. Azure Maps 코드는 데이터 원본을 사용하여 데이터를 렌더링합니다. 이 접근 방법은 향상된 성능 혜택을 제공합니다. 또한 많은 레이어에서 비즈니스 논리를 레이어 스타일 옵션에 추가할 수 있는 데이터 기반 스타일 지정을 지원합니다. 이 지원은 셰이프에 정의된 속성에 따라 레이어 내에서 개별 셰이프를 렌더링하는 방법을 변경합니다.

## <a name="web-sdk-side-by-side-examples"></a>웹 SDK 화면 분할 예제

다음 컬렉션에는 각 플랫폼에 대한 코드 샘플이 있으며, 각 코드는 일반적인 사용 사례를 다룹니다. 이는 웹 애플리케이션을 Google Maps V3 JavaScript SDK에서 Azure Maps 웹 SDK로 마이그레이션하는 데 도움이 됩니다. 웹 애플리케이션과 관련된 코드 샘플은 JavaScript로 제공됩니다. 그러나 Azure Maps는 [NPM 모듈](how-to-use-map-control.md)을 통해 TypeScript 정의를 추가 옵션으로 제공합니다.

### <a name="load-a-map"></a>맵 로드

맵을 로드하는 작업은 두 SDK에서 동일한 단계 세트를 따릅니다.

- 맵 SDK에 대한 참조 추가
- 맵의 자리 표시자로 작동하는 `div` 태그를 페이지 본문에 추가합니다.
- 페이지가 로드될 때 호출되는 JavaScript 함수를 만듭니다.
- 각 map 클래스의 인스턴스를 만듭니다.

**주요 차이점**

- Google Maps를 사용하려면 API의 스크립트 참조에서 계정 키를 지정해야 합니다. Azure Maps의 인증 자격 증명은 map 클래스의 옵션으로 지정되며 이 자격 증명은 구독 키 또는 Azure Active Directory 정보일 수 있습니다.
- Google Maps는 초기화 함수를 호출하여 맵을 로드하는 데 사용되는 API의 스크립트 참조에서 콜백 함수를 사용합니다. Azure Maps에서는 페이지의 onload 이벤트를 사용해야 합니다.
- 맵이 렌더링될 `div` 요소를 참조하는 경우 Google Maps는 `HTMLElement` 개체가 필요하지만 Azure Maps의 `Map` 클래스에는 `id` 값만 필요합니다.
- Azure Maps의 좌표는 `[longitude, latitude]` 형식의 단순 숫자 배열로 지정할 수 있는 Position 개체로 정의됩니다.
- Azure Maps의 확대/축소 수준은 Google Maps의 확대/축소 수준보다 한 수준 낮습니다. 이러한 불일치는 두 플랫폼의 바둑판식 배열 시스템 크기 차이로 인해 발생합니다.
- Azure Maps는 탐색 컨트롤을 맵 캔버스에 추가하지 않습니다. 따라서 맵에는 기본적으로 확대/축소 단추와 맵 스타일 단추가 없습니다. 그러나 맵 스타일 선택기, 확대/축소 단추, 나침반 또는 회전 컨트롤 및 피치 컨트롤을 추가할 수 있는 컨트롤이 있습니다.
- Azure Maps에는 map 인스턴스의 `ready` 이벤트를 모니터링할 수 있는 이벤트 처리기가 추가되었습니다. 맵에서 WebGL 컨텍스트 및 필요한 모든 리소스의 로드가 완료되면 이 이벤트가 실행됩니다. 맵에서 로드가 완료되면 실행할 코드를 이 이벤트 처리기에 추가합니다.

아래 예제에서는 뉴욕 좌표(경도: -73.985, 위도: 40.747)를 중심으로 하고 Google Maps에서 확대/축소 수준이 12인 기본 맵을 보여줍니다.

**이전: Google Maps**

다음 코드는 특정 위치를 중심으로 확대/축소되는 Google 맵을 표시하는 방법의 예입니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

브라우저에서 이 코드를 실행하면 다음 이미지와 비슷한 맵이 표시됩니다.

<center>

![간단한 Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**이후: Azure Maps**

다음 코드는 맵 스타일 컨트롤 및 확대/축소 단추와 함께 Azure Maps에서 동일한 보기로 맵을 로드하는 방법을 보여줍니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

브라우저에서 이 코드를 실행하면 다음 이미지와 비슷한 맵이 표시됩니다.

<center>

![간단한 Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

웹앱에서 Azure Maps 지도 컨트롤을 설정하고 사용하는 방법에 대한 자세한 설명서는 [여기](how-to-use-map-control.md)서 찾을 수 있습니다.

> [!NOTE]
> Azure Maps는 Google Maps와 달리 맵을 로드할 때 초기 중심 및 확대/축소 수준을 지정할 필요가 없습니다. 맵을 로드할 때 이 정보를 제공하지 않으면 맵 자체에서 현재 사용자가 속한 도시를 확인하고 해당 도시를 기준으로 맵을 가운데에 맞추고 확대/축소하려고 시도합니다.

**추가 리소스:**

- 또한 Azure Maps는 [여기](map-add-controls.md)에 설명된 대로 맵 보기를 회전하고 피칭할 수 있는 탐색 컨트롤을 제공합니다.

### <a name="localizing-the-map"></a>맵 지역화

대상 그룹이 여러 국가에 분산되어 있거나 여러 언어를 사용하는 경우 지역화가 중요합니다.

**이전: Google Maps**

Google Maps를 지역화할 수 있도록 언어 및 지역 매개 변수가 추가되었습니다.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

다음은 언어가 "fr-FR"로 설정된 Google Maps의 예입니다.

<center>

![Google Maps 지역화](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**이후: Azure Maps**

Azure Maps는 맵의 언어 및 지역 보기를 설정하는 두 가지 방법을 제공합니다. 첫 번째 옵션은 이 정보를 글로벌 *atlas* 네임스페이스에 추가하는 것입니다. 그러면 앱의 모든 지도 컨트롤 인스턴스가 기본적으로 이 설정으로 지정됩니다. 다음은 언어를 프랑스어("fr-FR")로 설정하고 지역 보기를 "auto"로 설정합니다.

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

두 번째 옵션은 다음과 같이 맵을 로드할 때 맵 옵션에 이 정보를 전달하는 것입니다.

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Azure Maps를 사용하면 다른 언어 및 지역 설정을 사용하여 동일한 페이지에 여러 개의 맵 인스턴스를 로드할 수 있습니다. 또한 이러한 설정이 로드된 후에도 맵에서 설정을 업데이트할 수 있습니다. Azure Maps에서 지원되는 구체적인 언어 목록은 [여기](supported-languages.md)서 찾을 수 있습니다.

다음은 언어가 "fr"로 설정되고 사용자 지역이 "fr-FR"로 설정된 Azure Maps의 예입니다.

<center>

![Azure Maps 지역화](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>맵 보기 설정

Azure Maps 및 Google Maps의 동적 맵은 프로그래밍 방식으로 새 지리적 위치로 이동할 수 있습니다. 이렇게 하려면 JavaScript에서 적절한 함수를 호출합니다. 이 예제에서는 Google Maps에서 위성 항공 이미지를 맵에 표시하고, 위치를 맵의 가운데에 배치하며, 확대/축소 수준을 15로 변경하는 방법을 보여 줍니다. 경도가 -111.0225이고 위도가 35.0272인 위치 좌표가 사용됩니다.

> [!NOTE]
> Google Maps는 크기가 256픽셀인 타일을 사용하고, Azure Maps는 이보다 큰 512픽셀 타일을 사용합니다. 따라서 Azure Maps에서 Google Maps와 동일한 맵 영역을 로드하는 데 필요한 네트워크 요청 수가 줄어듭니다. 그러나 지도 컨트롤에서 타일 피라미드가 작동하는 방식 때문에, 타일이 더 큰 Azure Maps에서는 Google Maps의 맵과 동일한 가시 영역을 얻으려면 Azure Maps를 사용할 때 Google Maps에 사용되는 확대/축소 수준에서 1을 빼야 합니다.

**이전: Google Maps**

Google Maps 컨트롤은 `setOptions` 메서드를 사용하여 프로그래밍 방식으로 이동할 수 있습니다. 이 메서드를 사용하면 맵의 중심과 확대/축소 수준을 지정할 수 있습니다.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Google Maps 보기 설정](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**이후: Azure Maps**

Azure Maps에서는 맵의 `setCamera` 메서드를 사용하여 맵 위치를 프로그래밍 방식으로 변경하고, `setStyle` 메서드를 사용하여 맵 스타일을 변경할 수 있습니다. Azure Maps의 좌표는 "경도, 위도" 형식이고, 확대/축소 수준은 1을 뺀 값입니다.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Azure Maps 보기 설정](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**추가 리소스:**

- [지도 스타일 선택](choose-map-style.md)
- [지원되는 맵 스타일](supported-map-styles.md)

### <a name="adding-a-marker"></a>표식 추가

Azure Maps는 지점 데이터를 맵에 렌더링할 수 있는 여러 가지 방법이 있습니다.

- **HTML 표식** – 기존 DOM 요소를 사용하여 지점을 렌더링합니다. HTML 표식은 끌기를 지원합니다.
- **기호 레이어** – WebGL 컨텍스트 내에서 아이콘 및/또는 텍스트를 사용하여 지점을 렌더링합니다.
- **거품형 레이어** – 지점을 맵에 원으로 렌더링합니다. 원의 반지름은 데이터의 속성에 따라 조정할 수 있습니다.

기호 및 거품형 레이어는 모두 WebGL 컨텍스트 내에서 렌더링됩니다. 두 레이어는 모두 큰 지점 세트를 맵에 렌더링할 수 있습니다. 이러한 레이어를 사용하려면 데이터 원본에 데이터를 저장해야 합니다. `ready` 이벤트가 발생한 후 데이터 원본 및 렌더링 레이어를 맵에 추가해야 합니다. HTML 표식은 페이지 내에서 DOM 요소로 렌더링되며 데이터 원본을 사용하지 않습니다. 페이지의 DOM 요소가 많을수록 페이지 속도가 느려집니다. 맵에 수백개가 넘는 지점을 렌더링하는 경우에는 렌더링 레이어 중 하나를 대신 사용하는 것이 좋습니다.

다음 예제는 맵(경도: -0.2, 위도: 51.5)에 표식을 추가하고 숫자 10을 레이블로 오버레이합니다.

**이전: Google Maps**

Google Maps에서는 `google.maps.Marker` 클래스를 사용하고 맵을 옵션 중 하나로 지정하여 맵에 표식을 추가합니다.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Google Maps 표식](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**이후: HTML 표식을 사용하는 Azure Maps**

Azure Maps에서는 HTML 표식을 사용하여 지점을 맵에 표시할 수 있습니다. HTML 표식은 맵에 적은 수의 지점만 표시하면 되는 간단한 앱에 사용하는 것이 좋습니다. HTML 표식을 사용하려면 `atlas.HtmlMarker` 클래스의 인스턴스를 만들고, 텍스트 및 위치 옵션을 설정하고, `map.markers.add` 메서드를 사용하여 표식을 맵에 추가합니다.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps HTML 표식](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**이후: 기호 레이어를 사용하는 Azure Maps**

기호 레이어를 사용하는 경우 데이터를 데이터 원본에 추가하고, 데이터 원본을 레이어에 연결해야 합니다. 또한 `ready` 이벤트가 발생한 후 데이터 원본 및 레이어를 맵에 추가해야 합니다. 기호 위에 고유한 텍스트 값을 렌더링하려면 텍스트 정보를 데이터 요소의 속성으로 저장하고 해당 속성을 레이어의 `textField` 옵션에서 참조해야 합니다. 이 방법은 HTML 표식을 사용하는 것보다 약간 더 많은 작업이지만 성능상의 이점이 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 기호 레이어](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**추가 리소스:**

- [데이터 원본 만들기](create-data-source-web-sdk.md)
- [기호 레이어 추가](map-add-pin.md)
- [거품형 레이어 추가](map-add-bubble-layer.md)
- [클러스터 지점 데이터](clustering-point-data-web-sdk.md)
- [HTML 표식 추가](map-add-custom-html.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
- [기호 레이어 아이콘 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [기호 레이어 텍스트 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 표식 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 표식 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>사용자 지정 표식 추가

사용자 지정 이미지를 사용하여 맵에 지점을 나타낼 수 있습니다. 다음 맵 이미지에서는 사용자 지정 이미지를 사용하여 지점을 맵에 표시합니다. 지점은 위도 51.5, 경도 -0.2로 표시됩니다. 앵커는 표식의 위치를 오프셋하여 압정 아이콘의 지점이 맵의 올바른 위치와 맞춰집니다.

<center>

![노란색 압정 이미지](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**이전: Google Maps**

Google Maps에서는 압정 이미지의 지점을 맵의 좌표와 맞추는 `anchor` 지점인 이미지의 `url`을 포함하는 `Icon` 개체를 지정하여 사용자 지정 표식을 만듭니다. Google Maps의 앵커 값은 이미지의 왼쪽 위 모서리를 기준으로 합니다.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Google Maps 사용자 지정 표식](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**이후: HTML 표식을 사용하는 Azure Maps**

Azure Maps에서 HTML 표식을 사용자 지정하려면 HTML `string` 또는 `HTMLElement`를 표식의 `htmlContent` 옵션에 전달할 수 있습니다. Azure Maps에서 `anchor` 옵션은 9개의 정의된 참조 지점 중 하나를 사용하여 위치 좌표를 기준으로 표식의 상대 위치를 지정하는 데 사용됩니다. 정의된 참조 지점은 "가운데", "위쪽", "아래쪽", "왼쪽", "오른쪽", "왼쪽 위", "오른쪽 위", "왼쪽 아래", "오른쪽 아래"입니다. 콘텐츠는 기본적으로 html 콘텐츠의 하단 가운데에 고정됩니다. Google Maps에서 코드를 보다 쉽게 마이그레이션하려면 `anchor`를 "왼쪽 위"로 설정한 다음, Google Maps에서 사용되는 것과 동일한 오프셋으로 `pixelOffset` 옵션을 사용합니다. Azure Maps의 오프셋은 Google Maps와 반대 방향으로 -1을 곱합니다.

> [!TIP]
> Microsoft Edge에서 원치 않는 아이콘을 표시하는 기본 끌기 동작을 해제하려면 `pointer-events:none`을 html 콘텐츠의 스타일로 추가하세요.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps 사용자 지정 HTML 표식](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**이후: 기호 레이어를 사용하는 Azure Maps**

Azure Maps의 기호 레이어는 사용자 지정 이미지도 지원하지만, 먼저 이미지를 맵 리소스에 로드하고 고유한 ID를 할당해야 합니다. 그러면 기호 레이어에서 이 ID를 참조할 수 있습니다. 기호는 아이콘 `offset` 옵션을 사용하여 이미지의 올바른 지점에 맞추도록 오프셋할 수 있습니다. Azure Maps에서 `anchor` 옵션은 9개의 정의된 참조 지점 중 하나를 사용하여 위치 좌표를 기준으로 기호의 상대 위치를 지정하는 데 사용됩니다. 정의된 위치 좌표는 "가운데", "위쪽", "아래쪽", "왼쪽", "오른쪽", "왼쪽 위", "오른쪽 위", "왼쪽 아래", "오른쪽 아래"입니다. 콘텐츠는 기본적으로 html 콘텐츠의 하단 가운데에 고정됩니다. Google Maps에서 코드를 보다 쉽게 마이그레이션하려면 `anchor`를 "왼쪽 위"로 설정한 다음, Google Maps에서 사용되는 것과 동일한 오프셋으로 `offset` 옵션을 사용합니다. Azure Maps의 오프셋은 Google Maps와 반대 방향으로 -1을 곱합니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 사용자 지정 아이콘 기호 레이어](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> 지점의 고급 사용자 지정 렌더링을 만들려면 여러 렌더링 레이어를 함께 사용합니다. 예를 들어 서로 다른 색이 지정된 원에 동일한 아이콘이 있는 여러 개의 압정을 사용하려고 한다고 가정해 보겠습니다. 각 색 오버레이에 대해 많은 이미지를 만드는 대신 거품형 레이어 위에 기호 레이어를 추가하고 압정이 동일한 데이터 원본을 참조하도록 합니다. 이 방법은 서로 다른 많은 이미지를 만들어 유지 관리하는 것보다 더 효율적입니다.

**추가 리소스:**

- [데이터 원본 만들기](create-data-source-web-sdk.md)
- [기호 레이어 추가](map-add-pin.md)
- [HTML 표식 추가](map-add-custom-html.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)
- [기호 레이어 아이콘 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [기호 레이어 텍스트 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [HTML 표식 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [HTML 표식 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>폴리라인 추가

폴리라인은 맵에 선 또는 경로를 나타내는 데 사용됩니다. 다음 예제에서는 맵에 파선 폴리라인을 만드는 방법을 보여줍니다.

**이전: Google Maps**

Google Maps에서 폴리라인 클래스는 옵션 세트를 사용합니다. 좌표 배열은 폴리라인의 `path` 옵션으로 전달됩니다.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Google Maps 폴리라인](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**이후: Azure Maps**

Azure Maps에서 폴리라인은 `LineString` 또는 `MultiLineString` 개체라고 합니다. 이러한 개체는 선 계층을 사용하여 데이터 원본에 추가하고 렌더링할 수 있습니다.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Azure Maps 폴리라인](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**추가 리소스:**

- [맵에 선 추가](map-add-line-layer.md)
- [선 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>다각형 추가

다각형은 맵의 영역을 나타내는 데 사용됩니다. Azure Maps 및 Google Maps는 다각형을 비슷한 방식으로 지원합니다. 다음 예제에서는 맵의 가운데 좌표를 기준으로 삼각형을 형성하는 다각형을 만드는 방법을 보여줍니다.

**이전: Google Maps**

Google Maps에서 다각형 클래스는 옵션 세트를 사용합니다. 좌표 배열은 다각형의 `paths` 옵션으로 전달됩니다.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Google Maps 다각형](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**이후: Azure Maps**

Azure Maps에서는 레이어를 사용하여 `Polygon` 및 `MultiPolygon` 개체를 데이터 원본에 추가하고 이를 맵에 렌더링할 수 있습니다. 다각형 영역은 다각형 계층에 렌더링할 수 있습니다. 다각형 윤곽선은 선 계층을 사용하여 렌더링할 수 있습니다.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Azure Maps 다각형](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**추가 리소스:**

- [맵에 다각형 추가](map-add-shape.md)
- [맵에 원 추가](map-add-shape.md#add-a-circle-to-the-map)
- [다각형 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [선 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>정보 창 표시

Google Maps에서 엔터티에 대한 추가 정보는 `google.maps.InfoWindow` 클래스로 맵에 표시할 수 있습니다. Azure Maps에서 이 기능은 `atlas.Popup` 클래스를 사용하여 구현할 수 있습니다. 다음 예제에서는 표식을 맵에 추가하고, 이 표식을 클릭하면 정보 창/팝업이 표시됩니다.

**이전: Google Maps**

Google Maps에서는 `google.maps.InfoWindow` 생성자를 사용하여 정보 창을 만들 수 있습니다.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Google Maps 팝업](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**이후: Azure Maps**

Azure Maps에서는 팝업을 사용하여 위치에 대한 추가 정보를 표시할 수 있습니다. HTML `string` 또는 `HTMLElement` 개체를 팝업의 `content` 옵션에 전달할 수 있습니다. 원하는 경우 팝업을 모양에 관계없이 표시할 수 있습니다. 따라서 팝업에는 `position` 값을 지정해야 합니다. 팝업을 표시하려면 `open` 메서드를 호출하고 팝업이 표시될 `map`을 전달합니다.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Azure Maps 팝업](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> 기호, 거품형, 선 또는 다각형 계층을 사용하여 동일한 작업을 수행하려면 표식 대신 맵 이벤트 코드에 레이어를 전달하면 됩니다.

**추가 리소스:**

- [팝업 추가](map-add-popup.md)
- [미디어 콘텐츠가 포함된 팝업](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [도형의 팝업](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [여러 핀을 사용하여 팝업을 다시 사용](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Popup 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [팝업 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>GeoJSON 파일 가져오기

Google Maps는 `google.maps.Data` 클래스를 통해 GeoJSON 데이터의 로딩 및 동적 스타일 지정을 지원합니다. 이 클래스의 기능은 Azure Maps의 데이터 기반 스타일 지정과 훨씬 잘 어울립니다. 한 가지 중요한 차이점은 Google Maps를 사용하는 경우 콜백 함수를 지정한다는 것입니다. UI 스레드에서 개별적으로 처리하는 각 기능의 스타일을 지정하기 위한 비즈니스 논리입니다. Azure Maps에서 레이어는 데이터 기반 식을 스타일 지정 옵션으로 지정하는 기능을 지원합니다. 이러한 식은 렌더링 시간에 별도의 스레드에서 처리됩니다. 따라서 이 방법은 렌더링 성능을 향상시킵니다. 이러한 이점은 더 큰 데이터 세트를 빠르게 렌더링할 때 확인할 수 있습니다.

다음 예제에서는 USGS에서 지난 7일 동안 발생한 모든 지진에 대한 GeoJSON 피드를 로드합니다. 여기서는 크기가 조정된 원이 맵에 렌더링됩니다. 각 원의 색과 배율은 각 지진의 크기를 기반으로 하며, 데이터 세트에 있는 각 기능의 `"mag"` 속성에 저장됩니다. 크기가 5보다 크거나 같으면 빨간색 원이 됩니다. 3보다 크거나 같지만 5보다 작으면 주황색 원이 됩니다. 3 미만이면 초록색 원이 됩니다. 각 원의 반지름은 규모에 0.1을 곱한 값의 지수입니다.

**이전: Google Maps**

Google Maps에서 단일 콜백 함수는 `map.data.setStyle` 메서드에서 지정할 수 있습니다. 이 메서드는 `map.data.loadGeoJson` 메서드를 통해 비즈니스 논리를 GeoJSON 피드에서 로드된 각 기능에 적용하는 데 사용됩니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**이후: Azure Maps**

GeoJSON은 Azure Maps의 기본 데이터 형식이며 `datasource.importFromUrl` 메서드를 사용하여 데이터 원본으로 쉽게 가져올 수 있습니다. 거품형 레이어는 데이터 원본의 기능 속성에 따라 크기가 조정된 원을 렌더링하는 기능을 제공합니다. 콜백 함수를 포함하는 대신, 비즈니스 논리가 식으로 변환되어 스타일 옵션에 전달됩니다. 식은 비즈니스 논리의 작동 방식을 정의합니다. 식은 다른 스레드에 전달되고 기능 데이터와 비교하여 평가할 수 있습니다. 각각 비즈니스 논리가 다른 여러 데이터 원본과 레이어를 Azure Maps에 추가할 수 있습니다. 이 기능을 사용하면 여러 데이터 세트를 여러 가지 방법으로 맵에 렌더링할 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**추가 리소스:**

- [기호 레이어 추가](map-add-pin.md)
- [거품형 레이어 추가](map-add-bubble-layer.md)
- [클러스터 지점 데이터](clustering-point-data-web-sdk.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>표식 클러스터링

맵에 여러 데이터 요소를 시각화할 때 지점이 서로 겹치면 맵이 복잡해지고 읽고 사용하기 불편해집니다. 이때 지점 데이터를 클러스터링하면 이러한 사용자 환경을 개선하고 성능을 향상할 수 있습니다. 지점 데이터 클러스터링은 서로 가까이 있는 지점 데이터를 결합하여 맵에 단일 클러스터형 데이터 요소로 표시하는 프로세스입니다. 사용자가 맵을 확대하면 클러스터는 개별 데이터 요소로 분리됩니다.

다음 예제에서 코드는 지난 주의 지진 데이터에 대한 GeoJSON 피드를 로드하여 맵에 추가합니다. 클러스터는 포함하고 있는 지점 수에 따라 크기가 조정되고 색이 있는 원으로 렌더링됩니다.

> [!NOTE]
> 표식 클러스터링에 사용되는 여러 가지 알고리즘이 있습니다. Google 및 Azure Maps는 약간 다른 알고리즘을 사용합니다. 따라서 때때로 클러스터의 지점 배포가 달라질 수 있습니다.

**이전: Google Maps**

Google Maps에서는 MarkerClusterer 라이브러리에서 표식을 로드하여 클러스터링할 수 있습니다. 클러스터 아이콘은 1~5 숫자의 이름을 사용하고 동일한 디렉터리에서 호스팅되는 이미지로 제한됩니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps 클러스터링](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**이후: Azure Maps**

Azure Maps에서 데이터는 데이터 원본을 통해 추가 및 관리됩니다. 레이어는 데이터 원본에 연결하여 그 안에서 데이터를 렌더링합니다. Azure Maps의 `DataSource` 클래스는 여러 가지 클러스터링 옵션을 제공합니다.

- `cluster` – 데이터 원본에 지점 데이터를 클러스터링하라고 알려줍니다.
- `clusterRadius` - 지점을 함께 클러스터링하는 데 적용되는 반지름(픽셀)입니다.
- `clusterMaxZoom` - 클러스터링이 발생하는 최대 확대/축소 수준입니다. 이 수준보다 더 크게 확대하면 모든 지점이 기호로 렌더링됩니다.
- `clusterProperties` - 식을 사용하여 각 클러스터 내의 모든 지점에 대해 계산하여 각 클러스터 지점의 속성에 추가되는 사용자 지정 속성을 정의합니다.

클러스터링을 사용하도록 설정하면 데이터 원본이 클러스터형 및 비클러스터형 데이터 요소를 렌더링하기 위해 레이어로 보냅니다. 데이터 원본은 수십만 개의 데이터 요소를 클러스터링할 수 있습니다. 클러스터링된 데이터 요소에는 다음과 같은 속성이 포함되어 있습니다.

| 속성 이름             | Type    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 기능이 클러스터를 표시하는지 여부를 나타냅니다. |
| `cluster_id`              | 문자열  | DataSource `getClusterExpansionZoom`, `getClusterChildren` 및 `getClusterLeaves` 메서드에 사용할 수 있는 클러스터의 고유 ID입니다. |
| `point_count`             | number  | 클러스터에 포함된 지점의 수입니다.  |
| `point_count_abbreviated` | 문자열  | 길이가 긴 경우 `point_count` 값을 줄여서 표시하는 문자열입니다. (예: 4,000은 4K)  |

`DataSource` 클래스에는 `cluster_id`를 사용하여 클러스터에 대한 추가 정보에 액세스하는 다음과 같은 도우미 함수가 있습니다.

| 방법 | 반환 형식 | Description |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 다음 확대/축소 수준에서 지정된 클러스터의 자식 요소를 검색합니다. 이러한 자식 요소는 도형과 하위 클러스터의 조합일 수 있습니다. 하위 클러스터는 ClusteredProperties와 일치하는 속성이 있는 기능입니다. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | 클러스터가 확장 또는 분리를 시작하는 확대/축소 수준을 계산합니다. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 클러스터의 모든 지점을 검색합니다. `limit`를 설정하여 지점의 하위 세트를 반환하고, `offset`을 사용하여 지점을 통해 페이지를 이동합니다. |

클러스터형 데이터를 맵에 렌더링할 때 둘 이상의 레이어를 사용하는 것이 가장 쉬운 경우가 많습니다. 다음 예제에서는 세 개의 레이어를 사용합니다. 거품형 레이어에서 클러스터 크기에 따라 크기가 조정되고 색이 지정된 원을 그립니다. 기호 레이어에서 클러스터 크기를 텍스트로 렌더링합니다. 그리고 두 번째 기호 레이어를 사용하여 비 클러스터형 지점을 렌더링합니다. 클러스터형 데이터를 렌더링하는 여러 가지 방법이 있습니다. 자세한 내용은 [지점 데이터 클러스터링](clustering-point-data-web-sdk.md) 설명서를 참조하세요.

GeoJSON 데이터는 Azure Maps에서 `DataSource` 클래스의 `importDataFromUrl` 함수를 사용하여 직접 가져올 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 클러스터링](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**추가 리소스:**

- [기호 레이어 추가](map-add-pin.md)
- [거품형 레이어 추가](map-add-bubble-layer.md)
- [클러스터 지점 데이터](clustering-point-data-web-sdk.md)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>열 지도 추가

지점 밀도 맵이라고도 하는 열 지도는 데이터 시각화의 한 형식입니다. 색 범위를 사용하여 데이터의 밀도를 나타내는 데 사용됩니다. 그리고 "핫 스폿" 데이터를 맵에 표시하는 데 사용되는 경우가 많습니다. 열 지도는 큰 점 데이터 세트를 렌더링하는 좋은 방법입니다.

다음 예제에서는 USGS에서 지난 달에 발생한 모든 지진에 대한 GeoJSON 피드를 로드하고, 이를 `"mag"` 속성이 가중치로 사용되는 가중 열 지도에 렌더링합니다.

**이전: Google Maps**

Google Maps에서 열 지도를 만들려면 API 스크립트 URL에 `&libraries=visualization`을 추가하여 "시각화" 라이브러리를 로드해야 합니다. Google Maps의 열 지도 계층은 GeoJSON 데이터를 직접 지원하지 않습니다. 먼저 데이터를 다운로드하여 가중 데이터 요소의 배열로 변환해야 합니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Google Maps 열 지도](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**이후: Azure Maps**

Azure Maps에서 GeoJSON 데이터를 데이터 원본에 로드하고 데이터 원본을 열 지도 계층에 연결합니다. 가중치에 사용할 속성은 식을 사용하여 `weight` 옵션에 전달할 수 있습니다. GeoJSON 데이터는 Azure Maps에서 `DataSource` 클래스의 `importDataFromUrl` 함수를 사용하여 직접 가져올 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 열 지도](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**추가 리소스:**

- [열 지도 계층 추가](map-add-heat-map-layer.md)
- [열 지도 계층 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [열 지도 계층 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>타일 레이어 오버레이

Google Maps에서 타일 레이어는 이미지 오버레이라고도 합니다. 타일 레이어를 사용하면 맵 바둑판식 배열 시스템에 맞춘 더 작은 바둑판식 이미지로 분할된 큰 이미지를 오버레이할 수 있습니다. 이 방법은 일반적으로 큰 이미지 또는 큰 데이터 세트를 오버레이하는 데 사용됩니다.

다음 예제에서는 아이오와 주립 대학교의 Iowa Environmental Mesonet에서 받은 날씨 레이더 타일 레이어를 오버레이합니다.

**이전: Google Maps**

Google Maps에서 타일 레이어는 `google.maps.ImageMapType` 클래스를 사용하여 만들 수 있습니다.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Google Maps 타일 레이어](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**이후: Azure Maps**

Azure Maps에서 타일 레이어는 다른 레이어와 비슷한 방식으로 맵에 추가할 수 있습니다. x, y, 확대/축소 자리 표시자가 있는 형식이 지정된 URL `{x}`, `{y}`, `{z}`는 각각 타일에 액세스할 위치를 레이어에 알리는 데 사용됩니다. Azure Maps 타일 레이어는 `{quadkey}`, `{bbox-epsg-3857}` 및 `{subdomain}` 자리 표시자도 지원합니다.

> [!TIP]
> Azure Maps에서는 기본 지도 계층을 비롯한 다른 계층 아래에 레이어를 쉽게 렌더링할 수 있습니다. 쉽게 읽을 수 있도록 맵 레이블 아래에 타일 레이어를 렌더링하는 것이 좋은 경우가 많습니다. `map.layers.add` 메서드는 아래에 새 레이어를 삽입할 레이어의 ID인 두 번째 매개 변수를 사용합니다. 맵 레이블 아래에 타일 레이어를 삽입하려면 `map.layers.add(myTileLayer, "labels");` 코드를 사용하면 됩니다.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps 타일 레이어](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> 타일 요청은 맵의 `transformRequest` 옵션을 사용하여 캡처할 수 있습니다. 이렇게 하면 필요한 경우 헤더를 수정하거나 요청에 추가할 수 있습니다.

**추가 리소스:**

- [타일 레이어 추가](map-add-tile-layer.md)
- [타일 레이어 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [타일 레이어 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>트래픽 표시

트래픽 데이터는 Azure와 Google 맵에 모두 오버레이할 수 있습니다.

**이전: Google Maps**

Google Maps에서는 트래픽 레이어를 사용하여 트래픽 데이터를 맵에 오버레이할 수 있습니다.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Google Maps 트래픽](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**이후: Azure Maps**

Azure Maps는 트래픽을 표시하는 여러 가지 옵션을 제공합니다. 도로 폐쇄나 사고 같은 트래픽 인시던트를 맵에 아이콘으로 표시할 수 있습니다. 트래픽 흐름, 색으로 구분된 도로를 맵에 오버레이할 수 있으며 게시된 속도 제한, 일반적인 예상 지연 시간 또는 절대 지연 시간을 기준으로 하도록 색을 수정할 수 있습니다. Azure Maps에서 인시던트 데이터는 1분마다 업데이트되고, 흐름 데이터는 2분마다 업데이트됩니다.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps 트래픽](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Azure Maps에서 트래픽 아이콘 중 하나를 클릭하면 팝업에 추가 정보가 표시됩니다.

<center>

![Azure Maps 트래픽 인시던트](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**추가 리소스:**

- [맵에 트래픽 표시](map-show-traffic.md)
- [트래픽 오버레이 옵션](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>그라운드 오버레이 추가

Azure 및 Google Maps는 사용자가 맵을 이동하고 확대/축소할 때 이미지를 이동하고 크기를 조정할 수 있도록 지리 참조된 이미지를 맵에 오버레이할 수 있습니다. 이러한 오버레이는 Google Maps에서 그라운드 오버레이라고 하며, Azure Maps에서는 이미지 레이어라고 합니다. 이 기능은 평면도를 작성하고, 이전 맵 또는 드론의 이미지를 오버레이하는 데 매우 유용합니다.

**이전: Google Maps**

Google Maps에서 그라운드 오버레이를 만들 때 오버레이할 이미지의 URL과 이 이미지를 맵에 바인딩할 경계 상자를 지정해야 합니다. 이 예제에서는 [뉴저지주 뉴어크의 1922년](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) 맵 이미지를 맵에 오버레이합니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

브라우저에서 이 코드를 실행하면 다음 이미지와 비슷한 맵이 표시됩니다.

<center>

![Google Maps 이미지 오버레이](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**이후: Azure Maps**

Azure Maps에서는 `atlas.layer.ImageLayer` 클래스를 사용하여 지리 참조된 이미지를 오버레이할 수 있습니다. 이 클래스에는 이미지 URL과 이미지의 네 모퉁이 좌표 세트가 필요합니다. 동일한 도메인에 이미지를 호스팅하거나 COR을 사용하도록 설정해야 합니다.

> [!TIP]
> 이미지의 각 모서리 좌표가 아닌 북쪽, 남쪽, 동쪽, 서쪽 및 회전 정보만 있는 경우 [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) 메서드를 사용할 수 있습니다.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Azure Maps 이미지 오버레이](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**추가 리소스:**

- [이미지 오버레이](map-add-image-layer.md)
- [이미지 레이어 클래스](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>추가 코드 샘플

다음은 Google Maps 마이그레이션과 관련된 몇 가지 추가 코드 샘플입니다.

- [그리기 도구](map-add-drawing-toolbar.md)
- [맵을 두 손가락으로 이동하도록 제한](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [스크롤 휠 확대/축소 제한](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [전체 화면 컨트롤 만들기](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**서비스:**

- [Azure Maps 서비스 모듈 사용](how-to-use-services-module.md)
- [관심 지점 검색](map-search-location.md)
- [좌표에서 정보 가져오기(역방향 지역 코드)](map-get-information-from-coordinate.md)
- [A에서 B로의 방향 표시](map-route.md)
- [JQuery UI를 사용하여 Autosuggest 검색](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3에서 Azure Maps 웹 SDK 클래스로 매핑

다음 부록에서는 Google Maps V3에서 일반적으로 사용되는 클래스 및 이와 동등한 Azure Maps 웹 SDK 간의 상호 참조를 제공합니다.

### <a name="core-classes"></a>핵심 클래스

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas.Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas.Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>오버레이 클래스

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | [맵에 원 추가](map-add-shape.md#add-a-circle-to-the-map) 참조                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>서비스 클래스

Azure Maps 웹 SDK에는 개별적으로 로드할 수 있는 서비스 모듈이 포함되어 있습니다. 이 모듈은 웹 API를 사용하여 Azure Maps REST 서비스를 래핑하며 JavaScript, TypeScript 및 node.js 애플리케이션에서 사용할 수 있습니다.

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>라이브러리

라이브러리는 맵에 기능을 추가합니다. 이러한 기능 중 상당수는 Azure Maps의 핵심 SDK에 포함되어 있습니다. 다음은 이러한 Google Maps 라이브러리 대신 사용할 수 있는 동일한 클래스입니다.

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| 라이브러리 그리기       | [그리기 도구 모듈](set-drawing-options.md) |
| 기하 도형 라이브러리      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| 시각화 라이브러리 | [열 지도 계층](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>다음 단계

Azure Maps 웹 SDK에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [맵 컨트롤을 사용하는 방법](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [서비스 모듈을 사용하는 방법](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [그리기 도구 모듈을 사용하는 방법](set-drawing-options.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)

