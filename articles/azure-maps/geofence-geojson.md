---
title: Azure Maps의 지오펜스 GeoJSON 데이터 형식 | Microsoft Docs
description: Azure Maps의 지오펜스 GeoJSON 데이터 형식에 대한 자세한 정보
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: d4b6c8289ae7c22521fc433c928f2b25a56c87ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130214"
---
# <a name="geofencing-geojson-data"></a>지오펜싱 GeoJSON 데이터

Azure Maps [GET 지오펜스](/rest/api/maps/spatial/getgeofence) 및 [POST 지오펜스](/rest/api/maps/spatial/postgeofence) API를 사용하면 제공된 지오펜스 또는 펜스 세트를 기준으로 좌표의 근접성을 검색할 수 있습니다. 이 문서에서는 Azure Maps GET 및 POST API에 사용할 수 있는 지오펜스 데이터를 준비하는 방법을 자세히 설명합니다.

지오펜스 또는 지오펜스 세트의 데이터는 [rfc7946](https://tools.ietf.org/html/rfc7946)에서 정의된 `GeoJSON` 형식의 `Feature` 개체 및 `FeatureCollection` 개체로 표시됩니다. 이외에도 다음 사항에 유의하세요.

* GeoJSON 개체 형식은 `Feature` 개체 또는 `FeatureCollection` 개체일 수 있습니다.
* 기하 도형 개체 형식은 `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon` 및 `GeometryCollection`일 수 있습니다.
* 모든 기능 속성에는 지오펜스를 식별하는 데 사용되는 `geometryId`가 포함되어야 합니다.
* `Point`, `MultiPoint`, `LineString`, `MultiLineString`이 있는 기능의 속성에는 `radius`가 포함되어야 합니다. `radius` 값은 미터 단위로 측정되고, `radius` 값의 범위는 1-10000입니다.
* `polygon` 및 `multipolygon` 기하 도형 형식이 있는 기능에는 radius 속성이 없습니다.
* `validityTime`은 사용자가 지오펜스 데이터의 만료 시간 및 유효 기간을 설정할 수 있게 해주는 선택적 속성입니다. 지정하지 않으면 데이터가 만료되지 않고 항상 유효합니다.
* `expiredTime`은 지오펜싱 데이터의 만료 날짜 및 시간입니다. 요청의 `userTime` 값이 이 값보다 이후이면 해당 지오펜스 데이터는 만료된 데이터로 간주되며 쿼리되지 않습니다. 이 경우 이 지오펜스 데이터의 geometryId가 지오펜스 응답 내의 `expiredGeofenceGeometryId` 배열에 포함됩니다.
* `validityPeriod`는 지오펜스의 유효 기간 목록입니다. 요청의 `userTime` 값이 유효 기간을 벗어난 경우 해당 지오펜스 데이터는 잘못된 데이터로 간주되며 쿼리되지 않습니다. 이 지오펜스 데이터의 geometryId가 지오펜스 응답 내의 `invalidPeriodGeofenceGeometryId` 배열에 포함됩니다. 다음 표에는 validityPeriod 요소의 속성이 나와 있습니다.

| 이름 | Type | 필수  | 설명 |
| :------------ |:------------: |:---------------:| :-----|
| startTime | DateTime  | true | 유효 기간의 시작 날짜/시간입니다. |
| endTime   | DateTime  | true |  유효 기간의 종료 날짜/시간입니다. |
| recurrenceType | string | false |   기간의 되풀이 유형입니다. 값은 `Daily`, `Weekly`, `Monthly` 또는 `Yearly`일 수 있습니다. 기본값은 `Daily`입니다.|
| businessDayOnly | Boolean | false |  데이터가 영업일에만 유효한지 여부를 나타냅니다. 기본값은 `false`입니다.|


* 모든 좌표 값은 `WGS84`에 정의된 [위도, 경도]로 표시됩니다.
* `MultiPoint`, `MultiLineString`, `MultiPolygon` 또는 `GeometryCollection`을 포함하는 각 기능의 경우 속성이 모든 요소에 적용됩니다. 예: `MultiPoint`의 모든 점은 동일한 반경을 사용하여 여러 개의 원형 지오펜스를 형성합니다.
* 점-원형 시나리오에서는 [GeoJSON 기하 도형 확장](https://docs.microsoft.com/azure/azure-maps/extend-geojson)에 자세히 설명된 속성을 가진 `Point` 기하 도형 개체를 사용하여 원형 기하 도형을 표시할 수 있습니다.      

다음은 중심점과 반경을 사용하여 `GeoJSON`에서 원형 지오펜싱 기하 도형으로 표시된 지오펜스에 대한 샘플 요청 본문입니다. 지오펜스 데이터의 유효 기간은 2018년 10월 22일 오전 9시-오후 5시에 시작되며 주말을 제외하고 매일 반복됩니다. `expiredTime`은 요청의 `userTime`이 `2019-01-01`보다 이후인 경우 이 지오펜스 데이터가 만료된 데이터로 간주됨을 나타냅니다.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```