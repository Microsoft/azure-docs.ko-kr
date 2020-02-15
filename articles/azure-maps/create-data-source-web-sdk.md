---
title: 지도의 데이터 원본 만들기 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK를 사용 하 여 데이터 원본을 만들고 맵에 추가 하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190225"
---
# <a name="create-a-data-source"></a>데이터 소스 만들기

Azure Maps 웹 SDK는 데이터 원본에 데이터를 저장 합니다. 데이터 원본을 사용 하 여 쿼리 및 렌더링을 위한 데이터 작업을 최적화 합니다. 현재 다음과 같은 두 가지 유형의 데이터 원본이 있습니다.

**GeoJSON 데이터 원본**

GeoJSON 기반 데이터 원본은 `DataSource` 클래스를 사용 하 여 데이터를 로컬에서 로드 하 고 저장 합니다. GeoJSON 네임 스페이스의 도우미 클래스를 사용 하 여 수동으로 데이터를 만들거나 만들 수 [있습니다.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) `DataSource` 클래스는 로컬 또는 원격 GeoJSON 파일을 가져오기 위한 함수를 제공 합니다. 원격 GeoJSON 파일은 CORs 사용 끝점에서 호스팅되어야 합니다. `DataSource` 클래스는 클러스터링 지점 데이터를 위한 기능을 제공 합니다. 그리고 `DataSource` 클래스를 사용 하 여 데이터를 쉽게 추가, 제거 및 업데이트할 수 있습니다.


> [!TIP]
> `DataSource`의 모든 데이터를 덮어쓸 수 있습니다. `clear` 호출한 후 함수를 `add` 하면 맵이 두 번 다시 렌더링 되어 약간의 지연이 발생할 수 있습니다. 대신 `setShapes` 함수를 사용 합니다 .이 함수는 데이터 원본의 모든 데이터를 제거 하 고 바꾸고 지도의 단일 다시 렌더링만 트리거합니다.

**벡터 타일 원본**

벡터 타일 소스는 벡터 타일 계층에 액세스 하는 방법을 설명 합니다. [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) 클래스를 사용 하 여 벡터 타일 소스를 인스턴스화합니다. 벡터 타일 계층은 타일 계층과 비슷하지만 동일 하지는 않습니다. 타일 계층은 래스터 이미지입니다. 벡터 타일 계층은 압축 파일 (PF 형식)입니다. 이 압축 파일은 벡터 맵 데이터 및 하나 이상의 계층을 포함 합니다. 각 계층의 스타일에 따라 파일을 클라이언트에서 렌더링 하 고 스타일을 지정할 수 있습니다. 벡터 타일의 데이터에는 요소, 선 및 다각형 형식의 지리적 기능이 포함 되어 있습니다. 래스터 타일 계층 대신 벡터 타일 계층을 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

 - 벡터 타일의 파일 크기는 일반적으로 해당 하는 래스터 타일 보다 훨씬 작습니다. 따라서 대역폭이 줄어듭니다. 낮은 대기 시간, 더 빠른 맵 및 더 나은 사용자 환경을 의미 합니다.
 - 벡터 타일은 클라이언트에서 렌더링 되므로 표시 되는 장치의 해상도에 맞게 조정 됩니다. 따라서 렌더링 된 맵은 명확 하 고 명확한 레이블이 있는 잘 정의 된 것으로 나타납니다.
 - 클라이언트에 새 스타일을 적용할 수 있으므로 벡터 맵에서 데이터의 스타일을 변경 하는 경우 데이터를 다시 다운로드할 필요가 없습니다. 반면 래스터 타일 계층의 스타일을 변경 하는 경우 일반적으로 서버에서 타일을 로드 한 다음 새 스타일을 적용 해야 합니다.
 - 데이터가 벡터 형식으로 전달 되므로 데이터를 준비 하는 데 필요한 서버 쪽 처리가 줄어듭니다. 따라서 최신 데이터를 더 빨리 사용할 수 있습니다.

