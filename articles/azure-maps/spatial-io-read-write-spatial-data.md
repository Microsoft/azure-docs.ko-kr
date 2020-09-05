---
title: 공간 데이터 읽기 및 쓰기 | Microsoft Azure 맵
description: Azure Maps Web SDK에서 제공 하는 공간 IO 모듈을 사용 하 여 데이터를 읽고 쓰는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: b094f63c075bdb8af225ff366343c60bc6818224
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816765"
---
# <a name="read-and-write-spatial-data"></a>공간 데이터 읽기 및 쓰기

다음 표에서는 공간 IO 모듈을 사용 하 여 읽기 및 쓰기 작업에 대해 지원 되는 공간 파일 형식을 나열 합니다.

| 데이터 형식       | 읽기 | 쓰기 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| .GPX)               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 공간 CSV       | ✓  |  ✓  |
| 잘 알려진 텍스트   | ✓  |  ✓  |

다음 섹션에서는 공간 IO 모듈을 사용 하 여 공간 데이터를 읽고 쓰기 위한 다양 한 도구를 모두 간략하게 설명 합니다.

## <a name="read-spatial-data"></a>공간 데이터 읽기

`atlas.io.read`함수는 공간 데이터를 사용 하 여 KML, .gpx), GeoRSS, GeoJSON 및 CSV 파일과 같은 일반적인 공간 데이터 형식을 읽는 데 사용 되는 기본 함수입니다. 이 함수는 zip 파일 또는 KMZ 파일로 이러한 형식의 압축 된 버전을 읽을 수도 있습니다. KMZ 파일 형식은 이미지와 같은 자산을 포함할 수도 있는 KML의 압축 된 버전입니다. 또는 read 함수는 이러한 형식의 파일을 가리키는 URL을 사용할 수 있습니다. Url은 CORS 사용 끝점에서 호스팅되어야 하거나 읽기 옵션에서 프록시 서비스를 제공 해야 합니다. 프록시 서비스는 CORS를 사용 하도록 설정 되지 않은 도메인에서 리소스를 로드 하는 데 사용 됩니다. Read 함수는 지도에 이미지 아이콘을 추가 하는 약속을 반환 하 고, UI 스레드에 대 한 영향을 최소화 하기 위해 비동기적으로 데이터를 처리 합니다.

Zip 또는 KMZ 압축 된 파일을 읽을 때 압축을 푼 후 첫 번째 유효한 파일을 검색 합니다. 예를 들어 kml, kml, .xml, geojson,. json, .csv, tsv 또는 .txt와 같은 다른 유효한 확장명이 있는 파일입니다. 그런 다음 KML 및 GeoRSS 파일에서 참조 되는 이미지를 미리 로드 하 여 액세스할 수 있도록 합니다. 액세스할 수 없는 이미지 데이터는 대체 대체 (fallback) 이미지를 로드 하거나 스타일에서 제거 될 수 있습니다. KMZ 파일에서 추출 된 이미지는 데이터 Uri로 변환 됩니다.

Read 함수의 결과는 `SpatialDataSet` 개체입니다. 이 개체는 GeoJSON FeatureCollection 클래스를 확장 합니다. 이를 그대로에 쉽게 전달 `DataSource` 하 여 지도에 기능을 렌더링할 수 있습니다. 는 `SpatialDataSet` 기능 정보를 포함할 뿐만 아니라 다음 표에 설명 된 대로 KML 그라운드 오버레이, 처리 메트릭 및 기타 세부 정보를 포함할 수도 있습니다.

| 속성 이름 | Type | Description | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 데이터 집합에 있는 모든 데이터의 경계 상자입니다. |
| `features` | `Feature[]` | 데이터 집합 내의 기능을 GeoJSON 합니다. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML GroundOverlays의 배열입니다. |
| `icons` | 레코드 &lt; 문자열, 문자열&gt; | 아이콘 Url 집합입니다. 키 = 아이콘 이름, 값 = URL |
| properties | any | 공간 데이터 집합의 문서 수준에서 제공 되는 속성 정보입니다. |
| `stats` | `SpatialDataSetStats` | 공간 데이터 집합의 콘텐츠 및 처리 시간에 대 한 통계입니다. |
| `type` | `'FeatureCollection'` | 읽기 전용 GeoJSON 형식 값입니다. |

## <a name="examples-of-reading-spatial-data"></a>공간 데이터를 읽는 예

