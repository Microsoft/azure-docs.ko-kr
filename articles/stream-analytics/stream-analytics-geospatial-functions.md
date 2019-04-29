---
title: Azure Stream Analytics 지리 공간적 함수 소개
description: 이 문서에서는 Azure Stream Analytics 작업에 사용되는 지리 공간적 함수를 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: ad789a597da759b9a2d58138c7ed441389a12adb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479986"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Stream Analytics 지리 공간적 함수 소개

Azure Stream Analytics의 지리 공간적 함수를 사용하여 지리 공간적 함수 데이터 스트리밍 시 실시간으로 분석할 수 있습니다. 몇 개의 코드 줄만으로, 복잡한 시나리오에 대한 프로덕션급 솔루션을 개발할 수 있습니다. 

지리 공간적 함수를 활용할 수 있는 시나리오의 예는 다음과 같습니다.

* 카풀
* 차량 관리
* 자산 추적
* 지오-펜싱
* 셀 사이트 간 전화 추적

Stream Analytics 쿼리 언어에는 기본 제공되는 7개의 지리 공간적 함수가 있습니다. 즉, **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** 및 **ST_WITHIN**입니다.

## <a name="createlinestring"></a>CreateLineString

`CreateLineString` 함수는 점을 수락한 후 지도에서 선으로 그릴 수 있는 GeoJSON LineString을 반환합니다. LineString 만들려면 2개 이상의 점이 있어야 합니다. LineString 점은 순서대로 연결됩니다.

다음 쿼리에서는 `CreateLineString`을 사용하여 3개 점으로 LineString을 만듭니다. 첫 번째 점은 입력 데이터를 스트리밍하여 생성되지만 다른 2개 점은 수동으로 생성됩니다.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>입력 예제  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>출력 예제  

 {"type" : "LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

자세한 내용은 [CreateLineString](https://msdn.microsoft.com/azure/stream-analytics/reference/createlinestring) 참조를 참조하세요.

## <a name="createpoint"></a>CreatePoint

`CreatePoint` 함수는 위도 및 경도를 수락하고, 지도에 그려질 수 있는 GeoJSON 점을 반환합니다. 위도 및 경도는 **float** 데이터 형식이어야 합니다.

다음 예제 쿼리는 `CreatePoint`를 사용하여 입력 데이터 를 스트리밍하여 얻은 위도 및 경도로 점을 만듭니다.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>입력 예제  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>출력 예제
  
 {"type" : "Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinates" : [20.2321, -87.33]}  

자세한 내용은 [CreatePoint](https://msdn.microsoft.com/azure/stream-analytics/reference/createpoint) 참조를 참조하세요.

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon` 함수는 점을 수락하고 GeoJSON 다각형 레코드를 반환합니다. 점 순서는 오른쪽 링 방향 또는 시계 반대 방향을 따라야 합니다. 선언된 순서대로 한 점에서 다른 점으로 이동한다고 상상해보세요. 다각형의 중심은 항상 왼쪽에 오게 됩니다.

다음 예제 쿼리는 `CreatePolygon`을 사용하여 3개 점에서 다각형을 만듭니다. 처음 2개 점이 수동으로 생성된 후 마지막 점이 입력 데이터에서 생성됩니다.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>입력 예제  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>출력 예제  

 {"type" : "Polygon", "coordinates" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coordinates" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

자세한 내용은 [CreatePolygon](https://msdn.microsoft.com/azure/stream-analytics/reference/createpolygon) 참조를 참조하세요.


## <a name="stdistance"></a>ST_DISTANCE
`ST_DISTANCE` 함수는 두 점 사이의 거리(미터)를 반환합니다. 

다음 쿼리는 `ST_DISTANCE`를 사용하여 주유소가 자동차로부터 10km 이내 거리에 있을 때 이벤트를 생성합니다.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

자세한 내용은 [ST_DISTANCE](https://msdn.microsoft.com/azure/stream-analytics/reference/st-distance) 참조를 참조하세요.

## <a name="stoverlaps"></a>ST_OVERLAPS
`ST_OVERLAPS` 함수는 두 다각형을 비교합니다. 다각형이 겹치면 이 함수는 1을 반환합니다. 다각형이 겹치지 않으면 이 함수는 0을 반환합니다. 

다음 쿼리는 `ST_OVERLAPS`를 사용하여 건물이 홍수 범람 가능 구역 내에 있을 때 이벤트를 생성합니다.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

다음 예제 쿼리는 폭우가 자동차를 향할 때 이벤트를 생성합니다.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

자세한 내용은 [ST_OVERLAPS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-overlaps) 참조를 참조하세요.

## <a name="stintersects"></a>ST_INTERSECTS
`ST_INTERSECTS` 함수는 두 LineString을 비교합니다. LineString이 교차하면 이 함수는 1을 반환합니다. LineString이 교차하지 않으면 이 함수는 0을 반환합니다.

다음 예제 쿼리를 `ST_INTERSECTS`를 사용하여 포장된 도로가 비포장 도로와 교차하는지를 확인합니다.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>입력 예제  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{“type”:”LineString”, “coordinates”: [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{“type”:”LineString”, “coordinates”: [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{“type”:”LineString”, “coordinates”: [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{“type”:”LineString”, “coordinates”: [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>출력 예제  

 1  
  
 0  

자세한 내용은 [ST_INTERSECTS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-intersects) 참조를 참조하세요.

## <a name="stwithin"></a>ST_WITHIN
`ST_WITHIN` 함수는 점 또는 다각형이 다각형 내에 있는지 여부를 확인합니다. 다각형 안에 점 또는 다각형이 포함되어 있으면 이 함수는 1을 반환합니다. 점 또는 다각형이 선언된 다각형 내에 있지 않으면 이 함수는 0을 반환합니다.

다음 예제 쿼리는 `ST_WITHIN`을 사용하여 배송 목적지가 지정된 창고 다각형 내에 있는지 여부를 확인합니다.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>입력 예제  
  
|deliveryDestination|warehouse|  
|-------------------------|---------------|  
|{“type”:”Point”, “coordinates”: [76.6, 10.1]}|{“type”:”Polygon”, “coordinates”: [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{“type”:”Point”, “coordinates”: [15.0, 15.0]}|{“type”:”Polygon”, “coordinates”: [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>출력 예제  

 0  
  
 1  

자세한 내용은 [ST_WITHIN](https://msdn.microsoft.com/azure/stream-analytics/reference/st-within) 참조를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)