벡터 원본을 사용 하는 모든 레이어는 `sourceLayer` 값을 지정 해야 합니다.

데이터 원본을 만든 후에는 [Sourcemanager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)인 `map.sources` 속성을 통해 맵에 데이터 원본을 추가할 수 있습니다. 다음 코드는 `DataSource`를 만들어 맵에 추가 하는 방법을 보여 줍니다.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps는 [Mapbox Vector 타일 사양](https://github.com/mapbox/vector-tile-spec)(개방형 표준)을 준수 합니다.

## <a name="connecting-a-data-source-to-a-layer"></a>계층에 데이터 원본 연결

데이터는 렌더링 레이어를 사용 하 여 맵에서 렌더링 됩니다. 하나 이상의 렌더링 레이어에서 단일 데이터 원본을 참조할 수 있습니다. 다음 렌더링 계층에는 데이터 원본이 필요 합니다.

- [거품형 계층](map-add-bubble-layer.md) -지도에서 점 데이터를 크기가 조정 된 원으로 렌더링 합니다.
- [기호 계층](map-add-pin.md) -지점 데이터를 아이콘이 나 텍스트로 렌더링 합니다.
- [열 지도 계층](map-add-heat-map-layer.md) -점 데이터를 밀도 열 맵으로 렌더링 합니다.
- [선 계층](map-add-shape.md) -선을 렌더링 하거나 다각형의 윤곽선을 렌더링 합니다. 
- [다각형 계층](map-add-shape.md) -단색 또는 이미지 패턴으로 다각형 영역을 채웁니다.

다음 코드에서는 데이터 원본을 만들고 지도에 추가한 다음 거품형 계층에 연결 하는 방법을 보여 줍니다. 그런 다음 원격 위치에서 데이터 원본으로 GeoJSON point 데이터를 가져옵니다. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

이러한 데이터 소스에 연결 하지 않는 추가 렌더링 계층이 있지만 렌더링을 위해 데이터를 직접 로드 합니다. 

- [이미지 계층](map-add-image-layer.md) -지도 위에 단일 이미지를 오버레이 하 고 해당 모퉁이를 지정 된 좌표 집합에 바인딩합니다.
- [타일 계층](map-add-tile-layer.md) -수퍼는 지도 위에 래스터 타일 계층을 적용 합니다.

## <a name="one-data-source-with-multiple-layers"></a>여러 계층이 있는 하나의 데이터 원본

여러 레이어는 단일 데이터 원본에 연결할 수 있습니다. 이 옵션이 유용 하 게 사용할 수 있는 여러 가지 시나리오가 있습니다. 예를 들어 사용자가 다각형을 그리는 시나리오를 생각해 보겠습니다. 사용자가 지도에 점이 추가 될 때 다각형 영역을 렌더링 하 고 채워야 합니다. 스타일이 지정 된 선을 추가 하 여 다각형을 윤곽선으로 만들면 사용자가 그릴 때 다각형의 가장자리를 더 쉽게 볼 수 있습니다. 다각형의 개별 위치를 편리 하 게 편집 하기 위해 각 위치 위에 핀 이나 표식 같은 핸들을 추가할 수 있습니다.

![단일 데이터 원본에서 데이터를 렌더링 하는 여러 레이어를 보여 주는 지도](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

대부분의 매핑 플랫폼에서 다각형 개체, 선 개체 및 다각형의 각 위치에 대 한 pin이 필요 합니다. 다각형이 수정 될 때 줄과 핀을 수동으로 업데이트 해야 하며,이는 빠르게 복잡 해질 수 있습니다.

Azure Maps를 사용 하는 경우 아래 코드와 같이 데이터 소스에 단일 다각형이 필요 합니다.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [팝업 추가](map-add-popup.md)

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"]
> [열 지도 추가](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)