다음 코드에서는 공간 데이터 집합을 읽고 클래스를 사용 하 여 map에서 렌더링 하는 방법을 보여 줍니다 `SimpleDataLayer` . 이 코드는 URL에서 가리키는 .GPX) 파일을 사용 합니다.

<br/>

<iframe height='500' scrolling='no' title='공간 데이터를 간단 하 게 로드' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 펜으로 <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>공간 데이터를 간단</a> 하 게 로드 Azure Maps ()를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

다음 코드 데모에서는 KML 또는 KMZ를 맵에 읽고 로드 하는 방법을 보여 줍니다. KML은 또는 형식으로 된 접지 오버레이를 포함할 수 있습니다 `ImageLyaer` `OgcMapLayer` . 이러한 오버레이는 기능에서 별도로 맵에 추가 해야 합니다. 또한 데이터 집합에 사용자 지정 아이콘이 있는 경우 기능을 로드 하기 전에 해당 아이콘을 맵 리소스에 로드 해야 합니다.

<br/>

<iframe height='500' scrolling='no' title='지도에 KML 로드' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 Azure Maps ()를 통해 Map to Map에 대 한 Pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>LOAD KML</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

필요에 따라 CORS를 사용 하지 않을 수 있는 도메인 간 자산에 액세스 하기 위한 프록시 서비스를 제공할 수 있습니다. Read 함수는 CORS를 먼저 사용 하 여 다른 도메인의 파일에 액세스 하려고 시도 합니다. 처음에 CORS를 사용 하 여 다른 도메인의 리소스에 액세스 하지 못한 경우 프록시 서비스가 제공 된 경우에만 추가 파일을 요청 합니다. Read 함수는 제공 된 프록시 URL의 끝에 파일 URL을 추가 합니다. 이 코드 조각은 읽기 함수에 프록시 서비스를 전달 하는 방법을 보여 줍니다.

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

아래 데모에서는 분리 된 파일을 읽고 맵에 렌더링 하는 방법을 보여 줍니다. 이 경우 코드는 공간 데이터 열이 있는 CSV 파일을 사용 합니다.

<br/>

<iframe height='500' scrolling='no' title='구분 된 파일 추가' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 펜으로 <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>구분 된 파일 추가</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> )를 <a href='https://codepen.io'>CodePen</a>참조 하세요.
</iframe>

## <a name="write-spatial-data"></a>공간 데이터 쓰기

공간 IO 모듈에는 두 가지 주요 쓰기 함수가 있습니다. `atlas.io.write`함수는 문자열을 생성 하는 반면 `atlas.io.writeCompressed` 함수는 압축 된 zip 파일을 생성 합니다. 압축 된 zip 파일에는 공간 데이터를 포함 하는 텍스트 기반 파일이 포함 되어 있습니다. 이러한 함수는 모두 파일에 데이터를 추가 하는 약속을 반환 합니다. 또한,,, `SpatialDataSet` `DataSource` `ImageLayer` `OgcMapLayer` , 기능 컬렉션, 기능, 기 하 도형 또는 이러한 데이터 형식의 조합 배열 등의 데이터를 작성할 수 있습니다. 이러한 함수 중 하나를 사용 하 여 작성 하는 경우 원하는 파일 형식을 지정할 수 있습니다. 파일 형식을 지정 하지 않으면 데이터가 KML로 기록 됩니다.

아래 도구는 함수와 함께 사용할 수 있는 대부분의 쓰기 옵션을 보여 줍니다 `atlas.io.write` .

<br/>

<iframe height='700' scrolling='no' title='공간 데이터 쓰기 옵션' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>공간 데이터 쓰기 옵션</a> 을 확인 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="example-of-writing-spatial-data"></a>공간 데이터를 작성 하는 예제

다음 샘플에서는 지도에서 공간 파일을 끌어서 놓고 로드할 수 있습니다. 맵에서 GeoJSON 데이터를 내보낸 다음 지원 되는 공간 데이터 형식 중 하나에 문자열이 나 압축 파일로 쓸 수 있습니다.

<br/>

<iframe height='700' scrolling='no' title='공간 파일을 맵으로 끌어서 놓기' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen에서 펜으로 <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>공간 파일 끌어서 놓기</a> Azure Maps ()를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

필요에 따라 CORS를 사용 하지 않을 수 있는 도메인 간 자산에 액세스 하기 위한 프록시 서비스를 제공할 수 있습니다. 이 코드 조각에서는 프록시 서비스를 통합할 수 있는 방법을 보여 줍니다.

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>잘 알려진 텍스트 읽기 및 쓰기 (WKT)

WKT ( [잘 알려진 텍스트](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) )는 공간 기 하 도형을 텍스트로 나타내는 OGC (Open Geospatial Consortium) 표준입니다. 많은 지리 공간적 시스템은 PostGIS 플러그 인을 사용 하는 azure SQL 및 Azure PostgreSQL와 같은 WKT을 지원 합니다. 대부분의 OGC 표준과 마찬가지로 좌표는 "x y" 규칙에 맞게 "경도 위도"로 서식 지정 됩니다. 예를 들어 경도-110 및 위도 45의 지점은 `POINT(-110 45)` WKT 형식을 사용 하 여 작성할 수 있습니다.

함수를 사용 하 여 잘 알려진 텍스트를 읽고 `atlas.io.ogc.WKT.read` 함수를 사용 하 여 쓸 수 있습니다 `atlas.io.ogc.WKT.write` .

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>잘 알려진 텍스트 읽기 및 쓰기 예 (WKT)

다음 코드는 잘 알려진 텍스트 문자열을 읽고 `POINT(-122.34009 47.60995)` 거품형 계층을 사용 하 여 지도에서 렌더링 하는 방법을 보여 줍니다.

<br/>

<iframe height='500' scrolling='no' title='잘 알려진 텍스트 읽기' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen의 Azure Maps ()에서 펜으로 <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>잘 알려진 텍스트 읽기</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

다음 코드는 잘 알려진 텍스트를 앞뒤로 읽고 쓰는 방법을 보여 줍니다.

<br/>

<iframe height='700' scrolling='no' title='잘 알려진 텍스트 읽기 및 쓰기' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>CodePen의 Azure Maps ()로 펜 <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>읽기 및 쓰기 잘 알려진 텍스트</a> 를 참조 하세요 <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="read-and-write-gml"></a>읽기 및 쓰기 GML

GML은 다른 XML 사양에 대 한 확장으로 자주 사용 되는 공간 XML 파일 사양입니다. 함수를 사용 하 여 GeoJSON 데이터를 GML 태그로 XML로 작성할 수 있습니다 `atlas.io.core.GmlWriter.write` . GML을 포함 하는 XML은 함수를 사용 하 여 읽을 수 있습니다 `atlas.io.core.GmlReader.read` . Read 함수에는 두 가지 옵션이 있습니다.

- `isAxisOrderLonLat`옵션-좌표 "위도, 경도" 또는 "경도, 위도"의 축 순서는 데이터 집합에 따라 다를 수 있으며 항상 잘 정의 되어 있지 않습니다. 기본적으로 GML 판독기는 좌표 데이터를 "위도, 경도"로 읽으므로이 옵션을 true로 설정 하면 "경도, 위도"로 읽혀집니다.
- `propertyTypes`옵션-이 옵션은 키 값 조회 테이블로, 키가 데이터 집합의 속성 이름입니다. 값은 구문 분석할 때 값을 캐스팅할 개체 형식입니다. 지원 되는 형식 값은 `string` , `number` , `boolean` 및  `date` 입니다. 속성이 조회 테이블에 없거나 형식이 정의 되어 있지 않으면 속성이 문자열로 구문 분석 됩니다.

`atlas.io.read`함수는 `atlas.io.core.GmlReader.read` 입력 데이터가 XML 임을 검색 했지만 데이터가 다른 지원 공간 XML 형식 중 하나가 아닌 경우 함수를 기본적으로 제공 합니다.

는 `GmlReader` 다음 SRIDs 중 하나가 있는 좌표를 구문 분석 합니다.

- EPSG: 4326 (기본 설정)
- EPSG: 4269, EPSG: 4283, EPSG: 4258, epsg: 4308, EPSG: 4230, epsg: 4272, epsg: 4271, epsg: 4267, epsg: 4608, EPSG: 4674 가능한 경우 작은 오류 여백이 있을 수 있습니다.
- EPSG: 3857, EPSG: 102100, EPSG: 3785, EPSG: 900913, EPSG: 102113, EPSG: 41001, EPSG: 54004

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [atlas.io 정적 함수](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [atlas. ogc. WKT 함수](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [OGC 지도 계층 추가](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 작업 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)
